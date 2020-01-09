# Spring Boot 환경에서 EC2 접속 및 필수 환경설정 하기  

<br>

## ssh 접속하기 (MAC or LINUX)
1. 매번 `ssh -i pem키위치 ip주소` 이런식으로 접속하기 귀찮으므로 키 위치를 복사한다.
cp pem키위치 ~/.ssh/
```
cp ~/Documents/pem/example-directory/example.pem ~/.ssh/
```

2. 키가 잘 복사되었는지 확인해본다
```
cd ~/.ssh/
ll // 디렉토리 확인
```

3. 복사된 키의 권한을 변경한다.
```
chmod 600 ~./ssh/pem키이름
```

4. ~/ssh 디렉토리에서 config파일을 생성한다.(확장자 없음)
```
vim ~/.ssh/config
```
이후 config파일에 아래와 같이 작성
```
# 주석
Host "서비스명"
HostName "EC2 IP주소"
User ec2-user
IdentityFile ~/.ssh/pem키이름
```
작성후 :wq로 종료한다.

<br>

5. 생성된 config파일은 실행 권한이 필요하므로 다음같이 설정한다.
```
chmod 700 ~/.ssh/config
```

6. 명령어를 사용해 접속한다. ssh config에 등록한 서비스명
```
ssh example
```




----------------------------------------


## EC2에 SpringBoot 필수설정 설치하기

1. 아마존 리눅스1의 경우 java7이 기본으로 깔려있어서 8을 설치해야 한다.
```
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```
2. 설치후 다음과같이 입력한다.
```
sudo /usr/sbin/alternatives --config java
```
3. jre-1.7.0과 jre-1.8.0중 선택하라고 나오는데 2를 선택한다
```
2
```
4. 사용하지 않는 1.7은 삭제한다.
```
sudo yum remove java-1.7.0-openjdk
```
5. y를 눌러 삭제한다.
```
y
```
6. 자바 버전을 확인한다. (2020년 01월 08일 기준으로 1.8.0_222 라고 나오면 정상)



# 타임존 변경하기

1. 기본설정이 미국이기때문에 한국으로 변경해야 한다. (UTC 에서 KST로 바꾸어야함, 9시간 차이남)
```
sudo rm /etc/localtime
```
2. 아래와 같이 코드를 실행한다.
```
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```
3. 정상적으로 수행되었다면 date 명령어로 타임존이 변경된 것을 확인할 수 있다.
```
date
```

<br>

## Hostname 변경하기

1. 여러서버를 관리중일 경우 IP만으로 구분하기 어렵다. 그래서 HOSTNAME을 변경해야 한다.
```
sudo vim /etc/sysconfig/network
```
2. HOSTNAME= 라고 되어있는 부분의 값을 내가원하는 내용으로 변경 후 vim 명령모드를 종료한다.
예제 에서는 SpringBoot_Board라는 이름을 사용하였다.
``` 
NETWORKING=yes
HOSTNAME=SpringBoot_Board
NOZEROCONF=yes
```
4. sudo reboot를 입력해서 서버를 재부팅 한다.
5. 위에 명령어를 입력하면 서버가 끊겼다는 내용이 나오고 시간이 조금 지난후에 접속해보면 변경된 이름으로 나오게 된다.
6. Hostname을 변경했으면 /etc/hosts에 hostname을 등록해주어야한다. 안그러면 장애가 발생하는 경우가 있다.
```
sudo vim /etc/hosts
```
7. 처음에 쓰여진 내용 밑에 추가로 127.0.0.1 서비스이름 이렇게 추가로 등록해야한다, 원래것을 지우는게 아니라 추가로 등록해야 한다. 왠지 들여쓰기도 맞아야 할것같아서 들여쓰기도 맞췄다.
```
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1           localhost6 localhost6.localdomain6
    127.0.0.1   SpringBoot_Board
```
9. 제대로 됐는지 확인을 위해 다음 명령어를 입력한다. "curl 등록한 호스트 이름" 잘못 되었으면 찾을수 없는 주소라고 나온다 실패라면 "could not resolve host" 이런식으로 나오게 된다. 잘되면 "curl: (7) Failed to connect to SpringBoot_Board port 80: Connection refused" 이런식으로 나온다. Failed 이긴하지만 80포트로 연결이 안됐을뿐(이 내용을 쓰는상황에서 80번 포트로 연결된게 없어서 그럼) curl 호스트이름으로 실행은 잘 된것이다.
```
curl 등록한 호스트 이름
```



















