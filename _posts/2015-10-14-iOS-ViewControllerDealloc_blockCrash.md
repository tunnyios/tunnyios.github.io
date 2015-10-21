---
layout: post
title: iOS中控制器的释放问题
description: "分析iOS中控制器未释放的原因，以及block的循环引用的简单介绍"
category: personal
tags: [iOS, block, dealloc]
imagefeature: 
comments: true
mathjax:
---

------

![](http://7xke07.com1.z0.glb.clouddn.com/2015-10-14-iOS-ViewControllerDealloc_blockCrash.JPG)

##**iOS中控制器的释放问题**

ARC工程是可以重写dealloc方法并被系统调用的，但不需要手动调用父类的dealloc，手写[super dealloc]方法会报错，事实上系统会自动帮你调用父类的dealloc方法，不需要你实现。可以通过在dealloc方法中打印log查看控制器是否被释放。

控制器在被pop后移出栈后会被释放，但有些时候会发现控制器出栈的时候不会调用dealloc方法，归根结底，是因为当前控制器被某个对象强引用了，控制器的引用计数不为0，系统无法帮你释放这部分内存。

<!--more-->

###**控制器中NSTimer没有被销毁**

当控制器中存在NSTimer时，就需要注意，因为当**[NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(updateTime:) userInfo:nil repeats:YES];**时，这个/*target:self*/ 就增加了VC的RetarnCountr, 如果你不将这个timer invalidate，就别想调用dealloc。需要在viewWillDisappear之前需要把控制器用到的NSTimer销毁。

{% highlight objective-c %}
// 销毁timer
[timer invalidate];
// 置nil
timer = nil; 
{% endhighlight %}

###**控制器中的代理不是weak属性**

例如**@property (nonatomic, weak) id<HCAppViewDelegate> delegate;**代理要使用弱引用，因为自定义控件是加载在视图控制器中的，视图控制器view对自定义控件是强引用，
如果代理属性设置为strong，则意味着delegate对视图控制器也进行了强引用，会造成循环引用。导致控制器无法被释放，最终导致内存泄漏。

###**控制器中block的循环引用**

block会把它里面的所有对象强引用(在ARC下)/*PS:MRC下会retain加1*/，包括当前控制器self，因此有可能会出现循环引用的问题。
即一个对象有一个Block属性，然而这个Block属性中又引用了对象的其他成员变量，那么就会对这个变量本身产生强应用，那么这个对象本身和他自己的Block属性就形成了循环引用。在ARC下需要修改成这样：(/*也就是生成一个对自身对象的弱引用*/)

{% highlight objective-c %}
__weak typeof(self) weakSelf = self;
{% endhighlight %}

###**注意**

其实总结下来也就是：控制器强引用着 block 。凡是控制器的对象，或者控制器的成员变量(*无论成员变量是基础类型还是其他的*),或者是类的方法调用，类的成员变量的方法调用，，只要在 block 中就会被强引用，(间接导致 block 强引用了控制器)。引发循环引用，最终导致内存泄漏！

**即：初学者保险起见可以把block中所有的涉及到self的全给替换成weakSelf**

但是每种情况都采用这种方法有时候并不好。试想下，如果我们有个对象，用于发送一个后台任务（比如下载数据），并且调用了self的一个方法。这时如果我们弱引用self，该对象的生命周期结束早于闭包结束被释放，因而当我们的闭包调用的*doSomething()*方法，该对象可能就不存在了，方法也得不到执行。因此如果在闭包外面弱引用了self，则遇到这种情况就又需要在闭包内声明一个强引用指向弱引用。(PS:*这种语法不仅恶心乏味不直观，而且违反了闭包作为一个独立处理实体的原则*)

{% highlight objective-c %}
__weak __typeof(self)weakSelf = self; 
int i = 0;
AFNetworkReachabilityStatusBlock callback = ^(AFNetworkReachabilityStatus status) 
{ 
	__strong __typeof(weakSelf)strongSelf = weakSelf; 
	strongSelf.networkReachabilityStatus = status; 
	if (strongSelf.networkReachabilityStatusBlock) { 
		strongSelf.networkReachabilityStatusBlock(status); 
	}
}; 
{% endhighlight %}

函数的闭包和 block 如果没有引用任何实例或类变量，其本身也不会造成循环引用。最常见的一个例子就是 UIView 的 animateWithDuration。

{% highlight objective-c %}
func myMethod() {
   ...
   [UIView animateWithDuration:0.3 animations:^{
	       self.someOutlet.alpha = 1.0
	       self.someMethod()
       }];
}
{% endhighlight %}

PS:系统自带的动画的 block 方法、 GCD()等方法，可以不用_weak typeof (self)weakSelf = self;
GCD方法，闭包会强引用self，但是实例化的self不会强引用闭包，所以一旦闭包结束，它就会被释放，所以循环引用也不会产生；

**学会理解对象的生命周期，明白何时应该声明弱引用，以及对象生存周期的意义，这很重要**


