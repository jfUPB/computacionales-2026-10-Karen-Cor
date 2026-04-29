# :floppy_disk:Bitácora de aplicación:floppy_disk:
# Actividad 6

Codigo fuente modificado

<details>
  <summary>ofApp.cpp</summary>

```cpp
#include "ofApp.h"

// --------------------------------------------------------------
void ofApp::setup() {
	ofSetFrameRate(60);
	ofBackground(0);
}

// --------------------------------------------------------------
void ofApp::update() {
	float dt = ofGetLastFrameTime();

	for (int i = 0; i < particles.size(); i++) {
		particles[i]->update(dt);
	}

	for (int i = particles.size() - 1; i >= 0; i--) {
		if (particles[i]->shouldExplode()) {
			int explosionType = (int)ofRandom(4);
			int numParticles = (int)ofRandom(20, 30);
			for (int j = 0; j < numParticles; j++) {
				if (explosionType == 0) {
 					particles.push_back(new CircularExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 1) {
					particles.push_back(new RandomExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 2) {
					particles.push_back(new StarExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else {
					particles.push_back(new RingExplosion(
						particles[i]->getPosition(),
						particles[i]->getColor(),
						j, numParticles));
				}
			}
			delete particles[i];
			particles.erase(particles.begin() + i);
		} else if (particles[i]->isDead()) {
			delete particles[i];
			particles.erase(particles.begin() + i);
		}
	}
}

// --------------------------------------------------------------
void ofApp::draw() {
	for (int i = 0; i < particles.size(); i++) {
		particles[i]->draw();
	}
}

// --------------------------------------------------------------
void ofApp::createRisingParticle() {
	float minX = ofGetWidth() * 0.35f;
	float maxX = ofGetWidth() * 0.65f;
	float spawnX = ofRandom(minX, maxX);
	glm::vec2 pos(spawnX, ofGetHeight());
	glm::vec2 target(ofGetWidth() / 2.0f + ofRandom(-300, 300),
		ofGetHeight() * 0.10f + ofRandom(-30, 30));
	glm::vec2 direction = glm::normalize(target - pos);
	glm::vec2 vel = direction * ofRandom(250, 350);
	ofColor col;
	col.setHsb(ofRandom(255), 220, 255);
	float lifetime = ofRandom(1.5f, 3.5f);

	int type = (int)ofRandom(3);
	if (type == 0) {
		particles.push_back(new RisingParticle(pos, vel, col, lifetime));
	} else if (type == 1) {
		particles.push_back(new SpiralParticle(pos, vel, col, lifetime));
	} else {
		particles.push_back(new GravityParticle(pos, vel, col, lifetime));
	}
}

// --------------------------------------------------------------
void ofApp::mousePressed(int x, int y, int button) {
	createRisingParticle();
}

// --------------------------------------------------------------
void ofApp::keyPressed(int key) {
	if (key == ' ') {
		for (int i = 0; i < 1000; i++) {
			createRisingParticle();
		}
	}
	if (key == 's') {
		ofSaveScreen("screenshot_" + ofToString(ofGetFrameNum()) + ".png");
	}
}

// --------------------------------------------------------------
ofApp::~ofApp() {
	for (int i = 0; i < particles.size(); i++) {
		delete particles[i];
	}
	particles.clear();
}

```

</details>

<details>
  <summary>ofApp.h</summary>

```h
#pragma once
#include "ofMain.h"
#include <vector>

class Particle {
public:
	virtual ~Particle() { }
	virtual void update(float dt) = 0;
	virtual void draw() = 0;
	virtual bool isDead() const = 0;
	virtual bool shouldExplode() const { return false; }
	virtual glm::vec2 getPosition() const { return glm::vec2(0, 0); }
	virtual ofColor getColor() const { return ofColor(255); }
};

class RisingParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float lifetime;
	float age;
	bool exploded;

public:
	RisingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life)
		: position(pos)
		, velocity(vel)
		, color(col)
		, lifetime(life)
		, age(0)
		, exploded(false) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		velocity.y += 9.8f * dt * 8;
		float explosionThreshold = ofGetHeight() * 0.15f + ofRandom(-30, 30);
		if (position.y <= explosionThreshold || age >= lifetime) {
			exploded = true;
		}
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, 10);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return position; }
	ofColor getColor() const override { return color; }
};

class ExplosionParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float age;
	float lifetime;
	float size;

public:
	ExplosionParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life, float sz)
		: position(pos)
		, velocity(vel)
		, color(col)
		, age(0)
		, lifetime(life)
		, size(sz) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		float alpha = ofMap(age, 0, lifetime, 255, 0, true);
		color.a = alpha;
	}

	bool isDead() const override { return age >= lifetime; }
};


class CircularExplosion : public ExplosionParticle {
public:
	CircularExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.2f, ofRandom(16, 32)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(80, 200);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, size);
	}
};


class RandomExplosion : public ExplosionParticle {
public:
	RandomExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.5f, ofRandom(16, 32)) {
		velocity = glm::vec2(ofRandom(-200, 200), ofRandom(-200, 200));
	}

	void draw() override {
		ofSetColor(color);
		ofDrawRectangle(position.x, position.y, size, size);
	}
};


class StarExplosion : public ExplosionParticle {
public:
	StarExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.3f, ofRandom(20, 40)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(90, 180);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		int rays = 5;
		float outerRadius = size;
		float innerRadius = size * 0.5f;
		ofPushMatrix();
		ofTranslate(position);
		for (int i = 0; i < rays; i++) {
			float theta = ofMap(i, 0, rays, 0, TWO_PI);
			float xOuter = cos(theta) * outerRadius;
			float yOuter = sin(theta) * outerRadius;
			float xInner = cos(theta + PI / rays) * innerRadius;
			float yInner = sin(theta + PI / rays) * innerRadius;
			ofDrawLine(0, 0, xOuter, yOuter);
			ofDrawLine(xOuter, yOuter, xInner, yInner);
		}
		ofPopMatrix();
	}
};


class ofApp : public ofBaseApp {
public:
	void setup();
	void update();
	void draw();
	void mousePressed(int x, int y, int button);
	void keyPressed(int key);

	std::vector<Particle *> particles;
	~ofApp();

private:
	void createRisingParticle();


	class SpiralParticle : public RisingParticle
	{
	private:
		float spiralAngle;
		float spiralRadius;

	public:
		SpiralParticle(const glm::vec2 & pos, const glm::vec2 & vel,
			const ofColor & col, float life)
			: RisingParticle(pos, vel, col, life)
			, spiralAngle(0)
			, spiralRadius(30.0f) { }

		void update(float dt) override {
			RisingParticle::update(dt);
			spiralAngle += dt * 5.0f; 
			position.x += cos(spiralAngle) * spiralRadius * dt; 
		}

		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, 8); 
		}
	};


	class GravityParticle : public RisingParticle {
	public:
		GravityParticle(const glm::vec2 & pos, const glm::vec2 & vel,
			const ofColor & col, float life)
			: RisingParticle(pos, vel, col, life) { }

		void update(float dt) override
		{
			position += velocity * dt;
			age += dt;
			velocity.y += 9.8f * dt * 20;
			if (age >= lifetime)
			{
				exploded = true;
			}
		}

		void draw() override {
			ofSetColor(color);
			ofDrawRectangle(position.x - 5, position.y - 5, 10, 10);
		}
	};


	class RingExplosion : public ExplosionParticle {
	public:
		RingExplosion(const glm::vec2 & pos, const ofColor & col, int index, int total)
			: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.8f, ofRandom(8, 14))
		{
			float angle = (TWO_PI / total) * index;
			float speed = ofRandom(100, 150);
			velocity = glm::vec2(cos(angle), sin(angle)) * speed;
		}

		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, size * 0.7f);
		}
	};
};

```

</details>

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

<img width="1355" height="822" alt="image" src="https://github.com/user-attachments/assets/20512519-2c47-4fe1-b529-c644391419c1" />

¿Dónde detuve la ejecución y por qué?

En el push_back de la explosión dentro de update(), justo cuando el objeto acaba de ser creado. Ese punto me permite ver el objeto completo en memoria antes de que el programa continúe modificándolo.

¿Qué se observa en la imagen?

El objeto RingExplosion expandido mostrando tres niveles de jerarquía apilados: RingExplosion contiene a ExplosionParticle, que contiene a Particle, que contiene el _vfptr. Debajo de eso están los campos concretos: position, velocity, color, age, lifetime — todos viviendo físicamente dentro del mismo objeto.

¿Cómo demuestra comprensión?

La herencia no es abstracta — se ve literalmente en memoria. El objeto RingExplosion no es solo RingExplosion, internamente carga todos los datos de sus clases padre apilados. Eso explica por qué puede usar position o age sin haberlos declarado él mismo: los heredó y están físicamente dentro de él.

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

<img width="1000"  src="https://github.com/user-attachments/assets/5087c17e-f7e1-4131-9abf-ad720994c350" />

Detuve la ejecución en la línea 72 de ofApp.cpp, dentro de createRisingParticle(), justo después del particles.push_back(new SpiralParticle(...)). Elegí este punto porque el objeto acaba de ser instanciado en el heap y empujado al vector, por lo que se puede observar su estado inicial completo antes de que el programa lo modifique.

En el depurador se observa el objeto [24] de tipo SpiralParticle recién agregado al vector. Al expandirlo se ve la jerarquía completa: SpiralParticle → RisingParticle → Particle. Los valores confirman que es un objeto recién nacido: age = 0, exploded = false, spiralAngle = 0, spiralRadius = 30. El campo lifetime = 1.55 indica cuántos segundos tiene permitido vivir.

<img width="1000" src="https://github.com/user-attachments/assets/97957a77-7817-41f5-bff0-7bd898e45db4" />

Detuve la ejecución en la línea 177 de ofApp.h, dentro del update() de SpiralParticle, justo antes de aplicar el movimiento espiral. Elegí este punto porque es donde el objeto está activamente modificando su estado en cada frame.

El depurador muestra el objeto this de tipo SpiralParticle, y al expandirlo se ve la data heredada de RisingParticle anidada dentro. Los valores confirman que el objeto está vivo y evolucionando: age = 0.017 (un frame ha pasado), spiralAngle = 0.086 (ya no es 0, está girando), y position.x = 504 (se ha movido desde su posición inicial). exploded = false confirma que sigue activo.

## Evidencia 6 — Sin fugas de memoria

Demuestra que no hay fuga de memoria: tu partícula se elimina correctamente del vector cuando muere y la memoria se libera. Explica qué pasa en el delete y cómo verificas que el puntero se retira del vector.

## Evidencia 7 — Prueba de condición límite

Diseña y ejecuta un escenario de prueba deliberado para verificar una condición límite de tu implementación. Por ejemplo: ¿Qué pasa cuando el vector de partículas se vacía completamente? ¿O cuando se crean muchas partículas al mismo tiempo? Tú decides qué condición quieres probar y por qué es relevante. Explica tu diseño del escenario de prueba, captura el depurador en el momento clave y justifica qué verificaste.
