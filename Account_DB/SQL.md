# SQL 코딩
![bank](https://github.com/secgyu/Database/blob/main/Account_DB/BankingDB.png)
1️⃣
> 10,000,000원 이상을 대출한 고객 이름과 주소를 고객 이름의 역순으로 검색하여라.
```sql
select name, address
from customer
where ss_no in (select ss_no
                from loan
                where amount >= 10000000)
order by name desc;
```
<br/>

2️⃣
> 2010년도에 개설된 통장의 계좌번호, 계좌주 이름은?
```sql
select a.num, c.name
from account a
join customer c on a.ss_no = c.ss_no
where a.open_date >= '2010-01-01' and
      a.open_date <= '2010-12-31';
```
<br/>

3️⃣
> "부산시"에 소재한 지점에서 통장을 개설한 고객의 이름, 주소, 총 잔액, 총 대출액은?
```sql
select c.name, c.address, sum(a.balance) total_balance, sum(l.amount) total_amount
from customer c
join account a on a.ss_no = c.ss_no
join branch b on a.branch_name = b.name
join loan l on c.ss_no = l.ss_no
where b.city like '%부산%'
group by c.name, c.address;
```
<br/>

4️⃣:
>각 지점에 대해서 총 대출 액수 보다 자산액이 더 큰 지점 명과 소재 도시는?
```sql
SELECT b.name, b.city
FROM branch b
WHERE b.assets > (SELECT SUM(l.amount)
                  FROM loan l
                  WHERE l.branch_name = b.name);
```
<br/>

5️⃣
> 두 번 이상(>=2) 대출을 받은 고객의 이름과 주민등록번호는?
```sql
select name, ss_no
from customer
where ss_no in (select ss_no
                from loan
                group by ss_no
                having count(num) >= 2);
```
<br/>

6️⃣
> 대출 액수의 총 합이 가장 적고 통장의 총 잔액이 가장 많은 지점의 이름과 도시명은?
```sql
select name, city
from branch
where name = (select branch_name
              from loan
              group by branch_name
              having sum(amount) in (select min(total_amount)
                                     from (select sum(amount) as total_amount
                                           from loan
                                           group by branch_name)min_money))
                                           
union

select name, city
from branch
where name in (select branch_name
               from account
               group by branch_name
               having sum(balance) in (select max(total_balance)
                                       from (select sum(balance) as total_balance
                                             from account
                                             group by branch_name)max_money))
```
<br/>

7️⃣
> 가장 자산액이 많은 지점의 대출 중에 가장 작은 대출을 받은 고객의 이름과 주소는?
```sql
select c.name, c.address
from customer c
join loan l on l.ss_no = c.ss_no
where l.amount = (select min(l2.amount)
                  from loan l2
                  where l2.branch_name = (select b.name
                                          from branch b
                                          where b.assets = (select max(b2.assets)
                                                            from branch b2)))
```
<br/>

8️⃣
> 1990년 이전에 개설한 통장이 있는 지점 명과 그 지점의 총 통장의 잔액 및 총 대출 액수는?
```sql
select b.name,
       (select sum(balance) 
        from account
        where b.name = account.branch_name) as 총잔액,
       (select sum(amount) 
        from loan
        where b.name = loan.branch_name) as 총대출
from branch b
where b.name in (select branch_name 
                 from account
                 where open_date < '1990-01-01')
```
