---
layout: post
title:  "Mac终端下查看温度和风扇状态的软件"
date:   2019-08-08 10:08:36 +0800
categories: 实用工具
tags:
  - Mac
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

### 安装!
```shell
$ sudo gem install iStats
```
等待安装完成后, 输入
```shell
$ istats
```
可查看信息, 很方便的!
```
--- CPU Stats ---
CPU temp:               54.75°C     ▁▂▃▅▆▇

--- Fan Stats ---
Total fans in system:   2           
Fan 0 speed:            2669 RPM    ▁▂▃▅▆▇
Fan 1 speed:            2468 RPM    ▁▂▃▅▆▇

--- Battery Stats ---
Battery health:         unknown     
Cycle count:            2           ▁▂▃▅▆▇  0.2%
Max cycles:             1000        
Current charge:         8499 mAh    ▁▂▃▅▆▇  100%
Maximum charge:         8499 mAh    ▁▂▃▅▆▇  97.1%
Design capacity:        8755 mAh    
Battery temp:           31.89°C    
```