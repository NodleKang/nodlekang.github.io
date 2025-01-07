---
layout: single
title: "[CSP] 클라우드 자원 구조"
date: 2025-01-07 14:00:00 +0900
categories: 
  - CSP
tag: 
  - aws
  - azure
  - gcp
  - resource
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 CSP 자원 구조를 학습한 내용을 정리한 것입니다.

# CSP 자원 구조

대표적인 CSP는 다음과 같은 자원 구조를 가집니다.

|                    구분                     |           AWS            |      Azure       |     GCP      |
|:-----------------------------------------:|:------------------------:|:----------------:|:------------:|
|                 최상위 Root                  |       Organization       |      Tanant      | Organization |
|   클라우드 정책을 정의하는 논리 그룹 <br> (부서 또는 서비스)    | Organizational Units(OU) | Management Group |    Folder    |
| 실제 자원이 구성되는 단위 <br> (환경별 분리 혹은 프로젝트별 분리) | Account | Subscription | Project |
|             실제 자원 혹은 클라우드 서비스             | Resource Group & Service | Resource Group & Service | Resource Group & Service |

<pre class="mermaid">
flowchart TD
    subgraph Root["최상위Root"]
        direction TB
        MyCompany[MyCompany]
    end

    subgraph LogicalGroup["클라우드 정책을 정의하는 논리그룹"]
        direction TB
        MyCompany[MyCompany]
        CommonService[공통서비스]
        Dep1[부서1]
        Dep2[부서N]
    end

    MyCompany --> CommonService
    MyCompany --> Dep1
    MyCompany --> Dep2

    subgraph GroupPerEnv["실제 자원이 구성되는 단위"]
        direction TB
        CommonService --> CS1[보안]
        CommonService --> CS2[관리]
        CommonService --> CS3[감사]

        direction TB
        Dep1 --> A1[보안]
        Dep1 --> B1[관리]
        Dep1 --> C1[감사]
    end

    subgraph RealResourceGroup["실제 자원 또는 클라우드 서비스"]
        direction TB
        CS1 --> CS11[자원]
        CS2 --> CS21[자원]
        CS3 --> CS31[자원]

        A1 --> A11[자원]
        B1 --> B11[자원]
        C1 --> C11[자원]
    end
</pre>

<script type="module">
	import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
	mermaid.initialize({
		startOnLoad: true
	});
</script>

# CSP 자원 구조 예

앞에서 확인한 CSP 자원 구조를 바탕으로 간단한 시나리오를 바탕으로 프로젝트를 할 때 클라우드 자원 구조가 어떻게 되는지 알아 보겠습니다.

이 내용은 이해를 돕기 위한 예제일 뿐입니다.

CSP 자원 구조는 절대적인 것은 아니며, 실제 환경에 따라 적절히 조절해서 구성합니다.

* 시나리오
  * 나의 회사는 AWS 환경을 사용합니다. (회사 이름: MyCompany)
  * 나의 회사에 있는 솔루션 사업부에서 새로운 프로젝트를 하려고 합니다. (솔루션 사업부 이름: SolutionDepartment)
  * 새로운 프로젝트는 어플리케이션 성능을 모니터링하는 프로젝트로입니다. (프로젝트 이름: NewAPM)
  * 프로젝트에 사용할 자원은 단독으로 사용할 수 있도록 분리된 영역에 구성해야 합니다.

* 시나리오 기반 클라우드 자원 구조
  * CSP로 AWS를 사용하므로 최상위 Root는 'MyCompany'라는 **Organization**입니다.
  * 클라우드 정책을 정의하는 논리 그룹은 'SolutionDepartment'라는 **Organizational Units(OU)** 입니다.
  * 실제 자원이 구성되는 단위는 프로젝트와 같은 'NewAPM'라는 **Account** 입니다.
  * 'NewAPM' **Account** 하위에 실제 사용할 자원이나 클라우드 서비스를 구성합니다.
