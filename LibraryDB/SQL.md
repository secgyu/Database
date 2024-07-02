# SQL 코딩
![shopping](https://github.com/secgyu/Database/blob/main/LibraryDB/LibraryDB.png)

1️⃣
> 2020년에서 2022년 사이에 발간한 도서의 제목과 분류를 발간날짜 순과 소장 도서의 역순으로 검색하여라.
```sql
select b.title, b.category
from book b
where p_date >= '2020-01-01' and 
      p_date <= '2022-12-31'
order by p_date asc, stocks desc;
```

2️⃣
> "대연동"에 사는 회원과 사서의 이름과 생년월일은?
```sql
select name, address, '회원' m_or_l
from member
where address like '%대연동%'

union

select name, address, '사서' m_or_l
from librarian
where address like '%대연동%';
```

3️⃣
> 성이 '황'이고 '희'로 끝나는 이름의 사서가 대출해준 도서의 제목과 반납 예정날짜는?
```sql
select b.title, r.dueDate
from book b
join Rental r on r.isbn_no = b.isbn_no
where lib_id in (select id from librarian where name like '황%희');
```

4️⃣
> 도서의 제목, 저자 이름과 이 도서를 대출한 회원 이름을 검색하여라.
```sql
select 
    b.title,
    (SELECT m.name FROM member m WHERE m.id = b.author_id) AS 저자이름,
    (SELECT m.name FROM member m WHERE m.id = l.mem_id) AS 회원이름
from book b
join Rental r ON b.isbn_no = r.isbn_no
```

5️⃣
> 가장 나이 많은 회원이 가장 적은 수를 소장한 도서의 대출을, 사서들 중에서 가장 어린 사서가 담당한,
  이 도서의 제목과 발간날짜를 검색하여라.
```sql
select b5.title, b5.pub_date
from Rental r5
join book b5 on r5.isbn_no = b5.isbn_no
join member m5 on r5.mem_id = m5.id
join librarian lib5 on r5.lib_id = lib5.id
where m5.birthdate = (select min(birthdate) from member3)
      and 
      lib5.id = (select id 
                 from (select lib.id, max(lib.birthdate)
                       from Rental r2
                       join member mem2 on r2.mem_id = mem2.id
                       join book b2 on r2.isbn_no = b2.isbn_no
                       join librarian lib on r2.lib_id = lib.id
                       where mem2.birthdate in (select min(m3.birthdate) from member m3) 
                       and 
                       b2.isbn_no like (select isbn_no from (select isbn_no, min(b.stocks)
                                                             from Rental r
                                                             join member mem on r.mem_id = mem.id
                                                             join book b on r.isbn_no = b.isbn_no
                                                             where mem.birthdate in (select min(m2.birthdate) from member3 m2) 
                                                             group by b.isbn_no))
                group by lib.id));
```

6️⃣
> 가장 최근에 발간된 도서의 제목과 소장수를 검색하여라. 단, 그룹화 연산자를 사용하지 않는다.
```sql
select title, stocks
from book 

minus

select b1.title, b1.stocks
from book b1, book b2
where b1.p_date < b2.p_date;
```

7️⃣
> 각 회원이 반납 예정날짜 이후에 반납한 도서의 수와 최초 대출날짜를 검색하여라.
```sql
select *
from (select mem_id, count(isbn_no)
	from Rental
	where returnDate < dueDate
	group by mem_id) a
join (select mem_id, min(rentdate)
       from Rental
       where returnDate < dueDate
       group by mem_id) b on a.mem_id = b.mem_id;
```
