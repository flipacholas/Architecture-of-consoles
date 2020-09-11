---
name: Nintendo 64
releaseDate: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: Powerful and complicated!
cover: nintendo64
javascript: ['threejs', 'plyr']
top_tabs:
  Model:
    caption: "The Nintendo 64
    <br>Released on 23/06/1996 in Japan, 29/09/1997 in America and 01/03/1997 in Europe"
    file: "international"
  Motherboard:
    caption: "Showing revision 'NUS-CPU-03'.
    <br>Later ones reduced the number of chips required for AV encoding
    <br>Disk Drive connector is found on the back"
  Diagram: {}

# SEO
title: Nintendo 64 Architecture
---

## A quick introduction

Nintendo's goal was to give players the *best* graphics possible, for this it will partner with one of the biggest players in computer graphics to produce the *ultimate* graphics chip.

The result was a nice-looking console for the family... and a 500-page manual for the developer.

Don't worry, I promise you this article will not be *that* long... Enjoy!

---

## CPU

The main processor is a **NEC VR4300** that runs at **93.75 MHz**, it's a binary-compatible version of Silicon Graphics' **MIPS R4300i** that features:
- A **MIPS III ISA**: Succeeds the MIPS II featuring new 64-bit instructions. 64-bit words are called *doublewords*.
- An internal **64-bit bus** while connected to an **external 32-bit data bus**.
- **5-stage pipeline**: Up to five instructions can be executed simultaneously (a detailed explanation can be found in a [previous article]({{% ref "sega-saturn" %}}#cpu)).
- **24 KB L1 cache**: Divided in 16 KB for instructions and 8 KB for data.

An internal **64-bit FPU** is also included in this package, the CPU identifies it as a co-processor (*COP1*) although the unit is fitted next to the ALU and it's only accessed through the ALU pipeline, meaning there's no co-processing per se.

#### Simplified memory access

The way RAM is assembled follows the **unified-memory architecture** or 'UMA' where all available RAM is centralised in one place only and any component that requires RAM will access this shared location. The component arbitrating its access is, in this case, the GPU.

The reason for choosing this design comes to the fact that it saves a considerable amount of production costs while, on the other side, it increments access contention if not managed properly.

#### No DMA controller?

Due to the unified memory architecture, the CPU no longer has direct access to RAM, so the GPU will be providing DMA functionality as well.

#### RAM Available

Apart from the UMA, the structure of RAM is a little bit complicated, so I'll try to keep it simple, here it goes...

The system physically contains **4.5 MB of RAM**, however it's connected using a **9-bit** data bus where the 9th bit is reserved for the GPU (more details later). As a consequence, every component except the GPU will only find **up to 4 MB**.

The type of RAM fitted in the board is called **Rambus DRAM** or 'RDRAM' for short, this was just another design that competed against SDRAM on becoming the next standard. RDRAM is connected in **serial** (where transfers are done one bit at a time) while SDRAM uses a **parallel connection** (transfers multiple bits at a time).

RDRAM's latency is directly proportional to the number of banks installed so in this case, with the amount of RAM this system has, the resulting latency **is significant**.

By contrast, the amount of available RAM on this console **can be expanded** by installing the *Expansion Pak* accessory: A fancy-looking small box that includes 4.5 MB. Curiously enough, the RAM bus must be terminated, so the console always shipped with a terminator (called *Jumper Pak*) fitted in the place of the Expansion Pak. Now, you may ask, what would happen if you switch on the console without any *Pak* installed? **Literally nothing**, you get a blank screen!

---

## Graphics

The core of the graphics reside on a huge chip designed by Silicon Graphics called **Reality Co-Processor** running at **62.5 MHz**. This package contains *a lot* of circuitry so don't worry if you find it difficult to follow, the graphics sub-system has a very complex architecture!  
This design is based on the philosophy that the GPU is not meant to be a 'simple' rasteriser like the [competitor's]({{< ref "playstation" >}}#graphics). Instead, it should also be capable of **accelerating geometry calculations** (offloading the CPU), and for that, more circuitry will be needed.

Having said that, this chip is divided into three main modules, two of them are used for graphics processing:

{{< tabs >}}
  {{< tab active="true" name="Reality Signal Processor" >}}
    
{{< float_block >}}
  {{< linked_img src="RSP.png" alt="RSP Diagram" >}}
  <figcaption class="caption">Architecture of the RSP</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Also known as **RSP**, it's just another CPU package composed of:

- The **Scalar Unit**: A MIPS R400-based CPU which implements a subset of the R400 instruction set.
- The **Vector Unit**: A co-processor that performs vector operations with 32 128-bit registers. Each register is *sliced* in eight parts to operate eight 16-bit vectors at once (just like SIMD instructions on conventional CPUs).
- The **System Control**: Another co-processor that provides DMA functionality and controls its neighbour module, the RDP (more about it later on).

In order to operate this module, the CPU stores in RAM a series of commands called **Display list** along with the data that will be manipulated, then the RSP reads the list and applies the required operations on it. The available features include geometry transformations (such as perspective projection), clipping and lighting.

This seems straightforward, but how does it perform these operations? Well, here's the interesting part: Unlike its competitors (PS1 and Saturn), **the geometry engine is not hard-wired**. Instead, the RSP contains some memory (4 KB for instructions and 4 KB for data) to store **microcode**, a small program, with no more than 1000 instructions, that **implements the graphics pipeline**. In other words, it directs the Scalar Unit on how it should operate our graphics data. The microcode is fed by the CPU during runtime.

Nintendo provided different microcodes to choose from and, similarly to the [SNES' background modes]({{< ref "super-nintendo">}}#graphics), each one balances the resources differently.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Reality Display Processor" >}}

{{< float_block >}}
  {{< linked_img src="RDP.png" alt="RDP Diagram" >}}
  <figcaption class="caption">Architecture of the RDP</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
After the RSP finished processing our polygon data, it will start sending **rasterisation commands** to the next module, the **RDP**, to draw the frame. These commands are either sent using a dedicated bus called **XBUS** or through main RAM.

The RDP is another processor (this time with fixed functionality) that includes multiple engines used to map textures onto our polygons, mix colours and compose the new frame.

It can process either **triangles** or **rectangles** as primitives, the latter is useful for drawing sprites. The RDP's rasterisation pipeline contains the following blocks:

- A **Rasteriser**: Allocates the initial bitmap that will serve as frame-buffer.
- A **Texture Unit**: Processes textures using 4 KB of dedicated memory (called 'TMEM') allowing up to eight tiles to be used for texturing. It can perform the following operations on them:
  - **Bilinear filtering**: Maps the selected 2D texture over the 3D shape and smooths it to avoid pixelated areas.
    - A 'complete' filter would require four points to carry out the interpolation, however, this console only uses three (**triangular interpolation**) resulting in some anomalies. Thus, certain textures will have to be 'adapted' beforehand.
  - **Mip-Mapping**: Automatically selects a scaled-down version of the texture depending on its **level of detail**. This avoids computing large textures that would be seen far away from the camera and prevents aliasing.
    - If enabled, the N64 maps textures using **trilinear filtering** instead. This new algorithm will also interpolate between the mipmaps to soften sudden changes in the level of detail.
  - **Perspective correction**: Chosen algorithm for mapping textures onto triangles. Unlike [other algorithms]({{< ref "playstation">}}#graphics), this one takes into account the depth value of each primitive, achieving better results.
- A **Colour Combiner**: Mixes and interpolates multiples layers of colours (for instance, to apply shaders).
- A **Blender**: Mixes pixels against the current frame-buffer to apply translucency, anti-aliasing, fog, dithering. It also performs z-buffering (more about it later on).
- A **Memory interface**: Used by the previous blocks to read and write the current frame-buffer in RAM and/or fill the TMEM.

The RDP provides four modes of functioning, each mode combines these blocks differently in order to optimise specific operations.

Since this module is constantly updating the frame-buffer, it handles RAM very differently: Remember the unusual 9-bit 'byte'? The ninth bit is used for frame-buffer-related calculations (like z-buffering and antialiasing) and can only be accessed through the Memory interface.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

The resulting frame must be sent to the **Video Encoder** in order to display it on screen (**DMA** and the **Video Interface** component are essential to accomplish this).

The theoretical maximum capabilities are 24-bit colour depth (16.8 million colours) and 640x480 resolution (or 720x576 in the PAL region). I mention it as 'theoretical' since using the maximum capabilities can be resource-hungry, so programmers will tend to use lower stats to free up enough resources for other services.

#### Quick demo

Let's put all the previous explanations into perspective, for that I'll borrow Nintendo's *Super Mario 64* to show, in a nutshell, how a frame is composed:

{{< tabs >}}
  {{< tab active="true" name="Models and Lighting" >}}
    
{{< float_block >}}
  {{< linked_img src="mario/wireframe.jpg" alt="Wireframe Mario" >}}
  <figcaption class="caption">Primitive view of our scene
  <br>In order to save polygons, some characters are modelled using sprites (quads)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
To start with, our 3D models are located in the cartridge ROM, but in order to keep a steady bandwidth, we need to copy them to RAM first.

Then it's time to build a scene using our models, the CPU could do it by itself but it may take ages, so the task is delegated to the RCP. The CPU will instead send orders to the RCP, this is done by carrying out these tasks:

1. Compose the Display List that contains the operations to be carried out by the RSP and store it in RAM.
2. Point the RSP where the display lists are.
3. Send microcode to the RSP to kickstart the Scalar Unit.

Afterwards, the RSP will start performing the first batch of tasks and the result will be sent to the RDP in the form of rasterisation commands.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Textures and Rasterisation" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Upscaled" active="true" >}}
      {{< linked_img src="mario/result.png" alt="Rendered Mario" >}}
      <figcaption class="caption">Rendered frame (<i>Tada!</i>)
      <br>Upscaled (640x480) for demonstration purposes</figcaption>
    {{< /tab >}}
    {{< tab name="Original" >}}
      {{< linked_img src="mario/result_original.png" alt="Rendered Mario" class="pixel" >}}
      <figcaption class="caption">Rendered frame (<i>Tada!</i>)
      <br>Original resolution (320x240) as seen on screen</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
So far we managed to process our data and apply some effects on it, but we still need to:

- Apply textures and other effects.
- Draw a frame-buffer, so we can display it on a screen.

As you may guess, these tasks will be performed by the RDP. The CPU will feed the data (such as textures) by placing it on RAM, this module has a fixed pipeline but we can select the optimal mode of operation based on the current task to improve the frame-rate.

Once the RDP finishes processing the data, it will write the final bitmap to the frame-buffer area in RAM. Afterwards, the CPU must transfer the new frame to the **Video Interface** (preferably using the DMA) which will in turn sent it to the **Video Encoder** for display.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Designs

Here are some examples of previous 2D characters for the [Super Nintendo]({{< ref "super-nintendo">}}) that have been redesigned for the new 3D era, they are interactive so I encourage you to check them out!

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="zelda_ocarina_link" >}}
    <figcaption class="caption">The Legend of Zelda: Ocarina of Time (1998)
    <br>785 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="kirby_cristals" >}}
    <figcaption class="caption">Kirby 64: The Crystal Shards (2000)
    <br>516 triangles</figcaption>
  </div>
{{< /side_by_side >}}

#### Modern visible surface determination

If you've read about the previous consoles, you came across the never-ending problem regarding [visibility of surfaces]({{< ref "sega-saturn" >}}#an-introduction-to-the-visibility-problem) and by now may think polygon sorting is the only way out of this. Well, for the first time in this series, the RDP features a hardware-based approach called **Z-buffering**. In a nutshell, the RDP allocates an extra buffer called **Z-Buffer** in memory. This has the same dimensions of a frame-buffer, but instead of storing RGB values, each entry contains the depth (Z-value) of the nearest pixel with respect to the camera.

After the RDP rasterises the vectors, the z-value of the new pixel is compared against the respective value in Z-buffer. If the new pixel contains a smaller z-value, it means the new pixel is positioned in front of the previous one, so it's applied onto the frame-buffer and the z-buffer is also updated. Otherwise, the pixel is discarded.

Overall, this is a huge welcomed addition: Programmers do not need to worry anymore about implementing [software-based]({{< ref "playstation">}}#tab-2-2-visibility-approach) polygon sorting methods which drain a lot of CPU resources. However, Z-buffer does not save you from feeding unnecessary geometry (discarded or overdrawn, both consuming resources). For this, game engines may choose to include an **occlusion culling** algorithm to discard unseen geometry as early as possible.

#### Secrets and limitations

SGI clearly invested a lot of technology into this system. Nonetheless, this was a console meant for the household and as such, it had to keep its cost down. Some hard decisions resulted in difficult challenges for programmers:

{{< tabs >}}

{{% tab active="true" name="Pipeline Stalls" %}}

Due to the huge number of components and operations in the graphics pipeline, the RCP ended up being very susceptible to **stalls**: An undesirable situation where sub-components keep idling for considerable periods because the required data is delayed at the back of the pipeline.

This will always result in performance degradation and is up to the programmer to avoid them. Although to make things easier, some CPUs such as the Scalar Unit implement a feature called **Bypassing** which enables to execute similar instructions at a faster rate by bypassing some execution stages that can be skipped.
For example, if we have to compute sequential `ADD` instructions there's no need to write the result back to a register and then read it back every time each `ADD` is finished, we can instead keep using the same register for all additions and do the write-back once the last `ADD` is completed.

{{% /tab %}}

{{% tab name="Texture memory" %}}

The RDP relies on 4 KB of TMEM (Texture memory) to load textures. Unfortunately, in practice 4 KB happened to be insufficient for high-resolution textures. Furthermore, if mipmapping is used, the available amount of memory is then reduced to half.

As a result, some games used solid colours with Gouraud shading (like *Super Mario 64*) and others relied on pre-computed textures (for example, where multiple layers had to be mixed).

{{% /tab %}}
{{< /tabs >}}

---

## Audio

Before we go into the details, let's define the two endpoints of the audio sub-system:
- Our starting point is the cartridge ROM, it contains data that only the CPU can interpret.
- The ending point is the **Digital-to-Analog converter** or 'DAC', which only understands *waveform data*.

Now, how do we connect both ends? Consoles normally include a dedicated audio chip that does the work for us. Unfortunately, the Nintendo 64 **doesn't have such dedicated chip**, so this task is distributed across these components:
- The **main CPU**: Transfers the audio data from the game's ROM to RAM, then it initialises **Audio Lists** to be used by the RSP.
- The **RSP**: With the use of even more microcode, it interprets the audio lists previously stored in RAM and performs the required operations to the audio data which, for example, can include:
  - Uncompressing **ADPCM samples** and applying effects.
  - Sequencing and mixing **MIDI data** using **audio banks** stored in RAM as well.

The resulting data is, as expected, waveform data. This is then sent to the **Audio Interface** or 'AI' block which will then transfer it to the digital-to-analogue converter. The resulting waveform contains two channels (since our system is stereo) with 16-bit resolution each.

{{< centered_container >}}
  {{< linked_img src="Audio.png" alt="Audio Diagram" >}}
  <figcaption class="caption">Overview of how the audio pipeline is often programmed</figcaption>
{{< /centered_container >}}

#### The repertoire

Time to checkout the soundtracks made for the N64. There are too many (good ones) to mention in this article, so here are some that caught my attention:

{{< side_by_side >}}
  <div class="toleft">
    {{< video src="observatory" >}}
    <figcaption class="caption">The Legend of Zelda: Majora's Mask (2000)
    <br>The music of this game is tied to its daunted atmosphere</figcaption>
  </div>

  <div class="toright">
    {{< video src="redial" >}}
    <figcaption class="caption">Bomberman Hero (1998)
    <br>This game has nice and unique house-based soundtrack</figcaption>
  </div>
{{< /side_by_side >}}

#### Secrets and limitations

Because of this design, the constraints will depend on the implementation:
  - Sampling rate can be up to 44.1Hz, but using the top rate will steal lots of CPU cycles.
  - There's no strict limit in the number of channels, it all depends how much the RSP is capable of mixing (often around 16-24 channels if processing ADPCM or ~100 if PCM).
  - Memory is another concern, while competitors relied on larger mediums (i.e. CD-ROM) and dedicated audio memory, Nintendo 64 cartridges hold much less data (let alone music data) and have to share its main memory with other components. For those reasons, players may notice that N64 ports contain lesser quality music or repeated scores.
    - A method for overcoming this limitation consisted in implementing a software sequencer that could 'construct' the samples at runtime using a pre-populated set of sounds (similar to MIDI music).

---

## Operating System

Similar to the PS1 and Saturn, N64 games are written for bare-metal. However, there are no BIOS routines available to simplify some operations. As a substitute, **games embed small OS** that provides a fair amount of abstraction to efficiently handle the CPU, GPU and I/O.

This is not the conventional *desktop OS* that we may imagine at first, it's just a micro-kernel with the smallest footprint possible that provides the following functionality:

- Multi-Threading (bear in mind the CPU is single-core).
- Scheduling and Preemption.
- Simplified register and I/O access.

The kernel is automatically embedded by using Nintendo's libraries. Additionally, if programmers decide not to include one of the libraries, the respective portion of the kernel is stripped to avoid cartridge space being wasted.

#### Input/Output

As you know by now, I/O is not directly connected to the CPU, so the RCP's third module (which I haven't mentioned until now) serves as an **I/O interface**, it basically communicates with the CPU, controllers, game cartridge and Audio/Video DACs.

---

## Games

Nintendo held on to the cartridge medium for storage and as a consequence, games enjoyed higher bandwidths (between 5-50 MB/s depending on the ROM's speed) while being more expensive to produce. The biggest cartridge found in the market has 64 MB.

Inside cartridges manufacturers may include extra memory (in the form of *EEPROM*, *flash* or *SRAM* with a battery) to hold saves, however this is not a strong requirement any more since certain accessories could be used to store saves as well.

#### Accessories

The Nintendo 64 controller included a connector used to plug in accessories, some of them are:
- The **Controller Pak**: Another medium (similar to Sony's *Memory Card*) used to store save data and use it on other consoles.
- The **Rumble Pak**: Contains a small motor in order to provide haptic feedback, useful for immersing the player on certain games.

All accessories connected to the controller are managed by the **Peripheral Interface**.

Apart from that, this console included a special connector at the bottom of its motherboard which was meant to be used by the yet-unreleased **Disk drive**, some sort of an 'extra floor' that contained a proprietary disk reader, the drive was only released on Japan nonetheless and eventually cancelled for the rest of the world.

#### Source Development Kit

In general, development was mainly done in C, assembly was also used to achieve better performance. While this system contained a 64-bit instruction set, 64-bit instructions were rarely used since in practice, 32-bit instructions happened to be faster to execute and required half the storage.

Libraries contained several layers of abstractions in order to command the RCP, for example, structs like the **Graphics Binary Interface** or 'GBI' were designed to assemble the necessary Display lists more easily, the same applied for audio functions (its struct was called **Audio Binary Interface** or 'ABI').

In terms of microcode development, Nintendo already provided a set of microcode programs to choose from, however if developers wanted to customise it, that would indeed be a challenging tasks: The Scalar Unit instruction set wasn't initially documented (at the request of Nintendo, of course), later on the company changed its position and SGI finally released some documentation for microcode programming.

Hardware used for development included workstations supplied by SGI, like the **Indy** machine which came with an extra daughterboard called **U64** that contains the hardware and I/O of the retail console. Tools were supplied for Windows computers as well.

Other third-party tools consisted in cartridges featuring a long ribbon cable that connected to the workstation. This cartridge fitted in a retail Nintendo 64 but included internal circuitry to redirect the *read* requests from the console to the workstation's RAM. The deployment/debugging process was carried out by transferring a copy of the game to RAM and then, when the console was switched on, it would start reading from there.

---

## Anti-piracy / Region Lock

The anti-piracy system is a continuation of the [SNES' CIC]({{< ref "super-nintendo.md" >}}#anti-piracy--region-lock). As you know, bootleg detection and region locking are possible thanks to the CIC chip (which must be present in every *authorised* game cartridge), the Nintendo 64 improved this system by requiring different games to have a specific variant of the CIC chips in order to make sure the cartridge was not a counterfeit or contained a CIC clone, the **Peripheral Interface** or 'PIF' would do checksum checks at the start and during gameplay to supervise current CIC installed on the cartridge.

If by any reason the PIF considers the current cartridge is not valid, it will then induce the console in a permanent freeze.

Region-locking was done by slightly altering the shape of the cartridge between different regions so the user can't physically insert the game on an N64 from a different region.

Overall, there was not too much concern regarding piracy thanks to the use of cartridge medium, although game prices were three times higher than CD-based ones.

#### Unused ports

As silly as it may seem, Nintendo left one door opened: The **Disk Drive port**.

A few companies reversed engineered the interface in order to develop their own hardware, and some of the resulting products became a concern for piracy.

I guess the one worth mentioning is the **Doctor v64**, this device has the same shape as the Disk Drive but included a CD-ROM drive that's used to clone the contents of the cartridge to a CD, the opposite (reading Roms from a CD) is also possible.

#### Emulation

When I was a kid I used to play some N64 games on a Pentium II machine using an emulator, it wasn't *that bad* but then I wondered now *how the freck* was it able to happily emulate a complex 64-bit machine since, among other things, my PC barely had enough RAM to keep the integrated video alive.

The truth is, while reproducing the architecture of this console can be complex, things like microcode will give a hint of what the console is trying to do, and since emulators *don't have to be* cycle-accurate, they can apply enough optimisations to provide more performance in exchange for real emulation.  
Another example are the 64-bit instructions, since games barely used them, emulation speed would hardly be hit when running on a 32-bit host machine.

---

## That's all folks

I have to say, this article may be the longest one I've ever written, but hopefully you found it a nice read!

I'll probably take the following days to tide up some things on the website instead of starting to write the next article.

Anyway, if you enjoy my articles and would like to help, please take a look [here](/support). If you have any comments or suggestions, feel free to email me {{< email "here" >}}.

Until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- [**Detailed N64 programming manual**](http://n64devkit.square7.ch/)
- [**Development Software Archive**](http://ultra64.ca/resources/software/)
- [**Anatomy of this system**](https://tomplaskon.wordpress.com/2014/06/30/anatomy-of-a-nintendo-64/)

#### CPU

- [**MIPS R4300i data-sheet**](http://datasheets.chipdb.org/MIPS/R4300i_datasheet.pdf)
- [**NEC VR4300 data-sheet**](http://datasheets.chipdb.org/NEC/Vr-Series/Vr43xx/U10504EJ7V0UMJ1.pdf)
- [**More about the memory system** (Archived)](https://web.archive.org/web/20200430063847/http://en64.shoutwiki.com/wiki/N64_Memory)

#### Graphics / Audio

- **Nintendo Ultra64 RSP Programmer’s Guide**
- [**Different Microcodes**](https://www.retroreversing.com/n64rsp)
- [**The Models Resource** (Archived)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)

#### Games

- [**GamePak (Wikipedia)**](https://en.wikipedia.org/wiki/Nintendo_64_Game_Pak)
- [**Inside some GamePaks**](https://imgur.com/gallery/KteOi)
- [**Development Hardware**](https://n64squid.com/homebrew/n64-sdk/development-hardware/)
- [**More about the U64**](http://n64devkit.square7.ch/n64man/u64/u64.htm)

#### Anti-piracy

- [**Reversing the Nintendo 64 CIC (REcon talk)**](https://www.youtube.com/watch?v=HwEdqAb2l50)

#### Photography

- Motherboard and console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams and game screenshots: **Me**

#### Bonus

- [**Laserjet printers use the same CPU!**](http://www.nec.co.jp/press/en/9801/2002.html)