---
layout: post
title:  "Dart/Flutter中object, json之间的互相转换"
date:   2019-10-16 10:08:36 +0800
categories: Flutter
tags:
  - Dart
  - Flutter
  - Json
---
### 首先, JsonStr与Map和List的互转
我们会用到`dart:convert`包, dart自带的json解析工具, 直接`import 'dart:convert';`使用即可
```dart
final list = ['123', '456', '789'];
//map/list 转 jsonstring
final jsonstr = json.encode(list);
//jsonstring 转 map/list
//这里转为了List对象
final jsonobj = json.decode(jsonstr);
```
### Json与Object转换 基本方法
现在我们已经成功把json数据通过`json.decode`转换为`Map`对象, 如果在一般场景中我们可以直接通过`Map<Key>`来取值使用.
当然今天我们的目标是将数据转为我们期望的实体对象, 现在我们需要新建一个Model类.
```dart
class TestModel {
  String key1;
  int key2;

  TestModel({this.key1, this.key2});
  
  TestModel.fromJson(Map<String, dynamic> json) {
    firstName = json['key1'];
    lastName = json['key2'];
  }
  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['key1'] = this.key1;
    data['key2'] = this.key2;
    return data;
  }
}
```
这个类中的`fromJson`和`toJson`需要依据我们的实际数据来定义, 然后在初始化的时候直接调用
```dart
//传入一个map来创建和赋值对象
final model = TestModel.fromJson(map);
//将对象转换为Map/List, 进行操作
final params = model.toJson();
```
想必看到这里会觉得如果大型项目手动一个个去创建对象及其from/toJson方法, 那将是一个灾难, 下面推荐两种可以解放我们双手的方法来应对这个问题.
## 自动创建可解析的Object
### 方法1 json_to_dart
[json_to_dart](https://javiercbk.github.io/json_to_dart/)

这个网站提供了通过json数据直接自动成功Object的功能
具体就是把json串复制到输入框中, 并且在下面填写你想要的类名, 点击`Generate Dart`按钮, 就会自动生成需要的代码.

输入以下代码, 这可以是接口返回的数据
```json
{
    "key1": "valuesk1",
    "key2": "valuesk2"
}
```
在类名一栏填写`TestModel`, 点击`Generate Dart`最终会生成以下代码
```dart
class TestModel {
  String key1;
  String key2;

  TestModel({this.key1, this.key2});

  TestModel.fromJson(Map<String, dynamic> json) {
    key1 = json['key1'];
    key2 = json['key2'];
  }

  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['key1'] = this.key1;
    data['key2'] = this.key2;
    return data;
  }
}
```
一键就完成了我们所需对象的生成!最后点击`Copy Dart code to clipboard`将代码复制到我们需要的地方就可以了!
>tips: 支持对象嵌套, 但暂时不支持`[]`里面的`[]`嵌套, 当然这在实际开发中影响较小.

>注意: 有些数据在Json中value为`null`的情况下, 生成对象属性类型也会为`Null`类型, 这里需要手动修改一下对应的实际类型. 

### 方法2 json_serializable
此方法为大佬们写的组件, 在项目已经有类定义的情况下, 自动生成对应类`from/toJson`方法.
首先在项目的`pubspec.yaml`文件`dependencies:`下增加依赖包
```yaml
dependencies:
  json_serializable: ^3.3.0
  json_annotation: ^3.0.1
  build_runner: ^1.9.0
```
具体的最新版请自行移步  ->[json_serializable](https://pub.dev/packages/json_serializable#-installing-tab-) ->[json_annotation](https://pub.dev/packages/json_annotation#-installing-tab-)->[build_runner](https://pub.dev/packages/build_runner#-installing-tab-) 查看
**pub**
```shell
$ pub get
```
**Flutter**
```shell
$ flutter pub get
```
现在我需要按照一定邀请创建`TestModel`类
```dart
import 'package:json_annotation/json_annotation.dart';

part 'testmodel.g.dart';

@JsonSerializable()
class TestModel {
  String key1;
  String key2;

  TestModel({this.key1, this.key2});
  //重要代码  名称必须为_$<类名>FromJson 和 _$<类名>ToJson
  factory TestModel.fromJson(Map<String, dynamic> json) => _$TestModelFromJson(json);
  Map<String, dynamic> toJson() => _$TestModelToJson(this);
}
```
此时`part 'textmodel.g.dart';` 和最后的两个方法会报错, 先不要管因为我们没有生成对应的目标文件.
#### 生成目标文件
在Terminal中项目的根目录, 运行命令
```shell
$ flutter packages pub run build_runner build
```
这个会根据我们定义的文件自动生成对应的目标文件, 在这条命令执行完毕后, 项目中会多出`testmodel.g.dart`文件, 此时我们的对象就可以直接使用`fromJson`和`toJson`方法进行json的解析和转换了!
```dart
//传入一个map来创建和赋值对象
final model = TestModel.fromJson(map);
//将对象转换为Map/List, 进行操作
final params = model.toJson();
```
**命令持续生成**

我们可以命令的`watch`参数, 让builder根据我们保存文件的操作持续自动生成对应的`.g`文件
```shell
$ flutter packages pub run build_runner watch
```
## 总结
个人感觉[json_to_dart](https://javiercbk.github.io/json_to_dart/) 的方法是好于 [json_serializable](https://pub.dev/packages/json_serializable#-installing-tab-) 的

[json_serializable](https://pub.dev/packages/json_serializable#-installing-tab-) 会入侵项目代码, 且需要额外的配置, 还需要命令手动生成

而 [json_to_dart](https://javiercbk.github.io/json_to_dart/) 只关注生成对象本身, 我们什么时候需要, 什么时候就拿出来用, 其它时间与项目并无耦合, 在使用中更加明确分离.