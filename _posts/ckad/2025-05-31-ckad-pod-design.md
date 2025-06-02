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
## Pod Placement

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

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
