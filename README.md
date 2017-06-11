## Tutorial 101: Processing consecutive duplicates in a list using Scala - a functional approach   
### Step by step examples to solving problems 8-11 from the famous Nine-Nine problmes

This tutorial presents a functional solution for solving problems 8-11 of the famous Ninety-Nine Prolog problems (by Werner Hett). Given a list of integers, the tutorial explains how to process consecutive duplicates using Scala. The tutorial starts with a code snippet on problem \# 8 then gradually describes a more generalized algorithm to solve these 4 problems.

For illustration purposes, I am using the following integer list that contains multiple consecutive duplicates.

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)

Lets start with the first problem and present a straight forward code sample.

#### Problem 8: Eliminate consecutive duplicates of list elements.

If a list contains repeated elements they should be replaced with a single copy of the element. The order of the elements should not be changed.

Here I presented a few code snippets that you can use to learn Scala. Using recursion, lists, and parametrized types you can get a good feeling for the Scala programing language and take your first to explore idiomatic approaches to these particular kind of problems using this powerful and expressive programming language. 




