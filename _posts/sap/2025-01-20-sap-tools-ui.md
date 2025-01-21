---
layout: single
title: "[SAP] 도구들"
date: 2025-01-20 13:30:00 +0900
categories: 
  - SAP
tag: 
  - SAP
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 2025년 1월에 SAP 관련 기초 개념과 화면을 학습한 내용입니다.

# SAP GUI

SAP 기본 화면 구성은 그림과 같습니다.

`커맨드필드`에 T-CODE(트랜잭션코드)를 입력해서 프로그램을 실행합니다.

![SAP GUI](/assets/images/post/sap/2025-01-20-sap-tools-ui/sap_gui.png)

## ABAP Workbench

ABAP 프로그램 구현을 위해서는 `ABAP Workbench` 를 사용해야 합니다.

`ABAP Workbench`는 SAP GUI에 커맨드 필드에 T-CODE(트랜잭션코드) `SE80`를 입력해서 직접 접근할 수 있습니다.

`ABAP Workbench`에는 ABAP Program, Function, Table 등을 생성할 수 있는 많은 개발 도구가 구성되어 있습니다.

![ABAP Workbench](/assets/images/post/sap/2025-01-20-sap-tools-ui/abap_workbench.png){: width="50%"}

ABAP Workbench Tool에서 자주 사용되는 개발관련 T-CODE(Transaction Code)들은 아래와 같습니다.

| 구분 | T-CODE | 설명 |
|:---:|:---|:---|
| 개요 | SE80 | 오브젝트 탐색기 (Object Navigator) |
| 개요 | SE16 | 데이터 브라우저 (Data Browser) |
| 개요 | SE16N | 일반 테이블 조회 (General Table Display) |
| 개요 | SE09 | 전송 조직자 (Transport Organizer) |
| 개요 | BAPI | BAPI 탐색기 (BAPI Explorer) |
| 개발 | SE11 | ABAP 딕셔너리 (ABAP Dictionary) |
| 개발 | SE38 | ABAP 편집기 (ABAP Editor) |
| 개발 | SE37 | 기능 빌더 (Function Builder) |
| 개발 | SE24 | 클래스 빌더 (Class Builder) |
| 개발 | SE91 | 메시지 관리 (Message Maintenance) |
| 개발 | SE93 | 트랜잭션 관리 (Maintain Transaction) |
| 테스트 | SM21 | 시스템로그 (System Log) |
| 테스트 | ST05 | Performance Trace |
| 테스트 | ST22 | 덤프 분석 (ABAP Runtime Error) |
| 유틸리티 | ABAPHELP | 키워드 문서 (ABAP Keyword Documentation) |
| 유틸리티 | ABAPDOCU | 데모 (ABAP Documentation and Examples) |
| 유틸리티 | SQVI | 퀵 뷰어(Quick Viewer) |
| 유틸리티 | SQ01, SQ02, SQ03 | SAP Query |

## ABAP Dictionary

ABAP Dictionary는 ABAP 프로그램에서 사용되는 Table, View, Structure, Type 등과 같은 Object들을 의미합니다.

ABAP Dictionary는 Object들의 정보(metadata, data, definition)를 중앙 집중식으로 관리합니다.

`ABAP Dictionary`는 SAP GUI에 커맨드 필드에 T-CODE(트랜잭션코드) `SE11`를 입력해서 직접 접근할 수 있습니다.

- ABAP Dictionary와 ABAP Workbench는 동적으로 연결되어 있습니다:
  - ABAP Dictionary 내에 변경된 Object의 metadata는 모든 시스템 Object에게 알려집니다.
  - 모든 데이터를 중앙집중식으로 관리하여 데이터의 무결정, 일관성, 안정성을 보장합니다.

![ABAP Dictionary](/assets/images/post/sap/2025-01-20-sap-tools-ui/abap_dictionary.png)

ABAP Dictionary 관련 화면에 대한 추가 설명은 [https://pickylog.tistory.com/5](https://pickylog.tistory.com/5) 블로그를 참고하면 좋습니다.

# SAP ADT(ABAP Development Tools)

<span style="color:red">**Core Data Services (CDS) 뷰**</span>와 <span style="color:red">**ABAP Managed Database Procedures (AMDP)**</span>를 정의하고 관리하기 위해서는 **ADT**를 사용하는 것이 필수적입니다.

SAP ADT(ABAP Development Tools)는 SAP의 ABAP 개발을 위한 통합 개발 환경으로, Eclipse에 ADT 플러그인을 설치해서 사용합니다.

![Eclipse IDE](https://developers.sap.com/tutorials/abap-install-adt/jcr:content.github-proxy.1731587701.file/eclipse11.png)

![Add ADT plugin](https://developers.sap.com/tutorials/abap-install-adt/jcr:content.github-proxy.1731587701.file/adt.png)
