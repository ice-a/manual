# linux环境安装Maven

## 前提条件

**默认你已经安装好了JDK 如果没有安装JDK  请先去安装JDK**

## 下载maven

```
wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz
```
## 解压
```
# 进入安装包目录 解压文件
tar zvxf apache-maven-3.6.1-bin.tar.gz
```
将解压文件放置到指定目录 
```
# 个人习惯安装到 /usr/local/maven/目录下
mkdir -p /usr/local/maven/
# 执行复制操作
 cp -r apache-maven-3.6.1 /usr/local/maven/maven3.6.1
```
## 配置 maven
编辑配置文件
```
vim /usr/local/maven/maven3.6.1/conf/settings.xml
```
修改 maven 镜像 (使用阿里镜像)
```
<mirror>
  <id>aliyun</id>
  <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>
<mirror>
```
修改环境变量 
```
vim /etc/profile

# 在最开始添加如下内容
export MAVEN_HOME=/usr/local/maven/maven3.6.1
export PATH=$PATH:$MAVEN_HOME/bin
```

使环境变量立即生效

```
source /etc/profile
```

查看是否安装成功

```
mvn -v
```

查看 mvn 路径

```
echo $MAVEN_HOME
```

