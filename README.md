# Apache-HBase-Primer
Apache HBase is an open source NoSQL database based on the wide-column data store model. HBase was initially released in 2008. While many NoSQL databases are available, Apache HBase is the database for the Apache Hadoop ecosystem.
--------------------------------------------------------------------------------------------------------------
##Chapter1 Fundamental Characteristics
1. Pseudo-distributed mode is suitable for small-scale testing while fully-distributed mode is suitable for production.
2. HBase supports auto-sharding, which implies that tables are dynamically split and distributed by the database when they become too large.
3. HBase is based on Hadoop and HDFS, and it provides low latency, random, real-time, read/write access to big data.
4. Most NoSQL databases make use of no SQL at all, but NoSQL does not imply absolutely no SQL is used, because of which NoSQL is also termed as “not only SQL.”
5. HBase does not support transactions.HBase is not eventually consistent but is a strongly consistent at the record level. Strong consistency implies that the latest data is always served but at the cost of increased latency. In contrast, eventual consistency can return out-of-date data.
6. When a region becomes too large, it splits into two at the middle row key into approximately two equal regions. 
7. HBase is designed for batch processing systems optimized for streamed access to large data sets.While HBase supports random read access, HBase is not designed to optimize random access.HBase incurs a random read latency which may be reduced by enabling the Block Cache and increasing the Heap size.

##Chapter2 Apache HBase AND HDFS
1. As a result, the directory structure in HDFS for data files is as follows:
<code>		/hbase/<tablename>/<encoded-regionname>/<column-family>/<filename> </code>
2. Compaction is the process of creating a larger file by merging smaller files. Compaction can become necessary if HBase has scanned too many files to find a result but is not able to find a result. After the number of files scanned exceeds the limit set in hbase.hstore.compaction.max, parameter compaction is performed to merge files to create a larger file.
3. When data is added to HBase, the following sequence is used to store the data:
A. The data is first written to a WAL called HLog.
B. The data is written to an in-memory MemStore.
C. When memory exceeds certain threshold, data is flushed to disk as HFile (also called a StoreFile).
D. HBase merges smaller HFiles into larger HFiles with a process called compaction.
4. The RegionServer contains a set of Regions and is responsible for handling reads and writes. The region is the basic unit of scalability and contains a subset of a table’s data as a contiguous, sorted range of rows stored together. The Master coordinates the HBase cluster, including assigning and balancing the regions. The Master handles all the admin operations including create/delete/modify of a table. The ZooKeeper provides distributed coordination.
5. The HBase Master coordinates the HBase cluster’s administrative operations. At startup, each region is assigned to a RegionServer and the Master may move a region from one RegionServer to the other for load balancing.
6. The client contacts the Master only for creating a table and for modifications and deletions. The cluster can keep serving data even if the Master goes down.For Put and Get operations, clients don’t have to contact the Master and can directly contact the RegionServer responsible for handling the specified row. For a client scan, the client can directly contact the RegionServers responsible for handling the specified set of keys. The client queries the .META. table to identify a RegionServer. The .META. table is a system table used to track the regions. 
7. The .META. table is also a table like the other tables and client has to find from ZooKeeper on which RegionServer the .META. table is located. Prior to HBase 0.96, HBase design was based on a table that contained the META locations, a table called -ROOT-.With HBase 0.96, the -ROOT- table has been removed and the META locations are stored in the ZooKeeper


##Chapter5 Column Family and Column Qualifier
1. A KeyValue consists of the key and a value, with the key being comprised of the row key plus the column family plus the column qualifier plus the timestamp. The value is the data identified by the key. The timestamp represents a particular version.
2. Each row has same column families.Each row can have different column qualifiers within a column family.
3. What makes a HBase table sparse is that each row does not have to include all the column families. Each column family is stored in its own data file. As a result, some data files may not include data for some of the rows if the rows do not store data in those column families.
4. Coordinates for a cell arerow key ➤ column key ➤ version.Physical coordinates for a cell are region directory ➤ column family directory ➤ rowkey ➤ column family name ➤ column qualifier ➤ version
5.Versions are sorted from newest to oldest by sorting the timestamps lexicographically.
##Chapter8 Major Components of a Cluster
1. HBase runs in two modes: standalone and distributed. On a distributed cluster, the Master is typically on the same node as the HDFS NameNode, and the RegionServers are on the same node as a HDFS Datanode, with each RegionServer being collocated with a datanode. 
2. For a small cluster, a ZooKeeper may be collocated with the NameNode (not the datanode), but for a large cluster, the ZooKeeper should run on a separate node. 
3. The Master manages the cluster.RegionServers manage data. 
4. The ZooKeeper bootstraps and coordinates the cluster. 
5. A region has a startKey and a endKey and contains a sorted, contiguous range of rows.
6. HBase has two in-memory structures: MemStore and block cache. While MemStore is for the Write path, the block cache is for the Read path. Reads read block cache first, and if the requested data is not found, the HFile on the disk is read. A block cache is provided for frequently read data and reduces the read latency. Regardless of the number of columns, atomicity is provided at row level.