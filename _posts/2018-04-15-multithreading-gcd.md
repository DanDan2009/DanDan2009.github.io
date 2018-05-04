---
layout:     post
title:      "iOS 多线程--GCD 串行队列、并发队列以及同步执行、异步执行"
subtitle:   "GCD中的队列"
date:       2018-04-15 17:00:00
author:     "Dan"
header-img: "img/multi_thread.jpg"
header-mask: 0.3
catalog:    true
tags:
    - iOS
    - 多线程
    - 队列
    - GCD
---

由于水平有限，以下内容不保证全部正确，请用批判性的眼光看以下内容，如发现错误，恳请指正。

### 1 什么是队列（queue）
   在开始GCD之前先来说一下队列的概念，因为GCD的任务都是在队列中派发的；
   队列（queue）：是先进先出（FIFO, First-In-First-Out）的线性表。但是在`队列`前面加上`串行`和`并发`这两个定语之后，也就是`串行队列`、`并发队列`，有时就容易搞不清楚了，特别是再加上`同步`和`异步`的概念之后，有时就更不清楚了。
   


   
### 2 串行队列和并发队列
注意是`并发队列（Concurrent Queue）`，不是`并行队列`，关于`并发`和`并行`的区别见下一节

什么是`串行队列`和`并发队列` 呢？上面已经说了`串行队列`和`并发队列`中的`串行`和`并发`是`队列`的定语，可以加个`的`，`串行的队列`和`并发的队列`；所以`串行队列`和`并行队列`说到底还是`队列`，既然是`队列`，肯定是要先进先出（FIFO, First-In-First-Out）的，记住这一点很重要。

`串行队列`：说明这个队列中的任务要`串行`执行，也就是一个一个的执行，必须等上一个任务执行完成之后才能开始下一个，而且一定是按照先进先出的顺序执行的，比如`串行队列`里面有4个任务，进入队列的顺序是a、b、c、d，那么一定是先执行a，并且等任务a完成之后，再执行b... 。
  
`并发队列`：说明这个队列中的任务可以`并发`执行，也就任务可以同时执行,比如`并发队列`里面有4个任务，进入队列的顺序是a、b、c、d，那么一定是先执行a，再执行b...，但是执行b的时候a不一定执行完成，而且a和b具体哪个先执行完成是不确定的，  具体同时执行几个，由系统控制(GCD中不能直接设置并发数，可以通过创建信号量的方式实现，NSOperationQueue可以直接设置)，但是肯定也是按照先进先出（FIFO, First-In-First-Out）的原则调用的。
  
### 4 关于`并发`和`并行`
并行的英文是parallelism，并发的英文时concurrency ，

1. 并发表示逻辑概念上的同时，并行表示物理概念上的同时。
2. 并发指的是代码的性质，并行指的是物理运行状态
3. 并发是说进程B的开始时间是在进程A的开始时间与结束时间之间，我们就说A和B是并发的。并行指同一时间两个线程运行在不同的cpu。
4. 并发是同时处理很多事情（dealing with lots of things at once），并行是同时执行很多事情（doing lots of things at once）；

5. 并发可认为是一种逻辑结构的设计模式。你可以用并发的设计方式去编写程序，然后运行在一个单核cpu上，通过cpu动态地逻辑切换制造出并行的假象。此时，你的程序不是并行，但是是并发的。如果将并发的程序运行在多核CPU上，此时你的程序可以认为是并行。并行更关注的是程序的执行（execution）；
6. 对于单核CPU来说，并行至少两个CPU才行；而并发一个cpu也可以，两个任务交替执行即可；

综上所述：并发更多的是编写程序上的概念，并行是物理CPU执行上的概念。并发可以用并行的方式实现。并发是从编程的角度来解释的，并行是从cpu执行任务的角度来看的，一般来说我们只能编写并发的程序，却无法保证编写出并行的程序。

可以把并发和并行当成不同维度的东西。并发是从程序员编写程序的角度来看的。并行是从程序的物理执行上来看的。


Erlang 的发明者 Joe Armstrong 在他的一篇博文 (http://joearms.github.io/2013/04/05/concurrent-and-parallel-programming.html)  中提到如何向一个 5 岁的小孩去介绍并发和并行的区别
![](/img/15248975776264.jpg)


### 同步和异步
  
GCD中的`同步`和`异步`是针对任务的执行来说的，也就是同步执行任务和异步执行任务。 同步或异步描述的是task与其上下文之间的关系
  
同步执行：可以理解为，调用函数时(或执行一个代码块时)，必须等这个函数（或代码块）执行完成之后才会执行下面的代码。
同步执行 一般在当前线程中执行任务，不会开启新的线程。

异步：不管调用的函数有没有执行完，都会继续执行下面的代码。具备开启新线程的能力。

同步和异步的主要区别是向队列里面添加任务时是立即返回还是等添加的任务完成之后再返回。

```
dispatch_sync(dispatch_queue_t queue, dispatch_block_t block);
dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
```

dispatch_sync就是添加同步任务的，添加任务的时候，必须等block里面的代码执行完，dispatch_sync这个函数才能返回。

dispatch_async是添加异步任务的，添加任务的时候会立即返回，不管block里面的代码是否执行。
  
  
### 测试
1. 串行队列异步任务
以下代码均是在viewDidLoad方法中执行的

```
dispatch_queue_t serialQueue = dispatch_queue_create("Dan-serial", DISPATCH_QUEUE_SERIAL);
    for(int i = 0; i < 5; i++){
        dispatch_async(serialQueue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
            [NSThread sleepForTimeInterval: i % 3];
        });
    }
输出如下：    
我开始了：0 , <NSThread: 0x60400027e480>{number = 3, name = (null)}
我开始了：1 , <NSThread: 0x60400027e480>{number = 3, name = (null)}
我开始了：2 , <NSThread: 0x60400027e480>{number = 3, name = (null)}
我开始了：3 , <NSThread: 0x60400027e480>{number = 3, name = (null)}
我开始了：4 , <NSThread: 0x60400027e480>{number = 3, name = (null)}
```
可以看到是按顺输出的，是在同一个线程，而且开启了新线程，

2. 串行队列同步任务

```
for(int i = 0; i < 5; i++){
        dispatch_sync(serialQueue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
            [NSThread sleepForTimeInterval: i % 3];
        });
    }  
输出如下：    
我开始了：0 , <NSThread: 0x60000006d8c0>{number = 1, name = main}
我开始了：1 , <NSThread: 0x60000006d8c0>{number = 1, name = main}
我开始了：2 , <NSThread: 0x60000006d8c0>{number = 1, name = main}
我开始了：3 , <NSThread: 0x60000006d8c0>{number = 1, name = main}
我开始了：4 , <NSThread: 0x60000006d8c0>{number = 1, name = main}
```
可以看到是按顺输出的，是在同一个线程，但是没有开启新线程，是在主线程执行的


3. 并发队列异步任务

```
    dispatch_queue_t concurrent_queue = dispatch_queue_create("DanCONCURRENT", DISPATCH_QUEUE_CONCURRENT);
    for(int i = 0; i < 5; i++){
        dispatch_async(concurrent_queue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
            [NSThread sleepForTimeInterval: i % 3];
            NSLog(@"执行完成：%@ , %@",@(i),[NSThread currentThread]);
        });
    }
输出如下：
我开始了：0 , <NSThread: 0x600000462340>{number = 3, name = (null)}
我开始了：2 , <NSThread: 0x604000269380>{number = 6, name = (null)}
我开始了：3 , <NSThread: 0x604000269180>{number = 5, name = (null)}
我开始了：1 , <NSThread: 0x600000461d80>{number = 4, name = (null)}
执行完成：0 , <NSThread: 0x600000462340>{number = 3, name = (null)}
执行完成：3 , <NSThread: 0x604000269180>{number = 5, name = (null)}
我开始了：4 , <NSThread: 0x600000462340>{number = 3, name = (null)}
执行完成：1 , <NSThread: 0x600000461d80>{number = 4, name = (null)}
执行完成：4 , <NSThread: 0x600000462340>{number = 3, name = (null)}
执行完成：2 , <NSThread: 0x604000269380>{number = 6, name = (null)}
```
可以看到，是并发执行的，而且开启了不止一个新线程。
这里有没有发现什么不对的地方呢？执行完成的顺序不确定是可以理解的，但是开始的顺序为什么也不确定呢？根据上面说的，队列是先进先出的，那么`我开始了`应该按照顺序打印才对，但是实际打印是无序的，为什么？这个问题暂时还没搞清楚，我的猜测可能是NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);这个操作比较耗时导致的。

4. 并发队列同步任务
  
```
 for(int i = 0; i < 5; i++){
        dispatch_sync(concurrent_queue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
            [NSThread sleepForTimeInterval: i % 3];
            NSLog(@"执行完成：%@ , %@",@(i),[NSThread currentThread]);
        });
    }
    
输出如下：
我开始了：0 , <NSThread: 0x60000007ec80>{number = 1, name = main}
执行完成：0 , <NSThread: 0x60000007ec80>{number = 1, name = main}
我开始了：1 , <NSThread: 0x60000007ec80>{number = 1, name = main}
执行完成：1 , <NSThread: 0x60000007ec80>{number = 1, name = main}
我开始了：2 , <NSThread: 0x60000007ec80>{number = 1, name = main}
执行完成：2 , <NSThread: 0x60000007ec80>{number = 1, name = main}
我开始了：3 , <NSThread: 0x60000007ec80>{number = 1, name = main}
执行完成：3 , <NSThread: 0x60000007ec80>{number = 1, name = main}
我开始了：4 , <NSThread: 0x60000007ec80>{number = 1, name = main}
执行完成：4 , <NSThread: 0x60000007ec80>{number = 1, name = main}
```
可以看到，程序没有并发执行，而且没有开启新线程，是在主线程执行的。
有没有觉得奇怪呢？为什么向并发队列添加的任务，没有开启新线程，而是在主线程执行的？
如下解释:
```
使用dispatch_sync 添加同步任务，必须等添加的block执行完成之后才返回。
既然要执行block，肯定需要线程，要么新开线程执行，要么再已存在的线程（包括当前线程）执行。  
dispatch_sync的官方注释里面有这么一句话：
As an optimization, dispatch_sync() invokes the block on the current thread when possible.
作为优化，如果可能，直接在当前线程调用这个block。
  
所以，一般，在大多数情况下，通过dispatch_sync添加的任务，在哪个线程添加就会在哪个线程执行。

上面我们添加的任务的代码是在主线程，所以就直接在主线程执行了。

```
  
### 串行队列里的任务都在一个线程上执行？ 
  测试如下
  ```
  - (void)viewDidLoad {
      dispatch_queue_t serialQueue = dispatch_queue_create("Dan-serial", DISPATCH_QUEUE_SERIAL);

    dispatch_sync(serialQueue, ^{
        // block 1
        NSLog(@"current 1: %@", [NSThread currentThread]);
    });

    dispatch_sync(serialQueue, ^{
        // block 2
        NSLog(@"current 2: %@", [NSThread currentThread]);
    });

    dispatch_async(serialQueue, ^{
        // block 3
        NSLog(@"current 3: %@", [NSThread currentThread]);
    });

    dispatch_async(serialQueue, ^{
        // block 4
        NSLog(@"current 4: %@", [NSThread currentThread]);
    });
}
    //结果如下
    //    current 1: <NSThread: 0x600000071600>{number = 1, name = main}
//    current 2: <NSThread: 0x600000071600>{number = 1, name = main}
//    current 3: <NSThread: 0x60400027bcc0>{number = 3, name = (null)}
//    current 4: <NSThread: 0x60400027bcc0>{number = 3, name = (null)}
  ```
可以看到，向串行队列添加的同步任务在主线程执行的，和上面的结论一致(通过dispatch_sync添加的任务，在哪个线程添加就会在哪个线程执行)。
异步任务在新开的线程执行的，而且只开了一个线程
 
 再做如下测试：  
  
  ```
  - (void)viewDidLoad {
      dispatch_queue_t queue = dispatch_queue_create("Dan", NULL);
       dispatch_async(queue, ^{
        NSLog(@"current : %@", [NSThread currentThread]);
        dispatch_queue_t serialQueue = dispatch_queue_create("Dan-serial", DISPATCH_QUEUE_SERIAL);

        dispatch_sync(serialQueue, ^{
            // block 1
            NSLog(@"current 1: %@", [NSThread currentThread]);
        });

        dispatch_sync(serialQueue, ^{
            // block 2
            NSLog(@"current 2: %@", [NSThread currentThread]);
        });

        dispatch_async(serialQueue, ^{
            // block 3
            NSLog(@"current 3: %@", [NSThread currentThread]);
        });

        dispatch_async(serialQueue, ^{
            // block 4
            NSLog(@"current 4: %@", [NSThread currentThread]);
        });
    });
  }
// 结果如下
//    current  : <NSThread: 0x604000263440>{number = 3, name = (null)}
//    current 1: <NSThread: 0x604000263440>{number = 3, name = (null)}
//    current 2: <NSThread: 0x604000263440>{number = 3, name = (null)}
//    current 3: <NSThread: 0x604000263440>{number = 3, name = (null)}
//    current 4: <NSThread: 0x604000263440>{number = 3, name = (null)}

```

可以看到：  

* 在主线程向自定义的串行队列添加的同步任务，直接在主线程执行
* 在主线程向自定义的串行队列添加的异步任务，会开一个新线程
  
* 在非主线程向自定义的串行队列添加的同步任务，直接在当期线程执行
* 在非主线程向自定义的串行队列添加的异步任务，直接在当期线程执行
  
结论：使用dispatch_sync函数添加到serial dispatch queue中的任务，其运行的task往往与所在的上下文是同一个thread；使用dispatch_async函数添加到serial dispatch queue中的任务，一般会(不一定)新开一个线程，但是不同的异步任务用的是同一个线程。
     
     
#### 测试:    
1. 主线程只会执行主队列的任务？ 
   不是的,如上

2. 以下代码的执行结果是什么？为什么？

```
- (void)viewDidLoad {
    [super viewDidLoad];
    dispatch_queue_t queue = dispatch_queue_create("com.dan.queue", DISPATCH_QUEUE_SERIAL);
    dispatch_sync(queue, ^{
        NSLog(@"current thread = %@", [NSThread currentThread]);
        dispatch_sync(dispatch_get_main_queue(), ^{
            NSLog(@"current thread = %@", [NSThread currentThread]);
        });
    });    
}
```
输出`current thread = <NSThread: 0x60000006d600>{number = 1, name = main}`，然后发生死锁。
原因：使用dispatch_sync向串行队列添加任务，会在当前线程执行，而当前线程就是主线线程，所以第一个NSLog输出，由于第一个dispatch_sync的 block代码是在主线程执行的，所以第二个dispatch_sync相当于如下写法，所以会发生死锁，如果不明白为什么，找Google。
```
- (void)viewDidLoad {
dispatch_sync(dispatch_get_main_queue(), ^{
    NSLog(@"current thread = %@", [NSThread currentThread]);
        });
}
```
会发生死锁，


  

#### 疑问
```
- (void)viewDidLoad {
    dispatch_queue_t concurrent_queue = dispatch_queue_create("Dan——CONCURRENT", DISPATCH_QUEUE_CONCURRENT);
    for(int i = 0; i < 10; i++){
        dispatch_async(concurrent_queue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
        });
    }
}
//运行结果如下
//我开始了：3 , <NSThread: 0x60000026e240>{number = 6, name = (null)}
//我开始了：1 , <NSThread: 0x60400027a440>{number = 4, name = (null)}
//我开始了：2 , <NSThread: 0x60000026f800>{number = 5, name = (null)}
//我开始了：0 , <NSThread: 0x60400027a400>{number = 3, name = (null)}
//我开始了：4 , <NSThread: 0x60000026e240>{number = 6, name = (null)}
//我开始了：5 , <NSThread: 0x60400027a440>{number = 4, name = (null)}
//我开始了：8 , <NSThread: 0x60000026e980>{number = 9, name = (null)}
//我开始了：7 , <NSThread: 0x60000026e800>{number = 8, name = (null)}
//我开始了：6 , <NSThread: 0x60000026e8c0>{number = 7, name = (null)}
//我开始了：9 , <NSThread: 0x60400027a280>{number = 10, name = (null)}
```

为什么不按顺序开始？并发队列也是队列，队列应该是先进先出，虽然执行结束的顺序不确定，但是开始的时候应该是确定的啊

```
- (void)viewDidLoad {
    dispatch_queue_t concurrent_queue = dispatch_queue_create("Dan——CONCURRENT", DISPATCH_QUEUE_CONCURRENT);
    for(int i = 0; i < 10; i++){
        dispatch_async(concurrent_queue, ^{
            NSLog(@"我开始了：%@ , %@",@(i),[NSThread currentThread]);
        });
    }
    NSLog(@"");
}
//运行结果如下
//我开始了：0 , <NSThread: 0x60000027d200>{number = 3, name = (null)}
//我开始了：1 , <NSThread: 0x60000027d740>{number = 4, name = (null)}
//我开始了：2 , <NSThread: 0x60000027d200>{number = 3, name = (null)}
//我开始了：3 , <NSThread: 0x60000027d740>{number = 4, name = (null)}
//我开始了：4 , <NSThread: 0x60000027d200>{number = 3, name = (null)}
//我开始了：5 , <NSThread: 0x60000027d740>{number = 4, name = (null)}
//我开始了：6 , <NSThread: 0x60000027d200>{number = 3, name = (null)}
//我开始了：7 , <NSThread: 0x60000027d740>{number = 4, name = (null)}
//我开始了：8 , <NSThread: 0x60000027d200>{number = 3, name = (null)}
//我开始了：9 , <NSThread: 0x60000027d740>{number = 4, name = (null)}
```

加了NSLog(@"");之后就按顺序开始了，为什么？如果你知道，请不吝赐教。



