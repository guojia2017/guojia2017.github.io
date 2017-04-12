---
title: git branch
date: 2017-04-11 16:23:02
tags:
---

一、启动git bash
1. 可以从安装程序直接点击git bash启动
2. 在要操作的问价夹下右键单价，选择git bash菜单启动。

远程操作的第一步，通常是从远程主机克隆一个版本库，这时就要用到``git clone``命令。
``` bash
$ git clone <远程版本库的网址> <本地目录>
```
本地目录可以省略，省略时默认克隆到当前文件夹下。

二、 知己知彼，百战不殆

``` bash
$ git remote
$ git remote -v
$ git remote show <主机名>
$ git remote add <主机名> <远程主机地址>
$ git remote rm <主机名>
```

1. 为了便于管理，Git要求每个远程主机都必须指定一个主机名。git remote命令就用于管理主机名。
不带选项的时候，git remote命令列出所有远程主机。
```bash
$ git remote
origin
```
2. 使用-v选项，可以参看远程主机的网址。
```bash
$ git remote -v
origin  git@github.com:jquery/jquery.git (fetch)
origin  git@github.com:jquery/jquery.git (push)
上面命令表示，当前只有一台远程主机，叫做origin，以及它的网址。
```
3. git remote show命令加上主机名，可以查看该主机的详细信息。
```bash
$ git remote show <主机名>
//比如: $ git remote show origin
```
4. git remote add命令用于添加远程主机。
```cs
$ git remote add <主机名> <网址>
```
5. git remote rm命令用于删除远程主机。
```bash
$ git remote rm <主机名>
```
6. git remote rename命令用于远程主机的改名。
```bash
$ git remote rename <原主机名> <新主机名>
```
三、分支
``` bash
$ git branch
$ git branch -r
$ git branch -a
```
四、本地操作
``` bash
$ git rm <文件名>
$ git rm -r <文件夹名>
```
五、添加文件
``` bash
$ git add <文件名>
$ git add .
```
六、提交
``` bash
$ git commit -m "提交说明信息"
```
七、同步
``` bash
$ git push <主机名> <分支>
```


分支操作：

首先, clone 一个远端仓库，到当前目录下
```cs
$ Git clone git://example.com/myproject $ cd myproject
```
然后，看看你本地有什么分支：
```cs
$ git branch
```
但是有些其他分支你在的仓库里面是隐藏的，你可以加上－a选项来查看它们：
```cs
$ git branch -a
```
屏幕信息：
```cs
* master
  ```

在本地新建一个分支
```cs
$ git branch BlogSource
```

 切换到新的分支
 ```cs
 $ git checkout BlogSource
 ```

 新分支发布到github
 ```cs
 git push origin Branch1
 ```




