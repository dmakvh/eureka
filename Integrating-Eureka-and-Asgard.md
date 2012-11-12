[Asgard](https://github.com/Netflix/asgard) the web based cloud deployment tool can be used with Eureka to mitigate problems during a rolling deployment in a AWS [autoscaling group](http://aws.amazon.com/autoscaling/) and for seamlessly switching between old and new deployments.

**Rolling Deployment**

During a rolling deployment, Asgard can check for the health of an instance before propagating the new version to all of the instances in an ASG(Autoscaling group). This is a useful risk mitigation strategy for preventing outages if the new version has problems for some reason. 

To use this feature, eureka clients are required to register a _health check URL_.This can be done in 2 ways

* By specifying a relative health check url path relative to the deployed webapplication given by the property below

<pre>
eureka.healthCheckUrlPath

Example: eureka.healthCheckUrlPath=/myapp/healthCheck.jsp
</pre>

If the secure port is enabled (eureka.securePort.enabled=true), _HTTPS_ is used as a preferred communication protocol, otherwise HTTP is assumed.

* By specifying a full healthcheck URL  given by the property below

<pre>
eureka.healthCheckUrl
eureka.secureHealthCheckUrl

Example:eureka.healthCheckUrl=http://${eureka.hostname}:8080/Priam/REST/healthcheck
Example:eureka.secureHealthCheckUrl=https://${eureka.hostname}:8088/Priam/REST/healthcheck
</pre>

The expression _${eureka.hostname}_ is automatically evaluated by Eureka client to supply the appropriate host name. This option may be needed in the case if the eureka client is servicing as proxy to some other non-java service where the running port is different from the p