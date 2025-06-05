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

## Rolling Update (애플리케이션 업데이트)

### 주요 옵션들

- **maxUnavailable**: 최대로 사용할 수 없는 파드 개수
  - 롤아웃 할 때, 동시에 얼마나 많은 파드를 서비스에서 제외할 수 있는지 조절하는 규칙
  - `maxUnavailable: 25%` 라고 설정하면, 전체 파드 중에 25% 까지만 동시에 사용할 수 없게 할 수 있음
- **maxSurge**: 최대 서지
  - 롤아웃 할 때, 서비스 용량을 일시적으로 늘려서 서비스 중단 없이 부드러운 전환을 돕는 역할을 함
  - `maxSurge: 50%` 라고 설정하면, 원래 파드 개수의 50% 만큼 더 많은 파드를 임시로 생성할 수 있음
- **progressDeadlineSeconds**: 진행 대기시간
  - 롤아웃이 시작하고 완료되는 데 허용하는 최대 시간
  - `progressDeadlineSeconds: 600` 라고 설정하면, 롤아웃을 시작한 후에 10분 안에 새로운 버전의 파드들이 ready 상태가 안 되면 Kubernetes는 롤아웃이 실패한 한 것으로 판단하고 롤백함
- **minReadySeconds**: 최소 대기 시간
  - 새로 업데이트된 파드가 서비스 트래픽을 받을 수 있을 정도로 ready 상태를 유지해야 하는 최소 시간
  - `minReadySeconds: 30` 라고 설정하면, 새로운 파드가 30초 이상 ready 상태를 유지해야만 정상으로 간주함
- **revisionHistoryLimit**: 최대 이력으로 남길 수 있는 리비전 개수

### 예제

디플로이먼트 생성:
- 이름: app-deploy
- 이미지: smlinux/app:v1
- 포트: 8080
- 복제: 3

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment app-deploy --replicas=3 --image=smlinux/app:v1 --port=8080
{% endhighlight %}

</details>
<p></p>

앞서 생성한 app-deploy의 서비스 생성:
- 이름: app-service
- 타입: NordPort
- 포트: 80

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
