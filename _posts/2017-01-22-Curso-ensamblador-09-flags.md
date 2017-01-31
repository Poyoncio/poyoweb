---
layout: post
title: Curso ensamblador 09. Flags   
date: 2017-01-22
---
--------------------


En este post veremos lo que son los Flags. Los flags son unas estructuras de control que equivalen a el if/else de C. Sirven para determinar ciertas condiciones y ejecutar un determinado código en función de los resultados y son una parte de la programación en ASM bastante importante.


Antes de comenzar a explicar código usando flags, veremos lo que son las etiquetas. Las etiquetas son espacios donde se almacena un determinado código, sin ir muy lejos, podemos encontrar ejemplos de estas etiquetas, por ejemplo, la de comienzo para el linker:

```nasm
global _start
_start:
```

En este caso, la etiqueta es especial, ya que es global, es decir, es accesible desde cualquier parte de nuestro código. Vamos a ver un pequeño ejemplo mostrando las etiquetas:


```nasm
section .data
msg db "Esto es la etiqueta mensaje",0xA,0xD
len equ $ - msg

section .text
    global _start
_start:
        jmp mensaje     ;Saltamos a la etiqueta mensaje

    exit:               ;Etiqueta exit
    mov eax, 1
    mov ebx, 0
    int 0x80

    mensaje:            ;Etiqueta mensaje
    mov eax, 4
    mov ebx, 1
    mov ecx, msg
    mov edx, len

    int 0x80

        jmp exit        ;Saltamos a la etiqueta exit
```

<img src="/images/mensaje-flags-db-etiquetas.png" />


Bien, lo que se puede observar aquí, es que declaramos 2 etiquetas, "exit" y "mensaje", también podemos observar un operador llamado "jmp", este operador sirve para precisamente saltar de una etiqueta a otra, es decir, comienza el programa, se encuentra un "jmp mensaje", por tanto el programa comienza a ejecutar esa etiqueta, en caso de no estar ese JMP la ejecución seguiría por la etiqueta exit. Antes que acabe la etiqueta mensaje hace un "jmp exit", que lo que hace es saltar a la etiqueta "exit", para terminar el programa correctamente. ¿Difícil? ¿Verdad que no?. Estaría bien que ustedes hagan también pruebas, así que sigamos con la explicación de los flags.


Existen distintos tipos de banderas en función de determinadas características, antes de poner todas las flags, vamos a ver como funcionan. Imaginemos que tenemos dos números, y queremos saber cual es mas grande y cual mas pequeño, bien, para compararlos usaremos el operador "cmp", que lo que hace por decirlo de alguna forma, es restar como el operador "sub", es decir, el operador de la izquierda, por el de la derecha:

```nasm
mov eax, 4
mov ebx, 1

cmp  eax, ebx
```

El resultado se almacenara en un registro especial, es decir, no se almacenara en EAX ni nada por el estilo, se utilizan unos registros llamados, registros de flags. Bien, una vez hecha la resta, existen distintos registros, los más "populares/usados" son la de **cero** (zf) y la de **carry** (cf). El registro "zf", se activara en caso de que la resta de como resultado 0, es decir, a = b los dos valores sean iguales. Decir que se activaran significa, que ZF será igual a 1, es decir, si la resta da como resultado 0, al registro ZF se le asignara el valor 1 y por tanto el registro CF se quedara tal y como estaba, a 0. El registro CF se activa cuando el valor de la derecha es más pequeño que el valor de la izquierda, es decir a < b, por tanto quedara la bandera CF igual a 1 y la bandera ZF queradá igual a 0. En caso que sea a > b, ninguna de las banderas se activara y por tanto el programa deducirá que "a" es más grande que "b".

<br>

La explicación hasta ahora sería la que se cumple con enteros sin signo, pero con enteros con signo la cosa cambia:

<br>

En el caso de las comparaciones con signo hay 3 registros, la de **cero** (zf), que ya la conociamos, la de **desborde** (of) y la de **signo** (sf). En caso de tener dos números iguales solo se activara ZF. En caso de tener a > b ZF quedará igual a 0 y SF quedara igual a OF, es decir SF = OF. En caso de que a < b, ZF se quedará igual a 0 y SF será desigual a OF, es decir SF != OF. Esto ultimo puede resultar un poco confuso, realmente no hay mucho más que explicar, es aprendérselo y ya. Esto no lo usaremos, es simplemente para saber como funciona por dentro la comparacion, por ejemplo si estais haciéndole debugging a un programa, estos datos se tienen que tener en cuenta y si lo se aprende ya, es más que mejor. Aquí les dejo un tabla que resume el concepto:  


<img src="/images/ab-flags-ofcfsf.png" />

<br>

Bien, lo que hemos visto hasta ahora es la parte interna/teórica y sobretodo aburrida. Ahora vamos a ver los operadores condicionales, es decir, los que ejecutan una determinada orden en función de los registros flags. Tenemos diversos, tipos, podemos verlos resumidos en la siguiente tabla:

<img src="/images/flags-09-jejajnbjge.png" />

<br>

Como se puede observar, hay algunos que se pueden recordar porque son por así decirlo, lo opuesto, por ejemplo, si son A y B iguales se activa JE, si son desiguales se activa JNE. Aquí podemos ver un ejemplo de estas condiciones aplicadas:

```nasm
section .data
msgIntr db "Introduzca un numero: "
lenIntr equ $ - msgIntr

msgIguales db "Son iguales", 0xA, 0xD
lenIguales equ $ - msgIguales

msgDesigual db "Son desiguales", 0xA, 0xD
lenDesigual equ $ - msgDesigual

section .bss
numero resb 1

section .text
    global _start
_start:

    mov eax, 4
    mov ebx, 1
    mov ecx, msgIntr        ;Pedimos un numero
    mov edx, lenIntr

    int 0x80

    mov eax, 3
    mov ebx, 2
    mov ecx, numero         ;Lo guardamos en "numero"
    mov edx, 1

    int 0x80

    mov eax, [numero]        ;Lo movemos a EAX
    sub eax, '0'          ;Lo pasamos a int

    cmp eax, 2               ;Comparamos

      jz Igual               ;En caso de que EAX - 2 = 0 salta a Igual
      jnz Desigual           ;En caso de que EAX - 2 != 0 salta a Desigual
      jmp exit               ;En caso de no cumplirse las anteriores salta a exit

Igual:
    mov eax, 4
    mov ebx, 1
    mov ecx, msgIguales       ;Imprime que son iguales
    mov edx, lenIguales

    int 0x80

      jmp exit                ;Salta a exit

Desigual:
    mov eax, 4
    mov ebx, 1
    mov ecx, msgDesigual       ;Imprime que son desiguales
    mov edx, lenDesigual

    int 0x80

      jmp exit                ;Salta a exit

exit:
    mov eax, 1                ;Sys.exit
    mov ebx, 0

    int 0x80
```

Llevamos ya varios capítulos explicando la estructura básica, así que no me voy a detener mas a explicar las casas básicas. La única parte que nos interesa, es que hay una entrada de datos que se guarda en "numero" mas tarde se vuelca el valor de esa variable a EAX, le restamos el carácter '0' y luego ejecutamos un CMP con el valor de EAX y 2 por defecto. Y luego se ejecutara esta parte:

```nasm
    jz Igual               
    jnz Desigual           
    jmp exit  
```

Aquí lo que podemos ver, es que despues de la comparación, si A y B son iguales, el programa saltara a la etiqueta "Igual", si es desigual saltara a la etiqueta "Desigual" y si no se cumple ninguna de las dos saltara si o si a la etiqueta "exit".

Ahora vamos a analizar la parte de la etiqueta "Igual":

```nasm
Igual:
    mov eax, 4
    mov ebx, 1
    mov ecx, msgIguales       ;Imprime que son iguales
    mov edx, lenIguales

    int 0x80

       jmp exit                ;Salta a exit
```       
    
Lo que se puede observar es una simple impresión por pantalla y un JMP a la etiqueta "exit", esto se hace para que una vez terminada la ejecución de "Igual", no salte a la etiqueta "Desigual", es decir, saltara a "exit". En "exit" solo tenemos una simple llamada al sistema para finalizar el programa. La parte que me interesa que se entienda, es la de salto en función de la comparación.


Vamos a ver como se ejecuta el programa:
<img src="/images/captura-falso-verdadero-flags.png" />


Como podemos observar, primero comparamos con 2, en este caso nos devolverá que son iguales porque salta a la etiqueta "Igual", una vez terminada podemos ver que nos da un salto en la consola, esto simplemente es un problema de la entrada de datos de la que no nos tenemos que preocupar. Y a continuación ejecutamos de nuevo pero en este caso con 3, y se puede observar que nos devolverá que es desigual, ya que salta en la etiqueta "Desigual". Obviamente no probaremos todas las condiciones, eso lo dejo en sus manos. 


Hasta aquí el tutorial de hoy, no hay demasiado más que explicar, espero que se haya entendido. Dejo por [aquí](https://mega.nz/#!O9gQ2JjL!cEa1U39hEfeehfIMgCNZazbkSpA1zumyRmOKm_pUaqo) los gráficos para que puedan descargar las tablas.
