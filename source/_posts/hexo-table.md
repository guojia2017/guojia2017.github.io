---
title: Hexo表格
date: 2017-03-28 10:37:01
tags:
---

#### Markdown语法

```cs
| 排序方法 | 平均情况 | 最好情况 | 最坏情况 | 辅助空间 | 稳定性 |
|:-----|:-----|:-----|:-----|:-----|:-----|
| 冒泡排序 | O(n²) | O(nlogn) | O(n²) | O(1) | 稳定 |
| 简单选择 | O(n²) | O(n²) | O(n²) | O(1) | 稳定 |
| 直接插入 | O(n²) | O(n) | O(n²) | O(1) | 稳定 |
| 希尔排序 | O(nlogn)~O(n²) | O(n^1.3) | O(n²) | O(1) | 不稳定 |
| 堆排序 | O(nlogn) | O(nlogn) | O(nlogn) | O(1) | 不稳定 |
| 归并排序 | O(nlogn) | O(nlogn) | O(nlogn) | O(n) | 不稳定 |
| 快速排序 | O(nlogn) | O(nlogn) | O(n²) | O(nlogn)~O(n) | 不稳定 |
```

<!--more-->

#### 效果

<style>
	table th:first-of-type{width : 100px;	}
	table th:nth-of-type(2){width : 100px ;}
	table th:nth-of-type(3){width : 100px ;}
	table th:nth-of-type(4){width : 100px ;}
	table th:nth-of-type(5){width : 100px ;}
	table th:nth-of-type(6){width : 100px ;}
</style>

| 排序方法 | 平均情况 | 最好情况 | 最坏情况 | 辅助空间 | 稳定性 |
| :----- | :----- | :----- | :----- | :----- | :----- |
| 冒泡排序 | O(n²) | O(nlogn) | O(n²) | O(1) | 稳定 |
| 简单选择 | O(n²) | O(n²) | O(n²) | O(1) | 稳定 |
| 直接插入 | O(n²) | O(n) | O(n²) | O(1) | 稳定 |
| 希尔排序 | O(nlogn)~O(n²) | O(n^1.3) | O(n²) | O(1) | 不稳定 |
| 堆排序 | O(nlogn) | O(nlogn) | O(nlogn) | O(1) | 不稳定 |
| 归并排序 | O(nlogn) | O(nlogn) | O(nlogn) | O(n) | 不稳定 |
| 快速排序 | O(nlogn) | O(nlogn) | O(n²) | O(nlogn)~O(n) | 不稳定 |

#### 说明
```cs
第一行：纵标目。
第二行：对齐方式 |:-----|左对齐，|:-----:|居中，|-----:|右对齐。
每列的宽度是根据对应列里最长的文本来决定。
```

> 
|转载|
|:----|
|《[Hexo表格的写法](http://www.714.hk/msi-182876-1-1.html)》|
