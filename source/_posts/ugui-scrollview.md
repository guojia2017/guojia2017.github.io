---
title: UGUI Scrollview自动居中子项
date: 2017-03-27 19:03:44
tags:
---

# 一、原理
使用UGUI实现日历的功能时，需要自动居中ScrollView的子项。
大致实现思路如下：
1. 初始时求出每个子项居中时的Container位置。
2. 当Scrollview滑动结束时快要停止时，获取最近的一个中心点。
3. 将Container移动至该中心点位置。

# 二、代码

```cpp
using UnityEngine;
using System.Collections.Generic;
using UnityEngine.UI;
using UnityEngine.EventSystems;
using System;

/// <summary>
/// 拖动ScrollRect结束时始终让一个子物体位于中心位置。
/// </summary>

public class CenterOnChild : MonoBehaviour, IEndDragHandler, IDragHandler
{
    //将子物体拉到中心位置时的速度
    public float mSpeed = 15f;

    /// <summary>
    /// 一屏显示的子项Item个数
    /// </summary>
    public int mShowCount = 5;

    /// <summary>
    /// 注册该事件获取当拖动结束时位于中心位置的子物体
    /// </summary>
    public event Action<GameObject> OnCenter;
    public event Action<int> OnIndexCenter;

    /// <summary>
    /// 小于该速度时对ScrollView进行修正，认为ScrollView移动快要停止，
    /// </summary>
    private float mMinSpeed = 20f;

    private ScrollRect mScrollView;
    private Transform mContainer;
    private float mTargetPosition;

    private List<float> mChildrenPos = new List<float>();

    private Vector3 mPrevious;
    private int mSampleCount = 2;
    private List<float> mSampleSpeeds = new List<float>();

    enum CenteringState
    {
        Init,
        Ready,
        Centering,
        Finish,
    }
    CenteringState mState = CenteringState.Init;
    bool hasInit = false;
    public void Init()
    {
        if (hasInit)
            return;
        hasInit = true;

        mScrollView = GetComponent<ScrollRect>();
        if (mScrollView == null)
        {
            Debug.LogError("CenterOnChild: No ScrollRect");
            return;
        }
        mContainer = mScrollView.content;

        GridLayoutGroup grid = mContainer.GetComponent<GridLayoutGroup>();
        if (grid == null)
        {
            Debug.LogError("CenterOnChild: No GridLayoutGroup on the ScrollRect's content");
            return;
        }

        mScrollView.movementType = ScrollRect.MovementType.Unrestricted;
        mScrollView.decelerationRate = 0.0001f;

        this.InitCenterPositions();
    }

    void Update()
    {
        if (mState == CenteringState.Ready)
        {
            Vector3 current = mContainer.localPosition;
            if (mSampleSpeeds.Count >= mSampleCount)
            {
                mSampleSpeeds.RemoveAt(0);
            }
            mSampleSpeeds.Add(Vector3.Distance(mPrevious, current));

            int sample = 0;
            for (int i = mSampleSpeeds.Count - 1; i >= 0; i--)
            {
                if (mSampleSpeeds[i] < mMinSpeed * Time.deltaTime)
                {
                    sample++;
                }
            }
            if (sample == mSampleCount)
            {
                mState = CenteringState.Centering;
                mTargetPosition = FindClosestCenter(current.y);
            }

            mPrevious = current;
        }

        if (mState == CenteringState.Centering)
        {
            Vector3 v = mContainer.localPosition;
            v.y = Mathf.Lerp(mContainer.localPosition.y, mTargetPosition, mSpeed * Time.deltaTime);
            mContainer.localPosition = v;
            if (Mathf.Abs(mContainer.localPosition.y - mTargetPosition) < 0.01f)
            {
                mState = CenteringState.Finish;
            }
        }
    }

    public void OnEndDrag(PointerEventData eventData)
    {
        mPrevious = mContainer.localPosition;
        mState = CenteringState.Ready;
        mSampleSpeeds.Clear();
    }

    public void OnDrag(PointerEventData eventData)
    {
        mState = CenteringState.Init;
        mPrevious = mContainer.localPosition;
    }

    private float FindClosestCenter(float currentPos)
    {
        int childIndex = 0;
        float closest = 0;
        float distance = Mathf.Infinity;

        for (int i = 0; i < mChildrenPos.Count; i++)
        {
            float p = mChildrenPos[i];
            float d = Mathf.Abs(p - currentPos);
            if (d < distance)
            {
                distance = d;
                closest = p;
                childIndex = i;
            }
        }

        GameObject centerChild = mContainer.GetChild(childIndex).gameObject;

        if (OnCenter != null)
            OnCenter(centerChild);
        if (OnIndexCenter != null)
            OnIndexCenter(childIndex);

        return closest;
    }

    public void InitCenterPositions()
    {
        GridLayoutGroup grid = mContainer.GetComponent<GridLayoutGroup>();
        mChildrenPos.Clear();

        RectTransform rectTran = mScrollView.GetComponent<RectTransform>();
        float childPosy = -mShowCount / 2 * grid.cellSize.y;

        if (mShowCount % 2 == 0)
        {
            childPosy = -(mShowCount - 1) * 0.5f * grid.cellSize.y;
        }

        mChildrenPos.Add(childPosy);

        for (int i = 0; i < mContainer.childCount - 1; i++)
        {
            childPosy += (grid.cellSize.y + grid.spacing.y);
            mChildrenPos.Add(childPosy);
        }
    }
}
```

# 三、运行效果
<img src = "unity-scrollview/calender.png" width= "300"  align="center">

<style>
		table th:first-of-type{width:350px;}
</style>

|参考|说明|
|:--|:--:|
|《[UGUI ScrollRect滑动居中CenterOnChild实现 ](http://www.cnblogs.com/suoluo/p/5535420.html)》|参考的这篇文章，算法思路简单有效，在其基础上进扩展很方便。我这里做了一些调整，就是当滑动快要停止的时候，才寻找最近的一个中心位置，而原文是在手指滑动结束后寻找中心位置。|