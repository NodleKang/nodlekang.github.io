---
layout: single
title: "[OpenTelemetry] 알아보기 2"
date: 2024-12-09 15:00:00 +0900
categories: 
  - 모니터링
tag:
  - otel
  - telemetry
  - OpenTelemetry
  - otlp
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 오픈 텔레메트리(OpenTelemetry)에 대해 학습한 내용을 정리한 포스트 입니다.

(OpenTelemetry란 무엇인가)[https://medium.com/@dudwls96/opentelemetry-란-무엇인가-18b6e4fe6e36]

문서를 거의 필사한 것이므로 원문을 참고하시는 것을 권장합니다.

# OpenTelemetry 란 무엇인가?

OpenTelemetry(약자 OTel)는 Traces, Metrics, Logs와 같은 데이터를 계측하고, 생성하고, 수집하고, 내보낼 수 있는 Observability 프레임워크입니다.

하지만 OpenTelemetry는 **수집한 데이터를 저장하거나 조회할 수 있는 방법을 제공하지 않고**, <br> 
상용 서비스(예를 들어 Datadog, New Relic 등) 혹은 오픈소스 서비스(예를 들어 Prometheus, Jaeger 등)로 **데이터를 전달**합니다.

OpenTelemetry는 데이터를 수집, 변환, 전송하기 위한 표준화된 SDK, API, OTel Collector를 제공하는 것을 목표로 합니다.

![OpenTelemetry Diagram](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*4AyWg2AtbbPXCGfadOW4Xg.png)

그림에서 보듯이 어플리케이션에서 생성된 데이터는 OTel Instrumentation(라이브러리), OTel API, OTel SDK를 통해 수집됩니다.

수집된 데이터는 OTLP 프로토콜을 통해 OTel Collector로 전달됩니다.

SDK는 데이터를 OTel Collector로 전달하고, OTel Collector는 데이터를 Exporter로 전달합니다.

# OpenTelemetry 아키텍처와 구성요소

![OpenTelemetry Architecture and Component](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*wrwirPr3xKSQ3UODXOxiHQ.png)

현재(2024.12.09) OpenTelemetry에서 개발 언어별 API, SDK에서 지원되는 데이터는 다음과 같습니다.

| 개발언어 | Traces | Metrics | Logs   |
|---|---|---|--------|
| C#/.NET | Stable | Stable | Stable |
| Go | Stable | Stable | Beta   |
| Java | Stable | Stable | Stable |
| JavaScript | Stable | Stable | Development |
| Python | Stable | Stable | Development |

최신의 전체 목록은 [여기](https://opentelemetry.io/docs/languages/)에서 확인할 수 있습니다.

현재(2024.12.09) 제로 코드 계측을 지원하는 언어는 .NET, Go, Java, JavaScript, Python 등이 있습니다. 

# OpenTelemetry Collector

OpenTelemetry Collector 는 원격측정데이터(Traces, Metrics, Logs)를 수신하고 처리한 후에 OpenTelemetry 백엔드로 데이터를 내보내는 역할을 합니다.

## OpenTelemetry Collector 구성요소

OpenTelemetry Collector 는 Receivers, Processor, Exporter로 구성되어 있습니다.

간단하게 `데이터를 받는 Receiver`, `데이터를 가공하는 Processor`, `데이터를 내보내는 Exporter`로 생각하면 됩니다.

* Receiver
  * gRPC 혹은 HTTP 프로토콜로 Trace, Metric, Log 데이터를 수신합니다.
  * 하나 이상의 Receivers를 구성할 수 있습니다.
  * [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver)에서 제공되는 다양한 Receiver 목록을 확인할 수 있습니다.
* Processor
  * 수신한 데이터를 가공하거나 필터링하는 역할을 합니다.
  * [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)에서 제공되는 다양한 Processor 목록을 확인할 수 있습니다.
* Exporter
  * Trace, Metric, Log 데이터를 하나 이상의 OpenTelemetry 백엔드로 내보내는 역할을 합니다.
  * 하나 이상의 Exporter를 구성할 수 있습니다. 
  * [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter)에서 제공되는 다양한 Exporter 목록을 확인할 수 있습니다.

## OpenTelemetry Collector 설정 파일 (예)

```yaml
#  OpenTelemetry Collector 설정
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  batch:

exporters:
  otlp:
    endpoint: otelcol:4317

extensions:
  health_check:
  pprof:
  zpages:

service:
  extensions: [health_check, pprof, zpages]
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
```

# OpenTelemetry Protocol (OTLP)

OTLP는 OpenTelemetry 프로젝트를 위해 설계된 범용 텔레메트리 데이터 전송 프로토콜입니다.

OTLP(OpenTelemetry Protocol)는 단순한 메시지 스펙 이상의 기능을 제공하기 때문에 프로토콜이라고 불립니다.

* OTLP(OpenTelemetry Protocol) 특징
  * Request/Response 스타일 프로토콜입니다.
  * 데이터 전송 메커니즘 정의: OTLP는 gRPC와 HTTP를 통한 텔레메트리 데이터의 전송 방식을 명시합니다.
  * 인코딩 방식 지정: 프로토콜은 Protobuf 바이너리 형식과 JSON 형식의 인코딩을 지원합니다.
  * 압축 옵션 정의: 모든 서버 컴포넌트는 none, gzip 압축 옵션을 지원해야 합니다.
  * 동시 요청 지원, 오류 처리 메커니즘 등 통신에 필요한 규칙을 포함합니다.

# 상용 서비스들의 OpenTelemetry 지원 현황

| 기능                      | Dynatrace | Datadog | New Relic | AppDynamics | Jeniffer     |
|-------------------------|---|---|---|---|--------------|
| OTLP 지원                 | O | O | O | O | O            |
| OTel Collector 필수 여부 | X | X | 선택적 | O | O            |
| 자체 에이전트/트레이서            | - | O | O | O | x            |
| 지원 프로토콜                 | OTLP | OTLP | OTLP(gRPC, HTTP/1.1) | OTLP/HTTP(s) | OTLP(HTTPs) |

* Dynatrace
  * OpenTelemetry 프로토콜(OTLP)을 지원합니다.
  * SaaS 엔드포인트를 통해 OTLP 내보내기를 직접 수행할 수 있어 별도의 컬렉터 설치가 필요 없습니다.
  * ActiveGate를 통한 내보내기 옵션도 제공합니다.

* Datadog
  * OpenTelemetry API를 통해 원격 측정 데이터를 생성하고 백엔드로 내보낼 수 있습니다.
  * Datadog에서 제공하는 OpenTelemetry 트레이서를 사용할 수 있습니다.
  * OpenTelemetry 컬렉터 없이도 Datadog 트레이서를 직접 사용할 수 있습니다.
 
* New Relic
  * OpenTelemetry SDK 또는 OpenTelemetry Collector를 선택적으로 사용할 수 있습니다.
  * gRPC HTTP/1.1을 통해 OpenTelemetry 프로토콜(OTLP) 트래픽을 지원합니다.
  * New Relic 에이전트와 OpenTelemetry 계측을 함께 사용할 수 있어 유연성이 높습니다

* AppDynamics
  * OpenTelemetry 프로토콜(OTLP)을 지원합니다.
  * OpenTelemetry Collector를 사용하여 데이터를 수집하고 AppDynamics 백엔드로 전송합니다.
  * Java, .NET, Node.js 에이전트에서 OpenTelemetry 지원을 제공합니다.
  * OTLP/HTTP(s)를 통해 OpenTelemetry 데이터를 수신합니다. 

* Jeniffer
  * OpenTelemetry 프로토콜(OTLP)을 지원합니다.
  * OpenTelemetry Collector를 사용하여 데이터를 수집하고 AppDynamics 백엔드로 전송합니다.
  * Java, .NET, Node.js 등 에이전트에서 OpenTelemetry 지원을 제공합니다.
  * HTTP(s)를 통해 OpenTelemetry 데이터를 수신합니다. 
