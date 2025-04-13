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

> 개념

- 비밀번호, API 키, SSH 키와 같은 민감한 데이터를 안전하게 저장하고 컨테이너에 전달하기 위해 사용하는 리소스로, 
- 데이터를 Base64로 인코딩하여 관리하며 ConfigMap과 유사한 방식으로 동작합니다.

> documents

- kubectl references > create > secret generic

> 문제 키워드

- Create a secret named
- for the environment variable inside the pod

> 문제 샘플

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

> 개념

- 애플리케이션에서 사용하는 비민감성 구성 데이터를 키-값 형태로 저장하여 컨테이너와 분리된 환경 설정을 관리하는 API 오브젝트
- ConfigMap은 일반적인 설정 데이터를 평문으로 저장하는 반면, Secret은 민감한 정보를 Base64로 인코딩하여 저장하며, RBAC 및 암호화를 통해 더 높은 보안성을 제공합니다.

> documents

- kubectl references > create > configmap
- k8s docs > configmap 검색

> 문제 키워드

- ConfigMap
- mount the key (VolumeMount)

> 문제 샘플

- my-config라는 이름의 ConfigMap을 생성하고, key/value 쌍으로 key2/value4를 추가합니다.
- nginx 이미지를 사용하는 단일 컨테이너를 포함하는 configmap-pod이라는 이름의 Pod를 시작하고, 방금 생성한 키를 Pod 내부의 /app/data 디렉터리에 마운트합니다.

### ConfigMap 생성

```bash
# 클러스터 변경
kubectl config use-context k8s
```

```bash
kubectl create configmap my-config --from-literal=key2=value4
``

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

> 개념

- **Resource Requests**: 컨테이너가 실행되기 위해 필요한 최소한의 자원을 정의하며, Kubernetes 스케줄러는 이를 기준으로 적절한 노드에 파드를 배치합니다.

- **Resource Limits**: 컨테이너가 최대 사용할 수 있는 자원을 설정하며, 이를 초과하면 Kubernetes는 해당 컨테이너를 제한하거나 종료할 수 있습니다.

> Pod Resource 단위

- 쿠버네티스 CPU는 베어메탈 프로세서의 하이퍼스레드와 동일함 (AWS vCPU, GCP core, Azure vCore 등과 같음)
- 1000m (밀리코어) = 1 core = 1 CPU = 1 AWS vCPU = 1 GCP Core

> document

- k8s docs > limit 검색 > Resource Management for Pods and Containers

> 문제 키워드

- a certain amount of CPU and memory
- a minimum of 200m CPU and 1Gi memory for its container

> 문제 샘플

- myspace 네임스페이스에 pod-resources라는 이름의 Pod를 생성하며, 컨테이너가 최소 100m CPU와 200Mi 메모리, 최대 200m CPU와 500Mi 메모리를 요청하도록 설정합니다.
- 해당 Pod는 nginx 이미지를 사용해야 합니다.
- myspace 네임스페이스는 이미 생성되어 있습니다.

### 실습

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

> 개념

- **LivenessProbe**: 컨테이너가 정상적으로 **살아있고** 작동 중인지 확인하며, 실패 시 Kubernetes가 해당 컨테이너를 **재시작**하여 장애 복구를 지원합니다. (재시작!)
- **ReadinessProbe**: 컨테이너가 외부 요청을 처리할 **준비가 되었는지** 확인하며, 준비되지 않은 경우 서비스 트래픽에서 **제외**하여 안정성을 확보합니다. (제외!)

> document

- k8s docs > livenessprobe

> 문제 키워드

- restart the pod
- endpoint
- The service *** should never send traffic

> 문제 샘플

- 클러스터에서 실행 중인 Pod가 응답하지 않을 경우, /healthz 엔드포인트가 HTTP 500을 반환하면 Kubernetes가 해당 Pod를 재시작하도록 설정합니다.

- 서비스 probe-http-pod는 Pod가 실패 상태일 때 절대로 트래픽을 보내지 않아야 합니다.

- 애플리케이션에는 다음과 같은 두 개의 엔드포인트가 있습니다:
  - /healthz: 애플리케이션이 정상적으로 작동 중인지 확인하며, HTTP 200을 반환하면 정상 상태를 의미하고, HTTP 500을 반환하면 응답하지 않는 상태를 나타냅니다.
  - /started: 애플리케이션이 트래픽을 수락할 준비가 되었는지 확인하며, HTTP 200을 반환하면 초기화가 완료된 상태를 나타내고, HTTP 500을 반환하면 초기화가 완료되지 않은 상태를 나타냅니다.
- 제공된 probe-http-pod Pod에 위 엔드포인트를 사용하는 프로브를 구성합니다.
- 프로브는 포트 80을 사용해야 합니다.

### 실습

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

> 개념

- Kubernetes 클러스터 내에서 실행되는 애플리케이션(주로 Pod)이 Kubernetes API 서버와 안전하게 통신할 수 있도록 도와주는 특별한 계정입니다.
- Service Account는 사람대신 **애플리케이션이 사용**합니다.

- Service Account 사용 이유:
  - 애플리케이션(주로 Pod)이 API 서버라는 중앙 시스템에 요청을 보낼 때, Service Account가 그 애플리케이션이 누구인지 확인해주고 필요한 권한을 부여할 수 있게 해줍니다.
  - Kubernetes는 각 namespace마다 기본 Service Account을 자동으로 만들어줍니다. (default)
  - Service Account를 사용하면, 어떤 애플리케이션이 어떤 작업을 할 수 있는지 쉽게 관리할 수 있습니다.

> document

- k8s docs > serviceaccount

> 문제 키워드

- Service Account

> 문제 샘플

- prod 네임스페이스에 있는 app-deploy Deployment를 업데이트하여 app-serviceaccount Service Account로 실행되도록 설정합니다.
- 해당 Service Account는 이미 생성되어 있습니다.

### 실습

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
