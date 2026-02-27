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


## Bitácora de reflexión

