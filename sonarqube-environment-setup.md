# Linux环境搭建SonarQube

**注意 7.9 版以后已不支持 mysql 数据库，持久化请安装 PostgreSql **。

## 下载

```SHELL
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.3.1.34397.zip
```

## 解压
使用 upzip 解压， 如果没有 unzip ， 先下载安装 unzip。
```SHELL
yum install unzip
```
执行解压操作。
```SHELL
# 创建安装目录 个人习惯安装到 /usr/local/sonar 目录下
mkdir -p /usr/local/sonar

# 执行解压
unzip sonarqube-8.3.1.34397.zip 

# 解压文件移动安装目录下
mv sonarqube-8.3.1.34397/* /usr/local/sonar
```
## 安装
为 sonarqube 创建一个用户
```SHELL
# 创建sonar用户，不能使用root启动，否则报错
useradd sonar
# 更改sonar目录及文件权限
chown -R sonar. /usr/local/sonar
```
使用 postgresql 为 sonarqube 创建一个库
```SQL
# 切换用户
su - postgres

# 登录数据库
psql

# 创建用户
create user root with password '123456';

# 创建数据库
create database sonar owner root;

# 授权
grant all privileges on database sonar to root;
```
修改配置文件
```SHELL
vim /usr/local/sonar/conf/sonar.properties
# 修改数据源
sonar.jdbc.username=root 
sonar.jdbc.password=123456

# <sonar> 是为 sonar 创建的数据库名
sonar.jdbc.url=

# sonar 默认监听 9000 端口，如果 9000 端口被占用，需要在此配置文件中更改 监听端口
```

# 服务管理

```SHELL
# 进入安装目录
cd /usr/local/sonar

# 启动 (不显示启动日志)
su sonar ./bin/linux-x86-64/sonar.sh start 

# 查看状态
su sonar ./bin/linux-x86-64/sonar.sh status 

# 停止
su sonar ./bin/linux-x86-64/sonar.sh stop

# 查看日志
tail -f logs/sonar.log

# 启动并查看日志
su sonar ./bin/linux-x86-64/sonar.sh console
```

注意： 当提示如下信息时：

```SHELL
#  WrapperSimpleApp: Encountered an error running main: java.lang.IllegalStateException: SonarQube requires Java 11 to run

# 请先去安装 jdk11 或以上版本

# 修改配置文件
vim ./conf/wrapper.conf

# 添加如下内容 (此处值为安装的 jdk11 路径)
wrapper.java.command=/usr/local/java/jdk11/bin/java
```

注意： 当提示如下信息时：

```
jvm 1    | [1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
jvm 1    | [2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
jvm 1    | 2020.05.14 20:54:05 WARN  app[][o.s.a.p.AbstractManagedProcess] Process exited with exit value [es]: 78
jvm 1    | 2020.05.14 20:54:05 INFO  app[][o.s.a.SchedulerImpl] Process[es] is stopped
jvm 1    | 2020.05.14 20:54:05 INFO  app[][o.s.a.SchedulerImpl] SonarQube is stopped


vi /etc/security/limits.conf

# [1] 解决办法 添加如下内容（不要忽略 * 号）
* soft nofile 65536
* hard nofile 65536

# [2] 解决办法 添加如下内容
vm.max_map_count=262144

# 临时修改
sysctl -w vm.max_map_count=262144

# 使配置生效
sysctl -p
```



## 查看日志

控制台输出如下信息时，证明安装已成功。
```
jvm 1    | 2020.05.14 20:10:32 INFO  app[][o.s.a.SchedulerImpl] Process[ce] is up
jvm 1    | 2020.05.14 20:10:32 INFO  app[][o.s.a.SchedulerImpl] SonarQube is up
```

## 访问

在浏览器输入访问地址。
```SHELL
http://<hostname>:<port> # 端口默认 9000
```
## 登录

```
# 用户名 
admin
# 密码
admin
```

