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

# MySQL-Practice-Questions

## 1、取得每个部门最高薪水的人员名称
- 第一步：取得每个部门最高薪水『按照部门分组求最大值』<br>
``
mysql> select deptno,max(sal) as maxsal from emp group by deptno;
``

| deptno | maxsal  |
| :--------:|:--------:|
|     10 | 5000.00 |
|     20 | 3000.00 |
|     30 | 2850.00 |
- 第二步：将上面的查询结果当作临时表t，t表和emp e表进行连接<br>
条件：e.deptno=t.deptno and e.sal=t.sal

```
mysql> select
    ->   e.ename t.*
    -> from
    ->   emp e
    -> join
    ->  (select deptno,max(sal) as maxsal from emp group by deptno) t
    -> on
    ->  e.deptno=t.deptno and e.sal = t.maxsal;
```

| ename | deptno | maxsal  |
| :--------:|:--------:|
| BLAKE |     30 | 2850.00 |
| SCOTT |     20 | 3000.00 |
| KING  |     10 | 5000.00 |
| FORD  |     20 | 3000.00 |

# 2、那些人的薪水在部门的平均薪水之上
第一步：找出部门的平均薪水『按部门编号分组求平均薪水』<br>
``
select deptno,avg(sal) as avgsal from emp group by deptno;
``

| deptno | avgsal      |
| :--------:|:--------:|
|     10 | 2916.666667 |
|     20 | 2175.000000 |
|     30 | 1566.666667 |
第二步：将上面的查询结果当作临时表t，与emp e表进行连接<br>
条件：t.deptno=t.deptno and e.sal > t.avgsal
```
select
  e.ename,e.sal,t.*
from  
  emp e
join
  (select deptno,avg(sal) as avgsal from emp group by deptno) t
on
  e.deptno=t.deptno and e.sal > t.avgsal;
```

| ename | sal     | deptno | avgsal      |
| :--------:|:--------:| :--------:|:--------:|
| ALLEN | 1600.00 |     30 | 1566.666667 |
| JONES | 2975.00 |     20 | 2175.000000 |
| BLAKE | 2850.00 |     30 | 1566.666667 |
| SCOTT | 3000.00 |     20 | 2175.000000 |
| KING  | 5000.00 |     10 | 2916.666667 |
| FORD  | 3000.00 |     20 | 2175.000000 |

# 3、1取得部门中(所有人)平均薪水的等级
第一步：取得部门中的平均薪水<br>
``
select deptno,avg(sal) as avgsal from emp group by deptno;
``

| deptno | avgsal      |
| :--------:|:--------:|
|     10 | 2916.666667 |
|     20 | 2175.000000 |
|     30 | 1566.666667 |
第二部：将上面的查询结果当作临时表t，t表和salgrade s表进行关联
条件：e.sal between s.losal and s.hisal
```
select
  t.*,s.grade
from
  salgrade s
join
  (select deptno,avg(sal) as avgsal from emp group by deptno) t
on
  t.avgsal between s.losal and s.hisal;
```
| deptno | avgsal      | grade |
| :--------:|:--------:|:--------:|
|     10 | 2916.666667 |     4 |
|     20 | 2175.000000 |     4 |
|     30 | 1566.666667 |     3 |

# 3、2取得部门中(所有人)薪水的平均等级
第一步：每个员工的薪水等级(oder by 以部门编号排序，为了好理解)

```
select
  e.ename,e.sal,e.deptno,s.grade
from
  emp e
join
  salgrade s
on
  e.sal between s.losal and s.hisal ;
```
| ename  | sal     | deptno | grade |
| :--------:|:--------:|:--------:|
| MILLER | 1300.00 |     10 |     2 |
| KING   | 5000.00 |     10 |     5 |
| CLARK  | 2450.00 |     10 |     4 |
| ADAMS  | 1100.00 |     20 |     1 |
| SCOTT  | 3000.00 |     20 |     4 |
| FORD   | 3000.00 |     20 |     4 |
| JONES  | 2975.00 |     20 |     4 |
| SMITH  |  800.00 |     20 |     1 |
| MARTIN | 1250.00 |     30 |     2 |
| ALLEN  | 1600.00 |     30 |     3 |
| JAMES  |  950.00 |     30 |     1 |
| BLAKE  | 2850.00 |     30 |     4 |
| WARD   | 1250.00 |     30 |     2 |
| TURNER | 1500.00 |     30 |     3 |

第二步：在以上基础上继续以部门编号分组，求平均薪水等级
```
select
  e.deptno,s.grade
from
  emp e
join
  salgrade s
on
  e.sal between s.losal and s.hisal
group by
  e.deptno;
```
| deptno | grade |
| :--------:|:--------:|
|     10 |     4 |
|     20 |     1 |
|     30 |     3 |
