---
layout: post
title: Curso ensamblador 02. Ensambladores y partes de la compilación
date: 2016-12-31
---
--------------------
En el ultimo post vimos el aburrido funcionamiento general de la CPU, en el capítulo de hoy, toca algo mucho menos aburrido, vamos a tratar con los tipos de ensamblador y las fases que tiene un programa antes de ser compilado. Igual que con los lenguajes de más alto nivel, en ensamblador existen diferentes "variantes/lenguajes" cada uno con una syntaxis propia y con unas dependencias que se tienen que cumplir:

<br>

<img src="/images/tipos-ensamblador.png"/>

<br>

Como podemos observar tenemos diferentes ensambladores, [NASM](www.nasm.us) y [FASM](https://flatassembler.net/) son casi lo mismo, tienen soporte para todas las plataformas, en canvio [MASM](http://www.masm32.com/) que es el ensamblador desarrollado por Microsoft, solo tiene para su plataforma. En este curso usaremos **NASM**, si usted esta buscando tutoriales de FASM, MASM o derivados este curso también sirve, la única diferencia entre ellos es una leve cambio de syntaxis. Para obtener el compilador de NASM solo tenemos que ir a su [página](www.nasm.eu) y descargarlo:

**NOTA**: Este curso sera orientado a linux, es conveniente que usted instale linux o tenga una VM, pues hay pequeños detalles que en windows cambian. Para linux, es sencilla la instalación, basta con poner en la terminal:

### Debian/Ubuntu
```
$ sudo apt-get install nasm 
```

### CentOS y derivados
```
$ sudo yum install nasm
```
También se puede compilar el [código fuente](https://github.com/letolabs/nasm).

<br>
<br>

Una vez instalado si teclean en la terminal "nasm" obtendrán algo así:
<img src="/images/primer-comando-nasm.png"/>

<br>

Por tanto, una vez tenemos instalado correctamente NASM vamos a proceder a compilar un programa para ver como funciona. Primero que todo, abre un fichero con cualquier editor de texto, y guarda lo como **Hola-mundo.asm**. Una vez hecho esto, copia y pega este código:
<br>
<br>
**NOTA: No hace falta que comprendas este código, no te dejes engañar, no es complicado, simplemente aun no entiendes como funciona, cuando llegue el momento lo entenderás a la perfección.**

```nasm
section .data 

msg db "Hola mundo", 0xA, 0xD
len equ $ - msg 

section .code 

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

Una vez copiado el código a tu fichero **Hola-mundo.asm** ya estas preparado para convertirlo en un ejecutable. Primero de todo ingresamos lo siguiente a la terminal:

```
$ nasm -f elf Hola-mundo.asm
```

<br>

Si estamos por ejemplo en otro directorio hay que poner la dirección completa a nuestro fichero, es decir:

```
$ nasm -f elf /home/Poyoncio/Documentos/asm/Hola-mundo.asm
```

<br>

Como hemos podido observar el argumento -f indica el tipo de ejecutable que queremos crear, en este caso [elf](https://es.wikipedia.org/wiki/Executable_and_Linkable_Format) que es el ejecutable de linux. Si hemos hecho bien este proceso, nos aparecera un nuevo archivo, llamado **Hola-mundo.o**, es un **archivo de tipo objeto**:

<img src="/images/fichero-hola-mundo-ensamblado.png" />

<br>

Por tanto ahora lo que haremos sera coger todos los recursos que necesitamos (bibliotecas y códigos importados) y los pondrá también dentro del ejecutable. Esta acción se llama **enlazador**:

```
$ ld -m elf_i386 -s -o Hola-mundo Hola-mundo.o
```

<br>

Seguramente os habréis dado cuenta de que en vez de poner elf, hemos puesto elf_i386, esto son simplemente temas de arquitectura, cuando veamos los registros, entraremos en detalle. 

Si hemos hecho bien el proceso nos aparecera un nuevo fichero llamado **Hola-mundo**, este fichero ya seria el ejecutable:

<img src="/images/ld-ejecutable-Hola-mundo.png" />

<br>

Como se puede observar, ya no utilizamos "nasm" porque ya habíamos finalizado el ensamblaje cuando convertimos el código fuente a fichero objeto. Y para ejecutar el programa con un simple **./Hola-mundo** ya nos funcionara:

<img src="/images/ejecutable-hola-mundo-ejecutado.png" />

<br>
<br>

Entonces, vamos a ver en una sola imagen como hemos pasado de tener un fichero de texto plano a un ejecutable:
<br>

<img src="/images/enlazador-esquema-funcionamiento.png" />

<br>

Como podemos observar, el que esta descrito como "paso 2" en la imagen, es cuando hemos ensamblado el código en texto plano a fichero objeto, el paso 3 lo hemos hecho cuando hemos enlazado los recursos necesarios con el fichero objeto mediante el comando "ld" y finalmente el paso 4 es la ejecución.

<br>

Para no tener que ejecutar estos comandos cada vez que quieres compilar un programa, voy a dejar un código en python (v. 2.7) bastante "sucio" para ahorrarnos ese tiempo. Solo hay que poner el código con extensión .py dentro de la carpeta y ejecutarlo con el fichero que queremos ejecutar sin extensión:


```python
import os 
import system 

fichero = str(sys.argv[1])

os.system('nasm -f elf '+fichero+'.asm && ld -m elf_i386 -s -o '+fichero+' '+fichero'+.o)
os.system('./'+fichero)

```
<img src="/images/python-script-auto-hola-mundo.png" />

<br>

En el próximo capítulo, comenzaremos con los registros de uso general para adentrarnos un poco más. La idea de este post, es que hayáis entendido el proceso previo al ejecutable, dejo por [aqui](https://mega.nz/#!W5pghApL!dBj640Ew7oeLIX8CvtyIspfOZkDWntFkbPPYxOZOOD4) el link de descarga del esquema, para que juntéis la imagen con las del post anterior.

