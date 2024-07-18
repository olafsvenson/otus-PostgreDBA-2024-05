### Виды и устройство репликации в PostgreSQL. Практика применения.

Создаем отдельные кластера и настроим, они будут отличаться портом.

``` text
Сервер №1 порт 5432
Сервер №1 порт 5433
Сервер №1 порт 5433
```

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_1 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_2 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_3 initdb

sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_1 -o "-p 5432" start
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_2 -o "-p 5433" start
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_3 -o "-p 5434" start
```


Меняем wal_level

Для сервера №1
``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5432
alter system set wal_level = logical;
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_1 -o "-p 5432" restart

Для сервера №2
``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5433
alter system set wal_level = logical;
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_1 -o "-p 5433" restart
```

Создаем таблицы на сервере №1


``` text
CREATE TABLE test1 (id serial PRIMARY KEY, data text);
CREATE TABLE test2 (id serial PRIMARY KEY, data text);
```

Создаем публикацию таблицы test1

``` text
create publication serv1_test1 for table test1;
```

![](files/1.png)

Создаем таблицы на сервере №2

``` text
CREATE TABLE test1 (id serial PRIMARY KEY, data text);
CREATE TABLE test2 (id serial PRIMARY KEY, data text);
```

Создаем публикацию таблицы test2

``` text
create publication serv2_test2 for table test2;
```
![](files/2.png)

И сразу же, находясь на втором сервере, подписываемся на первый сервер

``` text
create subscription serv2_test1_sub
connection 'host=127.0.0.1 port=5432 user=postgres dbname=postgres'
publication serv1_test1 with (copy_data = false);
```

![](files/3.png)

На сервере №1 подписываемся на публикацию со 2-го

``` text
create subscription serv1_test2_sub
connection 'host=127.0.0.1 port=5433 user=postgres dbname=postgres'
publication serv2_test2 with (copy_data = false);
```
![](files/4.png)

Проверяем подписки

Подключаемся к серверу №1 и вставляем данные

``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5432
insert into test1(data) values ('serv1_value1');
insert into test1(data) values ('serv1_value2');
insert into test1(data) values ('serv1_value3');
```

![](files/6.png)


Подключаемся к серверу №2 и вставляем данные

``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5433
insert into test2(data) values ('serv2_value1');
insert into test2(data) values ('serv2_value2');
insert into test2(data) values ('serv2_value3');
```

![](files/7.png)

Тут же, на сервере №2, проверяем подписку на table1

``` text
select * from test1;
```
Находясь на сервере №2 мы видим записи, которые вставлялись на сервере №1

![](files/8.png)


Теперь подключаемся к серверу №1 и проверяем подписку

``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5432
select * from test2;
```

![](files/9.png)


Публикации и подписки работают на обоих серверах.



Настроим сервер №3, создадим подписку и установим copy_data = true и проверим таблицы.



``` text
sudo -u postgres /usr/pgsql-15/bin/psql -p 5434
alter system set wal_level = logical;
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw10_3 -o "-p 5434" restart


CREATE TABLE test1 (id serial PRIMARY KEY, data text);
CREATE TABLE test2 (id serial PRIMARY KEY, data text);

create subscription serv3_test1_subs
connection 'host=127.0.0.1 port=5432 user=postgres dbname=postgres'
publication serv1_test1 with (copy_data = true);

create subscription serv3_test2_subs
connection 'host=127.0.0.1 port=5433 user=postgres dbname=postgres'
publication serv2_test2 with (copy_data = true);
```

![](files/10.png)

Записи с сервера №1 и №2 появились на сервере №3.

