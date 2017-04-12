---
title: git branch
date: 2017-04-11 16:23:02
tags:
---



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




