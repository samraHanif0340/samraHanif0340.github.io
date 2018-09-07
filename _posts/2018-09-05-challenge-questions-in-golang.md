---
title: "Challenge Questions in Golang"
tags: 
  - golang 
categories:
  - golang  
last_modified_at: 2018-09-05
---

In this post, we will answer several programming challenge questions in Golang. These questions are often asked as part of interviews. Although there may be built-in methods to solve these problems, we shall only use basic data structures to solve each of them.

We prioritise recursive and clean codes. The solution codes are pretty self explanatory.

## Questions 

1) Find every pair of numbers (inclusive of duplicates) from two integer arrays (i.e. one number from each array), whose sum equals a given number. A hash map is used to store previously seen numbers, for quick O(1) retrieval later.

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

1) Delete a single node, given only the pointer to that node, in a singly-linked list. The desired node is deleted by copying the contents (i.e., value and pointer) of the next node into the current node.

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
    {: .notice--warning}
    This solution does not work for deleting the last node in a singly linked list.
