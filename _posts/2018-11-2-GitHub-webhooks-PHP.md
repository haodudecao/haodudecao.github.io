---
layout: post
title: "GitLab Webhooks PHP"
subtitle: '利用 gitlab 简单的自动更新'
author: "HaoDu"
header-style: text
tags:
  - Git
  - GitLab
  - Webhooks
---
##  原理
> 当 GitLab 项目内容有变动时,会去请求设置好的url,执行预置的脚本,自动更新代码 


## 步骤
#### 1. GitLab 集成中配置好url

如
```
www.baidu.com/hook.php
```
SSL verification
Enable SSL verification
不知道为啥，即使勾选了 ssl 验证，依然能够完成自动部署

#### 2. 将服务器上ssh 公钥复制,放到 GitLab
（这一步可以跳过）

不知这一步是否起作用,我复制的root 用户的公钥,应该复制调用脚本的用户的公钥,
在此操作之前执行了
```
git config --global credential.helper store
```
(免去每次git pull输入密码)
#### 3.脚本
建立test.php 文件
```
<?php 
//可通过下面这句查看执行脚本的用户是谁,方便在浏览器中调试
//echo exec('whoami');
exec('sudo cd /data/www/project;sudo git pull')
```
```
$php test.php //可在服务器直接执行
```
或者写个脚本去执行shell脚本（试验上面那种方法未成功，GitLab 中显示红色 ==internal error==，~~可能是因为忘了+ sudo，加上之后就可以了~~ 应该是语法错误，测试不加 sudo，只是没有完成更新，GitLab 仍是绿色200）
```
var_dump(exec("sudo sh /data/www/shell/Bke.sh"));
```
脚本代码

```
#!/bin/bash
cd /data/www/Bke && git checkout develop;
git pull;
```

#### 4.权限配置问题(==即使权限配置有问题，GitLab 上仍然显示200 ，只要访问这个链接没有问题，他就是200==)
###### 1.通过php exec函数 执行的话,要确保exec函数没有被禁用
```
$which php //查看php路径
/usr/bin/php
```
找到php.ini 文件,查看是否禁用exec函数

###### 2通过下面命令查看执行php的用户是哪一位沙雕
```
$ps -aux| grep 'php'
$ps -aux| grep 'php-fpm'
```

###### 3给该用户最高权限
(能用git,能用test.php)
通过上一步我们知道了是apache这位沙雕在执行脚本  
然后 服务器执行`visudo`
,结果里面有这样一段
```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL

```
把下边的加上
```
apache   ALL=(ALL)       ALL
%apache        ALL=(ALL)       NOPASSWD: ALL
```


