# :floppy_disk:Bitácora de aplicación:floppy_disk:
# Actividad 6
## Evidencia 1 — Herencia en memoria

Demuestra con el depurador que comprendes cómo la herencia organiza los datos en memoria para uno de tus nuevos tipos de partícula. Debes poder mostrar la jerarquía completa de objetos anidados en el objeto inspeccionado y explicar qué campo pertenece a qué clase de la jerarquía.

<img width="1305" height="821" alt="image" src="https://github.com/user-attachments/assets/e1c3050c-9837-4d61-b6b6-2089516f578f" />

- ¿Dónde detuve la ejecución y por qué?

En la línea donde se hace push_back(new SpiralParticle(...)). La elegí porque es el momento exacto en que el objeto acaba de ser creado en memoria y todavía está vivo.

- ¿Qué se observa en la imagen?

El objeto SpiralParticle dentro del vector particles[0], con su jerarquía completa: contiene los campos de RisingParticle (position, velocity, age...), sus propios campos (spiralAngle, spiralRadius), y el _vfptr que apunta a la tabla de funciones virtuales.

- ¿Cómo demuestra comprensión?

La vtable muestra que aunque el vector solo guarda punteros Particle*, cada objeto carga su propia tabla que le dice qué versión de draw o update ejecutar. Eso es el polimorfismo en tiempo de ejecución: el programa decide en el momento de ejecutar, no cuando compila.


## Evidencia 2 — La _vtable de tu nuevo tipo

Demuestra con el depurador que comprendes cómo se implementa el polimorfismo a nivel de la _vtable. Compara la tabla de funciones de tu nuevo tipo con la de otro tipo existente (p. ej., CircularExplosion). Explica qué entradas son iguales y cuáles son diferentes, y por qué.

<img width="1358" height="815" alt="image" src="https://github.com/user-attachments/assets/b13e833d-414e-48c5-a539-13510d555c18" />


- ¿Dónde detuve la ejecución y por qué?

En el push_back(new RingExplosion(...)) dentro de update(). Lo elegí ahí porque RingExplosion solo se crea cuando una partícula explota, ese es su único momento de nacimiento y el único lugar donde puedo capturarlo recién creado.

- ¿Qué se observa en la imagen?
  
El objeto RingExplosion expandido mostrando los campos heredados de ExplosionParticle (position, velocity, age, size) y el _vfptr con la lista de funciones virtuales, donde se puede ver cuáles métodos implementa RingExplosion y cuáles vienen de sus clases padre.

- ¿Cómo demuestra comprensión?
  
La vtable muestra que RingExplosion solo sobreescribió draw — porque quería un patrón visual de anillo específico — pero dejó que update e isDead los manejara ExplosionParticle. Eso significa que el comportamiento de moverse, envejecer y morir es compartido con todas las explosiones, solo cambia cómo se dibuja.

## Evidencia 3 — Polimorfismo en tiempo de ejecución

Demuestra que el polimorfismo en tiempo de ejecución funciona para tu nuevo tipo: el despacho dinámico ejecuta tu versión del método virtual cuando corresponde. Debes mostrar que el programa tomó el camino correcto y no el de otro tipo.

## Evidencia 4 — Encapsulamiento en el contexto de herencia

Demuestra con el depurador que comprendes el encapsulamiento en el contexto de tu jerarquía de herencia: ¿Qué campos son visibles desde la subclase (protegidos/públicos) y cuáles no (privados)? ¿Cómo se refleja esto en la vista del depurador?

## Evidencia 5 — Ciclo de vida completo de tu partícula

Demuestra el ciclo de vida completo de uno de tus nuevos tipos: desde su creación (el objeto entra al vector), su estado durante update, hasta su eliminación (el objeto se retira del vector y se libera la memoria). Explica qué observas en cada etapa.

## Evidencia 6 — Sin fugas de memoria

Demuestra que no hay fuga de memoria: tu partícula se elimina correctamente del vector cuando muere y la memoria se libera. Explica qué pasa en el delete y cómo verificas que el puntero se retira del vector.

## Evidencia 7 — Prueba de condición límite

Diseña y ejecuta un escenario de prueba deliberado para verificar una condición límite de tu implementación. Por ejemplo: ¿Qué pasa cuando el vector de partículas se vacía completamente? ¿O cuando se crean muchas partículas al mismo tiempo? Tú decides qué condición quieres probar y por qué es relevante. Explica tu diseño del escenario de prueba, captura el depurador en el momento clave y justifica qué verificaste.
