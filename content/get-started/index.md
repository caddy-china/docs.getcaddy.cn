---
title: 快速开始
---

<a name="quick-start" />
## 快速开始 {#quick-start}

1. [下载 Caddy](https://caddyserver.com/download) 并将其放在你的 **PATH** （环境变量） 当中。
2. `cd` 到你网站的目录。
3. 运行 `caddy` 。

完成！从你的浏览器打开 `http://localhost:2015` 看一下是否工作。默认情况下，Caddy 服务器工作在当前目录下。

如果你看到 404 错误，则 Caddy 正在工作，但是你的站点的根目录缺少索引文件。（需要更多的指导？阅读[初学者教程](#beginner)）


接下来，[学习使用 Caddyfile 配置 Caddy](#caddyfile)。

<a name="beginner" />
## 初学者教程 {#beginner}

本教程版主你第一次安装、运行和配置 Caddy。假设你从来没有使用过 web 服务器！（如果有，操作[快速开始](#quick-start)）尽管 Caddy 很容易使用，但是仍然希望你能已经熟悉使用你的机器。

- 提取，移动和重命名文件
- 管理用户和文件权限
- 使用终端或命令行
- 配置防火墙

有了这些前提条件，说明你已经准备好了。

### **Topics** {#topic}

1. [下载](#download)
2. [安装](#install)
3. [运行](#run)
4. [配置](#configure)

<a name="download" />
#### 下载 {#download}

从 [下载页面](https://caddyserver.com/download) 下载 Caddy。你可以获得几乎任何系统和架构的 Caddy。Caddy 下载页面和其他 web 服务器一样都是唯一的：它运行你使用插件自定义你的构建。

对于本教程，你不需要任何插件。

又是我们对构建服务器进行维护。如果下载页面关闭，你可以随时从 [GitHub](https://github.com/mholt/caddy) 下载 [最新版本](https://github.com/mholt/caddy/releases/latest)

<a name="install" />
#### 安装 {#install}

你下载的文件是压缩包。你需要提取 Caddy 二进制文件（可执行文件）。

| Windows | macOS | Linux |
|----|----|-----|
| 
| 右键 .zip 文件，然后选择「全部提取」。选择要提取的到的任意文件夹，只要你不取消。完成后你可以删除。 | 双击 .zip 文件夹，或者运行：<br /> `unzip caddy*.zip caddy` | 运行命令：<br /> `tar -xzf caddy*.tar.gz caddy` |


接下来，我们将把Caddy二进制文件移动到一个可以轻松执行的文件夹中。

#### Windows

```
将可执行文件移动到任何容易获取的文件夹。例如，C:\Caddy。
```

#### MacOS/Linux

任何位置都可以执行：

```bash
mv ./caddy /usr/local/bin
```


### 运行

默认情况下，Caddy 将使用当前目录（正在执行的目录，而不是二进制文件夹的文件夹）作为站点的根目录。这使得运行本地网站变得容易！

使用终端或命令行，切换到您的站点所在的文件夹：

```bash
cd path/to/my/site
```

#### Windows

假设您将`caddy.exe`文件放在`C:\Caddy`中，运行：
```bash
C:\Caddy\caddy.exe
```

#### MacOS/Linux

```
caddy
```

在浏览器中 加载http://localhost:2015 。如果您看到404错误，则Caddy正在工作，但您的站点缺少索引文件。

您可以按Ctrl + C 退出 Caddy, 它将终止运行。

### 配置

您的网站已经运行！但这不是最终目的，因为我们将它运行在 localhost ：

1. 该网站在2015年的端口上服务，而不是80（标准HTTP端口）。
2. 该站点不受HTTPS保护。

通过告诉Caddy要提供的网站的域名，很容易解决这两个问题。通过域名，我们将使用 example.com （使用您的真实域名）。如果可以访问服务器的 80 和 443 端口，则面将正常工作，而您的域名可以指向您的服务器。如果只是在本地电脑运行，或者您没有真正的域名，请 localhost 用作您的域名。

使用命令指定域名：

#### Windows

```bash
C:\Caddy\caddy.exe -host example.com
```

#### MacOS/Linux

```bash
caddy -host example.com
```

当您第一次使用真实的域名（不是localhost）运行 Caddy 时，系统会要求您输入您的电子邮件地址。这是因为 Caddy 需要验证您是否拥有该域名，并自动申请SSL证书。

提交电子邮件地址后，您会看到类似的错误permission denied？这是因为，Caddy正试图绑定 80 和 443 端口，但这样做需要root或管理员权限：

#### Windows

右键单击cmd.exe，然后单击“以管理员身份运行”。然后再运行 Caddy:

```
C:\Caddy\caddy.exe -host example.com
```

#### Macos/Linux

使用sudo以root方式运行Caddy：

```bash
sudo caddy -host example.com
```

在正式 Linux 服务器上

```bash
sudo setcap cap_net_bind_service=+ep $(which caddy)
caddy -host example.com
```

如果您有权限，并运行 Caddy ，您会看到：

```bash
Activating privacy features... done.
https://example.com
http://example.com
```

使用真实的域名触发了 Caddy 的 HTTPS 功能，它们在 80 和443 端口上运行。如果您只是使用 localhost 主机名，Caddy将继续在 2015 的端口上运行，除非您使用 `-port` 选项进行更改。

在命令行界面可以快速配置 Caddy。但是如果你想每次都使用同一个配置怎么办？ Caddyfile ！

Caddyfile 是一个文本文件，告诉 Caddy 如何服务。我们来做一个：

#### Windows

在您的网站的文件夹中创建一个名为 Caddyfile（扩展名不为.txt）的文件，并在其中放置一行（使用您的实际域名或localhost）：

```bash
example.com
```

#### Macos/Linux

使用您的实际域名（或localhost）：

```bash
echo example.com > Caddyfile
```


运行 Caddy ，会找到当前目录的 Caddyfile 文件

```bash
caddy
```

 Caddyfile 的第一行始终是要提供的站点的域名或IP。

如果 Caddyfile 与目前的目录不一样，可以告诉 Caddy ， Caddyfile 在哪里：

#### Windows

```bash
caddy -conf C:\path\to\Caddyfile
```

#### MacOS/Linux

```bash
caddy -conf ../path/to/Caddyfile
```

学习如何使用 Caddyfile ，将会使它变得更容易。

## Caddyfile 配置

Caddyfile是一个配置Caddy运行方式的文本文件。

Caddyfile的第一行始终是要提供的站点的地址。例如：

```
localhost:8080
```

当您将其保存在 Caddyfile 的文件中时，Caddy会在您启动时寻找当前目录的 Caddyfile：

```
caddy
```

如果Caddyfile位于不同的位置或具有不同的名称，请告诉Caddy它的位置：

#### Windows

```bash
caddy -conf C:\path\to\Caddyfile
```

#### MacOS/Linux

```bash
caddy -conf ../path/to/Caddyfile
```
站点地址之后的行以指令开头。指令是凯蒂识别的关键字。例如，gzip 是一个 HTTP 指令：

```
localhost:8080
gzip
```

指令可能在它们之后有一个或多个参数：

```
localhost:8080
gzip
log ../access.log
```

一些指令需要超过一行的配置。对于这些指令，您可以打开一个指令块并设置更多的参数。开放的大括号必须在一行的末尾：

```
localhost:8080
gzip
log ../access.log
markdown /blog {
    css /blog.css
    js  /scripts.js
}
```

如果指令块为空，则应该省略大括号。

包含空格的参数必须用引号`"`括起来。

Caddyfile 以`#` 字符开头可以注释：

```
# Comments can start a line
foobar # or go at the end
```

要使用单个 Caddyfile 文件配置多个站点，您必须使用每个站点用大括号来分离：

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
与指令一样，开放的大括号必须在同一行的末尾。关闭大括号必须在自己的线上。所有指令必须出现在网站的定义中。

对于共享相同配置的站点，请指定多个地址：

```
localhost:8080, https://site.com, http://mysite.com {
    ...
}
```

站点地址也可以在特定路径下定义，或者具有通配符代替左侧的单个域标签：

```
example.com/static, *.example.com {
    ...
}
```

请注意，使用站点地址中的路径将以最长匹配的前缀路由请求。如果您的基本路径是目录，您可能希望以正斜杠后缀该路径/。

在地址和参数中允许使用环境变量。它们必须用大括号括起来，您可以使用 Unix 或 Windows 变量格式：

```
localhost:{$PORT}
root {%SITE_ROOT%}
```

这两种语法都适用于任何平台。单个环境变量不会扩展成多个参数/值。

在Caddyfile中没有继承或脚本，您可能不止一次指定同一个站点地址。是的，有时这意味着你复制+粘贴一些重复的行。如果有很多重复的行，可以使用import指令来减少重复。

更多请查看文档。



