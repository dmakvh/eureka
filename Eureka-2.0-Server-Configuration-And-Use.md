## Prerequisites
* JDK 1.7

You can get the Eureka 2.0 binaries from the following sources. Always try to get the latest release as tend to have more fixes.
* [Bintray](https://bintray.com/netflixoss/maven/eureka/view)
* [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.netflix.eureka%22)

## Types of Servers
Eureka 2.0 uses two main types of servers by default, write servers and read servers. An optional bridge module is available that can be configured to be a component of a write server that allows bridge of data from a currently deployed eureka 1.0 system to the 2.0 system.

## Common Server Configuration
The combination of resolverType and serverList defines the write server cluster.
* ResolverType can either be fixed (read straight from the serverList) or dns (the serverList name is taken as a dns entry and expanded);
* ServerList is a comma delimited list with each entry in the format name:registrationPort:interestPort:replicationPort.
```
eureka.common.writeCluster.resolverType=fixed
eureka.common.writeCluster.serverList=["localhost:12102:12103:12104","localhost:12112:12113:12114"]
```

Each server will also need configuration to self identify.
* appName - the server's appName in its own InstanceInfo record
* vipAddress - the server's vipAddress in its own InstanceInfo record
* dataCenterInfo.type - Basic or AWS
```
eureka.instanceInfo.appName=eureka-write-cluster
eureka.instanceInfo.vipAddress=eureka-write-cluster
eureka.dataCenterInfo.type=Basic
```

## Write Servers
Artifact
* com.netflix.eureka:eureka2-write-server

Write servers have the following responsibilities:
* accepting registration requests from eureka registration clients
* accepting interest discover requests from eureka interest clients
* replicate local registration data to peer write servers

### Building the Write Server

### Configuring the Write Server
For each of the responsibilities listed above, a separate port needs to be exposed.
See the ]example configuration](../blob/2.x/eureka2-write-server/src/main/resources/eureka-write-server.properties)

## Read Servers
Artifact
* com.netflix.eureka:eureka2-read-server


### Building the Read Server


### Configuring the Read Server


## Bridge Servers

### Building the Bridge Server


### Configuring the Bridge Server


## The Embedded Cluster
