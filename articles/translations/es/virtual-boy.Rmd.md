---
short_title: Arquitectura del Virtual Boy
long_title: Arquitectura del Virtual Boy
name: Virtual Boy
subtitle: Potencial oculto con un final desafortunado
date: 2021-02-12
release_date: 1995-07-21
cover: "virtualboy"
javascript: ["virtualboy_graph"]
generation: 5
published: true
top_tabs:
  Models:
    - title: "Back"
      file: back
      extension: jpg
      active: true
      caption: "El Virtual Boy.<br>Lanzado el 21/07/1995 en Japón y el 14/08/1995 en América."
      bib_source: copetti
    - title: "Front"
      file: front
      caption: "Vista frontal de la consola.<br>Los usuarios deben mirar a través de los visores."
      latex_height: 95
  Motherboard:
    caption: "No confundir con la 'placa de servo', a la que se conecta la placa base.<br>El chip Virtual Sound Unit, 128 KB de DRAM y 64 KB de PSRAM están montados en la parte trasera."
    paperback_latex_height: 90
    marked_paperback_latex_height: 90
  Diagram:
    caption: "Observa cómo los fondos de las dos capturas están ligeramente desplazados horizontalmente."
---

## Una breve introducción

Una consola a menudo recordada por su corta vida útil y su reducido espacio de color. Aunque técnicamente correcto, creo que estos atributos tienden a eclipsar otras propiedades sorprendentes.

En este artículo, invito a los lectores a conocer mejor sus características internas; muchas de ellas ganaron protagonismo en el mercado solo después de que el Virtual Boy fuera descontinuado.

## {.supporting-imagery}

## Pantalla

Todo el sistema es una curiosa proeza de ingeniería. Externamente, recuerda a un voluminoso visor de realidad virtual montado sobre un bípode. El jugador debe acercar la cabeza al visor para ver el juego en acción.

![Esto es lo más lejos que llegué intentando fotografiar la pantalla y la carcasa al mismo tiempo. ¡En realidad, los juegos se ven muy nítidos y a tamaño completo!](case/front.png)

Internamente, la historia es bien distinta (y bastante complicada). Por esa razón, creí conveniente empezar por explicar cómo muestra imágenes esta consola, para luego adentrarme en el hardware interno.

### Proyectando una imagen

Al encender el Virtual Boy, verás dos imágenes **monocromáticas en rojo** (una por cada ojo) a través del visor. ¿Todo claro hasta ahora? Bien, aquí viene lo interesante: **esta consola no tiene pantalla**. Lo que ves es más bien una ilusión, así que veamos con más detalle qué está pasando realmente.

Los temas implicados en esta explicación (óptica, fenómenos visuales, etcétera) pueden parecer complejos al principio, así que he elaborado varios diagramas para hacer esta sección un poco más inmersiva.

#### Escáner {.tabs .active}

![Diagrama principal del escáner, que muestra cómo la luz de los LEDs llega a los ojos del jugador.](_diagrams/scanner.png){.tabs-nested .active .tab-float title="Diagrama"}

![Vista aérea de la consola. El primer interruptor es el 'control de enfoque', y debajo está el 'dial IPD'.](case/top.jpg){.tabs-nested-last title="Interruptores"}

El gran volumen de esta consola se debe al **escáner**, que ocupa una parte significativa del interior. El escáner es la zona del Virtual Boy responsable de mostrar imágenes. Consta de dos **unidades de visualización**, cada una de las cuales proyecta un frame de manera independiente (en total, dos frames: uno por cada ojo).

Una unidad de visualización es donde ocurre toda la 'magia'. Consta de los siguientes componentes:

- Una **unidad LED**: Contiene 224 LEDs rojos apilados verticalmente, junto con el circuito necesario para controlar cada uno.
- Una **lente**: Refracta la luz de los LEDs.
  - En la parte superior de la carcasa del Virtual Boy hay un **control de enfoque** que permite acercar o alejar la lente de los LEDs. Esto permite al usuario ajustar la consola según su distancia focal (para evitar imágenes borrosas).
- Un **espejo**: Refleja la luz de la lente y la dirige hacia los ojos del usuario. Además, este componente oscila de forma continua gracias a un **motor de bobina móvil** conectado a él. El **servo control** —una placa independiente— controla dicho motor mediante pulsos eléctricos a 50 Hz.
  - En definitiva, es una zona muy compleja y delicada de la consola. Por eso, se incluye un **fotointerruptor** adicional (un tipo de fotosensor). Este informa al servo control sobre la oscilación del espejo, que a su vez monitoriza y aplica las correcciones necesarias.

Junto al control de enfoque hay un **dial IPD** (un mando giratorio), que ajusta la distancia entre las dos unidades de visualización. Esto sirve para adaptar las pantallas a la distancia interpupilar del usuario.

#### Mecánica {.tab}

![Visualización simplificada del ángulo del espejo oscilante a lo largo del tiempo. El eje Y indica el ángulo y el eje X representa el tiempo. Los LEDs izquierdo y derecho están activos durante los intervalos rojo y azul, respectivamente. Durante el intervalo gris, ningún LED está activo. Por simplicidad, la velocidad angular representada aquí es constante (en la práctica es variable).](scangraph){.tab-float animation="true" book_static="true" #fig-oscil_angle}

Ahora que hemos identificado cada componente, veamos cómo el Virtual Boy consigue presentar imágenes a nuestros ojos.

Quizás no te habías fijado antes, pero **no hay ninguna pantalla de matriz de puntos**. ¿Cómo es posible entonces que veamos imágenes bidimensionales a través del visor? Al igual que un monitor CRT, las unidades de visualización aprovechan la forma en que percibimos las imágenes:

1. Los espejos oscilantes permiten que una única columna de LEDs se desplace horizontalmente por nuestro campo visual. El ángulo del espejo se orienta estratégicamente para proyectar los LEDs en **384 'posiciones de columna' distintas** (distribuidas por nuestro campo visual).
2. Dado que la visión humana es logarítmica y el espejo oscila a **50 Hz** (cada ciclo dura 20 ms), terminamos percibiendo **384 columnas de LEDs iluminándose al mismo tiempo** hasta que el espejo detiene su oscilación. Esto es el resultado del *efecto de persistencia retiniana*.
3. Todo esto se sincroniza cuidadosamente con el controlador de LEDs, que actualiza cada LED individualmente a medida que el espejo se desplaza ligeramente. Así terminamos percibiendo **una imagen completa** procedente del visor.

En la práctica, se deben cumplir ciertas condiciones para que estos principios funcionen:

- Los LEDs solo deben funcionar cuando la velocidad angular del espejo es estable (es decir, cuando el espejo no está cambiando de dirección). Esto puede entenderse como el [estado activo](master-system#tab-1-4-result) de un monitor CRT.
- Ahora bien, la velocidad angular del espejo no permanece constante en la práctica. Como el espejo no puede cambiar de dirección instantáneamente, los períodos 'estables' están sujetos a fuerzas que alteran su velocidad. Para gestionar esto, el Virtual Boy almacena en memoria una lista de valores llamada **Column Table**, que especifica cuánto tiempo asignar a cada intervalo de columna. Esto ayuda a equilibrar los períodos de exposición de la 'columna de LEDs', tanto los excesivos como los insuficientes.
- Por último, no hay que olvidar que todo este proceso ocurre **dos veces**, ya que hay dos unidades de visualización (una por cada ojo). Por desgracia, **las dos unidades no pueden recibir energía y datos al mismo tiempo**, por lo que cada una opera en períodos de visualización distintos (desfasadas **10 ms**). Nosotros no lo percibimos; ¡otra ilusión más!

#### Pantalla {.tab}

![Ilustración simplificada de cómo funciona la primera unidad LED durante períodos concretos de tiempo. Observa cómo los LEDs muestran columnas del frame buffer durante las fases activas.](frame){.tab-float animation="true" hardcover_latex_width="95%" paperback_latex_width="85%"}

A diferencia de los chips de vídeo anteriores diseñados siguiendo el modelo de los CRT (como el [PPU](nes#graphics) y el [VDP](master-system#graphics)), el Virtual Boy **no renderiza los gráficos al vuelo**. En su lugar, el chip gráfico envía el frame procesado a un frame buffer en memoria. Después, cada columna del frame se transfiere al conjunto de LEDs para su visualización.

Cuando la placa de servo determina que es el momento de mostrar, el chip gráfico transmite columnas de píxeles del frame buffer a los 224 LEDs apilados verticalmente, de manera cuidadosamente sincronizada. En consecuencia, los LEDs muestran 384 columnas durante el período de visualización, dando a la consola una 'resolución de pantalla' de **384×224 píxeles**.

Además, es necesario almacenar dos frame buffers, ya que cada uno irá a una unidad de visualización diferente. El subsistema gráfico también emplea doble buffer y otras particularidades (que se detallan más adelante en la sección 'Gráficos'). Por ahora, basta con recordar cómo se envía un frame digital a los LEDs.

#### Períodos activos {.tab}

![Otra ilustración simplificada, esta vez mostrando cómo la oscilación del espejo desvía la luz de los LEDs para que el usuario perciba finalmente un frame completo.](active){.tab-float animation="true" paperback_latex_width="92%"}

Como consecuencia de este diseño, hay períodos de:

- **Visualización activa**: Durante este tiempo, los LEDs leen una imagen del frame buffer y nada puede interrumpirlo.
- **Visualización activa 2**: Idéntico a la fase anterior, pero ahora la segunda unidad de visualización está activa.
- **Reposo de dibujo**: Un período en el que ningún LED está activo y la velocidad angular del espejo es inestable.

Este ciclo se repite 50 veces por segundo (de ahí la tasa de refresco de 50 Hz), lo que significa que para cada frame, la CPU y la GPU tienen aproximadamente 10 milisegundos para actualizar la imagen que verá el usuario. En la práctica, los ingenieros de Nintendo implementaron una solución más sofisticada. Lo ampliaré en la sección 'Gráficos', pero por ahora espero que tengas ya una idea clara de cómo el Virtual Boy se las ingenió para generar imágenes con hardware de bajo coste.

#### Comentarios finales {.tabs-close}

Esta ha sido una breve explicación de cómo la óptica puede transformar una única línea vertical en una imagen. Si tienes (o has leído sobre) el Virtual Boy, puede que te preguntes cuándo entra en juego la imagen tridimensional. Quiero aclarar que ninguna de las explicaciones anteriores tiene que ver con ese efecto. Lo menciono porque en el pasado he visto afirmaciones que sugieren que los espejos oscilantes son la causa del efecto de 'percepción de profundidad'. Sin embargo, a partir de la información que he recopilado durante este estudio, creo que esa afirmación es incorrecta.

Dicho esto, creo que es el momento de adentrarnos en el fenómeno 3D...

### Creando una visión tridimensional

Durante la campaña de marketing del Virtual Boy, hubo mucho alboroto en torno a la capacidad de la consola para proyectar un 'mundo 3D'. No me refiero a imágenes con polígonos 3D (como las de las demás consolas de quinta generación), sino a la percepción real de la profundidad.

En pocas palabras, el Virtual Boy se apoya en la **imagen estereoscópica** para lograr esa ilusión [@cpu-tucker] [@graphics-patent]. En general, este sistema no solo jugueteaba con nuestra visión para proyectar una imagen completa: ¡también conseguía que ciertos dibujos parecieran más cercanos o más lejanos que los demás!

::: {.subfigures .tabs-nested .tab-float .open-float .pixel #fig-marios_tennis_stereo}

![Unidad de visualización izquierda.](tennis/left.png){.active paperback_latex_width="97%" title="Izquierda"}

![Unidad de visualización derecha.](tennis/right.png){paperback_latex_width="97%" title="Derecha"}

Captura durante una partida de *Mario's Tennis* (1995), que muestra las diferencias entre los dos frames renderizados (observa los pequeños desplazamientos en el fondo).

:::

La técnica empleada es muy sencilla: cada uno de los dos frames mostrados (uno por cada ojo) contiene elementos ligeramente desplazados en horizontal [@fig-marios_tennis_stereo]. Así, al verlos con ambos ojos, estos desplazamientos crean la ilusión de que algunas partes de la imagen están más cerca que otras. La profundidad percibida depende de la dirección en que los elementos están desplazados.

Los objetos desplazados hacia el centro de los ojos del espectador (hacia la derecha en el frame izquierdo, y hacia la izquierda en el frame derecho) parecen más cercanos; los desplazados en sentido contrario parecen más lejanos. Por último, los elementos sin desplazamiento horizontal se perciben como situados entre los dos extremos. Esta técnica se conoce como **paralaje estereoscópico**.

{.close-float}

Uno de los inconvenientes de la estereoscopia es la **fatiga ocular**. Esto se mitigaba gracias a que los juegos incluían una función de 'pausa automática', que recordaba al usuario hacer descansos cada 30 minutos. Nintendo también incluyó varios mensajes de advertencia en su embalaje y documentación para ayudar a prevenir problemas graves. Aun así, no existía ninguna opción para desactivar el efecto 3D.

## CPU

Bueno, retomemos la arquitectura digital: veamos ahora cómo los juegos construyen los frames que ves y la música que escuchas.

Para la CPU, Nintendo empleó una versión personalizada del **NEC V810**, que opera a los impresionantes **20 MHz** (*teniendo en cuenta que la [SNES](super-nintendo#cpu) funcionaba a una media de 1,79 MHz y la [GameBoy](game-boy#cpu) a 4,19 MHz, ¡imagina lo que esta es capaz de hacer!*). Nintendo la denomina **NVC** porque el chip incluido en el Virtual Boy combina un núcleo V810 con varios componentes adicionales, que examinaremos en los próximos párrafos.

![El chip NVC.](nvc_chip.webp){latex_width="80%"}

Para empezar, el V810 forma parte de la **familia de CPU V800** que NEC diseñó para el mercado embebido [@cpu-necfamily] [@cpu-necv805]. Aunque esta CPU no fue tan popular como la competencia (como la [serie MIPS](playstation#cpu) o la [Motorola 68000](mega-drive-genesis#cpu)), sí ofrece un abanico de prestaciones de vanguardia:

- La **ISA de la serie V800**: Un set de instrucciones RISC que combina instrucciones de 16 y 32 bits.
- **Treinta y dos registros de 32 bits**: El V810 es una CPU de 32 bits completa y los registros se ajustan perfectamente a esa arquitectura.
  - Como es habitual en las [CPUs RISC](playstation#the-offering), `r0` es siempre cero.
- Un **bus de direcciones de 32 bits**: Permite acceder a hasta 4 GB de memoria, una cantidad enorme para la época.
- Un **pipeline de cinco etapas**: Consulta un [artículo anterior](sega-saturn#cpu) para una explicación detallada del pipeline de instrucciones. Cabe señalar que mientras otros sistemas debutaron con tres etapas de pipeline, este fue directamente a por las cinco.
  - Para evitar [hazards](playstation#delay-galore), NEC recurrió a bloqueos por hardware para detener el pipeline automáticamente.
- **1 KB de caché L1** de instrucciones.
  - Curiosamente, no volverás a ver otra consola portátil 'con caché' de Nintendo hasta la [Nintendo DS](nintendo-ds): ¡casi una década después!

Esto era excepcionalmente sofisticado para una consola portátil de 1995, pero Nintendo fue aún más lejos al incorporar recursos adicionales:

- **Interfaces de E/S**: Se encargan de la comunicación con los accesorios propietarios.
- **Bus externo de 16 bits**: El V810 puede configurarse con un bus de 16 o 32 bits. Los ingenieros de Nintendo optaron por el primero.
  - Esto introduce algunas penalizaciones (es decir, estados de espera) en las transferencias de memoria de 32 bits, pero como verás pronto, los programas no eran *tan* exigentes.
- Un **temporizador**: Simplemente un contador de 16 bits.
- Un **control de espera**: Detiene la CPU en función del bus externo al que se accede. Esto se debe a que el V810 trata toda la memoria de forma uniforme. En la práctica, acceder a la ROM del juego es más lento que, por ejemplo, a la RAM interna. Este componente corrige los tiempos en consecuencia.

Aunque todo esto suena de maravilla, tiene un coste considerable: **seis pilas AA**. Esto explica probablemente por qué las empresas se aferraron a tecnologías más antiguas en los dispositivos portátiles, al menos durante los años 90.

### Acceso a memoria

Las direcciones de 32 bits son muy tentadoras sobre el papel, pero si el sistema no va a utilizar ni de lejos 4 GB de posiciones de memoria, es un enorme desperdicio de recursos. Por ejemplo, aunque las líneas de dirección superiores no cambien, igualmente se decodifican en cada lectura de memoria.

Por eso, y con buen criterio, Nintendo redujo el direccionamiento a **27 bits**. Esto significa que se puede acceder a un máximo de **128 MB de memoria**. El sistema sigue utilizando palabras de 32 bits para las direcciones, pero los 5 bits superiores se descartan. En consecuencia, algunas regiones del mapa de memoria están duplicadas.

Dicho esto, la distribución del mapa de memoria permite a la CPU acceder a la mayoría de los componentes que conforman el sistema. Entre ellos se incluyen [@cpu-guy]:

- **64 KB de RAM** de uso general. Por eso Nintendo la denomina 'Work RAM' o 'WRAM'.
  - El tipo de chip utilizado se llama **PSRAM** (abreviatura de 'Pseudostatic RAM' o 'Pseudo-SRAM'), que incorpora DRAM con autorrefrescado. Esta configuración es a la vez más rápida y eficiente energéticamente que la DRAM convencional de bajo coste.
- La ROM del cartucho y cualquier RAM incorporada (si existe).
- El chip de sonido.
- El chip gráfico, junto con su memoria dedicada.
- Las interfaces de E/S y sus registros correspondientes.

Hasta aquí llega la CPU; ahora es el momento de ver qué puedes hacer con ella.

## Gráficos

Repasemos los requisitos clave para mostrar los gráficos correctamente:

- El Virtual Boy debe mostrar dos frames distintos.
- Cada frame tiene una resolución de 384×224 píxeles.
- La paleta de colores consta de varios tonos de rojo, más negro/transparente (que representa el LED apagado).
- Los escenarios requieren que se aplique paralaje para conseguir profundidad.

La buena noticia es que todo esto está acelerado por el **Video Image Processor** o 'VIP', un chip dedicado desarrollado por Nintendo. El VIP toma prestadas algunas características del antiguo [PPU](game-boy#graphics), aunque lo considero una clara ruptura con sus predecesores.

### Arquitectura

A primera vista, el VIP puede parecer otro motor de tiles más, pero es mucho más avanzado. Para empezar, no solo procesa datos gráficos, sino que también controla el escáner.

![El chip VIP.](vpu_chip.webp){latex_width="80%"}

Además, mientras que los motores de tiles clásicos renderizaban los gráficos por línea de escaneo, el VIP emplea una **arquitectura de frame buffer**, almacenando el frame final en memoria (como bitmap) y enviándolo posteriormente para su visualización. Esto se acerca más al modus operandi de los [renderizadores 3D](sega-saturn#graphics) modernos. De hecho, el Virtual Boy fue un paso más allá al implementar una forma de [**page flipping**](game-boy-advance#beyond-tiles), en la que se asignan dos frame buffers por unidad de visualización, lo que permite al escáner leer de uno mientras el VIP escribe en el otro. Todo esto ayuda a evitar el desgarro de imagen.

![Arquitectura del VIP.](_diagrams/vip.png){.open-float}

Dicho esto, el VIP puede dividirse en tres áreas principales:

- El **Pixel Processor** o 'XP': Genera fondos y sprites, de manera similar al PPU clásico. Este escribe el bitmap procesado final sobre la zona del frame buffer en memoria.
- El **Display Processor** o 'DP': Controla el escáner y envía un frame buffer para su visualización.
- Las **interfaces**: Proporcionan acceso a dos bloques de memoria: **128 KB de VRAM** y **128 KB de DRAM** [@cpu-ic]. Las interfaces también arbitran el acceso entre la VRAM y la CPU (denominada 'Host' desde la perspectiva del VIP).

{.close-float}

En general, el pipeline es sencillo:

1. La CPU configura el VIP escribiendo en sus registros internos y llenando la VRAM y la DRAM con los materiales necesarios.
2. El XP genera entonces los frame buffers, que se almacenan en VRAM.
3. El DP selecciona el frame buffer adecuado y lo transmite al escáner para su visualización. Para ello, copia el frame, cuatro columnas a la vez, en una pequeña zona de buffer en VRAM conocida como **Serial Access Memory** o 'SAM', que se emite automáticamente al escáner.

### Organizando el contenido

![Distribución de memoria del VIP](_diagrams/vip_content.png)

Desde la perspectiva del desarrollador, hay dos bloques de memoria utilizados por el XP y el DP:

- **128 KB de DRAM** para almacenar los datos gráficos que se van a procesar.
- **128 KB de VRAM** para almacenar los frame buffers a los que accede el DP.
  - Como hay dos unidades de visualización, el sistema requiere un total de **cuatro frame buffers**. Cada bitmap generado ocupa 24 KB, por lo que los desarrolladores deben asignar 96 KB de memoria. Los 32 KB restantes los utiliza el XP para almacenar **tiles**.

Conviene tener en cuenta que esos 128 KB de VRAM son DRAM de doble puerto 'real' [@cpu-toshiba], no simplemente un bloque de RAM reservado para gráficos (que Nintendo [también llama 'VRAM'](game-boy#organising-the-content)). La DRAM de doble puerto permite que dos dispositivos lean de ella al mismo tiempo, lo que explica por qué el XP puede escribir en la VRAM mientras el escáner lee de ella de forma simultánea.

![El chip VRAM.](vram_chip.webp){latex_width="90%"}

Además, Nintendo instaló un chip VRAM particular que no se encuentra en ningún catálogo convencional. Hay documentación limitada al respecto, pero según los detalles de la solicitud de patente [@graphics-patent], el **Serial Access Memory** (SAM) podría almacenarse en una zona separada dentro de este chip. La SAM está compuesta presumiblemente de SRAM y contiene circuitos adicionales que permiten al escáner leer 16 bits simultáneamente [@cpu-sharp].

### Construyendo un frame

Adentrémonos ahora en cómo se dibuja un único frame en el Pixel Processor. Para ilustrarlo, me serviré de los recursos de *Virtual Boy Wario Land*.

Antes de empezar, te recomiendo encarecidamente leer sobre un [motor de tiles anterior](game-boy#graphics), ya que reforzará los conceptos previos.

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel latex_subfigure_width="0.46"}

![La tabla de patrones con múltiples tiles agrupados.](wario/tiles.jpg){.active .border title="Todos"}

![Un tile individual.](wario/single.png){.border title="Individual"}

Tiles encontrados en VRAM.

:::

Como ya hemos [visto](game-boy#tab-1-1-tiles), los motores de tiles tradicionales construyen sus capas mediante bitmaps de 8×8. En el caso del Virtual Boy, los tiles (originalmente llamados 'Characters') se almacenan en VRAM dentro de una zona conocida como **Pattern table**. Cada tile ocupa 16 bytes (ya que se asignan 2 bits por píxel), por lo que hay espacio suficiente para 2048 tiles.

En cuanto a los colores, los desarrolladores pueden componer **ocho paletas de colores**: cuatro para los gráficos de fondo y otras cuatro para los sprites.

Puede que te preguntes para qué sirven las paletas de colores si los LEDs son monocromos. Bien, esta configuración permite a los desarrolladores usar **diferentes tonos de rojo**. Estos tonos se obtienen alterando el brillo de los LEDs.

Los ajustes de brillo se almacenan en dos registros, a los que luego hacen referencia las paletas, creando de facto un catálogo de diferentes tonos. Todas las paletas resultantes contienen:

- Dos rojos personalizados.
- Un rojo adicional calculado automáticamente a partir de la suma de los dos anteriores.
- El color 'transparente'.

#### Fondos {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=3 latex_subfigure_width="0.49"}

![Capa de fondo 0 (BG0).](wario/background_1.png){.active .border title="BG0"}

![Capa de fondo 1 (BG1). Esta permanece mayormente oculta durante el juego, y se vuelve completamente visible cuando se pausa.](wario/background_2.png){.border title="BG1"}

![Capa de fondo 2 (BG2).](wario/background_3.png){.border title="BG2"}

Algunas capas de fondo declaradas.

:::

La capa de fondo es muy sencilla: se seleccionan tiles para formar un mapa de 512×512 (64×64 tiles, lo que supone un total de 4096 tiles). Pero eso no es todo, porque en lugar de disponer de una única capa de fondo... ¡hay **14**!

Cada capa de fondo individual recibe el nombre de **segmento**. Un segmento ocupa 8 KB de memoria, y cada referencia de tile almacena atributos de **inversión horizontal/vertical** y un **índice de paleta**. Cada referencia de tile consume 2 bytes de memoria.

El Pixel Processor también permite combinar varios segmentos para generar una capa mayor, aunque solo están disponibles ciertas combinaciones. La mezcla más grande combina ocho segmentos.

#### Sprites {.tab}

Los sprites (u 'Objects', como los llama Nintendo) son tiles individuales con coordenadas independientes, pero requieren más memoria.

Hay una zona en DRAM llamada **Object Attribute Memory** (OAM) con 8 KB de memoria asignada, donde se almacenan las definiciones de sprites. Cada definición ocupa 8 bytes, lo que permite declarar hasta 1024 sprites.

Cada sprite incluye las siguientes propiedades:

- Coordenadas X e Y.
- Un flag que determina dónde se muestra (pantalla izquierda, derecha o ambas).
- El **desplazamiento de paralaje**, que aplica paralaje automático. Esto hace que cada pantalla muestre el sprite con un ligero desplazamiento horizontal.
  - Este valor es **con signo**: un valor positivo lo hace parecer más lejano, mientras que un valor negativo lo hace parecer más cercano.
- El índice del tile utilizado.
- Inversión horizontal/vertical.
- El índice de paleta.

Notarás que en esta sección no hay capturas de pantalla de ejemplo; los siguientes párrafos explican por qué.

#### Window {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=4 latex_subfigure_width="0.49"}

![World 1.](wario/window_1.png){.active .border title="World 1"}

![World 2. Este se renderiza en ambas pantallas.](wario/window_2.png){.border title="World 2"}

![World 3. Este se amplía cuando se pausa el juego.](wario/window_3.png){.border title="World 3"}

![World 4. Esta es la 'capa de sprites' que te debía de antes.](wario/window_4.png){.border title="World 4"}

Ejemplos de windows. Algunos se renderizan en ambas pantallas (con efectos de paralaje), mientras que otros son exclusivos de una.

:::

El sistema de capas puede parecer sencillo a primera vista. Al fin y al cabo, el VIP proporciona capas de fondo y una capa de sprites. ¿Qué más se necesitaría?

En la práctica, para mostrar cualquiera de las capas anteriores, deben colocarse en un contenedor llamado **Window** (también denominado 'World'). Una window es el plano real que se renderiza en la pantalla, y se rellena con las capas construidas previamente. Hay **32 windows disponibles** que se superponen para formar el frame final, y cada definición de window ocupa 32 bytes.

Las windows ofrecen varios **modos de renderizado**. Los desarrolladores pueden seleccionar una capa de fondo o de sprites y mostrarla tal como está. Para ello, la window debe configurarse en **Normal mode** u **Object mode**, según el tipo de capa.

Sin embargo, el chip ofrece modos adicionales que aplican efectos extra a las capas de fondo:

- **Line Shift Mode**: Las filas de píxeles individuales pueden desplazarse horizontalmente. Esto recuerda a los efectos de toda la vida provocados durante las [interrupciones horizontales](game-boy#the-wobble-effect).
- **Affine Mode**: Como su nombre indica, activa las [transformaciones afines](super-nintendo#that-feature). Es decir, escalado, rotación, o una combinación para simular la proyección en perspectiva [@graphics-tucker].

#### Resultado {.tab}

![¡Tachán!](wario/frame.jpg){.tab-float .pixel}

Tras configurarlo todo, el Pixel Processor comienza a renderizar las 32 windows. Al terminar, el frame final se vuelca en la zona del frame buffer. Este proceso se repite también para la otra unidad de visualización.

Dado que el sistema **emplea un diseño de doble buffer**, el Display Processor siempre lee el frame buffer que el Pixel Processor no está manipulando en ese momento, evitando así el desgarro de imagen. Durante el siguiente período activo, el Pixel Processor sobrescribe el frame que mostraba antes el Display Processor, y el ciclo continúa.

Si hay poco contenido que renderizar (es decir, se usan pocas windows), habrá largos intervalos entre la escritura en el frame buffer y su recuperación por parte del Display Processor. Esto permite a la CPU realizar cambios adicionales en el frame si es necesario. El VIP también está preparado para esto: la CPU puede configurar interrupciones para monitorizar diversos estados del VIP, incluido este escenario.

Por otro lado, si se renderizan demasiadas windows en Affine Mode, el Pixel Processor puede *no cumplir el plazo*, lo que provoca caídas de frame. Por fortuna, también hay interrupciones disponibles para detectar esta condición. En cualquier caso, la documentación oficial proporcionaba los tiempos para cada tipo de capa.

### Contenido creativo {.tabs-close}

Como puedes ver, hay mucha más tecnología en esta consola de lo que parece a simple vista.

::: {.subfigures .tabs-nested .tab-float .open-float .pixel max_subfigures=3 latex_subfigure_width="0.49"}

![Mapa de fondo inicial.](tennis/affine_map.png){.active .border title="Mapa"}

![Mapa renderizado, con proyección en perspectiva aplicada.](tennis/affine_window.png){.border title="Window"}

![Frame que ve el usuario.](tennis/affine_frame.jpg){title="Frame"}

Disección del Affine Mode, con *Mario's Tennis* (1995) como ejemplo.

:::

En un principio, creía que la [Game Boy Advance](game-boy-advance) era la primera consola portátil capaz de reproducir el aclamado [efecto Mode 7](super-nintendo#that-feature) de la Super Nintendo, casi 11 años después. Resulta que fue el Virtual Boy quien lo logró primero, solo cinco años después. Y podía ir más lejos todavía: como hemos visto, las transformaciones afines en el Virtual Boy podían aplicarse a las 32 capas (aunque con ciertas limitaciones).

Además, todas estas nuevas funciones operaban en combinación con los efectos de paralaje, algo de lo que también se encargaba el VIP.

No puedes evitar preguntarte qué tipo de juegos habrían surgido si esta consola hubiera durado un poco más, el tiempo suficiente para que los desarrolladores se familiarizaran con este hardware.

{.close-float}

Otra característica digna de mención era la capacidad de la CPU para modificar el frame buffer directamente, lo que ofrecía a los desarrolladores la flexibilidad de construir su propio renderizador cuando el VIP no era suficiente. Así es como algunos juegos lograron mostrar sus innovadores gráficos. Por ejemplo, *Red Alarm* implementó un escenario compuesto de wireframes 3D renderizados por la CPU.

::: {.subfigures .side-by-side .pixel}

![Red Alarm (1995).](redalarm.jpg){.toleft paperback_latex_width="97%"}

![Waterworld (1995).](waterworld.jpg){.toright paperback_latex_width="97%"}

Ambos juegos apenas utilizan el VIP para dibujar, dejando a la CPU encargarse de los gráficos principales.

:::

Por desgracia, desafíos fundamentales como la [determinación de superficie visible](sega-saturn#an-introduction-to-the-visibility-problem) no siempre se resolvieron correctamente. Sospecho que esto se debió a las limitaciones de la CPU. En cualquier caso, esto derivó en escenas que se convertían en una maraña de mallas, lo que dificultaba al jugador distinguir qué objetos estaban detrás de otros.

## Audio

Imagina que tomas el [canal de onda](game-boy#tab-2-2-wave) de la Game Boy, lo multiplicas por cinco y añades un canal de ruido: eso es, en esencia, lo que ofrece el chip de sonido del Virtual Boy. También puede considerarse hermano del [sistema de audio de la PC Engine](pc-engine#audio).

![Visualización en osciloscopio de *Mario's Tennis* (1995), mostrando todos los canales de audio.](tennis){.open-float .negate .border video="true"}

En la placa base hay un chip llamado **Virtual Sound Unit** (VSU) que proporciona las capacidades de sonido. La VSU se apoya en RAM interna para almacenar **cinco wavetables** y un banco de registros para configurar cada uno de los **seis canales disponibles** (cinco de onda y uno de ruido).

Los registros son de 8 bits de ancho, mientras que la RAM interna está conectada a un bus de datos de 6 bits. La CPU sigue tratando las palabras de 6 bits como un byte, descartando los dos bits superiores.

Cada wavetable consta de 32 muestras PCM codificadas en valores de 6 bits. Los canales admiten control de paneo (mediante un valor de nivel de volumen, de 0 a 15, para cada canal, izquierdo y derecho) y un control de frecuencia de 11 bits.

{.close-float}

Cada canal también incluye un control básico de envolvente, que permite que el volumen aumente o disminuya con el tiempo.

El quinto canal ofrece más efectos: concretamente, una **función de barrido** para desplazar progresivamente la frecuencia, y un **modulador** para alterar la waveform en función de los valores almacenados en la RAM interna.

Por su parte, el sexto y último canal solo puede generar **ruido**.

### Salida

La salida mezclada es **estéreo**, con una **resolución de 10 bits** y una frecuencia de muestreo de **41,7 kHz**. Cabe señalar también que la consola tiene altavoces estéreo, así que no necesitas ponerte auriculares para disfrutarlo.

## E/S

El Virtual Boy está clasificado técnicamente como 'portátil', así que no esperes accesorios revolucionarios. Dicho esto, aún hay algunos complementos que merecen mención.

### Interfaces disponibles

Internamente, casi todos los componentes están conectados directamente a la CPU, excepto algunas zonas gestionadas en exclusiva por el VIP.

![Vista de la parte inferior de la carcasa (lado derecho) [@photography-amos], mostrando el puerto serie y el puerto de comunicaciones.](case/underside.png){.open-float}

Externamente, la consola dispone de dos conectores para accesorios:

- El **puerto serie**: Conecta el **mando**, con datos transferidos en serie (un bit cada vez) [@cpu-diagrams]. La interfaz adapta la transmisión a un valor de 16 bits para que lo lea la CPU. A su vez, la CPU interpreta cada posición de bit como una entrada de botón. También se pueden configurar interrupciones para notificar a la CPU en el instante en que se pulsa cualquier tecla.
  - Una de las patillas de este conector suministra **5 voltios** del mando a la consola, en esa dirección. ¡Esto es lo que **alimenta la consola**!
- El **puerto de comunicaciones**: Aunque no es un accesorio en sí, este puerto permite la comunicación entre dos Virtual Boys. Esto me recuerda al [cable Link](game-boy#external-communications) de la Game Boy. Los dos Virtual Boys se comunican de forma maestro-esclavo mediante flujos de datos en serie.
  - Por desgracia, ningún juego llegó a utilizar esta función. Sin embargo, te lleva a preguntarte qué tipo de funcionalidad multijugador estaba imaginando Nintendo.

{.close-float}

### El proveedor

El mando del Virtual Boy es bastante peculiar comparado con el resto de la línea de consolas. Es algo similar al de la Super Nintendo, aunque sin los botones 'X' e 'Y', con un agarre más grande y un D-Pad adicional a la derecha. Lo más llamativo es que fue diseñado para que el jugador **no necesitara mirarlo durante la partida**.

![El mando [@photography-amos]. Observa el deslizador en la parte superior central; eso es el interruptor de encendido.](io/controller.png){.tabs-nested .active .open-float .tab-float latex_width="90%" title="Mando"}

![Cartucho de pilas encajado en la parte trasera [@photography-amos].](io/batterypack.png){.tab-nested latex_width="90%" title="Pack de pilas"}

![Cartucho alternativo (vendido como accesorio aparte) [@photography-amos] que realiza la conversión de CA a CC, eliminando la necesidad de pilas. Obtiene la energía del transformador de la SNES.](io/acpack.png){.tabs-nested-last latex_width="90%" title="AC Pack"}

Además, el mando también estaba pensado para alimentar la consola, por lo que lleva un compartimento de pilas extraíble en la parte trasera. El cartucho, denominado **Tap**, aloja **seis pilas AA**. Para los usuarios que se hartaban de buscar repuestos cada vez que una de las seis se agotaba, Nintendo ofrecía un Tap alternativo que se conecta a una fuente de alimentación de CA externa.

He de admitir que cuando probé el Virtual Boy con el AC Tap conectado (ya que encontrar seis pilas cargadas es todo un suplicio), jugar a cualquier juego se convertía en un auténtico malabarismo: tenía que controlar los cables sin poder apartar los ojos del visor. ¡Pero supongo que ese es el precio a pagar si prefieres no preocuparte por las pilas!

{.close-float}

## Sistema operativo

El V810 inicia la ejecución en la dirección `0xFFFFFFF0H`, lo que significa que al encender el Virtual Boy, busca instrucciones en esa ubicación. Dicho esto, esa dirección se encuentra dentro de la ROM del cartucho, lo que otorga al juego el control sobre la inicialización del hardware.

Además, no hay ningún chip de BIOS en la placa base, por lo que no existe ningún sistema operativo ni capa de abstracción que simplifique las operaciones.

### Los quehaceres

Por esta razón, Nintendo instruyó a los desarrolladores para que realizaran varias tareas que garantizaran el correcto funcionamiento de la consola:

- La WRAM solo es utilizable después de 200 microsegundos (0,0002 segundos) tras el arranque de la consola. La documentación incluye una lista de pasos que los juegos deben seguir para tener en cuenta este retraso.
  - Muchos años después, fuentes no oficiales afirmaron que esto era una errata de Nintendo, y que en realidad es la DRAM del VIP la que necesita ese tiempo de espera, como corroboran los datasheets [@operating_system-dram].
- El juego debe rellenar la Column Table. Recordando explicaciones anteriores, el período de 'visualización activa' está sujeto a una velocidad angular variable. Para evitar que ciertas columnas del frame aparezcan más estrechas que otras, los juegos rellenan una tabla en DRAM que aplica correcciones. Para facilitar las cosas, Nintendo proporcionó una Column Table predeterminada en su SDK, que los juegos podían utilizar.
  - La tabla también puede modificarse en cualquier momento durante el juego y, dado que controla el tiempo de encendido de los LEDs, también sirve como mecanismo para ajustar el nivel de brillo por columna.

## Juegos

Podría asumirse que el desarrollo de juegos simplemente heredaría las mismas herramientas que la Game Boy (es decir, *ensamblador puro*), pero resulta que Nintendo y NEC invirtieron en nuevas tecnologías para modernizar y acelerar el desarrollo de terceros.

### Ecosistema de desarrollo

Los estudios de juegos tenían la opción de adquirir un **kit de desarrollo** de Nintendo, que incluía una estación de depuración completamente equipada, una cadena de herramientas y abundante documentación.

El kit de hardware se llamaba **VUE Development System** ('VUE' era el nombre en clave de esta consola). Era una torre similar a un PC que albergaba los componentes internos del Virtual Boy, más 4 MB de RAM (ampliable hasta 8 MB). Se conectaba a un visor similar al Virtual Boy comercial y a un mando comercial. Para ejecutar y depurar software, el kit de desarrollo incluía una placa de interfaz que se comunicaba con un IBM PC mediante SCSI. Para manejar el equipo, los desarrolladores necesitaban un IBM PC/AT (o clon compatible) con una CPU Intel 80386, 2 MB de memoria y MS-DOS instalado.

El kit de software incluía un enlazador, un ensamblador y un depurador. Bajo petición, Nintendo también ofrecía un **compilador de C**: los desarrolladores ya no necesitaban escribir programas directamente en ensamblador. En conjunto, esta configuración ocupaba 1,5 MB de tu preciado —y ruidoso— disco duro.

Es una lástima que este modelo de desarrollo se revirtiera más tarde con el lanzamiento de la Game Boy Color. Supongo que se debió a que la CPU de la [Game Boy Color](game-boy#the-color-effect) no era capaz de manejar código 'no optimizado' de un compilador.

### Soporte

Los cartuchos de juego se denominan **Game Paks**, el mismo nombre que se usa para el soporte de la Game Boy, aunque difieren completamente en forma y funcionalidad.

![Ejemplo de un juego comercial.](wario.jpg){.open-float}

Gracias al espacio de direcciones de memoria disponible, la ROM puede alcanzar hasta **16 MB sin mapeador**. Lo mismo se aplica a la RAM externa. Al igual que la Game Boy, los cartuchos pueden incluir SRAM con respaldo de batería. El acceso al cartucho se gestiona mediante un **bus de datos de 16 bits**.

Pensándolo bien, 16 MB de ROM es bastante para mediados de los 90. Al fin y al cabo, el tamaño máximo de una ROM de Game Boy era de solo 128 KB (sin mapeador). Aunque no hay que olvidar que esta consola usa direccionamiento de 32 bits, por lo que cada dirección ocupa 4 bytes. Eso es el doble que las direcciones de 2 bytes de la Game Boy, lo que incrementa los requisitos de espacio.

{.close-float}

### Normativa

Por razones de seguridad, Nintendo ordenó a los desarrolladores incluir una serie de pantallas destinadas a mitigar la fatiga ocular y otras afecciones asociadas al uso prolongado de la pantalla.

Así, todos los juegos mostraban un aviso de 'lee las instrucciones', una guía de alineación y un diálogo de pausa automática.

![La pantalla de 'lee las instrucciones'.](rules/instructions.jpg){.tabs-nested .active .open-float .tab-float .pixel title="Instrucciones"}

![La guía de alineación de pantalla.](rules/align.jpg){.tab-nested title="Alineación"}

![El diálogo de pausa automática.](rules/pause.jpg){.tabs-nested-last title="Pausa automática"}

Estas pantallas aparecen tras encender la consola:

- La primera 'ordena' al usuario leer el manual de instrucciones antes de continuar.
- La segunda es una especie de 'carta de ajuste', utilizada para calibrar correctamente el dial IPD y el control de enfoque.
- La tercera pide al usuario que elija si desea recibir un recordatorio para hacer un descanso cada treinta minutos.

{.close-float}

La primera y la tercera pantalla solían personalizarse para adaptarse al tema del juego.

Creo que Nintendo no volvió a imponer directrices similares hasta el lanzamiento de la [Wii](wii#return-to-home).

## Antipiratería y homebrew

El hecho de que Nintendo creara una variante más del Game Pak les permitió mantener el control sobre la distribución. Ahora bien, como esta consola nunca llegó a despegar, los piratas probablemente ni se molestaron.

Además, la consola **carece tanto de protección anticopia como de mecanismos de region lock**. Los juegos importados de América y Japón funcionarán en cualquiera de las dos consolas. Por otra parte, suponiendo que alguien esté dispuesto a fabricar sus propios Game Paks, el desarrollo homebrew también es posible sin restricciones.

La última vez que lo comprobé, los [flashcarts](game-boy-advance#flashcarts) eran teóricamente posibles, aunque solo unos pocos han llegado a comercializarse en los últimos años.

## ¡Esto es todo, amigos!

![Mi hermano trasteando con el Virtual Boy... el único miembro que queda del equipo de testeo...](brother.jpg)

A lo largo de esta serie, hemos visto todo tipo de sistemas: algunos con CPUs modestas pero con sinergias impresionantes, otros con hardware cuestionable pero con un packaging impecable. Este caso concreto, sin embargo, presentó una propuesta interesante: la CPU estaba lejos de ser limitada, y el hardware gráfico estaba a la altura. El sonido no era sobresaliente, pero resultaba comparable al de otras consolas portátiles de la época.

Entonces, ¿por qué no jugaron más niños con ella? Me temo que un estudio de marketing excede el ámbito de este artículo. Pero desde el punto de vista técnico, sospecho que el mensaje simplemente no caló. Al fin y al cabo, a nadie le importan las transformaciones afines ni una CPU con pipeline si lo único que ven son puntos rojos.

Supongo que esa fue mi principal motivación para escribir este artículo: descubrir tecnología innovadora enterrada en los lugares más inesperados.

También me gustaría dar las gracias a la comunidad de *Planet Virtual Boy* por su ayuda con el estudio. Actualmente trabajan en muchos proyectos que siguen dando nueva vida al Virtual Boy, así que te recomiendo visitar su foro para ampliar información.

Por último, con esto se cierra la última entrega de la 'saga retro'; la siguiente volverá a los renderizadores (relativamente) modernos, ¡empezando por la PSP!

¡Hasta la próxima!  
Rodrigo
