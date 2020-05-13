# Linux环境安装Jenkins

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
wget https://pkg.jenkins.io/redhat-stable/jenkins-2.222.1-1.1.noarch.rpm
```
### 安装
```
rpm -ivh jenkins-2.222.1-1.1.noarch.rpm
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

# 上述全部内容替换为固定 java 命令所在位置：(注意按个人情况修改路径)
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
http://<hostname>:<port>/

# jenkins 发布路径
ll /var/lib/jenkins/
```

**问题及解决办法**

```
1. 如果页面出现 Please wait while Jenkins is getting ready to work, 并且长时间无反应:

# 修改配置文件中的 url 为国内镜像地址
vim /var/lib/jenkins/hudson.model.UpdateCenter.xml

# 修改为如下内容
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>

# 重启服务
systemctl restart jenkins
```
****

## 查看初始密码

````
# 查看自动生成的密码
cat /var/lib/jenkins/secrets/initialAdminPassword
```
**注意 不要安装任何插件 等到镜像配置好了再按需要安装插件**
## 添加管理员用户
按提示输入用户名密码就好

## 实例配置

默认就好

## 初始化Jenkins

```
# 修改插件地址
cd /var/lib/jenkins/updates

sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json

# 浏览器访问地址 等待刷新完成
http://<hostname>:<port>/pluginManager/available

# 重启服务
systemctl restart jenkins

# 浏览器访问地址
http://<hostname>:<port>/pluginManager/advanced
```

## 下载插件失败解决

即使配置了镜像地址， 依然可能出现镜像下载失败的情况。注意查看报错信息 然后去如下地址下载指定的镜像，然后手动安装

```
https://mirrors.tuna.tsinghua.edu.cn/jenkins
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

## 配置Jenkins插件

Jenkins有各种各样的插件用于管理和构建任务，因为之前安装时未安装任何的插件，所以要接下来需要安装一些基础的常用的插件

### 权限管理插件

```
Jenkins默认权限管理粒度特别粗，无法实现精准的权限控制，所以需要安装插件解决企业级权限控制问题。

插件名称：Role-based Authorization Strategy
```

### 凭证管理插件

```
凭据可以用来存储需要密文保护的数据库密码、Gitlab密码信息、Docker私有仓库密码等，以便Jenkins可以和这些第三方的应用进行交互。

插件名称：Credentials Binding
```

## SVN插件

```
用于从连接访问远程 svn  并将远程 svn 目录内容拉取到 jenkins 目录。

插件名称： Subversion
```

## 容器管理插件 

```
用于将项目发布到指定的 web 容器.

插件名称：Deploy to container
```



