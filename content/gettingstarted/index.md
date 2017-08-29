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


``
