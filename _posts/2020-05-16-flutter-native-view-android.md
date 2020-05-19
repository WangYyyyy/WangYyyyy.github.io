---
layout: post
title:  "Flutter调用原生view-Android篇"
date:   2020-05-16 15:22:32 +0800
categories: Flutter
tags:
  - Flutter
  - Android
---
本文介绍Flutter直接取原生的view然后在Flutter页面中展示.使用的Flutter版本是`1.17.0`.
### 核心概念
调用Android原生页面, Android的类需要实现`io.flutter.plugin.platform.PlatformView`接口, 然后重写`public View getView()`函数来返回目标view, Flutter使用`AndroidView`来创建使用.
### 具体实现

**STEP 1**

定义`NormalView`类实现`PlatformView`接口, 代码中我们将返回一个TextView, Flutter会传递参数过来, 并且展示出来.

```java
public class NormalView implements PlatformView {

    private TextView textView;

    @SuppressLint("SetTextI18n")
    NormalView(Context context, Object args) {
        Map map = (Map) args;
        this.textView = new TextView(context);
        this.textView.setText("Android原生View\n" + map.get("value"));
        this.textView.setGravity(Gravity.CENTER);
        this.textView.setTextColor(0xFFFFFFFF);
    }

    @Override
    public View getView() {
        return textView;
    }

    @Override
    public void dispose() {

    }
}
```
**STEP 2**

新建`NormalViewFactory`类, 继承`io.flutter.plugin.platform.PlatformViewFactory`类, 用来生构建我们想要的view
```java
public class NormalViewFactory extends PlatformViewFactory {

    public NormalViewFactory() {
        super(StandardMessageCodec.INSTANCE);
    }

    @Override
    public PlatformView create(Context context, int viewId, Object args) {
        return new NormalView(context, args);
    }
}
```
**STEP 3**

新建`NativeViewPlugin`类实现`io.flutter.embedding.engine.plugins.FlutterPlugin`接口, 用来注册`Factory`.
```java
public class NativeViewPlugin implements FlutterPlugin {

    @Override
    public void onAttachedToEngine(@NonNull FlutterPluginBinding binding) {
        binding.getPlatformViewRegistry().registerViewFactory("normalView", new NormalViewFactory());
        binding.getPlatformViewRegistry().registerViewFactory("advancedView", new AdvancedViewFactory(binding));
    }

    @Override
    public void onDetachedFromEngine(@NonNull FlutterPluginBinding binding) {

    }
}
```
字符串`normalView`比较关键, Flutter中调用需要的KEY值

**STEP 4**

在`MainActivity`中注册`NativeViewPlugin`
```kotlin
class MainActivity: FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        flutterEngine.plugins.add(NativeViewPlugin())
    }
}
```
**STEP 5**

Flutter中创建并使用刚刚我们写好的view
```dart
final androidView = AndroidView(
        viewType: 'normalView',
        creationParams: {'value': 'Flutter传给Android的参数'},
        creationParamsCodec: StandardMessageCodec(),
      );
```
最后我们直接用`androidView`放在我们需要展示的地方就好了.

### 高级用法
如果想在原生页面中跳转或者做一些其它操作, 直接利用`NormalViewFactory`中的`context`进行操作, 这里不多赘述, 具体实现在demo中会有展现.

附上demo项目地址[GitHub](https://github.com/WangYyyyy/flutter_native_views)