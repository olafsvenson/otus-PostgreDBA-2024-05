# Работа с уровнями изоляции транзакции в PostgreSQL

1) Запустить везде psql из под пользователя postgres
``` text
sudo -u postgres /usr/pgsql-15/bin/psql
```
![](files/1.png)

2) Выключить auto commit
``` sql   
\set AUTOCOMMIT off
```
![](files/2.png)

3) В первой сессии новую таблицу и наполнить ее данными:
 ``` sql    
 create table persons(id serial, first_name text, second_name text); 
 insert into persons(first_name, second_name) values('ivan', 'ivanov'); 
 insert into persons(first_name, second_name) values('petr', 'petrov'); 
 commit;
```
![](files/3.png)

4) Посмотреть текущий уровень изоляции:
``` sql    
show transaction isolation level;
```
![](files/4.png)

5) Начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции
   
   **первая сессия**
![первая сессия](files/5_1.png)

   **вторая сессия**
![первая сессия](files/5_2.png)

7) В первой сессии добавить новую запись
``` sql
insert into persons(first_name, second_name) values('sergey', 'sergeev');
```
![](files/6.png)

7) Сделать во второй сессии
``` sql
select * from persons;
```
**видите ли вы новую запись и если да то почему?**

![](files/7.png)

8) Завершить первую транзакцию
``` sql  
commit;
```
![](files/8.png)

9) Сделать во второй сессии
``` sql  
select * from persons;
```
**видите ли вы новую запись и если да то почему?**
![](files/9.png)

10) Завершите транзакцию во второй сессии
``` sql  
commit;
```
![](files/10.png)

11) Начать новые но уже repeatable read транзации
``` sql     
\set AUTOCOMMIT off
set transaction isolation level repeatable read;
begin;
```
   **первая сессия**
![](files/11_1.png)
  
   **вторая сессия**
![](files/11_2.png)


12) В первой сессии добавить новую запись
``` sql    
insert into persons(first_name, second_name) values('sveta', 'svetova');
```
![](files/12.png)

13) Сделать во второй сессии
``` sql   
select * from persons;
```
**видите ли вы новую запись и если да то почему?**
**нет**
![](files/13.png)

14) Завершить первую транзакцию
``` sql 
commit;
```
![](files/14.png)

15) Сделать во второй сессии
``` sql
select * from persons;
```
**видите ли вы новую запись и если да то почему?**
**нет**
![](files/15.png)

16) Завершить вторую транзакцию
сделать во второй сессии
``` sql
select * from persons;
```
![](files/16.png)

**видите ли вы новую запись и если да то почему? **
