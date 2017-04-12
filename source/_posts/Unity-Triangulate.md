---
title: Unity Triangluate 多边形三角化
date: 2017-02-28 14:34:39
tags: [算法,Unity]
---

### 多边形三角化算法
1. 构建多边形的顺时针顶点列表: a1、a2、a3、...... 、an。
2. 从第一个顶点开始依次取出3个顶点，得到n个三角形：△123，△234，△345，...，△(n-1)(n)1，△n12。
在n个三角形中求出符合条件的三角形△ABC(个数少于等于n个)：
△ABC为顺时针三角形，即B点为凸点；
其他顶点不在△ABC所围成的封闭区域内；
在求得的三角形△ABC中找到特征角最大的那个三角形△PQR即为第一个目标三角形。将该索引△PQR加入到结果中。
3. 从顶点列表中移除Q，得到新的顶点列表：a1、a2、a3、...... 、a(n-1)；按照2的步骤求得第二个目标三角形。
4. 重复以上操作，直到顶点列表中只剩下3个顶点。
5. 由列表中的最后三个顶点构成一个三角形。

### Triangulation.cs

<!--more-->

```cs
using UnityEngine;
using System.Collections.Generic;

/**
 * 多边形三角化算法
 * */

public class Triangulation
{
    public class TrigonIndex
    {
        public int V1;
        public int V2;
        public int V3;

        public TrigonIndex(int v1, int v2, int v3)
        {
            V1 = v1;
            V2 = v2;
            V3 = v3;
        }
    }

    /// <summary>
    /// 多边形顶点
    /// </summary>
    private List<Vector3> mVertices;
    private Dictionary<Vector3, int> mVertexNumber; //顶点编号
    private List<TrigonIndex> mTriangleIndex;

    public Triangulation(List<Vector3> polygon)
    {
        this.mVertices = polygon;
        mTriangleIndex = new List<TrigonIndex>();
        mVertexNumber = new Dictionary<Vector3, int>();

        for (int i = 0; i < this.mVertices.Count; i++)
        {
            if (mVertexNumber.ContainsKey(mVertices[i]))
            {
                Debug.LogError("Same Point ：Please remove ！" + mVertices[i]);
                continue;
            }
            mVertexNumber.Add(mVertices[i], i);
        }

        this.Triangulate();
    }


    public void Triangulate()
    {
        List<Vector3> check = new List<Vector3>();
        for (int i = 0; i < mVertices.Count; i++)
        {
            check.Add(mVertices[i]);
        }

        int p = 0;
        int q = 0;
        int r = 0;

        Vector3 P = Vector3.zero;
        Vector3 Q = Vector3.zero;
        Vector3 R = Vector3.zero;

        int attempt = check.Count;

        while (check.Count > 3)
        {
            int vIndex = -1;
            int v1 = 0, v2 = 0, v3 = 0;

            float characteristicAngle = 0;

            for (int i = 1; i <= check.Count; i++)
            {
                p = (q - 1 + check.Count) % check.Count;
                q = (i + 1) % check.Count;
                r = (q + 1) % check.Count;

                P = check[p];
                Q = check[q];
                R = check[r];

                if (IsClockWise(P, Q, R) && !HasPolygonVertexInTriangle(P, Q, R, check))
                {
                    float ca = GetCharacteristicAngle(P, Q, R);
                    if (ca > characteristicAngle)
                    {
                        characteristicAngle = ca;
                        vIndex = q;
                        v1 = mVertexNumber[P];
                        v2 = mVertexNumber[Q];
                        v3 = mVertexNumber[R];
                    }
                }
            }

            if (vIndex != -1)
            {
                check.RemoveAt(vIndex);
                mTriangleIndex.Add(new TrigonIndex(v1, v2, v3));
            }

            attempt--;
            if (attempt == 0)
            {
                Debug.LogError("Error！");
                break;
            }
        }

        mTriangleIndex.Add(new TrigonIndex(mVertexNumber[check[0]], mVertexNumber[check[1]], mVertexNumber[check[2]]));
    }

    /// <summary>
    /// 三角形特征角
    /// </summary>
    /// <param name="A">点A</param>
    /// <param name="B">点B</param>
    /// <param name="C">点C</param>
    /// <returns></returns>
    private float GetCharacteristicAngle(Vector3 A, Vector3 B, Vector3 C)
    {
        float angleA = Mathf.Acos(Vector3.Dot((C - A).normalized, (B - A).normalized)) * Mathf.Rad2Deg;
        float angleB = Mathf.Acos(Vector3.Dot((C - B).normalized, (A - B).normalized)) * Mathf.Rad2Deg;
        float angleC = Mathf.Acos(Vector3.Dot((B - C).normalized, (A - C).normalized)) * Mathf.Rad2Deg;

        float min = angleA;
        min = min < angleB ? min : angleB;
        min = min < angleC ? min : angleC;
        return min;
    }

    /// <summary>
    /// 三角形是否包含多边形的其他顶点
    /// </summary>
    /// <param name="A"></param>
    /// <param name="B"></param>
    /// <param name="C"></param>
    /// <returns></returns>
    private bool HasPolygonVertexInTriangle(Vector3 A, Vector3 B, Vector3 C, List<Vector3> vertices)
    {
        for (int i = 0; i < vertices.Count; i++)
        {
            if (InTrigon(mVertices[i], A, B, C))
            {
                return true;
            }
        }

        return false;
    }

    //  顺时针方向
    public bool IsClockWise(Vector3 A, Vector3 B, Vector3 C)
    {
        Vector3 BA = A - B;
        Vector3 BC = C - B;
        Vector3 cross = Vector3.Cross(BA, BC);
        if (cross.z > 0)
        {
            return true;
        }

        return false;
    }

    public List<TrigonIndex> GetTriangleIndex()
    {
        return this.mTriangleIndex;
    }

    public bool InTrigon(Vector3 target, Vector3 center, Vector3 left, Vector3 right)
    {
        Vector3 Ctl = left - center;
        Vector3 Ctr = right - center;
        Vector3 Ctt = target - center;
        Vector3 Ltr = right - left;
        Vector3 Ltc = right - center;
        Vector3 Ltt = left - target;
        Vector3 Rtl = left - right;
        Vector3 Rtc = center - right;
        Vector3 Rtt = target - right;
        if (Vector3.Dot(Vector3.Cross(Ctl, Ctr).normalized, Vector3.Cross(Ctl, Ctt).normalized) == 1 &&
            Vector3.Dot(Vector3.Cross(Ltr, Ltc).normalized, Vector3.Cross(Ltr, Ltt).normalized) == 1 &&
            Vector3.Dot(Vector3.Cross(Rtc, Rtl).normalized, Vector3.Cross(Rtc, Rtt).normalized) == 1)
            return true;
        else
            return false;
    }
}
```

### 运行效果
<img src = "Unity-Triangluate/Result.png" width="300" align = "center">

### 算法分析
目前实现的算法，时间复杂度比较高，可以参考德洛内三角化算法进行优化。

|算法原理|思路|
|:--|:--:|
|《[多边形切分成三角形的算法](http://www.blogbus.com/szamuro-logs/210569513.html)》|算法主要来源于此|
|《[任意多边形切分成三角形的算法和代码](http://bbs.csdn.net/topics/60072939)》|czmissyou的回复|
|《[在Unity中使用UGUI修改Mesh绘制几何图形](http://www.cnblogs.com/jeason1997/p/5130413.html)》||
|《[Delaunay德洛内三角化算法](http://blog.csdn.net/mrbaolong/article/details/46929387)》||
|《[简单多边形的三角化算法](http://www.doc88.com/p-9512309531272.html)》|.|

