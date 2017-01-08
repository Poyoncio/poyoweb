---
layout: post
title: Curso ensamblador 06. Operaciones aritméticas (I)
date: 2017-01-08
---
--------------------
En el post de hoy veremos las operaciones aritméticas básicas, hoy haremos programas básicos donde unos números definidos en el programa se irán ejecutando, así que sin más preámbulos comencemos:

## Suma:
Empezamos con lo básico, la suma, se realiza con el operador "add". Para realizar una suma requerimos de al menos 1 operador de uso general para guardar el resultado, por ejemplo:

```nasm
mov eax, 4
add eax, 7
```

Y se preguntaran, ¿donde se guarda el resultado? Bien, pues el resultado de la suma quedaría siempre en el registro de la izquierda, en un lenguaje como python quedaría así:

```python
eax = 4
eax = eax + 7
```

En caso que no tuviéramos ningún registro donde almacenar el resultado, quedaría así:

```
4 = 4 + 7
```

Como podemos ver, no tendría demasiada lógica, lo único "nuevo" es que los resultados se almacenan en el operador de la izquierda después de realizar la suma, así que continuemos.

<br>

## Resta:
La resta es más de lo mismo, se restan los dos números y se almacenan en un registro, se usa el operador sub:

```nasm
mov eax, 7
sub eax, 2
```

En python sería algo así:

```python
eax = 7
eax = eax - 2
```

<br>
<br>
<br>

Este seria el correspondiente código a una suma y una resta:

```nasm
section .data
msgSuma db "La suma de 7 y 2 es: "
lenSuma equ $ - msgSuma

msgResta db 0xA,0xD,"La resta de 7 y 2 es: "
lenResta equ $ - msgResta

finLinea db 0xA,0xD,""        ;Equivale a un enter
lenfinLinea equ $ - finLinea

section .bss
ResSuma resb 1            ;Almacenar resultado suma
ResResta resb 1           ;Almacenar resultado resta

section .text
    global _start
_start:
                          ;Parte de la suma:
    mov eax, 4            ;Imprimimos frase suma     
    mov ebx, 1  
    mov ecx, msgSuma      ;Frase suma
    mov edx, lenSuma

    int 0x80

    mov eax, 7            ;Almacenamos 7
    mov ebx, 2            ;Almacenamos 2

    add eax, ebx          ;Sumamos 7 y 2
    add eax, '0'          ;Añadimos fin
    mov [ResSuma], eax    ;Almacenamos en ResSuma el resultado

    mov eax, 4            ;Imprimimos resultado suma
    mov ebx, 1
    mov ecx, ResSuma      ;Resultado
    mov edx, 1

    int 0x80
                          ;Parte de la resta:
    mov eax, 4            ;Imprimimos frase resta
    mov ebx, 1
    mov ecx, msgResta     ;Frase resta
    mov edx, lenResta

    int 0x80

    mov eax, 7            ;Almacenamos 7
    mov ebx, 2            ;Almacenamos 2

    sub eax, ebx          ;Restamos 7 y 2
    add eax, '0'          ;Añadimos fin
    mov [ResResta], eax   ;Almacenamos resultado resta

    mov eax, 4            ;Imprimimos resultado resta
    mov ebx, 1
    mov ecx, ResResta     ;Resultado resta
    mov edx, 1

    int 0x80

    mov eax, 4             ;Imprimimos enter
    mov ebx, 1
    mov ecx, finLinea
    mov edx, lenfinLinea

    int 0x80

    mov eax, 1            ;Salimos
    mov ebx, 0

    int 0x80
```

<img src="/images/captura-suma-resta-ejecucion-0.png" />

Bien, seguramente se hayan quedado anonadados, así que vamos a explicar por partes:

```nasm
section .data
msgSuma db "La suma de 7 y 2 es: "
lenSuma equ $ - msgSuma

msgResta db 0xA,0xD,"La resta de 7 y 2 es: "
lenResta equ $ - msgResta
```

Se puede observar que en msgResta, ponemos el salto de línea (0xA, 0xD) delante de la frase en vez de detrás, esto simplemente lo que hará, será meter un salto de línea justo antes de imprimir la frase.  

<br>

```nasm
finLinea db 0xA,0xD,""        
lenfinLinea equ $ - finLinea
```

Aquí lo único que declaramos es un espacio, pero lo importante, es el salto de línea (0xA, 0xD) que declaramos antes, esto nos sirve para que el último resultado no nos salga pegado, en caso de no tener esta parte del código nos quedaría esto:

<img src="/images/pegado-suma-resta-enter-1.png" />

<br>

```nasm
section .bss

ResSuma resb 1            
ResResta resb 1
```

Aquí lo que se hace es declarar los espacios en memoria que necesitaremos, tanto para la suma como para la resta.

<br>

```nasm
    mov eax, 4            ;Imprimimos frase suma     
    mov ebx, 1  
    mov ecx, msgSuma      ;Frase suma
    mov edx, lenSuma

    int 0x80
```

Aquí se imprime la frase "La suma de 7 y 2 es:"

<br>

```nasm
    mov eax, 7            ;Almacenamos 7
    mov ebx, 2            ;Almacenamos 2

    add eax, ebx          ;Sumamos 7 y 2
    add eax, '0'          ;Añadimos fin
    mov [ResSuma], eax    ;Almacenamos en ResSuma el resultado

    mov eax, 4            ;Imprimimos resultado suma
    mov ebx, 1
    mov ecx, ResSuma      ;Resultado
    mov edx, 1

    int 0x80
```

Lo que tenemos en este pedazo es lo que viene siendo la suma y la impresión del resultado. Primeramente pasamos a eax 7 y a ebx 2, lo sumamos y en el registro eax se queda asignado el valor 9. En la instrucción "add eax, '0'" lo que hacemos es sumar el carácter '0' a 9, y lo que quedara será 9'0', es decir, lo convertirá a cadena, porque si recordamos lo que se le tenia que pasar a sys.write:

<img src="/images/sys-write-sys-call-noob.png" />

Para imprimir un carácter, es necesario pasar un dato de tipo char*, si guardaramos el resultado tal cual tendríamos un int. Cuando ejecutamos la instrucción "mov [ResSuma], eax" lo que hacemos es mover el dato 9'0' a la reserva ResSuma, y se preguntaran, ¿y los corchetes para que sirven? Bien, los corchetes indican que se modificara el valor dentro de ResSuma, es decir, si no pusiéramos esto, lo que se modificaría sería la dirección de ResSuma, así en canvio, entramos dentro de esa dirección y asignamos el valor a esa reserva. Y por último imprimimos el resultado de la suma, que seria 9.

<br>

```nasm
    mov eax, 4            ;Imprimimos frase resta
    mov ebx, 1
    mov ecx, msgResta     ;Frase resta
    mov edx, lenResta

    int 0x80
```

Imprimimos "La resta de 7 y 2 es:"

<br>

```nasm
    mov eax, 7            ;Almacenamos 7
    mov ebx, 2            ;Almacenamos 2

    sub eax, ebx          ;Restamos 7 y 2
    add eax, '0'          ;Añadimos fin
    mov [ResResta], eax   ;Almacenamos resultado resta

    mov eax, 4            ;Imprimimos resultado resta
    mov ebx, 1
    mov ecx, ResResta     ;Resultado resta
    mov edx, 1

    int 0x80
```

En esta parte hacemos lo mismo que en la anterior, movemos a EAX y EBX los valores 7 y 2 respectivamente, los restamos y le añadimos el valor '0' para convertirlo a un valor char*. Movemos el resultado a la dirección correspondiente y finalmente imprimimos el valor.

<br>

```nasm
    mov eax, 4             ;Imprimimos enter
    mov ebx, 1
    mov ecx, finLinea
    mov edx, lenfinLinea

    int 0x80

    mov eax, 1            ;Salimos
    mov ebx, 0

    int 0x80
```

Aquí lo que hacemos es imprimir el mensaje finLinea, que lo que hace como hemos comentado antes, es dar un enter para que el resultado no se vea pegado y finalmente finalizar el programa.

<br>

Ahora vamos a jugar un poco, y vamos a modificar en la parte de la suma:

```nasm
    mov eax, 7            ;Almacenamos 7
    mov ebx, 2            ;Almacenamos 2

    add eax, ebx          ;Sumamos 7 y 2
    add eax, '0'          ;Añadimos fin
    mov [ResSuma], eax    ;Almacenamos en ResSuma el resultado
```

Por:

```nasm
mov eax, -7            ;Almacenamos -7
mov ebx, 2            ;Almacenamos 2

add eax, ebx          ;Sumamos -7 y 2
add eax, '0'          ;Añadimos fin
mov [ResSuma], eax    ;Almacenamos en ResSuma el resultado
```

<br>

Si ejecutamos una vez ya cambiado nos encontraremos con lo siguiente:

<img src="/images/captura--7-2-rstapostiivo.png" />

Como podemos ver, hay un error, este error sucede al usar signos, esto pasa tanto sumando como restando, esto se puede solucionar con flags, pero aun no hemos llegado, de todas formas, lo importante en este tutorial es comprender como funciona la suma/resta y como acceder a la memoria para modificar un valor.  
  
