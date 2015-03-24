---
layout: post
title: Objective－C类变量成员变量以及构造方法
description: "objective-c语法的一些特性，以及面向对象特性"
category: personal
tags: [OC, 面向对象, oc语法特性, 构造方法, 类变量, 成员变量]
imagefeature: 
comments: true
mathjax:
---

------

![pitcure](http://tunnyios.github.io/images/2015-03-12.JPG)

充满仪式性的第一次。初探Object-C特性

oc中以@开头得都是Foundation框架得NSString类型的字符串。

oc有3中**数据类型**：基础类型，对象类型，id类型

> * 基础类型不需要加＊，包括int、float、double、和char类型
> *	对象类型指的是类或协议所声明的指针类型必须要加＊
> *	id类型可以表示任 意类型，但一般只表示对象类型

<!--more-->
%i 表示格式化输出int类型  %o 8进制 %#x 16进制; %f 浮点类型会保留6位 %@ 打印对象

**数据类型转换**，总的原则：小存储容量数据类型可以自动转换成大存储容量数据类型。

> * 不同类型数据间按照下面关系的从左到右(从低到高)自动转换
		_Bool、char、short、int、枚举类型->int->long int->long long->float->double->long double

**@interface**部分定义了类名、继承的父类、实现的协议、成员变量和成员方法等信息，主要做定义成员变量和成员方法

**@implementation**部分实现了接口部分定义的成员方法，主要做了两件事：实现成员方法，以及对成员方法的初始化

> * 初始化由它的构造函数来做，所以第一步先定义构造函数，如果没有定义构造函数就是用默认的构造函数，然后再实现方法。

**实例成员变量**、**类变量**、**实例方法**、**类方法**：

> * 实例成员变量：即对象变量，不用static修饰，放在@interface里面。实例变量的初始化用实例构造函数。
> * 类变量：即静态变量，用static休息，放在@interface上面。类变量的初始化用类构造函数。
> * 实例方法：只能被实例个体调用，用－；实例方法可以访问类变量。
> * 类方法：可以被类直接调用，用+；类方法不能访问实例成员变量。

#### Objective-C code 1.0 属性和类方法

{% highlight objective-c %}
static int count;
@interface Song : NSobject{
	@pubilc
	NSString *title;
	NSString *artist;
	long int duration;
}
//操作方法
-(void)start;
-(void)stop;
-(seek):(long int)time;
+(int) initCount;
+(void) initialize;
//访问成员变量的方法
@property(copy,readwrite) NSString *title;
@property(nonatomic,retain) NSString *artist;
@property(readonly) long int duration;
@end


@implementation Song

@synthesize title;   //属性必须用synthesize 组装
@synthesize artist;
@synthesize duration;

-(id) init
{
	self = [super init];
	count++;
	return self;
}

+(int) initCount
{
	return count;
}

+(void) initialize
{
	count = 0;
}

@end

int main( int argc, const char *argv[] ) 
{	ClassA *c1 = [[ClassA alloc] init];	ClassA *c2 = [[ClassA alloc] init];	// print count	NSLog(@"ClassA count: %i", [ClassA initCount] );	ClassA *c3 = [[ClassA alloc] init];	NSLog(@"ClassA count: %i", [ClassA initCount] );	[c1 release];	[c2 release];	[c3 release];		return 0;｝
{% endhighlight %}

从面向对象的封装角度考虑，想要访问类中的成员变量是用通过实例方法访问。成员变量前面要有作用域限定符(protected、public、private 缺省情况是protected)成员变量的访问是通过读取方法(getter)和设定方法(setter)。这三个作用域限定符只能限定成员变量，不能限定类变量，更不能限定实例成员方法。protected限定可以在子类中继承下去；private限定只能在该类中使用,实例成员方法都是public的。

**属性**：使用getter和setter方法，太繁琐。因此提出了属性的概念。在接口定义部分，使用@property关键字，定义属性后就会产生get和set方法(如果不想让人访问，可以不定义属性)属性的调用和成员变量的调用是不一样的，因此通过调用的方式可以看出来调用的是成员变量的方式还是属性的方式。

一般我们把成员变量和属性明明相同，意味着这个属性是为了这个成员变量而封装的。其实属性并不真正保存数据，而是通过属性的set方法，绑定到成员变量里去了，属性是两个方法，通过这两个方法改变成员变量的值。 属性访问用.  成员变量访问用 ->  方法访问用[]

调用方法给成员变量赋值是不会发生内存泄漏和空指针异常的；直接赋值会出问题。**记住一点**：通过方法或者属性来访问成员变量。不要直接使用成员变量，直接使用属性就等于使用了它的set方法。

括号里面是**属性的参数**：跟读写相关的 readonly/writeonly/readwrite; 跟内存管理相关的 copy/retain/assign; 跟线程有关的 nonatomic 非原子性的（*原子是线程同步的，只允许一个线程来访问它，是排它的。事实上是这样的，当多个线程去访问方法的时候，如果是原子性的，会造成性能降低。因此很多情况下定义为非原子的，意思是：为了提高性能，不考虑它的线程同步问题，不考虑线程安全问题，不考虑多个线程同时访问它会造成数据不一致的问题，为了提高性能，牺牲了线程安全，这就是nonatomic*） 

#### Objective-C code 1.1 属性示例

{% highlight objective-c %}
Song *mySong = [[Song alloc] init];

/* OC会自动生成一个下划线的方法来获取title的值*/
mySong.title = @"xxxxx";
self.title = @"xxxxx";
_title = @"xxxxx";			//属性访问
[mySong setTitle: @"xxxx"];
mySong->title = @"xxxx";
//用完之后一定要release
[mySong release];
{% endhighlight %}

**构造方法**：主要目的是初始化成实例员变量。

实例构造函数：创建实例(以init开头)返回自身类型的指针，在.h中定义(分配内存实例化，然后构造)，实例构造函数也是模式代码 **如code1.2**

类构造函数：类构造函数是特定写法*+ (void) initialize{}*，**如code1.0**且**只有一个**。他在类第一次访问的时候被自动调用，因此一般用来初始化类变量，并且**只调用一次**,调用时它会先于实例构造函数执行。

每一个类都会有2个初始化方法，一个是实例初始化的方法，另一个也是可以初始化实例的类方法(以类名＋with开头)。实例初始化函数需要写alloc，并且使用完后需要 release ； 用类方法初始化实例成员变量不用写alloc函数，使用完后会被自动释放池释放。不需要手动release。

**如code1.0** init方法是默认构造方法,在这个构造方法中累计类变量 count,在实例方法中可以访问类变量的,但是类方法不能 访问实例变量。initCount 方法是一个普通的类方法,用于返回类变 count, initialize方法是非常特殊的类方法, 它是在类第一次访问时候被自动调用,因此它一般用来初始 化类变量的,类似于C#中的静态构造方法。在第一次实例化ClassA时候会调用两个方法: initialize 类方法和实例构造方法init,然后再次实例化ClassA时候只 是调用实例构造方法init,而没有调用initialize类方法。这样类变量count被一直累加,它隶属类因此c1实例可以 访问,c2和c3都可以访问。

#### Objective-C code 1.2 实例构造方法--模式代码

{% highlight objective-c %}
-(Song *) initWithTile: (NSString *) newTitle andArtist: (NSString *) newArtist andDuration: (long int) newDuration
{
//固定写法，创建父类对象 self相当于this super代表父类
	self = [super init];
	if (self)
	{
		_title = @"xxxx";
		artist = @"xxxx";
		[self setDuration: @"xxxx"];
	}
	
	return self;
}
{% endhighlight %}



