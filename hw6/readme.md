### Журналы

1. Настройте выполнение контрольной точки раз в 30 секунд.

``` text
sudo -u postgres nano /var/lib/pgsql/15/hw6/postgresql.conf
	
checkpoint_timeout = 30s
```
![](files/1.png)

 Делаем рестарт
 
 ``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw6 restart
```

2. 10 минут c помощью утилиты pgbench подавайте нагрузку.

Инициализируем
``` text
sudo -u postgres /usr/pgsql-15/bin/pgbench -i -U postgres postgres
```
![](files/2_1.png)

Запускаем 

sudo -u postgres /usr/pgsql-15/bin/pgbench -c8 -P 60 -T 600 -U postgres postgres

![](files/2_2.png)

3. Измерьте, какой объем журнальных файлов был сгенерирован за это время. Оцените, какой объем приходится в среднем на одну контрольную точку.

``` text
sudo -u postgres ls -lh /var/lib/pgsql/15/hw6/pg_wal/
```
Было сгенерировано 6 файлов по 16mb = 96mb

![](files/3.png)

4. Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. Почему так произошло?

``` text
SELECT * FROM pg_stat_bgwriter \gx
```
![](files/4.png)

41 точка сгенерировалась по расписанию, 1 по требованию

5. Сравните tps в синхронном/асинхронном режиме утилитой pgbench. Объясните полученный результат.
   По умолчанию синхронный режим включен

   ![](files/5_1.png)

Запускаем pgbench

``` text
sudo -u postgres /usr/pgsql-15/bin/pgbench -c8 -P 10 -T 60 -U postgres postgres
```

![](files/5_2.png)

выключаем синхронный режим, перезапускаем сервер, запускаем pgbench

![](files/5_3.png)


В асинхронном режиме из-за отсутствия журналирования уменьшилось latency и увеличилось tps.
   
6. Создайте новый кластер с включенной контрольной суммой страниц. Создайте таблицу. Вставьте несколько значений. 
Выключите кластер. Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. Что и почему произошло? как проигнорировать ошибку и продолжить работу?

Останавливаем сервер

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw6 stop
```

Включаем checksums

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_checksums --enable -D /var/lib/pgsql/15/hw6
```

![](files/6_1.png)

Включаем сервер.

Создаем и заполняем таблицу

``` text
create table t1(c1 integer);
insert into t1 values(1),(2),(3);
```

![](files/6_2.png)

Узнаем где файл таблицы
``` text 
SELECT pg_relation_filepath('t1');
```

![](files/6_3.png)

Остановим сервер, отредактируем файл, запустим сервер.

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw6 stop
sudo -u postgres nano /var/lib/pgsql/15/hw6/base/5/16414
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw6 start
```

Попробуем получить данные из таблицы

``` text
select * from t1;
```
![](files/6_4.png)

Получаем ошибку crc


Чтобы игнорировать нужно установить ignore_checksum_failure = on.

``` text
set ignore_checksum_failure = on;
select * from t1;
```

![](files/6_5.png)
