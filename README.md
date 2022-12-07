# projectdogs

<a>https://drawsql.app/teams/projectdogs/diagrams/projectdogs</a>


![Screenshot 2022-12-06 at 21 19 21](https://user-images.githubusercontent.com/108239463/205990931-a5847f78-289c-48b5-a351-80ca997112b4.png)

## 1

Как часто гуляют? Сколько раз за последний месяц гуляли?

```sql
select
      um.user_id,
      u.name,
      count(*) as count_of_meetings  
from user_on_meetings um
           
left join meetings m
on um.meeting_id=m.meeting_id
           
left join users u
on um.user_id=u.user_id
           
           
where m.start_time>to_timestamp('01-11-2022 00:00:00', 'dd-mm-yyyy hh24:mi:ss') AND m.end_time<to_timestamp('30-11-2022 23:59:59', 'dd-mm-yyyy hh24:mi:ss')
group by um.user_id, u.name
order by count_of_meetings DESC
```

## 2
Пользователь, чаще всего организующий прогулки?
```sql
select
  m.user_id_creator,
  u.name user_name,
  count(m.user_id_creator) as count_of_meetings
from meetings m

left join users u
on m.user_id_creator=u.user_id

group by user_id_creator, user_name
order by count_of_meetings DESC
limit 1
```

## 3
Протяженность прогулки?
```sql
select 
  meeting_name,
  to_char(end_time - start_time, 'hh24:mi:ss') as meeting_time
from meetings
           
group by meeting_name, meeting_time
order by meeting_time DESC
```

## 4
Найти, были ли прогулки, где в группе были две собаки с одинаковыми именами?
```sql
select 
  d.name,
  m.meeting_name
from meetings m
           
left join user_on_meetings um
on m.meeting_id=um.meeting_id
           
left join users u
on u.user_id=um.user_id
           
left join dogs d
on d.user_id=u.user_id
           
           
group by d.name, m.meeting_name
HAVING count(distinct dog_id)>1
```

## 5
Количество подписчиков у каждого пользователя? Самый популярный пользователь? 
```sql
select 
  u.name as followers_of,
  u.user_id,
  count(*) as followers_count 

from followers f 
left join users u
on f.user_to=u.user_id
group by u.name, u.user_id
```

## 6
Найти платные прогулки? 
```sql
select 
  pm.meeting_id,
  m.meeting_name,
  pm.price
from paid_meetings pm
           
left join meetings m
on pm.meeting_id=m.meeting_id
           
group by pm.meeting_id, m.meeting_name, pm.price
```

## 7
Прогулка, где было больше всего собак?
```sql
select
  um.meeting_id,
  count(d.dog_id) as count_of_dogs
                  
from user_on_meetings um
           
left join users u
on um.user_id=u.user_id
           
left join dogs d
on um.user_id=d.user_id
           
left join meetings m
on um.meeting_id=m.meeting_id
           
           
group by um.meeting_id
           
order by count_of_dogs DESC
```

## 8
Сколько активных собак на прогулке?
```sql
select
  m.meeting_name,
  d.name,
  count(d.dog_id) as count_of_dogs
                  
from user_on_meetings um
           
left join users u
on um.user_id=u.user_id
           
left join dogs d
on um.user_id=d.user_id
           
left join tempers t
on d.temper_id=t.temper_id
           
left join meetings m
on um.meeting_id=m.meeting_id
           
where t.temper = 'active' and m.meeting_id=5
group by m.meeting_name, d.name
```

