# Linux 공부하기
## 리눅스란 무엇인가?

리눅스는 1991년 핀란드의 개발자 Linus Torvalds에 의해 개발되었다.

Linux Distro
- Debian : Debian, Ubuntu, KNOPPIX(CD Linux)
- Red Hat: Fedora, RedHat Enterprise, CentOS, Vine Linux(Japan)
- Slackware: openSUSE(Novell), SUSE Linux Enterprise


## 용어 정의하기

#### Kernel
- Management the Memory, File System, CPU, Device, etc.
- Infra 와 OS단계 사이에 커널이 존재한다, 메모리상에서 매니지먼트하고 CPU나 디바이스, 파일시스템을 관리한다.
- 하드웨어를 제어하는 소프트웨어를 펌웨어인데 펌웨어와 비슷한 개념으로 볼 수 있다.

<br>

#### Linux Shell

커널을 조금더 쉽게 다루기 위해 OS 안에 커널과 커맨드가 존재하는데 커널을 더욱 쉽게 조작하기위해
쉘이라는 것을 통해 커널을 조작한다 커널을 조작하는 명령의 들의 집단정도로 정의할 수 있겠다.

- sh (Bourne shell) : By Unix Shell, Super shell (기능은 적지만 가장 최상위 조상 격이다.)
- bash (Bourne-agin shell) : Super shell in Linux (리눅스의 super shell 역할이다.)
- csh (C shell) : C like syntax (C 언어 개발자들은 bash를 쓰면 답답해 한다, 그래서 생긴 쉘을 c언어처럼 작성할 수 있는 shell)
- tcsh (Enhanced-C shell): csh를 개선한 쉘
- ksh (korn shell) : by David Korn, Powerful Script Language (많은 기능 보유)
- zch (Z shell) : Unix/GNU shell script, Powerful Script Language 

bash나 sh를 잘 다루는 것이 중요하다.

<br>

## 리눅스 파일 디렉토리 설명

| 명령어 | 동작 |
| ------ | --- |
| `/bin` | 기본 명령어 |
| `/boot` | 부팅관련 |
| `/dev` | 디바이스 관련 |
| `/etc` | config, password, rc.d 등등 |
| `home` | 유저들이 있는 home 디렉토리 |
| `/lib` | 공유 라이브러리 |
| `/media` | ssd 혹은 외장하드같은 마운트되는것들 |
| `/opt` | 어플리케이션 패키지 |
| `/proc` | 프로세스 정보 |
| `/root` | 루트 홈 디렉토리 |
| `/sbin` | 관리자용, ifconfig |
| `/srv` | 시스템 데이터 |
| `/temp` | 임시, temporary 디렉토리 |
| `/usr` | 웹, 소스 혹은 프로그램 |
| `/` |  |
| `/var` | log파일, ftp, mail 등등 양이 많은것들 |
| `/lost+found` | 휴지통 같은개념 |

<br>

## 리눅스 포트 - IANA(Internet Assigned Numbers Authority) 에서 정한 표준

| 포트 번호 | 역할 |
| ------ | --- |
| `20` | FTP (data) |
| `21` | FTP (control) |
| `22` | SSH |
| `23` | Telnet |
| `25` | SMTP(이메일) |
| `465` | SMTPS |
| `43` | whois |
| `53` | DNS |
| `80` | HTTP |
| `443` | HTTPS |
| `110` | POP3(이메일) |
| `995` | POP3S(이메일, 보안) |
| `123` | NTP (network time protocol) 인공위성에 맞춰진 표준시간 |
| `143` | IMAP2/4 |
| `993` | IMAPS |
| `514` | SysLog |

<br>

## 리눅스 명령어

| 포트 번호 | 역할 |
| ------ | --- |
| `ps -ef` | CPU를 소비하는 프로세스의 목록 |
| `ll` | 내 위치의 하위 디렉토리와 권한 노출 |
| `passwd <username>` | 유저 패스워드 변경 |
| `deluser <username>` | 유저 삭제 (디렉토리는 삭제하지 않는다.) |
| `su - <username>` | 해당 유저로 로그인하게 된다. |
| `pwd` | 현재 디렉토리 |