# :floppy_disk:Bitácora de aplicación:floppy_disk:
# Actividad 6.

## Fase 1

<details>
  <summary>triangle.cpp</summary>

```cpp
#include <iostream>
#include <glad/glad.h>
#include <GLFW/glfw3.h>


// Callback: ajusta el viewport cuando cambie el tamaño de la ventana
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
	glViewport(0, 0, width, height);
}


void processInput(GLFWwindow* window) {
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldClose(window, true);
}

// Tamaño de las ventanas
const unsigned int SCR_WIDTH = 400;
const unsigned int SCR_HEIGHT = 400;

// Fuentes de los shaders
const char* vertexShaderSrc = R"glsl(
#version 460 core
layout(location = 0) in vec3 aPos;

uniform vec2 offset;

void main()
{
    vec3 newPos = aPos;
    newPos.x += offset.x;
    newPos.y += offset.y;

    gl_Position = vec4(newPos, 1.0);
}
)glsl";


const char* fragmentShaderSrc = R"glsl(
#version 460 core

out vec4 FragColor;
uniform vec4 ourColor;

void main()
{
    FragColor = ourColor;
}
)glsl";

// IDs globales
unsigned int VAO, VBO;
unsigned int shaderProg;

// Compila y linkea un programa de shaders, retorna su ID
unsigned int buildShaderProgram() {
	int success;
	char log[512];

	unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vs, 1, &vertexShaderSrc, nullptr);
	glCompileShader(vs);
	glGetShaderiv(vs, GL_COMPILE_STATUS, &success);
	if (!success) {
		glGetShaderInfoLog(vs, 512, nullptr, log);
		std::cerr << "ERROR VERTEX SHADER:\n" << log << "\n";
	}

	unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fs, 1, &fragmentShaderSrc, nullptr);
	glCompileShader(fs);
	glGetShaderiv(fs, GL_COMPILE_STATUS, &success);
	if (!success) {
		glGetShaderInfoLog(fs, 512, nullptr, log);
		std::cerr << "ERROR FRAGMENT SHADER:\n" << log << "\n";
	}

	unsigned int prog = glCreateProgram();
	glAttachShader(prog, vs);
	glAttachShader(prog, fs);
	glLinkProgram(prog);
	glGetProgramiv(prog, GL_LINK_STATUS, &success);
	if (!success) {
		glGetProgramInfoLog(prog, 512, nullptr, log);
		std::cerr << "ERROR LINKING PROGRAM:\n" << log << "\n";
	}

	glDeleteShader(vs);
	glDeleteShader(fs);
	return prog;
}

// Crea un VAO/VBO con los datos de un triángulo
void setupTriangle() {
	float vertices[] = {
		-0.5f, -0.5f, 0.0f,
		 0.5f, -0.5f, 0.0f,
		 0.0f,  0.5f, 0.0f
	};
	glGenVertexArrays(1, &VAO);
	glGenBuffers(1, &VBO);

	glBindVertexArray(VAO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
	glBindVertexArray(0);
}


int main()
{
	// 1) Inicializar GLFW
	if (!glfwInit()) {
		std::cerr << "Fallo al inicializar GLFW\n";
		return -1;
	}
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

	// 2) Crear ventana
	GLFWwindow* mainWindow = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "Ventana", nullptr, nullptr);
	if (!mainWindow) {
		std::cerr << "Error creando ventana1\n";
		glfwTerminate();
		return -1;
	}

	// 3) Lee el tamaño del framebuffer
	int bufferWidth, bufferHeight;
	glfwGetFramebufferSize(mainWindow, &bufferWidth, &bufferHeight);
	
	// 4) Callbacks 
	glfwSetFramebufferSizeCallback(mainWindow, framebuffer_size_callback);


	// 5) Cargar GLAD y recursos en contexto de window1
	glfwMakeContextCurrent(mainWindow);

	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
		std::cerr << "Fallo al cargar GLAD (contexto1)\n";
		return -1;
	}

	// 6) Habilita el V-Sync
	glfwSwapInterval(1);

	// 7) Compila y linkea shaders
	shaderProg = buildShaderProgram();

	glUseProgram(shaderProg);

	int offsetLocation = glGetUniformLocation(shaderProg, "offset");
	int colorLocation = glGetUniformLocation(shaderProg, "ourColor");

	// 8) Genera el contenido a mostrar
	setupTriangle();

	// 9) Configura el viewport
	glViewport(0, 0, bufferWidth, bufferHeight);


	// 10) Loop principal
	while (!glfwWindowShouldClose(mainWindow))
	{
		// 11) Manejo de eventos
		glfwPollEvents();

	
		// 12) Procesa la entrada
		processInput(mainWindow);

		// 13) Configura el color de fondo y limpia el framebuffer
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);
		
		// 14) Indica a OpenGL que use el shader program
		glUseProgram(shaderProg);

		float timeValue = glfwGetTime();

		float moveX = sin(timeValue) * 0.5f;

		float red = (sin(timeValue) + 1.0f) / 2.0f;
		float green = (cos(timeValue) + 1.0f) / 2.0f;

		glUniform2f(offsetLocation, moveX, 0.0f);
		glUniform4f(colorLocation, red, green, 0.2f, 1.0f);

		// 15) Activa el VAO y dibuja el triángulo
		glBindVertexArray(VAO);
		glDrawArrays(GL_TRIANGLES, 0, 3);

		// 16) Intercambia buffers y muestra el contenido
		glfwSwapBuffers(mainWindow);
	}

	// 17) Limpieza
	glfwMakeContextCurrent(mainWindow);
	glDeleteVertexArrays(1, &VAO);
	glDeleteBuffers(1, &VBO);
	glDeleteProgram(shaderProg);

	glfwDestroyWindow(mainWindow);
	glfwTerminate();
	return 0;
}
```

</details>

(Contiene código C++ + Vertex Shader + Fragment Shader)

## Fase 2

- Evidencia #1

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/5f3f9fa1-09a5-4bb8-8883-4441f14f7a4c" />
-
<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/2ecc1e3e-9c5f-405b-a3b4-2bc9130b9093" />

Explicación: Primero se usa glfwInit() para iniciar GLFW, para después crear la ventana con glfwCreateWindow(). Luego con glfwMakeContextCurrent(mainWindow) esa ventana queda con el contexto OpenGL activo. luego de eso se usa gladLoadGLLoader(...), que sirve para cargar las funciones de OpenGL que voy a usar en el programa.

Justificacion: GLFW tiene que ir primero porque es el que crea la ventana y prepara el contexto. Sin ese contexto GLAD no tendría de dónde cargar las funciones de OpenGL, por eso ese orden es necesario.



- Evidencia #2

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/6745756f-65fe-4a2e-84d7-fcad73a815d6" />
-
<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/6f825f09-72f7-4f6f-9956-e76cc55e52a1" />

Explicacion: Primero el arreglo vertices[] guarda las posiciones de los 3 puntos del triángulo. Después con glBufferData esos datos se mandan al buffer de OpenGL. Luego con glVertexAttribPointer se le dice a OpenGL cómo leer esos datos: en grupos de 3 números (x, y, z) y usarlos en el atributo 0

Justificación: El shader no lee directamente el arreglo, primero se deben guardar los datos en un buffer y después indicar cómo usarlos. Así OpenGL sabe de dónde sacar la posición de cada vértice



- Evidencia #3

<img width="636" height="500" alt="Grabacindepantalla2026-04-29104651-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/da1f665a-4bc2-411d-afea-997686ec018a" />

-
<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/fdebc057-31ee-4506-9523-fa610b984c03" />

Explicación: En el programa usé glUniform2f(...) para mover el triángulo y glUniform4f(...) para cambiar su color; el arreglo vertices[] nunca cambia, siempre tiene los mismos puntos. Lo que cambia son los valores que se mandan al shader en cada frame

Justificación: Esto es posible porque los uniforms son valores externos que el shader recibe antes de dibujar, cambiando el comportamiento del shader sin reescribir los valores originales



- Evidencia #4

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/d1e20372-5ee8-4cc4-a152-d57c29a51725" />

Explicación:
Cambié el valor del offset a 2.0 en X

Esperaba que el triángulo se moviera demasiado hacia la derecha

Eso fue lo que pasó: salió de la zona visible y dejó de verse en pantalla

Justificación: OpenGL dibuja dentro del rango visible normal (NDC), que normalmente va entre -1 y 1. Como moví el triángulo más allá de ese rango, quedó fuera de la vista

Conclusión: Los uniforms pueden cambiar mucho el resultado visual. Si se usan valores exagerados, el objeto puede salir de pantalla aunque el VBO esté bien



- Evidencia #5

¿por qué usé uniforms para el movimiento y el color del triángulo?

<img width="1024" height="512" alt="image" src="https://github.com/user-attachments/assets/aa887aa1-f182-4b4f-8fff-3e9dd61633cf" />

Explicación: Decidí usar uniforms porque el movimiento y el color eran valores generales para todo el triángulo en cada frame, no era necesario mandar un color diferente para cada vértice ni una posición distinta por punto. Por eso era mejor usar un uniform y cambiarlo desde el codigo.

Justificación: Los atributos son una opcion mas factible cuando cada vértice necesita datos propios, como posiciones, normales o colores distintos. En este caso todo el triángulo se mueve junto y cambia como una sola figura, así que uniform era una opción correcta y sencilla
