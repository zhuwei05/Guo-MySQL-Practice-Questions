# MySql常用命令总结

- 1、使用SHOW语句找出在服务器上当前存在什么数据库：<br>
mysql> SHOW DATABASES;
- 2、创建一个数据库MYSQLDATA<br>
mysql> CREATE DATABASE MYSQLDATA;
- 3、选择你所创建的数据库<br>
mysql> USE MYSQLDATA; (按回车键出现Database changed 时说明操作成功！)
- 4、查看现在的数据库中存在什么表<br>
mysql> SHOW TABLES;
- 5、创建一个数据库表<br>
mysql> CREATE TABLE MYTABLE (name VARCHAR(20), sex CHAR(1));
- 6、显示表的结构：<br>
mysql> DESCRIBE MYTABLE;
- 7、往表中加入记录<br>
mysql> insert into MYTABLE values (”hyq”,”M”);
- 8、用文本方式将数据装入数据库表中（例如D:/mysql.txt）<br>
mysql> LOAD DATA LOCAL INFILE “D:/mysql.txt” INTO TABLE MYTABLE;
- 9、导入.sql文件命令（例如D:/mysql.sql）<br>
mysql>use database;
mysql>source d:/mysql.sql;
- 10、删除表<br>
mysql>drop TABLE MYTABLE;
- 11、清空表<br>
mysql>delete from MYTABLE;
- 12、更新表中数据<br>
mysql>update MYTABLE set sex=”f” where name=’hyq’;

匿名帐户删除、 root帐户设置密码:
```
use mysql;
delete from User where User=”";
update User set Password=PASSWORD(’newpassword’) where User=’root’;
```
GRANT的常用用法如下：
```
grant all on mydb.* to NewUserName@HostName identified by “password” ;
grant usage on *.* to NewUserName@HostName identified by “password”;
grant select,insert,update on mydb.* to NewUserName@HostName identified by “password”;
grant update,delete on mydb.TestTable to NewUserName@HostName identified by “password”;
```


#### 全局管理权限：
- FILE: 在MySQL服务器上读写文件。
- PROCESS: 显示或杀死属于其它用户的服务线程。
- RELOAD: 重载访问控制表，刷新日志等。
- SHUTDOWN: 关闭MySQL服务。

#### 数据库/数据表/数据列权限：
- ALTER: 修改已存在的数据表(例如增加/删除列)和索引。
- CREATE: 建立新的数据库或数据表。
- DELETE: 删除表的记录。
- DROP: 删除数据表或数据库。
- INDEX: 建立或删除索引。
- INSERT: 增加表的记录。
- SELECT: 显示/搜索表的记录。
- UPDATE: 修改表中已存在的记录。

#### 特别的权限：
- ALL: 允许做任何事(和root一样)。
- USAGE: 只允许登录–其它什么也不允许做。
