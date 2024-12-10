---
layout: single
title: "[OpenTelemetry] 텔레메트리"
date: 2024-12-09 09:00:00 +0900
categories: 
  - 모니터링
tag: 
  - observability
  - telemetry
  - log
  - metric
  - trace
  - span
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 오픈 텔레메트리(OpenTelemetry)에 대해 학습한 내용을 정리한 포스트 입니다.

첫 내용으로 `원격측정데이터` 즉, 텔레메트리(Telemetry)에 대해 학습한 내용을 정리했습니다.

# 텔레메트리(Telemetry)

텔레메트리 데이터는 분산 시스템이나 원격 장치에서 수집된 데이터를 의미합니다.

주로 원격 시스템이나 장치에서 데이터를 수집하기 때문에 `원격 측정 데이터`라고도 합니다.

주요 텔레메트리 데이터는 log, metric, trace가 있는데, 이 세가지 데이터를 Observability의 3가지 기둥이라고도 합니다.

## Log

로그 데이터는 시스템의 이벤트를 기록한 데이터입니다.

로그 데이터는 시간순으로 기록되며, 이벤트가 발생한 시간, 이벤트의 유형, 이벤트의 상세 내용 등을 포함합니다.

예로 Apache의 access log, error log, MySQL의 general log, slow query log 등이 있습니다.

## Metric

메트릭 데이터는 시간에 따른 수치 데이터를 의미합니다.

예로 CPU 사용량, 메모리 사용량, 디스크 사용량, 네트워크 사용량 등이 있습니다.

메트릭 데이터는 시간에 따른 변화를 추적하고, 시스템의 성능을 모니터링하는데 사용됩니다.

## Trace

트레이스 데이터는 분산 시스템에서의 요청을 추적하는 데이터입니다.

트레이스 데이터는 여러 서비스 간의 의존성을 추적하고, 서비스 간의 호출 관계, 지연 시간, 오류 등을 확인하는데 사용됩니다.

예로 사용자가 웹 페이지를 요청하면, 웹 서버에서 데이터베이스 서버로 요청을 전달하고, 데이터베이스 서버에서 데이터를 조회해서 응답하는 과정을 추적할 수 있습니다.

### Span

트레이스 데이터는 Span이라는 단위로 구성됩니다.

Span은 분산 시스템에서의 작업을 나타내는 단위로, 시작 시간, 종료 시간, 부모 Span, 태그, 로그 등의 정보를 포함합니다.

Span은 여러 Span이 연결되서 트레이스를 구성하며, 트레이스는 Span을 통해서 여러 서비스 간의 호출 관계를 추적할 수 있습니다.

![Traces](https://dt-cdn.net/images/distributed-traces-obs-1280-3f571c7197.png)

# 텔레메트리 데이터 특징

대부분의 텔레메트리 데이터는 시간에 따른 변화를 추적하는 시계열 데이터입니다. (Time Series Data)

텔레메트리 데이터에는 메타 데이터가 포함될 수 있습니다.

예로 로그 데이터에는 로그 레벨, 로그 메시지, 로그 위치 등의 메타 데이터가 포함될 수 있습니다.