---
layout:     post  
title:      The main pillars of learning programming — and why beginners should master them.
subtitle:   学习编程的主要支柱——以及为什么初学者应该掌握它们
date:      2019-01-25 21:40:00
author:     "Dan"
header-img: "img/the-main-pillars-of-learning-programming.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---



## The main pillars of learning programming — and why beginners should master them. 学习编程的主要支柱——以及为什么初学者应该掌握它们。

![](/img/15484216327623.jpg)


I have been programming for more than 20 years. During that time, I’ve had the pleasure to work with many people, from whom I learned a lot. I’ve also worked with many students, coming fresh from university, with whom I had to take on the role of a teacher or mentor.
我已经编程20多年了。在这段时间里，我很高兴和很多人一起工作，从他们身上我学到了很多。我也和很多刚从大学毕业的学生一起工作，我不得不扮演老师或导师的角色。

Lately, I have been involved as a trainer in a program that teaches coding to absolute beginners.
最近，我作为一名培训师参与了一个向绝对初学者教授编程的项目。

Learning how to program is hard. I often find that university courses and bootcamps miss important aspects of programming and take poor approaches to teaching rookies.
学习如何编程是困难的。我经常发现，大学课程和训练营忽略了编程的重要方面，在教授新手时采取了糟糕的方法。

I want to share the five basic pillars I believe a successful programming course should build upon. As always, I am addressing the context of mainstream web applications.
我想分享我认为一门成功的编程课程应该建立在的五个基本支柱。像往常一样，我正在处理主流web应用程序的上下文。

A rookie’s goal is to master the fundamentals of programming and to understand the importance of libraries and frameworks.
新手的目标是掌握编程的基础知识，理解库和框架的重要性。

Advanced topics such as the cloud, operations in general, or build tools should not be part of the curriculum. I am also skeptical when it comes to Design Patterns. They presume experience that beginners never have.
诸如云计算、一般操作或构建工具等高级主题不应该成为课程的一部分。当涉及到设计模式时，我也持怀疑态度。他们假定初学者从未有过的经验。

So let’s look at where new programmers should start.
让我们看看新程序员应该从哪里开始。

### Test-Driven Development (TDD) 测试驱动的开发(TDD)
![](/img/15484216568411.jpg)


TDD brings a lot of benefits. Unfortunately, it is an advanced topic that beginners are not entirely ready for.
TDD带来了很多好处。不幸的是，这是一个初学者还没有完全准备好的高级主题。

Beginners shouldn’t write tests. This would be too much for their basic skill levels. Instead, they should learn how to use and work with tests.
初学者不应该编写测试。这对他们的基本技能水平来说是太高了。相反，他们应该学习如何使用和使用测试。

Each programming course should center around exercises. I extend my exercises with unit tests and provide the students an environment which is already setup for running those tests.
每门编程课程都应该以练习为中心。我用单元测试扩展了我的练习，并为学生提供了一个运行这些测试的环境。

All the students have to do is write their code and then watch the lights of the testrunner turning from red to green. The resulting gamification is a nice side effect.
所有的学生所要做的就是写他们的代码，然后看着testrunner的灯从红色变成绿色。由此产生的游戏化是一个很好的副作用。

For example: If the selected technology is Spring, I provide the exercises and tests within a Spring project. The students don’t need to know anything about Spring. All they need to know is the location of the exercises and the button to trigger the tests.
例如:如果选择的技术是Spring，我将在Spring项目中提供练习和测试。学生们不需要知道关于春天的任何事情。他们只需要知道练习的位置和触发测试的按钮。

Additionally, students must know how to use a debugger and have a Read-Eval-Print Loop (REPL) handy. The ability to analyse code during runtime and to have a playground for small experiments is essential in TDD.
此外，学生必须知道如何使用调试器，并有一个读- eval - print循环(REPL)方便。在TDD中，在运行时分析代码的能力和为小型实验提供场所的能力是必不可少的。

The main point is to ensure students don’t have to learn basic TDD behaviours after they’ve acquired core programming skills. Changing habits later in the students’ career will be much harder than learning those habits now. That’s why they should live and breath unit tests from the beginning.
重点是确保学生在掌握了核心编程技能后，不需要学习基本的TDD行为。在学生以后的职业生涯中改变习惯要比现在学习这些习惯困难得多。这就是为什么他们应该从一开始就进行生存和呼吸单元测试。

Later in their professional life, they should have an antipathy for projects without unit tests. They should intuitively see the absence of unit tests as anti-pattern.
在他们以后的职业生涯中，他们应该对没有单元测试的项目产生反感。他们应该直观地将缺少单元测试视为反模式。

### Fundamentals First 基本面第一

![](/img/15484216800766.jpg)


I hear very often that rookies should immediately start with a framework. This is like teaching people how to drive by placing them in a rally car and asking them to avoid oversteering. This simply ignores the fact that they still mistake the brake for the throttle.
我经常听到新手应该立即从框架开始。这就像教人们如何驾驶，把他们放在拉力赛车，并要求他们避免过度转向。这完全忽略了一个事实，他们仍然把刹车错当成油门。

The same applies when we start students with a framework like Angular. Beginners need to understand the fundamentals of programming first. They need to be familiar with the basic elements and what it means to write code before they can use somebody else’s.
同样的道理也适用于像Angular这样的框架。初学者首先需要了解编程的基础知识。在使用其他人的代码之前，他们需要熟悉基本的元素和编写代码的含义。

The concept of a function, a variable, a condition, and a loop are completely alien to novices. These four elements build the foundations of programming. Everything a program is made of relies on them.
函数、变量、条件和循环的概念对初学者来说是完全陌生的。这四个元素构成了编程的基础。程序的所有组成部分都依赖于它们。

Students are hearing these concepts for the very first time, but it is of the utmost importance that the students become proficient with them. If students do not master the fundamentals, everything that follows looks like magic and leads to confusion and frustration.
学生们是第一次听到这些概念，但最重要的是让学生熟练掌握它们。如果学生没有掌握基础知识，接下来的一切就像魔术一样，会导致困惑和挫折。

Teachers should spend more time on these fundamentals. But, sadly, many move on far too quickly. The problem is that some teachers struggle to put themselves into the role of a student. They have been programming for ages and have forgotten what types of problems a beginner has to deal with. It is quite similar to a professional rally driver. He can’t imagine that somebody needs to think before braking. He just does it automatically.
教师应该在这些基础上花更多的时间。但遗憾的是，许多人的进步太快了。问题是，有些老师很难把自己放在学生的角色上。他们已经编程很长时间了，已经忘记了初学者需要处理的问题类型。它非常类似于职业拉力赛车手。他无法想象有人需要在刹车前思考。他只是机械地做。

I design my exercises so that they are challenging but solvable in a reasonable amount of time by using a combination of the four main elements.
我设计我的练习，使他们具有挑战性，但可以解决在合理的时间内使用四个主要元素的组合。

A good example is a converter for Roman and Arabic numbers. This challenge requires patience from the students. Once they successfully apply the four elements to solve the challenge, they also get a big boost in motivation.
一个很好的例子是罗马数字和阿拉伯数字的转换器。这个挑战需要学生们的耐心。一旦他们成功地运用这四个要素来解决挑战，他们的动力也会得到很大的提升。

Fundamentals are important. Don’t move on until they are settled.
基础是很重要的。在他们解决之前不要离开。

### Libraries and Frameworks 库和框架

![](/img/15484217056057.jpg)


After students spend a lot of time coding, they must learn that most code already exists in the form of a library or a framework. This is more a mindset than a pattern.
在学生花费大量时间编码之后，他们必须了解大多数代码已经以库或框架的形式存在。这与其说是一种模式，不如说是一种心态。

As I have written before: Modern developers know and pick the right library. They don’t spend hours writing a buggy version on their own.
正如我以前所写的:现代开发人员知道并选择正确的库。他们不会花几个小时自己编写有bug的版本。

To make that mindset transition a success, the examples from the “fundamentals phase” should be solvable by using well-known libraries like Moment.js, Jackson, Lodash, or Apache Commons.
要使这种心态转变成功，“基础阶段”的例子应该可以通过使用著名的库(如Moment)来解决。js、Jackson、Lodash或Apache Commons。

This way, students will immediately understand the value of libraries. They crunched their heads around those complicated problems. Now they discover that a library solves the exercise in no time.
这样，学生就会立刻明白图书馆的价值。他们对那些复杂的问题绞尽脑汁。现在他们发现库可以在短时间内解决这个问题。

Similar to TDD, students should become suspicious when colleagues brag about their self-made state management library that makes Redux unnecessary.
与TDD类似，当同事吹嘘自己创建的状态管理库使Redux变得不必要时，学生应该产生怀疑。

When it comes to frameworks, students will have no problem understanding the importance once they understand the usefulness of libraries.
当涉及到框架时，一旦学生理解了库的有用性，他们就会毫不费力地理解其重要性。

Depending on the course’s timeframe, it may be hard to devote time to frameworks. But as I already pointed out, the most important aspect is shifting the mindset of the student away from programming everything from scratch to exploring and using libraries.
根据课程的时间框架，可能很难花时间在框架上。但是正如我已经指出的，最重要的方面是改变学生的思维方式，从从头开始编程到探索和使用库。

I did not add tools to this pillar, since they are only of use to experienced developers. At this early stage, students do not need to learn how to integrate and configure tools.
我没有向这个支柱添加工具，因为它们只对有经验的开发人员有用。在这个早期阶段，学生不需要学习如何集成和配置工具。

### Master & Apprentice 主和学徒

![](/img/15484217228451.jpg)


In my early 20s I wanted to learn to play the piano. I did not want a teacher, and thought I could learn it by myself. Five years later, I consulted a professional tutor. Well, what can I say? I’ve learned more in 1 month than during the five years before.
在我20岁出头的时候，我想学习弹钢琴。我不想要老师，我想我可以自学。五年后，我咨询了一位专业导师。我能说什么呢?我一个月学到的东西比五年前多。

My piano teacher pointed out errors in my playing I couldn’t hear and made me aware of interpretational things I never would have imagined. After all, she instilled in me the mindset for music and art, both of which were out of reach for me as a technical person.
我的钢琴老师指出了我演奏时听不到的错误，让我意识到一些我从未想过的诠释性的东西。毕竟，她给我灌输了音乐和艺术的思维方式，而这两者都是我作为一个技术人员所无法企及的。

It is the same in programming. If somebody has no experience in programming, then self-study can be a bad idea. Although there are many success stories, I question the efficiency of doing it alone.
编程也是一样。如果某人没有编程经验，那么自学可能是一个坏主意。虽然有很多成功的故事，但我怀疑独自完成这件事的效率。

Instead, there should be a “master & apprentice” relationship. In the beginning, the master gives rules the apprentice must follow — blindly! The master may explain the rules, but usually the reasoning is beyond the apprentice’s understanding.
相反，应该有一种“师徒关系”。一开始，师傅给了徒弟必须遵守的规则——盲从!师傅可以解释规则，但通常推理是徒弟无法理解的。

These internalised rules form a kind of safety net. If one gets lost, one always has some safe ground to return to.
这些内部规则形成了一种安全网。如果一个人迷路了，他总能找到安全的地方。

Teaching should not be a monologue. The master has to deal with each student individually. He should check how the students work, give advice, and adapt the speed of the course to their progress.
教学不应该是独白。校长必须单独与每个学生打交道。他应该检查学生是如何工作的，给出建议，并根据他们的进步调整课程的速度。

Once the apprentices reach a certain level of mastery, they should be encouraged to explore new territory. The master evolves into a mentor who shares “wisdom” and is open for discussions.
一旦学徒达到一定程度的掌握，他们应该被鼓励去探索新的领域。大师变成了一个分享“智慧”并愿意讨论的导师。

### Challenge and Motivation  挑战和激励

![](/img/15484217401332.jpg)


“Let’s create a Facebook clone!” This doesn’t come from a CEO backed by a horde of senior software developers and a multi-million euro budget. It is an exercise from an introductory course for programmers. Such an undertaking is virtually impossible. Even worse, students are put into wonderland and deluded into believing they have skills that are truly beyond their reach.
“让我们创建一个Facebook克隆!”这不是一个由一群高级软件开发人员和数百万欧元预算支持的首席执行官说的。它是程序员入门课程中的一个练习。这样的工作实际上是不可能的。更糟糕的是，学生们被带入仙境，并被骗相信他们拥有真正超出他们能力范围的技能。

No doubt the teacher is aware of that, but creates such exercises for motivational reasons.
毫无疑问，老师知道这一点，但创建这样的练习动机的原因。

The main goal of an exercise is not to entertain. It should be created around a particular technique and should help the students understand that technique.
练习的主要目的不是娱乐。它应该围绕一种特定的技术来创建，并且应该帮助学生理解这种技术。

Motivation is good, but not at the sacrifice of content. Programming is not easy. If the students don’t have an intrinsic motivation, coding might not be the way to go.
动机是好的，但不以牺牲内容为代价。编程并不容易。如果学生没有内在的动机，那么编写代码可能不是正确的选择。

Newbies should experience what it means to be a professional developer. They should know what awaits them before they invest lots of hours.
新手应该体验一下成为专业开发人员意味着什么。在投入大量时间之前，他们应该知道等待他们的是什么。

For example, many business applications center around complex forms and grids. Creating these is an important skill that exercises can impart. Building an application similar to Facebook might not be the best lesson for students to learn right away.
例如，许多业务应用程序以复杂的表单和网格为中心。创造这些是练习可以传授的重要技能。构建一个类似于Facebook的应用程序可能不是学生马上要学习的最好的课程。

Similarly, a non-programmer might be surprised at how few code lines a developer writes per day. There are even times where we remove code or achieve nothing.
类似地，非程序员可能会惊讶于开发人员每天编写的代码行如此之少。甚至有时候我们删除代码或者什么也没做。

Why? Because things go wrong all the time. We spend endless hours fixing some extremely strange bugs that turn out to be a simple typo. Some tool might not be working just because a library got a minor version upgrade. Or the system crashes because somebody forgot to add a file to git. The list can go on and on.
为什么?因为事情总是出错。我们花了无数的时间来修复一些非常奇怪的错误，结果只是一个简单的打印错误。有些工具可能不能正常工作，仅仅因为库进行了小的版本升级。或者系统崩溃，因为有人忘记向git添加文件。这样的例子不胜枚举。

Students should enjoy these experiences. An exercise targeting an unknown library under time pressure might be exactly the right thing. ;)
学生应该享受这些经历。一个针对时间压力下的未知库的练习可能是正确的。,)

The sun isn’t always shining in real life. Beginners should be well-prepared for the reality of programming.
现实生活中的太阳并不总是灿烂的。初学者应该对编程的现实做好充分的准备。

### Final Advice  最后的建议

Last but not least: One cannot become a professional programmer in two weeks, two months or even a year. It takes time and patience.
最后但并非最不重要:一个人不可能在两周、两个月甚至一年内成为一名专业程序员。这需要时间和耐心。

Trainers should not rush or make false promises. They should focus on whether students understand the concepts and not move on too fast.
培训师不应操之过急或做出虚假承诺。他们应该关注学生是否理解这些概念，而不是进展太快。





原文: https://medium.freecodecamp.org/the-main-pillars-of-learning-programming-and-why-beginners-should-master-them-e04245c17c56

