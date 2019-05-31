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
  
  

  
  
  
