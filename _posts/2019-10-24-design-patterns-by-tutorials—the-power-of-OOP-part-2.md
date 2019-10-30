---
layout:     post  
title:      Design patterns by Tutorials— The power of OOP (part 2)
subtitle:   设计模式之旅--OOP的力量2
date:      2019-10-24 21:40:00
author:     "Dan"
header-img: "img/design-patterns-by-tutorials%E2%80%94the-power-of-OOP-part-1.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---



## Design patterns by Tutorials — The power of OOP (part 2)

Singleton Pattern: pure-singleton and semi-singleton design pattern in Swift
单例模式:Swift中的纯单例和半单例设计模式

Prerequisites — This blog series requires an intermediate level of expertise in object-oriented programming. You should have basic knowledge about class, object, constructor, inheritance, value and reference type. An Intermediates will gain knowledge and experts will sharpen his or her knowledge by reading this series from start to finish.
先决条件——本系列博客需要具备面向对象编程的中级专业知识。您应该对类、对象、构造函数、继承、值和引用类型有基本的了解。中级用户将获得知识，而专家将通过从头到尾阅读本系列来提高其知识。

### Singleton class
单例类

In object-oriented programming, a singleton class is a class that can have only one object during the whole life-cycle of an application or project.
在面向对象编程中，单例类是在应用程序或项目的整个生命周期中只能有一个对象的类。

Singleton design pattern is a part of iOS Applications life-cycle code. For example, in an iOS project, the UIApplication class is the best example of a singleton class, which is created by the iOS system on app launch and passed to the AppDelegate as a parameter in *application:didFinishLaunchingWithOptions:*  method.
单例设计模式是iOS应用程序生命周期代码的一部分。例如，在一个iOS项目中，UIApplication类是单例类的最好例子，它是由iOS系统在app启动时创建的，并作为 *application:didFinishLaunchingWithOptions:* 方法中的一个参数传递给AppDelegate。

### There are two types of Singleton design patterns.
单例设计模式有两种类型。

1. Pure-singleton design pattern
Pure-singleton设计模式

2. Semi-singleton design pattern
Semi-singleton设计模式

### Pure-Singleton Design Pattern:
Pure-Singleton设计模式:

In this pattern, a programmer who is using the functionality of a pure-singleton class is not allowed to create an instance of the class. A programmer can only call methods and access properties available for the singleton class by using the predefined instance of that class.
在此模式中，使用纯单例类功能的程序员不允许创建该类的实例。程序员只能通过使用单例类的预定义实例来调用方法和访问可用的属性。

The object of a pure-singleton class is automatically created during application launch with pre-defined parameters mentioned by the developer who created that class.
在应用程序启动期间，使用创建该类的开发人员提到的预定义参数自动创建纯单例类的对象。

Pure-singleton classes must be marked as final to avoid inheritance confusion. In swift, you can use structure also to achieve the same concept.
纯单例类必须标记为final，以避免继承混淆。在swift中，你也可以使用结构来实现相同的概念。

A programmer cannot inherit the pure-singleton class. When you try to inherit any class in swift, you must call the constructor of the superclass. Calling the constructor of the superclass is not possible in pure-singleton class because all constructors of the pure-singleton class are always marked as private.
程序员不能继承纯单例类。当你试图继承swift中的任何类时，你必须调用超类的构造函数。在pure-singleton类中调用超类的构造函数是不可能的，因为pure-singleton类的所有构造函数都被标记为private。

Let understand this design pattern in a simple way with below example.
让我们通过下面的例子用简单的方式来理解这个设计模式。

```swift

//PureSingletonDesignPatterExample1.swift :

// Hitendra Solanki
// Pure-singleton design pattern Playground example
final class LogManager {
  //shared and only one available object
  static let logger: LogManager = LogManager(databaseURLEndpoint: "https://www.hitendrasolanki.com/logger/live")
  
  private var databaseURLEndpoint: String
  
  //marked as private, no one is allowed to access this initialiser outside of the class
  private init(databaseURLEndpoint: String) {
    self.databaseURLEndpoint = databaseURLEndpoint
  }
  
  func log(_ value: String...){
    //complex code to connect to the databaseURLEndpoint and send the value to server directly
  }
}

//This is function in playground which executes our test code
func main(){
  LogManager.logger.log("test log from medium blog") //this will log on "/live" endpoint
}

//call main to execute our test code
main()


```


In the above example, *PureSingletonDesignPatterExample1.swift* we have marked all init methods as private of LogManger class so no one can create an instance of that class. If we try to create a sub-class of LogManger, the compiler will give you an error.
在上面的例子中，PureSingletonDesignPatterExample1.swift我们已经将所有init方法标记为LogManger类的私有方法，因此没有人可以创建该类的实例。如果我们试图创建LogManger的子类，编译器会给你一个错误。

UIApplication, AppDelegate are examples of the pure-singleton class. Have you ever tried to create an object of UIApplication class manually? Try to do so and run the app. [What happened? App crashed, right? -I have tested this crash on swift 5, XCode 10.2]
UIApplication和AppDelegate是纯单例类的例子。你曾经尝试手动创建一个UIApplication类的对象吗?试着这样做并运行应用程序。应用程序崩溃,对吧?-我已经测试了这个崩溃在swift 5, XCode 10.2]

### Limitations of Pure-Singleton Design Pattern,
纯单例模式的局限性，

We cannot test the pure-singleton class with the test data. In the above example *PureSingletonDesignPatterExample1.swift*, suppose you want to test the LogManager class by pointing it to some test mode URL instead of production mode URL, we can not do that when we are writing test cases in XCTest target.
我们不能用测试数据来测试纯单例类。在上面的例子中，*PureSingletonDesignPatterExample1.swift*，假设你想要测试LogManager类，将它指向某个测试模式URL而不是生产模式URL，当我们在XCTest目标中编写测试用例时，我们不能这样做。

We can overcome this limitation by converting our pure-singleton class to semi-singleton class or by using the dependency injection pattern. I will write a dedicated article on dependency injection pattern. For now, let’s continue with the semi-singleton design pattern.
我们可以通过将我们的纯单例类转换为半单例类或者使用依赖注入模式来克服这个限制。我将专门写一篇关于依赖注入模式的文章。现在，让我们继续使用半单例设计模式。

### Semi-singleton design pattern:
Semi-singleton设计模式:

* In semi-singleton design pattern, a programmer who is using the class is allowed to create an object of the singleton class if required. as well as also allowed to call methods and use properties of the singleton class.
在半单例设计模式中，如果需要，使用该类的程序员可以创建单例类的对象。以及允许调用方法和使用单例类的属性。

* A programmer can also inherit the semi-singleton class if the singleton is not marked as final by the developer of the singleton class. In semi-singleton design pattern to mark the class as a final is not necessary.
如果单例类的开发人员没有将单例类标记为final，程序员也可以继承半单例类。在半单例模式中，将类标记为final是没有必要的。

```swift

//SemiSingletonDesignPatterExample1.swift
// Hitendra Solanki
// Semi-singleton design pattern Playground example
//In semi-singleton design pattern, marking the class as final is optional
final class LogManager {
  //shared object
  static let logger: LogManager = LogManager(databaseURLEndpoint: "https://www.hitendrasolanki.com/logger/live")
  
  private var databaseURLEndpoint: String
  
  //not marked as private, anyone is allowed to access this initialiser outside of the class
  init(databaseURLEndpoint: String) {
    self.databaseURLEndpoint = databaseURLEndpoint
  }
  
  func log(_ value: String...){
    //complex code to connect to the databaseURLEndpoint and send the value to server directly
  }
}

//This is function executes our main code
func main(){
  LogManager.logger.log("main log from medium blog on live server endpoint") //this will log on "/live" endpoint
}


// This is function executes our TEST MODE code
// Here in playground, Hitendra Solanki created this method for the demostratino purpose only
// Usually we write this kind of test codes, inside the test targe of the XCode-project
func testThatLogManger(){
  
  //we are allowed to create an instace of class LogManager,
  //because it follows the Semi-Singleton design patterns
  let logManagerTestObject = LogManager(databaseURLEndpoint: "https://www.hitendrasolanki.com/logger/test")
  
  logManagerTestObject.log("test log from medium blog on test server endpoint") //this will log on "/test" endpoint
}

main() //call main
testThatLogManger() //call test execution

```

* In the above example, *SemiSingletonDesignPatterExample1.swift*, we have not marked the init method as private, so we can create multiple instances of the semi-singleton class to remove the dependency from the property **databaseURLEndpoint**. Now we can create an object of the LogManger class by using any **databaseURLEndpoint** value, now our class LogManger follows the semi-singleton pattern.
在上面的例子中，*SemiSingletonDesignPatterExample1.swift*，我们没有将init方法标记为私有的，所以我们可以创建半单例类的多个实例来从属性**databaseURLEndpoint**中移除依赖项。现在我们可以使用任何**databaseURLEndpoint**值创建LogManger类的对象，现在我们的类LogManger遵循半单例模式。

UserDefault, FileManager, NotificationCenter are the example of semi-singleton classes. We can use pre-defined shared objects of UserDefault, FileManager, and NotificationCenter which are UserDefault.standard, FileManager.default and NotificationCenter.default.
UserDefault、FileManager、NotificationCenter是半单例类的例子。我们可以使用UserDefault、FileManager和NotificationCenter等预定义的共享对象，它们是UserDefault.standard，FileManager.default和NotificationCenter.default。

The pre-defined object of pure or semi-singleton classes always lives in memory and never destroyed until you close the application. A programmer-defined object of semi singleton class destroyed after the scope of the object is completed.
纯类或半单例类的预定义对象总是存在于内存中，在关闭应用程序之前不会被销毁。半单例类的程序员定义的对象在对象的作用域完成后被销毁。

>
Question: Which is a better way, “Pure-singletone design pattern” or “Semi-singleton design pattern”, when I should use the first one and when other one?
问:“纯单例设计模式”和“半单例设计模式”，哪一种更好? 什么情况下应该使用？

This totally depends on the responsibilities of your class. If you have created singleton class in your project and needs to change some of the properties at some point or if you want to add more responsibilities to the class by adding new methods in future and if you required its test cases also with mock data for newly added methods, you should go with the semi-singleton design pattern.
这完全取决于你的类的功能。如果您已经创建了单例类项目,需要在某些时候改变一些属性，或在未来你想添加新的方法，而且你需要为新添加的方法使用模拟数据进行测试,你应该采用semi-singleton设计模式。

If you created your class, you are done with unit testing and want to publish it via a framework or library, you can go with pure-singleton design patterns. Because you created the class and some other developer will use it, so unit testing of your singleton-class is your responsibility before release, not the responsibility of the developer who is using it.
如果你创建了你的类，你已经完成了单元测试并且想要通过一个框架或者库来发布它，你可以使用纯单例设计模式。因为您创建了这个类，其他一些开发人员将会使用它，所以在发布之前对单例类进行单元测试是您的责任，而不是使用它的开发人员的责任。



[点击查看原文](https://medium.com/swift-programming/https-medium-com-hitendrahckr-part-2-singletone-desing-pattern-in-swift-by-tutorials-the-power-of-oop-431314237b10)

