## HTTP Caddyfile

这个页面记录了 HTTP 服务器如何使用 Caddyfile。如果您还没有了解，先阅读[Caddyfile教程](/get-started/#caddyfile)，或者先读一下[Caddyfile语法](/get-started/#caddyfile-syntax)。


**主题**

1. [站点地址](#website)
2. [路径匹配](#path-matching)
3. [指令](#directives)
4. [占位符](#placeholders)

<a name="website"></a>
### 站点地址 {#website}

HTTP服务器使用站点地址作为标签。在窗体方案中指定地址://主机:端口/路径，其中除一个外都是可选的。`scheme://host:port/path`

主机部分通常是localhost或域名。默认端口是`2015`(除非该站点符合自动HTTPS的条件，在这种情况下，它被更改为`443`)。协议是指定端口的另一种方法。“http”或“https”是有效的，分别代表端口80和443。如果指定了协议和端口，则端口优先。例如，这个假设自动HTTPS应用于它可以使用的地方):

```
:2015                    # 主机: (any), 端口: 2015
localhost                # 主机: localhost; 端口: 2015
localhost:8080           # 主机: localhost; 端口: 8080
example.com              # 主机: example.com; 端口s: 80->443
http://example.com       # 主机: example.com; 端口: 80
https://example.com      # 主机: example.com; 端口s: 80->443
http://example.com:1234  # 主机: example.com; 端口: 1234
https://example.com:80   # Error! HTTPS on port 80
*.example.com            # 主机: *.example.com; 端口: 2015
example.com/foo/         # 主机: example.com; 端口s: 80, 443; Path: /foo/
/foo/                    # 主机: (any), 端口: 2015, Path: /foo/
```

网站地址必须是唯一的;一个站点的所有配置必须按照相同的定义进行分组。

<a name="path-matching"></a>
### Path Matching 路径匹配 {#path-matching}

一些指令接受指定匹配的基本路径的参数。基本路径是一个前缀。如果URL以基本路径开头，它将是一个匹配。例如，一个 `/foo` 的基本路径将匹配请求到 `/foo`  `/foo.html` `/foobar` 和 `/foo/bar.html` 。如果你想限制一个基本路径只匹配一个特定的目录，那么使用 `/foo/`，这将不匹配 `/foobar`。

<a name="directives"></a>
### Directives 指令 {#directives}

大多数指令调用一层中间件。中间件是应用程序中的一个小层，它用来处理 HTTP 请求，而且做得很好。中间件在启动时被链接在一起(预编译，如果您愿意)。只有从 Caddyfile 中调用的中间件处理程序将被链接进来，因此小的 Caddyfiles 非常快速且高效。

参数的语法因指令而异。有些人需要论据，有些则没有。请参阅每个指令的文档的签名。

对于注册为插件的指令，文档页将显示与服务器类型前缀前缀的指令名称，例如 "http.realip" 或 "dns.dnssec" 。当在 Caddyfile 中使用它们时，清删除前缀("http")。前缀只是为了确保唯一的命名，但是不应在 Caddyfile 中使用。

<a name="placeholders"></a>
### Placeholders 占位符 {#placeholders}
在某些情况下，指令会接受占位符(可替换的值)。这些词是由大括号{ }和在请求时的HTTP服务器解释的。例如，{ query }或{ > Referer }。把它们看做变量。这些占位符与您可以在Caddyfiles中使用的环境变量没有关系，只是我们借用了语法来熟悉它。




## Automatic HTTPS



<a name="on-demand"></a>
### ondemand {#ondemand}

<a name="dns-challenge"></a>
#### dns-challenge {#dns-challenge}

## MITM Detection

## Placeholders

## Template Actions
