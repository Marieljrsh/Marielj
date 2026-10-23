---
fecha: 2026-10-23
autor: "Mariel Jocelyn Rosas Herrera"
---

# Proyecto Parte 1 — Programación del carrito

## Objetivos de la sesión

- [✅] Escribir el programa del ESP32 para manejar dos motores DC mediante el puente H TB6612FNG
- [✅] Lograr que el carrito avance, retroceda y gire hacia ambos lados
- [✅] Regular la velocidad de los motores con PWM
- [✅] Extra · Identificar qué limita este programa para usarlo después con un control Bluetooth

## Materiales

- ESP32 DevKit (ESP32-WROOM-32, con puerto USB-C)
- Arduino IDE o PlatformIO con el núcleo ESP32 3.x
- Módulo puente H TB6612FNG
- Cable USB-C para subir el programa

## Desarrollo y resultados

### Estrategia del programa

Empecé asignando los pines del ESP32 que van al puente H. Cada motor ocupa tres: dos para indicar el sentido de giro y uno para la velocidad por PWM. Luego escribí una función por motor que recibe un número (1 para adelante, -1 para atrás y 0 para detenerse) y encima armé las funciones de movimiento del carrito combinando ambos motores. El `loop` solo repite una rutina de prueba: recorre todos los movimientos y termina avanzando con tres velocidades distintas.

### Conexiones

| Señal del TB6612 | Pin del ESP32 | Nombre en el código | Función |
| ---------------- | ------------- | ------------------- | ------- |
| AIN1 | GPIO 17 | `nAlto` | Dirección motor izquierdo |
| AIN2 | GPIO 16 | `nBajo` | Dirección motor izquierdo |
| PWMA | GPIO 23 | `pwma` | Velocidad motor izquierdo |
| BIN1 | GPIO 19 | `rAlto` | Dirección motor derecho |
| BIN2 | GPIO 18 | `rBajo` | Dirección motor derecho |
| PWMB | GPIO 22 | `pwmb` | Velocidad motor derecho |
| STBY | 3.3 V | — (no usa GPIO) | Driver siempre habilitado |

### Código

```cpp
#include <Arduino.h>

// Motor izquierdo (canal A del TB6612)
#define nAlto 17   // AIN1
#define nBajo 16   // AIN2
#define pwma  23   // PWMA
// Motor derecho (canal B del TB6612)
#define rAlto 19   // BIN1
#define rBajo 18   // BIN2
#define pwmb  22   // PWMB
// STBY va conectado directo a 3.3 V del ESP32 (no usa GPIO)
// =====================================================

// ----- Velocidades (PWM de 8 bits: 0 a 255) -----
const int velA = 170;   // lenta
const int velB = 210;   // media
const int velX = 255;   // rápida
int vel = velB;

// dir: 1 = adelante, -1 = atrás, 0 = alto
void motorIzq(int dir) {
  digitalWrite(nAlto, dir == 1  ? HIGH : LOW);
  digitalWrite(nBajo, dir == -1 ? HIGH : LOW);
  ledcWrite(pwma, dir == 0 ? 0 : vel);
}

void motorDer(int dir) {
  digitalWrite(rAlto, dir == 1  ? HIGH : LOW);
  digitalWrite(rBajo, dir == -1 ? HIGH : LOW);
  ledcWrite(pwmb, dir == 0 ? 0 : vel);
}

void alto() {
  motorIzq(0);
  motorDer(0);
}

void adelante()  { motorIzq(1);  motorDer(1);  }
void atras()     { motorIzq(-1); motorDer(-1); }
void derecha()   { motorIzq(1);  motorDer(-1); }  // izquierda avanza, derecha retrocede
void izquierda() { motorIzq(-1); motorDer(1);  }  // derecha avanza, izquierda retrocede

void setup() {
  pinMode(rAlto, OUTPUT);
  pinMode(rBajo, OUTPUT);
  pinMode(nAlto, OUTPUT);
  pinMode(nBajo, OUTPUT);

  // PWM a 1 kHz, 8 bits (core ESP32 3.x)
  ledcAttachChannel(pwma, 1000, 8, 0);
  ledcAttachChannel(pwmb, 1000, 8, 1);

  alto();               // arranca detenido
  delay(2000);          // tiempo para soltar el carrito
}

void loop() {
  // ----- Movimientos con velocidad media -----
  vel = velB;
  adelante();   delay(2000);
  alto();       delay(1000);
  atras();      delay(2000);
  alto();       delay(1000);
  derecha();    delay(1000);
  alto();       delay(1000);
  izquierda();  delay(1000);
  alto();       delay(2000);

  // ----- Prueba de velocidades avanzando -----
  vel = velA;
  adelante();   delay(2000);
  alto();       delay(1000);

  vel = velB;
  adelante();   delay(2000);
  alto();       delay(1000);

  vel = velX;
  adelante();   delay(2000);
  alto();       delay(3000);
}
```

*Programa subido al ESP32. El carrito repite sin parar la rutina de movimientos y la prueba de velocidades.*

### Cómo se mueve el TB6612 según los pines

| Pin de dirección 1 | Pin de dirección 2 | Efecto en el motor |
| ------------------ | ------------------ | ------------------ |
| HIGH | LOW | Gira en un sentido (adelante en el código) |
| LOW | HIGH | Gira en sentido contrario (atrás) |
| LOW | LOW | Sin giro; el motor queda libre y se detiene por inercia |

La velocidad no depende de estos pines sino del PWM. Por eso `alto()` pone las direcciones en LOW y además manda PWM en 0.

### Velocidades usadas

| Constante | Valor PWM | Ciclo de trabajo aproximado |
| --------- | --------- | --------------------------- |
| `velA` (lenta) | 170 | 67 % |
| `velB` (media) | 210 | 82 % |
| `velX` (rápida) | 255 | 100 % |

Con 8 bits el PWM tiene 256 niveles (0 a 255), así que el ciclo de trabajo es valor / 255.

### Lectura de las funciones

| Parte del código | Qué hace |
| ---------------- | -------- |
| `motorIzq()` / `motorDer()` | Fijan el sentido y la velocidad de un solo motor |
| `adelante()`, `atras()`, `derecha()`, `izquierda()` | Mezclan los dos motores para producir cada movimiento |
| `ledcAttachChannel()` | Asocia un pin a un canal PWM de 1 kHz y 8 bits |
| `vel` | Variable global con el PWM que reciben los dos motores |
| `alto()` | Detiene ambos motores |

### Limitaciones que encontré en el programa

- `vel` es una sola variable para los dos motores, así que no se puede dar más velocidad a un lado que al otro. Por eso `derecha()` e `izquierda()` son giros sobre el propio eje y no curvas abiertas.
- Todos los `delay()` bloquean el programa. Mientras espera, el ESP32 no puede leer nada, ni un sensor ni un control.
- La rutina de prueba vive dentro de `loop()`, por lo que no hay forma de interrumpirla sin reiniciar la placa.
- Los nombres de los pines no siguen la misma lógica: el motor izquierdo usa `nAlto`/`nBajo` y el derecho `rAlto`/`rBajo`, y `pwma`/`pwmb` van en minúsculas. Funciona, pero cuesta recordar cuál es cuál.
- Que `adelante()` mueva las dos llantas hacia el frente depende de la conexión física de los motores. Como los motores van en espejo en el chasis, la conexión de uno se invirtió en la parte 1; si se cambiara de nuevo ese cableado, habría que invertir el sentido en el código.

## Problemas encontrados y cómo los resolví

- **Síntoma:** al compilar salían errores en `ledcSetup` y `ledcAttachPin`.
- **Cómo lo detecté:** el compilador avisaba que esas funciones no existen en el núcleo ESP32 3.x.
- **Solución:** las reemplacé por `ledcAttachChannel(pin, frecuencia, resolución, canal)` y cambié `ledcWrite` para que reciba el pin en lugar del canal.

| Núcleo ESP32 2.x | Núcleo ESP32 3.x |
| ---------------- | ---------------- |
| `ledcSetup(canal, frecuencia, resolución)` | `ledcAttach(pin, frecuencia, resolución)` o `ledcAttachChannel(pin, frecuencia, resolución, canal)` |
| `ledcAttachPin(pin, canal)` | Ya está incluido en la función anterior |
| `ledcWrite(canal, valor)` | `ledcWrite(pin, valor)` |

- **Síntoma:** el carrito se quedaba detenido a pesar de que el código cargaba bien.
- **Cómo lo detecté:** revisé las conexiones del módulo y vi que `STBY` debe estar en nivel alto para que el driver trabaje.
- **Solución:** conecté `STBY` directo a 3.3 V, de modo que el driver siempre queda habilitado y no gasta un GPIO.

## Conclusiones y aprendizajes

- Para mover un motor con un microcontrolador hacen falta dos señales distintas: la dirección (dos pines digitales) y la velocidad (una salida PWM).
- El PWM no cambia el voltaje, sino el porcentaje del tiempo que el motor recibe energía. Con 170 de 255 el motor está encendido cerca de dos tercios del tiempo.
- Para girar sobre su eje, el carrito necesita que una llanta avance mientras la otra retrocede. Es la ventaja de la configuración de dos ruedas motrices con rueda loca que se eligió en la parte 1.
- Una función por motor y otra por movimiento hace que el código sea fácil de reutilizar: cambiar la secuencia solo implica reordenar llamadas.
- Las librerías cambian entre versiones del núcleo. Antes de copiar ejemplos de internet conviene revisar con qué versión fueron escritos.
- Ideas para probar: bajar `velA` para encontrar el PWM mínimo con el que los motores TT empiezan a moverse, y comprobar que la tierra (GND) del ESP32, del TB6612 y del portapilas sea común.
- Para el control de Xbox, este código hay que reorganizarlo: quitar los `delay()`, leer el control en cada vuelta del `loop`, y separar la velocidad de cada motor para poder dar curvas con el joystick.

## Próximos pasos

- Conectar un control de Xbox por Bluetooth al ESP32.
- Cambiar los `delay()` por temporizadores con `millis()`.
- Usar una velocidad por motor para poder girar de forma gradual.