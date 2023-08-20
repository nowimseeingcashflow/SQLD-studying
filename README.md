# SQLD-studying
자격증 취득을 위한 공부를 기록하려고 합니다

데이터 모델링의 이해 부분은 블로그에 작성했습니다
https://blog.naver.com/happyphjm1/223178609287

# 관계형 데이터베이스 SQL
릴레이션에 데이터를 저장하고 관리한다
Parent-Child 관계로 표현하기 쉬움

SQL은 ANSI/ISO SQL 표준을 준수하기 때문에 여러 가지 관리 시스템 바뀌어도 괜찮음
(관리 시스템 ex. MYSQL, Oracle...)

## SQL 종류
### 종류
   - DDL (Data Definition) : 구조 정의 (Create, Alter, Drop, Rename...)
   - DML (Data Manipulation) : 데이터 입력, 수정, 삭제, 조회 (Insert, Update, Delete, Select...)
   - DCL (Data Control) : 데이터베이스 사용자 권한 설정 (Grant, Revoke..)
   - TCL (Transaction Control) : 트랜잭션 제어 (Commit, Rollback, Savepoint...)

### 실행 순서
1. 파싱 : 문법 확인, 구문 분석 → Library Cache에 저장
2. 실행 : 옵티마이저가 수립한 계획에 따라 SQL 실행
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

대충 이런 식으로 한다

empno는 number~
emane은 varchar2~
default 설정도 가능

primary key 설정은 constraint문으로 할 수도 있다. 
constraint emp_pk primary key(empno) 이런 식으로~

constraint key 로는 foreign key 설정도 할 수 있다
하위 테이블에서 마스터 테이블을 참조하면 된다
```
constraint dept_fk foreign key(deptno)
   references dept(deptno)
   ON DELETE CASCADE
```
(EMP 테이블이 DEPT 테이블을 참조했다)

CASCADE란 단어 뜻대로 단계적으로 적용되는 것을 의미한다 위 예시에선 DEPT 테이블이 삭제되면 EMP 테이블도 삭제되는 역할을 한다<br/>
나중에 DELETE 구문에서도 활용 예제가 있다

### ALTER

- 테이블명 변경
  ALTER TABLE ~ RENAME TO

- 칼럼 추가/변경
  - 추가
    ALTER TABLE ~ ADD 하면 된다
    ```
    ALTER TABLE EMP
       ADD (age number(2) default 1);
    ```
  - 변경
    ALTER TABLE ~ MODIFY
    추가할 때랑 비슷하다

  - 칼럼명 변경
    ALTER TABLE ~ RENAME COLUMN ~ TO
    테이블명 변경과 비슷

  - 삭제
    ALTER TABLE ~ DROP
    밑에서 서술할 DROP을 참고하면 된다

### DROP
테이블을 삭제하고 싶을 때<br/>
  ***DROP TABLE***<br/>
구문으로 손쉽게 처리가 가능하다.<br/>
후에 나올 DELETE과는 많은 차이가 있다<br/>
일단 DROP은 테이블 구조 자체를 없애버리고 되돌릴 수 없음을 알고 있어야 한다.

DROP TABLE ~ CASCADE CONSTRAINT<br/>
지우려는 테이블을 참조하는 슬레이브 테이블의 제약사항들까지 모두 없애버리는 구문이다

#### VIEW
뷰는 테이블에서 유도된 가상의 테이블 같은 것이다<br/>보기 전용으로 만들어진 테이블 복사본 정도로 이해하면 될 것 같다<br/>테이블에서 따오고 싶은 부분만 참조해온다

- 특징
  - 참조한 테이블이 변하면 뷰도 변한다
  - 뷰의 검색 외에 입력, 수정, 삭제 등에는 제약이 있다
  - 특정 칼럼만 조회시켜 보안성 ↑

- 생성
  ```
  CREATE VIEW T_EMP AS
     SELECT ~ FROM EMP;
  ```
  ~ 부분엔 가지고 오고 싶은 칼럼들을 적는다<br/>
  *를 쓰면 전부 가져온다

- 조회<br/>
조회는 SELECT 문으로 일반 테이블처럼 하면 된다<br/>
삭제도 DROP으로 하면 된다
  
