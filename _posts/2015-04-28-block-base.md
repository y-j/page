---
layout:     post
title:      block的使用
category: blog
description: block实际上和其他 变量类似，不过block存储的数据是一个函数体，所以使用block可以像其他标准函数一样传入参数并得到返回值
---
block实际上和其他 变量类似，不过block存储的数据是一个函数体，所以使用block可以像其他标准函数一样传入参数并得到返回值。
想要快速的写出一个block很简单，在Xcode中输入`inlineBlock`就会有代码补全提示

```
<#returnType#>(^<#blockName#>)(<#parameterTypes#>) = ^(<#parameters#>) {
    <#statements#>
};
```

这样只需在`=`左边填上返回值类型`returnType`，`block`的名称`blockName`，参数类型`parameterTypes`，=右边填上参数`parameters`，和想要执行的代码`statements`（如果有返回值，`return`返回值，如果没有可不写`return`）

```
int (^sumBlock)(int, int) = ^(int a, int b) {
        return a + b;
    };
```

这是一个简单的block，各种参数都有，再比如

```
 void (^testBlock)() = ^() { 
        NSLog(@"=============");
    };
```

这是没有传入任何参数的情况，等号左边的传入参数类型的()不可省，等号右边如果没有参数，()可写可不写。

```
__block int m = 10;
void (^modifyValueBlock)() = ^() {
        m = 5;
};
```

block内部可以访问外面的变量，但是默认情况下block内部不能修改外面的局部变量，通过给局部变量加上`__block`关键字，这样局部变量就可以在block内部修改.不过对于全局变量不仅可以使用而且还可以改变。

有时候block的类型是规定的，可以通过typedef来定义block类型`typedef int (^MakeBlock)(int, int);`，这样可以很方便的创建block

```
MakeBlock sum = ^ (int a, int b) {
    return a + b;
};
    
MakeBlock minus = ^(int a, int b) {
    return a - b;
};
```

这样以后直接可以使用`MakeBlock`来定义`block`变量。

**block的通常使用场景**

* block作为属性对象，在适当的时候调用

```
 @interface Person : NSObject

 @property (nonatomic, copy) void(^studyBlock)();

 @end
```

在合适的地方给`studyBlock`赋值，需要调用的时候调用：

```
Person *p = [[Person alloc] init];
p.studyBlcok = ^{
        NSLog(@"studyBlcok reach");
};
p.studyBlcok();
```

* block作为方法的参数使用，在方法内部调用，实现在外部

```
 typedef void(^makeBlcok) ();
 @interface Person : NSObject

 - (void)study:(void (^)())block;
 // - (void)study:(makeBlcok)block;

 @end
 @implementation Person

 - (void)study:(void (^)())block {
        block(); // 在外部传入定义好类型的block后，在这执行。
 }

 @end
```

传入的参数类型为block类型，所有也可以给类型起个别名使用

```
        void(^typeBlock)() = ^() {
            NSLog(@"study reach");
        };
        [p study:typeBlock];
        // 或者
        [p study:^{
            NSLog(@"study reach");
        }];
```

这样传入制定类型的block，内部执行

* block作为方法的返回值，目的就是为了代替方法，在内部实现

```
 @interface Person : NSObject

 - (void (^)())work;

 @end

 @implementation Person

 - (void (^)())work {
    return ^{
        NSLog(@"work reach");
    };
 }

 @end
```

在返回block中执行代码

```
// p.work(); 
void(^ returnBlock)() = p.work;
returnBlock(); // 这两种效果一样
```

**block的递归调用**

block想要递归调用，block变量必须是全局变量或者是静态变量，这样在程序启动的时候代码块变量就初始化了，可以递归调用

* 使用静态变量

```
 - (void)testBlock {
    static void (^ const block)(int) = ^(int i) {
        if (i > 0) {
            NSLog(@"value = %d", i);
            block(i - 1);
        }
    };
    block(9);
}
```

如果将static去掉，那么在运行时就会出错，因为block在被函数块引用时是未初始化值，所以调用它的话就访问了无效地址，或者说所要执行的指令是未定义的。

* 作为全局变量

```
void (^ block)(int) = ^(int i) {
    if (i > 0) {
        NSLog(@"value = %d", i);
        block(i - 1);
    }
};
```

当函数块引用是全局的或static的，即函数块内所引用的函数块引用变量的值在初始时就已经确定的，才可以使用递归。

* block作为类的属性时使用copy修饰
`@property (nonatomic, assign) void (^block)();`默认情况下block是在栈中，随时可能被回收，所以对block做一次copy操作，block的内存就会放在堆里面。