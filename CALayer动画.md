## CALayer核心动画 
CALayer有两个重要的属性： position和anchorPoint

1、 position 用来设置CALayer在父层中的位置，以父层的左上角为原点（0， 0）

2、 anchorPoint 称之为定位点，决定CALayer上哪一个点会在position属性所指定的位置，以自身的左上角为原点（0， 0），它的X，Y的取值范围是0～1，默认值是（0.5， 0.5）

3、 特别注意，poison和anchorPoint永远都是重合的

4、UIView的center就是内部layer的position


#### 隐式动画
1、CATransition;  转场动画
2、CATransaction;   // 事务
// 隐式动画封装的是一个事物
![avatar](/Users/yanrenhao/Desktop/animation.jpeg)

```objc
    // 开启事务
    [CATransaction begin];
    // 设置隐式动画时常
    [CATransaction setAnimationDuration:3];
    // 关闭隐式动画
    [CATransaction setDisableActions:YES];
    // 提交
    [CATransaction commit];
```

### 获取当前时间的时分秒

```objc
// 获取当前时间的时分秒数据，通过日历组件来获取
    NSCalendar *calendar = [NSCalendar currentCalendar];
    NSDateComponents *component = [calendar components:NSCalendarUnitSecond | NSCalendarUnitMinute | NSCalendarUnitHour fromDate:[NSDate date]];
    NSInteger h = component.hour;
    NSInteger m = component.minute;
    NSInteger s = component.second;
```



### UIView动画与核心动画的区别
1、核心动画只作用在layer上；</br>
2、核心动画看到的一切都是假象，并没有去修改属性的真实值</br>

#### 什么时候核心动画，什么时候使用UIView动画；
1、当需要与用户进行交互时，必须使用UIView动画；</br>
2、做帧动画时，当根据路径做动画时，使用核心动画</br>
3、做转场动画时，使用核心动画，转场动画类型比较的多</br>



