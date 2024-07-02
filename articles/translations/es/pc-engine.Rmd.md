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
    - 
      title: "Japonés"
      caption: "El PC Engine.<br>Lanzado el 30/10/1987 en Japón."
      file: japanese
    - 
      title: "Internacional"
      caption: "El TurboGrafx-16.<br>Lanzado el 29/08/1989 en América, el 22/11/1989 en Francia y en 1990 en el Reino Unido y España."
      active: true
      file: international
  Motherboard:
    caption: "Mostrando la placa base del TurboGrafx-16.<br>La PC Engine fue considerablemente más pequeña y compacta."
  Diagram:
    caption: "Parece bastante simple, ¿eh?"
#Historical
aliases:
  - /projects/consoles/pc-engine/
---

## Una introducción rápida

Hudson y NEC unieron fuerzas para dar el pistoletazo de salida a la 4ª generación de consolas. Por desgracia, sus esfuerzos acabarán eclipsándose cuando llegue la competencia. No obstante, su consola se mantendrá en lo más alto como uno de los diseños más compactos del mercado.

## Modelos y variantes

Al igual que la Master System, NEC lanzó un montón de revisiones y variaciones peculiares que pueden ser un poco problemáticas de seguir al principio. Así que, para futura referencia, aquí están los modelos más importantes:

- **PC Engine**: La primera consola con esta arquitectura, aunque sólo salió a la venta en Japón. Parece una caja blanca pequeña con los puertos esenciales.
- **TurboGrafx-16**: Rediseño de la PC Engine para el mercado estadounidense. Posee las mismas características, pero con un aspecto exterior distinto. Me recuerda a la [adaptación de NES/Famicom](nes).
- **SuperGrafx**: Versión mejorada de la PC Engine con una carcasa completamente diferente y un nuevo hardware. Sus características exclusivas quedan fuera del alcance de este artículo.
- **PC Engine Duo/TurboDuo**: Una combinación del PC Engine/TurboGrafx y la expansión CD-ROM² de posventa.

Este artículo se enfocará en la PC Engine/TurboGrafx-16, pero también abordará las expansiones que llevaron al lanzamiento de la PC Engine Duo/TurboDuo.

## {.supporting-imagery}

## CPU

Dentro de esta consola encontramos el **HuC6280**, un chip fabricado por **Hudson Soft** que alberga dos componentes. Uno de ellos es la CPU, que puede operar a dos velocidades: **~1.79 MHz** y **~7.16 MHz**.

El HuC6280 no es un componente disponible a la venta, como el [Z80](master-system#cpu), sino una CPU propia diseñada por NEC. Después de revisar los documentos oficiales, parece replicar mucho el comportamiento de los famosos **MOS 6502** y **WDC 65C02**. Dicho esto, ¿qué significa todo esto para el programador?

Antes de empezar, ya hemos cubierto algunas partes del MOS 6502 [en el artículo de la NES](nes#cpu) y también hemos descrito una variante de 16 bits realizada por WDC y Ricoh [en la SNES](super-nintendo#cpu), en caso de que quieras echarles un vistazo primero.

El 65C02 es otra versión modificada del MOS 6502 realizado por Western Design Center, que es mucho más eficiente por su enfoque de fabricación (CMOS). Desde el lado del software, el 65C02 añade un par de nuevas instrucciones y modifica el comportamiento de algunas existentes [@cpu-opcodes].

Podríamos hablar mucho más sobre el 65C02, pero creo que es mejor centrarse en las características añadidas por Hudson (complementando el 65C02) porque son críticas para operar eficientemente esta consola. En comparación con el 65C02, el HuC6280 tiene [@cpu-hardware]:

- **Más opcodes**, algunos dirigidos a los componentes que envuelven la CPU.
- Un **puerto de I/O de 8 bits** utilizado para interconectar componentes externos.
- Un **controlador de interrupciones** para recibir interrupciones de otros dispositivos.
- Un **temporizador**. Funciona mediante una cuenta atrás a partir de un valor especificado y, cuando termina, envía una interrupción a la CPU.

Finalmente, hay **8 KB de RAM** disponibles para fines generales.

### Acceso a memoria

Una cosa que no he mencionado *todavía* es que NEC también añadió una **Unidad de Gestión de Memoria** o 'MMU' junto a la CPU, permitiéndole manejar **direcciones de 21 bits** (recuerdad que el 6502 original sólo tiene direcciones de 16 bits) [@cpu-hardware_manual]. Por lo tanto, la cantidad de memoria a la que se puede acceder pasa de 64 KB a **2 MB**.

Esta MMU es muy diferente de cualquier MMU actual, yo diría que la primera está más cerca de un [mapper](nes#going-beyond-existing-capabilities). Dicho esto, la MMU que se encuentra en el PC Engine se compone de **ocho registros de 8 bits** (denominados **Mapping Register** o 'MPR') que se combinan con las 16 líneas de dirección de la CPU para formar un bus de direcciones de 21 bits.

![Enfoque de direccionamiento de la MMU.](mmu.png)

Esto funciona de la siguiente manera:

1. La CPU puede leer y/o escribir en cualquier MPR utilizando las instrucciones especiales `TAM` y `TMA`, respectivamente.
2. Cuando se accede a la memoria física, la MMU reserva las líneas de dirección A13-A15 de la CPU para seleccionar uno de los ocho MPR.
3. La dirección final se obtiene combinando las líneas de la CPU (situadas en A0-A12) con las líneas de la MMU (situadas en A13-A20, donde la salida es el valor de 8 bits del MPR seleccionado), dando como resultado una dirección de 21 bits.

Por ello, esta MMU agrupa la memoria física en páginas de 8 KB (13 líneas de CPU = páginas de 8 KB), que es la cantidad de memoria a la que puede acceder la CPU sin intercambiar el MPR o alterar su valor.

De todos modos, que nadie se sienta desbordado si le cuesta trabajo entenderlo (es sólo un modus operandi poco convencional que a algunos les puede resultar interesante).

## Gráficos

El encargado de esto es el **Hudson Soft HuC6270**, un chip aparte también denominado **Controlador de pantalla de vídeo** o VDC. El HuC6270 dibujará todo lo que el jugador verá en la pantalla y su funcionalidad es muy similar a [la contraparte de Sega](master-system#graphics), así que por favor echad un vistazo a ese artículo de antemano ya que me centraré en lo que hay de diferente con la oferta de Hudson.

### Organizando el contenido

Lo primero es lo primero, el VDC es un **tile engine** (más o menos el estándar hasta que apareció la 5ª generación) pero nótese cómo el PC Engine incluye **64 KB de VRAM** que es una cantidad considerable comparada con la competencia. Esto puede dar lugar a un nuevo tipo de contenido, que comprobaremos más adelante.

![Arquitectura de memoria de la VDP.](graphics/vdc.png)

La forma en que se organiza los datos gráficos puede resultar un poco confusa: tanto la CPU como el VDC utilizan direcciones de 16 bits, pero mientras que la CPU solo puede manejar palabras de 8 bits, el HuC6270 almacena palabras de 16 bits en la VRAM [@graphics-chibi]. Esto significa que una sola dirección en la RAM contiene un byte, mientras que una dirección en la VRAM almacena dos bytes, así que los desarrolladores tenían que tener en cuenta esta discrepancia al transferir datos a la VRAM.

La razón de esto se reduce a cómo Hudson organizó la circuitería: el VDC tiene un bus de dirección de 16 bits, pero **solo se controlan las primeras 15 líneas** (la última siempre está configurada en '0'), por lo que las direcciones impares se obtienen del segundo byte. No sé por qué Hudson eligió este camino, pero sí sé que todo esto tendría más sentido si el sistema tuviera en su lugar 128 KB de VRAM (ya que un bus de dirección de 16 bits solo puede acceder hasta 64 KB, así que al duplicar de alguna manera el bus de datos, se pueden recuperar hasta 128 KB). ¿Quizás ese era el plan original de Hudson/NEC?

### Construyendo el frame

Además de la peculiaridad de granularidad mencionada anteriormente, en términos de funcionalidad, el VDP es muy sencillo. El subsistema tiene tres componentes principales: El **VDC** y la **VRAM**, que ya he mencionado - y el **codificador de video** (también llamado 'VEC', hablaremos más sobre esto a su debido tiempo).

El sistema puede utilizar múltiples resoluciones, esto se debe a que el juego puede alterar un conjunto de registros que actúan como parámetros para controlar los tiempos de visualización, que a su vez modifican cuándo el CRT comienza a proyectar el fotograma (en contraposición al overscan). La resolución mínima es de **256 × 224 píxeles** [@graphics-vdc_manual], mientras que algunos proyectos homebrew han demostrado que este sistema puede alcanzar hasta **512 × 240 píxeles**.

Ahora veamos cómo se dibuja un fotograma paso a paso; para ello, tomaré prestados los assets de *Bonk's Adventure*.

#### Tiles {.tabs.active}

![Tiles de fondo encontrados en VRAM.](graphics/tiles_background.png){.tabs-nested .active .tab-float .pixel .desktop-margined title="Background"}

![Tiles de sprite encontrados en VRAM.](graphics/tiles_sprites.png){.tabs-nested-last .pixel title="Sprites"}

Como breve recordatorio, los tiles son solo **bitmaps de 8x8 píxeles** que el renderizador obtiene para dibujar porciones de la pantalla. Con el VDC, el fotograma se compone de dos planos: la capa de fondo y la capa de sprites.

Dentro de la VRAM hay un área llamada **Generador de Caracteres** donde se definen los tiles exclusivos para la capa de fondo. Cada píxel de un tile ocupa cuatro bits, por lo que puede usar hasta 16 colores. En teoría, se pueden definir hasta 4096 tiles de fondo, pero esto es menor en la práctica debido a que la VRAM es mucho más pequeña.

Los sprites, por otro lado, se dibujan usando tiles desde una ubicación de memoria separada en la VRAM, esto se llama **Generador de Sprites** y difiere del anterior Generador de Caracteres ya que los tiles aquí son de **16x16 píxeles**.

El codificador de video es un chip separado que almacena **32 paletas de colores** (16 para el fondo y 16 para los sprites) [@graphics-encoder_manual], cada paleta almacena 16 colores y cada color tiene una amplitud de 9 bits (3 bits para Rojo + 3 bits para Verde + 3 bits para Azul).

#### Almazenado tiles {.tab}

![Cómo está estructurado un solo tile de fondo.](graphics/storagetiles.png){.tabs-nested .active .tab-float title="Background"}

![Cómo está estructurado un solo tile de sprite.](graphics/storagetiles_sprites.png){.tabs-nested-last title="Sprites"}

(Esta sección está escrita para aquellos interesados en cómo Hudson aprovechó los 64 KB de VRAM con esa granularidad de 16 bits, pero no es necesario entenderla completamente para seguir el resto del artículo).

Hasta ahora hemos discutido que cada píxel de un tile se almacena usando 4 bits (o medio byte, también llamado **nibble**). Ahora, Hudson dicta que **los tiles están compuestos por cuatro bitmaps de 8x8** (llamados 'CH0', 'CH1', 'CH2' y 'CH3', respectivamente). Cada mapa tiene 1-bit de ancho, pero una vez que se combinan los cuatro, forman el tile final con píxeles de 4 bits.

Además, ¿recuerdas la alineación de 16 bits en la VRAM? **Cada palabra de 16 bits almacena una sola fila de dos bitmaps de 1-bit** (8 filas + 8 filas). Así que, después de escribir ocho entradas, se almacenarán dos mapas (en lugar de solo uno). Por favor, echa un vistazo a los diagramas para entender mejor esto.

Lo mismo ocurre con los tiles de Sprite, pero dado que son bitmaps de 16x16, cada bitmap ocupa 16 palabras. Dicho de otra manera, para almacenar un solo tile de sprite, se toman 64 palabras (equivalente a 8 bytes en VRAM).

#### Capa de Fondo {.tab}

![Capa de fondo renderizado.](graphics/background.png) {.tab-float.pixel}

La capa de fondo se construye llenando la **Tabla de Atributos de Fondo** (Background) con entradas en la VRAM; la posición de cada entrada define la coordenada X/Y del tile en la pantalla. Cada entrada contiene el índice del tile del Generador de Caracteres y la paleta de colores.

La dimensión máxima de esta capa es de **1024 x 512 píxeles** (128 x 64 tiles), pero los programadores pueden configurar una capa de 256 x 256 píxeles (32 x 32 tiles) como mínimo.

Como siempre, esta capa es desplazable cambiando el valor de algunos registros en el VDC.

#### Capa de Sprites {.tab}

![Capa de sprite renderizado.](graphics/sprites.png) {.tab-float.pixel}

El VDC contiene una memoria interna llamada **Buffer de Tabla de Atributos de Sprite** donde se pueden definir hasta 64 sprites. Cada entrada de la tabla almacena la posición X/Y independiente, la paleta de colores, el índice de tile y la inversión H/V. Además, hay un atributo que permite combinar un sprite con otro.

Cada entrada tiene una longitud de 8 bytes, aunque se desperdicia algo de espacio debido a la granularidad de 16 bits.

Para colmo, **la CPU no puede acceder a esta tabla**, por lo que necesita ser completada en VRAM y luego activar un canal DMA para copiarla al VDC (para que este último pueda utilizarla).

En cuanto a las limitaciones, solo puede haber hasta 16 sprites por línea de barrido. Por otro lado, se pueden establecer interrupciones para que el juego pueda ser notificado cuando haya un caso de desbordamiento de sprite o [colisión](master-system#tab-4-1-collision-detection).

#### Resultado {.tab}

![¡Tachán!](graphics/complete.png) {.tab-float.pixel}

Hasta ahora hemos visto cómo el VDC realiza todo el trabajo pesado, pero la última tarea en realidad es delegada al **Codificador de Video** o 'VCE' (por sus siglas en inglés, Video Encoder). Hudson nombró a este chip **HuC6260** y su función básica es recibir flujos de datos de 9 bits del VDC, aplicar las paletas de colores, y enviar el resultado a la TV (en forma de una señal analógica).

Si has leído artículos anteriores, quizá esté familiarizado con la importancia del "timing". Bueno, aquí no es la excepción: Para evitar artefactos no deseados (como 'nieve'), el VCE solo puede ser actualizado durante una interrupción vertical.

### Salida de video {.tabs-close}

El codificador de video proporciona salidas RGB (junto con Sync) y YPbPr, lo cual es ideal para su uso con un cable SCART o un cable de componentes, respectivamente. ¡Esto tiene buena pinta!...

![Puerto RF en el lado derecho de la PC Engine.](rfport.jpg){.tabs-nested .active .open-float .tab-float title="RF"}

![El 'Turbo Booster' para el TurboGrafx-16 [@photography-amos], conectado al puerto de expansión para suministrar video compuesto y energía.](turbo_booster.png){.tabs-nested-last title="Accesorio"}

... Por desgracia, Hudson decidió instalar un modulador de RF como única forma de obtener vídeo fuera de la caja, así que no es tan bueno después de todo. Pero, nuevamente, la PC Engine fue diseñada en los años 80, así que este enfoque garantizaba la compatibilidad con todo tipo de televisores (asumiendo que son de la misma región de la consola).

Por el lado bueno, el puerto de expansión contiene pines que llevan vídeo RGB y múltiples tipos de sincronización, pero necesitarán un accesorio externo para poder aprovecharlos.

{.close-float}

## Audio

La PC Engine contiene un [Generador de Sonido Programable](master-system#audio) o 'PSG' (por sus siglas en inglés, Programmable Sound Generator), al igual que muchas otras consolas de tercera generación analizadas en este sitio web. Por otro lado, este PSG en particular depende de **memoria de forma de onda** (también llamada 'buffer de forma de onda') para sintetizar sonido, en lugar de usar un conjunto predefinido de formas de onda (es decir, pulso, triángulo, etc.). La memoria de forma de onda permite a los programadores definir sus propias formas de onda, lo que brinda más flexibilidad para los arreglos de la banda sonora.

![R-Type (1987).](rtype){.open-float video="true"}

Este sistema cuenta con **seis canales de audio** [@audio-manual], cada uno se configura escribiendo en un conjunto de registros. Estos últimos almacenan los atributos de los canales, que incluyen:

- **Forma de onda**: El ciclo de la forma de onda se genera utilizando treinta y dos valores de 5 bits, cada uno corresponde a la amplitud de la onda en un momento particular.
  - Algunos juegos como *Fire Pro Wrestling 2* y *Bloody Wolf* incluso alteraron los valores durante la reproducción para obtener sonidos especiales (a expensas de clics audibles durante las transferencias).
- **Control de frecuencia**: Dos registros de 8 bits alteran la frecuencia del canal para producir diferentes notas musicales con la misma forma de onda.
- **Nivel de amplitud**: Un solo registro de 8 bits almacena dos valores de 4 bits que especifican cuán fuerte sonará el canal. Los dos valores corresponden al **control de panoramización** 'Izquierda' y 'Derecha', una característica pionera en comparación con lo que ofrecía la competencia.

{.close-float}

### Funciones extra

Algunos grupos de canales tienen otros modos de operación. Por ejemplo, los dos últimos canales están conectados a un **generador de ruido**. Además, el segundo canal puede actuar como un **oscilador de baja frecuencia** para modular el primer canal (inicialmente asumí que esto era un sinónimo de [síntesis FM](mega-drive-genesis#audio), pero los LFO son más bien un 'subconjunto' que logra un **efecto de vibrato**).

Hablando de modos, hay otro disponible llamado **Direct D/A (DDA)** que permite que la CPU escriba directamente sobre el búfer de audio (eludiendo el PSG). Consecuentemente, la PC Engine puede reproducir **muestras PCM**. Estas todavía son de 5 bits y dependen estrechamente de los ciclos de la CPU. Aunque, ¡dos canales pueden combinarse para reproducir muestras de 10 bits! Recomiendo encarecidamente que consulten la sección de 'Fuentes' para escuchar ejemplos [@audio-demo].

En resumen, esto hace que el PSG de la PC Engine sea una pieza de hardware increíblemente flexible.

### Sonido final

El PSG mezclará todo y emitirá una señal estéreo. Pero de nuevo, a menos que encuentres una forma de eludir el puerto RF, solo escucharás mono desde tu televisor.

## E/S

Esta consola tuvo una enorme cantidad de accesorios, algunos de los cuales mejoraron completamente la consola internamente (proporcionando más RAM, por ejemplo) y externamente (agregando más puertos y/o la posibilidad de leer otros medios de almacenamiento).

![Puerto de expansión en la parte trasera de la PC Engine.](expansion.jpg) {.open-float}

Esto se atribuye en gran medida a la inclusión del **Puerto de Expansión** en la parte trasera de la consola, que dio acceso a los siguientes componentes [@io-exp]:

- **Línea de +5 voltios**: Para alimentar el accesorio.
- **Una línea de entrada de voltaje**: Para eludir la PSU original y alimentar la consola a través del accesorio.
- **Entrada y salida de audio**: Que puede recibir la salida de audio estéreo y/o enviar audio desde una línea de entrada.
- **Líneas de datos y dirección de la CPU**: Permite que la CPU acceda al accesorio y viceversa.
- **Líneas de datos del VDC** (aquellas que van al codificador de video): Permite que el accesorio toque esas líneas, aunque no estoy seguro para qué.
- **Líneas de control de la CPU**: Lee la bandera de detección de HuCard y CD, el modo de velocidad utilizado y también tiene acceso a las líneas de interrupción.
- **Salida de video analógico**: Incluyendo RGB + Sincronización Vertical y Sincronización Horizontal + Sincronización Compuesta.

{.close-float}

Tengo curiosidad por saber hasta qué punto NEC o Hudson visualizaron esta consola, ¿estaban tratando de construir una especie de 'consola modular' que pudiera ser actualizada para entrar en la siguiente generación?

### El resto

Todavía no he mencionado el mando (o Joypad, como lo llaman en algunos lugares). Es muy similar al resto. La consola sólo dispone de un puerto para el mando y los juegos comprueban una única dirección en la memoria para obtener las pulsaciones de las teclas. Esa dirección devuelve un valor de 4 bits [@io-data].

## Sistema operativo

No hay ROM interna o 'BIOS' o cualquier otra cosa que se ejecute antes del juego, por lo que no tenemos un Sistema Operativo aquí. El vector de reset se encuentra en las direcciones `$FFFE` y `$FFFF` (recuerda que los registros internos son de 8 bits pero las direcciones de memoria son de 16 bits, por lo que necesitamos dos palabras para inicializar el contador de programa) y esas direcciones apuntan a la tarjeta del juego [@operating_system-sheldows].

Esto también significa que depende del programador encargarse de las tareas de 'mantenimiento' (es decir, inicializar la memoria, configurar la MMU, etc...).

## Juegos

Los programas están escritos en ensamblador 6502, aderezados con opcodes 65C02 adicionales y los introducidos por Hudson.

### Formato Estándar

En lugar de depender de esos cartuchos *gruesos y aburridos* que a todos les parece gustar, NEC/Hudson ideó otro formato, esta vez del tamaño de una tarjeta de crédito, llamada **HuCard**. Derivan de un formato más antiguo llamado 'Bee Card', utilizado por algunos juegos de MSX.

![Típico juego de tiendas.](hucard.jpg) {.open-float.pico}

Curiosamente, son muy similares a la [Sega Card](master-system#medium) pero contienen 38 pines en lugar de 35 [@games-pinout]. Internamente, las diferencias son más significativas:

- El mapa de memoria de la CPU puede acceder hasta **1 MB de memoria HuCard sin un mapeador**, por lo que la mayoría de los juegos se quedaron con ROMs de 1 MB. Mientras tanto, juegos como 'Street Fighter II' residían en una ROM de 2.5 MB! [@games-secrets]
- Hay un pin de **Audio In** que parece permitir que la tarjeta proporcione un canal de audio extra a la consola.

{.close-float}

### Expansión de CD-ROM

Ese puerto de expansión abrió la puerta a una cantidad masiva de accesorios y unidades de expansión que eventualmente convirtieron una simple PC Engine en algo completamente diferente. Como si no fuera suficiente, la ranura HuCard complementó las posibilidades de expansión. Lo que quiero decir es que para este artículo, tendré que centrarme en las actualizaciones notables (algunas de las cuales se incluyeron en futuras revisiones de la consola).

![Lector de CD-ROM² (versión TurboGrafx-16) [@photography-amos]. ¿Estaban en el negocio del Discman en ese entonces?](cd/reader.png){.tabs-nested .active .open-float .tab-float title="Lector"}

![Base necesaria para conectar la consola con el lector [@photography-amos]. También suministra energía y salida de video compuesto.](cd/compartment.png){.tab-nested title="Compartimiento"}

![Cómo se veía con todo encajado [@photography-amos].](cd/fitted.png){.tabs-nested-last title="Encajado"}

Echemos un vistazo a la expansión de **CD-ROM²**, que consiste en un lector de CD-ROM y una HuCard especial llamada **System Card**, esta última actúa como una **BIOS** para inicializar el juego y proporcionar algunas rutinas para hacer de interfaz con el lector. Internamente, el lector incluía **64 KB de RAM** para propósitos generales, otros **64 KB de RAM** para transmitir muestras ADPCM y finalmente, **2 KB** para guardar datos. Como puedes imaginar, esto permitió a los desarrolladores de juegos aprovechar el almacenamiento extra y audio en CD, mientras proporcionaba a los editores el alivio económico de distribuir sus juegos en un formato no propietario.

{.close-float}

Años después, NEC lanzó otro módulo de CD llamado **Super CD-ROM²** y mientras que el hardware del lector permaneció mayormente igual, su RAM de propósito general se incrementó a 256 KB. Los clientes que ya poseían una unidad CD-ROM² pudieron 'actualizarla' comprando la **Super System Card**, que incluía la RAM extra (¡y una rutina de I/O adicional!). Encima de todo eso, NEC luego envió nuevas actualizaciones de BIOS llamadas **Arcade Card**, que en este caso contenían **2 MB de RAM**.

Los juegos basados en CD dependían estrictamente de la tarjeta BIOS para la que fueron desarrollados, aunque las tarjetas más nuevas eran retrocompatibles (con algunas excepciones). Por lo tanto, la Arcade Card es la elección preferible para los usuarios que quieren jugar *casi* todos los juegos basados en CD-ROM. He dicho 'casi' porque que las compañías de terceros también lanzaron sus propias tarjetas BIOS (por ejemplo, 'Games Express CD Card'), y estas eran necesarias para jugar juegos específicamente de ese estudio.

Con el Super CD-ROM², NEC/Hudson también lanzó una nueva variante de la PC-Engine/TurboGrafx-16 llamada **Turbo Duo** que agrupaba la consola, el lector y la tarjeta BIOS en un solo dispositivo.

### Otras expansiones

Si tienes curiosidad por conocer otras expansiones lanzadas, consulta el catálogo en línea [@games-accessories].

## Anti-Piratería y Homebrew

Las HuCards están bloqueadas por región, lo que significa que un juego de PC-Engine no funcionará en un TurboGrafx-16 (o viceversa) directamente. Esto se debe a que el orden de las líneas de datos está invertido en la versión americana. Como puedes imaginar, esto no es algo que un adaptador barato no pueda resolver. Sin embargo, los juegos pueden incluir rutinas para verificar la región del sistema.

Por otro lado, los juegos de CD-ROM no están bloqueados por región ni protegidos contra copias, pero ten en cuenta que aún necesitan una System Card para inicializarlos (que está bloqueada por región).

## Eso es todo amigos

![Mi PC Engine desde un cable RF (_por motivos nostálgicos... eso creo_). ¡Un gran agradecimiento a Matt por donarla!](mypc.jpg)

¡Hasta aquí llegamos! Acabas de leer el último artículo del año.

2020 ha sido un año 'interesante' para el sitio web (dejando de lado todos esos eventos negativos que nos han afectado a todos). El año comenzó con el [artículo sobre la Wii](wii) y luego continuó con el [PS2](playstation-2), [Xbox](xbox), [Nintendo DS](nintendo-ds), [Master System](master-system) y finalmente, este.

Ha cambiado mucho en el número de visitantes también. Tuvo un comienzo lento al principio, pero luego durante el verano, atrapó un número asombrosamente alto de visitas. Quiero agradecer a los usuarios de Hacker News, OSNews, Reddit, Gigazine, YouTubers, usuarios de Twitter y muchos otros foros y personas por compartir los artículos. Y por supuesto, ¡un gran saludo a los donantes cuya generosidad ha ayudado a mantener el sitio a flote!

¡Que tengas una feliz Navidad, feliz Año Nuevo 2021 y hasta la próxima!  
Rodrigo
