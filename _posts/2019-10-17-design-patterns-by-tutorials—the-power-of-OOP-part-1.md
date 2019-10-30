---
layout:     post  
title:      Design patterns by Tutorials— The power of OOP (part 1)
subtitle:   设计模式之旅--OOP的力量1
date:      2019-10-17 21:40:00
author:     "Dan"
header-img: "img/design-patterns-by-tutorials%E2%80%94the-power-of-OOP-part-1.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 翻译
---


## Design patterns by Tutorials— The power of OOP (part 1)


Prerequisites — This blog series requires an intermediate level of expertise in object-oriented programming. You should have basic knowledge about class, object, constructor, inheritance, value and reference type. An intermediate will gain knowledge and experts will sharpen his or her knowledge by reading this series from start to finish.
先决条件——本系列博客需要具备面向对象编程的中级专业知识。您应该对类、对象、构造函数、继承、值和引用类型有基本的了解。一个中级将获得知识和专家将锐化他或她的知识阅读本系列从开始到结束。

Design patterns are used to represent the best practices adopted by the experienced object-oriented software developer community.
设计模式用于表示经验丰富的面向对象软件开发人员社区采用的最佳实践。

The builder design pattern helps us to build an object in a more simpler and readable way. The builder design pattern follows two simple rules as mentioned below.
builder设计模式帮助我们以更简单和可读的方式构建对象。builder设计模式遵循两个简单的规则，如下所述。

1. Separate the original class representation and its construction methods.
   分离原始的类表示及其构造方法。

2. returns the instance of the class in the final step
    在最后一步中返回类的实例

The best example of a builder design pattern is SwiftUI, yes you read right. SwiftUI uses builder design pattern for most of its classes e.g. Text, Image
builder设计模式的最佳示例是SwiftUI，是的，您没有看错。SwiftUI的大多数类都使用了builder设计模式，例如文本、图像等

Problem:
问题:

Think about the class say Person having ten or more properties, imaging its constructor design when you need to create an instance of the class Person. Its constructor will take ten or more arguments, it will be hard to manage these many arguments as a single function or constructor and eventually, you will lose the readability of code. checkout the below example.
想想这个类，比如Person有十个或更多的属性，当你需要创建Person类的一个实例时，想象一下它的构造函数设计。它的构造函数需要10个或更多的参数，很难将这些参数作为一个函数或构造函数来管理，最终，您将失去代码的可读性。签出下面的例子。

```

WithoutDesignPatternExample1.swift:

class Person {
  //personal details
  var name: String = ""
  var gender: String = ""
  var birthDate: String = ""
  var birthPlace: String = ""
  var height: String = ""
  var weight: String = ""
  
  //contact details
  var phone: String = ""
  var email: String = ""
  
  //address details
  var streeAddress: String = ""
  var zipCode: String = ""
  var city: String = ""
  
  //work details
  var companyName: String = ""
  var designation: String = ""
  var annualIncome: String = ""
  
  //constructor
  init(name: String,
       gender: String,
       birthDate: String,
       birthPlace: String,
       height: String,
       weight: String,
       phone: String,
       email: String,
       streeAddress: String,
       zipCode: String,
       city: String,
       companyName: String,
       designation: String,
       annualIncome: String) {
    self.name = name
    self.gender = gender
    self.birthDate = birthDate
    self.birthPlace = birthPlace
    self.height = height
    self.weight = weight
    self.phone = phone
    self.email = email
    self.streeAddress = streeAddress
    self.zipCode = zipCode
    self.height = height
    self.city = city
    self.companyName = companyName
    self.designation = designation
    self.annualIncome = annualIncome
  }
}

//This is function in Xcode-Playground which executes our test code
func main() {
  let hitendra = Person(name: "Hitendra Solanki",
                        gender: "Male",
                        birthDate: "2nd Oct 1991",
                        birthPlace: "Gujarat, India",
                        height: "5.9 ft",
                        weight: "85kg",
                        phone: "+91 90333-71772",
                        email: "hitendra.developer@gmail.com",
                        streeAddress: "52nd Godrej Street",
                        zipCode: "380015",
                        city: "Ahmedabad",
                        companyName: "Fortune 500",
                        designation: "Software architect",
                        annualIncome: "45,000 USD")
  
  //use of Person object
  print("\(hitendra.name) works in \(hitendra.companyName) compay as a \(hitendra.designation).")
}

//call main to execute our test code in Xcode-Playground
main()

/* Console output:
Hitendra Solanki works in Fortune 500 compay as a Software architect.
*/

```

Try to run the above example in the playground, it will run successfully and give you an expected output. Logically it is correct.
试着在playground上运行上面的例子，它会成功地运行并给你一个预期的输出。逻辑上是正确的。

We can improve the above example, by overcoming the below points.
我们可以通过克服以下几点来改进上面的例子。

1. We have to pass the values in a mentioned order, can not reorder the parameters sequence to improve the readability.
    我们必须按上述顺序传递值，不能重新排序参数序列以提高可读性。

2. We have to pass all the values, even if we don’t know some values at the time of object creation.
    我们必须传递所有的值，即使我们在创建对象时不知道一些值。

E.g. Suppose you required to create an object of Person class, but the person is still finding a job. When that person will join any company then only we will have works details.
假设您需要创建Person类的一个对象，但该对象仍在寻找工作。当那个人将加入任何公司，然后只有我们会有工作细节。

### Solution:
解决方案:

1. Create logical groups of related properties.
   创建相关属性的逻辑组。

2. Create separate builder classes for different groups of properties[helps in properties normalization, this is optional.
   为不同的属性组创建单独的生成器类[有助于属性规范化，这是可选的。

3. Retrieve an instance in the final step.
   在最后一步中检索一个实例。

Let’s simplify this with an example,
让我们用一个例子来简化它，

We already have a class named Person in example WithoutDesignPatternExample1.swift, in which we have 14 properties. If we closely check all the 14 properties, the properties have 4 logical groups.
在example中，我们已经有了一个名为Person的类，而不带有designpatternexample1。swift，我们有14个属性。如果我们仔细检查所有14个属性，这些属性有4个逻辑组。

1. Personal details properties
    个人信息属性

2. Contact details properties
    联系信息属性

3. Address details properties
    地址详细信息属性

4. Company details properties
    公司详细信息属性

Faceted and Fluent design pattern together helps us to overcome the two problems mentioned above.
Faceted and Fluent的设计模式一起帮助我们克服上面提到的两个问题。

```

BuilderDesignPattern[Faceted+Fluent]Example1.swift:

//This is function in playground which executes our test code
func main() {
  
  var hitendra = Person() //person with empty details
  let personBuilder = PersonBuilder(person: hitendra)
  
  hitendra = personBuilder
    .personalInfo
      .nameIs("Hitendra Solanki")
      .genderIs("Male")
      .bornOn("2nd Oct 1991")
      .bornAt("Gujarat, India")
      .havingHeight("5.9 ft")
      .havingWeight("85 kg")
    .contacts
      .hasPhone("+91 90333-71772")
      .hasEmail("hitendra.developer@gmail.com")
    .lives
      .at("52nd Godrej Street")
      .inCity("Ahmedabad")
      .withZipCode("380015")
    .build()
  
  //use of Person object
  print("\(hitendra.name) has contact number \(hitendra.phone) and email \(hitendra.email)")
  
  //later on when we have company details ready for the person
  hitendra = personBuilder
    .works
      .asA("Software architect")
      .inCompany("Fortune 500")
      .hasAnnualEarning("45,000 USD")
    .build()
  
  //use of Person object with update info
  print("\(hitendra.name) works in \(hitendra.companyName) compay as a \(hitendra.designation).")
}

//call main to execute our test code
main()

//Person class which only contains the details
class Person {
  //personal details
  var name: String = ""
  var gender: String = ""
  var birthDate: String = ""
  var birthPlace: String = ""
  var height: String = ""
  var weight: String = ""
  
  //contact details
  var phone: String = ""
  var email: String = ""
  
  //address details
  var streeAddress: String = ""
  var zipCode: String = ""
  var city: String = ""
  
  //work details
  var companyName: String = ""
  var designation: String = ""
  var annualIncome: String = ""
  
  //empty constructor
  init() { }
}

//PersonBuilder class helps to construct the person class instance
class PersonBuilder {
  var person: Person
  init(person: Person){
    self.person = person
  }
  
  //personal details builder switching
  var personalInfo: PersonPersonalDetailsBuilder {
    return PersonPersonalDetailsBuilder(person: self.person)
  }
  
  //contact details builder switching
  var contacts: PersonContactDetailsBuilder {
    return PersonContactDetailsBuilder(person: self.person)
  }
  
  //address details builder switching
  var lives: PersonAddressDetailsBuilder {
    return PersonAddressDetailsBuilder(person: self.person)
  }
  
  //work details builder switching
  var works: PersonCompanyDetailsBuilder {
    return PersonCompanyDetailsBuilder(person: self.person)
  }
  
  func build() -> Person {
    return self.person
  }
}

//PersonPersonalDetailsBuilder: update personal details
class PersonPersonalDetailsBuilder: PersonBuilder {
  func nameIs(_ name: String) -> Self {
    self.person.name = name
    return self
  }
  func genderIs(_ gender: String) -> Self {
    self.person.gender = gender
    return self
  }
  func bornOn(_ birthDate: String) -> Self {
    self.person.birthDate = birthDate
    return self
  }
  func bornAt(_ birthPlace: String) -> Self {
    self.person.birthPlace = birthPlace
    return self
  }
  func havingHeight(_ height: String) -> Self {
    self.person.height = height
    return self
  }
  func havingWeight(_ weight: String) -> Self {
    self.person.weight = weight
    return self
  }
}

//PersonContactDetailsBuilder: update contact details
class PersonContactDetailsBuilder: PersonBuilder {
  func hasPhone(_ phone: String) -> Self {
    self.person.phone = phone
    return self
  }
  func hasEmail(_ email: String) -> Self {
    self.person.email = email
    return self
  }
}

//PersonAddressDetailsBuilder: update address details
class PersonAddressDetailsBuilder: PersonBuilder {
  func at(_ streeAddress: String) -> Self {
    self.person.streeAddress = streeAddress
    return self
  }
  func withZipCode(_ zipCode: String) -> Self {
    self.person.zipCode = zipCode
    return self
  }
  func inCity(_ city: String) -> Self {
    self.person.city = city
    return self
  }
}

//PersonCompanyDetailsBuilder: update company details
class PersonCompanyDetailsBuilder: PersonBuilder {
  func inCompany(_ companyName: String) -> Self {
    self.person.companyName = companyName
    return self
  }
  func asA(_ designation: String) -> Self {
    self.person.designation = designation
    return self
  }
  func hasAnnualEarning(_ annualIncome: String) -> Self {
    self.person.annualIncome = annualIncome
    return self
  }
}

/* Console output:
 
 Hitendra Solanki has contact number +91 90333-71772 and email hitendra.developer@gmail.com
 Hitendra Solanki works in Fortune 500 compay as a Software architect.
 
 */

```

In the above example, we have separated the responsibilities of Person class in different classes. Person class now only holds the data properties, while we have created the multiple builder classes having responsibilities to build/update the relative group of properties.
在上面的例子中，我们将Person类的职责划分到不同的类中。Person类现在只包含数据属性，而我们已经创建了多个builder类，它们负责构建/更新相关的属性组。

We have one base builder class PersonBuilder and have four more derived builder classes named PersonPersonalDetailsBuilder, PersonContactDetailsBuilder, PersonAddressDetailsBuilder and PersonCompanyDetailsBuilder.
我们有一个基本的构建器类PersonBuilder，还有四个派生的构建器类，分别是PersonPersonalDetailsBuilder、PersonContactDetailsBuilder、PersonAddressDetailsBuilder和PersonCompanyDetailsBuilder。

The base class PersonBuilder helps us to switch between multiple builders anytime while other four builders derived from PersonBuilder have responsibilities to update relative properties.
基类PersonBuilder帮助我们随时在多个构建器之间进行切换，而从PersonBuilder派生的其他四个构建器则负责更新相关属性。

In the above example, we can clearly see that the construction of a Person object is more readable compared to our first example WithoutDesignPatternExample1.swift also we can update the group of properties or any single property at any time in a more readable manner.
在上面的例子中，我们可以清楚地看到Person对象的结构与我们的第一个例子相比更具可读性。

![](/img/15713151587680.jpg)


In the above example, note that we are returning the builder instance its self after calling every property update method. Which helps us to write chaining of multiple methods of the same builder instead of writing multiple lines separately. This concept is known as Fluent pattern.
在上面的示例中，请注意，在调用每个属性更新方法后，我们将返回生成器实例本身。这有助于我们编写同一生成器的多个方法的链接，而不是单独编写多行。这个概念称为连贯模式。

### Benefits:
好处:

1. Easy initialize an object of a class having too many properties in a more readable manner.
    容易初始化一个具有太多属性的类的对象，具有更好的可读性。

2. Follows the single responsibility principle.
    遵循单一责任原则。

3. Initialize object or update properties in any order as per your convenience.
    根据您的方便，以任何顺序初始化对象或更新属性。

### Bonus:
奖金:

To make the builder pattern consistent across the whole project, you can create protocol as below.
要使生成器模式在整个项目中保持一致，您可以创建如下协议。

![](/img/15713152016523.jpg)



原文：https://medium.com/flawless-app-stories/design-patterns-by-tutorials-the-power-of-oop-2e871b551cbe

