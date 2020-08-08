# Docker Compose Project for OSGi Remote µServices
[![SMC Tech Blog](https://img.shields.io/badge/Mainteiner-SMC%20Tech%20Blog-blue)](https://techblog.smc.it) [![Twitter Follow](https://img.shields.io/twitter/follow/SMCpartner.svg?style=social&label=%40SMCpartner%20on%20Twitter&style=plastic)](https://twitter.com/SMCpartner) 

This project refers to the article [What are OSGi Remote µServices](https://techblog.smc.it/it/2020-07-31/cosa-sono-osgi-remote-services) published 
on the SMC [TechBlog](https://techblog.smc.it) blog.

For the OSGi bundles that you can see in this projects, you will find the source 
codes at this git repositories:

* [aries-rsa-whoiam-examples](https://github.com/smclab/aries-rsa-whoiam-examples)
* [aries-rsa-raspberrypi-examples](https://github.com/smclab/aries-rsa-raspberrypi-examples)

This Docker Compose project define the following services:

1. Apache ZooKeeper
2. Apache Karaf (two instance)
3. Liferay Portal 7.3 GA4

The OSGi containers are already set up to support the OSGi Remote Services 
specification through the implementation of Apache Aries RSA (Remote Services
Admin).The following diagram shows the scenario implemented by this project's Docker Compose.

[![Docker Services](docs/images/scenario-osgi-remote-service-1.png)](https://techblog.smc.it)

This table shows which OSGi bundles are deployed on the various OSGi container 
instances which are pulled up as services from Docker (via docker compose).

| Service              | Bundle Name     | Description                                                  |
| -------------------- | --------------- | ------------------------------------------------------------ |
| Who I am Service     | WhoIam API      | Bundle that defines the Who I am service contract through the Java interface. The bundle exports the interface package |
|                      | WhoIam Service  | Bundle that implements the interface of the Who I am service. The bundle imports the interface package |
|                      | WhoIam Consumer | Bundle that consumes the Who I am service. The reference to the service is obtained transparently from the Service Registry. The bundle imports the interface package |
| Raspberry Pi Service | Raspberry Pi API      | Bundle that defines the Raspberry Pi service contract via the Java interface. The bundle exports the interface package. |
|                      | Raspberry Pi Service  | Bundle that implements the interface of the Raspberry Pi service. The bundle imports the interface package. |
|                      | Raspberry Pi Consumer | Bundle that consumes the Raspberry Pi service. The reference to the service is obtained transparently from the Service Registry. The bundle imports the interface package. |

Table 1 - OSGi bundles that will implement the example scenario for Remote µServices

## 1. Quick Start
I've tested this services task with the Docker Desktop Engine 19.03 and the 
Docker Compose 1.26. The minimum of the resources to assign to Docker are: 

1. Number of the CPUs >= 2 
2. Memory >= 8 GByte

You can start all services defined on the docker-compose.yml file through the 
following command.

```bash
# Clone this repository project
$ git clone https://github.com/smclab/docker-osgi-remote-services-example.git

$ cd docker-osgi-remote-services-example

# Start all services
$ docker-compose up -d

# Check logs
$ docker-compose logs -f

```

This clip show howto start all services

[![asciicast](https://asciinema.org/a/351174.svg)](https://asciinema.org/a/351174)


You  can see this output after the services brings up (output of the command 
`docker-compose ps`).

```bash
                          Name                                        Command                       State                                                      Ports
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
docker-osgi-remote-services-example_karaf-instance-1_1     sh -c cp /opt/apache-karaf ...   Up                      1099/tcp, 44444/tcp, 0.0.0.0:8103->8101/tcp, 8181/tcp
docker-osgi-remote-services-example_karaf-instance-2_1     sh -c cp /opt/apache-karaf ...   Up                      1099/tcp, 44444/tcp, 0.0.0.0:8109->8101/tcp, 8181/tcp
docker-osgi-remote-services-example_liferay-instance-1_1   /bin/sh -c /usr/local/bin/ ...   Up (health)   0.0.0.0:21311->11311/tcp, 8000/tcp, 8009/tcp, 0.0.0.0:6080->8080/tcp, 0.0.0.0:9201->9201/tcp
docker-osgi-remote-services-example_zookeeper-instance_1   /docker-entrypoint.sh zkSe ...   Up (healthy)            0.0.0.0:2181->2181/tcp, 2888/tcp, 3888/tcp, 0.0.0.0:9080->8080/tcp
```

You can connect to Apache Karaf instance via this commands. The default password
is: *karaf*

```bash
$ ssh -p 8103 karaf@127.0.0.1
$ ssh -p 8109 karaf@127.0.0.1
```

You can view the installed bundle by *list* command.

```bash
START LEVEL 100 , List Threshold: 50
ID │ State  │ Lvl │ Version            │ Name
───┼────────┼─────┼────────────────────┼─────────────────────────────────────────────────────────────
12 │ Active │  80 │ 1.0.0.202007271149 │ Aries Remote Service Admin Examples - WhoIam Service
13 │ Active │  80 │ 1.0.0.202007271112 │ Aries Remote Service Admin Examples - WhoIam API
17 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Core
18 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Discovery Gogo Commands
19 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Discovery Local
20 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Discovery Zookeeper
21 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Event Publisher
22 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin provider TCP
23 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin SPI
24 │ Active │  80 │ 1.14.0             │ Aries Remote Service Admin Topology Manager
27 │ Active │  80 │ 3.4.14             │ ZooKeeper Bundle
34 │ Active │  80 │ 4.2.7              │ Apache Karaf :: OSGi Services :: Event
```

You can view the Apache Aries (RSA) endpoints by *rsa:endpoints* command.

```bash
karaf@root()> rsa:endpoints
Endpoints for framework a59e01f5-f587-4068-80cf-963d0219c2c4
id                        | interfaces                                                                     | framework                            | comp name
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
tcp://172.22.0.3:8202     | [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService]           | a59e01f5-f587-4068-80cf-963d0219c2c4 | it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl
tcp://192.168.43.185:8203 | [it.smc.techblog.apache.aries.rsa.examples.raspberrypi.api.RaspberryPiService] | 5a6b8cfe-9a75-40bf-9a7b-2bb4cb33942e | it.smc.techblog.apache.aries.rsa.examples.raspberrypi.service.RaspberryPiServiceImpl
tcp://172.22.0.5:8202     | [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService]           | ff72cd20-668e-4806-9063-8a052328c254 | it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl
```

You can connect to Liferay Gogo Shell via this command.

```bash
$ telnet localhost 21311
```

You can see output of the comand *b 1180* that show the Remote Services in use by
the consumer.

```bash
g! b 1180     
it.smc.techblog.apache.aries.rsa.examples.whoiam.consumer_1.0.0.202007301439 [1180]
  Id=1180, Status=ACTIVE      Data Root=/opt/liferay/osgi/state/org.eclipse.osgi/1180/data
  "No registered services."
  Services in use:
    {it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService}={service.intents=[osgi.basic, osgi.async], endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, service.id=2532, service.bundleid=1183, service.scope=bundle, endpoint.service.id=50, aries.rsa.port=8202, service.imported.configs=[aries.tcp], service.imported=true, endpoint.id=tcp://172.29.0.4:8202, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, component.id=0, aries.tcp.id=tcp://172.29.0.4:8202, endpoint.framework.uuid=fbd31e60-c578-49c2-b0b9-bc15e8479586}
    {org.osgi.service.log.LogService, org.osgi.service.log.LoggerFactory, org.eclipse.equinox.log.ExtendedLogService}={service.id=2, service.bundleid=0, service.scope=bundle}
  No exported packages
  Imported packages
    it.smc.techblog.apache.aries.rsa.examples.whoiam.api; version="1.0.0" <it.smc.techblog.apache.aries.rsa.examples.whoiam.api_1.0.0.202007301439 [1186]>
  No fragment bundles
  No required bundles
  ```

## 2. How to refresh OSGi bundles
If you want to install the latest versions of the bundles on OSGi containers, 
the operation is quite simple. What you need to do is copy the JAR files of the 
OSGi bundles you want to update, inside the container's deployment volume.

1. karaf-instance-{n}/deploy for Apache Karaf
2. liferay-instance-1/deploy for Liferay Portal

Below is an example on how to update Who I am Services bundles (API and Service) 
on the first instance of Apache Karaf.

```bash
# From the root project directory
$ cd karaf-instance-1/deploy

# Deploy the last version of Who Iam Service OSGi Bunle
# For getting the osgi bundles we can use the maven command
$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-whoiam-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.whoiam.api:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.jar

$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-whoiam-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.whoiam.service:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.jar 
```

You can use maven to download OSGi bundles, because for both projects the bundles 
are made available on the public maven repository that GitHub makes available.

On the logs you will notice that the bundle has been updated.
```bash
karaf-instance-1_1    | 22:37:43.329 INFO  [fileinstall-/opt/apache-karaf-4.2.7/deploy] Updating bundle it.smc.techblog.apache.aries.rsa.examples.whoiam.api / 1.0.0.202008081912
karaf-instance-1_1    | 22:37:43.330 INFO  [fileinstall-/opt/apache-karaf-4.2.7/deploy] Updating bundle it.smc.techblog.apache.aries.rsa.examples.whoiam.service / 1.0.0.202008081915
```

On the logs you will also find everything that triggered the bundle update. 
**Remember that we are in the context of OSGi Remote Services!**

```bash
karaf-instance-1_1    | 22:37:43.409 INFO  [FelixFrameworkWiring] Received ServiceEvent type: unregistering, sref: [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService]
karaf-instance-1_1    | 22:37:43.419 INFO  [FelixFrameworkWiring] Removing endpoint in zookeeper. Endpoint: {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=48, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, Path: /osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-1_1    | 22:37:43.452 INFO  [Thread-22-EventThread] Received event WatchedEvent state:SyncConnected type:NodeDeleted path:/osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-1_1    | 22:37:43.461 INFO  [Thread-22-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@f3f5baa for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=48, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-1_1    | 22:37:43.465 INFO  [Thread-22-EventThread] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
karaf-instance-1_1    | 22:37:43.466 INFO  [Thread-22-EventThread] Watching /osgi/service_registry
karaf-instance-1_1    | 22:37:43.484 INFO  [Thread-22-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
liferay-instance-1_1  | 2020-08-08 22:37:43.479 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:140] Received event WatchedEvent state:SyncConnected type:NodeDeleted path:/osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-2_1    | 22:37:43.498 INFO  [Thread-23-EventThread] Received event WatchedEvent state:SyncConnected type:NodeDeleted path:/osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-2_1    | 22:37:43.537 INFO  [Thread-23-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@34ba5593 for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=48, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-2_1    | 22:37:43.545 INFO  [Thread-23-EventThread] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
karaf-instance-2_1    | 22:37:43.546 INFO  [Thread-23-EventThread] Watching /osgi/service_registry
liferay-instance-1_1  | 2020-08-08 22:37:43.553 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.topologymanager.importer.local.EndpointListenerManager$EndpointListenerAdapter@5049f5fe for filter (&(objectClass=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService)(!(endpoint.framework.uuid=4fecf9fa-adbe-4851-811d-3f44aaef6bbd))), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=48, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.558 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@b44b7ce for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=48, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.559 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:140] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
liferay-instance-1_1  | 2020-08-08 22:37:43.559 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:178] Watching /osgi/service_registry
karaf-instance-2_1    | 22:37:43.572 INFO  [Thread-23-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
liferay-instance-1_1  | 2020-08-08 22:37:43.573 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:178] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
karaf-instance-2_1    | 22:37:43.651 INFO  [Thread-23-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@34ba5593 for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-1_1    | 22:37:43.674 INFO  [Thread-22-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@f3f5baa for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.741 INFO  [pool-5-thread-4][ImportRegistrationImpl:149] closing down all child ImportRegistrations
karaf-instance-1_1    | 22:37:43.748 INFO  [FelixFrameworkWiring] Received ServiceEvent type: registered, sref: [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService]
karaf-instance-1_1    | 22:37:43.749 INFO  [FelixFrameworkWiring] interfaces selected for export: [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService]
karaf-instance-1_1    | 22:37:43.765 INFO  [FelixFrameworkWiring] TopologyManager: export succeeded for [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], endpoint {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, rsa org.apache.aries.rsa.core.RemoteServiceAdminInstance
karaf-instance-1_1    | 22:37:43.767 INFO  [FelixFrameworkWiring] Exporting endpoint to zookeeper. Endpoint: {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, Path: /osgi/service_registry/tcp:##172.18.0.5:8202
liferay-instance-1_1  | 2020-08-08 22:37:43.796 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.topologymanager.importer.local.EndpointListenerManager$EndpointListenerAdapter@5049f5fe for filter (&(objectClass=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService)(!(endpoint.framework.uuid=4fecf9fa-adbe-4851-811d-3f44aaef6bbd))), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.798 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@b44b7ce for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-2_1    | 22:37:43.806 INFO  [Thread-23-EventThread] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
karaf-instance-2_1    | 22:37:43.807 INFO  [Thread-23-EventThread] Watching /osgi/service_registry
karaf-instance-1_1    | 22:37:43.810 INFO  [Thread-22-EventThread] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
karaf-instance-1_1    | 22:37:43.811 INFO  [Thread-22-EventThread] Watching /osgi/service_registry
liferay-instance-1_1  | 2020-08-08 22:37:43.812 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:140] Received event WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/osgi/service_registry
karaf-instance-2_1    | 22:37:43.815 INFO  [Thread-23-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
liferay-instance-1_1  | 2020-08-08 22:37:43.814 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:178] Watching /osgi/service_registry
liferay-instance-1_1  | 2020-08-08 22:37:43.824 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:178] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
karaf-instance-1_1    | 22:37:43.830 INFO  [fileinstall-/opt/apache-karaf-4.2.7/deploy] Started bundle: file:/opt/apache-karaf-4.2.7/deploy/it.smc.techblog.apache.aries.rsa.examples.whoiam.api.jar
karaf-instance-2_1    | 22:37:43.846 INFO  [Thread-23-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@34ba5593 for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-2_1    | 22:37:43.852 INFO  [Thread-23-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-1_1    | 22:37:43.855 INFO  [Thread-22-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.4:8202
liferay-instance-1_1  | 2020-08-08 22:37:43.866 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.topologymanager.importer.local.EndpointListenerManager$EndpointListenerAdapter@5049f5fe for filter (&(objectClass=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService)(!(endpoint.framework.uuid=4fecf9fa-adbe-4851-811d-3f44aaef6bbd))), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.880 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@b44b7ce for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.882 INFO  [Thread-8-EventThread][ZookeeperEndpointRepository:178] Watching /osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-1_1    | 22:37:43.891 INFO  [Thread-22-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@f3f5baa for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.4:8202, component.id=0, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=af75c7fb-b722-407c-8369-bd2b3143ed0a, endpoint.id=tcp://172.18.0.4:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=59, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.894 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.topologymanager.importer.local.EndpointListenerManager$EndpointListenerAdapter@5049f5fe for filter (&(objectClass=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService)(!(endpoint.framework.uuid=4fecf9fa-adbe-4851-811d-3f44aaef6bbd))), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
karaf-instance-2_1    | 22:37:43.890 INFO  [Thread-23-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@34ba5593 for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.895 INFO  [Thread-8-EventThread][InterestManager:126] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@b44b7ce for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
liferay-instance-1_1  | 2020-08-08 22:37:43.896 INFO  [pool-5-thread-2][RemoteServiceAdminCore:435] Importing service tcp://172.18.0.5:8202 with interfaces [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService] using handler class org.apache.aries.rsa.provider.tcp.TCPProvider.
karaf-instance-1_1    | 22:37:43.916 INFO  [Thread-22-EventThread] Watching /osgi/service_registry/tcp:##172.18.0.5:8202
karaf-instance-1_1    | 22:37:43.931 INFO  [Thread-22-EventThread] Calling endpointchanged on class org.apache.aries.rsa.discovery.command.EndpointsCommand@f3f5baa for filter (endpoint.framework.uuid=*), type {aries.rsa.port=8202, aries.tcp.id=tcp://172.18.0.5:8202, component.id=3, component.name=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl, endpoint.framework.uuid=454ba77c-e980-429b-824c-42cd23101101, endpoint.id=tcp://172.18.0.5:8202, endpoint.package.version.it.smc.techblog.apache.aries.rsa.examples.whoiam.api=1.0.0, endpoint.service.id=107, objectClass=[it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService], service.bundleid=12, service.imported=true, service.imported.configs=[aries.tcp], service.intents=[osgi.basic, osgi.async], service.scope=bundle}, endpoint {}
```

Following are the maven commands to download the latest versions of the OSGi 
bundles of projects [aries-rsa-whoiam-examples](https://github.com/smclab/aries-rsa-whoiam-examples) 
and [aries-rsa-raspberrypi-examples](https://github.com/smclab/aries-rsa-raspberrypi-examples).

```bash
# Download for Who I am Services bundles (API, Service, Consumer)
$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-whoiam-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.whoiam.api:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.whoiam.api.jar

$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-whoiam-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.whoiam.service:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.whoiam.service.jar

$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-whoiam-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.whoiam.consumer:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.whoiam.cosumer.jar

# Download for Raspberry Pi Services bundles (API, Service, Consumer)
$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-raspberrypi-examples  \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.raspberrypi.api:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.raspberrypi.api.jar

$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-raspberrypi-examples   \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.raspberrypi.service:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.raspberrypi.service.jar

$ mvn dependency:get \
    -DremoteRepositories=github::default::https://maven.pkg.github.com/smclab/aries-rsa-raspberrypi-examples   \
    -Dartifact=it.smc.techblog.apache.aries.rsa.examples:it.smc.techblog.apache.aries.rsa.examples.raspberrypi.consumer:LATEST:jar \
    -Dtransitive=false \
    -Ddest=it.smc.techblog.apache.aries.rsa.examples.raspberrypi.consumer.jar
```
Obviously in the same way you can install your bundles for your experiments.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to 
discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

Without specific disclaimer, all the plugins inside this repositories are free
software ("Licensed Software"); you can redistribute it and/or modify it under
the terms of the [GNU Lesser General Public License](http://www.gnu.org/licenses/lgpl-2.1.html)
as published by the Free Software Foundation; either version 2.1 of the License,
or (at your option) any later version.

These plugins are distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; including but not limited to, the implied warranty of MERCHANTABILITY,
NONINFRINGEMENT, or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Lesser General
Public License for more details.

You should have received a copy of the [GNU Lesser General Public
License](http://www.gnu.org/licenses/lgpl-2.1.html) along with this library; if
not, write to the Free Software Foundation, Inc., 51 Franklin Street, Fifth
Floor, Boston, MA 02110-1301 USA