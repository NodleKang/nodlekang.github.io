---
layout: single
title: "CKAD 연습"
date: 2025-06-04 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습

## 연습 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## Blue/Green 배포

blue 라는 이름으로 smlinux/nginx:blue 이미지를 가진 파드 2개를 배포하기, 레이블은 version=blue 설정하고, 포트는 8080 포트 사용하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment blue --image=smlinux/nginx:blue --replicas=2 --dry-run=client -o yaml > d.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: blue
    version: blue # 버전 정보 추가
  name: blue
spec:
  replicas: 2
  selector:
    matchLabels:
      app: blue
      version: blue # 버전 정보 추가
  template:
    metadata:
      labels:
        app: blue
        version: blue # 버전 정보 추가
    spec:
      containers:
      - image: smlinux/nginx:blue
        name: nginx
        ports: # 포트 지정
        - containerPort: 8080 # 컨테이너 포트
{% endhighlight %}

</details>
<p></p>

app-svc 서비스를 version=blue 레이블로 묶어 NodePort 타입의 서비스 포트 80으로 운영하기

`kubectl expose deployment <디플로이먼트이름> --port=<서비스가노출할포트> --target-port=<실제파드의포트>`
`kubectl create service nodeport <서비스이름> --tcp=<서비스가노출할포트>:<실제파드의포트>`

<details><summary>보기</summary>

{% highlight bash %}
kubectl expose deployment blue --type=NodePort --port=80 --target-port=8080 --name=app-svc
{% endhighlight %}

{% highlight bash %}
kubectl create service nodeport app-svc --tcp=80:8080 --dry-run=client -o yaml > s.yml
{% endhighlight %}

{% highlight yaml %}controlplane:~$ cat s.yml 
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: blue
    version: blue
  name: app-svc
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector: # 서비스 대상 파드 선택 기준
    app: blue
    version: blue # 버전 
  type: NodePort # 서비스 타입
{% endhighlight %}

</details>
<p></p>

green 이라는 이름으로 smlinux/nginx:green 이미지를 가진 파드 2개를 배포하기, 레이블은 version=green 설정하고, 포트는 8080 포트 사용하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment green --image=smlinux/nginx:green --replicas=2 --dry-run=client -o yaml > d.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: green
    version: green # 버전 정보 추가
  name: green
spec:
  replicas: 2
  selector:
    matchLabels:
      app: green
      version: green # 버전 정보 추가
  template:
    metadata:
      labels:
        app: green
        version: green # 버전 정보 추가
    spec:
      containers:
      - image: smlinux/nginx:green
        name: nginx
        ports: # 포트 지정
        - containerPort: 8080 # 컨테이너 포트
{% endhighlight %}

</details>
<p></p>

app-svc의 레이블을 version=green으로 변경하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl edit svc app-svc
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2025-06-05T07:04:14Z"
  labels:
    app: green
    version: green
  name: app-svc
  namespace: default
  resourceVersion: "7456"
  uid: 4da6c861-7c3a-42e0-a305-267023e1c5ce
spec:
  clusterIP: 10.105.229.67
  clusterIPs:
  - 10.105.229.67
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 30086
    port: 80
    protocol: TCP
    targetPort: 8080
  selector: # 선택 레이블 변경
    app: green
    version: green
  sessionAffinity: None
  type: NodePort
{% endhighlight %}

{% highlight bash %}
curl <노드IP>:30086
{% endhighlight %}

</details>
<p></p>

## Canary 배포

stable라는 이름으로 smlinux/app:stable 이미지를 가진 Pod를 2개 배포하기. 레이블은 name=app, version=stable을 사용하며 port는 8080 포트를 사용해야 함.

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment stable --image=smlinux/app:stable --replicas=2 --dry-run=client -o yaml > stable.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: stable
  name: stable
spec:
  replicas: 2
  selector:
    matchLabels:
      name: app       # 레이블 설정
      version: stable # 레이블 설정
  template:
    metadata:
      labels:
        name: app       # 레이블 설정
        version: stable # 레이블 설정
    spec:
      containers:
      - image: smlinux/app:stable
        name: app
{% endhighlight %}

</details>
<p></p>

canary-svc 서비스를 name=app 레이블로 묶어 NodePort 타입의 서비스 포트 80으로 운영하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl expose deployment stable --port=80 --target-port=8080 --name=canary-svc --type=NodePort
{% endhighlight %}

{% highlight bash %}
curl <CLUSTER-IP>:80
curl <노드IP>:31610
{% endhighlight %}

</details>
<p></p>

new라는 이름으로 smlinux/app:new-release 이미지를 가진 Pod 1개를 배포하기. 레이블은 name=app, version=new를 사용하며 port는 8080 포트를 사용해야 함.

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment new --image=smlinux/app:new-release --replicas=1 --dry-run=client -o yaml > new.yaml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: new
  name: new
spec:
  replicas: 1 # 복제 개수
  selector:
    matchLabels:
      name: app    # 레이블 설정
      version: new # 레이블 설정
  template:
    metadata:
      labels:
        name: app    # 레이블 설정
        version: new # 레이블 설정
    spec:
      containers:
      - image: smlinux/app:new-release
        name: app
        ports:
        - containerPort: 8080 # 컨테이너 포트 설정
{% endhighlight %}

</details>
<p></p>

canary-svc 서비스의 레이블을 version=new 로 변경하시오.

<details><summary>보기</summary>

{% highlight bash %}
kubectl edit svc canary-svc
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2025-06-05T07:39:49Z"
  labels:
    app: stable
  name: canary-svc
  namespace: default
  resourceVersion: "3721"
  uid: d8752163-552a-4e4a-bb8a-573606478734
spec:
  clusterIP: 10.102.223.13
  clusterIPs:
  - 10.102.223.13
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 31610
    port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    version: new # 선택 조건 변경
  sessionAffinity: None
  type: NodePort
{% endhighlight %}

{% highlight bash %}
curl <CLUSTER-IP>:80
curl <노드IP>:31610
{% endhighlight %}

</details>
<p></p>

## Rolling Update

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>

---

__**__

<details><summary>보기</summary>

{% highlight bash %}
{% endhighlight %}

</details>
<p></p>
