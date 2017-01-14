---
layout: post
title: Curso ensamblador 08. Operacions aritméticas (III)   
date: 2017-01-14
---
--------------------
En el post de hoy terminaremos de ver las operaciones aritméticas con los incrementos y los decrementos, así que vamos a ello:


### Incremento:

El incremento no es más que la suma de 1 a nuestro número original, se lleva a cabo mediante el operador "inc" y es tan fácil como:

```nasm
mov eax, 2
inc eax
```

Aquí lo que haríamos sería sumar 1 a EAX, que en este caso tenia asignado 2, por tanto nos quedara 3, en python sería algo así:

```python
a = 2
a = a + 1
```

Como se puede ver, no hay ninún problema.

<br>

### Decremento:

El decremento no es más que la resta de 1 a nuestro número original, y se lleva a cabo mediante "dec" y es tan facil como:

```nasm
mov eax, 2
dec eax
```

Pasamos el valor 2 a EAX y luego le restamos 1, por tanto quedará 1, en python sería así:

```python
a = 2
a = a - 1
```

Como se puede ver tampoco hay mucha complicación aquí.

<br>
<br>
<br>

Les parecera una tontería haver hecho un tutorial de esto, pero es necesario que se comprenda para cuando lleguemos a los loops, ahora vamos a hacer un pequeño code que reciba datos por teclado, y que los incremente y decremente:

```nasm
section .data
msgIntroducir db "Introduzca el numero que quiere incrementar y decrementar: "
lenmsgIntroducir equ $ - msgIntroducir

msgIncremento db "El numero incrementado es: "
lenmsgIncremento equ $ - msgIncremento

msgDecremento db 0xA,0xD,"El numero decrementado es: "
lenmsgDecremento equ $ - msgDecremento

msgEnter db 0xA,0xD,""
lenmsgEnter equ $ - msgEnter


section .bss
Numero resb 1
ResInc resb 1
ResDec  resb 1

section .text
    global _start
_start:

    mov eax, 4                ;Imprimimos que introduzca un caracter
    mov ebx, 1
    mov ecx, msgIntroducir
    mov edx, lenmsgIntroducir

    int 0x80

    mov eax, 3                ;Introducir caracter
    mov ebx, 2
    mov ecx, Numero           ;Se guarda en numero
    mov edx, 1

    int 0x80

    mov eax, [Numero]         
    sub eax, '0'               ;Restamos a Numero el caracter '0'

    inc eax                    ;Operamos

    add eax, '0'               ;Volvemos a poner '0' para guardar
    mov [ResInc], eax

    mov eax, 4                 ;Imprimimos frase incremento
    mov ebx, 1
    mov ecx, msgIncremento
    mov edx, lenmsgIncremento

    int 0x80

    mov eax, 4                 ;Imprimimos resultado incremento              
    mov ebx, 1
    mov ecx, ResInc
    mov edx, 1

    int 0x80

    mov eax, [Numero]        
    sub eax, '0'              ;Quitamos caracter '0' para operar

    dec eax                   ;Operamos

    add eax, '0'              ;Añadimos caracter '0' para guardar
    mov [ResDec], eax

    mov eax, 4                ;Imprimimos frase decremento
    mov ebx, 1
    mov ecx, msgDecremento
    mov edx, lenmsgDecremento

    int 0x80

    mov eax, 4                ;Imprimimos frase operacion
    mov ebx, 1
    mov ecx, ResDec
    mov edx, 1

    int 0x80

    mov eax, 4                ;Imprimimos un enter
    mov ebx, 1
    mov ecx, msgEnter
    mov edx, lenmsgEnter

    int 0x80

    mov eax, 1                ;Salimos
    mov ebx, 0

    int 0x80
```

<img src="/images/captura-inc-dec-exitoso-asm.png" />


Como habrán podido observar funciona correctamente, si miramos el código, podemos ver lo siguiente:

```nasm
mov eax, [Numero]
sub eax, '0'

inc eax

add eax, '0'
mov [ResInc], eax
```

Habíamos obtenido un número por pantalla, por tanto el número que obtenemos por pantalla es con el carácter '0', es decir, es de tipo char*, para convertirlo a int y poder operar con el, tenemos primero que restar el caracter '0', luego operar con el y finalmente volver a añadir el carácter para guardarlo.


Hasta aquí el último capítulo de aritmética, no creo que en este post haya que explicar mucho más, pues no tiene mucho misterio, en el próximo post entraremos de lleno en los flags que es lo más interesante.
