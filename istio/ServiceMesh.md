# < 2교시: Service Mesh >





# 1. Service Mesh

## 1) 서비스메쉬란?

- 서비스 메시는 서비스간의 통신을 제어하고 관리하는 방식임
- 서비스간 호출은 인프라계층의 proxy 를 통해 이루어짐
- 서비스 메시를 구성하는 개별 proxy는 서비스 내부가 아니라 각 서비스와 함께 실행되므로 sidecar 라고 부름
- 각 서비스에 inject 된 이러한 sidecar proxy 들이 모여 서 Mesh Network를 형성



![img](ServiceMesh.assets/0XnD9vnWjjQSpAe5I.png)



## 2) 서비스메쉬 특징

- 서비스 메시는 InfraStructure Layer에 존재함
- 서비스간 통신의 추상화를 통해 복잡한 내부 네트워크를 제어, 추적, 관리함
- 서비스 메시의 구현체인 경량화 Proxy를 통해 다양한 Routing Rules, circuit breaker 등의 기능을 설정할 수 있음
- A/B 테스트, 카나리아 배포, 속도 제한, 액세스 제어, 암호화 및 종단 간 인증과 같은 보다 복잡한 운영 요구 사항도 해결할 수 있음



## 3) 서비스 메쉬 주요기능

- 트래픽 라우팅 제어
- 서킷브레이커
- 로드발란싱(부하분산알고리즘)
- 보안기능(TLS, 암호화, 인증 및 권한 부여)
- 서비스간 계층에서 metric 제공(kiali, jeager, prometheus 를 통해  모니터링 제공)





# 2. Istio

서비스메시를 구현할 수 여러 구현체는 Istio, linked, conduit, AWS App Mesh 등이 존재한다. 그중 Istio 에 대해서 알아보자.



## 1) Istio 란?

Istio는 기존 분산 애플리케이션에 투명하게 계층화되는 오픈 소스 서비스 메시이다.  Istio는 간단한 설정만으로 서비스를 보호, 연결 및 모니터링하는 강력한 기능을 제공한다. 

Istio는 서비스 코드 변경이 거의 또는 전혀 없이 로드 밸런싱, 서비스 간 인증 및 모니터링 기능을 제공한다. 

강력한 컨트롤 플레인은 다음과 같은 중요한 기능을 제공한다.

- TLS 암호화, 강력한 ID 기반 인증 및 권한 부여를 통해 클러스터에서 안전한 서비스 간 통신
- HTTP, gRPC, WebSocket 및 TCP 트래픽에 대한 자동 로드 밸런싱
- Routing rule, retry policy, failovers 및 복원력 테스트를위한 Fault Injection 등을 통해 트래픽 동작을 세밀하게 제어
- 액세스 제어, 속도 제한 및 할당량을 지원하는 플러그형 정책 레이어 및 구성 API
- 클러스터 내의 모든 트래픽에 대한 메트릭을 확인 및 추적 가능



## 2) istio 사용전후

- Istio 사용전

![service-mesh-before](ServiceMesh.assets/service-mesh-before.svg)



- Istio 사용후

![서비스 메시](ServiceMesh.assets/service-mesh.svg)






## 3) Istio 주요 기능

참조링크: https://istio.io/latest/docs/tasks/



### (1) Traffic management

* Istio의 트래픽 라우팅 규칙을 사용하여 서비스 간의 트래픽 흐름 및 API 호출을 쉽게 제어할 수 있다.
* Istio는 circuit breakers, timeouts, and retries 와 같은 서비스 수준의 속성을 쉽게 구성할 수 있다.
* A/B testing, canary 배포 및 백분율 기반 트래픽 분할을 통한 단계적 출시와 같은 중요한 작업을 쉽게 설정할 수 있다.



### (2) Observability

* Istio는 서비스 메시 내의 모든 통신에 대한 상세한 원격 메트릭을 생성한다.
* 이 원격 메트릭은 서비스 동작의 상태 값을 제공하여 운영자가 문제를 해결하고 애플리케이션을 유지 관리하고 최적화할 수 있도록 한다. 
* Istio를 사용하면 전체적인 서비스 메시 Observability 이점을 을 얻을 수 있다.



### (3) Security capabilities

* Istio에는 감사 도구 및 상호 TLS를 비롯한 보안 요구사항을 해결할 수 있는 포괄적인 보안 솔루션이 포함되어 있다. 
* 강력한 정책, 투명한 TLS 암호화, AAA(인증, 권한 부여 및 감사) 도구를 제공하여 서비스와 데이터를 보호한다.
* Istio의 보안 모델은 기본 보안을 기반으로 하며, 신뢰할 수 없는 네트워크에서도 보안 중심 애플리케이션을 배포할 수 있도록 심층 방어를 제공하는 것을 목표로 한다.







# 3. [개인Cluster] 실습

개인 VM 환경에서 istio 를 설치해 보자.





## 1) 개인 VM 접속 설정 - ★★★

개인 VM Cluster에 접속할 수 있도록 설정 한다.

```sh

# ktdsEduCluster 접속하도록 설정 변경
$ export KUBECONFIG="${HOME}/.kube/config"

# Cluste 확인
$ kubectl get nodes
NAME        STATUS   ROLES                  AGE   VERSION
bastion02   Ready    control-plane,master   49d   v1.26.5+k3s1

```





## 2) [참고] helm install

쿠버네티스에 서비스를 배포하는 방법이 다양하게 존재하는데 그중 대표적인 방법중에 하나가 Helm chart 방식 이다.



### (1) [참고] helm chart 와 Architecture

#### helm chart 의 필요성

일반적으로 Kubernetes 에 서비스를 배포하기 위해 준비되는 Manifest 파일은 정적인 형태이다. 따라서 데이터를 수정하기 위해선 파일 자체를 수정해야 한다.  잘 관리를 한다면 큰 어려움은 없겠지만, 문제는 CI/CD 등 자동화된 파이프라인을 구축해서 애플리케이션 라이프사이클을 관리할 때 발생한다.  

보통 애플리케이션 이미지를 새로 빌드하게 되면, 빌드 넘버가 변경된다. 이렇게 되면 새로운 이미지를 사용하기 위해 Kubernetes Manifest의 Image도 변경되어야 한다.  하지만 Kubernetes Manifest를 살펴보면, 이를 변경하기 쉽지 않다. Image Tag가 별도로 존재하지 않고 Image 이름에 붙어있기 때문입니다. 이를 자동화 파이프라인에서 변경하려면, sed 명령어를 쓰는 등의 힘든 작업을 해야 한다.

Image Tag는 굉장히 단적인 예제이다.  이 외에 도 Configmap 등 배포시마다 조금씩 다른 형태의 데이터를 배포해야 할때 Maniifest 파일 방식은 너무나 비효율적이다.  Helm Chart 는 이런 어려운 점을 모두 해결한 훌륭한 도구이다.  비단,  사용자가 개발한 AP 뿐아니라 kubernetes 에 배포되는 오픈소스 기반 솔루션들은 거의 모두 helm chart 를 제공한다.

Istio 도 마찬가지로 helm 배포를 위한 chart 를 제공해 준다.



#### Helm Architecture

![helm-architecure](ServiceMesh.assets/helm-architecure.png)



### (2) [참고] helm client download

helm client 를 local 에 설치해 보자.

개인PC 의 WSL Termimal 에서 아래 작업을 수행하자.

```sh

# root 권한으로 수행
$ sudo -s


## 임시 디렉토리를 하나 만들자.
$ mkdir -p ~/temp/helm/
  cd ~/temp/helm/

# 다운로드
$ wget https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz

# 압축해지
$ tar -zxvf helm-v3.12.0-linux-amd64.tar.gz

# 확인
$ ll linux-amd64/helm
-rwxr-xr-x 1 1001 docker 50597888 May 11 01:35 linux-amd64/helm*

# move
$ mv linux-amd64/helm /usr/local/bin/helm

# 확인
$ ll /usr/local/bin/helm*
-rwxr-xr-x 1 1001 docker 50597888 May 11 01:35 /usr/local/bin/helm*


# 일반유저로 복귀
$ exit


# 확인
$ helm version
version.BuildInfo{Version:"v3.12.0", GitCommit:"c9f554d75773799f72ceef38c51210f1842a1dea", GitTreeState:"clean", GoVersion:"go1.20.3"}


$ helm -n user02 ls
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION

```



### (3) [참고] bitnami repo 추가

- 유명한 charts 들이모여있는 bitnami repo 를 추가후 nginx 를 배포해 보자.

```sh
# test# add stable repo
$ helm repo add bitnami https://charts.bitnami.com/bitnami

$ helm search repo bitnami
# bitnami 가 만든 다양한 오픈소스 샘플을 볼 수 있다.
NAME                                            CHART VERSION   APP VERSION     DESCRIPTION
bitnami/airflow                                 14.1.3          2.6.0           Apache Airflow is a tool to express and execute...
bitnami/apache                                  9.5.3           2.4.57          Apache HTTP Server is an open-source HTTP serve...
bitnami/appsmith                                0.3.2           1.9.19          Appsmith is an open source platform for buildin...
bitnami/argo-cd                                 4.7.2           2.6.7           Argo CD is a continuous delivery tool for Kuber...
bitnami/argo-workflows                          5.2.1           3.4.7           Argo Workflows is meant to orchestrate Kubernet...
bitnami/aspnet-core                             4.1.1           7.0.5           ASP.NET Core is an open-source framework for we...
bitnami/cassandra                               10.2.2          4.1.1           Apache Cassandra is an open source distributed ...
...

# 설치테스트(샘플: nginx)
$ helm -n user02 install nginx bitnami/nginx

$ ku get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-6ddf75599-nnv99   1/1     Running   0          58s

NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/nginx   LoadBalancer   10.43.49.128   <pending>     80:30587/TCP   58s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           58s

NAME                              DESIRED   CURRENT   READY   AGE


# 간단하게 nginx 에 관련된 deployment / service / pod 들이 설치되었다.


# 설치 삭제
$ helm -n user02 delete nginx

$ ku get all
No resources found in user02 namespace.
```





## 3) helm 을 이용한 Istio 설치



### (1)  helm repo add

```sh
# 일반 User 권한으로
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



### (2) create ns

```sh
# create ns
$ kubectl create namespace istio-system
```



### (3) install istio crd 설치

```sh
# istio-base 설치
$ helm -n istio-system install istio-base istio/base

NAME: istio-base
LAST DEPLOYED: Sun Feb 18 09:08:51 2024
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Istio base successfully installed!



$ helm -n istio-system ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
istio-base      istio-system    1               2023-08-25 14:10:34.566977068 +0000 UTC deployed        base-1.18.2     1.18.2

# helm 확인
$ helm -n istio-system status istio-base
$ helm -n istio-system get all istio-base


# CRD 확인(istio 를 포함한 각종 CRD 를 확인할 수 있다.)
$ kubectl get crd
NAME                                       CREATED AT
addons.k3s.cattle.io                       2024-02-18T06:52:21Z
etcdsnapshotfiles.k3s.cattle.io            2024-02-18T06:52:21Z
helmcharts.helm.cattle.io                  2024-02-18T06:52:21Z
helmchartconfigs.helm.cattle.io            2024-02-18T06:52:21Z
traefikservices.traefik.containo.us        2024-02-18T06:52:51Z
traefikservices.traefik.io                 2024-02-18T06:52:51Z
middlewaretcps.traefik.io                  2024-02-18T06:52:51Z
middlewares.traefik.io                     2024-02-18T06:52:51Z
ingressroutes.traefik.containo.us          2024-02-18T06:52:51Z
ingressroutetcps.traefik.containo.us       2024-02-18T06:52:51Z
serverstransporttcps.traefik.io            2024-02-18T06:52:51Z
ingressrouteudps.traefik.io                2024-02-18T06:52:51Z
ingressrouteudps.traefik.containo.us       2024-02-18T06:52:51Z
ingressroutetcps.traefik.io                2024-02-18T06:52:51Z
tlsoptions.traefik.io                      2024-02-18T06:52:51Z
serverstransports.traefik.io               2024-02-18T06:52:51Z
ingressroutes.traefik.io                   2024-02-18T06:52:51Z
tlsstores.traefik.containo.us              2024-02-18T06:52:51Z
serverstransports.traefik.containo.us      2024-02-18T06:52:51Z
tlsstores.traefik.io                       2024-02-18T06:52:51Z
tlsoptions.traefik.containo.us             2024-02-18T06:52:51Z
middlewaretcps.traefik.containo.us         2024-02-18T06:52:51Z
middlewares.traefik.containo.us            2024-02-18T06:52:51Z
sidecars.networking.istio.io               2024-02-18T09:08:48Z
telemetries.telemetry.istio.io             2024-02-18T09:08:48Z
peerauthentications.security.istio.io      2024-02-18T09:08:48Z
requestauthentications.security.istio.io   2024-02-18T09:08:48Z
authorizationpolicies.security.istio.io    2024-02-18T09:08:48Z
destinationrules.networking.istio.io       2024-02-18T09:08:48Z
wasmplugins.extensions.istio.io            2024-02-18T09:08:48Z
workloadentries.networking.istio.io        2024-02-18T09:08:48Z
virtualservices.networking.istio.io        2024-02-18T09:08:48Z
envoyfilters.networking.istio.io           2024-02-18T09:08:48Z
serviceentries.networking.istio.io         2024-02-18T09:08:48Z
proxyconfigs.networking.istio.io           2024-02-18T09:08:48Z
workloadgroups.networking.istio.io         2024-02-18T09:08:48Z
gateways.networking.istio.io               2024-02-18T09:08:48Z
istiooperators.install.istio.io            2024-02-18T09:08:48Z



# istio 관련crd 가 존재한다면 정상 설치 된 것이다.

```



### [참고] repo 설치실패시

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





### (4) install istiod

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
NAME                         READY   STATUS    RESTARTS   AGE
pod/istiod-bc4584967-qnr58   1/1     Running   0          32s

NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                                 AGE
service/istiod   ClusterIP   10.43.33.75   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   32s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istiod   1/1     1            1           33s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/istiod-bc4584967   1         1         1       33s

NAME                                         REFERENCE           TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istiod   Deployment/istiod   <unknown>/80%   1         5         1          33s


```



### [참고] repo 설치 실패시

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





### (5) install istio-ingressgateway

참조링크 : https://istio.io/latest/docs/setup/additional-setup/gateway/



```sh
$ kubectl create namespace istio-ingress

$ helm -n istio-ingress install istio-ingressgateway istio/gateway


$ kubectl -n istio-ingress get all
NAME                                       READY   STATUS              RESTARTS   AGE
pod/istio-ingressgateway-9cc99c9db-vrqvn   0/1     ContainerCreating   0          5s

NAME                           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
service/istio-ingressgateway   LoadBalancer   10.43.6.103   <pending>     15021:30444/TCP,80:32135/TCP,443:30322/TCP   5s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-ingressgateway   0/1     1            0           5s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-ingressgateway-9cc99c9db   1         1         0       5s

NAME                                                       REFERENCE                         TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   <unknown>/80%   1         5         0          5s



$ kubectl -n istio-ingress get svc
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.6.103   <pending>     15021:30444/TCP,80:32135/TCP,443:30322/TCP   56s


32135 / 30322 nodeport 로 접근 가능


```





### [참고] clean up

모든 테스트를 마치고 istio를 최종 삭제할때는 아래 명령으로 삭제 하자.

```sh
$ helm -n istio-system delete istio-istiod
  helm -n istio-system delete istio-base
  helm -n istio-ingress delete istio-ingressgateway

# 확인
$ helm -n istio-system ls
$ helm -n istio-ingress ls


# namespace 삭제
# $ kubectl delete namespace istio-system
#   kubectl delete namespace istio-ingress
```





## 4) sample app sidecar inject

특정 POD 에 sidecar 를 inject 하는 방법에 대해서 알아보자.





### (1) userlist pod 실행

1교시 kubernetes 실습때 수행했던 userlist pod 를 다시 확인해 보자.

```sh
$ alias ku='kubectl -n user02'

# userlist pod 확인
$ ku get pod
NAME                       READY   STATUS    RESTARTS   AGE
userlist-8d74d58d8-spffl   1/1     Running   0          2m25s



# 존재하지 않는다면 아래명령으로 실행
$ ku create deploy userlist --image=ssongman/userlist:v1
```



위 userlist에 sidecar 를 inject 해보자.

특정 pod 에 sidecar 를 inject 시키는 방식은 총 3가지가 존재한다.

1. namespace label 추가 방식
2. deployment annotations 방식
3. istioctl kube-inject 방식



우리는 첫번째 방식을 이용해 보자.



### (2) sidecar inject 설정

적용하기전에 예시를 살펴보자.

```sh
# 적용하는 방법
# [적용예시]  kubectl label namespace <namespace> istio-injection=enabled
```



- 적용 확인

```sh
# 적용전 확인
$ kubectl get ns user02 -o yaml

apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2024-02-18T06:57:00Z"
  labels:                                     <-- labels 확인(istio label 이 없다.)
    kubernetes.io/metadata.name: user02
  name: user02
  resourceVersion: "740"
  uid: aa9d25cd-4a3c-4785-81d9-17ff35049365
spec:
  finalizers:
  - kubernetes
status:
---


# 적용(label 추가)
# 자기 Namespce 로 변경하여 적용하자.
$ kubectl label namespace user02 istio-injection=enabled
namespace/user02 labeled


# 적용후 확인
$ kubectl get ns user02 -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2024-02-18T06:57:00Z"
  labels:
    istio-injection: enabled                    <-- istio label 이 잘 추가되었다.
    kubernetes.io/metadata.name: user02
  name: user02
  resourceVersion: "4553"
  uid: aa9d25cd-4a3c-4785-81d9-17ff35049365
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
---
```





### (3) pod 적용을 위한 재기동

적용을 희망하는 pod 를 재기동시킨다.  

재기동은 pod 를 삭제하게 되면 새롭게 running 된다.

```sh
# 확인
$ ku get pod
NAME                       READY   STATUS    RESTARTS   AGE
userlist-8d74d58d8-spffl   1/1     Running   0          2m25s


# pod 재기동(삭제)
$ ku delete pod userlist-8d74d58d8-spffl
pod "userlist-8d74d58d8-spffl" deleted


## pod 삭제는 graceful 방식으로 삭제되므로 약간의 시간이 필요하다.

# 확인
$ ku get pod
NAME                       READY   STATUS    RESTARTS   AGE
userlist-8d74d58d8-5j4jg   2/2     Running   0          33s



# istio sidecar 가 포함되어 2개의 container 가 되었다.


# describe 로 확인
$ ku describe pod userlist-8d74d58d8-5j4jg
...
Containers:
  userlist:
    Container ID:   containerd://d1cc40a908feaf4baf9e51d6d4eb9f55e9adb305cddf8c1d9d5ee3b57cd53afd
    Image:          ssongman/userlist:v1
    Image ID:       docker.io/ssongman/userlist@sha256:74f32a7b4bab2c77bf98f2717fed49e034756d541e536316bba151e5830df0dc
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 18 Feb 2024 09:29:58 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zhkvp (ro)
  istio-proxy:
    Container ID:  containerd://579f37df58ee9c684574d71c91aaf860083e23b37dab98558f9441e1c5d0a606
    Image:         docker.io/istio/proxyv2:1.20.3
    Image ID:      docker.io/istio/proxyv2@sha256:18163bd4fdb641bdff1489e124a0b9f1059bb2cec9c8229161b73517db97c05a
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
      Started:      Sun, 18 Feb 2024 09:29:58 +0000
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



### (4) clean up

userlist 는 더 이상 불필요하므로 삭제 하자.

```sh
$ ku delete deploy userlist

# label 삭제
$ kubectl label --overwrite namespace user02 istio-injection-


# namespace 확인
$ kubectl get ns user02 -o yaml
```







# 4. [EduCluster] 실습

원할한 테스트를 위해 Load Balancer 와 Monitoring 이 존재하는 EduCluster 환경에서 테스트를 수행한다.





## 1) ktdsEduCluster 접속 설정 변경 - ★★★

EduCluster 에 접속할 수 있는 접속정보 파일로 설정 변경 작업을 수행한다.



```sh

# ktdsEduCluster 접속하도록 설정 변경
$ export KUBECONFIG="${HOME}/.kube/config-ktdseducluster"


# Cluste 확인
$ kubectl get nodes
NAME          STATUS   ROLES                       AGE     VERSION
master01.c1   Ready    control-plane,etcd,master   7h44m   v1.28.6+k3s2
master02.c1   Ready    control-plane,etcd,master   7h41m   v1.28.6+k3s2
master03.c1   Ready    control-plane,etcd,master   7h40m   v1.28.6+k3s2
worker01.c1   Ready    worker                      5h46m   v1.28.6+k3s2
worker02.c1   Ready    worker                      5h22m   v1.28.6+k3s2
worker03.c1   Ready    worker                      5h22m   v1.28.6+k3s2



# 자신 Namespace alias 설정
$ alias ku='kubectl -n user02'     <-- 각자 Namespace 를 alais 로 설정하자.


```

* 주의할점
  * 위 설정은 Terminal Session 에 설정된다.
  * Terminal 을 재기동 하거나 새로운 Terminal 을 생성하게 되면 반드시 위 내용을 반영해야 한다.

​	





[참고] 다시 개인 VM Cluster 로 접속할때...

```sh

# VM Cluster 접속하도록 설정 변경 
$ export KUBECONFIG="${HOME}/.kube/config"

# Cluster node 확인
$ kubectl get nodes
NAME        STATUS   ROLES                  AGE   VERSION
bastion02   Ready    control-plane,master   49d   v1.26.5+k3s1

```





## 2) sample app (bookinfo) install

다양한 Istio 기능을 테스트하기 위해 사용되는 4개의 개별 마이크로서비스로 구성된 샘플 애플리케이션을 배포해 보자.

참조링크 : https://istio.io/latest/docs/examples/bookinfo/





### (1) bookinfo application 설명

bookinfo 프로그램은 온라인 서점의 단일 카탈로그 항목과 유사한 책에 대한 정보를 보여주는 프로그램이다.

페이지에는 책에 대한 설명, 책 세부 정보(ISBN, 페이지 수 등) 및 몇 가지 책 리뷰가 표시된다.



Bookinfo 애플리케이션은 4개의 개별 마이크로서비스로 나뉜다.

- productpage

  - productpage 서비스는 및 details 과 reviews 로 구성된 서비스를 호출한다.

- details

  - details 서비스는 도서 정보가 표현된다.

- reviews

  - reviews 서비스는 서평을 표현하며 ratings 서비스를 호출한다.

- ratings

  - ratings서비스는 도서 순위 정보를 표현한다.

  

![image-20230826121558547](ServiceMesh.assets/image-20230826121558547.png)




reviews마이크로 서비스 에는 3가지 버전이 있다.

- 버전 v1은 ratings 서비스를 호출하지 않는다.
- 버전 v2는 ratings 서비스를 호출하고 각 등급을 1~5개의 검은색 start로 표시한다.
- 버전 v3은 ratings 서비스를 호출하고 각 등급을 1~5개의 빨간색 별표로 표시한다.



### (2) bookinfo architecture 

- istio 적용전

![bookinfo-noistio](ServiceMesh.assets/bookinfo-noistio.svg)

bookinfo 프로그램은 polyglot 예제이다. 즉, 각 마이크로서비스는 다른 언어로 작성되었다. 

이러한 구성은 Istio를 적용하는데 있어서 전혀 영향이 주지 않는 대표적인 예제가 될 수 있다.



- istio 적용후

istio 적용하는데 있어서 Application 자체를 변경할 필요가 없다. 각 서비스에 envoy sidecar 를 주입만 하면 된다.

적용후 모습은 아래와 같다.

![bookinfo-withistio](ServiceMesh.assets/bookinfo-withistio.svg)

모든 서비스에 inject 되는 Sidecar 는 incoming / outgoing 되는 모든 call 을 intercept 하여 각종 기능에 적용된다.



### (3) bookinfo 생성

#### namespace 설정

- namespace 생성 및 자동 sidecar inject 설정

```sh

# user02 를 자신의 Namespace 명으로 변경


# 설정전 확인
$ kubectl get ns user02 -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2024-02-18T08:21:00Z"
  labels:
    kubernetes.io/metadata.name: user02
  name: user02
  resourceVersion: "98957"
  uid: 02d09df1-ac89-40e5-b0fb-36ade0087989
spec:
  finalizers:
  - kubernetes
status:
  phase: Active




# label 설정
$ kubectl label namespace user02 istio-injection=enabled



# 설정후 확인
$ kubectl get ns user02 -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2024-02-18T08:21:00Z"
  labels:
    istio-injection: enabled              <-- 설정 완료
    kubernetes.io/metadata.name: user02
  name: user02
  resourceVersion: "122074"
  uid: 02d09df1-ac89-40e5-b0fb-36ade0087989
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
---
```



#### [참고] git clone

- 실습자료 download
  - 실습자료는 이미 기존에 이미 받아 놓았으므로 생략한다.
  - 만약 ~/githubrepo/ktds-edu-k8s-istio/  디렉토리가 없다면 아래를 참고하여 clone 하자.


```sh
$ mkdir ~/githubrepo

$ cd ~/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git

$ cd ~/githubrepo/ktds-edu-k8s-istio/
drwxr-xr-x 8 song song  4096 May 14 01:59 .git/
-rw-r--r-- 1 song song 11357 May 14 01:59 LICENSE
-rw-r--r-- 1 song song  2738 May 14 01:59 README.md
drwxr-xr-x 3 song song  4096 May 14 01:59 beforebegin/
drwxr-xr-x 8 song song  4096 May 14 01:59 istio/
drwxr-xr-x 3 song song  4096 May 14 01:59 ktcloud-setup/
drwxr-xr-x 4 song song  4096 May 14 01:59 kubernetes/

```



#### bookinfo app depoy 

```sh

$ cd ~/githubrepo/ktds-edu-k8s-istio

$ ll ./istio/bookinfo/11.bookinfo.yaml
-rw-rw-r-- 1 ubuntu ubuntu 7974 Feb 18 07:00 ./istio/bookinfo/11.bookinfo.yaml



# 4개의 마이크로서비스들에 대한 생성 yaml 확인
$ cat ./istio/bookinfo/11.bookinfo.yaml
...

# bookinfo 생성
$ ku apply -f ./istio/bookinfo/11.bookinfo.yaml

service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created



# 약 2분 소요...


# 확인
$ ku get pods
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-58c888794b-86dnl       2/2     Running   0          71s
productpage-v1-5c9b8f457d-4hh94   2/2     Running   0          66s
ratings-v1-6fb94bb7cd-tdcdc       2/2     Running   0          70s
reviews-v1-6dbbc44b9d-9qvst       2/2     Running   0          68s
reviews-v2-7fcd6bfb8b-pblcl       2/2     Running   0          68s
reviews-v3-6b778f96f4-j7xgb       2/2     Running   0          67s


# 모든 pod가 2개 container로 실행되었다.


$ ku get services
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.43.184.228   <none>        9080/TCP   91s
productpage   ClusterIP   10.43.66.156    <none>        9080/TCP   86s
ratings       ClusterIP   10.43.165.5     <none>        9080/TCP   90s
reviews       ClusterIP   10.43.30.168    <none>        9080/TCP   88s


```



### (4) productpage 호출 테스트

#### ratings --> productpage 를 호출


```sh

# 1. ratings app pod 확인
$ ku get pod -l app=ratings
NAME                          READY   STATUS    RESTARTS   AGE
ratings-v1-5bf485ffc8-tnm5t   2/2     Running   0          42m


$ ku describe pod -l app=ratings
...
Containers:
  ratings:
  istio-proxy:
...


# 2. rating 에서 productpage call 확인
$ ku exec ratings-v1-5bf485ffc8-tnm5t -c ratings -- curl -sS productpage:9080/productpage




<!DOCTYPE html>
<html>
  <head>
    <title>Simple Bookstore App</title>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
...




# 3. rating 에서 productpage call 확인
$ ku exec ratings-v1-5bf485ffc8-tnm5t -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"

<title>Simple Bookstore App</title>      <-- 이렇게 나오면 정상

```



#### [참고] 

위 작업을 한개의 명령으로 확인 할 수 있다.

```sh

$ ku exec "$(ku get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"

<title>Simple Bookstore App</title>      <-- 이렇게 나오면 정상


```



### (5) virtualservice / gateway 생성

bookinfo host 를 각자 계정명으로 변경한 후 적용하자.

IP 는 AWS 의 Elastic IP 이므로 변경할 필요 없다.

```sh

$ cd ~/githubrepo/ktds-edu-k8s-istio

$ ll ./istio/bookinfo/12.bookinfo-gw-vs.yaml
-rw-rw-r-- 1 ubuntu ubuntu 717 Feb 18 07:00 ./istio/bookinfo/12.bookinfo-gw-vs.yaml




# 12.bookinfo-gw-vs.yaml 파일 확인
$ vi ./istio/bookinfo/12.bookinfo-gw-vs.yaml
---
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "bookinfo.user02.cloud.43.203.62.69.nip.io"    # 각자 Namespace명으로 변경 필요
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080


# 생성
$ ku apply -f ./istio/bookinfo/12.bookinfo-gw-vs.yaml

gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created


# 확인
$ ku get gateway
NAME               AGE
bookinfo-gateway   4s


$ ku get vs
NAME       GATEWAYS               HOSTS                                            AGE
bookinfo   ["bookinfo-gateway"]   ["bookinfo.user02.cloud.43.203.62.69.nip.io"]   11s


```



### (6) Ingress 생성

bookinfo host 를 각자 계정명으로 변경한 후 적용하자.

#### Ingress 생성

```sh

$ cd ~/githubrepo/ktds-edu-k8s-istio



# 15.bookinfo-ingress.yaml 파일 확인
$ cat ./istio/bookinfo/15.bookinfo-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookinfo-ingress-user02                         <-- 각자 NS명으로 변경 필요
  namespace: istio-ingress
spec:
  ingressClassName: traefik
  rules:
  - host: "bookinfo.user02.cloud.43.203.62.69.nip.io"   <-- 각자 NS명으로 변경 필요
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: istio-ingressgateway
            port:
              number: 80


# 15.bookinfo-ingress.yaml 파일 수정
$ vi ./istio/bookinfo/15.bookinfo-ingress.yaml
...


# ingress 생성
# ingress 는 istio-ingress namespace 에서 실행해야 한다.
$ kubectl -n istio-ingress apply -f ./istio/bookinfo/15.bookinfo-ingress.yaml


$ kubectl -n istio-ingress get ingress
NAME                      CLASS     HOSTS                                        ADDRESS                                                                 PORTS   AGE
bookinfo-ingress-user02   traefik   bookinfo.user02.cloud.43.203.62.69.nip.io   10.128.0.35,10.128.0.36,10.128.0.38,10.128.0.39,10.208.0.2,10.208.0.3   80      4s


```



#### browser 에서 확인

```
http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage
```



![image-20230826121558547](ServiceMesh.assets/image-20230826121558547.png)



#### ingress domain 으로 call

```sh

## ingress 확인
$ curl -s "http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage" | grep -o "<title>.*</title>"

$ curl -s "http://bookinfo.user20.cloud.43.203.62.69.nip.io/productpage" | grep -o "<title>.*</title>"

<title>Simple Bookstore App</title>    <-- 나오면 정상


# 만약 위 결과가 나오지 않으면 아래 node port 로 접근해서 원인을 찾는다.
```



#### istio-ingressgateway node port

```sh

# istio ingressgateway service 확인
$ kii get svc
NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.78.43   <pending>     15021:32086/TCP,80:32190/TCP,443:30919/TCP   74m

NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.43.78.43   <pending>     15021:32086/TCP,80:32190/TCP,443:30919/TCP   2d4h




# 32190 node port 로 접근 가능


# master01 IP의 node port 로 접근 테스트
$ curl http://10.128.0.35:32190/productpage -H "Host:bookinfo.user02.cloud.43.203.62.69.nip.io"  | grep -o "<title>.*</title>"

<title>Simple Bookstore App</title>


```





#### 초당 0.5회 call 

```sh
$ while true; do curl -s http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage | grep -o "<title>.*</title>"; sleep 0.5; echo; done

```

while문으로 call유지 한채로 아래 monitoring 에서 kiali / jaeger / grafana 확인하자.



## 3) Monitoring



istio 에서 제공하는 모니터링종류는 아래와 같이 grafana / kiali / jaeger 가 있다.



### (1) Grafana

http://grafana.istio-system.cloud.43.203.62.69.nip.io/

주로보는 대쉬보드 : Dashboards > Browse > istio > Istio Service Dashboard

![image-20220602191900236](ServiceMesh.assets/monitoring-grafana.png)



* Mesh-Dashboard
  * http://grafana.istio-system.cloud.43.203.62.69.nip.io/d/G8wLrJIZk/istio-mesh-dashboard?orgId=1&refresh=5s

* Service Dashboard
  * http://grafana.istio-system.cloud.43.203.62.69.nip.io/d/LJ_uJAvmk/istio-service-dashboard?orgId=1&refresh=1m&var-datasource=default&var-service=productpage.user02.svc.cluster.local&var-qrep=destination&var-srccluster=All&var-srcns=All&var-srcwl=All&var-dstcluster=All&var-dstns=All&var-dstwl=All



### (2) Kiali

http://kiali.istio-system.cloud.43.203.62.69.nip.io

![image-20220602162703029](ServiceMesh.assets/monitoring-kiali.png)

* traffic-animation
* replay







### (3) Jaeger

http://jaeger.istio-system.cloud.43.203.62.69.nip.io

![img](ServiceMesh.assets/monitoring-jaeger.png)





# 5. [EduCluster] 실습(Traffic control)





## 0) 초기 셋팅



#### default destination rules

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

# 13.destination-rule-all.yaml 파일 확인
$ cat ./istio/bookinfo/13.destination-rule-all.yaml

apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: productpage
spec:
  host: productpage
  subsets:
  - name: v1
    labels:
      version: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: ratings
spec:
  host: ratings
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v2-mysql
    labels:
      version: v2-mysql
  - name: v2-mysql-vm
    labels:
      version: v2-mysql-vm
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: details
spec:
  host: details
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
---


# 적용
$ ku apply -f ./istio/bookinfo/13.destination-rule-all.yaml


```





#### 초당 0.5회 call

```sh
$ while true; do curl -s http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage | grep -o "<title>.*</title>"; sleep 0.5; echo; done

```

위 while문 유지 하면서 monitoring (kiali / jaeger / grafana) 하면서 아래 traffic 실습을 진행하자.



## 1) Traffic Shifting(WBR)

서비스별로 트래픽의 가중치를 조정하므로서 특정 버전에서 다른 버전으로 트래픽을 이동하는 방법을 제어할 수 있다.

트래픽을 특정 Version 별 가중치를 기반으로 Routing 하므로 WBR(Weight Based Routing) 이라고 한다.

kiali 를 확인하면서 아래를 진행해보자.



### (1) 기본 routing 설정

```sh

$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/bookinfo/21.virtual-service-all-v1.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1
---

$ ku apply -f ./istio/bookinfo/21.virtual-service-all-v1.yaml

```

- 변화사항
  - reviews 의 v2,v3호출 되지 않도록 라우팅 변경함.
- kiali 변화사항 모니터링
  - graph 모니터링
  - replay 기능 모니터링
  - Service 확인
    - VS yaml 확인



### (2) reviews - v1/v3 - 50%

> WBR(Weight-bassed routing) 적용


 reviews 서비스의 v1, v3에 각각 50% 씩만 흘려보자.

```sh
$ cat ./istio/bookinfo/22.virtual-service-reviews-50-v3.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50

$ ku apply -f ./istio/bookinfo/22.virtual-service-reviews-50-v3.yaml

```



### (3) reviews - v3 - 100%

reviews:v3 서비스가 안정적이라고 판단되면 아래virtualservice 적용하여 review:v3으로 100% 라우팅할 수 있다.

```sh
$ cat ./istio/bookinfo/23.virtual-service-reviews-v3.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v3


$ ku apply -f ./istio/bookinfo/23.virtual-service-reviews-v3.yaml

```

- 변화사항

  - Kiali 를 확인하면 v1로 흐르는 콜은 점점 작아지다가 사라지며
  - v3으로 100% 흐르는 모습을 볼수 있다.

- kiali UI 에서 직접 수정가능하다.





- clean up

```sh
# 내부 라우팅 정책을 삭제 한다.
$ ku delete -f ./istio/bookinfo/21.virtual-service-all-v1.yaml
```







## 2) Request Routing(CBR)

여러 버전의 마이크로서비스로 동적으로 라우팅하는 방법을 확인할 수 있다.

트래픽의 특정 Contents를 기반으로 Routing 하므로 CBR(Contents Based Routing) 이라고 한다.

reviews 서비스의 routing 을 변경해보면서 Kiali 를 집중 모니터링 하자.



### (1) 기본 routing 설정

우선 모든 서비스가 reviews:v1 로만 흐르도록 변경한다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/bookinfo/21.virtual-service-all-v1.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1
---


$ ku apply -f ./istio/bookinfo/21.virtual-service-all-v1.yaml

```



### (2) user 기반 routing

아래 경우 jason이라는 사용자의 모든 트래픽은 reviews:v2로 라우팅 되도록 설정한다.

```sh
$ cat ./istio/bookinfo/24.virtual-service-reviews-test-v2.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1


$ ku apply -f ./istio/bookinfo/24.virtual-service-reviews-test-v2.yaml

```

해석해보면 headers 의 end-user 가 jason 일 경우만 v2 버젼으로 call 되도록 설정한다.



browser 에서 jason 으로 로그인 한다음 접근해보자. 

http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage



로그인전에는 reviews-1 이 호출된다.

![image-20220602174208861](ServiceMesh.assets/image-20220602174208861.png)



jason 으로 로그인이후에는 reviews-v2 가 호출되는 모습을 볼 수 있다.

![image-20220602174111135](ServiceMesh.assets/image-20220602174111135.png)



- 결론

 CBR(Contents based Route) 의 한 종류인 canary 배포로 활용할 수 있다.

v2 를 운영환경에 배포한 다음 특정 사용자만 접속가능하게 한다음 충분히 테스트 한후 문제가 없다면 정식 버젼으로 변경하는 프로세스로 잡을 수 있다.



- clean up 

```sh
$ ku delete -f ./istio/bookinfo/21.virtual-service-all-v1.yaml
```

위와 같이 virtualservice 를 삭제하게 되면 초기에 테스트 한것처럼 v1, v2, v3 가 RoundRobbin 으로 접속된다.







## 3) Fault Injection

application 의 복원력을 테스트하기 위해서 결함을 주입할 수 있다.





### (1) 사전준비

적절한 테스트를 위해서 바로 윗단계에서 테스트 한것처럼 jason 으로 로그인 시 v2 로 접속되며 그 외에는 v1 으로 접속되는 환경으로 변경한다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ ku apply -f ./istio/bookinfo/21.virtual-service-all-v1.yaml

$ ku apply -f ./istio/bookinfo/24.virtual-service-reviews-test-v2.yaml
```



현재의 상태는 아래와 같다.

- jason 로그인시
  - `productpage` → `reviews:v2` → `ratings` 

- jason 외
  - `productpage` → `reviews:v1` 






### (2) http 지연 오류 inject

application 의 탄력성(resiliency) 를 테스트 하기 위해서 jason 으로 로그인시 7초 지연 되도록 설정해 보자.

reviews:v2 서비스에는 rating 서비스 호출시 10초 connection timeout 이 있다.  

이번에 주입한 7초 지연 건이 있더라도 오류 없이 종단 간 흐름이 계속될 것으로 기대한다.



```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/bookinfo/25.virtual-service-ratings-test-delay.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    fault:
      delay:
        percentage:
          value: 100.0
        fixedDelay: 7s
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1

$ ku apply -f ./istio/bookinfo/25.virtual-service-ratings-test-delay.yaml
```



위와 같이 적용후  jason 으로 접속 시도해 보자.



7초 지연후 문제없이 호출할 것으로 예상했지만 아래와 같은 에러가 발생한다.

```
Error fetching product reviews!
Sorry, product reviews are currently unavailable for this book.
```



오류원인을 확인하기 위해 dev-tools 로 확인해 보니 아래와 같이 6초의 load 시간인 것을 확인할 수 있다.

![image-20220602181328032](ServiceMesh.assets/image-20220602181328032.png)



버그를 찾았다.

reviews 에서  connection timeout 10초에는 문제가 없다. 하지만 productpage 에서 reviews 사이에서 6초 timeout 이 걸려 있는 점을 확인할 수 있다.

이와 같은 버그는 서로 다른 팀이 서로 다른 마이크로서비스를 독립적으로 개발하는 일반적인 엔터프라이즈 애플리케이션에서 발생할 수 있다. Istio의 결함 주입 규칙(Fault Injection)은 최종 사용자에게 영향을 주지 않고 이러한 이상 현상을 찾는데 유용하게 사용될 수 있다.





### (3) HTTP 중단 오류 inject (500 error)

jason user 로 로그인시 http 500 를 리턴하도록 해보자.

"Ratings service is currently unavailable" 라는 메세지가 나올것을 기대한다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/bookinfo/26.virtual-service-ratings-test-abort.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    fault:
      abort:
        percentage:
          value: 100.0
        httpStatus: 500
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1



$ ku apply -f ./istio/bookinfo/26.virtual-service-ratings-test-abort.yaml
```

위를 적용하고 jason 으로 로그인해 보자.

아래와 같은 메세지가 나오는 것을 확인할 수 있다.

```
[Book Reviews]


An extremely entertaining play by Shakespeare. The slapstick humour is refreshing!
Reviewer1
Ratings service is currently unavailable


Absolutely fun and entertaining. The play lacks thematic depth when compared to other plays by Shakespeare.
Reviewer2
Ratings service is currently unavailable
```

jason 을 제외한 나머지 사용자는 잘 처리되는 것을 확인할 수 있다.

- kiali / jaeger 로 확인해 보자.



### (4) HTTP 중단 오류 inject (500 비율 조정)

httpStatus: 500 error 의 비율을 조정해 보자.

ratings 서비스를 call 했을때 500 error 비율을 50 으로 설정해 보자.

json 로그인시 ratings 이 호출되고 50% 비율로 500 에러가 리턴될 것이다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/bookinfo/27.virtual-service-ratings-500-fi-rate.yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault:
      abort:
        httpStatus: 500
        percentage:
          value: 50
    route:
    - destination:
        host: ratings
        subset: v1


$ ku apply -f ./istio/bookinfo/27.virtual-service-ratings-500-fi-rate.yaml

```

* UI 에서 확인
  * http://bookinfo.user02.cloud.43.203.62.69.nip.io/productpage



#### curl test

```sh

# istio sidecar 가 inject된 pod에서 수행 ( curltest pod 에서)
$ ku run curltest --image=curlimages/curl -- sleep 365d

# curltest pod 내로 진입
$ ku exec -it curltest -- sh


# 여러번 호출해 보자.
$ curl -i http://ratings:9080/ratings/0

HTTP/1.1 500 Internal Server Error
HTTP/1.1 200 OK



# 여러번 호출해 보자.
/ $ curl -i -s http://ratings:9080/ratings/0 | grep HTTP
HTTP/1.1 500 Internal Server Error
/ $ curl -i -s http://ratings:9080/ratings/0 | grep HTTP
HTTP/1.1 200 OK
/ $ curl -i -s http://ratings:9080/ratings/0 | grep HTTP
HTTP/1.1 500 Internal Server Error
/ $ curl -i -s http://ratings:9080/ratings/0 | grep HTTP
HTTP/1.1 200 OK


# 1초에 한번씩 호출해 보자.
$ while true; do curl -i -s http://ratings:9080/ratings/0 | grep HTTP; sleep 1; done
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 200 OK
HTTP/1.1 200 OK
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 200 OK
HTTP/1.1 200 OK
HTTP/1.1 200 OK
...


# 50% 비율로 500 에러가 발생하는 것을 확인 할 수 있다.
# 아래 비율을 조정해 보자
```



#### 비율 조정

kiali 에서도 쉽게 조정이 가능하다.

* 메뉴 : graph > Rating > Detail > VS 선택
  * 링크 : http://kiali.istio-system.cloud.43.203.62.69.nip.io/kiali/console/namespaces/user02/istio/virtualservices/ratings

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault:
      abort:
        httpStatus: 500
        percentage:
          value: 90              <--- 50에서 90으로 조정
    route:
    - destination:
        host: ratings
        subset: v1
```



확인

```sh

# 위에서 계속
...
HTTP/1.1 500 Internal Server Error
HTTP/1.1 200 OK
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error
HTTP/1.1 500 Internal Server Error

...


# 90% 비율로 500 에러가 발생하는 것을 확인 할 수 있다.


Ctrl + C

$ exit
```





* 초당 0.5회 Call 건도 중지
  * Bookinfo Test 는 모두 완료됨

- clean up

```sh
$ ku apply -f ./istio/bookinfo/21.virtual-service-all-v1.yaml
```



### (5) Clean up

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

# alias 설정
$ alias ku='kubectl -n user02'
 
$ ku delete -f ./istio/bookinfo/11.bookinfo.yaml
  ku delete -f ./istio/bookinfo/12.bookinfo-gw-vs.yaml
  ku delete -f ./istio/bookinfo/13.destination-rule-all.yaml 
  ku delete -f ./istio/bookinfo/21.virtual-service-all-v1.yaml
  kubectl -n istio-ingress delete -f ./istio/bookinfo/15.bookinfo-ingress.yaml

# namespace label 삭제
$ kubectl label --overwrite namespace user02 istio-injection-


```





## 4) Circuit Breaking

istio 는 Connection pool 과   Load balancing pool 기반의 circuit breaking 기능을 제공한다.



Istio 는 *DestinationRule* 의 `.trafficPolicy.outlierDetection`, `.trafficPolicy.connectionPool` 스팩을 통해 *circuit breaking* 을 정의할 수 있으며 다음과 같이 2가지 유즈케이스를 가지고 있다.

- **첫번째**, Connection Max & Pending 수에 따른 *circuit break*
  - *service* 요청(upstream)에 대한 connection pool 을 정의한다.
  - 최대 활성 연결 갯수와 최대 요청 대기 수를 지정한다.
  - 허용된 최대 요청 수 보다 많은 요청을 하게 되면 지정된 임계값만큼 대기 요청을 갖게된다.
  - 이 임계점을 넘어가는 추가적인 요청은 거부(*circuit break*)하게 된다.
- **두번째**, Load balancing pool의 인스턴스의 상태에 기반하는 *circuit break*
  - 인스턴스(*endpoints*) 에 대한 load balancing pool 이 생성/운영 된다.
  - n개의 인스턴스를 가지는 load balancing pool 중 응답이 없는 인스턴스를 탐지하고 배제(*circuit break*)한다.
  - HTTP 서비스인 경우 미리 정의된 시간 동안 API 호출 할 때 5xx 에러가 지속적으로 리턴되면 pool 로 부터 제외된다.



### (1) Connection Max & Pending 수에 따른 *circuit break*

> 지정된 서비스에 connections 의 volume을 제한하여 가능 용량 이상의 트래픽 증가에 따른 서비스 Pending 상태를 막도록 *circuit break* 를 작동시키는 방법

#### httpbin 서비스 실행

circuit break 대상이 되는 httpbin 앱을 설치한다.  httpbin 은 HTTP 프로토콜 echo 응답 하는 테스트 App 이다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/httpbin/11.httpbin-deploy-svc.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
  template:
    metadata:
      labels:
        app: httpbin
    spec:
      containers:
      - name: httpbin
        image: docker.io/honester/httpbin:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  selector:
    app: httpbin
  ports:
  - name: http
    port: 8000
    targetPort: 80


# 설치

$ ku apply -f ./istio/httpbin/11.httpbin-deploy-svc.yaml
deployment.apps/httpbin created
service/httpbin created


# 20초 정도 소요됨


# 확인
$ ku get pod
NAME                              READY   STATUS            RESTARTS   AGE
httpbin-d6d55998b-9sk6r           0/2     PodInitializing   0          15s
...

```



#### fortio client 

MSA 환경에서 로드 테스트 용도로 많이 사용하는 fortio 툴 을 설치한다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/httpbin/12.fortio-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: fortio
  labels:
    app: fortio
spec:
  containers:
  - image: docker.io/fortio/fortio:latest_release
    imagePullPolicy: IfNotPresent
    name: fortio
    ports:
    - containerPort: 8080
      name: http-fortio
    - containerPort: 8079
      name: grpc-ping


$ ku apply -f ./istio/httpbin/12.fortio-pod.yaml
pod/fortio created

$ ku get pod
NAME                              READY   STATUS    RESTARTS   AGE
fortio                            2/2     Running   0          11s
httpbin-d6d55998b-9sk6r           2/2     Running   0          92s
...

```



- curl 확인

fortio 에서 *httpbin* 으로 요청. **200(정상)** 응답 코드가 리턴됨.

```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio curl  http://httpbin:8000/get
HTTP/1.1 200 OK
...


$ ku exec -it fortio -c fortio -- /usr/bin/fortio curl  http://httpbin:8000/get | grep HTTP
HTTP/1.1 200 OK


# 0.5초에 한번씩 call
$ while true; do ku exec -it fortio  -c fortio -- /usr/bin/fortio curl  http://httpbin:8000/get | grep HTTP; sleep 0.5; echo; done

HTTP/1.1 200 OK

HTTP/1.1 200 OK

HTTP/1.1 200 OK

HTTP/1.1 200 OK
...

```



Kiali 에서는 다음과 같이 조회된다.

![image-20220602211432271](ServiceMesh.assets/image-20220602211432271.png)









#### circuit breaker 설정

- DestinationRule 를 생성하여 circuit break 가 발생할 수 있도록 Connection pool을 최소값으로 지정한다.
- http1MaxPendingRequests=1
  - Queue에서 onnection pool 에 연결을 기다리는 request 수를 1개로 제한한다.

- maxRequestsPerConnection=1
  - keep alive 기능 disable 한다.


```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/httpbin/13.dr-httpbin.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: dr-httpbin
spec:
  host: httpbin
  trafficPolicy:
    connectionPool:
      http:
        http1MaxPendingRequests: 1
        maxRequestsPerConnection: 1

$ ku apply -f ./istio/httpbin/13.dr-httpbin.yaml


```



Kiali 에서는 아래와 같이 번개모양의 circuit break 뱃지가 나타난다.

![image-20220602211729632](ServiceMesh.assets/image-20220602211729632.png)





#### Tripping the circuit breaker

- 비교를 위해 작은 양의 트래픽 load를 발생시킨다.
- `-c 1` 옵션으로 동시 연결을 1로 총 10회를 부하를 발생시킨다.
- 결과를 확인해보면 모두 응답코드 **200(정상)** 을 리턴 한다.

```sh

$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 1 -qps 0 -n 10 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 10 (100.0 %)
...


```



- `-c 1` 옵션으로 총 100회로 트래픽 load를 발생 시킨다.
- 결과를 확인해보면 100회 모두 **200(정상)** 을 리턴 한다.

```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 1 -qps 0 -n 100 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 100 (100.0 %)
...

```



- `-c 2` 옵션으로 동시 연결을 2로 늘려 트래픽 load를 발생 시킨다.
- 결과를 확인해보면 100회 모두 **200(정상)** 을 리턴 한다.


```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 1 -qps 0 -n 100 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 100 (100.0 %)
...

```



- `-c 3` 옵션으로 동시 연결을 3로 늘려 트래픽 load를 발생 시킨다.
- 결과를 확인해보면 응답코드 **503(오류)** 응답 코드가 6회 발생했다.


```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 3 -qps 0 -n 20 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 94 (94.0 %)
Code 503 : 6 (6.0 %)
...

```





- `-c 5` 옵션으로 동시 연결을 5로 늘려 트래픽 load를 발생 시킨다.
- 결과를 확인해보면 응답코드 **503(오류)** 응답 코드가 10회 발생했다.

```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 5 -qps 0 -n 100 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 90 (90.0 %)
Code 503 : 10 (10.0 %)
...

```





- `-c 10` 옵션으로 동시 연결을 10로 늘려 트래픽 load를 발생 시킨다.
- 결과를 확인해보면 응답코드 **503(오류)** 응답 코드가 37회 발생했다.


```sh
$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 10 -qps 0 -n 100 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 63 (63.0 %)
Code 503 : 37 (37.0 %)
...

```



- 아래와 같이 httpbin-dr를 삭제하고 circuit break 를 제거한 상태에서 동일한 트래픽 load 를 발생시키면 응답코드가 모두 200(정상) 임을 확인할 수 있다.  즉, 서버에 부하가 걸리더라도 WEB 서버를 보호하지 못하고 모두 처리를 하고 있는 상태라고 해석할 수 있다.

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ ku delete -f ./istio/httpbin/13.dr-httpbin.yaml

# kiali 에서 Circuit Braker Icon 이 사라진 것을 확인하자.



$ ku exec -it fortio -c fortio -- /usr/bin/fortio load -c 10 -qps 0 -n 100 -loglevel Warning http://httpbin:8000/get

...
Code 200 : 100 (100.0 %)
...

```



#### 결론

- istio 는 DestinationRule을 통해 *circuit break* 를 정의를 할 수 있다.
- *k8s service* `httpbin` 에 *DestionationRule* `dr-httpbin` 을 정의하여 connections 의 volume 1개, ending valume 1개로 제한하였다.
- 1번 요청의 경우는 정상 요청처리 중이다.
- 2번 요청이 발생 했을때 1번 요청 처리 중이라면 2번 요청은 pending 상태가 된다.
- 1,2번 요청이 처리,pending 상태에서 3번 요청이 발생하게 된다면 설정에 따라 *circuit break* 가 발생하게 된다.



![istio circuit breaking use-case 1](ServiceMesh.assets/istio-circuit-break-p1.png)



#### clean up

```sh
$ ku delete pod/fortio 
  ku delete deployment.apps/httpbin 
  ku delete svc/httpbin
  ku delete pod/curltest

```







### (2) Load balancing pool의 인스턴스의 상태에 기반하는 *circuit break*

n개의 인스턴스를 가지는 load balancing pool 중 오류 발생하거나 응답이 없는 인스턴스를 탐지하여 circuit break를 작동시키는 방법이다.

- 전제 조건
  - hello-server:latest 이미지는 env:RANDOM_ERROR 값의 비율로 랜덤하게 503 에러를 발생하는 로직이 포함되어 있다.
  - 데모를 위해서 hello-server-1, hello-server-2 가 동일 workload 라고 가정한다.



#### 테스트 POD 기동

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/hello/11.hello-pod-svc.yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-server-1
  labels:
    app: hello
spec:
  containers:
  - name: hello-server-1
    image: docker.io/honester/hello-server:latest
    imagePullPolicy: IfNotPresent
    env:
    - name: VERSION
      value: "v1"
    - name: LOG
      value: "1"
---
apiVersion: v1
kind: Pod
metadata:
  name: hello-server-2
  labels:
    app: hello
spec:
  containers:
  - name: hello-server-2
    image: docker.io/honester/hello-server:latest
    imagePullPolicy: IfNotPresent
    env:
    - name: VERSION
      value: "v2"
    - name: LOG
      value: "1"
    - name: RANDOM_ERROR
      value: "0.5"
---
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
  labels:
    app: hello
spec:
  selector:
    app: hello
  ports:
  - name: http
    protocol: TCP
    port: 8080


$ ku apply -f ./istio/hello/11.hello-pod-svc.yaml
pod/hello-server-1 created
pod/hello-server-2 created
service/hello-svc created

```



- 클라이언트용 *pod* 를 설치후 확인해 보자.

```sh
$ ku run curltest --image=curlimages/curl -- sleep 365d
pod/curltest created


# 여러번 call 해보자.
$ ku exec -it curltest -- curl http://hello-svc:8080
Hello server - v1
Hello server - v2
Hello server - v1
Hello server - v2
...


#
#$ wihle true;do ku exec -it curltest -- curl http://hello-svc:8080; sleep 0.1; done


```



#### 테스트 수행 - CB 설정전

- mobaXterm terminal 을 3개 준비하여 Split 화면에서 같이 수행하자.

  - Terminal 1 : hello-server-1 log 추척

    - ```sh
      
      $ ku logs -f hello-server-1 
      
      ```

  - Terminal 2 : hello-server-2 log 추척

    - ```sh
      
      $ ku logs -f hello-server-2
      
      ```

  - Terminal 3 : Client tool 수행

    - 아래 명령 수행

    - ```sh
      
      $ ku exec -it curltest -- curl http://hello-svc:8080
      
      ```

    - 

    

- curltest 컨테이너에서 hello-svc 서비스로 10개를 요청해 보자.

  - v1, v2 각각 5번씩 요청 결과가 조회된다.


```sh

# 20개를 0.1초간격으로 요청해 보자.
$ for i in {1..20}; do ku exec -it curltest -- curl http://hello-svc:8080; sleep 0.1; done


Hello server - v2
Hello server - v1
Hello server - v1
Hello server - v2
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v2
Hello server - v1
Hello server - v2
Hello server - v1
Hello server - v1
Hello server - v1


```

- 전체 20개의 로그가 리턴되었다.
- istio proxy 기반 위에는 트래픽 오류시 다른 POD 로 Retry 기능이 있으므로 에러가 리턴 되지는 않는다.
- 하지만 server-2로그를 확인하면 로그가 존재한다.



- hello-server-1 로그

```sh
$ ku logs -f hello-server-1

Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200



```

200(정상) 16회이다.



- hello-server-2 로그

```sh
$ ku logs -f hello-server-2

Hello server - v2 - 200
Hello server - v2 - 200
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)
Hello server - v2 - 200
Hello server - v2 - 200
Hello server - v2 - 503 (random)
Hello server - v2 - 503 (random)


# 총 12회 중  [ 200(정상) 4회, 503(실패)8회 ]

```

전체 12회 로그가 찍혔고 내부 로직에 따라 50% 확률로 에러 발생했다.

8개의 call이 에러 발생하여 k8s 가 자동으로 server-1 로 재요청된 것을 알 수 있다.

500 에러가 발생하는 서비스에 접근을 일시적으로 차단 시키는 것이 좋다고 판단할 수 있다.



- Kiali 에서는 다음과 같이 조회된다.

![image-20230514195312657](ServiceMesh.assets/image-20230514195312657.png)





#### Circuit breaker 설정

- DestinationRule 를 생성 outlierDetection 스펙을 통해 circuit break 를 정의한다.
  - outlierDetection
    - consecutiveErrors
      - 연속적인 에러가 몇번까지 발생해야 circuit breaker를 동작시킬 것인지 결정
  
    - interval
      - interval에서 지정한 시간 내에  consecutiveError 횟수 만큼 에러가 발생하는 경우 circuit breaker 동작. 즉, 20초 내에 3번의 연속적인 오류가 발생하면 circuit breaker 동작
  
    - baseEjectionTime
      - 차단한 호스트를 얼마 동안 로드밸런서 pool에서 제외할 것인가?
  
    - maxEjectionPercent
      - 네트워크를 차단할 최대 host의 비율
      - 즉, 최대 몇 %까지 차단할 것인지 설정 현재 구성은 2개의 pod가 있으므로, 100%인 경우 2개 모두 차단이 가능
  

```sh
$ cd ~/githubrepo/ktds-edu-k8s-istio

$ cat ./istio/hello/12.hello-dr.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: hello-dr
spec:
  host: hello-svc
  trafficPolicy:
    outlierDetection:
      interval: 1s
      consecutive5xxErrors: 1
      baseEjectionTime: 3m
      maxEjectionPercent: 100


$ ku apply -f ./istio/hello/12.hello-dr.yaml
```

* 설정값 설명
  * 매 interval(1s)마다 스캔하여
  * 연속적으로 consecutiveErrors(1) 번 5XX 에러 가 발생하면
  * baseEjectionTime(3m)동안 배제(circuit breaking) 처리된다.





#### 테스트 수행 - CB 설정후

- 다시 동일한 요청을 하자. 이번에도 20번 호출한다.

```sh

$ for i in {1..20}; do ku exec -it curltest -- curl http://hello-svc:8080; sleep 0.1; done

Hello server - v2
Hello server - v2
Hello server - v1
Hello server - v1
Hello server - v2
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1    <-- 이 지점에서 circuit 이 발동 됨
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1
Hello server - v1

```

- hello-server-1 pod 의 logs follow 하자.

```sh
$ ku logs -f hello-server-1

Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200
Hello server - v1 - 200


```

17회 정상 리턴이다.



- hello-server-2 pod 의 logs  follow 하자.

```sh
$ ku logs -f hello-server-2

Hello server - v2 - 200
Hello server - v2 - 200
Hello server - v2 - 200
Hello server - v2 - 503 (random)

```

* 4회 리턴중 마지막 503 에러 이후 로그가 찍히지 않았다.
* CB 정책에 의해 503 에러 발생후 3분 동안 v2 로 Call 이 가지 않았다.



kiali 의 모습은 아래와 같다.

![image-20230514195643796](ServiceMesh.assets/image-20230514195643796.png)





#### 결론

- *k8s service* `hello-svc` 에 *DestionationRule* `hello-dr` 을 정의하여 오류가 발생하는 인스턴스를 배제(*circuit break*) 한다.
- 1초간격(interval: 1s)으로 응답여부를 탐지하고 연속으로 1회 에러(consecutiveErrors) 가 발생하면 3분간(baseEjectionTime: 3m) *circuit break* 한다.

![istio circuit breaking use-case 2](ServiceMesh.assets/istio-circuit-break-p2.png)





#### [참고] 모든 서비스에 Circuit break 적용

```sh

apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: circuit-breaker-for-the-entire-default-namespace
spec:
  host: "*.default.svc.cluster.local"
  trafficPolicy:
    outlierDetection:
      interval: 1s
      consecutive5xxErrors: 1
      baseEjectionTime: 3m
      maxEjectionPercent: 100

```



#### clean up

```sh
$ ku delete pod/hello-server-1
  ku delete pod/hello-server-2
  ku delete svc/hello-svc
  ku delete dr/hello-dr
  ku delete pod/curltest

# 확인
$ ku get all
```





## 5) bookinfo clean up

필요시 삭제한다.

```sh
$ alias ku='kubectl -n user02'

$ cd ~/githubrepo/ktds-edu-k8s-istio/

# 삭제
$ ku delete -f ./istio/bookinfo/11.bookinfo.yaml
  ku delete -f ./istio/bookinfo/12.bookinfo-gw-vs.yaml
  ku delete -f ./istio/bookinfo/13.destination-rule-all.yaml 
  kubectl -n istio-ingress delete -f ./istio/bookinfo/15.bookinfo-ingress.yaml


# virtual Service 직접 삭제
ku delete vs details
ku delete vs productpage
ku delete vs ratings
ku delete vs reviews


# 확인
$ ku get all


# namespace label 삭제
$ kubectl label --overwrite namespace user02 istio-injection-

```





