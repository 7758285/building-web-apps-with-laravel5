# 基础知识

Laravel 5 是一个利用  [composer](http://getcomposer.org/) 在众多开源项目的基础之上构建出来的现代框架，主要使用了著名的 [Symfony](http://symfony.com/) 的部分组件。它使用了 PHP 5 的新特性以及一些常用的设计模式，所以这要求你需要了解一些基础知识以便于更好的使用它。那么我们需要了解哪些基本的东西才能更好更高效的使用 Laravel 5 进行开发呢？

我列举了以下几条：

## PHP 基础知识
  
  不建议新手过早的接触框架，因为这样会增加你的学习难度，尤其是国外框架。框架是高度抽象的，所以代码阅读起来并不是那么容易。在使用 Laravel 5 之前，建议你有以下知识储备：
  
 1. MVC
 2. [自动加载](http://php.net/manual/zh/language.oop5.autoload.php);
 3. [错误处理](http://php.net/manual/zh/book.errorfunc.php);
 4. [PHP标准库 (SPL)](http://php.net/manual/zh/book.spl.php#book.spl);
 5. [输出缓冲控制](http://php.net/manual/zh/book.outcontrol.php);
 6. [PHP 选项/信息](http://php.net/manual/zh/book.info.php);
 7. [数据库抽象层](http://php.net/manual/zh/refs.database.abstract.php);
 8. [session拓展](http://php.net/manual/zh/refs.basic.session.php);
 9. [Cookie的使用](http://php.net/manual/zh/features.cookies.php);
 10. [反射](http://php.net/manual/zh/book.reflection.php);
 11. [类和对象](http://php.net/manual/zh/book.classobj.php);
 12. [图像处理和 GD](http://php.net/manual/zh/book.image.php);
 13. 邮件相关的SMTP;
 14. [文件系统](http://php.net/manual/en/book.filesystem.php);
 15. [预定义变量](http://php.net/manual/zh/reserved.variables.php);
 16. [字符串处理](http://php.net/manual/zh/book.strings.php);
 17. [正则表达式](http://php.net/manual/en/book.pcre.php);
 
最重要的几个：

 1. [命名空间](http://php.net/manual/zh/language.namespaces.php)；
 2. 上面提到过的“[类和对象](http://php.net/manual/zh/book.classobj.php)” 下的全部；

 
更多可以参考我在知乎的回答：[想要开发自己的PHP框架需要那些知识储备？](http://www.zhihu.com/question/26635323/answer/33812516)

或者这里也有很全的很适合新手的文档：[PHP之道](http://wulijun.github.io/php-the-right-way/)

## 设计模式
  
  框架里很多写法都用到了设计模式，所以如果你对设计模式有一定了解的话，你看源码也会很 easy，那么你应该对单例、工厂、策略与命令链模式等有一些了解，可以参考：[《五种常见的 PHP 设计模式》](http://www.ibm.com/developerworks/cn/opensource/os-php-designptrns/) 或者 [《设计模式》](http://www.amazon.com/gp/product/0201633612)一书（国内有中文版)。

## 控制反转

  Laravel 整个框架大量依赖 IoC 容器运行，所以你有必要掌握 [控制反转（Inversion of Control，缩写为IoC）](http://zh.wikipedia.org/wiki/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC) 相关的知识。
  
  有以下链接供参考：
  
- http://www.4wei.cn/archives/1002316
- https://github.com/5-say/laravel-4.1-note/blob/master/04.%E7%9F%A5%E8%AF%86%E6%8B%93%E5%B1%95/PHP/PHP-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5.md
- http://blog.csdn.net/wzllai/article/details/24485245
  

## 模板引擎

  你至少应该用过模板引擎，当然，最好是知道模板引擎的实现原理。

## 开发工具的使用

 作为了个PHP程序员，PHP 包管理工具 [composer](http://getcomposer.org/) 是必要会的、当然还有一些前端工具如 [Gulp](http://gulpjs.com/) 等，毕竟，大部分人还处于“全栈开发”阶段；
 
