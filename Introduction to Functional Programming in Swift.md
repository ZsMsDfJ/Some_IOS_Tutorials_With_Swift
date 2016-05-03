#Introduction to Functional Programming in Swift
##Swift函数式编程介绍
[原文地址](https://www.raywenderlich.com/114456/introduction-functional-programming-swift)

![title](https://cdn2.raywenderlich.com/wp-content/uploads/2015/08/intro-250x250.png)

在2014年WWDC上隆重登场的swift不仅仅是作为一门新的语言，它为iOS与OS X平台带来了许多新的特性。

这篇教程会针对其中的FP(Functional Programming)，介绍函数式的思想与技术。

>注意。如果你想深入了解swift的函数式编程，可以看看[Swift by Tutorials](http://www.raywenderlich.com/store/swift-by-tutorials)中的函数式编程章节，[这里](http://www.raywenderlich.com/82599/swift-functional-programming-tutorial)也可以。

##入门
在Xcode中创建一个新的playground命名为IntroFunctionalProgramming，其他选项默认，选择iOS平台，点击create创建。

Keep in mind that this tutorial will cover FP at a high level, 所以将这些概念想象成现实中的场景会很有帮助。在这里，想象一下你在为一个游乐园做一个app，游乐园的游乐设施数据由远程服务器的API提供。

使用如下代码替换playground中的模板代码:
~~~~
enum RideType {
  case Family
  case Kids
  case Thrill
  case Scary
  case Relaxing
  case Water
}
 
struct Ride {
  let name: String
  let types: Set<RideType>
  let waitTime: Double
}
~~~~
这里你创建了一个叫RideType的枚举包含了一些游乐设施的类型，还有一个叫Ride的结构体，包含设施的名称，类型的集合与等待时间。

现在你也许会想象出玩海盗船时失重的恐怖，过山车的激情。不过相信我，没有一种设施的感觉像从其他编程风格到函数式编程的转换。

##什么是函数式编程？
函数式编程与其他表现方式的区别常常被描述为命令式与声明式思想的区别。

* 命令式思想在解决问题时思考的是算法与步骤，或者说如何从A(方法)到B(解决方案)。一个典型的命令式的例子：一个纸杯蛋糕的食谱，告诉你放多少材料，怎么去混合，怎么去烘焙。
* 声明式思想分析一个问题考虑解决方案是什么，而不是如何得到解决方案。用一个食谱的例子来说明这个概念。一个声明式的食谱会给你一张纸杯蛋糕的图片并且有一段描述，而不是告诉你如何混合成分与烘焙。

函数式编程鼓励使用基于函数的声明方法来解决问题。在这篇教程中你将会看到更多的命令式与声明式编程的区别。

##函数式编程概念
在这一节中，你将会学习函数式编程中的重要概念。许多函数式编程的专著中都挑选了不变状态(immutable state)与无副作用(lack of side effects)作为最重要的方面，为何我们不从这里开始呢？
###不变性与副作用
不管你最开始学的什么编程语言，接触到的第一个概念应该就是用变量来表现数据。当你回到这种表现的最初理念时，变量就会变得很古怪。

在你的playground中添加如下看起来理所当然的代码:
~~~~
var x = 3
// other stuff...
x = 4
~~~~
作为一个开发者在学习面向过程或面向对象时就是这么讲的，你不会花一点时间会思考它。不过一个量如何能先等于3然后等于4？

![x](http://www.raywenderlich.com/wp-content/uploads/2015/08/x3.png)

变量意为一个变化的量。考虑数学中的量x，你会在软件行为中将时间作为关键参数。你在改变变量时创造了可变的状态。

若在一个相对简单的系统中，可变状态也许不是一个大问题。不过当连接多个对象时，像一个庞大的OOP系统，可变状态就会很让人头痛。

例如，当2个或多个线程同时访问一个变量时，它们也许在访问或修改时出错，出现不期望的行为。像竞态条件、锁死等问题。

想象一下你可以在一个状态从不改变的地方写代码。在并发系统中的大量问题都会消失———poof！你可以创建一个不变属性来实现它，或者数据在程序运行期间不允许更改。

在objective-C中可以通过一个常量来实现，不会你的属性在创建时默认是可变模式。在swift中默认是不变的。

使用不变数据的优点就是在代码单元中使用时将会没有副作用，意味着你代码中函数不会改变它们外部的元素，当调用函数时不会发生可怕的现象。

在你playground的Ride结构体后面添加如下数组，在这篇教程中使用一个不变的swift常量来表现你的主要数据，看看会发生什么:
~~~~
let parkRides = [
  Ride(name: "Raging Rapids", types: [.Family, .Thrill, .Water], waitTime: 45.0),
  Ride(name: "Crazy Funhouse", types: [.Family], waitTime: 10.0),
  Ride(name: "Spinning Tea Cups", types: [.Kids], waitTime: 15.0),
  Ride(name: "Spooky Hollow", types: [.Scary], waitTime: 30.0),
  Ride(name: "Thunder Coaster", types: [.Family, .Thrill], waitTime: 60.0),
  Ride(name: "Grand Carousel", types: [.Family, .Kids], waitTime: 15.0),
  Ride(name: "Bumper Boats", types: [.Family, .Water], waitTime: 25.0),
  Ride(name: "Mountain Railroad", types: [.Family, .Relaxing], waitTime: 0.0)
]
~~~~
当你使用let创建parkRides而不是var时，这个数组及它的项就是不可变的。使用如下方法尝试改变数组中的一项:
~~~~
parkRides[0] = Ride(name: "Functional Programming", types: [.Thrill], waitTime: 5.0)
~~~~
改变其中的项会发生编译错误。去修改那些ride吧！没门儿，小鬼！
##模块化
你将会写出你的第一个函数，在swift的String使用NSString的方法，因此需要在playground的头部添加Foundation框架:
~~~~
import Foundation
~~~~
假设你需要一个按字母表排序的设施名称列表，使用命令式的方法来实现的话，添加如下函数到你的playground底部:
~~~~
func sortedNames(rides: [Ride]) -> [String] {
  var sortedRides = rides
  var i, j : Int
  var key: Ride
 
  // 1
  for (i = 0; i < sortedRides.count; i++) {
    key = sortedRides[i]
 
    // 2
    for (j = i; j > -1; j--) {
      if key.name.localizedCompare(sortedRides[j].name) == .OrderedAscending {
        sortedRides.removeAtIndex(j + 1)
        sortedRides.insert(key, atIndex: j)
      }
    }
  }
 
  // 3
  var sortedNames = [String]()
  for ride in sortedRides {
    sortedNames.append(ride.name)
  }
 
  print(sortedRides)
 
  return sortedNames
}
~~~~
这里你做了:

1. 在函数内遍历设施
2. 执行插入排序
3. 取得排序后设施中的名称

从sortedNames(:)的角度来看，它接收了设施的列表，然后输出了排序后的包含设施名称的列表。sortedNames(:)外面的东西没有受到影响，为了证明，首先我们打印出排序后的设施名称:
~~~~
print(sortedNames(parkRides))
~~~~
然后收集设施列表中的名称并打印出来:
~~~~
var originalNames = [String]()
for ride in parkRides {
  originalNames.append(ride.name)
}
 
print(originalNames)
~~~~
在编辑器中你会发现sortedNames(:)中对设施的排序并没有改变输入的设施列表。你使用命令式代码创建的这个模块函数被认为是准函数式(quasi-functional)的。

即使如此，用命令式语句写出的函数依旧很冗长、笨拙。如果有一种技术可以通过使用像sortedNames(:)这样的函数更进一步的简化代码岂不是很棒？

###第一类与高阶函数
另一个函数式编程的特点就是函数们。第一类函数是其中一种，它意味着可以被分配给一个值，可以作为其它函数的输入与输出。再往上就是高阶函数，它可以将函数作为参数并返回其它函数。

![first-class](http://www.raywenderlich.com/wp-content/uploads/2015/08/firstclass.png)

在这一节中，你将会学习到函数式编程中几个常见的高阶函数——filter、map与reduce。

####filter
在swift中，filter(:)是CollectionType型值的一个方法，比如说数组(array),它可以将另一个函数作为参数输入返回数组中应用这个函数返回为真的值。

filter(:) 将输入的函数应用到数组中的每一个元素上，返回另一个包含返回值为真的所有元素的数组。

回想一下你的sortedNames做过什么，用声明式的思想去思考一下而不是命令式。

注释掉sortedNames，你将会更有效率的重写它。在playground底部创建一个将Ride作为参数的函数:
~~~~
func waitTimeIsShort(ride: Ride) -> Bool {
  return ride.waitTime < 15.0
}
~~~~
waitTimeIsShort(:)接受一个ride，如果等待时间小于15秒则返回true，否则返回false。

在parkRides上调用filter然后输入你刚刚创建的函数:
~~~~
var shortWaitTimeRides = parkRides.filter(waitTimeIsShort)
print(shortWaitTimeRides)
~~~~