# Service Provider （服务提供器）

Laravel 由很多子模块组成：认证、会话、数据库、表单验证、缓存等等。他们都是按统一的方式注入到框架的 IoC 容器中。

那么他们就必须按一定的规范来完成注册，每个服务提供器必须有一个 `register()` 方法与 一个可选的 `boot()` 方法以及一个表示是否延迟加载的属性 `protected $defer`。`register()` 用于完成一些注册逻辑，比如向 IoC 容器注入一个服务，注册配置文件等。

- `register()` 方法
- `boot()` 方法
- `$defer` 属性

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

//TODO:未完