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

#### ResourceManager

+ +Only scheduling "task neutral"
+ Manages YARN container provisioning
+ Negotiates resources with ApplicationMaster
+ Ultimate arbitrator of resources(can recall Containers)
+ Does not provide applications specific status

### YARN Containers

+ A collection of physical resources such as RAM, CPU cores, disks etc.
+ Multiple containers per node, rack(e.g. 1 core, 1GB RAM)
+ Large containers multiple of minimum memory size
+ Assigned by REsource Manager and managed by NodeManager

### NodeManager

+ Per node resource(work) manager
+ Manges containers assigned to it by ResourceManager
+ Releases finished containers back to ResourceManager
+ Monitors node health, container resources, and kills container if it exceeds provisioned limits


### ApplicationMaster

+ Coordinates application execution on the cluster
+ Negotiates with ResourceManager for containers
+ Can use containers for anything
+ Dynamic relationship with ResourceManager, may be asked to return containers
+ Sometimes called "Container 0." negotiated between client and ResourceManager

### JobHistoryServer (MapReduce)

+ Tracks job progress
+ Was part of JobTracker
+ MapReduce sends progress to JHS
+ Information used by Web GUI


### YARN Work Flow Scheduling


Scheduling is the sole responsibility of the ResourceManager, There are three "pluggable" options, Defined ,in yarn-site.xml

YARN Scheduling Options

+ FIFO Scheduler
+ Capacity Scheduler(default)
+ Fair Scheduler

#### Capacity Scheduler

+ One or more queues(groups) with a predetermined fraction of the total capacity
+ Guarantees a minimum amount of resources for each queue
+ Excess capacity given to most starved queues
+ Support big memory applications
+ Best for well known work load flow
+ Default Scheduler in Apache Hadoop V2

#### Fair Scheduler

+ Applications get, on average, an equal share of resources over time
+ Application belongs to a apecific queue
+ Containers are given to the queue with the least amount of allocated resources
+ Supports Container resource specification
+ Best for unknown varied workloads
+ Not in wide use

> Note: Fair Scheduler from hadoop version 1 is being ported to hadoop version 2











