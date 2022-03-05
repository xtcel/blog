---
title:       "iOS旋转图片锯齿问题"
subtitle:    ""
description: ""
date:        2015-07-31
author:      "xtcel"
image:       ""
tags:        ["iOS", "锯齿"]
categories:  ["iOS" ]
---

#### 缘由

最近做项目，需要对一个小滑块进行旋转，滑块上面有一张图片。发现滑块只要一滑动就出现锯齿，后来发现是由于旋转变形时，图片仿射的多造成的。于是想如果在图片的周围加上一圈透明区域，那锯齿的就变透明了，这样我们就看不到，也就消除了锯齿的问题。

#### 解决之道

###### 以下是给图片加一个像素透明区域的代码： 创建一个category UIImage+Antialiase UIImage+Antialiase.h，代码如下：

```objectivec
@interface UIImage (Antialiase)
//创建抗锯齿图片
- (UIImage *)antialiaseImage;
//创建抗锯齿图片，并且调整其尺寸和缩放比例
- (UIImage *)antialiaseImageWithSize:(CGSize)size scale:(CGFloat)scale;
@end
```

###### UIImage+Antialiase.m，代码如下：

```objectivec
@implementation UIImage (Antialiase)
- (UIImage *)antialiaseImage {
  return [self antialiaseImageWithSize:self.size scale:self.scale];
  }
- (UIImage *)antialiaseImageWithSize:(CGSize)size scale:(CGFloat)scale { UIGraphicsBeginImageContextWithOptions(size, NO, scale); [self drawInRect:CGRectMake(1, 1, size.width-2, size.height-2)]; UIImage *image = UIGraphicsGetImageFromCurrentImageContext(); UIGraphicsEndImageContext(); return image;
}
@end
```

以下是具体使用的情况代码：

```objectivec
UIImage *sliderImage = [UIImage imageNamed:@"image_name"];
//给图片外沿添加一个像素的透明区域，以达到抗锯齿的效果
sliderImage = [sliderImage antialiaseImage];
```

这样就可以解决锯齿的问题了。
