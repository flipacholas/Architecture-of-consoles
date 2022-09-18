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
top_tabs:
  Models:
    - title: "Japanese"
      caption: "The Sega Saturn, released on 22/11/1994 in Japan"
      file: japanese
    - title: "International"
      caption: "The Sega Saturn, released on 11/05/1995 in America and 08/07/1995 in Europe"
      active: true
      file: international
  Motherboard:
    caption: "Showing 'VA8' revision which includes all components in a single board. <br>Remaining RAM chips are fitted on the back"

# Historical
aliases: [/projects/consoles/sega-saturn/]
---

## A quick introduction

Welcome to the 3D era! Well... *sorta*. Sega enjoyed quite a success with the Megadrive so there's no reason to force developers to write 3D games *right now*.

Just in case developers want the extra dimension, Sega adapted some bits of the hardware to enable polygon drawing as well. Hopefully, the result didn't get out of hand!

```{r results="asis"}
supporting_imagery()
```

## CPU

Just like its close competitors [drowned with options](`r ref("playstation#a-bit-of-history")`) during the RISC fever, Sega had to go through all the conundrums of choosing a vendor that could bring up the next generation of games (including those with '3D' capabilities). In the end, the company chose a fresh CPU whose creator was desperately looking for an adopter, the **Hitachi SuperH** or 'SH'.

Hitachi's new creation implemented modern arts such as a RISC instruction set and a pipelined data path, however, Sega wasn't satisfied by the final product, especially due to the small 16-bit multiplier. Thus, Hitachi synthesised a second revision with a larger multiplier unit and other requirements on Sega's checklist `r cite("cpu-history")`, leading to a new CPU called **SH-2**.

Even so, Sega couldn't stand still after hearing what choice of CPU [its](`r ref("playstation#cpu")`) [competitors](`r ref("nintendo-64#cpu")`) went for. So, they asked Hitachi to step up the clock frequency of the SH-2 - an impossible task once the chip is already out for manufacturing. Luckily, Hitachi had another trick up in their sleeve, **multiprocessing**. During the research phase of the SH, the team added minimal circuitry to allow the SH to work with other SHs within the same system at the same time. Upon hearing that, Sega decided on a two-chip configuration for the Sega Saturn. And the rest is history. 

### The final product

Having explained the origins, let's take a look at the shipped product.

This console has not one but **two Hitachi SH-2** CPUs running at **~28.63 MHz each** `r cite("cpu-overview")`. While both physically identical, they are placed in a **master-slave state**, where the first one may send commands to the second one. This can achieve some degree of parallelism, albeit both sharing the same external bus `r cite("cpu-dualcpu")` (which can lead to congestion).

These processors are part of the Hitachi SH7600 brand, a series designed for embedded systems featuring `r cite("cpu-brief")`:

- **SuperH ISA**: A special 32-bit RISC instruction set where instructions are 16-bit long. Not only does this design reduce the size of the programs, but since the CPU fetches instructions in 32-bit batches, two instructions can be retrieved in one cycle.
  - The speciality of compressing instructions to take out half the size was carried on by ARM with [Thumb](`r ref("game-boy-advance#cpu")`).
- **Five-stage pipeline**: Execution of instructions is divided into five steps or *stages*. The CPU will queue up to five instructions where each one is allocated in one stage. This allows taking advantage of all the CPU's resources without idling while also incrementing the number of instructions executed per unit of time.
- **One multiplication unit**: Speeds up multiplication operations with 64-bit/32-bit integers.
- **32-bit data bus**: The external bus is shared across the two CPUs.
- **4 KB cache**: Stores a small number of instructions and data previously fetched from memory to speed up future reads.

The specific CPU model selected for this console, the 'SH7604' or just 'SH-2', contain the following additions:

- **One division unit**: Speeds up division operations with 64-bit/32-bit integers.
- **Internal DMA controller**: Transfers data from memory independently (without the intervention of the CPU).

Please note that having two CPUs doesn't mean that games will work twice as fast! In practice, however, this requires very complex programming to efficiently manage CPUs that share the same bus. Cache also plays a critical part in this occasion.

### A divided choice of memory

The Sega Saturn contains a total of **2 MB of RAM** for general purpose usage called **Work RAM** (WRAM). Now, these two megs are split between two very different blocks:

- The first one provides **1 MB of SDRAM** and due to its fast access rates, this block is also called 'WRAM-H'.
- The other block contains the other megabyte, but it's named 'WRAM-L' since it uses **DRAM** instead, resulting in lower rates.

### Another processor

If the two SH-2 CPUs weren't enough, the console contains an additional coprocessor, the **Saturn Control Unit** or 'SCU' which is composed of two modules `r cite("cpu-scu")`:

- **A DMA controller**: Arbitrates access to WRAM across the three main buses without the intervention of the CPUs.
- **A DSP**: Used as a fixed-point 'geometry unit'. Compared to the SH-2, it does matrix/vectors calculations such as 3D transformations and lighting faster. However, it runs at half-speed and its instruction set is more complex. Moreover, it relies on the SH-2's WRAM to fetch and store data (using the DMA).

On the bright side, the SCU comes with **32 KB of SRAM** for local use. On the bad side, the SCU can't access WRAM-L.

## Graphics

Since the Saturn is the first '3D console' reviewed for [this series](`r ref("consoles")`), let us first go over the fundamental design changes that made way to the new generation of 3D graphics:

- GPU now relies on a **frame-buffer**: Graphics are no longer required to be rendered on-the-fly. Instead, the GPU reserves a portion of VRAM to draw a bitmap with all the computed geometry requested by the CPU, and then a video encoder picks up that region and outputs it through the video signal.
  - Consequently, having this reserved 'working space' allows the GPU to continue manipulating the bitmap even after finishing rendering the scene, so the CPU may now offload some exhaustive tasks such as lighting and anti-aliasing to the GPU. Here is when the term **graphics pipeline** starts to gain momentum.
- **More VRAM required**: The use of a frame buffer implies an increment of memory requirements (which is not a big issue anymore), the amount of RAM required for a frame buffer is proportional to the dimension of the screen and the number of colours used. As an example, with 600 KB of VRAM we can contain a frame buffer of 640x480 pixels wide with 32k colours per pixel (16 bpp).
  - Additionally, programmers are free to organise their VRAM usage: Not every single bit has to be allocated for the frame buffer, so why don't we also use it to cache textures, render other frame-buffers concurrently and add colour lookup tables to speed things up?
- CPU incorporates **vector operations**: A GPU with 3D capabilities would be incomplete without a proper CPU capable of feeding the required geometry. For that reason, next-gen CPUs include a form of specialised instructions that accelerates vector calculations, these are known as **Single instruction multiple data** or 'SIMD' extension.
  - In the case of the Saturn, vector operations are accelerated by the Saturn Control Unit (not by the CPUs).

### Sega's offering

This console includes **two proprietary GPUs**, each one serving different purposes while working concurrently. Some may argue that the new GPUs are an evolution of the [classic VDP](`r ref("mega-drive-genesis#graphics")`), while others may say it's a complete redesign... I think it's a bit of both.

Having said that, let's take a look at the two chips.

(ref:vdp1diagramtitle) VDP1

(ref:vdp1diagramcaption) VDP1 Architecture.

```{r fig.cap="(ref:vdp1diagramcaption)", fig.align='center', tab.title="(ref:vdp1diagramtitle)", tab.first=TRUE, tab.active=TRUE}
image('vdp/VDP1.png', "(ref:vdp1diagramcaption)", float=TRUE)
```

The **Video Display Processor 1** (VDP1) is a chip that draws sprites with geometric transformations `r cite("graphics-vdp1")`. The results are written onto a frame buffer, which is in turn streamed to the VDP2 for display.

This chip is programmed by sending 'drawing commands' to it. So, programmers are provided with **512 KB of dedicated RAM** to store these drawing commands and the required materials (textures/tiles, colour lookup tables, etc).

Consequently, the VDP1 is designed to use **quadrilaterals as primitives**, which means that it can only compose models using 4-vertex polygons (sprites). The chip applies **Forward Texture Mapping** to connect texture points onto the quadrilateral, in that direction. It doesn't come with any filtering/interpolation technique, so the calculations are subject to **aliasing**.

The VDP1 also provides this selection of effects:

- Two **shading algorithms** (Flat and Gouraud) for lighting.
- **Anti-aliasing**: In this case, it duplicates pixels to cover gaps during mapping.
- **Clipping** to discard polygons outside the camera's viewport.
- **Transparency** to blend two non-opaque bitmaps.

**Two 256 KB frame buffer chips** are available to concurrently draw new scenes of the game without breaking the current one being displayed. When the secondary buffer is finished being drawn, the VDP1 starts broadcasting the latter instead (**page-flipping**), and the cycle continues.

(ref:vdp2diagramtitle) VDP2

(ref:vdp2diagramcaption) VDP2 Architecture.

```{r fig.cap="(ref:vdp2diagramcaption)", fig.align='center', tab.title="(ref:vdp2diagramtitle)", tab.last=TRUE}
image('vdp/VDP2.png', "(ref:vdp2diagramcaption)", float=TRUE)
```

The **Video Display Processor 2** (VDP2) specialises in rendering large (4096×4096 pixels) planes with transformations (rotation, scale and translation) applied on them `r cite("graphics-vdp2")`.

More importantly, the VDP2's renders **on-the-fly** (without a frame-buffer) like previous [tile-based engines](`r ref("mega-drive-genesis#constructing-the-frame")`). It can display up to **16.7 million colours** (24-bit). This chip is also responsible for displaying the VDP1's buffer, which can also be transformed and/or mixed with the VDP2's layers. The VDP2's 'frame' is composed of up to four 2D planes and one 3D plane; or two 3D planes.

This chip relies on [tile-maps](`r ref("mega-drive-genesis#constructing-the-frame")`) to compose planes and performs **perspective correction** for 3D texture mapping, this is a more sophisticated approach which takes into account the depth value to compute rotations.

Effects available include **multi-texturing** (mapping more than one texture per polygon) and **shadowing**. With the latter, after the VDP2 receives the sprites generated by the VDP1, it can reduce their brightness and blend them with half-transparency. Nonetheless, the VDP2 only receives a sprite stream from the VDP1 (in pace with the CRT beam) so this function tends to be tricky to encode and operate.

This chip also houses **4 KB of Colour RAM (CRAM)** which is used to translate VDP1's custom colour values (index colours) into 24-bit RGB colours.

Finally, even though the VDP2 is limited to two 3D planes, nothing prevents the CPU from using its VRAM as frame-buffer area to draw additional 2D or 3D graphics in software.

`r close_tabs()`

I recommend checking out the sources (at the end of the article) if this section got your attention, since the VDPs have a lot more quirks that are beyond the scope of this article.

### Defining the problem

As you can see, the architecture of the graphics sub-system is quite complex, so it's interpreted differently depending on the needs:

### As a *powerful* 2D console

The capabilities of the Saturn for drawing 2D scenes were huge compared to the [MegaDrive](`r ref("mega-drive-genesis")`) or [SNES](`r ref("super-nintendo")`), although they weren't the main selling point of this console.

(ref:spritestitle) Sprites

(ref:spritescaption) Mega Man X4 (1997).<br>VDP1's Sprites plane.

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)", tab.first=TRUE, tab.active=TRUE}
image('2d/sprites.png', "(ref:spritescaption)", float=TRUE)
```

In this case, the VDP1 is tasked with plotting traditional sprites without any 3D distortion applied.

The CPU sets up the VDP1 by writing over its registers and filling its VRAM with commands and tiles. The process can also be accelerated thanks to the DMA controller. 

(ref:bgtabtitle) Backgrounds

(ref:bgcaptionfooter) VDP2's Background planes.

(ref:bgcaptionpre) Mega Man X4 (1997).

(ref:bgtitle1) 2D plane 1

(ref:bgcaption1) 2D plane 1.

(ref:bgtitle2) 2D plane 2

(ref:bgcaption2) 2D plane 2.

(ref:bgtitle3) 2D plane 3

(ref:bgcaption3) 2D plane 3.

```{r fig.cap=footer, fig.subcap=c( "(ref:bgcaption1)", "(ref:bgcaption2)", "(ref:bgcaption3)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:bgtabtitle)", tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
footer <- paste0("(ref:bgcaptionpre)", " ", "(ref:bgcaptionfooter)")
image('2d/bg1.png', "(ref:bgcaption1)", tab.name = "(ref:bgtitle1)", tab.active = TRUE, caption.post="(ref:bgcaptionpre)")
image('2d/bg2.png', "(ref:bgcaption2)", tab.name = "(ref:bgtitle2)", caption.post="(ref:bgcaptionpre)")
image('2d/bg3.png', "(ref:bgcaption3)", tab.name = "(ref:bgtitle3)", caption.post="(ref:bgcaptionpre)")
figcaption(footer)
```

The VDP2 is then instructed to draw background planes. These, along with the sprite layer, are automatically mixed to form a fully coloured scene.

The commanding part is fundamentally similar to the VDP1: Programmers got registers and VRAM to set up accordingly.

Some functions from the VDP2 can be exploited to create more realistic scenery, such as scaling to simulate a heatwave (see '2D plane 2').

(ref:resulttitle) Result

(ref:resultcaption) Mega Man X4 (1997). Mixed planes (_Tada!_).

```{r fig.cap="(ref:resultcaption)", fig.align='center', tab.title="(ref:resulttitle)", tab.last=TRUE}
image('2d/result.jpg', "(ref:resultcaption)", float=TRUE)
```

Not much mystery here, the VDP2 is responsible for the last step of sending the processed signal to the video encoder.

The VDP2 operates in sync with the CRT beam, meaning that its computations correspond to the pixels that will be displayed on the next scan line.

`r close_tabs()`

### As a *challenging* 3D console

Here's where the Saturn *shined and struggled* at the same time. While this console had eight processors to take advantage of, it all came down to:

- Whether programmers would be able to master most of the console's features during a small time frame (remember the console's commercial lifespan would be over once its successor is released, or even announced).
- Whether their game would be shipped at a reasonable date.

For this reason, most games ended up dramatically ranging in quality since each studio came up with a unique solution.

(ref:modelling3dtitle) 3D modelling

(ref:modelling3dcaption) Virtua Fighter Remix (1995).<br>3D models of characters without textures or background. Notice the primitives used to build the models.

```{r fig.cap="(ref:modelling3dcaption)", fig.align='center', tab.title="(ref:modelling3dtitle)", tab.first=TRUE, tab.active=TRUE}
image('3d/models.png', "(ref:modelling3dcaption)", float=TRUE)
```

So far we've been using individual regular quadrilaterals to form sprites and/or background layers. But what if we group multiple irregular primitives and arrange them to form a more complex figure? This is how 3D models come to fruition.

To put it in simple terms, classic 2D consoles like the [Super Nintendo](`r ref("super-nintendo")`) arranges their graphics (backgrounds and sprites) in quasi-rectangular areas. In some cases, such as with [Mode 7](`r ref("super-nintendo#unique-features")`), programmers can supply a rotation matrix to apply transformations over some of these areas. The Saturn, by contrast, allows defining 4-point quadrilaterals with arbitrary angles between their edges (Sega calls them 'distorted sprites'). Then, the VDPs' texture mapping capabilities paint the quadrilateral's area with a texture, the latter is scaled to conform to the polygon's shape.

In terms of operations needed with a 3D game, the CPUs and SCU are assigned to formulating a 3D world and project it in a 2D space. Then, both VDPs are commanded to render it, apply effects and finally broadcast it on TV.

(ref:pixel3dtitle) Pixel processing

(ref:pixel3dcaption) Virtua Fighter Remix (1995).<br>Rendered scene with 3D models and backgrounds.

```{r fig.cap="(ref:pixel3dcaption)", fig.align='center', tab.title="(ref:pixel3dtitle)", tab.last=TRUE}
image('3d/complete.png', "(ref:pixel3dcaption)", float=TRUE)
```

Either VDP can draw this new (projected) 3D space and stamp textures and effects. Now, which chip is 'in charge' varies between each game.

Some prioritised the VDP1 to draw the closest polygons and left the VDP2 to process distant scenery, others found interesting workarounds to task the VDP2 to draw closer polygons (thereby off-loading the amount of geometry fed into the VDP1). The challenge consists in designing an efficient engine that could display *impressive* graphics while keeping an acceptable frame rate.

`r close_tabs()`

### The new designs

These are some examples of characters that were re-designed for this console, the models are interactive so do try to fiddle with them!

(ref:sonicrmodelcaption) Sonic in Sonic R (1997).<br>185 quadrilaterals.

(ref:tailsrmodelcaption) Tails in Sonic R (1997).<br>254 quadrilaterals.

```{r fig.cap=c("(ref:sonicrmodelcaption)", "(ref:tailsrmodelcaption)"), side_by_side=TRUE, fig.pos = "H"}
model_viewer('sonic_r_saturn', class="toleft", "(ref:sonicrmodelcaption)")
model_viewer('tails_r_saturn', class="toright", "(ref:tailsrmodelcaption)")
```

While the Saturn is only able to draw quadrangles, you'll soon notice that these models exhibit two triangles instead of a single quadrangle in 'Wireframe' mode. This is because the format used to encode this model (glTF, an open standard for modern 3D modelling), so your modern device can render it, doesn't support quadrangles at the time of this writing. So, I recommend switching to 'Surface' mode to observe the quads.

In some way, this tells you how the current graphics technology can struggle to reproduce their ~30-year-old predecessors!

### An introduction to the visibility problem

When 3D polygons are projected onto a 2D space, it is crucial to determine **which polygons are visible from the camera's position and which are hidden behind** `r cite("graphics-vsd")`. Otherwise, models are not drawn correctly, effects like transparency appear 'broken' and ultimately, hardware resources are wasted. This process is widely known as **Visible surface determination** or 'VSD' and it's a fundamental problem in the world of computer graphics. There are multiple papers published that describe algorithms that tackle this at different stages of the graphics pipeline. Some of them give very accurate results, while others trade precision for better performance.

Now, unlike academic/professional equipment, consumer hardware is incredibly limited, so the choice of algorithm is narrowed down to just a few... or none whatsoever.

(ref:projectzcaption) Project Z-Treme (2019, Homebrew) `r cite("graphics-ztreme")`.<br>This engine ditched Z-sort in favour of a binary space partitioning (BSP) approach, fixing the glitches.

```{r fig.cap="(ref:projectzcaption)", open_float_group=TRUE, fig.align='center'}
image('projectz.jpg', "(ref:projectzcaption)", float=TRUE)
```

The Sega Saturn approach is what I consider a 'semi-solved' case. The VDP1 doesn't implement any VSD function: You either feed the geometry in the correct order or you get a mess. However, Sega provided a graphics library called 'SGL' that implemented a solution called **Z-sort** or **Painter's algorithm** `r cite("graphics-sgl")` which performs **polygon sorting by software**. 

Essentially, SGL allocates a buffer to sort the polygons based on the distance from the camera (from furthest to nearest), then, issues the display commands to the VDP1 in that order.

`r close_float_group(with_markdown = TRUE)`

One of the issues of Z-sort with 3D spaces is that its distance value (Z-order) is **approximated**, so graphic glitches may still appear. For this, programmers can skip SGL in favour of implementing their own algorithm.

In later articles, you will see alternative approaches. Some still rely on software, while others are accelerated by hardware.

### The transparency issue

The Sega Saturn is capable of drawing **half-transparent graphics**, in other words, mixing overlapping layers of colours (blending) to give the illusion we can see through them. Unfortunately, both VDPs aren't as coordinated as one would expect, so this effect will not work properly when these layers are found in different VDPs.

As a workaround, games can activate the 'mesh' property on a texture. With 'meshed' textures, the VDP1 sets the odd X/Y texture coordinates as 'transparent' (empty). Making it possible to blend other layers using the transparent pixels. Curiously enough, the mesh would appear blurred if the console was connected to the TV using the composite video signal (which was pretty much the standard back then, aside from RF) resulting in an accidental but effective way to accomplish half-transparency `r cite("graphics-geer")`.

As you may suspect, this just wasn't viable for some games, so in the end, these had no option but to ditch half-transparency altogether... Although some studios found ingenious fixes, take a look at these two cases:

(ref:daytonaplaycaption) Sega's Daytona (1993).

(ref:sonicrplaycaption) Traveller's Tales' Sonic R (1997).

```{r fig.cap=c("(ref:daytonaplaycaption)", "(ref:sonicrplaycaption)"), side_by_side=TRUE, fig.pos = "H"}
video('daytona', class="toleft", "(ref:daytonaplaycaption)")
video('sonicr', class="toright", "(ref:sonicrplaycaption)")
```

Both games command the VDP1 to draw foreground objects and background scenery. In turn, the VDP2 renders the landscape image far away and the stats in front of the 3D models. Consequently, VDP1 models with half-transparency won't refract the VDP2's landscape as the VDP1 is not aware of the VDP2's frame buffers.

During my terrible gameplay, you'll notice that the background of the first game pops out of nowhere (no half-transparency) whereas the second game not only accomplished half-transparency but also a **fading effect**: Traveller's Tales found a workaround by changing the 'mix ratio' registers of the VDP2 (used for defining the texture's alpha) combined with switching the lighting levels as the character gets closer `r cite("graphics-burton")`.

## Audio

The sound subsystem consists of several parts `r cite("audio-scsp")`:

- **Motorola 68EC000**: Controls the other components and interfaces with the main CPUs. It runs a [sound driver](`r ref("mega-drive-genesis#the-conductor")`) to operate the neighbour components.
- **Saturn Custom Sound Processor** (SCSP): Also referred to as Yamaha YMF292, it's composed of two modules:
  - A **multi-function sound generator**: Processes up to **32 channels** with **PCM samples** (up to 16-bit with 44.1 kHz, a.k.a 'CD quality') or [**FM channels**](`r ref("mega-drive-genesis#audio")`). In the case of the latter, a number of channels are reserved for operators.
  - A **DSP**: Applies effects like echo, reverb and chorus. The docs also mention 'filters' but I don't know if it means envelope or frequency filter (i.e. low pass, etc).
- **512 KB of RAM**: Stores the driver, audio data (i.e. PCM samples) and it's also a working area for the DSP.

### The opportunity

The new audio capabilities mean that studios can finally record/produce soundtracks in-house and then bundle them in the game without having to re-arrange it (as it happened with limited [sequencers](`r ref("super-nintendo#audio")`) or chips with strict [synthesis methods](`r ref("mega-drive-genesis#audio")`)).

This has been possible thanks to a combination of many factors:

- The new storage medium for games (CD-ROM) enables developers to store large soundtracks.
- The audio endpoint receives and mixes PCM data with acceptable quality.
- The audio subsystem provides enough power and bandwidth to stream PCM data in some compressed form, and then decode it on-the-fly.

## Operating System

Once the user powers on the console, the first component that starts up is the **System Management & Peripheral Control** (SMPC), a 4-bit microcontroller that takes care of initialising the neighbouring chips (such as switching on two SH-2s and setting them in a 'master-slave' configuration) `r cite("games-smpc")`.

(ref:japlogocaption) Japanese version.

(ref:eulogocaption) European and American versions.

(ref:logocaption) Logo displayed after splash animation finishes.

```{r fig.cap=c("(ref:logocaption) (ref:japlogocaption)", "(ref:logocaption) (ref:eulogocaption)"), side_by_side=TRUE, fig.pos = "H", figure = TRUE}
image('ipl/logo_jap.jpg', class="toleft", "(ref:japlogocaption)", caption.post="(ref:logocaption) ")
image('ipl/logo_eu.jpg', class="toright", "(ref:eulogocaption)", caption.post="(ref:logocaption)")
figcaption("(ref:logocaption)")
```

Afterwards, the master SH-2's reset vector is set to `0x00000000` `r cite("games-sh2")`, which points to an internal ROM containing the **Initial Program Loader** (IPL). This program performs the following functions `r cite("operating_system-bootrom")`:

1. Finish initialising the hardware.
2. If there's a cartridge inserted and it includes a program, continue booting from there.
4. If the 'Video CD' card is inserted, boot it.
3. If there's a disc inserted, check that it's genuine.
    - While at it, it displays the splash screen animation.
4. If the disc is genuine, boot the game.
4. If the disc is not genuine or there's no disc inserted, run the shell.

### Interactive shell

Alternatively to playing games, the Saturn included a music player called 'Multiplayer', from which a save manager can be opened.

(ref:multiplayercaption) Interactive shell called 'Multiplayer' or 'Audio CD Control Panel'.

(ref:memmanagercaption) Memory Manager. Moves saves between the cartridge and internal memory.

```{r fig.cap=c("(ref:multiplayercaption)", "(ref:memmanagercaption)"), side_by_side=TRUE, fig.pos = "H"}
image('ipl/multiplayer.jpg', class="toleft", "(ref:multiplayercaption)")
image('ipl/mem_manager.jpg', class="toright", "(ref:memmanagercaption)")
```

If a Video CD card is inserted, the player can reproduce MPEG video decoded from the card itself.

### No BIOS?

Unlike the [PlayStation](`r ref("playstation")`) whose ROM chip bundled a [BIOS](`r ref("playstation#operating-system")`), which in turn exposed APIs for programmers to use. The Saturn's ROM is often called 'IPL' presumably since its main job is to bootstrap the game and run the shell. However, the latter still stores some routines (called **services**) to manipulate the hardware (such as managing save data and power control). It even implements a 'semaphore'! (used to synchronise operations that involve multiple processors at the same time). Hence, that part of the ROM is called **System program**.

## Games

Official Sega Saturn games are loaded from the 2x CD-ROM reader. Its medium, the compact disc (CD), has a capacity of 680 MB. Sega Saturn games follow the ISO9660 for storing data in the CD `r cite("games-format")`. Many games store audio tracks next to the data tracks for streaming uncompressed audio while executing the game.

### Development

At first, Sega didn't provide complete software libraries and development tools (in fact, the initial documentation was inaccurate) so the only way to achieve good performance was through *harsh* assembly.

Later on, Sega released complete SDKs, hardware kits and some libraries to ease I/O and graphics operations. Overall, games are written in a mix of **C** and various assemblies targeting individual components.

### I/O

Peripheral management and real-time clock are also provided by the aforementioned **System Management & Peripheral Control** (SMPC). The SMPC is controlled with commands sent by the SH-2s.

### Expansion methods

This console bundles a considerable number of external connectors and interfaces that only received a handful of uses, at most.
- Behind the drive there's a **cartridge slot** officially used for **additional storage** (save data) or **extra RAM**. In Japan and the United Stated, a modem was also offered to provide [online functionality](`r ref("mega-drive-genesis#early-network-attempts")`).
- At the back of the console, there's a slot for a **Video CD Card** that performs MPEG decompression for programs/games that support it.
- Finally, there's a mysterious socket at the back of the console called **Communication Connector**. Sega didn't publish any documentation for developers, but after some reverse engineering efforts, people discovered that it's connected to the SCSP's MIDI pins and the two SH-2's serial interface (SCI) `r cite("games-development")`. In any case, Sega released a Floppy drive that relied on this interface.

## Anti-Piracy & Homebrew

In response to the easiness of cloning a CD, Saturn added a copy protection system (along with region locking) to control the distribution of games.

Copy protection on CDs is applied by burning special data (called 'system area') out of reach from conventional burners, the Saturn refuses to boot the disc as a 'game disc' if the out-of-reach data is not found or it's invalid. The disc reader also contains a custom **SH-1** processor that interfaces with the rest of the console using obscured protocols.

It's worth mentioning that since Saturn CDs follow the ISO9660 (a standard file system for CD data), PCs can read the game disc without problems (but, of course, they can't execute the game unless they use an emulator).

### Defeat

First of all, the classic method used for disabling the copy protection consisted in installing a **mod-chip** that could trick the CD reader when a burned disc is inserted. There was also a 'swap trick' that consisted in **hot-swapping** a genuine disc with a burned one just after the protection checks passed... with the risk of damaging the drive!

After the turn of the century, alternative but more sophisticated methods used for running unauthorised code were discovered, for instance:

- An **exploit in the copy protection mechanism** was found and it allowed to boot up any disc game without going through the copy protection checks. This was subsequently in the form of a cartridge called **pseudosaturn** `r cite("anti_piracy-pseudosaturn")`. Due to the use of the cartridge medium, Action Replay cartridges are often re-flashed with pseudosaturn (though the flasher also needs to be bootstrapped somehow, most commonly through the swap trick).
  - This method is still being used as of 2022, although a new fork of pseudosaturn named 'Pseudo Saturn Kai' is installed instead.
- Another method was reported in 2016 (almost 20 years later) by exploiting the fact that the **Video CD add-on can inject unencrypted code** to the CD subsystem (bypassing the CD reader altogether). This finally allowed users to load Homebrew independently of the ageing drive. The Video CD exploit is commercially distributed in a product called 'Satiator' (I'm not sponsored, by the way).
- Finally, there's another commercial alternative that replaces the CD reader with an SD or SATA adapter. The Saturn still thinks it's reading from a CD, but the 'CD' is being emulated by the adapter, which is in turn reading from a disc image `r cite("anti_piracy-ode")`.  These products are called **Optical Drive Emulators** (ODE).

## That's all folks

(ref:mysaturncaption) A Japanese Saturn I acquired to get more material for this article. While the games look fine, it was thanks to the Saturn's enormous Homebrew library that I was able to understand the real capabilities of this console.

```{r fig.cap="(ref:mysaturncaption)", fig.align='center', centered=TRUE}
image("mine.jpg", "(ref:mysaturncaption)", class = "centered-container")
```
