---
name: PC Engine / TurboGrafx-16
short_title: Arquitectura del PC Engine / TurboGrafx-16
long_title: Arquitectura del PC Engine / TurboGrafx-16
subtitle: Un salto pequeño, pero rápido
date: 2020-12-18
release_date: 1987-10-30
cover: "pcengine"
generation: 4
seo_image_pos: "Bottom"
top_tabs:
  Models:
    - title: "Japonés"
      caption: "El PC Engine.<br>Lanzado el 30/10/1987 en Japón."
      file: japanese
      latex_height: 90
    - title: "Internacional"
      caption: "El TurboGrafx-16.<br>Lanzado el 29/08/1989 en América, el 22/11/1989 en Francia y en 1990 en el Reino Unido y España."
      active: true
      file: international
      latex_height: 93
  Motherboard:
    caption: "Mostrando la placa base del TurboGrafx-16.<br>La PC Engine fue considerablemente más pequeña y compacta."
    latex_height: 93
    marked_latex_height: 94
  Diagram:
    caption: "Parece bastante simple, ¿eh?"
    paperback_latex_height: 95

# Historical
aliases:
  - /projects/consoles/pc-engine/
---

## Una introducción rápida

Con gran audacia, Hudson y NEC unieron fuerzas para dar el pistoletazo de salida a la 4ª generación de consolas. Por desgracia, sus esfuerzos fueron poco a poco eclipsados por la llegada de la competencia. No obstante, su consola sigue siendo uno de los diseños más compactos de aquella época.

## Modelos y variantes

Al igual que la [Master System](master-system#models-and-variants), NEC lanzó un montón de revisiones y variaciones peculiares que pueden ser un poco difíciles de seguir al principio. Así que, para futura referencia, aquí están los modelos más importantes:

- **PC Engine**: La primera consola con esta arquitectura, aunque sólo salió a la venta en Japón. Parece una caja blanca pequeña con los puertos esenciales.
- **TurboGrafx-16**: Rediseño de la PC Engine para el mercado estadounidense. Posee las mismas características, pero con un aspecto exterior distinto. Me recuerda a la [adaptación de NES/Famicom](nes#models-and-variants).
- **SuperGrafx**: Versión mejorada de la PC Engine con una carcasa completamente diferente y un hardware renovado. Sus características exclusivas quedan fuera del alcance de este artículo.
- **PC Engine Duo/TurboDuo**: Una combinación del PC Engine/TurboGrafx y la expansión CD-ROM² de posventa.

Este artículo se enfocará en la PC Engine/TurboGrafx-16, pero también abordará las expansiones que llevaron al lanzamiento de la PC Engine Duo/TurboDuo.

## {.supporting-imagery}

## CPU

Dentro de esta consola encontramos el **HuC6280**, un chip fabricado por **Hudson Soft** que alberga dos componentes. Uno de ellos es la CPU, que puede operar a dos velocidades: **~1.79 MHz** y **~7.16 MHz**.

![El chip HuC6280 en la placa base del PC Engine.](cpu.webp){latex_width="90%"}

El HuC6280 no es un componente de catálogo, como el [Z80](master-system#cpu), sino una CPU propia diseñada por NEC. Su especificación parece replicar en gran medida el comportamiento de los famosos **MOS 6502** y **WDC 65C02**. Dicho esto, ¿qué significa todo esto para el programador?

Antes de empezar, ya he cubierto el procesador MOS 6502 [en el artículo de la NES](nes#cpu) y también he descrito una variante de 16 bits realizada por WDC y Ricoh [en la SNES](super-nintendo#cpu), en caso de que quieras echarles un vistazo primero.

El 65C02 es otra versión modificada del MOS 6502 realizada por Western Design Center, mucho más eficiente gracias a su enfoque de fabricación CMOS. Desde el lado del software, el 65C02 añade un par de nuevas instrucciones y modifica el comportamiento de algunas existentes [@cpu-opcodes].

Podríamos hablar mucho más sobre el 65C02, pero creo que es mejor centrarse en las características añadidas por Hudson (que complementan al 65C02), porque son críticas para operar eficientemente esta consola. En comparación con el 65C02, el HuC6280 incorpora [@cpu-hardware]:

- **Más opcodes**, algunos dirigidos a los componentes que envuelven la CPU.
- Un **puerto de E/S de 8 bits** utilizado para interconectar componentes externos.
- Un **controlador de interrupciones** para recibir interrupciones de otros dispositivos.
- Un **temporizador**. Funciona mediante una cuenta atrás a partir de un valor especificado y, cuando termina, envía una interrupción a la CPU.

Finalmente, hay **8 KB de RAM** disponibles para fines generales.

### Acceso a memoria

Un aspecto que no he mencionado todavía es que NEC también añadió una **Unidad de Gestión de Memoria** (MMU) junto a la CPU, permitiéndole manejar **direcciones de 21 bits** (recuerda que el 6502 original sólo tiene direcciones de 16 bits) [@cpu-hardware_manual]. Por lo tanto, la cantidad de memoria a la que se puede acceder pasa de 64 KB a **2 MB**.

Esta MMU es muy diferente de cualquier MMU actual; yo diría que está más cerca de un [mapeador](nes#going-beyond-existing-capabilities). Dicho esto, la MMU del PC Engine se compone de **ocho registros de 8 bits** (denominados **Mapping Register** o 'MPR') que se combinan con las 16 líneas de dirección de la CPU para formar un bus de direcciones de 21 bits.

![Enfoque de direccionamiento de la MMU.](_diagrams/mmu.png)

Esto funciona de la siguiente manera:

1. La CPU puede leer y/o escribir en cualquier MPR utilizando las instrucciones especiales `TAM` y `TMA`, respectivamente.
2. Cuando se accede a la memoria física, la MMU reserva las líneas de dirección A13-A15 de la CPU para seleccionar uno de los ocho MPR.
3. La dirección final se obtiene combinando las líneas de la CPU (situadas en A0-A12) con las líneas de la MMU (situadas en A13-A20, donde la salida es el valor de 8 bits del MPR seleccionado), dando como resultado una dirección de 21 bits.

Por ello, esta MMU agrupa la memoria física en páginas de 8 KB (13 líneas de CPU = páginas de 8 KB), que es la cantidad de memoria a la que puede acceder la CPU sin intercambiar el MPR o alterar su valor.

De todos modos, no te agobies si te parece demasiado complejo (es sólo un modus operandi poco convencional que puede resultarle interesante a más de uno).

## Gráficos

Los gráficos los gestiona el **Hudson Soft HuC6270**, un chip independiente también denominado **Video Display Controller** o 'VDC'. El HuC6270 dibuja todo lo que el jugador ve en la pantalla y su funcionalidad es muy similar a [la contraparte de Sega](master-system#graphics). En este artículo me centraré en los aspectos diferenciales de la propuesta de Hudson.

### Organizando el contenido

Lo primero es lo primero: el VDC es un **tile engine** (más o menos el estándar hasta que apareció la 5ª generación), pero cabe señalar que el PC Engine incluye **64 KB de VRAM**, una cantidad considerable comparada con la competencia. Esto puede dar lugar a nuevos tipos de contenido, que exploraremos más adelante.

![Arquitectura de memoria del VDC.](_diagrams/vdc.png)

La forma en que se organizan los datos gráficos puede resultar un poco confusa: tanto la CPU como el VDC utilizan direcciones de 16 bits, pero mientras que la CPU solo puede manejar palabras de 8 bits, el HuC6270 almacena palabras de 16 bits en la VRAM [@graphics-chibi]. Esto significa que una sola dirección en la RAM contiene un byte, mientras que una dirección en la VRAM almacena dos bytes, así que los desarrolladores tenían que tener en cuenta esta discrepancia al transferir datos a la VRAM.

La razón de esto se reduce a cómo Hudson organizó la circuitería: el VDC tiene un bus de dirección de 16 bits, pero **solo se controlan las primeras 15 líneas** (la última siempre está configurada en '0'), por lo que las direcciones impares se obtienen del segundo byte. No sé por qué Hudson eligió este camino, pero sí sé que todo esto tendría más sentido si el sistema tuviera en su lugar 128 KB de VRAM (ya que un bus de dirección de 16 bits solo puede acceder hasta 64 KB, así que al duplicar el bus de datos de alguna manera, se pueden recuperar hasta 128 KB). ¿Quizás ese era el plan original de Hudson/NEC?

### Construyendo el frame

Además de la peculiaridad de granularidad mencionada anteriormente, el VDC es, funcionalmente, muy sencillo. El subsistema tiene tres componentes principales: el **VDC** y la **VRAM**, que ya he mencionado, y el **Video Colour Encoder** (también llamado 'VCE'; hablaremos más sobre esto a su debido tiempo).

El sistema es compatible con múltiples resoluciones, ya que el juego puede alterar un conjunto de registros que actúan como parámetros para controlar los tiempos de visualización, que a su vez modifican los tiempos de barrido del CRT. La resolución mínima es de **256 × 224 píxeles** [@graphics-vdc_manual], mientras que algunos proyectos homebrew han demostrado que este sistema puede alcanzar hasta **512 × 240 píxeles**.

Ahora veamos cómo se dibuja un fotograma paso a paso; para ello, tomaré prestados los assets de *Bonk's Adventure*.

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel latex_subfigure_width="0.46"}

![Tiles de fondo en la VRAM.](graphics/tiles_background.png){.active .desktop-margined .border title="Background"}

![Tiles de sprite en la VRAM.](graphics/tiles_sprites.png){.border title="Sprites"}

Los dos tipos de tiles en la VRAM.

:::

Como breve recordatorio, los tiles son solo **bitmaps de 8x8 píxeles** que el renderizador obtiene para dibujar porciones de la pantalla. Con el VDC, el fotograma se compone de dos planos: la capa de fondo y la capa de sprites.

Dentro de la VRAM hay un área llamada **Character Generator** donde se definen los tiles exclusivos para la capa de fondo. Cada píxel de un tile ocupa cuatro bits, por lo que puede usar hasta 16 colores. En teoría, se pueden definir hasta 4096 tiles de fondo, pero esto se reduce en la práctica debido a que la VRAM es mucho más pequeña.

Los sprites, por otro lado, se dibujan usando tiles desde una ubicación de memoria separada en la VRAM, denominada **Sprite Generator**, que difiere del anterior Character Generator ya que los tiles aquí son de **16x16 píxeles**.

El codificador de vídeo es un chip separado que almacena **32 paletas de colores** (16 para el fondo y 16 para los sprites) [@graphics-encoder_manual]; cada paleta almacena 16 colores y cada color tiene una amplitud de 9 bits (3 bits para Rojo + 3 bits para Verde + 3 bits para Azul).

#### Almacenando tiles {.tab}

::: {.subfigures .tabs-nested .tab-float latex_subfigure_width="0.46"}

![Estructura de un tile de fondo.](_diagrams/tiles.png){.active title="Background"}

![Estructura de un tile de sprite.](_diagrams/tiles_sprites.png){title="Sprites"}

Cómo se estructuran los tiles en la VRAM.

:::

(Esta sección está escrita para aquellos interesados en cómo Hudson aprovechó los 64 KB de VRAM con esa granularidad de 16 bits, pero no es necesario entenderla completamente para seguir el resto del artículo).

Hasta ahora hemos discutido que cada píxel de un tile se almacena usando 4 bits (o medio byte, también llamado **nibble**). Ahora bien, Hudson dicta que **los tiles están compuestos por cuatro bitmaps de 8x8** (llamados 'CH0', 'CH1', 'CH2' y 'CH3', respectivamente). Cada mapa tiene 1 bit de ancho, pero una vez que se combinan los cuatro, forman el tile final con píxeles de 4 bits.

Debido a la alineación de 16 bits, **cada palabra de 16 bits almacena una sola fila de dos bitmaps de 1 bit** (8 filas + 8 filas). Así que, después de escribir ocho entradas, se almacenarán dos mapas (en lugar de solo uno). Echa un vistazo a los diagramas para entenderlo mejor.

Lo mismo ocurre con los tiles de sprite, pero dado que son bitmaps de 16x16, cada bitmap ocupa 16 palabras. Dicho de otra manera, para almacenar un solo tile de sprite se necesitan 64 palabras (equivalente a 8 bytes en VRAM).

#### Capa de fondo {.tab}

![Capa de fondo renderizada.](graphics/background.png){.tab-float .pixel .latex-framed latex_width="80%"}

La capa de fondo se construye llenando la **Background Attribute Table** con entradas en la VRAM; la posición de cada entrada define las coordenadas X/Y del tile en la pantalla. Cada entrada contiene el índice del tile del Character Generator y la paleta de colores.

La dimensión máxima de esta capa es de **1024 x 512 píxeles** (128 x 64 tiles), pero los programadores pueden configurar una capa de 256 x 256 píxeles (32 x 32 tiles) como mínimo.

Como siempre, esta capa es desplazable cambiando el valor de algunos registros en el VDC.

#### Capa de sprites {.tab}

![Capa de sprites renderizada.](graphics/sprites.png){.tab-float .pixel .latex-framed latex_width="80%"}

El VDC contiene una memoria interna llamada **Sprite Attribute Table Buffer** donde se pueden definir hasta 64 sprites. Cada entrada de la tabla almacena la posición X/Y independiente, la paleta de colores, el índice de tile y la inversión H/V. Además, hay un atributo que permite combinar un sprite con otro.

Cada entrada tiene una longitud de 8 bytes, aunque se desperdicia algo de espacio debido a la granularidad de 16 bits.

Para colmo, **la CPU no puede acceder a esta tabla**, por lo que debe completarse en VRAM primero y luego transferirse al VDC mediante un canal DMA.

En cuanto a las limitaciones, solo puede haber hasta 16 sprites por línea de barrido. Por otro lado, se pueden configurar interrupciones para notificar al juego en caso de desbordamiento de sprites o [colisión](master-system#tab-2-1-collision-detection).

#### Resultado {.tab}

![¡Tachán!](graphics/complete.png){.tab-float .pixel .latex-framed latex_width="80%"}

Hasta ahora hemos visto cómo el VDC realiza la mayor parte del trabajo pesado, pero la tarea final es en realidad delegada al **Video Colour Encoder** o 'VCE'. Este chip, identificado en la placa base como el **HuC6260**, tiene como función principal recibir flujos de datos de 9 bits del VDC, aplicar las paletas de colores y enviar el resultado a la televisión en forma de señal analógica.

Si has leído artículos anteriores, quizá estés familiarizado con la importancia del timing. Este caso no es una excepción: para evitar artefactos no deseados (como la 'nieve'), el VCE solo puede actualizarse durante una interrupción vertical.

### Salida de vídeo {.tabs-close}

El codificador de vídeo proporciona salidas RGB (junto con Sync) y YPbPr, lo cual es ideal para su uso con un cable SCART o un cable de componentes, respectivamente. Esto tiene buena pinta...

![Puerto RF en el lado derecho de la PC Engine.](rfport.jpg){.tabs-nested .active .open-float .tab-float title="RF"}

![El 'Turbo Booster' para el TurboGrafx-16 [@photography-amos], conectado al puerto de expansión para suministrar vídeo compuesto y energía.](turbo_booster.png){.tabs-nested-last title="Accesorio"}

... Por desgracia, Hudson decidió instalar un modulador de RF como única forma de obtener vídeo de serie, así que al fin y al cabo no es tan bueno. Ahora bien, la PC Engine fue diseñada en los años 80, así que este enfoque garantizaba la compatibilidad con la mayoría de los televisores de la misma región.

Por el lado bueno, el puerto de expansión contiene pines que llevan vídeo RGB y múltiples tipos de sincronización, pero se necesita un accesorio externo para poder aprovecharlos.

{.close-float}

## Audio

La PC Engine contiene un [Generador de Sonido Programable](master-system#audio) (PSG, del inglés 'Programmable Sound Generator'), al igual que muchas otras consolas de tercera generación analizadas en este sitio web. Por otro lado, este PSG en particular depende de **memoria de forma de onda** (también llamada 'buffer de forma de onda') para sintetizar sonido, en lugar de usar un conjunto predefinido de formas de onda (pulso, triángulo, etc.). La memoria de forma de onda permite a los programadores definir sus propias formas de onda, lo que brinda mayor flexibilidad para los arreglos de la banda sonora.

![Visualización del osciloscopio durante una partida de R-Type (1987).](rtype){.open-float video="true" latex_width="80%" .negate .border}

Este sistema cuenta con **seis canales de audio** [@audio-manual], cada uno se configura escribiendo en un conjunto de registros. Estos últimos almacenan los atributos de los canales, que incluyen:

- **Forma de onda**: El ciclo de la forma de onda se genera utilizando treinta y dos valores de 5 bits, cada uno corresponde a la amplitud de la onda en un momento particular.
  - Algunos juegos como *Fire Pro Wrestling 2* y *Bloody Wolf* incluso alteraron los valores durante la reproducción para obtener sonidos especiales (a expensas de clics audibles durante las transferencias).
- **Control de frecuencia**: Dos registros de 8 bits alteran la frecuencia del canal para producir diferentes notas musicales con la misma forma de onda.
- **Nivel de amplitud**: Un solo registro de 8 bits almacena dos valores de 4 bits que determinan el volumen del canal. Los dos valores corresponden al **control de panoramización** 'Izquierda' y 'Derecha', una característica pionera en comparación con lo que ofrecía la competencia.

{.close-float}

### Funciones extra

Algunos grupos de canales tienen otros modos de operación. Por ejemplo, los dos últimos canales están conectados a un **generador de ruido**. Además, el segundo canal puede actuar como un **oscilador de baja frecuencia** (LFO, del inglés 'Low-Frequency Oscillator') para modular el primer canal (inicialmente asumí que esto era un sinónimo de [síntesis FM](mega-drive-genesis#audio), pero los LFO son más bien un 'subconjunto' que logra un **efecto de vibrato**).

Hablando de modos, hay otro disponible llamado **Direct D/A (DDA)** que permite que la CPU escriba directamente sobre el búfer de audio (eludiendo el PSG). En consecuencia, la PC Engine puede reproducir **muestras PCM**. Estas todavía son de 5 bits y dependen estrechamente de los ciclos de la CPU. Aunque, ¡dos canales pueden combinarse para reproducir muestras de 10 bits! [@audio-demo].

En resumen, esto hace que el PSG de la PC Engine sea una pieza de hardware increíblemente flexible.

### Sonido final

El PSG mezcla todos los canales y emite una señal estéreo. Pero de nuevo, a menos que encuentres una forma de eludir el puerto RF, solo escucharás mono desde tu televisor.

## E/S

Esta consola contó con una enorme variedad de accesorios, algunos de los cuales transformaron por completo sus capacidades internas (proporcionando más RAM, por ejemplo) y su funcionalidad externa (añadiendo más puertos o soporte para otros medios de almacenamiento).

![Puerto de expansión en la parte trasera de la PC Engine.](expansion.jpg){.open-float}

Todo ello se debe en gran medida a la inclusión del **Puerto de Expansión** en la parte trasera de la consola, que daba acceso a los siguientes componentes [@io-exp]:

- **Línea de +5 voltios**: Para alimentar el accesorio.
- **Una línea de entrada de voltaje**: Para eludir la fuente de alimentación original y alimentar la consola a través del accesorio.
- **Entrada y salida de audio**: Permite recibir la salida de audio estéreo y/o enviar audio desde una línea de entrada.
- **Líneas de datos y dirección de la CPU**: Permite que la CPU acceda al accesorio y viceversa.
- **Líneas de datos del VDC** (aquellas que van al codificador de vídeo): Permite que el accesorio acceda a esas líneas, aunque no estoy seguro para qué.
- **Líneas de control de la CPU**: Exponen información diversa, como el flag de detección de HuCard y CD, el modo de velocidad utilizado y varias líneas de interrupción.
- **Salida de vídeo analógico**: Incluyendo RGB + Sincronización Vertical y Sincronización Horizontal + Sincronización Compuesta.

{.close-float}

Tengo curiosidad por saber hasta qué punto NEC o Hudson visualizaron esta consola: ¿estaban tratando de construir una especie de 'consola modular' que pudiera actualizarse para hacer una transición sin problemas a la siguiente generación?

### El resto

Todavía no he mencionado el mando (o Joypad, como lo llaman en algunos lugares). Es muy similar al resto. La consola sólo dispone de un puerto para el mando y los juegos comprueban una única dirección en la memoria para obtener las pulsaciones de las teclas. Esa dirección devuelve un valor de 4 bits [@io-data].

## Sistema operativo

No hay ROM interna, 'BIOS' ni ningún otro software que se ejecute antes del juego, por lo que la consola carece de Sistema Operativo. El vector de reset se encuentra en las direcciones `$FFFE` y `$FFFF` (recuerda que los registros internos son de 8 bits pero las direcciones de memoria son de 16 bits, por lo que se necesitan dos palabras para inicializar el contador de programa), y esas direcciones apuntan a la tarjeta del juego [@operating_system-sheldows].

Esto también significa que depende del programador encargarse de las tareas de 'mantenimiento' (es decir, inicializar la memoria, configurar la MMU, etc.).

## Juegos

Los programas están escritos en ensamblador 6502, aderezados con opcodes 65C02 adicionales y los introducidos por Hudson.

### Formato estándar

En lugar de depender de esos cartuchos *gruesos y aburridos* que a todos les parece gustar, NEC y Hudson idearon un nuevo formato, esta vez del tamaño de una tarjeta de crédito, llamado **HuCard**. Este derivaba de un formato más antiguo llamado 'Bee Card', utilizado por algunos juegos de MSX.

![Típico juego de tiendas.](hucard.jpg){.open-float .pico latex_width="50%"}

Curiosamente, son muy similares a la [Sega Card](master-system#medium), pero con 38 pines en lugar de 35 [@games-pinout]. Internamente, las diferencias son más significativas:

- El mapa de memoria de la CPU puede acceder hasta **1 MB de memoria HuCard sin un mapeador**, por lo que la mayoría de los juegos se limitaron a ROMs de 1 MB. Aun así, ¡juegos como 'Street Fighter II' residían en una ROM de 2,5 MB! [@games-secrets]
- Hay un pin de **Audio In** que parece permitir que la tarjeta proporcione un canal de audio extra.

{.close-float}

### Expansión de CD-ROM

Ese puerto de expansión abrió la puerta a una gran cantidad de accesorios y unidades de expansión que acabaron convirtiendo una simple PC Engine en algo completamente diferente. Como si eso no fuera suficiente, la ranura HuCard también complementó las posibilidades de expansión. En este artículo me centraré en las actualizaciones más destacadas, algunas de las cuales se incorporaron en posteriores revisiones de la consola.

![Lector de CD-ROM² (versión TurboGrafx-16) [@photography-amos]. ¿Estaban en el negocio del Discman en ese entonces?](cd/reader.png){.tabs-nested .active .open-float .tab-float latex_width="90%" title="Lector"}

![Base necesaria para conectar la consola con el lector [@photography-amos]. También suministra energía y salida de vídeo compuesto.](cd/compartment.png){.tab-nested title="Compartimento"}

![Cómo se veía con todo encajado [@photography-amos].](cd/fitted.png){.tabs-nested-last title="Encajado"}

Echemos un vistazo a la expansión **CD-ROM²**, que consiste en un lector de CD-ROM y una HuCard especial llamada **System Card**. Esta última actúa como una **BIOS** para inicializar el juego y proporcionar rutinas para hacer de interfaz con el lector. Internamente, el lector incluía **64 KB de RAM** para uso general, otros **64 KB de RAM** para transmitir muestras ADPCM y, finalmente, **2 KB** para guardar datos. Como puedes imaginar, esto permitió a los desarrolladores aprovechar el almacenamiento adicional y el audio en CD, mientras ofrecía a los editores la ventaja económica de distribuir sus juegos en un medio popular.

{.close-float}

Años después, NEC lanzó otro módulo de CD llamado **Super CD-ROM²**. Si bien el hardware del lector permaneció mayormente igual, su RAM de uso general aumentó a 256 KB. Los clientes que ya poseían una unidad CD-ROM² pudieron 'actualizarla' comprando la **Super System Card**, que incluía la RAM adicional (¡y una rutina de E/S más!). Por si eso fuera poco, NEC envió posteriormente actualizaciones de BIOS en forma de la **Arcade Card**, que en este caso albergaba **2 MB de RAM**.

Los juegos basados en CD dependían estrictamente de la tarjeta BIOS para la que fueron desarrollados, aunque las tarjetas más nuevas eran retrocompatibles (con algunas excepciones). Por lo tanto, la Arcade Card era la opción preferida para los usuarios que querían jugar a *casi* todos los juegos basados en CD-ROM. He dicho 'casi' porque las compañías de terceros también lanzaron sus propias tarjetas BIOS (por ejemplo, 'Games Express CD Card'), necesarias para jugar a títulos específicos de esos estudios.

Con el Super CD-ROM², NEC y Hudson también lanzaron una nueva variante de la PC-Engine/TurboGrafx-16 llamada **Turbo Duo**, que agrupaba la consola, el lector y la tarjeta BIOS en un solo dispositivo.

### Otras expansiones

Si tienes curiosidad por conocer otras expansiones, consulta el catálogo en línea [@games-accessories].

## Anti-piratería y homebrew

Las HuCards están bloqueadas por región, lo que significa que un juego de PC-Engine no funcionará en un TurboGrafx-16 (o viceversa) sin modificación alguna. Esto se debe a que el orden de las líneas de datos está invertido en la versión americana. Como puedes imaginar, esto no es algo que un adaptador barato no pueda resolver. Sin embargo, los juegos pueden incluir rutinas para verificar la región del sistema.

Por otro lado, los juegos de CD-ROM no están bloqueados por región ni protegidos contra copias, aunque siguen necesitando una System Card para inicializarse, y dicha tarjeta sí está bloqueada por región.

## Eso es todo amigos

![Mi PC Engine desde un cable RF (_por motivos nostálgicos... eso creo_). ¡Un gran agradecimiento a Matt por donarla!](mypc.jpg)

¡Has llegado al último artículo del año!

2020 ha sido un año 'interesante' para el sitio web (dejando de lado los numerosos retos que nos han afectado a todos). El año comenzó con el [artículo sobre la Wii](wii) y luego continuó con el [PS2](playstation-2), [Xbox](xbox), [Nintendo DS](nintendo-ds), [Master System](master-system) y, finalmente, este.

El número de visitantes también ha crecido considerablemente. Tuvo un comienzo lento al principio, pero durante el verano experimentó un aumento sorprendente de visitas. Quiero agradecer a los usuarios de Hacker News, OSNews, Reddit, Gigazine, YouTubers, usuarios de Twitter y muchos otros foros y personas por compartir los artículos. Y por supuesto, ¡un gran saludo a los donantes cuya generosidad ha ayudado a mantener el sitio a flote!

¡Que tengas una feliz Navidad, feliz Año Nuevo 2021 y hasta la próxima!  
Rodrigo
