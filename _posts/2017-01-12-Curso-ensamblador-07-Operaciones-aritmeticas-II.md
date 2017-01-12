---
layout: post
title: Curso ensamblador 07. Operacions aritméticas (II)   
date: 2017-01-12
---
--------------------
En el post de hoy seguiremos con las operaciones aritméticas, en este caso, seguiremos con multiplicación y división.


### Multiplicación:
Las multiplicaciones son parecidas a la suma y a la resta, utilizan el operador "mul" y se representa de la siguiente forma:

```nasm
    mov eax, 2
    mov ebx, 2

    mul ebx
```

Como se puede observar se declara EAX, se declara EBX y luego en la multiplicación solo se pone EBX, ya que el lugar por defecto donde se depositara el resultado será EAX, es decir, **siempre se usara EAX como lugar de destino del resultado**, veamos un ejemplo en python:

```python
eax = 2
ebx = 2

eax = eax * ebx
```

No hay mucho misterio, por otra parte, decir que también existe el operador "imul", este a diferencia de "mul" nos permite hacer multiplicación con signos, con saber que existe "imul" es suficiente.

<br>

### División:
Las divisiones se llevan a cabo con el operador "div", y se ejecutan como cualquier división, pero hay que tener un orden en cuenta, el dividendo se guarda en EAX, el divisor en EBX, y el residuo se guardara por defecto en EDX. El cociente de la división se almacenara en EAX, hay que especificar, que el resultado se almacenara sin decimales, es decir, si nuestro cociente es 6,5, se almacenara 6 o 7, pero no 6,5.

```nasm
mov eax, 8                          
mov ebx, 2                               
mov edx, 0                          

div ebx
```

Como hemos podido ver en el ejemplo anterior movemos el valor 8 al dividendo, 2 al divisor y finalmente ponemos a EDX a 0 para que no nos de errores. Luego solo ponemos "div ebx" para que se ejecute, en python sería algo así:

```python
eax = 8
ebx = 2

eax = eax/ebx
```

En las multiplicaciones comentamos que existe el operador imul, y que servía para hacer operaciones con signo, pero que no era muy usado porque si hacemos una multiplicación con signo, "mul" no da error y devuelve el resultado correcto, en canvio, con "idiv" es diferente, si tratamos de hacer una división con signo mediante "div", nos arrojara un resultado erroneo, más abajo mostraremos un ejemplo.


<br>
<br>

Este sería el código correspondiente:

```nasm
section .data
msgMult db "La multiplicacion de 2*2 es: "
lenMult equ $ - msgMult

msgDiv db 0xA,0xD,"El cociente de 8/2 es: "
lenDiv equ $ - msgDiv

msgDivResiduo db 0xA,0xD, "El residuo de 8/2 es: "
lenDivResiduo equ $ -msgDivResiduo

finLinea db 0xA,0xD,""
lenfinLinea equ $ - finLinea

section .bss
ResMult resb 1
ResDiv resb 1
ResDivResiduo resb 1

section .text
    global _start
_start:

    mov eax, 4
    mov ebx, 1
    mov ecx, msgMult
    mov edx, lenMult

    int 0x80

    mov eax, 2
    mov ebx, 2

    mul ebx
    add eax, '0'
    mov [ResMult], eax

    mov eax, 4
    mov ebx, 1
    mov ecx, ResMult
    mov edx, 1

    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, msgDiv
    mov edx, lenDiv

    int 0x80

    mov eax, 8
    mov ebx, 2
    mov edx, 0

    div ebx

    add eax, '0'
    mov [ResDiv], eax

    add edx, '0'
    mov [ResDivResiduo], edx

    mov eax, 4
    mov ebx, 1
    mov ecx, ResDiv
    mov edx, 1

    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, msgDivResiduo
    mov edx, lenDivResiduo

    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, ResDivResiduo
    mov edx, 1

    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, finLinea
    mov edx, lenfinLinea

    int 0x80

    mov eax, 1
    mov ebx, 0

    int 0x80

```

<img src="/images/captura-07-mul-imul-idiv-div-ejemplo.png" />

<br>

Como podemos observar, lo único que hemos cambiado ha sido las strings y los operadores, no hay mucho que comentar, ya que ya comentamos todo esto en el [anterior capítulo](http://poyoncio.com/2017/01/08/Curso-ensamblador-06-Operaciones-aritmeticas-I/), lo único que comentaremos serán las cosas básicas:

```nasm
mov eax, 8
mov ebx, 2
mov edx, 0

div ebx

add eax, '0'
mov [ResDiv], eax

add edx, '0'
mov [ResDivResiduo], edx
```

Como se puede observar, le pasamos el valor 0 a EDX para evitar errores y una vez hecha ya la division con "div ebx" guardamos tanto EAX como EDX y más tarde se imprimirán los dos valores.

Bien, ya hemos comentado la única parte en la que podían surgir dudas, así que ahora vamos a hacer pruebas, primero que todo, vamos a cambiar los signos de la multiplicación:

```nasm
    mov eax, 2
    mov ebx, 2

    mul ebx
```

Por:

```nasm
    mov eax, -2
    mov ebx, 2

    mul ebx
```


Si ejecutamos bien este programa nos tendría que devolver lo siguiente:

<img src="/images/captura-mul--2-resultado.png" />


Si miramos la tabla ASCII podremos observar que nos da el resultado que esperábamos, es decir -4, como hemos podido ver, no hace falta usar "imul" ya que con "mul" ya nos devuelve el valor. Ahora vamos a cambiar los signos de la división:

```nasm
mov eax, 8
mov ebx, 2
mov edx, 0

div ebx
```

Por:

```nasm
    mov eax, 8
    mov ebx, -2
    mov edx, 0

    div ebx
```

Si lo ejecutamos correctamente nos devolverá:

<img src="/images/captura-div--8-bads.png" />

Como podemos ver, el resultado que tendría que devolver sería -4, es decir una ",", pero como podemos observar nos devuelve un cero. Para solucionar esto tendremos que usar el operador "idiv", que se encargara de tener en cuenta los signos, por tanto vamos a cambiar el "div" por "idiv" y nos saldrá lo siguiente:

<img src="/images/captura-idiv--4-coma.png" />

<br>

Como podemos ver, ahora nos arroja el resultado correcto. Hasta aquí el post de hoy, realmente es bastante sencillo el tema de la aritmética, en el próximo post ya veremos incremento/decremento y operaciones con ingreso por pantalla, y con eso, daremos por finalizado la aritmética y comenzaremos con los flags. 
