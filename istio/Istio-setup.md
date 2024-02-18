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
LAST DEPLOYED: Fri Feb  9 13:40:30 2024
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
istio-base      istio-system    1               2023-05-14 13:48:21.21648216 +0900 KST  deployed        base-1.17.2     1.17.2

NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
istio-base      istio-system    1               2024-02-09 13:40:30.255845915 +0000 UTC deployed        base-1.20.2     1.20.2


# helm 확인
$ helm -n istio-system status istio-base
$ helm -n istio-system get all istio-base


# CRD 확인(istio 를 포함한 각종 CRD 를 확인할 수 있다.)
$ kubectl get crd
NAME                                       CREATED AT
addons.k3s.cattle.io                       2024-02-18T02:00:52Z
authorizationpolicies.security.istio.io    2024-02-18T02:43:19Z
destinationrules.networking.istio.io       2024-02-18T02:43:19Z
envoyfilters.networking.istio.io           2024-02-18T02:43:19Z
etcdsnapshotfiles.k3s.cattle.io            2024-02-18T02:00:52Z
gateways.networking.istio.io               2024-02-18T02:43:19Z
helmchartconfigs.helm.cattle.io            2024-02-18T02:00:52Z
helmcharts.helm.cattle.io                  2024-02-18T02:00:52Z
ingressroutes.traefik.containo.us          2024-02-18T02:01:23Z
ingressroutes.traefik.io                   2024-02-18T02:01:23Z
ingressroutetcps.traefik.containo.us       2024-02-18T02:01:23Z
ingressroutetcps.traefik.io                2024-02-18T02:01:23Z
ingressrouteudps.traefik.containo.us       2024-02-18T02:01:23Z
ingressrouteudps.traefik.io                2024-02-18T02:01:23Z
istiooperators.install.istio.io            2024-02-18T02:43:19Z
middlewares.traefik.containo.us            2024-02-18T02:01:23Z
middlewares.traefik.io                     2024-02-18T02:01:23Z
middlewaretcps.traefik.containo.us         2024-02-18T02:01:23Z
middlewaretcps.traefik.io                  2024-02-18T02:01:23Z
peerauthentications.security.istio.io      2024-02-18T02:43:19Z
proxyconfigs.networking.istio.io           2024-02-18T02:43:19Z
requestauthentications.security.istio.io   2024-02-18T02:43:19Z
serverstransports.traefik.containo.us      2024-02-18T02:01:23Z
serverstransports.traefik.io               2024-02-18T02:01:23Z
serverstransporttcps.traefik.io            2024-02-18T02:01:23Z
serviceentries.networking.istio.io         2024-02-18T02:43:19Z
sidecars.networking.istio.io               2024-02-18T02:43:19Z
telemetries.telemetry.istio.io             2024-02-18T02:43:19Z
tlsoptions.traefik.containo.us             2024-02-18T02:01:23Z
tlsoptions.traefik.io                      2024-02-18T02:01:23Z
tlsstores.traefik.containo.us              2024-02-18T02:01:23Z
tlsstores.traefik.io                       2024-02-18T02:01:23Z
traefikservices.traefik.containo.us        2024-02-18T02:01:23Z
traefikservices.traefik.io                 2024-02-18T02:01:23Z
virtualservices.networking.istio.io        2024-02-18T02:43:19Z
wasmplugins.extensions.istio.io            2024-02-18T02:43:19Z
workloadentries.networking.istio.io        2024-02-18T02:43:19Z
workloadgroups.networking.istio.io         2024-02-18T02:43:19Z



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
NAME                          READY   STATUS    RESTARTS   AGE
pod/istiod-5d95465974-t9cxx   1/1     Running   0          47s

NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                                 AGE
service/istiod   ClusterIP   10.43.82.71   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   47s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istiod   1/1     1            1           47s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/istiod-5d95465974   1         1         1       47s

NAME                                         REFERENCE           TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   0%/80%    1         5         1          47s


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
NAME                                       READY   STATUS    RESTARTS   AGE
pod/istio-ingressgateway-9cc99c9db-czkc8   1/1     Running   0          14s

NAME                           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
service/istio-ingressgateway   LoadBalancer   10.43.33.106   <pending>     15021:32118/TCP,80:32217/TCP,443:31768/TCP   15s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-ingressgateway   1/1     1            1           15s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-ingressgateway-9cc99c9db   1         1         1       15s

NAME                                                       REFERENCE                         TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   <unknown>/80%   1         5         0          15s



# istio version 1.14 에서 현재 1.17.2로 버전업되면서 DaemonSet 이 사라졌다.
# traefik 과 충돌나는 현상도 사라졌다.


$ alias kii='kubectl -n istio-ingress'


$ kii get svc
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.165.9   <pending>     15021:30613/TCP,80:31166/TCP,443:32560/TCP   90s
---
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.78.43   <pending>     15021:32086/TCP,80:32190/TCP,443:30919/TCP   27s
---
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.33.106   <pending>     15021:32118/TCP,80:32217/TCP,443:31768/TCP   62s



32190 / 30919 nodeport 로 접근 가능
32217 / 31768 nodeport 로 접근 가능


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
$ alias ku='kubectl -n yjsong'

# userlist pod 확인
$ ku get pod
NAME                        READY   STATUS    RESTARTS   AGE
userlist-75c7d7dfd7-7tf7g   1/1     Running   0          6s


# 존재하지 않는다면 아래명령으로 실행
$ ku create deploy userlist --image=ssongman/userlist:v1
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
$ kubectl get ns yjsong -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2023-05-13T18:48:19Z"
  labels:                                     <-- labels 확인(istio label 이 없다.)
    kubernetes.io/metadata.name: yjsong
  name: yjsong
  resourceVersion: "771"
  uid: df181e8d-5c13-411f-8abd-29c73961cb1a
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
$ kubectl get ns yjsong -o yaml

apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2024-02-09T12:29:41Z"
  labels:
    istio-injection: enabled                    <-- istio label 이 잘 추가되었다.
    kubernetes.io/metadata.name: yjsong
  name: yjsong
  resourceVersion: "18477"
  uid: 0b68ba76-c5ba-41c4-8b95-2ee9ae4f6bd1
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
$ ku get pod
NAME                       READY   STATUS    RESTARTS        AGE
curltest                   1/1     Running   11 (4d1h ago)   97d
userlist-bfd857685-g9hsg   1/1     Running   11 (4d1h ago)   97d




# pod 재기동(삭제)
$ ku delete pod userlist-bfd857685-g9hsg
pod "userlist-6bfcd9456d-5rjmc" deleted

## gracefule 방식으로 삭제되므로 시간이 어느정도 소요됨.

# 확인
$ ku get pod
NAME                       READY   STATUS    RESTARTS        AGE
curltest                   1/1     Running   11 (4d1h ago)   97d
userlist-bfd857685-p9bzr   2/2     Running   0               33s


# istio sidecar 가 포함되어 2개의 container 가 되었다.


# describe 로 확인
$ ku describe pod userlist-bfd857685-p9bzr
...
Containers:
  userlist:
    Container ID:   containerd://ad5eb2796057a1323c8453a224050122aa7097ec7ad2883244c6f6925c86bb55
    Image:          ssongman/userlist:v1
    Image ID:       docker.io/ssongman/userlist@sha256:74f32a7b4bab2c77bf98f2717fed49e034756d541e536316bba151e5830df0dc
    Port:           8181/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 26 Aug 2023 02:10:58 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vdv7l (ro)
  istio-proxy:
    Container ID:  containerd://b570594e24d6e39ff3391f2218de185fcf140fe0e9d1896a7bd916c9c283d98d
    Image:         docker.io/istio/proxyv2:1.18.2
    Image ID:      docker.io/istio/proxyv2@sha256:b71f2657e038a0d6092dfd954050a2783c7887ff8e72f77ce64840c0c39b076e
    Port:          15090/TCP
    Host Port:     0/TCP
    Args:
      proxy
      sidecar
      --domain
      $(POD_NAMESPACE).svc.cluster.local
      --proxyLogLevel=warning
      --proxyComponentLogLevel=misc:error
      --log_output_level=default:info
    State:          Running
      Started:      Sat, 26 Aug 2023 02:10:58 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:      100m
      memory:   128Mi



```

위 Containers 내부에 userlist 와 istio-proxy 2개가 존재하는 것을 확인할 수 있다.



## 4) clean up

```sh
# Userlit 삭제시...
$ ku delete deploy userlist

# label 삭제시...
$ kubectl label --overwrite namespace yjsong istio-injection-
```





# 3. monitoring 설치



## 1) prometheus 설치

참조링크: https://istio.io/latest/docs/ops/integrations/prometheus/

아래 script로 클러스터에 Prometheus가 배포된다. 

```sh
# prometheus 설치
# istio 버젼을 확인후 동일한 버젼의 prometheus 를 설치하자.

$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/prometheus.yaml

serviceaccount/prometheus created
configmap/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created
service/prometheus created
deployment.apps/prometheus created



# 설치 확인
$ kubectl -n istio-system get pod -l app=prometheus
NAME                         READY   STATUS    RESTARTS   AGE
prometheus-db8b4588f-bzr2k   2/2     Running   0          34s




$ kubectl -n istio-system get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/istiod-5d95465974-t9cxx      1/1     Running   0          8m28s
pod/prometheus-db8b4588f-bzr2k   2/2     Running   0          46s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/istiod       ClusterIP   10.43.82.71     <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   8m28s
service/prometheus   ClusterIP   10.43.217.204   <none>        9090/TCP                                46s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istiod       1/1     1            1           8m28s
deployment.apps/prometheus   1/1     1            1           46s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/istiod-5d95465974      1         1         1       8m28s
replicaset.apps/prometheus-db8b4588f   1         1         1       46s

NAME                                         REFERENCE           TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   0%/80%    1         5         1          8m28s


```



- ingress 

```sh
$ cd ~/yjsong/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/monitoring/10.prometheus-ingress-cloud.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: prometheus-ingress
  labels:
    app: prometheus
spec:
  ingressClassName: traefik
  rules:
  - host: "prometheus.istio-system.cloud.43.203.62.69.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: prometheus
            port:
              number: 9090

$ ki apply -f ./istio/monitoring/10.prometheus-ingress-cloud.yaml

$ ki get ingress -l app=prometheus
NAME                 CLASS     HOSTS                                                ADDRESS                                                                 PORTS   AGE
prometheus-ingress   traefik   prometheus.istio-system.cloud.35.209.207.26.nip.io   10.128.0.35,10.128.0.36,10.128.0.38,10.128.0.39,10.208.0.2,10.208.0.3   80      13m
---
NAME                 CLASS     HOSTS                                               ADDRESS                                   PORTS   AGE
prometheus-ingress   traefik   prometheus.istio-system.cloud.43.203.62.69.nip.io   172.31.13.98,172.31.14.177,172.31.8.197   80      8s


```





## 2) grafana 

참조링크 : https://istio.io/latest/docs/ops/integrations/grafana/

```sh
$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/grafana.yaml

serviceaccount/grafana created
configmap/grafana created
service/grafana created
deployment.apps/grafana created
configmap/istio-grafana-dashboards created
configmap/istio-services-grafana-dashboards created





$ kubectl -n istio-system get pod -l app=grafana
NAME                      READY   STATUS    RESTARTS   AGE
grafana-b8bbdc84d-fbx2c   1/1     Running   0          23s




$ ki get all

NAME                             READY   STATUS    RESTARTS   AGE
pod/grafana-b8bbdc84d-fbx2c      1/1     Running   0          32s
pod/istiod-5d95465974-t9cxx      1/1     Running   0          20m
pod/prometheus-db8b4588f-bzr2k   2/2     Running   0          12m

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/grafana      ClusterIP   10.43.35.6      <none>        3000/TCP                                32s
service/istiod       ClusterIP   10.43.82.71     <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   20m
service/prometheus   ClusterIP   10.43.217.204   <none>        9090/TCP                                12m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana      1/1     1            1           32s
deployment.apps/istiod       1/1     1            1           20m
deployment.apps/prometheus   1/1     1            1           12m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-b8bbdc84d      1         1         1       32s
replicaset.apps/istiod-5d95465974      1         1         1       20m
replicaset.apps/prometheus-db8b4588f   1         1         1       12m

NAME                                         REFERENCE           TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   0%/80%    1         5         1          20m


```



- ingress 

```sh
$ cd ~/yjsong/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/monitoring/11.grafana-ingress-cloud.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: grafana-ingress
  labels:
    app: grafana
spec:
  ingressClassName: traefik
  rules:
  - host: "grafana.istio-system.cloud.43.203.62.69.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: grafana
            port:
              number: 3000


$ ki apply -f ./istio/monitoring/11.grafana-ingress-cloud.yaml

$ ki get ingress -l app=grafana
NAME              CLASS     HOSTS                                            ADDRESS                                   PORTS   AGE
grafana-ingress   traefik   grafana.istio-system.cloud.43.203.62.69.nip.io   172.31.13.98,172.31.14.177,172.31.8.197   80      5s


```





## 3) kiali 설치

참조링크: https://istio.io/latest/docs/ops/integrations/kiali/

```sh
# 설치
$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/kiali.yaml

serviceaccount/kiali created
configmap/kiali created
clusterrole.rbac.authorization.k8s.io/kiali-viewer created
clusterrole.rbac.authorization.k8s.io/kiali created
clusterrolebinding.rbac.authorization.k8s.io/kiali created
role.rbac.authorization.k8s.io/kiali-controlplane created
rolebinding.rbac.authorization.k8s.io/kiali-controlplane created
service/kiali created
deployment.apps/kiali created


$ kubectl -n istio-system get pod -l app=kiali
NAME                     READY   STATUS    RESTARTS   AGE
kiali-545878ddbb-wknm4   0/1     Running   0          15s



$ kubectl -n istio-system get svc kiali
NAME    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)              AGE
kiali   ClusterIP   10.43.204.82   <none>        20001/TCP,9090/TCP   24s


```



- ingress 

```sh
$ cd ~/yjsong/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/monitoring/12.kiali-ingress-cloud.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kiali-ingress
  labels:
    app: kiali
spec:
  ingressClassName: traefik
  rules:
  - host: "kiali.istio-system.cloud.43.203.62.69.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kiali
            port:
              number: 20001




$ ki apply -f ./istio/monitoring/12.kiali-ingress-cloud.yaml


$ ki get ingress -l app=kiali

NAME            CLASS     HOSTS                                          ADDRESS                                   PORTS   AGE
kiali-ingress   traefik   kiali.istio-system.cloud.43.203.62.69.nip.io   172.31.13.98,172.31.14.177,172.31.8.197   80      4s

```





## 4) jaeger 설치

참조링크: https://istio.io/latest/docs/ops/integrations/jaeger/

```sh
$ kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/jaeger.yaml

deployment.apps/jaeger created
service/tracing created
service/zipkin created
service/jaeger-collector created



$ kubectl -n istio-system get pod -l app=jaeger
NAME                     READY   STATUS    RESTARTS   AGE
jaeger-cc4688b98-kd45r   1/1     Running   0          33s



$ kubectl -n istio-system get svc -l app=jaeger

NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                                          AGE
jaeger-collector   ClusterIP   10.43.118.68   <none>        14268/TCP,14250/TCP,9411/TCP,4317/TCP,4318/TCP   14s
tracing            ClusterIP   10.43.23.78    <none>        80/TCP,16685/TCP                                 14s


```



- ingress 

```sh
$ cd ~/yjsong/githubrepo/ktds-edu-k8s-istio


$ cat ./istio/monitoring/13.jaeger-ingress-cloud.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: jaeger-ingress
  labels:
    app: jaeger
spec:
  ingressClassName: traefik
  rules:
  - host: "jaeger.istio-system.cloud.43.203.62.69.nip.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: tracing
            port:
              number: 80



$ ki apply -f ./istio/monitoring/13.jaeger-ingress-cloud.yaml

$ ki get ingress -l app=jaeger

NAME             CLASS     HOSTS                                           ADDRESS                                   PORTS   AGE
jaeger-ingress   traefik   jaeger.istio-system.cloud.43.203.62.69.nip.io   172.31.13.98,172.31.14.177,172.31.8.197   80      3s


```



## 5) test

```sh
$ while true; do curl http://userlist.yjsong.cloud.43.203.62.69.nip.io/users/1; sleep 1; echo; done;

```







## 6) clean up

```sh
$ cd ~/yjsong/githubrepo/ktds-edu-k8s-istio

# ingress 삭제
$ ki delete -f ./istio/monitoring/10.prometheus-ingress-cloud.yaml
  ki delete -f ./istio/monitoring/11.grafana-ingress-cloud.yaml
  ki delete -f ./istio/monitoring/12.kiali-ingress-cloud.yaml
  ki delete -f ./istio/monitoring/13.jaeger-ingress-cloud.yaml


# uninstall
$ kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/prometheus.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/grafana.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/kiali.yaml
  kubectl -n istio-system delete -f https://raw.githubusercontent.com/istio/istio/release-1.20/samples/addons/jaeger.yaml

```

