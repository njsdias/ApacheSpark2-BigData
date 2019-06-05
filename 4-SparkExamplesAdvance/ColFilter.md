# Movielens: Item-Based Collaborative Filtering

Introduce to: _cache()_, _persist()_

Item based collaborative is an algorithm that's used for recommending any kinds of items.
Basically the idea is we try to find relationships between items in this case between movies based on
customer behavior or user behavior.
So if we see two movies that users tend to rate together similarly then we might say there might be
some sort of connection between these two movies.
And if you like one movie maybe you're like these other movies that are connected to it through that
user behavior data.

So using that technique we can build up features like this one on the movie lens Web site where they
have their own recommender system and the same basic idea can be used to recommend anything on any Web
site you can think of.
It's used in e-commerce for example and a lot of it from places. 

So basically what we do is we start off by finding every pair of movies that were watched together by
the same person. So any movie that was watched with another movie by the same person goes into a big old bucket of movies
that are watched together by the same individuals.


And then what we do is you take all the users who watched both movies and we compare their ratings to
each other and using those rating vectors that are associated with each movie pair.
We can then try to compute how similar are these two movies to each other.


And that's just one way of doing it.
There's like countless ways of doing recommendations.
This is just one algorithm that works well in some situations.

In resume:

- find every pair of movies that were watched by the same person

- measure the similarity of theur ratings across all users who watached both

- sort by movie, then by similarity strength

To explain better how the Item-Based Collaborative Filtering can do, suposse you have three people: A, B, and C. The person A and B rated with 5 stars the movies X and Y and the person C rated with 5 stars only the movie Y. So, based in Item-Based Collaborative Filtering we can recommend the movie X to the person C that unknowns the existence of this movie. This recommendation is based in the fact the people A and B rated the movies X and Y in the same way and the probability the person C likes the movie X is high, because the movies X and Y are very well connected.

So, we need to build a strategy to implement the Item-Based Collaborative Filtering algorithm:

- Map input ratings to (userID, (moviesId, rating))

- Find every movie pair rated by the same user

  - This can be done with a "self-join" operation
  
  - At this point we have (userID, ((moviesId1, rating1), (moviesId2, rating2))
  
- Filter out duplicate pairs
  
- Make the movie pairs the key
     
  - map to (userID, ((moviesId1 ,moviesId2), (rating1, rating2))
  
- groupByKey() to get every rating pair found each movie pair

- Compute similarity between ratings for each movie in the pair

- Sort, save and display resutls


In this particular example we're going introduce a new concept called **caching or persisting** your RDD's.
And as we go through the code you'll see that we're going to produce an RDD of our final results and
query it more than once. Any time you do that **if you're performing more than one action on the same RDD**, you need to explicitly
tell SPARK to **cache that RDD** so it doesn't go off and try to **recreate it from scratch the second time
around.**

**Remember** if you create an RDD, run one action on it and then another action on that same RDD it might go off and
recreate it again. To prevent that we have this cache function or persist function that you can use to make sure that it keeps a copy of
that RDD around. So if you need it later on in the script again it can go back to that copy of it instead of recreating
it all from scratch.


What's the **difference** between **cache and persist**?

- Persist gives you the option to cache it to disk 

- cache gives you the option to cache just to memory.

So if you want to deal with if you're worried about **fault tolerance and nodes** you know failing on  and
be able to **recover from that as quickly as possible** _persist_ might be a better choice but obviously it
requires more resources to to get to that state. 

# Runing using Spark's Cluster Manager

- Right Click on package and choose: Export... -> Java -> Jar File. Choose the folder where you are saving the course and give the name MovieSims.jar

![cluster_run](https://user-images.githubusercontent.com/37953610/58922882-d732f180-8734-11e9-9f2a-01b952f8b42a.jpg)

- To run ising the Spark's Cluster Manager, open the command line (cmd) and go to inside of the folder that yu are saving the course and type:

      spark-submit --class com.orgname.spark.MovieSimilarities MovieSims.jar 50
      
![cluster_run2](https://user-images.githubusercontent.com/37953610/58923165-62f94d80-8736-11e9-8193-ba90e9744466.jpg)

