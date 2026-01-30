# :floppy_disk:Bitácora de aplicación:floppy_disk:
## Actividad 4.
- Crea un programa que use un ciclo para sumar los números del 1 al 5 y guarde el resultado en la dirección de memoria 12.

```
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


# :memo:Bitácora de proceso de aprendizaje:memo:
## Actividad 1.

```
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


## Actividad 2.

```
@SCREEN
D=A
@i
M=D
(READKEYBOARD)
@KBD
D=M
@KEYPRESSED
D;JNE
@i
D=M
@SCREEN
D=D-A
@READKEYBOARD
D;JLE
@i
M=M-1
A=M
M=0
@READKEYBOARD
0;JMP

(KEYPRESSED)
@i
D=M
@KBD
D=D-A
@READKEYBOARD
D;JGE
@16
A=M
M=-1
@i
M=M+1
@READKEYBOARD
0;JMP
```

## Actividad 3.



# :bulb:Bitácora de reflexión:bulb:
## Actividad 5.



