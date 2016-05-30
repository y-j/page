---
layout:     post
title:      Objective-C中单例模式的实现方式
category: blog
description: 单例模式是设计模式中最简单的形式之一。这一模式的目的是使得类的一个对象成为系统中的唯一实例，从而确保所有对象都访问这个唯一实例，不过单例对象一旦建立，对象指针是保存在静态区的，单例对象在堆中分配的内存空间，会在应用程序终止后才会被释放。
---

单例模式是设计模式中最简单的形式之一。这一模式的目的是使得类的一个对象成为系统中的唯一实例，从而确保所有对象都访问这个唯一实例，不过单例对象一旦建立，对象指针是保存在静态区的，单例对象在堆中分配的内存空间，会在应用程序终止后才会被释放。

实现单例模式通常有两种方式：

##### 1. 普通方式

Test1.h文件

```
@interface Test1 : NSObject

+ (instancetype)sharedTest1;

@end
```

Test1.m文件

```
@implementation Test1

static Test1 *_test1;

+ (instancetype)sharedTest1 {
    @synchronized(self) {
        if (_test1 == nil) {
            _test1 = [[self alloc] init];
        }
    }
    return _test1;
}

+ (instancetype)allocWithZone:(struct _NSZone *)zone {
    @synchronized(self) {
        if (_test1 == nil) {
            _test1 = [super allocWithZone:zone];
        }
    }
    return _test1;
}

- (id)copyWithZone:(NSZone *)zone {
    return _test1;
}
```

`static Test2 *_test2;`全局静态变量是显式用`static`修饰的全局变量，作用域是声明此变量所在的文件，其他的文件即使用`extern`声明也不能使用，这样避免了在其他文件用`extern`声明后更改。由于静态变量是静态存储的，对它进行初始化后存储空间只分配一次，所以变量的值在整个程序运行期间都存在。

使用`@synchronized(self)`加锁避免多线程时同时访问造成`_test1`被重复创建。

`allocWithZone`方法是对象分配内存空间时，最终会调用的方法，重写该方法，保证只会分配一个内存空间。

覆写`copyWithZone`方法时，保证对象在使用`copy`方法时，对象的唯一性。

##### 2. 使用GCD方式

Test2.h文件

```
@interface Test2 : NSObject

+ (instancetype)sharedTest2;

@end
```

Test2.m文件

```
@implementation Test2

static Test *_test2;

+ (instancetype)sharedTest2 {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _test2 = [[self alloc] init];
    });
    return _test2;
}

+ (instancetype)allocWithZone:(struct _NSZone *)zone {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _test2 = [super allocWithZone:zone];
    });
    return _test2;
}

- (id)copyWithZone:(NSZone *)zone {
    return _test2;
}

@end
```

`dispatch_once` 是线程安全的，能够做到在多线程的环境下Block中的代码只会被执行一次。

相比这两种方法，第二种显得更简单，推荐使用。

其实创建单例的代码可以抽取成宏，这样可以在项目中更简单的书写单例：

定义一个SingletonClass.h，这里不需要.m文件实现

```
// 定义.h文件
#define SingletonClassH(name) + (instancetype)shared##name; 

// 定义.m文件
#define SingletonClassM(name) \
static id _instance; \
\
+ (instancetype)allocWithZone:(struct _NSZone *)zone { \
static dispatch_once_t onceToken; \
dispatch_once(&onceToken, ^{ \
_instance = [super allocWithZone:zone]; \
}); \
return _instance; \
} \
\
+ (instancetype)shared##name { \
static dispatch_once_t onceToken; \
dispatch_once(&onceToken, ^{ \
_instance = [[self alloc] init]; \
}); \
return _instance; \
} \
\
- (id)copyWithZone:(NSZone *)zone { \
return _instance; \
}
```

使用`##`来连接输入的名字, 使用` \ `在宏定义中换行。
定义后再向写单例就变得很简单了：

Test3.h文件

```
#import "SingletonClass.h"
@interface Test3 : NSObject

SingletonClassH(Test3)

@end
```

Test3.m文件

```
@implementation Test3

SingletonClassM(Test3)

@end
```

最后附上这个小Demo的[地址](https://github.com/y-j/Singleton.git)