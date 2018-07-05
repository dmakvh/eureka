Hopefully, at this point you have visited [this](https://github.com/Netflix/eureka/wiki/Configuring-Eureka) page to understand how to setup the Eureka server.

The first step to interact with Eureka Server is to initialize the Eureka Client. If you are running in the AWS Cloud, you initialize the following way:

As of release 1.1.153, EurekaModule class is introduced to allow the use of eureka-client with governator/guice. See this [governated example](https://github.com/Netflix/eureka/blob/master/eureka-examples/src/main/java/com/netflix/eureka/ExampleEurekaGovernatedService.java).

Pre release 1.1.153, you can initialize Eureka Client in the following way:
<pre>
<code>
     DiscoveryManager.getInstance().initComponent(
                new CloudInstanceConfig(),
                new DefaultEurekaClientConfig());
</code>
</pre>

If you are running in other data centers, you initialize the following way:

<pre>
<code>
     DiscoveryManager.getInstance().initComponent(
                new MyDataCenterInstanceConfig(),
                new DefaultEurekaClientConfig());
</code>
</pre>

Eureka client looks for _eureka-client.properties_ as explained [here](https://github.com/Netflix/eureka/wiki/Configuring-Eureka#configuration).

## About Instance Statuses

By default, Eureka client starts in **STARTING** state which gives the instance a chance to do application-specific initializations, before it can serve traffic. 

The application then can explicitly put the instance for traffic, by turning the instance status to **UP**.

<pre>
<code>
     ApplicationInfoManager.getInstance().setInstanceStatus(InstanceStatus.UP)
</code>
</pre>
The application can also register health check [callbacks](http://netflix.github.com/eureka/javadoc/eureka-client/index.html) which can change the instance status to **DOWN** optionally.

At Netflix, we also use the **OUT_OF_SERVICE** status, primarily for taking an instance out of traffic. It is used for easy rollback of deployments of new revisions in case of problems. Most applications create a new ASG for a new revision and the traffic gets routed the new ASGs. In the case of problems, rolling back a revision is just a matter of turning off the traffic by setting all instances in the ASG to **OUT_OF_SERVICE**.

## Eureka Client Operations

Eureka client first tries to talk to the Eureka Server in the same zone in the AWS cloud for all operations and if it cannot find the server it fails over to the other zones. 

The application clients can load balance by using the information returned by Eureka clients. Following is an example of the sample application using the information returned by Eureka client to load balance the client.

<pre>
<code>
 InstanceInfo nextServerInfo = DiscoveryManager.getInstance()
                .getDiscoveryClient()
                .getNextServerFromEureka(vipAddress, false);

        Socket s = new Socket();
        int serverPort = nextServerInfo.getPort();
        try {
            s.connect(new InetSocketAddress(nextServerInfo.getHostName(),
                    serverPort));
        } catch (IOException e) {
            System.err.println("Could not connect to the server :"
                    + nextServerInfo.getHostName() + " at port " + serverPort);
        }
</code>
</pre>

If the basic round-robin load balancing is not sufficient for your needs, you can wrap a load balancer on top of the [API/operations](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/discovery/EurekaClient.java) provided here. In AWS cloud, make sure you retry on failures and keep the timeouts low, because there could be scenarios where Eureka server could return instances that no longer exists in the case of outages.

It is important to note that Eureka client cleans up the HTTP connections that have been idle for over 30 seconds that it created for the purpose of server communication. This is because of the AWS firewall restrictions that do not allow traffic on to pass through a connection after a few minutes of idle time.

The Eureka client interacts with the server the following ways.

## Register

Eureka client registers the information about the running instance to the Eureka server. In AWS cloud, the information about an instance is available by accessing the URL _http://169.254.169.254/latest/metadata_. Registration happens on first heartbeat (after 30 seconds).

## Renew

Eureka client needs to renew the lease by sending heartbeats every 30 seconds. The renewal informs the Eureka server that the instance is still alive. If the server hasn't seen a renewal for 90 seconds, it removes the instance out of its registry. It is advisable not to change the renewal interval since the server uses that information to determine if there is a wide spread problem with the client to server communication.

## Fetch Registry

Eureka clients fetches the registry information from the server and caches it locally. After that, the clients use that information to find other services. This information is updated periodically (every 30 seconds) by getting the delta updates between the last fetch cycle and the current one. The delta information is held longer (for about 3 mins) in the server, hence the delta fetches may return the same instances again. The Eureka client automatically handles the duplicate information.

After getting the deltas, Eureka client reconciles the information with the server by comparing the instance counts returned by the server and if the information does not match for some reason, the whole registry information is fetched again. Eureka server caches the compressed payload of the deltas, whole registry and  also per application as well as the uncompressed information of the same. The payload also supports both JSON/XML formats. Eureka client gets the information in compressed JSON format using jersey apache client.  

## Cancel

Eureka client sends a cancel request to Eureka server on shutdown. This removes the instance from the server's instance registry thereby effectively taking the instance out of traffic.

This is done when the Eureka client shuts down and the application should make sure to call the following during its shutdown.

<pre>
<code>
     DiscoveryManager.getInstance().shutdownComponent()
</code>
</pre>

## Time Lag

All operations from Eureka client may take some time to reflect in the Eureka servers and subsequently in other Eureka clients. This is because of the caching of the payload on the eureka server which is refreshed periodically to reflect new information. Eureka clients also fetch deltas periodically. Hence, it may take up to 2 mins for changes to propagate to all Eureka clients.

## Communication mechanism

By default, Eureka clients use [Jersey](http://jersey.java.net/) and Jackson along with JSON payload to communicate with Eureka Server. You can always use a mechanism of your choice by [overriding](http://netflix.github.com/eureka/javadoc/eureka-client/index.html) the default one. Note that [XStream](http://xstream.codehaus.org/) is also part of the dependency graph for some legacy use cases.