---
title: Unity Profiler
date: 2017-02-10 14:41:22
tags: [Unity]
---

**GC Alloc优化：**
优化方法：
在代码段中插入：

```cs
Profiler.BeginSample("GC Alloc") ;
Profiler.EndSample() ;
```

<!--more-->

引用
[[Unity] 使用Profiler.BeginSample()定位性能热点 ](http://blog.csdn.net/crasheye/article/details/50947351)