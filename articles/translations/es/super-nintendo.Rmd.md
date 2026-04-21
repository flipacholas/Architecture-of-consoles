---
short_title: Arquitectura de la Super Nintendo
long_title: Arquitectura de la Super Nintendo
name: Super Nintendo
subtitle: Hardware antiguo con características impresionantes
date: 2019-06-28
release_date: 1990-11-21
generation: 4
cover: snes
published: true
top_tabs:
  Models:
    - 
      title: "Internacional"
      file: international
      caption: "La Super Nintendo (en Europa) o Super Famicom (en Japón).<br>Lanzada el 21/11/1990 en Japón y el 11/04/1992 en Europa."
      active: true
      latex_height: 86
    - 
      title: "Americana"
      file: american
      caption: "La Super Nintendo.<br>Lanzada el 13/08/1991 en América."
      latex_height: 94
  Motherboard:
    caption: "Mostrando la revisión 'SNS-RGB-CPU-01'.<br>Las primeras revisiones tenían el Subsistema de Sonido conectado como una placa hija; las posteriores unificaron ambas PPUs."
    bib_source: photography-yaca
    extension: "jpg"
  Diagram:
    caption: "Los buses 'A' y 'B' son buses de direcciones; el bus de datos sigue la trayectoria del bus 'B' y tiene 8 bits de ancho."
    latex_height: 93
#Historical
aliases:
  - /projects/consoles/super-nintendo/
  - /writings/consoles/snes/
---

## Una introducción rápida

Parece que Nintendo logró llevar la siguiente generación de gráficos y sonido sin recurrir a componentes costosos de catálogo. Pero hay un truco: la nueva consola también fue **diseñada pensando en la expansibilidad**. En un mundo donde las CPUs evolucionan más rápido que la velocidad de la luz, Nintendo dependía en última instancia de los cartuchos de juego para hacer brillar su consola.

### Modelos y revisiones

Niños de todo el mundo conocieron esta consola bajo distintas formas, colores y nombres. No obstante, las diferencias internas eran menos llamativas. Como es sabido, debido a la diversidad de estándares analógicos, los modelos de distintas regiones presentan variaciones en sus señales analógicas (por ejemplo, la salida de vídeo) para adaptarse a dichos estándares. Comparada con [su predecesora](nes), sin embargo, las diferencias arquitectónicas son menos pronunciadas. Por ejemplo, en esta ocasión Nintendo no incorporó circuitería exclusiva para la [expansión de audio](nes#tab-4-1-extra-channels), lo que influyó de forma significativa en la calidad de los juegos según la región.

La empresa también lanzó revisiones de hardware a lo largo de la vida útil de la consola, cuyo principal objetivo era reducir el número de componentes dedicados en la placa base y abaratar así los costes de fabricación.

## {.supporting-imagery}

## CPU

La elección del procesador de la Super Nintendo es peculiar. A diferencia de [su competencia](mega-drive-genesis#cpu), que incluía un 68000 de pleno derecho, el chip de la SNES no supone una ruptura radical con su predecesora. Como ya sabemos, la NES empleó una [CPU 6502 modificada](nes#core-functionality), un componente muy apreciado en los ordenadores de finales de los 70 y principios de los 80. Para adaptarse a la nueva década (los 90), Nintendo optó por una solución más conservadora (y más económica): el **WDC 65C816**, una extensión de 16 bits del 6502.

### Modernizando el 6502

El 65C816 tiene su origen en Western Design Center (WDC), y en particular en Bill Mensch, exmiembro del equipo del 6502 (en MOS) y del equipo del 6800 (en Motorola). En 1978, un año después de dejar MOS, Mensch fundó Western Design Center, una empresa de semiconductores que produce clones del MOS 6502 con atractivas mejoras (diseño CMOS, opcodes adicionales, correcciones de circuitería, nuevos modos de direccionamiento, etc.).

![El chip WDC 65C816 en el Apple IIGS.](photos/65816.webp)

Un día, Apple se acercó a WDC para diseñar una variante del 6502, retrocompatible, que pudiera procesar mayores cantidades de datos. El fruto de ese encargo fue el WDC 65C816, lanzado en 1983. Curiosamente, Apple atravesó numerosos contratiempos en el desarrollo de un ordenador que usara el nuevo procesador, hasta que tres años después lanzó el Apple IIGS.

![Al final, el Apple IIGS y la Super Nintendo se convirtieron en los únicos grandes adoptantes del 65C816.](photos/wdcstack.webp){latex_width="87%"}

Mientras tanto, Nintendo mantenía una buena relación con Ricoh y su serie de chips hechos a medida para la NES. No he encontrado el documento exacto que explica cómo se conectaron Ricoh y WDC, pero lo que puedo confirmar es que en algún momento WDC acordó licenciar sus diseños del 65C816 a Ricoh [@cpu-interview]. En consecuencia, Ricoh lo adaptó a los nuevos requisitos de la Super Nintendo, convirtiéndolo en el **Ricoh 5A22**, suministrado en exclusiva a Nintendo.

### La nueva CPU

Como ya hemos visto, el procesador principal de esta consola es el **Ricoh 5A22**, un superconjunto del 65C816.

![El chip Ricoh 5A22, que Nintendo denominó 'S-CPU'.](photos/s-cpu.webp){latex_width="80%"}

A diferencia del Apple IIGS, que disfrutaba de compatibilidad con el software del Apple II, la Super Nintendo **no es compatible con los juegos de NES**. Hay que reconocer que, dada la elección del procesador, es posible que la SNES se planease originalmente con esa compatibilidad en mente, quién sabe.

La CPU emplea una **velocidad de reloj variable** que alcanza hasta **3,58 MHz** en operaciones de registro y desciende a **1,79 MHz** al acceder a los buses más lentos (por ejemplo, el puerto serie/controlador).

Para entender bien las capacidades del 5A22, conviene repasar primero lo que el 65C816 ofrece de serie:

- La **ISA 65816**: el debut del conjunto de instrucciones de 16 bits del 65C816. Se basa en la ISA del 6502, pero no implementa las instrucciones no documentadas a las que recurrían algunos juegos de NES [@cpu-unoffopcodes].
  - El tamaño de las instrucciones puede variar entre 1 byte (8 bits) y 4 bytes (32 bits), dependiendo de cómo se referencian las direcciones de memoria (el 'modo de direccionamiento' utilizado) [@cpu-isaref].
  - El [modo BCD defectuoso](nes#scrapped-functions) está **operativo** de nuevo (supongo que como consecuencia de un licenciamiento _apropiado_).
- **10 modos de operación distintos**: gracias a la combinación de retrocompatibilidad con el 6502, el retorno del modo BCD (ausente en la NES) y la posibilidad de alternar entre grupos de registros de 16 y 8 bits [@cpu-opcodes], los desarrolladores pueden usar esta CPU en distintas combinaciones de todos ellos.
  - A diferencia de los [procesadores MIPS](nintendo-64#cpu) de generaciones posteriores, no existe un conjunto de instrucciones mixto con opcodes dedicados para palabras de 8 y 16 bits. En su lugar, el mismo conjunto de instrucciones se interpreta de forma distinta según el modo activo.
  - Por razones de compatibilidad, la CPU arranca siempre en modo 'emulación' (6502 puro), y es el programa quien debe cambiar a un modo 'nativo' concreto para habilitar la funcionalidad de 16 bits. Resulta curioso que el x86 de Intel siga aplicando el [mismo *modus operandi*](xbox#boot-process) en sus CPUs modernas.
- **Tres registros de propósito general de 16 bits**, que coinciden con los del 6502 (`X`, `Y` y `A`). Sin embargo, gracias a los distintos modos de operación, estos registros pueden ahora alternar entre 16 y 8 bits.
- **Bus interno de datos de 16 bits** y un **bus externo de datos de 8 bits**: esto implica penalizaciones de rendimiento al mover valores mayores de 8 bits a través de la memoria (la CPU requiere ciclos adicionales), algo frecuente dado que la mayoría de las instrucciones son de 16 bits. El impacto global se ve mitigado, no obstante, por las unidades DMA de Ricoh (explicadas más adelante) y por la velocidad de reloj variable.
- **Espacio de direcciones de 24 bits**: permite a la CPU acceder a **hasta 16 MB de memoria**. Es similar al [Motorola 68000](mega-drive-genesis#cpu), salvo que el 65C816 obtiene sus direcciones de 24 bits combinando registros adicionales de 8 bits (`DBR` y `PBR`) con las líneas de direccionamiento de 16 bits originales del 6502 [@cpu-chibi]. En definitiva, esta metodología es análoga a alojar un [mapeador interno](pc-engine#memory-access).

Dicho todo esto, he de confesar que el 65C816 se antoja excesivamente engorroso para el rendimiento que ofrece. Comparado con propuestas como el [Motorola 68000](mega-drive-genesis#the-leader), no es difícil entender por qué el Apple IIGS fue el único ordenador personal en adoptar el 65C816. A lo largo de este artículo, sin embargo, verás cómo Nintendo y Ricoh supieron convertir las limitaciones de esta CPU en oportunidades para renovar su catálogo de juegos.

### Las adiciones de Ricoh

En 1983, el 65C816 nació como sucesor generalista de su ancestro de 1975, con todas las exigencias y restricciones que eso conllevaba. Sin embargo, Nintendo planeaba que su consola durase toda la década de los 90, lo que obligó a Ricoh a dar un paso adelante.

Lo primero que había que solucionar eran las limitaciones aritméticas del chip: el 65C816 carece de instrucciones dedicadas para la multiplicación o la división. Por ello, Ricoh incorporó sendas **unidades de multiplicación** y **división**, que permiten a la CPU realizar estas operaciones por hardware en lugar de por software. En vez de instrucciones convencionales, se accionan mediante registros.

El multiplicador recibe números de 8 bits y devuelve resultados de 16 bits [@cpu-multiplication]. El divisor, por su parte, acepta un dividendo de 16 bits y un divisor de 8 bits, y retorna un cociente de 16 bits junto con un resto también de 16 bits [@cpu-division]. Ambas unidades solo admiten números positivos (sin signo).

Cabe preguntarse qué relevancia tienen estas adiciones. Su importancia se hará evidente cuando exploremos las novedades de los chips gráficos de la Super Nintendo (en la sección 'Gráficos').

#### Acceso rápido a la memoria

El segundo reto era aumentar el ancho de banda de datos. Para ello, se añadieron **dos DMAs** (Acceso Directo a Memoria, del inglés 'Direct Memory Access') exclusivos que permiten mover datos sin la intervención de la CPU, obteniendo así mayores velocidades de transferencia. Para que este diseño funcione, las regiones de memoria se referencian mediante dos buses de direcciones distintos [@cpu-manual]:

- Un **'Bus A' de 24 bits** controlado por la CPU: conecta el cartucho, la CPU y la WRAM.
- Un **'Bus B' de 8 bits** controlado por la S-PPU: conecta el cartucho, la CPU, la WRAM, la S-PPU y la CPU de Audio.

Al configurar una transferencia DMA, el bus de *origen* debe ser distinto del bus de *destino*.

Además, los dos DMAs no son idénticos y cumplen funciones muy distintas [@graphics-piepgrass]:

- El **DMA de propósito general** realiza transferencias en cualquier momento; la CPU queda en pausa hasta que la transferencia concluye.
- El **DMA Horizontal** (HDMA) realiza una pequeña transferencia tras cada escaneo horizontal (mientras el haz del CRT se prepara para dibujar la siguiente fila). Esto evita interrupciones prolongadas de la CPU, aunque las transferencias se limitan a 4 bytes por línea de escaneo.

Por último, el sistema ofrece **ocho canales** para transferencias DMA, lo que permite despachar hasta ocho transferencias independientes a la vez.

#### El fallo de segmentación de 16 bits

El primitivo [comportamiento de Open Bus](nes#segmentation-fault) también está presente en esta consola. Además, se especula con la existencia de un registro interno llamado **Memory Data Register** (MDR), que supuestamente almacena estos valores [@cpu-mdr].

En contraste, el 68000 emplea una tabla de vectores para gestionar excepciones, lo que garantiza que la ejecución se redirige ante cualquier fallo.

### (Mucha) más memoria

Es fascinante comprobar la cantidad de contenido que la NES lograba mostrar con solo [2 KB de RAM](nes#memory). Pues bien, la Super Nintendo cuenta ahora con **128 KB de RAM** (que sigue denominándose 'Work RAM' o WRAM) — un incremento del 6400% en memoria de propósito general respecto a su predecesora.

No obstante, mientras que la NES se beneficiaba de la rápida RAM estática (SRAM), Nintendo optó por usar RAM dinámica (DRAM) para la WRAM de la Super Nintendo. *Dinámica*, en este contexto, significa que la memoria debe **refrescarse periódicamente**. El 5A22 se encarga de ello automáticamente, pero durante cada operación de refresco la CPU queda en pausa, lo que supone una ralentización global de aproximadamente un 3% [@cpu-korth].

Dicho esto, ¿qué pueden hacer los desarrolladores con toda esa memoria? Lo que deseen, en realidad. La WRAM almacena datos variables del juego: cuanto mayor sea el espacio disponible, mayor será la cantidad de información que se puede gestionar (reduciendo así la dependencia del [hardware del cartucho](nes#cartridgegame-data)).

No obstante, como demostrarán las siguientes secciones, la Super Nintendo es una máquina bastante compleja (pese a su CPU 'simplista'). Suelo referirme a esta consola como una 'colección de mini-ordenadores/subsistemas', y cada subsistema puede necesitar datos de la CPU. Por lo tanto, los programadores reservan porciones de la WRAM para gestionar esa información, lo que justifica la necesidad de los 128 KB.

## Gráficos

Dicho todo lo anterior, el subsistema gráfico de esta consola es una auténtica obra de ingeniería. Teniendo en cuenta la limitada CPU, cabría pensar que la SNES nunca podría eclipsar a [su competidora](mega-drive-genesis), que presume de un Motorola 68000 'de 32 bits'. Sin embargo, los ingenieros de Nintendo y Ricoh encontraron trucos ingeniosos que explotan el comportamiento de las pantallas CRT, expandiendo así las capacidades de la consola sin necesidad de componentes costosos de última generación.

Antes de profundizar, recomiendo encarecidamente leer el [artículo sobre la NES](nes#graphics) primero, ya que introduce conceptos clave que se retoman aquí.

### Diseño

Como cualquier otra consola de su generación, la Super Nintendo dibuja gráficos mediante tiles 2D (8 × 8 píxeles). La NES lo consiguió mediante su emblemática **Picture Processing Unit** (PPU), que emite la imagen en sincronía con una pantalla CRT. La Super Nintendo sigue el mismo camino, pero incorpora técnicas más sofisticadas para lograr resultados más ricos.

#### El chipset

La Super Nintendo alberga dos chips PPU distintos que constituyen el subsistema gráfico, conocidos conjuntamente como **Super PPU** o 'S-PPU'.

![Los dos chips PPU.](photos/s-ppus.webp)

Cada chip PPU cumple una función específica [@cpu-manual]:

- **PPU 1**: renderiza los gráficos (tiles) y les aplica transformaciones como rotación y escalado.
- **PPU 2**: proporciona efectos visuales —ventana, mosaico y fundidos— sobre los gráficos ya renderizados.

Desde el punto de vista de la programación, esta separación es redundante, ya que en la práctica ambos chips se tratan como una sola unidad.

#### Modalidades de visualización

El sistema NTSC emite una resolución estándar de **256 × 224 píxeles** a **~60 Hz** [@graphics-guide]. La variante europea, conforme a la especificación PAL, emite **256 × 240 píxeles** a **~50 Hz**. Sea como sea, la mayoría de los juegos no aprovechan los píxeles adicionales y muestran en su lugar una *letterbox* (bandas negras).

Lo curioso es que los televisores tradicionales tienen una relación de aspecto de 4:3. Sin embargo, si hacemos los cálculos, la resolución de salida de la Super Nintendo tiene una **relación de aspecto de 8:7**. Por consiguiente, al proyectar la imagen en el televisor, esta aparece **estirada horizontalmente**, como si fuera un fotograma de **292 × 224 píxeles** (en el caso de la variante NTSC) [@graphics-aspect]. Dicho de otro modo: los píxeles de la Super Nintendo tienen una relación de aspecto de 8:7, en lugar de ser perfectamente cuadrados.

::: {.subfigures .side-by-side .pixel}

![Fotograma renderizado con una resolución de 256 × 224 píxeles. Esto es lo que la consola envía al televisor.](stretch/internal.png){.toleft .snesratiosmaller hardcover_latex_width="76%" paperback_latex_width="66%" .border}

![Fotograma estirado visto desde el televisor (con una resolución aparente de 292 × 224 píxeles).](stretch/external.png){.toright .snesratiobigger hardcover_latex_width="85%" paperback_latex_width="75%" .border}

Kirby's Dream Land 3 (1997).

:::

La desviación de Nintendo respecto a la relación de aspecto estándar se explica por una cuestión de **coste**. Enseguida comprobarás que la S-PPU es extraordinariamente rica en funcionalidad, pero sin la velocidad necesaria para renderizarlo todo al ritmo del haz del CRT [@graphics-sanglard]. En lugar de añadir más circuitería a la placa, Nintendo optó por reducir la anchura de la imagen visible, dejando así más margen para el período de supresión horizontal.

No obstante, algunos juegos como *Chrono Trigger* tienen en cuenta este factor, por lo que sus formas aparecen deliberadamente aplanadas para lucir correctas una vez que el televisor las estira. Este sigue siendo, sin embargo, un caso excepcional.

### Organizando el contenido

![Arquitectura de memoria de la S-PPU.](_diagrams/sppu.png){.no-borders}

Por razones de coste y rendimiento, los datos gráficos se distribuyen en tres regiones de memoria:

- 64 KB de **VRAM** (RAM de Vídeo): almacena tiles y mapas (tablas) para construir las capas de fondo.
- 512 B de **CGRAM** (RAM de Gráficos en Color): almacena 512 entradas de paleta de colores, cada una del tamaño de una *palabra* (16 bits).
- 544 B de **Object Attribute Memory** (OAM): contiene tablas con referencias de 128 tiles que se usarán como *sprites*, junto con sus atributos.

La VRAM se implementa mediante **dos chips de 32 KB**, a cada uno de los cuales se accede simultáneamente a través de un bus de 8 bits independiente. Esto permite a la PPU 1 obtener valores de 16 bits por ciclo, lo que resulta especialmente eficiente para recuperar varios píxeles consecutivos en tiempo real [@graphics-sanglard_how]. Además, cada chip de VRAM cuenta con su propio bus de direcciones de 16 bits, en lugar de uno compartido. Esta configuración resultará útil cuando la PPU 1 necesite acceder a disposiciones de memoria alternativas, algo que exploraremos al hablar del 'Modo 7'.

### Construyendo el frame

Veamos ahora cómo se renderiza un frame en la consola y se muestra en el televisor. Para la demostración, se usará *Super Mario World* como ejemplo.

#### Tiles {.tabs .active}

![Algunos tiles de 16×16 encontrados en la VRAM.](sppu_mario/tiles.png){.tab-float .pixel .latex-framed}

Al igual que su predecesora, la S-PPU utiliza tiles para construir gráficos sofisticados, aunque con mejoras significativas respecto a la PPU original:

- **Los cartuchos de juego ya no están cableados directamente a la PPU**, por lo que los tiles deben copiarse primero a la VRAM (de forma similar a la [Mega Drive](mega-drive-genesis#graphics) de Sega). Aquí es donde la unidad DMA resulta de gran utilidad.
- **Los tiles ya no están restringidos a sus dimensiones tradicionales** (8×8 píxeles): ahora pueden alcanzar hasta **16×16 píxeles**.
- Cuando los tiles se almacenan en memoria, se **comprimen según la profundidad de color** (es decir, el número de colores por píxel). La unidad de medida es el **bpp** (bits por píxel). El valor mínimo es **2 bpp**, donde cada píxel ocupa dos bits y solo tiene 4 colores disponibles. El máximo es **8 bpp**, que permite 256 colores por píxel (a costa de consumir un byte completo).

#### Fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Capa de fondo 1 (BG1).](sppu_mario/background1_map.png){.active hardcover_latex_width="77%" paperback_latex_width="69%" .border title="Capa 1"}

![Capa de fondo 2 (BG2).](sppu_mario/background2_map.png){hardcover_latex_width="77%" paperback_latex_width="69%" .border title="Capa 2"}

![Capa de fondo 3 (BG3).](sppu_mario/background3_map.png){hardcover_latex_width="77%" paperback_latex_width="69%" .border title="Capa 3"}

Mapas de fondo en la VRAM.

:::

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=4 latex_subfigure_width="0.49"}

![Capa de fondo 1 (BG1) renderizada.](sppu_mario/background1.png){.active .border title="Capa 1"}

![Capa de fondo 2 (BG2) renderizada.](sppu_mario/background2.png){.border title="Capa 2"}

![Capa de fondo 3 (BG3) renderizada.](sppu_mario/background3.png){.border title="Capa 3"}

![Capas de fondo renderizadas combinadas.](sppu_mario/background_complete.png){.border title="Combinado"}

Las capas de fondo renderizadas tras aplicar selección y transparencia.

:::

La Super Nintendo puede generar hasta cuatro planos de fondo distintos. Con tiles de 8×8 o 16×16 píxeles, los [bloques](nes#tab-1-2-background-layer) se componen de 32×32 píxeles (2×2 tiles). Cada capa de fondo puede extenderse hasta 1024×1024 píxeles (32×32 tiles). La región de la VRAM donde se configuran estas capas se denomina **Tilemap** y se estructura como una tabla (valores continuos en memoria).

Cada entrada del Tilemap contiene los siguientes atributos:

- Valores de inversión vertical y horizontal.
- Prioridad (`0` o `1`).
- Referencia de paleta de la CGRAM.
- Referencia de tile.

Como siempre, estos planos son desplazables. No obstante, el número de funciones disponibles —profundidad de color, número de capas, regiones de desplazamiento independientes y tamaño de la selección— depende del **modo de fondo** activo en la S-PPU, lo que nos lleva a la siguiente sección...

#### Modos {.tab}

La S-PPU ofrece numerosas opciones para los fondos, aunque no son de libre elección. Los programadores deben escoger entre **ocho modos de fondo**, cada uno con un conjunto distinto de características [@cpu-rgme]:

- **Modo 0**: 4 capas con 4 colores cada una.
  - La paleta de colores es especialmente limitada, ya que este modo prioriza el mayor número de capas posible.
- **Modo 1**: 2 capas con 16 colores cada una + 1 capa con 4 colores.
  - Una capa puede dividirse en primer plano y fondo.
  - Es el modo más utilizado.
- **Modo 2**: 2 capas con 16 colores cada una.
  - Incluye un efecto adicional: cada columna de las capas puede desplazarse de forma independiente (similar al [efecto de tambaleo de la Game Boy](game-boy#the-wobble-effect), pero aplicado verticalmente).
- **Modo 3**: 1 capa con 256 colores + 1 capa con 16 colores.
  - Los colores pueden definirse como valores RGB en lugar de referenciar la CGRAM.
- **Modo 4**: combinación del Modo 2 y el Modo 3 (desplazamiento de columnas + mapeado de color RGB).
  - La primera capa admite 256 colores; la segunda queda limitada a solo 4.
- **Modo 5**: 1 capa con 16 colores + 1 capa con 4 colores.
  - El área seleccionada dispone de una resolución excepcional de **512 × 224 píxeles**, que se comprime horizontalmente para ajustarse a la pantalla (la salida final sigue siendo de 256 × 224 píxeles). Esto conlleva renderizar los tiles de 16×8 píxeles como de 8×8, y los de 16×16 como de 8×16.
  - Además, la resolución vertical puede ampliarse activando el **entrelazado**, alcanzando **512 × 448 píxeles**, lo que mantiene las proporciones correctas con el frame de salida. A cambio, el mismo efecto de compresión afecta a los tiles, ahora también en vertical. Es útil cuando la pantalla debe mostrar mayor cantidad de información (por ejemplo, en multijugador o pantalla dividida).
- **Modo 6**: combinación del Modo 2 y el Modo 5 (alta resolución + desplazamiento de columnas), aunque se limita a una sola capa con 32 colores.

Como puede verse, los programadores pueden decidir si priorizar la profundidad de color, el número de capas, los efectos especiales o la resolución.

#### Sprites {.tab}

![Capa de sprite renderizada.](sppu_mario/sprites.png){.tab-float .pixel latex_width="90%" .border}

Una región de memoria dedicada llamada **Object Attribute Memory** (OAM) almacena una tabla con referencias de hasta 128 sprites, cada uno con las siguientes propiedades [@graphics-guidelines]:

- **Tamaño**: la S-PPU puede combinar hasta 16 tiles en un cuadrante de 4×4 tiles para construir un sprite.
- **Referencias de tiles**: especifica los tiles utilizados para dibujar el sprite.
- **Posición en pantalla**: solo se renderizan los sprites dentro del área visible.
- **Prioridad**: cuando varias capas se superponen, se muestra el gráfico con mayor prioridad, determinada también por el modo de fondo activo.
- **Ranura de paleta de colores**: permite elegir entre 9 ranuras de la CGRAM.
- **Inversión X/Y**: habilita el reflejo horizontal y vertical.

La S-PPU puede renderizar **hasta 32 sprites por línea de escaneo**; si se supera este límite, los de menor prioridad quedan descartados.

#### Resultado {.tab}

![¡Tachán!](sppu_mario/complete.png){.tab-float .pixel latex_width="90%" .border}

La S-PPU renderiza cada línea de escaneo sobre la marcha, procesando primero la porción correspondiente de cada capa y mezclándolas a continuación.

Una de las principales limitaciones de los juegos de NES era que las actualizaciones gráficas solo podían producirse durante el **V-Blank** — el breve intervalo en que el haz del CRT regresaba al punto de inicio ofrecía un margen de tiempo razonable para reorganizar algunos tiles sin romper la imagen.

Pues bien, con las nuevas capacidades de la SNES, esa limitación adquiere otro significado.

Verás, como las nuevas unidades DMA/HDMA permiten a los programadores realizar transferencias de memoria sin esperar el V-Blank [@cpu-dma], los juegos pueden actualizar tiles, colores y registros sin aguardar a que se dibuje el frame completo. Y las posibilidades van aún más lejos: dado que los juegos pueden **modificar los ajustes de la S-PPU a mitad de frame**, es posible **activar distintos modos de fondo en diferentes etapas del mismo frame**, lo que abre la puerta a diseños de juego nuevos y originales.

### *Esa* característica {.tabs-close}

A decir verdad, aún no he mencionado la característica más importante de esta consola...

::: {.subfigures .tabs-nested .tab-float .open-float .pixel max_subfigures=3 latex_subfigure_width="0.49" #fig-fzero_mode7}

![Capa de fondo renderizada.](mode7/layer.png){.active .border title="Fondo"}

![Mapa de fondo asignado.](mode7/map.png){.border title="Mapa"}

![Frame renderizado en pantalla.<br>El primer cuarto de las líneas de escaneo usa otro modo para simular distancia; el Modo 7 arranca en el segundo cuarto (posible gracias al HDMA).](mode7/displayed.png){.border title="Mostrado"}

Deconstrucción de F-Zero (1990).

:::

Presentamos el **Modo 7**, *otro* modo de fondo, pero esta vez con un funcionamiento radicalmente distinto. Aunque solo puede renderizar una única capa de fondo de 8 bpp, ofrece la capacidad exclusiva de aplicar las siguientes **transformaciones afines** sobre ese plano [@cpu-rgme]:

- Traslación.
- Escalado.
- Rotación.
- Reflejo.
- Cizallamiento.

El Modo 7 se controla mediante una **matriz de rotación** que altera sus parámetros. Sin entrar en álgebra lineal, dependiendo del efecto deseado, la CPU debe calcular ciertas funciones trigonométricas (seno y coseno) para completar las entradas de esta matriz. Esta operación es computacionalmente costosa para el 65C816, incluso con aritmética de punto fijo. Por esa razón, en el 5A22 Ricoh incorporó registros de multiplicación y división para aliviar la carga de la CPU.

{.close-float}

Por cierto, cabe señalar que la lista de transformaciones no menciona la **perspectiva**, que es precisamente lo que se aprecia en el juego de ejemplo (F-Zero) [@fig-fzero_mode7]. Este efecto se consigue ajustando la matriz de rotación en cada llamada de HDMA, creando un efecto pseudo-3D en el proceso. Esto debería darte una idea de lo versátil que resulta la S-PPU.

Por último, el uso de transformaciones afines hace que ya no sea ventajoso obtener píxeles adyacentes de la VRAM. Para mantener un ancho de banda aceptable, el mapa de memoria se reorganiza en beneficio del nuevo pipeline: el primer chip de VRAM almacena el **Tilemap** (donde se referencian los tiles), mientras que el segundo guarda el **Tileset** (donde se almacenan los tiles). Como las entradas de ambas tablas ocupan 8 bits, esta configuración permite a la PPU 1 obtener referencias y tiles en un solo ciclo.

#### Circuitos generosos

Como el Modo 7 requiere cálculos adicionales, la PPU 1 incorpora circuitería dedicada para procesar la matriz de rotación. Lo realmente llamativo es que **también expone esos registros a los desarrolladores cuando está inactiva** (es decir, al usar los modos del 0 al 6, o durante el V-Blank) [@cpu-multiplication].

El multiplicador de la PPU 1 es a la vez más rápido y más capaz que el de la CPU, ya que admite números de 16 bits tanto positivos como negativos (con signo).

Al reflexionar sobre esto treinta años después, no puedo sino ver en ello un precursor de los [shaders de computación modernos](xbox-360#the-impact-on-the-industry).

### Más magia de color

Detrás de cada frame rico en detalle hay un pipeline de renderizado flexible. Como si todo lo anterior fuera poco, la S-PPU incluye un **mezclador de colores versátil** que permite personalizar cómo se combinan las capas. A diferencia de otros motores de sprites con pasos de renderizado fijos, los ingenieros de Nintendo habilitaron múltiples parámetros dentro del pipeline de la S-PPU, lo que permite a los desarrolladores alterar la interacción entre los píxeles de distintas capas para formar el frame final.

Esta funcionalidad se conoce colectivamente como **Color Math** [@graphics-color_math], aunque también recibe el nombre de 'Suma/Resta de Pantalla', a pesar de admitir también la división entre dos. Sea como sea, esto abrió el camino a **transparencias realistas, iluminación dinámica y otros efectos creativos**.

::: {.subfigures .side-by-side .pixel max_subfigures=2 #fig-dk_layers}

![La lámpara oscila junto con la luz; este inquietante efecto se consigue alterando las opciones de enmascaramiento de la S-PPU.](color_math/dk1.png){.toleft .border hardcover_latex_width="78%" paperback_latex_width="70%"}

![El loro te sigue con una antorcha que puede apuntar en ambas direcciones y rebotar. El efecto encaja a la perfección con la atmósfera del juego.](color_math/dk2.png){.toright .border hardcover_latex_width="78%" paperback_latex_width="70%"}

Donkey Kong Country (1994) exprimió las capacidades del mezclador de la S-PPU hasta límites asombrosos.

:::

En esencia, el proceso funciona así [@graphics-rgme_color_math]:

1. Internamente, la S-PPU alberga dos pipelines de renderizado: el **Main Screen** (o Pantalla Principal) y el **Sub Screen** (o Pantalla Secundaria), a los que los programadores pueden asignar capas de fondo y la capa de sprites.
    - El Sub Screen también puede rellenarse con un color sólido en lugar de recibir capas.
    - Los modos de alta resolución (Modo 5 y 6) consumen los recursos del Sub Screen, por lo que el Color Math no funciona en dichos modos.
2. Los píxeles de ambos pueden enmascararse mediante parámetros de ventana individuales.
3. La S-PPU combina por separado las capas de fondo del Main Screen y del Sub Screen, respetando la prioridad de cada capa y los ajustes de ventana.
4. Por último, la S-PPU permite a los programadores decidir cómo combinar el contenido de ambas pantallas: puede **sumar** o **restar** las dos pantallas y luego **dividir** el resultado entre dos (obteniendo efectos de transparencia).
    - La división entre dos es computacionalmente barata en circuitos digitales, ya que solo requiere desplazar el valor una posición a la derecha.

En el segundo ejemplo de *Donkey Kong Country* [@fig-dk_layers], el Main Screen consiste en una única capa blanca, mientras que el Sub Screen contiene el resto del escenario, codificado con **colores invertidos**. En la etapa final del pipeline, la S-PPU aplica resta y división, produciendo un frame con los colores correctos, efectos de transparencia y mayor brillo —simulando así la luz de la antorcha de forma convincente.

### Causa de la caída de fotogramas

Dicho esto, ¿qué provoca los tirones en los juegos? Cuando se activa la interrupción de V-Blank para permitir la actualización de gráficos, a veces el juego todavía está ejecutando código costoso y deja escapar la ventana de V-Blank. Como consecuencia, los gráficos no pueden actualizarse hasta la siguiente llamada de V-Blank, y como el frame no cambia, esto se manifiesta como una caída en la tasa de frames [@graphics-rgme_lag].

A la inversa, un procesamiento excesivo durante el V-Blank puede impedir que la S-PPU emita la señal de vídeo, ya que el bus permanece ocupado. Esto puede provocar la aparición de líneas negras durante el escaneo, aunque el efecto apenas se percibe dado que los frames se actualizan 50 o 60 veces por segundo.

### Una salida de vídeo conveniente

Todos los avances mencionados serían inútiles si la consola no pudiera transmitir la imagen al televisor a través de un medio que ambos comprendieran. Con la Super Nintendo, la compañía presentó una conexión *universal pero propietaria* denominada **Multi Out**, capaz de transportar simultáneamente varios tipos de señal, incluidas **Composite**, **S-Video** y **RGB** [@graphics-pinouts].

Nintendo incluyó con la consola un cable 'Multi Out a Composite', ya que el vídeo compuesto era prácticamente el mínimo común denominador de los televisores de la época.

En Europa, sin embargo, el puerto **SCART** gozaba de gran popularidad, especialmente en decodificadores y videograbadoras. Una de sus principales ventajas era la capacidad de soportar múltiples tipos de señal, lo que permitía a los equipos AV seleccionar el formato óptimo sin problemas de compatibilidad. Que yo sepa, solo los consumidores franceses recibieron un cable SCART oficial que aprovechaba los pines RGB de la Super Nintendo [@graphics-manuel].

En consecuencia, Nintendo modificó la disposición de pines de sus consolas PAL para cumplir con el protocolo SCART, reemplazando el pin de 'sincronización compuesta' por uno de 12 voltios —que indica al televisor que use una relación de aspecto de 4:3—. Así pues, aunque Multi Out es teóricamente universal, los cables RGB resultantes son específicos de cada región.

En mi opinión, los beneficios prácticos de Multi Out se hicieron más evidentes en años recientes, al permitir a los usuarios aprovechar la señal RGB en sus televisores modernos sin necesidad de modificar el interior de la consola. No obstante, a diferencia de Composite y S-Video, RGB requiere una señal de 'sincronización' adicional. Para ello, los cables pueden capturar la señal de sincronización de Composite o S-Video o, para obtener mejores resultados, usar una línea dedicada llamada 'sincronización compuesta'. Por desgracia, como ya se indicó, esta última señal es exclusiva de las consolas NTSC.

## Audio

Al igual que en el apartado gráfico, el departamento de audio de esta consola experimentó una renovación significativa. Me atrevería a decir que incluso ofrece las técnicas de síntesis más avanzadas de su generación.

### Arquitectura

Algunas empresas [se asociaron con Yamaha](mega-drive-genesis#audio), mientras que otras idearon una [solución propia](pc-engine#audio). Nintendo, por su parte, se alió con **Sony** —el conglomerado electrónico autor del *Walkman*— para que le suministrara un sofisticado sintetizador.

De este modo, Sony entregó todo un subsistema de audio compuesto por los siguientes elementos:

- **El S-DSP**: reproduce **muestras ADPCM** en **ocho canales** distintos, los mezcla y los envía a la salida de audio. El S-DSP procesa las muestras con **resolución de 16 bits** a una frecuencia de muestreo de **32 kHz**, y ofrece diversas técnicas de manipulación de sonido:
  - **Panorámica estéreo**: distribuye los canales de audio para crear un paisaje sonoro estéreo.
  - **Control de sobre envolvente ADSR**: ajusta cómo varía el volumen a lo largo del tiempo.
  - **Retardo**: simula efectos de eco. Incluye también un filtro de frecuencia para eliminar ciertas frecuencias durante la retroalimentación. ¡No confundas esto con *Reverb*!
  - **Generador de ruido**: genera formas de onda pseudoaleatorias que suenan como estática blanca.
  - **Modulación de tono**: permite que algunos canales influyan en el tono de otros, de forma similar a la síntesis FM (empleada por [su competidora](mega-drive-genesis#audio)).
- **La CPU SPC700** (también conocida como 'S-SMP'): una CPU independiente de 8 bits que controla el S-DSP y recibe comandos de la CPU principal [@audio-smp].
- **64 KB de PSRAM**: almacena datos y programas de audio; la CPU principal es responsable de cargar el contenido en ella.
  - Si se activa el 'Retardo', parte de la memoria se reserva para los datos de retroalimentación. Esto puede ser arriesgado: si no se gestiona con cuidado, los datos de retroalimentación podrían sobreescribir los datos existentes.

El subsistema de audio opera en paralelo con la CPU principal. Al encender la consola, el SPC700 ejecuta una ROM interna de 64 bytes que lo configura para recibir comandos de la CPU principal [@audio-spc]. Tras esa fase de inicialización, permanece inactivo hasta recibir instrucciones.

::: {.subfigures .tabs-nested .tab-float .open-float max_subfigures=1}

![Canales usados para la melodía.](melody){.active video="true" title="Melodía"}

![Los tambores se discriminan con fines demostrativos.](drums){video="true" title="Tambores"}

![Todos los canales de audio.](complete){video="true" latex_width="80%" title="Completo"}

Osciloscopio de Star Fox (1993).

:::

Para que el S-SMP opere de forma autónoma, necesita cargar un tipo de programa conocido como **Controlador de Sonido**. Este software instruye al chip sobre cómo manipular los datos de audio en bruto que la CPU principal envía a la PSRAM, y también cómo controlar el S-DSP.

Teniendo en cuenta las capacidades globales del sistema, la mayoría de los juegos incluían controladores de sonido que realizaban **secuenciación musical**. De forma similar al MIDI, estos controladores ordenaban al S-SMP y al S-DSP que leyeran datos de instrumentos sampleados y los reprodujeran a distintos tonos y efectos para generar la partitura musical. Era algo en lo que esta consola destacaba notablemente.

Como puede apreciarse, el subsistema de sonido supuso un gran salto adelante respecto a generaciones anteriores, aunque también planteó retos de programación considerables. Nintendo solo proporcionó un controlador oficial, el *Nintendo SPC Sound Engine* [@audio-engine]. Además, la documentación era célebre por sus explicaciones vagas y la omisión de características importantes.

Todo esto obligó a los desarrolladores a investigar por su cuenta y crear sus propias implementaciones si querían funcionalidades adicionales. Como consecuencia, surgió en el mercado una gran variedad de controladores de sonido [@audio-drivers], algunos de los cuales descubrieron capacidades impresionantes del hardware.

{.close-float}

### Control de tono {.interactive-only}

El cambio de tono permite producir notas distintas usando la misma muestra. El S-SMP incluye un control que permite alterar el tono con suavidad. Echa un vistazo a este canal extraído de Mother 2/Earthbound [@fig-pitch_bend]; ambos ejemplos provienen de la banda sonora original, pero el primero tiene el control de tono desactivado.

::: {.subfigures .side-by-side .interactive-only #fig-pitch_bend}

![Sin cambio de tono.](pitch/no_pitch){.toleft video="true"}

![Con cambio de tono activado.](pitch/pitch){.toright video="true"}

Osciloscopio de Mother 2/Earthbound (1994).

:::

### Evolución desde la NES

Para visualizar la evolución del sonido entre la NES y la Super Nintendo, aquí hay un fragmento de dos partituras: una de un juego de NES y otra de su secuela en Super Nintendo. Ambas emplean la misma composición.

::: {.subfigures .side-by-side latex_subfigure_width="0.49"}

![Mother (1989).](snowman_nes){.toleft video="true"}

![Mother 2/Earthbound (1994).](snowman_snes){.toright video="true"}

Osciloscopio de la misma partitura en ambos juegos.

:::

### Uso avanzado {.interactive-only}

::: {.subfigures .tabs-nested .tab-float .open-float .interactive-only}

![Canales usados para la melodía.](kirby/trebble){.active video="true" title="Melodía"}

![Canales usados para los tambores.](kirby/drums){video="true" title="Tambores"}

![Todos los canales de audio.](kirby/complete){video="true" title="Completo"}

Osciloscopio durante una partida de Kirby's Dream Land 3 (1997).

:::

La composición siguiente, más rica en instrumentos, aprovecha en gran medida el cambio de tono, el eco y el envolvente.

Esta combinación de técnicas permitió que la música solo requiriera cinco canales en total, dejando los otros tres para efectos de sonido.

{.close-float}

### Confusión estéreo

Los controles de volumen del S-DSP se organizan en valores de 8 bits con signo [@audio-gst], lo que significa que el volumen puede configurarse con **valores negativos**. *Pero espera*, si el '0' representa el silencio, ¿qué ocurre al poner '-1'? Pues bien, **invierte la señal**.

Esta técnica es especialmente útil para crear un **efecto envolvente**, que se consigue configurando los canales estéreo para emitir **en desfase**: un canal emite la señal normal y el otro emite la misma señal pero invertida.

Por desgracia, abusar de esta característica produce resultados muy desagradables —como la sensación de que la música suena dentro de la cabeza—, por lo que la mayoría de los emuladores de SNES pasan por alto esta configuración.

Además, el estéreo desfasado se cancela en dispositivos mono, por lo que los juegos que lo usaban debían incluir un selector estéreo/mono para evitar silenciar su propia banda sonora.

## Juegos

A decir verdad, el programa principal está escrito en puro **ensamblador 65816** y el controlador de música en **ensamblador SPC700**. No encontrarás ninguna de las comodidades disponibles en equipos del siglo XXI.

Sin embargo, Nintendo, Intelligent Systems y Ricoh distribuyeron algunas herramientas para facilitar la vida de los programadores, entre ellas [@games-sdk]:

- Distintos tipos de **unidades de desarrollo**, capaces de ser controladas por un **depurador** desde una máquina anfitriona (normalmente con MS-DOS).
- **Cartuchos programables** (alias *Flashcarts*) — no para *piratería*, sino para que los desarrolladores pudieran probar su código en unidades comerciales.
- Ensambladores de **65816 y SPC700**.
- **Manuales de desarrollo** que explicaban desde una perspectiva de bajo nivel cómo funciona esta consola, con directrices y normas que los desarrolladores debían cumplir para obtener la aprobación de Nintendo (requisito para la distribución).

Curiosamente, varios estudios —entre ellos Argonaut Software, Accolade y SN Systems— desarrollaron su propio equipamiento interno que ofrecía más capacidades que las herramientas oficiales (por ejemplo, editores de memoria, lectores de disquetes, devkits en forma de tarjeta ISA, etc.) [@games-devkit].

### Configuración de cartuchos

En cuanto al acceso al cartucho, las cosas se complican bastante más que con el modelo de mapeado relativamente sencillo de la NES. Es un asunto que merece atención, especialmente por lo que revela sobre sus posibilidades de expansión.

El 65C816 cuenta con un bus de direcciones de 24 bits que le permite acceder a hasta 16 MB de datos. No obstante, debido a la arquitectura de la consola, parte del espacio de direcciones queda reservada para [componentes mapeados en memoria](nes#memory). Además, el 65C816 solo dispone de 16 líneas de dirección, que se combinan con registros internos para construir una dirección de 24 bits. Esto es análogo a alojar un [mapeador interno](pc-engine#memory-access) y requerir [cambio de bancos](nes#cartridgegame-data) para acceder a datos adicionales más allá de los límites del bus de direcciones. Si has explorado otros sistemas de la misma generación, esta metodología te resultará familiar.

![Placas de cartucho de ejemplo diseñadas con distintas configuraciones [@photography-amos].<br>De arriba a abajo: LoROM (ROM dual con SRAM con batería), LoROM (ROM única con SRAM con batería) y LoROM (ROM única).](cartridges.png){.no-borders}

Dicho esto, a la hora de diseñar un cartucho existen múltiples formas de conectar eléctricamente los pines de dirección entre la ROM y la CPU, cada una aprovechando el cambio de bancos de un modo diferente.

Hay dos modelos fundamentales que permiten acceder a hasta **4 MB de ROM** y **64 KB de SRAM** [@games-memory], y funcionan así:

- Con el **modelo LoROM**, los datos de ROM se organizan en bloques de 32 KB con 128 bancos disponibles [@games-bazzinotti]. La SRAM, aunque cabe en dos bancos, es accesible a través de 15 bancos, donde también está presente la ROM.
  - Esta configuración requiere cambios de banco frecuentes durante la ejecución. A cambio, la mitad de los bancos también están mapeados a la WRAM, lo que significa que se puede acceder a ROM, SRAM y WRAM sin cambiar de banco.
- Con el **modelo HiROM**, el espacio de ROM se estructura en bancos completos, con datos disponibles en bloques de 64 KB en 64 bancos. Esto reduce la necesidad de cambiar de banco, pero a costa de no poder leer ROM, WRAM y SRAM dentro del mismo banco.

En ambos casos, una porción significativa del espacio de ROM se duplica en el área sobrante del espacio de direcciones de la CPU. Lo interesante es que una mitad del espacio opera a ~2,68 MHz mientras que la otra puede alcanzar **3,58 MHz** — pero solo si el chip de ROM soporta la velocidad mayor y el flag `FastROM` de la CPU está habilitado [@games-registers].

Además, los cartuchos que incluyen SRAM deben alojar un chip **MAD-1** (o similar) para realizar la decodificación de direcciones. Este componente es fundamental para gestionar correctamente los pines que seleccionan entre los chips de ROM y SRAM [@games-pcb].

#### Más allá de la convención

Cuando los programadores necesitan más espacio, entran en juego los modelos derivados de LoROM y HiROM. Dos variantes habituales, conocidas como **ExHiROM** y **ExLoROM**, amplían el espacio de direccionamiento de la ROM reduciendo el área duplicada. Ambas pueden acceder a **~7,9 MB de ROM**.

![Star Fox (1993) usa el chip Super FX GSU para renderizar superficies 3D. En segundo plano, la S-PPU solo ve una capa de fondo compuesta de tiles *erráticos*.](fox.png){.open-float .pixel}

Alternativamente, y lo más importante, LoROM y HiROM también pueden adaptarse para alojar **chips de mejora** dentro del cartucho: procesadores adicionales que amplían las capacidades de la consola. Algunos ejemplos de nuevas configuraciones:

- El modelo **MMC** permite el cambio de banco mediante el chip de mejora local. Lo usaron componentes como el **S-DD1** o el **SPC7110**, ambos descompresores de tiles por hardware.
- El modelo **Super Accelerator System** se diseñó para el aclamado **SA-1**, una **CPU secundaria basada en el 65C816** que opera a **10,74 MHz** con funcionalidad adicional. Se basa en el modelo MMC e incorpora nueva circuitería para los componentes mapeados en memoria adicionales.
- Dos configuraciones extra —una basada en LoROM y la otra en HiROM— abren paso a la **serie de chips DSP**, derivados de la CPU NEC µPD77C25 pero ejecutando un programa distinto. Proporcionan cálculos de vectores y matrices [@games-dsp], y varios juegos los han utilizado para transformaciones afines, descompresión de gráficos y cálculos de ruta más corta.
- El modelo **SFX** apunta al popular chip **Super FX GSU**. Esta configuración mapea hasta 8 MB de ROM, de los cuales 2 MB se comparten entre la CPU principal y el Super FX. El resto del espacio de direcciones incluye RAM adicional de respaldo y SRAM. Desarrollado por Argonaut, el Super FX es un procesador propietario especializado en **renderización de superficies 3D** y **transformaciones afines 2D**; los resultados se transmiten como tiles para que la S-PPU los muestre. Un puñado de juegos destacados lo utilizaron para renderizar modelos 3D o extender el Modo 7 a los sprites (el Modo 7 solo puede transformar fondos).

{.close-float}

Es difícil ignorar el impacto que esta ingeniería tuvo en los juegos de los años 90: muchos títulos superaron con creces las expectativas de la consola sin necesitar módulos de expansión ni nada por el estilo. Podría decirse que esto complicó los planes de Nintendo para un sucesor, lo que quizás explica la cancelación de juegos avanzados como Star Fox 2.

## Antipiratería / Bloqueo regional

Con la Super Nintendo, Nintendo volvió a erigirse como única autoridad en la distribución de juegos. Con el fin de imponer sus condiciones, la empresa ideó tres capas de protección para impedir la distribución no autorizada de cartuchos y la evasión de royalties.

En primer lugar, la forma exterior de los cartuchos **difiere entre regiones**, por lo que no encajan en consolas de otras áreas. Sea como sea, cualquiera podría sortear esto fácilmente con un adaptador de terceros.

En segundo lugar, al igual que la NES, esta consola todavía incorpora el [sistema de protección **10NES**](nes#anti-piracy-and-region-lock) para bloquear a los distribuidores no autorizados. No obstante, el chip CIC acabó siendo clonado.

Como capa final —diseñada específicamente para combatir la piratería—, los juegos incluían en su código una cadena de comprobaciones:

1. **Verificación del tamaño de la SRAM**: los cartuchos piratas suelen incorporar bloques grandes de SRAM para adaptarse a cualquier tipo de juego.
2. **Comprobaciones de integridad del código**: una serie de checksums distribuidos por el código del juego verifican si las rutinas de verificación del tamaño de SRAM anteriores han sido eliminadas. Estas comprobaciones se dispersan en distintas etapas del juego, lo que dificulta encontrarlas y eliminarlas.

Aunque estas medidas podían anularse extirpando las rutinas manualmente, la tarea era larga. Al fin y al cabo, estaban repartidas por todo el juego para fastidiar al jugador y empujarlo finalmente a adquirir una copia legítima. Lo cierto es que comprobarás que la mayoría de las ROMs que circulan por internet ya tienen eliminadas todas sus comprobaciones antipiratería.

## Eso es todo amigos

![Mi SNES modificada con un cartucho americano.<br>Ese juego solo se lanzó en Estados Unidos. Por suerte, un chaval lo estaba vendiendo en Glasgow.](mysnes.png)
