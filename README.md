<h1 align="center" style="color: black">SQL запросы марафон😢😎🥶🥶🥶🥶💀💀💀🫣</h1>
<h3>(1) Задание: Все бегуны, которые зарегистрированы на текущий марафон отображаются в таблице, как: имя, фамилия - BibNumber (CountryCode)</h3>

```sql
select distinct u."firstname", u."lastname", rnr."countrycode"
from "User" u
inner JOIN "Role" r on u."roleid" = r."roleid"
inner join "Runner" rnr on u."email" = rnr."email"
inner join "Registration" reg on rnr."runnerid" = reg."runnerid"
inner join "RegistrationEvent" rei on reg."registrationid" = rei."registrationid"
inner join "Event" ev on rei."eventid" = ev."eventid" 
where ev."marathonid" = 5;
```
<h3>(2) Таблица с результатами бегуна с предыдущих соревнований. Должен отображаться пол, возраст 
Список должен показать каждое соревнование, в котором бегун соревновался ранее.
</h3> 

```sql
select g."gender", date_part('Year', age(rnr.dateofbirth)) as age, ev."eventname"
from "Runner" rnr
inner join "Registration" reg on rnr."runnerid" = reg."runnerid"
inner join "RegistrationEvent" rei on reg."registrationid" = rei."registrationid"
inner join "Event" ev on rei."eventid" = ev."eventid"
inner join "Gender" g on rnr."genderid" = g."genderid"
```
<h3>(3) Выводятся следующие поля для каждого события:
<h5>• Марафон: полное название марафона.</h5>
<h5>• Событие: полное название события.</h5>
<h5>• Время: время гонки бегуна на события в часах, минутах и секундах.</h5>
<h5>• В целом положение бегуна в марафоне среди всех участников.</h5>
<h5>• Отдельно по категории положение бегуна на событии, среди бегунов одного того же пола и той же возрастной категории.</h5>
</h3>

```sql
select m."marathonname", ev."eventname", to_char( (rei."racetime" ||'seconds')::interval, 'HH24:MI:SS' ) 
as race_time, ROW_NUMBER() OVER(PARTITION BY ev."eventname" ORDER BY rei."racetime" asc) AS event_position
from "Event" ev 
inner join "Marathon" m on ev."marathonid" = m."marathonid"
inner join "RegistrationEvent" rei on ev."eventid" = rei."eventid"
where ev."marathonid" = '3' and rei."racetime" != 0
order by eventname asc, race_time asc
```
<h3>(4) Необходимо выводить сводные статистические данные по всем результатам, которые соответствуют критериям:
<h5>• Всего бегунов закончило: общее количество бегунов, у которые есть зафиксированное время соревнования.</h5>
<h5>• Среднее время гонки: Среднее значение всех результатов.</h5>
В списке показать всех бегунов, которые соответствуют критериям поиска. Следующие поля должны быть отображены:
<h5>• Ранг: положение бегуна для выбранного события, пола и возрастной категории.</h5>
<h5>• Время гонки: время гонки бегуна в категориях в часах, минутах и секундах.</h5>
<h5>• Имя бегуна: имя бегуна.</h5>
<h5>• Страна: страна бегуна.</h5>
<h5>• Должны отображаться данные бегуна, которая содержит следующую информацию: Фамилия Имя бегуна, страна, возраст в годах полных, на дату марафона, а далее в табличной форме</h5>
</h3>
<h3>4.1)</h3>

```sql
select ev."eventname", count(rei."racetime") as count_of_runners, to_char( (round(avg(rei."racetime"), 0) ||'seconds')::interval, 'HH24:MI:SS' ) as avg_time
from "Event" ev
inner join "RegistrationEvent" rei on ev."eventid" = rei."eventid"
where rei."racetime" != 0
group by ev."eventname"
order by eventname
```
<h3>4.2)</h3>

```sql
select u."firstname", u."lastname",  date_part('Year', age(rnr.dateofbirth)) as age, g."gender", m."marathonname", ev."eventname",  c."countryname", 
TO_CHAR((rei."racetime" || ' seconds')::interval, 'HH24:MI:SS') AS race_time,
ROW_NUMBER() OVER (PARTITION BY ev."eventname" ORDER BY rei."racetime" ASC) AS pposition
FROM "Event" ev
INNER JOIN "Marathon" m ON ev."marathonid" = m."marathonid"
inner join "Country" c on m."countrycode" = c."countrycode"
INNER JOIN "RegistrationEvent" rei ON ev."eventid" = rei."eventid"
inner join "Registration" reg on rei."registrationid" = reg."registrationid" 
inner join "Runner" rnr on reg."runnerid" = rnr."runnerid"
inner join "User" u on u."email" = rnr."email"
inner join "Gender" g on rnr."genderid" =  g."genderid" 
WHERE ev."marathonid" = '3' AND rei."racetime" != 0
ORDER BY ev."eventname" asc, g."gender" asc, race_time asc;
```
<h3>(5) Запрос должен позволить просматривать список всех бегунов, которые зарегистрированы на текущий марафон. Составить запрос, который может фильтровать бегунов по статусу регистрации и выбранным типам марафона, и он может сортировать все поля таблицы результатов. 
Общее количество бегунов, которые выведены в список, должно быть отображено над списком.
Имя, Фамилия, адрес электронной почты и регистрационный статус должны быть указаны для каждого бегуна.
</h3>
<h3>5.1)</h3>

```sql
select distinct u.firstname, u.lastname, rnr.email, rgs.registrationstatus, ev.eventtypeid
from "User" u
inner JOIN "Role" r on u.roleid = r.roleid
inner join "Runner" rnr on u.email = rnr.email
inner join "Registration" reg on rnr.runnerid = reg.runnerid
inner join "RegistrationEvent" rei on reg."registrationid" = rei.registrationid
inner join "Event" ev on rei."eventid" = ev."eventid"
inner join "RegistrationStatus" rgs on reg.registrationstatusid = rgs.registrationstatusid
where rgs.registrationstatusid = 1
order by eventtypeid asc;
```
<h3>5.2)</h3>

```sql
select count(*) from (select distinct u.firstname, u.lastname, rnr.email, rgs.registrationstatus
from "User" u
inner JOIN "Role" r on u.roleid = r.roleid
inner join "Runner" rnr on u.email = rnr.email
inner join "Registration" reg on rnr.runnerid = reg.runnerid
inner join "RegistrationEvent" rei on reg."registrationid" = rei.registrationid
inner join "Event" ev on rei."eventid" = ev."eventid"
inner join "RegistrationStatus" rgs on reg.registrationstatusid = rgs.registrationstatusid
where ev.eventtypeid = 'FM' and rgs.registrationstatusid = 1) as asd
```
<h3>(6) Спонсоры должны быть сгруппированы по благотворительным организациям, которым они перечисляют деньги.
Также должны выводится сводные данные:
<h5>•Благотворительные организации: общее количество благотворительных организаций.</h5>
<h5>•Всего спонсорских взносов: общая сумма спонсорских пожертвований, полученная для всех благотворительных организаций.</h5>
Список должен показать наименование и общее количество спонсоров получил для каждой благотворительной организации.
</h3> 
<h3>6.1)</h3>

```sql
select count(distinct chr."charityname") as char_count, sum(sps."amount") as all_donates
from "Charity" chr
inner join "Registration" reg on chr."charityid" = reg."charityid"
inner join "Sponsorship" sps on reg."registrationid" = sps."registrationid"
```
<h3>6.2)</h3>

```sql
select c."charityname" as charity_name, count(distinct s."sponsorshipid") as vsego_sponsors, sum(s."amount") AS vsego_poshertvovani
from "Charity" c
inner join "Registration" r on c."charityid" = r."charityid"
inner join "Sponsorship" s on r."registrationid" = s."registrationid"
group by c."charityname"
order by charity_name;
```
<h3>(7) Проверка бегуна по условиям:
Все поля обязательно заполнены.
Пароль должен отвечать следующим требованиям:
<h5>•	Минимум 6 символов</h5>
<h5>•	Минимум 1 прописная буква</h5>
<h5>•	Минимум 1 цифра</h5>
<h5>•	По крайней мере один из следующих символов: ! @ # $ % ^</h5>
</h3>

```sql
select u.password, date_part('Year', age(rnr.dateofbirth)) as age
from "User" u
inner join "Runner" rnr on u.email = rnr.email
where u.password like '______%' and u.password ~ '[a-z]' and u.password ~ '[1-9]'
and u.password ~ '[!@#$%^]' and date_part('Year', age(rnr.dateofbirth)) > 10;
