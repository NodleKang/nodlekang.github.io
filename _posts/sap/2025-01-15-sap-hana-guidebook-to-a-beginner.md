---
layout: single
title: "[SAP] 삼성에서 ERP로 먹고사는 컨설턴트가 알려주는 SAP"
date: 2025-01-15 09:50:00 +0900
categories: 
  - SAP
tag: 
  - SAP
  - CDS
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 2025년 1월에 [삼성에서 ERP로 먹고사는 컨설턴트가 알려주는 SAP] 도서를 읽고 개인적으로 필요한 내용을 정리한 것 입니다.

# 후기

공식 문서 등에서 미사여구로 꾸며진 글이 아니라 실제 업무를 하면서 느낄 수 있는 부분을 한국인이 한국어로 정리해서 저처럼 SAP를 처음 시작하려는 한국인 입문자에게는 꼭 필요한 책입니다.

책이 얇아서 금방 읽을 수 있어서 더욱 좋습니다. 

SAP를 처음 접하는 사람이라면 강력 추천합니다.

# 현장 용어

- **인터페이스를 잡는다.**: 원래 사용되던 시스템과 연결을 해 주고 잘 작동되도록 설정하는 일을 의미합니다.
- **S/4HANA**: 'SAP **business Suite for** HANA'라는 의미입니다.
- **Extended ERP**: SAP에서 주력인 ERP를 제외한 나머지 솔루션들(PLM, SRM, CRM 등)을 합쳐서 부르는 이름입니다. **Non-ERP** 라고도 합니다.

# SAP S/4HANA 제공 방식

SAP S/4HANA가 고객에게 제공되는 방식은 다음과 같습니다.

- On-premise
- Cloud
  - Private Cloud Edition
  - Public Cloud Edition

# SAP HANA DB

- 인메모리 구조
- Column Store(컬럼 형태 저장)

# SAP 주요 모듈

| 구분 | 분류 | 영역 | 모듈 | 이름 | 설명 | Core |
|:---:|:---:|:---:|:---:|:---|:---|:---:|
| 어플리케이션 | 물동 | 영업 | SD | Sales and Distribution | 영업 및 판매 영역을 지원하는 모듈 | O |
| 어플리케이션 | 물동 | 서비스 | CS | Customer Service | 고객 서비스(AS) 영역을 지원하는 모듈 | |
| 어플리케이션 | 물동 | 구매 | MM | Material Management | 내자/외자 구매 및 재고 관리를 지원하는 모듈 | O |
| 어플리케이션 | 물동 | 창고 | EWM | Extended Warehouse Management | 창고의 재고 및 입출고 관리 등을 지원하는 모듈 | |
| 어플리케이션 | 물동 | 생산 | PP | Production Planning | 생산 관리를 지원하는 모듈 | O |
| 어플리케이션 | 물동 | 품질 | QM | Quality Management | 품질 관리를 지원하는 모듈 | |
| 어플리케이션 | 물동 | 설비 | PM | Plant Maintenance | 설비 관리 업무를 지원하는 모듈 |
| 어플리케이션 | 회계 | 재무 | FI | Financial accounting | 재무회계(전표 처리, 예산 관리 등)를 지원하는 모듈 | O |
| 어플리케이션 | 회계 | 관리 | CO | Controlling | 관리회계 영역을 지원하는 모듈 | O |
| 어플리케이션 | 회계 | 자금 | TR | Treasury | 자금 관리(금융 자산 및 상품 등)를 지원하는 모듈 | |
| 어플리케이션 | 회계 | 프로젝트 | PS | Project System | 프로젝트 관리를 지원하는 모듈 | |
| 어플리케이션 | 기타 | 인사 | HCM | Human Capital Management | 인적 자본의 채용, 인사관리, 교육 등을 지원하는 모듈 | |
| 어플리케이션 | 기타 | 산업특화 | IS | Industry Solutions | 다양한 산업분야에 특화된 솔루션 | |
| 어플리케이션 | 기타 | 추가 개발 | CBO | Customer Bolt-On | ABAP(SAP 전용 개발 툴)으로 추가 개발한 영역 | |
| 인프라 | | | BC | Basis | 시스템의 설치, 운영, 모니터링 등을 지원하는 모듈 | |

# SAP 어플리케이션 영역 구조

![SAP 어플리케이션 영역 구조](/assets/images/post/sap/2025-01-15-sap-hana-guidebook-to-a-beginner/application_area_structure.jpg)

SAP에서 <span style="color:red">**표준**</span>은 <span style="color:red">**핵심모듈(Standard)와 산업특화 영역(IS)**</span>을 포함합니다.

# SAP CBO(Customer Bolt-On)

CBO는 사용자의 필요에 의해 더해지는 영역입니다.

CBO 프로그램 구분법: **프로그램ID와 트랜잭션 코드(T-Code)가 <span style="color:red">'Y'</span> 나 <span style="color:red">'Z'</span>로 시작하는 프로그램**은 CBO 프로그램입니다.

<div class="notice-info" markdown="1">
* 프로그램ID: 프로그램의 이름

* 트랜잭션 코드(T-Code): 프로그램의 별명
</div>

CBO는 크게 다섯가지 유형이 있습니다:
- 온라인: CRUD 모두 가능
- 리포트: 필요한 정보를 조합해서 보여줌
- 인터페이스: 다른 시스템과 데이터를 주고 받기
- BDC: 반복 작업(엑셀의 매크로 기능과 유사)
- Migration: 시스템 이관시 필요

# SAP를 사용하는 이유

- 문제가 발생하면 CBO 영역만 분석하면 됩니다.
- 'SAP 표준의 데이터는 맞다'는 믿음이 전 세계적으로 인정됩니다.
- 사용자가 데이터를 마음대로 변경할 수 없습니다.

# SAP 컨설턴트가 하는 일

- 현재 일하는 방법(AS-IS) 정리하기
- 미래 일하는 방법(TO-BE) 만들기
- 차이(Fit-Gap) 분석하기
  - Fit: 회사가 일하는 방식을 SAP솔루션이 제시하는 방식으로 맞추기
  - Gap: 회사가 일하는 방식에 맞게 추가 프로그램을 만들거나 보완하기
- 추가할 프로그램 설계하기
- ABAP 개발자와 협업하기

# ABAP 개발자가 하는 일

- SAP 컨설턴트의 설계서 이해하기
- 프로그램 개발하기
- 테스트하기

# SAP S/4HANA 구축 과정

![SAP S/4HANA 구축 과정](/assets/images/post/sap/2025-01-15-sap-hana-guidebook-to-a-beginner/sap_fit_gap.jpg)

1. 커스터마이징(Customizing)/컨피그레이션(Configuration)
  - 모든 프로세스가 포함돼 있는 S/4HANA에서 기업이 필요한 프로세스만 추출하는 과정
2. 확장(Enhancement)
  - SAP가 처음부터 일부 기능을 추가할 수 있게 열어 놓은 기능 확장
3. CBO(Customer Bolt-On)
  - 완전히 새로운 프로그램을 개발해서 추가

# SAP의 3계층 구조

![SAP의 3계층 구조](/assets/images/post/sap/2025-01-15-sap-hana-guidebook-to-a-beginner/sap_3tier.png)

# SAP Workbench

SAP Workbench는 SAP 시스템에서 ABAP 개발을 위해 사용되는 모든 개발툴의 모음입니다.

화면에 접근할 때는 T-Code <span style="color:red">**SE80**</span> 을 사용합니다.

# ABAP 개발자가 자주 사용하는 T-Code 모음

| T-Code | 이름 | 설명 |
|:---|:---|:---|
| SE80 | Object Navigater | ABAP 개발자가 사용하는 대부분의 기능을 제공합니다. (aka. SAP Workbench) |
| SE38 | ABAP 편집기 | ABAP 프로그램 소스 코드를 만들고 관리합니다. |
| SE37 | Function Builder | ABAP Function(함수)를 만들고 관리합니다. |
| SE24 | Class Builder | ABAP 클래스를 만들고 관리합니다. |
| SE18 | BAdi Builder | Business Add-Ins를 관리합니다. |
| SE19 | BAdi Builder | Business Add-Ins를 (더 자세하게) 관리합니다. |
| SE91 | Message Maintenance | 메시지 클래스 관리 |
| SE93 | 트랜잭션 유지보수 | T-Code 관리 |
| SE11 | ABAP Dictionary | 테이블, 뷰, 데이터 유형을 등을 만들고 관리합니다. |
| SE16 | 데이터 브라우저 | 테이블 데이터 조회합니다.(조회 위주) |
| SE16N | 일반 테이블 조회 | 테이블 데이터 관리합니다.(생성/변경/조회) |
| ST22 | ABAP 런타임 에러 | ABAP 프로그램 수행 중 오류를 조회합니다. (덤프 확인) |
| SHDB | 트랜잭션 리코더 | 트랜잭션을 녹은하듯이 기록합니다. (BDC 레코딩) |
| SPRO | 커스터마이징 | 컨설턴트가 회사별 설정 사항을 세팅하는 곳입니다. |
| SMARTFORMS | 스마트폼 | 각종 양식 문서를 디자인하는 곳입니다. |
| SPROXY | PROXY 관리 | 다른 시스템과의 데이터 인터페이스를 관리합니다. |
| SXI_MONITOR | XML Message Processing | PO를 통한 다른 시스템과의 인터페이스 이력을 조회합니다. |
| S000 | 표준 메뉴 | SAP가 제공하는 기본 메뉴 트리로 돌아갑니다. |
| SE43 | Edit Area Menu | 영역 메뉴를 관리합니다. |
| SM37 | Job Selection | 백그라운드 잡 로그와 계획을 조회하고 관리합니다. |
| SE09 | Transport Organizer | CTS 릴리즈/생성/변경 |
| STMS | Transport 관리 시스템 | 전송 관리 시스템(이관) |
| SU01 | User Maintenance | 사용자 관리(비밀번호 초기화 등) |
| SU01D | User Maintenance | 사용자 조회(조회 전용) |
| BAPI | BAPI Explorer | BAPI 탐색기 |
| ABAPDOCU | ABAP Documentation | ABAP 도움말(F1) |
