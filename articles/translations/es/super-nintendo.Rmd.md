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
    - 
      title: "Americana"
      file: american
      caption: "La Super Nintendo.<br>Lanzada el 13/08/1991 en América."
  Motherboard:
    caption: "Mostrando la revisión 'SNS-RGB-CPU-01'.<br>Las primeras revisiones tenían el Subsistema de Sonido conectado como una tarjeta hija, las posteriores unificaron ambos PPUs."
    bib_source: photography-yaca
    extension: "jpg"
  Diagram:
    caption: "Los buses 'A' y 'B' son buses de direcciones, el bus de datos sigue la trayectoria del bus 'B' y tiene 8 bits de ancho."
#Historical
aliases:
  - /projects/consoles/super-nintendo/
  - /writings/consoles/snes/
---

## Una introducción rápida

Parece que Nintendo logró llevar la próxima generación de gráficos y sonidos sin usar componentes costosos listos para usar. Pero hay un truco: la nueva consola también fue **diseñada pensando en la expansibilidad**. En un mundo donde los CPUs evolucionan más rápido que la velocidad de la luz, Nintendo dependía en última instancia de los cartuchos de juegos para hacer brillar su consola.

## {.supporting-imagery}

## CPU

La elección del procesador de la Super Nintendo es peculiar. A diferencia de [su competencia](mega-drive-genesis#cpu) que incluye un 68000 completamente desarrollado, el chip de la SNES no es una ruptura radical con su predecesor. Para recapitular, la NES empleó una [CPU modificada 6502](nes#cpu), un componente admirado de los ordenadores de finales de los 70 y principios de los 80. Ahora, para allanar el camino para la nueva década (los 90), Nintendo optó por una solución más conservadora (y más barata): el **WDC 65C816**, una extensión de 16 bits del 6502.

### Modernización del 6502

El CPU 65C816 tiene su origen en Western Design Center, particularmente de Bill Mensch, un exmiembro del equipo del 6502 (en MOS) y del equipo del 6800 (en Motorola). En 1978, un año después de dejar MOS, Mensch fundó Western Design Center (WDC), una empresa de semiconductores que vende clones del MOS 6502 con atractivas mejoras (diseño CMOS, opcodes extra, correcciones de circuitos, nuevos modos de direccionamiento, etc.).

Un día, Apple se acercó a WDC para diseñar una CPU compatible con el 6502 que también pudiera procesar mayores cantidades de datos. Esto resultó en el WDC 65C816, lanzado en 1983. Curiosamente, Apple pasó por muchos contratiempos durante el desarrollo de un ordenador que usara el nuevo CPU, hasta tres años después, con el lanzamiento del Apple IIGS.

Mientras tanto, Nintendo disfrutaba de una buena relación con Ricoh y su serie de chips hechos a medida para la NES. No he encontrado el documento exacto que describe lo que conectó Ricoh con WDC, pero lo que puedo confirmar es que en algún momento, WDC acordó licenciar sus diseños del 65C816 a Ricoh [@cpu-interview]. En consecuencia, este último lo adaptó a los nuevos requisitos de la Super Nintendo y se convirtió en el **Ricoh 5A22**, suministrado exclusivamente a Nintendo.

### La nueva CPU

Como has visto antes, el procesador principal de esta consola es el **Ricoh 5A22**, un superconjunto del 65C816.

A diferencia del Apple IIGS, que disfrutaba de compatibilidad con software del Apple II, la Super Nintendo **no es compatible con los juegos de NES**. Para ser justos, al observar la elección del procesador, hay una ligera posibilidad de que el SNES se planeara originalmente para ser compatible con los juegos del NES, quién sabe.

Continuando, la CPU emplea una **velocidad de reloj variable** que alcanzará hasta **3.58 MHz** durante operaciones de registro y hasta **1.79 MHz** al acceder a los buses más lentos (p. ej., el puerto serial/controlador).

En resumen, el 5A22 cuenta con:

- La **ISA 65816**: La instrucción de 16 bits debutante del 65C816. Está basada en la ISA 6502 pero no implementa instrucciones no documentadas a las que recurrieron algunos juegos de NES [@cpu-unoffopcodes].
  - El tamaño de las instrucciones puede variar entre 1 byte (8 bits) y 4 bytes (32 bits) dependiendo de cómo se referencian las direcciones de memoria (también conocido como el 'modo de direccionamiento' utilizado) [@cpu-isaref].
  - El [modo BCD roto](nes#scrapped-functions) está **funcionando** nuevamente (supongo que como consecuencia de un licenciamiento _apropiado_).
- **10 diferentes modos de operación**: Debido a la combinación de un modo de compatibilidad retroactiva, el regreso del modo BCD (ausente en la NES) y la capacidad de cambiar entre grupos de registros de 16 bits y 8 bits [@cpu-opcodes], los desarrolladores pueden utilizar esta CPU con diferentes combinaciones de estos.
  - A diferencia de CPUs [MIPS posteriores](nintendo-64#cpu), no existe un conjunto de instrucciones mixto con opcodes dedicados para palabras de 8 y 16 bits. En su lugar, el mismo conjunto de instrucciones será interpretado de manera diferente según el modo activado.
  - Por razones de compatibilidad, la CPU siempre comienza en modo 'emulación' (6502 puro) y depende del programa cambiar a un modo 'nativo' particular para habilitar la funcionalidad de 16 bits. Por cierto, es divertido cómo el x86 de Intel todavía aplica el [mismo _modus operandi_](xbox#boot-process) en sus CPUs modernas.
- **Tres registros de propósito general de 16 bits**. Este conjunto coincide con los del 6502 (`X`, `Y` y `A`). Sin embargo, debido a los diferentes modos de operación, estos registros ahora pueden cambiar entre 16 bits y 8 bits.
- **Bus interno de datos de 16 bits** y un **bus externo de datos de 8 bits**: Lo que significa que hay penalizaciones de rendimiento por mover valores mayores de 8 bits a través de la memoria (la CPU consume ciclos adicionales), especialmente considerando que la mayoría de las instrucciones tienen 16 bits de longitud. Sin embargo, el costo total es relativo, ya que la variante de Ricoh incluye unidades DMA (explicadas más adelante) y el reloj de la CPU variará según la operación.
- **Espacio de direcciones de 24 bits**: Permite que la CPU acceda a **hasta 16 MB de memoria**. Al igual que el [Motorola 68000](mega-drive-genesis#cpu), excepto que el 65C816 obtiene sus direcciones de 24 bits combinando registros adicionales de 8 bits (`DBR` y `PBR`) con las líneas de direccionamiento de 16 bits originales del 6502 [@cpu-chibi]. En general, esta metodología es similar a usar un [mapeador interno](pc-engine#memory-access).

### Adiciones de Ricoh

El 65C816 era una CPU de propósito general aceptable para su década (los 80). Sin embargo, Nintendo planeó que su consola durara durante los 90. Entonces, Ricoh tuvo que mejorar su juego, si me permiten el juego de palabras.

Como consecuencia, la primera mejora aparente fue la adición de **unidades de multiplicación** y **división de 16 bits**, que proporcionan a la CPU la capacidad de realizar este tipo de operaciones por hardware (el 65C816 no incluye ninguna instrucción dedicada para este tipo de aritmética).

#### Acceso rápido a la memoria

El segundo grupo de adiciones fueron los **dos** DMAs exclusivos (Acceso Directo a Memoria) que permiten mover datos sin la intervención de la CPU (resultando en mayores velocidades). Para que este diseño funcione, las regiones de memoria se referencian utilizando dos buses de direcciones diferentes [@cpu-manual]:

- 24-bit **'Bus A'** controlado por la CPU: Conecta el cartucho, la CPU y la WRAM.
- 8-bit **'Bus B'** controlado por el S-PPU: Conecta el cartucho, la CPU, WRAM, S-PPU y la CPU de Audio.

Cuando se está configurando un DMA, el *origen* debe venir de un bus diferente que el *destino*.

Además, los dos DMAs no son idénticos y proporcionan funciones muy distintas [@graphics-piepgrass]:

- **DMA de propósito general** realiza transferencias en cualquier momento. La CPU se detiene hasta que la transferencia se termina.
- **DMA Horizontal** (HDMA) realiza una pequeña transferencia después de cada escaneo horizontal (mientras el haz de CRT se prepara para dibujar la siguiente fila). Esto evita interrumpir la CPU por intervalos largos, pero las transferencias están limitadas a 4 bytes por línea de escaneo.

Finalmente, el sistema proporciona **ocho canales** para configurar las transferencias DMA, permitiendo así despachar hasta ocho transferencias independientes a la vez.

#### Fallo de segmentación

Esta consola también presenta una 'anomalía' especial llamada **Open Bus**: Si hay una instrucción que intenta leer una dirección no asignada/no válida, en su lugar se suministra el último valor leído (la CPU almacena este valor en un registro llamado **Registro de Datos de Memoria** o `MDR`) y la ejecución continúa en un estado impredecible.

Para comparación, el 68000 usa una tabla de vectores para manejar excepciones, por lo que la ejecución se redirigirá siempre que se detecte una falla.

### (Muchos) más memoria

Es fascinante darse cuenta de la cantidad de contenido que el NES logró mostrar con solo [2 KB de RAM](nes#memory). Pues bien, la Super Nintendo ahora cuenta con **128 KB de SRAM** (aún llamada 'Work RAM' o WRAM). Eso es 6400% más memoria de propósito general que su predecesora.

Entonces, ¿qué pueden hacer los desarrolladores con esto? Lo que deseen, realmente. WRAM se utiliza para almacenar información variable del juego. Cuanto mayor sea el espacio, más información se puede almacenar y procesar (por lo tanto, ahorrando en hardware de [cartuchos](nes#cartridgegame-data)). Tenga en cuenta, sin embargo, que las siguientes secciones de este artículo le mostrarán que la Super Nintendo es una máquina bastante compleja (a pesar de su 'CPU simplista'), tiendo a llamar a esta consola una 'colección de mini-ordenadores/subsistemas'. Ahora, cada subsistema puede necesitar datos de la CPU. Por lo tanto, los programadores pueden asignar parte de la WRAM para procesar esa información, justificando así la necesidad de 128 KB.

## Gráficos

Después de todo lo dicho hasta ahora, permítanme decirles que el subsistema gráfico de esta consola es una verdadera obra de ingeniería. Con una CPU tan limitada, uno podría imaginar que el SNES nunca podría eclipsar a [su competidor](mega-drive-genesis) con un Motorola 68000 'de 32 bits'. Sin embargo, los ingenieros de Nintendo y Ricoh lograron encontrar trucos ingeniosos que aprovechan el comportamiento de las pantallas CRT, expandiendo así las capacidades de esta consola sin requerir componentes costosos de última generación.

En cualquier caso, antes de profundizar, recomiendo encarecidamente leer el [artículo sobre la NES](nes#graphics) primero, ya que introduce conceptos útiles que se revisitarán aquí.

### Diseño

Como con cualquier otra consola de su generación, la Super Nintendo dibuja gráficos usando tiles 2D (8 x 8 píxeles). La NES originalmente logró esto integrando la clásica 'Picture Processing Unit' (PPU) que transmite la imagen en sincronía con una pantalla CRT. La Super Nintendo sigue el mismo camino pero ahora implementa técnicas más sofisticadas para obtener resultados más abundantes.

#### El chipset

La Super Nintendo alberga dos chips PPU diferentes que constituyen el subsistema gráfico, conocidos conjuntamente como **Super PPU** o 'S-PPU'. Ambos paquetes PPU están diseñados para servir diferentes funciones [@cpu-manual]:

- **PPU 1**: Renderiza gráficos (tiles) y les aplica transformaciones (rotación y escalado).
- **PPU 2**: Proporciona efectos como *ventana*, *mosaico* y *desvanecimientos* sobre los gráficos renderizados.

Desde el punto de vista de la programación, esta separación es redundante ya que ambos chips son tratados como uno solo.

#### Modalidades de visualización

El sistema emite una resolución estándar de **256 x 224 píxeles** a **~60 Hz** (NTSC) [@graphics-guide]. La variante PAL europea emite **256 × 240 píxeles** a **~50 Hz** en su lugar (para cumplir con la especificación PAL). Sea como sea, la mayoría de los juegos no usan los píxeles adicionales y muestran una *letterbox* (líneas negras) en su lugar.

Ahora, aquí está la parte complicada, los televisores tradicionales tienen una relación de aspecto de 4:3. Sin embargo, si haces los cálculos, la resolución de salida del Super Nintendo tiene una **relación de aspecto de 8:7**. Por lo tanto, después de proyectar la imagen en el televisor, se verá **estirada horizontalmente**, como si fuera un fotograma de **292 x 224 píxeles** en su lugar (en el caso de la variante NTSC) [@graphics-aspect]. En otras palabras, se podría decir que los píxeles en el Super Nintendo tienen una relación de aspecto de 8:7 en lugar de estar 'perfectamente cuadrados'.

::: {.subfigures .side-by-side .pixel}

![Fotograma renderizado con una resolución de 256 x 224 píxeles. Esto es lo que la consola envía al televisor.](stretch/internal.png) {.toleft.snesratiosmaller}

![Fotograma estirado visto desde el televisor (con una resolución aparente de 292 x 224 píxeles).](stretch/external.png) {.toright.snesratiobigger}

Kirby's Dream Land 3 (1997).

:::

Algunos juegos como 'The Legend of Zelda: A Link to the Past' tienen en cuenta este factor, por lo que las formas se aplastan explícitamente por diseño, lo cual se ve correcto después de ser estiradas por la TV. Sin embargo, este es un caso excepcional ya que la mayoría de los juegos no toman medidas adicionales para tener en cuenta este factor.

### Organizando el contenido

![Arquitectura de memoria de la S-PPU.](SPPU_architecture.png) {.no-borders}

Debido a razones de costo y rendimiento, los datos gráficos se distribuyen en tres regiones de memoria:

- 64 KB de **VRAM** (RAM de Video): Almacena tiles y mapas (tablas) utilizados para construir capas de fondo.
- 512 B de **CGRAM** (RAM de Gráficos en Color): Almacena 512 entradas de paleta de colores, cada entrada tiene el tamaño de una *palabra* (16 bits).
- 544 B de **OAM** (Memoria de Atributos de Objetos): Contiene tablas con referencias de 128 tiles que se usarán como *Sprites* junto con sus atributos.

### Construyendo el frame

Veamos ahora cómo se renderiza un fotograma en la consola y luego se muestra en el televisor. Para demostrar esto, *Super Mario World* será utilizado como ejemplo.

#### Tiles {.tabs.active}

![Algunos tiles de 16x16 encontrados en VRAM.](sppu_mario/tiles.png) {.tab-float.pixel}

Al igual que su predecesor, la S-PPU utiliza tiles para construir gráficos sofisticados. Aunque, hay mejoras significativas en comparación con la PPU original:

- Las **carcasas de juegos ya no están conectadas cableadamente a la PPU**, por lo que los tiles deberán copiarse primero a la VRAM (al igual que la Mega Drive de Sega). Aquí es donde la unidad DMA resulta muy útil.
- Los tiles ya no están restringidos a sus dimensiones tradicionales (8x8 píxeles), a partir de ahora también pueden tener hasta **16x16 píxeles** de ancho.
- Cuando los tiles se almacenan en memoria, estos serán comprimidos dependiendo de cuántos colores por píxel necesiten usar. La unidad de tamaño es **bpp** (bits por píxel). El valor mínimo es **2 bpp** (donde cada píxel solo ocupa dos bits en memoria y solo tiene 4 colores disponibles) mientras que el máximo es **8 bpp**, que codifica hasta 256 colores (a expensas de consumir un byte completo).

#### Fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Capa de fondo 1 (BG1).](sppu_mario/background1_map.png){.active title="Capa 1"}

![Capa de fondo 2 (BG2).](sppu_mario/background2_map.png){title="Capa 2"}

![Capa de fondo 3 (BG3).](sppu_mario/background3_map.png){title="Capa 3"}

Mapas de fondo en la VRAM.

:::

::: {.subfigures .tabs-nested .tab-float .pixel}

![Capa de fondo 1 (BG1) renderizada.](sppu_mario/background1.png){.active title="Capa 1"}

![Capa de fondo 2 (BG2) renderizada.](sppu_mario/background2.png){title="Capa 2"}

![Capa de fondo 3 (BG3) renderizada.](sppu_mario/background3.png){title="Capa 3"}

![Capas de fondo renderizadas combinadas.](sppu_mario/background_complete.png){title="Combinado"}

Las capas de fondo renderizadas después de aplicar selección y transparencia.

:::

El Super Nintendo puede generar hasta cuatro planos de fondo diferentes. Usando ya sea tiles de 8x8 o 16x16, los bloques están hechos de 32x32 píxeles (2x2 tiles). Dicho esto, el tamaño de cada capa de fondo puede ser de hasta 1024x1024 píxeles de ancho (32x32 tiles). La región en la VRAM donde estas capas son configuradas se llama **Tilemap** y está estructurada como una tabla (valores continuos en memoria).

Cada entrada en el Tilemap contiene los siguientes atributos:

- Valores de inversión Vertical & Horizontal.
- Prioridad (ya sea `0` o `1`).
- Referencia de paleta de CRAM.
- Referencia de tile.

Como siempre, estos planos son desplazables, aunque el número de características disponibles (color, número de capas, región de desplazamiento independiente y tamaño de la selección) dependerá del **modo de fondo** activado en la S-PPU, lo que nos lleva a la siguiente sección...

#### Modos {.tab}

La S-PPU proporciona muchas operaciones para los fondos, pero estas no pueden elegirse arbitrariamente. En su lugar, hay ocho modos de fondo para elegir, cada uno con un conjunto diferente de características [@cpu-rgme]:

- **Modo 0**: 4 capas con 4 colores cada una.
  - La paleta de colores es particularmente simple ya que este modo es el único que permite el mayor número de capas.
- **Modo 1**: 2 capas con 16 colores cada una + 1 capa con 4 colores.
  - Una capa puede ser dividida en primer plano y fondo.
  - Este es el modo más comúnmente usado.
- **Modo 2**: 2 capas con 16 colores cada una.
  - Este modo tiene un efecto adicional: Las capas pueden tener cada una de sus columnas desplazadas independientemente (similar a los efectos de [Game Boy](game-boy#graphics) pero desplazadas verticalmente).
- **Modo 3**: 1 capa de fondo con 128 colores + 1 capa de fondo con 16 colores.
  - Los colores pueden configurarse como valores RGB en lugar de usar referencias de CGRAM.
- **Modo 4**: Modo 2 y 3 combinados (Desplazamiento de columna + mapeo de color RGB).
  - La primera capa tiene 128 colores y la segunda solo tiene 4.
- **Modo 5**: 1 capa con 16 colores + 1 capa con 4 colores.
  - El área seleccionada tendrá una resolución sobresaliente de **512 x 224 píxeles** que se comprimirá horizontalmente para ajustarse a la pantalla (¡el fotograma de salida sigue siendo de 256 x 224 píxeles!). Esto tiene el costo de renderizar mosaicos de 16 x 8 píxeles como de 8 x 8 píxeles, y mosaicos de 16 x 16 píxeles como de 8 x 16 píxeles.
  - Además, la resolución vertical también se puede extender activando **entrelazado** (alcanzando **512 x 448 píxeles**, que ahora es proporcional a la dimensión de salida). A cambio, el mismo efecto de compresión afectará a los mosaicos pero ahora también verticalmente. Esto es útil en casos en los que la pantalla debe mostrar cantidades adicionales de información (es decir, durante multijugador/pantalla dividida).
- **Modo 6**: Combinación de Modo 2 y 5 (alta resolución + desplazamiento de columnas), pero solo se permite una capa con 32 colores.

Como puedes ver, los programadores ahora tienen la opción de priorizar el número de colores, capas, efectos o resolución del área seleccionada.

#### Sprites {.tab}

![Capa de sprite renderizada.](sppu_mario/sprites.png) {.tab-float.pixel}

Un área en memoria llamada **Memoria de Atributos de Objetos** (OAM) almacena una tabla con referencias de hasta 128 sprites con estas propiedades [@graphics-guidelines]:

- **Tamaño**: El PPU puede combinar hasta 16 mosaicos pequeños en forma de un cuadrante de mosaicos 4x4 para construir un sprite.
- **Referencias de Mosaicos**: El valor apunta a los mosaicos utilizados para dibujar el sprite.
- **Posición en la Pantalla**. Solo se renderizarán los sprites posicionados dentro del área visible.
- **Prioridad**: Dado que múltiples capas se superponen entre sí, el gráfico con la prioridad más alta será mostrado, esto también se determina por el modo de fondo en uso.
- **Ranura de paleta de colores**, permitiendo 9 ranuras para elegir desde CGRAM.
- **Voltear X/Y**.

El S-PPU puede dibujar hasta 32 sprites por línea de exploración, si se excede este número, el S-PPU solo descartará los de menor prioridad.

#### Resultado {.tab}

![¡Tada!](sppu_mario/complete.png) {.tab-float.pixel}

El S-PPU dibuja cada línea de exploración sobre la marcha primero procesando la porción respectiva de cada capa y luego mezclándolas juntas.

Una de las principales limitaciones de los juegos de NES era el hecho de que sólo podían actualizar sus gráficos durante el **V-Blank**. El momento en que el haz del CRT estaba regresando al punto de inicio proporcionaba un marco de tiempo razonable para reorganizar algunos tiles sin romper la imagen.

Bueno, ahora gracias a las nuevas capacidades del SNES, esta limitación adquirió un significado diferente.

Verás, debido a que las nuevas unidades DMA/HDMA ahora permiten a los programadores realizar transferencias de memoria sin esperar el V-Scan [@cpu-dma], los juegos ahora pueden actualizar mosaicos, colores y registros sin esperar a que se dibuje todo el fotograma. De hecho, podemos ir más allá: Debido a que los juegos ahora pueden **cambiar los ajustes del S-PPU durante medio fotograma**, esto significa que es posible **activar diferentes modos de fondo en diferentes etapas del mismo fotograma**, ¡abriendo la puerta a diseños de juegos nuevos y originales!

### _Esa_ característica {.tabs-close}

A decir verdad, aún no he mencionado la característica más importante de esta consola...

::: {.subfigures .tabs-nested .tab-float .open-float .pixel max_subfigures=1}

![Capa de fondo renderizado.](mode7/layer.png){.active title="Fondo"}

![Mapa de fondo asignado.](mode7/map.png){title="Mapa"}

![Fotograma renderizado en la pantalla.<br>El primer cuarto de las líneas de escaneo usa otro modo para simular distancia, el Modo 7 empieza en el segundo cuarto (esto es posible gracias a HDMA).](mode7/displayed.png){title="Mostrado"}

F-Zero (1990).

:::

Introduciendo el **Modo 7**, *otro* modo de fondo, pero esta vez, con una forma de trabajar completamente diferente. Aunque solo puede renderizar una única capa de fondo de 8 bpp, proporciona la capacidad exclusiva de aplicar las siguientes **transformaciones afines** en ese plano [@cpu-rgme]:

- Traducciones.
- Escalado.
- Rotación.
- Reflejo.
- Cizallamiento.

El S-PPU se controla con una **matriz de rotación** para alterar los parámetros de este modo. No entraré en el álgebra lineal aquí, pero dependiendo del efecto deseado, la CPU tendrá que realizar algunas funciones trigonométricas (seno y coseno) para llenar las entradas de esta tabla en consecuencia. Esto es realmente caro para el 65C816, incluso con el uso de precisión en punto fijo. Por esta razón, con el 5A22, Ricoh agregó registros de multiplicación y división para reducir algunos ciclos.

{.close-float}

Por cierto, nota que la lista de transformaciones no menciona **perspectiva**, que es lo que ves en el juego de ejemplo (F-Zero). Esto se logra alterando la matriz de rotación en cada llamada de HDMA, creando un efecto pseudo-3D en el proceso. ¡Eso debería darte una idea de lo práctico que es el S-PPU!

Finalmente, debido al alto número de cálculos necesarios, el mapa de memoria se cambia para optimizar el flujo de los dos PPUs, el primero procesa el **Tilemap** (donde se referencian los tiles) mientras que el otro obtiene el **Tileset** (donde se almacenan los tiles).

### Causa de la caída de cuadros

Cambiando de tema, ¿alguna vez te has preguntado qué causa el retraso en los juegos? Cuando se llama a la interrupción V-Blank para permitir la actualización de gráficos, a veces el juego aún está ejecutando algún código pesado y se salta la ventana V-Blank, los gráficos no se pueden actualizar hasta la siguiente llamada de V-Blank y como el cuadro no se actualizó, esto se manifiesta como una caída en la tasa de cuadros.

Esto también puede ocurrir al contrario, un procesamiento extenso durante un V-Blank no permitirá que el PPU emita la señal de vídeo (ya que el bus está bloqueado). Por lo tanto, se mostrarán líneas negras durante un escaneo, aunque esto es apenas perceptible ya que los cuadros se actualizan 50 o 60 veces por segundo.

### Una salida de vídeo conveniente

Todos los avances mencionados serán inútiles a menos que la consola envíe la imagen al televisor a través de un medio que ambos puedan entender. Con la Super Nintendo, la compañía debutó una especie de conexión *universal pero propietaria* llamada **Multi Out** que puede transportar muchos tipos de señales al mismo tiempo, incluyendo **Composite**, **S-Video** y **RGB** [@graphics-pinouts].

Junto con la consola, Nintendo incluyó un cable 'Multi Out to composite' ya que eso era prácticamente el denominador común de los televisores en ese entonces. En Europa, sin embargo, el puerto **SCART** también era muy popular ya que muchos decodificadores de señal y videograbadoras dependían de él. Una gran cosa sobre SCART es que también puede transportar muchos tipos de señales, lo que permite que los equipos AV utilicen el tipo de señal más óptimo sin encontrar problemas de compatibilidad. Hasta donde sé, solo a los consumidores franceses se les ofreció un cable SCART oficial que aprovechaba los pines RGB expuestos en la Super Nintendo [@graphics-manuel].

No obstante, Nintendo alteró la configuración de pines de sus consolas PAL para cumplir con el protocolo SCART, y al hacerlo reemplazó el pin de 'sincronización compuesta' con uno de 12 Voltios (lo cual indica a la TV que ajuste la relación de aspecto a 4:3). Entonces, aunque Multi Out es 'universal', los cables RGB resultantes, si es que los hay, son específicos para cada región.

Creo que los verdaderos beneficios de Multi Out comenzaron a ser evidentes durante los tiempos actuales, ya que permitió a los usuarios aprovechar la señal RGB con sus televisores de última generación sin manipular el interior de esta consola. Aunque, a diferencia de Composite y S-Video, RGB requiere una señal de 'sincronización' adicional. Para esto, el cable puede estar cableado para capturar la señal de sincronización de Composite o S-Video; o para obtener mejores resultados, usar una línea de sincronización dedicada llamada 'sincronización compuesta'. Pero, como se mencionó en el párrafo anterior, solo las consolas NTSC tenían esto último.

## Audio

Así como las capacidades gráficas, el departamento de audio de esta consola ha pasado por una renovación significativa. Yo diría que incluso proporciona las técnicas de síntesis más avanzadas de su generación.

### Arquitectura

Algunas empresas [se asociaron con Yamaha](mega-drive-genesis#audio), otras idearon una [solución interna](pc-engine#audio). Bueno, Nintendo se asoció con **Sony** (el conglomerado de electrónica y autores del _Walkman_) para que les proporcionara un sofisticado sintetizador. Y así, Sony les dio dos procesadores: un DSP que puede secuenciar y mezclar muestras; y una CPU que maneja ese DSP.

Por lo tanto, el subsistema de audio resultante de esta consola está compuesto por:

- **El S-DSP**: Reproduce muestras ADPCM a través de ocho canales diferentes, se mezclan y se envían a través de la salida de audio. El DSP es capaz de manipular muestras con una resolución de 16 bits y una frecuencia de muestreo de 32 kHz, también proporciona:
  - **Panorámica Estéreo**: Distribuye nuestros canales para proporcionar sonido estéreo.
  - **Control de sobre envolvente ADSR**: Configura cómo cambia el volumen en diferentes momentos.
  - **Retardo**: Simula efectos de eco. También incluye un filtro de frecuencia para eliminar algunas frecuencias durante la retroalimentación. ¡No confundas esto con *Reverb*!
  - **Generador de ruido**: Genera formas de onda pseudoaleatorias que suenan como estática blanca.
  - **Modulación de tono**: Permite que algunos canales distorsionen a otros. Similar a la síntesis FM (usada por [su competencia](mega-drive-genesis#audio)).
- **La CPU SPC700**: También llamada 'S-SMP', es una CPU independiente de 8 bits que se comunica con el DSP y recibe comandos de la CPU principal [@audio-smp].
- **64 KB de PSRAM**: Almacena datos y programas de audio. La CPU principal es responsable de llenarla.
  - Si se activa 'Retardo', se asignará algún espacio para los datos de retroalimentación (esto es realmente muy peligroso, ya que si no se usa con cuidado, puede sobrescribir los datos existentes).

Este bloque funciona en paralelo con la CPU principal. Cuando se enciende la consola, el SPC700 arranca una ROM interna de 64 bytes que lo configura para recibir comandos de la CPU principal [@audio-spc]. Después de eso, permanece inactivo.

::: {.subfigures .tabs-nested .tab-float .open-float max_subfigures=1}

![Canales usados para la melodía.](melody){.active video="true" title="Melodía"}

![Los tambores son discriminados con fines demostrativos.](drums){video="true" title="Tambores"}

![Todos los canales de audio.](complete){video="true" title="Completo"}

StarFox (1993).

:::

Para que el S-SMP comience a hacer trabajo útil, necesita cargar un tipo de programa conocido como **Controlador de Sonido**. Este último instruye al chip sobre cómo manipular los datos de audio en bruto que la CPU principal envía a PSRAM y también cómo comandar el S-DSP.

Como puedes ver, el subsistema de sonido fue un gran avance en comparación con la generación anterior, pero también fue un reto programarlo. La documentación proporcionada por Nintendo fue notable por incluir explicaciones ininteligibles y omitir características importantes, por lo que dependía de los programadores realizar su propia investigación.

Como consecuencia, se encontraron toneladas de diferentes controladores de sonido en el mercado [@audio-drivers], y algunos de ellos terminaron descubriendo características impresionantes del hardware.

{.close-float}

### Control de tono

El cambio de tono permite producir notas distintas usando la misma muestra. Bueno, el S-SMP incluye un bender útil para alterar el tono de manera suave. Echa un vistazo a este canal extraído de Mother 2/Earthbound, ambos ejemplos provienen de la banda sonora original, sin embargo, el primero tiene el control de tono desactivado.

![Sin cambio de tono.](pitch/no_pitch){.toleft video="true"}

![Con cambio de tono activado.](pitch/pitch){.toright video="true"}

### Evolución desde el NES

Para demostrar la evolución de los sonidos desde la NES hasta la Super NES, aquí hay dos partituras, una de un juego de NES y otra de su secuela de Super NES. Ambos utilizaron la misma composición:

![Mother (1989).](snowman_nes){.toleft video="true"}

![Mother 2/Earthbound (1994).](snowman_snes){.toright video="true"}

### Uso avanzado

::: {.subfigures .tabs-nested .tab-float .open-float max_subfigures=1}

![Canales utilizados para la melodía.](kirby/trebble){.active video="true" title="Melody"}

![Los tambores se discriminan con fines de demostración.](kirby/drums){video="true" title="Drums"}

![Todos los canales de audio.](kirby/complete){video="true" title="Completado"}

Kirby's Dream Land 3 (1997).

:::

Aquí hay una composición más rica en instrumentos que aprovecha en gran medida el cambio de tono, el eco y el envolvente.

Esta combinación de técnicas permitió que la música solo requiriera cinco canales en total, dejando los otros tres para efectos de sonido.

{.close-float}

### Confusión estéreo

Los controles de volumen del DSP están organizados en bloques de valores de 8 bits con signo [@audio-gst], esto significa que el volumen puede configurarse con **valores negativos**. *Pero espera*, si '0' significa mudo, ¿qué haría un número como '-1'? Bueno, **invertirá la señal**.

Esto se utiliza particularmente para crear un **efecto envolvente** especial, que se logra configurando los canales estéreo para salir **desfasados** (un canal emite la señal normal y el otro emite la misma señal pero invertida).

Desafortunadamente, abusar de esta característica resulta en resultados muy desagradables (por ejemplo, la sensación de que la música proviene de dentro de tu cabeza), por lo que notarás que la mayoría de los emuladores de SNES simplemente omiten esta configuración por completo.

Además, el estéreo desfasado se cancela en dispositivos mono, por lo que esos juegos tenían que incluir un selector estéreo/mono para evitar silenciar su propia banda sonora.

## Juegos

Para ser franco, el programa principal está escrito en puro **ensamblador 65816** y el controlador de música está escrito en **ensamblador SPC700**. Me temo que no encontrarás ninguna de las comodidades disponibles en equipos del siglo XXI.

Sin embargo, había algunas herramientas distribuidas por Nintendo, Intelligent Systems y Ricoh para facilitar la vida de los programadores, estas incluían [@games-sdk]:

- Diferentes tipos de **unidades de desarrollo** con la capacidad de ser controladas por un **depurador** desde una máquina anfitriona (normalmente ejecutando MS-DOS).
- **Cartuchos programables** (alias _Flashcarts_). No para _piratería_ sino para permitir a los desarrolladores probar su código en unidades minoristas.
- Ensambladores de **65816 y SPC700**.
- **Manuales de desarrollo** que explican desde una perspectiva de bajo nivel cómo funciona esta consola. Estos también pueden incluir pautas y normas que los desarrolladores deben cumplir para que su juego sea aprobado por Nintendo (requisito para su distribución).

Curiosamente, múltiples estudios de juegos como Argonaut Software, Accolade, SN Systems y otros también desarrollaron su propio equipo interno que proporcionaba más capacidades que las ofertas oficiales (por ejemplo, editor de memoria, lector de disquetes, devkit como una tarjeta hija ISA, etc) [@games-devkit].

### Configuración de cartuchos

Cuando se trata de acceder al cartucho, las cosas se vuelven mucho más confusas en comparación con el modelo de mapeo relativamente más simple del NES. Lo encuentro interesante de todos modos, especialmente para entender cómo podría expandirse.

El 65C816 usa un bus de direcciones de 24 bits, lo que le permite acceder a hasta 16 MB de datos. Sin embargo, y debido a la forma en que está diseñada esta consola, parte del espacio de direcciones es consumido por [componentes mapeados en memoria](nes#memory). Además, el 65C816 solo viene con 16 líneas de dirección, que luego se combinan con un registro interno para formar una dirección de 24 bits. Esto es análogo a albergar un [mapa interno](pc-engine#memory-access) y requerir [banco de conmutación](nes#cartridgegame-data) para acceder a datos adicionales más allá de los límites del bus de direcciones. Si lees otros artículos de la misma generación, encontrarás esta metodología algo familiar.

![Tableros de cartuchos de ejemplo diseñados con diferentes configuraciones [@photography-amos].<br>De arriba a abajo: LoROM (ROM dual con SRAM respaldado por batería), LoROM (ROM único con SRAM respaldado por batería) y LoROM (ROM único)](cartridges.png) {.no-borders}

Dicho esto, cuando se trata de diseñar el cartucho, hay muchas maneras de conectar eléctricamente los pines de dirección entre la ROM y la CPU. Cada uno aprovecha la conmutación de bancos de una manera diferente. Existen dos modelos fundamentales que permiten acceder hasta **4 MB de ROM** y **64 KB de SRAM** [@games-memory], y funcionan de la siguiente manera:

- Con el **Modelo LoROM**, los datos ROM están disponibles en bloques de 32 KB con 128 bancos para elegir [@games-bazzinotti]. SRAM, por otro lado, cabe en dos bancos, pero se ha hecho accesible a través de 15 bancos donde también se pueden encontrar datos de ROM.
  - Esto significará que el juego/programa puede necesitar realizar un cambio de banco significativo durante la ejecución. Por otro lado, la mitad de los bancos también están asignados a parte de la WRAM (lo que significa que se puede acceder a la ROM, SRAM y WRAM sin cambiar de banco).
- Con el **Modelo HiROM**, el espacio de ROM ahora ocupa bancos completos, lo que significa que los datos están disponibles en bloques de 64 KB con 64 bancos para elegir. Esto requiere menos cambio de banco a costa de no poder leer ROM, WRAM y SRAM dentro del mismo banco.

En ambos casos, una gran parte del espacio de ROM se refleja en el área sobrante del espacio de direcciones de la CPU, pero aquí está la parte interesante: una mitad del espacio se puede leer a ~2.68 MHz mientras que la otra puede alcanzar **3.58 MHz**. Esto solo sucederá si el chip de ROM soporta la velocidad más alta y la bandera `FastROM` de la CPU está habilitada [@games-registers].

Algo que aún no he mencionado es que los cartuchos que incluyen SRAM también necesitan albergar un chip **MAD-1** (o similar), que realiza la decodificación de direcciones. Esto es especialmente necesario para enganchar correctamente los pines que seleccionan entre los chips de ROM y SRAM [@games-pcb].

#### Más allá de la convención

Ahora, si los programadores necesitan más espacio, es cuando entran en juego los modelos derivados de LoROM y HiROM. Por ejemplo, dos variaciones comúnmente conocidas como **ExHiROM** y **ExLoROM** expanden el espacio de direccionamiento de la ROM reduciendo el área reflejada. Ambos pueden acceder a **~7,9 MB de ROM**.

![Star Fox (1993) utiliza el chip Super FX GSU para renderizar superficies 3D (el S-PPU solo ve una capa de fondo hecha de tiles _erráticos_).](fox.png) {.open-float.pixel}

Alternativamente y lo más importante, LoRom y HiROM también pueden adaptarse para albergar **chips de mejora** en el cartucho. Estos son procesadores adicionales que amplían las capacidades de la consola. Por nombrar algunos ejemplos de nuevas configuraciones:

- El modelo **MMC** permite el cambio de banco por el chip de mejora local. Esto fue utilizado por componentes como el **S-DD1** o el **SPC7110**, ambos descompresores de teselados de hardware.
- El modelo **Super Accelerator System** fue diseñado para el aclamado **SA-1**, una **CPU secundaria basada en el 65C816** que opera a **10.74 MHz** e incluye funcionalidad extra. Se basa en el modelo MMC con circuitería adicional para los componentes mapeados a memoria adicionales.
- Dos configuraciones adicionales, una basada en LoROM y la otra en HiROM, abren paso a la serie de chips **DSP**. Son el mismo derivado del CPU NEC µPD77C25, pero ejecutando un programa diferente. Proporcionan cálculos de vectores y matrices [@games-dsp] y diferentes juegos lo han utilizado para calcular transformaciones afines, descompresión de gráficos y cálculos de ruta más corta.
- El modelo **SFX** apunta al popular chip **Super FX GSU**. Esta configuración asigna hasta 8 MB de ROM, donde 2 MB se comparten entre la CPU principal y el Super FX. El resto del espacio de direcciones también incluye RAM de respaldo adicional y SRAM. El Super FX es un procesador propietario hecho por Argonaut que se especializa en **renderización de superficies 3D** y **transformaciones afines 2D**, los resultados se transmiten luego como tiles para que el S-PPU los pueda mostrar. Un puñado de juegos notables lo usaron para dibujar modelos 3D y/o extender el Modo 7 para sprites (ya que el Modo 7 solo puede transformar fondos).

{.close-float}

Es difícil ignorar el impacto que esta ingeniería tuvo en los juegos durante los años 90, muchos de los cuales lograron superar las expectativas de esta consola sin requerir módulos de expansión ni nada por el estilo. Podrías decir que esto complicó los planes de Nintendo de entregar un sucesor de esta consola (lo que puede explicar por qué juegos avanzados como Star Fox 2 fueron cancelados).

## Antipiratería / Bloqueo de región

Con la Super Nintendo, Nintendo una vez más se convirtió en la única autoridad en la distribución de juegos. Así, en un intento por hacer cumplir las normas de la compañía, los ingenieros idearon tres capas diferentes para proteger los cartuchos contra la distribución de terceros (y, posteriormente, la evasión de royalties).

Primero, la forma externa de los cartuchos **es diferente entre regiones**, por lo que no encajarán en consolas de diferentes regiones. Sea como sea, cualquiera podría eludir esto fácilmente usando un adaptador de terceros.

En segundo lugar, esta consola, como la NES, todavía incorpora el [**sistema de protección 10NES**](nes#anti-piracy-and-region-lock) para bloquear distribuidores no autorizados. Sea como sea, el chip CIC eventualmente fue clonado.

Como capa final (y especialmente hecha para proteger contra la piratería) los juegos también incluyen una cadena de verificaciones de piratería como:

1. Comparando el tamaño de la SRAM (las copias piratas normalmente incluyen bloques grandes de SRAM para adaptarse a cualquier tipo de juego).
2. Una serie de sumas de comprobación en el código para verificar si se eliminó la comparación anterior. Estas verificaciones se dispersarían en diferentes etapas del juego, por lo que serían difíciles de encontrar.

Esto podría anularse eliminando manualmente estas rutinas, pero, naturalmente, llevaría tiempo considerable encontrarlas todas. Después de todo, estarían dispersas por todo el juego solo para molestar al jugador (y eventualmente hacer que compren una copia legítima). La verdad sea dicha, te darás cuenta de que la mayoría de las ROMs que navegan por internet han eliminado todas sus verificaciones de piratería.

## Eso es todo amigos

![Mi SNES modificada con un cartucho americano.<br>Ese juego solo se lanzó en los Estados Unidos. ¡Afortunadamente, un chaval lo estaba vendiendo en Glasgow!](mysnes.png)
