---
title:       "Electron菜单栏和导航栏隐藏"
subtitle:    ""
description: ""
date:        2019-08-06
author:      "xtcel"
image:       ""
tags:        ["Electron", "导航栏隐藏"]
categories:  ["Tech" ]
---

### 隐藏菜单栏

在main/index.js中修改ready事件回调

```javascript
app.on('ready', () => {
  createWindow()
  // 隐藏菜单栏
  const { Menu } = require('electron');
  Menu.setApplicationMenu(null);
  // hide menu for Mac 
  if (process.platform !== 'darwin') {
    app.dock.hide();
  }
})
```

### 隐藏导航栏

在初始化BrowserWindow的配置参数中，

windows添加

```javascript
frame: false
```

Mac添加

```javascript
titleBarStyle: 'hidden'
```

修改后如下：

```javascript
mainWindow = new BrowserWindow({
    height: 641,
    width: 1007,
    useContentSize: true,
    titleBarStyle: 'hidden',
    frame: false,
    webPreferences: {
      webSecurity: false
    }
  })
```

### 导航栏支持拖拽

使用自定义导航栏，需添加导航栏拖拽功能，否则无法移动窗口。
导航栏添加拖拽功能需添加如下CSS样式：

```javascript
-webkit-app-region: drag
```

如果导航栏内还有其他控件（如按钮等），需取消此控件的拖拽功能，否则控件点击事件等无效。

```javascript
-webkit-app-region: no-drag;
```
