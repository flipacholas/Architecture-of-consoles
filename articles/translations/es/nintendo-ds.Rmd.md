---
short_title: Arquitectura de la Nintendo DS
long_title: Arquitectura de la Nintendo DS
name: Nintendo DS
date: 2020-08-11
subtitle: Nuevas formas de interacción
release_date: 2004-11-21
cover: "nintendods"
javascript: ['audioswitcher', 'threejs']
generation: 7
top_tabs:
  Models:
    - title: "Original"
      caption: "La Nintendo DS original (edición azul).<br>Lanzada el 21/11/2004 en América, el 02/12/2004 en Japón y el 11/03/2005 en Europa."
      active: true
      file: original
    - title: "Lite"
      caption: "La Nintendo DS Lite (edición negra).<br>Lanzada el 02/03/2006 en Japón, el 11/06/2006 en América y el 23/06/2006 en Europa."
      file: lite
  Motherboard:
    caption: "Primera revisión."
    latex_height: 95
    marked_latex_height: 95
  Diagram:
    caption: "Si te cuesta seguir los componentes: la parte superior es de acceso exclusivo del ARM9, la inferior es exclusiva del ARM7 y la central es compartida."
    latex_height: 95

# Historical
aliases: [/projects/consoles/nintendo-ds/]
---

## Una breve introducción

Esta consola es una respuesta ingeniosa a muchas necesidades que no era posible satisfacer en el ecosistema portátil. Habrá algo de innovación y algunas concesiones, pero esta combinación puede allanar el camino a contenido nuevo y creativo.

## {.supporting-imagery}

## CPU

Al igual que la [anterior consola portátil de Nintendo](game-boy-advance), el sistema gira en torno a un gran chip llamado **CPU NTR**. 'NTR' es la abreviatura de **Nitro**, el nombre en clave de la Nintendo DS original. Este Sistema en Chip (SoC, del inglés *System-on-Chip*) es también la continuación de una próspera [alianza Nintendo-ARM](game-boy-advance#the-nintendo-partnership), que tuvo sus inicios en la Game Boy Advance.

Antes de examinar las nuevas CPU, veamos cómo evolucionó ARM a finales de los años 90, pues ello influyó en la tecnología que acabaría integrando la Nintendo DS.

### Los nuevos territorios de ARM

A mediados de los años 90, ARM recibía un creciente flujo de clientes del mercado móvil (antes de que los teléfonos se volvieran 'inteligentes'). Sin embargo, le costaba satisfacer a un sector concreto: la **informática de alto rendimiento**. El ARM7 era bien acogido por muchos dispositivos móviles, pero no llegaba a convencer ni a Apple (con su línea de PDA 'Newton') ni a Acorn (con su línea RiscPC), que comercializaban software que habría sacado partido de una CPU más rápida. Más allá de la ausencia de una solución de 64 bits (algo que [MIPS ya comercializaba](nintendo-64#cpu)), ARM tampoco era capaz de producir una CPU que funcionase a más de 40 MHz. En definitiva, los cuellos de botella empezaban a acumularse.

![La 'Digital Personal Workstation' modelo 433au (1997) de DEC, equipada con una CPU Alpha 21164A.](dec_work.webp){.open-float latex_width="80%"}

No obstante, durante la comercialización de la línea ARM7, ARM ya había comenzado a trabajar en un sucesor llamado **ARM8**, el primer intento de entrar en el mercado de alto rendimiento. De manera coincidente, **Digital Equipment Corporation (DEC)**, la empresa estadounidense históricamente reconocida por su línea de máquinas PDP y VAX (los llamados 'miniordenadores') y por el sofisticado sistema operativo VMS, se enfrentaba al problema contrario: le costaba desarrollar una CPU de bajo consumo basada en sus soluciones de alto rendimiento.

Resultó que [el modelo de licencias de ARM](game-boy-advance#tab-1-2-a-new-cpu-venture) se convirtió en una oportunidad para DEC, que optó por desarrollar una nueva CPU tomando prestados elementos de ARM (su set de instrucciones y microarquitectura).

{.close-float}

Al final, DEC tomó el diseño de datapath de su procesador RISC **Alpha** y, con la ayuda de ARM, lo fusionó con la microarquitectura de ARM [@cpu-jaggar]. Esta colaboración dio lugar a la CPU **StrongARM**, lanzada en 1996 y orientada al sector del alto rendimiento.

![Una CPU StrongARM a 233 MHz, parte de la tarjeta de mejora de CPU ofrecida para el Acorn RiscPC.](strongarm.jpg){latex_width="90%"}

El StrongARM era una nueva CPU basada en ARM con las siguientes características [@cpu-furber]:

- El **set de instrucciones ARMv4**. Si quieres saber más al respecto, ya lo [analicé anteriormente](game-boy-advance#commanding-the-cpu) en el artículo de la Game Boy Advance.
- Hasta **233 MHz** de velocidad de reloj, aproximadamente un 583 % más rápido que el chip ARM7 más potente.
- Un **pipeline de 5 etapas**, un avance respecto al [diseño original de 3 etapas](game-boy-advance#tab-2-1-the-core).
- Un **nuevo sistema de caché** que implementa una **arquitectura Harvard**, con **16 KB** asignados a instrucciones y otros **16 KB** a datos.
  - Este diseño ayudó a paliar los cuellos de botella de memoria (algo de lo que adolecía el modelo de von Neumann/Princeton).

Cabe mencionar que este chip seguía funcionando con una alimentación de **3,3 V**, como los chips ARM anteriores. De hecho, el StrongARM requiere tan solo 2 voltios y disipa únicamente 1 vatio [@cpu-furber]. En cambio, el chip Intel Pentium a 200 MHz requiere 3,5 V y disipa hasta 15,5 W [@cpu-pentium].

Como cabía esperar, la repercusión en Acorn y Apple fue tal que ambas empresas lanzaron inmediatamente mejoras de CPU y nuevos modelos Newton, respectivamente, usando la invención de DEC.

Ese mismo año, ARM también lanzó su prometida CPU basada en ARM8, el ARM810. Era comparativamente más lenta y no ofrecía ventajas prácticas sobre el StrongARM. En otras palabras, llegó tarde al mercado y sin méritos suficientes para despertar interés comercial. En consecuencia, ARM centró su atención en mejorar la línea ARM7 para el mercado móvil. Sin embargo, el potencial de la CPU de DEC era tan disruptivo que ARM Holdings absorbió el diseño del StrongARM [@cpu-jaggar] para producir su siguiente línea de CPU: la **ARM9** (que es la que alberga la Nintendo DS).

#### Puntos de inflexión

Gracias al StrongARM, ARM también consolidó su posición en el mercado de dispositivos portátiles, desplazando por completo a [MIPS](nintendo-64#cpu) y [SuperH](dreamcast#cpu) como alternativas viables. A partir de entonces, ARM estaba en camino de convertirse en la arquitectura más adoptada para dispositivos móviles [@cpu-android_abi].

Por desgracia para DEC, esta CPU sería su último gran logro antes de ser adquirida por Compaq en 1998. El destino del StrongARM quedaría entonces en manos de Intel, que continuó su desarrollo bajo la nueva línea 'Intel XScale'... hasta que liquidó la división para centrarse en [CPU x86 de 'bajo consumo'](xbox#p6-and-the-end-of-pentium-numbers) (es decir, el Intel Atom) [@cpu-xscale]. Quince años después, Intel no solo perdió su oportunidad en el mercado móvil, sino que ahora se encuentra compitiendo cara a cara con ARM en el mercado de escritorio.

### El SoC debut de Nintendo

El silicio de la Nintendo DS es una interesante confluencia entre tecnología asequible y pionera. Además de los avances ya mencionados, la CPU NTR (el SoC) sigue una **arquitectura multiprocesador** que alberga dos CPU ARM distintas: la **ARM7TDMI** y la **ARM946E-S**. Este diseño se realizó antes de que ARM Holdings lanzase oficialmente [soluciones multiprocesador](nintendo-3ds#cpu). Por tanto, su funcionamiento puede considerarse un tanto heterodoxo teniendo en cuenta la tecnología actual.

![El chip CPU NTR.](cpu_ntr.jpg){latex_width="90%"}

Aunque no es el primer sistema paralelo que analizamos en [esta serie](consoles), su diseño difiere notablemente del resto. No estamos hablando de la configuración maestro-esclavo *experimental* que debutó en el [Saturn](sega-saturn), ni del enfoque de *coprocesador* del [PS1](playstation) o la [N64](nintendo-64). La Nintendo DS incluye dos ordenadores en gran medida independientes que realizan operaciones exclusivas, cada uno con su propio bus dedicado. Esta metodología de diseño se conoce como **multiprocesamiento asimétrico**, y la resultante dependencia entre CPU condiciona el rendimiento global de la consola.

Veamos ahora las dos CPU.

#### ARM7TDMI {.tabs .active}

![Estructura y componentes de la parte ARM7.](_diagrams/cpu/arm7_core.png){.tab-float}

Empezando por la más conocida de las dos, la **ARM7TDMI** es la misma CPU de la [Game Boy Advance](game-boy-advance#cpu), aunque ahora funciona a **~34 MHz**, el doble de su velocidad original. Conserva todas sus características originales (especialmente [Thumb](game-boy-advance#tab-2-3-squeezing-performance)).

En cuanto a los cambios: como los ingenieros de Nintendo colocaron el ARM7 junto a la mayoría de los puertos de E/S, esta CPU se encarga de arbitrar y gestionar las operaciones de E/S. De hecho, ningún otro procesador puede conectarse directamente a la E/S. Como puedes ver, este no es el procesador 'principal' que se encarga del sistema, sino el 'subprocesador' que alivia la carga de la CPU principal transfiriendo datos entre los distintos componentes.

#### ARM946E-S {.tab}

![Estructura y componentes de la parte ARM9.](_diagrams/cpu/arm9_core.png){.tab-float}

Esta es la CPU 'principal' de la Nintendo DS: la **ARM946E-S**. Funciona a **~67 MHz**, lo que no llega exactamente a la 'velocidad StrongARM'. Aun así, como parte de la **serie ARM9**, este núcleo no solo hereda todas las características de la **ARM7TDMI** y el **StrongARM**, sino que también incluye algunos añadidos que pueden resultar interesantes [@cpu-arm9reference]:

- La **ISA ARMv5TE**, una extensión de la [ISA ARMv4 anterior](game-boy-advance#commanding-the-cpu) y [Thumb](game-boy-advance#tab-2-3-squeezing-performance), con más instrucciones y un multiplicador más rápido.
  - Si te fijas en el nombre del núcleo, la letra 'E' significa **Enhanced DSP**, lo que indica que muchas de estas nuevas instrucciones están relacionadas con aplicaciones de procesamiento de señal.
  - El Thumb extendido se denomina a veces **Thumb v2**. Añade `BLX` y `BKPT`, que facilitan el cambio entre los modos ARM y Thumb, y la depuración, respectivamente.
- Un **pipeline de 5 etapas**, al igual que las familias StrongARM y ARM8.
- **12 KB de caché L1**: a diferencia de la ARM7TDMI, el nuevo núcleo incorpora caché y, al igual que el StrongARM basado en Harvard, asigna **8 KB** a instrucciones y **4 KB** a datos.
  - La caché emplea un mecanismo de **write-buffer**, que permite actualizar la RAM de forma asíncrona para que la CPU pueda trabajar en otras tareas.
- **48 KB de Tightly-Coupled Memory (TCM)**: similar a la [memoria Scratchpad](playstation#cpu), aunque aquí se discrimina entre instrucciones (32 KB) y datos (16 KB).
- Un **coprocesador CP15** integrado. Actúa como una **Memory Protection Unit (MPU)**, determinando qué rangos de memoria pueden accederse, cachearse o escribirse.

Nintendo también añadió los siguientes componentes en torno al núcleo:

- Una **unidad de división y raíz cuadrada** para acelerar estas operaciones, ya que el ARM9 por sí solo no es capaz de realizar este tipo de aritmética.
- Un **controlador de Acceso Directo a Memoria (DMA, del inglés *Direct Memory Access*)**: acelera las transferencias de memoria sin depender de la CPU. Combinado con la caché, la CPU y el DMA pueden trabajar de forma potencialmente simultánea.
  - La caché y el DMA pueden ofrecer un gran rendimiento, pero también introducen nuevos problemas, como la **integridad y coherencia de datos**. Los programadores deben, por tanto, mantener la consistencia de memoria manualmente, por ejemplo, [vaciando el write buffer](playstation-2#preventing-past-mishaps) antes de activar el DMA.

Supongo que con un hardware así, es fácil imaginar el *verdadero* motivo por el que los niños adoraban esta consola, ¿no?

### Interconexión {.tabs-close}

Hasta ahora he descrito cómo operan las dos CPU de forma individual. Pero el sistema solo funciona como un todo si ambas cooperan constantemente. Para lograrlo, ambas CPU se 'hablan' directamente mediante una **unidad FIFO** dedicada [@cpu-double]. Este bloque contiene dos colas de 64 bytes (con hasta 16 elementos cada una) para **comunicación bidireccional**.

![Representación de la unidad FIFO.](_diagrams/cpu/fifo.png)

La unidad FIFO funciona de la siguiente manera: la CPU 'emisora' (la que necesita transmitir un mensaje) coloca un bloque de datos de 32 bits en la cola, y la CPU que actúa como 'receptora' puede entonces extraer ese bloque de la cola y realizar con él las operaciones necesarias.

Cada vez que se escribe un valor en la cola, cualquiera de las dos CPU puede leerlo manualmente (**sondeo**). Sin embargo, esto implica comprobar repetidamente si hay nuevos valores, lo que puede resultar costoso. Como alternativa, puede activarse una **unidad de interrupción** para notificar al receptor cada vez que haya un nuevo valor en la cola.

### Memoria principal

Al igual que su predecesora, la RAM está distribuida en distintas ubicaciones, lo que permite al sistema priorizar la colocación de los datos según la velocidad de acceso. En resumen, la memoria de uso general disponible es la siguiente [@cpu-mem_map]:

![La arquitectura de memoria de la Nintendo DS.](_diagrams/cpu/ram.png){.open-float}

- **32 KB de Work RAM (WRAM)** en un bus de **32 bits**: para almacenar datos rápidos compartidos entre el ARM7 y el ARM9.
  - Ten en cuenta que solo una CPU puede acceder a ella en cada momento.
- **64 KB de WRAM** en un bus de **32 bits**: también para datos rápidos, pero accesible únicamente por el ARM7.
- **4 MB de Pseudo-Static RAM (PSRAM)** en un bus de **16 bits**: un tipo más lento, disponible para ambas CPU y arbitrado por una unidad de interfaz de memoria.
  - La PSRAM es una variante de la Dynamic RAM (DRAM) que, por el contrario, realiza sus ciclos de refresco internamente, comportándose así como la SRAM (la alternativa más rápida pero más costosa a la DRAM). Este diseño me recuerda a la [1T‑SRAM](gamecube#clever-memory-system).

{.close-float}

### Compatibilidad con versiones anteriores

Aunque la arquitectura de la Nintendo DS supone una ruptura radical con su predecesora, esta consola logró conservar los elementos críticos que permiten la compatibilidad nativa con los juegos de Game Boy Advance.

Para que la DS se transforme en una GBA, la consola incluye un conjunto de rutinas de software que la sitúan en el **AGB Compatibility Mode**. Al activarse, estas rutinas detienen el ARM9, deshabilitan la mayor parte del hardware nuevo, redirigen los buses, ponen el ARM7 al mando y reducen su velocidad de reloj a 16,78 MHz. Por último, el ARM7 ejecuta la [BIOS AGB](game-boy-advance#operating-system) original, que arranca el cartucho GamePak (igual que una Game Boy Advance original).

El modo AGB también presenta algunas características ausentes en la consola original, como mostrar el juego con márgenes negros (ya que la nueva resolución de pantalla es mayor). Además, dado que la DS tiene dos pantallas, los usuarios pueden elegir en cuál se mostrará el juego de GBA.

Por último, una vez en modo AGB, **no hay vuelta atrás**. Hay que reiniciar la consola para reactivar el nuevo hardware.

### Potencia desaprovechada

Con tantos componentes sofisticados integrados en un único chip asequible, no es ningún misterio que surgieran algunos problemas derivados de la forma en que se vieron obligados a trabajar juntos.

Empecemos por el ARM9: esta CPU funciona al doble de velocidad que el ARM7, pero la mayor parte (si no toda) la E/S corre a cargo del ARM7. Por ello, el ARM9 tiende a sufrir bloqueos excesivos mientras espera que el ARM7 responda a las solicitudes de E/S.

Además, el bus de la PSRAM tiene solo **16 bits de ancho** (siguiendo aún el modelo de la [EWRAM de la Game Boy Advance](game-boy-advance#memory-locations)) y funciona a la mitad de la velocidad de reloj del ARM9. En consecuencia, cada vez que cualquiera de las CPU necesita leer una palabra de 32 bits de la memoria, la interfaz **bloquea la CPU** e introduce hasta tres 'ciclos de espera' mientras se reconstruye la palabra completa.

El grado de bloqueo varía considerablemente en función del tipo de transferencia (lectura de instrucciones o datos) y del ancho de transferencia (16 o 32 bits). El peor caso se da durante la lectura no secuencial de datos de 32 bits, donde el ARM9 emplea cinco ciclos por transferencia. Esto mejora cuando los datos son secuenciales: los tiempos de transferencia se reducen a un solo ciclo. Sin embargo, el ARM9 no admite lecturas secuenciales de opcodes, por lo que las lecturas de instrucciones no se benefician de esta mejora. En otras palabras, las penalizaciones son las mismas independientemente de si las instrucciones son adyacentes.

En definitiva, esto significa que en el peor caso, los imponentes 66 MHz del ARM9 se reducen en la práctica a tan solo ~8 MHz. Es decir, si el programa hace un uso pésimo de la caché y la TCM. En situaciones así, me pregunto si Nintendo podría haber mitigado el problema simplemente aumentando la cantidad de RAM interna rápida. También me pregunto si esto no es más que deuda técnica arrastrada en aras de la compatibilidad con versiones anteriores.

En cualquier caso, para un informe detallado, recomiendo consultar los tiempos documentados por Martin Korth [@cpu-mem_timings].

## Gráficos

Esta sección es un tanto singular: no solo esta consola dispone de varias pantallas que renderizar, sino que también combina motores de tile tradicionales con un renderizador moderno.

![Comparación de resolución de pantalla y relación de aspecto entre la Game Boy, la Game Boy Advance y la serie Nintendo DS.](_diagrams/screen_comparison.png)

Empecemos con los atributos físicos: la Nintendo DS incorpora dos pantallas LCD, cada una con una resolución de **256 × 192 píxeles**, lo que supone aproximadamente un 20 % más de píxeles que la GBA. Pueden mostrar 262.144 colores (18 bits) y se refrescan a ~60 Hz.

### Arquitectura

El subsistema gráfico puede renderizar objetos tanto 2D como 3D. El primero está compuesto por geometría bidimensional —en otras palabras, bitmaps de 8 × 8 píxeles llamados 'tiles'. El segundo dibuja objetos tridimensionales (polígonos) mediante vértices.

Al adentrarnos en el chip interno que opera estas pantallas, observamos que esta consola cuenta con hardware diferenciado para geometría 2D y 3D. Los datos 2D los gestiona un motor conocido, la **Picture Processing Unit** (PPU, ahora denominada simplemente **motor 2D**), mientras que los datos 3D los maneja un subsistema completamente nuevo. Dicho esto, la consola alberga **dos motores 2D** y **un motor 3D**. Conviene señalar que, aunque esta no es la [primera consola de Nintendo](nintendo-64) en incorporar gráficos 3D, sí es la primera que incluye un diseño propio para renderizar formas tridimensionales.

![Esquema del subsistema gráfico.](_diagrams/gpu/overall.png)

Ahora bien, cada motor solo puede vincularse a una de las pantallas. Esto no supone un problema para los juegos exclusivamente en 2D, ya que cada pantalla dispone de su propio motor 2D. Sin embargo, para los juegos que deseen explotar las características más avanzadas, solo hay un motor 3D disponible. En consecuencia, las capacidades 3D solo están disponibles en una pantalla a la vez.

... ¿Y qué ocurre con la mezcla de objetos 2D y 3D? Desde luego, es posible. Permíteme explicar cada motor por separado para abordar este punto después.

### Construyendo un frame con gráficos 2D

Antes de repasar cada etapa, te recomiendo leer sobre la [PPU de la GBA](game-boy-advance#graphics), ya que aquí me limitaré a destacar los cambios que dieron lugar a la 'siguiente generación' de juegos en 2D.

Para situar el contexto: como hay dos motores 2D, el primero se denomina **Main** y el segundo **Sub**. Esto no implica necesariamente a qué pantalla está conectado cada uno. Por otro lado, Main ofrece algo más de funcionalidad que Sub.

Para apoyar las explicaciones, esta vez utilizaré *New Super Mario Bros* como ejemplo.

#### Tiles {.tabs .active}

![Algunos tiles encontrados en la VRAM. A efectos demostrativos, se utiliza una paleta por defecto.](mario/tiles.png){.tab-float .border .pixel latex_width="75%"}

A estas alturas todos sabemos cómo funciona un motor de tiles básico, pero ¿cómo se gestionan exactamente los tiles en esta consola? Pues bien, hay un total de **656 KB de VRAM** disponibles [@graphics-amero], y este bloque se reparte en distintos bancos: cuatro de 128 KB, uno de 64 KB, uno de 32 KB y tres de 16 KB. Los programadores pueden rellenar estos bancos con gráficos y luego indicar al motor qué datos necesita. Además, ambos motores pueden leer de cualquiera de los bancos, aunque no pueden acceder al mismo de forma simultánea.

No obstante, existen algunas limitaciones en la distribución de los datos. Por ejemplo, el ARM7 solo puede acceder a dos bancos de 128 KB. Al mismo tiempo, estos dos bancos no pueden almacenar sprites, y Sub es el único motor capaz de acceder al último banco de 16 KB. La lista continúa, pero ya te haces una idea.

Una última cosa: el motor 3D, al que entraremos más adelante, puede acceder a algunos de estos bancos para leer texturas.

#### Tipos de fondo {.tab}

Desde que la [Super Nintendo](super-nintendo#graphics) debutó, los diseños sucesivos de la PPU han ido orientándose a ofrecer mayor flexibilidad para las capas de fondo. Catorce años después, este esfuerzo culminó en un chip capaz de leer tiles, aplicar numerosas **transformaciones afines** y, si eso fuera poco, ensamblar una capa directamente desde un frame buffer.

Antes de analizar los distintos modos que el motor 2D utiliza para generar fondos, veamos esta lista, que especifica los tipos de fondos que el motor puede generar:

- **Grupo de tipo tile**: Estos tipos de fondo siguen el sistema de tiles tradicional; es decir, el frame se renderiza rellenándolo con tiles.
    - **Fondo estático**: Un fondo ordinario de hasta 512 × 512 píxeles, con 256 colores y 16 paletas. Incluye todos los [efectos](super-nintendo#tab-1-2-background) habituales (inversión horizontal/vertical y desplazamiento, mosaico y mezcla alfa) más un efecto de fundido adicional. Se pueden utilizar hasta 1.024 tiles.
    - **Fondo afín**: Un fondo con [transformaciones afines](super-nintendo#that-feature). Sin embargo, no admite inversión horizontal/vertical y solo puede leer 256 tiles (una cuarta parte del máximo). El tamaño de esta capa es de 1024 × 1024 píxeles.
    - **Afín extendido**: Igual que el afín, pero con la cantidad total de tiles restaurada y compatible con inversión horizontal/vertical.
- **Grupo de tipo bitmap**: En lugar de procesar tiles, el motor trata la VRAM como un frame buffer.
    - **Afín extendido (256 colores)**: Hereda todos los efectos disponibles en el 'afín extendido' de tile. La diferencia es que ahora se aplican sobre un único bitmap de 512 × 512 píxeles.
    - **Afín extendido (color directo)**: Similar al anterior, pero el frame buffer admite hasta 32.768 colores (15 bits).
    - **Pantalla grande**: Consume un bloque entero de 128 KB de VRAM para renderizar un frame buffer de 1024 × 512 píxeles.
- **Fondo 3D**: Muestra la salida del motor 3D como capa de fondo, algo esencial para visualizar lo que el motor 3D ha procesado. Aunque no ofrece muchos efectos 2D, sí presenta características interesantes como el desplazamiento horizontal y la mezcla alfa (con otras capas de fondo). Además, es el único tipo que admite hasta 262.144 colores (18 bits).

Estos modos no pueden elegirse de forma arbitraria; la consola ofrece un conjunto de **modos de fondo** con combinaciones predefinidas. Sea como fuere, siempre que un modo de fondo indique que admite 'afín extendido', los desarrolladores pueden elegir la variante ('tile', '256 colores' y 'color directo').

#### Modos de fondo {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=2}

![Capa de fondo 0 (BG0). Esta capa en particular se desplaza horizontalmente en determinadas líneas de escaneo para simular el movimiento de las nubes.](mario/bg1.png){.active .pixel .border hardcover_latex_width="85%" paperback_latex_width="76%" title="Capa 0"}

![Capa de fondo 2 (BG2).](mario/bg2.png){.pixel .border hardcover_latex_width="85%" paperback_latex_width="76%" title="Capa 2"}

![Capa de fondo 3 (BG3).](mario/bg3.png){.pixel .border hardcover_latex_width="85%" paperback_latex_width="76%" title="Capa 3"}

Capas de fondo estático en uso.

:::

Aquí es donde los tipos de fondo entran en acción. Main y Sub ofrecen varios modos de operación. Todos ellos generan **cuatro capas de fondo**; sin embargo, cada capa tiene capacidades distintas según el modo activado [@graphics-2d_modes]:

- **Modo 0**: 4 capas estáticas.
- **Modo 1**: 3 capas estáticas + 1 capa afín.
- **Modo 2**: 2 capas estáticas + 2 capas afines.
- **Modo 3**: 3 capas estáticas + 1 capa afín extendida.
- **Modo 4**: 2 capas estáticas + 1 capa afín + 1 capa afín extendida.
- **Modo 5**: 2 capas estáticas + 2 capas afines extendidas.
    - Este es un modo muy popular por su gran flexibilidad.
- **Modo 6**: 1 capa de fondo 3D + 1 pantalla grande.
    - Como solo hay espacio para un único frame buffer, este modo está disponible únicamente en 'Main'.

Además, en los modos 0 a 5, el motor Main puede configurar la primera capa estática como fondo 3D. Las capacidades 3D se tratarán en breve.

#### Sprites {.tab}

![Capa de sprites renderizada.](mario/sprites.png){.tab-float latex_width="90%" .pixel .border}

Los sprites u 'Objetos' heredan la misma funcionalidad que la PPU de la GBA, pero con dos añadidos destacables.

En primer lugar, la **Object Attribute Memory** (OAM, la región donde se almacenan las entradas de sprites) tiene ahora **2 KB** de tamaño [@cpu-mem_map], lo que permite mostrar hasta 128 sprites por frame y por pantalla. Por tanto, se asigna 1 KB a cada motor.

En segundo lugar, la OAM puede referenciar ahora **bitmaps de la VRAM** en lugar de utilizar únicamente tiles y paletas [@graphics-bitmap_sprites]. Este es otro alejamiento del sistema de tiles. De hecho, ambos 'modos' de sprite pueden coexistir en el mismo frame, ya que esta opción se establece por sprite individual.

#### Resultado {.tab}

![Todas las capas combinadas... ¿falta algo?](mario/halfcomplete.png){.tab-float .pixel .border}

A medida que cada capa se renderiza al vuelo, la etapa final se encarga de combinarlas y enviarlas a la pantalla seleccionada. Esto es básicamente lo que ocurre en las anteriores consolas basadas en PPU. ¿Significa esto que hemos terminado?

¡Todavía no! Main aún necesita leer una capa del otro motor, el más potente.

### El acelerador 3D {.tabs-close}

Si alguna vez has jugado con una Nintendo DS, ya sabes que esta consola puede mostrar una cantidad *particular* de gráficos 3D. Sin embargo, a diferencia de algunos juegos de GBA, estos no se [renderizan mediante la CPU](game-boy-advance#beyond-tiles). En su lugar, la CPU NTR incluye **dos componentes** que juntos constituyen el **motor 3D**, un diseño que me recuerda al [Reality Coprocessor de SGI](nintendo-64#graphics).

Volviendo a la sección 'Modos de fondo', notarás que todos los modos tienen al menos una capa estática. Esto se debe a que puedes rellenar esa capa con los gráficos producidos por el motor 3D. La única condición es que solo Main puede hacerlo. Esto también explica por qué el Modo 6 solo está disponible en Main.

#### Motor de geometría {.tabs .active}

![Arquitectura del motor de geometría.](_diagrams/gpu/geometry.png){.tab-float}

Si has leído alguno de los artículos de la 5.ª o 6.ª generación, puede que te preguntes... ¿Dónde está el [procesador SIMD](sega-saturn#graphics)? Es una buena pregunta, porque el ARM9 no destaca precisamente en operaciones vectoriales, y no creo que el divisor dedicado sea suficiente. Por eso Nintendo integró un componente llamado **Geometry Engine** que se encarga de las **transformaciones de vértices**, la **proyección**, la **iluminación**, el clipping, el culling y el **ordenamiento de polígonos**, este último esencial para el uso correcto de las características de transparencia [@graphics-3d_control].

Este motor tiene algunas limitaciones estrictas, especialmente en cuanto al número de polígonos que puede procesar: dispone de **248 KB adicionales de RAM** para almacenar la geometría procesada. Esta cantidad admite hasta **2048 triángulos o 1706 cuadriláteros** [@graphics-3d_status], aunque este límite puede ampliarse mediante tiras de polígonos (en lugar de polígonos individuales). Cabe tener en cuenta, además, que la resolución de pantalla de esta consola es relativamente pequeña, lo que también sirve de compensación.

En cualquier caso, este motor se controla mediante una **Command FIFO** que la CPU o el DMA van rellenando [@graphics-3d_commands]. La FIFO almacena 256 entradas, complementadas por otro búfer llamado **PIPE** con capacidad para cuatro comandos más (para un total de 260).

#### Motor de renderizado {.tab}

![Arquitectura del motor de renderizado.](_diagrams/gpu/rendering.png){.tab-float}

El motor de renderizado se encarga de convertir los vectores en píxeles (es decir, de la rasterización), de colorearlos (es decir, de aplicar texturas) y de aplicar iluminación y otros efectos [@graphics-3d_overview]. Para interpolar texturas e iluminación, respectivamente, recurre a la **perspective correction** y al **sombreado Gouraud**. Además, la unidad ofrece características modernas como **niebla**, **mezcla alfa**, **depth buffering** (ya sea [Z-buffering](nintendo-64#modern-visible-surface-determination) o una variante denominada W-buffering), **stencil tests** y **antialiasing**. Sin embargo, este último es muy básico (simplemente define los bordes exteriores de los polígonos como transparentes) y solo funciona con píxeles opacos.

El sistema de renderizado es una mezcla de lo antiguo y lo nuevo: en lugar de generar un frame buffer, emplea el **renderizado con búfer de línea**, rellenando líneas de escaneo (de forma similar al motor 2D) y almacenando los resultados en un búfer más pequeño. Esto se debe a que el motor 3D debe operar al mismo ritmo que el motor 2D.

Al prescindir del frame buffer tradicional, el rasterizador emplea el **renderizado por línea de escaneo**, recorriendo cada línea para procesar los bordes de polígonos que encuentre. Arisotura (la desarrolladora del emulador MelonDS) señaló que, por cada cuadrilátero, el renderizador solo puede rellenar **un tramo por línea de escaneo** [@graphics-arisotura]. Esto puede resultar problemático, ya que el resultado se desordena si el cuadrilátero es cóncavo o contiene bordes cruzados, por ejemplo.

En cuanto a los efectos, la unidad también ofrece **sombreado** y una característica singular llamada **Toon Shading** (otro nombre para el [Cel Shading](gamecube#creativity)): aunque esta unidad no es [programable](xbox#the-importance-of-programmability), se pueden modificar los parámetros de iluminación para lograr un efecto de dibujo animado [@graphics-3d_control].

#### Resultado {.tab}

![Ah, así está mejor.](mario/complete.png){.tab-float .border .pixel}

En lugar de escribir los resultados en un frame buffer para su visualización, el motor de renderizado escribe en un bloque denominado **Colour Buffer** que almacena hasta 48 líneas de escaneo [@graphics-3d_overview]. El motor 2D lee cada línea de escaneo para rellenar la capa BG0. Esto se hace siguiendo el modelo FIFO.

El renderizado 3D comienza antes que el 2D, lo que permite a este último aplicar transformaciones sobre la nueva capa si es necesario. Main también permite capturar el frame generado por el motor 2D, el 3D o la combinación de ambos, combinarlo con otro frame de la VRAM y escribir el resultado de vuelta a la VRAM para su posterior visualización.

En cuanto al control, el motor de renderizado también permite modificar sus parámetros a mitad del frame gracias a un mecanismo de doble búfer que preserva una copia del estado anterior hasta que el frame actual ha terminado de dibujarse. De este modo, no se producen desgarros de imagen.

### Comparativas famosas {.tabs-close}

Algunos de los primeros juegos lanzados para esta consola intentan emular sus equivalentes de Nintendo 64. Por eso me pareció una buena oportunidad para resumir por qué los jugadores pueden notar ciertos patrones entre ambas versiones [@fig-mario_kart_comp].

::: {.subfigures .side-by-side max_subfigures=2 #fig-mario_kart_comp}

![Super Mario 64 (1996).<br>Renderizado a 320×240 píxeles.](comparison/mario_n64.png){.toleft hardcover_latex_width="87%" paperback_latex_width="78%" .border .pixel}

![Super Mario 64 DS (2004).<br>Renderizado a 256×192 píxeles.](comparison/mario_nds.png){.toright hardcover_latex_width="87%" paperback_latex_width="78%" .border .pixel}

Primera comparativa de la serie Super Mario 64.

:::

::: {.subfigures .side-by-side max_subfigures=2}

![Mario Kart 64 (1996).<br>Renderizado a 320×240 píxeles.](comparison/kart_n64.png){.toleft hardcover_latex_width="87%" paperback_latex_width="78%" .border .pixel}

![Mario Kart DS (2005).<br>Renderizado a 256×192 píxeles.](comparison/kart_nds.png){.toright hardcover_latex_width="87%" paperback_latex_width="78%" .border .pixel}

Segunda comparativa de la serie Mario Kart.

:::

Para explicar lo que ocurre, he organizado las distintas aclaraciones en función de lo que algunos usuarios comentaron en foros:

- Las texturas de la NDS parecen más **pixeladas**: El motor de renderizado no aplica ningún filtro. Por tanto, las texturas se interpolan mediante el método del 'vecino más próximo'.
- Las texturas de la NDS parecen más **ricas**: El motor de renderizado no está limitado por un [bloque de 4 KB de memoria de texturas](nintendo-64#tab-3-2-texture-memory). En su lugar, dispone de hasta 512 KB de VRAM y numerosos mecanismos de compresión. Naturalmente, esto permite cargar más datos.
- Los modelos de la NDS presentan **bordes pixelados**: Los modelos de la NDS se renderizan a una resolución inferior a la de sus equivalentes de N64.
- Las texturas de la NDS aparecen **distorsionadas** al verlas desde lejos: El rasterizador opera con coordenadas de [punto fijo](playstation#missing-units). La baja resolución y la ausencia de mip-mapping también contribuyen al aliasing.

En pocas palabras, eso es lo esencial. Para casos más especializados, tendrás que profundizar en ambos motores y posiblemente desensamblar los juegos para investigar qué funciones se utilizan y cómo.

### Modelos en 3D

A continuación se visualizan modelos 3D de juegos diseñados para esta consola. Fíjate en el uso de mapas de textura detallados, aunque limitados al filtrado por vecino más próximo. Esto da lugar a un aspecto 'moderno pixelado', icónico de los gráficos de la Nintendo DS.

![Nintendogs (2005).<br>750 triángulos.](dalmatian_ds){.toleft hardcover_latex_width="70%" paperback_latex_width="61%" model3d="true"}

![New Super Mario Bros (2006).<br>636 triángulos.](mario_ds){.toright model3d="true"}

A pesar de haber hablado de muchas limitaciones del subsistema gráfico, he de decir que gran cantidad de juegos supieron sacarle partido.

## Audio

Ya hemos visto cómo los juegos de Game Boy Advance disponían de una combinación de [síntesis moderna con características heredadas](game-boy-advance#audio), lo que llevó a que los juegos posteriores priorizasen finalmente los elementos contemporáneos (es decir, la síntesis basada en samples). Con la Nintendo DS, el nuevo subsistema de audio se centra en subsanar las limitaciones de los canales modernos de Modulación por Impulsos Codificados (PCM, del inglés *Pulse-Code Modulation*) para alcanzar mayor fidelidad.

![Osciloscopio mostrando la salida estéreo mezclada durante una partida de Last Window: The Secret of Cape West (2010).](window){.open-float .negate .border latex_width="90%" video="true"}

En consecuencia, el nuevo conjunto de audio incorpora un total de **16 canales PCM**, lo que permite trasladar la tarea de mezcla de la CPU al hardware dedicado. Internamente, las muestras PCM pueden ser ahora de **8 bits** (*al estilo GBA*), **16 bits** (la resolución óptima) o **ACPCM** (una forma comprimida) [@audio-channels]. En todos los casos, el mezclador produce una señal estéreo que puede reproducirse por el altavoz —ahora estéreo— o por auriculares. También puede escribir los datos estéreo resultantes en la WRAM, lo que permite al subprocesador (ARM7) aplicar efectos como la reverberación.

{.close-float}

Dicho esto, ¿significa entonces que la Nintendo DS puede por fin procesar códecs populares como el MP3? Sí, de hecho, algunos programas homebrew lo implementaron de alguna forma. Sin embargo, la decodificación de audio sigue consumiendo un considerable ancho de banda, potencia de procesamiento y almacenamiento [@cpu-homebrew]. Por ello, el *secuenciado* de audio conservó su puesto como la opción más viable.

### Un Generador de Sonido Programable (o dos) de lo más excéntrico

Dado que esta consola puede ejecutar juegos de GBA de forma nativa, debería albergar circuitos que reproduzcan el [Generador de Sonido Programable](game-boy-advance#tab-4-2-psg) (PSG) de su predecesora, ya sea en hardware o en software. Y así es: los últimos seis canales PCM incluyen un 'modo PSG'. Esto les permite sintetizar una onda de pulso o una forma de onda personalizada, aunque solo dos pueden generar ruido. Sin embargo, por alguna razón, **¡los juegos de GBA no hacen uso del modo PSG!**

Como se mencionó anteriormente, la salida del mezclador llega a los altavoces y auriculares a través de un **Conversor Digital-Analógico (DAC, del inglés *Digital-to-Analogue Converter*) de 10 bits**, cuya frecuencia de salida es de **32 kHz** [@audio-regs]. Esto es considerablemente inferior a la calidad de las muestras que se le suministran. Además, **no realiza ningún tipo de interpolación** para suavizar la pérdida de precisión. Estas medidas de reducción de costes no son ideales para la reproducción de samples, ya que añaden ruido. Ahora bien, el grado en que esto resulta perceptible depende de tu oído: en mis intentos de identificar el ruido adicional, no percibí ningún 'siseo' hasta que subí el volumen y lo comparé directamente con una variante de 16 bits. Y, siendo justos, el sistema de sonido sigue siendo un paso adelante respecto a los [samples mezclados por software con resolución de 8 bits](game-boy-advance#tab-4-1-direct-sound). Por el contrario, el efecto de aliasing es más problemático con los sonidos PSG, ya que la reducción de muestreo introduce armónicos erróneos que distorsionan el tono PSG original. No obstante, juegos como New Super Mario Bros. hacen buen uso de las ondas de pulso como acompañamiento, así que no consideraría el modo PSG completamente inútil.

Volviendo al tema, ¿cómo gestiona todo esto un juego de GBA? *No lo hace*. Dentro de la misma carcasa, Nintendo integró un **sistema de sonido separado** para el modo AGB, con sus propios canales y mezclador que siguen las especificaciones de la consola predecesora. Así, los juegos de GBA no se ven afectados por las limitaciones del nuevo mezclador. Por desgracia, dado que este subsistema está segregado del de la DS (es decir, no envía señal al mezclador de la DS), los juegos de DS no pueden hacer uso de él.

### Comparación interactiva {.interactive-only}

He creado este widget interactivo para que puedas comparar cómo el nuevo sistema de audio influyó en la siguiente generación de bandas sonoras. Cada widget reproduce la misma partitura, pero te permite alternar entre los arreglos antiguo y nuevo (te sugiero usar auriculares para apreciar realmente la diferencia). ¡Dale una vuelta!

::: {.subfigures .side-by-side figure="false" .interactive-only}

![**GBA:** Gyakuten Saiban (2001, solo en JAP).<br>**NDS:** Phoenix Wright: Ace Attorney (2005).](){audio_switcher="true" src1="trial_gba" src2="trial_nds" label1="GB Advance" label2="Nintendo DS" .toleft}

![**GBA:** Mario Kart: Super Circuit (2001).<br>**NDS:** Mario Kart DS (2005).](){audio_switcher="true" src1="yoshi_gba" src2="yoshi_nds" label1="GB Advance" label2="Nintendo DS" .toright}

:::

Sea como fuere, tuve que amplificar un poco la ganancia de la banda sonora de GBA para igualar el volumen. Esto afecta a la relación señal-ruido —algo a tener en cuenta mientras cambias entre las dos. En cualquier caso, espero que hayas podido apreciar cómo ha evolucionado el subsistema de sonido.

### Algunos tropiezos {.interactive-only}

A continuación te muestro algunos casos complicados. En ciertos ports, la consola original contaba con características de audio únicas que no resultaba sencillo recrear en esta consola, aunque te dejo a ti el veredicto:

::: {.subfigures .side-by-side figure="false" .interactive-only}

![**SNES:** Super Mario Kart (1992).<br>**NDS:** Mario Kart DS (2005).](){audio_switcher="true" src1="mario_snes" src2="mario_nds" label1="SNES" label2="Nintendo DS" .toleft}

![**Mega Drive:** Sonic 3D Blast (1996).<br>**NDS:** Sonic Chronicles (2008).](){audio_switcher="true" src1="sonic_megadrive" src2="sonic_nds" label1="Mega Drive" label2="Nintendo DS" .toright}

:::

Como puedes escuchar en el primer ejemplo (especialmente en los últimos diez segundos), es difícil competir con los matices que ofrecía la [S-SMP](super-nintendo#audio) de la SNES.

Debo confesar que incluí el segundo ejemplo a propósito. En serio, ¿qué *demonios* pasó ahí? Como si el nuevo arreglo hubiese sido concebido inicialmente para una consola de Atari. En mi opinión, la Nintendo DS podría haber gestionado algún tipo de remuestreo de FM a PCM, así que el nuevo arreglo *minimalista* puede ser simplemente una elección creativa.

## E/S

A decir verdad, las capacidades de E/S de la Nintendo DS merecen un estudio aparte. Equipos que antes estaban fuera del alcance del gran público —como la conectividad inalámbrica, una pantalla táctil, un Reloj en Tiempo Real (RTC, del inglés *Real-Time Clock*) y un micrófono— combinados con software de primera categoría, hicieron destacar a esta consola año tras año durante todo su ciclo de vida. Diría que Nintendo acertó con la innovación adecuada, al precio adecuado y en el momento adecuado; una filosofía reafirmada con la llegada de la [Wii](wii), dos años después.

Bajo el capó, sin embargo, los componentes de E/S son gestionados en exclusiva por el ARM7. De hecho, más allá de transferir datos de un lado a otro, no verás demasiada actividad en esa CPU... lo que, en última instancia, deja al ARM7 un tanto desaprovechado.

### Acceso a cartuchos y memoria

En cuanto a los componentes internos, hay un bloque de **Memory Interface** que conecta tres extremos:

- Un **Slot-1 de 17 pines**, donde van las tarjetas de Nintendo DS.
- Un **Slot-2 de 32 pines**, donde residen los [Game Paks de GBA](game-boy-advance#games) o los accesorios (como el *Rumble Pak*).
- **4 MB de PSRAM**, que es la memoria principal.

Ambas CPU pueden acceder a esta interfaz, aunque contiene registros que se modifican para priorizar una CPU sobre la otra en caso de que se emitan dos solicitudes simultáneas en el mismo bus.

![Modelo de memoria externa con el ancho del bus de datos etiquetado.](_diagrams/memory_access.png)

#### Dentro de las tarjetas Slot-1

Aquí viene la parte complicada: a diferencia del Game Pak original, **las tarjetas DS no están mapeadas en memoria**; utilizan múltiples **interfaces serie** para comunicarse. Por tanto, para que cualquiera de las CPU pueda leer el contenido de la tarjeta, este debe copiarse primero a la RAM.

En el caso del chip ROM de la tarjeta, la CPU debe enviar bloques de comandos de 64 bits que referencian direcciones de 32 bits a la tarjeta DS. Después, los datos pueden recuperarse bien extrayéndolos de un registro de 32 bits, bien mediante DMA. El bus de datos tiene 4 bits de ancho y, dependiendo del tipo de ROM instalada, puede alcanzar velocidades de hasta **6,7 MB/s** [@io-card_ports].

Junto a la ROM de la tarjeta DS, suele haber un chip de 'respaldo' para almacenar los datos de guardado. A estos chips se accede mediante un bus de **Interfaz Periférica Serie** (SPI, del inglés *Serial Peripheral Interface*) [@io-backup]. En él podemos encontrar:

- **EEPROM** o **memoria flash**: Como [ya incluían los Game Paks](game-boy-advance#cartridge-ram-space).
- **RAM Ferroeléctrica (FRAM)**: Se comporta como la Static RAM (SRAM) pero no requiere batería.

Curiosamente, algunos juegos reutilizaron el bus SPI para incluir hardware especial, como:

- Un **controlador infrarrojo**: Ciertos títulos de Pokémon integran en su tarjeta un sensor y un emisor de infrarrojos, ya sea para comunicarse con un podómetro externo (el *Pokéwalker*), o para interactuar con otros jugadores antes de usar el Wi-Fi (presumiblemente como mecanismo adicional de protección contra copias).
- Un **controlador Bluetooth 2.1**: El juego *Learn with Pokémon: Typing Adventure* albergaba en su tarjeta un controlador Broadcom BCM2070 [@io-extras] [@io-bcm2070], utilizado para conectarse al teclado inalámbrico incluido en la caja del juego.
- Un **adaptador MicroSD**: El juego *DSvision*, exclusivo de Japón, permitía a los usuarios reproducir películas en la Nintendo DS (podría considerarse un sucesor espiritual del [GBA Video](game-boy-advance#beyond-tiles)). Las películas se vendían en tarjetas MicroSD de 512 MB con marca DSvision [@io-dsvision_video], o se descargaban de sus servidores y se transferían mediante un adaptador USB-MicroSD patentado [@io-dsvision]. En ambos casos, los datos empleaban gestión de derechos digitales para impedir el acceso o la modificación por terceros.
  - Puede que recuerdes la MicroSD como el ingrediente principal de las 'flashcards'. La idea es similar, pero la ejecución es muy diferente. Entro en más detalles más adelante, en la sección 'Antipiratería'.

#### Dentro de los cartuchos Slot-2

En la parte inferior de la consola, el Slot-2 está mapeado en memoria usando el [patillaje original](game-boy-advance#accessing-cartridge-data), aunque las direcciones están ligeramente desplazadas en modo DS. Sin embargo, al igual que en la GBA, el bus de datos de la ROM tiene 16 bits de ancho y el bus de datos de la RAM tiene 8 bits.

### Periféricos

El ARM7 también está conectado a otro nodo SPI que hace de interfaz con el **controlador de pantalla táctil**, que gestiona la pantalla inferior. Está formada por un panel resistivo que requiere el uso de un lápiz stylus. El bus también conecta con la memoria flash donde se almacena el *firmware* (más detalles en la sección 'Sistema Operativo').

::: {.subfigures .open-float .tabs-nested .tab-float max_subfigures=2 #fig-hotel_dusk_puzzle}

![Un puzle de centralita. Para rescatar a la chiquilla, el jugador tenía que deslizar la pantalla con dos dedos al mismo tiempo para encender las luces.](puzzle.png){.active .pixel .border hardcover_latex_width="95%" paperback_latex_width="85%" title="Puzzle"}

![Pero si lo haces mal...](puzzle_fail.png){.pixel .border hardcover_latex_width="95%" paperback_latex_width="85%" title="Fallo"}

Hotel Dusk: Room 215 (2007).

:::

Una característica curiosa de esta pantalla táctil es que, además de detectar las posiciones X/Y, también puede devolver la **posición diagonal** (usada para calcular el 'valor de presión', que representa el área sobre la que se ejerce presión). Por desgracia, esta función nunca se expuso oficialmente. Así que, que yo sepa, ningún juego comercial hizo uso de esta característica indocumentada, salvo el homebrew.

En cuanto este artículo se difundió por distintos foros, muchos lectores señalaron que *Hotel Dusk: Room 215* recurría a esta característica en uno de sus puzles, donde se le indica al jugador que use dos dedos al mismo tiempo [@fig-hotel_dusk_puzzle]. Sin embargo, no es así. Tras realizar una serie de experimentos con el depurador no$gba, observé que el puzle no hace uso de los datos de presión. En cambio, comprueba si los valores X/Y alternan de forma brusca. El juego interpreta este comportamiento como si el jugador estuviera presionando la pantalla con dos dedos.

{.close-float}

El controlador de pantalla táctil también está conectado al **micrófono** de la consola. Los juegos lo operan simplemente activando un amplificador y después emitiendo comandos SPI para obtener su entrada, bit a bit [@io-microphone]. El audio capturado tiene una resolución de 8 bits, aunque su frecuencia de muestreo depende de cuántos ciclos el ARM7 tenga previsto dedicar a la extracción de muestras. En general, aunque el micrófono es muy básico en cuanto a capacidades, los juegos lo usaron bien como detector básico de voz o para identificar acciones como soplar en él. *WarioWare: Touched!* y la serie *Nintendogs* son ejemplos de juegos que aprovecharon el uso del micrófono.

Por último, en el mismo conjunto, encontramos el **Reloj en Tiempo Real (RTC)**. Algunos títulos recurrieron a él para que los escenarios evolucionasen conforme avanzaba el tiempo. Juegos como *Animal Crossing: Wild World*, *The Sims 2* y la serie *Pokémon* son ejemplos notables. El sistema operativo de esta consola también mantenía un calendario interno para uno de sus servicios.

### Red inalámbrica

Por último, y no menos importante, la consola alberga un **Controlador Inalámbrico** dedicado que implementa la especificación IEEE 802.11b, también conocida como 'Wi-Fi'. El controlador opera en la banda de 2,4 GHz, donde Nintendo implementó numerosos servicios innovadores para habilitar el juego multijugador [@io-wifi]:

- **Internet Play**: La consola se conecta a un punto de acceso (PA) Wi-Fi existente para comunicarse con un servidor en línea.
  - En esta ocasión, Nintendo proporcionó la infraestructura de red y las bibliotecas necesarias para gestionar la autenticación y el emparejamiento. Posteriormente, toda la comunicación entre cliente y servidor era gestionada por servidores con certificados SSLv3 firmados por Nintendo [@io-ssl].
  - Para las conexiones a puntos de acceso cifrados, la consola solo admitía Wired Equivalent Privacy (WEP), un protocolo actualmente considerado inseguro.
- **Multi-card Play**: Configura la Nintendo DS como punto de acceso con un identificador personalizado, permitiendo que hasta 15 consolas se conecten a ella.
- **Single-card Play**: El sucesor del [Multiboot](game-boy-advance#secondary-boot), que permite al juego cargar un pequeño programa en otra DS. Resultaba especialmente útil cuando un grupo de amigos solo disponía de una copia de la tarjeta.

Puede que recuerdes que todo esto también se comercializó bajo el memorable logotipo 'Nintendo Wi-Fi Connection'.

## Sistema Operativo

Podemos afirmar con cierta seguridad que, en esta generación, las consolas siempre vienen acompañadas de algún tipo de capa de abstracción e interfaz interactiva. La Nintendo DS sigue heredando el modelo de sistema operativo anterior, consistente en APIs ligeras para simplificar el acceso a la E/S, pero ahora incluye una interfaz de usuario mínima para ajustar la configuración e interactuar con algunas de sus 'aplicaciones'.

Dicho esto, el sistema operativo de esta consola está distribuido en varios chips, así que empecemos por los que se leen durante el arranque.

### Punto de entrada

Desde el primer momento, el ARM7 y el ARM9 deben inicializar el hardware antes de poder hacer algo de provecho. Para ello, la CPU NTR (el SoC) integra dos pequeñas ROM:

- Una **BIOS de 4 KB** conectada al bus del ARM9.
- Una **BIOS de 16 KB** conectada al bus del ARM7.

Al encender la consola, cada CPU arranca desde su respectiva ROM, ya que sus **vectores de reset** apuntan a cada chip. Para referencia, el vector del ARM9 está en `0xFFFF0000`, mientras que el del ARM7 está en `0x00000000` [@operating_system-boot].

### La siguiente etapa

A continuación, cada BIOS almacena dos conjuntos de rutinas: código de arranque y llamadas de interrupción. Estas últimas no son ninguna novedad si conoces la historia de la [consola anterior](game-boy-advance#operating-system), pero el código de arranque ha crecido en complejidad. Además de inicializar el hardware, el código del ARM7 también realiza **comprobaciones de seguridad** sobre la tarjeta DS y copia el **firmware del sistema** a la RAM principal.

El firmware del sistema se recupera de una **Flash de 256 KB** que reside en la placa hija del Wi-Fi y solo es accesible a través del bus SPI [@operating_system-firmware]. El mismo chip también almacena diversas preferencias del usuario (idioma, apodo, fecha de nacimiento, alarma y mensaje de bienvenida), así como la configuración del sistema (calibración de la pantalla táctil, indicador de primer inicio, versión del firmware y configuración del Wi-Fi).

Una vez completadas sus respectivas rutinas de arranque, ambas CPU se sincronizan para comenzar a funcionar como una 'máquina única'. El ARM9 termina de cargar muy por delante del ARM7, de modo que envía un valor de 4 bits al ARM7 y permanece en un bucle casi interminable hasta que este responde. Una vez que lo hace, ambos procesadores proceden a cargar su primer programa desde la RAM: el firmware.

#### Ventana de oportunidad

Si alguna vez has jugado con una Nintendo DS, probablemente hayas notado que **solo** puedes jugar si la tarjeta está insertada antes de encender la consola. Esto se debe a que la BIOS del ARM7 realiza algunas comprobaciones sobre la tarjeta durante el arranque (lo explico con más detalle en la sección 'Antipiratería'). Si todas las pruebas se superan, el ejecutable del juego para el ARM7 se copia entonces a la WRAM, y el del ARM9 se copia a la memoria principal.

Si los ejecutables no se copian —ya sea porque la tarjeta no es válida o no se detectó durante el arranque—, el juego no puede iniciarse y el usuario tendrá que reiniciar la consola para intentarlo de nuevo.

### Interfaz interactiva

Con o sin juego presente, el sistema completa su proceso de arranque cargando la interfaz interactiva del firmware.

![Pantalla principal.](shell/home.png){.tabs-nested .active .open-float .tab-float .desktop-margined .pixel .border hardcover_latex_width="90%" paperback_latex_width="80%" title="Inicio"}

![Esta pantalla aparece cada vez que enciendes la DS. El logotipo 'Nintendo' solo aparece cuando hay una tarjeta válida insertada.](shell/welcome.png){.tab-nested .pixel .border hardcover_latex_width="90%" paperback_latex_width="80%" title="Splash"}

![Pantalla de ajustes.](shell/settings.png){.tabs-nested-last .pixel .border hardcover_latex_width="90%" paperback_latex_width="80%" title="Ajustes"}

La interfaz es, en líneas generales, similar a la de sus contemporáneas. Los usuarios la emplean para iniciar su juego, cambiar la configuración, descargar software mediante 'Download Play', o entretenerse con el **PictoChat**: un chat abierto que se comunica con las Nintendo DS cercanas (usando el protocolo 'Multi-card Play').

Conviene destacar que tanto los datos de solo lectura como los modificables residen en el mismo chip Flash (regrabable), así que, en teoría, ¡es posible sobrescribir el firmware! Por suerte, o por razones evidentes, Nintendo protegió el último cuarto del chip (64 KB) frente a escrituras exigiendo un puente en un punto de la placa base llamado `SL1`, accesible al retirar el compartimento de la batería. No obstante, sobrescribir el resto de la memoria flash puede seguir teniendo consecuencias catastróficas [@operating_system-bricker].

{.close-float}

### Actualizabilidad

Por último, este firmware acabó siendo actualizado por Nintendo en varias ocasiones (cinco, para ser exactos) con el objetivo de corregir vulnerabilidades de seguridad. Las actualizaciones no podían ser instaladas por el usuario (recuerda la protección `SL1`). En cambio, Nintendo incluyó el firmware revisado en los sucesivos lotes de fabricación.

## Juegos

Aquí hay mucho de lo que hablar, principalmente porque las capacidades de esta consola inspiraron a programadores y artistas a desarrollar diseños innovadores y nuevas formas de interacción. Veamos...

### Soporte

Esta consola ejecuta juegos procedentes de tres fuentes, aunque solo dos de ellas pueden hacer un aprovechamiento 'completo' del hardware:

![Ejemplo de un juego comercial en Slot-1.](game.jpg){.open-float}

- **Tarjetas NDS o 'Slot-1'**: Este es el soporte principal para cargar juegos nativos de DS y el único formato utilizado para la distribución comercial.
- **Cartucho GBA o 'Slot-2'**: Este slot es uno de los pilares de la compatibilidad nativa con la Game Boy Advance. Sin embargo, dado que los juegos del Slot-1 también pueden acceder a esta interfaz, se desarrollaron **cartuchos de expansión** para mejorar los programas de DS. Estos aportaban RAM adicional, mandos de entrada extra (como el *Guitar Hero Grip Controller*) o retroalimentación háptica (es decir, el *Rumble pack*).
- **Download Play**: También conocido como '[MultiBoot](game-boy-advance#secondary-boot) inalámbrico', permite que otra consola que ejecute un juego del Slot-1 transfiera un programa mediante comunicación inalámbrica. El contenido descargado se almacena en la WRAM y arranca una vez completada la transferencia. Como la WRAM es volátil, los datos descargados se borran al apagar la consola.
  - Los establecimientos comerciales autorizados utilizaron esta función para instalar **Estaciones de Descarga**, donde los consumidores podían descargar demos de juegos.

{.close-float}

### Estructura del programa

Ya has visto que la BIOS de esta consola debe dividirse en código ARM9 y ARM7. Pues bien, el mismo principio se aplica en gran medida a los juegos. Por tanto, la ROM dentro de las tarjetas NDS se organiza en las siguientes áreas:

- **Cabecera (4 KB)**: Contiene metadatos, incluido el punto de entrada de cada ejecutable, información del fabricante y el logotipo de Nintendo [@games-header].
- **Área segura (16 KB)**: Utilizada para la protección contra copia. Los detalles se tratan en la sección 'Antipiratería'.
- **Contenido principal (tamaño variable)**: El espacio restante almacena los ejecutables y los datos del juego (gráficos, sonidos, etc.). Además, la disposición de estos datos fue definida por el kit de desarrollo oficial de Nintendo o 'SDK' (una dependencia obligatoria para los juegos comerciales), que implementó un sistema de archivos estructurado de forma jerárquica con archivos y directorios [@games-fs].

### Ecosistema de desarrollo

Para los estudios interesados en desarrollar juegos para esta consola, Nintendo suministraba tanto **kits de hardware** como un **kit de desarrollo de software** (SDK) repleto de utilidades.

#### El hardware {.tabs .active}

El kit de desarrollo, llamado **IS-NITRO-EMULATOR**, consistía en una unidad azul de tamaño mediano que albergaba la mayor parte del hardware interno y los componentes de E/S de la DS [@games-emulator]. A ella se conectaba un cable grueso unido a una carcasa ficticia de Nintendo DS, que servía de 'mando' y pantalla. A petición, el devkit podía equiparse con capacidades opcionales como salida de audio/vídeo, una interfaz Wi-Fi real (por defecto, el Wi-Fi se emulaba mediante Ethernet) y soporte para depuración. Personalmente, esperaba que esto último fuese estándar, aunque los equipos de pruebas también podían utilizarlas.

El kit lee tarjetas DS, y Nintendo también proporcionó **tarjetas de desarrollo regrabables** para facilitar el trabajo de los desarrolladores. Estas tenían una carcasa más grande con chips de respaldo intercambiables [@games-hardware].

En definitiva, estos son solo algunos ejemplos, pero a lo largo del ciclo de vida de la consola se vendieron muchos otros equipos para ayudar con tareas específicas, como la composición musical, para lo que Nintendo ofrecía el 'IS-NITRO-UIC', un adaptador MIDI que conectaba la Nintendo DS a un PC.

#### El software {.tab}

Por su parte, **DS-NITRO-SDK** era el complemento en software. Este paquete incluía utilidades para interactuar con el devkit, las cadenas de herramientas de C y C++, y las APIs de hardware [@games-software]. Cabe destacar que Nintendo prohibía cualquier intento de saltarse las APIs proporcionadas para acceder directamente al hardware. Aunque **los juegos se ejecutan sobre el metal desnudo**, Nintendo no aprobaría su distribución si realizaban operaciones 'prohibidas' [@games-insider], como controlar el ARM7 directamente o apagar las pantallas.

Existen razones comprensibles para imponer estas normas; por ejemplo, mantener una experiencia de juego coherente y [garantizar niveles de calidad elevados](nes#anti-piracy-and-region-lock). Aunque, en mi opinión, limitar el ARM7 a tareas de E/S refleja una falta de confianza en los desarrolladores, lo que en última instancia desperdició el potencial de la CPU...

### Libertad de interacción {.tabs-close}

![Dr. Kawashima's Brain Training (2005). Las nuevas categorías de juegos atrajeron a un público más allá del círculo juvenil tradicional.](kawashima.png){.open-float .pixel .border}

Por el lado positivo, gracias a todas las nuevas formas de interacción disponibles, los estudios tuvieron la oportunidad de priorizar la experiencia de juego sobre los gráficos.

Por primera vez en la electrónica de consumo, dos pantallas, una pantalla táctil, un micrófono, conexión Wi-Fi y un reloj en tiempo real convivían en la misma consola portátil. En consecuencia, con el paso de los años, los juegos posteriores presentaron formas de interacción novedosas: algunos requerían sostener la consola de lado (como *Brain Training*), otros animaban a jugar con la bisagra (como *Hotel Dusk*) e incluso algunos usaban el micrófono para intentar enseñar inglés (*English Training*).

Nintendo aprovechó esta corriente con una nueva campaña llamada **Touch Generations**, orientada a ofrecer productos a un público de mayor edad.

{.close-float}

De hecho, algunos 'juegos' eran simplemente aplicaciones independientes envueltas en el encanto de Nintendo, como el *Nintendo DS Browser*, una versión del Opera 8.5 [@games-browser], o *Cooking Guide: Can't Decide What to Eat?*, un libro de recetas interactivo. ¿No se asemeja esto al modelo de negocio que los futuros smartphones (y las *apps*) adoptarían poco después?

## Antipiratería y homebrew

Aunque las tarjetas DS no se vieron afectadas por la [maldición del disco compacto](playstation#anti-piracy-region-lock), los [trucos basados en marcas registradas](game-boy#anti-piracy) ya no bastaban para frenar la producción de [flashcarts](game-boy-advance#flashcarts). Por ello, esta vez Nintendo reforzó su seguridad tradicional con capas adicionales para recuperar el control sobre la distribución de juegos.

### Mecanismos de seguridad

Para entender el nuevo sistema implantado, analicemos cada área por separado.

#### Sistema de cifrado {.tabs .active}

Aunque los datos del juego en la ROM siguen sin estar cifrados, la Nintendo DS estrena un sistema de **cifrado simétrico** para proteger la comunicación entre la Memory Interface y la tarjeta del Slot-1.

El área 'Cabecera' de la tarjeta contiene un valor denominado **Gamecode**, que sirve como identificador único del juego. La Memory Interface utiliza esta información para generar **KEY1**, la clave de cifrado. Usando el **algoritmo de cifrado Blowfish** en combinación con KEY1, la Memory Interface cifra los comandos adicionales enviados a la tarjeta [@anti_piracy-key1] [@anti_piracy-encrypt1].

A continuación, KEY1 se combina con el reloj interno y otros valores de la cabecera del cartucho para generar una nueva clave denominada **KEY2**. La diferencia fundamental respecto a KEY1 es que KEY2 incorpora aleatoriedad para resultar impredecible. Además, su cifrado se basa en múltiples operaciones XOR y desplazamientos de bits para ofuscar los datos [@anti_piracy-encrypt2].

KEY1 y KEY2 son utilizadas en distintas etapas por la interfaz del Slot-1 para cifrar la comunicación con la tarjeta. KEY1 también es utilizada por la BIOS del ARM7 para validar la tarjeta DS e inicializar la interfaz de la tarjeta.

#### Validación de la tarjeta DS {.tab}

Para poner este nuevo sistema en funcionamiento, la BIOS incluye rutinas que validan la tarjeta del Slot-1 durante el arranque. El proceso es el siguiente [@operating_system-boot]:

1. La BIOS del ARM7 recupera el ID del chip ROM de la tarjeta, lo guarda en RAM y procede a habilitar el cifrado KEY1 y KEY2.
2. Los primeros 2 KB del 'Área segura' se copian a la RAM en orden aleatorio. Los primeros 8 bytes de este bloque almacenan una cadena denominada **Secure Area ID**, y los valores siguientes contienen sumas de comprobación (tipo CRC16) y otros metadatos. Todo esto llega cifrado con KEY1, y el Secure Area ID está cifrado dos veces con KEY1 usando distintos parámetros.
3. La BIOS del ARM7 descifra el Secure Area ID y verifica que el resultado coincide con `encryObj`. Si es así, la tarjeta ha **superado la primera prueba**. Tras esto, la cadena se destruye para no revelar el algoritmo. Si la validación falla, los 2 KB del Área segura se rellenan con datos basura, impidiendo el acceso posterior a la tarjeta.
4. La segunda prueba consiste en recuperar de nuevo el ID del chip. El número de lecturas depende del reloj interno. Al final, si el valor del ID del chip coincide con el primero almacenado, **la segunda prueba se ha superado**.
5. Por último, el resto del Área segura se lee en orden aleatorio y se reconstruye en RAM. Tras esto, se ejecuta el firmware.

Si todo va bien, el firmware encontrará el ejecutable requerido de la tarjeta en RAM, lo que permitirá al usuario iniciar el juego. De lo contrario, la entrada del juego aparecerá atenuada.

#### Protección del Download Play {.tab}

Los programas recibidos mediante Download Play deben estar firmados por Nintendo mediante un **sistema de cifrado asimétrico** independiente; en concreto, RSA-1024 combinado con SHA-1 [@anti_piracy-downloadplay]. Por supuesto, solo Nintendo conoce la clave privada.

La verificación de la firma la realiza el firmware del sistema, que también almacena la clave pública.

### Derrota {.tabs-close}

Si eras un asiduo usuario de homebrew de DS entre 2007 y 2011, es muy probable que te hayas topado con interminables catálogos de software creado por la comunidad —desde emuladores y ports de juegos hasta reproductores multimedia—, así como con las múltiples herramientas disponibles para ejecutarlo. Lo cierto es que, antes de que se descubriesen los métodos más accesibles, los hackers lo pasaron muy mal para sortear el complejo sistema de Nintendo.

#### Slot-2 existente {.tabs .active}

Dado que el subsistema de GBA sigue ejecutando cartuchos sin ninguna protección significativa (más allá de los [trucos de marcas registradas](game-boy#anti-piracy)), los flashcarts de GBA existentes siguieron siendo compatibles con la Nintendo DS. Esto permitía ejecutar homebrew de GBA, lo que funcionaba bien si no te importaba prescindir de las nuevas funciones exclusivas de los títulos de DS.

Como siempre, los flashcarts también permitían a los usuarios ejecutar ROMs piratas. Sin embargo, dado que Nintendo no podía alterar el sistema de protección de la GBA (ya que hacerlo podría dejar inutilizables los juegos existentes), no se implementaron medidas adicionales.

#### Slot-2 mejorado {.tab}

![Ejemplo de flashcart para el Slot-2 capaz de ejecutar juegos de DS. Requiere un método de passthrough para arrancarla.](supercard.webp){.tab-float latex_width="90%"}

Desde que la consola llegó a las tiendas, personas de todo el mundo realizaron investigaciones clandestinas para descifrar las complejidades del Slot-1, la BIOS del ARM7 y el firmware.

En enero de 2005, Rafael Vuijk (conocido como `DarkFader`) publicó un método para inyectar código arbitrario en modo DS. Este método utilizaba una FPGA conectada entre el socket del Slot-1 y los pines de la tarjeta de *Metroid Prime Hunters: First Hunt* [@anti_piracy-vuijk]. En esencia, Vuijk descubrió que, si se manipulaban los valores de la cabecera, la ejecución desde el Slot-1 podía redirigirse al Slot-2 (donde vivían los flashcarts de GBA). Sin embargo, como el sistema de cifrado de la DS no se había comprendido del todo, esto requería una tarjeta DS original para superar las comprobaciones de validación iniciales. En cualquier caso, este modelo se denominó **DS Passthrough** y sirvió de base para ejecutar **programas del Slot-2 en modo DS**.

Los meses siguientes vieron proliferar en el mercado flashcarts para el Slot-2. Estas nuevas variantes incorporaban código ARM9 que se ejecutaba una vez arrancado desde el Slot-1. Mientras tanto, el propio arranque se lograba mediante diversos 'métodos de passthrough' descubiertos a lo largo del mismo año, entre ellos:

- **PassMe**: Diseñados originalmente por `Natrium42` [@anti_piracy-passme], eran tarjetas del Slot-1 de terceros que incluían circuitos integrados y una ranura para una tarjeta del Slot-1 original. Se trataba, en esencia, de una versión comercializada del invento de Vuijk para el usuario medio. Con el tiempo, varios fabricantes desarrollaron revisiones más pequeñas y sencillas.
- **WiFiMe**: Desarrollado por Tim Schuerewegen en abril de 2005, era un conjunto de herramientas que incluía un driver de Wi-Fi Ralink modificado y una utilidad de línea de comandos para Windows 2000 y XP. Mediante una tarjeta Wi-Fi compatible, un PC podía emitir un programa de DS personalizado que la consola podía descargar a través del Download Play [@anti_piracy-wifime]. Una vez ejecutado, redirigía la ejecución al Slot-2. Esta técnica aprovechaba que el firmware no comprobaba las firmas RSA de determinadas áreas del binario.
- **FlashMe**: Desarrollado por `Loopy` en abril de 2005 [@anti_piracy-flashme], este método abordaba los inconvenientes de los dos anteriores. Mediante un puente en el terminal `SL1` y la ejecución de un programa homebrew especialmente diseñado (arrancado mediante otro método), era posible modificar el firmware para que arrancase automáticamente el código del Slot-2 en modo DS.

Como era de esperar, los lotes posteriores de Nintendo DS se entregaron con firmwares actualizados que corregían estos trucos. Así, los hackers y fabricantes se volcaron en explotar deficiencias de la BIOS del ARM7 (mucho más difíciles de corregir). De ahí surgió el **PassMe2** [@anti_piracy-passthrough]. Aun así, el juego del gato y el ratón continuó hasta que una nueva revelación asestó un duro golpe a Nintendo.

#### Slot-1 nativo {.tab}

![La 'R4 DS' fue una de las primeras flashcards para el Slot-1 en llegar al mercado. Poco después, una avalancha de réplicas lo inundó.](r4.webp){.tab-float latex_width="90%"}

En enero de 2006, Martin Korth, desarrollador del emulador de Nintendo DS llamado 'NO$GBA', logró extraer la BIOS del ARM7 e hizo ingeniería inversa del sistema de seguridad del Slot-1 [@anti_piracy-passthrough]. Con ello, nuevas herramientas y documentación revelaron los verdaderos mecanismos de seguridad de la Nintendo DS. Como ya has visto en este artículo, el modelo de cifrado es efectivo hasta que se descubre la clave (esta es una debilidad de los sistemas de cifrado simétrico, a diferencia de los sistemas de cifrado asimétrico como RSA, donde una entidad diferente custodia la clave privada).

¿Qué ocurrió entonces? Pues bien, para empezar, las tarjetas PassMe fueron sustituidas rápidamente por tarjetas **NoPass**, que cumplían la misma función pero ya no requerían una tarjeta original insertada. La segunda gran disrupción llegó con una nueva oleada de **flashcards plug-and-play para el Slot-1**, que cargaban homebrew directamente desde el Slot-1 (ya fuera incluyendo una ranura para MicroSD o memoria interna regrabable).

Como el sistema de cifrado no podía modificarse sin introducir cambios incompatibles que afectasen a todos los juegos comerciales existentes, Nintendo perdió definitivamente esta batalla. La única alternativa viable era recurrir a la vía legal, tal como hizo con su consola anterior.

#### Reflexiones finales {.tabs-close}

A título personal, me parece asombroso lo sencillo que resultó adquirir y utilizar flashcards plug-and-play en comparación con consolas anteriores. En artículos anteriores he descrito que, si los usuarios querían ejecutar programas homebrew o juegos piratas, debían adentrarse en un proceso engorroso y lleno de pasos.

En el caso de la DS, sin embargo, las flashcards se vendían literalmente como juegos comerciales (aunque con un 'estilo' de marketing diferente), y apuesto a que debió de ser muy preocupante para los estudios ver con qué facilidad se podía recurrir a la piratería.

![Las flashcards para el Slot-1 fueron una industria muy activa entre 2007 y 2011. Estas son solo algunas de las muchas opciones disponibles. ¡Gracias al donante anónimo por enviármelas!](slot1_flashcards.webp)

Otro aspecto llamativo es la enorme cantidad de flashcards de marca (sin contar todos los clones) que aparecieron en el mercado. Si lo analizas desde una perspectiva técnica, el propósito de una flashcard es muy sencillo: redirigir los comandos a la tarjeta MicroSD (donde se almacena una ROM o un programa homebrew) [@anti_piracy-acekard]. Pero a medida que la industria avanzó, algunos fabricantes dieron pasos adicionales para diseñar un software más elaborado dentro de la flashcard (denominado 'kernel' o 'firmware'), o incluso integraron hardware bastante inusual. Por ejemplo, la 'N-Card' ofrecía almacenamiento integrado, mientras que la 'SuperCard DSTwo' incluía un Ingenic Jz4740 (una CPU compatible con [MIPS](playstation#cpu)) a 360 MHz [@anti_piracy-dstwo]. No he visto este nivel de pasión desde la era de los [cartuchos de expansión](super-nintendo#beyond-convention).

## ¡Esto es todo, amigos!

![Mi DS *actual*, usada para este estudio. En realidad vendí la primera en 2010... Me pregunto dónde estará.](myds.jpg)

¡Muy bien! Creo que he cubierto el 99 % de lo que quería contar...

Espero no haber sonado demasiado duro al criticar algunas secciones. No me malinterpretes, ¡sigo pensando que esta consola es una buena obra de ingeniería! Pero hay algunos defectos que me hacen preguntarme si se trata realmente de un compromiso rentable o de un fallo de diseño. Lo cierto es que eso no impidió que yo, con 11 años, quisiera una desesperadamente. Así que supongo que eso es prácticamente 'misión cumplida' para la empresa.

También me gustaría dar las gracias a mis amigos y a la comunidad de MelonDS por tomarse el tiempo de revisar el borrador y señalar *un montón* de correcciones. La Nintendo DS me permitió tratar muchos temas que llevaba tiempo queriendo escribir, y me temía que acabaría abarcando demasiado, pero espero que hayas disfrutado del artículo.

¡Hasta la próxima!  
Rodrigo
