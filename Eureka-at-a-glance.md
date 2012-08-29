## What is Eureka?

Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. Eureka also comes with a built-in load balancer that does basic round-robin load balancing. At Netflix, a much more sophisticated load balancer wraps eureka to provide weighted load balancing based on several factors like traffic, resource usage, error conditions etc to provide superior resiliency.

## What is the need for Eureka?

In AWS cloud, because of it's inherent nature, servers come and go. Unlike the traditional load balancers which work with servers with well known ip addresses and host names, in AWS, load balancing requires much more sophistication in registering and de-registering servers with load balancer on the fly. Since, AWS does not yet provide a middle tier load balancer, Eureka fills a big gap in the area of mid-tier load balancing.

## How different is Eureka from AWS ELB?

AWS Elastic Load Balancer is a load balancing solution for edge services exposed to end-user web traffic. Eureka fills the need for mid-tier load balancing. While you can theoretically put your mid-tier services behind the AWS ELB, you expose them to the outside world and there by losing all the usefulness of the AWS security groups.

AWS ELB is also a traditional proxy-based load balancing solution whereas with Eureka it is different in that sense since the load balancing happens at the instance/server/host level.The client instances know all the information about which servers it need to talk to. This is a blessing or a curse depending on which way you look at it. If you are looking for a sticky user session based load balancing which the AWS now offers, Eureka does not offer a solution out of the box. In fact, in Netflix, most of the load balancing is not sticky user session based mostly because you can scale better without sticky sessions and hence this model works great.

Another important aspect that differentiates proxy-based load balancing from Eureka based load balancing is that your application can be resilient to the outages of the load balancers in the case of Eureka since the information of all the servers is cached on the client. Of course, it comes with a small price to pay in terms of memory usage but is well worth it for resiliency.

## How different is Eureka from Route 53?

Route 53 is a naming service which Eureka can provide the same for the mid-tier servers and the similarity stops there. Route 53 is a DNS service which can host your DNS records even for non-AWS data centers. Route 53 can also do latency based routing across AWS regions. Eureka is analogous to internal DNS and has nothing to do with the DNS servers across the world. Eureka is also region-isolated in the sense that it does not data from other AWS regions. It's primary purpose of holding information is for load balancing within a region.

While you can register your mid-tier servers with Route 53 and rely on AWS security groups to protect your servers from public access, your mid-tier server identity is still exposed to the external world. It also comes with the draw back of the traditional DNS based load balancing solutions where the traffic will be routed to servers that no longer exists which is especially not uncommon in the AWS world.

Eureka - High level architecture

![Eureka High level Architecture](https://github.com/Netflix/eureka/raw/master/images/eureka_architecture.png)

[!https://github.com/Netflix/eureka/raw/master/images/eureka_architecture.png(Eureka Architecture)!]

Eureka - Configurability
Eureka - Resilience
Eureka in multi region
Eureka - Monitoring