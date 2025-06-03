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

__*user ID 101로 실행하는 nginx 파드 생성용 YAML 만들기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 101
  containers:
  - name: nginx
    image: nginx
{% endhighlight %}

</details>
<p></p>

---

__*NET_ADMIN, SYS_TIME 기능을 추가한 파드용 YAML 만들기*__

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
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
{% endhighlight %}

</details>
<p></p>

## Resource requests and Limits

---

__*cpu=100m, memory=256Mi 이고, 제한은 cpu=200m, memory=512Mi 인 nginx 파드 생성하기*__

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
    resources:
      limits:
        cpu: "200m"
        memory: "512Mi"
      requests:
        cpu: "100m"
        memory: "256Mi"
{% endhighlight %}

</details>
<p></p>

## Limit Ranges

---

__*memory 제한이 최소 100Mi 에서 최대 500Mi 인 LimitRange를 가진 네임스페이스 limitrange 생성하기*__

**LimitRange**: 네임스페이스 안에서 개별 파드나 컨테이너가 사용할 수 있는 리소스의 최소/최대값, 기본값

**ResourceQuota**: 네임스페이스 전체에서 사용할 수 있는 리소스의 총량(예: CPU, 메모리, 오브젝트 수 등)을 제한

<details><summary>보기</summary>

{% highlight bash %}
kubectl create namespace limitrange
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limitrange
  namespace: limitrange
spec:
  limits:
  - max:
      memory: 500Mi
    min:
      memory: 100Mi
    type: Pod
{% endhighlight %}

{% highlight bash %}
kubectl describe limitranges -n limitrange
{% endhighlight %}

</details>
<p></p>

---

__*limitrange 네임스페이스에 memory requests가 250Mi 인 nginx 파드 생성하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: limitrange
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: 250Mi # 네임스페이스에 LimitRange 설정의 min보다 크거나 같아야 함
      limits:
        memory: 500Mi # 네임스페이스에 LimitRange 설정의 max보다 작거나 같아야 함
{% endhighlight %}

</details>
<p></p>

## Resource Quotas

**LimitRange**: 네임스페이스 안에서 개별 파드나 컨테이너가 사용할 수 있는 리소스의 최소/최대값, 기본값

**ResourceQuota**: 네임스페이스 전체에서 사용할 수 있는 리소스의 총량(예: CPU, 메모리, 오브젝트 수 등)을 제한

---

__*one 네임스페이스에 requests cpu=1, memory=1Gi 하드 리퀘스트와 cpu=2, memory=2Gi 하드 리밋을 갖는 ResourceQuota를 생성하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create ns one
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: ResourceQuota
metadata:
  name: my-rq
  namespace: one
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
{% endhighlight %}

</details>
<p></p>

---

__*one 네임스페이스에 CPU 1, 메모리 256Mi를 requests하고 CPU 2, 메모리 512Mi로 limits 하는 파드를 생성하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: pod-sample
  namespace: one
spec:
  containers:
  - name: pod-sample
    image: busybox
    resources:
      requests:
        memory: "256Mi"
        cpu: "1"
      limits:
        memory: "512Mi"
        cpu: "2"
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
