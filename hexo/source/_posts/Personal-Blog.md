---
title: 搭建Hexo个人博客
date: 2017-02-09 17:33:37
tags:
---

使用Hexo+GitHub搭建个人博客。

<!--more-->

1. Hexo 个人博客建立 
``` 
http://www.shellsec.com/news/35418.html 
```

2. Hexo 主题：next、Daily
``` 
https://github.com/iissnan/hexo-theme-next
https://github.com/GallenHu/hexo-theme-Daily
```

3. Hexo 新建博客
``` 
	http://www.jianshu.com/p/b06222fbc135
```

4. Hexo 更改博客主题 
``` 
	https://github.com/zchen9/hexo-theme-hollow
```

5. 博客可视化编辑
``` 
	https://www.zybuluo.com/mdeditor
```

6. Hexo主题
``` 
https://hexo.io/themes/ 
```

7. Hexo进阶
``` 
   http://moxfive.coding.me/yelee/
```

8. Hexo使用图片《[点击查看原文](http://www.tuicool.com/articles/umEBVfI)》
8.1. 首先确认`_Config.yml`中有`post_asset_folder:true`
8.2. hexo 目录中，执行 ``npm install https://github.com/CodeFalling/hexo-asset-image --save``
8.3. 执行 ``hexo new "image test"`` 命令新建博客，生成image-test.md文件的同时，会生成一个文件夹image-test，将图片test.jpg复制到image-test文件夹下。
8.4. 编辑``image-test.md``文件，使用``![图片名字][image-test/test.jpg]`` 即可插入图片。
8.5. 设置图片大小
`<img src="./xxx.png" width = "300" height = "200" alt="图片名称" align=center />`

9. Markdown 11种基本语法《[原文](http://cnblogs.com/hnrainll/p/3514637.html)》
9.1. 标题设置（让字体变大，和word的标题意思一样）
在Markdown当中设置标题，有两种方式：
第一种：通过在文字下方添加“=”和“-”，他们分别表示一级标题和二级标题。
第二种：在文字开头加上 “#”，通过“#”数量表示几级标题。（一共只有1~6级标题，1级标题字体最大）
9.2. 块注释（blockquote）
通过在文字开头添加“>”表示块注释。（当>和文字之间添加五个blank时，块注释的文字会有变化。）
9.3. 斜体
``将需要设置为斜体的文字两端使用1个“*”或者“_”夹起来``
9.4. 粗体
``将需要设置为斜体的文字两端使用2个“*”或者“_”夹起来``
9.5. 无序列表
在文字开头添加(*, +, and -)实现无序列表。但是要注意在(*, +, and -)和文字之间需要添加空格。（建议：一个文档中只是用一种无序列表的表示方式）
9.6. 有序列表
使用数字后面跟上句号。（还要有空格）
9.7. 链接（Links）
Markdown中有两种方式，实现链接，分别为内联方式和引用方式。
内联方式：`This is an [example link](http://example.com/).`
引用方式：
I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].  
`[1]: http://google.com/        "Google" `
`[2]: http://search.yahoo.com/  "Yahoo Search" `
`[3]: http://search.msn.com/    "MSN Search"` 
9.8. 图片（Images）
图片的处理方式和链接的处理方式，非常的类似。
内联方式：`![alt text](/path/to/img.jpg "Title")`
引用方式：
`![alt text][id] `
`[id]: /path/to/img.jpg "Title"`
9.9. 代码（HTML中所谓的Code）
实现方式有两种：
第一种：简单文字出现一个代码框。使用`<blockquote>`。
第二种：大片文字需要实现代码框。使用Tab和四个空格。
9.10. 脚注（footnote）
实现方式如下：
`hello[^hello]`
`[^hello]: hi`
9.11. 下划线
在空白行下方添加三条“-”横线。（前面讲过在文字下方添加“-”，实现的2级标题）
9.12. 引用块
Markdown语法：`> 引言`
效果：
> 引言

引用 
- 《[Markdown官方网站](http://daringfireball.net/projects/markdown/)》
- 《[推荐一款在线的Markdown编辑器](https://stackedit.io/ )》
- 《[Hexo+NexT主题配置备忘](http://blog.ynxiu.com/2016/hexo-next-theme-optimize.html)》
- 《[如何优雅的选择字体(font-family) ](https://segmentfault.com/a/1190000006110417)》
