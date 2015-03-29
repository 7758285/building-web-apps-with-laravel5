# 框架核心 - Ioc容器

Laravel 拥有一个很强大的 IoC 容器。Laravel 的核心就是这个 IoC 容器，基于 IoC 容器、框架能够很好很方便的组织管理各个组件。事实上 Laravel 的 [Application 类](https://github.com/laravel/framework/blob/master/src/Illuminate/Foundation/Application.php) 就是继承自 [Container 类](https://github.com/laravel/framework/tree/master/src/Illuminate/Container)。

在了解 IoC 容器之前，你有必要去了解一下什么是 IoC，以及我们为什么需要 IoC 容器，这里就不赘述了，推荐您再回顾《知识储备》一节中依赖注入部分的内容。

下面我会以最简单最容易理解的方式来讲解 Laravel 中的容器，我不会讲它的所有使用方法，只讲解我们应用开发常用的几种方式，其它部分我们在用到的时候再提，降低你的记忆负担。