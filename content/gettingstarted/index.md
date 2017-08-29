---
title: 快速开始
---


## 快速开始

1. [下载](https://caddyserver.com/download)然后解压到path目录（Linux一般是/bin/ win一般在环境变量中的path对应目录）


2.打开网站所在目录

3. 运行`caddy`


打开浏览器 http://localhost:2015  默认情况下， Caddy 运行以当前目录为网站目录

如果您看到404错误，则证明Caddy正在工作，但您的站点的根目录缺少索引文件（index.html/index.php等）。（需要更多的指导？阅读初学者教程。）


接下来，学习使用Caddyfile配置Caddy

## 初学者教程

本教程将帮助您第一次安装，运行和配置Caddy。假设你从来没有使用过Web服务器！（如果有，快速启动。）尽管 Caddy 很容易使用，但仍然希望你能够熟悉它：

- 提取，移动和重命名文件
- 管理用户和文件权限
- 使用终端或命令行
- 配置防火墙

有了这些前提条件，说明你已经准备好了。

### 下载

从下载页面[下载Caddy](https://caddyserver.com/download)。几乎任何操作系统和架构都可以使用 Caddy 。Caddy 的下载页面与其他的Web服务器相比是独一无二的：它允许您使用插件自定义您的构建。

对于本教程，您不需要任何插件。

有时我们的构建服务器进行维护。如果下载页面关闭，您可以随时从GitHub[下载最新版本](https://github.com/mholt/caddy/releases/latest)（不需要插件）。

### 安装

您下载的文件是压缩档案。您将需要提取Caddy二进制文件（可执行文件）。

#### Windows

```
将zip文件解压到目录
```

#### MacOS

```bash
unzip caddy*.zip caddy
```

#### Linux

```bash
tar -xzf caddy*.tar.gz caddy
```

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




