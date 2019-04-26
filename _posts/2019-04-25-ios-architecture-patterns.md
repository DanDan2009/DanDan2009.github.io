---
layout:     post  
title:      iOS Architecture Patterns
subtitle:   iOS架构模式
date:      2019-04-25 21:40:00
author:     "Dan"
header-img: "img/ios-architecture-patterns.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---


##iOS Architecture Patterns
iOS架构模式

>
Demystifying MVC, MVP, MVVM and VIPER
揭开MVC, MVP, MVVM和VIPER的神秘面纱

![](/img/15561941001752.jpg)

Don’t miss the [iOS Developer Roadmap](https://github.com/BohdanOrlov/iOS-Developer-Roadmap) for 2018!

UPD: Slides which I presented at NSLondon available [here](http://slides.com/borlov/arch/fullscreen).
UPD:我在NSLondon展示的幻灯片可以在这里找到。

Feeling weird while doing MVC in iOS? Have doubts about switching to MVVM? Heard about VIPER, but not sure if it worth it?
在iOS中使用MVC感觉奇怪吗?对切换到MVVM有疑问吗?听说过蝮蛇，但不确定是否值得?

Keep reading, and you will find answers to questions above, if you don’t — feel free to complain in comments.
继续阅读，你会发现上面问题的答案，如果你没有——请在评论中自由抱怨。

You are about to structure your knowledge about architectural patterns in iOS environment. We’ll briefly review some popular ones and compare them in theory and practice going over a few tiny examples. Follow links if you need more details about any particular one.
您将在iOS环境中构建关于体系结构模式的知识。我们将简要回顾一些流行的例子，并在理论和实践中比较它们。如果您需要关于任何一个特定的详细信息，请跟随链接。

Mastering design patterns might be addictive, so beware: you might end up asking yourself more questions now than before reading this article, like these:
掌握设计模式可能会上瘾，所以要注意:现在你可能会比阅读本文之前问自己更多的问题，比如:

Who supposed to own networking request: a Model or a Controller?
谁应该拥有网络请求:模型还是控制器?

How do I pass a Model into a View Model of a new View?
如何将模型传递到新视图的视图模型中?

Who creates a new VIPER module: Router or Presenter?
谁创建了一个新的毒蛇模块:路由器还是演示程序?


![](/img/15561945896765.jpg)


### Why care about choosing the architecture?
为什么要关心选择架构呢?

Because if you don’t, one day, debugging a huge class with dozens different things, you’ll find yourself being unable to find and fix any bugs in your class.”. Naturally, it is hard to keep this class in mind as whole entity, thus, you’ll always be missing some important details. If you are already in this situation with your application, it is very likely that:
因为如果你不这样做，有一天，调试一个包含许多不同内容的大型类时，你会发现自己无法找到并修复类中的任何bug。”当然，很难将这个类作为一个整体记住，因此，您总是会遗漏一些重要的细节。如果你的申请已经处于这种情况，很有可能:

* This class is the UIViewController subclass.
这个类是UIViewController子类。

* Your data stored directly in the UIViewController
你的数据直接存储在UIViewController中

* Your UIViews do almost nothing
你的uiview几乎什么都不做

* The Model is a dumb data structure
该模型是一种哑数据结构

* Your Unit Tests cover nothing
您的单元测试没有覆盖任何内容

And this can happen, even despite the fact that you are following Apple’s guidelines and implementing Apple’s MVC pattern, so don’t feel bad. There is something wrong with the Apple’s MVC, but we’ll get back to it later.
这是有可能发生的，即使你遵循了苹果的指导方针并实现了苹果的MVC模式，所以不要感到难过。苹果的MVC有一些问题，但是我们稍后会回到它。

Let’s define features of a good architecture:
让我们定义一个好的架构的特性:

1. Balanced distribution of responsibilities among entities with strict roles.
   在具有严格角色的实体之间平衡地分配职责。

2. Testability usually comes from the first feature (and don’t worry: it is easy with appropriate architecture).
    可测试性通常来自第一个特性(不要担心:使用适当的体系结构很容易)。

3. Ease of use and a low maintenance cost.
   使用方便，维护成本低。

####  Why Distribution?
为什么分布?

Distribution keeps a fair load on our brain while we trying to figure out how things work. If you think the more you develop the better your brain will adapt to understanding complexity, then you are right. But this ability doesn’t scale linearly and reaches the cap very quickly. So the easiest way to defeat complexity is to divide responsibilities among multiple entities following the single responsibility principle.
当我们试图弄清楚事物是如何运作的时候，分布在我们的大脑中保持着相当大的负荷。如果你认为你发展得越多，你的大脑就会更好地适应理解复杂性，那么你是对的。但是这种能力并不是线性增长的，并且会很快达到上限。因此，克服复杂性的最简单方法是按照单一责任原则在多个实体之间划分责任。

#### Why Testability?
可测试性的原因吗?

This is usually not a question for those who already felt gratitude to unit tests, which failed after adding new features or due to refactoring some intricacies of the class. This means the tests saved those developers from finding issues in runtime, which might happen when an app is on a user’s device and the fix takes a week to reach the user.
对于那些已经对单元测试心存感激的人来说，这通常不是一个问题，单元测试在添加新特性之后失败了，或者由于重构了类的一些复杂之处而失败了。这意味着这些测试可以让开发人员避免在运行时发现问题，而当应用程序位于用户的设备上，而修复程序需要一周时间才能到达用户时，可能会出现这种情况。

#### Why Ease of use?
为什么是易用性?

This does not require an answer but it is worth mentioning that the best code is the code that has never been written. Therefore the less code you have, the less bugs you have. This means that desire to write less code should never be explained solely by laziness of a developer, and you should not favour a smarter solution closing your eyes to its maintenance cost.
这并不需要答案，但值得一提的是，最好的代码是从未写过的代码。因此，代码越少，bug就越少。这意味着，编写更少代码的愿望不应该仅仅由开发人员的懒惰来解释，而且您不应该对维护成本视而不见，而偏爱更智能的解决方案。

### MV(X) essentials
MV (X)的必需品

Nowadays we have many options when it comes to architecture design patterns:
现在，当涉及到架构设计模式时，我们有很多选择:

* MVC
* MVP
* MVVM
* VIPER


First three of them assume putting the entities of the app into one of 3 categories:
前三种假设是将应用的实体分为三类:

* Models — responsible for the domain data or a data access layer which manipulates the data, think of ‘Person’ or ‘PersonDataProvider’ classes.
 模型——负责域数据或操作数据的数据访问层，请考虑“Person”或“PersonDataProvider”类。

* Views — responsible for the presentation layer (GUI), for iOS environment think of everything starting with ‘UI’ prefix.
视图—负责表示层(GUI)，对于iOS环境，请考虑所有以“UI”开头的内容。

* Controller/Presenter/ViewModel — the glue or the mediator between the Model and the View, in general responsible for altering the Model by reacting to the user’s actions performed on the View and updating the View with changes from the Model.
Controller/Presenter/ViewModel—模型与视图之间的粘合剂或中介，通常负责通过响应用户在视图上执行的操作来更改模型，并使用来自模型的更改更新视图。

Having entities divided allows us to:
实体分裂使我们能够:

* understand them better (as we already know)
  更好地理解他们(正如我们已经知道的)

* reuse them (mostly applicable to the View and the Model)
  重用它们(主要适用于视图和模型)

* test them independently
  独立测试

Let’s start with MV(X) patterns and get back to VIPER later.
让我们从MV(X)模式开始，稍后再回到VIPER。

### MVC

#### How it used to be
过去怎么样

Before discussing Apple’s vision of MVC let’s have a look on the [traditional one](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).
在讨论苹果的MVC愿景之前，让我们先来看看传统的MVC。
![](/img/15562607037314.jpg)
Traditional MVC

In this case, the **View** is stateless. It is simply rendered by the **Controller** once the **Model** is changed. Think of the web page completely reloaded once you press on the link to navigate somewhere else. Although it is possible to implement the traditional MVC in iOS application, it doesn’t make much sense due to the architectural problem — all three entities are tightly coupled, each entity **knows** about the other two. This dramatically reduces reusability of each of them — that is not what you want to have in your application. For this reason, we skip even trying to write a canonical MVC example.
在本例中，视图是无状态的。一旦模型被更改，控制器就会简单地呈现它。当您按下链接导航到其他位置时，请考虑完全重新加载web页面。虽然可以在iOS应用程序中实现传统的MVC，但由于架构问题，这没有多大意义——所有三个实体都是紧密耦合的，每个实体都知道其他两个。这极大地降低了它们的可重用性——这不是您希望在应用程序中拥有的。由于这个原因，我们甚至跳过了编写规范MVC示例。

Traditional MVC doesn't seems to be applicable to modern iOS development.
传统MVC似乎不适用于现代iOS开发。

### Apple’s MVC
苹果的MVC

#### Expectation
期望

![](/img/15562608418627.jpg)
Cocoa MVC

The **Controller** is a mediator between the **View** and the **Model** so that they don’t know about each other. The least reusable is the **Controller** and this is usually fine for us, since we must have a place for all that tricky business logic that doesn’t fit into the **Model**.
控制器是视图和模型之间的中介，因此它们彼此不了解。可重用性最差的是控制器，这对我们来说通常没有问题，因为我们必须为所有不适合模型的复杂业务逻辑提供一个位置。

In theory, it looks very straightforward, but you feel that something is wrong, right? You even heard people unabbreviating MVC as the **Massive View Controller**. Moreover, view controller offloading became an important topic for the iOS developers. Why does this happen if Apple just took the traditional MVC and improved it a bit?
理论上，这看起来很简单，但你觉得有些地方不对劲，对吧?你甚至听到有人将MVC缩写为大型视图控制器。此外，视图控制器卸载成为iOS开发者的一个重要课题。如果苹果只是采用传统MVC并对其进行了一些改进，为什么会出现这种情况呢?

### Apple’s MVC
苹果的MVC

#### Reality
现实

![](/img/15562610605038.jpg)
Realistic Cocoa MVC

Cocoa MVC encourages you to write **Massive** View Controllers, because they are so involved in **View’s** life cycle that it’s hard to say they are separate. Although you still have ability to offload some of the business logic and data transformation to the **Model**, you don’t have much choice when it comes to offloading work to the **View**, at most of times all the responsibility of the **View** is to send actions to the **Controller**. The view controller ends up being a delegate and a data source of everything, and is usually responsible for dispatching and cancelling the network requests and… you name it.
Cocoa MVC鼓励你编写大量的视图控制器，因为它们在视图的生命周期中是如此的重要以至于很难说它们是独立的。尽管你仍然有能力出售一些业务逻辑和数据转换模型,你没有多少选择卸载工作到视图时,大多数时候所有的责任的观点是将操作发送到控制器。视图控制器最终是一个委托和所有东西的数据源，通常负责分派和取消网络请求，以及……你能想到的。

How many times have you seen code like this:
你见过多少次这样的代码:

```
  
var userCell = tableView.dequeueReusableCellWithIdentifier("identifier") as UserCell
userCell.configureWithUser(user)

```

The cell, which is the **View** configured directly with the **Model**, so MVC guidelines are violated, but this happens all the time, and usually people don’t feel it is wrong. If you strictly follow the MVC, then you supposed to configure the cell from the controller, and don’t pass the **Model** into the **View**, and this will increase the size of your **Controller** even more.
单元格是直接用模型配置的视图，因此违反了MVC准则，但这种情况经常发生，通常人们并不觉得它是错的。如果严格遵循MVC，那么应该从控制器配置单元格，而不是将模型传递给视图，这将进一步增加控制器的大小。

>
Cocoa MVC is reasonably unabbreviated as the Massive View Controller.
Cocoa MVC被合理地简化为大型视图控制器。

The problem might not be evident until it comes to the Unit Testing (hopefully, it does in your project). Since your view controller is tightly coupled with the view, it becomes difficult to test because you have to be very creative in mocking views and their life cycle, while writing the view controller’s code in such a way, that your business logic is separated as much as possible from the view layout code.
在单元测试之前，这个问题可能并不明显(希望在您的项目中确实如此)。因为你的视图控制器是紧密耦合的观点,很难测试,因为在嘲弄你必须非常有创意的观点和他们的生命周期,在写视图控制器的代码以这样一种方式,你的业务逻辑分离尽可能从视图布局代码。

Let’s have a look on the simple playground example:
让我们来看一个简单的操场例子:

UPD: See updated code examples by Wasin Thonkaew
UPD:参见Wasin Thonkaew更新的代码示例

```

import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

class GreetingViewController : UIViewController { // View + Controller
    var person: Person!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.greetingLabel.text = greeting
        
    }
    // layout code goes here
}
// Assembling of MVC
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
view.person = model;

```

MVC example

>
MVC assembling can be performed in the presenting view controller
MVC组装可以在呈现视图控制器中执行

This doesn’t seem very testable, right? We can move generation of greeting into the new GreetingModel class and test it separately, but we can’t test any presentation logic (although there is not much of such logic in the example above) inside the GreetingViewController without calling the UIView related methods directly (viewDidLoad, didTapButton) which might cause loading all views, and this is bad for the unit testing.
这看起来不太可行，对吧?我们可以代问候进入新的GreetingModel类和单独测试它,但我们不能测试任何表示逻辑(尽管没有多少这样的逻辑在上面的示例中)在GreetingViewController没有直接调用UIView相关方法(viewDidLoad didTapButton)可能会导致加载所有视图,这不利于单元测试。

In fact, loading and testing UIViews on one simulator (e.g. iPhone 4S) doesn’t guarantee that it would work fine on the other devices (e.g. iPad), so I’d recommend to remove “Host Application” from your Unit Test target configuration and run your tests without your application running on simulator.
事实上，在一个模拟器(如iPhone 4S)上加载和测试uiview并不能保证它在其他设备(如iPad)上也能正常工作，因此我建议从单元测试目标配置中删除“Host Application”，并在模拟器上运行应用程序的情况下运行测试。

>
The interactions between the View and the Controller aren’t really testable with Unit Tests
视图和控制器之间的交互实际上不能通过单元测试进行测试

With all that said, it might seems that Cocoa MVC is a pretty bad pattern to choose. But let’s assess it in terms of **features** defined in the beginning of the article:
综上所述，选择Cocoa MVC似乎是一个非常糟糕的模式。但是让我们从文章开头定义的特性来评估它:

* **Distribution **— the View and the Model in fact separated, but the View and the Controller are tightly coupled.
分布——视图和模型实际上是分开的，但是视图和控制器是紧密耦合的。

* **Testability** — due to the bad distribution you’ll probably only test your Model.
可测试性——由于分布不好，您可能只测试您的模型。

* **Ease of use** — the least amount of code among others patterns. In addition everyone is familiar with it, thus, it’s easily maintained even by the unexperienced developers.
易用性——在其他模式中代码量最少。此外，每个人都熟悉它，因此，即使是没有经验的开发人员也很容易维护它。

Cocoa MVC is the pattern of your choice if you are not ready to invest more time in your architecture, and you feel that something with higher maintenance cost is an overkill for your tiny pet project.
如果您还没有准备好在体系结构上投入更多的时间，并且您觉得对于您的小型宠物项目来说，维护成本较高的东西是多余的，那么您可以选择Cocoa MVC模式。

>
Cocoa MVC is the best architectural pattern in terms of the speed of the development.
就开发速度而言，Cocoa MVC是最好的架构模式。

### MVP


#### Cocoa MVC’s promises delivered
Cocoa MVC的承诺已经交付

![](/img/15562624080372.jpg)
Passive View variant of MVP


Doesn’t it look exactly like the Apple’s MVC? Yes, it does, and it’s name is MVP (Passive View variant). But wait a minute… Does this mean that Apple’s MVC is in fact a MVP? No, its not, because if you recall, there, the **View** is tightly coupled with the **Controller**, while the MVP’s mediator, **Presenter**, has nothing to do with the life cycle of the view controller, and the **View** can be mocked easily, so there is no layout code in the **Presenter** at all, but it is responsible for updating the **View** with data and state.
它看起来不像苹果的MVC吗?是的，它是，它的名字是MVP(被动视图变体)。但是等一下，这是否意味着苹果的MVC实际上是MVP呢?不,它不是,因为如果你还记得,在那里,视图与控制器紧密耦合,MVP的调停人,主持人,无关的生命周期视图控制器,视图可以轻易嘲笑,所以没有主持人的布局代码,但它是负责更新视图与数据和状态。

>
What if I told you, the UIViewController is the View.
如果我告诉你UIViewController是视图。

In terms of the **MVP**, the UIViewController subclasses are in fact the **Views** and not the **Presenters**. This distinction provides superb testability, which comes at cost of the development speed, because you have to make manual data and event binding, as you can see from the example:
就MVP而言，UIViewController子类实际上是视图而不是呈现者。这种区别提供了极佳的可测试性，但这是以开发速度为代价的，因为您必须进行手工数据和事件绑定，从示例中可以看到:

```

import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
}

class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of MVP
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter

```
MVP example


#### Important note regarding assembly
关于装配的重要说明

The MVP is the first pattern that reveals the assembly problem which happens due to having three actually separate layers. Since we don’t want the **View** to know about the **Model**, it is not right to perform assembly in presenting view controller (which is the **View**), thus we have to do it somewhere else. For example, we can make the app-wide **Router** service which will be responsible for performing assembly and the **View-to-View** presentation. This issue arises and has to be addressed not only in the MVP but also in **all the following patterns**.
MVP是第一个揭示装配问题的模式，这是由于有三个实际独立的层。由于我们不想让视图知道模型，所以在呈现视图控制器(即视图)时执行assembly是不正确的，因此我们必须在其他地方执行。例如，我们可以使应用程序范围的路由器服务，它将负责执行组装和视图到视图的表示。这个问题不仅在MVP中出现，而且在以下所有的模式中都必须得到解决。

Let’s look on the **features** of the MVP:
让我们来看看MVP的特点:

* **Distribution** — we have the most of responsibilities divided between the Presenter and the Model, with the pretty dumb View (in the example above the Model is dumb as well).
分布——我们在演示者和模型之间分配了大部分职责，并使用了相当愚蠢的视图(在上面的例子中，模型也是愚蠢的)。

* **Testability** — is excellent, we can test most of the business logic due to the dumb View.
可测试性——非常好，由于哑视图，我们可以测试大部分业务逻辑。

* **Easy of use **— in our unrealistically simple example, the amount of code is doubled compared to the MVC, but at the same time, idea of the MVP is very clear.
易于使用——在我们这个不切实际的简单示例中，与MVC相比，代码量增加了一倍，但与此同时，MVP的概念非常清晰。

>
MVP in iOS means superb testability and a lot of code.
MVP在iOS中意味着出色的可测试性和大量的代码。

### MVP
最有价值球员

#### With Bindings and Hooters
用绳子和帽子

There is the other flavour of the MVP — the Supervising Controller MVP. This variant includes direct binding of the **View** and the **Model** while the **Presenter** (The Supervising Controller) still handles actions from the **View** and is capable of changing the **View**.
MVP还有另一种味道——监督控球员MVP。这种变体包括视图和模型的直接绑定，而演示者(监控控制器)仍然处理来自视图的操作，并且能够更改视图。

![](/img/15562629867352.jpg)
Supervising Presenter variant of the MVP

But as we have already learned before, vague responsibility separation is bad, as well as tight coupling of the **View** and the **Model**. That is similar to how things work in Cocoa desktop development.
但是正如我们之前已经了解到的，模糊的责任分离是不好的，视图和模型的紧密耦合也是不好的。这类似于Cocoa桌面开发中的工作原理。

Same as with the traditional MVC, I don’t see a point in writing an example for the flawed architecture.
与传统MVC相同，我认为没有必要为有缺陷的体系结构编写示例。

###MVVM


The latest and the greatest of the MV(X) kind
最新和最伟大的MV(X)类

The MVVM is the newest of MV(X) kind thus, let’s hope it emerged taking into account problems MV(X) was facing previously.
MVVM是最新的MV(X)类型，因此，让我们希望它出现考虑到MV(X)之前面临的问题。

In theory the Model-View-ViewModel looks very good. The **View** and the **Model** are already familiar to us, but also the **Mediator**, represented as the **View Model**.
理论上，模型-视图-视图模型看起来非常好。视图和模型对我们来说已经很熟悉了，而且中介也很熟悉，表示为视图模型。

![](/img/15562632680865.jpg)


 It is pretty similar to the MVP:
这和MVP很像:

* the MVVM treats the view controller as the **View**
MVVM将视图控制器视为视图

* There is no tight coupling between the **View** and the **Model**
视图和模型之间没有紧密耦合

In addition, it does **binding** like the Supervising version of the MVP; however, this time not between the **View** and the **Model**, but between the **View** and the **View Model**.
此外，它确实像MVP的监管版本一样具有约束力;然而，这一次不是在视图和模型之间，而是在视图和视图模型之间。

So what is the **View Model** in the iOS reality? It is basically UIKit **independent** representation of your **View** and its state. The **View Model** invokes changes in the **Model** and updates itself with the updated **Model**, and since we have a binding between the **View** and the **View Model**, the first is updated accordingly.
iOS中的视图模型是什么?它基本上是UIKit独立于视图及其状态的表示。视图模型调用模型中的更改并使用更新后的模型更新自身，由于我们在视图和视图模型之间有一个绑定，因此第一个模型将相应地更新。

#### Bindings
绑定

I briefly mentioned them in the MVP part, but let’s discuss them a bit here. Bindings come out of box for the OS X development, but we don’t have them in the iOS toolbox. Of course we have the KVO and notifications, but they aren’t as convenient as bindings.
我在MVP部分简单地提到了他们，但是让我们在这里讨论一下。绑定对于OS X开发来说是开箱即用的，但是我们在iOS工具箱中没有它们。当然，我们有KVO和通知，但是它们没有绑定那么方便。

So, provided we don’t want to write them ourselves, we have two options:
所以，如果我们不想自己写，我们有两个选择:

* One of the KVO based binding libraries like the RZDataBinding or the SwiftBond
基于KVO的绑定库之一，如RZDataBinding或SwiftBond

* The full scale functional reactive programming beasts like ReactiveCocoa, RxSwift or PromiseKit.
像ReactiveCocoa、RxSwift或promise ekit这样的全功能反应性编程工具。

In fact, nowadays, if you hear “MVVM” — you think ReactiveCocoa, and vice versa. Although it is possible to build the MVVM with the simple bindings, ReactiveCocoa (or siblings) will allow you to get most of the MVVM.
事实上，现在，如果你听到“MVVM”，你会想到ReactiveCocoa，反之亦然。虽然可以使用简单的绑定构建MVVM，但ReactiveCocoa(或同级)将允许您获得大部分MVVM。

There is one bitter truth about reactive frameworks: the great power comes with the great responsibility. It’s really easy to mess up things when you go reactive. In other words, if you do something wrong, you might spend a lot of time debugging the app, so just take a look at this call stack.
关于反应性框架有一个痛苦的事实:强大的力量伴随着巨大的责任。当你反应过度时，很容易把事情搞砸。换句话说，如果您做错了什么，您可能会花费大量时间调试应用程序，所以只需查看这个调用堆栈。

![](/img/15562635554240.jpg)
Reactive Debugging

In our simple example, the FRF framework or even the KVO is an overkill, instead we’ll explicitly ask the View Model to update using showGreeting method and use the simple property for greetingDidChange callback function.
在我们的简单示例中，FRF框架甚至是KVO都是多余的，相反，我们将显式地要求视图模型使用showGreeting方法进行更新，并使用greetingDidChange回调函数的简单属性。

```

import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingViewModelProtocol: class {
    var greeting: String? { get }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())? { get set } // function to call when greeting did change
    init(person: Person)
    func showGreeting()
}

class GreetingViewModel : GreetingViewModelProtocol {
    let person: Person
    var greeting: String? {
        didSet {
            self.greetingDidChange?(self)
        }
    }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())?
    required init(person: Person) {
        self.person = person
    }
    func showGreeting() {
        self.greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
    }
}

class GreetingViewController : UIViewController {
    var viewModel: GreetingViewModelProtocol! {
        didSet {
            self.viewModel.greetingDidChange = { [unowned self] viewModel in
                self.greetingLabel.text = viewModel.greeting
            }
        }
    }
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self.viewModel, action: "showGreeting", forControlEvents: .TouchUpInside)
    }
    // layout code goes here
}
// Assembling of MVVM
let model = Person(firstName: "David", lastName: "Blaine")
let viewModel = GreetingViewModel(person: model)
let view = GreetingViewController()
view.viewModel = viewModel

```

MVVM example

And again back to our **feature** assessment:
再回到我们的特性评估:

* **Distribution** — it is not clear in our tiny example, but, in fact, the MVVM’s View has more responsibilities than the MVP’s View. Because the first one updates it’s state from the View Model by setting up bindings, when the second one just forwards all events to the Presenter and doesn’t update itself.
分布-在我们的小例子中并不清楚，但事实上，MVVM的视图比MVP的视图有更多的责任。因为第一个通过设置绑定更新视图模型中的状态，而第二个只将所有事件转发给演示者而不更新自己。

* **Testability** — the View Model knows nothing about the View, this allows us to test it easily. The View might be also tested, but since it is UIKit dependant you might want to skip it.
可测试性——视图模型对视图一无所知，这使我们能够轻松地测试它。视图也可能被测试，但由于它依赖于UIKit，你可能想跳过它。

* **Easy of use **— its has the same amount of code as the MVP in our example, but in the real app where you’d have to forward all events from the View to the Presenter and to update the View manually, MVVM would be much skinnier if you used bindings.
易于使用——它的代码量与我们示例中的MVP相同，但在实际应用程序中，您必须将视图中的所有事件转发给演示程序，并手动更新视图，如果使用绑定，MVVM会更瘦。

>
The MVVM is very attractive, since it combines benefits of the aforementioned approaches, and, in addition, it doesn’t require extra code for the View updates due to the bindings on the View side. Nevertheless, testability is still on a good level.
MVVM非常吸引人，因为它结合了上述方法的优点，而且，由于视图端的绑定，它不需要额外的代码来更新视图。尽管如此，可测试性仍然处于良好的水平。

### VIPER


**LEGO building experience transferred into the iOS app design**
乐高搭建体验转移到iOS app设计中

VIPER is our last candidate, which is particularly interesting because it doesn’t come from the MV(X) category.
VIPER是我们最后一个候选项，这非常有趣，因为它不是MV(X)类。

By now, you must agree that the granularity in responsibilities is very good. VIPER makes another iteration on the idea of separating responsibilities, and this time we have five layers.
到目前为止，您必须同意职责中的粒度非常好。VIPER对分离职责的概念进行了另一次迭代，这次我们有5层。

![](/img/15562693816553.jpg)
VIPER


* **Interactor** — contains business logic related to the data (Entities) or networking, like creating new instances of entities or fetching them from the server. For those purposes you’ll use some Services and Managers which are not considered as a part of VIPER module but rather an external dependency.
Interactor—包含与数据(实体)或网络相关的业务逻辑，比如创建实体的新实例或从服务器获取它们。出于这些目的，您将使用一些服务和管理器，这些服务和管理器并不被认为是VIPER模块的一部分，而是一个外部依赖项。

* **Presenter** — contains the UI related (but UIKit independent) business logic, invokes methods on the Interactor.
Presenter -包含与UI相关(但独立于UIKit)的业务逻辑，调用交互器上的方法。

* **Entities** — your plain data objects, not the data access layer, because that is a responsibility of the Interactor.
实体——您的普通数据对象，而不是数据访问层，因为这是交互器的职责。

* **Router** — responsible for the segues between the VIPER modules.
路由器-负责毒蛇模块之间的segue。

Basically, VIPER module can be a one screen or the whole user story of your application — think of authentication, which can be one screen or several related ones. How small are your “LEGO” blocks supposed to be? — It’s up to you.
基本上，VIPER模块可以是一个屏幕，也可以是应用程序的整个用户故事——考虑一下身份验证，它可以是一个屏幕，也可以是几个相关的屏幕。你的“乐高”积木应该有多小?-由你决定。

If we compare it with the MV(X) kind, we’ll see a few differences of the distribution of responsibilities:
如果我们将其与MV(X)类型进行比较，我们将看到职责分配的一些差异:

* **Model** (data interaction) logic shifted into the Interactor with the Entities as dumb data structures.
模型(数据交互)逻辑转换为与实体交互的哑数据结构。

* Only the UI representation duties of the **Controller**/**Presenter**/**ViewModel** moved into the **Presenter**, but not the data altering capabilities.
只有控制器/演示程序/视图模型的UI表示功能被转移到演示程序中，而没有数据修改功能。

* **VIPER** is the first pattern which explicitly addresses navigation responsibility, which is supposed to be resolved by the Router.
VIPER是第一个明确处理导航责任的模式，它应该由路由器来解决。

>
Proper way of doing routing is a challenge for the iOS applications, the MV(X) patterns simply don’t address this issue.
正确的路由方式对于iOS应用程序是一个挑战，MV(X)模式根本无法解决这个问题。

The example doesn’t cover **routing** or **interaction between modules**, as those topics are not covered by the MV(X) patterns at all.
该示例不涉及模块之间的路由或交互，因为MV(X)模式根本不涉及这些主题。

```

import UIKit

struct Person { // Entity (usually more complex e.g. NSManagedObject)
    let firstName: String
    let lastName: String
}

struct GreetingData { // Transport data structure (not Entity)
    let greeting: String
    let subject: String
}

protocol GreetingProvider {
    func provideGreetingData()
}

protocol GreetingOutput: class {
    func receiveGreetingData(greetingData: GreetingData)
}

class GreetingInteractor : GreetingProvider {
    weak var output: GreetingOutput!
    
    func provideGreetingData() {
        let person = Person(firstName: "David", lastName: "Blaine") // usually comes from data access layer
        let subject = person.firstName + " " + person.lastName
        let greeting = GreetingData(greeting: "Hello", subject: subject)
        self.output.receiveGreetingData(greeting)
    }
}

protocol GreetingViewEventHandler {
    func didTapShowGreetingButton()
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

class GreetingPresenter : GreetingOutput, GreetingViewEventHandler {
    weak var view: GreetingView!
    var greetingProvider: GreetingProvider!
    
    func didTapShowGreetingButton() {
        self.greetingProvider.provideGreetingData()
    }
    
    func receiveGreetingData(greetingData: GreetingData) {
        let greeting = greetingData.greeting + " " + greetingData.subject
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var eventHandler: GreetingViewEventHandler!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.eventHandler.didTapShowGreetingButton()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of VIPER module, without Router
let view = GreetingViewController()
let presenter = GreetingPresenter()
let interactor = GreetingInteractor()
view.eventHandler = presenter
presenter.view = view
presenter.greetingProvider = interactor
interactor.output = presenter

```

Yet again, back to the **features**:
再次回到特性:

* **Distribution** — undoubtedly, VIPER is a champion in distribution of responsibilities.
分配-毫无疑问，蝰蛇是分配责任的冠军。

* **Testability** —no surprises here, better distribution — better testability.
可测试性——这里没有意外，更好的分布——更好的可测试性。

* **Easy of use** — finally, two above come in cost of maintainability as you already guessed. You have to write huge amount of interface for classes with very small responsibilities.
易于使用——最后，正如您已经猜到的，上面提到的两项是可维护性成本。您必须为职责非常小的类编写大量的接口。

### So what about LEGO?
那么乐高呢?

While using VIPER, you might feel like building The Empire State Building from LEGO blocks, and that is a signal that you have a problem. Maybe, it’s too early to adopt VIPER for your application and you should consider something simpler. Some people ignore this and continue shooting out of cannon into sparrows. I assume they believe that their apps will benefit from VIPER at least in the future, even if now the maintenance cost is unreasonably high. If you believe the same, then I’d recommend you to try Generamba — a tool for generating VIPER skeletons. Although for me personally it feels like using an automated targeting system for cannon instead of simply taking a sling shot.
在使用VIPER时，您可能会觉得像是用乐高积木搭起帝国大厦，这是您遇到问题的信号。也许，现在为您的应用程序采用VIPER还为时过早，您应该考虑一些更简单的方法。有些人忽视了这一点，继续用大炮向麻雀射击。我假设他们相信他们的应用程序至少在未来会从VIPER中受益，即使现在的维护成本高得不合理。如果你也这么认为，那么我建议你试试Generamba——一种生成毒蛇骨架的工具。尽管对我个人来说，这感觉就像使用自动瞄准系统而不是简单的弹弓射击。

### Conclusion
结论

We went though several architectural patterns, and I hope you have found some answers to what bothered you, but I have no doubt that you realised that there is **no silver bullet** so choosing architecture pattern is a matter of weighting tradeoffs in your particular situation.
我们讨论了几种体系结构模式，我希望您已经找到了一些困扰您的问题的答案，但是我确信您已经意识到没有什么灵丹妙药，所以选择体系结构模式是在特定情况下权衡利弊的问题。

Therefore, it is natural to have a mix of architectures in same app. For example: you’ve started with MVC, then you realised that one particular screen became too hard to maintain efficiently with the MVC and switched to the MVVM, but only for this particular screen. There is no need to refactor other screens for which the MVC actually does work fine, because both of architectures are easily compatible.
因此，在同一个应用程序中混合架构是很自然的。例如:您从MVC开始，然后您意识到使用MVC很难有效地维护一个特定的屏幕，因此切换到MVVM，但是只针对这个特定的屏幕。不需要重构MVC实际工作良好的其他屏幕，因为这两种架构都很容易兼容。

>
Make everything as simple as possible, but not simpler — Albert Einstein
让每件事都尽可能的简单，但不要越简单越好——阿尔伯特·爱因斯坦

Thank you for reading! If you liked this article, please clap so other people can read it too :)
感谢您的阅读!如果你喜欢这篇文章，请鼓掌，这样其他人也可以阅读。

Follow me on Twitter for more iOS design and patterns.
在Twitter上关注我，了解更多iOS设计和模式。


[原文](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52)


