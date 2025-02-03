---
short_title: Nintendo 64 Architecture
long_title: Architecture of the Nintendo 64
name: Nintendo 64
release_date: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: A constrained performant
cover: nintendo64
javascript: ['threejs']
seo_image_pos: "Top"
top_tabs:
  Model:
    caption: "The Nintendo 64.<br>Released on 23/06/1996 in Japan, 29/09/1996 in America and 01/03/1997 in Europe"
    file: "international"
  Motherboard:
    caption: "Showing revision 'NUS-CPU-03'.<br>Later ones reduced the number of chips required for AV encoding.<br>Disk Drive connector is found on the back"
  Diagram: {}

# Historical
aliases: [/projects/consoles/nintendo-64/]
---

## A quick introduction

Nintendo's goal was to give players the *best* graphics possible, for this it will partner with one of the biggest players in computer graphics to produce the *ultimate* graphics chip.

The result was a nice-looking console for the family... and a 500-page manual for the developer.

Don't worry, I promise you this article will not be *that* long... Enjoy!

## {.supporting-imagery}

## CPU

The origins of the Nintendo 64's main processor begin with the **MIPS R4000**, [MIPS](playstation#tab-1-2-mips-and-sony)' new avant-garde CPU. Released in 1991, the R4000's most apparent novelty was the inclusion of **64-bit capabilities**, resulting from widening the size of buses, registers and computation units to manipulate 64-bit values with efficiency. Developers, on the other hand, accessed these capabilities through the new **MIPS III** instruction set. All in all, the R4000 enabled new applications to manipulate larger chunks of data without consuming extra cycles.

For their next-generation console, Nintendo looked into bringing industrial hardware to home consoles. Unlike [Sony](playstation#cpu), who owned plenty of in-house components and only needed a MIPS second-source, Nintendo directly partnered with the owners of MIPS (and many graphics workstations) to design their entire ecosystem. That company was **Silicon Graphics** (SGI).

Back at SGI's headquarters, the R4000 was an expensive product (around $400 [@cpu-r4000demo]), which made it unfeasible for a video game console. Yet, Nintendo didn't want to give up on its state-of-the-art offerings, so they went for a low-end variant called **R4300i**, from which NEC was able to second-source it.

In the end, Nintendo and SGI's CPU of choice became the **NEC VR4300** running at **93.75 MHz** [@cpu-anatomy]. This is a binary-compatible version of the MIPS R4300i that features [@cpu-nec]:

- **Two modes of operation**:
  - **32-bit mode**: Traditional mode where the CPU behaves as a MIPS II-compatible processor. There's nothing special about this mode except that all new functions are locked out.
  - **64-bit mode**: 'Native' mode where all 64-bit extensions are available. It's also binary-compatible with 32-bit applications.
- **32 general-purpose registers**: These are 32-bit wide in '32-bit mode' and 64-bit wide in '64-bit mode'.
- The **MIPS III ISA**: A RISC instruction set that succeeds MIPS II. It features new opcodes that compute 64-bit words called 'doublewords'. Finally, instructions are always **32-bit long**, independently of the mode.
  - It's worth mentioning that since MIPS II, [load delay slots](playstation#delay-galore) are gone for good, though branch delay ones still persist.
- An internal **64-bit bus** connected to an **external 32-bit data bus**: While doublewords won't degrade performance when operated internally, the CPU will still need to expend extra cycles to move 64-bit data throughout the system.
  - This is one of the cutbacks of the R4300i variant (the R4000 has a full 64-bit data bus).
- **32-bit address bus**: Up to 4 GB of physical memory can be addressed.
- **5-stage pipeline**: Up to five instructions can be allocated for execution (a detailed explanation can be found in a [previous article](sega-saturn#cpu)).
- **24 KB L1 cache**: Divided into 16 KB for instructions and 8 KB for data.

An internal **Floating-point Unit** (FPU) is also included in this package. The VR4300 identifies it as a co-processor (CP1), however, the unit is fitted next to the ALU and it's only accessed through the CPU's internal ALU pipeline, meaning there's no co-processing per se. On the other side, the FPU still houses a dedicated register file and will speed up operations with 64-bit and 32-bit floating-point numbers. Finally, this unit follows the IEEE754 standard.

### Simplified memory access

The way RAM is assembled follows the **unified-memory architecture** or 'UMA' where all available RAM is centralised in one place only and any component that requires RAM will access this shared location. The component arbitrating its access is, in this case, the GPU.

The reason for choosing this design comes from the fact that it saves a considerable amount of production costs while, on the other side, it increments access contention if not managed properly.

### No DMA controller?

Due to the unified memory architecture, the CPU no longer has direct access to RAM, so the GPU will be providing DMA functionality as well.

### Memory design

Apart from the UMA, the structure of RAM is a little bit complicated, so I'll try to keep it simple. Here it goes...

The system physically contains **4.5 MB of RAM**, however, it's connected using a **9-bit** data bus where the 9th bit is reserved for the GPU (I explain more in the 'Graphics' section). As a consequence, every component except the GPU will only find **up to 4 MB**.

![Memory layout of this system. I presume the CPU-RCP bus speed is either the RCP's clock speed or the CPU one, but I haven't been able to confirm that, yet.](memory.png)

The type of RAM fitted in the board is called **Rambus DRAM** (RDRAM) [@cpu-memory], this was just another design that competed against SDRAM to become the next standard. RDRAM is connected in **serial** (where transfers are done one bit at a time) while SDRAM uses a **parallel connection** (transfers multiple bits at a time).

#### Latency and speed

RDRAM latency is directly proportional to the number of banks installed [@cpu-rdram] so in this case, with the amount of RAM this system has, the resulting latency is significant (it's been reported to lie around the **640 ns** [@cpu-beyondrsp]). This is compensated with a high clock speed of **250 MHz** (~2.6 times faster than the CPU) applied on the memory banks. Nintendo claimed RDRAM can provide high-speed data transfer of 500 MB/sec to read or write consecutive data.

Furthermore, Nintendo chose NEC's uPD488170L memory banks for their motherboard [@cpu-beyondrdram]. These chips implement a technology called 'Rambus Signaling Logic', which doubles the transfer rate [@cpu-data]. This may explain why some sources state the 'effective' rate of memory is 500 MHz.

#### Leaving room for improvement

Interestingly, the amount of available RAM on this console **can be expanded** by installing the **Expansion Pak** accessory: A fancy-looking small box that adds another **4.5 MB**. While this accessory remained optional for some games (and the majority didn't make use of it), certain titles like Donkey Kong 64 or The Legend of Zelda: Majora's Mask were designed with the expansion as a requirement, and would display an error screen without it.

![The Expansion Pak [@photography-amos], an optional accessory sold separately (sometimes included with the game that requires it).](expansion_pak.png){.tabs-nested .active title="Expansion"}

![The Jumper Pak [@photography-amos]. In the absence of the Expansion Pak, this must be present to terminate the RDRAM bus.](jumper_pak.png){.tabs-nested-last title="Placeholder"}

Curiously enough, the RAM bus must be terminated, so the console always shipped with a terminator (called **Jumper Pak**) fitted in the place of the Expansion Pak. Now, you may ask, what would happen if you switched on the console without any *Pak* installed? **Literally nothing**, you get a blank screen!

### Memory management

The VR4300 includes another coprocessor called **System Control Coprocessor** (CP0) which is composed of a **Memory Management Unit** (MMU) and a **Translation Lookaside Buffer** (TLB), the former handles how memory is organised and cached. The VR4300 can access up to 4 GB worth of 32-bit memory addresses, but as we've seen, we don't have 4 GB of RAM in this console (even after considering memory-mapped I/O). So, the MMU takes over memory addressing and provides a useful memory map where the physical memory is mirrored multiple times. Consequently, memory locations are treated as 'virtual addresses' (as opposed to 'physical addresses'). Furthermore, the TLB enables developers to define their own memory map in some mirrors without (significant) performance penalties.

At first, this may seem redundant, but each mirror (called 'segment') is connected to different circuitry (i.e. L1 cache, uncached, TLB address) so developers can optimise usage by selecting the most appropriate segment depending on the needs.

Some segments are meant to discriminate 'kernel' locations from 'user' ones for security purposes. The N64 always operates in 'kernel' mode, thus, the 'non-TLB kernel cached' segment (called 'KSEG0') is the most common one for games.

The MMU can also work in 64-bit mode, where memory addresses are 40-bit long. This means virtual address space covers 1 TB worth of addresses... but I don't think the Nintendo 64 will take advantage of this!

## Graphics

What you see on the screen is produced by a huge chip designed by Silicon Graphics called **Reality Co-Processor** and running at **62.5 MHz**. This package contains *a lot* of circuitry so don't worry if you find it difficult to follow, the graphics sub-system has a very complex architecture!

This design is based on the philosophy that the GPU is not meant to be a 'simple' rasteriser like the [competitor's](playstation#graphics). Instead, it should also be capable of **accelerating geometry calculations** (offloading the CPU), and for that, more circuitry will be needed.

### Architecture

This chip is divided into three main modules, two of which are used for graphics processing:

#### Reality Signal Processor {.tabs .active}

![Architecture of the Reality Signal Processor (RSP).](RSP.png){.tab-float}

Also known as **RSP**, it's just another CPU package composed of:

- The **Scalar Unit**: Another cut-down derivative of the MIPS R4000. This time, it only implements a subset of the MIPS III ISA, thereby lacking many general-purpose functions (i.e. interrupts and exceptions), the 64-bit extension, multiplication and divisions.
- The **Vector Unit**: A co-processor that performs vector operations with 32 128-bit registers. Each register is *sliced* into eight parts to operate eight 16-bit vectors at once (just like SIMD instructions on conventional CPUs). As you can see, this component does the heavy lifting for the Scalar Unit.
- The **System Control**: Another co-processor that provides DMA functionality and controls its neighbour module, the RDP (more about it later on).

To operate this module, the CPU stores in RAM a series of commands called **Display list** along with the data that will be manipulated, then the RSP reads the list and applies the required operations on it. The available features include geometry transformations (such as perspective projection), clipping and lighting.

This seems straightforward, but how does it perform these operations? Well, here's the interesting part: Unlike its competitors (PS1 and Saturn), **the geometry engine is not hard-wired**. Instead, the RSP contains some memory (4 KB for instructions and 4 KB for data) to store **microcode** [@audio_video-ultra]: A small program, with no more than 1000 instructions, that **implements the graphics pipeline**. In other words, it directs the Scalar Unit on how it should operate our graphics data. The microcode is fed by the CPU during runtime.

Nintendo provided different microcodes to choose from [@audio_video-microcodes] and, similarly to the [SNES' background modes](super-nintendo#graphics), each one balances the resources differently.

#### Reality Display Processor {.tab}

![Architecture of the Reality Display Processor (RDP).](RDP.png){.tab-float}

After the RSP finishes processing our polygon data, it will start sending **rasterisation commands** to the next module, the **RDP**, to draw the frame. These commands are either sent using a dedicated bus called **XBUS** or through main RAM.

The RDP is another processor (this time with fixed functionality) that includes multiple engines to rasterise vectors, map textures onto our polygons, mix colours and compose the new frame.

It can process either **triangles** or **rectangles** as primitives, the latter is useful for drawing sprites. The RDP's rasterisation pipeline contains the following blocks:

- A **Rasteriser**: Converts primitives (made of vertices) into pixels.
- A **Texture Unit**: Processes textures using 4 KB of dedicated memory (called 'TMEM') allowing up to eight tiles to be used for texturing. It can perform the following operations on them:
  - **Bilinear filtering**: Maps the selected 2D texture over the 3D shape and smooths it to avoid pixelated areas (caused by oversampling).
    - A 'complete' filter would require four points to carry out the interpolation, however, this console only uses three (**triangular interpolation**) resulting in some anomalies. Thus, certain textures will have to be 'adapted' beforehand.
  - **Mip-Mapping**: Automatically selects a scaled-down version of the texture depending on its **level of detail**. This avoids computing large textures that would be seen far away from the camera and prevents aliasing (product of undersampling).
    - If enabled, the RDP maps textures using **trilinear filtering** instead. This new algorithm will also interpolate between the mipmaps to soften sudden changes in the level of detail.
  - **Perspective correction**: The chosen algorithm for mapping textures onto triangles. Unlike [other inverse-mapping algorithms](playstation#graphics), this one takes into account the depth value of each primitive, achieving better results.
- A **Colour Combiner**: Mixes and interpolates multiple layers of colours (for instance, to apply shaders).
- A **Blender**: Mixes pixels against the current frame buffer to apply translucency, anti-aliasing, fog and dithering. It also performs z-buffering (more about it later on).
- A **Memory interface**: Used by the previous blocks to read and write the current frame buffer in RAM and/or fill TMEM.

The RDP provides four modes of functioning, each mode combines these blocks differently to optimise specific operations.

Since this module is constantly updating the frame buffer, it handles RAM very differently: Remember the unusual 9-bit 'byte'? The ninth bit is used for frame buffer-related calculations (z-buffering and antialiasing) and can only be operated through the Memory interface.

#### Remaining steps {.tabs-close}

The resulting frame must be sent to the **Video Encoder** to display it on-screen (**DMA** and the **Video Interface** component are essential to accomplish this).

The theoretical maximum capabilities are 24-bit colour depth (16.8 million colours) and 640x480 resolution (or 720x576 in the PAL region). I mention it as 'theoretical' since using the maximum capabilities can be resource-hungry, so programmers will tend to use lower stats to free up enough resources for other services.

### Quick demo

Let's put all the previous explanations into perspective, for that, I'll borrow Nintendo's *Super Mario 64* to show, in a nutshell, how a frame is composed:

#### Vertex Processing {.tabs .active}

![Primitive view of our scene. In order to save polygons, some characters are modelled using sprites (quads)](mario/wireframe.jpg){.tab-float}

Initially, our materials (3D models, etc) are located in the cartridge ROM, but to keep a steady bandwidth, we need to copy them to RAM first. In some cases, data may be found pre-compressed in the cartridge, so the CPU will need to decompress it before operating it.

Once that's done, it's time to build a scene using our models. The CPU could carry out the whole pipeline by itself but that may *take ages*, so many tasks are delegated to the RCP. The CPU will instead send orders to the RCP. This is done by carrying out these tasks:

1. Compose the Display List that contains the operations to be carried out by the RSP and store it in RAM.
2. Point the RSP where the display lists are.
3. Send microcode to the RSP to kickstart the Scalar Unit.

Afterwards, the RSP will start performing the first batch of tasks and the result will be sent to the RDP in the form of rasterisation commands.

#### Pixel processing {.tab}

![Rendered frame (_Tada!_).](mario/result.png){.tab-float}

So far, we managed to process our data and apply some effects to it, but we still need to:

- Rasterise vectors, apply textures and other effects.
- Display the frame buffer.

As you may guess, these tasks will be performed by the RDP. To make this work, textures must be copied from RAM into TMEM using DMA.

The RDP has a fixed pipeline but we can select the optimal mode of operation based on the current task to improve frame-rate.

Once the RDP finishes processing the data, it will then write the final bitmap to the frame buffer area in RAM. Afterwards, the CPU must transfer the new frame to the **Video Interface** (VI) preferably using DMA. The VI will, in turn, send it to the **Video Encoder** for display.

### Designs {.tabs-close}

Here are some examples of previous 2D characters for the [Super Nintendo](super-nintendo) that have been redesigned for the new 3D era, they are interactive so I encourage you to check them out!

![The Legend of Zelda: Ocarina of Time (1998).<br>704 triangles.](link_ocarina_64){.toleft model3d="true"}

![Kirby 64: The Crystal Shards (2000).<br>516 triangles.](kirby_cristals_64){.toright model3d="true"}

### Modern visible surface determination

If you've read about the previous consoles, you came across the never-ending problem regarding the [visibility of surfaces](sega-saturn#an-introduction-to-the-visibility-problem) and by now may think polygon sorting is the only way out of this. Well, for the first time in this series, the RDP features a hardware-based approach called **Z-buffering**. In a nutshell, the RDP allocates an extra buffer called **Z-Buffer** in memory. This has the dimensions of a frame buffer, but instead of storing RGB values, each entry contains the depth (Z-value) of the nearest pixel with respect to the camera.

After the RDP rasterises the vectors, the z-value of the new pixel is compared against the respective value in Z-buffer. If the new pixel contains a smaller z-value, it means the new pixel is positioned in front of the previous one, so it's applied onto the frame buffer and the z-buffer is also updated. Otherwise, the pixel is discarded.

Overall, this is a huge welcomed addition: Programmers do not need to worry anymore about implementing [software-based](playstation#tab-3-2-visibility-approach) polygon sorting methods which drain a lot of CPU resources. However, Z-buffer does not save you from feeding unnecessary geometry (discarded or overdrawn, both consuming resources). For this, game engines may choose to include an **occlusion culling** algorithm to discard unseen geometry as early as possible.

### Secrets and limitations

SGI clearly invested a lot of technology into this system. Nonetheless, this was a console meant for the household and as such, it had to keep its cost down. Some hard decisions resulted in difficult challenges for programmers:

#### Pipeline Stalls {.tabs .active}

Due to the huge number of components and operations in the graphics pipeline, the RCP ended up being very susceptible to **stalls**: An undesirable situation where sub-components keep idling for considerable periods because the required data is delayed at the back of the pipeline.

This will always result in performance degradation and is up to the programmer to avoid them. Although to make things easier, some CPUs such as the Scalar Unit implement a feature called **Bypassing** which enables to execute similar instructions at a faster rate by bypassing some execution stages that can be skipped.

For example, if we have to compute sequential `ADD` instructions, there's no need to write the result back to a register and then read it back every time each `ADD` is finished. We can instead keep using the same register for all additions and do the write-back once the last `ADD` is completed.

#### Texture memory {.tab}

The RDP relies on 4 KB of TMEM (Texture memory) as a single source to load textures. Unfortunately, in practice 4 KB happened to be insufficient for high-resolution textures. Furthermore, if mipmapping is used, the available amount of memory is then reduced to half.

As a result, some games used solid colours with Gouraud shading (like *Super Mario 64*) and others relied on pre-computed textures (for example, where multiple layers had to be mixed).

### The universal video out {.tabs-close}

Nintendo carried on using the 'universal' [Multi Out](super-nintendo.md#a-convenient-video-out) port found on its predecessor, bad news is that it **no longer carries the RGB signal!** It looks to me like another measure to save costs since RGB wasn't used anyway in the previous console.

The good news is that the three lines can still be reconstructed in the first revisions by soldering some cables and fitting an inexpensive signal amplifier. This is because the video digital-to-analogue converter transmits an RGB signal to the video encoder. However, later motherboard revisions combined both chips, so the only remaining option is to bypass the video DAC and encoder altogether with custom circuitry that exposes those signals.

## Audio

Before we go into the details, let's define the two endpoints of the N64's audio sub-system:

- Our starting point is the cartridge ROM, it contains data that only the CPU can interpret.
- The ending point is the **Digital-to-Analog converter** or 'DAC', which only understands *waveform data*.

Now, how do we connect both ends? Consoles normally include a dedicated audio chip that does the work for us. Unfortunately, the Nintendo 64 **doesn't have such dedicated chip**, so this task is distributed across these components:

- The **main CPU**: Transfers the audio data from the game's ROM to RAM, then it initialises **Audio Lists** to be used by the RSP.
- The **RSP**: With the use of even more microcode, it interprets the audio lists previously stored in RAM and performs the required operations to the audio data which, for example, can include:
  - Uncompressing **ADPCM samples** and applying effects.
  - Sequencing and mixing **MIDI data** using **audio banks** stored in RAM as well.

The resulting data is, as expected, waveform data. This is then sent to the **Audio Interface** or 'AI' block which will then transfer it to the digital-to-analogue converter. The resulting waveform contains two channels (since our system is stereo) with 16-bit resolution each.

![Overview of how the audio pipeline is often programmed.](Audio.png)

### The repertoire

Time to check out the soundtracks made for the N64. There are too many (good ones) to mention in this article, so here are some that caught my attention:

![The Legend of Zelda: Majora's Mask (2000).<br>The music of this game is tied to its daunted atmosphere.](observatory){.toleft video="true"}

![Bomberman Hero (1998).<br>This game has a nice and unique house-based soundtrack.](redial){.toright video="true"}

### Secrets and limitations

Because of this design, the constraints will depend on the implementation:

- The sampling rate can be up to **44.1 kHz**, but using the top rate will steal lots of CPU cycles.
- There's no strict limit on the number of channels, it all depends on how much the RSP is capable of mixing (often around 16-24 channels if processing ADPCM or ~100 if PCM).
- Memory is another concern, while competitors relied on larger mediums (i.e. CD-ROM) and dedicated audio memory, Nintendo 64 cartridges hold much less data (let alone music data) and have to share main memory with other components.
  
For those reasons, players may notice that N64 ports contain lesser-quality music or repeated scores. Although, a common workaround is to implement a music sequencer that 'constructs' samples at runtime using a pre-populated set of sounds (similar to MIDI music).

## Operating System

Similar to the PS1 and Saturn, N64 games are written for bare metal. However, there are no BIOS routines available to simplify some operations. As a substitute, **games embed a small OS** that provides a fair amount of abstraction to efficiently handle the CPU, GPU and I/O.

This is not the conventional *desktop OS* that we may imagine at first, it's just a micro-kernel with the smallest footprint possible that provides the following functionality:

- Multi-threading using message passing (don't forget the CPU is single-core).
- Scheduling and Preemption.
- Simplified register and I/O access.

All in all, those functions are critical for organising audio, video and game logic tasks that need to work concurrently.

The kernel is automatically embedded by using Nintendo's libraries. Additionally, if programmers decide not to include one of the libraries, the respective portion of the kernel is stripped to avoid cartridge space being wasted.

### Boot process

Unlike previous cartridge-based systems, the Nintendo 64 follows a sophisticated boot process to prepare all of its hardware before the actual game runs. This is executed as soon as the user turns on the console and it's very similar to its CD-based contemporaries bundling a [BIOS](playstation#operating-system) or [IPL](sega-saturn#operating-system).

These routines are also referred to as **Initial Program Load** (IPL) and work as follows [@operating_system-ipl] [@operating_system-ipl_decomp]:

1. The user turns on the console.
2. The **PIF-NUS** (a separate chip in the motherboard) subdues the main CPU into an infinite reset until PIF-NUS validates the CIC chip found in the game cartridge.
    - The PIF-NUS and the CIC chip are further explained in the I/O and Anti-piracy sections, respectively.
2. If the verification process has finished successfully, the CPU starts execution at `0xBFC00000`. This address points to an **internal ROM** within PIF-NUS, particularly, the first boot stage called **IPL1**.
3. IPL1 initialises part of the hardware (CPU registers, the parallel interface and the RCP) and copies the next stage (**IPL2**) from the internal ROM to the RSP's memory for faster execution. Then, it redirects execution there.
4. IPL2 copies the first megabyte of the game ROM into RSP memory, verifies it (using PIF) and executes it. This block contains the next boot stage called **IPL3**.
5. IPL3 initialises RDRAM and CPU cache. Afterwards, it starts the operating system (i.e. virtual memory and exception vectors), sets up the program state (i.e. stack pointer) and finally proceeds to call the starting routine of the game.

As IPL3 is found within the game cartridge, not every game bundles the same code. Presumably, the variants are correlated to the CIC chip variant bundled in the cartridge.

## I/O

As you know by now, I/O is not directly connected to the CPU, so the RCP's third module (which I haven't mentioned until now) serves as an **I/O interface**, this is the block handling communication with the CPU, controllers, game cartridge and Audio/Video DACs.

### Accessories

The Nintendo 64 controller includes a connector used to plug in accessories. Examples of commercial accessories include:

- The **Controller Pak**: Another medium (similar to Sony's *Memory Card*) used to store save data and share it with other consoles.
- The **Rumble Pak**: Contains a small motor to provide haptic feedback, useful for 'immersing' the player in certain games.

All accessories connected to the controller are managed by the **PIF-NUS**, an obscure block that also handles security. The RCP communicates to the PIF using a 'really slow' (words from the programming manual) **Serial bus**.

## Games

Nintendo held on to the cartridge medium for storage instead of switching to optical discs. As a consequence, games enjoyed higher bandwidths (according to Nintendo, an average of 5 MB/sec) while also being more expensive to produce. The biggest cartridge found in the market has 64 MB.

Inside cartridges, manufacturers may include extra memory (in the form of *EEPROM*, *flash* or *SRAM* with a battery) to hold saves. Though this is not a strong requirement anymore since certain accessories could be used to store saves as well.

Cartridges communicate to the RCP using a dedicated 16-bit bus called **Parallel Bus** (PBUS) or 'Parallel Interface' (PI).

### Source Development Kit

In general, development was mainly done in **C** and **assembly**, the latter was often required to achieve better performance. While we've seen this system provides 64-bit operations, the new instructions were rarely used since, in practice, 32-bit instructions happened to be faster to execute (as the R4300i/VR4300 comes with a 32-bit data bus).

Libraries in the official SDK contain several layers of abstractions to command the RCP. For example, C structs like the **Graphics Binary Interface** or 'GBI' were designed to assemble the necessary Display lists more easily. The same applies to audio functions (its struct was called **Audio Binary Interface** or 'ABI').

In terms of microcode development, Nintendo already provided a set of microcode programs to choose from. However, if developers wanted to customise it, that would indeed be a challenging task: The Scalar Unit instruction set wasn't initially documented, but later on, Nintendo changed its position and SGI finally released some documentation for microcode programming.

![An SGI Indy I found at The Centre for Computing History (Cambridge, UK) when I visited in August 2024. By comparison, this computer houses a MIPS R4400 CPU, an improved successor of the R4000 (all in all, miles ahead of the VR4300).](sgi_indy.webp)

Hardware used for development included workstations supplied by SGI [@games-devkit], such as the **Indy** machine bundling an extra daughterboard called **U64** that contains the hardware and I/O of the retail console. Tools were supplied for Windows computers as well [@games-u64].

Other third-party tools consisted of custom cartridges featuring a long ribbon cable that connected to the workstation. This cartridge fitted in a retail Nintendo 64 but included internal circuitry to redirect 'read' requests from the console to the workstation's RAM. The deployment/debugging process was carried out by transferring a copy of the game to RAM and then when the console was switched on, it would start reading from there.

### The alternative medium

Additionally, the PBUS branches out to another connector at the bottom of the N64's motherboard. This was meant to be used by the yet-unreleased **Nintendo 64 Disk Drive** (64DD), some sort of an 'extra floor' that contains a proprietary magnetic disk reader [@games-dd]. Its disks provide up to 64 MB of capacity. While only released in Japan, the Disk drive opened the door to an alternative (and cheaper) medium for distributing games.

![The Nintendo 64 Disk Drive [@photography-amos].<br>Released on 01/12/1999 in Japan.](64dd/module.png){.open-float .tabs-nested .tab-float .active title="Module"}

![The 64DD attached to the console [@photography-amos].](64dd/attached.png){.tabs-nested-last title="Attached"}

The magnetic medium is slower than cartridges, with transfer speeds of up to 1 MB/sec, but still faster than 4X CD-ROM readers. Disks are double-sided and operate at 'Constant Angular Velocity' (like the later [miniDVD](gamecube#medium)). The smallest readable area is called a 'block' and it's half of a concentric circle.

There's **no buffer memory** included in this reader, so the bits read are stored in RDRAM for execution. To tackle the increased memory requirement, Nintendo included the RAM Expansion Pak with the 64DD as well. In doing so, it also standardised the extended RAM space so all 64DD games could take advantage of it.

{.close-float}

Furthermore, parts of the disk are re-writable to enable storing saves, the amount of writable area depends on the type of disk used (Nintendo provided seven types). On the software side, game data is structured with a filesystem called 'Multi File System' (MFS) provided by Nintendo with their SDK. Games can either access disk data using the file system or on a block-to-block basis, the latter relies on another library called 'Leo' for low-level functions.

The Disk drive also houses an internal ROM (referred to as 'DDROM') that stores code the N64 executes to bootstrap the disk and show the splash animation. This works as a new IPL stage added on top of the traditional boot process. The ROM also stores fonts (Latin and Kanji) and some sounds. The ROM is only found on retail units, as development units relied on external programs loaded through the dev kit.

## Anti-piracy / Region Lock

The anti-piracy system is a continuation of the [SNES' CIC](super-nintendo.md#anti-piracy--region-lock). As you know, bootleg detection and region locking are possible thanks to the CIC chip (which must be present in every *authorised* game cartridge) [@anti_piracy-cic], the Nintendo 64 improved this system by requiring different games to have a specific variant of the CIC chips. This makes sure the cartridge is not counterfeit or contains a CIC clone. The PIF performs checksum checks at the start and during gameplay to supervise the current CIC installed on the cartridge.

If for any reason the PIF considers the current cartridge is not valid, it will then induce the console in a permanent freeze.

Region-locking was done by slightly altering the shape of the cartridge between different regions so the user couldn't physically insert the game on an N64 from a different region.

Overall, there wasn't too much concern regarding piracy thanks to the use of cartridge medium, although game prices were three times higher than CD-based ones.

### Unused ports

As silly as it may seem, Nintendo left one door open: The **Disk Drive port**.

![The Doctor V64 attached to the console [@photography-amos].](v64/attached.png){.open-float .tabs-nested .tab-float .active title="Attached"}

![The back of the V64 [@photography-amos], showing some interesting A/V.](v64/back.png){.tabs-nested-last title="Back"}

A few companies reversed-engineered the interface to develop their own hardware, and some of the resulting products became a concern for piracy.

I guess the one worth mentioning is the **Doctor v64**, this device has the same shape as the Disk Drive but includes a CD-ROM drive instead.

The expansion can rip the contents of the cartridge to a CD, the opposite (reading ROM files from a CD) is also possible.

{.close-float}

### Emulation

When I was a kid I used to play some N64 games on a Pentium II machine using an emulator, it wasn't *that bad* but in later years I wondered now *how the freck* was able to happily emulate a complex 64-bit machine since, among other things, my PC barely had enough RAM to keep the integrated video alive.

The truth is, while reproducing the architecture of this console can be complex, things like microcode will give a hint of what the console is trying to do, and since emulators *don't have to be* cycle-accurate, they can apply enough optimisations to provide more performance in exchange for real emulation.

Another reason is the 64-bit instructions, since games barely used them, emulation speed would hardly be hit when running on a 32-bit host machine.

## That's all folks

![My _timeshared_ N64 at a friend's house.<br>While I only wanted the console for the article, my friend always wished for a N64 DD, so we bought a complete (but expensive) Japanese N64 DD set together to avoid spending too much individually. I then installed N64RGB so we could plug it into a modern TV; and the result is a nice piece of entertainment (and topic of discussion!).](n64.jpeg)

I have to say, this article may be the longest one I've ever written, but hopefully you found it a nice read!

I'll probably take the following days to tide up some things on the website instead of starting to write the next article.

Until next time!  
Rodrigo
