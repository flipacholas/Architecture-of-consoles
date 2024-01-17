---
short_title: Arquitectura de la Nintendo 64
long_title: Arquitectura de la Nintendo 64
name: Nintendo 64
release_date: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: '¡Potente y compleja!'
cover: nintendo64
javascript:
  - 'threejs'
top_tabs:
  Model:
    caption: "La Nintendo 64.<br>Lanzada el 23/06/1996 en Japón, el 29/09/1996 en América y el 01/03/1997 en Europa"
    file: "international"
  Motherboard:
    caption: "Mostrando la revisión 'NUS-CPU-03'.<br>Las versiones posteriores redujeron el número de chips requeridos para codificar el audio y video.<br>El conector de 'Disk Drive' se encuentra en el otra cara"
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nintendo-64/
---

## Una breve introducción

El objetivo de Nintendo era dar a los jugadores los mejores gráficos posibles, para llevar a cabo esta tarea, se asociaron con uno de los mayores expertos en gráficos de la industria para producir el chip *definitivo*.

El resultado fue una bonita consola para toda la familia... y un manual de instrucciones de 500 páginas para los desarrolladores.

No te preocupes, te prometo que este artículo no va a ser tan largo como el manual... ¡Disfrútalo!

## {.supporting-imagery}

## CPU

Los orígenes del procesador de la Nintendo 64 comienzan con el **MIPS R4000**, la nueva CPU de vanguardia de Silicon Graphics. Lanzado en 1991, la novedad más aparente del R4000 fue la inclusión de **capacidades de 64 bits**, resultante en la ampliación del tamaño de los buses, registros y unidades de cálculo para manipular los valores de 64 bits con eficiencia. Los desarrolladores, por otra parte, accedieron estas capacidades a través del nuevo set de instrucciones **MIPS III**. En general, el R4000 ha permitido que nuevas aplicaciones puedan manejar porciones de datos más grandes sin consumir ciclos adicionales.

Sea como fuere, el R4000 era un producto costoso (alrededor de $400 [@cpu-r4000demo]), lo que lo hacía inviable para una consola de videojuegos. Sin embargo, Nintendo no quería abandonar sus funcionalidades más avanzadas, así que se decidieron por una variante de gama baja llamada **R4300i**, de la cual NEC fue capaz de proveerla como segunda fuente.

Al final, la CPU elegida de Nintendo fue la **NEC VR4300** corriendo a **93.75 MHz** [@cpu-anatomy]. Esta es una versión compatible con el código binario del MIPS R4300i de Silicon Graphics que incluye [@cpu-nec]:

- **Dos modos de operación**:
  - **Modo de 32 bits**: Modo tradicional donde la CPU se comporta como un procesador compatible con MIPS II. No hay nada especial en este modo excepto que todas las nuevas funciones están bloqueadas.
  - **Modo de 64 bits**: Modo 'Nativo' donde todas las extensiones de 64 bits están disponibles. También es compatible con código binario de aplicaciones de 32 bits.
- **32 registros de uso general**: En el modo de 32-bits, los registros tienen 32-bits de ancho y en el modo 64-bits, 64-bits de ancho.
- **Set de instrucciones MIPS III**: Un set de tipo RISC y sucesor del MIPS II. Incluye nuevos opcodes que calculan palabras de 64 bits llamadas 'doublewords' (palabras dobles). Finalmente, las instrucciones son siempre de **32 bits**, independientemente del modo.
  - Vale la pena mencionar que desde el MIPS II, las [load delay slots'](playstation#delay-galore) han desaparecido para siempre, aunque las 'branch delay slots' todavía persisten.
- Un bus de datos interno de **64 bits** conectado a un **bus externo de 32 bits**: Aunque las *doublewords* no afectarán el rendimiento mientras sean operadas internamente, la CPU aún necesitará gastar ciclos adicionales para mover datos de 64 bits por todo el sistema. Esto es uno de los recortes de la variante R4300i (el R4000 tiene un bus de datos completo de 64 bits).
- Bus de direcciones de **32 bits**: Hasta 4 GB de memoria física puede ser direccionada.
- **Segmentación de 5 etapas**: Hasta cinco instrucciones pueden ser ejecutadas simultáneamente (una explicación detallada se puede encontrar en un [artículo previo](sega-saturn#cpu).
- **24 KB de L1 cache**: Dividido en 16 KB para instrucciones y 8 KB para datos.

También se incluye en este paquete una **Floating-point unit o 'FPU'** (unidad de coma flotante) de 64 bits. La VR4300 la identifica como un co-procesador (CP1) aunque la unidad está instalada junto a la ALU (unidad aritmética) y solo se accede a ella a través de esa unidad, con lo que no hay co-procesamiento de por si. De todas maneras, la FPU contiene un banco de registros dedicado y acelerará las operaciones con números de coma flotante de 64 y 32 bits. Por último, esta unidad sigue el estándar IEEE754.

### Simplificando el acceso a la memoria

La manera en la que la RAM está ensamblada sigue la **arquitectura de memoria unificada** (o 'UMA', del inglés 'Unified-memory Architecture') donde toda la RAM disponible se centraliza en un solo lugar y cualquier otro componente que requiera RAM accederá a esta ubicación compartida. El componente que arbitra su acceso es, en este caso, la GPU.

La razón por la cual se eligió este diseño es por el ahorro considerable en costes de producción, mientras que por otro lado, incrementa la contención de acceso si no se gestiona adecuadamente.

### ¿Sin controlador de DMA?

Debido a la arquitectura de memoria unificada, la CPU ya no tiene acceso directo a la RAM, por lo que la GPU también proporciona la funcionalidad DMA ('Direct Memory Access').

### Diseño de la memoria

Aparte de la UMA, la estructura de la RAM es un poco complicada, así que trataré de explicarlo en términos simples. Aquí va...

El sistema contiene físicamente **4,5 MB de RAM**, sin embargo, está conectada a un bus de datos de **9 bits** donde el 9º bit está reservado para la GPU (se explica más en la sección 'Gráficos'). Como consecuencia, cada componente excepto la GPU sólo encontrará **hasta 4 MB**.

![Organización de la memoria en este sistema. Asumo que la velocidad del CPU-RCP bus es la velocidad de reloj del RCP o la de la CPU, pero aún no he conseguido confirmarlo.](memory.png)

El tipo de memoria RAM instalada en la placa se llama **Rambus DRAM** (RDRAM) [@cpu-memory], este fue otro diseño que compitió con SDRAM para convertirse en el siguiente estándar. RDRAM está conectada en **serie** (donde las transferencias se hacen de un bit a la vez) mientras que SDRAM utiliza una **conexión paralela** (transfiere múltiples bits a la vez).

La latencia de la RDRAM es directamente proporcional al número de bancos instalados [@cpu-rdram], por lo que en este caso, con la cantidad de RAM que tiene este sistema, la latencia resultante es importante (un post en el foro beyond3d menciona que es alrededor de **640 ns**]). Aunque esto es compensado con una alta velocidad de reloj de **250 MHz** (~2.6 veces más rápida que la CPU) aplicada a los bancos de memoria. Nintendo afirma que RDRAM puede proveer una transferencia de datos de alta velocidad a 500 MB por segundo para leer o escribir datos consecutivos.

Además, hay otro debate en los foros de beyond3d que afirma que Nintendo escogió los bancos de memoria uPD488170L de NEC por su placa base [@cpu-beyondrdram]. Estos chips utilizan una tecnología denominada 'Rambus Signaling Logic' que duplica la tasa de transferencia [@cpu-data]. Esto puede explicar por qué algunas fuentes declaran que la tasa 'efectiva' es 500 MHz.

Finalmente, la cantidad de RAM disponible en esta consola **se puede ampliar** instalando el accesorio *Expansion Pak*: Un diminuto bloque bastante peculiar que incluye 4,5 MB. Curiosamente, el bus de RAM debe ser terminado, por lo que la consola siempre se vende con un terminador (llamado *Jumper Pak*) instalado en el lugar del Expansion Pak. Ahora, uno podría preguntarse, ¿qué pasaría si encendemos la consola sin ningún *Pak* instalado? **Literalmente nada**, ¡te quedas con una pantalla en blanco!

### Gestión de memoria

El VR4300 incluye otro procesador llamado **System Control Coprocessor** (CP0) que contiene una **Unidad de gestión de memoria** (o MMU, del inglés) y un **Translation Lookaside Buffer** (TLB), el primero controla como la memoria es organizada y almacenada en el caché. El VR4300 puede acceder hasta 4 GB de direcciones de memoria de 32 bit, pero como ya hemos observado, no tenemos 4 GB de RAM en esta consola (incluso después de considerar la I/O mapeada en memoria). Por lo tanto, la MMU se hace cargo del direccionamiento de memoria y provee un útil mapeado de memoria donde la memoria física se espeja (mirror) varias veces. Como consecuencia, las ubicaciones de memoria se tratan como 'direcciones virtuales' (en vez de 'direcciones físicas). Además, el TLB permite a desarrolladores poder definir sus propios mapeado de memoria en algunos espejos sin (considerable) bajas de rendimiento.

Al principio, esto puede parecer innecesario, pero cada espejo (también llamado 'segmento') está conectado a diferentes circuitos (por ejemplo, el caché L1, memoria sin caché y el TLB), así los desarrolladores pueden optimizar su uso seleccionando el segmento más apropiado para la tarea.

Algunos segmentos están hechos para diferenciar ubicaciones 'kernel' de las ubicaciones 'usuario' por razones de seguridad. La N64 siempre opera en modo 'kernel'. Asimismo, el segmento 'kernel sin TLB almacenado en caché' (llamado 'KSEG0') es el más común para juegos.

La MMU también puede funcionar en modo de 64 bits, donde las direcciones de memoria son de 40 bits. Esto significa que el espacio de direcciones virtual incluye 1 TB de direcciones... ¡Pero no creo que la Nintendo 64 se aproveche de esto!

## Gráficos

Lo que se ve en la pantalla está producido por un inmenso chip diseñado por Silicon Graphics llamado **Reality Co-Processor** que corre a **62,5 MHz**. Este paquete contiene *un montón* de circuitería así que no te preocupes si te resulta difícil de seguir, el sub-sistema de gráficos tiene una arquitectura muy compleja.

Este diseño se basa en la filosofía de que la GPU no debe ser una 'simple' rasterizadora como en el caso de [la competencia](playstation#graphics). Por el contrario, debe ser capaz de **acelerar los cálculos geometricos** (aliviando la carga de la CPU) y para ello, es necesario más circuitería.

### Arquitectura

Este chip está dividido en tres módulos principales, dos de los cuales se utilizan para el procesamiento de gráficos:

#### Reality Signal Processor {.tabs.active}

![Arquitectura del Reality Signal Processor (RSP).](RSP.png) {.tab-float}

También conocido como **RSP**, es simplemente otro paquete con una CPU compuesto por:

- La **Scalar Unit**: Otra versión derivada y reducida del MIPS R4000. Esta vez, sólo implementa un subconjunto del set MIPS III, por lo que carece de muchas funciones de propósito general (por ejemplo, interrupciones y excepciones), la extensión de 64 bits, multiplicación y división.
- La **Vector Unit**: Un co-procesador que realiza operaciones vectoriales con 32 registros de 128 bits. Cada registro está separado en ocho partes para operar ocho vectores de 16 bits a la vez (al igual que las instrucciones SIMD en las CPUs convencionales). Como puede ver, este componente hace el trabajo duro para la Scalar Unit.
- El **System Control**: Otro co-procesador que cubre las funciones de DMA y controla su módulo vecino, el RDP (más sobre esto en breve).

Para operar este módulo, la CPU almacena en la RAM una serie de comandos llamados **Display list** junto con los datos que serán manipulados, luego el RSP lee dicha lista y aplica las operaciones requeridas sobre los datos. La funcionalidad disponible incluye transformaciones geométricas (como la proyección en perspectiva), clipping e iluminación.

Esto parece sencillo, pero ¿cómo realiza estas operaciones? Bueno, aquí está la parte interesante: A diferencia de su competencia (la PS1 y la Sega Saturn), **el motor de la geometría no es fijo**. En su lugar, el RSP contiene un poco de memoria (4 KB para instrucciones y 4 KB para datos) para almacenar **microcode** [@audio_video-ultra], un pequeño programa, con no más de 1000 instrucciones, que **implementa la tubería de gráficos**. En otras palabras, dirige a la Scalar Unit sobre como debe operar nuestros gráficos. La CPU envía microcode mientras el programa se encuentra en ejecución.

Nintendo proporcionó diferentes microcódigos para escoger [@audio_video-microcodes] y, similar a [los modos de fondo de la SNES](super-nintendo#graphics), cada uno balancea los recursos de manera diferente.

#### Reality Display Processor {.tab}

![Arquitectura del Reality Display Processor (RDP).](RDP.png) {.tab-float}

Una vez que el RSP termine de procesar los datos de nuestros polígonos, empezará a enviar **comandos de rasterización** al siguiente módulo, el **RDP**, para dibujar el frame. Estos comandos se envían por un bus dedicado llamado **XBUS** o por la RAM principal.

El RDP es otro procesador (esta vez con funcionalidad fija) que incluye múltiples motores utilizados para rasterizar vectores, mapear las texturas a nuestros polígonos, mezclar colores y componer el nuevo frame.

Este puede procesar **triángulos** o **rectángulos** como primitivos, el último es útil para dibujar sprites. La tubería de rasterización del RDP contiene los siguientes bloques:

- Un **Rasteriser**: Convierte primitivos (hechos de vértices) a píxeles.
- Una **Texture Unit**: Procesa texturas usando 4 KB de memoria dedicada (llamada 'TMEM') por lo que permite hasta ocho tiles para ser usadas para la textura. Puede realizar las siguientes operaciones sobre ellas:
  - **Bilinear filtering**: Mapea la textura 2D seleccionada sobre la figura 3D y la suaviza para evitar áreas pixeladas (causado por sobremuestreo).
    - Un filtro 'completo' requiere cuatro puntos para aplicar la interpolación, sin embargo, esta consola sólo utiliza tres (**interpolación triangular**) lo que produce algunas anomalías. Por lo tanto, ciertas texturas tendrán que ser 'adaptadas' de antemano.
  - **Mip-Mapping**: Selecciona automáticamente una versión reducida de la textura dependiendo de su **nivel de detalle**. Esto evita computar grandes texturas que se verían lejos de la cámara y con ello prevenir el 'aliasing' (causado por submuestreo).
    - Si se activa, la N64 mapea las texturas usando **trilinear filtering** en su lugar. Este nuevo algoritmo también interpolará entre mipmaps para suavizar los cambios repentinos en el nivel de detalle.
  - **Perspective correction**: Algoritmo escogido para mapear texturas en triángulos. A diferencia de [otros algoritmos](playstation#graphics), este tiene en cuenta el valor de profundidad de cada primitiva, logrando mejores resultados.
- Un **Colour Combiner**: Mezcla e interpola múltiples capas de colores (por ejemplo, para aplicar luces).
- Un **Blender**: Mezcla píxeles sobre el actual frame-buffer para aplicar translucidez, anti-aliasing, niebla o dithering. También realiza z-buffering (más sobre esto en adelante).
- Una **Memory interface**: Utilizada por los bloques anteriores para leer y enviar el frame-buffer procesado a la RAM y/o llenar el TMEM.

La RDP proporciona cuatro modos de operación, cada uno combina estos bloques de forma diferente para optimizar operaciones específicas.

Dado que este módulo requiere actualizar constantemente el frame-buffer, la RAM se maneja de forma diferente: ¿Te acuerdas del inusual 'byte' de 9 bits? El noveno bit se utiliza para los cálculos relacionados con el frame-buffer (z-buffering y el antialiasing) y sólo se puede acceder a él a través de la Memory interface.

#### Pasos restantes {.tabs-close}

El frame que se obtiene debe ser enviado al **Video Encoder** para visualizarlo en pantalla (el **DMA** y el **Video Interface** son esenciales para llevar a cabo esto).

En teoría, las capacidades máximas son una profundidad de color de 24 bits (16,8 millones de colores) y una resolución de 720x576 (o 640x480 en la región NTSC). En la práctica no se utilizan todos, ya que hacer uso de las capacidades máximas puede ser muy costoso en términos de recursos, por lo que los programadores tenderán a utilizar cifras más bajas para proveer suficientes recursos a otros servicios.

### Demostración rápida

Pongamos todas las explicaciones anteriores en perspectiva. Para ello tomaré prestado el *Super Mario 64* de Nintendo para demostrar, en pocas palabras, cómo se forma un frame:

#### Procesamiento de vértices {.tabs.active}

![Vista primitiva de nuestra escena. Para ahorrar polígonos, algunos caracteres se modelan con sprites (cuadriláteros)](mario/wireframe.jpg) {.tab-float}

Inicialmente, nuestros modelos 3D se encuentran en la ROM del cartucho, pero para mantener un ancho de banda constante, necesitamos copiarlos primero a la RAM. En algunos casos, los datos se pueden encontrar ya comprimidos en el cartucho, por lo que la CPU tendrá que descomprimirlo antes de utilizarlo.

Una vez hecho esto, es hora de construir una escena usando nuestros modelos. la CPU podría hacerlo por sí misma pero esto tardaría *toda una vida*, así que la tarea se delega al RCP. La CPU se limitará a enviar órdenes al RCP, esto se hace llevando a cabo las siguientes tareas:

1. Componer la Display List que contiene las operaciones a realizar por el RSP y almacenarla en la RAM.
2. Apuntar al RSP donde se encuentra la Display List.
3. Enviar el microcode al RSP para poner en marcha la Scalar Unit.

Seguidamente, el RSP comenzará a realizar las tareas y el resultado será enviado al RDP en forma de comandos de rasterización.

#### Procesamiento de píxeles {.tab}

![Frame renderizado (_Tachán!_).](mario/result.png) {.tab-float}

Hasta ahora hemos logrado procesar nuestros datos y aplicar algunos efectos sobre ellos, pero todavía tenemos que:

- Rasterizar vectores, aplicar texturas y otros efectos.
- Mostrar un frame-buffer en pantalla.

Como puedes adivinar, estas tareas serán realizadas por el RDP. Para que esto funcione, las texturas deben ser copiadas desde la RAM a el TMEM usando el DMA.

El RDP tiene un motor fijo pero permite seleccionar un modo de funcionamiento en base a la tarea que se llevará a cabo, ayudando a mejorar el framerate.

Una vez que el RDP termine de procesar los datos, escribirá el mapa de bits resultante en el frame-buffer que se encuentra dentro de la RAM. Una vez terminado, la CPU debe transferir el nuevo frame a la **Video Interface** (VI), preferiblemente usando el DMA,  que a su vez la enviará al **Video Encoder** para su visualización.

### Diseños {.tabs-close}

He aquí algunos ejemplos de personajes previamente diseñados en dos dimensionas para la [Super Nintendo](super-nintendo), pero que han sido rediseñados para la nueva era de tres dimensiones. Los modelos son interactivos así que os animo a que les echéis un vistazo.

![The Legend of Zelda: Ocarina of Time (1998).<br>704 triángulos.](link_ocarina_64){.toleft model3d="true"}

![Kirby 64: The Crystal Shards (2000).<br>516 triángulos.](kirby_cristals_64){.toright model3d="true"}

### Una solución moderna al problema de superficies visibles

Si has leído sobre las consolas anteriores, probablemente estás al tanto del interminable problema sobre la [visibilidad de superficies](sega-saturn#an-introduction-to-the-visibility-problem) y tal vez pienses que la única solución disponible es el 'ordenamiento de polígonos'. Bueno, por primera vez en esta serie, el RDP presenta una solución basada en hardware llamada **Z-buffering**. En pocas palabras, el RDP asigna un nuevo buffer llamado **Z-Buffer** en la memoria. Este tiene las mismas dimensiones que un frame-buffer, pero en lugar de almacenar los valores RGB (rojo, verde y azul), cada elemento contiene la profundidad del píxel más cercano con respecto a la cámara (llamado 'z-value').

Una vez que el RDP rasteriza los vectores, el z-value del nuevo píxel se compara con el valor respectivo en el Z-buffer. Si el nuevo píxel contiene un z-value más pequeño, esto significa que el nuevo píxel se posiciona delante del anterior, por lo que el nuevo valor se escribirá el frame-buffer y z-buffer. De lo contrario, el píxel se descarta.

En general, el nuevo algoritmo es muy beneficioso: Los programadores ya no tienen que preocuparse por implementar métodos para ordenar polígonos [basados en software](playstation#tab-4-2-visibility-approach) que tienden a consumir bastantes recursos de la CPU. Sin embargo, el Z-buffer no previene procesar geometría innecesaria (eventualmente descartada o sobrescrita, que consumen recursos de todas maneras). Para ello, el motor del juego puede optar por incluir un algoritmo de **'occlusion culling'** para descartar la geometría, que no será visible, lo antes posible.

### Secretos y limitaciones

Es evidente que SGI invirtió mucha tecnología en este sistema. Sin embargo, esta sigue siendo una consola para el hogar y como tal, debe mantener un bajo coste. Por ahí, ciertas decisiones se convirtieron en retos para los programadores:

#### Atascos en la tubería {.tabs.active}

Debido al gran número de componentes y operaciones en la tubería de gráficos, el RCP terminó siendo muy susceptible a **burbujas de segmentación**: Una situación indeseable caracterizada por subcomponentes que se mantienen inactivos durante bastante tiempo porque los datos requeridos se retrasan en llegar.

Esto siempre se manifestará en una degradación del rendimiento y depende del programador para evitarlos. Aunque para facilitar las cosas, algunas CPUs como la Scalar Unit implementan una característica llamada **Bypassing** que permite ejecutar instrucciones similares a un ritmo más rapido, esto se consigue evitando etapas de ejecución que pueden ser omitidas.

Por ejemplo, si tenemos que computar múltiples instrucciones `ADD` (suma) a la vez, no hay necesidad de escribir el resultado de la suma en el registro destinatario y luego leerlo de nuevo para ejecutar el siguiente `ADD`. En su lugar, se puede usar el mismo registro intermediario para llevar al cabo todas las adiciones y al final, enviar el resultado al registro destinatario una vez que el último `ADD` se completa.

#### Memoria de texturas {.tab}

El RDP usa 4 KB de TMEM ('Texture Memory') como única unidad para procesar las texturas. Desafortunadamente, en la práctica 4 KB resultaron ser insuficientes para las texturas de alta resolución. Además, si se utiliza el mipmapping, la cantidad de memoria disponible se reduce a la mitad.

Como resultado, algunos juegos utilizan colores sólidos con sombreado Gouraud (como *Super Mario 64*) mientras que otros hacen uso de texturas precalculadas (por ejemplo, cuando hay que mezclar varias capas).

### La salida de video universal {.tabs-close}

Nintendo siguió usando la salida 'universal' llamada [Multi Out](super-nintendo.md#a-convenient-video-out) como su predecesor, pero desafortunadamente, **¡ya no transmite más la señal RGB!**. Esto me suena a otra medida para ahorrar costes, ya que la misma señal no se había aprovechado en la consola anterior.

Las buenas noticias son que los tres canales pueden ser reconstruidos en las primeras revisiones de la consola mediante la soldadura de algunos cables y la instalación de un amplificador de señal (que tiende a ser barato). Esto es debido a que el conversor de digital a analógico de video transmite una señal RGB al codificador de video. Sin embargo, las revisiones posteriores de la placa base combinaron los dos chips, así que la única solución disponible es de sobrepasar el video DAC y el codificador con algún chip especializado que pueda exponer la señal RGB.

## Audio

Antes de entrar en detalles, definamos los dos extremos del subsistema de audio de la N64:

- Nuestro punto de partida es la ROM del cartucho, contiene datos que sólo la CPU puede interpretar.
- El punto final es el **Digital-to-Analog converter** o 'DAC', que sólo entiende datos que codifican la waveform (forma de onda de sonido).

Dicho esto, ¿cómo conectamos ambos? Las consolas normalmente incluyen un chip de audio dedicado que hace ese trabajo por nosotros. Desafortunadamente, la Nintendo 64 **no tiene dicho chip dedicado**, por lo que esta tarea se distribuye a través de estos componentes:

- La **CPU principal**: Transfiere los datos de audio de la ROM del juego a la RAM, y luego crea **Audio Lists** para ser usadas por el RSP.
- El **RSP**: Con el uso de más microcode, este interpreta las Audio Lists previamente almacenadas en la RAM y realiza las operaciones necesarias sobre los datos de audio. Esto, por ejemplo, incluye:
  - Descomprimir **ADPCM samples** y aplicar efectos.
  - Secuenciar y mezclar **datos MIDI** utilizando **bancos de audio** almacenados también en la RAM.

Los datos resultantes, como era de esperar, contienen los waveforms. Estos se envían al bloque de **Audio Interface** o 'AI' que los transferirá al Digital-to-Analog converter. La waveform resultante contiene dos canales (ya que nuestro sistema es estéreo) con una resolución de 16-bits cada uno.

![Resumen de cómo se tiende a programar el motor de audio.](Audio.png)

### Repertorio musical

Es hora de echar un vistazo a las bandas sonoras hechas para la N64. La verdad es que hay demasiados buenos ejemplos para mencionar todos en este artículo, así que os dejo algunos que me llamaron la atención:

![The Legend of Zelda: Majora's Mask (2000).<br>La música de este juego está ligada a su atmósfera intimidante.](observatory){.toleft video="true"}

![Bomberman Hero (1998).<br>Este juego tiene una agradable y única banda sonora de estilo house.](redial){.toright video="true"}

### Secretos y limitaciones

Debido a este diseño, las limitaciones de este sistema dependerán de la implementación:

- La frecuencia de los samples puede ser de hasta 44,1Hz, pero usar la frecuencia máxima requiere demasiados ciclos de CPU.
- No hay un límite estricto en el número de canales, todo depende de cuántos canales sea capaz de mezclar el RSP (a menudo alrededor de 16-24 canales si se procesa ADPCM o ~100 si PCM).
- La memoria es otro asunto a tener en cuenta. Mientras que la competencia proveía medios más grandes (por ejemplo, CD-ROM) y memoria de audio dedicada, los cartuchos de Nintendo 64 contienen mucha menos capacidad (por no hablar de espacio reservado para música) y deben compartir la RAM principal con otros componentes.

Por estas razones, los jugadores pueden llegar a notar que los ports de N64 contienen música de menor calidad o repetitiva. Un método para superar esta limitación consistía en implementar un secuenciador en software que pudiera 'construir' los samples mientras el juego se ejecutaba, esto requiere un banco de sonidos (similar a la música MIDI).

## Sistema operativo

Al igual que la PS1 y Saturn, los juegos de N64 hablan directamente con el hardware. Sin embargo, no hay rutinas de la BIOS disponibles para simplificar algunas operaciones. Como sustituto, **los juegos incorporan un pequeño sistema operativo** que proporciona una buena cantidad de abstracción para manejar eficientemente la CPU, la GPU y el I/O.

Este no es el *Sistema Operativo de escritorio* convencional que podemos imaginar al principio, es sólo un micro-kernel con el menor tamaño posible que proporciona la siguiente funcionalidad:

- Multi-Threading mediante 'message passing' (aunque tened en cuenta que la CPU es de un solo núcleo).
- Scheduling (planificador) y Preemption (Multitarea apropiativa).
- Simplificado acceso a registros y I/O.

En general, estas funciones son críticas para organizar las tareas de audio, video y la lógica del juego. Todas deben funcionar al mismo tiempo.

El kernel se inserta automáticamente al utilizar librerías de Nintendo. Además, si los programadores deciden no incluir alguna de las librerías, la porción respectiva del kernel se quita para evitar desperdiciar el espacio del cartucho.

### Proceso de arranque

A diferencia de los anteriores sistemas basados en cartuchos, la Nintendo 64 sigue un sofisticado proceso de arranque para preparar todo su hardware antes de que se ejecute el juego. Este se ejecuta cuando el usuario enciende la consola y es muy similar a sus contemporáneos con lectora de CD que contienen una [BIOS](playstation#operating-system) o una [IPL](sega-saturn#operating-system) [@operating_system-ipl].

Estas rutinas también se denominan **Initial Program Load** (IPL) y funcionan de la siguiente manera: [@operating_system-boot]:

1. El usuario enciende la consola.
2. El **PIF-NUS** (un chip separado en la placa madre) somete la CPU principal a un reinicio infinito hasta que el PIF-NUS valide el chip CIC encontrado en el cartucho del juego.
    - El PIF-NUS y el chip del CIC se explican en más detalle en la sección de I/O y Antipiratería, respectivamente.
2. Si el proceso de verificación terminó con éxito, la CPU inicia la ejecución en `0xBFC00000`. Esta dirección apunta a una **ROM interna** dentro del PIF-NUS, en particular, la primera etapa de arranque llamada **IPL1**.
3. IPL1 inicializa parte del hardware (registros de la CPU, la interfaz paralela y el RCP) y copia la siguiente etapa (**IPL2**) de la ROM interna a la memoria del RSP para una ejecución más rápida. Luego, redirige allí la ejecución.
4. IPL2 inicializa la RDRAM y el caché de la CPU. Después, copia el primer megabyte de la ROM del juego a la RDRAM. Este bloque contiene la siguiente etapa del arranque denominada **IPL3**.
5. IPL3 inicia el sistema operativo (es decir, la memoria virtual y los vectores de excepción), configura el estado del programa (es decir, el puntero de pila o 'stack pointer') y finalmente invoca la rutina inicial del juego.

Como IPL3 se encuentra en el cartucho del juego, no todos los juegos empaquetan el mismo código. Presumiblemente, las variantes de IPL3 están correlacionadas con la variante del chip CIC que se encuentra en el cartucho.

## E/S

Como ya sabéis, el I/O no está conectado directamente a la CPU, así que el tercer módulo del RCP (que no he mencionado hasta ahora) sirve como **interfaz I/O**. Básicamente, se comunica con la CPU, los controladores, el cartucho de juego y los DAC de Audio/Video.

### Accesorios

El mando de Nintendo 64 incluye un conector para enchufar accesorios. Ejemplos de accesorios comerciales incluyen:

- El **Controller Pak**: Otro medio (similar a la *Memory Card* de Sony) que se utiliza para almacenar partidas y llevarlas a otras consolas.
- El **Rumble Pak**: Contiene un pequeño motor para proporcionar vibración, útil para sumergir al jugador en la atmósfera del juego.

Todos los accesorios conectados al mando son manejados por el **PIF-NUS**, un bloque relativamente desconocido que también controla la seguridad. El RCP se comunica al PIF utilizando un **Bus de serie** 'muy lento' (citado del manual de programación).

## Juegos

Nintendo se aferró al cartucho como medio para el almacenamiento y como consecuencia, los juegos disfrutaron de mayores anchos de banda (un promedio de 5 MB/s según Nintendo) mientras que costaban más de fabricar. El cartucho más grande que hubo en el mercado tiene 64 MB.

Dentro de los cartuchos, los fabricantes pueden incluir memoria adicional (en forma de *EEPROM*, *flash* o *SRAM* con una batería) para guardar partidas. Sin embargo, no es un requerimiento estricto ya que algunos accesorios también podían ser usados para almacenar las partidas.

Los cartuchos se comunican al RCP utilizando un bus dedicado de 16 bits llamado **Bus Paralelo** (PBUS) o 'Interfaz Paralela' (PI).

### Kit de Desarrollo

En general, el desarrollo se realizó principalmente en **C** y **ensamblador**, este último a menudo era necesario para lograr un mejor rendimiento. Mientras que hemos visto que este sistema ofrece operaciones de 64 bits, las nuevas instrucciones rara vez fueron usadas ya que, en la práctica, las instrucciones de 32 bits fueron más rápidas de ejecutar (dado que la R4300i/VR4300 viene con un bus de datos de 32 bits).

Por otra parte, las librerías en SDK oficial contenían varias capas de abstracciones para comandar el RCP. Por ejemplo, la **Graphics Binary Interface** o ‘GBI’ era una estructura diseñadas para ensamblar las Display Lists más fácilmente, lo mismo se aplicaba a las funciones de audio (su estructura se llamó **Audio Binary Interface** o 'ABI').

En cuanto al uso de microcode, Nintendo proporcionó varias opciones de microcode fijo para elegir. Sin embargo, en el caso que los desarrolladores quieran modificarlo, se verían con una ardua tarea: Las instrucciones de la Scalar Unit no estaban inicialmente documentadas, aunque luego, Nintendo cambió de posición y SGI finalmente publicó cierta documentación para habilitar el desarrollo de nuevo microcode.

El hardware utilizado para el desarrollo de videojuegos incluía estaciones de trabajo suministradas por SGI [@games-devkit], como la máquina **Indy** que venía con una placa extra llamada **U64** que contiene el hardware y I/O de la consola. También se suministraron herramientas para ordenadores con Windows instalado [@games-u64].

Otras herramientas proporcionadas por terceros consistían en cartuchos con un largo cable que se conectaba a la estación de trabajo. Este cartucho se insertaba en una Nintendo 64 común, pero incluía un circuito interno para redirigir los comandos de lectura de la consola a la RAM de la estación de trabajo. El proceso de depuración (o 'debugging') se llevó a cabo transfiriendo una copia del juego a la RAM y una vez que la consola fuera encendida, comenzaría a leer desde ahí.

### El medio alternativo

Adicionalmente, el PBUS se ramifica a otro conector en la parte inferior de la placa base de la N64. Esto estaba destinado a ser utilizado por la **Nintendo 64 Disk Drive** (64DD) aún inédita. Esta era una especie de 'piso adicional' que contiene un lector de disco magnético proprietario [@games-dd]. Sus discos contienen hasta 64 MB de capacidad. Aunque solo se lanzó en Japón, la unidad de disco abrió la puerta a un medio alternativo (y más barato) para distribuir juegos.

![La Nintendo 64 Disk Drive [@photography-amos].<br>Lanzada el 01/12/1999 en Japón.](64dd/module.png){.open-float .tabs-nested .tab-float .active title="Módulo"}

![La 64DD conectado a la consola [@photography-amos].](64dd/attached.png){.tabs-nested-last title="Conectado"}

El medio magnético es más lento que los cartuchos, con velocidades de transferencia de hasta 1 MB/seg, aunque más rápidos que los lectores de 4X CD-ROM. Los discos son de doble-cara y operan a una 'Velocidad Angular Constante' (como el posterior [miniDVD](gamecube#medium)). El área lejible más pequeño se llama 'block' y es la mitad de un círculo concéntrico.

El lector no incluye un búfer de memoria, así que los bits leídos son almacenados en RDRAM para ejecución. Nintendo incluyó la unidad de expansión de RAM con la 64DD, por lo que compensa la necesidad de más RAM (además de estandarizar la disponibilidad de RAM extendida para todos los juegos de la 64DD).

{.close-float}

Además, partes del disco se pueden reescribir para permitir el almacenamiento de partidas, la cantidad de área disponible para escritura depende del tipo de disco usado (Nintendo proporcionó siete tipos). Del lado del software, los datos del juego son estructurados con un sistema de archivos llamado 'Multi File System' (MFS) que Nintendo proveyó en su SDK. Los juegos pueden acceder los datos del disco utilizando el sistema de archivo o a nivel de bloque, el último requiere otra librería llamada 'Leo' que proporciona funciones de bajo nivel.

El Disk Drive también alberga una ROM interna (denominada "DDROM") que almacena el código que ejecuta la N64 para arrancar el disco y mostrar la animación de bienvenida. Esto funciona como una nueva etapa del IPL añadida encima del proceso de arranque tradicional. La ROM también almacena fuentes (Latín y Kanji) y algunos sonidos. Esta ROM solo se encuentra en la version 'retail' del 64DD, ya que las unidades de desarrollo usaban programas externos cargados a través del kit de desarrollo.

## Anti-piratería / Region Lock

El sistema antipiratería es una continuación del [CIC de la SNES](super-nintendo.md#anti-piracy--region-lock). Como sabéis, la detección de piratería y el 'region lock' son posibles gracias al chip CIC (que debe estar presente en cada cartucho de juego *autorizado*) [@anti_piracy-cic], la Nintendo 64 mejoró este sistema requiriendo que diferentes juegos tuvieran una variante específica del CIC. Esto es para asegurarse de que el cartucho no era una falsificación o contenía un clon del CIC. El PIF realiza comprobaciones por medio de checksums al inicio y durante la ejecución del juego para supervisar el CIC instalado en el cartucho.

Si por alguna razón el PIF considera que el cartucho insertado no es válido, este congela la ejecución del juego.

El region-lock se realiza alterando ligeramente la forma externa del cartucho entre las diferentes regiones para que el usuario no pueda insertar físicamente el juego en una N64 de diferente región.

En general, no hubo demasiada preocupación por la piratería gracias al uso del cartucho como medio, aunque los precios de los juegos eran tres veces más altos que aquellos basados en CD.

### Puertos sin utilizar

Por muy tonto que parezca, Nintendo dejó una 'puerta' abierta: El puerto del Disk Drive.

![El Doctor V64 conectado a la consola [@photography-amos].](v64/attached.png){.open-float .tabs-nested .tab-float .active title="Conectado"}

![El V64 visto desde atrás, mostrando conectores especiales para A/V.](v64/back.png){.tabs-nested-last title="Detrás"}

Algunas compañías, mediante ingeniería inversa, desarrollaron hardware propietario. Y algunos de estos resultados llegaron a preocupar a Nintendo debido a sus capacidades para ejecutar juegos piratas.

Supongo que vale la pena mencionar el **Doctor v64**, este dispositivo tiene la misma forma que el Disk Drive pero incluye una unidad de CD-ROM.

Esta expansión puede grabar el contenido del cartucho a un CD, y lo opuesto (leer la ROM desde un CD) también es posible.

{.close-float}

### Emulación

Cuando yo era niño solía jugar a algunos juegos de Nintendo 64 en un ordenador con un Pentium II usando un emulador, no funcionaba *tan* mal, pero años más tarde me pregunté *cómo narices* era capaz de emular una máquina de 64 bits tan tranquilamente si, entre muchas cosas, mi ordenador apenas tenía la suficiente RAM para mantener la tarjeta gráfica integrada con vida.

La verdad es que, mientras que reproducir la arquitectura de esta consola puede ser un tarea bastante difícil, cosas como el microcode dan una pista de lo que la consola está intentando hacer, y cómo los emuladores *no tienen que ser precisos*, pueden aplicar suficientes optimizaciones para proporcionar más rendimiento a cambio de una emulación idéntica.

Otro ejemplo son las instrucciones de 64 bits, ya que los juegos apenas las utilizaron, la velocidad de emulación raras veces empeora cuando se lleva a cabo en un ordenador de 32 bits.

## Eso es todo amigos

![Mi N64 compartida en la casa de un amigo.<br>Mientras que yo solo quería la consola para este artículo, mi colega siempre soñó por un N64 DD, así que compramos un set muy completo (pero caro) compuesto por una N64 japonesa con un DD y así evitamos gastar demasiado individualmente. Después, le instalé el N64RGB para que podamos conectarlo a una TV moderna; y el resultado es una buena unidad de entretenimiento (¡y tema de conversación!).](n64.jpeg)

Debo decir que este artículo puede ser el más largo que he escrito, pero espero que al menos te haya sido de interés.

Probablemente me tome los siguientes días para ordenar algunas cosas en la web en lugar de empezar a escribir el siguiente artículo.

¡Hasta la próxima!  
Rodrigo
