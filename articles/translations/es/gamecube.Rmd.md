---
short_title: Arquitectura de la GameCube
long_title: Arquitectura de la GameCube
name: GameCube
release_date: 2001-09-14
date: 2019-11-19
subtitle: Ingeniería elocuente
generation: 6
javascript:
  - 'threejs'
  - 'audioswitcher'
cover: gamecube
top_tabs:
  Model:
    file: international
    caption: "La extraordinaria GameCube.<br>Lanzada el 14/09/2001 en Japón, 18/11/2001 en América y 03/05/2002 en Europa."
  Motherboard:
    caption: "Tomado de mi modelo 'DOL-CPU-10', modelos posteriores eliminaron el Puerto Serie 2 y la Salida Digital. El chip codificador, la expansión, los slots del control y la PSU se encuentran en el otro lado."
    bib_source: copetti
  Diagram:
    caption: "Cada bus de datos está etiquetado con su ancho."
#Historical
aliases:
  - /projects/consoles/gamecube/
---

## Una breve introducción

Se acabaron los días de 'intentos 3D', la nueva oferta de Nintendo consiste en una ruptura limpia y poderosa con su predecesor que abrirá la puerta a contenido nuevo, original e inédito.

Vale la pena señalar que el diseño de esta arquitectura llevó a uno de los hardwares más compactos de esta generación. Esto fue enfatizado por la falta de revisiones *slim* o *lite*.

## {.supporting-imagery}

## CPU

Después de la pérdida del dominio de SGI en el mercado gráfico, Nintendo necesitaba nuevos aliados con los que asociarse.

![Construcción del Gekko.](cpu/cpu_features.png) {.open-float.no-borders}

Un candidato prometedor parece ser IBM: Aparte de su famoso trabajo en mainframes, recientemente se aliaron con Motorola y Apple para crear una CPU lo suficientemente potente como para competir con el dominio de Intel en el mercado de PC. El producto resultante es una serie de procesadores que llevan el nombre **PowerPC**, que se seleccionaron para *impulsar* el 99% de los Macintoshes de Apple y algunos sistemas integrados.

Saltando al futuro, Nintendo necesitaba algo potente pero barato, así que para cumplir con esas líneas rojas, IBM tomó uno de sus diseños anteriores, el *PowerPC 750CXe* (encontrado en el último iMac G3, conocido como el *Early-Summer 2001*), y lo reforzó con capacidades que agradarían a los desarrolladores de juegos. El resultado fue el **PowerPC Gekko** y corre a **486 MHz**.

{.close-float}

### Características

Vamos a ver qué hace que el Gekko sea tan especial, y para hacer eso necesitamos primero tenemos que ver las funcionalidades del 750CXe [@cpu-750cxe]:

- **ISA de PowerPC**: Sin entrar en muchos detalles, es *otra* arquitectura de conjunto de instrucciones RISC de 32 bits. El 750CXe implementa la especificación v1.10.
- **Bus de datos de 64 bits**: Aunque la ISA puede caber en un bus de 32 bits, todavía necesitamos mover trozos más grandes de datos (explicado en la siguiente sección) sin sufrir penalizaciones de rendimiento.
- **Superscalar de doble emisión**: Si las unidades requeridas están disponibles, la CPU puede procesar hasta dos instrucciones en la misma etapa del pipeline. En el caso de que la cola incluya una instrucción de bifurcación, el número de instrucciones concurrentes posibles se eleva a **tres**.
- **Ejecución fuera de orden**: La CPU puede reordenar la secuencia de instrucciones para mantener todas sus unidades trabajando, aumentando así la eficiencia y el rendimiento.
- **Unidades de Dos Enteros**: Combinadas con el modelo superscalar y fuera de orden, incrementa el número de operaciones de enteros realizadas por unidad de tiempo.
- **FPU integrada** con registros de 32 bits y 64 bits: Acelera las operaciones con floats y doubles.
- **Pipeline de cuatro etapas (con bonificación)**: [Aquí](game-boy-advance#cpu) hay una introducción anterior al pipeline de instrucciones. En el 750CXe, las operaciones FPU se dividen en tres etapas más (7 etapas en total), mientras las operaciones de carga-almacenamiento se dividen en dos (5 etapas en total).
  - En conjunto, esto incrementa el rendimiento de las instrucciones sin [descontrolarse](xbox#tab-1-3-the-microarchitecture).

Adicionalmente, esta CPU también incluye unidades dedicadas para acelerar ciertos cálculos específicos [@cpu-stokes]:

- **Unidad de predicción de bifurcaciones**: Siempre que hay una condición que necesita ser evaluada (lo que decidirá si la CPU debe seguir el camino 'A' o el camino 'B'), la CPU seguirá uno de los caminos basado en ejecuciones anteriores y luego evaluará la condición. Si la predicción resulta ser correcta, la CPU habrá ahorrado algo de tiempo, si no, revertirá y seguirá el camino correcto.
- **Unidad de carga-almacenamiento dedicada**: Separa las unidades que manipulan registros y las que manejan la memoria principal.
- **Unidad de gestión de memoria integrada**: Interfa cualquier acceso a la memoria desde la CPU.
  - Las consolas anteriores incluían este componente como otro coprocesador, la MMU ahora se encuentra dentro del mismo chip, reduciendo los costos de fabricación.

Y, por supuesto, también se incluye algo de caché para acelerar el ancho de banda de la memoria:

- **64 KB de caché L1**: Dividido en 32 KB para instrucciones y 32 KB para datos.
- **256 KB de caché L2**: Se puede llenar con instrucciones y datos, lo que mejora enormemente el ancho de banda.

### Mejoras de IBM

Mientras que las características previas son muy apreciadas (comparadas con las generaciones anteriores), esta CPU aún queda por detrás en el rendimiento de juegos, comparada con otras CPU (no olvidemos que esta sigue siendo una CPU de uso general, buena en hojas de cálculo pero *mediana* con la física). Para compensar, IBM agregó los siguientes ajustes que constituirán el Gekko [@cpu-ibm]:

- Conjunto de instrucciones mejoradas con **50 nuevas instrucciones SIMD**: Estas operan dos números en punto flotante de 32 bits o un número en punto flotante de 64 bits usando un solo ciclo. En consecuencia, las nuevas instrucciones SIMD acelerarán los cálculos vectoriales, particularmente útiles durante las transformaciones geométricas.
  - No confundir con la extensión SIMD de Motorola (AltiVec) que se enviaba en los G4 Macs de gama alta.
- **32 registros en punto flotante**: Estos vienen con las nuevas instrucciones SIMD.
- **Tubería de recolección de escritura**: Un mecanismo especial de escritura de memoria disponible para usar. Si está habilitado, en lugar de realizar transferencias *single-beat*, retiene todas las solicitudes de escritura de memoria en un búfer de 128 bytes hasta que esté al 25% lleno, luego realiza las escrituras solicitadas utilizando una técnica llamada *transacción ráfaga* que puede mover bloques de 32 bytes de datos a la vez.
  - Como puedes imaginar, esto ahorra mucho ancho de banda al utilizar completamente los buses disponibles.
- **Caché L1 bloqueada**: Los programas pueden quitar 16 KB de caché de datos L1 para usar como 'scratchpad' (memoria increíblemente rápida).

Además de manejar la lógica del juego (física, colisiones, etc.), estas mejoras permitirán a la CPU implementar partes del pipeline gráfico (transformaciones de geometría, iluminación, etc.) con un rendimiento aceptable. Esto es muy importante ya que la GPU solo puede acelerar un conjunto limitado de operaciones, por lo que el resultado final no está condicionado por las limitaciones de la GPU.

### ¿Un paso adelante o un paso atrás?

> En tu [artículo de la Nintendo 64](nintendo-64), explicaste que el sistema tiene una CPU de 64 bits, pero la de GameCube es de 32 bits. ¿Acaso Nintendo degradó su consola?

Es cierto que Gekko implementa una especificación PowerPC de 32 bits, mientras que el MIPS R4300i puede alternar entre modos de 32 y 64 bits. Para responder si esto es una mejora o no, debes preguntarte: ¿Por qué necesitarías '64 bits'?

- Para direccionar más de 4 GB de memoria → La GameCube no tiene cerca de esa cantidad de ubicaciones de memoria, por lo que esto no es un requisito.
- Para operar trozos más grandes de datos usando menos ciclos y ancho de banda → Eso está cubierto por las nuevas instrucciones SIMD de Gekko, la FPU y el bus de datos de 64 bits; y el write-gather pipe.
- Para promocionar más funcionalidades → Ehh... No creo que eso persuade a la gente hoy en día.

Como pueden ver, la GameCube ya disfruta de las ventajas de los sistemas de 64 bits sin que fuera una 'consola de 64 bits'. Y por ende, no se puede resumir estas dos máquinas complejas por su 'número de bits'.

### Sistema de memoria inteligente

Durante el diseño de la arquitectura de próxima generación, los arquitectos de Nintendo realizaron un análisis postmortem de [su diseño anterior](nintendo-64) y descubrieron que el uso de una arquitectura de memoria unificada junto con algunos componentes de alta latencia (RDRAM) resultó en una de las mayores causas de cuellos de botella (casi el 50% de los ciclos de la CPU se desperdiciaron durante el tiempo de inactividad) [@cpu-gdc]. Además, la inclusión de múltiples unidades independientes contribuyó a una competencia preocupante por el bus de memoria.

Por esa razón, los arquitectos de GameCube idearon un nuevo sistema de memoria estrictamente basado en **proporcionar espacio de memoria dedicado** y **usar chips de baja latencia**. Con el nuevo diseño, la GPU y la CPU ya no competirán por la misma RAM (causando problemas de fill-rate) ya que la GPU ahora tendrá su propia memoria interna y *increíblemente* rápida. Por otro lado, la GPU aún estará a cargo de arbitrar el acceso a E/S también.

![Organización de la memoria en este sistema.](cpu/memory.png)

El resultado fue un sistema organizado con dos buses principales:

- El **Northbridge**: Tiene un ancho de 64 bits y conecta la CPU con la GPU. Funciona 3 veces más lento que el reloj de la CPU, por lo que las tareas deberán estar optimizadas para no depender tanto de la GPU. Otros componentes como el DMA y la caché pueden ser útiles.
- El **Southbridge**: También tiene 64 bits de ancho y conecta la GPU con **24 MB de 1T-SRAM** llamada 'Splash'. Este tipo de RAM está hecho de DRAM (que es el tipo más popular pero también más barato y lento) sin embargo es mejorado con circuitos adicionales para **comportarse como SRAM** (más rápido y caro, utilizado para el caché). El bus es **dos veces más rápido que la GPU**, posiblemente para permitir que la GPU proporcione un ancho de banda constante entre la CPU y la memoria principal.

Además, este diseño contiene un bus adicional (aunque inusual) donde se puede encontrar más memoria:

- El **Eastbridge**: Conecta la GPU con otro chip de memoria llamado **RAM de audio** o 'ARAM' [@cpu-hitmen]. Proporciona **16 MB de SRAM** para uso general, lo cual es bastante considerando que se trata de memoria *adicional*. Sin embargo, el bus no es accesible mediante medios normales (direcciones de memoria). En su lugar, está conectado a un punto final serie de 8 bits (sincronizado a una velocidad dos veces más lenta que la GPU y cuatro veces más lenta que la CPU), que solo es accesible a través de DMA.

En general, esto significa que, aunque ARAM proporciona una cantidad considerable de RAM, estará limitado a tareas menos críticas, como actuar como un búfer de audio o ser utilizado por ciertos accesorios (explicado en la sección de E/S).

### Organizando la memoria y gestionando ARAM

Hasta ahora, hemos visto que, en teoría, las capacidades de memoria son indudablemente superiores a su predecesor, pero aún hay margen de mejora. Por ejemplo, Nintendo pudo haber incorporado más hardware para integrar ARAM en el mapa de memoria de la CPU.

En relación con esto, vamos a revisar el MMU utilizado en Gekko. La CPU, con su bus de direcciones de 32 bits, puede acceder hasta 4 GB de memoria, pero el sistema no alberga esa cantidad. Por lo tanto, para evitar exponer direcciones de memoria no pobladas (e impredecibles), la dirección de 'memoria virtual' se activa por defecto para enmascarar las direcciones físicas con un mapa de direcciones 'virtual' más seguro, fácilmente cacheable y continuo [@cpu-dolphin_final].

Para que esto funcione, Gekko (y otras arquitecturas PowerPC) traduce direcciones virtuales a físicas con el siguiente proceso:

1. Realiza **Traducción de direcciones de bloque** (BAT): Hay ocho pares de registros programables (cuatro para datos y cuatro para instrucciones) donde cada par mapea un rango de direcciones virtuales a un rango continuo de direcciones físicas. La MMU intenta encontrar la dirección física si se encuentra dentro de esos rangos.
2. Si BAT no funcionó, lee la **Tabla de páginas**: La MMU también almacena una tabla que cataloga la ubicación física de las páginas (bloque de direcciones virtuales).
    - La MMU puede tardar en leer una tabla de páginas, por lo que se incluye un **búfer de traducción adelantada** (TLB) para almacenar en caché lecturas recientes.
    - Otras arquitecturas como x86 o MIPS también proporcionan paginación, aunque no todas ofrecerán un TLB.
3. Finalmente, si la dirección virtual solicitada aún no se puede traducir, la MMU desencadena una excepción de 'falla de página' en la CPU y deja que el sistema operativo decida qué hacer a continuación.

Entonces, ¿qué utilidad tiene esto para los desarrolladores? Bueno, resulta que Nintendo lanzó algunas bibliotecas que **extienden la RAM principal usando ARAM** con la ayuda de la paginación. Para resumir, ARAM no es direccionable, pero la CPU puede llamar a DMA para obtener y almacenar datos allí. Por lo tanto, la CPU puede mover páginas de la RAM principal para hacer espacio para otros recursos y almacenarlos temporalmente en ARAM. Después de eso, cada vez que se produce un fallo de página, el sistema operativo contiene rutinas para buscar las páginas faltantes en ARAM y restaurarlas a su ubicación original en la RAM principal.

En conclusión, con algunos trucos ingeniosos, estas capacidades de propósito general permitieron a los juegos de GameCube disfrutar de más memoria de la permitida técnicamente, alcanzando así niveles de calidad superiores. Aunque es importante tener en cuenta que tales trucos pueden venir con algunas sanciones de rendimiento (especialmente si se dan por sentadas).

## Gráficos

Esta es una de las secciones más críticas de esta consola, básicamente hace que la GameCube sea una *GameCube*.

La historia de la GPU de esta consola tiene algunas conexiones interesantes: Wei Yen, el director del SoC del N64 ([el RCP](nintendo-64#graphics)), luego fundó ArtX y consiguió un contrato con Nintendo para desarrollar su chip de próxima generación: **Flipper**.

![Super Mario Sunshine (2002).](sunshine.png) {.open-float}

Hubo muchos avances realizados desde la iteración anterior, por ejemplo, el subsistema se simplificó severamente a un núcleo único (pero poderoso).

Durante el proceso de desarrollo, ArtX fue adquirido por ATI, que a su vez fue vendido a AMD seis años después. Por lo tanto, es por eso que ves una pegatina de ATI estampada en la parte frontal de la caja.

{.close-float}

### Arquitectura y diseño

Flipper es un bloque complejo que maneja múltiples servicios [@graphics-cheng], así que enfoquémonos en el componente gráfico por ahora (ya que es el responsable de dar vida a nuestra geometría). Llamaremos a esta área la **GPU** o **Motor Gráfico** y, si has estado leyendo el [artículo de la N64](nintendo-64#graphics), solo para que sepas que el núcleo ahora es funcional desde el primer momento, por lo que los programadores no tendrán que preocuparse por inyectar código para hacerlo funcionar. Sin embargo, habrá algunas partes interesantes que son personalizables.

![Diseño de la GPU, Flipper.](flipper_pipeline.png)

Como siempre, para dibujar un fotograma en la pantalla, nuestros datos pasarán por el canal de la GPU. Los datos pasan por muchos componentes diferentes que podemos agrupar en cuatro etapas:

#### Base de datos {.tabs.active}

![Diagramas de la fase de base de datos.](flipper_pipeline/database.jpg) {.tab-float}

La CPU y la GPU se comunican entre sí usando un **búfer FIFO** de longitud fija en la RAM principal, esta es una porción reservada donde la CPU escribirá comandos de dibujo que la GPU leerá (y eventualmente mostrará), esta funcionalidad es nativamente soportada por la CPU y la GPU.

Además, la CPU y la GPU no tienen que estar apuntando al mismo FIFO al mismo tiempo, por lo que la CPU puede llenar uno separado mientras la GPU está leyendo el primero [@cpu-gdc]. Esto evita el tiempo de inactividad.

Emitir comandos individuales para construir nuestra geometría puede resultar muy tedioso con escenas complejas, por lo que las bibliotecas oficiales incluyen herramientas que generan las Listas de Visualización necesarias (conjunto precompilado de comandos FIFO) a partir de los activos del juego; este bloque solo necesita ser copiado a la RAM para permitir que la GPU los muestre de manera efectiva.

La GPU contiene un **procesador de comandos** que se encarga de buscar comandos en el FIFO.

#### Geometría {.tab}

![Diagrama de la fase de vértice usando modo indirecto.](flipper_pipeline/vertex.jpg) {.tab-float}

Aquí los primitivos se transforman para darles forma según el escenario deseado y se preparan para el rasterizado. El motor utiliza una **unidad de vértices** o 'VU' dedicada para lograr esto.

Hay dos **modos de vértice** disponibles para manejar los primitivos emitidos a través del FIFO:

- **Modo directo**: La CPU emite cada entrada FIFO con todos los atributos requeridos (posición, norma, color, coordenada de textura o índice de matriz). Útil cuando los datos ya están en caché.
- **Modo indirecto**: La entrada del FIFO contiene un valor de índice que especifica dónde se encuentra la información del atributo en la RAM, por lo que la unidad de vértices necesita buscarlo por sí misma. Estos datos están estructurados como una matriz, así que para que la VU la recorra, cada entrada de vértice debe especificar dónde comienza la matriz (**puntero base**), cuán larga es cada entrada (**paso**) y en qué posición está el vértice (**índice**).

Una vez cargados, los primitivos pueden ser **transformados**, **recortados**, **iluminados** (cada vértice tendrá un valor RGB que también puede ser interpolado para propósitos de sombreado Gouraud) y finalmente, **proyectados**.

#### Textura {.tab}

![Diagrama de la fase de textura usando una configuración predeterminada.](flipper_pipeline/texture.jpg) {.tab-float}

Ahora es el momento de aplicar texturas y efectos a nuestros modelos, y para eso la GPU incluye múltiples unidades que procesarán nuestros píxeles. Ahora, este es un procedimiento muy sofisticado (aunque bastante complejo), por lo que si encuentras difícil seguirlo, solo piensa en él como una gran línea de ensamblaje que procesa píxeles. Habiendo dicho esto, hay tres grupos de unidades disponibles:

- **Cuatro unidades de píxel paralelas** (también llamadas 'canales de píxeles'): Rasterizan nuestras primitivas (las convierte en píxeles). Tener cuatro unidades disponibles permite entregar hasta 2x2 píxeles en cada ciclo.
- **Una unidad de mapeo de textura** al final de cada unidad de píxel (dando **cuatro en total**): Juntas procesan hasta ocho texturas para nuestras primitivas (ahora meros píxeles) en cada ciclo.
  - También puede hacer un bucle para fusionar múltiples capas de textura sobre el mismo primitivo, esta característica se llama **Multitexturizado** y puede usarse para lograr **texturas detalladas**, **mapeo de entorno** (reflejos) y **mapeo de relieves** [@graphics-staff], por ejemplo.
  - Finalmente, la unidad también proporciona un **búsqueda temprana del [z-búfer](nintendo-64#modern-visible-surface-determination)**, **mapeo mip** (procesamiento de una textura de menor tamaño, basado en el nivel de detalle) y **filtrado anisotrópico** (una bienvenida mejora sobre los [filtros anteriores](nintendo-64#tab-1-2-reality-display-processor) que proporciona mayor detalle con texturas inclinadas).
- **Unidad de Entorno de Texturas** (Texture Environment unit) o 'TEV': Un combinador potente de colores, programable de 16 etapas. Básicamente combina múltiples [texels](playstation#tab-4-5-textures) (iluminación, texturas y constantes) para lograr una inmensa cantidad de efectos de textura que se aplicarán sobre nuestros polígonos.
  - La unidad funciona recibiendo cuatro texels que luego se procesan según la operación solicitada. Después, puede alimentarse a sí misma con los texels resultantes como nueva entrada, por lo que en la siguiente etapa/ciclo, la unidad puede realizar un tipo diferente de operación sobre el resultado anterior. Este 'bucle' puede durar hasta 15 iteraciones.
  - Cada etapa tiene 2^4 operaciones para elegir [@graphics-dolphin_uber] y, considerando que el resultado puede ser re-procesado en la siguiente etapa, ¡hay ~5.64 × 10^511 permutaciones posibles!
  - Los programadores instalan el TEV en tiempo de ejecución (lo que significa que puede cambiar en cualquier momento) y esto es crucial, ya que abre la puerta a muchos materiales y efectos originales.

Todo esto es asistido por 1 MB de memoria de Textura (del tipo 1T-SRAM) que puede dividirse en caché y memoria Scratchpad (RAM rápida). **Descompresión de hardware en tiempo real** para texturas SRTC (Compresión de Texturas S3) también está disponible para ajustar más texturas en ese solo megabyte. de memoria disponible.

#### Renderizado {.tab}

![Diagrama de la fase de renderizado.](flipper_pipeline/render.png) {.tab-float}

La etapa final del proceso de renderizado incluye aplicar algunos toques opcionales pero útiles a nuestra escena:

- **Niebla**: Funde el último color del TEV con un color constante de niebla para simular efectivamente un entorno brumoso.
- **Z-compare**: Un [Z-buffer](nintendo-64#modern-visible-surface-determination) tardío. El motor usará 2 MB de 1T-SRAM embebida para los cálculos de Z-buffering.
- **Mezcla**: Combina los colores del cuadro actual con el búfer de cuadros anterior.
- **Tramado**: Como indica el nombre, aplica tramado sobre nuestro cuadro.

El frame resultante finalmente se escribe en el búfer de tramas en la 1T-SRAM integrada, pero esto todavía está bloqueado dentro de Flipper (la área se llama 'Embedded Frame Buffer' o 'EFB', aunque también incluye el z-buffer). Entonces, para mostrarlo en nuestra TV, tenemos que copiarlo al **bastidor de tramas externo** o 'XFB' [@graphics-xfb], que puede ser recogido por la **Interfaz de vídeo** o 'VI'. Además, el proceso de copia puede aplicar efectos como **Antialiasing** (suaviza los bordes), **Desentremecedor** (suaviza los cambios repentinos en brillo), **conversión de RGB a YUV** (un formato similar que ocupa menos espacio en la memoria) y **escalado Y** (escala verticalmente el frame).

Vale la pena mencionar que el área XFB también puede ser manipulada por la CPU, esto le permite combinar mapas de bits previamente renderizados con nuestro frame recién renderizado; o cuando ciertos juegos necesitan renderizar frames muy ricos en color que no caben en el EFB, entonces se renderizan en partes y se combinan por la CPU después (siempre sincronizados con el VI).

### Comparación interactiva {.tabs-close}

Es hora de poner todo esto en perspectiva, revisa cómo los programadores evolucionaron los diseños de sus juegos anteriores para aprovechar las nuevas capacidades gráficas de esta consola. ¡No olvides que los ejemplos son interactivos!

#### La mejora {.tabs.active}

El famoso modelo de Mario que tuvo que ser reducido debido al conteo de polígonos en la [generación anterior](nintendo-64) fue completamente rediseñado para esta, echa un vistazo más de cerca a cómo el modelo evolucionó de caras simples a mangas arrugadas.

![Super Smash Bros (1999) para la N64.<br>320 triángulos.](mario_smash_64){.toleft .nested model3d="true"}

![Super Smash Bros. Melee (2001) para la GC.<br>4.718 triángulos.](mario_melee_gc){.toright model3d="true"}

Es realmente impresionante cuánto detalle se ha añadido en tan sólo dos años, ¿no?

#### La actualización {.tab}

En este caso, Sonic Team ya diseñó un modelo de Sonic para [su consola única](dreamcast), pero después de haber porteado su juego a la GameCube, fueron capaces de añadir más polígonos a su modelo, logrando mejores detalles.

![Sonic Adventure (1999) para la DC.<br>1.001 triángulos.](sonic_adventure_dc){.toleft .nested model3d="true"}

![Sonic DX (2003) para la GC.<br>1.993 triángulos.](sonic_dx_gc){.toright model3d="true"}

### Creatividad {.tabs-close}

Como se puede ver en el funcionamiento interno de este canal, la tecnología gráfica ha evolucionado hasta el punto que los programadores ahora pueden tomar el control de ciertas funciones del proceso de renderizado.

![The Legend of Zelda: The Wind Waker (2003).](wind_waker.png) {.open-float}

Al mismo tiempo, las tarjetas gráficas de PC estaban empezando a descartar los canales de funciones fijas en favor de **núcleos de sombreado** (unidades que ejecutan pequeños programas que definen cómo los píxeles son operados). Flipper todavía contiene una GPU de función fija, sin embargo, al incluir componentes como la unidad TEV, uno podría argumentar que Nintendo proporcionó su propia solución de sombreado.

Supongo que uno de los mejores ejemplos de juegos que utilizaron esta nueva capacidad es **The Legend of Zelda: Wind Waker** que implementa una técnica única de color/iluminación conocida como **Cel shading** para hacer que sus texturas se vean como una *caricatura*.

{.close-float}

### Sistema de salida de vídeo

La señal de vídeo produce una resolución de hasta 640x480 píxeles (o 768×576 px en PAL) con hasta 16,7 millones de colores (profundidad de 24 bits). Además, el sistema podría transmitir su señal en **modo progresivo** (que tiene una imagen más clara, pero no todos los televisores lo soportaban durante esa época).

El XFB puede tener múltiples dimensiones, así que, por razones de compatibilidad, la interfaz de vídeo hará todo lo posible para mostrar el frame, remuestreando el XFB para caber en la pantalla de televisión basada en la región.

### Conexiones

La consola no incluía una, sino dos conectores de salida de vídeo:

![Conexiones A/V en la parte posterior.](av_photo.jpg) {.open-float}

- Uno llamado **Analógico A/V** que en realidad es el clásico [Multi Out](super-nintendo.md#a-convenient-video-out). Este es el modo más comúnmente usado.
  - La versión PAL de esta consola no lleva S-Video y la NTSC no ofrece RGB (¡qué mal!).
- Otro llamado **Digital A/V** que envía audio y vídeo en forma digital (similar al HDMI hoy en día, pero usando un protocolo completamente diferente!).
  - Nintendo lanzó un conjunto de cables de componentes que se conectan a este enchufe. El mismo enchufe incorporaba un DAC de vídeo y un codificador para convertir la señal digital en YPbPr (calidad óptima).
  - ¡El cable se vendió como un accesorio adicional y ahora se considera una especie de reliquia!

{.close-float}

## Audio

Nintendo finalmente lanzó algunos circuitos de audio dedicados para descargar la enorme tarea de la CPU-GPU y ofrecer sonidos de mejor calidad. Su nueva solución es un independiente**Digital Signal Processor** o 'DSP' fabricado por **Macronix** funcionando dentro de Flipper.

El trabajo del DSP consiste en realizar diferentes operaciones sobre nuestros datos de audio en bruto (p. ej. cambios de volumen, conversión de muestreo, efectos de sonido 3D, filtros, eco, reverberación, etc.) y luego emitir una señal PCM de 2 canales. Sin embargo, no funciona solo, la DSP proporciona audio con la ayuda de otros componentes.

Su primer ayudante es la **Interfaz de audio** (AI), un convertidor digital a analógico estéreo de 16 bits encargado de enviar la muestra final a través de la señal de audio que termina en el televisor. La AI sólo puede procesar 32 bytes de datos de audio cada 0.25 ms, así que si tenemos en cuenta que cada muestra de sonido pesa 2 bytes y necesitamos dos para crear sonido estéreo, la AI podrá mezclar hasta ocho muestras estereas con hasta 32 kHz de tasa de muestreo, *sonido*!

Finalmente, tenemos el bloque de **RAM de audio** (ARAM), que es un gran contenedor (16 MB) pero muy lento de memoria libre que se puede utilizar para almacenar datos de sonido crudos. Hay bastante espacio, así que la GPU también puede usarlo para almacenar material adicional (como texturas). La CPU no tiene acceso directo a esta memoria, así que recurrirá a DMA para mover el contenido alrededor.

Para bien o para mal, el DSP es programable con el uso de microcódigo ([_¡Ay!_](nintendo-64#audio)), pero no temas, ya que el SDK oficial ya incluye un microcódigo de uso general que casi todos los juegos usaban, excepto en la secuencia de arranque de la consola y algunos juegos de Nintendo (_qué conveniente_, como Nintendo no documentó el DSP, así que solo ellos saben cómo programarlo).

Dicho esto, el proceso de generación de sonido funciona de la siguiente manera [@audio-bourdon]:

1. La CPU ordena al DMA que mueva muestras en bruto a ARAM.
2. La CPU envía una lista de comandos que instruyen cómo debe operar estos samples el DSP. En otras palabras, sube el programa de microcódigo (solo uno está oficialmente disponible para los desarrolladores).
3. El DSP recupera muestras de ARAM, aplica las operaciones requeridas y las mezcla en dos canales. Finalmente, almacena los datos resultantes en RAM.
4. AI extrae samples procesados de la RAM y los envía a través de la señal de audio.

### Compresión y libertad

Aunque hayamos alcanzado la *edad de sampling* y ya no estamos bloqueados con formas de onda específicas, el nuevo sistema de sonido sigue siendo una gran mejora. Para empezar, la saga de la [secuencia de música](nintendo-64#audio) forzada ha desaparecido para siempre. El sistema ahora puede transmitir música pre producida a la salida de audio sin problemas, parecido a lo que la [Saturn](sega-saturn#audio) y la [PS1](playstation#audio) lograron hace años.

Déjame mostrarte un ejemplo usando dos juegos, uno de la Nintendo 64 y su secuela de la GameCube. Ambos tienen diferente música, pero el contexto (batalla enemiga) es el mismo. Echa un vistazo a cómo las dos pistas difieren en calidad de sonido, teniendo en cuenta el diseño de cada sistema (compartido vs. dedicado).

![Paper Mario (2000) para la N64.<br>Secuenciado sobre la marcha por el RSP.](PaperMario64){.toleft video="true"}

![Paper Mario: The Thousand-Year Door (2004) para la GC.<br>Transmitido al DSP.](PaperMario){.toright video="true"}

*Como puedes escuchar*, el DSP finalmente les dio a los compositores de música la flexibilidad y riqueza que siempre pidieron.

#### Material adicional

Para una comparación de lado a lado más directa, he preparado este widget interactivo que muestra cómo los compositores adaptaron sus arreglos para la GameCube y su predecesor. Aquí, la misma canción se utiliza para un título de Nintendo 64 y uno de GameCube y la comparación resultante me permite demostrar una vez más las ventajas técnicas del DSP de GameCube.

![**Nintendo 64:** Kirby 64: The Crystal Shards (2000).<br>**GameCube:** Kirby Air Ride (2003).](){audio_switcher="true" src1="kirbycrystal_n64" src2="kirbyair_gc" label1="Nintendo 64" label2="GameCube" .centered-container}

Ahora, para visualizar lo que está ocurriendo en cada pista, aquí están los dos espectrogramos respectivos. Antes de empezar, si no estás familiarizado con este tipo de gráficos, recomiendo leer mi artículo anterior de la NES, en particular la sección [audio](nes#audio) (donde los introduje).

![Espectrograma del canal PCM en Kirby 64: The Crystal Shards (2000).](spectrograms/kirbycrystal_n64.png) {.toleft}

![Espectrograma del canal PCM en Kirby Air Ride (2003)](spectrograms/kirbyair_gc.png) {.toright}

Para ser justos, las pistas mezcladas son difíciles de seccionar en un espectrograma, pero creo que puedo tratar de deducir algunos patrones.

Para empezar, casi todo el espectro de frecuencias se está utilizando de forma uniforme en la pista de GameCube, que pueden ser atribuidos a los instrumentos adicionales utilizados para el acompañamiento (que agregan armonía y por lo tanto llenan más áreas en el gráfico).

Por último, las amplitudes en el espectrograma de la GameCube parecen más uniformemente distribuidas. En otras palabras, el volumen de cada instrumento está mezclado de forma única e incluye efectos como la reverberación. Estoy especulando que este último es lo que el compositor originalmente pretendía mientras producía esta canción, y estos tipos de controles son posibles por el hecho de que la GameCube soporta streaming de audio. Así, los compositores pueden usar cualquier herramienta para secuenciar y mezclar su música a diferencia de depender estrictamente de la consola (y sus limitaciones) para la secuencia y mezcla en tiempo de ejecución.

No diría que la Nintendo 64 es completamente incapaz de producir el mismo resultado. Sin embargo, no cabe duda que, en el mundo de la Nintendo 64, cada función de audio cuesta ciclos y/o memoria adicionales, y esto puede tener un impacto en otras áreas del juego. De ahí la necesidad de racionar los recursos. Por otro lado, con el apoyo de la GameCube para muestras grandes se puede simplemente transmitir la canción completa.

## E/S

Parece que esta generación está dedicando mucho trabajo a la capacidad de expansión y los accesorios, la GameCube incluyó un par de nuevos puertos interesantes, aunque algunos de ellos permanecieron inutilizados.

### E/S interna

![Diagrama principal de la arquitectura de la GameCube. Aquí encontramos el 'Northbridge', que controla la mayor parte de la E/S.](diagram.png)

Flipper está a cargo de interconectar la CPU con el resto de los componentes, así que, además de incluir circuitos de sonido y gráficos, también proporciona una colección de hardware llamado **Northbridge** que se compone de [@cpu-tree]:

- **Interfaz de audio** o 'AI': Conecta el Codificador de Audio.
- **Interfaz de Vídeo** o 'VI': Conecta el Codificador de Vídeo.
- **Interfaz de Procesador** 'PI': Conecta la CPU (Gekko).
- **Interfaz de Disco** o 'DI': Conecta el controlador de DVD, que puede considerarse un ordenador independiente per se.
- **Interfaz Serial** o 'SI': Conecta cuatro posibles mandos utilizando un protocolo serial propietario.
- **Interfaz Externa** o 'EXI': Conecta los accesorios externos (más explicado abajo), Tarjeta de Memoria y el IPL BIOS junto con el Reloj en tiempo real o 'RTC'. Utiliza un protocolo similar a SPI para conectar estos dispositivos usando solo un nodo serial.

Cada interfaz expone un conjunto de registros para alterar parte de su comportamiento.

### E/S opcional

En la parte inferior de la carcasa de la GameCube, encontrarás dos enchufes externos para conectar algunos widgets.

![Ranuras cubiertas para accesorios en la parte inferior de la carcasa.](accessories_covered.jpg){.tabs-nested .active .open-float .tab-float title="Tapado"}

![Ranuras descubiertas para accesorios en la parte inferior de la carcasa.](accessories.jpg){.tabs-nested-last title="Destapado"}

Ambos son técnicamente idénticos (bus en serie funcionando a 32 MHz), pero se presentan con una forma externa distinta para acomodar diferentes accesorios:

- **Puerto Serial 1**: Nintendo envió módem y adaptadores de banda ancha para esta ranura.
- **Puerto Serial 2**: No se envió ningún accesorio público que usara este puerto, pero es posible que encuentres algunos accesorios de terceros que proporcionaron herramientas de depuración.

Estos puertos son operados en el EXI stack.

{.close-float}

### Una E/S inusual

Notarás que todavía no he mencionado otro enchufe disponible que se encuentra junto a los puertos en serie: el **Puerto en Paralelo**. Este puerto resulta ser mucho más rápido (8 bits a 80 MHz vs. 1 bit a 32 MHz), lo que puede ser la razón por la que Nintendo lo llamó **Puerto de Alta velocidad**. ¡Pero la parte más inusual es que este puerto no está conectado a través de EXI, sino a través de ARAM!

El único accesorio oficial conocido hasta la fecha es el famoso **Game Boy Player** que se conectaba como piso extra debajo de la GameCube y contenía el hardware necesario para jugar de forma nativa a juegos de la [**Game Boy**](game-boy) y de la [**Game Boy Advance**](game-boy-advance). El Player funciona haciendo todo el trabajo pesado solo y luego enviando los resultados (fotogramas y datos de audio) a ARAM que la GameCube envía a los componentes respectivos para la pantalla y el sonido.

### Diseño consistente

Me pareció que vale la pena señalar que no importa cuántos accesorios conectes, la consola siempre mantendrá su forma cúbica (o al menos lo intentará).

## Sistema Operativo

Al encender la consola, la CPU comenzará a cargar un sistema operativo llamado **Dolphin OS** que se encuentra en el chip BIOS/IPL. Este es un diminuto sistema operativo que se encargará de inicializar el hardware y proporcionar algunas funciones convenientes, como llamadas al sistema y variables globales para que los juegos utilicen. Los juegos desarrollados con el SDK oficial ejecutarán implícitamente estas llamadas durante operaciones de bajo nivel.

![El logotipo oficial, que se muestra una vez finalizada la animación de arranque.](ipl/splash.png)

### Apariencia y carcasa

Después de finalizar el proceso de arranque, el sistema operativo cargará un pequeño programa *extraoficialmente* llamado **Menú principal**.

![Menú principal con múltiples ajustes disponibles.](ipl/menu.png){.tabs-nested .active title="Menú"}

![Ajustes de reloj.](ipl/clock.png){.tab-nested title="Reloj"}

![Ajustes de sonido.](ipl/sound.png){.tabs-nested-last title="Opciones"}

Este programa es el responsable de mostrar la famosa animación del splash (el cubo que dibuja un logotipo de la GameCube) y cargar el juego si hay uno insertado. A falta de un juego *válido*, proporcionará un menú simple en forma de cubo con varias opciones para elegir:

- Cambiar Fecha y Reloj.
- Cargar el juego desde el lector de DVD.
- Gestionar guardados en cualquier Tarjeta de Memoria.
- Cambiar los ajustes de sonido y la posición de la pantalla.

## Juegos

Nintendo proporcionó a los desarrolladores muchas herramientas para ayudar (y alentar) el desarrollo de juegos para su consola [@games-sdk]:

- **Dolphin SDK**: El conjunto oficial de APIs y bibliotecas útiles. Esto incluye la biblioteca **GX** a cargo de programar la GPU de Flipper.
- **Compiladores C** y **C++**.
- **Depuradores** y **Testers**: Están destinados a ser utilizados con un kit de desarrollo oficial.
- **Cygnus**: Ahora conocido como 'Cygwin', se utiliza básicamente para replicar el entorno UNIX en Windows.
- **CodeWarrior**: Un IDE estándar *en aquellos días*.
- Varias **herramientas de asistencia** como MusyX, Editores de texturas, Exportadores de listas de visualización, Programadores USB, etc.
- ¡Toneladas y toneladas de **documentación**! (disponible en PDF y HTML)

### Hardware especializado

Aparte del software, la compañía suministró diferentes kits de hardware (que varían en precio) antes y después de que la consola fuera públicamente lanzada.

Probablemente la más popular que vale la pena mencionar es el **Dolphin Development Hardware** o 'DDH' que consistía en una torre parecida a un PC que contiene parte de la E/S de la GameCube y un montón de hardware para desarrollo [@games-devstuff], se utilizó principalmente como una estación de debugging mientras el juego se desarrollaba en un PC en Windows.

### Medios

Los juegos se cargan desde un disco patentado llamado **miniDVD**, tiene casi la mitad de tamaño que un disco DVD tradicional y puede contener hasta 1,4 GB de datos.

Como dato interesante, el lector de discos funciona a **Velocidad Angular Constante** o 'CAV', lo que significa que sus datos se leerán a mayor velocidad si se encuentran en la zona exterior del disco (3,125MB/s) y más lentamente si se encuentran en la zona interior (2MB/s). Esto difiere de los sistemas de **Velocidad Lineal Constante** (utilizados por los lectores de CD/DVD tradicionales) en los que los efectos son los contrarios.

Las partidas se almacenan en un accesorio externo propietario llamado **Tarjeta de memoria** y hay suficientes ranuras para dos.

### Mandos inusuales

Nintendo comercializó un accesorio conocido como el **Cable GameBoy Link** que conectaba una [**Game Boy Advance**](game-boy-advance) al puerto del mando de la GC, de modo que los juegos podían cargar pequeños programas en la GBA y tratarla como un mando especial. Esta interesante función permitía interacciones y contenidos únicos en algunos juegos.

### Plataforma en línea

Pues bien, a diferencia de [la competencia](dreamcast), Nintendo no sólo obligaba a los usuarios a comprar accesorios extra para acceder a los contenidos en línea, sino que tampoco desplegaba ningún servicio de internet en el que los editores pudieran confiar [@games-online], haciendo que los desarrolladores fueran los únicos responsables de proporcionar la infraestructura de internet necesaria.

Como resultado, aunque el juego en línea era una característica posible, no se adoptó de forma generalizada y solo una pequeña cantidad de juegos hicieron uso de ella.

## Antipiratería y Homebrew

Nintendo lleva bastante tiempo en esta batalla, así que no es ninguna novedad que haya incluido mecanismos de seguridad para evitar ejecutar software no autorizado o juegos de una región diferente. Además, debido a la nueva gama de E/S que ofrece la GameCube, la superficie de ataque aumentó considerablemente.

### Mecanismos de seguridad

Podemos organizarlos en estas áreas:

#### Controlador de DVD {.tabs.active}

Aunque se trata de la primera consola de Nintendo que utiliza el disco, intentar reproducir copias piratas de juegos no iba a ser tan fácil. El miniDVD está protegido mediante códigos de barras patentados en la cara interna del disco, además de tener sus datos encriptados. El proceso de validación y descifrado funciona a la perfección: El controlador del miniDVD se encarga de ello, mientras que el sistema se limita a solicitar únicamente los datos.

El hardware que compone el lector de DVD puede imaginarse como un muro fortaleza al que solo se accede mediante una serie de comandos, el controlador del miniDVD incorpora una CPU propietaria que se encargará de decidir si el disco insertado es auténtico o no, y si no lo es, ningún comando emitido por la CPU principal convencerá de leerlo en caso contrario.

**Derrota**: Como en cualquier otro juego del gato y el ratón, era cuestión de tiempo que terceras empresas consiguieran hacer ingeniería inversa del controlador para construir mod-chips que pudieran engañar al lector. ¡Pero ten en cuenta que ningún mod-chip hará que esta consola se adapte mágicamente a un CD/DVD convencional sin alterar físicamente la carcasa!

#### IPL y EXI {.tab}

Otra posible vía de explotación consiste en utilizar la E/S externa disponible para cargar programas Homebrew. Aunque, sin crackear primero el lector de DVD, la única otra opción disponible es intentar tomar el control del primer programa que cargue la GameCube, y eso es... El IPL.

Eso significa que usando ingeniería inversa en la BIOS y sustituyendo el chip por uno modificado, uno podría ejecutar, digamos, un lector de archivos, y desde ahí ejecutar programas recibidos desde los puertos de accesorios (suponiendo que el hardware adicional esté conectado).

Sea como fuere, el chip IPL está cifrado mediante condicionales XOR y un Cipher-text [@anti_piracy-ipl], por lo que es "imposible" realizar ingeniería inversa.

**Derrota**: Los hackers acabaron descubriendo que el hardware que gestiona el descifrado de la IPL contenía un bug que les permitía capturar el Cipher-text utilizado [@anti_piracy-steil]. ¡Con esto, se podría construir otra ROM y encriptarla con la misma clave para que la GameCube la arranque como propia!

Por si fuera poco, los piratas informáticos también han encontrado nuevos métodos para engañar al lector de miniDVD para que cargue DVD convencionales.

#### Mención Honorífica {.tab}

Antes de que se descubrieran esos dos mecanismos, existía en realidad una forma mucho más sencilla de cargar código arbitrario sin ningún tipo de modificación. Este método consistía en **apropiarse del protocolo en línea**.

Algunos juegos como *Phantasy Star Online* implementaban su propia funcionalidad en línea, que podía actualizarse descargando un ejecutable actualizado (archivo DOL) de los servidores de la compañía, y esta no implementaba ningún tipo de seguridad en su protocolo. Así que, como puedes ver, esto era un Ataque de intermediario esperando a suceder...

Resumiendo, suplantando un servidor falso, la GameCube descargaba (y ejecutaba) cualquier DOL que le proporcionaras. Eso significa que los hackers solo necesitaban el juego original y el adaptador de banda ancha. Esta técnica se conoce como **PSOload** [@anti_piracy-psoload].

## Eso es todo amigos {.tabs-close}

![Mi vieja GameCube rescatada recientemente del desván. Solo necesitaba el mando para la Wii (¡Era más barato comprar toda la consola de segunda mano!).](my_gamecube.jpeg)

Pues ya está, ¡el **10º artículo**!

Realmente intenté poner un límite aproximado a la longitud de este artículo pero tienen que entender, la tecnología se ha vuelto *tan compleja* que si accidentalmente me salto algo importante, todo el tema se vuelve imposible de seguir.

De todos modos, me gustaría dar las gracias a la comunidad de IRC #dolphin-dev por ayudarme a entender la complicada tubería de la GPU de Flipper, esta gente lleva bastantes años desarrollando el emulador de GameCube y es realmente impresionante todo lo que han tenido que aguantar.

Y, por último, considere la posibilidad de [contribuir](support) si te ha parecido una lectura interesante. Me esfuerzo por hacerla lo más completa posible, y en el proceso, olvido cuánto tiempo me está costando. Sin embargo, la considero una buena inversión.

¡Hasta la próxima!  
Rodrigo.
