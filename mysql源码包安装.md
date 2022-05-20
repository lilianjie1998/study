## 一，准备阶段

```
创建安装账户：

[root@10_128 ~]# tar -xvf mysql-5.7.25-el7-x86_64.tar 
mysql-5.7.25-el7-x86_64.tar.gz
mysql-test-5.7.25-el7-x86_64.tar.gz

[root@10_128 ~]# useradd -s /bin/false -d /usr/local/mysql mysql

[root@10_128 ~]# id mysql
uid=1001(mysql) gid=1001(mysql) 组=1001(mysql)
```

## 二，安装过程

```
[root@10_128 ~]# tar -zxvf mysql-5.7.25-el7-x86_64.tar.gz -C /usr/local/mysql/
[root@10_128 ~]# cd /usr/local/mysql/mysql-5.7.25-el7-x86_64/
[root@10_128 mysql-5.7.25-el7-x86_64]# mv * ../
[root@10_128 mysql-5.7.25-el7-x86_64]# cd ..
[root@10_128 mysql]# rmdir mysql-5.7.25-el7-x86_64/
[root@10_128 mysql]# mkdir data
[root@10_128 mysql]# chown -R mysql:mysql ../mysql/
[root@10_128 mysql]# cd bin/

[root@10_128 bin]# ./mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
2022-05-20T06:13:54.435348Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2022-05-20T06:13:54.761179Z 0 [Warning] InnoDB: New log files created, LSN=45790
2022-05-20T06:13:54.804144Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2022-05-20T06:13:54.868897Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 06e39177-d804-11ec-828c-000c29ff3ef3.
2022-05-20T06:13:54.869676Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2022-05-20T06:13:54.870620Z 1 [Note] A temporary password is generated for root@localhost: PhlM&o;Ff1e?
[root@10_128 bin]# cp ../support-files/mysql.server /etc/init.d/mysqld
```

## 三，配置过程

```
[root@10_128 bin]# vim /etc/my.cnf
[root@10_128 bin]# cat /etc/my.cnf
[mysqld]
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
socket = /tmp/mysql.sock
log-error = /usr/local/mysql/data/error.log
pid-file = /usr/local/mysql/data/mysql.pid

!includedir /etc/my.cnf.d
```

## 四，启动服务并登陆

```
[root@10_128 ~]# chkconfig --add mysqld
[root@10_128 ~]# chkconfig --level 2345 mysqld on 开机服务自启动
[root@10_128 ~]# chkconfig --level 2345 mysqld off 开机服务不自启动
[root@10_128 ~]# service mysqld start
Starting MySQL.Logging to '/usr/local/mysql/data/error.log'.
 SUCCESS! 
 
```

## 五，修改密码

```
添加环境变量：
将内容export PATH=$PATH:/usr/local/mysql/bin添加到 /etc/profile文件的结尾
执行[root@10_128 ~]# source /etc/profile

[root@10_128 ~]# mysql -u root -p
Enter password: 
注：密码为root@localhost: PhlM&o;Ff1e?  参考安装过程

mysql> set password=password("Li!@12..");
Query OK, 0 rows affected, 1 warning (0.03 sec)

mysql> quit
Bye
```

## 六，数据库的使用命令

```
查看表结构
describe user;

创建新的库
create database auth；

创建新的表
create table user（字段1 类型，字段2 类型，....，primary key （主键名））；

删除表
drop table user；
删除库
drop database auth；

插入数据
insert into 表名（字段1，字段2,...） values(字段1的值，字段2的值，....)；

查询数据
select 字段1，字段2，··· from 表名 where 条件表达式

修改数据
update 表名 set 字段名1=字段值1 where 条件表达式

更改密码
update mysql.user set password=PASSWORD('123456') where user='root';

flush privileges;  刷新用户授权

授权
grant 权限列表 on 库名.表名 to 用户名@来源地址 [ identified by '密码' ]

权限列表：
select查询
insert插入
update更新
delete删除

create创建
alter修改
drop删除mysql数据库表结构权限

查看权限
show grants for 用户名@来源地址
撤销权限
revoke 权限列表 on 数据库名.表名 from 用户名@来源地址

备份数据库
tar zcf /backup/mysql_all-$(date +%F).tar.gz /usr/local/mysql/data/
```

