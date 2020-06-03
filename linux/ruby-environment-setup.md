# Linux环境安装Ruby

## 下载

```SHELL
wget https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.6.tar.gz
```

## 解压
```SHELL
# 进入安装包目录 解压文件
tar zvxf ruby-2.6.6.tar.gz
```
## 创建安装目录 

```SHELL
# 个人习惯安装到 /usr/local/ruby/ 目录下
mkdir -p /usr/local/ruby/
```
## 编译安装

```SHELL
# 安装编译环境
yum -y install gcc tcl gcc-c++

# 进入源码路径
cd ruby-2.6.6/

# 配置
./configure --prefix=/usr/local/ruby/ruby2.6

# 编译安装
make && make install
```
## 验证安装结果

```SHELL
# 进入bin目录
cd /usr/local/ruby/ruby2.6/bin/

# 执行如下命令 查看控制台
./ruby -v
```
## 配置 ruby
修改环境变量 
```SHELL
vim /etc/profile

# 在最开始添加如下内容
export RUBY_HOME=/usr/local/ruby/ruby2.6
export PATH=$PATH:$RUBY_HOME/bin

# 保存退出
:x
```
使环境变量立即生效
```SHELL
source /etc/profile
```
切换到任意目录， 查看是否配置成功

```
ruby -v
```
查看 ruby 路径

```SHELL
echo $RUBY_HOME
```
## 配置 gem 
```SHELL
# 移除自带源
gem source -r https://rubygems.org/
# 添加国内镜像源
gem source -a https://gems.ruby-china.com/
gem sources -u
```
**添加过程共可能出现问题如下：**

```SHELL
# install OpenSSL and rebuild Ruby (preferred) or use non-HTTPS sources (Gem::Exception)

# 原因：没有安装 openssl
# 解决办法：

# 安装 openssl
yum install openssl-devel -y

# 进入源码解压目录
cd /root/ruby-2.6.6/ext/openssl
# 执行如下命令
ruby ./extconf.rb
# 编译安装
make && make install

# 如果安装失败 提示如下内容：
make: *** No rule to make target `/include/ruby.h', needed by `ossl.o'.  Stop.

# 编辑 Makefile 文件
vim Makefile

# 在顶部添加如下内容
top_srcdir = ../..

# 保存退出
:x

# 重新编译安装
make && make install

# 重新添加国内镜像源
gem source -a https://gems.ruby-china.com/
gem sources -u
```

```SHELL
# ERROR:  While executing gem ... (NameError)
#     uninitialized constant Gem::RemoteFetcher::Zlib

# 原因：没有安装 Zlib

# 解决办法：
# 安装 zlib
yum install -y zlib-devel

# 进入源码解压目录
cd /root/ruby-2.6.6/ext/zlib

# 执行如下命令
ruby ./extconf.rb

# 编译安装
make && make install

# 如果安装失败 提示如下内容：
make: *** No rule to make target `/include/ruby.h', needed by `ossl.o'.  Stop.

# 编辑 Makefile 文件
vim Makefile

# 在顶部添加如下内容
top_srcdir = ../..

# 保存退出
:x

# 重新编译安装
make && make install

# 重新添加国内镜像源
gem source -a https://gems.ruby-china.com/
gem sources -u
```

验证镜像源是否配置成功

```SHELL
# 执行命令
gem source -l

# 若显示结果如下，则证明配置成功
*** CURRENT SOURCES ***

https://gems.ruby-china.com/
```

## 配置 bundle

```SHELL
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
```
