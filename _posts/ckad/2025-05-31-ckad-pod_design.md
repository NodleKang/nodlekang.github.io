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


## ReplicaSet 이란?

쿠버네티스에서 "지정된 개수의 Pod(파드) 복제본을 항상 실행 상태로 유지" 하는 것을 목적으로 하는 **컨트롤러**입니다.

사용자가 ReplicaSet을 직접 생성하고 관리하기보다는 **Deployment(배포)**라는 상위 개념의 오브젝트를 사용합니다.

예를 들어, Pod가 3개 떠 있어야 한다고 정의했다면, ReplicaSet은 어떤 이유로든 Pod 개수가 3개 미만이 되면 자동으로 새로운 Pod를 생성해서 3개를 유지합니다.

- ReplicaSet은 **selector** 필드를 사용하여 어떤 Pod를 관리할지 결정합니다. 
- Pod의 **metadata.labels**와 ReplicaSet의 **spec.selector.matchLabels**가 일치하는 Pod들을 관리 대상으로 삼습니다.
- **Deployment는 ReplicaSet의 버전 관리를 수행**하여 새로운 버전 배포 시 새 ReplicaSet을 만들고, 롤백 시 이전 ReplicaSet으로 돌아가도록 합니다.

ReplicaSet 샘플

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-app-replicaset
  labels:
    app: my-app
spec:
  replicas: 3 # 유지하고자 하는 Pod의 개수
  selector:
    matchLabels:
      app: my-app # 이 레이블을 가진 Pod를 관리 대상으로 삼음
  template: # Pod를 생성할 때 사용할 템플릿
    metadata:
      labels:
        app: my-app # Pod에 부여될 레이블 (selector의 matchLabels와 일치해야 함)
    spec:
      containers:
      - name: my-container
        image: my-image:1.0
        ports:
        - containerPort: 80
{% endhighlight %}

</details>
<p></p>

ReplicaSet 제어 명령
{% highlight bash %}
kubectl get rs
kubectl describe rs <리플리카셋이름>
{% endhighlight %}

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

__*nginx:1.18.0 이미지를 사용하며, 2개의 replicas를 가지고, nginx 이름을 가진 Deployment 생성하기. 컨테이너는 80 포트로 노출(expose)하도록 정의하기 (이 Deployment에 대한 service 생성 안 함)*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment nginx --image=nginx:1.18.0 --replicas=2 --port=80
{% endhighlight %}

{% highlight bash %}
kubectl get deployment nginx -o yaml
{% endhighlight %}

</details>
<p></p>

---

__*Deployment로 생성된 ReplicaSet의 YAML 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl get deployments.apps nginx --show-labels
kubectl get rs --selector app=nginx -o yaml
{% endhighlight %}

</details>
<p></p>

---

__*Rollout 상태 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout status deployment nginx
{% endhighlight %}

</details>
<p></p>

---

__*nginx 이미지를 nginx:1.19.8로 업데이트 하기*__

`kubectl set image (-f FILENAME | TYPE NAME) CONTAINER_NAME_1=CONTAINER_IMAGE_1 ... CONTAINER_MAME_N=CONTAINER_IMAGE_N [options]`

<details><summary>보기</summary>

{% highlight bash %}
# yaml에서 image 부분 찾아서 수정 후 저장하고 나오기
kubectl edit deployments.apps nginx
{% endhighlight %}

</details>
<p></p>
---

__*rollout 이력 및 replicas 상태 점검하기*__

`kubectl rollout history`

`kubectl get rs -o wide`

<details><summary>보기</summary>
{% highlight bash %}
kubectl rollout history deployments.apps nginx
{% endhighlight %}

{% highlight bash %}
kubectl get rs -o wide
{% endhighlight %}
</details>
<p></p>

---

__*최근 rollout 작업을 취소하고 새로운 파드들이 이전 이미지(nginx:1.18.0)으로 돌아갔는지 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout undo deployment nginx

kubectl rollout history deployments.apps nginx
kubectl get rs -o wide
{% endhighlight %}

</details>
<p></p>
---

__*의도적으로 없는 이미지로 Depolyment 갱신하기(nginx:1.91)*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl set image deployments nginx nginx=nginx:1.91

kubectl get po
kubectl rollout status deployment nginx
{% endhighlight %}

</details>
<p></p>
---

__*Deployment를 두 번째 리비전(Revision 2)으로 롤백하고 이미지 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout undo deployment nginx --to-revision=2

kubectl get rs -o wide
{% endhighlight %}

</details>
<p></p>
---

__*네 번째 리비전(Revision 4) 상세보기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout history deployment nginx --revision=4
{% endhighlight %}

</details>
<p></p>
---

__*Deployment를 5개 replicas로 스케일링하기*__

`kubectl scale deployment <디플로이이름> --replicas=5`

<details><summary>보기</summary>

{% highlight bash %}
kubectl scale deployment nginx --replicas=5
{% endhighlight %}

</details>
<p></p>
---

__*파드의 개수를 5-10개 사이로 오토스케일링하며, CPU 사용률을 80%로 타켓팅하는 Deployment 설정하기*__

`kubectl autoscale deployment`

<details><summary>보기</summary>

{% highlight bash %}
kubectl autoscale deployment nginx --min=5 --max=10 --cpu-percent=80

kubectl get hpa nginx # nginx의 HorizontalPodAutoscaler 확인
{% endhighlight %}

</details>
<p></p>

---

__*Deployment의 rollout 일시정지 시키기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout pause deploy nginx
{% endhighlight %}

</details>
<p></p>

---

__*nginx 이미지를 1.19.9 버전으로 업데이트하고, 롤아웃을 일시 중지했으니 별다른 변경 사항이 없는지 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl set image deploy nginx nginx=nginx:1.19.9

kubectl rollout history deploy nginx # 새로운 리비전 없음
{% endhighlight %}

</details>
<p></p>

---

__*롤아웃을 다시 시작하고 nginx:1.19.9 이미지가 적용되었는지 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl rollout resume deploy nginx
kubectl rollout history deploy nginx
{% endhighlight %}

</details>
<p></p>

---

__*Deployment 및 Horizontal Pod Autoscaler 삭제하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl delete deploy nginx
kubectl delete hpa nginx
{% endhighlight %}

</details>
<p></p>
---

__*canary deployement. nginx 인스턴스 2개(version=v1 및 version=v2)를 실행하여, 75%-25% 비율로 로드 밸런싱되는 카나리 배포 구현하기*__

3개 replicas를 가지는 'version=v1' Deployment 배포하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment my-app --image=nginx --replicas=3 --dry-run=client -o yaml > v1.yaml

vi v1.yaml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
    version: v1
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: v1
  template:
    metadata:
      labels:
        app: my-app
        version: v1
    spec:
      containers:
      - image: nginx
        name: nginx
{% endhighlight %}

{% highlight bash %}
kubectl apply -f v1.yaml
{% endhighlight %}

</details>
<p></p>

serive 생성하기

'Kubernetes Documentation > Service'에서 샘플 가져와서 사용하기(다음 항목은 필수로 설정하기):
- spec.type
- spec.ports
- spec.selector

Service 유형:
- **ClusterIP**: 클러스터 내부에서만 접근 가능한 서비스(기본값)
- **NodePort**: 클러스터 외부에서 각 노드의 특정 포트로 접근할 수 있는 서비스
- **LoadBalancer**: 클라우드 환경에서 외부 로드밸런서를 통해 접근 가능한 서비스

<details><summary>보기</summary>

{% highlight bash %}
vi my-svc.yaml
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  name: my-svc
spec:
  type: ClusterIP # 클러스터 내부에서만 접근 가능한 서비스
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
  selector:
    app: my-app
{% endhighlight %}

{% highlight bash %}
kubectl apply -f my-svc.yaml

kubectl get svc my-svc # Service의 IP 확인
NAME     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
my-svc   ClusterIP   10.108.186.79   <none>        80/TCP    7m26s
{% endhighlight %}

{% highlight bash %}
# 서비스 테스트용 파드 실행
kubectl run test-pod --image=busybox --restart=Never --rm -it -- /bin/sh

/ # wget -O- 10.108.186.79:80 # 서비스 접근 테스트
{% endhighlight %}

</details>
<p></p>

1개 replicas를 가지는 'version=v2' Deployment 배포하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl create deployment my-app-v2 --image=nginx --replicas=1 --dry-run=client -o yaml > v2.yaml

vi v2.yaml
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app-v2
    version: v2
  name: my-app-v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app-v2
      version: v2
  template:
    metadata:
      labels:
        app: my-app-v2
        version: v2
    spec:
      containers:
      - image: nginx
        name: nginx
{% endhighlight %}

{% highlight bash %}
kubectl apply -f v1.yaml
{% endhighlight %}

</details>
<p></p>

Service를 통해 노출된 IP를 호출하면 request가 두 버전으로 로드밸런싱 되는지 확인하기

<details><summary>보기</summary>

{% highlight bash %}
kubectl run test-pod --image=busybox --restart=Never --rm -it -- /bin/sh -c "while sleep 1; do wget -qO- 10.108.186.79:80; done"
{% endhighlight %}

</details>
<p></p>

v2 가 stable 하면, v2를 4개 replicas로 스케일링하고 v1 은 shutdown 하기

`kubectl scale --replicas=<개수> deployment <디플로이이름>`

<details><summary>보기</summary>

{% highlight bash %}
kubectl scale --replicas=4 deploy my-app-v2
kubectl delete deployments.apps my-app
{% endhighlight %}

</details>
<p></p>

## Jobs

---

__*perl:5.34 이미지를 사용하여 "perl -Mbignum=bpi -wle 'print bpi(2000)'" 명령어를 실행하는 pi라는 Job 생성하기*__

Kubernetes Docs > Job

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
{% endhighlight %}


{% highlight bash %}
kubectl logs jobs/pi
{% endhighlight %}

</details>
<p></p>

---

__*busybox 이미지를 사용하여 "echo hello;sleep 30;echo world" 명령을 실행하는 Job 생성하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: batch/v1
kind: Job
metadata:
  name: hello
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["/bin/sh", "-c", "echo hello;sleep 30;echo world"]
      restartPolicy: Never
  backoffLimit: 4
{% endhighlight %}

{% highlight bash %}
kubectl logs jobs/hello
{% endhighlight %}

</details>
<p></p>

---

__*실행 시간이 10초를 초과하면 자동으로 종료되는 잡(Job) 생성하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: batch/v1
kind: Job
metadata:
  name: auto-terminate
spec:
  activeDeadlineSeconds: 10 # spec 바로 아래 추가
  template:
    spec:
      containers:
      - name: auto-terminate
        image: busybox
        command: ["/bin/sh", "-c", "while true; do echo hello; sleep 2;done"]
      restartPolicy: Never
  backoffLimit: 4
{% endhighlight %}

{% highlight bash %}
kubectl logs jobs/auto-terminate
{% endhighlight %}

</details>
<p></p>

---

__*5번 실행되면 종료되는 잡(Job) 생성하기*__

<details><summary>보기</summary>

{% highlight yaml %}
apiVersion: batch/v1
kind: Job
metadata:
  name: auto-terminate
spec:
  completions: 5 # spec 바로 아래 추가
  template:
    spec:
      containers:
      - name: auto-terminate
        image: busybox
        command: ["/bin/sh", "-c", "echo completion"]
      restartPolicy: Never
  backoffLimit: 4
{% endhighlight %}

{% highlight bash %}
kubectl logs jobs/auto-terminate
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
