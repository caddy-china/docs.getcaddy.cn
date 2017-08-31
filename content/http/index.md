## basicauth

## bind

## browse

## errors

## expvar

## ext

## fastcgi

fastcgi 代理请求到一个 FastCGI 服务器。虽然这个指令最常见的用途是为PHP站点提供服务，但它默认是一个通用的 FastCGI 代理。这个指令可以用不同的请求路径多次使用。

### 语法

```
fastcgi path endpoint [preset] {
	root     directory
	ext      extension
	split    splitval
	index    indexfile
	env      key value
	except   ignored_paths...
	upstream endpoint
	connect_timeout duration
	read_timeout    duration
	send_timeout    duration
}
```

- path 

在请求转发之前匹配的基本路径。

- endpoint

FastCGI 服务器的地址或 Unix socket 。

- preset

可选的预设名称（见下文）。使用预设时，不需要重复预设的单独设置

- root 

根指定 FastCGI 服务器使用的根目录，如果它不同于虚拟主机的根目录。这在FastCGI 服务器位于不同的服务器、chroot-jailed 和 容器中，则很有用。

- ext 

指定扩展名，如果请求URL具有该扩展名，则会将请求代理到FastCGI。


- split

指定如何分割URL;当它成为 PATH_INFO CGI 变量的一部分后，分割值就变成了第一部分的末尾和 URL 中的任何内容。

- index

指定文件未由URL指定时要尝试的默认文件

- env

为给定值设置了一个名为 key 的环境变量; ENV 属性可以使用多次，而值可以使用[请求占位符](/http-server/#placeholders)。

- except 

排除请求路径（可分隔），即使它与基本路径匹配，它也不受 fastcgi处理的影响。

- upstream 

指定了一个额外的后端来使用。将执行基本的负载平衡。这可以多次指定。



- connect_timeout 

允许连接到后端的时间。必须是持续时间值(例如: "10s" )。

- read_timeout

允许从后端读取响应的时间。必须是持续时间值。

- send_timeout

允许向后端发送请求的时间。必须是持续时间值。

### Presets 预设

预设是某种快速cgi配置的简写。这些预设是可用的:

php 简写

```
ext   .php
split .php
index index.php
```
您不需要指定预置的配置设置。但是，如果需要手动声明它们，则可以覆盖它的单独设置。


### Examples 例子

在 `127.0.0.1:9000` 处理 FastCGI 的所有请求：

```
fastcgi / 127.0.0.1:9000
```


将/ blog中的所有请求转发到php-fpm提供的PHP站点（如WordPress）：

```
fastcgi /blog/ 127.0.0.1:9000 php
```

使用自定义FastCGI配置：

```
fastcgi / 127.0.0.1:9001 {
	split .html
}
```
使用 PHP 预设，但覆盖ext属性：

```
fastcgi / 127.0.0.1:9001 php {
	ext .html
}
```
使用PHP预设，但 FastCGI 服务器正在基于[官方 Docker 映像](https://hub.docker.com/_/php/)（容器端口 9000 发布到 127.0.0.1:9001）的容器中运行：

```
fastcgi / 127.0.0.1:9001 php {
	root /var/www/html
}
```


## gzip

## header

## import

## index

索引设置用于 “index” 文件的文件名列表。当请求目录路径而不是特定的文件时，将对目录进行检查，以检查现有的索引文件。将提供第一个匹配的文件名。

按照这个顺序，默认的索引文件是:

1. index.html
2. index.htm
3. index.txt
4. default.html
5. default.htm
6. default.txt

当使用 index 指令时，将不会附加此列表。

### 语法

```
index filenames...
```

- filenames...

以空格分隔的文件名作为索引的列表。至少需要一个名称。

### 例子

只使用 goaway.pn g和 easteregg.html 作为索引文件：

```
index goaway.png easteregg.html
```


## internal

## limits

## log

## markdown

## mime

## pprof

## proxy

## push

## redir

## request_id

## rewrite

## root

## shutdown

## startup

## status

## templates

## timeouts

## tls

## websocket