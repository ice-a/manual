# Linux环境安装PostgresSQL

PostgreSQL版本： PostgreSQL 10.10,  64-bit

## 安装RPM
```SHELL
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```
## 安装客户端
```SHELL
yum install postgresql10 -y
```
## 安装服务端
```SHELL
yum install postgresql10-server -y
```
## 初始化 db
```SHELL
/usr/pgsql-10/bin/postgresql-10-setup initdb
```
## 启动
```SHELL
systemctl start postgresql-10
```
## 设置开机自启
```SHELL
systemctl enable postgresql-10
```

## 切换用户
```SHELL
# PostgresSQL 安装后会自动创建 postgres 用户，无密码
su - postgres
```
## 登录数据库
```SHELL
psql
```

## 创建用户
```SHELL
# 创建用户
create user root with password '123456';
# 创建数据库
create database sonar owner root;
# 授权
grant all privileges on database sonar to root;
```

## 配置

```SHELL
vim /var/lib/pgsql/10/data/postgresql.conf

# 打开注释并修改为 *
listen_addresses = '*'
# 打开注释
port=5432

vim /var/lib/pgsql/10/data/pg_hba.conf
# 修改
host    all             all             127.0.0.1/32             md5     # [peer] -> [md5]

# 添加
host    all             all             0.0.0.0/0                md5
```

## 重启服务

```SHELL
service postgresql-10 restart
```

