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

## 도커 명령어

| 커맨드 | 기능 |
| ------ | --- |
| `docker run example` | 이미지를 찾아 실행한다. (없으면 도커허브에서 풀링) |
| `docker ps` | 현재 실행중인 컨테이너 리스트를 확인할 수 있다. |
| `docker ps -a` | 실행중인 도커 컨테이너와 중지된 컨테이너까 정보를 확인할 수 있다. |
| `docker container run <docker-image-name> <command>` | 도커 컨테이너를 실행한다. |
| `수식.not` | 식의 조건을 반전 |

<br>
<br>

`Docker Quickstart Terminal`을 실행하고 `docker run hello-world`을 입력해보자 `
`Unable to find image 'hello-world:latest' locally` 라는 문구가 나오더니 다운로드를 할 것이다.
로컬상에서 hello-world라는 이미지를 찾을 수 없어 도커허브에서 pulling 한것이다. 깃에서의 풀은 코드를 받는것이지만 도커에서는 이미지를 받는것이다.
`https://hub.docker.com/` 이곳에서 도커 이미지를 공유할 수 있다. 좀더 알아보고 싶다면 `https://docs.docker.com/get-started/` 이곳을 확인해보라는 메세지가 나온다.
즉 우리는 방금 도커허브에서 헬로월드 예제를 다운받아 실행해본 것이다.

이번에는 `docker ps -a`를 실행해보자

![도커 커맨드 실행](./images/주석%202020-07-05%20110042.png)

위 이미지 처럼 컨테이너의 상태와 언제만들어졌는지 status에선 언제 실행되었는지가 표기된다. names는 정해준 값이 없기 때문에 임의에 이름이 부여되어 있다.

`docker-machine.exe ls`를 실행해보면 실행되고 있는 버추얼박스와 정보를 확인할 수 있다.

