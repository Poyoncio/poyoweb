---
layout: post
title: Curso ensamblador 03. Registros de uso general y la instruccion mov    
date: 2017-01-01
---
--------------------
Ahora que ya sabemos compilar programas, comenzaremos a comprender la lógica de ensamblador. En el día de hoy, toca hablar de **los registros de uso general**, por tanto, ¿Qué son los registros? Los registros son un tipo de memoria que viene integrada en el procesador. Son característicos porque son un tipo de memoria rápida y tienen poca capacidad. Los registros son bastante usados en ensamblador por ejemplo para llevar a cabo operaciones aritméticas. Existen diferentes registros, tales como los registros de datos, de memoria, de propósito específico... Pero los que nos interesa en este capítulo son los de uso general o propósito general, existen diferentes registros de uso general que se clasifican en función de su capacidad:

<br>

<img src="/images/registros-uso-general-grafico.png" />

<br>

Seguramente con este gráfico aun no han entendido  nada, así que vamos a explicarlo paso a paso. Los registros que tenemos se clasifican en función de la capacidad que soportan, hay de 64, 32, 16 y de 8 bits. Bien, si nos fijamos en la primera palabra de cada fila podemos ver que esta formada por "AX" mas un prefijo que va variando en función de su capacidad. En los registros de 64 bits seria "**R**"+"AX", en el de 32 es "**E**"+"AX" y en el de 16 directamente **no hay prefijo**, solo es "AX", por el momento no se fijen en la ultima fila de debajo del todo porque es un caso especial. Como podemos observar no solo AX se repite, sino que además también se repite con "BX", "CX" y "DX". Por tanto tenemos:

<br>

<img src="/images/registros-capacidad-64-32-16.png" />

<br>

Los registros que a nosotros nos interesa durante este curso es el de 32 bits, es decir EAX, EBX, ECX y EDX, ya que haremos programas para esa arquitectura, por eso, en el [anterior post](http://poyoncio.com/2016/12/31/Curso-ensamblador-02-Ensambladores-y-partes-de-la-compilaci%C3%B3n/) cuando creábamos el ejecutable a partir del fichero objeto, poniamos elf_i386, porque elf es el formato ejecutable para linux y [i386](https://es.wikipedia.org/wiki/Intel_80386) la arquitectura que vamos a usar, como podemos observar también del anterior post, en el código fuente, Hola-mundo.asm, utilizábamos EAX, EBX, ECX y EDX.

<br>

Seguramente ya hayan entendido como se clasifican estos registros, entre RAX y EAX, la única diferencia que hay, es la capacidad, tanto RAX como EAX como AX tienen la misma función dentro del código. Hay que comentar que en determinados casos como la división o los loops hay datos que se almacenan por defecto en EAX, EBX, ECX o EDX, pero esto ya lo veremos muchísimo más adelante, el concepto que me interesa que se entienda, es que estos registros son como si fueran variables de un lenguaje de más bajo nivel, es decir que puedes asignarles el valor que quieras, pero que no hace falta inicializarlas, es decir por ejemplo en C++ se hace:

```C
int numero; //Aqui se inicializa
numero = 1;
```

Mientras que en python se hace:

```python
numero = 1  
```


<br>

### Instrucción MOV
Tenia pensado comenzar con las instrucciones en el próximo capítulo, pero para dejar aun mas claro este, comenzaremos con la instrucción más básica, más útil y más simple. La instrucción, es "mov", sirve para dar un determinado valor a una variable. Por ejemplo:

```nasm
  mov variable, 6
```   

<br>

Lo que pasaría en este código, es que a variable se le daría el resultado de 6, es decir en un lenguaje de más bajo nivel como es python el equivalente seria:

```python
  variable = 6   #mov variable, 6   
```

<br>

Por tanto la instrucción "mov" podemos decir que es equivalente a " = ". En mov siempre se asigna el valor de la derecha del todo a la variable de la izquierda, es decir:

```
  mov variable, valor que queremos que tenga
```

<br>

Pero claro, no se puede usar una variable cualquiera, es decir, las variables se tienen que inicializar antes como es el caso de C++:

```C
  int numero;    //Declaramos el tipo de dato
  numero = 1;    //mov numero, 1
```

<br>

Para ver el tema de inicializar las variables, antes se tiene que ver el tema de los segmentos, que lo veremos dentro de poco, por tanto no podemos usar hacer ahora ningún ejemplo con "mov"?? Pues claro que podemos!! Si leemos un poco más arriba, hemos dicho que los registros de uso general (EAX, EBX, ECX y EDX) son **variables que no hace falta inicializar** por tanto vamos a ver un ejemplo:

```nasm
  mov eax, 1  
  mov ebx, 4
  mov ecx, 19
  mov edx, 32
```

<br>

Esta instrucción lo que haría seria "eax = 1", "ebx = 4", "ecx = 19" y "edx = 32". Como podemos ver, los registros de uso general, actúan como variables, no tiene mucho misterio. Si han entendido hasta aquí ya tienen un 50% del camino de programar en ensamblador. Hay que decir también que si pegan el anterior código en un fichero y lo compilan, dará errores, porque faltan diversas partes fundalmentales para que se compile correctamente.

<br>
<br>
<br>

Antes de acabar, me gustaría aclarar, que en un programa compilado para 32 bits no pueden haber instrucciones de 64 bits, pero en canvio dentro de un programa de 32 bits si que pueden haver registros de  16 y 8 bits. También hay que decir que dentro de los registros de uso general existen otros registros como puede ser EDI, ESI, ESP y EBP pero estos ya no son tan sencillos como los que hemos comentado. En el próximo post hablaremos sobre las partes de un programa y la salida/entrada de datos, después del próximo post, ya podremos crear los primeros programas funcionales.  
