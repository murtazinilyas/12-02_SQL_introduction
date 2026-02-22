# Домашнее задание к занятию «Работа с данными (DDL/DML)»

### Задание 1
1.1. Поднимите чистый инстанс MySQL версии 8.0+. Можно использовать локальный сервер или контейнер Docker.

1.2. Создайте учётную запись sys_temp. 

1.3. Выполните запрос на получение списка пользователей в базе данных. (скриншот)

1.4. Дайте все права для пользователя sys_temp. 

1.5. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

1.6. Переподключитесь к базе данных от имени sys_temp.

Для смены типа аутентификации с sha2 используйте запрос: 
```sql
ALTER USER 'sys_test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
1.6. По ссылке https://downloads.mysql.com/docs/sakila-db.zip скачайте дамп базы данных.

1.7. Восстановите дамп в базу данных.

1.8. При работе в IDE сформируйте ER-диаграмму получившейся базы данных. При работе в командной строке используйте команду для получения всех таблиц базы данных. (скриншот)

*Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.*

### Решение 1

Поднял инстанс MySQL через Docker, подключился к нему через DBeaver и WSL.

Создал пользователя sys_temp, выполнил запрос на получение списка пользователей:
![Список пользователей]()

Дал все права пользователю sys_temp, выполнил запрос на получение списка прав для него:
![Список прав пользователя sys_temp]()

Скачал дамп базы данных sakila, создал базу данных с тем же именем на своем инстансе и восстановил в нее дамп.

ER-диаграмма в DBeaver:

![ER-диаграмма в DBeaver]()

Список всех таблиц в получившейся БД в консоли:

![Список таблиц базы данных sakila в консоли]()

Простыня запросов:

```SQL
docker run --name test_db -p 3306:3306 -e MYSQL_ROOT_PASSWORD=secret -d mysql:latest
docker exec -it test_db mysql -u root -p
CREATE USER 'sys_temp'@'localhost' IDENTIFIED BY '123456';
SELECT user, host FROM mysql.user;
GRANT ALL PRIVILEGES ON *.* TO 'sys_temp'@'localhost';
SHOW GRANTS FOR 'sys_temp'@'localhost';
CREATE DATABASE sakila;
docker exec -i test_db mysql -u sys_temp -p sakila < sakila-schema.sql
docker exec -i test_db mysql -u sys_temp -p sakila < sakila-data.sql
USE sakila
SHOW TABLES;
```

### Задание 2
Составьте таблицу, используя любой текстовый редактор или Excel, в которой должно быть два столбца: в первом должны быть названия таблиц восстановленной базы, во втором названия первичных ключей этих таблиц. Пример: (скриншот/текст)
```
Название таблицы | Название первичного ключа
customer         | customer_id
```

### Решение 2

Написал скрипт для составления таблицы с названиями таблиц из восстановленной базы и названиями первичных ключей этих таблиц:

```SQL
DROP TABLE IF EXISTS `zadacha2`;
CREATE TABLE zadacha2 (
	название_таблицы VARCHAR(255),
	название_первичного_ключа VARCHAR(255)
);

INSERT INTO zadacha2 (название_таблицы, название_первичного_ключа)
SELECT 
    TABLE_NAME, 
    COLUMN_NAME
FROM 
    INFORMATION_SCHEMA.KEY_COLUMN_USAGE
WHERE 
    TABLE_SCHEMA = 'sakila'
    AND CONSTRAINT_NAME = 'PRIMARY';
```

![Получившаяся таблица]()

### Задание 3*
3.1. Уберите у пользователя sys_temp права на внесение, изменение и удаление данных из базы sakila.

3.2. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

*Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.*

### Решение 3

Сначала отозвал у пользователя все права на все базы.
Потом выдал права на просмотр, внесение, изменение и удаление данных из базы sakila.
Далее забрал права на внесение, изменение и удаление данных из базы sakila.
Выполнил запрос на получение списка прав:

![Список прав пользователя sys_temp]()

Простыня запросов:
```SQL
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'sys_temp'@'localhost';
GRANT INSERT, UPDATE, DELETE, SELECT ON sakila.* TO 'sys_temp'@'localhost';
REVOKE INSERT, UPDATE, DELETE ON sakila.* FROM 'sys_temp'@'localhost';
SHOW GRANTS FOR 'sys_temp'@'localhost';
```
