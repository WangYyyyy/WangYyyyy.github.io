---
layout: post
title:  "Flutter与原生(Android, iOS)通讯之BasicMessageChannel"
date:   2019-10-21 14:22:32 +0800
categories: Flutter
tags:
  - Dart
  - Flutter
  - iOS
  - Android
---
BasicMessageChannel实现功能是flutter与原生之间互相**发送数据**, 然后再各个平台对数据进行处理.

### Flutter声明channel
声明一个名字为`Test_BasicMessageChannel`的channel
其中`StandardMessageCodec`为消息的类型, 还有`BinaryCodec`, `StringCodec`, `JSONMessageCodec`, `JSONMethodCodec`, `StandardMessageCodec`, `StandardMethodCodec`, 具体的还请自行查文档, 但只看名字已经大概看得出消息传递类型的使用场景.
```dart
//核心代码, 声明channel
final messageChannel = BasicMessageChannel("Test_BasicMessageChannel", StandardMessageCodec());
//发送信息
channel.send("Flutter 发送的信息");
//BasicMessageChannel 回调函数
messageChannel.setMessageHandler((dynamic value) {
  print(value);
  return null;
});

```
Standard类型应该可以兼容大多数场景, 具体跟原生的类型对应如下
```dart
/// On Android, messages are represented as follows:
///
///  * null: null
///  * [bool]\: `java.lang.Boolean`
///  * [int]\: `java.lang.Integer` for values that are representable using 32-bit
///    two's complement; `java.lang.Long` otherwise
///  * [double]\: `java.lang.Double`
///  * [String]\: `java.lang.String`
///  * [Uint8List]\: `byte[]`
///  * [Int32List]\: `int[]`
///  * [Int64List]\: `long[]`
///  * [Float64List]\: `double[]`
///  * [List]\: `java.util.ArrayList`
///  * [Map]\: `java.util.HashMap`
///
/// On iOS, messages are represented as follows:
///
///  * null: nil
///  * [bool]\: `NSNumber numberWithBool:`
///  * [int]\: `NSNumber numberWithInt:` for values that are representable using
///    32-bit two's complement; `NSNumber numberWithLong:` otherwise
///  * [double]\: `NSNumber numberWithDouble:`
///  * [String]\: `NSString`
///  * [Uint8List], [Int32List], [Int64List], [Float64List]\:
///    `FlutterStandardTypedData`
///  * [List]\: `NSArray`
///  * [Map]\: `NSDictionary`
```
### Android核心代码
```java
 BasicMessageChannel messageChannel = new BasicMessageChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), "Test_BasicMessageChannel", StandardMessageCodec.INSTANCE);

 messageChannel.setMessageHandler(new BasicMessageChannel.MessageHandler() {
  //接收信息的回调
  @Override
  public void onMessage(Object message, @NotNull BasicMessageChannel.Reply reply) {
      if (message.equals("callback")) {
        //给Flutter发送信息
        messageChannel.send("来自Android  BasicMessageChannel回调信息");
      }else {
          System.out.println("来自Flutter BasicMessageChannel的消息");
          System.out.println(message);
      }
  }
});
 ```
### iOS核心代码
```swift
let root = self.window.rootViewController as! FlutterViewController
let messageChannel = FlutterBasicMessageChannel.init(name: "Test_BasicMessageChannel", binaryMessenger:root.binaryMessenger , codec: FlutterStandardMessageCodec.sharedInstance())
//接收信息的回调
messageChannel.setMessageHandler { (message, reply) in
    if message as? String == "callback" {
        //发送信息
        messageChannel.sendMessage("来自iOS  BasicMessageChannel回调信息")
    }else {
        NSLog("来自Flutter BasicMessageChannel的消息\n%@", (String(describing: message!)))
    }
}
```

最后为附上代码[GitHub](https://github.com/WangYyyyy/flutter_native_communication)
