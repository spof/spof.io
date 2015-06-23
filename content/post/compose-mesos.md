+++
categories = ["walkthrough", "docker", "mesos"]
description = "Using docker-compose to create a local mesos, marathon and chronos cluster."
date = "2015-06-23"
keywords = ["walkthrough", "mesos", "marathon", "docker"]
title = "Mesos Sandbox Using Docker Compose"
+++

## Getting started with Mesos, Marathon and Chronos
When I first set out to set up my own Mesos, Marathon and Chronos cluster, I turned to Docker Compose. Here I'll walkthrough how to use docker-compose to get started with your own local cluster for deployment testing and better understanding of mesos and mesos frameworks. What I'll cover includes:

* [docker-compose up][]
* [Accessing the Services][]
* [Sending Your First Deployment][]
* [Scaling Up and Down][]
* [Conclusion][]


## docker-compose up
Start by installing [docker-compose](https://docs.docker.com/compose/install/). In my example, I'm using [boot2docker](http://boot2docker.io/) and OS X. Once you're setup with docker-compose, grab a copy of [github.com/dontrebootme/compose-mesos](https://github.com/dontrebootme/compose-mesos)

    $ git clone https://github.com/dontrebootme/compose-mesos.git
    $ cd compose-mesos

Now that we have docker-compose, boot2docker, and compose-mesos files, we can spin up the services.

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
We just created containers for zookeeper, mesos-master, three mesos-slaves, marathon framework, and chronos framework. How do we access them? Referencing `docker-compose.yml` we can see the following port definitions:

Service | Address
--------|-------
|Mesos Master|http://192.168.59.103:5050|
|Marathon|http://192.168.59.103:8080|
|Chronos|http://192.168.59.103:4400|

[Mesos](http://mesos.apache.org/) is our brain for the scheduling and placement of tasks around the cluster.
[Marathon](https://mesosphere.github.io/marathon/) is our way to interact with Mesos through deployment definitions such as `microbot_v1.json` and [chronos](http://mesos.github.io/chronos/) is a distributed cron system for scheduling tasks around your cluster.

## Sending Your First Deployment
We will start by sending our deployment to Marathon using a simple curl command. There is also a great CLI tool called [marathonctl](https://github.com/shoenig/marathonctl), but we will simply use curl for this example to better learn what is happening.

From within compose-mesos, we can run:

```
$ curl -X POST -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps -d@microbot_v1.json
```

This is equivalent to:

```
$ marathonctl app create microbot_v1.json
```

What we've just instructed Marathon to do was place 10 instances of the `microbot` container with other definitions such as health checks, upgrade strategies etc. Check out `microbot_v1.json` for more information about the application.

Once Marathon receives the application json it will work with Mesos Master to schedule placement of the containers. You can view this by browsing to **http://192.168.59.103:8080/**

You should see deployment in action similar to:
{{< img src="/media/compose-mesos-img1.png" title="Marathon Apps" >}}

You can drill into the app deployment to see all the tasks such as:
{{< img src="/media/compose-mesos-img2.png" title="Marathon App Tasks" >}}

## Scaling Up and Down
Through the Marathon UI you can scale the containers by simply using the `Scale` button or from the command like by modifying the deployment json file (microbot_v1.json) and change the `instances` parameter. For updates to apps, we dont use an HTTP POST, we instead use a PUT to the app deployment endpoint:

```
$ curl -X PUT -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps/microbot -d@microbot_v1.json
```

Using marathonctl:

```
$ marathonctl app update microbot microbot_v1.json
```

If you wanted to update the deployment (including version changes), use `curl -X PUT` or `marathonctl app update`. Try playing with changes to the deployment examples I've provided.

## Conclusion
The examples here can be used to get familiar with Mesos, Marathon, and Chronos. You can use it for desting application deployments, modifications, or exploring the Mesos/Marathon API.

When you're all done scaling up/down containers and services, remove all of the containers with:

```
$ ./cleanup
```

Leave a comment or send me a tweet [@dontrebootme](http://twitter.com/dontrebootme) if you found this useful.
