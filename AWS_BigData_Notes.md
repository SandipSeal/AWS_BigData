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
7 A partition key in a data record is a unicode string of max size 256 bytes. The sequence number is unique per partition key in a shard.
