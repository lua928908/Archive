# MySQL 기초 배우기

참고할 만한 자료 <br>

[생활코딩 - 데이터베이스 2012.04.04](https://youtu.be/am1X-UmvhZI?list=PLzk28xMxwFP9eEKTSnLEL2Ynj-RQQR96p) <br>
[생활코딩 - DATABASE 1&2 - MySQL 2018.02.11](https://youtu.be/h_XDmyz--0w?list=PLuHgQVnccGMCgrP_9HL3dAcvdt8qOZxjW)

```
다른 사람에게 지식을 공유하는 용도 보다는
내가 기억하고 기록하기 위한 목적이 먼저였기에 설명이 불친절 하거나 일부 설명이 생략되거나 예제의 컬럼명이 갑자기 변하는등의
오류가 있을 수 있다. 
```

### CRUD

생성: CREATE DATABASE <생성할 DB명>;

조회: SHOW DATABASES;

사용: USE <사용할 DB명>;

삭제: DROP DATABASE <삭제할 DB명>;

```
# 삽입
insert into `student` values(id, "taylor", "man", "경기도 용인시", "2020-02-11 00:00:00");

# 업데이트
update `student` set address="경기도 용인시 처인구" where id=9;

# 삭제
delete from `student` where id=0;


# 모두 조회
select * from `student`;
# name, birthday만 조회
select name, birthday from `student`;
# 3번 id만 조회
select * from `student` where id=3;
# 남자 이면서 서울인 경우
select * from `student` where gender="남자" and address="서울";
# 여자 이거나 서울인 경우
select * from `student` where gender="여자" or address="서울";
# limit으로 갯수를 지정 (자료가 많은 경우 select를 할때는 where나 limit을 쓰는게 좋다.)
select * from `student` limit 3;
# limit으로 length를 조절가능, offset이라는 개념 (1번째에 있는 자료 2개 까지만 가져옴)
select * from `student` limit 1,2;
# 남자인 정보를 2개까지만 가져옴
select * from `student` where gender="남자" limit 2;
```


## Index - 색인

책에보면 색인이라고 해서 가,나,다,라 혹은 ㄱ,ㄴ,ㄷ,ㄹ 와 같이 단어별로 배열이 되어있어 몇페이지에 어떤정보가 있는지
쉽게 찾을 수 있게 해놓은 것들이 색인이다. 데이터베이스에서도 색인의 개념이 있다. 테이블 안에 들어있는 행이 여러개 일때 그중에 필요한 데이터를 찾아오는데
효과적인 처리를위해 index를 사용한다.

####인덱스의 종류
1. primary : 중복되지 않는 유일한 키
    * 테이블 전체를 통틀어서 중복되지 않는 값을 지정해야 한다.
    * where 문을 이용해서 데이터를 조회할 때 가장 고속으로 데이터를 가져올 수 있다.
    * 테이블마다 딱 하나의 primary key를 가질 수 있다.

2. normal : 중복을 허용하는 인덱스
    * 중복을 허용한다.
    * primary, unique 보다 속도가 느리다.
    * 여러개의 키를 지정할 수 있다.

3. unique : 중복을 허용하지 않는 유일한 키
    * 테이블 전체를 통틀어서 중복되지 않는 값을 지정해야 한다. (== primary key)
    * 고속으로 데이터를 가져올 수 있다.
    * 여러개의 unique key를 지정할 수 있다.

4. foreign : 다른 테이블과의 관계성을 부여하는 키
    *
    
5. full text : 자연어 검색, myisam에서만 지원
    * mysql의 기본설정(ft_min_word_len)이 4로 되어 있기 때문에 최소 4글자 이상을 입력하거나 이 값을 조정해야 한다.
    * mysql은 전문 검색 엔진이 아니기 때문에 한글 검색이 잘 안된다. 전문검색 엔진으로 lucene, sphinx를 참고
    * 스토리지 엔진 중 myisam에서만 사용가능 (InnoDB는 불가)


key들을 통틀어 index라고 부른다.

```
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `name` char(4) NOT NULL,
  `address` varchar(50) NOT NULL,
  `department` enum('국문과','영문과','컴퓨터공학과','전자공학과','물리학과') NOT NULL,
  `introduction` text NOT NULL,
  `number` char(255) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_number` (`number`) USING BTREE,
  KEY `idx_department` (`department`),
  KEY `idx_department_name` (`department`,`address`),
  FULLTEXT KEY `idx_introduction` (`introduction`)
) ENGINE=MyISAM AUTO_INCREMENT=9 DEFAULT CHARSET=utf8;
 
INSERT INTO `student` VALUES (1, '이숙경', '청주', '컴퓨터공학과', '저는 컴퓨터 공학과에 다닙니다. computer', '0212031');
INSERT INTO `student` VALUES (2, '박재숙', '서울', '영문과', '저는 영문과에 다닙니다.', '0512321');
INSERT INTO `student` VALUES (3, '백태호', '경주', '컴퓨터공학과', '저는 컴퓨터 공학과에 다니고 경주에서 왔습니다.', '0913134');
INSERT INTO `student` VALUES (4, '김경훈', '제천', '국문과', '제천이 고향이고 국문과에 다닙니다.', '9813413');
INSERT INTO `student` VALUES (6, '김경진', '제주', '국문과', '이번에 국문과에 입학한 김경진이라고 합니다. 제주에서 왔어요.', '0534543');
INSERT INTO `student` VALUES (7, '박경호', '제주', '국문과', '박경호입니다. 잘 부탁드립니다.', '0134511');
INSERT INTO `student` VALUES (8, '김정인', '대전', '영문과', '김정인입니다. 대전에서 왔고, 영문과에 다닙니다.', '0034543');
```

위와 같이 data를 만들고 실습을 진행해본다.

```
# where문으로 검색을 할 때 primary key가 가장 빠른 조회 속도를 보여준다.
select * from student where id=3;

# primary키는 table에서 유일해야 하지만 unique는 값은 중복되면 안되지만, 하나의 table에 unique key를 가진 컬럼은 여러개일 수 있다.
select * from student where number=0534543;

# 컴퓨터공학과에 속한 학생이 여러명일 수 있듯이 어떠한 table에 중복으로 존재할 수 있다, normal key라고 부르는데 실제 table을 create할때는 key 라고 표기한다.
select * from student where department='국문과'

# 중복키는 index의 이름은 아니고 index를 사용하는 방식이라고 볼 수 있다. 하나의 key에 여러개의 컬럼을 포함한다. 
select * from student where department='국문과' AND address='제주';
```

<br>

## JOIN - 여러 table 사용하기

서비스가 커지고 데이터 규모가 커지면, 하나의 테이블로 정보를 수용하기 어렵기 때문에 여러개의 table을 합쳐서 필요한 데이터로 사용한다.


```
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `id` tinyint(4) NOT NULL,
  `name` char(4) NOT NULL,
  `sex` enum('남자','여자') NOT NULL,
  `address` varchar(50) NOT NULL,
  `distance` INT NOT NULL,
  `birthday` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
   
INSERT INTO `student` VALUES (2, '박재숙', '남자', '서울',  10, '1985-10-26 00:00:00');
INSERT INTO `student` VALUES (1, '이숙경', '여자', '청주', 200, '1982-11-16 00:00:00');
INSERT INTO `student` VALUES (3, '백태호', '남자', '경주', 350, '1989-2-10 00:00:00');
INSERT INTO `student` VALUES (4, '김경훈', '남자', '제천', 190, '1979-11-4 00:00:00');
INSERT INTO `student` VALUES (8, '김정인', '남자', '제주', 400, '1990-10-1 00:00:00');
INSERT INTO `student` VALUES (6, '김경진', '여자', '제주', 400, '1985-1-1 00:00:00');
INSERT INTO `student` VALUES (7, '박경호', '남자', '영동', 310, '1981-2-3 00:00:00');
```

위와 같이 data를 만들고 실습을 진행해본다.

`select * from student`를 통해 데이터를 조회하면 `김정인,제주,400` 이라는 값과 `김경진,제주,400` 이라는 값이 존재한다.
그런데 만약 제주도의 거리가 알고보니 400이 아니라 500이라면 김정인의 제주400과 김경진의 제주400을 모두 바꾸어 주저야한다. 지금은 값이 적어
어렵지 않지만 만약 table의 data가 100만개라면 나중에 데이터가 변경될 때 관리가 쉽지않고 값을 불러오는 과정에서도 같이 값이 여러번 저장되는 비효율이
발생한다. 그래서 student라는 table과 location이라는 table을 따로 만들어 관리고 join을 통해 student와 location이 합쳐진 table을 얻을 수 있다.


```
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `id` tinyint(4) NOT NULL,
  `name` char(4) NOT NULL,
  `sex` enum('남자','여자') NOT NULL,
  `location_id` tinyint(4) NOT NULL,
  `birthday` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
 
DROP TABLE IF EXISTS `location`;
CREATE TABLE `location` (
`id`  tinyint UNSIGNED NOT NULL AUTO_INCREMENT ,
`name`  varchar(20) NOT NULL ,
`distance`  tinyint UNSIGNED NOT NULL ,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
table을 `student` 와 `location` 으로 쪼개서 만들었따.
`desc student;` 혹은 `desc location;`을 통해 table이 어떻게 정의 되었는가 확인할 수 있다.

```
INSERT INTO `location` VALUES (1, '서울', 10);
INSERT INTO `location` VALUES (2, '청주', 200);
INSERT INTO `location` VALUES (3, '경주', 255);
INSERT INTO `location` VALUES (4, '제천', 190);
INSERT INTO `location` VALUES (5, '대전', 200);
INSERT INTO `location` VALUES (6, '제주', 255);
INSERT INTO `location` VALUES (7, '영동', 255);
INSERT INTO `location` VALUES (8, '광주', 255);

INSERT INTO `student` VALUES (1, '이숙경', '여자', 1, '1982-11-16 00:00:00');
INSERT INTO `student` VALUES (2, '박재숙', '남자', 2, '1985-10-26 00:00:00');
INSERT INTO `student` VALUES (3, '백태호', '남자', 3, '1989-2-10 00:00:00');
INSERT INTO `student` VALUES (4, '김경훈', '남자', 4, '1979-11-4 00:00:00');
INSERT INTO `student` VALUES (6, '김경진', '여자', 5, '1985-1-1 00:00:00');
INSERT INTO `student` VALUES (7, '박경호', '남자', 6, '1981-2-3 00:00:00');
INSERT INTO `student` VALUES (8, '김정인', '남자', 5, '1990-10-1 00:00:00');
```

예제를 위한 더미 값을 넣어준다.

그럼 가장 중요한 `location_id` 컬럼을 `location` 테이블에서 어떻게 가져올 수 있을까?

<br>

#### join의 종류

![SQL JOIN 이미지](../images/Visual_SQL_JOINS_V2.png)

<br>

* OUTTER JOIN : 매칭되는 행이 없어도 결과를 가져오고 매칭되는 행이 없다면 NULL로 표시한다.
    1. LEFT JOIN : 가장 많이 사용되는 join의 형태이다, 왼쪽 테이블을 중심으로 오른쪽의 테이블을 매치시켜 왼쪽은 무조건 표시하고 오른쪽 데이터중 매치되는게 없다면 null을 표시한다.
    2. RIGHT JOIN : `LEFT JOIN`의 반대개념이다, 오른쪽 테이블을 중심으로 왼쪽과 매칭되면 모두표시. 매칭되는 값이 없으면 null 이다.
    
* INNER JOIN : join 하는 두개의 table 모두에 데이터가 존재하는 행에 대해서만 결과를 가져온다.

이미지를 참고하면 이해가 쉬워진다.


```
SELECT s.name, s.location_id, l.name AS address, l.distance  FROM student AS s LEFT JOIN location AS l ON s.location_id = l.id;
```

위 코드는 가장 많이사용 되는 형태의 join의 형태이다.

```
SELECT [column 이름]  FROM [table 이름] LEFT JOIN [table 이름] ON [JOIN 조건(매칭조건)];
```
정도로 이해해도 무방하지 않을까? 위에 코드 뒤에 where문을 붙여 조건을 추가하는것도 가능하다.
on 뒤에 붙는 쿼리는 매칭할 조건이고 where 뒤에 붙는 쿼리는 if문으로 이해하면 될 것 같다.


## mysql 사용자 관리

#### 권한부여

DB 서버에 접속하는 사용자가 어느 db에 접속할 수 있는지 액세스 권한을 부여한다.
*을 사용하면 모든 데이터베이스, 테이블을 제어 대상으로 한다.

```
GRANT 권한 ON 데이터베이스.테이블 TO `아이디`@`호스트` IDENTIFIED BY "비밀번호"

GRANT DELETE,INSERT,SELECT,UPDATE ON class.* TO `lua`@`%` IDENTIFIED BY '1234'; 
```

위 예제는 아이디는 lua 비밀번호는 1234인 유저가 class 데이터베이스에 접근해 CRUD를 가능하게 하는 예제이다.
모든 권한을 주고 싶으면 `all privileges` 이라고 입력하면 된다.

<br>

```
1) 특정 IP 접근 허용 설정
grant all on DB명.테이블명 to 'root'@'220.000.00.000' identified by 'root의 패스워드';


2) 특정 IP 대역 접근 허용 설정
grant all on DB명.테이블명  to 'root'@'220.000.%' identified by 'root의 패스워드';


3) 모든 IP의 접근 허용 설정
grant all on DB명.테이블명  to 'root'@'%' identified by 'root의 패스워드'
```

혹은 위처럼 IP를 특정할 수 있다.

<br>

```
SHOW GRANTS FOR [사용자 아이디]
```

SHOW GRANTS를 사용해 해당 아이디에 설정된 권한을 볼 수 있다.

<br>

#### 권한제거 - REVOKE

```
REVOKE 권한 ON 데이터베이스.테이블 FROM 사용자

REVOKE delete ON class.* FROM lua;
```

위와 같은 코드를 통해 사용자의 권한을 제거할 수 있다.

<br>

#### 유저삭제 - DROP

```
DROP USER `사용자`@`아이피`;

DROP USER `lua`@`%`;
```

위와 같은 코드를 통해 사용자를 삭제할 수 있다.