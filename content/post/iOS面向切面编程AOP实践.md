---
title:       "iOS面向切面编程AOP实践"
subtitle:    "iOS面向切面编程AOP实践"
description: "iOS面向切面编程AOP实践"
date:        2016-07-13
author:      "xtcel"
image:       ""
tags:        ["iOS", "AOP", "Runtime"]
categories:  ["iOS" ]
---

#### 什么是AOP

AOP：Aspect Oriented Programming，译为面向切面编程。

在不修改源代码的情况下，通过运行时给程序添加统一功能的技术。

我觉得其中有两层涵义：

* 第一：不修改源代码，即尽可能的解耦。
* 第二：添加统一的功能，即我们能实现的是添加统一的单一的功能，在某处使用AOP，我们只能实现一项单一的功能。如：日志记录。当然你可以添加多个AOP的模块到项目中，每一个实现不同功能，但是每一个功能必须是单一的。

主要功能：日志记录，性能统计等。

#### iOS中如何实现AOP

有心的读者可能会发现，我在上面的AOP简介中并没有原话搬用百度百科的AOP简介，因为这是一篇iOS的AOP教程，在OC中我们就是用运行时来给实现AOP的。（我们基本不会使用预编译方式来实现AOP）

在iOS中实现AOP的核心技术是Runtime,使用Runtime的Method Swizzling黑魔法，我们可以移花接木，在运行时将方法的具体实现添油加醋、偷梁换柱。

点此移步了解[Method Swizzling](http://www.cocoachina.com/ios/20160121/15076.html)

#### AOP技术实现

越是底层的框架越是难用，任何语言皆是如此，同样Method Swizzling也不例外。那是否有一个第三库，可以让我们轻松驾驭Method Swizzling黑魔法呢？

当然有，而且不止一个，其中最著名的要数[Aspects](https://github.com/steipete/Aspects)，Aspects的使用非常简单，整个库封装为两个方法：

```Objective-C
+ (id<AspectToken>)aspect_hookSelector:(SEL)selector
                      withOptions:(AspectOptions)options
                       usingBlock:(id)block
                            error:(NSError **)error;
```

```Objective-C
- (id<AspectToken>)aspect_hookSelector:(SEL)selector
                      withOptions:(AspectOptions)options
                       usingBlock:(id)block
                            error:(NSError **)error;
```

实际为同一个方法，这两个方法是同名不同类型的方法，一个是静态类方法，一个是成员方法。
使用这个方法可以给类的实例方法添加一个Block，并且对这个类的所有对象都会起作用。

所有的调用,都会是线程安全的。Aspects 使用了Objective-C 的消息转发机会,会有一定的性能消耗。所有对于过于频繁的调用,不建议使用 Aspects。Aspects更适用于视图/控制器相关的等每秒调用不超过1000次的代码。

###### 代码示例

在调试应用时,使用Aspects动态添加日志记录功能。

```objective-c
[UIViewController aspect_hookSelector:@selector(viewWillAppear:) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo) {
    //NSLog(@"😜😜😜Appear:--> %@", aspectInfo.instance);（为什么不使用此方式，请查看评论）
    NSLog(@"😜😜😜Appear:--> %@", NSStringFromClass([aspectInfo.instance class]));
} error:NULL];
```

通过这段代码，我们给UIViewController的viewWillAppear:方法添加了一个钩子，每当在调用viewWillAppear:后就会执行block中的代码。在此我们打印了一段Log（加上emoji表情就更好找log啦），通过log我们可以看到当前显示的页面的VC名称，从而快速定位到该类。还可以在ViewController的Dealloc时打印log：

```objective-c
[UIViewController aspect_hookSelector:NSSelectorFromString(@"dealloc") withOptions:AspectPositionBefore usingBlock:^(id<AspectInfo> aspectInfo) {
        //NSLog(@"😂😂😂Dealloc:---->: %@", aspectInfo.instance);（为什么不使用此方式，请查看评论）
        NSLog(@"😂😂😂Dealloc:---->: %@", NSStringFromClass([aspectInfo.instance class]));
    } error:NULL];
```

与上一段代码的微小差别是Selector换成了NSSelectorFromString(@"dealloc")，而不是@selector(dealloc)，这是因为在ARC下面是不能直接手动调用Dealloc的，@selector(dealloc)会被编译器直接报错。

通过这个log，我们可以知道ViewController是否释放，如果没有释放很可能就是有循环引用，这时你务必仔细检查你的代码，这在性能调试和debug中非常有用。

#### AOP实战

在实际的项目开发中，事件统计是很多APP都会添加一项重要功能，它能统计用户的行为、商品的销售状况、商品查看数据等，今天的AOP实战是利用AOP实现APP事件统计。

##### 这样统计？

假设产品有这么个需求：当用户在详情页点击添加到购物车按钮时，记录一下事件。我们实现起来大概会是这样

```objective-c
- (void)onBuyButtonClicked:(id)sender
{
    [XXXAnalytics track:eventName properties:properties];
}
```

这个需求就这样轻松搞定了，但细细想想还是有不少问题的：

* 页面上会有其他的 Button，可能每个 Button 都要放上这么一段代码。
* 这些统计其实跟具体的业务无关，没必要跟业务代码混杂在一起，不优雅。
* 当改版或者重构时，有可能忘了把相应的事件统计代码迁移过去。

##### 使用AOP实现统计

基于上面的问题，需要将事件统计这段代码抽离，与具体点击事件逻辑代码解耦。通过AOP在运行时将事件统计的代码加入到方法中正是这个问题的最佳解。代码大概如下：

```objective-c
[PBAGoodsDetailViewController aspect_hookSelector:@selector(onBuyButtonClicked:) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo) {
        [XXXAnalytics track:eventName properties:properties];
    } error:NULL];
```

###### 多个事件？

当然事件统计往往需要统计多个事件，这时我们只要对该方法稍微抽象一下就可以了，代码如下：

```objective-c
- (void)setupAnalytics
{
    [self trackEventWithClass:aViewController selector:@seletor(onBuyButtonTapped:) event:kSomeEventYouDefined];
    [self trackEventWithClass:bViewController selector:@seletor(followButtonTapped:) event:kAnotherEventYouDefined];
    // ...
}
- (void)trackEventWithClass:(Class)klass selector:(SEL)selector event:(NSString *)event
{
[klass aspect_hookSelector:@selector(selector) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo) {
    [XXXAnalytics track:eventName properties:properties];
    } error:NULL];
}
```

###### 使用plist文件配置事件统计

当事件非常多时，你的setupAnalytics方法将会变得越来越长，而且不好维护。如果我们可以利用一张表格来配置事件统计，看起来会更加直观简洁。
使用Xcode创建一个plist文件，其文件结构如图：
![EventList.plish](https://upload-images.jianshu.io/upload_images/453533-23f4284ad6eb902c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
使用类名作为字典的键，值为一个数组，数组内存放该类下的事件列表，每个事件包含事件ID(EventId)和触发事件的方法名称（MethodName）。
在AppDelegate.m中，添加事件统计的代码如下：

```objective-c
- (void)setupAnalytics
{
    //设置事件统计
    //放到异步线程去执行
    __weak typeof(self) ws = self;
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        //读取配置文件，获取需要统计的事件列表
        NSString *path = [[NSBundle mainBundle] pathForResource:@"EventList" ofType:@"plist"];
        NSDictionary *eventStatisticsDict = [[NSDictionary alloc] initWithContentsOfFile:path];
        for (NSString *classNameString in eventStatisticsDict.allKeys) {
            //使用运行时创建类对象
            const char * className = [classNameString UTF8String];
            //从一个字串返回一个类
            Class newClass = objc_getClass(className);
            NSArray *pageEventList = [eventStatisticsDict objectForKey:classNameString];
            for (NSDictionary *eventDict in pageEventList) {
                //事件方法名称
                NSString *eventMethodName = eventDict[@"MethodName"];
                SEL seletor = NSSelectorFromString(eventMethodName);

                NSString *eventId = eventDict[@"EventId"];

                [self trackEventWithClass:newClass selector:seletor event:eventId];
            }
        }
    });
}
```

至此，一切好像都好像完美了，但人生总是充满了变数。

###### 事件需要传递参数

一个阳光明媚的上午，产品跑过来和我说事件统计需要传递一些参数，比如点击查看商品详情事件需要传递商品ID和商品名称。我当时心中就一万只草泥马在奔腾，但是没办法呀！我们只是搬砖的程序猿，只能低头默默的改。好不容易设计好的架构，眼看就要打回原形。后来仔细研究一番发现，其实Aspects是可以通过Block获取到方法传递的参数的，马上心情好了许多，修改思路马上再脑海形成。

首先，将Block改为```^(id<AspectInfo> aspectInfo, NSDictionary *dict)```，第一个参数一定要为```id<AspectInfo> aspectInfo```,后面接方法传递的对应类型的参数，这样便可以接收到方法调用传递的参数。但是每一个事件需要传递的参数都各不相同，那我们要如何配置呢？
我的方案是：在plist的事件字典中加入一个键为Params，值为数组的键值对。修改后配置文件如下：

![EventListV2](https://upload-images.jianshu.io/upload_images/453533-a0d4cbd4b540a4ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
统计代码：

```objective-c
- (void)setupAnalytics
{
    //设置事件统计
    //放到异步线程去执行
    __weak typeof(self) ws = self;
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        //读取配置文件，获取需要统计的事件列表
        NSString *path = [[NSBundle mainBundle] pathForResource:@"EventList" ofType:@"plist"];
        NSDictionary *eventStatisticsDict = [[NSDictionary alloc] initWithContentsOfFile:path];
        for (NSString *classNameString in eventStatisticsDict.allKeys) {
            //使用运行时创建类对象
            const char * className = [classNameString UTF8String];
            //从一个字串返回一个类
            Class newClass = objc_getClass(className);
            NSArray *pageEventList = [eventStatisticsDict objectForKey:classNameString];
            for (NSDictionary *eventDict in pageEventList) {
                //事件方法名称
                NSString *eventMethodName = eventDict[@"MethodName"];
                SEL seletor = NSSelectorFromString(eventMethodName);
                NSString *eventId = eventDict[@"EventId"];
                NSArray *params = eventDict[@"Params"];
                [self trackEventWithClass:newClass selector:seletor event:eventId params:params];
            }
        }
    });
}
```

统计方法：

```objective-c
- (void)trackEventWithClass:(Class)klass selector:(SEL)selector event:(NSString *)event params:(NSArray *)paramNames
{
    [klass aspect_hookSelector:@selector(selector) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo, NSDictionary *dict) {
        //定义与事件相关的属性信息
        NSMutableDictionary *properties = [NSMutableDictionary dictionary];
        //如果有参数，那么把参数名和参数值拼接在eventID之后
        if (paramNames.count > 0) {
            if ([dict isKindOfClass:[NSDictionary class]]) {
                //获取dict
                for (NSString *paramName in paramNames) {
                    //添加所需参数
                    NSString *paramValue = [dict objectForKey:paramName];
                    properties[paramName] = paramValue;
                }
            }
        }

        [XXXAnalytics track:eventName properties:properties];
    } error:NULL];
}
```

将需要传递的参数以字典格式作为方法的第一个参数，Params中配置事件统计需要传递的参数的Key，通过此方法可以传递任何我们需要传递的参数，使用plist快速、灵活配置需要传递的参数。实战内容到此基本结束，我们使用AOP已经实现了一个低耦合、可灵活配置的事件统计。

#### 还有一些挑战

在使用Aspects中我发现，如果方法为类方法时，并不会回调block。在调用aspect_hookSelector:withOptions:usingBlock:时，报```Aspects: Block signature <NSMethodSignature: 0x7fa13345ce60> doesn't match (null).```错误提示，意思是block不匹配，其根本原因在于无法使用Class获取该Class的类方法，通过runtime只能获取到成员方法，而类方法需要使用该Class的MetaClass获取，MateClass可以使用```object_getClass(newClass)```得到。代码如下：

```objective-c
[ws trackEventWithClass:object_getClass(newClass) selector:seletor event:eventId params:params];
```

修改后虽然不会报错，但是依然不会触发block。查看Aspects的github介绍发现，Aspects压根就不支持类方法，这让我很是苦恼。不过按道理应该是可以的，于是和同事讨论了一下，就使用Method Swizzling做了交换两个类方法的试验，结果是成功了。

查看Aspects的源代码发现，Aspects交换的是成员方法。无奈最后只能修改Aspects的源代码，我在其中一方法中加入了Class类型判断，如果是MetaClass，那么就初始化为类方法，而非成员方法。代码如下：

```objective-c
static void aspect_prepareClassAndHookSelector(NSObject *self, SEL selector, NSError **error) {
    NSCParameterAssert(selector);
    Class klass = aspect_hookClass(self, error);
    //TODO:Edit bu JackYong
    Method targetMethod;
    IMP targetMethodIMP;
    if (class_isMetaClass(klass)) {
        targetMethod = class_getClassMethod(klass, selector);
        targetMethodIMP = method_getImplementation(targetMethod);
    } else {
        targetMethod = class_getInstanceMethod(klass, selector);
        targetMethodIMP = method_getImplementation(targetMethod);
    }
```

修改后block和往常一样被调用了。暂时使用没有遇到什么问题，不过目测应该是有bug的，不然Aspects的开发者早就加了这判断。
Demo：https://github.com/yongca887/AOPDemo

##### Aspects的坑

* 1.无法为类方法添加hooking（通过上面的方法暂时可以解决，不过还是不太建议使用）
* 2.Block无法自动判断参数个数，自动匹配。如果你添加一个无参的方法，而Block中有跟一个参数，那么你会收到Block不匹配的错误。

> 参考
> [iOS 统计打点那些事](http://my.oschina.net/leejun2005/blog/504732)
