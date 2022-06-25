Guia rápida para instalar Gerrit en Linux (Canonical, Ubuntu, 22.04 LTS, Jammy Jellyfish) 
=

Introducción
======
Este contenido explica cómo instalar una instancia básica de Kafka en una máquina Linux.

Requerimientos
======

* Un servidor con el sistema operativo Ubuntu, 22.04 LTS, Jammy Jellyfish previamente instalado.
* Asegurar conectividad de los puertos 22, 80, 8080 y 29418.

Pasos por seguir
======

1- Iniciar sesión dentro del servidor. Para esto abra una ventana de terminal.
2- Actualizar los repositorios de Paquetes dentro de Ubuntu.

```
sudo apt-get update && sudo apt-get upgrade -y
```

3- Instalar Java SE Runtime Environment (versión 11 o superior). Actualmente versión 11.0.14 2022-01-18. Gerrit aún no es compatible con Java 13 o posterior en este momento.

```
sudo apt install default-jre -y
```
