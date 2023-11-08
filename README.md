안녕하세요.<br/>
자격증 취득을 위한 공부를 기록하려고 합니다.

AD 관련 공부도 하고 있습니다.<br/>
https://blog.naver.com/quest_kor/221487945625 
<br/>
하려고 했는데 영 대우나 향후 전망이 별로인 것 같아서 접었습니다 서버 쪽 공부할 겁니당

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
ename은 varchar2~<br/>
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

  DB에 부하를 주는 작업이므로 INDEX 등 사용해서 정렬 회피를 할 수 있다.<br/>
  그러나 인덱스도 이래저래 유용하기만 한 것은 아니니 잘 판단해서 쓰자.

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
종합한 행에 HAVING 키워드로 조건을 걸 수도 있다.

```
SELECT DEPTNO, SUM(E_AGE)
FROM EMP
GROUP BY DEPTNO
HAVING SUM(E_AGE) > 1000
```

SUM() 부분은 다른 여러 함수들로 대체 가능하며 함수들로는<br/>
COUNT(), AVG(), MAX(), MIN(), STDDEV(), VARIAN()<br/>
등이 있다.

이 중 COUNT()는 좀 특이한 게,<br/>
SELECT COUNT(*) FROM EMP;<br/>
이렇게 쓰면 EMP의 모든 행들의 개수를 보여주지만,

SELECT COUNT(E_AGE) FROM EMP;<br/>
이렇게 쓸 시 E_AGE 행의 개수들 중 NULL을 가지고 있는 건 보여주지 않는다.

### Type Casting

일단 형변환 함수들이 있다.<br/>
TO_NUMBER(), TO_CHAR(), TO_DATE()

이런 타입 변환 함수를 쓰지 않아도 DB 관리 시스템 선에서 암시적으로 형변환시켜주긴 하나,<br/>
인덱스 칼럼에다가는 함수를 직접 쓰지 않으면 인덱스가 깨진다. 주의할 것.

### Built-in 함수

DUAL 테이블이라는 게 있다.<br/>
기본적으로 내장된 테이블이며 SELECT 등을 테스트해보기 위해 존재하는 테이블이다.

#### 문자열 함수

이런 거 나열하는 건 딱 질색이지만, 시험이니까...

- ASCII(string) : 문자, 숫자를 ASCII로 변환<br/>
- CHAR(ASCII) : ASCII를 문자로 변환<br/>
- SUBSTR(string, m, n) : 문자열에서 m번째 위치부터 n개를 자름<br/>
- CONCAT(string1, string2) : 문자열 1과 2를 붙인다<br/>
- LOWER(string) : 소문자로 변환<br/>
- UPPER(string) : 대문자로 변환<br/>
- LEN(string) : 문자열 길이 나타냄(공백 포함)<br/>
- TRIM(string, char) : 양 끝에 있는 char를 잘라냄 char 지정 안 할 시 공백 자름. LTRIM, RTRIM도 있다.

  #### 날짜형 함수

  SYSDATE는 지금 시간을 보여주고, 타입은 날짜 타입이다.

- EXTRACT(YEAR | MONTH | DAY FROM 날짜) : 년, 월, 일을 추출해 보여준다.<br/>
- TO_CHAR(날짜, 'YYYYMMDD') : 날짜형 함수는 아니지만, 날짜형 타입을 다룬다.

  #### 숫자형 함수

- ABS(NUM) : 절댓값<br/>
- SIGN(NUM) : 양수, 음수, 0인지 구별<br/>
- MOD(NUM1, NUM2) : 숫자1을 숫자2로 나누어 나머지를 계산한다. % 써도 됨.<br/>
- CEIL(NUM) : 올림. CEILING 해도 똑같다.<br/>
- FLOOR(NUM) : 내림<br/>
- ROUND(NUM. M) : M번째 자리에서 반올림<br/>
- TRUNC(NUM, M) : M번째 자리에서 절삭. 둘 다 M 생략 시 디폴트값 0

### DECODE

SELECT DECODE(EMPNO, 1000, 'TRUE', 'FALSE')<br/>
FROM EMP;

괄호 안의 1, 2번이 같으면 3번 출력, 다르면 4번 출력

### CASE

SELECT CASE [???]<br/>
   WHEN ~ THEN<br/>
   WHEN ~ THEN<br/>
   ELSE ~<br/>
 END FROM EMP;

CASE를 따지는 그런 구문이당

### ROW

- ROWNUM<br/>
  ORACLE에서 SELECT문의 결과에 대해 주어진 일련번호다.<br/>
  보통 보여줄 행 수를 제한하려고 쓴다.

  SELECT * FROM EMP<br/>
  WHERE ROWNUM < 2;

  SELECT * FROM (SELECT ROWNUM a, ENAME FROM EMP)<br/>
  WHERE a <=5;

  여러 줄을 쓸 땐 이렇게 ..

- ROWID<br/>
  SELECT문으로 확인 가능한 데이터마다 가지는 유일한 값이다. 이것도 ORACLE에 있음.<br/>
  오브젝트 번호, 상대 파일 번호, 블록 번호, 데이터 번호로 나뉜다. 길이는 6363

### WITH

Subquery를 임시 테이블, 뷰같은 느낌으로 사용할 수 있다.<br/>
```
WITH W_EMP AS
(SELECT * FROM EMP WHERE DEPTNO = 30)
SELECT * FROM W_EMP;
```
## DCL

### GRANT

권한을 부여한다.<br/>
GRANT privileges ON Table TO user;<br/>

PRIVILEGES 부분에 들어갈 수 있는 권한에는 이러한 것들이 있다.

SECECT, INSERT, UPDATE, DELETE, REFERENCES, ALTER, INDEX, ALL (모든 권한)

유저명 뒤에 WITH GRANT OPTION 또는 WITH ADMIN OPTION을 붙이면 권한을 줄 권한도 줄 수 있다.

### REVOKE

권한 회수문이다.<br/>
REVOKE privilieges ON Table FROM user;

## TCL

### COMMIT

변경된 데이터가 있으면 DB에 바로 반영되지 않는다.<br/>
커밋을 해줘야 한다. 마치 깃처럼...<br/>
AUTO-COMMIT 기능이 없다면 커밋을 생활화해야 한다.<br/>
이 글도 커밋을 안해서 몇 번 날려먹은 적이 있다. SO SAD

### ROLLBACK

데이터에 대한 변경을 모두 철회하고 트랜잭션을 종료한다.<br/>
즉 저번 커밋 상태로 되돌린다.<br/>
LOCK 상태가 풀려 다른 사용자도 DB 행을 조작할 수 있게 된다.

### SAVEPOINT

COMMIT을 하기 전 세이브포인트를 몇 개 지정한다.<br/>
SAVEPOINT SavepointName;<br/>
하면 된다.

ROLLBACK TO SavepointName; 을 통해 특정 세이브포인트로 돌아갈 수 있다.<br/>
그냥 ROLLBACK 해버리면 싹 초기화되기 때문에 조심.

# SQL 활용편

## Join

여러 개의 릴레이션을 사용해 새 릴레이션을 만드는 과정이다. 기본적으로 교집합을 만든다.<br/>
![EQUIJOIN](https://github.com/nowimseeingcashflow/SQLD-studying/assets/102157329/2a1d86ba-23da-4bb1-ad2d-0b26222b3afd)

이 두 개를 어떻게 합칠 수 있을까?

### EQUI JOIN

SELECT * FROM A, B WHERE A.DEPTNO = B.DEPTNO;

추가적인 조건을 걸 수도 있다.

- INNER JOIN<br/>
SELECT * FROM A INNER JOIN B IN A.DEPTNO = B.DEPTNO;

사용법은 비슷하다.

대신 Explain plan을 들여다보면, EQUI JOIN만 HASH JOIN을 사용한다는 차이점이 있다.

- INTERSECT<br/>
교집합을 조회하는 키워드이다.<br/>
```
SELECT DEPTNO FROM EMP
INTERSECT
SELECT DEPTNO FROM DEPT;
```

두 테이블에서 공통되는 값을 보여줄 것이다.

- NON-EQUI JOIN<br/>
두 테이블을 조회할 때 =이 아닌 부등호들을 활용한다면 NON-EQUI JOIN이 될 것이다.<br/>
아예 겹치지 않는 부분들을 조회할 것이기 때문에..

### OUTER JOIN
```
SELECT * FROM A LEFT OUTER JOIN B
ON A.DEPTNO = B.DEPTNO;

SELECT * FROM A, B WHERE A.DEPTNO (+)= B.DEPTNO;  (오라클 방식)
```

교집합을 조회하고 또한 한쪽에만 있는 데이터들도 포함시킨다.<br/>
LEFT OUTER JOIN, RIGHT OUTER JOIN, 그리고 FULL OUTER JOIN이 있다.

![OUTERJOIN](https://github.com/nowimseeingcashflow/SQLD-studying/assets/102157329/1a9f013e-4582-437a-9b0e-a57000957fde)

이런 느낌으로...

### CROSS JOIN

SELECT * FROM A CROSS JOIN B;

그냥 조건 없이 다 조인한다.<br/>
카테시안 곱이 발생한다. A 행 개수 * B 행 개수.

### UNION
```
SELECT A FROM EMP
UNION
SELECT A FROM DEPT;
```

이 방법을 통해 두 테이블을 합칠 수 있다.<br/>
두 테이블은 행 수, 행 내 데이터 등이 같아야 하며 겹치는 부분들은 1개만 나열된다. 이 과정에서 Sorting이 발생한다.

Sort하지 않고 중복된 것 모두 집어넣는 UNION도 있다. 키워드는 UNION ALL.

### MINUS

UNION이 합집합이라면 MINUS는 차집합을 보여준다.<br/>
차집합이기 때문에 앞의 테이블에 있는 값들 중 뒷 테이블에 있는 값을 제거해서 보여준다.<br/>
UNION과 사용 방법은 동일하다. MS-SQL에서는 EXCEPT라는 키워드로 쓰인다.

## 계층형 조회

connect by ~ 를 사용하는 트리형 구조의 질의.<br/>

```
SELECT MAX(LEVEL) FROM MyTable.EMP
START WITH MGR IS NULL
CONNECT BY PRIOR EMPNO = MGR;
```

MAX(LEVEL)는 가장 깊은 층(마지막 Leaf node)를 나타낸다.<br/>
MGR은 부모 노드를 의미한다. 그렇기에 가장 첫 번째 부모 노드부터 시작하는 것.<br/>
LPAD 함수를 사용하여 시각적으로 더 잘 드러나게 할 수 있다.

```
SELECT LEVEL, LPAD(' ', 4 * (LEVEL - 1)) || EMPNO, MGR, CONNECT_BY_ISLEAF FROM MyTable.EMP
START WITH MGR IS NULL
CONNECT BY PRIOR EMPNO = MGR;
```

깊이에 따라 공백이 띄워지기 때문에 아주 눈에 잘 들어온다.

### 키워드
- LEVEL : 항목의 깊이를 의미한다. 1이 가장 상위 레벨.
- CONNECT_BY_ROOT : 계층 구조에서 가장 최상위값을 표시한다.
- CONNECT_BY_ISLEAF : 계층 구조에서 가장 최하위값을 표시한다.
- SYS_CONNECT_BY_PATH : 계층 구조의 전체 전개 경로를 표시한다.
- NOCYCLE : 순환 구조가 발생지점까지만 전개된다.
- CONNECT_BY_ISCYCLE : 순환 구조 발생 지점을 표시한다.

### 계층형 조회

- START WITH ~ : 계층 전개의 시작 위치를 지정.
- PRIOR 자식 = 부모 : 부모 → 자식의 순방향 전개.
- PRIOR 부모 = 자식 : 자식 → 부모의 순방향 전개.
- NOCYCLE : 데이터를 전개할 때 이미 조회했으면 순환 구조가 형성되는데 이 때 이 사이클을 없앤다.
- ORDER SIBLINGS BY 칼럼명 : 동일한 LEVEL인 형제노드 사이에서 정렬 수행.

## Subquery

서브쿼리란... 쿼리 안의 쿼리 즉 SELECT문 안에서 SELECT문을 다시 사용하는 것이다.<br/>
FROM문 안에서 SELECT문을 사용하는 인라인 뷰, SELECT문 안의 SELECT문인 Scala Subquery 등이 있다.

```
SELECT * FROM EMP
WHERE DEPTNO = (SELECT DEPTNO FROM DEPT WHERE DEPTNO = 10);
```

### 반환 행 개수
단일 행을 반환하는 서브쿼리와 다중 행을 반환하는 서브쿼리가 차이가 있다.<br/>

- 단일 행 서브쿼리
  서브쿼리를 실행시켰을 때 한 행만 조회된다.<br/>
  비교 연산자인 =, <, <= 등을 사용한다.

- 다중 행 서브쿼리
  반면 이 친구는 다중 행 연산자를 사용해야 한다.
  - IN : OR 연산이다. 다중 행 중에서 조건을 만족하는 것이 있다면 참.
  - ALL : 다중 행 중에서 모두 조건을 만족해야 참이다.
  - ANY : 다중 행 중에서 한 가지 조건을 만족하면 참이다.
  - EXISTS : 다중 행 검색해서 있으면 SELECT 1, 즉 참을 반환해준다.

### Scala Subquery

```
SELECT ENAME AS "이름", SAL AS "급여",
               (SELECT AVG(SAL) FROM EMP) AS "평균급여"
FROM EMP WHERE EMPNO = 1000;
```
여기서 평균급여의 개수가 여러 개이면 어떻게 될까?<br/>
문제가 있을 것이다... 스칼라 서브쿼리는 한 행, 한 칼럼만 반환하게 된다.<br/>
여러 항을 반환하면 오류가 발생.

### Correlated Subquery

Subquery 내에서 Main query 내의 칼럼을 사용하는 것이다.<br/>
```
SELECT EMPNO, ENAME, DEPTNO, MGR, JOB, SAL
FROM EMP a
WHERE a.DEPTNO =
   (SELECT DEPTNO FROM DEPT b
   WHERE b.DEPTNO = a.DEPTNO);
```
WHERE문 안에서 서브쿼리를 사용, 그러나 그 서브쿼리에서 메인 쿼리인 a를 호출한다. 이것이 연관 서브쿼리.

## 그룹 함수
### ROLLUP

GROUP BY의 칼럼에 대하여 Subtotal을 만든다.<br/>
GROUP BY가 어떤 칼럼에 대해 총합계를 보여주는 역할을 하는데 ROLLUP 함수가 여기서 이걸 대신해 총합계를 보여준다.<br/>

### GROUPING SETS

GROUP BY에 나오는 칼럼들으로 여러 가지 소계를 만들어준다.<br/>
GROUPING SETS에 넣은 칼럼들으로 소계를 도출한다.

### CUBE

다차원 집계를 제공해 다양한 데이터 분석을 가능하게 한다.<br/>
예를 들어 칼럼에 DEPTNO(부서)와 JOB(직업)이 있다고 한다면,<br/>
부서 내 직업별 소계, 직업별 소계, 부서별 소계, 그리고 전체 소계 이렇게 모든 차원의 집계를 제공할 것이다.

### GROUPING

GROUPING 함수를 사용하면 ROLLUP, CUBE, GROUPING SETS 함수들을 사용한 행은 1을 반환한다.<br/>
실제로 1을 보여주려고 쓰는 함수가 아니겠지? 테이블을 만들 때 편의를 위해 사용한다.

## 윈도우 함수

행과 행 간의 관계를 정의하기 위해 제공된다.

```
SELECT WINFUNC(...ARGUMENTS)
OVER(ORDER BY WINDOWING절
 또는 PARTITION BY 칼럼)
FROM 테이블;
```
대충 이렇게 쓴다

```
SELECT EMPNO, ENAME, SAL, SUM(SAL)
      OVER(ORDER BY SAL
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS "합계"
FROM EMP;
```

![ㄹㅇㄴㄻㄴㅇㄹㅇㄴㅁ](https://github.com/nowimseeingcashflow/SQLD-studying/assets/102157329/96f593bc-09ea-4af6-ba87-24447df998ed)

왜 이런 모양으로 다 같은 값이 나올까?<br/>
각 행에서 UNBOUNDED하게 위아래 SAL을 모두 더했기 때문이다.<br/><br/>
이러면 의미없으니... 이런 모양을 방지하기 위해<br/>
CURRENT ROW나 5 PRECEDING과 같이 앞에 값을 지정해서 PRECEDING 또는 FOLLOWING 행을 더할 수 있다.
<br/><br/>
ROWS 대신에 RANGE도 사용하는데 사실 왜 있는지 모르겠다. RANGE를 쓰면 중복되는 값은 숫자가 이상하게 나온다.<br/>
https://superkong1.tistory.com/42 참조.

### 순위 함수

윈도우 함수 중에서 행 간의 순위를 매기는 데에 사용할 수 있는 함수이다.<br/>
RANK(), DENSE_RANK() 그리고 ROW_NUMBER()이 있고, 사용법은 같으나 출력값만 약간씩 달라진다.<br/>
```
SELECT ENAME, SAL,
RANK() OVER(ORDER BY SAL DESC) ALL_RANK,
DENSE_RANK() OVER(ORDER BY SAL) DENSERANKY,
ROW_NUMBER() OVER(ORDER BY SAL) ROWNUMB
FROM EMP;
```

RANK()는 일반적인 순위를 보여줄 것이고,<br/><br/>
DENSE_RANK()는 공동 순위가 있어도 동일한 순위는 하나의 건수로 계산한다. <br/>예를 들어 4명의 사람이 있고 1등 2등 2등 다음에 누가 와야 한다면 RANK()에서는 4등이 나와야겠지만 DENSE_RANK()는 3등이 나온다.<br/><br/>
ROW_NUMBER()에서는 공동 순위가 있더라도 1234 순으로 간다.

### 집계 함수

PARTITION 별로 값을 보여주는 함수이다.<br/>
SUM(N), AVG(N), COUNT(N), MAX(N), MIN(N)이 있다.

### 행 순서 관련 함수

상위 행 또는 이후에 나오는 행, 최상위 최하위 행 등을 보여줄 수 있는 함수들이다.<br/>
FIRST_VALUE(N), LAST_VALUE(N), LAG(N), LEAD(M, INT=1) 등이 있다.

### 비율 관련 함수

행들을 비율로 나누는 역할을 한다!<br/>
CUME_DIST(), PERCENT_RANK(), NTILE(INT), RATIO_TO_REPORT() 등이 있다.

## 테이블 파티션

대용량의 테이블을 여러 개의 데이터 파일로 분리해서 저장할 수 있다.<br/><br/>
파티션의 기준은 여러 가지.. Range를 설정해서, 숫자 값에 따라서 List 파티션도 가능하고, DB 시스템 상에서 해시값을 받아와 분류할 수도 있을 것이다.
<br/><br/>
파티션 인덱스는 4가지 종류가 있다.<br/>
- Global Index : 여러 개의 파티션에서 하나의 인덱스만 사용
- Local Index : 해당 파티션 별로 각자의 인덱스 사용
- Prefixed Index : 파티션 키와 인덱스 키가 동일한 것
- Non Prefixed Index : 파티션 키와 인덱스 키가 다른 것

## SQL 최적화의 원리

DB 관리 시스템에서 SQL 실행 계획을 세우는 옵티마이저라는 게 있다.<br/>
이 실행 계획을 어떻게 수립할 것인가 방향을 세워주는 게 개발자의 역량이기 때문에 우리가 어떻게 작성하느냐도 중요할 것..<br/>
<br/>
실행 계획은 PLAN_TABLE 에서 확인할 수 있다.<br/>
옵티마이저가 계획한 것들은 PLAN_TABLE로 간다는 것.

옵티마이저 엔진은 규칙 기반 옵티마이저와 비용 기반 옵티마이저를 사용한다.<br/>
요즘에는 기본적으로 비용 기반 옵티마이저를 사용한다. 총 비용이 적은 방향으로 계획을 수립한다.
<br/><br/>
- 인덱스
<br/>인덱스는 데이터를 빠르게 검색할 수 있는 방법을 제공한다.<br/>
인덱스는 인덱스 키로 정렬이 되어있기 때문에 빠르게 검색을 가능하게 한다.<br/> 오름차순, 내림차순으로도 탐색이 가능하다.<br/>
<br/>테이블을 생성할 때 기본키는 자동으로 인덱스가 만들어지고 인덱스의 이름은 SYSXXXX가 된다.
<br/><br/>
인덱스의 구조는 Root Block, Branch Block, Leaf Block으로 구성된다.
Root는 가장 상위의 한 개의 블록이고,<br/>
Branch는 다음 단계의 주소들을 가지고 있는 포인터로 구성되며,<br/>
Leaf Block이 실사용되는 인덱스 키:ROWID의 쌍으로 정렬되어 저장되어 있는 테이블 인덱스이다.
<br/>Leaf Block은 링크드 리스트로 되어있어 양방향 탐색이 가능하다.
<br/>
인덱스의 생성은 CREATE INDEX 문을 사용하면 생성할 수 있다.<br/>
<br/>
- 인덱스 스캔

<br/>인덱스의 스캔에는 3가지 방법이 있다.<br/>
가장 빠른 방법인 Unique 스캔은 중복된 키 값이 없을 때, 인덱스를 사용하면 1가지 값을 가져온다.<br/>
Range 스캔에는 WHERE문이 사용된다. LIKE나 BETWEEN과 비슷하다.<br/>
Full 스캔은 leaf block을 전부 탐색하기 때문에 결국 가장 오래 걸린다.<br/><br/>

- 옵티마이저 조인
  - Nested Loop 조인
    하나의 테이블에서 데이터를 먼저 찾고 그다음 테이블을 조인하는 방식이다.<br/>
    Outer Table -> Inner Table 순서로 간다.<br/>
    RANDOM ACCESS를 최대한 줄이는 것이 Nested loop의 관건이다.

  - Sort Merge 조인
    두 개의 테이블을 메모리 공간에 모두 로딩하고 sort함.
    <br/>sort가 끝나면 merge한다.
    <br/>데이터 양이 너무 많으면 성능이 떨어지는 방법이다.

  - Hash 조인
    두 개의 테이블 중에서 작은 테이블을 해시 메모리에 로딩하고 조인 키를 사용해서 해시 테이블을 생성한다.<br/>
    해시 함수를 사용해서 주소를 계산하고 해당 주소를 사용해서 테이블을 조인해야 하기 때문에 CPU 연산이 많다.<br/>
    
