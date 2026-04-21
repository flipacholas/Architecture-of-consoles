---
short_title: Arquitectura Game Boy Advance
long_title: Arquitectura de la Game Boy Advance
name: Game Boy Advance
date: 2019-08-18
release_date: 2001-03-21
generation: 6
subtitle: Tecnología a todo color impulsada por dos pilas AA
cover: gba
top_tabs:
  Models:
    - title: "Original"
      file: international
      caption: "La Game Boy Advance original.<br>Lanzada el 21/03/2001 en Japón, el 11/06/2001 en América y el 22/06/2001 en Europa."
      latex_height: 85
      active: true
    - title: "Clamshell"
      file: clamshell
      caption: "La Game Boy Advance SP.<br>Lanzada el 14/02/2003 en Japón, el 23/03/2003 en América y el 28/03/2003 en Europa."
      latex_height: 85
    - title: "Micro"
      file: micro
      caption: "La Game Boy Micro.<br>Lanzada el 13/09/2005 en Japón, el 19/09/2005 en América y el 04/11/2005 en Europa."
      latex_height: 85
  Motherboard:
    caption: "Mostrando el modelo original, revisión '03'. 'AGB' es el nombre en clave de la Game Boy Advance. El amplificador de audio está en la parte posterior."
    extension: "jpg"
    bib_source: copetti
    latex_height: 95
    marked_latex_height: 95
  Diagram:
    caption: "Cada bus de datos está etiquetado con su ancho. El diseño mostrado del cartucho AGB no incluye un mapeador (ya que la nueva CPU es capaz de direccionar mucha más memoria), aunque los juegos con una ROM grande pueden seguir incluyendo uno."
    latex_height: 92
# Historical
aliases:
  - /projects/consoles/game-boy-advance/
---

## Una introducción rápida

El diseño interno de la Game Boy Advance es bastante impresionante para tratarse de una consola portátil que funciona con dos pilas AA.

Esta consola seguirá utilizando el motor de sprites *característico* de Nintendo. Además, incorporará una CPU relativamente nueva de una empresa británica que ganaría una enorme popularidad en los años venideros.

### Modelos y variantes

La Game Boy Advance, como marca, abarca cuatro variantes. A modo de resumen:

- El modelo **original** estrenó la nueva arquitectura. Supuso una ruptura radical con la [serie Game Boy](game-boy), tanto en diseño como en componentes internos, al tiempo que mantenía la retrocompatibilidad.
- La variante 'Special' o **SP** introdujo mejoras externas de calidad de vida (batería recargable, diseño tipo clamshell y pantalla iluminada).
- La **Game Boy Micro** es una versión en miniatura con mejoras similares, aunque a costa de la retrocompatibilidad.
- El **Game Boy Player** es una versión modular que se acopla bajo la [GameCube](gamecube) mediante el [puerto de alta velocidad](gamecube#unusual-io). Se maneja a través de la GameCube y, gracias a que integra la mayor parte de los componentes internos de la consola original, ofrece compatibilidad nativa y retrocompatibilidad. Sin embargo, su forma peculiar la hace física y ergonómicamente incompatible con muchos accesorios.

Debo decir que la retrocompatibilidad con Game Boy es un tema fascinante, así que le he dedicado algunos párrafos al final de la sección sobre la CPU.

## {.supporting-imagery}

## CPU

La mayoría de los componentes se combinan en un único paquete denominado **CPU AGB**. Este contiene dos CPU completamente diferentes:

- Un **Sharp SM83** funcionando a **8,4 o 4,2 MHz**: *¡Si es la misma CPU de Game Boy!* Se usa para ejecutar juegos de Game Boy (**DMG**) y Game Boy Color (**CGB**). [Mi artículo anterior](game-boy) ofrece un resumen de la predecesora.
- Un **ARM7TDMI** funcionando a **16,78 MHz**: este es el nuevo procesador en el que nos centraremos; con toda seguridad ejecuta juegos de Game Boy Advance.

Ten en cuenta que ambas CPU **nunca funcionarán al mismo tiempo** ni realizarán ningún co-procesamiento extravagante. La **única** razón para incluir el *muy* antiguo Sharp es la **retrocompatibilidad**.

Dicho esto, adentrémonos en el chip ARM. Sin embargo, como es la primera CPU ARM que se trata en esta serie, déjame empezar por los antecedentes históricos de esta familia. ¡Prometo que vale la pena!

### El milagro de Cambridge

La historia de los orígenes de la CPU ARM y su posterior ascenso a la fama es fascinante. Aquí encontramos una combinación de inversión pública, crecimiento exponencial, decisiones desacertadas y asociaciones de largo recorrido.

#### El auge de Acorn Computers {.tabs .active}

::: {.subfigures .tabs-nested .tab-float}

![El BBC Micro (1981) con una caja de discos de 5¼ encima [@photography-bbc_micro], el primer disco incluye el juego *Elite* (1984).](bbc_micro.jpg){.active paperback_latex_width="79%" hardcover_latex_width="90%" title="BBC Micro"}

![La placa ARM Evaluation Board (1986), un módulo 'Tube' con una CPU ARM1. La encontré en The Centre for Computing History (Cambridge, Reino Unido).](arm_evaluation.webp){paperback_latex_width="79%" hardcover_latex_width="90%" title="ARM1"}

Los predecesores de la primera CPU ARM comercial, la ARM2.

:::

A finales de los años 70, Reino Unido vivía tiempos convulsos. La economía intervencionista construida sobre los ideales de posguerra había llegado a su fin, y el péndulo pronto osciló hacia las reformas de libre mercado. En medio de esta tormenta, iniciativas con sede en Cambridge como **Acorn Computers**, junto con Sinclair y otras similares, vendían kits de ordenador a laboratorios y aficionados. Al igual que las empresas estadounidenses y japonesas, los ordenadores de Acorn dependían de la [CPU 6502](nes#core-functionality) y un dialecto propietario de BASIC.

Al entrar en los años 80, los intereses ministeriales dentro del nuevo gobierno británico llevaron a la creación de un proyecto para mejorar la alfabetización en informática en las escuelas [@cpu-clp]. Gracias al próximo 'Proton' de Acorn, la empresa obtuvo el contrato para construir un ordenador asequible que cumpliera con la visión del gobierno. El resultado fue el **BBC Micro** (apodado el 'Beeb'), que disfrutó de un éxito significativo entre escuelas, profesores y estudiantes. Dentro del Micro, Acorn incorporó una interfaz vanguardista **'Tube'** que podía ampliar el ordenador con un **segundo procesador**. Esto allanaría el camino para la próxima gran inversión de Acorn.

Durante el desarrollo de su próximo producto, esta vez enfocado a empresas, Acorn no encontró una CPU adecuada para suceder a la 6502. La presión por innovar contra la competencia japonesa y estadounidense, combinada con una planificación desafortunada, colocó a Acorn en una situación financiera complicada. Por lo tanto, se encargó a una nueva división de Acorn la producción de una CPU convincente. Para sortear las restricciones recientes de Acorn, el equipo de CPU basó su arquitectura en las enseñanzas de un artículo de investigación llamado **The Case for the Reduced Instruction Set Computer** [@cpu-patterson] y su prototipo, el **CPU RISC** [@cpu-furber]. Finalmente, en 1985, Acorn entregó la **CPU ARM1** como un módulo Tube para el BBC Micro, pero solo se comercializó para fines de I+D. No sería hasta 1987, con la introducción del primer ordenador **Acorn Archimedes**, cuando los chips ARM (para entonces, la CPU ARM2) tomarían un papel central.

#### Una nueva empresa de CPU {.tab}

![Un modelo tardío del Newton... después de jugar con él.](newton.jpg){.tab-float}

Durante la comercialización del Acorn Archimedes, Apple se sintió cautivada por las CPUs energéticamente eficientes de Acorn, pero la empresa estadounidense aún no estaba convencida de que la última ARM3 de Acorn fuera adecuada para el nuevo proyecto estrella de Apple, el **Newton**. Sin embargo, en lugar de alejarse (después de todo, Acorn era un competidor), ambos exploraron la posibilidad de evolucionar la ARM3 para cumplir los requisitos de Apple [@cpu-furber], a saber, **frecuencia de reloj flexible**, **[MMU](nintendo-64#memory-management) integrada** y **direccionamiento completo de 32 bits**.

Esta colaboración pronto se convirtió en una asociación en la que Acorn, Apple y VLSI (fabricante de chips ARM) crearon una nueva empresa dedicada exclusivamente al desarrollo de CPUs ARM. Apple proporcionó la inversión (obteniendo el 43% de la participación), Acorn compartió su personal y VLSI se encargó de la fabricación. En 1990, **Advanced RISC Machines (ARM) Ltd** surgió con Robin Saxby como su presidente ejecutivo.

Años después, Apple finalmente lanzó el **Newton MessagePad** con un **ARM610**, perteneciente a la siguiente generación de chips ARM, moldeada por las aportaciones de Apple. Mientras tanto, Acorn también lanzó el **RiscPC** utilizando las nuevas CPUs.

Ahora, mientras Acorn y Apple seguían rezagados en el mercado de ordenadores y dispositivos portátiles, ARM ideó un modelo de negocio radical. Sin involucrarse en la fabricación, la visión de Saxby consistía en **licenciar la propiedad intelectual de ARM**, en forma de **diseños de CPU** y su **set de instrucciones** [@cpu-saxby]. Esto otorgó a ARM clientes más allá del ámbito de los ordenadores, como Texas Instruments [@cpu-arm_history], que más tarde conectó a la empresa con el emergente mercado móvil (culminando en el Nokia 6110) y las cajas decodificadoras. Los años siguientes verían la tecnología de ARM incorporada en miles de millones de dispositivos móviles [@cpu-arm_history].

### La asociación con Nintendo {.tabs-close}

De vuelta en Japón, y gracias al [análisis de Game Boy](game-boy), aprendimos que la estrategia de hardware de Nintendo para sistemas portátiles favorece un modelo de [System On a Chip (SoC)](game-boy#cpu). Esto ha permitido a la compañía camuflar tecnología de catálogo asequible y combinarla con desarrollos internos. Al hacerlo, la nueva consola podría ser única y competitiva. Afortunadamente, el modelo de licencias de ARM encajaba perfectamente con esas necesidades.

![El chip CPU AGB, que integra la CPU ARM7TDMI (entre otros muchos componentes).](cpu_agb.png){latex_width="90%"}

Ambas empresas mantuvieron conversaciones desde 1994 (un año antes del lanzamiento del [Virtual Boy](virtual-boy)), probablemente en el marco del 'Proyecto Atlantis', durante el cual Nintendo estudió la ARM710 [@cpu-atlantis]. Sin embargo, nada se materializó hasta muchos años después [@cpu-jaggar], y la razón era simple: los japoneses consideraban prohibitiva la densidad de código de ARM y la necesidad de 32 cables de datos (algo que la [CPU del Virtual Boy](virtual-boy#cpu) ya había logrado evitar). No obstante, el nuevo diseñador de CPU de ARM — Dave Jaggar — respondió rápidamente con el **ARM7TDMI**, un nuevo diseño centrado en maximizar el rendimiento bajo restricciones de energía y almacenamiento. **Este fue un punto de inflexión para ARM**, ya que este nuevo producto no solo complació a Nintendo, sino que también atrajo la atención de **Texas Instruments**, **Nokia** y otros competidores en el ámbito de la telefonía móvil.

No es de extrañar que, al comenzar Nintendo a trabajar en el sucesor del [Game Boy Color](game-boy#cpu), su elección recayera en el ARM7TDMI.

### El ARM7TDMI

Veamos ahora qué tiene para ofrecer este chip.

#### Comandando la CPU

Para empezar, el ARM7TDMI implementa el set de instrucciones **ARMv4**, el sucesor del ARMv3. Esto implica:

- Un **diseño basado en RISC**: Como se explicó antes, las CPUs ARM han sido influenciadas por un artículo de la Universidad de California, Berkeley llamado 'The Case for the Reduced Instruction Set Computer' [@cpu-patterson]. Su investigación describe una serie de directrices para el diseño de procesadores escalables y defiende el uso de una [arquitectura de carga-almacenamiento](xbox#tab-1-4-cisc-or-risc), un tamaño de instrucción fijo y un gran archivo de registros. Muchos de estos rasgos estaban ausentes en el concurrido mercado de CPUs de la época (es decir, [Intel 8086](xbox#cpu), [MOS 6502](nes#core-functionality), [Zilog Z80](master-system#cpu) y el [Motorola 68000](mega-drive-genesis#cpu)), pero influirían en el diseño de nuevas líneas de CPUs a lo largo de las décadas de los 80 y 90.
- **Ejecución condicional**: Una característica peculiar del ISA de ARM. Esencialmente, casi todas las instrucciones incorporan una condición que indica si deben ejecutarse. Por lo general, otras CPUs siguen el proceso de 'comparar y saltar' (también llamado 'bifurcación') para controlar qué instrucciones debe ejecutar la CPU. Por el contrario, los programadores de ARM pueden insertar la condición en la propia instrucción. Esto es posible porque los primeros cuatro bits de los opcodes de ARM están reservados para una condición (es decir, `igual`, `no igual`, etc.). En resumen, esto reduce la complejidad del código ARM, ya que la ejecución condicional proporciona un diseño más limpio para las rutinas, a diferencia de las bifurcaciones y la división de subrutinas. Además, sirve como solución a los [peligros de control](playstation#delay-galore) (explicados en más detalle más adelante).
- Un **segundo operando flexible**, también conocido como 'Operand2' [@cpu-operand2]. Por lo general, las operaciones se componen de dos operandos (como `suma 2 y 2`). Sin embargo, las instrucciones de ARM también permiten incorporar una operación adicional de desplazamiento (`shift`) en el segundo operando. Por ejemplo, es posible calcular `desplaza 2 cuatro bits y luego súmalo a 2` en una sola instrucción.
  - El desplazamiento de bits es también un recurso de bajo coste para realizar divisiones o multiplicaciones por potencias de 2, lo que abre la puerta a numerosas técnicas de optimización.
- Instrucciones de multiplicación de **32 bits** y **64 bits**: una novedad introducida en el ARMv4. Además, las operaciones de 64 bits almacenan el resultado en dos registros.

#### El paquete

Ahora que sabemos cómo los desarrolladores se comunican con este chip, vamos a ver qué hay dentro del silicio.

##### El núcleo {.tabs .active}

En términos de circuitería, el ARM7TDMI es una versión reducida del ARM710 con adiciones interesantes. El núcleo incluye [@cpu-arm] [@cpu-furber]:

- **16 registros de propósito general de 32 bits**: Aunque es un gran avance en comparación con los siete [registros de 8 bits del SM83/Game Boy](game-boy#the-cpu-core), esto supone un compromiso con las directrices RISC, que estipulan treinta y dos registros de 32 bits. Esto se debe a que ARM prefirió mantener un tamaño de chip reducido [@cpu-furber].
- **Bus de datos y ALU de 32 bits**: significa que puede mover y operar valores de 32 bits sin consumir ciclos adicionales.
- **Direccionamiento limpio de 32 bits**: Esto es parte de la aportación de Apple. Las tres primeras CPUs ARM usaban direcciones de memoria de 26 bits para optimizar el rendimiento (el Contador de Programa y el Registro de Estado combinados podían caber en una sola palabra de 32 bits) a cambio de capacidad de direccionamiento de memoria (se podía acceder a hasta 64 MB de memoria). La serie ARM6 posterior (con su ISA ARMv3) implementó lógica de direccionamiento de 32 bits, pero mantuvo un modo de compatibilidad con versiones anteriores para el código heredado. Ahora, el ARM7TDMI (enfocado en la movilidad) desechó el modo de 26 bits y solo alberga lógica para direcciones de 32 bits (reduciendo la cantidad de silicio necesario).
- **Sin [Unidad de Gestión de Memoria (MMU)](nintendo-64#memory-management)**: Desde el ARM1, ARM proporcionó una solución MMU. Primero como el coprocesador 'MEMC', y luego integrado con el ARM610. Ahora, el ARM7TDMI parece ser el único en su serie que no incorpora ninguna, posiblemente por falta de demanda (los primeros dispositivos móviles no requerían memoria virtual sofisticada).
- **Sin caché**: Otra medida de ahorro de costes en este chip, ya que los chips ARM anteriores incluían algo de caché.

Finalmente, todo esto puede operar con una fuente de alimentación de **3 voltios** [@cpu-furber]. Esto es un evidente paso hacia la computación móvil, ya que los núcleos anteriores requerían una fuente de 5 V.

##### El pipeline {.tab}

Desde su primera iteración, ARM ha implementado una **pipeline en tres etapas** para ejecutar código. En otras palabras, la ejecución de las instrucciones se divide en tres pasos o *etapas*. La CPU captura, decodifica y ejecuta hasta tres instrucciones simultáneamente. Esto permite aprovechar al máximo los recursos de la CPU (lo que reduce el silicio inactivo) al mismo tiempo que aumenta el número de instrucciones ejecutadas por unidad de tiempo.

Al igual que dos [contemporáneos muy](playstation#delay-galore) [similares](sega-saturn#the-ramifications), las CPUs ARM son susceptibles a **peligros de datos**. Sin embargo, ni el programador ni el compilador lo notarán ya que, en este caso, la CPU detendrá automáticamente la pipeline cada vez que sea necesario.

También están presentes los **peligros de control**, pero ARM los abordó con un enfoque eficiente llamado **anulación condicional**: Cada vez que una instrucción de rama está en la segunda etapa (`Decode`), la CPU calculará la condición de la rama [@cpu-mcmillan]. En función del resultado, si la rama debe ejecutarse, la CPU anulará automáticamente la instrucción posterior (convirtiéndola en un relleno). Ahora, esto puede parecer ineficiente en comparación con el [enfoque de MIPS](playstation#delay-galore) (ya que un compilador MIPS puede insertar instrucciones útiles, no solo rellenos). Por lo tanto, aparte de la bifurcación, ARM proporciona **ejecución condicional**. Esta última convierte este diseño de pipeline en una ventaja, ya que ARM puede decodificar una instrucción y calcular su condición incorporada en la misma etapa. Por lo tanto, en este caso, no se añadirán rellenos. De ahí que la ejecución condicional sea preferible a las bifurcaciones al programar para CPUs ARM [@cpu-armasm].

##### Exprimir el rendimiento {.tab}

Una de las desventajas de una arquitectura de carga-almacenamiento llevó a que el código de ARM fuera muy **disperso**. Competidores como x86 podían realizar las mismas tareas usando menores cantidades de código, requiriendo menos almacenamiento. En consecuencia, al examinar Nintendo el último diseño de ARM, el ARM7, este no les convenció. El tamaño de las instrucciones de ARM significaba que supuestos dispositivos con buses de 16 bits, memoria y almacenamiento limitados — todo para ahorrar costes y energía — harían que la CPU fuera ineficiente y crearían un cuello de botella. Por fortuna, Dave Jaggar acababa de terminar de diseñar el ARM7 y no estaba dispuesto a rendirse. Durante su camino de vuelta tras reunirse con Nintendo, se le ocurrió la solución: el **set de instrucciones Thumb** [@cpu-jaggar].

Thumb es un set de instrucciones independiente que funciona como modo alternativo dentro de la CPU. Está compuesto por un subconjunto del set de instrucciones ARM, con instrucciones codificadas en **palabras de 16 bits** (en lugar de 32 bits) [@cpu-thomas]. Al ser de 16 bits, las instrucciones Thumb requieren **la mitad del ancho de bus** y ocupan **la mitad de la memoria**. Para conseguirlo, introduce las siguientes concesiones:

- **Thumb no ofrece ejecución condicional**, dependiendo en su lugar de las bifurcaciones.
- Sus códigos de operación de procesamiento de datos usan un **formato de dos direcciones** (p. ej., `suma R1 a R3`), en lugar de uno de tres direcciones (p. ej., `suma R1 y R2 y almacena el resultado en R3`).
- Solo tiene acceso a la mitad inferior del archivo de registros. Por lo tanto, **solo hay disponibles ocho registros de propósito general**.

En general, dado que las instrucciones Thumb solo ofrecen un subconjunto funcional de ARM, los desarrolladores pueden tener que escribir más instrucciones para conseguir el mismo efecto. En la práctica, Thumb utiliza el 70% del espacio del código ARM. Para memoria de 16 bits de ancho, Thumb ejecuta *más rápido* que ARM. Si es necesario, las instrucciones ARM y Thumb pueden mezclarse en el mismo programa (lo que se denomina *interfuncionamiento*) para que los desarrolladores puedan elegir cuándo y dónde utilizar cada modo.

##### Los extras {.tab}

El ARM7TDMI es, en esencia, un núcleo compatible con ARMv3 con extras. El nombre (*TDMI*) hace referencia a estos extras:

- **T** → **Thumb**: la inclusión del set de instrucciones Thumb.
- **D** → **Extensiones de depuración**: proporcionan capacidades de depuración mediante la interfaz **Joint Test Action Group** (JTAG).
- **M** → **Multiplicador mejorado**: los núcleos ARM anteriores necesitaban varios ciclos para calcular multiplicaciones completas de 32 bits, esta mejora los reduce a unos pocos.
- **I** → **EmbeddedICE Macrocell**: habilita puntos de interrupción de hardware, puntos de vigilancia y permite detener el sistema mientras se depura código. Esto facilita el desarrollo de programas para esta CPU.

En conjunto, esto convierte al ARM7TDMI en una solución atractiva para dispositivos móviles e integrados.

### Áreas de memoria {.tabs-close}

La inclusión de Thumb en particular tuvo una fuerte influencia en el diseño final de esta consola. Nintendo mezcló buses de 16 y 32 bits entre sus distintos módulos para reducir costes, al tiempo que proporcionaba a los programadores los recursos necesarios para optimizar su código.

![Arquitectura de memoria del sistema.](_diagrams/memory.png)

La memoria utilizable de la Game Boy Advance se distribuye en las siguientes ubicaciones (ordenadas de más rápida a más lenta) [@cpu-vijn]:

- **IWRAM** (WRAM interna) → 32 bits con 32 KB: útil para almacenar instrucciones ARM.
- **VRAM** (RAM de vídeo) → 16 bits con 96 KB: aunque este bloque está dedicado a los datos gráficos (que se explican en la siguiente sección), también aparece en el mapa de memoria de la CPU. Por tanto, los programadores pueden almacenar otros datos aquí si la IWRAM no es suficiente.
- **EWRAM** (WRAM externa) → 16 bits con 256 KB: un chip independiente junto a la CPU AGB. Es óptima para almacenar instrucciones exclusivas de Thumb y bloques de datos pequeños. Por otro lado, el acceso al chip puede ser hasta seis veces más lento que el de la IWRAM.
- **Game PAK ROM** → 16 bits con tamaño variable: aquí se accede a la ROM del cartucho. Aunque es una de las fuentes más lentas, se refleja en el mapa de memoria para ofrecer distintas velocidades de acceso. Además, Nintendo instaló un **búfer de precarga** que sirve de interfaz con el cartucho para reducir los bloqueos y compensar la falta de caché en la CPU. Este componente almacena en caché de forma autónoma direcciones consecutivas cuando la CPU no accede al cartucho, y puede contener hasta ocho palabras de 16 bits.
  - En la práctica, sin embargo, la CPU rara vez deja que el búfer de precarga haga su trabajo, ya que por defecto sigue leyendo instrucciones del cartucho para mantener la ejecución [@cpu-pfau]. De ahí que IWRAM y EWRAM sean tan críticas.
- **Game PAK RAM** → 8 bits con tamaño variable: es el área donde se accede a la RAM del cartucho (SRAM o flash).
  - Se trata estrictamente de un bus de 8 bits (la CPU verá 'basura' en los bits no utilizados).

En definitiva, aunque esta consola se comercializó como un sistema de 32 bits, la mayor parte de su memoria solo es accesible a través de un bus de 16 bits. Esto significa que los juegos usan principalmente el set de instrucciones Thumb para evitar consumir dos ciclos por lectura de instrucción. Solo en circunstancias muy excepcionales — por ejemplo, al usar instrucciones no disponibles en Thumb o al leer desde la IWRAM — sacan partido del set de instrucciones ARM.

### Convirtiéndose en una Game Boy Color

Aparte de la inclusión de [hardware GBC](game-boy) (como la CPU Sharp SM83, la BIOS original, los modos de audio y vídeo, y una ranura de cartucho compatible), hay dos funciones adicionales necesarias para que la retrocompatibilidad funcione.

Desde el punto de vista del hardware, la consola se basa en un interruptor eléctrico para detectar si hay un cartucho de Game Boy o Game Boy Color insertado [@cpu-gbc_mode]. Un **detector de forma** en la ranura del cartucho identifica el tipo de cartucho y permite a la CPU ARM7 leer su estado y actuar en consecuencia. Además, la alimentación, junto con los buses del mando, el cartucho y la WRAM, se **redirigen** físicamente en función del estado de ese interruptor.

Desde el punto de vista del software, existe un registro especial de 16 bits llamado `REG_DISPCNT` que puede alterar muchas propiedades de la pantalla, pero uno de sus bits pone la consola en 'modo GBC' [@cpu-diaz]. Esto hace que el sistema ceda el control a la CPU SM83 y arranque la [BIOS de GBC](game-boy#operating-system). Eso sí, activar el modo GBC en `REG_DISPCNT` solo funciona si el sistema está arrancando desde la BIOS de GBA, ya que esta comprueba el valor del contador de programa (el registro `PC`).

Un aspecto curioso documentado en la Gbdev Wiki y en GBATek es que **todas las variantes de Game Boy Advance son técnicamente capaces de ejecutar código de Game Boy** [@cpu-gbc_mode] [@cpu-korth]. Esto incluye a la Game Boy Micro, que carece de la ranura de cartucho heredada. La razón es que todas las variantes integran el mismo SoC y no necesitan estrictamente el detector de forma para entrar en modo GB. Además, manipulando otros registros, es posible eludir las [rutinas de verificación de la GB](game-boy#operating-system). Sin embargo, a falta de un cartucho GB, el juego debe residir de algún modo en la VRAM (ya que la CPU leerá ceros hasta llegar a ella), y el mando queda inutilizable (pues el detector de forma debe conmutarse para redirigir el bus al SM83) [@cpu-sonos_notes].

## Gráficos

Antes de entrar en materia, conviene saber que las capacidades gráficas son una mezcla de elementos de la [Super Nintendo](super-nintendo#graphics) y la [Game Boy](game-boy#graphics). De hecho, el nuevo núcleo gráfico sigue llamándose **PPU**. Por ello, recomiendo leer esos artículos antes de continuar, ya que retomaré muchos conceptos explicados anteriormente.

![Comparación de resolución de pantalla y relación de aspecto entre la serie Game Boy y la Game Boy Advance.](_diagrams/screen_comparison.png)

En comparación con las anteriores [Game Boy](game-boy), ahora disponemos de una pantalla LCD con una gama de colores más rica, capaz de mostrar hasta **32.768 colores** (15 bits). Tiene una resolución de **240 × 160 píxeles** (lo que da a los juegos un aspecto panorámico) y una frecuencia de actualización de **~60 Hz**. Me pregunto si la nueva relación de aspecto fue una decisión pensada para favorecer los juegos de plataformas.

### Organizando el contenido

![Arquitectura de memoria de la PPU.](_diagrams/ppu.png)

Los datos gráficos se distribuyen entre estas regiones de memoria:

- 96 KB 16-bit **VRAM** (Video RAM): 64 KB almacenan fondos y 32 KB almacenan sprites.
- 1 KB 32-bit **OAM** (Object Attribute Memory): alberga hasta 128 entradas de sprite (no los gráficos, solo los índices y atributos). Como indica su amplio bus, la OAM está optimizada exclusivamente para un acceso rápido.
- 1 KB 16-bit **PAL RAM** (Palette RAM): contiene dos paletas grandes, una para fondos y otra para sprites. Cada paleta contiene 256 entradas de colores de 15 bits, donde el color `0` equivale a *transparente*.

### Construyendo el frame

Si has leído los artículos anteriores, la GBA te resultará familiar, aunque hay funciones adicionales que pueden sorprenderte. En cualquier caso, el hecho de que este nuevo sistema funcione con solo dos pilas AA hace que su estudio resulte aún más fascinante.

Tomaré prestados los gráficos de *Sonic Advance 3* de Sega para mostrar cómo se compone un frame.

#### Mosaicos {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2}

![Estos dos bloques están formados por tiles de 4 bpp.](sonic/tiles1.png){.active .pixel .border hardcover_latex_width="64%" paperback_latex_width="58%" title="Bloques 1-2"}

![Puede que notes algunos patrones verticales extraños aquí; no son gráficos sino 'Tile Maps' (explicados en la siguiente sección).](sonic/tiles2.png){.pixel .border hardcover_latex_width="64%" paperback_latex_width="58%" title="Bloques 3-4"}

![Estos dos bloques están reservados para los sprites.](sonic/tilesobj.png){.pixel .border hardcover_latex_width="64%" paperback_latex_width="58%" title="Bloques 5-6"}

Pares de charblocks encontrados en la VRAM.

:::

Los tiles de la GBA siguen siendo **bitmaps de 8×8 píxeles**, pero ahora pueden usar 16 colores (4 bpp) o 256 colores (8 bpp). Los tiles de 4 bpp consumen 32 bytes, mientras que los de 8 bpp consumen 64 bytes.

Los tiles pueden almacenarse en cualquier lugar de la VRAM. Sin embargo, la PPU espera que estén agrupados en **charblocks** — regiones continuas de **16 KB**. Cada charblock está reservado para un tipo específico de capa (fondo o sprites), y los programadores deciden dónde empieza cada uno. Esto puede dar lugar a cierto solapamiento que, a su vez, permite que dos charblocks compartan los mismos tiles.

Dado el tamaño de un charblock, se pueden almacenar hasta 256 tiles de 8 bpp o 512 tiles de 4 bpp por bloque. En total, **se pueden asignar hasta seis charblocks**, que en conjunto requieren 96 KB de memoria — exactamente la cantidad de VRAM que tiene esta consola.

Por último, solo cuatro charblocks pueden usarse para fondos, mientras que dos quedan reservados para sprites.

#### Fondos {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=2}

![Capa de fondo 0 (BG0).](sonic/bg0.png){.active .pixel .border hardcover_latex_width="65%" paperback_latex_width="58%" title="Capa 0"}

![Capa de fondo 2 (BG2).](sonic/bg2.png){.pixel .border hardcover_latex_width="65%" paperback_latex_width="58%" title="Capa 2"}

![Capa de fondo 3 (BG3). Esta capa en particular se desplazará horizontalmente en determinadas líneas de escaneo para simular efectos de agua.](sonic/bg3.png){.pixel .border hardcover_latex_width="65%" paperback_latex_width="58%" title="Capa 3"}

Capas de fondo estático en uso.

:::

La capa de fondo de este sistema ha mejorado notablemente desde la Game Boy Color. Por fin incluye algunas características encontradas en la [Super Nintendo](super-nintendo) (¿recuerdas las [transformaciones afines](super-nintendo#that-feature)?).

La PPU puede dibujar hasta cuatro capas de fondo. Las capacidades de cada una dependerán del modo de funcionamiento seleccionado [@graphics-tiles]:

- **Modo 0**: proporciona cuatro capas estáticas.
- **Modo 1**: solo hay tres capas disponibles, aunque una de ellas es **afín** (puede girarse o escalarse).
- **Modo 2**: suministra dos capas afines.

Cada capa tiene una dimensión de hasta 512×512 píxeles. En caso de ser afín, la dimensión máxima es de 1024×1024 píxeles.

La estructura de datos que define la capa de fondo sigue llamándose **Tile Map**. Ahora bien, esta información se codifica en forma de **screenblocks** — estructuras que definen porciones de la capa de fondo (32×32 tiles). Un screenblock ocupa solo 2 KB, aunque se necesitarán varios para construir toda la capa. Los programadores pueden colocarlos en cualquier lugar de la VRAM, potencialmente solapándolos con los charblocks de fondo donde residen los tiles. Esto significa que ¡no todas las entradas de tiles contendrán gráficos!

#### Sprites {.tab}

![Capa de sprites renderizada.](sonic/sprites.png){.tab-float .pixel .border}

Un sprite puede tener hasta 64×64 píxeles. Sin embargo, teniendo en cuenta la pantalla de 240×160 píxeles, acabarán ocupando una parte considerable de ella.

¡Por si fuera poco, la PPU ahora puede aplicar **transformaciones afines** a los sprites! En concreto, rotación y escalado.

Las entradas del sprite tienen un ancho de 32 bits y sus propiedades se dividen en dos grupos:

- **Atributos**: incluyen posición x e y, inversión horizontal y vertical, tamaño, forma (cuadrado o rectángulo), tipo de sprite (afín o regular) y la ubicación del primer tile.
- **Datos afines**: solo se necesitan para sprites afines. Especifican el escalado y la rotación [@graphics-affine].
  - Con el escalado, los sprites pueden llegar a ocupar hasta 128×128 píxeles en pantalla.

#### Resultado {.tab}

![Todas las capas combinadas (_¡Tachán!_).](sonic/result.png){.tab-float .pixel .border}

Como siempre, la PPU combina y renderiza todas las capas automáticamente, ¡pero aún no ha terminado! El sistema dispone de varios efectos para aplicar sobre estas capas [@graphics-effects]:

- **Mosaico**: hace que los tiles parezcan más *pixelados*.
- **Fusión de color**: combina los colores de dos grupos de capas superpuestas. La opacidad de cada grupo puede controlarse para lograr distintos grados de **transparencia**. Alternativamente, el segundo grupo puede fijarse en negro o blanco sólido, produciendo **efectos de fundido**.
- **Ventanas**: divide la pantalla en tres áreas rectangulares separadas (llamadas 'ventanas'). Estas pueden solaparse, y cada una tiene sus propios gráficos y controles de fusión. Dos ventanas tienen posición programable, mientras que la tercera es el complemento de las dos primeras (es decir, el resto de la pantalla no cubierto por ellas).

Debo decir que estos efectos recuerdan mucho a [los de la época de la Super Nintendo](super-nintendo#more-colour-magic).

Del mismo modo, para actualizar el frame hay varias opciones disponibles:

- Comandar la **CPU**: el procesador tiene ahora acceso completo a la VRAM en cualquier momento. Sin embargo, puede producir artefactos no deseados si altera datos a mitad de frame, por lo que esperar al VBlank/HBlank (*la forma tradicional*) sigue siendo la opción más segura en la mayoría de los casos.
- Usar el **Controlador DMA**: el DMA ofrece tasas de transferencia unas diez veces más rápidas y puede programarse durante el VBlank y el HBlank. Esta consola dispone de cuatro canales DMA (dos reservados para sonido, uno para operaciones críticas y otro de propósito general). Ten en cuenta que el controlador detiene la CPU durante la operación, aunque si se usa correctamente, la CPU apenas lo notará.

### Más allá de los mosaicos {.tabs-close}

A veces, los artistas diseñan un fondo cuyos gráficos el motor de tiles no puede dibujar en su totalidad. Las consolas modernas solucionaron esto implementando una arquitectura de **frame buffer**, lo que permite a los programadores alterar cada píxel de forma arbitraria e individual. Sin embargo, esto no es posible cuando hay muy poca RAM... Pues bien, resulta que la GBA tiene 96 KB de VRAM que son suficientes para asignar un **bitmap** con las dimensiones de nuestra pantalla LCD.

La buena noticia es que la PPU realmente implementó esta funcionalidad incluyendo tres modos extra, que se denominan **modos bitmap** [@graphics-bitmap]:

- **Modo 3**: asigna un único frame a todo color (16 bpp, 32.768 colores).
- **Modo 4**: proporciona dos frames, cada uno con la mitad de profundidad de color (8 bpp, 256 colores).
- **Modo 5**: ofrece dos frames a todo color con la mitad de tamaño cada uno (160×128 píxeles).

La razón de ofrecer dos bitmaps es permitir la **conmutación de páginas**: modificar un bitmap mientras se está visualizando puede provocar artefactos no deseados. Si en su lugar la CPU manipula un segundo bitmap, ninguno de esos artefactos será visible al usuario. Una vez terminado, la PPU puede actualizarse para apuntar al segundo, intercambiando así el frame visualizado.

::: {.subfigures .tabs-nested .open-float .tab-float .pixel}

![Super Monkey Ball Jr. (2002). El modo bitmap permitía a la CPU renderizar gráficos 3D rudimentarios para los escenarios, mientras que los objetos en primer plano se gestionaban como sprites (una capa separada).](bitmap/monkey.png){.active .border hardcover_latex_width="95%" paperback_latex_width="83%" title="3D"}

![Demo bitmap de Tonc (homebrew). Observa que la pantalla no muestra los patrones típicos producidos por los motores de tiles.](bitmap/demo.png){.border hardcover_latex_width="95%" paperback_latex_width="83%" title="Demo"}

![SpongeBob SquarePants de Nickelodeon (distribuido como cartucho *GBA Video*). Para adaptarse al medio, sufrió una fuerte compresión.](bitmap/spongebob.png){.border hardcover_latex_width="95%" paperback_latex_width="83%" title="Video"}

Ejemplos de programas que utilizan modos bitmap.

:::

En definitiva, parece una característica de vanguardia; sin embargo, la mayoría de los juegos se aferraron al motor de tiles. ¿Y por qué? Porque en la práctica **los bitmaps consumen muchos recursos de CPU**.

La CPU puede delegar la mayor parte de los cálculos en el chip gráfico. En cambio, el sistema de frame buffer que proporciona la PPU se limita a mostrar ese segmento de memoria como una **única capa de fondo**, lo que significa que se acabaron las transformaciones afines individuales, la superposición de capas o los efectos a menos que la CPU los compute. Además, el frame buffer ocupa 80 KB de memoria, por lo que solo 16 KB (la mitad) están disponibles para almacenar tiles de sprites.

{.close-float}

Por esta razón, estos modos resultaron de utilidad principalmente en casos excepcionales, como reproducir vídeo en movimiento (la serie **Game Boy Advance Video** dependía completamente de esto) o mostrar **geometría 3D** renderizada por la CPU. En cualquier caso, los resultados fueron innegablemente impresionantes.

#### Capacidades ocultas

Hasta aquí llegan las prestaciones oficiales, pero en el terreno no documentado existen controles ocultos que quedaron instalados y sugieren capacidades adicionales de la Game Boy Advance, quizás concebidas en algún momento:

- Por ejemplo, un flag dentro del registro `DISPCNT` (que afecta al controlador LCD) habilita un modo denominado 'green swap' [@cpu-korth]. Al activarlo, el controlador de pantalla intercambia el canal verde de pares de píxeles contiguos. No queda claro por qué Nintendo implementó esto.
- Para añadir más misterio, activar green swap también desbloquea otros flags en los mapas de fondos y sprites. Estos instruyen a la PPU para que renderice únicamente las columnas pares o impares de cada mapa [@cpu-sonos_notes].

En conjunto, parecen piezas de un rompecabezas mayor, quizás orientado a implementar una [función estereoscópica](virtual-boy#display) que quedó a medias. Sea como fuere, una entrevista anterior con Satoru Iwata sugiere que la Game Boy Advance SP se planeó en un principio con pantalla estereoscópica [@cpu-iwata], pero la pérdida de resolución retrasó el plan hasta la llegada de la [Nintendo 3DS](nintendo-3ds), ocho años después.

## Audio

Se acabaron los sonidos repetitivos. La GBA cuenta ahora con un **sintetizador de muestras de 2 canales** capaz de reproducir ricas secuencias de audio. Por si fuera poco, el sistema de sonido original de la Game Boy también sigue disponible.

### Funcionalidad

Aquí tienes un desglose de cada componente de audio, tomando *Sonic Advance 2* como ejemplo:

#### Direct Sound {.tabs .active}

![Osciloscopio de los canales PCM.](pcm){.tab-float .negate .border latex_width="90%" video="true"}

Como se indicó antes, la GBA incorpora un sistema de audio renovado llamado **Direct Sound**. Este reproduce **muestras PCM** (del inglés *Pulse-Code Modulation*) estéreo [@cpu-korth]; es decir, grabaciones directas que ya no están limitadas a un [conjunto predefinido de waveforms](game-boy#audio). Los datos de audio se alimentan a una cola **FIFO** (del inglés *First-In-First-Out*) de 16 bytes, y un **Conversor Digital-Analógico** (DAC, del inglés *Digital-to-Analogue Converter*) los convierte en sonido audible.

Ahora bien, las muestras de audio no alcanzan la calidad del CD, ya que la consola debe seguir siendo asequible. El DAC solo puede operar con **muestras de 8 bits con signo** (es decir, valores entre -128 y 127), la mitad de la resolución del audio de CD (16 bits), lo que introduce un ruido considerable. No obstante, los juegos pueden elegir la frecuencia de muestreo: el límite técnico es de 65 kHz (para referencia, el audio de CD es de 44,1 kHz), aunque las frecuencias más altas se consiguen a costa de la resolución de las muestras [@audio-jsgroth]. En cualquier caso, todas estas opciones exigen cartuchos de mayor capacidad y más ciclos de CPU, por lo que no todos los juegos destinarán los mismos recursos a alimentar el circuito de audio.

Para evitar cuellos de botella en la CPU, cada canal de audio incluye una unidad de **Acceso Directo a Memoria** (DMA, del inglés *Direct Memory Access*) y un **temporizador**, que se encargan de transferir los datos de audio y mantener la cola sincronizada, respectivamente.

#### PSG {.tab}

![Canales exclusivos de PSG.](psg){.tab-float .negate .border latex_width="90%" video="true"}

Aunque el subsistema de la Game Boy no comparte su CPU, sí da acceso a su **Generador de Sonido Programable** (PSG, del inglés *Programmable Sound Generator*) heredado.

Por razones de compatibilidad, se trata del mismo diseño que el de la Game Boy original; ya lo describí en detalle en [el artículo correspondiente](game-boy#audio) y analicé cada canal en particular.

Ahora bien, su cometido es complementar al Direct Sound. La mayoría de los juegos de GBA lo usaban como acompañamiento o para efectos de sonido. Aunque muchos también optimizaron su música para los nuevos canales y dejaron el PSG sin usar.

#### Combinado {.tab}

![Resultado final. *¡Tachán!*](complete){.tab-float .negate .border latex_width="85%" video="true"}

Al final del proceso, todos los canales se mezclan automáticamente y se emiten a través del **altavoz mono** y el **jack de auriculares estéreo**.

Retomando los canales de Direct Sound, aún no he explicado de dónde proceden los datos PCM. A diferencia de algunas consolas de sobremesa de la [quinta generación](sega-saturn#audio) capaces de reproducir muestras de alta fidelidad, la GBA no dispone ni del soporte de almacenamiento ni de los recursos de hardware necesarios para transmitir música en calidad de CD. Entonces, ¿cómo sacan partido los juegos de este sistema? Pues bien, al igual que la [Nintendo 64](nintendo-64#audio), recurren a la **secuenciación de música**: los juegos almacenan pequeños datos de instrumentos, y la CPU principal cede parte de sus ciclos para secuenciar y mezclar melodías en tiempo real (similar al MIDI). El resultado se transmite de forma continua a los canales de muestras [@audio-programming].

Por cierto, esta técnica no fue una ocurrencia tardía: en la sección 'Sistema operativo' de este artículo descubrirás que la **ROM de la BIOS ya incluye un secuenciador de audio** para ayudar a los desarrolladores con esta tarea.

En definitiva, esto debería darte una idea de lo potente que es el ARM7TDMI.

### Lo mejor de ambos mundos {.tabs-close}

Aunque la diversidad del subsistema de audio puede suponer un reto para los compositores, algunos juegos acabaron aprovechando la dualidad PCM-PSG y "alternaban" el chip protagonista según el contexto.

En *Mother 3*, por ejemplo, el jugador puede entrar en dos habitaciones distintas: una relativamente normal y otra con una ambientación nostálgica. Dependiendo de la habitación en la que se encuentre el personaje, la misma partitura sonará *en estilo moderno* o *en estilo de 8 bits*.

::: {.subfigures .side-by-side max_subfigures=2 latex_subfigure_width="0.49"}

![Habitación normal, usa solo los canales PCM.](crackers/cinema){.toleft .negate .border video="true"}

![Habitación nostálgica, el PSG lidera la melodía mientras los canales PCM aportan el bajo.](crackers/8bit){.toright .negate .border video="true"}

Osciloscopio durante una partida de Mother 3 (2006).

:::

## Sistema Operativo

El vector de reinicio del ARM7 apunta a la dirección de memoria `0x00000000`, donde reside una **ROM de 16 KB**. Esto significa que, al encenderse, la Game Boy Advance arranca primero desde esa ROM, cuyo programa muestra la icónica pantalla de inicio y luego decide si cargar el juego del cartucho.

::: {.subfigures .side-by-side}

![Animación de inicio a mitad.](splash/start.png){.toleft .pixel .border hardcover_latex_width="95%" paperback_latex_width="83%"}

![Animación de inicio al final.](splash/finish.png){.toright .pixel .border hardcover_latex_width="95%" paperback_latex_width="83%"}

La GBA estrena una nueva animación de inicio que exhibe su paleta de colores ampliada y sus capacidades de escalado de sprites.

:::

Al igual que en la [generación anterior](game-boy#operating-system), el gestor de arranque se encarga de la inicialización del hardware y la protección contra copia. Sin embargo, los 16 KB le dan espacio más que suficiente para incluir también **rutinas de software** que los juegos pueden invocar para simplificar ciertas operaciones y reducir el tamaño del cartucho [@operating_system-vijn]. Por eso, la ROM de la GBA se conoce comúnmente como 'Basic Input/Output System' o **BIOS**.

Entre las rutinas disponibles encontramos:

- **Funciones aritméticas**: rutinas para realizar divisiones, raíces cuadradas y tangentes de arcos.
- **Cálculo de la matriz afín**: Dado un valor de "zoom" y un ángulo, calcula la matriz afín que se suministrará a la PPU para escalar o rotar un fondo o sprite.
  - Hay dos funciones, una para los sprites y otra para los fondos. Sus parámetros son ligeramente diferentes, pero el principio es el mismo.
- **Funciones de descompresión**: implementan algoritmos de descompresión que incluyen Run-Length, LZ77 y Huffman. También ofrecen desempaquetado de bits y diferencia secuencial.
- **Copia de memoria**: dos funciones que mueven la memoria de un lado a otro. La primera copia bloques de 32 bytes usando un código de operación especializado para este tipo de transferencia (`LDMIA` para cargar y `STMIA` para almacenar) solo una vez. La segunda copia bloques de 2 o 4 bytes usando códigos de operación `LDRH/STRH` o `LDMIA/STMIA`, respectivamente. Por lo tanto, la segunda función es más flexible pero no tan rápida.
- **Sonido**: ¡implementa el secuenciador MIDI mencionado anteriormente! Incluye muchas funciones para controlarlo.
- **Interfaz de energía**: accesos directos para reiniciar, borrar la mayor parte de la RAM, detener la CPU hasta que se produzca un determinado evento (V-blank o uno personalizado) o cambiar a "modo de bajo consumo" (que simplemente pausa el audio y la PPU).
- **Multi-boot**: carga un programa en otra GBA a través del puerto 'EXT' (la interfaz Link/serie) y lo pone en marcha. El protocolo es propietario y el programa cargado se almacena en la EWRAM del receptor.
- **Activar el modo Game Boy Color**: no está documentado y solo se invoca durante el arranque. Básicamente, configura la PPU y la VRAM para el juego de Game Boy Color, desactiva la CPU ARM, pone el SM83 al mando y, por último, arranca la [ROM de Game Boy Color](game-boy#operating-system).

La BIOS está conectada a través de un bus de 32 bits y está implementada con una combinación de instrucciones ARM y Thumb, siendo la Thumb la más utilizada.

Por último, recuerda que todo esto solo funcionará en el ARM7. En otras palabras, no hay ninguna aceleración de hardware disponible para estas operaciones. De ahí que Nintendo proporcionara toda esta funcionalidad a través de software.

### Arranque secundario

Si no hay ningún cartucho válido, el gestor de arranque espera indefinidamente a que un dispositivo externo envíe un programa Multi-boot a través del puerto 'EXT'. Así es como el 'cargador' Multi-boot puede transferir su programa en primer lugar.

Los juegos solían referirse a esta función como **Single-Pak Link**, ya que permitía que varias consolas conectadas entre sí jugaran en multijugador usando un único cartucho. Entre bastidores, la consola con el cartucho enviaba una pequeña copia de su juego a las demás consolas mediante Multi-boot.

## Juegos

Los juegos se distribuyen en un nuevo formato de cartucho propietario que sigue llamándose **Game Pak**, pero presenta un diseño más pequeño.

![El nuevo diseño del Game Pak para juegos de GBA [@photography-amos].](gba_gamepak.png){.no-borders latex_width="80%"}

La programación para la GBA comparte algunas metodologías con la [Super Nintendo](super-nintendo), pero también hereda los avances de principios de los 2000: lenguajes de alto nivel estandarizados, compiladores fiables, CPUs RISC depurables, estaciones de trabajo no propietarias para el desarrollo, documentación comparativamente mejor y... ¡acceso a Internet!

Los programas de GBA se escriben principalmente en **C** con secciones de rendimiento crítico en **ensamblador** (ARM y Thumb) para ahorrar ciclos. El SDK oficial que Nintendo suministraba a los estudios autorizados incluía bibliotecas y compiladores.

### Acceso a los datos del cartucho

Aunque el ARM7 tiene un bus de direcciones de 32 bits, hay **solo 24 líneas de dirección conectadas al cartucho**.

Esto significa, en teoría, que se puede acceder a hasta 16 MB en el cartucho sin necesidad de un [mapeador](game-boy#games). Sin embargo, el mapa de memoria muestra que **32 MB de datos del cartucho son accesibles** [@games-memorymap]. Entonces, ¿qué está pasando aquí? La verdad es que la Game Pak utiliza **direcciones de 25 bits** (lo que explica ese bloque de 32 MB) pero su bit más inferior está fijado a cero. Por lo tanto, solo se manipulan los 24 bits restantes. Así funciona el direccionamiento del Game Pak.

![Representación del modelo de direccionamiento del Game Pak. Fíjate en cómo el último bit de la dirección de 25 bits (llamado 'A0') siempre es cero. También debo señalar que en realidad, las patillas de direcciones y datos están compartidas/multiplexadas.](_diagrams/cart_addressing.png)

Ahora bien, ¿significa esto que los datos situados en direcciones impares (con su bit menos significativo a `1`) serán inaccesibles? No, porque el bus de datos es de 16 bits: en cada transferencia, la CPU o el controlador DMA obtiene el byte de la dirección indicada junto con el siguiente, lo que permite leer tanto direcciones pares como impares. Como puedes ver, se trata de otra obra de ingeniería que aprovecha al máximo las capacidades del hardware al tiempo que reduce los costes.

Curiosamente, las CPUs ARM de 26 bits también recurrían a la misma técnica. Estas albergaban un Contador de Programa de 24 bits, ya que las direcciones debían ser múltiplos de ocho (es decir, alineadas por palabra), por lo que los dos últimos bits de la dirección de 26 bits siempre eran ceros. Sin embargo, dado que estas CPUs leen 32 bits de memoria (el byte de la dirección indicada más los tres siguientes), se puede acceder a todo el espacio de direcciones de 26 bits.

### Memoria RAM del cartucho

Para guardar partidas, los Game Pak podrían incluir [@games-ziegler]:

- **Static RAM** (SRAM): es volátil, por lo que necesita una batería para mantener su contenido. El cableado de la consola la hace accesible a través del mapa de memoria de la CPU, con capacidades de hasta 64 KB (aunque los juegos comerciales no superaban los 32 KB).
- **Flash ROM**: similar a la SRAM pero sin necesidad de batería. No obstante, tiene un número limitado de ciclos de escritura.
- **Electrically Erasable Programmable ROM** (EEPROM): requieren una interfaz serie y teóricamente pueden escalar a cualquier tamaño (a menudo se encuentran de hasta 8 KB).

### Accesorios

A lo largo de su ciclo de vida, la GBA contó con una interesante gama de accesorios que reimaginaron las posibilidades de uso de esta consola. Muchos de ellos sacaron partido de Multi-boot, la versatilidad del puerto EXT y la flexibilidad de la ranura del Game Pak.

#### El nuevo puerto EXT {.tabs .active}

La anterior [conexión Game Boy Link](game-boy#external-communications) (también llamada 'EXT' o interfaz serie) recibió una nueva actualización. Los mismos 6 pines que ofrecían capacidades multijugador y accesorios se mantuvieron, pero la electrónica de cada pin evolucionó significativamente.

El nuevo cable Link diseñado para la GBA presenta varios rasgos novedosos:

- Colores y tamaños diferenciados: un extremo es morado y el otro es gris y más grande. Detrás se esconde una jerarquía fija: el extremo morado designa a la consola conectada como **maestra**, y el extremo gris como **esclava**.
- Una toma adicional en mitad del cable: permite encadenar otro cable Link de GBA usando el conector morado/maestro.

El nuevo diseño lo hace incompatible con las consolas anteriores (y sus juegos). Sin embargo, permite encadenar hasta cuatro GBA con facilidad, estandarizando así arenas multijugador más grandes.

Nintendo también lanzó una variante llamada 'cable GameCube-Game Boy Advance', específicamente diseñado para conectar una GBA a una [GameCube](gamecube). Lo explico con más detalle en la siguiente pestaña.

#### Cambios internos {.tab}

Desde el lado de la consola, la conexión serie original se amplió con modos de operación adicionales, seleccionables mediante dos registros.

Estos modos se agrupan en dos categorías:

- **Modos síncronos**, basados en el protocolo **Serial Peripheral Interface (SPI)**. Este protocolo reserva un pin para marcar el ritmo del reloj, que el maestro (el conector morado) utiliza para controlar el flujo.
  - Así es como siempre operaban las conexiones Link de la Game Boy y la Game Boy Color.
- **Modos asíncronos**, basados en la interfaz **Universal Asynchronous Receiver-Transmitter (UART)**. No hay ningún reloj que dirija la señal; el único acuerdo es una tasa de baudios (bits por segundo) común definida por el juego. La consola maestra simplemente señaliza el inicio y el fin de cada transferencia.
  - Liberar el pin de reloj deja líneas disponibles para coordinar la transferencia entre consolas.

Los juegos de GBA pueden elegir entre modos síncronos y asíncronos, mientras que los títulos de Game Boy que corren en la GBA están restringidos a sus modos SPI heredados. 'Oficialmente', estos últimos solo admiten el cable Link original, pero he aquí algo curioso: los juegos de GB también funcionan con dos cables Link de GBA encadenados y conectados a las consolas por los extremos grises (convirtiendo a ambas en esclavas). Esto se debe a que esa configuración cruza las líneas de datos entre las dos consolas, replicando el esquema del cable Link original.

#### Modos de intercambio {.tab}

El conector EXT proporciona cuatro pines programables, que la consola gestiona a través de los siguientes modos de operación [@games-comms]:

- **Modo Normal**, usando el protocolo SPI. Diseñado principalmente para intercambiar datos entre una GBA y un accesorio. La interfaz puede enviar y recibir paquetes de 8 o 32 bits, y opera a 256 KHz o 2 MHz, logrando transferencias de 32 KB/s o 250 KB/s respectivamente. Es bastante rápido; sin embargo, por problemas de fiabilidad, la velocidad mayor solo está prevista para accesorios conectados directamente al zócalo (sin cable).
  - Aunque la velocidad es alta, corresponde a la CPU ARM (a 16,78 MHz) procesar los datos transferidos a tiempo, lo que puede ser todo un reto dado que tiene muchas otras tareas entre manos.
- **Modo Multi-jugador**, usando UART. A cambio de velocidad, permite comunicarse con hasta cuatro GBA encadenadas. Básicamente, las cuatro GBA se turnan para emitir un paquete de 16 bits por la misma línea de datos. Para lograrlo, cada GBA señaliza a la siguiente que transfiera su paquete, algo posible gracias al cableado del nuevo cable Link. Al final de la transferencia, todas las GBA contienen los paquetes que cada una ha enviado, almacenados en cuatro registros de 16 bits diferentes.

Dicho esto, ¿recuerdas Multi-boot? Esta función es compatible tanto con el Modo Normal como con el Modo Multi-jugador. Corresponde al maestro elegir cuál. La elección depende del número de periféricos y de la velocidad necesaria.

#### Aún más modos {.tab}

![El cable GameCube-Game Boy Advance [@photography-amos], específicamente diseñado para conectarse al puerto del controlador de la GameCube (gestionado por la [Interfaz Serie](gamecube#internal-io)).](link_cable.png){.tab-float .no-borders latex_width="85%"}

Para accesorios muy concretos, hay modos de comunicación adicionales disponibles:

- **Modo UART**. Se comporta como una interfaz RS-232. Concretamente, un *null modem de 5 cables con control de flujo RTS/CTS*. Además, envía y recibe datos en paquetes de 8 bits y usa un buffer FIFO para encolar hasta cuatro paquetes mientras se envían o reciben.
- **Modo JOY BUS**. Protocolo propietario en el que la GBA se convierte en periférico de la GameCube. La GBA solo puede recibir paquetes, procesar su contenido y responder.
  - Este modo se usa exclusivamente con el 'cable GameCube-Game Boy Advance', que asigna a la GBA como esclava.
- **Modo de uso general**. Como su nombre indica, los cuatro pines pasan a ser controlables por el programa, lo que permite implementar un protocolo personalizado.

## Anti-Piratería y Homebrew {.tabs-close}

En términos generales, el uso de cartuchos propietarios supuso una gran barrera en comparación con el constante [juego del gato y el ratón](playstation#anti-piracy-region-lock) al que tuvieron que enfrentarse otros fabricantes de consolas al recurrir al [formato CD-ROM](sega-saturn#the-compact-disc-cd).

Para luchar contra los cartuchos *bootleg* (reproducciones no autorizadas), la BIOS de la GBA incorporaba [las mismas comprobaciones de arranque](game-boy#anti-piracy) que se encontraban en la Game Boy original.

### Flashcarts

A medida que el almacenamiento en estado sólido se hacía más asequible, apareció en el mercado un nuevo tipo de cartucho. Los **flashcarts** parecían Game Paks normales pero incluían una memoria regrabable o una ranura para tarjetas. Esto permitía a los usuarios ejecutar archivos ROM de juegos en la consola. En realidad, el concepto no era nuevo: los desarrolladores llevaban tiempo usando herramientas similares de manera interna para probar sus juegos en hardware real (y los fabricantes les proporcionaban el equipo necesario para ello).

Las soluciones anteriores incluían memoria NOR Flash grabable (hasta 32 MB) y SRAM con batería de respaldo. Para cargar los binarios en el cartucho, la caja incluía un cable Link-to-USB para usar con una GBA y un PC con Windows XP. Con software de flasheo y controladores patentados, el ordenador cargaba un programa Multi-boot en la GBA, que a su vez transfería un binario del PC al flashcart (insertado en la GBA). En general, toda la tarea de cargar un juego se consideró *demasiado lenta*. Los flashcarts posteriores (como el 'EZ-Flash') ofrecían mayor almacenamiento y podían programarse sin necesidad de la GBA como intermediario [@anti_piracy-ezflash]. Los modelos más recientes migraron a almacenamiento extraíble (SD, MiniSD, MicroSD y demás).

La disponibilidad comercial de estas tarjetas resultó ser un **área legal gris**: Nintendo condenó su uso por permitir la piratería, mientras que algunos usuarios defendieron que era el único método para ejecutar **Homebrew** (programas producidos fuera de los estudios de juegos y, en consecuencia, sin la aprobación de Nintendo). El argumento de Nintendo se apoyaba en el hecho de que los flashers como el EZ-Writer ayudaban a los usuarios a parchear las ROM de los juegos para que pudieran ejecutarse en los cartuchos EZ-Flash sin problemas. Tras las acciones legales de Nintendo, estos cartuchos fueron prohibidos en algunos países (como el Reino Unido). No obstante, persistieron en todo el mundo.

## Esto es todo amigos

![Mi GBA y un par de juegos.<br>¡Lástima que no tenga retroiluminación!](mygba.png)
