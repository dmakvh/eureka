The demo application comes packaged with the ability to configure,build and run all the components needed for running Eureka

* Eureka Server
* Application Service
* Application Client

For more details on understanding the configuration [this](https://github.com/Netflix/eureka/wiki/Getting-started-with-Eureka) this page.

#About the Demo

The demo will help you set up a Eureka Server listening in a port of your choice. The demo will also help you setup an Application service that would serve requests and an Application Client that will send requests to the service.

The Application Service registers with Eureka Server which the Application client can find and then send the request to the Application Service. The client and server exit gracefully after trading messages.

#Eureka Server Configuration

* Navigate to eureka-server/conf/ and edit the _eureka-client.properties_ and _eureka-client-test.properties_ if needed. (You do not have to edit the eureka-server.properties for the demo unless you are setting up advanced server configurations)
 
* [Build](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server) the application.

* The above build also sets up all the libraries needed for running the demo service and the demo client.

*  Copy the WAR artifact to your tomcat deployment directory under _$TOMCAT_HOME/webapps/ 

    <pre><code>
    cp ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war
    </pre></code> 

* Start your tomcat server. Access _**http://localhost:<port>/eureka**_ to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there.

#Eureka Client Configuration for Application Service

* Navigate to eureka-server/conf/sampleservice and edit the _sample-eureka-service.properties_ if needed.

#Eureka Client Configuration for Application Client

* Navigate to eureka-server/conf/sampleclient and edit the _sample-eureka-client.properties_ if needed.

#Running the Demo

* Switch to eureka-server directory and run ./runservice.sh
* Wait until you see the message _ Service started and ready to process requests._
* Switch to eureka-client directory and run ./runclient.sh
* You should see the message _Received response from server. Communication all fine using Eureka_ 
