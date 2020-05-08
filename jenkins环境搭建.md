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
```
# 如果访问特别慢 建议使用迅雷等工具离线下载安装
wget https://pkg.jenkins.io/redhat-stable/jenkins-2.190.3-1.1.noarch.rpm
```
### 安装
```
rpm -ivh jenkins-2.190.3-1.1.noarch.rpm
```
## 配置Jenkins
```
# 编辑配置文件
vi /etc/sysconfig/jenkins

# 修改如下内容
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
# 9967b774d1dd4c709a8fca93d6bb3a82
```

**问题及解决办法**

```
1. 如果页面出现 Please wait while Jenkins is getting ready to work, 并且长时间无反应:

# 修改配置文件
vi /var/lib/jenkins/hudson.model.UpdateCenter.xml

# 修改为如下
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
# [https://updates.jenkins.io/update-center.json] -> [https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json]

# 重启服务
systemctl start jenkins
```



****

**注意: 不要安装推荐的插件 因为会很慢***

## 初始化Jenkins

```
# 浏览器访问地址 等待刷完成
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

