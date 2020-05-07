# gitlib环境搭建

## 下载gitlib安装包

```
wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6/gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm
```

## 安装gitlib

```
rpm -i gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm
```

## 修改gitlib配置

```
# 编辑指定配置文件
vi /etc/gitlab/gitlab.rb

# 修改外部访问地址
external_url 'http://{hostname}:[port]'
```

## 重载配置及启动gitlab
```
gitlab-ctl reconfigure
gitlab-ctl restart
```

## 配置防火墙

```
# 端口 {port} = 8000
firewall-cmd --zone=public --add-port={port}/tcp --permanent
firewall-cmd --reload
```

