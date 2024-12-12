---
layout: single
title: "[OpenTelemetry] Java 애플리케이션 제로코드 계측하기"
date: 2024-12-11 15:00:00 +0900
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

본 포스트에서는 OpenTelemetry Java Agent를 사용하여 Java 애플리케이션에 제로코드 계측을 적용하는 방법에 대해 설명합니다.

즉, Java 애플리케이션을 수정하지 않고도 OpenTelemetry를 적용하는 방법에 대해 설명합니다.

# OpenTelemetry Java Agent

OpenTelemetry Java Agent는 Java 애플리케이션에 계측을 적용하는 데 사용되는 도구입니다.

Java Agent는 Java 애플리케이션을 실행할 때, JVM에 붙어서 애플리케이션의 바이트코드를 조작하여 계측을 적용합니다.

이 때에 사용되는 Java Agent는 OpenTelemetry Java Instrumentation(Java Agent JAR)을 사용합니다.

# Java Agent 적용 방법

OpenTelemetry Java Agent를 사용하려면 다음과 같은 단계를 거칩니다.

1. Java Agent 다운로드
2. Java Agent 설정
3. Java Agent 구성
4. Java 애플리케이션 실행

# Java Agent 다운로드

최신 버전의 [opentelemetry-javaagent.jar](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar) 파일을 다운로드 받습니다.

# Java Agent 설정

Java 애플리케이션을 실행할 때, `-javaagent` 옵션을 사용하여 다운로드 받은 `opentelemetry-javaagent.jar`를 지정합니다.

JVM에 -javaagent 옵션을 사용하여 계측 라이브러리를 로드하면, 애플리케이션의 모든 클래스에 대해 계측이 적용됩니다.

기본적으로 Java Agent는 OTLP(OpenTelemetry Protocol)을 사용하여 http://localhost:4317 엔드포인트(OpenTelemetry Collector의 기본 엔드포인트)로 데이터를 내보냅니다.

Java Agent 설정은 직접 명령줄에서 사용하거나, 환경 변수를 설정하여 사용할 수 있습니다.

```bash
# 명령에서 직접 Java Agent 사용하는 방식
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -Dotel.service.name=your-service-name \
     -jar myapp.jar
```

명령에서 직접 사용할 수 있는 속성 목록은 [여기](https://opentelemetry.io/docs/languages/java/configuration/)에서 확인할 수 있습니다.

```bash
# 환경 변수를 설정하여 Java Agent 사용하는 방식
export JAVA_TOOL_OPTIONS="-javaagent:path/to/opentelemetry-javaagent.jar"
export OTEL_SERVICE_NAME="your-service-name"
java -jar myapp.jar
```

사용할 수 있는 환경 변수 목록은 [여기](https://opentelemetry.io/docs/specs/otel/configuration/sdk-environment-variables/)에서 확인할 수 있습니다. 

# Java Agent 구성

Java Agent는 다양한 방식으로 구성할 수 있습니다.

* 플래그를 사용하여 구성
* 환경 변수를 사용하여 구성
* 설정 파일을 사용하여 구성

플래그를 사용하여 구성하는 방법은 다음과 같습니다.

```bash
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -Dotel.service.name=your-service-name \
     -Dotel.traces.exporter=logging-otlp \
     -Dotel.exporter.otlp.endpoint=localhost:4317 \
     -jar myapp.jar
```

환경 변수를 사용하여 구성하는 방법은 다음과 같습니다.

```bash
OTEL_SERVICE_NAME=your-service-name \
OTEL_TRACES_EXPORTER=logging-otlp \
OTEL_EXPORTER_OTLP_ENDPOINT=localhost:4317 \
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -jar myapp.jar
```

설정 파일을 사용하여 구성하는 방법은 다음과 같습니다.

```bash
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -Dotel.javaagent.configuration-file=path/to/properties/file.properties \
     -jar myapp.jar
```

설정 파일은 다음과 같이 작성할 수 있습니다.

```properties
otel.service.name=your-service-name
otel.traces.exporter=logging-otlp
otel.exporter.otlp.endpoint=localhost:4317  # OpenTelemetry Collector 엔드포인트
```

# Java 애플리케이션 실행

Java 애플리케이션을 실행하면 Open Telemetry Java Agent가 JVM에 붙어서 계측을 적용하며, 계측 데이터를 OpenTelemetry Collector로 전송합니다.

```bash
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -Dotel.service.name=your-service-name \
     -jar myapp.jar
```

# 참고

* [OpenTelemetry 환경변수](https://opentelemetry.io/docs/specs/otel/configuration/sdk-environment-variables/)
* [OpenTelemetry Java SDK 구성](https://opentelemetry.io/docs/languages/java/configuration/)
* [Java OpenTelemetry Examples](https://github.com/open-telemetry/opentelemetry-java-examples?tab=readme-ov-file#java-opentelemetry-examples)
