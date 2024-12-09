---
layout: single
title: "[OpenTelemetry] 알아보기 2"
date: 2024-12-09 15:00:00 +0900
categories: 
  - 모니터링
tag: 
  - observability
  - telemetry
  - 텔레메트리
  - OpenTelemetry
  - 모니터링
  - log
  - metric
  - trace
  - span
  - baggage
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 오픈 텔레메트리(OpenTelemetry)에 대해 학습한 내용을 정리한 포스트 입니다.

(OpenTelemetry란 무엇인가)[https://medium.com/@dudwls96/opentelemetry-란-무엇인가-18b6e4fe6e36]

문서를 거의 필사한 것이므로 원문을 참고하시는 것을 권장합니다.

# OpenTelemetry 란 무엇인가?

![OpenTelemetry Diagram](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*4AyWg2AtbbPXCGfadOW4Xg.png)

OpenTelemetry(약자 OTel)는 Traces, Metrics, Logs와 같은 데이터를 계측하고, 생성하고, 수집하고, 내보낼 수 있는 Observability 프레임워크입니다.

하지만 OpenTelemetry는 **수집한 데이터를 저장하거나 조회할 수 있는 방법을 제공하지 않고**, <br> 
상용 서비스(예를 들어 Datadog, New Relic 등) 혹은 오픈소스 서비스(예를 들어 Prometheus, Jaeger 등)로 **데이터를 전달**합니다.

OpenTelemetry는 데이터를 수집, 변환, 전송하기 위한 표준화된 SDK, API, OTel Collector를 제공하는 것을 목표로 합니다.

# OpenTelemetry 아키텍처와 구성요소

![OpenTelemetry Architecture and Component](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*wrwirPr3xKSQ3UODXOxiHQ.png)

OpenTelemetry는 개발 언어별 SDK, OTel Collector, OTel Collector Exporter 등으로 구성되어 있습니다.

현재(2024.12.09) OpenTelemetry에서 개발 언어별 API, SDK에서 지원되는 데이터는 다음과 같습니다.

| 개발언어 | Traces | Metrics | Logs   |
|---|---|---|--------|
| C#/.NET | Stable | Stable | Stable |
| Go | Stable | Stable | Beta   |
| Java | Stable | Stable | Stable |
| JavaScript | Stable | Stable | Development |
| Python | Stable | Stable | Development |

최신의 전체 목록은 [여기](https://opentelemetry.io/docs/languages/)에서 확인할 수 있습니다.

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
