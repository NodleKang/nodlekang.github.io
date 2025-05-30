---
layout: single
title: "CKAD 연습(Multi-Container Pods)"
date: 2025-05-29 17:00:00 +0900
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
