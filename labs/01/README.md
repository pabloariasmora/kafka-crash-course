Guia rápida para instalar Gerrit en Linux (Canonical, Ubuntu, 22.04 LTS, Jammy Jellyfish) 
=

Introducción
======
Este contenido explica cómo instalar una instancia básica de Kafka en una máquina Linux.

Requerimientos
======

* Cuatro servidor con el sistema operativo Ubuntu, 22.04 LTS, Jammy Jellyfish previamente instalado.
* Asegurar conectividad de los puertos 22, 80, 8080 , 9092 (kafka), 2181 (zookeeper), 2888(zookeeper), 3888 (zookeeper).

Pasos por seguir
======

1- Iniciar sesión dentro del servidor. Para esto abra una ventana de terminal.

2- Actualizar los repositorios de Paquetes dentro de Ubuntu.

```
sudo apt-get update && sudo apt-get upgrade -y
```

3- Instalar Java JDK Environment (versión 11 o superior). 

```
sudo apt install openjdk-11-jdk -y
```

4- Necesitaremos la herramienta wget para descargar cualquier cosa en las máquinas virtuales. Entonces, instalaremos wget. Ejecute el siguiente comando:

```
sudo apt install wget -y
```

5- Repita todos los pasos en todas las maquinas (4).

6- Ahora edescargar los binarios de Apache Kafka. Utilizando el comando wget.

```
wget https://dlcdn.apache.org/kafka/3.2.0/kafka_2.13-3.2.0.tgz

```

7-  Descomprimamos los binarios. Puedes usar el comando tar.

```
tar -xzf kafka_2.13-3.2.0.tgz
```

8- Abra el .bash_profile y agregue el directorio bin de Kafka en su ruta.

```
cd ~
nano .bash_profile
```

9- Dentro de .bash_profile escrirbimos la siguiente información:

```
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$HOME/kafka_2.13-3.2.0/bin
```

10- Recargamos nuestra terminal con las nuevas rutas

```
source ~/.bash_profile
```

11- Repita lo mismo en las cuatro máquinas virtuales. ¡Excelente! Sus máquinas virtuales están listas para iniciar la configuración real.

Configuración de Zookeeper
========

Apache Kafka necesita un de Zookeeper. En un entorno de producción, se debe configurar un clúster de zookeeper conocido como Zookeeper Ensemble. Sin embargo, para las actividades de desarrollo, puede configurar una única instancia de Zookeeper. 

Para un ambiente de desarrollo inicialmente no necesitamos descargar Zookeeper por separado. La descarga de Kafka también incluye una copia de Zookeeper. Las siguientes instrucciones muestran la configuración de Zookeeper para entornos de desarrollo.

Lo primero es revisar el archivo de configuración de Zookeeper. 

1- Revisemos el contenido del archivo zookeeper.properties.

```
cat $HOME/kafka_2.13-3.2.0/config/zookeeper.properties 
```

2- Creamos una carpeta para almacenar la configuración de nuestro Zookeeper

```
sudo mkdir -p /var/lib/zookeeper
```

3- Otorgamos los permisos de acceso a todos los usurios (por facilidad de la practica).
```
sudo chmod 777 /var/lib/zookeeper
```

4- Copiamos la configuración por defecto a nuestra nueva carpeta.
```
cp $HOME/kafka_2.13-3.2.0/config/zookeeper.properties /var/lib/zookeeper/zookeeper.properties
```

5- Cambiamos dentro del archivo /var/lib/zookeeper/zookeeper.properties, la direccion de dataDir=/tmp/zookeeper por
```
dataDir=/var/lib/zookeeper
```

6- ¡Excelente! Estamos listos para iniciar el servidor Zookeeper. Iniciar Zookeeper es sencillo. Todo lo que necesita hacer es ejecutar zookeeper-server-start.sh y proporcionar zookeeper.properties como argumento.

```
zookeeper-server-start.sh /var/lib/zookeeper/zookeeper.properties
```

7- ¡Excelente! El servidor zookeeper se está ejecutando. Presione `CTRL+C` para terminar el proceso. Ahora estamos seguros de que las configuraciones son buenas y el servidor se inicia sin problemas. 

Algunas preguntas interesantes es de como podemos garantizar que en un reinicio el servicio se mantenga en ejecución. Como incorporamos el comando de inicio a systemctl, o si basta con agregar `&` a nuestro comando de ejecución.

Recordemos que la finalidad del Zookeeper que contiene Kafka es para fines de desarrollo.

Configurando Zookeeper para un ambiente de producion base (StandAlone).
==========

Como pudimos observar existen algunas limitantes de la configuración de desarollo, ya que los scripts base proporcionados carecen de indendencia de ejecución. Vamos a proceder con la instalación de la version StandAlone de Zookeeper.

8- Ahora edescargar los binarios de Apache Zookeeper. Utilizando el comando wget.

```
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz

```

7-  Descomprimamos los binarios. Puedes usar el comando tar.

```
tar -xzf apache-zookeeper-3.8.0-bin.tar.gz 
```

8-  Renombramos nuestra carpeta para facilidad de referencia

```
mv apache-zookeeper-3.8.0-bin zookeeper
```

8- Abra el .bash_profile y agregue el directorio bin de Kafka en su ruta.

```
cd ~
nano .bash_profile
```

9- Dentro de .bash_profile escrirbimos la siguiente información:

```
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$HOME/kafka_2.13-3.2.0/bin:$HOME/apache-zookeeper-3.8.0-bin
```

10- Recordemos nuestra carpeta `/var/lib/zookeeper` y la configuración previa

```
ls -la /var/lib/zookeeper
cat /var/lib/zookeeper/zookeeper.properties
```

11- Comparemos la configuración previa con el siguiente archivo:

```
cat ~/apache-zookeeper-3.8.0-bin/conf/zoo_sample.cfg
```

12- Renombremos nuestra nueva configuración 
```
cp ~/apache-zookeeper-3.8.0-bin/conf/zoo_sample.cfg ~/apache-zookeeper-3.8.0-bin/conf/zoo.cfg
```

13- Iniciamos propiamente Zookeeper

```
zkServer.sh start
```

14- Ahora puede validar que ZooKeeper se está ejecutando correctamente en modo independiente conectándose al puerto del cliente y enviando el comando de cuatro letras srvr. Esto devolverá información básica de ZooKeeper desde el servidor en ejecución:

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

Para comodidad, habilitar systemctl para garantizar que zookeeper se inicie automáticamente cada vez que inicie la máquina virtual. 

8- Usando root, creamos el archivo de servicios.

```
  sudo vi /etc/systemd/system/zoo.service
```

9- Copiamos dentro del archivo gerrit.service la siguiente información:

```
[Unit]
Description=Zookeeper Daemon
Wants=syslog.target

[Service]
Type=forking
WorkingDirectory=/var/lib/zookeeper
User=ubuntu 
ExecStart=/home/zookeeper_home/bin/zkServer.sh
TimeoutSec=30
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=zookeeper
PIDFile=/var/lib/zookeeper/logs/zookeeper.pid
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

6- Recargamos las definiciones dentro de systemctl

```
sudo systemctl daemon-reload
```

7- Habilitamos que corrar luego de reiniciar

```
sudo systemctl enable zoo
```

8- Iniciamos el servicio

```
sudo service zoo start
```

9- Verificamos que el estado del servicio sea `Active`

```
service zoo status
```

10- Ejecutamos un par de commandos para validar que nuestra configuración fue exitosa
```
zkCli.sh version
```

11- Revisamos los nodos disponibles

```
zkCli.sh -server localhost:2181 ls /brokers/ids
```

Es esperado que a este punto no tengamos ningun nodo, por tanto recibiremos un mensaje de `Node does not exist`. Ya que no hemos configurado Kafka.





Configuración de un broker de Kafka sobre el mismo nodo que Zookeeper
========

1 - Creamos una carpeta para que Kafka pueda escribir sus logs

```
mkdir /tmp/kafka-logs
```

3- Revisamos las configuraciones bases de Kafka

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



