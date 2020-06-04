# Linux环境安装Nginx

##  安装环境准备

+ gcc 安装

```shell
# 安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，需要安装 gcc

yum install -y gcc-c++ 
```

+ PCRE 安装

```shell
# PCRE(Perl Compatible Regular Expressions) 是一个 Perl 库，包括 perl 兼容的正则表达式库。
# nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库

yum install -y pcre pcre-devel
```

>  注：pcre-devel 是使用 pcre 开发的一个二次开发库， nginx 也需要此库。

+ zlib

```shell
# zlib 库提供了很多种压缩和解压缩的方式
# nginx 使用 zlib 对 http 包的内容进行 gzip 压缩
# 所以需要在 linux 上安装 zlib 库 

yum install -y zlib zlib-devel
```

+ openssl

```shell
# openssl 是一个强大的安全套接字层密码库
# 囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议
# 并提供丰富的应用程序供测试或其它目的使用。

# nginx 不仅支持 http 协议，还支持 https (即在 ssl 协议上传输 http )
# 所以需要在 linux 安装 openssl 库
yum install -y openssl openssl-devel
```

##  编译安装

### 新建临时文件目录

```shell
mkdir -p /var/temp/nginx
```
### 获取安装包
```shell
wget http://nginx.org/download/nginx-1.18.0.tar.gz
```
### 解压安装包

```shell
tar -zxvf nginx-1.8.0.tar.gz

# 进入解压目录
cd nginx-1.8.0
```

### 编译生成 makefile

```
# 使用 --prefix 设置 nginx 安装目录, 可根据个人习惯自行设置
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```

### 编译安装
```
make 
make install
```
### 查看目录
```shell
cd /usr/local/nginx
ll

# 若显示结果如下 则证明安装成功
total 4
drwxr-xr-x. 2 root root 4096 Jun  4 09:37 conf
drwxr-xr-x. 2 root root   40 Jun  4 09:37 html
drwxr-xr-x. 2 root root   19 Jun  4 09:37 sbin
```

## 启动nginx
### 启动
```shell
# 进入安装目录
cd /usr/local/nginx/sbin/

# 启动
./nginx 
```
### 测试访问
+ 内网访问
```shell
# 运行如下命令
curl http://localhost

# 显示如下 代表启动成功
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
+ 外网访问 <font color="red">(注意关闭防火墙 或 开启 80 端口)</font>
```
直接输入 ip 地址 若显示 Welcome to nginx! 就代表已经启动成功了。
```

****

### 查看进程

```shell
ps aux|grep nginx

# 显示如下
root      36255  0.0  0.0  20568   632 ?        Ss   09:41   0:00 nginx: master process ./nginx
nobody    36256  0.0  0.1  23092  1392 ?        S    09:41   0:00 nginx: worker process
root      36258  0.0  0.0 112812   968 pts/0    S+   09:41   0:00 grep --color=auto nginx

# 说明
# root   后第一串数字 ->  主进程id
# nobody 后第一串数字 ->  工作进程
```


### 指定配置文件启动

****

```shell
./nginx -c /usr/local/nginx/conf/nginx.conf
```

 ```shell
# 如果不指定 -c 参数, nginx 在启动时默认加载 conf/nginx.conf 文件
# 此文件的地址也可以在编译安装 nginx 时指定./configure 参数
--conf-path=path(nginx.conf)
 ```

## 停止 nginx

### 快速停止 [不建议使用]

```shell
# 进入软件目录
cd /usr/local/nginx/sbin

# 此命令会先查出 nginx 进程 id
# 再使用 kill 命令 强制杀掉进程
./nginx -s stop
```

### 完整停止 [建议使用]

```shell
# 进入软件目录
cd /usr/local/nginx/sbin
# 此方式停止步骤是等待 nginx 进程处理任务完毕进行停止
./nginx -s quit
```

## 重启nginx

### 先停止后启动 [建议使用]

```shell
./nginx -s quit
./nginx
```

### 重新加载配置文件

```shell
# 当 nginx 的配置文件 nginx.conf 修改后 
# 要想让配置生效 需要重启 nginx 
# 或者使用 -s reload 命令 
# 就可以在不重启服务器的情况下 使配置文件生效了
./nginx -s reload
```

## 更多配置

###  使用 nginx 作为静态资源服务器

+ 修改配置文件：

```shell
vi  /usr/local/nginx/conf/nginx.conf
```

+ 设置资源访问路径

```shell
user root; # 配置文件第一行设置 [user root;]
http{
	server {
         location /images/ {
            alias  /home/static/images/;
            index  index.html;
        }
	}
}

# 说明
# /images/                       # 访问路径
# /home/static/images/           # 真实文件存放路径
```

+ 重新加载配置文件
+ 
```shell
/usr/local/nginx/sbin/nginx -s reload
```

+ 将文件上传到 `/home/static/images/` 路径下，就可以通过 `http://{IP}/images/ + {filename}.{ext}` 地址访问静态资源了。

## 使用 nginx 实现反向代理

### 前提条件

既然想反向代理，就需要有被反向代理的服务器，此处以 tomcat 为例。

默认你已部署完成并启动了 tomcat 服务器。

如果没有，请先安装 tomcat 服务器。

+ 修改配置文件

```shell
vi  /usr/local/nginx/conf/nginx.conf
```

+ 设置反向代理配置
```shell
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass http://127.0.0.1:8080;
        index  index.html index.htm index.jsp;
    }
}
```

+ 重新加载配置文件

```shell
/usr/local/nginx/sbin/nginx -s reload
```

+ 测试  (注意不要忘记打开防火墙 80 端口)

```
浏览器输入 http://{ip地址} , 若显示 Tomcat的小猫 就代表已经启动成功了! 🎉
```

