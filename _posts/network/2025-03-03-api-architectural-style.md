---
layout: single
title: "API 아키텍처 스타일 비교"
date: 2025-03-03 09:00:00 +0900
categories: 
  - Network
tag: 
  - API
  - SOAP
  - REST
  - GraphQL
  - RPC
toc: true
toc_label: 목차
toc_sticky: true
---

| 구분 | SOAP<br>(Simple Object Access Protocol) | REST<br>(REpresentational State Transfer) | GraphQL | RPC<br>(Remote Procedure Call) |
|---|---|---|---|---|
| 주요 특성 | 메시지 봉투 구조<br>envelped message structure | 6가지 아키텍처 제약 조건 준수 | 스키마 및 타입 시스템 | 로컬 프로시저 호출 |
| 포멧 | XML만 가능 | XML, JSON, HTML, plain text | JSON | JSON, XML, Protobuf, Thrift, Flatbuffers |
| 학습 난이도 | 어려움 |  쉬움 | 중간 | 쉬움 |
| 커뮤니티 | 작음 | 큼 | 성장 중 | 큼 |
| 사용 사례 | - pg(payment gateways) <br>- id 관리 <br>- CRM 솔루션 <br>- 레거시 시스템 지원 | - public APIs <br>- 단순 리소스기반 어플리케이션 | - mobile APIs <br>- 복잡한 시스템 <br>- 마이크로서비스 | - 명령 및 액션 지향 API <br>- 대규모 마이크로서비스 시스템에서의 고성능 통신 |
