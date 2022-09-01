---
typora-copy-images-to: 2019-09-26-centos7-environment
---

|Property | Content |
|-- | -- |
|Id | 5addfd8b-c105-4327-b897-f9c709a19690 |
|Name | 2019-09-26-centos7-environment.md |
|ArticleLink | https://blog.spiritling.cn/articles/5addfd8b-c105-4327-b897-f9c709a19690 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/1 |
| Gitee Issue | https://gitee.com/SpiritLing/Comments/issues/I4VSHZ |

# Centos7 安装需要的软件环境（mysql, nodejs, nginx, jdk, Jenkins, Git）

## Mysql 安装

### 下载安装

1. 下载并安装 MySQL 官方的 Yum Repository

```bash
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

2. Yum 安装源

```bash
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

3. 安装 Mysql

```bash
yum -y install mysql-community-server
```

4. 设置开机 Mysql

```bash
systemctl enable mysqld.service
```

5 启动 Mysql

```bash
service mysqld start
```

### Mysql 设置

1. 访问 Mysql 之前需要先查看默认密码

```bash
grep "password" /var/log/mysqld.log
```

2. 登录 Mysql

```bash
mysql -u root -p
```

然后输入刚才查到的密码，密码不可见隐藏显示。

3. 修改密码策略

```bash
> show VARIABLES like "%password%"
+---------------------------------------+---------+
| Variable_name                         | Value   |
|---------------------------------------+---------|
| default_password_lifetime             | 0       |
| disconnect_on_expired_password        | ON      |
| log_builtin_as_identified_by_password | OFF     |
| mysql_native_password_proxy_users     | OFF     |
| old_passwords                         | 0       |
| report_password                       |         |
| sha256_password_proxy_users           | OFF     |
| validate_password_dictionary_file     |         |
| validate_password_length              | 8       |
| validate_password_mixed_case_count    | 1       |
| validate_password_number_count        | 1       |
| validate_password_policy              | MEDIUM  |
| validate_password_special_char_count  | 1       |
+---------------------------------------+---------+
```

介绍几个主要的的参数说明：

| 参数                                 | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| validate_password_number_count       | 参数是密码中至少含有的数字个数，当密码策略是 MEDIUM 或以上时生效。 |
| validate_password_special_char_count | 参数是密码中非英文数字等特殊字符的个数，当密码策略是 MEDIUM 或以上时生效。 |
| validate_password_mixed_case_count   | 参数是密码中英文字符大小写的个数，当密码策略是 MEDIUM 或以上时生效。 |
| validate_password_length             | 参数是密码的长度，这个参数由下面的公式生成。                 |

```sql
# 更改密码长度
set global validate_password_length=0;

# 更改数字个数
set global validate_password_number_count=0;

# 更改大小写字母个数
set global validate_password_mixed_case_count=0;

# 更改特殊字符个数
set global validate_password_special_char_count=0;
```

4. 修改密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Your New Pssword';
```

5. 开启远程访问

```sql
grant all privileges on *.* to 'root'@'You IP or ALL %' identified by 'Your Password' with grant option;
# 刷新权限
flush privileges;
```

6.  配置默认字符

在 `my.cnf`（`/etc/my.cnf`） 或者 `my.ini` 文件中

在 `my.cnf` 配置中插入下面语句

```
[client]
default-character-set=utf8
```

一定要在 `[mysqld]` 之前插入这两句，否则就会出现下面报错

```bash
mysql: [ERROR] unknown variable 'datadir=/var/lib/mysql'
```

主要原因就是`[client]`的配置信息，放在了`[mysqld]`配置信息的中间，导致其他`[mysqld]`的配置都归在`[client]`下。

在 `socket` 之后插入下面两行

```
character-set-server=utf8
collation-server=utf8_general_ci
```

7. 事务隔离（可选：`confluence 安装需要设置`）

[Mysql 四种事务隔离](https://www.cnblogs.com/shihaiming/p/11044740.html)

在 `my.cnf` 或者 `my.ini` 文件中

```sql
transaction_isolation = READ-COMMITTED
```

<blockquote class="blockquote-center">做完上面的可以重启下Mysql服务。</blockquote>

```
service mysqld restart
```

## 使用 `rpm` 安装 `JDK`

### 下载

先通过[官网](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下载如下图标注的红色文件

![jdk 下载](https://archive.static.spiritling.net/images/20190802195857.png)

现在下载 jdk 需要登录 oracle 账户才可以下载

在此特别贡献一个账号密码，千万不要修改密码

```
username：oracle@spiritling.uu.me
password：NkzKx!5fsx5Tj4@
```

### 传输

将下载的文件通过[FileZilla](https://filezilla-project.org/)软件上传到服务器

### 安装

使用 `rpm` 命令安装

```bash
rpm -ivh jdk-8u152-linux-x64.rpm
```

![jdk 下载](https://archive.static.spiritling.net/images/20190802200348.png)

## Nginx 下载安装

### 添加 `yum` 源

```bash
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

安装完 `yum` 源之后，可以使用下面命令查看

```
yum repolist
```

### 安装

```bash
yum install nginx
```

### 配置服务

设置开机启动

```bash
systemctl enable nginx
```

启动服务

```bash
service nginx start
```

### nginx 命令

```
nginx # 打开 nginx
nginx -t # 测试配置文件是否有语法错误
nginx -s reopen # 重启Nginx
nginx -s reload # 重新加载Nginx配置文件，然后以优雅的方式重启Nginx
nginx -s stop # 强制停止Nginx服务
nginx -s quit # 优雅地停止Nginx服务（即处理完所有请求后再停止服务）
```

```
nginx [-?hvVtq][-s signal] [-c filename][-p prefix] [-g directives]

-?,-h           : 打开帮助信息
-v              : 显示版本信息并退出
-V              : 显示版本和配置选项信息，然后退出
-t              : 检测配置文件是否有语法错误，然后退出
-q              : 在检测配置文件期间屏蔽非错误信息
-s signal       : 给一个 nginx 主进程发送信号：stop（强制停止）, quit（优雅退出）, reopen（重启）, reload（重新加载配置文件）
-p prefix       : 设置前缀路径（默认是：/usr/share/nginx/）
-c filename     : 设置配置文件（默认是：/etc/nginx/nginx.conf）
-g directives   : 设置配置文件外的全局指令
```

## 安装 NodeJS

### 通过 `yum` 安装 nodejs

#### 更新 nodejs 各种版本 `yum` 源

-   Nodejs v10.x 安装命令

```bash
$ curl --silent --location https://rpm.nodesource.com/setup_10.x | bash -
```

-   Nodejs v8.x 安装命令

```bash
$ curl --silent --location https://rpm.nodesource.com/setup_8.x | bash -
```

其他版本如上所示

#### 直接安装

```bash
yum install nodejs -y
```

### 通过压缩包安装

首先需要去官网下载对应的安装包

选择全部镜像 > 阿里云镜像

找到 `node-v12.10.0-linux-x64.tar.gz` 形似这个文件名，具体版本号会发生变化

```bash
wget https://npm.taobao.org/mirrors/node/v12.10.0/node-v12.10.0-linux-x64.tar.gz
```

下载完毕后解压到指定目录

```bash
tar xf node-v12.10.0-linux-x64.tar.gz -C /usr/local/
```

重命名文件夹

```bash
cd /usr/local/
mv node-v12.10.0-linux-x64/ nodejs
```

设置全局命令

```bash
ln -s /usr/local/nodejs/bin/node /usr/local/bin

ln -s /usr/local/nodejs/bin/npm /usr/local/bin
```

然后就可以愉快的使用了

## Jenkins 安装

### 官方安装

[官方链接 RPM 安装](https://pkg.jenkins.io/redhat/)

在正式安装之前，需要先安装好 `java` 环境

安装源

```shell
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
```

导入 key

```shell
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
```

安装 `jenkins`

```shell
yum install jenkins
```

修改端口

```
vim /etc/sysconfig/jenkins
```

找到 `JENKINS_PORT="8080"` 修改为 `JENKINS_PORT="你需要的端口"`

如果你需要在自动化构建中运行 `root` 权限的 shell ，那么还需要修改上面文件中

```
JENKINS_USER="root"
```

进行重启服务

```
systemctl restart jenkins.service
```

启动访问后，提示从 `/var/lib/jenkins/secrets/initialAdminPassword` 获取密码

登录后进行其他操作

当使用 http 访问时，插件按装时会出现一些失败，所以需要先进入以下链接

```
HOST/pluginManager/advanced
```

修改最底下的升级站点

```
http://updates.jenkins.io/update-center.json
```

也可以使用国内清华源

```
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```

然后安装插件，添加管理员即可使用

### Docker 镜像安装

直接看[官网安装](https://jenkins.io/zh/doc/book/installing/#docker)，需要基本的 Docker 操作知识。

这里附带一份我自己使用 Docker 安装 Jenkins 的 shell 命令

```shell
docker run \
-u root \
--name=jenkins_blueocean \
-d \
--restart=always \
-p 10001:8080 \
-p 50000:50000 \
-v /var/docker-data/jenkins/home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
jenkinsci/blueocean
```

如果需要配合 nginx 使用，则可以在 nginx 配置中添加下面的代码

```
location / {
 proxy_pass http://localhost:10001;

    proxy_redirect  off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

## Git 安装新版

### 下载编辑工具

```
yum -y groupinstall "Development Tools"
```

### 下载依赖包

```
yum -y install zlib-devel perl-ExtUtils-MakeMaker asciidoc xmlto openssl-devel
```

### 删除自带的 git

安装依赖时，yum 自动安装了 Git，需要卸载旧版本 Git，命令为：

```
yum remove git
```

### 下载 `git` 最新版本的源代码

#### 去 GitHub 网站直接下载发布版

[Github Release 版本](https://github.com/git/git/releases)

[Gitee Release 版本](https://gitee.com/SpiritLing/git/releases)

当然在国内下载 github 的发布版可能会很慢，所以可以推荐去官网下载

#### 官网下载

[git 官网](https://git-scm.com/) 点击 `Downloads` ，进入下载页面

点击 `Linux/Unix` 下载 Linux 版本的 git

拉到最下方，点击[download a tarball](https://mirrors.edge.kernel.org/pub/software/scm/git/) 调转到 git 压缩包压在页面，选择你需要的版本进行下载

### 解压 git 压缩包

```
tar -zxvf git-2.30.0.tar.gz
```

### 进入 git 目录，配置安装路径

```
cd git-2.13.3
# make configure  只在源码安装情况下使用，release版本下
./configure --prefix=/usr/local/git
```

### 安装

```
make && make install
```

### 配置全局路径

```
export PATH="/usr/local/git/bin:$PATH"
source /etc/profile
```

### 查看 git 版本

```
git --version
```

### 配置软连接

在有些系统或者软件中有可能会使用 git 默认地址，所以上面的配置也许有可能无法访问到 git 命令，所以需要添加软连接，添加到你所需要的地方

```
ln -s /usr/local/git/bin/git /usr/bin/git
```

前方是自己安装的位置，后面是需要软连接到需要的位置。

至于加不加 `-s` ，可以看这里 [Linux Ln 命令](https://man.linuxde.net/ln)

{% centerquote %} 只记录自己曾经使用过的方式，如果有其他更好的，欢迎留言! {% endcenterquote %}

## 安装 Docker

> 警告：切勿在没有配置 Docker YUM 源的情况下直接使用 yum 命令安装 Docker.

### 准备工作

#### 系统要求

Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10。 CentOS 7 满足最低内核的要求，但由于内核版本比较低，部分功能（如 `overlay2` 存储层驱动）无法使用，并且部分功能可能不太稳定。

#### 卸载旧版本

旧版本的 Docker 称为 `docker` 或者 `docker-engine`，使用以下命令卸载旧版本：

```bash
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-selinux \
                docker-engine-selinux \
                docker-engine
```

### 使用 yum 安装

执行以下命令安装依赖包：

```bash
sudo yum install -y yum-utils \
        device-mapper-persistent-data \
        lvm2
```

鉴于国内网络问题，强烈建议使用国内源，官方源请在注释中查看。

执行下面的命令添加 `yum` 软件源：

```bash
sudo yum-config-manager \
  --add-repo \
  https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

官方源

```
sudo yum-config-manager \
  --add-repo \
  https://download.docker.com/linux/centos/docker-ce.repo
```

如果需要测试版本的 Docker CE 请使用以下命令：

```bash
sudo yum-config-manager --enable docker-ce-test
```

如果需要每日构建版本的 Docker CE 请使用以下命令：

```bash
sudo yum-config-manager --enable docker-ce-nightly
```

#### 安装 Docker CE

> 在 centos8 中需要在安装 `docker-ce` 之前安装依赖

```shell
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
```

更新 `yum` 软件源缓存，并安装 `docker-ce`。

```bash
sudo yum makecache fast
sudo yum install -y docker-ce
```

### 使用脚本自动安装

在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，CentOS 系统上可以使用这套脚本安装，另外可以通过 `--mirror` 选项使用国内源进行安装：

```bash
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
# $ sudo sh get-docker.sh --mirror AzureChinaCloud
```

执行这个命令后，脚本就会自动的将一切准备工作做好，并且把 Docker CE 的稳定\(stable\)版本安装在系统中。

### 启动 Docker CE

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

### 建立 docker 用户组

默认情况下，`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。

建立 `docker` 组：

```bash
sudo groupadd docker
```

将当前用户加入 `docker` 组：

```bash
sudo usermod -aG docker $USER
```

退出当前终端并重新登录，进行如下测试。

### 测试 Docker 是否安装正确

```bash
$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

若能正常输出以上信息，则说明安装成功。

### 镜像加速

如果在使用过程中发现拉取 Docker 镜像十分缓慢，可以配置 Docker [国内镜像加速](#配置镜像加速)。

### 添加内核参数

如果在 CentOS 使用 Docker CE 看到下面的这些警告信息：

```bash
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
```

请添加内核配置参数以启用这些功能。

```bash
$ sudo tee -a /etc/sysctl.conf <<-EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

然后重新加载 `sysctl.conf` 即可

```bash
sudo sysctl -p
```

### 参考文档

-   [Docker 官方 CentOS 安装文档](https://docs.docker.com/install/linux/docker-ce/centos/)。

### 配置镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。国内很多云服务商都提供了国内加速器服务，例如：

-   [Azure 中国镜像 `https://dockerhub.azk8s.cn`](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)
-   [阿里云加速器\(需登录账号获取\)](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
-   [七牛云加速器 `https://reg-mirror.qiniu.com`](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)

> 由于镜像服务可能出现宕机，建议同时配置多个镜像。
>
> 国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务，具体请参考官方文档。

本节我们以 Azure 中国镜像 `https://dockerhub.azk8s.cn` 为例进行介绍。

#### Ubuntu 16.04+、Debian 8+、CentOS 7

对于使用 [systemd](https://www.freedesktop.org/wiki/Software/systemd/) 的系统，请在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）

```json
{
    "registry-mirrors": [
        "https://dockerhub.azk8s.cn",
        "https://reg-mirror.qiniu.com"
    ]
}
```

> 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。

之后重新启动服务。

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

> 注意：如果您之前查看旧教程，修改了 `docker.service` 文件内容，请去掉您添加的内容（`--registry-mirror=https://dockerhub.azk8s.cn`）。

#### Windows 10

对于使用 `Windows 10` 的用户，在任务栏托盘 Docker 图标内右键菜单选择 `Settings`，打开配置窗口后在左侧导航菜单选择 `Docker Engine`，在右侧像下边一样编辑 json 文件，之后点击 `Apply & Restart` 保存后 Docker 就会重启并应用配置的镜像地址了。

```json
{
    "registry-mirrors": [
        "https://dockerhub.azk8s.cn",
        "https://reg-mirror.qiniu.com"
    ]
}
```

#### macOS

对于使用 macOS 的用户，在任务栏点击 Docker Desktop 应用图标 -&gt; `Perferences`，在左侧导航菜单选择 `Docker Engine`，在右侧像下边一样编辑 json 文件。修改完成之后，点击 `Apply & Restart` 按钮，Docker 就会重启并应用配置的镜像地址了。

```json
{
    "registry-mirrors": [
        "https://dockerhub.azk8s.cn",
        "https://reg-mirror.qiniu.com"
    ]
}
```

#### 检查加速器是否生效

执行 `$ docker info`，如果从结果中看到了如下内容，说明配置成功。

```bash
Registry Mirrors:
 https://dockerhub.azk8s.cn/
```

![关注公众号](https://static.spiritling.pub/images-PZagGrHhrXppbn4S/share/2020-06-01/weixin-gongzhongh-saoma-sousuo.png)
