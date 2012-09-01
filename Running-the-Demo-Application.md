The demo application comes packaged with the ability to configure,build and run all the components needed for running Eureka

* Eureka Server
* Application Service
* Application Client

For more details on understanding the configuration [this](https://github.com/Netflix/eureka/wiki/Getting-started-with-Eureka) this page.

#Eureka Server Configuration

* Navigate to eureka-server/conf/ and edit the _eureka-client.properties_ and _eureka-client-test.properties_ if needed. (You do not have to edit the eureka-server.properties for the demo unless you are setting up advanced server configurations)
 
* [Build](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server) the application.

*  Copy the WAR artifact to your tomcat deployment directory under _$TOMCAT_HOME/webapps/ _

    <pre><code>
    cp ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war
    </pre></code> 

* Start your tomcat server. Access _**http://localhost:<port>/eureka**_ to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there.

#Eureka Client Configuration for Service

* Navigate to eureka-server/conf/sampleservice and edit the _eureka-client.properties_ and _eureka-client-test.properties_ if needed. 

* Switch to eureka-server directory and run ./runservice.sh

* 
