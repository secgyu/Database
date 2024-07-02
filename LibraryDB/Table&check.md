# Create Table
![shopping](https://github.com/secgyu/Database/blob/main/LibraryDB/LibraryDB.png)

1️⃣
> 주키 및 두 개의 외래 키 선언을 포함하여 Rental 테이블을 생성하기 위한 CREATE TABLE 문장을 작성하여라.
  각 애트리뷰트의 의미에 맞게 타입을 적절하게 선택해야 한다.
```sql
CREATE TABLE Rental(
  ISBN_NO VARCHAR2(255),
  MEM_ID VARCHAR2(50),
  rentDate DATE,
  dueDate DATE,
  returnDate DATE,
  Lib_ID VARCHAR2(50),
  Primary Key (ISBN_NO, MEM_ID),
  Foreign Key (ISBN_NO) references Book(ISBN_NO),
  Foreign Key (MEM_ID) references Member(ID),
  Foreign Key (Lib_ID) references Librarian(ID)
);
```
2️⃣
> Primary Key와 Unique 제약의 공통점과 차이점을 기술하여라. 이 두 제약을 검증하는 시점을 제시하여라.
- Primary Key와 Unique의 공통점은 해당 필드에 대해서 중복된 값을 입력(허용)할 수 있다.  
  이를 통해 무결성을 유지할 수 있다.
- 차이점은 Primary Key는 해당 필드에 NULL값을 허용하지 않지만, Unique는 NULL 값을 허용한다.

3️⃣
> "도서의분류는 잡지, 소설, 공학, 예술, 수필, 만화 등만 가능하다"를 Book 테이블의 CHECK 제약으로 작성하여라.
```sql
Alter table Book
Add Constraints chk_category
Check (category in ('잡지', '소설', '공학', '예술', '수필', '만화'));
```

4️⃣
> "도서의 대출 날짜는 회원가입 날짜 이후여야 한다"를 Rental 테이블의 CHECK 제약으로 작성하여라.
```
Alter table Rental
Add Constraints chk_joinDate
Check (rentDate >= (select joinDate
                    from Membe
                    where mem_id = id)
);
```

5️⃣
> "각 도서 분류에 해당하는 소장 도서 수의 평균은 모두 5이상(>=)이어야 한다"를 CHECK 제약으로 선언하여라.
```sql
Alter table Book
Add Constraints chk_avg_book
Check ((select AVG(counts)
        from (select count(*) counts
              from Book
              Group By Category) CategoryCount) >= 5);
```

6️⃣
> 5번 CHECK 제약은 해당 제약조건을 완전하게 유지시키지 못한다. 그 이유를 설명하여라.
  이 제약조건을 ASSERTION으로 선언 시 이 문제를 해결할 수 있는 이유는 무엇인가?
- CHECK 제약은 단일 테이블 내에서 컬럼 값을 검사한다.  
  CHECK 제약은 집계함수 및 Sub_Query를 사용 제한 하지만 5번의 제약은 AVG를 사용한다.
  ASSERTION을 사용하게되면 데이터베이스 전체에 걸친 조건을 정의할 수 있게된다.  
  또한 ASSERTION은 집계함수 및 Sub_Query 사용이 가능하다.
