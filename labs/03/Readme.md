Run Kafka Producer Console
=======

The Kafka distribution provides a command utility to send messages from the command line. It start up a terminal window where everything you type is sent to the Kafka topic.
Kafka provides the utility `kafka-console-producer.sh`

1- Grab all the IPs and PORTS for the Kafka Brokers

```
Ex: 
172.31.21.170:9092
172.31.27.129:9092
172.31.26.124:9092
```

2- Create a new topic

```
kafka-topics.sh --create --replication-factor 3 --partitions 3 --topic topic-lab3 --bootstrap-server 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092
```

3- Start a new producer on one of the broker nodes.

```
kafka-console-producer.sh  --broker-list 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3
```

`--broker-list` − The list of brokers that we want to send the messages to. 

4- Send at least five messages

```
Message 1
Message 2
Message 3
Message 4
Message 5 is a long one
```

Run Kafka Consumer Console
====
All Kafka distributions provide a command utility to see messages from the command line. It displays the messages in various modes.

Kafka provides the utility `kafka-console-consumer.sh`

5- Start a basic consumer loop from CLI.

```
kafka-console-consumer.sh --bootstrap-server 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3 
```

Sadly no message was received.

6- Type a new message on the Producer.

```
This message should be seen
```

If you need to read historical data, using the --from-beginning option. Otherwise, you will only be reading future data

7- Stop consumer. Press `CTRL+C` to finish the process.

8- Start a basic consumer loop from CLI using `--from-beginning` option.

```
kafka-console-consumer.sh --bootstrap-server 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3 --from-beginning
```

Notice that the messages are not coming in order. This is because we only have one consumer so it is reading the messages from all 50 partitions. Order is only guaranteed within a partition.

9- Stop consumer. Press `CTRL+C` to finish the process.

10- Read from just one partition using `--partition`

```
kafka-console-consumer.sh --bootstrap-server 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3 --from-beginning --partition 2
```

Notice that you will only have few messages, fell free to send messages, not all are going to be display since they are been sent to other particions, but once you get the next one this should be in order with the first one.

11- Stop consumer. Press `CTRL+C` to finish the process.

12- Start again step 10, so you can see again that the messages are in order.

By default, the console consumer will show only the value of the Kafka record. Using this command you can show both the key and value.

13- Using the formatter `kafka.tools.DefaultMessageFormatter` and using the properties `print.timestamp=true print.key=true print.value=true`:


```
kafka-console-consumer.sh --bootstrap-server 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3 --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --from-beginning
```

14- Stop producer. Press `CTRL+C` to finish the process.

15- Start again a new producer that all us to sent keys to the topic.

```
kafka-console-producer.sh --broker-list 172.31.21.170:9092,172.31.27.129:9092,172.31.26.124:9092 --topic topic-lab3 --property "parse.key=true" --property "key.separator=:"
```

16- Sent at least 5 messages

17- Send at least five messages

```
thisisakey1:Messagevalue1
thisisakey2:Messagevalue2
thisisakey3:Messagevalue3
thisisakey4:Messagevalue4
thisisakey5:Messagevalue5
```




