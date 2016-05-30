---
layout:     post
title:      iOS中多线程的几种方式
category: blog
description: 使用线程可以把占据时间长的程序中的任务放到后台去处理，在一些等待的任务实现上如用户输入、文件读写和网络收发数据等，线程就比较有用了。在这种情况下可以释放一些珍贵的资源如内存占用等等
---
## 多线程

首先CPU在同一时间只能处理一条线程，多线程的并发执行是指CPU快速的在不同线程之间切换，由于调度的时间很快，造成了一种多条线程同时执行的假象。

- 多线程的优点：
	+ 可以在一定程度上提高程序的执行效率
	+ 适当的提到系统资源的利用率
- 多线程的缺点：
	+ 程序设计的会很复杂，线程之间的通信，数据传递会很麻烦。
	+ 开启线程需要占用一定的内存空间，如果开启了大量的线程，会占用大量的内存空间（主线程默认是1MB，子线程则是512KB，占用的内存空间可以更新，不过通常采取默认），线程开启的过多时，CPU调度的开销也会很大。

## 主线程
一个程序启动后，默认会开启一条线程，称为主线程，主要用来显示、刷新UI界面、处理UI事件。像是一些耗时的操作不要放在主线程中，否则会卡住主线程，影响UI的流畅。

> iOS中常见的四种多线程方案

	pthread:基于C语言的跨平台API,不过很少用，手动管理生命周期
	NSThread:基于OC，面向对象，简单易用，手动管理生命周期
	GCD:基于C语言，能充分利用多核，而且自动管理生命周期
	NSOperation:基于OC对GCD的封装，面向对象，功能丰富，自动管理生命周期，和GCD一样经常使用

## NSThread
* 创建线程


```
 NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(test) object:nil];// 通过创建NSThread对象创建一条线程,object:传入`test`函数的参数，这里传入为空
[thread start];// 开启线程
```

可以通过`+ (NSThread *)mainThread`获得主线程。

还可以直接创建并启动线程，这样使用简单，不过不可以对线程做具体设置

```objc
[NSThread detachNewThreadSelector:@selector(test) toTarget:self withObject:nil];

- (void)performSelectorInBackground:(SEL)aSelector withObject:(nullable id)arg NS_AVAILABLE(10_5, 2_0);
```

* 线程的状态

```
- (void)start;// 进入运行状态，在入伍执行完毕的时候，线程自动进入死亡状态。
+ (void)sleepUntilDate:(NSDate *)date;// 暂停
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;// 暂停
+ (void)exit;// 进入死亡状态
```
	
* 解决安全隐患的互斥锁
`@synchronized(对象) { // 锁定代码  }`
加锁能防止多线程同时访问数据造成的数据错乱，不过会消耗大量的CPU资源，互斥锁是以线程同步让多条线程在同一条线上按顺序执行任务。

* 线程间的通信

在两个线程间需要传递数据，或者一个线程执行完任务后，需要在另一个线程继续执行任务

```
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait modes:(nullable NSArray<NSString *> *)array;// 数组中存放Runloop的模式
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait;
	// equivalent to the first method with kCFRunLoopCommonModes(和前一个方法的kCFRunLoopCommonModes模式相等)
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait modes:(nullable NSArray<NSString *> *)array NS_AVAILABLE(10_5, 2_0);
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait NS_AVAILABLE(10_5, 2_0);
	// equivalent to the first method with(和前一个方法的kCFRunLoopCommonModes模式相等) kCFRunLoopCommonModes
		
```