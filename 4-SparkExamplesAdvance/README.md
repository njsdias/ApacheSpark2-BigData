# Spark with Advance Examples

Here we will see more advance functionalities of Scala with some advanced examples.

## Movielens: PopularMovies.scala 

Return to the dataset of Movielens. The dataset have the next structure:

- userID

- movieId

- ratting

- timestamp

How many times that individual movie was rated. So, we don't care about usedID, the timestamp and even the ratings themselves. we only want is how many times an individual movies appears in the dataset. 

- Extract movieID and convert it to integer: key value pair (movieId, 1)

      val movies = lines.map(x => (x.split("\t")(1).toInt, 1))

- Count how many times a movie appears   

      val movieCounts = movies.reduceByKey( (x, y) => x + y )
      
  output (moviedId,totalcount):
  
      (451,170)
      (454,16)
      (147,185)
      (1084,21)
   
- Sort by count:

      val flipped = movieCounts.map( x => (x._2, x._1) )
      val sortedMovies = flipped.sortByKey()

## MovieLens Improved: PopularMoviesNicer.scala

The last information is not very usefull to us because don't tell us much than the total counts and the movieId. Waht we want is a table with all finormation of the movie and the counts. Imagine that you have a big data information and if you do not use an RDD you will have  problems with memory. An RDD solve the problem because can distribute the information around the clusters. Addictional we want to make usre that it's not transferred across the network more than once so this where **broadcasting variables** come in.

Broadcasting variables gives us the means of taking a chunk of data and explicitly sending it to all the nodes in our cluster so that irs here and ready whener it needs it. So by using this pattern:

- sc.broadcast() : to ship off whatever you want

- .value() : to get the object back

you can send arbitrary data across your entire cluster and make sure that it's available whenever it's needed without having to retransmitted all the time.


We're going write a function (_def loadMovieNames()_) to build up a map object in Scala that maps movie IDs to movie names. For that we build a function that populates a empty map with movies names.

      var movieNames:Map[Int, String] = Map()  //initialize with a empty map
      
So we take the first field which represents the movie Id and we map that to the second field which is the movie name

      movieNames += (fields(0).toInt -> fields(1))
      
Now in the main function we create the broadcast variable so that map of ID's to movie names can be available to our entire cluster all at once so we call as our broadcast with whatever the _loadMovieNames_ function returns, which is that map of integer movie Id to string names.

Once we have the map of all movies and remember we want only pass it once, we can proceed our code as usually until the point we need the pass the information of the movie and map it with the counts. 

      val sortedMoviesWithNames = sortedMovies.map( x  => (nameDict.value(x._2), x._1) )
      
In the nameDict we have (movieID, name) and the sortedMovies we have (count,movieID). So, in the above line of code we use the name of the movie that is in the dictionary _nameDict.value(x._2)_ and the hw many times each movie was rated _sortedMovies(x._1)_.

So remember _nameDict_ was our broadcast variable object, we're calling dot _value_ to actually retrieve what's inside of that broadcast variable which is the map that we stuffed into it originally. So we're gonna call it _nameDict.value_ to retrieve that dictionary ,lookup that movie ID which is. In this case the second field of the sortedMovies RDD and then we will print alongside that the count and then pronounce the results. So this should give us back a new set of results that consists of the string based movie name followed, by the movie count.


      (Liar Liar (1997),485)
      (Return of the Jedi (1983),507)
      (Fargo (1996),508)
      (Contact (1997),509)
      (Star Wars (1977),583)

So, we have an example of using broadcast variables to make sure that information is available to an entire cluster when needed. You know this is really only necessary when you need to have that data broadcast to every node in your cluster.

So if you're performing some sort of distributed map operation or flat map operation that requires that data you want to think about using a broadcast variable instead.
