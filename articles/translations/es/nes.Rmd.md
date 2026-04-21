---
long_title: "Arquitectura de la Nintendo Entertainment System (NES)"
short_title: "Arquitectura de la Nintendo Entertainment System (NES)"
name: NES
long_name: Nintendo Entertainment System (NES)
subtitle: Más allá del 6502
date: 2019-01-25
release_date: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - 
      title: "Internacional"
      file: international
      caption: "La NES.<br>Lanzada el 18/10/1985 en América del Norte y el 01/09/1986 en Europa."
      active: true
      latex_height: 87
    - 
      title: "Japonés"
      file: japanese
      caption: "La Famicom.<br>Lanzada el 15/07/1983 en Japón."
      latex_height: 95
  Motherboard:
    caption: "Mostrando la variante 'NES'."
    paperback_latex_height: 95
    marked_paperback_latex_height: 95
  Diagram:
    paperback_latex_height: 95
#Historical
aliases:
  - /projects/consoles/nes
---

## Una breve introducción

A primera vista, la NES puede ser considerada un ordenador cualquiera con el 6502, una sofisticada caja y un mando.

Y mientras que esto es *técnicamente* cierto, déjame mostrarte por qué el CPU no es la parte *central* de este sistema.

## Modelos y variantes

![Una grabadora Betamax típica. Este y otros aparatos similares influyeron en el diseño internacional de la NES. Encontré este en particular en el Centre for Computing History (Cambridge, Reino Unido) cuando lo visité en agosto de 2024.](betacord.webp)

Nintendo terminó distribuyendo múltiples variantes de la misma consola en todo el mundo [@general-variants] y aunque todas comparten la misma arquitectura, muchas se ven dramáticamente diferentes e incluso pueden incluir accesorios. Así que, para simplificar este artículo, me centraré en las dos revisiones más populares:

- La **Family Computer** (conocida como _Famicom_) fue la primera encarnación, pero sólo lanzada en Japón. Este diseño con aspecto de juguete cuenta con dos controles fijos a la consola (del cual el segundo control incluye un micrófono interno), un enchufe frontal para la pistola de luz (conocida como _Zapper_), una salida de video RF (con señal NTSC-J) y pines extra en la ranura del cartucho para expandir las capacidades de audio.
- La **Nintendo Entertainment System** (conocida como _NES_) fue la edición rediseñada para audiencias occidentales viviendo en Norte América, Europa y Oceanía; con un diseño y mecanismo que coincide con el común reproductor de VHS/Betamax. Del lado técnico, los controles ahora son desmontables (y sin micrófono) y la salida de video se mejoró con conectores RCA NTSC/PAL de video compuesto, aunque la expansión de audio se reemplazó con un subsistema antipiratería. Para rematar, la parte inferior de la carcasa esconde un 'puerto de expansión' que quedó inutilizado, junto con pines adicionales del cartucho que se comunican con ese puerto [@general-cartridge].

Debido a que el autor creció con el nombre 'NES', utilizaré ese término para referirme a la consola en general, pero cambiaré al nombre 'Famicom' para referirme a capacidades únicas que solo se encuentran en la variante japonesa.

## {.supporting-imagery}

## CPU

La CPU de la NES es un **Ricoh 2A03** [@cpu-cpu], basado en el popular **MOS Technology 6502** de 8 bits que trabaja a **1.79 MHz** (o 1.66 MHz en sistemas PAL). Este es el componente central que ejecuta el código del cartucho del juego.

### Un poco de contexto

Para entender los circuitos que alberga la placa base de la NES, conviene echar antes un vistazo al estado de la industria en aquella época. El mercado de procesadores a finales de los 70 y principios de los 80 fue bastante diverso.

::: {.subfigures .side-by-side}

![Un Commodore PET, con una CPU 6502.](pet.webp) {.toleft}

![Un Tandy TRS-80, con una CPU Z80.](tandy.webp) {.toright}

El panorama de los ordenadores de los años 70, también proporcionado por el Centre for Computing History (Cambridge, Reino Unido).

:::

Si una compañía planeaba construir un microordenador asequible, las opciones disponibles eran:

- El **Intel 8080** (1974): una CPU popular presente en el *Altair*, el primer ordenador 'personal'. Dispone de un bus de datos de 8 bits y otro de direcciones de 16 bits, además de siete registros de 8 bits. Estas prestaciones trazaron la línea entre la simple calculadora y el verdadero ordenador.
- **Zilog Z80** (1976): insatisfechos con el rumbo de Intel, antiguos ingenieros del 8080 fundaron su propia empresa para producir un sucesor 'no oficial', mejorado con más instrucciones, registros y componentes internos. Se vendía a un precio económico y podía ejecutar programas del 8080 [@cpu-re_1977, p. 86]. Amstrad y Sinclair fueron algunas de las empresas que optaron por esta CPU.
- **Motorola 6800** (1974): otra CPU de 8 bits diseñada en la misma época. Aunque competidor directo del 8080, el 6800 se programaba con un set de instrucciones más sofisticado, si bien a menor frecuencia de reloj [@cpu-arizona]. El 6800 se incluía en ordenadores de tipo todo-en-uno y "hágalo-usted-mismo", y en sintetizadores.

De forma análoga a lo ocurrido con Intel, ingenieros de Motorola frustrados con la falta de interés de la compañía en capitalizar el potencial del 6800 [@cpu-austin] se unieron a una pequeña pero ambiciosa empresa de semiconductores, **MOS**, donde desarrollaron una versión rediseñada del 6800: el **MOS 6502**. Pese a ser incompatible con el resto, el nuevo chip era mucho, *mucho* más barato de producir [@cpu-summary_costs] [@cpu-re_1981, p. 76] y fue cuestión de tiempo que muchos de los famosos fabricantes de ordenadores (Commodore, Tandy, Apple, Atari, Acorn y otros) eligieran el 6502 como pieza central de sus máquinas.

En Japón, Nintendo necesitaba un CPU barato y conocido, por lo que optó por utilizar el 6502. **Ricoh**, su proveedor de procesadores, fabricó con éxito un procesador compatible con el 6502.

### Funcionalidad central

Para comprender las capacidades de esta consola, conviene examinar primero lo que ofrece el MOS 6502 original:

- La **Arquitectura de Set de Instrucciones** (ISA, del inglés 'Instruction Set Architecture') **del 6502**: MOS quería ofrecer mejoras drásticas a costa de la compatibilidad con terceros (en especial con Motorola) [@cpu-arizona]. Así pues, el set de instrucciones del 6502 sigue manejando palabras de **8 bits** como el 6800 y otros, pero su programación no es compatible entre sí.
- Un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**. Esta era la combinación habitual en los microprocesadores de la época. En esencia, permite operar datos en bloques de ocho bits sin agotar las ubicaciones de memoria (al menos, no demasiado deprisa).
  - En teoría, las direcciones de 16 bits obligan a la CPU de 8 bits a emplear ciclos adicionales para procesar el tamaño extra. Sin embargo, gracias a los nuevos modos de direccionamiento de MOS (explicados más adelante), estas penalizaciones se aliviaron sin necesitar demasiados circuitos adicionales.
- **Tres registros de uso general** (`X`, `Y` y `A`), que pueden parecer insuficientes frente a [ficheros de registros más amplios](master-system#cpu). Esta decisión reduce costes, aunque implica que la CPU deba mover datos en memoria con mayor frecuencia. En el 6502, `X` e `Y` se denominan 'registros de índice' y se usan para direccionar memoria, mientras que `A` está conectado directamente a la ALU y se dedica a las operaciones aritméticas.
  - En comparación, el Motorola 6800 dispone de dos registros acumuladores y un único registro de índice, lo que permite un set de instrucciones más sencillo.
- Una **Unidad Aritmético-Lógica** (ALU, del inglés 'Arithmetic Logic Unit') **de 8 bits**, lo que no sorprende en una CPU de 8 bits; aunque vale la pena señalar que otros como el Zilog Z80 incorporaban una [ALU de 4 bits](master-system#cpu).
- Un **puntero de pila de 8 bits**: no es algo que suela tratarse en este tipo de análisis, pero merece mención por su notable divergencia respecto al diseño de Motorola. Como se ha indicado, el bus de direcciones es de 16 bits, por lo que un componente dependiente de memoria como el puntero de pila debería, en principio, tener ese mismo tamaño. No obstante, MOS optó por reducirlo a la mitad y almacenar la pila en un rango fijo de memoria. Con todo, fue una ingeniosa medida de reducción de costes, ya que alentó a los desarrolladores a adoptar técnicas de programación eficientes para aprovechar al máximo el espacio de pila.
- **13 modos de direccionamiento**. Gracias a la inclusión de dos registros de índice, los programas pueden elegir entre múltiples formatos para acceder a la memoria. Algunos están optimizados para el direccionamiento de página cero (los primeros 256 bytes de memoria), mientras que otros codifican una dirección de búsqueda para recuperar la dirección real de forma dinámica. En definitiva, esto permitió ahorrar el mayor número posible de ciclos de memoria, a costa de una mayor complejidad.
  - En comparación, el 6800 y su único registro de índice solo ofrecen siete modos de direccionamiento, sin equivalente para los tipos más flexibles.

Como puede apreciarse, la notable ingeniería del 6502 permitió a MOS ofrecer un producto sólido a un precio enormemente asequible.

#### El enigma de la licencia de Ricoh

Hasta hoy en día sigue sin ser claro *cómo* Ricoh logró clonar el 6502. Uno esperaría que MOS le diera la licencia del diseño del chip a Ricoh, pero hay muchas contradicciones al respecto:

- Tanto la variante de Ricoh como la de MOS presentan el mismo diseño, pero la de Ricoh contiene buses cortados (deshabilitando ciertas funciones) [@cpu-differences]. Lo detallaré más adelante.
- No se ha encontrado ningún documento que afirme explícitamente que MOS dio la licencia del 6502 a Ricoh.
- Un artículo publicado en 2008 por Nikkei Trendy establece que Ricoh obtuvo la licencia de Rockwell, un fabricante autorizado de chips [@cpu-trendi]. Sin embargo, es debatible si una segunda fuente pudo otorgar la propiedad intelectual a un tercero, mucho menos con la aprobación de MOS.
- No sería la primera vez que Nintendo pudo eludir derechos de propiedad intelectual, dado que el juicio *Ikegami Tsushinki vs. Nintendo* dictaminó que Nintendo no era dueño del código del Donkey Kong original [@cpu-dk].

#### Funcionalidad descartada

El 2A03 de Ricoh omite el modo **Binary-Coded Decimal** (BCD) incluido originalmente en el 6502 [@cpu-visualbcd]. El BCD permite la codificación de cada dígito decimal de un número como un binario separado de 4 bits. El 6502 usa palabras de 8 bits – lo que significa que cada palabra almacena dos dígitos decimales.

Como un ejemplo para los curiosos, el número decimal `42` se representa como:

- `0010 1010` en binario.
- `0100 0010` en BCD.

Este modo es útil en aplicaciones que requieran tratar cada dígito decimal por separado (como por ejemplo, un reloj digital). Sin embargo, requiere más almacenamiento ya que cada palabra de 8 bits solo puede codificar hasta el número decimal `99` - mientras que el binario tradicional puede codificar hasta el `255`.

Ricoh deliberadamente rompió el modo BCD en su chip cortando las líneas de control que lo activan. Esto se hizo presumiblemente para evitar el pago de regalías a MOS, dado que BCD estaba patentado por ellos (y la legislación que permitía registrar derechos de autor sobre diseños de circuitos integrados no se estableció sino hasta 1984 [@cpu-protection_act]).

### Memoria

Tanto el Ricoh 2A03 como el MOS 6502 contienen un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**, lo que les permitía acceder hasta a **64KB de memoria**. Entonces, ¿cómo llenó Nintendo ese espacio de memoria?

Por un lado, la placa base contiene un chip que otorga **2 KB de RAM estática** (SRAM) [@cpu-sample_ram]. Nintendo llama esta área «Work RAM» (WRAM) y puede usarse para almacenar:

- Variables para manejar el estado del juego y/o para buscar información.
- La «pila», la cual temporalmente guarda los valores de registros mientras que el procesador ejecuta subrutinas.
- Un «área de búfer» para que el procesador pueda copiar datos grandes entre dos ubicaciones.

Por el otro lado, los componentes del sistema son **mapeados en memoria** [@cpu-cpu_map], lo que significa que son accedidos usando direcciones de memoria, por lo que ocupan parte del espacio de direcciones del procesador. Por lo tanto, el espacio de memoria del procesador se llena de direcciones que apuntan al cartucho del juego, la WRAM, el PPU, la APU y dos controles (no se preocupen por cada componente, ya que se explican a lo largo de este artículo).

#### Fallo de segmentación

Heredada del diseño de MOS, esta consola incorpora también una 'anomalía' especial denominada **Bus abierto** (Open Bus): si una instrucción intenta leer desde una dirección no mapeada o inválida, se devuelve en su lugar el último valor leído [@cpu-open_bus]. Si el programa no lo gestiona adecuadamente, la ejecución puede continuar en un estado impredecible.

#### Datos del cartucho/juego

Solo en caso de que no lo sepas, los juegos de NES se distribuyen en forma de cartuchos, y los buses del cartucho se conectan directamente al procesador.

Nintendo conectó las líneas del cartucho de forma que sólo se puede acceder a 49120 Bytes (~ 49.97 KB) de datos del cartucho [@cpu-cpu_map]. Ahora, ¿a qué me refiero con "datos del cartucho"? Bueno, a cualquier chip conectado a esos buses, por ejemplo:

- Una **ROM de programa** donde reside el programa del juego. Esto excluye los datos gráficos, como verás después en la sección de 'Gráficos'. Naturalmente y a diferencia de otros chips, este es obligatorio.
- **Chips de RAM** para extender la WRAM.
- Una **RAM alimentada por baterías** para almacenar partidas.

El hecho de que haya diferentes combinaciones se debe a que al procesador no le importa de qué componente esté leyendo información, solo ve las ubicaciones de la memoria. Así que es a criterio de los desarrolladores de escoger (o crear) un esquema factible para guardar el juego.

![PCB de Super Mario Bros [@photography-nrom].](nrom.png){.tabs-nested .active title="Original"}

![La misma PCB con las partes importantes etiquetadas. El significado del chip "Lockout" se explica en la sección "Antipiratería".](nrom_marked.png){.tabs-nested-last title="Marcado"}

Por ejemplo, 'Super Mario Bros' de Nintendo utilizó un diseño que llamaron _NES-NROM-256_ y que consiste de 32KB de programa ROM y 8KB de 'Character ROM' para gráficos (veremos más sobre esto en la sección de 'Gráficas') [@cpu-nrom]. La _NES-NROM-256_ también se preparó para almacenar hasta 3KB de WRAM extra, aunque el juego no haga uso de ella.

#### Más allá de las capacidades existentes

Una de las mayores limitaciones de los buses de direcciones de 16 bits (que afectan la 3ra y 4ta generación de consolas) es su espacio compacto de direcciones. Hoy en día, los ordenadores de 32 bits pueden direccionar hasta 4GB de memoria (y las máquinas de 64 bits se dan el lujo de disfrutar hasta 16 exabytes) así que esto ya no es una preocupación, pero en aquel entonces la NES sólo tenía 64KB de espacio de direcciones y una gran parte de este era ocupada por el hardware mapeado en memoria (algo que [sus competidores evitaron](master-system#accessing-the-rest-of-the-components)).

Entonces, ¿esto significaba que los desarrolladores sólo podían desarrollar juegos que no excedieran el límite de 49.97KB? ¡Por supuesto que no! Si algo nos ha enseñado la historia, es que siempre hay una solución ingeniosa a un problema desafiante; y este problema se abordó con un **Mapeador**.

![Representación simplificada de cómo un mapeador extiende las capacidades de direccionamiento de la CPU. Con la inclusión de un mapeador, la CPU puede acceder a bancos extra (grupos de direcciones) de una gran ROM de programa. Aunque el juego/programa tiene la nueva tarea de cambiar manualmente entre bancos siempre que sea necesario.](_diagrams/mapper/mapper.png){.tabs-nested .active title="Con mapeador"}

![La misma configuración pero sin un mapeador instalado. Aunque es más simple y económico, la CPU solo puede acceder a un número finito de bancos.](_diagrams/mapper/no_mapper.png){.tabs-nested-last title="Sin mapeador"}

Un mapeador es un chip extra incluido en el cartucho que se ubica entre la memoria del chip y la línea de direcciones de la consola. Su tarea principal es extender el espacio de direcciones para que los desarrolladores puedan tener más chips. Esto se logra a través de **cambio de bancos**: las direcciones de memoria se agrupan en bancos, y el mapeador dispone de interruptores (controlados por direcciones de memoria) para alternar entre bancos. Ahora, la CPU sigue viendo la misma cantidad de memoria, así que es el juego, que fue programado con un mapeador presente, quien está a cargo de operarlo. Debido a su rentabilidad, los mapeadores eran la orden del día en la tecnología de los 80 hasta principios de los 90.

![PCB de Super Mario Bros 2 [@photography-tsrom]. Super Mario Bros. 3 también usa este diseño, pero incluye una ROM de programa de 256 KB en su lugar.](tsrom.png){.tabs-nested .active title="Original"}

![La misma imagen con las partes importantes marcadas. Al principio, pensé que la SRAM adicional era para guardar partidas, pero luego me di cuenta de que no hay guardados en este juego (y tampoco hay una batería). En realidad, ese chip de RAM se usa para almacenar un nivel descomprimido.](tsrom_marked.png){.tabs-nested-last title="Marcado"}

Volviendo a la NES, juegos como 'Super Mario Bros 2' y 'Super Mario Bros 3' venían con el mapeador 'MMC3' (fabricado por Nintendo) en sus cartuchos. En comparación, el MMC3 proporcionó hasta 512 KB de espacio para la ROM de programa, hasta 256 KB para Character memory y hasta 8 KB de WRAM extra [@cpu-mmc3]. Ahora puedes ver porqué 'Super Mario Bros 3' difiere significativamente en calidad comparado con su primer entrega.

En general, mientras que esta consola pueda parecer limitada al examinar sus características internas, Nintendo se aseguró de que se pudiera adaptar a medida que evolucionaba la tecnología. Por otro lado, mientras que esta técnica ayudó a mantener los costos de la consola bajos, trasladó parte de la responsabilidad al cartucho del juego. Así, la calidad del juego y los costos del cartucho eran dos aspectos que los desarrolladores debían equilibrar.

## Gráficos

Los gráficos son generados mediante un chip propietario llamado el **Picture Processing Unit** (PPU). Este es uno de los chips que le da a la NES una *identidad*. Para decirlo de otra manera, cualquiera podría elegir una CPU 6502 de la tienda de hardware, así que ¿por qué es la NES diferente de, digamos, una Apple 2 o una Commodore 64? Bueno, lo que distingue a la NES de otras máquinas son los chips que rodean a la CPU: la PPU y la APU. Estos dan forma a las capacidades gráficas y de audio de la NES respectivamente.

![El chip PPU europeo en la placa base de mi NES.](ppu_chip.webp)

Dicho esto, el PPU renderiza gráficos 2D llamados **sprites** y **fondos**, emitiendo el resultado a la señal de video.

### Organizando el contenido

![Arquitectura de memoria de la PPU](_diagrams/ppu.png)

Para renderizar algo en la pantalla, la PPU debe saber *qué* gráficos dibujar, *dónde* colocarlos en la pantalla; y *cómo* dibujarlos (es decir, qué paleta usar).

Para responder a estas preguntas, el PPU viene pre-programado con un mapa de memoria diferente que busca el siguiente tipo de datos:

- Los datos de gráficos se extraen del cartucho del juego, que contiene un chip dedicado llamado **Character memory** que almacena los dibujos 2D (llamados **tiles**) organizados en una estructura de datos llamada **Pattern Table**. La Character memory se materializa en forma de 'Memoria de Solo Lectura' (ROM) o 'Memoria de Acceso Aleatorio' (RAM) dependiendo de si el juego se distribuye con un conjunto inmutable de gráficos o si el procesador debe intervenir, respectivamente.
  - El PPU direcciona **hasta 8 KB** de Character memory organizada en dos grupos de 4 KB cada uno.
- Los metadatos que le dicen al PPU 'dónde' y 'cómo' dibujar los gráficos se encuentran en otras áreas:
  - Un separado **2 KB de SRAM** se ajusta en la placa base, esta vez dedicada a datos gráficos. Nintendo llama a este espacio **Video RAM** (VRAM) y almacena dos estructuras de datos llamadas **Nametables**.
  - El PPU incorpora **256 B** de DRAM para almacenar la **Object Attribute Memory** (OAM).
  - Por último, la PPU también alberga **4 B** de memoria para definir paletas de colores.

No te preocupes por la nueva terminología, el significado de estas estructuras de datos se discute paso a paso en los siguientes párrafos.

### Construyendo el frame

Al igual que sus contemporáneos, este chip está diseñado en relación a los monitores CRT. No hay un frame-buffer como tal: la PPU renderizará en sintonía con el haz del CRT, construyendo la imagen al vuelo.

El PPU dibuja frames con una dimensión fija de **256x240 píxeles** [@graphics-overscan]. Por desgracia, debido a las discrepancias en los estándares de video analógico por todo el mundo, la imagen diferirá en apariencia dependiendo de la región del dispositivo (NTSC o PAL) desde la cual se muestra. En resumen, los televisores NTSC recortarán los bordes superior e inferior para acomodar el overscan (solo ~224 líneas de escaneo son visibles), por lo que estos bordes son considerados 'zonas peligrosas' por los desarrolladores cuando deciden dónde colocar elementos en el juego. Por otro lado, los televisores PAL no recortarán los bordes pero mostrarán barras negras adicionales para llenar la señal más alta (PAL usa 288 líneas de escaneo).

Detrás de las escenas, el frame que produce el PPU está compuesto por dos capas diferentes. Para demostrar cómo funciona, utilizaremos *Super Mario Bros.*:

#### Mosaicos {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined max_subfigures=1}

![Dos Pattern Tables con múltiples tiles.](ppu_mario/chr_map.png){.active title="Todo"}

![Un único mosaico.](ppu_mario/single.png){title="Individual"}

Tiles encontrados en la Character ROM (a efectos de demostración se utiliza una paleta por defecto).

:::

Para comenzar, el PPU usa **tiles** como ingrediente básico para producir sprites y fondos.

La NES define los tiles como mapas básicos de **8x8 píxeles**, estos se almacenan en la **Character memory** (residiendo en el cartucho del juego) y organizados en una gran estructura de datos llamada **Pattern Table** [@graphics-rust]. Cada tile ocupa 16 B y una Pattern Table alberga 256 tiles [@graphics-pattern]. Dado que el PPU direcciona hasta 8 KB de Character memory, puede acceder a hasta dos Pattern Tables.

Dentro de un tile, cada uno de sus píxeles se codifica utilizando un valor de 2 bits, que referencia a uno de cuatro colores de una paleta. Los programadores pueden definir hasta ocho paletas (cuatro para el fondo y las demás para los sprites). Los colores referenciados en cada paleta apuntan a una 'paleta maestra' compuesta de 64 colores [@graphics-palettes], que representa todos los colores que esta consola puede producir. Las paletas se componen de cuatro colores, aunque uno está reservado para `transparente`.

Para empezar a dibujar algo en la pantalla, los juegos llenan un conjunto de tablas con referencias a tiles en la Character memory. Cada tabla es responsable de una capa (sprite o fondo) del frame. Luego, el PPU lee esas tablas y compone las líneas de escaneo que serán proyectadas por el cañón de CRT.

Ahora explicaré cómo funciona cada capa/tabla y cómo difieren en términos de funcionalidad.

#### Capa de fondo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Mapa de fondo asignado.](ppu_mario/nametable.png){.active title="Global"}

![Mapa de fondo asignado con la zona seleccionada marcada.](ppu_mario/nametable_marked.png){title="Seleccionado"}

Mapa de fondo configurado con mirroring vertical, lo que permite un desplazamiento horizontal suave. Sin embargo, solo se puede usar una mitad.

:::

La capa de fondo es un mapa de 512x480 píxeles que contiene tiles estáticos [@graphics-nametables]. Recordarás que el frame visible es mucho más pequeño, por lo que el juego decide qué parte de la capa se selecciona para mostrar. Los juegos pueden mover el área visible durante el gameplay, así es como se consigue el **Efecto de desplazamiento (Scrolling effect)**.

Para ahorrar memoria, grupos de cuatro tiles se combinan en mapas de 16x16 píxeles llamados **bloques**, dentro de los cuales todos los tiles comparten una paleta de colores.

Las **Nametables** (almacenadas en VRAM) especifican qué tiles mostrar en la capa de fondo. La PPU busca cuatro 1024-byte Nametables, cada uno corresponde a un cuadrante de la capa. Sin embargo, solo hay 2 KB de VRAM disponibles! Como consecuencia, solo se pueden almacenar dos Nametables sin hardware adicional del cartucho. No obstante, las dos restantes aún deben direccionarse en algún lugar: la mayoría de los juegos simplemente apuntan las dos restantes donde están las primeras dos (esto se llama **mirroring**).

Aunque al principio esta arquitectura pueda parecer defectuosa, fue diseñada para mantener bajos los costes mientras ofrece **expandibilidad** sencilla: si los juegos necesitaban un fondo más amplio, se podía incluir VRAM extra en el cartucho.

Los últimos bytes de cada Nametable almacenan una tabla de 64 bytes llamada **Attribute table**, que especifica qué paleta de colores se asigna a cada bloque [@graphics-attribute_table].

#### Capa de Sprites {.tab}

![Capa de sprite renderizada.](ppu_mario/sprite_layer.png) {.tab-float .pixel}

Los sprites son tiles que pueden moverse por la pantalla. También pueden superponerse entre sí o aparecer detrás del fondo. El gráfico visible se decidirá según su valor de prioridad (es el mismo concepto que 'capas' en el software de diseño gráfico tradicional).

La tabla **OAM** especifica qué tiles serán utilizados como sprites [@graphics-oam]. Además del índice de tiles, cada entrada contiene una posición (x,y) y varios atributos (paleta de color, una prioridad y flip flags). Esta tabla se almacena en una DRAM de 256 bytes en el chip de la PPU.

La tabla OAM puede ser rellenada por la CPU. Sin embargo, en la práctica, esto puede ser muy lento (con el riesgo de corromper el frame si no se realiza a tiempo), por lo que la PPU contiene un pequeño componente llamado **Direct Memory Access** o 'DMA' el cual puede ser programado (alterando los registros de la PPU) para recuperar la tabla desde WRAM. Con DMA está garantizado que la tabla será cargada cuando el próximo frame sea dibujado, ¡pero hay que tener en cuenta que la CPU será detenida durante la transferencia!

La PPU está limitado a **ocho sprites por línea de escaneo** y hasta **64 sprites por frame**. El límite de líneas de exploración se puede superar gracias a una técnica llamada 'rotación del orden de OAM', donde el juego altera manualmente el orden de las entradas en OAM. Esto hace que la PPU renderice un conjunto de sprites diferente en cada frame, y la velocidad del haz del CRT engañará al usuario para que vea más sprites de los permitidos. Sin embargo, también parecerá que parpadean en la pantalla.

#### División del fondo {.tab}

![Capa de fondo renderizada resaltando las dos porciones con diferentes valores de desplazamiento definidos. Solo la segunda porción se desplaza cuando Mario se mueve.](ppu_mario/split.png) {.tab-float .pixel}

Antes de continuar, hay algo que no te he contado aún. Si juegas a Super Mario Bros, notarás que cuando Mario se mueve, la escena se desplaza sin problemas. Sin embargo, también observarás que la parte superior (donde están las estadísticas) permanece estática **¡aunque ambas porciones son parte de la misma capa de fondo!** Entonces, ¿qué está pasando aquí? Bueno, el juego está alterando los valores de desplazamiento a mitad del frame para mostrar el supramundo y las estadísticas (que residen en una porción fija del fondo) al mismo tiempo. La NES no proporciona esta característica de manera nativa, pero el juego deduce los tiempos observando el estado de la PPU (manifestado a través de su registro de estado [@graphics-ppustatus]).

Para lograr esto, los juegos utilizan una técnica llamada **Sprite 0 Hit**. Super Mario Bros instruye a la PPU para que renderice un sprite ficticio detrás de la moneda, este resulta ser el primer sprite dibujado dentro del frame. Después de que la PPU lo haga, actualiza su registro de estado con un flag que indica que el primer sprite (también conocido como 'sprite 0') ha sido dibujado. Mientras tanto, el juego está constantemente verificando a mitad del frame si el estado del sprite 0 ha sido señalado (también conocido como 'golpeado'), si eso sucede, el juego procede a actualizar la propiedad de desplazamiento de la tabla de fondo para moverlo a donde está Mario.

En general, 'Sprite 0 Hit' es un procedimiento muy delicado, ya que es fácil descoordinar los tiempos (la bandera del sprite 0 no se borra después de sondearla, lo que lleva a positivos 'duplicados' [@graphics-chibiakumas]). Además, como esta rutina se repite indefinidamente, puede ser bastante costosa (en términos de ciclos de CPU) de ejecutar. Por otro lado, mapeadores más recientes asumieron esta función mediante el uso de interrupciones automáticas que se activan cada vez que se alcanza una línea de escaneo arbitraria [@graphics-nesdoug] (una técnica mucho más eficiente), lo que mejoró significativamente las capacidades visuales de Super Mario Bros 3, por ejemplo.

#### Resultado {.tab}

![¡Tachán!](ppu_mario/result.png) {.tab-float .pixel}

Una vez finalizado el frame... ¡es hora de pasar al siguiente!

Sin embargo, la CPU no puede modificar ninguna tabla que esté siendo utilizada por la PPU, de lo contrario, pueden aparecer artefactos en la pantalla. Entonces, cuando se completan todas las líneas de escaneo, la PPU dispara la interrupción de **Vertical Blank** (V-Blank) en la CPU [@graphics-vblank]. Esto notifica al juego que puede comenzar a actualizar las tablas sin romper la imagen presentada. En ese momento el haz del CRT apunta debajo del área visible de la pantalla, dentro del overscan (o borde inferior).

Solo un puñado de registros de la PPU pueden actualizarse fuera de la ventana de V-Blank [@graphics-outside_vblank], lo que explica la capacidad de desplazar la capa de fondo a mitad del frame.

### Secretos y Limitaciones {.tabs-close}

Si crees que habría sido preferible un sistema de frame-buffer con memoria asignada para almacenar el frame completo: los costos de la RAM eran muy altos y el objetivo de la consola era ser asequible. Ahora déjame mostrarte por qué este diseño resultó ser muy eficiente y flexible.

#### Multidesplazamiento {.tabs .active}

![Super Mario Bros. 2. Configuración de Nametable para desplazamiento vertical (mirroring horizontal).](secrets/multiscrolling_mirror.png) {.tab-float .pixel}

![Super Mario Bros. 3. Mario puede correr y volar, así que la PPU necesita desplazarse diagonalmente. Observa que el borde derecho muestra la paleta de colores incorrecta, mientras que el borde izquierdo tiene una máscara aplicada.](secrets/multiscrolling.png) {.tab-float .pixel}

Algunos juegos requieren que el personaje se mueva verticalmente, por lo que el nametable será configurado con **Horizontal Mirroring**. Otros juegos requieren que el personaje se mueva de izquierda a derecha, en este caso se utiliza el **Vertical Mirroring**.

Cualquiera de los dos tipos de mirroring permitirá que la PPU actualice los tiles de fondo sin que el usuario lo note: hay mucho espacio para desplazarse mientras se renderizan nuevos tiles a distancia.

Pero ¿qué ocurre si el personaje desea moverse en diagonal? La PPU puede moverse en cualquier dirección, pero sin VRAM extra, los bordes están forzados a compartir la misma paleta de colores (recuerda que los tiles se agrupan en bloques).

Por esta razón, algunos juegos como *Super Mario Bros. 3* muestran gráficos anómalos en el borde derecho de la pantalla cuando Mario se mueve (el juego está configurado para desplazarse verticalmente) [@graphics-seam]. Es posible que necesitaran minimizar el coste del hardware por cartucho (ya que este juego ya tiene un potente mapper instalado).

Como un interesante *arreglo*: la PPU permitía a los desarrolladores aplicar una máscara vertical sobre los tiles, ocultando efectivamente parte del área defectuosa.

#### Cambio de Tiles {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![Primeras líneas de exploración.](secrets/multiplexing_1.png){.pixel .active title="Principio"}

![Últimas líneas de exploración.](secrets/multiplexing_2.png){.pixel title="Final"}

![Frame actual mostrado al usuario.](secrets/multiplexing_complete.png){.pixel title="Visualizado"}

Frames hipotéticos renderizados con los tiles disponibles en líneas de exploración específicas.

:::

Otra especialidad de Super Mario Bros. 3 es la cantidad de gráficos que puede mostrar.

El juego muestra más tiles de fondo de los estrictamente permitidos. ¿Cómo lo hacía? Si sacamos capturas de pantallas en dos momentos diferentes mientras la imagen se está generando, podemos observar que la imagen visualizada está compuesta de *dos* composiciones diferentes.

Esta es otra de las hechicerías del mapeador MMC3, que no solo se utilizaba para acceder a espacio extra en la ROM de programa, sino que también ensanchaba el espacio de la Character ROM al conectar dos chips de Character memory diferentes. Al comprobar cuál parte de la pantalla está solicitando la PPU, el mapeador redirigirá a uno u otro chip, permitiendo así más mosaicos únicos en la pantalla de los que originalmente se soportaban [@graphics-n3s].

#### Comportamiento curioso {.tab}

A lo largo de mi investigación, me encontré con muchos artículos interesantes que explican el comportamiento inusual de la PPU, así que pensé en mencionar algunos aquí:

- A diferencia del [VDP de la Master System](master-system#graphics), que genera colores RGB que luego se codifican en señales NTSC/PAL para la transmisión, la **PPU de la NES lo hace todo a la vez** [@graphics-palettes]. Por lo tanto, no hay una conexión uno a uno entre los colores de la paleta maestra de la PPU y el espacio de color RGB estándar (ampliamente adoptado por la tecnología actual). Esto deja cierto margen para la interpretación y, como consecuencia, varios emuladores mostrarán una paleta diferente.
  - Las discrepancias entre paletas RGB son más evidentes con el kit de 'bricolaje' de Tim Worthington que agrega salida de señal RGB a la NES, ya que también implementa un interruptor que elige entre tres paletas predefinidas. [@graphics-nesrgb].
- La paleta maestra contiene un **color 'maldito'** (`$0D`) que podría estropear la señal de TV NTSC [@graphics-cursed_colour]. Bueno, lo que sucede es que algunas televisiones confunden la señal para mostrar ese color con la señal de blanqueo, por lo que podría ocurrir un parpadeo.
- El PPU depende de la DRAM para almacenar su OAM. Ahora bien, la DRAM necesita ser refrescada constantemente para evitar la pérdida de datos (a diferencia de la SRAM), y sucede que la PPU no refrescará la DRAM cuando no esté renderizando el frame [@graphics-oam]. Esto se manifiesta durante el blanking vertical. Por esta razón, se desaconseja actualizar la OAM fuera del blanking vertical, ya que el período de no actualización durante el V-blank habrá corrompido parte de la tabla.
  - La variante PPU para sistemas PAL no se ve afectada por esto, ya que se refresca durante V-Blank (que dura más en sistemas PAL).

## Audio {.tabs-close}

Un componente dedicado llamado **Audio Processing Unit** (APU, o 'Unidad de Procesamiento de Audio') ofrece este servicio [@audio-apu]. Ricoh lo incorporó dentro del chip de la CPU presumiblemente para evitar la clonación no autorizada de la CPU y la APU.

### Funcionalidad

Este circuito de audio se conoce comúnmente como **Generador de Sonido Programable** (PSG, del inglés 'Programmable Sound Generator'), lo que implica vagamente que solo puede producir un conjunto predefinido de formas de onda, lo cual es _mayormente_ cierto en este caso. La APU secuencia datos de audio sobre **cinco canales** de audio, cada uno reservado para una forma de onda o señal específica. Cada canal contiene diferentes propiedades que alteran la altura, el sonido, el volumen y/o la duración de la forma de onda. Se mezclan continuamente y se envían a través de la señal de audio de salida.

La funcionalidad de la APU se expone a través de direcciones de memoria, la CPU lee los datos relacionados con la música encontrados en la ROM de programa y programa la APU en consecuencia.

Además, en el modelo Famicom en particular se proporcionan pines en el cartucho que envían la señal de audio mezclada al cartucho, para que este último pueda mezclarla con **canales extra** (requiriendo chips adicionales) [@general-cartridge].

Repasemos ahora los cinco canales que proporciona la APU [@audio-review]:

#### Pulso {.tabs .active}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de pulso 1.](pulse_single_1){video="true" title="Pulso 1"}

![Osciloscopio del canal de pulso 2.](pulse_single_2){.active video="true" title="Pulso 2"}

![Osciloscopio de todos los canales de audio.](pulse_full){video="true" title="Completo"}

Mother (1989).

:::

Los primeros **dos canales** producen **ondas de pulso** [@audio-apupulse]. Cuando se escuchan, exhiben un sonido *beep* distintivo que se utiliza principalmente para **melodía o efectos de sonido**. El secuenciador respectivo puede generar tres tipos de ondas de pulso, hechas a partir de variar el ancho de pulso (a.k.a. **ciclo de trabajo**). Los circuitos también están conectados a una **unidad de barrido** (permitiendo doblar el tono) y a un **generador de envolvente** para reducir el volumen con el tiempo (también conocido como **decadencia**).

La mayoría de los juegos utilizan un canal de pulso para la melodía y otro para el acompañamiento. Cuando llega el momento de ejecutar un efecto de sonido, normalmente el juego reemplaza el canal de acompañamiento para reproducir el efecto y luego lo devuelve al acompañamiento, lo que permite mantener la melodía sin interrupciones.

Creo que es justo decir que las ondas de pulso son unos de los emblemas de esta generación de consolas. Asumo que su adopción se realizó puramente por razones de rentabilidad: la (limitada) CPU solo puede procesar tantos datos a la vez, y las ondas de pulso son ideales en el sentido de que no requieren muchos parámetros para reproducir melodías simples (lo que, a su vez, libera ciclos de CPU para otras operaciones).

#### Triángulo {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de onda triangular.](triangle_single){.active video="true" title="Triángulo"}

![Osciloscopio de todos los canales de audio.](triangle_full){video="true" title="Completo"}

Mother (1989).

:::

Una de las especialidades de la APU, en comparación con la competencia, es la capacidad de producir **ondas triangulares**. A menudo se usan como una **línea de bajo** para la melodía. Además, al modificar su tono dramáticamente también se puede usar para **percusión**.

La APU tiene un canal reservado para este tipo de onda. Detrás de escena, un secuenciador dedicado toma 32 ciclos para generar una señal triangular [@audio-aputriangle], esta limitación hace que la forma de onda triangular resultante tome la forma de una escalera.

Por otro lado, el circuito respectivo no proporciona control de volumen. En cualquier caso, algunos juegos encontraron otras formas jugando con el control de volumen del mezclador.

#### Ruido {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de ruido.](noise_single){.active video="true" title="Ruido"}

![Osciloscopio de todos los canales de audio.](noise_full){video="true" title="Completo"}

Mother (1989).

:::

El concepto de 'Ruido' se atribuye a una serie de formas de onda que no siguen ningún patrón u orden. A su vez, nuestros oídos lo interpretan como estática blanca. Dicho esto, la APU asigna un canal que puede reproducir diferentes tipos de ruido.

Detrás de escena, el generador de ruido depende de un generador de envolvente (similar al canal de pulso) que se silencia aleatoriamente por una puerta OR [@audio-apunoise]. La condición para silenciar depende del valor de un registro de desplazamiento de 15 bits conectado a un bucle de retroalimentación. En resumen, esto hace que el circuito genere una señal con patrones _pseudo-impredecibles_, y por lo tanto, ruido.

En términos de control, 4 bits alteran el período del generador de envolvente y un bit altera el 'Modo' del registro de desplazamiento. Eso deja 32 preajustes de ruido disponibles. La mitad (16) de estos preajustes producen **estática limpia**, y la otra mitad produce **estática robótica**.

En términos generales, los juegos utilizan el canal de ruido para efectos de percusión o ambiente.

#### Muestra {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Osciloscopio del canal de muestra.](sample_single){.active video="true" title="Muestra"}

![Osciloscopio de todos los canales de audio.](sample_full){video="true" title="Completo"}

Mother (1989).

:::

Los "Samples" son porciones de música grabadas que pueden ser reproducidas. Como puedes ver, los samples no se limitan a una sola forma de onda, pero consumen mucho más espacio.

La APU tiene un canal dedicado a samples. Aquí, las muestras están limitadas a una **resolución de 7 bits** (codificadas con valores de `0` a `127`) y una **frecuencia de muestreo de ~15,74 kHz** [@audio-2a03ref]. Para programar este canal, los juegos pueden transmitir valores de 7 bits (lo que roba muchos ciclos y almacenamiento) o usar **modulación delta** para codificar únicamente la variación entre la siguiente muestra y la anterior.

La implementación de modulación delta en la APU solo recibe valores de 1 bit, esto significa que los juegos solo pueden indicar si la muestra aumenta o disminuye en `1` cada vez que el contador se activa. Entonces, a costa de la fidelidad, la modulación delta puede ahorrar a los juegos tener que transmitir valores continuos a la APU.

Dado que programar este canal lleva más espacio y ciclos de CPU, los juegos normalmente almacenan pequeñas piezas (como muestras de tambores) que pueden reproducirse repetidamente. Sea como sea, a lo largo de la vida útil de la NES, numerosos desarrolladores han ideado usos ingeniosos para este canal.

### Secretos y Limitaciones {.tabs-close}

Si bien la APU no alcanzó la calidad de un vinilo, casete o CD, los programadores encontraron diferentes maneras de expandir sus capacidades, principalmente gracias a la arquitectura modular de la NES.

#### Canales extras {.tabs .active}

![Osciloscopio de Castlevania III (USA/Europa, 1989).](castlevania_usa){.tabs-nested .tab-float .active video="true" title="Americano"}

![Osciloscopio de Akumajō Densetsu (versión japonesa de Castlevania III, 1989).](castlevania_jap){.tabs-nested-last video="true" title="Japonés"}

¿Recuerdas que el Famicom proporcionaba pines exclusivos en el cartucho disponibles para la expansión de sonido? Bueno, juegos como *Castlevania 3* aprovecharon esto y añadieron un chip extra llamado **Konami VRC6**, que añadía **dos ondas de pulso adicionales y una onda de diente de sierra** a la mezcla.

Echa un vistazo a los dos ejemplos que muestran la diferencia entre las versiones japonesa y americana del juego (esta última se ejecuta en la variante NES, que no proporcionó capacidades de expansión de sonido).

#### Tremolo {.tab}

![Vista de osciloscopio de Final Fantasy III (1990).](tremolo_full){.tab-float video="true"}

En lugar de incrementar los costes de los cartuchos, algunos juegos priorizaron la creatividad sobre la tecnología para añadir más canales.

En este ejemplo, Final Fantasy III recurrió a efectos de trémolo para dar la sensación de canales adicionales.

### Una observación más refinada {.tabs-close}

Ahora que has echado un vistazo a lo que la APU es capaz de hacer, déjame mostrarte un método alternativo para observar cómo se comporta su sonido. Esto no solo complementará lo que ya sabes sobre la APU, sino que también proporcionará un examen más objetivo, especialmente porque ya no dependerá de tus oídos.

Primero lo primero, comencemos con una rápida introducción a la teoría del sonido.

Gracias a los principios del **Análisis de Fourier**, podemos descomponer cada sonido que escuchamos en una **suma de ondas sinusoidales** de diferentes frecuencias y amplitudes [@audio-complexwaveforms]. La onda sinusoidal más grave (a la frecuencia más baja) se llama **fundamental** y el resto se llaman **sobretonos**. Si añades la onda fundamental y sus sobretonos, obtienes el sonido original de vuelta. Dicho esto, con los sonidos que tienen un tono reconocible, encontrarás que la mayoría (si no todos) de los sobretonos tienen frecuencias que son múltiplos de la frecuencia fundamental. Por lo tanto, estos sobretonos se llaman **armónicos** [@audio-harmonics].

Los armónicos se convertirán en un tema recurrente en esta sección, ya que las formas de onda como los pulsos, triángulos y dientes de sierra siguen una fórmula que dicta los armónicos que contienen. De lo contrario, estas formas de onda pueden desviarse de su forma 'perfecta'.

#### Introducción a los espectrogramas

Dado que las ondas sinusoidales son ahora el ingrediente clave que puede componer cualquier sonido, ahora podemos analizar los sonidos que escuchamos por sus ondas sinusoidales. Ahora, para cualquier tipo de análisis de datos, no hay nada más conveniente que dibujar un gráfico para organizar grandes cantidades de información. Pues bien, en el caso del análisis de sonido, tenemos **espectrogramas**. Estos codifican toda la información de una muestra de audio en una sola gráfica. El eje X denota el tiempo (en segundos), el eje Y denota las frecuencias (en Hz) de las ondas sinusoidales producidas durante ese tiempo y el eje Z (brillo del color de cada punto) denota la potencia/volumen (en decibelios) de cada frecuencia.

![Ejemplo de espectrograma visualizando seis segundos de un canal de pulso.](spectrogram_example){video="true"}

Como puedes observar en este ejemplo, cada línea horizontal (también conocidas como secuencias de puntos) corresponde a una onda sinusoidal (la más baja es la fundamental, mientras que el resto son los armónicos) y su brillo indica la amplitud. Con esto en mente, podemos extraer la siguiente información:

- Con el tiempo, las líneas horizontales tienden a desplazarse drásticamente. Ese es el tono del canal de Pulso cambiando.
- El inicio de cada nota es fuerte (los puntos son brillantes) seguido de un rápido apaciguamiento. Esa es la envolvente de la APU en acción.
  - Además, al final del período de decadencia, aparece una línea vertical brillante. Es bastante breve, por lo que no es fácilmente audible, pero, en cualquier caso, eso es ruido (un sonido de golpe corto) y sospecho que es una deficiencia de la APU.
- Mantener una nota por más de 0.25 segundos hace que emerja el **vibrato** (fluctuaciones continuas en el eje Y). No estoy seguro si eso es intencional (usando la función de barrido) o un efecto adverso de la APU.

Nota cómo la mayoría de estas observaciones no son fácilmente derivadas simplemente escuchando una muestra de audio, esta es la razón de escribir esta sección.

#### Trazando la APU

Para estudiar la APU de la NES, he compilado cinco espectrogramas, cada uno correspondiente a un canal de la APU usando los ejemplos anteriores. Junto a ellos, encontrarás mi intento de desentrañar lo que los datos están mostrando.

Antes de empezar, debo confesar que para poder recopilar los datos sin inexactitudes (como ruido extra), ha habido algunos compromisos. Las grabaciones se obtuvieron utilizando un programa de Windows llamado 'towave', que utiliza **síntesis limitada por banda** para resolver un problema fundamental con la emulación de chips de audio basados en PSG. Básicamente, los pulsos, triángulos y dientes de sierra están formados por **armónicos infinitos**. Sin embargo, eso no se mezcla bien con las tarjetas de sonido modernas que están limitadas a muestras de 44.1 kHz. Entonces, se utiliza una técnica llamada 'síntesis limitada por banda' para elegir los armónicos correctos dentro del límite de la tarjeta de sonido. En resumen, esta técnica proporciona un equilibrio factible entre rendimiento, precisión y prevención de aliasing. Sin embargo, los datos pueden no ser idénticos al 100% a su contraparte analógica (que, por el contrario, también introduciría otros problemas, como el ruido del equipo de grabación), pero creo que es aceptable y, lo más importante, cumple la función para esta sección del artículo.

Dicho esto, seguimos con el análisis.

##### Pulso {.tabs .active}

![Espectrograma del canal Pulse 1.](spectrograms/eb0_pulse_nes.png) {.tab-float}

La teoría dice que un tono de pulso solo contiene armónicos impares. En otras palabras, el fundamental se combina con su tercer armónico, el quinto y así sucesivamente. Además, cada armónico disminuye su amplitud cuanto más lejos está del fundamental. La fórmula de amplitud es `amplitud = 1 ÷ número armónico` [@audio-pulse].

Por lo tanto, observa cómo la luminosidad de cada armónico en el espectrograma se atenúa cuanto más alto está en el eje Y. Sin embargo, las ondas de pulso del APU también parecen exhibir el mencionado efecto vibrato que aumenta con cada número de armónico. Además, las áreas del espectrograma que deberían estar vacías de cualquier sonido contienen tonos apagados (posiblemente el resultado de ruido y otras imperfecciones).

##### Triángulo {.tab}

![Espectrograma del canal Triángulo.](spectrograms/eb0_triangle_nes.png) {.tab-float}

Una onda triangular también está formada por armónicos impares pero con una disminución más rápida de su amplitud (donde `amplitud = 1 ÷ número armónico²` [@audio-triangle]).

Sin embargo, esto no es lo que se muestra aquí, la forma de escalera del triángulo que produce el APU lleva a armónicos adicionales y amplitudes aumentadas.

##### Ruido {.tab}

![Espectrograma del canal Ruido.](spectrograms/eb0_noise_nes.png) {.tab-float}

Naturalmente, el ruido no se ajusta a las reglas de los armónicos y puede llenar aleatoriamente todo el espacio de frecuencia, de ahí la falta de un tono fácilmente reconocible.

Aunque, siguiendo la línea de tiempo, puedes diferenciar los diferentes preajustes de ruido que proporciona el APU, cada uno exhibiendo un conjunto distinto de armónicos.

##### Muestra {.tab}

![Espectrograma del canal de Muestra.](spectrograms/eb0_dcm_nes.png) {.tab-float}

A diferencia de los canales anteriores, el canal de muestra solo reproduce la grabación de baja resolución que el desarrollador alimenta al APU. Considerando que el ejemplo reproducía un kit de batería, no puedo ver ningún rasgo identificable en el espectrograma (aparte de similitudes con el ruido blanco).

##### Diente de sierra {.tab}

![Espectrograma del canal Diente de Sierra del VRC6.](spectrograms/castlevania_saw_nes.png) {.tab-float}

Como un extra, vamos a revisar también el canal de Diente de Sierra de la expansión VRC6. Para empezar, una onda de diente de sierra perfecta está compuesta por todos los armónicos y cada uno exhibe amplitudes decrecientes (donde `amplitud = 1 ÷ número armónico` [@audio-sawtooth]).

Esto es todo un requisito para el equipo digital y, de hecho, es inasumible para un cartucho de juego (que puede que ni siquiera necesite tal perfección). Así que, al igual que las ondas triangulares del APU, el VRC6 secuencia ondas de Diente de Sierra en 7 ciclos (y por lo tanto produce efectos de escalera similares).

En consecuencia, el respectivo espectrograma es muy desordenado, las técnicas de aproximación del VRC6 llenan la onda con armónicos adicionales en muchos lugares.

#### Conclusión {.tabs-close}

Bueno, parece que las formas de onda sintéticas de la NES no se parecen en nada a lo que dicta la teoría. ¿Significa esto que el APU está defectuoso? ¡No! El diseño de la APU terminó otorgando a esta consola sonidos únicos e identificables - y estas propiedades, intencionadas o no, hicieron que los espectrogramas mostraran resultados inusuales.

Como nota al margen, la geometría perfecta puede ser agradable a la vista, pero curiosamente, ¡nuestros oídos no son particularmente aficionados a las formas de onda con bordes perfectos! (puedes empezar a escuchar ruidos de chasquidos).

Mirando hacia adelante, el análisis de sonido utilizando espectrogramas será útil en otros artículos, ya sea para un análisis simple o para hacer comparaciones con otros sistemas. Ten en cuenta que estos gráficos no son, ni mucho menos, _la herramienta definitiva_, especialmente con muestras de sonido que se han mezclado con demasiados canales/instrumentos (dificultando en gran medida su descomposición). Pero creo que proporcionarán un buen comienzo para cualquier tipo de estudio objetivo.

## Juegos

Los juegos de NES están principalmente escritos en lenguaje ensamblador 6502 y residen en el **Program ROM**, mientras los gráficos del juego (tiles) se almacenan en la **Character memory**.

Además, los juegos se vendían (o alquilaban) en tiendas minoristas **con la aprobación de Nintendo**.

### El medio alternativo

Aunque solo se lanzó en Japón, pensé que sería una buena oportunidad para presentar un complemento de corta duración pero peculiar que, al igual que los mapeadores, trajo más capacidades a esta consola. Este periférico se llamaba **Famicom Disk System** (FDS) y se lanzó en 1986 (~3 años después de la Famicom). Tenía la forma de un lector de disquetes externo y venía con un cartucho de forma extraña llamado 'adaptador de RAM'.

::: {.subfigures .side-by-side}

![La unidad, donde se insertan los disquetes (la foto muestra un disquete de cartón insertado para protección). Se alimenta con seis baterías C (1,5 V cada una) o un adaptador de CA de 3,6 W.](fds/drive.png) {.toleft.no-borders}

![El adaptador de RAM, encajado en la ranura del cartucho de la Famicom y conectado a la unidad a través de un cable.](fds/ram.png) {.toright.no-borders}

Los dos componentes que componen el Famicom Disk System (FDS).

:::

El Famicom Disk System añadió los siguientes servicios a la Famicom:

- Un nuevo medio de distribución para juegos llamado **Famicom Disk** [@games-fds]. Basado en el 'Quick Disk' de Mitsumi, proporciona **~64 KB de datos** por lado y es regrabable.
- Un **canal de audio extra** que utiliza [síntesis de tabla de ondas](game-boy#tab-2-2-wave) [@games-fds_audio].

![Equipo FDS montado en la Famicom.](fds/mounted.png) {.open-float.no-borders}

Dado que el disquete es un medio único (a diferencia de los cartuchos de varios chips), todos los datos del juego necesitan ser comprimidos dentro, aunque se mantienen organizados con el uso de un **sistema de archivos** propietario.

No obstante, el Famicom/NES requiere estrictamente que la Program ROM y la Character memory estén segregadas para funcionar, por lo que ese es el trabajo del 'adaptador de RAM'. Este componente alberga **32 KB de RAM de Programa** y **8 KB de RAM de Caracteres** para almacenar en búfer los datos del juego leídos desde el disquete, y al hacerlo, permite que la consola los lea como si se tratara de un juego basado en cartucho.

{.close-float}

Para operar la unidad, el adaptador de RAM incorpora un ROM adicional de 8 KB para almacenar un **BIOS** [@games-fds_bios]. Este programa realiza las siguientes tareas:

- Carga una animación de introducción.
- Inicializa el juego desde el disquete. Detrás de escena, la BIOS contiene rutinas para copiar el contenido del disquete al chip de memoria correspondiente, para que la consola pueda leerlo.
- Proporciona API de E/S para que los juegos las usen, como recorrer el sistema de archivos de los discos.

![Ejemplo de dos juegos de venta minorista para el FDS. La variante azul del disco era adicionalmente a prueba de polvo.](fds/floppies.jpg) {.toleft}

![La animación de presentación del FDS, esperando que el usuario... ehm... inserte un juego.](fds_bios){.toright video="true"}

Durante esa época, Nintendo desplegó algunos 'quioscos' en las tiendas minoristas para que los usuarios pudieran llevar sus disquetes y sobrescribirlos con un nuevo juego a un precio reducido.

Por desgracia, después de unos años de vida útil, el Famicom Disk System fue descontinuado y los juegos subsecuentes regresaron al medio de cartucho. Por el lado positivo, había nuevos mapeadores disponibles con capacidades similares (o mejores) en comparación con las funciones del FDS.

## Antipiratería y protección regional

Nintendo pudo bloquear la publicación no autorizada gracias a la inclusión de un chip de **bloqueo** propietario llamado **Circuito Integrado de Comprobación** (CIC) [@anti_piracy-cic]. Se encuentra en la consola y está conectado a la señal de reinicio (y por lo tanto no se puede quitar fácilmente).

Este chip ejecuta **10NES**, un programa interno que verifica la existencia de otro chip de bloqueo en el cartucho del juego. Si esa verificación falla, entonces la consola se envía a un reinicio infinito.

Ambos chips Lockout están en constante comunicación mientras la consola esté encendida. Este sistema puede ser desbloqueado mediante el corte de uno de los pines en el Lockout de la consola, esto deja el chip en estado inactivo. Otra opción es enviarle una señal de -5V que lo paraliza.

El CIC surgió del temor que despertó la crisis de videojuegos de 1983. En aquel entonces el presidente de Nintendo, Hiroshi Yamauchi, decidió que para mejorar la calidad de los juegos, deberían ser aprobados uno por uno [@anti_piracy-vindicator].

Como notarás, el modelo japonés de la consola, la Famicom, fue lanzado antes de que la crisis de 1983 ocurriera. Por lo tanto, ni los cartuchos de juego ni la consola incluyen la circuitería CIC [@anti_piracy-nocic], en su lugar, los pines se utilizan para la expansión de sonido opcional.

## Eso es todo amigos
