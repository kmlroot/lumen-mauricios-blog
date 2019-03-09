---
title: Conceptos Básicos de Elixir
date: "2019-03-09"
template: "post"
draft: false
slug: "/posts/conceptos-basicos-de-elixir/"
category: "Elixir"
tags:
  - "Elixir"
  - "FP"
description: "Elixir es un lenguaje funcional, dinámico y compilado que nos brinda características tales como escalabilidad, capacidad de desarrollar aplicaciones mantenibles y distribuidas."
---

[Elixir](http://elixir-lang.org) es un lenguaje funcional, dinámico y compilado que nos brinda características tales como escalabilidad, capacidad de desarrollar aplicaciones mantenibles y distribuidas.
El principal beneficio de este lenguaje es que está construido sobre la maquina virtual de [Erlang](http://www.erlang.org), parte de las comunicaciones del mundo están basadas en Erlang, plataformas como Whatsapp están contruidas sobre esta plataforma.

[Elixir](http://elixir-lang.org) nos trae una gran cantidad de características (que muchos otros lenguajes también poseen) haciendo que la construcción de nuestros programas sea mucho más sencilla, algunas de estas características son:

### Pipe operator

Toma el valor pasado por parámetro en este caso **100** y realiza una [composición de funciones](https://es.wikipedia.org/wiki/Función_compuesta) para arrojarnos un valor final que en este casó será **500**
Cabe aclarar que el pattern matching se puede aplicar en situaciones mucho más complejas, pero en este ejemplo solo verémos la parte más básica y fundamental.

```elixir
defmodule Operaciones do
  def sumar_uno(numero), do: numero + 1
  def restar_uno(numero), do: numero - 1
  def multiplicar_10(numero), do: numero * 10
  def dividir_entre_2(numero), do: numero / 2
end

IO.puts 100 |> Operaciones.sumar_uno
            |> Operaciones.restar_uno
            |> Operaciones.multiplicar_10
            |> Operaciones.dividir_entre_2
						
#=> 500.0
```

### Pattern Matching

Cada una de las variables en el lado izquierdo tomará el valor de los elementos tipo [String](https://hexdocs.pm/elixir/String.html#content) en el lado derecho

```elixir
#=> Capitales

{colombia, francia, austria} = {"Bogotá", "París", "Vienna"}

IO.puts colombia #=> Bogotá
IO.puts francia #=> París
IO.puts austria #=> Vienna
IO.puts belgica #=> Bruselas
```

### Modulos

Tal como lo presenté en ejemplo del *Pipe Operator* un módulo encapsula una o más funciones

```elixir
defmodule MiModulo do
end
```

### Funciones
Las funciones son una relación entre entradas y salidas con la propiedad de que cada entrada está relacionada exactamente a una salida.
En **Elixir** toda función debe estar encerrada dentro de un módulo

```elixir
def mi_funcion do
end

defp mi_funcion_privada do
end
```

Otra característica bastante particular acerca de las funciones en **Elixir** son sus argumentos. En muchas ocaciones leyendo la documentación de Elixir y en la misma consola interactiva **(iex)** nos encontraremos que después de la función está el símbolo `/` seguido de algún número, este número quiere decir la cantidad de parámetros que recibe este método. Por ejemplo:

```elixir

#=> No recibe parámetros
#=> mi_function

def mi_funcion([]) do
end

#=> Recibe 1 parámetro
#=> mi_function/1

def mi_funcion(argumento_1) do
end


#=> Recibe dos parámetros
#=> mi_function/2

def mi_funcion(argumento_1, argumento_2) do
end
```

Gracias por leerme, hasta pronto :)

### Referencias
* [Sitio web lenguaje Elixir](http://elixir-lang.org/)
* [Documentacion Elixir](https://hexdocs.pm/elixir/Kernel.html)
