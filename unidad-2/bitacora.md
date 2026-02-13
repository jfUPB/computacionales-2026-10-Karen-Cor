# :floppy_disk:Bitácora de aplicación:floppy_disk:

``` hack
// a = 10
@10
D=A
@16
M=D

// b = 20
@20
D=A
@17
M=D

// tmp = a
@16
D=M
@18
M=D

// a = b
@17
D=M
@16
M=D

// b = tmp
@18
D=M
@17
M=D

// loop infinito (fin del programa)
(END)
@END
0;JMP

```
<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/50daa8c1-7354-41f0-8aff-00a986d55f4c" />
En este punto vemos que se va a apuntar a la direccion de memoria 17, numero que se guardara en el registro A, y se escribe en la direccion de memoria lo que hay en D, siendo entonces que en el registro numero 17 quedara el numero 20.


<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/86d9e755-1561-4058-82b3-2d69c74167dc" />
Siguiendo lo anterior, ahora se apuntara a la direccion de memoria 16, se lee el valor que hay en esa direccion y se almacena en D, siendo este valor 10.

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/0933916e-a768-47e6-ae57-4b1295d77840" />
Aqui se observa la direccion de memoria que antes era 10, ya cambiada a 20, faltando que el 20 de la posicion 17 cambie a 10, como lo indica el problema. Por lo que se apunta a la direccion 17 de memoria, y se indica almacenar lo que hay en D (M=D), quedando ya en 10


<img width="1543" height="777" alt="image" src="https://github.com/user-attachments/assets/a69e9207-4c1e-4271-bbff-c7d883baf7ea" />




   -

<img width="512" height="256" alt="image" src="https://github.com/user-attachments/assets/f61ce4d2-c0a7-44dd-a9e8-5bf89fe4aae1" />




``` hack
// arr = {10, 15, 2, 3, 50}
@10
D=A
@16
M=D

@15
D=A
@17
M=D

@2
D=A
@18
M=D

@3
D=A
@19
M=D

@50
D=A
@20
M=D

// sum = 0
@21
M=0

// i = 0
@22
M=0

// arrSize = 5
@5
D=A
@23
M=D

// LOOP
(LOOP)
@22
D=M
@23
D=D-M
@END
D;JGE

// sum = sum + arr[i]
@22
D=M
@16
A=D+A
D=M
@21
M=D+M

// i++
@22
M=M+1

@LOOP
0;JMP

(END)
@END
0;JMP

```
<img width="512" height="256" alt="image" src="https://github.com/user-attachments/assets/2c02dea9-1d8f-4667-a2d0-0faa8c8b1e5e" />



# :memo:Bitácora de proceso de aprendizaje:memo:
## Actividad 1.
un punto negro en la esquina superior izquierda de la pantalla.
``` hack
@SCREEN      // Cargar la dirección base de la pantalla (16384)
D=A          // Guardar esa dirección en D
@punto       // Variable para guardar la dirección
M=D          // Ahora "punto" apunta a SCREEN
@32768       // Valor binario: 1000000000000000 (solo el primer bit en 1)
D=A          // D = 32768
@punto       // Dirección de memoria donde escribir
A=M          // A = dirección SCREEN
M=D          // Escribir 32768 en esa dirección

(END)
@END
0;JMP
```

<img width="512" height="256" alt="image" src="https://github.com/user-attachments/assets/45b4d58c-f7c6-48b9-b4fc-8c065bfd2f92" />


## Actividad 2.
una línea horizontal negra de 16 pixeles de largo en la esquina superior izquierda de la pantalla.

``` hack
@SCREEN    // Cargar dirección base de pantalla (16384)
M=-1       // Escribir -1 = 1111111111111111 (todos los bits 1)

(END)
@END
0;JMP    
```


<img width="512" height="256" alt="image" src="https://github.com/user-attachments/assets/bae68aff-2bb2-4fc4-8811-5231f46ffe86" />


## Actividad 3.
mover la línea horizontal de derecha a izquierda usando las teclas d y i respectivamente.

``` hack
@pos
M=0          // posición inicial = 0

(LOOP)
// Leer teclado
@KBD
D=M

// Si es 'd' (100)
@100
D=D-A
@DERECHA
D;JEQ

// Si es 'i' (105)
@KBD
D=M
@105
D=D-A
@IZQUIERDA
D;JEQ

@DIBUJAR
0;JMP

(DERECHA)
// Guardar posición actual antes de cambiar
@pos
D=M
@pos_anterior
M=D
// Mover a la derecha
@pos
M=M+1
@DIBUJAR
0;JMP

(IZQUIERDA)
// Guardar posición actual antes de cambiar
@pos
D=M
@pos_anterior
M=D
// Mover a la izquierda
@pos
M=M-1

(DIBUJAR)
// BORRAR LA POSICIÓN ANTERIOR
@SCREEN
D=A
@pos_anterior
D=D+M        // D = SCREEN + pos_anterior
A=D
M=0          // borrar esa línea

// DIBUJAR EN NUEVA POSICIÓN
@SCREEN
D=A
@pos
D=D+M        // D = SCREEN + pos
A=D
M=-1         // dibujar línea negra

@LOOP
0;JMP
```
![Grabación 2026-02-13 120005](https://github.com/user-attachments/assets/74e1160e-dc94-4732-acce-5b80a6b1344d)


# :bulb:Bitácora de reflexión:bulb:

