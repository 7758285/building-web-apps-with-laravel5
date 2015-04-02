# 框架核心 - Ioc容器

Laravel 拥有一个很强大的 IoC（Inversion of Control，控制反转） 容器。Laravel 的核心就是这个 IoC 容器，基于 IoC 容器、框架能够很好很方便的组织管理各个组件。事实上 Laravel 的 [Application 类](https://github.com/laravel/framework/blob/master/src/Illuminate/Foundation/Application.php) 就是继承自 [Container 类](https://github.com/laravel/framework/tree/master/src/Illuminate/Container)。

在了解 IoC 容器之前，你有必要去了解一下什么是 IoC，以及我们为什么需要 IoC 容器，这里就不赘述了，如果对这块还是云里雾里的话，推荐您再回顾 [《知识储备》](http://overtrue.gitbooks.io/building-web-apps-with-laravel5/content/basics.html) 一节中依赖注入部分的内容。

下面我会以最简单最容易理解的方式来讲解 Laravel 中的容器，我不会讲它的所有使用方法，只讲解我们应用开发常用的几种方式，其它部分我们在用到的时候再提，降低你的记忆负担。

你完全可以把容器理解为一个工具箱，出门工作前我们往里面**放入**各种可能需要用到的工具，然后在工作时我们就可以直接取出来使用。

用代码来表示：

```php
$mailer = new Mailer;
App::bind('mailer', $mailer);
```
这里的 `App` 就是框架基类 `Application` 的别名（其实是外观，后面再讲），因为上面我们说到了 `Application` 继承了 `Container`，所以 `App` 即为容器。

上面的代码我们以 `mailer` 为别名放进了一个用于发送邮件的“工具” `Mailer` 对象实例。这里的别名 `mailer` 你可以随意，无关系，不过一个可读性高的名称当然更受欢迎了。

我们在使用的时候可以很方便的从容器取出来：

```php
$mailer = App::make('mailer');
$mailer->to('i@overtrue')->withSubject('您好安正超！')->send();
```

你可能会说：“那这样岂不是每个请求，不管需要不需要发送邮件都实例化了 `Mailer` 对象，感觉很浪费”。

对，确实是这样，不过容器也提供了更多的绑定写法：

```php
App::bind('mailer', function(){
    return new Mailer();
});
```

这里我们使用一个 [匿名函数](http://php.net/manual/zh/functions.anonymous.php) ，它返回一个 `Mailer` 实例，这样一来，我们在这里就没有实例化 `Mailer`， 因为大家都知道，函数不调用不运行，所以函数体内的代码此时是没有运行的，只是一个定义。当我们 `App::make('mailer')` 的时候，容器会调用它并返回 `Mailer` 对象，这样就达到了我们所期望的 “需要时才实例化” 的目的。

但是这样的写法还是有一个缺点：每次调用都重新实例化返回。 应用开发过程中我们很多工具类其实都不需要每次都重新实例化，我们需要 “不调用就不实例化，如果已经实例化过，就直接使用之前实例化的实例”，也就是所谓的 “单例模式”：

```php
App::singleton('mailer', function(){
    return new Mailer();
});
```

你肯定已经发现了，我们的只是把上面的`App::bind()` 换成了 `App::singleton()`， 这俩的区别就是：

- `bind` 直接找到别名对应的内容返回
- `singleton` 在 `bind` 基础上多了一步检查工作：如果已经实例化过，则直接返回实例化的对象，不再重新实例化。


这里的 `App::xxx($abstract, $concrete)` 第二个参数 `$concrete` 在取出实例时会被框架传入容器本身以便于你可以从在函数里访问容器对象：

```php
App::bind('foo', function($app){
    // $app 为框架容器，你可以在这里访问容器
    return new Foo();
});
```