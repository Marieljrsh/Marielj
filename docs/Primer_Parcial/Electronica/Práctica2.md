---
titulo: "Sesión 3 — ESP32, control de LEDs"
fecha: 2026-09-18
autor: "Nombre"
equipo: "Nombre del equipo (si aplica)"
estado: borrador   # borrador | completa
---

# Sesión 3 — ESP32, control de LEDs

## Qué debía lograr hoy
- [x] Encender un LED con el ESP32
- [x] Encender dos LEDs al mismo tiempo con el ESP32
- [x] Hacer que dos LEDs parpadeen a destiempo con el ESP32
- [x] Hacer que un LED parpadee al presionar un botón con el ESP32
- [x] Presionar el botón para que, con ayuda del ESP32, la computadora reciba una señal
- [x] Conectar el ESP32 al teléfono para enviar una señal al presionar el botón
- [x] Conectar el teléfono al ESP32 para encender o apagar el LED

## Qué usé
- ESP32
- Protoboard
- 2 LEDs azules
- 2 resistencias de 220 Ω
- Botón pulsador
- Cables de puente
- Cable USB

## Qué hice y qué pasó (evidencia)
![LED encendido mediante ESP32 con resistencia en protoboard](../../recursos/imgs/esp32_led_encendido.png)
*LED encendido mediante el ESP32, conectado a través de una resistencia en la protoboard.*

![Dos LEDs parpadeando a destiempo con ESP32](../../recursos/imgs/esp32_leds_destiempo.png)
*Dos LEDs (rojo y azul) parpadeando a destiempo, cada uno controlado de forma independiente con el ESP32.*

![Botón de entrada activando LED con el ESP32](../../recursos/imgs/esp32_boton_led.png)
*Al presionar el botón se enciende un LED y, al soltarlo, se enciende el otro.*

![Señal de botón en monitor serial del ESP32](../../recursos/imgs/esp32_serial.png)
*Al presionar el botón, el ESP32 detecta el estado del pin y envía la señal "PRESIONADO" a la computadora mediante el monitor serial.*

![Código Arduino para Bluetooth con ESP32](../../recursos/imgs/esp32_bluetooth_codigo.png)
*Código en Arduino IDE que configura el ESP32 como dispositivo Bluetooth, permitiendo recibir comandos "ON"/"OFF" desde el celular.*

![Código Arduino para control de LEDs por señal recibida](../../recursos/imgs/esp32_control_led_codigo.png)
*Código en Arduino IDE donde el ESP32 lee el estado de un pin de entrada y enciende o apaga los LEDs según la señal recibida.*

## Qué falló y cómo lo resolví
- **Síntoma:** Se confundían los pines de entrada y salida en el código.
- **Cómo lo encontré:** Revisando línea por línea la programación contra la conexión física del circuito y comparando con la disposición de pines del ESP32.
- **Solución:** Consulté la datasheet del ESP32 y corregí la asignación de pines para que las entradas y salidas coincidieran con el hardware real.

## Qué aprendí
Aprendí a identificar entradas y salidas del ESP32 e interpretar su datasheet; a controlar el estado de un LED desde código; y a entender que el ESP32 puede comunicarse por Bluetooth con un celular para recibir órdenes en tiempo real. También comprendí que un “error de conexión” puede ser un problema de configuración lógica y no solo físico.

## Siguiente paso
Explorar cómo controlar más de un LED de forma independiente vía Bluetooth desde el celular.
