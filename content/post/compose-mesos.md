+++
categories = ["walkthrough", "docker", "mesos"]
description = "Using docker-compose to create a local mesos, marathon and chronos cluster."
date = "2015-06-23"
keywords = ["walkthrough", "mesos", "marathon", "docker"]
title = "Mesos Sandbox Using Docker Compose"
+++

## Getting started with Mesos, Marathon and Chronos
Getting started with [mesos](http://mesos.apache.org/) doesn't need to be overly complicated. Using [docker-compose](https://docs.docker.com/compose/install/) you can create a full mesos testing environment including marathon and chronos to learn how each component works and test app deployments.
Here's a walkthrough to get you started with mesos and some examples to test deployments and scaling.

## docker-compose up
Start by installing [docker-compose](https://docs.docker.com/compose/install/) using the provided docker documentation. To test this you can use [boot2docker](http://boot2docker.io/) on OS X or run on any docker engine host you have available. Next grab a copy of [github.com/dontrebootme/compose-mesos](https://github.com/dontrebootme/compose-mesos) to make the deployment easy.

    $ git clone https://github.com/dontrebootme/compose-mesos.git
    $ cd compose-mesos

Now that we have docker-compose, boot2docker, and compose-mesos files, we can start the containers that run each service.

```
$ docker-compose up -d
Creating composemesos_zk1_1...
Creating composemesos_mesos1_1...
Creating composemesos_chronos_1...
Creating composemesos_marathon_1...
Creating composemesos_slave1_1...
Creating composemesos_slave2_1...
Creating composemesos_slave3_1...
```
Please have a look at `docker-compose.yml` for the details on the different services, ports etc.

## Accessing the Services
After the containers for zookeeper, mesos-master, three mesos-slaves, marathon framework, and chronos framework have started how do you access them? Referencing `docker-compose.yml` you can see the following port definitions:

Service | Address
--------|-------
|Mesos Master|http://192.168.59.103:5050|
|Marathon|http://192.168.59.103:8080|
|Chronos|http://192.168.59.103:4400|

Here is a quick overview of each service. [Mesos](http://mesos.apache.org/) is the brain for the scheduling and placement of tasks around the cluster. [Marathon](https://mesosphere.github.io/marathon/) is the way to interact with Mesos through deployment definitions such as `microbot_v1.json` and [chronos](http://mesos.github.io/chronos/) is the distributed cron system for scheduling tasks around the cluster.

## Deploying Your First Application
To deploy an application you'll make an API call to marathon using a simple curl command. There is also a great CLI tool called [marathonctl](https://github.com/shoenig/marathonctl), but for this example we'll use curl to better understand what is happening.

From within compose-mesos run:

```
$ curl -X POST -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps -d@microbot_v1.json
```

This is equivalent to:

```
$ marathonctl app create microbot_v1.json
```

Marathon now knows about the `microbot` app along with application metadata such as health checks, upgrade strategies etc. You can see the full definition in the `microbot_v1.json` file.

Once marathon receives the application json it will work with mesos master to schedule placement of the containers. You can view this by browsing to **http://192.168.59.103:8080/**

The deployment should look something like this:
{{< img src="/media/compose-mesos-img1.png" title="Marathon Apps" >}}

Click on the app to drill into the app deployment to see all the tasks:
{{< img src="/media/compose-mesos-img2.png" title="Marathon App Tasks" >}}

## Scaling Up and Down
Through the Marathon UI you can scale the application by using the `Scale` button or from the command line by modifying the deployment json file (microbot_v1.json) and change the `instances` parameter. For updates to apps use a PUT to the app deployment endpoint:

```
$ curl -X PUT -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps/microbot -d@microbot_v1.json
```

Using marathonctl:

```
$ marathonctl app update microbot microbot_v1.json
```

If you wanted to update the deployment (including version changes) use `curl -X PUT` or `marathonctl app update`. Try playing with changes to the deployment examples I've provided.

## Conclusion
The examples here can be used to get familiar with mesos, marathon, and chronos. You can use it for testing application deployments, modifications, or exploring the mesos/marathon API.

When you're done scaling up/down containers and services, remove all of the containers and mesos cluster with:

```
$ ./cleanup
```

Leave a comment or send me a tweet [@dontrebootme](http://twitter.com/dontrebootme) if you found this useful.
