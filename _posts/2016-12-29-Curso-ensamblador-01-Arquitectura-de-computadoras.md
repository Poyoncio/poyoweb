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

Para no confundirnos, nos referiremos a procesadores como [CPU](https://es.wikipedia.org/wiki/Unidad_central_de_procesamiento) (Unidad Central de Procesamiento), se acostumbra a decir que la CPU también esta formada por la **memoria principal**, la **unidad lógica** y la **unidad de  control**.

<br>

### La memoria principal
Es la encargada de almacenar los datos, los datos se alamacenan junto con una dirección para ser accedidas más tarde. Hay varios tipos de memoria, pero las veremos más adelante para no saturarnos, solo añadir que existen dos categorías de memoria: solo lectura (ROM) y escritura/lectura (RWM).

<img src="/images/funcionamiento-memoria.png"/>

En la anterior imagen se puede observar el funcionamiento de la memoria, entra un dato, en este caso 2A y se almacena en la dirección 0, entra 45 y se almacena en la dirección 1. Cuando un programa requiere de esta información almacenada, solo tiene que situarse en la memoria asignada anteriormente. 

<br>

Cuando un dato es eliminado, lo único que hace es marcar ese espacio como vacío, y solo sera "reemplazado" cuando se requiera de esa celda de almacenaje. Por eso cuando eliminas un fichero lo puedes recuperar con herramientas forenses, porque ese dato no es borrado hasta que otro programa lo necesita. Más adelante entraremos más a fondo.  

<br>

### La unidad lógica
La unidad lógica o unidad aritmética lógica (ALU) es el encargado de calcular las operaciones aritméticas (suma, resta, multiplicación... mediante puertas lógicas), muchos circuitos requieren de esta parte, el más básico que usted puede ver en su pantalla seria el reloj, que se mantiene sumando 1 al tiempo actual. 

<img src="/images/unidad-aritmetica-logica.png"/>

Este es el símbolo con el que se representa en los esquemas, no es un componente que necesitemos estudiar, por lo tanto, con saber que existe es más que suficiente. 

<br>

### La unidad de control
La unidad de control (UC) es la encargada de buscar los datos en la memoria principal para interpretarlos y que sean ejecutadas por otra unidad que veremos más adelante, con el concepto con el que nos tenemos que quedar, es con que la unidad de control, busca en la memoria principal para que sean usados los datos en un programa.

<br>
<br>

Por tanto podemos esquematizar toda la teoria mencionada anteriormente con la siguiente imagen: 

<img src="/images/organizacion-cpu.png"/>

Y aqui un breve resumen de lo mas importante y de lo que se tienen que acordar:

<br>

<img src="/images/tabla-principales-partes-cpu.png"/>

<br>

Si has conseguido llegar hasta aquí comprendiendo lo básico, te felicito, este es de los tutoriales con más teoría del curso, así que ahora todo sera cuesta bajo. Es recomendable que [descarguen la tabla y el esquema](https://mega.nz/#!K4pCyZZC!v8pghftCc7gy4pAr4E4Lj3b-EQl0PD827G-uA5qcApc) para así acordarse de lo más necesario cuando se necesite. También hay que comentar, que existen otros componentes importantes que se trataran **más adelante** (tranquilos que aun queda) como el bus o la unidad de proceso.  
