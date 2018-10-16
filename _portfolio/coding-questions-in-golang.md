---
title: "Coding Questions in Golang"
excerpt: "Programming trivia in Golang"
header:
  teaser: assets/images/gopher_trivia.jpg
feature_row:
  - image_path: /assets/images/gophercises_punching.jpg
  - image_path: /assets/images/gophercises_lifting.jpg
---

{% include feature_row %}

{% include toc %}

In this post, we will answer several programming challenge questions in Golang. These questions are often asked as part of interviews. Although there may be built-in methods to solve these problems, we shall only use basic data structures to solve each of them.

We prioritise recursive, concurrent, and clean codes. The solution codes are pretty self explanatory.

## Questions

1. [Find pair of numbers, with a given sum, from two arrays](#findPair)
2. [Delete single node, given a single pointer, in a singly linked list](#deleteSinglyLinkedNode)
3. [K-Means Clustering algorithm](#kmeans)
4. [Given two sorted lists, merge them into a single sorted list](#mergeSortedList)
5. [Print fibonanci numbers](#fibonanci)
6. [Reverse a string](#reverseString)
7. [Match brackets in a string](#matchBrackets)

{: .notice--success}
More programming challenge questions in Golang will be added as time permits. Let me know if there is any particular problem you would like to have solved here.

## Repository
The [repository](https://github.com/Adaickalavan/Coding-Questions-in-Golang) contains the Golang solution codes.

## Solutions

1. <a name="findPair"></a> Find every pair of numbers (inclusive of duplicates) from two integer arrays (i.e. one number from each array), whose sum equals a given number. A hash map is used to store previously seen numbers, for quick O(1) retrieval later.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/findPair).

    ```go
    package main

    import (
      "fmt"
    )

    //Retrieve pairs of numbers from two arrays which satisfies a given sum
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
      arr2 := []int{0, 6, 3, 4, 0}
      output := findPair(arr1, arr2, 4)
      fmt.Print(output)

    }

    ```

    Expected output:

    ```go
    [[4 0] [4 0] [-2 6] [-2 6] [4 0] [4 0]]
    ```

1. <a name="deleteSinglyLinkedNode"></a> Delete a single node, given only the pointer to that node, in a singly-linked list. The desired node is deleted by copying the contents (i.e., value and pointer) of the next node into the current node.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/deleteSinglyLinkedNode).

    {: .notice--warning}
    This solution does not work for deleting the last node in a singly linked list.

    ```go
    package main

    import (
      "fmt"
    )

    //Node contains value and pointer to next node
    type node struct {
      val      int
      nextNode *node
    }

    //Deletes node given by pointer
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

      delete(fourthNode)

      for ii := firstNode; ii != nil; ii = ii.nextNode {
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

1. <a name="kmeans"></a> Given a list of data points and a list of centroids, perform `K-Means Clustering` to return a new set of updated centroids. Goroutines are used to parallelize the code execution.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/kmeans).

1. <a name="mergeSortedList"></a> Given two sorted lists, merge them into a single sorted list.

   Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/mergeSortedList).

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
    Expected output:
    ```go
    [1 3 4 4 5 7]
    ```

1. <a name="fibonanci"></a> Print Fibonanci numbers using closures in Golang.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/fibonanci).

    ```go
    package main

    import (
      "fmt"
    )

    //Closure returns a fibonanci function
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
    Expected output:
    ```go
    1
    2
    3
    5
    8
    ```
1. <a name="reverseString"></a> Reverse a given string. The string is reversed in parallel using goroutines and channels.

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/reverseString).

    ```go
    package main

    import "fmt"

    //Reverse a string in parallel
    func reverser(arr1 []rune, length int, c0 chan int) {
      c1 := make(chan int)
      switch {
      case length == 0:
        c0 <- 1
        return
      case length == 1:
        c0 <- 1
        return
      case length >= 2:
        go reverser(arr1[1:length-1], length-2, c1)
        arr1[0], arr1[length-1] = arr1[length-1], arr1[0]
        <-c1
        c0 <- 1
      }
    }

    func main() {

      str := "abcdefgh"
      str1 := []rune(str)

      ch := make(chan int)

      go reverser(str1, len(str1), ch)
      <-ch

      fmt.Print(string(str1))
    }
    ```
    Expected output:
    ```text
    hgfedcba
    ```

1. <a name="matchBrackets"></a> Given a string containing just the characters '(', ')', '[', ']', '{', '}', determine if the input string is valid. An input string is valid if :
    + Open brackets must be closed by the same type of brackets.
    + Open brackets must be closed in the correct order.
  
    Note that an empty string is also considered valid. String below are considered valid strings.
    ```text
    ""
    "()"
    "()[]{}"
    "{[]}"
    ```
    Strings below are considered as invalid strings.
    ```text
    "["
    "}{"
    "{()"
    "(]"
    "([)]"
    ```

    Link to solution [code](https://github.com/Adaickalavan/Coding-Questions-in-Golang/tree/master/matchBrackets).

    ```go
    package main

    import (
      "fmt"
    )

    func main() {
      strList := []string{"", "()", "()[]{}", "{[]}", "[", "}{", "{()", "(]", "([)]"}
      for _, str := range strList {
        input := []rune(str)
        valid := matchBrackets(&[]rune{}, input)
        fmt.Println("The string", str, "is", valid)
      }
    }

    //Map closing brackets to corresponding opening brackets
    var bracketMap = map[rune]rune{
      rune(')'): rune('('),
      rune('}'): rune('{'),
      rune(']'): rune('['),
    }

    //Recursively compare brackets in a string
    func matchBrackets(stack *[]rune, input []rune) bool {
      switch {
      case len(*stack) == 0 && len(input) == 0:
        return true
      case len(*stack) != 0 && len(input) == 0:
        return false
      case len(*stack) == 0 && len(input) != 0:
        switch _, ok := bracketMap[input[0]]; ok {
        case true: //closing bracket
          return false
        default: //opening bracket
          *stack = append(*stack, input[0])
          return matchBrackets(stack, input[1:])
        }
      default: //len(*stack) != 0 && len(input) != 0
        switch elem, ok := bracketMap[input[0]]; ok {
        case true: //closing bracket
          if elem == (*stack)[len(*stack)-1] {
            *stack = (*stack)[:len(*stack)-1]
            return matchBrackets(stack, input[1:])
          }
          return false
        default: //opening bracket
          *stack = append(*stack, input[0])
          return matchBrackets(stack, input[1:])
        }
      }
    }
    ```
    Expected output:
    ```text
    The string  is true
    The string () is true
    The string ()[]{} is true
    The string {[]} is true
    The string [ is false
    The string }{ is false
    The string {() is false
    The string (] is false
    The string ([)] is false
    ```
