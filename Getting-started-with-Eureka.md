Eureka comes with two components - Eureka Client and the Eureka Server. Your architecture which is using Eureka will typically have two applications

1) Application Client which using Eureka to make requests to the Application Service.
2) Application Service which receives requests from Application Client and sends a response back.

The Application Service will first have to register with Eureka Server to be discovered by Application Client. Registering Application Client with Discovery is optional, but registration happens anyway by default. Both the client and the service uses Eureka Client (java based) to register with Eureka Server.If you are using a non-java based application you need to use REST(todo:attach link) endpoints to register with Eureka.

Once registered with Eureka Server, the Eureka Client by default pulls this registration information every 30 seconds (configurable) to its local cache and can find information about the service or make calls to the remote service.

Hence, the setups involve the following

1) Eureka Server
2) Eureka Client for the client
3) Eureka Client for the service

A good place to start for the setups is the built in sample application that comes with Eureka which demonstrates all of the 3 setups explained above.

Here is a step by step instruction of the setups.

1) Install the latest git and gradle packages.





