# :floppy_disk:Bitácora de aplicación:floppy_disk:
# Actividad 6
## Evidencia 1 — Herencia en memoria

Demuestra con el depurador que comprendes cómo la herencia organiza los datos en memoria para uno de tus nuevos tipos de partícula. Debes poder mostrar la jerarquía completa de objetos anidados en el objeto inspeccionado y explicar qué campo pertenece a qué clase de la jerarquía.

<img width="1305" height="821" alt="image" src="https://github.com/user-attachments/assets/e1c3050c-9837-4d61-b6b6-2089516f578f" />

Detuve la ejecución en la línea donde se crea el objeto con particles.push_back(new SpiralParticle(...)), porque en ese momento el objeto acaba de ser instanciado y se puede observar completamente su estructura en memoria antes de que empiece a modificarse.

En el depurador se observa el objeto SpiralParticle dentro del vector particles, y al expandirlo se pueden ver tanto sus atributos propios (spiralAngle, spiralRadius) como los atributos heredados de RisingParticle (position, velocity, age, etc.), además del _vfptr.

Esto demuestra comprensión de la herencia porque evidencia que en C++ el objeto hijo incluye directamente en memoria los datos de su clase padre. No son objetos separados, sino una sola estructura que combina ambos niveles de la jerarquía, lo que permite reutilizar atributos y comportamientos.

## Evidencia 2 — La _vtable de tu nuevo tipo

Demuestra con el depurador que comprendes cómo se implementa el polimorfismo a nivel de la _vtable. Compara la tabla de funciones de tu nuevo tipo con la de otro tipo existente (p. ej., CircularExplosion). Explica qué entradas son iguales y cuáles son diferentes, y por qué.

<img width="1358" height="815" alt="image" src="https://github.com/user-attachments/assets/b13e833d-414e-48c5-a539-13510d555c18" />

Detuve la ejecución en el momento en que se crea un objeto RingExplosion dentro del método update(), ya que es el punto donde el objeto existe en memoria y se puede inspeccionar su tabla de funciones virtuales.

En el depurador se observa el objeto con sus atributos heredados de ExplosionParticle y el _vfptr, que apunta a la vtable. Al revisar esa tabla, se identifican las funciones virtuales disponibles, como draw(), update() e isDead().

Esto demuestra comprensión del polimorfismo porque permite ver que no todas las funciones pertenecen a la misma clase: draw() apunta a la implementación de RingExplosion, mientras que update() e isDead() corresponden a la clase base. Esto evidencia cómo el programa decide en tiempo de ejecución qué método ejecutar mediante la vtable.

## Evidencia 3 — Polimorfismo en tiempo de ejecución

Demuestra que el polimorfismo en tiempo de ejecución funciona para tu nuevo tipo: el despacho dinámico ejecuta tu versión del método virtual cuando corresponde. Debes mostrar que el programa tomó el camino correcto y no el de otro tipo.



Detuve la ejecución en el bucle donde se llama a particles[i]->update(dt), porque es el punto donde se aplica el mismo método a todos los objetos, sin importar su tipo real.

En el depurador se observa que el vector particles almacena punteros de tipo Particle*, pero cada elemento puede ser en realidad un RisingParticle, SpiralParticle o GravityParticle. Al avanzar paso a paso, se ve que el flujo del programa entra a diferentes implementaciones de update() dependiendo del objeto.

Esto demuestra el polimorfismo dinámico, ya que aunque todos los objetos se manejan como si fueran del mismo tipo base, cada uno ejecuta su propio comportamiento. La decisión de qué función se ejecuta ocurre en tiempo de ejecución, no en compilación.

## Evidencia 4 — Encapsulamiento en el contexto de herencia

Demuestra con el depurador que comprendes el encapsulamiento en el contexto de tu jerarquía de herencia: ¿Qué campos son visibles desde la subclase (protegidos/públicos) y cuáles no (privados)? ¿Cómo se refleja esto en la vista del depurador?

<img width="1351" height="808" alt="image" src="https://github.com/user-attachments/assets/000d3613-9b14-4e4b-a5b9-8d0eed9b389e" />
-
<img width="1355" height="812" alt="image" src="https://github.com/user-attachments/assets/fbe027be-3989-44ba-8429-3b3900befd53" />

Detuve la ejecución dentro del método update() de la clase RisingParticle, específicamente en la línea donde se actualiza la posición. Elegí este punto porque es donde el objeto modifica directamente sus propios atributos.

En las capturas se observan variables como position, velocity, age y lifetime. Al avanzar una instrucción, se puede ver cómo cambian los valores de position y age, lo que indica que el objeto está actualizando su estado interno.

Esto demuestra el encapsulamiento, ya que estos atributos no son modificados desde fuera del objeto, sino únicamente a través de sus propios métodos. El control de los datos está dentro de la clase, lo que asegura que el comportamiento del objeto sea consistente.



## Evidencia 5 — Ciclo de vida completo de tu partícula

Demuestra el ciclo de vida completo de uno de tus nuevos tipos: desde su creación (el objeto entra al vector), su estado durante update, hasta su eliminación (el objeto se retira del vector y se libera la memoria). Explica qué observas en cada etapa.

## Evidencia 6 — Sin fugas de memoria

Demuestra que no hay fuga de memoria: tu partícula se elimina correctamente del vector cuando muere y la memoria se libera. Explica qué pasa en el delete y cómo verificas que el puntero se retira del vector.

## Evidencia 7 — Prueba de condición límite

Diseña y ejecuta un escenario de prueba deliberado para verificar una condición límite de tu implementación. Por ejemplo: ¿Qué pasa cuando el vector de partículas se vacía completamente? ¿O cuando se crean muchas partículas al mismo tiempo? Tú decides qué condición quieres probar y por qué es relevante. Explica tu diseño del escenario de prueba, captura el depurador en el momento clave y justifica qué verificaste.
