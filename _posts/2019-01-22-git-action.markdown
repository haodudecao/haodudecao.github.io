---
layout:     post
title:      "Git 常用操作"
subtitle:   "git 查询 工具"
date:       2019-01-22
author:     "HDDC"
header-img: "img/post-bg-js-module.jpg"
catalog: true
tags:
    - 笔记
    - Git
    - tool
---


操作| 语句
---|---
查看相关命令|`git help -a | grep credential`
==撤销/回滚==|
**撤销工作区更改**<br> 将工作区的内容替换为版本库的(误删文件也可以用此命令恢复，如果已经提交到暂存区，则需要先 reset 如 git rm 命令会将文件删除并提交到暂存区，此时 用此命令无效)<br>.表示全部<br> 可以指定文件或目录 也可以用 * 通配|`git checkout -- .`
**撤销 add（暂存区修改）**<br> 默认全部<br> 可以指定文件或目录<br> HEAD表示最新的版本|`git reset HEAD `
**撤销 commit（本地版本库）**<br> 回退到上一个版本`HEAD^`<br>上上个版本`HEAD^^`<br>默认保留变更代码（soft）<br>--hard 表示本地不保留代码变更记录 |`git reset --hard HEAD^ `
**撤销 push（远程仓库）**<br> 本地代码回退到上一个版本<br>提交覆盖远程仓库<br>--force-with-least 安全提交，如果本地非最新版本（别人提交了代码），git会拒绝覆盖， |`git push origin 分支 --force-with-lease  `
**切换到任一版本**<br> |`git reset --hard commit_id`
**查看任一版本历史**<br>慎用，使用之后不在任何一个分支上 ，处于分离头指针状态（detached HEAD）|`git checkout commit_id`
**查看命令历史** |`git reflog`
**对比两次 commit 差异** |`git diff hash1 hash 2 --stat`
**清除已跟踪文件缓存（想忽略已跟踪的文件）**|`git rm -r --cached 文件/文件夹名字`
**如果本地已有项目文件，切换到该目录下，初始化仓库**|`git init `
**如果本地没有项目文件，可以初始化仓库到一个新文件夹**|`git init directory`
**clone 项目 并重命名为 xxx**|`git clone http://www.baidu.com directory`
查看远程仓库 | ```git remote -v```
修改上次commit描述 |     `git commit -amend -m'description'`
解决git冲突造成的<br> Please move or remove them before you can merge<br> 目前理解的是当本地有修改未提交时，git pull 会报这个错误|`git clean -d -fx ""`
**查看 git commit 记录**<br>参数：<br>--oneline 一行显示<br>-p 查看每次修改具体变化<br>--author="John"<br>-n 加数字，表示最近几条<br>默认只显示当前分支的 log --all 可现实所有分支<br>--graph 分支图|`git log`<br>`git log | grep "^Author: " | awk '{print $2}' | sort | uniq -c | sort -k1,1n`
合并部分文件到另一分支<br>含义为当前分支为 A， 获取 B 分支文件，注意，会全部替换，而非合并|`git checkout B 文件夹或文件名`
配置 user 信息|`git config --local`只对仓库有效<br> `git config --global`对登录⽤户所有仓库有效<br>`git config --system` <br>对系统的所有⽤户有效<br>
 清除，--unset|git config --unset --local user.name 
只提交 track 的文件修改或删除 -u |git add -u 
快速重命名文件到暂存区| git mv a.php b.php
一个分支替换另一个分支|`git push origin develop:master -f`<br>把本地的 develop 分支强制(-f)推送到远程 master<br>但是上面操作，本地的 master 分支还是旧的，通常来说应该在本地做好修改再去 push 到远端，所以我推荐如下操作<br><br>git checkout master // 切换到旧的分支<br>git reset --hard develop // 将本地的旧分支 master 重置成 develop<br>git push origin master --force // 再推送到远程仓库
比较文件|`git diff  ` 比较工作区和暂存区的差异<br>`git diff  -- 文件1 文件2  `比较工作区和暂存区 指定文件差异<br>`git diff --cached` 比较暂存区和 HEAD 差异
重写/合并旧的 commit<br>r -重写<br>s -合并：要合并的 commit 必须在一起，不在一起就剪切到一起|`git rebase -i ` 


> working...
