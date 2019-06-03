# SuperHero Network

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




So pretty simple straightforward problem here. Our high level strategy will be to parsing that input line one line at a time.


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

So what we have here is basically a network graph in computer science terms. So imagine every one of these circles represents a super hero in our social graph and these lines represent the connections between them, you know the people that appear together in the same comic books and this example. -> 00:02:14.250 
