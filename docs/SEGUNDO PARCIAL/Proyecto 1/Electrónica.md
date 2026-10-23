# Proyecto Parte 1 — Electrónica del carrito

## Objetivos de la sesión

- [✅] Unir el ESP32 con el puente H TB6612FNG
- [✅] Conectar los dos motores DC a las salidas del puente H
- [✅] Dar energía al circuito con pilas y un interruptor de encendido
- [✅] Extra · Documentar el recorrido de la energía y una lista de revisión por si algo falla

## Materiales

- ESP32 DevKit (ESP32-WROOM-32)
- Módulo puente H TB6612FNG (rojo)
- 2 motorreductores DC tipo TT
- Portapilas con 4 pilas AA (6 V) e interruptor
- Power bank de 5 V para el ESP32
- Interruptor basculante (switch)
- Protoboard mini
- Cables jumper
- Multímetro

## Desarrollo y resultados

### Idea general del circuito

El circuito tiene dos partes separadas que solo comparten la tierra. La parte de **control** es el ESP32, que decide qué hacer y manda señales de bajo consumo. La parte de **potencia** son las pilas, el puente H y los motores, que es donde circula la corriente grande. El ESP32 no puede mover los motores directamente porque sus pines entregan muy poca corriente, así que el TB6612 funciona como intermediario: recibe las órdenes del ESP32 y las convierte en corriente para los motores. Las pilas pasan por el interruptor antes de llegar al puente H, de modo que al apagarlo se cortan los motores sin desconectar el ESP32. Este último se alimenta aparte con la power bank de 5 V. El pin STBY quedó unido a 3.3 V para que el driver esté siempre habilitado.

### Recorrido de la energía

| Tramo | Voltaje | Qué alimenta |
| ----- | ------- | ------------ |
| Pilas → interruptor → VM | 6 V | Los motores (potencia) |
| Power bank → ESP32 | 5 V | El microcontrolador |
| 3.3 V del ESP32 → VCC y STBY | 3.3 V | La lógica interna del puente H |

### Conexiones

| Desde | Hacia | Descripción |
| ----- | ----- | ----------- |
| Batería (+) | Interruptor | Encendido / apagado de los motores |
| Interruptor | VM del puente H | Alimentación de potencia de 6 V |
| Power bank (5 V) | ESP32 | Alimentación del microcontrolador |
| Batería (−) | GND común | Tierra compartida por todo el circuito |
| GND del ESP32 | GND común | Tierra compartida con el puente H y la batería |
| ESP32 GPIO 17 | AIN1 | Dirección motor izquierdo |
| ESP32 GPIO 16 | AIN2 | Dirección motor izquierdo |
| ESP32 GPIO 23 | PWMA | Velocidad motor izquierdo |
| ESP32 GPIO 19 | BIN1 | Dirección motor derecho |
| ESP32 GPIO 18 | BIN2 | Dirección motor derecho |
| ESP32 GPIO 22 | PWMB | Velocidad motor derecho |
| ESP32 3.3 V | STBY | Driver siempre habilitado |
| ESP32 3.3 V | VCC del puente H | Alimentación lógica |
| AO1 / AO2 | Motor izquierdo | Salida de potencia |
| BO1 / BO2 | Motor derecho | Salida de potencia |

### Agrupación de los pines del TB6612

| Grupo | Pines | Quién los usa |
| ----- | ----- | ------------- |
| Potencia | VM, GND, AO1/AO2, BO1/BO2 | Pilas y motores |
| Lógica | VCC, GND, STBY | ESP32 (3.3 V) |
| Control | AIN1, AIN2, PWMA, BIN1, BIN2, PWMB | Señales del ESP32 |

El puente H trabaja con dos voltajes a la vez: 6 V para mover los motores y 3.3 V para su lógica. Como la señal de los GPIO también es de 3.3 V, los niveles coinciden y no hace falta un convertidor.

### Mediciones

| Medición | Valor medido | Valor esperado |
| -------- | ------------ | -------------- |
| Voltaje de la batería | ___ V | Cerca de 6 V con pilas nuevas |
| Voltaje en el motor (PWM 255) | ___ V | Un poco menos que el de la batería, por la caída del driver |
| Voltaje en el pin 3.3 V del ESP32 | ___ V | Cerca de 3.3 V |
| Voltaje en STBY | ___ V | Cerca de 3.3 V |

### Lista de revisión antes de encender

1. La tierra del ESP32, la del puente H y el negativo de las pilas están unidas.
2. STBY está conectado a 3.3 V y no queda al aire.
3. VM recibe los 6 V después del interruptor y VCC recibe 3.3 V.
4. Cada motor está en su par de salidas (AO1/AO2 el izquierdo, BO1/BO2 el derecho).
5. Las pilas están cargadas y bien sujetas en el portapilas.

### Limitaciones del montaje

- Las uniones de la protoboard mini son sueltas y pueden aflojarse con las vibraciones del carrito. Más adelante convendría soldar o usar terminales atornillables.
- Los cables de los motores y los de señal van cerca; esto puede meter ruido eléctrico en las líneas del ESP32.
- Con pilas gastadas el voltaje baja, y con él la velocidad y la fuerza de los motores, aunque el PWM sea el mismo.
- No hay fusible ni protección contra polaridad invertida, así que conectar mal las pilas puede dañar el puente H.

## Problemas encontrados y cómo los resolví

- **Síntoma:** los motores no giraban aunque el programa sí se estaba ejecutando.
- **Cómo lo detecté:** medí con el multímetro y no había voltaje en las salidas del puente H.
- **Solución:** el pin STBY estaba sin conectar. Si no está en nivel alto, el driver permanece apagado. Lo uní a 3.3 V del ESP32.

- **Síntoma:** el ESP32 se reiniciaba cuando los motores arrancaban.
- **Cómo lo detecté:** el LED del ESP32 se apagaba justo en el momento en que los motores empezaban a girar.
- **Solución:** comprobé que todas las tierras estuvieran unidas y cambié a pilas con carga completa. Como arrancar un motor consume un pico de corriente, unas pilas gastadas hacen que el voltaje caiga y afecte al resto del circuito.

## Conclusiones y aprendizajes

- Un puente H sirve para dos cosas: entregar a los motores más corriente de la que da el ESP32 e invertir el sentido de giro.
- El TB6612FNG apaga sus salidas si STBY no está en nivel alto. Conectarlo directo a 3.3 V evita gastar un GPIO.
- La tierra común es lo que permite que las señales del ESP32 tengan sentido para el puente H. Sin ella los niveles no se interpretan bien.
- Separar la alimentación del control (power bank) de la de los motores (pilas) reduce los problemas cuando los motores arrancan, pero no los elimina si la tierra está mal unida o las pilas están bajas.
- Medir con el multímetro ayuda a descubrir si el fallo está en el programa o en el circuito: si hay señal en los pines de control pero no en las salidas, el problema está en el driver.
- Para la siguiente etapa, conviene que el interruptor y el portapilas queden fijos en el chasis, para que ningún cable se desconecte con el movimiento.

## Próximos pasos

- Terminar el chasis
- Fijar el interruptor, el portapilas y la protoboard para que no se muevan
- Completar la tabla de mediciones con los valores reales