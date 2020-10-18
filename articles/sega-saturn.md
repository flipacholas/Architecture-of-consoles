---
name: Sega Saturn
subtitle: What can you do with 8 processors?
date: 2019-08-03
releaseDate: 1994-11-22
generation: 5
cover: saturn
javascript: ['threejs', 'plyr']
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
---

## A quick introduction

Welcome to the 3D era! Well... *sorta*. Sega enjoyed quite a success with the Megadrive so there's no reason to force developers to write 3D games *right now*.

Just in case developers want 3D, Sega adapted some bits of the hardware to enable polygon drawing as well, hopefully, the result didn't get out of hand!

---

## CPU

The system has not one, but two **Hitachi SH-2** CPUs running at ~28.63MHz each. While both physically identical, they are placed in a master-slave state, where the first one may send commands to the second one. This can achieve some degree of parallelism, albeit both sharing the same external bus (which can lead to congestion).

These processors are part of the Hitachi SH7600 Series, a series designed for embedded systems featuring:
- **SuperH ISA**: A special 32-bit RISC instruction set where instructions are 16-bit long. Not only this design reduces the size of the program, but since the CPU fetches instructions in 32-bit batches, two instructions can be retrieved in one cycle.
- **Five-stage pipeline**: Execution of instructions is divided into five steps or *stages*. The CPU will queue up to five instructions where each one is allocated in one stage. This allows to take advantage of all the CPU's resources without idling while also incrementing the number of instructions executed per unit of time.
- **One multiplication unit**: Speeds up multiplication operations with 64-bit/32-bit integers.
- **Two 32-bit internal buses**: giving a theoretical 64-bit bus, registers are still 32-bit.
- **32-bit external bus** shared across the two CPUs.
- **4 KB cache**: Stores a small amount instructions and data previously fetched from memory to speed up future reads.

The specific CPU model selected for this console, the 'SH7604' or just 'SH-2', contain the following additions:
- **One division unit**: Speeds up division operations with 64-bit/32-bit integers.
- **Internal DMA controller**: Transfers data from memory independently (without the need of using the CPU).

Having two CPUs doesn't mean that games will work twice as fast, in practice, this requires very complex programming to efficiently manage CPUs that share the same bus! Here is when cache comes very handy.

The console contains an additional coprocessor, the **Saturn Control Unit** or 'SCU' which is composed of two modules:
- **A DMA controller**: Arbitrates access to WRAM across the three main buses without the intervention of the CPUs.
- **A DSP**: Used as a fixed-point Geometry unit. Compared to the SH-2, it does matrix/vectors calculations such as 3D transformations and lighting faster, however, it runs at half-speed and has a more complex instruction set, it also relies on the SH-2's WRAM to fetch and store data (using the DMA).

#### A divided choice of memory

The system contains a total of **2 MB** of RAM for general purpose usage, this is called **Work RAM** or 'WRAM'. Now, these two megs are split between two very different blocks. The first one provides 1 MB of SDRAM and due to its fast access rates, this block is also called 'WRAM-H'. The other block contains the other megabyte, but it's named 'WRAM-L' since it uses DRAM instead, resulting in lower rates. It's worth mentioning that the SCU can't access the latter type.

---

## Graphics

Since the Saturn is the first '3D console' reviewed for [this series]({{< ref "../consoles" >}}), let us first go over the fundamental design changes that made way to the new generation of 3D graphics:

- GPU now relies on a **frame-buffer**: Graphics are no longer required to be rendered on-the-fly, instead the GPU reserves a portion of VRAM to draw a bitmap with all the computed geometry requested by the CPU, then a video encoder picks up that region and outputs it through the video signal.
  - Consequently, having this reserved 'working space' allows the GPU to continue manipulating the bitmap even after finishing rendering the scene, so the CPU may now offload some exhaustive tasks such as lighting and anti-aliasing to the GPU. Here is when the term of **graphics pipeline** starts to gain momentum.
- **More VRAM required**: The use of a frame-buffer implies an increment of memory requirements (which is not a big issue any more), the amount of RAM required for a frame-buffer is proportional to the dimension of the screen and the number of colours used. As an example, with 600 KB of VRAM we can contain a frame-buffer of 640x480 pixels wide with 32k colours per pixel (16 bpp).
  - Additionally, programmers are free to organise their VRAM usage: Not every single bit has to be allocated for the frame-buffer, so why don't we also use it to cache textures, render other frame-buffers concurrently and add colour lookup tables to speed things up?
- CPU incorporates **vector operations**: A GPU with 3D capabilities would be incomplete without a proper CPU capable of feeding the required geometry. For that reason next-gen CPUs include a form of specialised instructions that accelerates vector calculations, these are known as **Single instruction multiple data** or 'SIMD' extension.
  - In the case of the Saturn, vector operations are accelerated by the Saturn Control Unit (not by the CPUs).

#### Sega's offering

This console includes **two 32-bit** proprietary GPUs, each one serving different purposes while working concurrently:

{{< tabs >}}

{{< tab name="VDP1" active="true" >}}

{{< float_block >}}
  {{< linked_img src="vdp/VDP1.png" alt="VDP1 architecture" >}}
  <figcaption class="caption">VDP1 Architecture</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The **Video Display Processor 1** or 'VDP1' is a custom chip specialised in rendering polygons, it is designed to use **quadrilaterals as primitives** which means that it can only compose models using 4-vertex polygons.

Textures are applied using the following algorithms:

1. **Forward Texture Mapping** to map the textures into each quad. It is subject to some aliasing.
2. **Bilinear Approximations** to correct unstable textures (noticeable while slowly moving the camera view), this effect is also called *texture warping*.

Since texture-related operations tend to make intensive use of the memory bus, programmers are provided with 512 KB of VRAM to cache textures and avoid congesting the bus, resulting in better fill-rates.

The chip also provides this selection of effects:

- Two **shading algorithms** (Flat and Gouraud) for lighting.
- **Edge anti-aliasing** to smooth out jagged edges.
- **Clipping** to discard polygons outside the camera's viewport.
- **Transparency**.

Two 256 KB frame-buffers are available to concurrently draw new scenes of the game without breaking the current one being displayed (**double-buffering**). When the secondary buffer is finished being drawn, it is then copied to the primary one during special events (like V-Blank) so the user doesn't notice this operation.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="VDP2" >}}


{{< float_block >}}
  {{< linked_img src="vdp/VDP2.png" alt="VDP2 architecture" >}}
  <figcaption class="caption">VDP2 Architecture</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The **Video Display Processor 2** or 'VDP2' specialises in rendering large (4096×4096 pixels) planes with the ability to apply transformations (rotation, scale and translation) on them. It can either draw up to four 2D planes and one 3D plane; or two 3D planes.

Its features were technically advanced at the time, algorithms used to accomplish these include:

- The use of [classic tile-maps]({{< ref "mega-drive-genesis#constructing-the-frame" >}}).
- **Perspective transformations** to project 3D objects onto a 2D space and map textures.
- **Multi-texturing** to map more than one texture per polygon.
- **Bump-mapping** to simulate bumps on the plane's surface without spending extra polygons.

This chip also contains 4 KB of CRAM enabling it to display up to 16.7 millions of colours, which the VDP1 can also access. The VDP2's frame is generated by first mixing the VDP1's frame-buffer with its own ones.

Even though the VDP2 is limited to two 3D planes, nothing prevents the CPU from using its VRAM as frame-buffer area to draw additional 3D graphics by software.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Defining the problem

As you can see the architecture of the graphics sub-system is quite complex, so it's interpreted differently depending on the needs:

#### As a *powerful* 2D console

The capabilities of the Saturn for drawing 2D scenes were huge compared to the [MegaDrive]({{< ref "mega-drive-genesis" >}}) or [SNES]({{< ref "super-nintendo" >}}), although they weren't the main selling point of this console.

{{< tabs >}}

{{< tab active="true" name="Sprites" >}}


{{< float_block >}}
  {{< linked_img src="2d/sprites.png" alt="Sprites" >}}
  <figcaption class="caption">VDP1/Sprites plane
  <br>Mega Man X4 (1997)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
In this case, the VDP1 is tasked to draw plain individual quadrilaterals that are filled with textures (one per polygon), this is how sprites are achieved.
{{% /inner_markdown %}}


{{< /tab >}}

{{< tab name="Backgrounds" >}}


{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="2D plane 1" active="true" >}}
      {{< linked_img src="2d/bg1.png" >}}
    {{< /tab >}}
    {{< tab name="2D plane 2" >}}
      {{< linked_img src="2d/bg2.png" >}}
    {{< /tab >}}
    {{< tab name="2D plane 3" >}}
      {{< linked_img src="2d/bg3.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">VDP2/Background planes
  <br>Mega Man X4 (1997)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The VDP2 is then required to draw multiple background planes that are finally mixed forming a fully coloured scene.

Some functions from the VDP2 can be exploited to create more realistic scenery, such as scaling to simulate a heat wave (see '2D plane 2').
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}


{{< float_block >}}
  {{< linked_img src="2d/result.jpg" alt="Result" >}}
  <figcaption class="caption">Mixed planes (<i>Tada!</i>)
  <br>Mega Man X4 (1997)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Not much mystery here, the VDP2 is tasked with the last step of combining all frame-buffers and letting the video encoder take it from there.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### As a *challenging* 3D console

Here's where the Saturn *shined and struggled* at the same time. While this console had eight processors to take advantage of, it all came down to:
- Whether programmers would be able to master most of the console's features during a small time frame (remember the console's commercial lifespan would be over once its successor is released, or even announced).
- Whether their game would be shipped at a reasonable date.

For this reason, most games ended up dramatically ranging in quality since each studio came up with their unique solution, the possible permutations were almost infinite!

{{< tabs >}}

{{< tab active="true" name="Models" >}}


{{< float_block >}}
  {{< linked_img src="3d/models.png" alt="3D Models" >}}
  <figcaption class="caption">3D models of characters without textures or background
  <br>Notice the primitives used to build the models
  <br>Virtua Fighter Remix (1995)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
So far we've been using single quadrilaterals to form sprites or background layers. But what if we group multiple primitives to form a more complex figure? This is how 3D models come to fruition.

In a nutshell, the CPU is tasked with formulating a 3D world, while both VDPs will be commanded to project it, apply textures and effects on it and finally display it in a 2D space.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Textures" >}}


{{< float_block >}}
  {{< linked_img src="3d/complete.png" alt="3D Scene" >}}
  <figcaption class="caption">Rendered scene with 3D models and backgrounds
  <br>Virtua Fighter Remix (1995)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Either VDP can draw this new 3D space and stamp textures and effects. Now, which chip is 'in charge' varies between each game.

Some prioritised the VDP1 to draw the closest polygons and left the VDP2 to process distant scenery, others found interesting workarounds to task the VDP2 to draw closer polygons (off-loading the amount of geometry fed into the VDP1). The challenge was to design an efficient engine that could display *impressive* graphics while keeping an acceptable frame-rate.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### An introduction to the visibility problem

When 3D polygons are projected onto a 2D space, it is crucial to determine **which polygons are visible from the camera's position and which are hidden behind**. Otherwise, models are not drawn correctly, effects like transparency appear 'broken' and ultimately, hardware resources are wasted. This process is widely known as **Visible surface determination** or 'VSD' and it's a fundamental problem in the world of computer graphics. There are multiple papers published that describe algorithms that tackle this at different stages of the graphics pipeline. Some of them give very accurate results, while others trade precision for better performance. Now, unlike academic/professional equipment, consumer hardware is incredibly limited, so the choice of algorithm is narrowed down to just a few... or none whatsoever.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="projectz.jpg" alt="Project Z" >}}
  <figcaption class="caption">This engine ditched Z-sort in favour of a binary space partitioning (BSP) approach, fixing the glitches
  <br>Project Z-Treme (2019, Homebrew)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Sega Saturn approach is what I consider a 'semi-solved' case. The VDP1 doesn't implement any VSD function: You either feed the geometry in the correct order or you get a mess. However, Sega provided a graphics library called 'SGL' that implemented a solution called **Z-sort** or **Painter's algorithm** which performs **polygon sorting by software**. 

Essentially, SGL allocates a buffer to sort the polygons based on the distance from the camera (from furthest to nearest), then, it issues the display commands to the VDP1 in that order.  
One of the issues of Z-sort with 3D spaces is that its distance value (Z-order) is **approximated**, so graphic glitches may still appear. For this, programmers can skip SGL in favour of implementing their own algorithm.
{{% /inner_markdown %}}

{{< /float_group >}}

In later articles, you will see alternative approaches. Some still rely on software, while others are accelerated by hardware.

#### The new designs

These are some examples of characters that were re-designed for this console, the models are interactive so do try to fiddle with them!

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="sonic_r" >}}
    <figcaption class="caption">Sonic in Sonic R (1997)
    <br>298 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="sonic_r_tails" >}}
    <figcaption class="caption">Tails in Sonic R (1997)
    <br>425 triangles</figcaption>
  </div>
{{< /side_by_side >}}

#### The transparency issue

The Sega Saturn is capable of drawing **half-transparent graphics**, in other words, mixing overlapping layers of colours to give the illusion we can see through them. Unfortunately, both VDPs aren't as coordinated as one would expect, so this effect will not work properly when these layers are spread around the two VDPs at the same time.

As a workaround, games could activate the 'mesh' property on a texture. With 'meshed' textures, the VDP sets the odd X/Y texture coordinates as 'transparent'. Making it possible to blend other layers using the transparent pixels. Curiously enough, the mesh would appear blurred if the console was connected to the TV using the composite video signal (which was pretty much the standard back then, aside from RF) resulting in an accidental but effective way to accomplish halt-transparency.

As you may suspect, this just wasn't viable for some games, so at the end, these had no option but to ditch half-transparency all-together.  
Although... some found ingenious fixes, take a look at these two cases:

{{< side_by_side >}}
  <div class="toleft">
    {{< video src="daytona" >}}
    <figcaption class="caption">Sega's Daytona (1993)</figcaption>
  </div>
  
  <div class="toright">
    {{< video src="sonicr" >}}
    <figcaption class="caption">Traveller's Tales' Sonic R (1997)</figcaption>
  </div>
  <figcaption class="caption">Both games command the VDP1 to draw foreground objects and background scenery. The VDP2 draws instead the landscape image far away and the stats in front of the 3D models. With this layout, VP1 models with half-transparency won't refract the VDP2's landscape as the VDP1 is not aware of the VDP2's frame-buffers.</figcaption>
{{< /side_by_side >}}

Apart from my terrible gameplay, you'll notice that the background of the first game pops out of nowhere (no half-transparency) whereas the second game not only accomplished half-transparency but also a **fading effect**: Traveller's Tales found a workaround by changing the 'mix ratio' registers of the VDP2 (used for defining the texture's alpha) combined with switching the lighting levels as the character gets closer.

---

## Audio

The sound subsystem consists in several components:
- **Motorola 68EC000**: Works as a controller and interfaces with the main CPUs.
- **Saturn Custom Sound Processor**: Composed of two modules:
  - A multi-function sound generator: Supports up to 32 channels for PCM samples with a sampling rate of 44.1 kHz (CD quality), or 8 channels for FM synthesis (the remaining 24 channels are used as operators).
  - A DSP: Applies effects like reverb or room acoustics.
- **512 KB of RAM**: Used to store sound programs or samples.

---

## Games

The console starts by booting from the **IPL (initial program loading) ROM** which initialises the hardware and displays the splash screen. Then the game is loaded from the 2x CD-ROM reader, its medium (CD) has a capacity of 680 MB of data.

#### Development

At first, Sega didn't provide complete software libraries and development tools (even the documentation was inaccurate) so the only way to achieve good performance was through *harsh* assembly. Later on, Sega released complete SDKs, hardware kits and some libraries to ease I/O and graphics operations. Overall, games are written in a mix of C and various assemblies targeting individual components.

#### I/O

Peripherals are handled by the **SMPC** (System Management & Peripheral Control), a micro-controller that also provides a real-time clock and allows the SH-2 to program them.

#### Expansion

The cartridge slot is used to provide **storage** (save data) or **extra RAM**. Another expansion slot is found near the CD Reader, this one expects a 'Video CD Card' that, as the name suggests, enables to play Video CD.

---

## Anti-Piracy & Homebrew

Copy protection on CDs is applied by burning special data out of reach from conventional burners, the Saturn CD reader refuses to read the disc if the out-of-reach data is not found. The disc reader also contains a custom **SH-1** processor that interfaces with the rest of the system using encrypted communication. It's worth mentioning that Saturn CDs don't have any reading protection, so you can actually access its content from a PC.  

A popular method of disabling the copy protection was by installing mod-chips that could trick the CD reader when a burned disc is inserted.

A more sophisticated method for running unauthorised code was published in 2016 (almost 20 years later) by exploiting the fact that the Video CD add-on can inject unencrypted code to the CD subsystem (bypassing the CD reader altogether), this finally allowed load custom code without depending on the ageing drive.

---

## Sources / Keep Reading

#### General

- [**SegaRetro's SH-2 article including some official docs (Archived)**](https://web.archive.org/web/20171114020844/http://segaretro.org/SH-2)
- [**SegaRetro's comparison with other systems (Archived)**](https://web.archive.org/web/20190502055725/https://segaretro.org/Sega_Saturn/Hardware_comparison)
- [**Wikipedia's technical documentation**](https://en.wikipedia.org/wiki/Sega_Saturn#Technical_specifications)
- [**Very complete compilation of docs and tools**](https://antime.kapsi.fi/sega/docs.html)

#### CPU

- [**More about the SMPC** (Archived)](https://web.archive.org/web/20190621223925/https://wiki.yabause.org/index.php5?title=SMPC)
- **Sega Saturn Dual CPU User's Guide**
- **Sega Saturn Technical Bulletin #2**
- **SH-2: SH7604 Product Brief**

#### Graphics

- [**Official VDP1 User's guide**](https://segaretro.org/images/8/8b/ST-013-R3-061694.pdf)
- [**Official VDP2 User's guide**](https://segaretro.org/images/c/c1/ST-058-R2-060194.pdf)
- [**In-depth study about Saturn's graphics capabilities**](https://www.youtube.com/watch?v=f_OchOV_WDg)
- [**Analysis of Saturn's transparency routines**](https://www.mattgreer.org/articles/sega-saturn-and-transparency/)
- [**Traveller's Tales secrets explained by no other than Jon Burton**](https://www.youtube.com/watch?v=FdD0GvVRSMc)
- [**The Models Resource** (Archived)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)
- **Official SGL Developer's Manual**
- [**Overview of different algorithms for Visible Surface Determination**](https://www.cs.uic.edu/~jbell/CourseNotes/ComputerGraphics/VisibleSurfaces.html)
- [**More about Project Z-treme** (Archived)](https://web.archive.org/web/20200527233242/https://sonicfangameshq.com/forums/showcase/project-z-treme-sega-saturn-sage-2019-tech-demo.323/)
- [**Unofficial Sonic using 'Z-sorted' Z-treme engine** (Archived)](https://web.archive.org/web/20191108221705/https://sonicfangameshq.com/forums/showcase/sonic-z-treme-saturn-sage-2018-demo.161/)

#### Copy protection

- [**Cracking the Sega Saturn CD reader** (Archived)](http://web.archive.org/web/20191112225106/https://assemblergames.com/threads/saturn-optical-drive-emulator.62274/)

#### Photography

- Motherboard and console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams and game screenshots: **Me**

#### Extra

- [**Other Motherboard revisions**](https://dragoncity17.wordpress.com/2014/07/15/saturn-differencier-les-versions/)
