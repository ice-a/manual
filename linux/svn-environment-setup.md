# Linux环境安装SVN

## 安装
执行如下安装命令
```SHELL
yum -y install subversion
```
## 配置
创建目录
```SHELL
# 存放数据
mkdir -p /usr/local/svn/data
# 存放授权配置
mkdir -p /usr/local/svn/conf
```
启动服务
```SHELL
# -d 守护进程形式
# -r 指定数据存放目录
# 更多配置 --help
svnserve -d -r /usr/local/svn/data
```

查看进程

```SHELL
ps -ef|grep svn
```

查看端口

```SHELL
# svn 默认端口 3690
netstat -lntup|grep 3690
lsof -i tcp:3690
lsof -i :3690
```

创建版本库 svnadmin

```SHELL
# 查看帮助
svnadmin help

# 查看创建版本库帮助
svnadmin help create

# 创建 <repo_ver1> 版本库
svnadmin create /usr/local/svn/data/repo_ver1

# 查看版本库结构
npm install -y tree # 安装 tree 命令
tree /usr/local/svn/data/repo_ver1
```

版本库配置管理

```SHELL
# 配置文件位置
cd /usr/local/svn/data/repo_ver1/conf

# 查看配置文件
ll

# 配置文件说明
# authz 授权文件
# passwd 密码文件
# svnserve.conf 主配置文件 包含 authz 和 passwd 文件

# 修改前备份配置文件
cp svnserve.conf svnserve.conf.ori

# 编辑配置文件
vim svnserve.conf

# 修改配置文件
# 不允许匿名访问
[# anno-access = read] -> [anno-access = none]
# 写操作要授权
[# auth-access = write] -> [auth-access = write]

# 打开用户密码数据库
# 每个库的密码配置到统一管理文件中
[# password-db = passwd] -> [password-db = /usr/local/svn/conf/passwd]

# 打开权限数据库
# 每个库的密码配置到统一管理文件中
[# authz-db = authz] -> [authz-db = /usr/local/svn/conf/authz]
```

检查变更内容

```SHELL
diff svnserve.conf svnserve.conf.ori
```

拷贝配置文件到统一管理配置目录

```SHELL
cp passwd authz /usr/local/svn/conf
```

修改权限

```SHELL
cd /usr/local/svn/conf/
chmod 700 *
```

添加用户

```SHELL
vim passwd
# 在 [users] 中插入一行， 格式: [用户名 = 密码]
caoshd = 123456
jenkins = 123456

# 保存退出
:x
```

添加权限

```SHELL
vim authz
# 新增用户组
[groups]
dev = tom,mike

# 配置指定仓库权限
[repo_ver1:/] # [版本库:/项目/目录]
cao.shd = rw  # 单独给用户读写权限
@dev = rw     # 给dev全组读写权限
* = r         # 其他用户 只读权限
```

提示

```SHELL
# 更改 svnserve.conf 文件，需要重启服务。
# 更改 passwd authz 文件，不需要重启服务。
```

重启svn服务

```SHELL
# 杀死服务
pkill svnserve
# 启动服务
svnserve -d -r /usr/local/svn/data
```

访问地址

```SHELL
# 使用 tortoiseSVN 工具访问 
svn://<hostname>/<repo_name>
```

## svn钩子

使用svn钩子可以在用户提交前或提交后进行一些操作

```SHELL
# 钩子位置 <版本库目录>/hooks
cd /usr/local/svn/data/repo_ver1/hooks

# 查看钩子模板 带有注释的比较常用
ll
-rw-r--r--. 1 root root 1977 Apr 15 23:42 post-commit.tmpl            # 提交后触发
-rw-r--r--. 1 root root 1638 Apr 15 23:42 post-lock.tmpl
-rw-r--r--. 1 root root 2289 Apr 15 23:42 post-revprop-change.tmpl    
-rw-r--r--. 1 root root 1567 Apr 15 23:42 post-unlock.tmpl
-rw-r--r--. 1 root root 3426 Apr 15 23:42 pre-commit.tmpl             # 提交前触发
-rw-r--r--. 1 root root 2434 Apr 15 23:42 pre-lock.tmpl
-rw-r--r--. 1 root root 2786 Apr 15 23:42 pre-revprop-change.tmpl
-rw-r--r--. 1 root root 2122 Apr 15 23:42 pre-unlock.tmpl
-rw-r--r--. 1 root root 2780 Apr 15 23:42 start-commit.tmpl
```

应用

+ 当客户端提交文件后 自动触发 jenkins 服务器执行构建命令 

```SHELL
cd /usr/local/svn/data/repo_ver1/hooks

# 使用钩子模板 提交后操作
cp post-commit.tmpl post-commit

# 写脚本
vim post-commit
# 将模板中的自带内容全部注释
# 添加如下触发命令

# -X 指定请求方式
# -v 显示命令
# -u 携带用户名和密码
# -H 携带请求头信息

curl -X post -v -u admin:123456 http://192.168.127.111/job/project-x/build?token=X_TOKEN

# 修改权限
chmod 755 post-commit
```
