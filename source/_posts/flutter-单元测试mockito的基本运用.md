---
title: flutter 单元测试mockito的基本运用
date: 2020-06-18 17:44:20
tags: flutter
---

> web的单元测试好写，flutter的单元测试也好写，web的ui点击事件单元测试可以用jest全包，flutter的widget点击事件怎么模拟？

<!--more-->

### web和flutter单元测试的不同

- 如今react/vue的单元测试大家基本都用jest/enzyme了，确实好用，当我们mock的时候，jest会自动把待测试文件匹配的路径一同模拟。

  比如：

  ```javascript
  import * as api from '@/services/api';
  jest.mock("@/services/api", () => {
      return {
          auth: {
              token: jest.fn(() => {
                  return Promise.resolve({ data: { info: {} }, status: 'success' });
              })
          }
      };
  });
  ```

  那我们不用去担心待测试文件方法调用，如果test文件调用api.auth.token()，肯定会返回**{ data: { info: {} }, status: 'success' }**

- flutter的思想并不是这样的，mock不匹配路径，严格点说，和路径无关。flutter单元测试的中心思想很简单，和单元测试的思想是一致的，模拟依赖，各司其职。

  首先flutter没有模拟路径的方法，那如何去解决api请求的问题呢，毕竟最大的难点就是这里。

  对比两个代码段：

  ```dart
  FLToastProvider(
        defaults: _toastDefaults,
        child: Builder(builder: (BuildContext context) {
          return MultiProvider(
              providers: [
                ChangeNotifierProvider<DatetimeProvider>(
                    create: (_) => DatetimeProvider()),
                ChangeNotifierProvider<HealthProvider>(
                    create: (_) => HealthProvider(),
              ],
              child: widget.child
          );
        }),
      )
  ```

  ```
  FLToastProvider(
        defaults: _toastDefaults,
        child: Builder(builder: (BuildContext context) {
          return MultiProvider(
              providers: [
                ChangeNotifierProvider<DatetimeProvider>(
                    create: (_) => DatetimeProvider()),
                ChangeNotifierProvider<HealthProvider>(
                    create: (_) => HealthProvider(api: healthApi)),
              ],
              child: widget.child
          );
        }),
      )
  ```

  暂时把这个代码片段叫做makeTestableWidget，如上所示，原来我们的api依赖直接卸载provider里面，现在我们把它抽了出来，作为依赖传进去。这就是单元测试思想的体现。现在我们可以把我们模拟的依赖作为参数传进去，就可以去控制api请求的结果了。

  ```dart
  class MockHealthAPI extends Mock implements HealthApi {}
  
  final healthApi = MockHealthAPI();
  ```

  ```dart
  dosth...
  when(healthApi.getBloodPressureByGraph({"category": "TIMES", "times": 7,}))
        .thenReturn(GraphBpModel.fromJson(json.decode("""
          {"graphBp":{"time":[{"year":1592202572319,"time":1592202572319},{"year":0,"time":1591861014653},{"year":0,"time":1591177545312}],"high":[110,110,121],"low":[80,80,98],"status":["NORMAL","NORMAL","NORMAL"]},"graphHeartRate":{"time":[{"year":1592202572319,"time":1592202572319},{"year":0,"time":1591861014653},{"year":0,"time":1591177545312}],"heartRate":[60,60,72],"status":["NORMAL","NORMAL","NORMAL"]},"bpLatest":{"high":96,"low":68,"bpStatus":"NORMAL","heartRate":41,"heartRateStatus":"LOW","measuredAt":1591177056191590,"year":null,"referenceBpMax":140,"referenceBpMin":90,"referenceHeartRateMax":100,"referenceHeartRateMin":47}}
        """)));
  dosth...
    await tester.tap(BtnFinder);
  //
      await tester.pump();
  //  检查 是否调用过
      verify(healthApi.getBloodPressureByGraph({"category": "TIMES", "times": 7})).called(1);
  ```

  测试通过。