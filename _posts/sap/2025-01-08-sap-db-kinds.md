---
layout: single
title: "[SAP] DB"
date: 2025-01-08 14:55:00 +0900
categories: 
  - SAP
tag: 
  - SAP HANA DB
  - SAP MaxDB
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 SAP 시스템에서 사용할 수 있는 DB들을 조사한 내용을 정리한 것입니다.

# 요약

| DB | Owner | 설명                                                |
|:---|:---|:--------------------------------------------------|
| SAP HANA | SAP | 인메모리 기반, 실시간 데이터 처리 및 분석. SAP S/4HANA의 기본 DB.     |
| SAP MaxDB | SAP | SAP NetWeaver와 최적화된 관계형 DB. 무료 사용 가능.             |
| SAP ASE | SAP(Sybase) | 트랜잭션 처리에 최적화된 관계형 DB. SAP Business Suite와 통합 가능.  |

# SAP HANA

- **SAP의 주력 데이터베이스**로, 인메모리(In-Memory) 기술을 기반으로 설계된 고성능 데이터베이스.
- 특징:
  - 데이터를 메모리에 저장하여 실시간 데이터 처리와 분석 가능.
  - OLTP(Online Transaction Processing)와 OLAP(Online Analytical Processing)를 동시에 처리하는 하이브리드 데이터베이스.
  - SAP S/4HANA, SAP BW/4HANA 등 최신 SAP 솔루션의 기본 데이터베이스로 사용.
- 장점:
  - 빠른 데이터 처리 속도.
  - 실시간 분석 및 보고.
  - 데이터 압축 및 효율적인 메모리 사용.

# SAP MaxDB

- SAP에서 개발한 **관계형 데이터베이스 관리 시스템(RDBMS)**.
- 특징:
  - SAP NetWeaver 기반 애플리케이션과 최적화된 통합.
  - 소규모부터 대규모 시스템까지 확장 가능.
  - 비SAP 애플리케이션에서도 사용 가능.
- 장점:
  - 안정성과 확장성.
  - 무료로 사용 가능(오픈 소스 형태).

# SAP ASE (Adaptive Server Enterprise, 이전 Sybase ASE)

- SAP가 인수한 Sybase의 **관계형 데이터베이스.**
- 특징:
  - 트랜잭션 처리에 최적화된 데이터베이스.
  - SAP Business Suite와 통합 가능.
- 장점:
  - 높은 트랜잭션 처리 성능.
  - SAP 애플리케이션과의 긴밀한 통합.
