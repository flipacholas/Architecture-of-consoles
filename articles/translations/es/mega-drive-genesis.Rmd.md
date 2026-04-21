---
short_title: Arquitectura del Mega Drive / Genesis
long_title: Arquitectura del Mega Drive / Genesis
name: Mega Drive / Genesis
subtitle: Nuevas técnicas de composición
date: 2019-05-18
release_date: 1988-10-29
generation: 4
cover: megadrive
seo_image_pos: "Bottom"
top_tabs:
  Models:
    - title: "Japonés"
      caption: "El Mega Drive.<br>Lanzado el 29/10/1988 en Japón."
      file: japanese
      latex_height: 91
    - title: "Americano"
      caption: "El Genesis.<br>Lanzado el 14/08/1989 en América."
      file: american
      latex_height: 95
    - title: "Europeo"
      caption: "El Mega Drive.<br>Lanzado en 09/1990 en Europa."
      file: european
      active: true
      latex_height: 95
  Motherboard:
    caption: "Mostrando la revisión japonesa 'VA0'.<br>Nótese la inusual placa hija sobre el VDP, empleada para corregir errores de fabricación (corregidos adecuadamente en revisiones posteriores)."
    extension: "jpg"
    bib_source: photography-okqubit
  Diagram:
    latex_height: 95

# Historical
aliases: [/projects/consoles/mega-drive-genesis/]
---

## Una introducción rápida

Sega (y sus anuncios de televisión) quieren que sepas: los desarrolladores no pueden crear buenos juegos a menos que la consola ofrezca gráficos más rápidos y sonidos más ricos.

Su nuevo sistema incluye muchos componentes *ya conocidos* listos para programar. Esto significa que, en teoría, los desarrolladores solo necesitarían aprender sobre la nueva GPU de Sega... ¿verdad?

## {.supporting-imagery}

## CPU

Esta consola cuenta con dos procesadores de uso general de dos generaciones distintas.

### El protagonista

En primer lugar, tenemos un **Motorola 68000** funcionando a **~7,6 MHz**. Se trata de una nueva clase de CPU cuya historia merece ser contada...

#### Impulsando una década

El Motorola 68000, o '68k', supuso un salto generacional en la historia de los microprocesadores. Tras aprender de los [errores del 6800](nes#a-bit-of-context), la empresa comenzó de cero con un nuevo equipo capaz de ofrecer una puerta asequible al espacio de 32 bits. En él podían procesarse bloques de datos mucho mayores, lo que permitía a los programadores ofrecer nuevas capacidades hasta entonces desconocidas para el consumidor.

![El Macintosh de Apple (1984) y el Lisa (1983). A pesar de sus precios muy distintos, ambos se apoyaban en el Motorola 68000 para mostrar su innovadora interfaz de usuario.](macintosh.webp)

Lanzado en 1979, el 68k llegó a tiempo para impulsar la siguiente década de microcomputadoras. Aunque al principio pasó apuros al perder el contrato de IBM frente a Intel, no tardaron otras empresas y start-ups en adoptar este chip para dar vida a sus nuevas creaciones. Algunas introdujeron mejoras incrementales sobre sus predecesoras, mientras que otras alcanzaron capacidades inimaginables. Por nombrar algunas:

- Una **Interfaz Gráfica de Usuario** (GUI, del inglés *Graphical User Interface*). Los nuevos productos basados en el 68k de Amiga, Apple, Atari y Sinclair estrenaron una forma novedosa de interacción con el usuario, basada esta vez en iconografía, formas y colores, y controlada con un 'ratón'.
- **Aplicaciones de estación de trabajo**: estas máquinas, ahora asequibles, eran capaces de procesar grandes cargas de trabajo con cierto grado de especialización. Esto impulsó el auge de la infraestructura de redes, el diseño gráfico y el equipamiento de investigación, entre otros.
  - Esto coincidió también con la llegada de los sistemas operativos basados en UNIX al gran público, lo que generó una incómoda proliferación de 'estándares' y los consecuentes pleitos.

Cabe señalar que gran parte de esta funcionalidad no era nueva: simplemente había estado reservada a los costosos miniordenadores o a los prohibitivos mainframes. El hito reside en que todo esto era ahora accesible desde una oficina o un hogar cualquiera.

![El chip Motorola 68000 en el Mega Drive; este en particular es de segunda fuente, fabricado por Hitachi.](m68000.jpg)

A finales de los 80, sin embargo, el interés por el 68000 [fue perdiendo fuerza en favor de una nueva ola de CPUs RISC](playstation#tab-1-1-a-bit-of-history). No obstante, los precios habían bajado lo suficiente para que empresas como Sega se interesaran en llevarlo al mercado de las consolas.

#### Características

Sentado el contexto, permíteme contarte qué ofrece el 68000 [@cpu-user]:

- El **set de instrucciones del 68000**: un nuevo set de instrucciones con abundantes características, incluyendo un conjunto de opcodes de multiplicación y división. Algunas instrucciones tienen 8 bits de longitud (llamadas 'byte'), otras tienen 16 bits ('word') y el resto son de 32 bits ('long-word').
- **Dieciséis registros de 32 bits de uso general**. Conviene tener en cuenta que esta CPU divide el conjunto en ocho 'registros de datos' (donde pueden realizarse operaciones aritméticas) y ocho 'registros de dirección' (empleados exclusivamente para almacenar direcciones de memoria).
  - Aun así, supone un gran avance si tenemos en cuenta que el 6502 y el Z80 solo ofrecen registros de 8 bits.
- Una **Unidad Aritmético-Lógica** (ALU, del inglés *Arithmetic Logic Unit*) **de 16 bits**: lo que significa que necesita ciclos adicionales para calcular operaciones aritméticas sobre números de 32 bits, aunque funciona correctamente con valores de 16 y 8 bits.
- Un **bus de datos externo de 16 bits**: como puede apreciarse, aunque esta CPU tiene ciertas 'capacidades de 32 bits', no ha sido diseñada para ser una máquina de 32 bits completa. El ancho de este bus implica un mejor rendimiento al mover datos de 16 bits.
  - Resulta curioso que Motorola presentara una CPU de 32 bits completa, el **68020**, cuatro años antes del lanzamiento de esta consola. Sin embargo, los costes se habrían disparado si Sega hubiera optado por ese chip.
- Un **bus de direcciones de 24 bits**: lo que significa que **se puede acceder a hasta 16 MB de memoria**. Las direcciones de memoria siguen codificándose con valores de 32 bits dentro de la CPU (el byte superior simplemente se descarta).

Si te preguntas por qué se utilizan direcciones de 24 bits en una CPU capaz de manejar palabras de 32 bits: el equipamiento de la época difícilmente necesitaba gestionar 4 GB de memoria. Dado que implementar líneas sin usar resulta costoso tanto en rendimiento como en dinero, Motorola llegó a un compromiso sensato con registros de 32 bits y 24 líneas de dirección, preparando a los desarrolladores para la llegada de la CPU de 32 bits completa (el 68020) cinco años después.

#### Un set de instrucciones peculiar

Antes de la [revolución RISC](playstation#tab-1-1-a-bit-of-history), existía una escuela de pensamiento que intentaba consolidar el diseño de los sets de instrucciones. En esencia, las CPUs de consumo de los años 70 (como el 6502 o el 8080) ofrecen instrucciones que predefinen cómo se puede acceder a la memoria (lo que se conoce como 'modo de direccionamiento'). Una variedad de modos de direccionamiento permite a la CPU manejar distintos casos de uso. Sin embargo, el número de modos de direccionamiento tiende a aumentar proporcionalmente la complejidad del set de instrucciones.

Para remediar esto, con el 68000, Motorola buscó unificar la mayor parte de sus instrucciones. Lo hizo desvinculando la función de la instrucción (el 'opcode') del modo de direccionamiento, convirtiendo este último en un simple parámetro (u 'operando') más. De este modo, los desarrolladores podían usar los mismos opcodes con el modo de direccionamiento que prefirieran según sus necesidades.

Como ejemplo sencillo, para mover datos:

- El [MOS 6052](nes#cpu) y el [WDC 65816](super-nintendo#cpu) ofrecen los opcodes `LDA`, `STA`, `LDX`, `STX`, `LDY` y `STY`. La elección depende de la dirección de la información (de registro a memoria o viceversa) y del registro específico implicado.
  - Además, en el WDC 65816, el parámetro que especifica el tamaño del dato (8 o 16 bits) reside en un registro separado, lo que requiere instrucciones adicionales (`SEP` y `REP`) para cambiarlo.
- En cambio, el Motorola 68000 ofrece un único opcode `MOVE`; sus operandos especifican el origen, el destino, el tamaño y el tipo de direccionamiento.

Este principio se denomina **ortogonalidad del set de instrucciones**, e influyó enormemente en la nueva generación de CPUs de finales de los 70, aunque se diluyó rápidamente cuando los [diseños RISC](game-boy-advance#tab-1-1-the-rise-of-acorn-computers) tomaron el relevo, trasladando de facto la carga a los compiladores. En cualquier caso, la familia Motorola 68k gozó de gran popularidad durante la década de los 80, y no fue hasta principios de los 90 cuando las empresas empezaron a migrar a otro fabricante.

#### El papel del Mega Drive

En esta consola, el 68k actúa como CPU 'principal' y se encarga de gestionar la lógica del juego, manejar la E/S y realizar los cálculos gráficos.

En consecuencia, el bus externo está físicamente conectado a [@cpu-memorymap]:

- 64 KB de RAM de uso general.
- ROM del cartucho (hasta 4 MB).
- Dos mandos.
- Los registros, puertos y DMA del Video Display Processor.
- Los registros de la placa base (identifican la consola).
- Puertos de expansión (destinados a accesorios 'futuros').
- La RAM de la segunda CPU, gestionada por un *árbitro de bus*.
- El Texas Instruments SN76489 (un Generador de Sonido Programable, o PSG, que actúa como segundo chip de sonido).

### El escudero

Esta consola incorpora otra CPU: un **Zilog Z80** funcionando a **~3,5 MHz**. Se trata del mismo procesador analizado en el artículo de la [Master System](master-system#cpu).

Con el Mega Drive, sin embargo, el Z80 se destinó principalmente al **control del sonido**. Por ello, su **espacio de direcciones de 16 bits** comprende lo siguiente [@cpu-z80map]:

- 8 KB de RAM.
- Los dos chips de sonido disponibles.
- Un bloque dinámico de 32 KB mapeado al espacio de direcciones del 68000, seleccionable mediante un 'árbitro de bus'.
  - En la práctica, sin embargo, la única zona de acceso fiable aquí es la ROM del cartucho [@audio-stealth].

Por último, es importante señalar que **ambas CPUs pueden ejecutarse en paralelo**... bajo ciertas restricciones, que los siguientes párrafos explican.

### Memoria disponible

La CPU principal dispone de **64 KB** de RAM dedicada para almacenar datos de uso general, y el Z80 cuenta con **8 KB** de RAM para operaciones relacionadas con el sonido.

#### Intercomunicación

Sega eligió dos procesadores independientes **ajenos el uno al otro**, así que ¿cómo pueden los juegos gestionar ambos a la vez? Pues bien, el programa principal se ejecuta en el 68000, y esta CPU puede escribir posteriormente en la RAM del Z80. Así, el 68000 puede enviar un programa a la RAM del Z80 e instruir al Z80 para que lo cargue (enviándole una señal de reset) [@cpu-using_z80]. Una vez que el Z80 está bajo control, este puede utilizarse para gestionar el subsistema de sonido y mover memoria mediante el método descrito anteriormente, todo mientras el 68000 se encarga de otras operaciones.

![Arquitectura de memoria del Mega Drive/Genesis.](_diagrams/memory.png){.no-borders}

Hay un inconveniente, sin embargo: como cada CPU debe acceder al bus de la otra, **ambas CPUs no pueden acceder al mismo bus al mismo tiempo**. Para solucionar esto, un componente adicional llamado **Bus Arbiter** (árbitro de bus) se encarga de detener a cualquiera de los dos procesadores, permitiendo la manipulación de memoria sin riesgos.

Conviene señalar que este diseño puede rendir por debajo de lo esperado si no se gestiona adecuadamente, por lo que los juegos deben prestar especial atención al árbitro de bus para asegurarse de que ninguna CPU quede bloqueada más tiempo del necesario.

## Gráficos

La respuesta es ¡*Blast Processing*! ¿Qué más necesitas saber?

Bien, si quieres conocer la respuesta *real*: el 68000 procesa los datos gráficos y los renderiza en un chip propietario llamado **Video Display Processor** (o 'VDP' para abreviar), que envía el frame resultante (en forma de líneas de escaneo) para su visualización.

El VDP funciona a **~13 MHz** y admite varios modos de resolución según la región: hasta **320x224 píxeles** en NTSC y hasta **320x240 píxeles** en PAL.

### Detrás de las múltiples resoluciones de pantalla

Técnicamente hablando, el VDP puede mostrar 40 o 32 columnas de [tiles](master-system#tab-1-1-tiles) por línea de escaneo, y el número de filas de tiles depende de la región (28 en NTSC o 30 en PAL) [@graphics-keil]. Ahora bien, la mayoría de los juegos PAL prescinde de los tiles adicionales que permiten los sistemas PAL (probablemente para mantener la coherencia entre ambas regiones, siendo NTSC el mínimo común denominador), por lo que instruyen al VDP para renderizar con 28 filas (como en los sistemas NTSC). Así, el VDP no tiene más remedio que rellenar el área no utilizada con un color de fondo (también empleado durante el overscan).

Puedes identificar qué juegos PAL renderizan en modo NTSC comprobando el `Mode Set Register #2` en un emulador con capacidades de depuración (p. ej., Exodus). Si el cuarto bit por la derecha es `0`, el VDP está funcionando en modo NTSC [@graphics-resolution].

![Para ofrecer un modo multijugador rápido en Sonic 2 (1992), el juego activa el 'modo entrelazado' para renderizar un nivel en un solo jugador usando tiles de 8x16 píxeles (junto con otros cambios).](twopsonic/sonic2.png){.side-by-side .toleft .border .pixel}

![Por su parte, el modo multijugador más elaborado de Sonic 3 (1994) se basa en tiles dedicados de 8x8 píxeles, separados de los niveles en un solo jugador.](twopsonic/sonic3.png){.toright .border .pixel}

Además, el VDP dispone de un parámetro adicional para apilar dos tiles y formar **mapas de 8x16**, tratándolos como un tile único y duplicando así la resolución vertical. Sin embargo, esto reduce a la mitad la tasa de refresco, ya que los frames se renderizan con entrelazado (un frame renderiza las líneas de escaneo pares, el siguiente las impares, y así sucesivamente), lo que limita su funcionalidad. El modo multijugador de Sonic 2 es un buen ejemplo de este modo [@graphics-sonicmultip].

Por último, cabe señalar que el VDP se encarga automáticamente de añadir el relleno para el área de overscan, por lo que los juegos no tienen que preocuparse por qué zonas son seguras para dibujar gráficos (como ocurría con las ['zonas de peligro' de la NES](nes#constructing-the-frame)).

### Organización del contenido

En cuanto al procesamiento de los datos gráficos, este chip tiene dos modos de operación:

- **Modo IV**: El modo heredado que funciona igual que el de su [predecesor](master-system#graphics).
  - Esto no significa que esta consola pueda ejecutar juegos de Master System de inmediato, ya que se necesita un accesorio adicional, el *Power Base Converter*, para adaptar los cartuchos anteriores a esta consola. El conversor también instruye al chip de E/S para poner el Z80 al mando.
- **Modo V**: El modo de operación nativo. Nos centraremos en este.

¿Y los Modos 0 a III? Bien, estos pertenecen a la aún más antigua *SG-1000* y el Mega Drive no los admite.

Como dato curioso, un antiguo desarrollador de este sistema me comentó que la estructura de comandos del Modo V (usada para controlar el VDP) hereda el diseño del TMS9918, el conocido chip de vídeo utilizado en la SG-1000 [@graphics-clarification]. Esto facilitó que desarrolladores de terceros pudieran operar el Modo V sin depender de documentación oficial (y de los consiguientes costes de licencia).

#### Memoria disponible

![Arquitectura de memoria del VDP.](_diagrams/vdp.png){.no-borders}

El contenido gráfico se distribuye en tres regiones de memoria [@graphics-keil]:

- **64 KB de VRAM** (Video RAM): contiene la mayor parte de los datos gráficos.
- **80 B de VSRAM** (Vertical Scroll RAM): el VDP admite desplazamiento vertical y horizontal; los valores de desplazamiento vertical se almacenan en este espacio separado, por alguna razón.
- **128 B de CRAM** (Colour RAM): almacena cuatro entradas de paleta con 16 colores cada una (incluyendo `transparent`); el sistema ofrece 512 colores a elegir. Además, se pueden aplicar efectos de *Highlight* y *Shadow* a cada paleta para obtener una gama más amplia de colores por paleta.

### Construcción del frame

La siguiente sección explica cómo el VDP dibuja cada frame. A modo de demostración, se usa *Sonic The Hedgehog* como ejemplo. Antes de continuar, recomiendo revisar el *modus operandi* de su [predecesor](master-system#graphics), ya que buena parte de lo explicado allí volverá a aparecer aquí.

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel latex_subfigure_width="0.46"}

![Varios tiles agrupados. Para la demostración se utiliza una paleta por defecto.](vdp_sonic/tiles.png){.active .border title="Todos"}

![Un tile individual de 8x8 píxeles.](vdp_sonic/tiles_single.png){.border title="Individual"}

Algunos tiles encontrados en la VRAM.

:::

Al igual que la PPU de Nintendo, el VDP es un motor basado en tiles y, como tal, utiliza **tiles** (bitmaps básicos de 8x8) para componer los planos gráficos. En el caso del VDP, cada tile se codifica con un array de 4 bytes, donde cada entrada de 4 bits corresponde a un píxel y su valor corresponde a una entrada de color (que apunta a una paleta de colores).

Los cartuchos de juego almacenan los tiles en su **ROM** (alojada en el cartucho), pero deben copiarse a la **VRAM** para que el VDP pueda leerlos [@graphics-ports]. Tradicionalmente, esto solo era posible durante intervalos de tiempo específicos y lo gestionaba la CPU; por fortuna, esta consola añadió circuitería especial para delegar esta tarea al VDP (entraremos en detalles más adelante).

Los tiles se utilizan para construir un total de **cuatro planos** que, al fusionarse, forman el frame visible en pantalla. Además, los tiles de estos planos pueden solaparse entre sí, por lo que el VDP determina cuál es visible en función del tipo de plano y el **valor de prioridad** del tile.

#### Fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel #fig-background_map}

![Mapa de fondo asignado.](vdp_sonic/layer2.png){.active .border title="Completo"}

![Mapa de fondo asignado con el área seleccionada marcada.](vdp_sonic/layer2_selected.png){.border title="Selección"}

Ejemplo del plano de fondo.

:::

El plano de fondo, también conocido como **Plane B**, es un tilemap desplazable (conjunto de tiles) que contiene **tiles estáticos** [@graphics-macdonald].

Este plano admite seis dimensiones distintas: 256x256, 256x512, 256x1024, 512x256, 512x512 y 1024x256. Los programadores pueden seleccionar la dimensión que mejor se adapte al tipo de desplazamiento requerido.

Cada tile puede ser **volteado horizontal y/o verticalmente** y tener una **prioridad** asignada.

En el ejemplo mostrado [@fig-background_map], notarás que el área seleccionada para su visualización no es un cuadrado... *¡Y no tiene por qué serlo!* El VDP permite configurar valores de desplazamiento horizontal para el frame completo, para cada línea de escaneo individual o para cada ocho píxeles. Esto significa que los desarrolladores pueden dar al área seleccionada la forma de un rombo y modificar sus ángulos a medida que el jugador se mueve para simular **efectos de perspectiva**. Estos trucos no dañan el plano; el VDP obtiene cada línea horizontal seleccionada y construye un frame regular a partir de ella.

#### Primer plano {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Plano frontal asignado.](vdp_sonic/layer1.png){.active .border title="Completo"}

![Plano frontal asignado con el área seleccionada marcada.](vdp_sonic/layer1_selected.png){.border title="Selección"}

Ejemplo del plano frontal; el Window Plane no se utiliza.

:::

El plano frontal, también conocido como **Plane A** [@graphics-macdonald], comparte las mismas propiedades que el plano de fondo, salvo que este tiene **mayor prioridad**, lo que significa que los tiles renderizados aquí siempre se sitúan por encima del plano de fondo.

Además, este plano puede dividirse para formar un nuevo *subplano*: el **Window Plane**. La única diferencia es que este último no se desplaza.

En definitiva, los nuevos valores de prioridad y los planos separados permiten a los diseñadores crear nuevos tipos de escenarios. Asimismo, al usar diferentes velocidades de desplazamiento en cada plano, se puede lograr un **efecto parallax**.

#### Sprites {.tab}

![Capa de sprites asignada.](vdp_sonic/sprite.png){.tabs-nested .active .tab-float .pixel .border latex_width="80%" title="Completo"}

![Capa de sprites asignada con el área seleccionada marcada.](vdp_sonic/sprite_selected.png){.tabs-nested-last .pixel .border latex_width="80%" title="Selección"}

En este plano, los tiles se tratan como **sprites**. Se posicionan en un mapa de **512x512 píxeles** y solo se selecciona para su visualización una parte de él (la resolución de salida del VDP). Esto es útil para ocultar sprites no deseados o preparar otros que se mostrarán más adelante. El VDP también ofrece una antigua función de [detección de colisiones](master-system#tab-2-1-collision-detection).

Los sprites se forman combinando hasta 4x4 tiles (mapa de 32x32 píxeles) y seleccionando hasta 16 colores (incluyendo *transparent*). Si se necesita un sprite más grande, se pueden combinar varios sprites en uno.

Solo puede haber un máximo de 20 sprites por línea de escaneo y 80 por pantalla. Superar estos límites corromperá toda la capa.

La región de la VRAM donde se definen los sprites se denomina **Sprite Attribute Table** [@graphics-macdonald]; cada entrada contiene el índice del tile, las coordenadas en la capa (x e y), el valor `link` (gestiona qué sprites se dibujan primero), la prioridad (el sprite con mayor prioridad es el que se muestra en caso de solapamiento), el índice de la paleta de colores, y el volteo vertical y horizontal.

#### Resultado {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2 #fig-result_frame}

![Frame resultante.](vdp_sonic/result.png){.active .border paperback_latex_width="81%" hardcover_latex_width="90%" title="Frame"}

![Frame emitido al televisor (en formato NTSC). El VDP añade automáticamente un área de overscan que la mayoría de los televisores CRT ocultarán.](vdp_sonic/overscan.png){.border paperback_latex_width="81%" hardcover_latex_width="90%" title="Con overscan"}

¡Tachán!

:::

![Puntos CRAM en la esquina inferior izquierda del área de overscan.](vdp_sonic/cram_dots.png){.tab-float .pixel .border paperback_latex_width="81%" hardcover_latex_width="90%" #fig-cram_dots}

Mientras se dibuja el frame, el sistema llama secuencialmente a diferentes rutinas de interrupción según la posición del haz del CRT. Como probablemente hayas visto en consolas anteriores, esto permite a la CPU preparar el siguiente frame (o modificar el actual).

Convencionalmente, se invocan dos tipos de interrupciones: **H-Blank** (en cada línea horizontal) y **V-Blank** (al final de cada frame).

El H-Blank se llama numerosas veces por frame, pero se limita a ejecutar rutinas cortas. El V-Blank, en cambio, permite rutinas más largas, con el inconveniente de que solo se llama 50 o 60 veces por segundo (según la región de la consola).

Cabe señalar que el área de overscan del ejemplo muestra algunos puntos de color aleatorios en la esquina inferior izquierda [@fig-cram_dots]. Este fenómeno, conocido como **puntos CRAM** (CRAM dots), ocurre cuando la CPU actualiza las paletas en la CRAM mientras el VDP está emitiendo las líneas de escaneo restantes (en el ejemplo, esto sucede durante el overscan). Este conflicto hace que el VDP lea el valor que la CPU está escribiendo en ese momento, en lugar de la ubicación requerida en la CRAM. Así, la imagen se corrompe. En este caso concreto, el juego solo actualiza la CRAM durante el overscan, por lo que esta anomalía pasa desapercibida en los televisores CRT tradicionales. Sin embargo, en otro nivel del juego, este cambia la paleta a mitad del frame para simular efectos de agua. Por ello, los programadores intentaron disimularlo intercalando un efecto de ola de agua parpadeante [@audio-sonic_water]. Como puede verse, todo es cuestión de equilibrar los colores adicionales con el efecto secundario de la CRAM.

### Una unidad de transferencia dedicada {.tabs-close}

Hasta ahora hemos analizado qué puede hacer la CPU para actualizar los frames, pero ¿qué hay del VDP? ¿Ofrece algo más especializado? Pues sí: este chip incorpora un **Acceso Directo a Memoria** (DMA) que permite mover datos entre ubicaciones de memoria a una **mayor velocidad** y **sin la intervención de la CPU**.

El DMA puede activarse durante el H-Blank, el V-Blank o en estado activo (fuera de cualquier interrupción), y puede utilizarse para escribir sobre la VRAM, la CRAM y/o la VSRAM [@graphics-macdonald]. Sin embargo, durante las transferencias desde la RAM de la CPU mediante DMA, el bus de la CPU queda bloqueado, por lo que una planificación cuidadosa es fundamental para lograr un buen rendimiento.

El uso eficaz de estas características puede posibilitar gráficos de alta resolución, un desplazamiento parallax fluido y altas tasas de frames. En el mejor de los casos, puede que tu juego aparezca en anuncios de televisión con multitud de letreros de *¡Blast Processing!*

### Salida de vídeo

El primer diseño de esta consola (conocido comúnmente como 'Modelo 1') incluye el mismo puerto de salida de vídeo que [la Master System](master-system#video-output). Las posteriores revisiones 'Modelo 2' y 'Modelo 3' pasaron a utilizar un puerto mini-DIN.

## Audio

Las capacidades de audio de esta consola son, cuanto menos, poco convencionales. Por un lado, el Mega Drive incorpora tecnología de audio de la generación anterior. Por otro lado, introduce una técnica de síntesis novedosa (aunque compleja). Así, en cierto modo, se obtienen dos generaciones en una sola consola.

Dicho esto, el Mega Drive está equipado con dos chips de sonido: un **Yamaha YM2612** y un **Texas Instruments SN76489**.

### Funcionalidad

Veamos qué ofrece cada chip, ya que son *muy* distintos.

#### Yamaha YM2612 {.tabs .active}

::: {.subfigures .tabs-nested .tab-float latex_subfigure_width="0.49"}

![Canales FM.](fm_single){.active video="true" .negate .border title="FM"}

![Canal PCM.](pcm_single){video="true" .negate .border title="Muestra PCM"}

Sonic The Hedgehog (1991).

:::

El **Yamaha YM2612** es un **sintetizador FM** [@audio-ymwiki] que proporciona **seis canales FM**, uno de los cuales puede reproducir **muestras PCM** en su lugar. Funciona a un sexto de la velocidad de reloj del 68000 y emite un canal a la vez, aunque alterna entre cada canal cada cuatro ciclos para crear la ilusión de generar los seis canales simultáneamente [@audio-jsgroth]. Por esta razón, la tasa de muestreo del Yamaha YM2612 es de **~53 kHz**. Además, acepta muestras PCM con una resolución de **nueve bits** [@audio-nukeykt]. Ahora bien, el noveno bit nunca se documentó oficialmente, por lo que los juegos comerciales solo enviaban muestras de 8 bits. Aparte de eso, su bus está conectado exclusivamente al Z80.

La **Modulación de Frecuencia** (FM, del inglés *Frequency Modulation*) es una de las muchas técnicas profesionales utilizadas para producir sonido. Experimentó un notable auge de popularidad durante los años 80 y dio lugar a sonidos completamente nuevos, muchos de los cuales pueden escucharse en los éxitos del pop de aquella época (me encantaría darte una lista, pero mi [gusto musical](./about#super-curated-repertoire) es un poco _experimental_).

Simplificando al máximo, el algoritmo FM toma una única forma de onda (**portadora** o *carrier*) y altera su frecuencia mediante otra forma de onda (**modulador** o *modulator*). El resultado es una nueva forma de onda con un sonido diferente. La combinación portadora-modulador se denomina **operador**, y varios operadores pueden encadenarse para componer la forma de onda final. Cada combinación da lugar a un sonido distinto. El YM2612 permite **cuatro operadores por canal**.

En comparación con los sintetizadores PSG tradicionales, esto supuso una mejora radical: ya no se estaba limitado a formas de onda predefinidas.

#### Texas Instruments SN76489 {.tab}

![Canales PSG.<br>Sonic The Hedgehog (1991).](psg_single){.tab-float latex_width="80%" .negate .border video="true" #fig-psg}

El **Texas Instruments SN76489** es un **Generador de Sonido Programable** (PSG, del inglés *Programmable Sound Generator*) capaz de producir tres ondas de pulso y un canal de ruido.

Se trata, de hecho, del [chip de sonido original](master-system#audio) de la Master System, ahora integrado en el VDP. Este funciona a la velocidad del Z80.

Programar el PSG implica escribir en una única ubicación de 8 bits (llamada `PSG port` [@audio-plutiedev_psg]), a la que pueden acceder ambas CPUs. Internamente, su bus está conectado únicamente al 68000, pero el árbitro de bus gestiona el acceso de forma transparente cuando el Z80 escribe en él.

Cabe señalar que en este ejemplo [@fig-psg], el canal 'Pulse 3' permanece en silencio. Esto se debe a que el juego activa un modo que reserva el tercer pulso para modular el canal de ruido [@audio-sonic], una función presente también en la Master System.

#### Combinado {.tab}

![Todos los canales de audio.<br>Sonic The Hedgehog (1991).](complete){.tab-float .interactive-only video="true"}

Ambos chips pueden emitir sonido al mismo tiempo; un componente adicional llamado **Audio Mixer** se encarga de recibir y combinar ambas señales.

Finalmente, la señal analógica resultante se transmite a través de la línea de salida de audio.

### El director de orquesta {.tabs-close}

El Z80 es un procesador independiente por sí solo, por lo que necesita su propio programa (almacenado en los 8 KB de RAM disponibles) para interpretar los datos musicales recibidos del 68000 o de la ROM del cartucho y manipular eficazmente los dos chips de sonido. Este programa se denomina **secuenciador** o **driver**.

No obstante, el diseño de un driver de sonido no depende exclusivamente del Z80. Aunque el mapa de memoria del Z80 sugiere que es el único procesador capaz de controlar los dos chips de audio de forma conjunta (lo que puede ser un alivio para el 68000, que ya tiene bastante trabajo con otras tareas), el árbitro de bus puede detener el Z80 para conceder al 68000 acceso al YM2612 [@audio-68kym]. No es tan eficiente, pero sigue siendo una alternativa viable para los programadores. Por ejemplo, el primer *Sonic The Hedgehog* implementó su driver de sonido en el 68000, mientras que las entregas posteriores delegaron esta tarea al Z80 [@audio-stealth].

### El muestreo llevado al límite

En lugar de conformarse con kits de batería convencionales, más de un compositor musical ha llevado el canal PCM al límite para producir sonidos más naturales. Dado que esto solo podía implementarse mediante software, los juegos debían secuenciar y transmitir su música de forma continua sin superar los límites del Z80. La principal restricción era que, para rellenar su RAM durante la secuenciación, el bus principal debía **bloquearse** primero (impidiendo el envío de comandos o muestras a los chips de audio durante ese intervalo). De lo contrario, podían aparecer anomalías sonoras como silencios, notas congeladas o tasas de muestreo bajas.

Aunque al principio parecía un obstáculo insalvable, algunos juegos lo superaron con técnicas creativas. Por ejemplo, *Toy Story* (1995) de Traveller's Tales empleó el 68000 para secuenciar la música [@audio-gamehut]. Resultaba una tarea tan intensiva que solo podía ejecutarse en puntos muy concretos del juego, como el menú principal.

::: {.subfigures .tabs-nested .toleft .interactive-only}

![Canal PCM.](good_sampling/sonic_pcm){.active video="true" title="Muestra PCM"}

![Todos los canales de audio.](good_sampling/sonic_complete){video="true" title="Completo"}

Vista de osciloscopio de Sonic The Hedgehog 3 (1994). Se rumoreó que su banda sonora fue coautoría de Michael Jackson.

:::

![Vista de osciloscopio de Toy Story (1995).](good_sampling/randy_pcm){.tabs-nested .tabs-nested-last .active .toright video="true" .interactive-only title="Muestra PCM/Completo"}

Aunque se podría argumentar que aún dista mucho de alcanzar la calidad de un CD (16 bits a 44,1 kHz), los avances conseguidos con respecto a sus predecesores merecen sin duda reconocimiento. Y siempre es este tipo de habilidad el que empuja los límites de la música en la siguiente generación de consolas.

### Composición FM asistida

Programar un sintetizador FM ya se consideraba complicado con el panel del Yamaha DX7; imagina los quebraderos de cabeza de componer música únicamente con ensamblador del 68k/Z80...

Por fortuna, Sega distribuyó posteriormente un software para PCs con MS-DOS llamado **GEMS** para simplificar la composición (y depuración) de música para Mega Drive [@audio-gst]. Esta completa herramienta incluía numerosos *patches* (operadores preconfigurados) entre los que elegir, lo que puede explicar por qué algunos juegos tienen sonidos *muy* similares.

Además, su driver de audio permitía a los juegos instanciar más canales de los que el hardware admitía. Mediante el uso de **valores de prioridad**, la consola reproducía la música asignando dinámicamente los canales a los slots disponibles. Asimismo, los canales con alta prioridad pero sin música se omitían automáticamente.

Los canales también incorporaban cierta **lógica** al incluir condicionales en sus datos, lo que permitía que la música 'evolucionara' en función del progreso del jugador en el juego.

### (Bonus) Sonido del Mega CD

He aquí un dato curioso: el complemento Mega CD proporcionaba dos canales extra para Audio CD, entre otras cosas. Uno de sus juegos más famosos, Sonic CD, presentaba composiciones muy impresionantes pero, como todos los juegos, tenía que reproducirse en bucle. El problema era que las pistas en bucle de una lectora de CD a velocidad 1x dejaban huecos perceptibles. Por ello, el juego incluía rellenos de bucle que se ejecutaban desde otro chip PCM mientras el cabezal del CD regresaba al inicio.

Por alguna razón, estos rellenos solo se encuentran en las primeras betas del juego y no llegaron a la versión de lanzamiento. Sin embargo, el remake de 2011 los incluyó finalmente.

![Versión MegaCD (1993).](sonic_cd_original){.toleft audio="true" .interactive-only}

![Versión remasterizada (2011).](sonic_cd_remaster){.toright audio="true" .interactive-only}

## Juegos

Los juegos se escriben principalmente en **ensamblador del 68000**, mientras que el driver de sonido se implementa en **ensamblador del Z80**. Ambos residen en la ROM del cartucho y pueden llegar hasta 4 MB sin necesidad de un [mapeador](nes#going-beyond-existing-capabilities).

### Funciones adicionales

En cuanto a la capacidad de expansión, este diseño no era tan modular como el de la [NES](nes) o la [SNES](super-nintendo). Por ello, complementos posteriores como el 32x (que incluía un nuevo chipset que tomaba el relevo del 68k) tuvieron que saltarse el VDP, lo que obligaba a usar un 'Cable Conector'.

Solo se fabricó un chip personalizado para cartuchos: el **Sega Virtua Processor** (SVP) [@games-virtua], un Samsung SSP1601 rebautizado (un Procesador de Señal Digital de 16 bits). El nuevo chip generaba polígonos que se codificaban posteriormente como tiles para que el VDP los leyera. En cualquier caso, solo un juego salió al mercado con él, ya que el SVP resultó demasiado costoso de fabricar.

### Los primeros pasos en red

Antes de que los servicios en línea fueran ampliamente adoptados y estandarizados, Sega probó suerte con **Sega Meganet**, un servicio de marcación telefónica para juegos. Meganet requería que los usuarios adquirieran un accesorio separado llamado _Sega Mega Modem_, lo conectaran a la parte trasera de la consola (donde se encuentra el conector DE-9) y lo enchufaran a la línea telefónica. Los juegos trataban el módem como un mando más, con la particularidad de comunicarse con él de forma serie [@games-ioports] (en contraposición a la codificación paralela usada para los mandos).

Sea como fuere, esta funcionalidad solo duró un par de años antes de que Sega eliminara el conector DE-9 en posteriores revisiones y discontinuara el servicio por completo.

## Antipiratería / Bloqueo regional

Para bloquear los juegos importados, Sega modificó ligeramente la forma de la ranura del cartucho entre regiones, aunque mantuvo los mismos pines. Los juegos también podían realizar un 'bloqueo regional' comprobando el valor del registro `Version`, que indica el código de región de la consola.

Había dos formas sencillas de eludir esto: adquirir un conversor de cartuchos de terceros o desmontar la consola y puentear los pines de la placa base para alterar el valor del registro `Version`.

En cuanto a las medidas antipiratería por software, la comprobación más sencilla consistía en verificar el tamaño de la SRAM. Los cartuchos pirata solían incluir más espacio del necesario para albergar cualquier título, por lo que los juegos comprobaban el tamaño esperado al arrancar. Los programadores también podían implementar comprobaciones de checksum adicionales en puntos aleatorios del juego para contrarrestar los intentos de eludir las verificaciones iniciales de SRAM. La única forma de vencer esto era encontrar laboriosamente todas las comprobaciones y eliminarlas una a una...

## Eso es todo, amigos
