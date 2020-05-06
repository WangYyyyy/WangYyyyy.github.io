---
layout: post
title:  "Flutter与原生(Android, iOS)通讯之MethodChannel"
date:   2019-10-21 14:22:32 +0800
categories: Flutter
tags:
  - Dart
  - Flutter
  - iOS
  - Android
---
MethodChannel实现功能是flutter与原生之间互相调用, 可以指定名称和参数做对应处理.

### Flutter声明channel
声明一个名字为`Test_MethodChannel`的channel
```dart
//核心代码, 声明channel
final methodChannel = MethodChannel("Test_MethodChannel");
//发送信息(名称和参数)
final res = await methodChannel.invokeMethod("args", {"p1": "1", "p2": 2});
//发送信息(无参)
methodChannel.invokeMethod("name"); 
//MethodChannel 回调函数
//如果native端进行调用, F端同样也可以回调处理, 具体看各个平台的代码
Future<dynamic> platformCallHandler(MethodCall call) async {
  if (call.method == 'adcallback') {
    print(call.arguments);
  }
}
```
### Android核心代码
```java
//注册
MethodChannel methodChannel = new MethodChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), "Test_MethodChannel");

//Android端调用
methodChannel.invokeMethod("adcallback", "来自Android  MethodChannel回调信息");

//接收回调处理
methodChannel.setMethodCallHandler(new MethodChannel.MethodCallHandler() {
  @Override
  public void onMethodCall(MethodCall call, MethodChannel.Result result) {
    if (call.method.equals("args")) {
      System.out.println("来自Flutter MethodChannel的参数");
      System.out.println(call.arguments);
      result.success("来自Android的返回信息");
    }else if (call.method.equals("error")) {
      result.error("500", "来自Android的错误信息", "details");
    }else if (call.method.equals("notImplemented")) {
      result.notImplemented();
    }
  }
});
 ```
### iOS核心代码
```swift

let root = self.window.rootViewController as! FlutterViewController
//注册
let methodChannel = FlutterMethodChannel.init(name: "Test_MethodChannel", binaryMessenger: root.binaryMessenger)
//发送消息
methodChannel.invokeMethod("adcallback", arguments: "来自iOS  MethodChannel回调信息")
//接收信息的回调
methodChannel.setMethodCallHandler { (call, result) in
  if call.method == "args" {
    NSLog("来自Flutter MethodChannel的参数")
    NSLog("\(call.arguments ?? "空参数")")
    result("来自iOS的返回信息")
  }else if call.method == "error" {
    result(FlutterError.init(code: "500", message: "iOS的错误信息", details: "details"))
  }else if call.method == "notImplemented" {
    result(FlutterMethodNotImplemented)
  }
}
```

最后为附上代码[GitHub](https://github.com/WangYyyyy/flutter_native_communication)
