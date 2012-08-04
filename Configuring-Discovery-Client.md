Discovery client Responsibilities

1) Register
2) Renew
3) Cancel
4) Query instances
5) Caching

Getting started

1) Identity where eureka properties are or Set them as System properties
2) Identify application by setting properties
3) Set up disovery properties
         1) Mandatory - serviceurl, dns set up
         2) Other setups
          
4) DiscoveryManager.initComponent();
5) DiscoveryManager.getDiscoveryClient().getInstancebyVipAddress


Static properties

netflix.discovery.client.refresh.interval=30
netflix.discovery.appinfo.replicate.interval=30
netflix.discovery.dnsPollIntervalMs=300000
netflix.discovery.discoveryServer.gzipContent=true
netflix.discovery.discoveryServer.readTimeout=8000
netflix.discovery.discoveryServer.connectTimeout=5000
netflix.discovery.backupregistry=

Dynamic Properties

netflix.discovery.shouldUseDns=false
netflix.discovery.registration.enabled=true
netflix.discovery.preferSameZone=true
netflix.discovery.printDeltaFullDiff=false
netflix.discovery.disableDelta=false

