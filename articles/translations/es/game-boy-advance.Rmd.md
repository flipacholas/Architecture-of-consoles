---
short_title: Arquitectura Game Boy Advance
long_title: Arquitectura de la Game Boy Advance
name: Game Boy Advance
date: 2019-08-18
release_date: 2001-03-21
generation: 6
subtitle: Nuevas asociaciones impulsadas por dos pilas AA
cover: gba
top_tabs:
  Model:
    file: international
    caption: "La Game Boy Advance original.<br>Lanzada el 21/03/2001 en Japón, el 11/06/2001 en América y el 22/06/2001 en Europa."
  Motherboard:
    caption: "Mostrando la revisión \"03\". Tenga en cuenta que \"AGB\" es el identificador del modelo Game Boy Advance.<br>La ranura para cartuchos y el amplificador de audio están en la parte posterior."
    extension: "jpg"
    bib_source: photography-gekkio
  Diagram:
    caption: "Cada bus de datos está etiquetado con su ancho.<br>El diseño mostrado del cartucho AGB no incluye un mapeador (ya que la nueva CPU es capaz de direccionar mucha más memoria), aunque los juegos con una ROM grande pueden seguir incluyendo uno."
#Historical
aliases:
  - /projects/consoles/game-boy-advance/
---

## Una introducción rápida

El diseño interno de la Game Boy Advance es bastante impresionante para tratarse de una consola portátil que funciona con dos pilas AA.

Esta consola continuará utilizando la GPU *distintiva* de Nintendo. Además, introducirá una CPU relativamente nueva de una empresa británica que ganará popularidad en los próximos años.

## {.supporting-imagery}

## CPU

La mayoría de los componentes se combinan en un único paquete denominado **CPU AGB**. Este paquete contiene dos CPU completamente diferentes:

- Un **Sharp SM83** funcionando a **8,4 o 4,2 MHz**: *¡Pero si es la misma CPU que se encuentra en Game Boy!* Se usa efectivamente para ejecutar juegos de Game Boy (**DMG**) y Game Boy Color (**CGB**). Aquí tienes [mi artículo anterior](game-boy) si quieres saber más sobre estas consolas.
- Un **ARM7TDMI** funcionando a **16,78 MHz**: este es el nuevo procesador en el que nos centraremos, con toda seguridad ejecuta juegos de Game Boy Advance.

Tenga en cuenta que ambas CPU **nunca funcionarán al mismo tiempo** ni realizarán ningún co-procesamiento extravagante. La **única** razón para incluir el *muy* antiguo Sharp es para **la retrocompatibilidad**.

Dicho esto, antes de describir el chip ARM, me parece útil empezar con la historia que hay detrás de esta marca de CPU.

### El milagro de Cambridge

La historia de los orígenes de la CPU ARM y su posterior ascenso a la fama es fascinante. Aquí encontramos una combinación de inversión pública, crecimiento exponencial, decisiones desacertadas y asociaciones de largo recorrido.

#### El auge de Acorn Computers {.tabs.active}

![Foto del BBC Micro con una caja de discos de 5¼ encima [@photography-bbc_micro], el primer disco es Elite.](bbc_micro.jpg) {.tab-float}

A finales de los años 70, Reino Unido se vio marcado por el inicio de una transición de una economía intervencionista a un mercado libre. En medio de esta tormenta, iniciativas con sede en Cambridge como **Acorn Computers**, junto con Sinclair y otros similares, estaban vendiendo kits de ordenador a laboratorios y aficionados. De manera similar a las empresas estadounidenses y japonesas, los ordenadores de Acorn dependían de la [CPU 6502](nes#cpu) y un dialecto propietario de BASIC.

Al entrar en los años 80, los intereses ministeriales dentro del nuevo gobierno británico llevaron a la creación de un proyecto para mejorar la alfabetización en informática en las escuelas [@cpu-clp]. Gracias al próximo 'Proton' de Acorn, la empresa obtuvo el contrato para construir un ordenador asequible que cumpliera con la visión del gobierno. El resultado fue el **BBC Micro** (apodado el 'Beeb'), que disfrutó de un éxito significativo entre escuelas, profesores y estudiantes. Dentro del Micro, Acorn incorporó una interfaz vanguardista **'Tube'** que podía ampliar el ordenador con un **segundo procesador**. Esto allanaría el camino para la próxima gran inversión de Acorn.

Durante el desarrollo de su próximo producto, esta vez enfocado a empresas, Acorn no encontró una CPU adecuada para suceder a la 6502. La presión por innovar contra la competencia japonesa y estadounidense, combinada con una planificación desafortunada, colocó a Acorn en una situación financiera complicada. Por lo tanto, se encargó a una nueva división de Acorn la producción de una CPU convincente. Para sortear las restricciones recientes de Acorn, el equipo de CPU basó su arquitectura en las enseñanzas de un artículo de investigación llamado **The Case for the Reduced Instruction Set Computer** [@cpu-patterson] y su prototipo, el **CPU RISC** [@cpu-furber]. Finalmente, en 1985, Acorn entregó la **CPU ARM1** como un módulo Tube para el BBC Micro, pero solo se comercializó para fines de I+D. No será hasta 1987, con la introducción del primer ordenador **Acorn Archimedes**, que los chips ARM (para entonces, la CPU ARM2) tomarían un papel central.

#### Una nueva empresa de CPU {.tab}

![Un modelo tardío del Newton... después de jugar con él.](newton.jpg) {.tab-float}

Durante la comercialización del Acorn Archimedes, Apple se sintió cautivada por las CPUs energéticamente eficientes de Acorn, pero la empresa estadounidense aún no estaba convencida de que la última ARM3 de Acorn fuera adecuada para el nuevo proyecto estrella de Apple, el **Newton**. Sin embargo, en lugar de alejarse (después de todo, Acorn era un competidor), ambos discutieron la posibilidad de evolucionar la ARM3 para cumplir los requisitos de Apple [@cpu-furber], a saber, **frecuencia de reloj flexible**, **[MMU](nintendo-64#memory-management) integrada** y **direccionamiento completo de 32 bits**.

Esta colaboración pronto se convirtió en una asociación donde Acorn, Apple y VLSI (fabricante de chips ARM) crearon una nueva empresa dedicada exclusivamente al desarrollo de CPUs ARM. Apple proporcionó la inversión (obteniendo el 43% de la participación), Acorn compartió su personal y VLSI se encargó de la fabricación. En 1990, **Advanced RISC Machines (ARM) Ltd** surgió con Robin Saxby como su presidente ejecutivo.

Años después, Apple finalmente lanzó el **Newton MessagePad** con un **ARM610**, uno de la siguiente generación de chips ARM que incorporaban las aportaciones de Apple. Mientras tanto, Acorn también lanzó el **RiscPC** utilizando las nuevas CPUs.

Ahora, mientras Acorn y Apple se demoraban en el mercado de ordenadores/dispositivos portátiles, ARM ideó un modelo de negocio radical. Manteniéndose al margen de la fabricación, la visión de Saxby consistía en **licenciar la propiedad intelectual de ARM**, en forma de **diseños de CPU** y su **conjunto de instrucciones** [@cpu-saxby]. Esto otorgó a ARM clientes más allá del ámbito de los ordenadores, como Texas Instruments [@cpu-arm_history], que más tarde conectó a la empresa con el emergente mercado móvil (culminando en el Nokia 6110) y las cajas decodificadoras. Los años siguientes verán la tecnología de ARM incorporada en miles de millones de dispositivos móviles [@cpu-arm_history].

### La asociación con Nintendo {.tabs-close}

De vuelta en Japón, y gracias al [análisis de Game Boy](game-boy), aprendimos que la estrategia de hardware de Nintendo para sistemas portátiles favorece un modelo de [System On a Chip (SoC)](game-boy#cpu). Esto ha permitido a la compañía ofuscar tecnología asequible de uso común y combinarla con desarrollos internos. Al hacerlo, la nueva consola podría ser única y competitiva.

![CPU AGB, que alberga la CPU ARM7TDMI (entre otros muchos componentes).](cpu_agb.png)

Afortunadamente, el modelo de licencias de ARM se ajustaba perfectamente a esas necesidades. Ambas empresas mantuvieron conversaciones desde 1994 (un año antes del lanzamiento del [Virtual Boy](virtual-boy)) a pesar de que no se materializó nada hasta muchos años después [@cpu-jaggar]. La razón era simple: los japoneses encontraron inviable la densidad de código de ARM y la necesidad de 32 cables de datos (algo que la [CPU del Virtual Boy](virtual-boy#cpu) ya había logrado evitar). No obstante, el nuevo diseñador de CPU de ARM - Dave Jaggar - respondió rápidamente con el **ARM7TDMI**, una nueva CPU que se centró en maximizar el rendimiento bajo restricciones de energía y almacenamiento. **Este fue un punto de inflexión para ARM**, ya que este nuevo producto no solo complació a Nintendo, sino que también llamó la atención de **Texas Instruments**, **Nokia** y el resto de los competidores en el ámbito de los teléfonos móviles.

No es sorprendente que, cuando Nintendo comenzó a trabajar en el sucesor del [Game Boy Color](game-boy#cpu), su elección de CPU se convirtió en el ARM7TDMI.

### El ARM7TDMI

Vamos ahora a profundizar en lo que ofrece este chip.

#### Manejar la CPU

Para empezar, la ARM7TDMI implementa el conjunto de instrucciones **ARMv4**, el sucesor del ARMv3. Esto implica:

- Un **diseño basado en RISC**: Como se explicó antes, las CPUs ARM han sido influenciadas por un artículo de la Universidad de California, Berkeley llamado 'The Case for the Reduced Instruction Set Computer' [@cpu-patterson]. Su investigación describe una serie de directrices para el diseño de procesadores escalables y defiende el uso de una [arquitectura de carga-almacenamiento](xbox#tab-1-4-cisc-or-risc), un tamaño de instrucción fijo y un gran archivo de registros. Muchos de estos estaban ausentes en el mercado de CPUs populares (es decir, [Intel 8086](xbox#cpu), [MOS 6502](nes#cpu), [Zilog Z80](master-system#cpu) y el [Motorola 68000](mega-drive-genesis#cpu)), pero influirían en el diseño de nuevas líneas de CPUs a lo largo de las décadas de los 80 y 90.
- **Ejecución condicional**: Una característica peculiar del ISA de ARM. Esencialmente, casi todas las instrucciones incorporan una condición que indica si deben ejecutarse. Típicamente, otras CPUs siguen el proceso de 'comparar y saltar' (también llamado 'ramificación') para controlar qué instrucciones debe ejecutar la CPU. Por el contrario, los programadores de ARM pueden insertar la condición en la propia instrucción. Esto es posible porque los primeros cuatro bits de los códigos de operación de ARM están reservados para una condición (es decir, `igual`, `no igual`, etc). En resumen, esto reduce la complejidad del código ARM, ya que la ejecución condicional proporciona un diseño más limpio para las rutinas, a diferencia de las bifurcaciones y la división de subrutinas. Además, esto también sirve como una solución para [peligros de control](playstation#delay-galore) (explicados en más detalle más adelante).
- Instrucciones de multiplicación de **32 bits** y **64 bits**: Una adición del ARM v4. Además, las operaciones de 64 bits generan el resultado en dos registros.

#### El paquete

Ahora que sabemos cómo los desarrolladores se comunican con este chip, vamos a ver qué hay dentro del silicio.

##### El núcleo {.tabs.active}

En términos de circuitería, el ARM7TDMI es una versión reducida del ARM710 con adiciones interesantes. El núcleo incluye \[@cpu-arm\] \[@cpu-furber\]:

- **16 registros de propósito general de 32 bits**: Aunque es un gran avance en comparación con los siete [registros de 8 bits del SM83/Game Boy](game-boy#the-cpu-core), esto es un compromiso con las directrices de RISC, que requerían treinta y dos registros de 32 bits en su lugar. Esto se debe a que ARM favoreció mantener un tamaño de silicio pequeño [@cpu-furber].
- **Bus de datos y ALU de 32 bits**: significa que puede mover y operar valores de 32 bits sin consumir ciclos adicionales.
- **Direccionamiento limpio de 32 bits**: Esto es parte de la aportación de Apple. Los tres primeros CPUs ARM usaban direcciones de memoria de 26 bits para optimizar el rendimiento (el Contador de Programa y el Registro de Estado combinados podían caber en una sola palabra de 32 bits) a cambio de capacidad de direccionamiento de memoria (se podía acceder hasta 64 MB de memoria). La serie ARM6 de seguimiento (con su ISA ARMv3) implementó lógica de direccionamiento de 32 bits, pero mantuvo un modo compatible hacia atrás para el código antiguo. Ahora, el ARM7TDMI (enfocado en la movilidad) desechó el modo de 26 bits y solo alberga lógica para direcciones de 32 bits (reduciendo la cantidad de silicio necesario).
- **Sin [Unidad de Gestión de Memoria (MMU)](nintendo-64#memory-management)**: Desde el ARM1, ARM proporcionó una solución MMU. Primero como el coprocesador 'MEMC', y luego integrado con el ARM610. Ahora, el ARM7TDMI parece ser el único en su serie que no proporciona ninguno, potencialmente debido a la falta de interés (los primeros dispositivos móviles no requerían memoria virtual sofisticada).
- **Sin caché**: Otro recorte de costes de este chip, ya que los chips ARM anteriores incluían algo de caché.

Finalmente, todo esto puede operar con una fuente de alimentación de **3 voltios** [@cpu-furber]. Esto es un evidente paso hacia la computación móvil, ya que los núcleos anteriores requerían una fuente de 5 V.

##### La canalización {.tab}

Desde su primera iteración, ARM ha implementado una **canalización en tres etapas** para ejecutar código. En otras palabras, la ejecución de las instrucciones se divide en tres pasos o *etapas*. La CPU busca, decodifica y ejecuta hasta tres instrucciones simultáneamente. Esto permite aprovechar al máximo los recursos de la CPU (lo que reduce el silicio inactivo) al mismo tiempo que aumenta el número de instrucciones ejecutadas por unidad de tiempo.

Al igual que dos [contemporáneos muy](playstation#delay-galore) [similares](sega-saturn#the-ramifications), las CPUs ARM son susceptibles a **peligros de datos**. Sin embargo, ni el programador ni el compilador lo notarán ya que, en este caso, la CPU detendrá automáticamente la canalización cada vez que sea necesario.

También están presentes los **peligros de control**, pero ARM los abordó con un enfoque eficiente llamado **anulación condicional**: Cada vez que una instrucción de rama está en la segunda etapa (`Decodificación`), la CPU calculará la condición de la rama [@cpu-mcmillan]. Basado en el resultado, si la rama debe ejecutarse, la CPU anulará automáticamente la instrucción de seguimiento (convirtiéndola en un relleno). Ahora, esto puede parecer ineficiente en comparación con el [enfoque de MIPS](playstation#delay-galore) (ya que un compilador MIPS puede insertar instrucciones útiles, no solo rellenos). Por lo tanto, aparte de la bifurcación, ARM proporciona **ejecución condicional**. Esta última convierte este diseño de canalización en una ventaja, ya que ARM puede decodificar una instrucción y calcular su condición incorporada en la misma etapa. Por lo tanto, en este caso, no se añadirán rellenos. Es por eso que la ejecución condicional es preferida sobre la bifurcación al programar para CPUs ARM [@cpu-armasm].

##### Exprimir el rendimiento {.tab}

Una de las desventajas de una arquitectura de carga-almacenamiento llevó a que el código de ARM fuera muy **disperso**. Competidores como x86 podían realizar las mismas tareas usando menores cantidades de código, requiriendo menos almacenamiento. En consecuencia, cuando Nintendo echó un vistazo al último diseño de ARM, el ARM7, no quedaron satisfechos con él. El tamaño de las instrucciones de ARM significaba que supuestos artilugios equipados con buses de 16 bits con memoria y almacenamiento limitados - todo para ahorrar costos y energía - harían que la CPU fuera ineficiente y congestionada. Afortunadamente, Dave Jaggar acababa de terminar de diseñar el ARM7 y no se rendiría todavía. Durante su viaje tras reunirse con Nintendo, se le ocurrió una solución: El **conjunto de instrucciones Thumb** [@cpu-jaggar].

Thumb es un subconjunto del conjunto de instrucciones ARM cuyas instrucciones están codificadas en **palabras de 16 bits** (en lugar de 32 bits) [@cpu-thomas]. Al ser de 16 bits, las instrucciones Thumb requieren **la mitad del ancho de bus** y ocupan **la mitad de la memoria**.

El principal compromiso es que **Thumb no ofrece ejecución condicional**, dependiendo en su lugar de las bifurcaciones. Además, sus códigos de operación de procesamiento de datos solo usan un formato de dos direcciones, en lugar de uno de tres direcciones, y solo tiene acceso a la mitad inferior del archivo de registros (por lo tanto, **solo hay disponibles ocho registros de propósito general**). En general, dado que las instrucciones Thumb solo ofrecen un subconjunto funcional de ARM, los desarrolladores pueden tener que escribir más instrucciones para conseguir el mismo efecto.

En la práctica, Thumb utiliza el 70% del espacio del código ARM. Para memoria de 16 bits de ancho, Thumb ejecuta *más rápido* que ARM. Si es necesario, las instrucciones ARM y Thumb pueden mezclarse en el mismo programa (lo que se denomina *interfuncionamiento*) para que los desarrolladores puedan elegir cuándo y dónde utilizar cada modo.

##### Los extras {.tab}

El ARM7TDMI es, en esencia, un núcleo compatible con ARMv3 con extras. A este último se hace referencia en su nombre (*TDMI*), lo que significa:

- **T** → **Thumb**: la inclusión del conjunto de instrucciones Thumb.
- **D** → **Extensiones de depuración**: proporcionan depuración JTAG.
- **M** → **Multiplicador mejorado**: los núcleos ARM anteriores necesitaban varios ciclos para calcular multiplicaciones completas de 32 bits, esta mejora los reduce a unos pocos.
- **I** → **Macrocélula EmbeddedICE**: habilita puntos de interrupción de hardware, puntos de vigilancia y permite detener el sistema mientras se depura código. Esto facilita el desarrollo de programas para esta CPU.

En conjunto, esto convierte al ARM7TDMI en una solución atractiva para dispositivos móviles e integrados.

### Áreas de memoria {.tabs-close}

La inclusión de Thumb en particular tuvo una fuerte influencia en el diseño final de esta consola. Nintendo mezcló buses de 16 y 32 bits entre sus distintos módulos para reducir costes, al tiempo que proporcionaba a los programadores los recursos necesarios para optimizar su código.

![Arquitectura de memoria del sistema.](memory.png)

La memoria utilizable de la Game Boy Advance se distribuye en las siguientes ubicaciones (ordenadas de más rápida a más lenta) [@cpu-vijn]:

- **IWRAM** (WRAM interna) → 32 bits con 32 KB: útil para almacenar instrucciones ARM.
- **VRAM** (RAM de vídeo) → 16 bits con 96 KB: aunque este área está dedicada a los datos gráficos (que se explican en la siguiente sección de este artículo), sigue encontrándose en el mapa de memoria de la CPU, por lo que los programadores pueden almacenar otros datos si la IWRAM no es suficiente.
- **EWRAM** (WRAM externa) → 16 bits con 256 KB: un chip independiente junto a la AGB de la CPU. Es óptima para almacenar instrucciones y datos sólo de Thumb en pequeños trozos. Por otro lado, el acceso al chip puede ser hasta seis veces más lento en comparación con la IWRAM.
- **Game PAK ROM** → 16 bits con tamaño variable: aquí se accede a la ROM del cartucho. Si bien puede proporcionar una de las velocidades más lentas, también se refleja en el mapa de memoria para administrar diferentes velocidades de acceso. Además, Nintendo instaló un **Búfer de precarga** que interconecta el cartucho para aliviar el bloqueo excesivo. Este componente almacena en caché de forma independiente direcciones continuas cuando la CPU no está accediendo al cartucho, puede contener hasta ocho palabras de 16 bits.
  - En la práctica, sin embargo, la CPU rara vez dejará que el búfer de precarga haga su trabajo. Ya que por defecto seguirá obteniendo instrucciones del cartucho para continuar la ejecución \[@cpu-pfau\] (de ahí que IWRAM y EWRAM sean tan críticas).
- **Game PAK RAM** → 8 bits con tamaño variable: es el área donde se accede a la RAM del cartucho (SRAM o Memoria Flash).
  - Se trata estrictamente de un bus de 8 bits (la CPU verá "basura" en los bits no utilizados) y, por este motivo, Nintendo afirma que sólo puede operar a través de sus librerías.

Aunque esta consola se comercializó como un sistema de 32 bits, la mayor parte de su memoria sólo es accesible a través de un bus de 16 bits, lo que significa que los juegos utilizarán mayoritariamente el conjunto de instrucciones Thumb para evitar gastar dos ciclos por búsqueda de instrucción. Sólo en circunstancias muy excepcionales (por ejemplo, necesidad de utilizar instrucciones que no se encuentran en Thumb y almacenarlas en IWRAM), los programadores se beneficiarán del conjunto de instrucciones ARM.

### Convirtiéndose en una Game Boy Color

Aparte de la inclusión de [hardware GBC](game-boy) (Sharp SM83, BIOS original, modos de audio y vídeo, ranura para cartuchos compatibles, etc.), hay dos funciones adicionales necesarias para que funcione la retrocompatibilidad.

Desde el punto de vista del hardware, la consola se basa en interruptores para detectar si hay un cartucho de Game Boy o Game Boy Color insertado. Un **detector de forma** en la ranura del cartucho identifica eficazmente el tipo de cartucho y permite a la CPU leer su estado. Se supone que algún componente de la CPU AGB lee ese valor y desconecta automáticamente el hardware que no se necesita en modo GBC.

Desde el lado del software, hay un registro especial de 16 bits llamado `REG_DISPCNT` que puede alterar muchas propiedades de la pantalla, pero uno de sus bits pone la consola en 'modo GBC' [@cpu-diaz]. Al principio, me costó entender exactamente cuando la GBA intenta actualizar este registro. Por suerte, algunos desarrolladores ayudaron a aclararlo:

> Creo que lo que ocurre durante el arranque GBC es que comprueba el interruptor (legible en REG_WAITCNT 0x4000204), hace el fundido (un fundido muy rápido, difícil de notar), y finalmente cambia a modo GBC (la BIOS escribe en REG_DISPCNT 0x4000000), parando el ARM7.
> 
> La única pieza que falta en el rompecabezas es qué pasaría si quitaras una parte de la carcasa del cartucho GBC para que el interruptor dejara de estar pulsado, y luego hicieras un cambio de modo por software al modo GBC. El modo multi-arranque podría ayudar en este caso. No estoy seguro de si es necesario pulsar el interruptor para que el bus de cartuchos de la GBC funcione correctamente, o si simplemente funciona. Estoy dispuesto a suponer que el interruptor es necesario para que el bus funcione, pero es sólo una suposición.
> 
> -- <cite>Dan Weiss (alias Dwedit, actual responsable de PocketNES y Goomba Color)</cite>

## Gráficos

Antes de comenzar, notarás que el sistema es una mezcla entre [SNES](super-nintendo.md#graphics) y [Game Boy](game-boy#graphics), el núcleo gráfico sigue siendo el conocido motor 2D llamado **PPU**. Recomiendo leer esos artículos antes de continuar, ya que voy a revisar muchos conceptos explicados previamente.

En comparación con las anteriores [Game Boy](game-boy), ahora tenemos una pantalla LCD que puede mostrar hasta **32.768 colores** (15 bits). Tiene una resolución de **240 x 160 píxeles** y una frecuencia de actualización de **~60 Hz**.

### Organizando el contenido

![Arquitectura de memoria de la PPU.](ppu.png)

Los gráficos se distribuyen a través estas regiones de memoria:

- 96 KB 16-bit **VRAM** (Video RAM): donde 64 KB almacenan fondos y 32 KB almacenan sprites.
- 1 KB 32-bit **OAM** (Memoria de Atributos de Objetos): almacena hasta 128 entradas de sprite (no los gráficos, sólo los índices y atributos). Su bus está optimizado para un renderizado rápido.
- 1 KB 16-bit **PAL RAM** (RAM de Paletas): almacena dos paletas, una para fondos y otra para sprites. Cada paleta contiene 256 entradas de colores de 15 bits cada una, siendo el color `0` *transparente*.

### Construyendo el frame

Si has leído los artículos anteriores, la GBA te resultará familiar, aunque hay funciones adicionales que pueden sorprenderte, y no olvides que esta consola funciona con dos pilas AA.

Voy a tomar prestados los gráficos del *Sonic Advance 3* de Sega para mostrar cómo se compone un fotograma.

#### Mosaicos {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Estos dos bloques están formados por mosaicos de 4 bpp.](sonic/tiles1.png){.active title="Bloques 1-2"}

![Puede que notes algunos patrones verticales extraños aquí, estos no son gráficos sino "Mapas de Mosaicos" (ver siguiente sección).](sonic/tiles2.png){title="Bloques 3-4"}

![Estos dos bloques están reservados para los sprites.](sonic/tilesobj.png){title="Bloques 5-6"}

Pares de bloques de cuadros encontrados en la VRAM.

:::

Los mosaicos de GBA son estrictamente **mapas de bits de 8x8 píxeles**, pueden usar 16 colores (4 bpp) o 256 colores (8 bpp). Los mosaicos de 4 bpp consumen 32 bytes, mientras que los de 8 bpp consumen 64 bytes.

Los mosaicos pueden almacenarse en cualquier lugar de la VRAM, sin embargo, la PPU los quiere agrupados en **bloques de cuadros**: Una región de **16 KB**. Cada bloque está reservado para un tipo específico de capa (fondo y sprites) y los programadores deciden dónde empieza cada bloque de cuadros. Esto puede dar lugar a cierto solapamiento que, como consecuencia, permite que dos bloques de cuadros compartan los mismos mosaicos.

Debido al tamaño de un bloque de cuadros, se pueden almacenar hasta 256 mosaicos de 8 bpp o 512 mosaicos de 4 bpp por bloque. Se permiten hasta seis bloques de cuadros, que combinados requieren 96 KB de memoria: exactamente la cantidad de VRAM que tiene esta consola.

Sólo se pueden utilizar cuatro bloques de cuadros para fondos y dos para sprites.

#### Fondos {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![Capa de fondo 0 (BG0).](sonic/bg0.png){.active title="Capa 0"}

![Capa de fondo 2 (BG2).](sonic/bg2.png){title="Capa 2"}

![Capa de fondo 3 (BG3).<br>Esta capa en particular se desplazará horizontalmente en determinadas líneas de exploración para simular efectos de agua.](sonic/bg3.png){title="Capa 3"}

Capas de fondo estático en uso.

:::

La capa de fondo de este sistema ha mejorado notablemente desde la Game Boy Color. Por fin incluye algunas características encontradas en la [Super Nintendo](super-nintendo) (¿recuerdas las [transformaciones afines](super-nintendo#características-únicas)?).

La PPU puede dibujar hasta cuatro capas de fondo. Las capacidades de cada uno dependerán del modo de funcionamiento seleccionado [@graphics-tiles]:

- **Modo 0**: proporciona cuatro capas estáticas.
- **Modo 1**: solo hay tres capas disponibles, aunque una de ellas es **afín** (puede girarse y/o escalarse).
- **Modo 2**: suministra dos capas afines.

Cada capa tiene una dimensión de hasta 512x512 píxeles. Si es una afín, entonces será de hasta 1024x1024 píxeles.

El dato que define la capa de fondo se llama **Mapa de mosaicos**. Para implementar esto de forma que la PPU lo entienda, los programadores utilizan **bloques de pantalla**, una estructura que define porciones de la capa de fondo (mosaicos de 32x32). Un bloque de pantalla ocupa sólo 2 KB, pero se necesitará más de uno para construir toda la capa. Los programadores pueden colocarlos en cualquier lugar dentro de los bloques de cuadros de fondo, ¡esto significa que no todas las entradas de las fichas contendrán gráficos!

#### Sprites {.tab}

![Capa de sprite renderizada](sonic/sprites.png) {.tab-float.pixel}

El tamaño de un sprite puede ser de hasta 64x64 píxeles de ancho, pero para tener una pantalla tan pequeña acabarán ocupando una gran parte de ella.

¡Por si fuera poco, ahora la PPU puede aplicar **transformaciones afines** a los sprites!

Las entradas del sprite tienen un ancho de 32 bits y sus valores pueden dividirse en dos grupos:

- **Atributos**: contiene posición x/y, volteo h/v, tamaño, forma (cuadrado o rectángulo), tipo de sprite (afín o regular) y ubicación del primer tile.
- **Datos afines**: Solo se utilizan si el sprite es afín. Especifican la escala y la rotación.

#### Resultado {.tab}

![Todas las capas combinadas (Tachán).](sonic/result.png) {.tab-float.pixel}

Como siempre, la PPU combinará todas las capas automáticamente, ¡pero aún no ha terminado! El sistema dispone de un par de efectos para aplicar sobre estas capas:

- **Mosaico**: consigue hacer que los tiles parezcan más *en bloque*.
- **Fusión alfa**: combina los colores de dos capas superpuestas dando lugar a efectos de transparencia.
- **Ventanas**: divide la pantalla en dos *ventanas* diferentes donde cada una puede tener sus propios gráficos y efectos independientes, la zona exterior de ambas ventanas también se puede renderizar con tiles.

Por otro lado, para actualizar el fotograma hay múltiples opciones disponibles:

- Órdenes de la **CPU**: el procesador tiene ahora acceso completo a la VRAM siempre que quiera. Sin embargo, puede producir artefactos no deseados si altera algunos datos a mitad de fotograma, por lo que esperar a VBlank/HBlank (*manera tradicional*) sigue siendo la opción más segura en la mayoría de los casos.
- Empleo del **Controlador DMA**: el DMA proporciona tasas de transferencia ~10x más rápidas y puede programarse durante VBlank y HBlank. Esta consola proporciona 4 canales DMA (dos reservados para sonido, uno para operaciones críticas y otro para propósito general). Tenga en cuenta que el controlador detendrá la CPU durante la operación (¡aunque es posible que apenas lo note!).

### Más allá de los mosaicos {.tabs-close}

A veces es posible que queramos componer un fondo a partir del cual el motor de mosaicos no pueda dibujar todos los gráficos necesarios. Ahora, las consolas modernas solucionaron esto implementando una arquitectura de **búfer de fotogramas**, pero esto no es posible cuando hay muy poca RAM... Pues bien, resulta que la GBA tiene 96 KB de VRAM que son suficientes para asignar un **mapa de bits** con las dimensiones de nuestra pantalla LCD.

La buena noticia es que la PPU realmente implementó esta funcionalidad incluyendo tres modos extra, estos se llaman **modos de mapa de bits** [@graphics-bitmap]:

- **Modo 3**: asigna una única trama a todo color (16 bpp, 32.768 colores).
- **Modo 4**: proporciona dos fotogramas con la mitad de colores (8 bpp, 256 colores) cada uno.
- **Modo 5**: hay dos cuadros totalmente coloreados con la mitad de tamaño cada uno (160x128 píxeles).

La razón de tener dos mapas de bits es permitir **pasar de página**: dibujar sobre un mapa de bits previamente mostrado puede exponer algunos artefactos extraños durante el proceso. Si en su lugar manipulamos otro, entonces ninguno de los fallos se mostrará al usuario. Una vez finalizado el segundo mapa de bits, la PPU puede actualizarse para que apunte al segundo, intercambiando efectivamente el fotograma visualizado.

::: {.subfigures .tabs-nested .open-float .tab-float .pixel}

![Super Monkey Ball Jr. (2002).<br>El modo Bitmap permitía a la CPU proporcionar algunos gráficos 3D rudimentarios para los escenarios.<br>Los objetos de primer plano son sprites (capa separada).](bitmap/monkey.png){.active title="3D"}

![Tonc's demo.<br>Mapa de bits renderizada con algunas primitivas.<br>Observa que la pantalla no muestra patrones significativos producidos por los motores de mosaicos.](bitmap/demo.png){title="Demo"}

![Nickelodeon's SpongeBob SquarePants.<br>Episodio distribuido como un cartucho de GBA Video (sufrió mucha compresión, claro).](bitmap/spongebob.png){title="Video"}

Ejemplos de programas que utilizan modos de mapa de bits.

:::

En general, parece una característica de vanguardia, sin embargo, la mayoría de los juegos se aferraron al motor de mosaicos. ¿Y por qué? Porque en la práctica **consume muchos recursos a la CPU**.

La CPU puede delegar la mayor parte de los cálculos en el chip gráfico. En cambio, el sistema de búfer de fotogramas que proporciona la PPU se limita a mostrar ese segmento de memoria como una **única capa de fondo**, lo que significa que se acabaron las transformaciones afines individuales, la superposición de capas o los efectos a menos que la CPU los compute. Además, el búfer de fotogramas ocupa 80 KB de memoria, por lo que sólo 16 KB (la mitad) están disponibles para almacenar mosaicos de sprites.

{.close-float}

Por esta razón, estos modos se usan excepcionalmente, como para reproducir vídeos en movimiento (**Game Boy Advance Video** dependía completamente de esto) o renderizar **geometría 3D** con la CPU. En cualquier caso, los resultados fueron, como mínimo, impresionantes.

## Audio

La GBA incorpora un **reproductor de muestras de 2 canales** que funciona en combinación con el sistema de sonido heredado de la Game Boy.

### Funcionalidad

Aquí tienes un desglose de cada componente de audio utilizando en *Sonic Advance 2* como ejemplo:

#### PCM {.tabs.active}

![Canales exclusivos de PCM.](pcm){.tab-float video="true"}

El nuevo sistema de sonido ahora puede reproducir muestras PCM, proporciona dos canales llamados **Sonido Directo** donde recibe muestras utilizando una **cola FIFO** (implementada como un búfer de 16 bytes).

Las muestras son de **8 bits** y **con signo** (codificadas en valores de -128 a 127). La frecuencia de muestreo por defecto es de 32 kHz, aunque esto depende de cada juego: como una frecuencia mayor implica un mayor tamaño y más ciclos de CPU, no todos los juegos gastarán la misma cantidad de recursos para alimentar el chip de audio.

El **DMA** es esencial para evitar atascos en los ciclos de la CPU. Los **temporizadores** también están disponibles para mantener el sincronismo con la cola.

#### PSG {.tab}

![Canales exclusivos de PSG.](psg){.tab-float video="true"}

Aunque el subsistema de la Game Boy no comparte su CPU, sí da acceso a su PSG. Por razones de compatibilidad, se trata del mismo diseño que el de la Game Boy original. Anteriormente he escrito [este artículo](game-boy#audio) que entra en detalle sobre cada canal en particular.

La mayoría de los juegos de GBA lo utilizaban como acompañamiento o efecto. Los posteriores optimizarán su música para PCM y dejarán el PSG sin utilizar.

#### Combinado {.tab}

![¡Tachán!](complete){.tab-float video="true"}

Por último, todo se mezcla automáticamente y se emite a través del jack de altavoces/auriculares.

Aunque la GBA sólo tiene dos canales PCM, algunos juegos pueden reproducir mágicamente más de dos muestras simultáneas. ¿Cómo es esto posible? Bueno, aunque sólo tener dos canales puede parecer un poco débil sobre el papel, la CPU principal puede utilizar algunos de sus ciclos para proporcionar tanto la secuenciación de audio como la mezcla \[@audio-programming\] (¡eso debería darte una idea de lo potente que es el ARM7!). Además, en la sección "Sistema operativo", ¡descubrirás que la ROM de la BIOS incluía un secuenciador de audio!

### Lo mejor de ambos mundos {.tabs-close}

Algunos juegos llevaban la dualidad PCM-PSG más allá y "alternaban" el chip protagonista en función del contexto.

En este juego (*Mother 3*), el jugador puede ingresar a dos habitaciones diferentes, una *relativamente normal* y otra con una configuración *nostálgica*. Dependiendo de la habitación en la que se encuentre el personaje, la misma partitura sonará *en estilo moderno* o *en estilo 8 bits*.

![Habitación normal, sólo utiliza PCM.](crackers/cinema){.toleft video="true"}

![Habitación nostálgica, el PSG lidera la sintonía.](crackers/8bit){.toright video="true"}

## Sistema Operativo

El vector de reinicio de ARM7 está en `0x00000000`, que apunta a la **ROM de 16 KB de la BIOS**. Esto significa que la Game Boy Advance arranca primero desde la BIOS, que a su vez muestra la icónica pantalla de inicio y luego decide si cargar el juego o no.

::: {.subfigures .side-by-side}

![Animación de inicio a mitad de camino.](splash/start.png) {.toleft.pixel}

![Animación de inicio al final.](splash/finish.png) {.toright.pixel}

La GBA cuenta con una nueva animación de inicio en la que ahora muestra su paleta de colores extendida y capacidades de escala de sprites.

:::

La ROM de la BIOS también almacena rutinas de software que los juegos pueden llamar para simplificar ciertas operaciones y reducir el tamaño del cartucho [@operating_system-vijn]. Entre ellas figuran:

- **Funciones aritméticas**: rutinas para realizar divisiones, raíces cuadradas y tangentes de arcos.
- **Cálculo de la matriz afín**: Dado un valor de "zoom" y un ángulo, calcula la matriz afín que se introducirá en la PPU para escalar/rotar un fondo o sprite.
  - Hay dos funciones, una para los sprites y otra para los fondos. Sus parámetros son ligeramente diferentes, pero la idea es la misma.
- **Funciones de descompresión**: implementa algoritmos de descompresión que incluyen Run-Length, LZ77 y Huffman. También proporciona desempaquetado de bits y diferencia secuencial.
- **Copia de memoria**: dos funciones que mueven la memoria de un lado a otro. La primera copia bloques de 32 bytes usando un código de operación especializado para este tipo de transferencia (`LDMIA` para cargar y `SDMIA` para almacenar) solo una vez. La segunda copia bloques de 2 o 4 bytes usando códigos de operación `LDRH/STRH` o `LDMIA/STMIA`, respectivamente. Por lo tanto, la segunda función es más flexible pero no tan rápida.
- **Sonido**: ¡implementa un completo secuenciador MIDI! Incluye muchas funciones para controlarlo.
- **Interfaz de energía**: accesos directos para reiniciar, borrar la mayor parte de la RAM, detener la CPU hasta que se produzca un determinado evento (V-blank o uno personalizado) o cambiar a "modo de bajo consumo".
- **Multi-boot**: carga un programa en otra GBA y lo arranca. Encontrará más detalles en la sección "Juegos".

La BIOS está conectada a través de un bus de 32 bits y se implementa utilizando una combinación de instrucciones ARM y Thumb, aunque esta última es la más destacada.

Además, recuerda que todo esto sólo funcionará en el ARM7. En otras palabras, no hay ninguna aceleración de hardware disponible para acelerar estas operaciones. De ahí que Nintendo proporcionara toda esta funcionalidad a través de software.

## Juegos

Los juegos se distribuyen en un nuevo formato de cartucho propietario, sigue llamándose **Game Pak**, pero presenta un diseño más pequeño.

![El nuevo diseño del Game Pak para juegos de GBA [@photography-amos].](gba_gamepak.png) {.no-borders}

Los programas de GBA se escriben principalmente en **C** con secciones de rendimiento crítico en **ensamblador** (ARM y Thumb) para ahorrar ciclos. Nintendo suministró un SDK con bibliotecas y compiladores.

La programación para la GBA compartió algunas metodologías con la [Super Nintendo](super-nintendo), pero también heredó todos los avances de principios de la década de los 2000: lenguajes de alto nivel estandarizados, compiladores fiables, CPUs RISC depurables, estaciones de trabajo no propietarias para el desarrollo, documentación comparativamente mejor y... ¡acceso a Internet!

### Acceso a los datos del cartucho

Aunque el ARM7 tiene un bus de direcciones de 32 bits, hay **solo 24 líneas de dirección conectadas al cartucho**.

Esto significa, en teoría, que se puede acceder a hasta 16 MB en el cartucho sin necesidad de un mapeador. Sin embargo, el mapa de memoria muestra que **32 MB de datos del cartucho son accesibles**. Entonces, ¿qué está pasando aquí? La verdad es que la Game Pak utiliza **direcciones de 25 bits** (lo que explica ese bloque de 32 MB) pero su bit más inferior está fijado a cero. Por lo tanto, únicamente los 24 bits restantes son manipulados. Así funciona el direccionamiento del Game Pak.

![Representación del modelo de direccionamiento del Game Pak. Fíjate en cómo el último bit de la dirección de 25 bits (llamado 'A0') siempre es cero. También debo señalar que en realidad, las patillas de direcciones y datos están compartidas/multiplexadas.](cart_addressing.png)

Ahora bien, ¿significa esto que los datos situados en direcciones impares (con su bit menos significativo a `1`) serán inaccesibles? No, porque el bus de datos es de 16 bits: en cada transferencia, la CPU/DMA obtendrá el byte localizado más el siguiente, lo que le permitirá leer tanto direcciones pares como impares. Como puede ver, se trata de otra obra de ingeniería que aprovecha al máximo las capacidades del hardware al tiempo que reduce los costes.

Curiosamente, las CPUs ARM de 26 bits también recurrían a la misma técnica. Estos albergaban un Contador de Programa de 24 bits, ya que los bits tenían que ser múltiplos de ocho (también conocido como alineados por palabra) por lo que los dos últimos bits de la dirección de 26 bits siempre eran ceros. Sin embargo, dado que estas CPUs recuperan 32 bits (el primer byte más los siguientes tres), se puede acceder a todo el espacio de direcciones de 26 bits.

### Memoria RAM del cartucho

Para guardar partidas, los Game Pak podrían incluir [@games-ziegler]:

- **SRAM**: necesitan una batería para mantener su contenido y pueden tener un tamaño de hasta 64 KB (aunque los juegos comerciales no superaban los 32 KB). Se accede a través del mapa de memoria de la GBA.
- **Flash ROM**: similar a la SRAM pero sin necesidad de batería, puede tener un tamaño de hasta 128 KB.
- **EEPROM**: requieren una conexión serie y teóricamente pueden tener cualquier tamaño (a menudo se encuentran de hasta 8 KB).

### Accesorios

Se incluye el mismo conector [Game Boy Link](game-boy#external-communications) para proporcionar capacidades multijugador o contenido adicional. Sin embargo, ya no hay sensor de infrarrojos, por alguna razón (tal vez demasiado poco fiable para grandes transferencias).

![El cable de conexión GameCube-Game Boy Advance [@photography-amos], este se conecta al puerto del controlador de la GameCube (manejado por la [Interfaz Serial](gamecube#internal-io)) para transferencias de datos.](link_cable.png) {.no-borders}

Además, la BIOS de la GBA implementaba internamente una función especial conocida como **Multi-boot**: Otra consola (ya sea GBA o GameCube) puede enviar un juego funcional a la EWRAM del receptor y entonces, este último arrancaría desde allí (en lugar del Game Pak).

## Anti-Piratería y Homebrew

En términos generales, el uso de cartuchos propietarios supuso una gran barrera en comparación con el constante juego del gato y el ratón al que tuvieron que enfrentarse otros fabricantes de consolas al utilizar el CD-ROM.

Para luchar contra los cartuchos *bootleg* (reproducciones no autorizadas), la BIOS de la GBA incorporaba [las mismas comprobaciones de arranque](game-boy#anti-piracy) que se encontraban en la Game Boy original.

### Flashcarts

A medida que el almacenamiento en estado sólido se hacía más asequible, aparecía en el mercado un nuevo tipo de cartucho. Los **flashcarts** parecían cartuchos normales pero tenían el añadido de una memoria regrabable o una ranura para tarjetas. Esto permitía a los usuarios reproducir archivos ROM de juegos dentro de la consola. En realidad, el concepto no es nuevo: los desarrolladores han utilizado internamente herramientas similares para probar sus juegos en una consola real (y los fabricantes proporcionaban el hardware necesario para ello).

Las soluciones anteriores incluían una memoria NOR Flash grabable (hasta 32 MB) y algunas SRAM respaldadas por baterías. Para cargar los binarios en el cartucho, el cartucho se suministraba con un cable Link-to-USB que se utilizaba con una GBA y un PC con Windows XP. Mediante el uso de software de flasheo y controladores patentados, el ordenador cargaba un programa de arranque múltiple en la GBA, que a su vez se utilizaba para transferir un binario de juego del PC al flashcart (insertado en la GBA). En general, toda la tarea de subir un juego se consideró *demasiado lenta*. Los Flashcarts posteriores (como el 'EZ-Flash') ofrecían mayor almacenamiento y la posibilidad de ser programados sin necesidad de la GBA como intermediario [@anti_piracy-ezflash]. Los últimos se basaban en almacenamiento extraíble (SD, MiniSD, MicroSD o lo que fuera).

La disponibilidad comercial de estas tarjetas resultó ser un **área legal gris**: Nintendo condenó su uso por permitir la piratería, mientras que algunos usuarios defendieron que era el único método para ejecutar **Homebrew** (programas realizados fuera de los estudios de juegos y, en consecuencia, sin la aprobación de Nintendo). El argumento de Nintendo se apoyaba en el hecho de que los flashers como el EZ-Writer ayudaban a los usuarios a parchear las ROM de los juegos para que pudieran ejecutarse en los cartuchos EZ-Flash sin problemas. Tras los intentos legales de Nintendo, estos cartuchos fueron prohibidos en algunos países (como el Reino Unido). No obstante, persistieron en todo el mundo.

## Esto es todo amigos

![Mi GBA y un par de juegos.<br>¡Lástima que no tenga retroiluminación!](mygba.png)
