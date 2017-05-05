---
title: scene bake load
date: 2017-05-05 17:07:27
tags:
---

转载《[GearVR开发之系列问题二——动态加载烘焙信息 ](http://blog.csdn.net/zhangxiao13627093203/article/details/51013871)》

一、动态加载预设怎么保留烘焙信息？   
1、描述：使用Unity开发手机游戏或者应用时，因为要考虑到性能和内存的消耗，我们的通常想到的是使用动态加载，不管是使用Assetbundl也好还是Resource.Load也好。但是，动态加载烘焙的预设会丢失烘焙信息，因为Unity的烘焙信息都是保持在场景中的，每次烘焙完都会在场景对应的目录下生成一个和场景对应名字的文件夹，里面就是烘焙完的贴图和LightData。这些数据在新建的场景中就被丢失了，然而烘焙又只能在一个场景中进行烘焙。例如，我有5个预设地图要进行烘焙，当然如果这5个预设地图在一个场景中烘焙完可以稳定运行在手机上，那就不要那么费劲了，直接使用就好。我要描述的情况是，当这5个预设的地图烘焙完不能稳定运行在手机上，那就要动态进行加载，而动态加载又会丢失烘焙信息。似乎是一个矛盾，不可调和。

2、思路：比较保守的一种做法是异步加载场景，先将5个预设地图分别在5个场景中进行烘焙，然后在几个场景中进行切换。这种方法的优点是比较省事，但是如果资源比较大的话还是要做Loading的等待加载。还有一种方法就动态加载预设，并且预设中也保持了烘焙信息。

二、解决方法
Google搜索一下找到了一个帖子点击打开链接，我在这里整理了一下并添加了一些自己的东西在里面。我会将我的工程文件添加到下载里面，如有需要的朋友可以自行下载试验。这个工程其实最主要的还是下面这段代码

<!--more-->

```cs
 
using UnityEngine;  
using System.Collections;  
using System.Collections.Generic;  
  
public class PrefabLightmapData : MonoBehaviour  
{  
    [System.Serializable]  
    struct RendererInfo  
    {  
        public Renderer     renderer;  
        public int          lightmapIndex;  
        public Vector4      lightmapOffsetScale;  
    }  
      
    [SerializeField]  
    RendererInfo[]  m_RendererInfo;  
    [SerializeField]  
    Texture2D[]     m_Lightmaps;  
  
    static GameObject FatherObject;  
    static PrefabLightmapData Instance;  
    void Awake ()  
    {  
        if (m_RendererInfo == null || m_RendererInfo.Length == 0)  
            return;  
          
        var lightmaps = LightmapSettings.lightmaps;  
        var combinedLightmaps = new LightmapData[lightmaps.Length + m_Lightmaps.Length];  
          
        lightmaps.CopyTo(combinedLightmaps, 0);  
        for (int i = 0; i < m_Lightmaps.Length;i++)  
        {  
            combinedLightmaps[i+lightmaps.Length] = new LightmapData();  
            combinedLightmaps[i+lightmaps.Length].lightmapFar = m_Lightmaps[i];  
        }  
          
        ApplyRendererInfo(m_RendererInfo, lightmaps.Length);  
        LightmapSettings.lightmaps = combinedLightmaps;  
          
  
       
    }  
    void Start()  
    {  
        Instance = new PrefabLightmapData();  
        Instance = this;  
  
        FatherObject = gameObject;  
    }  
      
    static void ApplyRendererInfo (RendererInfo[] infos, int lightmapOffsetIndex)  
    {  
        for (int i=0;i<infos.Length;i++)  
        {  
            var info = infos[i];  
             
            #if UNITY_EDITOR  
            if (UnityEditor.GameObjectUtility.AreStaticEditorFlagsSet(info.renderer.gameObject, UnityEditor.StaticEditorFlags.BatchingStatic))  
            {  
                Debug.LogWarning("The renderer " + info.renderer.name + " is marked Batching Static. The static batch is created when building the player. " +  
                                 "Setting the lightmap scale and offset will not affect lightmapping UVs as they have the scale and offset already burnt in.", info.renderer);  
            }  
            #endif  
              
            info.renderer.lightmapIndex = info.lightmapIndex + lightmapOffsetIndex;  
            info.renderer.lightmapScaleOffset = info.lightmapOffsetScale;  
        }  
    }  
     
    #if UNITY_EDITOR  
    [UnityEditor.MenuItem("Assets/Bake Prefab Lightmaps")]  
    static void GenerateLightmapInfo ()  
    {  
        if (UnityEditor.Lightmapping.giWorkflowMode != UnityEditor.Lightmapping.GIWorkflowMode.OnDemand)  
        {  
            Debug.LogError("ExtractLightmapData requires that you have baked you lightmaps and Auto mode is disabled.");  
            return;  
        }  
        UnityEditor.Lightmapping.Bake();  
          
        PrefabLightmapData[] prefabs = FindObjectsOfType<PrefabLightmapData>();  
          
        foreach (var instance in prefabs)  
        {  
            var gameObject = instance.gameObject;  
            var rendererInfos = new List<RendererInfo>();  
            var lightmaps = new List<Texture2D>();  
              
            GenerateLightmapInfo(gameObject, rendererInfos, lightmaps);  
              
            instance.m_RendererInfo = rendererInfos.ToArray();  
            instance.m_Lightmaps = lightmaps.ToArray();  
              
            var targetPrefab = UnityEditor.PrefabUtility.GetPrefabParent(gameObject) as GameObject;  
            if (targetPrefab != null)  
            {  
                //UnityEditor.Prefab  
                UnityEditor.PrefabUtility.ReplacePrefab(gameObject, targetPrefab);  
            }  
        }  
    }  
      
    static void GenerateLightmapInfo (GameObject root, List<RendererInfo> rendererInfos, List<Texture2D> lightmaps)  
    {  
        var renderers = root.GetComponentsInChildren<MeshRenderer>();  
        foreach (MeshRenderer renderer in renderers)  
        {  
            if (renderer.lightmapIndex != -1)  
            {  
                RendererInfo info = new RendererInfo();  
                info.renderer = renderer;  
                info.lightmapOffsetScale = renderer.lightmapScaleOffset;  
                  
                Texture2D lightmap = LightmapSettings.lightmaps[renderer.lightmapIndex].lightmapFar;  
                  
                info.lightmapIndex = lightmaps.IndexOf(lightmap);  
                if (info.lightmapIndex == -1)  
                {  
                    info.lightmapIndex = lightmaps.Count;  
                    lightmaps.Add(lightmap);  
                }  
                  
                rendererInfos.Add(info);  
            }  
        }  
    }  
  
    [UnityEditor.MenuItem("Assets/Add Sub-Objects")]  
    static  void SubObjects()  
    {  
        if (Instance == null||FatherObject==null)  
        {  
            return;  
        }  
        else  
        {  
            Instance.m_RendererInfo = new RendererInfo[FatherObject.transform.childCount];  
            Transform[] tempTransforms = new Transform[FatherObject.transform.childCount];  
            int index = 0;  
  
            foreach (Transform child in FatherObject.transform)  
            {  
                if (child.GetComponent<Renderer>() != null)  
                {  
                    Instance.m_RendererInfo[index].renderer = child.GetComponent<Renderer>();  
                    index++;  
                }  
            }  
        }  
    }  
#endif  
}  
```

1、场景的Light配置
将这段代码添加到工程中，新建一个场景，配置好Lighting如图所示，我用的是unity5.3.3，Lightling在菜单目录中的Windows里面。

2、子物体的Render自动加载

然后将你要烘焙的预设设置成Static，添加脚本到这个预设中，接下来就是要添加父物体下的带Render的子物体添加到如图所示的Renderer Info

中。这里我自己拓展了一点功能在里面，可以自动将父目录下的多个子物体（有时子物体会达到上百个）添加到面板上去，省去了手动托时的麻烦。先点击运行，之后点击菜单栏中的Assets下的Add Sub-Objects，这样Render就自动添加进来了，但是并没有保存下来，因为是在运行中添加的。右键点击Inspector下的脚本，选择Copy Component。关闭运行，右键点击Inspector，选择Paste Component。这样就完成了子物体的Render自动加载到Inspector中。
3、烘焙

上面的步骤都完成之后下一步就要进行烘焙了。将要烘焙的灯光都配置成Baked，然后点击Assets下的Bake Prefab Lightmaps，等待一段时间，在场景的目录下回有一个以场景命名的文件夹，里面就是刚刚烘焙好的场景烘焙信息，这个时候你会发现Inspector下的Lightmaps是一个你无法获取的烘焙贴图，你需要将这个贴图替换成场景烘焙的贴图就可以了。如图所示，然后拖成预设。

4、在新场景中运用

这个场景的Lighting设置一定要保持和你烘焙时候的Lighting设置一致，不然还是运用不了刚刚烘焙好的预设，并且删除一切灯光。如果，运行时发现没有看到你想看到的效果，一定要检测你的Inspector面板上的贴图是不是存在。

最后，附上我自己的工程文件下载地址！码字不易，欢迎拍砖！

百度网盘：链接：http://pan.baidu.com/s/1eRQVMEm 密码：sps4