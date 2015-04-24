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

另外 Laravel 还引入了别名功能，主要表现在 [`config/app.php中alias部分`](https://github.com/laravel/laravel/blob/master/config/app.php#L161-L194) ，这样你就可以直接使用 `Validator` 类名代替 `Illuminate\Support\Facades\Validator`，更加简化类名记忆。这块的内部实现主要使用了 PHP提供的 [``]()