---
layout: single
title: "[OpenTelemetry] 알아보기"
date: 2024-12-10 10:00:00 +0900
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

# 소개

OpenTelemetry(줄여서 OTel)는 2019년 5월에 OpenTracing과 OpenCensus 프로젝트를 통합하여 만들어진 오픈소스 관찰가능성(observability) 프레임워크입니다. 

OpenTelemetry는 수집한 데이터를 직접 저장하거나 조회할 수 있는 방법을 제공하지 않고, <br> 
상용 서비스(예를 들어 Datadog, New Relic 등) 혹은 오픈소스 서비스(예를 들어 Prometheus, Jaeger 등)로 데이터를 전달합니다.

# 목표

1. **표준화**: 분산 시스템에서의 원격측정데이터(Traces, Metrics, Logs)를 표준화합니다.
2. **통합**: 다양한 언어와 프레임워크에서 사용할 수 있도록 지원합니다.
3. **확장성**: 다양한 환경에서 사용할 수 있도록 지원합니다.

# 아키텍처와 주요 구성요소

![OpenTelemetry](https://opentelemetry.io/img/otel-diagram.svg)

1. **OpenTelemetry Instrumentation**: 다양한 프레임워크와 라이브러리에서 사용할 수 있는 계측(Instrumentation) 라이브러리
2. **OpenTelemetry API**: 로그, 메트릭, 트레이스를 수집하기 위한 API
3. **OpenTelemetry SDK**: API의 구현체로, 데이터 처리와 내보내기 기능 제공
4. **OpenTelemetry Collector**: 원격측정데이터(Traces, Metrics, Logs)를 수집, 처리, 내보내는 중앙 집중식 컴포넌트
5. **OpenTelemetry Exporter**: 다양한 백엔드 시스템으로 데이터를 내보내는 컴포넌트

# 데이터 형식 (Signals)

OpenTelemetry에서는 수집하는 데이터들을 `Signals` 라고 지칭하며, 다음과 같은 주요 데이터 형식이 있습니다.

1. **로그(Log)**: 시스템에서 발생하는 이벤트를 기록한 데이터
2. **메트릭(Metric)**: 시간에 따른 수치 데이터
3. **트레이스(Trace)**: 분산 시스템에서의 요청을 추적한 데이터
4. **스팬(Span)**: 트레이스 데이터의 단위로, 트레이스를 구성하는 요소. 작업의 시작과 끝을 나타냄.
5. **컨텍스트(Context)**: 분산 시스템에서의 요청을 추적하기 위한 데이터
6. **배기지(Baggage)**: 컨텍스트 전파를 위한 키-값 쌍

# 계측 (Instrumentation)

OpenTelemetry는 두 가지 주요 계측 방식을 지원합니다.

## 제로 코드 계측 (Zero-code Instrumentation)

통상 자동 계측(Auto-Instrumentation)이라고 합니다.

코드를 수정하지 않고 프레임워크나 라이브러리를 사용하여 자동으로 원격측정데이터(Traces, Metrics, Logs)를 수집합니다.

주로 에이전트(Agent) 혹은 바이트코드 조작(Bytecode Manipulation)을 사용을 통해 구현됩니다.

현재(2024.12.09) 제로 코드 계측을 지원하는 언어는 .NET, Go, Java, JavaScript, Python 등이 있습니다. 

## 코드 기반 계측 (Code-based Instrumentation)

통상 수동 계측(Manual-Instrumentation)이라고 합니다.

코드를 직접 수정하여 원격측정데이터(Traces, Metrics, Logs)를 수집합니다.

예를 들어, <br>
로그를 수집하기 위해 `log.info()`, `log.error()`, `log.debug()` 등의 코드를 추가하거나, <br> 
메트릭을 수집하기 위해 `meter.counter()`, `meter.gauge()`, `meter.histogram()` 등의 코드를 추가하거나, <br> 
트레이스를 수집하기 위해 `tracer.startSpan()`, `tracer.endSpan()`, `tracer.withSpan()` 등의 코드를 추가할 수 있습니다.

## 계측 범위 (Instrumentation Scope)

계측 범위는 원격측정데이터(Traces, Metrics, Logs)를 수집하는 범위를 의미합니다.

계측 범위는 전체 시스템, 특정 서비스, 특정 함수, 특정 코드 블록 등으로 나눌 수 있습니다.

예를 들어, 전체 시스템, 특정 서비스, 특정 함수를 대상으로 원격측정데이터(Traces, Metrics, Logs)를 수집할 수 있습니다.

# OpenTelemetry Collector

OpenTelemetry Collector는 원격측정데이터(Traces, Metrics, Logs)를 수신하고 처리한 후에 OpenTelemetry 백엔드로 데이터를 내보내는 역할을 합니다.

![OpenTelemetry Collector](https://imagedelivery.net/xZXo0QFi-1_4Zimer-T0XQ/6e07fd96-5d60-4b3b-bd7b-efae0f38fe00/lg2x)

OpenTelemetry Collector 는 Receivers, Processor, Exporter로 구성되어 있습니다.

간단하게 `데이터를 받는 Receiver`, `데이터를 가공하는 Processor`, `데이터를 내보내는 Exporter`로 생각하면 됩니다.

## Receiver
* 다양한 프로토콜(gRPC, HTTP)과 형식으로 데이터를 수신합니다.
* 하나 이상의 Receivers를 구성할 수 있습니다.
* OTLP(OpenTelemetry Protocol)가 OpenTelemetry의 기본 수신 프로토콜이지만, 그 외에도 다양한 프로토콜과 형식을 지원합니다.
* 예를 들어, Prometheus, Kafka, AWS CloudWatch, Google Cloud Pub/Sub, Datadog 등을 지원합니다.
* [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver)에서 제공되는 다양한 Receiver 목록을 확인할 수 있습니다.

## Processor
* 수신한 데이터를 가공, 변환, 필터링하는 역할을 합니다. 
* [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)에서 제공되는 다양한 Processor 목록을 확인할 수 있습니다.

## Exporter
* 처리된 데이터를 하나 이상의 OpenTelemetry 백엔드 시스템으로 내보내는 역할을 합니다.
* 하나 이상의 Exporter를 구성할 수 있습니다. 
* [여기](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter)에서 제공되는 다양한 Exporter 목록을 확인할 수 있습니다.

# OpenTelemetry Protocol (OTLP)

OTLP는 OpenTelemetry 프로젝트를 위해 특별히 설계된 원격측정데이터(Traces, Metrics, Logs) 전송 프로토콜입니다.

주요 특징은 다음과 같습니다.
* Request/Ressponse 형식의 프로토콜
* gRPC와 HTTP를 통한 데이터 전송 지원
* Protobuf 바이너리 형식과 JSON 형식의 인코딩 지원
* 압축 옵션(none, gzip) 지원
* 동시 요청 처리 및 오류 처리 매커니즘 지원

# 지원 언어

현재(2024.12.09) OpenTelemetry에서 지원하는 언어는 다음과 같으며, 제로 코드 계측을 지원하는 언어는 .NET, Go, Java, JavaScript, Python 등이 있습니다.

| 개발언어 | Traces | Metrics | Logs   |
|---|---|---|--------|
| C#/.NET | Stable | Stable | Stable |
| Go | Stable | Stable | Beta   |
| Java | Stable | Stable | Stable |
| JavaScript | Stable | Stable | Development |
| Python | Stable | Stable | Development |

최신의 전체 목록은 [여기](https://opentelemetry.io/docs/languages/)에서 확인할 수 있습니다.

# 참고 자료

* [OpenTelemetry 공식 홈페이지](https://opentelemetry.io/)
* [OpenTelemetry GitHub](https://github.com/open-telemetry)
* [OpenTelemetry Collector Receivers](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver)
* [OpenTelemetry Collector Processors](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)
* [OpenTelemetry Collector Exporters](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter)
* [OpenTelemetry Protocol (OTLP)](https://github.com/open-telemetry/opentelemetry-proto/tree/main/docs)
