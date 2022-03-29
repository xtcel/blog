---
title:       "Flutter Text底部黄色双划线"
subtitle:    ""
description: ""
date:        2019-12-11
author:      "xtcel"
image:       ""
tags:        ["Flutter", "Flutter Text"]
categories:  ["Tech" ]
---

### 问题

文字下面出现黄色双划线
没有使用Scaffold脚手架组件，Scaffold是一个Material风格APP的脚手架（包含AppBar、Body、ActionButton），Text不是在Material组件下，但是有些时候我们并不想使用Scaffold嵌套在最外层，比如一个Dialog或者其他的。
只需要将Text放在Material为根的组件中，Text就会继承Material的主题，否则Text会使用默认TextDecoration，默认的TextDecoration会有黄色双下划线。

### 解决方案

所以我们有几种解决方法：
1.使用Scaffold脚手架组件

```dart
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: const Text('Sample Code'),
    ),
    body: Center(
      child: Text('You have pressed the button $_count times.')
    ),
    backgroundColor: Colors.blueGrey.shade200,
    floatingActionButton: FloatingActionButton(
      onPressed: () => setState(() => _count++),
      tooltip: 'Increment Counter',
      child: const Icon(Icons.add),
    ),
  );
}
```

2.修改根组件为Material组件

```dart
Widget build(BuildContext context) {
    return Material(
        type: MaterialType.transparency,
        child: Container()
    );
}
```

3.不使用默认TextDecoration

```dart
Text("Text Content",
  style: TextStyle(
    decoration: TextDecoration.none,
  )
);
```
