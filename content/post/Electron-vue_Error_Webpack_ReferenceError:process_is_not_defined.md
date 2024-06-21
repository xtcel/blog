---
title:       "Electron-vue报错Webpack ReferenceError:process is not defined"
subtitle:    ""
description: ""
date:        2019-07-04
author:      ""
image:       ""
tags:        ["Electron", "Electron-vue", "报错 process is not defined"]
categories:  ["Tech" ]
---

#### 报错 Webpack ReferenceError: process is not defined

最新重新安装electron-vue后，使用yarn安装项目，结果一直报错。起初以为是yarn的问题，使用npm安装也发现同样的问题。Google后发现应该是node版本问题，将node降为Node11可以正常工作。

```shell
ERROR in Template execution failed: ReferenceError: process is not defined

  ERROR in   ReferenceError: process is not defined

    - index.ejs:11 eval
      [.]/[html-webpack-plugin]/lib/loader.js!./src/index.ejs:11:2

    - index.ejs:16 module.exports
      [.]/[html-webpack-plugin]/lib/loader.js!./src/index.ejs:16:3

    - index.js:284 
      [electron-test]/[html-webpack-plugin]/index.js:284:18

    - runMicrotasks

    - task_queues.js:93 processTicksAndRejections
      internal/process/task_queues.js:93:5
```

#### 解决方案

修改.electron-vue/webpack.web.config.js和.electron-vue/webpack.renderer.config.js文件的HtmlWebpackPlugin，添加templateParameters，修改后如下：

```javascript
new HtmlWebpackPlugin({
      filename: 'index.html',
      template: path.resolve(__dirname, '../src/index.ejs'),
      templateParameters(compilation, assets, options) {
        return {
          compilation: compilation,
          webpack: compilation.getStats().toJson(),
          webpackConfig: compilation.options,
          htmlWebpackPlugin: {
            files: assets,
            options: options
          },
          process,
        };
      },
      minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        removeComments: true
      },
      nodeModules: false
    }),
```

#### 参考

https://github.com/SimulatedGREG/electron-vue/issues/871
