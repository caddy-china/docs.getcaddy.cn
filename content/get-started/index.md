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

Caddyfile 是一个配置 Caddy 运行方式的文本文件。

**Caddyfile 迪一行始终是要提供站点的地址**，例如：

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

一些指令需要比一行更多的配置。对于这些指令，您可以打开一个块并设置更多参数，开放的大括号必须独占一行结束：

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

和指令一样，开启的大括号必须在同一行的末尾，关闭大括号必须独占一行。**所有的指令必须在网站的定义中**。

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

请注意，使用站点地址中的路径将以最长匹配的前缀路由请求。如果你的基本路径是目录，则可以使用正斜线 `/` 后缀路径。

在地址和参数中允许使用环境变量。它们必须使用大括号括起来，你可以使用 Unix 或者 Windows 变量格式：

```
localhost:{$PORT}
root {%SITE_ROOT%}
```

任何语法都可以在任意平台上运行。单个环境变量不会拓展到多个参数／值。

Caddyfile 中 **没有继承或脚本**，**你可能不会多次指定相同的站点地址**。是的，这意味着有时你需要多 复制+粘贴 几条重复的行。如果你有很多重复的行，可以使用 [import](import) 指令来减少重复。

好吧，这该足够让你了解 Caddy 文档，去征服吧！
