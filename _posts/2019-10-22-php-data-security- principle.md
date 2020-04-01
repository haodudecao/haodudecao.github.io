---
layout: post
title: "PHP 提交数据安全过滤的基本原则"
author: "HaoDu"
tags:
  - PHP
---
## php提交数据过滤的基本原则

1. 提交变量进数据库时，我们必须使用`addslashes()`进行过滤，像注入问题，一个`addslashes()`也就搞定了。其实在涉及到变量取值时，`intval()`函数对字符串的过滤也是个不错的选择。
2. ~~在 php.ini 中开启magic_quotes_gpc和magic_quotes_runtime。
magic_quotes_gpc可以把get,post,cookie里的引号变为斜杠。magic_quotes_runtime对于进出数据库的数据可以起到格式话的作用。其实，早在以前注入很疯狂时，这个参数就很流行了。~~

*PHP 5.4 之后这个参数去掉了,必须手动在代码中 用addslashes*

3. 在使用系统函数时，必须使用`escapeshellarg()`,`escapeshellcmd()`参数去过滤，这样你也就可以放心的使用系统函数。
4. 对于跨站，`strip_tags()`,`htmlspecialchars()`两个参数都不错，对于用户提交的的带有html和php的标记都将进行转换。比如尖括号"<"就将转化为 "<"这样无害的字符。
```php
$new = htmlspecialchars("<a href='test'>Test</a>", ENT_QUOTES);
strip_tags($text,);
```
5. 对于相关函数的过滤，就像先前的`include()`,`unlink`,`fopen()`等等，只要你把你所要执行操作的变量指定好或者对相关字符过滤严密，我想这样也就无懈可击了。

## PHP数据过滤函数总结


- 入库:  `trim($str),addslashes($str)`
- 出库:  `stripslashes($str)`
- 显示:  `htmlspecialchars(nl2br($str))`