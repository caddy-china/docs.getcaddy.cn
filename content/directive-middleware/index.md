## http.authz

## http.awses

## http.awslambda

## http.cache

## http.cgi

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

## http.filter

## http.forwardproxy

## http.git

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
