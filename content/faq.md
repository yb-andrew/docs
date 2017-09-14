---
date: 2016-03-09T20:08:11+01:00
title: Frequently Asked Questions
weight: 70
---

## Architecture & Concepts

### How can YugaByte DB with both CP and HA at the same time?

### Why is a group of YugaByte nodes called a universe instead of the more commonly used term clusters?

The YugaByte universe packs a lot more functionality that what people think of when referring to a cluster. In fact, in certain deployment choices, the universe subsumes the equivalent of multiple clusters and some of the operational work needed to run these. Here are just a few concrete differences, which made us feel like giving it a different name would help earmark the differences and avoid confusion.

- A YugaByte universe can move into new machines/AZs/Regions/DCs in an online fashion, while these primitives are not associated with a traditional cluster.

- It is very easy to setup multiple async replicas with just a few clicks (in the Enterprise edition). This is built into the universe as a first-class operation with bootstrapping of the remote replica and all the operational aspects of running async replicas being supported natively. In the case of traditional clusters, the source and the async replicas are independent clusters. The user is responsible for maintaining these separate clusters as well as operating the replication logic.

- Failover to async replicas as the primary data and failback once the original datacenter is up and running are both natively supported within a universe.

### Why did YugaByte use RocksDB as a starting point for its DocDB storage engine instead of simply using it as a library?


## Product Editions 

### Is the Community Edition open-source?

### How does the Enterprise Edition differ from the Community Edition?

### How is the Enterprise Edition priced?

## Enterprise Edition 

### What is YugaWare?

YugaWare is the orchestration/UI/Admin console for the YugaByte Enterprise Edition.

### How does the installation process work for YugaByte Enterprise?
YugaWare first needs to be installed on any machine. The next step is to configure YugaWare to work with public and/or private clouds. In the case of public clouds, Yugaware spawns the machines to orchestrate bringing up the data platform. In the case of private clouds, you add the nodes you want to be a part of the data platform into Yugaware. Yugaware would need ssh access into these nodes in order to manage them.

### What are the OS requirements and permissions to run YugaWare, the YugaByte admin console?

Prerequisites for YugaWare are listed [here](/enterprise-edition/deploy/#prerequisites)

### What are the OS requirements and permissions to run the YugaByte data nodes?

Prerequisites for the YugaByte data nodes are listed [here](/enterprise-edition/admin/#prerequisites-for-data-nodes)

### How are the build artifacts packaged and where are they stored?

The admin console software is packaged as a set of docker container images hosted on [Quay.io](https://quay.io/) container registry and managed by [Replicated](https://www.replicated.com/) management tool. Installation of the admin console starts with installing Replicated on a Linux host. Replicated installs the [docker-engine] (https://docs.docker.com/engine/), the docker container runtime, and then pulls its own container images the Replicated.com container registry. YugaWare then becomes a managed application of Replicated, which starts by pulling the YugaWare container images from Quay.io for the very first time. Replicated ensures that YugaWare remains highly available as well as allows for instant upgrades by simply pulling the incremental container images associated with a newer YugaWare release. Note that if the host running the admin console does not have Internet connectivity, then a fully air-gapped installation option is also available.

The data node software is packaged into the YugaWare application. YugaWare distributes and installs the data node software on the hosts identified to run the data nodes. Since it's already packaged into existing artifacts, the data node does not require any Internet connectivity.

### How does the admin console interact with the YugaByte data nodes?

The YugaWare admin console does a password-less ssh to interact with the data nodes. It needs to have the access key file (like a PEM file) uploaded into it via the UI. The setup on each of the data nodes to configure password-less ssh is documented [here](/deploy/#private-cloud-or-on-premises-data-centers).

A REST API is also exposed by the admin console to the end users in addition to the UI as another means of interacting with the data platform.


### Would we have access to the database machines that get spawned in public clouds?

Yes, you would have access to all machines spawned. The machines are spawned by YugaWare. YugaWare runs on your machine in your AWS region/datacenter. If you have configured YugaWare to work with any public cloud like AWS or GCP,  it will spawn YugaByte nodes using your credentials on your behalf. These machines run in your account, but are created and managed by YugaWare on your behalf. You can log on to these machines anytime, and YugaWare will additionally show you some stats graphed into a built in dashboard either per node or per universe.

### How many machines would I need to try out YugaByte against my load?

You would need:  

- One machine to install Yugaware on  

- Minimum as many data nodes as the replication factor. So just one machine for replication factor 1, and 3 machines in case of rf=3  

- A machine to run the load tests on  

Typically you can saturate a database machine (or three in case of replication factor 3) with just one large enough test machine running a synthetic load tester that has a light usage pattern. YugaByte ships some synthetic load-testers with the product which can simulate a few different workloads. For example, one load tester simulates a timeseries/IoT style workload and another does stock-ticker like workload. But if you have a load tester that emulates your planned usage pattern, nothing like it!

### Can we control the properties of the machines Yugaware is spinning up? For instance, the VPC/network to spawn machines into, if it has dedicated IOPS, the tenancy of the machine (to prevent "noisy neighbor" problem), etc?

Yes, you can control what Yugaware is spinning up. For example: 

- You can choose if Yugaware should spawn a new VPC with peering to the VPC on which application servers are running (to isolate the database machines into a separate VPC) AWS, or ask it to re-use an existing VPC.  

- You to choose dedicated IOPs EBS drives on AWS and specify the number of dedicated IOPS you need.  

In general, we are be able to fill the gaps quickly if we are missing some features. But as a catch all, Yugaware will allow creating these machines out of band and import these as "on-premise" install.  


## Comparisons

### How is YugaByte different than the standard Apache Cassandra?

### How is YugaByte different than the standard Redis?

### How does YugaByte compare against other NoSQL databases such as Apache HBase and MongoDB?

### Why would I not use a mixture of Redis cluster and a sharded SQL solution?

#### Development and operational complexity

Sharding has to be implemented at two tiers. 
Scale out (expanding/shrinking the cluster) has to be re-implemented twice - once at the Redis layer and again for the sharded SQL layer. You need to also worry about resharding the data in the Redis layer.
The application has to be aware of two tiers and move data between then, deal with consistency issues, etc. It has to write to Redis and Cockroach, read from Redis, deal with staleness of data, etc.

#### Higher cost

Typically not all the data needs to be cached, and the cache has to adapt to the query pattern. Most people deploying Redis this way end up caching all the data.
Some queries need to be answered with low latency from the cache, and others are longer running queries that should not pollute the cache. This is hard to achieve if two systems deal with different access patterns for the same data.

#### Cross-DC administration complexity

- With sync or async replicas - you need to solve all the failover and failback issues at to layers.

- All the latency/consistency/throughput tracking needs to be done at two levels, which makes the app deployment architecture very complicated.


## Apache CQL

### Do INSERTs do “upserts” by default? How do I insert data only if it is absent?

By default, inserts overwrite data on primary key collisions. So INSERTs do an upsert. This an intended CQL feature. In order to insert data only if the primary key is not already present,  add a clause "IF NOT EXISTS" to the INSERT statement. This will cause the INSERT fail if the row exists.

Here is an example from CQL:

```sql
INSERT INTO mycompany.users (id, lastname, firstname) 
   VALUES (100, ‘Smith’, ‘John’) 
IF NOT EXISTS;
```

### Can I have collection data types in the partition key? Will I be able to do partial matches on that collection data type?

Yes, you can have collection data types as primary keys as long as they are marked FROZEN. Collection types that are marked `FROZEN` do not support partial matches.

### What is the difference between a `COUNTER` type and `INTEGER` types?

Unlike Apache Cassandra, YugaByte COUNTER type is almost the same as INTEGER types. There is no need of lightweight transactions requiring 4 round trips to perform increments in YugaByte - these are efficiently performed with just one round trip.


## Redis


