Quick guide to install Zookeeper on Linux (Canonical, Ubuntu, 22.04 LTS, Jammy Jellyfish) 
=

Introduction
======
This content explains how to install a basic instance of Zookeeper on a Linux machine.

Requirements
======

* Three servers with Ubuntu operating system, 22.04 LTS, Jammy Jellyfish previously installed.
* Ensure connectivity of ports 22, 80, 8080, 9092 (kafka), 2181 (zookeeper), 2888 (zookeeper), 3888 (zookeeper), 9000 (ZooNavigator)

Steps to follow
======

1- Log in to the server. For this open a terminal.

2- Update the Package repositories within Ubuntu.

```
sudo apt-get update && sudo apt-get upgrade -y
```

3- Install Java JDK Environment (version 11).

```
sudo apt install openjdk-11-jdk -y
```

4- We will need the wget tool to download anything to the virtual machines. So, we will install wget. Run the following command:

```
sudo apt install wget -y
```

5- Repeat all steps on all machines.

6- Now download the Apache Kafka binaries. Using the wget command.

```
wget https://dlcdn.apache.org/kafka/3.2.0/kafka_2.13-3.2.0.tgz

```

7- Let's uncompress the binaries. You can use the tar command.

```
tar -xzf kafka_2.13-3.2.0.tgz
```

8- Open the `.bash_profile` and add the Kafka bin directory to your path.

```
cd ~
nano .bash_profile
```

9- Inside `.bash_profile`,  write the following information:

```
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$HOME/kafka_2.13-3.2.0/bin
```

10- Reload your terminal with the new routes

```
source ~/.bash_profile
```

11- Repeat the same on all three virtual machines. Excellent! Your virtual machines are ready to start the real setup.


Zookeeper Configuration (Development)
========

Apache Kafka needs a Zookeeper. In a production environment, a zookeeper cluster known as the Zookeeper Ensemble must be set up. However, for development activities, you can configure a single instance of Zookeeper.

For a development environment initially we do not need to download Zookeeper separately. The Kafka download also includes a copy of Zookeeper. The following instructions show how to configure Zookeeper for development environments.

The first thing is to review the Zookeeper configuration file. 

1- Let's review the content of the zookeeper.properties file.

```
cat $HOME/kafka_2.13-3.2.0/config/zookeeper.properties 
```

2- Create a folder to store the configuration of our Zookeeper

```
sudo mkdir -p /var/lib/zookeeper
```

3- Grant access permissions to all users (for ease of practice).
```
sudo chmod 777 /var/lib/zookeeper
```

4- Copy the default configuration to our new folder.
```
cp $HOME/kafka_2.13-3.2.0/config/zookeeper.properties /var/lib/zookeeper/zookeeper.properties
```

5- Inside the file `/var/lib/zookeeper/zookeeper.properties` change the address of `dataDir=/tmp/zookeeper` by
```
dataDir=/var/lib/zookeeper
```

6- Excellent! We are ready to start the Zookeeper server. Starting Zookeeper is easy. All you need to do is run zookeeper-server-start.sh and supply zookeeper.properties as an argument.

```
zookeeper-server-start.sh /var/lib/zookeeper/zookeeper.properties
```

7- Excellent! The zookeeper server is running. Press `CTRL+C` to finish the process. Now we are sure that the configurations are good and the server starts without problems.

Some interesting questions area: 
- How we can guarantee that the service stays running on a restart. 
- How do we add the start command to systemctl, or if we just add it `&` to our run command.

Remember that the purpose of the Zookeeper contained in Kafka is for development purposes.

Configuring Zookeeper for a base production environment (StandAlone).
==========

As we could see, there are some limitations of the development configuration, since the provided base scripts lack execution independence. We are going to proceed with the installation of the StandAlone version of Zookeeper.

1- Download the Apache Zookeeper binaries. Using the wget command.

```
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz

```

2- Let's decompress the binaries. You can use the tar command.

```
tar -xzf apache-zookeeper-3.8.0-bin.tar.gz 
```

3- Open `.bash_profile` and add the Kafka bin directory to your path.

```
nano ~/.bash_profile
```

4- Inside `.bash_profile` write the following information:

```
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$HOME/kafka_2.13-3.2.0/bin:$HOME/apache-zookeeper-3.8.0-bin/bin
```

5- Reload terminal with the new routes

```
source ~/.bash_profile
```

6- Let's remember our folder `/var/lib/zookeeperand`, check the previous configuration

```
ls -la /var/lib/zookeeper
cat /var/lib/zookeeper/zookeeper.properties
```

7- Let's compare the previous configuration with the following file:

```
cat ~/apache-zookeeper-3.8.0-bin/conf/zoo_sample.cfg
```

8- Let's rename our new configuration

```
cp ~/apache-zookeeper-3.8.0-bin/conf/zoo_sample.cfg ~/apache-zookeeper-3.8.0-bin/conf/zoo.cfg
```

9- Let's delete the contents inside `/var/lib/zookeeper`, for the ease of the laboratory. To remove traces of the version of Zookeeper included by Kafka.

``` 
rm -r /var/lib/zookeeper/*
```

5- Change within the file `~/apache-zookeeper-3.8.0-bin/conf/zoo.cfg`, the address of `dataDir=/tmp/zookeeper` by `dataDir=/var/lib/zookeeper`

```
nano ~/apache-zookeeper-3.8.0-bin/conf/zoo.cfg
```

13- Start Zookeeper properly

```
zkServer.sh start
```

14- You can now validate that ZooKeeper is running correctly in standalone mode by connecting to the client port and sending the four letter srvr command. This will return basic ZooKeeper information from the running server:

```
telnet localhost 2181
...
...
...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
srvr
Zookeeper version: 3.6.3--6401e4ad2087061bc6b9f80dec2d69f2e3c8660a, built on 04/08/2021 16:35 GMT
Latency min/avg/max: 0/0.0/0
Received: 4
Sent: 3
Connections: 1
Outstanding: 0
Zxid: 0x6
Mode: standalone
Node count: 5
Connection closed by foreign host.
```

15- Stop Zookeeper

```
zkServer.sh stop
```

16- Validate that ZooKeeper is not running

```
telnet localhost 2181
...
...
Trying 127.0.0.1...
telnet: Unable to connect to remote host: Connection refused
```

For convenience, enable systemctl to ensure that zookeeper starts automatically every time you start the virtual machine.

17- Using root, create the services file.

```
  sudo vi /etc/systemd/system/zoo.service
```

18- Copy the following information into the zoo.service file:

```
[Unit]
Description=Zookeeper Daemon
Wants=syslog.target

[Service]
Type=forking
WorkingDirectory=/var/lib/zookeeper
User=ubuntu 
ExecStart=/home/ubuntu/apache-zookeeper-3.8.0-bin/bin/zkServer.sh start
ExecStop=/home/ubuntu/apache-zookeeper-3.8.0-bin/bin/zkServer.sh stop
TimeoutSec=30
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=zookeeper
PIDFile=/var/lib/zookeeper/logs/zookeeper.pid
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

19- Reload the definitions inside systemctl

```
sudo systemctl daemon-reload
```

20- Enable running the service after restart

```
sudo systemctl enable zoo
```

21- Start the service

```
sudo service zoo start
```

22- Verify that the status of the service is `Active`

```
service zoo status
```

23- You can now validate that ZooKeeper is running correctly in standalone mode by connecting to the client port and sending the four letter srvr command. This will return basic ZooKeeper information from the running server:

```
telnet localhost 2181
...
...
...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
srvr
Zookeeper version: 3.6.3--6401e4ad2087061bc6b9f80dec2d69f2e3c8660a, built on 04/08/2021 16:35 GMT
Latency min/avg/max: 0/0.0/0
Received: 4
Sent: 3
Connections: 1
Outstanding: 0
Zxid: 0x6
Mode: standalone
Node count: 5
Connection closed by foreign host.
```

24- Execute the following command in the servers to see in which Mode (follower|leader) they are

```
zkServer.sh status
```

The output from the server should follow

```
zkServer.sh status
...
...
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /home/ubuntu/apache-zookeeper-3.8.0-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone
```

25- Stop the service

```
sudo service zoo stop
```

26- Now you can validate that ZooKeeper is not running

```
telnet localhost 2181
...
...
Trying 127.0.0.1...
telnet: Unable to connect to remote host: Connection refused
```

Configuring Zookeeper for a base production environment (Ensemble).
==========

1- Execute the StandAlone installation steps from 1 to 24 on 2 more servers.

2- Get the IP Address of the 3 servers

```
ip addr show
```

For the example, let's assume the following:

- *Server1:* 172.31.1.97 (IP1)
- *Server2:* 172.31.3.236 (IP2)
- *Server3:* 172.31.5.250 (IP3)

3- In all the files `~/apache-zookeeper-3.8.0-bin/conf/zoo.cfg`,  add a line per server following the format:

```
server.X=IP1:2888:3888
```

Example:

```
server.1=172.31.1.97:2888:3888
server.2=172.31.3.236:2888:3888
server.3=172.31.5.250:2888:3888
```

4- Following the same order of the `X` and the `IP` we create the files `myid`. One per server.

```
echo 1 > /var/lib/zookeeper/myid
```

```
echo 2 > /var/lib/zookeeper/myid
```

```
echo 2 > /var/lib/zookeeper/myid
```

5- Start the service

```
sudo service zoo start
```

6- You can now validate that ZooKeeper is running correctly in standalone mode by connecting to the client port and sending the four letter srvr command. This will return basic ZooKeeper information from the running server:

```
telnet localhost 2181
...
...
...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
srvr
Zookeeper version: 3.6.3--6401e4ad2087061bc6b9f80dec2d69f2e3c8660a, built on 04/08/2021 16:35 GMT
Latency min/avg/max: 0/0.0/0
Received: 4
Sent: 3
Connections: 1
Outstanding: 0
Zxid: 0x6
Mode: standalone
Node count: 5
Connection closed by foreign host.
```

In case of error we can check the logs inside `~/apache-zookeeper-3.8.0-bin/logs`

7- Execute the following command in the servers to see in which Mode (follower|leader) they are

```
zkServer.sh status
```

The output from two of the servers should follow

```
zkServer.sh status
...
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /home/ubuntu/apache-zookeeper-3.8.0-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
```

The output from one of the servers should follow

```
zkServer.sh status
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /home/ubuntu/apache-zookeeper-3.8.0-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: leader
```


Configurando ZooNavigator (UI) usando Docker
=====

1- Before installing Docker Engine for the first time on a new host machine, you need to configure the Docker repository. Then you can install and update Docker from the repository. On the fourth machine.

```
 sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release -y
```

2- Add the official Docker GPG key:

```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

3- Use the following command to configure the repository:

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4- Install Docker Engine. Update the apt package index and install the latest version of Docker Engine, containerd and Docker Compose.

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

5- Start the container for ZooNavigator

```
sudo docker run \
  -d \
  -p 9000:9000 \
  -e HTTP_PORT=9000 \
  --name zoonavigator \
  --restart unless-stopped \
  elkozmon/zoonavigator:latest
```

6- Go to http://localhost:9000 or replace localhost with the IP of the virtual machine used.

7- On ZooNavigator home page use one of your Followers or Leader IPs and port 2181 as Connection String

Ex: `172.31.1.97:2181`

8- Click `Connect`

9- In the left panel, select the value `Zookeeper`

10- Select the value `Config`

11- Check all our nodes listed as participants.
