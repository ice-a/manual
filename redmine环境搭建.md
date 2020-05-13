# Linux安装Redmine

**默认你已经安好了 ruby 环境和 mysql 环境**

## 下载 redmine

```
wget https://downloads.bitnami.com/files/stacks/redmine/4.1.1-1/bitnami-redmine-4.1.1-1-linux-x64-installer.run
```

## 授予执行权限

```
chmod +x bitnami-redmine-4.1.1-1-linux-x64-installer.run
```

## 运行安装命令

按照提示一路傻瓜式输入即可。

```
./bitnami-redmine-4.1.1-1-linux-x64-installer.run
```
## 配置
常用服务管理命令
```
# 进入安装目录
cd /usr/local/redmine

# 可用命令如下
./ctlscript.sh status                      # 查看运行状态
./ctlscript.sh start                       # 启动服务
./ctlscript.sh restart apache              # 重启指定名称的单个服务 如 apache 服务
./ctlscript.sh stop                        # 停止服务 
./ctlscript.sh restart                     # 重启全部服务
```
其他高级配置参考如下网站
```
https://docs.bitnami.com/general/apps/redmine/
```
