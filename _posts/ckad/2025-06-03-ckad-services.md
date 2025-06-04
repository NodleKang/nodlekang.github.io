---
layout: single
title: "CKAD 연습(Services)"
date: 2025-06-03 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Services)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## Services & Networking

---

__*80 포트로 노출(expose)된 nginx 파드 생성하기*__

<details><summary>보기</summary>

**`--expose` 옵션** :
- 파드를 외부에 노출하기 위한 Service도 생성함. 
- 기본적으로 ClusterIP 타입의 Service가 생성되어 클러스터 내부에서 해당 파드로 접근할 수 있게 함.
- 이 옵션이 없으면 외부에서 파드에 접근하려면 Pod ID를 직접 알아야 함.

{% highlight bash %}
kubectl run nginx --image=nginx --port=80 --expose
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>
