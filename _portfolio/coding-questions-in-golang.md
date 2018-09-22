---
title: "Coding Questions in Golang"
excerpt: "Programming trivia in Golang"
header:
  teaser: assets/images/gopher_trivia.jpg
---

{% include toc %}

In this post, we will answer several programming challenge questions in Golang. These questions are often asked as part of interviews. Although there may be built-in methods to solve these problems, we shall only use basic data structures to solve each of them.

We prioritise recursive and clean codes. The solution codes are pretty self explanatory.

## Questions

1. [Find pair of numbers, with a given sum, from two arrays](#findPair)
2. [Delete single node, given a single pointer, in a singly linked list](#deleteSinglyLinkedNode)
3. [K-Means Clustering algorithm](#kmeans)
4. [Given two sorted lists, merge them into a single sorted list](#mergeSortedList)
5. [Print fibonanci numbers](#fibonanci)

{: .notice--success}
More programming challenge questions in Golang will be added as time permits. Let me know if there is any particular problem you would like to have solved here.

## Repository
The [repository](https://github.com/Adaickalavan/Coding-Questions-in-Golang) contains the Golang solution codes.

## Solutions

1. <a name="findpair"></a> Find every pair of numbers (inclusive of duplicates) from two integer arrays (i.e. one number from each array), whose sum equals a given number. A hash map is used to store previously seen numbers, for quick O(1) retrieval later.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/findPair).

    ```go
    package main

    import (
      "fmt"
    )

    func findPair(arr1 []int, arr2 []int, n int) [][]int {
      hm := make(map[int]int)
      output := [][]int{}

      for _, val := range arr1 {
        hm[val] = hm[val] + 1
      }

      for _, val := range arr2 {
        temp := n - val
        if count, ok := hm[temp]; ok {
          for jj := count; jj > 0; jj-- {
            output = append(output, []int{temp, val})
          }
        }
      }

      return output
    }

    func main() {
      arr1 := []int{-1, -2, 4, 4, -2, -6, 5, -7}
      arr2 := []int{6, 3, 4, 0, 0}
      n := 4 //Desired sum
      output := findPair(arr1, arr2, n)
      fmt.Println(output)
    }
    ```

   Expected output:

    ```go
    [[-2 6] [-2 6] [4 0] [4 0] [4 0] [4 0]]
    ```

1. Delete a single node, given only the pointer to that node, in a singly-linked list. The desired node is deleted by copying the contents (i.e., value and pointer) of the next node into the current node.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/deleteSinglyLinkedNode).

    {: .notice--warning}
    This solution does not work for deleting the last node in a singly linked list.

    ```go
    package main

    import (
      "fmt"
    )

    type node struct {
      val      int
      nextNode *node
    }

    func delete(ptr *node) {
      ptrNext := ptr.nextNode
      ptr.val = ptrNext.val
      ptr.nextNode = ptrNext.nextNode
    }

    func main() {
      sixthNode := &node{val: 6, nextNode: nil}
      fifthNode := &node{val: 5, nextNode: sixthNode}
      fourthNode := &node{val: 4, nextNode: fifthNode}
      thirdNode := &node{val: 3, nextNode: fourthNode}
      secondNode := &node{val: 2, nextNode: thirdNode}
      firstNode := &node{val: 1, nextNode: secondNode}

      delete(sixthNode)

      for ii := firstNode; ii != nil; ii = ii.nextNode{
        fmt.Println(ii)
      }
    }
    ```

    Expected output:

    ```go
    &{1 0xc04204a200}
    &{2 0xc04204a1f0}
    &{3 0xc04204a1e0}
    &{5 0xc04204a1c0}
    &{6 <nil>}
    ```

1. Given a list of data points and a list of centroids, perform `K-Means Clustering` to return a new set of updated centroids.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/kmeans).

1. Given two sorted lists, merge them into a single sorted list.

   Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/mergeSortedList)

    ```go
    package main

    import (
      "fmt"
    )

    //Recursively merge two ordered list
    func arrange(arr1 []int, arr2 []int, output *[]int) {
      switch {
      case len(arr1) == 0 && len(arr2) == 0:
        return
      case len(arr1) == 0 && len(arr2) > 0:
        *output = append(*output, arr2...)
        return
      case len(arr2) == 0 && len(arr1) > 0:
        *output = append(*output, arr1...)
        return
      default:
        if arr1[0] <= arr2[0] {
          *output = append(*output, arr1[0])
          arrange(arr1[1:], arr2, output)
        } else {
          *output = append(*output, arr2[0])
          arrange(arr1, arr2[1:], output)
        }
      }
    }

    func main() {
      arr1 := []int{1, 4, 7}
      arr2 := []int{3, 4, 5}
      output := []int{}
      arrange(arr1, arr2, &output)
      fmt.Println(output)
    }
    ```

1. Print Fibonanci numbers using closures in Golang.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/fibonanci).

    ```go
    package main

    import (
      "fmt"
    )

    func evenFib() func() float64 {
      x := float64(0)
      y := float64(1)
      return func() float64 {
        x, y = y, x+y
        return y
      }
    }

    func main() {
      f := evenFib()
      for ii := 0; ii < 5; ii++ {
        result := f()
        fmt.Println(result)
      }
    }
    ```