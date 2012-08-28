What is Eureka?

Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. Eureka also comes with a built-in load balancer that does basic round-robin load balancing. At Netflix, a much more sophisticated load balancer wraps eureka to provide weighted load balancing based on several factors like traffic, resource usage, error conditions etc to provide superior resiliency.

What is the need for Eureka?

In AWS cloud, because of it's inherent nature, servers come and go. Unlike the traditional load balancers which work with servers with well known ip addresses and hostnames, in AWS, load balancing requires much more sophistication in registering and de-registering servers with load balancer on the fly. Since, AWS does not yet provide a middle tier load balancer, Eureka fills a big gap in the area of mid-tier load balancing.

How different is Eureka from the AWS ELB?

AWS Elastic Load Balancer is a load balancing solution for edge services exposed to end-user web traffic. Eureka fills the need for mid-tier load balancing. While you can theoretically put your middle tier services behind the AWS ELB, you expose your middle-tier services to the outside world and also lose all the usefulness of the AWS security groups.


