---
layout: single
title: "CKAD 연습"
date: 2025-04-05 10:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 시험 준비

## K8S 리소스

[5분만에 Kubernetes 리소스 9개 이해하기](https://youtu.be/bM6-AbChWPE?si=ls-zHVG-TSRIDivB)

- Deployment
  - Pod를 자동으로 관리하는 컨트롤러
- DeamonSet
  - 클러스터의 모든 노드별로 필수로 올려야 하는 Pod를 배포하는 리소스
  - 노드가 추가되면 자동으로 Pod를 배포함
  - 예: 로그수집기, 모니터링에이전트
- StatefulSet
  - 쿠버네티스 Pod는 기본적으로 Stateless임
  - StatefulSet은 각각 유니크한 아이디가 배정되서 Pod가 삭제되거나 리스케줄링 되더라도 아이디를 유지하고, 각각 저장소를 배정받음
  - 예: 상태를 저장해야 하는 DB와 같은 Pod
- Service
  - Pod들을 묶어주는 일종의 로드밸런서 (Pod 자체는 삭제되거나 리스케줄링되면서 IP가 계속 변경됨)
  - 종류: ClusterIP(내부 트래픽 관리용으로 주로 사용), NodePort, LoadBalancer, ExternalName
- Ingress
  - 외부로 네트워크를 노출해야 한다면 엔드포인트를 하나로 묶어줘야 함
  - 최전방에서 요청을 받고 적절한 Service로 요청을 넘겨주는 역할

## Limit Ranges

특정 네임스페이스 내에서 Pod 또는 컨테이너가 사용할 수 있는 CPU, 메모리 등의 자원에 대해 최소값과 최대값을 제한하거나 기본값을 설정하는 정책

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limit-range
  namespace: mynamespace
spec:
  limits:
  - default: # 기본 CPU 제한 (CPU: 500 밀리코어, MEM: 50 Mi)
      cpu: 500m
      memory: 50Mi
    defaultRequest: # 기본 요청 제한 (CPU: 500 밀리코어, MEM: 50 Mi)
      cpu: 500m
      memory: 50Mi
    min:
      cpu: 100m
    type: Container # 기본 설정은 Container 에만 적용 가능
```

```bash
# 파일 반영
kubectl apply -f my-limit-range.yaml
```

```bash
# mynamespace 네임스페이스에 만든 my-limit-range 라는 Limit Range 확인
kubectl get limitranges my-limit-range -n mynamespace
```

```bash
# mynamespace 네임스페이스에 만든 my-limit-range 라는 Limit Range 상세 확인
kubectl describe limitranges my-limit-range -n mynamespace
```

## Resource Quotas

네임스페이스 전체에서 생성할 수 있는 오브젝트의 수와 총 자원 사용량(예: CPU, 메모리, 스토리지 등)에 제한을 두어, 특정 팀이나 애플리케이션이 클러스터 자원을 독점하지 못하도록 하는 정책

[kubectl create quota 명령](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-quota-em-)

```bash
# Resource Quotas 생성 - Pods 개수, Servoces 개수, 네임스페이스 지정
kubectl create quota my-quota --hard=pods=10,services=5 -n mynamespace
```

```bash
# mynamespace 네임스페이스에 만든 my-quota 라는 Resource Quotas 확인
kubectl get resourcequotas my-quota -n mynamespace
```

```bash
# mynamespace 네임스페이스에 만든 my-quota 라는 Resource Quotas 상세 확인
kubectl desccribe resourcequotas my-quota -n mynamespace
```

## Security Context

- Kubernetes의 Security Context는 Pod 또는 Container 수준에서 사용자 ID, 권한 상승 여부, 읽기 전용 파일 시스템 등과 같은 보안 관련 설정을 정의하는 구성 요소입니다.
- Container 수준에서 보안 관련 설정을 하면 Container에만 적용되며, Pod에 설정한 것을 무시하고 적용됩니다.

- 주요 옵션들:
  - runAsUser: 특정 user ID(UID)로 실행하도록 설정하며, 기본적으로 root(UID=0) 권한을 피하기 위해 사용
  - runAsGroup: 특정 group ID(GID)로 실행하도록 설정하여, 해당 그룹의 권한으로 작업을 수행
  - runAsNonRoot: 반드시 root가 아닌 사용자로 실행되도록 강제
  - privileged mode: 컨테이너가 호스트 시스템의 커널에 접근할 수 있는 루트와 동일한 권한을 부여
  - Linux Capabilities: 컨테이너에 필요한 최소한의 리눅스 커널 기능(capabilities)을 부여하거나 제한
  - readOnlyRootFilesystem: 컨테이너의 루트 파일 시스템을 읽기 전용으로 설정하여 파일 수정 및 쓰기를 방지
  - supplementalGroups: 컨테이너가 추가적으로 속할 group ID를 지정하여 파일 시스템 접근 권한을 확장

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext: # Pod에 적용
    runAsUser: 1000
    runAsGroup: 3000
    supplementalGroups: [4000]
  containers:
  - name: sec-ctx-demo
    image: registry.k8s.io/e2e-test-images/agnhost:2.45
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext: # Container에 적용
      runAsNonRoot: true
      runAsUser: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

```bash
kubectl apply -f security-context-demo.yaml
```

```bash
kubectl get pod security-context-demo
```

```bash
# security-context-demo 파드가 어떤 id로 동작 중인지 확인
kubectl exec security-context-demo -- id
```

## Secret (generic)

__*개념*__

- 비밀번호, API 키, SSH 키와 같은 민감한 데이터를 안전하게 저장하고 컨테이너에 전달하기 위해 사용하는 리소스로, 
- 데이터를 Base64로 인코딩하여 관리하며 ConfigMap과 유사한 방식으로 동작합니다.

__*documents*__

- kubectl references > create > secret generic

__*키워드*__

- Create a secret named
- for the environment variable inside the pod

__*샘플*__

- my-secret라는 이름의 Secret을 생성하고, key/value 쌍으로 key1/value3를 추가합니다.
- nginx 컨테이너 이미지를 사용하는 env-secret이라는 이름의 nginx Pod를 시작하고, Pod 내부에서 환경 변수 이름을 FC_VARIABLE 설정하여 Secret 키 key1의 값을 노출합니다.

### Secret 생성

```bash
# 클러스터 선택 - k8s
kubectl config use-context k8s
```

```bash
# Secret 생성:
#   이름: my-secret
#   키=밸류: key1=value3
kubectl create secret generic my-secret --from-literal=key1=value3
```

```bash
# Secret 확인
kubectl get secrets
```

```bash
# Secret 확인
kubectl describe secrets my-secret
```

### Secret 사용해서 Pod에 환경변수 설정

```bash
kubectl run env-secret --image=nginx --env=FC_VARIABLE=value --dry-run=client -o yaml > pod.yaml
```

```bash
vi pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: env-secret
  name: env-secret
spec:
  containers:
  - name: env-secret
    image: nginx
    env:
    - name: FC_VARIABLE
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: key1
```

```bash
kubectl apply -f pod.yaml
```

```bash
kubectl get pod env-secret
```

```bash
# 파드에 접속해서 env 확인하기
kubectl exec env-secret -- env
```

## ConfigMap

__*개념*__

- 애플리케이션에서 사용하는 비민감성 구성 데이터를 키-값 형태로 저장하여 컨테이너와 분리된 환경 설정을 관리하는 API 오브젝트
- ConfigMap은 일반적인 설정 데이터를 평문으로 저장하는 반면, Secret은 민감한 정보를 Base64로 인코딩하여 저장하며, RBAC 및 암호화를 통해 더 높은 보안성을 제공합니다.

__*documents*__

- kubectl references > create > configmap
- k8s docs > configmap 검색

__*키워드*__

- ConfigMap
- mount the key (VolumeMount)

__*샘플*__

- my-config라는 이름의 ConfigMap을 생성하고, key/value 쌍으로 key2/value4를 추가합니다.
- nginx 이미지를 사용하는 단일 컨테이너를 포함하는 configmap-pod이라는 이름의 Pod를 시작하고, 방금 생성한 키를 Pod 내부의 /app/data 디렉터리에 마운트합니다.

### ConfigMap 생성

```bash
# 클러스터 변경
kubectl config use-context k8s
```

```bash
kubectl create configmap my-config --from-literal=key2=value4
```

```bash
kubectl describe configmap my-config
```

### ConfigMap을 VolumeMount해서 Pod에 반영

```
kubectl run configmap-pod --image=nginx --dry-run=client -o yaml > configmap-pod.yaml
```

```
vi configmap-pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
  - name: configmap-pod
    image: nginx
    volumeMounts:
    - name: foo
      mountPath: "/app/data"
  volumes:
  - name: foo
    configMap:
      name: my-config
```

```
kubectl apply -f configmap-pod.yaml
```

```
# 파드 상세보기
# Mounts, Volumes 부분에서 내용 확인 가능
kubectl desribe pod configmap-pod
```

```
# 볼륨 확인
kubectl exec configmap-pod -- ls /app/data
```

```
# 볼륨안에 파일 내용 확인
kubectl exec configmap-pod -- cat /app/data/key2
```

## Resource Requests & Limits ★

__*개념*__

- **Resource Requests**: 컨테이너가 실행되기 위해 필요한 최소한의 자원을 정의하며, Kubernetes 스케줄러는 이를 기준으로 적절한 노드에 파드를 배치합니다.

- **Resource Limits**: 컨테이너가 최대 사용할 수 있는 자원을 설정하며, 이를 초과하면 Kubernetes는 해당 컨테이너를 제한하거나 종료할 수 있습니다.

__*Pod Resource 단위*__

- 쿠버네티스 CPU는 베어메탈 프로세서의 하이퍼스레드와 동일함 (AWS vCPU, GCP core, Azure vCore 등과 같음)
- 1000m (밀리코어) = 1 core = 1 CPU = 1 AWS vCPU = 1 GCP Core

__*documents*__

- k8s docs > limit 검색 > Resource Management for Pods and Containers

__*키워드*__

- a certain amount of CPU and memory
- a minimum of 200m CPU and 1Gi memory for its container

__*샘플*__

- myspace 네임스페이스에 pod-resources라는 이름의 Pod를 생성하며, 컨테이너가 최소 100m CPU와 200Mi 메모리, 최대 200m CPU와 500Mi 메모리를 요청하도록 설정합니다.
- 해당 Pod는 nginx 이미지를 사용해야 합니다.
- myspace 네임스페이스는 이미 생성되어 있습니다.

__*실습*__

- 메모리는 bytes 혹은 mebibytes(MiB)

```bash
# 클러스터 설정
kubectl config use-context microk8s
```

```bash
# namespace 확인
kubectl get namespace myspace
```

```bash
kubectl run pod-resources --image=nginx -n myspace --dry-run=client -o yaml > pod-resources.yaml
```

```bash
vi pod-resources.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-resources
  namespace: myspace
spec:
  containers:
  - name: pod-resources
    image: nginx
    resources:
      requests:
        cpu: "100m"     # 최소 CPU
        memory: "200Mi" # 최소 MEM
      limits:
        cpu: "200m"     # 최대 CPU
        memory: "500Mi" # 최대 MEM
```

```bash
kubectl apply -f pod-resources.yaml
```

```bash
kubectl describe pod pod-resources -n myspace | grep -i cpu
kubectl describe pod pod-resources -n myspace | grep -i mem
```

## LivenessProbe & ReadinessProbes

__*개념*__

- **LivenessProbe**: 컨테이너가 정상적으로 **살아있고** 작동 중인지 확인하며, 실패 시 Kubernetes가 해당 컨테이너를 **재시작**하여 장애 복구를 지원합니다. (재시작!)
- **ReadinessProbe**: 컨테이너가 외부 요청을 처리할 **준비가 되었는지** 확인하며, 준비되지 않은 경우 서비스 트래픽에서 **제외**하여 안정성을 확보합니다. (제외!)

__*documents*__

- k8s docs > livenessprobe

__*키워드*__

- restart the pod
- endpoint
- The service *** should never send traffic

__*샘플*__

- 클러스터에서 실행 중인 Pod가 응답하지 않을 경우, /healthz 엔드포인트가 HTTP 500을 반환하면 Kubernetes가 해당 Pod를 재시작하도록 설정합니다.

- 서비스 probe-http-pod는 Pod가 실패 상태일 때 절대로 트래픽을 보내지 않아야 합니다.

- 애플리케이션에는 다음과 같은 두 개의 엔드포인트가 있습니다:
  - /healthz: 애플리케이션이 정상적으로 작동 중인지 확인하며, HTTP 200을 반환하면 정상 상태를 의미하고, HTTP 500을 반환하면 응답하지 않는 상태를 나타냅니다.
  - /started: 애플리케이션이 트래픽을 수락할 준비가 되었는지 확인하며, HTTP 200을 반환하면 초기화가 완료된 상태를 나타내고, HTTP 500을 반환하면 초기화가 완료되지 않은 상태를 나타냅니다.
- 제공된 probe-http-pod Pod에 위 엔드포인트를 사용하는 프로브를 구성합니다.
- 프로브는 포트 80을 사용해야 합니다.

__*실습*__

```bash
# 클러스터 변경
kubectl config use-context k8s
```

```bash
# 동작중인 서비스 확인
kubectl get svc
```

```bash
# 동작중인 파드 내용을 소스로 받기
kubectl get pods probe-http-pod -o yaml > probe-http-pod.yaml
```

```bash
vi probe-http-pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: probe-http-pod 
  name: probe-http-pod
spec:
  containers:
  - name: probe-http
    image: registry.k8s.io/e2e-test-images/agnhost:2.40
    ports:
    - name: liveness-port
      containerPort: 80
    livenessProbe:
      httpGet:
        path: /healthz
        port: liveness-port # 숫자 80 으로 명시해도 됨
      failureThreshold: 1   # 최소 1회, 기본 3회
    readinessProbe:
      httpGet:
        path: /started
        port: liveness-port # 숫자 80 으로 명시해도 됨
      failureThreshold: 1   # 최소 1회, 기본 3회
```

```
# 동작 중인 파드 삭제
kubectl delete pod probe-http-pod
```

```
# 수정한 파드 반영
kubectl apply -f probe-http-pod.yaml
```

```bash
# 파드에서 Probe 정보 확인
kubectl describe pods probe-http-pod | grep -i liveness
  Liveness:    http-get http://:80/healthz delay=0s timeout:1s period=10s # seccess=1 # failure=3
kubectl describe pods probe-http-pod | grep -i readiness
  Readiness:   http-get http://:80/started delay=0s timeout:1s period=10s # seccess=1 # failure=3
```

```bash
# 파드의 IP주소 확인 (클러스터 내부 주소임)
kubectl get pod probe-http-pod -o wide
```

```bash
# 클러스터 내부에서 앞에서 확인한 IP와 함께 /healthz 엔드포인트 확인
curl http://:80/healthz
# 클러스터 내부에서 앞에서 확인한 IP와 함께 /stared 엔드포인트 확인
curl http://:80/stared
```

## Service Account ★

__*개념*__

- Kubernetes 클러스터 내에서 실행되는 애플리케이션(주로 Pod)이 Kubernetes API 서버와 안전하게 통신할 수 있도록 도와주는 특별한 계정입니다.
- Service Account는 사람대신 **애플리케이션이 사용**합니다.

- Service Account 사용 이유:
  - 애플리케이션(주로 Pod)이 API 서버라는 중앙 시스템에 요청을 보낼 때, Service Account가 그 애플리케이션이 누구인지 확인해주고 필요한 권한을 부여할 수 있게 해줍니다.
  - Kubernetes는 각 namespace마다 기본 Service Account을 자동으로 만들어줍니다. (default)
  - Service Account를 사용하면, 어떤 애플리케이션이 어떤 작업을 할 수 있는지 쉽게 관리할 수 있습니다.

__*documents*__

- k8s docs > serviceaccount

__*키워드*__

- Service Account

__*샘플*__

- prod 네임스페이스에 있는 app-deploy Deployment를 업데이트하여 app-serviceaccount Service Account로 실행되도록 설정합니다.
- 해당 Service Account는 이미 생성되어 있습니다.

__*실습*__

```bash
# 실행 중인 Deployment 확인하기
kubectl get deployments.apps -n prod
```

```bash
# 동작 중인 Pod 확인하기
kubectl get pods -n prod
```

```bash
# 존재하는 Service Accounts 확인하기
kubectl get serviceaccounts -n prod
```

```bash
# 실행중인 Pod를 통해서 Deployment가 어떤 Service Account로 실행했는지 확인하기
kubectl get pod app-deploy-2a0c09afai -n prod -o yaml | grep -i serviceaccount
```

```bash
# 동작중인 Deployment 소스 받기
kubectl get deployments.apps -n prod app-deploy -o yaml > app-deploy.yaml
```

```bash
vi app-deploy.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deploy
  namespace: prod
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app-deploy
  template:
    metadata:
      labels:
        app: app-deploy
    spec:
      containers:
      - image: nginx
        name: nginx
      serviceAccountName: app-serviceaccount # Service Account 이름 설정
```

```
# 동작 중이던 Deployment 삭제
kubectl delete -f app-deploy.yaml
```

```
# 새 Deployment 반영
kubectl delete -f app-deploy.yaml
```

```bash
# 실행중인 Pod를 통해서 Deployment가 어떤 Service Account로 실행했는지 확인하기
kubectl get pod app-deploy-xxx129uuup -n prod -o yaml | grep -i serviceaccount
```

## CronJob

__*개념*__

- 일정한 시간 간격으로 작업을 실행하도록 설정하는 리소스

__*documents*__

k8s docs > CronJob

__*키워드*__

- CronJob

__*샘플*__

- 매니페스트 파일 /data/test-cronjob.yaml에 Pod을 정의하세요. 이 Pod은 busybox:stable 컨테이너에서 uname 명령을 실행해야 합니다.
- 명령은 매 분마다 실행되며, 10초 이내에 완료되지 않으면 Kubernetes에 의해 종료됩니다.
- 크론잡 이름과 컨테이너 이름은 둘 다 cronjob-resource 로 설정해야 합니다.
- 위 매니페스트에서 리소스를 생성하고, 잡이 최소 한 번 성공적으로 실행되는지 확인하세요.
- 반드시 busybox:stable 이미지를 사용해야 합니다.

__*실습*__

```bash
kubectl config use-context k8s
```

```bash
vi /data/test-cronjob.yaml
```

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob-resource
spec:
  schedule: "* * * * *" # 매 분마다 실행
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cronjob-resource
            image: busybox:stable
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - uname; sleep 10         # 컨테이너 시작하고 10초 후에 종료
          restartPolicy: OnFailure    # 컨테이너가 실피했을 때만 재시작
          startingDeadlineSeconds: 10 # 10초 이내 실행 안 되면 건너뜀
```

```bash
kubectl apply -f /data/test-cronjob.yaml
```

```bash
kubectl get cronjob
```

```bash
# CronJob으로 실행되고 있는 CronJob 확인
kubectl get pod cronjob-resource-27667193-127398
# Job 상황을 주기적으로 확인
kubectl get jobs --watch
# Job을 로그 확인
kubectl logs <Pod이름>
```

## Docker Container 빌드 ★

__*개념*__

- CKAD는 다음과 같은 작업을 할 수 있어야 합니다.:
  - Container Build
  - Container Run
  - Container Save & Export
  - 고객에게 Container 이미지를 아카이브 파일로 배포

__*documents*__

k8s docs > docker command > [kubectl for Docker Users](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

__*키워드*__

- docker command

__*샘플*__

도커 컨테이너 이미지 빌드 및 저장
- /data/build/apache-php/Dockerfile 파일을 사용해 도커 이미지를 빌드하세요.
- 이미지 이름은 apache-php, 태그는 test-v1로 지정하세요.

컨테이너 이미지 저장
- 앞에서 만든 컨테이너 이미지를 고객에게 전달해야 합니다.
- 해당 이미지를 apache-php-test-v1.tar 파일로 저장하세요.
- 저장 경로는 /data/apache-php-test-v1.tar 이어야 합니다.

컨테이너 export
- 빌드한 컨테이너를 apache-php 파일 이름으로 실행하시오.
- 동작중인 apache-php 컨테이너를 /data/apache-php-container.tar 파일로 export 하시오.

__*실습*__

### docker 명령

__*docker run*__

```bash
# nginx 웹 서버를 포트 80에 띄우고, 항상 실행되도록 한 컨테이너 실행
docker run -d --restart=always -e DOMAIN=cluster --name nginx-app -p 80:80 nginx
```

- -d: 컨테이너를 백그라운드(Detached) 모드로 실행
- --restart=always: Docker 데몬이 재시작되거나 컨테이너가 종료돼도 자동 재시작
- -e DOMAIN=cluster: 컨테이너 내부에 DOMAIN=cluster 환경 변수 설정
- --name nginx-app: 컨테이너 이름을 nginx-app으로 지정
- -p 80:80: 호스트의 80 포트를 컨테이너의 80 포트에 바인딩
- nginx: 사용하는 이미지는 공식 nginx 이미지

__*주요 docker 명령들*__

다음은 요청하신 Docker 명령어들의 기능과 예시를 정리한 표입니다:

| 명령어           | 설명                                                     | 예시 |
|------------------|----------------------------------------------------------|------|
| `docker build`   | -t 옵션, Dockerfile을 기반으로 이미지 생성                        | `docker build -t 이미지이름:태그 .` |
| `docker save`    | -o 옵션, 이미지를 `.tar` 파일로 저장                              | `docker save -o myimg.tar 이미지이름:태그` |
| `docker load`    | -i 옵션, 저장된 이미지 파일을 로드                                | `docker load -i myimg.tar` |
| `docker export`  | 컨테이너를 `.tar`로 내보냄 (컨테이너 상태 저장)          | `docker export 컨테이너 > container.tar` |
| `docker import`  | 내보낸 `.tar` 파일로 이미지 생성                         | `docker import container.tar 이미지이름:태그` |
| `docker run`     | 새 컨테이너 생성 및 실행                                 | `docker run -d --name 컨테이너이름 nginx` |
| `docker attach`  | 실행 중인 컨테이너에 연결 (표준 입력/출력 공유)         | `docker attach 컨테이너이름` |
| `docker exec`    | 실행 중인 컨테이너 안에서 명령 실행                      | `docker exec 컨테이너이름 ls /` |
| `docker logs`    | 컨테이너의 로그 출력                                     | `docker logs 컨테이너이름` |
| `docker stop`    | 실행 중인 컨테이너 중지                                  | `docker stop 컨테이너이름` |
| `docker rm`      | 중지된 컨테이너 삭제                                     | `docker rm 컨테이너이름` |
| `docker version` | 클라이언트와 데몬의 Docker 버전 확인   | Docker 버전 출력 |
| `docker --help`    | Docker 명령어 전체 도움말 확인          | Docker 도움말 |

- `docker save`/`docker load`는 이미지와 관련된 명령어
- `docker export`/`docker import`는 컨테이너와 관련된 명령어

| 명령어           | 대상                     | 포함 정보                           | 사용 목적                     |
|------------------|--------------------------|------------------------------------|--------------------------------|
| `docker save`    | **이미지**                | 이미지의 모든 레이어, 태그, 히스토리 등 메타데이터 포함 | 이미지를 백업하거나 이동할 때 |
| `docker export`  | **컨테이너**              | 컨테이너의 파일 시스템만 포함         | 컨테이너의 파일 시스템만 추출  |
| `docker load`    | **이미지**                | `.tar` 파일로 저장된 이미지          | 저장된 이미지를 Docker에 불러오기 |
| `docker import`  | **컨테이너 파일 시스템**  | 파일 시스템만 포함 (새 이미지 생성)  | 컨테이너의 파일 시스템으로부터 새로운 이미지 생성 |

### Dockerfile을 이용한 이미지 빌드

```bash
# 제시된 Dockerfile 확인
cd /data/build/apache-php/
cat Dockerfile
```

```bash
# Dockerfile 이용해서 컨테이너 빌드
docker build -t apache-php:test-v1 .
```

```bash
# 컨테이너 이미지 목록 출력
docker images
```

### 이미지를 아카이브 파일로 저장

```bash
docker save -o /data/apache-php-test-v1.tar apache-php:test-v1
```

### 이미지를 컨테이너로 실행하고, export하기

```bash
# 이미지를 컨테이너로 실행
docker run -d --name apache-php apache-php:test-v1
```

```bash
# 동작중인 컨테이너를 export하기
docker export -o /data/apache-php-container.tar apache-php
```

## 제목

__*개념*__

__*documents*__

k8s docs > 

__*키워드*__

__*샘플*__

__*실습*__


## 제목

__*개념*__

__*documents*__

k8s docs > 

__*키워드*__

__*샘플*__

__*실습*__


## 제목

__*개념*__

__*documents*__

k8s docs > 

__*키워드*__

__*샘플*__

__*실습*__
