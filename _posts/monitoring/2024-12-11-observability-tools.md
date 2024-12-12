---
layout: single
title: "Observability 도구"
date: 2024-12-10 09:30:00 +0900
categories: 
  - 모니터링
tag: 
  - Observability
  - 분산추적
  - Prometheus
  - Jaeger
  - Zipkin
  - OpenTelemetry
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 오픈소스 Observability 도구들(Prometheus, Jaeger, Zipkin, OpenTelemetry)에 대해 학습한 내용을 정리한 것입니다. 

Observability 도구에 대해 알아보기 전에 분산 추적(Distributed Tracing) 개념을 먼저 알아보겠습니다.

# 분산 추적 (Distributed Tracing)

최근 구축되는 대부분의 시스템은 분산 시스템으로 구성되는데, 이러한 시스템은 각 서비스 간의 의존성이 복잡하게 얽혀 있습니다.

이런 분산된 시스템에 위치한 서비스(마이크로서비스) 간의 요청과 응답을 추적하고, 각 서비스의 성능을 모니터링하는 데 사용되는 기술이 분산 추적(Distributed Tracing)입니다.

요즘처럼 쿠버네티스에 기반한 분산 시스템에서 서비스 간의 의존성이 복잡하게 얽혀 있는 경우, 분산 추적은 필수적인 기술이라고 할 수 있습니다.

분산 추적은 2010년 초반에 Google에서 발표한 Dapper 논문에서 처음 소개되었습니다.

# 오픈소스 Observability 도구

## 종류와 특징

Observability에 사용할 수 있는 도구 중에 대표적인 것으로 Prometheus, Jaeger, Zipkin, OpenTelemetry 등이 있습니다. 

* Prometheus
  * 주요 기능: Metrics 수집과 모니터링
  * 특징:
    * Pull 방식으로 Metrics 수집
    * 시계열 데이터베이스를 사용해서 Metrics 저장
    * 강력한 쿼리 언어(PromQL) 제공
    * Exporter를 통해 다양한 서비스와 연동 가능

* Jaeger
  * 주요 기능: 분산 추적
  * 특징:
    * Uber에서 개발한 오픈소스 도구로 CNCF 프로젝트로 인정받음 
    * OpenTracing 표준과 완벽하게 호환
    * 쿠버네티스 환경에서의 사용에 최적화
    * 마이크로서비스 환경에서의 Request 흐름 추적에 최적화되어 있음
    * 확장성이 뛰어남

* Zipkin
  * 주요 기능: 분산 추적
  * 특징:
    * Twitter에서 개발한 오픈소스 도구
    * 분산 시스템에서의 지연 시간 문제 해결에 중점을 둠
    * 다양한 언어와 프레임워크를 지원
    * 상대적으로 간단한 설정과 사용

* OpenTelemetry
  * 주요 기능: 분산 시스템에서 원격측정데이터(Traces, Metrics, Logs)의 수집과 전달
  * 특징:
    * 데이터 수집과 전달에만 중점을 둠
    * 데이터를 저장하고 조회하는 역할은 다른 서비스(Prometheus, Jaeger, Dynatrace, New Relic 등)에게 맡김
    * 표준화, 통합, 확장성을 목표로 함
    * OpenTelemetry Collector, OpenTelemetry Exporter 등의 주요 구성요소를 제공

## 도구 선택 기준

각 Observability 도구는 목적이나 사용 환경에 따라 단독으로 사용할 수도 있고, 다른 도구와 함께 사용할 수도 있습니다.

시스템을 구성하고 있는 서비스가 다양한 언어와 프레임워크로 개발되어 있지만, 통합된 모니터링을 원할 때는 OpenTelemetry 와 함께 백엔드가 될 수 있는 Prometheus, Jaeger, Zipkin 등을 함께 사용하는 것이 좋습니다.

* Prometheus
  * Metrics 수집과 모니터링에 중점을 둘 때
  * Pull 방식으로 Metrics 수집이 필요할 때
  * 강력한 쿼리 언어(PromQL)를 사용하고 싶을 때

* Jaeger
  * 분산 시스템에서의 Request 흐름 추적에 중점을 둘 때
  * OpenTracing 표준을 준수하는 서비스와 연동할 때
  * 쿠버네티스 환경에서의 사용을 고려할 때
  * OpenTelemetry 호환성을 고려할 때

* Zipkin
  * 분산 시스템에서의 지연 시간 문제 해결에 중점을 둘 때
  * 간단한 설정과 사용을 원할 때
  * 다양한 언어와 프레임워크를 지원하는 도구를 찾을 때

* OpenTelemetry
  * 시스템을 구성하고 있는 서비스가 다양한 언어와 프레임워크로 개발되어 있지만, 통합된 모니터링을 원할 때 
  * 분산 시스템에서의 원격측정데이터(Traces, Metrics, Logs)의 수집과 전달에 중점을 둘 때
  * 데이터를 저장하고 조회하는 역할은 다른 서비스에게 맡기고 싶을 때
  * 표준화, 통합, 확장성을 고려할 때

# Observability 도구 설치와 구성

## Prometheus

Prometheus는 Metrics 수집과 모니터링을 위한 오픈소스 도구입니다. (logs, traces는 지원하지 않음)

Prometheus는 UI가 제공되긴 하지만 UI가 다소 미흡하고, 대시보드를 직접 구성해야 하는 불편함때문에 Grafana와 함께 사용하는 것이 일반적입니다.

일반적으로 UI 보다는 Metrics 수집에 중점을 두고 사용합니다.

Prometheus는 Pull 방식으로 Metrics 수집을 합니다.

구성파일(`prometheus.yml`)에 Metrics 수집 대상을 설정하면 서비스 디스커버리(Service Discovery) 기능에 의해서 자동으로 Metrics를 수집합니다.

Prometheus에는 `Exporter`라는 개념이 있는데, Exporter는 모니터링 대상에서 Metrics를 수집해서 Prometheus에 전송하는 에이전트입니다.

### 설치

Prometheus는 다음과 같은 여러 가지 방법으로 설치해서 사용할 수 있습니다.

* 사전에 컴파일된 바이너리를 다운로드하여 실행
* Docker 이미지를 사용하여 실행
* 소스에서 직접 빌드
* Ansible, Puppet, Chef 등의 프로비저닝 도구를 사용

여기서는 Docker 이미지를 사용하여 설치하는 방법만 간단히 살펴 보겠습니다.

아래와 같이 `docker run` 명령어를 사용하여 Prometheus를 실행할 수 있습니다.

```bash
docker run -p 9090:9090 prom/prometheus
```

이 명령어는 `prom/prometheus` 이미지를 다운로드 받아서 실행하고, 9090 포트로 서비스를 제공합니다.

Prometheus 대시보드 접속은 브라우저를 열고 `http://localhost:9090` 주소로 접속하면 확인할 수 있습니다.

### 구성

Prometheus는 `prometheus.yml` 파일을 통해 설정을 관리합니다.

이 파일의 기본 위치는 `/etc/prometheus/prometheus.yml` 이지만, 
Docker 이미지를 사용할 경우 `/etc/prometheus/prometheus.yml` 파일을 볼륨 마운트하여 사용할 수 있습니다.

`prometheus.yml` 파일에는 Prometheus가 수집할 Metrics 정보와 수집 주기 등을 설정할 수 있습니다.

아래는 redis 서버의 Metrics를 수집하는 설정 예시입니다.

```yaml
# 다른 설정 부분 생략... 
scrape_configs:
  # 스크랩된 시계열 데이터에 `job=<job_name>` 레벨 추가 
  - job_name: "prometheus"
 
    # metrics_path 기본값은 '/metrics' 입니다.
    # scheme 기본값은 'http' 입니다.
 
    static_configs:
      - targets: ["localhost:9090"]
 
  - job_name: redis_exporter
    static_configs:
    - targets: ["localhost:9121"]
```

## Jaeger

Jaeger는 분산 시스템에서의 트랜잭션을 추적하고 모니터링하는 오픈소스 도구입니다.

Jaeger는 분산 시스템으로부터 **trace** 데이터를 수집하고, 이를 저장하고, 이를 시각화하는 세 가지 주요 기능을 제공합니다.

### 설치

Jaeger는 여러 구성 요소(Collector, Agent, Query 등)로 구성되어 있으며, 설치와 구성이 다른 Observability 도구에 비해 다소 복잡합니다. 

일반적으로 복잡한 운영 환경에서는 Jaeger의 각 구성 요소를 별도로 설치하고 구성합니다.

하지만, 개발이나 간단한 운영 환경에서는 Jaeger의 모든 주요 구성 요소(Agent, Collector, Query 서비스, UI)를 하나의 실행 파일 또는 컨테이너 이미지로 통합한 버전인 Jaeger All-in-One을 사용할 수 있습니다.

<div class="notice" markdown="1">
Jaeger All-in-One은 데이터를 메모리에 저장하므로, 실제 운영 환경에서는 사용하지 않는 것이 좋습니다.
</div>

아래와 같이 `docker run` 명령어를 사용하여 Jaeger All-in-One을 실행할 수 있습니다.

```bash
sudo docker run -d \ # 컨테이너를 백그라운드에서 실행
  --name jaeger \
  -e COLLECTOR_ZIPKIN_HOST_PORT=:9411 \ # Zipkin 호환 데이터를 수집하기 위한 포트 설정
  -e COLLECTOR_OTLP_ENABLED=true \ # OTLP(OpenTelemetry Protocol) 활성화
  -p 6831:6831/udp \ # UDP 포트 6831을 매핑하여 Jaeger Agent의 Thrift Compact 프로토콜 사용
  -p 6832:6832/udp \ # UDP 포트 6832를 매핑하여 Jaeger Agent의 Thrift Binary 프로토콜 사용
  -p 5778:5778 \ # Jaeger Agent의 HTTP 상태 포트 매핑
  -p 16686:16686 \ # Jaeger Query UI 포트 매핑
  -p 4317:4317 \ # OpenTelemetry gRPC 포트 매핑
  -p 4318:4318 \ # OpenTelemetry HTTP 포트 매핑
  -p 14250:14250 \ # Jaeger Collector의 gRPC 포트 매핑
  -p 14268:14268 \ # Jaeger Collector의 HTTP 포트 매핑
  -p 14269:14269 \ # Jaeger Collector의 HTTP 상태 포트 매핑
  -p 9411:9411 \ # Zipkin JSON API 포트 매핑
  jaegertracing/all-in-one:1.64.0
```

Jaeger All-in-One 방식 외에 Jaeger를 설치하는 방법에는 다음과 같은 방법이 있습니다.

* Kubernetes 환경에서 Jaeger Operator를 사용하여 Kubernetes 클러스터에 Jaeger 설치
* Jaege Operator를 사용하지 않고 Helm Chart를 사용하여 Kubernetes 클러스터에 Jaeger 설치
* Docker Compose를 사용하여 로컬 환경에 Jaeger 설치
* 소스 코드를 직접 빌드하여 Jaeger 설치
