---
layout: post
title: "糟糕的 PHP"
subtitle: "terrible PHP"
author: "HaoDu"
tags:
  - PHP
---

大部分编程语言的符号是 case sensitive 的，少数（如 Basic）是 case insensitive 的。然而 PHP 两种都不是。试验下就会发现，PHP 的函数、方法以及关键字是 case insensitve 的，而变量、常量都是 case sensitive 的。

至于类，你猜是 sensitive 还是 insensitive？

答案是 insensitive。然而通常我们使用 classloader，如果以大小写不一致的方式使用，则在路径是 case sensitive 的 *nix 环境下，就很容易产生问题了。

好吧，尽管 PHP 再次体现了奇葩，通常 PHP 程序员并不 care 这个问题，绝大多数人遵循“使用和 manual 一致的大小写”的代码风格。

不过这导致一些很不友善的 API 命名，比如 htmlspecialchars ，为什么不是 html_special_chars 或 htmlSpecialChars？

了解函数是 case insensitive 后，我们知道事实上后者的写法是可行的。实际上最初这个函数（在 PHP2 的文档中）就是以 CamelCase 方式命名的（HtmlSpecialChars）。但不知是出于什么原因，（或许是出于“统一”大小写风格？）PHP3 开始，文档中所有函数全部小写了。

但是最初为什么函数被设计为 case insensitive？

PHP 之父 Rasmus 在一个访谈中说：“...I then wrote a very simple parser that would pick tags out of HTML files and replace them with the output of the corresponding functions in the C library... The simple parser slowly grew to include conditional tags, then loop tags, functions, etc. At no point did I think I was writing a scripting language. I was simply adding a little bit of functionality to the macro replacement parser. I was still writing all my real business logic in C.”

这里透露出许多信息。其实最初的 PHP 也许比今天更接近一个模板引擎！早期 PHP 就是“宏替换（macro replacement）”，而业务逻辑并不是它的职责。宏替换具体来说是将 HTML 中的某些 tag 替换为 C 写的对应函数的输出。所以爆栈的这个回答里说，因为 HTML tag 是 case insensitive 的，所以在查找对应函数时，就按照 case insensitive 的方式了。后来的 PHP 虽然已经变得完全不同，但函数名就一直 case insensitive 了。

这一原因很有可能是真相。实际上不仅函数，那些早期就有的结构，比如 if / for 之类的关键字，还有 echo、list 之类并非是函数的特殊指令，也都是 case insensitive 的。或许我们应该反过来问，为何在语言大部分元素都是 case insensitive 的情况下，变量名却是 case sensitive 的？

下面我们讨论下 class 。class 的属性和常量是 case sensitive 的，方法是 case insensitive 的。这样和普通变量、常量、函数的大小写情况正好是一致的。（唔！一致性？！）

问题是类名本身为什么是 insensitive？（从直觉上，类名难道不是更接近常量？）

想一想？

想一想？

想一想？

下面揭晓答案：

因为 PHP 类一开始其构造器并非现在的 __construct，而是和类同名的方法。因为方法是 insensitive，所以类名也就是 insensitive 的。（唔！一致性？！）

好了，我想现在你可以很自豪的说，自己终于搞清楚 PHP 中各种大小写规则了！然而且慢！

PHP 有 goto 语句！可以跳转到指定的 label。goto 作为关键字，我们可以肯定它是 case insensitive 的。可是 label 是 sensitive or insensitive 的？

大家一起来猜一猜罢 ?

【补遗：2年前原文里其实还有一点遗漏，文中提到的 PHP 里的各种构造，其中有一种，实际是又可 case sensitive 又可 case insensitive 的。厉害不？猜猜是哪一种？】

PS. 下一篇《PHP黑系列》什么时候出？其实很久以前也写好草稿了，至于哪天发，看心情罢。

注：题图来自于 Is PHP Case sensitive? What are sensitive words in PHP? - Isrg Rajan ，也是一篇讲 PHP 大小写问题的文章，不过写得没我好，嗯。

