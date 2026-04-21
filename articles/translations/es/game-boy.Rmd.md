---
short_title: Arquitectura de la Game Boy / Color
long_title: Arquitectura de la Game Boy / Color
name: Game Boy / Color
subtitle: Simple y portátil
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Models:
    - 
      title: "Original"
      caption: "La Game Boy original.<br>Lanzada el 21/04/1989 en Japón, el 31/07/1989 en América y el 28/09/1990 en Europa."
      img_class: reduced-width
      file: international
      active: true
      latex_height: 87
    - 
      title: "Color"
      caption: "La sucesora de nueva generación llamada Game Boy Color.<br>Lanzada el 21/10/1998 en Japón, el 18/11/1998 en América del Norte y el 23/11/1998 en Europa."
      img_class: reduced-width
      file: color
      latex_height: 95
  Motherboard:
    caption: "Mostrando la revisión '04'. Ten en cuenta que 'DMG' es el identificador del modelo original de Game Boy."
  Diagram:
    caption: "Basada en la Game Boy original."
#Historical
aliases:
  - "/projects/consoles/game-boy/"
  - "game-boy-color"
---

## Una introducción rápida

La Game Boy puede imaginarse como una versión portátil de la NES con potencia limitada, aunque verás que introdujo novedades realmente interesantes.

### El análisis variado

La inmensa popularidad de esta consola dio lugar a una amplia variedad de revisiones (la *Game Boy Pocket*, *Light*, e incluso adaptaciones en forma de cartuchos para la [Super Nintendo](super-nintendo)). De hecho, la marca *Game Boy* abarca dos generaciones: dentro de la cuarta encontramos la Game Boy monocroma y sus revisiones, mientras que la siguiente alberga la Game Boy Color (lanzada tras la desaparición de la [Virtual Boy](virtual-boy)).

La buena noticia es que este artículo cubre ambos extremos del espectro. Al final, tendrás una visión clara de cómo funciona la Game Boy y cómo su tecnología evolucionó hasta convertirse en la Game Boy Color.

## {.supporting-imagery}

## CPU

En lugar de colocar múltiples chips estándares en la placa base, Nintendo optó por un **diseño de chip único** para alojar (y ocultar) la mayor parte de los componentes, incluyendo la CPU. Este tipo de diseño se denomina comúnmente **System on a Chip** (SoC) y, en este caso, fue desarrollado específicamente para esta consola, lo que permitió a Nintendo adaptarlo a sus necesidades (eficiencia energética, antipiratería y capacidades de E/S adicionales, entre otras). Además, el SoC de la Game Boy no aparecía en ningún catálogo comercial, lo que complicaba sobremanera la tarea a quienes quisieran copiarlo.

Dicho esto, el SoC de la Game Boy se conoce como **DMG-CPU** o **Sharp LR35902** [@cpu-realboy] y, como su nombre indica, fue fabricado por **Sharp Corporation**. Esta empresa, junto con Ricoh (el [proveedor de CPU de la NES](nes#a-bit-of-context)), mantenía una estrecha relación con Nintendo.

### El núcleo de la CPU

Dentro del DMG-CPU se encuentra el procesador principal: un **Sharp SM83** [@cpu-gekkio], una combinación del popular Zilog Z80 (presente en la [Sega Master System](master-system#cpu)) y el Intel 8080. Funciona a **~4,19 MHz**, más rápido que [la CPU de ~1 MHz de la NES](nes#cpu), aunque recuerda que las velocidades de reloj [pueden ser engañosas](master-system#relative-comparison).

![El chip DMG-CPU en la placa base de mi Game Boy.](dmg_cpu.png){latex_width="90%"}

Cuando analicé la [Master System](master-system), expliqué que el Z80 es en esencia un superconjunto del 8080. Entonces, ¿qué retiene y qué omite el SM83 de ambos? [@cpu-z80_comparison]

- Ni los registros `IX` e `IY` del Z80 ni las instrucciones `IN` y `OUT` del 8080 están presentes. Esto significa que los [puertos de E/S](master-system#accessing-the-rest-of-the-components) no están disponibles. Desconozco si fue una medida de ahorro, pero lo que sí está claro es que los componentes deben estar **completamente mapeados en memoria** [@cpu-fayzullin].
- Solo se implementa el conjunto de registros del Intel 8080. En consecuencia, hay **únicamente siete registros de propósito general**, frente a los 14 del Z80 (gracias a su conjunto 'alternativo' adicional).
- Solo se implementan partes del conjunto de instrucciones extendido del Z80; concretamente, las instrucciones de manipulación de bits.

Sharp también incorporó **algunas instrucciones propias**, ausentes tanto en el Z80 como en el 8080, que optimizan ciertas operaciones relacionadas con la forma en que Nintendo y Sharp organizaron el hardware. Un ejemplo es la nueva instrucción `LDH` (del inglés 'load high' [@cpu-ldh]), diseñada específicamente para acceder a los últimos 256 bytes del mapa de memoria (donde las direcciones comienzan en `$FF00`) y, lo más importante, ocupa un byte menos (lo que la hace ligeramente más rápida).

#### El efecto Color

![Placa base de la Game Boy Color [@photography-amos].](motherboard_cgb.png){.tabs-nested .active title="Original"}

![La misma imagen con las partes importantes marcadas.](motherboard_cgb_marked.png){.tabs-nested-last title="Marcado"}

Casi una década después, tras el abandono de la [Virtual Boy](virtual-boy) y su [hardware de vanguardia](virtual-boy#cpu), llegó una humilde sucesora: la **Game Boy Color**. Dentro de ella hay un nuevo SoC denominado **CPU CGB** que incorpora varias novedades. Sin embargo, su núcleo de CPU SM83 permanece prácticamente sin cambios, salvo por su velocidad de reloj duplicada (ahora a **~8,38 MHz**).

Cuesta pensar que, tras casi una década, Nintendo mantuviera el mismo núcleo de CPU, pero esta decisión conllevaba las siguientes ventajas:

- Los desarrolladores podían reutilizar las habilidades adquiridas para programar la nueva consola.
- Se ahorraban costes al no tener que rediseñar el sistema para una nueva arquitectura.
- La retrocompatibilidad se conseguía con un esfuerzo mínimo. De hecho, Nintendo la implementó programando dos modos de operación en la CPU CGB:
  - **Modo normal**: el SM83 opera a **~4,19 MHz**.
  - **Modo de doble velocidad**: el SM83 opera a **~8,38 MHz**.

Eso sí, todo esto a costa de adoptar tecnología obsoleta para los estándares de finales de los 90. Basta con echar un vistazo al [estado del mercado de CPU](playstation#tab-1-1-a-bit-of-history) para apreciar las capacidades de las que Nintendo prescindía. Aunque, en su descargo, Nintendo sí lo intentó con el [hardware de la Virtual Boy](virtual-boy#cpu).

### Acceso al hardware

El SM83 mantiene un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**, lo que permite direccionar hasta **64 KB de memoria**. El mapa de memoria se compone principalmente de las siguientes regiones [@cpu-memory_map]:

- Espacio del Game Pak (el cartucho de juego).
- Work RAM (WRAM), High RAM (HRAM) y Video RAM (VRAM).
- Diversas E/S (joypad, audio, gráficos y LCD).
- Controles de interrupción.

No te preocupes por los nombres nuevos: estos componentes se explican a lo largo del artículo.

### Memoria disponible

![Arquitectura de memoria del DMG (Game Boy original). La PPU arbitra el acceso a la VRAM.](_diagrams/dmg-ram.png)

Nintendo instaló **8 KB de RAM** en la placa base, destinada a uso general (de ahí el nombre **Work RAM** o 'WRAM') [@cpu-nintendo]. Vale la pena mencionar que eso supone cuatro veces la cantidad incluida en la [NES](nes#memory).

Hay **127 B** adicionales de RAM dentro del SoC. Se denomina **High RAM** (HRAM) y ofrece un pequeño espacio para datos que pueden recuperarse más rápido (mediante la ya mencionada instrucción `LDH`). La idea es muy similar al modo 'Zero Page' del MOS 6502 [@cpu-zero_page], que también optimizaba el rendimiento en función de la ubicación en memoria. Dicho esto, el bus de la High RAM no es técnicamente más rápido que el de la Work RAM, pero tiene prioridad para la CPU. Verás la importancia de esto en la sección 'Gráficos', cuando hable del Acceso Directo a Memoria (DMA).

![Arquitectura de memoria ampliada del CGB (Game Boy Color).](_diagrams/cgb-ram.png)

Más adelante, con la variante Color, Nintendo amplió la WRAM a **32 KB**. Sin embargo, dado que las capacidades de direccionamiento de la CPU no habían cambiado, integrar toda esa nueva memoria sin agotar el espacio de direcciones no era tarea sencilla. Para resolverlo, los ingenieros de Nintendo recurrieron al [cambio de bancos](nes#going-beyond-existing-capabilities) dentro de la propia consola.

Aunque anteriormente se encontraba en los [cartuchos de NES](nes#cartridgegame-data), la Game Boy Color aplica el mismo principio para acceder a los 24 KB extra de WRAM. El truco es sencillo: los últimos 4 KB del espacio original de 8 KB pueden intercambiarse entre siete bancos distintos. Para que esto funcione, la CPU incluye un registro adicional (llamado `SVBK`) que actúa como selector de banco, y es lo que los desarrolladores deben usar para acceder a la memoria extendida.

## Gráficos

En el mundo gráfico de la Game Boy, todos los cálculos los realiza la CPU, y la **Picture Processing Unit** (PPU) se encarga de renderizarlos. Este es otro componente del SoC DMG-CPU que podría describirse como una versión mejorada del [chip gráfico del predecesor](nes#graphics) (que, curiosamente, comparte el mismo nombre).

::: {.subfigures .side-by-side}

![Frame renderizado por la PPU.](lcd/kirby_white.png){.toleft .border .pixel hardcover_latex_width="70%" paperback_latex_width="64%"}

![Frame mostrado en la pantalla LCD de la Game Boy original.](lcd/kirby_green.png){.toright .border .pixel hardcover_latex_width="70%" paperback_latex_width="64%"}

Kirby's Dream Land (1992), capturado desde el emulador 'bgb' para simular distintos tipos de pantalla.

:::

La imagen se muestra en una pantalla integrada de tipo **Liquid-Crystal Display** (LCD) con una resolución de **160×144 píxeles**. En el caso de la Game Boy monocroma, la LCD solo puede mostrar **4 tonos de gris** (blanco, gris claro, gris oscuro y negro). No obstante, al estar tintada de verde, la imagen presentará un tono ligeramente *verdoso*.

### Un modelo familiar

Si has leído el [análisis de la NES](nes#graphics), puede que recuerdes que la PPU fue diseñada en torno al comportamiento de los monitores CRT (especialmente al funcionamiento de su haz). Esto puede parecer incompatible con una pantalla LCD, aunque esta también debe actualizarse periódicamente. Así pues, la nueva PPU aprovecha este requisito para ofrecer [efectos basados en CRT](nes#tab-1-4-background-split), similares a los que permitieron a los desarrolladores de NES crear contenido imaginativo.

Además, a diferencia de su homólogo doméstico, la Game Boy no se alimenta de la corriente alterna que proporcionan los enchufes de pared —esa que varía según el país o la región—. En consecuencia, sus circuitos analógicos no están ligados a la red eléctrica local. Dicho de otro modo: mientras la NES debía adaptarse a configuraciones de 60 Hz y 50 Hz, la Game Boy funciona con las mismas **cuatro pilas AA** independientemente del lugar del mundo. Por lo tanto, todas las Game Boys comparten las mismas velocidades de reloj de CPU y una tasa de refresco de **59,7 Hz**, lo cual facilita la vida a desarrolladores y usuarios por igual.

### Organizando el contenido

![Arquitectura de memoria de la PPU.](_diagrams/ppu.png)

La PPU está conectada exclusivamente a **8 KB de Video RAM** (también denominada 'VRAM' o 'Display RAM'). Esos 8 KB contienen la mayor parte de los datos que la PPU necesita para renderizar los gráficos. Los demás datos se almacenan dentro de la propia PPU, pues requieren tasas de acceso más rápidas.

La PPU también arbitra el acceso de la CPU a la VRAM, y es el juego quien se encarga de rellenar las distintas áreas con el tipo de datos correcto. Además, la PPU pone a disposición registros que permiten definir cómo se organiza la información gráfica. No obstante, hay muchas reglas que respetar (explicadas en las secciones siguientes).

### Construyendo el frame

El frame final que se ve en la pantalla LCD está compuesto por **tres capas superpuestas**, cada una pensada para un tipo de uso diferente.

Veamos cómo la PPU logra dibujar píxeles en pantalla. Para la demostración se usará *Super Mario Land 2*.

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2 latex_subfigure_width="0.46"}

![Una tabla de patrones con múltiples tiles.](ppu_mario/tiles.png){.active .border title="Todo"}

![Un único tile.](ppu_mario/tiles_single.png){.border title="Individual"}

Tiles encontrados en la VRAM.

:::

La PPU utiliza **tiles** como ingrediente básico para renderizar gráficos; en concreto, **sprites y fondos** [@graphics-overview].

Los tiles son sencillamente **mapas de bits de 8×8 píxeles**. Cada uno ocupa 16 bytes y se almacena en la VRAM, en una región denominada **Tile set** o 'Tile pattern table'. El color de cada píxel corresponde a uno de los cuatro tonos de gris disponibles, seleccionado mediante una paleta de 'color'. El uso de paletas también permite cambiar los colores sin necesidad de modificar el Tile set.

Las Game Boys monocromas disponen de registros suficientes para definir **hasta tres paletas**, aunque su uso está restringido según el tipo de capa que se renderiza (explicado en las secciones siguientes). Como ya se indicó, solo hay cuatro colores/tonos disponibles, por lo que un único registro de 8 bits puede albergar una paleta de cuatro tonos sin problema.

Además, los tiles se agrupan en **dos tablas de patrones**.

Para construir la imagen, los tiles se recogen en otro tipo de tabla denominada **Tile map**, que indica a la PPU dónde renderizar cada tile. Se almacenan dos mapas para construir **distintas capas** del frame.

Las secciones siguientes explican cómo se usan los Tile maps para ensamblar estas capas.

#### Capa de fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=3 latex_subfigure_width="0.49"}

![Mapa de fondo asignado en la VRAM.](ppu_mario/background.png){.active .border title="Completa"}

![Área seleccionada del mapa de fondo. La selección incluye una porción sin usar en la parte superior, que quedará cubierta por la capa *Ventana*.](ppu_mario/background_selected.png){.border title="Selección"}

![Capa de fondo mostrada.](ppu_mario/background_rendered.png){.border title="Renderizado"}

Proceso de renderización de la capa de fondo.

:::

La capa de fondo es un mapa de **256×256 píxeles** (32×32 tiles) que contiene **tiles estáticos**. Sin embargo, recuerda que solo 160×144 píxeles son visibles en pantalla, por lo que el juego decide qué zona mostrar en cada momento. Los juegos también pueden desplazar el área visible durante el gameplay, que es como se consigue el **efecto de desplazamiento**.

Uno de los dos Tile maps puede emplearse para construir la capa de fondo. Además, solo hay **una paleta** disponible para esta capa.

#### Sprites {.tab}

![Capa de sprite renderizada.](ppu_mario/sprite.png){.tab-float .border .pixel latex_width="90%"}

Los sprites, también conocidos como 'Objects', son tiles que pueden moverse de forma independiente por la pantalla. También pueden superponerse entre sí o aparecer detrás del fondo; lo que se muestra en pantalla viene determinado por su atributo de prioridad.

Esta capa dispone de un color extra: **Transparente**. Esto implica que los sprites solo pueden mostrar tres tonos de gris en lugar de cuatro, pero a cambio esta capa cuenta con **dos paletas dedicadas**.

La **Object Attribute Memory** (OAM) es un mapa almacenado dentro de la PPU que especifica qué tiles se usarán como sprites; su ubicación dentro de la PPU agiliza el acceso durante el renderizado. Por esta razón, **todos los sprites se definen en la OAM** (en lugar de en un Tile map de la VRAM). Los juegos suelen rellenar esta región llamando a la **unidad OAM DMA** del chip, que obtiene los datos de la RAM principal o de la ROM del juego y los transfiere a la OAM. Mientras la DMA está activa, la CPU no puede acceder a la memoria externa, de ahí la importancia de aprovechar la High RAM durante ese período.

Aparte del índice de tiles, cada entrada de la OAM contiene los siguientes atributos: posición X-Y, paleta elegida, prioridad y flip flags (que permiten reflejar el tile vertical y horizontalmente).

La PPU está limitada a renderizar **hasta diez sprites por línea de escaneo** y **hasta 40 por frame**; si se supera este límite, algunos sprites no se dibujarán.

#### Ventana {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2}

![Mapa de Ventana asignado.](ppu_mario/window.png){.active .border hardcover_latex_width="70%" paperback_latex_width="64%" title="Completa"}

![Mapa de Ventana mostrado. El juego la activa durante las últimas líneas de escaneo, por lo que solo se renderizan las primeras filas en la parte inferior de la pantalla.](ppu_mario/window_rendered.png){.border hardcover_latex_width="70%" paperback_latex_width="64%" title="Selección"}

Proceso de renderización del mapa de Ventana.

:::

La capa Ventana es un mapa de **160×144 píxeles** (que cubre toda la pantalla) y renderiza tiles sobre el fondo y los sprites. Dicho esto, esta capa **no se desplaza**.

El Tile map restante puede asignarse a la capa Ventana, que además comparte la misma paleta.

En definitiva, puede parecer una característica un tanto limitada: dado que la Ventana carece de transparencia y por tanto oscurece completamente las capas inferiores, cabe preguntarse para qué sirve. La respuesta es que, combinada con efectos de trama temporizados, la Ventana puede emplearse **parcialmente** en distintas zonas de la pantalla. ¿Recuerdas las [complejas rutinas sincronizadas](nes#tab-1-4-background-split) que los juegos de NES necesitaban para mostrar las estadísticas del jugador? La Ventana resuelve ese problema, y la PPU de la Game Boy lo gestiona de forma automática.

Por consiguiente, los juegos suelen usar la capa Ventana para mostrar contadores de vida, puntuaciones y otra información persistente.

#### Resultado {.tab}

![Resultado final. *Tachán.*](ppu_mario/result.png){.tab-float .border .pixel latex_width="90%"}

Una vez finalizado el frame, es hora de pasar al siguiente. Sin embargo, la CPU no puede modificar las tablas mientras la PPU está leyendo la VRAM, por lo que el sistema dispone de interrupciones que se activan cuando la PPU está inactiva. Este comportamiento te resultará familiar de la era NES.

Cuando se completa una sola línea de escaneo, comienza el período de **Horizontal Blank**, que permite a los desarrolladores modificar la parte del frame que aún no se ha dibujado.

Cuando se completan todas las líneas de escaneo, comienza el período de **Vertical Blank** y se activa una interrupción dedicada. En ese momento, el juego puede actualizar los gráficos para el siguiente frame.

Hay un estado adicional denominado **OAM search** que se inicia al comienzo de cada línea de escaneo. En ese punto, la PPU determina qué sprites se mostrarán en esa línea, por lo que el juego puede actualizar cualquier región excepto la OAM.

#### El efecto de tambaleo {.tabs-close}

![Super Mario Land 2: 6 Golden Coins (1992), jugando con los controles de desplazamiento X e Y a mitad de pantalla.](mario_wobble.png){.border .pixel latex_width="90%"}

La inclusión de la capa Ventana y las interrupciones adicionales dieron pie a contenidos y efectos únicos. Por ejemplo, las interrupciones horizontales permitían modificar el frame antes de que terminara de completarse, de modo que podía aplicarse un valor de desplazamiento diferente a cada línea, haciendo que cada fila se desplazara a su propio ritmo [@graphics-lyc].

El resultado es un interesante *efecto de tambaleo* (no estoy seguro de que ese sea su nombre oficial, pero me parece suficientemente descriptivo).

### Las adiciones de color

Hasta aquí hemos analizado la Game Boy original. Veamos ahora las novedades del modelo denominado 'Color'.

#### Modos de operación

![The Legend of Zelda: Link's Awakening DX (1998).<br>Un juego híbrido de Game Boy Color ejecutándose en modo CGB.](ppu_color/zelda.png){.toleft .border .pixel hardcover_latex_width="71%" paperback_latex_width="90%"}

![Super Mario Land 2, ejecutándose en una Game Boy Color. El juego corre en modo DMG, pero la consola aplica una paleta colorizada igualmente.](ppu_color/dmg_mario.png){.toright .border .pixel hardcover_latex_width="71%" paperback_latex_width="90%"}

La PPU de la Game Boy Color se comporta en esencia como un superconjunto de la original. Con todo, Nintendo quería que los usuarios de Color percibieran mejoras incluso con juegos exclusivamente monocromáticos. Por ello, para mantener la compatibilidad, la nueva PPU ofrece **dos modos de operación**:

- **Modo CGB**: modo PPU extendido con todas las mejoras visuales de los juegos de Game Boy Color.
- **Modo DMG**: modo tradicional con todos los extras deshabilitados. Sin embargo, como verás en la sección 'Sistema operativo', los juegos monocromáticos igualmente se mejoran con paletas de color.

#### Organizando el (nuevo) contenido

La placa base del CGB alberga ahora **16 KB de VRAM**, el doble de la cantidad original. Debido a las limitaciones de direccionamiento de la CPU, esta nueva configuración se implementa en forma de **dos bancos de 8 KB**, con un nuevo registro (`VBK`) que actúa como selector. Por su parte, la PPU puede acceder a los dos bancos simultáneamente. En la práctica, los programadores solo necesitan rellenar los bancos de VRAM con ayuda de `VBK` y luego indicar en el Tile map en qué banco reside cada tile, dejando el resto a la PPU.

Dicho esto, ¿qué se puede hacer con la VRAM extra? Muchas cosas:

- Almacenar el doble de tiles.
- Almacenar paletas adicionales, que también ofrecen una gama de colores más amplia.
- Ampliar el espacio de metadatos de los tiles para codificar más efectos y referenciar paletas adicionales.

##### Los visuales

Gracias a la nueva PPU, los programadores pueden diseñar paletas con una gama de **32.768 colores**.

En primer lugar, los desarrolladores deben rellenar una nueva área denominada **Palette Memory**, que almacena hasta **dieciséis paletas de colores** (la mitad para el fondo y la ventana, y la otra mitad para los sprites), cada una con **cuatro colores** [@cpu-nintendo]. Cada entrada ocupa 16 bits (2 bytes), aunque solo se usan 15. La Palette Memory no es directamente direccionable por la CPU; en su lugar, un nuevo registro actúa como búfer para escribir en esta memoria (metodología presente también en la [Super Nintendo](super-nintendo#organising-the-content)). Así es, en definitiva, como los juegos en color definen sus paletas.

Dicho esto, los tiles de fondo y ventana pueden usar cualquiera de las ocho paletas disponibles. Lo mismo se aplica a los tiles de sprite, con la diferencia de que están limitados a paletas de tres colores, pues una entrada sigue reservada para el color 'transparente'.

##### El espacio extra

Por otro lado, **los tile sets doblan ahora su tamaño**, lo que permite a los programadores almacenar el doble de tiles en la VRAM. **Los Tile maps de fondo y ventana también se han ampliado**, incorporando metadatos adicionales que expanden las capacidades de estas capas. Por ejemplo, los tiles de fondo y ventana ahora pueden **invertirse horizontal y verticalmente**, lo que evita tener que almacenar gráficos duplicados en la VRAM (y, a su vez, permite renderizar más contenido único).

Además, la CPU CGB incluye **una unidad DMA adicional**, capaz de copiar datos del Game Pak o de la WRAM a la VRAM. Opera en dos modos [@cpu-nintendo]:

- **DMA de propósito general**: las transferencias pueden ocurrir en cualquier momento; el DMA toma prioridad sobre otros accesos a memoria. Los programadores deben por ello planificar cuándo usar este componente (durante o fuera del escaneo) y cuántos datos copiar, pues un uso incorrecto puede provocar desgarro de imagen (ya que el acceso a la VRAM quedará bloqueado durante la transferencia).
- **DMA de Horizontal Blank (H-Blank)**: las transferencias solo se producen durante los períodos de H-Blank. Esto evita artefactos en pantalla, pero limita las transferencias a lotes de 16 bytes y las pausa durante el escaneo LCD.

Una vez más, esta unidad ofrece a los programadores nuevas posibilidades para crear contenido más rico, aprovechando períodos que de otro modo permanecerían inactivos.

## Audio

El audio lo gestiona la **Audio Processing Unit** (APU, o 'Unidad de Procesamiento de Audio'), un chip [Generador de Sonido Programable (PSG)](master-system#audio) con **cuatro canales** [@audio-overview].

Curiosamente, este es uno de los pocos componentes que no evolucionó entre revisiones de la Game Boy. De hecho, a diferencia de la CPU, no puede acelerarse: modificar la velocidad de los osciladores no mejora la calidad del sonido, sino que simplemente cambia el tono. Para mejorar sus capacidades habría hecho falta más circuitería, lo que también habría encarecido el coste.

### Funcionalidad

Veamos lo que ofrece. Cada uno de los cuatro canales está reservado para un solo tipo de forma de onda:

#### Pulso {.tabs .active}

::: {.subfigures .tabs-nested .tab-float max_subfigures=3 latex_subfigure_width="0.49"}

![Canal de pulso 1.](pulse_single_1){.active video="true" title="Pulso 1"}

![Canal de pulso 2.](pulse_single_2){video="true" title="Pulso 2"}

![Todos los canales de audio.](pulse_full){video="true" title="Completo"}

Osciloscopio de Pokémon Rojo/Azul (1996).

:::

Las ondas de pulso tienen un sonido *beep* muy característico y se utilizan principalmente para **melodías y efectos de sonido**.

La APU reserva dos canales, cada uno dedicado a una onda de pulso. Estos ofrecen cuatro tonos diferentes, obtenidos al variar la anchura del pulso. El primer canal cuenta con un exclusivo **control de barrido**.

Dado el número limitado de canales, las melodías a menudo se interrumpen cuando se activan efectos de sonido durante el gameplay. Esto resulta especialmente notorio en juegos como *Pokémon Rojo/Azul*, donde durante un combate el grito del Pokémon se apodera de todos los canales de música.

#### Onda {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=2 latex_subfigure_width="0.49"}

![Canal de onda.](wave_single){.active video="true" title="Onda"}

![Todos los canales de audio.](wave_full){video="true" title="Completo"}

Osciloscopio de Pokémon Rojo/Azul (1996).

:::

El tercer canal de la APU permite definir una **forma de onda personalizada**. La onda se compone de 32 muestras de 4 bits almacenadas en una tabla de ondas.

Este canal también permite controlar el volumen y la frecuencia; esta última permite producir distintas notas musicales a partir de la misma entrada.

#### Ruido {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=2 latex_subfigure_width="0.49"}

![Canal de ruido.](noise_single){.active video="true" title="Ruido"}

![Todos los canales de audio.](noise_full){video="true" title="Completo"}

Osciloscopio de Pokémon Rojo/Azul (1996).

:::

El ruido es básicamente un conjunto de formas de onda aleatorias que suenan como estática blanca. La APU le dedica uno de sus cuatro canales.

Los juegos suelen emplearlo para percusión o efectos de *ambiente*.

Este canal ofrece solo dos tonos: uno produce *estática limpia* y el otro *estática robótica*. Su frecuencia también puede ajustarse.

### Secretos y limitaciones {.tabs-close}

El mezclador produce sonido estéreo, lo que significa que los canales pueden asignarse al lado izquierdo o derecho. En otras palabras, el subsistema de audio ofrece **controles de paneo**. Esto solo se aprecia a través de los auriculares, pues el altavoz integrado es mono.

Además, el hardware del mezclador está conectado a un pin dedicado del cartucho, lo que permite al cartucho transmitir un **canal adicional**, siempre que este produzca el sonido analógico (algo que solo es posible con hardware extra). Sin embargo, ningún juego del mercado llegó a usar esta característica, y tampoco encontrarás este pin en el [Game Boy Advance](game-boy-advance) (ni en su [subsistema compatible con versiones anteriores](game-boy-advance#becoming-a-game-boy-color)).

## Sistema operativo

A diferencia de la NES/Famicom, que arrancaba directamente en el juego, la Game Boy fue diseñada para arrancar siempre desde una **ROM interna de 256 bytes**, y solo entonces saltar al código del juego. El proceso de arranque es el siguiente [@operating_system-boot]:

1. Al encender la consola, la CPU comienza leyendo desde la dirección **0x0000** (la ubicación de la ROM de la Game Boy).
2. Se inicializan la RAM y la APU.
3. El logotipo de *Nintendo* se copia de la ROM del cartucho a la Display RAM y se dibuja en el borde superior de la pantalla. Si no hay cartucho insertado, el logo mostrará tiles basura. Lo mismo puede ocurrir si el cartucho está mal insertado.
4. El logotipo se desplaza hacia abajo y se reproduce el icónico sonido *po-ling*.
5. El logotipo del juego se compara con el almacenado en la ROM de la consola. A continuación, se realiza un rápido **checksum** en la cabecera de la ROM del cartucho para confirmar que está correctamente insertado. Si alguna de estas comprobaciones falla, la consola se congela.
6. La ROM de la consola desaparece del mapa de memoria.
7. La CPU comienza a ejecutar el juego.

Curiosamente, el logotipo de *Nintendo* que aparece en pantalla no se borra de la VRAM, lo que brinda a los juegos la oportunidad de aplicar animaciones y efectos para hacer la transición a su propio logo.

::: {.subfigures .side-by-side}

![Inicio de la animación. El logotipo aparece desde arriba y se desliza hacia abajo.](boot/start.png){.toleft hardcover_latex_width="70%" paperback_latex_width="64%" .border .pixel}

![Final de la transición. Si las comprobaciones han pasado, la consola ejecuta el juego; de lo contrario, se queda congelada.](boot/end.png){.toright hardcover_latex_width="70%" paperback_latex_width="64%" .border .pixel}

La animación de arranque de la Game Boy original.

:::

### La secuencia revisada

![La pantalla de inicio de la Game Boy Color. El logotipo ya no se desliza, sino que presenta un efecto de arcoíris.](boot_cgb.png){.border latex_width="90%" .pixel}

Una vez más, en el caso de la Game Boy Color encontramos cambios drásticos en el contenido de la ROM. Por ejemplo, el tamaño de la ROM es ahora de **2 KB** [@operating_system-boot_cgb] y las rutinas exhiben un nuevo comportamiento:

- La secuencia de arranque verifica si el juego insertado es exclusivo de Game Boy o de Game Boy Color, y establece los registros correspondientes para activar el modo DMG o CGB. Para ello, el código de arranque examina metadatos específicos del juego (dentro de la ROM del cartucho) que están estructurados de forma diferente en los juegos CGB.
- Si se inserta un juego DMG, el programa de arranque también rellena la Palette RAM con **paletas calculadas**, basadas en un algoritmo sencillo pero ingenioso que también se vale de los metadatos del juego. Esto es lo que dota a los juegos monocromáticos de una apariencia a todo color al ejecutarse en la consola de nueva generación.
  - En esta fase, el usuario puede pulsar una combinación de botones para modificar la paleta elegida por el código de arranque.
- Por alguna razón, el logotipo de Nintendo también se copia a la HRAM, y la etapa del checksum solo verifica la primera mitad del logotipo en la HRAM.

## Juegos

Una de las principales limitaciones del hardware de consumo de los años 70 y 80 eran sus restricciones de programación: aunque prometedores, los lenguajes de alto nivel como C o Pascal aún no estaban preparados para aplicaciones con exigencias de rendimiento, pues los compiladores carecían de madurez y los juegos debían exprimir el máximo rendimiento posible. Como resultado, los juegos de Game Boy se escribían principalmente en **lenguaje ensamblador**.

![El diseño original del Game Pak [@photography-amos].](gamepak_original.webp){.no-borders latex_width="80%"}

Por otro lado, los juegos comerciales se distribuían en forma de **Game Paks**, los cartuchos diseñados por Nintendo para la serie Game Boy. En cuanto a capacidad, los juegos tenían un tamaño máximo de **32 KB**, limitado por el espacio de direcciones disponible. Sin embargo, gracias a un **Memory Bank Controller** ([mapeador](nes#going-beyond-existing-capabilities)), los juegos podían alcanzar tamaños mayores. El Game Pak más grande del mercado contaba con una ROM de 1 MB (o 8 MB en el caso del Game Boy Color).

Por último, algunos Game Paks también incluían un reloj en tiempo real y/o SRAM adicional, junto con una batería externa para almacenar partidas.

### Cartuchos modernizados

::: {.subfigures .side-by-side}

![Diseño negro [@photography-amos], que indica 'Game Boy Color mejorado'.](gamepak_enhanced.webp){.toleft .no-borders hardcover_latex_width="68%" paperback_latex_width="60%"}

![Diseño translúcido [@photography-amos], que indica 'exclusivo de Game Boy Color'.](gamepak_gbc.webp){.toright .no-borders hardcover_latex_width="68%" paperback_latex_width="60%"}

Revisiones de los Game Pak de la era Color.

:::

Con la incorporación del modo de operación adicional de la Game Boy Color, Nintendo definió **tres tipos distintos** de juegos de Game Boy:

- **Game Boy**: totalmente compatible con todos los modelos de Game Boy. Siempre se ejecuta en modo DMG.
- **Game Boy Color mejorado**: un híbrido que se adapta a la consola anfitriona. Aunque es totalmente compatible con el modelo monocromo, se potencia visualmente al ejecutarse en modo CGB en la Game Boy Color. Sin embargo, la funcionalidad general sigue limitada para garantizar la compatibilidad con la primera Game Boy.
- **Exclusivo de Game Boy Color**: solo compatible con la Game Boy Color, pero optimizado para aprovechar al máximo su hardware.

A los tres tipos se les asignaron colores oficiales para facilitar su identificación, aunque algunos juegos como Pokémon y Donkey Kong contaron con diseños alternativos.

### Comunicación externa

Por primera vez, los juegos podían comunicarse con hardware externo mediante un cable **Game Boy Link**, lo que hizo posible el juego multijugador y el uso de accesorios. El cable se conectaba al subconector de 6 pines de la consola, con el otro extremo unido a otra Game Boy o a un accesorio.

La interfaz se basa en el protocolo **Serial Peripheral Interface** (SPI), donde una Game Boy se designa como **maestra** (generando la señal de reloj) y la otra como **esclava**. En cada transferencia, tanto la maestra como la esclava intercambian un paquete de 8 bits (1 byte) [@games-spi].

![El cable Game Boy Link, con distintos adaptadores para múltiples variantes de la consola [@photography-amos]. Desde el lanzamiento de la Game Boy original, las revisiones posteriores miniaturizaron el conector manteniendo el mismo número de pines.](link_cable.png){.no-borders}

La Game Boy original transfería datos a 8 Kbit/segundo (1 KB/seg), mientras que la variante Color podía alcanzar hasta 512 Kbit/segundo (64 KB/seg), lo que se conocía como modo de 'alta velocidad'.

En un momento dado, Nintendo también comercializó un 'adaptador para 4 jugadores' que permitía a hasta cuatro Game Boys intercambiar datos simultáneamente. El protocolo base (SPI) se mantuvo, pero el adaptador —que actuaba como maestro— implementó una capa de comunicación adicional que los juegos debían seguir para emitir datos a las demás consolas [@games-fourplyr].

#### Métodos inalámbricos

El modelo Color también incorporó un **emisor y receptor de infrarrojos** (formado por un LED y un fototransistor, respectivamente) [@cpu-nintendo]. Esto permitió intercambiar datos de forma inalámbrica entre Game Boy Colors, como se vio en juegos como Pokémon Oro y similares. Sin embargo, el sistema no implementaba ningún protocolo de comunicación: solo un registro único (`RP`) que codificaba la acción del sensor IR (emitir o recibir), el bit (`0` o `1`) transferido y el último bit recibido. Para facilitar las cosas a los desarrolladores, Nintendo incluyó una implementación de referencia en el manual oficial de desarrollador de Game Boy [@cpu-nintendo].

## Antipiratería

Como has visto en la sección 'Sistema operativo', la consola nunca ejecuta un juego de inmediato: primero realiza una serie de comprobaciones que **impiden la ejecución de cartuchos no autorizados** y también verifican que el cartucho **se ha insertado correctamente**.

Para superar estas comprobaciones, los juegos debían incluir una copia del logotipo de Nintendo (en forma de tiles) en la cabecera de su ROM [@games-dhole]. De este modo, Nintendo podía valerse de las leyes de **copyright y marca registrada** para controlar la distribución. *Bastante ingenioso, ¿verdad?*

Por el contrario, *Sega v. Accolade* otorgó posteriormente a las empresas el derecho a usar logotipos con copyright para este tipo de requisito, al considerarse uso legítimo.

Dicho esto, los juegos podían implementar medidas antipiratería adicionales, como verificar el tamaño de la SRAM (generalmente mayor en copias piratas) o realizar un checksum de la ROM en puntos aleatorios del gameplay para detectar modificaciones en el código del juego.

## Eso es todo amigos
