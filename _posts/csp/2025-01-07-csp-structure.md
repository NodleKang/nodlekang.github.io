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

본 포스트는 CSP(Cloud Service Providers) 자원 구조를 학습한 내용을 정리한 것입니다.

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
        Dep1 --> A1[운영]
        Dep1 --> B1[검수]
        Dep1 --> C1[개발]
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

# CSP 스토리지 선택

CSP에서 스토리지를 선택할 떄는 워크로드 요건에 따라 성능 최적화, 효율적인 비용을 고려해서 스터리지 타입과 옵션을 고려해야 합니다.

| 구분 | 요구사항 | CSP 스토리지 | 워크 로드 |
|:---:|:---:|:---:|:---|
| 블록 <br> 스토리지 | 고성능 <br> 범용성 | AWS EBS <br> Azure Disk <br> GCP Persistent Disk | 관계형 혹은 NoSQL 데이터베이스 <br> 데이터 웨어하우징, 엔터프라이즈 어플리케이션 <br> 로컬 백업 및 복구 |
| 파일 <br> 스토리지 | 파일공유 <br> 확장성 | AWS EFS / Fsx <br> Azure Files <br> GCP Cloud FileStore | 웹 지원 어플리케이션 <br> 콘텐츠 관리 시스템(CMS) <br> 빅 데이터 분석 <br> 컨테이너 스토리지 |
| 오브젝트 <br> 스토리지 | 가용성 <br> 저비용 <br> 확장성 | AWS S3 <br> Azure Blob Storage <br> GCP Cloud Storage | 데이터 레이크와 빅데이터 분석 <br> 아카이브 <br> 서버리스 컴퓨팅 <br> 백업 및 복구, 재해복구(DR) |

* 참고 - CSP 별 DISK 유형과 성능
  * AWS : https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-volume-types.html
  * Azure : https://learn.microsoft.com/ko-kr/azure/virtual-machines/disks-types
  * GCP : https://cloud.google.com/compute/docs/disks?hl=ko#disk-types
