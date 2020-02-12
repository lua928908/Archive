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

