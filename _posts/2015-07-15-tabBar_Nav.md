---
layout: post
title: tabBarController和Navigation之间的问题
description: "实现类似于微信中tabBarItem和Nav之间的切换"
category: personal
tags: [iOS, tabBarController, tabBar, Navigation]
imagefeature: 
comments: true
mathjax:
---

------

##tabbar 和navigation之间的问题

创建一个tabbarController的主界面,childControllers为 ViewController1、ViewController2,如何在ViewController2里面的某一个界面中点击一个按钮 让ViewController2 push到下一个界面 并且让界面显示为该界面。

更形象的例子：可参考微信，在微信界面点击进入聊天框；在通讯录界面，点击相应联系人，然后点击发送消息，进入聊天框。两个方式进入聊天框 左上角的返回键 都是返回tabbar的主界面。

<!--more-->

废话不多说直接上核心代码：其实也就是只有一段，就是监听消息按钮的点击事件，做相应的处理。在这里遇到了点问题，一会儿说。

###问题代码

{% highlight objective-c %}


-(IBAction)sendMessage
{
    //将当前控制器弹出栈
    [self.navigationController popToRootViewControllerAnimated:YES];

//取到storyBoard中对应的控制器
    self.tabBarController.selectedIndex = 0;
    UINavigationController *nav = self.tabBarController.viewControllers[0];
    
    UIStoryboard *mainSB = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
    UIViewController *chatVc = [mainSB instantiateViewControllerWithIdentifier:@"chat"];
    
    [nav pushViewController:chatVc animated:YES];
}

{% endhighlight %}

###问题效果图

![](http://7xke07.com1.z0.glb.clouddn.com/image/tabBar-Nav-error.gif)

###原因分析

大家会看到效果图中，发消息的那个控制器已经pop掉了，但是当点击通讯录tabBarItem时，发消息的那个控制器会一闪而过。

原因(个人理解)：**通过pop方法将控制器弹出栈，会销毁当前控制器**，但是在同一个方法里先pop了当前的控制器，紧接着又进行了其他的操作(PS:当还在这个控制器的操作没有执行完的时候,在内存中还被持有在内存中时，就不会被销毁！*此处只是个别现象，应该具体问题具体分析*)，导致控制器没有被立刻销毁。之后点击tabBarItem时，才销毁了控制器，因此会一闪而过；

为什么只有在点击了通讯录tabBarItem时，才会销毁发消息控制器呢?

原因(个人理解)：这个是具体情况，因为tabBarController对应有导航控制器，但是pop后，对应A控制器已经被移出栈顶了，但是没有被销毁，所以还显示着。当点击通讯录tabBarItem时，会显示对应导航控制器的栈顶控制器，此时栈顶控制器是B控制器，但是A控制器还压在B控制器上，因此tabBar要显示控制器B，系统会发现A已经不在栈中了，然后销毁了A控制器…

###修改后的代码


{% highlight objective-c %}


-(IBAction)sendMessage
{
//取到storyBoard中对应的控制器
    self.tabBarController.selectedIndex = 0;
    UINavigationController *nav = self.tabBarController.viewControllers[0];
    
    UIStoryboard *mainSB = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
    UIViewController *chatVc = [mainSB instantiateViewControllerWithIdentifier:@"chat"];
    
    [nav pushViewController:chatVc animated:YES];
    
        //注意此处：仅仅是将位置换了一下
    [self.navigationController popToRootViewControllerAnimated:YES];
}

{% endhighlight %}

###效果图演示

![](http://7xke07.com1.z0.glb.clouddn.com/image/tabBar-Nav-ok.gif)

###demo代码分享

链接: http://pan.baidu.com/s/1nttHH7F 密码: zap2
