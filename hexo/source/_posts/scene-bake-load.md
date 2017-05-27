---
title: 动态加载Lightmap
date: 2017-05-05 17:07:27
tags:
---

一、动态加载预设怎么保留烘焙信息？   
1. 描述：使用Unity开发手机游戏或者应用时，因为要考虑到性能和内存的消耗，我们的通常想到的是使用动态加载，不管是使用Assetbundl也好还是Resource.Load也好。但是，动态加载烘焙的预设会丢失烘焙信息，因为Unity的烘焙信息都是保持在场景中的，每次烘焙完都会在场景对应的目录下生成一个和场景对应名字的文件夹，里面就是烘焙完的贴图和LightData。这些数据在新建的场景中就被丢失了，然而烘焙又只能在一个场景中进行烘焙。例如，我有5个预设地图要进行烘焙，当然如果这5个预设地图在一个场景中烘焙完可以稳定运行在手机上，那就不要那么费劲了，直接使用就好。我要描述的情况是，当这5个预设的地图烘焙完不能稳定运行在手机上，那就要动态进行加载，而动态加载又会丢失烘焙信息。似乎是一个矛盾，不可调和。

2. 思路：比较保守的一种做法是异步加载场景，先将5个预设地图分别在5个场景中进行烘焙，然后在几个场景中进行切换。这种方法的优点是比较省事，但是如果资源比较大的话还是要做Loading的等待加载。还有一种方法就动态加载预设，并且预设中也保持了烘焙信息。

二、解决方法
动态加载Lightmap。

<!--more-->

编辑器脚本：PrefabLightmapDataEditor.cs
```cs
using UnityEngine;
using UnityEditor;

public class PrefabLightmapDataEditor : Editor
{
    
    [MenuItem("GameObject/导出烘焙Prefab", false, 0)]
    static void ExportBakePrefab()
    {
        List<GameObject> allRoots = Utils.GetAllRoots();

        string name = "Bake" + EditorSceneManager.GetActiveScene().name;
        GameObject go = new GameObject(name);
        go.transform.SetParent(null);
        go.transform.position = Vector3.zero;

        foreach (GameObject o in allRoots)
        {
            o.transform.SetParent(go.transform);
        }

        PrefabLightmapData data = go.AddComponent<PrefabLightmapData>();
        data.SaveLightmap();

        string path = "Assets/Resources/" + Consts.BakeMapDictionary + name + ".prefab";

        EditorUtility.SetDirty(go);
        PrefabUtility.CreatePrefab(path, go);
        Debug.LogError("烘焙信息导出成功：" + name);

        go.transform.DetachChildren();
        GameObject.DestroyImmediate(go);
        AssetDatabase.Refresh();
    }

    [MenuItem("GameObject/查看烘焙效果", false, 0)]
    static void CheckBakePrefab()
    {
        foreach (PrefabLightmapData data in GameObject.FindObjectsOfType<PrefabLightmapData>())
        {
            data.LoadLightmap();
        }
    }
}
```

被绑定在预制件根节点上的序列化脚本：PrefabLightmapData.cs
```cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class PrefabLightmapData : MonoBehaviour
{
    [System.Serializable]
    struct RendererInfo
    {
        public Renderer renderer;
        public int lightmapIndex;
        public Vector4 lightmapOffsetScale;
    }

    [UnityEngine.SerializeField]
    Texture2D[] lightmapTexs;   //当前场景的灯光贴图

    [UnityEngine.SerializeField]
    Texture2D[] lightmapTexs2;

    [UnityEngine.SerializeField]
    RendererInfo[] rendererList;

    [UnityEngine.SerializeField]
    RenderSettingData renderSetting;

    public void SaveLightmap()
    {
        Renderer[] renders = GetComponentsInChildren<Renderer>(true);
        RendererInfo rendererInfo;

        int index = 0;

        //  optimize
        List<Renderer> info = new List<Renderer>();
        for (int i = 0; i < renders.Length; i++)
        {
            if (renders[i].lightmapIndex != -1)
                info.Add(renders[i]);
        }
        rendererList = new RendererInfo[info.Count];

        foreach (Renderer r in info)
        {
            rendererInfo.renderer = r;
            rendererInfo.lightmapIndex = r.lightmapIndex;
            rendererInfo.lightmapOffsetScale = r.lightmapScaleOffset;
            rendererList[index] = rendererInfo;
            index++;
        }
        //  optimize end . 

        //序列化光照贴图
        LightmapData[] ldata = LightmapSettings.lightmaps;
        lightmapTexs = new Texture2D[ldata.Length];
        lightmapTexs2 = new Texture2D[ldata.Length];

        for (int t = 0, tLength = ldata.Length; t < tLength; ++t)
        {
            lightmapTexs[t] = ldata[t].lightmapColor;
            lightmapTexs2[t] = ldata[t].lightmapDir;
        }

        //  other settings
        RenderSettingData.Info();
        renderSetting.fogDensity = RenderSettings.fogDensity;
        //  other settings
    }

    void Awake()
    {
        this.LoadLightmap();
    }

    public void LoadLightmap()
    {
        //  rendering settings
        RenderSettings.fogDensity = renderSetting.fogDensity;
        RenderSettingData.Info();
        //  rendering settings

        if (null == rendererList || rendererList.Length == 0)
        {
            Debug.Log(gameObject.name + " 的 光照信息为空");
            return;
        }

        Renderer[] renders = GetComponentsInChildren<Renderer>(true);

        for (int i = 0; i < rendererList.Length; i++)
        {
            RendererInfo ri = rendererList[i];

            ri.renderer.lightmapIndex = ri.lightmapIndex;
            ri.renderer.lightmapScaleOffset = ri.lightmapOffsetScale;
        }

        if (null == lightmapTexs || lightmapTexs.Length == 0)
        {
            return;
        }

        //LightmapSettings.lightmapsMode = LightmapsMode.NonDirectional;
        LightmapData[] ldata = new LightmapData[lightmapTexs.Length];
        LightmapSettings.lightmaps = null;

        for (int t = 0; t < lightmapTexs.Length; ++t)
        {
            ldata[t] = new LightmapData();
            ldata[t].lightmapColor = lightmapTexs[t];
            ldata[t].lightmapDir = lightmapTexs2[t];
        }

        LightmapSettings.lightmaps = ldata;
    }
}
```

### 下面说点碰到的坑

0. **最大的一个坑**
单独的烘焙场景，对比动态LightmapData的效果，总是有色差——2个效果的亮暗程度不一致。纠结了半天，查不到问题在哪里。
Window>Lighting>Settings>Realtime Lighting>Realtime Global Illumination . 取消勾选。
详情查看：[详解Unity 5]
原因：烘焙好的贴图不接收环境光。

1. PC正常，发布到android或ios完全看不到光照信息，并且序列化参数都正确，光照贴图也加载正常？
在发布时，Edit - Project Settings - Graphics - Shader Stripping - Lightmap modes: Manual
关于该选项的官方说明：
By default, Unity looks at your scenes and lightmapping settings to figure out which Fog and Lightmapping modes are used; and skips corresponding shader variants. This saves game build data size, and improves loading times.
也就是说，如果你想用脚本动态的控制，那么就得将这里设置为手动模式。
2. 最后一个坑就是不能完全相信搜索结果，他人记录的可能只是针对其出现的问题。

### 其他
1. PrefabUtility.CreatePrefab(path, go);其中的path用的是相对路径("Assets/xxx/xxx") ;

引用
《[Unity 5.x动态加载光照信息（所有坑已踩） ](http://w2bc.com/article/163862)》
《[Unity3D研究院之Unity5.x运行时动态更新烘培贴图（八十七）](http://www.xuanyusong.com/archives/3807)》
《[GearVR开发之系列问题二——动态加载烘焙信息 ](http://blog.csdn.net/zhangxiao13627093203/article/details/51013871)》
《[Lightmapping使用及动态加载lightmap方案](http://blog.sina.com.cn/s/blog_5b6cb9500101cplo.html)》
《[详解Unity 5 全局光照系统Enlighten问题](http://imgtec.eetrend.com/blog/7281)》
《[Duplicate Realtime Lightmap Data](https://forum.unity3d.com/threads/duplicate-realtime-lightmap-data.351606/)》