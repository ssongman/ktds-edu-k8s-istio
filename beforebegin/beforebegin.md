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

- 다운로드 주소 : https://github.com/git-for-windows/git/releases/download/v2.45.2.windows.1/Git-2.45.2-64-bit.exe
- 참조 링크 : https://git-scm.com/





## 3) Typora 설치

교육자료(MarkDown 문서)를 typora로 확인하기 위해 Typora를 설치한다. 

github site 를 직접 확인해도 되긴 하지만 각종 실습 자료를 직접 수정해야 하므로 가능한 Typora 를 이용하자.



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

실습을 위해서 해당 자료를 download 하여 markdown 전용 viewer 인 Typora 로 오픈하여 실습에 참여하자.



## 1) 개인 PC에 교육자료 download

gitbash 실행후 command 명령어로 아래와 같이 디렉토리를 생성후 git clone 으로 download 하자.

```sh
## githubrepo directory 생성
$ mkdir -p /c/githubrepo

$ cd /c/githubrepo

$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 597, done.
remote: Counting objects: 100% (32/32), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 597 (delta 22), reused 28 (delta 20), pack-reused 565
Receiving objects: 100% (597/597), 3.85 MiB | 9.97 MiB/s, done.
Resolving deltas: 100% (326/326), done.



$ ll /c/githubrepo
drwxr-xr-x 1 송양종 197121 0 Jun  6 11:06 ktds-edu-k8s-istio/

```



만약 교육중 자료가 변경(오타 변경 등의 사유로) 되어 다시 받아야 하는 경우 가 있을 경우 해당 위치에서 git pull 만 다시 받도록 하자.

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



## 1) 개인 VM 서버 주소 확인- ★

원할한 실습을 위해서 개인별 한개씩 VM 이 할당되어 있다.  해당 노드에 kubernetes 를 설치 및 다양한 실습을 진행할 것이다.

수강생별 개인 VM Server 접속 주소를 확인하자. 또한 KtdsEduCluster 에서 사용할 개인별 Namespace 를 확인하자.

| 이름   | 소속            | Email                   | Namespace | VM  Server   | VM  Server IP  | 비고 |
| ------ | --------------- | ----------------------- | --------- | ------------ | -------------- | ---- |
| 송양종 | AX성장전략팀    | 강사1                   | user01    | ke-bastion01 |                |      |
| 송양종 | AX성장전략팀    | 강사2                   | user02    | ke-bastion02 |                |      |
| 송양종 | AX성장전략팀    | 강사3                   | user03    | ke-bastion03 |                |      |
| 강문정 | DX개발팀        | moonjung.kang@kt.com    | user04    | ke-bastion04 | 4.217.255.28   |      |
| 김경호 | 보안정책팀      | gyeong-ho.kim@kt.com    | user05    | ke-bastion05 | 20.41.80.183   |      |
| 김재현 | DX개발팀        | kim.db@kt.com           | user06    | ke-bastion06 | 20.41.86.64    |      |
| 김준엽 | AI구독서비스팀  | junyeob.kim@kt.com      | user07    | ke-bastion07 | 52.231.103.156 |      |
| 김준태 | AI구독서비스팀  | joontae.kim@kt.com      | user08    | ke-bastion08 | 52.231.95.179  |      |
| 김태형 | AI/DX기술팀     | taehyeong.kim@kt.com    | user09    | ke-bastion09 | 4.217.233.128  |      |
| 류경하 | 아키텍처팀      | kyungha.ryu@kt.com      | user10    | ke-bastion10 | 4.217.233.235  |      |
| 박창기 | 메시징플랫폼팀  | flying@kt.com           | user11    | ke-bastion11 | 4.217.234.92   |      |
| 백승연 | ICT  CoE팀      | seung_yeon.baek@kt.com  | user12    | ke-bastion12 | 4.230.1.78     |      |
| 이성민 | B2B  CRM팀      | lee.seoungmin@kt.com    | user13    | ke-bastion13 | 4.230.1.99     |      |
| 임성식 | ICIS  Tr 빌링팀 | sslim@kt.com            | user14    | ke-bastion14 | 20.249.168.44  |      |
| 장시영 | ICIS  Tr 고객팀 | siyoung.jang0409@kt.com | user15    | ke-bastion15 | 4.217.234.171  |      |
| 정문경 | ICT  CoE팀      | mungyeong.jeong@kt.com  | user16    | ke-bastion16 | 20.249.169.61  |      |





## 2) SSH (Mobaxterm) 실행

Mobaxterm 을 실행하여 VM 접속정보를 위한 신규 session 을 생성하자.

- 메뉴
  - session  : 상단 좌측아이콘 클릭

  - SSH : 팝업창 상단 아이콘 클릭





![image-20230514022214007](beforebegin.assets/image-20230514022214007.png)

빨간색 영역을 주의해서 입력한후 접속하자.



- Romote host
  - 개인별로 접근 주소가 다르므로 위 수강생별  VM  Server IP 주소를 확인하자.
  - ex)  bastion02 : 54.180.160.149  (샘플)

- User
  - Specify username 에 Check
  - User : ubuntu 입력

- Port : 22
- Advanced SSH settings
  - Use private key : C:\githubrepo\ktds-edu-k8s-istio\beforebegin\vm-key\ktdsedu-employee.pem
    - 교육자료 Download 되는 자료에 위 key가 포함되어 있음






## 3) VM 서버에서 실습자료 download

실습 테스트를 위해서 실습 자료를 받아 놓자.

이미 각자 VM에 해당 교육자료가  git clone 되어 있으므로 git pull 로 최신 데이터로 update 만 진행하자

```sh

# 최신 데이터를 한번 더 받는다.
$ cd ~/githubrepo/ktds-edu-k8s-istio/
$ git pull

```



#### git pull 실패한 경우

만약 기 수정 파일이 존재하여 pull이 잘 안되는 경우는 삭제후 다시 clone

```sh


# < 만약 기 수정 파일이 존재하여 pull이 잘 안되는 경우는 삭제후 다시 clone >

# 1) 모두 삭제
$ rm -rf ~/githubrepo/ktds-edu-k8s-istio/

# 2) git clone 수행
$ cd ~/githubrepo
$ git clone https://github.com/ssongman/ktds-edu-k8s-istio.git
Cloning into 'ktds-edu-k8s-istio'...
remote: Enumerating objects: 446, done.
remote: Counting objects: 100% (73/73), done.
remote: Compressing objects: 100% (53/53), done.
remote: Total 446 (delta 34), reused 50 (delta 18), pack-reused 373
Receiving objects: 100% (446/446), 3.86 MiB | 7.60 MiB/s, done.
Resolving deltas: 100% (220/220), done.

# 3) 확인
$ ll ~/githubrepo
drwxrwxr-x 7 ktdseduuser ktdseduuser 4096 May 13 17:36 ktds-edu-k8s-istio/

$ cd ~/githubrepo/ktds-edu-k8s-istio/

```



## [참고] git repo 초기화 방법

수정된 파일이 존재하여 git pull 이 잘 안될때는 삭제후 다시 Clone 하는 방법도 있지만

내용이 많다거나 다른 사유로 인해 clone 작업이 힘들 경우 아래와 같은 명령어를 사용해도 된다.

```sh
# 1) 마지막 commit hash 값으로 reset 처리
## 아직 staged 에 올라가지 않은 수정파일,  untracked file 까지 모두 사라진다.
$ git reset --hard HEAD~
$ git pull



# 2) untrackted file 을 초기화 해야 하는 경우
$ git clean -f -d
$ git pull


# 3) 파일단위로 restore 를 원할 경우
$ git restore modified_file
$ git pull


# 4) stash
# stash 는 내가 수행한 작업을 commit 하기전 임시로 저장해 놓는 명령이다.
$ git stash
$ git pull


# 참고
## commit log 확인
$ git log 
```

