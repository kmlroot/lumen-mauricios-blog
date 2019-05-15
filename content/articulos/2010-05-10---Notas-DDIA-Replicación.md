---
title: Notas Designing data-intensive application, Replicación
date: "2019-05-10"
template: "post"
draft: false
slug: "/articulos/notas-ddia-replicacion/"
category: "Sistemas distribuidos"
tags:
  - "Sistemas distribuidos"
  - "Replicación"
description: "Este artículo es un resumen del cápitulo 5 del libro Designing data-intensive application, este capítulo nos habla acerca de la replicación."
---

Replicación quiere decir que podemos tener una copia de los datos en muchas máquinas y así darnos ventajas como alta disponibilidad, latencia, escalabilidad; en este capitulo el autor nos habla de 3 algoritmos para replicación de cambios entre nodos: un solo líder, multi-líder y sin lider.

## Lideres y seguidores

Cada vez que tenemos multiples replicas de nuestra base de datos debemos asegurar que toda la información sea consistente entre ellas, la solución que más usada es la replicación basada en un lider, en esta tenemos un lider y varios seguidores, cualquier cosa que el lider escriba en su estado local deberá ser transmitido a los nodos seguidores. Siempre podemos leer de cualquiera de los nodos, ya sea lider o seguidores, pero si quieremos escribir solo podemos hacerlo en el lider. 

### Replicación Síncrona vs Asíncrona

La replicación síncrona tiene la ventaja de todos los nodos seguidores van a estar siempre actualizados, si por algún motivo el lider falla la información siempre va a estar disponible en uno de sus seguidores, sin embargo replicar de forma asíncrona totalmente no es conveniente, lo que se debería tener es un nodo con replicación síncrona y los demás con replicación asíncrona y si el nodo síncrono se empieza a poner lento uno de los que son asíncronos se deberá empezar a comportar de forma síncrona. En la replicación asíncrona si el **lider** falla vamos a tener serios problemas con la lectura de los datos ya que la información no alcanza a ser replicada en los nodos **seguidores**.

### Manejo de fallas en un nodo

- Falla en un nodo seguidor: Si un nodo seguidor falla el podrá saber la última transacción procesada y así conectarse con el **lider** y actualizarse con las transacciones que han sido procesadas desde el momento del fallo.
- Falla en un nodo lider: Uno de los nodos seguidores se convierte en lider.

### Implementación de logs replicados

- Replicación basada en instrucciones: El lider registra cada una de las peticiones de escritura y luego las envia a los nodos seguidores. Existe un problema con las funciones no deterministicas, ya que estas generan un valor único por cada réplica, por ejemplo la función `NOW()` va a generar un *timestamp* diferente en cada uno de los nodos
- WAL (Write-ahead log): Existe un log donde toda la información se va agregando secuencialmente, el lider puede enviar este log a los nodos seguidores para que tengan una copia exacta del mismo.
- Replicación lógica: Es un método de replicación donde podemos ver los cambios que se han realizado es decir, si se inserta un dato vamos a poder ver los nuevos valores, si se eliminan datos vamos a poder identiticar que fila fue eliminada.

### Replication lag

Cuando una aplicación realiza lecturas a un "nodo seguidor" que funciona de forma asíncrona puede ver información desactualizada si el nodo está presentando alguna falla, por lo tanto tendremos información diferente entre el lider y ese "nodo seguidor" a esto se le conoce como consistencia eventual, sin embargo si se espera un tiempo determinado (no se sabe con certeza cuanto, pueden ser una fracción de segundo) los nodos estarán sincronizados nuevamente (si la falla no fue muy grave)

Existen algunos métodos para solucionar el problema anterior:
- *Reading your own writes*
- *Monotonic reads*
- *Consistent prefix reads*

NOTA: Algunos sistemas que funcionan con el modelo de un solo lider son Raft, Kafka, Paxos

Una desventaja del modelo de un solo lider es que todas las escrituras pasan solamente por este, entonces si presenta una caida no se va a poder realizar la escritura hasta que se elija un nuevo lider.

## Replicación multi-lider

En este modelo varios nodos pueden ser lideres, por lo tanto varios nodos pueden aceptar escrituras, en este modelo cada lider también actua como seguidores a los otros lideres.

Algunos casos de uso para la replicación multi-lider son:

- Operación en múltiples datacenters: En este caso los lideres van a estar replicados en varios datacenters, lo que no pasa con la replicación de un solo lider (en la cuál el lider estaba en un solo datacenter, si algo falla en este datacenter debemos esperar a que se elija un nuevo lider); en esta configuración podemos tener un lider en cada datacenter.

Una desventaja de la replicación multi-lider es que los datos pueden ser modificados concurrentemente en dos diferentes *datacenters*.

- Clientes con operaciones *offiline*
- Edición colaborativa (Google docs, etc.)

### Manejando conflictos en la escritura

- Resolución automática de conflictos
  * CRDTs - Conflict-free replicated datatypes: Es una estructura de datos que puede ser editada por multiples usuarios y replicada en multiples *datacenters*
  * *Mergeable persistent data structures*: Rastrea la historia explicitamente, similar a como lo hace Git
  * *Operational transformation*: Algoritmo de resolución de conflictos con el que funcionan aplicaciones como Google docs

### Topologías en replicación multi-lider 

- Topología circular: Las escrituras necesitan pasar a través de varios nodos antes que alcance todas las replicas.
- Topología en estrella: Funciona igual que la topología circular.
- Topología todos con todos: Cada lider envía sus escrituras a todos los otros lideres

![Toplogías en replicación multi-lider](https://camo.githubusercontent.com/c14455023cdcb84765ff0f3eb2debf04f367b269/687474703a2f2f6d61737574616e67752e636f6d2f6173736574732f696d616765732f646174612d696e74656e736976652d6e6f74652d322f696c6c757374726174696f6e2d312e706e67)

En las topologías circular y en estrella si un nodo falla este fallo puede interrumpir el flujo de replicación de mensajes.

## Replicación sin lider 

En la replicación sin lider no se fuerza a tener un order particular en las escrituras, estas se pueden enviar a varios nodos y también leer de varios nodos. Algunos motores de bases de datos como Riak, Cassandra y Voldemort usan la replicación sin lider, todos ellos inspirados en [Dynamo](http://www.read.seas.harvard.edu/~kohler/class/cs239-w08/decandia07dynamo.pdf)

- **Quorum para leer y escribir**: Si tenemos *n* replicas cada escritura deberá ser confirmada por *w* nodos para ser considerada exitosa y al menos *r* nodos para cada lectura, es decir, `w + r > n`, los valores *w* y *r* son llamados quorum, podemos ver a *r* y *w* como el número mínimo de votos que se requiren para que una escritura y una lectura sean válidas.

La replicación sin lider al igual que la replicación multi-lider también es adecuada para operaciones en multiples *datacenters*, ya que soporta escrituras concurrentes, interrupciones en la red, picos en la latencia, Cassandra implementa su soporte de multiples datacenters con este tipo de replicación.

### Detectando escrituras concurrentes

- **Last write wins**: Cada replica va a almacenar solo el valor más reciente y va a permitir que los valores más antiguos sean sobreescritos o descartados, esto ayuda a alcanzar una convergencia eventual pero a costa de durabilidad en el sistema, si la perdida de data no es aceptable para tu sistema el método de *last write wins* no es el adecuado.
