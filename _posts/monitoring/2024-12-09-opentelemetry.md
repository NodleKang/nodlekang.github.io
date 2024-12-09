---
layout: single
title: "OpenTelemetry"
date: 2024-12-09 14:00:00 +0900
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
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 오픈 텔레메트리(OpenTelemetry)에 대해 학습한 내용을 정리한 첫 번째 포스트 입니다.

# OpenTelemetry 등장 배경

OpenTelemetry는 분산 시스템에서의 로그, 메트릭, 트레이스를 표준화하기 위해 2019년 5월에 OpenTracing과 OpenCensus를 합쳐서 만들어진 프로젝트입니다.

OpenTracing은 분산 시스템에서의 트레이스를 표준화하기 위해 CNCF가 채택한 프로젝트였고,

OpenCensus는 분산 시스템에서의 로그, 메트릭, 트레이스를 표준화하기 위해 Google이 시작한 프로젝트였습니다.

OpenTelemetry는 Microservices, Kubernetes, Istio 등의 환경에서의 텔레메트리 데이터를 수집하고, 전송하고, 저장하기 위한 표준화된 방법을 제공합니다. 

# OpenTelemetry 목표

OpenTelemetry의 목표는 분산 시스템에서의 로그, 메트릭, 트레이스를 표준화하고, 이를 통해 시스템의 성능을 모니터링하고, 문제를 해결하는데 도움을 주는 것입니다.

OpenTelemetry는 로그, 메트릭, 트레이스를 표준화하기 위한 API와 SDK를 제공하며, 이를 통해 다양한 언어와 프레임워크에서 텔레메트리 데이터를 수집하고, 전송하고, 저장하는 것을 지원합니다.

# OpenTelemetry 수집 데이터 형식

OpenTelemetry 에서는 수집하는 데이터들을 Signals 라고 지칭하며, 다음과 같은 형식으로 정의합니다.

- 로그(Log): 시스템에서 발생하는 이벤트를 기록한 데이터
- 메트릭(Metric): 시간에 따른 수치 데이터
- 트레이스(Trace): 분산 시스템에서의 요청을 추적한 데이터
- 배기지(Baggage): 컨텍스트 전파를 위한 키-값 쌍
- 컨텍스트(Context): 분산 시스템에서의 요청을 추적하기 위한 데이터
- 이벤트(Event): 시스템에서 발생하는 이벤트를 기록한 데이터
- 에러(Error): 시스템에서 발생하는 오류를 기록한 데이터
- 지연(Latency): 시스템에서 발생하는 지연을 기록한 데이터
- 요청(Request): 시스템에서 발생하는 요청을 기록한 데이터
- 응답(Response): 시스템에서 발생하는 응답을 기록한 데이터
- 상태(State): 시스템에서 발생하는 상태를 기록한 데이터

## 컨텍스트(Context)

컨텍스트는 분산 시스템에서의 요청을 추적하기 위한 데이터로, 사용자 ID, 세션 ID, 트랜잭션 ID 등을 포함합니다.

컨텍스트는 여러 서비스 간의 의존성을 추적하고, 서비스 간의 호출 관계, 지연 시간, 오류 등을 확인하는데 사용됩니다.

## 배기지(Baggage)

배기지는 컨텍스트 전파를 위해 사용되는 키-값 쌍으로, 분산 시스템에서 요청을 추적하기 위한 데이터를 전파하기 위해 사용됩니다.

예를 들어, 서비스와 서비스 간의 호출 관계를 추적하기 위해 사용자 ID, 세션 ID, 트랜잭션 ID 등을 배기지로 전파할 수 있습니다.

![Baggage](https://opentelemetry.io/img/otel-baggage.svg){: width="100"}

# 컨텍스트 전파(Context Propagation)

컨텍스트 전파는 분산 시스템에서의 요청을 추적하기 위해 컨텍스트를 전파하는 것을 의미합니다.

컨텍스트 전파는 여러 서비스 간의 의존성을 추적하고, 서비스 간의 호출 관계, 지연 시간, 오류 등을 확인하는데 사용됩니다.

예를 들어, HTTP 요청을 받은 서비스는 HTTP 헤더에 컨텍스트 정보를 포함하여 다음 서비스에 전달할 수 있습니다.

HTTP 요청을 받은 서비스는 HTTP 헤더에서 컨텍스트 정보를 추출하여 로그, 메트릭, 트레이스를 수집하고, 다음 서비스에 전달할 수 있습니다.

HTTP 헤더에는 사용자 ID, 세션 ID, 트랜잭션 ID 등의 컨텍스트 정보나 배기지 정보를 포함할 수 있습니다.

# OpenTelemetry 구성 요소

OpenTelemetry는 다음과 같은 구성 요소로 이루어져 있습니다.

- OpenTelemetry API: 로그, 메트릭, 트레이스를 수집하기 위한 API
- OpenTelemetry SDK: 로그, 메트릭, 트레이스를 수집하기 위한 SDK
- OpenTelemetry Collector: 로그, 메트릭, 트레이스 데이터를 수집하고, 전송하고, 저장하는 컴포넌트
- OpenTelemetry Exporter: 로그, 메트릭, 트레이스 데이터를 특정 저장소로 전송하는 컴포넌트
- OpenTelemetry Instrumentation: 로그, 메트릭, 트레이스를 수집하기 위한 라이브러리
- OpenTelemetry Protocol: 로그, 메트릭, 트레이스 데이터를 전송하기 위한 프로토콜
- OpenTelemetry Semantic Conventions: 로그, 메트릭, 트레이스 데이터를 표준화하기 위한 규칙
- OpenTelemetry Specification: 로그, 메트릭, 트레이스 데이터를 표준화하기 위한 명세

# OpenTelemetry 계측 (Instrumentation)

OpenTelemetry 계측은 로그, 메트릭, 트레이스를 수집하기 위한 라이브러리를 사용하는 것을 의미합니다.

시스템을 관할가능(observability)하게 만들기 위해 시스템을 구성하는 오소들이 로그, 메트릭, 트레이스를 수집하고, 전송하고, 저장하게 할 수 있습니다.

OpenTelemetry는 자동 계측과 수동 계측을 지원합니다.

## 자동 계측 (Auto-Instrumentation)

프레임워크나 라이브러리를 사용하여 자동으로 로그, 메트릭, 트레이스를 수집하는 것을 의미합니다.

예를 들어, Spring Boot, Django, Express, FastAPI, gRPC 등의 프레임워크나 라이브러리를 사용하여 자동으로 로그, 메트릭, 트레이스를 수집할 수 있습니다.

## 수동 계측 (Manual-Instrumentation)

코드를 직접 수정하여 로그, 메트릭, 트레이스를 수집하는 것을 의미합니다.

예를 들어, <br>
로그를 수집하기 위해 `log.info()`, `log.error()`, `log.debug()` 등의 코드를 추가하거나, <br> 
메트릭을 수집하기 위해 `meter.counter()`, `meter.gauge()`, `meter.histogram()` 등의 코드를 추가하거나, <br> 
트레이스를 수집하기 위해 `tracer.startSpan()`, `tracer.endSpan()`, `tracer.withSpan()` 등의 코드를 추가할 수 있습니다.

## 계측 범위 (Instrumentation Scope)

계측 범위는 로그, 메트릭, 트레이스를 수집하는 범위를 의미합니다.

계측 범위는 전체 시스템, 특정 서비스, 특정 함수, 특정 코드 블록 등으로 나눌 수 있습니다.

예를 들어, 전체 시스템에서 로그, 메트릭, 트레이스를 수집하거나, 특정 서비스에서 로그, 메트릭, 트레이스를 수집하거나, 특정 함수에서 로그, 메트릭, 트레이스를 수집할 수 있습니다.

# OpenTelemetry 의미체계 (Semantic Conventions)

OpenTelemetry 의미체계는 로그, 메트릭, 트레이스 데이터를 표준화하기 위한 규칙을 의미합니다.

데이터 표기에 일관성을 부여하고, 데이터를 쉽게 이해하고, 데이터를 쉽게 분석할 수 있도록 도와줍니다.

예를 들어, 로그 데이터의 필드 이름, 메트릭 데이터의 단위, 트레이스 데이터의 태그 등을 표준화하여 데이터를 일관성 있게 표기할 수 있습니다.

마치 일반적인 프로젝트에서 PMO가 정한 표준화된 양식을 사용하는 것과 같은 원리입니다.

# OpenTelemetry 명세 (Specification)

OpenTelemetry의 전반적인 명세를 의미합니다. 

이는 로그, 메트릭, 트레이스 데이터를 표준화하기 위한 전체적인 명세를 포함할 뿐 아니라, 

API, SDK, 프로토콜, 데이터 형식 등 OpenTelemetry의 모든 구성 요소에 대한 표준을 정의합니다.

# OpenTelemetry 구현체 (Implementation)

OpenTelemetry의 구현체는 다양한 언어와 프레임워크에서 OpenTelemetry를 사용할 수 있도록 지원하는 라이브러리를 의미합니다.

예를 들어, Java, Python, Go, Node.js, .Net 등의 언어와 Spring Boot, Django, Express, FastAPI, gRPC 등의 프레임워크에서 OpenTelemetry를 사용할 수 있습니다.
