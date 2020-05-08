---
layout: post
title:  "Flutter调用原生swift中print/NSLog不打印的问题解决"
date:   2020-05-08 09:22:32 +0800
categories: Flutter
tags:
  - Swift
  - Flutter
  - iOS
---
之前也有一个bug是在iOS 13环境下, iOS原生打印失效的问题, 现在更新到`Flutter版本: 1.17.0`在OC中使用NSLog可以正常打印日志了, 但是在Swift中打印日志不能在Flutter控制台打印的情况依旧存在, 这里记录一下临时的解决方案.
### 解决办法
`print`函数目前没有发现解决办法
可以在Swift代码中是用`NSLog`函数, 但使用此函数在Flutter控制台第一行的信息不打印, 我们只需要在需要的内容前增加`\n`
``` swift
NSLog("Before\nAfter")
```
这样会打印出`After`.
``` swift
NSLog("\nHello, World!")
```
这样会完整打印出`Hello, World!`

现在GitHub上这个issue还是open状态, 官方依然没有解决, 记录一下临时先这么用着吧! 

附上issue地址[GitHub issue](https://github.com/flutter/flutter/issues/13204)