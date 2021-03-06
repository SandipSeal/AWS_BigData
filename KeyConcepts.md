# KPL Key Concepts
PDF
Kindle
RSS
The following sections contain concepts and terminology necessary to understand and benefit from the Kinesis Producer Library (KPL).

Topics

Records
Batching
Aggregation
Collection
Records
In this guide, we distinguish between KPL user records and Kinesis Data Streams records. When we use the term record without a qualifier, we refer to a KPL user record. When we refer to a Kinesis Data Streams record, we explicitly say Kinesis Data Streams record.

A KPL user record is a blob of data that has particular meaning to the user. Examples include a JSON blob representing a UI event on a website, or a log entry from a web server.

A Kinesis Data Streams record is an instance of the Record data structure defined by the Kinesis Data Streams service API. It contains a partition key, sequence number, and a blob of data.

## Batching
Batching refers to performing a single action on multiple items instead of repeatedly performing the action on each individual item.

In this context, the "item" is a record, and the action is sending it to Kinesis Data Streams. In a non-batching situation, you would place each record in a separate Kinesis Data Streams record and make one HTTP request to send it to Kinesis Data Streams. With batching, each HTTP request can carry multiple records instead of just one.

The KPL supports two types of batching:

Aggregation – Storing multiple records within a single Kinesis Data Streams record.

Collection – Using the API operation PutRecords to send multiple Kinesis Data Streams records to one or more shards in your Kinesis data stream.

The two types of KPL batching are designed to coexist and can be turned on or off independently of one another. By default, both are turned on.

### Aggregation
Aggregation refers to the storage of multiple records in a Kinesis Data Streams record. Aggregation allows customers to increase the number of records sent per API call, which effectively increases producer throughput.

Kinesis Data Streams shards support up to 1,000 Kinesis Data Streams records per second, or 1 MB throughput. The Kinesis Data Streams records per second limit binds customers with records smaller than 1 KB. Record aggregation allows customers to combine multiple records into a single Kinesis Data Streams record. This allows customers to improve their per shard throughput.

Consider the case of one shard in region us-east-1 that is currently running at a constant rate of 1,000 records per second, with records that are 512 bytes each. With KPL aggregation, you can pack 1,000 records into only 10 Kinesis Data Streams records, reducing the RPS to 10 (at 50 KB each).

### Collection
Collection refers to batching multiple Kinesis Data Streams records and sending them in a single HTTP request with a call to the API operation PutRecords, instead of sending each Kinesis Data Streams record in its own HTTP request.

This increases throughput compared to using no collection because it reduces the overhead of making many separate HTTP requests. In fact, PutRecords itself was specifically designed for this purpose.

Collection differs from aggregation in that it is working with groups of Kinesis Data Streams records. The Kinesis Data Streams records being collected can still contain multiple records from the user. The relationship can be visualized as such:

```
record 0 --|
record 1   |        [ Aggregation ]
    ...    |--> Amazon Kinesis record 0 --|
    ...    |                              |
record A --|                              |
                                          |
    ...                   ...             |
                                          |
record K --|                              |
record L   |                              |      [ Collection ]
    ...    |--> Amazon Kinesis record C --|--> PutRecords Request
    ...    |                              |
record S --|                              |
                                          |
    ...                   ...             |
                                          |
record AA--|                              |
record BB  |                              |
    ...    |--> Amazon Kinesis record M --|
    ...    |
record ZZ--|
```


#### Node Type Details<a name="rs-node-type-info"></a>

 The following tables summarize the node specifications for each node type and size\. The headings in the tables have these meanings: 
+ *vCPU* is the number of virtual CPUs for each node\.
+ *RAM* is the amount of memory in gibibytes \(GiB\) for each node\.
+ *Slices per Node* is the number of slices into which a compute node is partitioned\.

  The number of slices per node might change if the cluster is resized using elastic resize\.
+ *Storage* is the capacity and type of storage for each node\.
+  *Node Range* is the minimum and maximum number of nodes that Amazon Redshift supports for the node type and size\. 
**Note**  
You might be restricted to fewer nodes depending on the quota that is applied to your AWS account in the selected AWS Region\. 
+ *Total Capacity* is the total storage capacity for the cluster if you deploy the maximum number of nodes that is specified in the node range\.

For 16 TB or larger amounts of data, we recommend that you consider RA3 nodes as the default choice\. For workloads that need the fastest performance, consider using DC2 nodes\.


# Redshift

https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes
