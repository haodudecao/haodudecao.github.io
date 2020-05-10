---
layout: post
title: "Windows 子系统环境开发"
subtitle: "Windows 子系统环境开发"
author: "HaoDu"
catalog: true
tags:
  -
---

# 起因
> 由于项目使用的 PHP 扩展的特殊性（第三方扩展，只有github 源码，只能源码安装,包括swool 和其他一些，swool可能win 还能装），只能在类unix环境下编译安装，办公 PC 又是 windows，所以决定使用 `windows子系统`
# 经过
## windows 子系统
**(前提是windows10 必须是最新版，具体版本如下)**
>若要查看你的电脑上安装了哪个版本的 Windows 10，请执行以下操作：
选择“开始”按钮 ，然后选择“设置”。
在“设置”中，依次选择“系统”>“关于”。

field|value
--|--
版本 |windows10专业版
版本号| 2004
操作系统版本| 19041.207
windows 应用商店搜索，`linux` 只有 `debian` 系列，下了个 `debian`;


安装完了之后，需要升级到子系统 2 版本，就可以正常启用子系统了
https://blog.csdn.net/WPwalter/article/details/101508601
## 安装lnmp环境
开始我准备按照之前的 centos 下已有的经验，一个一个来安装，
```
yum install php7.4

yum: command not found
```
yum 命令不存在
网上查到的解决方案
>到上面这个网站去下载如下RPM包
       python-iniparse-0.3.1-2.1.el6.noarch.rpm
       yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm 
       yum-3.2.29-69.el6.centos.noarch.rpm
       yum-plugin-fastestmirror-1.1.30-30.el6.noarch.rpm
       下载完成之后，用rpm命令进行安装，如下：
        rpm -ivh --force --nodeps python-iniparse-0.3.1-2.1.el6.noarch.rpm
        rpm -ivh --force --nodeps yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm 
        rpm -ivh --force --nodeps  yum-3.2.29-69.el6.centos.noarch.rpm yum-plugin-fa

rpm命令也不存在，陷入死循环。。。

，后来看到大家用 wget，结果wget 命令也不存在，wget 是个软件，需要安装。。。
而用来安装的yum 也不存在。。。。。。。。。死循环+1

后来知道 debian 一般用 apt ，同 centos 下yum （apt-get 是旧版本）
```
apt install wget
```
报错
```
Could not open lock file /var/lib/dpkg/lock-frontend - open (13: Permission
```
网上找教程是让删除 lock 文件，不管用；
 win cmd 下执行
```
Debian config --default-user root
```
（给了root权限后，后来又出现数字不一样 好像是10 或者11，再删，管用了）
用了之后

先 `apt update` 再 `apt upgrade`  结果跑了好久，看执行过程，应该是卡在更新debian相关的内容上了
切换linux 源，
修改这个文件，先备份下，然后修改里边的url 为国内域名就好了  一般改163的网易
```
 mv /etc/apt/sources.list /etc/apt/sources.list.bak
 ```
然后重新执行，跑动了
```
 apt install wget
 ```
 安装好 `wget` 就可以进行下一步了

过程中 我想用命令行看下php扩展 
        `php -m`
添加php到环境变量怎么搞，忘了
> windows下设置PHP环境变量
1.找到“高级系统设置”（二选一的方法找到环境变量）

① 我的电脑-属性-高级-环境变量

②win8,10 直接在搜索框搜 “查看高级系统设置”-环境变量

 

2.找到变量"Path" （win10用②）

①在原来的后边加上“;D:/xammp/php” (php.exe所在的目录，前边还有个；)

②或者是按新建加上 “D:/xammp/php” 

 

3.重新打开cmd 

win+R，输入 php -v （查看php 版本信息）


## 安装lnmp一键包
lnmp.org  因为项目用的是`PHP7.4` ，只有最新的lnmp 1.7才有，所以我们要安装最新的1.7版本

```
screen -S lnmp
wget http://soft.vpser.net/lnmp/lnmp1.7beta.tar.gz -cO lnmp1.7beta.tar.gz && tar zxf lnmp1.7beta.tar.gz && cd lnmp1.7 && ./install.sh lnmp 
```

请注意最后面的lnmp参数，如需要lnmpa 或 lamp 模式，请替换lnmp为你要安装的模式。

## 运行项目

### 子系统和win 上端口会冲突，子系统优先（我遇到的情况是这样的，other说他遇到的是win 优先）
### 子系统文件无法通过 windows 图形化界面修改文件，提示权限问题（使用root 用户后，重启电脑即可）
### 电脑重启后，子系统相关软件不能自启动，需要手动（也可写脚本，放到win 自启中）

### hosts 文件需要修改win 上的，--我陷入误区，改了子系统中的--；

### 搞好了之后，终于看到报错了，缺少组件 composer update（win下的话 也要加环境变量哦）
composer 也卡，源切到淘宝
```
$ composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```
 `composer install` 不要 update 
除非修改了依赖关系，否则应该使用 
`composer install`

>所有的依赖都定义在composer.json中，手册中给出了一些基本用法和例子。你可能已经注意到，在指定版本号的时候，我们并不一定要指明一个精确的版本。那么就有可能发生这么一个情况，对于同一份composer.json，我们在不同时刻拉取到的依赖文件可能不同（因为composer会在满足条件的情况下去拉取最新的那份依赖），从而导致一些异常情况。composer update和composer install正是为了解决这个问题而出现的。

>当你执行composer update的时候，composer会去读取composer.json中指定的依赖，去分析他们，并且去拉取符合条件最新版本的依赖。然后他会把所拉取到的依赖放入vendor目录下，并且把所有拉取的依赖的精确版本号写入composer.lock文件中。

>composer install所执行的事情非常类似，只在第一步的时候有差别。当你本地如果已经存在一份composer.lock时，它将会去读取你的composer.lock而非composer.json，并且以此为标准去下载依赖。当你本地没有composer.lock的时候，它所做的事情和composer update其实并没有区别。

>这意味着，只要你本地有一份composer.lock，你就可以保证无论过去了多久，你都能拉到相同的依赖。而如果你把它纳入你的项目的版本控制中，那么你就可以确保你项目中的每一个人、每一台电脑，不管什么系统，都能拉取到一模一样的依赖，以减少潜在的依赖对部署的影响。当然，请记得，你应该使用的命令是composer install。

>那什么时候该使用composer update呢？当你修改了你的依赖关系，不管是新增了依赖，还是修改了依赖的版本，又或者是删除了依赖，这时候如果你执行composer install的时候，是不会有任何变更的，但你会得到一个警告信息
### 继续报错，.env 文件不存在以及空格问题

### 跑路由后401，伪静态问题
laraval伪静态 
```
#try_files $uri $uri/ @rewrite; 
try_files $uri $uri/ /index.php?$query_string;

rewrite ^/(.*)$ /index.php?_url=/$1;

```
==这两句有啥区别，到底要加到哪里。。。==
### 根据项目需求不同的php版本  再nginx 配置文件中， include 不一样的php版本（php-fpm)

### 安装 node.js 和 npm
``````

```
 
  172  npm install -g cnpm --registry=https://registry.npm.taobao.org
```


```
  
   78  wget https://nodejs.org/dist/v12.16.3/node-v12.16.3-linux-x64.tar.xz
   79  tar -xvf node-v12.16.3-linux-x64.tar.xz
   81  cd node-v12.16.3-linux-x64
   170  ln -s /home/node-v12.16.3-linux-x64/bin/npm /usr/local/bin/#软链
   83  cd bin/;
   88  node -v
   89  cd /home/wwwroot/
   91  cd gcoin/
   94  npm config set registry https://registry.npm.taobao.org
   96  cd gcoin-pc/
   97  cnpm i
   98  npm -v
   99  node -v
  101  ls /usr/local/bin/
  124   npm install --save @/components/common/footBar#这个是 npm 提示，其实不应该安装，这是文件名写错，他当做一个依赖了
  126  rm -rf ./node_modules#清空
  128  cnpm i
  129  npm run  dev
  131  npm cache clear -f#清空缓存
  132  rm -rf ./node_modules
  133  npm cache clear -f
  140  cnpm i
  141  cnpm run  dev
```
npm run dev 的时候，一直遇到这个问题

```
This dependency was not found:

* @/components/common/footBar in ./src/router/index.js

To install it, you can run: npm install --save @/components/common/footBar


These relative modules were not found:

```
网上搜索，发现可能是把文件当作包来处理了
最后发现是 没有 ` footBar` 这个文件， 只有`footbar`...将文件改为大写，完美运行

```
 DONE  Compiled successfully in 3304ms                                                                                                                                                                                             9:47:46 AM

 I  Your application is running here: http://0.0.0.0:8066
```

这一刻还是有点爽的，经历了两天的挫败，终于自己解决了个小问题




