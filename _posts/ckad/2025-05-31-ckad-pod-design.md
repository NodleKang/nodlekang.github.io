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

__*'app=v1' 라벨을 가진 파드 생성하기*__

{% highlight bash %}
kubectl run nginx --image=nginx --restart=Never --labels=app=v1
{% endhighlight %}

---

__*파드의 모든 라벨보기*__

{% highlight bash %}
kubectl get pod --help | grep -i label

kubectl get pod --show-labels
{% endhighlight %}

---

__*파드의 라벨을 'app=v2'로 변경하기*__

{% highlight bash %}
kubectl edit <파드>

혹은

kubectl label --help | more
kubectl label po nginx app=v2
{% endhighlight %}

---

__*특정 라벨의 값 확인하기*__

{% highlight bash %}
kubectl get pod --help | grep -i label
kubectl get po nginx --label-columns=app
{% endhighlight %}

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
