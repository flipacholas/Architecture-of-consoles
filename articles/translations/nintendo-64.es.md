---
name: Nintendo 64
releaseDate: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: Potente y complicada!
cover: nintendo64
javascript: ['threejs', 'plyr']
top_tabs:
  Model:
    caption: "La Nintendo 64
    <br>Lanzada el 23/06/1996 en Japón, el 29/09/1997 en América y el 01/03/1997 en Europa"
    file: "international"
  Motherboard:
    caption: "Mostrando la revision 'NUS-CPU-03'.
    <br>Versiones posteriores redujeron el número de chips requeridos para la codificar el audio y video
    <br>El conector de 'Disk Drive' se encuentra en el otra cara"
  Diagram: {}

# SEO
title: Arquitectura de la Nintendo 64
---

## Una breve introducción

El objetivo de Nintendo era proporcionar a los jugadores los mejores gráficos posibles, para llevar a cabo esta tarea, se asociaron con uno de los mayores expertos en gráficos de la industria para producir el chip *definitivo*.

El resultado fue una bonita consola para toda la familia... y un manual de instrucciones de 500 páginas para los desarrolladores.

No te preocupes, te prometo que este artículo no va a ser tan largo como el manual... ¡Disfrútalo!

---

## CPU

El procesador principal es un **NEC VR4300** que corre a **93.75 MHz**; una versión compatible binaria del **MIPS R4300i** de Silicon Graphics que incluye:
- Set de instrucciones **MIPS III**: Sucesor del MIPS II con nuevas instrucciones de 64 bits. Las palabras (words) de 64-bits se llaman *doublewords*.
- Un bus interno de **64-bits** conectado a un **bus de datos externo de 32-bits**.
- **Segmentación de instrucciones de 5 etapas**: Hasta cinco instrucciones pueden ser ejecutadas simultáneamente (para una explicación detallada, puedes visitar [este otro articulo]({{< ref path="sega-saturn#cpu" lang="en" >}}).
- **24 KB de L1 cache**: Dividido en 16 KB para instrucciones y 8 KB para datos.

También se incluye en este paquete una **FPU de 64 bits**, la CPU la identifica como un co-procesador (*COP1*) aunque la unidad está instalada junto a la unidad aritmética y solo se accede a ella a través de esa unidad, con lo que no hay co-procesamiento de por si.

#### Acceso simplificado a la memoria

La manera en la que la RAM está ensamblada sigue la **arquitectura de memoria unificada** (o 'UMA', del inglés 'Unified-memory Architecture') donde toda la RAM disponible se centraliza en un solo lugar y cualquier otro componente que requiera RAM accederá a esta ubicación compartida. El componente que arbitra su acceso es, en este caso, la GPU.

La razón por la cual se eligió este diseño es por el ahorro considerable en costes de producción, mientras que por otro lado, incrementa la contención de acceso si no se gestiona adecuadamente.

#### ¿Sin controlador de DMA?

Debido a la arquitectura de memoria unificada, la CPU ya no tiene acceso directo a la RAM, por lo que la GPU también proporciona la funcionalidad DMA ('Direct Memory Access').

#### RAM disponible

Aparte de la UMA, la estructura de la RAM es un poco complicada, así que trataré de explicarlo en términos simples, aquí va...

El sistema contiene físicamente **4,5 MB de RAM**, sin embargo, está conectada a un bus de datos de **9 bits** donde el 9º bit está reservado para la GPU (más detalles en adelante). Como consecuencia, cada componente excepto la GPU sólo encontrará **hasta 4 MB**.

El tipo de memoria RAM instalada en la placa se llama **Rambus DRAM** o 'RDRAM' para abreviar, este fue otro diseño que compitió con SDRAM para convertirse en el siguiente estándar. RDRAM está conectada en **serie** (donde las transferencias se hacen de un bit a la vez) mientras que SDRAM utiliza una **conexión paralela** (transfiere múltiples bits a la vez).

La latencia de la RDRAM es directamente proporcional al número de bancos instalados, por lo que en este caso, con la cantidad de RAM que tiene este sistema, la latencia resultante **es importante**.

Por el contrario, la cantidad de RAM disponible en esta consola **se puede ampliar** instalando el accesorio *Expansion Pak*: Una pequeña caja de aspecto elegante que incluye 4,5 MB. Curiosamente, el bus de RAM debe ser terminado, por lo que la consola siempre se vende con un terminador (llamado *Jumper Pak*) instalado en el lugar del Expansion Pak. Ahora, podría preguntarse, ¿qué pasaría si encendemos la consola sin ningún *Pak* instalado? **Literalmente nada**, ¡te llevas una pantalla en blanco!

---

## Gráficos

El núcleo de los gráficos reside en un inmenso chip diseñado por Silicon Graphics llamado **Reality Co-Processor** que corre a **62,5 MHz**. Este paquete contiene *un montón* de circuitos así que no te preocupes si te resulta difícil de seguir, el sub-sistema de gráficos tiene una arquitectura muy compleja.

Este diseño se basa en la filosofía de que la GPU no debe ser una 'simple' rasterizadora como la de [la competencia]({{< ref path="playstation#graphics" lang="en" >}}). Por el contrario, debe ser capaz de **acelerar los cálculos geometricos** (aliviando la carga de la CPU) y para ello, es necesario más circuitería.

Dicho esto, este chip está dividido en tres módulos principales, dos de los cuales se utilizan para el procesamiento de gráficos:

{{< tabs >}}
  {{< tab active="true" name="Reality Signal Processor" >}}

{{< float_block >}}
  {{< linked_img src="RSP.png" alt="Diagrama RSP" >}}
  <figcaption class="caption">Arquitectura del RSP</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}

También conocido como **RSP**, es simplemente otro paquete con una CPU compuesto por:

- La **Scalar Unit**: Una CPU basada en la MIPS R400 que implementa un subconjunto de instrucciones del R400.
- La **Vector Unit**: Un co-procesador que realiza operaciones vectoriales con 32 registros de 128 bits. Cada registro está separado en ocho partes para operar ocho vectores de 16 bits a la vez (al igual que las instrucciones SIMD en las CPUs convencionales).
- El **System Control**: Otro co-procesador que cubre las funciones de DMA y controla su módulo vecino, el RDP (más sobre esto en breve).

Para operar este módulo, la CPU almacena en la RAM una serie de comandos llamados **Display list** junto con los datos que serán manipulados, luego el RSP lee dicha lista y aplica las operaciones requeridas sobre los datos. La funcionalidad disponible incluye transformaciones geométricas (como la proyección en perspectiva), clipping e iluminación.

Esto parece sencillo, pero ¿cómo realiza estas operaciones? Bueno, aquí está la parte interesante: A diferencia de sus competidores (PS1 y Sega Saturn), **el motor de la geometría no está fijado**. En su lugar, el RSP contiene un poco de memoria (4 KB para instrucciones y 4 KB para datos) para almacenar **microcode**, un pequeño programa, con no más de 1000 instrucciones, que **implementa la tubería de gráficos**. En otras palabras, dirige a la Scalar Unit como debe operar nuestros gráficos.
La CPU envía microcode mientras el programa se encuentra en ejecución.

Nintendo proporcionó diferentes versiones de microcode para elegir y, similarmente a los [modos de background de SNES]({{< ref path="super-nintendo#graphics" lang="en" >}}), cada uno balancea los recursos a su manera.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Reality Display Processor" >}}

{{< float_block >}}
  {{< linked_img src="RDP.png" alt="Diagrama RDP" >}}
  <figcaption class="caption">Arquitectura del RDP</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}

Una vez que el RSP termine de procesar los datos de nuestros polígonos, empezará a enviar **comandos de rasterización** al siguiente módulo, el **RDP**, para dibujar el frame. Estos comandos se envían por un bus dedicado llamado **XBUS** o por la RAM principal.

El RDP es otro procesador (esta vez con funcionalidad fija) que incluye múltiples motores utilizados para mapear las texturas en nuestros polígonos, mezclar colores y componer el nuevo frame.

Este puede procesar ya sean **triángulos** o **rectángulos**, el último es útil para dibujar sprites. El motor de rasterización del RDP contiene los siguientes bloques:

- Un **Rasteriser**: Asigna el mapa de bits inicial que servirá como frame-buffer.
- Una **Texture Unit**: Procesa texturas usando 4 KB de memoria dedicada (llamada 'TMEM') por lo que permite hasta ocho tiles para ser usadas para la textura. Puede realizar las siguientes operaciones sobre ellas:
  - **Bilinear filtering**: Mapea la textura 2D seleccionada sobre la figura 3D y la suaviza para evitar áreas pixeladas.
    - Un filtro 'completo' requiere cuatro puntos para aplicar la interpolación, sin embargo, esta consola sólo utiliza tres (**interpolación triangular**) lo que produce algunas anomalías. Por lo tanto, ciertas texturas tendrán que ser 'adaptadas' de antemano.
  - **Mip-Mapping**: Selecciona automáticamente una versión reducida de la textura dependiendo de su **nivel de detalle**. Esto evita computar grandes texturas que se verían lejos de la cámara y con ello prevenir el 'aliasing'.
    - Si se activa, la N64 mapea las texturas usando **trilinear filtering** en su lugar. Este nuevo algoritmo también interpolará entre mipmaps para suavizar los cambios repentinos en el nivel de detalle.
  - **Perspective correction**: Algoritmo escogido para mapear texturas en triángulos. A diferencia de [otros algoritmos]({{< ref path="playstation#graphics" lang="en" >}}), éste tiene en cuenta el valor de profundidad de cada primitiva, logrando mejores resultados.
- Un **Colour Combiner**: Mezcla e interpola múltiples capas de colores (por ejemplo, para aplicar luces).
- Un **Blender**: Mezcla píxeles sobre el actual frame-buffer para aplicar translucidez, anti-aliasing, niebla o dithering. También realiza z-buffering (más sobre esto más adelante).
- Una **Memory interface**: Utilizada por los bloques anteriores para leer y enviar el frame-buffer procesado a la RAM y/o llenar el TMEM.

La RDP proporciona cuatro modos de operación, cada uno combina estos bloques de forma diferente para optimizar operaciones específicas.

Dado que este módulo requiere actualizar constantemente el frame-buffer, debe manejar la RAM de forma más efectiva: ¿Recuerdas el inusual 'byte' de 9 bits? El noveno bit se utiliza para los cálculos relacionados con el frame-buffer (como el z-buffering y el antialiasing) y sólo se puede acceder a él a través de la Memory interface.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

El frame que se obtiene debe ser enviado al **Video Encoder** para poderlo visualizar en pantalla (**DMA** y el **Video Interface** son esenciales para llevar a cabo esto).

En papel, las capacidades máximas son una profundidad de color de 24 bits (16,8 millones de colores) y una resolución de 720x576 (o 640x480 en la región NTSC). Está mencionado como 'teórico' ya que hacer uso de las capacidades máximas puede ser muy costoso en términos de recursos, por lo que los programadores tenderán a utilizar cifras más bajas para proveer suficientes recursos a otros servicios.

#### Demostración rápida

Pongamos todas las explicaciones anteriores en perspectiva, para ello tomaré prestado el *Super Mario 64* de Nintendo para demostrar, en pocas palabras, cómo se forma un frame:

{{< tabs >}}
  {{< tab active="true" name="Modelos y sombreado" >}}

{{< float_block >}}
  {{< linked_img src="mario/wireframe.jpg" alt="Wireframe Mario" >}}
  <figcaption class="caption">Vista primitiva de nuestra escena
  <br>Para ahorrar polígonos, algunos caracteres se modelan con sprites (cuadriláteros)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}

Para empezar, nuestros modelos 3D se encuentran en la ROM del cartucho, pero para mantener un ancho de banda constante, necesitamos copiarlos a la RAM primero.

A continuación, es hora de construir la escena usando nuestros modelos, la CPU podría hacerlo por sí misma pero esto puede tardar toda una vida, así que la tarea se delega al RCP. La CPU se limitará a enviar órdenes al RCP, esto se hace llevando a cabo las siguientes tareas:

1. Componer la Display List que contiene las operaciones a realizar por el RSP y almacenarla en la RAM.
2. Apuntar al RSP donde se encuentran la Display List.
3. Enviar el microcode al RSP para poner en marcha la Scalar Unit.

Seguidamente, el RSP comenzará a realizar las tareas y el resultado será enviado al RDP en forma de comandos de rasterización.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Texturas y rasterización" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Sobreescalado" active="true" >}}
      {{< linked_img src="mario/result.png" alt="Mario Renderizado" >}}
      <figcaption class="caption">Frame renderizado (<i>Tachán!</i>)
      <br>Sobreescalado (640x480) por motivos de demostración</figcaption>
    {{< /tab >}}
    {{< tab name="Original" >}}
      {{< linked_img src="mario/result_original.png" alt="Mario Renderizado" class="pixel" >}}
      <figcaption class="caption">Frame renderizado (<i>Tachán!</i>)
      <br>Resolución original (320x240) como se ve en pantalla</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Hasta ahora hemos logrado procesar nuestros datos y aplicar algunos efectos sobre ellos, pero todavía tenemos que:

- Aplicar texturas y otros efectos.
- Dibujar un frame-buffer, para poder mostrarlo en pantalla.

Como puedes adivinar, estas tareas serán realizadas por el RDP. La CPU le enviará los datos (como las texturas) colocándolos en la RAM, este módulo tiene una motor fijo pero permite seleccionar el modo de funcionamiento en base a la tarea que se llevará a cabo, ayudando a mejorar el framerate.

Una vez que el RDP termine de procesar los datos, escribirá el mapa de bits resultante en el frame-buffer que se encuentra dentro de la RAM. Una vez terminado, la CPU debe transferir el nuevo frame a la **Video Interface** (preferiblemente usando el DMA) que a su vez la enviará al **Video Encoder** para su visualización.

{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Diseños

He aquí algunos ejemplos de personajes previamente diseñados en dos dimensionas para la [Super Nintendo]({{< ref path="super-nintendo" lang="en" >}}), pero que han sido rediseñados para la nueva era de tres dimensiones. Los modelos son interactivos así que os animo a que les echéis un vistazo.

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="zelda_ocarina_link" >}}
    <figcaption class="caption">The Legend of Zelda: Ocarina of Time (1998)
    <br>785 triangulos</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="kirby_cristals" >}}
    <figcaption class="caption">Kirby 64: The Crystal Shards (2000)
    <br>516 triangulos</figcaption>
  </div>
{{< /side_by_side >}}

#### Una solución moderna al problema de superficies visibles

Si has leído sobre las consolas anteriores, probablemente estás al tanto del interminable problema de [visibilidad de superficies]({{< ref path="sega-saturn#an-introduction-to-the-visibility-problem" lang="en" >}}) y tal vez pienses que la única solución a este es mediante la 'clasificación de polígonos'. Bueno, por primera vez en esta serie, el RDP presenta una solución basada en hardware llamada **Z-buffering**. En pocas palabras, el RDP asigna un nuevo buffer llamado **Z-Buffer** en la memoria. Este tiene las mismas dimensiones que un frame-buffer, pero en lugar de almacenar los valores RGB (rojo, verde y azul), cada entrada contiene la profundidad del píxel más cercano con respecto a la cámara (llamado 'z-value').

Una vez que el RDP rasteriza los vectores, el z-value del nuevo píxel se compara con el valor respectivo en el Z-buffer. Si el nuevo píxel contiene un z-value más pequeño, esto significa que el nuevo píxel se posiciona delante del anterior, por lo que se sobrescribe en el frame-buffer y z-buffer. De lo contrario, el píxel se descarta.

En general, el nuevo algoritmo es my beneficioso: Los programadores ya no tienen que preocuparse por implementar métodos para clasificar polígonos [basados en software]({{< ref path="playstation#tab-2-2-visibility-approach" lang="en" >}}) que tienden a consumir bastantes recursos de la CPU. Sin embargo, el Z-buffer no previene procesar geometría innecesaria (eventualmente descartada o sobrescrita, que consumen recursos de todas maneras). Para ello, el motor del juego puede optar por incluir un algoritmo de **'occlusion culling'** para descartar la geometría que no será visible, lo antes posible.

#### Secretos y limitaciones

Es evidente que SGI invirtió mucha tecnología en este sistema. Sin embargo, esta sigue siendo una consola para el hogar y como tal, debe mantener un bajo coste. Por ahí, ciertas decisiones se convirtieron en retos para los programadores:

{{< tabs >}}

{{% tab active="true" name="Atascos" %}}

Debido al gran número de componentes y operaciones en la tubería de gráficos, el RCP terminó siendo muy susceptible a **burbujas de segmentación**: Una situación indeseable caracterizada por subcomponentes que se mantienen inactivos durante períodos considerables de tiempo porque los datos requeridos se retrasan en llegar.

Esto siempre se manifestará en una degradación del rendimiento y depende del programador para evitarlos. Aunque para facilitar las cosas, algunas CPUs como la Scalar Unit implementan una característica llamada **Bypassing** que permite ejecutar instrucciones similares a un ritmo más rápido para evitar pasar por etapas de ejecución que pueden ser omitidas.
Por ejemplo, si tenemos que computar múltiples instrucciones `ADD` (suma) a la vez, no hay necesidad de escribir el resultado de la suma en el registro destinatario y luego leerlo de nuevo para ejecutar el siguiente `ADD`. En vez de eso, se puede usar el mismo registro intermediario para llevar al cabo todas las adiciones y al final, enviar el resultado al registro destinatario una vez que el último `ADD` se completa.

{{% /tab %}}

{{% tab name="Memoria de Textura" %}}

El RDP usa 4 KB de TMEM ('Texture Memory') para procesar las texturas. Desafortunadamente, en la práctica 4 KB resultaron ser insuficientes para las texturas de alta resolución. Además, si se utiliza el mipmapping, la cantidad de memoria disponible se reduce a la mitad.

Como resultado, algunos juegos utilizan colores sólidos con sombreado Gouraud (como *Super Mario 64*) mientras que otros hacen uso de texturas precalculadas (por ejemplo, cuando hay que mezclar varias capas).

{{% /tab %}}
{{< /tabs >}}

#### La salida de video universal

Nintendo siguió usando la salida 'universal' llamada [Multi Out]({{< ref path="super-nintendo#a-convenient-video-out" lang="en" >}}) como su predecesor, desafortunadamente **ya no transmite más la señal RGB**. Cosa que suena a otra medida para acaparar costes, dado que la misma señal no se había aprovechado en la consola anterior.

Las buenas noticias son que los tres canales pueden ser reconstruidos en las primeras revisiones de la consola mediante la soldadura de algunos cables y la instalación de un amplificador de señal (tiende a ser barato). Esto es debido a que el convertidor digital a analógico de video envía una señal RGB al codificador de video. Sin embargo, posteriores unidades combinaron los dos chips, así que la única solución disponible es de sobrepasar el video DAC y el codificador con algún chip especializado que pueda exponer la señal RGB.

---

## Audio

Antes de entrar en detalles, definamos los dos puntos opuestos del subsistema de audio:
- Nuestro punto de partida es la ROM del cartucho, contiene datos que sólo la CPU puede interpretar.
- El punto final es el **Digital-to-Analog converter** o 'DAC', que sólo entiende datos que codifican la waveform (forma de onda de sonido).

Entonces, ¿cómo conectamos ambos extremos? Las consolas normalmente incluyen un chip de audio dedicado que hace el trabajo por nosotros. Desafortunadamente, la Nintendo 64 **no tiene ese chip dedicado**, por lo que esta tarea se distribuye a través de estos componentes:
- La **CPU principal**: Transfiere los datos de audio de la ROM del juego a la RAM, y luego crea **Audio Lists** para ser usadas por el RSP.
- El **RSP**: Con el uso de más microcode, este interpreta las Audio Lists previamente almacenadas en la RAM y realiza las operaciones necesarias sobre los datos de audio. Esto, por ejemplo, incluye:
  - Descomprimir **ADPCM samples** y aplicar efectos.
  - Secuenciar y mezclar **datos MIDI** utilizando **bancos de audio** almacenados también en la RAM.

Los datos resultantes son, como se espera, datos que contienen la waveform. Estos se envían al bloque de **Audio Interface** o 'AI' que los transferirá al Digital-to-Analog converter. La waveform resultante contiene dos canales (ya que nuestro sistema es estéreo) con una resolución de 16-bits cada uno.

{{< centered_container >}}
  {{< linked_img src="Audio.png" alt="Audio Diagram" >}}
  <figcaption class="caption">Resumen de cómo se tiende a programar el motor de audio</figcaption>
{{< /centered_container >}}

#### Repertorio musical

Es hora de echar un vistazo a las bandas sonoras hechas para la N64. La verdad es que hay demasiadas (y buenas) para mencionarlas todas en este artículo, así que os dejo algunas que me llamaron la atención:

{{< side_by_side >}}
  <div class="toleft">
    {{< video src="observatory" >}}
    <figcaption class="caption">The Legend of Zelda: Majora's Mask (2000)
    <br>La música de este juego está ligada a su atmósfera intimidante</figcaption>
  </div>

  <div class="toright">
    {{< video src="redial" >}}
    <figcaption class="caption">Bomberman Hero (1998)
    <br>Este juego tiene una agradable y única banda sonora al estilo 'House'</figcaption>
  </div>
{{< /side_by_side >}}

#### Secretos y limitaciones

Debido a este diseño, las limitaciones de este sistema dependerán de la implementación:
  - La frecuencia de los samples puede ser de hasta 44,1Hz, pero usar la frecuencia máxima se requiere demasiados ciclos de CPU.
  - No hay un límite estricto en el número de canales, todo depende de cuánto sea capaz de mezclar el RSP (a menudo alrededor de 16-24 canales si se procesa ADPCM o ~100 si PCM).
  - La memoria es otro asunto a tener en cuenta. Mientras que la competencia proveía medios más grandes (por ejemplo, CD-ROM) y memoria de audio dedicada, los cartuchos de Nintendo 64 contienen mucha menos capacidad (por no hablar de espacio reservado para música) y deben compartir la RAM principal con otros componentes. Por estas razones, los jugadores pueden llegar a notar que los ports de N64 contienen música de menor calidad o repetitivos.
    - Un método para superar esta limitación consistía en implementar un secuenciador en software que pudiera 'construir' los samples mientras el juego se ejecutaba, esto requiere un banco de sonidos (similar a la música MIDI).

---

## Sistema operativo

Al igual que la PS1 y Saturn, los juegos de N64 hablan directamente con el hardware. Sin embargo, no hay rutinas de la BIOS disponibles para simplificar algunas operaciones. Como sustituto, **los juegos incorporan un pequeño sistema operativo** que proporciona una buena cantidad de abstracción para manejar eficientemente la CPU, la GPU y el I/O.

Este no es el *Sistema Operativo de escritorio* convencional que podemos imaginar al principio, es sólo un micro-kernel con el menor tamaño posible que proporciona la siguiente funcionalidad:

- Multi-Threading (tened en cuenta que la CPU es de un solo núcleo).
- Scheduling (planificador) y Preemption (Multitarea apropiativa).
- Acceso a registro y I/O simplificados.

El kernel se almacena automáticamente al utilizar librerías de Nintendo. Además, si los programadores deciden no incluir una de las librerías, la porción respectiva del kernel se quita para evitar desperdiciar el espacio del cartucho.

#### Entrada/Salida

Como ya sabéis, el I/O no está conectado directamente a la CPU, así que el tercer módulo del RCP (que no he mencionado hasta ahora) sirve como **interfaz I/O**. Básicamente, se comunica con la CPU, los controladores, el cartucho de juego y los DAC de Audio/Video.

---

## Juegos

Nintendo se aferró al cartucho como medio para el almacenamiento y como consecuencia, los juegos disfrutaron de mayores anchos de banda (entre 5-50 MB/s dependiendo de la velocidad de la ROM) mientras que costaban más de producir. El cartucho más grande que hubo en el mercado tiene 64 MB.

Dentro de los cartuchos, los fabricantes pueden incluir memoria extra (en forma de *EEPROM*, *flash* o *SRAM* con una batería) para guardar partidas, sin embargo no es un requerimiento estricto ya que algunos accesorios también podían ser usados para almacenar los progresos.

#### Accesorios

El mando de Nintendo 64 incluye un conector para enchufar accesorios, algunos de ellos son:
- El **Controller Pak**: Otro medio (similar a la *Memory Card* de Sony) que se utiliza para almacenar partidas y llevarlas a otras consolas.
- El **Rumble Pak**: Contiene un pequeño motor para proporcionar vibración, útil para sumergir al jugador en la 'experiencia del juego'.

Todos los accesorios conectados al controlador son manejados por el **Peripheral Interface**.

Aparte de esto, esta consola incluía un conector especial en la parte inferior de su motherboard que estaba destinado a ser utilizado por el **Disc drive** que jamás fue lanzado. Este era una especie de 'piso extra' que contenía un lector de discos propietario. La unidad sólo fue lanzada en Japón y eventualmente cancelada en el resto del mundo.

#### Kit de Desarrollo

En términos generales, desarrollar para la N64 se hacía principalmente en C, aunque también se requería ensamblador para lograr un mejor rendimiento. Si bien este sistema contenía un conjunto de instrucciones de 64 bits, rara vez se utilizaban las de 64 bits, ya que en la práctica, las instrucciones de 32 bits resultaban ser más rápidas de ejecutar y requerían la mitad del almacenamiento.

Por otra parte, las librerías contenían varias capas de abstracciones para comandar el RCP. Por ejemplo, la **Graphics Binary Interface** o ‘GBI’ era una estructura diseñadas para ensamblar las Display Lists más fácilmente, lo mismo se aplicaba a las funciones de audio (su estructura se llamó **Audio Binary Interface** o 'ABI').

En cuanto al uso de microcode, Nintendo proporcionó varias opciones de microcode escrito para elegir. Sin embargo, en el caso que los desarrolladores quieran modificarlo, se verían con una ardua tarea: Las instrucciones de la Scalar Unit no estaban inicialmente documentadas (a la orden de Nintendo, por supuesto). Más tarde, la compañía cambió de posición y SGI finalmente publicó cierta documentación para habilitar el desarrollo de nuevo microcode.

El hardware utilizado para el desarrollo de videojuegos incluía estaciones de trabajo suministradas por SGI, como la máquina **Indy** que venía con una placa extra llamada **U64** que contiene el hardware y I/O de la consola. También se suministraron herramientas para ordenadores con Windows instalado.

Otras herramientas proporcionadas por terceros consistían en cartuchos con un largo cable que se conectaba a la estación de trabajo. Este cartucho se insertaba en una Nintendo 64 pero incluía un circuito interno para redirigir los comandos de lectura de la consola a la RAM de la estación de trabajo. Una vez acabado, la consola se encendía y comenzaba a leer desde la RAM (del ordenador), esto permitía llevar a cabo la implementación y depuración del juego.

---

## Anti-piratería / Region Lock

El sistema antipiratería es una continuación del [CIC de SNES]({{< ref path="super-nintendo#anti-piracy--region-lock" lang="en" >}}). Como sabéis, la detección de piratería y el 'region lock' son posibles gracias al chip CIC (que debe estar presente en cada cartucho de juego *autorizado*), la Nintendo 64 mejoró este sistema requiriendo que diferentes juegos tuvieran una variante específica del CIC para asegurarse de que el cartucho no era una falsificación o contenía un clon del CIC. El **Peripheral interface** o 'PIF' realizaba comprobaciones por medio de checksums al principio y durante la ejecución juego para supervisar el CIC instalado en el cartucho.

Si por alguna razón el PIF considera que el cartucho insertado no es válido, este bloquea la ejecución del juego.

El region-lock se realiza alterando ligeramente la forma externa del cartucho entre las diferentes regiones para que el usuario no pueda insertar físicamente el juego en un N64 de una región diferente.

En general, no hubo demasiada preocupación por la piratería gracias al uso del cartucho como medio, aunque los precios de los juegos eran tres veces más altos que los dependientes del CD.

#### Puertos sin utilizar

Por muy tonto que parezca, Nintendo dejó una 'puerta' abierta: El puerto del Disk Drive.

Algunas compañías, mediante ingeniería inversa, estudiaron la interfaz para desarrollar hardware propietario; Los nuevos productos lanzados llegaron a preocupar a Nintendo debido a sus capacidades para ejecutar juegos piratas.

Supongo que el que vale la pena mencionar es el **Doctor v64**, este dispositivo tiene la misma forma que el Disk Drive pero incluye una unidad de CD-ROM que se utiliza para grabar el contenido del cartucho a un CD, aunque lo opuesto (leer la ROM desde un CD) también es posible.

#### Emulación

Cuando era niño solía jugar a algunos juegos de N64 en un PC con un Pentium II usando un emulador, no era *super lento* pero ahora me pregunto *cómo leches* era capaz de emular felizmente una máquina tan compleja de 64 bits dado que, entre otras cosas, mi PC apenas tenía suficiente RAM para mantener viva la gráfica integrada.

La verdad es que, mientras que reproducir la arquitectura de esta consola puede ser un tarea bastante difícil, cosas como el microcode dan una pista de lo que la consola está intentando hacer, y cómo los emuladores *no tienen que ser precisos*, pueden aplicar suficientes optimizaciones para proporcionar más rendimiento a cambio de una emulación idéntica.  

Otro ejemplo son las instrucciones de 64 bits, ya que los juegos apenas las utilizan, la velocidad de emulación raras veces declina cuando se lleva a cabo en un ordenador de 32 bits.

---

## Eso es todo amigos

Debo decir que este artículo puede ser el más largo que he escrito, pero espero que al menos te haya sido de interés.

Probablemente me tome los siguientes días para ordenar algunas cosas en la web en lugar de empezar a escribir el siguiente artículo.

De todas formas, si te gustan mis artículos y me quieres ayudar, por favor echa un vistazo [aquí](/support). Si tienes algún comentario o sugerencia, no dudes en enviarme un email {{< email "aquí" >}}.

¡Hasta la próxima!  
Rodrigo

---

## Fuentes / Sigue leyendo

#### General

- [**Manual de programación detallado**](http://n64devkit.square7.ch/)
- [**Archivo de software de desarrollo**](http://ultra64.ca/resources/software/)
- [**Anatomía de este sistema**](https://tomplaskon.wordpress.com/2014/06/30/anatomy-of-a-nintendo-64/)

#### CPU

- [**Ficha del MIPS R4300i**](http://datasheets.chipdb.org/MIPS/R4300i_datasheet.pdf)
- [**Ficha del NEC VR4300**](http://datasheets.chipdb.org/NEC/Vr-Series/Vr43xx/U10504EJ7V0UMJ1.pdf)
- [**Sobre el sistema de memoria** (Archived)](https://web.archive.org/web/20200430063847/http://en64.shoutwiki.com/wiki/N64_Memory)

#### Gráficos / Audio

- **Nintendo Ultra64 RSP Programmer’s Guide**
- [**Diferentes Microcodes**](https://www.retroreversing.com/n64rsp)
- [**The Models Resource** (Archivado)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)

#### Juegos

- [**GamePak (Wikipedia)**](https://en.wikipedia.org/wiki/Nintendo_64_Game_Pak)
- [**Dentro de algunos GamePaks**](https://imgur.com/gallery/KteOi)
- [**Hardware de desarrollo**](https://n64squid.com/homebrew/n64-sdk/development-hardware/)
- [**Más sobre el U64**](http://n64devkit.square7.ch/n64man/u64/u64.htm)

#### Antipiratería

- [**Estudiando el Nintendo 64 CIC (charla de REcon)**](https://www.youtube.com/watch?v=HwEdqAb2l50)

#### Fotografía

- Motherboard y consola: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagramas y capturas de juegos: **Me**

#### Bonus

- [**Las impresoras Laserjet usan la misma**](http://www.nec.co.jp/press/en/9801/2002.html)
