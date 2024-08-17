### Работа с join'ами, статистикой.

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 start
sudo -u postgres /usr/pgsql-15/bin/psql
wget https://edu.postgrespro.ru/demo_small.zip && sudo apt install unzip && unzip demo_small.zip && sudo -u postgres psql -d postgres -f /tmp/demo_small.sql
```

1. Реализовать прямое соединение двух или более таблиц

Номера билетов для определенного рейса
``` text

select t.ticket_no, f.flight_no
from bookings.tickets t
inner join bookings.ticket_flights tf on t.ticket_no = tf.ticket_no
inner join bookings.flights f on tf.flight_id = f.flight_id
where 
	f.flight_no ='PG0242'
```	
2. Реализовать левостороннее (или правостороннее)соединение двух или более таблиц

Брони, где нет посадочных талонов

``` text
select
	b.book_ref,
	t.ticket_no,
	boarding_no
from
	bookings.bookings b
join bookings.tickets t on
	t.book_ref = b.book_ref
left join bookings.boarding_passes bp on
	bp.ticket_no = t.ticket_no
where
	boarding_no is null
```

3. Реализовать кросс соединение двух или более таблиц

Все номера мест для определенного самолета

``` text
select a.model, s.seat_no
from bookings.aircrafts a
cross join bookings.seats s 
where a.model='Boeing 777-300'
order by s.seat_no
```

4. Реализовать полное соединение двух или более таблиц

Пассажиры бизнесс класса

``` text
select t.passenger_id, tf.fare_conditions
from bookings.ticket_flights tf
full join bookings.tickets t
	on tf.ticket_no = t.ticket_no
where tf.fare_conditions='Business'
```


