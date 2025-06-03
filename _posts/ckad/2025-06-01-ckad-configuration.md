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

__*ConfigMap 'options'를 var5=val5 값으로 생성하고, 'var5' 변수의 값을 'option'이라는 환경 변수로 로드하는 새로운 Nginx 파드를 생성하기*__

Kubernetes Docs > Define Environment Variables for a Container

<details><summary>보기</summary>

{% highlight bash %}
kubectl create configmap my-config --from-literal=var5=valu5
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    env:
      - name: option
        valueFrom:
          configMapKeyRef:
            name: my-config
            key: var5
{% endhighlight %}

{% highlight bash %}
kubectl exec nginx -it -- env | grep option
{% endhighlight %}

</details>
<p></p>

---

__*ConfigMap 'anotherone'를 'var6=val6', 'var7=val7' 값으로 생성하고, 이 ConfigMap을 환경 변수로 새로운 Nginx 파드를 생성하기*__

Kubernetes Docs > Configure a Pod to Use a ConfigMap

<details><summary>보기</summary>

{% highlight bash %}
kubectl create configmap anotherone --from-literal=var6=valu6 --from-literal=var7=valu7
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: busybox
    envFrom:
    - configMapRef:
        name: anotherone
{% endhighlight %}

</details>
<p></p>

---

__*ConfigMap 'cmvolume'를 'var8=val8', 'var9=val9' 값으로 생성하고, 이 ConfigMap을 nginx 파드 내에 '/etc/lala' 경로에 볼륨으로 마운트하기. 파드 생성 후 '/etc/lala' 경로를 ls 명령으로 확인하기*__

Kubernetes Docs > Configure a Pod to Use a ConfigMap

<details><summary>보기</summary>

{% highlight bash %}
kubectl create configmap cmvolume --from-literal=var8=valu8 --from-literal=var9=valu9
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
      - name: config-volume
        mountPath: /etc/lala
  volumes:
    - name: config-volume
      configMap:
        name: cmvolume
  restartPolicy: Never
{% endhighlight %}

{% highlight bash %}
kubectl exec nginx -it -- /bin/sh
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

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Resource requests and Limits

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Limit Ranges

---

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>

## Resource Quotas

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
