---
title:       "UIButton 设置 title 文字时闪动"
subtitle:    ""
description: ""
date:        2017-12-22
author:      "xtcel"
image:       ""
tags:        ["UIButton", "iOS"]
categories:  ["iOS" ]
---

UIButton-system类型 动态设置title文字时闪动
解决方案：

```objectivec
self.sendCodeButton.titleLabel?.text = "\(self.remainCount)s重新发送"
self.sendCodeButton.setTitle("\(self.remainCount)s重新发送", for: .normal)
```
