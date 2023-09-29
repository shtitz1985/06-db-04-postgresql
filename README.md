# `Домашнее задание к занятию 4. «PostgreSQL» - Зозуля Максим`

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL, используя psql.

```
docker run -d --name pg_docker \
-e POSTGRES_PASSWORD=postgres \
-p 5432:5432 \
-v $HOME/docker/volumes/postgres/data:/var/lib/postgresql/data \
-v $HOME/docker/volumes/postgres/bckp:/var/lib/postgresql/bckp \
postgres:13

docker exec -it pg_docker psql -U postgres

```
Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

---
**Найдите и приведите** управляющие команды для:

- вывода списка БД, `\l`
- подключения к БД, `\c <db_name>`
- вывода списка таблиц, `\dt`
- вывода описания содержимого таблиц, `\d+ <table_name>`
- выхода из psql. `\q`

## Задача 2

Используя `psql`, создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления, и полученный результат.

[IMAGE](1.PNG)

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили
провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.

[IMAGE](2.PNG)

Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders?

---
Если известно заранее, что данные будут равномерно распределены по какому-то полю, то можно использовать шардирование при проектировании. Однако, в реальных сценариях, где распределение данных может быть непредсказуемым или изменчивым, важно предусмотреть механизмы балансировки данных и масштабирования для управления шардами и обеспечения производительности.

---
## Задача 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.

[IMAGE](3.PNG)

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

Можно прямо в создание partitions добавить директиву UNIQUE напротив объявления полей таблицы:

```
CREATE TABLE public.orders_price_lte_499 (
    id integer DEFAULT nextval('public.orders_new_id_seq'::regclass) NOT NULL,
    title character varying(80) NOT NULL UNIQUE,
    price integer DEFAULT 0
);

CREATE TABLE public.orders_price_gt_499 (
    id integer DEFAULT nextval('public.orders_new_id_seq'::regclass) NOT NULL,
    title character varying(80) NOT NULL UNIQUE,
    price integer DEFAULT 0
);
```
После восстановления такого дампа `title` станет уникальным полем. 

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

