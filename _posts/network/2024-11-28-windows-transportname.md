---
layout: single
title: "Windows에서 네트워크 어댑터 식별자 확인하기"
date: 2024-11-28 09:00:00 +0900
categories: 
  - Network
tag: 
  - network
  - windows
  - 어댑터
  - ip
  - transport name
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 Windows에서 IP와 Transport Name을 확인하는 방법에 대해 설명합니다.

# Transport Name 이란

네트워크 어댑터를 식별하는 고유 식별자로 시스템에서 네트워크 트래픽을 관리하고 라우팅하는 데 사용됩니다.

일반적으로 \Device\Tcpip_{GUID} 형식으로 표시됩니다.

여기서 GUID는 고유 식별자입니다.

# 네트워크 어댑터 식별자 확인하기

현재 개발 중인 어플리케이션에서 네트워크 패킷을 캡처해서 미러링하는 기능을 구현하고 있습니다.

호스트에서 패킷을 캡처해야 하는 경우, 네트워크 어댑터의 식별자를 확인해야 합니다.

Linux에서는 ifconfig 명령어를 이용하여 네트워크 어댑터의 IP 주소와 식별자를 확인할 수 있지만,

Windows에서 사용하는 ipconfig 명령어로는 식별자는 확인할 수 없습니다.

따라서 PowerShell을 이용하여 **네트워크 어댑터의 IP 주소와 식별자를 확인**하는 방법을 소개합니다.

## 명령어

PowerShell을 관리자 권한으로 실행한 후 다음 명령어를 입력하면 IP와 Transport Name을 확인할 수 있습니다.

```powershell
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled = 'True'" | ForEach-Object {
    $adapter = $_
    $ipAddresses = $adapter.IPAddress
    $description = $adapter.Description
    $transportName = $adapter.SettingID

    foreach ($ip in $ipAddresses) {
        Write-Output "Description: $description"
        Write-Output "IP Address: $ip"
        Write-Output "Transport Name: \Device\Tcpip_$transportName"
        Write-Output ""
    }
}
```

위 명령을 실행하면 다음과 같이 네트워크 어댑터의 IP 주소와 Transport Name이 출력됩니다.

```powershell
Description: vmxnet3 이더넷 어댑터 #2
IP Address: 10.65.80.67
Transport Name: \Device\Tcpip_{872A1E36-92DB-4E56-86C9-426DACE0144D}
```
