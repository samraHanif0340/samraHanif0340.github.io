---
title: "Functional programming in scala specialization"
excerpt: "Course review and resources"
header:
  teaser: /assets/images/scala_c2_w4_a01_01.jpg
feature_row:
  - image_path: /assets/images/scala_c1_w4_l05_01.jpg
  - image_path: /assets/images/scala_c2_w4_a01_01.jpg 
---

{% include feature_row %}

{% include toc %}

## Review

### Course Link
<a href="https://www.coursera.org/specializations/scala">Functional programming in scala specialization - Martin Odersky</a>

### Info
This is an excellent course on mastering Scala, delivered in Coursera by the founder of the language himself, Prof. Martin Odersky from the famed  École Polytechnique Fédérale de Lausanne (EPFL). 

<!-- 
The course covers the three main neural network architectures, namely, feedforward neural networks, convolutional neural networks, and recursive neural networks. Their related applications, extensions, and tweaks are covered with sufficient example codes in Python, TensorFlow, and Keras. The specialisation uses Jupyter for its assignments. 
-->

### Syllabus
This is a 5 course specialisation.
1. Functional Programming Principles in Scala
2. Functional Program Design in Scala
3. Parallel programming
4. Big Data Analysis with Scala and Spark
5. Functional Programming in Scala Capstone

{: .notice--warning}
The content of this page is now being added in stages. Visit at a later time for further updates.


### Repository

The [repository](https://github.com/Adaickalavan/Functional-Programming-in-Scala-Specialization-EPFL-Coursera) consists of the following:  
* In-class exercises - codes (.scala and .sc worksheets)
* Projects - codes (.scala and .sc worksheets)

<!--
### Verified Certificate

* Neural Networks and Deep Learning 
[![certificate](/assets/images/certificate_neural_networks_and_deep_learning_coursera.jpg){:height="60%" width="60%" .align-center}](https://www.coursera.org/account/accomplishments/certificate/PLGXT56A8AF4)
-->

### Notes

Users of IntelliJ-Idea as their Scala IDE might have encountered the following problem. Assume you have a scala class with accompanying methods written in a Foo.scala file.
```scala
//File : Foo.scala
class Foo {
  def foo1: Int = 2
}
```
Then you try to import the Foo class into a worksheet named Bar.sc to test the behaviour of your Foo class.
```scala
//File : Bar.sc
import Foo
object Bar {
  val bar1 = new Foo
}
```
Everything works fine initially. However, if you return to Foo.scala file and make some changes, these changes will not be picked up by your Bar.sc worksheet. Errors such as "Object cannot be found" (i.e., referring to your new/modified object in Foo.scala file) will be thrown when trying to run your worksheet. Rebuilding, recompiling, or "Make Project" does not help. 

However, upon restarting the IntelliJ application, your Bar.sc worksheet will work flawlessly and detect the new changes in Foo.scala file.

Do the following to solve the problem:
* Go to File -> Settings -> Languages & Frameworks -> Scala -> Worksheet (tab)
* Unselect "Run worksheet in the compiler process"

## Course 1: Functional programming principles in Scala
### Week 4: Pattern matching

In lecture 5, week 4, on the topic of pattern matching, we are given a programming exercise shown in the image below, for which the solution was not given in class. 

![scala](/assets/images/scala_c1_w4_l05_01.jpg){:height="60%" width="60%" .align-center}

I have provided my solution to this in-class exercise. The underscore ```_``` acts as a wildcard representing anyting in Scala.

```scala
object week4_5{

  trait Expr
  case class Number(n: Int) extends Expr
  case class Sum(e1: Expr, e2: Expr) extends Expr
  case class Var(x: String) extends Expr
  case class Prod(e1: Expr, e2: Expr) extends Expr

  def show(e:Expr):String = {
    def format(e:Expr):String = {
      e match{
        case Sum(_,_) => "(" + show(e) + ")"
        case _ => show(e)
      }
    }
    e match{
      case Number(x) => x.toString
      case Sum(l,r) => show(l) + "+" + show(r)
      case Var(x) => x
      case Prod(l,r) => format(l) + "*" + format(r)
    }
  }

  show(Sum(Number(1),Number(64)))
  show(Sum(Prod(Number(2),Var("x")),Var("y")))
  show(Prod(Sum(Number(2),Var("x")),Var("y")))

}
```

The output upon evaluating the above worksheet is:
```scala
defined trait Expr
defined class Number
defined class Sum
defined class Var
defined class Prod

show: show[](val e: Expr) => String

res0: String = 1+64
res1: String = 2*x+y
res2: String = (2+x)*y
```

### Week 6: Putting the pieces together

In lecture 5, week 6, we are given a programming exercise on obtaining all phrases of words that can serve as mnemonics for a phone number. The URL to download the vocabulary is not clear from the instructional video. 

![scala](/assets/images/scala_c1_w6_l05_01.jpg){:height="60%" width="60%" .align-center}

Hence, we provide the complete line of code to download the vocabulary: 

```scala
val in = Source.fromURL("https://lamp.epfl.ch/files/content/sites/lamp/files/teaching/progfun/linuxwords.txt")
```

## Course 2: Functional program design in Scala
### Week 4 Lecture 3: Functional reactive programming

Odersky describes the "Signal" method for reactive programming, using a bank account example. In order to track the logic while using "Signal", an "accounts.scala" file is provided in the repository (in addition to "accounts.sc" file) with many helpful print statements. Running "accounts.scala" file in debug mode, will print key values at intermediate stages, thus allowing us to track the logic behind "Signal" operation. 
```scala
  println("xxx.caller->"+caller) //print identity of caller
  println("xxx.observers->"+observers) //print identity of observers in the set
  println("xxx.this->"+this) //print identitiy of the current object
```

In short, we should remember the following points to understand "Signal":
1. On instantiation or upon being called, Signal A updates its formula/definition in the ```myExpr = () => expr``` variable. Signal A also updates its ```myValue``` variable.
1. Whenever an object B calls Signal A, Signal A stores the identity of the object B in its observers list. This is achieved via ```observers += caller.value``` in ```def apply()``` function.
1.  Whenever a Signal A is updated or evaluated, it updates its ```myValue``` variable via ```myValue = newValue``` operation in ```protected def computeValue()``` function. Additionally, Signal A calls all of its observers to update their values too via ```observers = Set.empty; obs.foreach(_.computeValue())``` in ```protected def computeValue()``` function. 

Notice that the observers list of a Signal A is emptied after calling its observers. The observers list of Signal A will be populated again when some other object B calls Signal A in order to compute object B's ```myValue``` variable.

### Week 4 Assignment: Calculator
Contrary to the `Signal` class introduced during Lecture 3, the `Signal` class provided in the assignment has an additional `var observed` besides the original `var observers`. Here `observers` refer to objects which are dependent on me whereas `observed` refers to objects that I depend on. The relevant portions of the code are shown below.
```scala
class Signal[T](expr: => T) {

  private var observers: Set[Signal[_]] = Set()
  private var observed: List[Signal[_]] = Nil
  ...

  protected def computeValue(): Unit = {
      for (sig <- observed)
        sig.observers -= this
      observed = Nil
      ...
    }

  ...

  def apply() = {
      ...
      caller.value.observed ::= this
      myValue
    }
}
```
The principles of `var observed` is described next using an example. Assume we have 3 `Signals` defined as follows:
```
val a = Var(1)
val b = Var(a() + 1)
val c = Var(b() + 1)
```
On iniatilization of the three signals, the table below shows the `var observers` and `var observed` of each signal.

| Signal | observers | observed |
|:------:|:---------:|:--------:|
| a      | b         | none     |
| b      | c         | a        |
| c      | none      | b        |

If at a later time, the `expr` of signal `b` is changed to `b() = 2`, then value of signal `c` will be recomputed accordingly. The `var observers` and `var observed` list will be updated to become:

| Signal | observers | observed |
|:------:|:---------:|:--------:|
| a      | none      | none     |
| b      | c         | none     |
| c      | none      | b        |

Since signal `b` does not depend on signal `a` anymore, signal `b` is removed from `var observers` of signal `a`. Hence, in future, if signal `a` is updated, it does not need to inform or call signal `b` anymore.

Note that the `Signal` class works correctly even without `var observed`. In the example above, without use of `var observed`, signal `a` makes a redundant call to signal `b` when signal `a` is updated in the future. Essentially, use of `var observed` avoids unnecessary computations.

