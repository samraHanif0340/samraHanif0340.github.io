---
title: "Functional programming in scala specialization"
excerpt: "Course review and resources"
header:
  teaser: /assets/images/scala_c1_w4_l05_01.jpg
feature_row:
  - image_path: 
  - image_path: 
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

<!--
### Repository

The [repository](https://github.com/Adaickalavan/) consists of the following:  
* Projects - Solutions (.scala and .sc worksheets)

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