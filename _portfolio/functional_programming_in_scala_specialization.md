---
title: "Functional programming in scala specialization - Martin Odersky"
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
* Lectures - Notes
* Projects - Solutions (.scala and .sc worksheets)

### Verified Certificate

* Neural Networks and Deep Learning 
[![certificate](/assets/images/certificate_neural_networks_and_deep_learning_coursera.jpg){:height="60%" width="60%" .align-center}](https://www.coursera.org/account/accomplishments/certificate/PLGXT56A8AF4)
-->

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

