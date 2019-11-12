AWS BigData:

Kinesis:

Kinesis Data Streams:

1. Data Stream consists of set of Shards. Shard has many data records. Kinesis billing is per shard. Records are sorted per shard.
2. Data Records are composed of - Sequence Number, Partition Key and Data Blob. Data blob can be upto 1 MB size. All the records in a shard have same partition key. Sequence Number is unique to each record and is added by Kinesis after ingestion.
3. The default (and minimum) retention period of data records in Kinesis Data Stream in 24 hours. The max retention period can be 7 days
4. Each stream will have publisher and consumer. The consumers can be of 2 types - 
      a. fan-out consumers
      b. enhanced fan-out consumers
5. Producer Limit - 1 MB/sec or 1000 messages/sec at write per SHARD
6. Each shard can support 5 transaction (API calls) per second for reads, upto total read of 2 MB per second. 
Note: Consumer Classic - 2 MB/Sec read capacity per shard across all consumers
      Consumer Enhanced Fan-Out - 2 MB/Sec read capacity per shard per enhanced consumer
7. The total capacity of a stram is the sum of capacities of all the shards
8. A partition key in a data record is a unicode string of max size 256 bytes. The sequence number is unique per partition key in a shard.
9. Kinesis Data Stream supports following producers -
      a. SDK
      b. Kinesis Producer Library (KPL)
      c. Kinesis Agent
      d. 3rd Party libraries (Spark, Log4j, kafka etc.)
10. Kinesis SDK can 2 methods for data ingestion - PutRecord (single data record) and PutRecords (for multiple insert in batch mode)
11. Kinesis SDK is used for low throughput, higher latency integrations. It creates simple API/Lambda integration
12. ProvisionedThroughputExceeded Exceptions happens when sending more data (exceeding MB/s or TPS for any shard)
13. Kinesis Producer Library (KPL) is set of C++ & Java libraries. This is used for high performance and long running streaming jobs. KPL supports both synchronous and asynchronous API calls.
14. Batching (both turned on by default) – increase throughput, decrease cost: 
      • Collect Records and Write to multiple shards in the same PutRecords API call
      • Aggregate – increased latency
            • Capability to store multiple records in one record (go over 1000 records per second limit)
            • Increase payload size and improve throughput (maximize 1MB/s limit)
15. Kinesis Agent is a Java based program built on top of KPL. I can be installed on Linux based servers. It is used mainly for log data ingestion.
16. Kinesis Agent Features -
      a. Write from multiple directories and write to multiple streams.
      b. Routing feature based on directory/log file
      c. Pre-process data before sending etc.
17. Kinesis classic consumers pull data from Kinesis data streams. Kinesis support following classic consumers -
      a. Kinesis SDK
      b. Kinesis Client Library (KCL)
      c. Kinesis Connector Library
      d. AWS Lambda
      e. Kinesis Firehose
      f. 3rd Party Libraries (Spark, Log4j, Flume, Kafka Connect etc.)
18. This is a Java first client lirary but exists for other progamming language as well (Python, .Net etc.). Share multiple shards with multiple consumers in one group - Shard Discovery.
19. KCL provides checkpointing capability. KCL uses DynamoDB for checkpointing and co-ordination. Sufficient RCU and WCU should be provisioned in DynamoDB to avoid slow-down.
20. Kinesis Connector Library is a old (2016) Java library that leverages KCL. Kinesis Client Library runs on a EC2 instance and it can write data to following -
      a. Amazon S3
      b. Amazon DynamoDB
      c. Redshift
      d. ElasticSearch
 AWS Lambda and Kinesis Firhose remove the need to using Kinesis Connector Libraries
 21. Kinesis Enhanced Fan-out is a game changing feature introduced in Aug-2018. Each enhanced fan-out consumer can read upto 2 MB/sec per shard. In this mode, kinesis pushes the data to consumers over HTTPS.
 22. Kinesis support shard splitting and shard merging. Kinesis auto-scaling is not a native feature. We can implement auto-scaling using AWS lambda.
 
 Kinesis Firehose:
 
 1. Kinesis Firehose is a fully managed near real-time data streaming service. The minimum data latency for Kinesis Firehose is 60 sec. 
 2. KFS supports data ingestion into - s3, RedShift, ElasticSearch and Splunk. Lambda function can be invoked to transform the data out from KFS before sending the same to the target.
 3. Firehose supports compression of data when the target is s3 (GZIP, ZIP, SNAPPY)
 4. Firehose buffers the data before sending it to the target. The buffer is flushed based on the buffer size and timing rule. For Lambda, the maximum buffer size can be 3 MB.
 
 
 Simple Queue Service (SQS):
 
 1. It is a fully managed queue service on AWS. SQS can scale from 1 message to 10000 messages/sec.
 2. Default retention of SQS is 4 days and maximum reyention is 14 days. Max size of each message is 256 KB
 3. AWS SQS-FIFO has lower throughput - upto 3000 messages/sec. Messages are processed in order by the consumer. Messages are sent only once to the consumer.
 4. Messages of size larger than 256 KB can be sent to consumer using SQS Extended client (Java library). 



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
6. For cross region replication, versioning must be enabled for both the buckets.
7. AWS s3 E-Tag is used to verify the integrity of the file.For file size < 5GB, it is done based on MD5 hash value
8. s3 performance may degrade when TPS > 100. 
9. Add random characters in front of the object name to distribute the objects to many partitions.Never use dates for pre-fix keys
10. Currenly s3 supports 3500 RPS for PUT and 5500 RPS for GET for each prefix
11. s3 cloudFront is used to cache s3 content.s3 transfer acceleration uses Edge locations



