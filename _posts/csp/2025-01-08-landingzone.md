---
layout: single
title: "Landing Zone"
date: 2025-01-08 10:30:00 +0900
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

클라우드 컴퓨팅 환경에서 **Landing Zone**은 클라우드 인프라를 설정하고 운영하기 위한 **기본적인 아키텍처와 환경**을 말합니다. 

이는 조직이 클라우드로 워크로드를 안전하고 효율적으로 배포할 수 있도록 미리 정의된 설정, 정책, 보안 규칙, 네트워크 구성 등을 포함하는 **출발점** 역할을 합니다.

# 주요 특징

1. **표준화된 환경 제공**: Landing Zone은 클라우드 리소스를 배포하기 전에 필요한 표준화된 설정을 제공합니다. 이를 통해 일관된 환경을 유지할 수 있습니다.
2. **보안 및 규정 준수**: 보안 정책, IAM(Identity and Access Management) 설정, 네트워크 방화벽 규칙 등을 미리 정의하여 보안과 규정 준수를 보장합니다.
3. **자동화된 설정**: 클라우드 환경을 빠르고 효율적으로 설정할 수 있도록 자동화된 스크립트나 템플릿을 제공합니다.
4. **확장 가능성**: 초기 설정 이후에도 조직의 요구에 따라 확장하거나 맞춤화할 수 있는 유연성을 제공합니다.

# 구성 요소
Landing Zone은 일반적으로 다음과 같은 요소들로 구성됩니다:
- **계정 구조**: 멀티 계정 전략(예: AWS Organizations, Azure Management Groups 등)을 통해 리소스를 분리하고 관리.
- **네트워크 설정**: VPC, 서브넷, 라우팅 테이블, VPN, Direct Connect 등 네트워크 아키텍처 구성.
- **보안 및 규정 준수**: IAM 역할 및 정책, 데이터 암호화, 로깅 및 모니터링 설정.
- **모니터링 및 로깅**: 클라우드 환경의 상태를 추적하기 위한 CloudWatch, CloudTrail, Azure Monitor 등의 도구 설정.
- **자동화 도구**: Terraform, AWS CloudFormation, Azure Resource Manager(ARM) 템플릿 등을 사용해 환경을 자동화.

# 사용 이유

Landing Zone은 클라우드 환경을 처음부터 체계적으로 설계하고 관리할 수 있도록 도와줍니다. 

Landing Zone 을 사용하면 다음과 같은 이점을 얻을 수 있습니다:
- 고객사 Private Network(내부망) 연결을 사용하여 Private 통신을 통한 통신 암호화가 가능합니다.
- Landing Zone 내에 CSP Account 에 관한 설정 및 정보를 확인하여 CSP Account 관리가 용이합니다.
- Landing Zone 에서 사용중인 보안장비(Appliance)를 사용해서 통합 보안 관리가 가능합니다.
- Landing Zone 안에서 서비스되는 Account들의 Log에 대해서 로그 저장소를 통합으로 운영합니다.

Landing Zone 이 없으면 다음과 같이 작업해야 합니다:
- Dev, QA, PRD 등으로 구분하여 각각 Cloud Account 사용하고, IAM Account에 대해서 개별 권한을 관리해야 합니다.
- 보안 관련 Appliance에 대해서 필요한 Cloud Account마다 보안 구성을 해야 합니다.
- Dev, QA, PRD 등 각 환경에서 각각 IAM 계정과 권한 관리를 수행해야 합니다.
- 내부망(Private Network) 연결을 위한 Direct Connect 혹은 VPN 연결 작업을 따로 해야 합니다.

# 예시
- **AWS Control Tower**: AWS에서 제공하는 Landing Zone 솔루션으로, 멀티 계정 환경을 쉽게 설정하고 관리할 수 있도록 지원합니다.
- **Azure Landing Zone**: Microsoft Azure에서 제공하는 모범 사례 기반의 클라우드 환경 설정 가이드.
- **Google Cloud Landing Zone**: Google Cloud에서 제공하는 초기 클라우드 환경 설정 템플릿.

결론적으로, Landing Zone은 클라우드 환경을 처음부터 올바르게 설정하고 관리하기 위한 **기본 토대**를 제공하며, 조직이 클라우드로의 전환을 원활하게 수행할 수 있도록 돕는 중요한 개념입니다.
