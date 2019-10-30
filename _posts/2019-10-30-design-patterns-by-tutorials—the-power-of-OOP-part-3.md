---
layout:     post  
title:      Design patterns by Tutorials— The power of OOP (part 3)
subtitle:   Adapter pattern in Swift   Swift适配器模式
date:      2019-10-30 17:23:00
author:     "Dan"
header-img: "img/design-patterns-by-tutorials%E2%80%94the-power-of-OOP-part-1.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---


## Design patterns by Tutorials— The power of OOP (part 3)

Prerequisites — This tutorials series requires an intermediate level of expertise in object-oriented programming. You should have basic knowledge about class, object, constructor, inheritance, value and reference type. An Intermediates will gain knowledge and experts will sharpen his or her knowledge by reading this series from start to finish.
先决条件——本系列教程要求具备面向对象编程的中级专业知识。您应该对类、对象、构造函数、继承、值和引用类型有基本的了解。中级用户将获得知识，而专家将通过从头到尾阅读本系列来提高其知识。

### What is an Adapter?
什么是适配器?

An adapter is a physical device that allows one hardware or electronic interface to be adapted to another hardware or electronic interface without loss of functionality.
适配器是一种物理设备，它允许一个硬件或电子接口适应于另一个硬件或电子接口，而不会丢失功能。

Adapters are used when you have one form of input and you want to access something that cannot take your form of input.
当您有一种形式的输入，并且希望访问不能采用您的输入形式的内容时，将使用适配器。

![](/img/15724282500788.jpg)

Now It’s time to understand the adapter pattern by answering below simple questions.
现在，通过回答下面的简单问题来理解适配器模式。

1. What is the Adapter pattern in Object-Oriented programming?
什么是面向对象编程中的适配器模式?

2. What problem does it solve?
它解决了什么问题?

3. How we can use it in programming?
我们如何在编程中使用它?

### What is the Adapter pattern in Object-Oriented programming?
什么是面向对象编程中的适配器模式?

In the adapter pattern, we define one class that takes the existing type as an input and give us the required type or required functionality/properties to access.
在适配器模式中，我们定义一个类，该类接受现有类型作为输入，并为我们提供所需的类型或所需的功能/属性来访问。

### What problem does it solve?
它解决了什么问题?

It helps to convert existing types of data to another form of data.
它有助于将现有类型的数据转换为另一种形式的数据。

This is pretty useful when we use code of third party frameworks or library and it accepts restricted types only.
当我们使用第三方框架或库的代码并且它只接受受限制的类型时，这是非常有用的。

### How we can use it in programming?
我们如何在编程中使用它?

Let’s understand this with the below example.
让我们通过下面的例子来理解这一点。

We have classes like **Point**, **LineSegment** and **DrawingPad** .
我们有类**Point**, **LineSegment** 和 **DrawingPad** 。

>
Remember our old school days of Computer Graphics using C language?
还记得我们以前用C语言做计算机图形学的日子吗?
That time just to draw a line on the screen, we have to calculate all the points of the line between given two points. To calculate points of line there are multiple basic algorithms available like simple line drawing algorithm, DDA Algorithm, Bresenham’s line algorithm etc.
为了在屏幕上画一条线，我们需要计算这条直线上两点之间的所有点。为了计算直线的点，有多种基本的算法可以使用，如简单的直线绘制算法、DDA算法、Bresenham直线算法等。
We will keep our example code clean and easy to understand for the adapter pattern without writing this complex algorithms here.
对于适配器模式，我们将保持示例代码的简洁和易于理解，而不需要在这里编写这种复杂的算法。

![](/img/15724285679593.jpg)


```swift
// Hitendra Solanki
// Adapter design pattern Playground example
import Foundation

class Point {
    var x: Int
    var y: Int
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

class LineSegment {
    var startPoint: Point
    var endPoint:   Point
    
    init(startPoint: Point, endPoint: Point) {
        self.startPoint = startPoint
        self.endPoint = endPoint
    }
}

//Mark:- DrawingPad
//  This is just to make example more interesting
//  We are just printing the point on Console to make it simple
class DrawingPad {
    //Draw Single point, main drawing method
    func draw(point: Point) {
        print(".")
    }
    
    //Draw multiple points
    func draw(points: [Point]) {
        points.forEach { (point) in
            self.draw(point: point)
        }
    }
}



func main(){
    let drawingPad = DrawingPad()
    
    let point1 = Point(x: 10, y: 10)
    
    drawingPad.draw(point: point1)
}

//call main function to execute code
main()


// AdapterPattern_intro.swift 
```

### In above example
在以上的例子

Line 6, class **Point** represents a single point.
第6行，类**Point**表示一个点。

Line 16, class **LineSegment** represents a line segment.
第16行，类**LineSegmen**t表示一个线段。

Line 29, class**DrawingPad** represents a canvas for drawing. DrawingPad can draw single or multiple points on a canvas.
第29行，类**DrawingPad**表示一个用于绘图的画布。DrawingPad可以在画布上绘制单个或多个点。

**We can draw a point in this example but can’t draw a line segment directly on the canvas using a drawing pad instance. Here adapter pattern comes in rescue, thanks to the adapter pattern.**
我们可以在本例中绘制一个点，但不能使用画板实例在画布上直接绘制线段。在这里，适配器模式发挥了作用，这要感谢适配器模式。

![](/img/15724291478955.jpg)

```swift
// Hitendra Solanki
// Adapter design pattern Playground example
import Foundation

class Point {
    var x: Int
    var y: Int
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

class LineSegment {
    var startPoint: Point
    var endPoint:   Point
    
    init(startPoint: Point, endPoint: Point) {
        self.startPoint = startPoint
        self.endPoint = endPoint
    }
}

//Mark:- DrawingPad
//  This is just to make example more interesting
//  We are just printing the point on Console to make it simple
class DrawingPad {
    //Draw Single point, main drawing method
    func draw(point: Point) {
        print(".")
    }
    
    //Draw multiple points
    func draw(points: [Point]) {
        points.forEach { (point) in
            self.draw(point: point)
        }
    }
}

//Mark: LineSegmentToPointAdapter
//  This is responsible to generate all points exists on LineSegment
class LineSegmentToPointAdapter {
    var lineSegment: LineSegment
    init(lineSegment: LineSegment) {
        self.lineSegment = lineSegment
    }
    func points() -> Array<Point> {
        //To make things simple, without complex line drawing algorithms,
        //This will only work for points like below
        //(10,10) to (20,20)
        //(34,34) to (89,89)
        //(x,y) to (p,q) where y=x and q=p i.e. (x,x) to (p,p) just to make it simple for this demo
        
        var points: Array<Point> = []
        let zipXY = zip(
            (self.lineSegment.startPoint.x...self.lineSegment.endPoint.x),
            (self.lineSegment.startPoint.y...self.lineSegment.endPoint.y)
        )
        for (x,y) in zipXY {
            let newPoint = Point(x: x, y: y)
            points.append(newPoint)
        }
        return points
    }
    
}




func main(){
    let drawingPad = DrawingPad()
    
    //Remeber: (x,y) to (p,q) where y=x and q=p => (x,x) to (p,p) for our points logic to work
    
    //create lineSegment
    let startPoint = Point(x: 2, y: 2)
    let endPoint = Point(x: 10, y: 10)
    let lineSegment = LineSegment(startPoint: startPoint, endPoint: endPoint)
    
    //create adapter
    let adapter = LineSegmentToPointAdapter(lineSegment: lineSegment)
    
    //get points from adapter to draw
    let points = adapter.points()
    
    drawingPad.draw(points: points) //it will draw total 9 dots on console
}

//call main function to execute code
main()

//AdapterPattern_simpleSolution.swift
```

Line45, class **LineSegmentToPointAdapter** represents an adapter which is the core concept of this article. It takes the existing type LineSegment as an input and gives us an array of points in our case.
Line45，类**Linesegmenttopadapter**表示一个适配器，这是本文的核心概念。它将现有类型LineSegment作为输入，并在本例中为我们提供一个点数组。

### Conclusion
结论

1. An adapter is any class that takes the one object as a parameter in the constructor.
适配器是在构造函数中接受一个对象作为参数的任何类。

2. An adapter class has properties/methods that we can use as the required type.
适配器类具有可以用作所需类型的属性/方法。

3. An adapter can also directly act a required type, that depends on implementation.
适配器还可以直接扮演所需的类型，这取决于实现。

This is just a simple implementation of the adapter pattern in Object-oriented programming using Swift. But wait, there is something more and advance in this pattern.
这只是一个简单的实现适配器模式在面向对象编程使用Swift。但是等一下，在这个模式中还有更多的东西和进展。

The adapter design pattern is more powerful with….
适配器设计模式更强大的是……

More content coming soon…
更多内容即将推出…

[点击查看原文](https://medium.com/flawless-app-stories/adapter-pattern-design-patterns-by-tutorials-the-power-of-oop-part-3-112a956c1101)  :  https://medium.com/flawless-app-stories/adapter-pattern-design-patterns-by-tutorials-the-power-of-oop-part-3-112a956c1101

