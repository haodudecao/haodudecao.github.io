---
layout: post
title: "PHP 提交数据安全过滤的基本原则"
subtitle: "PHP Fundamental principles for secure filtering of submitted data "
author: "HaoDu"
catalog: true
tags:
  - PHP
---
## php提交数据过滤的基本原则

1. 提交变量进数据库时，我们必须使用`addslashes()`进行过滤，像注入问题，一个`addslashes()`也就搞定了。其实在涉及到变量取值时，`intval()`函数对字符串的过滤也是个不错的选择。
2. ~~在 php.ini 中开启magic_quotes_gpc和magic_quotes_runtime。
magic_quotes_gpc可以把get,post,cookie里的引号变为斜杠。magic_quotes_runtime对于进出数据库的数据可以起到格式话的作用。其实，早在以前注入很疯狂时，这个参数就很流行了。~~<br>
*PHP 5.4 之后这个参数去掉了,必须手动在代码中 用addslashes*

3. 在使用系统函数时，必须使用`escapeshellarg()`,`escapeshellcmd()`参数去过滤，这样你也就可以放心的使用系统函数。
4. 对于跨站，`strip_tags()`,`htmlspecialchars()`两个参数都不错，对于用户提交的的带有html和php的标记都将进行转换。比如尖括号"<"就将转化为 "\&lt;"这样无害的字符。

    ```php
    $new = htmlspecialchars("<a href='test'>Test</a>", ENT_QUOTES);
    strip_tags($text,);
    
    ```

    **strip_tags() 会有一个问题，就是当用户在使用 "<" ">" 交流时会过滤掉有用内容**
    >  虽然 strip_tags() 从技术上来说是安全的，但如果输入的不合法的 HTML（比如， 没有结束标签），它就成了一个「愚蠢」的函数，可能会去除比你期望的更多的内容。 由于非技术用户在通信中经常使用 < 和 > 字符，strip_tags() 也就不是一个好的选择了。

5. 对于相关函数的过滤，就像先前的`include()`,`unlink`,`fopen()`等等，只要你把你所要执行操作的变量指定好或者对相关字符过滤严密，我想这样也就无懈可击了。

## PHP数据过滤函数总结


- 入库:  `trim($str),addslashes($str)`
- 出库:  `stripslashes($str)`
- 显示:  `htmlspecialchars(nl2br($str))`

## 函数详解
#### `htmlspecialchars()` 函数把预定义的字符转换为 HTML 实体。

字符|替换后
---|---
 & （和号）| \&amp\;
 " （双引号）| '&quot'; 除非设置了 ENT_NOQUOTES
 ' （单引号）| 设置了 `ENT_QUOTES` 后， '&#039;' (如果是 `ENT_HTML401`) ，或者 '&apos;' (如果是 `ENT_XML1、 ENT_XHTML 或 ENT_HTML5`)。
 < （小于）| '&lt;'
> （大于）| '&gt;'

#### `htmlentities()` 本函数各方面都和 htmlspecialchars() 一样， 除了  会转换所有具有 HTML 实体的字符。比如货币表示符号欧元英镑等、版权符号等。 其他符号列表可以参考：http://www.thesauruslex.com/typo/eng/enghtml.htm
一般来说，使用 htmlspecialchars 转化掉基本字符就已经足够了，没有必要使用 htmlentities。实在要使用 htmlentities 时，要注意为第三个参数传递正确的编码。
