### Резервное копирование и восстановление 
1. Создаем ВМ/докер c ПГ.

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw9 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw9 start
sudo -u postgres /usr/pgsql-15/bin/psql
```

2. Создаем БД, схему и в ней таблицу.

``` text
create database hw9;
\c hw9
create schema otus;
```

3. Заполним таблицы автосгенерированными 100 записями.

``` text
CREATE TABLE otus.otus_test  AS 
SELECT * FROM GENERATE_SERIES(1, 100) AS id;
```

![](files/1.png)
