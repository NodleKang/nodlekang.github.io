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

## Deployments

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
