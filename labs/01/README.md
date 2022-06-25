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

Para esta etapa la del curso se planea mantener a Zookeeper en un nodo separado (de momento).Entonces, seleccione una de las cuatro maquinas como su zookeeper. 

No necesitamos descargar Zookeeper por separado. La descarga de Kafka también incluye una copia de Zookeeper. Lo primero es revisar el archivo de configuración de Zookeeper. 

1- Revisemos el contenido del archivo zookeeper.properties.

```
cat $HOME/kafka_2.13-3.2.0/config/zookeeper.properties 
```

2- Creamos una carpeta para almacenar la configuración de nuestro Zookeeper

```
sudo mkdir -p /var/lib/zookeeper
```

3- Otorgamos los permisos de acceso a nuestro usuario en mi caso a ubuntu, pero deben verificar el usuario asignado a sus maquinas.
```
sudo chown ubuntu:ubuntu /var/lib/zookeeper
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

Para comodidad, colocar el comando de inicio de zookeeper en el archivo rc.local y habilitar systemctl para garantizar que zookeeper se inicie automáticamente cada vez que inicie la máquina virtual. 

8- Abra su archivo `/etc/rc.d/rc.local` y coloque el siguiente comando de inicio en la parte inferior del archivo. Asegúrese de especificar la ruta completa.

```
vi /etc/rc.d/rc.local
```

```
/home/ubuntu/kafka_2.13-3.2.0/bin/zookeeper-server-start.sh /var/lib/zookeeper/zookeeper.properties> /dev/null 2>&1 & 
```





