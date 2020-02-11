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