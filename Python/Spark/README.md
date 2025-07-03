# General
  - [PySpark Cheatsheet](./PySpark_Cheatsheet.md)

# Spark Basic

## Version

```
print(" - Spark version: ", sc.version)
print(" - Scalar version: ", util.Properties.versionString)
```

## Language per paragraph in Zeppelin

  - Default is Scalar: `%spark`
  - Pyspark: `%pyspark`
  - SparkSql: `%sql`
  - R: `%r`
  ...

## Loading dataset

```
val container_events = spark.read.format("delta").load("...")
container_events.printSchema()
```

## Filter

```
var dhr_df = container_events.filter(col("CET_SystemCreateTimeUTC").gt(lit("2024-01-01")) && (col("CET_DeliveryAction") != 2) && (col("CET_EventType") === lit("DHR")))

// or one by one (NOTE: Equality in spark is 3 "=")
// var ce_df = container_events.filter(col("CET_SystemCreateTimeUTC").gt(lit("2024-01-01")))
// var dhr_df = ce_df.filter(col("CET_EventType") === lit("DHR"))
```

## Select/Map a sub-set of columns

```
var eta_df = raw_eta.select(col("CET_Id").as("Id"), 
                        col("CET_SystemCreateTimeUTC").as("CreatedTimeUTC"),
						col("CET_EventLocalTimeUTC").as("EventTimeUTC"),
                        col("EventParameters.TransportMode").as("TransportMode"),
                        col("ContextCollection.CarrierCode").as("CarrierCode"),
						col("EventParameters.Location").as("Location"),
                        col("ContextCollection.Facility").as("CtxFacility"),
						col("CET_MasterBillNumber").as("MBN"),
						col("CET_CarrierBookingReference").as("BIL"),
						col("ContextCollection.VesselName").as("VesselName"),
						col("ContextCollection.LloydsNumber").as("VesselIMO"),
						col("ContextCollection.VoyageNumber").as("VoyageNumber")
						).withColumn("GroupKey", concat_ws("-", col("MBN"), col("BIL"), col("VesselName"), col("VesselIMO"), col("VoyageNumber"))
						).drop("MBN", "BIL", "VesselName", "VesselIMO", "VoyageNumber")
```

## Counts

### Using Scalar

```
var count_per_carrier = dhr.groupBy("CarrierCode")
                                .agg(count("*").as("Total"),
                                     count("CtxFacility").as("HasFacility"),
                                     count(when(col("CtxFacility").isNull || col("CtxFacility") === "" || col("CtxFacility").isNaN, 1)).as("MissingFacility"))
count_per_carrier.show()
```

### Using SQL

  - First need to create a temp view
    ```
    dhr.createOrReplaceTempView("dhr_table")
    ```
  - Then we can query using sql syntax
    ```
    %sql
    select CarrierCode, 
				count(Id) as TotalCount, 
				sum(case when Location is null then 1 else 0 end) LocationMissing,
				sum(case when CtxFacility is null then 1 else 0 end) FacilityMissing,
				count(CtxFacility) as HasFacility
    from dhr_table group by CarrierCode 
	```
	
# Tips & Tricks
  - Select a specific offset using SparkSql
    ```
	SELECT * FROM ce_result
	ORDER BY CET_SystemCreateTimeUTC
	LIMIT 1000
	OFFSET 2000
	```
  - Convert data from Scala to PySpark
    + In Scala, put the data in Object Exchange: `z.put("df", containerEventDf: org.apache.spark.sql.DataFrame)`
	+ Read it from Object Exchange in PySpark
    ```
	%pyspark
	from pyspark.sql import DataFrame

	df = DataFrame(z.get("df"), sqlContext)
	```
  - Convert data from PySpark to Scala
    + In Python: `z.put("df", df._jdf)`
	+ In Scala: `val df = z.get("df").asInstanceOf[org.apache.spark.sql.DataFrame]`
  - Convert data via SqlContext.table
    + Create temp view: `df.createTempView("df")`
	+ In Scala: `val df = spark.table("df")`
	+ In Python: `df = sqlContext.table("df")`