---
layout: single
title: "[SAP] BTP (Business Technology Platform)"
date: 2025-01-15 17:00:00 +0900
categories: 
  - SAP
tag: 
  - SAP BTP
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 2025년 1월에 [Explaining SAP Business Technology Platform (SAP BTP) to a Beginner](https://community.sap.com/t5/technology-blogs-by-sap/explaining-sap-business-technology-platform-sap-btp-to-a-beginner/ba-p/13557182) 글을 읽고 정리한 내용입니다.

# SAP Business Technology Platform (SAP BTP)란 무엇인가?

SAP BTP는 조직이 클라우드 솔루션을 구축하거나 기존 SAP 시스템을 확장하는 데 필요한 다양한 서비스와 솔루션을 제공하는 플랫폼입니다.

SAP BTP는 기업이 필요한 모든 서비스가 통합된 패키지라고 생각하면 됩니다. 기업은 통합된 패키지에서 필요한 부분만 사용하면 됩니다.

# SAP BTP의 기술적 측면 이해하기

SAP BTP는 개발자가 클라우드 솔루션을 구축하고 기존 SAP 솔루션을 확장하는 데 필요한 다양한 기능을 제공합니다.

1. **Platform-as-a-Service (PaaS)**:
  - SAP BTP는 클라우드 솔루션을 구축하고 배포할 수 있는 PaaS를 제공합니다. Cloud Foundry, ABAP, Kyma와 같은 다양한 환경을 통해 개발자는 원하는 프로그래밍 언어를 선택할 수 있습니다.
2. **개발 환경**:
  - **SAP Business Application Studio**라는 서비스가 제공되어, **모든 플러그인이 사전 설치된 개발 환경을 제공**합니다. 이를 통해 개발자는 즉시 코딩을 시작할 수 있습니다.
3. **간편한 빌드 및 배포**:
  - SAP Business Application Studio와 MTA Build Tool (MBT), BTP CLI 등을 사용하여 빌드와 배포를 간편하게 할 수 있습니다.
4. **데이터베이스 서비스**:
  - SAP HANA Cloud라는 데이터베이스 서비스를 제공하여, 대량의 데이터 저장 및 처리, 실시간 데이터 접근을 지원합니다.
5. **비즈니스 로직 집중**:
  - **SAP Cloud Application Programming Model과 같은 프레임워크**를 통해 개발자는 복잡한 비즈니스 요구 사항 해결에 집중할 수 있습니다.
6. 통합 서비스:
  - **SAP Integration Suite를 통해 SAP 및 비SAP 시스템 간의 통합**을 간소화하여, 시스템 통합 및 유지 관리에 소요되는 시간을 줄입니다.

# SAP BTP 환경

SAP BTP에서 <span style="color:red">**환경**</span>은 **애플리케이션과 서비스를 개발, 실행 및 관리할 수 있는 PaaS 제공**을 의미합니다.

각 **환경**은 **특정 작업과 기술에 맞게 설계된 공간**으로, 다양한 소프트웨어 개발 및 운영을 위한 도구와 기술을 제공합니다. 

SAP BTP는 다음과 같은 **주요 환경**을 제공합니다:

1. **Cloud Foundry**:
다양한 프로그래밍 언어와 프레임워크를 지원하는 오픈 플랫폼으로, 클라우드 애플리케이션을 쉽게 개발하고 배포할 수 있습니다.
2. **ABAP**:
SAP의 전통적인 프로그래밍 언어인 ABAP을 사용하는 개발 환경으로, SAP 애플리케이션을 확장하고 맞춤화하는 데 적합합니다.
3. **Kyma**:
Kubernetes 기반의 오픈 소스 플랫폼으로, 클라우드 네이티브 애플리케이션을 개발하고 관리하는 데 사용됩니다.
4. **Neo**:
이전의 PaaS 제공으로, 현재는 서비스 종료 예정입니다.

# SAP BTP의 역사

- **2012**: SAP는 "SAP NetWeaver Cloud"라는 이름으로 첫 번째 PaaS 제공을 시작했습니다. 코드명은 Neo로, NetWeaver On-Demand의 약자입니다.
- **2013**: SAP NetWeaver Cloud가 "SAP HANA Cloud Platform"으로 이름이 변경되었습니다.
- **2017**: Cloud Foundry 환경이 Neo와 함께 추가되었습니다.
- **2017**: "HANA"라는 용어가 이름에서 삭제되었고, 새로운 이름은 "SAP Cloud Platform"이 되었습니다. 이 플랫폼은 안전한 클라우드 환경에서 새로운 애플리케이션을 생성하거나 기존 SAP 애플리케이션을 확장하는 PaaS 제공이었습니다.
- **2018**: ABAP 환경이 SAP Cloud Platform에 추가되었습니다.
- **2020**: Kyma 환경이 SAP Cloud Platform에 추가되었습니다.
- **2021**: SAP Cloud Platform 브랜드가 공식적으로 퇴출되었고, **SAP의 One Platform 전략을 지원하기 위해 SAP BTP가 새 브랜드 이름으로 도입**되었습니다. SAP BTP는 확장된 기능을 제공하지만, 기본적으로는 이전의 SAP Cloud Platform과 동일한 핵심 기능을 유지합니다.
