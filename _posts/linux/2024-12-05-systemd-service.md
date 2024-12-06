---
layout: single
title: "Linux 서비스 등록하기"
date: 2024-12-05 15:00:00 +0900
categories: 
  - Linux
tag: 
  - linux
  - systemd
  - systemctl
  - service
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 리눅스에서 systemd를 사용하여 서비스를 등록하는 방법에 대해서 정리한 것 입니다.

# 개요

리눅스에서 서비스를 등록하면 시스템이 부팅될 때 자동으로 실행되며, 사용자가 서비스를 쉽게 관리할 수 있습니다.

# 서비스 파일 작성

서비스 파일은 `/etc/systemd/system/` 디렉토리에 작성합니다.

`myapp` 이라는 서비스를 등록하고자 한다면, `myapp.service` 파일을 작성합니다.

```bash
$ sudo vi /etc/systemd/system/myapp.service
```

# 서비스 파일 구성

서비스 파일은 크게 3가지 섹션으로 구성됩니다.

1. `[Unit]`: 서비스의 설명과 의존성을 기술합니다.
2. `[Service]`: 서비스 실행에 필요한 정보를 기술합니다.
3. `[Install]`: 서비스를 어떻게 설치할 것인지 기술합니다.

## `[Unit]` 섹션

* `Description`: 서비스의 설명을 기술합니다.
* `After`: 이 서비스가 실행되기 전에 실행되어야 하는 서비스를 기술합니다.
* `Before`: 이 서비스가 실행된 후에 실행되어야 하는 서비스를 기술합니다.
* `Requires`: 서비스가 실행되기 전에 반드시 실행되어야 하는 서비스를 기술합니다.
* `Wants`: 서비스와 함께 실행되어야 하지만, 실패해도 무관한 서비스를 기술합니다.
* `Conflicts`: 이 서비스와 함께 실행될 수 없는 서비스를 기술합니다.
* `ConditionPathExists`: 특정 경로에 파일이 존재할 때만 이 서비스를 실행할 수 있도록 기술합니다.
* `ConditionPathIsDirectory`: 특정 경로가 디렉토리일 때만 이 서비스를 실행할 수 있도록 기술합니다.
* `ConditionFileNotEmpty`: 특정 파일이 비어있지 않을 때만 이 서비스를 실행할 수 있도록 기술합니다.
* `ConditionFileIsExecutable`: 특정 파일이 실행 가능할 때만 이 서비스를 실행할 수 있도록 기술합니다.

## `[Service]` 섹션

* `Type`: 서비스의 타입을 기술합니다. (simple, forking, oneshot, dbus, notify, idle 등)
  * `simple`: (기본값) `ExecStart`에 지정된 명령을 실행하고, 해당 프로세스가 종료되면 서비스도 종료됩니다.
  * `forking`: `ExecStart`에서 시작된 프로세스가 자식 프로세스를 생성하고, 부모 프로세스는 종료됩니다. systemd는 부모 프로세스가 종료되면 서비스가 시작된 것으로 간주합니다.
  * `oneshot`: `simple`과 유사하지만, 프로세스가 완전히 종료될 때까지 기다립니다.
  * `dbus`: `simple`과 유사하지만, 프로세스 결과 단위가 main 프로세스가 아니라 D-Bus 라는 이름을 획득한 후에만 서비스가 시작된 것으로 간주합니다.
  * `notify`: `simple`과 유사하지만, 서비스가 준비되면 `sd_notify()`를 호출하여 systemd에게 알립니다.
  * `idle`: `simple`과 유사하지만, 활성화된 작업이 없을 때까지 systemd 서비스를 시작하지 않습니다.
* `User`: 서비스를 실행할 사용자를 기술합니다.
* `Group`: 서비스를 실행할 그룹을 기술합니다.
* `ExecStart`: 서비스 시작 시 실행할 명령/바이너리/스크립트가 위치한 절대 경로를 기술합니다.
* `ExecStop`: 서비스 중지 시 실행할 명령/바이너리/스크립트가 위치한 절대 경로를 기술합니다.
* `PIDFile`: 서비스의 PID를 저정하는 파일의 절대 경로를 기술합니다. 주로 Type=forking 일 때 사용합니다. (예: `/var/run/myapp.pid`)
* `WorkingDirectory`: `ExecStart`에 지정된 명령을 이 디렉토리에서 실행합니다. 지정하지 않으면 루트(`/`) 디렉토리에서 실행합니다.
* `RuntimeDirectory`: 서비스 실행 시 생성되는 임시 디렉토리 경로를 기술합니다. 주로 PID 파일이나 소켓 파일을 저장하는 경로를 지정합니다.
* `Restart`: 서비스 재시작 정책을 기술합니다. (always, on-failure, on-abort 등)
  * `always`: 서비스가 종료되면 항상 재시작합니다.
  * `on-failure`: 서비스가 실패하면 재시작합니다.
  * `on-abort`: 서비스가 비정상적으로 종료되면 재시작합니다.
  * `no`: 재시작하지 않습니다.
  * `on-success`: 서비스가 성공적으로 종료되면 재시작합니다.
* `RestartSec`: 서비스 재시작 전에 대기할 시간을 기술합니다. 기본값 100ms 이고, 1초는 1s로 표기합니다. `min`(분), `s`(초), `ms`(밀리초) 단위로 설정할 수 있습니다.
* `StartLimitInterval`: 서비스 재시작을 시도하는 시간 간격을 기술합니다. `min`(분), `s`(초), `ms`(밀리초) 단위로 설정할 수 있습니다. 
* `StartLimitBurst`: 서비스 재시작을 시도하는 횟수를 기술합니다.
* `Environment`: 서비스 실행에 필요한 환경 변수를 기술합니다.
* `EnvironmentFile`: 서비스 실행에 필요한 환경 변수가 포함된 파일을 기술합니다.
* `TimeoutStartSec`: 서비스 시작이 완료될 때까지 대기할 시간을 기술합니다. `min`(분), `s`(초), `ms`(밀리초) 단위로 설정할 수 있습니다.
* `TimeoutStopSec`: 서비스 중지가 완료될 때까지 대기할 시간을 기술합니다. `min`(분), `s`(초), `ms`(밀리초) 단위로 설정할 수 있습니다.
* `StandardOutput`: 서비스 실행 결과를 출력할 파일을 기술합니다. 설정하지 않으면 `journal`에 기록됩니다. (예: `journal`, `syslog`, `file:/var/log/myapp.log`)
* `StandardError`: 서비스 실행 에러를 출력할 파일을 기술합니다. 설정하지 않으면 `journal`에 기록됩니다. (예: `journal`, `syslog`, `file:/var/log/myapp.log`)
* `SyslogIdentifier`: 서비스 실행 로그에 기록할 식별자를 기술합니다. 예를 들어, `myapp` 이라고 기술하면, `syslog` 에 `myapp`이라는 식별자로 로그가 기록됩니다.
* `KillSignal`: 서비스를 종료할 때 사용할 시그널을 기술합니다. 기본값은 `SIGTERM` 입니다.
  * `SIGTERM`: 정상적인 종료를 의미합니다.
  * `SIGKILL`: 강제 종료를 의미합니다.
  * `SIGINT`: 인터럽트 종료를 의미합니다.
  * `SIGQUIT`: 종료를 의미합니다.
* `KillMode`: 서비스를 종료할 때 사용할 방법을 기술합니다.
  * `control-group`: 프로세스 그룹을 종료합니다.
  * `process`: 프로세스를 종료합니다.
  * `mixed`: 프로세스 그룹을 종료하고, 프로세스를 종료합니다.
* `SendSIGKILL`: `KillMode`가 `mixed`일 때, `SIGKILL`을 보낼지 여부를 기술합니다. 기본값은 `yes` 입니다.

## `[Install]` 섹션

* `WantedBy`: 서비스를 어떤 타겟에 연결할 것인지 기술합니다. (주로 multi-user.target)
  * `multi-user.target`: 사용자가 로그인할 수 있는 다중 사용자 모드를 의미합니다.
  * `default.target`: 기본 타겟을 의미합니다.
* `RequiredBy`: 이 서비스를 필요로 하는 타겟 서비스를 기술합니다.
* `Also`: 이 서비스와 함께 설치할 서비스를 기술합니다.

# 서비스 파일 예시

내가 하고 있는 프로젝트는 OpenSearch를 사용하고 있습니다. 

아래는 내가 작성한 OpenSearch를 서비스로 등록하는 예시입니다.

```bash
$ sudo vi /etc/systemd/system/opensearch.service
```

```bash
[Unit]
Description=OpenSearch Service
After=network-online.target

[Service]
Type=forking
User=${USER}
Group=${GROUP}
Environment=OPENSEARCH_HOME=/engn001/mysolution/opensearch-2.13.0
ExecStart=/bin/bash ${OPENSEARCH_HOME}/start.sh
ExecStop=/bin/bash ${OPENSEARCH_HOME}/stop.sh
WorkingDirectory=${OPENSEARCH_HOME}

Restart=on-failure
RestartSec=5s
StartLimitInterval=1min
StartLimitBurst=3

LimitNOFILE=65535
LimitNPROC=65535
LimitAS=infinity
LimitFSIZE=infinity

SyslogIdentifier=opensearch

[Install]
WantedBy=multi-user.target
```

# 서비스 등록

나는 예시로 제시한 OpenSearch 서비스를 아래와 같이 등록해서 사용하고 있습니다.

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl enable openserach.service
$ sudo systemctl start openserach.service
```
