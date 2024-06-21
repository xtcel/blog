---
title:       "iOS控件传任意类型值"
subtitle:    ""
description: ""
date:        2018-11-04
author:      ""
image:       ""
tags:        ["iOS"]
categories:  ["iOS" ]
---

iOS控件传任意类型值

#### 一般方法

一般可以用打tag的方法来传值:

```objectivec
[button addTarget:self action:@selector(action:) forControlEvents:UIControlEventTouchUpInside];
[button setTag:10];
```

然而这样只能传递基础类型，通常需要传递Model或者数组。明显使用tag不能实现我们的需求。

是否有更好的方案呢？

有的，利用object-c的runtime特性。

#### runtime

```obj
#import<objc/runtime.h>

[button addTarget:self action:@selector(buttonClickedEvent:) forControlEvents:UIControlEventTouchUpInside];

// RunTime 传值 将需要传的值放到@"需要传的值"这个位置
objc_setAssociatedObject(button, @"key", value, OBJC_ASSOCIATION_RETAIN_NONATOMIC);

- (void)buttonClickedEvent:(id)sender {
    //获取到通过runtime传过来的值
    id value = objc_getAssociatedObject(sender, @"key");
}
```
