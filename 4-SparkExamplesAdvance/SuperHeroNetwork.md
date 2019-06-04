# SuperHero Network: MostPopularSuperhero.scala

Here we want to track the connections between super heroes analysing data in comic books. 

Someone actually went out and looked at every single Marvel comic book and kept track of all the superheroes that appear together within the same comic book. And what we're doing is we're treating those co appearances within comic books as social connections. So the fact that for example the Hulk and  Spider-Man appeared in the same comic might imply that they're friends if you will.

Suppose one example where the Hulk appeared with Iron Man and The Hulk appeared with Thor but Thor only appeared with Iron Man.
And you know how they were different comic books and maybe Spider-Man and The Hulk are connected by appearing in the same comic book, but let's pretend that Spider-Man and Thor never actually appeared in the same comic book totally made up social graph here. But it gives you a very simple example of what a social graph looks like and just like we can construct something like this in the context of superheroes who appeared with each other in the same comic books.

![superheroes](https://user-images.githubusercontent.com/37953610/58820476-9cdd2d80-862a-11e9-93cd-a1469d56d92f.jpg)


For this example we will work with two files that come with the state. So we're gonna work with what is called Marvel-graphs.txt, which contains the actual social graph itself in kind of a strange format. And then there's Marvel-names.txt, which just maps superhero IDs to their human readable names.

- Marvel.graphs.txt

      399 2548 3495 3556 4726 2664 403 400 4860 3994 3836 2650 3764 3765 3934 5467 4235 268 4232 2557 **5306** 2397 2144 6315 2399 4898 1127 5768 1929 5762 6313 5310 4318 6066 522 6306 1011 2669 2603 2449 5485 2040 3015 5194 5978 647 5232 3806 1587 64 5046 5131 2050 5709 5941 2561 5706 859 6206 508 4441 2213 5716 3373 3208 2354 154 5112 5294 4698 4511 1195 3974 2503 140 206 898 1886 2971 4398 4716 1289 4395 1365 4859 1965 
      
- Marvel-names.txt

      5300 "SPENCER, TRACY"
      5301 "SPERZEL, ANTON"
      5302 "SPETSBURO, GEN. YURI"
      5303 "SPHINX"
      5304 "SPHINX II"
      5305 "SPHINX III"
      5306 "SPIDER-MAN/PETER PAR"
      5307 "SPIDER-MAN III/MARTH"
      5308 "SPIDER-MAN CLONE/BEN"
      5309 "SPIDER-WOMAN/JESSICA"

The Marvel-graphs.txt every line is just this big stream of numbers. And the way to interpret it is the _first number_ represents a given superhero and all the subsequent numbers represents all of the superheroes that appeared with that superhero in other comic books.
We know that's the hero that we're talking about and it's followed by a list of all the heroes that appeared with that Hero.

And to map those hero IDs to names we can do that with the Marvel-names.txt. So, for example you can see that Spider-Man is 5306. And actually he appears with whoever 399 as in this example Spider-Man is pretty popular. But let's find out if he's actually the most popular.

In this first approach we need to see who is the most popular among the super heroes. For that we are define a strategy:

- Map input data (heroID, number of co-occurences) per line

- add up co.occurence by heroId using reduceByKey()

- Flip (map) RDD to (number, heroID) 

- Use max() on the RDD to find the hero with the most co-occurrences

- Look up the name of the winner and display the result

Our high level strategy will be to parsing that input line one line at a time. And since we don't really care about the actual individual connections for this problem we just want find out who's the most popular.

All we care about is the total number of connections for each superhero. So we're going to look at each line of input data extract the superhero ID that's the first number though that we're talking about. And then just store the count the total number of other superheroes that appear with that super hero.

Now remember these can span multiple lines so the same superhero idea might be broken up onto two or more of different lines so we need to combine them together somehow and **reduceByKey** will allow us to add up all the individual lines for a given superhero into one final result. From there we use the same sorting trick that we've used before. We're just going to flip that around so that the key is the count of how many friends you have and the value becomes the superhero ID.

And then we can just call Max on the resulting RDD to find who has the most friends and friends again it's a proxy for coappearances in other comic books in this example. Then we can look up the name of the winner from Marvel-names.txt and display the result.

If we click on the **marvel-graph.txt** and preview them you'll see that they are as I advertised Marvel graph again just
a list of lines that have a bunch of numbers on them where each number represents a superhero ID.
The first number is the hero we're talking about followed by a list of that hero's connections. We have **marvel-names.txt** which is mapping super hero IDs to their human readable names, where the names are enclosed in quotation marks.


**Build up an RDD** that can map superhero IDs to superhero names. We can also build up an RDD and that will be automatically be available to every node near the cluster as well.

So to do this we're going to call **flatMap** on the parseNames with the parseNames function that we defined. Remember in the marvel-names.txt each row is a number and a space and then within quotation marks the name of that character. But there is some invalidated data in here somewhere maybe there are some empty names maybe there is some blank lines. So we need to deal with all these different edge cases as well. And that's where a flat map comes in. So if there's a possibility of a line that we cannot successfully parse we only use something like a flat map so we have the option of returning nothing for a given line because it's not actually going to end up in our RDD. **So that's where we're calling flat map instead of map.**

    // Build up a hero ID -> name RDD
    val names = sc.textFile("../marvel-names.txt")
    val namesRdd = names.flatMap(parseNames)

**parseNames function**

    // Function to extract hero ID -> hero name tuples (or None in case of failure)
      def parseNames(line: String) : Option[(Int, String)] = {
        var fields = line.split('\"')
        if (fields.length > 1) {
           return Some(fields(0).trim().toInt, fields(1))
        } else {
          return None // flatmap will just discard None results, and extract data from Some results.
      }
    }

The input is a string of the Marvel names that textfile and the output is going to be an **option a scala, option of a tuple** of a superhero ID and the superhero name. Basically an option is a scala construct for saying you could have data where you might not have data in other languages. We have the concept of a null value or a nil value or something of that nature scala doesn't have that. Instead it has the concept of an option that wraps basically a value and you can have either an actual value returned as an option and that is a sum value which is a subclass of option or you can return a non-value which is also a subclass of option.
So by returning an option you can return a sum which actually contains data or none which contains no data.

And when you're using **flatMap** what will happen is that if you return a sum object it will say OK. We are gonna actually construct a line in the new RDD based on what's inside that some object but if it returns _None_ it will say _OK_ there's nothing actually to do here. I'm not going to create a new line at all in my resulting RDD.

In resume: With this Option we can guarantee that each line of an RDD has data with the format that we defined in the function parseNames.

This is the result from flatMap with parseNames function:

      (18558,MARVEL MYSTERY COMIC)
      (18559,HUMAN TORCH 4)
      
**For counting the superheroes** that are in a just line we defined a countCoOccurrences function. It is split the elements of each line with white spaces. The regular expression "w+" is for one white space. But the "s+" is a regular expression that means split
it up based on whitespace. Whenever white spaces might be multiple spaces and might be tabs that might be tabs and spaces. We don't care just split up on whitespace please. The output of this function is the first number (superhero ID) and the amount of element that the line contains minus one. Because the first one id the superhero ID.

    // Function to extract the hero ID and number of connections from each line
    def countCoOccurences(line: String) = {
      var elements = line.split("\\s+")
      ( elements(0).toInt, elements.length - 1 )
    }
    
Here we read the marvel-hraph.txt and map each line to extract the information that was defined on _countCoOccurences_ function.

    // Load up the superhero co-apperarance data
    val lines = sc.textFile("../marvel-graph.txt")
    
    // Convert to (heroID, number of connections) RDD
    val pairings = lines.map(countCoOccurences)

Next we use _reduceByKey_ to have (heroId, connections)

    // Combine entries that span more than one line
    val totalFriendsByCharacter = pairings.reduceByKey( (x,y) => x + y )

And here we flip the information (connections, heroID)

    // Flip it to # of connections, heroID
    val flipped = totalFriendsByCharacter.map( x => (x._2, x._1) )

Here we find the hero with most of connections:

     // Find the max # of connections
    val mostPopular = flipped.max()

The result is:

      (1933,859)   //connections, heroID

And here we extract the name of this superheroe using **lookup** between _namesRdd_ and _mostPopular_.

    // Look up the name (lookup returns an array of results, so we need to access the first result with (0)).
    val mostPopularName = namesRdd.lookup(mostPopular._2)(0)
    
    // Print out our answer!
    println(s"$mostPopularName is the most popular superhero with ${mostPopular._1} co-appearances.")
     
The result is:
    
    CAPTAIN AMERICA is the most popular superhero with 1933 co-appearances.
    
    
As exercise is purposed print out the Top 10 super heroes:

     //Top 10
    val sortedheroes = flipped.sortByKey(false)               //connection , heroID, descending order
    val herotop = sortedheroes.take(10)                       // taking the 10 first
    val mostTenPopularConID = herotop.map( x => (x._1,x._2))  //connection , heroID
    
    println()    
    println("Top 10")
    //print out
    for ( x <- mostTenPopularConID) {
      val mostPopularName2 = namesRdd.lookup(x._2)(0)
      val connect = x._1
      println(s"$mostPopularName2 has $connect co-appearances.") 
    }
  
 The out put that I had was:
 
 ![top10heroes](https://user-images.githubusercontent.com/37953610/58900232-af249d80-86f6-11e9-8d31-9b0437e42645.JPG)

# SupeHero Network: Breadth First Search algorithm


The main objective is find the degrees of separation between any two superheroes.
And to do that we're going to introduce a concept called **breadth first search** which is a _computer science algorithm_ and illustrate how you can use Apache Spark to implement what might not seem at first to be something that lends itself to distributed processing but through some creative thinking you can take even complex algorithms like this and make a spark application out of them.


So maybe you've heard the story that the actor Kevin Bacon is six degrees away from any other actor in Hollywood.
That is if you look at the people that Kevin Bacon has appeared with in other films and the people that those people appeared with in other films and so on and so forth. Everyone's within six degrees of Kevin Bacon.

The same is true of the superheroes in our superhero social network.
You might be surprised at just how closely everyone is connected to say Superman and to get a little feeling of what I mean by Degrees of Separation.

So in this example the Hulk and Spider-Man are one degrees of separation apart from each other because they have a direct connection.
But for example Iron Man is **two degrees of separation** from Spider-Man because we have to go through the Hulk to find Iron Man.


So for example to make it more concrete even the Hulk in Spider-Man may have appeared in the same comic book. But let's say that Spider-Man and Iron Man never did, but Iron Man and The Hulk did.Therefore they're two degrees of separation apart. 

Take Thor for example. If you take this path we might say that he has three degrees of separation but we always talk about the shortest path So and in the case of Thor he would be two degrees of separation because they are connected by one person the Hulk. OK two steps to get to Thor.


So how do we do that. Well we need to use a _search algorithm_ called **breadth first search**.

So what we have here is basically a network graph in computer science terms. So, folowing the next figure, imagine every one of these circles represents a super hero in our social graph and these lines represent the connections between them, you know the people that appear together in the same comic books and this example. 

![bfs-algorithm](https://user-images.githubusercontent.com/37953610/58894323-aed1d580-86e9-11e9-903c-8c947bf5d447.jpg)


And our social super hero network  illustrates different superheroes as circles. The lines represent connections between the superheroes and the number in the middle in this case infinity represents the distance from any given superhero that we start with.

So let's say for example we want to start with superhero s. Maybe that represents Spider-Man. Well we want to end up with is a graph that indicates how many degrees separated from Spider-Man from node s as is every other node in this graph.

So to do that we need some sort of approach some strategy. So lets start off with a couple of basic ideas here.
First of all we are going to  come up with the idea of maintaining the state of a given node and it can be one
of three colors. Now White means that a node is completely unexplored by our algorithm. So we're starting off with everything being white as our initial state because nothing has been explored yet. Now we have other colors grey meaning that it needs to be explored and black meaning that it has been fully explored.

So, the color represents the state of each superhero as we search through this graph. White means it hasn't been touched at all.
Grey means it needs to be touched. And black means we're done with it. And inside each one we're going to keep track of the degrees of separation from some given character that we started with.

So let's see how that works. So again our initial state we're going gonna start off saying we want to measure everyone's degrees
of separation to this node. Notice maybe it's Spider-Man. So to do that we're going to color this first node grey meaning that it needs to be explored. And the initial degrees of separation will be zero because Spider-Man is 0 degrees away from Spider-Man because Spider-Man is Spider-Man. It makes sense so far. So what that gray indicates is that we need to explore all the connections of this node.

![bfs-action1](https://user-images.githubusercontent.com/37953610/58895119-25230780-86eb-11e9-8633-da640f02b154.JPG)


So we'll do that next. So by reaching out on the tendrils of that great node we're going to color these new connections grey me they need to connect to explore them as well and in the process I'm going to increment the degree count from 0 to 1 and store that in this new exploration here. So we've increased 0 to 1 and we've propagated that out to all the connections of the original node. Now we've colored that original node black meaning that we're done with it now we've already explored his connections. And now these two nodes are gray, meaning that they need to be explored further.

![bfs-action2](https://user-images.githubusercontent.com/37953610/58895444-cf9b2a80-86eb-11e9-986f-dacc59d9989a.jpg)

Now if we had a situation where there was already a number on one of these connections we would maintain the lowest connection count.
Kind of like we showed before with the Hulk and or Spider-Man and Thor for example being connected through the Hulk.
So we always maintain the shortest distance that we find and the darkest color. 

So we then go out to split out this. No that was gray. We are now going from one to two in our depth and we will go out to its connections make them two because we're including that and color them gray. When you do the same for this left connection and that gets explored out and it goes there and we're done with these two nodes they get colored black so we know we're done with them. 

So that's another iteration of the BFS algorithm.

![bfs-action3](https://user-images.githubusercontent.com/37953610/58895712-754e9980-86ec-11e9-8171-92ef3a409c0e.JPG)

So that's another iteration of the BFS algorithm. Basically we keep iterating through this process of exploring connections, coloring them gray incrementing the degree of separation count and then marking the node black that we actually processed.
We just do this over and over and over again until we're done. 


Now the nodes at right need to be explored all three of these nodes.
So we'll see how that works here.
These get branched out and we propagate out the next level of 3 out to there.

![bfs-action4](https://user-images.githubusercontent.com/37953610/58895974-03c31b00-86ed-11e9-9fbc-149db84f0cf8.JPG)

The node at left bottom doesn't have any connections so he's just going to turn black.
We're done with that branch.
And finally we'll explore any and explored connections of the other nodes.
There aren't any so they end up going black as well and that is our final graph for degrees of separation
from node s.

![bfs-action5](https://user-images.githubusercontent.com/37953610/58896095-3240f600-86ed-11e9-8b4b-1c7e66ac24e9.JPG)


So the way to interpret this look if you look at any given node.
It already has the answer in there of how many degrees of separation am I from S ,and you can see we are one degree from between S and w. but from t to W. for example is 2.

And again we preserve the shortest value there. You know you could actually do it through three hops if you really want to but two is the shortest path. And that's what breadth of research does that's how it works.

# Breadth First Search algorithm: Implementation : DregreesofSeparation.scala

We need to transform our dataset to adquate it to BFS algorithm using tuples into tuples (nested tuples).  For exmample, one line of Marvel-graph.txt is transformed from:

      5983 1165 3836 4362 1282
     
 to
 
      (5983, (1165, 3836, 4362, 1282), 9999, WHITE)
 

So in this output tuple that we're going to put into our RDD for this data, the first entry in the list
will be the superhero ID that we're talking about followed by another table that indicates the connections
to that superhero.

This next entry will indicate the degrees of separation from the person we care about.
And initially we are going to set that to 9999, just to represent infinity.
Ok really it should be infinity but infinity isn't really a number.
So we'll use 9999 as a proxy.
And finally the color of the node, which initially will be white meaning that it is unexplored entirely

Here's the code that will do that.

    def convertToBFS(line: String): BFSNode = {
    
       // Split up the line into fields
       val fields = line.split("\\s+")
    
       // Extract this hero ID from the first field
       val heroID = fields(0).toInt
    
       // Extract subsequent hero ID's into the connections array
       var connections: ArrayBuffer[Int] = ArrayBuffer()
       for ( connection <- 1 to (fields.length - 1)) {
         connections += fields(connection).toInt  // a mutable object here because I need to keep adding stuff to it and
                                                  // changing it as I go.
       }
    
       // Default distance and color is 9999 and white
       var color:String = "WHITE"
       var distance:Int = 9999
    
       // Unless this is the character we're starting from
       if (heroID == startCharacterID) {
         color = "GRAY"
         distance = 0
       }
       
       /* I want using the two array object on my
            array buffer to actually convert that
            mutable array buffer to an immutable array */
            
       return (heroID, (connections.toArray, distance, color))
    }

Now we need iterate and update the information in each iteration.
First we need to iterate to leave process the RDD just like was
explained before where we go through one step at a time branching out on all the gray nodes coloring
those nodes black and then updating the distance on those gray nodes accordingly.
So we just go through looking for grey nodes to expand ,when we're done we color that black and we
keep updating the distance as we expand our way out through the social graph.
So we do this is by using a **mapper** and a **reducer** a map function and a reduce function in SPARK.

**The mapper is responsible for:**

- Creating new nodes for each connection of a given grey nodes, with a distance incrementede by one, color grey and no connections

- Colors the gray node we jsut processed balack

- Copies the nodes itself into the results


**The reducer is responsible for**

- Combines together all nodes for the same heroID

- Preserves the shortest distance, and the darkes colour found

- Preserves the list of connections from the original node.



Any individual executor on our cluster might run across the character that we're looking for.
So to do that we're going to use something called an **accumulator**. An accumulator is basically a shared
object across the entire spark cluster that maintains a count of something or some sort of a state.
And it just allows all your executor's to increment a shared variable across a whole clustered in a
safe thread safe way. Syntactically we can set something up like this:

      val hitCounter:LongAccumulator("Hit Counter")

which set up a shared accumulator named "Hit Counter" with and initial value of 0.

So what we're going to do each iteration if we run across a character that we're trying to find, will
increment that hit counter accumulator. So all the executors will say okay increment that value from 0 to something else.

If I run across this person and then the drivers can check this accumulator value through each iteration
and say OK well if that hit counter accumulator is greater than zero then I know I found the person.
And in fact that hit accumulator will actually contain the number of times that superhero was encountered.
It might be more than one.
It might have been reach from several different directions potentially. So that's how we're going to
message back to the driver script that we actually found the person we're after using an accumulator.



