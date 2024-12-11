---
layout: single
title: "Prometheus 설치와 구성"
date: 2024-12-10 09:00:00 +0900
categories: 
  - 모니터링
tag: 
  - Observability
  - Prometheus
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 Prometheus를 설치하고 구성하는 방법을 정리한 포스트입니다.

# 개요

Prometheus는 SoundCloud라는 해외 음악 관련 서비스 엔지니어가 개발한 감시 시스템입니다.

Prometheus는 시계열(time series) 데이터베이스를 사용하며, 서비스 디스커버리(Service Discovery) 기능에 의해서 자동으로 모니터링을 해 줍니다.

Prometheus를 활용하면 Metrics를 활용한 집계와 추세 예측을 할 수 있습니다.

# Prometheus에서 Expoter란?

Exporter는 Prometheus의 구성 요소 중에 하나로, 모니터링 대상에서 Metrics를 수집해서 Prometheus에 전송하는 에이전트입니다.

# 설치

Prometheus는 여러 가지 방법으로 설치할 수 있습니다.

* 사전에 컴파일된 바이너리를 다운로드하여 실행
* Docker 이미지를 사용하여 실행
* 소스에서 직접 빌드
* Ansible, Puppet, Chef 등의 프로비저닝 도구를 사용

저는 OpenTelemetry와 함께 Prometheus를 사용하는 테스트를 위해 Docker 이미지를 사용하여 설치하겠습니다.

## Docker로 설치하기

Docker를 사용하여 Prometheus를 실행하는 방법은 다음과 같습니다.

```bash
docker run -p 9090:9090 prom/prometheus
```

이 명령어는 `prom/prometheus` 이미지를 다운로드 받아서 실행하고, 9090 포트로 서비스를 제공합니다.

# Prometheus 대시보드 접속

브라우저를 열고 `http://localhost:9090` 주소로 접속하면 Prometheus 대시보드를 확인할 수 있습니다.
