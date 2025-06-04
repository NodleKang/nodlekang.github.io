---
layout: single
title: "CKAD 연습(Services)"
date: 2025-06-03 17:00:00 +0900
categories:
  - Kubernetes
tag:
  - CKAD
toc: true
toc_label: 목차
toc_sticky: true
---

CKAD 연습(Services)

## 연습소스

[github에 CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)

## 참고 사이트

[쿠버네티스 연습사이트 - Kubernetes Playground (Killercoda)](https://killercoda.com/playgrounds/scenario/kubernetes)

## Services & Networking

---

__*80 포트로 노출(expose)된 nginx 파드 생성하기*__

**`--expose` 옵션:**
  - 파드를 외부에 노출하기 위한 Service도 생성함. 
  - 기본적으로 ClusterIP 타입의 Service가 생성되어 클러스터 내부에서 해당 파드로 접근할 수 있게 함.
  - 이 옵션이 없으면 외부에서 파드에 접근하려면 Pod ID를 직접 알아야 함.

<details><summary>보기</summary>

{% highlight bash %}
kubectl run nginx --image=nginx --port=80 --expose
{% endhighlight %}

</details>
<p></p>

---

__*CluserIP 타입 서비스가 생성됐는지와 엔드포인트를 확인하기*__

- **Endpoints**: Kubernetes의 `kube-proxy`가 Service로 들어오는 트래픽을 실제 파드로 전달하기 위해 참조하는 정보
  - **파드의 내부 IP 주소**: 서비스가 트래픽을 보낼 대상이 되는 파드의 내부 IP주소
  - **컨테이너 포트**: 해당 IP주소를 가진 파드 안에 있는 컨테이너가 리스닝하고 있는 포트 번호
  - 파드가 재시작되거나 스케일 아웃/인 될 때, Kubernetes controlplane에 의해 자동으로 업데이트 됨.

<details><summary>보기</summary>

{% highlight bash %}
kubectl describe svc nginx | grep -i endpoint
kubectl get endpoints
{% endhighlight %}

</details>
<p></p>

---

__*서비스의 ClusterIP를 가져오고, 임시 busybox 파드를 생성하고, wget 명령으로 그 IP에 접근해보기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl get service nginx

kubectl run busybox --image=busybox --rm --restart=Never -it -- /bin/sh -c "wget -O- 10.111.33.164:80"
{% endhighlight %}

</details>
<p></p>

---

__*같은 서비스를 ClusterIP에서 NodePort 타입으로 변환하고, NodePort의 포트 찾기. 찾은 노도의 IP에 접근해본 후에 파드와 서비스 삭제하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl edit svc nginx
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2025-06-04T06:22:33Z"
  name: nginx
  namespace: default
  resourceVersion: "5691"
  uid: 7c09b9af-0afc-4a19-a63a-c74293ffa107
spec:
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 30905
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort # 서비스 타입 변경
{% endhighlight %}

{% highlight bash %}
# 서비스가 80포트를 외부에 expose하고 있는 포트 번호 확인
kubectl get svc nginx
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.111.33.164   <none>        80:30905/TCP   31m
{% endhighlight %}

`wget -O- <노드의IP>:<서비스가 expose하고 있는 포트>`
{% highlight bash %}
# 리눅스면 루프백IP 사용
wget -O- 127.0.0.1:30905
{% endhighlight %}

{% highlight bash %}
kubectl delete svc nginx
kubectl delete pod nginx
{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>

---

__*연습*__

<details><summary>보기</summary>

{% highlight bash %}

{% endhighlight %}

</details>
<p></p>
