+++
categories = ["walkthrough", "docker", "mesos"]
description = "Using docker-compose to spin up a local mesos, marathon and chronos cluster."
date = "2015-06-23"
keywords = ["walkthrough", "mesos", "marathon", "docker"]
title = "Mesos sandbox using Docker Compose"
+++

## Getting started with Mesos, Marathon and Chronos
When I first set out to set up my own Mesos, Marathon and Chronos cluster, I turned to Docker Compose. Here I'll walkthrough how to use docker-compose to get started with your own local cluster for deployment testing and better understanding of mesos and mesos frameworks. What I'll cover includes:

* docker-compose up
* accessing the services
* sending your first deployment
* scaling up and down
* cleaning up


## docker-compose up
Start off by getting yourself setup with [docker-compose](https://docs.docker.com/compose/install/). In my example, I'm using [boot2docker](http://boot2docker.io/) and OS X. Once you're setup with docker-compose, grab a copy of [github.com/dontrebootme/compose-mesos](https://github.com/dontrebootme/compose-mesos)

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
So we just spun up a zookeeper, mesos-master, three mesos-slaves, marathon framework and chronos framework. How do we access them? Referencing `docker-compose.yml` we can see the following port definitions:

Service | Address
--------|-------
|Mesos Master|http://192.168.59.103:5050|
|Marathon|http://192.168.59.103:8080|
|Chronos|http://192.168.59.103:4400|

[Mesos](http://mesos.apache.org/) is our brain for the scheduling and placement of tasks around the cluster.
[Marathon](https://mesosphere.github.io/marathon/) is our way to interact with Mesos through deployment definitions such as `microbot_v1.json`
[Chronos](http://mesos.github.io/chronos/) is a distributed cron system for scheduling tasks around your cluster.

## Sending Your First Deployment
We will start by sending our deployment to Marathon using simply a curl command. There is a great CLI tool called [marathonctl](https://github.com/shoenig/marathonctl), but we will simply use curl for this example.

From within compose-mesos, we can run:

```
$ curl -X POST -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps -d@microbot_v1.json
```

This is equivalent to:

```
$ marathonctl app create microbot_v1.json
```

What we've just instructed Marathon to do was to place 10 instances of the `microbot` container with other definitions such as health checks, upgrade strategies etc. Check out `microbot_v1.json` for a full picture of the deployment definition.

Now that we've sent the deployment to Marathon, Marathon will reach out to Mesos and begin placement the containers. You can view this by browsing to **http://192.168.59.103:8080/**

You should see deployment in action similar to:
{{< img src="/media/compose-mesos-img1.png" title="Marathon Apps" >}}

You can drill into the app deployment to see all the tasks such as:
{{< img src="/media/compose-mesos-img2.png" title="Marathon App Tasks" >}}

## Scaling Up and Down
Through the Marathon UI, you can scale up or down your number of containers by simply using the `Scale` button. A preferred method would be modifying the deployment json file (such as microbot_v1.json) and change the `instances` parameter. For updates to apps, we dont use an HTTP POST, we instead use a PUT to the app deployment endpoint:

```
$ curl -X PUT -H "Content-Type: application/json" http://192.168.59.103:8080/v2/apps/microbot -d@microbot_v1.json
```

Using marathonctl:

```
$ marathonctl app update microbot microbot_v1.json
```

If you wanted to update the deployment (including version changes), use `curl -X PUT` or `marathonctl app update`. Try playing with changes to the deployment examples I've provided.

## Cleaning Up
When you're all done scaling up/down containers and services, go ahead and run:

```
$ ./cleanup
```

## Conclusion
So what we've done is stood a quick cluster for testing out Mesos, Marathon, and Chronos. We can remove all our work with a simple cleanup script at the end. I hope this guide has been useful for getting you started with Mesos and Marathon.
