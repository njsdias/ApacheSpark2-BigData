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
 
 First of all spark-submit wraps the entire process of kicking off your script and maintaining it so
we can actually integrate with the cluster manager and figure out, OK how do I distribute the script
out to the entire cluster.

- How do they integrate with the cluster manager to actually make sure that my job is actually distributed
and reliable?

- How do I make sure my RDD's are really are indeed reliable and distributed?

So there is a -- jar's option for example where you can specify a path to any jar files for other scala packages or Java packages that your code might depend on that isn't part of the core scala or SPARK packages themselves. 

So for example you might have an add on library for integrating Spark
with some other system like Kafka or you know flume or something like that you might need a series of
jar files to actually enable that functionality in your script and you can use the dash dash jars
option to actually say okay here's where you can find that jar file.
