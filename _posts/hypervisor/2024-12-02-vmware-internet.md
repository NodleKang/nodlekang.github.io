---
layout: single
title: "VMware 인터넷 설정"
date: 2024-12-02 10:00:00 +0900
categories: 
  - Linux
tag: 
  - linux
  - vmware
  - internet
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 VMware에서 인터넷을 사용하기 위해 설정하는 방법에 대해서 정리한 것입니다.

# 환경

다음과 같은 환경에서 테스트하였습니다.

<div class="notice" markdown="1">
- Host OS: Windows 10
- Hypervisor: VMware Player 17.5.2
- Guest OS: Rocky Linux 8.10 minimal
</div>

# 호스트 네트워크 설정

VMware Player에서 인터넷을 사용하게 하려면 호스트의 네트워크 설정을 변경해야 합니다.

호스트인 윈도우에 네트워크 어댑터 설정에서 인터넷에 연결되어 있는 네트워크 어댑터를 VMware에 공유합니다.

![윈도우 네트워크 어댑터 공유](/assets/images/post/hypervisor/2024-12-02-vmware-internet/windows_adapter_share.png)

# VMware 네트워크 설정

![VMware 네트워크 설정](/assets/images/post/hypervisor/2024-12-02-vmware-internet/vmware_adapter.png)

# 게스트 OS 설정

앞의 설정을 해도 게스트 OS에서 부팅할 때 인터페이스가 활성화되어 있지 않기 때문에 인터넷을 사용할 수 없습니다.

처음 인터넷 사용을 시도하면 `Cloud not resolve host` 에러가 발생합니다.

이 문제를 해결하기 위해 게스트 OS에서 부팅할 때 인터페이스가 활성화되도록 설정하면 인터넷을 사용할 수 있습니다.

```bash
$ vi /etc/sysconfig/network-scripts/ifcfg-ens*
```

```bash
ONBOOT=yes
```

# VMware Player 다운로드

Broadcom 정책 변화로 VMware Workstation Pro 17 부터는 개인 사용자에게 무료로 제공됩니다.

하지만 개인적인 테스트에는 원래도 개인 사용자에게 무료로 제공되던 VMware Player로도 충분해서 VMware Player를 사용하고 있습니다. 

VMware Player는 아래 링크에서 바로 다운로드 받을 수 있어서 편리합니다.

![VMware Player 다운로드 CDN](https://softwareupdate.vmware.com/cds/vmw-desktop/player/)
