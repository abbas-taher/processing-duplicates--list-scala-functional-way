## Tutorial 101: Processing consecutive duplicates in a list using Scala - the functional way   
### Step by step examples to solving problems 8-11 from the Nine-Nine set of exercises

This tutorial presents solutions for solving problems 8-11 of the famous Ninety-Nine Prolog problems (by Werner Hett). Given a list of integers, we’ll look at different methods to process consecutive duplicates. First, we start with a code snippet on problem\#8 then gradually describe a more generalized algorithm to solving these 4 problems in Scala.

For illustration purposes, I am using the following integer list that contains multiple consecutive duplicates.

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)

Let's start with the first problem where we present two code samples: a hard coded approach and a more flexible functional method.

### Problem\#8: Remove consecutive duplicates in a list
#### *Hard coded approach 
Given the above list replace each set of the consecutive integers with one copy while the sequence of the integers remains the same.

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

In the above, the list passed to the function is split into two lists using the "span" operator. The head of the first list which contains the duplicates is selected while the second list which contains the remaining elements is passed reclusively using the same function. The recursion terminates when there are no more elements in the list to be removed.

#### *Using a functional approach
The above code snippets is straight forward and "duplicate elimination" is hard coded within the recursive function. To make the code more flexible we can use an externally defined function that takes a list of duplicate integers and "purges" them into one. To use the purge function we need to pass it as a parameter to the main function which then applies the duplicate elimination function within the recursive algorithm. Here is the code for the two functions:

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

Without the inner "iter" function you can recursively traverse the list. This is a common technique used in Scala for such cases and the language enables programmers to define nested functions where parameters from the outer function are in scope within the inner function.  

### Problem\#10: Run-length encoding 
#### Problem: Run-length data count
Extending the code of problem\#8 we can very easily implement a simple data count commonly known as "run-length encoding". Consecutive duplicates of elements are aggregated and their count is registered into a list. Thus, all we need is to define a new external function that performs the operation and then passes it as a parameter to the reduceDup recursive function set above.

    def length(ls : List[Int]) : List[Int] ={
       List(ls.size)
    }
    
    scala> reduceDup(listdup,length)
    res: List[Int] = List(4, 2, 2, 1, 1, 3, 2)

This is quite simple and we can see here the beauty of functional programming where functions can be passed as parameters to other function.

#### Problem\#10: Run-length encoding - simple data compression 
Similar to the result of the previous problem, we can also try to implement Problem\#10 - the so-called run-length encoding data compression method. In this problem consecutive duplicates of data are encoded as a tuple (N,D) where N is the number of duplicates of the Data element D.

For example given our data list we need to generate the following list of tuples:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    res: List( (4,1), (2,2), (2,4), (1,5), (1,3), (3,4), (2,3) )
    
If we try to use the reduceDup function defined above with a new function which we call runLength we shall reach a dead end. For the reduce parameter (which takes a function in reduceDup) is a mapping from List[Int] into List[Int] while here we are taking a list of integers but generating a tuple in the form List[(Int,Int)]. To overcome this dead end we redefine our recursive function set using a parameterized type "T". The code to do so is as follows.      
    
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

The type "T" has been abstracted and assigned in the return types of both the outer and inner functions; thus, we gain greater flexibility in passing various kinds of external functions. For example, the runLength function below can be used to solve problem\#10 and return a tuple instead of an integer.

    def runLength(ls : List[Int]) : List[(Int, Int)] ={
       List((ls.size,ls.head))
    } 

    scala> processDup(listdup,runLength)
    res: List[(Int, Int)] = List((4,1), (2,2), (2,4), (1,5), (1,3), (3,4), (2,3))

#### Problem\#8: Revisited
We can use the parameterized abstract version as well to solve Problem\#8 without writing a single line of code. Simply calling "processDup" instread of "reduceDup" using the length function will do the job.

    def length(ls : List[Int]) : List[Int] ={
       List(ls.size)
    }
    
    scala> processDup(listdup,length)
    res: List[Int] = List(4, 2, 2, 1, 1, 3, 2)

### Problem\#9: Group consecutive duplicates of list into sublists
Now we can tackle the next problem where we group repeated elements into a separate sublists. Given our parameterized function "processDup" above, the solution for this problem is amazingly simple:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    def group(ls : List[Int]) : List[List[Int]] = {
       List(ls)
    }
    
    scala> processDup(listdup,group)
    res: List[List[Int]] = List(List(1, 1, 1, 1), List(2, 2), List(4, 4), List(5), List(3), List(4, 4, 4), List(3, 3))

### Problem\#11: Modified Run-length encoding - simple data compression 
In this problem we modify the result of problem\#10 such that if an element has no duplicates it is simply copied into the result list. Only elements with duplicates are transferred as an (N,D) tuple. For example given our data list we need to generate the following list of data/tuples:

    val listdup = List (1,1,1,1,2,2,4,4,5,3,4,4,4,3,3)
    
    res: List( (4,1), (2,2), (2,4), 5, 3, (3,4), (2,3) )

 The code snippet to perform the screening is as follows:

     scala> processdup(listdup, size).map{ case (len,e) => 
                                            { if (len==1) e else (len,e) }
                                          } 

Using the result of Problem\#10 we map each element in the list of tuples according to whether its "N" is equal or different from 1.

### Concluding Remarks
You can see from the few code snippets presented in this article that solving multiple problems in a unified way is not very difficult. With the right approach and using abstraction, recursion, parameterized types, and functional programming you can write very concise and efficient code in Scala. Please take your time to experiment with these samples as a first step to exploring the idiomatic approach to programming.

Last, as a bonus exercise you can modify the "processDup" function above to work with other types of data such as strings for example. By generalizing on the processed list "List[L]" you can create a more generalized recursive function that can be used without changing any of the presented external functions.   
