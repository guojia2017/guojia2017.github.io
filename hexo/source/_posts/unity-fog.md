---
title: Unity代码控制Fog的开关
date: 2017-05-02 11:18:08
tags:
---
场景中的雾气设置(Window>Lighting>Settings>Other Setttings>Fog)完成之后，某些情形下需要在运行时(Runtime)动态的关闭或者开启雾气。
```cs
		RenderSettings.fog = false ;	//disable
		RenderSettings.fog = true ;	//enable
```

<!--more-->
