---
layout: single
title: "[SAP] 기술 전환 방향"
date: 2025-01-08 16:30:00 +0900
categories: 
  - SAP
tag: 
  - SAP NetWeaver
  - SAP BTP
  - SAP PCE
  - SAP HANA
  - SAP ECC
  - SAP Fiori
  - SAPUI5
  - SAP RAP
  - SAP ABAP
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 2025년 1월에 SAP 사업과 기술 방향 전환을 학습한 내용입니다.

# SAP의 사업 방향 전환

전통적으로 SAP는 온프레미스(On-Premise) ERP 솔루션(SAP ECC 등)을 제공해왔습니다. 

그러나 클라우드 컴퓨팅의 성장과 고객의 요구 변화에 따라 클라우드 기반 솔루션(SAP S/4HANA Cloud, SAP BTP 등)을 중심으로 전환하고 있습니다.

# SAP 기술 스택

## SAP 기술 스택 전환 방향

1. **SAP ECC -> SAP S/HANA**
   - SAP는 기존의 SAP ECC(ERP Central Component)를 SAP S/4HANA로 전환하고 있습니다.
   - SAP S/4HANA는 SAP HANA 데이터베이스를 기반으로 하며, 실시간 데이터 처리와 분석, 간소화된 데이터 모델을 제공합니다.
2. **SAP NetWeaver -> SAP BTP(Business Technology Platform)**
   - SAP NetWeaver는 전통적인 온프레미스(On-Premise) 어플리케이션 플랫폼이었으나, SAP는 이를 SAP BTP(Business Technology Platform)로 전환하고 있습니다. 
   - SAP BTP는 클라우드 기반의 통합 플랫폼으로, 데이터 관리, 애플리케이션 개발, 통합, 확장성을 제공합니다.
3. **온프레미스 -> SAP PCE(Private Cloud Edition)**
   - SAP PCE(Private Cloud Edition)는 온프레미스와 클라우드의 장점을 결합한 솔루션으로, 기존 SAP ECC 고객이 클라우드로 전환할 수 있는 경로를 제공합니다. 
   - 이는 SAP S/4HANA를 클라우드 환경에서 사용할 수 있도록 지원합니다.
4. **SAP HANA 중심의 데이터 관리** 
   - SAP는 데이터베이스 기술의 중심을 SAP HANA로 전환했습니다. 
   - SAP HANA는 인메모리(In-Memory) 데이터베이스로, 빠른 데이터 처리와 실시간 분석을 가능하게 합니다.

## SAP 기술 스택 주요 구성 요소

 | **SAP 솔루션**           | **설명**                                                                                                                                                          | **특징**                                                                                                                                                                 |
|---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **SAP NetWeaver**         | SAP의 전통적인 온프레미스 애플리케이션 플랫폼으로, ERP, CRM, SCM 등 다양한 SAP 애플리케이션을 실행하는데 사용.                                                    | 현재 SAP BTP로 대체되고 있으며, 클라우드 중심 플랫폼으로 전환 중.                                                                                                     |
| **SAP ECC (ERP Central Component)** | 전통적인 ERP 솔루션으로, 재무, 물류, 인사 등 기업 핵심 비즈니스 프로세스를 관리.                                                                            | SAP S/4HANA로 대체 중이며, 2027년까지 SAP ECC에 대한 지원 종료 예정.                                                                                                 |
| **SAP HANA**              | 인메모리 데이터베이스로, 데이터를 메모리에 저장하여 실시간 분석 및 빠른 데이터 처리 가능.                                                                            | SAP S/4HANA와 같은 최신 SAP 솔루션의 핵심 데이터베이스.                                                                                                              |
| **SAP S/4HANA**           | 차세대 ERP 솔루션으로, SAP HANA 데이터베이스 기반으로 설계.                                                                                                       | 간소화된 데이터 모델, 실시간 데이터 처리, Fiori UI 제공. 클라우드 및 온프레미스에서 사용 가능.                                                                        |
| **SAP BTP (Business Technology Platform)** | 클라우드 기반 기술 플랫폼으로, 데이터 관리, 애플리케이션 개발, 통합 및 확장성을 제공.                                                                                  | SAP 및 비SAP 시스템 통합, 사용자 정의 애플리케이션 개발, 데이터 활용 인사이트 제공.                                                                                   |
| **SAP PCE (Private Cloud Edition)** | SAP S/4HANA를 클라우드 환경에서 사용할 수 있도록 지원.                                                                                                   | 기존 온프레미스 고객의 클라우드 전환 경로 제공. 클라우드 유연성과 온프레미스 제어성을 결합한 하이브리드 솔루션.                                                                                 |

# SAP 프론트엔드/UI 기술 스택

## SAP 프론트엔드/UI 기술 스택 전환 방향

1. **SAP GUI → SAP Fiori**
   - 전통적으로 SAP는 SAP GUI(Graphical User Interface)를 사용하여 ERP 시스템과 상호작용했습니다. SAP GUI는 데스크톱 기반의 클라이언트 애플리케이션으로, 복잡하고 직관적이지 않은 사용자 경험으로 인해 현대적인 요구를 충족하기 어려웠습니다.
   - SAP는 이를 대체하기 위해 SAP Fiori를 도입했습니다. SAP Fiori는 **웹 기반**의 현대적인 UI/UX 프레임워크로, 사용자 친화적이고 직관적인 경험을 제공합니다.
2. **SAP Fiori 중심의 UX 전략**
   - SAP Fiori는 SAP의 새로운 UX 전략의 핵심입니다. Fiori는 단순하고 직관적인 디자인 원칙을 기반으로 하며, 모바일, 태블릿, 데스크톱 등 다양한 디바이스에서 일관된 사용자 경험을 제공합니다.
   - SAP Fiori는 SAP Fiori Launchpad라는 중앙화된 대시보드를 통해 애플리케이션에 접근할 수 있도록 하며, 역할 기반(Role-Based)으로 사용자에게 필요한 정보와 기능만을 제공합니다.
3. **SAPUI5 → OpenUI5**
   - SAP Fiori 애플리케이션은 SAPUI5라는 SAP의 프론트엔드 프레임워크를 기반으로 개발됩니다. SAPUI5는 HTML5, CSS3, JavaScript를 기반으로 하며, 반응형 디자인(Responsive Design)을 지원합니다.
   - SAPUI5는 SAP의 상용 라이브러리이고, 이를 오픈소스화한 버전이 OpenUI5입니다. OpenUI5는 SAP 외부에서도 사용할 수 있으며, 개발자 커뮤니티에서 널리 활용되고 있습니다.
4. **웹 표준 기술로의 전환**
   - SAP는 최신 웹 표준 기술(HTML5, CSS3, JavaScript)을 적극적으로 채택하여 SAPUI5와 Fiori를 개발하고 있습니다. 이를 통해 SAP 애플리케이션은 브라우저 기반으로 실행되며, 클라이언트 설치 없이도 접근할 수 있습니다.
   - SAP는 또한 TypeScript와 같은 현대적인 언어를 점진적으로 도입하여 개발 생산성을 높이고 코드 품질을 개선하고 있습니다.
5. **SAP Fiori Elements와 템플릿 기반 개발**
   - SAP는 SAP Fiori Elements를 통해 개발자들이 표준화된 템플릿을 사용하여 Fiori 애플리케이션을 빠르게 개발할 수 있도록 지원합니다.
   - Fiori Elements는 SAP의 OData 서비스와 통합되어, 백엔드 데이터를 자동으로 UI에 바인딩하고, 개발자가 최소한의 코딩으로 애플리케이션을 생성할 수 있도록 돕습니다.
6. **SAP Build Apps (Low-Code/No-Code)**
   - SAP는 SAP Build Apps(이전 명칭: SAP AppGyver)라는 Low-Code/No-Code 플랫폼을 통해 비개발자도 쉽게 SAP Fiori 스타일의 애플리케이션을 만들 수 있도록 지원합니다.
   - SAP Build Apps는 SAP BTP와 통합되어, SAP 시스템과의 데이터 연결 및 확장이 용이합니다.
7. **SAP Fiori 3로의 진화**
   - SAP는 Fiori의 최신 버전인 SAP Fiori 3를 통해 더욱 통합된 사용자 경험을 제공합니다. Fiori 3는 SAP의 모든 애플리케이션에서 일관된 디자인 언어를 제공하며, 인공지능(AI)과 머신러닝(ML)을 활용한 스마트 기능을 포함합니다.
   - 예를 들어, SAP CoPilot과 같은 디지털 어시스턴트가 Fiori 3 환경에서 사용자와 상호작용할 수 있습니다.
8. **React, Angular 등 외부 프레임워크와의 통합**
   - SAP는 SAPUI5 외에도 React, Angular, Vue.js와 같은 외부 프론트엔드 프레임워크와의 통합을 지원합니다. 이를 통해 개발자들은 자신이 선호하는 프레임워크를 사용하여 SAP 시스템과 상호작용하는 애플리케이션을 개발할 수 있습니다.
   - SAP Graph API와 같은 RESTful API를 통해 SAP 데이터를 외부 프레임워크에서 쉽게 활용할 수 있습니다.

## SAP 프론트엔드/UI 기술 스택 주요 구성 요소

| **항목**                    | **설명**                                                                                       | **특징**                                                                                                   |
|-----------------------------|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| **SAP Fiori**              | SAP의 현대적인 UX 프레임워크로, 단순하고 직관적인 사용자 경험 제공.                             | 역할 기반(Role-Based), 반응형(Responsive), 일관성(Consistency)을 핵심 원칙으로 함.                        |
| **SAPUI5**                 | SAP Fiori 애플리케이션을 개발하기 위한 프론트엔드 프레임워크.                                     | HTML5, CSS3, JavaScript 기반. 반응형 디자인과 다양한 UI 컴포넌트 제공.                                    |
| **OpenUI5**                | SAPUI5의 오픈소스 버전으로, SAP 외부에서도 자유롭게 사용 가능.                                    | SAPUI5와 동일한 기능 제공, 오픈소스 라이선스 하에 사용 가능.                                              |
| **SAP Fiori Elements**     | 템플릿 기반 개발 도구로, OData 서비스를 활용하여 표준화된 Fiori 애플리케이션을 빠르게 개발 가능.   | 개발자의 코딩 작업을 최소화하고, 일관된 UX와 표준화를 보장.                                               |
| **SAP Build Apps**         | 비개발자도 쉽게 애플리케이션을 제작할 수 있는 Low-Code/No-Code 플랫폼.                           | SAP Fiori 스타일의 앱 제작 가능. 비개발자와 개발자 모두에 적합.                                           |
| **SAP Fiori Launchpad**    | SAP Fiori 애플리케이션에 중앙화된 접근성을 제공하는 대시보드.                                     | 역할 기반으로 필요한 애플리케이션만 사용자에게 표시.                                                      |
| **SAP CoPilot**            | SAP의 디지털 어시스턴트로, Fiori 환경에서 사용자와 상호작용하며 작업을 지원.                     | 자연어 기반 인터페이스 제공. 사용자 생산성을 향상시키고 작업의 효율성을 높임.                             |

# SAP 개발 언어

SAP의 개발 언어 전환은 기존의 **ABAP 중심**에서 JavaScript, TypeScript, Java, Python 등 다양한 언어로 확장되고 있으며, **SAP BTP(Business Technology Platform)**와 같은 클라우드 플랫폼에서의 개발을 지원하기 위해 새로운 도구와 프레임워크를 도입하고 있습니다.

## SAP 개발 언어 전환 방향

1. **ABAP에서 클라우드 친화적인 언어로 확장**
   - SAP는 여전히 ABAP(Advanced Business Application Programming)를 핵심 언어로 유지하고 있지만, 클라우드 환경과 최신 개발 요구사항에 맞추어 **ABAP RESTful Application Programming Model (RAP)**과 같은 새로운 개발 모델을 도입했습니다.
   - 동시에, SAP는 JavaScript, TypeScript, Java, Python 등 현대적인 언어를 지원하여 개발자들이 SAP 시스템과 통합된 애플리케이션을 더 유연하게 개발할 수 있도록 하고 있습니다.
2. **클라우드 네이티브 개발 지원**
   - SAP는 클라우드 네이티브 개발을 지원하기 위해 SAP BTP를 중심으로 다양한 언어와 프레임워크를 지원합니다.
   - 개발자들은 SAP BTP에서 Node.js, Java, Python 등을 사용하여 확장 애플리케이션을 개발할 수 있습니다.
3. **Low-Code/No-Code 개발 도구 도입**
   - SAP는 SAP Build Apps(이전 명칭: SAP AppGyver)와 같은 Low-Code/No-Code 도구를 통해 비개발자도 쉽게 애플리케이션을 개발할 수 있도록 지원합니다.
   - 이러한 도구는 프로그래밍 언어에 대한 깊은 지식 없이도 SAP 시스템과 통합된 애플리케이션을 생성할 수 있게 합니다.
4. **멀티 언어 지원**
   - SAP는 다양한 개발 언어를 지원하여 개발자들이 자신이 선호하는 언어를 사용해 SAP 시스템과 상호작용할 수 있도록 하고 있습니다.
   - 예를 들어, SAP Graph API와 같은 RESTful API를 통해 JavaScript, Python, Java 등 다양한 언어로 SAP 데이터를 활용할 수 있습니다.
5. **오픈소스 및 커뮤니티 중심의 접근**
   - SAP는 오픈소스 기술을 적극적으로 도입하고 있으며, SAPUI5의 오픈소스 버전인 OpenUI5와 같은 프로젝트를 통해 개발자 커뮤니티와 협력하고 있습니다.
   - 또한, SAP는 클라우드 네이티브 개발을 위해 Kubernetes, Docker와 같은 오픈소스 기술을 활용합니다.

## SAP 개발 언어 전환의 주요 기술 및 도구

| **항목**                                      | **설명**                                                                                   | **특징**                                                                                      |
|-----------------------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| **ABAP RESTful Application Programming Model (RAP)** | ABAP의 현대화된 개발 모델로, 클라우드 환경에서 SAP 시스템과 RESTful API를 통해 상호작용 지원.   | SAP S/4HANA Cloud와 같은 클라우드 솔루션에서 ABAP 활용 가능.                                  |
| **SAP BTP (Business Technology Platform)**    | SAP의 클라우드 플랫폼으로, 다양한 언어(Node.js, Java, Python, Go 등)를 지원하여 애플리케이션 개발 가능. | 클라우드 네이티브 개발, 데이터 관리, 통합, AI/ML 기능 제공.                                   |
| **SAP Cloud Application Programming Model (CAP)** | SAP BTP에서 클라우드 애플리케이션을 개발하기 위한 프레임워크. Node.js와 Java 지원.               | OData 서비스와 통합되어 데이터 모델링 및 API 개발 간소화.                                      |
| **SAP Graph API**                             | RESTful API를 통해 SAP 데이터를 다양한 언어(JavaScript, Python, Java 등)로 쉽게 활용 가능.        | SAP 데이터와 외부 시스템 간의 통합 지원.                                                     |
| **SAP Build Apps**                            | Low-Code/No-Code 플랫폼으로, 프로그래밍 언어 없이도 SAP 시스템과 통합된 애플리케이션 개발 가능.   | 비개발자가 사용 가능하며, SAP 시스템과 원활히 연결되는 애플리케이션 제작을 지원.              |
