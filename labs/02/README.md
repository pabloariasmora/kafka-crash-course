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

7- Start Kafka as foreground process

```
kafka-server-start.sh  ~/kafka_2.13-3.2.0/config/server.properties
```

8- On a different server review current kafka nodes attached to Zookeeper

```
zkCli.sh -server localhost:2181 ls /brokers/ids | tail -2
```
You should see an output similar to 

```
[0]
Exiting JVM with code 0
```

9- Now repeat steps 1-5 on a different server

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
zkCli.sh -server localhost:2181 ls /brokers/ids | tail -2
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
zkCli.sh -server localhost:2181 ls /brokers/ids | tail -2
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


