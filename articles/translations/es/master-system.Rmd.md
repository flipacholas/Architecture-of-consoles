---
short_title: Arquitectura de la Sega Master System
long_title: Arquitectura de la Sega Master System
name: Sega Master System
subtitle: Competente de serie
date: 2020-10-12
release_date: 1985-10-20
generation: 3
cover: mastersystem
javascript: ['audioswitcher']
published: true
top_tabs:
  Model:
    caption: "La Sega Master System.<br>Lanzada el 20/10/1985 en Japón, en 09/1986 en América y en 06/1987 en Europa."
    latex_height: 87
  Motherboard:
    caption: "El chip de sonido está integrado en el procesador de vídeo."
    latex_height: 95
    marked_latex_height: 95
  Diagram:
    caption: ""
    latex_height: 95

# Historical
aliases: [/projects/consoles/master-system/]
---

## Una introducción rápida

La Master System es fruto de una larga cadena de sucesión. Lo que comenzó como un conjunto de componentes de catálogo ha adquirido una nueva identidad gracias a la ingeniería de Sega.

## Modelos y variantes

Al principio me confundí un poco al leer sobre los distintos modelos que Sega llegó a comercializar. He aquí un resumen de las principales variantes para despejar cualquier duda:

- **Sega Mark III**: la primera consola con esta arquitectura, lanzada en exclusiva en Japón.
- **Sega Master System** (Europa y América): una Mark III rediseñada con nueva carcasa, una ROM de BIOS y una ranura de cartucho diferente.
- **Sega Master System** (Japón): una Master System europea/americana con la ranura de cartucho de la Mark III, un nuevo chip FM y una toma jack para las 'gafas 3D'. Sin embargo, carece del botón `RESET`.

A partir de ahora, usaré los términos 'Master System' o 'SMS' para referirme colectivamente a estos modelos, excepto al hablar de características exclusivas de una variante concreta.

## {.supporting-imagery}

## CPU

Sega apostó por una **Zilog Z80** de pleno derecho funcionando a **~3,58 MHz**, una opción muy extendida entre fabricantes de ordenadores como Sinclair, Amstrad y Tandy.

::: {.subfigures .side-by-side}

![El Tandy TRS-80 americano (1977). Lo encontré en el Computer History Museum (Mountain View, California) durante mi visita en junio de 2019.](trs80.webp){.toleft paperback_latex_width="60%" hardcover_latex_width="70%"}

![El Sinclair ZX80 británico (1980), ¡es mucho más pequeño de lo que parece! Pertenece a la colección de Christopher Rivett [@photography-crivett].](zx80.webp){.toright paperback_latex_width="60%" hardcover_latex_width="70%"}

Ordenadores populares de todo el mundo con una CPU Z80.

:::

La CPU Z80 tiene una historia curiosa: fue diseñada nada menos que por los autores del Intel 8080 (Federico Faggin y Masatoshi Shima), quienes, desencantados con el rumbo de Intel, decidieron fundar su propia empresa de semiconductores, Zilog, en 1974.

![El chip Z80 en la placa base de la Master System.](z80_chip.webp)

Su primer producto puede considerarse un sucesor no oficial del Intel 8080, con las siguientes novedades:

- La **ISA Z80**: un set de instrucciones compatible con el Intel 8080, pero ampliado con muchas más instrucciones. Esta opera con palabras de **8 bits**.
- Un **bus de datos de 8 bits**, idóneo para mover datos de 8 bits. Los valores más grandes consumirán ciclos de CPU adicionales.
- **Catorce registros de propósito general de 8 bits** [@cpu-registers]: lo cual es bastante si tenemos en cuenta que el Intel 8080 tiene la mitad y el [MOS 6502 solo tres](nes#core-functionality). No obstante, el banco de registros del Z80 presenta ciertas particularidades (o ventajas, según se mire):
  - Solo **siete registros son accesibles a la vez**; los otros siete se denominan 'Registros Alternativos' y deben intercambiarse con el primer conjunto para acceder a ellos. Este mecanismo responde al principio de [cambio de bancos](nes#going-beyond-existing-capabilities). Además, el Z80 ofrece instrucciones especializadas como `EX` y `EXX` para transferir el contenido entre ambos conjuntos.
  - Dentro de cada conjunto, seis registros de 8 bits también pueden combinarse en pares para formar hasta **tres registros de 16 bits**, lo que permite operar con valores mayores.
- Un **bus de direcciones de 16 bits**, cuyas consecuencias se explican en la siguiente sección.
- Una **ALU de 4 bits**: esto puede sorprender, pero simplemente significa que las operaciones sobre valores de 8 bits requieren el doble de ciclos de cómputo.

La fotografía de la placa base que encabeza el artículo muestra una CPU NEC D780C-1, lo que simplemente indica que Sega recurrió a fabricantes secundarios para producir el chip. Otras revisiones incluyeron incluso el chip fabricado por la propia Zilog. En cualquier caso, a efectos de este análisis, lo que importa es que las características internas son las mismas.

### Comparativa relativa

Cabe señalar que la CPU 6502 (presente en la [NES](nes#core-functionality)) funciona a apenas ~2 MHz, es decir, aproximadamente la mitad de la velocidad del chip de la Master System. Si a ello sumamos el mayor banco de registros del Z80, podría parecer que la Master System superaría a la NES sin discusión.

Ahora bien, si profundizamos, comprobamos que el 6502 dispone de una ALU más capaz (de 8 bits). Por lo tanto, operaciones aritméticas que en el 6502 se completan en dos ciclos, **en el Z80 consumen cuatro**. Esto demuestra que atributos relativos como la velocidad de reloj o el tamaño del banco de registros, analizados de forma aislada, pueden ser engañosos. Tanto el Z80 como el 6502 destacan y flaquean en tareas distintas; en última instancia, todo depende de la habilidad del programador.

### Memoria disponible

Como ya vimos, el Z80 dispone de un bus de direcciones de 16 bits, por lo que la CPU puede acceder a hasta **64 KB de memoria**. En el mapa de memoria de la Master System encontrarás **8 KB de RAM** de propósito general [@cpu-map], duplicada en otro bloque de 8 KB. Por último, también se mapean **hasta 48 KB de ROM** del juego.

### Acceso al resto de componentes

Como habrás leído en el párrafo anterior, en el espacio de direcciones solo se encuentran la RAM principal y algo de ROM del cartucho. ¿Cómo accede entonces el programa al resto de componentes? Pues bien, a diferencia de la [Famicom/NES de Nintendo](nes), no todo el hardware de la Master System está mapeado en memoria. En su lugar, a ciertos periféricos se accede a través del **espacio de Entrada/Salida (E/S)**.

Esto se debe a que la familia Z80 incluye una característica llamada **puertos de E/S**, que permiten a la CPU comunicarse con el resto del hardware sin agotar las direcciones de memoria. Para ello, existe un espacio de direcciones separado para los 'dispositivos de E/S' denominado **puertos**, y ambos comparten los mismos buses de datos y direcciones. La diferencia radica en que los puertos se leen y escriben mediante las instrucciones `IN` y `OUT`, respectivamente, en contraposición a la instrucción convencional de carga/almacenamiento (`LD`).

Cuando se ejecuta una instrucción `IN` u `OUT`, el Z80 configura las líneas de dirección para apuntar al periférico (que podría ser, por ejemplo, un teclado), activa su pin `IORQ` para indicar que se ha iniciado una solicitud de E/S y, finalmente, activa el pin `RD` o `WR` según se trate de una instrucción `IN` o `OUT`, respectivamente. En consecuencia, el periférico direccionado debe monitorizar manualmente el bus de direcciones y los pines de E/S para realizar la operación requerida. En el caso de una instrucción `IN`, la CPU almacena el valor recibido en un registro predefinido.

![Esquema de direccionamiento de la SMS.](_diagrams/addressing.png)

La forma en que Sega interconectó la CPU con el resto de componentes no solo permite acceder a valores, sino también exponer u ocultar determinados componentes del mapa de memoria.

Curiosamente, la [Game Boy](game-boy#cpu) albergaba una 'variante' del Z80 que prescindía por completo de los puertos de E/S. Por ello, esta debía encajar todo en el mapa de memoria.

### Retrocompatibilidad

La arquitectura de esta consola es muy similar a la de su predecesora, la **Sega SG-1000**. Gracias a ello, la Master System resultó retrocompatible con la SG-1000, aunque esta característica solo se aplica a la variante japonesa, ya que las demás tienen una ranura de cartucho diferente.

## Gráficos

Los gráficos en pantalla los genera un chip propietario denominado **Video Display Processor** o 'VDP'. Internamente, este comparte el mismo diseño que el Texas Instruments TMS9918 (utilizado en la SG-1000) [@graphics-texas], aunque incorpora mejoras que se detallarán en las siguientes secciones.

### Organizando el contenido

![Arquitectura de memoria del VDP.](_diagrams/vdp.png)

Al VDP van conectados **16 KB de VRAM**, accesibles únicamente para el VDP mediante un **bus de datos de 16 bits** (Sega modificó el diseño original para acceder simultáneamente a dos chips de memoria con buses de 8 bits [@cpu-service]). Si vuelves a observar la foto de la placa base, notarás que los chips de RAM y VRAM son prácticamente iguales, salvo que la VRAM utiliza el modelo de chip terminado en '20', que ofrece menor latencia [@cpu-nec].

En la Master System, la VRAM almacena todo lo que el VDP necesita para renderizar (salvo la Colour RAM). La CPU rellena la VRAM escribiendo en registros específicos del VDP, que a su vez propagan los valores a la VRAM. Dado que el acceso al VDP se realiza a través de puertos de E/S, la CPU debe usar las instrucciones `IN` y `OUT`.

### Construyendo el frame

El VDP renderiza frames con una resolución de **hasta 256×192 píxeles**. Las posteriores revisiones añadieron compatibilidad con 256×224 y 256×240 píxeles. No obstante, para mantener la compatibilidad con todos los modelos, los desarrolladores se ciñeron a la resolución estándar. Este chip tiene el mismo *modus operandi* que la [PPU de Nintendo](nes#constructing-the-frame); en otras palabras, los gráficos se renderizan sobre la marcha.

Además, el VDP dispone de cuatro modos de operación distintos, que modifican las características del frame (profundidad de color y resolución):

- **Modos 0 a III**: heredados del TMS9918 de la SG-1000. Se incluyen por retrocompatibilidad, aunque cualquier juego de SMS puede usarlos.
- **Modo IV**: el modo nativo de la Master System, que da acceso a todas las prestaciones del VDP. En este análisis nos centraremos en este modo.

Veamos ahora cómo se dibuja un frame paso a paso. Para ello, utilizaré los recursos gráficos de *Sonic The Hedgehog*. Además, para facilitar las explicaciones, este análisis se centrará en la disposición de memoria estándar que Sega sugiere para organizar el contenido gráfico (sin olvidar que el VDP es muy flexible y permite a los juegos optimizar dicha disposición).

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel latex_subfigure_width="0.46"}

![Todos los tiles.](sonic/tiles.png){.active .border title="Todos"}

![Un tile individual.](sonic/tile.png){.border title="Individual"}

Tiles encontrados en la VRAM.

:::

El Modo IV se basa en el **sistema de tiles**. Como ya vimos en [explicaciones anteriores](nes#tab-1-1-tiles), los tiles son simplemente **bitmaps de 8×8 píxeles** que el motor de renderizado recupera para dibujar los gráficos del juego. En el caso del VDP, el frame se compone de dos planos: la capa de fondo y la capa de sprites.

Dentro de la VRAM existe una zona dedicada a los tiles llamada **Character generator** (Sega denomina 'Characters' a los tiles), con un tamaño de **14 KB**. Cada tile ocupa 32 bytes, por lo que se pueden almacenar hasta 448 tiles.

Cada tile contiene 64 píxeles, y el VDP exige que cada uno use 4 bits, lo que permite elegir entre **16 colores**. Estos bits indexan una entrada de la **Colour RAM** o 'CRAM', ubicada dentro del VDP, que almacena las paletas de colores. Los sistemas de paleta reducen el tamaño de los tiles en memoria y permiten a los programadores alternar los colores sin necesidad de almacenar múltiples copias.

La Colour RAM almacena **dos paletas de 16 colores cada una**. Cada entrada ocupa 6 bits, y cada grupo de 2 bits define un color del modelo RGB. Esto ofrece un total de 64 colores a elegir.

#### Capa de fondo {.tab}

![Screen map asignado.](sonic/tilemap.png){.tabs-nested .active .tab-float .pixel .interactive-only title="General"}

![Screen map asignado con el área seleccionada marcada.](sonic/tilemap_marked.png){.tabs-nested-last .pixel .latex-framed latex_width="80%" title="Seleccionado"}

La capa de fondo es un extenso plano donde se dibujan los tiles estáticos. Para ubicar contenido en ella, existe otra zona en la VRAM llamada **Screen map**, que ocupa 1,75 KB.

Esto permite a los desarrolladores construir una capa de 896 tiles (32×28 tiles) [@graphics-vdp], pero si hacemos los cálculos, vemos que esta capa es mayor que la resolución de salida de la consola. En realidad, solo 768 tiles (32×24) son visibles, por lo que el área visible se selecciona manualmente a discreción del programador. Además, al desplazar lentamente las coordenadas X e Y del área seleccionada se consigue un **efecto de desplazamiento**.

Cada entrada del mapa ocupa 2 bytes (coincidiendo con el ancho del bus de datos del VDP) y contiene la dirección del tile en el Character generator, junto con los siguientes atributos:

- **Inversión horizontal y vertical**.
- El **bit de prioridad** (determina si dibujar parte o la totalidad del tile por delante de los sprites).
- La **paleta de colores** empleada.

Curiosamente, hay tres bits sin usar en cada entrada que el juego puede aprovechar para otros propósitos (por ejemplo, flags adicionales de apoyo al motor del juego).

#### Sprites {.tab}

![Capa de sprites renderizada.](sonic/sprites.png){.tab-float .pixel .latex-framed latex_width="80%"}

Los sprites son esencialmente tiles que pueden moverse libremente. El VDP puede rasterizar **hasta 64 sprites** usando un tile individual (8×8 px) o dos tiles apilados verticalmente (8×16 px).

La **Sprite Attribute Table** es una zona de 256 bytes en la VRAM que contiene una lista de todos los sprites definidos. Sus entradas son similares a las de la capa de fondo, salvo que cada sprite incluye dos valores adicionales que representan las coordenadas X/Y.

El VDP está limitado a **un máximo de ocho sprites por línea de escaneo horizontal** [@graphics-macdonald]. Además, si varios sprites se superponen, se muestra el primero de la lista.

#### Resultado {.tab}

![¡Tachán!](sonic/result.png){.tab-float .pixel .latex-framed latex_width="80%" #fig-frame_result}

El VDP mezcla automáticamente las dos capas para formar el frame final. El proceso de renderizado se realiza línea de escaneo a línea de escaneo, por lo que el VDP no sabe realmente cómo quedará el frame; algo que solo el usuario percibe una vez que el televisor muestra la imagen completa.

Si examinas la imagen de ejemplo [@fig-frame_result], quizás notes una columna vertical en el lado izquierdo del frame. Esto ocurre porque el Screen map es lo suficientemente alto para ofrecer desplazamiento vertical sin generar artefactos, **pero no lo suficientemente ancho para el desplazamiento horizontal**. Para solventarlo, el VDP puede **enmascarar** el extremo izquierdo con una columna de 8 px para evitar que se muestren tiles incompletos.

Para actualizar los gráficos del siguiente frame sin interrumpir la imagen que se está mostrando, el VDP envía dos tipos de **interrupciones** a la CPU. La primera notifica que el televisor CRT ha terminado de emitir un número determinado de líneas de escaneo (llamada **interrupción horizontal**), mientras que la otra señala que el CRT ha completado el dibujo de la última línea de escaneo (llamada **interrupción vertical**), lo que indica que el frame ha finalizado. Durante estos eventos, el haz del CRT se reposiciona para dibujar la siguiente línea de escaneo (**intervalo de blanking**), por lo que cualquier modificación del estado del VDP no romperá la imagen. El blanking horizontal dispone de un margen de tiempo más corto que el vertical, aunque sigue siendo suficiente para cambiar, por ejemplo, la paleta de colores. Esto permite producir ciertos efectos.

### Secretos y limitaciones {.tabs-close}

A primera vista, el VDP puede parecer otro chip con funcionalidad mínima que hoy damos por descontada. Sin embargo, logró atraer considerable atención frente a la propuesta de Nintendo en su momento. ¿Por qué?

#### Detección de colisiones {.tabs .active}

En primer lugar, el VDP podía **detectar si dos sprites colisionaban**. Para ello bastaba con consultar su registro `status` [@graphics-collision]. Aunque no podía identificar qué sprites concretos colisionaban, los desarrolladores podían 'triangular' la posición leyendo otros registros, como el `scan-line counter`.

Esta función no era nueva: el TMS9918 también la incluía, por lo que la SG-1000 también disponía de detección de colisiones.

#### La necesidad de modularidad {.tab}

Al analizar el diseño de la PPU de Nintendo, puse el énfasis en su arquitectura interna de memoria. Aunque limitadas, algunas restricciones resultaron [beneficiosas en cierta medida](nes#secrets-and-limitations), ya que permitían ampliar el sistema con hardware adicional incluido en el cartucho, reduciendo así los costes.

El VDP no aprovecha este enfoque modular. En cambio, Sega implementó una solución diferente que, a su vez, reduce los costes del cartucho. La capa de fondo más reducida y las interrupciones horizontales son ejemplos de ello.

#### Gafas 3D {.tab}

![Gafas Sega 3-D [@photography-amos]. La variante americana se conectaba a través del puerto de tarjeta.](glasses.png){.tab-float .no-borders}

Resulta que Sega también comercializó unas **'gafas 3D'** como accesorio oficial. Estas funcionaban sincronizadas con el CRT. Durante el gameplay, el juego alternaba la posición de los objetos entre frames. Cada lente contenía una pantalla LCD que se volvía negra para bloquear la visión. La combinación adecuada de parpadeo gráfico y obturadores alternos generaba en la mente del usuario una imagen estereoscópica, produciendo así el efecto '3D'.

Los obturadores se controlan mediante varias direcciones de memoria, pero ninguna indica si las gafas están realmente conectadas. Por ello, los juegos compatibles con este accesorio incluyen una opción en los ajustes para activar manualmente esta función.

Los controladores LCD se conectan mediante un cable jack que se enchufa a la consola. Las versiones europea y americana no incluían la entrada jack, por lo que dependen del puerto de tarjeta para conectar un adaptador (más adelante veremos más sobre esta ranura).

### Salida de vídeo {.tabs-close}

El conector de salida de vídeo de este sistema es *increíblemente* versátil. Este expone tanto señales **composite** como **RGB**, que pueden considerarse los dos 'extremos' de la calidad de vídeo.

El inconveniente es que no transporta 'sincronización compuesta', por lo que usar la salida RGB requiere capturar la señal de sincronización del composite, cuya calidad no es óptima.

## Audio

Las capacidades de audio de esta consola están, en líneas generales, a la par con otros equipos de los años 80. Dentro del chip VDP encontramos una versión ligeramente personalizada del **Texas Instruments SN76489** [@audio-sn76489], que es un **Generador de Sonido Programable** (PSG, del inglés 'Programmable Sound Generator'). Se trata del mismo tipo que empleaba la NES/Famicom, aunque con funciones distintas.

### Funcionalidad

Un PSG solo puede sintetizar un conjunto limitado de formas de onda, con cada canal asignado a una sola. Ya introduje algunos PSGs en los artículos de la [NES](nes#audio) y la [Game Boy](game-boy#audio), si te interesa este tipo de síntesis sonora.

En la SMS, el PSG se programa modificando su conjunto de registros a través de los mencionados puertos de E/S.

Veamos ahora cada tipo de waveform que el SN76489 puede sintetizar:

#### Pulso {.tabs .active}

![Osciloscopio de Sonic The Hedgehog (1991), con los canales de pulso.](pulse){.tab-float video="true" latex_width="80%" .negate .border}

Las ondas de pulso producen ese sonido icónico de la generación de 8 bits. La onda sonora se genera elevando el voltaje, manteniéndolo constante y después dejándolo caer. Repetir esto a una velocidad constante produce un tono.

El periodo de la onda define la frecuencia del sonido (la nota musical), mientras que su ciclo de trabajo afecta al timbre.

El SN76489, en concreto, puede producir **tres ondas de pulso simultáneamente**. Además, expone un contador de 10 bits en cada canal, que se emplea internamente para bloquear la salida, generando así ondas de pulso con frecuencias programables.

#### Ruido {.tab}

![Osciloscopio de Sonic The Hedgehog (1991), con el canal de ruido.](noise){.tab-float video="true" latex_width="80%" .negate .border}

El ruido es un tipo de señal asociada a la interferencia. Cuando se envía esta señal a un altavoz, suena como estática.

El SN76489 contiene un Registro de Desplazamiento de Retroalimentación Lineal (LFSR, del inglés 'Linear Feedback Shift Register') que se alimenta de una señal inicial y cicla para generar una señal pseudoaleatoria. Este puede alternar entre **ruido blanco** y **ruido periódico**.

El oscilador ofrece tres frecuencias seleccionables que determinan el tono del ruido [@audio-ti_sn76489]. Existe también una cuarta opción en la que la salida del tercer canal de pulso modula el canal de ruido. Algunos juegos combinaron esta opción con el modo periódico para producir sonidos graves (similares a un pulso cuatro octavas más bajo).

En general, los juegos suelen usar el canal de ruido para **percusión o efectos de sonido**.

#### Mezclado {.tab}

![Osciloscopio de Sonic The Hedgehog (1991), con todos los canales de audio.](pulse_complete){.tab-float video="true" latex_width="80%" .negate .border}

Hasta ahora hemos visto qué hace cada canal de forma individual. En la práctica, el televisor recibe una señal mono con todos los canales mezclados por el PSG.

Por último, el chip también incluye atenuadores programables que reducen los decibelios de cada canal, que actúan en la práctica como un **control de volumen**.

### Secretos y limitaciones {.tabs-close}

Al igual que el VDP, el PSG puede parecer trivial, pero esconde funciones interesantes:

#### Expansión FM {.tabs .active}

![Double Dragon (1987).](){audio_switcher="true" src1="psg" src2="fm" .float .interactive-only label1="PSG" label2="FM"}

La versión japonesa de la Master System incorporaba un chip adicional de Yamaha llamado **YM2413**. Este es radicalmente diferente del PSG, ya que emplea **modulación de frecuencia** para generar sonido. Expliqué brevemente cómo funciona en el [artículo sobre la Mega Drive](mega-drive-genesis#tab-2-1-yamaha-ym2612), por si te interesa.

Este chip añade **nueve canales de audio**. Cada canal puede seleccionar uno de los 16 instrumentos predefinidos o definir uno personalizado programando el portador y el modulador. Por desgracia, solo se permite un instrumento personalizado a la vez. Por otro lado, el instrumento personalizado ofrece funciones interesantes, como parámetros de la envolvente ADSR y retroalimentación.

El YM2413 también dispone de un segundo modo de operación llamado **Rhythm mode**, que proporciona **seis canales** complementados con **cinco canales adicionales** exclusivos para instrumentos de ritmo.

La salida de audio final la genera el YM2413, que mezcla sus canales con los del PSG.

La variante Mark III no incluía este chip, aunque la funcionalidad FM estaba disponible como unidad de expansión denominada **FM Sound Unit**. Las Master Systems europeas y americanas, en cambio, tuvieron que conformarse con el PSG, aunque con el tiempo surgieron algunas modificaciones de terceros.

#### Precisión en la emulación {.tab .interactive-only}

![Comparación de formas de onda de pulso en emuladores.<br>La de la NES muestra cierta caída mientras que la de la SMS tiene forma cuadrada.](decay.jpg){.tab-float}

Al consultar SMS Power (una web que recopila información técnica exhaustiva sobre el sistema), encontré una sección llamada 'The imperfect SN76489' [@audio-sn76489], que aborda ciertas discrepancias surgidas al redactar este artículo.

Si vuelves al vídeo de ejemplo de la onda de pulso, notarás que se visualiza como una onda cuadrada casi perfecta. El generador de pulso funciona bloqueando el voltaje para generar el tono. Sin embargo, en los circuitos eléctricos, los componentes no pasan de cero a uno (ni al revés) de forma instantánea; siempre existe un período transitorio, principalmente por los filtros presentes en el circuito eléctrico.

Para grabar, uso emuladores que capturan los canales por separado y evitan interferencias. No obstante, los emuladores no siempre tienen en cuenta el factor transitorio, por lo que su resultado puede acercarse más al 'escenario ideal' que al comportamiento real de la electrónica de los 80. Observa la imagen de ejemplo: ambas muestras proceden de emuladores, pero la de la NES podría mostrar un comportamiento más próximo al mundo analógico.

Por el momento no dispongo de las herramientas necesarias para confirmar si la SMS debería mostrar un comportamiento similar. En cualquier caso, si así fuera, no significaría que lo que escuchaste fuera incorrecto; solo que podría estar a un volumen ligeramente diferente, algo apenas perceptible.

#### Reproducción de muestras {.tab}

![Osciloscopio de Alex Kidd - The Lost Stars (1986), con la muestra PCM de 1 bit.](ball){.tab-float video="true" latex_width="80%" .negate .border}

Aunque el SN76489 carece de un [canal PCM](nes#tab-3-4-sample) para reproducir muestras, existen ciertos trucos que permiten simular esta funcionalidad.

Estos trucos se basan en los canales de pulso: se descubrió que si el nivel de tono se fija en `1`, el nivel de volumen (que altera la amplitud) condiciona la forma de la waveform.

smspower.org describe varios diseños que permiten reproducir muestras PCM de 1, 4 y 8 bits. No obstante, los requisitos de almacenamiento se disparan con resoluciones y tasas de muestreo más altas, por lo que estas técnicas se aprovechan mejor en juegos homebrew.

Cabe señalar que la transmisión de muestras consume una cantidad considerable de ciclos de CPU y, dado que este sistema solo tiene un procesador, es posible que el juego deba detenerse brevemente.

## E/S {.tabs-close}

Al igual que otros sistemas de su generación, la CPU es la encargada principal de gestionar la E/S. En este caso, el procesador Z80 destaca por su direccionamiento de E/S especializado, aunque siguen consumiéndose ciclos de CPU para mover bits entre componentes.

Por otro lado, la SMS utiliza un chip **controlador de E/S** dedicado para comunicarse con los mandos y habilitar o deshabilitar partes del sistema, lo que altera el mapa de direcciones. Además, este controlador es fundamental para admitir la expansión FM, ya que el módulo FM expone puertos que entran en conflicto con el resto del sistema.

### Interfaces disponibles

Además de los dos puertos para mandos, el sistema incluye una ranura de cartucho propietaria, una ranura para 'Sega Card' y una ranura de expansión reservada para 'futuros accesorios'. Esta última nunca se utilizó, salvo para la expansión FM en la Mark III. Aun así, la SMS y la Mark III presentaban diseños de puerto de expansión diferentes [@cpu-mk3].

### Los botones superiores

Otra particularidad de esta consola es la inclusión de dos botones en la parte superior de la carcasa: `PAUSE` y `RESET`. ¡Ya puedes imaginar para qué sirven!

![Parte superior de la carcasa [@photography-amos].](top.png){.open-float}

Pulsar el botón `PAUSE` envía una interrupción no enmascarable a la CPU [@io-pause]. Dicho manejador de la interrupción se almacena dentro del propio juego, lo que significa que corresponde al propio juego decidir cómo responder a esa pulsación.

En cambio, por alguna extraña razón, el botón `RESET` se gestiona como una pulsación de tecla en el mando.

{.close-float}

## Sistema operativo

En la placa base hay una pequeña **ROM de BIOS de 8 KB** que se ejecuta cada vez que se enciende la consola. El programa en sí no constituye un 'Sistema Operativo'; es más preciso describirlo como un **Gestor de Arranque**.

### Selector de medio

El objetivo principal de la BIOS es arrancar un juego válido (desde cualquier ranura) en el siguiente orden de prioridad: la Sega Card, el cartucho y el módulo de expansión.

El proceso de arranque funciona así:

1. Se enciende la consola.
1. La BIOS copia parte de su código en la RAM principal.
    - Este paso es crucial, ya que el programa comenzará a manipular puertos de E/S, lo que acabará inhabilitando el acceso a la ROM.
1. Mostrar la pantalla de presentación (solo en los modelos USA/Europa).
2. Comprobar cada ranura en busca de un juego válido.
    - Esto se realiza comunicándose con el chip controlador de E/S para activar la ranura requerida. A continuación, el programa de arranque copia la cabecera del juego (16 bytes) de cada ranura para verificar si el contenido es válido (es decir, si está correctamente insertado). La cabecera debe contener `TMR SEGA`.
3. Realizar la comprobación de región.
5. Redirigir la ejecución al juego.

### Pantalla sorpresa

Si alguna de las comprobaciones falla, la consola entra en un bucle indefinido mientras muestra una pantalla que pide al usuario que inserte un juego válido.

![Mensaje de error USA/Europa (tras la pantalla inicial).](bios/usa){.toleft video="true"}

![Mensaje de 'error' japonés (¡mejorado con el chip FM!).](bios/japanese){.toright video="true"}

Hay que admitir que, en cuanto al diseño de la pantalla de error, las diferencias creativas entre regiones son bastante notables. La primera vez que escuché la vistosa versión japonesa, pensé que estaba basada en *Electric Light Orchestra* (el grupo), pero en realidad procede de *Space Harrier* (el juego). El efecto de perspectiva del suelo, por cierto, se consigue alterando las paletas de colores.

### Más diferencias regionales

Dado que la variante japonesa era retrocompatible con la SG-1000, la comprobación de cabecera se sustituía por una 'comprobación de integridad' que leía los datos de los primeros 256 bytes varias veces para detectar posibles errores.

Además, la Mark III no tenía BIOS, por lo que las ranuras se activaban mediante interruptores de hardware. En este caso, el cartucho era el medio con mayor prioridad.

### Actualización y chips de BIOS posteriores

La ROM de BIOS, por su propia naturaleza, **no es actualizable**. Sin embargo, a medida que aparecían nuevas revisiones de la consola, se descubrió que Sega también había ido actualizando el programa de la BIOS.

Las ROM más recientes llegaron incluso a incluir juegos completos. Como consecuencia, el chip aumentó de tamaño y se le añadió un mapeador dedicado.

## Juegos

En pocas palabras, los juegos se escribían en ensamblador Z80 puro, y punto. En aquella época no había compiladores ni herramientas de alto nivel, más allá del ensamblador.

### Medio

::: {.subfigures .side-by-side max_subfigures=2 latex_subfigure_width="0.49"}

![Ejemplo de cartucho de juego [@photography-amos].](game_cartridge.webp){.toleft .no-borders}

![Ejemplo de Sega Card [@photography-amos].](game_card.webp){.toright .no-borders}

Los dos canales de distribución.

:::

La Master System admite dos medios distintos de distribución de juegos:

- El **cartucho**: el más habitual, capaz de direccionar hasta 48 KB de memoria. No obstante, al incluir un mapeador, el sistema podía acceder a un rango mayor y gestionar chips adicionales, como la RAM con batería para guardar partidas.
  - Sega ofreció a los desarrolladores mapeadores oficiales llamados 'Paging Chips'. El más potente podía mapear hasta 512 KB de memoria.
- La **Sega Card**: visiblemente más delgada y económica de producir, aunque solo llegaba a los 32 KB de memoria. Como Sega nunca diseñó un mapeador para este medio, la tarjeta más grande disponible en el mercado contenía una ROM de 32 KB.

## Antipiratería y bloqueo regional

A diferencia de Nintendo, Sega no recurrió a métodos agresivos para controlar la distribución de sus juegos. Sí impidió, sin embargo, que los sistemas americanos y europeos ejecutaran juegos japoneses, modificando la forma de la ranura de cartucho e implementando una comprobación de cabecera de ROM diferente.

Además, los sistemas americanos y europeos debían incluir el código `TMR SEGA` mencionado anteriormente en su cabecera. Supongo que esto permitía a Sega prevenir la distribución no autorizada amparándose en las leyes de marcas registradas.

## Eso es todo amigos

Haber escrito el artículo sobre la Nintendo DS antes que este me permite apreciar lo complicada que se ha vuelto la tecnología. La Master System es muy sencilla en comparación, incluso tras algún que otro 'puntillazo técnico' que me he permitido aquí y allá.

En fin, espero que este artículo te haya ofrecido una visión completa del estado de la tecnología a principios y mediados de los años 80. También quiero agradecer a la comunidad de smspower.org y al discord de /r/Emulation su lectura del borrador inicial y los *muchos* comentarios y sugerencias aportados.

¡Hasta la próxima!  
Rodrigo

*Dedicado a la memoria de Jacinto 'Pocho' Fornasier.*
