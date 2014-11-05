---
layout: post
title: "我的macos编程之旅-Objective C入门"
date: 2014-10-24 08:48:13 +0800
comments: true
categories: macos objective-c
---
<<Objective-C Programming>>这本书属于基础入门书，Part I和Part II的内容是C语言基础，直接略过，直接跳到“Part III Objective-C and Foundation”。由于已经有C/C++编程经验，看书时着重于Objective C和C++的不同之处。

Objective-C是在C语言的基础上添加面向对象特性发展而来，下面就看看Objective-C中的面向对象三大要素。

## 1. 封装

和C++、Java等面向对象语言不同，Objective-C中并没有定义private, protected, public关键字，所有数据成员（instance variable）
都是私有的，无法直接访问。例如如下类定义中，x是不能直接被外部访问的。

```objc
@interface Foo : NSObject
{
    double x;
}

@property int y;

-(int) f:(int)x;
-(int) g:(int)x:(int)y;

@end

@implementation Foo
@synthesize y;
-(int) f:(int)x
{
}

-(int) g:(int)x:(int)y
{
}

@end
```
要访问类的数据成员，必须通过类的成员方法，最常见的就是称之为getter和setter的方法。为了简化存取方法，Objective-C引入了property概念。比如上面的代码中:

```objc
@property int y;
```
定义了属性y，并在实现文件中使用如下代码:
```objc
@synthesize y;
```

上面的语句相当于为y定义了getter和setter方法，比如你可以通过y()返回属性值，setY()设置属性值。

同样的，成员方法也没有共有私有之分，所有成员方法都可以被外部访问。上述代码中成员方法前的“-”表示这是一个实例方法，如果为“+”表示类方法，等价于C++中的静态成员函数。如果要为类定义一个方法，又不希望被外部调用，该如何做到呢？解决的方法是不在头文件(.h)中定义，直接在实现文件(.m)中加入成员函数。是的，没错，Objective-C并不要求在头文件中列出所有的成员方法。

## 2. 继承

和C++不同的是，Objective-C是单根继承体系，所有的类都直接或间接继承自NSObject（注：Objective-C语言规范并没有规定这个根类的名字），这一点和Java类似。同样的，Objective-C不支持多继承。和Java语言中的接口类似的东西在Objective-C中被称作协议(protocol)。协议是一个方法声明的列表，有些方法是必须实现的，有些方法可以定义为可选的。和类一样，协议可以继承其它的协议。一个Objective-C类可以实现多个协议。值得一提的是，协议的名字可以和类名一样，比如NSObject是一个类，同时还存在一个名为NSObject的协议。因为采用了不同的关键字，所以编译器能够区分。比如下面的代码中，Foo继承NSObject类，同时实现了NSObject协议：

```objc
@interface Foo : NSObject <NSObject>
```
在C++和Java中，父类可以控制成员方式是否被子类覆盖（通过virtual/final关键字)，在Objective-C中，只要子类的成员方法和父类完全一样（包括参数个数及类型），就会覆盖父类的成员方法。

## 3. 多态

Objective-C支持多态，而且不像C++那么复杂，比如C++中，如果使用栈变量实例，是无法达到多态效果的。关于Objective-C的多态，可通过下面的代码实例说明：

```objc
@interface Person : NSObject {
    NSString *name;
}
-(void)setName:(NSString*)name;
@end

@implementation Person
-(void)setName:(NSString*)nameValue {
    name = nameValue;
    NSLog(@"Person's name:%@", name);
}
@end

@interface Student : Person
@end

@implementation Student
-(void)setName:(NSString*)nameValue {
    name = nameValue;
    NSLog(@"Student's name:%@", name);
}
@end

int main(int argc, const char* argv[]) {
    @autoreleasepool {
        Person* person = [[Student alloc]init];
        [person setName:@"Student A"];
    }

    return 0;
}
```

## 4. 小结

Objective-C支持面向对象的编程，同时又没有C++的多继承、虚继承等复杂的概念，如果之前有过C++/Java等编程经验，会很容易就能理解Objective-C中的面向对象编程，学起来也会非常轻松。
