---
title:       "解决Mac新文字漏洞软件崩溃问题"
subtitle:    ""
description: ""
date:        2015-07-31
author:      ""
image:       ""
tags:        ["Mac", "QQ", "崩溃", "漏洞"]
categories:  ["Tech" ]
---

#### 恶作剧

早上同事用QQ发了一段文字给我，内容为如下： 玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉王玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉玉 当我点开这条QQ消息的时候，QQ瞬间就崩溃了！我还不知道怎么回事，重新打开了QQ查看刚才发过来的这条消息，QQ再次崩溃。怎么QQ崩溃啦我整个人都不好了，233…… 我上网查了一下，发现很多人都遇到了类似问题，原来这是一个新的Mac文字漏洞。

#### 探索

以下是有人在知乎上提的问题：

从浏览次数和关注人数来看，说明很多人都被调戏了！QQ的崩溃信息如下：

```shell
Process: QQ [1588] Path: /Applications/QQ.app/Contents/MacOS/QQ Identifier: com.tencent.qq Version: 4.0.2 (18031) Code Type: X86 (Native) Parent Process: ??? [1][1] Responsible: QQ [1588]

Date/Time: 2015-07-20 16:53:34.393 +0800 OS Version: Mac OS X 10.10.4 (14E46) Report Version: 11

Time Awake Since Boot: 6300 seconds

Crashed Thread: 2 Dispatch queue: NSTextCheckingOperationQueue :: NSOperation 0x7cab7510 (QOS: USER_INITIATED)

Exception Type: EXC_BREAKPOINT (SIGTRAP) Exception Codes: 0x0000000000000002, 0x0000000000000000

Application Specific Information: *** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'condition "must be there"'

Application Specific Backtrace 1: 0 CoreFoundation 0x917b3c63 \_\_raiseError + 195 1 libobjc.A.dylib 0x95c7aa2a objc_exception_throw + 276 2 CoreFoundation 0x917b3b7d +[NSException raise:format:] + 141 3 DataDetectorsCore 0x9b8761e2 DDRaiseException + 67 4 DataDetectorsCore 0x9b8914d0 DDCrashv + 145 5 DataDetectorsCore 0x9b891503 DDCrash + 27 6 DataDetectorsCore 0x9b84c170 DDScannerScanQuery + 729 7 DataDetectorsCore 0x9b84ba6a DDScannerScanStringWithRange + 319 8 AppKit 0x93fb1f41 checkDataDetectors + 161 9 AppKit 0x93faf40a NSSpellCheckerCheckString + 16505 10 AppKit 0x93fab336 -[NSTextCheckingOperation main] + 162 11 Foundation 0x9b2b937e -[\_\_NSOperationInternal \_start:] + 770 12 Foundation 0x9b2b9071 -[NSOperation start] + 71 13 Foundation 0x9b2b8d8b \__NSOQSchedule_f + 213 14 libdispatch.dylib 0x95e61130 \_dispatch\_client_callout + 50 15 libdispatch.dylib 0x95e64d05 \_dispatch\_queue_drain + 1017 16 libdispatch.dylib 0x95e6699d \_dispatch\_queue_invoke + 186 17 libdispatch.dylib 0x95e63f89 \_dispatch\_root_queue_drain + 395 18 libdispatch.dylib 0x95e7363d \_dispatch\_worker_thread3 + 97 19 libsystem_pthread.dylib 0x9794c1da \_pthread\_wqthread + 724 20 libsystem_pthread.dylib 0x97949e2e start_wqthread + 30

Thread 2 Crashed:: Dispatch queue: NSTextCheckingOperationQueue :: NSOperation 0x7cab7510 (QOS: USER_INITIATED) 0 com.apple.CoreFoundation 0x917b45f7 ***TERMINATING_DUE_TO_UNCAUGHT_EXCEPTION*** + 7 1 com.apple.CoreFoundation 0x917b3f79 \_\_raiseError + 985 2 libobjc.A.dylib 0x95c7aa2a objc_exception_throw + 276 3 com.apple.CoreFoundation 0x917b3b7d +[NSException raise:format:] + 141 4 com.apple.datadetectorscore 0x9b8761e2 DDRaiseException + 67 5 com.apple.datadetectorscore 0x9b8914d0 DDCrashv + 145 6 com.apple.datadetectorscore 0x9b891503 DDCrash + 27 7 com.apple.datadetectorscore 0x9b84c170 DDScannerScanQuery + 729 8 com.apple.datadetectorscore 0x9b84ba6a DDScannerScanStringWithRange + 319 9 com.apple.AppKit 0x93fb1f41 checkDataDetectors + 161 10 com.apple.AppKit 0x93faf40a NSSpellCheckerCheckString + 16505 11 com.apple.AppKit 0x93fab336 -[NSTextCheckingOperation main] + 162 12 com.apple.Foundation 0x9b2b937e -[\_\_NSOperationInternal \_start:] + 770 13 com.apple.Foundation 0x9b2b9071 -[NSOperation start] + 71 14 com.apple.Foundation 0x9b2b8d8b \__NSOQSchedule_f + 213 15 libdispatch.dylib 0x95e61130 \_dispatch\_client_callout + 50 16 libdispatch.dylib 0x95e64d05 \_dispatch\_queue_drain + 1017 17 libdispatch.dylib 0x95e6699d \_dispatch\_queue_invoke + 186 18 libdispatch.dylib 0x95e63f89 \_dispatch\_root_queue_drain + 395 19 libdispatch.dylib 0x95e7363d \_dispatch\_worker_thread3 + 97 20 libsystem_pthread.dylib 0x9794c1da \_pthread\_wqthread + 724 21 libsystem_pthread.dylib 0x97949e2e start_wqthread + 30 \``\`

```

#### 分析

从崩溃信息可以看到是在NSTextCheckingOperationQueue线程中崩溃的，这是一个执行文本检查的线程。 其实之前苹果就出过几次类似问题，比如”Fill:///“崩溃问题。都是DataDectorsCore.framework这个framework的问题， 这个库的主要作用是自带的拼写检测和特殊格式的信息（比如URL、电话号码、时间等）匹配都会触发 DataDectorsCore.framework。 我查找了”Fill:///“崩溃问题的解决方案，于是也尝试了同样的方案解决这个问题，果然不出所料的不再崩溃了！

#### Debug

**解决崩溃问题的方法就是：** 在终端运行以下两条命令：

```shell
sudo mv -v /System/Library/PrivateFrameworks/DataDetectorsCore.framework/Versions/A/Resources/com.apple.datadetectorscore.cache.urlifier.system /System/Library/PrivateFrameworks/DataDetectorsCore.framework/Versions/A/Resources/com.apple.datadetectorscore.cache.urlifier.system.backup

sudo mv -v /System/Library/PrivateFrameworks/DataDetectorsCore.framework/Versions/A/Resources/com.apple.datadetectorscore.cache.full.asia.system /System/Library/PrivateFrameworks/DataDetectorsCore.framework/Versions/A/Resources/com.apple.datadetectorscore.cache.full.asia.system.backup

```
