# Docker Compose Project for OSGi Remote µServices

This project refers to the article *What are OSGi Remote µServices* published 
on the SMC [TechBlog](https://techblog.smc.it) blog.

For the OSGi bundles that you can see in this project, you will find the source 
codes at this git repositories:

* [aries-rsa-whoiam-examples](https://github.com/smclab/aries-rsa-whoiam-examples)
* [aries-rsa-raspberrypi-examples](https://github.com/smclab/aries-rsa-raspberrypi-examples)

This Docker Compose project define the following services:

1. Apache ZooKeeper
2. Apache Karaf
3. Liferay Portal 7.3 GA4

The OSGi containers are already set up to support the OSGi Remote Services 
specification through the implementation of Apache Aries RSA (Remote Services
Admin).

## 1. Quick Start
You can start all services defined on the docker-compose.yml file through the 
following command.

```bash
# Clone this repository project
$ git clone https://github.com/smclab/docker-osgi-remote-services-example.git

$ cd docker-osgi-remote-services-example

# Start all services
$ docker-compose up
```

You  can see this output after the services brings up (output of the command 
`docker-compose ps`).

```bash
                          Name                                        Command                       State                                                             Ports                                                  
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
docker-osgi-remote-services-example_karaf-instance-1_1     sh -c cp /opt/apache-karaf ...   Up                      0.0.0.0:1099->1099/tcp, 0.0.0.0:4444->4444/tcp, 44444/tcp, 0.0.0.0:8101->8101/tcp, 0.0.0.0:8181->8181/tcp
docker-osgi-remote-services-example_karaf-instance-2_1     sh -c cp /opt/apache-karaf ...   Up                      0.0.0.0:2099->1099/tcp, 0.0.0.0:5444->4444/tcp, 44444/tcp, 0.0.0.0:9101->8101/tcp, 0.0.0.0:9181->8181/tcp
docker-osgi-remote-services-example_liferay-instance-1_1   /bin/sh -c /usr/local/bin/ ...   Up (health: starting)   0.0.0.0:21311->11311/tcp, 8000/tcp, 8009/tcp, 0.0.0.0:6080->8080/tcp, 0.0.0.0:9201->9201/tcp             
docker-osgi-remote-services-example_zookeeper-instance_1   /docker-entrypoint.sh zkSe ...   Up (healthy)            0.0.0.0:2181->2181/tcp, 2888/tcp, 3888/tcp, 0.0.0.0:9080->8080/tcp                                       
```

You can connect to Apache Karaf instance via this commands. The default password
is: *karaf*

```bash
$ ssh -p 8101 karaf@127.0.0.1 
$ ssh -p 9101 karaf@127.0.0.1 
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
Endpoints for framework fbd31e60-c578-49c2-b0b9-bc15e8479586
id                    | interfaces                                                           | framework                            | comp name                                                                 
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
tcp://172.29.0.4:8202 | [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService] | fbd31e60-c578-49c2-b0b9-bc15e8479586 | it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl
tcp://172.29.0.5:8202 | [it.smc.techblog.apache.aries.rsa.examples.whoiam.api.WhoIamService] | 118de5a0-e754-4a35-a3f1-adbf78c8b36f | it.smc.techblog.apache.aries.rsa.examples.whoiam.service.WhoIamServiceImpl
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