---
layout: post
title: 一个tabBarController管理多个Storyboard
description: "面对复杂开发，多个storyboard的使用方法"
category: personal
tags: [iOS, storyborad, tabBarController]
imagefeature: 
comments: true
mathjax:
---

------

##一个tabBarController管理多个Storyboard

随着项目的业务逻辑越来越复杂,随着项目越来越大,那么我们Storybard中得控制器就越来越多, 就越来越难以维护。然而使用Storyborad又能更方便的帮助我们做屏幕适配(PS:尤其在6、6+出来后)。

我们可以将复杂的问题简单化，通过创建多个Storyboard分别管理不同的模块的方式来优化代码,分成两步：*好处：多个Storyboard可以分开管理，一个人负责一块儿，提交代码时不冲突；逻辑简单，方便屏幕适配*

1. 按业务逻辑拆分Storyboard
2. 在ApplicationDelegate中创建一个tabBarController,
	并将4个Storybard作为子控制器添tabBarController。

<!--more-->

废话不多说直接上核心代码：此处有4个Stroyboard(Home、Message、Discover、Profile)，每个Storyboard中都是initial对应的是导航控制器，导航控制器的根控制器是UIViewController

###AppDelegate.m

{% highlight objective-c %}


	// 1.创建Window
	self.window = [[UIWindow alloc] initWithFrame:ACScreenBounds];
    self.window.backgroundColor = [UIColor whiteColor];
    // 2.创建TabBarCongtroller
    UITabBarController *tb = [[UITabBarController alloc] init];
    // 3.加载4个Storyboard
    UIStoryboard *homeSB =[UIStoryboard storyboardWithName:@"Home" bundle:nil];
    UIStoryboard *messageSB =[UIStoryboard storyboardWithName:@"Message" bundle:nil];
    UIStoryboard *discoverSB =[UIStoryboard storyboardWithName:@"Discover" bundle:nil];
    UIStoryboard *profileSB =[UIStoryboard storyboardWithName:@"Profile" bundle:nil];
    
    //3.5 设置tabBarItem
    UINavigationController *homeNav = [homeSB instantiateInitialViewController];
    UIViewController *homeVc = homeNav.topViewController;
    homeVc.title = @"首页";
    
    UINavigationController *messageNav = [messageSB instantiateInitialViewController];
    UIViewController *messageVc = messageNav.topViewController;
    messageVc.tabBarItem.title = @"消息";
    messageVc.tabBarItem.image = [UIImage imageNamed:@"1"];
    
    // 4.创建并将4个Storyboard添加到TabBarCongtroller中
    tb.viewControllers = @[homeNav,
                           messageNav,                       discoverSB.instantiateInitialViewController,
profileSB.instantiateInitialViewController
                           ];
    
    // 5.设置根控制器
    self.window.rootViewController = tb;
    // 6.显示Window
    [self.window makeKeyAndVisible];

{% endhighlight %}

###代码中需要注意的

* tabBarItem的title和image必须在拿到实例后设置才能显示
* tabBarItem的title和image只能在继承自UIViewController的控制器才能设置
* 将子控制器添加到tabBarController中时，一定要添加实例设置后的控制器，如果直接添加类似于这个的discoverSB.instantiateInitialViewController,将不能显示title和iamge

/*

     此外还应注意不能这样设置：
     UIViewController *homeVc = [homeSB instantiateViewControllerWithIdentifier:@"home"];
     homeVc.title = @"首页";
     tb.viewControllers = @[homeSB.instantiateInitialViewController];
     
     因为homeVc和 [homeSB instantiateInitialViewController].topViewController 指向的不是同一片内存地址，因此设置不会生效。
     
*/

###代码优化

以上就是一个tabBarController来管理多个Storyboard的方法。还可以把上面的创建tabBarController封装到一个自定义的UITabBarController中，达到优化的效果，将代码放到它改存在的位置。因为这些子控制器是归根控制器来管理的Application根本不关心子控制器如何操作，所以子控制器应该封装在跟控制器中，子控制器的内容只让根控制器决定。

事例图：

![pitcure](http://7xke07.com1.z0.glb.clouddn.com/image/storyboard.png)
