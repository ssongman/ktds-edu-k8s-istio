# < 시작전에 >





# 1. 실습 환경 준비(개인PC)



## 1) mobaXterm 설치

Cloud VM에 접근하기 위해서는 터미널이 필요하다.

CMD / PowerShell / putty 와 같은 기본 터미널을 이용해도 되지만 좀더 많은 기능이 제공되는 MobaxTerm(free 버젼) 을 다운로드 하자.



- download 위치
  - 링크: https://download.mobatek.net/2312023031823706/MobaXterm_Installer_v23.1.zip

- mobaxterm 실행

![image-20220601194018844](beforebegin.assets/image-20220601194018844.png)





## 2) gitbash 설치

교육문서를 다운로드 받으려면 Git Command 가 필요하다. Windows 에서는 기본 제공되지 않아 별도 설치 해야 한다.

- 다운로드 주소 : https://github.com/git-for-windows/git/releases/download/v2.40.1.windows.1/Git-2.40.1-64-bit.exe
- 참조 링크 : https://git-scm.com/





## 3) Typora 설치

교육자료(MarkDown 문서)를 typora 로 확인하기를 희망하는 경우 Typora 를 설치한다. 

github site 를 이용하기를 희망한다면 굳이 설치하지 않아도 된다.

하지만  gcp vm 접속을 위한 key 가 필요하므로 아래 git clone 은 수행하도록 하자.



### (1) Typora 설치

- 참고
  - 링크: https://typora.io/

- download 위치
  - 다운로드주소 : https://download.typora.io/windows/typora-setup-x64.exe

- Typora 실행



### (2) Typora 환경설정

원할한 실습을 위해 코드펜스 옵션을 아래와 같이 변경하자.

- 코드펜스 설정
  - 메뉴 : 파일 > 환경설정 > 마크다운 > 코드펜스
    - 코드펜스에서 줄번호 보이기 - check
    - 긴문장 자동 줄바꿈 : uncheck




![image-20220702154444773](beforebegin.assets/image-20220702154444773.png)

- 개요보기 설정
  - 메뉴 : 보기 > 개요
    - 개요 : check






# 2. 교육문서 Download

해당 교육문서는 모두 markdown 형식으로 작성되었다.  Chrome Browser 에서 github 문서를 직접 확인해도 된다.

하지만 실습을 따라가다 보면 개인별로 수정해야 할 부분이 있는데 web browser 에서는 수정이 안되기 때문에 수정이 용이한 환경이 훨씬 좋을 것이다.

좀더 효율적인 실습을 위해서 해당 자료를 download 하여 markdown 전용 viewer 인 Typora 로 오픈하여 실습에 참여하자.



## 1) 개인 PC에 교육자료 download

gitbash 실행후 command 명령어로 아래와 같이 디렉토리를 생성후 git clone 으로 download 하자.

```sh
## githubrepo directory 생성
$ mkdir -p /c/githubrepo

$ cd /c/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 427, done.
remote: Counting objects: 100% (54/54), done.
remote: Compressing objects: 100% (39/39), done.
remote: Total 427 (delta 26), reused 36 (delta 14), pack-reused 373
Receiving objects: 100% (427/427), 3.84 MiB | 8.82 MiB/s, done.
Resolving deltas: 100% (212/212), done.


$ ll /c/githubrepo
drwxr-xr-x 1 ssong 197609 0 Aug 27 15:19 ktds-edu-k8s-istio/

```



만약 교육중 (오타 변경 등의 사유로) 자료가 변경되어 다시 받아야 하는 경우 가 있을 경우 해당 위치에서 git pull 만 다시 받도록 하자.

```sh
$ cd /c/githubrepo/ktds-edu-k8s-istio

$ git pull

```









## 2) Typora 로 readme.md 파일오픈

- typora 로 오픈
  - 파일열기(Ctrl + O)  후 아래 파일 오픈


```
## typora 에서 아래 파일 오픈

C:\githubrepo\ktds-edu-k8s-istio\README.md
```







# 3. 실습 환경 준비(Cloud)

본 교육 과정에서의 모든 실습은 Cloud 에서 수행할 것이다.



## 1) 개인 VM 서버 주소 확인

원할한 실습을 위해서 개인별 한개씩 VM 이 할당되어 있다.  해당 노드에 kubernetes 를 설치 및 다양한 실습을 진행할 것이다.

수강생별 개인 VM Server 접속 주소를 확인하자. 또한 KtdsEduCluster 에서 사용할 개인별 Namespace 를 확인하자.



|  이름  |       소속팀       | VM Server | VM  Server IP | Namespace | 비고 |
| :----: | :----------------: | :-------: | :-----------: | :-------: | :--: |
| 송양종 | ICIS Tr 아키텍처팀 | bastion01 |               |  user01   |      |
| 송양종 | ICIS Tr 아키텍처팀 | bastion02 |               |  user02   |      |
| 이도겸 | ICIS Tr 아키텍처팀 | bastion03 |               |  user03   |      |
| 김수진 | ICIS Tr 아키텍처팀 | bastion04 |               |  user04   |      |
| 김영진 |   플랫폼인프라팀   | bastion05 |               |  user05   |      |
| 김준엽 |     AI솔루션팀     | bastion06 |               |  user06   |      |
| 김태산 |     AWS기술TF      | bastion07 |               |  user07   |      |
| 김하성 |      SI개발팀      | bastion08 |               |  user08   |      |
| 박동기 |  인프라품질혁신TF  | bastion09 |               |  user09   |      |
| 안정민 | 금융결제DX플랫폼팀 | bastion10 |               |  user10   |      |
| 윤상용 |  네트워크IT개발팀  | bastion11 |               |  user11   |      |
| 윤여인 |        PM팀        | bastion12 |               |  user12   |      |
| 윤영준 |     보안사업팀     | bastion13 |               |  user13   |      |
| 윤현철 |  네트워크IT개발팀  | bastion14 |               |  user14   |      |
| 이경은 |   플랫폼인프라팀   | bastion15 |               |  user15   |      |
| 이은영 |     OSS개발1팀     | bastion16 |               |  user16   |      |
| 이정운 |     AWS기술TF      | bastion17 |               |  user17   |      |
| 이준경 |      SI개발팀      | bastion18 |               |  user18   |      |
| 조민정 |     CRM사업팀      | bastion19 |               |  user19   |      |
| 조영찬 |   ICIS Tr 빌링팀   | bastion20 |               |  user20   |      |





## 2) SSH (Mobaxterm) 실행

Mobaxterm 을 실행하여 VM 접속정보를 위한 신규 session 을 생성하자.

- 메뉴
  - session  : 상단 좌측아이콘 클릭

  - SSH : 팝업창 상단 아이콘 클릭





![image-20230514022214007](beforebegin.assets/image-20230514022214007.png)

빨간색 영역을 주의해서 입력한후 접속하자.



- Romote host
  - 개인별로 접근 주소가 다르므로 위 수강생별  VM  Server IP 주소를 확인하자.
  - ex)  bastion03 : 35.247.230.92  (샘플)

- User
  - Specify username 에 Check
  - User : ktdseduuser  입력

- Port : 22
- Advanced SSH settings
  - Use private key : C:\githubrepo\ktds-edu-k8s-istio\gcp-vm-key\ktdseduuser
    - 교육자료 Download 되는 자료에 위 key가 포함되어 있음






## 3) 실습자료 download

접속 완료 하였다면 테스트를 위해서 git clone 으로 실습 자료를 받아 놓자.

```sh
## githubrepo directory 생성
$ mkdir -p ~/githubrepo

$ cd ~/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 69, done.
remote: Counting objects: 100% (69/69), done.
remote: Compressing objects: 100% (55/55), done.
remote: Total 69 (delta 15), reused 62 (delta 11), pack-reused 0
Unpacking objects: 100% (69/69), 1.63 MiB | 4.09 MiB/s, done.

$ ll ~/githubrepo
drwxrwxr-x 7 ktdseduuser ktdseduuser 4096 May 13 17:36 ktds-edu-k8s-istio/

$ cd ~/githubrepo/ktds-edu-k8s-istio/


## 만약 ktds-edu-kafka-redis 이미 존재한다면 최신 데이터를 한번 더 받는다.
$ cd ~/githubrepo/ktds-edu-k8s-istio/
$ git pull

```

