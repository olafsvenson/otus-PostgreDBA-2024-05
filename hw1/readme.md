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


![](files/7.png)

**Видите ли вы новую запись и если да то почему?**

**Нет, запись во второй сессии не видна, т.к. транзакция в первой не завершена, это из-за текущего уровня изоляции Read Commited.**

8) Завершить первую транзакцию
``` sql  
commit;
```
![](files/8.png)

9) Сделать во второй сессии
``` sql  
select * from persons;
```

![](files/9.png)


**Видите ли вы новую запись и если да то почему?**

**Теперь когда первая транзакция заверешена изменения видны во второй.**


10) Завершите транзакцию во второй сессии
``` sql  
commit;
```
![](files/10.png)

11) Начать новые но уже repeatable read транзации
``` sql     
\set AUTOCOMMIT off
set transaction isolation level repeatable read;
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
![](files/13.png)

**Видите ли вы новую запись и если да то почему?**

**Нет**

14) Завершить первую транзакцию
``` sql 
commit;
```
![](files/14.png)

15) Сделать во второй сессии
``` sql
select * from persons;
```

![](files/15.png)

**Видите ли вы новую запись и если да то почему?**

**Нет, даже после завершения транзакции в первой сессии запись во второй не видна, это из-за уровня изоляции repeatable read**

16) Завершить вторую транзакцию
сделать во второй сессии
``` sql
select * from persons;
```
![](files/16.png)

**Видите ли вы новую запись и если да то почему?**

**ДА, когда завершили транзакцию во второй сессии запись видна**
