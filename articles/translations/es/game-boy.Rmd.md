---
short_title: Arquitectura de la Game Boy / Game Boy Color
long_title: Arquitectura de la Game Boy / Game Boy Color
name: Game Boy / Game Boy Color
subtitle: Simple y portátil
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Models:
    - 
      title: "Original"
      caption: "La Game Boy original.<br>Lanzada el 21/04/1989 en Japón, el 31/07/1989 en Estados Unidos y el 28/09/1990 en Europa."
      img_class: reduced-width
      file: international
      active: true
    - 
      title: "Color"
      caption: "La sucesora de nueva generación llamada Game Boy Color.<br>Lanzada el 21/10/1998 en Japón, 18/11/1998 en América del Norte, 23/11/1998 en Europa."
      img_class: reduced-width
      file: color
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

La Game Boy puede ser entendida como una versión portátil de la NES con potencia limitada, pero pronto se verá que incluye nueva funcionalidad muy interesante.

### El análisis variado

La inmensa popularidad de esta consola resultó en una variedad de revisiones (es decir, la Game Boy Pocket, Light, e incluso en forma de cartuchos para la [Super Nintendo](super-nintendo)). De hecho, la marca Game Boy cubre dos generaciones. Dentro de la cuarta generación encontramos la Game Boy monocroma y sus revisiones, y en la siguiente generación reside la Game Boy Color (lanzada después de la desaparición de la [Virtual Boy](virtual-boy)). La buena noticia es que este artículo cubre ambos extremos. Entonces, al final, tendrás una buena comprensión de cómo funciona la Game Boy y cómo su tecnología evolucionó para convertirse en la Game Boy Color.

## {.supporting-imagery}

## CPU

En lugar de colocar chips estándares en la placa base, Nintendo optó por un único chip para alojar (y ocultar) la mayor parte de los componentes, incluyendo la CPU. Este tipo de chip se llama **System On a Chip** (SoC) y, en este caso, ha sido específicamente diseñado para esta consola, permitiendo a Nintendo adaptarlo a sus necesidades (eficiencia energética, anti-piratería y E/S extra, entre otras cosas). Al mismo tiempo, este chip no se encuentra en ningún catálogo comercial, lo que significa que a los competidores de entonces les resultaba más difícil clonarlo.

Dicho esto, el SoC que se encuentra en la Game Boy se conoce como **DMG-CPU** o **Sharp LR35902** [@cpu-realboy] y, como indica el nombre, es fabricado por **Sharp Corporation**. Esta empresa, junto con Ricoh (el [proveedor de CPU de NES](nes#a-bit-of-context)), disfrutaba de una estrecha relación con Nintendo.

### El núcleo de la CPU

Dentro del DMG-CPU, el procesador principal es un **Sharp SM83** [@cpu-gekkio] y es una mezcla entre el Z80 (la misma CPU utilizada en la [Sega Master System](master-system#cpu)) y el Intel 8080. Funciona a **~4.19 MHz**, que es más rápido que la CPU promedio de 1 MHz, pero recuerda que las velocidades de reloj [pueden ser engañosas](master-system#relative-comparison).

![El DMG-CPU, encontrado en la placa base de la Game Boy.](dmg_cpu.png)

Ahora, cuando hice el [análisis de la Master System](master-system), expliqué que el Z80 es en sí mismo un superconjunto del 8080. Entonces, ¿qué tiene y qué le falta al SM83 de esos dos? [@cpu-z80_comparison]

- Ni los registros `IX` o `IY` del Z80 ni las instrucciones `IN` o `OUT` del 8080 están incluidos. Esto significa que los [puertos de E/S](master-system#accessing-the-rest-of-the-components) no están disponibles. No sé a ciencia a cierta si es una medida tomada para reducir costes, pero lo que sí sé, es que los componentes tendrán que ser **completamente mapeados en memoria** [@cpu-fayzullin].
- El conjunto de registros del Intel 8080 es el único que está implementado. En consecuencia, hay **solo siete registros de propósito general**, a diferencia del Z80 con sus 14 registros (debido a la adición de un conjunto 'alternativo').
- Incluye algunas de las instrucciones extendidas del Z80 (solo instrucciones de manipulación de bits).

Para finalizar, también se añadieron **nuevas instrucciones** que no están presentes ni en el Z80 ni en el 8080. Optimizan ciertas operaciones relacionadas con la forma en que Nintendo/Sharp organizó el hardware. Un ejemplo es la nueva instrucción `LDH` (que significa 'cargar desde la memoria alta' [@cpu-ldh]), que ha sido específicamente incluida para operar en los últimos 256 bytes del mapa de memoria (donde las direcciones comienzan en `$ff00`) y, lo más importante, ocupa un byte menos (y, por lo tanto, es ligeramente más rápida).

#### El efecto Color

![Placa base de la Game Color [@photography-amos].](motherboard_cgb.png){.tabs-nested .active title="Original"}

![La misma imagen con las partes importantes marcadas.](motherboard_cgb_marked.png){.tabs-nested-last title="Marcado"}

Casi 10 años después, tras el abandono de la [Virtual Boy](virtual-boy) y su [hardware de vanguardia](virtual-boy#cpu), llegó una humilde sucesora: la **Game Boy Color**. Dentro de ella, hay un nuevo SoC denominado **CPU CGB** que lleva algunas adiciones. Sin embargo, su núcleo de CPU SM83 sigue siendo el mismo, excepto por su velocidad de reloj duplicada (ahora funcionando a **~8.38 MHz**).

Es difícil pensar que después de una década Nintendo aún incluiría la misma CPU, pero tal decisión viene con las siguientes ventajas:

- Los desarrolladores pueden reutilizar las habilidades adquiridas para programar la nueva consola.
- Hay un ahorro de costos al no tener que rediseñar su sistema para trabajar con una nueva arquitectura.
- Se hace posible la compatibilidad hacia atrás sin un esfuerzo considerable. De hecho, Nintendo lo implementó programando dos modos de operación en la CPU CGB:
  - **Modo normal**: El SM83 opera a **~4.19 MHz**.
  - **Modo de doble velocidad**: El SM83 opera a **~8.38 MHz**.

Sin embargo, esto viene a costa de adoptar tecnología obsoleta para los estándares de finales de los 90. Solo tienes que mirar el [estado del mercado de CPU](playstation#tab-1-1-a-bit-of-history) para notar las capacidades que Nintendo se estaba perdiendo (para ser justos, Nintendo lo intentó con el [Virtual Boy](virtual-boy)).

### Acceso al hardware

El SM83 mantiene un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**, por lo que se puede direccionar hasta **64 KB de memoria**. El mapa de memoria está compuesto principalmente por los siguientes puntos finales [@cpu-memory_map]:

- Espacio del Game Pak (el cartucho de juego).
- Work RAM (WRAM), High RAM (HRAM) y Display RAM (VRAM).
- E/S (joypad, audio, gráficos y LCD).
- Controles de interrupción.

Esto se explicará a lo largo de este artículo.

### Memoria Disponible

![Arquitectura de memoria del DMG (Game Boy original). La PPU arbitra el acceso a la VRAM.](dmg-ram.png)

Nintendo instaló **8 KB de RAM** en la placa base, esto es para uso general (que llamaron **Work RAM** o 'WRAM') [@cpu-nintendo]. Fíjese que es cuatro veces más que la cantidad incluida en la [NES](nes).

Hay un adicional de **127 B** de RAM en el SoC. Se llama **High RAM** o 'HRAM', y proporciona un pequeño espacio para datos que se puede acceder más rápido a través de la instrucción `LDH` única del SM83. Esto es muy similar al modo 'Zero Page' del 6502 [@cpu-zero_page], que también optimizó el rendimiento basado en la ubicación de la memoria. Ahora, la High RAM no es técnicamente más rápida de acceder que la RAM general, pero es un área priorizada para la CPU. Verás lo que esto significa cuando llegues a la sección 'Gráficos', donde hablo del componente DMA.

![Arquitectura de memoria ampliada del CGB (Game Boy Color). De nuevo, la PPU arbitra el acceso a la VRAM.](cgb-ram.png)

Más tarde, con la variante Color, Nintendo amplió la WRAM a **32 KB**. Sin embargo, dado que la CPU se mantuvo sin cambios (particularmente sus capacidades de direccionamiento), no es posible conectar toda la nueva memoria sin desbordar primero el espacio de direcciones disponible. Para solucionar esto, los ingenieros de Nintendo implementaron [cambio de banco](nes#going-beyond-existing-capabilities). Originalmente encontrado en los [cartuchos de NES](nes#cartridgegame-data), la Game Boy Color utiliza el mismo principio para acceder a esos 32 KB utilizando solo 8 KB de espacio de memoria. El truco es simple: los últimos 4 KB se pueden intercambiar utilizando siete bancos diferentes. En consecuencia, la CPU agrupa un registro adicional (llamado `SVBK`) que actúa como el cambiador de banco, esto es lo que los desarrolladores deben usar para examinar la memoria extendida.

## Gráficos

Todos los cálculos relacionados con los gráficos son realizados por la CPU. Una vez finalizados, la **Picture Processing Unit** ('PPU' o Unidad de procesamiento de imágenes) los renderiza. Este es otro componente encontrado dentro del DMG-CPU y podría describirse como una versión mejorada del [chip de gráficos del predecesor](nes#graphics) (con el mismo nombre).

La imagen se muestra en una pantalla integrada de tipo LCD a una resolución de **160×144 píxeles** y, en el caso del Game Boy monocromo, muestra **4 tonos de gris** (blanco, gris claro, gris oscuro y negro). Dado que la Game Boy original tiene una pantalla LCD verde, la imagen se verá *verdosa*.

Si has leído el artículo de la NES con anterioridad, puede que recuerdes que la PPU fue diseñada a partir del funcionamiento del tubo de rayos catódicos (CRT). Sin embargo (y por razones obvias), tenemos una pantalla LCD en la Game Boy. Pues bien, la nueva PPU también sigue esa metodología ya que los LCDs requieren ser refrescados también. Al hacerlo, esta consola disfrutará de efectos [basados en CRT](nes#tab-5-4-background-split) que anteriormente permitieron a los desarrolladores de NES crear contenido imaginativo.

### Organizando el contenido

![Arquitectura de memoria de la PPU.](ppu.png)

La PPU está conectada a **8 KB de VRAM** o 'Display RAM'. Al hacerlo, también proporciona acceso arbitrado a la CPU. Esos 8 KB contendrán la mayor parte de los datos que la PPU necesitará para renderizar los gráficos. El resto de materiales se almacenarán dentro de la PPU, ya que requieren tasas de acceso más rápidas.

El juego se encarga de rellenar las diferentes áreas con el tipo de dato correcto. Además, la PPU expone registros para que el juego pueda instruir a la PPU sobre cómo se organiza esos datos. No obstante, hay muchas reglas a seguir (lo verás en las siguientes secciones).

### Construyendo el frame

Veamos cómo la PPU logra dibujar cosas en pantalla. Para fines de demostración, se utilizará como ejemplo *Super Mario Land 2*.

#### Tiles {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Múltiples mosaicos.](ppu_mario/tiles.png){.active title="Todo"}

![Múltiples mosaicos separados en una cuadrícula.](ppu_mario/tiles_grid.png){title="Cuadrícula"}

![Un único mosaico.](ppu_mario/tiles_single.png){title="Individual"}

Tiles encontrados en la Tabla de Patrones (o 'Tile pattern table').

:::

La PPU utiliza **mosaicos** como ingrediente principal para renderizar gráficos, especialmente, **sprites y fondos** [@graphics-overview].

Los tiles son sencillamente **mapas de bits de 8x8 pixeles** almacenados en VRAM en una región llamada **Tile Set** o 'Tile pattern table', cada pixel corresponde con uno de los cuatro tonos de gris disponibles. En la práctica, sin embargo, los tonos de gris se seleccionan a través de una paleta de 'color'. Los Game Boys monocromos contienen registros que definen estas paletas. Como se explicó antes, solo hay cuatro colores/tonos de grises para elegir, por lo que un único registro de 8 bits puede contener una paleta de cuatro tonos sin problema. Dicho esto, el sistema proporciona tres registros (por lo tanto, **tres paletas programables**) con **uso restringido** (más sobre esto se explica más adelante).

Además, los mosaicos se agrupan en **dos tablas de patrones**.

Para construir la imagen, los mosaicos se referencian en otro tipo de tabla llamada **Mapa de mosaico**. Esta información le dirá a la PPU donde renderizar estos tiles. Dos mapas son almacenados para construir diferentes capas de un frame.

Las siguientes secciones explican como son utilizados los 'Tile maps' para construir las capas.

#### Capa de fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Mapa de fondo asignado en la VRAM.](ppu_mario/background.png){.active title="Completa"}

![Área seleccionada del mapa de fondo. Nótese que la parte seleccionada incluye una porción de la parte superior, esta será superpuesta por la _Capa de ventana_.](ppu_mario/background_selected.png){title="Selección"}

![Mapa de fondo mostrado.](ppu_mario/background_rendered.png){title="Renderizado"}

Proceso de renderización del mapa de fondo.

:::

La capa de fondo es un mapa de **256x256 píxeles** (32x32 tiles) que contiene **tiles estáticos**. Sin embargo, recuerda que sólo 160x144 píxeles son visible en pantalla, por lo que el juego decide qué parte es seleccionada para mostrarse. Los juegos pueden también mover el área visible durante el gameplay, así es como se consigue el **Efecto de desplazamiento (Scrolling effect)**.

Uno de los dos 'Tile maps' puede ser utilizado para proyectar la capa de fondo. Además, solo hay **una paleta** disponible para esta capa.

#### Ventana {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Mapa de ventana asignada.](ppu_mario/window.png){.active title="Completa"}

![Mapa de ventana mostrada. El juego la activa durante la última línea escaneada (scan-line). Por lo tanto, sólo las primeras filas se renderizan en la parte inferior de la pantalla.](ppu_mario/window_rendered.png){title="Selección"}

Proceso de renderización del mapa de ventana.

:::

La ventana es una capa de **160x144 píxeles** que contiene tiles mostrados encima del fondo y sprites. Esta capa **no se desplaza**.

El mapa de mosaicos restante se puede asignar a la capa de ventana, también comparte la misma paleta con la capa de fondo.

En definitiva, esto puede sonar como una característica tonta. Dado que la Ventana no tiene transparencia y por lo tanto oscurece completamente el Fondo, te puedes preguntar '¿Para qué sirve?'. Pues bien, tanto el fondo como la ventana se pueden utilizar **simultáneamente** en diferentes partes de la pantalla. Esto está destinado a mostrar información principalmente en la parte inferior de la pantalla, pero mientras que en la NES esto requería realizar [escrituras complejas y sincronizadas](nes#tab-5-4-background-split), la PPU del Game Boy puede manejarlo automáticamente.

Por lo tanto, los juegos lo utilizan normalmente para mostrar estadísticas del jugador, puntuaciones y otra información "siempre activa".

#### Sprites {.tab}

![Capa de sprite renderizado.](ppu_mario/sprite.png) {.tab-float.pixel}

Los sprites son tiles que pueden moverse de forma independiente alrededor de la pantalla. También pueden superponerse entre sí y aparecer detrás del fondo, el gráfico visible se decidirá basándose en el atributo de prioridad.

Esta capa también tiene un color extra disponible: **Transparente**. Inicialmente, esto significa que solo pueden mostrar tres grises diferentes en lugar de cuatro. Afortunadamente, esta capa en particular está provista de **dos paletas dedicadas** para elegir.

La **Memoria de Atributo de Objeto** o 'OAM' es un mapa almacenado dentro de la PPU que especifica los mosaicos que serán utilizados como sprites. En lugar de usar un mapa de mosaico, los **sprites se definen en la OAM**. Los juegos típicamente llenan esta región llamando a la **unidad OAM DMA** localizada dentro del chip, la DMA obtiene datos de la RAM principal o de la ROM del juego y los envía a la OAM. Ahora, mientras la DMA está trabajando, la CPU no puede acceder a la memoria externa (de ahí la importancia de usar HRAM durante ese período).

Aparte del índice de mosaicos, cada entrada contiene los siguientes atributos: posición X-Y, paleta elegida, prioridad y 'flip flags' (permitiendo girar el mosaico vertical y horizontalmente).

La PPU está limitada a renderizar hasta **diez sprites por escaneo de línea** y **hasta 40 por frame**, sobrecargar esto hará que no se muestren algunos sprites.

#### Resultado {.tab}

![Resultado final _¡Tachán!_](ppu_mario/result.png) {.tab-float.pixel}

Una vez finalizado el frame... ¡Es hora de pasar al siguiente! Sin embargo, la CPU no puede modificar las tablas mientras la PPU está leyendo la VRAM, por lo que el sistema proporciona un conjunto de interrupciones que se activan cuando la PPU está inactiva. Tal vez recuerdes este comportamiento por la NES.

Cuando se completa una sola línea de exploración, comienza el período de **Horizontal Blank**. Esto permite manipular la parte del frame que aún no se ha dibujado.

Cuando se completan todas las líneas de exploración, comienza el período de **Vertical Blank** y se llama a una interrupción dedicada. En ese momento, el juego puede actualizar los gráficos para el siguiente frame.

Hay un estado extra llamado **'OAM search'** que se activa al inicio del escaneo de línea, en ese momento la PPU está procesando qué sprites se mostrarán en esa línea de escaneo, así que el juego puede actualizar cualquier región excepto la OAM.

### Secretos y limitaciones {.tabs-close}

La inclusión de la Capa de ventana y las interrupciones adicionales permitieron nuevos tipos de contenido y efectos.

#### Efecto de tambaleo ('Wobble') {.tabs.active}

![The Legend of Zelda: Link's Awakening (1993). _¡Spoilers!_](zelda){.tab-float video="true"}

Las interrupciones horizontales permitieron alterar el frame antes de completarse. Lo que significa que se podría aplicar un valor de desplazamiento diferente a cada línea, de tal manera que cada fila del frame se desplazara a diferentes intervalos.

Esto resultó en un *Efecto de tambaleo (Wobble)* (aunque no estoy seguro de que ese sea su nombre oficial).

### Las adiciones de color {.tabs-close}

El PPU de la Game Boy Color se comporta como un superconjunto del original. Verás ahora cuáles son las adiciones del modelo denominado 'Color' de esta marca.

#### Modos de operación

![The Legend of Zelda: Link's Awakening DX (1998).<br>Un juego híbrido de Game Boy Color ejecutándose en modo CGB.](ppu_color/zelda.png) {.toleft.pixel}

![Super Mario Land 2, visto desde una Game Boy Color. Este último añade una paleta colorizada y se ejecuta en modo DMG.](ppu_color/dmg_mario.png) {.toright.pixel}

Para empezar, por razones de compatibilidad, el nuevo PPU tiene **dos modos de operación**. Sin embargo, Nintendo quería que los usuarios de Color vieran mejoras incluso con los juegos únicamente monocromáticos. Por consiguiente, los dos modos de operación son los siguientes:

- **Modo CGB**: Modo PPU extendido con todas las mejoras visuales que comprenden los nuevos juegos de Game Boy Color.
- **Modo DMG**: Modo tradicional con todos los extras deshabilitados. Sin embargo, verás en la sección 'Sistema operativo' que los juegos monocromáticos aún así se mejoran con paletas de colores.

#### Organizando el (nuevo) contenido

La placa madre del CGB ahora alberga **16 KB de VRAM** en su lugar, lo cual es el doble de la cantidad original de VRAM. Debido a las limitaciones de direccionamiento de la CPU, este nuevo arreglo se implementa en forma de **dos bancos de 8 KB**, con un nuevo registro (llamado `VBK`) actuando como un conmutador. Por otro lado, el PPU puede acceder a los dos bancos al mismo tiempo. Al final del día, esto significa que los programadores solo necesitan llenar los bancos VRAM con la ayuda de `VBK`, y luego especificar en el mapa de mosaicos el banco donde se encuentra el mosaico, para que el PPU pueda encargarse del resto.

Dicho esto, ¿qué puedes hacer con la VRAM extra? Muchas cosas:

- Almacenar el doble de mosaicos.
- Almacenar más paletas, que también ofrecen una gama de colores más amplia.
- Extender el espacio de metadatos de mosaicos para codificar más efectos y direccionar paletas adicionales.

##### Los visuales

Gracias al nuevo PPU, los programadores ahora pueden definir paletas de colores con **32,768 colores** a elegir.

En primer lugar, los desarrolladores ahora deben poblar una nueva área llamada **Memoria de Paleta**, que almacena hasta **dieciséis paletas de colores** (la mitad para el Fondo y la Ventana, la otra mitad para los sprites) que codifican **cuatro colores** [@cpu-nintendo]. Cada entrada cabe en un valor de 16 bits (2 bytes) y solo se usan 15 bits. La Memoria de Paleta no es direccionada por la CPU, sin embargo, se utiliza un nuevo registro como un búfer para escribir sobre esta memoria (una metodología encontrada en la [Super Nintendo](super-nintendo#organising-the-content)). En general, así es como la CPU define paletas.

Dicho esto, los mosaicos de fondo y ventana pueden referenciar cualquiera de esas ocho paletas. Lo mismo ocurre con los mosaicos de Sprites, excepto que están restringidos a paletas de tres colores, ya que una entrada está reservada para el color 'transparente'.

##### El espacio extra

Continuando, **los conjuntos de mosaicos ahora son el doble de grandes**. Por lo tanto, los programadores pueden almacenar el doble de mosaicos en VRAM. **Los mapas de mosaicos de fondo/ventana se han extendido** también, resultando en metadatos extra siendo codificados. Por consiguiente, expandiendo las capacidades de estas capas. Por ejemplo, sus mosaicos ahora pueden ser **invertidos horizontal y verticalmente**, ahorrando al juego de almacenar gráficos duplicados en VRAM (lo cual, a su vez, puede ser explotado para dibujar más contenido único).

Además, la CPU CGB también incluye **una unidad de DMA adicional**, que puede copiar el contenido del Game Pak o WRAM a VRAM. Opera en dos modos [@cpu-nintendo]:

- **DMA de propósito general**: La transferencia ocurrirá en cualquier momento y el DMA tendrá prioridad sobre cualquier otro acceso a la memoria. Así que, los programadores deben tener cuidado sobre cuándo usar este componente (es decir, durante o fuera del escaneo) y cómo (la cantidad de datos a copiar), ya que el uso incorrecto puede llevar al desgarro de la pantalla (el acceso a VRAM será bloqueado durante la transferencia).
- **DMA de espacio en blanco horizontal (H-Blank)**: La transferencia solo ocurrirá durante los períodos de H-Blank. Esto evita artefactos en la pantalla, pero solo puede transferir contenido en lotes de 16 bytes, y se pausa durante el escaneo LCD.

Una vez más, esta unidad ofrece a los programadores nuevas posibilidades para proporcionar contenido más enriquecido, aprovechando períodos originalmente destinados a la inactividad.

## Audio

El sistema de audio lo lleva a cabo la **Unidad de Procesamiento de Audio** (APU), un [chip PSG](master-system#audio) con cuatro canales [@audio-overview].

Curiosamente, esta es una de las pocas secciones que no ha evolucionado entre modelos. De hecho, ni siquiera puede acelerarse, ya que si cambias la velocidad de los osciladores, no escucharás sonidos 'mejores', sino un tono más alto.

### Funcionalidad

Cada uno de los cuatro canales está reservado para un solo tipo de forma de onda:

#### Pulso {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de pulso 1.](pulse_single_1){.active video="true" title="Pulso 1"}

![Osciloscopio del canal de pulso 2.](pulse_single_2){video="true" title="Pulso 2"}

![Osciloscopio de todos los canales de audio.](pulse_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

Las ondas de pulso tienen un sonido *beep* distintivo y son utilizadas para **melodía o efectos de sonido**.

La APU reserva dos canales para cada onda de pulso. Estos producen uno de cuatro tonos diferentes, resultante de variar la anchura del pulso. El primer canal tiene disponible un **control de 'Sweep'** exclusivo.

Debido al número limitado de canales, la melodía a menudo será interrumpida, especialmente cuando ciertos efectos se reproduzcan durante el gameplay. Esto es muy notable en juegos como Pokémon Rojo/Azul cuando durante un combate, el grito del Pokémon se superpone por encima de todos los canales de música; esta es también la razón por la cual ninguna música de batalla Pokémon usa percusión.

#### Onda {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de onda.](wave_single){.active video="true" title="Onda"}

![Osciloscopio de todos los canales.](wave_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

La APU permite definir una **forma de onda personalizada** para ser escuchada desde su tercer canal. La onda está compuesta de 32 muestras de 4 bits que se almacenan en una tabla de ondas.

Este canal también permite controlar su frecuencia (permitiendo producir diferentes notas musicales de la misma muestra) y volumen.

#### Ruido {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de ruido.](noise_single){.active video="true" title="Ruido"}

![Osciloscopio de todos los canales de audio.](noise_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

El ruido es básicamente un conjunto de formas de onda aleatorias que suenan como estática blanca. Se reserva un canal para ello.

Los juegos lo utilizan para percusión o efectos de *ambiente*.

Este canal solo tiene disponibles 2 tonos para usar, uno produce *estática limpia* y el otro produce *estática robótica*. Su frecuencia también se puede controlar.

### Secretos y limitaciones {.tabs-close}

El mezclador (mixer) produce sonido estéreo, por lo que los canales pueden asignarse al lado izquierdo o derecho ¡pero esto sólo se puede escuchar con auriculares! El altavoz es monofónico.

Además, el chip del mezclador también está conectado a un pin dedicado en el cartucho, permitiendo transmitir un **canal adicional** con la condición de que el cartucho tenga que producir realmente el sonido analógico (esto solo es posible con hardware extra). Sin embargo, ningún juego en el mercado terminó usando esta característica, y tampoco encontrarás este pin en el [Game Boy Advance](game-boy-advance) en su [subsistema compatible con versiones anteriores](game-boy-advance#becoming-a-game-boy-color).

## Sistema operativo

A diferencia de la NES, que se iniciaba directamente en el juego, la Game Boy fue diseñada para arrancar siempre desde una **ROM interna de 256 Bytes**, y solo luego saltar al código del juego. El proceso de arranque es el siguiente [@operating_system-boot]:

1. Después de encender la consola, la CPU comienza leyendo en la dirección **0x0000** (ubicada en la ROM de la Game Boy).
2. Se inicializan la RAM y la APU.
3. El logotipo de Nintendo se copia del cartucho ROM a la Display RAM, después se dibuja en el borde superior de la pantalla. Si no hay cartucho insertado, el logo contendrá tiles basura. Lo mismo puede ocurrir si está mal insertado.
4. El logotipo se desplaza hacia abajo y se reproduce el famoso sonido de *po-ling*.
5. El logotipo del juego se compara con el que está almacenado en la ROM de la consola. Una **suma de verificación (checksum)** rápida se hace en la cabecera de la ROM del cartucho para asegurarse de que éste se ha insertado correctamente. Si alguna de estas verificaciones falla, la consola se congela.
6. La ROM de la consola desaparece del mapa de memoria.
7. La CPU comienza a ejecutar el juego.

Curiosamente, el logo de *Nintendo* que se está mostrado en pantalla no se borra de la VRAM, de tal manera que los juegos pueden aplicar alguna animación o efecto para añadir su propio logo.

![La pantalla de inicio de la Game Boy junto con 20y, una demo casera que juega con el logo.](20y){video="true"}

### La secuencia revisada

![La pantalla de inicio de la Game Boy Color, el logo ya no se desliza pero tiene un efecto arcoíris.](boot_cgb.png) {.pixel}

Una vez más, en el caso de la Game Boy Color, encontramos cambios drásticos en el contenido de la ROM. Por ejemplo, el tamaño de la ROM ahora es de **2 KB** [@operating_system-boot_cgb] y las rutinas muestran un nuevo comportamiento:

- La secuencia de arranque ahora verificará si tiene insertado un juego solo de Game Boy o un juego de Game Boy Color, y luego establecerá los registros correspondientes para activar el modo DMG o CGB. El código de arranque revisa metadatos específicos del juego (dentro de la ROM del cartucho) que están estructurados de manera diferente para los juegos CGB.
- En el caso de que se inserte un juego DMG, el programa de arranque también llenará la RAM de paleta con paletas calculadas, estas se basan en un algoritmo simple pero inteligente que también se apoya en los metadatos del juego. Esto es lo que hace que los juegos monocromáticos se vean coloreados cuando se ejecutan en la consola de próxima generación.
  - En esta etapa, el usuario puede presionar una combinación de botones que alterará la paleta de elección del código de arranque.
- La comprobación del logotipo de Nintendo ahora también utiliza HRAM (sin embargo, en vano).

## Juegos

Los juegos están escritos en ensamblador y tienen un tamaño máximo de **32 KB**, esto se debe al limitado espacio de direcciones disponible. Sin embargo, con el uso de un **Banco controlador de memoria** ([mapeador](nes#going-beyond-existing-capabilities)), los juegos pueden alcanzar un mayor tamaño. El cartucho (Game Pak) más grande encontrado en el mercado tiene una ROM de 1 MB (o 8 MB, en el caso del Game Boy Color).

Los Game Pak pueden incluir un reloj en tiempo real y una batería externa junto con SRAM para guardar partidas, aunque todo esto es opcional.

### Tipos de cartuchos

Debido a los dos modos de operación que soportaba la Game Boy Color, Nintendo enumeró **tres tipos diferentes** de juegos de Game Boy:

- **Game Boy**: Un tipo totalmente compatible con todos los modelos de Game Boy. Siempre se ejecuta en modo DMG.
- **Mejorado para Game Boy Color**: Un híbrido que se ejecuta de manera diferente dependiendo de la consola anfitriona. Aunque es totalmente compatible con el modelo monocromático, se mejorará visualmente (debido a que se ejecuta en modo CGB) cuando se ejecute en la Game Boy Color. Sin embargo, la funcionalidad general aún estará restringida para mantener la compatibilidad con la Game Boy.
- **Exclusivo de Game Boy Color**: Solo compatible con la Game Boy Color, pero aprovecha al máximo su hardware.

### Comunicación externa

![Cable de enlace Game Boy para múltiples variantes de la consola [@photography-amos]. Desde el lanzamiento de la consola original, las revisiones subsiguientes miniaturizaron el conector manteniendo el mismo número de pines.](link_cable.png) {.no-borders}

Por primera vez, los juegos pueden comunicarse con otras Game Boys mediante el uso de un cable **Game Boy Link**, el cual proporciona funcionalidad multijugador, por ejemplo. La interfaz utiliza un protocolo bastante primitivo de **conexión en serie**.

La Game Boy original transfiere información a 8 Kbit/segundo (1 KB/seg), mientras que la variante Color puede alcanzar hasta 512 Kbit/segundo (64 KB/seg), esta última velocidad se conocía como modo 'alta velocidad'.

La última variante también viene con un **emisor y receptor de infrarrojos** (compuesto por un LED y un fototransistor, respectivamente) [@cpu-nintendo]. Esto hizo posible intercambiar datos de forma inalámbrica entre Game Boy Colors, como se ve en juegos como Pokémon Oro y similares. Sin embargo, encontrarás que el sistema no implementa ningún protocolo de comunicación, solo un único registro (`RP`) que codifica la acción del sensor IR (emitir o recibir), el bit único (`0` o `1`) transferido y el último bit recibido. No obstante, para aliviar las cosas a los desarrolladores, Nintendo incluyó una implementación de referencia en el manual oficial del desarrollador de Game Boy [@cpu-nintendo].

## Antipiratería

Como habéis visto en la sección 'Sistema Operativo', la consola nunca ejecutará un juego de inmediato, primero realiza una serie de verificaciones que **impiden la ejecución de cartuchos no autorizados** y también se asegura de que el cartucho **ha sido insertado correctamente**.

Para pasar estas comprobaciones, los juegos tuvieron que incluir una copia del logotipo de Nintendo (en forma de mosaicos) en la cabecera de su ROM [@games-dhole], de esta manera Nintendo podía hacer uso de las leyes de **Copyright y marca registrada** para controlar la distribución. *¿Inteligente, verdad?*

Por el contrario, _Sega v. Accolade_ posteriormente otorgó a cualquier empresa el derecho de usar logotipos con copyright para este tipo de requerimiento, ya que se incluye en el uso justo.

Dicho esto, se pueden implementar más medidas antipiratería dentro de los juegos, por ejemplo, verificando el tamaño de la SRAM (normalmente, es mayor en copias piratas) y realizando sumas de verificación (checksum) de la ROM en puntos aleatorios del juego.

## Eso es todo amigos
