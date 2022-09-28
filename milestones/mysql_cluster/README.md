## Установка кластера MySQL

Необходимо разработать Ansible роль для установки кластера MySQL.

Рекомендации:
  - Имена серверов: `db01.you.domain` и `db02.you.domain`
  - Характеристики: 4vCPU, 4 RAM, Internal address.

Цель:

1. Получить отказоустойчивый кластер баз данных MySQL.

Ожидаемые результаты:

1. MySQL работает в режиме репликации Master/Slave.
2. В кластере автоматически создаётся база данных c именем `wordpress`.
3. В кластере автоматически создаётся пользователь `wordpress` с полными правами на базу `wordpress` и паролем `wordpress`.

**Вы должны понимать, что в рамках обучения это допустимые значения, но в боевой среде использование подобных значений не приемлимо! Считается хорошей практикой использовать логины и пароли повышенного уровня сложности. В которых будут содержаться буквы верхнего и нижнего регистров, цифры, а также специальные символы!**

## Решение

Роль находится [здесь](https://github.com/Frolls/mysql-cluster-role)

1. MySQL работает в режиме репликации Master/Slave.

Проверим, что работает.

Состояние master:

```
mysql> show master status\G
*************************** 1. row ***************************
             File: mysql-bin.000001
         Position: 1517
     Binlog_Do_DB: wordpress
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)
```

Состояние slave:

```
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.20.20
                  Master_User: frolls
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 1517
               Relay_Log_File: relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
...
```

2. В кластере автоматически создаётся база данных c именем `wordpress`

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpress          |
+--------------------+
5 rows in set (0.00 sec)
```

3. В кластере автоматически создаётся пользователь `wordpress` с полными правами на базу `wordpress` и паролем `wordpress`

```
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select user from user;
+------------------+
| user             |
+------------------+
| frolls           |
| wordpress        |
| debian-sys-maint |
| mysql.session    |
| mysql.sys        |
| root             |
+------------------+
6 rows in set (0.00 sec)

mysql> show grants for wordpress;
+----------------------------------------------------------------------------+
| Grants for wordpress@%                                                     |
+----------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'wordpress'@'%'                                      |
| GRANT ALL PRIVILEGES ON `wordpress`.* TO 'wordpress'@'%' WITH GRANT OPTION |
+----------------------------------------------------------------------------+
2 rows in set (0.00 sec)
```