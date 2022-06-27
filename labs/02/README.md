


13- Ejecutamos un par de commandos para validar que nuestra configuración fue exitosa
```
zkCli.sh version
```

14- Revisamos los nodos disponibles

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



