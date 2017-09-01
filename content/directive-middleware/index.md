## http.authz

## http.awses

## http.awslambda

## http.cache

## http.cgi

该插件实现了Caddy的通用网关接口（CGI）。它可以通过命令行脚本在您的网站上生成动态内容。如果要收集有关入站 HTTP 请求的信息，比如您的脚本会检查某些环境变量，例如 PATH_INFO 和 QUERY_STRING。然后，要将动态生成的网页返回给客户端，您的脚本只需将内容写入标准输出中即可。在POST请求的情况下，您的脚本从标准输入读取额外的入站内容。

### 例子

CGI 脚本

```
In the Caddyfile:

  cgi /report /usr/local/cgi-bin/report

In /usr/local/cgi-bin/report:

  #!/bin/bash

  printf "Content-type: text/plain\n\n"

  printf "PATH_INFO    [%s]\n" $PATH_INFO
  printf "QUERY_STRING [%s]\n" $QUERY_STRING

  exit 0
  ```

当 https://example.com/report或https://example.com/report/weekly 等请求到达时，cgi中间件将检测到匹配项，并调用名为/usr/local/cgi-bin/report 的脚本。

环境变量 PATH_INFO 和 QUERY_STRING 被填充并自动传递给脚本。文档中描述了包含许多其他标准CGI变量。如果您需要传递任何特殊的环境变量，或者允许任何属于 Caddy 进程的环境变量传递给您的脚本，则需要使用文档中描述的高级指令语法。

[完整文档](https://jung-kurt.github.io/cgi/)


## http.cors

支持跨源资源共享

### 例子

**简单的使用**

```
cors
```

允许所有来源访问所有资源

**只允许某些域名**

```
cors / http://mytrusteddomain.tld http://myotherdomain.com
```
只允许来自几个特定域名的跨域请求

**完整配置**

```
cors / {
    origin            http://allowedSite.com
    origin            http://anotherSite.org https://anotherSite.org
    methods           POST,PUT
    allow_credentials false
    max_age           3600
    allowed_headers   X-Custom-Header,X-Foobar
    exposed_headers   X-Something-Special,SomethingElse
}
```
显示所有可用选项的示例

## http.datadog

## http.expires

## http.filemanager

filemanager 是基于浏览中间件的扩展。它提供指定目录中的文件管理界面，可用于上传，删除，预览和重命名该目录中的文件。

如果您遇到处理大文件的问题，您可能需要检查该 [timeouts 插件](/http/#timeout)，该插件可用于更改默认 HTTP 超时。

### 例子

**基本用法**

```
filemanager
```
显示在域的根处执行Caddy的目录。

**浏览具体路径**

```
filemanager / ./foo
```
显示`foo`域的根目录内容。

**浏览特定目录**

```
filemanager /filemanager
```

显示 Caddy 执行的目录`/filemanager`。

{{< note title="注意" >}}
默认账号密码为 `admin`
{{< /note >}}

### 语法

```
filemanager [url] [scope] {
    database        path
    no_auth
    locale          [en|jp|...]
    allow_commands  [true|false]
    allow_edit      [true|false]
    allow_new       [true|false]
    allow_publish   [true|false]
    commands        cmd1 cmd2...
    css             path
}
```

- url

是您将访问文件管理器的URL路径。默认为/。

- database

是存储设置的数据库的路径。

- no_auth

禁用身份验证

以下选项只是默认值：它们将仅用作新用户的默认选项。创建用户后，其设置应通过 Web UI 进行更改。当使用 no_auth 选项时，以下将定义用户权限。

范围是您要浏览的目录的路径，相对或绝对，默认为./。
语言环境是新用户（可用语言）的默认语言。

- allow_commands

allow commands选项的默认值。

- allow_edit

allow edit选项的默认值。

- allow_new
 
 allow new 选项的默认值。

- allow_publish 

allow publish选项的默认值。

命令是默认的可用命令。

- css 

具有自定义样式表的文件的路径


如果您使用`http.hugo`，`http.jekyll` 或任何其他使用文件管理器作为基础的插件，初始语法略有不同。这个范围和 url 选项是颠倒的，你应该写插件的名字而不是 filemanager 。

### 关于数据库

默认情况下，数据库将被保存在 `.caddy` 目录，在一个名为 `filemanager` 的子目录中。每个文件名称都是主机和基本`URL`的组合的散列。

如果您不设置数据库路径，并且更改主机或基本 URL，那么您的设置将被重置。因此，强烈建议您设置此选项。当您不设置它时，您将收到一个警告，告诉您当前数据库应该使用的值。

当您设置相对路径时，它将始终与`.caddy/filemanager` 目录相关。尽管如果您希望将数据库存储在其他位置，您也可以使用绝对路径。


## http.filter

## http.forwardproxy

## http.git

git 插件可以通过简单的 git push 来部署您的站点。

git 指令启动在服务器的生命周期中运行的服务例程。当服务启动时，它会克隆存储库。当服务器还在运行时，它会拉取最新的东西。你也可以设置一个 webhook 在 push 后立即拉取。以正常的 Git 方式，pull只包含更改，所以它非常有效。

[完整文档-英文](https://github.com/abiosoft/caddy-git/blob/master/README.md)

### 例子

**基本语法**

```
git repo [path]
```
- **repo** 是存储库的URL; 支持 SSH 和 HTTPS URL。
- **path** 是相对于站点根的路径，将存储库克隆到 默认是站点根。

这种简化的语法从主机每3600秒（1小时）抽取，只适用于公开存储库。

**完整的语法**

```
git [repo path] {
	repo        repo
	path        path
	branch      branch
	key         key
	interval    interval
	clone_args  args
	pull_args   args
	hook        path secret
	hook_type   type
	then        command [args...]
	then_long   command [args...]
}
```
- **repo** 是存储库的URL; 支持SSH和HTTPS URL。

- **path** 是将存储库克隆到的路径; 默认是站点根。它可以是绝对的或相对的（到站点根）。

- **branch** 是分支或标签; 默认是主分支。`{latest}` 是最新标签的占位符，可确保最新的标签始终拉取。

- **key** 是SSH私钥的路径; 适用于私有存储库。

- **interval** 是拉取间隔的秒数; 默认为3600（1小时），最小为5.间隔-1时候禁用周期性拉取。

- **clone_args** 是额外的 cli 参数,传递给 `git clone` 例如：`--depth=1`。`git clone` 当第一次获取源时调用。

- **pull_args** 是额外的cli args 传递`给git pull` 例如：  `-s recursive -X theirs`。`git pull` 当源被更新时使用。

- **path 和 secret** 用于创建一个 webhook，当 push 后可拉取最新代码 。这仅限于支持的webhooks。目前，Github，Gitlab 和 Travis 挂钩仅支持秘密。

- **type** 是 webhook 类型的使用。webhook类型是默认自动检测的，但它可以被显式设置为一个受支持的 webhook。这是通用 webhook 的一个要求。

- **command** 是成功拉取后执行的命令;然后是**args**，任何参数都可以传递给命令。对于多个命令，您可以有多个这样的行。**then_long**是用于长时间执行的命令，应该在后台运行。

块中的每个属性都是可选的。路径和repo可以在第一行中指定，就像在第一个语法中一样，或者可以在块中指定其他值。

**基本例子**

```
git github.com/user/myproject subfolder
```
公共存储库拉入 站点根目录下的`subfolder` 

**更多的控制**

```
git {
	repo     git@github.com:user/myproject
	branch   v1.0
	key      /home/user/.ssh/id_rsa
	path     subfolder
	interval 86400
}
```
私有库每天被拉入`subfolder`目录`v1.0`一次。

**拉取后执行命令**

```
git github.com/user/site {
	path  ../
	then  hugo --destination=/home/user/hugosite/public
}
```

此示例在每次拉取后都会生成 `hugo` 的静态站点。

**指定一个webhook**

```
git git@github.com:user/site {
	hook /webhook secret-password
}
```

`/webhook` 是路径，`secret-password` 是 hook 密码（如果适用）。 Webhooks 支持 GitHub，Gitlab，BitBucket，Travis 和 Gogs。

如果您的密码包含特殊字符，您可能需要引号。

{{< note title="译者注" >}}
如果需要支持 码云、coding `hook_type  generic`
{{< /note >}}


**通用webhook**

```
{
	"ref" : "refs/heads/branch"
}
```
这是通用 webhook 的预期有效载荷。`branch` 是分支名称，如 `master`。


## http.gopkg

## http.grpc

## http.hugo

## http.ipfilter

## http.jekyll

## http.jwt

## http.login

## http.mailout

## http.minify

## http.nobots

## http.prometheus

## http.proxyprotocol

## http.ratelimit

## http.realip

## http.reauth

## http.restic

## http.upload

## http.webdav
