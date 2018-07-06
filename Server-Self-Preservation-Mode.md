# Self Preservation Mode

Eureka servers will enter self preservation mode if they detect that a larger than expected number of registered clients have terminated their connections in an ungraceful way, and are pending eviction at the same time. This is done to ensure catastrophic network events do not wipe out eureka registry data, and having this be propagated downstream to all clients.

To better understand self preservation, it help to first understand how does eureka clients 'end' their registration lifecycle. The eureka protocol requires clients to execute an explicit unregister action when they are permanently going away. For example, in the provided java client, this is done in the shutdown() method.
any clients that fails 3 consecutive heartbeat renewals is considered to have an unclean termination, and will be evicted by the background eviction process. It is when > 15% of the current registry is in this later state, that self preservation will be enabled.

When in self preservation mode, eureka servers will stop eviction of all instances until either:
1. the number of heartbeat renewals it sees is back above the expected threshold, or
2. self preservation is disabled (see below)

Self preservation is enabled by default, and the default threshold for enabling self preservation is > 15% of the current registry size.

## How to configure self preservation threshold
The self preservation config is defined [here](https://github.com/Netflix/eureka/blob/master/eureka-core/src/main/java/com/netflix/eureka/DefaultEurekaServerConfig.java#L221). To change self preservation threshold in the example, set the property: `eureka.renewalPercentThreshold=[0.0, 1.0]`.

## How to disable self preservation
The self preservation config is defined [here](https://github.com/Netflix/eureka/blob/master/eureka-core/src/main/java/com/netflix/eureka/DefaultEurekaServerConfig.java#L195). To disable self preservation in the example, set the property: `eureka.enableSelfPreservation=false`.

In a production environment, if for some reason your servers have entered self preservation due to a legitimate reason, you can force the servers to be out of self preservation mode by temporarily disabling self preservation via config. We would expect that in these cases, a human operation is evaluating the situation and taking the appropriate actions.