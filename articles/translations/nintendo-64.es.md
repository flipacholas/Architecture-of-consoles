---
name: Nintendo 64
releaseDate: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: Potente y complicada!
cover: nintendo64
javascript: ['threejs']
top_tabs:
  Model:
    caption: "La Nintendo 64
    <br>Lanzada el 23/06/1996 en Japón, el 29/09/1996 en América y el 01/03/1997 en Europa"
    file: "international"
  Motherboard:
    caption: "Mostrando la revision 'NUS-CPU-03'.
    <br>Versiones posteriores redujeron el número de chips requeridos para la codificar el audio y video
    <br>El conector de 'Disk Drive' se encuentra en la otra cara"
  Diagram: {}

# SEO
title: Arquitectura de la Nintendo 64

# Historical
aliases: [/es/projects/consoles/nintendo-64/]
---

## Una breve introducción

El objetivo de Nintendo era dar a los jugadores los mejores gráficos posibles, para llevar a cabo esta tarea, se asociaron con uno de los mayores expertos en gráficos de la industria para producir el chip *definitivo*.

El resultado fue una bonita consola para toda la familia... y un manual de instrucciones de 500 páginas para los desarrolladores.

No te preocupes, te prometo que este artículo no va a ser tan largo como el manual... ¡Disfrútalo!

---

## CPU

El procesador principal es un **NEC VR4300** que corre a **93.75 MHz**, una versión compatible binaria del **MIPS R4300i** de Silicon Graphics que incluye:
- **Dos modos de operación**:
  - **Modo de 32-bit**: Modo tradicional y el más común para los juegos. Las *words* (palabras) tienen una longitud de 32 bits. 
  - **Modo de 64-bit**: Las *words* tienen 64 bits de longitud (llamadas *doublewords*). Esto incluye registros, datos y direcciones de memoria - aunque solo se decodifican 40 bits en el último caso. Consecuentemente, grandes cantidades de datos pueden ser operados con más eficiencia. Por otro lado, esto también incrementa el tamaño del programa consideradamente (por ejemplo, los punteros ocupan 8 Bytes en vez de 4).
- **32 registros de uso general**: En el modo de 32-bits, los registros tienen 32-bits de ancho. En el modo de 64-bits, el doble.
- Set de instrucciones **MIPS III ISA**: Sucesor del MIPS II con nuevas instrucciones de 64 bits que operan *doublewords*. El formato de instrucciones sigue teniendo una longitud de 32-bit, independientemente del modo.
- Un bus de datos interno de **64 bits** conectado a un **bus externo de 32 bits**: Aunque las *doublewords* no afectaran el rendimiento mientras sean operadas internamente, la CPU aún necesitará gastar ciclos adicionales para mover datos de 64 bits por todo el sistema.
- Bus de direcciones de **32 bits**: Hasta 4 GB de memoria física puede ser direccionada.
- **Segmentación de instrucciones de 5 etapas**: Hasta cinco instrucciones pueden ser ejecutadas simultáneamente (para una explicación detallada, puedes visitar [este otro articulo]({{< ref path="sega-saturn#cpu" lang="en" >}}).
- **24 KB de L1 cache**: Dividido en 16 KB para instrucciones y 8 KB para datos.

También se incluye en este paquete una **FPU de 64 bits**, la CPU la identifica como un co-procesador (*CP1*) aunque la unidad está instalada junto a la ALU (unidad aritmética) y solo se accede a ella a través de esa unidad, con lo que no hay co-procesamiento de por si. De todas maneras, la FPU contiene un archivo de registro dedicado y acelerará las operaciones con números de coma flotante de 64 y 32 bits. La FPU sigue el estándar IEEE754.

### Simplificando el acceso a la memoria

La manera en la que la RAM está ensamblada sigue la **arquitectura de memoria unificada** (o 'UMA', del inglés 'Unified-memory Architecture') donde toda la RAM disponible se centraliza en un solo lugar y cualquier otro componente que requiera RAM accederá a esta ubicación compartida. El componente que arbitra su acceso es, en este caso, la GPU.

La razón por la cual se eligió este diseño es por el ahorro considerable en costes de producción, mientras que por otro lado, incrementa la contención de acceso si no se gestiona adecuadamente.

### ¿Sin controlador de DMA?

Debido a la arquitectura de memoria unificada, la CPU ya no tiene acceso directo a la RAM, por lo que la GPU también proporciona la funcionalidad DMA ('Direct Memory Access').

### Diseño de la memoria

Aparte de la UMA, la estructura de la RAM es un poco complicada, así que trataré de explicarlo en términos simples, aquí va...

El sistema contiene físicamente **4,5 MB de RAM**, sin embargo, está conectada a un bus de datos de **9 bits** donde el 9º bit está reservado para la GPU (más detalles en adelante). Como consecuencia, cada componente excepto la GPU sólo encontrará **hasta 4 MB**.

{{< figure_img src="memory.png" alt="Memory Diagram" img_class="no-borders" class="centered-container" >}}
Trazado de la memoria en este sistema.  
Asumo que la velocidad del CPU-RCP bus es la velocidad de reloj del RCP o la de la CPU, pero aún no he conseguido confirmarlo.
{{< /figure_img >}}

El tipo de memoria RAM instalada en la placa se llama **Rambus DRAM** o 'RDRAM' para abreviar, este fue otro diseño que compitió con SDRAM para convertirse en el siguiente estándar. RDRAM está conectada en **serie** (donde las transferencias se hacen de un bit a la vez) mientras que SDRAM utiliza una **conexión paralela** (transfiere múltiples bits a la vez)..

La latencia de la RDRAM es directamente proporcional al número de bancos instalados, por lo que en este caso, con la cantidad de RAM que tiene este sistema, la latencia resultante **es importante** (un post en el foro beyond3d menciona que es alrededor de **640ns**). Aunque esto es compensado con una alta velocidad de reloj de **250 MHz** (~2.6 veces más rápida que la CPU) aplicada a los bancos de memoria. Nintendo afirma que '\[RDRAM\] puede proveer una transferencia de datos de alta velocidad a 500MB por segundo para leer o escribir datos consecutivos'.

Además, hay otro debate en los foros de beyond3d que afirma que Nintendo escogió los bancos de memoria uPD488170L de NEC por su placa base. Estos chips utilizan una tecnología denominada 'Rambus Signaling Logic' que duplica la tasa de transferencia. Esto puede explicar porque algunas fuentes declaran que la tasa 'efectiva' es 500 MHz

Finalmente, la cantidad de RAM disponible en esta consola **se puede ampliar** instalando el accesorio *Expansion Pak*: Una bonita pequeña caja que incluye 4,5 MB. Curiosamente, el bus de RAM debe ser terminado, por lo que la consola siempre se vende con un terminador (llamado *Jumper Pak*) instalado en el lugar del Expansion Pak. Ahora, uno podría preguntarse, ¿qué pasaría si encendemos la consola sin ningún *Pak* instalado? **Literalmente nada**, ¡te quedas con una pantalla en blanco!

### Gestión de memoria

El VR4300 incluye otro procesador llamado **System Control Coprocessor** (CP0) que contiene una **Unidad de gestión de memoria** (o MMU, del inglés) y un **Translation Lookaside Buffer** (TLB), el primero controla como la memoria es organizada y almacenada en el caché. El VR4300 puede acceder hasta 4 GB de direcciones de memoria de 32 bit, pero como ya hemos observado, no tenemos 4 GB de RAM en esta consola (incluso después de considerar la I/O mapeada en memoria). Por lo tanto, la MMU se hace cargo de el direccionamiento de memoria y provee un útil mapeado de memoria donde la memoria física se refleja varias veces. Como consecuencia, las ubicaciones de memoria se tratan como 'direcciones virtuales' (en vez de 'direcciones físicas). Además, el TLB permite a desarrolladores poder definir su propio mapeado de memoria en algunos reflejos sin (considerable) bajas al rendimiento.

Al principio, esto puede parecer innecesario, pero cada reflecto (llamado 'segmento') esta conectado a circuitos diferentes (por ejemplo, caché de L1, sin caché, dirección de TLB), así los desarrolladores pueden optimizar su uso seleccionando el segmento más apropiado para la tarea.

Algunos segmentos están hechos para diferenciar ubicaciones 'kernel' de las ubicaciones 'usuario' por razones de seguridad. La N64 siempre opera en modo 'kernel'. Asimismo, el segmento 'kernel no-TLB almacenado en caché' (llamado 'KSEG0') es el más común para juegos.

La MMU también puede operar en modo 64 bit, donde el espacio para direcciones virtuales cubre 1 TB de direcciones. Aunque por razones obvias, no hace falta explicar este modo en este artículo

---

## Gráficos

Lo que se ve en la pantalla esta producido por un inmenso chip diseñado por Silicon Graphics llamado **Reality Co-Processor** que corre a **62,5 MHz**. Este paquete contiene *un montón* de circuitería así que no te preocupes si te resulta difícil de seguir, el sub-sistema de gráficos tiene una arquitectura muy compleja.

Este diseño se basa en la filosofía de que la GPU no debe ser una 'simple' rasterizadora como la de [la competencia]({{< ref path="playstation#graphics" lang="en" >}}). Por el contrario, debe ser capaz de **acelerar los cálculos geometricos** (aliviando la carga de la CPU) y para ello, es necesario más circuitería.

### Arquitectura

Este chip está dividido en tres módulos principales, dos de los cuales se utilizan para el procesamiento de gráficos:

{{< tabs >}}
{{< tab active="true" name="Reality Signal Processor" >}}
{{< figure_img float="true" src="RSP.png" alt="Diagrama RSP" >}}
Arquitectura del RSP
{{< /figure_img >}}

{{% inner_markdown %}}
También conocido como **RSP**, es simplemente otro paquete con una CPU compuesto por:

- La **Scalar Unit**: Una CPU basada en la MIPS R400 que implementa un subconjunto de instrucciones del R400.
- La **Vector Unit**: Un co-procesador que realiza operaciones vectoriales con 32 registros de 128 bits. Cada registro está separado en ocho partes para operar ocho vectores de 16 bits a la vez (al igual que las instrucciones SIMD en las CPUs convencionales).
- El **System Control**: Otro co-procesador que cubre las funciones de DMA y controla su módulo vecino, el RDP (más sobre esto en breve).

Para operar este módulo, la CPU almacena en la RAM una serie de comandos llamados **Display list** junto con los datos que serán manipulados, luego el RSP lee dicha lista y aplica las operaciones requeridas sobre los datos. La funcionalidad disponible incluye transformaciones geométricas (como la proyección en perspectiva), clipping e iluminación.

Esto parece sencillo, pero ¿cómo realiza estas operaciones? Bueno, aquí está la parte interesante: A diferencia de sus competidores (PS1 y Sega Saturn), **el motor de la geometría no está fijado**. En su lugar, el RSP contiene un poco de memoria (4 KB para instrucciones y 4 KB para datos) para almacenar **microcode**, un pequeño programa, con no más de 1000 instrucciones, que **implementa la tubería de gráficos**. En otras palabras, dirige a la Scalar Unit como debe operar nuestros gráficos. La CPU envía microcode mientras el programa se encuentra en ejecución.

Nintendo proporcionó diferentes versiones de microcode para elegir y, similarmente a los [modos de background de SNES]({{< ref path="super-nintendo#graphics" lang="en" >}}), cada uno balancea los recursos a su manera.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Reality Display Processor" >}}
{{< figure_img float="true" src="RDP.png" alt="Diagrama RDP" >}}
Arquitectura del RDP
{{< /figure_img >}}

{{% inner_markdown %}}
Una vez que el RSP termine de procesar los datos de nuestros polígonos, empezará a enviar **comandos de rasterización** al siguiente módulo, el **RDP**, para dibujar el frame. Estos comandos se envían por un bus dedicado llamado **XBUS** o por la RAM principal.

El RDP es otro procesador (esta vez con funcionalidad fija) que incluye múltiples motores utilizados para rasterizar vectores, mapear las texturas en nuestros polígonos, mezclar colores y componer el nuevo frame.

Este puede procesar **triángulos** o **rectángulos** como primitivos, el último es útil para dibujar sprites. El motor de rasterización del RDP contiene los siguientes bloques:

- Un **Rasteriser**: Convierte primitivos (hechos de vértices) a píxeles.
- Una **Texture Unit**: Procesa texturas usando 4 KB de memoria dedicada (llamada 'TMEM') por lo que permite hasta ocho tiles para ser usadas para la textura. Puede realizar las siguientes operaciones sobre ellas:
  - **Bilinear filtering**: Mapea la textura 2D seleccionada sobre la figura 3D y la suaviza para evitar áreas pixeladas (causado por sobremuestreo).
    - Un filtro 'completo' requiere cuatro puntos para aplicar la interpolación, sin embargo, esta consola sólo utiliza tres (**interpolación triangular**) lo que produce algunas anomalías. Por lo tanto, ciertas texturas tendrán que ser 'adaptadas' de antemano.
  - **Mip-Mapping**: Selecciona automáticamente una versión reducida de la textura dependiendo de su **nivel de detalle**. Esto evita computar grandes texturas que se verían lejos de la cámara y con ello prevenir el 'aliasing' (causado por submuestreo).
    - Si se activa, la N64 mapea las texturas usando **trilinear filtering** en su lugar. Este nuevo algoritmo también interpolará entre mipmaps para suavizar los cambios repentinos en el nivel de detalle.
  - **Perspective correction**: Algoritmo escogido para mapear texturas en triángulos. A diferencia de [otros algoritmos]({{< ref path="playstation#graphics" lang="en" >}}), éste tiene en cuenta el valor de profundidad de cada primitiva, logrando mejores resultados.
- Un **Colour Combiner**: Mezcla e interpola múltiples capas de colores (por ejemplo, para aplicar luces).
- Un **Blender**: Mezcla píxeles sobre el actual frame-buffer para aplicar translucidez, anti-aliasing, niebla o dithering. También realiza z-buffering (más sobre esto más adelante).
- Una **Memory interface**: Utilizada por los bloques anteriores para leer y enviar el frame-buffer procesado a la RAM y/o llenar el TMEM.

La RDP proporciona cuatro modos de operación, cada uno combina estos bloques de forma diferente para optimizar operaciones específicas.

Dado que este módulo requiere actualizar constantemente el frame-buffer, la RAM se maneja de forma diferente: ¿Recuerdas el inusual 'byte' de 9 bits? El noveno bit se utiliza para los cálculos relacionados con el frame-buffer (z-buffering y el antialiasing) y sólo se puede acceder a él a través de la Memory interface.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

El frame que se obtiene debe ser enviado al **Video Encoder** para visualizarlo en pantalla (**DMA** y el **Video Interface** son esenciales para llevar a cabo esto).

En papel, las capacidades máximas son una profundidad de color de 24 bits (16,8 millones de colores) y una resolución de 720x576 (o 640x480 en la región NTSC). En la práctica no se utilizan todos, ya que hacer uso de las capacidades máximas puede ser muy costoso en términos de recursos, por lo que los programadores tenderán a utilizar cifras más bajas para proveer suficientes recursos a otros servicios.

### Demostración rápida

Pongamos todas las explicaciones anteriores en perspectiva. Para ello tomaré prestado el *Super Mario 64* de Nintendo para demostrar, en pocas palabras, cómo se forma un frame:

{{< tabs >}}
{{< tab active="true" name="Procesado de Vértices" >}}
{{< figure_img float="true" src="mario/wireframe.jpg" alt="Wireframe Mario" >}}
Vista primitiva de nuestra escena  
Para ahorrar polígonos, algunos caracteres se modelan con sprites (cuadriláteros)
{{< /figure_img >}}

{{% inner_markdown %}}
Inicialmente, nuestros modelos 3D se encuentran en la ROM del cartucho pero para mantener un ancho de banda constante, necesitamos copiarlos a la RAM primero. En algunos casos, los datos se pueden encontrar ya comprimidos en el cartucho, por lo que la CPU tendrá que descomprimirlo antes de utilizarlo.

En cuanto este completado, es hora de construir la escena usando nuestros modelos, la CPU podría hacerlo por sí misma pero esto tardaría toda una vida, así que la tarea se delega al RCP. La CPU se limitará a enviar órdenes al RCP, esto se hace llevando a cabo las siguientes tareas:

1. Componer la Display List que contiene las operaciones a realizar por el RSP y almacenarla en la RAM.
2. Apuntar al RSP donde se encuentra la Display List.
3. Enviar el microcode al RSP para poner en marcha la Scalar Unit.

Seguidamente, el RSP comenzará a realizar las tareas y el resultado será enviado al RDP en forma de comandos de rasterización.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Procesado de Píxeles" >}}
{{< figure_img float="true" src="mario/result.png" alt="Mario Renderizado" class="pixel" >}}
Frame renderizado (_Tachán!_)
{{< /figure_img >}}

{{% inner_markdown %}}
Hasta ahora hemos logrado procesar nuestros datos y aplicar algunos efectos sobre ellos, pero todavía tenemos que:

- Rasterizar vectores, aplicar texturas y otros efectos.
- Mostrar un frame-buffer en pantalla.

Como puedes adivinar, estas tareas serán realizadas por el RDP. Para que esto funcione, las texturas deben ser copiadas desde la RAM a el TMEM usando DMA.

El RDP módulo tiene una motor fijo pero permite seleccionar el modo de funcionamiento en base a la tarea que se llevará a cabo, ayudando a mejorar el framerate.

Una vez que el RDP termine de procesar los datos, escribirá el mapa de bits resultante en el frame-buffer que se encuentra dentro de la RAM. Una vez terminado, la CPU debe transferir el nuevo frame a la **Video Interface** (VI),preferiblemente usando el DMA, que a su vez la enviará al **Video Encoder** para su visualización.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Diseños

He aquí algunos ejemplos de personajes previamente diseñados en dos dimensionas para la [Super Nintendo]({{< ref path="super-nintendo" lang="en" >}}), pero que han sido rediseñados para la nueva era de tres dimensiones. Los modelos son interactivos así que os animo a que les echéis un vistazo.

{{< side_by_side >}}
  {{< threejs_canvas model="zelda_ocarina_link" class="toleft" >}}
The Legend of Zelda: Ocarina of Time (1998)
785 triangulos
  {{< /threejs_canvas >}}
  {{< threejs_canvas model="kirby_cristals" class="toright" >}}
Kirby 64: The Crystal Shards (2000)
516 triangulos
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

### Una solución moderna al problema de superficies visibles

Si has leído sobre las consolas anteriores, probablemente estás al tanto del interminable problema de [visibilidad de superficies]({{< ref path="sega-saturn#an-introduction-to-the-visibility-problem" lang="en" >}}) y tal vez pienses que la única solución es mediante la 'clasificación de polígonos'. Bueno, por primera vez en esta serie, el RDP presenta una solución basada en hardware llamada **Z-buffering**. En pocas palabras, el RDP asigna un nuevo buffer llamado **Z-Buffer** en la memoria. Este tiene las mismas dimensiones que un frame-buffer, pero en lugar de almacenar los valores RGB (rojo, verde y azul), cada entrada contiene la profundidad del píxel más cercano con respecto a la cámara (llamado 'z-value').

Una vez que el RDP rasteriza los vectores, el z-value del nuevo píxel se compara con el valor respectivo en el Z-buffer. Si el nuevo píxel contiene un z-value más pequeño, esto significa que el nuevo píxel se posiciona delante del anterior, por lo que se sobrescribe en el frame-buffer y z-buffer. De lo contrario, el píxel se descarta.

En general, el nuevo algoritmo es muy beneficioso: Los programadores ya no tienen que preocuparse por implementar métodos para clasificar polígonos [basados en software]({{< ref path="playstation#tab-4-2-visibility-approach" lang="en" >}}) que tienden a consumir bastantes recursos de la CPU. Sin embargo, el Z-buffer no previene procesar geometría innecesaria (eventualmente descartada o sobrescrita, que consumen recursos de todas maneras). Para ello, el motor del juego puede optar por incluir un algoritmo de **'occlusion culling'** para descartar la geometría que no será visible, lo antes posible.

### Secretos y limitaciones

Es evidente que SGI invirtió mucha tecnología en este sistema. Sin embargo, esta sigue siendo una consola para el hogar y como tal, debe mantener un bajo coste. Por ahí, ciertas decisiones se convirtieron en retos para los programadores:

{{< tabs >}}

{{% tab active="true" name="Atascos" %}}
Debido al gran número de componentes y operaciones en la tubería de gráficos, el RCP terminó siendo muy susceptible a **burbujas de segmentación**: Una situación indeseable caracterizada por subcomponentes que se mantienen inactivos durante períodos considerables de tiempo porque los datos requeridos se retrasan en llegar.

Esto siempre se manifestará en una degradación del rendimiento y depende del programador para evitarlos. Aunque para facilitar las cosas, algunas CPUs como la Scalar Unit implementan una característica llamada **Bypassing** que permite ejecutar instrucciones similares a un ritmo más rápido para evitar pasar por etapas de ejecución que pueden ser omitidas.

Por ejemplo, si tenemos que computar múltiples instrucciones `ADD` (suma) a la vez, no hay necesidad de escribir el resultado de la suma en el registro destinatario y luego leerlo de nuevo para ejecutar el siguiente `ADD`. En vez de eso, se puede usar el mismo registro intermediario para llevar al cabo todas las adiciones y al final, enviar el resultado al registro destinatario una vez que el último `ADD` se completa.
{{% /tab %}}

{{% tab name="Memoria de Textura" %}}
El RDP usa 4 KB de TMEM ('Texture Memory') como única unidad para procesar las texturas. Desafortunadamente, en la práctica 4 KB resultaron ser insuficientes para las texturas de alta resolución. Además, si se utiliza el mipmapping, la cantidad de memoria disponible se reduce a la mitad.

Como resultado, algunos juegos utilizan colores sólidos con sombreado Gouraud (como *Super Mario 64*) mientras que otros hacen uso de texturas precalculadas (por ejemplo, cuando hay que mezclar varias capas).
{{% /tab %}}
{{< /tabs >}}

### La salida de video universal

Nintendo siguió usando la salida 'universal' llamada [Multi Out]({{< ref path="super-nintendo#a-convenient-video-out" lang="en" >}}) como su predecesor, pero desafortunadamente, **ya no transmite más la señal RGB!**. Esto suena a otra medida para acaparar costes, ya que la misma señal no se había aprovechado en la consola anterior.

Las buenas noticias son que los tres canales pueden ser reconstruidos en las primeras revisiones de la consola mediante la soldadura de algunos cables y la instalación de un amplificador de señal (tiende a ser barato). Esto es debido a que el convertidor digital a analógico de video envía una señal RGB al codificador de video. Sin embargo, las revisiones posteriores de la placa base combinaron los dos chips, así que la única solución disponible es de sobrepasar el video DAC y el codificador con algún chip especializado que pueda exponer la señal RGB.

---

## Audio

Antes de entrar en detalles, definamos los dos puntos opuestos del subsistema de audio de la N64:
- Nuestro punto de partida es la ROM del cartucho, contiene datos que sólo la CPU puede interpretar.
- El punto final es el **Digital-to-Analog converter** o 'DAC', que sólo entiende datos que codifican la waveform (forma de onda de sonido).

Entonces, ¿cómo conectamos ambos extremos? Las consolas normalmente incluyen un chip de audio dedicado que hace el trabajo por nosotros. Desafortunadamente, la Nintendo 64 **no tiene ese chip dedicado**, por lo que esta tarea se distribuye a través de estos componentes:
- La **CPU principal**: Transfiere los datos de audio de la ROM del juego a la RAM, y luego crea **Audio Lists** para ser usadas por el RSP.
- El **RSP**: Con el uso de más microcode, este interpreta las Audio Lists previamente almacenadas en la RAM y realiza las operaciones necesarias sobre los datos de audio. Esto, por ejemplo, incluye:
  - Descomprimir **ADPCM samples** y aplicar efectos.
  - Secuenciar y mezclar **datos MIDI** utilizando **bancos de audio** almacenados también en la RAM.

Los datos resultantes son, como se espera, datos que contienen la waveform. Estos se envían al bloque de **Audio Interface** o 'AI' que los transferirá al Digital-to-Analog converter. La waveform resultante contiene dos canales (ya que nuestro sistema es estéreo) con una resolución de 16-bits cada uno.

{{< figure_img src="Audio.png" alt="Audio Diagram" class="centered-container" >}}
Resumen de cómo se tiende a programar el motor de audio
{{< /figure_img >}}

### Repertorio musical

Es hora de echar un vistazo a las bandas sonoras hechas para la N64. La verdad es que hay demasiadas (y buenas) para mencionarlas todas en este artículo, así que os dejo algunas que me llamaron la atención:

{{< side_by_side >}}
  {{< video src="observatory" class="toleft" >}}
The Legend of Zelda: Majora's Mask (2000)
La música de este juego está ligada a su atmósfera intimidante
  {{< /video >}}
  {{< video src="redial" class="toright" >}}
Bomberman Hero (1998)
Este juego tiene una agradable y única banda sonora al estilo 'House'
  {{< /video >}}
{{< /side_by_side >}}

### Secretos y limitaciones

Debido a este diseño, las limitaciones de este sistema dependerán de la implementación:
  - La frecuencia de los samples puede ser de hasta 44,1Hz, pero usar la frecuencia máxima se requiere demasiados ciclos de CPU.
  - No hay un límite estricto en el número de canales, todo depende de cuántos canales sea capaz de mezclar el RSP (a menudo alrededor de 16-24 canales si se procesa ADPCM o ~100 si PCM).
  - La memoria es otro asunto a tener en cuenta. Mientras que la competencia proveía medios más grandes (por ejemplo, CD-ROM) y memoria de audio dedicada, los cartuchos de Nintendo 64 contienen mucha menos capacidad (por no hablar de espacio reservado para música) y deben compartir la RAM principal con otros componentes. 

Por estas razones, los jugadores pueden llegar a notar que los ports de N64 contienen música de menor calidad o repetitivos. Un método para superar esta limitación consistía en implementar un secuenciador en software que pudiera 'construir' los samples mientras el juego se ejecutaba, esto requiere un banco de sonidos (similar a la música MIDI).

---

## Sistema operativo

Al igual que la PS1 y Saturn, los juegos de N64 hablan directamente con el hardware. Sin embargo, no hay rutinas de la BIOS disponibles para simplificar algunas operaciones. Como sustituto, **los juegos incorporan un pequeño sistema operativo** que proporciona una buena cantidad de abstracción para manejar eficientemente la CPU, la GPU y el I/O.

Este no es el *Sistema Operativo de escritorio* convencional que podemos imaginar al principio, es sólo un micro-kernel con el menor tamaño posible que proporciona la siguiente funcionalidad:

- Multi-Threading usando paso de mensajes (tened en cuenta que la CPU es de un solo núcleo).
- Scheduling (planificador) y Preemption (Multitarea apropiativa).
- Acceso a registro y I/O simplificados.

Considerándolo todo, estas funciones son críticas para organizar audio, video y tareas lógicas de juegos que deben funcionar al mismo tiempo.

El kernel se almacena automáticamente al utilizar librerías de Nintendo. Además, si los programadores deciden no incluir una de las librerías, la porción respectiva del kernel se quita para evitar desperdiciar el espacio del cartucho.

### I/O

Como ya sabéis, el I/O no está conectado directamente a la CPU, así que el tercer módulo del RCP (que no he mencionado hasta ahora) sirve como **interfaz I/O**. Básicamente, se comunica con la CPU, los controladores, el cartucho de juego y los DAC de Audio/Video.

---

### Accesorios

El mando de Nintendo 64 incluye un conector para enchufar accesorios. Unos ejemplos de accesorios comerciales incluyen:
- El **Controller Pak**: Otro medio (similar a la *Memory Card* de Sony) que se utiliza para almacenar partidas y llevarlas a otras consolas.
- El **Rumble Pak**: Contiene un pequeño motor para proporcionar vibración, útil para sumergir al jugador en la 'experiencia del juego'.

Todos los accesorios conectados al mando son manejados por el **Peripheral Interface** (PIF), un bloque escondido que también controla la seguridad. El RCP se comunica al PIF utilizando un 'muy lento' (*citado del manual de programación) **Bus de serie**.

---

## Juegos

Nintendo se aferró al cartucho como medio para el almacenamiento y como consecuencia, los juegos disfrutaron de mayores anchos de banda (un promedio de 5 MB/s según Nintendo) mientras que costaban más de producir. El cartucho más grande que hubo en el mercado tiene 64 MB.

Dentro de los cartuchos, los fabricantes pueden incluir memoria extra (en forma de *EEPROM*, *flash* o *SRAM* con una batería) para guardar partidas, sin embargo no es un requerimiento estricto ya que algunos accesorios también podían ser usados para almacenar los progresos.

Los cartuchos se comunican al RCP utilizando un bus dedicado de 16 bits llamado **Bus Paralelo** (PBUS) o 'Interfaz Paralela' (PI).

### Kit de Desarrollo

En términos generales, desarrollar para la N64 se hacía principalmente en C, aunque también se requería ensamblador para lograr un mejor rendimiento. Si bien este sistema contenía un conjunto de instrucciones de 64 bits, rara vez se utilizaban las de 64 bits, ya que en la práctica, las instrucciones de 32 bits resultaban ser más rápidas de ejecutar y requerían la mitad del almacenamiento.

Por otra parte, las librerías en SDK oficial contenían varias capas de abstracciones para comandar el RCP. Por ejemplo, la **Graphics Binary Interface** o ‘GBI’ era una estructura diseñadas para ensamblar las Display Lists más fácilmente, lo mismo se aplicaba a las funciones de audio (su estructura se llamó **Audio Binary Interface** o 'ABI').

En cuanto al uso de microcode, Nintendo proporcionó varias opciones de microcode escrito para elegir. Sin embargo, en el caso que los desarrolladores quieran modificarlo, se verían con una ardua tarea: Las instrucciones de la Scalar Unit no estaban inicialmente documentadas, aunque luego, Nintendo cambió de posición y SGI finalmente publicó cierta documentación para habilitar el desarrollo de nuevo microcode.

El hardware utilizado para el desarrollo de videojuegos incluía estaciones de trabajo suministradas por SGI, como la máquina **Indy** que venía con una placa extra llamada **U64** que contiene el hardware y I/O de la consola. También se suministraron herramientas para ordenadores con Windows instalado.

Otras herramientas proporcionadas por terceros consistían en cartuchos con un largo cable que se conectaba a la estación de trabajo. Este cartucho se insertaba en una Nintendo 64 pero incluía un circuito interno para redirigir los comandos de lectura de la consola a la RAM de la estación de trabajo. Una vez acabado, la consola se encendía y comenzaba a leer desde la RAM (del ordenador), esto permitía llevar a cabo la implementación y depuración del juego.

### El medio alternativo

Adicionalmente, El PBUS se ramifica a otro conector en la parte inferior de la placa base de la N64. Esto estaba destinado a ser utilizado por la **Nintendo 64 Disk Drive** (64DD) aún inédita, una especie de 'piso adicional' que contiene un lector de disco magnético patentado. Sus discos contienen hasta 64 MB de capacidad. Aunque solo se lanzó en Japón, la unidad de disco abrió la puerta a un medio alternativo (y más barato) para distribuir juegos.

{{< float_group >}}
{{< tabs float="true" nested="true" >}}
  {{< tab_figure_img name="Módulo" src="64dd/module.png" alt="Módulo N64 DD" active="true" >}}
La Nintendo 64 Disk Drive.  
Lanzada el 01/12/1999 en Japón.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Conectado" src="64dd/attached.png" alt="N64 DD conectada" >}}
El 64DD conectada a la consola.
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
El medio magnético es más lento que los cartuchos, con velocidades de transferencia de hasta 1 MB/seg, aunque más rápidos que los lectores de 4X CD-ROM. Los discos so de doble-cara y operan a una 'Velocidad Angular Constante' (como la posterior [miniDVD]({{< ref path="gamecube#medium" lang="en" >}})). El área lejible más pequeña se llama 'block' y es la mitad de un círculo concéntrico.

El lector no incluye un búfer de memoria, así que los bits leídos son almacenados en RDRAM para ejecución. Nintendo incluyó la unidad de expansión de RAM con la 64DD, por lo que compensa la necesidad de más RAM (además de estandarizar la RAM extendida con juegos 64DD).
{{% /inner_markdown %}}

{{< /float_group >}}

Además, partes del disco se pueden reescribir para permitir el almacenamiento de partidas, los discos utilizados determinan la cantidad de área de escritura (Nintendo proporcionó siete tipos). Del lado de software, los datos del juego son estructurados con un sistema de archivos llamado 'Multi File System' (MFS) que Nintendo proveyó en su SDK. Juegos pueden acceder los datos del disco utilizando el sistema de archivos o de forma 'bloque a bloque', la última se basa en otra librería llamada 'Leo' para funciones de bajo nivel.

La unidad de disco también alberga una ROM interna (denominada "DDROM") que almacena el código que ejecuta la N64 para arrancar el disco y mostrar la animación de bienvenida, esto se denomina "Cargador de programa inicial" (IPL). La ROM también almacena tipos de letra (Latín y Kanji) y algunos sonidos. Esta ROM solo es disponible en tiendas (las unidades de desarrollo dependían de programas externos cargados a través del kit de desarrollo). 

---

## Anti-piratería / Region Lock

El sistema antipiratería es una continuación del [CIC de SNES]({{< ref path="super-nintendo#anti-piracy--region-lock" lang="en" >}}). Como sabéis, la detección de piratería y el 'region lock' son posibles gracias al chip CIC (que debe estar presente en cada cartucho de juego *autorizado*), la Nintendo 64 mejoró este sistema requiriendo que diferentes juegos tuvieran una variante específica del CIC para asegurarse de que el cartucho no era una falsificación o contenía un clon del CIC. El PIF realizaba comprobaciones por medio de checksums al principio y durante la ejecución juego para supervisar el CIC instalado en el cartucho.

Si por alguna razón el PIF considera que el cartucho insertado no es válido, este bloquea la ejecución del juego.

El region-lock se realiza alterando ligeramente la forma externa del cartucho entre las diferentes regiones para que el usuario no pueda insertar físicamente el juego en un N64 de una región diferente.

En general, no hubo demasiada preocupación por la piratería gracias al uso del cartucho como medio, aunque los precios de los juegos eran tres veces más altos que los dependientes del CD.

### Puertos sin utilizar

Por muy tonto que parezca, Nintendo dejó una 'puerta' abierta: El puerto del Disk Drive.

{{< float_group >}}
{{< tabs float="true" nested="true" >}}
  {{< tab_figure_img name="Conectado" active="true" src="v64/attached.png" alt="Doctor V64 attached" >}}
El Doctor V64 conectado a la consola
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Atrás" src="v64/back.png" alt="Doctor V64" >}}
El V64 visto desde atrás, mostrando conectores especiales para A/V
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
Algunas compañías, mediante ingeniería inversa, estudiaron la interfaz para desarrollar hardware propietario; Los nuevos productos lanzados llegaron a preocupar a Nintendo debido a sus capacidades para ejecutar juegos piratas.

Supongo que el que vale la pena mencionar es el **Doctor v64**, este dispositivo tiene la misma forma que el Disk Drive pero incluye una unidad de CD-ROM que se utiliza para grabar el contenido del cartucho a un CD, aunque lo opuesto (leer la ROM desde un CD) también es posible.
{{% /inner_markdown %}}

{{< /float_group >}}

### Emulación

De niño solía jugar a algunos juegos de N64 en un PC con un Pentium II usando un emulador, no era *super lento* pero ahora me pregunto *cómo leches* era capaz de emular felizmente una máquina tan compleja de 64 bits dado que, entre otras cosas, mi PC apenas tenía suficiente RAM para mantener viva la gráfica integrada.

La verdad es que, mientras que reproducir la arquitectura de esta consola puede ser un tarea bastante difícil, cosas como el microcode dan una pista de lo que la consola está intentando hacer, y cómo los emuladores *no tienen que ser precisos*, pueden aplicar suficientes optimizaciones para proporcionar más rendimiento a cambio de una emulación idéntica.  

Otro ejemplo son las instrucciones de 64 bits, ya que los juegos apenas las utilizan, la velocidad de emulación raras veces declina cuando se lleva a cabo en un ordenador de 32 bits.

---

## Eso es todo amigos

Debo decir que este artículo puede ser el más largo que he escrito, pero espero que al menos te haya sido de interés.

Probablemente me tome los siguientes días para ordenar algunas cosas en la web en lugar de empezar a escribir el siguiente artículo.

De todas formas, si te gustan mis artículos y me quieres ayudar, por favor echa un vistazo [aquí](/support). Si tienes algún comentario o sugerencia, no dudes en enviarme un email {{< email "aquí" >}}.

¡Hasta la próxima!  
Rodrigo