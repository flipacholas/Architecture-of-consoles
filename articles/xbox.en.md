---
name: Xbox
subtitle: A feared competitor
date: 2020-06-26
releaseDate: 2001-11-15
cover: "xbox"
javascript:
  - plyr
generation: 6
top_tabs:
  Model:
    file: international
    caption: "The original Xbox\n<br>Released on 15/11/2001 in America, 22/02/2002 in Japan and 14/03/2002 in Europe"
  Motherboard:
    caption: "Showing first revision\n<br>Controllers are plugged using a separate daughterboard\n<br>Missing SRAM chips are on the back and there are four unpopulated SRAM slots"
  Diagram:
    caption: "Each controller is connected to a separate USB hub"
# SEO
title: Xbox Architecture
---

## A quick introduction

It seems that Microsoft has decided to pick up where Sega left off. Their offer? A system with familiarities appreciated by developers and online services welcomed by users.

Please note that, due to consistency with other sources, this article separates storage units between the metric prefix (i.e. megabytes or 'MB') and the standardised binary prefix (i.e. mebibytes or 'MiB'), thus:
- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

... and so forth.

---

## CPU

The processor included in this console is a slightly customised version of the famous **Intel Pentium III** (an off-the-shelf CPU for computers) running at **733 MHz**. With this, one can assume this console is just a PC behind the scenes... I won't tell you the answer, but I promise that at the end of the article you will be able to reach your own conclusion.

Anyhow, Pentiums, along with other lines of CPUs designed and manufactured by Intel, were incredibly popular in the computer market. Such was Intel's market share that they became the de-facto reference point for quality: As a typical user, if you wanted a good computer and had the budget, you only had to look for *something* carrying an Intel CPU. We all know by now that there are more factors involved, but that's what the marketing guys at Intel managed to project.

#### Technical information

Now that we positioned Intel in the map, let's go back to the topic of this console. During my research, I was expecting to find documentation with the level of depth as other CPUs (MIPS, SuperH, ARM, etc), but instead, I stumbled across an excessive amount of marketing terms that only diverted my search. So, for this article, I came up with a structure to organise all the necessary information which will help to understand how this CPU works. Furthermore, I will try to introduce some terminology that Intel used to brand this CPU.

Having said that, let us take a look:

{{< tabs >}}

{{< tab name="Branding" active="true" >}}

{{< float_block >}}
  {{< linked_img src="cpu/branding.png" alt="Branding" >}} <figcaption class="caption">How is this study organised</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
First things first, the Xbox's CPU is identified as a **Pentium III**. So what does this mean? Back then (early 00s), the Pentium series represented the next-generation of CPUs. They were 'new high-end' that grouped all the fancy technology that made computers super-fast, plus it helped buyers decide which CPU they had to buy if they wanted *the best of the best*.

The Pentium III replaced Pentium II, which in turn replaced the original Pentium. Moreover, when the first Pentium came out, it replaced the 80486, which in turn replaced the 80386... You get the idea. What matters is that 'Pentium' is mainly a brand name, it's not directly associated with its inner workings. Therefore, we must go deeper!

To dive further and not get lost in the way, I have catalogued the information into three sections which combined, make up the chip. The first the **Instruction Set Architecture** or 'ISA' (the group of instructions used to command the CPU), **Microarchitecture** (how is the ISA implemented in silicon) and the **Core** (what set of components are used to package the microarchitecture to form the specific CPU model).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="The ISA" >}}

{{% inner_markdown %}}
Indeed, after I mentioned the name Intel it was a matter of time before I introduce the famous **x86**, its instruction set.

Even though x86 first appeared with the release of the 16-bit CPU called **Intel 8086** in 1978, the ISA has been constantly expanded with more instructions as more Intel CPUs were released (80186, 80286 and so on). Consequently, x86 started to fragment as more ground-breaking features were added (i.e. 'real mode', vs 'protected mode' vs 'long mode'). To tackle this, modern x86 applications commonly targeted the 80386 ISA (also referred as **IA-32** or **i386**) as a baseline, which among other things, operates in a 32-bit environment.

Subsequently, Intel presented enhancements of IA-32 in the form of **extensions**, meaning these may or may not be included in an IA-32 CPU. Programs can query the CPU to check if a specific enhancement is present. The Xbox's CPU includes two extensions:

- **MMX** (Multimedia Extension): Adds 57 SIMD instructions and 8 64-bit registers (integers only) that can speed up vector operations.
- **SSE** (Streaming SIMD extension): Another SIMD-type extension which addresses some of the limitations of MMX (lack of floating-point support and unable to use floating-point unit in parallel). It adds 8 128-bit registers (called 'XMM') that hold four 32-bit floats; and 56 new instructions.

Good news is that since the console will always have the same CPU features, programmers can optimise their code to exploit these extensions as they will always be present.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="The Microarchitecture" >}}

{{% inner_markdown %}}
When it comes to building a circuit that can interpret x86 instructions, Intel has come up with so many different designs for their CPUs. Some designs were featured with the release of a new Pentium Series (i.e. Pentium 4) while others are featured when Intel releases an 'enhanced' version of a Pentium (such as the 'Pentium Pro'). Nevertheless, since the release of the first Pentium, the microarchitecture is identified with a different name from the CPU model. For example, the original Pentium includes the 'P5' microarchitecture.

Now, the Xbox CPU, along with the rest of Pentium III processors, use the **P6 Microarchitecture** (also known as 'i686'). This is the 6th generation (counting from the 8086) which features:
- A *massive* **14-stage pipeline**.
- **Out-of-order execution**: If possible, the CPU re-orders the sequence of instructions to increase efficiency and performance.
- **Speculative prediction**: Similar to [branch prediction]({{% ref), but it also executes the branch that the CPU has predicted it will be chosen.

Having said that, take a closer look at these features. It so happens they are very similar to [previous consoles]({{% ref), however, the other CPUs are very different in terms of design compared to Intel ones. Historically, one could argue that Intel could have never been able to accomplish, let's say, a pipelined CPU. Yet they managed to do so, so let us see why...
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="CISC or RISC" >}}

{{% inner_markdown %}}
All of the competitor's consoles previously analysed contain a **RISC** CPU whereas Intel's x86 ones are **CISC**. RISC CPUs are known for having a simplified instruction set compared to CISC CPUs. This includes, for instance, not featuring instructions that operate values directly from memory (as opposed to registers).

One of the advantages of RISC processors is that their simplistic approach enables its CPUs to be designed with a modular sense, which in turn can be exploited to improve performance with parallelism techniques. This is why we have seen CPUs like MIPS and PowerPC debuting pipelining, superscalar, out-of-order, branch prediction, etc. On the other side, 'CISC' processors were design many years before the RISC processors appeared and aimed to solve different needs. Consequently, their designs are not as flexible as RISC ones.

Back to the original question, the P6 is an interesting design, because while the CPU only understands a CISC instruction set (x86), it interprets the ISA using **microcode** (called 'micro-operations') and the unit that executes that code is built around the guidelines of RISC. All in all, this allows Intel to apply the optimisations of RISC processors while keeping a 'CISC layer' for compatibility with x86.

Microcode is already embedded in the silicon but it can be patched, allowing Intel to fix its CPUs after production whenever a bug or a security vulnerability is discovered. If you have read previous articles (i.e. N64 or PS2), bear in mind that Intel's microcode is **not publicly accessible** (let alone documented) and Intel is its solely 'maintainer'.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="The Core" >}}

{{< float_block >}}
  {{< linked_img src="cpu/core.png" alt="Core" >}} <figcaption class="caption">Coppermine design</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
There were numerous chips released using the P6 microarchitecture. Specifically, the Xbox includes one model called **Coppermine**. This was also released as the second revision of the Pentium III (replacing the 'Katmai' core) and includes the following components:
- **32 KiB L1** cache: Divided between 16 KiB for instructions and 16 KiB for data.
- Integrated **128 KiB L2** cache: This is *odd* since the off-the-shelf Coppermine has 256 KiB of L2. In fact, the Coppermine128 (found in the Intel 'Celeron' brand, the low-end Pentium alternative) has the same amount of L2. This is probably done to reduce manufacturing costs, and keep this console at a competitive price.
- 133 MHz **Front-side bus**: This is the bus that connects the L2 cache with the memory controller, we'll see more about it later on.
  - Intel names it 'Front-side bus' to distinguish it from another bus which connects the L2 (external cache) with the L1 (internal cache). The latter bus is called 'Back-side bus' and yes... It's an unfortunate name to use in the UK.

Coppermine also features two 'enhancements' over their original implementation of L2 cache, these are the **Advanced Transfer Cache** and the **Advanced System Buffering**. To sum them up, L2 cache is on-chip and their buses are wider, which help to reduce possible bottlenecks found in the Front-side bus.

Finally, the chip uses the 'Micro-PGA2' socket to connect it to the motherboard, but like any other console, the Xbox has it soldered with a Ball Grid Array or 'BGA'.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

#### P6 and the end of Pentium numbers

Here's a bit more history: After the years of the P6, Intel planned to succeed it with the 'Netburst' microarchitecture (featured in the Pentium IV). However, the line of succession also ended there: The microarchitecture couldn't be improved anymore. This prompted an Intel team in Israel to revisit the old P6 and develop a more efficient successor. The result was **Pentium M**, eventually extended to form the **Core** microarchitecture (and brand). 'Core' is the basis of present designs.

#### Motherboard architecture

At some point in the history of the PC, motherboards grew so much in complexity that new designs had to be developed from the ground up to efficiently tackle new emerging needs.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="cpu/motherboard.png" alt="Motherboard" >}} <figcaption class="caption">Overview of Xbox Motherboard</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The new standard developed relied on two dedicated chips to handle most of the motherboard functions. These chips are:
- The **Northbridge**: Serves as a memory controller and interfaces the GPU.
- The **Southbridge**: Interfaces the rest of I/O (i.e. USB, ATA/SATA, PCI, etc)

The combination of these chips is called **chipset** and they are important enough to condition the capabilities and performance of a motherboard. The Xbox, being so close to a PC, includes two chips as well: The **NV2A**, a combination of Northbridge and GPU; and the **MCPX** which handles the rest of I/O.
{{% /inner_markdown %}}

{{< /float_group >}}

Both chips are interconnected using a specialised bus called the **HyperTransport**. It's worth pointing out that some PC motherboards also included this technology, just with a different brand (i.e. MPCX → nForce MCP-D)

#### Memory layout

The Xbox includes a total of **64 MiB of DDR SRAM**, this type of RAM is very fast compared to what the competition offers, however it's also shared across all the components of this system. Hence, once more, we find ourselves in front of another **unified memory architecture** or 'UMA' layout.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="cpu/memory.png" alt="Switching Network" >}} <figcaption class="caption">Representation of the switching network <br>GPU uses two banks while the CPU uses a different one, reducing contention in the process</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
We have previously seen how [troublesome]({{% ref) this design can be sometimes. Nonetheless, programs can address this issue by spreading its data between different banks of memory. NV2A implements a **switching network** that enables different units (CPU, GPU, etc) to concurrently access them.

Furthermore, the console features an internal HardDisk, and it *so happens* to have three partitions of 750 MiB each reserved for temporary storage: The CPU can offload some of its data from main RAM, then upload it back whenever it's needed. Bear in mind this is a manual process and does not involve virtual RAM.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Graphics

As we've seen before, the graphics processor resides in the NV2A chip and just like MCPX, it is manufactured by Nvidia.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="halo.png" alt="Halo" >}} <figcaption class="caption">Halo (2001) in 720p mode</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This company has been in the graphics business for a long time, their *GeForce* series are one of the most popular GPU brands in the computer market, directly competing against the Radeon series from Artx/ATI. Overall, this provides good leverage on the quality of graphics found in the Xbox, considering it's Microsoft's first attempt in the console market.
{{% /inner_markdown %}}

{{< /float_group >}}

It all seams reasonable, but was it *really* a certain decision to make back then? It's easy to rely on present history to find out why Microsoft chose Nvidia over other popular brands from the time (3dfx, PowerVR, S3, etc), but if we read more about the competition back then, the panorama of options made it much more complex.

For instance, 3dfx's popular 'Voodoo 2' series had ~70% of marketshare in the PC market by the end of the 90s, while Nvidia was struggling to promote adoption of the new 'GeForce 256' (the first of the GeForce series). After this, Microsoft's choice now sounds more like a risk than a safe bet, but as we know by now, this risk eventually paid off.

> Nvidia was NOT the #1 player they are now, in 1999. They were in trouble. The new Geforce architecture was still young and lots of people didn’t like it. Now it seems forgone. But you’d need to research that history to know that, and why I had to fight to use it. I was right but I didn’t know I was right; I was very worried.
> 
> -- <cite>Seamus Blackley (Co-author of the original Xbox)</cite>

In the following section we'll examine the inner workings of this chip. Now, I'm afraid we find ourselves mixed in a lot of terminology and marketing terms just like the CPU section, but fear not! I'll start with the basics.

#### Architecture and design

The GPU core found on the NV2A is based on the popular 'GeForce3' series of GPUs, it's also referred as **NV20** in Nvidia's technical documents.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="NV2A_Pipeline.png" alt="Pipeline design of the NV2A" >}} <figcaption class="caption">Pipeline design of the NV2A</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Please note that, while the pipeline of the Xbox's GPU is based on the NV20 architecture, the NV2A has some modifications that are not compatible with the rest of the NV20 series (most importantly, it has been adapted to work in a UMA environment).

The units analysed contain a lot more features that go beyond the scope of this article, so I recommend checking out the sources/references if this section catches your attention. Also, since graphics-related terminology is constantly evolving (which can lead to some confusion), I've decided to use the terms chosen by Microsoft/Nvidia during the years of the Xbox, so remember this if you plan to read more graphics-related articles from other sources.
{{% /inner_markdown %}}

{{< /float_group >}}

Having said that, let's take a look at how frames are drawn in the Xbox. Some explanations are very similar to Gamecube's [Flipper]({{% ref), so I recommend reading that article first in case you struggle to follow this one.

{{< tabs >}}
{{< tab name="Commands" active="true" >}}

{{< float_block >}}
  {{< linked_img src="pipeline/Commands.png" alt="PFIFO Diagram" >}} <figcaption class="caption">Commands stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
First and foremost is explaining how the GPU can receive commands from the CPU. For that, the GPU contains a command processor called **PFIFO** that effectively fetches and processes graphics commands (called **Pushbuffer**) in a FIFO manner, the unpacked commands are subsequently delivered to the **PGRAPH** (the block in charge of graphics processing) and other engines.

Like Flipper, geometry doesn't have to be embedded in the command. PGRAPH provides many ways to submit graphics data. For instance, the CPU can allocate a buffer in RAM containing vertex data and then instruct the GPU to fetch them from that location. This approach can be efficient, as it prevents having to embed duplicated geometry.

The next explanations happen in PGRAPH.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Vertex" >}}
{{< float_block >}}
  {{< linked_img src="pipeline/Vertex.png" alt="Vertex stage" >}} <figcaption class="caption">Vertex stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This is an interesting section for this GPU in particular. At this stage, the GPU provides the ability to apply vertex transformations on our geometry. We've already seen this feature with Flipper, but unlike that GPU, this one uses a **programmable engine**. Meaning that developers may specify which vertex operations are performed and how, as opposed to relying on a pre-defined program (although the NV2A can also operate in 'fixed' mode).

This stage is handled by a **Vertex Unit** and the NV2A features two of them. Each can load a program containing up to 136 instructions (also called **microcode**). This program is referred as **vertex program** and it's loaded at runtime. A vertex program can perform the following operations:
- **Arithmetic operations** (i.e. addition, multiplication, minimum, etc).
  - This includes 'helper functions' to assist graphics-related tasks, such as dot product.
- **Vertex Swizzling** (Rearrangement and/or duplication).

In a nutshell, the vertex unit processes vertices by manipulating them in its registers. In other words, once the program is loaded, 16 read-only registers (called 'input registers') are initialised with the attributes of a vertex (each vector contains four components). Afterwards, the unit performs the set of operations (instructed by the program) using the input registers. Furthermore, 12 writable registers and up to 196 constants are provided to assists the computation. Finally, the resulting vertex is stored in another block of 11 writable registers (each one limited to a specific purpose), which are passed on to the next stage. This process is repeated for every vertex received.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Pixel" >}}
{{< float_block >}}
  {{< linked_img src="pipeline/Pixel.png" alt="Fragment/Pixel stage" >}} <figcaption class="caption">Fragment/Pixel stage</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
At this stage, vertices are transformed into pixels. The process starts with a rasteriser that generates pixels to draw each triangle. The NV2A's rasteriser can generate four pixels per cycle. <!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while [competitors embedded it]()). -->

Afterwards, **4 texture shaders** are used to fetch textures from memory, these also offer to automatically apply anisotropic filtering, mipmapping and **shadow buffering**. The latter one is used to test whether a pixel is visible or overshadowed with respect to the lighting source, so the correct colour can be applied. At this point, the GPU also offers to perform clipping and an early [Z-test]({{% ref) (the NV2A compresses the Z-buffer four times its original size to save bandwidth, contributing to a lot of performance improvements).

The resulting pixels are stored in a **set of shared registers** and then cycled through **8 register combiners**, where each one applies arithmetic operations on them. This process is programmable with the use of **pixel shaders** (another type of program). At each cycle, each combiner receives RGBA values (RGB + Alpha) from the register set. Then, based on the operation set by the shader, it will operate the values and write back the result. Finally, a larger amount of values are sent to the **final combiner** which can exclusively blend specular colours and/or fog.

Register combiners are programmable in a similar nature to the [Texture Environment Unit]({{% ref). That is, by altering its registers with a specific combination of settings. In the case of the Xbox, the PFIFO reads pushbuffers to set up PGRAPH, which includes the register combiners and texture shaders.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Post-processing" >}}
{{< float_block >}}
  {{< linked_img src="pipeline/PostProcessing.png" alt="Xbox Live" >}} <figcaption class="caption">Post-processing stage</figcaption>
{{< /float_block >}}
{{% inner_markdown %}}

Before the pixels are written to the frame-buffer, the NV2A contains four dedicated engines called **Raster Output Unit** or 'ROP' which perform necessary tests (alpha, depth and stencil) using allocated blocks in main memory. Finally, batches of pixels (four per cycle) are written back only if they passed these tests.

Moreover, the frame-buffer can be antialiased using a technique called **multisampling**. Essentially, this technique samples edges of polygons multiple times with different offsets added in the process. Afterwards, all samples are averaged and to form the antialiased image. This approach replaced the previous (and more resource-hungry) anti-aliasing function called 'supersampling', used by previous Nvidia GPUs.

{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

#### Importance of programmability

I find important to emphasise the significance of the new programmability model that Nvidia provided to developers. Years ago, most of the graphics pipeline was computed by the CPU, leaving the GPU to accelerate rasterising operations. With the introduction of 'shaders' (referring to both pixel shaders and vertex programs), programmers can take advantage of the resources of the GPU to accelerate many computations in the pipeline, offloading a great amount of work from the CPU.

The concept of 'shaders' was introduced by **Pixar** in 1989 as a method to extend **Renderman**, their pioneering software used for 3D rendering. This was back in the time when 3D graphics were mainly handled by industrial equipment, later on, we have seen how certain consoles incorporated [similar principles]({{% ref), but it wasn't until Nvidia released their GeForce3 line, that shaders became a standard in the consumer market.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
      {{< tab name="Vertex Program" active="true" >}}
        {{< video src="vertex" loop=true >}} <figcaption class="caption">Complex animation achieved by vertex program <br>Chameleon (2001), a demo developed by Nvidia to showcase GeForce3's shaders</figcaption>
      {{< /tab >}}
      {{< tab name="Pixel Shader" >}}
        {{< video src="fragment" >}} <figcaption class="caption">Different texture effects achieved by pixel shaders <br>Chameleon (2001), a demo developed by Nvidia to showcase GeForce3's shaders</figcaption>
      {{< /tab >}}
    {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Thanks to vertex programs, the GPU can now accelerate model transformations, lighting calculations and texture coordinate generation. The latter one is essential for composing [Higher Order surfaces]({{% ref). With this, the CPU can concentrate on providing better physics, AI and scene management.

In the case of the pixel shaders, programmers can manipulate and blend textures in multiple ways to achieve different effects such as multi-texturing, specular mapping, bump mapping, environment mapping and so on.

A new programming concept that emerges thanks to this approach is the **General Purpose GPU** or 'GPGPU', which consists in assigning tasks to the GPU that would have been exclusively done by the CPU. So not only the GPU has taken over most of the graphics pipeline, but now can act as an efficient co-processor for specialised computations (i.e. physics calculations). This is a new area that will evolve as GPUs become more powerful and flexible, however the NV2A was already able to achieve this thanks to a combination of hardware capabilities (vertex & pixel shaders) and specialised APIs developed (OpenGL's 'state programs').

{{% /inner_markdown %}}

{{< /float_group >}}

I have a feeling that shaders will be regularly revisited in future articles. Please remember that in this article, however, they may be considered a bit 'primitive' and some people may argue that the pixel shaders are not even 'shaders', compared to what GPU offers nowadays.

#### The Xbox's frame

The standard resolution of games is **640x480**, this is pretty much the standard in the sixth generation. Although, this constraint is just a number: The GPU can draw frame-buffers with up to 4096x4096, yet it doesn't mean the hardware would provide acceptable performance. On the other side, the console allows to alter screen settings globally, which may help to promote these unique features (widescreen and 'high resolution') instead of waiting for developers to discover them (as it happened with the [Gamecube/Wii]({{% ref)).

The video encoder, on the other hand, will try to broadcast whatever there is on the frame-buffer in a format your TV will understand. That means that widescreen images will become [anamorphic]({{% ref) unless the game outputs in HD (i.e. 720p or 1080i, which only a few games do).

That being said, what kind of signals does this console broadcast? Quite a lot. Apart from the typical PAL/NTSC composite, the Xbox offers **YPbPr** (requiring an extra accessory to get the 'component' connectors) and RGB (both for SCART and VGA compliant). All in all, very convenient without requiring expensive adapters and whatnot.

---

## Audio

The audio subsystem of this console was heavily influenced by the technology of professional audio equipment and ATX motherboards, although it does contain some peculiar parts. The MCPX includes two audio components, the **Audio Processing Unit** and the **Audio Controller Interface'**.

The Audio Processing Unit or 'APU' is the dedicated audio processor and composed of three sub-components:
- The **Voice Processor** or 'VP': A specialised circuit that can synthesise 256 voices at a sampling rate of 48 KHz. It also includes two DAHDSR envelope controls and various filters. Furthermore, 64 voices can be 3D (as opposed to Stereo or Mono). At the end, the processor mixes and outputs the voices through 32 channels, where groups of 8 channels have their own volume control.
- The **Global Processor** or 'GP': A programmable DSP used to process the audio data from the VP and apply various effects on it.
  - Not all the channels coming from the VP have to be fed here, instead, specific groups can be sent to apply specific effects (i.e reverb) exclusively on them.
- The **Encode Processor** or 'EP': As the name indicates, it generates the final stereo signal coming from the GP and stores it in main RAM.
  - Notice the operations of the EP can also be carried out by the GP, but having a separate component for encoding enables the console to strive for better audio encoding techniques, such as Dolby Digital.

The APU only processes audio data but can't output it. The latter is the job of the ACI, which reads the audio data in RAM, decodes it and sends the resulting PCM stereo sample (16-bit resolution with a sampling rate of 48 KHz) through the audio output.

---

## I/O

As I mentioned before, we have a 'Southbridge' subsystem which concentrates all I/O access.

The MCPX derives from its PC counterpart called **nVidia nForce Multimedia and Communications Processor** or 'MCP'. This is found on motherboards using the nForce 220/415/420 chipset.

#### External interfaces

The console includes the following external connectors:
- **4 USB 1.1 ports**: Used to connect the controllers, however, the external shape of the port is modified to only allow Xbox controllers.
  - These ports also contain an extra pin called 'Video Sync' to connect peripherals that interact with the screen.
- **10/100BASE-TX Ethernet port**: Used for online services (more details later). The actual ethernet function is performed by a separate transceiver found in the motherboard.

#### Internal interfaces

The MCPX also provides the following interfaces and protocols used to interconnect different subsystems:
- **SMBus**: Also referred as I<sup>2</sup>C, it's a serial interface that connects these components:
  - **System Management Controller** or 'SMC': Manages multiple services, such as power, temperature and fan control. It's actually a PIC16LC microcontroller.
  - **System Temperature Monitor** or 'STM': A digital thermometer (ADM1032) used by the SMC to detect overheating.
  - A **256 B EEPROM**: A re-writable ROM that stores unique identifiers (serial number, region, ethernet MAC address, etc).
  - **Video Encoder**: The encoder is primarily connected to the GPU but controlled through the SMBus.
- **IDE Controller**: This is a standard protocol widely used on PCs to communicate with Hard drives, optical readers and so forth. A single wide ribbon cable is used to connect the motherboard with the DVD drive and the HDD.
- **Low Pin Count** or 'LPC' bus: This is another interface borrowed from the PC, but instead of connecting the *good old* PC BIOS, it communicates with a Flash ROM, which in turn stores the equivalent of a BIOS. The Flash is 1 MiB large.

#### The controller

The Xbox came with a bulky controller called **The Duke**, its set of inputs aren't any different from what the other competitors had... except from the usage of analogue circuitry (8-bit wide) on the face buttons, allowing games to detect 'half-presses' from most of the button set. On the other side, the Duke was so widely criticised that Microsoft replaced it with a new revision called **Controller S** months after the release of the console.

{{< side_by_side >}}
  <div class="toleft">
    {{< tabs nested="true" >}}
      {{< tab name="Front" active="true" >}}
        {{< linked_img src="controller/duke_front.png" alt="Front of Duke controller" >}}
      {{< /tab >}}
      {{< tab name="Back" >}}
        {{< linked_img src="controller/duke_back.png" alt="Back of Duke controller" >}}
      {{< /tab >}}
    {{< /tabs >}}
    <figcaption class="caption">The Duke (2001)</figcaption>
  </div>
  <div class="toright">
    {{< tabs nested="true" >}}
      {{< tab name="Front" active="true" >}}
        {{< linked_img src="controller/s_front.png" alt="Front of S controller" >}}
      {{< /tab >}}
      {{< tab name="Back" >}}
        {{< linked_img src="controller/s_back.png" alt="Back of S controller" >}}
      {{< /tab >}}
    {{< /tabs >}}
    <figcaption class="caption">Controller S (2002)</figcaption>
  </div>
{{< /side_by_side >}}

At closer inspection, both controllers did include something special: Two **Memory Unit** slots to plug in a proprietary memory card, enabling to share saves between consoles. I assumed this feature was inherited from a [previous competitor]({{% ref). Days after publishing this article, I tried to send a link of it to Seamus Blackley, the co-creator of this console, who quickly replied me with very interesting comments. Regarding the Dreamcast similarities, he told me:

> The relationship to Dreamcast is just historical bias. That was accidental.
> 
> -- <cite>Seamus Blackley</cite>

#### A required adapter

Another interesting fact to mention about these controllers is that they can only be plugged in using a shape adapter referred as **breakaway dongle**, this was designed in an effort to prevent accidents when someone tripped over.

> I think they also planned for where an Xbox was on a high shelf and yanking the controller would pull the whole heavy console down on some kid’s head.
> 
> -- <cite>Xbox user</cite>

---

## Operating System

All right, let's start by addressing the elephant in the room.

> Does it run **Windows**?

I'm afraid this is a *yes and no* answer: There is a 'Windows' present in this console, but not in the form conventional PC users would expect.

#### Boot Process

As with Pentium machines, upon booting up the system, the CPU will start executing instructions found at the **reset vector** (address `0xFFFF.FFF0`). For the Xbox, this address points to a hidden ROM found in the MCPX (more details later). The MCPX contains routines to initialise the security system and continue booting from the Flash ROM instead. Inside the Flash ROM, the Xbox will initialise its hardware, boot a small kernel (based on Windows NT's kernel) and show the splash animation.

During security initialisation, the CPU turns into **protected mode**. This is critical since x86 CPUs always start-up in **real mode** to maintain compatibility with the first processor (the Intel 8086), however, if programmers need to access the modern features of the CPU (such as being able to access more than 1 MiB of memory), they have to manually enable them by switching to protected mode.

When the kernel loads, it injects microcode into the CPU (not to program it, but rather to *update it*). Finally, the kernel looks for the presence of a valid DVD disc. If there is one, it runs it. Otherwise, it will load a user-interactive shell stored in the Hard Drive.

#### The green screen

Let's take a look now at the program that the Xbox loads when there isn't a game disc inserted: The **Dashboard**.

{{< tabs >}}

{{< tab name="Interactive shell" active="true" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Home" active="true" >}}
      {{< linked_img src="shell/home.png" >}}
    {{< /tab >}}
    {{< tab name="Settings" >}}
      {{< linked_img src="shell/settings.png" >}}
    {{< /tab >}}
  {{< /tabs >}} <figcaption class="caption">The Dashboards offers multiple services</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The dashboard is not very different in terms of functionality compared to the [}}#interactive-shell">Playstation menu]({{< ref), or the [}}#splash-and-shell">Gamecube's IPL]({{< ref). It essentially includes all the functions typical users would expect, like being able to tweak some settings, moving saves around, playing DVD movies or CD audio; and so forth.

One thing worth mentioning is that the Dashboard also allowed to rip music from an audio CD and store it in the HDD. This music could be subsequently fetched from any game to 'personalise' its soundtrack. *Fun stuff!*
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Updatability" >}}
{{% inner_markdown %}}
Well yes, this is the first console of its generation to be *officially* updatable, but only the contents of the HDD are writable. The kernel part (in the Flash ROM) can't be re-written, but the system can patch it after it's loaded in memory. Kernel patches are therefore stored in the HDD.

Updates are distributed through retail games and/or downloaded by the system after connecting to the Xbox Live network service (more about it later on).

Some updates enhanced the functionality of the system. For instance, the first Xbox units didn't include Xbox Live, so games embedded the required files in the form of an update to install that service. Other updates focused on fixing security vulnerabilities.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

---

## Games

This part may be a bit confusing, mainly due to the lack of low-level documentation compared to other consoles. It seemed that Microsoft/Nvidia really wanted to use their libraries and forget about everything else. It's an interesting strategy nonetheless.

In any case, I tried to document it as informative as possible, without going into too much depth.

#### Development ecosystem

Game development in this console is very complex in terms of libraries, terminology and so forth. So I have separated it by frameworks.

{{< tabs >}}

{{< tab name="Hardware Abstraction" active="true" >}}
{{< float_block >}}
  {{< linked_img src="hal/general.png" alt="HAL" >}} <figcaption class="caption">Representation of HAL</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
We have seen how elements like a 'programmable coprocessor with microcode' tend to come with a lot of fanfare at first, but slowly dissipates once developers discover the real complexity of the new hardware.

From the developer perspective, I think one of the selling points of this console was the inclusion of popular high-level libraries that could efficiently manage low-level functionality. Microsoft and Nvidia's strategy consisted in, instead of documenting the every low-level aspect of their hardware, they just provided a fully-functional hardware abstraction library that could perform the operations developers would expect to find without requiring complete knowledge of the inner workings of the hardware.

There are multiple SDKs available to develop for this console, some 'official' and restrictive, while others 'not-so-official' but flexible. We'll take a look at the most popular ones.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Microsoft XDK" >}}
{{< float_block >}}
  {{< linked_img src="hal/xdk.png" alt="Vertex stage" >}} <figcaption class="caption">XDK structure</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Microsoft's Xbox Development Kit or 'XDK' is the official SDK used for Xbox development. It's a package that contains many tools, libraries and compilers. Most notably, it's used alongside Visual Studio .NET (2002 version) which was *quite an IDE* from that time, for better or worse.

The graphics library is a customised implementation of **Direct3D 8.0**, which was extended to include Xbox-specific features. Direct3D was a very powerful API used to develop code compatible with many GPUs. With this, PC developers have the opportunity to port their PC games without too many changes (in theory!). Although, the syntax used for vertex programs and pixel shaders reassembles downright assembly.

Apart from that, audio functionality is provided by **DirectSound** which takes care of the APU without the need to worry about moving audio data around. There are some network libraries as well, which are very convenient for achieving online functionality (i.e. multiplayer). The network API relies on **Windows Sockets**, which was Microsoft's attempt to simplify network communications using a Windows-only protocol (albeit having a design based on its standardised counterpart, BSD sockets).

To try all of this out, Microsoft distributed their own development kit hardware which is just a retail unit tinted in green with double the RAM installed (128 MiB in total) and, sometimes, a different MCPX chip (called MCPX-2, which directly boots from the Flash ROM). It contains an enhanced version of the dashboard which launches executables signed by the XDK. On the other end, the **Xbox Neighbourhood** (a Windows application) was used to link the dev kit with Visual Studio, enabling easy deployment and debugging. To experiment with Xbox Live, Microsoft provided sandboxed servers as well.

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="NXDK" >}}

{{< float_block >}}
  {{< linked_img src="hal/nxdk.png" alt="NXDK" >}} <figcaption class="caption">NXDK structure <br>Notice that even though some low-level libraries are wrapped with high-level ones, they are still accessible to the developer</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
In a effort to avoid Copyright litigation to Homebrew developers using the official SDK. A group of developers unaffiliated with Microsoft created a replacement of the official SDK called **Open XDK**. After some years, its development stopped, so another group picked it up from there and named the new fork **New XDK** or 'NXDK'.

The ongoing project reverse-engineered the low-level hardware components (microcode, push-buffers, etc) and designed a new API in C/C++ which provides high-level calls to each portion of the system.

To simplify the graphics layer without relying on Direct3D, the NXDK currently uses the **CG** compiler. CG is a language created by Nvidia for developing shaders. CG is chained with other compilers to generate Xbox-compatible code. During compilation, CG code is converted into NV20-compatible assembly then, this is translated again using a custom compiler to generate NV2A-compatible microcode and pushbuffer. For those who don't want to use CG, NXDK also exposes the rest of the compilers to write low-level shaders.

The rest of the APIs available handle other services (audio, networking, etc). All in all, this library gives more control of the hardware (as opposed to the official SDK) at the exchange of not using Microsoft's APIs (the de-facto standard, albeit completely proprietary). However, the NXDK remains as the most adequate option for developing legal Homebrew programs.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

#### Medium

Game are distributed on dual-layer DVD discs (up to 8.5 GB!), they are subsequently read by a customised DVD drive which includes anti-piracy protections (despite using a standard interface, ATA). It's worth mentioning that the XDK included some tools to customise the layout of data in the disc, enabling programmers to improve read speeds!

Now, the console also includes an internal 8 GB HDD, games use it to store saves or cache temporary content. The system, on the other hand, stores the dashboard, Xbox Live settings and network settings.

#### Network service

Forget about [}}#online-platform">modems]({{< ref) or [}}#network-service">experimental services]({{< ref). The Xbox included everything that nowadays we take for granted to provide a decent online service: Ethernet connection and a centralised online infrastructure (called **Xbox Live**).

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="shell/live.png" alt="Xbox Live" >}} <figcaption class="caption">Xbox Live Logo</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Furthermore, not only Xbox Live enabled online multiplayer but it also included other features like audio streaming for realtime voice chat.

But what exactly is Xbox Live? Well, it's just a collection of interconnected online services which companies can use to build their online platform. For instance, one of the services provide user profiles, so studios can use it an authentication method when accessing the online functionalities of a game. In the official SDK, Microsoft includes some APIs to talk with the Xbox Live servers.

It's important to point out that Microsoft controls who to grant Xbox Live access to, so developers will have to register to Microsoft in order to obtain the authentication keys that will be used by their games.
{{% /inner_markdown %}}

{{< /float_group >}}

The real online experience happens in the **Title Server**, which is the type of server that answers to clients (xbox consoles) around the world and handles realtime communication. Microsoft included in their SDK some samples to show how to build these servers, although they relied on Windows systems and were meant to be deployed in data centres running Windows Server.

#### The start of a new trend

After analysing Microsoft's implementation of Xbox Live and looking at the impact it had in the industry. It now sounds pretty obvious, right? As if the recipe for 'proper online gaming' (a.k.a Ethernet + Infrastructure) was always there, but not every company wanted to invest in it.

It turns it's not that simple: Microsoft also had to convince users they 'needed' this functionality, that online multiplayer wasn't just an optional addition, but a fundamental part of some games. Otherwise, Microsoft's efforts would just account as another 'online attempt'.

> Try imagining a world where no console gamer wanted online games, and where nobody believed a PC architecture could be a console. That was really how it was. Now it seems obvious BUT IT WASN'T.
> 
> -- <cite>Seamus Blackley</cite>

---

## Anti-Piracy and Homebrew

Independently whether this console contains off-the-shelf components or not, there's a fair amount of security measures implemented.

Please note that RSA encryption is a recurring topic here, I previously introduced it in the [}}#tab-7-2-chain-of-trust">Wii article]({{< ref), so if you are not familiar with RSA or any other symmetric/asymmetric encryption systems please take a look at that article first.

That being said, let's take a look.

#### DVD copy protection

Game discs are protected both logically and physically to prevent unauthorised duplication (this shouldn't be a surprise by now).

On the logical side, game discs include a couple of 'traps' to trick conventional DVD readers so they can't find the actual game content. For instance, the first area of the disc is formatted like a conventional DVD, and inside that section there's a warning message that plays if it's inserted in a non-Xbox system. In reality, game data is found on a second partition, but the metadata required to find that partition is not encoded in a format conventional DVD readers expect, so only a specialised reader (hence the Xbox's one) will find the game.

On the physical side, I'm afraid that at this time, it's not publicly known yet what data does the driver and the disc exchange to perform validation. The disc contains an inaccessible inner ring (by conventional readers) which stores unique identifiers, but it's not known how this data is used.

#### System protection

Let us go over the rest of the security measures that this system originally implemented.

{{< tabs >}}

{{< tab name="Introduction" active="true" >}}
{{% inner_markdown %}}
The Flash ROM and EEPROM containing the 'BIOS' and sensible data, respectively, are encrypted using a **RC-4 key**. After the kernel is booted from the BIOS, it will only launch executables signed by Microsoft using **RSA encryption**.

The HDD, on the other side, is formatted with a completely proprietary and undocumented filesystem called **FATX**.

This was a brief introduction of the chain of trust that Microsoft implemented. Seems pretty simple right? Well, **something's odd**: The execution is controlled by the CPU, but this is an off-the-shelf chip, so how does it understand data encrypted with RC-4? The answer is that **it doesn't**, so somewhere in this console is an unencrypted piece of code that sets up the first stage of encryption. This code, in particular, was the target for most hackers that strived to crack this console after it launched.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Bootstrap search" >}}
{{% inner_markdown %}}

Since it's officially documented by Intel that their processors will start execution at address `0xFFFF.FFF0`, it was only need to find that code in the Flash ROM (which in turn will lead to the RC-4 key). This is what a popular hacker and researcher named **Andrew 'bunnie' Wang** attempted during his academic research. The findings were quite interesting: The upper 512 bytes of the Flash ROM contains security routines including the key, but it doesn't work for the retail system. I speculate that Microsoft may have left that code from prototype/debug units (possibly accidental, since this block exposes the algorithms that Microsoft applied). In conclusion, this was considered *garbage code* so bunnie realised the real `0xFFFF.FFF0` wasn't in the Flash ROM.

To make a long story short, `0xFFFF.FFF0` was **hidden in the MCPX chip**: It contained a hidden 512 B ROM that would be executed once and hidden afterwards. This ROM was not easy to extract, so bunnie resorted to tapping the HyperTransport bus to catch the RC-4 key once it was transmitted.

And so it happened, bunnie published the key as part of his research and Microsoft was not happy about this. Thus, the hacking community found a way to gain control of the first security layer of this console, which included the kernel.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Permanent unlock" >}}
{{% inner_markdown %}}

Cracking the RSA layer was never meant to be easy, but since hackers gained access to the kernel, it would now be possible to reverse engineer it and develop patches that could nullify RSA all-together. Lo and behold, this eventually happened and opened the door to the Homebrew community. Anyone could now develop programs that could be executed on a modified Xbox without the approval of Microsoft. Some groups developed replacements for the original Dashboard which could do more functions, such as executing Linux!

This, however, required users to modify their BIOS using specialised hardware, which not everyone could do. In later years, new exploits were discovered that could easily bootstrap the main hack, one of them consisted in inserting a forged save-game of '007: Agent Under Fire' or 'Splinter Cell' that would generate a buffer overflow to kickstart a homebrew tool that could install a permanent exploit in the hard disk. The 'permanent exploit' was possible because the original Dashboard was subject to yet-another buffer overflow using a forged font file (note that fonts didn't need to be signed).

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Modchips" >}}
{{% inner_markdown %}}

From the piracy side of things, **Modchips** also appeared: Instead of tampering with the DVD drive, modchips overlapped the Flash ROM by exploiting the fact that the MCPX ROM could boot a secondary BIOS if it was found on an unused LPC port.

The substitute BIOS contained patched routines that could enable reading any type of game disc (specially the conventional ones).

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="The response" >}}
{{% inner_markdown %}}

Microsoft released many hardware revisions reducing the amount of exposure in their electronics and saving up costs. Meanwhile, software updates were released that updated both the Kernel and the Dashboard in an effort to reduce the number of vulnerabilities. However, some game exploits still managed to persist and what remained was another 'cat-and-mouse' game.

Another point to mention is that Xbox live was itself an effective prevention mechanism against piracy. Microsoft was able to block Xbox Live to those consoles with unauthorised modifications, which was a tradeoff that typical users had to consider before hacking their consoles with the only goal of playing pirated copies.

{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

---

## That's all folks

After a couple of months with deadlines and exams in the middle, the next article has finally been finished. I admit this one continues the trend of adding *too much* information and trivias, but while this research started with small (and frustrating) steps, I'm glad I found a lot of support from one special community, XboxDev, that helped me gather lots of information.

For anyone who would like to know more about this console, XboxDev is actively working on nxdk (along with different emulators) which strive to do things that were previously considered impossible in Xbox homebrew, so I suggest visiting their community for more information.

From my side, I'm going to take a few days to think carefully about the *next article* (and potentially go back a couple of generations to analyse a console I forgot about).

Until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- Huang, Andrew. [**Hacking the Xbox: an Introduction to Reverse Engineering**](https://bunniefoo.com/nostarch/HackingTheXbox_Free.pdf). No Starch Press, 2003.
- XboxDev organisation, [**The XboxDevWiki**](https://xboxdevwiki.net/Main_Page)
- Khronos Group. [**OpenGL Wiki**](https://www.khronos.org/opengl/wiki/)

#### CPU

- Anand Lal Shimpi. [**Intel Pentium III E "Coppermine" (Slot-1)**](https://www.anandtech.com/show/399).
- cpu-world.com, [**Intel Pentium III Coppermine core**](http://www.cpu-world.com/Cores/Coppermine.html).
- Thompson, R. and Thompson, B.. **PC Hardware In A Nutshell**. Sebastopol, O'Reilly, 2003.

#### Graphics

- Medvedev, Alexander. [**NVIDIA NV20 GPU in-depth features analysis**](http://ixbtlabs.com/articles/nv20/index.html).
- IGN, 1999, [**3Dfx Touts Market Share**](https://www.ign.com/articles/1999/02/03/3dfx-touts-market-share).
- Jordan, Vincent. [**NVIDIA GPU architecture**](https://vjordan.info/thesis/nvidia_gpu_archi/index.xhtml).
- Vorobiev, Andrey and Medvedev, Alexander. [**NVIDIA GeForce3 review**](http://ixbtlabs.com/articles/gf3/index.html).
- Chian, Kert. [**NVIDIA GeForce3 Investigation: What NVIDIA didn't tell us**](https://www.anandtech.com/show/767).
- Fernando, Randy. [**Programming the GPU: High-Level Shading Languages**](http://developer.download.nvidia.com/assets/gamedev/docs/EG_04_ProgrammingLanguages.pdf). Nvidia Corporation.
- Kirk, David. [**GeForce3 Architecture Overview**](http://developer.download.nvidia.com/assets/gamedev/docs/GF3ArchitectureOverview.pdf).
- Pixar, 1989, [**The RenderMan Interface Specification, Version 3.1**](https://web.archive.org/web/20161022001300/http://www.redrabbit-studios.com/coursework/renderman/prman/RISpec/index.html) (Archived)
- Wasson, Scott. [**NVIDIA’s GeForce3 graphics processor**](https://techreport.com/review/2515/nvidias-geforce3-graphics-processor/).
- Koppelman, David M. [**Case Study: NVIDIA GeForce 3 Series**](https://www.ece.lsu.edu/koppel/gp/notes/set-study-gf3.pdf).
- Kilgard, Mark J.. [**Specification of new OpenGL extension 'NV_vertex_program'**](https://www.khronos.org/registry/OpenGL/extensions/NV/NV_vertex_program.txt). Khronos.
- [**Nvidia Hardware Introduction**](https://people.freedesktop.org/~mslusarz/nouveau-wiki-dump/HwIntroduction.html)
- Domine, Sebastien and Spitzer, John. [**Texture Shaders**](http://developer.download.nvidia.com/assets/gamedev/docs/TextureShaders.pdf). Nvidia Corporation.
- Spitzer, John. [**Programmable Texture Blending**](http://developer.download.nvidia.com/assets/gamedev/docs/ProgrammableTextureBlending.pdf). Nvidia Corporation.

#### I/O

- ReactOS. [**Xbox Port Analysis**](https://reactos.org/wiki/Xbox_Port)

#### Games

- Github, [**Official Repository of the NXDK**](https://github.com/XboxDev/nxdk)
- [**XboxDev Repository**](https://github.com/XboxDev/XboxDev)

#### Anti-Piracy

- The Pheonix Project. [**Technical analyses of Free-X’s “Bert & Ernie” exploit**](http://archiv.sega-dc.de/phoenix.maxconsole.net/docs/berternie.inc.htm)

#### Photography

- Console and motherboard: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Controller Icon: [**Dimensions.Guide**](https://www.dimensions.guide/element/xbox-controller)
- Halo Screenshot: **XboxDev**
- Diagrams and rest of screenshots/videos: **Me**