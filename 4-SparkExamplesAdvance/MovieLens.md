# Problem Definition

Supose you have the data that are in the picture: id, name, age, numFriends.  In this example you see that you have two persons with the same age. The objective is sum up the number of friends of people have the same age and take the mean value: (385+2)/2

![ages_prob](https://user-images.githubusercontent.com/37953610/58716834-ce06f500-83c1-11e9-907a-d7a24ac42309.jpg)

First we build a function that allows us to take the age and the numbFriends from each row from a file that have data separated by comma. After that we read the content of the file that have all information. Next we build a RDD which stores only the age and the number of friends using the function that we construted for this purpose.

![code_ages](https://user-images.githubusercontent.com/37953610/58717420-1a066980-83c3-11e9-9de9-cca199c65af8.JPG)

Now we need write some expression that give us the total of friends that belongs to the persons that have the same age. The next figure tries to explain the main expressions in two steps:

  - First: Build a tuple with the (age,(numFriend,1))
  
  - Second: Some the numFriend and the people (33,(387,2))
  
  - Third: Divide 387/2
  
        val averageByAge = totalsByAge.mapValues(x => x._1/ x._2)

This results in (33,(387,2)) => (33, 193.5)

  - Last print the results:
  
        val results = averageByAge.collect()
        results.sorted.foreach(println)

![code_ages2](https://user-images.githubusercontent.com/37953610/58717996-67370b00-83c4-11e9-940d-0cb24297d379.JPG)  


**Run the project**

  - 1: Copy the file fakefriends.csv to the project folder (C:\SparkScala)
  
  - 2: Open the Eclipse. Select the project. Right click above the package. Select Import... -> File Systems -> Select the folder C:\SparkScala (where you have the files for the course) -> Select FriendsByAge

  - 3: Go to main menu RUN -> Run Configuration ... -> Click twice above _Scala Application_ -> Name: FriendsByAge -> Main Classe: com.orgname.spark.FriendsByAge -> Click in Run button

