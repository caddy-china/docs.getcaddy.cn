## basicauth

## bind

## browse

## errors

## expvar

## ext

当 URL 的路径部分为空时，ext 可以让您的站点拥有干净的URL。
通过检查一个点(`.`)路径的最后一个元素，可以检测到URL中的扩展。

### 语法

```
ext extensions...
```
- extensions... 

一个空间分隔扩展列表(包括`.`)来尝试。将按所列的顺序尝试扩展。至少需要一个扩展。

### 例子

假设您有一个名为/contact.html的文件。你可以试着用它来服务 .html 

按顺序尝试.html，.htm和.php：

```
ext .html .htm .php
```
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

markdown 可以根据需要将 `markdown文件` 作为 HTML 页面服务。您可以指定整个自定义模板，或者仅在页面上使用 CSS 和 JavaScript 文件，以自定义的外观和行为。

### 语法

```
markdown [basepath] {
	ext         extensions...
	[css|js]    file
	template    [name] path
	templatedir defaultpath
}
```
- basepath 

是匹配的基本路径。如果请求 URL 没有以此路径前缀，则 Markdow n将不会激活。默认是站点的根目录。

- extensions...

以空格分隔的文件扩展名列表，用作 Markdown（默认为.md，.markdown 和.mdown）; 这与使用缺省文件扩展名的ext指令不同。

- css

表明下一个参数是在页面上使用的 css 文件

- js

指出下一个参数是包含在页面上的 JavaScript 文件。

- file

添加到页面的JS或CSS文件。


- template

定义了一个带有给定名称的模板在给定的路径上。要指定默认模板，请省略名称。Markdown 文件可以使用其前面的名称来选择模板。

- templatedir 

设置在列出模板时使用给定的 defaultpath 的默认路径。


您可以多次使用 js 和 css 参数来为默认模板添加更多的文件。如果您想接受默认值，应该完全省略花括号。

### 前端 (文档元数据)

Markdown 文件可能从前面的事情开始，这是一个关于文件的特殊格式的元数据块。例如，它可以描述用于呈现文件的 HTML 模板，或者定义标题标签的内容。前面的内容可以是 YAML、TOML 或 JSON 格式。

TOML 以 `+++` 开始和结束：

```
+++
template = "blog"
title = "Blog Homepage"
sitename = "A Caddy site"
+++
```

YAML 以 `---` 开始和结束：

```
---
template: blog
title: Blog Homepage
sitename: A Caddy site
---
```

JSOn 用 `{}` 包起来

```
{
	"template": "blog",
	"title": "Blog Homepage",
	"sitename": "A Caddy site"
}
```

前面的字段 "author", "copyright", "description" 和 "subject" 将用于`<meta>`在呈现的页面上创建标签。

### Markdown 模板

模板文件只是带有模板标签的 HTML 文件，称为 actions ，可以根据所提供的文件插入动态内容。元数据中定义的变量可以从`{{.Doc.variable}}`这样的模板中访问，其中“variable”是变量的名称。该变量`.Doc.body`保存 markdown 文件的主体。

这是一个简单的示例模板（设计）：

```
<!DOCTYPE html>
<html>
	<head>
		<title>{{.Doc.title}}</title>
	</head>
	<body>
		Welcome to {{.Doc.sitename}}!
		<br><br>
		{{.Doc.body}}
	</body>
</html>
```
除了这些模板操作之外，所有[标准 Caddy 模板操作](/http-server/#template-actions)都可以在 Markdown 模板中使用。一定要将您渲染的任何HTML(使用 HTML、js 和 urlquery 函数)进行过滤!

### 例子

在没有特殊格式的情况下，在/ blog中设置Markdown页面（假设.md是Markdown扩展名）：

```
markdown /blog
```

与上述相同，但使用自定义的 CSS 和 JS 文件：

```
markdown /blog {
	ext .md .txt
	css /css/blog.css
	js  /js/blog.js
}
```

使用自定义模板：

```
markdown /blog {
	template default.html
	template blog  blog.html
	template about about.html
}
```


## mime

## pprof

## proxy

proxy 方便了基本的反向代理和稳健的负载均衡器。该 proxy 支持多个后端和添加自定义标头。负载均衡功能包括多个策略，运行状况检查和故障转移。Caddy 还可以代理 WebSocket 连接。

该中间件添加了可以以[日志](#log)格式使用的[占位符](/http-server/#placeholders)：{upstream} - 请求被代理的上游主机的名称。

### 语法

在其最基本的形式中，简单的反向代理使用以下语法：

```
proxy from to
```
- from

是要被代理的请求的基本路径

- to

是要代理的目标端点（可能包括端口范围）

**但是，包括负载平衡在内的高级功能可以用扩展语法来使用：**

```
proxy from to... {
	policy name [value]
	fail_timeout duration
	max_fails integer
	max_conns integer
	try_duration duration
	try_interval duration
	health_check path
	health_check_port port
	health_check_interval interval_duration
	health_check_timeout timeout_duration
	header_upstream name value
	header_downstream name value
	keepalive number
	without prefix
	except ignored_paths...
	upstream to
	insecure_skip_verify
	preset
}
```
- from

是要被代理的请求的基本路径。

- to

是要代理的目的端点。需要至少一个，但可以指定多个。如果未指定方案（http / https），则使用http。Unix 套接字也可以通过前缀 “unix：”来使用。

- policy

使用负载均衡策略; 仅适用于多个后端。可以是随机的，least_conn，round_robin，first，ip_hash，uri_hash 或 header。如果选择 header ，还必须提供 header 名称。默认是随机的。

- fail_timeout

指定记录对后端的请求失败最长时间。超时>0 启用了请求失败计数，并且在失败的情况下需要在后台进行负载均衡。如果失败的请求数量累积到 max_fail s值，则主机将被视为已关闭，并且不会将请求路由到该主机，直到失败的请求开始被忘记为止。默认情况下，这是禁用（0s），意味着失败的请求将不会被记住，后端将始终被视为可用。必须是持续时间值（如“10s”或“1m”）。

- max_fails

在考虑后端关闭之前需要的 fail_timeout 中的失败请求数。如果 fail_timeout 为0，则不使用。必须至少为1，默认值为1。

- max_conns

每个后端的最大并发请求数。0表示无限制。达到极限时，其他请求将失败，并显示 Bad Gateway（502）。默认值为0。

- try_duration 

为每个请求选择可用的上游主机多长时间。默认情况下，此重试被禁用（“0s”）。客户端可能会挂起这么长时间，而代理尝试找到可用的上游主机。仅当对初始选择的上游主机的请求失败时才使用此值。

- try_interval

是在选择另一个上游主机来处理请求之间等待多长时间。默认值为250ms。只有在向上游主机的请求失败时才相关。请注意，使用 try_duration 将其设置为0可能会导致非常严格的循环，并且如果所有主机都停留，则可以占满CPU。

- health_check

将使用路径来检查每个后台的运行状况。如果后端返回200-399的状态码，则该后端被认为是健康的。如果没有，后端标记为 health_check_interval 不健康，并且请求不会被路由到它。如果未提供此选项，则禁用健康检查。

- health_check_port

将使用端口来执行运行状况检查，而不是为上游提供的端口。如果您使用内部端口进行调试，则健康检查端点会从公共视图中隐藏，这很有用。

- health_check_interval

指定不健康后端的每个健康检查之间的时间。默认间隔为30秒（“30秒”）。


- health_check_timeout

设置健康检查请求的最后期限。如果健康检查在 health_check_timeout 内没有响应，则健康状况检查被认为是失败的。默认值为60秒（“60s”）。


- header_upstream 

将文件头传递给后端。字段名称是 name，值是 value。多个标题可以多次指定此选项，也可以使用请求占位符插入动态值。默认情况下，现有的字段头将被替换，但您可以通过使用加号（+）前缀字段名称来添加或合并字段值。您可以通过使用减号（ - ）前缀标题名称来删除字段，并将该值留空。


- header_downstream

修改后端返回的响应头。它的工作方式与 header_upstream 相同。

- keepalive 

是保持打开到后端的最大空闲连接数。默认启用;设置为0，禁用 keepalives。在繁忙的服务器上设置更高的值。


- without

在将请求代理向上游之前，没有URL前缀。例如，请求/api /foo /api将会导致代理请求/foo。

- except

一个空格分隔的路径列表，以排除在代理之外。与 ignored_paths 匹配的请求将通过 thrued。

- upstream 

指定另一个后端。如果需要，它可以使用像":8080-8085"这样的端口范围。当有很多后端路由时，它经常被使用多次。

-insecure_skip_verify 

覆盖了后端TLS证书的验证，基本上通过HTTPS禁用安全功能。

- preset 

配置代理以满足某些条件的可选速记方式。请参阅下面的预设。


{{< note title="注意" >}}
为了在失败事件中执行适当的冗余负载均衡，必须设置fail_timeout 和 try_duration 值为>0。
{{< /note >}}

第一个选项之后的所有内容都是可选的，包括由大括号括起来的属性块。


### 预设

以下的预设有:

- websocket

指示此代理正在转发WebSocket连接。这是简写:

```
header_upstream Connection {>Connection}
header_upstream Upgrade {>Upgrade}
```
{{< note title="注意" >}}
HTTP/2 不支持协议升级。
{{< /note >}}

- transparent

从原始请求中传递主机信息，这是大多数后端应用程序所期望的。简写:
 
 ```
 header_upstream Host {host}
header_upstream X-Real-IP {remote}
header_upstream X-Forwarded-For {remote}
header_upstream X-Forwarded-Proto {scheme}
```

### 策略

有几种负载均衡策略可用:

- random (default)

随机选择后端。

- least_conn

选择后端与最少的活动连接。

- round_robin

以循环方式选择后端。

- frist

按照在Caddyfile中定义的顺序选择第一个可用的后端。

- ip_hash 

通过散列请求 IP 来选择后端，根据后端的总数均匀分布在哈希空间上。

- uri_hash

通过散列请求 URI 来选择后端，根据后端的总数均匀分布在哈希空间上

- header 

通过散列由策略名称后面的[value]指定的给定标题的值进行选择，基于总后端数量均匀分布在散列空间

### 例子

将/ api内的所有请求代理到后台系统：

```
proxy /api localhost:9005
```

负载均衡三个后端之间的所有请求（使用随机策略）：

```
proxy / web1.local:80 web2.local:90 web3.local:100
```

与上述相同，具有 header 关联性：

```
proxy / web1.local:80 web2.local:90 web3.local:100 {
	policy header X-My-Header
}
```

循环风格：

```
proxy / web1.local:80 web2.local:90 web3.local:100 {
	policy round_robin
}
```

使用健康检查和代理标头来传递主机名，IP和方案上游：

```
proxy / web1.local:80 web2.local:90 web3.local:100 {
	policy round_robin
	health_check /health
	transparent
}
```

代理WebSocket连接：

```
proxy /stream localhost:8080 {
	websocket
}
```

排除 /static 或 /robots.txt 的代理请求：

```
proxy / backend:1234 {
	except /static /robots.txt
}
```

## push

## redir

如果URL匹配指定的规则，redir 会向客户端发送 HTTP 重定向状态代码。你也可以给它定义条件。

### 语法

```
redir from to [code]
```

- from 

要匹配的请求路径（它必须完全匹配，除了/，这代表匹配全部）。

-to 

重定向到（可使用的路径[请求占位符](/http-server/#placeholders)）。



- code

用于响应的HTTP状态代码; 必须在[300-308]（不包括306.）范围内。也可以使用`meta`为浏览器发布元标记进行重定向。默认状态代码是301（永久重定向）。

要创建永久的“全部”重定向，请忽略 from 值：

```
redir to
```

如果您有很多重定向，请通过创建表来共享重定向代码：

```
redir [code] {
	from to [code]
}
```
每行定义一个重定向，并可以选择覆盖在`表`顶部定义的重定向代码。如果未指定重定向代码，则使用默认值。

一组重定向也可以是有条件的：

```
redir [code] {
	if    a cond b
	if_op [and|or]
	...
}
```

- if

指定重写条件。默认情况下，多个 if 需要同时满足[译者注：`与`] 。a 和 b 可以是任何字符串，可以使用请求占位符。 cond 是条件，可能的值在[rewrite](#rewrite)中可以看到 （也有一个if声明）。

if_op 指定 if 之间如何联系; 默认是and[译者注：and = `与` or = `或`]。


### 被保护的路径

默认情况下，重定向会精确匹配路径到您定义的精确位置。您可以在任何"to"参数中使用[可替换值](/http-server/#placeholders)（例如{uri} 或 {path}）来保留请求URL的路径或其他部分。仅支持请求占位符。

### 例子

当请求进入/resources/images/photo.jpg 时，使用HTTP 307（临时重定向）状态码重定向到/resources/images/drawing.jpg：

```
redir /resources/images/photo.jpg /resources/images/drawing.jpg 307
```

将所有请求重定向到https://newsite.com，同时保留请求 URI：

```
redir https://newsite.com{uri}
```

定义共享 307 状态代码的多个重定向，最后一个除外：

```
redir 307 {
	/foo     /info/foo
	/todo    /notes
	/api-dev /api       meta
}
```

只有转发协议是 HTTP 时重定向：

```
redir 301 {
	if {>X-Forwarded-Proto} is http
	/  https://{host}{uri}
}
```





### 语法


## request_id

## rewrite

rewrite 执行内部 URL 重写。这允许客户端请求一个资源，但实际上是另一个资源，而不需要 HTTP 重定向。rewrite 是客户不可见的。

有简单的 rewrite（快速）和复杂的 rewrite（较慢），但它们足够强大以适应大多数动态后端应用程序。

### 语法

```
rewrite from to
```

- from

匹配的确切路径

-to 

重写（资源与响应）的目标路径

高级用户可能会打开一个块并进行复杂的重写规则：

```
rewrite [basepath] {
	regexp pattern
	ext    extensions...
	if     a cond b
	if_op  [and|or]
	to     destinations...
}
```
- basepath 

与正则表达式改写前匹配的基本路径。默认为/。

- regexp（简写："r"）

将匹配给定正则表达式模式的路径 。 
> 超高负载服务器应避免使用正则表达式。

- extensions... 

包含或忽略的文件扩展名（带`.`）以空格分隔。前缀以扩展名 `!` 排除。正斜杠 `/` 符号匹配没有文件扩展名的路径。

- if 

指定重写条件。多个if之间默认为 and (`与`) 的关系。a 和 b 可以是任何字符串，可以使用[请求占位符](/http-server/#placeholders)。 `cond`是条件，可能的值如下所述。

- if_op 

指定 if 之间如何联系; 默认是and[译者注：and = `与` or = `或`]。

- destinations... 

一个或多个空格分隔的路径来重写，支持 [请求占位符](/http-server/#placeholders)以及编号的正则表达式捕获，如{1}，{2}等。重写将按顺序检查每个目标位置并重写存在的第一个目标规则。每一个都被作为一个文件进行检查，或者作为一个目录结束。如果没有其他目标规则，最后一个目标规则将作为默认值。

### if 条件

if关键字是描述的规则的强大方法。它需要的格式 `a cond b`，其中的值 `a` 和 `b` 被 `cond` 分隔开，形成一个条件。它可以是这样的:

- is = a等于b
- not = a不等于b
- has = a有b作为子串（b是a的子字符串）
- not_has = b不是a的子串
- starts_with = b是a的前缀
- not_starts_with = b不是a的前缀
- ends_with = b是a的后缀
- not_ends_with = b不是a的后缀
- match = `任何`匹配b，其中b是正则表达式
- not_match = a不匹配b，其中b是正则表达式

注意：作为一般规则，您可以cond通过使用前缀来对任何条件进行否定`not_`。

### 例子

将所有内容重写为 /index.php。（`rewrite / /index.php`只匹配/）

```
rewrite / {
	regexp .*
	to /index.php
}
```

当请求进入/mobile 时，实际上是 /mobile/index

```
rewrite /mobile /mobile/index
```

如果文件不是favicon.ico，并且它不是有效的文件或目录，请提供维护页面（如果存在），或者最后重写为index.php。

```
rewrite {
	if {file} not favicon.ico
	to {path} {path}/ /maintenance.html /index.php
}
```
如果用户代理包含"mobile"，且路径不是有效的文件/目录，rewrite 到移动索引页。

```
rewrite {
	if {>User-agent} has mobile
	to {path} {path}/ /mobile/index.php
}
```
用查询字符串 rewrite /app 到 /index 并且带`{1}`匹配`(.*)`。

```
rewrite /app {
	r  (.*)
	to /index?path={1}
}
```

将 /app/example 的请求重写到 /index.php?category=example.

```
rewrite /app {
	r  ^/(\w+)/?$
	to /index?category={1}
}
```


## root

root 指定站点的根目录。这在网站的根目录（/）与 Caddy 执行的目录不同时非常有用。

默认的root是当前的工作目录。相对的根路径是相对于当前工作目录。

### 语法

```
root path
```

- path 

站点根目录

### 例子

从 jake 的p ublic_html 文件夹中运行，而不是当前工作目录:

```
root /home/jake/public_html
```


## shutdown

## startup

启动在服务器开始时执行命令。这对于通过运行脚本或启动后台进程（如php-fpm）来准备服务站点非常有用。（另请参见 [shutdown](#shutdown)）

在启动时执行的每个命令都是阻塞的，除非您使用空格和`&`后缀命令，否则将导致命令在后台运行。命令的输出和错误分别转到`stdout`和`stderr`。没有stdin。

在`Caddyfile`中，命令只执行一次。

### 语法

```
startup command
```

- command

执行的命令; 其后可能是参数


### 例子

在开始监听之前启动php-fpm：

```
startup /etc/init.d/php-fpm start、
```

在 Windows 上，当命令路径包含空格时，可能需要使用引号：

```
startup "\"C:\Program Files\PHP\v7.0\php-cgi.exe\" -b 127.0.0.1:9123" &
```


## status

## templates

## timeouts

## tls

## websocket