---
title: Unity获取某一类物体
date: 2017-04-05 13:09:19
tags:
---

Unity中经常需要取出当前工程中的某一类所有物体（对象）。比如NGUI中取出所有的Atlas，并将图集Atlas打包的图片还原成合并之前的单个Sprite。
Unity中可以使用``Resources.FindObjectsOfTypeAll<T>();``获取。

比如：获取所有UIAtlas的代码。

<!--more-->

```cs
        UIAtlas[] atlasArray = Resources.FindObjectsOfTypeAll<UIAtlas>();

        for (int i = atlasArray.Length - 1; i >= 0; i--)
        {
            UIAtlas atlas = atlasArray[i];
            Generate(atlas);
        }
```


