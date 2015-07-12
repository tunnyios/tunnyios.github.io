---
layout: post
title: Assets.car提取素材
description: "关于素材的一些问题"
category: personal
tags: [assets, .car, themeEngine, cartool]
imagefeature: 
comments: true
mathjax:
---

------

## **关于Assets.car素材问题**

最近在做自己的第一个App,由于全程都是自己一个人完成，所以原型设计、素材都得自己找，自己改。遇到了提取Assets.car中的素材的问题，通过网络找到了2中解决方法：

<!--more-->	

### **themeEngine**

使用themeEngine工具，可以打开Assets.car文件。这软件需要Mac OS X Yosemite10.10.1以上的系统运行，Photo Shop需要13.0以上版本，解包之后可以直接在软件里面Send to Photoshop修改素材，修改之后还可以直接从Photoshop里面读取。非常方便。

*使用效果图*
 
![pitcure](http://tunnyios.github.io/images/150712_Assets01.png)
	
### **cartool**

使用cartool工具可以直接提取素材至指定目录中,使用方法：

1. 到github上下载cartool项目
2. 在Xcode中编译运行
3. 设置参数源路径和目标路径

*配置效果图*

![pitcure](http://tunnyios.github.io/images/150712_Cartool01.png)
