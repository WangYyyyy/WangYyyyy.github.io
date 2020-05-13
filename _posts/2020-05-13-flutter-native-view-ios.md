---
layout: post
title:  "Flutter调用原生view-iOS篇"
date:   2020-05-13 15:22:32 +0800
categories: Flutter
tags:
  - Flutter
  - iOS
---
本文介绍Flutter直接取原生的view然后在Flutter页面中展示.使用的Flutter版本是`1.17.0`.
### 核心概念
调用iOS原生页面, iOS的类需要实现`FlutterPlatformView`协议, 然后实现`- (UIView *)view`函数来返回目标view, Flutter使用`UIKitView`来创建使用.
### 具体实现

**STEP 1**

定义`NormalView`类实现`FlutterPlatformView`协议, 代码中我们将返回一个label, Flutter会传递参数过来, 并且展示出来.

```
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

NS_ASSUME_NONNULL_BEGIN

@interface NormalView : NSObject<FlutterPlatformView>

- (instancetype)initWithParamsFlutter:(NSDictionary *)params;

@property(nonatomic, strong)UILabel *normalLabel;

@end

NS_ASSUME_NONNULL_END
```

```
#import "NormalView.h"

@implementation NormalView

- (instancetype)initWithParamsFlutter:(NSDictionary *)params
{
    self = [super init];
    if (self) {
        _normalLabel = [UILabel new];
        _normalLabel.text = [NSString stringWithFormat:@"iOS原生View\n%@", params[@"value"]];
        _normalLabel.numberOfLines = 2;
        _normalLabel.textColor = UIColor.whiteColor;
        _normalLabel.textAlignment = NSTextAlignmentCenter;
    }
    return self;
}

- (UIView*)view {
    return _normalLabel;
}

@end
```
**STEP 2**

新建`NormalViewFactory`类, 实现`FlutterPlatformViewFactory`协议, 用来生构建我们想要的view
```
#import <Foundation/Foundation.h>
#import <Flutter/Flutter.h>

NS_ASSUME_NONNULL_BEGIN

@interface NormalViewFactory : NSObject<FlutterPlatformViewFactory>

@end

NS_ASSUME_NONNULL_END
```
```
#import "NormalViewFactory.h"
#import "NormalView.h"

@implementation NormalViewFactory

- (NSObject<FlutterPlatformView>*)createWithFrame:(CGRect)frame
                                   viewIdentifier:(int64_t)viewId
                                        arguments:(id _Nullable)args {
    return [[NormalView alloc] initWithParamsFlutter:args];
}

//如果要实现从Flutter传值, 需要实现此方法, 否则不用
- (NSObject<FlutterMessageCodec>*)createArgsCodec {
    return [FlutterStandardMessageCodec sharedInstance];
}

@end
```
**STEP 3**

在`AppDelegate`中注册`plugin`及`NormalViewFactory`
```swift
let plugin = registrar(forPlugin: "NativeViewPlugin")
plugin.register(NormalViewFactory(), withId: "normalView")
```
字符串`normalView`比较关键, Flutter中调用需要的KEY值

**STEP 4**

Flutter中创建并使用刚刚我们写好的view
```dart
final uiKitView = UiKitView(
  viewType: 'normalView',//KEY
  creationParams: {'value': 'Flutter传给iOS的参数'},//传递的值
  creationParamsCodec: StandardMessageCodec(),
);
```
最后我们直接用`uiKitView`放在我们需要展示的地方就好了.

### 高级用法
如果想在原生页面中跳转或者做一些其它操作, 完全可以在原生页面中定义`UINavigationController`或者使用`present`直接跳转, 这里不多赘述, 具体实现在demo中会有展现.

附上demo项目地址[GitHub](https://github.com/WangYyyyy/flutter_native_views)