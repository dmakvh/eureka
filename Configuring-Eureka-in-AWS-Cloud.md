In AWS Cloud, the instances come and go.This means you cannot identify eureka servers with a standard hostname or an IP Address. But, since Eureka Servers are the ones that help you identify other services with changing hostnames, you need a standard set of well identifiable addresses for Eureka Servers.

That is where AWS EC2 Elastic IP addresses come in handy. [This](http://aws.amazon.com/articles/1346) is a must read if you have not heard about Elastic IP addresses before.

The first step is to obtain one elastic ip per eureka server you are going to run with your setup.Ideally, you would run Eureka with the architecture specified [here](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance) page. So, you need one elastic ip for every server in the cluster. Once you configure your Eureka Server with the list of elastic ip address, Eureka server deals with the hassle of finding the elastic ip that is unused and binds it to itself during the start up. 

You normally have one [ASG](http://aws.amazon.com/autoscaling/) for a Eureka cluster within an AWS Region and the instance launches configured to spread equally across all zones. This means, you would at least launch one Eureka Server per zone for redundancy and zone failures. Whenever an instance gets killed, the ASG launches a new Eureka server mostly in the zone that got killed. The new server will pick a free Elastic IP from that zone and binds itself there. For clients that are accessing Eureka Servers, this is transparent and business as usual.

You can configure Elastic IPs in 2 ways depending on the level of flexibility you need. At Netflix, we would like to add new zones or new eureka servers transparently and hence we use the DNS model to allocate the new EIPs and the both the clients and servers find them on the fly. A simpler model would be to define them in the Eureka configuration files, but the drawback is, it gets baked into the [AMI](https://aws.amazon.com/amis/) and distributed to probably 100's of instances that need to talk to each other and adding or removing zones can be extremely cumbersome, because you need to deploy the new AMI with changed configurations to all the clients.

## Configuring EIPs using property files

The first information you would configure in Eureka Servers is the availability zones for a region. This is done in _eureka-client_ properties (or in _eureka-client-test.properties_ or _eureka-client-prod.properties_ if you want environment overrides)

In the following example, a region _us-east-1_ is specified to have 3 availability zone _us-east-1c,us-east-1d, us-east-1e_.
<pre>
<code>
eureka.us-east-1.availabilityZones=us-east-1c,us-east-1d,us-east-1e
</code>
</pre>

The next information you configure is the service urls for each zone where Eureka is listening for requests. Multiple eureka servers for a zone can be configured by providing a comma-delimited list.
<pre>
<code>
eureka.serviceUrl.us-east-1c=http://ec2-552-627-568-165.compute-1.amazonaws.com:7001/discovery/v2/,http://ec2-168-101-182-134.compute-1.amazonaws.com:7001/discovery/v2/
eureka.serviceUrl.us-east-1d=http://ec2-552-627-568-170.compute-1.amazonaws.com:7001/discovery/v2/
eureka.serviceUrl.us-east-1e=http://ec2-50-179-285-592.compute-1.amazonaws.com:7001/discovery/v2/
</code>
</pre>


<a name="assigneip">## Assigning EIPs using Service Urls</a>

So, why are we defining URLs when we are supposed to assign EIPs to servers?Any 2 instances which wants to communicate with one another normally uses a public hostname so that the AWS security groups honor the security restrictions.Eureka servers communicate with one another using these URLs and each URL contains a public hostname ( _ec2-552-627-568-165.compute-1.amazonaws.com_)  which is derived from an elastic ip (552.627.568.165). 

Eureka server finds an EIP based on which zone it is launched. It then tries to find an unused EIP from that zone and then binds that EIP to itself during the startup.

How does Eureka find unused EIPs? It uses the Eureka client to find the list of peer instances and see what EIPS they are bound with and picks the one that is not bound.It prefers to find the EIP assigned to its zone , so that the Eureka clients of the all the other instances in the zone can talk to Eureka server that are co-located in the same zone. If the Eureka server cannot find any EIPS free for its zone, it tries the EIPs assigned from other zones.If all of them are bound, then the Eureka server starts up and waits for an EIP to get free and tries every 5 mins to bind the EIP.


