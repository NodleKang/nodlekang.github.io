---
layout: single
title: "VMware 사용 설정"
date: 2024-12-02 10:00:00 +0900
categories: 
  - Linux
tag: 
  - linux
  - vmware
  - internet
  - ssl
  - 인증서
  - vmware-tools
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 윈도우에 설치한 VMware Player 환경을 설정하는 방법에 대해서 정리한 것입니다.

# 환경

다음과 같은 환경에서 테스트하였습니다.

<div class="notice" markdown="1">
- Host OS: Windows 10
- Hypervisor: VMware Player 17.5.2
- Guest OS: Rocky Linux 8.10 minimal
</div>

# VMware 다운로드

개인 사용자에게 무료로 제공되는 VMware Player와 VMware Tools를 다운로드 받아서 사용했습니다. 

참고로 Broadcom 정책 변화로 VMware Workstation Pro 17 부터는 개인 사용자에게 무료로 제공됩니다.

VMware Player 다운로드: [https://softwareupdate.vmware.com/cds/vmw-desktop/player/](https://softwareupdate.vmware.com/cds/vmw-desktop/player/)

VMware Toools 다운로드: [https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Tools](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Tools)

# 호스트 OS 네트워크 설정

VMware Player에서 인터넷을 사용하게 하려면 호스트 OS에서 네트워크 설정을 변경해야 합니다.

호스트 OS인 윈도우에서 인터넷에 연결되어 있는 네트워크 어댑터를 VMware에 공유합니다.

![윈도우 네트워크 어댑터 공유](/assets/images/post/hypervisor/2024-12-02-vmware-internet/windows_adapter_share.png)

# VMware 네트워크 설정

VMware Player에서 네트워크 설정은 NAT로 설정합니다.

만약 NAT 설정으로 안 된다면 다른 옵션을 선택해도 됩니다.

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
