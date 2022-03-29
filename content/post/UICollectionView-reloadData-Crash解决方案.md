---
title:       "UICollectionView reloadData Crash 解决方案"
subtitle:    ""
description: "UICollectionView如果更新datasource，需要更新UI，直接使用
[self.datas removeAllObjects];[UICollecitonView reloadData];直接崩溃"
date:        2018-01-18
author:      "xtcel"
image:       ""
tags:        ["UICollection"]
categories:  ["iOS" ]
---

### 问题

UICollectionView如果更新datasource，需要更新UI，直接使用

```objc
[self.datas removeAllObjects];
[UICollecitonView reloadData];
```

就会直接崩溃。

### 崩溃log

```objc
Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'UICollectionView received layout attributes for a cell with an index path that does not exist: <NSIndexPath: 0xc000000000000116> {length = 2, path = 1 - 0}'
*** First throw call stack:
(0x1824a2fe0 0x180f04538 0x1824a2eb4 0x182f3a720 0x188630a9c 0x18862fe90 0x18862f388 0x1885d107c 0x1857c1274 0x1857b5de8 0x1857b5ca8 0x18573134c 0x1857583ac 0x188852524 0x188dc89f8 0x188dc8b9c 0x18245142c 0x182450d9c 0x18244e9a8 0x18237eda4 0x183de8074 0x188639058 0x10010dd58 0x18138d59c)
libc++abi.dylib: terminating with uncaught exception of type NSException
```

### 解决方案

```objc
[self.collectionView reloadData];
[self.collectionView.collectionViewLayout invalidateLayout];
```

在reloadData之后将当前的布局设置失效invalidateLayout，则collectionView会重新刷新布局，不会沿用旧的布局导致获取不到数据，导致崩溃。
