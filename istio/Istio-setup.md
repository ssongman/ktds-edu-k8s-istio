#  < Istio Setup >

Helm 을 이용하여 Istio를 Setup 하며 Sample App Sidecar Inject 하고 모니터링 tool 을 Setup 한다.



# 1. Istio 설치 with helm



## 1)  helm repo add

```sh
# root 권한으로
$ helm repo add istio https://istio-release.storage.googleapis.com/charts

# 추가된 repo 목록 확인
$ helm repo list
NAME    URL
bitnami https://charts.bitnami.com/bitnami
istio   https://istio-release.storage.googleapis.com/charts


# remote repo 로 부터 유효한 chart update 
$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "istio" chart repository
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈

# 약 10초 정도 소요됨



# chart 검색
$ helm search repo istio
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/wavefront-adapter-for-istio     2.0.6           0.1.5           DEPRECATED Wavefront Adapter for Istio is an ad...
istio/istiod                            1.17.2          1.17.2          Helm chart for istio control plane
istio/base                              1.17.2          1.17.2          Helm chart for deploying Istio cluster resource...
istio/cni                               1.17.2          1.17.2          Helm chart for istio-cni components
istio/gateway                           1.17.2          1.17.2          Helm chart for deploying Istio gateways
---
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/wavefront-adapter-for-istio     2.0.6           0.1.5           DEPRECATED Wavefront Adapter for Istio is an ad...
istio/istiod                            1.20.2          1.20.2          Helm chart for istio control plane
istio/base                              1.20.2          1.20.2          Helm chart for deploying Istio cluster resource...
istio/cni                               1.20.2          1.20.2          Helm chart for istio-cni components
istio/gateway                           1.20.2          1.20.2          Helm chart for deploying Istio gateways
istio/ztunnel                           1.20.2          1.20.2          Helm chart for istio ztunnel components
# 2024.02.09
---
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/wavefront-adapter-for-istio     2.0.6           0.1.5           DEPRECATED Wavefront Adapter for Istio is an ad...
istio/istiod                            1.20.3          1.20.3          Helm chart for istio control plane
istio/base                              1.20.3          1.20.3          Helm chart for deploying Istio cluster resource...
istio/cni                               1.20.3          1.20.3          Helm chart for istio-cni components
istio/gateway                           1.20.3          1.20.3          Helm chart for deploying Istio gateways
istio/ztunnel                           1.20.3          1.20.3          Helm chart for istio ztunnel components
# 2024.02.18
---
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
istio/istiod            1.27.1          1.27.1          Helm chart for istio control plane
istio/istiod-remote     1.23.6          1.23.6          Helm chart for a remote cluster using an extern...
istio/ambient           1.27.1          1.27.1          Helm umbrella chart for ambient
istio/base              1.27.1          1.27.1          Helm chart for deploying Istio cluster resource...
istio/cni               1.27.1          1.27.1          Helm chart for istio-cni components
istio/gateway           1.27.1          1.27.1          Helm chart for deploying Istio gateways
istio/ztunnel           1.27.1          1.27.1          Helm chart for istio ztunnel components
# 2025.09.20

```



## 2) create ns

```sh
# create ns
$ kubectl create namespace istio-system
```



## 3) install istio crd 설치

```sh
# istio-base 설치
$ helm -n istio-system install istio-base istio/base

NAME: istio-base
LAST DEPLOYED: Sat Sep 20 12:53:35 2025
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Istio base successfully installed!


To learn more about the release, try:
  $ helm status istio-base
  $ helm get all istio-base




$ helm -n istio-system ls

NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
istio-base      istio-system    1               2025-09-20 12:53:35.324444407 +0000 UTC deployed        base-1.27.1     1.27.1


# helm 확인
$ helm -n istio-system status istio-base
$ helm -n istio-system get all istio-base


# CRD 확인(istio 를 포함한 각종 CRD 를 확인할 수 있다.)
$ kubectl get crd | grep istio
NAME                                       CREATED AT
authorizationpolicies.security.istio.io     2025-09-20T12:53:36Z
destinationrules.networking.istio.io        2025-09-20T12:53:36Z
envoyfilters.networking.istio.io            2025-09-20T12:53:36Z
gateways.networking.istio.io                2025-09-20T12:53:36Z
peerauthentications.security.istio.io       2025-09-20T12:53:36Z
proxyconfigs.networking.istio.io            2025-09-20T12:53:36Z
requestauthentications.security.istio.io    2025-09-20T12:53:36Z
serviceentries.networking.istio.io          2025-09-20T12:53:36Z
sidecars.networking.istio.io                2025-09-20T12:53:36Z
telemetries.telemetry.istio.io              2025-09-20T12:53:36Z
virtualservices.networking.istio.io         2025-09-20T12:53:36Z
wasmplugins.extensions.istio.io             2025-09-20T12:53:36Z
workloadentries.networking.istio.io         2025-09-20T12:53:36Z
workloadgroups.networking.istio.io          2025-09-20T12:53:36Z


# istio 관련crd 가 존재한다면 정상 설치 된 것이다.

```



## [참고] repo 설치실패시

chart 를 fetch 받아서 local 에서 수행한다.

```sh
# chart 를 fetch 받아서 local 에서 수행한다.
$ mkdir ~/istio

$ cd ~/istio

$ helm fetch istio/base

$ ls -ltr
-rw-r--r--  1 song song 50197 Jun 11 18:36 base-1.14.1.tgz

$ tar -xzvf base-1.14.1.tgz

# local chart로 직접 설치진행
$ helm -n istio-system install istio-base ~/istio/base

$ helm -n istio-system ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
istio-base      istio-system    1               2022-06-11 18:42:06.172184 +0900 KST    deployed        base-1.14.1     1.14.1

```





## 4) install istiod

Controle Plane역할을 수행하는 istiod 를 설치하자.

```sh
# istiod 설치
$ helm -n istio-system install istio-istiod istio/istiod
...
TEST SUITE: None
NOTES:
"istio-istiod" successfully installed!            <--- 이런 로그가 나오면 성공

## 확인
$ helm -n istio-system status istio-istiod
$ helm -n istio-system get all istio-istiod



## 확인
$ kubectl -n istio-system get all
NAME                          READY   STATUS              RESTARTS   AGE
pod/istiod-868755b6bf-hq6x7   0/1     ContainerCreating   0          5s

NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/istiod   ClusterIP   10.43.196.221   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   5s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istiod   0/1     1            0           5s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/istiod-868755b6bf   1         1         0       5s

NAME                                         REFERENCE           TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   cpu: <unknown>/80%   1         5         0          5s


```

## [참고] repo 설치실패시

```sh
# chart 를 fetch 받아서 local 에서 수행한다.
$ mkdir ~/istio

$ cd ~/istio

$ helm fetch istio/istiod

$ ls -ltr
-rw-r--r--  1 song song 50197 Jun 11 18:36 base-1.14.1.tgz
-rw-r--r--  1 song song 36974 Jun 11 18:48 istiod-1.14.1.tgz

$ tar -xzvf istiod-1.14.1.tgz

# local chart로 직접 설치진행
$ helm -n istio-system install istio-istiod ~/istio/istiod


$ helm -n istio-system ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
istio-base      istio-system    1               2022-06-11 18:42:06.172184 +0900 KST    deployed        base-1.14.1     1.14.1
istio-istiod    istio-system    1               2022-06-11 18:50:18.0303375 +0900 KST   deployed        istiod-1.14.1   1.14.1

```





## 5) install istio-ingressgateway

참조링크 : https://istio.io/latest/docs/setup/additional-setup/gateway/



```sh
$ kubectl create namespace istio-ingress

$ helm -n istio-ingress install istio-ingressgateway istio/gateway


$ kubectl -n istio-ingress get all
NAME                                        READY   STATUS              RESTARTS   AGE
pod/istio-ingressgateway-5b86b655cc-27qxz   0/1     ContainerCreating   0          4s

NAME                           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
service/istio-ingressgateway   LoadBalancer   10.43.120.76   <pending>     15021:31595/TCP,80:31287/TCP,443:30369/TCP   4s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-ingressgateway   0/1     1            0           4s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-ingressgateway-5b86b655cc   1         1         0       4s

NAME                                                       REFERENCE                         TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   cpu: <unknown>/80%   1         5         0          4s



# istio version 1.14 에서 현재 1.17.2로 버전업되면서 DaemonSet 이 사라졌다.
# traefik 과 충돌나는 현상도 사라졌다.


$ kubectl -n istio-ingress get svc
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.165.9   <pending>     15021:30613/TCP,80:31166/TCP,443:32560/TCP   90s
---
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.78.43   <pending>     15021:32086/TCP,80:32190/TCP,443:30919/TCP   27s
---
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.33.106   <pending>     15021:32118/TCP,80:32217/TCP,443:31768/TCP   62s
---
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.120.76   <pending>     15021:31595/TCP,80:31287/TCP,443:30369/TCP   34s



32190 / 30919 nodeport 로 접근 가능
32217 / 31768 nodeport 로 접근 가능
31287 / 30369 nodeport 로 접근 가능


```





## 6) clean up

모든 테스트를 마치고 istio를 최종 삭제할때 아래 명령으로 삭제 하자.

```sh
# 확인
$ helm -n istio-ingress list
$ helm -n istio-system list


# 삭제
$ helm -n istio-ingress delete istio-ingressgateway
$ helm -n istio-system delete istio-istiod
$ helm -n istio-system delete istio-base


# namespace 삭제
$ kubectl delete namespace istio-ingress
$ kubectl delete namespace istio-system
```



# 2. sample app sidecar inject



## 1) userlist pod 실행

1교시 kubernetes 실습때 수행했던 userlist pod 를 다시 확인해 보자.

```sh


# userlist pod 확인
$ kubectl -n yjsong get pod
NAME                        READY   STATUS    RESTARTS   AGE
curltest                    1/1     Running   0          4h10m
userlist-858d9c87d5-ncbzp   1/1     Running   0          4h49m



# 존재하지 않는다면 아래명령으로 실행
$ kubectl -n yjsong create deploy userlist --image=ssongman/userlist:v1
```



위 userlist에 sidecar 를 inject 해보자.

특정 pod 에 sidecar 를 inject 시키는 방식은 총 3가지가 존재한다.

1. namespace label 추가 방식
2. deployment annotations 방식
3. istioctl kube-inject 방식



우리는 첫번째 방식을 이용해 보자.



## 2) sidecar inject 설정

적용하기전에 예시를 살펴보자.

```sh
# 적용하는 방법
# [적용예시]  kubectl label namespace <namespace> istio-injection=enabled
```



- 적용 확인

```sh
# 적용전 확인
$ kubectl -n yjsong -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2025-09-20T08:04:55Z"
  labels:                                     <-- labels 확인(istio label 이 없다.)
    kubernetes.io/metadata.name: yjsong
  name: yjsong
  resourceVersion: "7565"
  uid: 86b97931-0103-4a68-a78b-19d51c9c0256
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
  
  
# 적용(label 추가)
# 자기 Namespce 로 변경하여 적용하자.
$ kubectl label namespace yjsong istio-injection=enabled
namespace/yjsong labeled


# 적용후 확인
$ kubectl -n yjsong -o yaml
ktdsedu@ke-bastion01:~$ kubectl -n yjsong -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2025-09-20T08:04:55Z"
  labels:
    istio-injection: enabled                    <-- istio label 이 잘 추가되었다.
    kubernetes.io/metadata.name: yjsong
  name: yjsong
  resourceVersion: "71380"
  uid: 86b97931-0103-4a68-a78b-19d51c9c0256
spec:
  finalizers:
  - kubernetes
status:
  phase: Active


```





## 3) pod 적용을 위한 재기동

적용을 희망하는 pod 를 재기동시킨다.  

재기동은 pod 를 삭제하게 되면 새롭게 running 된다.

```sh
# 확인
$ kubectl -n yjsong get pod
NAME                        READY   STATUS    RESTARTS   AGE
curltest                    1/1     Running   0          4h13m
userlist-858d9c87d5-ncbzp   1/1     Running   0          4h52m



# userlist pod 재기동
$ kubectl -n yjsong rollout restart deploy/userlist
deployment.apps/userlist restarted

## gracefule 방식으로 삭제되므로 시간이 어느정도 소요됨.

# 확인
$ kubectl -n yjsong get pod
NAME                        READY   STATUS        RESTARTS   AGE
curltest                    1/1     Running       0          4h13m
userlist-766c9b8874-xh7rg   2/2     Running       0          25s
userlist-858d9c87d5-ncbzp   1/1     Terminating   0          4h53m



# istio sidecar 가 포함되어 2개의 container 가 되었다.


# describe 로 확인
$ kubectl -n yjsong describe pod userlist-766c9b8874-xh7rg
...
Init Containers:
  istio-init:
  istio-proxy:
Containers:
  userlist:
...



```

- istio-proxy 컨테이너는 **Pod 라이프사이클 내내 Running** 상태로 유지
- 그러므로 Containers 내부에 userlist 와 istio-proxy 2개가 존재하게 된다.



## 4) clean up

```sh
# Userlit 삭제시...
$ kubectl -n yjsong delete deploy userlist

# label 삭제시...
$ kubectl label --overwrite namespace yjsong istio-injection-
```





# 3. monitoring 설치



## 1) prometheus 설치

참조링크: https://istio.io/latest/docs/ops/integrations/prometheus/

아래 script로 클러스터에 Prometheus가 배포된다. 

```sh
# prometheus 설치
# 반드시 istio 버젼을 확인후 동일한 버젼의 prometheus 를 설치하자.

$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/addons/prometheus.yaml
ons/prometheus.yaml
serviceaccount/prometheus created
configmap/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created
service/prometheus created
deployment.apps/prometheus created



# 설치 확인
$ kubectl -n istio-system get pod -l app.kubernetes.io/name=prometheus
NAME                         READY   STATUS    RESTARTS   AGE
prometheus-7bf56b6bc-xp8rx   2/2     Running   0          2m1s

NAME                             READY   STATUS    RESTARTS   AGE
pod/istiod-868755b6bf-hq6x7      1/1     Running   0          11m
pod/prometheus-7bf56b6bc-xp8rx   2/2     Running   0          2m15s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/istiod       ClusterIP   10.43.196.221   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   11m
service/prometheus   ClusterIP   10.43.103.223   <none>        9090/TCP                                2m15s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istiod       1/1     1            1           11m
deployment.apps/prometheus   1/1     1            1           2m15s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/istiod-868755b6bf      1         1         1       11m
replicaset.apps/prometheus-7bf56b6bc   1         1         1       2m15s

NAME                                         REFERENCE           TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   cpu: 0%/80%   1         5         1          11m

```



- ingress 

```sh

$ cat <<EOF | kubectl -n istio-system apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: prometheus-ingress
  labels:
    app: prometheus
spec:
  ingressClassName: traefik
  rules:
  - host: "prometheus.istio-system.cloud.20.249.162.253.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: prometheus
            port:
              number: 9090
EOF

$ kubectl -n istio-system get ingress -l app=prometheus
NAME                 CLASS     HOSTS                                                 ADDRESS                                                  PORTS   AGE
prometheus-ingress   traefik   prometheus.istio-system.cloud.20.249.162.253.nip.io   172.16.0.5,172.16.0.6,172.16.0.7,172.16.0.8,172.16.0.9   80      72s


```





## 2) grafana 

참조링크 : https://istio.io/latest/docs/ops/integrations/grafana/

```sh
# grafana 설치
# 반드시 istio 버젼을 확인후 동일한 버젼의 prometheus 를 설치하자.

$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/addons/grafana.yaml
ons/grafana.yaml
serviceaccount/grafana created
configmap/grafana created
service/grafana created
deployment.apps/grafana created
configmap/istio-grafana-dashboards created
configmap/istio-services-grafana-dashboards created



$ kubectl -n istio-system get pod -l app.kubernetes.io/name=grafana
NAME                      READY   STATUS    RESTARTS   AGE
grafana-cdb9db549-w2fjt   0/1     Running   0          26s



$ kubectl -n istio-system get all

NAME                             READY   STATUS    RESTARTS   AGE
pod/grafana-cdb9db549-w2fjt      1/1     Running   0          45s
pod/istiod-868755b6bf-hq6x7      1/1     Running   0          17m
pod/prometheus-7bf56b6bc-xp8rx   2/2     Running   0          7m58s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/grafana      ClusterIP   10.43.54.5      <none>        3000/TCP                                46s
service/istiod       ClusterIP   10.43.196.221   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   17m
service/prometheus   ClusterIP   10.43.103.223   <none>        9090/TCP                                7m58s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana      1/1     1            1           45s
deployment.apps/istiod       1/1     1            1           17m
deployment.apps/prometheus   1/1     1            1           7m58s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-cdb9db549      1         1         1       45s
replicaset.apps/istiod-868755b6bf      1         1         1       17m
replicaset.apps/prometheus-7bf56b6bc   1         1         1       7m58s

NAME                                         REFERENCE           TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   cpu: 1%/80%   1         5         1          17m

```



- ingress 

```sh

$ cat <<EOF | kubectl -n istio-system apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: grafana-ingress
  labels:
    app: grafana
spec:
  ingressClassName: traefik
  rules:
  - host: "grafana.istio-system.cloud.20.249.162.253.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: grafana
            port:
              number: 3000
EOF

$ ki apply -f ./istio/monitoring/11.grafana-ingress-cloud.yaml

$ ki get ingress -l app=grafana
NAME              CLASS     HOSTS                                            ADDRESS                                   PORTS   AGE
grafana-ingress   traefik   grafana.istio-system.cloud.20.249.162.253.nip.io   172.31.13.98,172.31.14.177,172.31.8.197   80      5s


```





## 3) kiali 설치

참조링크: https://istio.io/latest/docs/ops/integrations/kiali/

```sh
# 설치
$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/addons/kiali.yaml
ons/kiali.yaml
serviceaccount/kiali created
configmap/kiali created
clusterrole.rbac.authorization.k8s.io/kiali created
clusterrolebinding.rbac.authorization.k8s.io/kiali created
service/kiali created
deployment.apps/kiali created


$ kubectl -n istio-system get pod -l app=kiali
NAME                     READY   STATUS    RESTARTS   AGE
kiali-56f54f58f9-cgjgl   0/1     Running   0          20s


$ kubectl -n istio-system get svc kiali
NAME    TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)              AGE
kiali   ClusterIP   10.43.9.37   <none>        20001/TCP,9090/TCP   38s

```



- ingress 

```sh

$ cat <<EOF | kubectl -n istio-system apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kiali-ingress
  labels:
    app: kiali
spec:
  ingressClassName: traefik
  rules:
  - host: "kiali.istio-system.cloud.20.249.162.253.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kiali
            port:
              number: 20001
EOF

$ kubectl -n istio-system get ingress -l app=kiali
NAME            CLASS     HOSTS                                            ADDRESS                                                  PORTS   AGE
kiali-ingress   traefik   kiali.istio-system.cloud.20.249.162.253.nip.io   172.16.0.5,172.16.0.6,172.16.0.7,172.16.0.8,172.16.0.9   80      14s


```





## 4) jaeger 설치

참조링크: https://istio.io/latest/docs/ops/integrations/jaeger/

```sh
$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/addons/jaeger.yaml
ons/jaeger.yaml
deployment.apps/jaeger created
service/tracing created
service/zipkin created
service/jaeger-collector created

$ kubectl -n istio-system get pod -l app=jaeger
NAME                      READY   STATUS    RESTARTS   AGE
jaeger-84b9c75d5f-4m7qc   1/1     Running   0          11s


$ kubectl -n istio-system get svc -l app=jaeger

NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                                          AGE
jaeger-collector   ClusterIP   10.43.8.58     <none>        14268/TCP,14250/TCP,9411/TCP,4317/TCP,4318/TCP   21s
tracing            ClusterIP   10.43.72.128   <none>        80/TCP,16685/TCP                                 22s

```



- ingress 

```sh

$ cat <<EOF | kubectl -n istio-system apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: jaeger-ingress
  labels:
    app: jaeger
spec:
  ingressClassName: traefik
  rules:
  - host: "jaeger.istio-system.cloud.20.249.162.253.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: tracing
            port:
              number: 80
EOF

$ kubectl -n istio-system get ingress -l app=jaeger

NAME             CLASS     HOSTS                                             ADDRESS                                                  PORTS   AGE
jaeger-ingress   traefik   jaeger.istio-system.cloud.20.249.162.253.nip.io   172.16.0.5,172.16.0.6,172.16.0.7,172.16.0.8,172.16.0.9   80      21s


```



## 5) test

```sh

$ while true; do curl http://userlist.yjsong.cloud.20.249.162.253.nip.io/users/1; sleep 1; echo; done;


```







## 6) clean up

```sh

# ingress 삭제
$ kubectl -n istio-system delete ingress prometheus-ingress
  kubectl -n istio-system delete ingress grafana-ingress
  kubectl -n istio-system delete ingress kiali-ingress
  kubectl -n istio-system delete ingress jaeger-ingress

 

# uninstall
$ kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/prometheus.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/grafana.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/kiali.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/jaeger.yaml

```

