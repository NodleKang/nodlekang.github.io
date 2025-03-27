---
layout: single
title: "Landing Zone"
date: 2025-03-27 10:30:00 +0900
categories: 
  - CSP
tag: 
  - Landing Zone
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 클라우드 컴퓨팅 환경에서 Landing Zone 을 gpt4o 모델에게 물어봐서 학습한 내용을 정리한 것입니다.

# 개요

"Landing Zone"은 **회사가 클라우드를 안정적이고 안전하게 사용하기 위해 미리 준비한 기본 환경**이라고 생각하면 됩니다.

클라우드 환경을 제대로 사용하려면 **다양한 세팅(네트워크, 보안, 권한 관리 등)을 미리 해두는 것**이 중요합니다.

"Landing Zone"은 이런 세팅을 하나로 묶어서 **클라우드를 위한 "준비된 기초 환경"을 바로 이용**할 수 있게 만들어 주는 것입니다.

# Landing Zone 구성요소

- **클라우드 계정 구조**
  - 멀티 계정 전략(예: AWS Organizations, Azure Management Groups 등)을 통해 리소스를 분리하고 관리합니다.
- **네트워크 설정**
  - 회사의 인터넷이나 내부 네트워크와 클라우드 환경이 안전하게 연결되도록 합니다.
  - VPC, 서브넷, 라우팅 테이블, VPN, Direct Connect 등으로 네트워크 아키텍처를 구성합니다.
  - 예를 들어:
    - 데이터가 회사 내부망과 클라우드 간에 암호화되어 전송되게 합니다.
    - 개발 환경과 운영 환경 간에 네트워크를 분리해서 서로 간섭하지 않습니다.
- **보안 및 권한 관리**
  - 누가 클라우드 리소스에 접근할 수 있는지, 어떤 작업을 할 수 있는 정합니다.
  - IAM 역할 및 정책을 정합니다.
- **로깅 및 모니터링** 
  - 로깅 및 모니터링을 설정합니다.
- **컴플라이언스 지원**
  - 데이터 보호법(GDPR 같은)이나 보안 규정이 필요하다면, 이에 따라 클라우드 환경을 미리 설정해 둡니다.
  - 예: 모든 데이터를 암호화 해서 저장.
- **자동화된 환경 구축**
  - 코드를 활용해서 앞에서 설명한 내용들을 자동으로 구축합니다.
  - Terraform, AWS CloudFormation, Azure Resource Manager(ARM) 템플릿 등을 사용합니다.

# Landing Zone을 비유로 이해하기

만약 클라우드를 하나의 여러 나라로 떠나는 공항으로 생각하면, Landing Zone은 **각 나라로 떠나는 출국장**입니다.:
- 공항 출국장에서, 여권을 검사(보안)하고, 비행기 표를 확인(권한 관리)하고, 짐 검사(데이터 검증)를 하며 출발 준비를 마칩니다.
- 목적지별로 특정 항공편(개발환경, 운영환경)으로 이동할 수 있습니다.

클라우드에서 Landing Zone은 클라우드 사용을 위한 사전 준비 공간입니다. 각각의 서버(비행기)가 준비되기 위해 필요한 모든 설정(보안, 네트워크, 로그 등)이 여기서 자동으로 수행됩니다.

# Landing Zone 장점

Landing Zone 을 사용하면 다음과 같은 이점을 얻을 수 있습니다:
- 고객사 Private Network(내부망) 연결을 사용하여 Private 통신을 통한 통신 암호화가 가능합니다.
- Landing Zone 내에 CSP Account 에 관한 설정 및 정보를 확인하여 CSP Account 관리가 용이합니다.
- Landing Zone 에서 사용중인 보안장비(Appliance)를 사용해서 통합 보안 관리가 가능합니다.
- Landing Zone 안에서 서비스되는 Account들의 Log에 대해서 로그 저장소를 통합으로 운영합니다.

# Landing Zone 없을 때 단점

Landing Zone 이 없으면 다음과 같이 작업해야 합니다:
- Dev, QA, PRD 등으로 구분하여 각각 Cloud Account 사용하고, IAM Account에 대해서 개별 권한을 관리해야 합니다.
- 보안 관련 Appliance에 대해서 필요한 Cloud Account마다 보안 구성을 해야 합니다.
- Dev, QA, PRD 등 각 환경에서 각각 IAM 계정과 권한 관리를 수행해야 합니다.
- 내부망(Private Network) 연결을 위한 Direct Connect 혹은 VPN 연결 작업을 따로 해야 합니다.

# 예시: AWS Landing Zone
- AWS에서는 Control Tower라는 기능을 통해 Landing Zone을 구축합니다.
- Control Tower는 계정 구조, IAM 정책(권한), 감사 및 로깅(AWS CloudTrail), 네트워크 설정 등을 자동으로 구축합니다.

# 결론

Landing Zone은 클라우드 사용자가 모든 것을 직접 설정하지 않고, 미리 설계된 기본 틀 속에서 작업을 시작할 수 있도록 도와줍니다.

특히 대규모로 확장 가능한 환경을 위한 핵심 기반이 됩니다. (기본기가 탄탄한 집을 짓기 위한 토대)
