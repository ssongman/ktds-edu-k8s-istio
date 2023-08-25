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



## 1) 교육자료 download

gitbash 실행후 command 명령어로 아래와 같이 디렉토리를 생성후 git clone 으로 download 하자.

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





## 2) Typora 로 readme.md 파일오픈

- typora 로 오픈

```
## typora 에서 아래 파일 오픈

C:\githubrepo\ktds-edu-k8s-istio\README.md
```







# 3. 실습 환경 준비(Cloud)

본 교육 과정에서의 모든 실습은 Cloud 에서 수행할것이다.



## 1) 개인 VM 서버 주소 확인

원할한 실습을 위해서 개인별 한개씩 VM 이 할당되어 있다.  해당 노드에 kubernetes 를 설치 및 다양한 실습을 진행할 것이다.

수강생별 개인 VM Server 접속 주소를 확인하자.



| 수강생 | VM Server | VM  Server IP | 비고 |
| :----: | :-------: | :-----------: | :--: |
| 송양xx | bastion01 |  34.xx.xx.xx  |      |
| 송양xx | bastion02 |               |      |
| 이도겸 | bastion03 |               |      |
|        | bastion04 |               |      |
|        | bastion05 |               |      |
|        | bastion06 |               |      |
|        | bastion07 |               |      |
|        | bastion08 |               |      |
|        | bastion09 |               |      |
|        | bastion10 |               |      |
|        | bastion11 |               |      |
|        | bastion12 |               |      |
|        | bastion13 |               |      |
|        | bastion14 |               |      |
|        | bastion15 |               |      |
|        | bastion16 |               |      |
|        | bastion17 |               |      |
|        | bastion18 |               |      |
|        | bastion19 |               |      |
|        | bastion20 |               |      |





## 2) KtdsEduCluster Namespace 확인

KtdsEduCluster 에서 사용할 개인별 Namespace 를 확인하자.

| 수강생 | Namespace | 비고 |
| :----: | :-------: | :--: |
| 송양xx |  user01   |      |
| 송양xx |  user02   |      |
| 이도겸 |  user03   |      |
|        |  user04   |      |
|        |  user05   |      |
|        |  user06   |      |
|        |  user07   |      |
|        |  user08   |      |
|        |  user09   |      |
|        |  user10   |      |
|        |  user11   |      |
|        |  user12   |      |
|        |  user13   |      |
|        |  user14   |      |
|        |  user15   |      |
|        |  user16   |      |
|        |  user17   |      |
|        |  user18   |      |
|        |  user19   |      |
|        |  user20   |      |





## 3) SSH (Mobaxterm) 실행

Mobaxterm 을 실행하여 VM 접속정보를 위한 신규 session 을 생성하자.

- 메뉴
  - session  : 상단 좌측아이콘 클릭

  - SSH : 팝업창 상단 아이콘 클릭





![image-20230514022214007](beforebegin.assets/image-20230514022214007.png)

빨간색 영역을 주의해서 입력한후 접속하자.



- Romote host
  - 개인별로 접근 주소가 다르므로 위 수강생별  VM  Server IP 주소를 확인하자.
  - ex)  bastion03 : 35.247.230.92

- User
  - Specify username 에 Check
  - User : ktdseduuser  입력

- Port : 22
- Advanced SSH settings
  - Use private key : C:\githubrepo\ktds-edu-kafka-redis\gcp-vm-key\ktdseduuser
    - 교육자료 Download 되는 자료에 위 key가 포함되어 있음






## 4) 실습자료 download

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

