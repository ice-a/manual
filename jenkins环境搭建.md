# Jenkins环境搭建

## 安装JDK
Jenkins运行需要java环境, 所以需要先安装 jdk

```
# 安装 openjdk
yum install java-1.8.0-openjdk* -y

# 验证是否安装成功
java -version

# jdk安装目录
ll /usr/lib/jvm/
```
## 安装Jenkins
### 获取官网 jenkins 安装包
官网地址:  `https://jenkins.io/zh/`
**注意每次去官方取发布的最新稳定版 防止装完以后许多插件用不了 还得继续升级**
```
# 如果访问特别慢 建议使用迅雷等工具离线下载安装
wget https://pkg.jenkins.io/redhat-stable/jenkins-2.222.3-1.1.noarch.rpm
```
### 安装
```
rpm -ivh jenkins-2.222.3-1.1.noarch.rpm
```
**本质上在`/var/lib/jenkins/`路径下发布了一个jenkins的war包**

## 如果<font color="red">未使用</font>OpenJDK，自行配置Java位置

```
# 修改启动脚本
vim /etc/init.d/jenkins

# 找到如下内容：
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/lib/jvm/java-11.0/bin/java
/usr/lib/jvm/jre-11.0/bin/java
/usr/lib/jvm/java-11-openjdk-amd64
/usr/bin/java
"
do
  [ -x "$JENKINS_JAVA_CMD" ] && break
  JENKINS_JAVA_CMD="$candidate"
done

# 上述全部内容替换为固定java命令位置：(注意按个人情况修改路径)
JENKINS_JAVA_CMD=usr/local/java/jdk1.8/bin/java
```
## 配置Jenkins

```
# 编辑配置文件
vim /etc/sysconfig/jenkins

# 修改如下内容
# 注意用户名必须具有足够的权限 测试环境默认使用root就可以
JENKINS_USER="root" # jenkins用户名 root
JENKINS_PORT="80"   # jenkins端口   80

# 启动jenkins
systemctl start jenkins
```
## 配置防火墙

```
# 端口 {port} = 80 
firewall-cmd --zone=public --add-port={port}/tcp --permanent
firewall-cmd --reload
```

## 访问Jenkins

```
# 浏览器访问
http://{hostname}:[port]/

# jenkins 发布路径
ll /var/lib/jenkins/

# 查看自动生成的密码
cat /var/lib/jenkins/secrets/initialAdminPassword
```

**问题及解决办法**

```
1. 如果页面出现 Please wait while Jenkins is getting ready to work, 并且长时间无反应:

# 修改配置文件中的 url 为国内镜像地址
vi /var/lib/jenkins/hudson.model.UpdateCenter.xml

# 修改为如下内容
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://mirror.xmission.com/jenkins/updates/update-center.json</url>
  </site>
</sites>

# 重启服务
systemctl start jenkins
```
**注意: 不要安装推荐的插件 因为会很慢 等到配置好国内镜像后手动按需要安装插件 **
## 初始化Jenkins
```
# 浏览器访问地址 等待刷新完成
http://{hostname}:[port]/pluginManager/available

# 修改插件地址
cd /var/lib/jenkins/updates

sed -i 's/http:\/\/updates.jenkinsci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json

# 浏览器访问地址
http://{hostname}:[port]/pluginManager/advanced

# 最底部 Update Site 填入如下路径
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

# 重启服务
http://{hostname}:[port]/restart 
```

## 卸载Jenkins
```
# rpm卸载
rpm -e jenkins
# 检查是否卸载成功
rpm -ql jenkins 
# 彻底删除残留文件：
find / -iname jenkins | xargs -n 1000 rm -rf
```