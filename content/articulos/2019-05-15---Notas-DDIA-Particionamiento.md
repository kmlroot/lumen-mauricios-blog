---
title: Notas Designing data-intensive application, Particionamiento
date: "2019-05-19"
template: "post"
draft: false
slug: "/articulos/notas-ddia-particionamiento/"
category: "Sistemas distribuidos"
tags:
  - "Sistemas distribuidos"
  - "DDIA"
description: "Este artículo es un resumen del cápitulo 6 del libro Designing data-intensive application, este capítulo nos habla acerca de particionamiento o en inglés sharding."
---

Particionamiento, también conocido como *sharding* es una técnica en la cual cada partición es una pequeña base de datos de si misma, la razón principal para realizar particiones es tener mayor escalabilidad. Generalmente técnicas de particionamiento y replicación son combinadas, sin embargo la elección de los esquemas de particionamiento es completamente independiente del esquema de replicación.

## Particionamiento de datos llave - valor

La forma más fácil de particionar es asignar los datos de forma aleatoria a los diferentes nodos, sin embargo esto tiene una gran desventaja, cuando vas a leer de un nodo en particular no hay forma de saber que nodo está arriba (trabajando), entonces vas a tener que consultar a todos los nodos de forma paralela.

Pero con el modelo de llave - valor en el cual tenemos una llave primaria para acceder a los registros podremos encontrar fácilmente en los diferentes nodos lo que estamos buscando.

## Particionamiento de indices secundarios por documento

Consultar en una base de datos particionada es también llamado *scatter/gather* y esto puede hacer que la consulta sobre índices secundarios sea bastante costosa hasta si estás consultando en paralelo. Sin embargo, este método es bastante usado, bases de datos como MongoDB, Riak, Cassandra, entre otros, usan particionamiento de documentos basado en índices secundarios

## Rebalanceando particiones

El proceso de mover carga de un nodo a otro es llamado **rebalanceo,** no importa que esquema de particionamiento se esté usando, rebalancear tiene unos requerimientos mínimos:

1. Después de rebalancear, la carda debería ser compartida entre los nodos del cluster
2. Mientras se está rebalanceando, la base de datos debería continuar aceptando lecturas y escrituras
3. No se debería rebalancear más información de la necesaria, esto con el fin de hacer el rebalanceo más rápido y minimizar el I/O.

## Request Routing

Cuando un cliente quiere consultar un dato, no se debería preocupar por la cantidad de particiones que tenemos dentro de nuestro sistema, es más eso debería ser transparente, por esto necesitamos algo en la capa más arriba de nuestros nodos, esto es el ***service discovery,*** el cual nos ayuda a encontrar nuestros nodos estén donde estén (direcciones ips, puertos), muchos proyectos han creado sus propios service discovery y los han hecho *open source.*

Básicamente un *service discovery* debe estar en la capacidad de:

1. Permitir a los clientes contactar cualquier nodo
2. Enviar todas las peticiones de los clientes y enrutarlas
3. Requerir que todos los clientes estén alertar ante posibles particionamientos

Herramientas como Zookeeper nos ayudan a mantener de una forma autoritaria el control de nuestros nodos, sin tener que usar un protocolo de consenso que es bastante difícil de implementar (de forma correcta), uno de los sistemas que usa Zookeeper para rastrear el asignamiento de sus particiones es Kafka, otros sistemas como Cassandra y Riak usan un *gossip protocol,* estos sistemas llevan más complijadad a los nodos pero resuelven la necesidad de un coordinador externo (Zookeeper).

## Notas

- Multiples proyecto a gran escala buscan usar particionamiento para lograr un mejoramiento en el procesamiento de sus datos, por ejemplo, proyectos de Blockchain tales como Ethereum llevan tiempo investigando como el particionamiento (**sharding**) puede mejorar el nivel de transacciones por segundo que puede procesar, que actualmente es al rededor de 100 tps.
