---
date: 2021-10-20
long_title: Arquitectura de la PlayStation 3
short_title: Arquitectura de la PlayStation 3
generation: 7
subtitle: Un superordenador de otro planeta
long_name: PlayStation 3
name: PS3
cover: ps3
release_date: 2006-11-11
aliases: [/writings/consoles/ps3-private]
top_tabs:
  Motherboard:
    caption: "Modelo COK-001 (el primero de todos), extraída de mi unidad CECHA12.<br>Los 128 MB de flash NAND restantes y los conectores para la unidad Blu-ray PATA, la placa hija Wi-Fi/BT, el panel frontal y el lector MultiCard están montados en la parte trasera."
    bib_source: copetti
    paperback_latex_height: 93
    marked_paperback_latex_height: 93
  Models:
  - active: true
    caption: "El PlayStation 3 original, o 'PS3'.<br>Lanzado el 11/11/2006 en Japón, el 17/11/2006 en América y el 23/03/2007 en Europa."
    title: Original
    file: original
    paperback_latex_height: 83
    hardcover_latex_height: 86
  - caption: "La serie PS3 2000/3000 (conocida como 'Slim').<br>Lanzada el 01/09/2009 en Europa y América, y el 03/09/2009 en Japón."
    title: Slim
    file: slim
    paperback_latex_height: 93
    hardcover_latex_height: 96
  - caption: "La serie PS3 4000 (conocida como 'Super Slim').<br>Lanzada en septiembre de 2012 a nivel internacional."
    title: Super Slim
    file: super-slim
    paperback_latex_height: 93
    hardcover_latex_height: 96
  Diagram:
    hardcover_latex_height: 95
    paperback_latex_height: 97
---

## Una breve introducción

En 2006, Sony presentó la esperadísima consola de videojuegos de 'próxima generación': una máquina reluciente (aunque pesada) cuya arquitectura de hardware sigue las enseñanzas del [Emotion Engine](playstation-2), es decir, centrarse en el procesamiento vectorial para maximizar el rendimiento, incluso a costa de la complejidad. Mientras tanto, su nuevo 'superprocesador', el Cell Broadband Engine, nació en plena crisis de innovación y tuvo que mantenerse al día a medida que evolucionaban las tendencias en servicios multimedia.

Este análisis examina en profundidad el proyecto conjunto de Sony, IBM, Toshiba y Nvidia, junto con su ejecución y su repercusión en la industria.

### Sobre la extensión del artículo

Me temo que este artículo no es del típico que suelo escribir para otras consolas de [esta serie](consoles), que puede leerse en un momento libre. Si te interesa conocer todos los rincones de la PlayStation 3, prepárate para el viaje completo. Dicho esto, este texto abarca unos seis años de investigación y desarrollo a cargo de innumerables ingenieros, así que no espero que lo asimiles todo de una vez. Tómate tu tiempo (y algún que otro descanso si lo necesitas) y, si al final te quedas con ganas de más, en la sección 'Fuentes' encontrarás material adicional.

## {.supporting-imagery}

## CPU

Bienvenido a la parte más reconocible e innovadora de esta consola.

### Introducción

La CPU de la PS3 es enormemente compleja, pero también un ejercicio de ingeniería fascinante. Este afronta necesidades complejas con soluciones poco convencionales, muy representativas de una era marcada por el cambio y la experimentación. Antes de adentrarnos en las entrañas de la CPU de la PS3, he escrito los siguientes párrafos para aportar algo de contexto histórico. De ese modo, podremos descomponer el chip de arriba abajo de forma que no solo entiendas cómo funciona, sino también la razón de ser de sus principales decisiones de diseño.

#### El estado del progreso {.tabs .active}

![La CPU del PS1 (1994). Diseñada por LSI y Sony con tecnología de MIPS.](_diagrams/cell/ps1.png){.tab-float}

![El Emotion Engine del PS2 (2001). Diseñado por Toshiba, de nuevo con tecnología MIPS.](_diagrams/cell/ee.png){.tab-float}

Casi diez años después de la presentación del [PlayStation original basado en MIPS](playstation), nos encontramos a comienzos de la primera década del 2000 y las perspectivas para SGI/MIPS no son nada halagüeñas. Nintendo acaba de abandonarlos en favor de un [núcleo PowerPC de gama baja](gamecube#cpu) con IBM como nuevo proveedor, mientras que Microsoft, el recién llegado al mercado, optó por [Intel](xbox#cpu) y su dominio x86.

Sony tiene costumbre de tomar diseños de gama baja ya existentes (núcleos MIPS económicos) y moldearlos para alcanzar un rendimiento 3D aceptable a un coste reducido, un proceso en el que han participado otras empresas como LSI (para [la CPU del PS1](playstation#cpu)) y Toshiba (para el [Emotion Engine](playstation-2#cpu) del PS2). Esta metodología se prolongó hasta 2004 con el lanzamiento de la PlayStation Portable. Dicho esto ¿qué nueva amalgama de MIPS iban a construir para la PlayStation 3?

Resulta que el desarrollo de la PlayStation 3 es anterior al de la PlayStation Portable [@cpu-koranne, p. 5]. En el año 2000, pocos meses después del lanzamiento del PS2, Sony formó una alianza con IBM y Toshiba denominada 'STI', con el único objetivo de desarrollar el siguiente chip capaz de impulsar la próxima generación de superordenadores [@cpu-engine]. Por si esto no sonaba ya lo suficientemente ambicioso, ese nuevo chip también serviría para la sucesora del PS2. Finalmente, en 2004, IBM presentó el **Cell Broadband Engine** (también conocido como 'Cell BE' o simplemente 'Cell') [@cpu-nbc_cell].

#### Nuevas filosofías de diseño {.tab}

![El chip Cell Broadband Engine. Lamentablemente, demasiado brillante para mi cámara.](cell.jpg){.tab-float}

Para entender la propuesta _radical_ de Cell, conviene tener en cuenta los problemas que acuciaban a esa época (finales de los 90, principios de los 2000).

Año tras año, los consumidores exigen más velocidad. Así ha sido siempre. Sin embargo, el último enfoque empleado para lograrlo ([aplicar un pipeline al datapath](xbox#tab-1-4-cisc-or-risc) y aumentar la frecuencia de reloj) está dejando de escalar. El NetBurst de Intel [no puede evolucionar más](xbox#p6-and-the-end-of-pentium-numbers) y su prometido sucesor no aparece por ningún lado. De manera similar, el PowerPC 970/G5 de IBM no puede cumplir su promesa de '3 GHz' ni desarrollar una versión de bajo consumo (motivo por el que Apple no puede lanzar ningún portátil con su CPU de última generación) [@cpu-no5g]. En definitiva, parece que los ingenieros se enfrentan a una nueva [crisis de escalabilidad](playstation#tab-1-1-a-bit-of-history).

Así, la atención se vuelca en la **computación distribuida** [@cpu-mit]. En otras palabras: ¿por qué obsesionarse con exprimir el rendimiento de una sola máquina cuando se podrían tener varias máquinas más pequeñas repartiendo la carga de trabajo? Este enfoque no es, ni mucho menos, nuevo, dado que todas las consolas analizadas en este sitio web cuentan con más de un procesador. Sin embargo, es el desarrollo de un 'único procesador con múltiples núcleos' lo que abre nuevas oportunidades para el diseño de CPU (no necesariamente limitadas al mercado de las consolas).

En consecuencia, Cell forma parte de esta nueva ola de investigación y desarrollo. Esta nueva CPU combina un diseño multinúcleo con un énfasis especial en el procesamiento vectorial. Como recordarás, la computación vectorial es óptima para simulaciones (física, iluminación, etc.) y se materializó anteriormente en forma del [Geometry Transformation Engine](playstation#tab-2-2-geometry-transformation-engine) o de las [Vector Units](playstation-2#co-cpus), pero enseguida verás por qué el diseño de Cell supone un salto enorme respecto a los dos anteriores.

#### Una nueva era multinúcleo {.tab}

![Ejemplo de diseño heterogéneo.<br>Ha sido la arquitectura de facto de muchas consolas potentes hasta la fecha.](_diagrams/cell/paradigm/heterogeneous.png){.tab-float}

![Ejemplo de diseño homogéneo.<br>Cada núcleo puede llevar a cabo las mismas tareas que antes, pero no está necesariamente limitado a ellas.](_diagrams/cell/paradigm/homogeneous.png){.tab-float}

Si te paras a pensarlo, tanto la CPU del PS1 como el Emotion Engine eran ya procesadores multinúcleo. ¿Por qué tanto revuelo con Cell, entonces? Los dos chips anteriores estaban compuestos de un núcleo de uso general y varios núcleos específicos para determinadas aplicaciones (procesador de audio, descompresión de imagen, etc.), mezclando distintas arquitecturas, donde el núcleo de uso general se encargaba de comandar a los demás.

Este tipo de diseño de CPU recibe el nombre de **computación heterogénea**, y ha sido la opción de facto para construir máquinas especializadas en un conjunto concreto de aplicaciones (los videojuegos, en este caso). Su contraparte, la **computación homogénea**, predomina en el mercado de los ordenadores, donde las CPU deben ejecutar una gama más amplia de tareas (todas con la misma prioridad). Por ello, este último tipo puede integrar múltiples núcleos, pero todos del mismo tipo.

Volviendo al tema, **Cell combina ambos modelos**: hay dos tipos de núcleos en esta CPU, **un 'líder' de uso general y ocho 'asistentes' vectoriales**. Estos núcleos vectoriales pueden asumir distintos roles y, al hacerlo, cubren las tareas que antes resolvían los diseños heterogéneos; pero como los vectores de Cell no están limitados a un único tipo de tarea, sus núcleos ofrecen la flexibilidad propia de los ordenadores homogéneos. En definitiva, este diseño no es perfecto y arrastra ciertos compromisos, pero a lo largo de este texto verás los distintos problemas que Cell intentó resolver y cómo lo logró.

### Una ojeada a Cell {.tabs-close}

Tras exponer toda esta historia y teoría, creo que ya estamos preparados para presentar al protagonista de esta sección. Así es Cell [@fig-cell]...

![El Cell Broadband Engine (variante PS3).<br>Diseñado por IBM para supercomputación y simulación científica. El 'SPE' tachado indica que está deshabilitado (inutilizable). El otro 'SPE' de la izquierda está reservado al sistema operativo.](_diagrams/cell/cell.png){#fig-cell}

... y al final de esta sección sabrás qué hace cada componente.

#### Estructura general

Cell funciona a una frecuencia de _nada menos que_ **3,2 GHz** y está compuesto por multitud de componentes. Para facilitar el análisis, esta CPU puede dividirse en tres grandes áreas [@cpu-architecture]:

- El **líder**: es la parte de Cell que dirige al resto del circuito. Aquí encontramos un componente denominado **PowerPC Processor Element** (PPE).
- Los **asistentes**: son tan esenciales como el PPE, pero sus capacidades se limitan a un rol de asistente y acelerador. Este grupo está formado por ocho **Synergistic Processor Elements** (SPE).
- Las **interfaces**: a medida que la demanda de ancho de banda crece de forma exponencial, se implementan nuevas interfaces para mover datos sin generar cuellos de botella. En el grupo de interfaces encontramos un puñado de protocolos: el **Element Interconnect Bus** (EIB), el **Broadband Engine Interface Unit** (BEI), el **Memory Interface Controller** (MIC) y los **Flex I/O buses**.

Esta información se retomará a lo largo del artículo con más detalle, así que no hace falta que memorices estos nombres. El objetivo principal de esta sección es que te formes una imagen mental de la naturaleza de Cell y te familiarices con todos los componentes de los que hablaremos en su momento.

#### Cómo está organizado este análisis

Vista la estructura anterior, he tenido que organizarlo de forma que no te satures con tanta información. Así pues, analizaremos Cell estudiando cada componente en este orden:

1. El bus que conecta todos los componentes: el **Element Interconnect Bus (EIB)**.
2. El **PowerPC Processor Element (PPE)** y su elemento central, el **PowerPC Processing Unit (PPU)**.
3. Qué **memoria de uso general** hay disponible en esta consola.
4. Los **Synergistic Processor Elements (SPE)** y su elemento central, el **Synergistic Processor Unit (SPU)**.
5. El **modelo de programación** ideado para programar Cell de forma eficiente.

Dicho esto, comencemos con el análisis en sí.

### El interior de Cell: el corazón

Desde su presentación, Cell se ha denominado **Network-on-Chip** (NoC) [@cpu-koranne, p. 62] en lugar de la definición tradicional de Sistema en Chip (SoC, del inglés *System-on-Chip*); esto se debe al inusual bus de datos de Cell, el **Element Interconnect Bus** (EIB). [Ya hemos visto](playstation-2#a-recognisable-memory-choice) lo exigentes que pueden ser los componentes de una CPU y [lo susceptible](nintendo-64#tab-3-1-pipeline-stalls) que es un sistema a los cuellos de botella. Pues bien, para abordar este problema por undécima vez, IBM ha ideado un nuevo diseño... y lo ha documentado con términos análogos a la conducción por carretera.

![Diagrama simplificado del Element Interconnect Bus (EIB).<br>Cada flecha entre 'Ramps' (nodos) representa dos buses unidireccionales; así, cada nodo está conectado al siguiente mediante cuatro canales.](_diagrams/cell/eib.png)

El EIB está formado por doce nodos denominados **Ramps**, cada uno de los cuales conecta un componente de Cell. Los Ramps están interconectados mediante cuatro buses: dos circulan en **sentido horario** y los otros dos en **sentido antihorario**. Cada bus (o **canal**) tiene 128 bits de ancho. En lugar de recurrir a topologías de bus único (como hacían el Emotion Engine y su predecesor), los Ramps están interconectados siguiendo la topología de **token ring**, en la que los paquetes de datos deben atravesar todos los nodos vecinos hasta llegar al destino (no existe una ruta directa). Dado que el EIB dispone de cuatro canales, existen cuatro rutas posibles (**anillos**).

Quizás te preguntes: ¿qué sentido tiene un token ring si los datos pueden acabar recorriendo trayectos más largos (en comparación con un bus directo único)? Un bus único es muy susceptible a la congestión. Por eso, los ingenieros del EIB optaron por esta topología para gestionar grandes volúmenes de tráfico concurrente (sigue leyendo si quieres saber cómo ayudó el token ring).

Los datos se transfieren en forma de **paquetes de 128 bits** [@cpu-cell_arch]. Cada anillo puede gestionar hasta **tres transferencias concurrentes**, siempre que los paquetes no se solapen. El EIB opera mediante **créditos de comando**: cuando un componente necesita iniciar una transferencia, envía una solicitud al **Data Arbiter** del EIB, que gestiona el tráfico dentro de los anillos. En cuanto la solicitud es aprobada, los paquetes entran en el anillo y reciben un 'token', que el Data Arbiter usa como metadato para supervisar la transferencia. Además, algunos componentes tienen prioridad preferente sobre otros, como el **Memory Interface Controller** (MIC), donde reside la RAM principal. Por último, el Data Arbiter nunca colocará paquetes en anillos cuyo trayecto supere la mitad del anillo.

Cada Ramp interviene en la transferencia: lee la dirección de destino del paquete para decidir si envía los datos a su componente correspondiente o los reenvía al siguiente Ramp. Durante cada ciclo de reloj, los Ramps pueden recibir y enviar paquetes de 128 bits (16 bytes) al mismo tiempo. Así, teniendo en cuenta que hay cuatro canales y que **el EIB opera a 1,6 GHz** (la mitad de la velocidad de Cell), la tasa de transferencia teórica máxima es: `16 bytes x 2 transferencias/ciclo x 4 anillos x 1,6 GHz = 204,8 GB/s`. Este valor es, por supuesto, demasiado optimista, y hay muchos otros factores externos (el trayecto origen/destino, el estado del bus, etc.) que condicionan el rendimiento real. En cualquier caso, numerosas publicaciones de investigación de IBM y otros autores han recopilado velocidades más realistas mediante experimentos prácticos [@cpu-hpc].

Ahora que ya sabes cómo se interconectan todos los componentes de Cell, es hora de conocer el primer componente de este chip...

### El interior de Cell: el líder

Aquí echaremos un vistazo a la 'parte principal' de Cell: la porción de silicio encargada de comandar al resto. El nombre de este componente es **PowerPC Processor Element** (PPE), y puedes concebirlo como el MIPS R5900 del Emotion Engine.

#### Composición del PPE

¿Recuerdas cómo dividí Cell en distintas áreas antes? Lo mismo puede hacerse con el PPE. IBM usa el término 'element' para describir la máquina independiente [@cpu-arevalo, p. 8], pero en su interior usa el término 'unit' para separar el circuito central de las interfaces que comunican con el resto de Cell.

![Diagrama simplificado del PowerPC Processor Element (PPE).](_diagrams/cell/ppe.png)

Dicho esto, el PowerPC Processor Element está _sorprendentemente_ compuesto de dos partes:

- **PowerPC Processing Unit** (PPU): es la parte lógica del PPE (el 'núcleo'). ¡No lo confundas con la [PPU](nes#graphics) de Nintendo! (aunque comparten las mismas letras del alfabeto latino... en el mismo orden...).
- **PowerPC Processor Storage Subsystem** (PPSS): la gran interfaz que conecta el PPU con el mundo exterior. Además, proporciona nada menos que **512 KB de caché L2**.

Como puedes ver, el diseño del PPE (y del resto de Cell) es bastante modular, siguiendo los principios del diseño RISC. Pronto verás que esa modularidad se aplica incluso dentro del PPU.

#### El PowerPC Processing Unit

A continuación vamos a echar un vistazo al interior del PPU. Como recordatorio: hemos entrado en Cell, luego en el PPE y, finalmente, en el PPU. Lo analizaremos como cualquier otro núcleo de CPU.

##### Una arquitectura familiar {.tabs .active}

Para empezar, el PPU no se construyó desde cero, sino que **reutiliza tecnología PowerPC ya existente**. Sin embargo, a diferencia de [iteraciones anteriores](gamecube#the-powerpc-gekko) en las que IBM tomaba un procesador ya existente y lo actualizaba a medias para cumplir nuevos requisitos, el PPE no replica ningún diseño de CPU previo. En cambio, IBM construyó una nueva CPU que sigue la versión 2.02 de la especificación PowerPC (que resulta ser la última especificación PowerPC antes de rebautizarse como 'Power ISA'). En resumen, no encontrarás el diseño del PPU en ningún chip de aquella época, aunque se programa con el mismo código máquina que otros chips PowerPC.

¿Por qué eligió IBM la tecnología PowerPC para desarrollar un chip de alto rendimiento? Sencillo: PowerPC es una plataforma madura [@cpu-koranne] que disfrutó de unos diez años de pruebas y revisiones por parte de la base de usuarios de Macintosh, cumple todos los requisitos de Sony y, en caso de necesidad, puede adaptarse a distintos entornos. Por último, el uso de una arquitectura bien conocida es una buena noticia para los compiladores y bases de código existentes, lo que, para una nueva consola, supone una ventaja de partida considerable.

Vale la pena mencionar que IBM fue uno de los autores de los primeros chips PowerPC, junto con Motorola y Apple (recuerda la [alianza AIM](gamecube#tab-1-2-reaching-the-average-user)). Con todo, a principios de los 2000, los denominados miembros de la alianza ya [trabajaban por separado](gamecube#tab-2-3-the-band-splits-for-good), con Motorola/Freescale desarrollando una serie PowerPC distinta a la de IBM.

##### Características distintivas {.tab}

El PPU comparte historia con el PowerPC 970 (llamado _G5_ por Apple): ambos son descendientes del POWER4, un predecesor de PowerPC empleado principalmente en estaciones de trabajo y superordenadores. Esto se hará más evidente cuando muestre las unidades de ejecución modularizadas. Supone un cambio radical respecto a la [CPU 750 del GameCube](gamecube#the-powerpc-gekko), en la que Motorola tuvo un aporte considerable y que IBM luego modificó ligeramente.

Volviendo al tema, el PPU es un **procesador de 64 bits completo**. Esto significa:

- El tamaño de una palabra es de **64 bits**.
- Dispone de **registros de uso general de 64 bits** (32 en total).
- El bus de datos tiene _al menos_ 64 bits de ancho: en las siguientes secciones del artículo verás que es **mucho más ancho** que eso, pero por ahora quédate con que transferir palabras de 64 bits **no penalizará el rendimiento**.
- El bus de direcciones tiene 64 bits de ancho: en teoría, la CPU puede acceder a hasta **16 exabytes de memoria**. En la práctica, esto es muy costoso si la máquina no alberga toda esa memoria, así que las CPU modernas delegan el direccionamiento en la Unidad de Gestión de Memoria (MMU, del inglés *Memory Management Unit*) para dar más usos al bus de direcciones.

Por último, el PPU implementa la **PowerPC ISA versión 2.02**, incluyendo opcodes opcionales para la raíz cuadrada en punto flotante [@cpu-prog_tut, p. 23]. Además, se ha ampliado con un grupo de instrucciones SIMD llamado **Vector/SIMD Multimedia Extension** (VMX). Por otro lado, algunos elementos de la especificación original están ausentes, como el modo little-endian (de hecho, Cell solo opera en big-endian) y un puñado de opcodes.

#### Los bloques del PPU {.tabs-close}

Si aplicamos una visión 'microscópica' al PPU, podemos observar que esta unidad está compuesta de distintos bloques o subunidades que realizan operaciones independientes (leer valores de memoria, ejecutar operaciones aritméticas, etc.). Las capacidades del PPU vienen determinadas por lo que cada bloque puede hacer y cómo:

##### Instrucciones {.tabs .active}

![Diagrama simplificado de la Instruction Unit (IU).](_diagrams/cell/ppu/iu.png){.tab-float}

El primer bloque se denomina **Instruction Unit** (IU) y, como su nombre indica, lee instrucciones de la caché L2 y señaliza a otras unidades para que realicen la operación solicitada. Al igual que [sus contemporáneos i686](xbox#cpu), parte del set de instrucciones se interpreta mediante [**microcode**](nintendo-64#tab-1-1-reality-signal-processor) (la IU incorpora una pequeña ROM a tal efecto). La IU también alberga **32 KB de caché L1 para instrucciones**.

El despacho de instrucciones se lleva a cabo mediante un pipeline de **12 etapas**, aunque en la práctica el número total de etapas varía considerablemente en función del tipo de instrucción. Por ejemplo, el bloque de **predicción de ramificaciones** puede saltarse gran parte de ellas. Si combinamos la IU con las unidades vecinas, el número final de etapas suele rondar las **24** (sí, es un número elevado, pero recuerda que Cell funciona a 3,2 GHz).

Ahora viene lo interesante: la IU soporta **dual-issuing**, lo que significa que en determinadas condiciones puede despachar hasta dos instrucciones al mismo tiempo, mejorando considerablemente el rendimiento. En la práctica, sin embargo, hay muchas condiciones para que esto funcione, así que los programadores y compiladores son los responsables de optimizar sus rutinas para que la secuencia de instrucciones pueda aprovechar esta función. Por cierto, el dual-issuing ya lo habían implementado [CPUs anteriores](gamecube#the-powerpc-gekko) y el término varía según el fabricante; aquí he usado la definición de IBM.

Y por si fuera poco, la IU también es **multi-threaded**: la unidad puede ejecutar dos secuencias de instrucciones distintas (llamadas 'threads') al mismo tiempo. En realidad, la IU alterna entre los dos threads en cada ciclo, dando la apariencia de multi-threading. Esta técnica se conoce históricamente como **Simultaneous Multi-Threading** (SMT) o _hyper-threading_, tal como Intel lo denominaría más tarde. El multi-threading de IBM mitiga efectos no deseados como los [pipeline stalls](nintendo-64#tab-3-1-pipeline-stalls), ya que la CPU deja de bloquearse si una instrucción atasca el flujo. Para implementar el multi-threading, los ingenieros de IBM duplicaron los recursos internos de la IU, incluyendo los registros de uso general (antes dije que hay 32 registros disponibles; eso es por thread; en realidad hay 64 en total), pero los recursos que no forman parte de la especificación PowerPC (como la caché L1 y L2, y las interfaces) siguen siendo compartidos. Por tanto, este último grupo es de un solo thread.

En definitiva, combinando el dual-threading con el dual-issuing, el PPU puede ejecutar **hasta cuatro instrucciones por ciclo**. Aunque solo aplique en el mejor de los casos, sí abre oportunidades de optimización que los usuarios acabarán notando en la tasa de frames del juego.

##### Gestión de memoria {.tab}

![Diagrama simplificado de la Load-Store Unit (LSU) y sus unidades vecinas.](_diagrams/cell/ppu/lsu.png){.tab-float}

Los siguientes bloques otorgan al PPU la capacidad de ejecutar instrucciones de carga y almacenamiento, y de gestionar la memoria.

Para empezar, la **Load and Store Unit** (LSU) ejecuta los opcodes de 'load' y 'store' respaldados por **32 KB de caché L1 de datos**. Por tanto, esta unidad tiene acceso directo a la memoria y al fichero de registros.

Además, la LSU está acoplada a una Unidad de Gestión de Memoria (MMU, del inglés *Memory Management Unit*), algo habitual en el hardware contemporáneo. En pocas palabras, la MMU gestiona el direccionamiento de memoria mediante un [mapa de direcciones virtuales](nintendo-64#memory-management) combinado con [protección de memoria](playstation-portable#a-focused-memory-management). Para mejorar lo segundo, esta MMU en particular incluye una **segment unit**, que agrupa las direcciones de memoria en rangos denominados 'segments'. Para evitar degradaciones de rendimiento en el proceso, se incluyen un Translation Lookaside Buffer (TLB) (que cachea las direcciones traducidas) y un Segment Lookaside Buffer (SLB) (que cachea los segmentos).

##### Aritmética {.tab}

![Diagrama simplificado de las unidades que realizan operaciones aritméticas.](_diagrams/cell/ppu/vsu_fxu.png){.tab-float}

Solo quedan dos unidades más del PPU por explicar: las que realizan los cálculos matemáticos que cualquier juego necesita.

La primera es una **Fixed-Point Integer Unit** (FXU) tradicional. La FXU realiza aritmética de enteros: división, multiplicación, rotación de bits (similar al desplazamiento de bits, pero los bits descartados regresan al otro extremo) y conteo de ceros iniciales (útil para normalizar coordenadas de vértices, por ejemplo). Su pipeline tiene 11 etapas.

Si observas el diagrama, verás que el FXU, la LSU y la MMU están agrupados en una sola unidad llamada **Execution Unit** (XU), porque comparten el mismo fichero de registros.

La segunda unidad es bastante más interesante: la **Vector/Scalar Unit** (VSU) realiza operaciones con números en punto flotante y vectores. Está compuesta por una **FPU de 64 bits** (que sigue el estándar IEEE 754) y una **Vector/SIMD Multimedia Extension unit** (VXU), que ejecuta un conjunto de instrucciones SIMD denominado _VMX_. Esta última opera con vectores de 128 bits formados por entre dieciséis valores de 8 bits y cuatro de 32 bits [@cpu-arevalo]. Puede que ya hayas oído hablar de esta extensión: 'VMX' es el nombre de IBM para el 'AltiVec' de Motorola o el 'Velocity Engine' de Apple (_que vivan las marcas registradas_). Ahora bien, las verdaderas capacidades SIMD competitivas de Cell se encuentran en otro procesador, así que ¡no te relajes todavía!

#### Un vistazo general al PPE {.tabs-close}

Acabas de ver cómo funciona el PPE y de qué está hecho, pero ¿qué significa esto para un desarrollador?

Al fin y al cabo, el PowerPC Processor Element es solo un procesador de uso general, pero la clave está en que **no está pensado para funcionar en solitario**. ¿Recuerdas ese amplio bus principal (el EIB)? IBM diseñó el PPE para que los ingenieros puedan combinarlo con otros procesadores y acelerar aplicaciones concretas (HPC, gráficos 3D, seguridad, simulaciones científicas, redes, procesamiento de vídeo, etc.). Como este análisis trata sobre la PlayStation 3, verás que el resto de Cell está concebido pensando en los gráficos y la física, y el resto del artículo refleja ese propósito.

### Fuera de Cell: la memoria principal

Salgamos de Cell por un momento, porque da igual lo bueno que sea el PPE si no tenemos un espacio de trabajo adecuado (memoria) donde ponerlo a trabajar.

Sony incorporó **256 MB de XDR DRAM** en la placa base... Pero, de nuevo, ¿qué significa eso? Para saberlo, veamos cómo funcionan los bloques de memoria y cómo se conectan a Cell.

![Cell junto a los cuatro chips XDR DRAM de 64 MB.](cell_mem.jpg){.open-float}

El tipo de memoria instalada se llama **Extreme Data Rate** (XDR). Puede que reconozcas la XDR DRAM como la sucesora de la (gafada) **RDRAM** del [Nintendo 64](nintendo-64) y el [PlayStation 2](playstation-2#a-recognisable-memory-choice). ¡Pero no te adelantes a las conclusiones!

Rambus, como cualquier empresa, mejora sus inventos. Su tercera revisión (XDR) funciona ahora a tasa óctuple (cuatro veces la tasa de su rival, la DDR DRAM) [@cpu-xdr]. La latencia ya no supone un problema: según las hojas de datos de uno de sus fabricantes, la latencia de la XDR se sitúa entre 28 ns y 36 ns [@cpu-k4y5016, p. 2], casi [diez veces más rápida](nintendo-64#memory-design) que los chips RDRAM de primera generación.

{.close-float}

La primera revisión de la placa base de la PlayStation 3 contiene cuatro chips de 64 MB, gestionados en pares. La XDR se conecta a Cell mediante dos buses de 32 bits, uno por cada par. Así, cada vez que el PPU escribe una palabra (dato de 64 bits), esta se reparte entre dos chips XDR. Estos últimos funcionan a 400 MHz [@cpu-ram].

![Diagrama de la arquitectura de memoria de Cell.](_diagrams/cell/memory.png)

Cell se conecta a los chips XDR mediante el **Memory Interface Controller** (MIC), otro componente dentro de Cell (como el PPE). Además, el MIC almacena en buffer las transferencias de memoria para mejorar el ancho de banda, aunque tiene una limitación importante: la gran alineación de bytes. En esencia, el tamaño mínimo de datos para transferencias del MIC es de **128 bytes**, lo que funciona bien para lecturas o escrituras secuenciales. Pero si los datos son inferiores a 128 B o requieren alternar entre escritura y lectura, aparecen penalizaciones de rendimiento.

Dicho esto, ¿es el MIC un cuello de botella o un acelerador? Hay que ponerlo en perspectiva: la optimización del ancho de banda es crítica en sistemas con gran demanda de datos. Ya hemos visto soluciones como el [write-gather pipe](gamecube#ibms-enhancements) o el [write back buffer](playstation-2#preventing-past-mishaps), así que el MIC es simplemente una nueva respuesta a un problema recurrente. En cualquier caso, Sony afirma que la tasa de transferencia es de 25,6 GB/s. Con todo, hay demasiados factores que condicionarán la tasa real en la práctica (ya has visto lo complejo que resulta mover datos de un lugar a otro dentro de Cell).

Esto es todo en cuanto a la RAM principal, pero hay más memoria en otro lugar: el disco duro. El PS3 también permite que los juegos utilicen **2 GB** de su disco duro interno como área de trabajo (de manera similar a lo que [el Xbox original ofrecía](xbox#memory-layout)) [@cpu-files].

### El interior de Cell: los asistentes

[Ya hemos visto](playstation-2#the-leader) cómo Sony siempre acompaña a un procesador de uso general (el PPE en este caso) con aceleradores para alcanzar un rendimiento de juego aceptable (las VPU y la IPU en el caso del PS2; el GTE y el MDEC en el del PS1). Se trata de una práctica habitual en el hardware de consolas, ya que las unidades de uso general pueden realizar una amplia gama de tareas, pero no están especializadas en ninguna. Las consolas de videojuegos solo requieren un subconjunto de habilidades (física, gráficos y audio, por ejemplo), así que los coprocesadores les proporcionan esa especialización.

> [El PPE] es una versión recortada para reducir el consumo. Por eso no tiene la potencia que ves en, digamos, un Pentium 4 (...) Si tomas el código que hoy corre en tu Intel o AMD, sea cual sea su potencia, y lo recompilas para Cell, funcionará hoy mismo aquí, sin problema. Quizás tengas que cambiar alguna que otra librería, pero funcionará. Sin embargo, irá un 60 %, un 50 % más lento, y la gente dice «¡Dios mío! ¡Este procesador Cell es una porquería!» **Pero es porque solo estás usando esa única pieza** [@cpu-mit].
>
> -- <cite>Dr. Michael Perrone, responsable del departamento de Soluciones Cell del IBM TJ Watson Research Center</cite>

Los aceleradores incluidos en el Cell de la PS3 son los **Synergistic Processor Elements** (SPE). Cell incluye **ocho de ellos**, aunque uno se deshabilita en el arranque de la consola. Esto se debe a que la fabricación de chips exige un grado de precisión excepcional (Cell usó inicialmente el proceso de fabricación de 90 nm) y la maquinaria no es perfecta. En lugar de desechar circuitos con menos de un 10 % de defectos, Cell incluye un SPE de reserva. Así, si uno sale defectuoso, no se descarta el chip entero. Ese SPE de reserva quedará siempre desactivado, independientemente de si funciona o no (Sony no puede tener en el mercado dos PS3 diferentes).

#### Composición del SPE

El Synergistic Processor Element (SPE) es un pequeño ordenador independiente dentro de Cell, comandado por el PPE. ¿Recuerdas lo que expliqué antes sobre la adopción de elementos de la computación homogénea? Pues bien, estos coprocesadores son en cierta medida de uso general y no están restringidos a una única aplicación, así que podrán asistir en una amplia gama de tareas, siempre que los desarrolladores sepan programarlos correctamente.

![Diagrama simplificado del Synergistic Processor Element (SPE); hay ocho dentro de Cell (uno deshabilitado).](_diagrams/cell/spe.png)

Al igual que hicimos con el PPE, echaremos un vistazo al SPE. La explicación es más breve, así que si al final quieres saber más sobre los SPE, consulta la sección 'Fuentes' al final del artículo. Dicho esto, comencemos...

El SPE es un procesador que sigue una estructura similar a la del PPE, compuesto de dos partes:

##### El Memory Flow Controller {.tabs .active}

El **Memory Flow Controller** (MFC) es el bloque que interconecta el núcleo con el resto de Cell, equivalente al PowerPC Processor Storage Subsystem (PPSS) del PPE. Su función principal es mover datos entre la memoria local del SPU y la memoria principal de Cell, y mantener al SPU sincronizado con sus vecinos.

Para cumplir sus funciones, el MFC incorpora un controlador DMA para gestionar la comunicación entre el EIB y la memoria local del SPU. El MFC también alberga otro componente llamado **Synergistic Bus Interface** (SBI), situado entre el bus EIB y el controlador DMA. Es un circuito muy complejo de resumir, pero básicamente interpreta los comandos y datos recibidos del exterior y señaliza a las unidades internas del SPE. Como puerta de entrada a Cell, el SBI opera en dos modos: *bus master* (donde el SPE está adaptado para solicitar datos del exterior) o *bus slave* (donde el SPE está configurado para recibir órdenes del exterior).

Como curiosidad, dado que el límite de los paquetes del EIB es de hasta 128 bits, el bloque de Acceso Directo a Memoria del MFC solo puede mover hasta 16 KB de datos por ciclo; de lo contrario, el EIB lanza una excepción de 'Bus Error' durante la ejecución [@cpu-hpc, p. 4].

##### El Synergistic Processor Unit {.tab}

El **Synergistic Processor Unit** (SPU) es la parte del SPE donde reside el procesador central, equivalente al 'PPU' cuando hablamos del PPE.

Al contrario que el PPU, el SPU está aislado del resto de Cell. Por tanto, no existe memoria compartida entre el PPU y los demás SPU. En cambio, el SPU cuenta con memoria local que usa como espacio de trabajo. Sin embargo, el contenido de esa memoria local puede trasladarse de un lado a otro mediante el MFC.

En cuanto a funcionalidad, el SPU es bastante más limitado que el PPU. Por ejemplo, el SPU no incluye funciones de gestión de memoria (traducción de direcciones y protección de memoria) ni funciones avanzadas como la predicción dinámica de ramificaciones. Aun así, rinde de manera excepcional en el procesamiento vectorial.

Para programar esta unidad, los desarrolladores utilizan el PPU para invocar rutinas proporcionadas por el sistema operativo de la PlayStation 3; estas cargan el ejecutable escrito específicamente para el SPU en el SPU elegido y le dan la señal de inicio de ejecución. Después, el PPU mantiene una referencia del thread del SPU para fines de sincronización [@cpu-spuprog].

#### Arquitectura del SPU {.tabs-close}

Al igual que cualquier CPU, el Synergistic Processor Unit (SPU) se programa mediante una arquitectura de set de instrucciones (ISA, del inglés *Instruction Set Architecture*). Tanto el SPU como el PPU siguen la metodología RISC; sin embargo, a diferencia del PPU (que implementa una ISA PowerPC), la ISA del SPU es propietaria y está compuesta principalmente por un conjunto de instrucciones de tipo SIMD. Como resultado, el SPU dispone de **128 registros de uso general de 128 bits** que albergan vectores formados por valores en punto fijo o flotante de 32/16 bits. Por otro lado, para ahorrar memoria, las instrucciones del SPU son mucho más compactas: solo **32 bits de longitud**. La primera parte contiene el opcode y el resto puede referenciar hasta tres operandos que se calculan en paralelo.

Esto recuerda mucho a la anterior [Vector Floating Point Unit](playstation-2#co-cpus) del PS2, aunque mucho ha cambiado desde entonces. Por ejemplo, el SPU no exige que los desarrolladores aprendan un nuevo lenguaje ensamblador propietario: IBM y Sony proporcionaron kits de herramientas para programar los SPU en C++, C o ensamblador.

En cuanto al diseño, este procesador no ejecuta todas las instrucciones con la misma unidad; la ejecución se divide en dos bloques o 'pipelines de ejecución', llamados **Odd Pipeline** y **Even Pipeline**. Estos dos pipelines ejecutan distintos tipos de instrucciones, lo que permite al SPU despachar **dos instrucciones por ciclo** cuando es posible. Por otro lado, el SPU nunca realiza dual-issuing con instrucciones que dependan entre sí, mitigando así los [data hazards](playstation#delay-galore) que puedan surgir.

Veamos ahora los dos pipelines [@cpu-hyesoon]:

##### Odd Pipeline {.tabs .active}

![Diagrama simplificado del odd pipeline.](_diagrams/cell/spu/odd.png){.tab-float}

El Odd pipeline ejecuta la mayoría de las instrucciones, excepto las aritméticas.

En primer lugar, la **SPU Load/Store unit** (SLS) realiza tres funciones esenciales:

- Alberga **256 KB de memoria local** para almacenar instrucciones y datos. El tipo de memoria instalada es single-ported (teniendo en cuenta la importancia de esta área, es un poco decepcionante que no usaran chips dual-ported...). Además, el bus de direcciones tiene 32 bits de longitud.
- Ejecuta instrucciones de carga y almacenamiento.
- Reenvía instrucciones a otro bloque para su despacho.

Conviene tener en cuenta que solo hay **256 KB disponibles para almacenar el programa**. Como los programas SPU pueden compilarse en C/C++, no es fácil predecir el tamaño del binario resultante. Por este motivo, se recomienda que los desarrolladores asuman que solo tienen la mitad de la memoria disponible (128 KB) [@cpu-hpc, p. 4], lo que deja suficiente margen para que el código compilado ocupe el espacio que necesite, aunque a costa del almacenamiento y la eficiencia.

Por último, también hay una **SPU Channel and DMA Transport** (SSC), que el Memory Flow Controller usa para rellenar y/o leer la memoria local, y una _pequeña_ **Fixed-Point Unit** que solo realiza reorganización y rotación de vectores.

##### Even Pipeline {.tab}

![Diagrama simplificado del even pipeline.](_diagrams/cell/spu/even.png){.tab-float}

El Even pipeline destaca por sus capacidades aritméticas.

Aquí encontramos una _auténtica_ **Fixed-point Unit** (FXU) que realiza aritmética básica, operaciones lógicas (AND, OR, etc.) y desplazamientos de palabras.

Por último, tenemos una **Floating-point Unit** (FPU) que realiza operaciones en precisión simple (32 bits, `float`), doble precisión (64 bits, `double`) y enteros (32 bits, `int`). Esta sigue el estándar IEEE con algunas desviaciones (los floats se comportan de forma similar al [PS2](playstation-2#the-leader)).

### El interior de Cell: modelos de programación {.tabs-close}

A medida que llegamos al final de Cell, quizás te preguntes cómo se supone que los desarrolladores programan este _monstruo_. Pues bien, de manera similar a los [modelos de programación anteriores](playstation-2#you-define-the-workflow) ideados para el Emotion Engine, IBM propuso las siguientes metodologías [@cpu-prog_tut, p. 13]:

#### Enfoques centrados en el PPE

![Representación del patrón multietapa, donde el PPE asigna una tarea que se va pasando de SPE en SPE hasta ser devuelta con los datos procesados.](_diagrams/cell/programming/multistage.png){.tabs-nested .active title="Multietapa"}

![Representación del patrón en paralelo, donde el PPE asigna una subtarea a cada SPE, que a su vez devuelve los datos procesados, y el PPE los combina.](_diagrams/cell/programming/parallel.png){.tab-nested title="Paralelo"}

![Representación del patrón de servicios, donde el PPE asigna una tarea distinta a cada SPE y cada uno trabaja de forma independiente para cumplirla.](_diagrams/cell/programming/services.png){.tabs-nested-last title="Servicios"}

Los enfoques centrados en el PPE son un conjunto de patrones de programación que depositan las principales responsabilidades en el PPE y reservan los SPE para aliviar la carga de trabajo. Hay tres patrones posibles:

- **Modelo de pipeline multietapa**: el PPE envía trabajo a un único SPE, que realiza los cálculos necesarios y pasa los resultados al siguiente SPE. Esto continúa hasta que el último SPE de la cadena devuelve los datos procesados al PPE.
  - Por razones obvias, IBM no recomienda este diseño para tareas primarias, ya que requiere un considerable ancho de banda y tiende a ser difícil de mantener.
- **Modelo de etapas en paralelo**: el PPE divide su tarea principal en subtareas independientes y envía cada una a un SPE distinto. Cada SPE devuelve los datos procesados al PPE al terminar; este los combina para producir el resultado final.
- **Modelo de servicios**: cada SPE recibe una tarea concreta (decodificación MPEG, streaming de audio, proyección en perspectiva, iluminación de vértices, etc.) y el PPE se encarga de enviar los datos brutos al SPE designado. Mientras espera, el PPE realiza otras funciones.
  - Aunque esto implica que cada SPE tendrá un único trabajo, esa asignación no es permanente: el PPE debe reasignar distintos trabajos sobre la marcha según cambien las necesidades del programa.

#### Enfoques centrados en el SPE

![Representación del patrón centrado en el SPE, donde cada SPE gestiona su propia funcionalidad e interactúa con el PPE solo para obtener un recurso.](_diagrams/cell/programming/spe_centric.png)

En lugar de usar los SPE para servir al PPE, se invierte el esquema. Mediante la unidad DMA interna, los SPE leen y ejecutan tareas almacenadas en la memoria principal, mientras que el PPE se limita a la gestión de recursos.

Este modelo es bastante más radical que los anteriores, en el sentido de que los patrones previos se acercan más al paradigma tradicional tipo PC de 'procesador de uso general con coprocesadores'. Por ello, las bases de código que implementan algoritmos centrados en SPE pueden resultar más difíciles de portar a otras plataformas.

### Conclusión

Como puedes imaginar, aunque el diseño multinúcleo de Cell acelera técnicas emergentes como la [generación procedural](playstation-2#infinite-worlds), ninguno de estos diseños es particularmente sencillo de implementar, sobre todo teniendo en cuenta que los estudios de videojuegos prefieren bases de código que puedan compartirse entre distintas plataformas.

Para ilustrarlo, los desarrolladores del Unreal Engine 3 (Epic Games) pusieron de manifiesto las limitaciones de los SPE al intentar implementar su sistema de detección de colisiones [@cpu-unreal]. Su diseño se basa en el Particionado de Espacio Binario (BSP, del inglés *Binary Space Partitioning*), un algoritmo que depende en gran medida de las comparaciones (ramificaciones). Como los SPE no ofrecen predicción dinámica de ramificaciones como el PPU, su implementación decepcionó a los usuarios de PlayStation 3 al compararse con otras plataformas (el [Xbox 360](xbox-360#cpu) o los ordenadores [i686](xbox#tab-1-3-the-microarchitecture), ambos con técnicas de predicción coherentes en todos sus núcleos). Por ello, Epic Games tuvo que recurrir a optimizaciones adicionales exclusivas de Cell.

Supongo que es solo cuestión de tiempo, paciencia y mucho aprendizaje para que los ingenieros de software expriman todo el potencial de Cell. Sin embargo, la historia ha demostrado que [eso no es viable para todos los estudios](sega-saturn#as-a-challenging-3d-console), lo que me hace preguntarme si esa es la razón por la que el hardware de consolas actual (a partir de 2021) se ha homogeneizado tanto.

## Gráficos

Si creías que Cell, con todas sus peculiaridades, podía encargarse de cada tarea de esta consola, permíteme contarte algo hilarante: **Sony incorporó un chip separado para los gráficos 3D**.

::: {.subfigures .tabs-nested}

![Uncharted 3: Drake's Deception (2011).](games/uncharted3.jpg){.active title="Uncharted"}

![The Elder Scrolls V: Skyrim (2011).](games/skyrim.jpg){title="Skyrim"}

![Killzone 3 (2011).](games/killzone.jpg){title="Killzone"}

![One Piece: Pirate Warriors (2012).](games/onepiece.jpg){title="One Piece"}

Ejemplos de juegos de PS3. Todos renderizados a su resolución máxima (1280×720 píxeles).

:::

Parece que, incluso con un chip de superordenador, Sony tuvo que recurrir a una GPU para completar la PlayStation 3. Esto te hace preguntarte si IBM/Sony/Toshiba toparon con un muro al intentar escalar Cell más allá, y Sony no tuvo más opción que pedir ayuda a una empresa de gráficos.

> Habíamos creado el equipo ICE [Initiative For A Common Engine] con la intención de desarrollar una tecnología base que pudiera compartirse entre todos los estudios propios (...) Durante un tiempo, el [PS3] no tenía GPU; se pretendía que todo corriera sobre los SPU. El equipo ICE demostró a Japón que era simplemente imposible. Habría sido ridículo. En términos de rendimiento, habría sido un desastre. Por eso finalmente añadieron la GPU, ya hacia el final [@graphics-ign_naughtydog].
>
> -- <cite>Fuentes anónimas de Naughty Dog</cite>

Lo que sí sé con certeza es que la PS3 incluye un chip GPU fabricado por **Nvidia**, diseñado para aliviar parte del pipeline de gráficos. El chip se llama **Reality Synthesizer** o 'RSX' y funciona a **500 MHz** [@graphics-rsx_dev]. Su frecuencia de reloj resulta llamativa comparada con la de Cell (3,2 GHz), aunque enseguida verás que la GPU está mejor equipada para calcular grandes cantidades de operaciones en paralelo. Se trata, pues, de encontrar el equilibrio entre Cell y RSX a la hora de construir el pipeline de gráficos (aunque debo admitir que esto suena más sencillo en teoría que en la práctica).

A continuación realizaré el mismo nivel de análisis que hice con Cell, esta vez centrado en RSX y sus capacidades gráficas.

### Visión general

Han pasado cinco años desde que Nvidia presentó la gama [GeForce3/NV30](xbox#graphics) en 2001, época en la que el mercado lo disputaban actores como 3dfx, S3 y ArtX/ATI. Sin embargo, en los años siguientes, el número de empresas fue reduciéndose paulatinamente hasta que, en 2006, solo ATI y Nvidia seguían siendo los principales proveedores de tarjetas gráficas en el mercado de PC.

![El chip RSX junto a Cell.](rsx.jpg){.open-float}

El RSX hereda tecnología Nvidia preexistente; se dice que está basado en el modelo 7800 GTX para PC, que implementa la arquitectura **GeForce7** (o NV47) [@graphics-nvarch], también denominada 'Curie'.

En mi [anterior análisis del Xbox](xbox#graphics) hablé de la GeForce3 y sus pixel shaders debutantes, ¿qué ha cambiado desde entonces? Ha habido altibajos, pero sobre todo cambios incrementales, sin nada demasiado revolucionario comparado con los pixel shaders de la GeForce3.

{.close-float}

Por otro lado, mientras que el 7800 GTX usa el protocolo PCI Express para comunicarse con la CPU, el RSX ha sido rediseñado para funcionar con un protocolo propietario llamado **Flex I/O** [@graphics-gschwind], una interfaz específica dentro de Cell diseñada para conectar chips vecinos. Flex I/O opera en dos modos:

- Modo **BIC** para conectar otros procesadores Cell (pensado para entornos multiprocesador).
- El modo más lento **IOIF** para conectar hasta dos periféricos, uno 'rápido' y otro 'lento'.

Por desgracia, el RSX no es Cell, así que usa el protocolo IOIF, por la ranura más rápida.

A modo de comparación, el IOIF se comporta como un bus paralelo de 32 bits con un ancho de banda teórico de hasta 20 GB/s, mientras que el PCI Express usado en el 7800 GTX (x16 1.0) es un bus serie de 16 bits con un ancho de banda teórico de hasta 4 GB/s.

### Organización del contenido

El RSX tiene a su disposición **256 MB de GDDR3 SDRAM** dedicada. Sorprendentemente, es el [mismo tipo de memoria](wii#what-about-memory) que se encuentra en la Wii. El bus de memoria opera a **650 MHz** con un ancho de banda teórico de hasta **20,8 GB/s**.

![Ejemplo de cómo se organizan los datos en la memoria disponible. Fíjate en cómo el RSX puede acceder a su contenido desde distintos chips de memoria.](_diagrams/rsx/content.png)

En esos 256 MB, Cell puede colocar todo lo que RSX necesitará para renderizar un frame: datos de vértices, shaders, texturas y comandos. Además, gracias al bus Flex I/O de Cell, RSX también puede utilizar los ya mencionados 256 MB de XDR DRAM (la RAM principal de la CPU) como espacio de trabajo, aunque con ciertas penalizaciones de rendimiento. Esto resulta práctico cuando, por ejemplo, el frame renderizado vaya a ser postprocesado por un SPU.

Como puedes ver, aunque esta consola no implementó una arquitectura UMA, **sigue siendo posible distribuir los datos gráficos entre distintos chips de memoria** si los programadores así lo deciden. Lo menciono porque ojalá muchos de los 'explicadores técnicos' se informaran más sobre esta característica antes de soltar afirmaciones demasiado simplistas como «la PS3 era limitada porque no tenía UMA». Puede que eso sea cierto en ciertos casos, pero si no se mencionan estas particularidades, esa afirmación genérica es, en mi opinión, **engañosa**.

Por último, RSX admite muchas formas de optimización de datos para ahorrar ancho de banda; entre ellas, la compresión de color 4:1, la z-compression y el modo 'tiled' (lo explicaré con más detalle más adelante).

### Construyendo un frame

Veamos ahora cómo procesa y renderiza escenas 3D el RSX.

![Visión general del pipeline del RSX.](_diagrams/rsx/pipeline.png)

Su modelo de pipeline es muy similar al de la [GeForce3](xbox#architecture-and-design), pero potenciado con cinco años de progreso tecnológico. Por eso recomiendo leer ese artículo primero, ya que este se centrará en las novedades; también es recomendable leer sobre la [GPU de la PlayStation Portable](playstation-portable#functionality), porque muchos de los nuevos desarrollos y necesidades coinciden con ese chip. Dicho esto, veamos qué tenemos aquí... [@graphics-marcelina]

#### Comandos {.tabs .active}

![Diagrama de la etapa de comandos.](_diagrams/rsx/pipeline_commands.png){.tab-float}

Como en cualquier otra GPU, debe haber un bloque de circuito encargado de recibir las órdenes del exterior. En el RSX, esto lo gestionan dos bloques: **Host** y **Graphics Front End**.

El Host es el responsable de leer los comandos de la memoria (local o principal) y traducirlos en señales internas que comprenden los demás componentes del RSX; esto se realiza mediante cuatro subcomponentes:

- **Pusher**: lee los comandos gráficos de la memoria e [interpreta](playstation-portable#tab-1-1-commands) las instrucciones de ramificación. También contiene 1 KB de [prefetch buffer](game-boy-advance#memory-locations). Los comandos procesados se envían al FIFO Cache.
- **FIFO Cache**: almacena hasta 512 comandos decodificados por el Pusher de forma FIFO para proporcionar acceso rápido.
- **Puller**: como su nombre indica, extrae comandos del FIFO Cache cuando el RSX está listo para renderizar y los envía a la siguiente unidad.
- **Graphics FIFO**: almacena hasta ocho comandos que serán leídos por el Graphics Front End.

El Graphics Front End lee del Graphics FIFO y señaliza a las unidades necesarias dentro del RSX para que ejecuten las operaciones. Como recordarás, esto es equivalente al 'pfifo' de la GeForce3.

Como puedes ver, los comandos y datos pasan por múltiples buffers y cachés antes de llegar a su destino final. Esto es intencionado, ya que evita que el pipeline se detenga cuando distintas unidades y buses operan a diferentes velocidades. Así, la memoria en caché aprovecha el ancho de banda rápido siempre que es posible.

#### Vertex Shader {.tab}

![Diagrama del proceso de la etapa de vértices. Fíjate en que los Vertex Processing Engines (VPE) se omiten si los vértices no necesitan procesamiento adicional por parte del vertex shader.](_diagrams/rsx/pipeline_vertex.png){.tab-float}

La siguiente unidad es el bloque de **Geometry Processing**, una evolución del 'Vertex Block' de la GeForce3 que realiza la transformación de vértices. Sigue siendo programable mediante vertex shaders, una función ya ampliamente adoptada en la industria gráfica. Además, el límite de instrucciones se ha elevado a un mínimo de 512 (¡originalmente el límite era de 136!).

El bloque que ejecuta los shaders se llama **Vertex Processing Engine** (VPE) y puede procesar **un vértice por ciclo de reloj**. Por si fuera poco, hay **ocho VPE trabajando en paralelo**. Desde la serie GeForce6, Nvidia ha alineado su interfaz de programación de shaders con un modelo llamado 'Vertex Shader Model 3' o 'vs_3_0', un estándar desarrollado por Microsoft para sus librerías DirectX 9.0c [@graphics-vs3]. Los VPE también son compatibles con el modelo no propietario OpenGL 2.1 [@graphics-kilgard] y la variante propia de Nvidia (Cg) [@graphics-vp40].

Respecto a la GeForce3, disponemos de nuevas instrucciones para ramificaciones y llamadas a subrutinas. Además, el VPE incorpora cuatro samplers de textura que extraen colores de textura en esta etapa, por si los programadores quieren realizar operaciones sobre ellas en este bloque.

El bloque de Geometry Processing funciona de la siguiente manera:

1. El **Index Vertex Processor** (IDX) lee y cachea datos de vértices y texturas de la VRAM; después, envía los datos al VAB.
2. El **Vertex Attribute Buffer** (VAB) extrae datos de la caché del IDX y los redirige a cada VPE.
3. Cada VPE procesa los datos según el shader cargado. Este calcula **una instrucción de shader por ciclo de reloj**.
4. El resultado de cada VPE se envía al **Post Transform Cache**, que cachea los resultados para omitir cálculos idénticos sobre el mismo vértice. Esto solo aplica si se usan [índices de vértice](gamecube#tab-3-2-geometry) en lugar de datos de vértice.
5. El resultado final se almacena en el **Viewport Cull Unit** (VPC), que aplica recorte para descartar vértices fuera del viewport, y en la **Attribute RAM** (ATR), que cachea los atributos de vértice (textura, color, niebla, etc.) para ser leídos en las etapas siguientes.

#### Rasterización {.tab}

![Diagrama simplificado de la etapa de rasterización. El RSX integra distintas unidades para calcular los valores usados en la interpolación de píxeles y colores.](_diagrams/rsx/pipeline_rasterizer.png){.tab-float}

A continuación, toca convertir ([rasterizar](playstation#tab-3-3-rasterisation)) los vértices en píxeles. El rasterizador del RSX es bastante rápido: puede rasterizar hasta 8×8 píxeles (64) por ciclo y trabaja con frame buffers de hasta **4096×4096 píxeles** (aunque los desarrolladores suelen necesitar bastante menos).

El rasterizador acepta puntos, líneas (incluyendo strips y tipos cerrados), triángulos (incluyendo strips y fans), cuadriláteros y polígonos regulares. Como es habitual en esta generación de consolas, el rasterizador trabaja con **coordenadas de subpíxel**, donde los puntos de muestreo son semipuntos (`0,5`) de los píxeles. Esto permite a la unidad aplicar posteriormente métodos de antialiasing como el **Multisampling**. El Multisampling consiste en rasterizar la misma geometría varias veces pero desplazando unos pocos subpíxeles en cada pasada (el RSX admite cuatro modos de desplazamiento distintos) y luego calcula la media, lo que produce una imagen más suavizada.

Además, esta unidad también realiza **z-culling** mediante RAM dedicada dentro del RSX (con capacidad para unos tres millones de píxeles). Esto evita procesar píxeles y stencils ya renderizados y permite realizar el **early [z-test](nintendo-64#modern-visible-surface-determination)** sobre la geometría entrante.

Para rasterizar objetos 2D (sprites) se usa una unidad separada, aislada del pipeline 3D. En consecuencia, el RSX opera en dos modos, 2D y 3D, pero alternar entre ellos con frecuencia tiene un coste notable en cuanto al rendimiento.

#### Pixel Shader {.tab}

![Diagrama de la etapa de píxeles/fragmentos.](_diagrams/rsx/pipeline_pixel.png){.tab-float}

A continuación tenemos el bloque **Fragment Shader & Texture**, una unidad programable (mediante 'fragment programs' o 'shaders') que aplica el mapeado de texturas y otros efectos.

Como sucesor avanzado de las [unidades de textura de la GeForce3](xbox#tab-2-3-pixel), el nuevo bloque contiene **seis fragment units** (también llamadas 'pipes'), cada una de las cuales procesa texels de 2×2 (denominados 'quads'). Para organizar el trabajo concurrente de varias unidades, se incluye otro subcomponente llamado **Shader Quad Distributor** (SQD) que distribuye los quads a cada fragment unit. Después, cada fragment unit carga el fragment program.

Para ejecutar operaciones, cada pipe contiene la _enorme_ cantidad de **1536 registros de 128 bits**. Además, cada pipe puede procesar varios quads en paralelo (**multi-threading**), aunque el número de quads procesados en paralelo depende del número de registros asignados al fragment program (`n.º de threads = 1536 ÷ n.º de registros reservados por el shader`). En conjunto, pueden procesarse hasta 460 quads en paralelo. Además, hasta tres fragment pipes pueden procesar dos instrucciones al mismo tiempo (**dual-issuing**, como el PPU), siempre que las instrucciones no dependan entre sí.

Las fragment units ofrecen instrucciones aritméticas similares a las de la vertex unit, con la incorporación de opcodes relacionados con texturas, como múltiples tipos de lectura de texturas (ya que pueden codificarse con muchas estructuras y luego comprimirse) y desempaquetado. Al igual que el bloque de vértices, el fragment shader sigue el modelo Pixel Shader 3.0 de DirectX [@graphics-vs3], el perfil NV_fragment_program2 de OpenGL [@graphics-khronos] y el perfil 'fp40' de Cg [@graphics-fp40]. Todo ello para facilitar la programación y evitar tener que aprender APIs de bajo nivel desde cero.

Por último, dado que las unidades estarán constantemente leyendo fragmentos de texturas desde la VRAM o la RAM principal, este bloque incluye tres cachés de texturas: **4 KB de caché L1** por pipe, **48 KB de caché L2** para lecturas de VRAM; y **96 KB de caché L2 para la RAM principal**. Ten en cuenta que la caché de RAM principal es considerablemente mayor: fue una decisión deliberada para compensar la mayor latencia.

#### Operaciones de píxel {.tab}

![Etapa de postprocesado.](_diagrams/rsx/pipeline_post.png){.tab-float}

Antes de escribir los resultados en el frame buffer (almacenado en VRAM o en RAM principal), un bloque final llamado **Raster Operation Block** (ROP) realiza las comprobaciones definitivas sobre los píxeles resultantes.

Hay dos conjuntos de ROP compuestos por cuatro bloques cada uno (ocho en total). Cada grupo realiza z-testing, alpha blending y la escritura final en memoria. En conjunto, este circuito puede procesar hasta 16 valores Z y 8 colores de píxel por ciclo. Curiosamente, la variante para PC Nvidia 7800 GTX tiene 16 ROP en lugar de 8; ¿quizás ese recorte se hizo para priorizar el ancho de banda de memoria consumido por los SPU?

Para ahorrar aún más ancho de banda, los ROP también ofrecen compresión de color y z-compression. Además, existe un **modo Tiling** para optimizar el acceso a memoria del codificador de vídeo. En este modo, el frame buffer se almacena en bloques continuos de 128 B ordenados de la misma manera en que se emitirían/escanearían. Esto evita que la GPU realice intercambios de páginas (usados para el direccionamiento de memoria) mientras transmite el frame buffer para su visualización, mejorando así el ancho de banda. Estos 'tiles' se almacenan en ubicaciones marcadas de la memoria, exclusivas para este tipo de direccionamiento.

### Una salida de vídeo unificada {.tabs-close}

Se acabaron los días de los conectores de vídeo propietarios de cada consola y las docenas de señales analógicas comprimidas en un único conector para adaptarse a cada región del planeta. El PlayStation 3 incorporó por fin una señal de vídeo unificada que pronto sería adoptada en todo el mundo: la **High Definition Media Interface** (HDMI), utilizada para transferir audio y vídeo al mismo tiempo.

![Parte trasera de la PS3: la salida HDMI a la izquierda y, en el otro extremo, el antiguo Multi A/V para la salida de vídeo analógico.](cecha/back.jpg){.open-float}

El conector HDMI consta de 19 pines [@graphics-hdmi_wiki], todos en un único conector. Transmite una señal digital, lo que significa que la imagen y el audio se emiten mediante ceros y unos discretos (y no mediante un rango de valores continuos como en las señales analógicas). Por tanto, no sufre las interferencias ni la degradación de imagen que padecían los equipos anteriores, como los artefactos en pantalla provocados por cables SCART de mala calidad.

Aún hoy, el protocolo HDMI se revisa continuamente [@graphics-hdmi_spec], con nuevas versiones de la especificación que ofrecen más funciones (mayor resolución de imagen, tasa de refresco, espacios de color alternativos, etc.) manteniendo el mismo soporte físico para garantizar la retrocompatibilidad.

{.close-float}

A lo largo del ciclo de vida de la PS3, Sony añadió mediante actualizaciones de software ciertas funciones HDMI de nuevas revisiones [@graphics-hdmi_dev]. El último protocolo compatible con la PS3 es la versión 1.4, que incorporó de forma destacada el soporte para la 'televisión 3D'. Sin embargo, otras capacidades, como resoluciones de vídeo superiores, quedaron limitadas a **1920×1080 píxeles**, con la mayoría de los juegos renderizando sus frame buffers a tan solo 1280×720 píxeles.

### Visión 3D 'real'

¿Y qué era esa 'televisión 3D' que mencioné antes? Resulta que la vida útil de esta consola coincidió con una breve fiebre de televisores 3D (los llamados _3DTV_) [@graphics-3dtv]. Para soportarlos, Sony actualizó su SDK para asistir el renderizado de frames estereoscópicos en RSX e implementó la 'especificación 3D' en su codificador HDMI. Lo que ocurre entre bastidores es que el codificador emite dos frames al mismo tiempo, y el televisor los alterna de forma similar a lo que hacían las [gafas 3D del Master System](master-system#tab-2-3-3d-glasses) treinta años antes.

## Audio

Me temo que en esta sección no encontrarás mucha información, principalmente porque desde la [última invención portátil](playstation-portable#audio), el audio se ha desplazado _silenciosamente_ hacia el software. En otras palabras, ya no existen chips de audio dedicados.

La demanda de mejores gráficos tiende a crecer de forma exponencial (los consumidores quieren más escenarios, mayor detalle y colores más ricos), pero no sucede lo mismo con el sonido. Presumo que esto se debe a que las capacidades han alcanzado nuestro límite cognitivo (44,1 kHz de frecuencia de muestreo y resolución de 16 bits). Lo único que queda es implementar más canales y efectos, pero estos no requieren la potencia de procesamiento que justificaría instalar chips especializados, al menos en hardware doméstico.

![Resumen del pipeline de audio.](_diagrams/audio.png)

Así pues, el audio se implementa ahora completamente por software y es procesado por los SPU (me refiero al Synergistic Processor Unit, ¡no a la [Sound Processing Unit](playstation-2#audio)! Es un poco irónico que ambos compartan las mismas siglas...). Sony proporciona en su SDK numerosas librerías que instruyen a los SPU para llevar a cabo la secuenciación, la mezcla y el streaming de audio. Y si eso no es suficiente, también pueden aplicarse múltiples efectos.

Dicho esto, ¿a dónde se envía la señal de audio para su emisión? Al **RSX**. Este chip también contiene los puertos utilizados para emitir las señales de audio en bruto al televisor. Antes de enviarlas, la señal se codifica en distintos formatos en función de la salida seleccionada (analógica, HDMI o S/PDIF, este último también llamado 'audio digital').

## E/S y retrocompatibilidad

Todas las operaciones de Entrada/Salida (E/S) se delegan a otro chip de considerable tamaño llamado **Southbridge** [@io-southbridge]. Esto es muy similar a la arquitectura que [adoptó](xbox#motherboard-architecture) el Xbox original en su día. Parece que la brecha arquitectónica entre consolas se va estrechando, o quizás este enfoque demostró ser muy fiable e independiente de la arquitectura; te dejo que juzgues.

![El gran chip Southbridge supervisando pequeños chips e interfaces de E/S.](southbridge.png){.tabs-nested .active title="Original"}

![La misma fotografía con las partes importantes señaladas.](southbridge_marked.png){.tabs-nested-last title="Marcado"}

Al igual que el [IOP](playstation-2#io) del PS2, el Southbridge es completamente propietario, aunque esta vez fabricado por Toshiba (que lo denominó 'Super Companion Chip' [@io-supercompanion]). Así pues, aunque sigue siendo un chip bastante opaco, realiza un trabajo superior al consolidar múltiples interfaces y protocolos, tanto externos (USB, Ethernet, etc.) como internos (SATA). Como referencia, en el pasado, la lenta frecuencia de reloj del IOP acabó [creando cuellos de botella](playstation-2#available-interfaces) en interfaces rápidas como ATA y Ethernet, reduciendo considerablemente su ancho de banda máximo.

Además, el Southbridge implementa algoritmos de cifrado para proteger de forma transparente la comunicación entre protocolos estándar, como los datos del disco duro.

![Diagrama de las conexiones del Southbridge.](_diagrams/southbridge.png)

En conjunto, el Southbridge integra una enorme cantidad de interfaces, algo que tiene que ver con que esta consola se diseñó en plena tendencia del 'centro multimedia'. Ya no basta con que las consolas de videojuegos reproduzcan juegos; también deben convertirse en reproductores de DVD y Blu-ray, set-top boxes (parcialmente), visores de fotografías (importando las fotos de la cámara mediante el lector multicard) y, posiblemente, mucho más a medida que las necesidades evolucionen (gracias a su sistema operativo actualizable).

### Interfaces externas

![Como muchas torres de PC de aquella época (incluida la mía), un lector multicard era casi obligatorio. Junto a él, cuatro puertos USB 2.0.<br>¡Bastante 'premium' para una consola que costaba £425!.](cecha/front_open.jpg){.open-float .tabs-nested .tab-float .active title="Abierta"}

![La misma consola con la tapa cerrada.<br>Para reducir aún más el coste, los modelos posteriores sellaron el hueco y eliminaron el lector de tarjetas y dos puertos USB.](cecha/front_closed.jpg){.tabs-nested-last title="Cerrada"}

En cuanto a los puertos accesibles para el usuario, el Southbridge está conectado a:

- Un hub **USB 2.0**: proporciona **cuatro puertos USB A frontales**. Pueden usarse para accesorios o para conectar/cargar los mandos.
- Una interfaz **Serial ATA** (SATA): conecta la unidad Blu-ray y un disco duro de 2,5". Hasta 2008, los lectores Blu-ray usaban Parallel ATA [@io-bd_rev], por lo que se instaló un chip intermedio para realizar la conversión SATA → PATA.
- Un **controlador Ethernet 1000/100/10 (Gigabit)**: en forma de conector RJ45 en la parte trasera; también deriva hacia una placa hija inalámbrica que proporciona conexión **Wi-Fi 802.11b/g** y **Bluetooth 2.0**.
- Un **lector multicard**: proporciona ranuras para [Memory Stick](playstation-portable#tab-3-2-memory-stick-duo), SD, MultiMediaCard (MMC), Microdrive y Compact Flash.

{.close-float}

#### Equipos 'sin cables'

Gracias a la amplia adopción de la tecnología Bluetooth, los mandos con cable son ya cosa del pasado. La nueva versión del mando [Dualshock 2](playstation-2#interactive-accessories) del PS2 se llama **Sixaxis** y, aunque no es el [cambio radical](wii#next-gen-controllers) por el que optaron otros, incorpora un giroscopio para nuevos tipos de interacción. Eso sí, a costa de la respuesta háptica (_Rumble_). Un año después, Sony sorprendió a los jugadores con el **Dualshock 3**, que recuperó el motor de vibración.

Por otro lado, ahora es posible encender la consola desde el mando inalámbrico.

### Interfaces internas

En cuanto a los componentes internos, el Southbridge se conecta a:

- **Starship 2**: un adaptador para **dos chips de 128 MB de flash NAND**. Entre bastidores, Starship hace de puente entre el bus local del Southbridge y el 'Common Flash Interface Protocol' estandarizado (ampliamente adoptado para la interfaz con memoria Flash) [@io-starship]. En estos chips la PS3 almacena, entre otras cosas, el sistema operativo.
- El **chipset del PlayStation 2**: en una esquina de la placa base hay un llamativo chip que no es otro que el [Emotion Engine](playstation-2#cpu) y el [Graphics Synthesizer](playstation-2#graphics). El combo EE+GS se conecta a **32 MB de RDRAM** y a un puente de E/S (denominado 'PS2 bridge'), que en conjunto forman aproximadamente el 90 % del PlayStation 2 original.
  - El chip EE+GS envía la señal de vídeo directamente al RSX.
  - ¡Estos chips no son accesibles para los desarrolladores; se usan exclusivamente para la retrocompatibilidad!

#### Compatibilidad con versiones anteriores

Habiendo mencionado los chips del PS2, supongo que es el momento de hablar de la retrocompatibilidad de la PlayStation 3 de una vez por todas.

Para empezar, permíteme explicar cómo funciona la retrocompatibilidad en general: las consolas pueden reproducir los juegos de su predecesor mediante **software** (que instruye al hardware existente para que se comporte como esperaría el juego antiguo) o mediante **hardware** (el hardware existente proporciona compatibilidad total o parcial, o la empresa añade chips adicionales para recrear el sistema antiguo dentro de la nueva placa base). Con la potencia de procesamiento que muestra la PS3, cabría esperar que Sony incluyera un emulador de PS2 corriendo dentro de Cell y acelerado por RSX. Pues bien, por alguna razón eso no ocurrió y, en cambio, Sony instaló el chipset del PS2 en un rincón de la placa base.

![El gran chip EE+GS, dos módulos de [RDRAM](playstation-2#a-recognisable-memory-choice) de 16 MB y el 'PS2 bridge'.](eegs.jpg){.tabs-nested .active title="Original"}

![La misma fotografía con las partes importantes señaladas.](eegs_marked.jpg){.tabs-nested-last title="Marcado"}

Por otro lado, los chips ausentes pero menos críticos (IOP, SPU, etc.) se replican mediante software que corre en Cell. En cuanto a las partidas guardadas, inicialmente los usuarios tenían que adquirir un adaptador de tarjeta de memoria, pero tras una actualización de software las Memory Cards se emulan ahora como imágenes de disco almacenadas en el disco duro, mientras que Magic Gate (el sistema de cifrado) es gestionado de forma transparente por un SPU.

Como Cell y RSX siguen activos durante la reproducción de un juego de PS2, el sistema ofrece dos métodos de escalado para ampliar el área de pantalla durante el juego: 'vecino más cercano' o 'suavizado' (con antialiasing).

![Interfaz de usuario de la PS3 mostrando la entrada del juego tras insertar un disco de PS2.<br>(No te preocupes por los demás iconos por ahora, ya que algunos ni siquiera son oficiales).](xmb/ps2.jpg)

En definitiva, gracias a este diseño, la PS3 ejecuta juegos de PS2 con una tasa de compatibilidad impresionante. Además, puedes aprovechar las nuevas funciones que ofrece la nueva consola (mando inalámbrico, interfaz HDMI y tarjetas de memoria virtuales).

Por si fuera poco, los juegos de PS1 también pueden ejecutarse, esta vez sin necesidad de incluir el antiguo SoC o GPU (se basa en emulación pura mediante software).

#### El extraño final de una etapa

A lo largo del ciclo de vida de la PS3, Sony fue eliminando paulatinamente los chips exclusivos del PS2 de la placa base hasta que la compatibilidad quedó implementada únicamente por software (con mayores limitaciones, como solo poder ejecutar juegos de PS2 adquiridos en su tienda online). Como Sony nunca sustituyó el chipset del PS2 (algo que sí había hecho anteriormente con el hardware del PS1 dentro del PS2), cabe preguntarse cuál fue el razonamiento técnico y ejecutivo detrás de esta decisión. Pues bien, a modo de caso de estudio, aquí va mi opinión resumida sobre las razones:

- **Plazos**: Sony probablemente pretendía que los propietarios de PS2 compraran su nuevo producto como sustituto del anterior, algo más asequible para los consumidores (ya que pueden vender su consola vieja). Sin embargo, por alguna razón, Sony no tenía preparado un emulador de software antes del día de lanzamiento, así que inicialmente recurrió a añadir chips adicionales. Más tarde, a medida que la emulación por software avanzó satisfactoriamente, los fue eliminando en revisiones posteriores de la placa base.
  - Para complementar, el desarrollador 'M4j0r' comentó: «Un punto interesante es que Sony desarrolló las dos revisiones de emulación por hardware al mismo tiempo (EE/GS y solo GS), supongo que porque algunos juegos funcionan mejor según cuál se use.» [@operating_system-psxplace].
- **Costes**: El precio de lanzamiento de la primera revisión de la consola (_CECHA_, solo en Japón y EE. UU.) en 2006, que era compatible con PS2, fue de 599,99 $ o 60.000 ¥ sin impuestos [@io-launch_fun]. El modelo siguiente (_CECHC_, distribuido internacionalmente en 2007) eliminó el Emotion Engine y la RDRAM (transfiriendo esas tareas a la emulación por software) y se lanzó en el Reino Unido con un precio de £425. Más adelante ese mismo año, Sony publicó un nuevo modelo (_CECHG_) sin ningún chip relacionado con el PS2 por £126 menos [@io-price_cut]. Todo esto demuestra que la retrocompatibilidad es, al final, una función cara.
- **Hardware ocioso y energía desperdiciada**: aunque Cell y RSX siguen ocupándose de algunas tareas para recrear el entorno original, estas son mínimas comparadas con su pleno potencial. Sumado al hecho de que los modelos CECHA tienen un consumo acumulado de 399 vatios [@io-psu], te hace preguntarte si este diseño merece ese consumo energético, por no hablar de su eficiencia (para comparar, la nueva fuente de alimentación del CECHG consume 285 vatios).
  - Entiendo que hay otros factores que influyen en la reducción del consumo, como las nuevas revisiones de Cell y RSX. Aun así, sigo creyendo que el chipset del PS2 desempeña un papel importante.
- **Inflexibilidad**: el chip EE+GS no es reprogramable, lo que significa que el resultado siempre será el mismo, independientemente de si hay fallos o posibles mejoras. Compara esto con las mejoras gráficas del emulador PCSX2 [@io-widescreen_hack] y sus capacidades de modificación [@io-persona_mods]; esto demuestra que hay margen de mejora posible y apreciado.

Personalmente, creo que la emulación pura por software es la opción más viable a largo plazo debido a su escalabilidad, personalización e independencia del hardware propietario. Pero, por supuesto, requiere mayor esfuerzo para implementarse con precisión, como demuestra el desarrollo continuo de PCSX2 por parte de una comunidad de voluntarios (cabe señalar, eso sí, que dicho emulador solo corre en ordenadores x86).

#### Compatibilidad lateral

¡Aún no hemos terminado con la compatibilidad! Puede sorprenderte que Sony también permitiera a los usuarios ejecutar un subconjunto de juegos de [PlayStation Portable](playstation-portable#games). La emulación se llevaba a cabo completamente por software, igual que la compatibilidad con PS2 en los modelos más recientes.

Como la PS3 no dispone de ningún lector de [disco UMD](playstation-portable#tab-3-1-umd-discs), los usuarios deben acceder al catálogo de juegos de la tienda online de Sony para descargar e instalar cualquier juego de PSP.

## Sistema Operativo

Ahora que las consolas domésticas se han convertido en potentes centros multimedia, se necesita un sistema operativo más _elaborado_ para proporcionar a los usuarios más servicios y a los juegos una capa de abstracción más robusta. Todo ello manteniendo la seguridad y el rendimiento a la altura.

En consecuencia, términos como [shell](playstation-2#operating-system) o [BIOS](playstation#operating-system) ya no se usan para describir esta área, no porque hayan dejado de existir, sino porque describen solo una pequeña fracción del nuevo sistema. El término genérico ahora es 'sistema operativo', que abarca muchas áreas (boot loader, kernel, interfaz de usuario) que analizaremos por separado. Como siempre, recomiendo revisar primero el [sistema operativo del PSP](playstation-portable#operating-system), ya que su diseño modular es un ingrediente recurrente en la PS3.

### La seguridad privilegiada de Cell

Antes de entrar en detalles, debo mencionar los distintos modos de operación de Cell. En un principio tenía pensado describir esto en la sección 'CPU', pero dado lo densa que se puso esa sección, lo introduzco aquí, donde verás enseguida su uso práctico. Además, estos modos también condicionan el diseño de cualquier sistema operativo que corra en Cell, no solo el que Sony desarrolló para esta consola.

Dicho esto, para protegerse del acceso no autorizado a datos y/o recursos sensibles, Cell implementa un conjunto de **niveles de privilegio** heredados de la especificación PowerPC. En otras palabras, Cell ejecuta programas en dos modos:

- **Modo privilegiado**: Cell otorga acceso a todos los rincones de su hardware (registros, direcciones de memoria, opcodes, etc.) [@cpu-cell_arch-book, p. 183]. Por razones de seguridad, este modo solo debería ser utilizado por el núcleo del sistema operativo (es decir, el Kernel).
  - Además, Cell también estaba preparado para **ejecutar múltiples sistemas operativos de forma simultánea** y, para lograrlo a nivel de hardware, el 'modo privilegiado' puede subdividirse en **Privilege 1** y **Privilege 2**. 'Privilege 2' está destinado a ser usado por un Kernel, mientras que 'Privilege 1' lo usa un **Hypervisor**, que arbitra los recursos entre los distintos Kernels que corren al mismo tiempo.
  - La funcionalidad del 'hipervisor' también se convirtió en un área de investigación en las sedes de IBM [@operating_system-watson_hypervisor] [@operating_system-shype].
- **Modo usuario**: como su nombre indica, Cell solo otorga un conjunto limitado de recursos [@cpu-cell_arch-book, p. 32] y está orientado a las aplicaciones tradicionales que corren sobre el sistema operativo. Si por alguna razón un programa solicita acceso a una ubicación protegida, la ejecución salta al Kernel o al Hypervisor para que decida si el acceso debe concederse o no.

Además, los SPE cuentan con un modo de operación llamado **isolated mode**, que protege el proceso de ejecución dentro del SPU de modo que ninguna unidad externa (el PPE u otros SPE) pueda acceder a él hasta que el SPU termine. Este modo puede activarse tras cargar un programa en cualquier SPE y garantiza que el procesador no sea manipulado mientras se ejecuta el código sensible (por ejemplo, una rutina de cifrado).

El sistema operativo de Sony, que describiré en los siguientes párrafos, utiliza todos los modos descritos para gestionar su seguridad.

### Visión general

Como dije antes, el sistema operativo es bastante complejo. Para poder seguir esta sección sin demasiados problemas, podemos dividir los tipos de archivos que encontramos en el sistema operativo de esta consola en distintas capas:

- **Loaders**: en pocas palabras, los programas/binarios de esta consola están sistemáticamente cifrados. Así pues, los 'Loaders' son programas que ejecutan los programas 'reales'. Dicho de otro modo, los Loaders toman los binarios, los descifran, comprueban su autenticidad y finalmente los envían al procesador correspondiente (el PPE o cualquier SPE) para su ejecución. Por si esto ya parece complicado, los Loaders están encadenados entre sí para proteger aún más el software. Por último, los Loaders se encuentran en múltiples soportes.
  - Sony actualiza algunos Loaders (mediante actualizaciones de software) mientras que otros no pueden modificarse. Esto es independiente de si están instalados en almacenamiento reescribible, ya que algunos loaders están cifrados con claves específicas de cada consola y no pueden alterarse una vez que esta sale de fábrica (al menos por medios tradicionales).
- **Archivos del sistema**: comprenden binarios de bajo nivel (ejecutados mediante Loaders), metadatos para organizar el hardware, utilidades y otros recursos (fuentes, imágenes, etc.). Al igual que los Loaders, hay archivos del sistema específicos de cada consola que no pueden sustituirse ni regenerarse automáticamente.
  - Algunos binarios tomaron prestado código de los proyectos Free BSD y NetBSD [@operating_system-opensource].
- **Contenido del usuario**: incluye archivos de configuración (configuración de internet, etc.), datos utilizados por los juegos (archivos de instalación de juegos y partidas guardadas) y datos generados automáticamente por la consola (información del disco duro, etc.).
  - A diferencia de las otras capas, la destrucción de estos datos no conlleva consecuencias catastróficas.

### Jerarquía de seguridad del sistema operativo

En términos generales, el sistema operativo de la PS3 está diseñado con el mismo enfoque modular que el del PSP. Como recordatorio del artículo anterior, el sistema operativo está compuesto de múltiples **módulos**. Estos pueden servir al usuario (como un juego o una aplicación) o residir en memoria de forma indefinida para servir a otros módulos (en forma de llamadas al sistema y/o drivers). Algunos módulos tienen más privilegios de acceso que otros (módulo del kernel vs. módulo de usuario).

![Diagrama que muestra cómo encajan los componentes del sistema operativo del PlayStation en los niveles de privilegio de Cell.<br>Las referencias a 'OtherOS' se explican más adelante en las siguientes secciones.](_diagrams/os_levels.png)

A lo largo de su ciclo de vida, el sistema operativo invocará a muchos módulos con distintos privilegios. Sony construyó su sistema operativo de modo que los módulos se ejecuten bajo los tres niveles de privilegio de Cell:

- **Nivel 1**: aquí reside el **Hypervisor** programado por Sony. También denominado `lv1`, este programa es la puerta de acceso a cada bit de esta consola y está encadenado a las excepciones generadas por la MMU. Dicho esto, el hipervisor solo acepta solicitudes de programas autorizados por Sony (los que residen en el siguiente nivel de privilegio). Mientras el Hypervisor reside en memoria, también proporciona llamadas al sistema de bajo nivel y soporte para el sistema de archivos FAT16.
- **Nivel 2**: reservado naturalmente para el **Kernel**, un programa privilegiado también denominado `lv2` o 'Supervisor'. El kernel abstrae el hipervisor para que los programas del nivel 3 no interactúen con él directamente. El Kernel proporciona funciones de multi-threading tanto para el PPU como para el SPU. En última instancia, el Kernel inicializa los módulos del espacio de usuario.
- **Nivel 3**: el resto de los programas (llamados userland o userspace), incluyendo los **juegos** y el **visual shell**, se ejecutan en este nivel. Estos _plebeyos_ están a merced del Kernel para comunicarse con el hardware de la consola y no pueden iniciar ningún nuevo proceso o programa de forma unilateral.

### Soporte de almacenamiento

Con todo lo dicho, ¿dónde se almacenan todos estos datos? Desde la perspectiva del usuario general, solo hay dos soportes visibles: los discos Blu-ray para los juegos y un disco duro para las partidas. Pues bien, hay algunos más, así que los examinaremos uno a uno.

#### Cell BootROM {.tabs .active}

Resulta que dentro de Cell hay una pequeña ROM oculta en algún lugar donde los fabricantes pueden almacenar un boot-loader 'protegido'. IBM proporciona este espacio para evitar que cualquier empresa (no solo Sony) tenga que implementar manualmente [métodos de ofuscación a medida](xbox#system-protection) para proteger su código de arranque, ya que los componentes de catálogo no siempre están preparados para necesidades específicas.

Como este componente ya está físicamente protegido mediante ofuscación, no necesita estar cifrado. Por ello, es ideal para un boot-loader de primera etapa (que no puede estar cifrado), y la PlayStation 3 almacena allí su etapa de arranque inicial.

#### Memoria Flash NAND/NOR {.tab}

¿Recuerdas esos **256 MB de flash NAND** que mencioné brevemente antes? Pues bien, aquí es donde reside la mayor parte del sistema operativo. Al menos hasta que Sony lanzó el modelo _CECHH_ a finales de 2007, sustituyendo la NAND de 256 MB por una _diminuta_ **NOR de 16 MB**. Como consecuencia, algunos archivos tuvieron que moverse a otro lugar. Para simplificar, veamos primero qué almacenan estos chips [@operating_system-flash]:

- **Loaders específicos de la consola**: concretamente, dos loaders llamados `bootldr` y `metldr`. Estos archivos están cifrados con una clave grabada durante la fabricación, ¡por lo que no pueden reemplazarse!
  - Con todo, existen funciones ocultas en el hipervisor de Sony que permiten actualizarlos, aunque, por alguna razón, nunca se han utilizado [@operating_system-psxplace].
- **CoreOS**: la primera mitad del sistema operativo. Consiste principalmente en más loaders que continuarán el proceso de arranque y, en última instancia, inicializarán la segunda mitad (**GameOS**). CoreOS también proporciona el **Recovery Menu**, un shell alternativo con utilidades de mantenimiento que los usuarios pueden usar para intentar reparar su consola.
- **Identificadores únicos**: similares al [IDStorage del PSP](playstation-portable#tab-2-2-kernelipl), la consola los usa para controlar hardware asegurado como la unidad Blu-ray; o Sony los usa para autenticar la consola con sus servidores online (como la clave IDPS).
- **Recursos de seguridad**: algunos programas dependen de ellos para realizar operaciones de seguridad. Por ejemplo, las películas Blu-ray con DRM comprueban un bloque llamado Virtual Table Rights Management (VTRM). Sony también almacena **herramientas y registros de revocación** para incluir en listas negras certificados de seguridad que hayan sido comprometidos.

Debido a su mayor tamaño, los modelos con flash NAND también almacenan el resto del sistema operativo (denominado **GameOS** o `devflash`). Esto incluye:

- El **Visual Shell** (VSH): continuación de [la icónica interfaz del PSP](playstation-portable#visual-shell), también incluye una gran cantidad de módulos (plugins) y recursos.
- **Emuladores**: los programas mencionados que permiten a la PS3 ejecutar juegos de PS1, PS2 o PSP. El emulador de PS2 concreto que se carga depende de la revisión de la consola (si tiene hardware de PS2 completo, parcial o ningún hardware de PS2).
- **Librerías en tiempo de ejecución**: los programas desarrollados con el SDK de Sony están vinculados dinámicamente a un conjunto de librerías almacenadas aquí.
- **Reproductor Blu-ray**: programas que gestionan la interacción con la unidad Blu-ray y la decodificación de películas.
- **Recursos del sistema**: como fuentes y certificados de los que dependen los binarios para funcionar.

Por si fuera poco, las consolas NAND también albergan otros datos como el **xRegistry** (una colección de configuraciones de red, cuentas de PlayStation Network y una lista de dispositivos Bluetooth emparejados), más registros de revocación y un Loader para **OtherOS** (una pieza realmente interesante de la que hablaremos en los siguientes párrafos).

#### Disco duro {.tab}

El nuevo disco duro de 2,5 pulgadas, con capacidades entre 20 GB y 500 GB (a medida que se lanzaban más revisiones), proporciona almacenamiento persistente de datos para:

- **Contenido del usuario**: incluyendo partidas guardadas, trofeos (más información en la sección 'Juegos') y otros datos relacionados con el usuario.
- **Recursos de los juegos**: los juegos pueden copiar archivos del disco al disco duro para mejorar los tiempos de carga. El sistema operativo los trata como 'datos de juego'.
- **Caché**: una partición separada de 2 GB está disponible para los juegos como almacenamiento temporal (en caso de que la RAM principal no sea suficiente).

Los sistemas NOR, sin embargo, también almacenan el GameOS en el disco duro. Por ello, cada vez que el usuario cambia el disco duro, la consola solicita un archivo de actualización para reinstalar el GameOS en el disco. Sea como sea, ni los sistemas NOR ni los NAND arrancan sin el disco duro.

Ciertos datos del usuario pueden copiarse en una memoria USB y luego trasladarse a otra consola en caso de necesidad, aunque este proceso reformatea la consola de destino antes de copiar los datos.

#### eMMC {.tab}

En 2012, Sony presentó una revisión rediseñada de la consola llamada 'SuperSlim' (cuyo nombre en clave es CECH-4xxx). Estaba disponible en tres variantes: una con disco duro de 250 GB, otra con 500 GB, o una tercera opción con solo **12 GB de flash eMMC interna**. Las dos primeras siguen la distribución del sistema de archivos implementada en los modelos NOR, mientras que la tercera almacena todo en la eMMC (incluyendo los datos del usuario) y sigue la distribución NAND para los archivos del sistema.

No obstante, el modelo eMMC tiene un detalle. En lugar del chip NOR, Sony instaló un chip Panasonic 'MN66840' que, según la PS3 Dev Wiki [@operating_system-MN66840], parece redirigir el bus NOR hacia la eMMC. Supongo que es simplemente un truco para ahorrar costes, ya que reutiliza el mismo Southbridge de las otras variantes.

Curiosamente, si el usuario decide instalar un disco duro en el modelo eMMC, la consola transfiere todos los datos del usuario de la eMMC al nuevo disco duro. De este modo, el usuario puede aprovechar por completo el disco duro, aunque el espacio libre de la eMMC queda desaprovechado.

### Proceso de arranque {.tabs-close}

Bien, con todo el conocimiento previo, ahora aprenderás cómo arranca el sistema, y déjame decirte que es bastante complicado. La razón es sencilla: Sony no quiere que metas mano a su hardware o software, así que construyeron múltiples capas de ofuscación y cifrado para impedir que te infiltres y cargues tu propio código (y con suerte _te des por vencido y sigas comprando juegos/películas/lo que sea_), pero, como la historia demuestra, ocurrió lo contrario.

En la siguiente sección describiré lo que hace esta consola al pulsar el botón de encendido. Ten en cuenta que este proceso solo cambió drásticamente una vez (después de que los hackers lo descifraran). Para simplificar, empezaremos por el proceso de arranque 'original' (implementado antes de la versión `3.60` del sistema) [@operating_system-levin] [@operating_system-boot] [@operating_system-the_exploit]:

1. Un chip independiente de la placa base (llamado **Syscon**) se enciende y ejecuta instrucciones desde su ROM interna. A continuación, envía un 'Configuration Ring' a Cell mediante SPI (una conexión serie), lo que inicializa Cell y desactiva el octavo SPU. Por último, activa la línea de alimentación y da vida a Cell.
2. El vector de reset del PPU de Cell apunta a su ROM oculta, que almacena las rutinas para localizar y descifrar `bootldr` de la Flash. El fragmento descifrado es cargado por el primer SPU en isolation mode.
3. El SPU, ya aislado, habiendo cargado `bootldr`, inicializa parte del hardware (memoria XDR e interfaces de E/S), descifra un binario llamado `lv0` e instruye al PPU para ejecutarlo.
4. El PPU, que ya ejecuta `lv0`, descifra `metldr` (un loader específico de la consola) y lo envía al tercer SPU, de nuevo en isolation mode.
5. El SPU2, que ahora ejecuta `metldr`, ejecuta cinco loaders más secuencialmente:
    1. `lv1ldr` descifra y carga `lv1`, que contiene el **Hypervisor** que toma el control del primer nivel de privilegio. Además, `lv1` configura el disco duro, la unidad Blu-ray y el RSX.
    2. `lv2ldr` descifra y carga `lv2`, que contiene el kernel y corre sobre el hipervisor. También termina de inicializar el RSX, la emulación de PS2, Bluetooth, el controlador USB y el lector multicard.
    3. `appldr` descifra y carga `vsh` (el **Visual Shell**) y otras dependencias. `vsh` permitirá posteriormente al usuario cargar un juego.
    4. `isoldr` descifra y carga módulos que se ejecutarán en el tercer SPU en isolation mode. Estos módulos son críticos para la seguridad y realizan múltiples funciones criptográficas a lo largo del ciclo de vida de la consola. En consecuencia, **el tercer SPU queda reservado para funciones de seguridad y los juegos no pueden usarlo** (dejando solo seis SPE disponibles para los juegos).

El PPU, tras cargar `vsh`, otorga al usuario el control mediante una interfaz gráfica que se manifiesta con un icónico _sonido orquestal de arranque_ seguido del menú _XMB_.

#### Proceso de arranque revisado

En marzo de 2011, un hacker conocido como 'GeoHot' quebró la seguridad de `metldr`, comprometiendo así la autenticidad de los loaders posteriores. Sony respondió emitiendo actualizaciones de seguridad tanto para el hardware como para el software. Estas correcciones se analizan más adelante en la sección 'Antipiratería' de este artículo.

### Visual Shell

¿Te estás cansando de tanta teoría? Pasemos a algo que todo el mundo puede ver: el **Visual Shell**.

**XrossMediaBar** (XMB), la nueva interfaz de usuario que ganó reconocimiento internacional [dos años antes](playstation-portable#visual-shell), se ha adaptado ligeramente para poder interactuar con ella desde el sofá (la llamada 'interfaz de usuario a 10 pies') y ampliado para aprovechar la resolución 'Full HD' (1920×1080 píxeles).

![XMB en el PSP (2004).<br>Renderizado a 480×272 píxeles.](images/consoles/psp/xmb/main.jpg){.toleft}

![XMB en la PS3 (2006).<br>Renderizado a 1920×1080 píxeles.](xmb/main.jpg){.toright}

Aunque los usuarios de PSP encontrarán muchas similitudes, Sony añadió un nuevo conjunto de aplicaciones que aprovechan el potencial de Cell, RSX y la unidad Blu-ray. Muchas de ellas relacionadas con multimedia (reproductor de vídeo, presentación de imágenes, etc.), televisión (aplicaciones de TV a la carta, como iPlayer de la BBC), perfil social (avatares online) y compras online (PlayStation Now y PlayStation Store, por nombrar algunas).

Además, dado que se trata de una consola doméstica que pueden compartir varios miembros, XMB soporta **múltiples usuarios**, donde cada uno puede usar una cuenta de PlayStation Network diferente y almacenar datos de usuario separados (juegos adquiridos y partidas guardadas).

::: {.subfigures .tabs-nested}

![Al igual que en el PSP, al seleccionar un juego el fondo puede cambiar de estilo para llamar tu atención.](xmb/game.jpg){.active title="Juego"}

![El XMB ofrece una inmensa cantidad de configuraciones, especialmente útil al configurar tu flamante televisor 1080p con audio envolvente 3.1.](xmb/settings.jpg){title="Ajustes"}

![Diversas opciones multimedia.](xmb/multimedia.jpg){title="Multimedia"}

![El XMB permite instalar juegos, actualizaciones y expansiones (DLC) mediante un instalador de paquetes nativo.](xmb/installpkg.jpg){title="Instalación"}

La interfaz XrossMediaBar (XMB) de la PlayStation 3

:::

Por último, la inclusión de un disco duro es un alivio para los veteranos que en el pasado se veían obligados a comprar costoso almacenamiento propietario ([Memory Stick Pro Duo](playstation-portable#tab-3-2-memory-stick-duo)) cada vez que se quedaban sin espacio.

#### Préstame tu PS3

Sorprendentemente, no todas las aplicaciones incluidas en esta consola tenían objetivos _interesados_. Con la irrupción de la computación distribuida y las capacidades de Cell para proyectos de ciencia de datos, la Universidad de Stanford se alió con Sony para permitir a los propietarios de PlayStation 3 contribuir a la investigación médica. El resultado fue **Folding&#64;home** (se pronuncia 'folding at home').

Folding&#64;home era una aplicación instalada en todos los PlayStation 3 que, una vez abierta por el usuario, se conectaba a un servidor central y ejecutaba simulaciones de proteínas. Además, la aplicación también podía correr en segundo plano durante las horas de menor actividad.

![Folding&#64;home mostrando el trabajo realizado desde que el usuario inició la aplicación [@operating_system-folding_app].](xmb/folding.jpg)

A lo largo de su vida útil, la potencia de computación conjunta de 15 millones de usuarios de PS3 de todo el mundo contribuyó a la investigación de Folding&#64;home en la búsqueda de una cura para el Alzheimer [@operating_system-protein_retirement]. Finalmente, Folding&#64;home y Sony retiraron la aplicación en 2012, y la primera continúa viva en otras plataformas.

Se trata de mi opinión personal, pero disfruto leyendo sobre proyectos que aportan algo al mundo aprovechando las capacidades de la computación distribuida, en contraposición a los interminables artículos sensacionalistas que se quejan de la minería de criptomonedas. Supongo que no debemos olvidar que, con cada nueva tecnología poderosa, siempre habrá aplicaciones desinteresadas desarrolladas para ella.

### Una propuesta multi-SO

Cuando IBM describió Cell desde el nivel de software, mencionó que Cell es capaz de ejecutar múltiples sistemas operativos al mismo tiempo, gracias a sus numerosos núcleos de ejecución [@cpu-mit]. Así pues, Sony tomó esta idea y añadió al XMB una opción para instalar un **sistema operativo secundario** [@operating_system-open_platform]. Esta función se llamó **OtherOS** y, en pocas palabras, proporciona un gestor de particiones (el XMB simplemente guía al usuario para redimensionar la partición del GameOS y asignar nuevo espacio para el segundo sistema operativo) y un botón para arrancar desde el segundo sistema operativo (gracias a los archivos de arranque de OtherOS ya configurados en la Flash). Así que el usuario solo necesita llenar la nueva partición con un sistema operativo. Como consecuencia, muchas distribuciones de Linux (Ubuntu, Fedora, etc.) añadieron la PS3 como otra plataforma de instalación posible. Puedes considerarlo el sucesor espiritual de [Linux para PS2](playstation-2#an-unusual-kind-of-game).

![Red Ribbon GNU/Linux es una distribución exclusiva para la PS3/Cell, compilada con el objetivo PPC64 [@operating_system-ribbon].](xmb/red_ribbon.png){.open-float}

Gracias a OtherOS, los usuarios con experiencia tenían la oportunidad de desarrollar aplicaciones homebrew que corrían en Cell sin restricciones de licencia, algo especialmente interesante para fines de investigación y científicos [@games-cluster] [@operating_system-barcelona], ya que esta consola tenía un precio mucho más asequible que un mainframe. Para usos multimedia, la unidad Blu-ray y el lector multicard también eran accesibles desde OtherOS.

Por otro lado, aunque los privilegios de OtherOS pueden superar a los del GameOS (a nivel de kernel), **no superan al hipervisor**, que sigue residiendo en memoria. Así, cualquier acceso al hardware desde OtherOS sigue dependiendo de la voluntad del hipervisor de Sony, y resulta que este **bloquea el acceso a los buffers de comandos del RSX** (impidiendo el uso de las unidades de shader, entre otros componentes utilizados para acelerar las operaciones gráficas). En consecuencia, las distribuciones Linux resultantes recurren al renderizado por software (todos los gráficos se dibujan mediante Cell) y luego transmiten el frame buffer al RSX para su visualización. Aunque es decepcionante que OtherOS no pueda aprovechar todo el potencial de esta consola, probablemente fue una decisión para reducir las superficies de ataque. Irónicamente, el uso que OtherOS hace de Cell es similar a cómo IBM/Toshiba/Sony pudieron haber concebido originalmente la PS3.

{.close-float}

Compartiendo el destino de Folding&#64;home, OtherOS fue finalmente eliminado en una actualización posterior, aunque por causas distintas (principalmente relacionadas con la seguridad). Poco después, OtherOS fue _restablecido de forma no oficial_ gracias a exploits de software y esfuerzos de ingeniería inversa. En la actualidad, OtherOS está disponible si el usuario instala un _custom firmware_. Lo explico con más detalle en la sección 'Antipiratería y homebrew'.

En el momento de escribir estas líneas, el desarrollador René Rebe está implementando controladores xf86 adecuados que aprovechan la aceleración proporcionada por el RSX y sus 256 MB de memoria [@operating_system-rsxx11]. Su trabajo se combina con otros desarrollos que eliminaron las restricciones impuestas por el hipervisor (inicialmente gracias al descubrimiento de exploits de software y posteriormente con el uso de un _custom firmware_, esto último se explica más adelante en la sección 'Antipiratería y homebrew'). El señor Rebe publica su progreso en su canal de Youtube y depende de donaciones voluntarias para continuar su trabajo [@operating_system-rsxfund].

### Capacidad de actualización

Para la parte final de esta larga sección, hablemos de las capacidades de actualización del GameOS.

En pocas palabras, al igual que el [PSP](playstation-portable#updatability), Sony distribuyó archivos `PS3UPDAT.PUP` que empaquetan todos los nuevos binarios del sistema operativo. Debido al sistema de seguridad de la consola, solo pueden actualizarse los archivos que no están asegurados con claves únicas de la consola y que están almacenados en soportes reescribibles (Flash, disco duro, eMMC); el resto debe permanecer como está.

Los archivos PUP se distribuían a través del sitio web oficial de Sony, el asistente de actualización del XMB, o se encontraban en el contenido de un disco de juego (todos los juegos incluyen un archivo PUP, que refleja la versión del SDK para la que se desarrolló). Como los modelos con flash NAND solo tienen 256 MB de espacio y almacenan todo el sistema operativo allí, Sony nunca publicó archivos de actualización de más de 256 MB.

## Juegos

Esta sección abarca temas relacionados con el desarrollo de juegos, la distribución y los servicios.

### Ecosistema de desarrollo

Como esta consola aglutina tecnología de varias empresas, incluyendo productos ya comercializados en otros mercados (la gama de GPU GeForce7 de Nvidia para PC, por ejemplo), los desarrolladores se vieron inundados por múltiples herramientas distintas para desarrollar su software. Ten en cuenta que esto no implica que el desarrollo fuera sencillo, pero sí es algo que se aprecia comparado con los [tiempos del ensamblador](master-system#games).

Para programar Cell, IBM y Sony distribuyeron paquetes de desarrollo independientes: los de IBM se apoyaban a entornos sin restricciones como Linux (y OtherOS), mientras que las herramientas de Sony usaban el GameOS de la PS3 como único entorno de ejecución.

Dicho esto, IBM distribuyó gratuitamente el paquete **IBM Cell SDK** [@games-cell_programming]. Este incluye el **GCC toolchain** modificado para generar binarios PPU y SPU, lo que permite desarrollar en C, C++, Fortran y ensamblador. También es multiplataforma, pudiendo compilar código desde otros equipos (como un PC x86). El SDK también incluía librerías de bajo nivel para facilitar las operaciones matemáticas SIMD y la gestión SPU-PPU. Por último, incorporaba una bifurcación del IDE **Eclipse**.

Para reducir la complejidad del desarrollo en Cell, IBM también desarrolló otro compilador de vida corta llamado **XLCL** que compila código **OpenCL** (una variante de C/C++ para computaciones paralelizadas) para el PPU y el SPU. Sin embargo, este solo se distribuyó a través del canal Alphaworks de IBM, por lo que se mantuvo en fase experimental.

¿Y Sony? Pues bien, de manera similar a su [SDK del PSP](playstation-portable#development-ecosystem), distribuyó devkits de hardware (en muchas variantes con distintos tamaños y mejoras) y un paquete de software compuesto de compiladores, librerías y depuradores que usaba Visual Studio 2008 (y más tarde 2010) como IDE [@games-napier]. Como solo soportaban la PS3, su SDK incluía los mismos GCC toolchains, pero complementados con una gran cantidad de librerías para ayudar en tareas gráficas, de audio y de E/S. En el caso de los gráficos/RSX, Sony proporcionaba **GCM** para construir comandos en bruto y **psGL**, construido sobre GCM, para ofrecer una API OpenGL ES. Para escribir shaders, Nvidia proporcionaba **Cg**, un compilador de shaders que analiza un lenguaje similar al HLSL (el lenguaje de shaders definido por Microsoft).

#### Desarrollo sin licencia

Con la irrupción del Homebrew nativo (que corre en el GameOS, no en OtherOS), se crearon nuevos SDK de código abierto para eludir la dependencia de las librerías protegidas por derechos de autor de Sony y, así, evitar litigios. Un ejemplo es **PSL1GHT**, un SDK usado en conjunto con **ps3toolchain** [@games-ps3toolchain], que proporciona un conjunto de desarrollo completo listo para desarrollar Homebrew legal (aunque requiere una consola modificada/hackeada con las comprobaciones de firma desactivadas).

En 2018 monté mi propio conjunto basado en ps3toolchain, pero distribuido en forma de contenedor Docker [@games-docker], para que los desarrolladores no tuvieran que compilar ps3toolchain y, en cambio, descargar mi configuración precompilada (ahorrando muchas horas de compilación). El contenedor también incluía muchas herramientas como el shader Cg de Nvidia para mitigar los problemas de dependencias que encontré al experimentar con proyectos basados en PSL1GHT. Al final fue un experimento entretenido que me ayudó a conocer mejor el entorno de desarrollo.

#### Externalización del desarrollo

Vale la pena señalar que un curioso modelo de negocio entre empresas ganó popularidad por aquel entonces: los **motores de juego**. En lugar de invertir tiempo y dinero desarrollando un juego desde cero, ¿por qué no comprar la base de código de otras empresas y construir el juego sobre ella? Esto es lo que estudios como Epic Games concibieron [@games-unreal]. Además de vender títulos populares como _Unreal Tournament 3_, el estudio licenciaba una versión reducida (sin los recursos) a otros desarrolladores. Este paquete se llamó **'Unreal Engine 3'**. En pocas palabras, los motores de juego se encargan de todas las áreas fundamentales (física, iluminación, etc.) para que los desarrolladores solo tengan que añadir su contenido personalizado (scripts, texturas, modelos, sonidos, etc.).

El licenciamiento de motores de juego no es un modelo de negocio nuevo, pero debido al desafiante entorno de la PS3, acabaron convirtiéndose en otra opción atractiva para el desarrollo.

### Soporte de almacenamiento

Ahora que hemos terminado de hablar del desarrollo de juegos, es hora de la distribución. Aquí describo los mecanismos de distribución oficiales disponibles para los juegos de PS3.

#### Discos Blu-ray {.tabs .active}

![Ejemplo de juego en caja.](games/retail.jpg){.tab-float}

Nueva generación = nuevo soporte. A medida que las ventajas del DVD empiezan a menguar y sus limitaciones, expresadas por la industria del videojuego (límite de espacio) y la del cine (formato 480i) [@games-dvd], se vuelven evidentes, es cuestión de tiempo que Sony presente [otro estándar](playstation-portable#tab-3-1-umd-discs) para sustituir sus nuevos aparatos. Para esta nueva consola, se eligió el **disco Blu-ray**.

El Blu-ray, como su nombre indica, es un nuevo formato de disco óptico que ofrece una mayor densidad de almacenamiento gracias al uso de **diodos de luz azul** [@games-brit], a diferencia de los diodos rojos de los DVD. Como la luz azul tiene una longitud de onda más corta que la roja, puede comprimirse más información ([pits y lands](playstation#anti-piracy-region-lock)) en el mismo espacio [@games-blue_laser]. El resultado son discos Blu-ray con una capacidad sorprendentemente elevada (¡entre 25 GB y 50 GB!) en el mismo disco de plástico y con las mismas dimensiones que el CD/DVD.

El formato de datos Blu-ray responde a múltiples necesidades de distintas industrias: cine de alta definición, gestión de derechos digitales (DRM), bloqueo regional, un nuevo sistema de archivos e incluso un entorno de ejecución para programas Java [@games-bdj]. En el caso de la industria del videojuego, los juegos al por menor para PlayStation 3 se distribuyeron en discos Blu-ray de **25 GB o 50 GB** con **protección anticopia**. Son leídos por una unidad 2x que alcanza velocidades de hasta 8,58 MB/s [@io-bd_rev], aunque el láser de la PS3 también puede leer DVD (a velocidad 8x) y CD (a velocidad 24x) para reproducir juegos y películas antiguos.

Mientras que los títulos de lanzamiento se ejecutan directamente desde el disco, los juegos posteriores copiaron parte de sus recursos al disco duro para aumentar las velocidades de carga. No obstante, el disco del juego siempre es necesario para iniciar la partida.

#### Tienda online {.tab}

![La aplicación PS Store en el XMB.](xmb/store.jpg){.tabs-nested .active .tab-float title="Aplicación"}

![Catálogo de juegos al abrir la tienda.](store/main.jpg){.tab-nested title="Inicio"}

![La interfaz de búsqueda (que, por cierto, no usa el teclado nativo del XMB).](store/search.jpg){.tab-nested title="Búsqueda"}

![Ejemplo de entrada de juego (acompañada de sugerencias que solo funcionan en otras consolas...).](store/game.jpg){.tabs-nested-last title="Juego"}

Al mismo tiempo que el lanzamiento de la consola, Sony puso en marcha su canal de distribución propio llamado **PlayStation Store**, que permite a los estudios de videojuegos vender sus juegos de forma digital y a los usuarios adquirir nuevo contenido sin salir del sofá. Estos no requieren ningún soporte físico para funcionar (aparte de espacio en el disco duro), pero la propiedad del producto digital está vinculada a una cuenta online, algo que generó preocupación entre los usuarios en marzo de 2021, cuando Sony anunció [@games-closure] (y posteriormente reculó [@games-reversal]) el cierre de esta tienda.

En su tienda digital, Sony también aprovechó la oportunidad para vender versiones digitalizadas de juegos de PS1, PS2 y PSP llamadas **PlayStation Classics**. También se descargan e instalan de la misma manera, pero utilizan los emuladores incluidos para funcionar. De hecho, los PlayStation Classics de PS2 invocan el mismo emulador de software sin aceleración independientemente de si los modelos de PS3 contienen el chipset del PS2 [@io-ps2emu]. Supongo que se trata del capítulo final de la emulación por hardware en la PS3.

Entre bastidores, la PS Store no es más que un sitio web al que solo se puede acceder a través de la aplicación PS Store del XMB. A lo largo de su ciclo de vida, su interfaz de usuario ha sido renovada varias veces para reflejar lo que supongo era _una demanda global de interfaces más llamativas_.

### Servicio de red {.tabs-close}

Además de la tienda online, se añadieron a la plataforma muchas más soluciones online, incluyendo el debut de **PlayStation Network**, un servicio online gratuito que competía directamente con el [Xbox Live](xbox#network-service) de pago de Microsoft.

PlayStation Network permitía a los usuarios crear una cuenta personal y asignarle un avatar, para luego usar esa nueva identidad digital para el juego multijugador, enviar mensajes y otras interacciones sociales. Los usuarios también podían conseguir **trofeos** al completar ciertos eventos en un juego, que luego aparecen en el perfil online (como una especie de medalla de honor) para intimidar a los rivales y ganarse el respeto de los amigos, supongo.

![Los juegos ofrecen un catálogo de logros para desafiar a sus usuarios. La intención es proporcionar a los jugadores una sensación de orgullo y satisfacción.](xmb/trophies.jpg){.tabs-nested .active title="Trofeos"}

![Lista de amigos. (Los nombres están tachados por razones obvias).](xmb/friends.jpg){.tab-nested title="Amigos"}

![Tras jugar un poco en línea, gente desconocida empezó a enviarme mensajes.](xmb/mail.jpg){.tabs-nested-last title="Mensajes"}

Por último, al igual que el sistema operativo es actualizable, los juegos también se actualizan. Así, al iniciar un juego, el XMB puede sugerir descargar actualizaciones del juego (en forma de 'paquetes') que corrigen fallos y/o añaden contenido nuevo. Las actualizaciones se instalan en el disco duro y funcionan de manera similar a un _sistema de archivos por capas_.

## Antipiratería y Homebrew

Todo lo que acabas de leer tiene que estar protegido de alguna manera contra el acceso 'no autorizado'. Si quieres saber cómo lo llevó a cabo Sony, tienes mucho que leer.

### Visión general de la base de seguridad

Muchas partes de la consola ya proporcionan funciones de seguridad que no requieren ninguna implementación manual en software:

- **SysCon**, el chip propietario poco conocido (mencionado brevemente en el proceso de arranque), controla las líneas de alimentación de Cell, RSX y Southbridge. Su EEPROM contiene registros leídos por los módulos del sistema operativo para determinar qué funciones están habilitadas y cuáles no [@anti_piracy-qaflag].
  - Aunque uso la palabra 'poco conocido', SysCon es simplemente un microcontrolador, ya sea un [ARM7TDMI](game-boy-advance#cpu)-S de catálogo (sí, la PS3 comparte algo de su ADN con la [Game Boy Advance](game-boy-advance) e incluso con las últimas revisiones del PS2) mejorado con soporte [MagicGate](playstation-2#interactive-accessories), o una variante NEC 78K0R personalizada [@anti_piracy-syscon]. Lo que más intriga es el firmware interno de SysCon.
  - SysCon y Cell se comunican mediante una interfaz serie (SPI) que se conecta al componente **TEST** de Cell [@anti_piracy-cell_test]. TEST proporciona muchas funciones de depuración en Cell, aunque SysCon solo se conecta al puerto de 'Pervasive logic', lo que le permite gestionar áreas como la alimentación o la temperatura [@anti_piracy-pervasive].
- Cell alberga una **ROM oculta** que almacena rutinas de arranque sin cifrar sin preocuparse de miradas indiscretas.
- Los **modos de privilegio** de Cell y el **isolated mode** del SPE impiden que los programas accedan a recursos no autorizados.
- El Southbridge cifra transparentemente el contenido del disco duro mediante AES.
- El subsistema Blu-ray es otra fortaleza cerrada, y el contenido de sus discos está cifrado mediante una clave encontrada en el área 'ROM mark' del disco (inaccesible para los lectores convencionales) [@anti_piracy-bdauth].

Además de esto, Sony implementó las siguientes protecciones por software:

- Una compleja **cadena de confianza** que comienza con la ROM de arranque sin cifrar de Cell y termina con una interfaz gráfica de usuario (XMB) que solo carga binarios cifrados (por Sony) bajo un kernel y un hipervisor.
  - La cadena de confianza implementa múltiples **algoritmos de cifrado**, incluyendo asimétricos como RSA y ECDSA y sistemas simétricos como AES; combinados con HMAC y SHA-1 (para confirmar la integridad de los datos).
- Algunas claves de cifrado **se generan durante la fabricación**, lo que significa que si los hackers las encuentran y filtran, no funcionarán en otras consolas. Aunque esto tiene el coste de que Sony no puede parchear el software cifrado con esas claves una vez que la consola sale de fábrica.
  - Estas claves especiales se usan para `bootldr` y `metldr` (las primeras etapas de arranque).
- Los juegos deben llamar al kernel para acceder al hardware, que a su vez pide permiso al **Hypervisor**. Esta 'cebolla de abstracción' evita que los [exploits de los juegos](playstation-portable#tab-6-1-early-blunders) escalen privilegios, _en teoría_.

### La derrota

Ya has visto de lo que es capaz esta consola, ¿esperabas que los hackers se conformaran con las funciones limitadas de OtherOS? Supongo que Sony tampoco. La empresa se esforzó mucho por proteger algunas áreas, mientras dejaba otras a medio cerrar, como los hackers demostrarían más tarde.

Veamos cómo algunos de sus bastiones fueron descifrados por hackers independientes de todo el mundo; ten en cuenta que la comunidad de hacking de la PS3 era muy activa, con muchas herramientas y documentación producidas cada año. Así que me centraré en algunos hitos que allanaron el camino para una avalancha de contenido y desarrollo homebrew, pero puedes encontrar más información en PS3History [@anti_piracy-ps3history].

#### Elusión del Hypervisor {.tabs .active}

En 2010, tras tres años tranquilos en la escena del hacking, la comunidad dio un giro positivo. George Hotz, un hacker conocido por haber desbloqueado previamente el primer modelo de iPhone (alias '2G') para que pudiera funcionar con cualquier operadora (originalmente solo en Cingular/AT&T), logró leer y escribir en áreas protegidas de la memoria sin ser detenido por el Hypervisor. Luego publicó su exploit junto con un breve resumen en su blog [@anti_piracy-lv1xploit].

El exploit requiere dos materiales: una instalación de Linux corriendo bajo OtherOS (para ejecución de código arbitraria, aunque limitada) y un glitcher externo conectado al bus XDR (que interfiere con la RAM principal). En pocas palabras, el hipervisor utiliza una tabla hash almacenada en la RAM principal para catalogar las direcciones de memoria junto con sus niveles de privilegio, de modo que los programas de usuario no puedan acceder a espacios de memoria protegidos. El ataque funciona rompiendo la integridad de esa tabla para poder escribir sobre ella y, a continuación, usar ese privilegio para modificar las entradas y otorgar al programa actual acceso a todos los rincones de la memoria.

En resumen, Hotz descubrió que bajo Linux/OtherOS, los programas pueden solicitar al Hypervisor múltiples bloques de memoria que apuntan a la misma dirección física, pero si el programa los libera mientras hay una interferencia externa en el bus XDR (debida a un glitcher que envía pulsos eléctricos), el proceso de liberación queda incompleto [@anti_piracy-lv1xploit_summary]. Como consecuencia, la tabla hash del hipervisor (que reside en la RAM) sigue conteniendo una entrada de las direcciones asignadas, pero al mismo tiempo cree que ese espacio ha sido liberado. El exploit de Hotz procede entonces a solicitar más bloques para que el Hypervisor amplíe su tabla con más entradas, y el proceso continúa hasta que una entrada de la tabla hash se superpone a la ubicación de memoria del bloque que debía liberarse. Como la tabla hash mantenía la entrada antigua que otorgaba al usuario acceso a esa dirección, el hipervisor acaba dando al usuario acceso para modificar esa entrada de la tabla hash. ¡Así, el exploit modifica la entrada para extender el acceso a todo el espacio de memoria!

Aunque este exploit requería un entorno de Linux corriendo bajo OtherOS, fue un enorme paso adelante para posteriores proyectos de ingeniería inversa e investigación, ya que los hackers podían ahora investigar áreas críticas del sistema que antes eran inaccesibles. Cabe mencionar que, al mismo tiempo, Sony lanzó la actualización de software `3.21` que eliminaba OtherOS. Podría pensarse que esto disuadiría a los hackers de continuar su trabajo, pero simplemente les dio más razones para acelerarlo.

#### PSJailbreak {.tab}

A finales de 2010, un grupo identificado como 'PS Jailbreak' anunció (y luego publicó) una solución única para ejecutar Homebrew directamente desde el shell nativo de la consola (XMB, bajo GameOS) sin necesidad de manipular el hardware de la consola. Todo ello para disgusto de Sony, que pronto tomaría la vía legal para bloquear las ventas de ese producto.

'PS Jailbreak' consistía en un dongle USB que se conectaba al puerto USB frontal antes de encender la consola. El usuario tenía que pulsar el botón de encendido y, poco después, el de expulsión. Si las instrucciones se seguían correctamente, el usuario veía la interfaz XMB normal pero con la incorporación de una opción **'Install PKG'** y un par de aplicaciones homebrew diseñadas para volcar los juegos Blu-ray al disco duro y luego cargarlos.

Entre bastidores, este dongle realizaba una enorme cantidad de trabajo, que puede dividirse en dos grupos [@anti_piracy-psjail]:

1. El **exploit USB**: una vez encendida la consola, el dongle engaña al sistema haciéndole creer que está conectado a un hub USB de seis puertos, y luego realiza una compleja secuencia de comandos USB hasta provocar un **heap overflow** y escalar el acceso al Kernel de la PS3 (nivel 2), procediendo a continuación a ejecutar un payload.
2. El **Payload**: otro paquete complejo que parchea el shell original para habilitar funciones ocultas solo disponibles en unidades de depuración (como la entrada 'Install PKG'), deshabilitar la verificación de firmas (para cargar cualquier módulo/paquete arbitrario) y redirigir los comandos Blu-ray al disco duro (para cargar juegos desde el disco duro). El hecho de que este programa pueda alterar tanto desde el nivel del kernel te hace preguntarte para qué sirve el hipervisor.
    - Como complemento, M4j0r me comentó posteriormente: «Curiosamente, ni siquiera explota el código de Sony; esta parte de lv2 fue escrita por Logitech y los desarrolladores de ese exploit pudieron tener acceso al código fuente (debido al hackeo de 2008).» [@operating_system-psxplace].

Este producto fue posteriormente sometido a ingeniería inversa por otras comunidades y, poco después, aparecieron clones de código abierto (como PS Groove) que eliminaron muchas restricciones (por ejemplo, los usuarios ya podían desconectar el dispositivo tras terminar el exploit). Algunos forks incluso se desplegaron en una calculadora Texas Instruments [@anti_piracy-tijb]. En cualquier caso, Sony actuó rápidamente con la actualización de software `3.42` para eliminar esta mina de oro [@anti_piracy-ps3history], aunque la puerta al Homebrew ya estaba abierta.

#### Menciones honoríficas {.tab}

Antes de hablar del gran premio de la escena homebrew de la PS3, permíteme describir un par de métodos que se desarrollaron por aquella misma época:

- El **USB Jig**: otro pendrive USB, esta vez programado para hacer que la consola entrara en el **Factory Service Mode**, destinado únicamente al mantenimiento de la consola por personal autorizado. El programa incluido en el Jig replica lo que Sony proporciona a sus técnicos. La principal ventaja del modo de servicio es poder [hacer downgrade](playstation-portable#tab-6-2-downgrading) de la consola a una versión compatible con PSJailbreak. El payload también estaba disponible como aplicación Homebrew para PSP [@anti_piracy-pspjig]. Sony respondió parcheando el modo de servicio para dificultar su restauración al modo 'normal' o la modificación del firmware desde él, desanimando a los usuarios a recurrir al modo de servicio.
- El **emulador de disco óptico** (ODE): una serie de productos de hardware distribuidos por distintas empresas (Cobra, E3, etc.). En lugar de manipular el firmware de la consola, estos manipulaban la interfaz SATA/PATA del Blu-ray. Los ODE son placas que se sitúan entre la placa base y la unidad Blu-ray, actuando como intermediarios que engañan a la consola haciéndole creer que contiene un disco de juego válido, pero en realidad cargando una imagen de disco desde una unidad USB externa. La historia del hacking de la PS3 contiene largos períodos 'unhackables' en los que no había ningún exploit de software disponible para las nuevas consolas. Así que, a un precio considerable, los ODE vinieron a llenar ese vacío.
- El **Downgrader**: a medida que Sony seguía mitigando los exploits con más actualizaciones de software, los usuarios se quedaban sin opciones viables salvo hacer downgrade a un firmware vulnerable. Así, hubo empresas como E3 que distribuyeron equipos especializados que podían sobrescribir el sistema de la consola 'a la fuerza', es decir, flasheando directamente los chips NAND o NOR. Por razones obvias, este método requería más habilidad y paciencia comparado con los basados en USB.
- **Fugas del modo aislado**: se trata de algo para fines de investigación, a diferencia de una 'función' que verá el usuario (aunque igualmente imprescindible para desarrollos posteriores). El caso es que los datos de revocación (utilizados para incluir certificados comprometidos en listas negras) son analizados por `lv2ldr`, ¿bien hasta aquí? Pues se descubrió que este proceso contenía muchas vulnerabilidades. En primer lugar y por alguna razón inexplicable, **los datos de revocación son escribibles en el espacio de usuario**. En segundo lugar, **el analizador no realiza comprobaciones de límites** sobre los datos leídos ([_aquí vamos de nuevo_](playstation-2#tab-5-1-independence-overflow)). Por ello, los hackers consiguieron elaborar datos de revocación personalizados que podían producir un buffer overflow y, en última instancia, permitirles ejecutar **código arbitrario dentro del isolated mode del SPU**. Esto les dio acceso a datos confidenciales (como claves) que supuestamente estaban protegidos del resto del sistema [@operating_system-psxplace].

#### La caída del cifrado {.tab}

Al igual que la saga del PSP, los exploits iniciales requerían mucho esfuerzo y Sony podía parchearlos fácilmente, lo que resultaba en un desventajoso juego del gato y el ratón. Sin embargo, como ocurrió con el PSP, era cuestión de tiempo que un descubrimiento rompiera la seguridad fundamental de este sistema: su cadena de confianza.

En 2011, George Hotz (junto con el equipo fail0verflow) publicó otro descubrimiento revolucionario: **las claves de cifrado privadas que Sony usa para firmar los binarios ejecutados por `metldr`**. Los binarios cargados durante esa etapa de arranque están firmados con una clave ECDSA. Al ser un sistema de cifrado asimétrico, esto significa que cualquiera que posea la clave privada (Sony, y ahora _otros_) puede cifrar y firmar binarios; por tanto, parecerán 'auténticos' a ojos de `metldr`. Como `metldr` es la tercera etapa de arranque antes de cargar `lv1` (el hipervisor), esto significa que los hackers podrán personalizar o desarrollar su propio hipervisor, kernel y todo lo que haya por debajo. Y para más inri, todos los PlayStation 3 del mercado también considerarán auténticos los binarios personalizados. En definitiva, un exploit [al estilo Pandora](playstation-portable#tab-6-3-pandora) realizado completamente con software.

El descubrimiento de esta clave, que debería haber sido computacionalmente inviable, es posible gracias a lo que se considera un 'error garrafal' en la implementación de Sony del algoritmo ECDSA. En pocas palabras, la fórmula matemática usada para ECDSA utiliza un valor aleatorio que Sony nunca modificó en todos los archivos de actualización que distribuyó [@anti_piracy-ecsda], convirtiendo ese número en una constante y facilitando así la resolución de las demás variables, que es exactamente lo que ocurrió.

Los efectos de este descubrimiento se describen en los siguientes párrafos.

### La era del Custom Firmware (CFW) {.tabs-close}

Descifrar `metldr` significó que todo el mundo podía crear ya sistemas 'oficiales' para la PS3, lo que dio lugar a una avalancha de 'sabores' del GameOS producidos por distintas comunidades con diversas personalizaciones. Estos sistemas eran modificaciones de los archivos oficiales del firmware de Sony (que Sony distribuía como actualizaciones) y reempaquetados usando las claves filtradas de Sony, por lo que podían instalarse en cualquier consola. El resultado se llamó **Custom Firmware** (CFW) y se convirtió en el método de facto para hackear esta consola, al menos hasta que Sony respondió con medidas contundentes.

![Mi instalación de CFW con el 'VSH menu' abierto. Esta variante (llamada 'Rebug') también me permitió convertir mi consola en una estación de depuración (fíjate en la dirección IP en la esquina inferior derecha; conviene introducirla en el depurador para conectarse a un proceso en ejecución) y experimentar con mi propio homebrew.](cfw/vshmenu.jpg)

Mientras tanto, muchos CFW aparecieron en la red con distintos nombres ('Rebug', 'Ferrox', etc.) y contenían personalizaciones como [@anti_piracy-patches]:

- Deshabilitar las verificaciones de firma en cualquier módulo instalado o por instalar.
- Habilitar la lectura y escritura (el clásico _peek and poke_) sobre cualquier dirección de memoria, ya sea usando el Hypervisor (nivel 1) o el Kernel (nivel 2).
- Activar funciones de depuración ocultas para instalar módulos empaquetados como archivos 'pkg'. Estos no necesitaban estar firmados con las claves de Sony para funcionar en un entorno CFW.
- Permitir montar imágenes de disco como un disco Blu-ray virtual.
- Restaurar OtherOS e incluso mejorarlo eliminando las restricciones impuestas por el Hypervisor. El resultado se llamó OtherOS++.
- Sobrescribir la base de datos de la EEPROM de Syscon para poder instalar cualquier versión del sistema. Esto también se conoce como _QA Toggling_.
- Alterar el estilo del XMB (eliminar la advertencia de epilepsia, permitir capturas de pantalla en el juego, etc.).

Y mi favorita: replicar las funciones de depuración de un testkit, permitiendo que cualquier consola de consumo se convirtiera en una estación de depuración. Esto podía hacerse bien instalando un CFW con capacidades de depuración, bien un CFW que pudiera convertir la consola de consumo (llamada 'CEX') en un modelo de depuración (llamado 'DEX') alterando datos específicos de la consola en la memoria Flash.

#### La contundente respuesta de Sony

De manera similar a lo ocurrido tras la aparición de los [CFW](playstation-portable#tab-6-4-cfw-and-beyond) para el PSP, Sony respondió con dos actualizaciones de seguridad:

Desde el **lado del software**, Sony distribuyó dos actualizaciones del sistema que mejoraron el sistema de seguridad:

- Con `3.56`, los binarios se firman con nuevas claves de cifrado resistentes al descubrimiento previo de ECDSA [@anti_piracy-keys]; por tanto, los creadores de CFW no pueden personalizar los nuevos binarios (al no tener las claves privadas para volver a cifrarlos). Además, se distribuye una nueva revisión de la aplicación 'actualizador del sistema', que aplica los nuevos certificados en los archivos de actualización del sistema (`PS3UPDAT.PUP`), lo que significa que incluso si los hackers consiguen empaquetar un nuevo CFW, solo las consolas con la versión `3.55` o inferior podrán instalarlo [@anti_piracy-spkg].
- Más adelante, la actualización del sistema `3.60` renovó el proceso de arranque, anuló `metldr` y promovió a `lv0` para tomar el control en el arranque de los loaders (`lv1ldr`, `lv2ldr`, `appldr` e `isoldr`). En definitiva, esto significaba que los hackers no podían modificar los nuevos archivos del sistema sin descifrar primero `lv0` (encontrar su clave privada).
  - Esto ocurrió finalmente a finales de 2012, cuando un equipo llamado "The Three Musketeers" publicó las claves de `lv0` [@anti_piracy-lv0leak], lo que allanó el camino para nuevos CFW hechos a partir de versiones del sistema más nuevas que `3.55`. Aunque, debido a los cambios mencionados en el actualizador, solo los usuarios con la versión `3.55` o inferior (incluido cualquier CFW con las comprobaciones de firma desactivadas) pueden instalarlo.

Desde el **lado del hardware**, no solo los modelos posteriores de la PS3 (CECH-25xxx tardíos, CECH-3xxx y CECH-4xxx) venían preinstalados con una versión del sistema superior a `3.55`, sino que también contienen una variante diferente de `bootldr`/`lv0ldr` (llamada `lv0ldr.1`) que no solo descifra y carga `lv0`, sino que también lee un nuevo archivo del sistema llamado `lv0.2`. Este último contiene metadatos sobre `lv0` [@anti_piracy-lv0ldr] para garantizar que `lv0` no ha sido manipulado. `lv0.2` está firmado con una nueva clave (también invulnerable al descubrimiento previo de ECDSA), impidiendo así que los hackers tomen el control de la cadena de arranque.

Hasta el día de hoy, **estos modelos no son capaces de ejecutar un CFW**, por lo que reciben el apodo de _unhackables_. Aunque sí pueden ejecutar un 'Hybrid Firmware' (HFW), del que hablaremos más adelante.

Con el tiempo, el número de consolas compatibles con CFW solo disminuyó. Por ello, los PS3 que no se habían actualizado más allá de la versión `3.55` se convirtieron en una especie de reliquias. Mientras tanto, creció la demanda de alternativas, como los downgraders (para revertir a la versión `3.55` del sistema en los modelos antiguos) y los ODE (para reproducir juegos pirata en los modelos nuevos).

#### El renacimiento del homebrew

Tras un largo período de espera para los usuarios que habían perdido la ventana para instalar un CFW, a finales de 2017 un equipo de hackers publicó **PS3Xploit**, una colección de exploits y utilidades [@anti_piracy-ps3xploit] que devolvió la posibilidad de instalar un CFW en los modelos antiguos sin necesidad de un costoso downgrader (y de las habilidades para operarlo).

El payload principal de PS3Xploit replica el trabajo de un downgrader de hardware (parcheando archivos de CoreOS) íntegramente mediante software, y funciona así:

1. El punto de partida es el navegador de internet del XMB, construido sobre WebKit. PS3Xploit usa JavaScript para obtener **ejecución de código arbitrario dentro del userspace del sistema** (y fuera del entorno de JavaScript). Para iniciarlo, los usuarios solo tienen que abrir el navegador web nativo del XMB, introducir una URL que apunte al host de PS3Xploit y dejar que haga su trabajo.
2. Resulta que el kernel proporciona llamadas al sistema que pueden usarse para **sobrescribir los archivos del sistema operativo en la memoria Flash**. Además, el Visual Shell (XMB) y sus plugins almacenan en memoria rutinas que hacen uso de esas llamadas.
3. PS3Xploit no puede activar directamente esas llamadas al sistema debido a la protección 'no-execute' del Hypervisor, que impide que el exploit cargue nuevo código en el userland. Sin embargo, puede encontrar la manera de sobrescribir la memoria Flash 'tomando prestadas' las rutinas del Visual Shell.
4. En consecuencia, PS3Xploit procede a modificar la pila de ejecución de WebKit para redirigir la ejecución a las rutinas del Visual Shell. Este tipo de técnica (corromper la pila para desviar la ejecución hacia otro código residente en memoria) se llama **Return Oriented Programming** (ROP) y es muy popular en el ámbito de la seguridad informática. Una forma de mitigarlo es implementar **Address Space Layout Randomisation** (ASLR), que dificulta adivinar la ubicación de las rutinas (llamadas _gadgets_) pero, como puedes imaginar, el hipervisor de Sony carece de ASLR.
5. Por último, esas llamadas al sistema se activan con los parámetros de PS3Xploit y así reemplazan los archivos de CoreOS (la primera parte del sistema operativo, almacenada en la memoria Flash) por versiones parcheadas [@anti_piracy-flash_writer].
6. La consola ya puede instalar actualizaciones de software no oficiales, una oportunidad que el usuario puede aprovechar para instalar un custom firmware. Sin embargo, aún no puede hacer downgrade de la versión del sistema, pero una vez instalado un CFW actualizado, el usuario puede instalar más utilidades para hacer downgrade y, si lo desea, instalar un CFW mejor equipado.

Como puedes ver, este _regalo caído del cielo_ devolvió los custom firmwares al primer plano y dejó obsoletos los downgraders de hardware y los ODE. Por otro lado, para aquellas unidades que de ninguna manera podían instalar un CFW (los _unhackables_), el equipo ofreció posteriormente **PS3Hen**, un paquete de exploits diferente centrado en habilitar un subconjunto de funciones del CFW (incluyendo la capacidad de ejecutar homebrew). Este se instala como una entrada en el XMB y el usuario debe ejecutarlo cada vez que enciende la consola para volver a habilitar la ejecución de aplicaciones homebrew.

#### La respuesta parcial de Sony

La suerte quiso que Sony solo tomara pequeños pasos para bloquear PS3Xploit (quizás porque todo ello ocurrió años después de que la PlayStation 4, sucesora de la PS3, llegara a las tiendas). Publicaron algunas actualizaciones del sistema que no corregían esta cadena de exploits, pero sí eliminaban la rutina de WebKit usada para arrancar la cadena. En respuesta, los hackers publicaron actualizaciones de software ligeramente modificadas que restauraban esa entrada (y de algún modo, no necesitaban ser refirmadas) [@anti_piracy-hfw]. Estas actualizaciones personalizadas se llamaron **Hybrid firmware** (HFW) y, en el momento de escribir estas líneas, son la opción de facto para habilitar el homebrew en los sistemas unhackables.

Y aquí concluye la saga de antipiratería y homebrew. En mi humilde opinión, no creo que Sony esté interesada en invertir más esfuerzo en esta consola. Así que no esperaría más juegos del gato y el ratón en este ámbito.

## ¡Esto es todo, amigos!

![Dos PS3, una reproduciendo aquel juego que-no-debe-nombrarse.<br>Aparte de la jugabilidad llena de fallos y la -inusual- trama, el género me gusta bastante.](myps3s.jpg)

¡Has llegado al final!

A decir verdad, en un principio planeé que fuera un proyecto de dos meses, pero se convirtió en todo un verano (y ya has visto por qué). En cualquier caso, espero que esto te haya ayudado a ampliar tu conocimiento sobre este sistema y a comprender el razonamiento detrás del progreso tecnológico de aquella era. Así, ahora puedes pensar más allá de los rumores populares que _las masas_ repiten constantemente.

Si te lo preguntas, para este análisis he utilizado tres modelos de PS3:

- Un CECH-3001 _unhackable_ de mis años de adolescencia (¡por alguna razón la caja dice que es un modelo CECH-25XX!). Lo saqué del desván recientemente para probar PS3Hen.
- Un CECH-2100 que compré después de que saliera PS3Xploit. Con él, por fin pude instalar homebrew.
- Un modelo CECHA (solo lanzado en Japón) que adquirí en agosto de 2021 para recopilar material para este artículo (principalmente fotografías e información sobre la compatibilidad con PS2). Era bastante caro, pero afortunadamente las donaciones de los seguidores me ayudaron a compensar los costes.

Aunque he repetido muchas veces que Cell fue una tecnología revolucionaria, puede que hayas notado que no mencioné lo poco fiables que resultaron ser los primeros modelos. Fue la primera vez que escuché hablar de una consola que se averiaba simplemente por jugar un rato. Y es que esos aparatos consumían electricidad como dinosaurios y se calentaban como un horno (un horno _de plástico_). Por suerte, tenía un modelo Slim (que debería haberse llamado 'el-que-funciona')... ¡Vaya era tan apresurada, eh?

En cuanto a lo que sigue en mi agenda, me tomaré un tiempo antes de empezar el siguiente artículo para trabajar en otras áreas de mejora del sitio web y ponerme al día con asuntos personales.

¡Hasta la próxima!  
Rodrigo
