En el [anterior post](http://poyoncio.com/2017/02/03/Curso-ensamblador-10-Stack/) vimos lo que era el Stack, y cuales eran los operadores básicos, en el post de hoy, veremos los registros del stack, también conocido como **registros de índice**.

Tal y como se comento anteriormente en el post de los [registros de uso general](http://poyoncio.com/2017/01/01/Curso-ensamblador-03-Registros-de-uso-general-y-mov/), existen diversos registros, diferenciados por unas cualidades especiales que poseen. Los que hoy trataremos son esenciales para el correcto funcionamiento del stack y son muy utilizados en los lenguajes de más alto nivel derivados de ensamblador. Así que, sin más dilación, demos paso a estos registros.

Los registros de índice están formados por ```ESI```, ```EDI```, ```EBP``` y ```ESP```, pero los que nosotros veremos hoy, son los dos últimos, ```EBP``` y ```ESP```:

<br>

###ESP (Apuntador de pila)
Este operador se encarga de apuntar a el último elemento introducido en la pila, es decir, cada vez que se añade un nuevo valor a la pila, este registro aumenta, sabiendo a cada momento cual es el "TOP". Es un operador muy importante, ya que ayuda a llevar orden en nuestro código. Más adelante veremos ejemplos.

<br>

###EBP (Apuntador de base)
Este operador se encarga de almacenar una posición concreta del stack, es decir, se guarda la posición del stack, y a partir de ahí, podemos acceder a otros valores, que esten por debajo o por encima suyo. Seguramente no lo hayan terminado de entender, así que vamos a explicarlo con una imagen:

<img src="/images/cap-png-pointer-ebp-image-V-ebp.png" />

Como se puede observar, almacenamos ```V``` y encima ponemos ```EBP```, por tanto, esto nos permitirá acceder al valor ```V``` mediante una simple operación aritmética, realmente, nos es tal cual lo explico, es decir, hay unos procesos internos más complejos, ahora trato de que se comprenda para que sirve cada uno.


<br>

Una vez hecha la parte aburrida, vamos a ver código:

```nasm

section .text
saltoDeLinea db "",0xA,0xD
lensaltoDeLinea equ $ - saltoDeLinea

section .data
    global _start
_start:

    mov ebp, esp              ;Asignamos a ebp el valor mas alto actual

    mov eax, 4                ;sys.write
    mov ebx, 1
    mov ecx, [ebp + 8]       ;Pasamos a ECX el valor que esta 2 veces por
    mov edx, 5               ;encima de ebp

    int 0x80

    mov eax, 4                ;sys.write
    mov ebx, 1
    mov ecx, saltoDeLinea     ;enter
    mov edx, lensaltoDeLinea

    int 0x80

    mov eax, 1                 ;sys.exit
    mov ebx, 0

    int 0x80
```

Bien, este programa lo que hará será imprimir una string que enviemos por la terminal como parametro con una longitud máxima de 5 caracteres. Primero compilamos y luego:

<img src="/images/captura-comp-stack-init---seg-ebp-esp" />  

Vamos a analizar el código:

```nasm
    mov ebp, esp
```

Bien, esta parte lo que hace es mover el valor actual de ```ESP``` a ```EBP``` es decir, asignar a ```EBP``` la dirección más alta en el stack. Puede resultar difícil de imaginar, pero no se preocupen, en el próximo tutorial lo detallaremos un poco más.

A continuación tenemos lo siguiente:

```nasm
    mov eax, 4
    mov ebx, 1
    mov ecx, [ebp + 8]
    mov edx, 5

    int 0x80
```

Bien, aquí podemos ver una simple llamada al sistema para imprimir por pantalla. Como se puede observar, todo es normal, hasta llegar a  ```mov ecx, [ebp + 8]```. Como comentábamos un poco más arriba, en ```EBP``` tenemos guardado la dirección del valor guardado más alto en el stack, y ustedes se preguntaran, y porque sumas a ```EBP``` el valor 8 y luego lo pasas a ```ECX``` con ```MOV```? Bien, pues realmente no se suman 8 números como tal, para comprender esto hay que entender el funcionamiento a un poco más bajo nivel. Cuando se guarda un valor en la pila, se reservan 4 bytes, es decir, si queremos acceder al primer valor tendríamos que hacer ```[ebp + 4]```, al segundo ```[ebp + 8]```, al tercero ```[ebp + 12]``` y así sucesivamente, y ustedes se preguntaran, ¿Que nos esta contando este loco?¿Cuando hemos guardado la string pasada por linea de comando en la pila? Bien, pues esto sucede por defecto, es decir, cuando iniciamos nuestro programa, por defecto en la pila tenemos lo siguiente:

<img src="stack-asm-start-line-parameterscode-x.png" />

Para leer esta imagen, lo tenemos que hacer de abajo a arriba. Como podemos ver, lo primero que tenemos es la dirección de ```ESP```, en nuestro caso, lo que tenemos es ```EBP``` ya que cuando realizamos el ```mov ebp, esp``` le pasamos la dirección, a continuación en "Adress program path" tenemos lo que viene siendo lo que usamos para ejecutar nuestro programa, es decir, en nuestro caso sería ```./pila```, si modificamos en nuestro código la parte de ```mov ecx, [ebp + 8]``` por ```mov ecx, [ebp + 4]``` nos devolverá el dato que hemos usado para ejecutar el programa, es decir, en mi caso el programa esta en ~/Documentos/asm/pila, bien, pues si desde ~/Documentos ejecuto ```./asm/pila``` lo que nos devolverá será ```./asm/pila```, también hay que destacar que tenemos que modificar el buffer de ```EDX``` para que imprima todos los caracteres, pueden probar ustedes mismos, aquí pueden ver un ejemplo:


<img src="captura-ejemplo-asmpila-doc-ebp4.png" />


Bien, si seguimos mirando la imagen del stack, podemos ver que lo siguiente son los argumentos pasados por línea de comandos, como se puede ver, hay "Adress of Arg 1", "Adress of Arg 2" y así sucesivamente, bien, nosotros mediante ```[ebp + 8]``` accedemos al argumento 1, con ```[ebp + 12]``` accedemos al argumento 2, eso en caso de que hayamos ingresado 2 argumentos, pero como nosotros hemos ingresado solo 1 ese ```[ebp + 12]``` corresponderá a los 4 bytes nulos que vienen a continuación en la imagen.

<br>

Bien, espero que lo hayan entendido, lo que falta por analizar es un simple salto de línea, para que quede un poco más estético y la llamada para finalizar el proceso. Lo que me interesa que comprendan de este tutorial, es comenzar comprendar el funcionamiento de la pila, y como se debe de usar. Obviamente, no hemos comentado, que los ```[ ]``` son para mover el valor dentro de esa dirección, pero por si acaso lo aclaro.

<br>

Puede resultar un poco complejo el tema de la pila cuando se inicia el programa, pero no se preocupen, seguiremos viendo como tratar con la pila en el próximo capítulo con el **stack frame**.  
