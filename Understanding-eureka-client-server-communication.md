Hopefully, at this point you have visited [this](https://github.com/Netflix/eureka/wiki/Getting-started-with-Eureka) page to understand how to setup the Eureka server.

The first step to interact with Eureka Server is to initialize the Eureka Client.If you are running in the AWS Cloud, you initialize the following way

<pre>
<code>
     DiscoveryManager.getInstance().initComponent(
                new CloudInstanceConfig(),
                new DefaultEurekaClientConfig());
</code>
</pre>

If you are running in other data centers, you initialize the following way

<pre>
<code>
     DiscoveryManager.getInstance().initComponent(
                new MyDataCenterInstanceConfig(),
                new DefaultEurekaClientConfig());
</code>
</pre>

Eureka client looks for _eureka-client.properties_ as explained [here](https://github.com/Netflix/eureka/wiki/Getting-started-with-Eureka).

This kickstarts the communication process between Eureka client and the server by registering the Eureka Client to the server. Eureka client first tries to talk to the Eureka Server in the same zone in the AWS cloud and if it cannot find the server it fails over to the other zones.

The Eureka client interacts with the server the following ways

## Register

Eureka client registers the information about the running instance to the Eureka server. In AWS Cloud, the information about an instance is available by accessing the URL _http://169.254.169.254/latest/metadata_.

           



