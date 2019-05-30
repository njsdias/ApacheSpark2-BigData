# Setup Environment

First of all is need to download and install the appropriate tools. Besides of that we need to guarantee the right installation.
This chapter has the intention to be a guidance for this purpose.

The all instructions are detailed in the link below:
https://sundog-education.com/spark-scala/

Here the steps are more detailed and can be used with the link above. These instructions asre appropriated for Windows, bu in the link tou can find instructions for MacOS and Linux.

**1- Install Java 8:** Spark is not compatible with Java 9 or greater, so don't install Java 9 10 or 11.

  Download it for your Operating System
  
   https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
  
   ![jdk-site](https://user-images.githubusercontent.com/37953610/58574743-8c970e00-8238-11e9-9bfa-826e919a2850.JPG)

**NOTE** Do not forget the path where the Java will be installed (C:\Programs Files\Java\jdk1.8.0_101\) because we need it for setup de environment variables.

**2- Download Spark**

For handling with tgz file download and install [WinRAR](www.rarlab.com/download.html) 

Download Spark from:

   https://spark.apache.org/downloads.html
    
   ![spark-download](https://user-images.githubusercontent.com/37953610/58575184-7a699f80-8239-11e9-9965-799aecc4c523.JPG)
    
 - 1- Unzip the folder that you downloaded. 

 - 2- To copy the content of this folder

 - 3- To create a new folder named spark in C:\ (i.e. C:\spark)

 - 4-  Paste the content into C:\spark

 - 5- Go to C:\spark\conf and change the name of the log4j.properties.templ file to log4j.properties (delete the extension of the file)

 - 6- Open the file log4j.properties with an editor and find the line where is:

    log4j.rootCategory=INFO, console
    
change it for     

    log4j.rootCategory=ERROR, console

**3- Simulate the Hadoop in your computer**

 - 1- Download the file

  https://sundog-spark.s3.amazonaws.com/winutils.exe
  
 - 2- Create a folder C:\winutils and a subfolder C:\winutils\bin\

 - 3- Copy the winutils.exe to the folder C:\winutils\bin\

**4- Set the environment variables**

 - 1- In Windows go to Painel Control -> choose System -> Advanced -> Environment Variables... 

 - 2- Create a new User Variable for user clicking in _New..._

   - 2.1- Variable Name: SPARK_HOME  --------- Variable value: c:\spark
  
   - 2.2- Variable Name: JAVA_HOME  --------- Variable value: c:\Programs Files\Java\jdk1.8.0_101
  
   - 2.3- Variable Name: HADOOP_HOME  --------- Variable value: c:\winutils

3- In the User Variable window find a variable named as PATH. Open it and click on New.
  
   - 3.1 %SPARK_HOME%\bin
  
   - 3.2 %JAVA_HOME%\bin
  
 **5- Install the Scala IDE**
 
  - 1- In your web browser write: scala-ide.org
  
  - 2- Click in the green bottom where says: Download IDE and choose your Operating System (i.e. Windows 64 bit)
  
  - 3- Extract the file that you downloaded and copy the content for the new folder c:\eclipse
  
  - 4- Create a shortcut of exclipse.exe that uyou find inside of the folder c:\eclipse
  
 **6- Test your environment**
 
  - 1- Open the windows terminal. Click in the windows logo and type _cmd_. Choose _Command Prompt_ and execute it as Administrator.
  
  - 2- Type: cd c:\spark and type spark-shell. You will see the scala environment
  
  ![spark-scala](https://user-images.githubusercontent.com/37953610/58599122-bec76080-8276-11e9-8335-6c4c2fa2f7f0.JPG)

 
  - 3: rdd: resiliente distributed dataset
  
    Create a variable that build a small database where each row is a line that belongs to the file README.md
  
        val rdd=sc.textFile("README.md")
     
     Check how many lines have the file or how many rows have the table
      
        rdd.count()

     To quit press the _ctrl + d_
     
     ![spark-scala2](https://user-images.githubusercontent.com/37953610/58599657-27174180-8279-11e9-86cd-422859e51385.JPG)
