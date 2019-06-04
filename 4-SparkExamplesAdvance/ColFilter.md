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
  
- Make the ovie pairs the key
     
  - map to (userID, ((moviesId1, rating1), (moviesId2, rating2))
  
- groupByKey() to get every rating pair found each movie pair

- Compute similarity between ratings for each movie in the pair

- Sort, save and display resutls



