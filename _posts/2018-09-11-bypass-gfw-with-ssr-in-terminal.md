---
layout: post
title:  "Mac系统Terminal通过shadowsocks科学上网"
date:   2018-09-11 16:11:02 +0800
categories: 科学上网
tags:
  - shadowsocks
  - SSR
  - Mac
  - Terminal
---
### 先决条件
确保系统中SS或者SSR正常工作, 设置其Local Socks5监听端口号(这里为1086).
>Mac版ShadowsocksX-NG中, 设置步骤为 Advance Preferences -> Local Socks5 Listen Port.

### 简单方法
>一次性有效, 不影响系统整体

直接在终端运行命令
```shell
#1086为设置的端口号
export all_proxy=socks5://127.0.0.1:1086
```
设置之后在当前终端运行 `wget` `curl` 这类网络命令都会经过ss代理.
如果关掉当前窗口或者输入以下命令, 代理将失效
```shell
unproxy='unset all_proxy'
```
### 验证代理
在终端输入
```shell
~ curl cip.cc

IP	: 124.133.27.130
地址	: 中国  山东  济南
运营商	: 联通

数据二	: 山东省济南市 | 联通

数据三	: 中国山东济南 | 联通

URL	: http://www.cip.cc/124.133.27.130
```
```shell
~ export all_proxy=socks5://127.0.0.1:1086
~ curl cip.cc

IP	: 154.17.12.109
地址	: 美国  加利福尼亚州  圣卡洛斯
运营商	: isc.org

数据二	: 美国

数据三	: 美国加利福尼亚 | 科进

URL	: http://www.cip.cc/154.17.12.109
```
查看当前ip和地区, 来判断是否在使用代理生效, 如果当前代理服务器不可用, 命令会执行失败!
### 可复用的方法
这里以`zsh`为例, 编辑配置文件
```shell
# open ~/.zshrc亦可
~ vim ~/.zshrc
```
在里面添加上面的代理配置
```shell
# proxy list
alias proxy='export all_proxy=socks5://127.0.0.1:1086'
alias unproxy='unset all_proxy'
```
`:wq`保存退出
```shell
~ source ~/.zshrc
```
这样就可以使用 `proxy` 和 `unproxy` 来给当前终端设置或移除代理.
**具体使用**
直接在命令行输入对应的命令
```shell
#设置代理
~ proxy
#移除代理
~ unproxy
```