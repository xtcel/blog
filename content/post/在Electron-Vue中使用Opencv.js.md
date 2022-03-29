---
title:       "在 Electron 中使用 OpenCV.js"
subtitle:    ""
description: ""
date:        2019-03-15 
author:      ""
image:       ""
tags:        ["Electron", "OpenCV"]
categories:  ["Tech" ]
---

### 简介

##### Electron

[Electron](https://electronjs.org/) 是一个使用 JavaScript, HTML 和 CSS 等 Web 技术创建原生程序的框架，可以一键生成在Window、Linux、Mac的桌面应用，他隔离了平台之间的差异，抽象出一套通用的接口调用。Electron 基于 Chromium 和 Node.js, 让你可以使用 HTML, CSS 和 JavaScript 构建应用。
简单讲Electron可以使用Web技术让你创建跨平台的桌面应用，而Electron本质则是一个Chromium内核的浏览器，基本上Chrome浏览器能做的事情，你都可以实现。

##### Electron-vue

[Electron-vue](https://github.com/SimulatedGREG/electron-vue) 充分利用 vue-cli 作为脚手架工具，加上拥有 vue-loader 的 webpack、electron-packager 或是 electron-builder，以及一些最常用的插件，如vue-router、vuex 等等。Electron-vue很方便的将Vue.js的技术与electron相结合，让我们可以更好的发挥Vue全家桶的加持。

##### Opencv.js

Opencv是跨平台机器视觉库，它轻量级而且高效——由一系列 C 函数和少量 C++ 类构成，同时提供了Python、Ruby、MATLAB等语言的接口，实现了图像处理和计算机视觉方面的很多通用算法。[Opencv.js](https://docs.opencv.org/3.4.1/d5/d10/tutorial_js_root.html)则是opencv为js提供的一套接口。

#### 入门

在网上有很多关于Opencv的Python和C++的教程，而Opencv.js的却非常的少，主要是看官方的教程。我Google以后发现网上有一些Node.js使用Opencv的库，他们主要是使用[opencv4nodejs](https://github.com/justadudewhohacks/opencv4nodejs#readme)开源库，按照教程我在Electron-vue环境下安装了很多次都没有成功。opencv4nodejs使用cmake编译OpenCV，并且自己创建了一套基于Opencv的node.js接口，在使用上方便不少，无奈安装不了最后只能放弃，如果有读者有安装成功的可以留言交流。因为官方教程是直接在Web中直接使用<jscript>引入Opencv.js的，按道理Electron-vue中应该也可以以同样的方式引入。
一开始我尝试了使用ES6 Import引入js文件方式加入Opencv.js，但是一直没有成功，这种方式引入可以更方便的在Vue文件中直接使用Opencv。之后我便尝试使用直接在HTML中插入<jscrpt>方式引入Opencv，大概如下代码所示：

```javascript
let script = document.createElement('script');
        script.setAttribute('async', '');
        script.setAttribute('type', 'text/javascript');
        script.addEventListener('load', () => {
            console.log(cv.getBuildInformation());
            onloadCallback();
        });
        script.addEventListener('error', () => {
            self.printError('Failed to load ' + OPENCV_URL);
        });
        script.src = OPENCV_URL;
        let node = document.getElementsByTagName('script')[0];
        node.parentNode.insertBefore(script, node);
```

然而还是失败了，一直报错，提示找不到opencv。经过调试发现root上的cv为空，说明没有cv对象没有赋值到root上。然后我看了Opencv.js的源码，发现如下代码：

```javascript
if (typeof define === 'function' && define.amd) {
    // AMD. Register as an anonymous module.
    define(function () {
      return (root.cv = factory());
    });
  } else if (typeof module === 'object' && module.exports) {
    // Node. Does not work with strict CommonJS, but
    // only CommonJS-like environments that support module.exports,
    // like Node.
    module.exports = factory();
  } else {
    // Browser globals
    root.cv = factory();
  }
```

根据代码我想应该是上面的判断出了问题，于是在上面的每个判断条件下加了log,果然不出所料，这里的代码使用了module.exports = factory()方式，仔细思考一下，其实也没有问题，本来Electron就是基于Node环境的，所以opencv.js认为现在在nodejs环境下，需要使用CommonJS的格式export。所以只要让他不进入这个else if就可以了，所以我果断手动创建了一个bug.把判断条件改成了：

```obj
else if (typeof module === 'objectBUG' && module.exports)
```

以毒攻读，以创建Bug的方式解决这个"Bug"。
幸运的是，这次成功了！终于看到终端上把Opencv信息打印出来。

#### 还有一些小问题

官方教程使用的版本是Opencv.js 3.4,在集成成功后，发现视频并没有像官方教程那样改变成灰色图输出。这让我很疑惑，因为同样的版本在纯Web环境下是正常的。
我抱着尝试的心态，使用了Opencv4.0版本，结果令人意外的是这个版本竟然可以正常。所以可能是旧版本的一个bug,新版中修复了。如果你也想在Electron-vue中使用Opencv.js的话，请记得一定使用最新版本。

#### 视频处理Demo

Todo

#### 关于我

我是jacky，一名iOS开发者，现在致力于成为全栈开发工程师，主要涉略有iOS、Android、Web、Vue.js、Swift、PHP、Python、Opencv、TensorFlow。欢迎大家骚扰交流~~    
