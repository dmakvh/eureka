[Asgard](https://github.com/Netflix/asgard) the web based cloud deployment tool can be used with Eureka to mitigate problems during a rolling deployment in a AWS [auto scaling group](http://aws.amazon.com/autoscaling/) and for seamlessly switching between old and new deployments.

**Rolling Deployment**

During a rolling deployment, Asgard can check for the health of an instance before propagating the new version to all of the instances in an ASG (Auto Scaling group). This is a useful risk mitigation strategy for preventing outages if the new version has problems for some reason. 

To use this feature, eureka clients are required to register a _health check URL_.This can be done in 2 ways

* By specifying a relative health check url path relative to the deployed webapplication given by the property below

<pre>
eureka.healthCheckUrlPath

Example: eureka.healthCheckUrlPath=/myapp/healthCheck.jsp
</pre>

If the secure port is enabled (eureka.securePort.enabled=true), _HTTPS_  is used as a preferred communication protocol, otherwise HTTP is assumed.

* By specifying a full healthcheck URL  given one of the properties below

<pre>
eureka.healthCheckUrl
eureka.secureHealthCheckUrl

Example:eureka.healthCheckUrl=http://${eureka.hostname}:8080/Priam/REST/healthcheck
Example:eureka.secureHealthCheckUrl=https://${eureka.hostname}:8088/Priam/REST/healthcheck
</pre>

The expression _${eureka.hostname}_ is automatically evaluated by Eureka client to supply the appropriate host name.

***Eureka client datacenter***

The Eureka client must also be configured to use the Amazon datacenter for proper Asgard integration to take place.  For applications talking to Eureka through the java API, this is done by initializing DiscoveryClient with an instance of `CloudInstanceConfig` like so:

```
    DiscoveryManager.getInstance().initComponent(
        new CloudInstanceConfig(),
        new DefaultEurekaClientConfig());
```

If you are registering a Karyon application with Eureka, it's datacenter can be set to Amazon by setting the system property `com.netflix.karyon.eureka.datacenter.type` to `Amazon`.

**Other integration points**

Asgard can also link other URLs from eureka for its status and home page URLs when the following properties are specified.

<pre>
eureka.homePageUrl
eureka.homePageUrlPath
eureka.statusPageUrl
eureka.statusPageUrlPath
</pre>

**A note about old/new version deployment**

Another very useful integration point between Eureka and Asgard is for supporting Asgard clusters. Asgard clusters provide a way to run different versions of your deployment and allows switching traffic between them seamlessly in case of problems. This is especially useful in disaster recovery scenarios when the rollback of the entire farm can take quite a long time. Once Asgard is configured to use Eureka, the users do not need to do anything special to get this feature.
