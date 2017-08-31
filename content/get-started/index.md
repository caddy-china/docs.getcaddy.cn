---
title: 开始使用
---

<a name="quick-start"></a>
## 快速开始 {#quick-start}

1. [下载 Caddy](https://caddyserver.com/download) 并将其放在你的 **PATH** （环境变量） 当中。[译者注：Linux 一般为 /bin/local/ 目录]
2. `cd` 到你网站的目录。
3. 运行 `caddy` 。

完成了！从你的浏览器打开 `http://localhost:2015` 看一下是否运行。在默认情况下，Caddy服务于当前的工作目录。

{{< note title="如果你看到 404 错误" >}}
说明 Caddy 正在工作，但是你的站点的根目录缺少索引文件。（需要更多的指导吗？阅读[初学者教程](#beginner)）
{{< /note >}}



接下来，[学习使用 Caddyfile 配置 Caddy](#caddyfile)。

<a name="beginner"></a>
## 新手教程 {#beginner}

本教程将帮助你第一次安装、运行和配置 Caddy。假设你从来没有使用过 web 服务器！（如果有，那就[快速开始](#quick-start)）尽管 Caddy 很容易使用，但仍然期望您已经熟悉使用您的机器。

- 提取，移动和重命名文件
- 管理用户和文件权限
- 使用终端或命令行
- 配置防火墙


有了这些前提条件，您就可以开始了。

### **Topics** {#topic}

1. [下载](#download)
2. [安装](#install)
3. [运行](#run)
4. [配置](#configure)

<a name="download"></a>
### 下载 {#download}

从 [下载页面](https://caddyserver.com/download) 下载 Caddy。你可以获得几乎任何系统和架构的 Caddy。Caddy的下载页面不同于其他web服务器:它允许您使用插件自定义您的构建。


对于本教程，你不需要任何插件。

有时我们对构建服务器进行维护。如果下载页面关闭，你可以随时从 [GitHub](https://github.com/mholt/caddy) 下载 [最新版本](https://github.com/mholt/caddy/releases/latest)

<a name="install"></a>
### 安装 {#install}

你下载的文件是压缩包。你需要提取 Caddy 二进制文件（可执行文件）。

| Windows | macOS | Linux |
|----|----|-----|
| 右键 .zip 文件，然后选择「全部提取」。提取到任意文件夹，完成后你可以删除压缩包。 | 双击 .zip 文件夹，或者运行：<br /> `unzip caddy*.zip caddy` | 运行命令：<br /> `tar -xzf caddy*.tar.gz caddy` |

接下来，我们把 Caddy 二进制文件移动到一个可以轻松执行的文件夹中。

| Windows | macOS／Linux |
|----|----|
| 将可执行文件移动到容易获取的文件夹。例如 `C:\Caddy` 。 | 任何 `$PATH` 位置都可以：<br /> `mv ./caddy /usr/local/bin` <br /> 如果你得到 **权限拒绝** 的错误，你需要运行 `sudo` 。 |

现在 Caddy 在我们可以轻松获取的地方，让我们来[运行](#run)吧！

<a name="run"></a>
### 运行 {#run}

默认情况下，Caddy 将使用当前目录（正在执行的目录，而不是二进制文件所在文件夹）作为站点的根目录。这使得运行本地网站变得容易！

使用终端或命令行，切换到你的站点所在文件夹：

```shell
cd path/to/my/site
```

并运行 Caddy ：

| Windows | macOS／Linux |
|----|----|
| 假设你将 .exe 文件放在 C:\Caddy 中，运行：<br /> `C:\Caddy\caddy.exe` | `caddy` |

{{< note title="如果你看到 404 错误" >}}
说明 Caddy 正在工作，但是你的站点的根目录缺少索引文件。
{{< /note >}}

你可以按 **Ctrl + C** 退出 Caddy，它将尽可能终止。

<a name="caddyfile"></a>
### 配置 {#configure}

你的站点已经适合生产环境，但是并不理想，因为我们将它运行在 `localhost` 上（您的家庭电脑）：

1. 该站点正在 2015 端口上运行，而不是 80（标准 HTTP 端口）。
2. 该站点不受 HTTPS 保护。

通过提供给 Caddy 网站的名称很容易解决这两个问题。通过「name」，我们的意思是一个域名。我们将使用 `example.com` ，但是你要使用你的真实域名。如果你的计算机可以通过网络 80 和 443 端口获得更广泛的互联网访问，你的域名指向你所在的计算机，则接下来将正常工作[译者注：HTTPS 和 HTTP2 将会正常工作]。如果没有，或者你没有真正的域名，请暂时使用 `localhost` 作为你的域名。

站点的名称也可以作为 host 或者 hostname 。指定 host 的一种方法是使用命令参数：

| Windows | macOS／Linux |
|----|----|
| `C:\Caddy\caddy.exe -host example.com` | `caddy -host example.com` |

你第一次使用真实的 hostname （而不是 localhost）运行 Caddy 时，系统会要求你输入你的 E-Mail 地址。因为 Caddy 需要验证你是否拥有该域名，并自动申请 SSL 证书。

提交你的 E-Mail 后，你是否看到类似 `permission denied` 的错误？这是因为 Caddy 正在试图绑定 80 和 443 端口为一个真实的站点，但是这样需要 root 或者管理员权限：

| Windows | macOS／Linux |
|----|----|
| 右键 cmd.exe ，然后单击「以管理员身份运行」。然后再运行 Caddy：<br /> `C:\Caddy\caddy.exe -host example.com` | 使用 sudo 以 root 方式运行 Caddy：<br /> `sudo caddy -host example.com` |

如果你有权限，并在此运行 Caddy，你会看到：

```
Activating privacy features... done.
https://example.com
http://example.com
```

使用真实的域名触发了 Caddy 的隐私功能，它们运行在 80 和 443 端口上。如果你仅使用 `localhost` 作为主机名，则 Caddy 将继续使用 2015 端口工作，除非你使用 `-port` 选项进行更改。

[命令行](cli)非常适合快速配置 Caddy。但是如果你想每次重用同一个配置怎么办呢？使用 Caddyfile 很简单。

**Caddyfile** 是一个文本文件，告诉 Caddy 如何提供服务，它通常和你的网站在一起，我们来做一个：

| Windows | macOS／Linux |
|----|----|
| 在你的网站文件夹创建一个名为 Caddyfile （拓展名不为 .txt）的文本文件[译者注：无拓展名]，并在其中放置一行（使用你的实际域名或 localhost）：<br /> `example.com` | 使用你的实际域名（或 localhost）：<br /> `echo example.com > Caddyfile` |

运行 Caddy 会自动找到 Caddyfile 文件：

| Windows | macOS／Linux |
|----|----|
| `C:\Caddy\caddy.exe` | `caddy` |

这是因为 Caddy 的第一行始终要提供站点的地址（或名称）。

如果 Caddyfile 不在当前目录，可以告诉 Caddy 从哪里得到 Caddyfile：

| Windows | macOS／Linux |
|----|----|
| `C:\Caddy\caddy.exe -conf C:\path\to\Caddyfile` | `caddy -conf ../path/to/Caddyfile` |

你几乎知道这是危险的。接下来，学习[学习如何运用 Caddyfile](#caddyfile)。你会喜欢，因为这很容易。

<a name="caddyfile"></a>
## Caddyfile {#caddyfile}

本教程将向你介绍如何为 Caddy 配置 Caddyfile 。

Caddyfile 是一个文本文件，它配置 Caddy 的运行方式。

**Caddyfile 第一行始终是要站点的地址**，例如：

```
localhost:8080
```

当你将其保存在名为 Caddyfile 的文件中时，Caddy 会在你启动的时候找到它：

| Windows | macOS／Linux |
|----|----|
| `C:\Caddy\caddy.exe` | `caddy` |

如果 Caddyfile 位于不同位置或者具有其他名称，请告诉 Caddy 它的位置：

| Windows | macOS／Linux |
|----|----|
| `C:\Caddy\caddy.exe -conf C:\path\to\Caddyfile` | `caddy -conf ../path/to/Caddyfile` |

站点地址之后的行以指令开头。指令是 Caddy 识别的关键字。例如，[gzip](gzip) 是一个 HTTP 指令：

```
localhost:8080
gzip
```

指令可以在后面跟一个或者多个参数：

```
localhost:8080
gzip
log ../access.log
```

有些指令需要更多的配置，而不止一行。对于这些指令，您可以打开一个块并设置更多的参数。开始的大括号必须在一行的末尾:


```
localhost:8080
gzip
log ../access.log
markdown /blog {
    css /blog.css
    js  /scripts.js
}
```

如果指令块为空，则应该完全省略大括号。

包含空格的参数必须使用引号 `"` 扩起来。

Caddyfile 也可以使用 `#` 开头的注释：

```
# Comments can start a line
foobar # or go at the end
```

使用单个 Caddyfile 配置多个站点，你 **必须** 使用大括号来分离没个站点的配置：

```
mysite.com {
    root /www/mysite.com
}

sub.mysite.com {
    root /www/sub.mysite.com
    gzip
    log ../access.log
}
```

和指令一样，开始的大括号必须在同一行的末尾，结束的大括号必须独占一行。**所有的指令必须在网站的定义中**。

对于共享配置的站点，请指定多个地址：

```
localhost:8080, https://site.com, http://mysite.com {
    ...
}
```

站点地址也可以在特定路径下定义，或者具有通配符代替左侧的单域名标签：

```
example.com/static, *.example.com {
    ...
}
```

请注意，使用站点地址中的路径将以最长匹配的前缀路由请求。如果你的基本路径是目录，则可以将正斜线 `/` 放置于后缀路径。

在地址和参数中允许使用环境变量。它们必须使用大括号括起来，你可以使用 Unix 或者 Windows 变量格式：

```
localhost:{$PORT}
root {%SITE_ROOT%}
```

这两种语法都适用于任何平台。单个环境变量不会扩展成多个参数/值。

Caddyfile 中 **没有继承或脚本**，**你可能不会多次指定相同的站点地址**。是的，这意味着有时你需要多 复制+粘贴 一些重复的行。如果你有很多重复的行，可以使用 `import` 指令来减少重复。

好吧，这该足够让你了解 Caddy 文档，去使用它！

## 命令行界面

本页介绍了 Caddy 的命令行界面。要获得快速参考并查看默认值，请运行 `-help` 或 `-h` ，例如：`caddy -h` 。

### Flags 命令参数

- -agree 

表示您已阅读并同意让我们加密订阅者协议。如果没有指定此标志，则可能会在运行时提示您同意条款。因此，在自动化环境中建议使用此标志。

- -ca

基础 URL 到证书颁发机构的 ACME 服务器目录。用于创建TLS证书。

- -catimeout

更改 ACME CA HTTP 的超时时间。通常不需要更改此项，除非您的网络遇到与 ACME CA 服务器的重大延迟。在这些情况下，提高此值可以帮助您。接受持续时间值; 默认是10s。

- -conf

用于配置 Caddy 的 Caddyfile 。必须是文件的有效路径，无论是相对还是绝对。

- -cpu

CPU上限。可以是百分比（例如“75％”）或指定要使用多少个内核的数字（例如3）。

- -disable-http-challenge

禁用用于获取证书的 ACME HTTP 的验证。

- -disable-tls-sni-challenge

禁用获得证书的 ACME tls - sni 的验证。

- -email

如果没有为 Caddyfile 指定证书，则使用电子邮件地址来生成 [TLS 证书](/http/#tls)。这不是必须的，但强烈建议这样，如果你丢失了私钥，你可以恢复你的帐户。如果没有电子邮件，Caddy 可能会在运行时提示您发送邮件地址。如果在Caddyfile中没有指定，则建议在自动化环境中使用此选项。

- -grace

持续时间。如果你非常频繁地重新加载(每秒多次)，那么就缩短这段时间。语法与Go的相同。Parse Duration function (5s, 1m30s, etc).

- -help 或 -h

显示基本标志帮助。在显示帮助后，Caddy 将终止;它不会为网站服务。

-host

要侦听的默认主机名或IP地址。

- -http-port

用于HTTP的端口(默认为80)。改变这种情况会产生意想不到的后果，要小心。ACME HTTP 验证需要端口80。


- -https-port

端口用于 HTTPS (默认的443)。改变这种情况会产生意想不到的后果，要小心。ACME tls - sni 验证需要端口 443。

- -http2

HTTP/2 的支持。通过设置为 false 来禁用它。要禁用特定站点，使用 [tls](/http/#tls) 指令的 “alpn” 设置。

- -log

使流程日志。该值必须是日志文件、`stdout` 或 `stderr` 的路径。如果不存在日志文件，Caddy 将创建该日志文件。该文件将用于记录运行时发生的信息和错误。日志文件在变大时被滚动覆盖，因此在长时间运行的过程中使用是安全的。

- -pidfile

pidfile ，使用自动化的环境。Caddy 将编写一个包含当前进程 ID 的文件。

- -plugins

列出已注册为 Caddy 的插件。显示后，Caddy 将终止 ; 它不会为网站服务。

- -port

本地监听端口。

- -quic

使实验 QUIC 支持。请参阅[QUIC wiki页面](https://github.com/mholt/caddy/wiki/QUIC)，了解如何使用 QUIC 实验功能。

- -quiet

静默模式。启用后 Caddy 不会打印信息初始化输出，服务可正常运行。

-revoke

用于撤销SSL证书的主机名。在撤销完成后，Caddy 将停止;如果使用此选项，则将停止站点。

{{< note title="注意" >}}
证书必须在Caddy 的管理下。撤销是指仅限私钥。不要撤销证书以更新它。
{{< /note >}}



-root

路径到默认站点根目录，用于服务文件。

-type

更改服务器类型，默认是 http。如果您的 Caddyfile 是另一个服务器类型，请使用此选项来告诉它使用哪个服务器类型。

-validate

解析 Caddyfile 并退出。如果语法有效，消息将被打印到 stdout 和进程日志(如果有的话)，并将退出，状态为0。如果不是，将返回一个非零退出状态的错误。

-version

打印版本。它也可以打印生成信息，如果不是从标记的版本。印刷后，Caddy将终止;如果使用此选项，则不提供站点。

### Signals 信号

在符合 posix的 系统中，Caddy可以通过 Signals 信号来控制。在这里，我们粗略地列出它们，从最有力的动作到强制的动作。

- TERM

强行退出而不执行关机挂钩。

- INT

在执行关闭钩子后，强制退出流程。这是在 Windows 上运行的唯一“信号”(Ctrl + C)。即使关闭钩子仍在运行，第二个 SIGINT 也会立即终止。

HUP

强制地停止服务器，但不会执行关闭钩子。.

QUIT

在执行关闭钩子后，强制地停止服务器。

USR1

重新加载配置文件，然后强制地重新启动服务器。


### 短 Caddyfile

Caddy 也接受非标记参数，这被理解为速记 Caddyfile 文本。这对于快速的临时服务器实例非常有用。
每个未标记的参数是用于服务默认主机和端口的 Caddyfile 中的一行。记得在引号中包含空格或其他特殊字符。

例如，让您浏览默认主机和端口上文件的服务器:

```
$ caddy browse
```

在一个自定义端口上即时地为 markdown 文件提供服务:

```
$ caddy -port 8080 markdown
```

带有访问日志，实现上述功能：

```
$ caddy -port 8080 browse markdown "log access.log"
```

此简易功能仅用于快速，简单的配置。

### Pipe a Caddyfile


高级用户可能希望将 Caddyfile 的内容从开发环境中导入 Caddy 。如果您在Caddyfile 中使用 pipe，则必须使用带有 `stdin` 值的 `-conf` 标志，例如

```
$ echo "localhost:1234" | caddy -conf stdin
```

当您使用从您可以控制的父进程中动态生成的 Caddyfile 启动 Caddy 时，配置 Caddyfile 是非常方便的。


{{< warning title="警告" >}}
如果您在 Caddyfile 中使用 pipe，则稍后在程序中将无法从 stdin 读取，因为父进程必须发送 EOF才能关闭 pipe，以便 Caddy 可以解除阻止并开始投放。这将导致问题，例如，如果 Caddy 必须提示您提供电子邮件地址或协议条款。所以当 pipe 输入时，使用标志来避免后来需要 stdin（例如 -email 标志）
{{< /warning >}}

### 环境变量

凯蒂识别某些环境变量。



- HOME

主文件夹。如果使用托管 TLS（自动HTTPS），则可以在此处创建一个.caddy文件夹，并且可能在将来会持续其他状态，或者配置为这样做。

CADDYPATH

如果设置，则Caddy将使用此文件夹来存储资源，而不是默认的$HOME/.caddy。

CASE_SENSITIVE_PATH

如果0或者false，Caddy会在访问文件系统上的资产或匹配中间件处理程序的请求时，以不区分大小写的方式处理请求路径。默认值为1 / true（区分大小写的路径）

退出代码 

- 0 - 正常或预期退出
- 1 - 服务器完成启动之前的错误
- 2 - 双重SIGINT（强制退出）
- 3 - 用SIGQUIT错误停止
- 4 - 关闭回调（s）返回错误

需要说明的是，如果退出状态为1，则不能自动重新启动。

## Caddyfile 语法

这个页面描述了Caddyfile的语法。如果这是你第一次写Caddyfile，试试 [Caddyfile引导教程](/get-started/#caddyfile)。此页面不适用于初学者; 它是技术性的，有点无聊。


虽然这篇文章是冗长的，但是 Caddyfile 被设计成易读的。你会发现很容易记住，不麻烦，而且容易写出。

术语 “Caddyfile” 通常指的是一个文件，但更一般地意味着一个 Caddy 的配置文本。Caddyfile 可用于配置任何 Caddy 服务器类型：HTTP，DNS等。Carddyfile 的基本结构和语法对于所有服务器类型都是相同的，但会有语义变化。由于这种变化性，本文档仅将 Caddyfile 视为适用于所有服务器类型的通用配置语法。可以在各自的文档中找到具体类型的 Caddyfile文 档。例如，HTTP服务器记录其Caddyfile 的语义。

**主题**

- 1. 文件格式和编码
- 2. 词汇语法
- 3. 结构体
- 4. 标签
- 5. 指令
- 6. 环境变量
- 7. 进口
- 8. 例子

### 文件格式和编码

Caddyfile 是用 UTF-8 编码的纯 Unicode 文本。每个代码点是不同的; 具体来说，小写和大写字符是不同的。前导字节顺序标记（0xFEFF）（如果存在）将被忽略。

### 词汇语法

`token` 是在Caddyfile空格分隔的字符的序列。以引号开始的令牌从`"`字面上读取（包括空格），直到`"`未转义的下一个引号实例。引用文字可以像这样反斜杠转义：`\"`。只有引号是可以避免的。“自动引用”无效。

- 行

`\n`仅用（换行）字符分隔。`\r`除非引用，否则回车将被丢弃。空白、未引号的行是允许和忽略的。

注释被词法分析器丢弃。注释以不引用的哈希开始，#并继续到行尾。注释可能会开始一行或出现在行的中间作为不引用标记的一部分。为了本文档的目的，不再考虑注释和空白行。

然后由解析器对结构进行令牌的评估。

- 注释

注释被解析器丢弃。注释以未引用的 哈希 `#` 开头，并继续到该行的末尾。注释可以开始一行，或出现在行的中间作为不引用标记的一部分。为了本文档的目的，不再考虑注释和空白行。


然后由解析器对 token 进行评估。

### 结构体

Caddyfile 没有全局作用域。Caddyfile 中最全局的单元是一个**entry 条目**。条目由一个标签列表和一个与这些标签关联的定义组成。**label 标签**是一个字符串标识符，**definition 定义**是一个集合在一起的 token 的主体(一个或多个行):

```
list of labels
    definition (block)

```

只有一个条目的 Caddyfile 可以只包含标签行，然后在下一行上定义，如下所示。然而，一个包含多个条目的 Caddyfile 必须将每个定义包含在大括号`{ }`中。打开的花括号`{`必须在标签行的末尾，而结束的花括号`}`必须是这一行的唯一标记:

```
list of labels {
    definition (block)
}
list of labels {
    definition (block)
}
```

在大括号内的块内建议使用一致的标签缩进。

Caddyfile 的第一行始终是一个标签行。注释行、空行和导入行会导致异常。

- Labels 标签

标签是出现在块外的唯一标记（除了一个例外是导入指令）。标签行可能只有一个标签：

```
label
```

或多个标签，以空格分隔：

```
label1 label2 ...
```

如果许多标签要封闭，标签可能会以逗号后缀。号后缀的标签后面可以带有换行符，在这种情况下，下一行将被视为同一行的一部分：

```
label1,
label2
```

混合使用也是可以的（但不建议），只要行的最后一个标签有一个逗号，如果下一行继续是标签列表：

```
label1 label2,
label3, label4,
label5
```

一个带有多个标签的定义被复制到每个标签上，就好像它们是单独定义的，但同样定义相同。

### Directives 指令

定义的主体遵循标签行。定义体中的每一行的第一个标记是一个**directive 指令**。在同一条线上的指令之后的每一个标记都是一个**argument 参数**。参数是可选的:

```
directive1
directive2 arg1 arg2
directive3 arg3
```
逗号是不被接受的参数分隔符; 它们将被视为参数值的一部分。参数仅由同一行空格分隔。

指令可以通过打开块来跨越多行。块由花括号括起来{ }。开放的大括号{必须在指令的第一行的末尾，而关闭的大括号}必须是其行上唯一的标记：

```
directive {
    ...
}
```
在指令块内，每行的第一个token可能被视为**subdirective 子目录**或**property 属性**，具体取决于它的使用方式（其他也适用）。和以前一样，他们可以有参数：

```
directive arg1 {
    subdir arg2 arg3
    ...
}
```

子指令无法打开新的块。换句话说，不支持嵌套的指令块。如果指令块为空，则花括号应完全省略。

### Environment Variables 环境变量

任何 token （标签，指令，参数等）可能包含或仅包含一个环境变量，该变量采用 Unix 窗体或Windows 窗体，用大括号`{}`括起来，不加空格:

```
label_{$ENV_VAR_1}
directive {%ENV_VAR_2%}
```
任何一种形式都适用于任何操作系统 单个环境变量不会扩展到多个 token，参数或值。

### Import 导入



[import](/http/#import) 指令是一个特殊的例子，因为它可以出现在定义块之外。其结果是，没有任何标签可以接受“导入”的值。

在导入行中，该行将被替换为导入文件的内容，未修改。更多信息请参阅 [import 文档](/http/#import)

### 例子

一个非常简单的Caddyfile，只有一个条目：

```
label1

directive1 argument1
directive2
```

将先前的示例附加到另一个条目中，介绍了大括号的需要:

```
label1 {
	directive1 arg1
	directive2
}
label2, label3 {
	directive3 arg2
	directive4 arg3 arg4
}
```

有些人喜欢总是使用大括号，即使只有一个条目; 这很好，但不必要：

```
label1 {
	directive1 arg1
	directive2
}
```
例如，指令打开一个块:

```
label1

directive arg1 {
    subdir arg2 arg3
}
directive arg4
```

类似地，但在一个缩进的定义体中，并有一个注释:


```
label1 {
	directive1 arg1
	directive2 arg2 {
	    subdir1 arg3 arg4
	    subdir2
	    # nested blocks not supported
	}
	directive3
}
```