In addition to the Eureka [demo app] (https://github.com/Netflix/eureka/wiki/Running-the-Demo-Application), the following are additional examples.

# Custom Eureka clients
Following REST API documentation, it is easy to write own custom client. It may be useful in scenarios where one-time query is required, and overhead of bringing up the standard official client with all the dependencies is an overkill. One example might be short-lived, batch jobs.
* [Simple ReadOnly client] (https://github.com/Netflix/eureka/wiki/Example-Custom-ReadOnly-client)

# Complete end-to-end applications
Here are the complete end-to-end application using many key components from the Netflix OSS stack:
* [Flux Capacitor] (https://github.com/cfregly/fluxcapacitor)
* [Netflix Recipes-RSS Reader] (https://github.com/Netflix/recipes-rss)