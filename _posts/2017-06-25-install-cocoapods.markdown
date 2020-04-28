---
layout: post
title:  "CocoaPods安装"
date:   2017-06-25 10:24:42 +0800
categories: iOS
tags:
  - iOS
  - pod
  - CocoaPods
  - Ruby
  - gem
---
### 安装Xcode或者Xcode Command Line Tool
安装并运行过Xcode可以直接跳过这步

否则, 请在terminal输入以下命令进行安装
```shell
$ xcode-select --install
```
### 检查Ruby环境
```shell
$ ruby -v
```
一般Mac都会自带Ruby环境的, 但如果没有Ruby环境, 还请自行搜索Mac安装Ruby!
>本文基于你的Mac已经安装了Ruby（自带Gem）环境。
### 安装CocoaPods
```shell
#更新gem
$ sudo gem update --system
```
如果网络不通, 还请自行替换Ruby源或者VPN!
```shell
#安装
$ sudo gem install cocoapods
$ pod setup
```
安装可能需要一点时间, 一切正常完成后验证pod环境
```shell
$ pod env
```
安装完毕!