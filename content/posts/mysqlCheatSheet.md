---
title: "MysqlCheatSheet"
date: 2018-02-08T22:47:02-05:00
draft: true
---

Using cloud 9 built in MySQL server.

Some common `cloud 9 only` command line:

```shell
# To start MySQL server
mysql-ctl start
# Main command line interface
mysql-ctl cli
# Quit mySQL CLI
exit;
quit;
\q;
ctrl-c
# To stop MySQL server
mysql-ctl stop
```

`show databases;` list existing databases

Common interacting with Database:

1. Start MySQL CLI: `mysql-ctl cli;`

2. List available databases: `show databases;`

3. Create a database: `CREATE DATABASE database_name;`

4. `show databases` to check if it is created successfully.

5. To delete a database: `DROP DATABASE database_name;`

6. Pick up (or switch to) a database to work with it: `USE database_name;`

   ​	`SELECT database();` display current using (working with) database. It will show NULL if use database dropped. 

7. Creating Table

   ```mysql
   CREATE TABLE tablename
   (
       column_name data_type,
       column_name data_type
   );
   ```

 	8. `SHOW TABLES;` shows current tables in current database.
	9. `SHOW COLUMNS FROM tablename;` displays a nice detail information of  a table, or alternatively, `DESC tablename;`.
	10. To delete a table: `DROP TABLE tablename;`.