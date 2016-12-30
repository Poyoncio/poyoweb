---
layout: post
title: Curso ensamblador 01. Arquitectura de computadoras    
date: 2016-12-29
---
--------------------
La [arquitectua de computadoras](https://es.wikipedia.org/wiki/Arquitectura_de_computadoras) es el diseño que tienen los ordenadores, suelen estar formadas por "capas":
<br>

  <img src="/images/capas-arquitectura-computadoras.png" width="256" />

<br>
El **hardware** son los componentes físicos del sistema, como puede ser la RAM o el procesador. El **firmware** es el encargado de controlar los circuitos (hardware) y transmitir la información que estos envían al sistema. Mas adelante tendríamos el **lenguaje ensamblador**, que se encarga de "traducir/interpretar" el [código binario](https://es.wikipedia.org/wiki/C%C3%B3digo_binario) a instrucciones de más alto nivel. El **kernel** es el encargado de gestionar los recursos de nuestra computadora, como el límite de RAM o el espacio del disco duro, podemos encontrar [diferentes tipos de kernel](https://es.wikipedia.org/wiki/N%C3%BAcleo_%28inform%C3%A1tica%29). Y finalmente tendríamos el **sistema operativo** y sus respectivas **aplicaciones**.

<br>

Lo que nos interesa a nosotros y con lo que nos tenemos que quedar es con la relación hardware-ensamblador, más concretamente procesador-ensamblador, lo que hace el procesador es enviar pulsaciones eléctricas que son interpretadas como 0s y 1s, y que gracias a las [puertas lógicas](https://es.wikipedia.org/wiki/Puerta_l%C3%B3gica) el ensamblador interpreta la lógica de las instrucciones. 

<br>

Para no confundirnos, nos referiremos a procesadores como [CPU](https://es.wikipedia.org/wiki/Unidad_central_de_procesamiento) (Unidad Central de Procesamiento), las tres parted de la CPU que nos interesa son: la **pila**, el **acumulador** y los **registros**, se diferencian por la forma de almacenaje, que será lo que veremos en próximos capítulos del curso. 

<br>

Lo que me interesa que a usted le quede claro de este capítulo, es que existen unas capas definidas y que tienen como parte más importante el CPU. Hay que resaltar que los primeros capítulos serán teóricos, pero **enseguida que se pueda se comenzara con la práctica :)** El próximo capítulo ya comenzaremos a tratar con ensamblador. 

<br>

**NOTA**: Se adjuntan links, como información extra por si alguien no sabe que es, no hace falta que se lean ni mucho menos se aprendan. Añado que si algún experto lee esto que no se asuste, son solo explicaciones un poco más abstractas para que se comprendan mejor. 
