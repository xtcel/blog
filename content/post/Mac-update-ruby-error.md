---
title:       "Mac更新Ruby报错"
subtitle:    "Mac update ruby error"
description: ""
date:        2017-12-06
author:      ""
image:       ""
tags:        ["Mac", "Ruby"]
categories:  ["Tech" ]
---

Mac更新Ruby报错

```shell
sudo gem update --system  
ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted - /usr/bin/update_rubygems
```

解决方法：

```bash
sudo gem update -n /usr/local/bin --system
```
