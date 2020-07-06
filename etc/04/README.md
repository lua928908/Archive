# Docker 공부하기
## 도커란 무엇인가?

![도커 설명](./images/99DEAB4D5B652E051B.png)

<br>

도커의 이미지는 고래가 컨테이너를 쌓고있는 모습이다. 기본적인 컨셉이 그러하다 고래가 도커이고 그위에 컨테이너가 있다 컨테이너 안에는 서버에서 사용하는데
필요한 어플리케이션들이 이미지라는 형태로 정의되어 있다.
도커는 논리적인 구분이다. 물리적으로 서버가 쪼개지거나 하는것은 아니다. Host OS위에 도커가 올라가고 도커위에
내가 필요로하는 컨테이너인 프로세스(웹서버, DB, Git 등등)가 떠있는 것이다.

도커는 서버를 세팅하기위해 필요한 여러가지 도구들을 도커 이미지라는 형태로 관리하는 것이다.
다양한 OS에 여러 어플리케이션이 올려지고 이런것을 컨테이너 라고 부른다.
도커는 2013년 3월 산타클라라에서 열린 Pycon Conference에서 처음 발표되었고 리눅스 베이스 이며 go lang으로 만들어져 있다.
64비트 이므로 윈도우에서 사용한다면 윈7  미만에서 사용할 수 없다.

1. 윈도우, 리눅스 상관없이 서버환경을 구축하도록 도커파일을 통해 관리한다.
2. 설정되어 있는대로 필요 프로그램을 쉽게 다운받고 사용할 수 있게 해준다.



## 도커 설치하기

윈도우10
    - 처음 도커를 설치하려고하면 `Docker Desktop requires Windows 10 Pro/Enterprise (15063+) or Windows 10 Home (19018+).` 이런 에러가 발생할 것이다. [여기](https://github.com/docker/toolbox/releases)에서 Docker toolbox를 설치해주자
    - 정상적인 실행을 위해서는, 작업관리자 '성능' 탭에서 '가상화:사용'이라고 표시되면 도커를 사용할 수 있다.
    - 툴박스를 설치했으면 `Docker Quickstart Terminal`이라는 프로그램을 실행해 엔터를 눌러주자 터미널창에서 creating이 시작되며 VirtualBox를 설치할 것 이다.
    - 반응이 없는경우 엔터를 입력하자, 마지막에 고래모양이 나오면서 `Start interactivi shell`이라고 나오면 정상적으로 설치가 완료되었다.
    - 도커 명령어는 `Windows Powershell`이 아닌 `Docker Quickstart Terminal`에서 실행해야 한다. 네임스페이스가 다르다. 
    - 이번엔 Docker Desktop을 설치하다 GUI 환경에서 도커를 사용할 수 있다. [Docker for Windows Only](https://docs.docker.com/docker-for-windows/install/) 주의점이 있다면 윈도우10 Pro 이상이어야 한다 Home버전에서는 Hyper-5를 사용할 수 없어서 도커데스크톱 사용이 안된다. 
    - [참고 블로그](https://steemit.com/kr/@mystarlight/docker)를 확인해보면 좋을 것 이다.
    
맥
    - [Docker for MAC](https://docs.docker.com/docker-for-mac/install/)
    
<br>

![도커](./images/주석%202020-07-05%20154236.png)

도커는 리눅스 환경에서 실행되는데 윈도우는 리눅스가 아니기 때문에 윈도우에서 도커를 설치하면
오라클 버추얼박스 라는것을 추가로 띄워서 그위에 도커를 실행시키게 된다.

## 도커 명령어

| 커맨드 | 기능 |
| ------ | --- |
| `docker run example` | 이미지를 찾아 실행한다. (없으면 도커허브에서 풀링) |
| `docker container run <docker-image-name> <command>` | 도커 컨테이너를 실행한다. 위 커맨드와 의미적으로 동일하다.(아마도) [참고](https://okky.kr/article/735452?note=2016019)  |
| `docker ps` | 현재 실행중인 컨테이너 리스트를 확인할 수 있다. |
| `docker ps -a` | 실행중인 도커 컨테이너와 중지된 컨테이너까 정보를 확인할 수 있다. |
| `docker system df` | 컨테이너가 얼만큼의 디스크를 사용하는지 정보를 알려준다. |
| `docker prune` | 실행이 중지되어 있는 컨테이너들을 삭제한다. |
| `docker inspect nginx` | nginx 이미지를 볼 수 있다. |
| `docker exec -it ubuntush cat /etc/hosts` | 이미 실행되고 있는 ubuntush라는 컨테이너에 `cat /etc/hosts`라는 실행을 명령할 수 있다. |
| `docker rename "ubuntush" "ub"` | tbuntush라는 이름의 컨테이너 이름을 ub로 변경 |
| `docker stop \`docker ps -q\` ` | docker ps -q를 하면 실행중인 컨테이너의 id를 가져오는데 이걸 stop으로 전달해서 모두 중지시키는 명령어이다. |

<br>
<br>

## 도커 명령어 의미 익히기

`Docker Quickstart Terminal`을 실행하고 `docker run hello-world`을 입력해보자 `
`Unable to find image 'hello-world:latest' locally` 라는 문구가 나오더니 다운로드를 할 것이다.
로컬상에서 hello-world라는 이미지를 찾을 수 없어 도커허브에서 pulling 한것이다. 깃에서의 풀은 코드를 받는것이지만 도커에서는 이미지를 받는것이다.
`https://hub.docker.com/` 이곳에서 도커 이미지를 공유할 수 있다. 좀더 알아보고 싶다면 `https://docs.docker.com/get-started/` 이곳을 확인해보라는 메세지가 나온다.
즉 우리는 방금 도커허브에서 헬로월드 예제를 다운받아 실행해본 것이다.

이번에는 `docker ps -a`를 실행해보자

![도커 커맨드 실행](./images/주석%202020-07-05%20110042.png)

위 이미지 처럼 컨테이너의 상태와 언제만들어졌는지 status에선 언제 실행되었는지가 표기된다. names는 정해준 값이 없기 때문에 임의에 이름이 부여되어 있다.

`docker-machine.exe ls`를 실행해보면 실행되고 있는 버추얼박스와 정보를 확인할 수 있다.


```
 // 두 코드의 의미는 같다, container 생략 가능
 docker run --name webserver -d -p 3000:80 nginx 
 docker container run --name webserver -d -p 3000:80 nginx 
```
위 코드에서의 container는 생략이 가능하다 즉 docker run .... 으로 작성할 수 있다.
run 다음의 --name 은 컨테이너에 webserver라는 이름을 붙이는 것이다. -d는 Detachment를 의미한다. 분리시킨다는 의미다
백그라운드로 실행할 지 포그라운드로 실행할지를 의미한다. -d 옵션으로 백그라운드에서 실행하게 할 수 있다. -p는 포트를 의미하고
-p 3000:80 이니까 nginx의 80번 포트를 내 pc의 3000번 포트에 할당하는 것이다. 기본적으로 1000 이하의 포트는 시스템에서 사용한다. 마지막 nginx는 이미지의 이름이다.
 
위 코드를 실행해고 `docer ps`를 입력하면 지금 nginx 컨테이너가 실행중임을 알 수 있다.
<br>

위 코드를 실행했는데 localhost로 접속해보아도 `Welcome to nginx!` 라는 문구가 보이지 않고 연결할 수 없다는 에러가 나오는가?

<br>

![오라클 vm 설정](./images/주석%202020-07-05%20162206.png)
그렇다면 Oracle VM VirtualBox 앱을 실행 -> 설정 -> 네트워크 -> 고급 -> Port Forwarding으로 들어가 위 이미지처럼 설정해주자.

윈도우에서 도커를 쓰는경우 리눅스가 아니므로 오라클 VM 위에서 도커가 동작한다고 했다, 도커 컨테이너가 바로 진입하지 못하기 때문에
Port Forward로 일종의 Bridge(다리)를 놓아주는 것이다.


<br>

```
docker run -it --name "test1" centos /bin/cal
```
-it란 i는 인풋이다 화면상에 입력을 받기위함이고 t는 tty라고해서 standard out 화면상의 표준출력이다.
-it를 입력하면 인터랙티브한 대화형으로 실행할 수 있다. test1이라는 컨테이너이름을 주었다, 이름이 없으면 임의에 이름이 붙게된다.
centos 이미지를 실행하는데 마지막 /bin/cal은 명령이다.
즉, centos기반으로 이미지를 실행시켜서 /bin/cal을 실행하라는 명령이 되는 것이다.
위 코드를 실행하면 캘린더가 실행되게 된다.

<br>

```
docker run -it --name "centossh" centos /bin/bash
```
이번에는 centos의 쉘을 실행해보자 리눅스에서 기본 디렉토리가 /bin이므로 마지막 명령은 bash만 써도 된다.
--name을 지정해줄 때 ""를 쓰지않아도된다. 하지만 띄어쓰기가 들어가는경우 따옴표로 구분해주어야 한다.
위 코드를 실행하면 이번엔 도커 터미널이 다운받은 centos 이미지의 shell을 실행한 모습이 나오게 된다.
centos의 네임스페이스로 들어가게 되는 것이다. 이상태로 `whoami` 혹은 `ls` 명령으로 리눅스 명령을 실행 할 수 있다.

<br>

```
# ubuntush 라는 컨테이너를 만들었다.
docker run -it --name "ubuntush" ubuntu /bin/bash

# 실행은 되지만 우분투 쉘로 들어가지는 않는다.
docker start ubuntush

# 들어가고 싶다면 실행중인 상태에서 attach를 사용해 우분투 쉘로 들어갈 수 있다. 
docker attach ubuntush

# attach로 들어간 상태에서 우분투를 종료하지않고 터미널에서만 빠져 나오고 싶다면 Ctrl+p + Ctrl+Q 를 눌러주면 도니다.
```
Ctrl+p + Ctrl+Q를 누르면 실행되는 컨테이너를 종료하지않고 터미널에서 빠져나올 수 있다. (detach)
attach라는 명령어를 주면 실행되는 ubuntu나 centos에 터미널로 들어갈 수 있다. (ubuntush가 실행중이여야 들어갈 수 있다.)

<br>

```
 # cp를 통해 클라이언트의 파일을 도커컨테이너로 전달할 수 있다, 혹은 반대도 가능하다.
 docker [container] cp <container-name>:<path> <client-path>
 docker [container] cp <client-file> <container-name>:<path>
 ex)
 docker cp mysql5:/backup/dump.sql
 docker cp dump.sql mysql5:/


 # 내 디렉토리에 어떤 파일을(localpath) 컨테이너(container-path) 에게 공유할 수 있다.
 docker run -v <localpath>:<container-path>
```
파일을 공유하거나 공유할 때 사용하는 명령어가 -v 이다.

## Oracle & Mysql 설치해보기

```
# oracle 다운로드
docker pull oracleinanutshell/oracle-xe-11g

# oracle 이라는 컨테이너 만들고 실행
docker run -d -p 8080:8080 -p 1521:1521 --name oracle oracleinanutshell/oracle-xe-11g

# 도커가 oracle을 실행하는 상태로 bash 쉘을 키도록 했다
docker exec -it oracle bash

# 그안에서 cat /etc/issue를 통해 OS를 확인해보자, Ubuntu 18.04.1 LTS \n \l 라고 나올것이다.
cat /etc/issue
```

위 커맨드를 하나씩 실행해보면 결과적으로 도커위에 오라클이라는 컨테이너가 실행되고 그위에 우분투가 설치된 상태로 오라클이 실행되고 있는 모습이다.

```
# mysql 이미지 뒤에 태그로 버전을 지정해줄 수 있다.
docker pull mysql:5.7

# -e는 환경변수를 의미한다, MYSQL_ROOT_PASSWORD = root1! 라는 OS에서 사용할 환경변수를 만든것이다. 태그명이 latest가 아니면 적어주어야 실행된다.
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root1! --name mysql5 mysql:5.7 mysql
```