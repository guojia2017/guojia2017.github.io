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
$ git branch b1 // 创建branch分支
$ git checkout b1 //切换到分支branch001
```
四、删除操作
``` bash
$ git rm <文件名>
$ git rm -r <文件夹名>
//-r 表示递归删除子文件
```
五、添加操作
``` bash
$ git add <文件名>
$ git add .
```
六、提交
``` bash
$ git commit -m "提交说明信息"
```
七、推送
``` bash
$ git push <主机名> <分支>
```

分支操作：

首先, clone 一个远端仓库，到当前目录下
```cs
$ Git clone git://example.com/myproject $ cd myproject
```
克隆分支
```bash
Git clone -b maven https://github.com/Oracle-ao/ao-shop.git
```
然后，看看你本地有什么分支：
```cs
$ git branch
```
但是有些其他分支你在的仓库里面是隐藏的，你可以加上－a选项来查看它们：
```cs
$ git branch -a
```
屏幕信息：``* master``

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

 #### 实战
 将hexo的源文件一起上传至Github上。
 假设本地父目录是：github.io
 子目录是：hexo
 hexo的相关操作生成的文件都放在hexo目录下。
 建立2个分支，一个是hexo用于存放源文件，一个是master为默认生成文件的存放位置。由于我们只同步hexo分支下的文件，所以设置hexo为默认分支。

在``hexo``父目录下，右键选择``git bash here``.
1. 查看远程服务器地址和本地仓库。
```cs
git remote -v
// 如果提示信息为：
// ``fatal: Not a git repository (or any of the parent directories): .git``
// 证明当前目录没有建立仓库。
```
2. 初始化本地仓库。
```cs
git init
```
3. 添加远程主机。
```cs
git remote add origin git@github.com:guojia/guojia.github.io
```
4. 查看远程服务器地址。
```cs
git remote -v
```
5. 查看分支信息
```cs
git branch -v
```
7. 在本地创建一个分支
```cs
git branch hexo
```
8. 切换到hexo分支
```cs
git checkout hexo
```
6. 将远程主机上的hexo分支文件拉取至本地。
```cs
git pull origin hexo
```
9. 添加本地所有源文件到hexo分支，提交，并推送到远程主机。
```cs
git add .
git commit -m "hexo source files"
git push origin hexo
```

或者方法二：
1. 将hexo分支上的文件克隆至本地。
``克隆前和克隆后都可以先查看当前目录下的信息：``
``git status``
``git remote -v``
``git branch -v``
```cs
git clone -b hexo git@github.com:guojia/guojia.github.io
```
2. 添加远程主机
```cs
git remote add origin git@github.com:guojia/guojia.github.io
```
3. 添加本地文件
```cs
git add .
```
4. 提交
```cs
git commit -m "message"
```
5. 推送
```cs
git push origin hexo
```


参考：
http://ruanyifeng.com/blog/2014/06/git_remote.html
https://www.zhihu.com/question/21193762/answer/138139539
http://blog.csdn.net/binyao02123202/article/details/20130891
http://blog.csdn.net/win_lin/article/details/17037731
《git常用命令整理》(http://justcoding.iteye.com/blog/1830388)
《Github的分支创建与合并》http://www.cnblogs.com/horanly/p/6265182.html
《gitHub如何切换到非master分支的其他分支 》http://blog.csdn.net/hsany330/article/details/51820860