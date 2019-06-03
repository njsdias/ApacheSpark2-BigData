# Problem Definition. 

For this exmaple follows the _MinTemperatures.scala_ file.

Here we are dealing with a dataset that have a feature which identify if the feature value is for TMIN, TMAX or PPRCP. The objective is extract the most miniminum TMIN per station ID. Next we can see a brief of the dataset.

ITE00100554,18000101,TMAX,-75,,,E,

ITE00100554,18000101,TMIN,-148,,,E,

GM000010962,18000101,PRCP,0,,,E,

The next line code extract all entries that have TMIN using **filter** function.

    val minTemps = parsedLines.filter(x => x._2 == "TMIN")    //position 2 of the dataset 0 -> 1 -> 2 

Once our output is (stationID, entryType, temperature) we want a RDD only with (stationID, temperature) because all entry type is TMIN.

    val stationTemps = minTemps.map(x => (x._1, x._3.toFloat))

Now, we reduce our dataset to most min TMIN for each stationID. So, we use **reduceByKey** with **min** to extract the informatoin that we want. Don't forget collect the results.

    val minTempsByStation = stationTemps.reduceByKey( (x,y) => min(x,y))
    
    val results = minTempsByStation.collect()

At the end we have the next results:

    EZE00100082 minimum temperature: 7,70 F
    ITE00100554 minimum temperature: 5,36 F
