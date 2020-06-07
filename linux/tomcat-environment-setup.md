# Linux环境安装Tomcat

## 前提条件
**默认你已经安装好了JDK 如果没有安装JDK  请先去安装JDK**

## 下载 tomcat
```SHELL
# 国内下载使用如下镜像地址
wget https://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.55/bin/apache-tomcat-8.5.55.tar.gz

# aws 使用如下官网地址
wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.55/bin/apache-tomcat-8.5.55.tar.gz
```
## 解压
```SHELL
# 进入安装包目录 解压文件
tar zvxf apache-tomcat-8.5.55.tar.gz
```
将解压文件放置到指定目录 
```SHELL
# 个人习惯安装到 /usr/local/tomcat/目录下
mkdir -p /usr/local/tomcat/
# 执行复制操作
cp -r apache-tomcat-8.5.55 /usr/local/tomcat/tomcat8.5
```
## 配置 tomcat
编辑配置文件
```SHELL
vim /usr/local/tomcat/tomcat8.5/conf/server.xml
```
修改 tomcat 端口 和 字符集  <font color="red">按个人需求</font>
```XML
    <Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               URIEncoding="UTF-8"/>
```
添加环境变量 <font color="red">按个人需求</font>
```SHELL
vim /etc/profile

# 在最开始添加如下内容
CATALINA_HOME=/usr/local/tomcat/tomcat8.5
export PATH=$PATH:$CATALINA_HOME/bin
```
使环境变量立即生效
```SHELL
source /etc/profile
```
启动 tomcat 
```SHELL
# 如果已配置环境变量 则可以在任意路径下执行如下命令
startup.sh

# 如果未配置环境变量

# 执行启动命令
/usr/local/tomcat/tomcat8.5/bin/startup.sh
```
问题

```shell
# 启动过程中可能出现如下问题
[ec2-user@ip-172-31-4-175 tomcat8.5]$ sudo /usr/local/tomcat/tomcat8.5/bin/startup.sh
Neither the JAVA_HOME nor the JRE_HOME environment variable is defined
At least one of these environment variable is needed to run this program

# 解决办法
# 查看当前JAVA_HOME配置
echo $JAVA_HOME
# 显示如下
/usr/local/java/jdk1.8

# 编辑配置文件
sudo vim /usr/local/tomcat/tomcat8.5/bin/catalina.sh

# 在配置文件头部加入JAVA_HOME信息
export JAVA_HOME=/usr/local/java/jdk1.8
```

查看启动日志

```SHELL
tail -f /usr/local/tomcat/tomcat8.5/logs/catalina.out
```
访问 tomcat:

```SHELL
http://<hostname>:<port>/
```

<font color="red">✨Tips: </font>

<font color="red">1. 注意打开防火墙8080端口，外网才可访问</font>

<font color="red">2. aws 使用安全组管理网络访问权限，外网访问注意配置入站规则。</font>


## 配置tomcat 控制台 <font color="red">可选*</font>
创建 tomcat 控制台用户
```XML
# 编辑用户角色配置文件
vim /usr/local/tomcat/tomcat8.5/conf/tomcat-users.xml

# 添加一个超级权限 用户名 tomcat 密码 123456

<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="123456" roles="manager-gui,manager-script,manager-jmx,manager-status"/>
```
指定允许访问控制台的 IP 地址
```SHELL
vim /usr/local/tomcat/tomcat8.5/webapps/manager/META-INF/context.xml
# 添加可访问 manager 的主机IP
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|192.168.127.1|192.168.127.111" />
vim /usr/local/tomcat/tomcat8.5/webapps/host-manager/META-INF/context.xml
# 添加可访问 host-manager 的主机IP
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|192.168.127.1|192.168.127.111" />
```

