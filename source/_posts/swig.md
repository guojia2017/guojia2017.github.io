---
title: Hexo模板渲染引擎Swig语法
date: 2017-03-30 18:06:43
tags:
---

# Environment
|符号|语法|说明|
|:--|:--|:--|
|{% raw %} {{ }} {% endraw %}|{% raw %}  {{变量开始标记符；}}变量结束标记符 {% endraw %}|{% raw %} {{a}}：声明变量a {% endraw %}|
|{% raw %}  {% %}  {% endraw %}|{% raw %}  {%块开始标记符；%}块结束标记符 {% endraw %}|{% raw %} {% if is_index %}：声明语句if is_index {% endraw %}|
|{% raw %}  {# #}  {% endraw %}|{% raw %}  {#注释开始标记符；#}注释结束标记符 {% endraw %}|{% raw %} {# note:disable template！ #} {% endraw %}|

# Syntax
|符号|语法|说明|
|:--|:--|:--|
| . 或者[]|变量的属性访问| {%raw%} {{foo.bar}}或`{{foo['bar']}} {%endraw%} |

{#

# Append
|符号|语法|说明|
|:--|:--|:--|
|{% raw %}  ---注释内容---  {% endraw %}|{% raw %}  ---注释内容--- {% endraw %}|{% raw %} ---注释内容--- {% endraw %}|

# HTML语法
1. <link>标签
链接一个外部样式表
href：规定被链接文档的位置。
re：规定当前文档与被连接文档的关系。

#}

|参考|
|:--:|
|《[模板设计者](http://docs.jinkan.org/docs/jinja2/templates.html)》|
|《[next主题的模板引擎swig语法介绍](http://jinfang.oschina.io/posts/124966c9/)》|
