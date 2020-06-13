# Linux环境安装Docker

## 查看系统环境

```shell
uname -r
```

```shell
cat /etc/os-release
```

## 安装

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

## 常用命令

### 帮助命令

```shell
docker version           # 查看 docker 版本信息
docker info              # 显示 docker 系统信息, 包括镜像和容器的数量
docker {命令名} --help    # 帮助命令
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
docker search {镜像名称}   # 搜索指定名称的镜像

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
docker pull {镜像名称:[tag]}          # 下载指定名称的镜像

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
docker rmi -f {镜像ID}                                # 删除指定镜像
docker rmi -f {镜像id1} {镜像id2}                     # 删除多个指定镜像
docker rmi -f $(docker images -qa)                   # 删除全部镜像
```

### 容器命令

+ 新建容器并启动

```shell
docker run [可选参数] image

# 参数说明
--name="{NAME}"             # 容器的名字 用于区分容器 自定义
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
docker rm {容器ID}              # 删除指定容器 (不能删除正在运行容器， -f: 强制删除)
docker rm $(docker ps -aq)     # 删除全部容器
```

+ 启动和停止容器

```shell
docker start {容器ID}       # 启动容器
docker restart {容器ID}     # 重启容器
docker stop {容器ID}        # 停止容器
docker kill {容器ID}        # 强制关闭容器
```

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
docker logs -tf --tail 10 {容器ID}    查看指定容器最近十条日志

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

+ 查看容器中进程信息

```shell
docker top {容器ID}

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
        "Id": "bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d",
        "Created": "2020-06-03T12:46:16.26997503Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 32139,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-06-03T12:46:16.863175288Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:470671670cac686c7cf0081e0b37da2e9f4f768ddc5f6a26102ccd1c6954c1ee",
        "ResolvConfPath": "/var/lib/docker/containers/bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d/hostname",
        "HostsPath": "/var/lib/docker/containers/bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d/hosts",
        "LogPath": "/var/lib/docker/containers/bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d/bf9dff6cc7423fe3cd324984e4b430fc3de0835d45e3430c94144638269e749d-json.log",
        "Name": "/sad_meitner",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/e58aa0f86986d9c3c2728dfb5fc19bdbde426a795f0ee8e57a1ea5648ad8abbe-init/diff:/var/lib/docker/overlay2/e561ea776190156cb514d1c153fd6ca75376aabb5de3557dd9aa389629374af6/diff",
                "MergedDir": "/var/lib/docker/overlay2/e58aa0f86986d9c3c2728dfb5fc19bdbde426a795f0ee8e57a1ea5648ad8abbe/merged",
                "UpperDir": "/var/lib/docker/overlay2/e58aa0f86986d9c3c2728dfb5fc19bdbde426a795f0ee8e57a1ea5648ad8abbe/diff",
                "WorkDir": "/var/lib/docker/overlay2/e58aa0f86986d9c3c2728dfb5fc19bdbde426a795f0ee8e57a1ea5648ad8abbe/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "bf9dff6cc742",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200114",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-01-14 00:00:00-08:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "23131d1d63e7ec80202de807913bb5d6418e10e8725bc6e15ac14a2763377e41",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/23131d1d63e7",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "e79e04e9efa2074ee9140a396f5b6f4082cbab9db13bf09a243335c85ce61a09",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "5b2b54f4ff34fb6ebd86fe48b467d715a20eb17344ed6a38184d505bfc13a3d9",
                    "EndpointID": "e79e04e9efa2074ee9140a396f5b6f4082cbab9db13bf09a243335c85ce61a09",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

+ 进入当前正在运行的容器

```shell
# 方法一
docker exec -it {容器ID} /bin/bash

# 测试
[root@localhost ~]# docker exec -it bf9dff6cc742 /bin/bash
[root@bf9dff6cc742 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 方法二

docker attach {容器ID}

# 测试
docker attach bf9dff6cc742

# 显示如下
[root@localhost ~]# docker attach bf9dff6cc742
[root@bf9dff6cc742 /]# exit   # 因为内部没有任何进程 自动退出了
[root@localhost ~]# docker ps
```

+ 拷贝容器内部文件

```shell
docker cp {容器ID}:{容器内路径}  目的地主机路径

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

