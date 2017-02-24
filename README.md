# Apache-HBase-Primer
Apache HBase is an open source NoSQL database based on the wide-column data store model. HBase was initially released in 2008. While many NoSQL databases are available, Apache HBase is the database for the Apache Hadoop ecosystem.
--------------------------------------------------------------------------------------------------------------
Chapter1 Fundamental Characteristics
1.Pseudo-distributed mode is suitable for small-scale testing while fully-distributed mode is suitable for production.
2.HBase supports auto-sharding, which implies that tables are dynamically split and distributed by the database when they become too large.
3.HBase is based on Hadoop and HDFS, and it provides low latency, random, real-time, read/write access to big data.
4.Most NoSQL databases make use of no SQL at all, but NoSQL does not imply absolutely no SQL is used, because of which NoSQL is also termed as “not only SQL.”
5.HBase does not support transactions.HBase is not eventually consistent but is a strongly consistent at the record level. Strong consistency implies that the latest data is always served but at the cost of increased latency. In contrast, eventual consistency can return out-of-date data.
6.When a region becomes too large, it splits into two at the middle row key into approximately two equal regions. 
7.HBase is designed for batch processing systems optimized for streamed access to large data sets.While HBase supports random read access, HBase is not designed to optimize random access.HBase incurs a random read latency which may be reduced by enabling the Block Cache and increasing the Heap size.

Chapter2 Apache HBase AND HDFS
1.As a result, the directory structure in HDFS for data files is as follows:
		/hbase/<tablename>/<encoded-regionname>/<column-family>/<filename>
2.Compaction is the process of creating a larger file by merging smaller files. Compaction can become necessary if HBase has scanned too many files to find a result but is not able to find a result. After the number of files scanned exceeds the limit set in hbase.hstore.compaction.max, parameter compaction is performed to merge files to create a larger file.
3.When data is added to HBase, the following sequence is used to store the data:
A. The data is first written to a WAL called HLog.
B. The data is written to an in-memory MemStore.
C. When memory exceeds certain threshold, data is flushed to disk as HFile (also called a StoreFile).
D. HBase merges smaller HFiles into larger HFiles with a process called compaction.
4.The RegionServer contains a set of Regions and is responsible for handling reads and writes. The region is the basic unit of scalability and contains a subset of a table’s data as a contiguous, sorted range of rows stored together. The Master coordinates the HBase cluster, including assigning and balancing the regions. The Master handles all the admin operations including create/delete/modify of a table. The ZooKeeper provides distributed coordination.
