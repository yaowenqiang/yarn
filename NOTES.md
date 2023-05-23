Applications Run Natively IN Hadoop

+ BATCH(MapReduce)
+ INTERACTIVE(Tez)
+ ONLINE(HBase)
+ STREAMING(Storm, S4,..)
+ GRAPH(Graph)
+ in-memory(Spark)
+ HPC MPI(OpenMPI)
+ Other (Search, Weave...)


Apache Hadoop v2  YARN Provides Five New Features:

+ Scale
+ New programming models and services
+ Improved cluster utilization
+ Application agility
+ Reliability



Hadoop 2.2.0 Released Oct 15, 2013


Features:

+ YARN
+ HDFS Federation
+ HDFS Snapshots
+ NFSv3 access to data in HDFS
+ Binary Compatibility for MapReduce applications bwtween Hadoop v1 and Hadoop v2 to ease migration 
+ Performance
+ Support for running Hadoop on Microsoft Windows
+ Integration testing for the entire Apache Hadoop ecosystem at the ASF.


Hadoop 2.x with YARN included in Fedora 20!

> yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples-2.6.0-cdh5.13.0.jar pi 16 1000
> hdfs fsck /
> yum install pdsh

> pdsh -w n[0-2] uptime
> pdsh -w ^all-hosts date # use a host file


# Apache whirr


## YARN Components

+ ResourceManager(~ JobTracker)
+ YARN containers
+ NodeManager(~|~ TaskTracker)
+ ApplicationMaster


### YARN Architecture





