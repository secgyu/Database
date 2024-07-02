# SQL 코딩
![shopping](https://github.com/secgyu/Database/blob/main/WebShoppingDB/WebShoppingDB.png)

1️⃣
> 10개 이상의 재고가 있는 상품의 이름과, 단가를 단가의 내림차순과 상품 번호 순으로 검색한다.
```sql
select name, price
from product
where stocks >= 10
order by price desc, no
```

<br/>

2️⃣
> 지난 4월달에 주문해서 5월달에 배송된 모든 상품의 이름과 주문 수량은?
```sql
select distinct p.name, oi.count
from product p
join orderinfo oi on oi.pNo = p.No
join order o on oi.orderno = o.no
where o.orderdate >= '2024-04-01' and o.orderdate <= '2024-04-30' and
      o.shippingDate >= '2024-05-01' and o.shippingDate <= '2024-05-31'
```

<br/>

3️⃣
> 평가등급이 5이상(>=5)인 생산자가 공급하는 생산자 이름, 상품명, 종류, 가격은?
```sql
select producer, name, category, price
from product
where producer in (select name
                   from producer
                   where rating >= 5)
```

<br/>

4️⃣
> 이번 달 5일 이후에 배송된 각 회원이 주문한 상품의 총 수량과, 총 주문액은?
```sql
select o.memberid,
       sum(oi.count) 총수량,
       sum(oi.count * price) 총주문액
from orderinfo oi
join product p on oi.pNo = p.No
join order o on oi.OrderNo = o.No
where o.shippingDate > '2024-05-05'
group by o.memberid
```

<br/>

5️⃣
> "서울시"에 사는 사장이 운영하는 가장 평가등급이 높은 생산자 이름은? (집단화 연산자 사용 불가)
```sql
select producer.name
from producer, member
where member.address like '%서울시%' and
      producer.boss = member.id

minus

select p1.name
from producer p1, producer p2, member
where member.id = p1.boss and
      member.address like '%서울시%' and
      p1.rating < p2.rating
```

<br/>

6️⃣
> 올해 3월에서 5월 사이에 총 주문 액이 가장 고가인 주문을 한 회원의 이름과 주소는?
```sql
select mem.name, mem.address
from member mem
join order o on o.memberID = mem.id
join orderinfo oi on oi.orderno = o.no
join product p on oi.pno = p.no
where o.orderdate >= '2024-03-01' and 
      o.orderdate <= '2024-05-31' and
      (oi.count * p.price) = (select max(oi2.count * p2.price)
                              from orderinfo oi2
                              join order o2 on oi.orderno = o2.no
                              join product p2 on oi2.pno = p2.no
                              where o2.orderdate >= '2024-03-01' and
                                    o2.orderdate <= '2024-05-31')
group by mem.name, mem.address
```

<br/>

7️⃣
> 주문한 상품 수량보다 재고량이 더 적은 상품을 주문한 회원의 이름과 주문번호는?
```sql
select mem.name, mem.address
from member mem
join order o on o.memberID = mem.id
join orderinfo oi on oi.orderno = o.no
join product p on oi.pno = p.no
where oi.count > p.stocks;
```

<br/>

8️⃣
> "포항시 남구"로 배송되는 상품의 주문날짜 별 총 상품 수량과 주문 날짜는?
```sql
select sum(oi.count) 총상품수량, o.orderdate
from orderinfo oi
join order o on oi.orderno = o.no
where o.shippingAddr like '%포항시 남구%'
group by o.orderdate
```
