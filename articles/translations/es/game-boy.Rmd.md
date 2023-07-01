---
short_title: Arquitectura de la Game Boy
long_title: Arquitectura de la Game Boy
name: Game Boy
subtitle: Simple y portátil
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "La Game Boy original.<br>Lanzada el 21/04/1989 en Japón, el 31/07/1989 en Estados Unidos y el 28/09/1990 en Europa"
    img_class: reduced-width
  Motherboard:
    caption: "Mostrando la revisión '04'. Ten en cuenta que 'DMG' es el identificador del modelo original de Game Boy."
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/game-boy/
---

## Una introducción rápida

La Game Boy puede ser entendida como una versión portátil de la NES con potencia limitada, pero pronto verás como incluye nueva funcionalidad muy interesante.

```{r results="asis"}
supporting_imagery()
```

## CPU

En lugar de colocar chips estándares en la placa base, Nintendo optó por un único chip para alojar (y ocultar) la mayor parte de los componentes, incluyendo la CPU. Este tipo de chip se le conoce como 'System On Chip' (SoC o "Sistema en Chip") y el que encontramos en la Game Boy se le identifica como **DMG-CPU** o **Sharp LR35902** [@cpu-realboy].

Dicho esto, el procesador principal es un **Sharp SM83** [@cpu-gekkio] y es una mezcla entre el Z80 y el Intel 8080. Tiene una velocidad de **~4.19 MHz**.

El Z80 es en sí mismo un superconjunto del 8080, dicho esto ¿qué tiene el SM83 y qué no tiene de estos dos? [@cpu-steil]

- No se incluyen los registros `IX` e `IY` del Z80 ni las instrucciones `IN` u `OUT` del 8080: Por lo que los [puertos E/S (en inglés 'I/O ports') ](master-system#accessing-the-rest-of-the-components) no están disponibles. No sé a ciencia a cierta si es una medida tomada para reducir costes, pero lo que sí sé, es que los componentes tendrán que ser **completamente mapeados en memoria** [@cpu-fayzullin].
- Solo está implementado el conjunto de registros del 8080.
- Incluye una extensión del conjunto de instrucciones del Z80. Aunque sólo se encuentran instrucciones de manipulación de bits.

Para finalizar, también se añadieron **nuevas instrucciones** que no están presentes ni en el Z80 ni en el 8080. Creo que explicarlas de una a una va más allá del alcance de este artículo, pero la idea principal es que optimizan ciertas operaciones condicionadas por la forma en que Nintendo/Sharp organizaron el hardware.

### Memoria Disponible

Nintendo instaló **8 KB de RAM** para uso general (que llamaron **Work RAM** o 'WRAM') [@cpu-nintendo]. Fíjese que es cuatro veces más que la cantidad incluida en la [NES](nes).

### Acceso al hardware

El SM83 tiene un bus de datos de 8 bits y un bus de dirección de 16 bits, por lo que se puede direccionar hasta 64 KB de memoria. El mapa de memoria está compuesto por [@cpu-mongenel]:

- El área del cartucho.
- WRAM y Display RAM.
- E/S (el joypad, audio, gráficos y el LCD)
- Controles de interrupción.

## Gráficos

Todos los cálculos relacionados con los gráficos son realizados por la CPU. Una vez finalizados, la **Picture Processing Unit** ('PPU' o Unidad de procesamiento de imágenes) los renderiza. Este es otro componente que se encuentra dentro de la DMG-CPU, y de hecho se basa en la [PPU de su predecesor](nes#graphics).

La imagen se muestra en una pantalla integrada de tipo LCD a una resolución de **160×144 píxeles** que produce **4 tonos de gris** (blanco, gris claro, gris oscuro y negro). Pero dado que la Game Boy original tiene una pantalla LCD verde, los gráficos se ven con un tono *verdoso*.

Si has leído el artículo de la NES con anterioridad, puede que recuerdes que la PPU fue diseñada a partir del funcionamiento del tubo de rayos catódicos (CRT). Sin embargo (y por razones obvias), tenemos una pantalla LCD en la Game Boy. Pues bien, la nueva PPU no varía esa parte, ya que los LCDs también necesitan ser refrescados. De hecho, algunos de los efectos especiales logrados gracias a este comportamiento también son aplicados en la Game Boy.

### Organizando el contenido

![Arquitectura de memoria de la PPU.](ppu.png)

La PPU tiene **8 KB de VRAM** o 'Display RAM', a la que tanto la PPU y la CPU pueden acceder directamente pero no al mismo tiempo. Esos 8 KB contendrán la mayor parte de los datos que la PPU necesitará para renderizar los gráficos. El resto de bits se almacenarán dentro de la PPU, ya que requerirán de un ritmo de acceso más rápido.

El juego se encarga de rellenar las diferentes áreas con el tipo de dato correcto. Además, la PPU expone registros que el juego puede utilizar para instruir a esta acerca de cómo los datos están organizados (aunque hay bastantes reglas).

### Construyendo el frame

Ahora vamos a ver cómo hace la PPU para dibujar cosas en pantalla. Para demostrar esto, *Super Mario Land 2* será utilizado como ejemplo:

#### Tiles {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Múltiples mosaicos.](ppu_mario/tiles.png){.active title="Todo"}

![Múltiples mosaicos separados en una cuadrícula.](ppu_mario/tiles_grid.png){title="Cuadrícula"}

![Un único mosaico.](ppu_mario/tiles_single.png){title="Individual"}

Tiles encontrados en la Tabla de Patrones (o 'Tile pattern table').

:::

La PPU utiliza **tiles (Mosaicos)** como el ingrediente principal para renderizar gráficos, especialmente, **sprites y fondos** [@graphics-emu].

Los tiles son sencillamente **mapas de bits de 8x8 pixeles** almacenados en VRAM en una región llamada **Tile Set** o 'Tile pattern table', cada pixel corresponde con uno de los cuatro tonos de gris disponibles. Finalmente, los tiles son agrupados en dos tablas de patrones.

Con el fin de construir la imagen, los tiles son referenciados en otro tipo de tabla llamada **Tile map**. Esta información le dirá a la PPU donde renderizar estos tiles. Dos mapas son almacenados para construir diferentes capas de un frame.

Las siguientes secciones explican como son utilizados los 'Tile maps' para construir las capas.

#### Capa de fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Mapa de fondo asignado en la VRAM.](ppu_mario/background.png){.active title="Completa"}

![Área seleccionada del mapa de fondo. Nótese que la parte seleccionada incluye una porción de la parte superior, esta será superpuesta por la _Capa de ventana_.](ppu_mario/background_selected.png){title="Selección"}

![Mapa de fondo mostrado.](ppu_mario/background_rendered.png){title="Renderizado"}

Proceso de renderización del mapa de fondo.

:::

La capa de fondo es un mapa de **256x256 píxeles** (32x32 tiles) que contiene **tiles estáticos**. Sin embargo, recuerda que sólo 160x144 píxeles son visible en pantalla, por lo que el juego decide qué parte es seleccionada para mostrarse. Los juegos pueden también mover el área visible durante el gameplay, así es como se consigue el **Efecto de desplazamiento (Scrolling effect)**.

Uno de los dos 'Tile maps' puede ser utilizado para proyectar la capa de fondo.

#### Ventana {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Mapa de ventana asignada.](ppu_mario/window.png){.active title="Completa"}

![Mapa de ventana mostrada. El juego la activa durante la última línea escaneada (scan-line). Por lo tanto, sólo las primeras filas se renderizan en la parte inferior de la pantalla.](ppu_mario/window_rendered.png){title="Selección"}

Proceso de renderización del mapa de ventana.

:::

La ventana es una capa de **160x144 píxeles** que contiene tiles mostrados encima del fondo y sprites. No se puede desplazar.

El 'Tile map' restante puede ser asignado a la capa de ventana.

En principio, esto puede sonar como una función inútil. Después de todo, la capa de ventana superpone todo lo demás, así que ¿cuál es su utilidad? Bien, tanto el fondo como la ventana se pueden utilizar simultáneamente en diferentes partes de la pantalla. Esto se consigue cambiando el registro `LCDCONT` durante específicas líneas de exploración (del inglés 'scan-lines').

Por lo tanto, los juegos lo utilizan normalmente para mostrar estadísticas del jugador, puntuaciones y otra información "siempre activa".

#### Sprites {.tab}

![Capa de sprite renderizado.](ppu_mario/sprite.png) {.tab-float.pixel}

Los sprites son tiles que pueden moverse de forma independiente alrededor de la pantalla. También pueden superponerse entre sí y aparecer detrás del fondo, el gráfico visible se decidirá basándose en el atributo de prioridad.

También tienen un color extra disponible: **Transparente**. Así que solo pueden mostrar tres grises diferentes en lugar de cuatro. Afortunadamente, esta capa permite definir dos paletas de colores para hacer uso de todos los colores.

La **Memoria de Atributo de Objeto** u 'OAM' es un mapa almacenado dentro de la PPU, la cual especificará qué tiles se utilizarán como sprites. Los juegos rellenan esta región llamando a la **unidad DMA** localizada dentro del chip, la DMA transfiere datos de la RAM principal o de la ROM del juego a la OAM.

Aparte del índice de tiles, cada entrada contiene los siguientes atributos: posición X-Y, paleta de colores, prioridad y 'flip flags' (permitiendo girar el tile vertical y horizontalmente).

La PPU está limitada a renderizar hasta **diez sprites por escaneo de línea** y **hasta 40 por frame**, sobrecargar esto hará que no se muestren algunos sprites.

#### Resultado {.tab}

![Resultado final _¡Tachán!_](ppu_mario/result.png) {.tab-float.pixel}

Una vez finalizado el frame... ¡Es hora de pasar al siguiente! Sin embargo, la CPU no puede modificar las tablas mientras la PPU está leyendo la VRAM, por lo que el sistema proporciona un conjunto de interrupciones que se activan cuando la PPU está inactiva. Tal vez recuerdes este comportamiento por la NES.

Cuando se completa una línea de escaneo, la interrupción del **'Horizontal Blank'** se activa. Esto permite manipular la parte del frame que aún no se ha dibujado.

Cuando todas las líneas de escaneo han finalizado, la interrupción del **'Vertical Blank'** se activa. En ese momento, el juego puede actualizar los gráficos para el siguiente frame.

Hay un estado extra llamado **'OAM search'** que se activa al inicio del escaneo de línea, en ese momento la PPU está procesando qué sprites se mostrarán en esa línea de escaneo, así que el juego puede actualizar cualquier región excepto la OAM.

### Secretos y limitaciones {.tabs-close}

La inclusión de la Capa de ventana y las interrupciones adicionales permitieron nuevos tipos de contenido y efectos.

#### Efecto de tambaleo ('Wobble') {.tabs.active}

![The Legend of Zelda: Link's Awakening (1993). _¡Spoilers!_](zelda){.tab-float video="true"}

Las interrupciones horizontales permitieron alterar el frame antes de completarse. Lo que significa que se podría aplicar un valor de desplazamiento diferente a cada línea, de tal manera que cada fila del frame se desplazara a diferentes intervalos.

Esto resulto en un *Efecto de tambaleo (Wobble)* (aunque no estoy seguro de que ese sea su nombre oficial).

## Audio {.tabs-close}

El sistema de audio lo lleva a cabo la **Unidad de Procesamiento de Audio** (APU), un [chip PSG](master-system#audio) con cuatro canales [@audio-wiki].

### Funcionalidad

Cada canal está reservado para un tipo de forma de onda:

#### Pulso {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de pulso 1.](pulse_single_1){.active video="true" title="Pulso 1"}

![Osciloscopio del canal de pulso 2.](pulse_single_2){video="true" title="Pulso 2"}

![Osciloscopio de todos los canales de audio.](pulse_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

Las ondas de pulso tienen un sonido *beep* distintivo y son utilizadas para **melodía o efectos de sonido**.

La APU reserva dos canales para cada onda de pulso. Estos producen uno de cuatro tonos diferentes, resultante de variar la anchura del pulso. El primer canal tiene disponible un **control de 'Sweep'** exclusivo.

Debido al número limitado de canales, la melodía a menudo será interrumpida, especialmente cuando ciertos efectos se reproduzcan durante el gameplay. Esto es muy visible en juegos como Pokémon Rojo/Azul cuando durante un combate, el grito del Pokémon se superpone por encima de todos los canales de música.

#### Ruido {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de ruido.](noise_single){.active video="true" title="Ruido"}

![Osciloscopio de todos los canales de audio.](noise_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

El ruido (_noise_) se puede definir como un conjunto de formas de onda aleatorias que suenan como sonido blanco. Se reserva un canal para ello.

Los juegos lo utilizan para percusión o efectos de *ambiente*.

Este canal solo tiene disponibles 2 tonos para usar, uno produce *estática limpia* y el otro produce *estática robótica*. Su frecuencia también se puede controlar.

#### Onda {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de onda.](wave_single){.active video="true" title="Onda"}

![Osciloscopio de todos los canales.](wave_full){video="true" title="Completado"}

Pokémon Rojo/Azul (1996).

:::

La APU permite definir una **forma de onda personalizada** para ser escuchada desde su cuarto canal. La onda está compuesta de 32 muestras de 4 bits que se almacenan en una tabla de ondas.

Este canal también permite controlar su frecuencia (permitiendo producir diferentes notas musicales de la misma muestra) y volumen.

### Secretos y limitaciones {.tabs-close}

El mezclador (mixer) produce sonido estéreo, por lo que los canales pueden asignarse al lado izquierdo o derecho ¡pero esto sólo se puede escuchar con auriculares! El altavoz es monofónico.

Además, el chip del mezclador también está conectado a un pin extra en el cartucho, permitiendo transmitir un **canal adicional** con la condición de que el cartucho produzca el sonido analógico (esto solo es posible con hardware extra). Ningún juego del mercado terminó usando esta función.

## Juegos

Los juegos están escritos en ensamblador y tienen un tamaño máximo de **32 KB**, esto se debe al limitado espacio de direcciones disponible. Sin embargo, con el uso de un **Banco controlador de memoria** (mapeador), los juegos pueden alcanzar un mayor tamaño. El cartucho más grande que se puede encontrar en el mercado tiene 1 MB de ROM.

Los cartuchos pueden incluir un chip de reloj y una batería externa combinada con SRAM para guardar partidas.

### Comunicación externa

Por primera vez, los juegos pueden comunicarse con otras Game Boys mediante el uso de un cable **Game Boy Link**, el cual proporciona funcionalidad multijugador. La interfaz utiliza un protocolo bastante primitivo de **conexión en serie**.

## Antipiratería

Esta consola contiene una **ROM de 256 Bytes** dentro del SoC que se utiliza para iniciar la ROM del cartucho. Sin embargo, el juego no se ejecuta inmediatamente, primero se ejecuta una serie de verificaciones que **impiden la ejecución de cartuchos no autorizados** y también se comprueba que el cartucho **ha sido insertado correctamente**.

Para pasar estas comprobaciones, los juegos tuvieron que incluir una copia del logotipo de Nintendo (en forma de tiles) en la cabecera de su ROM [@games-dhole], de esta manera Nintendo podía hacer uso de las leyes de **Copyright y marca registrada** para controlar la distribución de juegos, *¿bastante inteligente, no?*. La ROM de la Game Boy también incluye una copia del logotipo para poder compararlas.

Dicho esto, el proceso de arranque es el siguiente [@anti_piracy-boot]:

1. Después de encender la consola, la CPU comienza leyendo en la dirección **0x00** (ubicada en la ROM de la Game Boy).
2. Se inicializan la RAM y el sonido.
3. El logotipo de Nintendo se copia del cartucho ROM a la VRAM, después se dibuja en el borde superior de la pantalla. Si no hay cartucho insertado, el logo contendrá tiles basura. Lo mismo puede ocurrir si está mal insertado.
4. El logotipo se desplaza hacia abajo y se reproduce el famoso sonido de *po-ling*.
5. El logotipo del juego se compara con el que está almacenado en la ROM de la consola, si la comprobación falla, la consola se congela.
6. Una **suma de verificación (checksum)** rápida se hace en la cabecera de la ROM del cartucho para asegurarse de que éste se ha insertado correctamente, si la comprobación falla, se congela la consola.
7. La ROM de la consola desaparece del mapa de memoria.
8. La CPU comienza a ejecutar el juego.

Curiosamente, el logo de *Nintendo* que se está mostrado en pantalla no se borra de la VRAM, de tal manera que los juegos pueden aplicar alguna animación o efecto para añadir su propio logo.

![20y, una demo casera que trastea con el logotipo.](20y){video="true"}

Otras medidas se pueden implementar dentro de los juegos. Por ejemplo, verificando el tamaño de la SRAM (normalmente, es mayor en copias piratas) y realizando sumas de verificación (checksum) de la ROM en puntos aleatorios del juego.

## Esto es todo amigos

