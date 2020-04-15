---
layout: post
title: "Git 将已有的项目添加到 Github"
subtitle: ''
author: "HaoDu"
hidden: 
catalog: true
header-style: text
tags:
  - Git
  - Github

---
# 1. 目标：
 把本地已经存在的项目，推送到github服务端，实现共享。
# 2. 实现步骤：
## (1). 先从github创建一个空的仓库
 
 先从github创建一个空的仓库，并复制链接地址
 
 ## (2). 初始化本地仓库，并提交内容到本地
 需要先打开 命令行终端，然后通过 cd 命令切换到需要添加到github 的项目的目录下，然后依次执行如下命令， 具体命令及其含义如下：
 ### 1). touch README.md
 
 > 创建说明文档，
 ### 2)初始化本地仓库
 
 ```
 git init
 ```
 ###  3)添加全部已经修改的文件，准备commit 提交
 
 该命令效果等同于 git add -A
 
 ```
 git add .
 ```
 ###  4)将修改后的文件提交到本地仓库，如：git commit -m ‘增加README.md说明文档’
 
 ```
 git commit -m ‘提交说明’
 ```
 ## (3). 连接到远程仓库，并将代码同步到远程仓库
 
 ### 1). git remote add origin 远程仓库地址
 > 连接到远程仓库并为该仓库创建别名 , 别名为origin . 这个别名是自定义的，通常用origin ; 远程仓库地址，就是你自己新建的那个仓库的地址，复制地址的方法参考 第二张图。
 如：git remote add origin https://github.com/CnPeng/MyCustomAlertDialog.git 这段代码的含义是： 连接到github上https://github.com/CnPeng/MyCustomAlertDialog.git 这个仓库，并创建别名为origin . （之后push 或者pull 的时候就需要使用到这个 origin 别名）
 
 ### 2). git push -u origin master
 > 创建一个 upStream （上传流），并将本地代码通过这个 upStream 推送到 别名为 origin 的仓库中的 master 分支上
 -u ，就是创建 upStream 上传流，如果没有这个上传流就无法将代码推送到 github；同时，这个 upStream 只需要在初次推送代码的时候创建，以后就不用创建了
 另外，在初次 push 代码的时候，可能会因为网络等原因导致命令行终端上的内容一直没有变化，耐心等待一会就好。
 
 
 ## (4). 继续修改本地代码，然后提交并推送到github
 做完上面三个步骤之后，就实现了将本地代码同步到github的功能，接下来要做的事情就是继续修改代码，然后提交并推送到github
 
 
 
 
 
 
 
 
 
 
 
 
 
 
