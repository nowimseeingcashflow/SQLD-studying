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

primary key 설정은 constraint문으로 할 수도 있다. 
constraint emp_pk primary key(empno) 이런 식으로~

constraint key 로는 foreign key 설정도 할 수 있다
하위 테이블에서 마스터 테이블을 참조하면 된다
```
constraint dept_fk foreign key(deptno)
  references dept(deptno)
```
(EMP 테이블이 DEPT 테이블을 참조했다)

