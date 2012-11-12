[Asgard](https://github.com/Netflix/asgard) the web based cloud deployment tool can be used with Eureka to mitigate problems during a rolling deployment in a AWS [autoscaling group](http://aws.amazon.com/autoscaling/) and for seamlessly switching between old and new deployments.

**Rolling Deployment**

During a rolling deployment, Asgard can check for the health of an instance before propagating the new version to all of the instances in an ASG(Autoscaling group). This is a useful risk mitigation strategy for preventing outages if the new version has problems for some reason. 

To use this feature, eureka clients are required to register a _health check URL_.This can be done in 2 ways

* By specifying a relative health check url path given by the property below

<pre>
eureka.healthCheckUrlPath
</pre>

<pre>
eureka.healthCheckUrl
eureka.secureHealthCheckUrl
</pre>