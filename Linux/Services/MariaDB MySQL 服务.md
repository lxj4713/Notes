<!-- toc -->
[toc]
<!-- toc -->



## 初始化MariaDB服务

## 安装并启动

```bash
yum install mariadb mariadb-server -y

systemctl start mariadb 
systemctl enable mariadb
```



## **MariaDB 安装后正常使用前需初始化**

1. 设置root管理员在数据库中的密码值（注意，该密码并非root管理员在系统中的密码，这里的密码值默认应该为空，可直接按回车键）。
2. 设置root管理员在数据库中的专有密码。
3. 随后删除匿名账户，并使用root管理员从远程登录数据库，以确保数据库上运行的业务的安全性。
4. 删除默认的测试数据库，取消测试数据库的一系列访问权限。
5. 刷新授权列表，让初始化的设定立即生效。



```shell
[root@centos1 ~]# mysql_secure_installation 
...
Enter current password for root (enter for none):  #当前数据库密码为空，直接按回车键

Set root password? [Y/n] y

Remove anonymous users? [Y/n] y（删除匿名账户）

Disallow root login remotely? [Y/n] y（禁止root管理员从远程登录）

Remove test database and access to it? [Y/n] y（删除test数据库并取消对它的访问权限）

Reload privilege tables now? [Y/n] y（刷新授权表，让初始化后的设定立即生效）
```



在很多生产环境中都需要使用站库分离的技术（即网站和数据库不在同一个服务器上），如果需要让root管理员远程访问数据库，可在上面的初始化操作中设置策略，以允许root管理员从远程访问。然后还需要设置防火墙，使其放行对数据库服务程序的访问请求，数据库服务程序默认会占用3306端口，在防火墙策略中服务名称统一叫作mysql：

```
[root@linuxprobe ~]# firewall-cmd --permanent --add-service=mysql
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```



**设置密码**

``` bash
MariaDB [(none)]> SET password = PASSWORD('mysqlpasswd');
```



# 管理用户及授权

`CREATE USER 用户名@主机名 IDENTIFIED BY '密码';`  的格式创建数据库管理账户,结尾分号(;)

``` bash
MariaDB [(none)]> CREATE USER  lxj@localhost IDENTIFIED BY 'lxjsqlpassws';
Query OK, 0 rows affected (0.00 sec)
```

## 查询用户信息

``` bash
MariaDB [(none)]> use mysql
MariaDB [mysql]> select host,user,password  from user where user='lxj';
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | lxj  | *355C3C0FADD71D72CABEF2412BC5ADA30A492CBC |
+-----------+------+-------------------------------------------+
1 row in set (0.00 sec)

```



## grant 命令用于为账户进行授权

在使用grant命令时需要写上要赋予的权限、数据库及表单名称，以及对应的账户及主机信息

GRANT命令的常见格式以及解释

| 命令                                           | 作用                                             |
| ---------------------------------------------- | ------------------------------------------------ |
| GRANT 权限 ON 数据库.表单名称 TO 用户名@主机名 | 对某个特定数据库中的特定表单给予授权             |
| GRANT 权限 ON 数据库.* TO 用户名@主机名        | 对某个特定数据库中的所有表单给予授权             |
| GRANT 权限 ON *.* TO 用户名@主机名             | 对所有数据库及所有表单给予授权                   |
| GRANT 权限1,权限2 ON 数据库.* TO 用户名@主机名 | 对某个数据库中的所有表单给予多个授权             |
| GRANT ALL PRIVILEGES ON *.* TO 用户名@主机名   | 对所有数据库及所有表单给予全部授权（需谨慎操作） |



``` sql
MariaDB [mysql]> show grants for lxj@localhost;
+----------------------------------------------------------------------------------------------------------+
| Grants for lxj@localhost                                                                                 | 
+----------------------------------------------------------------------------------------------------------
| GRANT USAGE ON *.* TO 'lxj'@'localhost' IDENTIFIED BY PASSWORD '*355C3C0FADD71D72CABEF2412BC5ADA30A492CBC'
| GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.`user` TO 'lxj'@'localhost'                             |
+----------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)
```



切回root账户，移除刚才的授权，移除授权的命令（revoke）与授权命令（grant）不同之外，其余部分都是一致的

``` sql
MariaDB [mysql]> REVOKE SELECT,UPDATE,DELETE,INSERT ON mysql.user FROM lxj@localhost;
Query OK, 0 rows affected (0.00 sec)

```







# 数据库操作

简单操作如下，深入了解见DataBases篇



## 创建数据库的命令以及作用

| 用法                                                         | 作用                   |
| ------------------------------------------------------------ | ---------------------- |
| CREATE database 数据库名称。                                 | 创建新的数据库         |
| DESCRIBE 表单名称;                                           | 描述表单               |
| UPDATE 表单名称 SET attribute=新值 WHERE attribute > 原始值; | 更新表单中的数据       |
| USE 数据库名称;                                              | 指定使用的数据库       |
| SHOW databases;                                              | 显示当前已有的数据库   |
| SHOW tables;                                                 | 显示当前数据库中的表单 |
| SELECT * FROM 表单名称;                                      | 从表单中选中某个记录值 |
| DELETE FROM 表单名 WHERE attribute=值;                       | 从表单中删除某个记录值 |

## 增删改查 CRUD

``` SQL
INSERT INTO mytable(rows,lines,name) VALUES('11','22', 'lxj');
DROP DATABASE mydata;
DELETE FROM mytable;   # 删除mytable表中的内容
UPDATE mytable SET rows=55 ;
SELECT * FROM ... WHERE ...;
```

where命令中使用的参数以及作用

| 参数    | 作用             |
| ------- | ---------------- |
| =       | 相等             |
| <>或!=  | 不相等           |
| >       | 大于             |
| <       | 小于             |
| >=      | 大于或等于       |
| <=      | 小于或等于       |
| BETWEEN | 在某个范围内     |
| LIKE    | 搜索一个例子     |
| IN      | 在列中搜索多个值 |



# 数据库备份及恢复

mysqldump 命令用于备份数据库数据

> mysqldump [参数] [数据库名称]

其中参数与mysql命令大致相同，-u参数用于定义登录数据库的账户名称，-p参数代表密码提示符。

**将mydata数据库内容导出成一个文件，保存到root目录**

```
[root@centos1 ~]# mysqldump -u root -p mydata > /root/mydata.dump
```



**输入重定向符把刚刚备份的数据库文件导入到mysql中**，就可以将恢复之前的数据了

``` sql
[root@centos1 ~]# mysql -u root -p mydata < /root/mydata.dump 
```









# ISSUE

## 远程连接失败 Host 'xxx' is not allowed to connect to this MariaDB server

**方法一：改表**

修改成%表示，所有主机都可以通过root用户访问数据库

``` sql
MariaDB [mysql]> update user set host='%' where user='root';
MariaDB [mysql]> select host,user,password from user;
+-----------------------+------+-------------------------------------------+
| host                  | user | password                                  |
+-----------------------+------+-------------------------------------------+
| %                     | root | *F2B1CE8FB96529C8598D17A00309E3B737CDB63E |
+-----------------------+------+-------------------------------------------+
5 rows in set (0.00 sec)
```

输入命令`mysql> FLUSH PRIVILEGES;` 使修改生效，再次远程连接数据库

**方法二：授权**

允许root使用mypassword从任何主机连接到mysql服务器的话。 

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 
```

允许用户myuser从ip为192.168.1.3的主机连接到mysql服务器，并使用mypassword作为密码 

``` sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.1.3' IDENTIFIED BY 
'mypassword' WITH GRANT OPTION; 
```

输入命令`mysql> FLUSH PRIVILEGES;` 使修改生效，再次远程连接数据库

FLUSH PRIVILEGES; 刷新先前的修改。

