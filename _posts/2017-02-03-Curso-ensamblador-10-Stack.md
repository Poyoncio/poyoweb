---
layout: post
title: Curso ensamblador 10. Stack
date: 2017-02-03
---
--------------------
En esta ocasion veremos que es el **stack** y como funciona. Así que comencemos:

Una pila o stack es una lista ordenada de datos que funciona como acumulador, es decir, almacena datos y a medida que se van almacenando se van quedando más abajo. A continuación podemos ver una ilustración de como funciona:

<img src="/images/stack-09-pop-push-nasm-facil.png" />

Como se puede observar, tenemos varios "bloques", cada bloque guarda un determinado tipo de dato. Imaginemos que tenemos una caja, y en la caja tenemos 3 libros, para sacar el libro de en medio, primero tenemos que sacar el último libro que hemos puesto. Y por tanto cada vez que pongamos un nuevo libro, el libro de el final de la caja estará más lejos. Es decir, añadimos libros, y el libro que tendremos que sacar primero será el último que hemos puesto, por si no ha quedado claro, vamos a poner una segunda imagen mejor explicada:

<img src="/images/stack-22-10-ex-nasm-2.jpg" />


Espero que se haya entendido, no es para nada complicado, pero a veces cuesta pillar el concepto. Bien, una vez explicado, vamos a continuar, los operadores que usa son **push** y **pop**, y se usan para poner y sacar de la pila respectivamente. Vamos a crear un pequeño code para ver como funcionan:

```nasm
section .data
msg1 db "Este mensaje se imprimira primero", 0xA, 0xD
lenmsg1 equ $ - msg1

msg2 db "Este mensaje se imprimira despues", 0xA, 0xD
lenmsg2 equ $ - msg2

section .text
    global _start
_start:

    push msg2            ;Empujamos el segundo mensaje
    push msg1            ;Empujamos el primer mensaje

    mov eax, 4
    mov ebx, 1
    pop ecx               ;Sacamos el primer mensaje
    mov edx, lenmsg1

    int 0x80

    mov eax, 4
    mov ebx, 1
    pop ecx               ;Sacamos el segundo mensaje
    mov edx, lenmsg2

    int 0x80

    mov eax, 1
    mov ebx, 0

    int 0x80
```

<img src="/images/captura-stack-facil-10-primero-segundo.png" />


Bien, vamos a analizar la parte que nos interesa:

```nasm
    push msg2
    push msg1
```

Como podemos ver, aquí lo que hacemos es, empujar primero a msg2, pues es el mensaje que queremos recuperar en segundo lugar y empujar msg1, ya que nos interesa que este encima de msg2 y que pueda ser el primero en sacar. Si seguimos mirando el code podemos ver lo siguiente:

```nasm
    mov eax, 4
    mov ebx, 1
    pop ecx
    mov edx, lenmsg1

    int 0x80
```

Como podemos ver, esto no es más que una llamada al sistema para imprimir por pantalla el msg1. Lo que realmente hace "pop ecx", es sacar el primer bloque del stack y guardarlo en ECX, sería equivalente a:

```nasm
mov ecx, topstack
```

Seria como hacer un MOV, con el valor que esta más arriba en el stack a ECX.

El siguiente pedazo de código es el mismo, una impresion por pantalla llamando al valor de ECX con pop, en ese caso, cogiendo msg2, ya que es el segundo en salir del stack.

```nasm
    mov eax, 4
    mov ebx, 1
    pop ecx
    mov edx, lenmsg2

    int 0x80
```

Como seguramente habrán notado, el stack es una de las cosas más sencillas de ensamblador, puede que no vean mucho uso en los programas pequeños como los que hacemos, pero enrealidad, el stack es vital para tener cierto orden dentro de nuestro código, tal y como veremos en el próximo tuto. Esta vez quedo un poco corto el tutorial, pero los recomiendo que practiquen ustedes, para así en el próximo poder comenzar con los **registros de índice**.
