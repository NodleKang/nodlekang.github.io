---
layout: single
title: "CKAD 연습(Configuration)"
date: 2025-06-01 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Configuration)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## ConfigMaps

key-value 쌍으로 비밀이 아닌 설정 데이터를 갖고 있는 리소스

---

__*foo=lala,foo2=lolo 값을 갖는 configmap 생성하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create configmap my-config --from-literal=foo=lala --from-literal=foo2=lolo
{% endhighlight %}

</details>
<p></p>

---

__*configmap 값 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl describe configmaps my-config
{% endhighlight %}

</details>
<p></p>

---

__*텍스트 파일을 사용해서 configmap 생성하기*__

<details><summary>보기</summary>

{% highlight bash %}
echo -e "foo3=lili\nfoo4=lele" > config.txt
{% endhighlight %}

{% highlight bash %}
kubectl create configmap my-config --from-file=config.txt
{% endhighlight %}

</details>
<p></p>

---

__*env 파일을 사용해서 configmap 생성하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create configmap my-config --from-env-file=config.env
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

## SecurityContext

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Requests and Limits

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Secrets

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Service Accounts

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
