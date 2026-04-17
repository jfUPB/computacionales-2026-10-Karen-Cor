# :floppy_disk:Bitácora de aplicación:floppy_disk:
## Actividad 4.

## Fase 1
Codigo fuente modificado

<details>
  <summary>ofApp.cpp</summary>

```cpp
#include "ofApp.h"
#include <algorithm>

void Subject::addObserver(Observer * observer) {
	if (!observer) return;
	if (std::find(observers.begin(), observers.end(), observer) == observers.end()) {
		observers.push_back(observer);
	}
}

void Subject::removeObserver(Observer * observer) {
	if (!observer) return;
	observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
}

void Subject::notify(const std::string & event) {
	for (Observer * observer : observers) {
		observer->onNotify(event);
	}
}

Particle::Particle()
	: state(nullptr) {
	position = ofVec2f(ofRandomWidth(), ofRandomHeight());
	velocity = ofVec2f(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
	size = ofRandom(2.0f, 5.0f);
	color = ofColor(255);

	state = new NormalState();
	state->onEnter(this);
}

Particle::~Particle() {
	if (state) {
		state->onExit(this);
		delete state;
		state = nullptr;
	}
}

void Particle::setState(State * newState) {
	if (state) {
		state->onExit(this);
		delete state;
	}
	state = newState;
	if (state) {
		state->onEnter(this);
	}
}

void Particle::update() {
	if (state) {
		state->update(this);
	}
	keepInsideWindow();
}

void Particle::draw() {
	ofPushStyle();
	ofSetColor(color);
	ofDrawCircle(position, size);
	ofPopStyle();
}

void Particle::onNotify(const std::string & event) {

	if (event == "attract") {
		setState(new AttractState());

	} else if (event == "repel") {
		setState(new RepelState());

	} else if (event == "stop") {
		setState(new StopState());

	} else if (event == "normal") {
		setState(new NormalState());

	} else if (event == "chaos") {
		setState(new ChaosState());
	}
}

void Particle::keepInsideWindow() {
	const float W = static_cast<float>(ofGetWidth());
	const float H = static_cast<float>(ofGetHeight());

	if (position.x < 0.0f) {
		position.x = 0.0f;
		velocity.x *= -1.0f;
	} else if (position.x > W) {
		position.x = W;
		velocity.x *= -1.0f;
	}
	if (position.y < 0.0f) {
		position.y = 0.0f;
		velocity.y *= -1.0f;
	} else if (position.y > H) {
		position.y = H;
		velocity.y *= -1.0f;
	}
}

void NormalState::onEnter(Particle * particle) {
	particle->velocity.set(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
}

void NormalState::update(Particle * particle) {
	particle->position += particle->velocity;
}

static void steer(Particle * particle, const ofVec2f & toward, float accel, float vmax, float posScale) {
	ofVec2f dir = toward - particle->position;
	float len = dir.length();
	if (len > 1e-6f) {
		dir /= len;
		particle->velocity += dir * accel;
	}
	particle->velocity.limit(vmax);
	particle->position += particle->velocity * posScale;
}

void AttractState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	steer(particle, mouse, 0.05f, 3.0f, 0.2f);
}

void RepelState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	ofVec2f away = particle->position - mouse;
	float len = away.length();
	if (len > 1e-6f) {
		away /= len;
		particle->velocity += away * 0.05f;
	}
	particle->velocity.limit(3.0f);
	particle->position += particle->velocity * 0.2f;
}

void StopState::update(Particle * particle) {
	particle->velocity *= 0.80f;
	if (particle->velocity.lengthSquared() < 1e-4f) {
		particle->velocity.set(0.0f, 0.0f);
	}
	particle->position += particle->velocity;
}

void ChaosState::update(Particle * particle) {

	particle->velocity += ofVec2f(
		ofRandom(-0.5f, 0.5f),
		ofRandom(-0.5f, 0.5f));

	particle->velocity.limit(5.0f);

	particle->position += particle->velocity;

	particle->color = ofColor(
		ofRandom(255),
		ofRandom(255),
		ofRandom(255));
}

Particle * ParticleFactory::createParticle(const std::string & type) {
	Particle * particle = new Particle();
	if (type == "star") {
		particle->size = ofRandom(2.0f, 4.0f);
		particle->color = ofColor(255, 0, 0);
	} else if (type == "shooting_star") {
		particle->size = ofRandom(3.0f, 6.0f);
		particle->color = ofColor(0, 255, 0);
		particle->velocity *= 3.0f;
	} else if (type == "planet") {
		particle->size = ofRandom(5.0f, 8.0f);
		particle->color = ofColor(0, 0, 255);
	} else if (type == "comet") {
		particle->size = ofRandom(4.0f, 7.0f);
		particle->color = ofColor(255, 255, 0);
		particle->velocity *= 4.0f;
	}
	return particle;
}

ofApp::~ofApp() {
	for (Particle * p : particles) {
		removeObserver(p);
		delete p;
	}
	particles.clear();
}

void ofApp::setup() {
	ofBackground(0);
	particles.reserve(100 + 5 + 10);

	for (int i = 0; i < 100; ++i) {
		Particle * p = ParticleFactory::createParticle("star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 5; ++i) {
		Particle * p = ParticleFactory::createParticle("shooting_star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 10; ++i) {
		Particle * p = ParticleFactory::createParticle("planet");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 8; ++i) {
		Particle * p = ParticleFactory::createParticle("comet");
		particles.push_back(p);
		addObserver(p);
	}
}

void ofApp::update() {
	for (Particle * p : particles) {
		p->update();
	}
}

void ofApp::draw() {
	for (Particle * p : particles) {
		p->draw();
	}
}

void ofApp::keyPressed(int key) {
	switch (key) {
	case 's':
		notify("stop");
		break;
	case 'a':
		notify("attract");
		break;
	case 'r':
		notify("repel");
		break;
	case 'n':
		notify("normal");
		break;
	default:
		break;
	case 'c':
		notify("chaos");
		break;
	}
}

```

</details>

<details>
  <summary>ofApp.h</summary>

```h
#pragma once

#include "ofMain.h"
#include <string>
#include <vector>

class Observer {
public:
	virtual ~Observer() = default;
	virtual void onNotify(const std::string & event) = 0;
};

class Subject {
public:
	void addObserver(Observer * observer);
	void removeObserver(Observer * observer);

protected:
	void notify(const std::string & event);

private:
	std::vector<Observer *> observers;
};

class Particle;

class State {
public:
	virtual ~State() = default;
	virtual void update(Particle * particle) = 0;
	virtual void onEnter(Particle * particle) { }
	virtual void onExit(Particle * particle) { }
};

class Particle : public Observer {
public:
	Particle();
	~Particle() override;

	Particle(const Particle &) = delete;
	Particle & operator=(const Particle &) = delete;

	void update();
	void draw();
	void onNotify(const std::string & event) override;

	void setState(State * newState);

	ofVec2f position;
	ofVec2f velocity;
	float size;
	ofColor color;

private:
	void keepInsideWindow();
	State * state;
};

class NormalState : public State {
public:
	void update(Particle * particle) override;
	void onEnter(Particle * particle) override;
};

class AttractState : public State {
public:
	void update(Particle * particle) override;
};

class RepelState : public State {
public:
	void update(Particle * particle) override;
};

class StopState : public State {
public:
	void update(Particle * particle) override;
};

class ChaosState : public State {
public:
	void update(Particle * particle) override;
};

class ParticleFactory {
public:
	static Particle * createParticle(const std::string & type);
};

class ofApp : public ofBaseApp, public Subject {
public:
	~ofApp() override;
	void setup() override;
	void update() override;
	void draw() override;
	void keyPressed(int key) override;

private:
	std::vector<Particle *> particles;
};

```

</details>

## Fase 2
Evidencia 1

<img width="1331" height="824" alt="image" src="https://github.com/user-attachments/assets/13066f41-22c9-4797-a032-aaa8deb722be" />
<img width="1349" height="849" alt="image" src="https://github.com/user-attachments/assets/8ce14ab3-583e-419a-9de2-b935a3f162df" />

Se ubica el breakpoint en la linea 177, dentro de factory, ya que alli se toma la decision de que objeto crear, al detener la aplicacion ahi vemos en las variables locales el tipo "commet", mostrando que se esta siguiendo correctamente la logica de creacion y, al avanzar cuatro lineas mas vemos como la particula obtiene valores de size, color y velocity dentro de los parametros especificados. 



Evidencia 2

<img width="1351" height="860" alt="image" src="https://github.com/user-attachments/assets/8bf5eece-fc6a-4a9c-ab0b-5c18029a52d0" />
<img width="1355" height="867" alt="image" src="https://github.com/user-attachments/assets/b7521f76-aaa8-4e48-b31d-19e774c505bc" />
<img width="1349" height="863" alt="image" src="https://github.com/user-attachments/assets/64ee38bf-fe53-4307-8521-382b67cd2728" />
<img width="1350" height="865" alt="image" src="https://github.com/user-attachments/assets/17d82ea1-f4d0-46d4-8246-222ebf5a435c" />

Se eligió keyPressed() y setState() porque permiten observar cuándo se activa cada evento y qué objeto de estado nuevo recibe la partícula.
- Explicacion:
Al presionar n, setState() recibe un objeto NormalState*.
Al presionar c, setState() recibe un objeto ChaosState*.
- Justificacion:
Cambia el tipo del objeto apuntado por state, por lo tanto las funciones virtuales como update() usarán otra implementación. Esto muestra polimorfismo y el funcionamiento del patrón State: el comportamiento cambia reemplazando el objeto estado, no modificando la clase Particle.



Evidencia 3

<img width="1353" height="868" alt="image" src="https://github.com/user-attachments/assets/b5429539-90b8-4334-96df-88fb4972b061" />
<img width="1349" height="870" alt="image" src="https://github.com/user-attachments/assets/ed5a4bd5-a7c6-4414-865e-0d4e4bd93986" />
<img width="1351" height="860" alt="image" src="https://github.com/user-attachments/assets/5c2e2cab-c716-4cb4-bd38-bc9684b7d2cb" />
<img width="1355" height="869" alt="image" src="https://github.com/user-attachments/assets/4e1938b2-1ab8-4ce8-8109-90bfb2cc3254" />

Se colocaron breakpoints en: keyPressed(), notify(), onNotify() y setState(), ya que permiten observar todo el recorrido del evento desde la entrada del usuario hasta el cambio interno de comportamiento.
- Explicación:
Al presionar la tecla c, keyPressed() ejecuta:
notify("chaos")
Luego notify() recorre el vector de observers y envía el evento a cada partícula.
Después Particle::onNotify() recibe:
event = "chaos"
y finalmente se ejecuta:
setState(new ChaosState())
- Justificación:
Esto demuestra la cadena entre patrones:
Observer distribuye el evento a todas las partículas.
State cambia el comportamiento interno reemplazando el objeto estado.















