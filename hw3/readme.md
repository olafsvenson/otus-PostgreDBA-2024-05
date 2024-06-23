# Физический уровень PostgreSQL
1) Создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере
2) Поставьте на нее PostgreSQL 15 через sudo apt
3) Проверьте что кластер запущен через
``` text
sudo -u postgres pg_lsclusters
```
![](files/3.png)

4) зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
``` text
create table test(c1 text);
insert into test values('1');
\q
```
![](files/4.png)
5) Остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop

![](files/5.png)

6) создайте новый диск к ВМ
добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, 
в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)

![](files/6_1.png)
![](files/6_2.png)
![](files/6_3.png)
![](files/6_4.png)

ссылка https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux не открывается
поэтому сделал через gparted

Установка gparted
![](files/6_5.png)

Выбираем нужный диск
![](files/6_6.png)

Cоздаем раздел и форматируем в ext4
![](files/6_7.png)

``` text
sudo lsblk --fs
sudo mkdir /mnt/data
```
![](files/6_9.png)

Редактируем fstab
![](files/6_10.png)

``` text
sudo mount -a
```

7) Cделайте пользователя postgres владельцем /mnt/data
``` text  
chown -R postgres:postgres /mnt/data/
```
![](files/7.png)

8) Перенесите содержимое /var/lib/postgres/15 в /mnt/data

т.к мы перегружали VM после добавления диска - служба postgresql запущена, перед переносом ее нужно остановить
![](files/7_2.png)

теперь переносим папку с данными

``` text  
mv /var/lib/postgresql/15 /mnt/data
```
![](files/8.png)

9) Попытка запустить кластер

Ошибка, сервер использует старый путь, про новый ничего не знает
![](files/8.png)

10) Задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его
напишите что и почему поменяли

нужно отредактировать файл конфигурации postgresql /etc/postgresql/15/main/postgresql.conf

![](files/10.png)

изменив параметр data_directory на новый путь

![](files/10_2.png)

11) попытайтесь запустить кластер, напишите получилось или нет и почему
``` text  
sudo -u postgres pg_ctlcluster 15 main start
```

![](files/11.png)

12) Зайдите через через psql и проверьте содержимое ранее созданной таблицы

![](files/12.png)
