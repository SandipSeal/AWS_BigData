# AWS BigData:

## Kinesis:

### Kinesis Data Streams:

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
19. KCL provides checkpointing capability. KCL uses DynamoDB for checkpointing and co-ordination. Sufficient RCU and WCU should be provisioned in DynamoDB to avoid slow-down. For each Amazon Kinesis Data Streams application, the KCL uses a unique Amazon DynamoDB table to keep track of the application's state. Because the KCL uses the name of the Amazon Kinesis Data Streams application to create the name of the table, each application name must be unique.
20. Kinesis Connector Library is a old (2016) Java library that leverages KCL. Kinesis Client Library runs on a EC2 instance and it can write data to following -
      a. Amazon S3
      b. Amazon DynamoDB
      c. Redshift
      d. ElasticSearch
 AWS Lambda and Kinesis Firhose remove the need to using Kinesis Connector Libraries
 21. Kinesis Enhanced Fan-out is a game changing feature introduced in Aug-2018. Each enhanced fan-out consumer can read upto 2 MB/sec per shard. In this mode, kinesis pushes the data to consumers over HTTPS.
 22. Kinesis support shard splitting and shard merging. Kinesis auto-scaling is not a native feature. We can implement auto-scaling using AWS lambda.
 23. Enhanced Fan-out consumers can be built by using either KCL or Kinesis API. KCL 2.0 onward automatically connects to all the shards in a kinesis data stream.API connects to individual shard.
 
### Kinesis Firehose:

- https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html#data-flow-diagrams
 
1. Kinesis Firehose is a fully managed near real-time data streaming service. The minimum data latency for Kinesis Firehose is 60 sec. 
2. KFS supports data ingestion into - s3, RedShift, ElasticSearch and Splunk. Lambda function can be invoked to transform the data out from KFS before sending the same to the target.
3. Firehose supports compression of data when the target is s3 (GZIP, ZIP, SNAPPY). Snappy or Zip compression is not available for delivery streams with Amazon Redshift as the destination. Compression is not enabled by default.
4. Firehose buffers the data before sending it to the target. The buffer is flushed based on the buffer size and timing rule. For Lambda, the maximum buffer size can be 3 MB.
5.  Kinesis Data Flow:

![S3 Destination](https://github.com/SandipSeal/AWS_BigData/blob/master/fh-flow-s3.png)
![RedShift Destination](https://github.com/SandipSeal/AWS_BigData/blob/master/fh-flow-rs.png)
![AWS ElasticSearch Destination](https://github.com/SandipSeal/AWS_BigData/blob/master/fh-flow-es.png)
![Splunk Destination](https://github.com/SandipSeal/AWS_BigData/blob/master/fh-flow-splunk.png)
 
 
### Kinesis Data Analytics

![Kinesis_Analytics](https://github.com/SandipSeal/AWS_BigData/blob/master/kinesis-app.png)

1. In-Application streams are created for intermediate storage of data from streaming sources and this facilitae running SQL query on streaming data.

2. Data in ingested into In-Application streams in 2 steps:
- Create In-Application stream
- Insert data into In-Application stream using data pump

Multiple writes can write into an in-application stream and multiple readers can read from a stream

3. After an in-application stream is created, you can perform normal SQL queries. When you query streams, most SQL statements are bound using a row-based or time-based window. For more information, see Windowed Queries.

You can also join streams. For examples of joining streams, see Streaming Data Operations: Stream Joins.

4. You can specify a query to process records in a tumbling window, sliding window, or stagger window manner, depending on your application needs. Kinesis Data Analytics supports the following window types:

- Stagger Windows: A query that aggregates data using keyed time-based windows that open as data arrives. The keys allow for multiple overlapping windows. This is the recommended way to aggregate data using time-based windows, because Stagger Windows reduce late or out-of-order data compared to Tumbling windows.

- Tumbling Windows: A query that aggregates data using distinct time-based windows that open and close at regular intervals.

- Sliding Windows: A query that aggregates data continuously, using a fixed time or rowcount interval.

### Kinesis Video Stream

1. Video Stram APIs -
- Producer API (PutMedia)
- Consumer API (GetMedia, GetMediaFromFragmentList)
 
 
## Simple Queue Service (SQS):
 
 1. It is a fully managed queue service on AWS. SQS can scale from 1 message to 10000 messages/sec.
 2. Default retention of SQS is 4 days and maximum reyention is 14 days. Max size of each message is 256 KB
 3. AWS SQS-FIFO has lower throughput - upto 3000 messages/sec. Messages are processed in order by the consumer. Messages are sent only once to the consumer.
 4. Messages of size larger than 256 KB can be sent to consumer using SQS Extended client (Java library). 
 
 Data Movement into AWS:
 
 1. Direct Connect
 2. Database Migration Service (DMS)
 3. Snowball + SnowMobil + Snowball Edge (storage + compute)

## Amazon S3:

1. Buckets must have globally unique name
2. Max size of a file in S3 is 5 TB. If size is more than 100 MB then multi-part upload should be used. It is must for file size > 5 GB
3. Read after Write consistency for PUTs of new objects. Eventual consistency for DELETEs and PUTs of existing objects. GET after PUT returns code 200 except the situation where one had performed a GET before the PUT to check if the object existed. 
4. S3 storage tiers -
      a. s3 standard - general purpose - 99.99% availability. Durability is 11'9. Data is replicated in 3 AZs.
      b. s3 standard - Infrequently access (IA) - monthly access - 99.9% availability. There is a charge when the data is accessed.
      c. s3 one zone - Infrequently access - less durability - 99.5% avilability. Cost is lower than s3 IA.
      d. s3 intelligent tiering (new!) - data automatically moves to right tier based on usage. 99.9% availability.
      e. Amazon glacier - archived data - Each item is called Archive; the archives are stored in vaults. The size of each archive can be upto 40TB.
      f. s3 RRS - Reduced Redundancy Storage. This tier has less durability.
   Storage class can be selected at the time of file upload.
5. Glacier data retrieval -
      a. expedited - 1-5 min
      b. standard - 3-5 hours
      c. bulk - 5-12 hours
6. Glacier provides vault lock feature that enable users to implement WORM (write once read many) requirement. Control over a vault can be enforced using vault lock policy. Policy cannot be changed after locking.
7. Vault Lock life-cycle:
```
      Attach a lock policy to the vault -----> lock is set to In-Progress state -----> users have 24 hours to validate the lock -----> complete the lock -----> vault lock goes to Locked state (Immutable)
```
8. For cross region replication, versioning must be enabled for both the buckets. Buckets can be in different accounts.We can change the storage class of the target bucket. Replication happens only for the new files i.e. the files uploaded after enabling the replication option.
9. AWS s3 E-Tag is used to verify the integrity of the file.For file size < 5GB, it is done based on MD5 hash value
10. s3 performance may degrade when TPS > 100. Data goes into different partitions. 
11. Add random characters in front of the object name to distribute the objects to many partitions.Never use dates for pre-fix keys
12. Currenly s3 supports 3500 RPS for PUT and 5500 RPS for GET for each prefix
12. s3 cloudFront is used to cache s3 content (improves reads).s3 transfer acceleration uses Edge locations (improves writes).
13. s3 LifeCycle Rule ---> Transition Action or Expiry Action. LifeCycle is present under Management tab. Users can apply filter while confuguring lifecycle policies.
14. Versioning at bucket level safeguards against accidental delete. If versioning is enabled then file cannot be deleted from UI; it just creates a delete marker.
15. AWS s3 encryption options -
      SSE-s3: Encryption keys are handled and managed by AWS. Must set the header. It uses 265 bit encryption key.
      SSE-KMS: Data key (CMK) is managed in KMS. Must set the header. 
      SSE-C:Encryption key is provided by client. Data Key is passed in the header. Possible in API call only.
      Client side encryption: Data is encrypted by client before sending to AWS
16. Amazon exposes both HTTP & HTTPS end-points. HTTPS must be used for SSE-C

## AWS Athena

### Athena Security

1. Following Data can be encrypted in Athena -
- Results of all the query output in AWS S3
- AWS Glue data catalog
2. Following S3 encryptions are supported in Athena -
- SSE-S3
- SSE-KMS
- Client Side Encryption with KMS key <br />
Athena does not support SSE with customer-provided keys (SSE-C), nor does it support client-side encryption using a client-side master key.
3. The setup for querying an encrypted dataset in Amazon S3 and the options in Athena to encrypt query results are independent. Each option is enabled and configured separately. You can use different encryption methods or keys for each. This means that reading encrypted data in Amazon S3 doesn't automatically encrypt Athena query results in Amazon S3. The opposite is also true. Encrypting Athena query results in Amazon S3 doesn't encrypt the underlying dataset in Amazon S3.
4. Regardless of whether you use options for encrypting data at rest in Amazon S3, transport layer security (TLS) encrypts objects in-transit between Athena resources and between Athena and Amazon S3. Query results that stream to JDBC or ODBC clients are encrypted using TLS.
5. Users need to have additional IAM permission for AWS KMS to access the data in s3 encrypted by AWS KMS encryption key. 
6. In addition to encrypting data at rest in Amazon S3, Amazon Athena uses Transport Layer Security (TLS) encryption for data in-transit between Athena and Amazon S3, and between Athena and customer applications accessing it.
7. When using Athena with the AWS Glue Data Catalog, you can use AWS Glue to create databases and tables (schema) to be queried in Athena, or you can use Athena to create schema and then use them in AWS Glue and related services. This topic provides considerations and best practices when using either method.
8. Under the hood, Athena uses Presto to execute DML statements and Hive to execute the DDL statements that create and modify schema. With these technologies, there are a couple of conventions to follow so that Athena and AWS Glue work well together.
9. Using Multiple Data Sources with Crawlers:
When an AWS Glue Crawler scans Amazon S3 and detects multiple directories, it uses a heuristic to determine where the root for a table is in the directory structure, and which directories are partitions for the table\. In some cases, where the schema detected in two or more directories is similar, the crawler may treat them as partitions instead of separate tables\. One way to help the crawler discover individual tables is to add each table's root directory as a data store for the crawler\.

The following partitions in Amazon S3 are an example:

```
s3://bucket01/folder1/table1/partition1/file.txt
s3://bucket01/folder1/table1/partition2/file.txt
s3://bucket01/folder1/table1/partition3/file.txt
s3://bucket01/folder1/table2/partition4/file.txt
s3://bucket01/folder1/table2/partition5/file.txt
```

If the schema for `table1` and `table2` are similar, and a single data source is set to `s3://bucket01/folder1/` in AWS Glue, the crawler may create a single table with two partition columns: one partition column that contains `table1` and `table2`, and a second partition column that contains `partition1` through `partition5`\.

To have the AWS Glue crawler create two separate tables, set the crawler to have two data sources, `s3://bucket01/folder1/table1/` and `s3://bucket01/folder1/table2`, as shown in the following procedure\.

## AWS IoT

AWS IoT Architecture:

![AWS_IoT](https://github.com/SandipSeal/AWS_BigData/blob/master/aws_iot_data_services.png)

- https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html

### IoT Device Gateway:

- Entry point for IoT devices to connect to AWS infrastructure.
- It is fully manages and scales automatically to support even a billion devices
- It supports MQTT, WebSockets and HTTP 1.1 protocol

### IoT Message Broker:

- It is a pub-sub service within AWS IoT - low latency
- Publishes the messages to a topic - like SNS service
- Mesages are forwarded to all the services connected to the Message Broker

### IoT Thing Registry:

- This is IAM of IoT
- Organises resources associated with each device in the IoT cloud
- Maintaintains metadata corresponding to each device. Each connected device gets unique Id.
- Can create X.509 certificate to help device to connect to AWS
- Allows to create device group

### Device Shadow:

- It is a JSON document representing the state of a connected device(Thing)
- We can set the state to different desired states

### Rules Engine:

- Rules are defined on Message Broker (MQTT) topics.
- Rules = When it's triggered or what action it does.
- Rules engine has large number of use cases and it can direct the message to many AWS services.
- Rules need IAM roles to perform their tasks

## IOT Greengrass:

- This brings compute layer to the device.
- Allows to execute AWS Lambda
- Can operate offline

## Amazon DynamoDB:

1. Fully managed; supports both key/value and document data models.
2. DynamoDB is highly scalable, highly available. It uses SSD for storage
3. Each table in DynamoDB must have a primary key; primary key must be selected at the time of table creation. Each row in DynamoDB table is called an item. The maximum size of an item can be 400 KB. 
4. RCU and WCU of a table are split across all the partitions in a table. If RCU is 100 and the table has 4 partitions then each partition will get 25 RCU.
5. A single parition can hold only 10GB of data. Each parition can support max 3000 RCU and 1000 WCU. Partition can be increased but cannot be decreased. Each partition key belongs to only one partition in DynamoDB table.
6. Amazon DynamoDB RCU & WCU
WCU: 1 WCU = 1 write per second for an item of size upto 1 KB.
RCU: 1 RCU = 1 Strong consistent read or 2 eventual consistent read per second for an item of size upto 4 KB
7. If RCU/WCU are exceed then 'ProvisionedThroughputExceededException' occurs
8. Calculating the number of partitions required in a DynamoDB table:
      By Capacity = Total size of the table / 10GB
      By throughput = (RCU/3000) + (WCU/1000)
      Num Partitions = Max (capacity,throughput)
9. DynamoDB supports 2 types of indeces - Local Secondary Index (LSI) and Global Secondary Index (GSI)
      LSI - This must be created at the time of table creation. LSI is local to the table partition key
      GSI - This can be added to the table after creation. GSI creates a table under the hood for maintaining the index. User needs to define RCU & WCU separately for the index.
      - Local Secondary Index contains - Partition key, New Sort Key and old Sort key (if exists) + Optional Projected values
      - Any data written to the table is copied async to any LSI
      - Shares WCU & RCU with the table
      - LSI is a sparse index. The Index will only have an item (row) if the new sort index key attribute is contained in the original           table item (row)
      - ItemCollections - set of rows that have same partition key and all of its LSI rows. Max size of ItemCollection is 10GB/table.
      - GSI has the concept as LSI but it allows to define an alternative partition & sort key
      - Unlike LSI where WCU & RCU are not shared with main tables, RCU & WCU are defined for the GSI - same way as table
      - Changes made to the table are asynchronously applied to GSI
10. DynamoDB DAX is a cache layer on top of DynamoDB. 
11. DynamoDB change logs can be sent to a stream (can be enabled at table level). DynamoDB stream can be consumed by Lambda function for appropriate actions; also the stream can be consumed by Kinesis (using KCL library). Data is retained in streams for 24 hours.
12. DynamoDB TTL defines the timestamp till which an item will be valid in a table. Once the TTL is passed the respective items will be expired in the table and won't consume any WCU/RCU. DynamoDB typically deletes the expired item within 48 hours of expiry.When Time to Live (TTL) is enabled on a table in Amazon DynamoDB, a background job checks the TTL attribute of items to determine whether they are expired.
13. The deleted items due to TTL also deletes the items in index tables (in case of GSI). TTL compares the current time in epoch time format to the time stored in the Time to Live attribute of an item. If the epoch time value stored in the attribute is less than the current time, the item is marked as expired and then deleted. This processing takes place automatically in the background and does not affect read or write traffic to the table. 
**Note**  
 The epoch time format is the number of seconds elapsed since 12:00:00 AM January 1st, 1970 UTC\. 
14. All the changes (Insert/Update/Delete) made to a DynamoDB table are sent to a stream. DynamoDB stream can be read by AWS Lambda. Stream persists the data for max 24 hours. DynamoDB stream can also feed Kinesis using KCL.
15. In a DynamoDB table having more than one partition, the data is distributed using partition key (Hash Value)
16. Criteria of a good partition key in DynamoDB table:
      - The atribute should have any distinct values
      - The attribute should have a uniform write pattern accross the key space
      - The attribute should have uniform temporal write pattern accross time
      - If any of the above is not possible to achieve then a synthetic/hybrid value should be considered
      - Should not mix HOT & COLD key values in a table
17. Adaptive capacity feature enables DynamoDB to accommodate imbalanced workloads. With adaptive capacity, users don’t need to overprovision read and write throughput. The unused capacity (WCU/RCU) of less access partitions is allocated/loaned to highly accessed partitions.
18. In DynamoDB, you have the option to specify conditions when granting permissions using an IAM policy (see Access Control). For example, you can:

- Grant permissions to allow users read-only access to certain items and attributes in a table or a secondary index.
- Grant permissions to allow users write-only access to certain attributes in a table, based upon the identity of that user.
19. Using Amazon DynamoDB global tables, you can replicate your table data across AWS Regions\. It is important that the replica tables and secondary indexes in your global table have identical write capacity settings to ensure proper replication of data\.
20. Requirements for Adding a New Replica Table
If you want to add a new replica table to a global table, each of the following conditions must be true:
      + The table must have the same partition key as all of the other replicas\.
      + The table must have the same write capacity management settings specified\.
      + The table must have the same name as all of the other replicas\.
      + The table must have DynamoDB Streams enabled, with the stream containing both the new and the old images of the item\.
      + None of the new or existing replica tables in the global table can contain any data\.

If global secondary indexes are specified, then the following conditions must also be met: 
      +  The global secondary indexes must have the same name\. 
      +  The global secondary indexes must have the same partition key and sort key \(if present\)\. 
21. Best Practices and Requirements for Managing Capacity

Consider the following when managing capacity settings for replica tables in DynamoDB\.

### Using DynamoDB Auto Scaling

Using DynamoDB auto scaling is the recommended way to manage throughput capacity settings for replica tables that use the provisioned mode\. DynamoDB auto scaling automatically adjusts read capacity units \(RCUs\) and write capacity units \(WCUs\) for each replica table based upon your actual application workload\.

### Managing Capacity Manually

If you decide not to use DynamoDB auto scaling, then you must manually set the read capacity and write capacity settings on each replica table and secondary index\.

The provisioned replicated write capacity units \(rWCUs\) on every replica table should be set to the total number of rWCUs needed for application writes across all Regions multiplied by two\. This will accommodate application writes that occur in the local Region and replicated application writes coming from other Regions\. For example, if you expect 5 writes per second to your replica table in Ohio and 5 writes per second to your replica table in N\.Virginia, then you should provision 20 WCUs to each replica table \(5 \+ 5 = 10; 10 x 2 = 20\)\.

22. You can use Amazon CloudWatch to monitor the behavior and performance of a global table. Amazon DynamoDB publishes ReplicationLatency and PendingReplicationCount metrics for each replica in the global table.
- ReplicationLatency—The elapsed time between when an updated item appears in the DynamoDB stream for one replica table, and when that item appears in another replica in the global table.
- PendingReplicationCount—The number of item updates that are written to one replica table, but that have not yet been written to another replica in the global table.

## Elastic MapReduce:

1. This is a managed hadoop cluster on AWS. HUE - Hadoop User Experience. 
2. Hadoop Architecture:
      a. Hadoop Common - contains libraries and utilities
      b. HDFS - distributed file system; fault-tlerent
      c. YARN - Resource manager
      d. MapReduce - Processing framework for Hadoop
3. EMR Node types - MasterInstanceGroup (only 1 Master Node), CoreInstanceGroup (one or more Core Nodes) and TaskInstanceGroup (upto 48 task nodes). EMR cluster can have only one master node (single point of failure). 
4. Core node - slave node in Hadoop architecture. It store data as part of HDFS or EMRFS (extends hadoop to access data from s3). Core node runs NodeManager daemon. 
5. Tasks Nodes - This is also a slave node. Task nodes are optional. No data node daemon. Task nodes can be added or removed; these nodes provides extra capacity.
6. EMR is a single AZ concept. Blocks are replicated across node; due to latency in transfering block across Availability Zones, blocks are stored in single avaiability zone. A typical DFS won't work well with Hadoop. In HDFS, each block of data is replicated 3 types. The block size in HDFS is 64 MB (default). Large blocksize will maximise the triughput for large file. Larger block size will reduce the random block seeks. 
7. Replication factor can be changed at file level. HDFS options - 
      - Instance Store - this option provides high I/O
      - EBS Volume - EBS volume can be attached to EMR cluster
      - EMRFS - allows cluster to store data in s3. multiple cluster can point to same s3 bucket. EMRFS follows s3's consistency model. EMRFS consistency view helps to overcome problem associated with s3's eventual consistency. EMRFS offers to configure retry logic if inconsistency is detected. Metadata in DynamoDB keeps track of s3 objects.
8. Choosing instance types for EMR -
      - MapReduce - General purpose - M3 or M4 (scale horizontally)
      - Machine Learning - P2, C3/C4
      - Spark - R3/R4 (high memory)
      - HBase - I2/D2 (high IOPS)
9. Master Node - low compute. For cluster < 50 nodes,use m3.xlarge or m4.xlarge for master node. for cluster > 50 nodes use m3/m4.2xlarge instance type.
10. AWS best partice - smaller custer with less number of nodes - this needs less maintenance.
11. CloudWatch Metrics for EMR are updated every 5 minutes. These data are retained for 2 weeks.Ganglia is a opensource monitoring option for Hadoop. 
12. EMR cluster can be resized either manually or using auto-scaling. AutoScaling Role should be added at the beginning of cluster creation.
13. Hive is a high level programming language. Hive needs an interpreter. Hive offers JDBC/ODBC connection. Trift Server allows programatic access to Hive. 
14. Tez is a framework to enhance the performance to Hive. Tez is faster than MapReduce. Hive can connect to s3 and DynamoDB. Hive data can be joined with data in DynamoDB table using EMR DynamoDB connector.
15. Presto is a in memory distributed fast SQL query engine. It is faster than Hive. It is sort of data virtualisation application; has connectors for various Hadoop applications.
16. Presto is not a database, requires lot of memory...not good for batch job. It is good for interactive query.
### EMR Security
1. EMR has 2 types of security groups -
- EMR Managed Security Group
- Additional Security Group
When a cluster is launched, 2 security groups are required to be selected; one for the Master and one for Core & Task nodes. User can either select default security group or can select select a different security group. A non-default security group isolates the cluster from other clustrs in the same account. The additional security group allows user to attach additional security rules without touching the EMR Managed Security Group.

2. EMR has 3 types of Roles -
- EMR Role - Allows EMR service to access EC2
- EC2 Instance Profile - Allows EC2 instances in the cluster to access S3, DynamoDB etc.
- Auto Scaling Role - Allows autoscaling service to add or remove nodes

3. Custom Roles allows to achieve more complex requirement - i.e. attach roles to generate KMS keys to encrypt the cluster.
4. EMR cluster can be launched in a private subnet within a VPC. An S3 endpoint will be required to connect to S3 from the EMR cluster; similarly NAT instance will be required to connect to KMS, DynamoDB etc.
5. EMR encryption at rest and in transit -
![EMR_Encryption](https://github.com/SandipSeal/AWS_BigData/blob/master/emr-encryption.png)

### EMR File Formats:

The desired properties of a file format-

- Read Fast
- Write Fast
- Splittable
- Should support various compression
- Should support Schema Evaluation (allow changing schema of the file)

#### Text File (CSV,TSV)
- Good Write performance but slow read performance
- Do not support block complression
- Text files are splittable (based on \n character)
- Limited schema evaluation capability. New fields can be added at the end but existing fields cannot be deleted


#### Sequence File
- Behaviour - Records are stored as key value pair. The value in a record in stored in binary format.
- Good read & write performance
- Supports block level compression
- Splittable
- Limited schema evaluation capability. New fields can be added at the end but existing fields cannot be deleted

#### Avro File
- Behaviour - This is a file format plus serialisation/de-serialisation framework. AVRO uses JSON format for defining data types and serialising data.
- Average read & write performance
- Supports block level compression
- Splittable
- Ideal for schema evaluation and the meta-data of the for the data structure is also stored along with the data

#### RC File (Row Columnar)
- Behavior - A columnar file format that store data in key value pair format. It has much similarity to the Sequence File format.
- Provides faster read but slower write performance
- Provides very good compression ratio; supports block level compression.
- RC files are splittable
- Does not support schema evaluation

#### ORC File (Optimised Row Columnar)
- Behavior - A columnar file format that store data in key value pair format. It has much similarity to the Sequence File format.
- Provides faster read but slower write performance (better than RC)
- Provides very good compression ratio; supports block level compression (better than RC)
- ORC files are splittable at stripe level
- Does not support schema evaluation

#### Parquet File
- Behavior - A columnar file format similar to RC & ORC. Parquet stores nested data structures in flat columnar format.
- Provides faster read but slower write performance
- Supports compression (mostly with snappy codec)
- Parquet files are conditionally splittable
- Limited schema evaluation capability. New fields can be added at the end but existing fields cannot be deleted

### Compression Formats

Compression formats comparison -

![Compression Formats](https://github.com/SandipSeal/AWS_BigData/blob/master/throughput2.png)

Key Highlights
- Gzip is a good choice as compression codec for cold data. Gzip compressed files are not splittable
- Snappy and Gzip are most commonly used compression techniques. Snappy is preferred over Gzip.
- Snappy does not support splitting either.

## AWS Machine Type

1. T2,T3,M4,M5 - General purpose. Webserver etc.
2. C4, C5 - Compute optimised. Batch processing, Machine/Deep Learning
3. R4, R5, X1, Z1 - Memory Optimised. In-memory database, Big data processing and analytics
4. P2,P3,G3,F1 - Accelerated Computing. GPU instances, Machine/Deep Learning
5. H1,I3,D2 - Storage optimised. HDFS, MapReduce, Kafka, Redshift


## AWS Machine Learning

1. Various services on AWS:
- AWS Comprehend - NLP service
- AWS Rekognition - Image/Video Analysis
- AWS Polly - Text to Speech conversion
- AWS SageMaker - Fully Managed ML service on AWS
- Amazon Transcribe - Identifies speech is audio file and transcribe them in text

## AWS CloudSearch

1. Amazon CloudSearch is a fully managed service in the cloud that makes it easy to set up, manage, and scale a search solution for your website or application.
2. With Amazon CloudSearch you can search large collections of data such as web pages, document files, forum posts, or product information. You can quickly add search capabilities without having to become a search expert or worry about hardware provisioning, setup, and maintenance. As your volume of data and traffic fluctuates, Amazon CloudSearch scales to meet your needs.

## AWS Security

### Encryption

1. Encryption in Flight: Achieved through SSL (HTTPS). SSL certificate encrypts the data in flight.
2. Serverside Encryption at Rest: Data is encrypted after being received at the server side. Data is decrypted before being sent.
3. Client Side Encryption: Data will be encrypted by the client and would never be decrypted in the server. Data will be decrypted by a receiving client.This can leverage 'Envelope Encryption'.

### S3 Encryption

- SSE s3: Encryption keys are handled and managed by AWS transparently. User must set the header "X-amz-server-side-encryption:AES256"
- SSE KMS: Encryption keys are handled and managed by KMS. This option provides user control + audit trail. User must set the header "X-amz-server-side-encryption:aws:kms"
- SSE-C:Encryption keys are fully handled and managed by customer outside AWS. s3 does the store the encryption key. User must use HTTPS for SSE-C. Encryption key (data key) must be provided in the HTTP request.
- Client Side Encryption: Client library such as Amazon S3 Encryption Client can be used for this. Data is encrypted before sending to s3. The encryption key is fully managed by user.
- S3 supports both HTTP & HTTPS end-points

### AWS KMS

- KMS should be used for sensitive information
- The CMK used in KMS for encrypting the data key can never be retrieved by the user. The CMK can be rotated for extra security
- Never ever store secrets (password, ID# etc.) in the plain text format.
- If the data size > 4KB then Envelope Encryption should be used.
- Users should have necessary IAM permission to call KMS service to encrypt/decrypt

### Cloud HSM

- AWS provides dedicated encryption hardware
- Encryption key is fully owned and managed by user
- CloudHSM hardware is tamper resistant. This is FIPS 140-3 Level 3 compliant.
- CloudHSM cluster is spread across multiple AZs
- Supports both symmetric and asymmetric encryption

![HSM Vs KMS](https://github.com/SandipSeal/AWS_BigData/blob/master/AWS-KMS_HSM.png)

### Security - Kinesis

#### Kinesis Data Streams
- SSL endpoints using the HTTPS protocol to do encryption in flight
- AWS KMS provides server-side encryption
- For client side-encryption, you must use your own encryption libraries
- Supported Interface VPC Endpoints / Private Link – access privately
- KCL – must get read / write access to DynamoDB table
#### Kinesis Data Firehose:
- Attach IAM roles so it can deliver to S3 / ES / Redshift / Splunk
- Can encrypt the delivery stream with KMS
- Supported Interface VPC Endpoints / Private Link – access privately
#### Kinesis Data Analytics
- Attach IAM role so it can read from Kinesis Data Streams and reference sources and write to an output destination (example Kinesis Data Firehose)

### Security – DynamoDB
- Data is encrypted in transit using TLS (HTTPS)
- DynamoDB can be encrypted at rest
- KMS encryption for base tables and secondary indexes
- Only for new tables
- To migrate un-encrypted table, create new table and copy the data 
- Encryption cannot be disabled once enabled
- Access to tables / API / DAX using IAM
- DynamoDB Streams do not support encryption 
- VPC Endpoint is provided through a Gateway

### Security - RDS
- VPC provides network isolation
- Security Groups control network access to DB Instances
- KMS provides encryption at rest
- SSL provides encryption in-flight
- IAM policies provide protection for the RDS API
- IAM authentication is supported by PostgreSQL and MySQL
- Must manage user permissions within the database itself
- MSSQL Server and Oracle support TDE (Transparent Data Encryption)

### Security - Lambda
- IAM roles attached to each Lambda function
      - Sources
      - Targets
- KMS encryption for secrets
- SSM parameter store for configurations
- CloudWatch Logs
- Deploy in VPC to access private resources

### Security - Glue
- IAM policies for the Glue service
- Configure Glue to only access JDBC through SSL 
- Data Catalog: Encrypted by KMS
- Connection passwords: Encrypted by KMS
- Data written by AWS Glue – Security Configurations: 
      - S3 encryption mode: SSE-S3 or SSE-KMS
      - CloudWatch encryption mode
      - Job bookmark encryption mode

### Security - EMR
- Using Amazon EC2 key pair for SSH credentials
- Attach IAM roles to EC2 instances for:
      1. proper S3 access for EMRFS requests to S3
      2. DynamoDB scans through Hive
- EC2SecurityGroups
      1. One for master node
      2. Another one for cluster node (core node or task node)
- Encrypts data at-rest: EBS encryption, Open Source HDFS Encryption, LUKS + EMRFS for S3
- In-transit encryption: node to node communication, EMRFS, TLS
- Data is encrypted before uploading to S3
- Kerberos authentication (provide authentication from Active Directory)
- Apache Ranger: Centralized Authorization (RBAC – Role Based Access) – setup on external EC2
- https://aws.amazon.com/blogs/big-data/best-practices-for-securing-amazon-emr/


### Security – ElasticSearch Service
- Amazon VPC provides network isolation
- ElasticSearch policy to manage security further
- Data security by encrypting data at-rest using KMS
- Encryption in-transit using SSL
- IAM or Cognito based authentication
- Amazon Cognito allow end-users to log-in to Kibana through enterprise identity providers such as Microsoft Active Directory using SAML

### Security - Redshift
- VPC provides network isolation
- Cluster security groups
- Encryption in flight using the JDBC driver enabled with SSL
- Encryption at rest using KMS or an HSM device (establish a connection). HSM can be on-prem or CloudHSM. Encryption at rest, encrypts the data blocks, system metadata and snapshots. Encryption in RedShist is immutable.
- Supports S3 SSE using default managed key
- Use IAM Roles for Redshift
- To access other AWS Resources (example S3 or KMS)
- Must be referenced in the COPY or UNLOAD command (alternatively paste access key and secret key creds)

### Security - Athena
- IAM policies to control access to the service
- Data is in S3: IAM policies, bucket policies & ACLs
- Encryption of data according to S3 standards: SSE-S3, SSE-KMS, CSE-KMS
- Encryption in transit using TLS between Athena and S3 and JDBC
- Fine grained access using the AWS Glue Catalog

### Security - Quicksight
- Standard edition: 
      -IAM users
      - Email based accounts
- Enterprise edition:
      - Active Directory
      - Federated Login
      - Supports MFA (Multi Factor Authentication)
      - Encryption at rest and in SPICE
- Row Level Security to control which users can see which rows

### AWS STS - Security Token Service
1. This service allows users to get temporary access to AWS services. Users can be granted access till 1 hour using STS
2. STS allows to achieve below functional requirements -
- Cross Account Access
- Federation (using AD credentials)
- Federation with third party providers/Cognito
3. Cross Account Access Steps:
      - Define IAM role for another account
      - Define which accounts can access the IAM role
      - Use AWS STS to retrieve the temporary credentials to access the account and impersonate the IAM roles. This is done using               AssumeRole API
      
### AWS Identity Federation

1. Federation lets users outside of AWS to assume temporary role for accessing AWS resources.
2. These users assume identity provided access role.
3. Federation assumes a form of 3rd party authentication
      - LDAP
      - Microsoft Active Directory (~= SAML)
      - Single Sign On
      - Open ID
      - Cognito
4. Using federation, you don’t need to create IAM users (user management is outside of AWS)

#### SAML Federation (for Enterprise)

![AWS_Resource](https://github.com/SandipSeal/AWS_BigData/blob/master/saml-based-federation.diagram.png)

![AWS_Console](https://github.com/SandipSeal/AWS_BigData/blob/master/saml-based-sso-to-console.diagram.png)

#### Custom Identity Broker (for Enterprise)

- Use only if identity provider is not compatible with SAML 2.0
- The identity broker must determine the appropriate IAM policy

![Custom_Identity](https://github.com/SandipSeal/AWS_BigData/blob/master/enterprise-authentication-with-identity-broker-application.diagram.png)

#### Web Identity Federation (for public applications)

![Web_Identity](https://github.com/SandipSeal/AWS_BigData/blob/master/mobile-app-web-identity-federation.diagram.png)

### IAM Policy Elements: Variables and Tags

1. Below are the examples for IAM policy variables -
      - https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies _variables.html
      - ${aws:username} : to restrict users to tables / buckets
      - ${aws:principaltype} : account, user, federated, or assumed role
      - ${aws:PrincipalTag/department} : to restrict using Tags

2. Below are the examples for federated IAM policy variables -
      - https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies _iam-condition-keys.html#condition-keys-wif
      - ${aws:FederatedProvider} : which IdP was used for the user (Cognito, Amazon..)
      - ${www.amazon.com:user_id} , ${cognito-identity.amazonaws.com:sub} ...
      - ${saml:sub}, ${sts:ExternalId}
      
## AWS Data Visualization

### Amazon QuickSight

1. Cloud based vizualization tool for the business analysts. QuckSight is serverless.
2. QuckSight can connect to several data sources - supports JDBC/ODBC, can connect to xls, CSV, TSV, AWS Athena data as well
3. QuckSight data preparation allows limited ETL capability
4. QuckSight imports the data from the sources into the SPICE engine. SPICE stands for -
- SuperFast, Parallel, In-Memory Processing Engine.
5. SPICE uses columnar data storage. SPICE accelerates query on large datasets.
6. Each user gets 10GB space in SPICE
7. QuickSight can scale out to hundreds of thousands of users
8. QuickSight Anti-Patterns:
- Highly formatted canned reports/dashboards
- Performing complex ETL transformations
9. QuickSight Security:
- Allows Multi-Factor Authentication
- Allows VPC connectivity - add QuickSight IP address range to DB security groups
- Row Level Security
- Private VPC access
10. QuickSight User Management:
- Standard Edition: via IAM or E-Mail sign-up
- Enterprise Edition allows Active Directory (AD) integration, Federated logins, E-Mail sign-up
This version allows encryption at rest
11. QuickSight Machine Learning Insights
- ML-powered anomaly detection: Uses Random Cut Forest algorithm to find out the top contributors to change in metrics
- ML-powered forecasting: Uses Random Cut Forest algorithms to detect seasonality and trends
- Autonaratives: Adds story to data in dashboards
- Suggested Insights: Provides recomendation to use appropriate insight tool(s).
12. QuickSight Visual Types:
- AutoGraph - QuickSight selects the visualization type automatically
- Bar Chart - Comparison/Histogram
- Line Graph/Area Chart - Change over time
- Scatter Plot - Correlation between variables
- Pie Chart - Aggregarion (group by)
- Tree Map - Aggregation (group by). Hierarchial Pie Chart. Area of the rectangle is dependent on the aggregated value
- Pivot Table - Tabular data/intersection of dimensions
- Stories - 
- KPI - Compare fact value to the target value
- Heat Map - Correlation; Each cell colour depends on the value of the measure/KPI variable. Ex - Number of home insurances by country.
- Gauge Chart - How much of capacity being used
- Geospatial Chart - Map visualization
- Word Cloud - Word or Phrase frequency. Size of word/phrase represents the frequency
13. QuickSight supported data sources - https://docs.aws.amazon.com/quicksight/latest/user/supported-data-sources.html
14. Row Level Security - https://docs.aws.amazon.com/quicksight/latest/user/restrict-access-to-a-data-set-using-row-level-security.html#create-data-set-rules-for-row-level-security
15. You can use the join interface in Amazon QuickSight to join tables from one or more data sources. By using Amazon QuickSight to join the data, you can merge disparate data without duplicating the data from different sources to a centralized data store.
The following limitations apply to joins:

- For joins of multiple data sources, the result set is a SPICE data set.

- For joins of multiple data sources, there's no size restriction on the data that you use to create the data set. You should always begin the join with your largest table. Often, this is a fact table. The rest of the tables combined must total less than 1 GB in size.

- You can't join on calculated fields that you created in Amazon QuickSight.

- You can't join on fields that use the geospatial data type.

- You can't use a custom SQL query in a join between multiple data sources. To use custom SQL to join tables from different data sources, create the join before importing to Amazon QuickSight.
16. You can refresh your SPICE data sets at any time. Refreshing imports the data into SPICE again, so the data includes any changes since the last import.

You can refresh SPICE data by taking any of the following approaches:

- You can use the options on the Your Data Sets page.
- You can refresh a data set during data preparation.
- You can schedule refreshes of the data.

For data that is not stored in SPICE, you can do the following:

- To refresh file-based data, you must delete and recreate the data set.
- To refresh data from a database, reopen your data set or the visualization you created.

## Storage Gateway:
1. AWS Storage Gateway connects an on-premises software appliance with cloud-based storage to provide seamless integration with data security features between your on-premises IT environment and the AWS storage infrastructure. You can use the service to store data in the AWS Cloud for scalable and cost-effective storage that helps maintain data security.
2. **File Gateway** – A file gateway supports a file interface into Amazon Simple Storage Service \(Amazon S3\) and combines a service and a virtual software appliance\.
3. **Volume Gateway** – A volume gateway provides cloud\-backed storage volumes that you can mount as Internet Small Computer System Interface \(iSCSI\) devices from your on\-premises application servers\. The gateway supports the following volume configurations:
+ **Cached volumes** – You store your data in Amazon Simple Storage Service \(Amazon S3\) and retain a copy of frequently accessed data subsets locally\. Cached volumes offer a substantial cost savings on primary storage and minimize the need to scale your storage on\-premises\. You also retain low\-latency access to your frequently accessed data\.
+ **Stored volumes** – If you need low\-latency access to your entire dataset, first configure your on\-premises gateway to store all your data locally\. Then asynchronously back up point\-in\-time snapshots of this data to Amazon S3\.
4. **Tape Gateway** – With a tape gateway, you can cost\-effectively and durably archive backup data in GLACIER or DEEP\_ARCHIVE\. A tape gateway provides a virtual tape infrastructure that scales seamlessly with your business needs and eliminates the operational burden of provisioning, scaling, and maintaining a physical tape infrastructure\. 

## Amazon Redshift:

1. Fully managed data warehouse in AWS cloud. This is a petabyte scale, column oriented datastore.
2. Redshift Architecture : Leader Node & Compute Nodes. The EC2 compute nodes are in single AZ. Client tools connect to the Leader Node.The default port is 5439.
3. Leader node parses the SQL and derive the execution plan. Leader Node also stores the metadata (system tables).
4. Each compute node has its own compute and storage. Each compute node can be scaled in/out and up/down. Each compute node consists of Node slices (portion of memory and disk). Number of slices depends on the type of nodes.
5. Dense Compute Node & Dense Storage Nodes. Reserved Instance Pricing is available for Amazon Redshift
6. Amazon s3 is the main source of loading data into Redshift. COPY command example below:
      COPY <Table Name> <s3 bucket/path> CREDENTIALS <credential>
7. COPY command can load data from DynamoDB, EMR, EC2 instance. AWS Data Pipeline can automate the ETL workflow. Also, AWS DMS can be used to migrate data into Redshift. Data can be loaded into Redshift using Kinesis.
8. Data can be exported to s3 using the UNLOAD command.
9. Redshift is a columnar database. It provides efficiency in data storage. Single line insert performs poorly in Redshift.
10. Primary Key and Foreign Key can be created in Redshift but referential integrity is not maintained in the database. 
11. REdshift support 3 dustribution styles -
      Even: Rows are distributed across the slices regardless of the value in a particular column.This is default distribution style.
      Key Distribution: Same keys reside in the same slice. Usefule for large table joins.
      All: Entire Table is copied into each slide. Good for small dimension/lookup tables
12. Redshift stores data in filesystem in the sorted order of sort key. Block-size in Redshift is 1 MB. Zone maps keep track of the min & max value in each block. If the data is not loaded in the order of the sort key then all the blocks will be scanned; vaccum command will be needed to be performed. 
13. 2 types of the sort key - compound & interleaved. With compound sort keys table is sorted by column values listed in the sort key order. Query performance may be degraded if it does include the primary sort column.
14. Interleaved sort key gives equal importance to all the sort columns. Data loading/vacuum operation is slower with interleave sort keys. It is useful for very large tables only. Not good for the table where data is loaded in sort orders.
      - An interleaved sort gives equal weight to each column, or subset of columns, in the sort key. If multiple queries use different         columns for filters, then you can often improve performance for those queries by using an interleaved sort style. When a query           uses restrictive predicates on secondary sort columns, interleaved sorting significantly improves query performance as compared         to compound sorting.
15. Redshift support compression scheme at each column level. Compression scheme can be defined during table creation. AWS recomends automatic compression. Once data is loaded first time into the table, compression is automatically applied by AWS. Compression can be applied manually using analyze command. Analyse commands provide suggestion for compression and after that DDL hasto be updated manually.
16. Table constraints (except NOT NULL) is not physically enforced in Redshift.
17. Data in s3 can be easily loaded into Redshift using simple COPY command. Other than s3, data can be directly loaded from AWS EMR, EC2 instance and DynamoDB. Kinesis, Kinesis Firehose, AWS DMS loads data first to s3 before loading the same into Redshift. 
18. Transfering data to AWS - upload to s3 over internet, Direct Connect, AWS Export/Import, Snowball/Snowmobil. 
19. Splitting a lagre data file into smaller chunks enhances the performance of the COPY command. Number of data files should be equal or multiple of the number of slices. 
20. Large files can be compressed before loading - gzip, lzip, bzip2. File size should be even size as possible. File size after after split should be of size betweeen 1 MB to 1 GB. For optimum parallelism, the ideal size is between 1 MB and 125 MB after compression. The number of files should be a multiple of the number of slices in your cluster. 
21. Manifest File - 
      - Load required files only
      - Load from different s3 buckets
      - Load files with different prefix
      - Ensures data consistency when loaded from s3
manifest is defined in JSON format. 
22. File formats supported by COPY command - CSV, delimited, fixed width, JSON, Avro. 
23. Error checking - STL_LOAD_ERRORS, STL_LOADERROR_DETAIL. These 2 tables can be joined for more detailed info. System tables and views do not use the same consistency model as regular tables. It is important to be aware of this issue when querying them, especially for STV tables and SVV views.
24. UPSERT is not supported in Redshift. Need to use combination of delete & insert or update (by joining target & staging tables)
25. COPY command can load encrypted files in s3. COPY command can load files encrypted using SSE s3, SSE-KMS; also supports client side encryption using client-side symmetric master key. SSE-C, client side encryption using KMS, client side encruption using asymmetric master key is not supported in COPY command. 
26. UNLOAD command exports data to one or more files in s3. Automatically applies SSE-s3 encryption. UNLOAD supports SSE-KMS and client side encryption using customer-managed key (CSE-CMK). UNLOAD does not support SSE-C encryption.
27. Redshift blocks are immutable; i.e. Updates result in a new block and Deleted rows are not removed from disk. VACUUM command recovers the storage space and sorts the data.
28. Variations of VACUUM - FULL, SORT, DELETE ONLY, REINDEX. This operation is I/O intensive; hence should be run during the maintenance window. 
29. Performing DEEP COPY on a very large table is much faster than running VACUUM command. DEEP COPY recreates and populates the table with a bulk insert. VACUUM command is not recomended on a table of size > 700 GB.
30. Redshift snapshots can be automated or manual. Automatic snapshot is taken every 8 hours or every 5 GB of data change. Redshift also allows cross-region snapshot/backup. 
31. If the Redshift cluster is encryted then snapshot will also be encrypted. When Redshift is restored from a snapshot a new cluster is created using same configuration
32. AWS RedShift Cluster Key Concepts - https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes
33. Amazon Redshift Best Practices for Designing Queries - https://docs.aws.amazon.com/redshift/latest/dg/c_designing-queries-best-practices.html
34. RDS PostgreSQL can query data from RedShift using extensions. There are 2 extensions -
      - PostgreSQL fdw (Foreign Data Wrapper) - This extension is very slow for large number of rows.
      - Dblink - This extension pushes all the query complexity to RedShift
      https://aws.amazon.com/blogs/big-data/join-amazon-redshift-and-amazon-rds-postgresql-with-dblink/
35. If your data has a fixed retention period, you can organize your data as a sequence of time-series tables. In such a sequence, each table is identical but contains data for different time ranges.
36. AWS RedShift Blogs (Imp)-
- https://aws.amazon.com/blogs/big-data/run-mixed-workloads-with-amazon-redshift-workload-management/
      
37. Amazon Redshift Advisor Recommendations<a name="advisor-recommendations"></a>
Amazon Redshift Advisor offers recommendations about how to optimize your Amazon Redshift cluster to increase performance and save on operating costs. 

+ [Compress Table Data](#cluster-compression-recommendation)
+ [Compress Amazon S3 File Objects Loaded by COPY](#cluster-compress-s3-recommendation)
+ [Isolate Multiple Active Databases](#isolate-active-dbs-recommendation)
+ [Reallocate Workload Management \(WLM\) Memory](#reallocate-wlm-recommendation)
+ [Skip Compression Analysis During COPY](#skip-compression-analysis-recommendation)
+ [Split Amazon S3 Objects Loaded by COPY](#split-s3-objects-recommendation)
+ [Update Table Statistics](#update-table-statistics-recommendation)
+ [Enable Short Query Acceleration](#enable-sqa-recommendation)
+ [Replace Single\-Column Interleaved Sort Keys](#single-column-interleaved-sort-recommendation)
+ [Alter Distribution Keys on Tables](#alter-diststyle-distkey-recommendation)
