---
layout: inner
title: Performance Considerations
lead_text: ''
permalink: /performance-considerations
---

## Performance Considerations

# <a name="__RefHeading__770_1654017897"></a><a name="_Toc528406969"></a>Introduction

Apache NiFi is an outstanding tool for moving and manipulating a multitude of data sources. It provides a robust interface for monitoring data as it moves through the configured NiFi system as well as the ability to view data provenance during each step.

While NiFi can be extremely performant in transitioning data from one source to another, it can be tricky to configure for optimal performance. This writeup will attempt to explain some of the basic and “advanced” configuration options and performance considerations in an effort to help developers configure the system for optimal performance.

Note: the vast majority of this information can be found by searching the web for various topics regarding NiFi. The information here attempts to provide a relatively consolidation of many of these sorts of posting found on the web.

# <a name="__RefHeading__772_1654017897"></a><a name="_Toc528406970"></a>Topics for Discussion


**[Introduction](#__RefHeading__770_1654017897)**

**[Topics for Discussion](#__RefHeading__772_1654017897)**

**[NiFi Configuration Files](#__RefHeading__774_1654017897)**

  * **[Bootstrap.conf](#__RefHeading__776_1654017897)**

    * [JVM Memory Settings](#__RefHeading__778_1654017897)

    * [Garbage Collection](#__RefHeading__780_1654017897)

    * [Running Java 8 or Later?](#__RefHeading__782_1654017897)

  * **[nifi.properties](#__RefHeading__784_1654017897)**

**[NiFi Repositories](#__RefHeading__786_1654017897)**

  * **[Flow File Repository](#__RefHeading__788_1654017897)**

  * **[Database Repository](#__RefHeading__790_1654017897)**

  * **[Content Repository](#__RefHeading__792_1654017897)**

  * **[Provenance Repository](#__RefHeading__794_1654017897)**

**[NiFi Clustering](#__RefHeading__796_1654017897)**

  * **[Clustering Considerations](#__RefHeading__798_1654017897)**

**[Tips & Tricks](#__RefHeading__800_1654017897)**

  * **[Avoid Unnecessary File Modifications](#__RefHeading__802_1654017897)**

  * **[Dataflow Optimization](#__RefHeading__804_1654017897)**

  * **[Handling Large Files](#__RefHeading__806_1654017897)**

  * **[Handling Disconnected Nodes](#__RefHeading__808_1654017897)**

**[Figures](#__RefHeading__810_1654017897)**


# <a name="__RefHeading__774_1654017897"></a><a name="_Toc528406971"></a>NiFi Configuration Files

NiFi contains basically two configuration files (`bootstrap.conf` & `nifi.properties`) and they live in the `conf` folder. These files allow you to provide basic settings for the NiFi application itself as well as the JVM environment NiFi runs in.

## <a name="__RefHeading__776_1654017897"></a><a name="_Toc528406972"></a>Bootstrap.conf

As mentioned earlier, the bootstrap.conf file contains various configuration settings allowing you to optimize the JVM environment for NiFi. Based upon the server capabilities (i.e. memory, processing speed, threads available, OS, etc.) you can tweak the JVM settings to get the most out of available system resources.

### <a name="__RefHeading__778_1654017897"></a><a name="_Toc528406973"></a>JVM Memory Settings

<table width="811" border="1" bordercolor="#00000a" cellpadding="8" cellspacing="0"><colgroup><col width="345"> <col width="433"></colgroup>

<tbody>

<tr valign="TOP">

<td width="345">

Argument

</td>

<td width="433">

Description

</td>

</tr>

<tr valign="TOP">

<td width="345">

<font face="Courier New, serif">java.arg.2=-Xms512m</font>

</td>

<td width="433">

Controls the minimum (or starting) amount of memory dedicated to the JVM heap space

</td>

</tr>

<tr valign="TOP">

<td width="345">

<font face="Courier New, serif">java.arg.3=-Xmx512m</font>

</td>

<td width="433">

Controls the maximum amount of memory allowed to be consumed by the JVM

</td>

</tr>

</tbody>

</table>

Keep in mind that the more flow files you anticipate having in flight at any one time will require increasing these settings. Additionally, if you’re incorporating processors into your NiFi flow that are resource intensive, you may also want to consider increasing these values. To evaluate if you have enough memory allocated to the JVM you can open up the System Diagnostics pane and view how much memory is being used at any given time along with how much space your flow files are taking up. Click the “Hamburger” menu at the top right of the NiFi UI and select Summary. Then on the bottom right side of the pane, click system diagnostics.  
![](./images/performance-considerations/m24de6d32.png)

### <a name="__RefHeading__780_1654017897"></a><a name="_Toc528406974"></a>Garbage Collection

<table width="825" border="1" bordercolor="#00000a" cellpadding="8" cellspacing="0"><colgroup><col width="344"> <col width="447"></colgroup>

<tbody>

<tr valign="TOP">

<td width="344">

Argument

</td>

<td width="447">

Description

</td>

</tr>

<tr valign="TOP">

<td width="344">

<font face="Courier New, serif">java.arg.13=-XX:+UseG1GC</font>

</td>

<td width="447">

This setting tells the JVM which Garbage Collection to use during runtime. Recent versions of NiFi current use the configuration above, but older ones may not so it’s best to validate this setting while also considering the version of Java you’re running to ensure whatever setting you choose is compatible. Keep in mind that smaller Java Memory Heap settings may result in more frequent garbage collection being performed.

</td>

</tr>

</tbody>

</table>

### <a name="__RefHeading__782_1654017897"></a><a name="_Toc528406975"></a>Running Java 8 or Later?

Consider adding the following lines to your bootstrap.conf file

<table width="825" border="1" bordercolor="#00000a" cellpadding="8" cellspacing="0"><colgroup><col width="350"> <col width="441"></colgroup>

<tbody>

<tr valign="TOP">

<td width="350">

Argument

</td>

<td width="441">

Description

</td>

</tr>

<tr valign="TOP">

<td width="350">

<font face="Courier New, serif">java.arg.7=-XX:ReservedCodeCacheSize=256m</font>

</td>

<td width="441">

Helps to prevent compiler from switching off when the code cache fills up

</td>

</tr>

<tr valign="TOP">

<td width="350">

<font face="Courier New, serif">java.arg.8=-XX:CodeCacheMinimumFreeSpace=10m</font>

</td>

<td rowspan="2" width="441">

Establishes a boundary for how much of the code cache can be used before flushing of the code cache will occur to prevent it from filling and resulting in the stoppage of the compiler

</td>

</tr>

<tr valign="TOP">

<td width="350">

<font face="Courier New, serif">java.arg.9=-XX:+UseCodeCacheFlushing</font>

</td>

</tr>

</tbody>

</table>

## <a name="__RefHeading__784_1654017897"></a><a name="_Toc528406976"></a>nifi.properties

Most of these settings described below are the default values for an OOTB NiFi instance. You should still validate these settings.

<table width="825" border="1" bordercolor="#00000a" cellpadding="8" cellspacing="0"><colgroup><col width="382"> <col width="409"></colgroup>

<tbody>

<tr valign="TOP">

<td width="382">

Argument

</td>

<td width="409">

Description

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.bored.yield.duration=10 millis</font>

</td>

<td width="409">

Designed to help with CPU utilization by preventing processors using the timer driven scheduling strategy from using excessive CPU when there is no work to do. Smaller values equate to lower latency but higher CPU utilization. Increasing this value will cut down overall CPU utilization.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.ui.autorefresh.interval=30 sec</font>

</td>

<td width="409">

Controls the interval between refreshing the latest statistics, bulletins, and flow revisions in the browser

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.database.directory=./database_repository</font>

</td>

<td width="409">

Recommend moving the location of this repo to outside the root directory of NiFi to simplify upgrading to future NiFi version. See <a href="#_Database_Repository">Database Repository</a> section for additional information.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.flowfile.repository.directory=./flowfile_repository</font>

</td>

<td width="409">

Recommend moving the location of this repo to outside the root directory of NiFi to simplify upgrading to future NiFi version. See <a href="#_Flow_File_Repository">Flowfile Repository</a> section for additional information

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.content.repository.directory.default=./content_repository</font>

</td>

<td width="409">

Recommend moving the location of this repo to outside the root directory of NiFi to simplify upgrading to future NiFi version. See <a href="#_Content_Repository">Content Repository</a> section for additional information.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.provenance.repository.directory.default=./provenance_repository</font>

</td>

<td width="409">

Recommend moving the location of this repo to outside the root directory of NiFi to simplify upgrading to future NiFi version. See <a href="#_Provenance_Repository">Provenance Repository</a> section for additional information.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.queue.swap.threshold=20000</font>

</td>

<td width="409">

This setting sets the default maximum queue size for all queues between NiFi components. Increasing this value will require additional memory be allocated to NiFi since these Flow Files are stored in memory. If a queue exceeds this size, the files are swapped from memory to disk and will later be retrieved when the queue size drops resulting in increased I/O.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.provenance.repository.query.threads=2</font>

</td>

<td width="409">

Adjusts the number of system threads available for searching the Provenance Repository

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.provenance.repository.index.threads=1</font>

</td>

<td width="409">

Adjusts the number of system threads available for indexing the Provenance Repository. If a significant number of Flow Files are being operated upon this setting can become a bottleneck. If you see a warning bulletin stating “The rate of the dataflow is exceeding the provenance recording rate. Slowing down flow to accommodate.” you may need to increase the number of threads available. Remember though that when you increase the number of threads available for one process you reduce the number available for other processes.

</td>

</tr>

<tr valign="TOP">

<td width="382">

<font face="Courier New, serif">nifi.provenance.repository.index.shard.size=500 MB</font>

</td>

<td width="409">

Sets the amount of Java heap space used for Provenance Repository indexing. Larger values will result in better performance and will utilize more Java Heap space.

<em><strong>IMPORTANT</strong>: configured shard size MUST be 50% smaller than the total configured Provenance Repository max storage size (nifi.provenance.repository.max.storage.size)</em>

Only set this value above 500 MB if you have also increased the max storage size above 1 GB.

</td>

</tr>

</tbody>

</table>

# <a name="__RefHeading__786_1654017897"></a><a name="_Toc528406977"></a><a name="_NiFi_Repositories"></a>NiFi Repositories

## <a name="__RefHeading__788_1654017897"></a><a name="_Toc528406978"></a><a name="_Flow_File_Repository"></a>Flow File Repository

This is the most important repository within NiFi. The Flow File repository contain the information for ALL flow files in flight. Each entry contains all the attributes associated with a given flow file as well as a pointer to where the Flow File’s actual content is stored within the Content Repository. It is a “Write-Ahead Log” of the metadata of each Flow File that currently exist within the system (all modifications are written to a log before they are applied). This repository provides the resiliency necessary to handle system restarts and failures.

It should be noted that Flow Files are never “modified”, but rather the original flow file is maintained during each update/modification to it and a copy is created with the updated version. This technique helps to support robust data provenance throughout the system.

Ideally, this repository should be located on a high-performance RAID that is not shared with other high I/O software and should never be located on the same RAID as the Content or Provenance repositories whenever possible. It should ideally use disk that does not compete for I/O with MarkLogic itself.

_Note: if this repository become corrupted or runs out of disk space the state of flow files may become permanently lost!_

## <a name="__RefHeading__790_1654017897"></a><a name="_Toc528406979"></a><a name="_Database_Repository"></a>Database Repository

This repository contains 2 H2 Databases. nifi-flow-audit.h2.db is used by NiFi to keep track of all configuration changes made within the NiFi UI & nifi-user-keys.h2.db which is only used when NiFi has been secured and it contains information about who has logged into NiFi.

## <a name="__RefHeading__792_1654017897"></a><a name="_Toc528406980"></a><a name="_Content_Repository"></a>Content Repository

The content repository is where the content, to include multiple historical versions of the content (if data archiving has been enabled), pointed to by each Flow File is stored. When a Flow File’s content is determined to be no longer needed the content is either deleted or archived (deletion and archival are configured in the NiFi properties file). NiFi documentation strongly recommends this repository be moved to its own hard disk/RAID. Additional performance gains can be obtained by defining multiple Content Repositories per NiFi instance.

To do this, comment out the _nifi.content.repository.directory.default_ setting and then add a new setting for each Content Repository you want to define (i.e. _nifi.content.repository.directory.contentS1R1 = …_, _nifi.content.repository.directory.contentS1R2 = …_, _nifi.content.repository.directory.contentS1R3 = …_).

In this example the _S#R#_ represents the system number and repository number respectively. By splitting the content across multiple disks, you can significantly increase I/O performance while also improving durability in the event of failures.

## <a name="__RefHeading__794_1654017897"></a><a name="_Toc528406981"></a><a name="_Provenance_Repository"></a>Provenance Repository

This repository contains the historical records for each Flow File. This is basically the data lineage from the point NiFi created the Flow File to the point when NiFi finished processing the it, aka Chain of Custody, and contains a record of each Flow File modification. It uses a rolling group of 16 provenance log files (default) to track events and increase throughput. The provenance data is stored in a Lucene index broken into multiple shards to support indexing and search.

Data in this repository is updated frequently and results in a lot of disk I/O. As such, you can significantly increase performance and durability by spitting the repository into multiple repositories. See [Content Repository](#_Content_Repository) for information on how to accomplish this. Additionally, updating the _nifi.provenance.repository.implementation_ setting in the nifi.properties file to use the <font face="Courier New, serif">org.apache.nifi.provenance.WriteAheadProvenanceRepository</font> instead of the default PersistentProvenanceRepository.

When switching to the WriteAheadProvenanceRepository, you may wish to consider additional changes to the nifi.properties as outlined in the [Hortonworks DataFlow Documentation](https://docs.hortonworks.com/HDPDocuments/HDF3/HDF-3.1.1/bk_user-guide/content/system-properties.html).

# <a name="__RefHeading__796_1654017897"></a><a name="_Toc528406982"></a>NiFi Clustering

In large Production deployments, NiFi should be run in a clustered configuration. Doing so effectively multiplies the number of resources available for processing NiFi flows. Therefore, clustering allows NiFi to scale “out.” However, clustering does not provide High Availability, so is purely a scaling approach. If tuning and scaling “up” with more CPU or I/O work, there is no need for the additional complexity of clustering.

Clustering will not have any impact if NiFi is running on a server that is not maxed out and the bottleneck for your process is MarkLogic instead. Clustering is a “scaling” approach to achieve additional throughput, not for achieving higher reliability.

In a clustered environment, each member of the cluster is responsible for processing its own Flow Files and has its own individual [Repositories](#_NiFi_Repositories) (discussed above) while sharing the same Flow File flow configuration.

Clustering in NiFi is accomplished by using ZooKeeper. NiFi comes with and embedded ZooKeeper already included in the NiFi distribution.

*   ZooKeeper: Provides distributed configuration service, synchronization service, and naming registry for large distributed systems. Is also responsible for electing the Primary Node

*   Zero-Master Configuration: Each node performs the same task but on a different set of data. The elected Master determines what nodes can connect to the cluster based upon the node’s configured FlowFile. Each node within the cluster sends a Heartbeat status to the Master node

*   Cluster Coordinator: Responsible for carrying out the tasks to manage which nodes are allowed to join the cluster and provides the most up to date data flow to new nodes joining the cluster

## <a name="__RefHeading__798_1654017897"></a><a name="_Toc528406983"></a>Clustering Considerations

*   When a node within the cluster goes down or becomes disconnected from the cluster, the remaining nodes DO NOT pick up or process the Flow Files from the missing node since each node is responsible for handling its own set of the data. That said, when the failed/disconnected node comes back online and joins the cluster, it will resume processing its own Flow Files (assuming there are no catastrophic failure such as a corrupted Flow File Repository or running out of disk space).

*   High Availability (from the point of Automatic Failover) is not currently natively supported. However, HA could be set up using an external system to monitor nodes within the cluster and then when one node goes down, a backup node could be brought online. As long as the new node coming online references the failed node’s repositories and flow file configuration, it should be able to join the cluster. (note this is a presumption, but has not been validated) See [Figure 3](#_Screen_Shots) for a conceptual diagram of attempting HA.

*   See _Figure_ 1 _- Data Distribution in NiFi Cluster_ in the [Screen Shots](#_Screen_Shots) section for a representation of distributing data across a NiFi clustered environment. In the screen shot, you’ll notice a DistributeLoad processor distributing unique flow files to each of the three HTTPInvoke processors. Each one is responsible for sending data from the Primary node to all other nodes within the cluster (including the Primary node) Note that the DistributeLoad and InvokeHTTP processors only run on the Primary node. The single ListenHTTP processor runs on all three nodes (including the Primary) and receives the data sent from the Primary.

# <a name="__RefHeading__800_1654017897"></a><a name="_Toc528406984"></a>Tips & Tricks

## <a name="__RefHeading__802_1654017897"></a><a name="_Toc528406985"></a>Avoid Unnecessary File Modifications

It is important to understand when your metadata in a FlowFile is changed, vs. when the content referenced by that FlowFile is changed.

If you remember our earlier discussion regarding what Flow Files are comprised of and where their content is stored you’ll see that the actual content is placed on disk in the ContentRepository, with only a pointer to that content residing in the Flow File. If you run a processor that modifies the Flow File’s underlying content, that content must be fetched from disk, updated, copied and then written back to disk. The Flow File’s pointer to the content will be also updated to reference the updated content, requiring a FlowFile disk write. Additionally, additional space is taken up to store the original version of Flow File content as well as the Provenance information.

## <a name="__RefHeading__804_1654017897"></a><a name="_Toc528406986"></a>Dataflow Optimization

Ref: [NiFi/HDF Dataflow Optimization (Part 1)](https://community.hortonworks.com/articles/9782/nifihdf-dataflow-optimization-part-1-of-2.html) & ([Part 2](https://community.hortonworks.com/content/kbentry/9785/nifihdf-dataflow-optimization-part-2-of-2.html))

The links above provide excellent insight into some optimizations that can be done to improve NiFi’s performance. No need to copy that information here. Briefly, look for resource exhaustion (I/O or CPU) if your flow is not running fast, or some processor is backing up. If not (there are ample disk and CPU resources) consider adding more threads. But if resources are exhausted, you must optimize the flows, reduce threads, or cluster. More and better details are in the linked web pages.

## <a name="__RefHeading__806_1654017897"></a><a name="_Toc528406987"></a>Handling Large Files

Large files can take on many forms such as binary video files or text-based files like CSV, JSON, or XML. In the latter case, these files are typically “record-based” such that the single file represents 1 – N individual records. In this case, you should avoid trying to load the entire file into memory and then splitting it into individual flow files. Instead, use a “record-based” processor (i.e. ConvertRecord) that allows the file to be processed using streaming. This will preclude the need to load the entire file into memory. Here is a [blog post](https://bryanbende.com/development/2017/06/20/apache-nifi-records-and-schema-registries) explaining the various Record Streaming processors and how to interact with them.

Additional things to consider are:

*   Ensure individual nodes have enough memory configured to handle the anticipated number, type, and size of files

*   Ensure the flow file and content repositories have enough disk space to accommodate the anticipated quantity and size of files to be processed

## <a name="__RefHeading__808_1654017897"></a><a name="_Toc528406988"></a>Handling Disconnected Nodes

If a node fails/becomes disconnected from the cluster and is unable to rejoin, look in the _nifi-1.x.x/logs_ directory. The nifi-app.log file will log most all NiFi activity including errors. Here are some thing to look for:

*   You may see something indicating the node’s Flow File configuration is out of date. This issue was largely seen with older versions of NiFI (previous to 1.0.0). In this case simply delete or rename the _flow.xml.gz_ file in the _conf_ directory and restart the node. When the node attempts to connect, the Primary Node (i.e. Cluster Coordinator) will see the rejoining node has no Flow File template and will then provide the most current one to that node.

*   Ensure regular heartbeats are being sent to the Cluster Coordinator. If the Cluster Coordinator doesn’t receive regular heartbeats (interval configured in the conf/nifi.properties file) the node sending the heartbeat notifications will become disconnected. The Cluster Coordinator will wait 8x the configured _nifi.cluster.protocol.heartbeat.interval_ before disconnecting a node. This can happen for numerous reasons such as network latency between the node and the Cluster Coordinator or excessive garbage collection. In the latter case, ensure you have configured enough Java Heap Memory for the node.

# <a name="__RefHeading__810_1654017897"></a><a name="_Toc528406989"></a><a name="_Screen_Shots"></a>Figures

_Figure 2 - Data Distribution in NiFi Cluster_

![](./images/performance-considerations/45518d03.png)

_Note: The Decompress HTTP Content processor is only needed if you have configured the PostHTTP processors on the Primary node to perform data compression (Compression Level property)_

_Figure 3 - Conceptual HA Diagram_

![](./images/performance-considerations/4d5a306c.png)

<font color="#404040"><span style="font-style: normal">In the diagram above, conceptually a standby node could be configured to reference the same Repositories as a currently running node as well as referencing the same configurations. When a node fails, the standby node could be brought online referencing all the same configuration and repositories. This, theoretically, could allow for some sort of HA. Note: It is extremely important that only a single node is allowed to run using this configuration. Multiple instances accessing the same repositories and configuration could result in corrupting the repositories.</span></font>
