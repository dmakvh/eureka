Eureka comes with in-built defaults which works for most of the scenarios. In case, you want to override the default configurations, there are 3 types of configurations you might want to look at.

You can extend the following default configuration classes to provide your own configuration value by using any mechanism you would like.

**Important: Please do not use the interfaces, they are just for documentation purposes and is likely to change in the future.**

* [Cloud](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/CloudInstanceConfig.java) or [Datacenter](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/MyDataCenterInstanceConfig.java) Instance Configuration both of which extends [PropertiesInstanceConfig](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/PropertiesInstanceConfig.java)
* [Eureka Client](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/discovery/DefaultEurekaClientConfig.java) Configuration
* [Eureka Server](https://github.com/Netflix/eureka/blob/master/eureka-core/src/main/java/com/netflix/eureka/DefaultEurekaServerConfig.java) Configuration 

Note that the above also serves as the documentation of default configuration values for all of the above configurations available with Eureka.

## Adding custom metadata to registration information
Sometimes, you may want to add custom metadata specific to your deployment environment for registration and query. For example, you may want to propagate a custom environment id that should be available for your specific deployment env. Eureka offers the ability to add custom metadata as key:value pairs outside of the standard registration data. You can do this in two ways:

### Accessing metadata
```java
String myValue = instanceInfo.getMetadata().get("myKey");
```

### statically set via configuration:
any config of the form
```eureka.metadata.mykey=myvalue``` will have the k:v pair ```mykey:myvalue``` added to eureka's metadata map.

### dynamically set via code:
To dynamically do this, you will need to first provide your own custom implementation of the [EurekaInstanceConfig](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/EurekaInstanceConfig.java) interface. You can then overload the ```public Map<String, String> getMetadataMap()``` method to return a metadata map that contains the desired metadata values. See [PropertiesInstanceConfig](https://github.com/Netflix/eureka/blob/master/eureka-client/src/main/java/com/netflix/appinfo/PropertiesInstanceConfig.java) for an example implementation that provides the configuration based system above.
