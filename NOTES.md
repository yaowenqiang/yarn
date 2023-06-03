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


#### MapReduce Compatibility

+ Primary goal with Version 2 was MapReduce Compatibility
+ MapReduce functionality is now a YARN framework
+ ApplicationMaster is required to locate data blocks for processing and then request container on these nodes
+ ApplicationMaster handles failed tasks and requests new containers to re-run them
+ Map and Reduce tasks are now created as needed b the ApplicationMaster providing a much better cluster utilization
+ Need to add the parallel shuffle as an auxiliary service
+ Now has JobHistoryServer to track job progress


#### Enabling ApplicationMaster Restarts

ApplicationMaster failures in YARN have the capability to be restarted a specified number of times,They also have the capability to recover completed tasks after restart. To enable ApplicationMaster restarts do the following:

+ Inside the yarn-site.xml, you can tune the property yarn.resourcemanager.am.max-retries. The default is 2
+ Inside the mapred-site.xml you can more directly turn how many times a MapReduce ApplicationMaster should restart with the property mapreduce.am.max-attempts, the default is 2


### Calculating the MapReduce Capacity of a Node

There are 8 important parameters for calculating a node's capacity:

mapred-site.xml

mapreduce.map.memory.mb and mapreduce.reduce.memory.mb - memory hard-limit for the mapper or reducer task
mapreduce.map.java.opts and mapreduce.reduce.java.opts - The heapsize of jvm -Xmx for the mapper or reducer task. This value should always be lower than mapreduce.[map]reduce].memory.mb

yarn-site.xml

yarn.schedulre.minmum-allocation-mb - The smallest container yarn will allow
yarn.schedulre.maximum-allocation-mb - The largest container yarn will allow
yarn.nodemanager.resource.memory-mb - The amout of physical memory on the compute node for containers. it is important that this isn't the total RAM on the node
yarn.nodemanager.vmem-pmem-ratio - The amount of virtual memory each container is allowed. This is calculated by containerMemoryRequest *  vmem-pmem-ratio



| Property | Value | 
           | - |
mapreduce.map.memory.mb | 1536
mapreduce.reduce.memory.mb | 2560
mapreduce.map.java.opts | -Xmx1024m
mapreduce.reduce.java.opts | -Xmx2048m
yarn.scheduler.minimum-allocation-mb | 512
yarn.scheduler.maximum-allocation-mb | 4096
yarn.nodemanager.resource.memory-mb | 36864
yarn.nodemanager.vmem-pmem-ratio | 2.1



Calculating the MapReduce Capacity of a Node

+ With a virtual memory ratio of 2.1 (the default value), each map can have up to 2335.6MB of RAM or a reducer can have 5376MB of virtual ram.
+ This means that our compute node configured for 36GB(75.6GB virtual) of container space can support up to 23 maps or 14 reducers or any combination of mappers and reducers allowed by the available reosurces on the node.

#### Changes to the suffle Service

+ The parallel MapReduce shuffle is now an Auxiliary Service in the NodeManager
+ Started on NodeManager as a Netty Web server
+ ApplicationMaster makes use of shuffle-service through the map and reduce containers
+ Option to do encrypted shuffle


#### binary Compability of org.apache.hadoop.mapred APIs

for the vast majority of users who use the org.apache.hadoop.mapred APIs, MapReduce on YARN ensures full binary compatibility. these existing applications can run on YARN directly without recompilation. You can use jars of your existing application that codes against mapred APIs  and use bin/hadoop to submit them directly to YARN.


#### Source Compatibility of org.apache.hadoop.mapreduce APIs

It was difficult to ensure full binary combination with org.apache.hadoop.mapreduce APIs. Existing application using mapreduce APIs are source Compatibility and can run on YARN with no changes. with simple recompilation against MRv2 jars that are shipped with Hadoop 2 and/or with minor updates.


#### Compatibility of Command-line scripts

+ Must of the command line scripts from Hadoop 1.x should just work
+ The mradmin functionality is now replace with rmadmin because there is not version 1 JobTracker and TaskTracker
+ If mradmin commands are executed they will produce warning messages and will not work.
+ There is no binary or source Compatibility for mradmin in Hadoop version 2 with YARN.


## Understand how to use Distributed Shell

> yarn org.apache.hadoop.yarn.applications.distributedshell.Client  -jar /usr/lib/hadoop-yarn/hadoop-yarn-applications-distributedshell-2.6.0-cdh5.13.0.jar  --shell_command uptime





