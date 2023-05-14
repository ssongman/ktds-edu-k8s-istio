# < 시작전에 >





# 1. 실습 환경 준비(개인PC)



## 1) mobaXterm

KT cloud 서버에 ssh 접근을 위한 터미널이 필요하다. 

다양한 터미널(putty 등)이 있지만 본 실습에서는 mobax term 이라는 free 버젼 솔루션을 사용한다.

- download 위치
  - 링크: https://download.mobatek.net/2312023031823706/MobaXterm_Installer_v23.1.zip

- mobaxterm 실행

![image-20220601194018844](beforebegin.assets/image-20220601194018844.png)



## 2) wsl2

k3s, istio, 설치는 Cluster 당 한번만 가능하다. 그러므로 이러한 설치실습은 본인 PC 에 wsl 에서 진행할 예정이다.

본인 PC 에 WSL이 설치되어 있는지 확인하자.



### (1) 확인 및 설치하는 방법

command 창에서 wsl 명령으로 설치여부를 확인 할 수 있다.

```sh
> wsl -l -v 
```



![image-20220601193023175](beforebegin.assets/image-20220601193023175.png)

- 만약 version 이 1 이라면 아래와 같이 update 수행
  - 참고링크
    - https://docs.microsoft.com/en-us/windows/wsl/install
    - https://docs.microsoft.com/ko-kr/windows/wsl/install-manual
  - PowerShell 에서 실행

```sh
> wsl --install

> wsl --set-version Ubuntu 2

# 기본값으로 설정 변경해도 됨
> wsl --set-default-version 2

# 강제 재기동
> wsl -t Ubuntu

```





- 설치되어 있지 않다면 아래와 같이 설치한다.

  - ```sh
    > wsl --install -d Ubuntu
         <-- 약 10분정도 소요됨.
    
    # 재기동 시도
    # update
    
    
    # 사용자 계정 및 암호 생성
       user: song
       pass: song
    ```
    
    





### (2) WSL 실행하는 방법

실행하는 방법은 아래와 같이 다양하다.  본인에게 편한 방법을 선택하면 되지만 mobaxterm 을 사용하는 것을 추천한다.



#### mobaxterm 에서 실행

* session > WSL 실행

![image-20220601193859958](beforebegin.assets/image-20220601193859958.png)





#### cmd 창에서 바로 실행

- cmd 창에서 `wsl` 명령을 입력하면 바로 default linux 가 실행된다.
- `wsl -u root` 명령으로 root 로 실행 할 수 있다.

![image-20220601193219422](beforebegin.assets/image-20220601193219422.png)



#### windows 터미널로 실행하는 방법

- windows 터미널 설치 : https://docs.microsoft.com/ko-KR/windows/terminal/get-started

  



### (3) 실습자료 downoad

wsl 접속 하는데 문제가 없다면 테스트를 위해서 github 에서 실습 자료를 받아 놓자. 

```sh
# root 접속
## githubrepo directory 생성
$ mkdir ~/githubrepo

$ cd ~/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 92, done.
remote: Counting objects: 100% (92/92), done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 92 (delta 12), reused 88 (delta 12), pack-reused 0
Receiving objects: 100% (92/92), 2.94 MiB | 3.61 MiB/s, done.
Resolving deltas: 100% (12/12), done.


$ ll ~/githubrepo
total 12
drwxr-xr-x 3 song song 4096 May 14 01:59 ./
drwxr-x--- 5 song song 4096 May 14 01:59 ../
drwxr-xr-x 7 song song 4096 May 14 01:59 ktds-edu-k8s-istio/

```





## 3) docker desktop 확인

Container 와 kubernetes 의 차이를 이해하기 위해서 간단한 container 배포 테스트를 진행한다.



### (1) docker destktop 확인

우측 하단  docker desktop  아이콘에서 우클릭후 아래 그림 처럼 Docker Desktop is running 확인

![image-20220601192354841](beforebegin.assets/image-20220601192354841.png)



### (2) docker destktop install

설치되어 있지 않으면 아래와 같이 설치한다.

- 설치 가이드 위치
  - 링크: https://docs.docker.com/desktop/windows/install/



### (3) docker daemon 확인

wsl terminal(or docker 가 실행가능 곳)에서 아래와 같이 version 을 확인하자.

```sh
$ docker version
Client: Docker Engine - Community
 Cloud integration: v1.0.31
 Version:           23.0.5
 API version:       1.42
 Go version:        go1.19.8
 Git commit:        bc4487a
 Built:             Wed Apr 26 16:17:45 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Desktop
 Engine:
  Version:          23.0.5
  API version:      1.42 (minimum version 1.12)
  Go version:       go1.19.8
  Git commit:       94d3ad6
  Built:            Wed Apr 26 16:17:45 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.20
  GitCommit:        2806fc1057397dbaeefbea0e4e17bddfbd388f38
 runc:
  Version:          1.1.5
  GitCommit:        v1.1.5-0-gf19387a
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0  
```

Server version 을 확인할 수 있다면 정상 설치 된 것이다.





### (4) WSL2에서 도커 데스크탑 실행 설정

도커 데스크탑을 설치하고 설정 페이지의 **General** 탭에서 **Use the WSL2 based engine** 옵션을 체크해준다.

최신 버젼부터는 자동으로 체크되어 있으니 확인만 하자.

![img](beforebegin.assets/cc2fa29ced0170be569fa2babb3f37ce853a4a6edaa393ae7d7e6cf0e734809e.m.png)





도커 데스크탑을 설치하고 정상적으로 설정되어있다면, 바로 WSL2 우분투 터미널에서 도커 명령어를 사용할 수 있다.

![image-20230507232851770](beforebegin.assets\image-20230507232851770.png)





## 4) Typora

교육자료(MarkDown 문서)를 typora 로 확인하기를 희망하는 경우 Typora 를 설치한다. 

github site 를 이용하기를 희망한다면 굳이 설치하지 않아도 된다.

하지만  gcp vm 접속을 위한 key 가 필요하므로 아래 git clone 은 수행하도록 하자.



### (1) Typora 설치

- download 위치
  - 링크: https://typora.io/

- Typora 실행



### (2) 교육자료 download

github 에서 교육 자료를 download 하자 

```sh
## githubrepo directory 생성
$ mkdir c:\githubrepo

$ cd c:\githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 92, done.
remote: Counting objects: 100% (92/92), done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 92 (delta 12), reused 88 (delta 12), pack-reused 0
Receiving objects: 100% (92/92), 2.94 MiB | 3.61 MiB/s, done.
Resolving deltas: 100% (12/12), done.

$ dir c:\githubrepo
2023-05-07  오후 09:52    <DIR>          .
2023-05-07  오후 09:52    <DIR>          ktds-edu-k8s-istio

```



### (3) 교육자료 실행

typora 를 실행하여 c:\githubrepo\ktds-edu-k8s-istio/README.md  를 load 한다.





# 2. 실습 환경 준비(Cloud)

## 1) Cloud 개요

WSL 에서의 k3s 는 한개의 노드를 사용한 초간편 Cluster이다. 하지만 Istio 와 Istio 모니터링 실습을 위해서는 좀더 높은 Cluster 사양이 필요하다.

원할한 실습을 위해서 KT Cloud 에 k3s, istio 등 관련 셋팅을 미리 해 놓은 상태이다.

개인별 계정과 개인별 Namespace 에서 다양한 실습을 진행할 것이다.  이를 위해 서버 접근정보를 이해하고 개인 계정을 확인하자.

부하를 분산하기 위해 사용자 그룹을 나눠서 master01, 02, 03 서버로 분리한다.



## 2) 수강생별 Namespace 및 접속 서버 주소

| Namespace |  이름  | GCP 서버 | GCP 서버 주소 | 비고 |
| :-------: | :----: | :------: | :-----------: | :--: |
|  user01   | 송양종 | master01 | 35.224.158.79 |      |
|  user02   |        | master01 | 35.224.158.79 |      |
|  user03   |        | master01 | 35.224.158.79 |      |
|  user04   |        | master01 | 35.224.158.79 |      |
|  user05   | 김상훈 | master01 | 35.224.158.79 |      |
|  user06   | 송하영 | master01 | 35.224.158.79 |      |
|  user07   |        | master01 | 35.224.158.79 |      |
|  user08   |        | master01 | 35.224.158.79 |      |
|  user09   |        | master01 | 35.224.158.79 |      |
|  user10   |        | master01 | 35.224.158.79 |      |
|  user11   |        | master02 | 35.202.110.75 |      |
|  user12   |        | master02 | 35.202.110.75 |      |
|  user13   |        | master02 | 35.202.110.75 |      |
|  user14   |        | master02 | 35.202.110.75 |      |
|  user15   |        | master02 | 35.202.110.75 |      |
|  user16   |        | master02 | 35.202.110.75 |      |
|  user17   |        | master02 | 35.202.110.75 |      |
|  user18   |        | master02 | 35.202.110.75 |      |
|  user19   |        | master02 | 35.202.110.75 |      |
|  user20   |        | master02 | 35.202.110.75 |      |
|  user21   |        | master03 | 34.136.12.122 |      |
|  user22   |        | master03 | 34.136.12.122 |      |
|  user23   |        | master03 | 34.136.12.122 |      |
|  user24   |        | master03 | 34.136.12.122 |      |
|  user25   |        | master03 | 34.136.12.122 |      |
|  user26   |        | master03 | 34.136.12.122 |      |
|  user27   |        | master03 | 34.136.12.122 |      |
|  user28   |        | master03 | 34.136.12.122 |      |
|  user29   |        | master03 | 34.136.12.122 |      |
|  user30   |        | master03 | 34.136.12.122 |      |





## 3) ssh (Mobaxterm) 실행

- 메뉴 : session > SSH 

- Romote host
  - 개인별로 접근 주소가 틀리다.  아래 수강생별 접속 주소를 확인하자.
  - master01 : 35.224.158.79
  - master02 : 35.202.110.75
  - master03 : 34.136.12.122

- User : ktdseduuser
- Port : 22
- Use private key(ssh key) : C:\githubrepo\ktds-edu-k8s-istio\beforebegin\gcp-vm-key\ktdseduuser



![image-20230514022214007](beforebegin.assets/image-20230514022214007.png)

빨간색 영역을 주의해서 입력한후 접속하자.






## 3) 실습자료 download

접속 완료 하였다면 테스트를 위해서 git clone 으로 실습 자료를 받아 놓자.

```sh
# 본인 계정
## githubrepo directory 생성
$ mkdir -p ~/user01/githubrepo

$ cd ~/user01/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 69, done.
remote: Counting objects: 100% (69/69), done.
remote: Compressing objects: 100% (55/55), done.
remote: Total 69 (delta 15), reused 62 (delta 11), pack-reused 0
Unpacking objects: 100% (69/69), 1.63 MiB | 4.09 MiB/s, done.

$ ll ~/user01/githubrepo
drwxrwxr-x 7 ktdseduuser ktdseduuser 4096 May 13 17:36 ktds-edu-k8s-istio/

$ cd ~/user01/githubrepo/ktds-edu-k8s-istio/
```

