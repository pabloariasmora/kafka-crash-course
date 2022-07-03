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

3- Let's review defaulf `Kafka` configuration 

```
cat ~/kafka_2.13-3.2.0/config/server.properties
```

2- Ejecutamos Kafka

```
kafka-server-start.sh  ~/kafka_2.13-3.2.0/config/server.properties
```

7- ¡Excelente! El broker kafka se está ejecutando. Presione `CTRL+C` para terminar el proceso. Ahora estamos seguros de que las configuraciones son buenas y el servidor se inicia sin problemas. 

8- Ejecutamos Kafka en segundo plano

```
kafka-server-start.sh -daemon ~/kafka_2.13-3.2.0/config/server.properties
```

11- Revisamos los nodos disponibles usando Zookeeper.

```
zkCli.sh -server localhost:2181 ls /brokers/ids
```

Esta vez deberiamos tener una salida similar a `[0]` ya que solo tenemos un broker asignado.



