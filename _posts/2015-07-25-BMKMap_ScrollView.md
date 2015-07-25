---
layout: post
title: scrollView中切换百度地图
description: "在scrollView中切换View和BMKMapView,以及scrollView的原理"
category: personal
tags: [iOS, scrollView, BMKMapView, mapView]
imagefeature: 
comments: true
mathjax:
---

------

##scrollView中切换百度地图

要实现如下功能：通过滑动切换View和BMKMapView,手势滑动超过一半后，显示对应的View。

效果图如下：

<!--more-->

![](http://7xke07.com1.z0.glb.clouddn.com/image/BMKMapView_scrollView.gif)


###实现方法

此处使用storyboard来实现scrollView(使用autolayout方便做屏幕适配,storyboard如何实现请参考url:*http://www.cocoachina.com/ios/20150104/10810.html*)。

使用UIScrollView来进行切换时，遇到一个问题：切换到BMKMapView视图上时，左右滑动的手势被UIScrollView拦截了，导致，BMKMapView上不能够左右滑动。因此需要自定scrollView。

通过自定义scrollView，重写两个方法- (BOOL)touchesShouldBegin:(NSSet *)touches withEvent:(UIEvent *)event inContentView:(UIView *)view 和 - (BOOL)touchesShouldCancelInContentView:(UIView *)view，来实现。

###scrollView的原理

当多个视图进行叠加的时候，touch事件是作用到最上面的视图上，但是如果父视图是UIScrollView，如果默认，可能touch子视图会造成UIScrollView的滚动。 

当手指触摸到UIScrollView内容的一瞬间，会产生下面的动作：

* 拦截触摸事件
* tracking属性变为YES
* 一个内置的计时器开始生效，用来监控在极短的事件间隔内是否发生了手指移动
> * case1：当检测到时间间隔内手指发生了移动，UIScrollView自己触发滚动，取消发送tracking 给子视图。tracking属性变为NO。手指触摸下即使有(可以响应触摸事件的)内部控件也不会再响应触摸事件。
> * case2：当检测到时间间隔内手指没有移动，当时间结束时，，UIScrollView会发送tracking events到子视图上。tracking属性保持YES。手指触摸下如果有(可以响应触摸事件的)内部控件，则将触摸事件传递给控件进行处理。

UIScrollView的子控件想要接收touch事件，就是用户点击UIScrollView上的子视图时，要先处理子视图上的touch，而不发生滚动。这时候就需要自定义UIScrollView重载touchesShouldBegin:withEvent:inContentView: ，从而决定自己是否接受子视图中的touch事件。 

###自定义scrollView的几个注意点

1. touchesShouldBegin:withEvent:inContentView 决定自己是否接收 touch 事件，YES：(自己不接收，发送事件)即不滚动；NO：(自己接收，不发送)即滚动；(PS:默认是YES)
2. touchesShouldCancelInContentView 开始发送 tracking messages 消息给 subview 的时候调用这个方法，决定是否发送 tracking messages 消息到subview。假如返回 NO，发送。YES 则不发送(PS:1和2配合使用)
3. scorllView的属性：delaysContentTouches 是个布尔值，当值是 YES 的时候，用户触碰开始，scroll view要延迟一会，看看是否用户有意图滚动。假如滚动了，那么捕捉 touch-down 事件，否则就不捕捉。假如值是NO，当用户触碰， scroll view 会立即触发 


###核心代码

{% highlight objective-c %}

//view是用户点击的视图
- (BOOL)touchesShouldBegin:(NSSet *)touches withEvent:(UIEvent *)event inContentView:(UIView *)view
{
    // 获取一个UITouch
    UITouch *touch = [touches anyObject];
    
    // 获取当前的位置
    CGPoint current = [touch locationInView:self];
    CGFloat x = [UIScreen mainScreen].bounds.size.width;
    if (current.x >= x + 10) {
        //在地图上
        NSLog(@"在地图上, 不滚动, view class is %@", view.class);
        return YES;
    } else {
        return [super touchesShouldBegin:touches withEvent:event inContentView:view];
    }
}

- (BOOL)touchesShouldCancelInContentView:(UIView *)view
{
    NSLog(@"cancle class is %@", view.class);
    
    if ([view isKindOfClass:NSClassFromString(@"TapDetectingView")]) {
        return NO;
    } else {
        return [super touchesShouldCancelInContentView:view];
    }
}


{% endhighlight %}

###注意的问题

1. 如果storyboard中使用的是系统自带的MKMapView，则不需要自定义scrollView
2. 判断用户当前点击的视图时，点击BMKMapView对应的View类型是TapDetectingView，因此需要转换一下

###demo代码分享

链接: http://pan.baidu.com/s/1jGznMbO 密码: 347m
