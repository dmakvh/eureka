The demo application comes packaged with the ability to configure,build and run all the components needed for running Eureka

* Eureka Server
* Application Service
* Application Client

For more details on understanding the configurations, visit [this](https://github.com/Netflix/eureka/wiki/Configuring-Eureka) page.

# About the Demo

The demo will help you set up a Eureka Server listening in a port of your choice. It will also help you setup an Application service that would serve requests and an Application Client that will send requests to the service.

The Application Service registers with the Eureka Server and the Application client can find and then send the request to the Application Service. The client and server exit gracefully after trading messages.

# Note about the Demo Server

The demo server is configured to be easy to set up for the demo use case, and is NOT configured for proper production usage. The server configurations to understand how to properly configure the eureka servers.

# Eureka Server Configuration

* Navigate to eureka-server/conf/ and edit the _eureka-client.properties_ and _eureka-client-test.properties_ if needed. (You do not have to edit the eureka-server.properties for the demo unless you are setting up advanced server configurations)
 
* [Build](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server) the application.

* The above build also sets up all the libraries needed for running the demo service and the demo client.

* Copy the WAR artifact to your tomcat deployment directory under _$TOMCAT_HOME/webapps/ 

    <pre><code>
    cp ./eureka-server/build/libs/eureka-server-XXX-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war
    </pre></code> 

* Start your tomcat server. Access _**http://localhost:8080/eureka**_ to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there.

# Eureka Client Configuration for Application Service

* Navigate to eureka-examples/conf and edit the _sample-eureka-service.properties_ if needed.

# Eureka Client Configuration for Application Client

* Navigate to eureka-examples/conf and edit the _sample-eureka-client.properties_ if needed.

# Running the Demo

See the examples readme https://github.com/Netflix/eureka/tree/master/eureka-examples