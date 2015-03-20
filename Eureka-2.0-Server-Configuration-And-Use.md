## Prerequisites
* JDK 1.7

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

**This common config to identify all write servers is needed by all server types.**

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
Write servers have the following responsibilities:
* accepting registration requests from eureka registration clients
* accepting interest discover requests from eureka interest clients and server ChangeNotifications
* replicate local registration data to peer write servers

### Building and Running the Write Server (for now)
```
git clone https://github.com/Netflix/eureka.git
cd eureka/
git checkout 2.x
cd eureka2-write-server/
../gradlew run
```
### Configuring the Write Server
For each of the responsibilities listed above, a separate port needs to be exposed. These are governed by the properties
* eureka.services.registration.port
* eureka.services.discovery.port
* eureka.services.replication.port

Change the ports specified in the [configuration file](../blob/2.x/eureka2-write-server/src/main/resources/eureka-write-server.properties) if you need the ports to be different from the defaults for each specific write server.

## Read Servers

Write servers have the following responsibility:
* accepting interest discover requests from eureka interest clients and server ChangeNotifications

### Building and Running the Read Server (for now)
```
git clone https://github.com/Netflix/eureka.git
cd eureka/
git checkout 2.x
cd eureka2-read-server/
../gradlew run
```
### Configuring the Read Server
The interest protocol port is governed by the property:
* eureka.services.discovery.port

Change the port specified in the [configuration file](../blob/2.x/eureka2-read-server/src/main/resources/eureka-read-server.properties) if you need the port to be different from the defaults for each specific read server.

## The Embedded Cluster
An embedded server is available from eureka2-testkit that can run multiple server types within the same JVM, for testing purposes.

### Building and Running the Embedded Cluster (for now)
```
git clone https://github.com/Netflix/eureka.git
cd eureka/
git checkout 2.x
cd eureka2-testkit/
../gradlew runCluster
```
### Configuring the Embedded Cluster
See available args in the testkit [build.gradle](../blob/2.x/eureka2-testkit/build.gradle) for task runCluster.
