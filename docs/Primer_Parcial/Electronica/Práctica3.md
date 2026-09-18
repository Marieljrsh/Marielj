---
titulo: "Sesión 4 — Puente H, control de motores DC y servomotor"
fecha: 2026-09-18
autor: "Nombre"
equipo: "Nombre del equipo (si aplica)"
estado: borrador   # borrador | completa
---

# Sesión 4 — Puente H, control de motores DC y servomotor

## Qué debía lograr hoy
- [x] Simular el control de dos motores DC con un puente H (L293D)
- [x] Controlar el sentido de giro de un motor DC (adelante, atrás, izquierda, derecha)
- [x] Encender y controlar un servomotor con Arduino

## Qué usé
- Arduino UNO (simulado en Tinkercad)
- Módulo puente H L293D (simulado en Tinkercad)
- 2 motores DC (simulados en Tinkercad)
- 1 servomotor (simulado en Tinkercad)
- Batería de 9V (simulada en Tinkercad)
- Cables de conexión (simulados en Tinkercad)

## Qué hice y qué pasó (evidencia)
![Diagrama de puente H con cuatro transistores](../../recursos/imgs/puente_h_diagrama.png)
*Diagrama de un puente H con cuatro transistores (Q1-Q4), usado para controlar el sentido de giro de un motor DC.*

![Montaje inicial con transistor, resistencia y LED](../../recursos/imgs/transistor_led_basico.png)
*Montaje inicial con un transistor, resistencia y LED como punto de partida antes de armar el puente H completo.*

![Simulación de Arduino con puente H y motores DC](../../recursos/imgs/arduino_puente_h.png)
*Simulación en Tinkercad de un Arduino UNO controlando dos motores DC a través de un módulo puente H (L293D).*

![Código Arduino para motores DC y servomotor](../../recursos/imgs/arduino_motores_servo.png)
*Código en Arduino que controla dos motores DC mediante el puente H junto con el movimiento de un servomotor.*

## Qué falló y cómo lo resolví
- **Síntoma:** Fue una sorpresa lo sencillo que resulta controlar la dirección de un motor DC combinando señales HIGH/LOW en el puente H.
- **Cómo lo encontré:** Al simular el circuito y observar cómo variaban los estados de los pines del puente H y la polaridad aplicada al motor.
- **Solución:** Confirmé que la lógica del puente H era la adecuada y usé la configuración correcta para cambiar la dirección del giro sin necesidad de cambiar el hardware.

## Qué aprendí
Que el sentido de giro de un motor DC depende de la polaridad con la que se alimenta; que el puente H invierte terminales para cambiar de dirección; y que la velocidad depende del voltaje aplicado. También me di cuenta de lo versátil que puede ser combinar señales digitales con componentes de potencia para controlar movimiento con precisión.

## Siguiente paso
Combinar el control de los motores DC con el servomotor para lograr un movimiento coordinado.
