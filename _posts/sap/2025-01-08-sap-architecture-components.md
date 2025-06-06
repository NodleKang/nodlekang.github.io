---
layout: single
title: "[SAP 아키텍처] 주요 컴포넌트"
date: 2025-01-08 14:00:00 +0900
categories: 
  - SAP
tag: 
  - SAP 컴포넌트
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 SAP 관련 업무 수행을 위해 학습하면서 알게된 SAP 아키텍처에 주요 컴포넌트들을 정리한 것 입니다.

# 요약 

|                   컴포넌트                    | 설명                                        |
|:-----------------------------------------:|:------------------------------------------|
|              Web Dispatcher               | 로드 밸런싱 및 트래픽 관리.                          |
|   ASCS (ABAP Central Services Instance)   | 중앙 서비스(메시지 서버, Enqueue 서버) 제공.            |
|     ERS (Enqueue Replication Server)      | ASCS의 Enqueue 서버 데이터 복제 및 장애 복구 지원.       |
|     PAS (Primary Application Server)      | 주요 애플리케이션 서버로 사용자 요청을 처리하는 핵심 컴포넌트        |
|    AAS (Additional Application Server)    | PAS의 부하를 분산하기 위해 추가로 배포된 애플리케이션 서버        |
|           HANA DB (SAP HANA DB)           | SAP 시스템의 데이터베이스로, 인메모리 데이터베이스로 고속 데이터 처리. |
|       HSR (HANA System Replication)       | HANA DB의 고가용성 및 재해 복구 지원.                 |
|       ADS (Adobe Document Services)       | PDF 문서 생성 및 처리.                           |
|              Content Server               | 문서 저장 및 관리.                               |
| Solman ABAP (Solution Manager ABAP Stack) | SAP 시스템 관리 및 모니터링(ABAP 기반).               |
| Solman Java (Solution Manager Java Stack) | ITSM 및 대시보드 관리(Java 기반).                  |
|        Cockpit (SAP HANA Cockpit)         | HANA DB 관리 및 모니터링 도구.                     |
|                 SAProuter                 | 네트워크 보안 및 라우팅.                            |

# Web Dispatcher

- 역할:
  - SAP 시스템의 로드 밸런서 역할을 수행합니다.
  - 클라이언트 요청(HTTP/HTTPS)을 적절한 SAP 애플리케이션 서버(PAS, AAS)로 분배합니다.
  - 보안 및 트래픽 관리 기능을 제공합니다.
- 주요 기능:
  - 사용자 요청을 처리할 서버를 선택하여 시스템 부하를 분산.
  - SSL 종료(SSL Termination) 및 보안 트래픽 관리.

# ASCS (ABAP Central Services Instance)

- 역할:
  - SAP 시스템의 중앙 서비스를 제공하는 핵심 컴포넌트.
  - 메시지 서버와 Enqueue 서버를 포함하여 SAP 애플리케이션 서버 간의 통신과 락 관리를 담당.
- 주요 기능:
  - Message Server: 애플리케이션 서버 간의 통신 및 로드 밸런싱.
  - Enqueue Server: 데이터 락 관리(동시성 제어).

# ERS (Enqueue Replication Server)

- 역할:
  - ASCS의 Enqueue Server 데이터를 복제하여 고가용성을 보장.
  - ASCS 장애 시 복제된 데이터를 사용하여 Enqueue Server 상태를 복구.
- 주요 기능:
  - Enqueue 데이터 복제 및 장애 복구 지원.

# PAS (Primary Application Server)

- 역할:
  - SAP 시스템의 주요 애플리케이션 서버로, 사용자 요청을 처리하는 핵심 컴포넌트.
  - SAP GUI 또는 웹 브라우저를 통해 들어오는 요청을 처리.
- 주요 기능:
  - 사용자 세션 관리.
  - 비즈니스 로직 실행.
  - 데이터베이스와의 통신.

# AAS (Additional Application Server)

- 역할:
  - PAS의 부하를 분산하기 위해 추가로 배포된 애플리케이션 서버.
  - PAS와 동일한 기능을 수행하지만, 중앙 서비스(ASCS)는 사용하지 않음.
- 주요 기능:
  - 사용자 요청 처리.
  - 시스템 확장 및 부하 분산.

# HANA DB (SAP HANA Database)

- 역할:
  - SAP 시스템의 데이터베이스로, 인메모리 기술을 기반으로 빠른 데이터 처리와 분석을 지원.
- 주요 기능:
  - 트랜잭션 데이터와 분석 데이터를 실시간으로 처리.
  - 데이터 압축 및 고성능 쿼리 실행.
  - SAP 애플리케이션과의 최적화된 통합.

# HSR (HANA System Replication)

- 역할:
  - SAP HANA 데이터베이스의 고가용성과 재해 복구를 위한 복제 메커니즘.
- 주요 기능:
  - HANA DB의 데이터를 실시간으로 복제하여 장애 발생 시 빠르게 복구.
  - 주 데이터베이스(Primary)와 복제 데이터베이스(Secondary) 간의 동기화.

# ADS (Adobe Document Services)

- 역할:
  - SAP 시스템에서 PDF 문서 생성 및 처리를 지원하는 서비스.
- 주요 기능:
  - SAP 시스템에서 양식(Form) 생성 및 출력.
  - PDF 문서의 데이터 바인딩 및 전자 서명 처리.

# Content Server

- 역할:
  - SAP 시스템의 문서 관리를 위한 저장소 역할.
- 주요 기능:
  - SAP 시스템에서 생성된 문서(예: PDF, 이미지, 계약서 등)를 저장 및 관리.
  - 데이터베이스와 별도로 대용량 데이터를 효율적으로 저장.

# Solman ABAP (Solution Manager ABAP Stack)

- 역할:
  - SAP Solution Manager의 ABAP 기반 컴포넌트로, SAP 시스템의 관리 및 모니터링을 지원.
- 주요 기능:
  - 시스템 모니터링, 변경 관리, 테스트 관리.
  - SAP 시스템의 기술적 지원 및 유지보수.

# Solman Java (Solution Manager Java Stack)

- 역할:
  - SAP Solution Manager의 Java 기반 컴포넌트로, 특정 기능(예: ITSM, 대시보드)을 지원.
- 주요 기능:
  - IT 서비스 관리(ITSM).
  - 대시보드 및 보고서 생성.
  - SAP 시스템의 Java 기반 애플리케이션 관리.

# Cockpit (SAP HANA Cockpit)

- 역할:
  - SAP HANA 데이터베이스의 관리 및 모니터링 도구.
- 주요 기능:
  - HANA DB의 상태 모니터링.
  - 백업 및 복구 관리.
  - 성능 최적화 및 사용자 관리.

# SAProuter

- 역할:
  - SAP 시스템의 네트워크 보안 및 라우팅을 담당하는 컴포넌트.
- 주요 기능:
  - 외부 네트워크(예: SAP 지원팀)와 내부 SAP 시스템 간의 안전한 연결 제공.
  - 방화벽 뒤에 있는 SAP 시스템에 대한 접근 제어.
  - 네트워크 트래픽 암호화 및 인증.
