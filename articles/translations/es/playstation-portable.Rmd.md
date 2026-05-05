---
short_title: Arquitectura de la PlayStation Portable (PSP)
long_title: Arquitectura de la PlayStation Portable (PSP)
name: PlayStation Portable
subtitle: "'Portátil' no implica 'limitada'"
date: 2021-04-21
release_date: 2004-12-12
cover: "psp"
javascript: ['audioswitcher', 'threejs']
generation: 7
top_tabs:
  Models:
    - title: "Original"
      caption: "La PSP original (modelo 1000, también conocida como 'Fat').<br>Lanzada el 12/12/2004 en Japón, el 24/03/2005 en América y el 01/09/2005 en Europa."
      active: true
      file: original
      latex_height: 87
    - title: "Slim"
      caption: "La PSP actualizada (modelo 2000, también conocida como 'Slim').<br>Lanzada el 05/09/2007 en Europa, el 06/09/2007 en América y el 20/09/2007 en Japón."
      file: slim
      latex_height: 95
    - title: "Slim & Lite"
      caption: "La PSP aún más actualizada (modelo 3000, también conocida como 'Brite'), edición plateada.<br>Lanzada el 14/10/2008 en América, el 16/10/2008 en Japón y el 17/10/2008 en Europa."
      file: slim-lite
      latex_height: 95
    - title: "Go"
      caption: "La PSP rediseñada (modelo 'N1000', también conocida como 'Go').<br>Lanzada el 01/10/2009 en América y Europa, y el 01/11/2009 en Japón."
      file: go
  Motherboard:
    caption: "Una placa base increíblemente pequeña... y sin embargo capaz de tanto.<br>Esos zigzags entre la CPU y la memoria intentan igualar la longitud de cada bus para que todas las señales lleguen al mismo tiempo. ¡Esto dice mucho de lo rápida que se ha vuelto la tecnología!<br>A partir del modelo 'Slim', el Tachyon y la 'NAND + SDRAM' pasaron a integrarse en un único chip."
    latex_height: 90
    marked_latex_height: 90
  Diagram:
    caption: "A partir del modelo 'Slim', el tamaño de la NAND y la SDRAM se duplicó (hasta 64 MB cada una)."
    paperback_latex_height: 90
    hardcover_latex_height: 95

# Historical
aliases: [/writings/consoles/psp-private/]
---

## Una breve introducción

Lanzada tan solo un mes después de la [Nintendo DS](nintendo-ds), la primera incursión de Sony en el mercado portátil demostró que la 'innovación horizontal' no es la única vía para triunfar en el mercado de las consolas portátiles.

Este artículo está dedicado a cualquiera que quiera entender, sin rodeos, cómo funciona la PSP. No es un texto breve, pero espero que al llegar al final seas capaz de comprender los pilares de esta consola: su lógica de diseño, la elección de CPU, el pipeline de la GPU, el sistema de seguridad, etc.

## {.supporting-imagery}

## La CPU principal

Al igual que Nintendo, Sony diseñó un Sistema en Chip (SoC, del inglés *System-on-Chip*) *extraordinariamente compacto* que alberga la mayoría de los componentes de los que hablaremos a lo largo de este artículo. Entre ellos se encuentra la **CPU principal**, encargada de ejecutar los juegos y demás programas (a diferencia de las otras CPU, de las que hablaremos a su debido tiempo). El SoC recibe el nombre de **Tachyon**, elegido por la propia Sony.

![El chip Tachyon en el modelo original de PSP.](tachyon.jpg){latex_width="90%"}

La CPU principal es un diseño propio que [mantiene la tradición](playstation-2#cpu) de recurrir a la tecnología **MIPS**. Ahora bien, ya han pasado cuatro años desde el lanzamiento de la PlayStation 2, así que cabe preguntarse: ¿en qué situación se encontraba MIPS por aquel entonces?

### MIPS tras el cambio de siglo

Me temo que los años dorados de MIPS quedaron en los 90, y nada indica que vayan a repetirse en un futuro próximo. Su empresa matriz, SGI, había perdido terreno frente a las estaciones de trabajo x86 de bajo coste y atravesaba graves dificultades económicas. Así que, en 1999, vendió su participación mayoritaria en MIPS [@cpu-foremski]. Con ello, MIPS volvió a ser una empresa independiente, aunque la competencia era mucho más feroz que en sus primeros tiempos.

La primera decisión de la compañía fue reconocer que las CPU MIPS ya no podían competir en el ámbito del alto rendimiento. En consecuencia, reorientó su enfoque hacia la **informática de bajo consumo**, disputando el terreno directamente a [ARM](game-boy-advance#the-cambridge-miracle) y su [abrumadora popularidad](nintendo-ds#arms-new-territories).

Para ordenar su fragmentada línea de CPU, MIPS la consolidó en torno a **tres sets de instrucciones** [@cpu-modern_mips]:

- **MIPS32**: Una actualización de [MIPS II](playstation#the-offering) (la última ISA exclusivamente de 32 bits), que incorpora funcionalidades tomadas de arquitecturas posteriores sin abandonar el límite de 32 bits.
- **MIPS64**: La continuación de MIPS V, una [ISA de 64 bits](nintendo-64#cpu) y la última antes de la renovación. MIPS V fue un superconjunto de todas las ISA anteriores, al igual que sus predecesoras.
- **microMIPS**: Disponible en las variantes 'microMIPS32' y 'microMIPS64', son superconjuntos de MIPS32 y MIPS64, respectivamente, con un grupo adicional de instrucciones de 16 bits (al estilo de [Thumb](game-boy-advance#tab-2-3-squeezing-performance)). Sin embargo, microMIPS no es compatible a nivel binario con las otras ISA, por lo que las bases de código heredadas debían recompilarse para ejecutarse sobre microMIPS.

La gran diferencia fue que estos sets de instrucciones se desarrollarían de forma coordinada, en lugar de que MIPS abandonase la arquitectura anterior en cuanto llegase la siguiente. Las nuevas revisiones se identificarían con el sufijo 'Release x', como 'MIPS32 Release 6' o 'MIPS32R6', que es la última revisión hasta la fecha.

Junto con las ISA, MIPS también comenzó a comercializar una nueva línea de núcleos IP. Por ejemplo, el diseño **MIPS32 4k** implementaba la **ISA MIPS32 R2**, estaba disponible en distintas variantes y ofrecía amplias posibilidades de personalización.

Volviendo al tema, Sony adquirió presumiblemente una licencia del MIPS32 4k y lo personalizó ampliando la ISA e incorporando coprocesadores específicos [@cpu-gcc_patch]. El fruto de este trabajo es **Allegrex**, que funciona a una velocidad variable entre **33 MHz y 333 MHz**.

### La nueva CPU portátil

Allegrex es un núcleo completo de 32 bits con las siguientes características [@cpu-hitmen] [@cpu-naked]:

- Una **ISA MIPS personalizada**: combina instrucciones de MIPS32R2 con instrucciones exclusivas para **operaciones aritméticas** (multiplicación, resta, mín/máx, desplazamientos de bits) y **control de interrupciones**. Estas se denominan 'Allegrex Extended Instructions'.
- Un **bus de direcciones de 32 bits**: esta CPU puede direccionar hasta 4 GB de memoria.
- **32 registros de uso general**: todos almacenan valores de 32 bits; dos de ellos (el registro cero y el registro de enlace) están reservados para usos especiales. A estas alturas, esto no debería sorprender.
- Un **pipeline de 7 etapas**: una más que su [hermana de sobremesa](playstation-2#a-special-order-for-sony).
- **32 KB de caché L1**: de los cuales 16 KB son para instrucciones y 16 KB para datos.
- Un **write-back buffer de caché**: la CPU puede escribir sobre la caché dando por sentado que la RAM ya ha sido actualizada. Mientras tanto, la caché se encarga de actualizar la memoria en cuanto su búfer interno se llena.
  - Este diseño acelera las escrituras en memoria, pero no funciona de inmediato en sistemas multiprocesador como esta consola [@cpu-coherency]. Por ello, los desarrolladores debían vaciar manualmente el búfer cuando otros componentes dependían de los nuevos valores en memoria.

En general, esto supone una propuesta más competitiva que la [competencia basada en ARM9](nintendo-ds#cpu), aunque aún no hemos terminado: queda revisar los coprocesadores que _cooperan_ junto al núcleo.

### Coprocesadores

Como en cualquier CPU MIPS, Allegrex dispone de tres ranuras para coprocesadores. Sony incorporó tres [@cpu-naked]:

- El **coprocesador de control del sistema** como 'CP0': un [componente indispensable](playstation#tab-2-1-system-control-coprocessor) en toda CPU MIPS. Este componente es el que habilita la protección de memoria, la coherencia de caché y el control de interrupciones en el propio núcleo [@cpu-mips_4k].
- Una **Unidad de Punto Flotante** (FPU, del inglés *Floating Point Unit*) como 'CP1': acelera las operaciones aritméticas con números decimales de 32 bits (conforme al estándar IEEE 754). Dispone de **treinta y dos registros de 32 bits** y un **pipeline independiente de 8 etapas**, lo que añade mayor paralelismo a la CPU principal.
- Una **Vector Floating Point Unit** (VFPU) como 'CP2': un coprocesador diseñado para **operaciones vectoriales**, similar a un procesador SIMD tradicional. En comparación con la FPU, ofrece **128 registros de 32 bits**, se basa en un set de instrucciones propietario e implementa un pipeline de longitud variable. La VFPU opera con valores en coma flotante de hasta 32 bits, que pueden agruparse en un vector de 4 componentes o incluso en una **matriz 4×4**. Esto resulta muy útil para calcular distintos tipos de operaciones geométricas sin necesidad de reorganizar los datos.
  - Su eficiencia proviene de su bus de datos de 128 bits conectado al resto del sistema, lo que alivia la carga de tráfico de la CPU principal.
  - A diferencia de la FPU, algunos comportamientos de la VFPU se apartan del estándar IEEE-754.

En definitiva, Allegrex es extraordinariamente rápida. Sin embargo, aún no sabemos qué puede hacerse con ella. Al fin y al cabo, puedes imaginar esta CPU como el director de una orquesta, y aún no hemos conocido a los músicos.

#### Una gestión de memoria enfocada

Detengámonos un momento en el sistema de memoria implementado en esta consola. Entre otras cosas, el coprocesador de control del sistema proporciona la funcionalidad de una **Memory Protection Unit** o 'MPU' (no confundir con una 'MMU'), que mapea el hardware físico sobre el espacio de memoria de la CPU con algunas particularidades intermedias. Aunque su *modus operandi* puede parecer algo primitivo a primera vista, veremos por qué resulta óptimo para las necesidades de esta consola.

![Mapeado de memoria con la MPU.](_diagrams/cpu/mmu.png)

Una **Memory Management Unit** o 'MMU' tradicional gestiona el acceso de la CPU a los componentes que la rodean. Esto implica que todas las líneas de direcciones de la CPU se conectan a la MMU; y es esta última la única conectada al resto del sistema.

Esto ofrece ventajas notables para funciones como la 'memoria virtual' y la 'protección de memoria'. Para implementar memoria virtual, una MMU debe incluir un componente llamado **Translation Lookaside Buffer** (TLB) con el fin de evitar degradación del rendimiento. Pues bien, **la MMU de Allegrex carece de TLB** [@cpu-tachyon], de modo que se centra exclusivamente en la protección de memoria. Por este motivo, la MMU de Allegrex se denomina en realidad MPU (Memory Protection Unit). Una MPU es una versión reducida de la MMU sin memoria virtual. En cualquier caso, **la protección de memoria otorga al sistema la capacidad de decidir a qué zonas de memoria puede acceder un programa**.

Gracias a ello, Allegrex no tendrá que gestionar situaciones en las que programas de usuario (como los juegos) intenten acceder a zonas restringidas (como las claves de cifrado). Para aplicar esta restricción, las direcciones de memoria se agrupan en cinco segmentos con distintos niveles de privilegio. Además, la MPU de Allegrex cuenta con tres modos de operación: **modo usuario**, **modo supervisor** y **modo kernel**.

Si un proceso sin privilegios (operando en modo usuario) intenta acceder a una dirección de memoria en una zona restringida, la MPU solicitará al sistema operativo (mediante 'excepciones') si debe concederle permiso.

En definitiva, esto permite a Sony, como desarrolladora del sistema operativo, implementar un sistema de seguridad respaldado por el hardware.

### Memoria disponible

Hasta aquí hemos analizado la CPU principal de la PSP y sus aceleradores. Veamos ahora la memoria física disponible en este sistema.

![Distribución de la memoria desde la perspectiva de la CPU principal.](_diagrams/cpu/memory.png)

La PSP incorpora dos bloques de memoria accesibles desde la CPU [@cpu-tachyon]:

- **16 KB de SRAM**: es lo que en [artículos anteriores](playstation#cpu) denominamos **Scratchpad**. Es poca RAM, pero muy rápida, así que corresponde a los desarrolladores sacarle el máximo partido, aunque documentos no oficiales la han tachado de 'inútil' [@cpu-naked].
- **32 MB de DDR SDRAM**: una cantidad considerablemente mayor, aunque con una tasa de acceso inferior. A lo largo del artículo la llamaremos 'memoria principal'.
  - Las siglas 'DDR' significan 'Double Data Rate' y denotan una evolución respecto a la SDRAM tradicional, con un protocolo de transferencia más rápido. La [Xbox original](xbox#memory-layout) utilizaba el mismo tipo.

### Diseño de buses

Pronto descubrirás que la PSP alberga numerosos componentes, cada uno con una función muy concreta. Para organizarlos sin caer en los [errores del pasado](nintendo-64#tab-3-1-pipeline-stalls), los ingenieros de Sony los distribuyeron en buses dedicados: así, solo los componentes con funciones afines comparten el mismo bus. Estos buses se comunican entre sí mediante árbitros dedicados, en forma de 'controladores de memoria' o unidades de Acceso Directo a Memoria (DMA, del inglés *Direct Memory Access*).

Todos los buses del Tachyon implementan un diseño ampliamente conocido llamado **Advanced High-performance Bus** (AHB), una solución desarrollada por ARM para gestionar la congestión en sus chips y SoC. Si te genera curiosidad, el AHB también fue adoptado en la [Wii](wii#io).

![Esquema básico de los tres buses principales de este sistema.](_diagrams/cpu/buses.png)

Aplicando estos principios, se construyeron los siguientes buses para la PSP [@cpu-overview]:

- El **bus del sistema**: conecta la CPU, el Scratchpad y la GPU. Tiene 128 bits de ancho.
- El **bus del Media Engine**: conecta otro grupo de componentes (que se explican en la siguiente sección). Tiene las mismas características que el bus del sistema.
- El **bus periférico**: conecta los componentes de E/S y almacenamiento. Tiene 32 bits de ancho.

Los tres buses se conectan al controlador DDR, que es donde se aloja la memoria principal.

### Gestión del tráfico en el bus

Dentro de cada bus conviven varios componentes que trabajan de forma independiente y almacenan los datos procesados en un espacio compartido (como la memoria principal). Lo que no queremos es que la CPU tenga que intervenir cada vez que un módulo necesite leer o escribir en memoria. Tradicionalmente, se incluía una unidad DMA en el bus para ofrecer esta función, pero una única DMA tiene un rendimiento limitado. La PSP contiene un número considerable de componentes, lo que puede derivar en cuellos de botella.

La solución es sencilla: **bus mastering**. En pocas palabras, cada componente dispone de su propio controlador DMA. Esto le permite convertirse en el 'bus master' y tomar el control del bus para acceder a la ubicación que necesite. Para evitar conflictos (varios 'bus masters' simultáneos), los componentes adyacentes reconocen este evento y esperan a que la operación concluya.

### El fin de una era

La revolución RISC produjo innumerables diseños de CPU a lo largo de los años 80 y 90, pero solo unos pocos pervivieron más allá del cambio de siglo. Al igual que [concluí mi análisis del SuperH](dreamcast#end-of-the-line) en el [artículo sobre la Dreamcast](dreamcast), **la PlayStation Portable será el último artículo de esta serie en presentar una CPU MIPS**.

Tras múltiples transformaciones, lo que queda de MIPS es hoy una empresa que diseña CPU **RISC-V**. A decir verdad, se trata de una estrategia interesante que le proporciona mayor margen para competir contra ARM.

Al repasar mis análisis anteriores, sin embargo, cuesta entender cómo una empresa de tanto talento pudo perder su cuota de mercado. En el segmento de las estaciones de trabajo, el x86 de Intel se impuso pese a todas las críticas: abarató el equipamiento gracias a la línea Pentium y los 'clones', y lo volvió atractivo mediante su software compatible, exclusivo de Windows. Por último, la [arquitectura P6](xbox#tab-1-4-cisc-or-risc) dotó a Intel de la escalabilidad que hasta entonces solo disfrutaban los diseños RISC.

![En 2011, el Computer History Museum (Mountain View, California) realizó una entrevista con los cofundadores y antiguos directivos de MIPS [@cpu-chm_mips], en la que hablaron de los orígenes de la empresa, su impacto en la industria y su posterior reorganización.](mips_interview.jpeg)

En el segmento de bajo consumo, en cambio, me resultó más difícil entender su declive: MIPS y ARM compartían modelos de negocio similares, basados ambos en la concesión de licencias de IP. Por fortuna, la entrevista del Computer History Museum de 2011 abordó esta misma pregunta, y la respuesta fue:

> Cometimos un error: no comprendimos la importancia del mercado de los teléfonos móviles. Al principio no había dinero que ganar (...) pero ARM lo consiguió [invirtiendo en él](game-boy-advance#tab-1-2-a-new-cpu-venture). Fue una jugada muy inteligente que resultó ser un punto de apoyo extraordinario. Nosotros nos fijamos en el margen bruto y dijimos: "¡No es una gran rentabilidad! Más vale que pongamos nuestra atención en otro lado." Pero, claro, resultó que los teléfonos móviles se convirtieron en smartphones [y luego] se volvieron algo gigantesco. [@cpu-chm_mips]
>
> -- <cite>John Hennessy, cofundador de MIPS Computer Systems</cite>

En cualquier caso, en lo que respecta a este tema, creo que, aunque sea poco probable que veamos una nueva CPU MIPS, sí veremos a sus antiguos ingenieros trasladar las tecnologías distintivas de MIPS a nuevos proyectos.

Dicho esto, continuemos con el análisis de la PSP.

## La CPU multimedia

Hasta aquí hemos hablado de la CPU principal y los buses del Tachyon, pero el SoC esconde aún muchas más funciones. En particular, hay otro bloque de módulos diseñado para una aplicación específica: el **multimedia**. Este grupo se especializa en el **procesamiento de audio e imágenes**, incluyendo la combinación de ambos (procesamiento de vídeo), y opera sin consumir ancho de banda del bus de la CPU principal (el 'grupo del sistema').

![Daxter (2006), mostrando una cinemática en el juego renderizada por el grupo del Media Engine.](games/daxter.jpg)

Este nuevo bloque se denomina **grupo del Media Engine** y está compuesto por los siguientes elementos:

- Una segunda **CPU Allegrex** que actúa como 'controlador' del grupo del Media Engine. Es la misma CPU del grupo del sistema, aunque sin el coprocesador vectorial [@cpu-marshall]. El controlador recibe órdenes de la CPU principal y gestiona el resto del hardware multimedia que operará de forma concurrente.
- **2 MB de DRAM integrada (eDRAM)**: la codificación y/o decodificación multimedia consume un ancho de banda considerable, así que, para no bloquear a los demás componentes, el Media Engine dispone de memoria dedicada para procesar los datos 'en local' y publicar los resultados cuando sea necesario.
  - La 'eDRAM' es idéntica a la DRAM, pero fabricada dentro de otro chip.
- Un decodificador **MPEG-AVC**: un decodificador hardware que hace una sola cosa, y a gran velocidad: ingerir un **flujo de vídeo H.264** [@cpu-ppsspp] y escupir datos que el subsistema de audio y gráficos entiende.
- Un **Virtual Mobile Engine** o 'VME': se trata de *algún tipo* de Procesador de Señal Digital (DSP, del inglés *Digital Signal Processor*) programable. El controlador lo programa y luego comienza a suministrarle datos. Después, al igual que el AVC, el VME almacena el bloque procesado en la memoria local.
  - Este componente es sumamente opaco; solo Sony parece conocer sus entresijos (incluyendo cómo programarlo). A día de hoy, sigue siendo un misterio.
  - Según informes no oficiales, el VME parece disponer de memoria local y un controlador DMA. También se le conoce como 'Dynamic Reconfigurable Engine' [@cpu-specs].

Huelga decir que el Media Engine no es directamente manejable desde el lado del desarrollador (Sony lo oculta todo tras una API de alto nivel). El sistema operativo proporciona frameworks como 'libmpeg' o 'libmp3' que ya implementan aplicaciones completas optimizadas para este motor [@cpu-modules].

## Gráficos

Para empezar, repasemos la pantalla física de esta consola, que es donde el usuario puede apreciar todo lo que se analiza en esta sección.

### Pantalla

La PSP incorpora una **pantalla LCD TFT de 4,3 pulgadas**. Tiene una resolución de **480 × 272 píxeles** (para referencia, aproximadamente 2,6 veces los píxeles de una sola pantalla de la Nintendo DS) y puede mostrar hasta 16.777.216 colores. Se trata, por tanto, de una profundidad de color de 24 bits (la denominada escala 'true color').

![Comparación de resolución de pantalla y relación de aspecto entre la Game Boy, la Game Boy Advance, la Nintendo DS y la serie PSP.](_diagrams/screen_comparison.png)

La relación de aspecto es *casi* 16:9, un formato que por aquel entonces se estaba convirtiendo en estándar en los televisores domésticos. Una vista más amplia supone también una oportunidad para los diseñadores de juegos de enriquecer la experiencia (especialmente en el género de los shooters en primera persona).

### Presentando el Graphics Engine

Es el momento de hablar del componente encargado de producir píxeles: el **Graphics Engine** (GE). Este motor reside dentro del 'grupo del sistema', convirtiéndose efectivamente en un 'grupo dentro de otro grupo'.

::: {.subfigures .tabs-nested}

![Kingdom Hearts Birth by Sleep (2010).](games/kh.jpg){.active title="KH"}

![Spider-Man 3 (2007).](games/spider.jpg){title="Spider-Man"}

![Grand Theft Auto: Vice City Stories (2006).](games/gta.jpg){title="GTA"}

![The Sims 2 (2005)](games/sims2.jpg){title="Sims 2"}

![OutRun 2006: Coast 2 Coast (2006)](games/outrun.jpg){title="OutRun"}

![LocoRoco (2006)](games/loco.jpg){title="Loco"}

Ejemplos de juegos de PSP. Todos renderizados a su resolución máxima (480 × 272 píxeles).

:::

El GE dibuja gráficos 3D (polígonos) con numerosas funciones aplicadas (como mapeado de texturas, iluminación y mucho más), que veremos en breve.

### Arquitectura del Graphics Engine

El subsistema GE tiene muchas peculiaridades interesantes que comentar, por lo que esta será una sección muy densa. ¡Pero no te preocupes! Intentaré ir paso a paso para evitar confusiones.

Para empezar, el GE consta de tres componentes [@graphics-overview]:

- El **núcleo gráfico**: donde tiene lugar la funcionalidad gráfica propiamente dicha.
- El **controlador de eDRAM**: media el acceso entre la memoria eDRAM y el núcleo.
- La **bus matrix**: actúa como 'árbitro de bus' entre el bus del sistema y los componentes internos del GE (recuerda que el GE se encuentra dentro del grupo del sistema).
  - En realidad, este componente es una malla de cables con algo de lógica, pero para simplificar la explicación es más cómodo tratarlo como una caja negra.

La razón de este diseño es que tanto la CPU principal como el núcleo gráfico pueden acceder a esos 2 MB de eDRAM. Para evitar la congestión, el tráfico interno del GE circula por otro Advanced High-performance Bus llamado **bus local** ('local' desde la perspectiva del GE). Esto permite al núcleo gráfico realizar sus funciones sin depender del bus del sistema para mover datos (y evita así bloquear al resto del sistema).

![Arquitectura del Graphics Engine.](_diagrams/ge_arch.png){.open-float}

El bus local tiene el mismo ancho que el bus del sistema (128 bits), pero, si con eso no bastara, el núcleo gráfico dispone de una línea directa a la eDRAM mediante un bus de 512 bits (formado por dos buses unidireccionales de 256 bits). En la siguiente sección verás por qué es necesario.

¿Y cómo se comunican la CPU y el GE entre sí? Como ya se ha mencionado, tanto la CPU como el núcleo gráfico pueden leer de la eDRAM. Además, el núcleo gráfico puede acceder al bus del sistema para obtener datos de cualquier otro componente (incluida la memoria principal). Nada de esto ocurre por arte de magia, claro.

{.close-float}

En pocas palabras, dos bloques de **bus matrix** reconfiguran la conexión entre el bus local y el bus del sistema. Siempre que algún componente quiere acceder a un bus 'ajeno', las bus matrices configuran la comunicación para que esa unidad se convierta en master de ambos buses sin que ninguna otra interfiera; esta situación se mantiene hasta que la unidad designada termina la transferencia.

Este comportamiento me recuerda a la técnica del **bus mastering**, donde el componente que lidera es el 'bus master' y tiene control total del bus, mientras el resto permanece como 'esclavo' a la espera de órdenes. Sin embargo, no tengo claro qué protocolo o estándar intentaron replicar los ingenieros de Sony. Según mi comprensión, podría guardar cierta similitud con I²C, un protocolo de comunicación en serie (especialmente útil en sistemas embebidos) que también implementa bus mastering.

### Organización del contenido

Ahora que conocemos los componentes disponibles y cómo interactúan, veamos qué información relacionada con los gráficos podemos almacenar en memoria.

Hay tres ubicaciones de memoria de las que el GE acaba leyendo o en las que acaba escribiendo:

- **2 MB de eDRAM**: la eDRAM ya mencionada. Se utiliza para almacenar el frame buffer, el [z-buffer](nintendo-64#modern-visible-surface-determination) y el búfer de texturas. Sus contenidos son escritos directamente por el GE. Este espacio de memoria también se denomina 'memoria local'.
  - La CPU puede acceder a esta memoria si es necesario, aunque su velocidad no es la idónea.
- **32 MB de DDR SDRAM**: es el área de trabajo de la CPU para construir Display Lists, datos de vértices, datos de texturas y CLUTs (tablas de consulta de color). En el contexto de los gráficos, este bloque se denomina 'memoria host'.
- **16 KB de SRAM**: la memoria Scratchpad también es accesible tanto por la CPU como por el GE.

### Funcionalidad

Al igual que su [hermana de sobremesa](playstation-2#graphics), el sistema gráfico de la PSP se centra en la **rasterización**. Sin embargo, la VRAM de la PSP es la mitad que la de la PS2, y su bus no es tan rápido. Para compensarlo, el Graphics Engine de la PSP incorpora un procesador vectorial.

![Diseño del pipeline del Graphics Engine.<br>He omitido las interfaces para no complicar el esquema en exceso.](_diagrams/gpu/pipeline.png)

El pipeline gráfico es muy similar al de la PS2, con la adición de la etapa de procesamiento vectorial. El núcleo gráfico se divide en dos áreas: el **surface engine**, que se ocupa del procesamiento vectorial, y el motor de renderizado que, como su nombre indica, realiza la rasterización y los efectos [@graphics-overview].

Aunque sus características han sido documentadas anteriormente [@cpu-programming], los detalles del propio pipeline gráfico no lo han sido. Lo cierto es que los programadores no dependen de esa información para desarrollar sus juegos. Sin embargo, en aras de este análisis, he elaborado un modelo propio.

Por otro lado, he decidido abordar esta sección con mayor profundidad técnica que en artículos anteriores. Me pareció una buena oportunidad para introducir nuevos conceptos y ampliar la visión sobre los gráficos por ordenador. Dicho esto, te recomiendo encarecidamente leer sobre los sistemas gráficos de la [PS1](playstation#graphics) y la [PS2](playstation-2#graphics) antes de continuar.

#### Comandos {.tabs .active}

![Etapa de comandos.](_diagrams/gpu/commands.png){.tab-float}

El Graphics Engine se controla mediante 'Display Lists' tradicionales almacenadas en la memoria principal. La CPU las construye y la GPU las lee (mediante Acceso Directo a Memoria). Las Display Lists básicamente indican a la GPU qué dibujar, cómo y dónde. En el caso de la PSP, las Display Lists no se limitan a las tareas de renderizado: también pueden incluir transformaciones vectoriales.

La estrategia implementada para procesar las listas es fundamental para que la CPU y la GPU trabajen de forma concurrente y no se bloqueen mutuamente. Queremos que la GPU acelere las operaciones, no que sea una carga. Por ello, tanto la CPU como la GPU admiten **deferred rendering** [@cpu-programming], una técnica que permite a la CPU construir el siguiente conjunto de Display Lists mientras la GPU procesa el anterior. El GE se configura especificando dónde comienza la Display List en memoria (dirección base) y dónde termina (dirección de parada). En consecuencia, hay dos formas de asignar una lista:

- **Doble búfer**: hay dos áreas de Display List separadas. Una se está transfiriendo a la GPU y la otra la está rellenando la CPU. Cuando ambos procesadores terminan, intercambian sus roles.
- **Controlada por almacenamiento**: la CPU rellena la misma lista que la unidad DMA está transfiriendo a la GPU, pero la CPU va algunos pasos por delante para evitar solapamientos. Consume menos memoria, pero es susceptible a bloqueos si la GPU alcanza a la CPU.

Además, la unidad DMA de la GPU no es una mera 'copiadora de memoria sin inteligencia': también puede interpretar los datos transferidos [@cpu-programming]. Así, las Display Lists pueden incluir **comandos** como `jump` y `return` para indicar a la DMA que salte y obtenga datos de otra ubicación. Esto evita que la CPU tenga que incrustar grandes recursos (modelos, texturas, etc.) en las Display Lists (lo que duplicaría los datos en memoria) y reduce el consumo de ancho de banda. Este sistema fue heredado de la PS2.

Por último, la DMA también puede interpretar **datos de bounding box**: combinados con información almacenada en el GE, la DMA omitirá los comandos de dibujo que queden fuera del área de visualización. También es posible declarar bounding boxes dentro de otras bounding boxes, aunque ese tema lo dejamos para otro día.

#### Procesamiento vectorial {.tab}

![Etapa de procesamiento vectorial.](_diagrams/gpu/vector.png){.tab-float}

El Graphics Engine debuta con la capacidad de realizar operaciones sobre vectores, lo que permite descargar gran parte del trabajo de la CPU. Sony diseñó esta unidad para acelerar tareas comunes que antes realizaban las VPU de la PS2 mediante microcode [@cpu-programming]. Aunque el GE no es tan flexible como una VPU (es una unidad de función fija), simplifica considerablemente la programación (dado que el microcode tenía un peso notable en la curva de aprendizaje). El procesador vectorial del GE se denomina **surface engine**.

El surface engine aborda **tres tipos de tareas**. La primera es la operación con **superficies paramétricas** [@graphics-pspdkgu]. ¿Recuerdas la sección ['Mundos infinitos'](playstation-2#infinite-worlds)? Sony explicó que, si bien la PS2 era capaz de esto, al final muy pocos juegos se molestaron en aprovecharlo. Es posible que el rendimiento de polígonos, junto con la dificultad de diseño e implementación, fuesen los factores determinantes.

Para abordar esto, el surface engine implementa dos curvas paramétricas:

- **Bézier**: una función relativamente rápida, aunque sus parámetros modifican la forma global. Además, sufre de 'problemas de continuidad' que se manifiestan como grietas en las superficies (lo que desaconseja su uso en animaciones). Se utiliza habitualmente para la generación de paisajes.
- **B-spline**: requiere más cómputo, pero soluciona los problemas de continuidad y ofrece control localizado. El modelado y la animación de personajes son candidatos naturales.

Ambas admiten **niveles de detalle**, lo que significa que los desarrolladores pueden establecer un valor arbitrario para modificar el nivel de subdivisión (con impacto en la calidad del modelo resultante).

La segunda tarea se denomina **vertex blending**, una técnica empleada para **animaciones**. El surface engine ofrece dos tipos:

- **Skinning** (o animación esquelética): el movimiento se basa en una estructura de 'articulaciones' unidas al modelo del personaje. Se aplica habitualmente para animaciones estándar (caminar, saltar, etc.).
- **Morphing**: utiliza múltiples instancias de un modelo y las promedia para obtener un 'efecto de transición' (mediante interpolación lineal). Se usa principalmente para animaciones artificiales (es decir, movimientos faciales) y efectos especiales. Conlleva un mayor consumo de memoria y una curva de aprendizaje más pronunciada.

Es importante señalar que la CPU puede seguir necesitando calcular animaciones para procesar la lógica del juego (como la detección de colisiones), por lo que no puede delegar todo el trabajo.

Por último, el surface engine también realiza **scissoring** (descarte de vértices fuera del viewport o de un área rectangular).

#### Rasterización {.tab}

![Etapa de rasterización.](_diagrams/gpu/rendering.png){.tab-float}

La siguiente etapa de la generación gráfica tiene lugar en el **motor de renderizado** (omitiendo el procesador de comandos). Aquí, los datos vectoriales se transforman en píxeles, lo cual es perfectamente comparable a cualquier otra GPU del mercado.

El motor dibuja muchos tipos de primitivas: puntos, líneas, tiras de líneas, triángulos, tiras de triángulos, abanicos de triángulos y sprites (formados por rectángulos 2D). También incluye una unidad llamada 'analizador diferencial digital' que se emplea para interpolar valores durante la rasterización y el mapeado de texturas.

Los desarrolladores pueden suministrar una matriz de proyección para aplicar la **transformación en perspectiva**. Esto convierte su mundo 3D en un espacio 2D (para poder verlo en pantalla), usando la cámara virtual como modelo. Se da por sentada la implementación de características modernas como la precisión de subpíxel (de lo contrario, los usuarios habrían [notado su ausencia](playstation#tab-4-1-distorted-modelstextures) de inmediato).

#### Texturas {.tab}

![Etapa de mapeado de texturas.](_diagrams/gpu/textures.png){.tab-float}

Este quizá sea el tema que más interesa a algunos. Los polígonos (ahora meros píxeles) pueden pintarse con texturas. En esta etapa, los mapas de textura se leen de la memoria y se procesan con diversas funciones. Este proceso se denomina **mapeado de texturas**.

El motor de renderizado dispone de tres **modos de mapeado** o, en otras palabras, tres formas de procesar los mapas de textura:

- **Mapeado UV**: cada coordenada del modelo se mapea a una coordenada de la textura.
- **Mapeado proyectivo**: en pocas palabras, simula un proyector físico que proyecta un mapa de textura sobre una superficie.
- **Mapeado de sombreado**: proyecta hasta cuatro luces (ambiental, direccional, puntual o focal) sobre el modelo. Es útil al combinar el resultado con otro mapa (lo verás con más detalle en la siguiente sección).
  - Si los programadores suministran un mapa de textura en lugar de un color sólido, el resultado es un efecto 'ojo de pez' (útil para reflexiones, es decir, **mapeado de entorno**).
  - Este modo también permite el [toon shading](gamecube#creativity).

Las texturas pueden usar **tablas de consulta de color** o 'CLUTs' para reducir su tamaño. Además, este motor aplica **perspective correction** y **bilinear o trilinear filtering** para la interpolación.

En otro orden de cosas, el GE dispone de **8 KB de caché de texturas** para ahorrar ancho de banda. La caché emplea el algoritmo 'Least Recently Used' para gestionar el espacio.

Por último, aunque el pipeline no es programable, los desarrolladores pueden enviar colores adicionales para mezclarlos con las texturas. También hay duplicación de color (que duplica el valor RGB de los colores), adición de color (que combina un color primario con uno secundario) y **niebla** (que difumina los polígonos lejanos).

#### Píxeles {.tab}

![Etapa de operaciones sobre píxeles.](_diagrams/gpu/operation.png){.tab-float}

Llegamos al final del pipeline. La geometría inicial se ha transformado en píxeles, que ahora están richamente coloreados, así que ha llegado el momento de decidir qué hacer con ellos.

Algunos píxeles pueden corresponder a geometría que no es necesaria para el frame actual (por ejemplo, porque está ocluida o enmascarada). Para filtrarlos, el GE puede realizar las siguientes pruebas:

- **Scissoring**: descarta polígonos fuera de un área arbitraria.
- **Rango de profundidad**: descarta polígonos demasiado lejanos o demasiado cercanos (los desarrolladores establecen los valores de referencia).
- **Color**: descarta píxeles iguales o distintos a un valor RGB.
- **Alfa**: compara el valor alfa del píxel con un valor de referencia.
- **Stencil**: similar al alfa, pero se basa en el valor de stencil.
- **Prueba de profundidad**: Z-buffering tardío.

A continuación, los píxeles también pueden pasar por estos bloques opcionales para efectos adicionales:

- **Mezcla alfa**: combina los píxeles no opacos con los del frame buffer usando diferentes operadores aritméticos.
- **Dithering**: suaviza los cambios de color.
- **Limitación de color**: ajusta los valores RGB para que encajen en el formato del frame buffer.
- **Operación lógica**: decide cómo combinar el nuevo frame con el frame buffer existente usando operadores lógicos (`AND`, `OR` y muchos más).
- **Enmascaramiento**: como su nombre indica, enmascara el z-buffer o el frame buffer.

Funciones complejas como el **antialiasing** son el fruto de una combinación estratégica de los elementos anteriores. Por último, el píxel resultante se escribe en el frame buffer, que a su vez se envía a la pantalla.

#### Observaciones {.tabs-close}

Como se ha evidenciado, la PSP hereda diversas características de la PS2. La diferencia, sin embargo, es que gran parte de esta funcionalidad está ahora grabada directamente en el silicio, en lugar de ofrecer numerosas unidades programables de propósito general (que requieren configuración manual). Presumiblemente, esto se hizo por dos razones: utilizar menos transistores (para que quepa en el Tachyon y la placa base siga siendo 'portátil') y facilitar la portabilidad de las bases de código de PS2 a la nueva consola.

### Diseño de modelos

Para ilustrar cómo este diseño influyó en el modelado, y para facilitar la comparación con la [PS2](playstation-2#better-models) y la [Nintendo DS](nintendo-ds#model-designs), aquí hay dos ejemplos de modelos diseñados para la PSP.

![Metal Gear Solid: Portable Ops Plus (2006).<br>1.383 triángulos.](snake_psp){.toleft hardcover_latex_width="88%" paperback_latex_width="80%" model3d="true"}

![Daxter (2006).<br>1.374 triángulos.](daxter_psp){.toright hardcover_latex_width="47%" paperback_latex_width="40%" model3d="true"}

### Salida de vídeo

El primer modelo de esta consola (modelo '1000') cuenta con un puerto de vídeo propietario llamado **remote port** en la esquina inferior izquierda (junto al conector de auriculares).

![Esquina inferior derecha de la PSP (modelo 3000) mostrando la 'Video Out' propietaria.](io/bottom.jpg){.open-float}

El remote port utiliza el protocolo RS-232 [@io-nil], un viejo estándar para la transferencia de datos en serie. Aunque las especificaciones no estaban disponibles públicamente para los desarrolladores (ni mucho menos documentadas), aparecieron en el mercado un par de auriculares con botones de control. Aparentemente, emplean el puerto serie para enviar comandos (reproducir, pausar, etc.) a la consola.

{.close-float}

En los modelos posteriores ('2000' y '3000'), el remote port se amplió con un patillaje YCbCr adicional. Sony distribuyó tres **cables de vídeo** (por componentes, S-Video y compuesto) que se apoyan en esta interfaz para transmitir la pantalla de la PSP al televisor.

El cable de vídeo envía un frame con una resolución de **720 × 480 píxeles (en NTSC) o 720 × 576 píxeles (en PAL)**, ya sea en modo progresivo o entrelazado (esta última opción solo está disponible en el modelo 3000). Como la resolución nativa de la PSP es de 480 × 272 píxeles, los juegos muestran **barras negras** para preservar la relación de aspecto. Sin embargo, estas barras no aparecen cuando la consola ejecuta la interfaz visual 'XMB', que soporta de forma nativa el ajuste de su resolución según el cable de vídeo.

## Audio

En esta sección solemos encontrar un [Generador de Sonido Programable](master-system#audio) (PSG, del inglés *Programmable Sound Generator*), un [secuenciador](nintendo-64#audio) o un *gran* [mezclador](nintendo-ds#audio). La razón es que el hardware de audio dedicado existe para liberar a la CPU de las operaciones de sonido, aunque si resulta ser demasiado limitado, las capacidades sonoras de la consola se resienten.

Pues bien, lo que en la PSP consideramos 'hardware de sonido' es sumamente básico: tan solo **dos canales PCM y un mezclador estéreo**. La frecuencia de muestreo máxima y la resolución son **48 kHz** y **16 bits**, respectivamente.

No veo ninguno de los aceleradores que mencioné en el primer párrafo. ¿Implica esto que el sonido será tan limitado como el de [otros](nintendo-64#secrets-and-limitations-1) [casos](game-boy-advance#audio)? ¡No! Porque la PSP recurre al software para compensarlo.

Y es que, si bien no hay mucha circuitería dedicada al sonido, Sony incluyó una gran cantidad de componentes de uso general capaces de ayudar con la decodificación, la transmisión y la mezcla de audio. Me refiero al **Media Engine**.

Este bloque destaca en el procesamiento de señales, pero necesita programarse. Por ello, Sony desarrolló el siguiente software para el Media Engine, a fin de complementar los dos canales PCM (a los que llamaremos el 'punto de salida') [@audio-sad] [@audio-sas]:

- **Driver de sonido**: se comunica con el punto de salida y realiza transferencias de memoria mediante DMA.
- **Sintetizador de audio por software**: como su nombre indica, genera las señales que se enviarán al punto de salida.
  - Este sintetizador soporta 32 canales, en formatos **PCM**, **ADPCM** o **ATRAC3** [@io-edepot].
  - Ofrece **reverb digital**, **control de tono** y **envolvente ADSR**.

Los juegos no acceden a estos módulos directamente; en su lugar, llaman a las numerosas bibliotecas incluidas en el SDK oficial. Algunas están diseñadas para operar con señales brutas, mientras que otras están optimizadas para aplicaciones específicas. Entre los ejemplos se incluyen:

- **Decodificación de MP3 y AAC**.
- **Secuenciación MIDI**.
- **Decodificación de audio VoIP** (G729 y u-law).
  - El sistema operativo de la PSP incluía una variante de la aplicación 'Skype', por si te preguntas qué uso podría tener VoIP en este contexto.

### Comparación de audio {.interactive-only}

Pongamos todo esto en práctica viendo cómo reproducían el sonido los juegos. He preparado este reproductor especial que permite cambiar entre consolas para facilitar la comparación lado a lado:

::: {.subfigures .side-by-side figure="false" .interactive-only}

![**Nintendo DS:** Final Fantasy IV (2007).<br>**PSP:** Final Fantasy IV - The Complete Collection (2011).](){audio_switcher="true" src1="overworld_nds" src2="overworld_psp" label1="Nintendo DS" label2="PSP" .toleft}

![**PlayStation 2:** Kingdom Hearts 2 (2005).<br>**PSP:** Kingdom Hearts Birth by Sleep (2010).](){audio_switcher="true" src1="encounter_ps2" src2="encounter_psp" label1="PlayStation 2" label2="PSP" .toright}

:::

En el primer ejemplo, el juego de Nintendo DS secuencia su música en tiempo real, mientras que el de PSP decodifica ATRAC3. Como puedes escuchar, la ausencia de hardware de sonido dedicado *per se* no implica una calidad de audio deficiente. Es más, podría argumentarse lo contrario: el Media Engine ofrecía más funciones que los chips de sonido de la mayoría de las consolas portátiles anteriores.

El segundo ejemplo es bastante más difícil de cuantificar. Incluí una pista de PS2 para comparar, pero tuve que subir un poco el volumen de la versión de PS2 (supongo que las bandas sonoras de PSP son más altas para compensar el altavoz más pequeño y la proximidad al usuario). Hay que tener en cuenta también que los arreglos difieren ligeramente entre plataformas, algo que puede deberse simplemente a una decisión creativa, ya que el juego de PSP se publicó cinco años después que el de PS2. En conjunto, la PSP no muestra deterioro (teniendo en cuenta que la [SPU2 de la PS2](playstation-2#audio) es muy potente y flexible).

## E/S

La PSP tiene una buena cantidad de conexiones y sensores sobre los que hablar. No obstante, aprovecharé esta oportunidad para presentar también algunos chips que aún no se han mencionado, y que desempeñan un papel fundamental en la gestión de la circuitería que detecta la entrada del jugador.

### Interfaces internas

La mayor parte de la E/S disponible se canaliza a través del **Peripheral Bus**, que tiene solo 32 bits de ancho —suficiente para transferir información sencilla a un ritmo normal— y también tiene acceso a la RAM principal.

El D-pad, el joystick y los botones están gestionados por un chip exclusivo denominado **System Control** o **'SysCon'**. Este nombre en clave es habitual en el hardware de Sony. El chip se encarga de interconectar muchos componentes internos, aunque en el caso de la PSP, SysCon solo gestiona los botones físicos.

### Interfaces externas

La consola cuenta con una buena cantidad de conexiones [@io-edepot]:

- **Wi-Fi 802.11b**: se conecta a un punto de acceso o funciona en modo 'ad hoc' para comunicarse con otras PSP cercanas. Similar a lo que ofrecía la [Nintendo DS](nintendo-ds#wireless-network).
- **USB 2.0**: para accesorios o para conectar la consola a un ordenador. En el segundo caso, la PSP se reconoce como un dispositivo de almacenamiento masivo, dando acceso al Memory Stick.
- **IrDA**: siglas de 'Infrared Data Association', similar a lo que utiliza un mando a distancia de televisión para cambiar de canal. Antes del lanzamiento de la PSP, el protocolo infrarrojo era popular para transferir contenido multimedia entre una cámara o teléfono y el ordenador. En el caso de la PSP, sin embargo, el puerto IrDA cayó en el más absoluto olvido.
  - Como suele ocurrir, Sony lo eliminó en la segunda revisión de la PSP (modelo '2000').

### Accesorios comerciales

El uso de un puerto estándar, el USB, incentivó a los fabricantes de terceros a diseñar accesorios para esta consola.

![Parte superior de la PSP (modelo 3000).<br>Obsérvense los dos orificios que rodean el puerto mini USB para sujetar un accesorio.](io/top.jpg)

Entre los accesorios comercializados se encontraban:

- Un **micrófono** para chat de voz.
- Un **receptor GPS**, para usarlo con el disco *Go! Explore GPS* en navegación por satélite.
- Una **cámara** para videoconferencias. Tiene sentido, dado que *Skype* venía incluido con el sistema.

Lo curioso es que este tipo de periféricos ya estaba disponible en el mercado de los Pocket PC y las PDA. Esto lleva a preguntarse si Sony concebía la PSP como un *Pocket PC para jóvenes*.

### Conectividad con consola doméstica

Tras el lanzamiento de la PlayStation 3, apareció en la PSP una nueva función: el **Remote Play**. Mediante una conexión Wi-Fi, era posible controlar la PS3 a distancia desde la PSP.

La idea era anterior al lanzamiento de la [Wii U](wiiu). Lamentablemente, solo un puñado de juegos de PS3 soportaba Remote Play, por lo que los usuarios únicamente podían navegar por los menús, reproducir contenido multimedia y jugar a títulos de PlayStation 1.

## Sistema operativo

Con el paso de los años, el programa encargado de controlar una consola antes de que arranque un juego se ha vuelto cada vez más complejo, impulsado principalmente por la creciente necesidad de seguridad y servicios (APIs actualizables, multijugador en línea, multimedia, etc.). La PSP intenta acomodar todo esto en un hardware muy limitado. No quiero decir con esto que el resultado sea *mediocre*, pero muchas de las decisiones de diseño son fruto del equilibrio entre costes, rendimiento y robustez.

### Arquitectura y diseño

Ante todo, la PSP contiene una **ROM de 4 KB** oculta y sin documentar dentro del Tachyon, donde reside el bootloader [@operating_system-ipl_loader]. En otras palabras, al encender la consola, la CPU comienza buscando instrucciones en esa ROM, que recibe distintos nombres: 'Bootrom', 'Pre-IPL' y 'Lib-PSP iplloader'.

El resto del sistema está instalado en **32 MB de memoria NAND Flash** ubicada en la placa base, donde reside la mayor parte del **sistema operativo** (SO) de la PSP.

El SO se compone de los siguientes elementos:

#### Módulos {.tabs .active}

Un **módulo** es lo que en el mundo del PC/Windows llamaríamos un 'programa' o 'driver'. Una vez cargados, los módulos pueden residir en memoria y realizar las siguientes tareas [@cpu-naked]:

- Exponer funciones para simplificar el acceso a determinado hardware.
- Ejecutarse como programa en primer plano (por ejemplo, los juegos).

El binario de un módulo puede estar cifrado o descifrado, e incluye metadatos que indican si se trata de un 'módulo de usuario' o un 'módulo del kernel'; este último permite al módulo controlar zonas privilegiadas de la memoria (donde reside el kernel).

Los módulos son invocados por el 'kernel' o la 'Visual Shell' (VSH). Los módulos de usuario pueden ejecutar otros módulos a discreción del kernel, lo que refuerza la seguridad: por ejemplo, el kernel jamás carga módulos del kernel desde los Universal Media Discs (UMD).

#### Kernel/IPL {.tab}

Aunque este SO no contiene un kernel *per se*, sí dispone de múltiples componentes que asumen las funciones de un kernel tradicional.

El primero es el **IPL**, que es cargado por el Pre-IPL y se encarga de inicializar el hardware. Una parte del IPL, denominada **IDStorage**, almacena también información exclusiva de la consola (como su dirección MAC, el número de serie y las claves de autenticación de UMD) [@operating_system-jas]. ¡Sobrescribir esta última área tiene consecuencias catastróficas! Por ello, las claves de UMD se duplican varias veces para sobrevivir a posibles corrupciones.

El IPL carga a continuación un conjunto de **módulos del kernel** que gestionan operaciones de bajo nivel (gestión de memoria, multihilo y sistema de archivos). También implementan las excepciones de acceso a memoria provocadas por la MPU (¿recuerdas el [apartado sobre segmentación](playstation-portable#a-focused-memory-management)?). Para simplificar, cuando use la palabra 'kernel' en este artículo, me referiré a estos módulos del kernel.

Dicho esto, el kernel no implementa multitarea, pero sí soporta multihilo cooperativo para procesos individuales.

El kernel permanece en memoria mientras la consola tenga alimentación. Por esa razón, **los programas (módulos de usuario) no se escriben para bare-metal**; en su lugar, dependen de las funciones que exponen los módulos del kernel. En consecuencia, el kernel reserva **4 MB de la RAM principal** y otros **4 MB para 'memoria volátil'** (un búfer temporal para numerosas operaciones, cuyo acceso se concede bajo demanda). Esto deja únicamente **24 MB de RAM principal** (de los 32 MB totales) para los programas de usuario.

#### Otras partes {.tab}

La NAND también aloja otras secciones del firmware.

En primer lugar, está la **Visual Shell** o 'VSH', que es lo primero que percibe el usuario al encender la PSP. La VSH es una interfaz gráfica de usuario que permite ejecutar juegos y otros módulos (únicamente los de tipo usuario). Está compuesta por múltiples módulos, algunos de los cuales solo se cargan cuando se solicitan.

Dato curioso: la llamada al sistema que se utiliza para iniciar los juegos comerciales funciona primero reiniciando la consola y luego cargando el ejecutable. Es probable que esta estrategia se adoptara para descargar la VSH y liberar recursos para el juego.

En segundo lugar, existe una segunda partición en la NAND que almacena datos relacionados con el usuario, como la configuración de red. Esta partición se denomina 'flash1' (a diferencia de 'flash0') y su contenido se llama **configuración del sistema**.

### Proceso de arranque {.tabs-close}

Ahora que hemos identificado las principales partes, examinemos cómo trabajan juntas para llevar la consola a un 'estado operativo' (una vez que se enciende). La seguridad solo se menciona brevemente aquí; la sección 'Antipiratería y homebrew' la analiza con más profundidad.

El *complejo* proceso de arranque funciona de la siguiente manera [@operating_system-ipl] [@cpu-naked]:

1. El vector de reset de la CPU principal apunta a `0x1FC00000`, que a su vez señala a la ROM Pre-IPL dentro del Tachyon.
    1. La primera mitad del Pre-IPL ordena a la CPU copiar su segunda mitad en la memoria scratchpad y continuar la ejecución desde allí.
        - El Pre-IPL busca la siguiente etapa en la **NAND o en un Memory Stick externo**. Cuando se selecciona este último (nunca bajo un uso normal), la PSP entra en un modo denominado **factory service mode**. Para simplificar, nos centraremos en el modo normal (selección de NAND).
    2. El Pre-IPL inicializa el controlador de NAND y continúa la ejecución desde allí. La segunda parte del Pre-IPL ejecuta el IPL, que está cifrado; por ello se descifra (mediante 'KIRK', cuyos detalles veremos más adelante) y se copia en la eDRAM (dentro del Graphics Engine) como área de trabajo.
    3. Una vez finalizado el descifrado, la ejecución continúa en la eDRAM, donde reside el IPL descifrado.
2. El proceso de ejecución del IPL se divide en tres etapas.
    1. La primera etapa, llamada **loader**, resetea la CPU principal y oculta la ROM Pre-IPL del mapa de memoria. El loader también inicializa el hardware mínimo y descomprime 'Main.bin', la siguiente etapa, en la eDRAM.
    2. **Main.bin** se centra en inicializar el resto del hardware, incluida la memoria principal. Una vez finalizado, descifra la tercera etapa en la memoria principal y continúa la ejecución desde allí.
    3. La etapa final, denominada **payload**, carga el kernel. Este se almacena en forma de múltiples binarios, módulos y metadatos que, una vez cargados en la RAM principal, dan vida al sistema. A continuación se muestra la interfaz interactiva.

### Visual Shell

Internacionalmente, esta consola debuta con la famosa interfaz **XrossMediaBar** o 'XMB', la completa Interfaz Gráfica de Usuario (GUI, del inglés *Graphical User Interface*) con la que se entregó la PSP.

![La pantalla principal (sin juego insertado o instalado).](xmb/main.jpg){.tabs-nested .active title="Inicio"}

![El XMB incluye un visor de fotos y vídeos.](xmb/photo.jpg){.tab-nested title="Multimedia"}

![La categoría 'Juego' permite ejecutar un juego, gestionar partidas guardadas o buscar otras PSP (Game Sharing).<br>Al situarse sobre el propio juego, el XMB se adapta visualmente con los recursos del juego, hasta que el usuario selecciona otro elemento de la categoría.](xmb/game.jpg){.tab-nested title="Juego"}

![La categoría 'Configuración' ofrece numerosas opciones de personalización.<br>Algunas entradas agrupan varios controles de configuración.](xmb/setting.jpg){.tab-nested title="Configuración"}

![Así se escribe en esta consola, al más puro estilo de los teléfonos móviles de antes.](xmb/keyboard.jpg){.tabs-nested-last title="Teclado"}

Desde el punto de vista de la interacción persona-ordenador, el diseño del XMB ofrece soluciones muy ingeniosas a los retos de navegación (moverse por múltiples niveles de información, evitar saturar al usuario con opciones y encajar todas las entradas en una pantalla de 4,3"). En esencia, la información se organiza en 'categorías' y 'elementos', y el D-pad permite navegar entre ellos: las flechas izquierda y derecha cambian de categoría, mientras que las flechas arriba y abajo seleccionan un elemento dentro de la categoría actual. Cabe destacar que todas las categorías están al mismo nivel jerárquico, por lo que ninguna entrada se superpone a las demás. Esto impide la inserción de publicidad que distraiga la atención.

Por otro lado, el XMB ofrece opciones de personalización, como cambiar el color de fondo y reorganizar los elementos. Además, da acceso a servicios multimedia y, por supuesto, permite cargar el juego (ya sea desde un UMD o un Memory Stick).

También cuenta con un visor de archivos integrado, que se usa tanto para la gestión multimedia como para la de partidas guardadas.

### Actualizabilidad

Como hemos visto, todo excepto el Pre-IPL está almacenado en un soporte de escritura y, por tanto, es 'actualizable'. Sony distribuyó las actualizaciones de firmware en forma de archivos descargables, que los usuarios podían descargar manualmente o a través del asistente de 'Actualización del sistema', el cual también instalaba dichos paquetes.

![El 'Asistente' de actualización del sistema.](xmb/update.jpg)

Algunas actualizaciones reforzaron el sistema de seguridad de la PSP añadiendo capas de cifrado al proceso de arranque y comprobaciones de integridad para los módulos del kernel.

Desde el punto de vista del desarrollador, algunas actualizaciones mejoraron las APIs añadiendo nuevas funciones y corrigiendo las existentes. En consecuencia, los juegos quedaban vinculados a la versión del sistema para la que se desarrollaron, y los usuarios debían actualizar el firmware para jugar a los más recientes. Sin embargo, las actualizaciones siempre mantenían la compatibilidad con versiones anteriores, por lo que los juegos más antiguos nunca deberían romperse en un sistema actualizado (al menos, en teoría).

Desde la perspectiva del usuario, algunas actualizaciones introdujeron nuevos servicios, como un navegador web, un lector de RSS y otros. Tras el lanzamiento de la PlayStation 3, muchas aplicaciones en línea se portaron a la PSP, incluyendo la 'PlayStation Store' y el 'Remote Play'.

## Juegos

Por primera vez en esta serie, los juegos desarrollados para esta consola no se escriben para bare-metal. En lugar de acceder directamente a direcciones de memoria y puertos, deben llamar a módulos para realizar operaciones sobre el hardware. De hecho, las APIs del SDK oficial están diseñadas en torno a este modelo.

### Ecosistema de desarrollo

El kit de desarrollo que Sony proporcionó a los estudios fue producido por SN Systems (los mismos autores de los kits de PS1 y PS2). El kit de software requería una estación de trabajo con Windows XP y Cygwin, o bien CentOS Linux.

Con este kit, los desarrolladores tenían acceso a [@games-sdk]:

- Un **SDK** con bibliotecas en C++ vinculadas a una versión concreta del firmware (ya que dependen de los módulos instalados).
- Una elección entre **dos compiladores**: una variante del compilador GCC llamada *psp-gcc*, o una alternativa de SN Systems denominada *SVCPSP*.
- El **depurador GDB**.
- El **ensamblador**, el **enlazador** y el **archivador** de SN Systems. Este último genera los ejecutables de PSP.
- Un **plugin para Visual Studio 2005 y 2008** que permitía a los desarrolladores usar el IDE de Microsoft para el desarrollo en PSP.

En un principio, Sony solo proporcionó un emulador para probar el software de PSP [@cpu-programming]. Más adelante, este fue sustituido por la **PSP Hardware Tool**: una torre parecida a un PC conectada a una carcasa de PSP no funcional (similar al [kit de Nintendo DS](nintendo-ds#tab-4-1-the-hardware)). El devkit se comunicaba con la estación de trabajo mediante un programa llamado **ProDG** (solo disponible para Windows).

### Soporte de almacenamiento

La PS1 y la PS2 contaban con dos tipos de almacenamiento: un disco de solo lectura para cargar los juegos y una 'Memory Card' regrabable para guardar partidas y ejecutables. La PSP continuó esta tradición, aunque con formatos distintos.

Dado que esta era la primera consola portátil de Sony orientada al gran público, ¿cómo encontraron el soporte adecuado para cada función? Bien, teniendo en cuenta que también fueron los creadores del *BetaMax*, el *MiniDisc* y otros, ya puedes imaginar qué hicieron esta vez... introducir *aún más* formatos.

#### Discos UMD {.tabs .active}

![Un juego comercial típico.](retail.jpg){.tab-float}

Los CD y DVD son demasiado grandes y fáciles de copiar. Además, los lectores convencionales no funcionan bien en entornos con vibraciones (quien haya intentado escuchar un CD de audio en un Walkman mientras *caminaba* sabe de lo que hablo). La solución de Sony fue crear algo completamente nuevo: el **Universal Media Disc** (UMD), un soporte propietario.

Los UMD almacenan **900 MB o 1,8 GB** según sean de una o dos capas, respectivamente. Se diferencian de los [DVD](playstation-2#medium) y los [MiniDVD](gamecube#medium) no solo en el diseño físico, sino también en su estructura de datos interna.

Además de juegos, Sony publicó las especificaciones 'UMD video' y 'UMD audio', que permitían a los distribuidores de terceros llevar su contenido a los usuarios de PSP —recuerda que estamos en 2004, cuando el *iPod Photo* tenía una pantalla de 2"—.

Estos discos van encajados en una **carcasa no extraíble**; el centro es magnético, de modo que se adhiere al motor del lector en cuanto se introduce el disco.

#### Memory Stick Duo {.tab}

![Un Memory Stick junto a una tarjeta SD convencional.](io/memory_stick.jpg){.tab-float}

El equivalente 'portátil' de la Memory Card es el **Memory Stick Pro Duo**, otro ejemplo de la insistencia de Sony en introducir un nuevo soporte en un mercado ya saturado (¿recuerdas la enorme cantidad de opciones que había entonces? Incluso las [flashcarts](game-boy-advance#flashcarts) debían adaptarse a múltiples variantes). Este formato llegó durante el auge de la fotografía digital y los teléfonos móviles, cuando la gente buscaba mucho almacenamiento a bajo precio, justo antes de que la tarjeta SD se impusiera.

El Memory Stick, como los demás, es un protocolo que conecta la memoria interna (como la Flash) con periféricos externos de forma cómoda: así puedes intercambiar tarjetas entre el ordenador y la cámara sin desmontar nada. La variante 'Pro Duo' (usada por la PSP) tiene una capacidad de **hasta 32 GB**, con velocidades de **1,23 MB/s** en lectura y **0,54 MB/s** en escritura.

La PSP utiliza el Memory Stick para almacenar contenido relacionado con el usuario, como partidas guardadas, multimedia y juegos. Está formateado con el sistema de archivos FAT32, y el controlador integrado en el Memory Stick puede soportar la gestión de derechos digitales de [MagicGate](playstation-2#interactive-accessories).

A diferencia de la RAM interna de la PSP, el contenido del Memory Stick no está mapeado en memoria; se accede al sistema de archivos a través de llamadas al sistema proporcionadas por los módulos del kernel.

### Servicios en red {.tabs-close}

Con el tiempo, Sony se puso a la altura de la competencia y mejoró su infraestructura en línea, ofreciendo incluso servicios que Microsoft y Nintendo aún no proporcionaban.

#### PlayStation Network {.tabs .active}

![La entrada PSN en el XMB.](xmb/network.jpg){.tab-float}

En respuesta al [Xbox Live](xbox#network-service) de Microsoft y la [Nintendo Wi-Fi Connection](nintendo-ds#wireless-network) de Nintendo, Sony desarrolló su propia infraestructura en línea: la **PlayStation Network** o 'PSN'.

Este servicio sustituyó al primitivo [DNAS](playstation-2#network-service), que se había utilizado anteriormente en la PlayStation 2. Entre las novedades se encontraba el uso de un sistema de autenticación centralizado.

Los usuarios podían registrar una cuenta de PSN gratuita, lo que les permitía jugar en multijugador con personas de todo el mundo a través de Wi-Fi.

Cabe mencionar que, en un principio, la PSP no incluía muchos servicios en línea. Esto cambió drásticamente con el lanzamiento de la PlayStation 3, aunque esa es ya una historia para un [artículo posterior](playstation-3)...

#### PlayStation Store {.tab}

![La PlayStation Store.](xmb/store.jpg){.tab-float}

Otra novedad que siguió al debut de la PlayStation Network fue la **PlayStation Store**: un mercado en línea que permite a los usuarios adquirir juegos digitalmente y descargarlos directamente al Memory Stick.

Supongo que fue una adición bien recibida por los estudios pequeños, ya que la distribución física conlleva costes considerables que terminaban [repercutiendo](nintendo-64#games) en el precio del juego. Curiosamente, un modelo posterior de la PSP llamado 'PSP Go' fue aún más lejos en este cambio: al eliminar el lector de UMD, solo podía reproducir títulos adquiridos en la PlayStation Store.

Con el tiempo, el catálogo de juegos se amplió enormemente con la llegada de los **[juegos de PS1](playstation#games)**: se descargaban al Memory Stick y se jugaban como cualquier otro título digital. Los ejecutables de PS1 en la PSP funcionaban gracias a la **emulación por software**; Sony integró en el sistema un emulador propio para que todo funcionase sin problemas.

#### Contenido actualizable {.tab}

![La opción 'Actualizar juego' en el XMB.](xmb/game_update.jpg){.tab-float}

Por último, los servicios en línea ofrecían la posibilidad de **descargar parches para los juegos** (en forma de 'actualizaciones') directamente a la consola.

Sin embargo, pocos juegos aprovecharon esta función; algo difícil de imaginar hoy en día, cuando enormes actualizaciones —los llamados *parches del día uno*— se publican antes incluso de que el juego llegue a las estanterías de las tiendas...

## Antipiratería y homebrew {.tabs-close}

El hecho de ser una consola portátil no implica que su seguridad sea inferior a la de la PS2; más bien al contrario: el sistema de seguridad implementado en la PSP es muy diverso, y algunas de sus partes siguen siendo un misterio hasta hoy.

Por supuesto, todos sabemos que su implementación tenía puntos débiles que acabaron abriendo la puerta al homebrew y a la piratería. Sin embargo, teniendo en cuenta que esta era una consola de 2004, ciertas decisiones de diseño pueden considerarse adelantadas a su tiempo, especialmente al compararlas con la competencia.

### Seguridad física

Voy a describir los tres chips principales responsables de las operaciones relacionadas con la seguridad. Los algoritmos implicados pueden resultar bastante complejos para quienes se acercan por primera vez, pero intentaré ofrecer un panorama general. No olvides consultar la bibliografía si el tema te resulta interesante, ya que este texto se centra principalmente en la propia PSP.

#### KIRK {.tabs .active}

Para empezar, tenemos **KIRK**, otro componente que reside dentro del Tachyon. KIRK es la implementación hardware de los siguientes algoritmos criptográficos (piensa en él como un acelerador) [@anti_piracy-kirk] [@anti_piracy-keys]:

- **Cifrado y descifrado AES** (del inglés *Advanced Encryption Standard*): un sistema de cifrado simétrico que opera con una clave dada. KIRK también proporciona un almacén de 128 claves y una clave maestra, ambas de 128 bits, para cifrar y descifrar datos.
- **ECDSA**: otro sistema de cifrado asimétrico. Incluye dos funciones:
  - Generación de par de claves pública y privada: se usa para cifrar y descifrar datos de usuario, como los archivos de guardado.
  - Verificación de firma: valida y descifra datos cifrados por Sony. KIRK también almacena la clave pública de Sony a tal efecto.
- **SHA-1**: genera un valor hash a partir de los datos. ECDSA depende de SHA durante la verificación de firma para garantizar que los datos originales no han sido manipulados.
- **Código de Autenticación de Mensajes basado en Cifrado (CMAC, del inglés *Cipher-based Message Authentication Code*)**: otro algoritmo que descifra y verifica la integridad de los datos cifrados (independientemente de si proceden de Sony o no) mediante una clave compartida. Se apoya en AES para el descifrado.
- **Generación de números pseudoaleatorios**: como su nombre indica, intenta generar un número impredecible para uso criptográfico.

En resumen, la CPU emite comandos a KIRK (por ejemplo, "cifra esta cadena con el algoritmo x y la clave y"). KIRK también puede actuar como bus master, escribiendo los resultados directamente en la RAM principal.

#### Lepton {.tab}

Como ya sabes, los discos UMD no son fáciles de duplicar, pero además su contenido está almacenado en forma cifrada. Los usuarios nunca lo notan, sin embargo, ya que el proceso de descifrado también lo gestiona el hardware.

Junto al Tachyon se encuentra otro Sistema en Chip denominado **Lepton**, que controla la unidad de UMD y actúa como intermediario entre la CPU principal y el contenido del disco.

Lepton alberga una enorme cantidad de circuitería —podrías considerarlo casi otro ordenador dentro de la PSP—. Este componente incluye su propia CPU, un DSP para la decodificación, 480 KB de memoria (como búfer) y 384 KB de ROM para almacenar su firmware.

La CPU principal se comunica con Lepton mediante el protocolo ATAPI, una interfaz tradicionalmente usada para conectar lectores de CD/DVD convencionales a los PC. La diferencia crucial, sin embargo, es que Lepton solo descifra los datos de un UMD si el disco es reconocido como auténtico.

#### SPOCK {.tab}

Además del cifrado gestionado por Lepton, los sectores de un UMD están cifrados mediante AES. **SPOCK** es otro bloque situado junto a KIRK que se ocupa de descifrar cada sector del disco UMD.

Dentro de la NAND Flash, el IDStorage contiene una clave cifrada conocida como 'master key'. SPOCK incluye una clave cableada en el hardware que se usa para descifrarla. La clave maestra descifrada permite entonces a SPOCK descifrar otra clave que se encuentra en el UMD, y esta segunda clave se utiliza finalmente para leer cada sector. *¡Pan comido!*

Por cierto, aunque SPOCK reside dentro del Tachyon, lo opera el Lepton.

En resumen, implementar estas rutinas en hardware permite al sistema ejecutar juegos sin consumir recursos de uso general en tareas de cifrado y descifrado.

### Seguridad software {.tabs-close}

El sistema operativo utiliza el hardware disponible para componer una arquitectura de seguridad. En general, el software está protegido mediante los siguientes principios:

- **Cadena de confianza**: el Pre-IPL, la primera etapa del proceso de arranque, es un fragmento de código de solo lectura escrito por Sony e integrado durante la fabricación. Todo lo que el Pre-IPL aprueba se considera de confianza, y dicha confianza se extiende a todas las etapas que carga a continuación.
- **Ejecutables firmados**: los ejecutables no solo están cifrados, sino también firmados con claves que solo Sony conoce, lo que garantiza que los datos manipulados no puedan volver a firmarse.
- **Hardware opaco**: KIRK, SPOCK y Lepton son bloques de silicio indocumentados y ofuscados que no revelan qué algoritmos aplican ni cómo lo hacen.
- **Seguridad a prueba de desarrolladores**: los modos de privilegio de la MPU restringen el acceso a las zonas de memoria sensibles, protegiendo así frente a posibles exploits a nivel de usuario en los juegos comerciales (que podrían derivar en ejecución de código arbitrario).

### Derrota

A pesar de todas estas protecciones, los hackers nunca abandonaron esta consola, quizá porque su enorme funcionalidad la hacía muy atractiva para el desarrollo de homebrew.

El volumen de trabajo descubierto en este ámbito es impresionante, así que me ha sido necesario trazar una línea. Por ello, aquí solo se cubren los avances más significativos. Para quienes quieran el panorama completo, playstationdev.wiki mantiene un buen archivo de cada uno de los descubrimientos.

#### Primeros tropiezos {.tabs .active}

Tras el lanzamiento de la PSP en Japón, era solo cuestión de tiempo que comenzaran a surgir exploits a nivel de usuario. Algunos aprovecharon múltiples fallos de seguridad encontrados en las primeras versiones del firmware:

- El juego 'Wipeout' incorporaba un navegador web para acceder al contenido descargable, pero no estaba protegido contra **ataques DNS (secuestro de dominio)**, lo que permitía a los usuarios navegar por cualquier URL de internet.
  - Más adelante se descubrió que el campo de URL del navegador no validaba la entrada. Así, introducir `file:///disc0:/` como URL **listaba el contenido del UMD** ya en forma descifrada, lo que brindó a los hackers la oportunidad de inspeccionar los ejecutables de PSP y someterlos a ingeniería inversa.
- La primera revisión del sistema **no verificaba las firmas** de los ejecutables almacenados en el Memory Stick, lo que permitía ejecutar módulos de usuario personalizados (pero no los del kernel).
  - Esto fue probablemente accidental, ya que en las unidades de desarrollo es habitual omitir las comprobaciones de firma. Parece que Sony se precipitó con el firmware comercial y olvidó reactivar dichas comprobaciones, como le ocurrió a [Microsoft](xbox#tab-5-2-bootstrap-search).
- Una vez parcheado esto, los hackers descubrieron **un fallo en la implementación del sistema de archivos** que permitía ejecutar código sin firma. En síntesis, el usuario debía duplicar la carpeta que contenía el ejecutable, añadir `%` al nombre de la segunda carpeta y reorganizar estratégicamente el contenido de ambas.

Estos primeros tropiezos ayudaron a construir una base de conocimiento sobre el funcionamiento interno de la PSP, lo que llevó a nuevos vectores de ataque y software personalizado que interactuaba directamente con el hardware (recuerda que solo los estudios de desarrollo tenían acceso a los SDK oficiales y a la documentación).

Como en todo juego del gato y el ratón, Sony respondió con más actualizaciones del sistema (obligatorias para los nuevos juegos). Mientras tanto, seguían apareciendo nuevos descubrimientos, como los llamados exploits 'TIFF' y 'GTA' [@anti_piracy-homebrew]. Cabe mencionar que estos primeros avances solo se consideraban 'lanzadores de homebrew', ya que todavía no se había conseguido acceso completo al kernel.

#### Downgrading {.tab}

![El *Chronoswitch downgrader*, una herramienta moderna de downgrade.](downgrader.jpg){.tab-float}

A medida que Sony fue publicando actualizaciones de firmware más robustas (que incorporaban parches de seguridad junto a funciones atractivas, como un navegador web), las versiones antiguas se convirtieron en una especie de tierra prometida donde los usuarios avanzados podían ejecutar su homebrew. Sin embargo, encontrar una PSP con una versión del sistema explotable resultaba cada vez más difícil. El **downgrading** ganó así una rápida popularidad: al contrario que la actualización, consiste en sustituir el firmware instalado por una versión anterior (y más fácilmente explotable).

El método más habitual para hacer downgrading consistía en ejecutar un exploit que engañaba al sistema para que instalase un archivo de actualización más antiguo. No era sencillo, ya que dependía de un conjunto de exploits cada vez más reducido en las últimas versiones del firmware.

Con el tiempo, llegaron al mercado dos 'modchips': 'Undiluted Platinum' y 'PSP-Devolution'. Ambos incorporaban un chip NAND independiente que servía como almacenamiento NAND secundario, lo que permitía arrancar un firmware alternativo (y explotable) o sustituir el principal. Esta técnica solo era compatible con el modelo '1000' de la PSP, ya que los posteriores integraron el Tachyon, la NAND y la DDR SDRAM en un único paquete.

#### Pandora {.tab}

El juego del gato y el ratón continuó hasta agosto de 2007, cuando llegó **Pandora** [@anti_piracy-pandora].

El 'método de la batería Pandora' es una colección de logros popular y respetada. Consiguió eludir la mayoría de las capas de seguridad y se centró en el único punto donde Sony no podía reaccionar rápidamente: el Pre-IPL. Esto es lo que Pandora logró [@anti_piracy-pandora_wiki]:

1. **Encontrar una forma de entrar en 'service mode'**: manipulando la batería extraíble de la PSP, que incorpora algo de circuitería con fines de identificación, los hackers descubrieron que sobrescribir el valor del número de serie a `0xFFFFFFFF` activaba el service mode durante el arranque. En ese estado, el Pre-IPL busca un sistema secundario en el Memory Stick. La batería modificada pasó a conocerse como batería **JigKick** o 'Pandora'.
    - Los usuarios podían fabricar una batería JigKick con una PSP hackeada, o desoldando el pin de tierra de la EEPROM de la batería. Esto último era mucho más arriesgado, ¡ya que suponía desmontar una batería de litio! No es de extrañar que algunos fabricantes de terceros comenzaran a vender kits para crear la 'batería Pandora'.
    - Funcionalmente, una batería JigKick se comporta como cualquier batería normal. Sin embargo, al insertarla, la PSP siempre arrancará desde el Memory Stick.
2. **Engañar al 'service mode' para que cargue un firmware falso o sin firma**: el siguiente paso requería encontrar una manera de cargar un IPL falso en service mode. El principal obstáculo era que el ejecutable seguía necesitando estar cifrado y firmado para funcionar. Los hackers encontraron un modo de sortear esto. Básicamente, el proceso funcionó así:
    1. Aunque el IPL se cifra en bloques de 1 KB de código, los hackers descubrieron que KIRK podía recibir instrucciones para descifrar tamaños menores, dejando el resto del código intacto.
    2. Mediante fuerza bruta, se elaboró un binario especial que, al ser descifrado por KIRK, solo contenía una instrucción similar a 'saltar 100 bytes hacia adelante'.
    3. Combinando ambas técnicas, los hackers construyeron un binario con la instrucción de salto cifrada junto con código no cifrado. Todo ello permitió **la ejecución de código arbitrario con máximos privilegios** en service mode.
    4. El código IPL personalizado en el Memory Stick solo podía residir en sectores específicos, por lo que se desarrollaron programas especiales para facilitar el proceso. Un Memory Stick con un IPL personalizado instalado en esa ubicación se denomina **Magic Memory Stick**.

A partir de ahí, solo quedaba integrar un payload útil. Las opciones más populares incluían:

- Un **de-bricker**, que reinstalaba un firmware oficial limpio, reparando consolas con NAND corruptas.
- Un **downgrader**, ahora con mayores permisos.
- Un instalador de **custom firmware** (CFW), del que hablaré con más detalle a continuación.

Cabe destacar que esto fue un descubrimiento monumental, especialmente teniendo en cuenta que KIRK no había sido completamente sometido a ingeniería inversa, y que el bloque cifrado que hacía 'saltar' al Pre-IPL tuvo que obtenerse mediante fuerza bruta.

#### CFW y más allá {.tab}

![Algunos CFW incluyen módulos especiales para gestionar opciones de bajo nivel.](xmb/cfw.jpg){.tab-float}

El método Pandora asestó un duro golpe a Sony: era un exploit de bootrom efectivo y, al igual que en el caso de la [Nintendo DS](nintendo-ds#tab-6-3-native-slot-1), las correcciones solo podían llegar en forma de nuevas revisiones de hardware.

Una de las principales aplicaciones de Pandora fueron los **Custom Firmwares** (CFW). Un CFW es un firmware oficial modificado para incorporar funcionalidad adicional, como módulos de homebrew. Estas personalizaciones disfrutaban de privilegios del kernel y tenían control total sobre la consola. Entre los nuevos módulos se encontraban cargadores de ISO, desactivadores de firma, gestión de la CPU a bajo nivel, un cargador de 'plugins' (que permitía realizar más personalizaciones sin reinstalar el firmware) y muchos más. Distintos grupos publicaron sus propias variantes, como M33, PRO y ME.

Un CFW necesita instalarse previamente, y el instalador depende de un exploit a nivel del kernel. Gracias a Pandora, esto ya no suponía ningún obstáculo. Sin embargo, Sony acabó corrigiendo las vulnerabilidades que daban lugar a Pandora, por lo que los modelos posteriores de la PSP quedaron limitados a los métodos de exploit basados en software tradicionales.

El golpe definitivo llegó cuando el [sistema de seguridad](playstation-3#os-security-hierarchy) de la [PlayStation 3](playstation-3) fue [comprometido](playstation-3#tab-9-4-the-fall-of-encryption). La PS3 almacenaba las **claves privadas utilizadas para descifrar los ejecutables de PSP** (ya que su sistema operativo incluye un emulador de PSP). Una vez extraídas, cualquiera podía firmar software a nivel de usuario e incrustar un exploit del kernel para, por ejemplo, arrancar un instalador de CFW o un cargador de CFW.

En 2021 se descubrió que el firmware más reciente contiene una vulnerabilidad a nivel del kernel durante el proceso de arranque, aprovechable para lanzar el CFW deseado. Esto se empaquetó en una solución llamada **Infinity 2** [@anti_piracy-davee].

### Fomento del homebrew {.tabs-close}

Vulnerar la seguridad no era el único objetivo de la comunidad homebrew; también había grupos comprometidos en proporcionar las herramientas necesarias para crear software sin consecuencias legales. Por ejemplo, el grupo pspdev publicó un SDK de código abierto llamado **PSPSDK** que replicaba muchas de las interfaces oficiales, eliminando a la vez las restricciones impuestas por Sony.

El PSPSDK también incluye un kit de herramientas para gestionar el proceso de compilación y empaquetado, al igual que el SDK oficial, pero diseñado para PSP que ejecuten un CFW.

## ¡Esto es todo, amigos!

![La PSP adquirida para este artículo.<br>Esto es lo que compraban los mayores que querían ser guay (yo tenía una DS).](mypsp.jpg)

¡Y así concluye este repaso a la última consola portátil antes de que los smartphones y las tabletas tomaran el relevo! Creo que esto nos da una idea de los servicios y funciones que los usuarios esperaban de las portátiles a principios de los 2000, y de cómo fue evolucionando todo a lo largo de la siguiente década.

Quiero dar las gracias al discord de la PSP Homebrew Community no solo por tomarse el tiempo de revisar este *interminable* artículo, sino también por ilustrarme con aún más información. Varios miembros de ese grupo han dedicado un gran esfuerzo a desarrollar herramientas gratuitas y aplicaciones de homebrew para ampliar las capacidades de esta consola. Mi reconocimiento se extiende igualmente a todos los autores de los documentos recogidos en las fuentes. Tardé unos 3 meses en escribir este artículo, pero fue posible gracias a los años de trabajo combinado que otras personas dedicaron a someter esta consola a ingeniería inversa.

En cuanto al próximo artículo, antes necesitaré dedicar uno o dos meses a atender todos los problemas y solicitudes presentados en el [repositorio de GitHub](https://github.com/flipacholas/Architecture-of-consoles) y a realizar algunas tareas de mantenimiento en el sitio, pero puedes estar seguro de que me esfuerzo por que la próxima entrega sea tan completa como esta.

¡Hasta la próxima!  
Rodrigo
