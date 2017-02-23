---
layout: post
title: Curso ensamblador 12. Bucles 
date: 2017-02-23
---
--------------------
Bien, en este nuevo tutorial del curso de ensamblador veremos como programar bucles, lo que vendría siendo while/for de otros lenguajes de más alto nivel.

En el tutorial dónde explicábamos los [registros de uso general](http://poyoncio.com/2017/01/01/Curso-ensamblador-03-Registros-de-uso-general-y-mov/), ya comentamos que cada registro tenía una funcion en determinados casos, bien, pues en los bucles ```AEX``` y ```ECX``` toman el valor de acumulador y contador respectivamente. De forma automática, en los loops se decrementara ```ECX``` y en caso de que ```ECX``` sea diferente a 0, se seguirá ejecutando el loop. En el caso de ```EAX``` será el encargado de ir modificando un valor cualquiera que queramos.

Vamos a poner un ejemplo en C:

```c
int acumulado = 0;  
for (int num = 5; num > 0; num--){
    acumulado++;
}
```

<br>

En la anterior declaración lo que básicamente podemos ver, es que declaramos una variable llamada ```acumulado``` que ira incrementando en caso de que ```num``` sea más grande que cero, además, num se ira decrementando en caso de que se cumpla la condición. La equivalencia en ASM sería la siguiente:

```nasm
mov eax, 0
mov ecx, 5

bucle:
    inc eax
    loop bucle
```

Bien, vamos a analizar el código:


```nasm
mov eax, 0
mov ecx, 5
```

Aquí lo que hacemos es mover a ```EAX``` el valor que vamos a acumular, en este caso lo ponemos a cero, y a continuación declaramos la etiqueta ```bucle``` que contiene lo siguiente:

```nasm
bucle:
    inc eax
    loop bucle
```

Lo que se puede observar, es que primero incrementamos ```EAX``` y a continuación, con la instrucción ```loop bucle``` lo que hacemos es, que si ```ECX``` es diferente a 0, la ejecución de la etiqueta continua.

<br>

Vamos a ver un ultimo ejemplo, para ver más claramente el funcionamiento:

```nasm
section .data
msg db "El bucle se ha ejecutado", 0xA, 0xD
lenmsg equ $ - msg

msg2 db 0xA,0xD, "La ejecución del bucle termino correctamente", 0xA, 0xD
lenmsg2 equ $ - msg2

section .text
    global _start
_start:

    mov eax, 0            ;Ponemos a 0 el contador
    mov ecx, 5            ;Ponemos las veces que se ejcutara el loop

bucle:

    push eax              ;Guardamos en la pila EAX
    push ecx              ;Guardamos en la pila ECX

    mov eax, 4
    mov edx, 1
    mov ecx, msg          ;Imprimimos por pantalla MSG
    mov edx, lenmsg

    int 0x80

    pop ecx                ;Recuperamos de la pila ECX
    pop eax                ;Recuperamos de la pila EAX

    loop bucle

exit:

    mov eax, 4
    mov ebx, 1
    mov ecx, msg2          ;Imprimimos msg2
    mov edx, lenmsg2

    int 0x80

    mov eax, 1             ;Sys.exit
    mov ebx, 0

    int 0x80
```

<img src="/images/captura-bucle-5-sehaejecutado-15-42.png" />

<br>

Vamos a analizar poco a poco el código:

```nasm
section .data
msg db "El bucle se ha ejecutado", 0xA, 0xD
lenmsg equ $ - msg

msg2 db 0xA,0xD, "La ejecución del bucle termino correctamente", 0xA, 0xD
lenmsg2 equ $ - msg2
```

En esta parte vemos ```msg``` que es el dato que imprimiremos en el bucle, y ```msg2``` que es el dato que imprimiremos al terminar, para asegurarnos que ha finalizado correctamente.

<br>

Sigamos:

```nasm
    mov eax, 0
    mov ecx, 5

bucle:

    push eax
    push ecx

    mov eax, 4
    mov edx, 1
    mov ecx, msg
    mov edx, lenmsg

    int 0x80

    pop ecx
    pop eax

    loop bucle
```

Aquí lo que podemos ver es que primero de todo, declaramos los parametros que usaremos en loop, es decir ```mov eax, 0``` se encarga de llevar el contador y ```mov ecx, 5``` es el encargado de controlar cuantas veces se ejecuta la etiqueta ```bucle```. Una vez dentro de la etiqueta ```bucle``` podemos observar que movemos ```EAX``` y ```ECX``` a la pila, esto se hace para que estos valores no se alteren, es decir, lo guardamos, hacemos las llamadas al sistema para imprimir ```msg``` y en ellas se alteran ```EAX``` y ```ECX``` y es por eso, que al final, antes del operador loop, los recuperamos con ```POP```.

<br>

Y ya para terminar:

```nasm
exit:

    mov eax, 4
    mov ebx, 1
    mov ecx, msg2          ;Imprimimos msg2
    mov edx, lenmsg2

    int 0x80

    mov eax, 1             ;Sys.exit
    mov ebx, 0

    int 0x80
```

Aqui lo único que se hace es imprimir ```msg2``` para indicar que se ha terminado correctamente y hacer una llamada al sistema para finalizar el programa.

<br>

Bien, hasta aquí el tutorial de hoy. Antes de terminar, debo decir que existen otros tipos de loops, como es el caso de ```loope``` y ```loopz```, estos dos operadores se activan en caso que la bandera ```ZF``` sea igual a 1 y ```ECX``` sea igual a 0, y los operadores ```loopne``` y ```loopnz``` se activan en caso de que la bandera ```ZF``` sea igual a 0 y ```ECX``` sea igual a 0. Pero obviamente, estos no los explicaremos, ya que este curso es de nivel básico.
