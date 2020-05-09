# Linux环境安装JDK

## 查看当前系统有无Java环境
运行如下命令，查看系统是否安装了 Java

```
java -version
```
如果显示结果如下， 则证明系统已默认安装了 OpenJDK。
```
openjdk version "1.8.0_161"
OpenJDK Runtime Environment (build 1.8.0_161-b14)
OpenJDK 64-Bit Server VM (build 25.161-b14, mixed mode)
```
如果对Java环境无特殊要求， 可直接使用系统自带的OpenJDK，不必继续阅读此文档。

## 卸载系统自带OpenJDK
检查 OpenJDK 安装包，运行如下命令：

```
rpm -qa | grep java 
```
系统显示结果如下
```
java-1.8.0-openjdk-headless-1.8.0.161-2.b14.el7.x86_64
tzdata-java-2018c-1.el7.noarch
python-javapackages-3.4.1-11.el7.noarch
javapackages-tools-3.4.1-11.el7.noarch
java-1.8.0-openjdk-1.8.0.161-2.b14.el7.x86_64
```
卸载OpenJDK，运行如下命令：

```
# 当弹出是否卸载消息时，输入 y 确认卸载
yum remove *openjdk*
```
查看卸载情况，再次输入上述检查命令：

```
rpm -qa | grep java 
```
若显示结果如下，则证明已成功卸载系统自带OpenJDK
```
tzdata-java-2018c-1.el7.noarch
python-javapackages-3.4.1-11.el7.noarch
javapackages-tools-3.4.1-11.el7.noarch
```
**以上内容因不同的发型版本显示结果会有些许差异， 请自行判断**

## 安装JDK
获取想要安装的JDK版本  (官网下载并上传到指定目录)

进入JDK安装包目录 解压安装包

```
# 创建jdk安装目录
# 本人习惯安装到 /usr/local/java 文件夹下 可按个人习惯自行选择安装位置
mkdir -p /usr/local/java
# 解压安装包到指定目录
tar -zxvf jdk-8u161-linux-x64.tar.gz -C /usr/local/java
# 进入解压文件位置 修改解压文件夹名称
cd /usr/local/java
mv jdk1.8.0_161/ jdk1.8
```
设置环境变量
```
vim /etc/profile

# 在配置文件最前面添加如下脚本
export JAVA_HOME=/usr/local/java/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin

# 保存退出
:x
```
使环境变量立马生效
```
source /etc/profile
```
检查是否安装成功
```
java -version
```
输出配置的环境变量
```
echo $JAVA_HOME
```