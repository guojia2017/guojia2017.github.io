---
title: Unity场景打包
date: 2017-05-04 11:35:30
tags:
---

我们的游戏场景有时为了效率和效果，会进行烘培。由于烘培是针对场景进行的配置，如果关卡数比较多的时候，普通加载场景的方法(Application.LoadLevel)需要把很多场景文件添加到Building Settings中。
或者将场景文件打包成AssetBundle，然后就可以在运行时加载没有配置在Buiding Settings的场景。

<!--more-->

- 打包

```cs
/// <summary>
/// 脚本位置：Editor文件夹下
/// 脚本功能：打包
/// </summary>
public class CreateAssetBundle : Editor
{
    // 打包unity场景文件
    [MenuItem("Assets/Build AssetBundle Scene")]
    static void MyBuild()
    {
        Caching.CleanCache();
        string[] levels = { "Assets/RuntimeScene.unity" };
        BuildPipeline.BuildPlayer(levels, 
		   Application.dataPath + "/Scene.unity3d", 
		   BuildTarget.Android, 
		   BuildOptions.BuildAdditionalStreamedScenes);
        AssetDatabase.Refresh();
    }
}
```

- 加载

```cs
/// <summary>
/// 脚本功能：加载场景
/// </summary>
public class LoadAssetBundle : MonoBehaviour
{
    private string url;
    private string assetname;

    void Start()
    {
        url = "file://" + Application.dataPath + "/Scene.unity3d";

        assetname = "RuntimeScene";

        StartCoroutine(Download());
    }

    IEnumerator Download()
    {
        WWW www = new WWW(url);
        yield return www;
        if (www.error != null)
        {
            Debug.Log("下载失败");
        }
        else
        {
            AssetBundle bundle = www.assetBundle;
            Application.LoadLevel("RuntimeScene");

            print("跳转场景");
            bundle.Unload(false);
        }

        // 中断正在加载过程中的WWW
        www.Dispose();
    }
}

```

参考：
《[Unity游戏开发使用Assetbundle加载场景实战](http://unity.jb51.net/xuexipeixun/rumenjiaocheng/537.html)》
《[Unity5版本的AssetBundle打包方案之打包Scene场景 ](http://blog.csdn.net/sinat_20559947/article/details/50037343)》