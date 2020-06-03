# MySQL环境搭建

在CentOS中默认安装有MariaDB(MySQL的分支)，但为了需要，还是要在系统中安装MySQL，而且安装完成之后可以直接覆盖掉MariaDB。

## 下载

```SHELL
wget http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

## rpm安装

```SHELL
rpm -ivh mysql57-community-release-el7-10.noarch.rpm
```

## 安装

安装完成后会自动覆盖掉之前的 mariadb.
```SHELL
yum -y install mysql-community-server
```

## 配置
###   启动服务
```SHELL
systemctl start mysqld.service
```

### 查看运行状态
```SHELL
systemctl status mysqld.service
```

### 查看日志，得到临时密码
```SHELL
grep "password" /var/log/mysqld.log
```
### 登录数据库
```SHELL
mysql -uroot -p
# 输入上面取得的临时密码
```
### 修改新密码
```SHELL
# 把 <new-password> 替换成要设置的密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '<new-password>';

# 当出现如下错误时,
# ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

# 需要设置使用弱密码策略(如果不想用强密码策略)
set global validate_password_policy=LOW;   # 仅检测长度
set global validate_password_length=6;     # 长度大于等于6

# 然后继续重置密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

## 开启远程访问
执行以下命令开启远程访问限制.
```SHELL
# 开启指定 <IP> 的远程访问权限
# 如果想开启所有 IP 的远程访问权限, 使用 `%` 代替 `<IP>`
# 设置访问密码 <password>
grant all privileges on *.* to 'root'@'<IP>' identified by '<password>' with grant option;
# 例如：
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;

# 刷新访问权限
flush privileges;

# 重启
exit
```

##  配置防火墙
防火墙开放MYSQL端口(3306) 和tomcar
```SHELL
firewall-cmd --zone=public --add-port=3306/tcp --permanent

# 使配置立即生效
firewall-cmd --reload
```

## 更改字符集
### 查看字符集
```SHELL
# 登录mysql
mysql -u root -p

# 查看状态
status

# 显示如下
--------------
mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

Connection id:		16
Current database:	
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.7.29 MySQL Community Server (GPL)
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	latin1                                 <-- 注意这里需要修改为utf8
Db     characterset:	latin1                                 <-- 注意这里需要修改为utf8
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/var/lib/mysql/mysql.sock
Uptime:			16 min 38 sec

Threads: 3  Questions: 40  Slow queries: 0  Opens: 122  Flush tables: 1  Open tables: 115  Queries per second avg: 0.040
--------------

# 退出 mysql
exit
```
### 修改字符集
```SHELL
# 编辑配置文件
vim /etc/my.cnf

# 修改内容如下
--------------

[client]                                         # 追加
default-character-set = utf8                     # 追加

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
character-set-server = utf8                      # 追加
collation-server = utf8_general_ci               # 追加

--------------

# 重启服务
systemctl restart mysqld

# 重新登录 查看状态
status

# 显示如下
--------------
mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

Connection id:		3
Current database:	
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.7.29 MySQL Community Server (GPL)
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	utf8
Db     characterset:	utf8
Client characterset:	utf8                         <-- 已修改为utf8
Conn.  characterset:	utf8                         <-- 已修改为utf8
UNIX socket:		/var/lib/mysql/mysql.sock
Uptime:			41 sec

Threads: 1  Questions: 5  Slow queries: 0  Opens: 105  Flush tables: 1  Open tables: 98  Queries per second avg: 0.121
--------------

```