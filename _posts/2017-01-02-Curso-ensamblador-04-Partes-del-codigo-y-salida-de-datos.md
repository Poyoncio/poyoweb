---
layout: post
title: Curso ensamblador 04. Partes del código y salida de datos   
date: 2017-01-02
---
--------------------
En el pasado post tratamos con lo básico a cerca de registros de uso general, en este post trataremos de entender el código que pusimos en el [capítulo 2](http://poyoncio.com/2016/12/31/Curso-ensamblador-02-Ensambladores-y-partes-de-la-compilacion/), el código que compilamos que devolvía "Hola mundo", primero, les recomendaría que miraran el código detenidamente, y traten de buscarle la lógica o algún patrón:

```nasm
section .data

msg db "Hola mundo", 0xA, 0xD
len equ $ - msg

section .text

global _start
  _start:

  mov eax, 4
  mov ebx, 1
  mov ecx, msg
  mov edx, len

  int 0x80

  mov eax, 1
  int 0x80
```

<br>

Ahora mismo, seguramente, no comprenderán nada del código, pero les aseguro, es más, les garantizo 100% que cuando terminen de leer este post lo comprenderán, tal y como me paso a mi, así que comencemos despedazando el código:

<br>

Como podemos observar, tenemos dos partes fundamentales del código, la "section .data" y la "section .text", estas dos partes se llaman, **secciones**, todo programa hecho en ensamblador está dividido en secciones. Una sección no es más que un bloque de código, que se diferencia de las demás por el tipo de espacio en el que se tiene que guardar, unas secciones acceden a la memoria RAM, otras a la FLASH, y así, no especificaremos a que tipo de memoria accede cada sección pues es totalmente innecesario. Bien, pues como supongo que han deducido, la **seccion ".data" almacena variables**, y la **sección ".text" almacena las instrucciones** de nuestro programa. Por tanto, nada más comenzar podemos definir una estructura:

```nasm
section .data
    variables

section .text
    Instrucciones de nuestro programa

```

<br>
<br>

Ahora vamos a mirar detenidamente el código dentro de la sección ".data", más concretamente en la primera línea, hay una etiqueta, llamada "msg" seguida de "db" más la frase y unos dígitos al final del código (0xA y 0xD):

```nasm
msg db "Hola Mundo", 0xA, 0xD
```


Bien, lo que hace este código básicamente es asignar a "msg" la frase "Hola mundo", es decir:
```python
msg = "Hola Mundo"
```

Lo que hace "db" es algo parecido a "mov", realmente "db" es el encargado de declarar las variables, tal y como comentábamos en el [post anterior](http://poyoncio.com/2017/01/01/Curso-ensamblador-03-Registros-de-uso-general-y-mov/), lo que hace es que msg tome el valor de "Hola Mundo" y os preguntareis, ¿y no sería mejor hacer "mov msg, 'Hola Mundo'"? Pues no, porque nos devolvería un error, como dijimos, las variables se tienen que declarar antes, por tanto, si que se podría declarar "msg db 'Hola Mundo'" y luego en la sección .text poner "mov eax, msg", más adelante veremos los tipos de variables que se pueden declarar y otras variantes de db, con el concepto que nos tenemos que quedar es con que lo que se hace en esa línea es "msg = 'Hola Mundo'".

#### Código en nasm:

```nasm
msg db "Hola Mundo"
```
#### Código en python:

```python
msg = "Hola Mundo"
```

<br>

Seguramente se sigan preguntando, y los caracteres 0xA y 0xD, ¿Para que sirven?, esos caracteres, son caracteres de tipo [hexadecimal](https://es.wikipedia.org/wiki/Sistema_hexadecimal), si van a cualquier [calculadora online](http://es.calcuworld.com/calculadoras-matematicas/calculadora-hexadecimal/) que pase de caracteres hexadecimales a décimales, se darán cuenta que equivalen a 10 y 13 respectivamente. Y algunos pueden pensar, "Vaya, esto seguro que es lo más complicado del código", **pero no!!** Es la parte más sencilla del código, a lo que realmente equivalen 0xA y 0xD es a un simple salto de línea, es decir, a un "\n", ¿Complicado? Esta sería la equivalencia:

#### Código en nasm:

```nasm
msg db "Hola mundo", 0xA, 0xD
```
#### Código en python:

```python
msg = "Hola mundo \n"
```

Cuando ponemos "msg db 'Hola Mundo', 0xA, 0xD" también podríamos poner "msg db 'Hola Mundo', 10, 13". Es decir se pueden poner tanto caracteres hexadecimales como decimales, el código no nos dara error, pero es conveniente por temas de estética ponerlo en hexadecimal. Pero ahora vamos a ver lo que realmente hacen estos caracteres, vamos a jugar un poco con ellos. Lo que vamos a hacer va a ser, hacer que nuestro código imprima 2 veces por pantalla el mensaje "Hola Mundo" y vamos a borrar los caracteres 0xA y 0xD:

```nasm
section .data
msg db "Hola mundo"
len equ $ - msg

section .text
global _start
  _start:

  mov eax, 4        
  mov ebx, 1
  mov ecx, msg
  mov edx, len

  int 0x80

  mov eax, 4
  mov ebx, 1
  mov ecx, msg
  mov edx, len

  int 0x80

  mov eax, 1
  int 0x80
```

Y este sería el resultado:

<img src="/images/0xd-0xa-hola-mundo-analisis.png" />

Como se puede observar, al no haber los caracteres 0xA y 0xD el programa imprimirá seguido. No explicaremos más a cerca de estos caracteres pues lo veo innecesario.

<br>

Ahora vamos a desplazarnos a la segunda línea de la sección data:

```nasm
len equ $ - msg
```

Como podemos observar se trata de una estructura parecida a la línea anterior. Lo que básicamente se almacena en "len" es la longitud de la variable msg. Si nos fijamos en la primera línea que hemos analizado, se declaraba la variable con el operador "db", en este caso lo declaramos con "equ", la principal diferencia entre "db" y "equ" es que uno es una variable y el otro una constante, ¿Que diferencia hay? Ya lo dice el nombre, una **variable (db), es un valor que durante la ejecución del programa puede tomar varios valores**, mientras que una **constante (equ), es un tipo de operador que no permite asignar otro valor diferente al inicial**, es decir, no se puede modificar. Como ya he mencionado, el tema de variables y constantes aún se tiene que ver. Volviendo a la segunda línea, si nos fijamos lo que hace es restar msg a el símbolo del dolar($), esto devolverá un valor de tipo [entero](https://es.wikipedia.org/wiki/N%C3%BAmero_entero) (negativo). Seguramente se preguntarán,  ¿Y para que queremos la longitud? El programa necesita ese dato para imprimir el mensaje almacenado en msg. Ese dato también se puede calcular, si contamos "Hola mundo" podemos contar 10 palabras+espacios, por tanto también podríamos poner:

```nasm
len equ 10
```

Pero seguramente, cuando tengamos un programa más complejo, no nos apetecerá contar las palabras de cada string, quiero que entiendan esta línea bien, imagínense que el símbolo $ es igual a 0 y que msg es igual al número de caracteres/espacios que ocupa, entonces realizamos la operación:

```nasm
len = 0 - 10
```

Como podemos observar, esto nos devolverá un len = -10, pero este valor, por razones que vamos a ignorar es de tipo unsigned int, es decir entero sin signo, lo que significa que es un entero con símbolo positivo si o si, por tanto nos quedara en la constante len un valor de 10.

<br>

Bien, vamos a hacer un breve repaso. Lo que hemos hecho en la sección .data principalmente ha sido asignarle "Hola mundo" a msg, y luego obtener una constante con la longitud de msg, ¿**Parecia** difícil verdad?.

<br>

Ahora vamos a atacar la sección .text, lo primero que podemos ver, es lo que parece la declaración de una etiqueta global y su posterior comienzo:

```nasm
global _start
  _start:
```

Este tipo de instrucción se pone para indicar el comienzo del código. Algo así como el main en C++:
#### Código en C++:
```c
int main()
{
  codigo
}
```

#### Código en nasm:
```nasm
global _start
  _start:

  codigo
```

Se utiliza para indicar al compilador dónde comienza nuestro código. Es recomendable darle una tabulación tal y como se pone en el ejemplo ya que se ve más fácilmente. Al igual que el main, la etiqueta de inicio es esa, es decir no la podemos cambiar, es un valor reservado para ese uso, si tratamos por ejemplo en la sección .data de poner:

```nasm
section .data
_start db "Hola mundo", 0xA, 0xD
```

Nos dará el siguiente error:

<img src="/images/error-start-hola-mundo-segmento.png" />

Lo que básicamente indica es que se ha tratado de asignar un nuevo valor a la etiqueta start. No hay mucho más que entender, así que sigamos, la siguiente porción de código es esta:

```nasm
  mov eax, 4        
  mov ebx, 1
  mov ecx, msg
  mov edx, len
```

Seguramente algunos hayan deducido, que esta es la parte que imprime dos veces por pantalla el mensaje, porque antes, cuando jugábamos con 0xA y 0xD en el ejemplo de "hola mundo" seguido, lo único que hicimos fue poner esta parte de código dos veces. Es muy probable que se pregunten, como puede ser que "eax = 4", "ebx = 1", "ecx = 'Hola mundo'" y "edx = 10" sean capaces de imprimir por pantalla? Muy sencillo, esto forma parte de una API que se comunica con el kernel. Esta parte resulta algo tediosa, pero la haremos lo más liviano posible, así que vamos a usar un gráfico:

<img src="/images/sys-calls-personalizado.png" />

Como podemos observar tenemos distintas llamadas al sistema, la intención de hoy es trabajar con el output, así que no hace falta ni mucho menos aprenderse toda la tabla. Como iba diciendo, hay diversas llamadas al sistema, como se puede ver, dependen de EAX, es decir, que si tenemos un "mov eax, 4" la llamada que esta realizando es a sys.write, en este caso, escribirá por consola, vamos a ver detenidamente esa fila concretamente:

<img src="/images/sys-write-sys-call-noob.png" />

Como se puede observar, no solo se tiene que realizar ese "mov eax, 4", sino que hay unos parametros que se tienen que pasar, estos parametros tienen que ser más concretamente EBX que ser 1, ECX tiene que ser una constante de tipo char (letras) y EDX de tipo size_t. Si googlean un poco, podrán ver que size_t es lo mismo que un unsigned int, y que por tanto es un int positivo. Si miramos de nuevo el código podemos ver, que lo que requiere lo tenemos:

```nasm
  mov eax, 4
  mov ebx, 1
  mov ecx, msg
  mov edx, len
```

Pasamos el 4 de sys.write a EAX, pasamos el 1 requerido como parametro a EBX, ponemos el mensaje de tipo char en ECX y finalmente el valor de la longitud del msg a EDX, en C++ la función quedaría parecido a esto:

```c
  sys.write(1, msg, len)
```

Más concretamente:

```c
  sys.write(int numero, const char* mensaje, size_t longitud) //Declaramos funcion
  {
    Imprime el msg por pantalla
  };

  sys.write(1, msg, len); //Llamamos a la funcion
```

<br>

El tema de las llamadas al sistema cuesta un poco al principio, pero es qüestion de entenderlo, difícil no es. Así que sigamos avanzando un poco más de código, ya estamos casi en el final:

```nasm
  int 0x80
```

Aquí lo que vemos es lo que se llama una [interrupción](https://es.wikipedia.org/wiki/Int_80h). Una **interrupción** es una parada del programa para "dialogar" con el kernel. Es muy habitual usarlos cuando se hacen "system calls" que es la tabla grande que hemos puesto anteriormente.  Por tanto, lo que hace "int 0x80" es enviar ese "eax = 4" al sistema junto a los parametros al sistema, es el mensajero por decirlo de alguna manera.

Imaginemos que nosotros estamos haciendo operaciones aritméticas, y que queremos sumar 4 + 5, pues le haríamos "mov eax, 4" y "mov ebx, 5", aun ni siquiera hemos realizado la suma, y el sistema ya ha dado fallo porque no hemos pasado los parametros del mensaje y de la longitud, pues por eso se invento "int 0x80", para poder sumar tranquilamente, y en caso de querer imprimir algo poner la interrupción, es decir:

```nasm
  mov eax, 4
  mov ebx, 5
  add eax, ebx    ;Aqui sumamos
```

```nasm
  mov eax, 4      ;sys.write
  mov ebx, 1      ;1
  mov ecx, msg    ;Mensaje
  mov edx, len    ;Longitud  

  int 0x80        ;Interrupcion que hace que se envie
```  

<br>

Espero que haya quedado suficientemente claro el código, así que sigamos:

```nasm
  mov eax, 1

  int 0x80
```

Realmente que tenemos aquí? Pues vamos a ver a la tabla:

<img src="/images/sys-call-exit-noob.png" />

Podemos ver que es un sys.exit, es decir, para finalizar el programa, en la tabla podemos observar que el valor de EBX tiene que ser 0, y que los parametros de ECX y EDX no se necesitan. Pero si os fiajáis, nosotros solo tenemos puesto EAX como 1 y EBX no lo hemos puesto. Realmente es aconsejable poner "mov ebx, 0" porque lo que envía al sistema, es que el programa se ha cerrado, pero con exito, es decir, que no se ha cerrado por un error, no suele dar errores no poner "mov ebx, 0", pero son "buenos modales".

<br>

Supongo que por hoy ya estarán aburridos, así que les dejo el código comentado para que repasen y se tomen una cerveza, se la merecen!!

```nasm
section .data                   ;Seccion variables

msg db "Hola mundo", 0xA, 0xD   ;Le asignamos una string a msg (devuelve const char*)
len equ $ - msg                 ;Calculamos la longitud de msg (devuelve size_t o int)

section .text                   ;Seccion de codigo

global _start                   ;Declaramos la etiqueta como global
  _start:                       ;Iniciamos la etiqueta

  mov eax, 4                    ;Sys.write()
  mov ebx, 1                    ;1
  mov ecx, msg                  ;String
  mov edx, len                  ;Longitud

  int 0x80                      ;Enviamos al sistema

  mov eax, 1                    ;Sys.exit()
  int 0x80                      ;Enviamos al sistema
```

<br>

Supongo que todo el mundo lo habrá entendido, dejo por [aquí](https://mega.nz/#!ex4ABLaL!d7q9xyAPBZGWhDZDtEuILjQ2l8htSxwZF8Maf7JDAvI) la tabla para que la junten a sus anteriores apuntes ¿No era tan complicado como parecía verdad?.

**NOTA**: En caso de que no hayan entendido algo no se preocupen, este tipo de cosas se repetirán durante todo el curso.
