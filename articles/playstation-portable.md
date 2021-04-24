---
name: PlayStation Portable
subtitle: "'Portable' does not imply 'Limited'"
date: 2021-04-21
releaseDate: 2004-12-12
cover: "psp"
javascript: ['audioswitcher', 'threejs']
generation: 7
top_tabs:
  Models:
    Original:
        order: 0
        caption: "The original PSP (aka model '1000')
        <br>Released on 12/12/2004 in Japan, 24/03/2005 in America and 01/09/2005 in Europe"
        active: true
    Slim:
        order: 1
        caption: "The updated PSP (aka model '2000')
        <br>Released on 05/09/2007 in Europe, 06/09/2007 in America and 20/09/2007 in Japan"
    Slim & Lite:
        order: 2
        caption: "The even-updated PSP (aka model '3000' or 'Brite', silver edition)
        <br>Released on 14/10/2008 in America, 16/10/2008 in Japan and 17/10/2008 in Europe"
    Go:
        order: 3
        caption: "The redesigned PSP (aka model 'N1000')
        <br>Released on 01/10/2009 in America and Europe; and 01/11/2009 in Japan"
  Motherboard:
    caption: "Such a tiny board... but you can do so much with it
    <br>Those zigzags you see between the CPU and memory attempt to correct the length of each bus so all of the signals arrive at the same time. This tells you how fast technology has become!
    <br>After the 'Slim' model, Tachyon and 'NAND + SDRAM' became one"
  Diagram:
    caption: "After the 'Slim' model, NAND and SDRAM size was doubled (64 MB each)"

# SEO
title: PlayStation Portable Architecture

# Historical
aliases: [/writings/consoles/psp-private/]

---

## A quick introduction

After just one month since the arrival of the [Nintendo DS]({{< ref "nintendo-ds" >}}), Sony proved that 'horizontal innovation' is not the only means to succeed in the portable console market.

This article is dedicated to anyone that wants to understand, straight to the point, how the PSP operates. It's not a short write-up, but I hope that at the end of the reading you will be able to grasp the building blocks of this console (its design rationale, choice of CPUs, GPU pipeline, security system and so forth).

P.S. If you ever feel fed up with information, don't forget to take a look at the diagrams (the {{< img_link label="main one" src="diagram.png" >}} plus the others I made for each section).

---

## 'Main' CPU

Similarly to Nintendo, Sony built an *extremely packed* System-on-Chip that houses most of the component we are going to discuss throughout this article. This includes the **'main' CPU** in charge of executing games/programs (unlike other CPUs we'll talk about in due time). The SoC is called **Tachyon**, a name chosen by Sony themselves (after watching some *Star Trek*?).

Moving on, the main CPU is also an in-house design by Sony. In the official documents, they refer to it as a 'MIPS core' which borrows some IP from the MIPS R4000 (you may remember Nintendo doing something [alike]({{< ref "nintendo-64#cpu" >}})). This new design is called **Allegrex** and runs at a variable speed, from **33 MHz to 333 MHz**.

Allegrex is a complete 32-bit core offering:
- A **custom MIPS ISA**: Mixes instruction from different MIPS revisions. To be precise, it implements all MIPS I instructions for general purpose while adding branch instructions from MIPS II. Furthermore, Sony added exclusive ones for **arithmetic operations** (multiplication, subtraction, min/max, bit-shifts) and **interrupt control**. These are called 'Allegrex Extended instructions'. All of this, while still retaining the 'RISC' badge.
- A **32-bit Address Bus**: This means that up to 4 GB of memory can be seen by this CPU.
- **32 general-purpose registers**: All of them store 32-bit numbers, two of them (the zero register and link register) are reserved for special uses. This should come as no surprise.
- **7-stage pipeline**: Same as the [Gamecube]({{< ref "gamecube#cpu" >}}). [Here]({{< ref "game-boy-advance#cpu" >}}) is a previous explanation of CPU pipelining if you don't remember.
- A **Memory Protection Unit** or 'MPU' (not to be confused with an 'MMU'): This is a dedicated unit that maps the physical hardware onto the CPU's memory space with some special quirks in-between. We'll see more about it in a bit.
- **32 KB of L1 Cache**: Distributed between 16 KB for instructions and 16 KB for data.
- A **Cache Write-back Buffer**: The CPU can write over cache believing it has updated physical memory as well. Then, the cache takes care of updating memory when the buffer is full.
  - This design speeds up memory stores but doesn't work right away with multi-processor systems, which is the case of the PSP. So, developers will have to take care of evicting the buffer when other components require those new values in memory.

In conclusion: Allegrex is incredibly fast. However, we still don't know what can you do with it. After all, you can imagine this CPU as the conductor of an orchestra, and we haven't checked out the performers... yet.

#### Coprocessors

As with any MIPS R4000, Allegrex has three coprocessor slots. Sony added two:
- A **Floating Point Unit** as 'CP1': Accelerates arithmetic operations with 32-bit decimal numbers. It has an independent 8-stage pipeline which adds more parallelism to the main CPU.
- A **Vector Floating Point Unit** as 'CP2': A coprocessor designed for vector operations, similar to a traditional SIMD processor. It has 128 registers that store 32-bit floats (using the IEEE 754 standard) and a special instruction set with variable pipeline stages.
  - Its usefulness comes from its 128-bit data bus connected to the rest of the system, offloading the main CPU's transit.
  - If you read the [PS2 article]({{< ref "playstation-2" >}}), the PSP's VFPU is equivalent to the PS2's [VPU0]({{< ref "playstation-2#tab-1-1-vector-processing-unit-0" >}}) operating in **permanent Macromode!**

#### Focused memory management

Let me talk for a moment about the memory unit included in this system. Its *modus operandi* can be seen as a bit primitive at first, but we'll see why it's optimal for the needs of this console.

{{< centered_container >}}
  {{< linked_img src="cpu/mmu.png" alt="Memory Diagram" >}}
  <figcaption class="caption">Memory addressing with the MPU</figcaption>
{{< /centered_container >}}

A traditional Memory Management Unit or 'MMU' takes care of giving the CPU access to the components surrounding it, this implies that all the address lines of the CPU will be connected to the MMU; and the latter is connected to the rest of the system.

This is particularly advantageous to provide features like 'virtual memory' and 'memory protection'. Well, to achieve virtual memory, the MMU contains a component called Translation look-aside buffer or 'TLB' to prevent performance degradation. Now, **Allegrex's MMU lacks a TLB**, so it focuses on the second example (memory protection). This is why 'MPU' is more appropriate name for Allegrex's MMU. An MPU is a cutdown version of an MMU without virtual memory. In any case, **memory protection gives the system the power to decide which memory locations a program can access**.

Thanks to this, Allegrex won't have to deal with user-land programs (i.e. games) accessing restricted locations (i.e. encryption keys). To accomplish this, memory addresses are grouped into five segments with different privilege levels set. Furthermore, our MPU contains three modes of operation: **User mode**, **Supervisor mode** and **Kernel mode**.

If a normal process (operating in 'user mode') wants to access a memory address found in a privileged location, the MPU will ask the operating system (through the use of 'exceptions') if the process should access it and the execution will continue from there. 

All in all, this allows Sony, the developer of the operating system, to implement a security system enforced by hardware.

#### Memory available

So far we've analysed the main CPU and its accelerators. Now let's see the physical memory available in this system.

{{< centered_container >}}
  {{< linked_img src="cpu/memory.png" alt="Memory diagram" >}}
  <figcaption class="caption">Memory layout from the main CPU perspective</figcaption>
{{< /centered_container >}}

The PSP comes with two memory blocks accessible from the CPU:
- **16 KB of SRAM**: This is what we historically called **Scratchpad** on [previous articles]({{< ref "playstation#cpu" >}}). It's small but very fast RAM. It's up to developers to make proper use of it, although unofficial documents already called it 'useless'.
- **32 MB of DDR SDRAM**: It's huge and many other components will make use of it, but since it's not that close to the CPU, its access rate is slower. We'll refer to it as 'Main memory' across the article.
  - The 'DDR' initials mean 'Double Data Rate', which denotes an evolution from the traditional 'SDRAM' featuring a faster transfer protocol. The [original Xbox]({{< ref "xbox#memory-layout" >}}) used the same type.

#### Bus design

You'll soon find that the PSP accommodates lots of components with unique functionality. So, to organise them without repeating [previous mistakes]({{< ref "nintendo-64#tab-4-1-pipeline-stalls" >}}), engineers interconnected them using dedicated buses. Thus, only small groups of components, with similar applications, will share the same bus. At the same time, those buses talk to each other using dedicated arbiters (in the form of 'memory controllers' or 'DMA').

All of the buses found in Tachyon implement a well-known design called **Advanced High-performance Bus** or 'AHB'. A solution by Arm that also tackles this issue with their chips and SoCs. If you are curious, AHB is also used in the [Wii]({{< ref "wii#io" >}}).

{{< centered_container >}}
  {{< linked_img src="cpu/buses.png" alt="Main buses diagram" >}}
  <figcaption class="caption">Basic layout of the three main buses in this system</figcaption>
{{< /centered_container >}}

That being said, the following buses are constructed:
- The **System Bus**: Connects the CPU, Scratchpad and GPU. It's 128-bit wide.
- The **Media Engine Bus**: Connects another group of components (explained in the next section). It has the same properties as the System Bus.
- The **Peripheral Bus**: Connects I/O, storage-related components. It's 32-bit wide.

All three buses connect to the DDR Controller, which is where main RAM is found.

#### Tackling traffic congestion

Inside each bus, there will be multiple components working independently. Consequently, they will store processed data in a shared space (such as main RAM). Now, we don't want the CPU to intervene whenever a module needs to read or write from memory. Traditionally, a DMA unit was placed in the bus to provide this facility, but a single DMA can only do so much and Sony placed a significant number of components which eventually, will lead to bottlenecks.

So, the solution is very simple: **Bus Mastering**. In a nutshell, each component will get its own DMA controller. This gives them the ability to become the 'bus master' and take control of the bus to access whatever location they want. To avoid contention (multiple 'bus masters' at the same time), the neighbouring components will acknowledge this event and wait until the operation completes.

---

## 'Multimedia' CPU

Up to this point, we've discussed the main CPU and the buses inside Tachyon, but there's still many features found in this SoC. In particular, there's another block of modules designed for a particular application: **Multimedia**. The group specialised in **audio and image processing**, including the combination of both (video processing), and it operates without consuming bandwidth from the main CPU group (the 'System group').

{{< centered_container >}}
  {{< linked_img src="games/daxter.jpg" alt="Video playing in Daxter" >}}
  <figcaption class="caption">Daxter (2006)
  <br>In-game cinematic rendered with this block</figcaption>
{{< /centered_container >}}

That being said, the new block is called **Media Engine Group** and it's composed of the following components:
- A second **Allegrex CPU**: Serving as the 'controller' of the Media Engine group. This is the same CPU found in the System Group without the vector coprocessor. The controller is responsible for receiving orders from the main CPU and managing the rest of the multimedia hardware that will work concurrently.
- **2 MB of eDRAM**: Multimedia encoding and/or decoding consumes a considerable amount of bandwidth, so to avoid stalling the other components, the media engine contains dedicated memory to process the data 'in-house' and publish the results once it's necessary to do so.
  - 'eDRAM' is the same as DRAM but manufactured inside another chip.
- An **MPEG-AVC** decoder: A hardware decoder that does only one thing and fast. That is, swallow **H.264 video stream** and spit out data that the audio and graphics endpoint understand.
- A **Virtual Mobile Engine** or 'VME': This is *some sort* of programmable DSP. The controller programs it and then starts feeding in the data. Afterwards and similarly to the AVC, the VME stores the processed chunk back in local memory.
  - This is a very obscure component, as only Sony seems to know the ins and out of it (including how to program it). To this day, it's still a mystery.
  - From Sony's leaked slides published at pcmag.com, the VME seems to have local memory and a DMA controller. It's also called 'Dynamic Reconfigurable Engine'.

Needless to say, the media engine is not directly operable from the developer's side (Sony hides all of it through a thick API). The official SDK includes libraries like 'libmpeg' or 'libmp3' which already implement complete applications optimised for this engine.

---

## Graphics

Let's go back to the 'System group' now since the engine in charge of generating pixels is found within this block. It's organised as a 'group within a group' but you don't have to worry about that now. The group we will be talking about now is called **Graphics Engine** or 'GE'.

{{< centered_container >}}
  {{< tabs nested="true" >}}
    {{< tab name="KH" active="true" >}}
      {{< linked_img src="games/kh.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">Kingdom Hearts Birth by Sleep (2010)</figcaption>
    {{< /tab >}}
    {{< tab name="Spider-Man" >}}
      {{< linked_img src="games/spider.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">Spider-Man 3 (2007)</figcaption>
    {{< /tab >}}
    {{< tab name="GTA" >}}
      {{< linked_img src="games/gta.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">Grand Theft Auto: Vice City Stories (2006)</figcaption>
    {{< /tab >}}
    {{< tab name="Sims 2" >}}
      {{< linked_img src="games/sims2.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">The Sims 2 (2005)</figcaption>
    {{< /tab >}}
    {{< tab name="OutRun" >}}
      {{< linked_img src="games/outrun.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">OutRun 2006: Coast 2 Coast (2006)</figcaption>
    {{< /tab >}}
     {{< tab name="Loco" >}}
      {{< linked_img src="games/loco.jpg" alt="Game Screenshot" >}}
      <figcaption class="caption">LocoRoco (2006)</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /centered_container >}}

The GE draws 3D graphics (polygons) with many features applied (texture mapping, lighting and so much more) which you'll see in a bit.

#### Display

Before we continue, let's go over the physical aspects of this console, which is where the user can appreciate everything that will be discussed here.

So, the PSP carries a **4.3" TFT LCD screen**. It has a resolution of **480x272 pixels** (for reference, it's ~2.6 times the pixels of one screen of the Nintendo DS) and can display up to 16,777,216 colours. Thus, it's got a 24-bit colour depth (the so-called 'True Color' scale).

The aspect ratio is *almost* 16:9, a format that during that time was increasingly becoming a standard on home tellies as well. Notice that a wider range of view is also an opportunity for game designers to improve game experience (especially in the first-person shooting genre).

#### Architecture

The GE subsystem has lots of interesting quirks to mention, so this will be a very complex section. But fear not! I'll try to go step-by-step to avoid any confusions.

First things first, the Graphics Engine is made of three types of components:
- The **Graphics Core**: Where the actual graphics functionality happens.
- The **eDRAM Controller**: Mediates access between eDRAM memory and the core.
- The **Bus Matrix**: Works as a 'bus arbiter' between the System Bus and the internals of the Graphics Engine (remember the GE is found within the System Group).
  - In reality, this component is a mesh of wires with some logic, but for the sake of explaining, it's simpler to mask with a black box.

The reason for this design is that both main CPU and the Graphics Core can access those 2 MB of eDRAM. So, to prevent congestions, the traffic inside the Graphics Engine goes through another AHB bus called **Local Bus** ('local' from the perspective of the GE). This allows the Graphics Core to perform its functions without depending on the System Bus to move data around (and consequently stalling the rest of the system).

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="ge_arch.png" alt="Architecture of the GE" >}}
  <figcaption class="caption">Architecture of the Graphics Engine</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Local Bus is as wide as the System Bus (128-bit), but if that wasn't enough, the Graphics core has a direct line to eDRAM using a 512-bit bus (made of two unidirectional 256-bit buses), you'll see why it will be needed in the following section.

What about how the CPU and GE communicate with each other? As I said before, both CPU and Graphics Core can read from eDRAM. Additionally, the Graphics Core can access the System Bus to fetch data from any other component (including main RAM). So, all of that just doesn't happen magically.
{{% /inner_markdown %}}
{{< /float_group >}}

In a nutshell, there are two 'Bus Matrix' blocks that re-wire the connection between the Local bus and the System Bus. Whenever there's a component that wants to access an 'alien' bus, the Bus Matrices configure the communication so that one unit becomes a bus master of the two buses and no other overlaps, until the designated unit finishes transferring memory.

In Sony's docs, they refer to it as the Graphics Core or CPU becoming a 'bus master' while the alien bus is set to 'slave'. However, they didn't spend a lot of words trying to explain it and I'm not sure which protocol/standard they are trying to replicate. Based on what's documented, I think it may be somewhat similar to I²C, a protocol used for serial communications (particularly useful with embedded systems) which also performs bus mastering.

#### Organising the content

Now that we've seen what components we got and how they interact with each other, let's find out what information related to graphics we can place in memory.

There are three memory locations from which the GE will end up pulling or filling:
- **2 MB eDRAM**: The aforementioned eDRAM. It's used to store the frame-buffer, [z-buffer]({{< ref "nintendo-64#modern-visible-surface-determination" >}}) and texture buffer. Its contents are directly written by the GE. This memory space is also called 'Local Memory'.
  - The CPU can access this memory if needed, although its speed is not ideal.
- **32 MB DDR SDRAM**: This is the working area of the CPU to build the display lists, vertex data, texture data and CLUTs (Colour lookup tables). In the context of graphics, this bloc is called 'Host Memory'.
- **16 KB SRAM**: The scratchpad memory is also accessible by both CPU and GE.

#### Functionality

Like its [home sibling]({{< ref "playstation-2#graphics" >}}), the Graphics System focuses on **rasterization**. Although, VRAM is half the size and the bus is not as fast. To compensate, the GE features a vector processor!

{{< centered_container >}}
  {{< linked_img src="pipeline.png" alt="Pipeline design of the Graphics Engine" >}}
  <figcaption class="caption">Pipeline design of the Graphics Engine
  <br>I've skipped the interfaces since it's becoming too convoluted</figcaption>
{{< /centered_container >}}

The graphics pipeline is very similar to the PS2, with the addition of the vector processing stage. The Graphics core is divided into two areas: **The Surface Engine** which deals with vector processing and **Rendering Engine** which, as its name indicates, performs rasterisation and effects.

The pipeline is not explicitly documented by Sony, just its features. Thus, I've deduced a model myself. This is only done for the sake of analysis in these series (programmers don't depend on this to build their game).

On another note, I've decided to make it a bit more technical than previously. I thought this would be a good opportunity to learn new concepts and have a wider vision of computing graphics. Having said that, I strongly recommend reading about the [PS1]({{< ref "playstation#graphics" >}}) and [PS2]({{< ref "playstation-2#graphics" >}}) graphics system before continuing.

{{< tabs >}}
{{< tab name="Commands" active="true" >}}
{{< float_block >}}
  {{< linked_img src="gpu/commands.png" alt="Command stage diagram" >}}
  <figcaption class="caption">Command stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Graphics engine is controlled using traditional 'Display lists' stored in main memory. The CPU builds them and the GPU reads them (using DMA). Display Lists basically tell the GPU what, how and where to draw. In the case of the PSP, a display list is not limited to rendering tasks, they can also include vector transformations.

The strategy implemented to process lists is critical to ensure both CPU and GPU work concurrently and don't stall each other. Remember, we want the GPU to accelerate operations, not to be a burden. Hence, the CPU/GPU support **deferred rendering**, a technique that allows the CPU to build the next set of display lists while the GPU is processing a previous one. The GE is configured by specifying where in memory the display list starts (base address) and where it stops (stall address). As a result, there are two ways of allocating a list:
- **Double buffered**: There are two separate display lists areas, one is being transferred to the GPU, the other is being filled by the CPU. When both processors finish, they swap places.
- **Store controlled**: The CPU fills the same list the DMA is transferring to the CPU, but the CPU is some entries ahead to avoid overlapping. This uses less memory but it's susceptible to stalls if the GPU catches up.

Furthermore, Sony stated that the GPU's DMA unit is not just a 'dumb memory copier' - it can also understand the data transferred. Thus, display lists can include **commands such as 'Jump' and 'Return'** to tell the DMA to branch out and get data from somewhere else. This saves the CPU from embedding large resources (models, textures, etc) in the display lists (effectively duplicating data in memory) and reduces bandwidth consumption. This was inherited from the PS2.

Finally, the DMA can also interpret **Bounding Box data**: Combined with information stored in the Graphics Engine, the DMA will skip drawing commands that aren't within the display area. It's also possible to declare bounding boxes inside other bounding boxes, but let's leave that topic for another day!
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Vector Processing" >}}
{{< float_block >}}
  {{< linked_img src="gpu/vector.png" alt="Vector Processing stage diagram" >}}
  <figcaption class="caption">Vector Processing stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The GE debuts the ability to perform operations over vectors, which helps to offload a lot of work from the CPU. Sony built this unit to accelerate common tasks previously carried out by the PS2's VPUs using microcode. While the GE is not as flexible as a VPU (the GE is a fixed-function unit), it does simplify a lot of coding (considering microcode had a considerable weight on the learning curve). The GE's vector processor is called **Surface Engine**.

That being said, the **Surface Engine undertakes three types of tasks**. The first one operates **parametric surfaces**. Remember the ['Infinite Worlds']({{< ref "playstation-2#infinite-worlds" >}}) section? Well, Sony explained that, while the PS2 was indeed capable of this, in the end just a few games bothered. It's possible that polygon performance, along with the difficulty in design and implementation, were the main factors.

To tackle this, the Surface Engine implements two parametric curves:
- **Bézier**: A relatively fast function but its parameters change the global shape. Additionally,  it suffers from 'continuity issues' which manifests with cracks on surfaces (discouraging its use with animations). It's often used for landscape generation.
- **B-spline**: Requires more computations but fixes continuity issues and provides localised control. Character modelling and animation are potential candidates.

Both support **levels of detail**, which means that developers can set an arbitrary value to alter the level of subdivision (impacting in the resulting quality of the model).

The second task is called **vertex blending**, a technique used for **animations**. The Surface engine provides two types:
- **Skinning** (a.k.a skeleton animation): Movement relies on a structure of 'joints' attached to the character model. It's often applied for normal animation (walking, jumping, etc.).
- **Morphing**: Uses multiple instances of a model and averages them to get a 'transition effect' (using linear interpolation). It's normally used for artificial animation (i.e. facial movement) and special effects. It does come with larger memory consumption and a bigger learning curve.

It's important to denote that the CPU may still need to compute animations to process game logic (i.e. collision detection) so the CPU can't offload all the work.

Finally, the Surface Engine provides **scissoring** (discarding vertices outside the viewport/rectangular area) as well.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Rasterization" >}}
{{< float_block >}}
  {{< linked_img src="gpu/rasterizing.png" alt="Rasterizing stage diagram" >}}
  <figcaption class="caption">Rasterizing stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This is the starting point of the **Rendering Engine** (skipping the command processor). In here, vector data is transformed into pixels, which is pretty much in pace with any other GPU in the market.

The engine draws many types of primitives, including points, lines, line strips, triangles, triangle strips, triangle fans and, finally, sprites (made of 2D rectangles). It also contains a unit called 'Digital Differential Analyser' that will be used for interpolating values during rasterisation and texture mapping.

Developers can supply a projection matrix to apply **perspective transformation**, this sends their 3D world to a 2D space (so you can see it on the screen) using the camera as a reference.

Sony didn't provide much information about how its rasteriser works in particular, so it's not well-known how many pixels are processed per cycle, for instance. Modern features, like sub-pixel precision, are assumed to be implemented (otherwise, users would've been able to [spot its absence]({{< ref "playstation#tab-2-1-wobbling-textures" >}}) right away).
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Textures" >}}
{{< float_block >}}
  {{< linked_img src="gpu/textures.png" alt="Texture mapping stage diagram" >}}
  <figcaption class="caption">Texture mapping stage</figcaption>
{{< /float_block >}}{{% inner_markdown %}}

This may be the topic of most interest for some. Polygons (now mere pixels) can be painted with textures. At this stage, texture maps are fetched from memory and processed with many functions. This process is called **Texture Mapping**.

The Rendering Engine has three **mapping modes** or, in other words, three ways of processing texture maps:
- **UV Mapping**: Every coordinate of the model is mapped to a coordinate of the texture.
- **Projective Mapping**: In a nutshell, it simulates a physical projector shining the texture map over a surface.
- **Shade Mapping**: Casts up to four lights (either ambient, directional, point or spot) on the model. This is useful when blending the result with another map (you'll see more in the next section).
  - If programmers supply a texture map instead of a solid colour, you get a 'fish eye' effect (useful for reflections, a.k.a. **environmental mapping**).
  - This mode will also permit to obtain [toon shading]({{< ref "gamecube#creativity" >}}) as well.

Textures may use **Colour Lookup Tables** or 'CLUTs'. Furthermore, this engine applies **perspective correction** and **bilinear or trilinear filtering** for interpolation.

On another topic, there's **8 KB of texture cache** found in GE to save bandwidth, it uses the 'Least Recently Used' method for space management.

Finally, while this pipeline is not programmable, developers can send extra colours to be blended with textures. There's also colour doubling (doubles the RGB value of colours), colour addition (combines a primary colour with a secondary colour) and **Fogging** (haze over far away polygons).

{{% /inner_markdown %}}{{< /tab >}}
{{< tab name="Pixel Operations" >}}
{{< float_block >}}
  {{< linked_img src="gpu/operation.png" alt="Pixel Operation stage diagram" >}}
  <figcaption class="caption">Pixel Operation stage</figcaption>
{{< /float_block >}}{{% inner_markdown %}}

We're reaching the end of the pipeline. The initial geometry has been transformed into pixels and these are now rich-coloured, so it's time to decide what to do with them.

Some pixels may correspond to geometry that it's not needed for this frame (i.e. occluded, masked, etc). To filter that out, the GE can perform the following tests:
- **Scissoring**: Discard polygons away from an arbitrary area.
- **Depth Range**: Discards too far or too near polygons (developers set the reference values).
- **Colour**: Discard pixels equal or not equal to an RGB value.
- **Alpha**: Compares the pixel's alpha value to a reference value.
- **Stencil**: Similar nature to the previous one but relies on the stencil value.
- **Depth test**: Late [Z-buffering]({{< ref "nintendo-64#modern-visible-surface-determination" >}}).

Afterwards, pixels will also travel through these optional blocks for further effects:

- **Alpha blending**: Combines non-opaque pixels with the ones in the frame-buffer using different arithmetic operators.
- **Dithering**: Softens colour changes.
- **Colour clamping**: Aligns RGB values to fit in the frame-buffer format.
- **Logical operation**: Decides how to merge the new frame with the existing frame-buffer using logical operators (`AND`, `OR` and many more).
- **Masking**: As the name indicates, it masks the z-buffer or frame-buffer. 

Complex functions like **antialiasing** are the result of a strategic combination of the above. Finally, the outputted pixel is written to the frame-buffer, which in turn is broadcasted for display.
{{% /inner_markdown %}}{{< /tab >}}

{{< /tabs >}}

As evidenced, the PSP inherits various features from the PS2. The difference, however, is that functionality is now hardwired in the silicon, as opposed to offering many general-purpose programmable units (which require manual work to set them up). I presume this was done for two reasons: To use fewer transistors (so it fits in Tachyon and the board remains 'portable') and to facilitate porting PS2 codebase to the new console.

#### Interactive Models

To show how this system impacted model design and to help compare it to the [PS2]({{< ref "playstation-2#better-models" >}}) and/or [Nintendo DS]({{< ref "nintendo-ds#interactive-models" >}}), here are two examples of models designed for the PSP. Don't forget the viewer is interactive!

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="snake_psp" >}}
    <figcaption class="caption">Metal Gear Solid: Portable Ops Plus (2006)
    <br>1,383 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="daxter" >}}
    <figcaption class="caption">Daxter (2006)
    <br>1,374 triangles</figcaption>
  </div>
{{< /side_by_side >}}

#### Video Out

The first model of this console (1000) has a proprietary port called **Remote Port** at the bottom-left corner (next to the audio jack). 

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="io/bottom.jpg" alt="Bottom-right corner of the PSP" >}}
  <figcaption class="caption">Bottom-right corner of the PSP (3000 model) showing the proprietary 'Video Out'</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
It uses the RS232 protocol, an old standard for transferring data in serial. Though it wasn't publicly available to developers (let alone documented), a couple of audio headsets with control buttons appeared on the market. They apparently use the serial port to send commands (play, pause, etc) to the console.
{{% /inner_markdown %}}

{{< /float_group >}}

In later models (2000 and 3000), the remote port was expanded with an extra YCbCr pinout. Sony shipped three **video cables** (Component, S-Video and Composite) that rely on this interface to enable users to see the contents of the PSP screen on the TV.

The video cable will send a frame with a resolution of **720x480 pixels (in NTSC) or 720x576 pixels (in PAL)**, in progressive or interlaced mode (the latter option is only available on the 3000 model). Considering the PSP's resolution is 480x272 pixels, games will show **black bars** to correct the aspect ratio. Although, the bars won't show when console is running the 'XMB' visual shell, as it natively supports adjusting its resolution for the video cable.

---

## Audio

In this section we tend to encounter a [PSG]({{< ref "master-system#audio" >}}), [sequencer]({{< ref "nintendo-64#audio" >}}) or a *large* [mixer]({{< ref "nintendo-ds#audio" >}}). This is because, at the end of the day, dedicated audio hardware is meant to offload audio operations from the CPU. Although, if this hardware tuns out to be 'weak', it degrades the sound capabilities of the console.

Now, what we consider 'sound hardware' in the PSP is very barebones: Only **two PCM channels and a stereo mixer**. The maximum sampling rate and resolution are **48 KHz** and **16-bit**, respectively.

I don't see any of the accelerators I mentioned in the first paragraph. So, does this imply that the sound will be as limited as [other]({{< ref "nintendo-64#secrets-and-limitations-1" >}}) [cases]({{< ref "game-boy-advance#audio" >}}) showed? No! Because the hardware of the PSP enables to compensate it with more software.

You see, while there isn't a large amount of sound-related circuitry, there's a lot of general-purpose components that Sony supplied. They can help with audio decoding, streaming and mixing. I'm talking about the **Media Engine**.

That block is very good at signal processing, but it needs to be programmed. So, Sony wrote the following software running on the Media Engine to complement the two PCM channels (we'll call them 'endpoint'):
- **Sound Driver**: Talks to the endpoint and performs memory transfers using DMA.
- **Software Audio Synthesizer**: As the name indicates, it generates the signals that will be sent to the endpoint.
  - This synthesizer supports 32 channels, using either **PCM**, **ADPCM** or **ATRAC3** formats.
  - Provides **digital reverb**, **pitch control** and **ADSR envelope**.

Games don't have access to these modules directly. Instead, they call many libraries included in the official SDK. Some libs are designed to operate raw signals while others are optimised for particular applications, examples include:
- **MP3 and AAC decoding**.
- **MIDI sequencing**.
- **VoIP audio decoding** (G729 and u-law).
  - The operating system of the PSP bundled a variant of the 'Skype' app, if you wonder what uses VoIP could have here.

#### Audio comparison

Let's put all this into practice by taking a look at how games reproduced their sound. I wrote this special player that allows you to swap between consoles (to enable side-by-side comparisons):

{{< side_by_side >}}
  <div class="toleft audio-switcher">
    {{< audio_switcher src1="overworld_nds"  label1="Nintendo DS"
                       src2="overworld_psp"  label2="PSP" >}}
    <figcaption class="caption"><strong>Nintendo DS:</strong> Final Fantasy IV (2007)
    <br><strong>PSP:</strong> Final Fantasy IV - The Complete Collection (2011)</figcaption>
  </div>

  <div class="toright audio-switcher">
    {{< audio_switcher src1="encounter_ps2"  label1="PlayStation 2"
                       src2="encounter_psp"  label2="PSP" >}}
    <figcaption class="caption"><strong>PlayStation 2:</strong> Kingdom Hearts 2 (2005)
    <br><strong>PSP:</strong> Kingdom Hearts Birth by Sleep (2010)</figcaption>
  </div>
{{< /side_by_side >}}

In the first example, the Nintendo DS game sequences its music on-the-fly while the PSP one decodes ATRAC3. As you can see, having no dedicated sound hardware per se doesn't imply ending up with the worst quality. In fact, one could argue the opposite, as the Media Engine provided more features than most sound chips of previous portable consoles ever did.

The second example is much trickier to quantify. I placed a PS2 tune for comparison, but I had to boost the PS2 one a bit (I presume PSP soundtracks are louder to compensate for the smaller speaker and the proximity to the user). Also, bear in mind that the arrangements are slightly different between platforms. It's possibly just a creative change since the PSP game was released five years after the PS2 one. Overall, the PSP doesn't show deterioration (considering the [PS2's SPU2]({{< ref "playstation-2#audio" >}}) is very powerful and flexible).

---

## I/O

The PSP has plenty of connections and sensors. However, this section will be a good opportunity to introduce some chipsets that haven't been mention yet, and they play a big part in handling the circuitry that detects the player's input.

#### Internal interfaces

Most of the I/O available is linked in the **Peripheral Bus**. It's only 32-bit wide which is enough to transfer simple information at a normal pace. It has access to main RAM as well.

The D-Pad, joystick and buttons are handled by a unique chip referred to as **'System Control' or SysCon**, this is a common codename in Sony's hardware and it deals with interfacing many internal components. In this case, it's just the physical buttons.

#### External interfaces

The console features a good amount of connections:
- **802.11b WiFi**: Either connects to a hub or operates in 'Ad Hoc' mode to talk to nearby PSPs. This is similar to what the [Nintendo DS]({{< ref "nintendo-ds#freedom-of-interaction" >}}) offered.
- **USB 2.0**: For accessories or to plug the console into a computer. In the case of the latter, the PSP is recognised as a mass storage device, providing access to the Memory Stick.
- **IrDA**: Stands for 'Infrared Data Association', similar to what a TV remote uses to change the channel. Before the release of the PSP, the infrared protocol was popular for transferring multimedia between a camera/phone and the computer. As for the PSP, however, the IrDA port went bluntly ignored.
  - As it tends to happen, Sony removed it with the second revisions of the PSP (2000).

#### Commercial accessories

The use of a standard port such as USB enabled companies to design accessories for this console.

{{< centered_container >}}
  {{< linked_img src="io/top.jpg" alt="Top view" >}}
  <figcaption class="caption">Top side of the PSP (model 3000)
  <br>Notice the two holes surrounding the mini USB port to hold an accessory</figcaption>
{{< /centered_container >}}

Examples of commercialised accessories include:
- A **microphone** for audio chatting.
- A **GPS antenna**: Apart from navigation, I don't know what else can you do with it.
- A **camera**: A good addition for the microphone to achieve videoconferencing. This makes sense as Skype was even embedded in the system.

The funny thing is that these gadgets were also available for Pocket PC/PDAs as well, it makes you think if Sony envisioned the PSP as a *PocketPC for youngsters*.

#### Home Console connectivity

After the release of the Playstation 3, a new feature appeared on the PSP: **Remote Play**. Using a WiFi connection, the PS3 could be remotely controlled from the PSP.

This was many years before the Wii U launched, eh? Unfortunately, only a handful of PS3 games supported remote play, meaning users could only navigate through the menus, do some multimedia and play Playstation 1 games.

---

## Operating System

Over the years, the program in charge of controlling the console before a game starts has become more and more convoluted. Mainly due to the increasingly need for security and services (updatable APIs, online multiplayer, multimedia, etc). The PSP will try to fit all of that in very constrained hardware. Now, I don't mean to say that the result will be *mediocre*, but many decisions are the result of balancing costs, performance and robustness.

#### Architecture and design

First and foremost, the PSP contains a hidden/undocumented **4 KB ROM** inside Tachyon where the boot-loader is. In other words, upon powering on, the CPU will start by looking for instructions in there. That ROM has many names, including 'Bootrom', 'Pre-IPL' and 'Lib-PSP iplloader'. The latter is the internal codename used by Sony.

The rest of the system is installed in **32 MB of NAND Flash** found in the motherboard. Here is where the majority of the PSP's Operating system is residing.

The Operating System is composed of the following components:

{{< tabs >}}

{{< tab name="Modules" active="true" >}}{{% inner_markdown %}}
A **Module** is what we would call a 'program' or 'driver' in the PC/Windows world. Once loaded, modules may reside in memory and perform the following tasks: 
- Expose functions to simplify access to certain hardware.
- Run as a foreground program (i.e. games).

A Module binary may be encrypted or decrypted, it also embeds metadata to define whether it's a 'user module' or 'kernel module' type, the latter allows the new module to control privileged areas in memory (where the Kernel resides).

Modules are summoned by the 'Kernel' or 'Visual Shell', user modules can execute other modules at the discretion of the Kernel. Consequently, the kernel will never load kernel modules from UMD discs, for instance.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Kernel/IPL" >}}
{{% inner_markdown %}}
While this Operating System doesn't contain a Kernel per se, it has multiple components that fill in the duties of a traditional Kernel.

The first component is the **IPL**, which is loaded by the Pre-IPL and takes care of initialising the hardware. A part of IPL also stores unique information of the console (such as the MAC Address, serial number and 'UMD authentication keys'). Overwriting this area causes catastrophic results! So, for obvious reasons, the UMD keys are duplicated a couple of times to survive corruption. The aforementioned section is called **IDStorage**.

The IPL then loads a set of **kernel modules** which handle low-level operations (memory management, multi-threading and file-system). They also implement memory access exceptions triggered by the MPU (remember the [segmentation paragraph](#focused-memory-management)?). For the sake of simplicity, whenever I use the word 'Kernel' in this article, I'll be referring to these kernel modules.

Having said that, the Kernel doesn't do multitasking but it does implement cooperative multi-threading for single processes.

Moreover, the Kernel will persist in memory as long as the console has power, which is related to the fact **programs (user modules) aren't written for bare-metal**. Instead, user modules rely upon functions exposed by kernel modules. Consequently, the Kernel reserves **4 MB of main RAM** and another **4 MB for 'volatile memory'** (temporary buffer for many operations, with access granted at request). Thus, it only leaves **24 MB of main RAM** (out of 32 MB) for user programs.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Other Parts" >}}
{{% inner_markdown %}}
NAND also houses other sections of the firmware.

Firstly, there's the **Visual Shell** or 'VSH', which is the first thing the user notices after powering on the PSP. The VSH is a graphical user interface that allows the user to run games and other modules (only user-type ones). The Visual Shell is composed of multiple modules, some of which are only loaded upon request.

As a curious fact, the system call used to bootstraps retail games will first reboot the console then load the executable. Possibly to unload the Visual Shell and free up resources for the game.

Secondly, there is a second partition in NAND that stores user-related data, such as network settings. This partition is called 'flash1' (as opposed to 'flash0') and its content is referred to as **System Settings**.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

#### Boot process

Now that we've identified the main parts, let's see how they organise to put the console into a 'working state' once we switch it on. Security is briefly discussed here, but I recommend reading the next section which goes into more depth.

That being said, the *complex* boot process works as follows:
1. Main CPU's reset vector is at '0x1FC00000', which points to the Pre-IPL ROM inside Tachyon.
    1. The first half of the Pre-IPL tells the CPU to copy the other half to Scratchpad memory and continue execution from there.
        - Pre-IPL will now look for the next stage from either **NAND or an external memory stick**. When the latter is selected (never under normal means), the PSP enters a mode called **Factory Service Mode**. For simplicity purposes, we'll focus on the normal mode (selecting NAND).
    3. Pre-IPL initialises the NAND controller and continues execution from NAND. The second part runs IPL. It's encrypted, so it will be decrypted (using 'KIRK', more details later) and copied to eDRAM (in the Graphics Engine) as a working area.
    4. Once finished decrypting, it continues execution at eDRAM, where the decrypted IPL is.
2. IPL's execution process is divided into three stages.
    1. The first stage, called **Loader**, resets the main CPU and hides the Pre-IPL ROM from the memory map. Loader also initialises minimal hardware and decompresses 'Main.bin', the next stage, on eDRAM.
    2. **Main.bin** focuses on initialising the rest of the hardware, including main memory. Once it finishes, it decrypts the third stage on to main memory and continues execution from there.
    3. The final stage it's referred to as **Payload** and loads the Kernel. The Kernel is stored in the form of different binaries, modules and meta-data that, once loaded into main RAM, will give life to the system. Afterwards, the interactive shell is shown.

#### Visual Shell

This console debuts the famous **XrossMediaBar** or 'XMB' (at least, internationally speaking). XMB is the name of the feature-rich GUI shipped with the PSP.

{{< centered_container >}}
  {{< tabs nested="true" >}}
    {{< tab name="Home" active="true" >}}
      {{< linked_img src="xmb/main.jpg" >}}
      <figcaption class="caption">Main screen (no game inserted or installed)</figcaption>
    {{< /tab >}}
    {{< tab name="Multimedia" >}}
      {{< linked_img src="xmb/photo.jpg" >}}
      <figcaption class="caption">XMB includes a photo and video viewer</figcaption>
    {{< /tab >}}
    {{< tab name="Game" >}}
      {{< linked_img src="xmb/game.jpg" >}}
      <figcaption class="caption">'Game' category allows to run a game, manage saves or search for other PSPs (Game Sharing)
      <br>When the user steps on the actual game item, the XMB styles itself with assets provided by the game, until the user selects another element from the category</figcaption>
    {{< /tab >}}
    {{< tab name="Settings" >}}
      {{< linked_img src="xmb/setting.jpg" >}}
      <figcaption class="caption">'Settings' category provides lots of customisation options
      <br>Some items group many settings controls</figcaption>
    {{< /tab >}}
    {{< tab name="Keyboard" >}}
      {{< linked_img src="xmb/keyboard.jpg" alt="Keyboard interface" >}}
      <figcaption class="caption">This is how you type in this console, old-school cellphone style</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /centered_container >}}

From the Human-Computer Interaction standpoint, the XMB's design reveals very interesting approaches for solving many interaction challenges (such as navigating around many depths of information, avoid flooding the user with options and fitting all these solutions in a 4.3" screen). In essence, information is organised by 'categories' and 'elements'; and the D-Pad is the key to navigate through them. The Left/Right arrows switch between categories and Up/Down arrows select an element from the same category. It's worth pointing out that all categories are found within the same hierarchy level. So, no entry is shown superimposed over the rest (impeding the insertion of attention-stealing advertisement and whatnot).

On another topic, the XMB provides customisation options, such as changing the background colour and re-arrange items. Moreover, it provides access to multimedia services and, of course, allows the user to load up the game (whether from a UMD or Memory Stick).

There's also a file viewer embedded that's used for both multimedia and save management.

#### Updatability

As we've seen before, everything except Pre-IPL is stored in writable storage, and thus is 'updatable'. Sony distributed firmware updates in the form of a downloadable file. Users could either manually download it or use the 'System update' assistant from XMB to automatically download it (using WiFi connection) and install it.

{{< centered_container >}}
  {{< linked_img src="xmb/update.jpg" >}}
  <figcaption class="caption">System Update 'Wizard'</figcaption>
{{< /centered_container >}}

Some updates strengthened the security system by adding more layers of encryption on the boot process and integrity checks on kernel modules.

From the developer's standpoint, some updates enhanced the APIs by adding more functions and correcting existing ones, consequently making games strongly dependent on a higher system version. Hence, users had to update their PSPs to play such games. However, new updates were always backwards compatible, so an old game should never break on an updated system (in theory).

From the user's perspective, some updates brought new services, like a web browser, RSS reader and so forth. After the release of the Playstation 3, many online applications were ported to the PSP, such as the 'Playstation Store' and 'Remote Play'.

---

## Games

For the first time in this series, games developed for this console are not written for bare-metal: Instead of relying on memory address and ports, they have to call modules to perform operations on the hardware. The APIs found in the official SDK are modelled after this design.

#### Development ecosystem

The development toolkit provided by Sony to game studios was developed by SN Systems (the same author of the PS1 and PS2 kits). The software kit required a Windows XP with Cygwin or a CentOS Linux setup.

In this kit we could find:
- An **SDK**: Containing C++ libraries that linked to a particular version of the firmware (as they depend on the modules installed).
- A choice of **two compilers**: A variant of the GCC compiler called 'psp-gcc', or one developed by SN Systems called 'SVCPSP' ('SVC' targeting the PSP).
- The **GDB debugger**.
- SN System's **assembler**, **linker** and **archiver**. The latter generates PSP executables.
- A **plugin for Visual Studio 2005 and 2008**, so developers could use Microsoft's IDE for PSP development.

Initially, Sony only provided an emulator to test PSP software. This was later replaced with the **PSP Hardware Tool**: A PC-like tower connected to a dummy PSP case (similar to the [Nintendo DS kit]({{< ref "nintendo-ds#tab-9-1-the-hardware" >}})). The devkit connected to the workstation using a piece of software called **ProDG** (only available on Windows).

#### Storage medium

The PS1 and PS2 featured two types of storage, a read-only disc for loading games and a re-writable 'memory card' for storing saves and executables. The PSP continued this tradition using different methods. Considering it was their first -mainstream- portable console, how did Sony manage to find the right mediums? Well, taking into account they were the authors of the *BetaMax*, *MiniDisc* and so forth, you can guess what they did this time... introduce *even more* formats.

{{< tabs >}}
{{< tab name="UMD Discs" active="true" >}}
{{< float_block >}}
  {{< linked_img src="retail.jpg" alt="Retail game" >}}
  <figcaption class="caption">Typical retail game</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
CDs and DVDs are too big, easily copiable and conventional readers won't work in a shaky environment (those who tried listening to an audio CD on a Walkman while *walking* know what I'm talking about). So, Sony's solution was a new invention from the ground up called **Universal Media Disc** or 'UMD', a proprietary medium.

UMDs hold either **900 MB or 1.8 GB** depending on being single-layered or dual-layered, respectively. Not only UMDs differ physically from a DVD or MiniDVD, but they also contain a different internal structure. Apart from games, Sony published the 'UMD Video' and 'UMD Audio' specification, allowing other types of distributors to ship their content to PSP users - Remember this is the year 2004 (when the *iPod Photo* had a 2" display).

These discs are presented with an **unremovable caddy**, the centre is magnetic so it attaches itself to the reader's motor once the disc is inserted.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Memory Stick Duo" >}}
{{< float_block >}}
  {{< linked_img src="io/memory_stick.jpg" alt="Memory Stick" >}}
  <figcaption class="caption">A Memory Stick next to a conventional SD card</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The 'portable' equivalent of the Memory Card is the **Memory Stick Pro Duo**, another product of Sony's insistence to bring more mediums to a flooded market (remember the massive number of options back then? Even [flashcarts]({{< ref "game-boy-advance#flashcarts" >}}) acommodated many of them). This was released during the boom in consumer photography and cellphones, when people wanted lots of storage for a cheap price; and just before the SD card took over.

The Memory Stick, like the rest, is a protocol that interfaces internal memory (like Flash) with any peripheral in a way that's convenient. So, you may swap cards between the computer and the camera without having disassemble anything. The 'Pro Duo' variant (used by the PSP) has a capacity of **up to 32 GB** and **1.23 MB/s read** and **0.54 MB/s write speed**.

The PSP relies on the Memory Stick to store user-related content, such as saves, media and games. It's formatted with the FAT32 file system. The controller embedded in the Memory Stick can include MagicGate DRM and the PSP will read either type. 

The contents are not memory mapped: The file system is accessed through system calls (kernel modules).
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Network services

Eventually, Sony caught up with the competition and improved its online infrastructure. They even offered more services not yet provided by Microsoft or Nintendo.

{{< tabs >}}
{{< tab name="Playstation Network" active="true" >}}
{{< float_block >}}
  {{< linked_img src="xmb/network.jpg" >}}
  <figcaption class="caption">PSN category in XMB</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Microsoft named its online infrastructure [Xbox Live]({{< ref "xbox#network-service" >}}), while Nintendo named it [Nintendo WiFi Connection]({{< ref "nintendo-ds#network-service" >}}) and Sony... **Playstation Network** or 'PSN'.

This service superseded the primitive [DNAS]({{< ref "playstation-2#network-service" >}}), previously deployed for the Playstation 2. One of the additions was the use of a centralised authentication system.

Users could register a free PSN account using the console, this enabled them to play with other people around the globe using WiFi connection.

It's worth mentioning that at first, the PSP didn't include many online services. This changed drastically with the release of the Playstation 3, but I guess this is a story for a later article...
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Playstation Store" >}}
{{< float_block >}}
  {{< linked_img src="xmb/store.jpg" alt="PlayStation Store" >}}
  <figcaption class="caption">PlayStation Store</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Another thing that followed after the debut Playstation Network was the **Playstation Store**. As the name indicates, it's an online store that allows users to acquire games digitally and download them on the Memory Stick.

I suppose this was a great relief for small game studios, as physical distribution always implied a significant cost, which would eventually [impact]({{< ref "nintendo-64#games" >}}) the price of the game. Strangely enough, a later model of the PSP called 'PSP Go' enforced this process as it could only play games acquired from the Playstation Store (it lacked the UMD reader).

Later on, the game catalogue drastically expanded with the arrival of **[PS1 games]({{< ref "playstation#games" >}})**. These were downloaded to the memory card and played like any other digital game. PS1 binaries on the PSP worked with the use of **software emulation**, Sony embedded an in-house emulator in the system to make all this work seamlessly.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Updatable content" >}}
{{< float_block >}}
  {{< linked_img src="xmb/game_update.jpg" >}}
  <figcaption class="caption">'Update game' option in XMB</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Last but not least, online services provided the opportunity to **download game patches** (in the form of 'updates') directly from the console.

However, not many games used this feature. This is hard to conceive nowadays, as large game updates tend to be published before the game hits the store...
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Anti-Piracy and Homebrew

Just because this is a portable console doesn't mean it should have weaker security than the PS2. It's quite the opposite in all senses - The security system implemented on the PSP is very diverse, with some parts still remaining a mystery to this day.

Now, we all know by now that its implementation contained pitfalls that eventually lead to Homebrew and piracy, but considering it was released in 2004 and it's a portable console, you may find that some algorithms bundled are state-of-the-art compared to the competition.

#### Physical security

I'm going to describe the three main chips that compute security-related operations. The algorithms can be pretty complicated for first-timers, but I'll try to give a quick overview. Don't forget to check out the bibliography if you find it interesting, as this writing is primarily focused on the PSP.

Throughout the explanations, keep in mind that none of this information was documented by Sony for security reasons, in addition to labelling security-related routines with silly names.

Furthermore, I won't be able to go over each cypher system, as it's a tortuous topic and would divert the article from its main goal. Nonetheless, I invite you to learn about the logic behind each algorithm from other sources.

{{< tabs >}}
{{< tab name="KIRK" active="true" >}}

{{% inner_markdown %}}

To start with, we got **KIRK**, another component inside Tachyon. KIRK is the implementation in hardware of the following algorithms (think of it as an accelerator):
- **AES Encryption & Decryption**: A symmetric encryption system that operates with a given key. KIRK also provides a key vault of 128 keys and a master key, both 128-bit long, to encrypt/decrypt data.
- **ECDSA**: An asymmetric encryption system. It includes two functions:
  - Public-Private key generation: Used for encrypting and decrypting user data, such as save files.
  - Signature verification: Checks and decrypts data encrypted by Sony (it stores a public key).
- **SHA-1**: Generates a hash value from data. ECDSA depends on SHA during signature verification (confirms that the original data hasn't been tampered with). 
- **Cipher-based Message Authentication Code (CMAC)**: Another algorithm used to decrypt and verify the integrity of the encrypted data (whether it comes from Sony or not) using a shared key. It relies on AES for decryption.
- **Pseudo-random number generation**: As the name indicates, it (attempts) to generate an unpredictable number.

In short, the CPU sends commands to KIRK (i.e. encrypt this string with algorithm x and key y). It can behave like a bus master as well, so it stores the results on main RAM right away.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Lepton" >}}

{{% inner_markdown %}}

As you know, UMD discs are not easily duplicated, but its contents are also stored in an encrypted form. Users will never notice this, nevertheless, since the decryption process is performed by hardware too.

**Lepton** is another System-on-Chip living alongside Tachyon, it controls the UMD drive and serves as a middle-man between the main CPU and UMD content.

Lepton contains an enormous amount of circuitry - you may even consider it another computer living inside the PSP. This component includes its own CPU, a DSP for decoding, 480 KB of memory (as buffer) and finally, 384 KB of ROM for storing its firmware.

The main CPU communicates to Lepton using the ATAPI protocol, an interface traditionally used for plugging conventional CD/DVD drives to PCs. The main difference, however, is that Lepton will decide whether to load & decrypt the UMD data (if the disc is authentic).

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="SPOCK" >}}

{{% inner_markdown %}}

Apart from the encryption system handled by Lepton, UMD's sectors are encrypted using AES. **SPOCK** is another block found next to KIRK that focuses on decrypting each sector of the UMD disc.

Inside NAND Flash, IDStorage stores an encrypted key known as 'master key'. Thus, SPOCK contains a hardwired key to decrypt the 'master key'. The decrypted master key is then used by SPOCK to decrypt another key found in the UMD. The latter key is then used to read each sector. *Easy-peasy!*

By the way, even though SPOCK is found within Tachyon, it's operated by Lepton.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

To sum up, having these routines in hardware allows the system to execute games without consuming general-purpose resources to apply encryption/decryption.

#### Software security

The operating system will use the hardware provided to compose a security system. Overall, the software is protected with the following principles:
- **Chain of trust**: Pre-IPL, the first stage of the boot process, is a read-only piece of code written by Sony and embedded during manufacturing. From there, anything that Pre-IPL approves is deemed trustworthy, and so are the subsequent programs executed.
- **Signed executables**: Not only executables are encrypted, but they are also signed with keys that only Sony knows, this ensures that tampered data can't be resigned.
- **Obscure hardware**: KIRK, SPOCK and Lepton are undocumented and obfuscated blocks of silicon that won't expose which algorithms are applied and how.
- **Developer-proof security**: The MMU's privilege modes will restrict access to sensible memory locations, even with the existence of user-level exploits on retail games (which lead to arbitrary code execution).

#### Defeat

I think the last thing hackers ever did was to give up on this console (maybe because the number of benefits made it very attractive for Homebrew development?). Please note that the amount of development in this area is outstanding, so I had to draw a line somewhere. Thus, only huge discoveries are mentioned here. In any case, playstationdev.wiki keeps a good archive of every single one.

{{< tabs >}}
{{< tab name="Early blunders" active="true" >}}

{{% inner_markdown %}}
After the release of the PSP in Japan, it was a matter of time before user-land exploits emerged, some of them were combined with the lack of security in early versions of the firmware:
- 'Wipe Out' embedded a web browser to access downloadable content, it wasn't protected against **DNS attacks (domain hijacking)**, allowing users to browse any URL in the world-wide-web.
  - Later on, it was discovered that the browser's URL entry didn't verify the input. So, entering 'file:///disc0:/' as URL would **list the contents of the UMD**, already unencrypted. This enabled hackers to inspect PSP executables and reverse engineer them.
- The first revision of the system **didn't check for signatures** in the executables stored in the memory stick, allowing to run custom user modules (not kernel ones).
  - This was probably accidental as it's common practice to use a debug version of the system (without signature checks) for development purposes. So they may have rushed into release and forgotten to re-activate the checks, similarly to what [Microsoft]({{< ref "xbox#tab-9-2-bootstrap-search" >}}) did.
- After the previous one was patched, hackers discovered **a flaw in the file system implementation to keep running unsigned code**. In a nutshell, the user would have to duplicate the folder containing the executable, add an extra '%' at the end of the name of the second folder and strategically reorganise the contents of both folders.

These early blunders help to build a knowledge base of the inner workings of the PSP, which lead to more attack vectors and software routines to interact with the hardware (keep in mind only game studios had access to official SDKs and documentation).

As with any other cat and mouse game, Sony responded with more system updates (in addition to new games requiring them) while newer exploits (the so-called 'TIFF', 'GTA' exploit and so forth) kept appearing. It's worth mentioning that these were only considered 'Homebrew Launchers' as kernel access wasn't obtained yet.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Downgrading" >}}
{{< float_block >}}
  {{< linked_img src="downgrader.jpg" alt="Chronoswitch Downgrader" >}}
  <figcaption class="caption">Chronoswitch downgrader, a modern downgrade tool</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}

As soon as Sony gained momentum with strong firmware updates with security patches and attractive features (i.e. a web browser), old versions became some sort of 'promise land', where advanced users enjoyed the ability to run their homebrew. However, acquiring a PSP with that system version became increasingly challenging. Thus, 'downgrading' quickly gained popularity. Downgrading is the opposite of upgrading, consisting of replacing the current firmware installed with one of lower version (and better exploited).

A common approach for performing a downgrade consists of executing an exploit that would trick the system into installing an old update file. This wasn't easy as the latest system may not have been exploitable at that time.

Subsequently, two 'modchips' reached the market, 'Undiluted Platinum' and 'PSP-Devolution'. They shipped with a separate NAND chip and was used as secondary NAND storage. Enabling to boot a secondary (and exploitable) firmware, or replace the primary one. This approach is only supported on the PSP model 1000 as later ones unified Tachyon and the NAND + DDR SDRAM package.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Pandora" >}}

{{% inner_markdown %}}
The 'cat and mouse' game was the order of the day until the so-called 'Pandora' arrived.

The 'Pandora battery method' is a popular (and respectable) collection of achievements. It managed to bypass most of the security layers and focused on where Sony could not react quickly, the Pre-IPL. This is what Pandora managed to succeed in:
- **Find a way of entering entering 'Service Mode'**: By tampering with the PSP's removable battery, which had some circuitry attached for identification purposes, hackers discovered that overriding its serial number value to '0xFFFFFFFF' triggered 'Service Mode' at boot time. Thus, Pre-IPL would look for a secondary system in the Memory Stick. The modified battery was referred to as **JigKick** or 'Pandora' Battery.
  - Users could either create a Jigkick battery with a hacked PSP or by desoldering the ground pin of the battery's EEPROM. The latter is riskier taking into account casual users are disassembling a Lithium battery! Thus, some companies distributed their own 'Pandora battery' maker.
  - A Jigkick battery works like any other battery. However, when inserted, the PSP will always boot from the Memory Stick.
- **Trick KIRK loading a fake/unsigned firmware**: The next step was to find a way to load a fake IPL in Service Mode (it still has to be encrypted and signed). Well, hackers found a way by embedding an unencrypted payload next to the encrypted block that Pre-IPL will try to validate (due to Pre-IPL not checking if the encrypted block is 1 KB long). The encrypted section had to be crafted in a way that, once decrypted, will tell Pre-IPL to 'jump 100 Bytes down'. Thus, bypassing the security routines and redirecting execution to the payload, enabling to **execute code with maximum privileges** on Service Mode. This reminds me of what happened to the [Wii]({{< ref "wii#the-fall-of-encryption" >}}).
  - Since the custom IPL required to be executed from a specific sector in the Memory Stick, special programs were developed so any user could install it. A memory stick with a custom IPL is called **Magic Memory Stick**.

What remains here is to embed a useful payload. Popular options included a 'de-bricker' which (re)installed a clean official firmware onto the PSP (thereby repairing it if the NAND was previously corrupted), a downgrader or a 'custom firmware' installer (more details later).

Don't forget that all this was a tremendous task, especially if we take into account KIRK wasn't reversed engineered and the encrypted block that makes Pre-IPL 'jump' had to be brute-forced.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="CFW and beyond" >}}
{{< float_block >}}
  {{< linked_img src="xmb/cfw.jpg" >}}
  <figcaption class="caption">Some CFWs include special modules to operate low-level options</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Pandora method represented a big hit for Sony, it was an effective bootrom exploit, and just like the [Nintendo DS]({{< ref "nintendo-ds#tab-11-3-native-slot-1" >}}), fixes would only arrive in the form of new hardware revisions.

Furthermore, before Pandora was published, a so-called **Custom Firmware** or 'CFW' arrived. CFW is an official firmware with many modifications applied (such as Homebrew modules). These customisations will enjoy kernel privileges and as you may guess, they have complete control of the console. Examples of new modules included ISO loaders, signature disablers, low-level CPU management, a 'plugin' loader (to add more customisations without re-installing the firmware), and many, many more. Lots of hackers crafted their own flavour, such as M33, PRO and ME.

A CFW can be installed on top of the current firmware with the help of any kernel-level exploit (this was the earliest method). However, it can only be automatically booted with exploits previously used by Pandora to bypass signature checks. Otherwise, users need to rely on cat-and-mouse exploits to reboot to a CFW. That's why the latest PSP models won't be able to kickstart a CFW using the *good-old* methods.

In the end, the last straw happened when the security system of the Playstation 3 was hacked, as it contained the **private keys used to decrypt PSP executables** (the PS3's OS includes a PSP emulator). This allowed anyone to sign user-land software and embed a kernel exploit to produce CFW installers and/or CFW loaders, for instance.

In recent developments, it was discovered that the latest firmware contains a kernel exploit during the boot process, which could be used to kickstart a CFW of choice. This was packaged in an solution called **Infinity 2**.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

To sum it up, Sony was right when they claimed the UMDs were un-hackable. After all, hacking was achieved thanks to (many) flaws in software.

#### Homebrew encouragement

Not only development centred on breaking security mechanisms, but there were also communities committed to provide Homebrew developers with the necessary tools to build their software without legal repercussions. For instance, the pspdev group published an open-source SDK called **PSPSDK** that replicated many interfaces and didn't enforce previous restrictions imposed by Sony.

PSPSDK also includes a toolkit to handle the compilation and packaging process, just like the official SDK but targeting a PSP running a CFW instead.

---

## That's all folks

{{< centered_container >}}
  {{< linked_img src="mypsp.jpg" alt="My PSP" >}}
  <figcaption class="caption">The PSP acquired for this article
  <br>This is what grownups used to buy if they wanted to look cool 😉 (I had a DS)</figcaption>
{{< /centered_container >}}

There you go, you've just finished reading about the last portable console before smartphones and tablets took over! I think this gives us an idea of what services/features users were expecting from portable handhelds in the early noughties - and how this evolved during the next decade.

Nevertheless, I want to thank the 'PSP Homebrew Community' discord for not only taking the time to proofread this *endless* article, but also to enlighten me with even more information. Various members of that group have dedicated a great effort to develop free tools and Homebrew applications to expand the capabilities of this console. My acknowledgement is also directed to all the authors that produced the documents listed in the sources section. It took me ~3 months to write this article, but it was possible thanks to the combined years of work that other people dedicated to reverse-engineer this console.

As for the next article, I'll need to first take one month or two to focus on all the issues & requests submitted on the [Github repo](https://github.com/flipacholas/Architecture-of-consoles) and do some maintenance work on the site as well, but rest assured I'm striving to make the next writing as complete as this one!

Until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- Sony Computer Entertainment, **PSP System Overview**
- groepaz/hitmen, [**yet another PlayStationPortable Documentation**](http://hitmen.c02.at/files/yapspd/psp_doc/)
- pspdev, [**PSPSDK Documentation**](https://pspdev.github.io/pspsdk/index.html)
- playstationdev.wiki, [**PSP Developers Wiki**](https://playstationdev.wiki/pspdevwiki/index.php?title=Main_Page)
- TyRaNiD, [**The Naked PSP**](https://uofw.github.io/upspd/docs/software/naked_psp.pdf), ps2dev.org
- PCMag India staff, [**Sony PSP Specs** (Leaked slides, I think)](https://in.pcmag.com/gallery/273/sony-psp-specs)
- Alex Marshall, [**PSPTEK**](http://daifukkat.su/docs/psptek/)

#### CPU

- Sony Computer Entertainment, **Allegrex User's Manual**
- Sony Computer Entertainment, **Allegrex FPU User's Manual**
- Sony Computer Entertainment, **Vector Floating-point Processing Unit (VFPU) User's Manual**

#### Graphics

- Sony Computer Entertainment, **Graphics Engine User's Manual**

#### I/O

- edepot.com, [**PSP Secrets**](http://www.edepot.com/reviews_sony_psp.html)
- \>NIL:'s white page, [**Interfacing with the PSP Remote port**](https://nil.rpc1.org/psp/remote.html)
- SilverSpring, [**Motherboard IC’s**](http://uofw.github.io/upspd/docs/SilverSpring_Blog/my.malloc.us/silverspring/motherboard-ics/index.html)

#### Audio

- Sony Computer Entertainment, **Sound Overview**

#### Operating System

- LAN.ST, [**IdStorage keys and their uses + regeneration \[TECHNICAL DISCUSSION\]**](http://uofw.github.io/upspd/docs/software/IdStorage%20keys%20and%20their%20uses%20+%20regeneration%20\[TECHNICAL%20DISCUSSION\].html)

#### Games

- Sony Computer Entertainment, **PSP Setup Guide**

#### Anti-Piracy

- pastie.org, [**UMD Keys Decrypted**](https://web.archive.org/web/20141201091221/http://pastie.org/1467912)
- PPSSPP's source code, [**kirk_engine.c** (Github)](https://github.com/hrydgard/ppsspp/blob/a1964ca1a108b1340e54ae30262f317c9dd6999b/ext/libkirk/kirk_engine.c)
- playstationdev.wiki, [**Keys**](https://playstationdev.wiki/pspdevwiki/index.php?title=Keys)
- wikibooks.org, [**PSP Homebrew History**](https://en.wikibooks.org/wiki/PSP/Homebrew_History)
- Fanjita, [**Pandora's Battery** (Official announcement)](https://web.archive.org/web/20070828091049/http://www.noobz.eu/joomla/news/pandoras-battery.html/)
- Davee, [**6.61 Infinity – An explanation**](https://www.lolhax.org/2017/09/24/6-61-infinity-an-explanation/)

#### Photography

- Console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams, casual photos and game screenshots: **Me**
- Memory Stick icon: [**Kisscc0**](https://www.kisscc0.com/clipart/sony-corporation-flash-memory-cards-sony-dsc-w1-me-i6mxg4/)

#### Other Media

- Raw music files: [**Zophar gallery**](https://www.zophar.net/)
- 3D models: [**The Models Resource** (Archived)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)