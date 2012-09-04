Make sure you have visited [this](https://github.com/Netflix/eureka/wiki/Configuring-Eureka-in-AWS-Cloud) page to understand about Eureka cluster configurations.

When the Eureka server comes up, it tries to get all of the instance registry information from a neighboring node. If there is a problem getting the information from a node, the server tries all of the peers before it gives up. If the server is able to successfully get all of the instances, it sets the renewal threshold that it should be receiving based on that information. If any time, the renewals falls below the percent configured for that value (default 85% within 15 mins), the server stops expiring instances to protect the current instance registry information.

If the server is not able get the list of instances, by default, it waits for a few minutes (5 mins default) to ensure that it can register all of the instances on the fly. 

Once the server is started, all of the [operations](https://github.com/Netflix/eureka/wiki/Understanding-eureka-client-server-communication) that is performed on the server is replicated to all of the peer nodes that the server knows about. If an operation fails for some reason, the information is reconciled on the next heartbeat that gets replicated between servers.

