In AWS Cloud, the instances come and go.This means you cannot identify eureka servers with a standard hostname or an IP Address. But, since Eureka Servers are the ones that help you identify other services with changing hostnames, you need a standard set of well identifiable addresses for Eureka Servers.

That is where AWS EC2 Elastic IP addresses come in handy. [This](http://aws.amazon.com/articles/1346) is a must read if you have not heard about Elastic IP addresses before.

The first step is to obtain one elastic ip per eureka server you are going to run with your setup.Ideally, you would run Eureka with the architecture specified in this page.