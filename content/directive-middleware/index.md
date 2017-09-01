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

- **url** 是您将访问文件管理器的URL路径。默认为/。

- **database** 是存储设置的数据库的路径。

- **no_auth** 禁用身份验证

以下选项只是默认值：它们将仅用作新用户的默认选项。创建用户后，其设置应通过 Web UI 进行更改。当使用 no_auth 选项时，以下将定义用户权限。

范围是您要浏览的目录的路径，相对或绝对，默认为./。
语言环境是新用户（可用语言）的默认语言。

- **allow_commands** allow commands选项的默认值。

- **allow_edit** allow edit选项的默认值。

- **allow_new** allow new 选项的默认值。

- **allow_publish** allow publish选项的默认值。

命令是默认的可用命令。

- **css** 是具有自定义样式表的文件的路径


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

该中间件基于JSON Web令牌（JWT）实现了 Caddy 的授权层。您可以在[jwt.io]（https://jwt.io）上了解有关在应用程序中使用JWT的更多信息。




### 基本语法

```
jwt [path]
```

默认情况下，路径下的所有资源将使用JWT验证进行安全保护。要指定需要保护的资源列表，请使用多个声明。请务必阅读插件文档，以正确配置服务器来验证您的 token 。

{{< note title="重要" >}}
您必须在名为`JWT_SECRET`（HMAC）*或*`JWT_PUBLIC_KEY`（RSA）的环境变量中设置用于构建您的令牌的秘密。否则，您的 token 将默认无法验证。球童将在没有这个值的情况下启动，但是必须在签名请求验证时出现。
{{< /note >}}


### 高级语法

您可以选择使用声明信息来进一步控制对路线的访问。在"jwt"块中，您可以根据声明的值指定允许或拒绝访问的规则。
如果声明是 json 数组的字符串，则 allow 和 deny 指令将检查数组是否包含指定的字符串值。如果数组中的任何值匹配，则允许或拒绝规则将生效。

```
jwt {
   path [path]
   redirect [location]
   allow [claim] [value]
   deny [claim] [value]
}
```
要基于声明授权访问，请使用`allow`语法。要拒绝访问，请使用`deny`关键字。您可以使用多个关键字来实现复杂的访问规则。如果任何`allow`访问规则返回true，则允许访问。如果“deny”规则为真，访问将被拒绝。拒绝规则将允许该声明的任何其他值。   

  例如，假设你有一个带有"user：someone"和"role：member"的令牌。如果您有以下访问块：

```
jwt {
   path [path]
   redirect [location]
   allow [claim] [value]
   deny [claim] [value]
}
```

中间件将以”role：member“的形式拒绝所有人，但将允许具有特定用户名为”someone“的用户。允许使用“role：admin”或 ”role：foo”的不同用户，因为拒绝规则将允许任何没有角色成员的用户。

如果设置了可选的“redirect”，则如果访问被拒绝，中间件将发送重定向到所提供的位置（HTTP 303）而不是访问被拒绝的代码。

### 传递令牌进行验证的方式

有三种方式传递令牌进行验证：（1）在“授权”标题中，（2）作为cookie，（3）作为URL查询参数。中间件将按照列出的顺序查找那些位置，如果找不到任何令牌，则返回“401”。


| Method               | Format                          |
| -------------------- | ------------------------------- |
| 授权标题 | `Authorization: Bearer <token>` |
| Cookie               | `"jwt_token": <token>`          |
| URL 查询参数| `/protected?token=<token>`      |

### 构造一个有效的标记

JWT由三部分组成：标题，声明和签名。为了正确构建JWT，建议您使用适合您语言的JWT库。至少，此授权中间件期望存在以下字段：

**header**

```JSON
{
“typ”：“JWT”，
“alg”：“HS256 | HS384 | HS512 | RS256 | RS384 | RS512”
}
```

**声明**

如果要限制您的令牌的有效期到某个时间段，请使用 “exp” 字段声明令牌的到期时间。这个时间应该是整数格式的 Unix 时间戳。
```JSON
{
“exp”：1460192076
}
```
### 代理令牌中的声明

您可以在索赔部分添加额外的索赔。一旦令牌被验证，您所包含的声明将作为标题传递给下游资源。由于token 已由 Caddy 验证，您可以放心，这些标题代表您的令牌的有效声明。例如，如果您在令牌中包含以下声明：


```json
{
  "user": "test",
  "role": "admin",
  "logins": 10,
  "groups": ["user", "operator"],
  "data": {
    "payload": "something"
  }
}
```
以下标头将被添加到代理到您的应用程序的请求中：

```
Token-Claim-User: test
Token-Claim-Role: admin
Token-Claim-Logins: 10
Token-Claim-Groups: user,operator
Token-Claim-Data.payload: something
```
token声明将始终转换为字符串。如果您希望您的声明是另一种类型，请记住在使用声明之前将其转换回来。嵌套的JSON对象将被格式化。在上面的示例中，您可以看到嵌套的“payload”字段被转化为“data.payload”。


所有前缀为`Token-Claims -`的请求头都被从上游转发出去，所以用户不能欺骗他们。

HTTP 标头中不允许使用特殊字符的声明将被转义为 URL。例如， Auth0 要求将索引命名为完整的URL，例如

```json
{
  "http://example.com/user": "test"
}
```
URL转义会导致一些难以理解的 header

```
Token-Claims-Http：％2F％2Fexample.com％2Fuser：test
```

如果您只关心路径的最后一部分，您可以使用`strip_header`指令在路径的最后一部分之前删除所有内容。

```
jwt {
  path /
  strip_header
}
```
当结合上述权利要求时，它将导致 header

```
Token-Claim-User: test
```

### 允许公共访问某些路径

在某些情况下，您可能希望允许公开访问特定路径，而不使用有效的 token。例如，您可能希望保护所有路由，除了访问`/login`路径。你可以用`except`指令来做到这一点。

```
jwt {
  path /
  except /login
}
```

以`/login`开头的每个路径将从JWT令牌要求中除外。所有其他路径将被保护。在您将路径设置为根目录的情况下，您还可能希望允许访问所谓的裸机或根域，同时保护其他所有内容。您可以使用允许访问裸域的“allowroot”指令。例如，如果您有以下配置块:

```
jwt {
  path /
  except /login
  allowroot
}
```
对"https://example.com/login" 和 "https://example.com/" 的请求都将被允许，而不需要有效的 token。任何其他路径将需要一个有效的 token。


### 允许公共访问不需要令牌

在某些情况下，无论是否存在有效的令牌，都可以访问页面。一个例子可能是Github主页或公共存储库，即使是注销用户也应该可见。在这种情况下，您需要解析可能存在的任何有效的令牌，并将声明传递给应用程序，并将其留给应用程序以决定用户是否可以访问。您可以使用指令`passthrough`为此：

```
jwt {
  path /
  passthrough
}
```
应该注意的是，“passthrough”将始终允许访问所提供的路径，而不管一个令牌是否存在或有效，以及不考虑`allow` /`deny`指令。应用程序将负责处理已解析的请求。


### 指定用于验证令牌的密钥

指定用于验证令牌的关键材料有两种方法。如果您在容器中运行Caddy或通过像 Systemd 这样的 init 系统运行，可以使用 HMAC 的环境变量`JWT_SECRET`或用于RSA（PEM编码的公钥）的`JWT_PUBLIC_KEY`来直接指定密钥。您不能同时使用这两者，因为它会在 JWT 规范中打开一个已知的安全漏洞。当您运行多个站点时，所有这些站点都必须使用相同的密钥来验证令牌。

当您从一个Caddyfile运行多个站点时，您可以指定包含PEM编码的公钥或HMAC密码的文件的位置。再次，您不能同时使用这两个网站，因为它会导致安全漏洞。但是，您可以在不同的站点上使用不同的方法，因为配置是独立的。

对于 RSA tokens:

```
jwt {
  path /
  publickey /path/to/key.pem
} 
```

对于 HMAC:

```
jwt {
  path /
  secret /path/to/secret.txt
}
```


当您将密钥材料存储在文件中时，此中间件将缓存结果，并使用文件上的修改时间来确定自上次请求以来密码是否已更改。这允许您在不担心文件锁定问题的情况下，通过编写新的密钥来旋转您的密钥或使令牌无效(尽管您仍然应该检查您的写是否成功，然后使用新密钥发出令 token)。

如果您有多个公共密匙或秘密，应该被认为是有效的，那么在不同的文件中使用多个声明来处理密钥或秘密。如果任何密钥验证了 token ，将允许授权。。


```
jwt {
  path /
  publickey /path/to/key1.pem
  publickey /path/to/key2.pem
}
```


### 可能的错误状态码

| 代码| 原因|
| ---- | ------ |
| 401 | 未授权 - 无令牌，令牌失败验证，令牌已过期|
| 403 | 禁止 - 令牌有效，但被拒绝，因为允许或DENY规则|
| 303 | 返回401或403，并重新启动。这优先于401或403状态。|

###



{{< warning title="警告" >}}
JWT验证只取决于验证正确的签名，并且令牌未到期。您还可以设置`nbf`字段，以防止在某个时间戳之前进行验证。规范中的其他字段，如 "aud"，"iss，"sub"，"iat"和"jti" 不会影响验证步骤。
{{< /warning >}}

## http.login

基于 github.com/tarent/loginsrv 的 Caddy 登录指令。根据后端检查登录名，然后返回为 JWT 令牌。该指令旨在与 http.jwt 中间件一起使用。

支持以下提供程序（登录后端）：

- Htpasswd
- OSIAM
- Simple (配置用户/密码对)
- Github, Google OAuth2 登录

### 例子

**简单例子**

```
jwt {
    path /
    allow sub bob
}

login / {
         simple bob=secret,alice=secret
}
```

根上下文 / 由 jwt 中间件保护。用户 alice 和 bob 可以登录

**htpasswd 文件 用户**

```
header /private Cache-Control "no-cache, no-store, must-revalidate"
  
jwt {
  path /private
  redirect /login
  allow sub demo
}

login {
  success_url /private
  htpasswd file=passwords
}
```

保护路径 /private 。用户在 htpasswd 文件中 。

**Github 示例更多的定制**

```
jwt {
  path /my-account
  redirect /login
}

login {
  github client_id={$github_client_id},client_secret={$github_client_secret}

  success_url /my-account
  logout_url /
  template login_template.html
  jwt_expiry 24h
  cookie_expiry 2400h
}
```
Githu b登录，从环境变量中获取 Github api 凭证。模板、重定向 url 和过期时间配置。






## http.mailout

## http.minify

Caddy 插件，可实时实现CSS，HTML，JSON，SVG和XML的压缩。它使用[tdewolff的库]（https://github.com/tdewolff/minify）

### 语法

```
minify paths...  {
    if          a cond b
    if_op       [and|or]
    disable     [js|css|html|json|svg|xml]
    minifier    option value
}
```

+ **paths** 是空格分隔的文件路径来压缩。如果没有指定，整个网站将被细化。
+ **if** 指定条件。默认情况下，多个if一起合并。**a** 和 **b** 是任何字符串，可以使用[请求占位符]（/http-server/#placeholders）。**cond**是条件，可能的值在[rewrite]（/http/#rewrite）中解释（也有一个`if`语句）。
+ ** if_op ** 指定如何评估ifs; 默认值为`and`。
+ ** disable ** 用于指示要禁用哪些minifiers; 默认情况下，它们都被激活。
+ **minifier** 设置**value** ** **option**在该分组。当选项为 true 或 false 时，其省略被称为"true"。可能的选项如下。

### Minifiers选项

| 文件后缀| 选项| 价值| 说明|
| ------------- | ------------- | ---------- ----------- |
| css，svg | decimals| number| 保留默认属性值。|
| xml，html | keep_whitespace | true \ | false | 保存`html`，`head`和`body`标签。|
| html | keep_end_tags | true \ | false | 保留所有结束标签。|
| html | keep_document_tags | true \ | false | 在内联标签之间保留空格，但仍将多个空格字符折叠成一个。|
| html | keep_default_attr_vals | true \ | false | 保留默认值属性。|
| html | keep_conditional_comments | true \ | false | 保留所有IE条件注释。|

有关每个选项以及每个分选程序如何工作的更多信息，请阅读[tdewolff / minify的文档]（https://github.com/tdewolff/minify/blob/master/README.md）。

#＃ 例子

压缩网站的所有受支持文件：

```
Minify all of the supported files of the website:
```

只压缩`/ assets`文件夹的内容：

```
minify /assets
```

只有minify css文件：

```
minify {
    disable html svg json xml js
}
```

缩小除`/api`之外的整个网站：

```
minify  {
    if {path} not_match ^(\/api).*
}
```
压缩`/ assets'文件夹的文件，除了`/ assets / js`：

```
minify /assets {
    if {path} not_match ^(\/assets\/js).*
}
```


## http.nobots

## http.prometheus

## http.proxyprotocol

## http.ratelimit

## http.realip

## http.reauth

## http.restic

## http.upload

## http.webdav
