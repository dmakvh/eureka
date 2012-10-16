Read the document [here](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance) to understand the concepts of the setup better.

Eureka comes with two components - **Eureka Client** and the **Eureka Server**. Your architecture which is using Eureka will typically have two applications

* **Application Client** which uses Eureka Client to make requests to the Application Service.
* **Application Service** which receives requests from Application Client and sends a response back.

The setups involve the following

* Eureka Server
* Eureka Client for the application client
* Eureka Client for the application service

Eureka can be run in both the AWS and the non-AWS environments.

**If you are running in the cloud environment, please pass in the java commandline property -Deureka.datacenter=cloud so that the Eureka Client/Server knows to initialize the information specific to AWS cloud.**

# Configuring Eureka Client

## Prerequisites

* JDK 1.6 or higher

You have the following choices to get the Eureka client binaries

* You can download the Eureka Client binary by using this URL "http://search.maven.org/#search%7Cga%7C1%7Ceureka-client"

* You can add the eureka client as a maven dependency
```xml
 <dependency>
  <groupId>com.netflix.eureka</groupId>
  <artifactId>eureka-client</artifactId>
  <version>1.1.16</version>
 </dependency>
```
* You can build the client as specified [here](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server).

## Configuration

The easiest way to configure Eureka client is by using the property files. By default, Eureka client searches for property files _eureka-client.properties_ in the _classpath_. It further searches for environment specific overrides in the environment specific properties files. The environment is typically _test_ or _prod_ and is supplied by a _-Deureka.environment_ java commandline switch to the eureka client (without the _.properties_ suffix). Accordingly, the client also searches for _eureka-client-{test,prod}.properties._

You can take a look at the examples [here](https://github.com/Netflix/eureka/tree/master/eureka-server/conf)  for default configurations. You can copy these configurations and edit for your need and place them in your class path. If you want to change the name of the properties file for some reason you can do so by specifying _-Deureka.client.props=<myprops>_  (without suffix) in the java commandline switch, where _<myprops>_ is the name of the property file to search for for.

The properties in the files explain what they are for. At the minimum the following things need to be configured
    <pre><code> 
    Application Name (eureka.name)
    Application Port (eureka.port)
    Virtual HostName (eureka.vipAddress)
    Eureka Service Urls (eureka.serviceUrls)
   </pre></code> 

For more advanced configurations, check out the options available in the following links.
http://netflix.github.com/eureka/javadoc/eureka-client/com/netflix/appinfo/EurekaInstanceConfig.html
http://netflix.github.com/eureka/javadoc/eureka-client/com/netflix/discovery/EurekaClientConfig.html

# Configuring Eureka Server

## Prerequisites

* JDK 1.6 or higher 
* Tomcat 6.0.10 or higher

With Eureka server, you have the following choices to get the binaries

*  You can build a WAR archive from the sources as specified [here](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server).

*  You can download the WAR archive from mavencentral by using this URL  
   "http://search.maven.org/#search%7Cga%7C1%7Ceureka-server" 

## Configuration

Eureka Server has two sets of configurations

* Eureka Client configuration as explained above.
* Eureka Server configuration.

The easiest way to configure Eureka Server is by using property files similar to the Eureka Client above. First, configure the Eureka client that is running with the server as specified above. Eureka server itself fires up a Eureka Client that it uses to find other Eureka Servers. Therefore, you need to first configure the Eureka Client for the Eureka Server as you would do with any other clients that connect to the Eureka service. The Eureka Server will use it's Eureka Client configuration to identify peer eureka server that have the same name (ie) eureka.name

After configuring the Eureka Client, you may need to configure the Eureka Server if you are running in AWS.Eureka server by default searches for property file _eureka-server.properties_ in the _classpath_. It further searches for environment specific overrides in the environment specific properties files. The environment is typically _test_ or _prod_ and is supplied by a _-Deureka.environment_ java commandline switch to the eureka server (without the _.properties_ suffix). Accordingly the server also searches for _eureka-server-{test,prod}.properties._

For AWS environments, you may want to specify the following properties, primarily used for binding the EIPs.
    <pre><code> 
   eureka.awsAccessId=
  eureka.awsSecretKey=
   </pre></code> 

[This](https://github.com/Netflix/eureka/wiki/Configuring-Eureka-in-AWS-Cloud) document explains the AWS-specific eureka server configuration in detail.

If you are [building](https://github.com/Netflix/eureka/wiki/Building-Eureka-Client-and-Server) the WAR archive, you can edit the files under _eureka-server/conf_ in place and the build takes care of placing the properties files under WEB-INF/classes before creating the archive.

If you are [downloading]("http://search.maven.org/#search%7Cga%7C1%7Ceureka-server" ) the archive from maven, then you can merge in the edited property files under WEB-INF/classes yourself.

For more advanced server configurations, refer to the options available [here](http://netflix.github.com/eureka/javadoc/eureka-core/com/netflix/eureka/EurekaServerConfig.html).

[Running](https://github.com/Netflix/eureka/wiki/Running-the-Demo-Application) the demo application may help you to understand the configurations better.

Check [this](https://github.com/Netflix/eureka/wiki/Configuring-Eureka-in-AWS-Cloud) page for additional configurations required for AWS cloud.