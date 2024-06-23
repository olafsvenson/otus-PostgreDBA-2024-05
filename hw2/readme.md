# Установка PostgreSQL 
1) Cоздать ВМ с Ubuntu 20.04/22.04 или развернуть докер любым удобным способом.
2) Поставить на нем Docker Engine
3) Сделать каталог /var/lib/postgres
``` text
sudo mkdir /var/lib/postgres
```
![](files/3.png)

4) Развернуть контейнер с PostgreSQL 15 смонтировав в него /var/lib/postgresql

``` text
docker pull postgres:15
```
![](files/4.png)

Создаем docker-сеть:
``` text
sudo docker network create pg-netw
```

![](files/4_1.png)

Подключаем созданную сеть к контейнеру сервера Postgres и запускаем его:
![](files/4_2.png)

5) Развернуть контейнер с клиентом postgres
Запускаем отдельный контейнер с клиентом в общей сети с БД:
``` text
sudo docker run -it --rm --network pg-netw --name pg-client postgres:15 psql -h pg-server -U postgres
```
 ![](files/6_1.png)

6) Подключится из контейнера с клиентом к контейнеру с сервером и сделать таблицу с парой строк
``` text
CREATE DATABASE otus_test; 
create table test (t text);
insert into test(t) values ('one'),('two'),('three');
```

![](files/6_2.png)


7. Подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера

 Тут я долго мучался и не мог понять как пробросить порт из VM в хост. Поменял тип подключения на Bridge и база в контейнера pg-server стала доступна.
 Меняю св-во адаптера
 
 ![](files/7_2.png)

Узнаю IP VM с Docker

![](files/7_3.png)

В pgAdmin на хосте указываю адрес с VM Docker
 ![](files/7_4.png)

 Select к ранее созданной таблице.
 
 ![](files/7_5.png)
 
8. Удалить контейнер с сервером.
   Создать его заново.
   Подключится снова из контейнера с клиентом к контейнеру с сервером.
   Проверить, что данные остались на месте.

    ![](files/8.png)
