# Linux环境安装Jdk
## 检查Java环境
运行如下命令，查看系统是否已经安装了 Jdk。
```SHELL
java -version
```
如果显示结果如下， 则证明系统已默认安装了 OpenJdk。
```SHELL
openjdk version "1.8.0_161"
OpenJdk Runtime Environment (build 1.8.0_161-b14)
OpenJdk 64-Bit Server VM (build 25.161-b14, mixed mode)
```
如果对Java环境无特殊要求， 可直接使用系统自带的OpenJdk，不必继续阅读此文档。
## 卸载OpenJdk
检查 OpenJdk 安装包，运行如下命令：
```SHELL
rpm -qa | grep java 
```
系统显示结果如下：
```SHELL
java-1.8.0-openjdk-headless-1.8.0.161-2.b14.el7.x86_64
tzdata-java-2018c-1.el7.noarch
python-javapackages-3.4.1-11.el7.noarch
javapackages-tools-3.4.1-11.el7.noarch
java-1.8.0-openjdk-1.8.0.161-2.b14.el7.x86_64
```
卸载OpenJdk，运行如下命令：
```SHELL
yum remove *openjdk* -y
```
查看卸载情况，再次输入上述检查命令：
```SHELL
rpm -qa | grep java 
```
若显示结果如下，则证明已成功卸载系统自带OpenJdk。
```SHELL
tzdata-java-2018c-1.el7.noarch
python-javapackages-3.4.1-11.el7.noarch
javapackages-tools-3.4.1-11.el7.noarch
```
**以上内容因不同的发型版本显示结果会有些许差异， 请自行判断**
## 安装Jdk
获取想要安装的Jdk版本  (官网下载并上传到指定目录)。
进入Jdk安装包目录，解压安装包。

```SHELL
# 创建jdk安装目录
# 本人习惯安装到 /usr/local/java 文件夹下 可按个人习惯自行选择安装位置
mkdir -p /usr/local/java
# 解压安装包到指定目录
tar -zxvf jdk-8u161-linux-x64.tar.gz -C /usr/local/java
# 进入解压文件位置 修改解压文件夹名称
cd /usr/local/java
mv jdk1.8.0_161/ jdk1.8
```
设置环境变量。
```SHELL
vim /etc/profile

# 在配置文件最前面添加如下脚本
export JAVA_HOME=/usr/local/java/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin

# 保存退出
:x
```
使环境变量立马生效。
```SHELL
source /etc/profile
```
可能出现问题及解决方法：

```shell
# 执行 source 命令可能出现如下问题
# sudo: source: command not found

# 解决办法 注意使用拥有 root 权限的用户
# 执行命令
locate source /etc/profile

# 显示如下
locate: can not stat () `/var/lib/mlocate/mlocate.db': No such file or directory

# 继续执行
updatedb
```

检查是否安装成功。

```SHELL
java -version
```
输出配置的环境变量。
```SHELL
echo $JAVA_HOME
```

