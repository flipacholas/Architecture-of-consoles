---
name: Sega Saturn
subtitle: What can you do with 8 processors?
date: 2019-08-03
releaseDate: 1994-11-22
generation: 5
cover: saturn
javascript: ['threejs']
published: true
top_tabs:
  Models:
    International:
      caption: "The Sega Saturn, released on 11/05/1995 in America and 08/07/1995 in Europe"
    Japanese:
      caption: "The Sega Saturn, released on 22/11/1994 in Japan"
  Motherboard:
    caption: "Showing 'VA8' revision which includes all components in a single board
    <br>Remaining RAM chips are fitted on the back"
  Diagram: {}

# SEO
title: Sega Saturn Architecture

# Historical
aliases: [/projects/consoles/sega-saturn/]
---

## A quick introduction

Welcome to the 3D era! Well... *sorta*. Sega enjoyed quite a success with the Megadrive so there's no reason to force developers to write 3D games *right now*.

Just in case developers want 3D, Sega adapted some bits of the hardware to enable polygon drawing as well. Hopefully, the result didn't get out of hand!

---

## CPU

The system has not one but **two Hitachi SH-2** CPUs running at **~28.63MHz each**. While both physically identical, they are placed in a master-slave state, where the first one may send commands to the second one. This can achieve some degree of parallelism, albeit both sharing the same external bus (which can lead to congestion).

These processors are part of the Hitachi SH7600 brand, a series designed for embedded systems featuring:
- **SuperH ISA**: A special 32-bit RISC instruction set where instructions are 16-bit long. Not only does this design reduce the size of the programs, but since the CPU fetches instructions in 32-bit batches, two instructions can be retrieved in one cycle.
- **Five-stage pipeline**: Execution of instructions is divided into five steps or *stages*. The CPU will queue up to five instructions where each one is allocated in one stage. This allows to take advantage of all the CPU's resources without idling while also incrementing the number of instructions executed per unit of time.
- **One multiplication unit**: Speeds up multiplication operations with 64-bit/32-bit integers.
- **32-bit data bus**: The external bus is shared across the two CPUs.
- **4 KB cache**: Stores a small amount of instructions and data previously fetched from memory to speed up future reads.

The specific CPU model selected for this console, the 'SH7604' or just 'SH-2', contain the following additions:
- **One division unit**: Speeds up division operations with 64-bit/32-bit integers.
- **Internal DMA controller**: Transfers data from memory independently (without the intervention of the CPU).

Having two CPUs doesn't mean that games will work twice as fast, in practice, however, this requires very complex programming to efficiently manage CPUs that share the same bus! Cache comes in handy for this occasion.

### A divided choice of memory

The system contains a total of **2 MB of RAM** for general purpose usage called **Work RAM** (WRAM). Now, these two megs are split between two very different blocks. The first one provides **1 MB of SDRAM** and due to its fast access rates, this block is also called 'WRAM-H'. The other block contains the other megabyte, but it's named 'WRAM-L' since it uses **DRAM** instead, resulting in lower rates.

### Another processor

The console contains an additional coprocessor, the **Saturn Control Unit** or 'SCU' which is composed of two modules:
- **A DMA controller**: Arbitrates access to WRAM across the three main buses without the intervention of the CPUs.
- **A DSP**: Used as a fixed-point 'geometry unit'. Compared to the SH-2, it does matrix/vectors calculations such as 3D transformations and lighting faster. However, it runs at half-speed and its instruction set is more complex. Moreover, it relies on the SH-2's WRAM to fetch and store data (using the DMA).

The SCU comes with **32 KB of SRAM** for local use. It's worth mentioning that the SCU can't access WRAM-L.

---

## Graphics

Since the Saturn is the first '3D console' reviewed for [this series]({{< ref "../consoles" >}}), let us first go over the fundamental design changes that made way to the new generation of 3D graphics:

- GPU now relies on a **frame-buffer**: Graphics are no longer required to be rendered on-the-fly. Instead, the GPU reserves a portion of VRAM to draw a bitmap with all the computed geometry requested by the CPU, then a video encoder picks up that region and outputs it through the video signal.
  - Consequently, having this reserved 'working space' allows the GPU to continue manipulating the bitmap even after finishing rendering the scene, so the CPU may now offload some exhaustive tasks such as lighting and anti-aliasing to the GPU. Here is when the term of **graphics pipeline** starts to gain momentum.
- **More VRAM required**: The use of a frame-buffer implies an increment of memory requirements (which is not a big issue any more), the amount of RAM required for a frame-buffer is proportional to the dimension of the screen and the number of colours used. As an example, with 600 KB of VRAM we can contain a frame-buffer of 640x480 pixels wide with 32k colours per pixel (16 bpp).
  - Additionally, programmers are free to organise their VRAM usage: Not every single bit has to be allocated for the frame-buffer, so why don't we also use it to cache textures, render other frame-buffers concurrently and add colour lookup tables to speed things up?
- CPU incorporates **vector operations**: A GPU with 3D capabilities would be incomplete without a proper CPU capable of feeding the required geometry. For that reason, next-gen CPUs include a form of specialised instructions that accelerates vector calculations, these are known as **Single instruction multiple data** or 'SIMD' extension.
  - In the case of the Saturn, vector operations are accelerated by the Saturn Control Unit (not by the CPUs).

### Sega's offering

This console includes **two proprietary GPUs**, each one serving different purposes while working concurrently. Some may argue that the new GPUs are an evolution of the [classic VDP]({{< ref "mega-drive-genesis#graphics" >}}), while others may say it's a complete redesign... I think it's a bit of both.

Having said that, let's take a look at the two chips.

{{< tabs >}}

{{< tab name="VDP1" active="true" >}}
{{< figure_img float="true" src="vdp/VDP1.png" alt="VDP1 architecture" >}}
VDP1 Architecture
{{< /figure_img >}}

{{% inner_markdown %}}
The **Video Display Processor 1** (VDP1) is a chip that draws sprites with geometric transformations. The results are written onto a frame-buffer, which is in turn streamed to the VDP2 for display.

This chip is programmed by sending 'drawing commands' to it. So, programmers are provided with **512 KB of dedicated RAM** to store these drawing commands and the required materials (textures/tiles, colour lookup tables, etc).

Consequently, the VDP1 is designed to use **quadrilaterals as primitives**, which means that it can only compose models using 4-vertex polygons (sprites). The chip applies **Forward Texture Mapping** to connect texture points onto the quadrilateral, in that direction. It doesn't come with any filtering/interpolation technique, so the calculations are subject to **aliasing**.

The VDP1 also provides this selection of effects:

- Two **shading algorithms** (Flat and Gouraud) for lighting.
- **Anti-aliasing**: In this case, it duplicates pixels to cover gaps during mapping.
- **Clipping** to discard polygons outside the camera's viewport.
- **Transparency** to blend two non-opaque bitmaps.

**Two 256 KB framebuffer chips** are available to concurrently draw new scenes of the game without breaking the current one being displayed. When the secondary buffer is finished being drawn, the VDP1 starts broadcasting the latter instead (**page-flipping**), and the cycle continues.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="VDP2" >}}
{{< figure_img float="true" src="vdp/VDP2.png" alt="VDP2 architecture" >}}
VDP2 Architecture
{{< /figure_img >}}

{{% inner_markdown %}}
The **Video Display Processor 2** (VDP2) specialises in rendering large (4096×4096 pixels) planes with transformations (rotation, scale and translation) applied on them.

More importantly, the VDP2's renders **on-the-fly** (without a frame-buffer) like previous [tile-based engines]({{< ref "mega-drive-genesis#constructing-the-frame" >}})). It can display up to **16.7 million colours** (24-bit). This chip is also responsible for displaying the VDP1's buffer, which can also be transformed and/or mixed with the VDP2's layers. The VDP2's 'frame' is composed of up to four 2D planes and one 3D plane; or two 3D planes.

This chip relies on [tile-maps]({{< ref "mega-drive-genesis#constructing-the-frame" >}}) to compose planes and performs **perspective correction** for 3D texture mapping, this is a more sophisticated approach which takes into account the depth value to compute rotations.

Effects available include **multi-texturing** (mapping more than one texture per polygon) and **shadowing**. With the latter, after the VDP2 receives the sprites generated by the VDP1, it can reduce their brightness and blend them with half-transparency. Nonetheless, the VDP2 only receives a sprite stream from the VDP1 (in pace with the CRT beam) so this function tends to be tricky to encode and operate.

This chip also houses **4 KB of Colour RAM (CRAM)** which is used to translate VDP1's custom colour values (index colours) into 24-bit RGB colours.

Finally, even though the VDP2 is limited to two 3D planes, nothing prevents the CPU from using its VRAM as frame-buffer area to draw additional 2D or 3D graphics in software.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

I recommend checking out the sources (at the end of the article) if this section got your attention, since the VDPs have a lot more quirks that are beyond the scope of this article.

### Defining the problem

As you can see, the architecture of the graphics sub-system is quite complex, so it's interpreted differently depending on the needs:

### As a *powerful* 2D console

The capabilities of the Saturn for drawing 2D scenes were huge compared to the [MegaDrive]({{< ref "mega-drive-genesis" >}}) or [SNES]({{< ref "super-nintendo" >}}), although they weren't the main selling point of this console.

{{< tabs >}}

{{< tab active="true" name="Sprites" >}}
{{< figure_img float="true" src="2d/sprites.png" alt="Sprites" >}}
VDP1's Sprites plane.  
Mega Man X4 (1997).
{{< /figure_img >}}

{{% inner_markdown %}}
In this case, the VDP1 is tasked with plotting traditional sprites without any 3D distortion applied.

The CPU sets up the VDP1 by writing over its registers and filling its VRAM with commands and tiles. The process can also be accelerated thanks to the DMA controller. 
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Backgrounds" >}}
{{< tabs float="true" nested="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="2D plane 1" active="true" src="2d/bg1.png" >}}
2D plane 1.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="2D plane 2" src="2d/bg2.png" >}}
2D plane 2.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="2D plane 3" src="2d/bg3.png" >}}
2D plane 3.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}
VDP2's Background planes.  
Mega Man X4 (1997).
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
The VDP2 is then instructed to draw background planes. These, along with the sprite layer, are automatically mixed to form a fully coloured scene.

The commanding part is fundamentally similar to the VDP1: Programmers got registers and VRAM to set up accordingly.

Some functions from the VDP2 can be exploited to create more realistic scenery, such as scaling to simulate a heatwave (see '2D plane 2').
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}
{{< figure_img float="true" src="2d/result.jpg" alt="Result" >}}
Mixed planes (_Tada!_).  
Mega Man X4 (1997).
{{< /figure_img >}}

{{% inner_markdown %}}
Not much mystery here, the VDP2 is responsible for the last step of sending the processed signal to the video encoder.

The VDP2 operates in sync with the CRT beam, meaning that its computations correspond to the pixels that will be displayed on the next scan line.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### As a *challenging* 3D console

Here's where the Saturn *shined and struggled* at the same time. While this console had eight processors to take advantage of, it all came down to:
- Whether programmers would be able to master most of the console's features during a small time frame (remember the console's commercial lifespan would be over once its successor is released, or even announced).
- Whether their game would be shipped at a reasonable date.

For this reason, most games ended up dramatically ranging in quality since each studio came up with their unique solution.

{{< tabs >}}

{{< tab active="true" name="3D modelling" >}}
{{< figure_img float="true" src="3d/models.png" alt="3D Models" >}}
3D models of characters without textures or background.  
Notice the primitives used to build the models.  
Virtua Fighter Remix (1995).
{{< /figure_img >}}

{{% inner_markdown %}}
So far we've been using single quadrilaterals to form sprites or background layers. But what if we grab multiple primitives and arrange them to form a more complex figure? This is how 3D models come to fruition.

In a nutshell, the CPUs and SCU are tasked with formulating a 3D world and project it in a 2D space. Then, both VDPs are commanded to render it, apply effects and finally broadcast it on TV.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Pixel processing" >}}

{{< figure_img float="true" src="3d/complete.png" alt="3D Scene" >}}
Rendered scene with 3D models and backgrounds.  
Virtua Fighter Remix (1995).
{{< /figure_img >}}

{{% inner_markdown %}}
Either VDP can draw this new 3D space and stamp textures and effects. Now, which chip is 'in charge' varies between each game.

Some prioritised the VDP1 to draw the closest polygons and left the VDP2 to process distant scenery, others found interesting workarounds to task the VDP2 to draw closer polygons (off-loading the amount of geometry fed into the VDP1). The challenge consists in designing an efficient engine that could display *impressive* graphics while keeping an acceptable frame rate.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### The new designs

These are some examples of characters that were re-designed for this console, the models are interactive so do try to fiddle with them!

{{< side_by_side >}}
  {{< threejs_canvas model="sonic_r" class="toleft" >}}
Sonic in Sonic R (1997)  
298 triangles
  {{< /threejs_canvas >}}

  {{< threejs_canvas model="sonic_r_tails" class="toright" >}}
Tails in Sonic R (1997)  
425 triangles
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

### An introduction to the visibility problem

When 3D polygons are projected onto a 2D space, it is crucial to determine **which polygons are visible from the camera's position and which are hidden behind**. Otherwise, models are not drawn correctly, effects like transparency appear 'broken' and ultimately, hardware resources are wasted. This process is widely known as **Visible surface determination** or 'VSD' and it's a fundamental problem in the world of computer graphics. There are multiple papers published that describe algorithms that tackle this at different stages of the graphics pipeline. Some of them give very accurate results, while others trade precision for better performance.

Now, unlike academic/professional equipment, consumer hardware is incredibly limited, so the choice of algorithm is narrowed down to just a few... or none whatsoever.

{{< float_group >}}

{{< figure_img float="true" src="projectz.jpg" alt="Project Z" >}}
This engine ditched Z-sort in favour of a binary space partitioning (BSP) approach, fixing the glitches.  
Project Z-Treme (2019, Homebrew)
{{< /figure_img >}}

{{% inner_markdown %}}
The Sega Saturn approach is what I consider a 'semi-solved' case. The VDP1 doesn't implement any VSD function: You either feed the geometry in the correct order or you get a mess. However, Sega provided a graphics library called 'SGL' that implemented a solution called **Z-sort** or **Painter's algorithm** which performs **polygon sorting by software**. 

Essentially, SGL allocates a buffer to sort the polygons based on the distance from the camera (from furthest to nearest), then, issues the display commands to the VDP1 in that order.
{{% /inner_markdown %}}

{{< /float_group >}}

One of the issues of Z-sort with 3D spaces is that its distance value (Z-order) is **approximated**, so graphic glitches may still appear. For this, programmers can skip SGL in favour of implementing their own algorithm.

In later articles, you will see alternative approaches. Some still rely on software, while others are accelerated by hardware.

### The transparency issue

The Sega Saturn is capable of drawing **half-transparent graphics**, in other words, mixing overlapping layers of colours (blending) to give the illusion we can see through them. Unfortunately, both VDPs aren't as coordinated as one would expect, so this effect will not work properly when these layers are found in different VDPs.

As a workaround, games can activate the 'mesh' property on a texture. With 'meshed' textures, the VDP1 sets the odd X/Y texture coordinates as 'transparent' (empty). Making it possible to blend other layers using the transparent pixels. Curiously enough, the mesh would appear blurred if the console was connected to the TV using the composite video signal (which was pretty much the standard back then, aside from RF) resulting in an accidental but effective way to accomplish half-transparency.

As you may suspect, this just wasn't viable for some games, so at the end, these had no option but to ditch half-transparency all-together... Although, some studios found ingenious fixes, take a look at these two cases:

{{< side_by_side figure="true" >}}
  {{< video src="daytona" class="toleft" >}}
Sega's Daytona (1993)
  {{< /video >}}
  {{< video src="sonicr" class="toright" >}}
Traveller's Tales' Sonic R (1997)
  {{< /video >}}
  {{< figcaption group="true" >}}Both games command the VDP1 to draw foreground objects and background scenery. The VDP2 draws instead the landscape image far away and the stats in front of the 3D models. With this layout, VDP1 models with half-transparency won't refract the VDP2's landscape as the VDP1 is not aware of the VDP2's frame-buffers.{{< /figcaption >}}
{{< /side_by_side >}}

Apart from my terrible gameplay, you'll notice that the background of the first game pops out of nowhere (no half-transparency) whereas the second game not only accomplished half-transparency but also a **fading effect**: Traveller's Tales found a workaround by changing the 'mix ratio' registers of the VDP2 (used for defining the texture's alpha) combined with switching the lighting levels as the character gets closer.

---

## Audio

The sound subsystem consists of several parts:
- **Motorola 68EC000**: Controls the other components and interfaces with the main CPUs. It runs a [sound driver]({{< ref "mega-drive-genesis#the-conductor" >}}) to operate the neighbour components.
- **Saturn Custom Sound Processor** (SCSP): Also referred to as Yamaha YMF292, it's composed of two modules:
  - A **multi-function sound generator**: Processes up to **32 channels** with **PCM samples** (up to 16-bit with 44.1 kHz, a.k.a 'CD quality') or [**FM channels**]({{< ref "mega-drive-genesis#audio" >}}). In the case of the latter, a number of channels are reserved for operators.
  - A **DSP**: Applies effects like echo, reverb and chorus. The docs also mention 'filters' but I don't know if it means envelope or frequency filter (i.e. low pass, etc).
- **512 KB of RAM**: Stores the driver, audio data (i.e. PCM samples) and it's also a working area for the DSP.

### The opportunity

The new audio capabilities mean that studios can finally record/produce soundtracks in-house and then bundle it in the game without having to re-arrange it (as it happened with limited [sequencers]({{< ref "super-nintendo#audio" >}}) or chips with strict [synthesis methods]({{< ref "mega-drive-genesis#audio" >}})).

This has been possible thanks to a combination of many factors:
- The new storage medium for games (CD-ROM) enables developers to store large soundtracks.
- The audio endpoint receives and mixes PCM data with acceptable quality.
- The audio subsystem provides enough power and bandwidth to stream PCM data in some compressed form, and then decode it on-the-fly.

---

## Games

The console starts by booting from the **IPL (initial program loading) ROM** which initialises the hardware and displays the splash screen. Then the game is loaded from the 2x CD-ROM reader. Its medium (CD) has a capacity of 680 MB.

### Development

At first, Sega didn't provide complete software libraries and development tools (in fact, the initial documentation was inaccurate) so the only way to achieve good performance was through *harsh* assembly.

Later on, Sega released complete SDKs, hardware kits and some libraries to ease I/O and graphics operations. Overall, games are written in a mix of **C** and various assemblies targeting individual components.

### I/O

Peripherals are controlled by the **SMPC** (System Management & Peripheral Control), a micro-controller that also provides a real-time clock and receives commands from the SH-2s.

### Expansion

There's a **cartridge slot** used for **additional storage** (save data) or **extra RAM**.

Another expansion slot is found near the CD Reader, this one expects a **Video CD Card** that, as the name suggests, enables to play Video CD.

Finally, there's a mysterious socket at the back of the console called **Communication Connector**. Sega didn't publish any documentation for developers, but after some reverse engineering efforts, people discovered that it's connected to the SCSP's MIDI pins and the two SH-2's serial interface (SCI). Sega released a Floppy drive that relied on this interface.

---

## Anti-Piracy & Homebrew

Copy protection on CDs is applied by burning special data out of reach from conventional burners, the Saturn CD reader refuses to read the disc if the out-of-reach data is not found. The disc reader also contains a custom **SH-1** processor that interfaces with the rest of the system using encrypted communication. It's worth mentioning that Saturn CDs don't have any reading protection, so you can actually access its content from a PC.  

A popular method of disabling the copy protection was by installing mod-chips that could trick the CD reader when a burned disc is inserted.

A more sophisticated method for running unauthorised code was published in 2016 (almost 20 years later) by exploiting the fact that the Video CD add-on can inject unencrypted code to the CD subsystem (bypassing the CD reader altogether). This finally allowed to load Homebrew without depending on the ageing drive.

---

## That's all folks
