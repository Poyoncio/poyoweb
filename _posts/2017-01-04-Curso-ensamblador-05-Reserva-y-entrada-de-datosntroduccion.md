---
layout: post
title: Curso ensamblador 05. Reserva y entrada de datos  
date: 2017-01-04
---
--------------------
En el anterior post vimos como funcionaba la salida de datos, en esta ocasion veremos como funciona la entrada de datos. Vamos a recordar la tabla que vimos en el anterior post:

<img src="/images/sys-calls-personalizado.png" />

Si nos fijamos solo en la entrada:

<img src="/images/sys-read-noob-5.png" />

Se puede observar que necesitaremos en EBX un 2, pues es la entrada estándar, en ECX una entrada de tipo char y en EDX la longitud que tendrá. Antes de comenzar a programar esta parte me gustaría enseñarles una nueva sección, en el [anterior capítulo](http://poyoncio.com/2017/01/02/Curso-ensamblador-04-Partes-del-codigo-y-salida-de-datos) vimos las secciones ".text" y ".data", pero no solo existen estas dos, hoy vamos a tratar con la **sección ".bss"**. La sección ".bss" o de reserva es una seccion donde se reserva espacio para usar, si nos fijamos en el Hola-mundo, teníamos en la seccion .data una declaración tal que así:

```nasm
section .data
msg db "Hola mundo"
```

Bien, como podemos observar, lo que se hace es declarar una variable y asignarle una string, pero lo que nosotros necesitamos es reservar un espacio vació para almacenar el dato que ingresemos, y por eso se invento la sección de reserva, su operador es "resb" y se puede recordar fácilmente como "reserva de bytes", cumple la misma función que memset de C:

#### Código en C:
```c
reserva = (char *) malloc(5);
```

#### Código en nasm:
```nasm
section .bss
reserva resb 5
```

Lo que hemos hecho aquí, ha sido declarar la sección y reservar 5 bytes, es decir lo que equivale a 5 caracteres. Por defecto cuando ingresemos los datos serán de tipos char*, es decir, si ingresamos números, no se podrá operar con ellos tal cual. Espero que se haya entendido, así que vamos a comenzar a programar la entrada de datos por partes, lo que vamos a pedir va a ser que ingrese varios números y los imprima por pantalla, primero el código de la sección data:

```nasm
section .data
msg db "Ingrese numeros: "
len equ $ - msg

msg2 db "Los numeros son: "
len2 equ $ - msg2
```

Declaramos una variable con el mensaje de ingreso, y otro con el de salida. Ahora vamos a reservar el espacio que necesitaremos, en mi caso voy a reservar 5 espacios:

```nasm
section .bss
reserva resb 5
```

A continuación programamos la sección .text:

```nasm
section .text     
global _start:     ;Declaramos la etiqueta de inicio
  _start:          ;Iniciamos el programa

  mov eax, 4       ;sys.write
  mov ebx, 1       ;Salida estandar
  mov ecx, msg     ;Pasamos el mensaje
  mov edx, len     ;Pasamos su longitud

  int 0x80         ;Lo enviamos al sistema
```

Lo que hemos hecho hasta aquí ha sido comenzar el programa y imprimir por pantalla msg. Así que ahora vamos a programar la entrada:

```nasm
  mov eax, 3        ;sys.read
  mov ebx, 2        ;Entrada estandar
  mov ecx, reserva  ;Espacio donde se depositara
  mov edx, 5        ;Total de caracteres permitidos

  int 0x80          ;Lo enviamos al sistema
```

Bien, este pedazo de código vamos a mirarlo más detenidamente, como podemos observar, pasamos a EAX el sys.write, en EBX ponemos la entrada estándar que es 2, en ECX ponemos el espacio donde se depositaran los caracteres, que por defecto nuestra reserva ya es de tipo char* y en EDX ponemos el máximo de caracteres que se aceptaran. La parte más importante ya esta hecha, así que ahora vamos a poner lo que falta:

```nasm
  mov eax, 4       ;sys.write
  mov ebx, 1       ;Salida estandar
  mov ecx, msg2    ;Mensaje 2
  mov edx, len2    ;Longitud 2

  int 0x80         ;Interrupcion

  mov eax, 4       ;sys.write
  mov ebx, 1       ;Salida estandar
  mov ecx, reserva ;Imprimimos los numeros
  mov edx, 5       ;Pasamos la longitud maxima del mensaje

  int 0x80         ;Interrupcion

  mov eax, 1       ;sys.exit
  mov ebx, 0       ;Salida exitosa

  int 0x80         ;Interrupcion

```  

Lo que básicamente hacemos es imprimir el mensaje 2, luego el numero ingresado y finalmente parar el programa. Dejo el código completo por si alguien tiene algún error:

```nasm
section .data
msg db "Ingrese numeros: "
len equ $ - msg

msg2 db "Los numeros son: "
len2 equ $ - msg2

section .bss
reserva resb 5

section .text     
global _start:      
  _start:          

  mov eax, 4       
  mov ebx, 1     
  mov ecx, msg    
  mov edx, len     

  int 0x80       

  mov eax, 3        
  mov ebx, 2        
  mov ecx, reserva  
  mov edx, 5        

  int 0x80          

  mov eax, 4      
  mov ebx, 1       
  mov ecx, msg2    
  mov edx, len2    

  int 0x80         

  mov eax, 4       
  mov ebx, 1        
  mov ecx, reserva
  mov edx, 5       

  int 0x80         

  mov eax, 1     
  mov ebx, 0       

  int 0x80        
```

Bien, ahora vamos a compilarlo y insertar números:

<img src="/images/input-asm-1234-reserva.png" />

Esto es lo que aparece cuando ponemos 4 caracteres, funciona bien, pero ahora vamos a jugar un poco, vamos a ver que pasa si ponemos el mismo numero de caracteres que de espacio reservado:

<img src="/images/reserva-asm-5-input.png" />

Fijaros bien en el resultado, no, no es que haya dado un enter despues de haber ejecutado el programa, este error se debe a lo que habíamos mencionado anteriormente en la reserva de bytes, si miramos de nuevo la función sys.read:

<img src="/images/sys-read-noob-5.png" />

Se puede ver que en ECX lo que devuelve es un dato de tipo char*, por tanto lo que realmente tenemos es 4 bytes de espacio, porque el último carácter tiene que ser "0", que es el que marca el final de la cadena y de linea por defecto, por eso nos aparecen los números seguidos del prompt:

<img src="/images/seguido-prompt-reserva.png" />

Es decir, hay 5 espacios, el ultimo por defecto tiene que ser "0", pero si introducimos 5 caracteres, "0" queda fuera y ese salto de línea se ejecuta en mi terminal:

<img src="/images/vulnerable-5-asm-input.png" />

 Por tanto, quedan los números, despues justo al lado el prompt que ha quedado como si le hubiera dado un enter, y en la línea de abajo otro prompt que es el que ya se puede utilizar, el primer prompt ejecuta el salto de línea y por tanto ese ya no lo uso yo. sale el mensaje seguido, bien, ahora vamos a ver que pasa si introducimos varios números de mas:

<img src="/images/reserva-numeros-de-mas-asm-resb.png" />

Como se puede observar, lo que pasa en la última prueba es que se ocupan los 5 bytes de espacio y por tanto 6789 mas el salto de línea pasan al prompt y se ejecuta el comando 6789. Es decir, el problema con el salto de línea no es problema de la reserva, si no que es problema de el número de caracteres que hemos puesto, es decir, si en la entrada de datos, modificamos el "mov edx, 5" por "mov edx, 10", esto nos dará un margen más grande:

<img src="/images/reserva-10-asm-noob-05.png" />  

Para solucionar este problema tendríamos que tener más nivel, así que por el momento lo dejaremos de lado, lo que me interesa es que hayan comprendido como funciona la reserva de memoria y como se ingresan datos por pantalla. En el próximo post veremos como realizar operaciones matemáticas básicas.
