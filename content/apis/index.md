---
title: API结构
---

## API 结构

为了提供一个统一API的实现方式和使用方式，将会做出一系列实现和使用的规范

### 身份验证

API 使用基于令牌的身份验证机制。某些终端不需要身份验证。您可以通过 /api/token 取得一个令牌，主要有两种令牌：

* **密码授权令牌**
* **私人访问令牌**

一般情况，后台管理程序推荐使用密码授权令牌。

### 基本结构

* Notadd 中实现 API 路由的方式，倾向于传统 Laravel 的实现方式，基于 Controller 调用 API Handler 的方式来实现。
* Handler 中使用 toResponse 方法返回 ApiResponse 的实例。
* Handler 主要实现 DataHandler、SetHandler 两种类型的 Handler。
* ApiResponse 为 \Psr\Http\Message\ResponseInterface 契约的一个实现。
* ApiResponse 实例所提供的并返回至前端调用的数据主要包含：code、data、message。

### 所支持的相关API操作

* /oauth/access 验证是否拥有 API 访问 Token
* /oauth/access/authorize
* /oauth/access/token
* /oauth/authorize
* /oauth/clients
* /oauth/refresh



## API Handler 示例

```php
namespace Notadd\Foundation\Setting\Controllers;

use Notadd\Foundation\Routing\Abstracts\Controller;
use Notadd\Foundation\Setting\Contracts\SettingsRepository;
use Notadd\Foundation\Setting\Handlers\AllHandler;
use Notadd\Foundation\Setting\Handlers\SetHandler;

/**
 * Class ApiController.
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
     * TODO: Method  Description
     *
     * @param \Notadd\Foundation\Setting\Handlers\AllHandler $handler
     *
     * @return \Notadd\Foundation\Passport\Responses\ApiResponse
     * @throws \Exception
     */
    public function all(AllHandler $handler)
    {
        $response = $handler->toResponse();

        return $response->generateHttpResponse();
    }

    /**
     * @param \Notadd\Foundation\Setting\Handlers\SetHandler $handler
     *
     * @return \Notadd\Foundation\Passport\Responses\ApiResponse
     * @throws \Exception
     */
    public function set(SetHandler $handler)
    {
        $response = $handler->toResponse();

        return $response->generateHttpResponse();
    }
}
```

```php
namespace Notadd\Foundation\Setting\Handlers;

use Illuminate\Container\Container;
use Illuminate\Http\Request;
use Illuminate\Translation\Translator;
use Notadd\Foundation\Passport\Abstracts\DataHandler;
use Notadd\Foundation\Setting\Contracts\SettingsRepository;

/**
 * Class SettingHandler.
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
     * @param \Illuminate\Http\Request                                $request
     * @param \Notadd\Foundation\Setting\Contracts\SettingsRepository $settings
     * @param \Illuminate\Translation\Translator                      $translator
     */
    public function __construct(
        Container $container,
        Request $request,
        SettingsRepository $settings,
        Translator $translator
    ) {
        parent::__construct($container, $request, $translator);
        $this->settings = $settings;
    }

    /**
     * Http code.
     *
     * @return int
     */
    public function code()
    {
        return 200;
    }

    /**
     * Data for handler.
     *
     * @return array
     */
    public function data()
    {
        return $this->settings->all()->toArray();
    }

    /**
     * Errors for handler.
     *
     * @return array
     */
    public function errors()
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
    public function messages()
    {
        return [
            '获取全局设置成功！',
        ];
    }
}
```

```php
namespace Notadd\Foundation\Setting\Handlers;

use Illuminate\Container\Container;
use Illuminate\Http\Request;
use Illuminate\Translation\Translator;
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
     * @param \Illuminate\Container\Container $container
     * @param \Illuminate\Http\Request $request
     * @param \Notadd\Foundation\Setting\Contracts\SettingsRepository $settings
     * @param \Illuminate\Translation\Translator $translator
     */
    public function __construct(
        Container $container,
        Request $request,
        SettingsRepository $settings,
        Translator $translator
    ) {
        parent::__construct($container, $request, $translator);
        $this->settings = $settings;
    }

    /**
     * Data for handler.
     *
     * @return array
     */
    public function data()
    {
        return $this->settings->all()->toArray();
    }

    /**
     * Errors for handler.
     *
     * @return array
     */
    public function errors()
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
    public function execute()
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
    public function messages()
    {
        return [
            '修改设置成功!',
        ];
    }
}
```

## 模块类 API 文档

* 内容管理系统：http://apizza.cc/console/project/930c250291c96035452c20b8fc780b54/browse
* 用户中心：http://apizza.cc/console/project/bf470481050f71459659784d6b23cb6d/browse
* 商城后台：http://apizza.cc/console/project/5b5d5e6bca11f037c8ff6e5e67cb76a1/browse
* 商家后台：http://apizza.cc/console/project/6c13995a0680cc2fe12a01e12e4cc681/browse
* 商城店铺：http://apizza.cc/console/project/1a02aacf4cdee2ea9b1074ccd56cca46/browse
