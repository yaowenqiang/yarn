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


### Understand scripted administration

Hadoop YARN Administration Files

+ core-site.xml - System wide properties
+ hdfs-site-xml - Hadoop Parallel File System properties
+ mapred-site.xml - Properties for the YARN MapReduce framework
+ yarn-site.xml - YARN properties
+ capacity-scheduler.xml - Capacity Scheduler properties

A complete list of default properties for all these files:
> http://hadoop.apache.org/docs/current/(look at the bottom left of the page under Configuration.)


#### Adding and Decommissioning YARN Nodes

1. Include YARN nodes file property yarn-site.yml;
    yarn.resourcemanager.nodes.include-path
2. Exclude YARN nodes file property in yarn-site.xml
    yarn.resourcemanager.exclude-path
3. Then run
    # yarn rmadmin -refreshNodes

Separate from HDFS node management, can use hostname or IP address

Notes: The mradmin  from Hadoop version1 funcionality is now replaced with rmadmin

#### Changes to the Capacity Scheduler

Adjust properies in capacity-scheduler.xml, then run
> yarn rmadmin -refreshQueues

Note: Queues cannot be deleted at this point, only addition of new queues is supported and the updated queue configuration must be valid, i.e, queue-capacity at each level should be equal to 100%


#### YARN WebProxy

+ A seperate proxy server in YARN that addresses security issues
+ By default the proxy is run as part of the ResourceManager
+ Configure as stand alone mode by changing the configuration property yarn.web-proxy.address, in yarn-site.xml (e.g hostname:8086)
+ By default an empty string which means it runs on the ResourceManager
+ In a standalone mode, yarn.web-proxy.pricipal and yarn.web-proxy.keytab control the Kerberos principal name and the curresponding keytab for use in secure mode

#### Managing YARN jobs

YARN jobs can be managed using the 'yarn application' command, Options include

+appType <Comma-separated list of application types> Works with --list to filter application based on their type(ALL, NEW, NEW_SAVING,SUBMITTING, ACCEPTED, RUNNING, FINISHED, FAILED, ILLED )
-help       Displays help for all commands
-kill <Application ID>   Kill the application
-list           List applications from the RM< Supports options use of -appTypes to filter application based on application type
-status <Application ID>    Prints the status of the application

Also MapReduce job can also be controlled with the 'mapred job' command

#### Setting Container Memory

Three value in the yarn-site.xml file These settings are as follows:

+ yarn.nodemanager.resource.memory-mb is the amount of memory the NodeManager can use for containers(default 8192MB)
+ yarn.scheduler.minimum-allocation-mb is the smallest container allowed by the ResourceManager. A requested  container smaller then this will result in a allocated container of this size (default 1024MB)
+ yarn.scheduler.maximum-allocation-mb is the largest container allowed by the ResourceManager(default 8192MB)

#### Setting Container Cores

To set the number of cores for containers use the following properties in the yarn-site.xml

+ yarn.scheduler.minimum-allocation-vcores is the minimum cores with which a container can be requested(default 1)
+ yarn.scheduler.maximum-allocation-vcores is the maximum cores with which a container can be requested(default 23)
+ yarn.nodemanager.resource.cpu-vcores is the number of CPU cores that can be allocated for containers from a node (default 8)

#### Setting MapReduce Properties

As a YARN application, MapReduce properties are set in mapred-site.xml. For example, the following properties are for setting some Java arguments and memory size for both the map and reduce containers.
+ mapreduce.map.memory.mb provides larger or smaller resource limit for maps(default 1536M)
+ mapreduce.map.java.opts provides larger or smaller heap-size for child mappers(default -Xmx 1024M)
+ mapreduce.reduce.memory.mb provides larger or smaller resource limit for reducers(default 3072M)
+ mapreduce.reduce.java.opts provides larger or smaller heap-size for child reducers(default -Xmx256M)


### Usnerstanding MapReduce Framework







