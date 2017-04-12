---
title: Unity3D路径
date: 2017-03-16 13:25:23
tags:
---

# 一、Unity的路径

1. Resources
  只读。Resources文件夹下的资源不管是否有用，全部会打包进.apk或者.ipa，并且打包时会将里面的资源压缩处理。
2. Application.dataPath
  只读。在移动端它是完全没用。
3. Application.streamingAssetsPath
  只读。该路径是用来存放流数据的缓存目录。这个文件夹中的资源在打包时会**原封不动**的打包进去。
4. Application.persistentDataPath
  可读可写路径。该路径是一个持久化数据存储目录的路径，可以在此路径下存储一些持久化的数据文件。在iOS上是应用程序的沙盒，可以被iCloud自动备份；在Android上的位置是根据Project Setting里设置的Write Access路径，可以设置是程序沙盒还是sdcard，注意：如果在Android设置保存在沙盒中，那么就必须root以后才能用电脑取出文件，因此建议写入sdcard里。
5. Application.temporaryCachePath
  缓存目录。跟Application.persistentDataPath类似。

<!-- more -->

## Windows Editor

<style>
	table th:first-of-type{width : 250px;	}
</style>

|Windows平台|路径|
|:--|:--|
|`Application.dataPath`|`E:/SuperBodhi/U3DBodhiMobileApp/U3DSupperBodhi_MobileApp/Assets`|
|`Application.streamingAssetsPath`|`E:/SuperBodhi/U3DBodhiMobileApp/U3DSupperBodhi_MobileApp/Assets/StreamingAssets`|
|`Application.persistentDataPath`|`C:/Users/Administrator/AppData/LocalLow/CIT/SuperBodhi`|
|`Application.temporaryCachePath`|`C:/Users/ADMINI~1/AppData/Local/Temp/CIT/SuperBodhi`|

## Android平台

<style>
	table th:first-of-type{width : 250px;	}
	table th:nth-of-type(3){width : 100px ;}
</style>

|Android|路径|备注|
|:--|:--|:--:|
|`Application.dataPath`|`/data/app/com.CIT.SuperBodhi.apk`| |
|`Application.streamingAssetsPath`|`jar:file:///data/app/com.CIT.SuperBodhi-1.apk!/assets `  |3个斜线|
|`Application.persistentDataPath`|`/storage/emulated/0/Android/data/com.CIT.SuperBodhi/files`| |
|`Application.temporaryCachePath`|`/storage/emulated/0/Android/data/cache`|.|

## iOS平台

|iOS|路径|
|:--|:--|
|`Application.dataPath`|`/var/containers/Bundle/Application/E34E2081-0D23-471B-AED9-D0B7D948D3C0/Data`|
|`Application.streamingAssetsPath`| `/var/containers/Bundle/Application/E34E2081-0D23-471B-AED9-D0B7D948D3C0/Data/Raw`|
|`Application.persistentDataPath`|`/var/mobile/Containers/Data/Application/E34E2081-0D23-471B-AED9-D0B7D948D3C0/Documents`|
|`Application.temporaryCachePath`|`/var/mobile/Containers/Data/Application/E34E2081-0D23-471B-AED9-D0B7D948D3C0/Library/Caches`|

# 二、WWW访问
　WWW是Unity提供的访问Web服务器的工具类：提供http访问功能，实现动态下载音频、视频、图片等功能。
支持的协议有：`http://、 https://、 file://、 ftp://`。
其中file://就是实现从本地读取文件；其使用情形为从服务器获取资源并写入本地的persistentDataPath，然后在运行过程中从本地读取资源。
　使用WWW读取`streamingAssetsPath`和`persistentDataPath`下的文件时需要加上协议前缀：file://
　WWW读取本地文件时需要加上协议前缀` file:// `

> 注：
1 当运行平台是windows时，文件协议的格式为3条斜线：file:///
2 Android平台下的streamingAssetsPath读取时，由于已经包含协议前缀：jar:file/// 所以不需要添加file://

# 三、代码

<font face="PT Mono">
WWW www = new WWW(UniformPath(Application.streamingAssetsPath+"/"+"mConfig.txt")) ; 
</font>

```cs
    public static string UniformPath(string path)
    {
        #if UNITY_EDITOR
        mProtocolHeader = "file:///";
        #else
        mProtocolHeader = "file://";
        #endif

        StringBuilder ss = new StringBuilder();

        if (path.ToLower().StartsWith(mProtocolHeader)||path.ToLower().StartsWith("jar:file:///"))
        {
            ss.Append(path);
        }
        else
        {
            ss.Append(mProtocolHeader);
            ss.Append(path);
        }

        return ss.ToString();
    }
	```

<style>
	table th:first-of-type{width : 250px;	}
</style>

|参考|
|:--|
|《[Unity路径的相关总结](http://www.jianshu.com/p/7ae085d74704)》|
|《[Unity官方文档:WWW](https://docs.unity3d.com/ScriptReference/WWW.html)》|

