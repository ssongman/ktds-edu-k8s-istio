

# Kubernetes,ServiceMesh

> ktds Container 기반 K8s,  istio 교육자료

본 교육은 Container를 기반으로 한 Kubernetes, ServiceMesh(istio)를 학습하는 과정으로 docker, kubernetes, git, grafana, kiali, jeager 등 다양한 솔루션들에 대해서 알아보며 각각 실습을 수행한다.

문의: 송양종( yj.song@kt.com / ssongmantop@gmail.com )






# 1. 시작전에 ( [가이드 문서 보기](./beforebegin/beforebegin.md) )  



## 1) 실습 환경 준비(개인PC)

- 실습에 필요한 tool 준비
  - mobaxterm 설치
  - wsl2 설치
    - 실습자료  Download (개인PC에서 실습용 )
  - docker desktop 설치
  - typora 설치
    - 교육자료  Download(교육자료 및 ssh key )
  



## 2) 실습 환경 준비(Cloud)

- Cloud 개요
- 수강생별 Namespace 매핑
- ssl 접속 확인
- 실습자료 Download(Cloud에서 실습용 )






# 2. Class1: kubernetes 맛보기 ( [가이드 문서 보기](./kubernetes/kubernetes.md) )  



## 1) Kubernetes 란 무엇인가?

- k8s 개요

- k8s 이전에는?

- 왜 k8s 가 필요한가?



## 2) [개인PC] Docker 실습

- sample app 실행
- Scale Out
- 부하분산 고민
- clean up



## 3) [개인PC] k3s 설치

- k3s 란?
- wsl 에 k3s 설치



## 4) [개인PC] Kubernetes 실습

- 개인 Namespace 확인
- sample app deploy
  - Deployment
  - Service 
  - Scale Out
  - Ingress



## 5) [Cloud] Kubernetes 실습

- Cloud 접속
- 개인 Namespace 확인
- Sample app deploy
  - Deployment/Service
  - Scale Out
  - Ingress






# 3. Class2: ServiceMesh ( [가이드 문서 보기](./istio/ServiceMesh.md) )  



## 1) Service Mesh 란?



## 2) Istio 란?



## 3) [개인PC] 실습

- helm 설치
- helm 을 이용한 Istio 설치
- sample app sidecar inject
  - userlist application을 활용하여 istio sidecar inject 에 대한 이해도를 높인다.



## 4) [Cloud] 실습

- sample app (bookinfo) install
  - bookinfo application 을 활용한 실습을 통해서 istio 를 이해한다.
  - bookinfo 는 온라인 서점의 단일 카탈로그 항목과 유사한 도서에 대한 정보를 표시하는 app 이다.
- Monitoring
  - Grafana
  - Kiali
  - Jaeger



## 5) [Cloud] 실습(Traffic control)

- Traffic Shifting
  - 서비스별로 트래픽의 가중치를 조정하므로서 특정 버전에서 다른 버전으로 트래픽을 이동하는 방법을 제어할 수 있다.
- Request Routing
  - 여러 버전의 마이크로서비스로 동적으로 라우팅하는 방법을 확인할 수 있다.
- Fault Injection
  - application 의 복원력을 테스트하기 위해서 결함을 주입할 수 있다.
- Circuit Breaking
  - istio는 Connection pool 과   Load balancing pool 기반의 circuit breaking 기능을 제공한다.

