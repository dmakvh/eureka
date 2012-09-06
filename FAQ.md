## What happens during network outages between Peers?

In the case of network outages between peers, following things may happen

The heartbeat replications between peers may fail and the server detects this situation and enters into a self-preservation mode protecting the current state.
Registrations may happen in an orphaned server and some clients may reflect new registrations while the others may not.
The situation autocorrects itself after the network connectivity is restored to a stable state. When the peers are able to communicate fine, the registration information is automatically transferred to the servers that do not have them.
The bottom line is, during the network outages, the server tries to be as resilient as possible, but there is a possibility of clients having different views of the servers during that time.

## Why not use HA proxy for load balancing?


In AWS cloud, instances come in and go out of traffic for it's inherent reasons. ASGs could expand the instances or destroy them depending on traffic. The challenge here whether
we use HAProxy or not, is to handle this dynamic nature. Even when you use HAProxy,  it needs to be educated about the instances that come in and go out,  which is exactly what
Eureka does.

One of the reasons why I could think of using proxy in the middle-tier is when you need sticky sessions. If that is a requirement, HAProxy could complement Eureka. But, since most
of the mid-tier services are non-sticky in our scenario, we don't have a reason to go through a proxy there by avoiding a network  hop. Another positive side effect of this is, it makes
our clients fairly resilient to Eureka server outages , since the client has all the information to contact the services it needs to talk to. Going through HA Proxy has a disadvantage in
that, you cannot be resilient to proxy outages.

