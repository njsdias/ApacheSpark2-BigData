## Histogram Real Movie Ratings

  - 1- Get Data from grouplens.org/datasets/movilens. Go to datasets and click on ml-100k-zip (search on site for it)
  
  - 2- Download and decompress the file and copy to your SparkScala project folder
  
  - 3  Download the Scala Code folder that you find in the main folder of this repository. Save it in a save place.   
  
  - 4- Open the Scala IDE Eclipse that you downloaded when you was setup the environment
  
  - 5- Choose a folder for your Workspace:  c:\SparkScala 
  
  - 6- Create a scala project: File -> New Scala Project 
  
  - 7- Project Name (for instance): SparkScalaCourse -> Press _Finish_
  
  - 8- Inside of Project Created: Right click on it and click . New -> Package -> com.orgname.projectname (i.e. com.sundogsoftware.spark)
      
     - You can choose any name for your package but read a litle about the convenctions for that 
     
  - 9- Right click on the package and choose import
  
  ![package_import](https://user-images.githubusercontent.com/37953610/58631942-f87e8280-82db-11e9-90bb-49f86980dd22.jpg)
  
  - Import the source code from your local system
  
    - Choose General -> File System 
    
    - Select the folder fo Source Code that you saved it in a safe place.
    
    - Select the Ratings Counter
    
    ![import_source_code](https://user-images.githubusercontent.com/37953610/58632722-5c09af80-82de-11e9-83f3-d7e2352e870a.jpg)
    
    
    ## Fix the errors
 
 **1 - Library Errors**
 
    The erros that you will find are related to the missed libraries. Now you need to import the libraries.
    
- Right click above of your project as you see in the next figure
    
    ![import_libraries](https://user-images.githubusercontent.com/37953610/58633075-56f93000-82df-11e9-8a4f-e650fbff30eb.jpg)
    
- Choose Proprieties -> Java Build Path -> Libraries -> Add External Jars
    
  - Go to the folder where you installed the spark (c:\spark\jars). Press _ctrl + a_ to select everything and click in _Open_
  
**2 - Incompablity Errors**

After add external Jars you will see erros related with the incompability among version of Scala IDE and Scala version that you installed.

**Important**: You need change the first code line to, before you run the configurations:

      package com.orgname.projectname
  

 To fix the errors that do a right click above your project and choose:

  - Proprieties -> Scala Compiler -> Fixed Scala Instalation: 2.11.11 (built-in) ------ in my case
  
**3- Run the program**

Go to the main menu: Run -> Run Configurations... -> Scala Application (click on it).

![run_menu](https://user-images.githubusercontent.com/37953610/58635526-06390580-82e6-11e9-9bd2-2515a0fd6006.jpg)

Give the:

  - Name: RatingsCounter

  - Main Class: com.orgname.projectname
  
  - Click Run

**Interpreting the results**

The results is the amount of movies have ranked by one, two, three ,four or five stars.

![hist_mov_rat](https://user-images.githubusercontent.com/37953610/58642704-c7ab4700-82f5-11e9-9338-49d54dca720b.JPG)
