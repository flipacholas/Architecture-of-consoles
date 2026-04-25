---
short_title: Arquitectura del Sega Saturn
long_title: Arquitectura del Sega Saturn
name: Sega Saturn
subtitle: ¿Qué puedes hacer con 8 procesadores?
date: 2019-08-03
release_date: 1994-11-22
generation: 5
cover: saturn
javascript: ['threejs']
published: true
seo_image_pos: "Top"
top_tabs:
  Models:
    - title: "Japonés"
      caption: "La Sega Saturn, lanzada el 22/11/1994 en Japón"
      file: japanese
      latex_height: 90
    - title: "Internacional"
      caption: "La Sega Saturn, lanzada el 11/05/1995 en América y el 08/07/1995 en Europa"
      active: true
      file: international
      latex_height: 93
  Motherboard:
    caption: "Revisión 'VA8', que integra todos los componentes en una sola placa. <br>Los chips de RAM restantes están montados en la parte trasera"
  Diagram:
    latex_height: 95

# Historical
aliases: [/projects/consoles/sega-saturn/]
---

## Una breve introducción

¡Bienvenido a la era 3D! Bueno... *más o menos*. Sega cosechó un éxito considerable con la Mega Drive, así que no había motivo para obligar a los desarrolladores a crear juegos 3D *en ese preciso momento*.

Por si acaso los desarrolladores querían esa dimensión extra, Sega incorporó en el hardware algunos elementos para habilitar el dibujado de polígonos. ¡Con suerte, el resultado no se les fue de las manos!

## {.supporting-imagery}

## CPU

Al igual que sus competidores más directos, [ahogados en opciones](playstation#tab-1-1-a-bit-of-history) durante la fiebre del RISC, Sega tuvo que enfrentarse a todos los dilemas que conlleva elegir un nuevo proveedor capaz de satisfacer las necesidades de la siguiente generación de juegos (incluidos los que requerían capacidades '3D'). Al final, la compañía optó por una CPU de nuevo cuño cuyo creador buscaba desesperadamente un cliente que la adoptase: el **Hitachi SuperH**, conocido como 'SH'.

Aunque inicialmente orientada a aplicaciones embebidas, la nueva creación de Hitachi incorporaba técnicas modernas como [@cpu-prog_manual]:

- Una [arquitectura load-store](xbox#tab-1-4-cisc-or-risc), lo que significa que las instrucciones no mezclan operaciones de memoria y de registro, dando lugar a un diseño de CPU más limpio y escalable. Este es uno de los pilares de las CPU RISC.
- Un **bus de datos y una Unidad Aritmético-Lógica (ALU, del inglés *Arithmetic Logic Unit*) de 32 bits**, que permite el flujo y procesamiento de mayores cantidades de datos (valores de 32 bits) sin consumir ciclos adicionales.
- **16 registros de uso general de 32 bits**, esta vez más flexibles que los de CPU anteriores, como el [Motorola 68000](mega-drive-genesis#cpu). Esta es otra decisión de diseño derivada de las directrices RISC [@cpu-patterson].
- **Bus de direcciones de 32 bits**, que permite direccionar hasta 4 GB de memoria (*adiós [mapeadores](nes#going-beyond-existing-capabilities)*).
- Un **datapath en pipeline** con **cinco etapas**: la ejecución de instrucciones se divide ahora en cinco pasos o *etapas*. La CPU puede encolar hasta cinco instrucciones simultáneamente, asignando cada una a una etapa distinta. Esto permite aprovechar al máximo todos los recursos de la CPU sin tiempos de inactividad, a la vez que aumenta el número de instrucciones procesadas por unidad de tiempo.
- Una **unidad de multiplicación de 16 bits**: realiza multiplicaciones con enteros de 16 bits.

Además, el SuperH incorpora un nuevo set de instrucciones llamado **SuperH ISA** que, aparte de adoptar un diseño RISC, presenta una particularidad: **todas sus instrucciones miden tan solo 16 bits de anchura**. Esto resulta sorprendente, ya que esta CPU opera con palabras de 32 bits, por lo que cabría esperar que las instrucciones tuviesen la misma longitud. Sin embargo, Hitachi logró condensarla a la mitad del ancho. Este formato no solo reduce el tamaño de los programas, sino que, al leer las instrucciones en bloques de 32 bits, la CPU puede **leer dos instrucciones en un solo ciclo**. En definitiva, esta técnica de comprimir el set de instrucciones contribuyó a paliar una preocupación habitual de las arquitecturas basadas en RISC denominada 'densidad de código', según la cual estas requerían más instrucciones (y, por tanto, más memoria) para realizar las mismas tareas que los sistemas no RISC.

### Las ramificaciones

Por otra parte, el SuperH no está exento de otros inconvenientes propios de los diseños RISC, como los [riesgos de control](playstation#delay-galore). En consecuencia, los programas deben incluir **branch delay slots** para evitar errores de cálculo. Para paliar esto, el SuperH incorpora **instrucciones de salto retardado** (*delayed branch instructions*), es decir, instrucciones de salto provistas de antemano con un delay slot [@cpu-prog_manual].

Los **riesgos de datos** (*data hazards*) también están presentes, aunque no es responsabilidad del programador ni del compilador gestionarlos: la CPU detendrá el pipeline automáticamente cuando sea necesario.

### Sega no queda satisfecha

Aun así, todo eso no impidió que Sega manifestase su insatisfacción con el producto final. El motivo principal era el pequeño multiplicador de 16 bits, que se consideraba un cuello de botella al procesar grandes cantidades de datos (una nueva exigencia de los juegos 3D). Por ello, Hitachi desarrolló una segunda revisión con una unidad multiplicadora ampliada y otras exigencias de Sega [@cpu-history], dando lugar a una nueva CPU llamada **SH-2**.

![Los dos chips SH-2 del Sega Saturn](sh2s.jpg)

Con todo, Sega no podía quedarse de brazos cruzados al conocer la elección de CPU de [sus](playstation#cpu) [competidores](nintendo-64#cpu). Así que pidió a Hitachi que aumentase la frecuencia de reloj del SH-2, algo imposible una vez que el chip ya está en producción. Por suerte, Hitachi tenía un as en la manga: el **multiprocesamiento**. Durante la fase de investigación del SH, el equipo añadió unos mínimos circuitos adicionales para permitir que el SH trabajase junto a otros SH dentro del mismo sistema de forma simultánea. Al conocer esto, Sega optó por una configuración de dos chips para la Sega Saturn. Y el resto es historia.

### El producto final

Sentado el contexto de sus orígenes, veamos el producto final.

Esta consola no cuenta con una sino con **dos CPU Hitachi SH-2** funcionando a **~28,63 MHz cada una** [@cpu-overview]. Aunque físicamente idénticas, se organizan en una configuración **maestro-esclavo**, en la que la primera puede enviar comandos a la segunda. Esto permite alcanzar cierto grado de paralelismo, aunque ambas comparten el mismo bus externo [@cpu-dualcpu] (lo que puede provocar congestión).

Hitachi comercializó distintas variantes del SH-2 dentro de una familia llamada 'SH7600'. Todas ellas incorporan [@cpu-brief]:

- El ya mencionado **pipeline de cinco etapas** y la **SuperH ISA**. Esta última se ha ampliado con seis instrucciones adicionales para saltos y aritmética especializados.
- Una **unidad de multiplicación de 32 bits** mejorada, que ahora realiza multiplicaciones con enteros de 32 bits.
- Un **bus de datos externo de 32 bits** compartido entre las dos CPU.

El chip concreto seleccionado para esta consola, el 'SH7604', incorpora las siguientes características adicionales [@cpu-prog_manual]:

- **4 KB de caché**: almacena un pequeño número de instrucciones y datos leídos previamente de la memoria para acelerar las lecturas futuras.
- Una **unidad de división de 32 bits**: realiza divisiones con enteros de 32 bits.
- Un **controlador interno de Acceso Directo a Memoria (DMA, del inglés *Direct Memory Access*)**: transfiere datos desde la memoria sin intervención de la CPU.
- Compatibilidad con **little endian**, lo que permite a la CPU interpretar valores codificados en el orden inverso. Esto resulta útil cuando la memoria externa se comparte con otros procesadores.

Conviene señalar algo: **¡tener dos CPU no significa que los juegos vayan a funcionar el doble de rápido!** En la práctica, esto exige una programación muy compleja para gestionar eficientemente las CPU que comparten el mismo bus. Por ejemplo, los accesos a memoria deberían aprovechar la caché al máximo.

### La arquitectura del pulpo

![Vista general del modelo de múltiples subsistemas.](_diagrams/subsystems.png)

La Sega Saturn alberga un número considerable de procesadores (¡ocho!). Para evitar que compitan por los recursos, los ingenieros agruparon los circuitos en cuatro subsistemas diferenciados:

- El subsistema de **CPU**, donde residen las CPU principales y la memoria. A decir verdad, también hay un inusual *tercer* componente, que se explica más adelante.
- El subsistema de **vídeo**, que comprende los aceleradores gráficos.
- El subsistema de **audio**, que puede considerarse un ordenador independiente por derecho propio (lo veremos con más detalle en la sección 'Audio' de este análisis).
- El subsistema de **CD-ROM**, una fortaleza inexpugnable gracias a los mecanismos de protección anticopia implementados, que se explican en la sección 'Anti-piratería'.

Cabe señalar que cada subsistema está conectado a un bus dedicado, excepto los de vídeo y audio, que comparten uno.

### Una memoria dividida

![Vista general de la arquitectura de memoria principal.](_diagrams/memory.png)

El subsistema de CPU contiene un total de **2 MB de RAM** de uso general, denominada **Work RAM** (WRAM). Sin embargo, no es tan sencillo: esta memoria se divide en dos bloques diferenciados y, una vez más, cada uno es accesible a través de buses distintos:

- El primer bloque ofrece **1 MB de SDRAM**. Gracias a su mayor velocidad de acceso, este bloque también recibe el nombre de **WRAM-H**. Su bus se comparte con otros componentes.
- El otro megabyte se denomina **WRAM-L**, pues utiliza **DRAM**, lo que se traduce en tasas de transferencia inferiores. Su bus, no obstante, está reservado para las CPU principales.

### El tercer procesador (y contando)

Sorprendentemente, parece que las dos CPU SH-2 aún no eran suficientes para Sega. Así que, para acelerar el procesamiento vectorial (a costa de mayor complejidad), el grupo de CPU también alberga un coprocesador adicional: la **Saturn Control Unit** o 'SCU'.

![La Saturn Control Unit en mi revisión de placa base.](chips/scu.webp){latex_width="80%"}

Se trata de un chip independiente formado por dos módulos [@cpu-scu]:

- Un **controlador de Acceso Directo a Memoria (DMA)**: arbitra el acceso a la WRAM-L entre los tres subsistemas sin intervención de las CPU.
- Un **Procesador de Señal Digital (DSP, del inglés *Digital Signal Processor*)**: se utiliza como 'unidad de geometría' de punto fijo. Comparado con el SH-2, realiza cálculos de matrices y vectores —como transformaciones 3D e iluminación— con mayor rapidez. Sin embargo, funciona a la mitad de velocidad y su set de instrucciones es más complejo. Además, depende de la lenta WRAM-L del SH-2 para leer y almacenar datos (mediante el DMA).

Para paliar esto, la SCU cuenta con **32 KB de SRAM** de uso local.

## Gráficos

Dado que el Saturn es la primera 'consola 3D' que analizamos en [esta serie](consoles), repasemos primero los cambios fundamentales de diseño que allanaron el camino a la nueva generación de gráficos 3D.

### Metodologías revisadas

En primer lugar, la GPU trabaja ahora con un **frame buffer**: ya no es necesario renderizar los gráficos al vuelo. En su lugar, la GPU reserva una porción de VRAM para dibujar un bitmap con toda la geometría computada que le solicita la CPU. Un codificador de vídeo recoge luego esa región y la emite a través de la señal de vídeo. A decir verdad, el Saturn combina ambos modelos... esto lo explico más adelante.

En consecuencia, disponer de este 'espacio de trabajo' reservado permite a la GPU seguir manipulando el bitmap incluso después de renderizar la escena. Esto posibilita que la CPU le delegue tareas adicionales de gran carga computacional (como iluminación y antialiasing), y es aquí donde el concepto de **pipeline gráfico** empieza a cobrar protagonismo.

En segundo lugar, **se precisa más VRAM**: el uso de un frame buffer aumenta los requisitos de memoria (algo que ya no supone un problema grave). La cantidad de RAM necesaria para un frame buffer es proporcional a las dimensiones de la pantalla y al número de colores utilizados (a.k.a. *colour depth*). Por ejemplo, 600 KB de VRAM pueden albergar un frame buffer de 640×480 píxeles con 32k colores (16 bits por píxel).

Además, los programadores tienen libertad para organizar el uso de la VRAM: no todos los bits tienen que destinarse al frame buffer. ¿Por qué no usarla también para cachear texturas, renderizar otros frame buffers de forma simultánea y añadir tablas de consulta de color para agilizar las operaciones?

Por último, la CPU incorpora **operaciones vectoriales**: una GPU con capacidades 3D no estaría completa sin una CPU capaz de suministrarle la geometría necesaria. Por eso, las CPU de siguiente generación incorporan algún tipo de instrucciones especializadas que aceleran los cálculos vectoriales, conocidas como extensiones de **Una Instrucción, Múltiples Datos** (SIMD, del inglés *Single Instruction Multiple Data*). No obstante, su diseño dista mucho de estar estandarizado en la industria.

En el caso del Saturn, las operaciones vectoriales las acelera la Saturn Control Unit, no las CPU SH-2.

### La propuesta de Sega

Esta consola incluye **dos GPU propietarias**: la VDP1 y la VDP2. Cada una cumple una función distinta mientras opera de forma simultánea. Algunos podrían afirmar que las nuevas GPU son una evolución del [VDP clásico](mega-drive-genesis#graphics), mientras que otros consideran que se trata de un rediseño completo... Creo que es un poco de ambas cosas.

Dicho esto, veamos los dos chips.

#### VDP1 {.tabs .active}

![Arquitectura del VDP1.](_diagrams/vdp/vdp1.png){.tab-float}

El **Video Display Processor 1** (VDP1) es un chip que dibuja [sprites](mega-drive-genesis#tab-1-4-sprites) con **transformaciones geométricas** [@graphics-vdp1]. Los resultados se escriben en un frame buffer que, a continuación, se transmite al VDP2 para su visualización.

Este chip se programa mediante **comandos de dibujo**. Así, los programadores disponen de **512 KB de RAM dedicada** para almacenar estos comandos junto con los materiales necesarios para procesarlos (texturas/tiles, tablas de consulta de color, etc.).

En consecuencia, el VDP1 está diseñado para utilizar **cuadriláteros como primitivas**, lo que significa que solo puede componer modelos con polígonos de cuatro vértices (sprites). El chip aplica **Forward Texture Mapping** para proyectar los puntos de textura sobre el cuadrilátero en esa dirección. No incorpora técnicas de filtrado ni interpolación, por lo que el renderizado está sujeto a **aliasing**.

El VDP1 también ofrece estos efectos:

- Dos **algoritmos de sombreado** (plano y Gouraud) para la iluminación.
- **Antialiasing**: en este caso, duplica píxeles para cubrir los huecos que aparecen durante el texture mapping.
- **Clipping** para descartar polígonos fuera del campo de visión de la cámara.
- **Transparencia** para mezclar dos bitmaps no opacos.

Se dispone de **dos chips de frame buffer de 256 KB** para dibujar nuevas escenas del juego de forma simultánea sin interrumpir la que se está mostrando en ese momento. Cuando el buffer secundario termina de dibujarse, el VDP1 pasa a emitir ese y el ciclo se repite. Esta técnica se denomina **page flipping**.

#### VDP2 {.tab}

![Arquitectura del VDP2.](_diagrams/vdp/vdp2.png){.tab-float}

El **Video Display Processor 2** (VDP2) se especializa en renderizar planos de gran tamaño (hasta 4096×4096 píxeles) con transformaciones aplicadas (rotación, escala y traslación) [@graphics-vdp2].

Y lo más importante: el VDP2 renderiza **al vuelo** (sin frame buffer), de manera muy similar a los [motores basados en tiles](mega-drive-genesis#constructing-the-frame) anteriores. Admite hasta **16,7 millones de colores** (24 bits).

Este chip también se encarga de mostrar el buffer de salida del VDP1, que puede a su vez transformarse y mezclarse con las capas del VDP2. El 'fotograma' del VDP2 consta de hasta **cuatro planos 2D y un plano 3D, o bien solo dos planos 3D**.

Este chip recurre a [tile-maps](mega-drive-genesis#constructing-the-frame) para componer los planos y aplica **perspective correction** para el texture mapping 3D. Este es un enfoque más sofisticado que tiene en cuenta el valor de profundidad al calcular las rotaciones.

Los efectos disponibles incluyen:

- **Multitexturizado** para proyectar más de una textura sobre un único polígono.
- **Sombreado**: tras recibir los sprites generados por el VDP1, el VDP2 puede reducir su brillo y mezclarlos con semitransparencia.
  - No obstante, el VDP2 solo recibe un flujo de sprites del VDP1 (al ritmo del haz CRT), por lo que esta función tiende a ser complicada de codificar y gestionar.

Este chip también alberga **4 KB de Colour RAM (CRAM)**, que se utiliza para convertir los valores de color personalizados del VDP1 (colores indexados) en colores RGB de 24 bits.

Por último, aunque el VDP2 tiene un límite de dos planos 3D, nada impide a la CPU usar su VRAM como frame buffer por software para renderizar gráficos 2D o 3D adicionales.

Si esta sección ha despertado tu interés, te recomiendo consultar las fuentes al final del artículo: los VDP tienen muchas más particularidades interesantes que escapan al ámbito de este análisis.

### Definiendo el problema {.tabs-close}

Como puedes ver, la arquitectura del subsistema gráfico es bastante compleja, y su interpretación varía con frecuencia según las necesidades:

### Como una *potente* consola 2D

Las capacidades del Saturn para dibujar escenas en 2D eran vastas comparadas con las de la [Mega Drive](mega-drive-genesis) o la [SNES](super-nintendo), aunque no eran el principal argumento de venta de la consola.

Para una demostración rápida, usaré *Mega Man X4* (1997) como ejemplo.

#### Sprites {.tabs .active}

![El plano de sprites del VDP1.](2d/sprites.png){.tab-float .latex-framed}

En este caso, el VDP1 tiene la tarea de dibujar sprites tradicionales sin aplicar ninguna distorsión 3D.

La CPU configura el VDP1 escribiendo en sus registros y cargando la VRAM con comandos y tiles. El proceso también puede acelerarse mediante el controlador DMA.

#### Fondos {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2 #fig-vdp2_bgframes}

![Plano 2D 1.](2d/bg1.png){.active .border hardcover_latex_width="87%" paperback_latex_width="77%" title="2D plane 1"}

![Plano 2D 2.](2d/bg2.png){.border hardcover_latex_width="87%" paperback_latex_width="77%" title="2D plane 2"}

![Plano 2D 3.](2d/bg3.png){.border paperback_latex_width="80%" title="2D plane 3"}

Planos de fondo del VDP2.

:::

A continuación, el VDP2 recibe instrucciones para dibujar los planos de fondo. Estos, junto con la capa de sprites, se componen automáticamente para formar una escena con todo su color.

La parte de configuración es fundamentalmente similar a la del VDP1: los programadores tienen acceso a registros y VRAM para configurar el chip de la manera adecuada.

Ciertas funciones del VDP2 pueden aprovecharse para crear escenarios más realistas, como efectos de escala para simular la distorsión por calor, tal como se aprecia en el 'Plano 2D 2' [@fig-vdp2_bgframes].

#### Resultado {.tab}

![Planos mezclados (_¡Tachán!_).](2d/result.jpg){.tab-float .border}

Sin mucho misterio, el VDP2 se encarga del último paso: transmitir la señal procesada al codificador de vídeo.

El VDP2 opera en sincronía con el haz CRT, lo que significa que sus cálculos en curso corresponden a los píxeles que están a punto de aparecer en la siguiente línea de escaneo.

### Como una consola 3D *exigente* {.tabs-close}

Aquí es donde el Saturn *brilló y tuvo dificultades* a partes iguales. Aunque contaba con ocho procesadores para sacar partido, todo se reducía en última instancia a:

- Si los programadores serían capaces de dominar la mayor parte de las características de la consola en un plazo reducido (recordemos que la vida comercial de la consola acabaría en cuanto se lanzara su sucesora, o incluso cuando se anunciara).
- Si su juego podría salir en un plazo razonable.

Por eso, la calidad de los juegos variaba enormemente de un título a otro, con cada estudio desarrollando enfoques únicos.

#### Modelado 3D {.tabs .active}

![Modelos 3D de personajes sin texturas ni fondo. Observa las primitivas usadas para construir los modelos.<br>Virtua Fighter Remix (1995).](3d/models.png){.tab-float}

Hasta ahora, los ejemplos anteriores usaban cuadriláteros regulares individuales para formar sprites o capas de fondo. Pero ¿qué ocurre si agrupamos múltiples primitivas irregulares y las disponemos para formar una figura más compleja? Así es como cobran vida los modelos 3D.

En términos sencillos, las consolas 2D clásicas como la [Super Nintendo](super-nintendo) organizan sus gráficos (fondos y sprites) en áreas cuasirectangulares. En algunos casos, como con el [Modo 7](super-nintendo#that-feature), los programadores pueden aportar una matriz de rotación para aplicar transformaciones a algunas de esas áreas. El Saturn, en cambio, permite a los desarrolladores definir cuadriláteros de cuatro vértices con ángulos arbitrarios entre sus aristas; Sega los denomina **sprites distorsionados**. Luego, las capacidades de texture mapping de los VDP recubren el área del cuadrilátero con una textura que se escala para adaptarse a la forma del polígono.

En cuanto a las operaciones requeridas en un juego 3D, las CPU y la SCU tienen la tarea de formular un mundo 3D y proyectarlo en un espacio 2D. Después, ambos VDP reciben instrucciones para renderizarlo, aplicar efectos y, finalmente, emitirlo al televisor.

#### Procesado de píxeles {.tab}

![Escena renderizada con modelos 3D y fondos.<br>Virtua Fighter Remix (1995).](3d/complete.png){.tab-float}

Cualquiera de los VDP puede dibujar el nuevo espacio 3D proyectado y aplicar texturas y efectos. Sin embargo, cuál de los dos está 'al mando' varía de un juego a otro.

Algunos desarrolladores priorizaban el VDP1 para renderizar los polígonos cercanos, dejando al VDP2 que gestionase el escenario lejano. Otros idearon ingeniosas soluciones alternativas que permitían al VDP2 dibujar polígonos más próximos (reduciendo así la cantidad de geometría enviada al VDP1). El desafío central reside en diseñar un motor eficiente capaz de mostrar gráficos *impresionantes* manteniendo a la vez una tasa de fotogramas aceptable.

### Los nuevos diseños {.tabs-close}

Estos son algunos ejemplos de personajes rediseñados específicamente para esta consola. Inicialmente construí un visor de modelos interactivo para la edición web y, posteriormente, adapté las visualizaciones para los formatos físicos.

![Sonic en Sonic R (1997).<br>185 cuadriláteros.](sonic_r_saturn){.toleft model3d="true" paperback_latex_width="93%"}

![Tails en Sonic R (1997).<br>254 cuadriláteros.](tails_r_saturn){.toright model3d="true" hardcover_latex_width="91%" paperback_latex_width="82%"}

Aunque el Saturn solo puede dibujar cuadrangulares, la vista Wireframe muestra dos triángulos en lugar de un único cuadrangular. Esto se debe a que el formato moderno utilizado para codificar este modelo —glTF, un estándar abierto para el modelado 3D contemporáneo que permite a los dispositivos actuales seguir renderizándolo— no admite cuadrangulares en el momento de redactar este artículo. Este comportamiento no se aprecia en la vista de superficie.

En cierto modo, esto pone de manifiesto lo difícil que puede resultar para la tecnología gráfica moderna emular fielmente a sus predecesoras de hace ~30 años.

### Una introducción al problema de visibilidad

Cuando los polígonos 3D se proyectan en un espacio 2D, es fundamental determinar **qué polígonos son visibles desde la perspectiva de la cámara y cuáles quedan ocultos detrás** [@graphics-vsd]. De lo contrario, los modelos no se dibujan correctamente, efectos como la transparencia aparecen rotos y los recursos de hardware se malgastan.

Este proceso se conoce ampliamente como **Determinación de Superficie Visible** (VSD, del inglés *Visible Surface Determination*), y es un reto fundamental en el campo de la informática gráfica. Numerosas publicaciones describen algoritmos que abordan este problema en distintas etapas del pipeline gráfico; algunos ofrecen resultados de gran precisión, mientras que otros sacrifican exactitud en aras de un mejor rendimiento.

Ahora bien, a diferencia del equipamiento académico o profesional, el hardware doméstico está muy limitado, lo que significa que la elección de algoritmo a menudo se reduce a muy pocas opciones... o ninguna en absoluto.

![Project Z-Treme [@graphics-ztreme], un motor homebrew de 2019. Abandonó el Z-sort en favor de un enfoque de Particionado de Espacio Binario (BSP, del inglés *Binary Space Partitioning*), corrigiendo numerosos fallos visuales.](projectz.jpg){.open-float}

El enfoque del Sega Saturn es lo que yo consideraría un caso *semirresuelto*. El VDP1 no implementa ninguna funcionalidad VSD: o bien se suministra la geometría en el orden correcto, o el resultado es un caos. Sin embargo, Sega proporcionó una biblioteca de gráficos llamada 'SGL' que implementa una solución denominada **Z-sort** o **algoritmo del pintor** [@graphics-sgl]. Esta realiza el **ordenamiento de polígonos por software**.

En esencia, SGL reserva un buffer para ordenar los polígonos según su distancia a la cámara (del más lejano al más cercano). Después, emite los comandos de dibujo correspondientes al VDP1 en ese orden.

{.close-float}

Una de las limitaciones del Z-sort en entornos 3D es que su valor de distancia (orden Z) es solo **aproximado**, lo que significa que pueden seguir apareciendo fallos visuales. Por ello, los programadores pueden prescindir de SGL e implementar sus propios algoritmos.

En artículos posteriores verás enfoques alternativos para la determinación de superficie visible. Algunos siguen dependiendo del software, mientras que otros se benefician de la aceleración por hardware.

### El problema de la transparencia

Como parte de su propio conjunto de [efectos de color](super-nintendo#more-colour-magic), el Sega Saturn es capaz de renderizar **gráficos semitransparentes**, es decir, mezclar capas de colores superpuestas para dar la ilusión de que podemos ver a través de ellas (también conocida como translucidez). Sin embargo, esta funcionalidad presenta importantes limitaciones [@graphics-vdp1]:

- Solo el VDP2 puede mezclar píxeles con semitransparencia, mientras que el VDP1 únicamente genera un buffer renderizado sin distinción entre sprites superpuestos. En consecuencia, los sprites semitransparentes superpuestos ocluyen a los que están por debajo.
- El proceso de Forward Texture Mapping del VDP1 corrompe la semitransparencia cuando se aplica a sprites distorsionados (es decir, polígonos).
- Los píxeles semitransparentes tardan seis veces más en dibujarse.

Como solución alternativa, los juegos 2D pueden activar la propiedad 'mesh' en una textura. Con las texturas 'mesh', el VDP1 establece las coordenadas de textura X/Y impares como 'totalmente transparentes' (es decir, vacías), lo que permite que las capas inferiores sean visibles. Curiosamente, cuando la consola está conectada al televisor mediante la señal de vídeo compuesto (un estándar en la época, junto al RF), el patrón mesh aparece difuminado. Esta consecuencia accidental pero eficaz proporcionó una forma de simular la semitransparencia [@graphics-geer].

No obstante, las partes opacas de un mesh seguirían ocultando otros sprites. Además, esto no resolvía el problema para los juegos 3D. Al final, algunos títulos no tuvieron más remedio que prescindir por completo de la semitransparencia... aunque ciertos estudios encontraron soluciones ingeniosas. Veamos dos casos [@fig-sat_transparency].

::: {.subfigures .side-by-side max_subfigures=1}

![Daytona de Sega (1993).](daytona){.toleft video="true" #fig-sat_transparency}

![Sonic R de Traveller's Tales (1997).](sonicr){.toright video="true"}

Ejemplo de juegos que abordan la semitransparencia de distintas maneras.

:::

En ambos ejemplos, el juego ordena al VDP1 dibujar los objetos del primer plano y el escenario de fondo. A su vez, el VDP2 renderiza las imágenes del paisaje lejano y superpone las estadísticas del HUD sobre los modelos 3D. En consecuencia, los modelos 3D del VDP1 (renderizados como sprites distorsionados) no pueden emplear semitransparencia, lo que les impide mezclarse de forma natural con las capas del VDP2.

Sin embargo, los dos juegos exhiben comportamientos distintos. Durante el gameplay, el fondo de *Daytona* aparece de golpe (ya que la semitransparencia está desactivada). En cambio, *Sonic R* logra no solo semitransparencia sino también un **efecto de fundido**. Traveller's Tales ideó una solución: ajustó los registros de 'relación de mezcla' del VDP2 (que definen el alfa de la textura) y modificó manualmente los niveles de iluminación a medida que el personaje se acerca [@graphics-burton].

## Audio

Las capacidades de audio de esta consola formaban parte de una revolución digital más amplia que tenía lugar en el mismo período. En esencia, la combinación de nuevos formatos de almacenamiento y sintetizadores de muestras asequibles (aunque sofisticados) permitió por fin a los compositores producir su música en casa e incorporarla directamente a los juegos en su forma original.

### El stack tecnológico

Sea como fuere, bajo la superficie de una funcionalidad atractiva se esconde una arquitectura compleja. En el caso del Sega Saturn, su subsistema de sonido se compone de varias partes [@audio-scsp]:

- El **Saturn Custom Sound Processor** (SCSP): también conocido como Yamaha YMF292, consta de dos módulos:
  - Un **generador de sonido multifunción**: procesa hasta **32 canales** con **muestras PCM** (hasta 16 bits a 44,1 kHz, calidad CD) o [**canales FM**](mega-drive-genesis#audio). En el caso de estos últimos, un subconjunto de canales está reservado para los operadores.
  - Un **Procesador de Señal Digital** (DSP): aplica efectos de audio como eco, reverberación y coro. La documentación también menciona 'filtros', aunque no queda claro si se refiere a filtros de envolvente o de frecuencia (p. ej., paso bajo).
- Un **Motorola 68EC000**: controla los componentes de audio e interactúa con las CPU principales. Ejecuta un [driver de sonido](mega-drive-genesis#the-conductor) para operar los módulos adyacentes.
  - Si esta CPU te resulta familiar, es porque lo es: la Mega Drive llevaba un 68000 como [CPU principal](mega-drive-genesis#cpu), mientras que el 68EC000 del Saturn es una pequeña mejora diseñada para aplicaciones embebidas. Este funciona a 11,3 MHz y está conectado mediante un bus de 16 bits [@cpu-overview].
- **512 KB de RAM**: se utiliza para almacenar el driver de sonido y los datos de audio (p. ej., muestras PCM). También sirve como área de trabajo para el DSP.

### La oportunidad

Como se mencionó anteriormente, el nuevo subsistema de audio permitió por fin a los estudios grabar y producir bandas sonoras en casa para incluirlas en el juego sin necesidad de reorquestarlas. A diferencia de los sistemas anteriores, estos dependían de [secuenciadores](super-nintendo#audio) con limitaciones o de hardware de sonido sujeto a estrictos [métodos de síntesis](mega-drive-genesis#audio).

Este cambio fue posible gracias a una combinación de factores clave:

- La adopción del CD-ROM como soporte de almacenamiento permitió a los desarrolladores incluir bandas sonoras extensas y de alta calidad.
- La salida de audio podía recibir y mezclar datos PCM con una fidelidad aceptable.
- El subsistema de audio ofrecía potencia y ancho de banda suficientes para transmitir datos PCM comprimidos y decodificarlos en tiempo real.

## Sistema Operativo

Al encender la consola, el primer componente que arranca es el **System Management and Peripheral Control** (SMPC), un microcontrolador de 4 bits que se encarga de inicializar los chips cercanos, como activar los dos SH-2 y configurarlos en modo 'maestro-esclavo' [@games-smpc].

![El chip SMPC en mi revisión de placa base.](chips/smpc.webp){latex_width="90%"}

A continuación, el vector de reset del SH-2 maestro se establece en `0x00000000` [@games-sh2], que apunta a un chip ROM de 512 KB que contiene el **Initial Program Loader** (IPL).

::: {.subfigures .side-by-side}

![Versión japonesa.](ipl/logo_jap.jpg){.toleft hardcover_latex_width="95%" paperback_latex_width="84%"}

![Versiones europea y americana.](ipl/logo_eu.jpg){.toright hardcover_latex_width="95%" paperback_latex_width="84%"}

Logotipo mostrado al finalizar la animación de presentación.

:::

El IPL lleva a cabo las siguientes funciones como parte de la secuencia de arranque [@operating_system-bootrom]:

1. Finalizar la inicialización del hardware.
2. Si hay un cartucho insertado que contiene código ejecutable, continuar el arranque desde él.
4. Si hay una tarjeta 'Video CD' insertada, arrancar desde ella.
3. Si hay un disco insertado, verificar que es auténtico.
    - Mientras tanto, el sistema muestra la animación de la pantalla de presentación.
4. Si el disco es auténtico, arrancar el juego.
4. Si el disco no es auténtico o no hay ningún disco insertado, iniciar el shell interactivo.

### Shell interactivo

Como alternativa a jugar a los juegos, el Saturn incluía un reproductor de música integrado llamado 'Multiplayer', desde el que los usuarios también podían acceder a un gestor de datos guardados.

![Shell interactivo llamado 'Multiplayer' o 'Audio CD Control Panel'.](ipl/multiplayer.jpg){.toleft}

![Gestor de memoria. Mueve las partidas guardadas entre el cartucho y la memoria interna.](ipl/mem_manager.jpg){.toright}

Si había una tarjeta Video CD insertada, Multiplayer también podía reproducir vídeo MPEG decodificado desde la propia tarjeta.

### ¿Sin BIOS?

A diferencia de la [PlayStation](playstation), cuyo chip ROM incluye una [BIOS](playstation#operating-system) que expone APIs para que los programadores las utilicen, la ROM del Saturn suele denominarse 'IPL', presumiblemente porque su función principal es inicializar el sistema, arrancar el juego y ejecutar el shell.

Sin embargo, la ROM del IPL también almacena algunas rutinas (llamadas **servicios**) para manipular el hardware, como la gestión de datos guardados y el control de energía. Incluso implementa un mecanismo de semáforo para sincronizar operaciones entre múltiples procesadores de forma simultánea. Por eso, esa parte de la ROM se denomina **System program**.

## Juegos

Los juegos oficiales de Sega Saturn se cargan desde la **unidad de CD-ROM de 2x**. Su soporte, una variante adaptada del **Compact Disc** (CD), tiene una capacidad de **650 MB** [@anti_piracy-rings] y se adhiere al estándar **ISO 9660** para el almacenamiento de datos [@games-format]. Además, muchos juegos incluyen pistas de audio junto a las pistas de datos para transmitir audio sin comprimir durante el gameplay.

### El Compact Disc (CD)

El CD es un soporte óptico en el que la información se almacena grabando **hoyos** (*pits*) y **tierras** (*lands*) en su superficie de policarbonato [@cpu-optical]. Un haz infrarrojo se dirige desde la unidad; la reflexión producida en la superficie del CD se utiliza para recuperar la información.

El proceso de convertir información digital (unos y ceros) en pits y lands, y viceversa, no es, ni mucho menos, sencillo: los CD deben ser lo bastante robustos para soportar el uso cotidiano y el desgaste intensivo, y lo bastante fiables para almacenar cualquier tipo de información sin riesgo de pérdida de datos. Por ello, y como parte de su especificación, los datos se codifican mediante el esquema **Non-Return-to-Zero inverted** (NRZi). Esto significa que el flujo de bits está formado por ceros hasta que se produce una transición de land a pit o de pit a land, momento en que se añade un `1` en su lugar.

Este diseño funciona bien hasta que el lector encuentra una secuencia de unos (cambios continuos de pit y land) o largas secuencias de ceros (pits o lands constantes), situaciones en las que el sensor tendrá dificultades para detectar la señal o para mantenerse sincronizado, respectivamente. Para solucionarlo, se aplica un modelo adicional llamado modulación **Eight-to-Fourteen** (ETF). Este inserta un pequeño número de ceros entre las codificaciones, facilitando así la lectura por parte del sensor.

Más allá de esto, pueden añadirse mecanismos adicionales para la detección de errores, aunque estos quedan fuera del ámbito de este artículo. Si quieres explorar más, consulta una interesante presentación de diapositivas elaborada por la Universidad RWTH de Aquisgrán [@cpu-optical].

### Desarrollo

En un principio, Sega no proporcionó bibliotecas de software ni herramientas de desarrollo completas (de hecho, la documentación inicial era inexacta), por lo que la única forma de lograr un buen rendimiento era mediante ensamblador *a las bravas*.

Con el tiempo, Sega lanzó SDK completos, kits de hardware y bibliotecas adicionales para facilitar las operaciones de E/S y gráficos. En general, los juegos se escribían combinando **C** y varios lenguajes ensamblador orientados a componentes individuales.

### E/S

La gestión de periféricos y el **Reloj en Tiempo Real** (RTC, del inglés *Real-Time Clock*) también corren a cargo del ya mencionado **System Management and Peripheral Control** (SMPC), que se controla mediante comandos enviados por los SH-2.

### Métodos de expansión

Esta consola incluye un número considerable de conectores e interfaces externos, la mayoría de los cuales solo tuvieron un puñado de usos:

- Detrás de la unidad hay una **ranura de cartucho**, destinada oficialmente a **almacenamiento adicional** (datos guardados) o **RAM extra**. En Japón y Estados Unidos también se ofrecía un módem para proporcionar [conectividad en línea](mega-drive-genesis#early-network-attempts).
- En la parte trasera de la consola hay una ranura para una **tarjeta Video CD**, que realiza la descompresión MPEG para programas o juegos que la admitan.
- Por último, también en la parte trasera hay un misterioso conector llamado **Communication Connector**. Aunque Sega no publicó documentación de desarrollo al respecto, los esfuerzos de ingeniería inversa revelaron que conecta con los pines MIDI del SCSP y con la Interfaz Serie (SCI) de los dos SH-2 [@games-development]. En cualquier caso, Sega lanzó una unidad de disquete que hacía uso de esta interfaz.

## Anti-piratería y Homebrew

En respuesta a la facilidad de clonar un CD, Sega implementó un sistema de protección anticopia —junto con un bloqueo regional— para controlar la distribución de sus juegos.

Para la protección anticopia en sí, Sega se apartó deliberadamente del formato CD estándar, haciendo imposible que las grabadoras de CD convencionales produjesen una copia perfecta de un juego de Saturn. Luego, como parte de su proceso de verificación, el lector de discos del Saturn busca estas características no estándar.

Para ser más precisos, los discos de Saturn se fabricaban con un patrón de datos inusual grabado en el borde exterior [@anti_piracy-rings], creando un anillo visible con etiquetas de marca registrada. Este anillo se sitúa fuera del área de datos estándar de un CD (conocida como 'Área de Programa') y más allá de la sección Lead-out, que señala el final de los datos legibles. En consecuencia, las unidades convencionales no podían acceder ni replicar esa parte del disco.

Dentro de la unidad de disco del Saturn hay un **procesador SH-1** dedicado que verifica la presencia del anillo al margen de las CPU principales. Utiliza protocolos de comunicación poco conocidos para comunicarse con el resto del sistema. No obstante, esta comprobación solo se realiza una vez.

### La derrota

Para empezar, el método clásico para deshabilitar la protección anticopia consistía en instalar un **mod chip**, que podía engañar al lector de CD cuando se insertaba cualquier tipo de disco. Otra técnica, conocida como 'swap trick', consistía en **intercambiar en caliente** un disco original por uno grabado inmediatamente después de que las comprobaciones de protección se hubieran validado... con el riesgo de dañar la unidad.

![Mi Saturn con el chip Fenrir instalado. Es un emulador de unidad óptica (ODE, del inglés *Optical Drive Emulator*) que sustituye la unidad de CD original para ejecutar juegos desde una tarjeta SD. Este es uno de los muchos métodos modernos utilizados para ejecutar Homebrew.](fenrir.webp)

Con el cambio de siglo, se descubrieron métodos más sofisticados para ejecutar código no autorizado, por ejemplo:

- Se identificó un **exploit en el mecanismo de protección anticopia** que permitía arrancar cualquier juego en disco sin pasar por las comprobaciones de protección. Este se implementó posteriormente en forma de cartucho llamado **PseudoSaturn** [@anti_piracy-pseudosaturn]. Como dependía del cartucho como soporte, los cartuchos Action Replay solían regrabarse con PseudoSaturn (aunque el programa de grabación necesitaba arrancarse de alguna forma; lo más habitual era mediante el swap trick).
  - Este método sigue en uso a fecha de 2022, aunque actualmente se instala una versión más reciente llamada 'Pseudo Saturn Kai'.
- En 2016 surgió otro método (casi 20 años después del lanzamiento de la consola) que aprovechaba que **el complemento Video CD puede inyectar código sin cifrar** en el subsistema de CD, evitando por completo la unidad de CD. Esto permitió por fin a los usuarios cargar Homebrew al margen de la envejecida unidad. El exploit de Video CD se distribuye comercialmente como un producto llamado 'Satiator' (no estoy patrocinado, por cierto).
- Por último, existe una alternativa comercial que sustituye el lector de CD por un adaptador SD o SATA. El Saturn sigue creyendo que lee de un CD, pero el 'CD' está siendo emulado por el adaptador, que a su vez lee de una imagen de disco [@anti_piracy-ode]. Estos productos se conocen como **emuladores de unidad óptica** (ODE, del inglés *Optical Drive Emulator*).

## ¡Esto es todo, amigos!

![Un Saturn japonés que adquirí para conseguir más material para este artículo. Aunque los juegos se ven bien, fue gracias a la enorme biblioteca Homebrew del Saturn que pude entender las verdaderas capacidades de esta consola.](mine.jpg)
