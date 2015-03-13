# 什么是依赖注入？

> 控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（Dependency Injection，简称DI），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

你可能在很多地方看到的都是类似上面这段一样的术语性的文档，以至于你始终没搞懂它。我尽量用通俗一点的说法让你明白什么是依赖注入。

可能写过单元测试的朋友会比较好理解依赖注入写法的一个好处：便于测试。

先看一个用户注册的例子：

```php
<?php

class UserService {
    
    public function create($data) {
        $user = User::create($data);
        
        $mailer = new Mailer(Config::get('mail'));
        $mailer->send('emails.welcome', '欢迎注册！', compact('user'));
        
        return $user;
    }
}

```
上面我们定义了一个用户服务类 `UserService`，它有一个注册用户的功能。可以看到 `create` 方法里先是用模型创建了一个用户，然后给这个用户发送一个欢迎邮件。

