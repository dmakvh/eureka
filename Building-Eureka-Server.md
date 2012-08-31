* Install the latest [git](http://git-scm.com/book/en/Getting-Started-Installing-Git) and [gradle](http://gradle.org/installation) packages.

*  Get the Eureka source from github
   <pre><code> 
    git remote add --track master origin git@github.com:Netflix/eureka.git
    git pull
   </pre></code> 

* Eureka server has Eureka client built in and as with any other application relies on the client to be configured.

 By default, the Eureka server searches for property files eureka-client.properties in the classpath. It further searches for environment specific overrides in the environment specific properties files. The environment is typically "test" or "prod" and is supplied by a "-Deureka.environment" java commandline switch to the eureka client (without the .properties suffix). Accordingly the client also searches for eureka-client-{test,prod}.properties.

Additionally, the same searching mechanism is used for Eureka Server by searching for eureka-server-{test,prod}.properties.

Navigate to eureka-server/conf/ and edit the following files to configure the eureka server. Eureka server also needs its own Eureka Client configuration as with any other application.
    <pre><code> 
    eureka-client.properties
    eureka-client-test.properties
    eureka-server.properties 
    eureka-server-test.properties
    </pre></code> 
  
  The Eureka configuration searches the client related property files in eureka-client.properties in the    
   _classpath_. It also searches for the environment specific properties specified by <pre><code>-Deureka.environment</pre></code> for different environments like _test_ and _prod_. 

   The properties in the files explain what they are for. At the minimum the following things need to be     
   configured
    <pre><code> 
    Application Name (eureka.name)
    Application Port (eureka.port)
    Virtual HostName (eureka.vipAddress)
    Eureka Service Urls (eureka.serviceUrls)
   </pre></code> 

 
* Now, build the Eureka Server by executing the following

    <pre><code> 
    ./gradlew -I gradle/netflix-oss.gradle clean build
    </pre></code> 

  This generates a WAR(web application archive) artifact - _**./eureka-server/build/libs/eureka-server-  
  <version>-SNAPSHOT.war**_

* This WAR contains all the server configurations you changed. Copy this to the your tomcat deployment directory under _$TOMCAT_HOME/webapps/ _

    <pre><code>
    cp ./eureka-server/build/libs/eureka-server-<version>-SNAPSHOT.war $TOMCAT_HOME/webapps/eureka.war
    </pre></code> 

* Start your tomcat server. Access _**http://localhost:<port>/eureka**_ to verify the information there. Your server's eureka client should register itself in 30 seconds and you should see that information there.
