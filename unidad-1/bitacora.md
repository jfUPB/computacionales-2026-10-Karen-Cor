# :floppy_disk:Bitácora de aplicación:floppy_disk:
## Actividad 4.
- Crea un programa que use un ciclo para sumar los números del 1 al 5 y guarde el resultado en la dirección de memoria 12.

``` asm 
@12
M=0
@13
M=1
(LOOP)
@13
D=M
@5
D=D-A
@END
D;JGT
@13
D=M
@12
D=D+M
M=D
@13
M=M+1
@LOOP
0;JMP
(END)

```
El programa debe sumar los valores del 1 al 5 utilizando un ciclo y almacenar el resultado en la dirección de memoria 12. El valor esperado es 15.

Ejecución:
Se inicializó la memoria 12 en 0 y la memoria 13 como contador en 1. El ciclo se ejecutó mientras el contador fue menor o igual a 5, sumando su valor a la memoria 12 en cada iteración.


El ciclo fue implementado mediante etiquetas y saltos condicionales, ya que el lenguaje Hack no cuenta con estructuras de control de alto nivel.


# :memo:Bitácora de proceso de aprendizaje:memo:
## Actividad 1.

``` asm
@1
D=A
@2
D=D+A
@16
M=D
@END
(END)
0;JMP
```
Al ejecutar este programa se almacena en la direccion de memoria 16 el numero 3, y los registros de memoria quedan con los siguientes valores: 7 en PC, 7 en A y 3 en D

Asi funciona:

> Inician los registros de memoria en 0

Se le asigna la direccion de memoria 1 para A

Se almacena en D la direccion de memoria de A

Se direcciona la memoria a 2

> En este punto PC tiene el valor de 3, A tiene el valor de 2 y D tiene el valor de 1

Se almacena en D el resultado de D+A, que seria 1+2. Se almacen en 3 en D

Se direcciona a la posicion 16

Se le asigna a la posicion 16 el valor de D

¿Qué diferencia hay entre los datos almacenados en la memoria ROM y en la RAM?

R\\:



# :bulb:Bitácora de reflexión:bulb:
## Actividad 5.

Describe con tus palabras las tres fases del ciclo Fetch-Decode-Execute. ¿Qué rol juega el Program Counter (PC) en este ciclo?

  - R:// Fetch, aqui la CPU busca la instruccion que debe realizar. Decode, aqui la CPU analiza las intrucciones que acaba de recibir. Execute, ahora la CPU realiza la accion indicada. PC indica la direccion de la instruccion y la CPU toma esa direccion para leer la intruccion.

¿Cuál es la diferencia fundamental entre una instrucción-A (que empieza con @) y una instrucción-C (que involucra D, M, A, etc.) en el lenguaje ensamblador de Hack? Da un ejemplo de cada una.

   - R:// Una instruccion A carga un calor en ese registro, direccion o valor. Una instruccion C realiza una operacion y puede guardar ese resultado o decidir un salto.
     Ejmplo:
     D;JGT Si D>0, salta a la direccion almacenada. En A, @16 Se esta apuntando a la direccion de memoria 16 y se almacen este numero en el registro A

Explica la función de los siguientes componentes del computador Hack: el registro D, el registro A y la ALU.

   - R:// Como se menciona en lo anterior el registro A apunta a la direccion de memoria indicada, mientras que el registro D es usado como base de operacion, guardando datos temporales. ALU reacciona operacion aritmeticas y logicas.

¿Cómo se implementa un salto condicional en Hack? Describe un ejemplo (p. ej., saltar si el valor de D es mayor que cero).

  - R:// Usando una instruccion-c. D;JGT, si D>0, entonces PC=A
  ```
@POSITIVE
D;JGT

// código que se ejecuta si D ≤ 0

(POSITIVE)
// código que se ejecuta si D > 0

```

¿Cómo se implementa un loop en el computador Hack? Describe un ejemplo (p. ej., un loop que decremente un valor hasta que llegue a cero).

   - R:// Los loops se realizan con una etiqueta y salto condicional, si la condicion cumple PC toma el valor de la direccion de loop, si no, sigue normalmente y el loop termina

```
@10
D=M          // D = valor inicial

(LOOP)
D=D-1        // D = D - 1
@10
M=D          // guardar el nuevo valor en RAM[10]

@LOOP
D;JGT        // si D > 0, volver al inicio del loop

```

¿Cuál es la diferencia entre la instrucción D=M y la instrucción M=D?

   - R:// Mientraas que D=M lee el valor en la memoria y lo almacena en D, M=D escribe en la memoria lo que hay en D

Describe brevemente qué se necesita para leer un valor del teclado (KBD) y para “pintar” un pixel en la pantalla (SCREEN).

   - R:// Leer el teclado, basta con leer el contenido de la direccion KBD. Para pintar se debe escribir un valor dentro de la memoria mapeada SCREEN (16384)





