# Linux环境安装Tomcat

## 前提条件
**默认你已经安装好了JDK 如果没有安装JDK  请先去安装JDK**
## 下载 tomcat
```SHELL
wget https://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.54/bin/apache-tomcat-8.5.54.tar.gz
```
## 解压
```SHELL
# 进入安装包目录 解压文件
tar zvxf apache-tomcat-8.5.54.tar.gz
```
将解压文件放置到指定目录 
```SHELL
# 个人习惯安装到 /usr/local/tomcat/目录下
mkdir -p /usr/local/tomcat/
# 执行复制操作
cp -r apache-tomcat-8.5.54 /usr/local/tomcat/tomcat8.5
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

# 进入 tomcat 解压目录
cd /usr/local/tomcat/tomcat8.5/bin

# 执行启动命令
./startup.sh
```
查看启动日志
```SHELL
tail -f /usr/local/tomcat/tomcat8.5/logs/catalina.out
```
访问 tomcat
```SHELL
http://<hostname>:<port>/
```
## 配置tomcat 控制台 <font color="red">可选</font>
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

