---
layout: post
title: Kafka Tutorial
cover: /assets/images/kafka-tutorial/cover.png
permalink: kafka-tutorial
date: 2018-12-23 12:00:00 -0400
categories: 
  - kafka
comments: true
---

Set up a local Kafka cluster and walk through basic Kafka commands. This
follows the [Udemy Kafka Course](https://www.udemy.com/apache-kafka/).

## What is Kafka?
[Kafka](https://kafka.apache.org/intro) is a distributed streaming platform used to:
- Publish and subscribe to streams of records
- Store streams of records
- Process streams of records.

The above link has a more detailed description of Kafka and how it is used. A great
use case is using Kafka for the pipeline of events in an event-sourced application.

## Environment set up
1. Install [Docker](https://www.docker.com/). See instructions 
[here](https://andrewcmeier.com/win-dev#docker-and-docker-compose) for installing
on Windows.
2. Install [Kubernetes](https://kubernetes.io/). See instructions
[here](https://andrewcmeier.com/win-dev#kubernetes) for installing
on Windows.
3. Install [Helm](https://helm.sh/). See instructions
[here](https://andrewcmeier.com/win-dev#helm) for installing
on Windows.
4. Add the [Bitnami repo](https://github.com/bitnami/charts).
    ```
    > helm repo add bitnami https://charts.bitnami.com
    > helm repo update
    ```
5. Create a namespace for Kafka.
    ```
    > kubectl create namespace kafka
    > kubectl config set-context docker-for-desktop --namespace kafka
    ```
6. Install the [Bitnami Kafka chart](https://github.com/bitnami/charts/tree/master/bitnami/kafka).
    ```
    > helm install bitnami/kafka --name kafka --namespace kafka
    ```
7. Verify the installation.
    ```
    > helm list
    ```
    ```
    > helm status kafka
    ```
8. List the services (the service names are the domains used in the tutorial).
    ```
    > kubectl get svc
    NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
    kafka                      ClusterIP   10.99.206.57   <none>        9092/TCP                     56m
    kafka-headless             ClusterIP   None           <none>        9092/TCP                     56m
    kafka-zookeeper            ClusterIP   10.98.222.47   <none>        2181/TCP,2888/TCP,3888/TCP   56m
    kafka-zookeeper-headless   ClusterIP   None           <none>        2181/TCP,2888/TCP,3888/TCP   56m
    ```
9. List the pods.
    ```
    > kubectl get pods
    NAME                READY     STATUS    RESTARTS   AGE
    kafka-0             1/1       Running   1          1h
    kafka-zookeeper-0   1/1       Running   0          1h
    ```
10. Install `telepresence` in order to proxy the Kafka service locally. We must use this so that the
IP addresses are resolved correctly when connecting to Kafka locally.
    ```
    $ curl -s https://packagecloud.io/install/repositories/datawireio/telepresence/script.deb.sh | sudo bash
    $ sudo apt install --no-install-recommends telepresence
    ```
    > If you are using Windows then you must use the [Windows Subsystem for Linux (WSL)](https://andrewcmeier.com/win-dev#windows-subsystem-for-linux).
11. Install .NET Core SDK.
    ```
    $ wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    $ sudo dpkg -i packages-microsoft-prod.deb
    $ sudo apt-get install apt-transport-https
    $ sudo apt-get update
    $ sudo apt-get install dotnet-sdk-2.2
    ```
    > If you are using Windows you must install in WSL so that you can use `telepresence`.

## Tutorial - CLI
This section will go through the steps to create a simple producer
and consumer using the Kafka command line scripts.

### Create a topic
Connect to the Kafka instance.
```
> kubectl exec -it kafka-0 bash 
```

Create a topic.
```
$ kafka-topics.sh --zookeeper kafka-zookeeper:2181 --topic logging.tutorial.main --create --partitions 3 --replication-factor 1
```
> `kafka-zookeeper:2181` is the address of the Zookeeper service retrieved from `kubectl get svc`.
Best practice for replication factor is > 1 but since we only have one broker we can only set `replication-factor=0`.
The topic naming is arbitrary and you can choose whatever topic name you like. See
[Kafka topic naming article](https://medium.com/@criccomini/how-to-paint-a-bike-shed-kafka-topic-naming-conventions-1b7259790073)
for some thoughts on how to name topics.

### Produce messages
Connect to the producer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main
>
```

Once the console launches you can type a message and press `Enter`.
```
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main
>hello
>world
```

To exit the producer console, press `Ctrl-C`.

### Produce messages with acknowledgement
Connect to the producer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main --producer-property acks=all
>
```
> Notice the additional `producer-property` argument. `acks=1` is the default which means
the leader broker will acknowledge the message. `acks=all` means that the leader
and the replica brokers will acknowledge the message. `acks=0` means that the producer
will not wait for acknowledgement.

Once the console launches you can type a message and press `Enter`.
```
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main --producer-property acks=all
>hello with ack
>world with ack
```

To exit the producer console, press `Ctrl-C`.

### Consume messages
Connect to the consumer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic logging.tutorial.main
```

Open a new terminal and connect to the producer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main
>
```

Write some messages in the producer console and you should see the messages
appear in the consumer console.

### Consume messages from beginning
Connect to the consumer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic logging.tutorial.main --from-beginning
```
> Note the additional `from-beginning` argument.

You should then see all the messages written to the `logging.tutorial.main` topic.

### Consume messages within a group
Connect to the consumer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic logging.tutorial.main --group my-app
```
> Note the additional `group` argument.

Open a new terminal and connect to another consumer console with the same group.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic logging.tutorial.main --group my-app
```

Open a new terminal and connect to the producer console.
```
> kubectl exec -it kafka-0 bash 
$ kafka-console-producer.sh --broker-list kafka:9092 --topic logging.tutorial.main
>
```

Write some messages in the producer console and you should see the messages
evenly split between the two consumer consoles.

## Tutorial - F#
See the [source code](https://github.com/ameier38/kafka-beginners-course) for
creating a producer and consumer using F# 
(<img src="/assets/images/i-heart-fsharp.png" alt="i-heart-fsharp" height="20px">).

## Summary
In this post we covered:
- [Introduced Kafka](#what-is-kafka)
- [Set up environment](#environment-set-up)
- [Created a topic](#create-a-topic)
- [Produced messages](#produce-messages)
- [Consumed messages](#consume-messages)
- [Built F# produce and consumer](#tutorial-f)

Much thanks to the engineers at [Confluent](https://www.confluent.io/)
and [Jet.com](https://github.com/jet) for all the work on the Kafka and F#
libraries :raised_hands:

## Additional Resources
- [Kafka homepage](https://kafka.apache.org/intro)
- [Udemy Kafka course](https://www.udemy.com/apache-kafka/)
- [Kafka topic naming](https://medium.com/@criccomini/how-to-paint-a-bike-shed-kafka-topic-naming-conventions-1b7259790073)
- [kafkacat](https://github.com/edenhill/kafkacat)
- [telepresence](https://www.telepresence.io/)
- [Connect to Kafka cluster in Kubernetes](https://medium.com/@valercara/connecting-to-a-kafka-cluster-running-in-kubernetes-7601ae3a87d6)
- [Install .NET Core SDK on Ubuntu](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-2.2.101)

I hope you enjoyed the post. If you run into any issues setting up the project leave a
comment and I can try to help debug :bug:.

{% include disqus.html %}