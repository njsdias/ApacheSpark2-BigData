# Spark with examples

Some notes:

  - Spark - a fast and general engine for large-scale data processing
  
  - Spark is scalable: Runs in your laptop as well in a distributed cluster for parallel processing
  
  - Spark is 100x faster than Hadoop MaprReduce in memory or 10x faster on disk due to DAG (Directed Acyclic Graph) that optimizes workflows. Nothing is executed before you give the command.
  
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

  
  
  
  
  
  
