## Tutorial 101: Processing consecutive duplicates in a list using Scala - the functional approach   
### Step by step to solving problems 8-11 from the Ninety Nine set of exercises in a generalized way

This tutorial presents solutions for solving problems 8-11 of the famous 99 Prolog exercises (by Werner Hett). Given a list of integers, we’ll look at different methods to process consecutive duplicates. First, we start with a code snippet on problem\#8 then gradually describe a more generalized algorithm using higher order functions to solve these 4 problems in Scala.

I am using the following integer list that contains multiple consecutive duplicates as a basis for testing.

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)

Let's start with the first problem where we present two code samples: a hard coded approach and a flexible functional method that enables reusability.

### Problem\#8: Eliminate consecutive duplicates in a list
#### *Hard coded approach 
Given the above list remove duplicate copies of each consecutive integer while keeping the sequence intact.

    def removeDup(lst : List[Int]) : List[Int] = {
       lst match {
         case head::tail => { 
           val (duplst,remainlst) = lst.span(_ == head)
           duplst.head :: removeDup(remainlst) }
         case Nil => List()
       }
    }   

    scala> removeDup(listdup)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)

In the above function the list is split into two sub-lists using the "span" operator. The head of the first list which contains the duplicates is selected while the second list (which contains the remaining elements) is passed recursively using the same function. The recursion terminates when there are no more elements to be removed from the remain list.

#### *Using a functional approach
The above code snippets is straight forward and "duplicate elimination" is hard coded within the recursive function. To make the code more reusable we can use an externally defined function that takes a list of duplicate integers and "purges" them into one. We then pass the purge function as a parameter to the higher order function "reduceDup" which calls the recursive algorithm "iter". Here is the code for the three functions:

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

Without the inner "iter" function you can not recursively traverse the list. This is a common technique used in Scala for such cases and the language enables programmers to define nested functions where parameters from the outer function are in scope within the inner function. Also, in reduceDup we are concatenating two lists using the ::: operator while in removeDup we only add a single element to the start of the list using the :: operator.

### Problem\#10: Run-length encoding 
#### Problem: Run-length data count
Reusing the code of problem\#8 we can very easily implement a simple data count commonly known as "run-length encoding". Consecutive duplicates of elements are aggregated and their count is registered into a list. Thus, all we need is to define a new external function that performs the operation and then passes it as a parameter to the reduceDup recursive function set above.

    def length(ls : List[Int]) : List[Int] ={
       List(ls.size)
    }
    
    scala> reduceDup(listdup,length)
    res: List[Int] = List(4, 2, 2, 1, 1, 3, 2)

This is quite simple and we can see here the beauty of functional programming and code reusability where functions can be passed as parameters to higher order functions.

#### Problem\#10: Run-length encoding - simple data compression 
Similar to the result of the previous problem, we can implement Problem\#10 - the so-called run-length encoding data compression method. In this problem consecutive duplicates of data are encoded as a tuple (N,D) where N is the number of duplicates of the Data element D.

For example given our data list we need to generate the following list of tuples:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    res: List( (4,1), (2,2), (2,4), (1,5), (1,3), (3,4), (2,3) )
    
If we try to use the reduceDup function defined above with a new function which we call runLength we shall reach a dead end. For the reduce parameter (which takes a function in reduceDup) is a mapping from List[Int] into List[Int] while here we are taking a list of integers but generating a tuple in the form List[(Int,Int)]. To overcome this problem we redefine our recursive function set using a generic type "T". The code to do so is as follows.      
    
    // generic version
    def processDup[T](ls : List[Int], reduce: List[Int] => List[T]) : List[T] = {
      def iter (lst : List[Int]): List[T] ={
        lst match {
          case head::tail => { 
            val (duplst,remainlst) = lst.span(_ == head)
            reduce(duplst) ::: iter(remainlst) }
          case Nil => List[T]()
        }
      }
      iter(ls)
    }

The type "T" has been abstracted and assigned in the return types of both the outer and inner functions; as a result, we gain greater flexibility in passing different kinds of external functions as parameters to perform various operations. For example, the runLength function below can be used to solve problem\#10 and return a list of tuples instead of a list of integers.

    def runLength(ls : List[Int]) : List[(Int, Int)] ={
       List((ls.size,ls.head))
    } 

    scala> processDup(listdup,runLength)
    res: List[(Int, Int)] = List((4,1), (2,2), (2,4), (1,5), (1,3), (3,4), (2,3))

#### Problem\#8: Revisited
We can use the parameterized generic version as well to solve Problem\#8 without writing a single line of code. Simply calling "processDup" instead of "reduceDup" and using the purge function will do the job.

    def purge(ls : List[Int]) : List[Int] = {
       ls.toSet.toList
    }

    scala> processDup(listdup,purge)
    res: List[Int] = List(1, 2, 4, 5, 3, 4, 3)

### Problem\#9: Pack consecutive duplicates of a list into sublists
In Problem\#9 we need to group repeated elements into separate sublists. Given our generic function "processDup" above, the solution is amazingly simple:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    def group(ls : List[Int]) : List[List[Int]] = {
       List(ls)
    }
    
    scala> processDup(listdup,group)
    res: List[List[Int]] = List(List(1, 1, 1, 1), List(2, 2), List(4, 4), List(5), List(3), List(4, 4, 4), List(3, 3))

### Problem\#11: Modified Run-length encoding - simple data compression 
In this problem we modify the result of problem\#10 such that if an element has no duplicates it is simply copied into the result list. Only elements with duplicates are transferred as an (N,D) tuple. For example, given our data list we need to generate the following list of data/tuples:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    res: List( (4,1), (2,2), (2,4), 5, 3, (3,4), (2,3) )

Using the result of Problem\#10 we map each element in the list of tuples according to whether "N" is equal or different from 1. The code snippet to perform the transformation:

     scala> processDup(listdup, runLength).map{ case (len,e) => { if (len==1) e else (len,e) } } 

### Concluding Remarks
We have demonstrated in these code snippets that solving a few problems in a unified way is not very difficult. With the right approach and using abstraction, parameterized types, and higher order functions you can write very concise and reusable code in Scala. Please take your time to experiment with these samples as a first step to exploring the idiomatic approach to functional programming.

Last, as a bonus exercise you can try to modify the "processDup" function above to work with another parameterized data type like strings for example. By modifying the processed list as List[A] instead of List[Int] you can create a more generic recursive function that can be used without changing any of the 4 presented external functions.   
