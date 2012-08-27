Eureka comes with two components - Eureka Client and the Eureka Server. Your architecture which is using Eureka will typically have two applications

1) Application Client which using Eureka to make requests to the Application Service.
2) Application Service which receives requests from Application Client and sends a response back.

The Application Service will first have to register with Eureka Server to be discovered by Application Client. Registering Application Client with Discovery is optional, but registration happens anyway by default. Both the client and the service uses Eureka Client (java based) to register with Eureka Server.If you are using a non-java based application you need to use REST(todo:attach link) endpoints to register with Eureka.

Once registered with Eureka Server, the Eureka Client by default pulls this registration information every 30 seconds (configurable) to its local cache and can find information about the service or make calls to the remote service.

Hence, the setups involve setting up the following

1) Eureka Server
2) Eureka Client for the client
3) Eureka Client for the service

PreRequisites

Eureka Server

1) JDK 1.6 or higher 
2) Tomcat 6.0.10 or higher

Eureka Client

1) JDK 1.6 or higher

Build

1) git version 1.7.11.3 or higher
2) Gradle 1.0-milestone-9 or higher

A good place to start for the setups is the built in sample application that comes with Eureka which demonstrates all of the 3 setups explained above. Please run this to familiarize yourself with the setups before you can do advanced (todo :url) setups with eureka clusters and in the cloud.

Here is a step by step instruction of the setups.

1) Install the latest git (todo:link) and gradle (todo:link) packages.
2) Get the Eureka build source from github

git remote add --track eureka build git@github.com:Netflix/gradle-template.git
git fetch build
git merge build/eureka

3) Get the Eureka source from github

git remote add --track eureka git@github.com:Netflix/eureka.git
git pull

4) Navigate to eureka-server/conf/ and edit the following files to configure the server
     
     1) eureka-client.properties -This is for configuring the Eureka Client which lives in Eureka Server as well.You should also edit the -test and -prod variations (these files are picked up based on the system property pass into the server -Deureka.environment)

     2) eureka-server.properties - This is for configuration server specific properties. There are also the -test and -prod.properties which you do not need editing for the sample server

   



./gradlew -I gradle/netflix-oss.gradle clean build








