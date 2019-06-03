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


