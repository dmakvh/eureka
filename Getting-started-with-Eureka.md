Eureka comes with two components - **Eureka Client** and the **Eureka Server**. Your architecture which is using Eureka will typically have two applications

* **Application Client** which using Eureka to make requests to the Application Service.
* **Application Service** which receives requests from Application Client and sends a response back.

The Application Service will first have to register with Eureka Server to be discovered by Application Client. Registering Application Client with Discovery is optional, but registration happens anyway by default. Both the client and the service uses Eureka Client (java based) to register with Eureka Server.If you are using a non-java based application you need to use REST(todo:attach link) endpoints to register with Eureka.

Once registered with Eureka Server, the Eureka Client by default pulls this registration information every 30 seconds (configurable) to its local cache and can find information about the service or make calls to the remote service.

The setups involve the following

* Eureka Server
* Eureka Client for the client
* Eureka Client for the service

Eureka can be run in both the AWS and the non-AWS environments. Following are the prerequisites.

# Prerequisites

## Eureka Server

* JDK 1.6 or higher 
* Tomcat 6.0.10 or higher

## Eureka Client

* JDK 1.6 or higher

## Eureka Build

* Git version 1.7.11.3 or higher
* Gradle 1.0-milestone-9 or higher

A good place to start for the setups, is the built in sample application that comes with Eureka which demonstrates all of the 3 setups explained above. Please run this to familiarize yourself with the setups before you can do advanced (todo :url) setups with eureka clusters within and outside of AWS.

Here is a step by step instruction of the setups for building and running the Sample Eureka Server, Application Client and Application Service

* Install the latest git (todo:link) and gradle (todo:link) packages.
* Get the Eureka build source from github

<pre><code> 
git remote add --track eureka build git@github.com:Netflix/gradle-template.git  
git fetch build  
git merge build/eureka
</pre></code>

*  Get the Eureka source from github
<pre><code> 
git remote add --track eureka git@github.com:Netflix/eureka.git
git pull
</pre></code> 

* Navigate to eureka-server/conf/ and edit the following files to configure the eureka server. Eureka server also needs its own Eureka Client configuration as with any other application.
<pre><code> 
eureka-client.properties
eureka-client-test.properties
eureka-server.properties 
eureka-server-test.properties
</pre></code> 
  
  The Eureka configuration searches the client related property files in eureka-client.properties in the _classpath_. It also searches for the environment specific properties for different environments like _test_ and _prod_. The search location can be changed by passing in the system properties (Do not pass in the prefix)
<pre><code> 
-Deureka.client.props
-Deureka.server.props
</pre></code> 

The properties in the files explain what they are for. At the minimum the following things need to be configured
<pre><code> 
Application Name (eureka.name)
Application Port (eureka.port)
Virtual HostName (eureka.vipAddress)
Eureka Service Urls
</pre></code> 

* Navigate to _eureka-server/conf/sampleclient _and edit the following files to configure the sample eureka client so that it can register and find information from Eureka Server.
     
* Navigate to _eureka-server/conf/sampleservice_ and edit the following files to configure the sample eureka service so that it can register with Eureka Server and can be found by other clients.
    
* If you are running the client and service on different hosts, you should pull the eureka source on both the hosts and configure sample client and sample service on different hosts.

* Now, build the Eureka Server by executing the following

<pre><code> 
./gradlew -I gradle/netflix-oss.gradle clean build
</pre></code> 

This generates a WAR(web application archive) artifact - _**./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war**_

* This WAR contains all the server configurations you changed. Copy this to the your tomcat deployment directory under _$TOMCAT_HOME/webapps/ _

<pre><code>
cp ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war
<pre><code> 

* Start your tomcat server. Access _**http://localhost:<port>/eureka**_ to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there.

* The build automatically sets up the client and the service with the necessary dependencies. Start the service by calling **eureka-server/runservice.sh**. Wait for the server message that says ``'Service started and ready to process requests..'``. If you do not see that, there is something wrong with your setups. Check your configurations to make sure you have set up the port and vipAddress correctly.

* Now run the **eureka-server/runclient.sh**. The client should now be able to communicate to the server on the configured port. The service should exit after processing the message and sending a response back to the client.

**Eureka! **You are all setup and should be ready to jump to advanced configuration of eureka in the cloud.










