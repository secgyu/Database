# 테이블 생성문제
1️⃣
> 주키 선언과 참조 무결성 제약을 포함하여 Company 테이블을 위한 CREATE TABLE 문장을 작성하여라.
```sql
CREATE TABLE Company (
    NAME VARCHAR2(100) PRIMARY KEY,
    address VARCHAR2(200),
    netWorth NUMBER(15, 2),
    President VARCHAR2(100),
    CONSTRAINT fk_president FOREIGN KEY (President) REFERENCES Member(NAME)
);
```
<br/r>

# 제약 문제

2️⃣
> 영화를 제작할 수 있으려면 1985년 이전에 태어나야 하며 1,000,000 이상의 재산이 있어야 한다는 의미의 CHECK 제약을 Movie 테이블에 추가하여라.
```sql
Alter table Movie
ADD CONSTRAINT chk_movie_production
CHECK (YEAR < 1985 OR 
       EXISTS (SELECT 1 FROM Member WHERE Member.NAME = Director AND Member.netWorth >= 1000000) OR 
       EXISTS (SELECT 1 FROM Member WHERE Member.NAME = Producer AND Member.netWorth >= 1000000)
);
```

<br/r>

3️⃣

> 2번 문제의 CHECK 제약은 완전하게 유지되지 못한다. 그 이유를 설명하고 이 문제를 해결할 수 있는 방안을 제시하여라.

- SQL 표준에서는 CHECK 제약 조건이 서브쿼리를 포함할 수 없습니다.  
 또한, CHECK 제약 조건은 단일 행만을 검증할 수 있으며, 다른 테이블의 데이터를 참조할 수 없습니다.  
 따라서 위의 chk_movie_production 제약은 대부분의 DBMS에서 작동하지 않는다.  
 해결방안으로 전역제약인 ASSERTION 혹은 트리거를 사용한다.
