---
layout: post
title:  "解决Android Studio Gradle同步时Could not resolve的问题"
date:   2019-11-08 10:22:32 +0800
categories: Flutter
tags:
  - Android Studio
  - Flutter
  - Gradle
  - Android
---
更新了flutter后, Android Studio的Gradle需要同步新的依赖, 但是提示失败, 其实这是墙的问题.
```
> Could not resolve io.flutter:flutter_embedding_debug:1.0.0-e7f9ef6aa0b9040102d1b3c9a6ae934df746ef94.
> Could not resolve io.flutter:arm64_v8a_debug:1.0.0-e7f9ef6aa0b9040102d1b3c9a6ae934df746ef94.
```

### 解决办法
Mac系统为例, 为gradle设置代理, 修改`gradle.properties`文件配置, 作者为shadowsocks, 则如下配置
```shell
$ vim .gradle/gradle.properties
```
加入以下内容
```
org.gradle.jvmargs=-DsocksProxyHost=127.0.0.1 -DsocksProxyPort=1086
```
**注意!!!**
如果你在Android Studio手动设置了代理, 同步gradle时会自动给`gradle.properties`文件添加一些代理配置, 但默认的那些配置并不会起作用, 是错误的配置, 而且当你删除Android Studio配置时, 并不会删除`gradle.properties`文件中的配置, 一定要check一下内容类似如下内容, 统统删掉! 使用上面的内容
```
#systemProp.socks.proxyHost=127.0.0.1
#systemProp.socks.proxyPort=1086

#systemProp.https.proxyHost=127.0.0.1
#systemProp.https.proxyPort=1086

#systemProp.https.proxyHost=socks5://127.0.0.1
#systemProp.https.proxyPort=1086
```
最后直接`esc` `:wq` 保存退出再回到Android Studio进行同步就行了.
