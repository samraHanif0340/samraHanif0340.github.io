---
title: "Coding Questions in C++"
excerpt: "Programming trivia in C++"
header:
  teaser: /assets/images/coding_questions_in_cpp_01.jpg
date: "2020-01-15"   
---

{% include gallery %}

In this post, we will answer several programming challenge questions in C++.

## Questions

{: .notice--success}
More programming challenge questions in C++ will be added as time permits. Let me know if there is any particular problem you would like to have solved here.

1. [Binary tree with insert, search, delete operations](#binaryTree)
1. [Move memory without temporary variable](#memoryMove)
1. [Match brackets in a string](#balancedBrackets)
1. [Longest valid parentheses substring](#longestSubstring)
1. [Sum of bits in 32-bit binary representation](#sumOfBits)

## Repository
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

1. <a name="longestSubstring"></a> Given a string containing just characters '(' and ')', return the start and end indexes of the longest valid parentheses substring. Several test cases are shown below.
    ```text
    Input: "" // answer: startIndex = 0, endIndex = 0
    Input: "(()" // answer: startIndex = 1, endIndex = 2
    Input: ")()())" // answer: startIndex = 1, endIndex = 4
    Input: "())((())" // answer: startIndex = 4, endIndex = 7
    Input: "())(()" // answer: startIndex = 0, endIndex = 1
    ```
    Expected output:
    ```text
    Longest valid substring of  is   , from  0 to  0
    Longest valid substring of (() is () , from  1 to  2
    Longest valid substring of )()()) is ()() , from  1 to  4
    Longest valid substring of ())((()) is (()) , from  4 to  7
    Longest valid substring of ())(() is () , from  0 to  1
    ```
    Link to solution [code](https://github.com/Adaickalavan/coding-questions-in-cpp/blob/master/app/balancedBrackets.cpp).
