---
layout: single
title: "[SAP] Open SQL"
date: 2025-01-16 13:30:00 +0900
categories: 
  - SAP
tag: 
  - SAP
  - Open SQL
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 2025년 1월에 SAP Open SQL을 학습한 내용을 정리하는 것입니다.

# Open SQL 이란?

Open SQL은 SAP ABAP(Advanced Business Application Programming) 언어에서 제공하는 DB 독립적인 SQL 인터페이스 입니다.

SAP 시스템에서 DB와 상호 작용하기 위해 사용됩니다.

# Open SQL 특징

1. **데이터베이스 독립성**
  - SAP 시스템에서 사용하는 모든 데이터베이스에서 동일하게 작동합니다.
  - 즉, 특정 데이터베이스에 종속되지 않습니다.
2. **ANSI SQL과 유사한 문법**
  - ANSI SQL과 비슷한 문법을 사용하지만, SAP에서 정의한 제한된 SQL 명령어 집합만 사용할 수 있습니다.
  - SELECT, INSERT, UPDATE, DELETE 같은 기본적인 SQL 명령어를 지원합니다.
3. **내장 데이터 캐싱**
  -  SAP 애플리케이션 서버의 **테이블 버퍼(Table Buffer)**를 활용하여 데이터베이스 접근을 최적화합니다.
4. **보안 및 데이터 무결성**
  - Open SQL은 SAP의 권한 관리 시스템과 통합되어 있어, 데이터 접근 권한을 자동으로 확인합니다.

# Java 개발자 관점에서 Open SQL 이해하기

Java에서 JDBC를 사용해 데이터베이스와 상호작용하는 것과 비슷하다고 생각하면 됩니다.

# Open SQL 기본 문법

## SELECT

```abap
SELECT * FROM mara INTO TABLE @DATA(lt_mara)
  WHERE matnr = '123456'.
```

- `mara`: SAP의 표준 테이블
- `INTO TABLE`: 데이터를 ABAP 내부 테이블에 저장
- `@DATA(lt_mara)`: 최신 ABAP 문법으로 내부 테이블 선언 및 데이터 저장

## INSERT

```abap
INSERT INTO ztable (field1, field2)
  VALUES ('value1', 'value2').
```

- `ztable`: 사용자 정의 테이블
- `field1`, `field2`: 테이블의 필드

## UPDATE

```abap
UPDATE ztable
  SET field1 = 'new_value'
  WHERE field2 = 'value2'.
```

## DELETE

```abap
DELETE FROM ztable
  WHERE field1 = 'value1'.
```




# CDS(Core Data Services)란 무엇인가요?

CDS는 **SAP에서 제공하는 데이터 모델링 프레임워크**로, SAP HANA DB와 같은 데이터베이스에서 데이터를 효율적으로 정의하고 관리하기 위해 사용됩니다.

아주 간단히 생각하면 **데이터를 정의하고 조작하는 고급 SQL 기반 언어**라고 생각해도 됩니다.

CDS는 **SAP의 ABAP 프로그래밍 모델과 SAP HANA에서 모두 사용**됩니다.

<div class="notice-info" markdown="1">
**CDS를 이해하기 위한 비유**

CDS는 데이터베이스와 어플리케이션 사이의 스마트 데이터 정의서 입니다.

기존 SQL로 데이터를 정의하는 것은 "수작업으로 건물을 짓는 것"과 비슷합니다.

반면, CDS는 "건축설계도"를 작성하는 것과 같습니다. 설계도를 기반으로 데이터베이스가 효율적으로 데이터를 처리하도록 돕습니다.
</div>



1. CDS 개요
  1) CDS 기본 개념
  2) S/4HANA 표준 CDS View
  3) Custome CDS Vew 생성
    (1) In-App Key User Extensibility Tool
    (2) ADT(New Data Definition)에서 Custom CDS View 만들기
    (3) 표준 CDS View Extension 과 활용
2. S/4HANA VDM 의 표준 CDS View
  1) RAP Programming Model 중심으로 개발되어 있는 SD 모듈의 Sales Order 관련 CDS View 예시
  2) Sourcing & Procurement의 Purchase Order 관련 CDS View 예시
  3) Manufacturing PP 모듈의 Production Order 관련 CDS View 예시
  4) Finance FI 모듈의 General Ledger 관련 CDS View 예시 (ADDOC Table 중심)
3. CDS Data Modeling
  1) 왜 CDS View 인가?
    (1) SQL과 ABAP Open SQL
    (2) ABAP에서 Classical Open SQL의 한계
    (3) SAP HANA와 Code-to-Data Programming 방식의 필요성
  2) CDS 개념과 ABAP CDS
    (1) ABAP CDS 기능
    (2) ABAP CDS View의 기술적 구성
    (3) CDS Annotation
    (4) CDS View의 기술적 유형
    (5) ABAP CDS View 생성
  3) CDS View 활용
    (1) Analytics
    (2) OData Service 기반 Fiori App과 API
    (3) ABAP Program 에서
  4) CDS View 모델링 가이드
    (1) Interface View: I_Sales Order 예시
    (2) Consumption View
    (3) CDS Meta Data Extension
    (4) RAP BO TP CDS View
    (5) API View, RAP BO Interface View
    (6) Analytical CDS View - Cube 설계/개발 가이드
    (7) Analytical CDS View - Query 설계/개발 가이드
    (8) Enterprise Search Models에서 사용되는 Search Object CDS View
  5) CDS View 개발 및 Performace 가이드
    (1) CDS View General 가이드라인
    (2) CDS VDM 가이드라인
    (3) CDS View Status 가이드 라인
    (4) CDS Extension 가이드라인
    (5) CDS Annotaion 가이드라인
4. Code Push Down 이해
  1) CDS - A Most popular way of code pushdown in ABAP
  2) AMDP (ABAP Managed Database Procedure)
  3) New Open SQL(Open SQL Enhancement)
5. 표준 CDS View 확장: Field, Table 추가 Enhancement
  1) In-App Extensibility CFL(Custom Field and Logic) Tool에 대한 기술적 이해
    (1) Table Field 추가(시스템 자동 Generation)
    (2) CDS View Extention(시스템 자동 Generation)
    (3) Data Source에 필드 추가
    (4) UI에 필드 추가
    (5) BAPI에 필드 추가
    (6) CFL을 활용한 구매 오더, 구매 Info Record 필드 추가
  2) ADT Extend View로 필드 추가
    (1) 기존 DB Table에 있는 필드 추가
    (2) 기존 DB에 없는 새로운 필드 추가
  3) ADT Extend View로 Table Association 추가: 다른 Table/CDS View와 Association
  4) Meta Data Extension을 통한 필드 UI Annotaion 추가



---

# CDS 기초 가이드: SAP S/4HANA 개발자를 위한 입문서

## 1. CDS란 무엇인가?
### 1.1 CDS 기본 개념
- CDS(Core Data Services)의 정의
- SAP HANA와 CDS의 관계
- CDS의 주요 특징과 장점

### 1.2 SAP S/4HANA에서의 CDS View
- 표준 CDS View란?
- Custom CDS View란?

## 2. Custom CDS View 생성 방법
### 2.1 In-App Key User Extensibility Tool
- In-App Extensibility의 개념
- Key User Tool을 사용한 Custom CDS View 생성

### 2.2 ADT(ABAP Development Tools)에서 Custom CDS View 생성
- ADT란 무엇인가?
- New Data Definition을 사용한 Custom CDS View 생성

### 2.3 표준 CDS View 확장
- 표준 CDS View Extension의 개념
- 확장된 CDS View의 활용 사례

## 3. CDS View의 활용 사례
### 3.1 SAP S/4HANA VDM의 표준 CDS View
- VDM(View Data Model)의 개념
- 주요 모듈별 표준 CDS View 예시
  - SD(Sales Order)
  - MM(Purchase Order)
  - PP(Production Order)
  - FI(General Ledger)

### 3.2 CDS View의 실무 활용
- Analytics(분석)에서의 활용
- OData Service 기반 Fiori App과 API
- ABAP 프로그램에서의 활용

## 4. CDS View 모델링 기초
### 4.1 CDS View의 기술적 구성
- ABAP CDS View의 구조
- CDS Annotation의 역할
- CDS View의 유형(Interface View, Consumption View 등)

### 4.2 CDS View 모델링 가이드
- Interface View 설계 예시
- Consumption View 설계 예시
- Analytical CDS View 설계 가이드(Cube와 Query)

## 5. CDS View 개발 및 성능 최적화
### 5.1 CDS View 개발 가이드라인
- CDS View 작성 시 유의사항
- CDS Annotation 활용 가이드

### 5.2 성능 최적화
- Code-to-Data Programming의 필요성
- CDS View 성능 개선을 위한 팁

## 6. Code Push Down 이해
### 6.1 Code Push Down이란?
- Code Push Down의 개념과 필요성
- SAP HANA에서의 Code Push Down

### 6.2 CDS와 Code Push Down
- CDS를 활용한 Code Push Down
- AMDP(ABAP Managed Database Procedure)와의 비교
- New Open SQL과의 관계

## 7. 표준 CDS View 확장
### 7.1 In-App Extensibility를 활용한 확장
- Custom Field and Logic(CFL) Tool의 개념
- 필드 추가 및 UI 확장 방법

### 7.2 ADT를 활용한 확장
- Extend View를 사용한 필드 추가
- Table Association 추가 방법

### 7.3 Meta Data Extension 활용
- UI Annotation 추가를 통한 사용자 경험 개선

---

이 목차는 초보자가 CDS의 기본 개념을 이해하고, 실무에서 활용할 수 있는 기술을 익히는 데 초점을 맞추고 있습니다. 각 섹션은 간단한 설명과 실습 예제를 포함하도록 작성하면 더욱 효과적일 것입니다.
