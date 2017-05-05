---
title: Git生成SSH密钥
date: 2017-04-11 15:05:10
tags:
---

运行git bash客户端。
一、设置git的user name和email
```cs
$ git config --global user.name "name"
$ git config --global user.email "name@mail.com"
```

二、查看git配置
```cs
$git config --lis
```

<!--more-->

三、生成SSH密钥
1.查看是否已经有了ssh密钥
```cs
cd ~/.ssh
ls
```
这两个命令就是检查是否已经存在 id_rsa.pub 或 id_dsa.pub 文件，如果文件已经存在，那么你可以跳过步骤2，直接进入步骤3。

2.生成密钥
```cs
$ ssh-keygen -t rsa -C "username@email.com"
```
按3个回车，密码为空这里一般不使用密钥。

``` bash
Your identification has been saved in /home/.ssh/id_rsa.  
Your public key has been saved in /home/.ssh/id_rsa.pub.  
```

最后得到了两个文件：id_rsa和id_rsa.pub。

3.添加 私密钥 到ssh：
```cs 
ssh-add id_rsa
```
需要之前输入密码（如果有）。

4.在github上添加ssh密钥，这要添加的是``id_rsa.pub``里面的公钥。
打开``http://github.com``，登陆账号，然后添加ssh。
注意在这里由于直接复制粘帖公钥，可能会导致增加一些字符或者减少些字符，最好用系统工具xclip来做这些事情。
```cs
xclip -selection c  id_rsa.pub
```

5.测试是否生效
``` cs
ssh git@github.com
```
成功信息。
``` cs
PTY allocation request failed on channel 0  
Hi username ! You've successfully authenticated, but GitHub does not provide shell access.  
Connection to github.com closed.  
```

参考
《[git教程](http://www.yiibai.com/git/what_is_version_control.html)》
《[git使用](http://blog.csdn.net/xsckernel/article/details/8563993)》
《[git bash设置ssh](http://blog.csdn.net/binyao02123202.article/details/20130891)》
