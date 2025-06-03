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
    env: # 환경변수
      - name: option
        valueFrom:  # 값을 가져오기
          configMapKeyRef:  # ConfigMap에 Key를 참조해서
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
    envFrom: # 환경변수 가져오기
    - configMapRef: # ConfigMap 참조해서
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

__*Secret의 기본 동작*__

- Secret에 저장되는 데이터는 **Base64 형식으로 인코딩**되고, 쉽게 디코딩할 수 있습니다. (**암호화 아님**)
- 일반 텍스트로 저장하는 것보다 안전한 정도입니다.
  
__*Kubernetes의 Secret 처리 방식*__

- Secret은 해당 Secret을 필요로 하는 Pod가 실행되는 노드에만 전송됩니다.
- Kubelet은 Secret 데이터를 디스크에 쓰지 않고, 메모리 기반인 tmpfs(임시파일시스템)에 저장합니다.
- Secret에 의존하는 Pod가 삭제되면 Kubelet은 Secret 데이터의 로컬 복사본도 삭제합니다.

---

__*'password=mypass' 값을 가진 mysecret 이라는 Secret 생성하기*__

<details><summary></summary>

{% highlight bash %}
kubectl create secret generic mysecret --from-literal=password=mypass
{% endhighlight %}

</details>
<p></p>

---

__*key/value를 파일에서 가져와서 mysecret2 이라는 시크릿 생성하기*__

<details><summary>보기</summary>

{% highlight bash %}
echo -n admin > username

kubectl create secret generic mysecret2 --from-file=username
{% endhighlight %}

</details>
<p></p>

---

__*mysecret2 의 값 확인하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl get secret mysecret2 -o yaml
{% endhighlight %}

</details>
<p></p>

---

__*mysecret2 시크릿을 /etc/foo 경로에 마운트하는 nginx 파드 생성하기*__

Kubernetes Docs > Secrets

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
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/foo"
  volumes:
  - name: secret-volume
    secret:
      secretName: mysecret2
{% endhighlight %}

</details>
<p></p>

__*방금 생성한 파드를 삭제하고, mysecret2 시크릿에서 username 변수를 가져와서 USERNAME 이라는 환경 변수로 nginx 파드에 마운트하기*__

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
    env:
    - name: USERNAME
      valueFrom:
        secretKeyRef:
          name: mysecret2
          key: username
{% endhighlight %}

</details>
<p></p>

__*secret-ops 네임스페이스에 ext-service-secret이라는 이름의 Secret을 생성하기. 그런 다음, API_KEY=LmLHbYhsgWZwNifiqaRorH8T 키-값 쌍을 리터럴(literal)로 제공하기*__

<details><summary>보기</summary>

{% highlight bash %}
kubectl create ns secret-ops
kubectl create secret generic ext-service-secret --from-literal=API_KEY=LmLHbYhsgWZwNifiqaRorH8T -n secret-ops
{% endhighlight %}

</details>
<p></p>

__*secret-ops 네임스페이스에 nginx 이미지를 사용하는 consumer라는 이름의 Pod를 생성하고, 해당 Pod에서 Secret을 환경 변수로 사용하기*__

<details><summary>보기</summary>

{% highlight bash %}
apiVersion: v1
kind: Pod
metadata:
  name: consumer
  namespace: secret-ops
spec:
  containers:
  - name: consumer
    image: nginx
    envFrom:
    - secretRef:
        name: ext-service-secret
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
