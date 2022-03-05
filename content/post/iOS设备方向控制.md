---
title:       "iOS设备方向控制"
subtitle:    ""
description: ""
date:        2015-07-30
author:      "xtcel"
image:       ""
tags:        ["iOS", "设备方向"]
categories:  ["iOS" ]
---

#### First

最近在写自己的项目的时候需要用到根据设备方向控制视图的方向。开始我的需求是iPad只可以左右翻转，iPhone只能正向。首先要实现设备方向翻转需要到General ->Device Orientation中勾选所需要可以启用的设备方向，iOS设备支持四种设备方向分别为：

- Portrait (正向)
- Upside down (颠倒)
- Landscape Left （向左侧翻转）
- Landscape Right (向右侧翻转) 我勾选了如下选项

#### Next

接下来我在ApplicationDelegate的application: supportedInterfaceOrientationsForWindow:方法中添加判断语句：

```objectivec
- (NSUInteger)application:(UIApplication *)application supportedInterfaceOrientationsForWindow:(UIWindow *)window {
  if (ISIPAD) {
      return UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight;
    } else {
      return UIInterfaceOrientationMaskPortrait;
    }
  }
}
```

ISIPAD的宏定义如下：

```objectivec
#define ISIPAD (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)
```

这样我就实现刚才所需要实现的功能。 然而随着开发的继续需求也发生了变化，新的需求是需要在特定的ViewController中iPhone也可以左右翻转。由于上面的方法是全局判断，这样就不能实现这个需求。现在需要ViewController层面的设备方向控制方法。我查看Api文档发现如下两个方法可以实现ViewController层面的设备方向控制：

```objectivec
- (BOOL)shouldAutorotate;
- (NSUInteger)supportedInterfaceOrientations;
- (BOOL)shouldAutorotate方法控制本ViewController
```

是否可以翻转界面，其官方描述为：

> Returns a Boolean value indicating whether the view controllers contents should auto rotate.  
>  When the user changes the device orientation, the system calls this method on the root view controller or the topmost presented view controller that fills the window. If the view controller supports the new orientation, the window and view controller are rotated to the new orientation. This method is only called if the view controller's shouldAutorotate method returns YES.  
> 
>  Override this method to report all of the orientations that the view controller supports. The default values for a view controller's supported interface orientations is set to UIInterfaceOrientationMaskAll for the iPad idiom and UIInterfaceOrientationMaskAllButUpsideDown for the iPhone idiom.  
> 
>  The system intersects the view controller's supported orientations with the app's supported orientations (as determined by the Info.plist file or the app delegate's application:supportedInterfaceOrientationsForWindow: method) to determine whether to rotate.  
>  **Return Value**  
>  YES if the content should rotate, otherwise NO. Default value is YES.  
>  (NSUInteger)supportedInterfaceOrientations方法返回ViewController支持的设备翻转方向的类型： 接口文档描述为： Returns all of the interface orientations that the view controller supports. 返回值：UIInterfaceOrientationMask类型的bit-mask值  
> A bit mask specifying which orientations are supported. See UIInterfaceOrientationMask for valid bit-mask values. The value returned by this method must not be 0.

UIInterfaceOrientationMask类型的定义如下：

```objectivec
typedef enum : NSUInteger {
  UIInterfaceOrientationMaskPortrait = (1 << UIInterfaceOrientationPortrait ),
  UIInterfaceOrientationMaskLandscapeLeft = (1 << UIInterfaceOrientationLandscapeLeft ),
  UIInterfaceOrientationMaskLandscapeRight = (1 << UIInterfaceOrientationLandscapeRight ),
  UIInterfaceOrientationMaskPortraitUpsideDown = (1 << UIInterfaceOrientationPortraitUpsideDown ),
  UIInterfaceOrientationMaskLandscape = (UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight ),
  UIInterfaceOrientationMaskAll = (UIInterfaceOrientationMaskPortrait | UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight | UIInterfaceOrientationMaskPortraitUpsideDown ),
  UIInterfaceOrientationMaskAllButUpsideDown = (UIInterfaceOrientationMaskPortrait | UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight ),
 } UIInterfaceOrientationMask;
```

从定义中我们看到一些预定义好的一些值：

```objectivec
- UIInterfaceOrientationMaskPortrait(正常)
- UIInterfaceOrientationMaskLandscapeLeft (左侧翻转)
- UIInterfaceOrientationMaskLandscapeRight (右侧翻转)
- UIInterfaceOrientationMaskPortraitUpsideDown (颠倒) -UIInterfaceOrientationMaskLandscape =(UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight )（左右翻转）
- UIInterfaceOrientationMaskAll = (UIInterfaceOrientationMaskPortrait | UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight | UIInterfaceOrientationMaskPortraitUpsideDown )(所有类型)
- UIInterfaceOrientationMaskAllButUpsideDown = (UIInterfaceOrientationMaskPortrait | UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight )(支持左右翻转和正常模式)
```

当然我们可以自己随意组合自己需要的模式，只需用 | 号将他们连接起来。 上面的描述文件中我们可以知道，当设备方向发生变化时，会调用根视图的这个方法或者顶层视图的这个方法，通过这个特性，我们可以在RootViewController中进行设置，一般根视图都是UINavigationController,那么只需要新建一个UINavgationCtorller子类或者创建UINavigationController的Category中重写这个方法就可以了。 以下是UINavigationController的Category：

```objectivec
#import "CSMainViewController.h"
#import"UINavigationController+InterfaceOrientation.h"
@implementation UINavigationController (InterfaceOrientation)
- (BOOL)shouldAutorotate {
  return self.topViewController.shouldAutorotate;
}

- (NSUInteger)supportedInterfaceOrientations {
   if (ISIPAD) {
     //如果是ipad，只能左右翻转 return UIInterfaceOrientationMaskLandscapeLeft | UIInterfaceOrientationMaskLandscapeRight;
     } else {
       //如果是iPhone并且是MainViewController类，则可以左右翻转和正向，其他视图都只能正向，不能翻转
       if ([self.topViewController isKindOfClass:[CSMainViewController class]]) {
          return UIInterfaceOrientationMaskAllButUpsideDown;
          } else {
            return UIInterfaceOrientationMaskPortrait;
          }
    }
}
@end
```

#### End

最后将Category的头文件导到ApplicationDelegate就可以了，终于一切和我们想象的一样工作！
