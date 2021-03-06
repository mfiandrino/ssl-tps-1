# Fases de la Traducción y Errores

## 1. Objetivos
* Este trabajo tiene como objetivo identificar las fases del proceso de traducción o Build y los posibles errores asociados a cada fase.
* Para lograr esa identificación se ejecutan las fases de traducción una a una, se detectan y corrigen errores, y se registran las conclusiones en readme.md.
* No es un trabajo de desarrollo; es más, el programa que usamos como ejemplo es simple, similar a hello.c pero con errores que se deben corregir. La complejidad está en la identificación y comprensión de las etapas y sus productos.

## 2. Temas
* Fases de traducción
* Preprocesamiento
* Compilación
* Ensamblado
* Vinculación (Link)
* Errores en cada fase
* Compilación separada

## 3. Tareas
* La primera tarea es investigar las funcionalidades y opciones que su
compilador presenta para limitar el inicio y fin de las fases de traducción.
* La siguiente tarea es poner en uso lo que se encontró. Para eso se debe
transcribir al readme.md cada comando ejecutado y su resultado o error
correspondiente a la siguiente secuencia de pasos. También en readme.md se
vuelcan las conclusiones y se resuelven los puntos solicitados. Para claridad,
mantener en readme.md la misma numeración de la secuencia de pasos.

### 3.1. Secuencia de pasos
Se parte de un archivo fuente que es corregido y refinado en sucesivos pasos.
Es importante no saltearse pasos para mantener la correlación, ya que el estado
dejado por el paso anterior es necesario para el siguiente.  
  
  #### 1. Preprocesador  
  a) Escribir hello2.c, que es una variante de hello.c:
  ~~~
  #include <stdio.h>
  int/*medio*/main(void){
  int i=42;
  prontf("La respuesta es %d\n");
  ~~~
  b) Preprocesar hello2.c, no compilar, y generar hello2.i. Analizar su contenido. ¿Qué conclusiones saca?  
     
  Para solo preprocesar el archivo fuente tuve que agregar el flag -E al compilador gcc. Comando: "gcc hello2.c -E -std=c18 -o hello2.i"
  
  Una vez preprocesado el archivo fuente, se puede ver que no hubo errores en tiempo de preprocesamiento. Lo que se puede oberservar en el nuevo archivo hello2.i es:  
  * En el lugar del #include <stdio.h> el preprocesador copió y pegó los contratos o las declaraciones de todas las funciones y variables de stdio.h.  
  * Se pueden ver las declaraciones de las funciones de manejo de archivos como fwrite, fread, fseek, ftell, fclose, también la declaracion de puts, fputs, printf, fprintf, scanf, fscanf, getchar, getline que son todas funciones que venimos usando o usamos en algún momento y nunca vemos de donde salen, simplemente escribimos el #include.  
  * También se observa la declaración de tres variables de tipo FILE* que no son nada menos que el stdin, el stdout y e stderr.  
  * Por otro lado el preprocesador reemplazo el comentario "/\*medio\*/" por un espacio entre las palabras int y main.    

  Puedo concluir que el preprocesador no va a detectar errores de funciones mal escritas, falta de argumentos o llaves no cerradas, no es su función. El preprocesador se ocupa de traer de todos los includes los contratos o declaraciones de funciones y variables que estos tengan, de reemplazar comentarios por un espacio en blanco y también (aunque no lo hace en este caso) se ocupa de unir cadenas de caracteres que estan separadas por espacios sin nada entre sí. Por lo tanto es coherente que no haya errores en tiempo de preprocesamiento.  
       
  c) Escribir hello3.c, una nueva variante:
  ~~~
  int printf(const char * restrict s, ...);
  int main(void){
  int i=42;
  prontf("La respuesta es %d\n");
  ~~~
  d) Investigar e indicar la semántica de la primera línea  
    
  La primera línea declara el contrato de la función printf, indicando el tipo de dato que recibe como argumentos y el tipo de dato que devuelve.  
      
  e) Preprocesar hello3.c, no compilar, y generar hello3.i. Buscar diferencias entre hello3.c y hello3.i.  
      
  La única diferencia entre ambos archivos es el siguiente bloque de código que se pega arriba de la declaración de printf:
  ~~~
  # 1 "hello3.c"
  # 1 "<built-in>"
  # 1 "<command-line>"
  # 31 "<command-line>"
  # 1 "/usr/include/stdc-predef.h" 1 3 4
  # 32 "<command-line>" 2
  # 1 "hello3.c"
  ~~~
  En donde podemos ver que cada línea tiene el siguiente formato "# linenum filename flags" que se interpreta como que la siguiente línea se originó en el archivo **filename** en la línea **linenum**.  
  Luego vienen los **flags** que pueden ser '1' '2' '3' '4', alguna combinación de ellos o no tener ninguno:  
  * '1' indica el comienzo de un nuevo archivo
  * '2' indica el retorno a un archivo
  * '3' indica que el siguiente texto viene de un archivo header del sistema
  * '4' indica que el siguiente texto debería ser tratado como si estuviera envuelto   en un bloque "C" externo implícito  
    
  \<built-in> no es realmente un archivo, es un pseudo-archivo que contiene las definiciones de las macros del compilador.  

  \<command-line> tampoco es realmente un archivo, es la línea de comandos del preprocesador, que se considera como otra fuente de definiciones de macros.  
      
  Por último se ve en la quinta línea como esta vez si comienza un nuevo archivo real llamado "stdc-predef.h" cuyo path en mi sistema operativo es "/usr/include/stdc-predef.h" con los flags 1 ya que comienza un nuevo archivo y tambien con los flags 3 y 4.  
  El código de este archivo es el siguiente:
  ~~~
  #ifndef	_STDC_PREDEF_H
  #define	_STDC_PREDEF_H	1

  #ifdef __GCC_IEC_559
  # if __GCC_IEC_559 > 0
  #  define __STDC_IEC_559__		1
  # endif
  #else
  # define __STDC_IEC_559__		1
  #endif

  #ifdef __GCC_IEC_559_COMPLEX
  # if __GCC_IEC_559_COMPLEX > 0
  #  define __STDC_IEC_559_COMPLEX__	1
  # endif
  #else
  # define __STDC_IEC_559_COMPLEX__	1
  #endif

  #define __STDC_ISO_10646__		201706L

  #endif
  ~~~
    
  #### 2. Compilación
  a) Compilar el resultado y generar hello3.s, no ensamblar.  
    
  Para solo compilar el archivo fuente sin ejecutar el ensamblador tuve que agregar el flag -S al compilador gcc. Comando: "gcc hello3.c -S -std=c18 -o hello3.s" o "gcc hello3.i -S -std=c18 -o hello3.s". Partir del hello3.c o del hello3.i (preprocesado) genera el mismo codigo assembler.  
  Al compilar se generan los siguientes warnings y errores:  
  ~~~
  $ gcc hello3.c -s -std=c18 -o hello3.s
  hello3.c: In function ‘main’:
  hello3.c:4:2: warning: implicit declaration of function ‘prontf’; did you mean ‘printf’? [-Wimplicit-function-declaration]
    4 |  prontf("La respuesta es %d\n");
      |  ^~~~~~
      |  printf
  hello3.c:4:2: error: expected declaration or statement at end of input
  ~~~  

  b) Corregir solo los errores, no los warnings, en el nuevo archivo hello4.c y empezar de nuevo, generar hello4.s, no ensamblar.  

  Corrigiendo el error de la falta de la llave para cerrar la función main, el código de hello4.c queda:
  ~~~
  int printf(const char * restrict s, ...);
  int main(void){
  int i=42;
   prontf("La respuesta es %d\n");
  }
  ~~~  
  Y para generar el hello4.s utilizo el comando "gcc hello4.c -S -std=c18 -o hello4.s" o "gcc hello4.i -S -std=c18 -o hello4.s" que, como mencionamos anteriormente, generan el mismo código assembler.  
  El código de hello4.s es:
  ~~~
      .file	"hello4.c"
    .text
    .section	.rodata
  .LC0:
    .string	"La respuesta es %d\n"
    .text
    .globl	main
    .type	main, @function
  main:
  .LFB0:
    .cfi_startproc
    endbr64
    pushq	%rbp
    .cfi_def_cfa_offset 16
    .cfi_offset 6, -16
    movq	%rsp, %rbp
    .cfi_def_cfa_register 6
    subq	$16, %rsp
    movl	$42, -4(%rbp)
    leaq	.LC0(%rip), %rdi
    movl	$0, %eax
    call	prontf@PLT
    movl	$0, %eax
    leave
    .cfi_def_cfa 7, 8
    ret
    .cfi_endproc
  .LFE0:
    .size	main, .-main
    .ident	"GCC: (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0"
    .section	.note.GNU-stack,"",@progbits
    .section	.note.gnu.property,"a"
    .align 8
    .long	 1f - 0f
    .long	 4f - 1f
    .long	 5
  0:
    .string	 "GNU"
  1:
    .align 8
    .long	 0xc0000002
    .long	 3f - 2f
  2:
    .long	 0x3
  3:
    .align 8
  4:
  ~~~

  c) Leer hello4.s, investigar sobre lenguaje ensamblador,e indicar de forma sintética cual es el objetivo de ese código.  
    
  El objetivo de este código es en principio identificar el main, luego con algunos registros como el stack pointer y el base pointer entre otros, guardar el valor de la variable i. Con la funcion prontf, su objetivo es distinguir y separar lo que es el argumento de la función y lo que es el nombre de la función. Luego llama a dicha función con la instrucción "call" + su nombre y por otro lado, le pasa su argumento.  
    
  d) Ensamblar hello4.s en hello4.o, no vincular.  
    
  Para solo ensamblar el código usando gcc, uso el flag -c, que en realidad hace los dos pasos anteriores (preprocesar,compilar) y ademas ensambla el código assembler para dejar el código objeto sin linkear. El comando sería: "gcc hello4.s -c -std=c18 -o hello4.o".  
  Otra manera de ensamblar el código pasando por alto gcc es usando el GNU assembler, que el comando para ensamblarlo sería: "as hello4.s -o hello4.o"  
  El resultado generó el código objeto de hello4.o, que como es binario en código máquina, no podemos interpretarlo con vscode ni con un editor de texto. En un fragmento de dicho código interpretado por el editor de texto se puede distinguir pocas cosas como por ejemplo el argumento del prontf, pero no mucho mas. Se ve así:
  ~~~
  ELF\00\00\00\00\00\00\00\00\00\00>\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\000\00\00\00\00\00\00\00\00\00\00@\00\00\00\00\00@\00\00
  \00\F3\FAUH\89\E5H\83\EC\C7E\FC*\00\00\00H\8D=\00\00\00\00\B8\00\00\00\00\E8\00\00\00\00\B8\00\00\00\00\C9\C3La respuesta es %d
  \00\00GCC: (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00GNU\00\00\00\C0\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00\00zR\00x\90\00\00\00\00\00\00\00\00\00\00\00\00+\00\00\00\00E\86C
  b
  ~~~

  #### 3. Vinculación
  a) Vincular hello4.o con la biblioteca estándar y generar el ejecutable.  
    
  Para vincular hello4.o puedo usar dos recursos:  
  El primero es gcc, en este caso no le agrego ningun flag y como entrada le doy el código objeto. El comando sería "gcc hello4.o -std=c18 -o hello4.ex"  
  El segundo es usando directamente el GNU Linker pasando como parametro el path a ciertas bibliotecas. El comando sería "ld /usr/lib/x86_64-linux-gnu/crti.o /usr/lib/x86_64-linux-gnu/crtn.o /usr/lib/x86_64-linux-gnu/crt1.o -lc hello4.o -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o hello4.ex"  
    
  En ambos casos surge el mismo problema. El linker no encuentra referencia a la función "prontf".
  ~~~
  /usr/bin/ld: hello4.o: in function `main':
  hello4.c:(.text+0x20): undefined reference to `prontf'
  collect2: error: ld returned 1 exit status
  ~~~
    
  b) Corregir en hello5.c y generar el ejecutable. Solo corregir lo necesario para que vincule  
    
  El código de hello5.c quedaría:  
  ~~~
  int printf(const char * restrict s, ...);
  int main(void){
  int i=42;
  printf("La respuesta es %d\n");
  }
  ~~~  
  Una vez corregido para que vincule y utilizando cualquiera de los dos comandos anteriormente mencionados, genero el ejecutable "hello5.ex".

  c) Ejecutar y analizar el resultado.  
    
  Para ejecutar hello5.ex uso el comando básico para ejecutar en consola "./hello5.ex" y los resultados después de ejecutarlo algunas veces son los siguientes:
  ~~~
  La respuesta es -320521304

  La respuesta es 100127960

  La respuesta es 907297064
  ~~~
  En el código de hello5.c se puede ver que en la función printf hace falta un argumento de tipo int para reemplazar el %d, pero no se lo estamos pasando, y a su vez, no era un impedimento para que el código objeto se pueda linkear, por lo tanto no lo corregí en el punto anterior.  
  Entonces al printf necesitar un int para reemplazar el %d, previamente reservó un espacio en memoria del tamaño de un int, pero como no le asigné ningun valor, ese espacio de memoria se quedó con los 0 y 1 que habían quedado guardados antes de reservar dicho espacio.  
  Por lo tanto la ejecución mostrará la interpretación de un int con los 0 y 1 alojados en ese espacio de memoria y eso es lo que podemos ver al ejecutarlo.  
  
  #### 4. Correción de Bug
  a) Corregir en hello6.c y empezar de nuevo; verificar que funciona como se espera.  
    
  Al corregir hello6.c el código quedaría: 
  ~~~
  int printf(const char * restrict s, ...);
  int main(void){
  int i=42;
  printf("La respuesta es %d\n",i);
  }
  ~~~
  Luego repitiendo los pasos anteriores, generé el código preprocesado, el código assembler, el código objeto y finalmente el linkeo para generar hello6.ex. Al ejecutarlo funcionó como se esperaba imprimiendo lo siguiente:  
  ~~~
  La respuesta es 42
  ~~~

  #### 5. Remoción de prototipo
  a) Escribir hello7.c, una nueva variante:
  ~~~
  int main(void){
  int i=42;
  printf("La respuesta es %d\n", i);
  }
  ~~~
    
  b) Explicar por qué funciona.  
    
  Al realizar todos los pasos anteriores para crear el ejecutable lo único que surge es un warning al ejecutar el comando "gcc hello7.i -std=c18 -S -o hello7.s", es decir, en tiempo de compilación, cuando se estaba traduciendo el código preprocesado a código assembler. El warning nos indica la declaración implícita de printf ya que no incluimos el prototipo de la función, ya sea incluyendo stdio.h o solamente el prototipo de printf (que lo removí para este punto).  
  El warning:
  ~~~
  hello7.c: In function ‘main’:
  hello7.c:3:2: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
      3 |  printf("La respuesta es %d\n", i);
        |  ^~~~~~
  hello7.c:3:2: warning: incompatible implicit declaration of built-in function ‘printf’
  hello7.c:1:1: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
    +++ |+#include <stdio.h>
      1 | int main(void){
  ~~~
  Esto funciona ya que por defecto el linker va a buscar en la biblioteca estandar de C, aunque nosotros no incluyamos el "stdio.h" en el código. Entonces al momento de compilar nos advierte con un warning que no encuentra el prototipo pero luego al linkear se va a buscar la función printf en la biblioteca estandar, el linker la encuentra, copia el código objeto de la función dentro de la bibioteca y lo pega en el código objeto de hello7.o para que termine siendo el ejecutable hello7.ex.  
  En el caso de cuando el código llamaba a la función prontf, había un error en la fase de linkeo, ya que la buscaba en la biblioteca estandar de C y no encontraba dicha función.

  #### 6. Compilación Separada: Contratos y Módulos
  a) Escribir studio1.c (sí, studio1, no stdio) y hello8.c.  
  La unidad de traducción studio1.c tiene una implementación de la función prontf, que es solo un wrappwer1 de la función estándar printf:
  ~~~
  void prontf(const char* s, int i){
    printf("La respuesta es %d\n", i);
  }
  ~~~
  La unidad de traducción hello8.c, muy similar a hello4.c, invoca a prontf, pero no incluye ningún header.
  ~~~
  int main(void){
  int i=42;
  prontf("La respuesta es %d\n", i);
  }
  ~~~
  
  b) Investigar como en su entorno de desarrollo puede generar un programa ejecutable que se base en las dos unidades de traducción (i.e., archivos fuente, archivos con extensión .c).  
  Luego generar ese ejecutable y probarlo.  
    
  Mi entorno de desarrolo es VSCode, así que siempre compilo usando la terminal, y para compilar mas de un archivo .c simplemente hay que escribir ambos archivos en el comando para que el compilador los use como fuentes. El comando quedaría "gcc hello8.c studio1.c -std=c18 -o hello8.ex".  
  Como el main no tiene disponible el prototipo de la función "prontf", y "prontf" tampoco tiene el prototipo de "printf", se crea el ejecutable pero surgen los siguientes warnings:
  ~~~
  hello8.c: In function ‘main’:
  hello8.c:3:2: warning: implicit declaration of function ‘prontf’ [-Wimplicit-function-declaration]
      3 |  prontf("La respuesta es %d\n", i);
        |  ^~~~~~
  studio1.c: In function ‘prontf’:
  studio1.c:2:3: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
      2 |   printf("La respuesta es %d\n", i);
        |   ^~~~~~
  studio1.c:2:3: warning: incompatible implicit declaration of built-in function ‘printf’
  studio1.c:1:1: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
    +++ |+#include <stdio.h>
      1 | void prontf(const char* s, int i){
  ~~~
  El ejecutable funciona correctamente.  
    
  c) Responder ¿qué ocurre si eliminamos o agregamos argumentos a la invocación de prontf? Justifique.  
    
  Si eliminamos o agregamos argumentos a la invocación no cambia nada, al finalizar todos los pasos surgen los mismos warnings detallados en el punto b). Esto se debe a que al no tener disponibles los prototipos de las funciones, es decir, sus contratos, a la hora de hacer todos los pasos para crear el ejecutable no se tiene información de cúales serían los argumentos que debería recibir la función "prontf" y que tipo de dato debería retornar. Por lo tanto no hay errores y solo muestra los mismos warnings.  
  Particularmente si agregamos argumentos, simplemente en la ejecución se va a imprimir el printf con el texto "La respuesta es: " y luego el segundo parametro que le pasemos a la funcion prontf, si es un entero definido como "i", imprime 42, si es una cadena de texto imprime un numero raro que es lo que interpretó de dicha cadena.  
  En el caso de que le saquemos argumentos va a ocurrir algo que paso en un punto anterior, como no recibe un segundo argumento prontf, printf interpreta lo que hay en un espacio de memoria del tamaño de un int y lo pone en lugar del %d.  
    
  d) Revisitar el punto anterior, esta vez utilizando un contrato de interfaz en un archivo header  
    
  * Escribir el contrato en studio.h.
    ~~~
    #ifndef _STUDIO_H_INCULDED_
    #define _STUDIO_H_INCULDED_
    void prontf(const char*, int);
    #endif    
    ~~~
  * Escribir hello9.c, un cliente que sí incluye el contrato.
    ~~~
    #include "studio.h" // Interfaz que importa
    int main(void){
    int i=42;
    prontf("La respuesta es %d\n", i);
    }   
    ~~~
  * Escribir studio2.c, el proveedor que sí incluye el contrato.
    ~~~
    #include "studio.h" // Interfaz que exporta
    #include <stdio.h> // Interfaz que importa
    void prontf(const char* s, int i){
    printf("La respuesta es %d\n", i);
    }
    ~~~
  * Responder: ¿Qué ventaja da incluir el contrato en los clientes y en el proveedor.  
      
    La ventaja que da incluir el contrato en los clientes y en el proveedor es que ayuda al programador a asegurarse de que invocó las funciones de la manera que fueron diseñadas. Corrobora en tiempo de compilación que las funciones fueron invocadas de la manera correcta, es decir, que reciban como argumentos la cantidad que el prototipo indica, de esta manera no habrá invocaciones ambiguas, sino bien especificadas. 
    Sorpresivamente solo arroja un warning y no un error cuando la función espera un tipo de dato y en la invocación se le asigna otro.  

    Por ejemplo si quiero agregar argumentos de más que no estén contemplados en el prototipo, el compilador me arroja el siguiente error:  
    ~~~
    hello9.c: In function ‘main’:
    hello9.c:4:2: error: too many arguments to function ‘prontf’
        4 |  prontf("La respuesta es %d\n", i,i);
          |  ^~~~~~
    In file included from hello9.c:1:
    studio.h:3:6: note: declared here
        3 | void prontf(const char*, int);
          |      ^~~~~~
    ~~~  
    Pero si la función es invocada con la cantidad correcta de argumentos pero el tipo de dato no coincide con el prototipo (en este caso, en vez de invocar prontf con la variable "i", la invoqué con una cadena de caracteres) arroja el siguiente warning:
    ~~~
    hello9.c: In function ‘main’:
    hello9.c:4:33: warning: passing argument 2 of ‘prontf’ makes integer from pointer without a cast [-Wint-conversion]
        4 |  prontf("La respuesta es %d\n", "estoEsUnTexto");
          |                                 ^~~~~~~~~~~~~~~
          |                                 |
          |                                 char *
    In file included from hello9.c:1:
    studio.h:3:26: note: expected ‘int’ but argument is of type ‘char *’
        3 | void prontf(const char*, int);
          |                          ^~~
    ~~~  
    Al probar el ejecutable, funciona pero imprime un valor raro, que es lo que interpreta como un int de un espacio de memoria al cual no le fue asignado un valor entero: 
    ~~~
    La respuesta es -370335740
    ~~~  

### 3.2. Punto Extra  
  a) Investigue sobre bibliotecas. ¿Qué son? ¿Se puden distribuir? ¿Son portables? ¿Cuáles son sus ventajas y desventajas?  
    
  Las bibliotecas son colecciones de archivos de código objeto, resultantes de la compilación de código de implementaciones funcionales en un determinado lenguaje de programación. Las bilbiotecas ayudan con funcionalidades al programador, ya que puede reutilizar lo que se haya desarrollado en las mismas.  
  Las bibilotecas al ser código objeto, están compiladas para la arquitectura en donde fueron compiladas justamente. Una arquitectura diferente no entendería el código objeto, por lo tanto no son portables. La única manera sería que ambas computadoras tuvieran la misma arquitectura.  
  **Ventajas:**  
  * Ayudan a mantener prolijo y entendible el código al abstraer funcionalidades que utiliza el programador.
  * Ayuda a la reutilización de código, ahorrandole tiempo y trabajo al programador.
  * Suponen tener todas las funcionalidades bien testeadas.
  * El creador de la biblioteca solo comparte el código objeto, así que puede ocultar la implementación en el lenguaje de programación que utilice
    
  **Desventajas:**
  * El usuario de una biblioteca no puede ver las implementaciones de las funciones, solo sus contratos.
  * No son portables
    
  b) Desarrolle y utilice la biblioteca studio.  
    
  * Para generar la biblioteca studio, primero creé "studio.c" con las funciones aritméticas básicas, sumar, restar y multiplicar. Su código es:  
    ~~~
    #include <stdio.h>
    #include "studio.h"

    float suma(float n1,float n2)
    {
        return n1+n2;
    }

    float resta(float n1,float n2)
    {
        return n1-n2;
    }

    float producto(float n1,float n2)
    {
        return n1*n2;
    }
    ~~~  
  * Luego generé studio.o ,el código objeto de studio.c, con el comando "gcc studio.c -std=c18 -c -o studio.o".   

  * Como una biblioteca es una colección de archivos de código objeto, también generé studio2.o, el código objeto de studio2.c, (que usamos durante el trabajo) con el comando "gcc studio2.c -std=c18 -c -o studio2.o".  

  * Posteriormente junté ambos archivos con el comando "ar" quedando así: "ar cr studio.a studio.o studio2.o". El nombre de mi biblioteca quedó "studio.a".  

  * Escribí el archivo para probar la biblioteca "pruebaStudio.c" cuyo código es:  
    ~~~
    #include <stdio.h>
    #include "studio.h"

    int main()
    {
        printf("La suma de 4.3 y 3.56 es: %.2f\n\n",suma(4.3,3.56));
        prontf("No importa lo que escriba aca", 89);
        return 0;
    }
    ~~~
  * Generé el ejecutable de pruebaStudio.c con el comando "gcc pruebaStudio.c studio.a -o pruebaStudio.ex"
  * Y por último ejecuté pruebaStudio.ex y el resultado fue:
    ~~~
    La suma de 4.3 y 3.56 es: 7.86

    La respuesta es 89
    ~~~

## 4. Restricciones  
* El programa ejemplo debe enviar por stdout la frase La respuesta es 42, el
valor 42 debe surgir de una variable.  
  
## 5. Productos
~~~
DD-FasesErrores
|-- readme.md
|-- hello2.c
|-- hello3.c
|-- hello4.c
|-- hello5.c
|-- hello6.c
|-- hello7.c
|-- hello8.c
|-- studio1.c
|-- studio.h
`-- studio2.c
~~~
