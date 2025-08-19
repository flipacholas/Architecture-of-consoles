---
short_title: Sega Saturn Architecture
long_title: Architecture of the Sega Saturn
name: Sega Saturn
subtitle: What can you do with 8 processors?
date: 2019-08-03
release_date: 1994-11-22
generation: 5
cover: saturn
javascript: ['threejs']
published: true
seo_image_pos: "Top"
top_tabs:
  Models:
    - title: "Japanese"
      caption: "The Sega Saturn, released on 22/11/1994 in Japan"
      file: japanese
      latex_height: 90
    - title: "International"
      caption: "The Sega Saturn, released on 11/05/1995 in America and 08/07/1995 in Europe"
      active: true
      file: international
      latex_height: 93
  Motherboard:
    caption: "Showing 'VA8' revision which includes all components in a single board. <br>Remaining RAM chips are fitted on the back"
  Diagram:
    latex_height: 95

# Historical
aliases: [/projects/consoles/sega-saturn/]
---

## A quick introduction

Welcome to the 3D era! Well... *sorta*. Sega enjoyed quite a success with the Mega Drive so there was no reason to force developers to write 3D games *right now*.

Just in case developers wanted the extra dimension, Sega prepared some bits of the hardware to enable polygon drawing as well. Hopefully, the result didn't get out of hand!

## {.supporting-imagery}

## CPU

Just like its close competitors [drowned in options](playstation#tab-1-1-a-bit-of-history) during the RISC fever, Sega had to go through all the conundrums of choosing a new vendor that could deliver the next generation of games (including those with '3D' capabilities). In the end, the company chose a fresh CPU whose creator was desperately seeking an adopter: the **Hitachi SuperH**, or 'SH'.

While initially focused on embedded applications, Hitachi's new creation debuted modern arts such as [@cpu-prog_manual]:

- A [load-store architecture](xbox#tab-1-4-cisc-or-risc), meaning instructions don't mix memory and register operations, resulting in a cleaner and scalable CPU design. This is one of the pillars of RISC CPUs.
- A **32-bit data bus and Arithmetic Logic Unit (ALU)**, enabling the flow and operation of larger amounts of data (32-bit values) without consuming extra cycles.
- **16 general-purpose 32-bit registers**, this time more flexible than those found in previous CPUs, such as the [Motorola 68000](mega-drive-genesis#cpu). This is another design decision derived from the RISC guidelines [@cpu-patterson].
- **32-bit address bus**, allowing up to 4 GB of memory to be addressed (*farewell [mappers](nes#going-beyond-existing-capabilities)*).
- A **pipelined data path** with **five stages**: The execution of instructions is now divided into five steps, or *stages*. The CPU can queue up to five instructions simultaneously, with each one assigned to a different stage. This allows taking advantage of all the CPU's resources without idling, while also increasing the number of instructions processed per unit of time.
- A **16-bit multiplication unit**: Performs multiplications with 16-bit integers.

Furthermore, the SuperH features a new instruction set called **SuperH ISA** which, apart from adopting a RISC design, **all of its instructions are 16 bits wide**. This comes as a surprise since this CPU operates 32-bit words, so you would expect instructions to have the same length. Yet, Hitachi managed to fit its ISA using half the size. Not only does this format reduce the size of programs, but since the CPU fetches instructions in 32-bit batches, **two instructions can be retrieved in one cycle**. Overall, this technique of compressing the instruction set helped tackle a common concern of RISC-based architectures called 'code density', where the latter required more instructions (therefore, more memory) to perform the same tasks as non-RISC systems.

### The ramifications

Conversely, other drawbacks of RISC designs are still present in the SuperH, such as [control hazards](playstation#delay-galore). Consequently, programs are required to include **branch delay slots** to avoid calculation errors. To remediate things, the SuperH features **delayed branch instructions** which are branch instructions pre-fitted with a delay slot [@cpu-prog_manual].

**Data hazards** are also present but not tackled by the programmer or compiler, the CPU will automatically stall the pipeline whenever necessary.

### Sega is not satisfied

Nevertheless, all of that didn't stop Sega from expressing dissatisfaction with the end product. This was mainly due to the small 16-bit multiplier, which was seen as a bottleneck when processing larger amounts of data (a new need for 3D games). Thus, Hitachi synthesised a second revision with an extended multiplier unit and other features from Sega's checklist [@cpu-history], leading to a new CPU called **SH-2**.

![The two SH-2 chips found in the Sega Saturn](sh2s.jpg)

Even so, Sega couldn't stand still after hearing what choice of CPU [its](playstation#cpu) [competitors](nintendo-64#cpu) went for. So, it asked Hitachi to step up the clock frequency of the SH-2 - an impossible task once the chip is already out for manufacturing. Luckily, Hitachi had another trick up their sleeve: **multiprocessing**. During the research phase of the SH, the team added minimal circuitry to allow the SH to work alongside other SHs within the same system simultaneously. Upon hearing that, Sega decided on a two-chip configuration for the Sega Saturn. And the rest is history.

### The final product

Having explained the origins, let's take a look at the shipped product.

This console has not one but **two Hitachi SH-2** CPUs running at **~28.63 MHz each** [@cpu-overview]. While both physically identical, they are placed in a **master-slave state**, where the first one may send commands to the second one. This can achieve some degree of parallelism, albeit both sharing the same external bus [@cpu-dualcpu] (which is prone to congestion).

Hitachi packaged different variants of the SH-2 and sold them as part of a series called 'SH7600'. All of them feature [@cpu-brief]:

- The aforementioned **five-stage pipeline** and **SuperH ISA**. The latter has been extended with six additional instructions for specialised branching and arithmetic.
- An upgraded **32-bit multiplication unit**, which now performs multiplication with 32-bit integers.
- A **32-bit external data bus** that is shared across the two CPUs.

The specific chip selected for this console, the 'SH7604', contains the following additions [@cpu-prog_manual]:

- **4 KB of cache**: Stores a small number of instructions and data previously fetched from memory to speed up future reads.
- A **32-bit division unit**: Performs division with 32-bit integers.
- An **internal Direct Memory Access (DMA) controller**: Transfers data from memory without the intervention of the CPU.
- Support for **little endian**, enabling the CPU to understand values encoded in the opposite order. This is useful when external memory is shared with other processors.

It's worth pointing out that **having two CPUs doesn't mean that games will work twice as fast!** In practice, however, this requires very complex programming to efficiently manage CPUs that share the same bus. For instance, memory access should leverage the cache as much as possible.

### The octopus architecture

![Overview of the multi-subsystem model.](_diagrams/subsystems.png)

The Sega Saturn houses a significant number of processors (eight!). So, to avoid making them fight for resources, engineers grouped the circuitry into four separate subsystems:

- The **CPU** subsystem, where the main CPUs and memory reside. Truth to be told, there's also an unusual *third* component. This is explained further down.
- The **Video** subsystem, comprising the graphics accelerators.
- The **Audio** subsystem, which can be considered a separate computer in its own right (you'll see more in the 'Audio' section of this article).
- The **CD-ROM** subsystem, a walled fortress owing to the copy-protection mechanisms implemented. These are explained further in the 'Anti-piracy' section.

It's worth noting that each subsystem is wired to a dedicated bus - except the Video and Audio ones, which share one.

### A divided choice of memory

![Overview of the main memory architecture.](_diagrams/memory.png)

The CPU subsystem contains a total of **2 MB of RAM** for general-purpose use, called **Work RAM** (WRAM). But it's not that simple: this memory is split into two distinct blocks and, once again, each is accessed via different buses:

- The first block provides **1 MB of SDRAM**. Due to its higher access speed, this block is also called **WRAM-H**. Its bus is shared with other components.
- The other megabyte is named **WRAM-L**, as it uses **DRAM** instead, resulting in lower rates. Its bus, however, is reserved for the main CPUs.

### The third processor (and counting)

Surprisingly, it seems that the two SH-2 CPUs still weren't enough for Sega. So, to accelerate vector processing (at the cost of more complexity), the CPU group also houses an additional coprocessor, the **Saturn Control Unit** or 'SCU'.

![The Saturn Control Unit in my motherboard revision.](chips/scu.webp){latex_width="80%"}

This is a separate chip comprised of two modules [@cpu-scu]:

- A **Direct Memory Access (DMA) controller**: Arbitrates access to WRAM-L across the three subsystems without the intervention of the CPUs.
- A **Digital Signal Processor (DSP)**: Used as a fixed-point 'geometry unit'. Compared to the SH-2, it performs matrix/vectors calculations such as 3D transformations and lighting faster. However, it runs at half-speed and its instruction set is more complex. Furthermore, it depends on the SH-2's slow WRAM-L to fetch and store data (using the DMA).

To alleviate things, the SCU comes with **32 KB of SRAM** for local use.

## Graphics

Since the Saturn is the first '3D console' reviewed for [this series](consoles), let us first go over the fundamental design changes that made way to the new generation of 3D graphics.

### Revised methodologies

Firstly, the GPU now relies on a **frame buffer**: graphics are no longer required to be rendered on the fly. Instead, the GPU reserves a portion of VRAM to draw a bitmap containing all the computed geometry requested by the CPU. A video encoder then picks up that region and outputs it through the video output signal. Be as it may, the Saturn does mix the two models... I will explain more later.

Consequently, having this reserved 'working space' allows the GPU to continue manipulating the bitmap even after finishing rendering the scene, enabling the CPU to offload additional exhaustive tasks (such as lighting and anti-aliasing) to the GPU. Here is where the concept of **graphics pipeline** starts to gain significance.

Secondly, **more is VRAM required**: Using a frame buffer increases memory requirements (which is no longer a major issue). The amount of RAM required for a frame buffer is proportional to the screen dimensions and the number of colours used (a.k.a. *colour depth*). As an example, 600 KB of VRAM can contain a frame buffer of 640x480 pixels at 32k colours (16 bits per pixel).

Additionally, programmers are free to organise their VRAM usage: not every bit must be allocated for the frame buffer. So, why not also use it to cache textures, render other frame buffers concurrently, and add colour lookup tables to speed things up?

Finally, the CPU incorporates **vector operations**: a GPU with 3D capabilities would be incomplete without a CPU capable of feeding the required geometry. For this reason, next-generation CPUs feature some form of specialised instructions that accelerates vector calculations, these are known as **Single Instruction Multiple Data** (SIMD) extensions. Nonetheless, their design across the industry will be far from standardised.

In the case of the Saturn, vector operations are accelerated by the Saturn Control Unit, not the SH-2 CPUs.

### Sega's offering

This console includes **two proprietary GPUs**: the VDP1 and VDP2. Each serves a different purpose while operating concurrently. Some might argue that the new GPUs are an evolution of the [classic VDP](mega-drive-genesis#graphics), while others may claim it's a complete redesign... I think it's a bit of both.

With that in mind, let's take a look at the two chips.

#### VDP1 {.tabs .active}

![Architecture of the VDP1.](_diagrams/vdp/vdp1.png){.tab-float}

The **Video Display Processor 1** (VDP1) is a chip that draws [sprites](mega-drive-genesis#tab-1-4-sprites) with **geometric transformations** [@graphics-vdp1]. The results are written to a frame buffer, which is subsequently streamed to the VDP2 for display.

This chip is programmed by issuing **drawing commands**. So, programmers are provided with **512 KB of dedicated RAM** to store these commands, along with the necessary materials (textures/tiles, colour lookup tables, etc.) to process them.

Consequently, the VDP1 is designed to use **quadrilaterals as primitives**, meaning it can only compose models using only four-vertex polygons (sprites). The chip applies **Forward Texture Mapping** to connect texture points onto the quadrilateral, in that direction. It doesn't come with any filtering or interpolation techniques, so the rendering is subject to **aliasing**.

The VDP1 also provides this selection of effects:

- Two **shading algorithms** (Flat and Gouraud) for lighting.
- **Anti-aliasing**: In this case, it duplicates pixels to cover gaps during texture mapping.
- **Clipping** to discard polygons outside the camera's viewport.
- **Transparency** to blend two non-opaque bitmaps.

**Two 256 KB frame buffer chips** are available to concurrently draw new scenes of the game without breaking the current one being displayed. When the secondary buffer is finished being drawn, the VDP1 switches to broadcasting that one instead, and the cycle repeats. This technique is called **page flipping**.

#### VDP2 {.tab}

![Architecture of the VDP2.](_diagrams/vdp/vdp2.png){.tab-float}

The **Video Display Processor 2** (VDP2) specialises in rendering large planes (up to 4096×4096 pixels) with transformations (rotation, scaling and translation) applied [@graphics-vdp2].

More importantly, the VDP2 renders **on the fly** (without a frame buffer), much like earlier [tile-based engines](mega-drive-genesis#constructing-the-frame). It supports up to **16.7 million colours** (24-bit).

This chip is also responsible for displaying the VDP1's output buffer, which can itself be transformed and mixed with the VDP2's layers. The VDP2's 'frame' consists of up to **four 2D planes and one 3D plane, or just two 3D planes**.

This chip relies on [tile-maps](mega-drive-genesis#constructing-the-frame) to compose planes and applies **perspective correction** for 3D texture mapping. This is a more sophisticated approach that accounts for the depth value when computing rotations.

Effects available include:

- **Multi-texturing** for mapping more than one texture onto a single polygon.
- **Shadowing**: after the VDP2 receives the sprites generated by the VDP1, it can reduce their brightness and blend them with half-transparency. 
  - Nonetheless, the VDP2 only receives a sprite stream from the VDP1 (in pace with the CRT beam) so this function tends to be tricky to encode and operate.

This chip also houses **4 KB of Colour RAM (CRAM)** which is used to translate the VDP1's custom colour values (indexed colours) into 24-bit RGB colours.

Finally, even though the VDP2 is limited to two 3D planes, nothing prevents the CPU from using its VRAM as a software-based frame buffer to render additional 2D or 3D graphics.

If this section got your attention, I recommend checking out the sources at the end, the VDPs have a lot more interesting quirks that lie beyond the scope of this article.

### Defining the problem {.tabs-close}

As you can see, the architecture of the graphics subsystem is quite complex, and its interpretation often varies depending on the needs:

### As a *powerful* 2D console

The capabilities of the Saturn for drawing 2D scenes were vast compared to those of the [Mega Drive](mega-drive-genesis) or [SNES](super-nintendo), although they were not the console's main selling point.

For a quick demonstration, I will use *Mega Man X4* (1997) as example.

#### Sprites {.tabs .active}

![The VDP1's Sprite plane.](2d/sprites.png){.tab-float .latex-framed}

In this case, the VDP1 is tasked with plotting traditional sprites without any 3D distortion applied.

The CPU sets up the VDP1 by writing to its registers and populating its VRAM with commands and tiles. The process can also be accelerated using the DMA controller. 

#### Backgrounds {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=2 #fig-vdp2_bgframes}

![2D plane 1.](2d/bg1.png){.active .border hardcover_latex_width="87%" paperback_latex_width="77%" title="2D plane 1"}

![2D plane 2.](2d/bg2.png){.border hardcover_latex_width="87%" paperback_latex_width="77%" title="2D plane 2"}

![2D plane 3.](2d/bg3.png){.border paperback_latex_width="80%" title="2D plane 3"}

VDP2's Background planes.

:::

The VDP2 is then instructed to draw background planes. These, together with the sprite layer, are automatically composited to form a fully coloured scene.

The commanding part is fundamentally similar to that of the VDP1: programmers have access to registers and VRAM to configure the chip accordingly.

Certain VDP2 functions can be exploited to create more realistic scenery, such as scaling effects to simulate a heat haze, as seen in the '2D plane 2' [@fig-vdp2_bgframes].

#### Result {.tab}

![Mixed planes (_Tada!_).](2d/result.jpg){.tab-float .border}

Not much mystery here, the VDP2 handles the last step of passing the processed signal to the video encoder.

The VDP2 operates in sync with the CRT beam, meaning that its ongoing computations correspond to the pixels due to appear on the upcoming scan line.

### As a *challenging* 3D console {.tabs-close}

Here's where the Saturn both *shone and struggled* at the same time. While this console had eight processors to take advantage of, it ultimately came down to:

- Whether programmers would be able to master most of the console's features during a small time frame (remember the console's commercial lifespan would effectively end once its successor was released, or even announced).
- Whether their game could ship within a reasonable date.

For this reason, game quality varied dramatically from title to title, with each studio coming up with unique approaches.

#### 3D modelling {.tabs .active}

![3D models of characters without textures or background. Notice the primitives used to build the models.<br>Virtua Fighter Remix (1995).](3d/models.png){.tab-float}

So far, previous examples used individual regular quadrilaterals to form sprites and/or background layers. But what if we group multiple irregular primitives and arrange them to form a more complex figure? This is how 3D models come to fruition.

To put it in simple terms, classic 2D consoles like the [Super Nintendo](super-nintendo) arrange their graphics (backgrounds and sprites) in quasi-rectangular areas. In some cases, such as with [Mode 7](super-nintendo#that-feature), programmers can supply a rotation matrix to apply transformations to some of these areas. The Saturn, by contrast, allows developers to define four-point quadrilaterals with arbitrary angles between their edges - Sega calls them **distorted sprites**. Then, the VDPs' texture-mapping capabilities paint the quadrilateral's area with a texture, which is scaled to conform to the polygon's shape.

In terms of operations required in a 3D game, the CPUs and SCU are tasked with formulating a 3D world and projecting it onto a 2D space. Then, both VDPs are commanded to render it, apply effects, and finally broadcast it to the television.

#### Pixel processing {.tab}

![Rendered scene with 3D models and backgrounds.<br>Virtua Fighter Remix (1995).](3d/complete.png){.tab-float}

Either VDP can draw the newly projected 3D space and stamp textures and effects. However, which chip is 'in charge' varies from game to game.

Some developers prioritised the VDP1 for rendering nearby polygons, leaving the VDP2 to handle distant scenery. Others devised clever workarounds that allowed the VDP2 to draw closer polygons (thereby offloading the volume of geometry passed to the VDP1). The core challenge lies in designing an efficient engine capable of displaying *impressive* graphics while maintaining an acceptable frame rate.

### The new designs {.tabs-close}

These are some examples of characters that were redesigned specifically for this console. I initially built an interactive model viewer for the website edition, and later adapted the visualisations for the physical formats.

![Sonic in Sonic R (1997).<br>185 quadrilaterals.](sonic_r_saturn){.toleft model3d="true" paperback_latex_width="93%"}

![Tails in Sonic R (1997).<br>254 quadrilaterals.](tails_r_saturn){.toright model3d="true" hardcover_latex_width="91%" paperback_latex_width="82%"}

While the Saturn is only able to draw quadrangles, the Wireframe view exhibits two triangles in place of a single quadrangle. This occurs because the modern format used to encode this model (glTF, an open standard for contemporary 3D modelling), allowing modern devices to still render it, doesn't support quadrangles at the time of this writing. This behaviour is not apparent in the Surface view.

In a way, this highlights how modern graphics technology can struggle to faithfully emulate their ~30-year-old predecessors!

### An introduction to the visibility problem

When 3D polygons are projected onto a 2D space, it is crucial to determine **which polygons are visible from the camera's perspective and which are hidden behind** [@graphics-vsd]. Otherwise, models are not drawn correctly, effects like transparency appear broken, and hardware resources are wasted.

This process is widely known as **Visible Surface Determination** or 'VSD', and it's a fundamental challenge in the field of computer graphics. Numerous publications describe algorithms that tackle this issue at different stages of the graphics pipeline - some offering highly accurate results, while others sacrifice precision in favour of better performance.

Now, unlike academic or professional-grade equipment, consumer hardware is severely constrained, meaning the choice of algorithm is often restricted to only a few options... or none whatsoever.

![Project Z-Treme [@graphics-ztreme], a 2019 Homebrew engine. It ditched Z-sort in favour of a Binary Space Partitioning (BSP) approach, fixing many glitches.](projectz.jpg){.open-float}

The Sega Saturn's approach is what I consider a *semi-solved* case. The VDP1 doesn't implement any VSD functionality: you either feed the geometry in the correct order or you get a mess. However, Sega provided a graphics library known as 'SGL' that implements a solution called **Z-sort** or **Painter's algorithm** [@graphics-sgl]. This performs **polygon sorting in software**.

Essentially, SGL allocates a buffer to sort polygons by their distance from the camera (arranging them from furthest to nearest). Then, it issues the corresponding display commands to the VDP1 in that order.

{.close-float}

One of the limitations of Z-sort in 3D environments is that its distance value (Z-order) is only **approximated**, meaning graphic glitches may still appear. For this, programmers may skip SGL in favour of implementing their own algorithms.

In later articles, you will see alternative approaches to visible surface determination. Some still rely on software, while others benefit from hardware acceleration.

### The transparency issue

As part of its own set of [colour math](super-nintendo#more-colour-magic), the Sega Saturn is capable of rendering **half-transparent graphics** - that is, blending overlapping layers of colours to give the illusion we can see through them (a.k.a. translucency). However, there are significant caveats to this functionality [@graphics-vdp1]:

- Only the VDP2 can blend pixels with half-transparency, while the VDP1 only outputs a rendered buffer, with no distinction between overlapping sprites. As a result, overlapping half-transparent sprites occlude the ones beneath.
- The VDP1's forward texture-mapping process corrupts half-transparency when applied to distorted sprites (i.e. polygons).
- Half-transparent pixels require six times longer to draw.

As a workaround, 2D games can activate the 'mesh' property on a texture. With meshed textures, the VDP1 sets the odd X/Y texture coordinates as 'fully transparent' (i.e. empty), allowing layers beneath to appear through. Curiously enough, when the console is connected to the television via the composite video signal (a standard at the time, aside from RF) the mesh pattern appears blurred. This accidental but effective consequence provided a way to simulate half-transparency [@graphics-geer].

Nevertheless, the opaque portions of a mesh would still occlude other sprites. Moreover, this didn't resolve the issue for 3D games. In the end, some titles had no option but to ditch half-transparency altogether... yet some studios found ingenious fixes, take a look at these two cases [@fig-sat_transparency].

::: {.subfigures .side-by-side max_subfigures=1}

![Sega's Daytona (1993).](daytona){.toleft video="true" #fig-sat_transparency}

![Traveller's Tales' Sonic R (1997).](sonicr){.toright video="true"}

Example of games dealing with half-transparency in different ways.

:::

In both examples, the game commands the VDP1 to draw foreground objects and background scenery. In turn, the VDP2 renders the landscape imagery far away, and overlays the HUD statistics in front of the 3D models. Consequently, the VDP1's 3D models (rendered as distorted sprites) are unable to employ half-transparency, this prevents them from naturally blending with the VDP2's layers.

Nevertheless, the two games exhibit different behaviour. During gameplay, the background of *Daytona* pops out of nowhere (as half-transparency is disabled). In contrast, *Sonic R* achieves not only half-transparency but also a **fading effect**. Traveller's Tales devised a workaround by adjusting the 'mix ratio' registers of the VDP2 (used for defining the texture's alpha) and manually switching the lighting levels as the character gets closer [@graphics-burton].

## Audio

The audio capabilities of this console were part of a broader digital revolution taking place during the same period. In essence, the combination of new storage formats and affordable (yet sophisticated) sample synthesisers ultimately unlocked the composers' ability to produce their music in-house, and then incorporate it directly into games in its original form.

### The tech stack

Be as it may, beneath the surface of attractive functionality lies a complex arrangement. In the case of the Sega Saturn, its sound subsystem is composed of several parts [@audio-scsp]:

- The **Saturn Custom Sound Processor** (SCSP): Also referred to as the Yamaha YMF292, it's composed of two modules:
  - A **multi-function sound generator**: Processes up to **32 channels** with **PCM samples** (up to 16-bit at 44.1 kHz - a.k.a. 'CD quality') or [**FM channels**](mega-drive-genesis#audio). In the case of the latter, a subset of channels is reserved for operators.
  - A **Digital Signal Processor** (DSP): Applies audio effects such as echo, reverb, and chorus. The documentation also mentions 'filters', but it's unclear whether this means envelope or frequency-based filters (e.g. low-pass).
- A **Motorola 68EC000**: Controls the audio components and interfaces with the main CPUs. It executes a [sound driver](mega-drive-genesis#the-conductor) to operate the neighbouring modules.
  - If this CPU looks familiar, that's because it is: the Mega Drive carried a 68000 as its [main CPU](mega-drive-genesis#cpu), while the 68EC000 in the Saturn is a small upgrade designed for embedded applications. The latter runs at 11.3 MHz and is connected using a 16-bit bus [@cpu-overview].
- **512 KB of RAM**: Used to store the sound driver and audio data (e.g. PCM samples). It also serves as a working area for the DSP.

### The opportunity

As previously mentioned, the new audio subsystem allowed studios to finally record and produce soundtracks in-house, then bundle them in the game without the need for rearrangement - unlike earlier systems that relied on constrained [sequencers](super-nintendo#audio) or sound hardware with strict [synthesis methods](mega-drive-genesis#audio)).

This shift was made possible thanks to a combination of key factors:

- The adoption of CD-ROM as storage medium for games allowed developers to store large and high-quality soundtracks.
- The audio endpoint could receive and mix PCM data with acceptable fidelity.
- The audio subsystem offered sufficient power and bandwidth to stream compressed PCM data and decode it in real time.

## Operating System

Upon powering on the console, the first component that starts up is the **System Management and Peripheral Control** (SMPC), a 4-bit microcontroller that takes care of initialising nearby chips, such as switching on the two SH-2s and setting them in a 'master-slave' configuration [@games-smpc].

![The SMPC chip in my motherboard revision.](chips/smpc.webp){latex_width="90%"}

Afterwards, the reset vector of the master SH-2 is set to `0x00000000` [@games-sh2], which points to a 512 KB ROM chip containing the **Initial Program Loader** (IPL).

::: {.subfigures .side-by-side}

![Japanese version.](ipl/logo_jap.jpg){.toleft hardcover_latex_width="95%" paperback_latex_width="84%"}

![European and American versions.](ipl/logo_eu.jpg){.toright hardcover_latex_width="95%" paperback_latex_width="84%"}

Logo displayed after splash animation finishes.

:::

The IPL performs the following functions as part of the boot sequence [@operating_system-bootrom]:

1. Finish initialising the hardware.
2. If a cartridge is inserted and contains executable code, continue booting from there.
4. If a 'Video CD' card is inserted, boot it.
3. If a disc is inserted, verify that it's genuine.
    - While at it, the system displays the splash screen animation.
4. If the disc is genuine, boot the game.
4. If the disc is not genuine or there's no disc inserted, launch the interactive shell.

### Interactive shell

Alternatively to playing games, the Saturn included a built-in music player called 'Multiplayer', from which users could also access a save data manager.

![Interactive shell called 'Multiplayer' or 'Audio CD Control Panel'.](ipl/multiplayer.jpg){.toleft}

![Memory Manager. Moves saves between the cartridge and internal memory.](ipl/mem_manager.jpg){.toright}

If a Video CD card was inserted, Multiplayer could also reproduce MPEG video decoded from the card itself.

### No BIOS?

Unlike the [PlayStation](playstation), whose ROM chip bundles a [BIOS](playstation#operating-system) that exposes APIs for programmers to use, the Saturn's ROM is often referred to as 'IPL' instead - presumably since its main job is to initialise the system, bootstrap the game, and run the shell.

However, the IPL ROM also stores some routines (called **services**) to manipulate the hardware, such as managing save data and power control. It even implements a semaphore mechanism for synchronising operations across multiple processors simultaneously. Hence, that part of the ROM is called **System program**.

## Games

Official Sega Saturn games are loaded from the **2x CD-ROM drive**. Its medium, a tailored variant of the **Compact Disc** (CD), has a capacity of **650 MB** [@anti_piracy-rings] and adheres to the **ISO 9660** standard for storing data [@games-format]. Additionally, many games include audio tracks next to the data tracks to stream uncompressed audio during gameplay.

### The Compact Disc (CD)

The CD is an optical medium on which information is stored by engraving **pits** and **lands** into its polycarbonate surface [@cpu-optical]. An infrared beam is then directed from the drive, and the reflection produced on the CD's surface is used to retrieve the information.

The process of converting digital information (ones and zeroes) into pits and lands and vice versa is by no means simple, especially since CDs must be robust enough to sustain day-to-day damage and intensive use, and reliable enough to store any kind of information without fear of data loss. Hence, as part of its specification, data is encoded using the **Non-Return-to-Zero inverted** (NRZi) scheme. This means the bitstream consists of zeroes until a land-to-pit or pit-to-land transition occurs, at which point a `1` will be appended instead.

This design works well until the reader encounters a sequence of ones (continuous pit and land changes) or long sequences of zeroes (constant pits or lands), at which the sensor will struggle to detect or keep synchronised, respectively. To address this, an additional model called **Eight-to-Fourteen** (ETF) modulation is applied. This inserts a handful of zeroes between the encodings, helping the sensor during the reading process.

Beyond this, further mechanisms for error detection may be added, although these are outside the scope of this article. If you'd like to explore more, check out an insightful slide presentation authored by RWTH Aachen University [@cpu-optical].

### Development

At first, Sega did not provide comprehensive software libraries or development tools (in fact, the initial documentation was inaccurate), so the only way to achieve good performance was through *harsh* assembly.

Eventually, Sega released complete SDKs, hardware kits and additional libraries to ease I/O and graphics operations. Overall, games were written using a combination of **C** and various assembly languages targeting individual components.

### I/O

Peripheral management and **Real-Time Clock** (RTC) are also provided by the aforementioned **System Management and Peripheral Control** (SMPC), which is controlled via commands sent by the SH-2s.

### Expansion methods

This console bundles a considerable number of external connectors and interfaces, most of which only saw a handful of uses:

- Behind the drive, there is a **cartridge slot**, officially intended for **additional storage** (save data) or **extra RAM**. In Japan and the United States, a modem was also offered to provide [online connectivity](mega-drive-genesis#early-network-attempts).
- At the rear of the console, there is a slot for a **Video CD Card**, which performs MPEG decompression for programs or games that support it.
- Finally, there is a mysterious socket at the back of the console called **Communication Connector**. Although Sega did not publish any developer documentation, reverse-engineering efforts revealed that it connects to the SCSP's MIDI pins and the Serial Interface (SCI) of two SH-2's [@games-development]. In any case, Sega released a floppy drive that made use of this interface.

## Anti-Piracy & Homebrew

In response to the easiness of cloning a CD, Sega implemented a copy protection system - along with region locking - to control the distribution of its games.

For the copy protection itself, Sega deliberately deviated from the standard CD format, making it impossible for regular CD burners to produce a perfect copy of a Saturn game. Then, as part of its verification process, the Saturn's disc reader looks for these non-standard features.

To be more specific, Saturn discs were manufactured with an unusual data pattern pressed into the outer edge [@anti_piracy-rings], creating a visible ring engraved with trademark labels. This ring sits outside the standard data area of a CD (known as the 'Program Area') and beyond the Lead-out section, which signals the end of readable data. As a result, conventional drives couldn't access or replicate that part of the disc.

Inside the Saturn's disc drive is a dedicated **SH-1 processor** that verifies the presence of the ring independently from the main CPUs. It uses obscure communication protocols to talk with the rest of the system. This check, however, is only performed once.

### Defeat

To begin with, the classic method used for disabling the copy protection consisted in installing a **mod chip**, which could deceive the CD reader when any type of disc was inserted. Another technique, known as the 'swap trick', consisted in **hot-swapping** a genuine disc with a burned one immediately after the protection checks had passed... with the risk of damaging the drive.

![My Saturn with the Fenrir chip installed. This is an Optical Drive Emulator (ODE) that replaces the original CD drive to execute games from an SD card instead. This is one of the many modern methods used for executing Homebrew.](fenrir.webp)

After the turn of the century, more sophisticated methods for running unauthorised code were discovered, for instance:

- An **exploit in the copy protection mechanism** was identified, allowing to boot up any disc game without undergoing copy protection checks. This was subsequently implemented in the form of a cartridge called **PseudoSaturn** [@anti_piracy-pseudosaturn]. As it relied on the cartridge medium, Action Replay cartridges were often re-flashed with PseudoSaturn (though the flasher program still needed to be bootstrapped somehow, most commonly through the swap trick).
  - This method remains in use as of 2022, although a newer fork named 'Pseudo Saturn Kai' is installed instead.
- Another method emerged in 2016 (almost 20 years after the console's release) by exploiting the fact that the **Video CD add-on can inject unencrypted code** into the CD subsystem (bypassing the CD drive entirely). This finally allowed users to load Homebrew independently of the ageing drive. The Video CD exploit is commercially distributed as a product called 'Satiator' (I'm not sponsored, by the way).
- Lastly, there's a commercial alternative that replaces the CD reader with an SD or SATA adapter. The Saturn still thinks it's reading from a CD, but the 'CD' is being emulated by the adapter, which is in turn reading from a disc image [@anti_piracy-ode]. These products are known as **Optical Drive Emulators** (ODE).

## That's all folks

![A Japanese Saturn I acquired to get more material for this article. While the games look fine, it was thanks to the Saturn's enormous Homebrew library that I was able to understand the real capabilities of this console.](mine.jpg)
