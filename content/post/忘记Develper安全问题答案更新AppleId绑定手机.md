---
title:       "忘记Develper安全问题答案，更新Apple Id绑定手机"
subtitle:    ""
description: ""
date:        2018-04-24
author:      ""
image:       ""
tags:        ["忘记Develper安全问题答案", "更新AppleId绑定手机"]
categories:  ["Tech" ]
---

最近登录ITC,发现苹果又更新协议了，正常这个时候都是想直接到developer.apple.com直接点同意协议就👌，不过这次打开developer网站发现需要绑定一个手机号码。
![image.png](https://upload-images.jianshu.io/upload_images/453533-9d4cebaf04f6b4be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/453533-fcfa4509f41fecde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/453533-667b635509811cdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击Edit Phone Number按钮，直接跳转到登录页面，输入账号密码点击登录，弹出了安全问题回答界面，WTF!!! 我是新来的iOS，我怎么知道这个安全问题的答案？联系申请账号的同事？他也不一定记得呀？怎么联系？心中瞬间奔腾着十万只草泥马！
不过后来研究发现，其实可以点击Get Support PIN通过其他设备验证登录。
![image.png](https://upload-images.jianshu.io/upload_images/453533-a1353b4b203101fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/453533-60b5a9f22b2ced02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
首先，在一台手机上登录AppID,登录成功后点击Generate PIN,发送登录请求。
![image.png](https://upload-images.jianshu.io/upload_images/453533-781294692542f4f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
此时手机上会显示在什么位置有一个登录请求，点击确认即可弹出4位PIN码，将PIN码输入到网站中，即可登录成功了！

![image.png](https://upload-images.jianshu.io/upload_images/453533-ac7906403d02aeed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在REACHABLE AT 点击 Add More,选择Phone既可填入需要绑定的手机号码，发送验证码，绑定成功大功告成！

现在重新登录ITC，发现协议更新提示已经不见了，又可以开心的更新发布app啦 ✌️
