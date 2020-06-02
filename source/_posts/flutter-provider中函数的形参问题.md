---
title: flutter provider中函数的形参问题
date: 2020-06-02 18:26:07
tags: flutter
---

```dart
class DatetimeProvider with ChangeNotifier {
  DateTime datetime  = DateTime.now();
 
  void setDatetime(DateTime datetime) {
    datetime = datetime;
    notifyListeners();
  }
}
```

调试过程中，调用setDatetime，无论传入任何的datetime，原datetime都不会改变。

原因：函数的形参和定义的属性同名，函数执行时，没有用到我们传入的参数，一直自身赋值自身...

修复：改变参数名称，将setDatetime(DateTime datetime)改为setDatetime(DateTime newDatetime)即可...