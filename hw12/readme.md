### Работа с join'ами, статистикой.

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 start
sudo -u postgres /usr/pgsql-15/bin/psql
wget https://edu.postgrespro.ru/demo_small.zip && sudo apt install unzip && unzip demo_small.zip && sudo -u postgres psql -d postgres -f /tmp/demo_small.sql
```

1. Реализовать прямое соединение двух или более таблиц

``` text
	select t.*
	from bookings.tickets t
	inner join bookings.bookings b on t.book_ref = t.book_ref
	inner join bookings.ticket_flights f on t.ticket_no = f.ticket_no
```	
2. Реализовать левостороннее (или правостороннее)соединение двух или более таблиц
3. Реализовать кросс соединение двух или более таблиц
4. Реализовать полное соединение двух или более таблиц
5. Реализовать запрос, в котором будут использованы разные типы соединений
6. Сделать комментарии на каждый запрос
7. К работе приложить структуру таблиц, для которых выполнялись соединения