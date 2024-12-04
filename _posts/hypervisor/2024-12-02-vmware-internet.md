---
layout: single
title: "VMware Player 환경 설정"
date: 2024-12-04 09:00:00 +0900
categories: 
  - Linux
tag: 
  - linux
  - vmware
  - 인터넷
  - 인증서
  - ssl
  - vmware-tools
  - 포트포워딩
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 윈도우에 설치한 VMware Player 사용을 위해 환경을 설정한 내용을 정리한 것 입니다.

# 환경

본 포스트는 다음과 같은 환경에서 테스트하였습니다.

<div class="notice" markdown="1">
- Host OS: Windows 10
- Hypervisor: VMware Player 17.5.2
- Guest OS: Rocky Linux 8.10 minimal
</div>

# VMware 다운로드

개인 사용자에게 무료로 제공되는 VMware Player와 VMware Tools를 다운로드 받아서 사용했습니다. 

참고로 Broadcom 정책 변화로 VMware Workstation Pro 17 부터는 개인 사용자 한해서 무료로 제공됩니다. (하지만 VMware Player를 사용했습니다.)

VMware Player 다운로드: [https://softwareupdate.vmware.com/cds/vmw-desktop/player/](https://softwareupdate.vmware.com/cds/vmw-desktop/player/)

VMware Toools 다운로드: [https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Tools](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Tools)

# 호스트 OS 네트워크 설정

VMware Player에서 인터넷을 사용하게 하려면 호스트 OS에서 네트워크 설정을 변경해야 합니다.

호스트 OS인 윈도우에서 인터넷에 연결되어 있는 네트워크 어댑터를 VMware에 공유합니다.

![윈도우 네트워크 어댑터 공유](/assets/images/post/hypervisor/2024-12-02-vmware-internet/windows_adapter_share.png)

# VMware 네트워크 설정

VMware Player에서 네트워크 설정은 NAT로 설정합니다.

![VMware 네트워크 설정](/assets/images/post/hypervisor/2024-12-02-vmware-internet/vmware_adapter.png)

# 게스트 OS 설정

게스트 OS에 접속해서 부팅될 때 인터페이스를 활성화하게 설정합니다.

설정이 활성화가 안 되어 있으면 `Cloud not resolve host` 에러가 발생합니다.

```bash
$ vi /etc/sysconfig/network-scripts/ifcfg-ens*
```

```bash
ONBOOT=yes
```

# VMware Tools 설치

게스트 OS에 VMware Tools를 설치하면 게스트 OS와 호스트 OS 간에 파일을 복사하거나 드래그 앤 드롭을 사용할 수 있습니다.

게스트 OS에 VMware Tools를 설치하려면 다음과 같은 절차를 따릅니다.

## AppStream 저장소 경로 변경

Rocky Linux 8.10 minimal 버전에 AppStream 저장소 경로가 기본적으로 8.10으로 설정되어 있지 않아서 `dnf` 패키지 관리자가 동작하지 않습니다.

이를 해결하기 위해 releasever를 8.10 으로 변경합니다.

```bash
echo "8.10" > /etc/yum/vars/releasever
```

## dnf 패키지 관리자 설정 변경

dnf(Dandified YUM) 패키지 관리자는 기본적으로 인터넷을 통해 패키지를 설치하는데, 
회사에서는 자체적으로 서명한 SSL 인증서를 사용하고 있어서 인증서 오류가 발생합니다.

이를 해결하기 위해 dnf 패키지 관리자 설정을 변경합니다.

```bash
sudo dnf config-manager --setopt=sslverify=false --save
```

이 명령은 dnf 패키지 관리자 설정을 변경하여 SSL 인증서를 검증하지 않도록 설정합니다.

만약 이 명령만으로 해결이 안 된다면 `/etc/yum.conf` 파일에 `sslverify=false` 옵션을 추가합니다.

## perl 설치

게스트 OS에 perl 패키지가 설치되어 있지 않으면 VMware Tools를 설치할 수 없으므로 perl 패키지를 설치합니다.

```bash
$ sudo dnf install perl
```

## VMware Tools 설치

게스트 OS에 다운로드 받은 VMware Tools 이미지를 연결하고 부팅합니다.

![VMware Tools 이미지 연결](/assets/images/post/hypervisor/2024-12-02-vmware-internet/vmware_tools.png)

부팅이 완료되면 다음과 같은 절차를 따라 VMware Tools를 설치합니다.

```
sudo mount /dev/cdrom /mnt
cd /tmp
tar xzvf /mnt/VMwareTools-*.tar.gz
cd vmware-tools-distrib
sudo ./vmware-install.pl
```

설치가 완료되면 게스트 OS를 재부팅합니다.

# 폴더 공유

게스트 OS와 호스트 OS 간에 파일을 복사하거나 드래그 앤 드롭을 사용하려면 VMware Player에서 폴더 공유를 설정해야 합니다.

![폴더 공유 설정](/assets/images/post/hypervisor/2024-12-02-vmware-internet/vmware_folder_share.png)

폴더 공유를 설정하고 게스트 OS에서 다음과 같이 마운트하면 호스트 OS의 폴더를 사용할 수 있습니다.

```bash
sudo mkdir /mnt/hgfs
sudo /usr/bin/vmhgfs-fuse .host:/ /mnt/hgfs -o subtype=vmhgfs-fuse,allow_other
```

마운트가 성공했는지 확인하려면 다음과 같이 명령을 실행합니다.

```bash
ls /mnt/hgfs
```

# 포트포워딩

호스트OS에서 게스트OS로 접속하려면 포트포워딩을 설정해야 합니다.

## 게스트OS IP 확인

ifconfig 명령을 사용하여 게스트OS의 IP를 확인합니다. 

eth 혹은 ens로 시작하는 인터페이스의 IP를 확인합니다.

```bash
$ ifconfig
```

## VMware Player 포트포워딩 설정

윈도우에 설치된 VMware Player에 포트포워딩을 설정하려면 직접 설정 파일을 수정해야 합니다.

VMware Player의 설정 파일은 다음과 같은 경로에 있습니다.

```
C:\ProgramData\VMware\vmnetnat.conf
```

해당 파일을 열어 보면 다음과 같이 tcp/udp 포트포워딩 설정이 있습니다.

TCP 포트포워딩을 하려면 `[incomingtcp]` 부분에 `포트 = 게스트OS IP:포트` 형식으로 설정을 추가합니다.

```
... 생략 ...
[incomingtcp]
... 생략 ...

# SSH
#      ssh -p 8889 root@localhost
8889 = 192.168.226.128:22 # 여기에 게스트OS에서 확인한 IP를 입력합니다.

[incomingudp]
# UDP port forwarding example
#6000 = 192.168.27.128:6001
... 생략 ...
```

설정을 추가한 후에 윈도우의 서비스에서 VMware NAT Service를 재시작하면 포트포워딩이 적용됩니다.

![서비스재시작](/assets/images/post/hypervisor/2024-12-02-vmware-internet/service_restart.png)

## 게스트OS sshd 서비스 확인과 방화벽 설정

게스트OS에서 sshd 서비스가 실행 중인지 확인합니다.

```bash
$ systemctl status sshd
```

만약 실행 중이 아니라면 다음과 같이 서비스를 시작합니다.

```bash
sudo systemctl start sshd
sudo systemctl enable sshd
```

## 호스트OS에서 게스트OS로 접속

호스트OS에서 게스트OS로 접속하려면 다음과 같이 명령을 실행합니다.

```bash
ssh -p 8889 root@localhost
```
