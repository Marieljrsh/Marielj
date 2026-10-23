# Proyecto Parte 1 — Chasis del carrito

## Objetivos de la sesión

- [✅] Modelar e imprimir en 3D la base del carrito, con un sitio fijo para el interruptor
- [✅] Imprimir los soportes de los motorreductores
- [✅] Armar el carrito con motores, llantas, rueda de apoyo, portapilas con interruptor, power bank, protoboard y ESP32
- [✅] Extra · Dejar documentado el orden de armado y los puntos débiles del diseño

## Materiales

- Impresora 3D con filamento PLA (blanco para la base, verde para los soportes)
- Onshape para el modelado
- BambuLab como laminador
- 2 motorreductores amarillos tipo TT
- 2 llantas de goma
- 1 rueda loca de apoyo (rueda circular que gira libremente)
- Portapilas 4AA con interruptor de 3 posiciones
- Power bank de 5 V con cable USB
- ESP32 sobre protoboard
- Módulo controlador de motores (puente H)
- Cables jumper
- Tornillos y tuercas
- Cinta doble cara y cinta aislante

## Desarrollo y resultados

### 1. Decisión de ruedas

El primer paso fue elegir el esquema de ruedas. Nos decidimos por dos ruedas motrices y una rueda loca, como en las aspiradoras robot tipo Roomba. Es la configuración más simple para girar sobre el propio eje: basta con que una llanta avance mientras la otra retrocede, sin necesidad de un mecanismo de dirección.

| Opción | Ventaja | Inconveniente |
| ------ | ------- | ------------- |
| 2 motrices + 1 loca (la elegida) | Pocos motores, giro sobre su eje, control simple | La rueda loca puede atorarse en superficies irregulares |
| 4 ruedas con motor | Más tracción | Más motores, más consumo y más cableado |
| 3 ruedas con dirección | Giro como en un auto | Hace falta un servo y es más complejo de controlar |

La rueda de apoyo va atrás para equilibrar el carrito y repartir el peso. Los motorreductores se sujetan a la base con tornillos y tuercas.

![Parte de abajo](../../recursos/imgs/Gemini_Generated_Image_tjnxuhtjnxuhtjnx.jpeg)
Figura 1. Vista inferior del chasis: llantas, rueda de apoyo y tornillería de los motores.

### 2. Modelado e impresión del chasis

El diseño está en Onshape, en el documento "Carro final", y se divide en tres piezas: soportes de motor, base y carcasa. Dividirlo así permite reimprimir solo la pieza que falla, en vez de todo el chasis. Las piezas se prepararon en BambuLab y se imprimieron en PLA: blanco para la base y verde para los soportes, lo que además ayuda a distinguirlas a simple vista.

![Armado](../../recursos/imgs/Gemini_Generated_Image_8903qk8903qk8903.jpeg)
Figura 2. Pieza del chasis impresa en PLA blanco.

Para los soportes de los motores TT partimos de un modelo ya hecho que bajamos de internet, lo importamos a Onshape y lo adaptamos. La geometría de estos soportes requiere mucha precisión, y así evitamos dibujarla desde cero.

![Modelo Onshape basemotores](../../recursos/imgs/Motorcarcasa%20(1).png)
Figura 2.1. Modelo 3D del soporte para motorreductores TT, descargado e importado a Onshape para asegurar los motores al chasis.

La base sí es diseño propio: una placa plana con esquinas achaflanadas y cortes laterales donde entran las llantas. Tiene orificios para atornillar los soportes de los motores y la rueda de apoyo.

![Modelo Onshape base](../../recursos/imgs/Base.png)
Figura 2.2. Modelo 3D de la base del chasis, diseñada en Onshape.

La carcasa cubre y protege la electrónica. Trae el compartimento del portapilas y el sitio fijo del interruptor en la parte trasera.

![Modelo Onshape chasis](../../recursos/imgs/Chasis.png)
Figura 2.3. Modelo 3D de la carcasa del carrito, diseñada en Onshape.

### 3. Energía para los motores

Los motores toman la energía de un portapilas 4AA, que da más voltaje y corriente que una sola pila y con eso más velocidad y fuerza. Un interruptor de tres posiciones enciende y apaga el sistema. Está en un soporte impreso en la parte trasera del chasis, así que queda firme y fácil de alcanzar.

![Interruptor y pilas](../../recursos/imgs/a11efd62-42ef-41cd-8a60-8060aef7728c.jpeg)
Figura 3. Interruptor montado en la pared impresa y portapilas 4AA con sus cables.

### 4. Energía y conexiones del control

El ESP32 recibe energía de una power bank de 5 V por USB, separada de la de los motores. Así, cuando los motores arrancan y piden un pico de corriente, el microcontrolador no se ve afectado. El ESP32 y el módulo controlador de motores están en la protoboard y se unen con jumpers.

El LED rojo encendido en la foto indica que la placa recibe corriente. Las soldaduras de los motores se cubrieron con cinta aislante para que no se rompieran.

![Diagrama de transmisión de motor](../../recursos/imgs/Gemini_Generated_Image_afnrd4afnrd4afnr.jpeg)

Figura 4. ESP32 y módulo controlador de motores en la protoboard, alimentados por USB.

### 5. Integración y reparto de componentes

Al final se montó todo en el chasis. Los motorreductores van en sus soportes verdes, uno a cada lado, con las llantas hacia afuera. La protoboard con el ESP32 se pegó con cinta doble cara al frente de la base y el portapilas quedó detrás. La parte trasera elevada del chasis contiene el portapilas y el interruptor, de modo que el peso queda atrás, cerca de la rueda de apoyo.

![Distribución](../../recursos/imgs/Gemini_Generated_Image_rz4c5rz4c5rz4c5r.jpeg)
Figura 5. Chasis ensamblado: motores, llantas, portapilas, protoboard y ESP32.

### Orden de armado recomendado

1. Imprimir base, soportes y carcasa, y comprobar que encajan antes de montar nada.
2. Atornillar los soportes verdes a la base y fijar los motorreductores.
3. Colocar las llantas en los ejes y la rueda loca en la parte trasera.
4. Cubrir con cinta aislante las soldaduras de los cables de los motores.
5. Instalar el portapilas y el interruptor en la zona trasera.
6. Pegar la protoboard con el ESP32 y el controlador de motores al frente.
7. Hacer las conexiones con jumpers y probar el sentido de giro de cada llanta antes de cerrar.

### Reparto de peso

| Zona | Componentes | Efecto |
| ---- | ----------- | ------ |
| Trasera | Portapilas, interruptor, rueda loca | Concentra la mayor parte de la masa y da estabilidad |
| Central | Motorreductores y llantas | Zona de tracción |
| Delantera | Protoboard, ESP32, controlador de motores | Poco peso; si pesara más, la rueda loca perdería apoyo |

Con el peso atrás, las llantas motrices conservan buen contacto con el piso. Si se añadiera algo pesado al frente (por ejemplo una cámara), habría que compensarlo moviendo el portapilas un poco hacia el centro o añadiendo un contrapeso en la parte trasera, para que el centro de masa siga quedando entre las llantas y la rueda loca. De lo contrario, el carro tendería a cabecear hacia adelante al frenar, la rueda loca se levantaría en las arrancadas y las llantas perderían tracción, lo que se notaría en giros menos precisos y en desvíos al avanzar en línea recta.

Como regla práctica, conviene que el centro de masa quede ligeramente detrás del eje de los motores: así las llantas cargan la mayor parte del peso y la rueda loca solo sirve de apoyo. Para comprobarlo basta con equilibrar el chasis sobre un lápiz colocado de forma transversal; el punto donde se sostiene sin inclinarse indica dónde está el centro de masa.