---
title: laradock下部署Notadd环境
---

{{< note title="划重点" >}}
熟练使用docker，能够在10分钟内部署好一套PHP环境
{{< /note >}}

## 为什么使用Docker？

### 部署快
只需要几分钟，就能部署好一套PHP环境

### 性能好
Docker 的性能损失只有1-2%，几乎可以忽略不计。

### 安全性高
容器与宿主机完全隔离，默认情况下不能相互访问。

### 同时支持多版本软件
可以PHP多版本共存

### 教程目的
如何用laradock 在10分钟内 部署 Notadd 与 laradock 环境

## 开始安装Docker

### Linux (先执行这步)

```
curl -sSL https://get.docker.com | sh
```
#### 安装Docker

Centos7 请执行这步
```
yum install -y docker-engine 
```

ubuntu 请执行这步

```
sudo apt-get install -y -q docker-engine
```

{{< note title="如果安装缓慢" >}}
建议使用清华大学镜像源
https://mirror.tuna.tsinghua.edu.cn/help/docker/
{{< /note >}}


`systemctl enable docker`  # docker 开机启动
`systemctl start docker` # 启动docker

#### 安装Docker-compose （ubuntu 请注意权限问题）
```
curl -L https://get.daocloud.io/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
#### 开启国内镜像加速

```
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s https://registry.docker-cn.com
```

{{< note title="如果系统不支持以上命令，且docker版本在1.13以上" >}}

`nano /etc/docker/daemon.json` 编辑文件，也可以使用vi

```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```
{{< /note >}}




### Windows10（64位）

下载安装
https://get.daocloud.io/docker-install/windows

#### 开启国内镜像加速

在桌面右下角状态栏中右键 Docker 图标，修改在 Docker Daemon 标签页中的 json ，把下面的地址:
```
https://registry.docker-cn.com
```
加到"registry-mirrors"的数组里。点击 Apply 。

### Mac 10.8+

下载安装
https://get.daocloud.io/docker-install/mac

#### 开启国内镜像加速

右键点击桌面顶栏的 docker 图标，选择 Preferences ，在 Daemon 标签（Docker 17.03 之前版本为 Advanced 标签）下的 Registry mirrors 列表中加入下面的镜像地址:

```
https://registry.docker-cn.com
```
点击 Apply & Restart 按钮使设置生效。

## 安装 laradock 与 Notadd
### 下载laradock 与 Notadd 

请确保git 可用 (win 建议在Powershell下执行)

```
git clone https://github.com/Laradock/laradock.git
mkdir -p wwwroot/data  # 创建网站目录
cd wwwroot 
mkdir public   #创建用于 HTTP服务软件的公共目录
git clone https://github.com/notadd/notadd.git
cd .. # 返回到上级目录

```


Linux： (win 和 mac 请直接编辑`env-example` 文件)

```
cd laradock

vi env-example
```

### env-example 配置说明
```
APPLICATION=../wwwroot

DATA_SAVE_PATH=../wwwroot/data
```

#### WORKSPACE 配置项

视情况开启
```
NODE=true
YARN=true
```
#### PHP_FPM配置说明

```
PHP_FPM_INSTALL_XDEBUG=false
PHP_FPM_INSTALL_MONGO=false
PHP_FPM_INSTALL_MSSQL=false
PHP_FPM_INSTALL_SOAP=false
PHP_FPM_INSTALL_ZIP_ARCHIVE=true
PHP_FPM_INSTALL_BCMATH=true
PHP_FPM_INSTALL_PHPREDIS=true
PHP_FPM_INSTALL_MEMCACHED=false
PHP_FPM_INSTALL_OPCACHE=false
PHP_FPM_INSTALL_EXIF=true
PHP_FPM_INSTALL_AEROSPIKE=false
PHP_FPM_INSTALL_MYSQLI=false
PHP_FPM_INSTALL_TOKENIZER=false
PHP_FPM_INSTALL_INTL=false
PHP_FPM_INSTALL_GHOSTSCRIPT=false
PHP_FPM_INSTALL_LDAP=false
PHP_FPM_INSTALL_SWOOLE=false
```
线上环境请将 `PHP_FPM_INSTALL_OPCACHE=true`

下面是数据库默认的账号和密码，请根据需要自行修改，不再阐述。

更改完毕后请务必进行此操作：

```
cp env-example .env
```
复制环境变量文件。

### 更改Caddy 配置
Caddy 是一个高性能，且使用很简单的HTTP服务器，自带HTTPS证书。

```
cd caddy
vi Caddyfile
```
更改为如下配置：

```
# Docs: https://caddyserver.com/docs/caddyfile
0.0.0.0:80 {
        root /var/www/notadd/public
        fastcgi / php-fpm:9000 php {
                index index.php
        }

        # To handle .html extensions with laravel change ext to
        # ext / .html

        rewrite {
                r .*
                ext /
                to /index.php?{query}
        }
        gzip
        browse
        log /var/log/caddy/access.log
        errors /var/log/caddy/error.log
}
```
{{< note title="容器和宿主机无法相互访问" >}}
必须通过端口映射或者目录映射的方式
{{< /note >}}

之前的设置映射了目录：
APPLICATION=../wwwroot ，所以 wwwroot目录 会对应容器的/var/www 目录

完成后请：
```
cd .. # 进入laradock 根目录
```

### 启动laradock

可以根据自己需要自行启动 nginx/apache/mysql/phpmyadmin/redis 等   

注：`phpmyadmin` 请访问 http://IP:88  `pgadmin`请访问 http://IP:5050   

```
docker-compose up caddy postgres pgadmin 
```
第一次运行需要安装环境，需要比较久的时间，请耐心等待

{{< note title="安装notadd需进入workspace" >}}
参见 laradock常用操作——工作空间相关说明
{{< /note >}}

## laradock 常用操作

以下操作请确保在laradock 根目录下
### 启动相关
laradock 默认会启动 php-fpm 和 workspace ，所以参数中无需加这两个。

启动 caddy 和 postgresql
 
```
docker-compose up caddy postgres
```
后台启动
```
docker-compose up -d caddy postgres
```
只重启caddy （比如修改了配置文件）
```
docker-compose restart caddy
```

停止所有
```
docker-compose stop
```


### 工作空间

{{< warning title="请先确认环境已经启动" >}}
在宿主机下无法直接使用php/composer 等命令，必须进入工作空间
{{< /warning >}}


```
docker-compose exec workspace bash
```
会进入 `/var/www` 目录

此时 可以执行`composer` 和`PHP`命令。

如果之前`env-example` 开启了`node`和`yarn` 也可执行对应命令。
#### 安装notadd
```
cd notadd
composer install
php notadd vendor:publish --force
```
#### 退出工作空间
```
exit
```
#### 连接数据库和PHP
请一定注意，数据库连接地址请一定填写为`mysql`、`postgres`、`mariadb` 等。
另外Nginx/Caddy/Apache 如果需要访问PHP容器，请填写:`php-fpm`

### 更改laradock 配置
当你再次修改完`env-example` 后，请一定按照如下方法执行：
```
cp env-example .env
```
重新构建相应的容器
```
docker-compose build php-fpm worksapce
```
如果还修改了 其他容器配置，请在后面一同加上

#### 修改容器参数

值得注意的是，直接在容器内通过命令所做的修改，比如增加PHP拓展，在重启容器后就会被恢复成初始状态～
所以，需要修改容器环境，需要修改对应的Dockerfile 文件，然后重新构建（上述操作）。

{{< note title="通过env 修改数据库密码请注意" >}}
由于数据库的数据是映射到 `wwwroot/data` 目录，
所以在`env-example` 修改数据库密码，即使重新构建也无效。 
如需强制更改 请删除`wwwroot/data` 里面对应数据库的数据。
日常修改密码，请使用`phpmyadmin` 或者 `pgadmin`

{{< /note >}}

## 如何使用Caddy
caddy 是用go语言开发的轻巧高性能的HTTP服务器，一个文件就能运行，配置也相对简单。
修改`caddy`下的`Caddyfile`文件后重启caddy即可。
常见的一些配置
### HTTP域名
80端口号，和后面的 `{` 必须有空格
```
domain1.com:80  domain2.com:80 {
  root /home/wwwroot  # 网站目录
  index index.php # 默认首页
# 这里是配置
}
```
### HTTPS 域名
```
domain.com:443 {
  root /var/www/notadd/public
  index index.php
  tls you@163.com   # 自动申请证书，必须在外网，且域名可访问
  #  如果你有证书，可如下方式配置
  # tls /home/ssl/domain.com.crt /home/ssl/domain.com.key
}
```
### 配置PHP转发 （Laravel为例）
```
Laravel.com:80 {
        root /var/www/notadd/public
        fastcgi / php-fpm:9000 php {
                index index.php
        }

        # To handle .html extensions with laravel change ext to
        # ext / .html

        rewrite {
                r .*
                ext /
                to /index.php?{query}
        }
        gzip   # 开启gzip
        browse # 开启文件浏览
        #日志
        log /var/log/caddy/access.log
        errors /var/log/caddy/error.log
}
```
### markdown 渲染
caddy 可以直接帮你把md 文件渲染成网页

```
domian.com:80 {
  markdown {
	ext /data # 不进行渲染的目录
	template [name] path # 模板，可不填，使用默认
  }
}
```
### 自动从git 同步

```
domian.com:80 {
  root /home
  git https://github.com/notadd/notadd.git /var/www/ {
      key /home/git/domian.key # key 文件地址，公有库可忽略
      interval 60 # 间隔60秒
      # 或者使用钩子同步
      hook /hook  password # hook地址和密钥，用于 github 等git 仓库推送更新。
  }
}
```

### 创建文件下载服务器

需要说明的是，这个自带界面哦，还能在线编辑文件

```
domian.com:80 {
  root /home
  filemanager
}

```
当然还有更多好玩的用法，参考官方文档： https://caddyserver.com/docs


### 如遇到问题，欢迎提问：https://bbs.notadd.com/category/8
