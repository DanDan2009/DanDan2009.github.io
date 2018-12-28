---
layout:     post
title:      " Optimizing App Startup Time"
subtitle:   "优化App启动时间"
date:      2018-12-18 21:40:00
author:     "Dan"
header-img: "img/optimizing-app-startup-time.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ARTS
    - Algorihm
    - iOS
---


optimizing app startup time

https://developer.apple.com/videos/play/wwdc2016/406/



 [ Music ]  Good morning and welcome to session 406, Optimizing App Startup Time.
[音乐]大家早上好，欢迎来到第406讲优化App启动时间。

 My name is Nick Kledzik, and today my colleague Louis and I are going to take you on a guided tour of how a process launches.
我的名字是Nick Kledzik，今天我和我的同事Louis将带您参观一个过程是如何启动的。

 Now you may be wondering, is this topic right for me.
现在你可能会想，这个话题适合我吗?

 So we had our crack developing marketing team do some research, and they determined there are three groups that will benefit by listening to this talk.
所以我们让我们优秀的营销团队做了一些研究，他们确定有三组人会从听这个演讲中受益。

 The first, is app developers that have a app that launches to slowly.
第一个是应用程序开发人员，他们的应用程序启动缓慢。

 The second group, is app developers that don't want to be in the first group [laughter].
第二组，是那些不想进入第一组的应用程序开发者[笑声]。

 And lastly, is anyone who's just really curious about how the OS operates.
最后，谁会对操作系统的运作方式感到好奇呢?

 So this talk is going to be divided in two sections, the first is more theory and the second more practical, I'll be doing the first theory part.
所以这次演讲将分为两个部分，第一部分更理论化，第二部分更实用，我将会讲第一部分。

 And in it I'll be walking you through all the steps that happen, all the way up to main.
我将带你们走过所有发生的步骤，一直走到主体。

 But in order for you to understand and appreciate all the steps I first need to give you a crash course on Mach-O and Virtual Memory.
但是为了让你们理解和欣赏我首先需要给你们上一堂模拟运算和虚拟内存的速成课。

 So first some Mach-O terminology, quickly.
首先是一些Mach-O术语。

 Mach-O is a bunch of file types for different run time executables.
Mach-O是一组用于不同运行时可执行文件的文件类型。

 So the first executable, that's the main binary in an app, it's also the main binary in an app extension.
第一个可执行文件，是app中的主二进制文件，也是app扩展中的主二进制文件。

 A dylib is a dynamic library, on other platforms meet, you may know those as DSOs or DLLs.
dylib是一个动态库，在其他平台上，你可能知道DSOs或dll。

 Our platform also has another kind of thing called a bundle.
我们的平台还有另一种东西叫bundle。

 Now a bundle's a special kind of dylib that you cannot link against, all you can do is load it at run time by an dlopen and that's used on a Mac OS for plug-ins.
捆绑包是一种特殊的dylib，你不能链接它，你能做的就是在运行时通过dlopen加载它，这在Mac操作系统中用于插件。

 Last, is the term image.
最后是图像。

 Image refers to any of these three types.
图像是指这三种类型中的任何一种。

 And I'll be using that term a lot.
我会经常用到这个词。

 And lastly, the term framework is very overloaded in our industry, but in this context, a framework is a dylib with a special directory structure around it to holds files needed by that dylib.
最后，术语框架在我们的行业中是非常重载的，但是在这个上下文中，框架是一个dylib，它的周围有一个特殊的目录结构来保存该dylib需要的文件。

 So let's dive right into the Mach-O image format.
让我们直接进入Mach-O图像格式。

 A Mach-O image is divided into segments, by convention all segment names are, use upper case letters.
一个马赫- o图像被分割成段，按照惯例所有段名都是大写字母。

 Now, each segment is always a multiple of the page size, in this example the text is 3 pages, the DATA and LINKEDIT are each one page.
现在，每个段总是页面大小的倍数，在这个例子中，文本是3页，数据和LINKEDIT都是1页。

 Now the page size is determined by the hardware, for arm64, the page size is 16K, everything else it's 4k.
现在页面大小由硬件决定，对于arm64，页面大小是16K，其他的都是4k。

 Now another way to look at the thing is sections.
另一种方法是分段。

 So sections is something the compiler omits.
所以部分是编译器忽略的。

 But sections are really just a subrange of a segment, they don't have any of the constraints of being page size, but they are non-overlapping.
但是section实际上只是段的子范围，它们没有页面大小的限制，但是它们是不重叠的。

 Now, the most common segment names are TEXT, DATA, LINKEDIT, in fact almost every binary has exactly those three segments.
现在，最常见的段名是TEXT, DATA, LINKEDIT，事实上几乎每个二进制都有这三个段。

 You can add custom ones but it usually doesn't add any value.
您可以添加自定义的，但通常不会添加任何值。

 So what are these used for? Well TEXT is at the start of the file, it contains the Mach header, it contains any machine instructions as well as any read only constant such as c strings.
这些是做什么用的?文本在文件的开头，它包含Mach头，它包含任何机器指令以及只读常量，比如c字符串。

 The DATA segment is rewrite, the DATA segment contains all your global variables.
数据段被重写，数据段包含所有的全局变量。

 And lastly, is the LINKEDIT.
最后是LINKEDIT。

 Now the LINKEDIT doesn't contain your functions of global variables, a LINKEDIT contains information about your function of variables such as their name and address.
LINKEDIT不包含全局变量的函数，它包含关于变量函数的信息，比如它们的名称和地址。


 You may have also heard of universal files, what are they? Well suppose you build an iOS app, for a 64 bit, and now you have this Mach-O file, so what happens the next code when you say you also want to build it for 32 bit devices? When you rebuild, Xcode will build another separate Mach-O file, this one built for 32 bits, RB7.
你可能也听说过通用文件，它们是什么?假设你为64位构建了一个iOS应用程序，现在你有了这个Mach-O文件，那么当你说你也想为32位设备构建它时，接下来会发生什么呢?当您重新构建时，Xcode将构建另一个单独的Mach-O文件，这个文件是为32位构建的，RB7。

 And then those two files are merged into a third file, called the Mach-O universal file.
然后这两个文件合并到第三个文件中，称为Mach-O通用文件。

 And that has a header at the start, and all the header has a list of all the architectures and what their offsets are in the file.
它在开头有一个头，所有的头都有一个所有架构的列表以及它们在文件中的偏移量。

 And that header is also one page in size.
标题也是一页的大小。

 Now you may be wondering, why are the segments multiple page sizes? Why is the header a page sizes, and it's wasting a lot of space.
现在您可能想知道，为什么段是多个页面大小?为什么标题是一个页面大小，它浪费了很多空间。

 Well the reason everything is page based has to do with our next topic which is virtual memory.
所有东西都是基于页面的原因与我们的下一个主题有关那就是虚拟内存。

 So what is virtual memory? Some of you may know the adage in software engineering that every problem can be solved by adding a level of indirection.
那么什么是虚拟内存呢?有些人可能知道软件工程中的格言，即每个问题都可以通过添加一个间接级别来解决。

 So the problem with, that virtual memory solves, is how do you manage all your physical RAM when you have all these processes? So they added a little of indirection.
因此，虚拟内存解决的问题是，当您拥有所有这些进程时，如何管理所有物理RAM ?所以他们增加了一些间接的信息。

 Every process is a logical address space which gets mapped to some physical page of RAM.
每个进程都是一个逻辑地址空间，它映射到RAM的某个物理页面。

 Now this mapping does not have to be one to one, you could have logical addresses that go to no physical RAM and you can have multiple logical addresses that go to the same physical RAM.
现在，这个映射不必是一对一的，您可以有逻辑地址，它们不去物理RAM，也可以有多个逻辑地址，它们去相同的物理RAM。

 This offered lots of opportunities here.
这提供了很多机会。

 So what can you do with VM? Well first, if you have a logical address that does not map to any physical RAM, when you access that address in your process, a page fault happens.
那么VM可以做什么呢?首先，如果有一个逻辑地址没有映射到任何物理RAM，当您在进程中访问该地址时，就会发生页面错误。

 At that point the kernel stops that thread and tries to figure out what needs to happen.
此时，内核停止该线程，并试图找出需要发生什么。

 The next thing is if you have two processes, with different logical addresses, mapping to the same physical page, those two processes are now sharing the same bit of RAM.
接下来，如果您有两个具有不同逻辑地址的进程，映射到相同的物理页面，那么这两个进程现在共享相同的RAM位。

 You now have sharing between processes.
您现在可以在进程之间进行共享。

 Another interesting feature is file backed mapping.
另一个有趣的特性是文件支持映射。

 Rather than actually read an entire file into RAM you can tell the VM system through the mmap call, the I want this slice of this file mapped to this address range in my process.
您可以通过mmap调用告诉VM系统，而不是将整个文件读入RAM，我希望这个文件的这一部分映射到进程中的这个地址范围。

 So why would you do that? Well rather than having to read the entire file, by having that mapping set up, as you first access those different addresses, as if you had read it in memory, each time you access an address that hasn't been accessed before it will cause a page fault, the kernel will read just that one page.
那你为什么要这么做呢?而不必读取整个文件,通过映射设置,当你第一次访问这些不同的地址,如果你读过它在内存中,每次访问一个地址,还没有被访问之前,将导致一个页面错误,内核将读一页。

 And that gives you lazy reading of your file.
这会让你懒于阅读你的文件。

 Now we can put all these features together, and what I told you about Mach-O you now realize that the TEXT segment of any of that dylib or image can be mapped into multiple processes, it will be read lazily, and all those pages can be shared between those processes.
现在我们可以把所有这些特性放在一起，我告诉你们的马赫- o你们现在意识到任何一个dylib或图像的文本段可以映射到多个进程中，它将被延迟读取，所有这些页面可以在这些进程之间共享。

 What about the DATA segment? The DATA segment is read, write, so for that we have trick called copy on write, it's kind of similar to the, cloning that seen in the Apple file system.
那么数据段呢?数据段是读、写的，所以我们有所谓的写上拷贝的技巧，它有点类似于，在苹果文件系统中看到的克隆。

 What copy and write does is it optimistically shares the DATA page between all the processes.
复制和写入所做的是在所有进程之间乐观地共享数据页。

 What happens when one process, as long as they're only reading from the global variables that sharing works.
当一个进程，只要它们只从全局变量中读取共享工作时，会发生什么。

 But as soon as one process actually tries to write to its DATA page, the copy and write happens.
但是一旦一个进程真正尝试写入它的数据页，就会发生复制和写入。

 The copy and write causes the kernel to make a copy of that page into another physical RAM and redirect the mapping to go to that.
复制和写入使内核将该页面复制到另一个物理RAM中，并将映射重定向到该物理RAM中。

 So that one process now has its own copy of that page.
所以一个进程现在有了它自己的页面副本。

 Which brings us to clean versus dirty pages.
这就把我们带到了干净和肮脏的页面上。

 So that copy is considered a dirty page.
所以那个拷贝被认为是肮脏的一页。

 A dirty page is something that contains process specific information.
脏页面是包含进程特定信息的页面。

 A clean page is something that the kernel could regenerate later if needed such as rereading from disc.
一个干净的页面是内核可以在以后需要时重新生成的，比如从磁盘上重新读取。

 So dirty pages are much more expensive than clean pages.
所以脏页比干净页贵得多。

 And the last thing is the permission boundaries are on page boundaries.
最后一点是权限边界在页面边界上。

 By that I mean the permissions are you can mark a page readable, writable, or executable, or any combination of those.
我的意思是说权限是你可以标记一个页面可读，可写，可执行，或任何这些的组合。

 So let's put this all together, I talked about the Mach-O format, something about virtual memory, let's see how they play together.
让我们把这些放在一起，我讲过马赫- o格式，一些关于虚拟内存的东西，让我们看看它们是如何一起工作的。

 Now I'm going to skip ahead and talk a little, how the dyld operates and in a few moments I'll actually walk you through this but for now, I just want to show you how this maps between Mach-O and virtual memory.
现在我要跳过来讲一点，讲一下dyld是如何工作的，过一会儿我将向你们详细介绍这个，但现在，我只想向你们展示这个在Mach-O和虚拟内存之间是如何映射的。

 So we have a dylib file here, and rather than reading it in memory we've mapped it in memory.
我们这里有一个dylib文件，我们不是在内存中读取它，而是在内存中映射它。

 So, in memory this dylib would have taken eight pages.
在记忆中，这个dylib用了8页。

 The savings, why it's different is these ZeroFills.
它的不同之处在于这些零填充。

 So it turns out most global variables are zero initially.
所以大多数全局变量一开始都是0。

 So the static [inaudible] makes an optimization that moves all the zero global variables to the end, and then takes up no disc space.
所以静态[听不清]进行了优化，将所有的0全局变量移动到最后，然后不占用磁盘空间。

 And instead, we use the VM feature to tell the VM the first time this page is accessed, fill it with zero's.
相反，我们使用VM特性告诉VM第一次访问这个页面时，用0填充它。

 So it requires no reading.
所以它不需要阅读。

 So the first thing dyld has to do is it has to look at the Mach header, in memory, in this process.
所以dyld要做的第一件事就是在这个过程中查看内存中的Mach头。

 So it'll be looking at the top box in memory, when that happens, there's nothing there, there's no mapping to a physical page so a page fault happens.
它会看到内存中的顶部框，当它发生时，那里什么都没有，没有到物理页面的映射所以页面错误发生了。

 At that point the kernel realizes this is mapped to a file, so it'll read the first page of the file, place it into physical RAM, set the mapping to it.
此时，内核意识到映射到一个文件，因此它将读取文件的第一页，将其放入物理RAM，并设置映射到它。

 Now dyld can actually start reading through the Mach header.
现在，dyld可以开始读取Mach报头了。

 It reads through the Mach header, the Mach header says oh, there's some information in the LINKEDIT segment you need to look at.
它会读取Mach header, Mach header会说，在LINKEDIT片段中有一些信息需要查看。

 So again, dyld drops down what's in the bottom box in process one.
再一次，dyld把过程1中底部盒子里的东西放下来。

 Which again causes a page fault.
这会再次导致页面错误。

 Kernel services it by reading into another physical page of RAM, the LINKEDIT.
内核通过读入RAM的另一个物理页面LINKEDIT来服务它。

 Now dyld can expect a LINKEDIT.
现在，dyld可以期待LINKEDIT了。

 Now in process, the LINKEDIT will tell dyld, you need to make some fix ups to this DATA page to make this dylib runable.
现在，在这个过程中，LINKEDIT将告诉dyld，您需要对这个数据页面进行一些修复，使这个dylib可运行。


 So, the same thing happens, dyld is now, reads some data from the DATA page, but there's something different here.
同样的事情发生了，dyld现在从数据页读取一些数据，但这里有些不同。

 dyld is actually going to write something back, it's actually going to change that DATA page and at this point, a copy on write happens.
dyld会写一些东西回来，它会改变那个数据页，此时，会发生写时复制。

 And this page becomes dirty.
这一页变得很脏。

 So what would have been 8 pages of dirty RAM if I just malloced eight pages and then the read the stuff into it I would have eight pages of dirty RAM.
那么8页的脏内存是多少如果我错置了8页然后读进去我就有8页的脏内存。

 But now I only have one page of dirty RAM and two clean pages.
但是现在我只有一页脏内存和两页干净内存。

 So what's going to happen when the second process loads the same dylib.
那么当第二个进程加载相同的dylib时会发生什么。

 So in the second process dyld goes through the same steps.
在第二个过程中，dyld经过相同的步骤。

 First it looks at the Mach header, but this time the kernel says, ah, I already have that page in RAM somewhere so it simply redirects the mapping to reuse that page no iO was done.
首先它查看Mach报头，但是这次内核说，啊，我已经在RAM的某个地方有了那个页面，所以它只是重定向映射来重用那个页面，没有iO。

 The same think with LINKEDIT, it's much faster.
和LINKEDIT一样，它更快。

 Now we get to the DATA page, at this point the kernel has to look to see if the DATA page, the clean copy already still exists in RAM somewhere, and if it does it can reuse it, if not, it has to reread it.
现在我们来看看数据页，此时内核必须查看数据页，干净的副本是否仍然存在于RAM的某个地方，如果存在，它可以重用它，如果不存在，它必须重新读取它。

 And now in this process, dyld will dirty the RAM.
在这个过程中，dyld会污染RAM。

 Now the last step is the LINKEDIT is only needed while dyld is doing its operations.
最后一步是LINKEDIT只在dyld执行操作时才需要。

 So it can hint to the kernel, once it's done, that it doesn't really need these LINKEDIT pages anymore, you can reclaim them when someone else needs RAM.
它可以向内核暗示，一旦完成，它就不再需要这些LINKEDIT页面了，你可以在别人需要RAM时回收它们。

 So the result is now we have two processes sharing these dylibs, each one would have been eight pages, or a total of 16 dirty pages, but now we only have two dirty pages and one clean, shared page.
结果是现在我们有两个进程共享这些dylib，每个进程都有8个页面，或者总共有16个脏页面，但是现在我们只有两个脏页面和一个干净的共享页面。

 Two other minor things I want to go over is that how security effects dyld, these two big security things that have impacted dyld.
我想讲的另外两件小事是安全如何影响dyld，这两件大事影响了dyld。

 So one is ASLR, address space layout randomization, this is a decade or two old technology, where basically you randomize the load address.
一种是ASLR，地址空间布局的随机化，这是一种十到二十年前的技术，基本上你随机化加载地址。

 The second is code signing, it has to, many of you have had to deal with code signing, in Xcode, and you think of code signing as, you run a cryptographic hash over the entire file, and then sign it with your signature.
第二种是代码签名，很多人都在Xcode中处理过代码签名，代码签名就是，在整个文件上运行加密哈希，然后用签名签名。

 Well, in order to validate that run time, that means the entire file would have to be re-read.
为了验证运行时，这意味着必须重新读取整个文件。

 So instead what actually happens at build time, is every single page of your Mach-O file gets its own individual cryptographic hash.
因此，在构建时实际发生的是，Mach-O文件的每个页面都有自己的加密散列。

 And all those hashes are stored in the LINKEDIT.
所有这些哈希表都存储在LINKEDIT中。

 This allows each page to be validated that it hasn't been tampered with and was owned by you at page in time.
这样可以验证每个页面，确保它没有被篡改，并且是您在页面时拥有的。

 Okay, so we finished the crash course, now I'm going to walk you from exec to main.
好了，我们已经完成了速成课程，现在我要带你从exec走到main。

 So what is exec? Exec is a system call.
那么什么是exec呢?Exec是一个系统调用。

 When you trap into the kernel, you basically say I want to replace this process with this new program.
当你陷入内核时，你基本上可以说我想用这个新程序替换这个过程。

 The kernel wipes the entire address space and maps in that executable you specified.
内核清除指定的可执行文件中的整个地址空间并映射。

 Now for ASLR it maps it in at a random address.
对于ASLR，它映射到一个随机地址。

 The next thing it does is from that random, back down to zero, it marks that whole region inaccessible, ad by that I mean it's marked not readable, not writeable, not executable.
它做的下一件事是从那个随机的，回到0，它标记了整个区域不可访问，我的意思是它标记了不可读，不可写，不可执行。

 The size of that region is at least 4KB to 32 bit processes and at least 4GB for 64 bit processes.
该区域的大小至少为4KB到32位进程，64位进程的大小至少为4GB。

 This catches any NULL pointer references and also foresees more bits, it catches any, pointer truncations.
它捕获任何空指针引用，并预测更多的位，它捕获任何，指针截断。

 Now, life was easy for the first couple decades, of Unix because all I do is map a program, set the PC into it, and start running it.
在最初的几十年里，Unix的生活很简单，因为我所做的就是映射一个程序，将PC放入其中，然后开始运行它。

 And then shared libraries were invented.
然后，共享库被发明了。

 So who loads dylibs? They quickly realize that they got really complicated fast and the kernel people didn't want the kernel to do it, so instead a helper program was created.
那么谁装了dylibs?他们很快意识到，他们很快就变得非常复杂，内核的人不想让内核来做这件事，所以创建了一个辅助程序。

 In our platform it's called dyld.
我们的平台叫dyld。

 On other Unix's you may know it as LD.
在其他Unix上，您可能知道它是LD。

so.
所以。

 So when the kernel's done mapping a process it now maps another Mach-O called dyld into that process at another random address.
因此，当内核完成了对进程的映射后，它现在以另一个随机地址将另一个名为dyld的Mach-O映射到进程中。

 Sets the PC into dyld and let's dyld finish launching the process.
将PC设置为dyld，让dyld完成进程的启动。

 So now dyld's running in process and its job is to load all the dylibs that you depend on and get everything prepared and running.
现在dyld正在运行，它的任务是加载所有你所依赖的dylibs，并准备好一切并运行。

 So let's walk through those steps.
我们来看看这些步骤。

 This is a whole bunch of steps and it has sort of a timeline along the bottom here, as we walk through these we'll walk through the timeline.
这是一系列的步骤它在底部有一个时间轴，当我们通过这些我们会通过时间轴。

 So first thing, is dyld has to map all the dependent dylibs.
首先，dyld需要映射所有依赖dylibs。

 Well what are the dependent dylibs? To find those it first reads the header of the main executable that the kernel already mapped in that header is a list of all the dependent libraries.
什么是依赖dylibs?为了找到这些文件，它首先读取内核已经映射到该文件头中的主可执行文件头，这是所有依赖库的列表。

 So it's got to parse that out.
它需要解析出来。

 Then it has to find each dylib.
然后它必须找到每个dylib。

 And once it's found each dylib it has to open and run the start of each file, it needs to make sure that it is a Mach-O file, validate it, find its code signature, register that code signature to the kernel.
一旦找到了每个dylib，它必须打开并运行每个文件的开头，它需要确保它是一个Mach-O文件，验证它，找到它的代码签名，将代码签名注册到内核。

 And then it can actually call mmap at each segment in that dylib.
然后它可以在dylib中的每个段调用mmap。

 Okay, so that's pretty simple.
很简单。

 Your app knows about the kernel dyld, dyld then says oh this app depends on A and B dylib, load the two of those, we're done.
你的应用程序知道内核dyld，然后dyld说，这个应用程序依赖于A和B dylib，加载这两个，我们做完了。

 Well, it gets more complicated, because A.
它变得更复杂了，因为A。

dylib and B.
dylib和B。

dylib themselves could depend upon the dylibs.
dylib本身可以依赖于dylibs。

 So dyld has to do the same thing over again for each of those dylibs, and each of the dylibs may depend on something that's already loaded or something new so it has to determine whether it's already been loaded or not, and if not, it needs to load it.
所以dyld必须对每个dylib做同样的事情，每个dylib可能依赖于已经加载或新加载的东西所以它必须确定它是否已经加载，如果没有，它需要加载它。

 So, this continues on and on.
这样继续下去。

 And eventually it has everything loaded.
最终它加载了所有东西。

 Now if you look at a process, the average process in our system, loads anywhere between 1 to 400 dylibs, so that's a lot of dylibs to be loaded.
现在，如果你看一个进程，我们系统中的平均进程，加载的dylib在1到400之间，所以要加载的dylib很多。

 Luckily most of those are OS dylibs, and we do a lot of work when building the OS to pre-calculate and pre-cache a lot of the work that dyld has to do to load these things.
幸运的是，大多数都是OS dylib，我们在构建OS时做了很多工作来预计算和预缓存dyld加载这些东西需要做的很多工作。

 So OS dylibs load very, very quickly.
所以OS dylibs加载非常非常快。

 So now we've loaded all the dylibs, but they're all sitting in their floating independent of each other, and now we actually have to bind them together.
现在我们已经加载了所有的dylib，但是它们都在各自的浮动中相互独立，现在我们需要把它们绑定在一起。

 That's called fix-ups.
这就是所谓的可以安排。

 But one thing about fix-ups is we've learned, because of code signing we can't actually alter instructions.
但是关于修复有一点我们已经学过了，因为代码签名我们实际上不能改变指令。

 So how does one dylib call into another dylib if you can't change the instructions of how it calls? Well, we call back our old friend, and we add a lot of old indirection.
那么，如果不能更改它如何调用的指令，一个dylib如何调用另一个dylib呢?好吧，我们把老朋友叫回来，然后加入很多间接引语。

 So our code-gen, is called dynamic PIC.
所以我们的代码生成，叫做动态PIC。

 It's positioned independent code, meaning the code can be loaded into the address and is dynamic, meaning things are, addressed indirectly.
它是定位独立的代码，这意味着代码可以加载到地址中，并且是动态的，这意味着事物是间接寻址的。

 What that means is to call for one thing to another, the co-gen actually creates a pointer in the DATA segment and that pointer points to what you want to call.
这意味着要从一件事调用到另一件事，co-gen实际上在数据段中创建了一个指针，这个指针指向要调用的东西。

 The code loads that pointer and jumps to the pointer.
代码加载该指针并跳转到该指针。

 So all dyld is doing is fixing up pointers and data.
所以dyld所做的就是修复指针和数据。

 Now there's two main categories of fix-ups, rebasing and binding, so what's the difference? So rebasing is if you have a pointer that's pointing to within your image, and any adjustments needed by that, the second is binding.
现在有两种主要类型的修复，重基和绑定，那么有什么区别呢?所以，如果你有一个指针指向你的图像，并且需要任何调整，第二个是绑定。

 Binding is if you're pointing something outside your image.
绑定是指你在你的图像外指向一些东西。

 And they each need to be fixed up differently, so I'll go through the steps.
它们每个都需要进行不同的修复，所以我将逐一介绍这些步骤。

 But first, if you're curious, there's a command, dyld info with a bunch of options on it.
但首先，如果你好奇，有一个命令，dyld info，上面有很多选项。

 You can run this on any binary and you'll see all the fix-ups that dyld will have to be doing for that binary to prepare it.
您可以在任何二进制文件上运行它，并且您将看到dyld为该二进制文件所做的所有修改。

 So rebasing.
所以重新定价。


 Well in the old age you could specify a preferred load address for each dylib, and that preferred load address was the static linker and dyld work together such that, if you load, it to that preferred load address, all the pointers and data that was supposed to code internally, were correct and dyld wouldn't have to do any fix-ups.
在年老的时候您可以指定为每个dylib优先加载地址,和优先加载地址是静态链接器和dyld一起工作,如果你加载,优先加载地址,所有的指针和数据应该在内部代码,是正确的和dyld不用做任何可以安排。

 But these days, with ASLR, your dylib is loaded to a random address.
但是现在，使用ASLR，您的dylib被加载到一个随机地址。

 It's slid to some other address, which means all those pointers and data are now still pointed to the old address.
它滑到另一个地址，这意味着所有那些指针和数据现在仍然指向旧地址。

 So in order to fix those up, we need to calculate the slide, which is how much has it moved, and for each of those interior pointers, to basically add the slide value to them.
为了解决这些问题，我们需要计算滑动条，也就是它移动了多少，对于每个内部指针，我们需要将滑动条的值添加到它们。

 So rebasing means going through all your data pointers, that are internal, and basically adding a slide to them.
因此，重新定位意味着遍历所有的数据指针，这些指针都是内部的，基本上是向它们添加一个幻灯片。

 So the concept is very simple, read, add, write, read, add, write.
这个概念很简单，读，加，写，读，加，写。

 But where are those data pointers? Where those pointers are in your segment, are encoded in the LINKEDIT segment.
但是那些数据指针在哪里呢?这些指针在你的段中，被编码在LINKEDIT段中。

 Now, at this point, all we've had is everything mapped in, so when we start doing rebasing, we're actually causing page faults to page in all the DATA pages.
现在，在这一点上，我们所拥有的是所有映射到的东西，所以当我们开始做重基时，我们实际上是在所有数据页中造成页面错误。

 And then we causing copy and writes as we're changing them.
当我们改变它们的时候，就会产生复制和写入。

 So rebasing can sometimes be expensive because of all the iO.
因此，由于iO的原因，重定位有时会很昂贵。

 But one trick we do is we do it sequentially and from the kernel's point of view, it sees data faults happen sequentially.
但我们要做的一个技巧是按顺序执行，从内核的角度来看，它会看到数据错误按顺序发生。

 And when it sees that, the kernel, is reading ahead for us which makes the iO less costly.
当它看到这一点时，内核就会提前为我们读取数据，从而降低iO的成本。

 So next is binding, binding is for pointers that point outside your dylib.
接下来是绑定，绑定是指向dylib之外的指针。

 They're actually bound by name, they're actually is the string, in this case, malloc stored in the link edit, that says this data pointer needs to point to malloc.
它们实际上是由名称绑定的，它们实际上是字符串，在这个例子中，malloc存储在link edit中，它说这个数据指针需要指向malloc。

 So at run time, dyld needs to actually find the implementation of that symbol, which requires a lot of computation, looking through symbol tables.
因此，在运行时，dyld需要实际找到该符号的实现，这需要大量计算，需要查看符号表。

 Once it's found, that values that's stored in that data pointer.
一旦找到它，那个值就存储在那个数据指针中。

 So this is way more computationally complex than rebasing is.
所以这比重新定位要复杂得多。

 But there's very little iO because rebasing has done most of the iO already.
但是iO很少，因为重基已经完成了大部分iO。

Next, so ObjC has a bunch of DATA structures, class DATA structure which is a pointer to its methods and a pointer to a super gloss and so forth.
下一步，ObjC有一堆数据结构，类数据结构是指向它的方法的指针和指向超光泽的指针等等。


 Almost all those are fixed up, via rebasing or binding.
几乎所有这些都是通过重设基或绑定固定的。

 But there's a few extra things that ObjC run time requires.
但是ObjC运行时需要一些额外的东西。

 The first is ObjC is dynamic language and you can request a class become substantiated by name.
第一个是ObjC是一种动态语言，您可以请求一个类被名称证实。

 So that means the ObjC run time has to maintain a table of all names of which class that they map to.
这意味着ObjC运行时必须维护一个表，其中包含它们映射到的类的所有名称。

 So every time you load something, it defines a class, its name needs to be registered with a global table.
因此，每次加载某个东西时，它都会定义一个类，它的名称需要注册到一个全局表中。

 Next, in C++ you may have heard of the fragile ivar problem, sorry.
接下来，在c++中，您可能听说过脆弱的ivar问题，抱歉。

 Fragile base class problem.
脆弱的基类问题。

 We don't have that problem with ObjC because one of the fix-ups we do is we change the offsets of all the ivars dynamically, at load time.
ObjC没有这个问题，因为我们做的一个修正是在加载时动态地改变所有ivar的偏移量。

 Next, in ObjC you can define categories which change the methods of another class.
接下来，在ObjC中，您可以定义改变另一个类的方法的类别。

 Sometimes those are in classes that are not in your image on another dylib, that, those method fix-ups have to be applied at this point.
有时这些类不在另一个dylib上的映像中，这些方法必须在此时应用。

 And lastly, ObjC [inaudible] is based on selectors being unique so we need unique selectors.
最后，ObjC[听不清]是基于选择器是唯一的所以我们需要唯一的选择器。

 So now the work that we've done all the DATA fix-ups, now we can do all the DATA fix-ups that can be basically described statically.
现在我们已经做了所有的数据修正，现在我们可以做所有的数据修正基本上可以静态描述。

 So now's our chance to do dynamic DATA fix ups.
现在是我们进行动态数据修复的时候了。

 So in C++, you can have an initializer, you can say [inaudible] equals whatever expression you want.
在c++中，你可以有一个初始化器，你可以说[听不清]等于你想要的任何表达式。

 That arbitrary expression, at this time needs to be run and it's run at this point now.
这个任意表达式，现在需要运行，现在已经运行了。

 So the C++ compiler generates, initiliazers for these arbitrary DATA initialization.
c++编译器为这些任意数据初始化生成初始化器。

 In ObjC, there's something called the +load method.
在ObjC中，有一个叫做+load的方法。

 Now the +load method is deprecated, we recommend that you don't use it.
现在+load方法已经被废弃了，我们建议您不要使用它。

 We recommend you use a plus initialize.
我们建议您使用+初始化。

 But if you have one, it's run at this point.
但如果你有一个，它在这一点运行。

 So, now I have this big graph, we have your main executable top, all the dylibs depend on, this huge graph, we have to run initializers.
现在我有了这个大图形，我们有了你的主可执行顶部，所有的dylibs依赖于，这个大图形，我们必须运行初始化器。

 What order do we want them in? Well, we run them bottom up.
我们希望它们按什么顺序排列?我们从下往上算。

 And the reason is, when an initialize is run it may need to call up some dylib and you want to make sure that dylibs already ready to be called.
原因是，当一个初始化运行时，它可能需要调用一些dylib，您需要确保dylibs已经准备好被调用。

 So by running the initializers from the bottom all the way up the app class you're safe to call into something you depend on.
因此，通过从下到上运行初始化器，你可以安全地调用你所依赖的东西。


So once all initiliazers are done, now we actually finally get to call the main dyld program.
 一旦所有的初始化程序都完成了，现在我们终于可以调用主要的dyld程序了

 So you survived this theory part, you now all are experts on how processes start, you now know that dyld is a helper program, it loads all dependent libraries, fixing up all the DATA pages, runs initializers and then jumps to main.
所以你挺过了这个理论部分，你们现在都是进程如何启动的专家，你们现在知道了dyld是一个助手程序，它加载所有依赖的库，修复所有的数据页，运行初始化器，然后跳转到main。

 So now to put all this theory you've learned to use, I'd like to hand it over to Louis, who will be giving you some practical tips.
现在，为了把你们所学到的理论运用到实践中，我想把它交给路易斯，他会给你们一些实用的建议。


  Thanks, Nick.
谢谢你,尼克。

 We've all had that experience where we pull our phone out of our pocket, press the home button, and then tap on an application we want to run.
我们都有过这样的经历:我们从口袋里掏出手机，按下home键，然后点击我们想运行的应用程序。

 And then tap, and tap, and tap again on some button because it's not responding.
然后点击，再点击，再点击某个按钮因为它没有响应。

 When that happens to me, it's really frustrating, and I want to delete the app.
当这种情况发生在我身上时，我真的很沮丧，我想删除这个应用程序。

 I'm Louis Gerbarg I work on dyld and today, we're going to discuss how to make your app launch instantly, so your users are delighted.
我是Louis Gerbarg，我在dyld工作，今天我们将讨论如何让你的应用程序立即启动，这样你的用户会很高兴。

 So first off, let's discuss what we're going to go through in this part of the talk.
首先，我们来讨论一下这部分的内容。

 We're going to discuss how fast you actually need to launch so that your users are going to have a good experience.
我们将讨论你实际需要多快才能让你的用户有一个好的体验。

 How to measure that launch time.
如何测量发射时间。

 Because it can be very difficult.
因为这是非常困难的。

 The standard ways you measure your application don't apply before your code can run.
在代码可以运行之前，度量应用程序的标准方法是不适用的。

 We're going to go through a list of the common reasons why your code, or sorry we're going to go through a list of, why, the common reasons your launch can be slow.
我们会列举一些常见的原因来解释你的代码，不好意思，我们会列举一些常见的原因来解释你的代码启动慢的原因。

 And finally, we're going to go through, a way to fix all the slow downs.
最后，我们要讲的是，解决所有慢速问题的方法。

 So I'm going to give you a little spoiler for the rest of my talk.
所以在我接下来的演讲中，我要告诉你们一个小插曲。

 You need to do less stuff [laughter].
你需要少做点事情[笑声]。


 Now, I don't mean your app should have less features, I'm saying that your app has to do less things before it's running.
现在，我不是说你的应用程序应该有更少的功能，我是说你的应用程序在运行之前必须做更少的事情。

 We want you to figure out how to defer some of your launch behaviors in order to initialize them just before execution.
我们希望您能弄清楚如何延迟一些启动行为，以便在执行之前对它们进行初始化。

 So, let's discuss the goals, how fast we want to launch.
所以，让我们讨论一下目标，我们想以多快的速度启动。

 Well, the launch time for various platforms are different.
不同平台的启动时间是不同的。

 But, a good, a good rule of thumb, is 400 milliseconds is a good launch time.
但是，一个很好的经验法则是，400毫秒是一个很好的启动时间。

 Now, the reason for that is that we have launch animations on the phone to give a sense of continuity between the home screen and your application, when you see it execute.
这样做的原因是，我们在手机上启动了动画，当你看到主屏幕和你的应用程序执行时，给它们一种连续性的感觉。

 And those animations take time, and those animations, give you a chance to hide your launch times.
这些动画需要时间，这些动画，让你有机会隐藏你的启动时间。

 Obviously that may be different, in different context your app extensions are also applications that have to launch, they launch in different amounts of time.
很明显，这可能是不同的，在不同的环境下你的应用扩展也是需要启动的应用，它们在不同的时间内启动。

 And a phone and TV, and a watch are different things, but 400 milliseconds is a good target.
手机，电视，手表是不同的东西，但是400毫秒是一个很好的目标。

 You can never take longer than 20 seconds to launch.
发射时间不可能超过20秒。

 If you take longer than 20 seconds, the OS will kill your app, assuming it's going through an infinite loop, and we've all had that experience.
如果你花的时间超过20秒，系统会杀死你的应用程序，假设它正在经历一个无限循环，我们都有过这样的经历。

 Where you click an app, it comes up to a home screen, it doesn't respond, and then it just goes away, and that's usually what's happening here.
当你点击一个应用，它会出现在主屏幕上，它没有回应，然后它就消失了，这通常就是这里发生的。

 Finally, it's very important to test on your slowest supported device.
最后，在支持最慢的设备上进行测试非常重要。

 So those timers are constant values across all supported devices on our platforms.
所以这些计时器是我们平台上所有受支持设备的常量。

 So, if you hit 400 milliseconds on a iPhone 6S that you're using for testing right now, you're probably just barely hitting it, you're probably not going to hit it on a iPhone 5.
所以，如果你在你正在测试的iPhone 6S上点击400毫秒，你可能只是刚刚点击它，你可能不会在iPhone 5上点击它。

 So let's do a recap of Nick's part of the talk.
让我们来回顾一下尼克的演讲。

 What do we have to do to launch, we have to parse images, map images, rebase images, bind images, run image initializers, and then call main.
我们需要做什么来启动，我们需要解析图像，映射图像，重设图像，绑定图像，运行图像初始化，然后调用main。

 If that sounds like a lot, it is, I'm exhausted just saying it.
如果这听起来很多，确实是，我只是说出来就累坏了。

 And then after that, we have to call UIApplicationMain, you'll see that in your ObjC apps or in your Swift apps handled implicitly.
然后，我们需要调用UIApplicationMain，你会在你的ObjC应用或Swift应用中看到隐式处理。

 That does some other things, including running the framework initializers and loading your nibs.
它还可以做其他一些事情，包括运行框架初始化器和加载nib。

 And then finally you'll get a call back in your application delegate.
最后，你会在应用委托中得到一个回调。

 I'm mentioning these last two because those are counted in those 400 milliseconds times that I just mentioned.
我之所以提到最后两个是因为它们是用我刚刚提到的400毫秒来计算的。

 But we're not going to discuss them in this talk.
但是我们不会在这个演讲中讨论它们。

 If you want a better view of what goes on there, there's a talk from 2012, iOS app performance responsiveness.
如果你想更好地了解那里发生了什么，有一个2012年的演讲，iOS应用程序性能响应。

 I highly recommend you go back and view the video.
我强烈建议你回去看视频。

 But that's the last we're going to speak of them right now.
但这是我们现在要讲的最后一个。

 So, let's move on, one more thing I want to talk about, warm versus cold launches.
那么，让我们继续，我想说的另一件事，热发射和冷发射。

 So when you launch an app, we talk about warm and cold launches.
当你启动一个应用程序时，我们会讨论热启动和冷启动。

 And a warm launch is an app where the application is already in memory, either because it's been launched and quit previously, and it's still sitting in the discache in the kernel, or because you just copied it over.
而暖启动是指应用程序已经在内存中，因为它之前已经启动并退出，它仍然在内核的磁盘中，或者因为你刚刚复制了它。

 A cold launch is a launch where it's not in the discache.
冷启动是指不在磁盘中的启动。

 And a cold launch is generally the more important to measure.
而冷发射通常是更重要的测量手段。

 The reason a cold launch is more important to measure is that's when your user is launching an app after rebooting the phone, or for the first time in a long time, that's when you really want it to be instant.
冷启动更重要的原因是，当你的用户在重启手机后启动应用程序，或者是很长一段时间以来的第一次启动应用程序时，你才真正希望它是即时的。

 In order to measure those, you really need to reboot between measurements.
为了度量这些，您确实需要在度量之间重新启动。

 Having said that, if you're working on improving your warm launches, your cold launches will tend to improve also.
话虽如此，如果你在改进暖启动，冷启动也会有所改进。

 You can do rapid development cycles on warm launches, but then every so often, test with a cold launch.
您可以在暖启动上进行快速的开发周期，但是偶尔也可以使用冷启动进行测试。

 So, how do we measure time before main? Well, we have a built in measurement system in dyld, you can access it through setting an environment variable.
那么，我们如何在main之前测量时间呢?我们在dyld中有一个内置的测量系统，你可以通过设置一个环境变量来访问它。

 DYLD Print Statistics.
DYLD打印数据。

 And it's been available in shipping OSes actually, but it prints out a lot of internal debugging information that's not particularly useful, it's missing some information that you probably want.
实际上，它已经在发布的操作系统中可用，但是它打印出了很多内部调试信息，这些信息并不是特别有用，它丢失了一些您可能想要的信息。

 And we're fixing that today.
我们今天正在解决这个问题。

 So it's significantly improved on the new OSes.
所以它在新的操作系统上有了显著的改进。

 It's going to put out, a lot more relevant information for you that should give you actionable ways to improve your launch times.
它会为你提供更多相关的信息，这些信息会给你提供可行的方法来提高你的发布时间。

 And it will be available in seed 2.
它将在seed 2中可用。

 So, one other thing I want to talk about with this, is that the debugger has to pause launch on every single dylib load in order to parse the symbols from your app and load your break points, over a USB cable that can be very time consuming.
所以，我想说的另一件事是，调试器必须在每次加载dylib时暂停启动以便解析应用程序中的符号并通过USB电缆加载断点，这会非常耗时。

 But dyld knows about that and it subtracts the debugger time out from the numbers it's registering.
但dyld知道这一点，它从注册的数字中减去调试器时间。

 So you don't have to worry about it, but you notice it because dyld's going to give you much smaller numbers than you'll observe by looking at the clock on the wall.
所以你不用担心它，但是你会注意到它因为dyld会给你比你看着墙上的钟看到的要小得多的数字。

 That's expected and understood, and it's everything's going correctly if you see that, but I just wanted to make note of it.
这是预料之中的，也是可以理解的，如果你看到了，一切都很正常，但我只是想把它记下来。

 So let's move on, to setting an environment variable in Xcode, you just go to the scheme editor, and you add it like this.
让我们继续，在Xcode中设置一个环境变量，你只要到scheme编辑器，像这样添加它。

 Once you do that you'll get the new console log into the output, console output logged.
一旦您这样做了，您将获得新的控制台日志到输出，控制台输出日志。

 And what does that look like? Well this is what the output looks like, and we have a time bar on the bottom representing the different parts of it.
那是什么样子的呢?这就是输出的样子，在底部有一个时间条表示它的不同部分。

 And let's add one more thing.
我们再加一件事。

 Let's add an indicator for that 400 milliseconds target, which this app I'm working on is not hitting.
让我们为400毫秒的目标添加一个指示器，我正在使用的这个应用程序没有命中这个指示器。

 So, if you look in, this is in order basically the steps that Nick discussed in order to launch an app so let's just go through them in order.
如果你看一下，这基本上是尼克讨论过的启动一个应用程序的步骤让我们按顺序看一下。

So dylib loading, the big thing to understand about dylib loading and the slowdown that you'll see from it, is that embedded dylibs can be expensive.
所以dylib装载，理解dylib装载的一个重要问题，以及你将会看到的减速，就是嵌入的dylib可能很贵。


 So Nick said an average app can be 100 to 400 dylibs.
尼克说，一个应用程序平均可以有100到400个dylib。

 But OS dylibs are fast because when we build the OS, we have ways of pre-calculating a lot of that data.
但是操作系统dylibs是快速的，因为当我们构建操作系统时，我们有预先计算大量数据的方法。


 But we don't have every dylib in every app when we're building the OS.
但在构建操作系统时，并非每个应用程序中都有dylib。


 We can't pre-calculate them for the dylibs you embed with your app, so we have to go through a much slower process as we load those.
我们无法为嵌入到应用程序中的dylib预计算它们，因此在加载它们时，我们必须经历一个慢得多的过程。


 And the solution for this is that we just need to use fewer dylibs and that can be rough.
解决这个问题的方法是，我们只需要使用更少的dylib，这可能是粗糙的。


 And I'm not saying you can't use any, but there are a couple of options here you can merge existing dylibs.
我不是说你不能使用任何，但是这里有两个选项你可以合并现有的dylibs。



 You can use static archives and link them into both, into apps that way.
你可以使用静态存档，并将它们链接到这两种应用程序中。

 And you have an option to lazy load, which is to use dlopen, but dlopen causes some subtle performance and correctness issues, and it actually results in doing more work later on, but it is deferred.
您可以选择延迟加载，即使用dlopen，但dlopen会导致一些微妙的性能和正确性问题，它实际上会导致以后做更多的工作，但它是延迟的。

 So, it's a viable option but you should think long and hard about it and, I would discourage it if at all possible.
所以，这是一个可行的选择，但你应该好好想想，如果可能的话，我会劝阻你。

 So, I have an app here that currently has 26 dylibs, And it's taking 240 milliseconds just to load those, but if I change it and merge those dylibs into two dylibs, then it only takes 20 milliseconds to load the dylibs.
我有一个应用程序，目前有26个dylib，它需要240毫秒来加载这些，但如果我改变它，把这些dylib合并成两个dylib，它只需要20毫秒来加载这些dylib。

 So I can still have dylibs, I can still use them to share, functionality between my app and my extension, but, limiting them will be very useful.
所以我仍然可以使用dylib，我仍然可以使用它们在我的应用程序和我的扩展之间共享功能，但是，限制它们会非常有用。

 And I understand this is a tradeoff you're making between your development convenience and your application launch time for your users.
我知道这是您在为用户的开发便利和应用程序启动时间之间所做的权衡。


 Because the more dylibs that you have the easier it is to build and re-link your app in and the faster your development cycles are.
因为dylib越多，构建和重新链接应用程序就越容易，开发周期也就越快。

 So you absolutely can and should use some, but it's good to try to target a limited number, we would, I would say off hand, a good target's about a half a dozen.
所以你完全可以，也应该使用一些，但最好是针对有限的数量，我们会，我想说，一个好的目标是大约六个。

 So now that we've fixed up our dylib count let's move on to the next place where we're having a slowdown.
现在我们已经解决了dylib计数的问题让我们进入下一个减速的地方。

 Between 350 milliseconds in binding and rebasing.
在350毫秒之间的绑定和重定位。

 So as Nick mentioned, rebasing tends to be slower due to iO and binding tends to be computationally expensive but it's already done the iO.
正如Nick提到的，由于iO的原因，重设基的速度会变慢绑定的计算成本也会变高但它已经完成了iO。

 So that iO is for both of them and they're comingled, the timing's also comingled.
所以iO是为他们俩准备的，他们来了，时机也来了。

 So if we go in and look at that, all that is fixing up pointers in the DATA section.
如果我们进去看一下，所有这些都是固定数据部分的指针。

 So what we have to do, is just fix up fewer pointers.
所以我们要做的，就是修复更少的指针。

 Nick showed you a tool you can run to see what pointers are being fixed up in the DATA, section, dyld info.
Nick向您展示了一个工具，您可以运行它来查看在DATA、section、dyld info中正在修复哪些指针。

 And it shows what segments and sections things are in, so that will give you a good idea of what's being fixed up.
它展示了事物的部分和部分，这能让你很好地了解正在被修复的东西。

 For instance, if you see a symbol to an ObjC class in ObjC section, that's probably that you have a bunch of ObjC classes.
例如，如果您在ObjC部分中看到ObjC类的符号，那么您可能有一堆ObjC类。

 So, one of the things you can do is you can just to reduce the number of ObjC classes object and ivars that you have.
你可以做的一件事就是减少你拥有的ObjC类object和ivar的数量。

 So there are a number of coding styles that are encouraging very small classes, that maybe only have one or two functions.
因此，有许多编码风格鼓励非常小的类，它们可能只有一两个函数。

 And, those particular patterns may result in gradual slowdowns of your applications as you add more and more of them.
而且，随着您添加越来越多的应用程序，这些特定模式可能会导致应用程序逐渐变慢。

 So you should be careful about those.
所以你应该小心这些。

 Now having 100 or 1,000 classes isn't a problem, but we've seen apps with 5, 10, 15, 20,000 classes.
现在有100或1000个类不是问题，但是我们已经看到应用程序有5、10、15、2万个类。

 And in those cases that can add up to 7 or 800 milliseconds to your launch time for the kernel to page them in.
在这些情况下，内核将它们页入的启动时间将增加7或800毫秒。

 Another thing you can do is you can try to reduce your use of C++ virtual functions.
您可以做的另一件事是尽量减少使用c++虚函数。

 So virtual functions create what we call V tables, which are the same as ObjC metadata in that in the sense that they create structures in the DATA section that have to be fixed up.
因此虚函数创建了我们称之为V表的东西，这和ObjC元数据是一样的，因为它们在数据部分创建了需要修复的结构。

 They're smaller than ObjC, they're smaller than ObjC metadata but they're still significant for some applications.
它们比ObjC要小，比ObjC元数据要小但是它们对一些应用来说仍然很重要。

 You can use Swift structs.
可以使用Swift structs。

 So Swift tends to use less data that has pointers for fix-ups of this sort.
因此，Swift倾向于使用较少的数据，而这些数据具有用于此类修复的指针。

 And, Swift is more inlinable and can better co-gen to avoid a lot of that, so migrating to Swift is a great way to improve this.
而且，Swift更容易嵌入，并且能够更好地协同工作以避免这种情况的发生，因此迁移到Swift是一种很好的改进方式。

 And one other thing, you should be careful about machine generated codes, so we have instances where, you may describe some structures in terms of a DSL or some custom language and then have a program that generates other code from it.
另一件事，你应该小心机器生成的代码，所以我们有这样的例子，你可以用DSL或自定义语言描述一些结构，然后有一个程序从它生成其他代码。

 And if those generated programs have a lot of pointers in them, they can become very expensive because when you generate your code you can generate very, very large structures.
如果这些生成的程序中有很多指针，它们会变得非常昂贵，因为当你生成代码时，你可以生成非常非常大的结构。

 We've seen cases where, this causes megabytes and megabytes of data.
我们已经看到，这会导致兆字节的数据。

 But the upside is you usually have a lot of control because you can just change the code generator to use something that's not pointers, for instance offset based, structures.
但好处是你通常有很多的控制，因为你可以改变代码生成器使用一些不是指针的东西，例如基于偏移量的结构。

 And that will be a big win.
这将是一个巨大的胜利。

 So in this case, let's look at what's going on here with my, with my load time.
在这种情况下，我们看看这里发生了什么，加载时间。

 And I have at least 10,000 classes, I actually have 20,000, so many it scrolled off the slide.
我至少有1万门课，实际上我有2万门课，很多都是从幻灯片上滚落下来的。

 And if I cut it down to 1,000 classes, I just cut my launch times, my time in this part of the launch from 350 to 20 milliseconds.
如果我把它减少到1000个类，我就减少了我的启动时间，我在这部分的时间从350毫秒减少到20毫秒。

 So, now, everything but the initializer is actually below that 400 millisecond mark, so we're doing pretty good.
现在，除了初始化器，其他的都低于400毫秒，我们做得很好。

 So for ObjC set up, well Nick mentioned everything it had to do.
对于ObjC的建立，尼克提到了它必须做的一切。

 It had to do class registration, it has to deal with the non-fragile ivars, it has to do category registration and it has to do selector uniquing.
它必须做类注册，它必须处理非易碎ivar，它必须做类别注册它必须做选择器唯一。

 And I'm not going to spend much time on this one at all, and the reason I'm not is, we solved all of those by fixing up the rebasing and data, and binding before.
我不打算在这上面花太多时间，我不打算花太多时间的原因是，我们解决了所有这些问题通过重新建立基础和数据，以及之前的绑定。

 All the reductions there are going to be the same thing you want to do here.
这里所有的还原都是一样的。

 So we just get a little bit of a free win here, it's small.
所以我们在这里得到了一点免费的胜利，它很小。

 It's 8 milliseconds.
这是8毫秒。

 But we didn't do anything explicit for it.
但是我们没有做任何明确的事情。

 And now finally, we're going to look at my initializers which are the big 10 seconds here.
最后，我们来看看我的初始化器它们是这里最重要的10秒钟。

 So I'm going to go a little more in depth on this than Nick did.
所以我要比尼克讲得更深入一点。

 There are two types of initializers, explicit initializers, things like +load.
有两种类型的初始化器，显式初始化器，比如+load。

 As Nick said we recommend replacing that with +initialize, which will cause the ObjC run time to initialize your code when the classes were substantiated instead of when the file is loaded.
正如Nick所说，我们建议用+initialize替换它，这将导致ObjC运行时在类被证实时初始化代码，而不是在加载文件时初始化代码。

 Or, in C/C++ there's an attribute that can be put onto functions which will cause it to, generate those as initializers, so that's an explicit initializer, that we just rather you didn't use.
或者，在C/ c++中，有一个属性可以放到函数上，它会生成那些初始化器，这是一个显式初始化器，我们只是希望你不用。

 We rather you replace them with call site initializers.
我们宁愿您用调用站点初始化器替换它们。

 So by call site initializers I mean things like dispatch once.
所谓调用站点初始化器，我指的是像分派一次这样的事情。

 Or if you're in cross platform code, pthread once.
或者，如果您使用的是跨平台代码，请使用一次pthread。

 Or if you're in C++ code, std once.
或者如果你在c++代码中，std一次。

 All these functions have basically the same sort of functionality where, any code in one of these blocks will be executed the first time its hit and only that.
所有这些函数都具有基本相同的功能，其中一个块中的任何代码都将在第一次命中时执行，仅此而已。

 Dispatch once is very, very optimized in our system.
调度一次在我们的系统中是非常非常优化的。

 After the first execution of it, it's basically equivalent to a no op running past it, so I highly recommend that instead of using, explicit initializers.
在第一次执行之后，它基本上等价于运行在它之上的no op，所以我强烈建议不要使用显式初始化器。

 So let's move on to implicit initializers.
我们继续讲隐式初始化器。

 So inplicit initializers are what Nick described mostly from C++ globals with non-trivial initializers, with non-trivial constructors.
因此，应用初始化器是Nick在c++全局变量中描述的，其中包含了非平凡的初始化器和非平凡的构造函数。

 And one option is you can replace those with call site initializers like we just mentioned.
一种选择是，你可以用我们刚才提到的调用站点初始化器替换它们。

 There's certainly places where you can place globals with non-global structures or pointers to objects that you will initialize.
当然，您可以在某些地方放置具有非全局结构的全局变量，或者指向要初始化的对象的指针。

 Another option is that you don't have non-trivial initializers.
另一个选项是，您没有非平凡的初始化器。

 So in C++ there's initializers called a POD a plain old data.
在c++中有一个初始化器叫做POD一个普通的旧数据。

 And if you're objects are just plain old datas, the static, or the static linker will pre-calculate all the data for the DATA section, lay it out as just data seen there, it doesn't have to be run, it doesn't have to be fixed up.
如果你的对象只是普通的旧数据，静态的，或者静态链接器会预先计算数据部分的所有数据，就像这里看到的数据一样，它不需要运行，也不需要固定。

 Finally, it can be really hard to find these, because they're implicit, but we have a warning in the compiler -Wglobal-constructors and if you do that it will give you warnings whenever you're generating one of these.
最后，很难找到这些，因为它们是隐式的，但我们在编译器- wglobal -构造函数中有一个警告，如果你这样做，它会在你生成其中一个时给你警告。

 So it's good to add that to the flags your compiler uses.
因此，最好将其添加到编译器使用的标志中。

 Another option is just to rewrite them in Swift.
另一种选择是快速重写它们。

 And the reason is, Swift has global variables and they'll be initialized, they're guaranteed to be initialized before you use them.
原因是，Swift有全局变量它们会被初始化，它们保证在你使用它们之前被初始化。

 But the way it does it, instead, is instead of using an initializer, it, behind the scenes, uses dispatch once for you.
但它的实现方式是，在后台，它不会使用初始化器，而是为您使用一次分派。

 It uses one of those call site initializers.
它使用其中一个调用站点初始化器。

 So moving to Swift will take care of this for you, so I highly encourage it that's an option.
所以转向斯威夫特会帮你们解决这个问题，所以我非常鼓励你们这样做这是一个选择。

 Finally, in your initializers please don't call dlopen, that will be a big performance hit for a bunch of reasons.
最后，在您的初始化器中，请不要调用dlopen，因为许多原因，这将是性能的一大打击。

 When dyld's running it's before the app has started and, we can do things like turn off our locking, because we're single threaded.
dyld运行的时候它还没启动，我们可以关闭锁定，因为我们是单线程的。

 As soon as dlopens happened, in those situations, the graph of how our initializers have to run changes, we could have multiple threads, we have to turn on locking, it's just going to be a big performance mess.
一旦dlopen发生，在这些情况下，初始化器如何运行变更的图表，我们可能有多个线程，我们必须打开锁定，这将是一个巨大的性能混乱。

 You also can have subtle deadlocking and undefined behaviors.
您还可以有微妙的死锁和未定义的行为。

 Also, please don't start threads in your initializers, basically for the same reason.
另外，请不要在初始化器中启动线程，原因基本相同。

 You can set up a mute text if you have to and mute text even have like, preferred mute texts even have, predefined static values that you can set them up with that run no code.
你可以设置一个静音文本，如果你必须，静音文本，甚至有，首选静音文本，预定义的静态值，你可以设置它们，不运行代码。

 But actually starting a thread in your initializer is, potentially a big performance and correctness issue.
但实际上在初始化器中启动线程是一个潜在的性能和正确性问题。

 So here we have some code, I have a C++ class with a non-trivial initializer.
这里我们有一些代码，我有一个c++类，它有一个重要的初始化器。

 I'm having trouble with the connection.
我的连接有问题。

 Please try again in a moment.
请稍候再试。

 Well, thank you Siri.
谢谢你，Siri。

 I'm having a, I have a non-trivial initializer.
我有一个非平凡的初始化器。

 And I guess I had it in for debugging all commented out and okay, I'm down to 50 milliseconds, total.
我想我已经调试好了所有注释掉了，总共只有50毫秒。

 I have plenty of time to initialize my nibs and do everything else, we're in very good shape.
我有足够的时间初始化nib和做其他的事情，我们的状态非常好。

 So now that we've gone through that, let's talk about what we should know if you just, this was really long and pretty dense.
现在我们已经讨论过了，我们来讨论一下我们应该知道的如果你只是，这个真的很长很密集。

 The first one is please use dyld print statistics to measure your times, add it to your performance or aggression suites.
第一个是请使用dyld打印统计数据来衡量您的时间，将其添加到您的性能或攻击套件中。

 So you can track how your app is performing over time, so as you're actively doing something you don't find it months later and have trouble debugging it.
所以你可以跟踪你的应用程序在一段时间内的表现，所以当你在积极地做一些事情的时候，你几个月后就找不到它了，调试起来也很困难。

 You can improve your app launch time by, reducing the number of dylibs you have, reducing the amount of ObjC classes you have, and eliminating your static initializers.
你可以通过减少dylibs的数量，减少ObjC类的数量，消除静态初始化器来提高应用程序的启动时间。

 And you can improve in general by using more Swift because it just does the right things.
通常情况下，你可以通过使用更快的速度来提高，因为这样做是正确的。

 Finally, dlopen usage is discouraged, it causes subtle performance issues that are hard to diagnose.
最后，不鼓励使用dlopen，它会导致难以诊断的细微性能问题。

 For more information you can see the URL up on screen.
有关更多信息，请参见屏幕上的URL。

 There are several related sessions later in the week and again, there's the app performance session from 2012 that goes into the other parts of app launch, that highly recommend you watch, if you're interested.
本周晚些时候会有几个相关的会议，2012年的app性能会议会涉及到app发布的其他部分，如果你感兴趣的话，强烈推荐你观看。

 Thank you for coming everybody, have a great week.
谢谢大家的到来，祝大家度过愉快的一周。

