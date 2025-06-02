---
layout: single
title: "CKAD 연습(Multi-Container Pods)"
date: 2025-05-30 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Multi-Container Pods)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## Multi-Container Pods

---

__*2개의 컨테이너가 있는 파드 생성하기, 둘 모두 busybox 이미지를 사용하고, "echo hello; sleep 3600" 명령 실행해야 함. 두 번째 컨테이너에 접속해서 'ls' 명령 실행하기*__

<details><summary>보기</summary>
{% highlight bash %}
kubectl run multi-container-pod --restart=Never --image=busybox --dry-run=client -o yaml > pod2.yml
vi pod2.yml
{% endhighlight %}
</details>
<p></p>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi-container-pod
  name: multi-container-pod
spec:
  containers:
  - image: busybox
    name: container1
    command: ["/bin/sh", "-c"] # Set shell interpreter
    args: ["echo hello; sleep 3600"] # Commands to run inside the container
  - image: busybox
    name: container2
    command: ["/bin/sh", "-c"] # Set shell interpreter
    args: ["echo hello; sleep 3600"] # Commands to run inside the container
  dnsPolicy: ClusterFirst
  restartPolicy: Always
{% endhighlight %}

<details><summary>보기</summary>
{% highlight bash %}
kubectl exec multi-container-pod -it -c container1 -- /bin/sh -c ls
{% endhighlight %}
</details>
<p></p>

---

__*nginx 컨테이너를 포트 80으로 노출(expose)하는 파드 생성하기. 'echo "Test" > /work-dir/index.html' 를 사용해서 페이지를 다운로드하는 init 컨테이너를 추가하기. emptyDir 타입 볼륨을 생성하고 두 컨테이너 모두에서 마운드하기. nginx 컨테이너에서는 "/usr/share/nginx/html"에 마운트하고, init 컨테이너에서는 "/work-dir"에 마운트하기. 작업이 완료되면 생성된 파드의 IP를 확인하고, bosybox 컨테이너를 생성한 다음 'wget -O- IP' 명령 실행하기'*__

<details><summary>보기</summary>
{% highlight bash %}
kubectl run nginx --image=nginx --port=80 --dry-run=client -o yaml > nginx.yml
vi nginx.yml
{% endhighlight %}
</details>
<p></p>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: common-volume
    ports:
    - containerPort: 80
    resources: {}
  initContainers:
  - image: busybox
    name: busybox
    volumeMounts:
    - mountPath: /work-dir
      name: common-volume
    command: ['sh', '-c', 'echo "Test" > /work-dir/index.html']
  volumes:
  - name: common-volume
    emptyDir: {} # emptyDir 타입 볼륨
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
{% endhighlight %}

{% highlight bash %}
kubectl get pod -o wide
kubectl run busybox --image=bosybox --restart=Never --rm -it -- /bin/sh -c "wget -O- 192.168.1.4"
{% endhighlight %}
