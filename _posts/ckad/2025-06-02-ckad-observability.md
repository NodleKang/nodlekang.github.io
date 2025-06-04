---
layout: single
title: "CKAD 연습(Observability)"
date: 2025-06-02 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Observability)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## Liveness, Readiness, startup probes

- **Probe**: 컨테이너의 상태를 주기적으로 진단하는 매커니즘
- **Liveness**: 
  - 컨테이너가 정상적으로 **동작(running)**하고 있는지 주기적으로 확인하고, 비정상(예: 데드락, 메모리 고갈로 응답 없음)이면 컨테이너를 **복구(재시작)**함
  - 예시: 사람이 의식이 없으면 심폐소생술 하는 것과 같음
- **Readiness**: 
  - 컨테이너가 사용자 요청을 처리할 **준비(ready)**가 됐는지 확인하고, 준비가 안 됐으면 해당 컨테이너로의 **트래픽을 제어**(차단)함
  - 예시: 식당에서 아직 재료 준비 중이면 손님을 안 받고, 준비가 다 되면 '영업중' 표시를 하는 것과 같음

---

__*ls 명령을 실행하는 Liveness Probe가 포함된 nginx 파드 생성하기. 파드를 실행하고 프로브 상태를 확인한 다음, 파드 삭제하기*__

Kubernetes Docs > Configure Liveness, Readiness and Startup Probes

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      exec:
        command:
        - ls
{% endhighlight %}

{% highlight bash %}
kubectl describe pod nginx | grep -i liveness
kubectl delete pod nginx
{% endhighlight %}

</details>
<p></p>

---

__*컨테이너가 시작되고 5초 후에 liveness probe를 시작하고, 5초 간격으로 계속 liveness probe를 하도록 yaml 수정하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      exec:
        command:
        - ls
      initialDelaySeconds: 5 # 5초 후부터 헬스체크 시작
      periodSeconds: 5 # 5초 간격으로 헬스체크
{% endhighlight %}

</details>
<p></p>

---

__*포트 80을 포함하는 nginx 파드를 생성하고, 해당 파드에 포트 80의 '/' 경로로 HTTP 엔드포인트가 준비됐는지 하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
{% endhighlight %}

</details>
<p></p>

---

__*많은 파드가 qa, alan, test, production 네임스페이스에서 실행 중이다. 모든 파드는 liveness probe가 구성되어 있다. liveness probe가 실패한 모든 파드 목록을 `<namespace>/<pod name>` 형식으로 출력하기*__

`kubectl get events -o json`

<details><summary>보기</summary>

{% highlight bash %}
kubectl get events -o json | jq -r '.items[] | select(.message | contains("Liveness probe failed")).involvedObject | .namespace + "/" + .name'
{% endhighlight %}

</details>
<p></p>

## Logging

---

__*`i=0; while true; do echo "$1: $(date)"; i=$((i+1)); sleep 1; done` 명령을 실행하는 busybox 파드 생성하고, 로그 확인하기*__

Kubernetes Docs > Define a Command and Arguments for a Container

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
  labels:
    purpose: demonstrate-command
spec:
  containers:
  - name: command-demo-container
    image: busybox
    command: ["/bin/sh", "-c"]
    args: ["i=0; while true; do echo '$1: $(date)'; i=$((i+1)); sleep 1; done"]
  restartPolicy: OnFailure
{% endhighlight %}

{% highlight bash %}
kubectl logs pod/command-demo
{% endhighlight %}

</details>
<p></p>

## Debugging

---

__*`ls /notetext` 명령을 실행하는 busybox 파드 생성하고, 에러가 있는지 확인하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
  labels:
    purpose: demonstrate-command
spec:
  containers:
  - name: command-demo-container
    image: busybox
    command: ["/bin/sh", "-c"]
    args: ["ls /notetext"]
  restartPolicy: OnFailure
{% endhighlight %}

{% highlight bash %}
kubectl events --for pod/command-demo
kubectl logs pods/command-demo
{% endhighlight %}

</details>
<p></p>

---

__*Node에 CPU/memory 사용량 확인하기(반드시 metrics-server 실행 중이어야 함)*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl top nodes
{% endhighlight %}

</details>
<p></p>
