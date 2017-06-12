## Tutorial 101: Processing consecutive duplicates in a list using Scala - a functional approach   
### Step by step examples to solving problems 8-11 from the famous Nine-Nine problems

This tutorial presents a functional solution for solving problems 8-11 of the famous Ninety-Nine Prolog problems (by Werner Hett). Given a list of integers, the tutorial explains how to process consecutive duplicates in a list using Scala. I first start with a code snippet on problem\#8 then gradually describe a more generalized algorithm to solving these 4 problems.

For illustration purposes, I am using the following integer list that contains multiple consecutive duplicates.

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)

Lets start with the first problem and present a two code samples.

### Problem\#8: Eliminate consecutive duplicates in a list
#### *Hard coded approach 
Given the above list replace each set of consecutive integers with one copy while the order of the integers stays the same.

    def removeDup(lst : List[Int]) : List[Int] = {
       lst match {
         case head::tail => { 
           val (duplst,remainlst) = lst.span(_ == head)
           duplst.head :: removedup(remainlst)
         }
         case Nil => List()
       }
    }   

    scala> removeDup(listdup)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)


In the above, the list passed to the function is split into two lists using the span operator. The head of the first list which contains the duplicates is selected while the second list which contains the remaining elements is passed recusively using the same function. The recursion terminates when there are no more elements in the list.

#### *Using an functional approach
The above code snippets is straight forward and "duplicate elimination" is hard coded within the recursive function. To make the code more flexible we can use an externaly defined function that takes a list of duplicate integers and "purges" them into one. To use the purge function we need to pass it as a parameter to the recursive function which applies the duplicate elimination function within the algorithm. Here is the code for the two functions:

    def reduceDup(ls : List[Int], reduce: List[Int] => List[Int]) : List[Int] = {
      def iter (lst : List[Int]): List[Int] ={
        lst match {
          case head::tail => { 
            val (duplst,remainlst) = lst.span(_ == head)
            reduce(duplst) ::: iter(remainlst) }
          case Nil => List()
        }
      }
      iter(ls)
    }

    def purge(ls : List[Int]) : List[Int] = {
       ls.toSet.toList
    }

    scala> reduceDup(listdup,purge)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)

 
#### Problem: Run-length data count
Extending the code of problem\#8 we can very easily implement a simple data count which is known as "run-length encoding". Consecutive duplicates of elements are aggregated and their count is registerd into a list. To do so all we need is to define a new external function that performs the operation and then pass it as a parameter to the reduceDup recursive function above.

    def length(ls : List[Int]) : List[Int] ={
       List(ls.size)
    }
    
    scala> reduceDup(listdup,length)
    res: List[Int] = List(4, 2, 2, 1, 1, 3, 2)

This is quite simple and we can see here the beauty of functional programming where functions can be passed as parameters to other function that can use them.


The above code and then the first part which contains the duplicates is purged into a set and 
so-called run-length encoding data compression method. Consecutive duplicates of elements are encoded as terms 

### Problem\#10: Run-length encoding data compression 
Use the result of the previous problem we can try to implement the so-called run-length encoding data compression method. In this problem consecutive duplicates of data are encoded as terms (N D) where N is the number of duplicates of the Data element D.

For example given our data list

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
we need to generate the following list of tuples:

    List( (4,1),(2,2),(2,4),(1,5),(1,3),(3,4),(2,3) )
    
If we try to use the reduceDup function defined above with a new function which we call runLength we shall reach a dead end. For the parameter which takes a function in reduceDup is a mapping from List[Int] into List[Int] while here we are taking a list of integers but generating a tuple instead in the form List[(Int,Int)]. To over come this dead end we can redefine our recursive function using a parametrize types. The code to do so is as follows.      
    
    def processDup[T](ls : List[Int], reduce: List[Int] => List[T]) : List[T] = {
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

Using this new function we gain greater flexibility in defining our external functions. For example the runLength function below can be used to solve this problem.

    def runLength(ls : List[Int]) : List[(Int, Int)] ={
       List((ls.size,ls(0)))
    } 

    scala> processDup(listdup,runLength)
    res: List[(Int, Int)] = List((4,1), (2,2), (2,4), (1,5), (1,3), (3,4), (2,3))

    
Run-length encoding of a list.

Use the result of problem 9 to implement the so-called run-length encoding data compression method. Consecutive duplicates of elements are encoded as terms (N E) where N is the number of duplicates of the element E.

### Concluding Remarks
Here I presented a few code snippets that you can be useful in learning Scala. Using recursion, lists, and parametrized types you can get a good feeling for the Scala and take your first steps to explore idiomatic approaches solving problems using this powerful and expressive programming language. 
