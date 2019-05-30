# Learning the basics

Here we are working with LearningScala1.sc to LearningScala4.sc files that you can be founded in [ScalaSpark folder](https://github.com/njsdias/ApacheSpark2-BigData/tree/master/SparkScala).

For use these files create a new project scala and import the files as you did in the previous [example](https://github.com/njsdias/ApacheSpark2-BigData/tree/master/1-HistRealMoviesRating).

## Types of variables and prints: LearningScala1.sc

So, here is only to write important notes.

  - 1- The same code in scala-spark run most faster than in python-spark
  
  - 2- In Scala is high recommeded use IMMUTABLE variables (those which don't have values changed along the code)
  
    - IMMUTABLE -> val namevariable = x
    
    - MUTABLE -> var namevariable = x
    
   You can specify the type of the variable, but the scala is able to didentify it for you:
   
    var namevaribale: String = "Hello"

The answer for the challenge of LearningScala1.sc is:

    val mypi = pi                                   //> mypi  : Double = 3.14159265
    val dmypi = 2 * pi                              //> dmypi  : Double = 6.2831853
    println(f"Pi is about $dmypi%.3f")              //> Pi is about 6,283
  
## Flow Control: LearningScala2.sc


The answer for the challenge (Fibonacci Sequence using only Flow Control loops and ifs) of LearningScala2.sc is:

![fibonacci](https://user-images.githubusercontent.com/37953610/58652922-8b371580-830c-11e9-832a-0762786455a7.JPG)
