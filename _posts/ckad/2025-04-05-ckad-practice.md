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

```
# 파일 반영
kubectl apply -f my-limit-range.yaml
```

```
# mynamespace 네임스페이스에 만든 my-limit-range 라는 Limit Range 확인
kubectl get limitranges my-limit-range -n mynamespace
```

```
# mynamespace 네임스페이스에 만든 my-limit-range 라는 Limit Range 상세 확인
kubectl describe limitranges my-limit-range -n mynamespace
```

## Resource Quotas

네임스페이스 전체에서 생성할 수 있는 오브젝트의 수와 총 자원 사용량(예: CPU, 메모리, 스토리지 등)에 제한을 두어, 특정 팀이나 애플리케이션이 클러스터 자원을 독점하지 못하도록 하는 정책

[kubectl create quota 명령](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-quota-em-)

```
# Resource Quotas 생성 - Pods 개수, Servoces 개수, 네임스페이스 지정
kubectl create quota my-quota --hard=pods=10,services=5 -n mynamespace
```

```
# mynamespace 네임스페이스에 만든 my-quota 라는 Resource Quotas 확인
kubectl get resourcequotas my-quota -n mynamespace
```

```
# mynamespace 네임스페이스에 만든 my-quota 라는 Resource Quotas 상세 확인
kubectl desccribe resourcequotas my-quota -n mynamespace
```

## Pod Resource 단위

- 쿠버네티스 CPU는 베어메탈 프로세서의 하이퍼스레드와 동일함 (AWS vCPU, GCP core, Azure vCore 등과 같음)
- 1000m (밀리코어) = 1 core = 1 CPU = 1 AWS vCPU = 1 GCP Core
- 메모리는 bytes 혹은 mebibytes(MiB)

## ★ Resource Requests & Limits

- **Resource Requests**: 컨테이너가 실행되기 위해 필요한 최소한의 자원을 정의하며, Kubernetes 스케줄러는 이를 기준으로 적절한 노드에 파드를 배치합니다.

- **Resource Limits**: 컨테이너가 최대 사용할 수 있는 자원을 설정하며, 이를 초과하면 Kubernetes는 해당 컨테이너를 제한하거나 종료할 수 있습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
  namespace: mynamespace
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "30Mi" # 최대 메모리
        cpu: "200m"    # 최대 CPU
      limits:
        memory: "50Mi" # 최소 메모리
        cpu: "200m"    # 최소 CPU
```

```
kubectl apply -f frontend.yaml
```

```
kubectl get pod frontend -n mynamespace
```

```
kubectl describe pod frontend -n mynamespace
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

```
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

```
kubectl apply -f security-context-demo.yaml
```

```
kubectl get pod security-context-demo
```

```
# security-context-demo 파드가 어떤 id로 동작 중인지 확인
kubectl exec security-context-demo -- id
```

## Secret (generic)

- 비밀번호, API 키, SSH 키와 같은 민감한 데이터를 안전하게 저장하고 컨테이너에 전달하기 위해 사용하는 리소스로, 
- 데이터를 Base64로 인코딩하여 관리하며 ConfigMap과 유사한 방식으로 동작합니다.

- 참고: kubectl references > create > secret generic

### Secret 생성

```
# 클러스터 선택 - k8s
kubectl config use-context k8s
```

```
# Secret 생성:
#   이름: my-secret
#   키=밸류: key1=value3
kubectl create secret generic my-secret --from-literal=key1=value3
```

```
# Secret 확인
kubectl get secrets
```

```
# Secret 확인
kubectl describe secrets my-secret
```

### Pod에서 Secret 사용

```
kubectl run env-secret --image=nginx --env=FC_VARIABLE=value --dry-run -o yaml > pod.yaml
```

```
vi pod.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
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

```
kubectl apply -f pod.yaml
```

```
kubectl get pod env-secret
```

```
# 파드에 접속해서 env 확인하기
kubectl exec env-secret -- env
```
