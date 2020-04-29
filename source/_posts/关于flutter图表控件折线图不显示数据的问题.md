---
title: 关于flutter图表控件fl_chart折线图不显示数据的问题
date: 2020-04-29 15:35:09
tags: flutter
---

> 目前正在带领app团队使用flutter重构公司的app，我负责其中一部分，其中有需要使用图表的组件，经过调研后决定使用fl_chart，功能强大且开发人员也比较活跃。但是发现折线图不能显示当前点的数据...没问题，clone下来自己修改下。

### line_chart的示例

```dart
LineChartBarData _createLineChatBarData(List<FlSpot> spots, List<int> xAxisIndex) {
  return LineChartBarData(
    showingIndicators: xAxisIndex,
    barWidth: 2, // 折线图折线宽度
    spots: spots,
    colors: [lineChartColor], //多个颜色会有渐变效果
    isCurved: true, //平滑曲线，一般配合curveSmoothness使用
    curveSmoothness: 0,
    isStepLineChart: false,
    dotData: FlDotData(
        show: true,
        dotSize: 3,
        strokeWidth: 0,
        showText: true,  // 添加自己的属性
        getTextStyle: (FlSpot spot) {  // 添加自己的方法
          return TextStyle(color: Colors.green);
        },
        getStrokeColor: (FlSpot spot, double xPercentage, LineChartBarData bar) {
          return Colors.red;
        },
        getDotColor: (FlSpot _, double xPercentage, LineChartBarData bar) {
          return Colors.red;
        }
      ),
  );
}

LineChart _chartChild() {
  List<int> spotsNum = [];
  widget.spotsArray.forEach((item) {
    item.forEach((spot) {
      spotsNum.add(spot.y.toInt());
    });
  });

  spotsNum.addAll(widget.extraLines);
  int yAxisMax = ArrayUtil.getArrMaxNum(spotsNum);
  int yAxisMin = ArrayUtil.getArrMinNum(spotsNum);
  List<int> xAxisIndex = widget.xAxis.asMap().keys.toList();
  List<LineChartBarData> lineBarsData = widget.spotsArray.asMap().keys.map((index) {
    return _createLineChatBarData(widget.spotsArray[index], xAxisIndex);
  }).toList();


  return LineChart(LineChartData(
      maxY: yAxisMax.toDouble(),  // y轴最大值
      minY: yAxisMin.toDouble(),  // y轴最小值
      titlesData: FlTitlesData(
        //标题
        leftTitles: SideTitles(   // Y轴
          //左侧标题
            margin: 15.w,
            showTitles: true, //展示标题
            reservedSize: 15, //标题宽度（不足会换行）
            textStyle: chartTitleStyle, //标题样式
//          interval: 7,
            getTitles: (val) {
              return null;
            }
        ),
        bottomTitles: SideTitles(  // X轴
          //底部标题
          margin: 20.h,
          showTitles: true, //展示标题
          textStyle: chartTitleStyle,
          getTitles: (val) {
            int index = xAxisIndex.length - 1 - int.parse((val - 1).toStringAsFixed(0));
            var dateString = widget.xAxis[index];
            return dateString;
          },
        ),
      ),
      lineBarsData: lineBarsData,
      borderData: FlBorderData(   // 图表边框
        show: false,
      ),
      extraLinesData: ExtraLinesData(  // 图表上额外的线（标准值标注）
        extraLinesOnTop: true,
        horizontalLines: widget.extraLines.map((line) {
          return _createExternalHLine(line.toDouble());
        }).toList(),
        verticalLines: [],
      ),
      gridData: FlGridData(  // 图表的珊格控制
        show: false,
        drawHorizontalLine: false,
        drawVerticalLine: false,
        verticalInterval: 3
      ),
//      showingTooltipIndicators: xAxisIndex.map((index) {  // 显示tooltip的点
//        return ShowingTooltipIndicators(index, lineBarsData.asMap().keys.map((idx) {
//          return LineBarSpot(
//              lineBarsData[idx], idx, lineBarsData[idx].spots[index]
//            );
//        }).toList());
//      }).toList(),
//      lineTouchData: LineTouchData(  图表触控控制系统（和showingTooltipIndicators搭配使用）
//        enabled: false,   //  设置为true则触控显示tooltip，false一直显示
////        fullHeightTouchLine: true,
//        getTouchedSpotIndicator: (LineChartBarData barData, List<int> spotIndexes) {
//          return spotIndexes.map((index) {
//            return TouchedSpotIndicatorData(
//              FlLine(
//                color: Colors.transparent,
//              ),
//              FlDotData(
//                show: true,
//                dotSize: 0,
//                strokeWidth: 3,
//                getStrokeColor: (spot, percent, barData) => _getCurColorByIndex(widget.status, index),
//              ),
//            );
//          }).toList();
//        },
//        touchTooltipData: LineTouchTooltipData(  //  tooltip样式控制
//            tooltipBgColor: Colors.transparent,
//            tooltipRoundedRadius: 0,
//            tooltipBottomMargin: 0,
//            getTooltipItems: (List<LineBarSpot> touchedSpots) {
////              if (touchedSpots == null) {
////                return null;
////              }
//              return touchedSpots.map((LineBarSpot touchedSpot) {
////                if (touchedSpot == null) {
////                  return null;
////                }
//                final TextStyle textStyle = TextStyle(
//                  color: Colors.red,
//                  fontWeight: FontWeight.bold,
//                  fontSize: 10,
//                );
//                return LineTooltipItem(touchedSpot.y.toString(), textStyle);
//              }).toList();
//            }
//        ),
//      ),
}
```

**单折线**使用showingTooltipIndicators和lineTouchData配合即可实现显示点的数值，效果大致如下:

![single](https://pic.downk.cc/item/5ea93820c2a9a83be51bc749.png)

但是如果为**多折线**，所有数据会显示在最上，效果如下：

![multi](https://pic.downk.cc/item/5ea93820c2a9a83be51bc74b.png)

显然和我们期待的不一致，所以需要另辟蹊径...

## 解决方法

查阅Fl_Chart的源码后发现，折线图文件分为“line_chart.dart”,"line_chart_data.dart"和“line_chart_painter.dart”三个文件，其中line_chart调用另两个文件，line_chart_data提供数据结构，也就是各种类文件，line_chart_painter负责具体绘制。

而我们需要做的就是在绘制图表上的点的同时，把当前点的y轴数值绘制上就可以了，经过一番查找，line_chart_data中 **FlDotData**负责定义点的数据，line_chart_painter中 **_drawDots**负责绘制，修改如下：

1. 首先将fl_chart图表库放入plugins文件夹中，使用本地库引用。

2. 在FlDotData的类中加入三个新属性：

   ```dart
   
   @required bool showText,   // 控制是否显示数值
   GetTextStyleCallback getTextStyle, // 数值样式
   Offset textOffset, // 数值偏移 （没用，还没实现）
   
   ```

   

3. 在line_chart_painter的_drawDots中的

   ```
   for (int i = 0; i < barData.spots.length; i++) {}
   ```

   循环最后加入绘制当前点的代码即可：

   ```dart
   // 修改：在每个点上面显示数值  = =
     void _drawDots(Canvas canvas, Size viewSize, LineChartBarData barData) {
       if (!barData.dotData.show || barData.spots == null || barData.spots.isEmpty) {
         return;
       }
       viewSize = getChartUsableDrawSize(viewSize);
   
       final barXDelta = _getBarLineXLength(barData, viewSize);
   
       for (int i = 0; i < barData.spots.length; i++) {
         final FlSpot spot = barData.spots[i];
         if (barData.dotData.checkToShowDot(spot, barData)) {
           final double x = getPixelX(spot.x, viewSize);
           final double y = getPixelY(spot.y, viewSize);
   
           final double xPercentInLine = ((x - getLeftOffsetDrawSize()) / barXDelta) * 100;
   
           final dotColor = barData.dotData.getDotColor(spot, xPercentInLine, barData);
   
           if (barData.dotData.getStrokeColor != null && barData.dotData.strokeWidth != null) {
             canvas.drawCircle(
                 Offset(x, y),
                 barData.dotData.dotSize + (barData.dotData.strokeWidth / 2),
                 _dotPaint
                   ..color = barData.dotData.getStrokeColor(spot, xPercentInLine, barData)
                   ..strokeWidth = barData.dotData.strokeWidth
                   ..style = PaintingStyle.stroke);
           }
           canvas.drawCircle(
               Offset(x, y),
               barData.dotData.dotSize,
               _dotPaint
                 ..color = dotColor
                 ..style = PaintingStyle.fill);
   //        print("barData.dotData.show ${barData.dotData.show}");
   //        print("barData.dotData.dotSize ${barData.dotData.dotSize}");
   //        print("barData.dotData.showText ${barData.dotData.showText}");
   
           if (barData.dotData.showText) {
             TextStyle _defaultStyle = TextStyle(color: Colors.red, fontSize: 12, fontWeight: FontWeight.bold);
             Offset _defaultOffset = Offset(x - 8, y - 20);
             // 绘制spot点y轴数值
             final TextSpan span = TextSpan(
               style: barData.dotData.getTextStyle(spot),
               text: "${spot.y.toInt()}",
             );
             final TextPainter tp = TextPainter(
                 text: span,
                 textAlign: TextAlign.center,
                 textDirection: TextDirection.ltr,
                 textScaleFactor: textScale);
             tp.layout();
             final drawOffset = _defaultOffset;
             tp.paint(canvas, drawOffset);
           }
         }
       }
     }
   ```

   

最后效果如图：

![final](https://pic.downk.cc/item/5ea93820c2a9a83be51bc745.png)