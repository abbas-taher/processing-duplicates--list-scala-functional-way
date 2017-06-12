## Tutorial 101: Processing consecutive duplicates in a list using Scala - a functional approach   
### Step by step examples to solving problems 8-11 from the famous Nine-Nine problems

This tutorial presents a functional solution for solving problems 8-11 of the famous Ninety-Nine Prolog problems (by Werner Hett). Given a list of integers, the tutorial explains how to process consecutive duplicates using Scala. I first start with a code snippet on problem\# 8 then gradually describe a more generalized algorithm to solving these 4 problems.

For illustration purposes, I am using the following integer list that contains multiple consecutive duplicates.

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)

Lets start with the first problem and present a straight forward code sample.

### Problem\#8: Eliminate consecutive duplicates in a list
#### *Hard coded approach 
Given the above list replace each set of consecutive integers with one copy while the order of the integers is not changed.

    def removedup(lst : List[Int]) : List[Int] = {
       lst match {
         case head::tail => { 
           val (duplst,remainlst) = lst.span(_ == head)
           duplst.head :: removedup(remainlst)
         }
         case Nil => List()
       }
    }   

    scala> removedup(listdup)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)


In the above, the list passed to the function is split into two lists using the span operator. The head of the first list which contains the duplicates is selected while the second list which contains the remaining elements is passed recusively using the same function.

#### *Using an functional approach
The above code snippets is straight forward and "duplicate elimination" is hard coded within the recursive function. To make the code more flexible we can use an externaly defined function that takes a list of duplicate integers and purges them into one. Also we need to change the recursive function to take the external purge function as a parameter. The code to do so is as follows:

    def processdup[T](ls : List[Int], reduce: List[Int] => List[T]) : List[T] = {
      def iter (lst : List[Int]): List[T] ={
       lst match {
         case head::tail => { 
           val (duplst,remainlst) = lst.span(_ == head)
           reduce(duplst) ::: iter(remainlst)
         }
         case Nil => List[T]()
       }
      }
      iter(ls)
    }


    def purge(ls : List[Int]) : List[Int] = {
       ls.toSet.toList
    }

    scala> processdup(listdup,purge)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)

 
The above code and then the first part which contains the duplicates is purged into a set and 

### Concluding Remarks
Here I presented a few code snippets that you can be useful in learning Scala. Using recursion, lists, and parametrized types you can get a good feeling for the Scala and take your first steps to explore idiomatic approaches solving problems using this powerful and expressive programming language. 
