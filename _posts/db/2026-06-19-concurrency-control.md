---
layout: single
title: "lock, mutex, latch"
date: 2025-06-19 14:00:00 +0900
categories: 
  - internal
tag: 
  - 동시성제어
  - 공유자원
  - concurrency
  - lock
  - mutex
  - latch
toc: true
toc_label: 목차
toc_sticky: true
---

<script type="module">
	import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
	mermaid.initialize({
		startOnLoad: true
	});
</script>

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

## 공유 자원 접근 제어는 왜 필요할까?

여러 스레드나 프로세스가 **동시에!!** 공유 자원에 접근하여 데이터를 읽거나 수정하려고 하면 여러 가지 문제들이 발생할 수 있습니다.

대표적인 것이 경쟁조건이라고 하는 **race condition** 입니다.

예를 들어 보겠습니다:
- 1,000원이 들어있는 은행 계좌가 있습니다.
- 철수는 저금통에 1,000원이 있는 걸 확인하고 100원을 인출하고, 잔액이 900원이 됐다고 기록합니다.
- 영희는 저금통에 1,000원이 있는 걸 확인하고 200원을 입금하고, 잔액이 1,200원이 됐다고 기록합니다.
- 철수와 영희는 서로의 존재를 모릅니다.

<table>
  <tr>
    <td>
      <pre class="mermaid">
      sequenceDiagram
          participant 철수
          participant 영희
          participant 은행

          Note over 은행,철수: 잔액: 1000원

          철수->>은행: 잔액 확인(1000원)
          영희->>은행: 잔액 확인(1000원)

          철수->>철수: 출금 (1000원 - 100원 = 900원)
          영희->>영희: 입금 (1000원 + 200원 = 1200원)

          철수->>은행: 철수의 잔액 기록 (900원)
          영희->>은행: 영희의 잔액 기록 (1200원)

          Note over 은행: 최종 잔액: 1200원 (금액 오류!)
          은행--x은행: 데이터 손상 / 업데이트 손실
      </pre>
    </td>
    <td>
      <pre class="mermaid">
      sequenceDiagram
          participant 철수
          participant 영희
          participant 은행

          Note over 은행,철수: 잔액: 1000원

          철수->>은행: 잔액 확인(1000원)
          영희->>은행: 잔액 확인(1000원)

          철수->>철수: 출금 (1000원 - 100원 = 900원)
          영희->>영희: 입금 (1000원 + 200원 = 1200원)

          영희->>은행: 영희의 잔액 기록 (1200원)
          철수->>은행: 철수의 잔액 기록 (900원)

          Note over 은행: 최종 잔액: 900원 (금액 오류!)
          은행--x은행: 데이터 손상 / 업데이트 손실
      </pre>
    </td>
  </tr>
</table>
