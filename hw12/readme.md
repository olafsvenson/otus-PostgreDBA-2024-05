### ������ � join'���, �����������.

``` text
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 initdb
sudo -u postgres /usr/pgsql-15/bin/pg_ctl -D /var/lib/pgsql/15/hw12 start
sudo -u postgres /usr/pgsql-15/bin/psql
wget https://edu.postgrespro.ru/demo_small.zip && sudo apt install unzip && unzip demo_small.zip && sudo -u postgres psql -d postgres -f /tmp/demo_small.sql
```

1. ����������� ������ ���������� ���� ��� ����� ������

``` text
	select t.*
	from bookings.tickets t
	inner join bookings.bookings b on t.book_ref = t.book_ref
	inner join bookings.ticket_flights f on t.ticket_no = f.ticket_no
```	
2. ����������� ������������� (��� ��������������)���������� ���� ��� ����� ������
3. ����������� ����� ���������� ���� ��� ����� ������
4. ����������� ������ ���������� ���� ��� ����� ������
5. ����������� ������, � ������� ����� ������������ ������ ���� ����������
6. ������� ����������� �� ������ ������
7. � ������ ��������� ��������� ������, ��� ������� ����������� ����������