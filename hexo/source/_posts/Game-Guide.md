---
title: Game Guide
date: 2017-02-28 11:20:10
tags:
---

简单的游戏新手引导采用队列依次触发的方式，如果更复杂的新手引导可以考虑树的结构来引导。

<!--more-->

```cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

/// <summary>
/// 新手引导
/// </summary>

public class Guide : MonoSingleton
{
    private Dictionary<string, GuideStep> mGuidesDictionary;
    private Dictionary<string, GuideData> mDatasDictionary;
    private GuideStep mCurrentStep;

    private string mStep;
    private GuideState mState = GuideState.WaitingStart;
    private bool mIsInit = false;

    private string mLocalKey = string.Empty;
    private int mLocalStep;
    private int mServerStep;

    private enum GuideState
    {
        WaitingStart,
        Guiding,
        Finish,
    }

    public void DownloadGuideStep(int serverStep)
    {
        if (this.mState != GuideState.WaitingStart)
            return;

        this.InitKey();
        this.mServerStep = serverStep;
    }

    public override void Init()
    {
        base.Init();
        this.InitKey();

        mGuidesDictionary = new Dictionary<string, GuideStep>();
        mDatasDictionary = new Dictionary<string, GuideData>();

        this.mStep = "0-0";

        this.Read();
        this.Register();
        this.mIsInit = true;
    }

    public void Init(List<GuideModel> config)
    {
        List<GuideData> datas = new List<GuideData>();
        foreach (GuideModel model in config)
        {
            datas.Add(new GuideData(model));
        }
        this.Init(datas);
    }

    void InitKey()
    {
        if (string.IsNullOrEmpty(this.mLocalKey))
            this.mLocalKey = UserInfo.GetInstance().UserData.info.name + "Guide";
    }

    private void Init(List<GuideData> datas)
    {
        this.Init();

        // Function 1 : Next Step . 
        foreach (GuideData data in datas)
        {
            if (!this.mDatasDictionary.ContainsKey(data.mStep))
            {
                GuideStep guideStep = GuideStepFactory.Create(data.mCommand);
                guideStep.InitData(data);
                guideStep.OnComplete += this.OnGuideStepFinish;

                this.mDatasDictionary.Add(data.mStep, data);
                this.mGuidesDictionary.Add(data.mStep, guideStep);
            }
        }
    }

    public bool IsInit()
    {
        return this.mIsInit;
    }

    void OnGuideStepFinish(GuideStep step)
    {
        this.SaveData(step.mData);
        this.MoveNext(step.mData.mNextStep);

        if (step.mData.mTrigger == GuideTrigger.Timer)
        {
            this.mReceive3DTouch = true;
        }
    }

    /// <summary>
    /// 下一步引导
    /// </summary>
    /// <param name="ID"></param>
    /// <param name="isRestroeGuide">是否是新手引导某一步的恢复</param>
    void MoveNext(string ID, bool isRestroeGuide = false)
    {
        LOG.LogWarning(this.GetType().Name + "===============================");
        LOG.LogWarning(this.GetType().Name + " " + ID);

        GuideStep next = null;

        if (mGuidesDictionary.ContainsKey(ID))
        {
            next = mGuidesDictionary[ID];
        }

        if (next == null)
        {
            GuideOver();
        }
        else
        {
            next.mIsRestoreGuide = isRestroeGuide;
            this.ChangeStep(next);
        }
    }

    bool mReceive3DTouch = false;

    void ChangeStep(GuideStep next)
    {
        this.mCurrentStep = next;
        this.mState = GuideState.Guiding;
        next.Enter();
    }

    private void SaveData(GuideData data)
    {
        if (data.mSave)
        {
            this.SaveToLocal(data.mStep);
            this.SaveToServer(data.mStep);
        }
    }

    void Register()
    {
        EventManager.RegisterEvent(EventTable.OnMapLoadComplete, this.StartGuide);
        EventManager.RegisterEvent(EventTable.ResetGuide, this.ResetGuide);
    }

    void UnRegister()
    {
        EventManager.UnregisterEvent(EventTable.OnMapLoadComplete, this.StartGuide);
        EventManager.UnregisterEvent(EventTable.ResetGuide, this.ResetGuide);
    }

    public void StartGuide()
    {
        if (this.mState != GuideState.WaitingStart)
            return;

        this.CompareStep();

        if (this.mDatasDictionary.ContainsKey(this.mStep))
        {
            this.MoveNext(this.mDatasDictionary[this.mStep].mNextStep, true);
        }
    }

    void CompareStep()
    {
        if (this.mLocalStep > this.mServerStep)
        {
            this.mStep = this.ConvertStep(this.mLocalStep);
            this.SaveToServer(this.mStep);
        }
        else
        {
            this.mStep = ConvertStep(this.mServerStep);
            this.SaveToLocal(this.mStep);
        }

        Debug.LogError("StartGuide！" + this.mStep);
    }

    //  引导结束
    void GuideOver()
    {
        this.mState = GuideState.Finish;
    }

    public bool IsInGuiding()
    {
        return this.mState == GuideState.Guiding;
    }

    public bool CanTouch()
    {
        return this.mReceive3DTouch || this.mCurrentStep.mData.mTrigger == GuideTrigger.Timer;
    }

    private void SaveToLocal(string step)
    {
        PlayerPrefs.SetString(mLocalKey, step);
    }

    private void SaveToServer(string step)
    {
        global_instance.Instance._client_session.RequestSaveGuide(ConvertStep(step));
    }

    private void Read()
    {
        string local = PlayerPrefs.GetString(mLocalKey);
        this.mLocalStep = this.ConvertStep(local);
        Debug.LogError("LocalStep：" + this.mLocalKey + " " + local);
    }

    public void ResetGuide()
    {
        PlayerPrefs.SetString(mLocalKey, "0-0");
        global_instance.Instance._client_session.RequestSaveGuide(ConvertStep("0-0"));
        Debug.LogError("重置新手引导，重新启动游戏！" + this.GetType().Name);
    }

    private string ConvertStep(int dataParam)
    {
        int group = dataParam / 10000;
        int step = dataParam % 10000;
        string str = group.ToString() + "-" + step.ToString();
        return str == null ? string.Empty : str;
    }

    private int ConvertStep(string step)
    {
        List<int> list = step.ToIntList('-');
        if (list.Count == 2)
        {
            return list[0] * 10000 + list[1];
        }
        return 0;
    }
}

```

<style>
 table align="center"{width:300px; }
</style>

|相关文章|
|:--|
|《[游戏新手引导的制作原理](http://blog.csdn.net/anypkv/article/details/38845637)》|
|《[游戏新手指引的一种具体做法](http://www.gameres.com/491821.html)》|



