---
layout: single
title: "Cloud Agent 어플리케이션 구조와 데이터 수집 흐름"
date: 2025-01-03 14:00:00 +0900
categories: 
  - Go
tag: 
  - Go
  - gopath
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 Cloud SDK를 이용해서 Cloud Metrics 데이터를 수집해서 서버로 전송하는 Agent 프로그램 구조를 설명한 것 입니다.

# 프로그램 흐름

<pre class="mermaid">
flowchart TD
    App[app] --> Config[Configure]
    App --> APIserver[APIServer]
    App --> AgentInfo[AgentInfo]
    App --> RemoteHealthCheck[RemoteHealthCheck]

    Config -->|주기적으로 <br> conf 파일 읽기| Config1[설정 정보 변경]
    Config1 -->|변경 사항 반영| Config

    AgentInfo -->|주기적으로 <br> Agent 정보 보내기| CollectServer[수집 서버]

    APIserver --> Q{Cloud <br> Configuration <br> 수신?}

    Q -->|예| Clippers[Clipper들 실행]
    Q -->|아니오| W[대기상태]
    
    Clippers -->|메트릭과 리소스 <br> 데이터 수집과 전달| UDPSender
    UDPSender -->|데이터 전송| CollectServer[수집 서버]

    RemoteHealthCheck -->|주기적으로 <br>수집서버 상태 점검| CollectServer[수집 서버]
</pre>

<script type="module">
	import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
	mermaid.initialize({
		startOnLoad: true
	});
</script>

# 데이터 수집 시퀀스

이 프로그램에서 가장 중요한 부분은 Cloud Configuration에 맞춰서 Metrics와 Resource 정보를 수집하는 것 입니다.

<pre class="mermaid">
sequenceDiagram
    autonumber
    participant C as StartClippersWithCloudConfiguration
    participant Server as 수집서버
    participant M as StartClippers
    participant CW as CloudWatchClipper
    participant R as ResourceClipper
    participant U as UDPSender

    C->>Server: 구성 정보 요청
    Server-->>C: 구성 정보 반환
    C->>M: StartClippers(구성 정보)
    
    alt 기존 Clipper가 실행 중인 경우
        M->>M: 기존 Clipper 정지
    end

    M->>CW: CloudWatchClipper 생성
    M->>R: ResourceClipper 생성

    CW->>CW: Clip() (주기적으로 메트릭 데이터 수집)
    R->>R: Clip() (주기적으로 메트릭 데이터 수집)

    CW->>U: 메트릭 데이터 전송 (pack 형식)
    R->>U: 메트릭 데이터 전송 (pack 형식)

    CW->>R: 메트릭 정보 전송 (채널 사용)
    R->>CW: 리소스 정보 전송 (채널 사용)
</pre>

# 참고

[github pages에 mermaid 적용하기](https://akuszyk.com/2023-05-03-yet-another-mermaid-in-github-pages-guide.html)
