---
date: 2017-08-29T18:53:19+08:00
title: Caddy 中文文档
type: index
---

# caddy 中文文档

## 如何使用  Caddy

### 1. 制作一个 Caddy文件

这个 `Caddyfile` 是配置 Caddy 的文本文件。它的设计很简单，难以弄错。

Caddyfile 的第一行始终是要提供的站点的地址。

您可以根据需要定义多个站点(使用`{}`); Caddy支持虚拟主机和许多其他功能！

```
matt.life   # 网站域名

ext .html   # 排除 URLs
errors error.log {       # 错误日志
    404 error-404.html   # 指定错误页面
}

# PHP 配置
fastcgi /blog localhost:9000 php

# API 负载均衡
proxy /api localhost:5001 localhost:5002

```
### 运行 Caddy

你需要做的是运行 `caddy` ！ 如果 `Caddyfile` 位于当前文件夹中，则自动加载您的`Caddyfile` 。对于生产环境，默认情况下会启用 HTTPS ！

```
$ caddy
Activating privacy features... done.
http://matt.life
https://matt.life

```

### 打开浏览器

输入您的网站的地址，查看它。正式网站将重定向到HTTPS。

Caddy 非常适合在家或工作场所建设服务，也可以为生产环境提供服务。试试看！



[![upyun](https://www.notadd.com/src/upyun.svg "又拍云")](https://console.upyun.com/register/?invite=r17EYO3BW) 提供CDN赞助