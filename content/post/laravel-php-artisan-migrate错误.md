---
title:       "Laravel php artisan migrate错误"
subtitle:    ""
description: ""
date:        2019-08-15 
author:      "xtcel"
image:       ""
tags:        ["Laravel", "migrate错误"]
categories:  ["Tech" ]
---

### 错误

运行php artisan migrate出现如下错误：

```
In Connection.php line 664:

  SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 767 bytes (SQL: alter table  
   `admin_users` add unique `admin_users_email_unique`(`email`))                                                                     


In Connection.php line 458:

  SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 767 bytes  
```

因为laravel默认使用utf8mb4字符集，utf8mb4是每一字符用4位表示，可以很好的支持emoji表情存存入数据库的问题
每个字符4为那么，总容量就少些，utf8最大是255，255*3 = 765；765/4 = 191.25, 所以utf8mb4最大支持191个字符，如果字段长度超过191，那么就会出现上述错误提示。

#### 有两种方法可以解决

##### 方法一：修改默认字符字段长度(官方解决方案)

```
//edit your AppServiceProvider.php file contains in providers folder
use Illuminate\Support\Facades\Schema;

public function boot()
{
    Schema::defaultStringLength(191);
}
```

此方法必须保证每个string字段长度小于等于191，如果大于191是一样会报错的，作者就是因为修改上面的代码后，没有修改字段长度，一直不能正常migrate

##### 方法二：修改为utf8字符集

```
Go to /config/database.php and find these lines

'mysql' => [
   ...,
   'charset' => 'utf8mb4',
   'collation' => 'utf8mb4_unicode_ci',
   ...,
   'engine' => null,
]
and change them to:

'mysql' => [
   ...,
   'charset' => 'utf8',
   'collation' => 'utf8_unicode_ci',
   ...,
   'engine' => 'InnoDB',
]
```

在config/database.php中，将charset和collation分别改为'utf8' 和 'utf8_unicode_ci'
因为utf8最大支持255，所以在191 - 255之间都是可以的。
