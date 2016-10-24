## Peer Configuration Overview
Eureka servers needs to be configured such that each server know all other peers. See below for examples of how to do this for both the DNS method, and the property based method.

In addition, eureka server replication can be batched to improve efficiency. To enable batched replication, set `eureka. shouldBatchReplication=true`. See DefaultEurekaServerConfig.java for more details.

## AWS Configuration Overview
In AWS Cloud, instances come and go. This means you cannot identify Eureka servers with a standard hostname or an IP Address. But, since Eureka Servers are the ones that help you identify other services with changing hostnames, you need a standard set of well identifiable addresses for Eureka Servers.

That is where AWS EC2 Elastic IP addresses come in handy. [This](http://aws.amazon.com/articles/1346) is a must read if you have not heard about Elastic IP addresses before.

The first step is to obtain one Elastic IP per Eureka server you are going to run with your setup. Ideally, you would run Eureka with the architecture specified [here](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance). So, you need one Elastic IP for every server in the cluster. Once you configure your Eureka Server with the list of elastic ip address, Eureka server deals with the hassle of finding the elastic ip that is unused and binds it to itself during the start up. 

You normally have one [ASG](http://aws.amazon.com/autoscaling/) for a Eureka cluster within an AWS Region and the instance launches configured to spread equally across all zones. This means, you would at least launch one Eureka Server per zone for redundancy and zone failures. Whenever an instance gets killed, the ASG will launch a new Eureka server most likely in the zone that was vacated. The new server will pick a free Elastic IP from that zone and bind itself there. For clients that are accessing Eureka Servers, this is transparent and business as usual as the eureka clients automatically failover to other servers and then reconnect again when the server comes back up.

You can configure Elastic IPs in 2 ways depending on the level of flexibility you need. At Netflix, we would like to add new zones or new Eureka servers transparently and hence we use the DNS model to allocate the new EIPs and the both the clients and servers find them on the fly. A simpler model would be to define them in the Eureka configuration files, but the drawback is, they get baked into the [AMI](https://aws.amazon.com/amis/) and distributed to probably 1000's of instances that need to talk to each other and adding or removing zones can be extremely cumbersome, because you need to deploy the new AMI with changed configurations to all the clients.

## Configuring EIPs using property files

The first information you would configure in Eureka Servers is the availability zones for a region. This is done in _eureka-client_ properties (or in _eureka-client-test.properties_ or _eureka-client-prod.properties_ if you want environment overrides)

In the following example, a region _us-east-1_ is specified to have 3 availability zone _us-east-1c,us-east-1d, us-east-1e_.
<pre>
<code>
eureka.us-east-1.availabilityZones=us-east-1c,us-east-1d,us-east-1e
</code>
</pre>

Next, you configure is the service urls for each zone where Eureka is listening for requests. Multiple eureka servers for a zone can be configured by providing a comma-delimited list.
<pre>
<code>
eureka.serviceUrl.us-east-1c=http://ec2-552-627-568-165.compute-1.amazonaws.com:7001/eureka/v2/,http://ec2-368-101-182-134.compute-1.amazonaws.com:7001/eureka/v2/
eureka.serviceUrl.us-east-1d=http://ec2-552-627-568-170.compute-1.amazonaws.com:7001/eureka/v2/
eureka.serviceUrl.us-east-1e=http://ec2-500-179-285-592.compute-1.amazonaws.com:7001/eureka/v2/
</code>
</pre>

The same configuration is then included with Eureka clients that register with Eureka Service and also with Eureka client that want to find the services from Eureka Server.

## Configuring EIPs using DNS

If you are looking for flexibility, you should configure Eureka service URLs using DNS.

You first configure a DNS name for region that can be used to find the list of available zones. Since, using  DNS you can find only one CNAME for a DNS name, we use the TXT records to find a list DNS names.

For instance, following is a DNS TXT record created in the DNS server that lists the set of available DNS names for a zone.

<pre>
<code>
txt.us-east-1.mydomaintest.netflix.net="us-east-1c.mydomaintest.netflix.net" 
"us-east-1d.mydomaintest.netflix.net" "us-east-1e.mydomaintest.netflix.net"
</code>
</pre>

Then, you can define TXT records recursively for each zone similar to the following (if more than one hostname per zone, space delimit)

<pre>
<code>
txt.us-east-1c.mydomaintest.netflix.net="ec2-552-627-568-165.compute-1.amazonaws.com" 
"ec2-368-101-182-134.compute-1.amazonaws.com"
txt.us-east-1d.mydomaintest.netflix.net="ec2-552-627-568-170.compute-1.amazonaws.com"
txt.us-east-1e.mydomaintest.netflix.net="ec2-500-179-285-592.compute-1.amazonaws.com"
</code>
</pre>

And then you specify the following properties in the Eureka Server _(eureka-client.properties_) and also in all the Eureka clients for them to be able to look up DNS and find the information necessary for communication.

<pre>
<code>
eureka.shouldUseDns=true
eureka.eurekaServer.domainName=mydomaintest.netflix.net
eureka.eurekaServer.port=7001
eureka.eurekaServer.context=eureka/v2
</code>
</pre>

At Netflix, we use this model to dynamically add/remove new Eureka server thus propagating the information to thousands of clients within a few minutes.

## Assigning EIPs using Service Urls

So, why are we defining URLs when we are supposed to assign EIPs to servers? Any 2 instances which want to communicate with one another normally use a public hostname so that the AWS security groups honor the security restrictions. Eureka servers communicate with one another using these URLs and each URL contains a public hostname (_ec2-552-627-568-170.compute-1.amazonaws.com_)  which is derived from an Elastic IP (552.627.568.170). 

Eureka server finds an EIP based on which zone it is launched. It then tries to find an unused EIP from that zone and then binds that EIP to itself during the startup.

How does Eureka find unused EIPs? It uses the Eureka client to find the list of peer instances and see what EIPS they are bound with and picks the one that is not bound. It prefers to find the EIP assigned to its zone, so that the Eureka clients of the all the other instances in the zone can talk to Eureka server that are co-located in the same zone. If the Eureka server cannot find any EIPS free for its zone, it tries the EIPs assigned in other zones. If all of them are bound, then the Eureka server starts up and waits for an EIP to become free and tries every 5 mins to bind the EIP.

The Eureka clients similarly try to find a Eureka server co-located in the same zone and if they do not find any, they fail over to the Eureka servers in the other zones.

## Eureka Failover

When a list of eureka servers are provided for a client, eureka clients automatically failover to other nodes in the cluster. Let us consider the below configuration to understand how this works

<pre>
<code>
eureka.us-east-1.availabilityZones=us-east-1c,us-east-1d,us-east-1e
eureka.serviceUrl.us-east-1c=http://ec2-552-627-568-165.compute-1.amazonaws.com:7001/eureka/v2/,http://ec2-368-101-182-134.compute-1.amazonaws.com:7001/eureka/v2/
eureka.serviceUrl.us-east-1d=http://ec2-552-627-568-170.compute-1.amazonaws.com:7001/eureka/v2/
eureka.serviceUrl.us-east-1e=http://ec2-500-179-285-592.compute-1.amazonaws.com:7001/eureka/v2/
</code>
</pre>

We have defined 3 zones (us-east-1c, us-east-1d and us-east1e) and exactly one EIP per zone. Say, for example the eureka server running in us-east-1c at the location http://ec2-552-627-568-165.compute-1.amazonaws.com:7001/eureka/v2/ fails, all eureka clients automatically communicate to the next server in the zone located at http://ec2-552-627-568-170.compute-1.amazonaws.com:7001/eureka/v2/. If that fails, the clients try the next one in the list and so on.

When the failed server comes back up, the eureka clients automatically reconnect back to the server in its zone.

## Eureka AWS-specific properties

In the AWS cloud environment, pass in the java commandline property **-Deureka.datacenter=cloud** so that the Eureka Client/Server knows to initialize the information specific to AWS cloud.

Eureka servers will require AWS access to run in EC2. There are two options for this. Either use the default (no) configuration and the servers will use the InstanceProfileCredentials on the EC2 instances, or alternatively you can explicitly set the AWS accessId and secretKey via configurations:
<pre>
   eureka.awsAccessId=
   eureka.awsSecretKey=
</pre>

## AWS access policies

Eureka tries to query ASG related information so that it can make sure the launched instances are automatically **OUT_OF_SERVICE** or **UP** depending on the value of "addToLoadbalancer" flag in Autoscaling group properties. The property used to determine which ASG you belong to is configured by specifying this property when [configuring](https://github.com/Netflix/eureka/wiki/Configuring-Eureka) the Eureka client.

<pre>
eureka.asgName
</pre>

Eureka server requires access to query the ASG information as well as bind/unbind IPs in the cloud. Hence AWS policy should be configured to allow the above accesses. The following is a sample policy with the accesses required.

<pre>
{
  "Statement": [
    {
      "Sid": "Stmt1358974336152",
      "Action": [
        "ec2:DescribeAddresses",
        "ec2:AssociateAddress",
        "ec2:DisassociateAddress"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "Stmt1358974395291",
      "Action": [
        "autoscaling:DescribeAutoScalingGroups"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
</pre>