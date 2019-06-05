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
with some other system like Kafka or you know flume or something like that you might need a series of
jar files to actually enable that functionality in your script and you can use the dash dash jars
option to actually say okay here's where you can find that jar file.

You can also tell to automatically distribute a set of files alongside your script.
So if you use the **-- files** option, if you have some small data file that might be a reasonable way
to make sure that it's available to every node on your cluster.


So you can actually **kick off** this script **periodically** using a cron job on an actual Linux cluster or
you know **Nagios** whatever you use to actually schedule jobs in a real production system. So you can't really do that with an IDE right and a graphical user interface. You need to be able to actually execute these commands from a command line prompt from OS level.
