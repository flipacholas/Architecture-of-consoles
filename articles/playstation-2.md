---
name: PlayStation 2
subtitle: Overshadowing the rest
date: 2020-04-08
releaseDate: 2000-03-04
cover: ps2
javascript: ['threejs']
generation: 6
top_tabs:
  Model:
    file: international
    caption: "The original Playstation 2
    <br>Released on 04/03/2000 in Japan, 26/10/2000 in America and 24/11/2000 in Europe"
  Motherboard:
    caption: "Showing revision 'GH-001' from model SCPH-10000 only released in Japan
    <br>Thanks to the donations received, I was able to purchase this model and take a proper photo to allow me identify most of the chips
    <br>I presume the chip at the bottom right corner is the 4 MB BIOS ROM"
  Diagram:
    caption: "The original design (Implemented on revision 'SCPH-10000')
    <br>Each data bus is labelled with its width and speed
    <br>This architecture went through many revisions, more details below"

# SEO
title: Playstation 2 Architecture

# Historical
aliases: [/projects/consoles/playstation-2/]
---

## A quick introduction

The Playstation 2 was not one of the most powerful consoles of its generation, yet it managed to achieve a level of popularity unthinkable for other companies.

This machine is nowhere near as simple as the [original Playstation]({{< ref "playstation" >}}) was, but we will see why it didn't share the same fate of [previous complicated consoles]({{< ref "sega-saturn" >}}).

---

## CPU

At the heart of this console we find a powerful package called **Emotion Engine** or 'EE' designed by Sony and running at **~294.91 MHz**. This chipset contains multiple components, one of them being the main CPU. The rest are at the CPU disposal to speed up certain tasks.

### The leader

The main core is a **MIPS R5900-compatible** CPU with lots of enhancements. This is the first chip that starts executing instructions after the console is turned on. The processor provides the following features:
- **MIPS III ISA**: A 64-bit RISC instruction set. *Wait, is it me or this is the same ISA found on a [competitor's console]({{< ref "nintendo-64#cpu" >}})?*. Not quite, Sony enhanced the ISA by adding some instructions from **MIPS IV** (prefetch and conditional move) along with their own SIMD extension called **multimedia instructions**. 
- **32 128-bit extra registers**: Another enhancement. They are better managed using multimedia instructions and are very useful for vector processing.
  - These registers are accessed through a 128-bit bus, while the rest of the CPU uses an internal 64-bit bus.
- **2-way superscalar**: Up to two instructions are executed in parallel.
- **24 KB L1 cache**: Divided into 16 KB for instructions and 8 KB for data.
  - It also implements a **prefetch function** to cache instructions and data before they are requested. This is done by including extra circuitry that can identify which places in memory are more often requested.
- **16 KB of Scratchpad RAM**: Also known as 'Fast RAM'.
- **Memory management unit**: Interfaces memory access with the rest of the system.

The core is complemented with a **dedicated floating point unit** (identified as 'COP1') that accelerates operations with 32-bit floating point numbers (also known as `floats` in C). This is a peculiar block as it doesn't follow the IEEE 754 standard, most evident with its absence of `infinity` (computed as `0` instead). For more information, check out Krysto's 'Nightmare on Floating-Point Street' (link in the 'Sources' section).

### A recognisable memory choice

Next to the Emotion Engine are two blocks of 16 MB of RAM, giving a total of **32 MB** of main memory. The type of memory used is **RDRAM** ([*déjà vu!*]({{< ref "nintendo-64#memory-design" >}})) which is accessed through a 16-bit bus.

{{< float_group >}}
{{< figure_img float="true" src="MemoryArch.png" alt="Emotion engine Memory architecture" >}}
Memory design of the Emotion Engine  
You can guess where the congestion is gonna appear
{{< /figure_img >}}

{{% inner_markdown %}}
At first, this can be a little disappointing to hear, considering the internal bus of the Emotion engine is as wide as 128 bits. However, the RAM chips are strategically placed by following the **dual-channel architecture**, which consists in connecting both chips using two independent 16-bit buses (one bus per chip) to improve data throughput. The resulting setup provides a theoretical 3.2 GB/sec, so rest assured that memory latency is not an issue in this console!
{{% /inner_markdown %}}

{{< /float_group >}}

At one corner of the Emotion engine there is a powerful **DMA Controller** or 'DMAC' that transfers data between main memory and Scratchpad; or between main memory and any component inside the EE.

Data transfers are done in batches of 128-bits, but here is the interesting part: Every eight batches, the main bus is temporarily unlocked. This leaves a small window to perform other DMA transfers in parallel (up to ten) or let the CPU use the main bus. This *modus operandi* is called **slice mode** and is one of the many modes available on this DMA unit. Bear in mind that while slice mode reduces stalls on the main bus, it does so at the cost of slowing down the overall DMA transfer.

### Preventing past mishaps

Whether we want it or not, with the amount of traffic happening inside the Emotion Engine, this design will eventually suffer the consequences of the **Unified memory architecture** or 'UMA'. That is... multiple independent components trying to access main memory at the same time, causing congestion. Well, to correct these issues, Sony alleviated the constant need for memory by:

- Wrapping their processors with **lots of cache**. Thus, only requiring access to main memory if absolutely necessary.
    - 99% of cache/scratchpad mentions in this article will be for this reason.
- Adding a 128-byte **Write Back Buffer**: Very similar to the [Write Gather Pipe]({{< ref "gamecube#ibms-enhancements" >}}), but instead of waiting until it's 25% full, it will check the state of the bus (i.e congested or free) first.

This sounds very convenient for applications that can benefit from cache, but what about those tasks, such as manipulating Display Lists, which shouldn't use cache at all? Luckily, the CPU provides a different memory access mode called **UnCached**, which **only** uses the Write Back Buffer. Thus, it will not waste cycles correcting the cache (product of *cache misses*).

Furthermore, the **UnCached accelerated mode** is also available. This one adds a buffer for speeding up read of continuous addresses in memory.

### Other interesting bits

Inside the same Emotion Engine package, there is yet-another processor called **Image Processing Unit** or 'IPU', this time designed for **image decompression**. As the successor of the [MDEC]({{< ref "playstation#tab-2-3-motion-decoder">}}), the IPU can be useful when a game needs to decode an MPEG2 movie without jamming the main CPU.

Long story short, the game sends compressed image streams to the IPU (hopefully using DMA) which is then decoded in a format that the GPU can display. The PS2's operating system also relies on the IPU to provide DVD playback.

Finally, the IPU also operates compressed **High-resolution textures**, which saves CPU usage and reduces large transfers. 

---

## Co CPUs

It's been two years since the rivals presented their [latest offering]({{< ref "dreamcast">}}). If you read the former article and just started reading this one, I presume you are *still* waiting for 'the thing' that makes the PS2 as powerful as it seemed back then. Now, let me introduce a *very* important set of components Sony fitted in the Emotion Engine, the **Vector Processing Units** or 'VPU'.

### Architecture

A Vector Processing Unit is a small independent processor designed to operate vectors. In particular, vectors made of four `floats`. These processors are so fast that they only spend **one cycle per operation**, which can be extremely convenient for geometry processing. Though they exhibit similar unstandardised behaviour to the CPU's FPU.

VPUs are made of the following components:
- Some **Vector Unit Memory** or 'VU Mem': Used as a working space for the Vector unit. It stores values needed to be operated and/or the results of previous operations.
- A **Vector Unit**: The core of the processor. It contains some memory (called **Micro Memory**) to store a program (called **Microprogram**) which instructs the unit on how to operate the data found in 'VU Mem'.
  - It implements a **64-bit ISA** and the execution unit is **split into two parallel sub-units**. The first one multiplies or adds floats, while the other one divides floats or operates integers. This enables to operate both floats and integers **concurrently**.
- A **Vector Interface**: Automatically decompresses vertex data coming from main memory in a format the Vector unit can understand. This unit can also transfer microprograms to Micro Memory.

### Functionality

To start working, the vector unit needs to be 'kickstarted'. For this, the main CPU is in charge of supplying the microcode.

There are **two VPUs** fitted in the Emotion engine, but they are arranged differently, giving way to different uses and optimisations.

{{< tabs >}}
{{< tab active="true" name="Vector Processing Unit 0" >}}
{{< figure_img float="true" src="VU0.png" alt="Architecture of VPU0" img_class="no-borders" >}}
Architecture of VPU0
{{< /figure_img >}}

{{% inner_markdown %}}
The first VPU, the **VPU0**, is positioned between the CPU and the other vector unit (VPU1). It provides an 'assisting' role to the main CPU.

The VPU0 has two modes of operation:
- **Micromode**: This is the 'traditional mode'. The VPU will independently execute 'microinstructions' from a microprogram stored in Micro memory.
- **Macromode**: The VPU0 becomes the 'COP2' of the main CPU and executes 'macro-instructions', received from the main CPU through a dedicated 128-bit bus.
  - Macro-instruction have the same functionality of microinstructions but use different opcodes. Nonetheless, the VPU execution unit is no longer split (meaning it can only execute one instruction at a time).
  - While this mode doesn't make full utilisation of all the components of the VPU0, it still speeds up the CPU's vector operations. Moreover, in terms of simplicity, a co-processor is easier to program than an independent unit (something PC programmers will find helpful).

The memory map of the VPU0 also has access to some of the other VPU's registers and flags, presumably to check its state or quickly read the results of some operations done by the other VPU.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Vector Processing Unit 1" >}}
{{< figure_img float="true" src="VUP1.png" alt="Architecture of VPU1" img_class="no-borders" >}}
Architecture of VPU1
{{< /figure_img >}}

{{% inner_markdown %}}
The second VPU found, the **VPU1**, is an enhanced version of the VPU0 with double the amount of micro memory and VU memory. Moreover, this unit includes an additional component called **Elementary function unit** or 'EFU' which speeds up the execution of exponential and trigonometric functions.

The VPU1 is located between the VPU0 and the Graphics Interface (the 'gate' to the GPU), so it includes additional buses to feed the geometry to the GPU as quickly as possible and without using the main bus.

On the other side and due to its location, the VPU1 **only operates in micromode**.

It's obvious that this VPU was designed for trigonometric operations, and may serve as a pre-processor for the GPU. Hence, it's often put in charge of delivering the famous Display Lists.

{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Infinite worlds

A useful approach that can be exploited with these units is **procedural generation**. In other words, instead of building the scene using hard-coded geometry, let the VPUs generate it using algorithms. In this case, the VPU computes **mathematical functions to produce the geometry** which is then interpreted by the GPU (i.e. triangles, lines, quadrangles, etc) and ultimately used to draw the scene.

Compared to using explicit data, procedural content is ideal for parallelised tasks, it frees up bandwidth, requires very little storage and it's dynamic (programmers can set parameters to achieve different results). There are certain areas that can highly benefit from this technique:
- **Complex surfaces** (e.g. spheres and wheels).
- **World rendering** (e.g terrains, particles, trees).
- **Bezier curves** (a very popular equation in computer graphics which is used to draw curves), these are turned into a **Bezier patch** (explicit geometry) and supports different degrees of precision based on the level of detail required.

On the other side, procedural content may struggle with animations and, if the algorithm is too complex, the VPU might not generate the geometry at the required time.

To sum up, procedural rendering is not a new technique, but thanks to the VPUs, it opens the doors to further optimisations and richer graphics. Nonetheless, is not a simple technique to implement and Sony R&D published various papers describing different approaches to use on their console.

### You define the workflow

With these new additions, programmers now have a lot of flexibility to design their graphics engines. In fact, there are multiple research papers published which benchmark popular pipeline designs.

Here are some examples of graphics pipelines set up with different optimisations:

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Parallel" active="true" src="Parallel.png" alt="Parallel EE pipeline" >}}
Parallel pipeline design
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Serial" src="Serial.png" alt="Serial EE pipeline" >}}
Serial pipeline design
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
In the first example, the **Parallel** design, the CPU is combined with the VPU0 in macromode to produce geometry in parallel with the VPU1. The CPU/VPU0 group makes full utilisation of scratchpad and cache to avoid using the main bus, which the VPU1 relies on to fetch data from main memory. At the end, both rendering groups concurrently send their respective Display Lists to the GPU.

The second example, the **Serial** design, proposes a different approach where the CPU/VPU0 group works as a preprocessor for the VPU1. The first stage will fetch and process all the geometry that the VPU1 will subsequently turn into Display List. 
{{% /inner_markdown %}}

{{< /float_group >}}

These have been so far examples from the theoretical point of view, but in order to explain a more 'practical' implementation, I'm going to refer to a video Jon Burton published regarding the development of one of their PS2 games.

{{< float_group >}}
{{< figure_img float="true" src="Crash.jpg" alt="Crash Bandicoot The Wrath of Cortex" >}}
Particles make the candle flame and the light coming from the window glass  
Crash Bandicoot: The Wrath of Cortex (2001)
{{< /figure_img >}}

{{% inner_markdown %}}
The former director of Travellers Tales explained how his team achieved a particle system fully encapsulated within the VPU1. In a nutshell, the VPU1 focused on reading a pre-populated database from its VU memory, the database was used to calculate the coordinates of particles at any given time without depending on any other component. The result of the operation could be transformed into Display Lists and sent right away.

With this method, the CPU was significantly offloaded, allowing it to carry out other tasks like AI and physics.
{{% /inner_markdown %}}

{{< /float_group >}}

There are many more examples out there, but to sum things up: It is now up to the programmer to find the optimal setup, and that, is a good thing.

---

## Graphics

Considering all the work done by the Emotion Engine, is there something left? The last step actually: Display!

{{< float_group >}}

{{< figure_img float="true" src="ffx.jpg" alt="Final Fantasy X" >}}
Final Fantasy X (2001)
{{< /figure_img >}}

{{% inner_markdown %}}
There's a simple but speedy chip specialised in that: The **Graphics Synthesizer** or 'GS' running at **~147.46 MHz**. It contains **4 MB of DDRAM** embedded inside to do all processing in-house. Thus, removing the need to access the main memory. The embedded RAM is connected using different buses depending on the type of data needed. 

The GS has fewer features than other graphics systems [previously reviewed]({{< ref "gamecube#graphics" >}}) in this site. Nonetheless, it's very fast at what it does.
{{% /inner_markdown %}}

{{< /float_group >}}

### Architecture and design

This GPU only does **rasterisation** and that is... Generating pixels, mapping textures, applying lighting and some other effects. This means there are no vertex transformations (these are covered by the VPUs). Also, this is a fixed-function pipeline, so no [fancy tweaking]({{< ref "gamecube#creativity" >}}) or [shaders]({{< ref "xbox#graphics" >}}) either, you are stuck with a fixed shading model (e.g. Gouraud).

{{< figure_img src="GS_Pipeline.png" alt="Pipeline design of the Graphics Synthesizer" img_class="no-borders" class="centered-container" >}}
Pipeline design of the Graphics Synthesizer
{{< /figure_img >}}

Looks pretty simple right? Well, let's dive deeper to see what happens at each stage.

{{< tabs >}}
{{< tab active="true" name="Pre-Process" >}}

{{< figure_img float="true" src="gs_pipeline/Preprocessing.png" alt="Pre-processing stage" img_class="no-borders" >}}
Pre-processing stage
{{< /figure_img >}}

{{% inner_markdown %}}
The Emotion Engine kickstarts the Graphics Synthesizer by filling its embedded DRAM with the required materials (**Texture bitmaps** and **Colour Lookup tables**, the latter are also known as 'CLUT'), assigning values on the GS's registers to configure it, and finally, issuing the drawing commands (Display Lists) which instruct the GS to draw primitives (points, lines, triangles, sprites, etc) at specific locations of the screen.

Additionally, the GS will preprocess some values that will be needed for later calculations. Most notably, the **Digital Differential Algorithm** value, which will be used for interpolations during drawing.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Rasterization" >}}
{{< figure_img float="true" src="gs_pipeline/Rasterizing.png" alt="Rasterizing stage" img_class="no-borders" >}}
Rasterizing stage
{{< /figure_img >}}

{{% inner_markdown %}}
Using the previous values calculated, the renderer generates pixels from the primitives. This unit can generate 8 pixels (with textures) or 16 pixels (without textures) concurrently, each pixel entry contains the following properties calculated:
- **RGBA**: Corresponding to the gradient of Red, Green, Blue and Alpha (transparency).
- **Z-value**: Used for depth testing in later stages.
- **Fog**: Optional, to simulate fog in the environment.
- **Texture properties**: Contains the address of the texture in DRAM and other properties (coordinates, level of detail, filter, etc) that will be used on the next stage.

It also performs **Scissoring Tests** to discard polygons outside the frame area (based on their X/Y values), some pixel properties are forwarded to the 'Pixel testing' stage for further checks. **Lighting** is also provided by selecting one of the two choices available, **Gauraud** and **Flat**.

The pack is then delivered to the 'Texture mapping' engine, but each property is operated by a specialised 'sub-engine', which enables to process different properties in parallel.  
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Texture" >}}
{{< figure_img float="true" src="gs_pipeline/Textures.png" alt="Texture mapping stage" img_class="no-borders" >}}
Texture mapping stage
{{< /figure_img >}}

{{% inner_markdown %}}
This stage is powered by a large Pixel Unit that can compute up to 16 pixels at the time, here textures will be mapped onto the polygons (now pixels). Furthermore, fog and anti-aliasing effects are applied here.

Texture maps are fetched from DRAM in an area defined as **Texture buffer**, although this is interfaced by a separate area called **Texture Page Buffer** which seems to serve as a caching mechanism for textures. CLUTs are also mapped using this page system. Both elements are retrieved using a **512-bit bus**.

The pixel unit performs **perspective correction** to map textures onto the primitives (a great improvement considering the previous [affine mapping]({{< ref "playstation#tab-4-5-textures" >}}) approach). Moreover, it also provides **bilinear and trilinear filtering**, the later one is used alongside mipmapped textures.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Tests" >}}
{{< figure_img float="true" src="gs_pipeline/Tests.png" alt="Pixel Testing stage" img_class="no-borders" >}}
Pixel Testing stage
{{< /figure_img >}}

{{% inner_markdown %}}
Here certain pixels will be discarded if they don't meet a number of requirements. Having said that, the following tests are carried out:
- **Alpha test**: Compares the alpha value (transparency) of a pixel against the 'standard' value. This is because in some cases, the alpha value is required to be within a certain range or greater/less than an arbitrary value.
- **Destination Alpha test**: This checks the pixel's alpha value again before drawing it to the frame-buffer. 
- **Depth Test**: Compares the pixel's Z-value against the corresponding Z-value in the [Z-buffer]({{< ref "nintendo-64#modern-visible-surface-determination" >}}). This avoids processing pixels that would be hidden behind other pixels.

{{% /inner_markdown %}}{{< /tab >}}
{{< tab name="Post-Process" >}}
{{< figure_img float="true" src="gs_pipeline/Postprocessing.png" alt="Post-processing stage" img_class="no-borders" >}}
Post-processing stage
{{< /figure_img >}}

{{% inner_markdown %}}
The last stage can apply some effects over our new pixels using the previous frame-buffer found in local DRAM:
- **Alpha Blending**: Merges colours of the current buffer with the previous one in memory. 
- **Dithering**: Large RGBA values will need be trimmed, so dithering can be applied to soften the loss of precision.
- **Colour Clamping**: After applying operations like Alpha Blending, the new RGB value may exceed the valid range (0-255), so clamping sets the value within range.
- **Formatting**: This converts the final frame-buffer generated in the pipeline to a format that can be stored in memory.

Finally, the new frame-buffer, along with the updated Z-buffer, are written to memory using a **1024-bit bus**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

### Even more post-processing

There's a dedicated component inside the GS called **Programmable CRT Controller** or 'PCRTC' which sends the frame-buffer in memory to the Video output, so you can see the frame on TV. But that's not all: It also contains a special block called **Merge Circuit** that allows to alpha-blend two separate frame-buffers (useful if games want to reuse the previous frame to form the new one). The resulting frame can be outputted through the video signal and/or written back to memory.

### Better models

With all being said, this surely brought better designs to refresh already-famous characters. Take a look at this 'Before & After':

{{< side_by_side >}}
  {{< threejs_canvas model="crash" class="toleft" >}}
Crash Bandicoot (1996) for the PS1  
732 triangles
  {{< /threejs_canvas >}}
  {{< threejs_canvas model="crash_wrath" class="toright" >}}
Crash Bandicoot: The Wrath of Cortex (2001)  
2226 triangles
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

Here are characters from new game series, these were modelled with high levels of detail from the ground up:

{{< side_by_side >}}
  {{< threejs_canvas model="sora" class="toleft" >}}
Kingdom Hearts (2002)  
2744 triangles
  {{< /threejs_canvas >}}
  {{< threejs_canvas model="hero" class="toright" >}}
Dragon Quest VIII (2004)  
2700 triangles
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

It's worth mentioning that games like *Dragon Quest* implemented a custom lighting model called **Cel Shading** (a term I have mentioned [before]({{< ref "gamecube#creativity" >}})), however, in my previous articles I explained that the GPU was mainly responsible for this. In the PS2 case, the required colour calculations are presumably done by the Emotion Engine, since the GS isn't as flexible as other GPUs.

### Video Output

As stated before, the PCRTC sends the frame-buffer through the video signal. The interface can broadcast video using a wide range of formats (to work with TVs from any geographical region):
- **PAL**: Sends up to 640x512 pixels at 50 Hz, either progressive (576p) or interlaced (576i).
  - No games found in the market use 576p. While some support progressive mode, they do so in 480p mode.
- **NTSC**: Up to 640x448 pixels at 60 Hz, either progressive (480p) or interlaced (480i).
- **VESA**: Up to 1280x1024 pixels.
- **DTV**: Up to the *enormous amount* of 720x480 pixels in progressive mode or 1920x1080 in interlaced mode.
  - By tweaking the PCRTC settings, a game can also force to output in 1080p. However, this mode is undocumented and is subject to unpredictable behaviour.
  - Does that mean the PS2 can 'display HD'? Technically... yes, but I don't think most game studios risked the performance penalty for a format that wasn't popularised yet.

{{< float_group >}}
{{< figure_img float="true" src="photos/ps2_back.png" alt="PS2 back" >}}
Right side of the console, seen from the back  
Showing A/C port, Digital audio port and AV Multi Out
{{< /figure_img >}}
{{% inner_markdown %}}

There're quite a lot of modes to choose from, but it all comes down to the format adoption during the early 2000s, which narrows it down to PAL and NTSC. Also, even though PAL provided a higher resolution than NTSC, some European versions of NTSC games resorted to letterboxing to mask the unused horizontal lines and slowed down the refresh rate to fit in the 50Hz limit (I call these 'bad ports'!).

{{% /inner_markdown %}}
{{< /float_group >}}

The video-out port (**Multi A/V**) is very convenient. It carries RGB, Component, S-Video and composite. So, all the important signals are there without requiring proprietary adapters or internal modifications.

---

## Audio

The new audio chip is an incremental update of the old [SPU]({{< ref "playstation#audio" >}}) called... **SPU2**! Improvements include the inclusion of **2 MB of internal memory** and **48 channels available** (two times the original amount).

The SPU2 is made of two sound processors inside, referred as **CORE0** and **CORE1**; and running at ~36.86 MHz. Each one processes **24 channels**.

Curiously enough, these are still two independent processors and to configure them, you have to alter their registers. However, Sony warned developers that both sets of registers have to be set with 1/48000 seconds of gap. If you hurry too much, the behaviour of the SPU2 becomes unpredictable!

The SPU2 inherits the same effects available from the original SPU. The memory provided is used as a 'work area': You can store raw waveform data and allocate extra space to process it and apply effects on it.

Finally, the chip can mix all channels to provide **stereo output**. Now, here is the interesting part: The SPU2 can feed itself the mixed stereo sample as new input, this enables to EE to access it (to mix it with even more audio, for instance), or keep adding more effects (such as reverb, echo and delay).

{{< side_by_side >}}
  {{< video src="goomy_noreverb" class="toleft" >}}
Without reverb
Kingdom Hearts II (2005)
  {{< /video >}}
  {{< video src="goomy" class="toright" >}}
With reverb
Kingdom Hearts II (2005)
  {{< /video >}}
{{< /side_by_side >}}

The audio signal is outputted through two mediums:
- **Digital Audio**: Referred as the Sony/Philips Digital Interface or 'S/PDIF'.
- **Analog Audio**: Goes through the digital-to-analogue converter and ends at the Multi A/V port.

---

## I/O

The I/O of the PS2 is not complicated, yet multiple revisions of this console completely changed various internal and external interfaces.

To start with, there's a dedicated processor that arbitrates the communication between different components, this CPU is no other than the **original MIPS R3000-based core** found in the [Playstation 1]({{< ref "playstation#cpu" >}}). This time, it's called **IOP** and runs at 37.5 MHz using a 32-bit bus.

The IOP communicates with the Emotion Engine using a specialised I/O interface called **System Interface** or 'SIF', both endpoints use their DMA units to transfer data between each other. The IOP also contains its own memory used as buffer.

The IOP gives access to the front ports, DVD controller, SPU2, the BIOS ROM and the PC card slot.

### Inherited compatibility

By including the original CPU, we can suspect PS1 compatibility would eventually happen somehow. Conveniently enough, the IOP happens to include the rest of the components that formed the CPU subsystem of the PS1. Moreover, the core can be under-clocked to run at PS1 speed. Unfortunately, the SPU2 has changed too much for the PS1, but for that, the Emotion Engine is 'repurposed' to emulate the old SPU.

In later revisions of this console, the IOP was replaced with a **PowerPC 401 'Deckard'** and **4 MB of SDRAM** (2 MB more than before), backwards compatibility persisted but through software instead.

### Available interfaces

This console kept the previous front ports that were included in the original Playstation, it also featured a couple of 'experimental' interfaces that looked very promising at first.

{{< float_group >}}
{{< figure_img float="true" src="photos/ps2_front.png" alt="PS2 slim back" >}}
Front of the PS2 showing common ports including Controllers and MemoryCards, plus the new USBs and i.Link
{{< /figure_img >}}
{{% inner_markdown %}}
The most popular addition: **Two USB 1.1 ports**. Widely adopted by third-party accessories, these persisted on all future revisions.

What about the 'experimental' ones? To start with, there was initially a front **i.Link port** (also known as IEEE 1394, or 'FireWire' in the Apple world). This port was used to connect two PS2 to enable local multiplayer, but it was removed after the third revision (presumably replaced by the 'Network card', more details below).
{{% /inner_markdown %}}
{{< /float_group >}}

On the back of the console we also had a slot for **PC cards**, you could buy the 'Network Adaptor card' from Sony that provided two extra ports. One for connecting an ethernet cable, and another one for plugging in a proprietary and external 'Hard Disk Drive Unit', also sold by Sony. Having a drive allowed games to store temporary data (or permanently install themselves there) for faster load times. Just a few games used this feature, though.

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Bay" active="true" src="photos/back_bay.png" alt="PS2 hard drive bay" >}}
Back of PS2 showing hard drive bay (cover removed)
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Front" src="photos/harddrive_adaptor_front.png" alt="Network adaptor" >}}
Network adaptor seen from the front.  
This particular model provided modem and ethernet ports.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Back" src="photos/harddrive_adaptor_back.png" alt="Network adaptor with disk" >}}
Network adaptor seen from the back with a hard drive fitted
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Slim" src="photos/ps2_slim_back.jpg" alt="PS2 slim back" >}}
Back of the slim model showing a fixed ethernet port
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}

In later revisions, the PCMCIA port was replaced by an **Expansion Bay** where a 3.5" Hard drive could be fitted inside the console. You had to buy first a **Network adaptor** which not only provided Modem and/or ethernet ports (depending on the model), but it also included the required connections for an ATA-66 hard disk. 'Slim' revisions completely removed this feature, but left an ethernet port permanently installed on the back. In addition to that, the new revision added a new front 'port', the **infrared sensor**.

{{% /inner_markdown %}}
{{< /float_group >}}

The ethernet transceiver supplied supports transfer rates of up to 100 Mbps (12.5 MB/s). However, the **observed rate is notoriously lower** (down to 2 MB/s in some cases). The explanation for that is relatively simple: To achieve a usable network communication, one is required to implement all the layers of the standard 'OSI Model'; and the transceiver is just one piece of the puzzle. The rest is often delegated to the IOP (thus, done in software) but due to the IOP's limited performance, this results in bottlenecks. There are multiple ways to tackle this and a previous discussion on ps2-home.com provides very insightful info, if you are curious (see the 'Sources' section).

### Interactive accessories

The new version of their controller, the **DualShock 2**, is a slightly improved version of DualShock. During the days of the original Playstation, multiple revisions of the original controller were released featuring different features (and also bringing fragmentation to the market). Now, for the benefit of developers, there was a single controller that unified all the previous properties.

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="DualShock 2" active="true" src="photos/dualshock2.png" alt="PS2 slim back" >}}
DualShock 2 controller
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Memory Card" src="photos/memorycard.png" alt="PS2 slim back" >}}
Official Memory Card (8 MB model)
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
Compared to the DualShock, the new version featured a slight redesign, it included two analogue sticks and two vibration motors for a richer input and feedback, respectively.

Next to the controller slot is the **Memory Card slot** which is compatible with PS1 and PS2 cards. The new cards embed extra circuitry for security purposes referred as **MagicGate**, which enable games to block data transfers between different memory cards.
{{% /inner_markdown %}}
{{< /float_group >}}

---

## Operating System

There's a **4 MB ROM** chip fitted on the motherboard that stores a great amount of code used to load a shell menu that the users can interact with, but it also provides system calls to simplify I/O access, games rely on this. 

Upon boot, the CPU will execute instructions in ROM which in turn will:
1. Initialise the hardware.
2. Load a **Kernel** into RAM, this will handle system calls and also provide multi-threading support (cooperative and priority-based).
3. Start the IOP processor and send it **modules**, these will enable the IOP to handle the hardware of this console. At the end, the IOP will be put in a 'waiting for command' state.
    - The use of modules allows Sony to release new hardware revisions of the PS2 without changing the IOP, lowering production costs.
3. Load 'OSDSYS', the program that displays the splash animation and the shell menu.

{{< tabs nested="true" class="centered-container" >}}
  {{< tab_figure_img name="Boot animation" active="true" src="bios/animated.jpg" alt="PS2 Splash screen boot animation" >}}
Splash animation after turning on the console
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Valid disc" src="bios/game_splash.jpg" alt="PS2 logo" >}}
PS2 logo showing after a valid PS2 game is inserted
  {{< /tab_figure_img >}}
{{< /tabs >}}

### Interactive shell

The functionality of the PS2 shell is pretty much in pace with the other 6th gen. consoles.

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Menu" active="true" src="bios/menu.jpg" alt="PS2 menu" >}}
Initial menu.  
Appears when there's no disc inserted.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Browser" src="bios/mem_list.jpg" alt="PS2 memory card browser" >}}
Memory Card browser
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Saves" src="bios/save_list.jpg" alt="PS2 Save browser" >}}
Saves browser.  
After selecting a memory card.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Editor" src="bios/save_editor.jpg" alt="PS2 Save editor" >}}
Save editor.  
After selecting a save.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Options" src="bios/options.jpg" alt="PS2 System Configuration" >}}
System Configuration
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
The shell features some practical sections which allow to perform day-to-day operations, like manipulating the saves of the memory card. It also provides some exceptional options, like changing the current video mode.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Games

It is unprecedented the level of popularity this system achieved during the noughties, at the end of its lifespan (2013, after 13 years!) the game library was filled with 1850 titles.

{{< float_group >}}
{{< figure_img float="true" src="mr_moskeeto.jpg" alt="Final Fantasy X" >}}
Whenever someone argues about the abundance of PS2 games, I remember this one  
Mr Moskeeto (2001)
{{< /figure_img >}}

{{% inner_markdown %}}
What happened here is really impressive. The PS2 doesn't have a 'programmer-friendly' architecture, (as seen from the perspective of a PC programmer) yet with such amount of games developed, I too wonder if there were more factors involved (such as 'licensing reliefs', low distribution costs, cost of development, small form factor and so on).
{{% /inner_markdown %}}

{{< /float_group >}}

### Development ecosystem

Sony provided the hardware and software to assist game development.

On the software side, there was the **Playstation 2 SDK** which included:
- The Emotion Engine toolchain: A set of C and C++ compilers, assemblers, linkers and debuggers used to control each element of the EE. The main CPU was mainly programmed using C/C++, however, performance-critical components like the vector units were programmed using assembly (microcode/macrocode) instead.
  - The pack also included an 'Emotion Engine simulator' which could roughly test out the code without sending it to the real hardware, although the simulator wasn't as accurate as the physical EE chip.
  - All of these tools worked on Linux, Solaris and Windows. The latter one ran through the Cygnus environment.
- Low-level libraries: Interfaced many system functions (using BIOS calls).
- 'Analysis' tools to profile performance usage.
- Additional tools to connect with the official development hardware.

On the hardware side, Sony provided studios with dedicated hardware to run and debug games in-house. The initial devkits were bare boards stacked together to replicate the un-released hardware of the PS2. Later kits (named **Development Tool**), had a more presentable appearance, enhanced I/O and combined workstation hardware (running RedHat 5.2) with PS2 hardware to build and deploy the game in the same case.

The combination of the Devkit, the official SDK and Codewarrior (a famous IDE) was one of the most popular setups.

### Medium

The disc drive can read both DVDs and CDs, so games could be distributed using either format, but for obvious reasons you will find most titles in DVD format.

{{< float_group >}}
{{< figure_img float="true" src="kh2_box.jpeg" alt="PS2 retail box" >}}
Typical retail game box and disc  
Kingdom Hearts II (2005)
{{< /figure_img >}}

{{% inner_markdown %}}
DVDs can hold up **4.7 GB** of data in the case of DVD-5 (the most common 'sub-format') or 8.5. GB in the case of DVD-9 (dual layer version, less common). There's actually a third format, DVD-10, which is double-sided but no games used it.

Due to the type of medium used, not only games could be played, but also movies. Now, this requires a decoder to be able to read the DVD movie format, and for that, the PS2 initially included the required bits installed in the memory card (after all, the card is just a storage medium) but later models came with the DVD software pre-installed in the BIOS ROM.
{{% /inner_markdown %}}

{{< /float_group >}}

In terms of speed, CD-ROMs are read at 24x speed (so 3.6 MB/s) and DVD-ROMs are read at 4x speed (5.28 MB/s).

### Network service

As you have seen, the networking features of this consoles weren't standardised until later revisions, which arrived four years later after the first release. Similarly, game studios were in charge of providing the necessary infrastructure if they decided to provide online services (like multiplayer). In later years, Sony deployed the **Dynamic Network Authentication System** or 'DNAS', it wasn't an online server, but an authentication system to prevent pirated games from connecting online.

### An unusual kind of game

Apart from all these games with their *fancy graphics*, Sony released a Linux distribution based on 'Kondara' (which is in turn based on Red Hat 6) available in two DVDs (first disc called 'Runtime Environment' and the second one called 'Software Packages') along with a VGA adapter, USB Keyboard and Mouse; plus some developer manuals. The pack was known as **Linux Kit** and you could run the OS by booting the first DVD and then proceed like any *old school* Linux environment. You obviously needed a Hard drive fitted in the console to install the Linux distro. Once installed, the first DVD was always required to boot this OS.

Linux Kit included compilers targeting the EE (gcc 2.95.2 with glibc 2.2.2) and assemblers targeting the vector units, along with a window system (XFree86 3.3.6) 'accelerated' in the Graphics Synthesizer. Overall, this sounds like an interesting environment. In fact, one of the research papers I read to write this article was carried out using this setup.

---

## Anti-Piracy and Homebrew

There's quite a lot to talk about here, so let's start with the DVD reader, shall we.

### Copy protection

This section was particularly concerning for game studios, since this console used a very affordable format disc to store games and was at extreme risk of being pirated.

{{< float_group >}}
{{< figure_img float="true" src="bios/rsod.jpg" alt="Error screen" >}}
This error screen could appear if the drive was faulty  
...or a pirated copy was inserted
{{< /figure_img >}}

{{% inner_markdown %}}
When the OS loads a game, it does so by sending specific commands to the DVD reader. The commands specifically used to read the content of a game behave very differently from the rest of commands (i.e. to read a DVD movie). It turns out authorised games contain an out-of-reach 'map file' in the inner section of the disc that indexes the filesystem by name, position and size. When the DVD is asked to read a game disc, it will always navigate through the disc using the map file, meaning that a pirated copy of a game, which could not include the map file, will be impossible to read. This was complemented by a region lock system that prevented imported games from working in a console from a different region.
{{% /inner_markdown %}}

{{< /float_group >}}

### Exploits discovered

Having explained the most critical part of this console, let's take a look at multiple methods discovered that could bypass the protection mechanisms.

{{< tabs >}}
{{% tab active="true" name="Modchips" %}}
As any other console of its generation (and previous ones) using disc-based systems, it was a matter of time before third-party companies reversed-engineered the DVD subsystem. The goal here was to find a usable exploit which could force the driver to navigate through the file system without needing an out-of-reach map file.

This eventually happened in the form of **modchips**, which lifted the region locking restrictions as well.
{{% /tab %}}

{{% tab name="Cheats" %}}
Along with the modchips, which required soldering skills to install, unauthorised but 'genuine' discs appeared in the market. These enabled to defeat the region protection and use in-game cheats by patching the OS. Moreover, 'cheat discs' had the advantage of not requiring to modify the console. I guess the best example to mention is *CodeBreaker*.
{{% /tab %}}

{{% tab name="Disc swapping" %}}
In the middle of the latest advancements, yet another trick appeared. This time, exploiting the reader's handling of faulty sectors. **Swap Magic** looks like another 'genuine' disc, but its 'game' tells the DVD to read a non-existent executable found on a deliberate faulty sector, provoking the driver to halt. This window of opportunity allowed users to swap the disc for a non-genuine one. Then Swap Magic, still loaded in memory, bootstrapped the main executable of the new disc, loading a real game at the end. All of this is carried out while the driver is still thinking a genuine disc is inserted.

This doesn't necessarily require to alter the console. However, depending on the model, the external case of the PS2 will have to be tampered to block the eject sensors of the drive. In some cases, placing cotton in certain places will do the trick.
{{% /tab %}}

{{% tab name="PS1 overflow" %}}
The PS2 stores a database file called `TITLE.DB` in MemoryCard which contains information used to optimise the emulation of PS1 games. When a PS1 game is inserted, the OS fetches the database file and loads the whole file in memory at a fixed address (*strike one*). The information parser is implemented using **`strncpy()`**, a function in C that copies strings (chain of characters) from one place to another.

For people familiar with C, you probably guessed where I'm going. The thing is that `strncpy()` doesn't know how long is a string, so unless it's terminated (by placing `\0` at the end of the chain) the copy goes on 'forever' (with unpredictable results!). Luckily, this function contains an optional parameter that specifies the maximum number of bytes to be copied, protecting the copy from buffer overflows. As ludicrous as it may seem, **Sony didn't use this parameter**, even though each database entry has a fixed size of 256 bytes (*strike two*).

Upon closer inspection in RAM, TITLE.DB happens to be copied **next to a saved register**, `$ra`, which states the address to return after the current function being executed finishes (*strike three*) leading up to **The independence exploit**: Craft a Title.db with a large string, embed an executable in it and design that string so `$ra` will be overridden to point to the executable. If you manage to upload that file to your MemoryCard (through another exploit or a PC USB adapter) you got yourself a simple Homebrew launcher.

After the slim revision was released, the exploit got patched (*I wonder how*). Curiously enough, it wasn't the last [blunder]({{< ref "wii#the-fall-of-encryption" >}}) that exposed clumsy code.
{{% /tab %}}

{{< /tabs >}}

### A semi-permanent software unlock

Some time ago, it was discovered that the BIOS of this console could be upgraded using the Memory Card, this function was never used in practice, but neither removed (at least during most of the console's lifespan). With this, hackers realised that if they find a way to install particular software into the MemoryCard, then the BIOS would always load it at boot. This discovery led to **Free MCBoot**, a program presented as 'upgrade data' which replaces the original shell with one that can execute **Homebrew**.

Bear in mind these changes are not permanent, a Memory Card with 'Free MCBoot' installed must be inserted prior the console's startup. Additionally, this software needs to be installed somehow, so another exploit (e.g. disc swapping) is required to bootstrap the installer.

### More disc tricks

The same year after the release of Free MCBoot, another trick was discovered: Disguising games as DVD movies, effectively allowing unauthorised game copies to be read without requiring a modchip.

This only required to patch the game image by adding dummy metadata and partitions only used by DVD movies. Then, when the burned copy is inserted on the console, the drive won't reject it, but it won't execute the game either. However, with the help of a Homebrew program called **ESR**, the game could be kickstarted.

---

## That's all folks

Congratulations and thank you for reaching the end of the article! To be honest, there was so much to talk about that I wondered if readers would eventually get tired of Playstation-related stuff after finishing this.

Anyway, in all seriousness, I do hope you discovered new things after reading this article and if you have any comments, don't hesitate to {{< email "drop me a mail" >}}.

Until next time!  
Rodrigo