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
| `history` | 내가 사용했던 명령어 기록이 나온다. |
| `rm -rf <디렉토리 이름>` | 디렉토리와 하위 파일을 모두 지운다. `rmdir`는 하위 파일이 있는경우 삭제가 안된다. |
| `which <실행파일 이름>` | $PATH하위에 실행파일 경로를 검색한다. |
| `find <파일 및 디렉토리 이름>` | 파일이나 디렉토리의 위치를 검색한다. |
| `password` | 사용자의 패스워드를 변경한다. |
| `grep <찾을단어> <파일명> <-io>` | 정규표현식으로 파일 및 내부이름을 검색할 때 사용한다. |
| `df -m` | 디스크 용량이 얼마나 사용중인지 정보를 볼 수 있다. df -m을 입력하면 용량을 메가바이트로 출력해준다. |
| `free -m` | 메모리의 사용량 정보를 알려준다. |
| `top` | 여러정보를 알려준다. up된 시간, load average, 숫자1을 누르면 cpu정보 등등을 볼 수 있다. |
| `vmstat` | top과 비슷한 역할을 한다, 서버상태에 정보를 볼 수 있다. `vmstat 1` 을 입력하면 1초마다 정보를 갱신하며 화면에 출력한다. |
| `chmod` | 파일의 쓰기,읽기,실행 권한을 변경한다, change mode 라는 뜻이다. |
| `chmod +r test.sh` | test.sh 라는 파일의 나,그룹,타인의 읽기권한(r)를 모두 가능하게 바꾼다. |
| `chmod +x test.sh` | test.sh 라는 파일의 나,그룹,타인의 실행권한(x)를 모두 가능하게 바꾼다. |
| `chmod +w test.sh` | test.sh 라는 파일의 나,그룹,타인의 쓰기권한(w)를 모두 가능하게 바꾼다. |
| `ln -s <경로> <별칭>` | ln은 링크를 달아주는 것이다. -s는 심볼링 하는것이다, `ln -s /home/lua/ttt ttt` 라고 입력하면 /home/lua/ttt 라는 경로를 ttt라는 별칭으로 바로가기처럼 지정하는 것이다. rm ttt를 통해 별칭삭제도 가능하다. |

## 기타사항

#### 한글적용
 
```
# 최신으로 업데이트
apt-get update

# locales 다운로드
apt-get install locales

# KO UTF-8을 사용 간으하게 설정한다.
localedef -f UTF-8 -i ko_KR ko_KR.UTF-8

# ~/.profile에 LC_ALL 값이 ko_KR.UTF-8로 바뀐다, 뒤에 bash를 붙여야 쉘이 실행되며 인코딩이 바뀐다.
LC_ALL=ko_KR.UTF-8 bash
```
apt-get install locales을 통해 lcales를 다운로드 받고 locale을 입력하면 언어설정이 나오는데
LC_ALL의 값이 ko로 변경되면 한글적용이 가능해진다.
`localedef -f UTF-8 -i ko_KR ko_KR.UTF-8`을 입력하면 설정이 된다.


#### VI 에디터

| 명령어 | 기능 |
| ------ | --- |
| `w` | 단어 단위로 이동한다. |
| `ctrl+f` | 페이지 다운 |
| `ctrl+u` | 페이지 업 |
| `cw` | 하나의 단어를 변경 |
| `dw` | 하나의 단어를 삭제 |
| `v` | 블럭지정 |
| `y` | 복사 |
| `yy` | 한줄 복사 |
| `p` | 붙여넣기 |
| `dd` | 한줄 삭제 |
| `:set nu` | 라인번호를 보여준다. |
| `h, j, k, l` | 방향키 역할을 하는데 잘 안쓸것 같다. |
| `:15` | 15번째 줄로 이동한다. |
| `:q!` | 저장하지 않고 에디터 나가기 |
| `!` | 커맨드 창으로 잠깐 나갔다가 엔터치면 에디터로 돌아온다. |
| `!명령` | 커맨드 창으로 나간 이후 명령어를 실행 |


#### .vimrc 로 VI 에디터 환결설정하기

```
# indentetion을 맞춰주는데 별로 스마트하지는 않다.
set smartindent

set tabstop=4
set expandtab

# tab 설정을 space 4개로 넣게 하기 위함이다.
set shiftwidth=4 
```


#### 그외 예제 따라해보기

```

#!/bin/bash
echo "\$0=$0"
echo "\$1=$1"
echo "\$2=$2"
echo "\$#=$#"
```
이런 코드를 가진 sh파일을 만들어보자

그리고 `./s3.sh aaa bbb` 이런 명령어로 실행해보면

```
$0=./s3.sh
$1=aaa
$2=bbb
$#=2
```
위와 같은 실행결과를 볼 수 있다. 즉 쉘스크립트를 작성할 때 $0은 실행된 파일의 이름이고
$1, $2와 같은 숫자는 실행하면서 전달받은 매개변수가 하나씩 매핑되는 것이고 $#은 전달받은 매개변수의
갯수를 출력하는 것이다.

```
#!/bin/sh

MSG="empty"

if [ $# -gt 0 ]; then
    MSG="$1"
fi

echo $MSG
```

$MSG라는 변수를 출력하는데 if문에서 조건식으로 `$# -gt 0`이 들어왔다 $#의 의미가 받은 파라미터 갯수라고 했다. gt는 그레이트 댄 이다.
즉, 파라미터 갯수가 0보다 크다면 받은 매개변수의 갯수이고 없으면 empty가 노출되는 것이다.

<br>

```
#!/bin/bash

if [ $# -eq 0 ]; then
    echo "Input the filename, please..."
    echo "usage) ./s5.sh <to-change-file>"
    exit 0
fi

cat $1

DATE=`date +%Y_%m_%d`
FN="${DATE}.txt"
# echo "mv $1 $FN"

mv $1 $FN
ls
```

위 코드는 매개변수가 없으면 안내메세지를 보여주고 매개변수가 있다면 매개변수의 내용을 cat으로 출력한 다음
현재시간으로 파일명을 변경해주는 스크립트 이다.

<br>

#### 쉘 스크립트에서 arr을 쓸 때 주의점
```
#!/bin/bash

arr=("aaa" "bbb" "ccc" 123)

echo "arr=$arr"
echo ${arr[0]}

len=${#arr[@]}
arr[$len]="4444"

echo "### ${#arr} : ${#arr[@]}"

echo ${arr[@]}
```
위 코드를 보면 arr이라는 어레이에 4개의 값을 넣었다. 그런데 arr에 새로운 값을 추가시킨 상태에서 `${#arr}`로 갯수를 찍어보면 값이
추가되어 length가 증가했음에도 처음 선언했던 arr의 값을 출력하게 된다. 즉 `${arr}`는 처음 메모리에 올라간 arr 변수를 사용하게되고
`${ arr[@] }` 처럼 @를 붙여줘야 현재 arr 변수의 상태를 가져오게 된다는 점이다. 그래서 쉘스크립트에서 array를 사용할 때는 습관적으로
@를 붙여서 사용해야 한다.