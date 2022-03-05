---
title:       "UICollectionView自定义布局--实现课表布局"
subtitle:    "UICollectionView自定义布局--实现课表布局"
description: "UICollectionView自定义布局--实现课表布局"
date:        2015-08-06
author:      "xtcel"
image:       ""
tags:        ["UICollectionView", "iOS", "自定义CollectionViewLayout"]
categories:  ["iOS" ]
---

#### 需求

自iOS6加入UICollectionView以来，UICollectionView在各大项目中的使用就非常频繁。UICollectionView灵活的布局可以实现很多以前使用UITableView根本无法完成的效果。这篇文章将会带你一步一步的使用自定义的UICollectionView布局来实现一个课表。为了方便读者，项目的代码在[我的github](https://github.com/yongca887/CustomCollectionViewLayout)上。 项目最终实现的效果如下图所示：  
![课表.png](http://qn.xtcel.com/blog/image/png/iOS-TimeTable.png)

### 分析

从上图可以看出，课表主要分为三块:

- 顶部显示星期数的WeekHeaderView
- 侧边显示第几节课的CourseHeaderView
- 中间显示的课程内容Cell。

### 自定义布局

在一些简单的项目中，在使用UICollectionView进行布局时，通常都会使用UICollectionViewFlowLayout,这是Apple默认实现一个流式布局，什么是流式？就是像水流一样一个接一个。UITableView就是流式，下一个Cell的位置是在上一个Cell位置之后。而课表的布局却是每个课程Cell不是一个接一个的，前一个Cell的位置和后一Cell的位置没有关系，课程Cell的位置取决于是从第几节课上到第几节课以及是星期几上课。位置取决于课程内容本身。 如果有一个依赖内容的布局，那就是暗示需要写自定义的布局类了，如果使用直接UICollectionViewFlowLayout你将会遇到很多问题。 怎么自定义布局？ 继承UICollectionViewFlowLayout的父类UICollectionViewLayout。这是面向对象编程中惯用的自定义某类的手法。你要自定义一个UIButton,你只需继承UIButton的父类和实现UIButton实现的接口，加入一些自己的实现就可以了。查看UICollectionViewLayout官文文档，其中讲到如何自定义一个布局对象。

文档原文如下：

> Methods to Override Every layout object should implement the following methods:  
> collectionViewContentSize  
> layoutAttributesForElementsInRect:  
> layoutAttributesForItemAtIndexPath:  
> layoutAttributesForSupplementaryViewOfKind:atIndexPath:(if your layout supports supplementary views)  
> layoutAttributesForDecorationViewOfKind:atIndexPath: (if your layout supports decoration views)  
> shouldInvalidateLayoutForBoundsChange:  
> These methods provide the fundamental layout information that the collection view needs to place contents on the screen. Of course, if your layout does not support supplementary or decoration views, do not implement the corresponding methods.

我英语不好，但是大概可以看出来，要自定义UICollectionView布局不实现这些方法是不行的。  
那这些方法都是些什么鬼 ？骚年不要急，听我慢慢道来。  
collectionViewContentSize  
由于collection view对它的content并不知情，所以布局首先要提供的信息就是内容区域大小，这样collection view才能正确的管理滚动。内容的总大小包含cell、supplementary views和decoration views。  
在本项目中，ContentSize为上图红色框大小，当contentSize比屏幕的大小更大时，就可以上下或者左右滚动。

```objectivec
- (CGSize)collectionViewContentSize { CGFloat collectionViewWidth = self.collectionView.bounds.size.width; CGFloat minContentWidth = courseHeaderWidth + (MinWeekHeaderWidth * kWeekNumber); CGFloat contentHeight =  weekHeaderHeight + (CellHeight * KCourseNumber); CGSize contentSize = CGSizeMake((collectionViewWidth layoutAttributesForElementsInRect:

```

### 解读

这是自定义布局类中最重要的一个方法，该方法返回所有的元素的布局属性数组(UICollectionViewLayoutAttributes)，包括所有的cell布局属性(主要是位置信息)、supplementary、decoration views。collection view调用这个方法并传递一个自身坐标系统中的矩形过去。这个矩形代表了这个视图的可见矩形区域（也就是它的bounds），你需要准备好处理传给你的任何矩形。 UICollectionViewLayoutAttributes类包含了collection view内item的所有相关布局属性。默认情况下，这个类包含frame，center，size，transform3D，alpha，zIndex属性(properties)，和hidden特性(attributes)。如果你的布局想要控制其他视图的属性（比如，背景颜色），你可以建一个UICollectionViewLayoutAttributes的子类，然后加上你自己的属性。 layoutAttributesForItemAtIndexPath: 有时，collection view会为某个特殊的cell，supplementary或者decoration view向布局对象请求布局属性，而非所有可见的对象。这就是当其他三个方法开始起作用时，你实现的layoutAttributesForItemAtIndexPath：需要创建并返回一个单独的布局属性对象，这样才能正确的格式化传给你的index path所对应的cell。 你可以通过调用 +[UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:]这个方法，然后根据index path修改属性。为了得到需要显示在这个index path内的数据，你可能需要访问collection view的数据源。到目前为止，至少确保设置了frame属性，除非你所有的cell都位于彼此上方。 layoutAttributesForSupplementaryViewOfKind:atIndexPath: layoutAttributesForDecorationViewOfKind:atIndexPath: shouldInvalidateLayoutForBoundsChange: 当collection view的bounds改变时，布局需要告诉collection view是否需要重新计算布局。我的猜想是：当collection view改变大小时，大多数布局会被作废，比如设备旋转的时候。因此，一个幼稚的实现可能只会简单的返回YES。虽然实现功能很重要，但是scroll view的bounds在滚动时也会改变，这意味着你的布局每秒会被丢弃多次。根据计算的复杂性判断，这将会对性能产生很大的影响。
