---
title: HOWTO 文档
---

## 如何基于 Notadd 构建 API

Notadd 底层实现了 passport 机制，有统一的授权管理，主要支持两种方式进行 API 授权，一个是 client，另一个是 passport，这个在其他文档中有做详细的说明。

这里主要说的是，如何基于 Notadd 进行 API 接口的开发。

### 业务逻辑

熟悉 Laravel 的同学都应该知道，Laravel 遵循这样的业务逻辑实现：

```text
路由(route) -> 控制器(controller) -> 业务逻辑(model) -> 数据输出(view)
```

而 Notadd 的 API 业务逻辑实现同样遵循类似的流程：

```text
路由(route) -> 控制器(controller) -> API 处理器(handler) -> 模型(model) -> 数据输出(json)
```

其中，主要的差异在于，API 处理器提供了对数据输出格式的输出，返回的数据格式统一为：

```php
[
    'code' => 200,             // API 接口返回的状态码，默认为 200
    'data' => [],              // API 接口返回的数据，主要为数组形式
    'message' => 'success！',  // API 接口返回的提示信息，可以包含错误信息或成功信息
]
```

### 路由

Notadd 在实现 API 授权的时候，使用的是有 **路由中间件(middleware)** 的方式来实现的。

具体实现方式，是在路由的中间配置参数中添加 **auth:api** 。

例如，在实现 api/setting/all 和 api/setting/set 两个 API 的时候，添加 auth:api 的中间件，代码参考如下：

```php
$this->router->group(['middleware' => ['auth:api', 'web'], 'prefix' => 'api/setting'], function () {
    $this->router->post('all', 'Notadd\Foundation\Setting\Controllers\SettingController@all');
    $this->router->post('set', 'Notadd\Foundation\Setting\Controllers\SettingController@set');
});
```

Notadd 针对需要跨域的 API 还提供了 cross 的路由中间件，以实现 API 跨域的功能。

例如，为前两个 API 提供跨域的功能实现，代码参考如下：

```php
$this->router->group(['middleware' => ['auth:api', 'cross', 'web'], 'prefix' => 'api/setting'], function () {
    $this->router->post('all', 'Notadd\Foundation\Setting\Controllers\SettingController@all');
    $this->router->post('set', 'Notadd\Foundation\Setting\Controllers\SettingController@set');
});
```

### 控制器

由于有了独立的 API处理器 ，控制器层可以制作简单处理，仅需向控制器注入 handler，并由 handler 提供的辅助方法返回 API 数据给前台，即可。

例如，在前面路由调用的 SettingController 中，仅需要注入 AllHandler ，使用方法 toResponse 和 generateHttpResponse 来返回结果给前台，代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-11-08 17:01
 */
namespace Notadd\Foundation\Setting\Controllers;

use Notadd\Foundation\Routing\Abstracts\Controller;
use Notadd\Foundation\Setting\Contracts\SettingsRepository;
use Notadd\Foundation\Setting\Handlers\AllHandler;
use Notadd\Foundation\Setting\Handlers\SetHandler;

/**
 * Class SettingController.
 */
class SettingController extends Controller
{
    /**
     * @var \Notadd\Foundation\Setting\Contracts\SettingsRepository
     */
    protected $settings;

    /**
     * SettingController constructor.
     *
     * @param \Notadd\Foundation\Setting\Contracts\SettingsRepository $settings
     *
     * @throws \Illuminate\Contracts\Container\BindingResolutionException
     */
    public function __construct(SettingsRepository $settings)
    {
        parent::__construct();
        $this->settings = $settings;
    }

    /**
     * All handler.
     *
     * @param \Notadd\Foundation\Setting\Handlers\AllHandler $handler
     *
     * @return \Notadd\Foundation\Passport\Responses\ApiResponse
     * @throws \Exception
     */
    public function all(AllHandler $handler)
    {
        return $handler->toResponse()->generateHttpResponse();
    }

    /**
     * Set handler.
     *
     * @param \Notadd\Foundation\Setting\Handlers\SetHandler $handler
     *
     * @return \Notadd\Foundation\Passport\Responses\ApiResponse
     * @throws \Exception
     */
    public function set(SetHandler $handler)
    {
        return $handler->toResponse()->generateHttpResponse();
    }
}
```

### API Handler 和模型

在 API Handler中提供了模型的操作接口。

在 Notadd 中，提供了两类 API Handler，一类是 DataHandler，另一类是 SetHandler，顾名思义，DataHandler 仅提供数据返回接口，而 SetHandler 不仅提供数据返回接口，还提供其他操作处理的接口。

具体差异体现在，DataHandler 在返回数据接口时仅调用方法 data，而 SetHandler 在调用 data 方法前还有调用 execute 方法。

例如，在前面的 SettingController 中使用的 AllHandler 为 DataHandler 类 Handler，提供返回所有 配置项 的 API 功能，SetHandler 为 SetHandler 类 Handler，提供 修改配置项 并返回所有 配置项 的 API 功能。

AllHandler 的代码如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-11-23 14:44
 */
namespace Notadd\Foundation\Setting\Handlers;

use Illuminate\Container\Container;
use Notadd\Foundation\Passport\Abstracts\DataHandler;
use Notadd\Foundation\Setting\Contracts\SettingsRepository;

/**
 * Class AllHandler.
 */
class AllHandler extends DataHandler
{
    /**
     * @var \Notadd\Foundation\Setting\Contracts\SettingsRepository
     */
    protected $settings;

    /**
     * AllHandler constructor.
     *
     * @param \Illuminate\Container\Container                         $container
     * @param \Notadd\Foundation\Setting\Contracts\SettingsRepository $settings
     */
    public function __construct(
        Container $container,
        SettingsRepository $settings
    ) {
        parent::__construct($container);
        $this->settings = $settings;
    }

    /**
     * Http code.
     *
     * @return int
     */
    public function code()                                                 // 定义 API 操作结果的状态码
    {
        return 200;
    }

    /**
     * Data for handler.
     *
     * @return array
     */
    public function data()                                                  // 定义 API 返回的数据
    {
        return $this->settings->all()->toArray();
    }

    /**
     * Errors for handler.
     *
     * @return array
     */
    public function errors()                                                // 定义 API 操作失败时返回的信息
    {
        return [
            '获取全局设置失败！',
        ];
    }

    /**
     * Messages for handler.
     *
     * @return array
     */
    public function messages()                                             // 定义 API 操作成功时返回的信息
    {
        return [
            '获取全局设置成功！',
        ];
    }
}
```

SetHandler 的代码如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-11-23 15:09
 */
namespace Notadd\Foundation\Setting\Handlers;

use Illuminate\Container\Container;
use Notadd\Foundation\Passport\Abstracts\SetHandler as AbstractSetHandler;
use Notadd\Foundation\Setting\Contracts\SettingsRepository;

/**
 * Class SetHandler.
 */
class SetHandler extends AbstractSetHandler
{
    /**
     * @var \Notadd\Foundation\Setting\Contracts\SettingsRepository
     */
    protected $settings;

    /**
     * SetHandler constructor.
     *
     * @param \Illuminate\Container\Container                         $container
     * @param \Notadd\Foundation\Setting\Contracts\SettingsRepository $settings
     */
    public function __construct(
        Container $container,
        SettingsRepository $settings
    ) {
        parent::__construct($container);
        $this->settings = $settings;
    }

    /**
     * Data for handler.
     *
     * @return array
     */
    public function data()                                                                    // 定义 API 返回的数据
    {
        return $this->settings->all()->toArray();
    }

    /**
     * Errors for handler.
     *
     * @return array
     */
    public function errors()                                                                  // 定义 API 操作失败时返回的信息
    {
        return [
            '修改设置失败！',
        ];
    }

    /**
     * Execute Handler.
     *
     * @return bool
     */
    public function execute()                                                                 // 定义 API 执行的修改操作
    {
        $this->settings->set('site.enabled', $this->request->input('enabled'));
        $this->settings->set('site.name', $this->request->input('name'));
        $this->settings->set('site.domain', $this->request->input('domain'));
        $this->settings->set('site.beian', $this->request->input('beian'));
        $this->settings->set('site.company', $this->request->input('company'));
        $this->settings->set('site.copyright', $this->request->input('copyright'));
        $this->settings->set('site.statistics', $this->request->input('statistics'));

        return true;
    }

    /**
     * Messages for handler.
     *
     * @return array
     */
    public function messages()                                                                // 定义 API 操作成功时返回的信息
    {
        return [
            '修改设置成功!',
        ];
    }
}
```

### 数据输出

API 结果的数据输出，已经在 控制器(controller) 中做了处理。

至此，一个完整的 API 开发完成。

## 如何开发一个 Notadd 模块

模块代码结构请参考项目 https://github.com/notadd/content

模块将包含如下几个部分：

* 模块注入
* 路由注入
* 门面注入
* CSRF注入

### 模块安装

对于模块的安装，目前 Notadd 支持的安装方式，仅为在 根项目 中对 模块 进行依赖。

例如，需要安装模块 notadd/content ,可以修改根项目的文件 composer.json，参考代码如下：

```json
{
    "name": "notadd/notadd",
    "description": "The Notadd Framework.",
    "keywords": [
        "notadd",
        "cms",
        "foundation",
        "framework"
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
    "autoload": {
        "classmap": [
            "storage/databases"
        ]
    },
    "require": {
        "php": ">=7.0",
        "notadd/content": "0.1.*",
        "wikimedia/composer-merge-plugin": "dev-master"
    },
    "require-dev": {
        "fzaninotto/faker": "~1.4",
        "mockery/mockery": "0.9.*",
        "phpunit/phpunit": "~5.0",
        "symfony/css-selector": "3.1.*",
        "symfony/dom-crawler": "3.1.*"
    },
    "config": {
        "preferred-install": "dist"
    },
    "scripts": {
        "post-create-project-cmd": [
            "php notadd key:generate"
        ],
        "post-install-cmd": [
            "Notadd\\Foundation\\Composer\\ComposerScripts::postInstall",
            "php notadd optimize"
        ],
        "post-update-cmd": [
            "Notadd\\Foundation\\Composer\\ComposerScripts::postUpdate",
            "php notadd optimize"
        ]
    },
    "extra": {
        "merge-plugin": {
            "include": [
                "extensions/*/*/composer.json"
            ],
            "recurse": true,
            "replace": false,
            "merge-dev": false
        }
    }
}
```

更新文件 composer.json 后，执行 composer update 即可完成对模块的安装。

完整示例代码，请参考项目 https://github.com/notadd/notadd

### 目录结构

目录结构如下：

```
# module                                             模块目录
    # resources                                      资源目录
        # translations                               翻译文件目录
        # views                                      视图目录
    # src                                            源码目录
        # ModuleServiceProvider.php                  模块服务提供者定义文件
    # composer.json                                  Composer 配置文件
```

一个 Notadd 的模块，是一个符合 composer 规范的包，所以，模块对第三方代码有依赖时，可以在 composer.json 中的 require 节点中添加第三方的包。

而作为一个符合 Notadd 模块定义规范的包，composer.json 需拥有如下信息：

* type 必须为 notadd-module
* require 中必须添加包 notadd/installers

代码参考如下(来自插件根目录下的文件 composer.json, 文件中不应该包含 // 的注释信息，此处仅作为说明)

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
    "type": "notadd-module",                                                          // type 必须设置为 notadd-module
    "authors": [
        {
            "name": "Notadd",
            "email": "notadd@ibenchu.com"
        }
    ],
    "require": {
        "php": ">=7.0",
        "notadd/installers": "0.5.*"                                                  // 必须依赖包 notadd/installers
    },
    "autoload": {
        "psr-4": {
            "Notadd\\Content\\": "src/"
        }
    }
}
```

### 模块注入

所谓 模块注入 ，是 Notadd 在加载模块的时候，会检索模块目录下的类 ModuleServiceProvider，此类必须命名为 ModuleServiceProvider，且需放在源码根目录中，且命名空间必须为 composer.json 的中 autoload 节点定义的符合 psr-4 规范的命名空间，否则 Notadd 将不能正确加载模块！

类 ModuleServiceProvider 的父类必须为 Illuminate\Support\ServiceProvider ，且必须包含 boot 方法。相关服务提供者的概念可以参考 Laravel 文档。

类 ModuleServiceProvider 的代码参考如下：

```php

<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Events\Dispatcher;
use Illuminate\Support\ServiceProvider;
use Notadd\Content\Listeners\RouteRegister;

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
        $this->app->make(Dispatcher::class)->subscribe(RouteRegister::class);                // 订阅事件 RouteRegister
    }
}
```

### 路由注入

由于 Notadd 的路由注入，需要实现事件 RouteRegister，并在事件监听中添加 路由 。

所以，所谓的路由注入，实际是在类 ModuleServiceProvider 实现事件 RouteRegister 的订阅，并在事件订阅类中注册模块所需要的路由。

类 ModuleServiceProvider 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Support\ServiceProvider;

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
    }
}
```

事件订阅类 RouteRegister 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 18:30
 */
namespace Notadd\Content\Listeners;

use Notadd\Content\Controllers\Api\Article\ArticleController as ArticleApiController;
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
        $this->router->group(['middleware' => ['cross', 'web']], function () {               // 路由的注册
            $this->router->group(['prefix' => 'api/article'], function () {
                $this->router->post('find', ArticleApiController::class . '@find');
                $this->router->post('fetch', ArticleApiController::class . '@fetch');
            });
        });
    }
}
```

### 门面注入

门面，是 Laravel 的一个功能特色，可以通过门面调用对应 IoC 容器的实例，所以 Notadd 必然会保留这一功能。

所谓的路由注入，实际是在类 ModuleServiceProvider 实现事件 FacadeRegister 的订阅，并在事件订阅类中注册模块所需要的路由。

类 ModuleServiceProvider 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Events\Dispatcher;
use Illuminate\Support\ServiceProvider;
use Notadd\Content\Listeners\FacadeRegister;

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
        $this->app->make(Dispatcher::class)->subscribe(FacadeRegister::class);
    }
}
```

事件订阅类 FacadeRegister 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-01-22 12:20
 */
namespace Notadd\Content\Listeners;

use Notadd\Foundation\Event\Abstracts\EventSubscriber;
use Notadd\Foundation\Facades\FacadeRegister as FacadeRegisterEvent;

/**
 * Class FacadeRegister.
 */
class FacadeRegister extends EventSubscriber
{
    /**
     * Name of event.
     *
     * @throws \Exception
     * @return string|object
     */
    protected function getEvent()
    {
        return FacadeRegisterEvent::class;
    }

    /**
     * Event handler.
     *
     * @param $event
     */
    public function handle(FacadeRegisterEvent $event)
    {
        $event->register('Log', 'Illuminate\Support\Facades\Log');
    }
}
```

### CSRF注入

所谓的CSRF注入，实际是在类 ModuleServiceProvider 实现事件 CsrfTokenRegister 的订阅，并在事件订阅类中注册模块所需要的路由。

类 ModuleServiceProvider 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Events\Dispatcher;
use Illuminate\Support\ServiceProvider;
use Notadd\Content\Listeners\CsrfTokenRegister;

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
    }
}
```

事件订阅类 CsrfTokenRegister 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-10 11:04
 */
namespace Notadd\Content\Listeners;

use Notadd\Foundation\Event\Abstracts\EventSubscriber;
use Notadd\Foundation\Http\Events\CsrfTokenRegister as CsrfTokenRegisterEvent;

/**
 * Class CsrfTokenRegister.
 */
class CsrfTokenRegister extends EventSubscriber
{
    /**
     * Name of event.
     *
     * @throws \Exception
     * @return string|object
     */
    protected function getEvent()
    {
        return CsrfTokenRegisterEvent::class;
    }

    /**
     * Register excepts.
     *
     * @param $event
     */
    public function handle(CsrfTokenRegisterEvent $event)
    {
        $event->registerExcept('api/article*');
        $event->registerExcept('api/category*');
        $event->registerExcept('api/content*');
        $event->registerExcept('api/page*');
    }
}
```
## 如何开发一个 Notadd 插件

插件代码结构请参考项目 http://git.oschina.net/notadd/duoshuo

插件将包含如下几个部分：

* 插件注入
* 路由注入
* CSRF注入

### 插件安装

对于 插件 的安装，仅需将插件文件按照下面的目录结构，放置到插件根目录 extensions (wwwroot/extensions)下，然后执行命令 composer update 即可。

### 目录结构

目录结构如下：

```
# wwwroot/extensions                              插件根目录
    # vendor                                      厂商目录(目录名称仅为示例，开发时自行修改)
        #  extension                              插件目录(目录名称仅为示例，开发时自行修改)
            # configuations                       可加载配置文件目录
            # resources                           资源目录
                # translations                    翻译文件目录
                # views                           视图目录
            # src                                 源码目录
                # Extension                       插件服务提供者定义文件
            # composer.json                       Composer 配置文件
```

一个 Notadd 的插件，是一个符合 composer 规范的包，所以，插件对第三方代码有依赖时，可以在 composer.json 中的 require 节点中添加第三方的包。

而作为一个符合 Notadd 插件定义规范的包，composer.json 需拥有如下信息：

* type 必须为 notadd-extension
* require 中必须添加包 notadd/installers

代码参考如下(来自插件根目录下的文件 composer.json, 文件中不应该包含 // 的注释信息，此处仅作为说明)

```json
{
    "name": "notadd/duoshuo",
    "description": "Notadd Extension for Duoshuo.",
    "type": "notadd-extension",                                                      // type 必须设置为 notadd-extension
    "keywords": ["notadd", "duoshuo", "extension"],
    "homepage": "https://notadd.com",
    "license": "Apache-2.0",
    "authors": [
        {
            "name": "Notadd",
            "email": "notadd@ibenchu.com"
        }
    ],
    "autoload": {
        "psr-4": {
            "Notadd\\Duoshuo\\": "src/"
        }
    },
    "require": {
        "php": ">=7.0",
        "notadd/installers": "0.5.*"                                                // 必须依赖包 notadd/installers
    }
}
```

### 插件注入

所谓 插件注入 ，是 Notadd 在加载插件的时候，会检索插件目录下的类 Extension，此类必须命名为 Extension，且需放在源码根目录中，且命名空间必须为 composer.json 的中 autoload 节点定义的符合 psr-4 规范的命名空间，否则 Notadd 将不能正确加载插件！

类 Extension 的父类必须为 Notadd\Foundation\Extension\Abstracts\Extension ，且必须包含 boot 方法。

类 Extension 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-21 11:28
 */
namespace Notadd\Duoshuo;

use Notadd\Foundation\Extension\Abstracts\Extension as AbstractExtension;

/**
 * Class Extension.
 */
class Extension extends AbstractExtension
{
    /**
     * Boot provider.
     */
    public function boot()
    {
    }
}
```

### 路由注入

由于 Notadd 的路由注入，需要实现事件 RouteRegister，并在事件监听中添加 路由 。

所以，所谓的路由注入，实际是在类 Extension 实现事件 RouteRegister 的订阅，并在事件订阅类中注册插件所需要的路由。

类 Extension 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-21 11:28
 */
namespace Notadd\Duoshuo;

use Illuminate\Events\Dispatcher;
use Notadd\Duoshuo\Listeners\RouteRegister;
use Notadd\Foundation\Extension\Abstracts\Extension as AbstractExtension;

/**
 * Class Extension.
 */
class Extension extends AbstractExtension
{
    /**
     * Boot provider.
     */
    public function boot()
    {
        $this->app->make(Dispatcher::class)->subscribe(RouteRegister::class);
    }
}
```

事件订阅类 RouteRegister 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-21 11:50
 */
namespace Notadd\Duoshuo\Listeners;

use Notadd\Duoshuo\Controllers\DuoshuoController;
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
        $this->router->group(['middleware' => ['auth:api', 'cross', 'web'], 'prefix' => 'api/duoshuo'], function () {
            $this->router->post('backup', DuoshuoController::class . '@backup');
            $this->router->post('configuration', DuoshuoController::class . '@configuration');
            $this->router->post('number', DuoshuoController::class . '@number');
        });
    }
}
```

### CSRF注入

所谓的CSRF注入，实际是在类 Extension 实现事件 CsrfTokenRegister 的订阅，并在事件订阅类中注册插件所需要的路由。

类 Extension 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-21 11:28
 */
namespace Notadd\Duoshuo;

use Illuminate\Events\Dispatcher;
use Notadd\Duoshuo\Listeners\CsrfTokenRegister;
use Notadd\Foundation\Extension\Abstracts\Extension as AbstractExtension;

/**
 * Class Extension.
 */
class Extension extends AbstractExtension
{
    /**
     * Boot provider.
     */
    public function boot()
    {
        $this->app->make(Dispatcher::class)->subscribe(CsrfTokenRegister::class);
    }
}
```

事件订阅类 CsrfTokenRegister 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2017, notadd.com
 * @datetime 2017-02-23 19:38
 */
namespace Notadd\Duoshuo\Listeners;

use Notadd\Foundation\Event\Abstracts\EventSubscriber;
use Notadd\Foundation\Http\Events\CsrfTokenRegister as CsrfTokenRegisterEvent;

/**
 * Class CsrfTokenRegister.
 */
class CsrfTokenRegister extends EventSubscriber
{
    /**
     * Name of event.
     *
     * @throws \Exception
     * @return string|object
     */
    protected function getEvent()
    {
        return CsrfTokenRegisterEvent::class;
    }

    /**
     * Register excepts.
     *
     * @param $event
     */
    public function handle(CsrfTokenRegisterEvent $event)
    {
        $event->registerExcept('api/duoshuo*');
    }
}
```
## 如何开发一个 Notadd Administration 模块的前端扩展

阅读此文档，需对 Laravel，VueJS 2,Webpack 有了解。

前端扩展，指的是，针对项目 [notadd/administration](https://github.com/notadd/administration) 的前端部分进行扩展功能的开发。

完整示例，请参考模块项目 [notadd/content](http://github.net/notadd/content) 。

前端扩展包含的功能注入点如下：

* 扩展安装注入
* 头部菜单注入
* 路由注入
* 侧边栏菜单注入

### 说明

项目 notadd/administration 的前端部分，是基于 VueJS 2 实现的单页应用(SPA)。

所以，对前端进行扩展，实际是对 VueJS 项目的扩展。

由于 VueJS 项目基于 Webpack 进行构建和打包，所以前端扩展项目也必须基于 Webpack 进行构建和打包。

如何创建和开发 VueJS 2 的项目，请参见 [VueJS 官方文档](http://cn.vuejs.org/v2/guide/)。

但是，Notadd 的前端扩展项目，并不是一个完整的 VueJS 2 的项目，因为 Notadd 只接受 UMD 模块风格的前端模块注入，所以在使用 Webpack 进行模块构建时，webpackConfig 中需要针对 output 参数进行调整，主要体现：

* 必须定义 **output** 的 **library** 别名，此名称，必须与 捆绑 的模块或扩展项目中 composer.json 文件中定义的 name 完全一致，否则无法加载前端扩展
* 必须定义 **output** 的 **libraryTarget** 为 **umd**

配置代码参考如下(来自文件 build/webpack.prod.conf.js)：

```javascript
var path = require('path')
var utils = require('./utils')
var webpack = require('webpack')
var config = require('../config')
var merge = require('webpack-merge')
var baseWebpackConfig = require('./webpack.base.conf')
var HtmlWebpackPlugin = require('html-webpack-plugin')
var ExtractTextPlugin = require('extract-text-webpack-plugin')
var OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin')

var env = config.build.env

var webpackConfig = merge(baseWebpackConfig, {
  module: {
    rules: utils.styleLoaders({
      sourceMap: config.build.productionSourceMap,
      extract: true
    })
  },
  output: {
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/extension.js'),
    library: 'notadd/content',                                                              // 必须定义 library 别名
    libraryTarget: "umd"                                                                    // 必须定义 libraryTarget 为 umd
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': env
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    }),
    new ExtractTextPlugin({
      filename: utils.assetsPath('css/[name].css')
    }),
    new OptimizeCSSPlugin()
  ]
})

if (config.build.productionGzip) {
  var CompressionWebpackPlugin = require('compression-webpack-plugin')

  webpackConfig.plugins.push(
    new CompressionWebpackPlugin({
      asset: '[path].gz[query]',
      algorithm: 'gzip',
      test: new RegExp(
        '\\.(' +
        config.build.productionGzipExtensions.join('|') +
        ')$'
      ),
      threshold: 10240,
      minRatio: 0.8
    })
  )
}

if (config.build.bundleAnalyzerReport) {
  var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
  webpackConfig.plugins.push(new BundleAnalyzerPlugin())
}

module.exports = webpackConfig
```

### 默认导出模块

使用 Webpack 导出 UMD 模块风格的模块是，在 Webpack 配置中定义的 entry 入口文件中，必须使用默认导出模块，作为 Notadd 前端功能注入的注入点。

代码参考如下：

```ecmascript 6
import {headerMixin, installMixin, routerMixin} from './helpers/mixes'

let Core = {}

headerMixin(Core)
installMixin(Core)
routerMixin(Core)

export default Core
```

如上代码所示，模块 Core 即为项目构建后的默认导出模块，在该示例中，使用了 mixin 特性，为模块增加 header，install，router的注入逻辑。

### 扩展安装注入

如上(默认导出模块)述说，installMixin 为模块 Core 注入了 Core.install 的实现，具体代码如下：

```ecmascript 6
export function installMixin (Core) {
  Core.install = function (Vue, Notadd) {
    Core.instance = Notadd
    vueMixin(Core, Vue)
  }
}
export function vueMixin (Core, Vue) {
  Core.http = Vue.http
}
```

Core.install 的调用者，为该方法提供了两个对象，一个是 Vue 全局对象，一个是 Notadd 全局对象。

Vue 全局对象提供的特性，可以参考 VueJS 2 的官方文档。

Notadd 全局对象主要包含如下特性：

* Notadd.Vue：Vue 全局对象的副本
* Notadd.http：axios 全局对象的副本
* Notadd.store：Vuex 对象的副本
* Notadd.components：常用的功能型组件(符合 Vue 组件规范)
* Notadd.layouts：常用的布局型组件(符合 Vue 组件规范)

所以，如果模块 Core 中需要使用 Vue 或 Notadd 的任意对象，均可通过 mixin 特性来附加。

### 头部菜单注入

如上(默认导出模块)述说，headerMixin 为模块 Core 注入了 Core.header 的实现，具体代码如下：

```ecmascript 6
export function headerMixin (Core) {
  Core.header = function (menu) {
    menu.push({
      'text': '文章',
      'icon': 'icon icon-article',
      'uri': '/content'
    })
  }
}
```

### 路由注入

如上(默认导出模块)述说，routerMixin 为模块 Core 注入了 Core.router 的实现，具体代码如下：

```ecmascript 6
import ContentArticle from '../components/Article'
import ContentArticleCreate from '../components/ArticleCreate'
import ContentArticleDraft from '../components/ArticleDraft'
import ContentArticleDraftEdit from '../components/ArticleDraftEdit'
import ContentArticleEdit from '../components/ArticleEdit'
import ContentArticleRecycle from '../components/ArticleRecycle'
import ContentCategory from '../components/ArticleCategory'
import ContentComment from '../components/Comment'
import ContentComponent from '../components/Component'
import ContentDashboard from '../components/Dashboard'
import ContentExtension from '../components/Extension'
import ContentLayout from '../components/Layout'
import ContentPage from '../components/Page'
import ContentPageCategory from '../components/PageCategory'
import ContentPageCreate from '../components/PageCreate'
import ContentPageEdit from '../components/PageEdit'
import ContentTemplate from '../components/Template'
import ContentTag from '../components/ArticleTag'
export function routerMixin (Core) {
  Core.router = function (router) {
    router.modules.push({
      path: '/content',
      component: ContentLayout,
      children: [
        {
          path: '/',
          component: ContentDashboard,
          beforeEnter: router.auth
        },
        {
          path: 'article/all',
          component: ContentArticle,
          beforeEnter: router.auth
        },
        {
          path: 'article/create',
          component: ContentArticleCreate,
          beforeEnter: router.auth
        },
        {
          path: 'article/:id/draft',
          component: ContentArticleDraftEdit,
          beforeEnter: router.auth
        },
        {
          path: 'article/:id/edit',
          component: ContentArticleEdit,
          beforeEnter: router.auth
        },
        {
          path: 'article/category',
          component: ContentCategory,
          beforeEnter: router.auth
        },
        {
          path: 'article/tag',
          component: ContentTag,
          beforeEnter: router.auth
        },
        {
          path: 'article/recycle',
          component: ContentArticleRecycle,
          beforeEnter: router.auth
        },
        {
          path: 'article/draft',
          component: ContentArticleDraft,
          beforeEnter: router.auth
        },
        {
          path: 'page/all',
          component: ContentPage,
          beforeEnter: router.auth
        },
        {
          path: 'page/create',
          component: ContentPageCreate,
          beforeEnter: router.auth
        },
        {
          path: 'page/:id/edit',
          component: ContentPageEdit,
          beforeEnter: router.auth
        },
        {
          path: 'page/category',
          component: ContentPageCategory,
          beforeEnter: router.auth
        },
        {
          path: 'component',
          component: ContentComponent,
          beforeEnter: router.auth
        },
        {
          path: 'template',
          component: ContentTemplate,
          beforeEnter: router.auth
        },
        {
          path: 'extension',
          component: ContentExtension,
          beforeEnter: router.auth
        },
        {
          path: 'comment',
          component: ContentComment,
          beforeEnter: router.auth
        }
      ]
    })
  }
}
```

Core.router 的调用者，为该方法提供了一个 router 对象，该 router 对象中包含如下特性：

* auth: 后台登录验证中间件
* bases: 基础路由定义
* modules: 模块路由定义

### 侧边栏菜单注入

侧边栏菜单注入，提供了扩展管理子级菜单的注入，由 Core.sidebar 提供注入，代码参考如下：

```ecmascript 6
export default {
  sidebar: function (sidebar) {
    sidebar.push({
      text: '多说评论',
      icon: 'fa fa-comment',
      uri: '/duoshuo'
    })
  }
}
```

### 前端扩展构建和打包

在进行代码编写和相关配置之后，使用命令 npm run build 即可完成对扩展模块的打包。

### 前端资源注入

通过前端工具构建和打包后，可以得到前端静态资源文件（js文件，css文件，图片文件等），可以模块中的类 ModuleServiceProvider 或扩展中的类 Extension 中将静态资源文件发布到 public 目录下。

类 ModuleServiceProvider 的代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Support\ServiceProvider;

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
        $this->publishes([
            realpath(__DIR__ . '/../resources/mixes/administration/dist/assets/content/administration') => public_path('assets/content/administration'),
            realpath(__DIR__ . '/../resources/mixes/foreground/dist/assets/content/foreground') => public_path('assets/content/foreground'),
        ], 'public');
    }
}
```

然而，这样并没有结束，仍然需要告诉 Administration 模块你提供了哪些静态资源文件，给后台的前端页面使用。

在模块中的类 ModuleServiceProvider 或扩展中的类 Extension 中提供了相应注入点，script 方法将告诉后台的前端页面引用前面打包生成的 UMD 模块文件，stylesheet 方法将告诉后台的前端页面引用前面打包生成样式文件。

具体代码参考如下：

```php
<?php
/**
 * This file is part of Notadd.
 *
 * @author TwilRoad <heshudong@ibenchu.com>
 * @copyright (c) 2016, notadd.com
 * @datetime 2016-10-08 17:12
 */
namespace Notadd\Content;

use Illuminate\Support\ServiceProvider;

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
    }
    /**
     * Get script of extension.
     *
     * @return string
     * @throws \Illuminate\Contracts\Container\BindingResolutionException
     */
    public static function script()
    {
        return asset('assets/content/administration/js/module.js');
    }

    /**
     * Get stylesheet of extension.
     *
     * @return array
     * @throws \Illuminate\Contracts\Container\BindingResolutionException
     */
    public static function stylesheet()
    {
        return [
            asset('assets/content/administration/css/module.css'),
        ];
    }
}
```
## 如何通过插件或模块的方式对后台首页进行模块注入

Notadd 后台管理的首页(Dashboard)，是可以通过插件或扩展，进行添加自定义模块的。

### 支持的自定义模块类型

* html(自定义原生 HTML 代码)
* text(字符串文本)
* button(普通按钮或带链接的按钮)
* chart(自定义图标，基于[百度图标](http://echarts.baidu.com/))

### 数据结构

#### button

```json
{
    content: '这是 Button 文本内容',
    link: 'http://www.hao123.com',
    span: 12,
    theme: 'primary',
    title: '这是 Button 标题',
    type: 'button',
}
```

#### chart

```json
{
    content: {
        title: {
            text: 'Notadd 图标测试',
        },
        tooltip: {},
        xAxis: {
            data: ['Shirt', 'Sweater', 'Chiffon Shirt', 'Pants', 'High Heels', 'Socks'],
        },
        yAxis: {},
        series: [
            {
                name: 'Sales',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20],
            },
        ],
    },
    span: 12,
    style: 'height: 300px;',
    title: '这是 Chart 标题',
    type: 'chart',
}
```

#### html

```json
{
    content: '这是 Html 文本内容',
    span: 12,
    title: '这是 Html 标题',
    type: 'html',
}
```

#### text

```json
{
    content: '这是 Text 文本内容',
    span: 12,
    title: '这是 Text 标题',
    type: 'text',
}
```

### 注入方法

基于 Notadd 后台前端框架，实现该页面的注入，仅需要前端模块注入的install方式中使用useBoard函数进行注入即可。

参考代码如下：

```javascript
export default function (injection) {
    injection.useBoard({
        content: {
            title: {
                text: 'Notadd Content 模块图标测试',
            },
            tooltip: {},
            xAxis: {
                data: ['资讯', '科技', '文化', '讲座', '娱乐', '软件'],
            },
            yAxis: {},
            series: [
                {
                    name: 'Sales',
                    type: 'bar',
                    data: [5, 20, 36, 10, 10, 20],
                },
            ],
        },
        span: 12,
        style: 'height: 300px;',
        title: '这是 Chart 标题',
        type: 'chart',
    });
}
```
## 如何用Vue2写出web单页应用

Notadd 的前端项目均基于 VueJS 2 构建，详细文档请参见[官方文档](http://cn.vuejs.org/v2/guide/) 。
