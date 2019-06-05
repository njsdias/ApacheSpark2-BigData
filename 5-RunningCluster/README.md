# Runnning Spark on a Cluster

All right this next section is going to be about running spark jobs on a real cluster.
And there are many ways of doing that.

We need a way of actually running these scripts from a command line and in a way that they can actually
be distributed using a cluster manager of some sort such as spark's built in cluster manager or hadoop's
yarn.

## Packaging and Deploying your Application

In order to prepare a script for being run with Spark-submit as opposed to within the IDE when you're
developing and debugging.

- Make sure there are no paths to your local filesystem used in your script! That's what HDFS, Anazon S3, etc are for

you have to make sure first of all that there aren't any hard coded paths in
that script to your local file system. And that's something we're kind of guilty of throughout this course. So far we've been putting in these relative paths to the Scala project that we're actually running from within the scala IDE.
But in the real world you want to make sure that your data is coming from some distributed reliable
file system that is accessible to every node on your cluster that might be an HDFS file system on a
hadoop cluster or Amazon S3 or something else but you want to make sure that your scripts are not assuming
that your data resides somewhere locally on your hard drive because that will not scale.

- Package up your Scala project into a JAR file (using Export in the IDE)

Then you just have to package up that script into a jar file somehow the scala IDE makes that easy because
we can just select the package and say export to jar and once we have that jar file we just need to
copy that wherever it needs to be.
We're gonna actually end up running it either locally at first to test it out or on your actual cluster
on your master node and use the spark-submit command to actually execute that driver script.

- You can now use spark-submit to execute your drive script outside of the IDE

      spark-submit --class <class object that constains your main functions> 
                   -- jars <paths to any dependencies>
                   -- files <files you want placed alongside your application>
                   <your JAR file>
                   
**Notes**

- What's special about spark-submit versus running in the IDE?

   - First of all spark-submit wraps the entire process of kicking off your script and maintaining it so
we can actually integrate with the cluster manager and figure out. 

   - The other good thing too is that by packaging things into a jar  file that's just Java bytecode we can run this script on any operating system that supports java. So we can develop on Windows like we're doing here, upload that jar file to a Linux cluster and
use spark-submit on that Linux cluster and it will work just fine.
   
   - Another thing that's good about spark-submit is that it's something you can run from a command line
   
- How do I distribute the script out to the entire cluster?

- How do they integrate with the cluster manager to actually make sure that my job is actually distributed
and reliable?

- How do I make sure my RDD's are really are indeed reliable and distributed?

So there is a **-- jars** option for example where you can specify a path to any jar files for other scala packages or Java packages that your code might depend on that isn't part of the core scala or SPARK packages themselves. 

So for example you might have an add on library for integrating Spark
with some other system like **Kafka** or you know **Flume** or something like that you might need a series of
jar files to actually enable that functionality in your script and you can use the dash dash jars
option to actually say okay here's where you can find that jar file.

You can also tell to automatically distribute a set of files alongside your script.
So if you use the **-- files** option, if you have some small data file that might be a reasonable way
to make sure that it's available to every node on your cluster.


So you can actually **kick off** this script **periodically** using a cron job on an actual Linux cluster or
you know **Nagios** whatever you use to actually schedule jobs in a real production system. So you can't really do that with an IDE right and a graphical user interface. You need to be able to actually execute these commands from a command line prompt from OS level.

## Packaging with SBT

Imagine that you have a very complex spark drivers script that actually has a lot of third party
dependencies that are part of the core spark distribution or maybe you have a bunch of add on packages
you need to manage well.

SBT is a technology you can use to actually package up all of those dependencies and do a self-contained
jar file that you can just move around to your cluster and run with one very simple line of code.
So if you're familiar with the Maven for Java you can think of its SBT as Maven for Scala.
It's basically a dependency management tool. For instance, if you get into more complex scripts where you have a bunch of third party dependencies and this is pretty easy to do because sometimes your dependencies have dependencies and whatnot.

For example if you're trying to use the **Cassandra database connector** from datastax with a scala script
and you need to integrate and output to a Cassandra database that's kind of pick up this whole tree
of dependencies that the scala the cassandra connector package depends on.
So, not only do you need to deal with having the Cassandra connecter library installed on your cluster,
**you also need to make sure that all the dependencies of that library and all their dependencies are
installed as well**. So it gets to become a very complex problem if you have enough of these things in one place.
**SBT manages that for you.**

So all it is is a very simple way of packaging up an application for Spark using Scala and automatically
pulling in all of your dependencies and making sure that all the dependencies for your dependencies
are in place as well and it puts all into one big nice neat jar package that you can then just copy
to your cluster and run.

The SBT can be get from **scala-sbt.org** .

So to use SBT it requires a very specific **directory structure** that you actually create your packages
from within. So you need to create a directory structure that looks like this.
You basically need a project folder and a source folder alongside each other and under your source folder
you will have a main folder and a scala folder and that's where your actual driver script source
code will go. So you have to set that up by hand.

      Project|
             | -> src -> main -> scala
             

But once you actually do that actually running SBT is quite simple.

- Put the Scala source files in the source folder

- In your project folder, create an assembly.sbt file that contains one line:
      
      addSbtPlugin("com.edd3si9n"% "sbt-assembly" % "0.14.3")
      
- Check the latest SBT documentation as this wil change over time. This works with sbt 0.13.11
The other thing you need to do is add this extra step here for a spark and scala you need an assembly.sbt

The next thing you need is an **SBT build file** and this is where you specify what to your script depends
on and SBT will then be responsible for getting that chain of dependencies and making sure of things
in place.

- At the root (slongside the src and project directories) create a **build.sbt** file

      name := "PopularMovies"
      version := "1.0"
      organization := "com.orgname"
      
      scalaVersion := "2.10.6"
      
      libraryDependencies ++= Seq(
      "org.apache.spark" %% "spark-core" %
      "1.6.1" % "provided"
      )
      
So we need to specify the name of our class that we're going to be running with.
Give it a version number and will call it 1.0, organization
you know from your packaging and the Scala version that your code depends on which in our case will
be 2.10.6 right now.

Then you need to specify any library dependencies that your script has.
And since this is a spark script we do depend on these SPARK core packages so this line says I need
the entire set of libraries for Spark core or else my script can't work. You also specify the version of Spark you're depending
on.

To explain the word **provided**.
So if I already know that these libraries are going to be pre-installed on my cluster I can say in this
build file they're already provided they're already there.
I don't need to actually package these up in my final jar file. 

And as it turns out we're going to be running this on an **elastic map reduce cluster using Amazon Web Services** and I know that comes with all the core SPARK libraries pre-installed so there's no need to actually package those as part of my
script. But you know if you did have dependencies that weren't part of a standard installation that's where
you put them.


So here's an example of putting in **Kafka dependency**. Kafka is basically a technology for streaming
large amounts of data from a distributed fleet.
Say log files from a giant web server or something. So, say for example you need to depend on kafka, which isn't built into Spark. You could add:

      "org.apache.spark" %% "spar-streaming-kafka" %% "1.6.1"

to your library dependencies, and sbt will automatically fetch it and everything it needs and bundle it into your JAR file.
**IMPORTANT:** Make sure you use the correct Spark version number, and note that we did NOT USE "provided" on the line, because it's not actually pre-installed.

So if I had to do stuff like that I could say OK from the org.apache.spark repository I need the
sparkstreaming kafka package that works with SPARK 1.6.1 and by adding that into my library
dependencies SBT would know that I need to go and retrieve that package make it part of my Jar file
and also retrieve any dependencies that that package may have which it turns out there are quite a few.

Once you have all those bits in place all you
do is go up to the top directory and **run SBT assembly** and it will auto magically go create your jar
file that contains any dependencies you might need within it and you'll find that output jar file
inside your target folder that gets created inside the scala the scala folder that's specific to the
Scala version that you're working with.
At this point you can **take that jar file and just run it.**

So what's really cool since you gave it the name of the class you actually care about is part of the
assembly.sbt, the build file. You can just say spark-submit. Type in the jar file and everything else just happens.

- You don't even need to specify the class name.

- You won't need any jar's dependency options because those are already contained in the jar file so just by saying spark-submit file name with your file you're done.

Just make sure your jar file is copied to where you're gonna run it from and you use spark-submit.
Kick it off and that's all you need to do.

To **Install SBT** go to the scala-sbt.org website and click download MSI and the installation is standard Windows Installer.

**Example with SBT build**

Import into your package the MovieSimilarities1M.scala file. As we have a new version of the data set extracted from grouplens.org/datasets/movielens/ (MovieLens 1M Dataset released 2/2003) we have modified our mappers that parse out the information to take into account the new file names and new delimiters that we have.
So we're looking for movies.dat now and a colon colon delimiter (::) here will be different.

     val lines = Source.fromFile("movies.dat").getLines()
     for (line <- lines) {
       var fields = line.split("::")
       if (fields.length > 1) {
        movieNames += (fields(0).toInt -> fields(1))
       }
     }

We also need to think about **where these files are going to be**, when we're actually running on a cluster.
So you'll see I'm actually saying I'm got to look this up for _movies.dat_ right alongside whatever
directory I'm running from.

So we need to make sure that movies.dat is distributed next to my Spark driver
script and I will look that up as part of the driver script and make sure that **it's in the directory
that I'm actually going to run from before I start.**

In this case, _movies.dat_ is small enough that I can get away with that but when we're dealing with a million
ratings you know we're getting into big data here. Maybe want to make sure that that's someplace distributed.

However, in case of _ratings.dat_ we are loading the file from S3.
So this _s3n_ prefix is instead of loading unloading from a local file system.
I'm gonna go to **Amazon's S3 service** which is a big distributed file store and look for the sundog-spark
bucket and load it in the data from there.

      val data = sc.textFile("s3n://sundog-spark/ml-1m/ratings.dat")

So this way I can make sure that the ratings data itself is accessible to every node on the cluster
because S3 is available to every node on my **Amazon Elastic MapReduce cluster**.
And it also ensures that that data is in a place that can handle the size of it and can store it redundantly.
So a little bit closer to a real world scenario here if you want to do a cluster that might be at HDFS
link instead of S3 and for example that would work just as well.


One other thing worth noting too is that **we're not actually specifying local star** or where we're creating
the configuration for this script so instead we want the pre existing configuration on the cluster to
take over. So when we set up an **Elastic Map Reduce cluster** it will come **pre-configured to take the best advantage
of the cluster** at hand, so it will know how many nodes it has available to it and how to split that up.

    val conf = new SparkConf()               // I haven't actually specified how this SPARK context is going to be run 
    conf.setAppName("MovieSimilarities1M")   // application name
    val sc = new SparkContext(conf)
    
Now **if I'd forgot to do that** and actually left that local bracket star bracket in the sparkconf, **it
wouldn't actually take advantage of the cluster** that would override any setting on the cluster itself.
**And actually just run on that one machine** so important remember that step.

So I'm going to create a spark conf folder on that conf object spark conf object rather set the app
name on it and then create my Spark context using that SPARK configuration object explicitly.
So, it is a little bit different way of setting up your SPARK context when you're running for real on a
cluster with that was packaged up using SBT.


For this example, extract the folder _sbt_ that is in the SparkScala folder in this repository. Copy this folder into a safe localization in your computer. Do not forget to change the configurations of the files in according with your spark and scala version. In my case I needed make some changes in the 

- build.sbt: From 2.0.0 to 2.3.0 after checked the spark version with the command _spark-submit --version_ in comand line terminal.

      libraryDependencies ++= Seq(
      "org.apache.spark" %% "spark-core" % "2.3.0" % "provided"
      
- assembly.sbt that is in _sbt/project_ folder. I changed fom 0.14.3 to 0.14.6

      addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")
      
 After this modifications run the command _sbt assembly_ inside of the folder _sbt_. If all is done well you will receive a Sucess Message. If you go to the _sbt_ folder you will find a new folder that was created automatically by the compile process named _target_ and inside of that you will see a folder with the name _scala-2.11_ (it is my dependy in my case) and inseide of that you wil find the jar file : _Moviesimilarities1M-assembly-1.0.jar_ . And that is my Spark drivers script that I can run from a real cluster.
 
![sbt-1](https://user-images.githubusercontent.com/37953610/58965982-5e21b180-87a9-11e9-9e2c-c49def6ce7a8.jpg)
 
![sbt-2](https://user-images.githubusercontent.com/37953610/58966167-b2c52c80-87a9-11e9-93ce-4a4178cef917.JPG)

![sbt-3](https://user-images.githubusercontent.com/37953610/58966538-60384000-87aa-11e9-84a0-3792017b286e.JPG)

 ## Introduction to Amazon Elastic MapReduce (EMR)
 
Here we're going to actually run our 1 million movie rating script on
a real cluster using Amazon's **Elastic Map Reduce** service and **Hadoop**.

About how **distributed SPARK** actually works so **the same scripts you've been using to run these SPARK
jobs locally on your own PC can be used on a cluster without much modification.**
So it's kind of up to spark-submit and SPARK itself to figure out what cluster manager you're running
on top of and that might be Spark's built in **cluster manager** could be Hadoop's **Yarn**, it could be **Mesos**
and integrate with that to actually distribute the work of all your mappers and reducers as well as
it can across the cluster that you have available to you. 

So basically the **spark driver** script is running on your master node your driver. And that communicates with your **cluster manager** to actually **distribute out the work** that's in that driver script **to different executor nodes, workers**. And the **cluster manager is then responsible for** dealing with failures of any individual nodes and getting
the results back together to get back to your driver script when it's done.

 ![spark-cluster-1](https://user-images.githubusercontent.com/37953610/58966826-e6ed1d00-87aa-11e9-84e2-8cd990b0d68e.JPG)

**Spark-submit Parameters** 

And first I should note that on a lot of clusters a lot of these settings are going **to be preconfigured
for you automatically.**
So if you don't specify anything in your script explicitly for, what the Master is going to be or if
it's not being specified on the command line there is also a configuration file within SPARK that can
be set up to set all of these things for you automatically.
And for example if you set up a cluster on Amazon Elastic MapReduce a lot of these things will be set
up for you in an optimal manner.

But sometimes you run into issues where things don't complete you run out of resources things, start
timing out and you need to tweak these things a little bit to get things to run more reliably so you
need to know they exist.

- --master

     - yarn : for running a YARN/HADOOP cluster
     
     - hostname:port : for conecting to a master on a Spark standalone cluster
     
     - mesos://masternode:port
     
     - If you have a sparkconf for anything in your script itself that overrides, that it will ignore what's on the command line. So the  hierarchy: 1- your script; 2-command line; 3-the configuration files for spark. So never forget to double check your scripts to make sure that you're not hard coding a given master. For example if you have that local bracket star that will override the master option here and if you were to run that script on a cluster they wouldn't take advantage of the full cluster.
     
- --num-executors

     - Must set explicitly with YARN, only 2 by default
     
- --executor-memory

    - You want to make sure that does not exceed the physical memory available to each individual executor node. If you're running on a cluster in the cloud those are often virtual machines that have less memory than
you might think. So make sure you are aware of the memory available to your script on each executor.
   
- --total-executors-cores

    - If you have multi cores on your virtual nodes then you might want to tweak that to actually put an upper limit on how many cores your script can consume.


**Amazon Elastic MapReduce**

In Amazon EMR it's easy to spin up the Hadoop cluster
and you can actually tell it to pre-install spark on it.
With everything automatically configured so very easy way to get started and run your script on a real
cluster where you just rent time and pay for what you need.


That's kind of the whole premise of Amazon Web Services, you just rent time and **pay** for the computing
resources that you actually need for whatever you're doing.
So you're charged basically by the hour
instances, how much time you're spending on how many computers of a given type.
And you're also charged for any network IO and any storage space and any storage IO as well so you **pay**
for what you use.

Do be careful to unless you got some corporate account or something
where it's not your money on the line because if you mess up it's very easy to forget to terminate your
cluster when you're done. And if you do that your cluster will just keep on running forever even though you're not using it and
you're going to be billed for all that time and you might not even realize it until you see a **credit
card charge for a thousand euros.** So you know if you want to fiddle around with EMR remember to **terminate your clusters when you're done.**

But again what do you and are sets up for you is a Hadoop cluster and you can **run Spark on top** of the
**Yarn component of Hadoop** so people kind of conflict,I do.

And Hadoop and sometimes I hear a lot of people talk about how SPARK is faster than Hadoop but it's
not really one or the other. What they really mean is **SPARK is faster than MapReduce** which is a way of running distributed jobs
on Hadoop, but **Hadoop itself is just a technology for managing a cluster** and one component of Hadoop is Yarn,
the cluster manager which SPARK can run on top of just fine. So, Hadoop and SPARK are not mutually exclusive which is a common misconception.

**Best pratices for running in clusters**
One other thing I want to point out too in terms of best practices, because **running on a real cluster
is expensive.**
These are expensive resources that you're dealing with here potentially.
You always want make sure you're **doing your development and testing locally on your own PC first**. or some desktop computer or some single computer that you have access to you that doesn't cost a lot of money. In a way to do that **often is to use a subset of your data just to develop with.**

So **if you're dealing with a big data set** that you can only manage on a cluster **consider using just a
piece of that data set** to develop and test with. And that way you're more likely to have a successful run when you're actually renting time on the cluster itself. You really want to **minimize the amount of time you're working on the cluster** if possible.

**Amazon Web Services account**

To set up you need to start out by:

- creating an Amazon Web Services account
      
- Create an EC2 key pair and download the .pem file
      
- On Windows, you'll need a terminal like Putty or MobaXterm

    - For Putty, need to convert the .pem to a .ppk private key file
      
There are many youtube videos on internet that we can watch and learn the details. Here the objective is to ask your attention to the details of running a spark file (jar) on clusters. But s usually I give you a step-by-step guide to follow: 
      
      https://andrewrgoss.com/projects/mov_sim_ml_1m_emr/


