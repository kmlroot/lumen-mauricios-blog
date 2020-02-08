---
title: Introducción a Hyperledger Fabric
date: '2020-02-08'
template: 'post'
draft: false
slug: '/articulos/introduccion-hyperledger-fabric/'
category: 'Blockchain'
tags:
  - 'Blockchain'
  - 'Hyperledger Fabric'
description: 'Hyperledger Fabric es un framework para construir aplicaciones Blockchain de tipo permisionadas.'
---

Hyperledger Fabric es un _framework_ para construir aplicaciones Blockchain permisionadas, es decir, se da acceso a ciertos participantes con identidades conocidas, a diferencia de redes públicas como Bitcoin o Ethereum que es de libre acceso y las identidades no son conocidas.

En Hyperledger Fabric contamos con 5 componentes principales: orderer, peer, ca, chaincode, sdk.

- Orderer: Es el encargado de establecer un consenso en la red a través de un orden de transacciones para después enviarselo a los _peers_ y que ellos también se lo distribuyan a los demás _peers_. Al día de hoy el protocolo de consenso que usa Hyperledger Fabric es Raft, y es el recomendado a usar en la versión 2.0 del _framework_ ya que también existe Kafka pero este será deprecado.
- Peer: Este componente es el encargado de guardar todas las transacciones y el estado de la blockchain, además en este componente es donde instalamos el famoso _smart contract_ que en Hyperledger Fabric se conoce como _chaincode_
- CA: Es una entidad certificadora que se encarga de emitir certificados a la red para que cada uno de los componentes y usuarios puedan ser identificados, como requisito esta entidad certificadora debe emitir certificados de Curva Elíptica (ECDSA)
- Chaincode: Es la lógica de negocio. El _chaincode_ debe ser lo más simple posible, actualmente podemos escribirlo en lenguajes/plataformas más "comunes" tales como: Go, Node.js, Java
- SDK: El SDK va a ser el encargado de conectarse a los peers y ejecutar las funciones que están en el _chaincode_ también puede tener funciones como leer los eventos que están pasando en la red

Todos los componentes de la red son contenedores de Docker y los puedes encontrar en [DockerHub](https://hub.docker.com/u/hyperledger), lo más común es desarrollar el SDK en Node.js y el _chaincode_ en Golang, esto porque son las dos librerías que más funcionalidades tienen (fabric-sdk-node & fabric-chaincode-go respectivamente) y más activamente se desarrollan en la comunidad.

En la siguiente imagen tenemos una arquitectura muy simple de una red de Blockchain entre dos organizaciones; en la imagen solo contamos un peer por organización, pero lo ideal siempre es tener más de uno, los peers siempre están hablando entre ellos por medio del _gossip protocol_ si uno de los peers muere cuando creemos otro el se va a actualizar con toda la copia de las transacciones.

![Hyperledger Fabric Architecture](https://user-images.githubusercontent.com/8335556/74089509-c844d180-4a6f-11ea-87e4-4f4eabe61c27.png)

Las dos organizaciones en este caso se comunican por medio de su orderer, hay otra forma complementaria a esta y es por medio de algo llamado los _anchor peers_ que los detallaremos en otra oportunidad.

Los casos de uso más relevantes para Blockchain permisionadas y en general para Blockchain, es donde tenemos un activo que nace y muere dentro de la red, además que es de interés común (el activo) entre los participantes; a diferencia de muchas noticias que vemos en Internet, no soy creyente de que Blockchain sea una aplicación para rastreo de objetos en una cadena de suministros, control de medicamentos en hospitales, entre otros. Por ejemplo, un caso "popular" es rastreo de café/bananas en Blockchain, esto no tiene sentido, ya que no puedo controlar el mundo físico desde Blockchain, ¿qué pasa cuando el usuario final se come el banano?, ¿qué pasa si transportándolo se pudre el banano? Nunca más puedo saber de el, los invito a ver este video de _Andreas Antonopoulos_ llamado ["Bananas on the Blockchain"](https://www.youtube.com/watch?v=H_kyYrbBY1I), por el contrario en un sistema como Bitcoin gracias al mecanismo de UTXO siempre voy a sacar porque manos a pasado el activo, además dicho activo siempre va a permanecer dentro de la red.

Las ventajas de Hyperledger Fabric es que tenemos mucha más transaccionalidad, el consenso es mucho más rápido al tener menos participantes en la red, identidades conocidas, lenguajes de programación "comúnes", y algunas deventajas es que estamos sacrificando decentralización sin embargo aún continuamos siendo distribuidos.

La idea es empezar a hacer una serie de artículos sobre Hyperledger Fabric, me gustaría que me dejaran en los comentarios que otras cosas les gustaría conocer sobre Blockchain, especialmente Hyperledger Fabric, Bitcoin, Tendermint.
