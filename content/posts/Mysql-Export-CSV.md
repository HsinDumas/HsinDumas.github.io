---
title: Mysql Export CSV
date: 2018-09-13 18:08:16
categories:
- MySQL
tags: 
- CSV
---

偷懒的办法

```mysql
mysql> select * from table into outfile '/home/data.csv' character set gbk fields terminated by ',' optionally enclosed by '"' lines terminated by '\n';
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
mysql> show variables like '%secure%';
+------------------+-----------------------+
| Variable_name    | Value                 |
+------------------+-----------------------+
| secure_auth      | ON                    |
| secure_file_priv | /var/lib/mysql-files/ |
+------------------+-----------------------+
2 rows in set (0.01 sec)

mysql> select * from table into outfile '/var/lib/mysql-files/data.csv' character set gbk fields terminated by ',' optionally enclosed by '"' lines terminated by '\n';
Query OK, 346 rows affected (0.20 sec)
```