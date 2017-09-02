## hook.service

将 caddy 作为服务运行

这个插件是由官方开发人员和Henrique Dias编码的，他是文件管理器的主要贡献者。



{{< note title="注意" >}}
注意：请注意，如果您使用不是默认值的名称安装服务，则每次使用命令使用该参数时，都需要指定该服务 `-name 自定义名称`。
{{< /note >}}



### 例子

由于官方文档的例子存在一定误导，此处没有按照原文翻译。

**安装服务**

```
caddy -service install -conf=/etc/Caddyfile 
```

`-conf=路径` 可以指定Caddyfile 的路径  

**卸载服务**

```
caddy -service uninstall  
```


**开始服务**

```
caddy -service start 
```

**停止服务**

```
caddy -service stop 
```

**重新启动服务**

```
caddy -service restart 
```