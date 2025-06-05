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
kubectl expose deployment app-deploy --name=app-service --type=NodePort --port=80 --target-port=8080
{% endhighlight %}

{% highlight bash %}
curl <CLUSTER-IP>:80
curl <워커노드IP>:32092
{% endhighlight %}

</details>
<p></p>

app-deploy를 롤링 업데이트 하고 롤백하기
- 이미지: smlinux/app:v2
- maxUnavailable: 25%
- maxSurge: 50%
- progressDeadlineSeconds: 300
- app:v1 으로 롤백

K8S Docs > Deployments > maxSurge 로 검색되는 영역

<details><summary>보기</summary>

{% highlight bash %}
kubectl edit deployments.apps app-deploy
{% endhighlight %}

{% highlight yaml %}
...
spec:
  progressDeadlineSeconds: 300 # 롤아웃 완료까지 기다릴 시간
  strategy:
    rollingUpdate:
      maxSurge: 50%       # 전체 파드에서 최대 50%까지만 추가 파드를 만들 수 있음
      maxUnavailable: 25% # 전체 파드 중에 최대 25% 까지만 사용할 수 없게 만들 수 있음
    type: RollingUpdate   # 롤링 업데이트
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: app-deploy
    spec:
      containers:
      - image: smlinux/app:v2 # 이미지 버전 변경
{% endhighlight %}

{% highlight bash %}
kubectl rollout status deployment app-deploy
kubectl rollout history deployment app-deploy
{% endhighlight %}

{% highlight bash %}
# 이전 버전으로 롤백
kubectl rollout undo deployment app-deploy
{% endhighlight %}

</details>
<p></p>

## Resource Quota

devops 네임스페이스에 Pods 10개, Service는 최대 5개까지 생성하게 제한하는 devops-quota 생성하기

생성할 때는 `kubectl create quota` 사용하지만, 정보를 확인할 때는 `kubectl get resourcequotas` 사용함

<details><summary>보기</summary>

{% highlight bash %}
kubectl create ns devops
kubectl create quota devops-quota --hard=pods=10,services=5 -n devops
{% endhighlight %}

</details>
<p></p>

## Pod Resource 제한

nginx 컨테이너가 동작할 때, 최대 메모리 50Mi, CPU 200m을 넘지 못하도록 구성하기. 그리고 nginx 컨테이너는 동작을 위해 cpu 200m, memory 30Mi를 요청하기.

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nginx-demo
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      limits:
        cpu: "200m"
        memory: "50Mi"
      requests:
        cpu: "200m"        
        memory: "30Mi"
{% endhighlight %}

</details>
<p></p>

## LimitRange

Pod 생성 시 리소스 limit과 request를 명시하지 않으면 CPU는 200m, memory는 50Mi가 기본으로 설정되도록 운영하기. 네임스페이스 이름은 devops, 자원 이름은 devops-limit 으로 사용하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create ns devops
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: LimitRange
metadata:
  name: devops-limit
  namespace: devops
spec:
  limits:
  - default: # 기본 limits 설정
      cpu: 200m
      memory: "50Mi"
    defaultRequest: # 기본 requests 설정
      cpu: 200m
      memory: "50Mi"
    type: Container
{% endhighlight %}

</details>
<p></p>

## Security Context

다음 조건을 만족하는 Security Context 구성하기
- safe 컨테이너는 UID 0인 root로 실행 금지
- safe 컨테이너를 실행할 때는 405 UID로 실행
- safe 컨테이너는 root 권한으로 escalation 금지

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 405     # Pod 내의 모든 컨테이너가 기본적으로 405 UID로 실행
    runAsNonRoot: true # Pod 내의 모든 컨테이너가 UID 0(root)으로 실행되는 것을 금지
  containers:
  - name: safe
    image: busybox
    securityContext:
      runAsUser: 405     # safe 컨테이너가 기본적으로 405 UID로 실행
      runAsNonRoot: true # safe 컨테이너가 UID 0(root)으로 실행되는 것을 금지
      allowPrivilegeEscalation: false # root 권한으로의 권한 상승 금지
{% endhighlight %}

</details>
<p></p>

## init 컨테이너

다음 조건에 맞는 init 컨테이너 추가하기
- fc-app.yml 파일로 main 컨테이너 애플리케이션이 동작함
- init 컨테이너로 busybox:1.28 추가하고, /workdir/fcdata.txt 라는 emptyDir 생성하기
- init 컨테이너가 fcdata.txt 파일을 생성하지 못 하면 main 컨테이너는 실행할 수 없게 하기
- init 컨테이너와 main 컨테이너는 볼륨 마운트로 /workdir 디렉토리를 공유함

<details><summary>보기 - 수정 전 fc-app.yml</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: fc-app
spec:
  containers:
  - name: main
    image: busybox:1.28
    command: ['sh', '-c', 'if [ !-f /workdir/fcdata.txt ];then exit 1;else sleep 300;fi']
{% endhighlight %}

</details>
<p></p>

<details><summary>보기 - 수정 후 fc-app.yml</summary>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: fc-app
spec:
  containers:
  - name: main
    image: busybox:1.28
    command: ['sh', '-c', 'if [ !-f /workdir/fcdata.txt ];then exit 1;else sleep 300;fi']
    volumeMounts:
    - name: vol
      mountPath: /workdir
  initContainers:
  - name: init
    image: busybox:1.28
    command: ['sh', '-c', 'touch /workdir/fcdata.txt']
    volumeMounts:
    - name: vol
      mountPath: /workdir
  volumes:
  - name: vol
    emptyDir: {}
{% endhighlight %}

</details>
<p></p>

## CronJob

다음 조건을 만족하는 컨테이너 실행하기
- /data/ckad/sample.yaml manifest 파일에 pod가 정의되어 있음
- sample.yaml을 수정하여 busybox:stable 컨테이너가 매분마다 실행되어야 함
- 10초 내에 완료되거나 kubernetes에 의해 종료되어야 함
- cronjob 이름과 container 이름 모두 hello여야 함
- hello 컨테이너가 성공적으로 실행되었는지 확인하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create cronjob hello --image=busybox:stable --schedule="*/1 * * * *" --dry-run=client -o yaml > /data/ckad/sample.yaml
vi /data/ckad/sample.yaml
{% endhighlight %}

{% highlight yaml %}
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  jobTemplate:
    metadata:
      name: hello
    spec:
      activeDeadlineSeconds: 10 # Job이 시작된 후 10초 이내에 완료되지 않으면 쿠버네티스는 해당 Job을 실패로 간주하고 종료함
      template:
        spec:
          containers:
          - image: busybox:stable
            name: hello
            command: 
            - sleep
            - 5
          restartPolicy: Never
  schedule: '*/1 * * * *'
{% endhighlight %}

{% highlight bash %}
kubectl get pods
{% endhighlight %}

</details>
<p></p>

## Service Account

frontend 네임스페이스에 app-1 디플로이먼트가 app 서비스계정을 사용하도록 수정하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl get deployment app-1 -n fronted -o yaml > pod.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-1
  namespace: frontend
  # ... (기타 메타데이터)
spec:
  # ...
  template:
    spec:
      serviceAccountName: app # 서비스 계정 정보 추가
      containers:
      = image: nginx
        name: nginx
        # ... (다른 컨테이너 및 볼륨 정의)
{% endhighlight %}

{% highlight bash %}
kubectl apply -f pod.yml
{% endhighlight %}

</details>
<p></p>

## docker 이미지 빌드

{% highlight bash %}
# 제시된 Dockerfile 확인
cd /data/build/apache-php/
cat Dockerfile
{% endhighlight %}

{% highlight bash %}
# Dockerfile 이용해서 컨테이너 빌드
docker build -t apache-php:test-v1 .
{% endhighlight %}

{% highlight bash %}
# Docker 이미지 확인
docker images
{% endhighlight %}

{% highlight bash %}
# Docker 이미지를 아카이브 파일로 저장
docker save -o /data/apache-php-test-v1.tar apache-php:test-v1
{% endhighlight %}

{% highlight bash %}
# Docker 이미지를 컨테이너로 실행
docker run -d --name apache-php apache-php:test-v1
{% endhighlight %}

{% highlight bash %}
# 동작 중인 컨테이너를 export 하기
docker export -o /data/apache-php-test-v1.tar apache-php
{% endhighlight %}
