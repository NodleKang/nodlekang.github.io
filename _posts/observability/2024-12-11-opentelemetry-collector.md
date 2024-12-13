---
layout: single
title: "[OpenTelemetry] Collector 설치와 구성"
date: 2024-12-11 11:00:00 +0900
categories: 
  - 모니터링
tag: 
  - Observability
  - OTel
  - Telemetry
  - OpenTelemetry
  - OTLP
  - Java
  - Instrumentation
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 OpenTelemetry 학습을 위해 테스트 환경을 구성하면서 학습한 OpenTelemetry Collector를 설치하고 구성하는 방법을 정리한 포스트입니다.

본 포스트에서는 아래와 같은 테스트 환경에서 필요한 OpenTelemetry를 설치하는 부분의 내용만 담겨 있습니다.

![테스트 환경](/assets/images/post/observability/2024-12-11-observability-collector/opentelemetry_test_env_collector.png)

OpenTelemetry 백엔드로 Jaeger를 설치하는 내용을 확인하고 싶다면 [링크]({% post_url 2024-12-11-opentelemetry-tools %})에서 확인하세요.

OpenTelemetry Java Agent 적용 내용을 확인하고 싶다면 [링크]({% post_url 2024-12-11-opentelemetry-java-agent %})에서 확인하세요.

# 개요

OpenTelemetry Collector는 다양한 소스에서 보낸 데이터를 수신하고(Receiver), 가공해서(Processor) 저장소로 내보내는(Exporter) 역할을 합니다.

# 설치

OpenTelemetry Collector는 여러 가지 방법으로 설치할 수 있으며, 원하는 방법을 선택하여 설치할 수 있습니다. 

1. Docker: Docker 이미지를 사용하여 Collector를 실행할 수 있습니다.
2. Docker Compose: 기존 docker-compose.yaml 파일에 OpenTelemetry Collector를 추가할 수 있습니다.
3. Kubernetes: 데몬셋으로 에이전트를 배포하고 단일 게이트웨이 인스턴스를 설정할 수 있습니다.
4. Linux: APK, DEB, RPM 패키지를 통해 Linux 시스템에 설치할 수 있습니다.
5. macOS: Intel 및 ARM 시스템용 릴리스가 제공됩니다.
6. Windows: gzip으로 압축된 tar 파일로 제공됩니다.
7. 소스에서 빌드: GitHub 저장소에서 최신 버전을 직접 빌드할 수 있습니다.

## Docker로 설치하기

Docker를 사용하여 OpenTelemetry Collector를 실행하는 방법은 다음과 같습니다.

```bash
# 최신 버전의 OpenTelemetry Collector 이미지를 다운로드 받습니다.
docker pull otel/opentelemetry-collector-contrib:0.115.1
# 다운로드 받은 이미지를 실행합니다.
docker run otel/opentelemetry-collector-contrib:0.115.1
```

사용자 정의 설정 파일을 사용하려면 다음과 같이 실행합니다.

```bash
# 설정 파일을 호스트에 복사합니다.
docker run -v /path/to/otel-collector-config.yaml:/etc/otel-collector-config.yaml otel/opentelemetry-collector-contrib:0.115.1 --config=/etc/otel-collector-config.yaml
```

## Docker Compose로 설치하기

docker-compose.yaml 파일에 OpenTelemetry Collector를 추가하여 실행할 수 있습니다.

```yaml
otel-collector:
  image: otel/opentelemetry-collector-contrib
  volumes:
    - ./otel-collector-config.yaml:/etc/otelcol-contrib/config.yaml
  ports:
    - 1888:1888 # pprof extension
    - 8888:8888 # Prometheus metrics exposed by the Collector
    - 8889:8889 # Prometheus exporter metrics
    - 13133:13133 # health_check extension
    - 4317:4317 # OTLP gRPC receiver
    - 4318:4318 # OTLP http receiver
    - 55679:55679 # zpages extension
```

## Linux에 설치하기

### DEB 패키지로 설치하기

Debian 계열의 Linux 시스템에는 DEB 패키지를 사용하여 OpenTelemetry Collector를 설치할 수 있습니다.

```bash
sudo apt-get update
sudo apt-get -y install wget
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.115.1/otelcol_0.115.1_linux_amd64.deb
sudo dpkg -i otelcol_0.115.1_linux_amd64.deb
```

### RPM 패키지로 설치하기

Red Hat 계열의 Linux 시스템에는 RPM 패키지를 사용하여 OpenTelemetry Collector를 설치할 수 있습니다.

```bash
sudo yum update
sudo yum -y install wget systemctl
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.115.1/otelcol_0.115.1_linux_amd64.rpm
sudo rpm -ivh otelcol_0.115.1_linux_amd64.rpm
```

### 매뉴얼하게 설치하기

바이너리가 포함된 tarball을 다운로드하여 수동으로 설치할 수도 있습니다.

```bash
curl --proto '=https' --tlsv1.2 -fOL https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.115.1/otelcol_0.115.1_linux_amd64.tar.gz
tar -xvf otelcol_0.115.1_linux_amd64.tar.gz
```

# 구성

OpenTelemetry Collector는 다양한 환경에서 실행할 수 있으며, 환경에 따라 다양한 설정이 필요합니다. 

매뉴얼하게 설치한 게 아니라면 기본적으로 구성 파일은 `/etc/<otel-directory>/config.yaml` 경로에 위치합니다.

구성 파일은 원격측정데이터에 접근하는 요소들(receivers, processors, exporters 등)을 정의합니다.

아래는 간단한 구성 파일의 예시입니다.

```yaml
# receivers: 데이터를 수신하는 구성 요소 
receivers:
  otlp: # OpenTelemetry Protocol을 사용하여 데이터를 수신합니다.
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317 # gRPC 프로토콜로 0.0.0.0:4317 포트에서 데이터를 수신합니다.
      http:
        endpoint: 0.0.0.0:4318 # HTTP 프로토콜로 0.0.0.0:4318 포트에서 데이터를 수신합니다.
# processors: 수신한 데이터를 처리하는 구성 요소
processors:
  batch: # 데이터를 일정 시간 동안 모아서 처리하는 processor입니다.

# exporters: 처리된 데이터를 내보내는 구성 요소
exporters:
  file:
    path: /engn001/tuna/otelcol/otelcol.log # 파일로 데이터를 내보냅니다.
  debug: # 디버그 정보를 출력합니다.
    verbosity: detailed
  otlp/jaeger: # Jaeger 형식으로 데이터를 내보냅니다.
    endpoint: localhost:4319 # localhost:4319 엔드포인트로 데이터를 내보냅니다.
    tls:
      insecure: true # 보안을 무시하고 데이터를 내보냅니다.

# extensions: 옵션으로 사용할 수 있는 확장 기능
extensions:
  health_check: # Collector의 상태를 확인하는 확장 기능입니다.
  pprof: # Go 언어의 pprof 프로파일링 도구를 사용하여 Collector의 성능을 분석할 수 있는 기능을 제공합니다.
    endpoint: 0.0.0.0:1777  
  zpages: # Collector의 내부 상태와 통계를 실시간으로 모니터링할 수 있는 웹 UI를 제공합니다.

# Collector의 서비스 설정
service:
  extensions: [health_check, pprof, zpages] # 사용할 확장 기능을 정의합니다.
  pipelines: # 데이터 파이프라인을 정의합니다.
    traces: # trace 데이터를 위한 파이프라인을 정의합니다.
      receivers: [otlp] # otlp receiver를 사용하여 데이터를 수신합니다.
      processors: [batch] # batch processor를 사용하여 데이터를 처리합니다.
      exporters: [file, otlp/jaeger]
    metrics: # metric 데이터를 위한 파이프라인을 정의합니다.
      receivers: [otlp]
      processors: [batch]
      exporters: [file, otlp/jaeger]
    logs: # log 데이터를 위한 파이프라인을 정의합니다.
      receivers: [otlp]
      processors: [batch]
      exporters: [file, debug]
```

구성 파일을 검토하려면 다음 명령을 실행합니다.

```bash
otelcol validate --config=customconfig.yaml
```

# 실행

OpenTelemetry Collector를 실행하려면 다음 명령을 실행합니다.

```bash
otelcol --config=customconfig.yaml
```
