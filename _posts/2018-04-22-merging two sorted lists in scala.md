---
title: "Merging two sorted lists in Scala"
tags: 
  - scala 
categories:
  - scala   
last_modified_at: 2018-04-22
---

This post elucidates the power of programming in Scala. We describe how to merge two sorted lists into a third sorted list in Scala. Although there are built-in methods to perform this form of sorting, here, we shall use pattern matching and recursion to solve the problem. 

```scala
def merge(i:List[int], j:List[int]):List[int]={
	(i,j) match {
		case (Nil, Nil) => Nil
		case (x::xs, Nil) => i
		case (Nil, y::ys) => j
		case (x::xs, y::ys) => {
			if (x <= y)
				x :: merge(i.tail,j)
			else
				y :: merge(i,j.tail)
		}
	}
}
```

Programming in Scala is simple and elegant, isn't it?
