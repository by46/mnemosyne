MySQL
======

start and stop
----------------
```
service mysqld start
```

```shell

shell> mysql --user=root mysql
mysql> CREATE USER 'monty'@'localhost' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'localhost'
    ->     WITH GRANT OPTION;
mysql> CREATE USER 'monty'@'%' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'%'
    ->     WITH GRANT OPTION;
mysql> CREATE USER 'admin'@'localhost';
mysql> GRANT RELOAD,PROCESS ON *.* TO 'admin'@'localhost';
mysql> CREATE USER 'dummy'@'localhost';
mysql> FLUSH PRIVILEGES;

```


如何重设 MySQL 的 root 密码
作者：王赛 • 2014年2月25日
MySQL下创建新用户、新数据库、设定访问权限控制都需要用到root密码。万一把root密码忘了，该怎么办？

幸运地是，重设密码很容易。

注意：MySQL的root用户和服务器操作系统的root用户是两个不同的用户，不要搞混了。
安全模式重置法

基本的思路是，以安全模式启动mysql，这样不需要密码可以直接以root身份登录，然后重设密码。

首先，我们停掉MySQL服务：

sudo service mysql stop  
以上命令适用于Ubuntu和Debian。CentOS、Fedora和RHEL下使用mysqld替换mysql。

以安全模式启动MySQL：

sudo mysqld_safe --skip-grant-tables --skip-networking &  
注意我们加了--skip-networking，避免远程无密码登录 MySQL。

这样我们就可以直接用root登录，无需密码：

mysql -u root  
接着重设密码：

mysql> use mysql;  
mysql> update user set password=PASSWORD("mynewpassword") where User='root';  
mysql> flush privileges;  
注意，命令后需要加分号。

重设完毕后，我们退出，然后启动 MySQL 服务：

mysql > quit  
quit不需要分号。

重启服务：

sudo service mysql restart  
同样，以上命令适用于Ubuntu和Debian，Centos、Fedora和RHEL需要用mysqld替换mysql。

现在可以尝试用新密码登录了：

mysql -u root -pmynewpassword  
注意，-p 和密码间不能有空格。
其他方案

以上是通用方案，在Ubuntu和Debian系统上，有一个debian-sys-maint用户，Debian类系统下一些系统脚本对mysql的操作是通过这个用户完成的。所以我们可以通过这个用户来修改 root 密码。该用户的密码可以在/etc/mysql/debian.cnf下找到：

# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost  
user     = debian-sys-maint  
password = PASSWORD  
socket   = /var/run/mysqld/mysqld.sock  
[mysql_upgrade]
host     = localhost  
user     = debian-sys-maint  
password = PASSWORD  
socket   = /var/run/mysqld/mysqld.sock  
basedir  = /usr  
用该用户登录 MySQL 后，也可以修改密码（具体修改过程见上）：

sudo mysql -u debian-sys-maint -p  