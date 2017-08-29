---
title: 技术规范
---


## 技术规范

### 支持的PSR规范

* 基于 PSR-4 规范实现 autoload
* 基于 PSR-1 规范的代码风格

## 依赖的 Package

Notadd Framework 基于 Composer 构建，并使用 Composer 组织代码。

## 目录结构说明

### 整站目录说明

```
# wwwroot                            网站根目录
    # extensions                     插件根目录
    # modules                        模块根目录
    # statics                        静态资源目录
        # assets                     前端资源目录
        # uploads                    上传目录
        # favicon.ico                ICON图标文件
    # public                         入口文件目录
        # index.php                  入口文件
        # .htacess                   Apache Rewrite
    # storage                        缓存目录
        # enviroments                环境变量目录
        # enviroment.yaml            环境变量文件
    # vendor                         第三方类库目录
```

### 示例插件目录说明

```
# extensions\vendor\brick-carving    BrickCarving插件目录
    # src                            插件源码目录
    # resources                      插件静态资源目录
    # vendor                         第三方类库目录
    # composer.json                  插件Composer文件
```

## 如何注入中间件

在 IoC 模式中，主要的核心点除了容器实例外，另一个有特色的地方，就是中间件，中间件用于 HTTP 请求的过滤和预处理。

在 Laravel 的项目中，想要扩展或添加自己的中间件，需要在类 app/Http/Kernel.php 中的数组属性 $routeMiddleware 中添加自己的中间件， 详情请参阅 Laravel 的官方文档。

而在基于 Notadd 的模块或插件中，修改 Notadd 底层的代码，显得不那么优雅，在 Notadd 更新后，修改的代码部分会被覆盖，而我们有一个更加优雅和高级的实现方式，通过 router 组件的容器实例直接实现中间件的添加，具体操作为：

1、对于模块，在类 ModuleServiceProvider 的 boot 方法中，添加如下代码：

```php
$this->app->make('router')->aliasMiddleware($name, $class); // $name 指代中间名字，$class 指代中间件类。
```

2、对于插件，在类 Extension 的 boot 方法中，添加如下代码：

```php
$this->app->make('router')->aliasMiddleware($name, $class); // $name 指代中间名字，$class 指代中间件类。
```

没错，在插件中，也是可以添加自己的中间件，而不仅仅在模块中允许添加。
