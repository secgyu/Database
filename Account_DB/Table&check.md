# 테이블 생성문제
![bank](https://github.com/secgyu/Database/blob/main/Account_DB/BankingDB.png)
1️⃣
> 주키 및 두 개의 외래키 선언을 포함하여 Loan 테이블을 생성하기 위한 CREATE TABLE 문장을 작성하여라.  
   각 애트리뷰트의 의미에 맞게 타입을 적절하게 선택해야 한다.

```sql
CREATE TABLE Loan (
    NUM NUMBER PRIMARY KEY,
    branch_name VARCHAR2(50),
    SS_no VARCHAR2(13),
    amount NUMBER(*, 0),
    CONSTRAINT fk_branch FOREIGN KEY (branch_name) REFERENCES Branch(NAME),
    CONSTRAINT fk_customer FOREIGN KEY (SS_no) REFERENCES Customer(SS_NO)
);
```
<br/>
2️⃣
> Loan 테이블의 두 외래키에 대한 참조무결성 제약을 위반 시킬 수 있는 모든 DML 연산을 나열하여라.
- INSERT : branch_name, ss_no가 각 참조되는 테이블에 없을 시 위배된다.
- UPDATE : branch_name, ss_no가 각 참조되는 테이블에 없는 다른 값으로 변경 시 위배된다.
- DELETE :
  - BRANCH 테이블에서 name을 삭제 시 Loan 테이블에서 branch_name과 동일한 값을 삭제 시, 참조무결성 위배된다.
  - Customer 테이블에서 ss_no를 삭제 시 Loan 테이블에서 ss_no와 동일한 값을 삭제 시, 참조무결성이 위배된다.

# 제약 문제
3️⃣
> "고객의 생년월일 전에 통장을 개설할 수 없다"를 Account의 CHECK 제약으로 작성하여라.

```sql
ALTER TABLE ACCOUNT
ADD CONSTRAINTS chk_birthdate
check(open_date >= (select birthdate
                    from Customer
                    where Account.ss_no = Customer.ss_no)
);
```

<br/>

4️⃣
> 3번 문제에서 작성한 CHECK 제약은 완전한가? 그렇지 않다면 그 이유를 설명하여라.
- CHECK 제약은 Account 테이블에 한정된 제약이지만, 이 제약은 Customer 테이블까지 참조한다.  
  테이블의 값이 변경될 시 Customer 테이블까지 참조하는것은 불가능하기 때문에 제약에 위배된다.
