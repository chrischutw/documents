# PostgreSQL cheatsheet

ref: <https://andyyou.github.io/2015/04/06/psql-notes/>
ref: <https://www.geeksforgeeks.org/postgresql-psql-commands/?ref=lbp>

## connection

```shell
psql -h hostname -U $User
```

## list database

```shell
\l```
## create database
```shell
CREATE DATABASE $DB;```

## create user
```shell
create user $User with encrypted password '$password';
```

## grant privileges

```shell
grant all privileges on database $DB to $User;
```

## Alter database owner

```shell
alter database $DB owner to $User;
```

## list table

```shell
\dt 
```

## list table in schema

```shell
\dt ${schema}.*
```

## update

```shell
update queries set is_archived='f' where id>103 and id<108;
```

## select

```shell
select is_archived from queries where id>103 and id<108;
```

## output

```shell
\copy (SELECT * FROM queries limit 1) to '/var/lib/postgresql/data/queries.csv' with csv
```

## dump

```shell
pg_dump --verbose --format=c --dbname=$db_name -f backup_files
```

## restore

```shell
pg_restore -h localhost -p 5432 -U $user -v -e -d $DB $backupfile
```

## pg_basebackup

```shell
pg_basebackup -l "base backup" -D $backupfolder -h 10.20.11.208 -U $user -c fast -X stream --no-slot --verbose
```

## pg_verifybackup

```shell
pg_verifybackup /Users/chris/Downloads/backup
```

## count table number from a schema

```shell
select count(*) from information_schema.tables where table_schema = '$schema';
```

## table or column with camel case

ref:<https://stackoverflow.com/questions/63095416/how-to-fix-sql-executions-error-in-postgresql-due-to-camel-case-names-of-table-a>

## timezone

- show timezone

```shell
show timezone;
```

- show all timezone

```shell
select * from pg_timezone_names;
```

- set timezone

```shell
alter database ${DB} set timezone TO 'ROC';
```

## connection with password

```shell
psql postgresql://${USER}:${PASS}@${HOST}:5432/${DB_NAME}
psql postgresql://${USER}:${PASS}@${HOST}:5432/${DB_NAME} -c "select count(*) from information_schema.tables where table_schema = '"${DB_NAME}"';"
```

## Connetion environment variables

```shell
- PGPASSWORD=284ck6a83
- PGUSER=postgres
- PGHOST=cdp-db.tenmax.io
- PGPORT=5432
```

## Docker-compose.yml

```shell
version: '3.8'
services:
  postgres:
    container_name: postgres
    image: postgres:9.5
    environment:
      - POSTGRES_PASSWORD=test1234
      - PGUSER=postgres
      - PGHOST=localhost
      - PGPORT=5432

    volumes:
      - ./data:/var/lib/postgresql/data
    ports:
      - 5432:5432
```
