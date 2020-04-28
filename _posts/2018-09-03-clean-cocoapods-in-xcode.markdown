---
layout: post
title:  "清除/删除,项目中的cocoapods"
date:   2018-09-03 16:46:34 +0800
categories: iOS
tags:
  - iOS
  - pod
  - CocoaPods
  - Ruby
  - gem
---
本文基于已经安装过pods环境, 如果没有安装请移步[CocoaPods安装](http://blog.wyyy.me/ios/2017/06/25/install-cocoapods.html)先进行安装!

我们在遇到一些老项目或者项目依赖混乱时, 直接彻底移除pod从头再来不妨是一个选择.

在项目根目录, 就是有podfile的目录, 安装组件
```shell
$ sudo gem install cocoapods-deintegrate cocoapods-clean
```
执行命令

```shell
$ pod deintegrate
$ pod clean
```
pod会彻底清除干净, 然后重新整理依赖`pod install`重新开始吧!
