# postgress

на каждого клиента отдельный процесс postgress backend (pstree -p <number>)
PGpoll-2, PgBouncer - для кэширования соединений

### порядок обработки запроса:

Парсер-Аналайзер-Реврайтер-Планер-Исполнитель (explain analize)
статистика - таблицы+атрибуты (намного больше чем в mysql)+межатрибутная статистика
статистика нужна для оптимизации запросов
оптимизатор умеет хорошо оптимизировать сложные клмплексные запросы

### MVCC

- cтарая запись не удаляется а остается с областью видимости для транзанкций от xmin до xmax
- xmin - номер транзанкци создавшей запись
- xmax - номер транзакции удалившей запись (обновившей)
- autovacuum - отдельный процесс который очищает муссор - требует ресурсы для очистки

### Индексы

- составные(несколько атрибутов)  
`create index idx_users_age_gender ON people(age, gender);`
- частичные(с заданным условием (age>25 лет)),
`create index idx_users_age_gender ON people(age) where age >= 25;`
- функциональные (преобразование изначальных значений)
`create index idx_users_age_gender ON people(LOWER(name)) where age >= 25;`

## postgres commands

```bash
explain (analyze, buffers) #explain request

select numbercol
from numbers n 
where numbercol = 2500;

su postgres; initdb -D /usr/local/pgsql/data #инициализация кластера БД (pg_ctl -D /usr/local/pgsql/data initdb)

#default data folder
/usr/local/pgsql/data

pg_hba.conf     #настройки подключения/аутентификации postgre

pg_ident.conf   #соответствие между именами пользователей операционной системы и именами ролей базы данных PostgreSQL

postgres --version
su - postgres; psql [database_name] [user_name]
pg_basebackup -R -h <server> - для репликации
pg_dumpall/pg_dump

\list               #список баз
\du                 #список всех пользователей
create database
create user
\password author    #задать пароль для пользователя
create user author with login password 'qwerty';
drop database
drop user
```

Данные в таблицах не отсортированы по первичному ключу	

# Mysql

Один процесс, но отдельный потоки для каждого клиента  
сам может кэшировать соединения  
Много разных движков хранения
Данные в таблицах отсортированы по первичному ключу	

```bash
show processlist\G
select connection_id()\G
show global status like 'threads_running'\G
```

порядок обработки запроса:  
Парсер-препроцессор-Оптимизатор запросов-Исполнитель (explain analize)  
статистика - таблицы+атрибуты

## MVCC
- UNDO log, в котором сохраняется инструкция по откату транзанкции
- На диске всегда база сразу с изменениями (DB_TRX_ID - транзанкция изменившая запись)
- на хранение данных для восстановления тратит меньше памяти.

### Индексы

- B-tree (+ hash index only for internal db use)
- есть хинты чтобы прямо указать какой индекс использовать

## MySQL commands

```bash
/etc/mysql/my.cnf
show full processlist # показывает все исполняемые транзанкции
explain select * from wp_posts
UPDATE mysql.user SET ssl_type=’ANY’ WHERE user=” # разрешить только SSL подключения для пользователя

#Обновить пароль пользователя root
mysql -u root -p
use mysql;
update user set password=NEWPASSWORD where user='root';
flush privileges;
```
