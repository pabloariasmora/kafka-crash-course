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
wget http://redrockdigimark.com/apachemirror/kafka/2.0.0/kafka_2.12-2.0.0.tgz

```

7-  Descomprimamos los binarios. Puedes usar el comando tar.

```
tar -xzf kafka_2.13-3.2.0.tgz
```
