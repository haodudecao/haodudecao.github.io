---
layout: post
title: "PHP 错误级别"
subtitle: 'error_reporting'
author: "HaoDu"
header-img: "img/post-bg-php-error-reporter.png"
catalog: true
tags:
  - PHP
---
> 我们在进行开发工作的时候，难免会遇到PHP的报错，解决这些错误，也是作为PHPer必须掌握的一种技能。
> 
> 如果程序发生错误，我们能大致的分析出出现错误的原因，对于我们解决这戏错误会有很大的帮助。

### Notice 通知级别的错误，程序继续执行
##### 1、直接使用未声明变量	
```
echo $str; 

Notice: Undefined variable: str

PHP7.2版本开始：不带引号的字符串是不存在的全局常量，转化成他们自身的字符串。
 在以前，该行为会产生 E_NOTICE，但现在会产生 E_WARNING。
 在下一个 PHP 主版本中，将抛出 Error 异常。
```
##### 2、输出数组值时没有加引号 
```php
$arr =['name'=>'zhangsan','age'=>20];
echo $arr[name];

Notice: Use of undefined constant name - assumed 'name' in test.php on line 71
zhangsan
```

##### 3、输出一个不存在的数组下标 
```
$arr =['name'=>'zhangsan','age'=>20]; 
echo $arr['sex'];

Notice: Undefined index: sex
```

##### 4、输出字符串没有加引号

```
echo hello;

Notice: Use of undefined constant hello - assumed 'hello'
```




### Warning 警告级别的错误，程序继续执行
##### 1、~ 函数调用时，实参没有相应操作（缺参数）~
过去我们传递参数过少会产生 warning
php7.1 开始会抛出fatal error
```
function handle_warning($str)
{ 
    return $str ;
}
handle_warning();

Warning: Missing argument 1 for handle_warning(),

```

##### 2、函数的使用错误,实参的传入类型错误
```
implode('a','abc') //第二个参数必须为数组，但是你传入的是字符串.

Warning: implode(): Invalid arguments passed 
```

##### 3、include()错误,未找到相关文件
```
include('test.php');
```
##### 4、错误的数学运算: 0 不能做除数

```
var_dump(1/0)

Warning: Division by zero
```

##### 5、返回资源类型时的错误
如 php < 5.50	mysql_connect()资源错误 fopen opendir() 连接地址http://php.net/manual/zh/resource.php
### Fatal error 致命级别错误致命级别错误，程序终止执行
##### 1、require
##### 2、定义重名函数 
##### 3、定义重名类
##### 4、调用未定义函数

```
$x = 0;
$y = 20;
diff($x, $y);
```
```
Fatal error: Uncaught Error: Call to undefined function diff() 
```
##### 5、函数在调用时要求参数为特定类型，如果给出的值类型不对
```
function double(int $num*) {
    return $num+$num;
}

add('xxx');

Fatal error: Uncaught TypeError: Argument 1 passed to add() must be of the type integer, string given,
```

##### 6、PHP7.2 使用Object String 作为类名。

##### 7 传参过少
过去我们传递参数过少会产生 warning
php7.1 开始会抛出fatal error

### Parse error 解析错误或语法错误,程序终止执行
##### 1、未加分号结束符 echo 'test'

```
$x =1;
echo 'hello'
echo $x;

Parse error: syntax error, unexpected 'echo' (T_ECHO), expecting ',' or ';'
```

##### 2、括号没有结束 小括号或者大括号没有结束括号。

```
if (1==2){
echo true;

Parse error: syntax error, unexpected end of file
```


##### 3、定义变量漏掉了$
```
y = "www.php.cn"; 
echo $y;

Parse error: syntax error, unexpected '=' 
```
##### 4、低版本的php不支持[] 低版本的php对于数组定义 不支持 $arr = [];
### Deprecated 最低级别的错误 —— 不推荐 或 不建议

```
class A
{
    function foo()
    {
            echo "\$this is not defined.\n";
    }
}

$a = new A();
$a->foo();


```


```
Deprecated: Non-static method A::foo() should not be called statically in D:\www\test\test.php on line 171
```


### 扩展:PHP 中的 Exception, Error, Throwable
- PHP 中将代码自身异常(一般是环境或者语法非法所致)称作错误 `Error`，将运行中出现的逻辑错误称为异常 `Exception`
- 错误是没法通过代码处理的，而异常则可以通过 `try/catch` 来处理
- PHP7 中出现了 `Throwable` 接口，该接口由 `Error` 和 `Exception` 实现，用户不能直接实现 `Throwable `接口，而只能通过继承 `Exception` 来实现接口

详见: [PHP7中的异常与错误处理](https://novnan.github.io/PHP/throwable-exceptions-and-errors-in-php7/)






 


 