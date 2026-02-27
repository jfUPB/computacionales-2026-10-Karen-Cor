# :floppy_disk:Bitácora de aplicación:floppy_disk:
Luego de compilar y ejecutar el codigo, se paso a depurar con el fin de visualizar los errores en el programa, usando un breakpoint en la linea 29, y dando dos saltos mas, podemos visualizar un suceso que termina siendo problematico en el codigo, y es que al copiar el objeto heroe, como copiaHeroe lo que se hace es una copia superficial, ps ambas variables terminan compartiendo la misma direccion de memoria:

Es decir, ambos objetos apuntan al mismo bloque en el heap, ps no se creo un nuevo arreglo. A nivel de memoria, el objeto heroe se encuentra en el stack y su miembro estadisticas almacena una dirección hacia un bloque reservado en el heap mediante new. Al copiar el objeto, el compilador copia esa dirección tal cual, por lo que copiaHeroe también apunta al mismo bloque del heap.

La consecuencia de esto es que ambos objetos comparten el mismo recurso dinámico. Si uno modifica las estadísticas, el otro también se ve afectado. Además, si se implementara un destructor que libere la memoria con delete[], se produciría una doble liberación del mismo bloque, lo que generaría corrupción del heap y posible fallo del programa.

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/fa81fe0f-d125-4e71-838f-ffeb08ebf6fb" />

Ahora, partiendo de eso mismo, se observa que se usa la memoria heap con el arreglo, pero al ser esta una memoria dinamica es necesario el uso de un destructor que libere dicho arreglo, pero al intentar implementarlo dentro de la clase de personaje observamos un double delete, el error se da debido a que se esta intentando liberar un bloque que ya no es valido, un delete en algo que fue eliminado.

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/d15c8937-5f05-4d02-b463-2d61a6855a74" />
<img width="512" height="256" alt="image" src="https://github.com/user-attachments/assets/c3246c75-3745-4d49-9448-9683ae7d5c3e" />
<img width="641" height="436" alt="image" src="https://github.com/user-attachments/assets/4eefc2e8-d100-4384-9465-47d44b3ae687" />

Este comportamiento confirma que la clase no gestiona correctamente la memoria dinámica y presenta un diseño inseguro.

Ahora:
Para evidenciar la fuga de memoria se eliminó el destructor de la clase Personaje, dejando activa la reserva dinámica mediante new int[3] sin su correspondiente liberación. Se activó el detector de fugas del runtime de C++ en modo Debug mediante _CrtSetDbgFlag. Al finalizar la ejecución, el sistema reportó “Detected memory leaks!”, confirmando que el bloque reservado en el heap no fue liberado. Esto demuestra que la clase realiza asignación dinámica sin implementar un mecanismo adecuado de liberación de memoria.

<img width="1341" height="433" alt="image" src="https://github.com/user-attachments/assets/40bd9718-084b-49a7-b1e9-6d1f749c54bf" />


<img width="1595" height="577" alt="image" src="https://github.com/user-attachments/assets/3884c8fe-1fa1-48de-9017-c6e2d04f189f" />




REESTRUCTURACION
``` 
#include <iostream>
#include <string>
#include <array>

class Personaje {
public:
    std::string nombre;
    std::array<int, 3> estadisticas;  // Vida, Ataque, Defensa

    Personaje(const std::string& n, int vida, int ataque, int defensa)
        : nombre(n), estadisticas{vida, ataque, defensa}
    {
        std::cout << "Constructor: nace " << nombre << std::endl;
    }

    void imprimir() const {
        std::cout << "Personaje " << nombre
                  << " [Vida: " << estadisticas[0]
                  << ", ATK: " << estadisticas[1]
                  << ", DEF: " << estadisticas[2]
                  << "]" << std::endl;
    }
};

void simularEncuentro() {
    std::cout << "\n--- Iniciando encuentro ---" << std::endl;

    Personaje heroe("Aragorn", 100, 20, 15);

    Personaje copiaHeroe = heroe;  // ahora es copia segura
    copiaHeroe.nombre = "Copia de Aragorn";

    heroe.imprimir();
    copiaHeroe.imprimir();

    std::cout << "Saliendo del encuentro..." << std::endl;
}

int main() {
    simularEncuentro();

    std::cout << "\nSimulación terminada." << std::endl;
    return 0;
}
```


# :bulb:Bitácora de reflexión:bulb:

## Parte 1:

- Explica con tus propias palabras qué es el stack y qué es el heap en C++.

R//: El stack es una memoria automatica por asi decirlo, que en cuanto se sale de la estancia del objeto se libera por si misma, mientras que el heap es una memoria dinamica, que debe usar un destructor que libere la memoria.

- Describe las tres formas de pasar parámetros a una función en C++ (valor, referencia y puntero). Para cada una, explica qué sucede en memoria y cuándo usarías cada método.

R//: Valor: Se crea una copia del objeto y dicha copia vive en el stack, y es la que se modifica, dejando la original, una vez se sale de la estancia el objeto se destruye y la memoria se libera. Referencia: Aqui no se crea una copia como tal, sino que se usa mas bien un alias referenciando el mismo objeto y vive en el stack, apuntan a la misma direccion. Puntero: Se pasa la direccion de memoria, la funcion recibe un puntero, que apunta al dato original.

- ¿Qué diferencia hay entre una variable local, una variable global y una variable local estática? ¿En qué segmento del mapa de memoria se almacena cada una?

R//: La variable local solo existe dentro de la funcion, y cuando la funcion termina se destruye. La variable global existe durante todo el programa, por lo que es visible desde cualquier parte. Y una variable local estatica es visible solo en la funcion, pero, a diferencia de la local, no se destruye al terminar dicha funcion. 

- Explica qué es un objeto en C++ desde la perspectiva de memoria. ¿Dónde se almacenan los miembros de instancia y dónde los miembros estáticos?

R//: Podria decirse que es un tipo de memoria que sigue a la clase indicada. Los miembros de la estancia se almacenan en la memoria stack

## Parte 2:

- Análisis de problemas: identifica al menos dos problemas serios en este código relacionados con el manejo de memoria. Explica por qué cada uno es problemático.

- Predicción de comportamiento: ¿Qué valor mostrará totalEnemigos después de ejecutar el programa? ¿Por qué ocurre esto?

- Propuesta de solución: escribe una versión corregida de la clase Enemigo que solucione los problemas identificados. Explica brevemente cada cambio que hiciste.


## Parte 3:

- De todos los conceptos que exploraste en esta unidad (stack vs heap, paso de parámetros, ciclo de vida de objetos, etc.), ¿Cuál consideras que es el más crítico para evitar errores en programas reales? ¿Por qué?

R//: La verdad es que los conceptos que tuve mas presentes mientras trabajaba esta unidad fue el stack vs heap, ya que una vez que las entendemos vemos que no es algo que este a la vista directamente en el codigo, pero que siempre estamos implementando y tomando en cuenta mientras avanzamos en nuestro trabajo, es importante conocer la diferencia y con eso las especificaciones que se usan en cada una.

- ¿Cómo cambió tu comprensión sobre lo que realmente es un “objeto” después de comparar C++ con C#? ¿Qué implicaciones prácticas tiene esta diferencia?

R//: Mientras que en c# el objeto vive en heap(sin preocuparnos por liberarlo) y es referenciado por una referencia, en c++ el objeto puede vivir en stack, heap, dentro de otro objeto o en una memoria dinamica, siendo cada uno distinto, cambiara el como trabajemos nuestro codigo, segun las necesidades que presentemos.

- Si tuvieras que explicar a un compañero de semestres anteriores por qué es importante entender la gestión de memoria en programación, ¿Qué le dirías en máximo 3 oraciones?

R//: Permite alcanzar un entendimiento completo de nuestro codigo, como funcionan/dirigen los datos/variables que estamos cargando.
