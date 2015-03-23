---
layout: post
type:  photo                # ! Important
photo: http://tunnyios.github.io/images/2015-03-18.JPG 		# In case you do not want the featured image to display on the front page
title: "Objective-C初探(2)"         # Title of the post
description: "objective-c语法特性之继承、多态、分类、协议"   # Description of the post, used for Facebook Opengraph & Twitter
headline: Some headline       # Will appear in bold letters on top of the post
modified: 2015-03-18        # Date
category: personal
tags: [Objective-C, 继承, 多态, 动态类型, 动态绑定, 分类, 协议]
image: 
  feature:
comments: true
mathjax:
---

------

### 继承与多态

继承性：objective-c可以继承非private限定的成员变量；属于单重继承，即只有一个父类。为了实现多重继承objective引入了协议。子类可以重写父类的方法，以及命名与父类同名的成员变量(即隐藏父类的成员变量)

多态：包括**动态类型**和**动态绑定** objective-c的多态实现机制：实际上就是一种动态类型检查。

多态性是指在父类中定义的成员变量和方法呗子类继承后，可以具有不同的数据类型或表现出不同的行为。声明一个对象，它的类型是父类的类型，而实例化子类的实例。在java、c++种都有这种写法，在objective-c里也可以这样写，属于**静态类型**。

#### Objective-C code 1.0 静态类型对象声明

{% highlight objective-c %}
//Graphics 是父类
Graphics *graphics;
//Ellipse 是子类
graphics = [[Ellipse alloc] init];
[graphicse onDraw];
{% endhighlight %}

**ID类型**是泛类型，可以用来存放各种类型的对象，使用id也就是使用**动态类型**。ID类型在编译的时候不进行类型检查，而在运行的时候进行类型检查，动态的检查被实例化的对象有没有对应的方法。从编程的角度来讲，如果已知它的静态类型，最好用静态类型；虽然ID类型可以表示任何类型的对象，但是不要滥用，如果能够确定对象数据类型的时候，要使用静态类型，静态类型在编译阶段检查错误，而不是在执行阶段，且静态类型程序的可读性好。

#### Objective-C code 1.1 动态类型对象声明

{% highlight objective-c %}
//Graphices 是父类
id *graphices;
//Ellipse 是子类
graphices = [[Ellipse alloc] init];
[graphices onDraw];
{% endhighlight %}

### 分类与协议
**分类**(*Gategory*) 允许向一个类文件中添加新的方法声明，不需要使用子类机制，并且在类实现的文件中的同一个名字下定义这些方法。想要扩展一个类的功能，有两种方法：1. 传统的方法是通过子类的继承机制来实现；2. 通过分类机制，**添加一个方法，来扩充一个功能，编译时不检测，运行时检查看当前类以及分类中有没有这个方法，没有就出错，这就是动态机制**。

分类机制比继承机制实现起来更加方便，不会影响到原来父类的所有功能。动态机制：编译时不检查，运行时检查。有就调用，没有就出错。分类本质上是通过Objective-C的动态绑定而实现的，通过分类使用能够达到比继承更好的效果。分类是在Java和C++等面相对象的语言中没有的概念。

#### Objective-C code 1.2 分类的固定写法

{% highlight objective-c %}
//假设原来的类叫ClassName要对这个类增加它的新功能
//首先要定义一个头文件 .h文件
#import "ClassName.h"
@interface ClassName (GategoryName)
//方法声明
@end

//在实现的.m文件中
@implementation ClassName (GategoryName)
//方法实现
@end

//调用
int main (int argc, const char *argv[])
{
	Vector *vecA = [[Vector alloc] init];
	Vector *vecB = [[Vector alloc] init];
	id result;
	
	//set the values
	[vecA setVec1: 3.2 andVec2: 4.7];
	[vecB setVec1: 32.2 andVec2: 47.7];
	
	//print it
	[vecA print];
	NSLog(@" - ");
	[vecB print];
	NSLog(@" - ");
	result = [vecA sub: vecB];
	
	//free memory
	[vecA release];
	[vecB release];
	[result release];
	return 0;
}
{% endhighlight %}

**协议**(Protocol) 与java的Interface C++的纯虚函数相同，就是用来声明接口的。协议定义了方法列表，协议不负责实现方法，目的是让别的类实现。协议只有定义部分，没有实现部分，所以没有.m文件，关键字是 @protocol。协议可以继承别的协议，协议中不能定义成员变量。objective-c 只能有一个父类，但可以有多个协议。协议的定义协议的继承、协议的实现，都要把要实现的协议名放到<p1, p2, p3...>中。

#### Objective-C code 1.3 协议示例

{% highlight objective-c %}
//单独在一个.h文件中定义协议，或者可以放到其他头文件中去声明
@protocol Graphics  //协议名
//方法声明,默认是必须实现的
-(void) onDraw;
@optional //可实现的
	- (void) anOptionalMethod;
@required //必须实现的
	- (void) anotherRequireMethod;
@end


#import <Foundation/Foundation.h>
#import "Graphics.h"

#interface Ellipse:NSObject <Graphics>
{
}
@end


#import "Ellipse.h"
@implementation Ellipse
-(void)onDraw
{
	NSLog(@"绘制椭圆形");
}
@end


int main (int argc, const char *argv[])
{
	id graphics; //或者用 id<Graphics> 范型
	graphics = [[Ellipse alloc] init];
	[graphics onDraw];
	[graphics release];
	
	graphics = [[Triangle alloc] init];
	[graphics onDraw];
	[graphics release];
	
	return 0;
}
{% endhighlight %}

**协议比较特殊，一般情况下声明称ID类型，不能声明成已知类型协议**

协议中定义的方法：一种是必须实现的方法 @required，一种是可以实现的方法 @optional，默认是必须实现的。

**两个非常重要的协议**：NSCopying 和 NSCoding 例如NSArray 已经实现了NSCopying 和 NSCoding这两个协议。

NSCopying协议 为了得到一个对象，可以自己创建一个，也可以从别的对象复制过来，一个对象能够被复制给别的对象使用，是因为它实现了NSCopying 这个协议。这个协议里只有一个方法：- (id) copyWithZone:(NSZone *)zone; 这个协议的实现方法比较固定。实现NSCopying协议时，类必须实现copyWithZone:方法，来响应copy消息。(这条消息只是将带有nil参数的copyWithZone:消息发送给你的类)

#### Objective-C code 1.4 NSCopying协议的实现

{% highlight objective-c %}
@protocol NSCopying
- (id)copyWihtZone:(NSZone *)zone;
@end


- (id)copyWithZone:(NSZone *)zone
{
	Student *stu = [[Student allocWithZone:zone] initWithName:self.name Age:self.age];
	
	return stu;
}


//对应main函数中，假设已经有了一个Student对象stu1
Student *stu2 = [stu1 copy];
//实现stu2是stu1的副本，这里是深拷贝，stu1和stu2对应不同的内存

{% endhighlight %}

如果你的类产生了子类，那么copyWithZone:方法也将被继承。Student *stu = [[Student allocWithZone:zone] init];	该方法应该改为 Student *stu = [[[slef class] allocWithZone:zone] init];如果编写一个类的copyWithZone:方法，那么子类的方法应该先调用父类的copy方法以复制继承来copy实例变量。

NSCoding协议 可序列化问题。对象能够存到文件里(序列化)，从文件里读取数据到对象(反序列化)， 这个对象必须实现NSCoding这个协议。 - (void) initWtihCoder:(NSCoder *)decoder; - (void) encodeWithCoder:(NSCoder *)encoder;

