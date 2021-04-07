# Linux环境安装Docker

## 查看系统环境

```shell
uname -r
```

```shell
cat /etc/os-release
```

## CentOS安装

卸载已经安装的旧版本的 docker

```shell
yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine
```

安装工具类

```shell
yum install -y yum-utils
```

配置镜像仓库(国内建议使用阿里提供的镜像)

```shell
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

更新 yum 软件包索引

```shell
yum makecache fast
```

安装 docker

```shell
yum install docker-ce docker-ce-cli containerd.io
```

启动 docker

```shell
systemctl start docker
```

验证启动结果

```shell
docker version
```

测试 hello world

```
docker run hello-world
```

查看已安装的 image 镜像

```
docker images
```

停止 docker

```
sudo systemctl stop docker
```

卸载 docker

```
# 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 移除资源
sudo rm -rf /var/lib/docker
```

## Ubuntu安装

环境准备
```bash
# 工具
sudo apt-get -y install \
  apt-transport-https \
  ca-certificates \
  curl

# https
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

仓库追加
```bash
# 添加仓库
sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"

# 更新仓库
sudo apt-get update
```

安装最新版
```bash
sudo apt-get -y install docker-ce
```

测试安装结果
```bash
sudo docker version
```

启动停止服务
```bash
sudo service docker start
sudo service docker stop
sudo service docker restart
```

## 常用命令

### 帮助命令

```shell
docker version           # 查看 docker 版本信息
docker info              # 显示 docker 系统信息, 包括镜像和容器的数量
docker <命令名> --help    # 帮助命令
```

### 镜像命令

+ 查看镜像

```shell
docker images      # 查看全部正在运行镜像

# 可选项
  -a, --all        # 列出全部镜像
  -q, --quiet      # 只显示镜像ID
```

+ 搜索镜像

```shell
docker search <镜像名称>   # 搜索指定名称的镜像

# 可选项
  --filter=STARS=3000    # 列出收藏数大于3000的镜像

# 测试
docker search mysql --filter=STARS=3000

NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9574                [OK]
mariadb             MariaDB is a community-developed fork of MyS…   3478                [OK]
```

+ 下载镜像

```shell
docker pull <镜像名称:[tag]>          # 下载指定名称的镜像

# 说明
# 如果不写 tag, 默认使用 latest (最新版)
docker pull mysql
latest: Pulling from library/mysql
afb6ec6fdc1c: Pull complete         # 分层下载 联合文件系统
0bdc5971ba40: Pull complete
97ae94a2c729: Pull complete
f777521d340e: Pull complete
1393ff7fc871: Pull complete
a499b89994d9: Pull complete
7ebe8eefbafe: Pull complete
597069368ef1: Pull complete
ce39a5501878: Pull complete
7d545bca14bf: Pull complete
211e5bb2ae7b: Pull complete
5914e537c077: Pull complete
Digest: sha256:a31a277d8d39450220c722c1302a345c84206e7fd4cdb619e7face046e89031d  # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest     # 真实地址

# 使用 tag
docker pull mysql:5.7

# 查看已下载的镜像
docker images
```

+ 删除镜像

```shell
docker rmi -f <镜像ID>                                # 删除指定镜像
docker rmi -f <镜像id1> <镜像id2>                      # 删除多个指定镜像
docker rmi -f $(docker images -qa)                    # 删除全部镜像
```

### 容器命令

+ 新建容器并启动

```shell
docker run [可选参数] image

# 参数说明
--name="<NAME>"             # 容器的名字 用于区分容器 自定义
-d                          # 以后台方式运行
-it                         # 使用交互方式运行, 进入容器查看内容
-p                          # 指定容器端口映射
    -p ip:主机端口: 映射端口  # 主机ip端口映射容器端口
    (-p 主机端口: 映射端口)   # 主机端口映射容器端口 括号里的内容可多次出现 定义多个映射端口规则
    -p 容器端口             # 仅指定容器端口
    容器端口                # 仅指定容器端口
-P                          # 随机指定端口

# 测试 启动并进入容器
docker run -it centos /bin/bash

# 显示如下
[root@bf9dff6cc742 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 退出 (容器会停止)
exit
```

+ 列出所有运行的容器

```shell
docker ps                          # 列出当前正在运行的容器

# 参数说明
    -a                             # 列出全部运行过的容器
    -n=10                          # 列出最近十条运行容器
    -q                             # 只显示容器编号
```

+ 退出容器

```shell
exit                             # 容器停止并退出
ctrl + P + Q                     # 退出不停止容器
```

+ 删除容器

```shell
docker rm <容器ID>              # 删除指定容器 (不能删除正在运行容器， -f: 强制删除)
docker rm $(docker ps -aq)     # 删除全部容器
```

+ 启动和停止容器

```shell
docker start <容器ID>       # 启动容器
docker restart <容器ID>     # 重启容器
docker stop <容器ID>        # 停止容器
docker kill <容器ID>        # 强制关闭容器
```

+ 导出**容器**
```bash
docker export <容器ID> > <容器名称>.tar
# 例如
docker export tomcat > tomcat.tar
```

+ 导入**容器**
```bash
docker import - <容器名称> < <容器全路径.tar>

# 例如
docker import - tomcat < tomcat.tar
```

+ 保存**镜像**
```bash
# 保存单个镜像
docker save <镜像ID> > <镜像名称>.tar

# 保存多个镜像
docker save -o <镜像名称>.tar <镜像ID2> <镜像ID2>
```

+ 载入**镜像**
```bash
docker load < <镜像名称>.tar
```

> 导入导出和保存载入的区别
> 1. 文件大小不同
>     export 导出的镜像文件体积小于 save 保存的镜像
>
> 2. 是否可以对镜像重命名
>     docker import 可以为镜像指定新名称
>     docker load 不能对载入的镜像重命名
>
> 3. 是否可以同时将多个镜像打包到一个文件中
>     docker export 不支持
>     docker save 支持
>
> 4. 是否包含镜像历史
>     export 导出（import 导入）是根据容器拿到的镜像，再导入时会丢失镜像所有的历史记录和元数据信息（即仅保存容器当时的快照状态），所以无法进行回滚操作。
>     而 save 保存（load 加载）的镜像，没有丢失镜像的历史，可以回滚到之前的层（layer）。
>
> 5. 应用场景不同
>
>   + **export 应用场景** 
>     从一个 ubuntu 镜像启动一个容器，然后安装一些软件并进行一些设置后，使用 docker export 保存为一个基础镜像。然后把这个镜像分发给其他人使用，作为基础的开发环境。
>
>   + **save 的应用场景** 
>     应用使用 docker-compose.yml 编排的多个镜像的组合，但是要部署的客户服务器并不能连外网。这时就可以使用 docker save 将用到的镜像打个包，然后拷贝到客户服务器上使用 docker load 载入。
>
### 其他命令

+ 后台启动容器

```shell
docker run -d 镜像名

# 测试
docker run id centos

# 现象
# centos 停止了

# 原因
# docker 容器使用后台运行必须要有一个前台进程 否则 docker 发现没有应用 就会自动停止容器

# 常见坑
nginx 容器启动后 发现没有提供服务 自动就停止。
```

+ 查看日志

```shell
docker logs -tf --tail 10 <容器ID>    查看指定容器最近十条日志

# 测试
# 编写测试脚本
docker run -d centos /bin/sh -c "while true; do echo helloworld; sleep 1; done"

# 查看正在运行容器
docker ps
# 显示如下
5583aeb20df7

# 查看容器日志
docker logs -tf --tail 10 5583aeb20df7
```

+ 查看容器内部进程信息

```shell
docker top <容器ID>

# 测试
docker top bf9dff6cc742

# 显示如下
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                32139               32123               0                   09:11               pts/0               00:00:00            /bin/bash
```

+ 查看镜像元数据

```shell
docker inspect bf9dff6cc742

# 显示如下
[
    {
        "Id": "sha256:d1165f2212346b2bab48cb01c1e39ee8ad1be46b87873d9ca7a4e434980a7726",
        "RepoTags": [
            "hello-world:latest"
        ],
        "RepoDigests": [
            "hello-world@sha256:308866a43596e83578c7dfa15e27a73011bdd402185a84c5cd7f32a88b501a24"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-03-05T23:25:25.230064203Z",
        "Container": "f5a78ef54769bb8490754e9e063a89f90cc8eee6a6c5a0a72655826e99df116e",
        "ContainerConfig": {
            "Hostname": "f5a78ef54769",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/hello\"]"
            ],
            "Image": "sha256:77fe0a37fa6ce641a004815f2761a9042618557d253f312cd3da61780e372c8f",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "19.03.12",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/hello"
            ],
            "Image": "sha256:77fe0a37fa6ce641a004815f2761a9042618557d253f312cd3da61780e372c8f",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 13336,
        "VirtualSize": 13336,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/4affb9589217caf7b23527f0fa4cfe1560a851766476c9e4df3e3a1f3d55ca9c/merged",
                "UpperDir": "/var/lib/docker/overlay2/4affb9589217caf7b23527f0fa4cfe1560a851766476c9e4df3e3a1f3d55ca9c/diff",
                "WorkDir": "/var/lib/docker/overlay2/4affb9589217caf7b23527f0fa4cfe1560a851766476c9e4df3e3a1f3d55ca9c/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:f22b99068db93900abe17f7f5e09ec775c2826ecfe9db961fea68293744144bd"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

+ 进入当前正在运行的容器

```shell
# 方法一
docker exec -it <容器ID> /bin/bash

# 测试
[root@localhost ~]# docker exec -it bf9dff6cc742 /bin/bash
[root@bf9dff6cc742 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 方法二

docker attach <容器ID>

# 测试
docker attach bf9dff6cc742

# 显示如下
[root@localhost ~]# docker attach bf9dff6cc742
[root@bf9dff6cc742 /]# exit   # 因为内部没有任何进程 自动退出了
[root@localhost ~]# docker ps
```

+ 拷贝容器内部文件

```shell
docker cp <容器ID>:<容器内路径>  目的地主机路径

# 测试
# 查看 /home 路径文件
[root@localhost ~]# ls /home
caoshd

# 进入容器
docker run -it centos

# 在容器内创建文件
cd /home
touch test.txt

# 退出容器 (容器停止了)
exit

# 查看容器ID
docker ps -a                      # -a 查看全部 包括已停止
d756bc697d26

# 执行 copy
docker cp d756bc697d26:/home/test.txt /home

# 查看 /home 路径文件
[root@localhost ~]# ls /home
caoshd  test.txt
```

## 网络配置

网络类型
* Bridge： 桥接模式， 需要配置网络映射（默认使用）
* Host： 与主机共用网络
* None： 仅容器内部网络

指定端口映射
```bash
dokcer run -d -p <HOST_IP>:<CONTAINER_IP>
```

随机端口映射
```bash
dokcer run -d -P
```

## 运行Nginx

获取镜像
```bash
sudo dokcer pull nginx
```

新建容器并后台运行
```bash
sudo docker run -d -p 8080:80 nginx
```

查看正在运行容器
```bash
docker ps
```

查看端口信息
```bash
netstat -na|grep 8080

# 显示如下
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:8080          127.0.0.1:37780         TIME_WAIT
tcp        0      0 127.0.0.1:8080          127.0.0.1:37776         TIME_WAIT
```
访问容器： [Nginx欢迎页](http://localhost:8080/)

## 制作docker镜像

* Dockerfile 用于制作镜像的配置文件

生成文件名为 `Dockerfile` 的文件
```bash
# 以官网中央仓库的tomcat作为基础版 生成自己的镜像
from tomcat

# 配置管理者信息及联系方式
MAINTAINER caoshd cao-shd@outlook.com

# 复制要发布的 war 包到 tomcat 发布目录
COPY xbot.war /usr/local/tomcat/webapps
```

制作镜像命令
```bash
docker build -t <image_name>:<image_version> <Dockerfile_path>

# 例如
docker build -t xbot:latest .
```

运行自己的容器
```bash
docker -run -d -p 80:8080 xbot
```