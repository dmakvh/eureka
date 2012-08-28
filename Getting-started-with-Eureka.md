Eureka comes with two components - **Eureka Client** and the **Eureka Server**. Your architecture which is using Eureka will typically have two applications

* **Application Client** which using Eureka to make requests to the Application Service.
* **Application Service** which receives requests from Application Client and sends a response back.

The Application Service will first have to register with Eureka Server to be discovered by Application Client. Registering Application Client with Discovery is optional, but registration happens anyway by default. Both the client and the service uses Eureka Client (java based) to register with Eureka Server.If you are using a non-java based application you need to use REST(todo:attach link) endpoints to register with Eureka.

Once registered with Eureka Server, the Eureka Client by default pulls this registration information every 30 seconds (configurable) to its local cache and can find information about the service or make calls to the remote service.

The setups involve the following

* Eureka Server
* Eureka Client for the client
* Eureka Client for the service

Eureka can be run in both the AWS and the non-AWS environments.A good place to start for the setups, is the built in sample application that comes with Eureka which demonstrates all of the 3 setups explained above. Please run this to familiarize yourself with the setups before you can do advanced (todo :url) setups with eureka clusters within and outside of AWS.

# PreRequisites

## Eureka Server

* JDK 1.6 or higher 
* Tomcat 6.0.10 or higher

## Eureka Client

* JDK 1.6 or higher

## Eureka Build

* Git version 1.7.11.3 or higher
* Gradle 1.0-milestone-9 or higher

Here is a step by step instruction of the setups for running the Sample Eureka Server, Application Client and Application Service

* Install the latest git (todo:link) and gradle (todo:link) packages.
* Get the Eureka build source from github

   `` git remote add --track eureka build git@github.com:Netflix/gradle-template.git  
      git fetch build  
      git merge build/eureka``

*  Get the Eureka source from github

    ``git remote add --track eureka git@github.com:Netflix/eureka.git
    git pull``

* Navigate to eureka-server/conf/ and edit the following files to configure the server
     
     1)eureka-client.properties -This is for configuring the Eureka Client which lives in Eureka Server as well.You should also edit the -test and -prod variations (these files are picked up based on the system property pass into the server -Deureka.environment)

     2) eureka-server.properties - This is for configuration server specific properties. There are also the -test and -prod.properties which you do not need editing for the sample server

For more details of additional configuration options for Eureka client and Eureka Server, please refer <url here>

* Navigate to eureka-server/conf/sampleclient and edit the following files to configure the sample eureka client
     
       sample-eureka-client.properties
     
* Navigate to eureka-server/conf/sampleservice and edit the following files to configure the sample eureka service
     
      sample-eureka-service.properties

* If you are running the client and service on different hosts, you should pull the eureka source on both the hosts and configure the steps (5) and (6) on these different hosts.

* Now, build the Eureka Server by executing the following

./gradlew -I gradle/netflix-oss.gradle clean build

This generates a WAR(web application archive) artifact - ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war

* This WAR contains all the server configurations you changed. Copy this to the your tomcat deployment directory under $TOMCAT_HOME/webapps/ 

cp ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war

* Start your tomcat server. Access http://localhost:<port>/eureka to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there

* The build automatically sets up the client and the service with the necessary dependencies. Start the service by calling eureka-server/runservice.sh. Wait for the server message that says 'Service started and ready to process requests..'. If you do not see that, there is something wrong with your setups. Check your configurations to make sure you have set up the port and vipAddress correctly.

* Now run the eureka-server/runclient.sh. The client should now be able to communicate to the server on the configured port. The service should exit after processing the message and sending a response back to the client.

Eureka! You are all setup and should be ready to jump to advanced configuration of eureka in the cloud.










