AWS BigData:

Kinesis:

Kinesis Data Streams:

1. Data Stream consists of set of Shards. Shard has many data records
2. Data Records are composed of - Sequence Number, Partition Key and Data Blob. Data blob can be upto 1 MB size
3. The default (and minimum) retention period of data records in Kinesis Data Stream in 24 hours. The max retention period can be 7 days
4. Each stream will have publisher and consumer. The consumers can be of 2 types - 
      a. fan-out consumers
      b. enhanced fan-out consumers
5. Each shard can support 5 transaction (API calls) per second for reads, upto total read of 2 MB per second. Shard may support 1000 records for write, upto total write of 1 MB per second.
Note: Consumer Classic - 5MB/Sec read capacity per shard across all consumers
      Consumer Enhanced Fan-Out - 5MB/Sec read capacity per shard per enhanced consumer
6. The total capacity of a stram is the sum of capacities of all the shards
7. A partition key in a data record is a unicode string of max size 256 bytes. The sequence number is unique per partition key in a shard.
8. Kinesis Data Stream supports following producers -
      a. SDK
      b. Kinesis Producer Library (KPL)
      c. Kinesis Agent
      d. 3rd Party libraries (Spark, Log4j, kafka etc.)
9. Kinesis SDK can 2 methods for data ingestion - PutRecord (single data record) and PutRecords (for multiple insert in batch mode)
10. Kinesis SDK is used for low throughput, higher latency integrations. It creates simple API/Lambda integration
11. ProvisionedThroughputExceeded Exceptions happens when sending more data (exceeding MB/s or TPS for any shard)
12. Kinesis Producer Library (KPL) is set of C++ & Java libraries. This is used for high performance and long running streaming jobs
13. Kinesis Agent is a Java based program built on top of KPL. I can be installed on Linux based servers. It is used mainly for log data ingestion.



Amazon S3:

1. Buckets must have globally unique name
2. Max size of a file in S3 is 5 TB. If size is more than 100 MB then multi-part upload should be used. It is must for file size > 5 GB
3. Read after Write consistency for PUTs of new objects. Eventual consistency for DELETEs and PUTs of existing objects
4. S3 storage tiers -
      a. s3 standard - general purpose - 99.99% availability
      b. s3 standard - Infrequently access (IA) - monthly access - 99.9% availability
      c. s3 one zone - Infrequently access - less durability - 99.5% avilability
      d. s3 intelligent tiering (new!) - data automatically moves to right tier based on usage
      e. Amazon glacier - archived data - Each item is called Archive; the archives are stored in vaults.
5. Glacier data retrieval -
      a. expedited - 1-5 min
      b. standard - 3-5 hours
      c. bulk - 5-12 hours


