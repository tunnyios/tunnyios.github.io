------
layout: post
title: Object－C初探(1)
description: "objetc-c语法的一些特性，以及面向对象特性"
category: sample-post
tags: [OC, 面向对象, oc语法特性]
imagefeature: 2015-03-12.JPG
comments: true
mathjax:
------

> * 充满仪式性的第一次。初探Object-C特性

1.	oc中以@开头得都是Foundation框架得NSString类型的字符串。
2.	oc有3中数据类型：基础类型，对象类型，id类型
> * 基础类型不需要加＊，包括int、float、double、和char类型
> *	对象类型指的是类或协议所声明的指针类型必须要加＊
> *	id类型可以表示任 意类型，但一般只表示对象类型
3.	%i 表示格式化输出int类型  %o 8进制 %#x 16进制; %f 浮点类型会保留6位 %@ 打印对象
4.	数据类型转换，总的原则：小存储容量数据类型可以自动转换成大存储容量数据类型。
	不同类型数据间按照下面关系的从左到右(从低到高)自动转换
		_Bool、char、short、int、枚举类型->int->long int->long long->float->double->long double

	[^1]
<!--more-->

[^1]: <http://en.wikipedia.org/wiki/Syntax_highlighting>

### Pygments高亮代码块

#### C

{% highlight c %}
#include <stdio.h>
int main(void)
{
    printf("Hello World\n");
    return 0;
}
{% endhighlight %}

#### Java

{% highlight java linenos %}
class helloworld
{
    public static void main(String args[])
    {
        System.out.println("Hello World");
    }
}
{% endhighlight %}

### 标准代码块

#### Python

~~~ python
#!/usr/bin/python
printf("Hello World")
~~~

### 行内代码

#### Bash

`echo "Hello World"`
