---
layout: post
title: "糟糕的 PHP"
subtitle: "Terrible PHP"
author: "HaoDu"
tags:
  - PHP
---

大部分编程语言的符号是 case sensitive 的，少数（如 Basic）是 case insensitive 的。然而 PHP 两种都不是。试验下就会发现，PHP 的函数、方法以及关键字是 case insensitive 的，而变量、常量都是 case sensitive 的。

至于类，你猜是 sensitive 还是 insensitive？

答案是 insensitive。然而通常我们使用 classloader，如果以大小写不一致的方式使用，则在路径是 case sensitive 的 *nix 环境下，就很容易产生问题了。

好吧，尽管 PHP 再次体现了奇葩，通常 PHP 程序员并不 care 这个问题，绝大多数人遵循“使用和 manual 一致的大小写”的代码风格。

不过这导致一些很不友善的 API 命名，比如 htmlspecialchars ，为什么不是 html_special_chars 或 htmlSpecialChars？

了解函数是 case insensitive 后，我们知道事实上后者的写法是可行的。实际上最初这个函数（在 PHP2 的文档中）就是以 CamelCase 方式命名的（HtmlSpecialChars）。但不知是出于什么原因，（或许是出于“统一”大小写风格？）PHP3 开始，文档中所有函数全部小写了。

但是最初为什么函数被设计为 case insensitive？

PHP 之父 Rasmus 在一个访谈中说：
>“...I then wrote a very simple parser that would pick tags out of HTML files and replace them with the output of the corresponding functions in the C library... The simple parser slowly grew to include conditional tags, then loop tags, functions, etc. At no point did I think I was writing a scripting language. I was simply adding a little bit of functionality to the macro replacement parser. I was still writing all my real business logic in C.”

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

PHP 的函数命名的不一致是被很多人诟病的，如 Problems with PHP 写到：

>No consistent naming convention is used. Some functions are verb_noun() and others are noun_verb(). Some are underscore_separated, while others are CamelCase or runtogether. Some are prefixed_byModuleName, and others use a module_suffix_scheme. Some use "to" and others use "2". And if you take a random set of ten library functions, chances are half a dozen different conventions will be included.

对此，在「PHP 是最好的语言」这个梗是怎么来的？这个回答中，
@涛吴
 写道：


>……告诉你一个简单的例子来说明 PHP 有多匪夷所思：PHP 的核心函数命名很不一致，有 「strptime」这样类 C 函数的名字，有「nl2br」这样的简写，却也有「htmlspecialchars」这样的长名。后来人们发现这种不一致并非偶然，而是当 PHP 还是只有不到一百个函数的小语言的时候，其作者决定用函数名的字符数量——来作为函数的 hash（！）。由于这个愚不可及的决定，PHP 的函数名长度要尽可能地长短有秩、均匀分布，影响一直延续至今。

乍一看，只觉得匪夷所思，不过作者给了出处。用函数名的字符长度作为hash这个说法来自于 http://news.php.net/php.internals/70691，PHP 的创造者 Rasmus 写道：

>Well, there were other factors in play there. htmlspecialchars was a very early function. Back when PHP had less than 100 functions and the function hashing mechanism was strlen(). In order to get a nice hash distribution of function names across the various function name lengths names were picked specifically to make them fit into a specific length bucket. This was circa late 1994 when PHP was a tool just for my own personal use and I wasn't too worried about not being able to remember the few function names.-Rasmus


一开始，我觉得是不是 Rasmus 是开玩笑的？甚至如果不知道这是 Rasmus 本人写的，我都怀疑是不是来“钓鱼”的。

所以我特意查看了PHP的老代码。在 PHP 1.99s 和 2.0.1 的代码里确实可以看到，lex.c 文件（题图即为该文件相关源码）里先是创建了一个 cmd_table ，如下：


```php

static cmd_table_t cmd_table[PHP_MAX_CMD_LEN+1][PHP_MAX_CMD_NUM+1] = {
  { { NULL,0,NULL } },        /* 0 */


  { { NULL,0,NULL } },        /* 1 */


  { { "if", IF, NULL },        /* 2 */
    { NULL,0,NULL } }, 


  { { "max", INTFUNC1,ArrayMax }, /* 3 */
    { "min", INTFUNC1,ArrayMin },
    { "key", KEY,NULL },
    { "end", END,NULL },
    ...
    { "shr", INTFUNC2,shr },
    { NULL,0,NULL } }, 


  { { "echo",PHPECHO,NULL },     /* 4 */
    { "else",ELSE,NULL },
    { "case",CASE,NULL },
    ...
    { "ceil",INTFUNC1,Ceil },
    { NULL,0,NULL } }, 

   ...

};

```
然后在 lexical analyzer 的代码里根据 token 查找指令的函数是这样定义的：
```php
/* Look up a command in the command hash table 
 * If not found, assume it is a user-defined function and return CUSTOMFUNC
 */
int CommandLookup(int cmdlen, YYSTYPE *lvalp) {
  register int i=0;


  if(cmdlen<=PHP_MAX_CMD_LEN) while(cmd_table[cmdlen][i].cmd) {
    if(!strncasecmp(&inbuf[tokenmarker],cmd_table[cmdlen][i].cmd,cmdlen)) {
      *lvalp = (YYSTYPE) MakeToken(&inbuf[tokenmarker],cmdlen);
      LastToken = cmd_table[cmdlen][i].token;
      return(cmd_table[cmdlen][i].token);
    } 
    i++;
  }
  *lvalp = (YYSTYPE) MakeToken(&inbuf[tokenmarker],cmdlen);
  return(CUSTOMFUNC);
}
```
而执行内建函数的定义是：
```php
void IntFunc(char *fnc_name) {
  int i=0;
  int cmdlen = strlen(fnc_name);


  while(cmd_table[cmdlen][i].cmd) {
    if(!strncasecmp(fnc_name,cmd_table[cmdlen][i].cmd,cmdlen)) {
      cmd_table[cmdlen][i].fnc();
      break;
    } 
    i++;
  }
}
```


所以，代码证明，确实 PHP 的早期版本就是拿 `strlen(fnc_name)` 作为 hash 的 ? 。


这则 post 在 reddit 上也不出意外的引发了许多讨论。有人表示这是Rasmus当年做的trade-off，然后遭到许多人的质疑，因为必须得有（相对于缺点的）优点，才谈得上是trade-off，但是拿函数名长度做hash看不出有任何好处。

实在要找“优点”的话，或许只能说是：能非常直观的看到hash table的bucket数量和每个bucket里有哪些名字，非常容易手动维护——比如可以手动调整每个bucket里名字的顺序（通过迎合token出现的概率）来略微提高一丢丢性能。

不过直接用首字母作为hash其实也差不多拥有同样的优点。而且还有更大的优点，你完全不用故意均匀分布，如果某个字母下数量太多，直接再按下一个字母hash就好了——好吧，这实际上是退化的tree，你可以在必要的时候在某个分支上进化一下。

其实，从软件工程的角度说，合理的方式是，一开始实现为直接遍历，然后在将来（必要的时候）进行性能优化——使用一个成熟可靠有人维护的 hash table 库。

总之，这件事情上要为 Rasmus 开脱，是非常困难的。

讲到这里，好像已经没啥好多说（黑）的了。


不过，这毕竟是非常早的 PHP 版本，至晚到 PHP3，这个部分已经完全重写了。所以按道理说，这一“愚不可及的决定”对函数命名造成的影响，本不应“延续至今”。


实际上，还有另一个也许更重要的原因。也是出自于 Rasmus：
> You also need to realize that there is consistency. It is just consistency from a different angle. PHP from day one was always a very thin wrapper on top of dozens, now hundreds, of underlying libraries. The function names and argument order, for the most part, were taken directly from these underlying libraries. So if you were familiar with MySQL's C API, for example, you would instantly be able to navigate PHP's mysql functions to the point where we barely needed PHP MySQL documentation because MySQL's C library documentation covered it function for function. And for many of the str functions (the ones without an underscore), try typing: man strlen/strchr/strrchr/strtok/strpbr/strspn... at your Linux command line prompt.

This approach covers the majority of the functions in PHP. The others are somewhat haphazard because it was not always obvious how to name these given there was no underlying API to mimic.

简言之，这无非是把锅甩给 C （和用 C 写的库和 API）。

但是呢，必须说这个锅甩的有点勉强，毕竟其他编程语言，即使是那些著名的胶水语言如 python，也很少直接在核心库照搬 C 的 API。

至于最后一句，那意思是“没有底层API时我们干脆就乱来了”！对此，只好呵呵了。如果结合早期的hash问题，我觉得这也许是破窗效应的“友善”说法吧……

PS. 破窗效应或许也可以描述 PHP 粉丝社区的问题。

最后，PHP 社区自身不是没有改善函数命名一致性的动议，如 rfc:consistent_function_names ，但貌似没有什么进展，也许这是注定徒劳无功的？


