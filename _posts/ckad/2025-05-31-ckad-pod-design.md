---
layout: single
title: "CKAD 연습(Pod Design)"
date: 2025-05-31 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Pod Design)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)


## ReplicaSet 이란?

쿠버네티스에서 "지정된 개수의 Pod(파드) 복제본을 항상 실행 상태로 유지" 하는 것을 목적으로 하는 **컨트롤러**입니다.

사용자가 ReplicaSet을 직접 생성하고 관리하기보다는 **Deployment(배포)**라는 상위 개념의 오브젝트를 사용합니다.

예를 들어, Pod가 3개 떠 있어야 한다고 정의했다면, ReplicaSet은 어떤 이유로든 Pod 개수가 3개 미만이 되면 자동으로 새로운 Pod를 생성해서 3개를 유지합니다.

- ReplicaSet은 **selector** 필드를 사용하여 어떤 Pod를 관리할지 결정합니다. 
- Pod의 **metadata.labels**와 ReplicaSet의 **spec.selector.matchLabels**가 일치하는 Pod들을 관리 대상으로 삼습니다.
- **Deployment는 ReplicaSet의 버전 관리를 수행**하여 새로운 버전 배포 시 새 ReplicaSet을 만들고, 롤백 시 이전 ReplicaSet으로 돌아가도록 합니다.

ReplicaSet 샘플

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-app-replicaset
  labels:
    app: my-app
spec:
  replicas: 3 # 유지하고자 하는 Pod의 개수
  selector:
    matchLabels:
      app: my-app # 이 레이블을 가진 Pod를 관리 대상으로 삼음
  template: # Pod를 생성할 때 사용할 템플릿
    metadata:
      labels:
        app: my-app # Pod에 부여될 레이블 (selector의 matchLabels와 일치해야 함)
    spec:
      containers:
      - name: my-container
        image: my-image:1.0
        ports:
        - containerPort: 80
{% endhighlight %}

</details>
<p></p>

ReplicaSet 제어 명령
{% highlight bash %}
kubectl get rs
kubectl describe rs <리플리카셋이름>
{% endhighlight %}

## Labels & Annotations

---

__*'app=v1' 레이블을 가진 파드 생성하기*__

{% highlight bash %}
kubectl run nginx --image=nginx --restart=Never --labels=app=v1
{% endhighlight %}

---

__*파드의 모든 레이블보기*__

{% highlight bash %}
kubectl get pod --help | grep -i label

kubectl get pod --show-labels
{% endhighlight %}

---

__*파드의 레이블을 'app=v2'로 변경하기*__

{% highlight bash %}
kubectl edit <파드>

혹은

kubectl label --help | more
kubectl label po nginx app=v2
{% endhighlight %}

---

__*특정 레이블의 값 확인하기*__

{% highlight bash %}
kubectl get pod --help | grep -i label
kubectl get po nginx --label-columns=app
{% endhighlight %}

---

__*레이블 제거하기*__

{% highlight bash %}
kubectl label po nginx app-
혹은
kubectl label po --selector "app=v2" app-
{% endhighlight %}

---

__*'app=v2' 레이블을 가진 파드만 필터링하기*__

레이블을 이용한 필터링에는 `--selector` 옵션을 사용합니다.

{% highlight bash %}
kubectl get pod --selector=app=v2 --show-labels
{% endhighlight %}

---

__*'app=v1' 혹은 'app=v2'를 가진 파드들에 'tier=web' 레이블 추가하기*__

{% highlight bash %}
kubectl label po --selector "app in (v1,v2)" tier=web
{% endhighlight %}

---

__*'app=v2' 레이블을 가진 모든 파드에 'owner:marketing' 애너테이션 추가하기*__

애너테이션 관련 작업은 `kubectl annotate` 명령을 사용합니다.

{% highlight bash %}
kubectl annotate po --selector "app=v2" owner=marketing
{% endhighlight %}

---

__*파드의 애너테이션 확인하기*__

애너테이션 관련 작업은 `kubectl annotate` 명령을 사용합니다.

{% highlight bash %}
kubectl annotate po --selector "app=v2" --list
{% endhighlight %}

---

__*파드의 애너테이션 제거하기*__

{% highlight bash %}
kubectl annotate po nginx description- owner-
{% endhighlight %}
<p></p>

## 파드 위치선정(Pod Placement)

---

__*'accelerator=nvidia-tesla-p100' 레이블이 있는 노드에 배포할 파드를 생성하기*__

<details><summary>보기</summary>

노드에 레이블을 추가합니다.

{% highlight bash %}
kubectl label nodes <노드이름> accelerator=nvidia-tesla-p100
kubectl get nodes --show-labels
{% endhighlight %}

파드 YAML에 'nodeSelector' 속성을 사용합니다.
{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
  nodeSelector: # ★ 추가 ★
    accelerator: nvidia-tesla-p100 # 레이블 선택 조건
{% endhighlight %}

YAML에서 파드가 배치되어야 할 위치를 찾을 수 있습니다.
{% highlight bash %}
kubectl explain po.spec
{% endhighlight %}

</details>
<p></p>

---

__*`tier=frontend:NoSchedule`이라는 테인트(taint)를 노드에 적용하기. 그 노드에 파드가 스케줄링 될 수 있도록 톨러레이션(toleration) 설정하기*__

<details><summary>보기</summary>

테인트(taint)를 파드에 적용하기

{% highlight bash %}
kubectl taint node node1 tier=frontend:NoSchedule
kubectl describe node node1
{% endhighlight %}

테인트(taint)를 가진 노드에 파드가 스케줄링될 수 있도록 톨러레이션(toleration) 설정하기

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations: # 톨러레이션 설정
  - key: "tier"
    operator: "Equal"
    value: "frontend"
    effect: "NoSchedule"
{% endhighlight %}

</details>
<p></p>

## Deployments

---

__*nginx:1.18.0 이미지를 사용하며, 2개의 replicas를 가지고, nginx 이름을 가진 Deployment 생성하기. 컨테이너는 80 포트로 노출(expose)하도록 정의하기 (이 Deployment에 대한 service 생성 안 함)*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment nginx --image=nginx:1.18.0 --replicas=2 --port=80
{% endhighlight %}

{% highlight bash %}
kubectl get deployment nginx -o yaml
{% endhighlight %}

</details>
<p></p>

---

__*Deployment로 생성된 ReplicaSet의 YAML 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl get deployments.apps nginx --show-labels
kubectl get rs --selector app=nginx -o yaml
{% endhighlight %}

</details>
<p></p>

---

__*Rollout 상태 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout status deployment nginx
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>





## Jobs

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Cron Jobs

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
