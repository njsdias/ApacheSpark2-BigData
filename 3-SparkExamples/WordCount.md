# Problem Definition

Here the files that we need to guide our work are:

- WordCount.scala

- WordCountBetter.scala

- WordCountBetterSorted.scala

## WordCount.scala
First we need to remember is that a each row of our RDD is equivalent to one line of our text file.

The **map()** transforms each element of an RDD into one new element. So, imagine we have the next sentence:

    The quick red fox jumped over lazy brown dogs
    
If we want transform all word in a Upper Case words we use **map** with the function **toUppercase**. 

    val lines    = sc.textFile("readfox.txt")
    val rageCaps = lines.map(x=>toUpperCase)
    
We can do the same with **flatMap**. The flatMap puts each word of the senetence in one row of our RDD. So, the flatMap instead of returning a single value in the function you pass into it will **return a list** of values (with zero or many elements).

Using the next line of code:

    val words = lines.flatMap(x=x.split(" "))   //splits word by space
    
we have:

    The
    quick
    red
    fox
    jumped 
    over 
    the 
    lazy 
    brown 
    dogs
    
If we are interest in know the occurences of each word we use **countByValue**

    val wordCounts = words.countbyValue()
    wordCounts.foreach(println)

The results comes up some problems when identify a word, as we can see with the outpu (touch,,1 -> expansion.,1)

    (touch,,1)
    (of.,3)
    (salesperson,5)
    (Leeches,1)
    (expansion.,1)
    (rate,7)
    (appropriate.,2)
    
 
## WordCountBetter.scala

For have better results we need to improve the previous code. We identified some problems:

-  word variants with capitalization, punctuation, etc.

We now there are fancy antural language processing toolkits like NTLK. But we will keep it simple, and use a regular expression.

In the next line of code split by word using the regular expression ("\\W+")

    val words = input.flatMap(x => x.split("\\W+"))

Normalize all words to lower case.

    val lowercaseWords = words.map(x => x.toLowerCase())
    
Count of the occurrences of each word

    val wordCounts = lowercaseWords.countByValue()
    
    
    


