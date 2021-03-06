# Facade（外观）模式

GoF《设计模式》中说道：为子系统中的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

当子系统（或者对象）使用很复杂时，我们建立一个接口(窗口)对象，将子系统的复杂的使用方法写在此象中，其它对象或程序通过调用此接口(窗口)来使用系 统。即在其它对象或程序中加了一层，此层用于调用子系统。而其它对象使用些层来调用子系统，而不管此层如何调用子系统。

我们通过外观的包装，使应用程序只能看到外观对象，而不会看到具体的细节对象，这样无疑会降低应用程序的复杂度，并且提高了程序的可维护性。

> 例子1：一个电源总开关可以控制四盏灯、一个风扇、一台空调和一台电视机的启动和关闭。该电源总开关可以同时控制上述所有电器设备，电源总开关即为该系统的外观模式设计。

![](../images/facade1.jpg)

-- 图片来自[ 设计模式（九）外观模式Facade（结构型）](http://blog.csdn.net/hguisu/article/details/7533759)

上面说一了堆，来一个比较简明一点的解释吧，虽然不是很准确：

在 Laravel 中外观基本上是起到了一个别名或者说代理的作用，让你通过一个入口完成对一个模块提供所有的服务的访问。

比如通过 `Illuminate\Support\Facades\Validator` 类就可以完成所有 [`Illuminate/Validation`](https://github.com/laravel/framework/tree/5.0/src/Illuminate/Validation) 模块提供的功能，而不用你来回的切换记忆该模块的几个类，不用关心它内部是怎么完成这些功能的。

另外 Laravel 还引入了别名功能，主要表现在 [`config/app.php中alias部分`](https://github.com/laravel/laravel/blob/master/config/app.php#L161-L194) ，这样你就可以直接使用 `Validator` 类名代替 `Illuminate\Support\Facades\Validator`，更加简化类名记忆。类别名的内部实现主要使用了 PHP提供的 [`class_alias`](http://php.net/manual/zh/function.class-alias.php) 函数实现。

比如我们在 Laravel 中用到的 `Input`, 它其实是 `Illuminate\Support\Facades\Input` 的别名，然后 `Illuminate\Support\Facades\Input` 是 `Illuminate\Http\Request` 的外观。

代码见：https://github.com/laravel/framework/blob/5.0/src/Illuminate/Support/Facades/Input.php

所以虽然你是静态的使用 `Input::get(xxx)`，但框架内部还是动态调用的 `Illuminate\Http\Request` 实例的一些方法。

## 外观的使用场景

在遇到以下情况使用facade模式：
    
1. 当你要为一个复杂子系统提供一个简单接口时。子系统往往因为不断演化而变得越来越复杂。大多数模式使用时都会产生更多更小的类。
    这使得子系统更具可重用性，也更容易对子系统进行定制，但这也给那些不需要定制子系统的用户带来一些使用上的困难。facade可以提供一个简单的缺省视图，
    这一视图对大多数用户来说已经足够，而那些需要更多的可定制性的用户可以越过facade层。
   
2. 客户程序与抽象类的实现部分之间存在着很大的依赖性。引入 facade将这个子系统与客户以及其他的子系统分离，可以提高子系统的独立性 和可移植性。
    
3. 当你需要构建一个层次结构的子系统时，使用 facade模式定义子系统中每层的入口点。如果子系统之间是相互依赖的，你可以让它们仅通过facade进行通讯，从而简化了它们之间的依赖关系。

## 优缺点

Facade模式有下面一些优点：

1. 对客户屏蔽子系统组件，减少了客户处理的对象数目并使得子系统使用起来更加容易。通过引入外观模式，客户代码将变得很简单，与之关联的对象也很少。
2. 实现了子系统与客户之间的松耦合关系，这使得子系统的组件变化不会影响到调用它的客户类，只需要调整外观类即可。
3. 降低了大型软件系统中的编译依赖性，并简化了系统在不同平台之间的移植过程，因为编译一个子系统一般不需要编译所有其他的子系统。一个子系统的修改对其他子系统没有任何影响，而且子系统内部变化也不会影响到外观对象。
4. 只是提供了一个访问子系统的统一入口，并不影响用户直接使用子系统类。

Facade模式的缺点:

1. 不能很好地限制客户使用子系统类，如果对客户访问子系统类做太多的限制则减少了可变性和灵活性。
2. 在不引入抽象外观类的情况下，增加新的子系统可能需要修改外观类或客户端的源代码，违背了“开闭原则”。

## 自定义 Laravel 外观

我们也可以很方便的自定义外观，外观在 Laravel 里支持两种模式：
1. 直接面向一个可实例化类的外观
2. 面向容器内对象的外观，使用注入容器时的别名即可。

它们表现在 [Illuminate/Support/Facades/Facade::getFacadeAccessor](https://github.com/laravel/framework/blob/5.0/src/Illuminate/Support/Facades/Facade.php#L128) 
方法的返回值，你可以返回一个对象或者类名，也可以返回一个容器内对象的别名。

比如我们自己写了一堆关于微信支付的功能，全放在 `app/Libraries/Wechat/` 下了，提供了一个入口文件：`app/Libraries/Wechat/Payment.php`。

我们使用第一种方式添加外观：

#### 第一步：继承外观抽象类 `Illuminate/Support/Facades/Facade`

`app/Libraries/Wechat/PaymentFacade.php`:

```php
<?php
namespace Wechat;

use Illuminate/Support/Facades/Facade;

class PaymentFacade extends Facade
{
    protected static function getFacadeAccessor(){
        return 'Wechat/Payment';// 或者 return new Payment(...),不建议
    }
}
```

#### 第二步：添加别名

将下面一行添加到你的 `config/app.php` 中 `alias` 部分：

```php
`WechatPayment` => 'Wechat/PaymentFacade',
```

然后就可以在控制器或者其它地方使用 `PaymentFacade::xxx` 了，`xxx` 为你 `Wechat/Payment` 中的方法。

当然，如果你的 `Wechat/Payment` 可能需要一些参数才能实例化，那么这种情况通常是做成 **服务提供器** 的方式来实现。我们会在下一节讲到。

部分内容摘自：[ 设计模式（九）外观模式Facade（结构型）- 真实的归宿 @ CSDN博客](http://blog.csdn.net/hguisu/article/details/7533759)

