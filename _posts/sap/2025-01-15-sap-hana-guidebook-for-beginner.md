---
layout: single
title: "[SAP] 삼성에서 ERP로 먹고사는 컨설턴트가 알려주는 SAP (작성중)"
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

공식 문서 등에서 미사여구로 꾸며진 글이 아니라 실제 업무를 하면서 느낀 점을 한국인이 한국어로 정리해서 저처럼 SAP를 처음 시작하려는 한국인 입문자에게는 꼭 필요한 책입니다.

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

![SAP 어플리케이션 영역 구조](/assets/images/post/sap/2025-01-15-sap-hana-guidebook-for-beginner/application_area_structure.jpg){: width="50%"}

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
