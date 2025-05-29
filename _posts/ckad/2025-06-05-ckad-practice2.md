---
layout: single
title: "CKAD 연습(Core Concepts)"
date: 2025-05-29 10:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Core Concepts)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## K8S 리소스

[5분만에 Kubernetes 리소스 9개 이해하기](https://youtu.be/bM6-AbChWPE?si=ls-zHVG-TSRIDivB)

- Deployment
  - Pod를 자동으로 관리하는 컨트롤러
- DeamonSet
  - 클러스터의 모든 노드별로 필수로 올려야 하는 Pod를 배포하는 리소스
  - 노드가 추가되면 자동으로 Pod를 배포함
  - 예: 로그수집기, 모니터링에이전트
- StatefulSet
  - 쿠버네티스 Pod는 기본적으로 Stateless임
  - StatefulSet은 각각 유니크한 아이디가 배정되서 Pod가 삭제되거나 리스케줄링 되더라도 아이디를 유지하고, 각각 저장소를 배정받음
  - 예: 상태를 저장해야 하는 DB와 같은 Pod
- Service
  - Pod들을 묶어주는 일종의 로드밸런서 (Pod 자체는 삭제되거나 리스케줄링되면서 IP가 계속 변경됨)
  - 종류: ClusterIP(내부 트래픽 관리용으로 주로 사용), NodePort, LoadBalancer, ExternalName
- Ingress
  - 외부로 네트워크를 노출해야 한다면 엔드포인트를 하나로 묶어줘야 함
  - 최전방에서 요청을 받고 적절한 Service로 요청을 넘겨주는 역할

## Pod 편집

실행 중인 Pod에서 편집할 수 있는 spec:
- `spec.containers[*].image`
- `spec.initContainers[*].image`
- `spec.activeDeadlineSeconds`
- `spec.tolerations`

실행 중인 Pod의 **환경변수, 서비스계정, 리소스제한** 은 **편집할 수 없습니다**.

하지만 꼭 편집해야 한다면 방법이 있습니다.

__*yaml 파일로 받아서 반영하는 방법*__

환경변수, 서비스계정, 리소스제한 같은 수정할 수 없는 spec을 수정하고 싶을 경우에는 YAML 포멧 파일로 받아서 수정합니다.

`kubectl get pod <파드이름> -o yaml > <파일이름>`

`vi <파일이름>`

실행 중인 Pod를 삭제하고 수정한 파일로 반영합니다.

`kubectl delete pod <파드이름>`

`kubectl create -f <파일이름>`

## Deployment 편집

Deployment에서는 Pod 템플릿의 모든 속성을 편집할 수 있습니다.

Pod 템플릿은 Deployment의 spec에 속하는 하위 요소이므로, 변경이 있을 때마다 Deployment는 자동으로 기존 Pod를 삭제하고 변경 사항이 적용된 새 Pod를 생성합니다.

`kubectl edit deployment <디플로이먼트이름>`

## Secrets

__*Secret의 기본 동작*__

- Secret에 저장되는 데이터는 **Base64 형식으로 인코딩**되고, 쉽게 디코딩할 수 있습니다. (**암호화 아님**)
- 일반 텍스트로 저장하는 것보다 안전한 정도입니다.

__*Secret의 안전성을 높이는 실제 방법*__

Secret 자체는 암호화되지 않지만, 모범 사례를 통해 안전성을 높일 수 있습니다.
- Secret 객체 정의 파일을 소스 코드 저장소에 커밋하지 않기
- Secret이 ETCD에 저장될 때는 암호화를 활성화하기 (Encryption at Rest)
  
__*Kubernetes의 Secret 처리 방식*__

- Secret은 해당 Secret을 필요로 하는 Pod가 실행되는 노드에만 전송됩니다.
- Kubelet은 Secret 데이터를 디스크에 쓰지 않고, 메모리 기반인 tmpfs(임시파일시스템)에 저장합니다.
- Secret에 의존하는 Pod가 삭제되면 Kubelet은 Secret 데이터의 로컬 복사본도 삭제합니다.

## ResourceQuota

**네임스페이스** 전체적으로 사용할 수 있는 총 **리소스 양**을 제한합니다.

오브젝트가 생성될 때 적용되며, 이미 실행 중인 오브젝트에는 소급 적용되지 않습니다.

## LimitRange

**개별 Pod의 최소/최대 리소스 양**을 제한합니다.

## CKAD-exercises

github에 CKAD-exercises 저장소 연습

https://github.com/dgkanatsios/CKAD-exercises

### Core Concepts

---

__*'mynamespace' 네임스페이스에 nginx 이미지가 있는 Pod 생성하기*__

`kubectl run <파드이름>`

<details><summary>보기</summary>

{% highlight bash %}
kubectl create namespace mynamespace
kubectl run nginx --image=nginx --restart=Never -n mynamespace
{% endhighlight %}

</details>
<p></p>

---

__*방금 설명한 Pod를 YAML로 작성하기*__

`kubectl run <파드이름> --dry-run=client -o yaml`

<details><summary>보기</summary>

{% highlight bash %}
kubectl run nginx --image=nginx --restart=Never -n mynamespace --dry-run=client -o yaml > nginx.yml
{% endhighlight %}

</details>
<p></p>

---

__*'env' 명령을 실행하는 busybox Pod 생성하기(kubectl 명령 사용)*__

`kubectl run` 또는 `kubectl create`와 같은 명령에서 `--command -- env` 옵션은 항상 명령의 가장 마지막에 배치해야 합니다.

이 옵션을 `--dry-run=client`와 같은 다른 옵션보다 앞에 사용하면 YAML 파일이 생성되기 전에 Pod가 실제로 생성될 수 있습니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run envpod --image=busybox -n mynamespace --dry-run=client -o yaml --command -- env  > envpod.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: envpod
  name: envpod
  namespace: mynamespace
spec:
  containers:
  - command:
    - env
    image: busybox
    name: envpod
  dnsPolicy: ClusterFirst
  restartPolicy: Always
{% endhighlight %}

{% highlight bash %}
kubectl apply -f envpod.yml
kubectl logs envpod -n mynamespace
{% endhighlight %}

</details>
<p></p>

---

__*CPU 1개, 메모리 1G, Pod 2개 hard limits이 있는 'myrq'라는 ResourceQuota 생성 YAML 작성하기*__

ResourceQuota는 **네임스페이스** 전체적으로 사용할 수 있는 **리소스 양**을 제한합니다.

`--hard` 옵션에서 파드 갯수는 `pods` 같이 복수형으로 명시해야 합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl create resourcequota myrq --hard='cpu=1,memory=1Gi,pods=2' -n mynamespace --dry-run=client -o yaml > myrq.yml
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: ResourceQuota
metadata:
  name: myrq
  namespace: mynamespace
spec:
  hard:
    cpu: 1
    memory: 1Gi
    pods: "2"
{% endhighlight %}

</details>
<p></p>

---

__*nginx 이미지로 Pod를 생성하고 80 Port로 트래픽 노출(expose)하기*__

`kubectl run <파드이름>` 명령과 함께 `--port` 옵션으로 포트번호를 지정합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run nginx --image=nginx --port=80 -n mynamespace
{% endhighlight %}

</details>
<p></p>

---

__*Pod 이미지를 nginx:1.24.0으로 변경하기. 이미지를 가져오는 즉시 컨테이너 다시 시작하기*__

`kubectl set image pod/<파드이름> <컨테이너이름>=<이미지이름:태그>`

<details><summary>보기</summary>

{% highlight bash %}
kubectl set image pod/nginx nginx=nginx:1.24.0 -n mynamespace
{% endhighlight %}

{% highlight bash %}
kubectl describe pod nginx -n mynamespace | grep -i image
kubectl describe pod nginx -n mynamespace | grep -i restart
{% endhighlight %}

</details>
<p></p>

---

__*직전에 생성한 Pod의 IP를 가져오고, 임시 busybox 이미지를 실행해서 생성한 Pod의 '/'에 wget 하기*__

파드 정보에서 `-o wide` 옵션을 사용해서 IP를 확인합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl get pod nginx -n mynamespace -o wide
{% endhighlight %}

{% highlight bash %}
kubectl run busybox --image=busybox -it --rm -n mynamespace -- /bin/sh -c "wget -O - 192.168.1.4:80/"
kubectl run -it --rm busybox --image=busybox -n mynamespace --command -- wget -O - 192.168.1.4:80/
{% endhighlight %}

</details>
<p></p>

---

__*Pod 로그 가져오기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl logs <파드이름>
{% endhighlight %}

</details>
<p></p>

---

__*Pod가 crash 되고 다시 시작된 경우, 이전 인스턴스에 대한 로그 가져오기*__

`--previous` 옵션 사용

<details><summary>보기</summary>

{% highlight bash %}
kubectl logs <파드이름> --previous
{% endhighlight %}

</details>
<p></p>

---

__*Pod에서 shell 실행하기*__

`kubectl exec` 명령에서 `-- /bin/sh`와 같이 `실행할 명령`은 맨 끝에 적어야 합니다.

`--`(더블 대시)는 kubectl에게 "지금부터는 파드 안에서 실행될 명령어를 전달할 것이니 혼동하지 마라"고 알려주는 역할을 합니다.

`kubectl exec <파드이름> -- <컨테이너 내부에서 실행할 명령> <그 명령의 인자들>`

{% highlight bash %}
kubectl exec -it <파드이름> -- /bin/sh
{% endhighlight %}

---

__*'hello world'를 echo 한후에 종료하는 busybox Pod를 생성하기*__

`--restart=Never` 옵션이 없으면 Pod를 계속 재시작하니, 해당 옵션을 포함해야 합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run busybox --image=busybox -n mynamespace --restart=Never -it -- echo "hello world"
{% endhighlight %}

</details>
<p></p>

---

__*직전과 동일한 작업을 하되, 완료되면 Pod를 자동으로 삭제하기*__

`--rm` 옵션을 사용합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run busybox --image=busybox -n mynamespace --restart=Never --rm -it -- echo "hello world"
{% endhighlight %}

</details>
<p></p>

__*nginx 파드를 생성하고 env 값을 'var1=value1'으로 설정하기. Pod 안에 env 값이 있는지 확인하기*__

`kubectl exec -it <파드이름> -- 명령어` 는 대화형(interactive)하게 파드의 특정 **컨테이너**에 접속해서 명령을 실행합니다.

컨테이너가 둘 이상이면 `kubectl exec -it <파드이름> -c <컨테이너이름> -- <실행할 명령>` 으로 실행합니다.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run nginx --image=nginx --env="var1=value1" -n mynamespace
kubectl exec -it nginx -n mynamespace -- env
{% endhighlight %}

</details>
<p></p>

__*연습*__

`명령`

<details><summary>보기</summary>

{% highlight bash %}
명령
{% endhighlight %}

</details>
<p></p>
