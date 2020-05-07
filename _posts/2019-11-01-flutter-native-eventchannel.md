---
layout: post
title:  "Flutter与原生(Android, iOS)通讯之EventChannel"
date:   2019-11-01 10:22:32 +0800
categories: Flutter
tags:
  - Dart
  - Flutter
  - iOS
  - Android
---
EventChannel实现功能是flutter与接收navtive端发送的通知, F端用`Stream`来订阅监听, 其原理是对`MethodChannel`封装, 关于`MethodChannel`可以[点击这里](http://blog.wyyy.me/flutter/2019/10/22/flutter-native-methodchannel.html)查看

### Flutter声明channel
声明一个名字为`Test_MethodChannel`的channel
```dart
//核心代码, 声明channel
final eventChannel = EventChannel("Test_EventChannel");
//EventChannel 回调
void _onEvent(Object event) {
  // 正常数据流
  print(event);
}
void _onError(Object error) {
  // 失败数据流
  print(error);
}
//EventChannel 订阅的对象
StreamSubscription<dynamic> streamSubscription = eventChannel.receiveBroadcastStream().listen(_onEvent, onError: _onError);

//取消 EventChannel 订阅
//调用此方法会触发native端的cancel回调, 具体看下文双端的代码
streamSubscription.cancel();
```
### Android核心代码
```java
//注册
EventChannel eventChannel = new EventChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), "Test_EventChannel");
//添加监听
//让目标class实现`EventChannel.StreamHandler`
//这里是当前class
eventChannel.setStreamHandler(this);

//重写核心方法
@Override
public void onListen(Object arguments, EventChannel.EventSink events) {
  //发送消息
  events.success("来自Android的event消息"));
  //发送错误信息
  events.error("code", "来自Android的error event消息", null);
}

@Override
public void onCancel(Object arguments) {
  System.out.println("Flutter 取消了 event的订阅!");
}

 ```
### iOS核心代码
```swift
//注册
let eventChannel = FlutterEventChannel.init(name: "Test_EventChannel", binaryMessenger: root.binaryMessenger)
//添加监听
//让目标class实现`FlutterStreamHandler`协议
//这里是当前class
eventChannel.setStreamHandler(self)

//实现协议
//MARK: FlutterStreamHandler
func onListen(withArguments arguments: Any?, eventSink events: @escaping FlutterEventSink) -> FlutterError? {
    events("来自iOS的error event消息")
    return nil;
}

func onCancel(withArguments arguments: Any?) -> FlutterError? {
    NSLog("Flutter 取消了 event的订阅!")
    return nil;
}
```

最后为附上代码[GitHub](https://github.com/WangYyyyy/flutter_native_communication)
