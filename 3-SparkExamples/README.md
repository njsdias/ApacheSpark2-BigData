# Spark with examples

Some notes:

  - Spark - a fast and general engine for large-scale data processing
  
  - Spark is scalable: Runs in your laptop as well in a distributed cluster for parallel processing
  
  - Spark is 100x faster than Hadoop MaprReduce in memory or 10x faster on disk due to DAG (Directed Acyclic Graph) that optimizes workflows. Nothing is executed before you give a command.
  
  - RDD (Resilient Distributed Dataset): It is important when you are working with streaming data
  
  - Spark Core:  Spark Streaming + Sparl SQL + MLLib + GraphX
  
  - Spark itself is written in Scala
  
  - Functional Programming Model is a good fit for distributted processing
  
  - Python is slow in comparison
  
Simple code comparison between Python and Scala

 -  Python
    
        nums    = sc.parallelize([1,2,3,4])
        squared = nums.map(lambda x: x*x).collect()
        
 - Scala
 
        val nums    = sc.parallelize(List(1,2,3,4))
        val squared = nums.map(x => x * x).collect()

 
Scala looks to your code and makes an _execution plan_ and slipt it in _stages_ and each stage is splited in _tasks_. Each stage is parallelized and so, each task inside in the stage is parallelize. Finally the tasks are scheduled across your cluster and executed.
 
 ![stages_tasks](https://user-images.githubusercontent.com/37953610/58712347-51234d80-83b8-11e9-93f8-911125ebe195.jpg)
 
  ## Introduction RDD's
  
It is a encapsulation around a very large dataset that you can then apply trnasformations and actions(returns a value). 

You look the RDD abstract away all the complexity that Spark does for you of making sute that its fault tolerant, resilent making sure that if one goes down your cluster it can still recover from that and pick up from where it left off.

Fundamentally it is a data set. an RDD is just a set of data basicaaly row after row after row of information and that can just be lines of raw of text can be a value information. 
  
**Transformations**: It creates a new RDD.

  - map
  
  - flatmap
  
  - filter
  
  - distinct
  
  - sample
  
  - union, intersection, subtract, cartesian
  
 Example:
 
    val rdd     = sc.parallelize(List(1,2,3,4))   // this RDD contains only four rows
    val squares = rdd.map(x => x *x) 
  
  this yields: 1,2,9,16. 
  
 For more complicate transformations is recommended use Functional Programming
  
      def squareIt(x:Int):Int = {
        return x*x
      }
      rdd.map(squareIt)
  
  **Actions**: It returns a value.
  
   - collect
   
   - count
   
   - countByValue
   
   - take
   
   - top
   
   - reduce
   
   - ... and more...
  
  ## Key/Values RDD's
  
For exmple let's consider a case where we need to figure out the average number of friends broken down by age. So let's image the we have a dataset that's for each person has their age and how many friends they have. Now it we want to actually figure out the average number of friends broken doen by age we need somehow aggregate all of the friend counts for every given age. So in this case the key is the thing that we wnat to aggregate on the age and the values will be the thins that we are aggregating the Friend counts. So we want to aggregate together all these friends counts broken down by age and end up with what's the average number of friends a 20 years old has for the key 20. 

If a tupple have two values Sclaa treats it automatically as a key->Value pair. 
    
      totalsByAge = rdd.map(x => (x,1))
      
- _reduceByKey()_ : to combine values with the same key using the some function

      rdd.reduceByKey( (x,y) => x + y)      //adds them up

- _groupByKey()_ : group values with the same key 

- _sortByKey()_ : sort RDD by key values

- _keys(), values()_ : create an RDD of just the keys, or just the values

## SQL-Style Joins
  
join, RightOuterJoin, LeftOuterJoin, cogroup, subtractByKey


## Problem Definition

Supose you have the data that are in the picture: id, name, age, numFriends.  In this example you see that you have two persons with the same age. The objective is sum up the number of friends of people have the same age and take the mean value: (385+2)/2

![ages_prob](https://user-images.githubusercontent.com/37953610/58716834-ce06f500-83c1-11e9-907a-d7a24ac42309.jpg)

First we build a function that allows us to take the age and the numbFriends from each row from a file that have data separated by comma. After that we read the content of the file that have all information. Next we build a RDD which stores only the age and the number of friends using the function that we construted for this purpose.

![code_ages](https://user-images.githubusercontent.com/37953610/58717420-1a066980-83c3-11e9-9de9-cca199c65af8.JPG)

Now we need write some expression that give us the total of friends that belongs to the persons that have the same age. The next figure tries to explain the main expressoins in two steps:

  - First: Build a tuple with the (age,(numFriend,1))
  
  - Second: Some the numFriend and the people (33,(387,2))
  
  - Third: Divide 387/2
  
        val averageByAge = totalsByAge.mapValues(x => x._1/ x._2)

This results in (33,(387,2)) => (33, 193.5)

  - Last print the results:
  
        val results = averageByAge.collect()
        results.sorted.foreach(println)

![code_ages2](https://user-images.githubusercontent.com/37953610/58717996-67370b00-83c4-11e9-940d-0cb24297d379.JPG)  





  
  
  
