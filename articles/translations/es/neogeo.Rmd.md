---
long_title: "Arquitectura del Neo Geo"
short_title: "Arquitectura del Neo Geo"
name: Neo Geo
long_name: Neo Geo
subtitle: El arcade en casa
date: 2024-06-19
release_date: 1990-04-26
generation: 4
aliases: ["/writings/consoles/neogeo-private-330"]
cover: neogeo
top_tabs:
  Model:
    caption: "El Neo Geo 'AES'.<br>Publicado el 26/04/1990 en Japón y el 01/07/1991 en América y Europa"
    file: "international"
    paperback_latex_height: 85
    hardcover_latex_height: 88
  Motherboard:
    caption: "Se muestra la revisión 'NEO-AES3-6'.<br>Esta fotografía fue tomada por mí con el modelo que compré. El propietario anterior recolocó algunos condensadores para mejorar la señal de vídeo."
    paperback_latex_height: 91
    marked_paperback_latex_height: 91
  Diagram:
    latex_height: 95
---

## Breve introducción

Recién salido del mundo del arcade, el Neo Geo fue, sin lugar a dudas, el hardware más caro de la cuarta generación. Esto plantea una pregunta inevitable: ¿hasta dónde llegaban sus prestaciones y cómo se comparaba con el resto?

En este análisis, examinaremos el fruto de que una empresa —SNK— dejara a un lado las restricciones de presupuesto y lanzara un producto pensado para satisfacer tanto a los dueños de salones recreativos como a los hogares más pudientes.

### Modelos y variantes

El Neo Geo es excepcional en el sentido de que fue diseñado para dos mercados al mismo tiempo: el recreativo y el doméstico. En consecuencia, SNK fabricó una única arquitectura que se comercializó en la forma de **dos variantes**, cada una orientada a un mercado específico:

- El **Multi Video System** o 'MVS' es la variante diseñada para los salones recreativos. Su E/S sigue la especificación 'JAMMA' (con algunas desviaciones [@general-jamma]), lo que facilita la integración en los muebles arcade. Además, su placa base especializada dispone de circuitería extra para gestionar los pagos con monedas. Sea como fuere, a lo largo de su vida útil, SNK llegó a lanzar innumerables revisiones, algunas de las cuales podían albergar hasta seis cartuchos de juego a la vez [@general-mvs_boards].
- El **Advanced Entertainment System** o 'AES' es el diseño simplificado para el hogar. Aunque más compacto y con una E/S reducida, no compromete sus capacidades fundamentales.

Supongo que también debo mencionar el **Neo Geo CD**, el sucesor de la variante AES que adoptó el CD-ROM para la distribución de juegos. Al igual que sus predecesores, este modelo también contó con múltiples revisiones.

En cualquier caso, en este análisis me centraré en la variante AES, que es la que tengo a mano. No obstante, haré las debidas referencias al MVS cuando corresponda hablar de su funcionalidad adicional.

## {.supporting-imagery}

## CPU

El sistema cuenta con una configuración de **doble procesador** compuesta por un **Motorola 68000** y un **Zilog Z80**, dos grandes iconos de la electrónica de los años ochenta. El 68000 se encarga de ejecutar el **programa principal** (también conocido como _el juego_), mientras que el Z80 queda relegado a la **gestión del sonido** [@cpu-z80]. Puede que recuerdes funciones idénticas en el [Mega Drive/Genesis](mega-drive-genesis), pero ahí terminan las similitudes, ya que las variantes de chip y la disposición del hardware difieren considerablemente.

![El chip Motorola 68HC000 en el interior del Neo Geo; este ejemplar es de segunda fuente, fabricado por Toshiba.](cpu/photo.jpg)

A modo de ejemplo, el 68000 del Neo Geo funciona a **12 MHz** (4 MHz más rápido que el del Mega Drive). Además, el chip concreto instalado es el **68HC000**, una variante que se distingue por el uso de puertas CMOS (en contraposición al proceso NMOS anterior y menos eficiente).

Si deseas conocer el interior del 68000 y del Z80, ya los analicé en los artículos del [Mega Drive/Genesis](mega-drive-genesis#cpu) y del [Master System](master-system#cpu), respectivamente. Por tanto, en el estudio del Neo Geo me centraré en sus nuevos usos.

### Una multitud de chips de soporte

La diferencia más notable entre el Neo Geo y cualquier consola doméstica de su generación es que, durante el diseño del primero, SNK no estuvo limitada por las restricciones presupuestarias del hogar medio. Gracias a ello, la placa base incorpora un extraordinario número de controladores integrados que no solo eliminan gran parte de los cuellos de botella de las CPU, sino que también amplían sus capacidades.

La lista de chips es extensa, pero lo más desconcertante es que, a lo largo de las revisiones, SNK rediseñó el chipset, ya fuera unificando varios chips o dividiéndolos. Por ahora, basta saber que la placa base contiene los siguientes grupos de 'aceleradores':

- **Controladores del 68k**: Para alternar entre tablas de vectores sin interrupciones. Explico este apartado en los siguientes párrafos.
- **Controladores del Z80**: Para aliviar las limitaciones en el direccionamiento de memoria.
- **Árbitros de E/S**: Para delegar todas las operaciones de E/S, ahorrando ciclos de paso.
- **Video Display Controller**: Para llevar a cabo todas las operaciones gráficas. Se tratará en una sección dedicada llamada 'Gráficos'.
- **Controladores del mueble**: Solo para la variante recreativa (el 'MVS'). Gestionan la ranura de monedas, proporcionan un marcador y realizan diagnósticos del sistema.
- **Controladores domésticos**: Ídem, pero solo en la variante doméstica (el 'AES'). En este grupo encontramos un codificador de vídeo que genera señales de vídeo compatibles con la televisión.

A partir de ahora, utilizaré estos nombres para referirme a cualquier grupo a lo largo del artículo. En algunos casos, también nombraré chips concretos dentro de los grupos para destacar su especial relevancia.

### Gestión sofisticada de la E/S

Permíteme presentarte una notable capacidad del 68000 que todavía no he tratado: la **tabla de interrupciones vectorizada**. Las CPU anteriores, como el [6502](nes#core-functionality) y el [Z80](master-system#cpu), tenían un diseño limitado para gestionar la comunicación E/S. Cuando un dispositivo externo reclamaba la atención de la CPU, este debía enviar una solicitud de interrupción que, a su vez, requería que la CPU ejecutara una rutina de software para procesarla. La información proporcionada durante la solicitud era muy escasa, lo que obligaba a la CPU a invertir ciclos adicionales en averiguar 'quién' había desencadenado la interrupción y 'por qué'.

Cabe señalar que CPU como el Z80 disponían de circuitería adicional para que el periférico indicara a la CPU qué instrucción ejecutar a continuación. Sin embargo, esta solución no se adoptó de manera generalizada.

El 68000 mejoró esta técnica tratando las interrupciones como **excepciones**. Los periféricos están obligados a identificarse cuando interrumpen a la CPU [@cpu-68k_int]. Esta utiliza el identificador para componer una dirección de memoria que, a su vez, apunta a una rutina de software específica. En definitiva, esto permite a los programadores gestionar la comunicación E/S de forma eficiente, ya que pueden catalogar sus manejadores de interrupción dedicados en una zona de memoria llamada **tabla de autovectores**, y la CPU los ejecutará automáticamente.

El Neo Geo saca partido de este diseño con la incorporación de un chip controlador llamado **NEO-E0**. En esencia, tanto el Sistema Operativo del Neo Geo como el juego implementan una tabla de excepciones dedicada: la del SO se centra en la comunicación con el hardware, mientras que la del juego se usa durante la ejecución normal. No obstante, el 68000 solo conoce una de ellas en cada momento. Por eso, el NEO-E0 proporciona un [cambio de bancos](nes#going-beyond-existing-capabilities) automático que garantiza que el 68000 lea siempre de la tabla correcta.

### Comunicación entre procesos

Cuando un sistema cuenta con una disposición de doble procesador, ha de existir algún medio para que ambas CPU se comuniquen. En esta consola, SNK implementó la comunicación entre procesos (IPC, del inglés *Inter-Process Communication*) mediante un chip **árbitro de E/S**. Este expone un único registro de 8 bits en el que el 68000 puede escribir [@cpu-68k_z80].

![Representación del canal IPC entre el 68000 y el Z80.](_diagrams/cpu/ipc.png){.no-borders}

Una vez actualizado el registro, el árbitro enviará una interrupción no enmascarable al Z80. A partir de ese momento, el Z80 puede leer el valor (mapeado en un [puerto](master-system#accessing-the-rest-of-the-components)) y reaccionar en consecuencia. Esto incluye volver a escribir en ese registro, de modo que el 68000 recibe nuevos datos. Sin embargo, por algún motivo, el 68000 no recibe ninguna interrupción (por lo que debe realizar algún tipo de sondeo).

El árbitro de E/S propiamente dicho ha ido cambiando con las sucesivas revisiones de la placa base: en un primer momento se implementó con el chip 'PRO-B0', que más tarde fue sustituido por el 'NEO-C1'.

### Prevención de contratiempos

Hablando de interrupciones, algo que los muebles arcade no pueden permitirse es requerir un mantenimiento constante.

No es ninguna novedad que los juegos se congelen de repente por culpa de un fallo de software. El remedio tampoco es ningún misterio: basta con apagar y encender, y volver a empezar. Ahora bien, imagina que esto le ocurriera a un mueble arcade. A menos que el propietario estuviera cerca, el mueble averiado no haría más que perder dinero.

El Neo Geo —heredero de los requisitos de las máquinas de monedas— incorpora un proceso de 'autoreparación' mediante un **programa Watchdog**, alojado en el chip árbitro de E/S [@cpu-watchdog].

![Los muebles arcade no pueden permitirse estar continuamente en mantenimiento. Ya tienen bastante con intentar atraer jugadores.](games/dragon.png){.border .pixel}

El sistema funciona de la siguiente manera: los juegos deben notificar periódicamente su presencia al watchdog (escribiendo en un registro concreto). Si transcurrido un tiempo determinado el watchdog no recibe ningún ping, asumirá que el programa se ha congelado y procederá a reiniciar el sistema.

Gracias a ello, los propietarios de MVS no necesitaban vigilar constantemente sus muebles. Por su parte, esos afortunados chavales que podían permitirse un sistema AES ni siquiera tenían que moverse de sus lujosos sofás cuando sus juegos se colgaban.

Por si eso fuera poco, las placas base MVS complementan este sistema con un microcontrolador adicional: el **NEO-F0**. Este controla el mecanismo de **bloqueo de monedas**, que impide a los usuarios malgastar monedas en sistemas averiados [@cpu-coin_lockout], entre otras funciones.

### Memoria disponible

La memoria está distribuida entre la placa base y el cartucho de juego (que, por cierto, es de proporciones considerables). Recuerda que se trata de un sistema costoso, por lo que el ancho de banda y la capacidad de memoria eran prioridades absolutas.

En cuanto a la memoria de uso general, el 68000 dispone de **64 KB de RAM**. El Z80, por su parte, cuenta con **2 KB**.

Los modelos MVS incluyen además otros **64 KB**. Estos cuentan con batería de respaldo y están destinados a almacenar puntuaciones de juego, por lo que no están disponibles para uso general.

Curiosamente, este sistema no incorpora ninguna forma de **Acceso Directo a Memoria** (DMA) para acelerar las transferencias de datos. Supongo que no es un requisito, teniendo en cuenta el número de buses dedicados.

## Gráficos

El Neo Geo representa la cima de los gráficos basados en tiles; su riqueza de color es comparable a la de los [sistemas basados en frame buffer](sega-saturn#graphics) (que solo se volvieron asequibles cuatro años después). Sea como fuere, en materia de efectos, la [Super Nintendo](super-nintendo#graphics) ha logrado hacer más con menos.

### Diseño

El subsistema gráfico gira en torno al modelo habitual del **Video Display Controller** (VDC), aunque en este caso está rodeado de un considerable número de componentes. El fruto es una consola capaz de mostrar ingentes cantidades de sprites —tantos, de hecho, que las icónicas [capas de fondo](nes#tab-1-2-background-layer) dejan de tener relevancia. Tiene sentido, pues los fondos se idearon originalmente para compensar el número limitado de sprites.

::: {.subfigures .tabs-nested .pixel}

![Metal Slug 3 (2000).](games/metalslug3.png){.active hardcover_latex_width="90%" paperback_latex_width="78%" .border title="Metal Slug"}

![Neo Turf Masters (1996).](games/turfmast.png){hardcover_latex_width="90%" paperback_latex_width="78%" .border title="Golf"}

![Neo Drift Out (1996).](games/neodrift.png){hardcover_latex_width="90%" paperback_latex_width="78%" .border title="Drift Out"}

![Art of Fighting 3: The Path of the Warrior (1996).](games/artoffight.png){hardcover_latex_width="90%" paperback_latex_width="78%" .border title="Fighting"}

![Windjammers (1993).](games/windjammers.png){hardcover_latex_width="90%" paperback_latex_width="78%" .border title="Windjammers"}

Ejemplos de juegos del Neo Geo.

:::

Dicho esto, la consola emite un frame compatible con las señales NTSC y PAL (la elección depende de la región). En este caso, las dimensiones son de **320 x 224 píxeles** (para NTSC) o **320 x 256 píxeles** (para PAL) [@graphics-frame_size].

#### El chipset

El grupo de chips encargado de generar la imagen en la televisión ha ido evolucionando a medida que se publicaban nuevas revisiones. Con el paso de los años, SNK alternó entre NEC y Fujitsu para fabricar sus circuitos integrados.

::: {.subfigures .side-by-side}

![El chip 'LSPC-A2'.](vdc/lspca2.jpg){.toleft hardcover_latex_width="73%" paperback_latex_width="65%"}

![El chip 'NEO-B1'.](vdc/neob1.jpg){.toright hardcover_latex_width="73%" paperback_latex_width="65%"}

En la revisión de mi placa base, estos dos componentes forman el Video Display Processor (VDC). Ambos fueron fabricados por Fujitsu.

:::

A efectos explicativos, comencemos por describir los chips más destacados del conjunto:

- El **Motorola 68000**. Ya lo conoces como la CPU principal. Su función principal es rellenar la VRAM y la Palette RAM, y configurar el VDC (mediante un conjunto de registros expuestos).
- El **Video Display Processor** (VDC): A partir de la información almacenada en varios chips de memoria, genera líneas de escaneo y las envía a la 'Palette RAM', que a su vez generará los píxeles de color necesarios.
  - La apariencia física del VDC varía según el modelo de placa base. Por ejemplo, la primera revisión alojaba tres chips ('LSPC-A0', 'NEO-B0' y 'NEO-C0'), mientras que las posteriores empleaban una combinación del 'LSPC-A2' y el 'NEO-B1'.
- El **Video DAC**: Convierte el flujo de color recibido de la 'Palette RAM' en una señal de vídeo.

No te preocupes si la explicación anterior resulta densa. Todo esto se explicará con más calma en las siguientes secciones. Pasemos ahora a analizar la información con la que operan estos chips.

### Organización del contenido

Los datos gráficos están distribuidos entre **dos placas distintas**. La primera es la **placa base de la consola**, y la otra se encuentra en el **cartucho de juego**. Es posible que recuerdes una disposición similar en la [NES/Famicom](nes#cartridgegame-data).

![Arquitectura de memoria del subsistema gráfico](_diagrams/vdc/content.png){.auto_rotate paperback_latex_width="95%"}

En la placa base encontramos:

- **68 KB de VRAM**: Almacena los atributos de los sprites y las referencias a tiles.
  - Físicamente, la VRAM está dividida en **4 KB de VRAM 'rápida'** y **64 KB de VRAM 'lenta'**. Cada bloque se conecta mediante un bus dedicado y presenta latencias radicalmente distintas. Esto condiciona el número de ciclos que la CPU debe esperar tras actualizar su contenido.
- **Cuatro line buffers**: Almacenan las líneas de escaneo renderizadas por el VDC. Solo codifican datos de tiles que deben combinarse con otros datos en la Palette RAM.
- **16 KB de Palette RAM**: Almacena las paletas de colores utilizadas por los gráficos. Este bloque también se encarga de enviar los colores de los píxeles al Video DAC.
- **64 KB de 'L0 ROM'**: Resulta que el VDC puede **reducir sprites**. Para lograrlo, la 'L0 ROM' almacena tablas de consulta que indican al VDC qué líneas de escaneo renderizar en función del nivel de zoom solicitado [@graphics-l0_rom].

La placa correspondiente del cartucho de juego se denomina **CHA Board** e incorpora lo siguiente:

- Varios chips **C ROM**: Almacenan los tiles para la capa de sprites.
- Una **S ROM**: Almacena los tiles para la capa 'Fix' (explicada en la siguiente sección).

### Construcción del frame

Ahora que ya tienes una visión general de los componentes más importantes, déjame resumirte cómo transforma el subsistema gráfico los datos en una imagen —en otras palabras, cómo renderiza un frame—:

1. La CPU rellena la VRAM con las referencias necesarias para la capa de sprites y la capa Fix.
2. A partir de la información en la VRAM, el VDC lee gráficos de las C ROM y la S ROM, y los almacena en los line buffers.
3. Mientras tiene lugar la lectura, el VDC utiliza la información de los line buffers para leer la Palette RAM. Esto hace que esta envíe los datos de color de los píxeles al Video DAC.
4. El Video DAC emite una señal de vídeo a la televisión.

Con estos pasos en mente, analizaré en detalle cada etapa usando _Art of Fighting 3_ como ejemplo.

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel latex_subfigure_width="0.46"}

![Varios tiles.](aof/tiles.jpg){.active .border title="Varios"}

![Un grupo de cuatro tiles.](aof/four_tiles.jpg){.border title="Grupo"}

Algunos tiles de la C ROM.

:::

Conocidos tradicionalmente como bitmaps de 8 x 8 píxeles, los tiles son el ingrediente principal para construir los gráficos de la cuarta generación. Por sus características particulares, el Neo Geo solo dibuja dos tipos de gráficos: **fix tiles** y **sprites**. Así, los tiles se codifican y almacenan por separado según el tipo gráfico. En ambos casos, cada píxel se codifica con **4 bits** (es decir, 4 bpp), lo que permite seleccionar hasta **15 colores más transparencia**.

En cuanto a la composición de color, la Palette RAM permite almacenar **256 paletas de colores** [@graphics-palettes]. Cada paleta consta de dieciséis colores definidos como valores **RGB de 16 bits**. Entre todas las ranuras disponibles, hay dos entradas reservadas: el 'Color de referencia', que debe estar fijado en `$8000` (negro puro), y el 'Color de fondo', que codifica un color arbitrario que se muestra cuando no hay tiles.

La CPU puede actualizar la Palette RAM en cualquier momento, pero solo debería hacerlo durante los periodos de borrado. De lo contrario, aparecerá un efecto de nieve.

#### Plano fijo {.tab}

![El plano fijo con un fondo negro.](aof/fixed_plane.png){.tab-float .border .pixel}

El Fixed plane es una capa de **320 x 256 píxeles** (40 x 32 tiles) de ancho [@graphics-fixed_layer], que ocupa prácticamente toda la pantalla. Sus tiles son completamente estáticos y solo tienen acceso a 16 paletas de color. En la práctica, se utiliza para mostrar información 'siempre visible', de forma similar a la [capa window](game-boy#tab-1-4-window) de la Game Boy.

Al emitirse en una pantalla CRT, el área mostrada será menor. Por tanto, su [zona segura](nes#constructing-the-frame) mide 38 x 28 tiles.

Los tiles fijos se declaran en una región de la VRAM denominada **Fix map**. Este adopta la forma de una tabla de 40 x 32, y cada entrada corresponde a una posición en la pantalla.

La S ROM del cartucho almacena los tiles exclusivos del Fixed plane. El VDC solo puede direccionar hasta 128 KB, pero esta limitación puede ampliarse incluyendo un [mapeador](nes#going-beyond-existing-capabilities) [@graphics-fix_bankswitching].

#### Sprites {.tab}

![La capa de sprites; obsérvese cómo se utiliza para todas las áreas de la escena (no solo los dos personajes).](aof/sprites.png){.tab-float .border .pixel}

Como ya sabes, los sprites son **tiles de movimiento libre**. Sin embargo, en el Neo Geo los sprites emplean tiles de **16 x 16 píxeles**, una dimensión inusual. Teniendo esto en cuenta, el VDC puede componer sprites que van desde **16 x 16 píxeles** (1 x 1 tiles) hasta **16 x 512 píxeles** (1 x 32 tiles). El motivo de ofrecer composiciones tan altas es que cualquier sprite también puede **combinarse horizontalmente** 'uniéndose' al anterior (aunque siguen contando como sprites independientes). En total, el límite es de **96 sprites por línea de escaneo** y **381 por frame** [@graphics-sprites], una cifra elevada en comparación con sus competidores.

En cuanto a los efectos, los sprites pueden ser **volteados** y/o **reducidos**. Estos atributos se almacenan en el bloque 'rápido' de VRAM, lo que permite a la CPU actualizarlos sin una latencia apreciable.

Dada su complejidad, los sprites se codifican en cuatro áreas de la VRAM denominadas **Sprite Control Blocks** (SCB), cada una de las cuales almacena los siguientes atributos [@graphics-beginners]:

- **SCB1**: Referencia al tile, referencia a la paleta de color, animación automática y volteo X/Y.
  - La 'animación automática' desencadena un incremento automático de la referencia al tile cada x frames (hay ocho intervalos disponibles), que se reinicia después de 4 u 8 incrementos [@graphics-animation]. Resulta útil para animar gráficos sin consumir ciclos de CPU.
- **SCB2**: Reducción horizontal y vertical.
- **SCB3**: Posición Y, tamaño vertical (en tiles) y si el sprite está unido al anterior.
  - La unión de sprites hace que el segundo herede los atributos del anterior, a excepción de la reducción horizontal.
- **SCB4**: Posición X.

Como puede apreciarse, por razones prácticas, los tres últimos bloques se almacenan en la VRAM rápida, mientras que el primero se almacena en la VRAM lenta [@graphics-vram].

#### Resultado {.tab}

![Frame resultante mostrado en pantalla.](aof/result.png){.tab-float .border .pixel}

Mientras que los [competidores](nes#constructing-the-frame) son conocidos por renderizar al ritmo del haz del CRT, el Neo Geo implementa un sistema de **renderizado por line buffer**. Con este enfoque, la consola almacena las líneas de escaneo renderizadas en dicho buffer, en lugar de enviarlas directamente a la pantalla. Como el VDC incorpora memoria suficiente para dos líneas de escaneo, esto le permite renderizar una línea mientras muestra la otra, lo que permite a los juegos actualizar los gráficos durante la **visualización activa** (además del [H-Blank](game-boy#tab-1-5-result) y el [V-Blank](nes#tab-1-5-result)).

A decir verdad, el VDC seguirá accediendo a la VRAM al mismo tiempo, por lo que la CPU debe esperar entre 12 y 16 ciclos [@graphics-vram] (según la operación) antes de volver a acceder a la VRAM; de lo contrario, la operación siguiente será ignorada (de ahí el uso de dos variantes de VRAM).

#### Emisión del frame {.tabs-close}

Para emitir las líneas de escaneo renderizadas, el proceso varía según la variante de consola (AES o MVS).

El modelo doméstico incorpora un codificador de vídeo para generar las señales compuestas y RGB. El conector A/V es algo similar al del [Master System](master-system#video-output) y el [MegaDrive](mega-drive-genesis#video-output), aunque no son intercambiables.

En el modelo MVS no existe nada parecido, ya que el protocolo JAMMA delega esa tarea al monitor del mueble. Por tanto, la placa base envía una señal RGB pura más sincronismo.

## Audio

¿Recuerdas el Z80 que mencioné al comienzo del artículo? Este sigue siendo la CPU de **4 MHz** habitual que has visto en muchas otras consolas y microordenadores, pero ahora tiene el privilegio de controlar un chip de sonido muy versátil: el **Yamaha YM2610**.

![El Yamaha YM2610 junto al YM3016, situado debajo. Ambos chips son necesarios para reproducir audio.](audio/yamaha.jpg)

El YM2610 es otro [sintetizador FM](mega-drive-genesis#tab-2-1-yamaha-ym2612) del popular fabricante japonés. Este está estrechamente relacionado con el [YM2612](mega-drive-genesis#tab-2-1-yamaha-ym2612) del [Mega Drive](mega-drive-genesis#audio), pero no te dejes engañar por su nombre, pues la versión del Neo Geo es algo más avanzada (aunque con algunas concesiones).

Tanto el YM2610 como el YM2612 forman parte de la serie 'OPN' de gama alta, lo que significa que Yamaha habilita cuatro operadores por canal FM. El chip del Neo Geo cuenta con **cuatro canales FM**, que son, a decir verdad, dos menos que el YM2612. Y hasta ahí llegan los puntos débiles.

Los canales FM cuentan con un **oscilador de baja frecuencia** (LFO) para modular la amplitud o la frecuencia de los operadores. El LFO contiene un conjunto predefinido de frecuencias que van de 3,98 Hz a 72,2 Hz [@audio-fm].

![Visualización en osciloscopio de Metal Slug 2 (1998), con los canales FM y ADPCM utilizados para la música.](metalslug){.open-float video="true" latex_width="80%" .negate .border}

Ahora bien, aunque el YM2610 es conocido por su síntesis FM, alberga funciones adicionales que no pasaron desapercibidas para los compositores musicales [@audio-app_manual2]:

- Un **Software-controlled Sound Generator** (SSG): Este es el nombre que Yamaha da a un [Generador de Sonido Programable](nes#audio) (PSG). En esencia, el chip integra un Yamaha YM2149 heredado que puede generar **tres ondas cuadradas** con **ruido** opcional. También incluye control de envolvente.
  - El YM2149 original incluía una interfaz llamada 'puertos E/S' para transferir datos entre la CPU y otro componente [@audio-ym2149]. Al ya no ser relevante, la variante integrada en el YM2610 no la incorpora.
- Siete **canales ADPCM**: Permiten reproducir **muestras** en formato ADPCM de 4 bits. Estos canales se dividen en dos grupos:
  - ADPCM-A: Consta de seis canales, con una resolución de muestreo de **12 bits** y una frecuencia de muestreo de **~18,5 kHz**.
  - ADPCM-B: Es el canal restante, con una resolución de **16 bits** y una frecuencia de muestreo de hasta **~55,56 kHz** (superior a la calidad de CD).

{.close-float}

El YM2610 está conectado a dos chips de memoria del cartucho denominados **V ROM**. Estos almacenan las muestras ADPCM, con una V ROM por grupo ADPCM.

En conjunto, el subsistema de audio es una amalgama de las consolas de tercera, cuarta y (entonces inédita) quinta generación. Sin embargo, a diferencia del [soporte CD](sega-saturn#the-compact-disc-cd), los cartuchos del Neo Geo tienen una capacidad limitada, por lo que los canales ADPCM están principalmente condicionados por las **limitaciones de almacenamiento**. En cualquier caso, algunos juegos encontraron disposiciones ingeniosas para el ADPCM, dejando el FM como elemento secundario [@audio-dissection].

Por último, el YM2610 trabaja en tándem con otro chip: el **Yamaha YM3016**. Este es un **Conversor Digital-Analógico** (DAC) dedicado de 16 bits, que toma la señal digital de salida del YM2610 y la convierte en audio analógico (comprensible para los altavoces).

### El director de orquesta

Para que el chip de audio haga algo con sentido, el Z80 debe comandarlo correctamente. Por ello, el Z80 ejecuta un programa almacenado en otro chip independiente denominado **M1 ROM**, que se encuentra dentro del cartucho de juego, concretamente en la CHA Board. El programa del Z80 suele denominarse **driver de sonido**, y hubo múltiples implementaciones [@audio-drivers].

![El Zilog Z80, el NEO-D0 y sus alrededores.](audio/z80.jpg)

En la misma línea, el Z80 va acompañado de un controlador propietario llamado **NEO-D0**, que actúa como banco de memoria y controlador de E/S.

## Sistema operativo

En la placa base se encuentra una **'System' ROM de 128 KB** [@operating_system-bios_rom], que funciona como la típica **BIOS** y ofrece:

- Un **bootloader** para inicializar el hardware, realizar una autocomprobación y arrancar la animación de presentación.
- Una **shell de configuración** (solo en los modelos MVS).
- Diversas **rutinas del sistema** que abstraen las operaciones de hardware.

### Los servicios visuales

Al encender la consola, el usuario verá una pantalla de presentación conocida como **Eyecatcher** [@operating_system-eyecatcher]. Su código se almacena en la System ROM, pero los recursos gráficos (el logotipo del Neo Geo y otras etiquetas) se obtienen de la C ROM y la S ROM del cartucho.

Alternativamente, la cabecera de la P ROM puede contener un flag que indique a la System ROM que delegue la rutina de presentación al cartucho [@operating_system-prom_header]. Esto solo funciona en la variante AES [@graphics-beginners], aunque los juegos posteriores lo adoptaron para mostrar texto diferente.

![El Eyecatcher implementado por la System ROM. Sigue dependiendo de los tiles suministrados por el cartucho. La implementación oficial muestra el característico subtítulo 'Max 330 Mega', a modo de alarde de la capacidad de almacenamiento teórica de los cartuchos del Neo Geo. Los juegos posteriores, haciendo uso del flag, muestran 'Giga Power' en su lugar.](system/eyecatcher.jpg){.toleft .pixel hardcover_latex_width="80%"}

![El Test Menu, con numerosas funciones de mantenimiento necesarias para configurar el mueble arcade.](system/testmenu.jpg){.toright .pixel hardcover_latex_width="80%"}

El sistema MVS también proporciona un **Test Menu** para configurar el mueble (p. ej., ajustar el calendario, probar los componentes, etc.). Este menú se activa accionando uno de los interruptores DIP del MVS (inaccesible para el usuario final). Por consiguiente, su placa base integra una S ROM que almacena los Fix tiles de este menú.

### Las rutinas del sistema

Además de los aspectos visuales, la System ROM proporciona rutinas de software para operar el hardware. Esto incluye:

- Leer las pulsaciones de los botones de los mandos y, en el caso del MVS, las ranuras de monedas.
- Acceder a la Memory Card.
- Inicializar la VRAM con la estructura adecuada para los bloques de sprites y el Fix tile map.

Curiosamente, las rutinas del sistema pueden, a su vez, llamar a rutinas del juego para continuar ciertas operaciones. Esto significa que los juegos también deben implementar ciertas llamadas [@operating_system-user_routine] [@operating_system-system_io]. Por ejemplo, la System ROM puede solicitar lo siguiente a los juegos:

- Mostrar la animación Eyecatcher personalizada (si el flag requerido está activado).
- Mostrar la animación de arranque del juego.
- Iniciar la demo del juego. Esto es lo que ves antes de insertar una moneda.
- Reproducir el sonido de 'moneda insertada'.
- Iniciar el juego cuando se hayan insertado suficientes monedas.

Como puede verse, la mayor parte de la E/S de bajo nivel está delegada en la System ROM; los juegos, en cambio, se centran en ofrecer su contenido único.

## Juegos

Para empezar, los juegos están escritos en ensamblador del **68k** y del **Z80**. Solo con la siguiente generación de consolas (y la llegada de [nuevas CPU](playstation#tab-1-1-a-bit-of-history)) los lenguajes de programación pasan a formar parte del repertorio.

### Soporte de distribución

Siguiendo la tendencia de la cuarta generación, el Neo Geo se apoya en los cartuchos como único soporte para los juegos.

No obstante, lo cierto es que los cartuchos del Neo Geo son los más grandes que he visto para una consola doméstica, debido a la cantidad de chips dedicados que alojan. A diferencia de la [Super Nintendo](super-nintendo), cuyos juegos podían [ampliar opcionalmente](super-nintendo#beyond-convention) las capacidades de la consola, los juegos del Neo Geo están obligados a incluir un considerable volumen de circuitería.

![Cartucho genérico del Neo Geo [@photography-amos]. Obsérvense las dos placas que sobresalen.](cartridge.png){.no-borders}

Ya he mencionado algunas partes del sistema de cartuchos en anteriores secciones, pero déjame hacer un breve resumen para que tengas una visión completa de cómo funciona este sistema.

Como ya sabes, los cartuchos del Neo Geo están compuestos por **dos placas**. La primera se denomina **PROG Board** y alberga los siguientes chips ROM:

- **P ROM**: Almacena el programa que ejecuta el 68000. Se conecta mediante un bus de datos de 16 bits y pueden direccionarse hasta **2 MB** (más requerirá un mapeador).
- **V ROM**: Almacena las muestras de audio ADPCM leídas por el YM2610.

Por su parte, la segunda placa es la ya mencionada **CHA Board** e incorpora lo siguiente:

- Las **C ROM** y la **S ROM**. Almacenan los tiles de sprites y los Fix tiles, respectivamente.
- Una **M1 ROM**: Almacena el programa del Z80. Como ya sabes, las capacidades de direccionamiento del Z80 son [muy limitadas](master-system#memory-available) (en este caso, el techo es de **62 KB**). Por ello, el acceso a la M1 ROM está gestionado por un [mapeador](nes#going-beyond-existing-capabilities) dedicado llamado **NEO-ZMC** (incluido en el cartucho), que permite direccionar hasta **512 KB** de memoria. Esta capacidad puede ampliarse añadiendo mapeadores adicionales a la CHA Board o adoptando el NEO-ZMC2 (que habilita hasta 4 MB).

### Guardado de progreso

Como híbrido entre consola doméstica y arcade, SNK proporcionó un accesorio rudimentario para registrar puntuaciones y progreso: una **Memory Card** propietaria.

![La Memory Card [@photography-amos].](memorycard.png){.no-borders latex_width="80%"}

La Memory Card funcionaba tanto en los modelos AES como en los MVS. Las opciones iban desde **2 KB** hasta **16 KB** de SRAM con batería de respaldo [@games-memory_card], aunque en todos los casos la batería era irreemplazable.

Los datos guardados se estructuraban en bloques de 64 B distribuidos en cinco segmentos, y la System ROM proporcionaba rutinas de E/S para operarla.

Dicho esto, si los muebles arcade estaban realmente 'preparados' para las Memory Card... Eso ya era otra historia.

> Yo conocía lo de las Memory Cards por las revistas, pero las recreativas oficiales de Neo Geo eran rarísimas en mi pueblo. En vez de eso, teníamos un montón de máquinas ‘Video Sonic’ de Segasa. ¡Algunas ni siquiera tenían todos los botones para jugar en condiciones, imagínate ya una ranura pa’ la Memory Card!
> 
> -- <cite>Orgulloso conocedor del Neo Geo de Jerez, España</cite>

## Antipiratería y Homebrew

Tanto las consolas como los juegos fueron objeto de partes no autorizadas, muchas de las cuales acabaron produciendo sus propias réplicas [@anti_piracy-bootlegs]. El hardware pirata se fabricaba combinando componentes de catálogo y chips obtenidos por ingeniería inversa, con el objetivo de ofrecer una alternativa más económica al hardware de SNK.

Así pues, por un lado, SNK intentaba mantener a raya a los piratas. Por el otro, los estudios de videojuegos se aseguraban de que su código solo se ejecutara en una consola y un cartucho auténticos.

### Combatir las consolas falsas

Ni a SNK ni a los estudios de videojuegos les convenía ejecutarse en hardware pirata (ya fuera un clon del Neo Geo o un cartucho pirata). Por ello, muchos juegos incluían rutinas que manipulaban zonas muy concretas de la placa base (p. ej., flags de estado, el reloj en tiempo real, el Watchdog, etc.) para detectar cualquier defecto en las placas clónicas [@anti_piracy-slot_checks]. Siempre que alguna de estas comprobaciones fallaba, el juego mostraba un mensaje de advertencia y se negaba a continuar.

### Combatir los cartuchos falsos

Dado que los cartuchos albergaban un número récord de circuitos integrados, los juegos implementaron medidas creativas para combatir la piratería.

Comenzando por las técnicas más comunes, la System ROM contiene una porción de datos llamada **Security Code** que se compara directamente con la P ROM del cartucho durante el arranque [@anti_piracy-security_code]. El bootloader solo continúa si ambas áreas coinciden.

Además, juegos como *Fatal Fury 2* también interactúan con su chip compañero 'PRO-CT0' (un multiplexor para la CHA Board) para asegurarse de que el programa reside en un cartucho oficial [@anti_piracy-fatal_fury]. En caso contrario, este asume que ha sido clonado, desencadenando diversas **molestias** antipiratería (como volver invencible al adversario).

Más adelante, los títulos más recientes también almacenaban **datos cifrados** en la C ROM, diseñados para ser descifrados de forma transparente por el 'NEO-CMC' (incluido en la CHA Board) [@anti_piracy-neo_cmc]. Este chip contenía un conjunto de funciones XOR poco convencionales para desofuscar los datos gráficos antes de que llegaran al VDC [@anti_piracy-mame_cmc]. El NEO-CMC también actúa como multiplexor para los datos de tiles dentro de las C ROM, lo que permite a los fabricantes prescindir por completo de los chips S ROM.

En los casos más extremos, juegos como 'Metal Slug X' incluían circuitos integrados especializados en su PROG Board, lo que complicaba aún más las cosas [@anti_piracy-progeop]. Esto recuerda al [sistema CIC de Nintendo](nes#anti-piracy-and-region-lock), que requería una comunicación constante entre el juego y el chip (aunque con objetivos y arquitectura considerablemente distintos).

En definitiva, esto demuestra que la batalla antipiratería no era más que otro juego del gato y el ratón. En este caso, sin embargo, SNK trataba de alejar tanto a los propietarios domésticos como a los de salones recreativos de las imitaciones más baratas.

### Utilidades poscomerciales

Una vez concluido el ciclo de vida de esta consola, emergió una nueva fase: el **Homebrew** (ejecución de software y/o hardware casero). En el momento de redactar este artículo (junio de 2024), las prácticas más habituales son las **modificaciones de hardware**, la **sustitución de firmware** y los **flashcarts**.

Gracias a su entusiasta comunidad, existen numerosos recursos en línea que enseñan a corregir y/o modificar el hardware del MVS y el AES. Entre ellos se encuentran la conversión de modelos MVS para funcionar fuera de los muebles (la llamada **consolización**), mientras que otros se centran en subsanar deficiencias de modelos concretos. Por ejemplo, el modelo AES que poseo tiene el codificador de vídeo puenteado, lo que mejora la calidad de la salida RGB.

En el ámbito del firmware, han surgido desarrollos como la 'UNIVERSE BIOS'/UniBIOS. Esta equivale a un **firmware personalizado** creado para sustituir la System ROM original. Internamente, la UniBIOS es una System ROM de MVS modificada para proporcionar las siguientes capacidades adicionales [@anti_piracy-unibios]:

- Cambiar entre el modo 'MVS' y 'AES'. Resulta muy útil para los sistemas AES, ya que los juegos tienen un número de monedas/vidas predefinido, y cambiar al modo MVS permite aumentar esta cantidad de forma arbitraria.
- Cambiar la región. Algunos juegos están censurados según la región en que se ejecutan.
- Activar trucos.

Por último, para ejecutar código arbitrario en esta consola, las tiendas venden flashcarts de terceros capaces de reprogramarse para replicar cualquier CHA Board y PROG Board existente. Dada su complejidad, no son precisamente asequibles, pero es otro producto del que disponen hoy en día los propietarios del Neo Geo.

## Eso es todo, amigos

![Mi Neo Geo AES. Lo compré por curiosidad, ya que el vendedor ofrecía un conjunto completo con un flashcart y dos mandos. Medio año después, se convirtió en una buena excusa para escribir un artículo sobre él.](myneogeo.webp)

Curiosamente, el último juego del Neo Geo se publicó en 2004, casi 14 años después de su lanzamiento. Todo un logro para una consola doméstica/arcade, y espero que este análisis te haya ayudado a entender estos resultados desde una perspectiva técnica.

Por mi parte, me resultó muy gratificante volver a una consola 2D después de escribir tantas palabras para el [artículo de la Nintendo 3DS](nintendo-3ds); esto me ayudó a retomar la estructura de escritura 'clásica' que adopté en los comienzos del sitio web en 2019.

Pensando en los próximos artículos, me quedan dos caminos por explorar: continuar la línea temporal del hardware moderno tras la [Nintendo 3DS](nintendo-3ds), o pasar a otra consola 2D: la Pippin. Esta última me permitiría presentar la CPU PowerPC, mientras que el hardware moderno me daría pie a hablar del ARMv7 y el ARMv8. ¡Ya veremos!

¡Hasta la próxima!  
Rodrigo
