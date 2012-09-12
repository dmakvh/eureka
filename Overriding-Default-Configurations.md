Eureka comes with in-built defaults which works for most of the scenarios. In case, you want to override the default configurations, there are 3 types of configurations you might want to look at.

You can extend the following default configuration classes to provide your own configuration value by using any mechanism you would like.

**Important: Please do not use the interfaces, they are just for documentation purposes and is likely to change in the future.**

* [Cloud](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/CloudInstanceConfig.java) or [Datacenter](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/MyDataCenterInstanceConfig.java) Instance Configuration both of which extends [PropertiesInstanceConfig](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/PropertiesInstanceConfig.java)
* [Eureka Client](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/discovery/DefaultEurekaClientConfig.java) Configuration
* [Eureka Server](https://github.com/Netflix/eureka/blob/master/eureka-core/src/main/java/com/netflix/eureka/DefaultEurekaServerConfig.java) Configuration 

Note that the above also serves as the documentation of default configuration values for all of the above configurations available with Eureka.