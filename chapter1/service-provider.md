# Service Provider （服务提供器）

Laravel 由很多子模块组成：认证、会话、数据库、表单验证、缓存等等。他们都是按统一的方式注入到框架的 IoC 容器中。

那么他们就必须按一定的规范来完成注册，每个服务提供器必须有一个 `register()` 方法与 一个可选的 `boot()` 方法以及一个表示是否延迟加载的属性 `protected $defer`， 另外还有一个配合 `$defer` 用的方法 `provides()`。`register()` 用于完成一些注册逻辑，比如向 IoC 容器注入一个服务，注册配置文件等。

- `register()` 方法在框架初始化时调用
- `boot()` 方法在所有提供器注册完成后调用
- `$defer` 属性为 `true` 则只在调用该服务提供器提供的服务时才会注册该服务提供器。
- `provides()` 方法返回一个数组，在 `$defer` 属性为 `true` 时有效，告诉框架当前提供器能提供的服务名称。当你向容器中提供该数组中任意一个名称时，框架才去注册该提供器，以达到延迟加载效果，不调用不注册。

我们来看一个框架内部自己的 Cookie 服务提供器：

```php
<?php namespace Illuminate\Cookie;

use Illuminate\Support\ServiceProvider;

class CookieServiceProvider extends ServiceProvider {
	
	/**
	 * Register the service provider.
	 *
	 * @return void
	 */
	public function register()
	{
		$this->app->singleton('cookie', function($app)
		{
			$config = $app['config']['session'];
			return (new CookieJar)->setDefaultPathAndDomain($config['path'], $config['domain']);
		});
	}
}
```

代码见：https://github.com/laravel/framework/blob/5.0/src/Illuminate/Cookie/CookieServiceProvider.php

可以看到它只用了 `register` 方法，向容器中注入了一个别名为 `cookie` 的单例模式的回调函数，函数返回一个 `Illuminate\Cookie\CookieJar` 的实例。

当我们从容器中调用它的时候：

```php
$cookie = App::make('cookie'); 
$cookie->make('logged_user_id', $userId, 60);// 创建一个60分钟有效期的Cookie
```

就能获取到 `Illuminate\Cookie\CookieJar` 的实例。

关于容器的使用请参阅：[框架核心 - Ioc容器](chapter1/container.md)

### 延迟加载

先看一个服务提供器示例：

```php
<?php namespace App\Providers;

use Riak\Connection;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider {

    /**
     * Indicates if loading of the provider is deferred.
     *
     * @var bool
     */
    protected $defer = true;

    /**
     * Register the service provider.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton('Riak\Contracts\Connection', function($app)
        {
            return new Connection($app['config']['riak']);
        });
    }

    /**
     * Get the services provided by the provider.
     *
     * @return array
     */
    public function provides()
    {
        return ['Riak\Contracts\Connection'];
    }

}
```

框架在初始化的时候，检查它的 `$defer` 为 `true`，于是尝试读取 `provides()` 方法，得到一个数组：

```php
['Riak\Contracts\Connection']
```

`register` 方法不会被调用，而是把 `RiakServiceProvider` 与 `['Riak\Contracts\Connection']` 建立对应关系并存储在框架属性 `$deferredServices` 中。

当你在控制器尝试要使用这个服务的时候，比如在某控制器，您依赖了这个类:

```php
<?php

namespace App\Controllers;

class FooController extends Controller 
{
    protected $raik;
    
    public function __construct(\Riak\Contracts\Connection $raik) {
        $this->raik = $raik;
    }
    
    // ... 
}
```

那么框架在调用这个控制器的时候发现它以来了 `Riak\Contracts\Connection`，并对应找出它由 `RiakServiceProvider` 提供，这时才会调用 `RiakServiceProvider` 的 `register` 方法，`register` 向容器注入了 `Riak\Contracts\Connection` 实例， 此时再从容器取出该实例用于控制器 `FooController` 的实例化。

