---
short_title: Arquitectura de la PlayStation
long_title: Arquitectura de la PlayStation
long_name: PlayStation
name: PS1
subtitle: Un novato prometedor
date: 2019-08-08
release_date: 1994-12-03
generation: 5
javascript: ['threejs']
top_tabs:
  Models:
    - title: "Original"
      caption: "La PlayStation original.<br>Lanzada el 03/12/1994 en Japón, el 09/09/1995 en Norteamérica y el 29/09/1995 en Europa."
      file: original
      active: true
      latex_height: 87
    - title: "Slim"
      caption: "La PS One (revisión slim).<br>Lanzada el 07/07/2000 en Japón, el 19/09/2000 en Norteamérica y el 29/09/2000 en Europa. <br>El resultado de condensar tantos circuitos integrados personalizados en uno solo."
      file: slim
      paperback_latex_height: 92
      hardcover_latex_height: 95
  Motherboard:
    caption: "Mostrando el modelo 'SCPH-1000'.<br>El resto de los chips están montados en la parte posterior.<br>Los modelos posteriores incorporaron SG-RAM en lugar de VRAM y prescindieron de la mayor parte de las E/S externas y las salidas de vídeo."
  Diagram: 
    caption: "La Bus Interface Unit también está conectada a puertos especiales de la GPU y la SPU."
    paperback_latex_height: 93
    hardcover_latex_height: 95

# Historical
aliases: [/projects/consoles/playstation/]
---

## Una introducción rápida

Sony era consciente de que el hardware 3D podía complicar sobremanera el desarrollo de software. Por eso, su primera consola mantiene un diseño *sencillo* y *práctico*... aunque eso tiene su precio.

## {.supporting-imagery}

## CPU

Esta sección disecciona el **Sony CXD8530BQ**, uno de los dos grandes chips que alberga esta consola. Es lo que hoy llamaríamos un 'System-on-Chip'.

### Los orígenes

El procesador principal responde a uno de esos esquemas del tipo 'X diseñado por Y, basado en Z y fabricado de segunda fuente por W', demasiado denso para resumirse en pocas frases. ¿Por qué no comenzamos con algo de contexto histórico?

#### Un poco de historia {.tabs .active}

![Un Macintosh Quadra 700 junto a una tarjeta de actualización PowerPC. Como ocurrió con muchos usuarios de la Motorola 68k, los 90 marcaron el inevitable giro hacia las CPU basadas en RISC (en el caso de Apple, el PowerPC).](quadra.webp){.tab-float}

Los primeros años noventa estuvieron marcados por un punto de inflexión en la fortuna de muchas CPU populares. Los otrora dominantes procesadores de 8 bits, como el [Z80](master-system#cpu) y el [6502](nes#core-functionality), habían quedado ya en un segundo plano, y la famosa [68000](mega-drive-genesis#cpu) de Motorola, junto con otros [diseños de 16 bits](super-nintendo#cpu) que habían cosechado éxito a finales de los 80, eran ahora candidatos a la sustitución. Incluso en el campo de los PC, Andrew S. Tanenbaum, en su célebre debate con Linus Torvalds, predijo que la arquitectura x86 de Intel solo le quedaban *cinco años* antes de desaparecer del mercado doméstico.

A primera vista, podría parecer que el desarrollo tecnológico había llegado a un punto muerto. Sin embargo, una nueva oleada de CPU relativamente desconocidas comenzaba a abrirse paso en los dispositivos de consumo. Muchos de estos diseños tenían su origen en el ámbito académico, con la intención de demostrar principios de diseño concretos. Entre los ejemplos más destacados de aquella época encontramos:

- **MIPS**: adoptada por Silicon Graphics Incorporated (orientada a estaciones de trabajo gráficas).
- **PowerPC**: adoptada por Apple (orientada a la autoedición).
- **SPARC**: desarrollada por Sun Microsystems (orientada a servidores y estaciones de trabajo empresariales).
- **ARM**: [desarrollada por Acorn](game-boy-advance#the-cambridge-miracle), inicialmente orientada al mercado doméstico, antes de expandirse a las PDA, los teléfonos móviles y otros dispositivos integrados.
- ... y muchos más chips de 'microcontrolador' que aún no habían sido finalizados ni adoptados por ningún sector importante —como el **SH de Hitachi** y el **V810 de NEC**—. Para sorpresa de todos, estos fueron posteriormente elegidos para la [Sega Saturn](sega-saturn) y la [Nintendo Virtual Boy](virtual-boy), respectivamente.

Todos estos procesadores tenían algo en común: se adherían a la disciplina del **Ordenador de Set de Instrucciones Reducido** (RISC, del inglés *Reduced Instruction Set Computer*), que transformó radicalmente la manera en que se diseñaban y programaban estos chips. Una de las reglas de la arquitectura RISC dictaba que una sola instrucción no podía combinar el acceso a memoria con operaciones sobre registros. Esto permitió a los diseñadores de hardware simplificar la circuitería responsable de ejecutar instrucciones... y luego potenciarla con técnicas de paralelismo.

#### MIPS y Sony {.tab}

![El SGI Iris 4D/80, una potente estación de trabajo gráfica con diseño de torre doble. La serie 4D inauguró las CPU MIPS en los ordenadores SGI; este modelo en concreto incluye el procesador R2000 [@cpu-iris]. Esta foto la tomé en el Computer History Museum (Mountain View, California) durante mi segunda visita, en marzo de 2025.](irix.webp){.tab-float}

**MIPS Computer Systems** nació del afán de sus fundadores (profesores de Stanford) por convertir su investigación en procesadores reales. Esto encajaba bien con el apetito de los inversores de capital riesgo de Silicon Valley en los años 80, ansiosos por financiar este tipo de innovaciones [@cpu-chm_mips]. Su primera CPU, la 'MIPS R2000', está considerada el primer procesador comercial en incorporar un diseño RISC, y encontró hueco en numerosas estaciones de trabajo UNIX.

Sin embargo, no fue hasta 1987 cuando los chips de MIPS comenzaron a ser el centro de atención, gracias a su adopción (y posterior adquisición) por parte de **Silicon Graphics Incorporated** (SGI) para impulsar sus equipos. SGI fue una fuerza influyente en el mercado de los gráficos por ordenador, especialmente con el desarrollo de [pipelines de vértices acelerados por hardware](nintendo-64#graphics), una función que originalmente recaía en el software (dentro de la CPU). Tras la fusión, SGI consolidó una posición de liderazgo tanto en el sector de las CPU como en el de los gráficos.

Antes del desarrollo de la PlayStation, MIPS adoptó un modelo de negocio basado en la **concesión de licencias de propiedad intelectual**, en el que los diseños de CPU se vendían en forma de licencias, y los licenciatarios podían entonces personalizarlos y fabricarlos libremente. Entre sus productos figuraba la **CPU R3000A**, presente en su catálogo de gama baja. Como tal, la R3000A no pertenecía a la línea estrella (a diferencia de la R4000, que [otros](nintendo-64#cpu) elegirían más adelante), pero resultaba una inversión atractiva en cuanto al coste.

Volviendo al tema que nos ocupa, Sony diseñó sus chips de audio y gráficos internamente, pero aún necesitaba el chip principal que los pusiera en marcha. La CPU elegida tenía que ser lo suficientemente potente para demostrar las *impresionantes* capacidades de los chips de Sony, sin dejar de ser asequible para mantener la consola a un precio competitivo.

#### LSI y el encargo {.tab}

Al mismo tiempo, **LSI Logic** (un fabricante de semiconductores) era licenciatario de MIPS y ofrecía a empresas un programa de CPU 'a medida'. Este servicio, conocido como **CoreWare**, permitía a los clientes ensamblar paquetes de CPU personalizados eligiendo entre una serie de bloques modulares [@cpu-lsi]. Entre los componentes de la biblioteca CoreWare se encontraba el bloque 'CW33300', un núcleo de CPU derivado del LSI LR33300 —un chip de CPU de catálogo que LSI también comercializaba—.

¿Adónde quiero llegar con todo esto? Resulta que tanto el LR33300 como el CW33300 son **compatibles en binario con la familia MIPS R3000A**. Sus arquitecturas difieren ligeramente en algunos aspectos, pero la interfaz de programación (ISA MIPS I) es la misma.

Al final, Sony encargó a LSI la construcción de su paquete de CPU. Eligieron el CW33000, realizaron algunos ajustes e integraron todo con otros bloques para dar forma al chip que encontramos en la placa base de la PlayStation.

### Lo que ofrece {.tabs-close}

![El chip SoC en la placa base de la PlayStation, donde reside el núcleo basado en el MIPS R3000A.](cpu_chip.jpg){latex_width="85%"}

El núcleo de CPU resultante funciona a **33,87 MHz** y ofrece:

- La **ISA MIPS I**: la primera versión de la Arquitectura de Set de Instrucciones de MIPS. Entre otras cosas, utiliza **palabras de 32 bits** e incluye instrucciones de multiplicación y división.
- **32 registros de uso general** y **2 registros de multiplicación/división**: también de 32 bits. Uno de los registros de uso general (`R0`) está cableado a cero, un rasgo habitual en los diseños RISC.
- **Bus de datos de 32 bits**: en la PS1, este bus se bifurca en dos:
  - **Bus principal** (32 bits): conecta el MDEC y la GPU.
  - **Bus secundario** (16/8 bits): conecta el resto de componentes y la E/S. La conexión entre ambos la gestiona la **Bus Interface Unit**, que también permite acceder a puertos especiales de la GPU y la SPU.
- **Bus de direcciones de 32 bits**: permite acceder a hasta 4 GB de memoria física; es decir, RAM, E/S mapeada en memoria, etc.
- **Pipeline de 5 etapas**: permite procesar hasta cinco instrucciones simultáneamente (véase un [artículo anterior](sega-saturn#cpu) para una explicación detallada).
- **4 KB de caché de instrucciones**: también puede 'aislarse', lo que permite al programa manipular la caché de instrucciones directamente.
- Sorprendentemente, **no hay caché de datos**. El **1 KB de memoria** que normalmente se reservaría para ella se mapea en una dirección fija [@cpu-mame_cpu]. Esta área recibe el nombre de **Scratchpad** y se utiliza como 'SRAM rápida'.

![Cuatro chips de 512 KB de RAM EDO.](edo_chips.webp){latex_width="85%"}

Dicho esto, Sony dotó al sistema de **2 MB de RAM** de uso general. Curiosamente, optaron por instalar chips **Extended Data Out** (EDO) en la placa base. Estos son algo más eficientes que la DRAM convencional y presentan menor latencia.

### Tomando el control de la CPU

En determinados momentos, cualquier subsistema —los gráficos, el audio o la unidad de CD— necesitará grandes bloques de datos a alta velocidad. Sin embargo, la CPU no siempre es capaz de satisfacer esa demanda.

Por ello, el controlador de CD-ROM, el MDEC, la GPU, la SPU y el puerto paralelo tienen acceso a un **controlador DMA** dedicado cuando lo precisan. El **Acceso Directo a Memoria** (DMA, del inglés *Direct Memory Access*) toma el control del bus principal para realizar transferencias de datos de forma independiente. Esto se traduce en un ancho de banda considerablemente mayor que si la transferencia pasara por la CPU, aunque esta sigue siendo necesaria para configurar la operación DMA.

Cabe señalar que, una vez que el DMA entra en funcionamiento, la CPU no puede acceder al bus principal. Esto significa que la CPU quedará inactiva a menos que tenga algo en la Scratchpad con lo que mantenerse ocupada.

### Complementando el núcleo

Al igual que otras CPU basadas en el MIPS R3000, el CW33000 admite configuraciones de hasta cuatro coprocesadores. Sony lo personalizó con tres:

#### System Control Coprocessor {.tabs .active}

Identificado como 'CP0', el **System Control Coprocessor** es un bloque habitual en las CPU MIPS. En los sistemas basados en el R3000, como este, el CP0 gobierna cómo se implementa la caché, lo que permite el acceso directo tanto a la caché de datos (en forma de 'Scratchpad') como a la caché de instrucciones (mediante el 'aislamiento de caché'). El coprocesador de control también gestiona interrupciones, excepciones y puntos de interrupción (breakpoints), estos últimos de gran utilidad durante la depuración.

> Espera, ¿no deberían los coprocesadores limitarse a _ampliar_ las funciones de la CPU? ¿Por qué el CP0 está tan estrechamente ligado a esta?

Efectivamente, los núcleos R3000 dependen del coprocesador de control del sistema para usar muchos componentes. Si esto debería ser 'legal' o no depende de cómo se interprete la palabra 'coprocesador'. Según MIPS, un coprocesador no es estrictamente una parte opcional de la CPU: también puede dirigir el entorno de esta (como la caché o las interrupciones). Por tanto, un coprocesador puede ser parte integral del sistema. Conviene tenerlo en cuenta al hablar de sistemas basados en MIPS.

Los posteriores [sistemas basados en el R4000](nintendo-64) incorporaron una Unidad de Gestión de Memoria (MMU, del inglés *Memory Management Unit*) y una Translation Lookaside Buffer (TLB) en este bloque, ampliando así sus capacidades y asumiendo [nuevas funciones](nintendo-64#memory-management).

#### Geometry Transformation Engine {.tab}

El 'CP2', o **Geometry Transformation Engine** (GTE), es un procesador matemático especializado que acelera los cálculos de vectores y matrices.

Aunque solo opera con tipos de punto fijo, ofrece operaciones de gran utilidad para los gráficos 3D, como:

- Multiplicación y suma de matrices o vectores, y cuadrado de vectores.
- Transformación de perspectiva (para proyecciones 3D).
- Producto vectorial de dos o tres vectores (el segundo caso se utiliza para el clipping).
- Numerosas funciones de interpolación con distintos parámetros.
- Depth cueing y valores de color derivados de una fuente de luz (para operaciones de iluminación y color).
- Promediado de profundidad (Z/depth averaging). Sospecho que se utiliza para la 'tabla de ordenación' (doy más detalles en la sección 'Gráficos').

No hace falta memorizarlo todo para seguir el resto del artículo. Basta con tener presente que el GTE se encarga de las etapas iniciales del pipeline gráfico, incluyendo la proyección 3D, la iluminación y el clipping. Con esto se generan los datos necesarios para enviarlos a la GPU y que esta los renderice.

#### Motion Decoder {.tab}

El **Motion Decoder**, también llamado 'MDEC' o 'Macroblock Decoder', es otro procesador que convive con la CPU. En este caso, descomprime 'macroblocks' a un formato que la GPU puede interpretar. Un macroblock es una estructura de datos que contiene una imagen codificada de forma similar a JPEG.

El MDEC descomprime mapas de bits de 8×8 píxeles a 24 bpp (bits por píxel). En conjunto, el MDEC puede procesar 9.000 macroblocks por segundo [@cpu-walker], lo que permite reproducir un **vídeo de movimiento completo** (FMV, del inglés *Full-Motion Video*) de 320×240 px a 30 frames por segundo.

El DMA se utiliza para transferir datos comprimidos entre el CD-ROM, la RAM y el MDEC. El mismo recorrido se usa en sentido inverso, aunque en este caso el destino es la VRAM.

Aunque este componente reside dentro del SoC y comparte el mismo bus de datos, no es un coprocesador MIPS; la CPU y el DMA acceden a él a través del mapa de memoria, en lugar de interceptar instrucciones.

Para más información sobre el MDEC, recomiendo consultar los recursos de Sabin [@cpu-sabin] y Czekański [@cpu-jakub_mdec].

### ¿Faltan unidades? {.tabs-close}

Hasta ahora hemos visto un 'CP0' y un 'CP2', pero **¿dónde está el 'CP1'?** Pues bien, este está reservado para una **Unidad de Punto Flotante** (FPU, del inglés *Floating-Point Unit*) —y por desgracia Sony no incluyó ninguna—. Esto no significa que la CPU sea incapaz de realizar operaciones aritméticas con números decimales; simplemente no será suficientemente rápida (si se recurre a rutinas por software) ni especialmente precisa (si se trabaja con aritmética de punto fijo).

La lógica del juego (física, detección de colisiones y similares) puede apañárselas bien con la aritmética de punto fijo. La codificación en punto fijo representa los números decimales con un número inmutable de posiciones decimales. Esto conlleva una pérdida de precisión en ciertas operaciones, pero hay que recordar que estamos ante una videoconsola, no un simulador de vuelo profesional. En consecuencia, el equilibrio entre precisión y rendimiento puede considerarse razonable.

Por cierto, si quieres refrescar conceptos como 'punto fijo', 'punto flotante', 'decimal' o 'entero', te recomiendo echar un vistazo a la entrada de Gabriel Ivancescu [@cpu-gabriel].

### Un sinfín de delays

Como ya hemos visto, el CW33300 es un procesador segmentado (con pipeline), lo que significa que encola varias instrucciones y las ejecuta en paralelo en distintas etapas. Esto mejora enormemente el rendimiento de instrucciones, pero sin un control adecuado puede dar lugar a **riesgos de pipeline**, con los consiguientes errores de cálculo.

La arquitectura MIPS I es especialmente susceptible a [@cpu-chen]:

- **Riesgos de control**: instrucciones que se ejecutan cuando no deberían.
- **Riesgos de datos**: instrucciones que operan con datos desactualizados antes de que estos se hayan actualizado.

![Instrucciones de 'Spyro The Dragon' visualizadas en el depurador NO$PSX. Obsérvese cómo `LW` (load word from memory), `JAL` (jump and link) y `BNE` (branch on not equal) van seguidas cada una de un delay slot para evitar riesgos. Las instrucciones marcadas en rojo (anteriores a la dirección `800597C4`) son instrucciones de relleno (sin utilidad), mientras que las marcadas en azul realizan cálculos con propósito.](delay_slots.jpg){.open-float .border paperback_latex_width="95%" hardcover_latex_width="85%" #fig-opcodes_fillers}

En consecuencia, las CPU MIPS I presentan el siguiente comportamiento:

- **Cualquier instrucción que siga a un opcode de 'rama' (branch) o 'salto' (jump) se ejecuta incondicionalmente**: por ello, los desarrolladores deben rellenar manualmente el pipeline con instrucciones modestas (como `calcula 0 más 0`) tras el salto para mitigar el riesgo. Estos rellenos se denominan **branch delay slots**.
  - Las CPU modernas convirtieron este fenómeno en una ventaja: la [predicción de saltos](gamecube#cpu). Al añadir circuitería para detectar el riesgo, la CPU puede descartar cálculos especulativos si la condición de rama o salto no se cumple; si se cumple, la CPU gana tiempo.
- **Las instrucciones 'load' no detienen el pipeline hasta que el dato recuperado está disponible**: la segunda etapa del pipeline (llamada `RD` o 'Read and Decode') recoge los operandos que se usarán en la tercera etapa (`ALU`) [@cpu-manual]. La cuarta etapa (`MEM`, de 'access MEMory') busca datos en memoria (por ejemplo, en la RAM principal o en el lector de CD). El problema es que, para cuando una instrucción `load` obtiene el dato externo, la siguiente instrucción ya ha leído sus operandos. Por tanto, cuando una instrucción depende del dato de un `load` anterior, debe insertarse un relleno para garantizar que los operandos correctos se lean a tiempo.

{.close-float}

Como ilustra el ejemplo [@fig-opcodes_fillers], algunos delay slots se rellenan con instrucciones con propósito, que realizan cálculos no afectados por el riesgo. Por tanto, los delay slots no siempre equivalen a ciclos desperdiciados.

#### Una filosofía de base

Expuesto esto, quizás te preguntes por qué se comercializaría un procesador con tales defectos. Pues bien, uno de los principios de la filosofía RISC es que la carga de la programación de la CPU se traslada del desarrollador al **compilador**. MIPS, en particular, **priorizó la producción de compiladores de alta calidad** (incluyendo ensambladores) para acompañar a sus nuevas CPU [@cpu-chm_mips]. La empresa concebía que los desarrolladores usarían un lenguaje de alto nivel (como C) mientras que la cadena de herramientas se encargaba automáticamente de los riesgos, ya fuera reordenando instrucciones para rellenar los delay slots o insertando rellenos sin utilidad como último recurso.

De hecho, exponer el pipeline de la CPU a los desarrolladores era una estrategia de diseño fundamental en MIPS, como evidencia el propio acrónimo: 'Microprocessor without Interlocked Pipelined Stages'.

En definitiva, aunque no es agradable ver un programa rellenando la CPU de burbujas, creo que MIPS afrontó este reto de forma muy ingeniosa. Dicho esto, tampoco estuvo exento de inconvenientes. Como [los años posteriores revelaron](nintendo-64#cpu), exponer el pipeline complicó sobremanera la compatibilidad con versiones anteriores, especialmente a medida que las futuras CPU debutaban con microarquitecturas revolucionarias.

## Gráficos

Como recordatorio, una gran parte del pipeline gráfico la lleva a cabo el GTE. Esto incluye la transformación de perspectiva (que proyecta el espacio 3D sobre un plano 2D usando la perspectiva de la cámara) y la iluminación. Los datos procesados se envían a la **Graphics Processor Unit** (GPU) de Sony para su renderizado.

### Organizando el contenido

El sistema cuenta con **1 MB de Video RAM** (VRAM) destinada a almacenar el frame buffer, las texturas y demás recursos que la GPU necesitará para renderizar la escena. La CPU puede rellenar esta área mediante DMA.

El chip instalado (**VRAM**) es de doble puerto, como el de la [Virtual Boy](virtual-boy#organising-the-content). La VRAM utiliza dos buses de 16 bits, lo que permite el acceso simultáneo por parte de la CPU, el DMA, la GPU y el codificador de vídeo.

![Distribución de la memoria con VRAM.](_diagrams/vram/vram.png){.tabs-nested .active title="VRAM"}

![Distribución de la memoria con SGRAM.](_diagrams/vram/sgram.png){.tabs-nested-last title="SGRAM"}

No obstante, en revisiones posteriores de esta consola, Sony cambió a chips **Synchronous Graphics RAM** (SGRAM) (la alternativa de puerto único, con un bus de datos individual de 32 bits). *¡Buu!*... Bueno, siendo justos, cada opción tiene sus ventajas e inconvenientes.

Lo cierto es que, debido a diferencias de temporización, algunos juegos posteriores (como *Jet Moto 3*) muestran gráficos con fallos al ejecutarse en sistemas basados en VRAM. Si quieres conocer los detalles, las 'Nocash PSX Specifications' de Martin Korth documentan las distintas temporizaciones y demás [@cpu-korth].

### Dibujando la escena

Si has leído el [artículo de la Sega Saturn](sega-saturn), cabe decirte que el diseño de esta GPU es *mucho* más sencillo. Para empezar, la GPU se compone de un **único chip**.

![El chipset gráfico en mi revisión de la placa base: la GPU (derecha), 1 MB de SGRAM (arriba a la izquierda) y el DAC de vídeo (abajo a la izquierda).](gpu_set.webp)

Para mostrar cómo se dibuja una escena, utilizaré principalmente *Spyro: Year of the Dragon*, de Insomniac, como ejemplo.

#### Comandos {.tabs .active}

![Pipeline básico de comandos de la GPU.](_diagrams/gpu_commands.png){.tab-float}

Para empezar, la CPU envía datos de geometría (vértices) a la GPU rellenando su buffer FIFO interno de 64 bytes con **comandos** (hasta tres). Estos comandos pueden solicitar a la GPU que renderice algo, cambie una configuración o manipule la VRAM.

En esencia, un comando de renderizado especifica cómo y dónde dibujar una primitiva. La GPU puede dibujar **líneas**, **rectángulos** y **triángulos** de forma individual —siendo estos últimos el ingrediente fundamental para construir modelos 3D complejos—.

Una vez recibida la geometría, se aplica el **clipping** para omitir operaciones sobre polígonos no visibles (que se encuentran fuera del viewport de la cámara).

La posición de cada primitiva se define mediante un sistema de coordenadas X/Y que se mapea directamente sobre el frame buffer. La GPU de la PS1 emplea un **modelo de coordenadas enteras**, en el que cada coordenada corresponde al punto central de un píxel (denominado **punto de muestreo** o *sampling point*). En otras palabras, no existen coordenadas fraccionarias.

#### Enfoque de visibilidad {.tab}

![Crash Bandicoot (1996) empleó geometría preordenada para ganar rendimiento [@graphics-gavin]. En consecuencia, su cámara solo se desplaza hacia delante o hacia atrás.](crash.jpg){.tab-float}

Al igual que la [competencia](sega-saturn), la PS1 no incluye ninguna función de hardware que aborde el [problema de la visibilidad](sega-saturn#an-introduction-to-the-visibility-problem). Sin embargo, la GPU gestiona los polígonos ordenados mediante una **tabla de ordenación**: una estructura dedicada en la que cada entrada se indexa por un valor de profundidad (también llamado 'valor Z') y contiene la dirección donde reside el comando de GPU correspondiente [@graphics-table].

La CPU debe ordenar manualmente los polígonos y luego colocar referencias en las entradas correspondientes de la tabla. A continuación, la CPU ordena al DMA que envíe la tabla a la GPU. Este proceso permite a la GPU renderizar la geometría en el orden correcto.

También se proporcionan varias funciones DMA para ayudar tanto a la CPU como a la GPU en la creación y recorrido de esta tabla.

#### Rasterización {.tab}

![Vista en wireframe de la escena.](spyro/wireframes.png){.tab-float}

Una vez que la GPU decodifica los comandos, llega el momento de convertir la geometría recibida (vértices) en píxeles. Esto permite al sistema aplicar mapeado de texturas y efectos, para finalmente mostrar el resultado en un panel bidimensional (como tu televisor o monitor). Para ello, la GPU reserva una matriz de píxeles como área de trabajo: esto se denomina **frame buffer**. En comparación con la más compleja [Sega Saturn](sega-saturn), la GPU solo necesita un único frame buffer.

La rasterizadora es la unidad encargada de convertir vectores en líneas, triángulos o rectángulos —y finalmente en píxeles—. El proceso difiere drásticamente según la primitiva solicitada [@graphics-spx]:

- **Los triángulos** son el tipo más complejo (y versátil), y admiten texturizado y sombreado.
- **Las líneas** se dibujan más rápidamente, pero no son aptas para superficies texturizadas. El sombreado sí está disponible.
- **Los rectángulos** son también más rápidos, pero solo admiten un [sprite](nes#tab-1-3-sprite-layer) de hasta 256×256 píxeles; los rectángulos más grandes simplemente duplican el gráfico del sprite. Aun así, no ofrecen [transformación afín](super-nintendo#that-feature) (salvo el volteo X/Y), ni sombreado ni efectos. Sospecho que los rectángulos se implementaron únicamente para facilitar el desarrollo de juegos en 2D.

En el caso del triángulo, que es la única primitiva texturizable, la rasterizadora:

1. Toma cada uno de los tres vértices y calcula las aristas, formando un triángulo.
2. Analiza el área del triángulo para determinar qué píxeles del frame buffer ocupa. Solo la porción del polígono que cubre los **puntos de muestreo** se convierte en píxeles.

Los píxeles generados no se escriben directamente en el frame buffer. En su lugar, se pasan a las etapas siguientes del pipeline para un procesamiento adicional, algo que explico en los párrafos siguientes.

#### Shaders {.tab}

![El sombreado Gouraud en acción.](spyro/shaders.png){.tab-float}

Para aplicar efectos de iluminación a triángulos o líneas, la GPU ofrece dos algoritmos:

- **Sombreado plano** (*Flat shading*): cada primitiva tiene un nivel de luz constante.
- **Sombreado Gouraud** (*Gouraud shading*): cada vértice incorpora su propio nivel de luz; el brillo se interpola automáticamente entre puntos.
  - Como cabe imaginar, este tipo ofrece resultados más realistas que el sombreado plano.

La razón de ofrecer esta elección es el rendimiento: el sombreado plano rellena aproximadamente 2,5 veces más polígonos por segundo que el sombreado Gouraud. Por eso es importante optimizar qué polígonos se benefician realmente de una iluminación más realista.

#### Texturas {.tab}

![Texturas aplicadas (_¡Tachán!_).](spyro/result.png){.tab-float}

Las superficies de los triángulos también pueden mezclarse con texturas (mapas de bits 2D) para producir el resultado final.

La GPU realiza un **mapeado inverso de texturas**: recorre cada píxel rasterizado y busca su píxel correspondiente en el mapa de texturas (denominado **texel**). Los texels se calculan interpolando linealmente el mapa de texturas (ubicado en la VRAM) para adaptarlo a la forma del polígono. La rutina de interpolación se llama **Affine Texture Mapping**, y opera únicamente con coordenadas 2D (valores X e Y), descartando la tercera coordenada (Z o 'profundidad') que de otro modo daría cuenta de la perspectiva.

Como los mapas de texturas rara vez coinciden con las dimensiones exactas de los polígonos rasterizados, suele aparecer **aliasing** (resultados de renderizado incorrectos). Esto se manifiesta como distorsiones no deseadas, como texels ausentes o sobredimensionados. Para remediarlo, las GPU más sofisticadas emplean **filtrado de texturas** para suavizar (interpolar) los cambios bruscos de color. La GPU de la PS1 no implementa ningún filtro, por lo que recurre a un algoritmo conocido como **vecino más cercano** (*nearest neighbour*) para corregir las escalas sin suavizar los resultados. Esto es muy rápido y barato, pero también explica por qué los modelos texturizados pueden verse 'pixelados'.

La GPU también admite los siguientes efectos, disponibles para los triángulos:

- **Semitransparencia**: simula la luz que pasa a través de texturas superpuestas.
- **Dithering**: suaviza los cambios bruscos de color respetando la misma paleta.

Cabe señalar que la PS1 destacaba especialmente en estos efectos.

#### Pasos finales {.tabs-close}

Una vez finalizado el proceso, la GPU escribe los píxeles en el área del frame buffer de la VRAM, donde los recoge el codificador de vídeo para mostrarlos en pantalla.

### Diseños

Dejemos a un lado la teoría por un momento. Aquí tienes algunos ejemplos de personajes diseñados específicamente para la era 3D. Obsérvese el uso de triángulos y texturas con el algoritmo de vecino más cercano. Te animo a compararlos con los modelos que aparecen en otros artículos.

![Spyro the Dragon (1998).<br>413 triángulos.](spyro_ps1){.toleft model3d="true" hardcover_latex_width="62%" paperback_latex_width="55%"}

![Crash Bandicoot (1996).<br>732 triángulos.](crash_ps1){.toright model3d="true" hardcover_latex_width="76%" paperback_latex_width="68%"}

### Jugando con la VRAM

Teniendo en cuenta la cantidad de VRAM disponible (*todo un megabyte*), se podría reservar un frame buffer *enorme* de 1024×512 píxeles con colores de 16 bits, o uno *realista* de 960×512 píxeles con 24 bits —lo que permitiría dibujar algunos de los frames más impresionantes vistos en los videojuegos—. Suena bastante impresionante, ¿verdad? Bien, esto plantea algunos problemas:

- Estas dimensiones necesitarían reescalarse para adaptarse a los formatos estándar (como 480i NTSC o 576i PAL), de modo que el codificador de vídeo pueda emitirlas en los televisores domésticos.
- ¿Cómo va a renderizar la GPU algo decente si no queda espacio para otros recursos (como texturas y tablas de colores)?
- La GPU de la PS1 solo puede renderizar frame buffers de hasta 640×480 píxeles y colores de 16 bits.

Bien, usemos entonces un buffer de 640×480 con 16 bits de color, lo que deja 424 KB de VRAM para los materiales. ¿Todo bien hasta ahora? De nuevo, esa resolución puede verse bien en monitores CRT, pero no resulta especialmente apreciable en los televisores de los 90 que todo el mundo tenía en casa. ¿Hay alguna manera de optimizar el frame buffer? Aquí entran los **frame buffers ajustables**.

![Visualización de la VRAM en el depurador NO$PSX. Se aprecia el doble frame buffer junto a las texturas. Estas últimas se traducen mediante una tabla de correspondencia de colores, también almacenada allí.](vram.jpg){.open-float}

En esencia, en lugar de desperdiciar VRAM en resoluciones 'poco apreciadas', la GPU de esta consola permite reducir las dimensiones del frame buffer para ampliar de hecho el espacio disponible para otros recursos. En una demostración de 'Gears Episode 2' [@graphics-halkun], Halkun muestra una configuración que divide el frame buffer de 640×480 en dos de 320×480, empleando una técnica denominada **page flipping** para renderizar varias escenas al mismo tiempo.

El page flipping consiste en alternar la ubicación del frame que se muestra entre dos buffers según se necesite, lo que permite al juego renderizar una escena mientras muestra otra. Así se oculta cualquier efecto de parpadeo y se mejoran los tiempos de carga (algo que el jugador sin duda agradecerá).

{.close-float}

En conjunto, la distribución de Halkun consume apenas 600 KB de VRAM. Los 424 KB restantes pueden utilizarse para almacenar tablas de correspondencia de colores y texturas que, junto con los **2 KB de caché de texturas disponible**, dan lugar a una configuración muy práctica y eficiente.

Por último, también vale la pena mencionar que la VRAM puede mapearse con **múltiples profundidades de color simultáneamente**, lo que significa que los programadores pueden reservar un frame buffer de 16 bpp junto a mapas de bits de 24 bpp (usados habitualmente por los frames de FMV, por ejemplo). Esto ofrece otra vía de optimización del espacio.

### Secretos y limitaciones

Aunque la PS1 contaba con una arquitectura sencilla y adecuada, no estaba exenta de problemas. Sorprendentemente, algunos de ellos se abordaron con soluciones ingeniosas.

#### Texturas distorsionadas {.tabs .active}

![Final Fantasy VIII (1999), de Square Soft. Como es habitual en los juegos de PS1, las texturas tiemblan ligeramente cuando se mueven.](ffviii){.tab-float video="true"}

Las rutinas empleadas para gestionar la geometría y aplicar texturas presentan ciertas imprecisiones.

En primer lugar, **la rasterizadora solo trabaja con unidades de píxel enteras**: aunque las coordenadas de los vértices se expresan como enteros, las aristas calculadas de los triángulos pueden ocupar solo una fracción de un píxel. Sin embargo, la rasterizadora solo dibujará el píxel si el área del triángulo cubre el punto de muestreo del píxel, y **no lleva un registro de la fracción ocupada** [@graphics-raster]. Esto genera algunos problemas:

- Los bordes exteriores de los modelos darán saltos bruscos al desplazarse levemente.
- Los triángulos dentro de una malla (que comparten los mismos vértices y aristas) a menudo 'competirán' por dibujar sobre los mismos píxeles. Teniendo en cuenta la tabla de ordenación, la GPU renderiza según un criterio de 'el último en llegar, el primero en servirse', lo que hará que las intersecciones entre triángulos parpadeen o se solapen al moverse levemente.

Esto se suele resolver implementando **resolución sub-píxel**, que permite a la rasterizadora rastrear las fracciones de píxel ocupadas por cada área triangular [@graphics-tomas]. En consecuencia, se pueden aplicar técnicas de antialiasing para suavizar los bordes dentados o los cambios bruscos de color.

Por otro lado, la tabla de ordenación **hace recaer sobre el desarrollador o el programa la responsabilidad de mostrar la geometría en el orden correcto**. En algunos casos, los cálculos subyacentes recurren a aproximaciones excesivas para ganar rendimiento. Esto puede dar lugar a parpadeos o **superficies ocluidas** que deberían haberse mostrado.

Además, utilizar un frame buffer de baja resolución puede amplificar todos estos problemas de aliasing.

Por último, como ya sabemos, las transformaciones afines **carecen de toda noción de profundidad**. Esto puede confundir la percepción del usuario, especialmente cuando la cámara se sitúa cerca del modelo y perpendicular a la línea de visión del espectador [@graphics-retrocomp]. Esta anomalía se conoce como **texture warping**. Por ello, algunos juegos recurrieron a la **teselación** (dividir polígonos grandes en otros más pequeños) para reducir la distorsión, mientras que otros optaron por sustituir las texturas por **colores sólidos**. En general, una GPU resuelve este problema implementando **corrección de perspectiva**, que interpola las texturas utilizando el valor de profundidad.

#### Afirmaciones contradictorias {.tab}

Si consultas otros canales técnicos o foros, encontrarás explicaciones alternativas sobre los efectos de vibración, distorsión y deformación de la PS1. Aunque algunas coinciden con mis explicaciones anteriores, otras difieren. Por eso me gustaría ofrecer mi punto de vista sobre por qué las siguientes afirmaciones son **incorrectas**:

> Los modelos y las texturas vibran por falta de FPU

Una FPU no determina si un sistema puede operar con números fraccionarios. La PS1, como cualquier otro ordenador sin FPU de hardware, puede realizar aritmética de punto fijo. Además, los números en punto flotante también pueden calcularse (aunque más lentamente) mediante software. En esencia, **las FPU son aceleradores**: no las confundas con el concepto de números decimales ni con una ALU (la parte crítica de una CPU responsable de las operaciones aritméticas).

> Los modelos y las texturas vibran por el sistema de coordenadas enteras de la GPU

Emplear coordenadas enteras es un enfoque habitual para abaratar los cálculos en la GPU. Lo que convierte esto en un problema visible es la falta de resolución sub-píxel, ya que **el antialiasing puede aplicarse para suavizar los cambios bruscos de color**, siempre que la rasterizadora rastree la fracción de píxel ocupada por el triángulo.

> Las texturas se deforman por falta de mip-mapping

Las GPU que implementan mapeado inverso de texturas, como esta, están sujetas a un error de medición conocido como 'submuestreo' (un píxel se mapea sobre múltiples texels). Esto produce aliasing (resultados incorrectos). El comportamiento es apreciable al renderizar geometría alejada de la cámara. Para remediarlo, los mapeadores de texturas modernos aplican 'filtrado trilineal', que suaviza los texels usando la misma textura almacenada a distintas escalas (mipmaps) e interpola entre ellas. En resumen, **el mip-mapping es un método para reducir el aliasing**. Es el **Affine Texture Mapping** (con su falta de corrección de perspectiva) el que interpola las texturas en superficies de tres puntos sin tener en cuenta la profundidad, produciendo el efecto de 'deformación'.

> Los modelos y las texturas parpadean por falta de Z-buffer

Una GPU equipada con [Z-buffer](nintendo-64#modern-visible-surface-determination) resuelve el problema de la [determinación de superficies visibles](sega-saturn#an-introduction-to-the-visibility-problem) a nivel de hardware. En cambio, la PS1 depende de una tabla de ordenación, lo que significa que los desarrolladores son los responsables de determinar qué geometría está delante de otra. En resumen, **el parpadeo de los triángulos es consecuencia de las rutinas de ordenación (software)**.

#### Gráficos prerenderizados {.tab}

![Final Fantasy VII (1997), de Square Soft, presenta numerosas escenas con fondos prerenderizados. La interacción y el movimiento de los modelos 3D son clave para engañar la percepción del espectador.](prerendered.png){.tab-float}

Destacamos ahora una característica *positiva*, para variar...

Cuando un juego aspiraba a escenarios más realistas de lo que la GPU podía ofrecer en tiempo real, una opción disponible era apilar dos triángulos y usar el Motion Decoder para reproducir **cinemáticas prerenderizadas** sobre ellos. Las secuencias FMV requerían mucho espacio de almacenamiento, pero por suerte el CD-ROM estaba bien preparado para ello.

Algunos títulos se valieron de esta técnica para componer fondos y, sinceramente, el resultado era bastante convincente en un televisor CRT. Por supuesto, los emuladores modernos con capacidades de escalado lo delatan enseguida.

### Salida de vídeo {.tabs-close}

![Cuatro revisiones diferentes de la PlayStation 1 apiladas [@photography-bruarn]; obsérvese la reducción de puertos a lo largo de los años. De abajo a arriba: SCPH-1000 (1994), SCPH-3000 (1995), SCPH-5501 (1996) y SCPH-9001 (1999).](models_back.png){.no-borders}

La primera revisión de esta consola ofrece una sorprendentemente amplia variedad de señales de vídeo, accesibles a través de los siguientes puertos:

- **RFU DC**: diseñado para conectar un modulador RF.
- **RCA**: salida de vídeo compuesto y audio estéreo.
- **S-Video**: proporciona una señal combinada de Luma + Sync y una señal Chroma independiente.
- **AV Multi Out**: ofrece todas las señales anteriores, salvo la RFU. Además transmite señal RGB y una línea de 5+ voltios.

En revisiones posteriores, la mayoría de estos puertos fueron eliminados, dejando únicamente el 'AV Multi Out' en los modelos tardíos.

## Audio

De esto se encarga la característica **Sound Processing Unit** (SPU) de Sony. Este chip admite la ingente cantidad de **24 canales** de **muestras ADPCM de 16 bits** (una versión más eficiente del conocido muestreo PCM) con una frecuencia de muestreo de **44,1 kHz** (calidad de CD de audio).

Este chip también ofrece las siguientes capacidades:

- **Modulación de tono**: los juegos pueden alterar automáticamente el tono de sus muestras en lugar de almacenar muestras adicionales. Esto resulta útil para la secuenciación de música.
- **Modulación de frecuencia**: los canales pueden asignarse para modificar la frecuencia de otros. Esta es comparable a la [síntesis de Modulación de Frecuencia](mega-drive-genesis#audio).
- **Envolvente ADSR**: conjunto de parámetros disponibles para la modulación de amplitud.
- **Bucle**: indica al sistema que reproduzca un fragmento de audio repetidamente.
- **Reverberación digital**: simula la reproducción de la muestra en un entorno acústico específico para mayor inmersión.

Se proporcionan **512 KB de DRAM** (denominada 'Sound RAM') como buffer de audio. Esta memoria es accesible tanto por la CPU (vía DMA) como por el controlador de CD. No obstante, los juegos solo disponen de 508 KB para almacenar muestras; el resto queda reservado por la SPU para procesar música de CD de audio. Esta cantidad se reduce aún más cuando la reverberación está activada.

El controlador de CD también puede enviar muestras directamente al mezclador de audio sin pasar por el buffer de audio ni requerir la intervención de la CPU. Las muestras también pueden comprimirse mediante la codificación 'XA', que la SPU puede decodificar en tiempo real.

### La era del streaming

[Al igual que en la Saturn](sega-saturn#the-opportunity), los juegos ya no dependen de la secuenciación de música ni de formas de onda predefinidas y, gracias al amplio almacenamiento del formato CD-ROM, los desarrolladores pueden guardar muestras totalmente producidas y transmitirlas directamente al chip de audio.

## E/S

Originalmente estaban disponibles dos puertos de E/S —**serie** y **paralelo**— para complementos. Sin embargo, estos se eliminaron en revisiones posteriores de la consola por su escasa adopción y por el temor a que pudieran utilizarse para eludir el sistema de protección anticopia.

### Subsistema de CD

El bloque responsable de controlar la unidad de CD es un área fascinante: puedes imaginarlo como un ordenador independiente que vive dentro de la PlayStation.

![Arquitectura del subsistema de CD.](_diagrams/cd.png){.open-float}

Este subsistema consta de:

- Un **DSP**: controla el motor y el láser, y procesa la señal RF procedente del láser.
- Una **Sub-CPU**: un paquete de CPU compuesto por un microcontrolador **Motorola 68HC05**, **512 B de RAM** y **16 KB de ROM** [@io-cdrom]. En pocas palabras, la Sub-CPU ejecuta un programa local almacenado en ROM y controla el DSP. Este programa implementa medidas de protección anticopia que se aplican independientemente de lo que 'quiera' la CPU principal.
- Un **controlador de CD**: actúa como intermediario entre el subsistema de CD y el resto de la consola, recibiendo comandos de la CPU principal (mediante FIFO) y lanzando interrupciones tras determinados eventos. Como controlador, el chip se comunica con la Sub-CPU y recibe datos del CD desde el DSP.
  - Además, el controlador integra una unidad DMA conectada a la SPU, lo que le permite transmitir audio directamente.
- **32 KB de SRAM** conectados al controlador: se utilizan presumiblemente como buffer para los datos leídos del disco.

{.close-float}

El subsistema recuerda en cierto modo a la típica unidad de CD que todos tenían en casa, si bien con ajustes particulares que Sony implementó en el programa de la Sub-CPU para realizar comprobaciones antipiratería.

### Puertos frontales

La consola cuenta con cuatro conectores frontales: dos para mandos de juego y dos para Memory Cards (utilizadas para almacenar las partidas guardadas). Los cuatro conectores son eléctricamente idénticos [@io-manual], por lo que la dirección de cada uno está fijada en el hardware. Además, Sony modificó la forma física de los puertos para evitar accidentes.

![Puertos frontales de la PlayStation [@photography-amos].](front_ports.png)

La comunicación con estos dispositivos se realiza a través de una interfaz serie. Los comandos enviados por la consola se envían a uno de los dos grupos (bien 'mem. card 0' y 'controller 0', bien 'mem. card 1' y 'controller 1'). Los dos accesorios responden con sus identificadores únicos, lo que permite a la consola centrarse en un tipo de dispositivo concreto (Memory Card o mando) a partir de ese momento.

## Sistema operativo

El sistema incluye una **ROM de 512 KB** que almacena una 'BIOS'. Este programa realiza numerosas funciones, entre ellas gestionar el proceso de arranque, mostrar el entorno de usuario (shell) y exponer una colección de rutinas de E/S [@operating_system-bios].

### BIOS/Kernel

La BIOS es una dependencia crítica para los juegos. No solo los inicia desde la unidad de CD, sino que también actúa como intermediaria para interactuar con el hardware de la consola. Esta metodología recuerda a la implementación de IBM para su IBM PC BIOS, que animaba a los desarrolladores a usar una tabla de interrupciones estándar (con rutinas de E/S) en lugar de depender de [puertos de E/S](master-system#accessing-the-rest-of-the-components) específicos de la plataforma.

Dicho esto, la BIOS de la PS1 expone rutinas como:

- Comandos para la unidad de CD-ROM.
- Operaciones de sistema de archivos para el CD-ROM y la Memory Card.
- Multithreading.
- Funciones estándar de C (por ejemplo, manipulación de cadenas y operaciones de memoria).

Dado que el acceso a la ROM de la BIOS es muy lento (al estar conectada mediante un bus de datos de 8 bits), las APIs se empaquetan en forma de **Kernel** y se copian en la RAM principal durante el arranque. Así, 64 KB de RAM principal quedan reservados para el Kernel, también denominado **PlayStation OS**.

### Proceso de arranque

El vector de reset de la CPU se encuentra en `0xBFC00000`, que apunta a la ROM de la BIOS.

![La famosa pantalla de presentación al encender la consola.](bios/splash.png){.tabs-nested .active title="Presentación"}

![El logotipo de PlayStation aparece al insertar un juego original.](bios/valid.png){.tab-nested title="Logotipo"}

![El entorno de usuario se muestra si no hay ningún disco insertado.](bios/shell.png){.tabs-nested-last title="Entorno"}

Al igual que el [proceso de arranque de la Saturn](sega-saturn#games), al encender la PS1 esta:

1. Busca la ROM de la BIOS y ejecuta rutinas para inicializar el hardware.
2. Carga el PlayStation OS.
2. Muestra la pantalla de presentación.
3. Si hay un CD insertado, el controlador de CD-ROM comprueba si es original:
    - **Si lo es**: el controlador permite leer su contenido. La CPU busca entonces un archivo llamado `SYSTEM.CNF` (que especifica la ubicación del ejecutable y algunos metadatos) y continúa la ejecución desde ahí.
    - **Si no lo es** → la CPU muestra un mensaje de error.
4. Si no hay ningún CD insertado, la CPU muestra el **entorno de usuario** (shell).
5. El control pasa al usuario.

El entorno de usuario es una interfaz gráfica sencilla que permite copiar o eliminar partidas guardadas de la Memory Card, o reproducir un CD de audio.

## Juegos

Al igual que la [Sega Saturn](sega-saturn) y otras consolas que realizaron el salto al formato CD, los juegos de PS1 se benefician de una nueva serie de recursos: gran capacidad de almacenamiento (620 MB en el caso de la PS1), una calidad de audio excepcional y una velocidad de lectura 'no tan lenta' gracias a su unidad 2x.

![Un juego típico de PS1, mostrado con el disco al revés. La cara inferior de los CD de PS1 presenta un recubrimiento negro distintivo que en su día se creyó que impedía la piratería. Aunque técnicamente incorrecto —y lo explico con más detalle en la sección 'Antipiratería'—, creo que sí sirve como indicador visual de un disco original.](parappa.webp)

Si tienes curiosidad por saber cómo funcionan los discos compactos (CD), he escrito una breve explicación en el [artículo de la Sega Saturn](sega-saturn#the-compact-disc-cd).

Sea como fuere, las revisiones de la PS1 comercializadas hasta 1997 (tres años después del debut de la consola) eran conocidas por incluir un láser de la unidad de CD defectuoso [@games-revisions], lo que provocaba frecuentes 'saltos' en los FMV y los CD de audio. Los modelos posteriores corrigieron la unidad láser y mejoraron la carcasa, paliando el problema.

### Ecosistema de desarrollo

Sony distribuyó un SDK a los estudios de desarrollo que incluía un compilador de **C** y librerías. Estas últimas estaban vinculadas a las rutinas de la BIOS para el acceso al hardware. Cabe mencionar, de pasada, que este nivel de abstracción facilitó posteriormente la emulación de la PS1 en una amplia variedad de plataformas, tanto [*oficiales*](playstation-2#inherited-compatibility) como *no oficiales*.

Junto al SDK, Sony también proporcionó hardware especializado como la **DTL-H2000**: una tarjeta ISA de doble ranura que contenía las entrañas y la E/S de la PS1 [@games-shadow], además de circuitería adicional para la depuración. La placa podía acceder al disco duro del ordenador anfitrión y ejecutar software de PS1 sin restricciones. El software incluido requería un PC con Windows 3.1 o 95 instalado.

![Una configuración típica de Net Yaroze, parte de la colección de Christopher Rivett [@photography-crivett].](yaroze.webp)

Dos años después del lanzamiento de la PS1, Sony comercializó un kit de desarrollo para aficionados llamado **Net Yaroze**. Además del kit de herramientas y los manuales, incluía una consola PS1 de color negro. Esta última incorporaba un sistema de protección anticopia modificado que solo permitía arrancar software Net Yaroze. El software se transfería desde un ordenador externo mediante el puerto serie y se cargaba en la memoria principal de la PS1. La limitación más significativa, comparada con los kits de desarrollo de nivel profesional, era que el Net Yaroze no proporcionaba acceso a la unidad de CD, lo que obligaba al software homebrew a residir completamente en la RAM principal (con el consiguiente overhead).

## Antipiratería y bloqueo regional

Como ya sabrás, para leer datos de un CD se utiliza un haz láser que detecta los **pits** y **lands** grabados en la pista del disco.

Los discos convencionales no son perfectamente planos y a menudo presentan pequeñas variaciones en sus pistas. Estos *defectos* son completamente imperceptibles durante la lectura, ya que el láser puede calibrarse automáticamente.

En esto basó Sony su protección anticopia: la Sub-CPU solo lee discos cuya pista concreta —la Tabla de Contenidos (TOC)— está grabada con una frecuencia definida, conocida informalmente como **Wobble Groove**. Esta frecuencia se introduce durante el proceso de masterización y no puede replicarse con grabadoras de CD convencionales. La TOC reside en la sección interior del CD (denominada área 'Lead-In') e indica al láser cómo navegar por el disco [@copy_protection-kalymos]. Además, se repite varias veces como mecanismo de tolerancia a fallos.

Dentro de la tabla de contenidos del juego, se incluye una de las siguientes cadenas de texto:

- **SCEA**, de 'Sony Computer Entertainment of America'.
- **SCEE**, de 'Sony Computer Entertainment of Europe'.
- **SCEI**, de 'Sony Computer Entertainment of Japan'.

Como puedes imaginar, esta técnica también se aplicó para el **bloqueo regional**.

### La brecha

Sin embargo, esta comprobación solo se ejecuta una vez al inicio, lo que significa que intercambiar manualmente el disco justo después de la verificación permite eludir esta protección, aunque con el riesgo de dañar la unidad.

Más adelante, algunos juegos tomaron cartas en el asunto y reinicializaban la unidad durante el gameplay, obligando a realizar de nuevo la comprobación. Esto se hacía para impedir que los usuarios realizaran el 'truco del cambio de disco'.

Como alternativa, podían soldarse a la consola pequeñas placas comerciales programadas para simular la señal Wobble Groove. Conocidas como **Modchips**, estos componentes —aunque legalmente controvertidos— gozaron de una popularidad enorme.

### Las represalias

La amplia disponibilidad de modchips, grabadoras de CD y emuladores fue percibida por las distribuidoras como una amenaza importante. En respuesta, los juegos posteriores incorporaron sus propias medidas de protección (principalmente checksums) para disuadir cualquier uso o modificación no autorizada.

Uno de estos métodos que me contaron consistía en reinicializar deliberadamente la unidad de CD y hacerla leer sectores concretos que fallarían la comprobación del Wobble Groove. Si aun así la unidad se desbloqueaba, el juego (que seguía en la RAM) mostraría *felizmente* su material antipiratería. Cabe destacar que este enfoque también podía afectar a consolas modificadas con discos originales.

Más adelante, Sony proporcionó una librería llamada **Libcrypt**, que reforzó la protección anticopia mediante dos enfoques [@copy_protection-libcrypt]:

- Desde el **lado del hardware**, los checksums de sectores concretos se almacenaban en los subcanales del disco.
  - Los subcanales del CD-ROM almacenan tradicionalmente metadatos para guiar al lector. No son accesibles por el usuario y las grabadoras convencionales rara vez permiten escribir en ellos manualmente.
- Desde el **lado del software**, se incrustaban rutinas en distintos puntos del juego. Estas recuperan los valores de checksum y los mezclan con otros para realizar verificaciones, en un intento de contrarrestar tanto los emuladores como los discos piratas.

## Eso es todo, amigos
