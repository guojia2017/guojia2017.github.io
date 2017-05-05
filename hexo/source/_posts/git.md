---
title: 将本地项目上传至github
date: 2017-04-11 11:49:51
tags:
---

<center>
《[如何用命令行在github新建一个项目](http://www.jianshu.com/p/df7ce9f3a5cb)》
</center>

前言：
　新建github帐号和git的基础知识那些就默认读者掌握并且已经添加自己电脑的``SSHKey``到``github``，下面主要说一下如何在``github``新建项目。

新建项目分2种情况：
1. 代码从零开始。
2. 本地已经存在项目代码，只是想放到github上开源或者存放。

然而，无论是哪种情况，都得先在github新建一个项目的仓库。
登录github后，找到Repositories这个tab，然后点击红框中的new来新建一个项目仓库。
其中问你要不要添加项目的忽略文件(.gitigore)还有要不要为你的项目增加一个许可证(license)。
上面的根据你自己的实际需要填完和选择即可，然后点击绿色的按钮就会创建一个项目仓库了。
然后就会跳转到你项目的一个主页。

<!--more-->

### 代码从零开始
　你可以在本地创建一个空白的文件夹，然后克隆刚刚创建的项目(ps: clone url 在项目主页的右下方位置可以找到)本地，然后添加代码再上传。
``` cs
mkdir emptyFolder
cd emptyFodler
git clone https://github.com/youraccount/yourproject.git
```
　然后剩下的就是git add和git push的事情。

### 本地已经存在代码
- 在你的项目文件目录先执行命令git init，使之成为一个git仓库.

``` cs
git init
```
- 将项目里所有文件加到本地的仓库，使用以下命令.

``` cs
  git add .  //注意还有一个小圆点
  git commit -m "some message for this project."	//确认添加
 ```
- 然后将github上的项目pull下来.

``` cs
  git pull https://github.com/account/project.git
```
- 为版本库添加名为origin的远程版本库。

``` cs
  git remote add origin https://github.com/account/project.git
```
- 执行推送命令，完成``GitHub``版本库的初始化.注意命令行中的``-u``参数，在推送成功后自动建立本地分支与远程版本库分支的追踪

``` cs
  git push -u origin master
  ```


