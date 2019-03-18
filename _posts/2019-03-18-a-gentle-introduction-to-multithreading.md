---
layout:     post  
title:      A gentle introduction to multithreading.
subtitle:   浅议多线程技术
date:      2019-03-18 21:40:00
author:     "Dan"
header-img: "img/a-gentle-introduction-to-multithreading.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---

## A gentle introduction to multithreading
浅议多线程技术

Approaching the world of concurrency, one step at a time.
一步一步地接近并发的世界。

Modern computers have the ability to perform multiple operations at the same time. Supported by hardware advancements and smarter operating systems, this feature makes your programs run faster, both in terms of speed of execution and responsiveness.
现代计算机具有同时进行多种操作的能力。在硬件改进和智能操作系统的支持下，该特性使程序运行得更快，无论是在执行速度还是响应速度方面。

Writing software that takes advantage of such power is fascinating, yet tricky: it requires you to understand what happens under your computer's hood. In this first episode I'll try to scratch the surface of threads, one of the tools provided by operating systems to perform this kind of magic. Let's go!
编写利用这种强大功能的软件很吸引人，但也很棘手:它要求您了解在您的计算机背后发生了什么。在这篇文章中，我将尝试触及线程的表面，这是操作系统提供的一种工具，用于执行这种神奇的功能。我们开始吧!

###  Processes and threads: naming things the right way
进程和线程:以正确的方式命名

Modern operating systems can run multiple programs at the same time. That's why you can read this article in your browser (a program) while listening to music on your media player (another program). Each program is known as a process that is being executed. The operating system knows many software tricks to make a process run along with others, as well as taking advantage from the underlying hardware. Either way, the final outcome is that you sense all your programs to be running simultaneously.
现代操作系统可以同时运行多个程序。这就是为什么您可以在浏览器(一个程序)中阅读本文，同时在媒体播放器(另一个程序)上听音乐。每个程序都被称为正在执行的进程。操作系统知道许多软件技巧，可以使进程与其他进程一起运行，也可以利用底层硬件。无论哪种方式，最终的结果是您感觉到所有程序都在同时运行。

Running processes in an operating system is not the only way to perform several operations at the same time. Each process is able to run simultaneous sub-tasks within itself, called threads. You can think of a thread as a slice of the process itself. Every process triggers at least one thread on startup, which is called the main thread. Then, according to the program/programmer's needs, additional threads may be started or terminated. Multithreading is about running multiple threads withing a single process.
在操作系统中运行进程并不是同时执行多个操作的唯一方法。每个进程都能够在自己内部同时运行子任务，称为线程。您可以将线程看作进程本身的一部分。每个进程在启动时至少触发一个线程，称为主线程。然后，根据程序/程序员的需要，可以启动或终止其他线程。多线程是指在一个进程中运行多个线程。

For example, it is likely that your media player runs multiple threads: one for rendering the interface — this is usually the main thread, another one for playing the music and so on.
例如，您的媒体播放器可能运行多个线程:一个用于呈现接口—这通常是主线程，另一个用于播放音乐，等等。

You can think of the operating system as a container that holds multiple processes, where each process is a container that holds multiple threads. In this article I will focus on threads only, but the whole topic is fascinating and deserves more in-depth analysis in the future.
您可以将操作系统看作一个包含多个进程的容器，其中每个进程都是一个包含多个线程的容器。在本文中，我将只关注线程，但是整个主题非常吸引人，值得在未来进行更深入的分析。

![](/img/15525659768541.jpg)
1. Operating systems can be seen as a box that contains processes, which in turn contain one or more threads.

#### The differences between processes and threads
进程和线程之间的区别

Each process has its own chunk of memory assigned by the operating system. By default that memory cannot be shared with other processes: your browser has no access to the memory assigned to your media player and vice versa. The same thing happens if you run two instances of the same process, that is if you launch your browser twice. The operating system treats each instance as a new process with its own separate portion of memory assigned. So, by default, two or more processes have no way to share data, unless they perform advanced tricks — the so-called inter-process communication (IPC).
每个进程都有由操作系统分配的内存块。默认情况下，内存不能与其他进程共享:浏览器不能访问分配给媒体播放器的内存，反之亦然。如果运行相同进程的两个实例，即两次启动浏览器，也会发生相同的事情。操作系统将每个实例视为一个新进程，分配了自己独立的内存部分。因此，默认情况下，两个或多个进程无法共享数据，除非它们执行高级技巧——所谓的进程间通信(IPC)。

Unlike processes, threads share the same chunk of memory assigned to their parent process by the operating system: data in the media player main interface can be easily accessed by the audio engine and vice versa. Therefore is easier for two threads to talk to eachother. On top of that, threads are usually lighter than a process: they take less resources and are faster to create, that's why they are also called lightweight processes.
与进程不同，线程共享操作系统分配给父进程的相同内存块:media player主界面中的数据可以被音频引擎轻松访问，反之亦然。因此，两个线程之间的对话更容易。最重要的是，线程通常比进程更轻:它们占用的资源更少，创建速度更快，这就是为什么它们也被称为轻量级进程。

Threads are a handy way to make your program perform multiple operations at the same time. Without threads you would have to write one program per task, run them as processes and synchronize them through the operating system. This would be more difficult (IPC is tricky) and slower (processes are heavier than threads).
线程是使程序同时执行多个操作的方便方法。没有线程，您将不得不为每个任务编写一个程序，以进程的形式运行它们，并通过操作系统同步它们。这将更加困难(IPC比较棘手)，速度也会更慢(进程比线程更重)。

#### Green threads, of fibers
绿色的线程--纤程

Threads mentioned so far are an operating system thing: a process that wants to fire a new thread has to talk to the operating system. Not every platform natively support threads, though. Green threads, also known as fibers are a kind of emulation that makes multithreaded programs work in environments that don't provide that capability. For example a virtual machine might implement green threads in case the underlying operating system doesn't have native thread support.
到目前为止所提到的线程都是操作系统问题:希望触发新线程的进程必须与操作系统通信。不过，并不是每个平台都支持线程。绿色线程，也称为纤程，是一种模拟，它使多线程程序在不提供这种功能的环境中工作。例如，虚拟机可能实现绿色线程，以防底层操作系统不支持本机线程。

Green threads are faster to create and to manage because they completely bypass the operating system, but also have disadvantages. I will write about such topic in one of the next episodes.
绿色线程创建和管理起来更快，因为它们完全绕过了操作系统，但也有缺点。我将在下一集中就这个话题写一篇文章。

The name "green threads" refers to the Green Team at Sun Microsystem that designed the original Java thread library in the 90s. Today Java no longer makes use of green threads: they switched to native ones back in 2000. Some other programming languages — Go, Haskell or Rust to name a few — implement equivalents of green threads instead of native ones.
“绿色线程”这个名称是Sun Microsystem的绿色团队提出的，他们在90年代设计了最初的Java线程库。今天，Java不再使用绿色线程:它们早在2000年就切换到了原生线程。其他一些编程语言——Go、Haskell或Rust等等——实现了与绿色线程等价的东西，而不是原生线程。

### What threads are used for
线程是用来做什么

Why should a process employ multiple threads? As I mentioned before, doing things in parallel greatly speed up things. Say you are about to render a movie in your movie editor. The editor could be smart enough to spread the rendering operation across multiple threads, where each thread processes a chunk of the final movie. So if with one thread the task would take, say, one hour, with two threads it would take 30 minutes; with four threads 15 minutes, and so on.
为什么一个进程应该使用多个线程?正如我之前提到的，并行处理可以大大加快速度。假设您要在影片编辑器中呈现影片。编辑器可以足够聪明地将呈现操作分散到多个线程，其中每个线程处理最后影片的一部分。因此，如果用一个线程完成任务需要一个小时，用两个线程完成任务需要30分钟;用四个线程15分钟，以此类推。

Is it really that simple? There are three important points to consider:
真的那么简单吗?有三点需要考虑:

1. not every program needs to be multithreaded. If your app performs sequential operations or often waits on the user to do something, multithreading might not be that beneficial;
不是每个程序都需要多线程。如果您的应用程序执行顺序操作，或者经常等待用户执行某些操作，多线程可能没有那么有益;

2. you just don't throw more threads to an application to make it run faster: each sub-task has to be thought and designed carefully to perform parallel operations;
您不需要向应用程序抛出更多的线程来使其运行得更快:必须仔细考虑和设计每个子任务来执行并行操作;

3. it is not 100% guaranteed that threads will perform their operations truly in parallel, that is at the same time: it really depends on the underlying hardware.
并不能100%保证线程将真正并行地执行它们的操作，也就是说，同时执行:这实际上取决于底层硬件。

The last one is crucial: if your computer doesn't support multiple operations at the same time, the operating system has to fake them. We will see how in a minute. For now let's think of concurrency as the perception of having tasks that run at the same time, while true parallelism as tasks that literally run at the same time.
最后一点很重要:如果你的电脑不能同时支持多个操作，操作系统就必须伪造它们。我们马上就会看到。现在，让我们把并发性看作是同时运行任务的感觉，而真正的并行性则是同时运行的任务。

![](/img/15525660165754.jpg)
2. Parallelism is a subset of concurrency. 并行是并发的一个子集。

### What makes concurrency and parallelism possible
什么使并发和并行成为可能

The central processing unit (CPU) in your computer does the hard work of running programs. It is made of several parts, the main one being the so-called core: that's where computations are actually performed. A core is capable of running only one operation at a time.
计算机中的中央处理器(CPU)负责运行程序。它由几个部分组成，其中最主要的部分是所谓的核心:这是实际执行计算的地方。一个核心一次只能运行一个操作。

This is of course a major drawback. For this reason operating systems have developed advanced techniques to give the user the ability to running multiple processes (or threads) at once, especially on graphical environments, even on a single core machine. The most important one is called preemptive multitasking, where preemption is the ability of interrupting a task, switching to another one and then resuming the first task at a later time.
这当然是一个主要的缺点。因此，操作系统开发了高级技术，使用户能够同时运行多个进程(或线程)，特别是在图形环境中，甚至在单核机器上。其中最重要的一种被称为抢占式多任务处理，抢占是指中断一项任务，切换到另一项任务，然后在稍后的时间恢复第一项任务的能力。

So if your CPU has only one core, part of a operating system's job is to spread that single core computing power across multiple processes or threads, which are executed one after the other in a loop. This operation gives you the illusion of having more than one program running in parallel, or a single program doing multiple things at the same time (if multithreaded). Concurrency is met, but true parallelism — the ability to run processes simultaneously — is still missing.
因此，如果您的CPU只有一个核心，那么操作系统的部分工作就是将单个核心的计算能力分散到多个进程或线程上，这些进程或线程在一个循环中一个接一个地执行。这个操作给您一种错觉，好像有多个程序并行运行，或者一个程序同时做多个事情(如果是多线程的)。虽然满足了并发性，但是真正的并行性——同时运行进程的能力——仍然缺失。

Today modern CPUs have more than one core under the hood, where each one performs an independent operation at a time. This means that with two or more cores true parallelism is possible. For example, my Intel Core i7 has four cores: it can run four different processes or threads at the same time, simultaneously.
今天，现代cpu有多个核心，每个核心一次执行一个独立的操作。这意味着使用两个或多个内核可以实现真正的并行。例如，我的Intel Core i7有四个内核:它可以同时运行四个不同的进程或线程。

Operating systems are able to detect the number of CPU cores and assign processes or threads to each one of them. A thread may be allocated to whatever core the operating system likes, and this kind of scheduling is completely transparent for the program being run. Additionally, preemptive multitasking might kick in in case all cores are busy. This gives you the ability to run more processes and threads than the actual number or cores available in your machine.
操作系统能够检测CPU内核的数量，并为每个CPU内核分配进程或线程。一个线程可以分配给操作系统喜欢的任何核心，这种调度对于正在运行的程序来说是完全透明的。此外，先发制人的多任务处理可能会在所有核心都很忙的情况下发挥作用。这使您能够运行比计算机中可用的实际数目或内核更多的进程和线程。

#### Multi-threading application on a single core: does it make sense?
单内核上的多线程应用程序:有意义吗?

True parallelism on a single-core machine is impossible to achieve. Nevertheless it still makes sense to write a multithreaded program, if your application can benefit from it. When a process employs multiple threads, preemptive multitasking can keep the app running even if one of those threads performs a slow or blocking task.
在单核机器上实现真正的并行是不可能的。不过，如果您的应用程序可以从中受益，那么编写多线程程序仍然是有意义的。当一个进程使用多个线程时，抢占式多任务处理可以使应用程序保持运行，即使其中一个线程执行了一个缓慢或阻塞的任务。

Say for example you are working on a desktop app that reads some data from a very slow disk. If you write the program with just one thread, the whole app would freeze until the disk operation is finished: the CPU power assigned to the only thread is wasted while waiting for the disk to wake up. Of course the operating system is running many other processes besides this one, but your specific application will not be making any progress.
例如，您正在处理一个桌面应用程序，它从一个非常慢的磁盘读取一些数据。如果只使用一个线程编写程序，整个应用程序就会冻结，直到磁盘操作完成:在等待磁盘唤醒时，分配给唯一线程的CPU能量就会浪费。当然，除了这个进程之外，操作系统还在运行许多其他进程，但是您的特定应用程序不会取得任何进展。

Let's rethink your app in a multithreaded way. Thread A is responsible for the disk access, while thread B takes care of the main interface. If thread A gets stuck waiting because the device is slow, thread B can still run the main interface, keeping your program responsive. This is possible because, having two threads, the operating system can switch the CPU resources between them without getting stuck on the slower one.
让我们以多线程的方式重新考虑您的应用程序。线程A负责磁盘访问，而线程B负责主接口。如果线程A因为设备速度慢而等待卡住，线程B仍然可以运行主界面，使程序保持响应。这是可能的，因为有两个线程，操作系统可以在它们之间切换CPU资源，而不会被较慢的线程卡住。

### More threads, more problems
线程越多，问题越多

As we know, threads share the same chunk of memory of their parent process. This makes extremely easy for two or more of them to exchange data within the same application. For example: a movie editor might hold a big portion of shared memory containing the video timeline. Such shared memory is being read by several worker threads designated for rendering the movie to a file. They all just need a handle (e.g. a pointer) to that memory area in order to read from it and output rendered frames to disk.
我们知道，线程共享其父进程的相同内存块。这使得它们中的两个或多个可以非常容易地在同一个应用程序中交换数据。例如:一个电影编辑器可能拥有包含视频时间轴的大部分共享内存。这样的共享内存由几个工作线程读取，这些线程被指定用于将电影呈现到文件中。它们都只需要一个句柄(例如指针)指向内存区域，以便从该内存区域读取数据并将呈现的帧输出到磁盘。

Things run smoothly as long as two or more threads read from the same memory location. The troubles kick in when at least one of them writes to the shared memory, while others are reading from it. Two problems can occur at this point:
两个或多个线程从同一个内存位置读取，可以顺利地运行。当其中至少有一个内存写入共享内存，而其他内存正在从中读取时，问题就出现了。此时可能出现两个问题:

* **data race** — while a writer thread modifies the memory, a reader thread might be reading from it. If the writer has not finished its work yet, the reader will get corrupted data;
数据竞争——当写线程修改内存时，读线程可能正在读取内存。如果作者还没有完成它的工作，读者将得到损坏的数据;

* **race condition** — a reader thread is supposed to read only after a writer has written. What if the opposite happens? More subtle than a data race, a race condition is about two or more threads doing their job in an unpredictable order, when in fact the operations should be performed in the proper sequence to be done correctly. Your program can trigger a race condition even if it has been protected against data races.
竞态条件——读取线程应该只在写入器写入之后才读取。如果发生相反的情况呢?比数据竞争更微妙的是，竞争条件是两个或多个线程以不可预知的顺序执行它们的工作，而实际上操作应该按照正确的顺序执行才能正确执行。您的程序可以触发竞争条件，即使它已被保护免受数据竞争。

#### The concept of thread safety
线程安全的概念

A piece of code is said to be thread-safe if it works correctly, that is without data races or race conditions, even if many threads are executing it simultaneously. You might have noticed that some programming libraries declare themselves as being thread-safe: if you are writing a multithreaded program you want to make sure that any other third-party function can be used across different threads without triggering concurrency problems.
如果一段代码正确工作，即没有数据竞争或竞争条件，那么它就是线程安全的，即使许多线程同时执行它。您可能已经注意到，一些编程库声明自己是线程安全的:如果您正在编写多线程程序，那么您希望确保任何其他第三方函数都可以跨不同的线程使用，而不会引发并发问题。

### The root cause of data races
数据竞争的根本原因

We know that a CPU core can perform only one machine instruction at a time. Such instruction is said to be atomic because it's indivisible: it can't be break into smaller operations. The Greek word "atom" (ἄτομος; atomos) means uncuttable.
我们知道一个CPU核心一次只能执行一条机器指令。这样的指令被称为原子指令，因为它是不可分割的:它不能被分割成更小的操作。希腊词“原子”(ἄτομος;atomos)意味着万事万物。

The property of being indivisible makes atomic operations thread-safe by nature. When a thread performs an atomic write on shared data, no other thread can read the modification half-complete. Conversely, when a thread performs an atomic read on shared data, it reads the entire value as it appeared at a single moment in time. There is no way for a thread to slip through an atomic operation, thus no data race can happen.
不可分割的特性使得原子操作本质上是线程安全的。当一个线程对共享数据执行原子写入时，没有其他线程能够读取半完成的修改。相反，当线程对共享数据执行原子读取时，它将读取在某个时刻出现的整个值。线程无法通过原子操作，因此不可能发生数据争用。

The bad news is that the vast majority of operations out there are non-atomic. Even a trivial assignment like x = 1 on some hardware might be composed of multiple atomic machine instructions, making the assignment itself non-atomic as a whole. So a data race is triggered if a thread reads x while another one performs the assignment.
坏消息是，绝大多数的操作都是非原子的。即使是一些硬件上的x = 1这样的简单赋值也可能由多个原子机器指令组成，从而使赋值本身作为一个整体是非原子的。因此，如果一个线程读取x，而另一个线程执行赋值，就会触发数据竞争。

### The root cause of race conditions
竞态条件的根本原因

Preemptive multitasking gives the operating system full control over thread management: it can start, stop and pause threads according to advanced scheduling algorithms. You as a programmer cannot control the time or order of execution. In fact, there is no guarantee that a simple code like this:
抢占式多任务处理使操作系统完全控制线程管理:它可以根据先进的调度算法启动、停止和暂停线程。作为程序员，您不能控制执行的时间或顺序。事实上，并不能保证这样一个简单的代码:

```
writer_thread.start()
reader_thread.start ()
``` 

would start the two threads in that specific order. Run this program several times and you will notice how it behaves differently on each run: sometimes the writer thread starts first, sometimes the reader does instead. You will surely hit a race condition if your program needs the writer to always run before the reader.
将以特定的顺序启动两个线程。多次运行这个程序，您会注意到它在每次运行时的行为是如何不同的:有时写线程首先启动，有时读线程先启动。如果您的程序需要编写器总是在读取器之前运行，那么您肯定会遇到竞态条件。

This behavior is called non-deterministic: the outcome changes each time and you can't predict it. Debugging programs affected by a race condition is very annoying because you can't always reproduce the problem in a controlled way.
这种行为被称为非确定性:结果每次都在变化，你无法预测它。调试受竞态条件影响的程序是非常烦人的，因为您不能总是以受控的方式重现问题。

#### Teach threads to get along: concurrency control
教线程如何相处:并发控制

Both data races and race conditions are real-world problems: some people even died because of them. The art of accommodating two or more concurrent threads is called concurrency control: operating systems and programming languages offer several solutions to take care of it. The most important ones:
数据竞争和竞争条件都是现实世界的问题:有些人甚至因此而死亡。容纳两个或多个并发线程的行为称为并发控制:操作系统和编程语言提供了多种解决方案来处理它。最重要的是:

* **synchronization** — a way to ensure that resources will be used by only one thread at a time. Synchronization is about marking specific parts of your code as "protected" so that two or more concurrent threads do not simultaneously execute it, screwing up your shared data;
同步——一种确保每次只有一个线程使用资源的方法。同步是将代码的特定部分标记为“受保护的”，这样两个或多个并发线程就不会同时执行它，从而破坏共享数据;

* **atomic operations** — a bunch of non-atomic operations (like the assignment mentioned before) can be turned into atomic ones thanks to special instructions provided by the operating system. This way the shared data is always kept in a valid state, no matter how other threads access it;
原子操作——由于操作系统提供了特殊的指令，许多非原子操作(如前面提到的赋值)可以转换为原子操作。这样，无论其他线程如何访问共享数据，共享数据始终保持在有效状态;

* **immutable data** — shared data is marked as immutable, nothing can change it: threads are only allowed to read from it, eliminating the root cause. As we know threads can safely read from the same memory location as long as they don't modify it. This is the main philosophy behind functional programming.
不可变数据——共享数据被标记为不可变的，没有什么可以改变它:线程只允许从它读取数据，消除了根本原因。我们知道，线程可以安全地从相同的内存位置读取数据，只要它们不修改它。这是函数式编程背后的主要原理。

I will cover all this fascinating topics in the next episodes of this mini-series about concurrency. Stay tuned!
在这个关于并发性的迷你系列的下一集中，我将讨论所有这些迷人的主题。请继续关注!

原文： https://www.internalpointers.com/post/gentle-introduction-multithreading



