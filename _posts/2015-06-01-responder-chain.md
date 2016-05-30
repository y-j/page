---
layout:     post
title:      iOS事件的传递、响应者链
category: blog
description: 很多类型的事件的传递都依赖于响应者链，它始于第一响应者终于Application对象。如果第一响应者不能处理这个事件，它会将事件传递给响应者链中的下一个响应者。
---
* 响应者链条：是由多个响应者对象连接起来的链条
* 作用：清楚的看见每个响应者之间的联系，并且可以让一个事件由多个对象处理。
* 响应者对象：有响应和处理事件能力的对象。

很多类型的事件的传递都依赖于响应者链，它始于第一响应者终于Application对象。如果第一响应者不能处理这个事件，它会将事件传递给响应者链中的下一个响应者。

第一响应者是一个可以响应并处理事件的对象Event Object，该对象包含了事件处理所必须的一些信息。UIResponder是所有响应者对象的基类，它定义了一些通用的接口包括事件的处理和普遍的响应者行为，UIApplication，UIcontroller，和UIview类的实例都是响应者，不过因为CALayers继承的是NSObject，所以它不是响应者。
一个响应者要成为第一响应者要经过两件事：

1. 覆盖`- (BOOL)canBecomeFirstResponder;`返回YES
1. 收到`- (BOOL)becomeFirstResponder;`消息
> 指定第一响应者对象前要确认app已经被建立，比如典型的条用`becomeFirstRespnder`方法在`viewDidAppear:`中，如果在`viewWillAppear:`指定第一响应者，那么对象绘制还没有被建立，以至于`becomeFirstResponder`方法会返回NO。

事件并不是唯一依赖响应者链的对象，响应者链通常被使用在下面几种情况：

* **触摸事件：**对于触摸事件，window对象会尝试着首先将事件传递给触摸事件发生点的View。这个View被视为“命中测试view” (hit-test view)。寻找“命中测试view”的过程被称作“命中测试”，如果命中测试view不能处理触摸事件，这个事件就会被传递到响应者链中的命中view的上一个响应者。
* **手势事件：**使用UIKit处理震动手势事件，第一响应者要么必须实现`UIResponderd`的`motionBegain：withEvent:`方法要么实现`motionEnded：withEvent：`方法。
* **远程控制事件：**为了去处理“远程控制事件”，第一响应者必须实现UIResponderd的`remoteControlReceivedWithEvent:`方法。
* **行为消息：**当用户操作一个控制，例如一个button或者switch，在行为方法的目标是nil时，这个消息将会被通过响应者链从控制器view开始发送。
iOS使用**“命中测试”**（hit-testing）去寻找触摸发生下的view。**命中测试**会执行检测判断是否该触摸点发生在某个具体的view的相对边界之内。如果检测是的，它就会递归的去检测该view的所有子view。该view的层级最底端view包含触摸点，它就成为了**“命中测试view”。**之后iOS就会决定谁是**命中测试view**,并且递交触摸事件给它处理。
一个事件会游走于指定的传递路径很长的时间直到它被处理。首先，这个单例 UIApplication 对象会从事件队列顶部拿出一个事件对象用于分发处理。比较典型的就是，它会发送这个事件给 Window 对象，Window对象会递交事件给原始对象 initial object去处理。initial object取决于事件的类型。
如果initial object（初始对象）命中测试view或者第一响应者不能处理该事件，UIKit会递交事件给响应者链中的下一个响应者。每一个响应者都有权决定它是否想要去处理该事件或者是继续递交给它自己的下一个响应者通过调用nextResponder方法。这个过程将会持续到某个响应者处理该事件或者没有更多的响应者为止。
响应者链队列始于iOS侦测事件并且递交她给initial对象，那是一个典型的view。这个initialview具备优先处理事件的权利，如图显示了两个不同的应用构造的两个不同的事件传递路径。一个app的事件传递路径取决于它自身的构造，但是所有的事件传递都遵循相同的传递规则。
![](/images/responderChain1.png)
对于左边的app，事件传递按下面的路径：
1.  Initial view 尝试着去处理事件或者消息。如果不能处理事件，它就递交事件给superview,因为这个initial view并不是视图控制器层级中得顶级view.
1.  这个superview尝试去处理该事件，如果superview不能处理该事件，它就递交事件给它的父view，因为它也不是view层级的顶级view。
1.  视图控制器的顶级view尝试着去处理该事件，如果连顶级view都不能处理该事件，它就递交事件给它的controller。
1.  这个viewcontroller尝试着去处理该事件，并且如果它不能处理该事件，它就会递交事件给window。
1.  如果window不能处理该事件，它就递交事件给singlegon app object（既UIApplication）
1.  如果连application都不能处理该事件，那么毫无疑问该事件将会被丢弃。

右边的应用传递按照稍微不同的路径，但是所有的事件传递都遵循相同的传递规则：

1.  一个view controller层级中得view向上递交事件知道它到达顶级view。
2.  顶级view递交事件给它的controller。
3.  Viewcontroller递交事件给它的顶级view的superview，步骤1-3重复直到它到达rootcontroller。
4.  这个rootviewcontroller递交事件给window对象。
5.  window对象递交事件给application对象。