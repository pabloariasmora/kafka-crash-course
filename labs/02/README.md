Quick guide to install Kafka on Linux (Canonical, Ubuntu, 22.04 LTS, Jammy Jellyfish)
=

Introduction
======
This content explains how to install a basic instance of Kafka on a Linux machine.

Requirements
======

* Three servers with Ubuntu operating system, 22.04 LTS, Jammy Jellyfish previously installed.
* Ensure connectivity of ports 22, 80, 8080, 9092 (kafka), 2181 (zookeeper), 2888 (zookeeper), 3888 (zookeeper), 9000 (ZooNavigator)
* Ensure Lab completion 01

Steps to follow
======

1- Log in to the server. For this open a terminal.

2- Update the Package repositories within Ubuntu.

```
sudo apt-get update && sudo apt-get upgrade -y
```

3- Lets validate Zookeeper Status

```
zkCli.sh status
```

4- We have a way to review current kafka nodes attached

```
zkCli.sh -server localhost:2181 ls /brokers/ids
```

The message `Node does not exist` is expected. Since at this moment we have not configured any `Kafka Broker`.

5- We need a `logs` folder for Kafka called `/tmp/kafka-logs`

```
mkdir /tmp/kafka-logs
```

6- Let's review defaulf `Kafka` configuration 

```
cat ~/kafka_2.13-3.2.0/config/server.properties
```

7- Stop all kafka brokers

8- Grab all the IPs and PORTS for the Zookeepers

```
Ex: 
172.31.21.170:2181
172.31.27.129:2181
172.31.26.124:2181
```

And `/kafka`

9- Replace on the `server.properties` file on all boostrap servers.

```
############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=172.31.21.170:2181,172.31.27.129:2181,172.31.26.124:2181/kafka
```

7- Start Kafka as foreground process

```
kafka-server-start.sh  ~/kafka_2.13-3.2.0/config/server.properties
```

8- On a different server review current kafka nodes attached to Zookeeper

```
zkCli.sh -server localhost:2181 ls /kafka/brokers/ids | tail -2
```
You should see an output similar to 

```
[0]
Exiting JVM with code 0
```

9- Now repeat steps 1-7 on a different server

10- On the new server you need to update the `broker.id` property

`nano ~/kafka_2.13-3.2.0/config/server.properties`

Should be something similar to this output

```
############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=1
```

11- Start Kafka as foreground process

```
kafka-server-start.sh  ~/kafka_2.13-3.2.0/config/server.properties
```

12- On a different server review current kafka nodes attached to Zookeeper

```
zkCli.sh -server localhost:2181 ls /kafka/brokers/ids | tail -2
```
You should see an output similar to 

```
[0,1]
Exiting JVM with code 0
```

For convenience, enable systemctl to ensure that kafka starts automatically every time you start the virtual machines.

13- Stop the `Kafka` on the all servers. Press `CTRL+C` to finish the process. Now we are sure that the configurations are good and the server starts without problems.

14- Using root, create the services file.

`sudo vi /etc/systemd/system/kafka.service`

15- Copy the following information into the `kafka.service` file:

```
[Unit]
Description=Kafka Daemon
After=network-online.target 
Requires=network-online.target

[Service]
Type=simple
User=ubuntu 
ExecStart=/home/ubuntu/kafka_2.13-3.2.0/bin/kafka-server-start.sh /home/ubuntu/kafka_2.13-3.2.0/config/server.properties
ExecStop=/home/ubuntu/kafka_2.13-3.2.0/bin/kafka-server-stop.sh /home/ubuntu/kafka_2.13-3.2.0/config/server.properties
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

16- Reload the definitions inside systemctl

`sudo systemctl daemon-reload`

17- Enable running the service after restart

`sudo systemctl enable kafka`

18- Start the service

`sudo service kafka start`

19- Verify that the status of the service is `Active`

`service kafka status`

20- On the other two servers perform from 14-19. Just remember to update the `broker.id` property on the third server.

21- On one of the servers review current kafka nodes attached to Zookeeper

```
zkCli.sh -server localhost:2181 ls /kafka/brokers/ids | tail -2
```
You should see an output similar to 

```
[0,1,2]
Exiting JVM with code 0
```

If you see a different output check the `/tmp/kafka-logs/meta.properties` on each server

```
cat /tmp/kafka-logs/meta.properties
```
You should see something similar to

```
#
#Sun Jul 03 04:25:47 UTC 2022
broker.id=0
version=0
cluster.id=T9VpTJzqSviD27qE0bff4w
```

22- Go to the Kafka admin server http://localhost:9000 or replace localhost with the IP of the virtual machine used.

23- On ZooNavigator home page use one of your Followers or Leader IPs and port 2181 as Connection String

```
Ex: 172.31.1.97:2181
```

24- Click Connect

25- In the left panel, select the value Zookeeper

26- Select the value `brokers`-> `ids`. Review the properties used for configuration. Also you can review other settings set from Kafka.


4- On one of the servers review current kafka nodes attached to Zookeeper

```
zkCli.sh -server localhost:2181 ls /kafka/brokers/ids | tail -2
```
You should see an output similar to 

```
[0,1,2]
Exiting JVM with code 0
```
51- Start all kafka brokers

Topic Creation
=====

1. On one of the Kafka nodes run the following command

```
kafka-topics.sh --create --replication-factor 3 --partitions 50 --topic demo-topic --bootstrap-server localhost:9092
```

Here with a replication factor of three for each partition, 50 partitions are created. On defining a replication factor of three,  there will be one leader and two followers, for a partition. Also, at the time when message or record is sent to the leader, it is copied in followers.

And regarding the boostrap-server is the url of one of the Kafka brokers which you give to fetch the initial metadata about your Kafka cluster.

2. Verify the recent configuration of the topic

```
kafka-topics.sh --describe --topic demo-topic --bootstrap-server localhost:9092
```

Your output should be similar to:

```
Topic: demo-topic
TopicId: b69sefAaSv2vWd9_7VzdiQ
PartitionCount: 50
ReplicationFactor: 3
Configs: segment.bytes=1073741824
Topic: demoPartition: 0  Leader: 0 Replicas: 2,0,1Isr: 0
Topic: demoPartition: 1  Leader: 2 Replicas: 1,2,0Isr: 0
Topic: demoPartition: 2  Leader: 0 Replicas: 0,1,2Isr: 0
Topic: demoPartition: 3  Leader: 0 Replicas: 2,1,0Isr: 0
Topic: demoPartition: 4  Leader: 0 Replicas: 1,0,2Isr: 0
Topic: demoPartition: 5  Leader: 0 Replicas: 0,2,1Isr: 0
Topic: demoPartition: 6  Leader: 0 Replicas: 2,0,1Isr: 0
Topic: demoPartition: 7  Leader: 0 Replicas: 1,2,0Isr: 0
Topic: demoPartition: 8  Leader: 0 Replicas: 0,1,2Isr: 0
Topic: demoPartition: 9  Leader: 0 Replicas: 2,1,0Isr: 0
Topic: demoPartition: 10 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 11 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 12 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 13 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 14 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 15 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 16 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 17 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 18 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 19 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 20 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 21 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 22 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 23 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 24 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 25 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 26 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 27 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 28 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 29 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 30 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 31 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 32 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 33 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 34 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 35 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 36 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 37 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 38 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 39 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 40 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 41 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 42 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 43 Leader: 0 Replicas: 1,2,0 Isr: 0
Topic: demoPartition: 44 Leader: 0 Replicas: 0,1,2 Isr: 0
Topic: demoPartition: 45 Leader: 0 Replicas: 2,1,0 Isr: 0
Topic: demoPartition: 46 Leader: 0 Replicas: 1,0,2 Isr: 0
Topic: demoPartition: 47 Leader: 0 Replicas: 0,2,1 Isr: 0
Topic: demoPartition: 48 Leader: 0 Replicas: 2,0,1 Isr: 0
Topic: demoPartition: 49 Leader: 0 Replicas: 1,2,0 Isr: 0
```

For Partition 0, Broker 0 is the leader and for partition 1, Broker 2 is the leader. The ISR is simply all the replicas of a partition that are "in-sync" with the leader. The definition of "in-sync" depends on the topic configuration, but by default, it means that a replica is or has been fully caught up with the leader in the last 10 seconds. The setting for this time period is: replica.lag.time.max.ms and has a server default which can be overridden on a per topic basis.

Listing Topics
=====

1. Let's add a few more topics to the cluster:

```
kafka-topics.sh --create --topic users.registrations --replication-factor 1 --partitions 2  --bootstrap-server localhost:9092
```

Lets analyze our `WARNING` message
```
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
```

Kafka enforces a set of “legal” characters that can constitute a topic name. Valid characters for Kafka topics are the ASCII Alphanumeric characters, ‘.’, ‘_’, and ‘-‘. So, anything that matches the following pattern can be a valid Kafka Topics name.

```
val legalChars = "[a-zA-Z0-9\\._\\-]"
```

However, one thing to keep in mind is that due to limitations in metric names, topics with a period (‘.’) or underscore (‘_’) could collide. To avoid issues it is best to use either, but not both.

There is no control or way around what Kafka enforces. However, you can use this rule as a foundation to get creative and come up with more standard naming conventions.

It is worth emphasizing that the topic names are case sensitive. So topicName is not the same as topicname or TopicName. Kafka would treat all three of them as individual topics.

2. Lets create a third topic

```
kafka-topics.sh --create --topic users.verifications --replication-factor 1 --partitions 2  --bootstrap-server localhost:9092
```

3. list all the Kafka topics in a cluster

```
kafka-topics.sh --list --bootstrap-server localhost:9092
```

The output should look like

```
demo-topic
users.registrations
users.verifications
```

4. Describe both new topics `users.registrations` and `users.verifications`

```
kafka-topics.sh --describe --topic users.registrations --bootstrap-server localhost:9092
```
Output should be similar to

```
Topic: users.registrations TopicId: AOARGFMgQByDB17XM1Uh_g PartitionCount: 2 ReplicationFactor: 1  Configs: segment.bytes=1073741824
Topic: users.registrations Partition: 0 Leader: 2 Replicas: 2 Isr: 2
Topic: users.registrations Partition: 1 Leader: 1 Replicas: 1 Isr: 1
```

```
kafka-topics.sh --describe --topic users.verifications --bootstrap-server localhost:9092
```

Output should be similar to

```
Topic: users.verifications TopicId: dSlbrCNWR6yTWk7DQb0HrA PartitionCount: 2 ReplicationFactor: 1 Configs: segment.bytes=1073741824
Topic: users.verifications Partition: 0 Leader: 0 Replicas: 0 Isr: 0
Topic: users.verifications Partition: 1 Leader: 2 Replicas: 2 Isr: 2
```

Increase the number of partitions of a Kafka topic?
=====

1. Alter the Kafka topic `users.verifications` to have 5 partitions

```
kafka-topics.sh --bootstrap-server localhost:9092 --alter --topic users.verifications --partitions 5
```

2. The command does not have any output, although you can verify afterwards with a `--describe` command.

```
kafka-topics.sh --describe --topic users.verfications --bootstrap-server localhost:9092
```

Check the update on the output

```
Topic: users.verifications TopicId: dSlbrCNWR6yTWk7DQb0HrA PartitionCount: 5 ReplicationFactor: 1 Configs: segment.bytes=1073741824
Topic: users.verificationsPartition: 0Leader: 0Replicas: 0Isr: 0
Topic: users.verificationsPartition: 1Leader: 2Replicas: 2Isr: 2
Topic: users.verificationsPartition: 2Leader: 2Replicas: 2Isr: 2
Topic: users.verificationsPartition: 3Leader: 0Replicas: 0Isr: 0
Topic: users.verificationsPartition: 4Leader: 1Replicas: 1Isr: 1
```

Delete a Kafka Topic
======

1. Delete the topic `demo-topic` when my Kafka broker is running at `localhost:9092`

```
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic demo-topic
```

2. You can verify the outcome of the command with a `kafka-topics.sh --list` command.

```
kafka-topics.sh --list --bootstrap-server localhost:9092
```
It make take some time (depending on the topic size) to see the effect of the command. But your output should look like:

```
users.verifications
users.registrations
```

3. Delete the topic `users.verifications` and `users.registrations`.

```
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic users.registrations
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic users.verifications
```

CMAK (Cluster Manager for Apache Kafka) - used to be know as Kafka Manager
======

1. Ensure to have docker running

2. Grab all the IPs and PORTS for the Zookeepers

```
Ex: 
172.31.21.170:2181
172.31.27.129:2181
172.31.26.124:2181
```

3. Start the container for CMAK

```
sudo docker run -d -p 9000:9000 -e ZK_HOSTS="172.31.21.170:2181,172.31.27.129:2181,172.31.26.124:2181" hlebalbau/kafka-manager:stable
```

4. Go to `http://localhost:9000` or replace localhost with the IP of the virtual machine used.

5. On CMAK, Click on the `Cluster tab` -> `Add Cluster`

6. Select a name for the cluster. And once again write IPs for the Zookeepers as part of the Cluster Zookeeper Hosts.

7. Despite not been able to select our current Kafka version, lets select the latest one available.

8. Leave everything else as default and click on Save

9. On the `Topic` tab, select List.

10. Validate the topics presents, a topic is marked red if it is marked for deletion in Zookeeper.
