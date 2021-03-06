## 查看binlog状态
```
mysql> show variables like 'log_%';
+----------------------------------------+---------------------+
| Variable_name                          | Value               |
+----------------------------------------+---------------------+
| log_bin                                | OFF                 |
| log_bin_basename                       |                     |
| log_bin_index                          |                     |
| log_bin_trust_function_creators        | OFF                 |
| log_bin_use_v1_row_events              | OFF                 |
| log_error                              | /var/log/mysqld.log |
| log_output                             | FILE                |
| log_queries_not_using_indexes          | OFF                 |
| log_slave_updates                      | OFF                 |
| log_slow_admin_statements              | OFF                 |
| log_slow_slave_statements              | OFF                 |
| log_throttle_queries_not_using_indexes | 0                   |
| log_warnings                           | 1                   |
+----------------------------------------+---------------------+
13 rows in set (0.00 sec)
```



设置 /etc/my.cf后
```
mysql> show variables like 'log_%';
+----------------------------------------+--------------------------------+
| Variable_name                          | Value                          |
+----------------------------------------+--------------------------------+
| log_bin                                | ON                             |
| log_bin_basename                       | /var/lib/mysql/mysql-bin       |
| log_bin_index                          | /var/lib/mysql/mysql-bin.index |
| log_bin_trust_function_creators        | OFF                            |
| log_bin_use_v1_row_events              | OFF                            |
| log_error                              | /var/log/mysqld.log            |
| log_output                             | FILE                           |
| log_queries_not_using_indexes          | OFF                            |
| log_slave_updates                      | OFF                            |
| log_slow_admin_statements              | OFF                            |
| log_slow_slave_statements              | OFF                            |
| log_throttle_queries_not_using_indexes | 0                              |
| log_warnings                           | 1                              |
+----------------------------------------+--------------------------------+
13 rows in set (0.00 sec)

mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      120 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

[root@rabbit1 mysql]# ll
total 176172
-rw-rw---- 1 mysql mysql       56 Jan  8  2017 auto.cnf
drwx------ 2 mysql mysql     4096 Feb 26 17:35 canal_test
drwx------ 2 mysql mysql     4096 Feb 26 17:47 canal_test2
drwx------ 2 mysql mysql     4096 May 19  2017 db_demo
-rw-rw---- 1 mysql mysql 79691776 Feb 26 19:29 ibdata1
-rw-rw---- 1 mysql mysql 50331648 Feb 26 19:29 ib_logfile0
-rw-rw---- 1 mysql mysql 50331648 Jan  8  2017 ib_logfile1
drwx------ 2 mysql mysql     4096 Feb 26 03:16 mysql
-rw-rw---- 1 mysql mysql      120 Feb 26 19:29 mysql-bin.000001
-rw-rw---- 1 mysql mysql       19 Feb 26 19:29 mysql-bin.index
srwxrwxrwx 1 mysql mysql        0 Feb 26 19:29 mysql.sock
drwx------ 2 mysql mysql     4096 Jan  8  2017 performance_schema
drwx------ 2 mysql mysql     4096 Oct 27 10:01 test
```

## 查看日志binlog
```
[root@rabbit1 mysql]# mysqlbinlog mysql-bin.000001 
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#180226 19:29:55 server id 1  end_log_pos 120 CRC32 0xc2bc70ca  Start: binlog v 4, server v 5.6.35-log created 180226 19:29:55 at startup
# Warning: this binlog is either in use or was not closed properly.
ROLLBACK/*!*/;
BINLOG '
s++TWg8BAAAAdAAAAHgAAAABAAQANS42LjM1LWxvZwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAACz75NaEzgNAAgAEgAEBAQEEgAAXAAEGggAAAAICAgCAAAACgoKGRkAAcpw
vMI=
'/*!*/;
# at 120
#180227 12:00:30 server id 1  end_log_pos 603 CRC32 0x04ef1abf  Query   thread_id=11    exec_time=0     error_code=0
use `canal_test`/*!*/;
SET TIMESTAMP=1519704030/*!*/;
SET @@session.pseudo_thread_id=11/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1075838976/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=45,@@session.collation_connection=45,@@session.collation_server=8/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
CREATE TABLE `t_user` (
  `id` int(11) NOT NULL,
  `name` varchar(45) COLLATE utf8_unicode_ci DEFAULT NULL,
  `updatetime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '时间随时更新',
  `createtime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '时间是创建的时间，不随时更新'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci
/*!*/;
DELIMITER ;
# End of log file
ROLLBACK /* added by mysqlbinlog */;
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
[root@rabbit1 mysql]# 
```
或者
```
mysql> show binlog events;
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Log_name         | Pos | Event_type  | Server_id | End_log_pos | Info                                                                                                                                                                                                                                                                                                                                                                                                                            |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| mysql-bin.000001 |   4 | Format_desc |         1 |         120 | Server ver: 5.6.35-log, Binlog ver: 4                                                                                                                                                                                                                                                                                                                                                                                           |
| mysql-bin.000001 | 120 | Query       |         1 |         603 | use `canal_test`; CREATE TABLE `t_user` (
  `id` int(11) NOT NULL,
  `name` varchar(45) COLLATE utf8_unicode_ci DEFAULT NULL,
  `updatetime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '时间随时更新',
  `createtime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '时间是创建的时间，不随时更新'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci                     |
| mysql-bin.000001 | 603 | Stop        |         1 |         626 |                                                                                                                                                                                                                                                                                                                                                                                                                                 |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> 
```

## 分析日志
```
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      603 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

mysql> show binlog events in 'mysql-bin.000001';
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Log_name         | Pos | Event_type  | Server_id | End_log_pos | Info                                                                                                                                                                                                                                                                                                                                                                                                                            |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| mysql-bin.000001 |   4 | Format_desc |         1 |         120 | Server ver: 5.6.35-log, Binlog ver: 4                                                                                                                                                                                                                                                                                                                                                                                           |
| mysql-bin.000001 | 120 | Query       |         1 |         603 | use `canal_test`; CREATE TABLE `t_user` (
  `id` int(11) NOT NULL,
  `name` varchar(45) COLLATE utf8_unicode_ci DEFAULT NULL,
  `updatetime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '时间随时更新',
  `createtime` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '时间是创建的时间，不随时更新'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci                     |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> 
```


## 查看binlogformat
```
mysql> show variables like '%binlog%';
+-----------------------------------------+----------------------+
| Variable_name                           | Value                |
+-----------------------------------------+----------------------+
| binlog_cache_size                       | 32768                |
| binlog_checksum                         | CRC32                |
| binlog_direct_non_transactional_updates | OFF                  |
| binlog_error_action                     | IGNORE_ERROR         |
| binlog_format                           | STATEMENT            |
| binlog_gtid_simple_recovery             | OFF                  |
| binlog_max_flush_queue_time             | 0                    |
| binlog_order_commits                    | ON                   |
| binlog_row_image                        | FULL                 |
| binlog_rows_query_log_events            | OFF                  |
| binlog_stmt_cache_size                  | 32768                |
| binlogging_impossible_mode              | IGNORE_ERROR         |
| innodb_api_enable_binlog                | OFF                  |
| innodb_locks_unsafe_for_binlog          | OFF                  |
| max_binlog_cache_size                   | 18446744073709547520 |
| max_binlog_size                         | 1073741824           |
| max_binlog_stmt_cache_size              | 18446744073709547520 |
| simplified_binlog_gtid_recovery         | OFF                  |
| sync_binlog                             | 0                    |
+-----------------------------------------+----------------------+
19 rows in set (0.00 sec)

mysql> 
```

## 查看MySQL用户权限
```
mysql> select host,user,password from mysql.user;
+-------------------------+--------+-------------------------------------------+
| host                    | user   | password                                  |
+-------------------------+--------+-------------------------------------------+
| localhost               | root   | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| iz2zei0nwllapkwklisoncz | root   |                                           |
| 127.0.0.1               | root   |                                           |
| ::1                     | root   |                                           |
| localhost               |        |                                           |
| iz2zei0nwllapkwklisoncz |        |                                           |
| %                       | root   | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| %                       | mysqld | *83D34C89B8E0F100D54C6D9276D357DB43E8779F |
| %                       | server | *866D5A029D62EC05ACC4584CE50F1CD2F50E0E82 |
+-------------------------+--------+-------------------------------------------+
9 rows in set (0.16 sec)

mysql> CREATE USER canal IDENTIFIED BY 'canal';  
Query OK, 0 rows affected (0.06 sec)

mysql> GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql> -- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> select host,user,password from mysql.user;
+-------------------------+--------+-------------------------------------------+
| host                    | user   | password                                  |
+-------------------------+--------+-------------------------------------------+
| localhost               | root   | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| iz2zei0nwllapkwklisoncz | root   |                                           |
| 127.0.0.1               | root   |                                           |
| ::1                     | root   |                                           |
| localhost               |        |                                           |
| iz2zei0nwllapkwklisoncz |        |                                           |
| %                       | root   | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| %                       | mysqld | *83D34C89B8E0F100D54C6D9276D357DB43E8779F |
| %                       | server | *866D5A029D62EC05ACC4584CE50F1CD2F50E0E82 |
| %                       | canal  | *E3619321C1A937C46A0D8BD1DAC39F93B27D4458 |
+-------------------------+--------+-------------------------------------------+
10 rows in set (0.00 sec)

mysql> 
```
## 查看权限
```
mysql> select * from mysql.user where user='canal' \G
*************************** 1. row ***************************
                  Host: %
                  User: canal
              Password: *E3619321C1A937C46A0D8BD1DAC39F93B27D4458
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: N
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type: 
            ssl_cipher: 
           x509_issuer: 
          x509_subject: 
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: 
      password_expired: N
*************************** 2. row ***************************
                  Host: 127.0.0.1
                  User: canal
              Password: *E3619321C1A937C46A0D8BD1DAC39F93B27D4458
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: Y
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type: 
            ssl_cipher: 
           x509_issuer: 
          x509_subject: 
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: 
      password_expired: N
*************************** 3. row ***************************
                  Host: localhost
                  User: canal
              Password: *E3619321C1A937C46A0D8BD1DAC39F93B27D4458
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: Y
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type: 
            ssl_cipher: 
           x509_issuer: 
          x509_subject: 
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: 
      password_expired: N

```
