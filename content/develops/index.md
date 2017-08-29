---
title: 二次开发
---

## 什么是二次开发

所谓二次开发，即是在 Notadd Framework 的基础上，对 Notadd Framework 的原有结构、原有功能进行调整或增强。

### Notadd Framework 支持进行的二次开发内容。



#### 功能性扩展说明列表

* [Administrator](#administrator) 
* [路由](#路由)
* [模块](#模块)  大功能，诸如商城、文章、微信
* [插件](#插件)  功能增强，诸如 全局短信，全局验证码。
* [拓展](#拓展)  特定环境拓展， 诸如 swoole拓展，PostgreSQL增强拓展

## Administrator

Administrator 作为唯一的网站管理实例，有着控制管理入口、分配管理职责等功能。

Notadd 的实现方式：

### 类 Administration

```php
namespace Notadd\Foundation\Administration;

use Illuminate\Container\Container;
use Illuminate\Events\Dispatcher;
use InvalidArgumentException;
use Notadd\Foundation\Administration\Abstracts\Administrator;

/**
 * Class Administration.
 */
class Administration
{
    /**
     * @var \Notadd\Foundation\Administration\Abstracts\Administrator
     */
    protected $administrator;

    /**
     * @var \Illuminate\Container\Container
     */
    protected $container;

    /**
     * @var \Illuminate\Events\Dispatcher
     */
    protected $events;

    /**
     * Administration constructor.
     *
     * @param \Illuminate\Container\Container $container
     * @param \Illuminate\Events\Dispatcher   $events
     */
    public function __construct(Container $container, Dispatcher $events)
    {
        $this->container = $container;
        $this->events = $events;
    }

    /**
     * Get administrator.
     *
     * @return \Notadd\Foundation\Administration\Abstracts\Administrator
     */
    public function getAdministrator()
    {
        return $this->administrator;
    }

    /**
     * Status of administrator's instance.
     *
     * @return bool
     */
    public function hasAdministrator()
    {
        return is_null($this->administrator) ? false : true;
    }

    /**
     * Set administrator instance.
     *
     * @param \Notadd\Foundation\Administration\Abstracts\Administrator $administrator
     *
     * @throws \InvalidArgumentException
     */
    public function setAdministrator(Administrator $administrator)
    {
        if (is_object($this->administrator)) {
            throw new InvalidArgumentException('Administrator has been Registered!');
        }
        if ($administrator instanceof Administrator) {
            $this->administrator = $administrator;
            $this->administrator->init();
        } else {
            throw new InvalidArgumentException('Administrator must be instanceof ' . Administrator::class . '!');
        }
    }
}
```

### 抽象类 Administrator

```php
namespace Notadd\Foundation\Administration\Abstracts;

use Illuminate\Events\Dispatcher;
use Illuminate\Routing\Router;
use InvalidArgumentException;

/**
 * Class Administrator.
 */
abstract class Administrator
{
    /**
     * @var \Illuminate\Events\Dispatcher
     */
    protected $events;

    /**
     * @var mixed
     */
    protected $handler;

    /**
     * @var string
     */
    protected $path;

    /**
     * @var \Illuminate\Routing\Router
     */
    protected $router;

    /**
     * Administrator constructor.
     *
     * @param \Illuminate\Events\Dispatcher $events
     * @param \Illuminate\Routing\Router    $router
     */
    public function __construct(Dispatcher $events, Router $router)
    {
        $this->events = $events;
        $this->router = $router;
    }

    /**
     * Get administration handler.
     *
     * @return mixed
     */
    public function getHandler()
    {
        return $this->handler;
    }

    /**
     * Administration route path.
     *
     * @return string
     */
    public function getPath()
    {
        return $this->path;
    }

    /**
     * Init administrator.
     *
     * @throws \InvalidArgumentException
     */
    final public function init()
    {
        if (is_null($this->path) || is_null($this->handler)) {
            throw new InvalidArgumentException('Handler or Path must be Setted!');
        }
        $this->router->group(['middleware' => 'web'], function () {
            $this->router->get($this->path, $this->handler);
        });
    }

    /**
     * Register administration handler.
     *
     * @param $handler
     */
    public function registerHandler($handler)
    {
        $this->handler = $handler;
    }

    /**
     * Register administration route path.
     *
     * @param string $path
     */
    public function registerPath($path)
    {
        $this->path = $path;
    }
}
```

### IOC 实例注册方式

```php
namespace Notadd\Administration;

use Illuminate\Support\ServiceProvider;
use Notadd\Administration\Controllers\AdminController;
use Notadd\Foundation\Administration\Administration;

/**
 * Class Extension.
 */
class ModuleServiceProvider extends ServiceProvider
{
    /**
     * Boot service provider.
     *
     * @param \Notadd\Foundation\Administration\Administration $administration
     */
    public function boot(Administration $administration)
    {
        $administrator = new Administrator($this->app['events'], $this->app['router']);
        $administrator->registerPath('admin');
        $administrator->registerHandler(AdminController::class . '@handle');
        $administration->setAdministrator($administrator);
    }
}
```

## 路由

可编程路由是 **Laravel** 的一大特性，而在 **Notadd** 中，允许以 **Event** 的形式进行扩展。

### 扩展示例

#### 第一步：

在 Module 的 ModuleServiceProvider 入口类或 Extension 的 Extension 入口类 中订阅 RouteRegister 事件：

```php
$this->app->make(Dispatcher::class)->subscribe(RouteRegister::class);
```

#### 第二步：

在类 RouteRegister 实现 router 定义，示例代码如下：

```php
namespace Notadd\Content\Listeners;

use Notadd\Content\Controllers\Api\Article\ArticleController as ArticleApiController;
use Notadd\Content\Controllers\Api\Article\ArticleTemplateController as ArticleTemplateApiController;
use Notadd\Content\Controllers\Api\Category\CategoryController as CategoryApiController;
use Notadd\Content\Controllers\Api\Category\CategoryTemplateController as CategoryTemplateApiController;
use Notadd\Content\Controllers\Api\Category\CategoryTypeController as CategoryTypeApiController;
use Notadd\Content\Controllers\Api\Page\PageController as PageApiController;
use Notadd\Content\Controllers\Api\Page\PageTemplateController as PageTemplateApiController;
use Notadd\Content\Controllers\Api\Page\PageTypeController as PageTypeApiController;
use Notadd\Content\Controllers\ArticleController;
use Notadd\Content\Controllers\Api\Article\ArticleTypeController as ArticleTypeApiController;
use Notadd\Content\Controllers\CategoryController;
use Notadd\Content\Controllers\PageController;
use Notadd\Foundation\Routing\Abstracts\RouteRegister as AbstractRouteRegister;

/**
 * Class RouteRegister.
 */
class RouteRegister extends AbstractRouteRegister
{
    /**
     * Handle Route Registrar.
     */
    public function handle()
    {
        $this->router->group(['middleware' => ['auth:api', 'web'], 'prefix' => 'api/article'], function () {
            $this->router->resource('template', ArticleTemplateApiController::class);
            $this->router->resource('type', ArticleTypeApiController::class);
            $this->router->resource('/', ArticleApiController::class);
        });
        $this->router->group(['middleware' => ['auth:api', 'web'], 'prefix' => 'api/category'], function () {
            $this->router->resource('template', CategoryTemplateApiController::class);
            $this->router->resource('type', CategoryTypeApiController::class);
            $this->router->resource('/', CategoryApiController::class);
        });
        $this->router->group(['middleware' => ['auth:api', 'web'], 'prefix' => 'api/page'], function () {
            $this->router->resource('template', PageTemplateApiController::class);
            $this->router->resource('type', PageTypeApiController::class);
            $this->router->resource('/', PageApiController::class);
        });
        $this->router->group(['middleware' => 'web', 'prefix' => 'article'], function () {
            $this->router->resource('/', ArticleController::class);
        });
        $this->router->group(['middleware' => 'web', 'prefix' => 'category'], function () {
            $this->router->resource('/', CategoryController::class);
        });
        $this->router->group(['middleware' => 'web', 'prefix' => 'page'], function () {
            $this->router->resource('/', PageController::class);
        });
    }
}
```

## Notadd 推荐的扩展方式

Notadd 是朝着可扩展功能和可扩展组件的方向发展的，但是这和传统的 Laravel 支持的扩展方式有所区别。

### 传统的 Laravel 的扩展方式

* 独立的 routes.php 实现路由的增加和修改
* 构建一个 service package ，通过 Service Provider的方式进行功能扩展和 IOC 容器实例注入

从以上两种方式可以看出，Laravel 具备很强的自扩展能力，但是也存在以下几个弊端：

* 必须修改默认代码，包括 routes 相关配置文件和 configuration 相关配置文件
* 无法彻底修改 Laravel 的功能实现

### Notadd 推荐的扩展方式

以独立的 package 形式存在的 Laravel 扩展包，传承了 composer 包管理的思想，但是没有针对可插拔做出实现，而 Notadd 的存在，正是为了解决这个问题。

* 遵循 composer 包管理规范的 package
* 不需要对源代码做出过多的修改，即可达到 package 的加载
* Module 和 Extension 两个级别的功能扩展级别

## 模块

**模块**是 Notadd 的功能实体，是区别于 **notadd/framework** 来说的，**notadd/framework** 仅是承载 Notadd 体系的逻辑实现，并没有包含功能性代码。

### 目录结构

**模块**位于目录 **modules** 下，每个模块在一个独立的文件夹内，模块内部的目录结构如下：

```
# module                                             模块目录
    # resources                                      资源目录
        # translations                               翻译文件目录
        # views                                      视图目录
    # src                                            源码目录
        # ModuleServiceProvider.php                  模块服务提供者定义文件
    # composer.json                                  Composer 配置文件
```



### Resources

Resources 目录是 Module 的资源类文件放置的目录，包含如下几个类型目录：

* assets
* translations
* views

#### Assets

assets 目录为前端相关资源或项目的放置目录。

#### Translations

translations 目录为多语言资源文件的放置目录。

#### Views

views 目录为视图资源文件的放置目录。

### ModuleServiceProvider

ModuleServiceProvider 是 Module 的模块入口文件，也 Module 的所有功能容器示例注册、路由注入等一系列功能注册及组件启动的服务提供者。

### 完整示例

```php
namespace Notadd\Content;

use Illuminate\Events\Dispatcher;
use Illuminate\Support\ServiceProvider;
use Notadd\Content\Events\RegisterArticleTemplate;
use Notadd\Content\Events\RegisterArticleType;
use Notadd\Content\Events\RegisterCategoryTemplate;
use Notadd\Content\Events\RegisterCategoryType;
use Notadd\Content\Events\RegisterPageTemplate;
use Notadd\Content\Events\RegisterPageType;
use Notadd\Content\Listeners\CsrfTokenRegister;
use Notadd\Content\Listeners\RouteRegister;
use Notadd\Content\Managers\ArticleManager;
use Notadd\Content\Managers\CategoryManager;
use Notadd\Content\Managers\PageManager;

/**
 * Class Module.
 */
class ModuleServiceProvider extends ServiceProvider
{
    /**
     * Boot service provider.
     */
    public function boot()
    {
        $this->app->make(Dispatcher::class)->subscribe(CsrfTokenRegister::class);
        $this->app->make(Dispatcher::class)->subscribe(RouteRegister::class);
        $this->loadMigrationsFrom(realpath(__DIR__ . '/../databases/migrations'));
        $this->loadTranslationsFrom(realpath(__DIR__ . '/../resources/translations'), 'content');
    }

    /**
     * Register services.
     */
    public function register()
    {
        $this->app->alias('article.manager', ArticleManager::class);
        $this->app->alias('category.manager', CategoryManager::class);
        $this->app->alias('page.manager', PageManager::class);
        $this->app->singleton('article.manager', function ($app) {
            $manager = new ArticleManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterArticleTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterArticleType($app, $manager));

            return $manager;
        });
        $this->app->singleton('category.manager', function ($app) {
            $manager = new CategoryManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterCategoryTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterCategoryType($app, $manager));

            return $manager;
        });
        $this->app->singleton('page.manager', function ($app) {
            $manager = new PageManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterPageTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterPageType($app, $manager));

            return $manager;
        });
    }
}
```

### ModuleServiceProvider

ModuleServiceProvider 是 Module 的模块入口文件，也 Module 的所有功能容器示例注册、路由注入等一系列功能注册及组件启动的服务提供者。

### 完整示例

```php
namespace Notadd\Content;

use Illuminate\Events\Dispatcher;
use Illuminate\Support\ServiceProvider;
use Notadd\Content\Events\RegisterArticleTemplate;
use Notadd\Content\Events\RegisterArticleType;
use Notadd\Content\Events\RegisterCategoryTemplate;
use Notadd\Content\Events\RegisterCategoryType;
use Notadd\Content\Events\RegisterPageTemplate;
use Notadd\Content\Events\RegisterPageType;
use Notadd\Content\Listeners\CsrfTokenRegister;
use Notadd\Content\Listeners\RouteRegister;
use Notadd\Content\Managers\ArticleManager;
use Notadd\Content\Managers\CategoryManager;
use Notadd\Content\Managers\PageManager;

/**
 * Class Module.
 */
class ModuleServiceProvider extends ServiceProvider
{
    /**
     * Boot service provider.
     */
    public function boot()
    {
        $this->app->make(Dispatcher::class)->subscribe(CsrfTokenRegister::class);
        $this->app->make(Dispatcher::class)->subscribe(RouteRegister::class);
        $this->loadMigrationsFrom(realpath(__DIR__ . '/../databases/migrations'));
        $this->loadTranslationsFrom(realpath(__DIR__ . '/../resources/translations'), 'content');
    }

    /**
     * Register services.
     */
    public function register()
    {
        $this->app->alias('article.manager', ArticleManager::class);
        $this->app->alias('category.manager', CategoryManager::class);
        $this->app->alias('page.manager', PageManager::class);
        $this->app->singleton('article.manager', function ($app) {
            $manager = new ArticleManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterArticleTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterArticleType($app, $manager));

            return $manager;
        });
        $this->app->singleton('category.manager', function ($app) {
            $manager = new CategoryManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterCategoryTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterCategoryType($app, $manager));

            return $manager;
        });
        $this->app->singleton('page.manager', function ($app) {
            $manager = new PageManager($app, $app['events']);
            $this->app->make(Dispatcher::class)->fire(new RegisterPageTemplate($app, $manager));
            $this->app->make(Dispatcher::class)->fire(new RegisterPageType($app, $manager));

            return $manager;
        });
    }
}
```
### Composer

通过对 Composer 的自定义，可以实现 Notadd 风格的目录结构。

### Type

配置 type 属性为 notadd-module，会告诉 Composer Installer 将该 Package 安装到目录 modules 下，而非默认目录 vendor 下。

### Require

添加 notadd/installers 的 Package，才能调整 Composer 对该类型 Package 的默认处理逻辑，实现重定向安装目录的特性。

介于，模块的安装方式有两种，一种方式是：将 Composer Package 写入程序根目录的 composer.json 文件，另一种方法是，单独初始化模块 Package，并以文件夹的形式放到 modules 目录，因此，包 notadd/installers 应放置在 require-dev 中。 

### 完整示例

```json
{
    "name": "notadd/content",
    "description": "Notadd's Content Module.",
    "keywords": [
        "notadd",
        "cms",
        "framework",
        "content"
    ],
    "homepage": "https://notadd.com",
    "license": "Apache-2.0",
    "type": "notadd-module",
    "authors": [
        {
            "name": "twilroad",
            "email": "heshudong@ibenchu.com"
        }
    ],
    "require": {
        "php": ">=7.0"
    },
    "require-dev": {
        "notadd/installers": "0.5.*"
    },
    "autoload": {
        "psr-4": {
            "Notadd\\Content\\": "src/"
        }
    }
}
```

## 插件

### 说明

**Extension** 作为 Notadd Framework 的一个特性存在，允许通过 Extension 的方式对 Notadd Framework 进行功能或模板的扩展。
**Extension** 的机制类似于 **Laravel** 中 **Service Provider** 的机制，提供了一种实现组件化的机制，并可以实现传统插件机制中的安装、卸载以及插件启动过程。

### 基本结构

一个完整的 Notadd Extension ，必然是遵循 **Composer** 相关规范的 **Package**。

#### 目录结构

**插件**位于目录 **extensions** 下，插件目录结构如下

```
# vendor                                             厂商目录
    # extension                                      插件目录
        # configuations                              可加载配置文件目录
        # resources                                  资源目录
            # translations                           翻译文件目录
            # views                                  视图目录
        # src                                        源码目录
            # Extension                              扩展服务提供者定义文件
        # composer.json                              Composer 配置文件
```

* [Extension](/#/v1.0/zh-CN/extensions/provider)
* [Resources](/#/v1.0/zh-CN/extensions/resources)
* [Composer](/#/v1.0/zh-CN/extensions/composer)

### 其他说明

* **composer.json** 中需定义 **type** 为 **notadd-extension**
* **composer.json** 中需依赖 **package** 为 **notadd/installers**

### Extension 结构

Extension 的机制类似于 **Laravel** 中 **Service Provider** 的机制，提供了一种实现组件化的机制，并可以实现传统插件机制中的安装、卸载以及插件启动过程。

#### 基本结构

一个完整的 Notadd Extension ，必然是遵循 **Composer** 相关规范的 **Package**。

#### 目录结构

**插件**位于目录 **extensions** 下，插件目录结构如下

```
# vendor                                                       厂商目录
    # extension                                                插件目录
        # configuations                                        可加载配置文件目录
        # resources                                            资源目录
            # translations                                     翻译文件目录
            # views                                            视图目录
        # src                                                  源码目录
        # bootstrap.php                                        插件启动脚本
        # composer.json                                        Composer 配置文件
```

### 其他说明

* **composer.json** 中需定义 **type** 为 **notadd-module**
* **composer.json** 中需依赖 **package** 为 **notadd/installers**



### Composer

通过对 Composer 的自定义，可以实现 Composer 自动加载 Extension 定义的依赖项。

### Type

配置 type 属性为 notadd-extension。

### Require

添加 notadd/installers 的 Package，才能实现 Composer 自动加载 Extension 定义的依赖项。

### 完整示例

```json
{
    "name": "notadd/extension-demo",
    "description": "Notadd's Demo Extension.",
    "type": "notadd-extension",
    "keywords": ["notadd", "demo", "extension"],
    "homepage": "https://notadd.com",
    "license": "Apache-2.0",
    "authors": [
        {
            "name": "twilroad",
            "email": "heshudong@ibenchu.com"
        }
    ],
    "autoload": {
        "psr-4": {
            "Notadd\\Demo\\": "src/"
        }
    },
    "require": {
        "php": ">=7.0",
        "notadd/installers": "0.5.*"
    }
}
```
## 模板


**模板**是 Notadd 的模板扩展机制，允许基于现有功能实现多套模板，并切换。

### 目录结构

**模板**位于 **`themes`** 目录下，目录结构如下：

```
# theme                                                        模板目录
    # composer.json                                            Composer 配置文件
```



## 拓展

### expand

拓展一般需要在特定环境下实现，比如workerman拓展，需要安装workerman支持的拓展。

（拓展文档有待更新）
