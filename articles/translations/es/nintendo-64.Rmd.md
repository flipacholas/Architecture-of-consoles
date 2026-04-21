---
short_title: Arquitectura de la Nintendo 64
long_title: Arquitectura de la Nintendo 64
name: Nintendo 64
release_date: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: Potente pero limitada
cover: nintendo64
javascript: ['threejs']
seo_image_pos: "Top"
top_tabs:
  Model:
    caption: "La Nintendo 64.<br>Lanzada el 23/06/1996 en Japón, el 29/09/1996 en América y el 01/03/1997 en Europa"
    file: "international"
    latex_height: 87
  Motherboard:
    caption: "Mostrando la revisión 'NUS-CPU-03'.<br>Las versiones posteriores redujeron el número de chips necesarios para la codificación AV.<br>El conector del Disk Drive se encuentra en la parte trasera"
  Diagram:
    latex_height: 95

# Historical
aliases: [/projects/consoles/nintendo-64/]
---

## Una breve introducción

El objetivo de Nintendo era dar a los jugadores los mejores gráficos posibles. Para llevar a cabo esta tarea, se asociaron con uno de los mayores expertos en gráficos de la industria para producir el chip *definitivo*.

El resultado fue una bonita consola para toda la familia... y un manual de instrucciones de 500 páginas para los desarrolladores.

No te preocupes, te prometo que este artículo no va a ser tan largo como el manual... ¡Disfrútalo!

## {.supporting-imagery}

## CPU

Los orígenes del procesador principal de la Nintendo 64 comienzan con el **MIPS R4000**, la nueva CPU de vanguardia de [MIPS](playstation#tab-1-2-mips-and-sony). Lanzada en 1991, la novedad más ostensible del R4000 fue la inclusión de **capacidades de 64 bits**, fruto de ensanchar los buses, registros y unidades de cálculo para manipular valores de 64 bits con eficiencia. Los desarrolladores, por su parte, accedieron a estas capacidades a través del nuevo set de instrucciones **MIPS III**. En líneas generales, el R4000 permitió que nuevas aplicaciones pudieran manejar bloques de datos más grandes sin consumir ciclos adicionales.

Para su consola de nueva generación, Nintendo investigó la posibilidad de llevar hardware industrial al hogar. A diferencia de [Sony](playstation#cpu), que disponía de una gran cantidad de componentes propios y solo necesitaba un proveedor secundario de CPUs MIPS, Nintendo se asoció directamente con los propietarios de MIPS (y de numerosas estaciones de trabajo gráficas) para co-diseñar todo su ecosistema. Esa compañía era **Silicon Graphics Incorporated** (SGI).

En las oficinas de SGI, el R4000 era un producto costoso (alrededor de $400 [@cpu-r4000demo]), lo que lo hacía prohibitivo para una consola de videojuegos. Aun así, Nintendo no quería renunciar a sus ambiciones tecnológicas de vanguardia, así que optaron por una variante de gama baja llamada **R4300i**, de la que NEC pudo actuar como segundo proveedor.

![La CPU MIPS R4300i (1994).](r4300i.webp)

Al final, Nintendo y SGI se decantaron por la **NEC VR4300** funcionando a **93,75 MHz** [@cpu-anatomy]. Se trata de una versión con compatibilidad binaria del MIPS R4300i que incluye [@cpu-nec]:

- **Dos modos de operación**:
  - **Modo de 32 bits**: Modo tradicional en el que la CPU se comporta como un procesador compatible con MIPS II. No hay nada especial en este modo, salvo que todas las nuevas funcionalidades quedan bloqueadas.
  - **Modo de 64 bits**: Modo 'nativo' donde todas las extensiones de 64 bits están disponibles. También es compatible a nivel binario con aplicaciones de 32 bits.
- **32 registros de uso general**: De 32 bits de ancho en modo de 32 bits, y de 64 bits en modo de 64 bits.
- **Set de instrucciones MIPS III**: Un set de tipo RISC sucesor del MIPS II. Introduce nuevos opcodes para calcular palabras de 64 bits denominadas 'doublewords'. Las instrucciones son siempre de **32 bits de longitud**, independientemente del modo.
  - Cabe mencionar que, a partir de MIPS II, los [load delay slots](playstation#delay-galore) pasaron a ser opcionales. En su lugar, el hardware puede trabar o detener automáticamente el pipeline para evitar riesgos de datos. Resulta curioso que esto contradiga la filosofía original de las [siglas MIPS](playstation#a-core-philosophy).
  - Curiosamente, los branch delay slots siguen presentes, aunque se añadieron nuevas instrucciones de salto que permiten descartarlos. Eso sí, dichas instrucciones fueron eliminadas en revisiones posteriores del set [@cpu-chen].
- Un **bus interno de 64 bits** conectado a un **bus de datos externo de 32 bits**: Aunque las operaciones con doublewords no penalizan el rendimiento cuando se ejecutan internamente, mover datos de 64 bits por el resto del sistema implica ciclos adicionales.
  - Éste es uno de los recortes de la variante R4300i (el R4000 dispone de un bus de datos completo de 64 bits).
- **Bus de direcciones de 32 bits**: Capaz de direccionar hasta 4 GB de memoria física.
- **Pipeline de cinco etapas**: Hasta cinco instrucciones pueden asignarse para ejecución simultánea (encontrarás una explicación detallada en un [artículo anterior](sega-saturn#cpu)).
- **24 KB de caché L1**: Repartidos en 16 KB para instrucciones y 8 KB para datos.

El paquete también incluye una **Unidad de Punto Flotante** (FPU, del inglés 'Floating Point Unit') integrada. La VR4300 la denomina coprocesador (CP1); sin embargo, la unidad está ubicada junto a la ALU y solo se accede a ella a través del pipeline de ésta, por lo que en la práctica no hay coprocesamiento como tal. Dicho esto, la FPU dispone de su propio banco de registros dedicado y acelera las operaciones con números de punto flotante de 64 y 32 bits. Para terminar, esta unidad cumple con el estándar IEEE 754.

### Simplificando el acceso a la memoria

La forma en que se ensambla la RAM sigue la **Arquitectura de Memoria Unificada** (UMA, del inglés 'Unified Memory Architecture'), donde toda la RAM disponible se centraliza en un único lugar y cualquier componente que necesite acceder a la RAM lo hace desde esa ubicación compartida. El árbitro de ese acceso es, en este caso, la GPU.

Este diseño se escogió principalmente por el considerable ahorro que supone en costes de fabricación; aunque si no se gestiona bien, también incrementa la contención de accesos.

### ¿Sin controlador de DMA?

Como consecuencia de la arquitectura de memoria unificada, la CPU ya no tiene acceso directo a la RAM. Por ello, la GPU también proporciona la funcionalidad de [Acceso Directo a Memoria](playstation#taking-over-the-cpu) (DMA).

### Diseño de la memoria

Al margen de la UMA, la estructura de la RAM es algo más enrevesada, así que intentaré explicarlo de la forma más sencilla posible. Allá vamos...

El sistema dispone físicamente de **4,5 MB de RAM**; sin embargo, está conectado mediante un bus de datos de **9 bits**, donde el 9º bit está reservado para la GPU (lo explico con más detalle en la sección 'Gráficos'). Como consecuencia, todos los componentes salvo la GPU solo podrán acceder a **un máximo de 4 MB**.

![Organización de la memoria del sistema. Asumo que la velocidad del bus CPU-RCP coincide con la velocidad de reloj del RCP o de la CPU.](_diagrams/memory.png){.no-borders}

El tipo de RAM instalada en la placa se llama **Rambus DRAM** (RDRAM) [@cpu-memory]. Este fue otro diseño que compitió con la SDRAM para convertirse en el siguiente estándar. La RDRAM emplea una arquitectura **serie** rápida (donde los módulos de memoria se encadenan en secuencia), mientras que la SDRAM utiliza una **conexión paralela** más lenta (conectando todos los módulos directamente al controlador de memoria). Cada una tenía sus ventajas e inconvenientes, tanto técnicos como comerciales. Con todo, conviene señalar que, aunque la SDRAM acabó imponiéndose, la RDRAM siguió apareciendo en generaciones posteriores de consolas, con nuevas revisiones del protocolo en cada una.

Por último, la Nintendo 64 implementó la variante **Base RDRAM** [@cpu-rdram_brew], la primera revisión del protocolo.

#### Latencia y velocidad

Aunque las instalaciones RDRAM requerían menos cableado y disfrutaban de frecuencias de reloj más altas que la SDRAM, la latencia de acceso aumentaba proporcionalmente con el número de bancos instalados [@cpu-rdram]. En el caso de la Nintendo 64, el tiempo que mediaba entre iniciar una operación de memoria y encontrar el valor en caché era considerable: alrededor de **640 ns** [@cpu-beyondrsp]. Los ingenieros intentaron aliviarlo dotando a los bancos de memoria de una frecuencia de reloj elevada, de **250 MHz** (aproximadamente 2,6 veces más rápida que la CPU). De este modo, Nintendo afirmó que la RDRAM podía alcanzar transferencias de hasta 500 MB/s al leer o escribir datos **consecutivos** [@cpu-dreamteam].

Como curiosidad, Nintendo eligió los bancos de memoria uPD488170L de NEC para la placa base de la N64 [@cpu-beyondrdram]. Estos chips implementan una tecnología conocida como 'Rambus Signaling Logic', un hiperónimo que engloba numerosas mejoras, entre las que destaca una que duplica la tasa de transferencia [@cpu-data]. Esto puede explicar por qué algunas fuentes citan la velocidad 'efectiva' de la memoria como 500 MHz.

#### Dejando margen para mejoras

Curiosamente, la cantidad de RAM disponible en esta consola **puede ampliarse** instalando el accesorio **Expansion Pak**: una peculiar cajita que añade otros **4,5 MB**. Mientras que este accesorio fue opcional para algunos juegos (y la mayoría ni lo aprovecharon), ciertos títulos como *Donkey Kong 64* y *The Legend of Zelda: Majora's Mask* se diseñaron con la expansión como requisito, y mostraban una pantalla de error si no estaba enchufado.

![El Expansion Pak [@photography-amos], un accesorio opcional vendido por separado (a veces incluido junto al juego que lo requería).](expansion_pak.png){.tabs-nested .active title="Expansión"}

![El Jumper Pak [@photography-amos]. En ausencia del Expansion Pak, este debe estar presente para terminar el bus RDRAM.](jumper_pak.png){.tabs-nested-last title="Relleno"}

Debido a su diseño de extremo a extremo, la RDRAM debe estar **correctamente terminada**; de lo contrario, las señales rebotan de un lado a otro por el bus (un fenómeno bien conocido como **reflexión**). Rambus lo mitigó exigiendo a los usuarios de PC que instalaran los módulos de memoria en pares y rellenaran las ranuras vacías con módulos 'Continuity RIMM' (CRIMM), que actuaban como terminadores. En el caso de esta consola, Nintendo incluyó un terminador llamado **Jumper Pak**, instalado de serie en el hueco del Expansion Pak. El Jumper Pak solo alberga los condensadores y resistencias justos para igualar la impedancia del bus [@cpu-jp_reversed], reduciendo así la reflexión. También sirve para cerrar el bucle de la cadena serie [@cpu-rdram_brew].

Llegados a este punto, cabe preguntarse: ¿qué pasaría si encendiéramos la consola sin ningún *Pak* instalado? Pues bien, los chips de memoria de la placa no funcionarían, la secuencia de arranque fallaría y no saldría ninguna señal de vídeo.

### Gestión de memoria

El VR4300 incluye otro coprocesador conocido como **System Control Coprocessor** (CP0), compuesto por una **Unidad de Gestión de Memoria** (MMU, del inglés 'Memory Management Unit') y un **Translation Lookaside Buffer** (TLB). La MMU controla cómo se organiza la memoria y cómo se gestiona su caché.

Aunque la CPU es capaz de direccionar hasta 4 GB de memoria, la Nintendo 64 dispone de mucha menos, incluso teniendo en cuenta la [E/S mapeada en memoria](nes#memory). Por eso, la MMU aprovecha el limitado espacio de direccionamiento ofreciendo un **mapa de memoria virtual** en el que la memoria física se espeja varias veces. En consecuencia, las ubicaciones de memoria se tratan como **direcciones virtuales** (en contraposición a las 'direcciones físicas'). Además, el TLB permite a los desarrolladores definir mapas de memoria personalizados en algunos espejos sin penalizaciones de rendimiento (significativas).

A primera vista puede parecer redundante, pero cada espejo (denominado **segmento**) está conectado a circuitería distinta (por ejemplo, caché L1, RAM física o regiones mapeadas con TLB), lo que permite a los desarrolladores optimizar el uso eligiendo el segmento más adecuado según las necesidades [@audio-memory_map].

Algunos segmentos se diseñaron para distinguir entre ubicaciones de 'kernel' y de 'usuario' por motivos de seguridad. Sin embargo, la N64 siempre opera en modo 'kernel', lo que convierte al segmento 'kernel cacheado sin TLB' (llamado 'KSEG0') en el más habitual para los juegos.

Por último, la MMU también puede funcionar en modo de 64 bits, donde las direcciones de memoria son de 40 bits. Esto ensancha el espacio de direcciones virtuales hasta aproximadamente 1 TB... ¡aunque dudo mucho que la Nintendo 64 llegue a aprovecharlo!

## Gráficos

Lo que vemos en pantalla lo genera un inmenso chip diseñado por Silicon Graphics llamado **Reality Co-Processor** (RCP), que funciona a **62,5 MHz** [@graphics-rcp2010]. Este paquete encierra *muchísima* circuitería, así que no te preocupes si en algún momento cuesta seguirlo: el subsistema gráfico tiene una arquitectura enormemente compleja.

Este diseño parte de la filosofía de que la GPU no debe ser una 'simple' rasterizadora como [la competencia](playstation#graphics). Al contrario, debe ser capaz de **acelerar los cálculos geométricos** (aliviando la carga de la CPU), y para ello se precisa mayor circuitería.

### Arquitectura

El Reality Co-Processor se divide en tres módulos principales, dos de los cuales están dedicados al procesamiento gráfico:

#### Reality Signal Processor {.tabs .active}

![Arquitectura del Reality Signal Processor (RSP).](_diagrams/rsp.png){.tab-float .no-borders}

También conocido como **RSP**, el Reality Signal Processor es un paquete CPU compuesto por [@graphics-rsp]:

- La **Scalar Unit**: Otra derivada recortada del MIPS R4000. Esta vez sólo implementa un subconjunto del set MIPS III, por lo que carece de muchas funciones de propósito general, como las interrupciones, la extensión de 64 bits, la multiplicación y la división.
- La **Vector Unit**: Un coprocesador que realiza operaciones vectoriales con **32 registros de 128 bits**. Cada registro está dividido en ocho partes para operar ocho vectores de 16 bits a la vez (similar a las instrucciones SIMD en CPUs convencionales). Como se puede ver, este componente hace el trabajo duro de la Scalar Unit (operando además de forma concurrente con ella).
- El **System Control**: Otro coprocesador que proporciona funcionalidad DMA y controla su módulo adyacente, el RDP (explicado en el siguiente apartado).

Para operar el RSP, la CPU almacena en la RAM una serie de comandos llamados **Display list** junto con los datos a manipular. El RSP lee dicha lista y aplica las operaciones necesarias. Las funciones disponibles incluyen transformaciones geométricas (como la proyección en perspectiva), clipping e iluminación.

Puede parecer trivial, pero ¿cómo realiza estas operaciones? Aquí está la parte interesante: a diferencia de sus competidoras ([PlayStation](playstation) y [Sega Saturn](sega-saturn)), **el motor de geometría no está cableado**. En su lugar, el RSP dispone de algo de memoria (4 KB para instrucciones y 4 KB para datos) para almacenar **microcode** [@graphics-rcp2010]: un pequeño programa de no más de 1000 instrucciones que **implementa el pipeline gráfico**. En otras palabras, instruye a la Scalar Unit sobre cómo procesar los datos gráficos. El microcode lo carga la CPU principal en tiempo de ejecución.

Nintendo proporcionó varios microcódigos para elegir y, al igual que [los modos de fondo de la SNES](super-nintendo#graphics), cada uno distribuye los recursos del sistema de forma distinta [@audio_video-microcodes].

Los datos resultantes se transmiten bien a través de un bus dedicado llamado **XBUS**, bien a través de la RAM principal. A lo largo de la vida de la consola, esta elección fue oscilando en función de las restricciones de memoria: la vía XBUS era más rápida, pero requería búferes adicionales en la memoria interna del RSP para almacenar y transferir los nuevos datos. Sin ir más lejos, los primeros programas de microcode como `Fast3D` ofrecían ambas opciones. Sin embargo, el posterior y más rápido `F3DEX` se centró enteramente en la RAM principal. Finalmente, su sucesor, `F3DEX2`, reintrodujo el soporte XBUS una vez resueltos los problemas de uso de memoria.

#### Reality Display Processor {.tab}

![Arquitectura del Reality Display Processor (RDP).](_diagrams/rdp.png){.tab-float .no-borders}

Al terminar de procesar los datos, el RSP empieza a enviar **comandos de rasterización** al siguiente módulo —el **Reality Display Processor** (RDP)— para dibujar el frame.

El RDP es otro procesador (esta vez con funcionalidad fija) que incluye múltiples motores para rasterizar vectores, mapear texturas sobre polígonos, mezclar colores y componer el nuevo frame.

Puede procesar **triángulos** o **rectángulos** como primitivos; los últimos son útiles para dibujar sprites. El pipeline de rasterización del RDP contiene los siguientes bloques [@graphics-rdp]:

- Un **Rasteriser**: Convierte primitivos (formados por vértices) en píxeles.
- Una **Texture Unit**: Procesa texturas usando **4 KB de memoria dedicada** (llamada 'TMEM'), lo que permite usar hasta ocho tiles para el texturizado. Puede realizar las siguientes operaciones sobre ellas:
  - **Bilinear filtering**: Mapea la textura 2D seleccionada sobre la figura 3D y la suaviza para evitar zonas pixeladas (provocadas por el sobremuestreo).
    - Un filtro 'completo' requeriría cuatro puntos para la interpolación; sin embargo, esta consola solo usa tres (**interpolación triangular**), lo que genera algunas anomalías. Por eso ciertas texturas deben 'adaptarse' previamente.
  - **Mip-Mapping**: Selecciona automáticamente una versión reducida de la textura según su **nivel de detalle**. Esto evita calcular texturas grandes que se ven lejos de la cámara y previene el aliasing (consecuencia del submuestreo).
    - Si está activado, el RDP mapea las texturas con **trilinear filtering**. Este nuevo algoritmo también interpola entre mipmaps para suavizar los cambios bruscos entre niveles de detalle.
  - **Perspective correction**: El algoritmo elegido para mapear texturas sobre triángulos. A diferencia de [otros algoritmos de mapeo inverso](playstation#graphics), este tiene en cuenta el valor de profundidad de cada primitivo, logrando resultados más fieles.
- Un **Colour Combiner**: Mezcla e interpola múltiples capas de colores. Como la *nueva generación* de la [unidad de matemáticas de color](super-nintendo#more-colour-magic), realiza sumas, multiplicaciones y restas de valores de color.
- Un **Blender**: Mezcla píxeles con el frame buffer actual para aplicar translucidez, antialiasing, niebla y dithering. También gestiona el z-buffering, que explico más adelante.
- Una **Memory Interface**: Utilizada por los bloques anteriores para leer y escribir en el frame buffer actual de la RAM y/o llenar el TMEM.

El RDP ofrece **cuatro modos de operación**, cada uno de los cuales combina estos bloques de forma distinta para optimizar tareas concretas.

Como este módulo actualiza constantemente el frame buffer, interactúa con la RAM principal de forma peculiar. ¿Recuerdas el inusual 9.º bit? Ese bit se usa como metadato para los cálculos relacionados con el frame buffer (z-buffering y antialiasing) y solo lo entiende la Memory Interface.

#### Pasos restantes {.tabs-close}

El frame resultante debe enviarse al **Video Encoder** para mostrarse en pantalla. Para ello son esenciales el **DMA** y el componente **Video Interface**.

Las capacidades máximas teóricas son una **profundidad de color de 24 bits** (16,8 millones de colores) y una resolución de **640×480 píxeles** (o 720×576 en la región PAL) [@graphics-video_interface]. Digo 'teóricas' porque aprovechar al máximo estas capacidades es muy costoso en recursos, por lo que los programadores suelen optar por especificaciones más modestas para liberar recursos para otros servicios.

### Demostración rápida

Pongamos en perspectiva todo lo anterior. Para ello, tomaré prestado el *Super Mario 64* de Nintendo como caso de estudio para mostrar, a grandes rasgos, cómo se compone un frame básico. Eso sí, ten en cuenta que en la práctica los juegos pueden usar búferes adicionales para componer frames más ricos.

#### Procesamiento de vértices {.tabs .active}

![Vista primitiva de nuestra escena. Para ahorrar polígonos, algunos personajes se modelan con sprites (cuadriláteros)](mario/wireframe.jpg){.tab-float}

Para empezar, los modelos 3D y demás recursos gráficos residen en la ROM del cartucho. Sin embargo, para mantener un ancho de banda constante, primero hay que copiarlos a la RAM. En algunos casos los datos vienen precomprimidos en el cartucho, por lo que la CPU tiene que descomprimirlos antes de usarlos.

Una vez hecho esto, toca montar una escena con nuestros modelos. La CPU *podría* encargarse de todo el pipeline gráfico por sí sola, pero eso llevaría *una eternidad*, así que muchas tareas se delegan al RCP. La CPU se limitará a enviar órdenes al RCP, lo que se lleva a cabo en los siguientes pasos:

1. Componer las **Display Lists** con las operaciones a ejecutar por el RSP, y almacenarlas en la RAM. La estructura de las Display Lists viene dictada por el microcode elegido [@graphics-rcp_structures].
2. Indicar al RSP dónde están las Display Lists.
3. Cargar el microcode elegido en el RSP, poniendo en marcha la Scalar Unit.

A continuación, el RSP empieza a trabajar en el primer lote de tareas y transmite su salida al RDP en forma de comandos de rasterización.

#### Procesamiento de píxeles {.tab}

![Frame renderizado (_¡Tachán!_).](mario/result.png){.tab-float}

Hasta aquí hemos procesado nuestros datos y aplicado algunos efectos, pero todavía queda pendiente:

- Rasterizar vectores, aplicar texturas y otros efectos.
- Mostrar el frame buffer en pantalla.

Como cabe esperar, estas tareas las realiza el RDP. Además, para que funcione, las texturas deben transferirse a los 4 KB de Texture Memory mediante DMA.

A diferencia del procesador anterior, el pipeline del RDP es fijo, pero podemos elegir el modo de operación óptimo según la carga de trabajo, el rendimiento y las tareas específicas necesarias. Por ejemplo, ordenar al RDP que renderice una sola textura es más rápido que combinar dos.

Una vez que el RDP termina de procesar los datos, este escribe el bitmap final sobre la zona del frame buffer (en la RAM). Después, la CPU debe transferir el nuevo frame a la **Video Interface** (VI), preferiblemente usando DMA, que a su vez lo envía al **Video Encoder** para su visualización [@graphics-video_interface].

### Diseños {.tabs-close}

Aquí tienes algunos ejemplos de personajes clásicos en 2D de la [Super Nintendo](super-nintendo) que fueron rediseñados para la era 3D. Fíjate en el detalle de las texturas en comparación con los modelos de otras consolas de la misma generación.

![The Legend of Zelda: Ocarina of Time (1998).<br>704 triángulos.](link_ocarina_64){.toleft model3d="true" hardcover_latex_width="68%" paperback_latex_width="62%"}

![Kirby 64: The Crystal Shards (2000).<br>516 triángulos.](kirby_cristals_64){.toright model3d="true"}

### Una solución moderna para la determinación de superficies visibles

Si has leído sobre las consolas anteriores, habrás encontrado el eterno problema de la [visibilidad de superficies](sega-saturn#an-introduction-to-the-visibility-problem) y puede que pienses que el ordenamiento de polígonos es la única salida. Pues bien, por primera vez en esta serie, la GPU incorpora una solución por hardware llamada **Z-buffering**. En pocas palabras, el RDP reserva un búfer adicional (llamado **Z-buffer**) en memoria. Tiene las mismas dimensiones que un frame buffer, pero en lugar de almacenar valores RGB, cada entrada contiene la profundidad (valor Z) del píxel más cercano relativo a la cámara.

Tras rasterizar los vectores, el valor Z del nuevo píxel se compara con el valor correspondiente en el z-buffer. Si el nuevo píxel tiene un valor Z menor, esto significa que el nuevo píxel se sitúa delante del píxel anterior, por lo que se aplica al frame buffer y se actualiza el z-buffer. En caso contrario, el píxel se descarta.

En general, se trata de una mejora muy bienvenida: los programadores ya no tienen que preocuparse por implementar métodos de [ordenamiento de polígonos por software](playstation#tab-3-2-visibility-approach), que consumen una cantidad considerable de recursos de la CPU. Sin embargo, el z-buffer no evita procesar geometría innecesaria (ya sea descartada o sobredibujada, en ambos casos se derrochan recursos). Para ello, los motores de juego pueden optar por incluir un algoritmo de **occlusion culling** que descarte la geometría invisible lo antes posible.

### Secretos y limitaciones

Es evidente que SGI invirtió mucha tecnología en este sistema. Sin embargo, la Nintendo 64 era una consola doméstica y, como tal, debía mantener unos costes bajos. Algunas decisiones difíciles se convirtieron en auténticos quebraderos de cabeza para los programadores:

#### Atascos en el pipeline {.tabs .active}

La eliminación de los load delay slots en MIPS II, en favor de paradas automáticas del pipeline, tuvo la ventaja de eliminar la necesidad de [instrucciones de relleno](playstation#a-core-philosophy). Sin embargo, este alejamiento de la filosofía original de MIPS también abrió la puerta a nuevos cuellos de botella. Debido al gran número de componentes y operaciones en el pipeline gráfico, el RCP resultó ser muy susceptible a **atascos excesivos**: una situación indeseable en la que los subcomponentes permanecen inactivos durante períodos considerables porque los datos necesarios llegan con retraso al final del pipeline.

Esto se traduce inevitablemente en una degradación del rendimiento, y depende del programador evitarlo. Para ayudar a mitigarlo, las CPUs MIPS llevan tiempo ofreciendo el **pipeline bypassing**: un mecanismo que permite ejecutar instrucciones similares a mayor velocidad saltándose algunas etapas de ejecución que pueden omitirse [@cpu-nec].

Por ejemplo, si la CPU tiene que calcular instrucciones `ADD` secuenciales que dependen unas de otras, no es necesario volcar el resultado a un registro y volver a leerlo tras cada operación. En su lugar, la CPU puede propagar los valores a través del datapath y hacer la escritura definitiva solo cuando se haya completado el último `ADD`.

#### Memoria de texturas {.tab}

El RDP depende de 4 KB de Texture Memory (TMEM) como única fuente para cargar texturas. Por desgracia, en la práctica 4 KB resultaron insuficientes para texturas de alta resolución. Además, cuando se activa el mipmapping, la memoria disponible se reduce a la mitad.

En consecuencia, algunos juegos recurrieron a colores sólidos con sombreado Gouraud (*Super Mario 64* es el ejemplo más conocido), mientras que otros apostaron por texturas precalculadas (especialmente cuando había que combinar varias capas).

### La salida de vídeo universal {.tabs-close}

Nintendo continuó usando la salida 'universal' [Multi Out](super-nintendo#a-convenient-video-out) de su predecesora, aunque con una mala noticia: **¡ya no transmite la señal RGB!** A mi parecer, otra medida de ahorro de costes, dado que el RGB tampoco se aprovechó demasiado en la consola anterior.

La buena noticia es que, en las primeras revisiones de la Nintendo 64, los tres canales RGB pueden recuperarse soldando algunos cables e instalando un amplificador de señal económico. Esto es posible porque el conversor digital-analógico de vídeo (DAC) todavía transmite una señal RGB al codificador de vídeo [@graphics-video_dac]. Sin embargo, las revisiones posteriores de la placa base fusionaron ambos chips, por lo que la única alternativa viable es puentear por completo el DAC de vídeo y el codificador con circuitería personalizada que exponga las señales RGB.

## Audio

Antes de entrar en materia, definamos los dos extremos del subsistema de audio de la N64:

- El punto de partida es la ROM del cartucho, que contiene datos que solo la CPU puede interpretar.
- El punto final es el **Conversor Digital-Analógico** (DAC, del inglés 'Digital-to-Analog Converter'), que solo entiende datos de *waveform* [@audio-programming].

Dicho esto, ¿cómo conectamos ambos extremos? Las consolas normalmente incluyen un chip de audio dedicado que se encarga de ello. Por desgracia, la Nintendo 64 **no tiene dicho chip**, así que esta tarea recae en los siguientes componentes:

- La **CPU principal**: Transfiere los datos de audio de la ROM del juego a la RAM, y luego compone **Audio Lists** para que las gestione el RSP.
- El **RSP**: Usando microcode adicional, interpreta las Audio Lists almacenadas previamente en la RAM y realiza las operaciones necesarias sobre los datos de audio, que pueden incluir:
  - Descomprimir **muestras ADPCM** y aplicar efectos.
  - Secuenciar y mezclar **datos MIDI** usando **bancos de audio** almacenados en la RAM.

Los datos resultantes son, como era de esperar, datos de waveform. Estos se envían al bloque **Audio Interface** (AI), que los transfiere al conversor digital-analógico [@audio-audio_interface]. Al tratarse de un sistema estéreo, la waveform resultante contiene dos canales, cada uno con una resolución de 16 bits.

![Resumen de cómo suele implementarse el pipeline de audio.](_diagrams/audio.png){.no-borders}

### Repertorio musical {.interactive-only}

Es hora de echar un vistazo a las bandas sonoras creadas para la N64. Hay demasiados buenos ejemplos para mencionarlos todos en este artículo, así que os dejo algunos que me llamaron especialmente la atención:

![The Legend of Zelda: Majora's Mask (2000).<br>La música de este juego está íntimamente ligada a su inquietante atmósfera.](observatory){.toleft video="true" .interactive-only}

![Bomberman Hero (1998).<br>Este juego tiene una banda sonora única y muy lograda de estilo house.](redial){.toright video="true" .interactive-only}

### Secretos y limitaciones

Por este diseño, las limitaciones vienen marcadas por la carga de trabajo global:

- La frecuencia de muestreo puede llegar hasta **44,1 kHz**, pero usar la tasa máxima consume demasiados ciclos de CPU.
- No hay un límite estricto en el número de canales; todo depende de cuántos sea capaz de mezclar el RSP (habitualmente entre 16 y 24 al procesar ADPCM, o aproximadamente 100 con PCM).
- La memoria es otro factor a tener en cuenta. Mientras que la competencia disponía de soportes más grandes (como el CD-ROM) y memoria de audio dedicada, los cartuchos de Nintendo 64 contienen muchos menos datos (por no hablar del espacio reservado para la música) y deben compartir la RAM principal con el resto de componentes.

Por estas razones, los jugadores pueden notar que los ports de N64 tienen música de menor calidad o que se repite con frecuencia. Un recurso habitual para paliar esto era implementar un secuenciador musical que generase muestras en tiempo real usando un conjunto de sonidos preestablecido (similar a la música MIDI).

## Sistema Operativo

Al igual que en la PS1 y la Saturn, los juegos de N64 se programan directamente sobre el hardware. No hay rutinas de BIOS disponibles para simplificar las operaciones de hardware, así que, en su lugar, **los juegos incorporan un pequeño sistema operativo** que ofrece un buen nivel de abstracción para gestionar eficientemente la CPU, la GPU y la E/S.

No es el *sistema operativo de escritorio* convencional que uno podría imaginar: es tan solo un microkernel con la menor huella posible, que proporciona la siguiente funcionalidad [@operating_system-schd]:

- Multithreading mediante paso de mensajes.
- Scheduling y preemption.
- Acceso simplificado a registros y E/S.

En definitiva, estas funciones son esenciales para coordinar de forma eficiente las tareas de audio, vídeo y lógica del juego, todas las cuales deben ejecutarse de forma concurrente.

El kernel se incluye automáticamente al usar las librerías de Nintendo. Además, si los programadores deciden prescindir de alguna parte de la librería, la porción correspondiente del kernel se elimina para no desperdiciar espacio en el cartucho.

### Proceso de arranque

A diferencia de los sistemas anteriores basados en cartuchos, la Nintendo 64 sigue un sofisticado proceso de arranque para preparar todo el hardware antes de que se ejecute el juego. Este proceso comienza en cuanto el usuario enciende la consola y es muy similar al de sus contemporáneas basadas en CD que incluían una [BIOS](playstation#operating-system) o una [IPL](sega-saturn#operating-system).

Estas rutinas también se denominan **Initial Program Loader** (IPL) y funcionan así [@operating_system-ipl] [@operating_system-ipl_decomp]:

1. El usuario enciende la consola.
2. El **PIF-NUS** (un chip independiente en la placa base) mantiene la CPU principal en un bucle de reinicio infinito hasta validar el chip CIC del cartucho de juego.
    - El PIF-NUS y el chip CIC se explican con más detalle en las secciones de E/S y antipiratería, respectivamente.
2. Si el proceso de verificación concluye satisfactoriamente, la CPU inicia la ejecución en `0xBFC00000`. Esta dirección apunta a una **ROM interna** del PIF-NUS, concretamente a la primera etapa de arranque, llamada **IPL1**.
3. IPL1 inicializa parte del hardware (registros de la CPU, la interfaz paralela y el RCP), copia la siguiente etapa (**IPL2**) de la ROM interna a la memoria del RSP para una ejecución más rápida y redirige la ejecución allí.
4. IPL2 copia los primeros cuatro bytes de la ROM del juego a la memoria del RSP para ajustar los tiempos del bus de la ROM. A continuación, copia otros 4 KB de la cabecera de la ROM y envía un checksum al PIF-NUS, que lo verifica usando el chip CIC del cartucho. Si la verificación falla, el PIF-NUS interrumpe la CPU indefinidamente. En caso contrario, la CPU continúa la ejecución en esos 4 KB, que contienen la siguiente etapa de arranque, llamada **IPL3**.
5. IPL3 inicializa la RDRAM, la caché de la CPU y el Expansion Pak (si está presente). Después, copia 1 MB de la ROM del juego a la RDRAM, calcula su checksum y lo compara con un valor precomputado almacenado en la cabecera de la ROM. Finalmente, la CPU salta al código del juego en la RDRAM.

Como IPL3 reside en el cartucho del juego, no todos los juegos incluyen el mismo código. Además, el checksum de IPL3 almacenado en el CIC está codificado de forma fija [@operating_system-ipl]. Por eso, el chip CIC y las variantes de IPL3 del cartucho van emparejados y no pueden intercambiarse con otros modelos.

## E/S

Como ya sabemos, la E/S no está conectada directamente a la CPU, así que el tercer módulo del RCP —que hasta ahora no había mencionado— actúa como **interfaz de E/S**. Este bloque gestiona la comunicación con la CPU, los mandos, el cartucho de juego y los DAC de audio/vídeo.

### Accesorios

Además de su forma poco convencional, el mando de Nintendo 64 incluye un conector para enchufar accesorios. Los ejemplos comerciales más destacados son:

- El **Controller Pak**: Un soporte de almacenamiento, similar a la [Memory Card](playstation#front-ports) de Sony, usado para guardar partidas y compartirlas entre consolas.
- El **Rumble Pak**: Contiene un pequeño motor que proporciona vibración háptica, útil para 'sumergir' al jugador en los juegos compatibles.
- El **Transfer Pak**: Incluye una ranura para conectar un cartucho de Game Boy o Game Boy Color ([Game Pak](game-boy#games)). Esto abrió la posibilidad de ejecutar juegos de Game Boy con la ayuda de un emulador, y/o transferir su contenido.

![El Controller Pak [@photography-amos].](accessories/controller_pak.webp){.tabs-nested .active title="Controller"}

![El Rumble Pak [@photography-amos].](accessories/rumble_pak.webp){.tab-nested title="Rumble"}

![El Transfer Pak [@photography-amos].](accessories/transfer_pak.webp){.tabs-nested-last title="Transfer"}

El **PIF-NUS**, un bloque algo misterioso que también se encarga de la seguridad, gestiona todos los accesorios conectados al mando. El RCP se comunica con el PIF mediante un **bus serie** [@io-serial_interface].

## Juegos

Nintendo mantuvo el cartucho como soporte de almacenamiento en lugar de adoptar el disco óptico. Como consecuencia, los juegos disfrutaron de mayores anchos de banda (una media de 5 MB/s) aunque resultaban más caros de fabricar. El cartucho más grande del mercado tenía una capacidad de 64 MB.

Dentro de los cartuchos, los fabricantes solían incluir memoria adicional (en forma de **EEPROM**, **flash** o **SRAM** con batería) para guardar partidas. Sin embargo, esto fue perdiendo protagonismo a medida que ciertos accesorios (como el Controller Pak) ofrecían almacenamiento alternativo.

Los cartuchos se comunican con el RCP mediante un bus dedicado de 16 bits conocido como **Parallel Bus** (PBUS) o 'Parallel Interface' (PI) [@games-peripheral_interface].

### Kit de desarrollo

En líneas generales, el desarrollo se realizó principalmente en **C** y **ensamblador**, siendo el ensamblador especialmente necesario para sacarle el máximo partido al hardware. Aunque hemos visto que el sistema admite operaciones de 64 bits, las nuevas instrucciones se usaron raramente, ya que en la práctica las instrucciones de 32 bits resultaban más rápidas (dado que la R4300i/VR4300 tiene un bus de datos de 32 bits).

Las librerías del SDK oficial incluían varias capas de abstracción para comandar el RCP. Por ejemplo, las estructuras en C como la **Graphics Binary Interface** (GBI) se diseñaron para facilitar el ensamblado de Display Lists [@games-gbi]. Lo mismo se aplica a las funciones de audio, cuya estructura se llamaba **Audio Binary Interface** (ABI).

En cuanto al desarrollo de microcode, Nintendo proporcionó varios programas preescritos para elegir. Sin embargo, si los desarrolladores querían personalizarlos, se encontraban ante una tarea ardua: el set de instrucciones de la Scalar Unit no estaba documentado inicialmente. Con el tiempo, Nintendo y SGI cambiaron de postura y publicaron documentación y herramientas para la programación de microcode [@games-gbi].

![Una SGI Indy que encontré en el Centre for Computing History (Cambridge, Reino Unido) cuando visité en agosto de 2024. A modo de comparación, este ordenador alberga una CPU MIPS R4400, la sucesora mejorada del R4000 (en resumidas cuentas, años luz por delante del VR4300).](sgi_indy.webp)

El hardware de desarrollo incluía estaciones de trabajo suministradas por SGI [@games-devkit], como las máquinas **Indy** equipadas con una placa hija adicional llamada **U64**, que contiene el hardware y la E/S de la consola de venta al público. También había herramientas disponibles para ordenadores Windows [@games-u64].

Tampoco faltaban las herramientas de terceros: cartuchos especiales con un largo cable plano que se conectaba a la estación de trabajo. Estos cartuchos encajaban en una Nintendo 64 normal, pero incorporaban circuitería interna para redirigir las peticiones de lectura de la consola a la RAM de la estación de trabajo. El proceso de despliegue y depuración consistía en transferir una copia del juego a esa RAM, de modo que, al encender la consola, empezase a leer desde ahí.

### El soporte alternativo

Curiosamente, el PBUS se ramifica hacia un conector secundario en la parte inferior de la placa base de la Nintendo 64. Estaba previsto para la **Nintendo 64 Disk Drive** (64DD), un periférico que funcionaba como un 'piso extra' bajo la consola y albergaba un lector de disco magnético propietario. Sus discos ofrecían hasta 64 MB de capacidad.

Aunque la 64DD solo se lanzó en Japón, abrió la puerta a un soporte alternativo (y más económico) para distribuir juegos.

![La Nintendo 64 Disk Drive [@photography-amos].<br>Lanzada el 01/12/1999 en Japón.](64dd/module.png){.open-float .tabs-nested .tab-float .active title="Módulo"}

![La 64DD conectada a la consola [@photography-amos].](64dd/attached.png){.tabs-nested-last title="Conectada"}

El soporte magnético es más lento que los cartuchos, con velocidades de transferencia de hasta 1 MB/s, aunque todavía más rápido que los lectores de CD-ROM a 4x. Los discos son de doble cara y operan a **Velocidad Angular Constante** (CAV, del inglés 'Constant Angular Velocity'), como el posterior [miniDVD](gamecube#medium). El área legible más pequeña se denomina *bloque* y corresponde a la mitad de un círculo concéntrico en la superficie del disco.

Cabe destacar que el lector **no incluye memoria búfer**, por lo que los datos se transmiten directamente a la RDRAM para su ejecución. Para lidiar con esa mayor demanda de memoria, Nintendo incluyó el Expansion Pak junto a la 64DD, estandarizando de paso el espacio de RAM ampliado para que todos los juegos de 64DD pudieran aprovecharlo de forma fiable.

{.close-float}

Además, parte del disco es reescribible para guardar partidas. El espacio disponible para escritura varía según el tipo de disco (Nintendo ofreció siete tipos).

En cuanto al software, los datos del juego se estructuran con un sistema de archivos llamado 'Multi File System' (MFS), incluido por Nintendo en su SDK [@games-rr_sdk]. Los juegos podían acceder a los datos del disco a través del sistema de archivos o directamente a nivel de bloque.

La Disk Drive también alberga una ROM interna, denominada 'DDROM', que almacena el código ejecutado por la N64 durante el arranque del disco y muestra la animación de bienvenida, añadiendo de hecho una nueva etapa IPL sobre el proceso de arranque tradicional. La ROM también almacena fuentes (latinas y kanji) y algunos sonidos.

## Antipiratería / Region Lock

El sistema antipiratería es una continuación del modelo [CIC de la Super Nintendo](super-nintendo#anti-piracy-region-lock). Como ya sabéis, la detección de copias no autorizadas y el region lock se hacían valer gracias al chip CIC (presente en cada cartucho *autorizado*) [@anti_piracy-cic]. La Nintendo 64 perfeccionó este sistema asignando variantes específicas del chip CIC a juegos concretos, garantizando así que cada cartucho no fuera una falsificación ni contuviese un clon del CIC.

Para ello, el PIF-NUS realiza verificaciones mediante checksum tanto al arranque como durante el juego para supervisar el CIC instalado en el cartucho.

Si el PIF determina que el cartucho no es válido, fuerza a la consola a un estado de congelación permanente.

En cuanto al region lock, se llevó a cabo alterando ligeramente la forma del cartucho según la región, de modo que los usuarios no pudieran insertar físicamente el juego en una consola de una región diferente.

En términos generales, la piratería no fue un problema significativo dada la complejidad y el coste inherentes a la réplica de cartuchos, aunque los juegos costaban el triple que un título en CD.

### Puertos sin aprovechar

Por absurdo que parezca, Nintendo dejó una puerta abierta: el **puerto del Disk Drive**.

![El Doctor V64 conectado a la consola [@photography-amos].](v64/attached.png){.open-float .tabs-nested .tab-float .active title="Conectado"}

![La parte trasera del V64 [@photography-amos], con algunas conexiones A/V interesantes.](v64/back.png){.tabs-nested-last title="Trasera"}

Varias empresas desentrañaron la interfaz mediante ingeniería inversa para desarrollar sus propios periféricos, y algunos de los productos resultantes se convirtieron en un quebradero de cabeza para Nintendo en materia de piratería.

El caso más sonado fue sin duda el **Doctor v64**, un dispositivo con la misma forma que el Disk Drive pero equipado con una unidad de CD-ROM.

Este periférico podía volcar el contenido de un cartucho a un CD, y también era posible lo contrario: leer archivos ROM desde un CD.

{.close-float}

### Emulación

De pequeño solía jugar a algunos juegos de N64 en una máquina Pentium II usando un emulador. No funcionaba *tan mal*, aunque ahora me sorprende que aquel viejo ordenador pudiera emular una máquina de 64 bits sin despeinarse, sobre todo teniendo en cuenta que, entre otras cosas, mi PC apenas tenía RAM suficiente para mantener viva la tarjeta gráfica integrada.

La clave está en que, aunque reproducir la arquitectura de esta consola puede ser complejo, las rutinas de microcode arrojan indicios sobre lo que la consola está intentando hacer, y como los emuladores *no tienen por qué ser precisos a nivel de ciclo*, pueden aplicar suficientes optimizaciones para ganar rendimiento a costa de precisión [@games-frame_emulation].

Otro factor es el set de instrucciones de 64 bits: como los juegos raramente las aprovecharon, el rendimiento de emulación apenas se resentía al ejecutarse en un ordenador de 32 bits.

## Eso es todo, amigos

![Mi N64 *compartida* en casa de un amigo. Mientras que yo solo quería la consola para el artículo, mi amigo llevaba años soñando con tener una 64DD. Así que compramos juntos un set japonés completo (y bastante caro) para repartir el coste. Después le instalé el mod N64RGB para poder conectarla a una tele moderna. El resultado es una bonita configuración de entretenimiento... ¡y todo un tema de conversación!](n64.jpeg)

Debo reconocer que este artículo es probablemente el más largo que he escrito, pero espero que te haya resultado ameno.

Seguramente me tome los próximos días para ordenar algunas cosas en la web en lugar de ponerme a escribir el siguiente artículo.

¡Hasta la próxima!  
Rodrigo
