---
title: 开始使用
---

## 安装说明

### 系统环境

操作系统： Linux（推荐）/Mac OS/ Windows 2008+

PHP版本 ： 7.0+

数据库： PostgreSQL 9.4+（推荐）/ MySQL 5.7+ / SQLite 3.9+


## 环境要求

### 需要的扩展

* PHP 必须大于 7.0.0
* 必须安装 PHP 扩展 dom
* 必须安装 PHP 扩展 fileinfo
* 必须安装 PHP 扩展 gd
* 必须安装 PHP 扩展 json
* 必须安装 PHP 扩展 mbstring
* 必须安装 PHP 扩展 openssl
* 必须安装 PHP 扩展 bc_math
* 使用 Mysql 数据库引擎则必须安装PHP扩展 pdo_mysql
* 使用 Pgsql 数据库引擎则必须安装PHP扩展 pdo_pgsql
* 使用 Sqlite 数据库引擎则必须安装PHP扩展 pdo_sqlite

### 需要的函数

`exec`,`system`,`scandir`,`shell_exec`,`proc_open`,`proc_get_status`


## Nginx/Apache/Caddy

### Nginx 配置

```
location / {
  rewrite ^(.*)$ /index.php break;
}
```

### Apache 配置

Apache 下一般public（服务器）/根目录（虚拟主机） 下都有附带的 `.htaccess` 文件，
如果在你的Apache环境中不起作用，请尝试下面这个版本：

```
Options +FollowSymLinks
RewriteEngine On

RewriteRule ^ index.php [L]
```
### caddy 配置

```
    fastcgi / 127.0.0.1:9000 php
    rewrite {
        to /index.php?{query}
    }
 ```
## 编译安装

{{< note title="安装前注意" >}}
安装前请确保已经安装git、php及composer，否则无法执行安装。
{{< /note >}}


### 1. 下载源代码

```bash
$ git clone https://github.com/notadd/notadd.git
```

### 2. 修改 public、storage 目录权限

设置为 php-fpm 的用户及用户组(部分一键安装包为 `www:www` )，Windows 请跳过此步

```bash
$ chown -R www-data:www-data notadd
```

或

```bash
$ chmod 755 notadd/public notadd/storage notadd/statics
```

### 3. 安装

```bash
$ cd notadd
$ composer install
$ php notadd vendor:publish --force
```
{{< note title="如果composer执行慢或者卡住" >}}
请使用中国镜像 https://pkg.phpcomposer.com/
{{< /note >}}

将域名绑定到 `notadd/public` 目录，并访问该域名进行安装。

访问后台入口 `http://yourdomain/admin`。

{{< warning title="pulic 必须为网站根目录" >}}
否则前端资源将请求不到，出现空白页。
同时，为了网站安全，请务必执行此操作。
{{< /warning >}}

## 在线安装

（暂未提供）

## Docker 安装

```
docker run -p 8080:80 --name notadd notadd/notadd
```

访问 http://localhost:8080

docker 安装相关文档请访问： https://github.com/notadd/docker-notadd

### 使用laradock部署

参见 https://docs.notadd.com/laradock/


## 模块的安装

以内容管理模块为例。



### GitHub源码安装方式：

1、克隆仓库的develop分支：

```bash
cd notadd/modules
git clone https://github.com/notadd/content.git
```

2、模块初始化：

```bash
cd content
composer install --no-dev
```

3、到 **后台/全局/应用管理/模块配置/本地安装** 进行模块的安装。


### 应用商店在线安装：

（后期提供）


## 插件安装

以百度推送插件为例。

GitHub 源码安装方式：

1、克隆仓库：

```bash
cd notadd/extensions
## 若没有厂商目录，则新建一个，已存在则忽略下面一条命令：
mkdir notadd
git clone https://github.com/notadd/baidu-push.git
```

{{< note title="请确保extensions目录有两层目录" >}}
例如，baidu-push的目录结构为 notadd/extensions/notadd/baidu-push。 (notadd 为开发者名)
{{< /note >}}

2、插件初始化：

```bash
cd baidu-push
composer install --no-dev
```

3、到 **后台/全局/应用管理/插件配置/本地安装** 进行模块的安装。





### 应用商店在线安装：

（后期提供）



## 应用商店 Alpha

正在开发中...

[点击查看可用扩展](https://bbs.notadd.com/topic/7/notadd-%E6%A8%A1%E5%9D%97%E6%8F%92%E4%BB%B6%E6%A8%A1%E6%9D%BF%E6%8B%93%E5%B1%95-%E8%BF%9B%E5%BA%A6%E8%A1%A8)


