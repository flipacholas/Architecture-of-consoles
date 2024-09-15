---
long_title: Arquitectura de la Wii
short_title: Arquitectura de la Wii
name: Wii
subtitle: Técnicas únicas de innovación
date: 2020-01-05
release_date: 2006-11-19
generation: 7
published: true
cover: wii
javascript:
  - 'threejs'
seo_image_pos: "Top"
top_tabs:
  Model:
    file: internacional
    caption: "Le Wii.<br>Lanzada el 19/11/2006 en América, el 02/12/2006 en Japón y el 08/12/2006 en Europa."
  Motherboard:
    caption: "Mostrando la revisión \"RVL-CPU-40\", las revisiones anteriores tenían un proceso de fabricación significativamente más grande y las posteriores eliminaron la mayor parte del I/O de la GameCube.<br>La NAND Flash está instalada en la parte posterior."
    bib_source: photography-okqubit
  Diagram:
    caption: Los buses de datos importantes están etiquetados con su ancho y velocidad.
#Historical
aliases:
  - /projects/consoles/wii/
---

## Una introducción rápida

Aunque la Wii carecía de los gráficos de última generación que disfrutaban sus competidores, los nuevos tipos de mandos y el software innovador le dieron a esta consola nuevas áreas de las que presumir.

Aquí analizaremos todos los aspectos de esta consola, desde su hardware ya familiar hasta su sistema de seguridad descuidado, incluidas sus principales fallas.

Nota rápida: Algunas secciones se superponen con parte del artículo anterior sobre [GameCube](gamecube), por lo que en lugar de repetir la información simplemente pondré un enlace a la parte respectiva del artículo.

## {.supporting-imagery}

## Mandos de nueva generación

Comencemos discutiendo uno de los aspectos más icónicos de la consola: los mandos.

El dispositivo principal no es otro más que el ** Wii Remote** (también llamado "Wiimote"), un dispositivo con una forma similar a la de un mando a distancia de televisión que contiene muchos tipos de controles de entrada:

- Para empezar, tiene un **conjunto de botones físicos** que son usados como cualquier otro mando convencional.
- También posee un **acelerómetro** para detectar cambios de orientación, este es el componente principal usado para la detección de movimiento.
- Finalmente, incluye una **cámara infrarroja** que, combinada con el acelerómetro y un poco de procesado del Wii, puede ser usado para apuntar a la pantalla.
  - Este sensor requiere de la **Barra de Sensores** (incluida con la consola). La barra consiste de dos conjuntos de LED infrarrojos que la cámara puede detectar, la Wii hace uso de una triangulación para calcular la posición del Wiimote respecto a la TV.

El chip principal del mando es el **Broadcom BCM2043** [@io-wiimote], un chip que incluye todos los circuitos necesarios para convertirse en un dispositivo Bluetooth independiente (CPU, RAM, ROM y, por supuesto, un módulo Bluetooth). Mientras que el Wiimote está programado para seguir el protocolo de interfaz humana "Bluetooth HID" para ser identificado como un dispositivo de entrada, este no cumple con el método estándar para intercambio de datos (posiblemente para prevenir el uso en sistemas diferentes a la Wii).

Por último, el Wiimote también incluye **16KB de memoria EEPROM** para almacenar datos del usuario y un **pequeño altavoz** limitado a muestreo de baja calidad (3KHz 4-bit ADPCM o 1.5KHz 8-bit PCM).

### Expansiones

Nintendo lanzó este sistema con otro mando para ser usado en la otra mano, el **Nunchuck**, este posee su propio acelerómetro, un joystick y dos botones. Se conecta mediante un puerto propietario de 6 pines al Wiimote.

![Nunchuk y Wiimote [@photography-amos], respectivamente.](controllers/both.jpg)

Otros accesorios fueron diseñados también para este puerto, cada uno provisto de diferentes tipos de entradas.

## CPU

Después del éxito del Gekko, se presume que IBM tomo este diseño y lo relanzó como el '750CL' para que otros fabricantes lo utilizaran[@cpu-ibmcl]. Después, cuando Nintendo solicito una nueva CPU para usar con en su nueva consola, aun conocida como 'Revolution' (de aquí el prefijo RVL en las placas madre), IBM y Nintendo acordaron usar el 750CL a una velocidad de reloj 1.5 veces mayor que el Gekko. Esta CPU es conocida como **Broadway** [@cpu-ibmuser] y funciona a **729MHz**.

Después de haber revisado el [Gekko](gamecube#cpu), me temo que no existen tantos cambios en la nueva CPU. Sin embargo, esto puede ser una ventaja: los desarrolladores de GameCube pudieron comenzar a desarrollar nuevos juegos para la Wii de inmediato gracias a la experiencia que habían adquirido con el Gekko. Además, el hecho de que el Broadway funciona a 1.5 veces la velocidad original les permitió implementar más características y calidad.

### ¿Qué ocurre con la memoria?

Esto es algo interesante, el diseño de memoria de la GameCube fue reorganizado y mejorado con los siguientes cambios:

- Splash (24 MB de 1T-SRAM) ahora se ubica dentro del SoC del Hollywood (explicado después) y es llamado **MEM1** [@operating_system-memory].
- ARAM (16MB de SDRAM en serie) ha **desaparecido**, sin embargo...
- Hay un nuevo chip de memoria, **MEM2**, que incluye **64 MB de SDRAM GDDR3** de propósito general.
  - Este tipo de memoria está basado en el sistema tradicional DDR2, pero mejorado con anchos de banda más altos (aproximadamente dos veces la tasa de transferencia original) y consumo energético reducido, lo cual es ideal para las GPU.

### Retrocompatibilidad

Por ahora, puedes pensar que esta consola es una GameCube mejorada y, por lo tanto, la compatibilidad con juegos de la generación anterior es heredada de forma nativa. Dicho esto, para hacer que la Wii logre *completa retrocompatibilidad*, el viejo conjunto de puertos fue llevado a la Wii, entre ellos el puerto de mandos de GameCube y los puertos de memoria. Sin embargo, hay existe una nueva restricción: El nuevo mapa de memoria es incompatible con el viejo. Por lo tanto, se implementó una fina capa de "emulación" en el software (más detalles en la sección de "I/O" y "Sistema Operativo").

Respecto a los accesorios de la GameCube que usaban los socket Serial/Hi-Speed, me temo que la Wii no incluyó estos puertos, por lo que estos accesorios no pueden ser usados en ella.

Desafortunadamente, en años posteriores, estos puertos fueron eliminados de las nuevas revisiones de la Wii.

## Gráficos

Del mismo modo que la GameCube (donde la unidad gráfica, interfaces I/O y capacidades de audio fueron combinadas en un único paquete llamado "Flipper"), la Wii alberga un gran chip al lado del Broadway llamado **Hollywood**. Aquí encontramos el subsistema gráfico que, para ser sinceros, es idéntico al Fipper exceptuando algunas correcciones mínimas.

Por lo tanto, la GPU del Hollywood aún desempeña las mismas tareas que [su homólogo el Flipper](gamecube#graphics) realizaba entonces, pero ahora goza de **1.5 veces** la velocidad de reloj (**243 MHz**). Este aumento significa que se pueden procesar más geometría y efectos durante la misma unidad de tiempo.

### Funcionalidad

Ya que el motor 3D sigue siendo el de [Flipper](gamecube#graphics) en lugar de repetir la misma descripción general del proceso, mencionaré algunos cambios de diseño interesantes que los juegos tuvieron que sufrir:

#### Panorámica estandarizada {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Modo 4:3.](mario/4_3.png){.active title="Tradicional"}

![Modo 16:9, compuesto por el codificador de vídeo.](mario/16_9.png){title="Ancha"}

![Modo 16:9, como se muestra en un televisor de pantalla ancha.](mario/16_9_stretched.png){title="Estirada"}

Super Mario Galaxy (2007).

:::

Los juegos de GameCube carecían del soporte adecuado para pantallas panorámicas (es decir, que componían fotogramas 16:9, alejándose del tradicional 4:3). Sin embargo, la GPU de Flipper ya era capaz de hacerlo y un puñado de juegos ofrecían opciones para activarlo, aunque todavía se consideraba una característica exclusiva.

Sea como fuere, el framebuffer sigue siendo idéntico y el codificador de vídeo sigue emitiendo un fotograma compatible con PAL o NTSC, por lo que esta característica "panorámica" se consigue en cambio **ampliando el campo de visión** en la matriz de proyección. El resultado es una escena renderizada con un ángulo de visión mayor que ahora aparece aplastada horizontalmente. Sin embargo, el televisor panorámico también interviene en este proceso, ya que posteriormente estirará el fotograma 4:3 (procedente de la consola) y la imagen visualizada se verá así *más o menos* en la proporción correcta. Si tienes curiosidad, esta técnica no es nueva, se ha utilizado en la proyección de películas y se denomina **pantalla panorámica anamórfica**. Además, es curioso cómo los desarrolladores de SNES tuvieron que lidiar con el [efecto contrario](super-nintendo#display-modalities).

Volviendo al tema, la Wii estandarizó esta característica al permitir activar un "modo panorámico" desde sus ajustes de sistema, lo que a su vez promovió su _amplia adopción_ (valga el juego de palabras).

#### Interacción con la pantalla {.tab}

![Super Mario Galaxy (2007).<br>Puedes recoger las estrellas apuntando a ellas.](mario/mario_galaxy.png) {.tab-float}

El nuevo y disruptivo diseño del mando supuso nuevos tipos de interacción en los juegos de Wii. Dado que el Wiimote permitía a los usuarios apuntar a la pantalla, algunos juegos como *Super Mario Galaxy* o *The Legend of Zelda: Twilight Princess* utilizaban esta función para permitir al jugador interactuar con el escenario.

En el informe titulado _Depuración de Mitos: ¿es más exigente emular la Wii que la GameCube?_[@graphics-dolphin], los desarrolladores del emulador Dolphin explican que los juegos como Super Mario Galaxy y otros juegos de disparos en primera persona dependen del[ Z-Buffer integrado](gamecube#tab-1-4-render) para identificar el objeto al que el Wiimote está apuntando y/o revisar que tan lejos está el objeto del cursor del Wiimote.

No se trata de una función nueva en sí, sino de un uso novedoso de las capacidades actuales. Los juegos de GameCube no dependían de un mando multiusos con función de puntero. Ahora, los jugadores pueden controlar al personaje y apuntar a la pantalla al mismo tiempo.

### Diseños actualizados {.tabs-close}

Los megahercios adicionales del Broadway y del Hollywood, combinados con diseños vanguardistas, aportaron algunas mejoras a los modelos de los personajes. Puede que no sea tan significativo como el de otras generaciones, pero aún así se nota y se aprecia.

![Super Smash Bros. Melee (2001) para la GC.<br>4.718 triángulos.](mario_melee_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) para Wii.<br>5.455 triángulos.](mario_brawl_wii){.toright model3d="true"}

![Sonic DX (2003) para la GC.<br>1.985 triángulos.](tails_dx_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) para Wii.<br>3.644 triángulos.](tails_brawl_wii){.toright model3d="true"}

### Señal de vídeo

Sorprendentemente, esta consola ya no utiliza el antiguo puerto [Multi Salida](super-nintendo.md#a-convenient-video-out), sino una variación del mismo llamada **AV Multi Salida** (vaya nombre) con una forma ligeramente diferente. Este lleva todas las señales anteriores más **YPbPr** (conocida como " vídeo por componentes") [@graphics-video]. También incluye algunas líneas de datos que el sistema utiliza para identificar el tipo de cable conectado.

Por desgracia, este medio hereda las mismas [limitaciones](gamecube.md#connections) de la GameCube. Es decir, no hay S-Video en los sistemas PAL ni RGB en los NTSC. Además, RGB sólo puede emitir señales entrelazadas (no progresivas).

## Audio

La Wii incluye el mismo **Macronix DSP** que se encuentra en la [GameCube](gamecube#audio), puedes echar un vistazo a ese artículo para ver el análisis detallado.

En comparación con la GameCube, el único cambio importante es que, al haber desaparecido la ARAM, se puede utilizar MEM1 o MEM2 como búfer de audio.

## E/S

El subsistema I/O de esta consola es un cambio de juego (*si me permites el juego de palabras*). Ahora las interfaces están controladas por un único módulo que también se ocupará de la seguridad. Estoy hablando del **Starlet**.

### El co-procesador oculto

El Starlet no es más que una CPU **ARM926EJ-S** conectada a la mayoría de los componentes internos de esta consola. Reside dentro del Hollywood, funciona a **243 MHz** (igual que el Hollywood) y contiene también su propia ROM y RAM. Por lo tanto, puedes considerar al Starlet un ordenador independiente que funciona junto a la CPU principal.

![Diagrama principal de la arquitectura de la Wii. Observe cómo Starlet es capaz de controlar la mayor parte del I/O e incluso ocultar algunos del Broadway.](diagram.png)

El núcleo es similar al utilizado en la [Nintendo DS](nintendo-ds), salvo por la inclusión de dos añadidos "especiales":

- Una "J" en su nombre de modelo, que denota la inclusión de **Jazelle**: una unidad dedicada que ejecuta Bytecode Java de 8 bits. Los programas Java seguirían dependiendo de la máquina virtual (conocida como "JVM"), pero algunos opcodes podrían ejecutarse directamente desde la CPU. En general, esto podría acelerar la ejecución del código Java compilado.
- Una **unidad de gestión de memoria** (MMU) dedicada para habilitar la memoria virtual. Útil para sistemas operativos de uso general.

Estas mejoras son un poco "raras", ya que no se utilizan en la Wii. No obstante, Nintendo seleccionó ese núcleo para el Starlet. Esto me recuerda al primer iPhone (2G), que también incluía una CPU ARM con Jazelle (también desperdiciada).

Si te lo estás preguntando, Jazelle nunca tuvo éxito. Después de algunas iteraciones, se descubrió que Java Bytecode simplemente funcionaba mejor en el software. Más tarde, ARM sucedió a Jazelle con "Thumb-2EE" y, en el momento de escribir este artículo (junio de 2021), ambas unidades han sido retiradas.

::: {.subfigures .distributed .open-float .float max_subfigures=1}

![](console/front.jpeg)

![](console/back.jpeg)

I/O externos en la Wii.<br>La oscura & y pequeña ranura frontal es un lector de tarjetas SD.

:::

Más adelante, esta "CPU de I/O" se encarga de arbitrar el acceso entre muchos I/O y el Broadway, y al hacerlo también se ocupa de la seguridad (que decide si permite el acceso o no). Esto es especialmente crucial cuando se trata de conceder acceso a la NAND, por ejemplo, que es donde se almacenan el sistema operativo principal y los datos del usuario.

El chip también hereda algunas tecnologías de ARM, como la **arquitectura de bus de microcontroladores avanzados** (AMBA), un protocolo que facilita la comunicación entre dispositivos mediante un conjunto de buses especializados.

{.close-float}

Dicho esto, Nintendo ha cableado el I/O de forma que hace uso de dos buses AMBA [@io-diagram]:

- El **Bus AHB** (bus AMBA de alto rendimiento): como su nombre indica, está diseñado para comunicaciones de alta velocidad. Aquí lo encontramos:
  - La interfaz **NAND**: accede a 512 MB de la NAND Flash que almacenan el sistema operativo y los datos de usuario.
  - Dos interfaces **Secure Digital Input Output** (SDIO): SDIO es un protocolo diseñado principalmente para acceder a una tarjeta SD, pero en este caso se utiliza una segunda para controlar también el módulo Wi-Fi (802.11 b/g).
  - Un controlador **USB 2.0**: sirve de interfaz para dos tomas USB externas y una tarjeta hija Bluetooth 2.0 interna.
  - Un módulo **SHA-1** y **AES**: Reservado para tareas de seguridad (más detalles en la sección Anti-Piratería).
- El **Bus APB** (Bus Periférico Avanzado): este está restringido a componentes de bajo rendimiento, incluyendo:
  - La **interfaz de la unidad de discos**: conecta el lector de discos.
  - La **interfaz serie**: conecta los mandos de GameCube.
  - La **interfaz externa** (EXI): Esta ya la hemos visto [antes](gamecube#internal-io). Se comunica con otro hardware de GameCube, utilizado para la retrocompatibilidad.

### Manteniendo la compatibilidad

![Wii usando equipamiento de GameCube [@photography-amos].](gamecube-mode.png) {.open-float.no-borders}

La Wii mantiene total compatibilidad con los juegos de la GameCube aunque el sistema de I/O haya cambiado drásticamente. Esto se debe a que el Starlet puede ser reprogramado cuando un juego de GameCube se ejecuta virtualmente para remapear el I/O, dejándolo igual que la GameCube original esperaría encontrar.

Además, el chip Real-Time Clock incluye alguna ROM de repuesto que almacena fuentes de mapa de bits (el conjunto latino y japonés) utilizadas por los juegos de GameCube; y SRAM para guardar ajustes [relacionados con IPL](gamecube#operating-system).

{.close-float}

## Sistema operativo

Generalmente hablando, hay **dos sistemas operativos** que conviven en la Wii. Uno es ejecutado en el Broadway (CPU principal) y el otro en el Starlet (CPU de I/O). Ambos residen dentro de los 512 MB de memoria de la NAND y pueden ser actualizados.

### SO del Starlet

El Starlet ya es una pieza de hardware interesante, pero su software es aún más intrigante. Verás, este sistema operativo no solo tiene acceso completo a cada rincón de la consola, sino que también es lo primero que se ejecuta cuando se presiona el botón de encendido.

El Starlet ejecuta un sistema *extraoficialmente* denominado **Sistema Operativo de Entradas/Salidas** o "IOS" (por favor, no confundir esto con el iOS de Apple) [@operating_system-ios]. IOS es un sistema operativo con todas las funciones compuesto por:

- Un **Microkernel**: controla la CPU ARM9, ejecuta **procesos** y se comunica con otro hardware mediante los **controladores**.
- **Controladores**: Habilitan la comunicación con hardware externo a la CPU (I/O).
- **Procesos**: realizan una tarea, como la gestión de la red o la implementación de un sistema de archivos.
- **Núcleo criptográfico**: acelera las operaciones relacionadas con el cifrado (**solo AES y SHA-1**).

Con esto en mente, la **tarea principal de IOS es aliviar la carga de trabajo de la CPU principal** abstrayendo I/O y seguridad. Por este motivo, los programadores no tienen que preocuparse de esos asuntos. Para lograr esto, el Starlet **reserva entre 12 y 16 MB** de RAM GDDR3 para sus tareas, el resto es utilizado por el Broadway y la GPU.

El Broadway y el Starlet se comunican entre sí mediante un protocolo de **comunicación entre procesos** o "IPC": en pocas palabras, ambas CPU comparten dos registros cada una. Una CPU puede escribir en los registros de la otra (los datos escritos pueden representar un comando o un valor) y desde allí, la CPU receptora puede realizar una función en respuesta.

El sistema de actualización de IOS **es un poco complicado**: las versiones actualizadas de IOS no se instalan sobre las antiguas, sino en otra ranura (el área reservada en NAND para IOS está dividido en "ranuras"). Esto es puramente **por razones de compatibilidad**, ya que permite que el software antiguo de la Wii siga usando la misma versión de IOS para la que fue desarrollado.

Nintendo lanzaba a menudo actualizaciones de IOS para mejorar la compatibilidad con el hardware (lo cual era necesario cuando se enviaba un nuevo accesorio). Únicamente hay **una excepción** cuando las actualizaciones de IOS realmente reemplazan a las anteriores: cuando se descubre que una versión específica tiene una vulnerabilidad explotable. Esto era sólo por razones de seguridad.

Cuando se inserta un juego de la GameCube, sucede algo diferente: el Starlet inicia un **MIOS** en su lugar. Esta variante de IOS solo ordena al Starlet emular la [IPL](gamecube#operating-system) original.

### SO del Broadway

Este se conoce comúnmente como el **menú del sistema** y se ejecuta efectivamente en la CPU PowerPC principal (**Broadway**).

![Menú del sistema con _muchos_ canales instalados.](system/home.png){.tabs-nested .active title="Menú Inicio"}

![Menú de ajustes utilizado para cambiar la configuración.](system/settings.png){.tab-nested title="Configuración"}

![El tablón de mensajes almacena cartas agrupadas por fecha.](system/mail.png){.tabs-nested-last title="Tablón de Mensajes"}

En comparación con IOS, no lo consideraría un sistema operativo "completo", sino más bien un "programa" que permite al usuario realizar las siguientes operaciones:

- **Iniciar el juego de Wii/GameCube**: solo si hay uno válido insertado.
- **Cambiar la configuración de la consola**: Incluyendo hora, fecha, modo de vídeo o ubicación de la barra de sensores, entre otros.
- **Ejecutar aplicaciones**: Una de las novedades de esta consola es la posibilidad de instalar pequeños juegos de Wii (llamados "WiiWare"), juegos retro (juegos de la "Consola Virtual") o simplemente aplicaciones convenientes (como un navegador de Internet). Nintendo llamó a esto **canales**, pero el sistema operativo también los denomina **títulos**.
  - Los usuarios pueden descargar/comprar canales a través de un canal preinstalado llamado **Canal Tienda Wiil**.
  - Los títulos de la consola virtual incorporan un emulador para ejecutar el juego. Curiosamente, el emulador no se comparte en todo el sistema ni siquiera entre juegos de la misma plataforma. Esto permite optimizar el emulador para juegos específicos.
- **Enviar/Recibir mensajes**: las Wii tienen una identificación única (grabada en su chip SEEPROM) que se puede compartir para intercambiar mensajes con otras Wii. Los mensajes se pueden ver en el **Tablón de mensajes**.
  - Nintendo y los juegos de Wii también utilizaron este medio para proporcionar un diario.

Al igual que IOS, Nintendo también lanzó múltiples actualizaciones para este sistema. Algunos arreglaron agujeros de seguridad, otros agregaron más funciones. Una nueva característica notable fue la capacidad de almacenar canales en la tarjeta SD.

Cualquier programa que se ejecute en el Broadway (incluido el menú del sistema) depende de una versión específica de IOS para funcionar. Cuando se inicia un juego o un canal, el Starlet se reinicia usando la versión declarada de IOS necesaria.

### Medio de actualización

Nintendo se refiere a ellas como ** Actualizaciones del sistema **. Contienen los dos sistemas operativos en el mismo paquete y utilizan números ordinales para el control de versiones. La última versión conocida es `4.3E`, lanzada en junio de 2010.

Los paquetes de actualización del sistema se pueden obtener de los servidores de Nintendo o de los discos de juegos. Los usuarios pueden buscar actualizaciones manualmente usando el menú del sistema. Las actualizaciones son obligatorias si un juego requiere una versión específica de IOS que no está instalada (y el disco contiene los paquetes necesarios).

### Secuencia de arranque

Hasta ahora hemos analizado dos sistemas operativos muy diferentes que residen en esta consola y se ejecutan simultáneamente. Esto parece bastante sencillo, aunque ambos deben coordinarse cuidadosamente durante el inicio de la consola para que funcione correctamente después.

Dicho esto, el proceso de arranque de esta consola es el siguiente [@operating_system-boot]:

1. El usuario toca el botón ON en la consola.
2. Etapa **Boot0**: El Starlet ejecuta un programa cableado que se encuentra en su Mask ROM integrada (1,5 KB).
    - En pocas palabras, el Starlet descifra y verifica la integridad de los primeros 96 KB de la NAND, luego el Starlet calcula su hash y lo compara con un hash guardado que se encuentra en la memoria OTP integrada del Starlet. Si ambos hashes no coinciden, entonces la consola se induce en un bucle infinito.
3. Etapa **Boot1**: El Starlet ejecuta un pequeño programa que se encuentra en los 96 KB de la NAND antes mencionados.
    - Este programa simplemente le indicará al Starlet que inicialice (borre) los 64 MB de RAM GDDR3, luego descifre y verifique el resto de la NAND.
4. Etapa **Boot2**: El Starlet carga el IOS inicial (necesario para el menú del sistema) y luego inicia el Broadway.
5. El Broadway arranca el menú del sistema. El usuario toma ahora el control.

## Juegos

Si bien los juegos nuevos no siempre presentaban avances gráficos considerables, sí sorprendían a los usuarios con la cantidad de funciones que ahora podían ofrecer. Esto fue gracias a los nuevos servicios que Nintendo ofreció con el lanzamiento de la consola, que van desde el nuevo conjunto de mandos hasta una infraestructura en línea estandarizada (WiiConnect24) que permitió jugar en línea de manera *gratuita*.

### Medios

Los juegos de Wii se distribuyen utilizando un formato de disco propietario llamado **Disco Óptico de Wii** (lo sé, el nombre no puede ser más obvio). De todos modos, Matsushita/Panasonic diseñaron este formato basándose en el disco DVD tradicional y añadiendo características no estándar, como un **burst cutting area** en la sección interna del disco para evitar reproducciones no autorizadas.

![Los juegos estándar se presentan en una caja y disco físicos.](box_case.jpeg){.tabs-nested .open-float .tab-float .active title="Físico"}

![Los juegos pequeños (WiiWare) y los juegos emulados (consola virtual) se pueden comprar y descargar a través del Canal Tienda.](system/store.png){.tabs-nested-last title="Digital"}

El disco de Wii proporciona **4,7 GB** (si es de una sola capa) o **8,54 GB** (si es de doble capa) de espacio disponible. A menudo contienen **dos particiones**: la primera para las actualizaciones del sistema y la otra para el propio juego.

Algunos juegos como *Super Smash Bros. Brawl* incluían más particiones para almacenar múltiples juegos de la Consola Virtual, que se ejecutaban dentro del juego principal.

{.close-float}

### Desarrollo

Como parte de la tradición, Nintendo proporcionó un kit de desarrollo. Este se llamaba **NDEV** [@games-ndev] y tenía la forma de un _ladrillo_ negro alargado. NDEV se lanzó con I/O mejorados y dos veces la cantidad de MEM2 (128 MB en total) para fines de depuración.

El paquete de software oficial se llamó **Revolution SDK** [@games-rsdk] e incluía varias herramientas, compiladores, depuradores y frameworks para llevar a cabo el desarrollo (principalmente en **C/C++**). Nintendo distribuyó actualizaciones posteriores a través de un portal web llamado **Warioworld.com** (ahora fuera de línea/redireccionado) al que solo podían acceder los desarrolladores aprobados.

El SDK oficial se basa en llamadas de IOS para interactuar con el hardware de la Wii, es por eso que las actualizaciones de IOS a menudo se correlacionan con las actualizaciones del SDK.

### Volver al Menú HOME

Teniendo en cuenta todos los avances de software de esta consola, puede que te sorprenda que los juegos **aún se ejecuten en bare metal**, lo que significa que tienen control total del Broadway, pero no del Starlet (por lo tanto, aquí se implementan mecanismos de seguridad). No hace falta decir que el comportamiento del juego aún está sujeto a la aprobación de Nintendo antes de ser distribuido.

![Menú de inicio mostrado dentro de un juego.](ingame/home.png){.tabs-nested .open-float .tab-float .active title="Menú"}

![Esta pantalla también debe incluirse.](ingame/strap_warning.png){.tabs-nested-last title="Recordatorio"}

Dicho esto, hay ciertas características en diferentes juegos que de alguna manera parecen terriblemente idénticas. Por ejemplo, ¿recuerdas el famoso **Menú HOME**? Al presionar el botón "HOME" en el Wiimote se activará una pantalla emergente en el juego, lo que permitirá al usuario regresar al menú del sistema sin la necesidad de reiniciar la consola. Teniendo en cuenta que el sistema operativo no ofrece esta característica, ¿cómo lograron todos los juegos crear la misma interfaz gráfica?

La respuesta es simple: Nintendo incluyó en su SDK algunas **bibliotecas obligatorias** que los juegos deben integrar. *He aquí*, uno de ellos dibuja esa pantalla. Además, esta es la razón por la que encontrarás que sólo las aplicaciones caseras presentan diseños "originales" para el menú HOME.

{.close-float}

El menú HOME oficial es uno de los aproximadamente 200 requisitos que los juegos debían incluir, según lo establecido en el documento **Pautas de programación de Wii** (que se encuentra en el SDK oficial). Otros requisitos consistían en mostrar la pantalla "Recordatorio de la correa de Wii" (que es solo una imagen BMP) al inicio del juego, seguida de otra regla que dictaba cómo interactuar con ella.

### Títulos personalizados

![El canal Mii te permite jugar con tu propio "Mii"...](system/mii-editor.png){.tabs-nested .open-float .tab-float .active title="Editor"}

![...que luego aparece en tus juegos.<br>Wii Music (2008).](ingame/music.png){.tabs-nested-last title="Juego"}

Otra característica nueva que me gustaría destacar es la introducción de los **Mii**, una especie de avatares que los usuarios pueden crear utilizando un canal dedicado llamado *Canal Mii*.

Pero la diversión no termina ahí, ya que los juegos también podían usar estos Mii recién creados para personalizar el juego.

{.close-float}

## Antipiratería y Homebrew

Creo que la cantidad de características que ofrecía esta consola la hacía muy atractiva para la piratería, ya que descifrar el sistema de seguridad permitiría a los desarrolladores caseros tener en sus manos las capacidades de la consola sin tener que pasar por las verificaciones de Nintendo. Sea como fuere, la Wii acabó teniendo una fantástica biblioteca Homebrew.

### Protección de copia

Empecemos con la víctima común: **la unidad de disco**.

Los discos de Wii incluyen la mencionada zona de "burst cutting", inaccesible para los lectores convencionales. Por lo tanto, en ausencia de esto, el lector siempre se negará a leer el contenido.

![La unidad de disco no permitirá que nadie pase esta pantalla hasta que se inserte un disco válido.](system/disc.png) {.open-float}

Los desarrolladores de modchip descubrieron que la unidad contenía una interfaz de depuración llamada "Serial Writer" [@anti_piracy-disc], aunque este puerto está bloqueado hasta que se introduce una *clave secreta*. Aun así, era cuestión de tiempo que se descubriera la clave. Una vez ocurrido esto, los modders pudieron desactivar la protección anti-copia y posteriormente desarrollaron un modchip que automatizaba este proceso.

Matsushita lanzó nuevas revisiones de esta unidad ofuscando la interfaz de depuración, sin embargo, se descubrieron otros fallos en el lector que volvieron a habilitarlo.

{.close-float}

Cabe mencionar que el principal beneficio de los modchips era la piratería pura y dura, ya que el contenido del disco sigue estando cifrado, por lo que se necesitaban más investigaciones y herramientas para ejecutar código personalizado.

El homebrew de la GameCube, por su parte, ya era posible de ejecutar siguiendo [exploits previos](gamecube#tab-5-3-honourable-mention) descubiertos en la predecesora.

### Cifrado del sistema

Esta es probablemente la sección más compleja de esta consola, pero su incesante investigación abrió la puerta a montones de desarrolladores con talento y programas asombrosos.

La Wii diseñó su seguridad interna en torno a un par de cifrados criptográficos (AES, RSA, ECC, SHA-1 y HMAC). Para facilitar las explicaciones, veamos cada grupo por separado:

#### Cifrado compartido {.tabs.active}

La comunicación entre muchos componentes (NAND, disco de juego y tarjeta SD) está encriptada para evitar su manipulación. Nintendo eligió un sistema de **clave simétrica** para protegerla [@anti_piracy-security], lo que significa que la Wii utiliza la misma clave para cifrar y descifrar sus datos.

El Starlet tiene tres claves AES de 128 bits almacenadas en su memoria OTP [@anti_piracy-keys], estas se escriben una vez durante la fabricación:

- **Clave común**: clave global generada por Nintendo que se encuentra en todas las Wii, se utiliza para descifrar la primera capa de cifrado utilizada en los canales y juegos basados en disco (a partir de ahora nos referiremos a ellos como **títulos**).
- **Clave SD**: Se utiliza para cifrar/descifrar los datos transferidos a la tarjeta SD, y sólo el menú del sistema puede realizar estas transferencias.
  - Nintendo almacenó una copia de esta clave dentro del IOS sin ninguna razón clara.
- **Clave NAND**: Esta clave se genera aleatoriamente durante el proceso de fabricación (lo que significa que es única para cada Wii) y se utiliza para proteger el chip NAND.

Con esto, podemos ver que el Starlet se encarga del cifrado/descifrado de contenido sensible, es por esto que esta CPU es la única que tiene acceso a datos confidenciales.

#### Cadena de confianza {.tab}

Los títulos contienen otra capa de seguridad, RSA-2048. Se trata de un **cifrado asimétrico**, lo que significa que necesitamos una clave para cifrar el contenido y otra para descifrarlo. En pocas palabras, esto permite a Nintendo cifrar los títulos utilizando una clave no revelada (llamada "clave privada"), mientras que la Wii los descifra utilizando una "clave pública", que se almacena en la consola. Si los piratas informáticos obtuvieran la clave pública, no sería suficiente para descifrar el sistema de seguridad, ya que se espera que los datos sigan cifrándose con la clave privada, que sólo Nintendo conoce.

Además, RSA no sólo se utiliza para cifrar contenidos, sino también para comprobar la integridad de dicho cifrado. Verás, Nintendo utiliza múltiples claves que se usan para firmar (cifrar) datos ya cifrados, formando una cadena de cifrado con el único propósito de asegurarse de lo siguiente:

- Todas y cada una de las claves utilizadas han sido autorizadas por Nintendo.
- Los datos no se han alterado y se han vuelto a cifrar sin autorización.

Permítanme darles un ejemplo de cómo funciona esto:

1. Nintendo crea una clave llamada `x`.
2. Nintendo programa el Starlet para que sólo confíe en los contenidos firmados con la clave `x`.
3. Si el Starlet tiene que descifrar un título con la clave `y`, solo procederá si `y` ha sido firmado con la clave `x`.

Esto se denomina **cadena de confianza**. Fuera de la Wii, esta técnica se utiliza habitualmente para proteger la mayoría de nuestras comunicaciones en todo el mundo (por ejemplo, los navegadores web que utilizan HTTPS se basan en "certificados raíz" para validar la autenticidad de certificados desconocidos).

#### Cadena del Starlet {.tab}

La OTP del Starlet almacena claves públicas (lo que significa que, para nuestros propósitos, sólo puede descifrar y verificar la firma del contenido). Su cadena de confianza está formada por las siguientes claves [@anti_piracy-certificate]:

- **Clave Raíz**: firma la clave CA.
  - El Starlet sólo necesita almacenar esta clave (pública), el resto puede ser descifrado (y posteriormente confiado) si ha sido firmado por esta clave.
- La **Clave de Autoridad de Certificación** (CA): firma las claves XS y CP.
- La **Clave XS**: esta clave firma "Tickets", un tipo de datos que contiene una lista de claves AES necesarias para descifrar títulos (llamadas claves de título).
- La **Clave CP**: una vez descifrado el título mediante la respectiva clave de título, la clave CP se utiliza para firmar los metadatos de un título (denominados TMD).
  - Aunque no firma el contenido en sí, los metadatos incluyen un hash SHA-1 que el Starlet utiliza para verificar la integridad de esos datos.

Como ves, todo esto **permite a Nintendo ser el único distribuidor de contenido**, lo que puede ser positivo para los estudios de juegos preocupados por la piratería.

#### Más claves {.tab}

Este sistema también contiene un par de **claves privadas y públicas de la ECC**. La criptografía de curva elíptica (ECC) es otro algoritmo similar al RSA. En este caso, sólo se utiliza para firmar el contenido transferido a través de la tarjeta SD. Esto es lo que impide que los contenidos copiados de una Wii se utilicen en otra.

La clave ECC está firmada por *otra* clave pública RSA llamada **MS**, que permitirá al Starlet confiar en la clave ECC.

La última clave utilizada por esta consola es la **clave HMAC**, que utiliza otro algoritmo que combina hashes SHA-1 y HMAC. Durante el proceso de arranque, el Starlet comprueba que la NAND no haya sido alterada por hardware de terceros. Para ello, calcula el hash SHA-1 de NAND y lo compara con un hash fijo para comprobar si coinciden. Además, el hash guardado se firma con la clave HMAC para garantizar su autenticidad.

Como nota final, la clave HMAC se almacena en SEEPROM (fuera del Starlet), no en OTP.

#### Observaciones {.tabs-close}

Después de todo esto, cabe mencionar que cuando el sistema ejecuta juegos de GameCube, **no se utiliza ninguno de los métodos de cifrado mencionados**. En su lugar, el Starlet sólo comprobará que el juego sólo accede a sus posiciones de memoria designadas. Esto se debe a que 1/4 de la RAM GDDR3 está asignada para simular la antigua [ARAM](gamecube#audio).

### La caída del cifrado

Empecemos con las claves AES, el algoritmo puede ser difícil de descifrar, pero si las claves se extraen de alguna manera (especialmente la clave común), esa capa de seguridad quedaría anulada al instante. Por lo tanto, el principal reto es **cómo extraerlas**.

![Diagrama de seguridad del Starlet.](encryption.png) {.open-float}

Pues bien, un grupo de hackers llamado **Team Twiizers** descubrió que la falta de firmas en el modo GameCube puede ser una prometedora zona de ataque [@cpu-ccc]. No solo descubrieron que **3/4 de esa RAM GDDR3 no se inicializaban** tras ejecutar un programa GC, sino también que puenteando algunos puntos de dirección de la placa base (usando, eso sí, unas pinzas) podían intercambiar los bancos seleccionados de RAM GDDR3, permitiendo el acceso a zonas restringidas. Y he aquí, se descubrieron las claves AES residían allí.

{.close-float}

No olvidemos que esto sólo permite descifrar la "primera capa" de seguridad, pero para poder ejecutar programas sin firmar (Homebrew), también hay que crackear RSA. Por desgracia, esto puede ser computacionalmente imposible... A menos que haya fallos en su aplicación. Pues bien, Team Twizzers no se detuvo ahí, así que empezó a invertir la forma en que se codificaba IOS, centrándose en sus funciones de verificación de firmas.

La verificación de firmas RSA, sin entrar en demasiados detalles, funciona comparando el hash de la operación RSA calculada con la firma descifrada. Después de algunas manipulaciones, el grupo descubrió algo gracioso: **Nintendo implementó esta función usando `strncmp`** (la comparación de "cadenas" de C).

Para la gente no familiarizada con C, `strncmp` es una rutina utilizada para comprobar si dos cadenas son iguales. Este método recibe tres parámetros: dos cadenas y un número entero, este último indica el número de caracteres que hay que comparar. Después, `strncmp` comienza a comparar cada carácter hasta que se alcanza el final de cualquier cadena (o el contador de caracteres). Las cadenas en C son solo una cadena de caracteres terminada en un carácter `\0`, esto significa que `strncmp` deja de compararse una vez que cualquier cadena llega a `\0`. Por lo tanto, al componer un título de Wii de forma que su hash contenga `\0` al principio, los cálculos RSA del Starlet se encontrarán comparando hashes muy cortos (o incluso vacíos) con sustanciales posibilidades de colisión (datos diferentes que producen el mismo valor hash). En última instancia, utilizando una cantidad factible de fuerza bruta, esto permitió que la comparación arrojara `igual`... **¡El título está firmado!**

Por si fuera poco, este fallo se descubrió en múltiples versiones de IOS, ¡e incluso en rutinas encontradas en el boot1 y el boot2!

### Los albores del Homebrew

Después de esto, sólo quedaba una cosa: hacer que el exploit fuera permanente e implementar una herramienta "fácil de usar", para que pudiera ejecutar programas personalizados sin problemas.

![La ejecución de aplicaciones de terceros se realizaba inicialmente mediante una partida guardada falsificada.](system/twilight_hack.png) {.open-float}

Hasta ahora, estos exploits requerían el uso de hardware adicional, por lo que no todos los usuarios podían aprovecharlos... Hasta que Team Twizzers descubrió *otro exploit*: un desbordamiento del búfer del juego.

Me refiero al famoso **The Legend of Zelda: Twilight Princess** (un juego de Nintendo, por cierto). TT descubrió que el archivo de guardado del juego podía modificarse para desbordar el número de caracteres utilizados para dar nombre al caballo del jugador. Así, cuando el jugador intenta leer el nombre desbordado, se desencadenaría una reacción en cadena que terminaría en la ejecución de código arbitrario. Esto podría usarse para ejecutar, digamos, un cargador de programas.

{.close-float}

Como ahora se podían falsificar las firmas, este archivo guardado manipulado se distribuía fácilmente por la red para que otras personas lo utilizaran. Como resultado, la comunidad homebrew ahora podía ejecutar su software personalizado.

### Un estado permanente

Al seguir revirtiendo IOS, se descubrió que las firmas sólo se comprueban durante la instalación de los títulos, no durante su ejecución.

::: {.subfigures .open-float .tabs-nested .tab-float}

![](system/homebrew_channel.png){.tab-nested .active title="Banner"}

![](system/homebrew/menu.png){.tab-nested title="Interior"}

El _Canal Homebrew no oficial_ (2008).<br>Probablemente el hack más fácil de usar de todos los tiempos.

:::

Por lo tanto, TT lo hizo de nuevo. Forjaron cuidadosamente un canal instalable que podía cargar programas arbitrarios desde la tarjeta SD. Si este canal se instalara antes de que Nintendo hubiera tomado medidas para mitigar los problemas de seguridad, la Wii disfrutaría de homebrew de forma permanente (independientemente de que Nintendo parcheara los fallos de su firma en el futuro, como así hizo).

El **Canal Homebrew** fue el resultado de esto, este título permitía a cualquier usuario poner en marcha programas homebrew que se beneficiaban del control total de este sistema (con todas las implicaciones que esto significa).

{.close-float}

### La respuesta de Nintendo

Por razones obvias, Nintendo emitió varias actualizaciones de sistema que corregían los exploits de firma en múltiples versiones de IOS, también se ocuparon de sus etapas de arranque defectuosas enviando nuevas revisiones de hardware.

![Muchas de estas están llegando.](system/updates.png) {.open-float}

Sin embargo, todavía se descubrieron fallos fundamentales en este sistema:

- El Broadway puede reiniciar al Starlet a cualquier versión de IOS sin permisos adicionales, permitiendo usar exploits de versiones no parcheadas.
- Las API ocultas de IOS pueden seguir utilizándose sin privilegios especiales, lo que permite un control aún más desautorizado del hardware.
- La unidad de disco puede recibir comandos para leer DVD convencionales y algunos IOS contenían llamadas ocultas para enviar esos comandos. Esto era especialmente preocupante por motivos de piratería.

{.close-float}

Por lo tanto, para resolver esto, lo único que quedaba era sólo el juego del gato y el ratón. Durante los meses siguientes, se descubrieron diferentes exploits y Nintendo intentó parchearlos uno tras otro. Este "juego" continuó hasta que la consola llegó al final de su vida útil y no se publicaron más actualizaciones. Podemos asumir que el ratón ganó esta vez.

En el momento de escribir estas líneas, los exploits mencionados en este artículo ya han sido parcheados, pero también sustituidos por otros que funcionan actualmente.

Supongo que no se puede discutir acerca del impacto que tuvo la hacking scene en este sistema, y quién puede olvidar la enorme cantidad de homebrew que se puso a disposición (había incluso una "tienda" de homebrew, que era más rápida y libre que el "Canal Tienda Wii" oficial).

## Eso es todo amigos

¡Feliz año nuevo 2020!

Este me llevó bastante tiempo, ingenuamente predije que como la mayor parte del contenido ya estaba hecho para la GameCube, sólo tendría que escribir párrafos cortos y añadir enlaces aquí y allá...

Creo que resultó más informativo de lo que esperaba, así que espero que lo haya encontrado una buena lectura.

¡Hasta la próxima!  
Rodrigo
