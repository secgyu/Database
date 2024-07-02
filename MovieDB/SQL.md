# SQL 코딩
![bank](https://github.com/secgyu/Database/blob/main/MovieDB/MovieDB.png)

1️⃣
> 2020년 5월에 출연 계약한 배우의 이름과 생년월일은?
```sql
select name, birthdate
from member
where name in (select mstar
from starsin2
               where contdate like '2020-05-%')
```

2️⃣
> 120분 이상의 상영시간을 갖는 영화의 제목, 감독과 제작자의 이름과 성별은?
```sql
select m.title, md.name 감독이름, md.gender 감독성별, mp.name 제작자이름, mp.gender 제작자성별
from movie m
join member md on m.director = md.name
join member mp on m.producer = mp.name
where m.length >= 120;
```

3️⃣
> 영화를 감독하고 제작한 경험이 있고, 영화에 출연했었던 영화사 사장들의 이름과 운영하고 있는 영화사 이름은?
```sql
select c.president, c.name
from company c
join movie m on m.studio = c.name
join starsin s on s.mtitle = m.title and
                   s.myear = m.year
where c.president = m.director and
      c.president = m.producer and
      c.president = s.mstar
```

4️⃣
> 생년월일이 서로 같은 배우들의 이름과 성별은? (GROUP BY절을 사용하지 말고 작성할 것)
```sql
select m1.name, m1.gender, m2.name, m2.gender
from member m1, member m2
where m1.birthdate = m2.birthdate and m1.name <> m2.name
```

5️⃣
> 영화사 사장들 중 가장 부자인 사장이 운영하는 영화사가 제작한 영화에 출연했던 여배우들의 이름을
 적은 재산액수에서 많은 재산액수 순으로 검색하여라.
```sql
select m.name
from member m
join starsin s on s.mstar = m.name
join movie m on s.mtitle = m.title and s.myear = m.year
join company c on m.studio = c.name
where gender = 'F' and
      c.name = (select c2.name
                from company c2
                where networth = (select max(networth)
                                  from company c3))
order by m.networth desc
```

6️⃣
> "부산시"에 사는 감독과 제작자 참여한 영화들에 대해서 영화사 이름과
  각 영화사 별 제작 영화편수, 평균상영시간, 가장 최근 영화의 개봉년도를 영화편수의 오름차순으로 검색하여라.
```sql
select m.studio,
       count(m.title) as 영화편수,
       avg(m.length) as 평균상영시간,
       max(m.year) as 최근상영시간
from movie m
where m.director in (select name 
                     from member
                     where lower(address) like '%부산시%')
      and
      m.producer in (select name
                     from member
                     where lower(address) like '%부산시%')
group by m.studio
order by count(m.title) asc;
```
