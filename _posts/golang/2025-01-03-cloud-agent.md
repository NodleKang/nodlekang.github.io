---
layout: single
title: "Cloud Agent 구조"
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

본 포스트는 Cloud SDK를 이용해서 Cloud Metrics 데이터를 수집해서 서버로 전송하는 agent 프로그램 구조를 설명한 것 입니다.

# 프로그램 흐름

<pre class="mermaid">
flowchart TD
flowchart TD
    App[app] --> Config[Configure]
    App --> APIserver[APIServer]
    App --> AgentInfo[AgentInfo]
    App --> RemoteHealthCheck[RemoteHealthCheck]

    Config -->|주기적으로 <br> conf 파일 읽기| Config1[설정 정보 변경]
    Config1 -->|변경 사항 반영| Config

    AgentInfo -->|주기적으로 <br> Agent 정보 보내기| CollectServer[수집 서버]

    APIserver --> Q{요청 수신?}

    Q -->|예| Clipper[Clipper 실행]
    Q -->|아니오| W[대기상태]
    
    Clipper -->|클라우드 메트릭 <br> 데이터 수집과 전달| UDPSender
    UDPSender -->|데이터 전송| CollectServer[수집 서버]

    RemoteHealthCheck -->|주기적으로 수집서버 상태 점검| CollectServer[수집 서버]
</pre>

<script type="module">
	import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
	mermaid.initialize({
		startOnLoad: true
	});
</script>

# 참고

[github pages에 mermaid 적용용](https://akuszyk.com/2023-05-03-yet-another-mermaid-in-github-pages-guide.html)
