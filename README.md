안녕하세요.<br/>
자격증 취득을 위한 공부를 기록하려고 합니다.

데이터 모델링의 이해 부분은 블로그에 작성했습니다.<br/>
https://blog.naver.com/happyphjm1/223178609287

# 관계형 데이터베이스 SQL
릴레이션에 데이터를 저장하고 관리한다.<br/>
Parent-Child 관계로 표현하기 쉬움

SQL은 ANSI/ISO SQL 표준을 준수하기 때문에 여러 가지 관리 시스템 바뀌어도 괜찮음<br/>
(관리 시스템 ex. MYSQL, Oracle...)

## SQL 종류
### 종류
   - DDL (Data Definition) : 구조 정의 (Create, Alter, Drop, Rename...)<br/>
   - DML (Data Manipulation) : 데이터 입력, 수정, 삭제, 조회 (Insert, Update, Delete, Select...)<br/>
   - DCL (Data Control) : 데이터베이스 사용자 권한 설정 (Grant, Revoke..)<br/>
   - TCL (Transaction Control) : 트랜잭션 제어 (Commit, Rollback, Savepoint...)

### 실행 순서
1. 파싱 : 문법 확인, 구문 분석 → Library Cache에 저장<br/>
2. 실행 : 옵티마이저가 수립한 계획에 따라 SQL 실행<br/>
3. 인출 : 데이터 읽어서 전송

## DDL
### Create
테이블 생성.
```SQL
CREATE TABLE EMP(
  empno number(10) primary key,
  ename varchar2(20),
  age number(10,2) default 1,
  createdate date default sysdate,
  deptno varchar2(4) not null
);
```

대충 이런 식으로 한다.

empno는 number~<br/>
emane은 varchar2~<br/>
default 설정도 가능

primary key 설정은 constraint문으로 할 수도 있다. <br/>
constraint emp_pk primary key(empno) 이런 식으로~

constraint key 로는 foreign key 설정도 할 수 있다.<br/>
하위 테이블에서 마스터 테이블을 참조하면 된다.
```
constraint dept_fk foreign key(deptno)
   references dept(deptno)
   ON DELETE CASCADE
```
(EMP 테이블이 DEPT 테이블을 참조했다)

CASCADE란 단어 뜻대로 단계적으로 적용되는 것을 의미한다.<br/>위 예시에선 DEPT 테이블이 삭제되면 EMP 테이블도 삭제되는 역할을 한다.<br/>
나중에 DELETE 구문에서도 활용 예제가 있다.

### ALTER

- 테이블명 변경<br/>
  ALTER TABLE ~ RENAME TO

- 칼럼 추가/변경
  - 추가<br/>
    ALTER TABLE ~ ADD 하면 된다<br/>
    ```
    ALTER TABLE EMP
       ADD (age number(2) default 1);
    ```
  - 변경<br/>
    ALTER TABLE ~ MODIFY<br/>
    추가할 때랑 비슷하다

  - 칼럼명 변경<br/>
    ALTER TABLE ~ RENAME COLUMN ~ TO<br/>
    테이블명 변경과 비슷

  - 삭제<br/>
    ALTER TABLE ~ DROP<br/>
    밑에서 서술할 DROP을 참고하면 된다

### DROP
테이블을 삭제하고 싶을 때<br/>
  ***DROP TABLE***<br/>
구문으로 손쉽게 처리가 가능하다.<br/>
후에 나올 DELETE과는 많은 차이가 있다<br/>
일단 DROP은 테이블 구조 자체를 없애버리고 되돌릴 수 없음을 알고 있어야 한다.

DROP TABLE ~ CASCADE CONSTRAINT<br/>
지우려는 테이블을 참조하는 슬레이브 테이블의 제약사항들까지 모두 없애버리는 구문이다.

#### VIEW
뷰는 테이블에서 유도된 가상의 테이블 같은 것이다.<br/>보기 전용으로 만들어진 테이블 복사본 정도로 이해하면 될 것 같다.<br/>테이블에서 따오고 싶은 부분만 참조해온다.

- 특징
  - 참조한 테이블이 변하면 뷰도 변한다
  - 뷰의 검색 외에 입력, 수정, 삭제 등에는 제약이 있다.
  - 특정 칼럼만 조회시켜 보안성 ↑

- 생성
  ```
  CREATE VIEW T_EMP AS
     SELECT ~ FROM EMP;
  ```
  ~ 부분엔 가지고 오고 싶은 칼럼들을 적는다.<br/>
  *를 쓰면 전부 가져온다.

- 조회<br/>
조회는 SELECT 문으로 일반 테이블처럼 하면 된다.<br/>
삭제도 DROP으로 하면 된다.
  
## DML
### INSERT
INSERT INTO Table(column...) VALUES~(...)<br/>
Table에는 테이블명이 들어가야 한다.

```
INSERT INTO NEW_DEPT SELECT * FROM DEPT;
```
이런 식으로 다른 테이블에서 SELECT해온 데이터를 바로 삽입할 수도 있다.

### UPDATE
데이터의 값을 수정한다.<br/>
```
UPDATE EMP
   SET ENAME = '짐짐'
WHERE EMPNO = 10;
```
이런 식으로 테이블 내의 데이터를 바꿀 수 있다.<br/>
WHERE에 걸리는 모든 데이터를 갈아치우기 때문에 주의해야 함.<br/>
조건을 안 걸면 아예 전부 다 바뀌고,,, 주의 필수.

### DELETE
DELETE FROM Table

WHERE 절로 조건을 설정해서 지우면 된다.<br/>
UPDATE와 같이 주의~

- TRUNCATE TABLE Table<br/>
  DELETE문은 데이터를 없애주긴 하나, 데이터가 있던 공간까지 없어주지 않는다.<br/>
  TRUNCATE문을 사용하면 각 행을 삭제하는 로그가 남지 않아 훨씬 빠르다.

### SELECT
SELECT Column FROM Table
(WHERE ~)

데이터 조회에 사용된다. 여러 개의 칼럼도 나열해서 조회 가능하다.

예)
SELECT ENAME || '씨' FROM Table;
을 하면 ENAME씨처럼 표시가 될 것

- ORDER BY<br/>
  SELECT문 뒤에 사용해서 정렬한 데이터를 볼 수 있다.<br/>
  ORDER BY ENAME ASC (ENAME으로 오름차순) ASC가 디폴트다.<br/>
  ORDER BY EMPNO DESC (EMPNO로 내림차순)

  DB에 부하를 주는 작업이므로 INDEX 등 사용해서 정렬 회피를 할 수 있다.

- 특수 키워드
  - DISTINCT<br/>
    칼럼명 앞에 붙여 중복된 데이터를 제거해준다<br/>
    SELECT DEPTNO FROM EMP ORDER BY DEPTNO;<br/>
    →SELECT DISTINCT DEPTNO FROM EMP ORDER BY DEPTNO;
    
  - 별명<br/>
    SELECT ENAME AS "이름" FROM EMP a WHERE a.EMPNO = 100;<br/>
    ENAME이 이름으로 표기된다. EMP라는 테이블을 코드상에서 a로 표현할 수 있다.

### WHERE

비교 연산자, 부정, 논리, 비교 연산자 등으로 사용 가능하다
SQL만의 연산자도 있음.

https://crazydeer.tistory.com/entry/SQL-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A2%85%EB%A5%98-between-like-in-is-null-%EC%97%B0%EC%82%B0%EC%9E%90

참고.

- LIKE<br/>
  %와 _를 사용해서 찾는다<br/>
  박% -> 박으로 시작하는 것들<br/>
  박_현 -> 박종현, 박지현... 등 한 글자가 들어간 자리만 찾는다.

- IN<br/>
  SELECT ~ WHERE JOB IN ('AGENT', 'COOK', 'ATHLETE');<br/>
  IN 뒤에 있는 것을 모두 탐색한다. 위 예시에서 3가지 모두 만족할 필요가 없고 1가지만 만족할 시 검색된다.<br/>
  SELECT ~ WHERE (JOB, ENAME) IN ('FIREFIGHTER', 11), ('COOK', 22);<br/>
  이런 식으로 서로 짝을 지어서 찾을 수도 있다.

### GROUP BY

테이블의 일정 행들을 종합해 합계, 평균, 편차 등으로 나타낸다.<br/>
