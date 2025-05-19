# PySpark Cheatsheet
  - Often use "pyspark.sql.functions" --> import: `from pyspark.sql import functions as sf`

## Load data
  - Read data from "delta" format: `ce_df = spark.read.format("delta").load("s3a://au-intlog-cpr-staging-1/<Path-And-FileName>")`
  - Show schema: `ce_df.printSchema()`
  - Create temp view, which can then be queried by SparkSql: `result.limit(5000).createOrReplaceTempView("ce_result")`
  
## Filtering
  - Against literal
    ```
    filtered_event_types = ["ARV", "IRA", "FUL", "GOU", "RLS", "CAV"]
    
    ce_df.filter((ce_df["CET_SystemCreateTimeUTC"] >='2025-04-01') \
                    & (ce_df["CET_IsActual"] == True) \
                    & (ce_df["CET_EventType"].isin(filtered_event_types)))
    ```
  - Using function `col` from "pyspark.sql.functions":
    ```
    ce_df.filter((sf.col('Arrival_DT') < sf.col('CustomRelease_DT')) & (sf.col('Arrival_DT') < sf.col('GateOut_DT')))
    ```

## Select
  - Using name directly: `ce_df.select("CET_CarrierBookingReference", "Location").distinct()`
  - Using function `col` from "pyspark.sql.functions":
    ```
    ce_df.select(sf.col("CET_SystemCreateTimeUTC").alias("CreatedTimeUTC"), sf.col("CET_ProviderCode"))
    ```
  - Select with coalesce after alias
    ```
    ce_df.select('arv.CET_ProviderCarrierCode', 'arv.CET_MasterBillNumber', 'arv.CET_CarrierBookingReference',
        sf.coalesce('arv.FacilityCodeSMDG', 'rls.FacilityCodeSMDG', 'gou.FacilityCodeSMDG').alias("FacilityCodeSMDG"))
    ```
    
## Join
  - Basic join: 
    ```
    ce_1.join(ce_2, ["CET_ProviderCarrierCode", "CET_MasterBillNumber", "CET_CarrierBookingReference"], "inner")
    ```
  - Join with alias and column renamed
    ```
    ce_1.alias("arv").withColumnRenamed("EventTime", "Arrival_DT") \
       .join(ce_2.alias("rls").withColumnRenamed("EventTime", "CustomRelease_DT"), 
             ["CET_ProviderCarrierCode", "CET_MasterBillNumber", "CET_CarrierBookingReference", "Location"], 
             "inner")
    ```
## GroupBy
  - Basic syntax: `df.groupBy(["CET_MasterBillNumber", "CET_CarrierBookingReference"])`
  - Aggreegation using Function after GroupBy (on non-groupby columns)
    ```
    df.groupBy(["CET_MasterBillNumber", "CET_CarrierBookingReference"])
      .agg(
        sf.first("VoyageNumber", True).alias("VoyageNumber"),
        sf.min("Arrival_DT").alias("Arrival_DT")
      )
    ```