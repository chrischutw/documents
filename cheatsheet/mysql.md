# MYSQL command cheatsheet

## list all users

```shell
SELECT User, Host FROM mysql.user;
```

## grant all priviliged

```shell


```

## Slave super read only

ref: <https://bugs.mysql.com/bug.php?id=84081>

```shell
set global super_read_only=ON;

mysql> show global variables like '%read_only';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_read_only      | OFF   |
| read_only             | ON    |
| super_read_only       | ON    |
| transaction_read_only | OFF   |
| tx_read_only          | OFF   |
+-----------------------+-------+
5 rows in set (0.01 sec)
# my.cnf
super-read-only=ON
```

## Fix Last_SQL_Error

ref: <https://dev.mysql.com/doc/refman/5.7/en/set-global-sql-slave-skip-counter.html>

```shell
# Stop the slave SQL thread
STOP SLAVE;
# Skip the problematic transaction
SET GLOBAL SQL_SLAVE_SKIP_COUNTER = 1;
# Restart the slave SQL thread
START SLAVE;
```

## Check master/slave status

```shell
SHOW MASTER STATUS\G;
SHOW SLAVE STATUS\G;
```

## Count table size

### replace database_name

```shell
SELECT table_name AS "Table", ROUND(((data_length + index_length) / 1024 / 1024 / 1024), 2) AS "Size (GB)" FROM information_schema.TABLES WHERE table_schema =$table ORDER BY (data_length + index_length) DESC;
```

## Alter table name

```shell
alter table `old_table_name`
rename to `new_table_name`

ALTER TABLE `feed_pools` CHANGE `id` `id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT;
```

## Show connections

```shell
show current connection
show variables like 'Threads_connected';
show max connections
show variables like 'max_connection';
```

## Show Columns

```shell
show columns from `table`;
```

## Create table

```shell
create table feed_pools_bak
(
id bigint(20) unsigned auto_increment not null, 
member_uniqid char(18) not null, 
subject tinyint(4) unsigned not null, 
feed_id int(10) unsigned not null, 
primary key(id), 
index(member_uniqid), 
index(feed_id)
) 
ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci 
AUTO_INCREMENT= 4294967296;
```

## copy data

```shell
insert into feed_pools select * from feed_pools_bak
```

## show user

```shell
use mysql ; select user, host from user where user='';
```

## WSREP has not yet prepared node for application use

ref <https://galeracluster.com/library/documentation/quorum-reset.html>

```shell
SET GLOBAL wsrep_provider_options='pc.bootstrap=YES';
```

## Create user on master node

```shell
show grants for 'pixrepl'@'%';
create user 'pixrepl'@'%' identified by 'password';
revoke all privileges, grant option from 'pixrepl'@'%';
grant replication slave on *.* to 'pixrepl'@'%';
flush privileges;
show grants for 'pixrepl'@'%';
```

## Change master user on slave node

```shell
stop slave; 
change master to master_user='pixrepl', master_password='password';
start slave;
```

## Drop old replication user on master node

```shell
drop user 'repl'@'%'; 
```

## Update user

```shell
UPDATE mysql.user SET Host='%' WHERE Host='localhost' AND User='username';
```

## max_connections

```shell
show variables like 'max_connections';
SET GLOBAL max_connections = 150;
```

## show processlist

```shell
show processlist
```

## Process

```shell
use mysql ; select user, host from user;

create user 'pixrepl'@'%' identified by 'password'; grant replication slave on *.* to 'pixrepl'@'%'; flush privileges;

stop slave; change master to master_user='pixrepl', master_password='password'; start slave;

drop user 'repl'@'%'; flush privileges;
```

## Date

ref: <https://popsql.com/learn-sql/mysql/how-to-query-date-and-time-in-mysql>

```shell
SELECT * FROM events where event_date between '2018-01-01' and '2018-01-31';
SELECT * FROM events where event_date `< or >` '2018-01-01' ;
```

## DB tuning path

```shell
/etc/sysctl.d
/etc/security/limit.d
```

## Calculate DB size

```shell
SELECT table_schema "Database", SUM(data_length + index_length) / 1024 / 1024 "Size (MB)" FROM information_schema.tables GROUP BY table_schema;
```

## Calculate table size

```shell
SELECT table_name AS "Table", round(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)" FROM information_schema.tables WHERE table_schema = `Database`;
```

## mysqldump

ref: <https://code.yidas.com/mysqldump/>

```shell
# dump all databases
mysqldump -u root -p --all-database > all_database.sql

# dump multiple databases
mysqldump -u root -p --databases db1 db2 db3 > backup.sql

# dump single database
mysqldump -u root -p --databases db > db.sql

# dump with 1356 error
mysqldump -u root --force -p --databases db > db.sql
```

## mysql restore

ref: <https://ubiq.co/database-blog/mysql-copy-database/>

```shell
mysql -p `database` < database.sql
```

## Previleges

ref: <https://www.prisma.io/dataguide/mysql/authentication-and-authorization/privilege-management>

```shell
# show grants from specific user
SHOW GRANTS for `user`;

# grant previliges
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, REFERENCES, ALTER ON `database`.* TO 'user'@'%'

# revoke previliges
REVOKE SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, REFERENCES, ALTER ON `database`.* FROM 'user'@'%'

```
