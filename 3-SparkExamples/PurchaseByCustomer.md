# Problem Definition

Find the the total amount spent by customer. The smaple contains: 

 - custId, 
 
 - itemID,
 
 - amountSpent.
 
 The objective is know how much each customer spent. 
 
 Define a strategy:
 
 - split each comma-delimited line into fields
 
 - map each line to key/value pairs of customer ID and dollar amount
 
 - use reduceByKey to add up amount spent by customer ID
 
 - collect() the results and print them
  
First we need create a new _Scala Object_ and give the name: _com.orgname.spark.PurchasebyCustomer_

Now lest's write some code.

- split each comma-delimited line into fields

      def extractCustomerPricePairs(line: String) = {
        val fields = line.split(",")
        (fields(0).toInt, fields(2).toFloat)
      }
      
- map each line to key/value pairs of customer ID and dollar amount
 
      val mappedInput = input.map(extractCustomerPricePairs)

- use reduceByKey to add up amount spent by customer ID

      val totalByCustomer = mappedInput.reduceByKey( (x,y) => x + y )

- collect() the results and print them
 
      val results = totalByCustomer.collect()

But we want th results sorted by amount of spent. so the key is the amount spent by customer and the custID is the value.

    val flipped = totalByCustomer.map( x => (x._2, x._1) )
    val totalByCustomerSorted = flipped.sortByKey(false)
