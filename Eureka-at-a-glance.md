## What is Eureka?

Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. Eureka also comes with a java-based client component which makes interactions with the service much easier. The client also has a built-in load balancer that does basic round-robin load balancing. At Netflix, a much more sophisticated load balancer wraps eureka to provide weighted load balancing based on several factors like traffic, resource usage, error conditions etc to provide superior resiliency.

## What is the need for Eureka?

In AWS cloud, because of it's inherent nature, servers come and go. Unlike the traditional load balancers which work with servers with well known ip addresses and host names, in AWS, load balancing requires much more sophistication in registering and de-registering servers with load balancer on the fly. Since, AWS does not yet provide a middle tier load balancer, Eureka fills a big gap in the area of mid-tier load balancing.

## How different is Eureka from AWS ELB?

AWS Elastic Load Balancer is a load balancing solution for edge services exposed to end-user web traffic. Eureka fills the need for mid-tier load balancing. While you can theoretically put your mid-tier services behind the AWS ELB, you expose them to the outside world and there by losing all the usefulness of the AWS security groups.

AWS ELB is also a traditional proxy-based load balancing solution whereas with Eureka it is different in that sense since the load balancing happens at the instance/server/host level.The client instances know all the information about which servers it need to talk to. This is a blessing or a curse depending on which way you look at it. If you are looking for a sticky user session based load balancing which the AWS now offers, Eureka does not offer a solution out of the box. In fact, in Netflix, most of the load balancing is not sticky user session based mostly because you can scale better without sticky sessions and hence this model works great.

Another important aspect that differentiates proxy-based load balancing from load balancing using Eureka is that your application can be resilient to the outages of the load balancers in the case of Eureka, since the information of all the servers is cached on the client. Of course, it comes with a small price to pay in terms of memory usage but is well worth it for resiliency.

## How different is Eureka from Route 53?

Route 53 is a naming service which Eureka can provide the same for the mid-tier servers and the similarity stops there. Route 53 is a DNS service which can host your DNS records even for non-AWS data centers. Route 53 can also do latency based routing across AWS regions. Eureka is analogous to internal DNS and has nothing to do with the DNS servers across the world. Eureka is also region-isolated in the sense that it does not know about servers in other AWS regions. It's primary purpose of holding information is for load balancing within a region.

While you can register your mid-tier servers with Route 53 and rely on AWS security groups to protect your servers from public access, your mid-tier server identity is still exposed to the external world. It also comes with the draw back of the traditional DNS based load balancing solutions where the traffic will be routed to servers that no longer exists which is especially not uncommon in the AWS world.

## When should I use Eureka?

You typically run in the AWS cloud and you have a host of middle tier services which you do not want to register with AWS ELB or expose traffic from outside world. You are either looking for a simple round-robin load balancing solution or willing to write your own wrapper around Eureka based on your load balancing need. You do not have the need for sticky sessions and load session data in an external cache such as memcached.

## How does the application client and application server communicate? 

The communication technology could be anything you like.Eureka helps you finding the information of the services you would want to communicate with but does not impose any communication level restrictions. The communication could be on http or https or at the socket level.


## High level architecture
![Eureka High level Architecture](https://github.com/Netflix/eureka/raw/master/images/eureka_architecture.png)

The architecture above depicts how Eureka is deployed at Netflix and this is how you would typically run it. There is **one** eureka cluster per **region** which knows only about instances in its region. There is **one** eureka server per **zone** to handle zone failures.

Services **register** with Eureka and then send **heartbeats** to renew their leases every 30 seconds.If the client cannot renew the lease for a few times, it is taken out of the server registry in about 90 seconds.The registration information and the renewals are replicated to all the eureka nodes in the cluster. The clients from any zone can look up the **registry** information (happens every 30 seconds) to locate their services (which could be in any zone) and make remote calls.

## Non-java services and clients

For services that do not run in java, you can run a side kick to register your service and be found by your clients. REST based endpoints are also exposed for all operations that are supported by the Eureka client. Non-java clients can use the REST end points to query for information about other services.

## Configurability

With Eureka you can add or remove cluster nodes on the fly. You can tune the internal configurations from timeouts to thread pools. Eureka uses [archaius](https://github.com/Netflix/archaius) and if you had a configuration source implementation a lot of these configurations can be tuned dynamically.

## Resilience

Being in the AWS cloud, it is hard to not think about resilience in everything we build. Eureka benefits from this experience, with the resilience built into both the client and the servers.

Eureka clients are built to handle the failure of one or more Eureka servers. Since Eureka clients have the registry cache information in them, they can probably operate reasonably well, even when all of the eureka servers go down.

Eureka Servers are resilient to other eureka peers going down. Even during a network partition between the clients and servers, the servers have built-in resiliency to prevent a large scale outage.

## Multiple Regions

Eureka can be deployed in any AWS region without doing too much work. In fact, you want to deploy it in a non-AWS data center, you can. Eureka clusters between regions do not communicate with one another.

## Monitoring

Eureka uses [servo](https://github.com/Netflix/servo/wiki) to track a lot information in both the client and the server for performance, monitoring and alerting.The data by default is available in the JMX registry.

