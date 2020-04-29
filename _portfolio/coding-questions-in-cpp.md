---
title: "Coding Questions in C++"
excerpt: "Programming trivia in C++"
header:
  teaser: /assets/images/coding_questions_in_cpp_01.jpg
date: "2020-01-15"   
---

{% include gallery %}

In this post, we will answer several programming challenge questions in C++. For easy testing of our single-file solutions, simply copy the entire source code and paste it into an online coding tool such as Coderpad at [https://coderpad.io/sandbox](https://coderpad.io/sandbox).

## Questions

{: .notice--success}
More programming challenge questions in C++ will be added as time permits. Let me know if there is any particular problem you would like to have solved here.

1. [Binary tree with insert, search, delete operations](#binaryTree)
1. [Move memory without temporary variable](#memoryMove)
1. [Match brackets in a string](#balancedBrackets)
1. [Sum of bits in 32-bit binary representation](#sumOfBits)
1. [Linked list - insert, delete, reverse](#linkedList)
1. [Longest continuous sequence](#longestContinuousSequence)
1. [Longest sequence](#longestSequence)
1. [Longest valid parentheses](#longestValidBrackets)
1. [Kth largest](#kthLargest)
1. [Mutatable priority queue](#mutatablePriorityQueue)

## Code
The [repository](https://github.com/Adaickalavan/coding-questions-in-cpp) contains the C++ solution codes.

## Solutions

1. <a name="binaryTree"></a> Create a binary tree with insert, search and delete operations.

    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/binaryTree.cpp).

1. <a name="memoryMove"></a> Move memory of an array from one location to another pointed by a new array pointer, without using temporary variables. The new and old memory regions might be overlapping. Copy the source array from the front into the destination, if they overlap at the front of the source array. Whereas, copy the source from the rear into the destination, if they overlap at the rear of the source array.

    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/memMove.cpp).

1. <a name="balancedBrackets"></a> Given a string containing just the characters '(', ')', '[', ']', '{', '}', determine if the input string is valid. An input string is valid if :
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

    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/balancedBrackets.cpp).

1. <a name="sumOfBits"></a> Count the number of 1's in a binary representation of a 32bit integer. A look-up table or hash map which stores integers and their corresponding bit count is used to solve this problem. Several test cases are shown below.
    ```text
    Input: 1 // binary = 0000 0001
    Input: 5 // binary = 0000 0101
    Input: 15 // binary = 0000 1111
    Input: 19 // binary = 0001 0011
    Input: 22 // binary = 0001 0110
    ```
    Expected output:
    ```text
    Output: 1
    Output: 2
    Output: 4
    Output: 3
    Output: 3
    ```

    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/sumOfBits.cpp).

1. <a name="linkedList"></a> Create a singly linked list with the following operations: insert at the tail, given a pointer delete the node (except the last node), and reverse.

    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/linkedList.cpp).

1. <a name="longestContinuousSequence"></a> Given two strings, find the longest continuous sequence of characters common to the two strings. The problem is solved using dynamic programming pradigm. A testcase is as follows.
    ```cpp
    string str1 = "abcdefghijklmnopqrst";
    string str2 = "abcd3ekl3abfgs";
    ```
    Expected output:
    ```text
    Max elements: 4
    Max sequence: abcd
    ``` 
    
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/longestContinuousSequence.cpp). 

1. <a name="longestSequence"></a> Given two strings, find the longest continuous or non-continous sequence with same ordering of characters common to the two strings. The problem is solved using dynamic programming pradigm. A testcase is as follows.
    ```cpp
    string str1 = "abcdefghijklmnopqrst";
    string str2 = "abcd3ekl3abfgs";
    ```
    Expected output:
    ```text
    Max elements: 8
    ``` 
    
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/longestSequence.cpp). 

1. <a name="longestValidBrackets"></a> Given a string containing just characters '(' and ')', return the start and end indexes of the longest valid parentheses substring. Several test cases are shown below.
    ```text
    Input: "" // answer: startIndex = 0, endIndex = 0
    Input: "(()" // answer: startIndex = 1, endIndex = 2
    Input: ")()())" // answer: startIndex = 1, endIndex = 4
    Input: "())((())" // answer: startIndex = 4, endIndex = 7
    Input: "())(()" // answer: startIndex = 0, endIndex = 1
    ```
    Expected output:
    ```text
    Longest valid parentheses of  is   , from  0 to  0
    Longest valid parentheses of (() is () , from  1 to  2
    Longest valid parentheses of )()()) is ()() , from  1 to  4
    Longest valid parentheses of ())((()) is (()) , from  4 to  7
    Longest valid parentheses of ())(() is () , from  0 to  1
    ```
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/longestValidBrackets.cpp).

1. <a name="kthLargest"></a> Return the kth largest element, from an infinite stream of integers, at any point in time. The problem is solved using a priority queue. The priority queue is implemented by a minimum heap of size k. Read the top value of the heap to get the kth largest value at any point in time. Example input:
    ```cpp
    vector<int> stream = {10, 20, 11, 70, 50, 40, 100, 5, ...};
    int k = 3;
    ```
    Expected STDOUT output:
    ```bash
    $ -, -, 10, 11, 20, 40, 50, 50, ...
    ```
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/kthLargest.cpp).

1. <a name="mutatablePriorityQueue"></a> Build a priority queue whose elements' priority can be updated dynamically. This problem is solved using the following two data structures:
    + `unordered_map<>` functions as a `set` to hold all unique elements in the queue
    + `multimap<>` functions to hold all inserted elements sorted by priority

    Operation of the data structres is as follows:
    + New element, which is not present in `unordered_map<>`, is pushed into the `multimap<>` and `unordered_map<>`.
    + New element, which is present in `unordered_map<>`, is pushed into the `multimap<>` if it has a higher priority. Priority of the element in `unordered_map<>` is incremented to the higher value.
    + To `pop()` the highest priority element from the queue: Firstly, elements are sequentially removed from the top of the `multimap<>` until an element which is also present in `unordered_map<>` is reached. This helps remove duplicate entries present in `multimap<>`. Once reached, remove the element from both `multimap<>` and `unordered_map<>`. 

    Example testcase:
    ```cpp
    MutatablePriorityQueue mpq;
    mpq.push('a',3); // value: 'a', priority: 3
    cout << mpq.top() << "\n";
    mpq.push('b',4); // value: 'b', priority: 4
    mpq.push('c',3); // value: 'c', priority: 3
    cout << mpq.top() << "\n";
    mpq.push('a',5); // value: 'a', priority: 5
    cout << mpq.top() << "\n";
    mpq.pop();
    cout << mpq.top() << "\n";
    mpq.pop();
    cout << mpq.top() << "\n";
    ```
    Expected STDOUT output:
    ```bash
    a
    b
    a
    b
    c
    ```
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/mutatablePriorityQueue.cpp).

