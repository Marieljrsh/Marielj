# Proyecto Parte 1 — Control remoto del carrito

## Qué debía lograr hoy

- [✅] Usar un control de Xbox como mando del carrito, conectado al ESP32 por Bluetooth
- [✅] Definir qué movimiento hace el carrito con cada dirección del joystick
- [✅] Tener tres velocidades seleccionables con botones
- [✅] Asegurar que el carrito se frene solo si el control se desconecta

## Qué usé

- Control inalámbrico de Xbox (con Bluetooth)
- ESP32 DevKit, que ya trae Bluetooth de baja energía (BLE)
- Librería `BLEGamepadClient` para leer el control desde el ESP32
- El mismo programa del carrito (ver la página de Programación)

## Qué hice y qué pasó (evidencia)

La idea fue no usar celular ni app: el control de Xbox habla directamente con el ESP32. El proceso de conexión quedó así:

1. Se enciende el carrito con el interruptor y el ESP32 ejecuta `controller.begin()`, que lo pone a buscar un control.
2. En el control se mantiene presionado el botón de sincronización hasta que el logo de Xbox parpadea rápido.
3. Cuando el logo se queda encendido fijo, ya están enlazados y el carrito responde.

Una vez conectados, el ESP32 revisa el control unas 50 veces por segundo (cada 20 ms). El joystick manda un número entre -1 y 1 en cada eje, y el programa solo reacciona si el valor pasa de 0.4 hacia cualquier lado.

**Movimiento (joystick izquierdo)**

| Dirección del stick | Motor izquierdo | Motor derecho | Resultado              |
|---------------------|-----------------|---------------|------------------------|
| Arriba ⬆️           | Adelante        | Adelante      | Avanza                 |
| Abajo ⬇️            | Atrás           | Atrás         | Retrocede              |
| Derecha ➡️          | Adelante        | Atrás         | Gira a la derecha en su lugar |
| Izquierda ⬅️        | Atrás           | Adelante      | Gira a la izquierda en su lugar |
| Centro (suelto)     | Detenido        | Detenido      | Se queda quieto        |

**Velocidad (botones)**

| Botón | Valor PWM | Velocidad aproximada |
|-------|-----------|----------------------|
| A     | 170       | Lenta (~67 %)        |
| B     | 210       | Media (~82 %), la que trae al encender |
| X     | 255       | Máxima (100 %)       |

**Seguridad:** si el control se apaga o se sale de rango, `controller.isConnected()` devuelve falso y el carrito apaga ambos motores de inmediato.

## Qué falló y cómo lo resolví

- **Síntoma:** El control nunca terminaba de enlazarse con el ESP32.
- **Cómo lo encontré:** El logo de Xbox se quedaba parpadeando todo el tiempo sin quedarse fijo.
- **Solución:** Descubrimos que el orden importa: primero hay que prender el carrito y luego activar el modo de emparejamiento del control. Así se conectó a la primera.

- **Síntoma:** Existía el riesgo de que el carrito siguiera avanzando sin control.
- **Cómo lo encontré:** Revisando el código nos preguntamos qué pasaba si se acababan las pilas del control; lo probamos apagándolo con el carrito en movimiento.
- **Solución:** Se agregó la revisión de `controller.isConnected()` al inicio del `loop`, para frenar el carrito antes de leer cualquier otra cosa.

## Qué aprendí

Antes pensaba que para manejar un robot a distancia se necesitaba una app o un módulo extra, pero el ESP32 puede leer directamente un control comercial por BLE. También entendí que un joystick es una entrada analógica: no dice solo "adelante" o "no", sino qué tanto se mueve, y por eso hay que poner un umbral para filtrar el ruido. Lo más importante que me llevo es pensar en qué pasa cuando algo falla; un vehículo a control remoto siempre debe frenarse si pierde la señal.

## Siguiente paso

Aprovechar que el joystick es analógico para que la velocidad dependa de qué tanto se empuja el stick, y usar el gatillo derecho como acelerador.
