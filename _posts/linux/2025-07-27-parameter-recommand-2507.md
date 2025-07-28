---
layout: single
title: "Linux 권장 파라미터(250725)"
date: 2025-07-27 15:00:00 +0900
categories: 
  - Linux
tag: 
  - linux
  -  kernel
  - parameter
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 리눅스에서 예기치 못한 부하에 대비하고, 패킷 처리 효율을 향상시키기 위해 권고되는 리눅스 커널 파라미터를 안내한 것 입니다. 
보통 API GW, EAI와 같은 고부하를 처리하는 인터페이스 서버에서 적용하는 것이 일반적입니다.

* **패킷 listen queue**: TCP 세션 연결 시 사용되는 버퍼 성격의 큐. 급격한 신규 TCP 연결 발생 시 대응 가능하도록 사전에 확장하는 것이 좋습니다.
* **송수신 버퍼**: TCP 세션이 사용하는 버퍼. 한번에 보낼 수 있는 패킷 크기를 늘려서 효율적으로 전송하도록 하는 개념

| 구분 | Kernel Parameter | 기본 값 | 권장 값 |
|:---:|:---|:---|:---|:---|
| 패킷 listen queue | net.core.somaxconn | 4096 | 8192 |
| 패킷 listen queue | net.core.netdev_max_backlog | 1000 | 30000 |
| 패킷 listen queue | net.ipv4.tcp_max_syn_backlog | 2048 | 8192 |
| 송수신 버퍼 | net.core.rmem_default | 212992 | 253952 |
| 송수신 버퍼 | net.core.rmem_max | 212992 | 16777216 |
| 송수신 버퍼 | net.core.wmem_default | 212992 | 253952 |
| 송수신 버퍼 | net.core.wmem_max | 212992 | 16777216 |
| 송수신 버퍼 | net.ipv4.tcp_rmem | 4096 131072 6291456 | 253952 253952 16777216 |
| 송수신 버퍼 | net.ipv4.tcp_wmem | 4096 16384 4194304 | 253952 253952 16777216 |

- 사전 대비 성격으로 설정하는 것이 권고되며, 통상 소량의 메모리를 추가 사용하는 것 이외의 반대급부는 거의 없음.
- 부하 테스트 전에 적용하고 테스트에 이상이 없다면 운영 환경까지 유지하는 것이 권장됨.