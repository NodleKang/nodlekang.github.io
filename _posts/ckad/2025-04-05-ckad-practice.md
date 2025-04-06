---
layout: single
title: "CKAD 연습습"
date: 2025-04-05 10:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: false
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