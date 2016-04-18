Make sure you have visited [this](https://github.com/Netflix/eureka/wiki/Configuring-Eureka-in-AWS-Cloud) page to understand about Eureka cluster configurations.

Eureka clients tries to talk to Eureka Server in the same zone. If there are problems talking with the server or if the server does not exist in the same zone, the clients fail over to the servers in the other zones.

Once the server starts receiving traffic, all of the [operations](https://github.com/Netflix/eureka/wiki/Understanding-eureka-client-server-communication) that is performed on the server is replicated to all of the peer nodes that the server knows about. If an operation fails for some reason, the information is reconciled on the next heartbeat that also gets replicated between servers.

When the Eureka server comes up, it tries to get all of the instance registry information from a neighboring node. If there is a problem getting the information from a node, the server tries all of the peers before it gives up. If the server is able to successfully get all of the instances, it sets the renewal threshold that it should be receiving based on that information. If any time, the renewals falls below the percent configured for that value (below 85% within 15 mins), the server stops expiring instances to protect the current instance registry information.

In Netflix, the above safeguard is called as **self-preservation** mode and is primarily used as a protection in scenarios where there is a network partition between a group of clients and the Eureka Server. In these scenarios, the server tries to protect the information it already has. There may be scenarios in case of a mass outage that this may cause the clients to get the instances that do not exist anymore. The clients must make sure they are resilient to eureka server returning an instance that is non-existent or un-responsive. The best protection in these scenarios is to timeout quickly and try other servers.

In the case, where the server is not able get the registry information from the neighboring node, it waits for a few minutes (5 mins) so that the clients can register their information. The server tries hard not to provide partial information to the clients there by skewing traffic only to a group of instances and causing capacity issues. 

Eureka servers communicate with one another using the same mechanism used between the Eureka client and the server as described [here](https://github.com/Netflix/eureka/wiki/Understanding-eureka-client-server-communication).

Also worth noting is that there are several [configurations](https://github.com/Netflix/eureka/blob/master/eureka-core/src/main/java/com/netflix/eureka/EurekaServerConfig.java) that can be tuned on the server including the communication between the servers if needed.

## What happens during network outages between Peers?

In the case of network outages between peers, following things may happen

* The heartbeat replications between peers may fail and the server detects this situation and enters into a self-preservation mode protecting the current state.
* Registrations may happen in an orphaned server and some clients may reflect new registrations while the others may not.
*  The situation autocorrects itself after the network connectivity is restored to a stable state. When the peers are able to communicate fine, the registration information is automatically transferred to the servers that do not have them.

The bottom line is, during the network outages, the server tries to be as resilient as possible, but there is a possibility of clients having different views of the servers during that time.
  