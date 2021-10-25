---
name: Dreamcast
date: 2019-10-07
releaseDate: 1998-11-27
subtitle: One last attempt
generation: 6
javascript: ['threejs']
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "The Dreamcast
    <br>Released on 29/11/1998 in Japan, 09/09/1999 in America and 14/10/1999 in Europe"
  Motherboard:
    caption: "Showing revision 'VA1'
    <br>While the official docs state that the system contains 128KB of flash memory, this motherboard happens to include a 256KB EEPROM chip for some reason instead
    <br>Battery and controller ports are found in a daughterboard called 'Front panel'"
  Diagram:
    caption: "Important data buses are labelled with their width and speed."

# SEO
title: Dreamcast Architecture

# Historical
aliases: [/projects/consoles/dreamcast/]
---

## Introduction

The Sega Dreamcast introduced many new features over its predecessor (the [Saturn]({{< ref "sega-saturn" >}})) to appeal to both game developers and console gamers. While this was Sega's last attempt to conquer the console market, some of the technologies which were pioneered in the Dreamcast carried on and into future mainstream devices.

---

## CPU

Unsurprisingly, Sega chose Hitachi again to develop their CPU. If you've been reading the [previous article about the Sega Saturn]({{< ref "sega-saturn" >}}) then, lo and behold, I present you the next generation of SH processor: the **SH-4** running at a whopping **200 MHz**. So, what's interesting about this CPU?

- **5-stage pipeline**: Up to five instructions can be in flight simultaneously (a detailed explanation can be found in a [previous article]({{< ref "sega-saturn#cpu" >}})).
  - Instruction pipelining is now found everywhere in this generation of consoles and will be standard from now on.
- **2-way superscalar**: A new type of parallelism where the CPU can process more than one instruction (two in this case) in each stage of the pipeline resulting in more instructions executed per second.
- A dedicated **Floating-Point Unit** or 'FPU': Computes 32-bit decimal numbers (the *floats*) and 64-bit ones (the *doubles*).
- 8 KB **instruction cache** and 16 KB **data cache**: This ratio is rather curious since consoles tend to include more instruction cache than data cache. However, the SH-4 allows the data cache to be split into two sections: 8 KB of *Scratchpad* (fast RAM) and 8 KB of data cache.
- **32-bit internal architecture** while keeping a **16-bit instruction set** (the SuperH ISA): Just like the SH-2, this increases code density and decreases bus overheads while still enjoying the advantages of a 32-bit architecture.
- **External 64-bit bus**: Critical for manipulating 64-bit values (e.g. doubles and longs) without wasting extra cycles.

### Extra work

The common chores of a game console CPU include handling a game's logic, running the enemy AI and keeping the GPU fed with instructions. In the Dreamcast, the SH-4 is also involved in the majority of the graphics pipeline, processing geometry data such as computing perspective transformations. As a result, it includes a **128-bit SIMD** unit that can accelerate vector operations.

### Improving memory access

The CPU includes a dedicated **Memory Management Unit** or 'MMU' for virtual addressing, this is helpful since the physical memory address space of this CPU happens to be **29 bits wide**. So with the help of four TLBs, programmers can use 32-bit addresses without hitting performance penalties.

Since only 29 bits are needed for addressing, the extra three bits control memory protection, alternating the memory map and circumventing the cache, respectively.

The programmer decides whether to use these features or not. Games for this system certainly don't necessarily _need_ memory protection and the MMU has to be manually enabled at boot.

### No UMA but...

While this system is not designed around the strict Unified Memory Architecture like a [well-known competitor]({{< ref "nintendo-64#simplified-memory-access">}}), it does **delegate I/O access to the GPU**. That means that if the CPU has to fetch anything that's beyond its dedicated RAM or a serial interface (which is also connected), it will have to request the GPU and wait if necessary.

### Special queries

This CPU also features a unique functionality called **Parallel I/O** or 'PIO' that is used to manipulate multiple I/O locations at the same time. Sega wired up these pins so the CPU can manipulate the GPU's **video mode** (more details about this later).

---

## Graphics

The GPU package is a custom-made chip called **Holly** running at **100 MHz**, it's designed by VideoLogic (now known as Imagination Technologies) and manufactured by NEC. Holly's 3D core happens to be Videologic's **PowerVR2** (also called 'PowerVR Series2' and 'CLX2').

{{< float_group >}}

{{< figure_img float="true" src="sonic.png" alt="Sonic Adventure" >}}
Sonic Adventure (1999)
{{< /figure_img >}}

{{% inner_markdown %}}
VideoLogic chose an alternative approach for the construction of their 3D engine called **Tile-Based Deferred Rendering** (TBDR).

Instead of rendering a whole frame at once (as traditional **Immediate Mode Renderers** or 'IMR' do), TBDR divides the rendering area into multiple sections called 'tiles'. Then, it carries out the rendering process on each tile individually and the result is combined to form the final frame.

{{% /inner_markdown %}}
{{< /float_group >}}

This innovative design brings interesting advantages:
- It can be greatly **parallelized**, which significantly reduces bandwidth and power usage.
- It implements a clever solution to the [**visibility problem**]({{< ref "sega-saturn#an-introduction-to-the-visibility-problem" >}}) by automatically sorting the polygons **from front to back** and then performing [z-tests]({{< ref "nintendo-64#modern-visible-surface-determination" >}}) at the first stages of the pipeline. The combination of these tasks not only solves the original issue, but also **prevents overdraw** (rasterization  of hidden polygons) which wastes resources and degrades performance.

It's no surprise that Imagination took this efficient technology forward to build the Series 4 PowerVR cores which powered an incredible number of devices, including the first generation of iPhone, the iPhone 3G, the Nokia N95, and the Dell Axim x51.

### Architecture

Let's take a look at the two main components of the Dreamcast's GPU:

{{< tabs >}}
{{< tab active="true" name="Tile Accelerator" >}}

{{< figure_img float="true" src="tile_accelerator.png" alt="Tile Accelerator Diagram" >}}
Architecture of the Tile Accelerator
{{< /figure_img >}}

{{% inner_markdown %}}
Before the rendering process starts, a component known as the **Tile Accelerator** performs pre-processing. It starts by allocating several 32x32 tile bins into which the geometry will be rendered.

Then, the Tile Accelerator will:

1. Grab the geometry data and drawing commands issued by the CPU (either using DMA or traditional transfers).
2. Convert this data into an *internal* format.
3. Distribute the geometry to each bin based on its coordinates. Clipped geometry will be discarded as well.
4. Generate the resulting Display Lists.

These Display Lists are then interpreted by the 3D engine: The PowerVR2.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="PowerVR2 Core" >}}
{{< figure_img float="true" src="powervr2.png" alt="PowerVR2 Core Diagram" >}}
Architecture of the PowerVR2 Core
{{< /figure_img >}}

{{% inner_markdown %}}
Here is where the graphics are brought into life, the Display Lists received from the TA tell the core to render the geometry of a single tile using an **internal frame-buffer**. The process is as follows:

1. The **Image Synthesis Processor** or 'ISP' fetches the primitives (either triangles or quads) and performs **Hidden-Surface Removal** to remove unseen polygons. Then, after calculating its Z-buffers and stencil buffers, the data goes through **Depth Testing** to avoid rendering polygons that would appear behind others and **Stencil Tests** to cull geometry that won't be visible if they are located behind a 2D polygon (also called **Mask**).
    - Notice how these tests are effectively carried out at the start of the pipeline. In contrast, previous consoles [using late z-buffering]({{< ref "nintendo-64#modern-visible-surface-determination" >}}) discard the geometry at the end of the pipeline. The ISP approach prevents processing the geometry that will eventually be discarded, thereby saving resources.
2. The **Texture and Shading Processor** or 'TSP' applies colouring, shading, and multiple effects over the tile area.
    - Textures are not applied until the tile is exported, meaning that emerging overdraw (if any) will not lower the fill rate.

After the operation is completed, the rendered tile is written to the main frame-buffer in VRAM. This process is repeated until all tiles are finished. Once complete, the resulting frame-buffer is picked by the **Video encoder** and sent through the video signal.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### The big picture

Apart from the clear architectural difference, the Texture and Shading Processor comes with many capabilities that give one an idea of how distant this console is from the old [Saturn]({{< ref "sega-saturn">}}). Here are some notable examples:

- **Alpha blending**: Combines colours of overlapping layers to achieve transparency effects.
  - The process used for applying transparency in this system is called **order-independent transparency**. The algorithm automatically sorts the primitives before blending their colours, and while this slows down the rendering process, it avoids relying on the game itself to do all the sorting manually. For this reason, Dreamcast games excelled in displaying transparent objects.
  - Combined with the tile-based system, order-independent transparency completely addresses previous [mishaps]({{< ref "sega-saturn#the-transparency-issue" >}}).
- **Mip-Mapping**: Automatically selects a scaled-down version of the texture depending on the level of detail required. This is done to prevent processing large textures that would be seen far away from the camera (which would be a waste of processing power and produce aliasing).
- **Environment mapping**: Applies reflections on textures.
- **Bilinear, Trilinear, and anisotropic filtering**: These are different algorithms used to smooth the textures and prevent pixelation. They are ordered from 'worst' to 'best', where the resulting quality of each one is directly proportional to the amount of computation required.
  - This is a huge step up from the Saturn since the former didn't provide any texture filter!
- **Bump mapping**: Simulates defects on surfaces without spending extra polygons.

### Gaining detail

Holly can now draw ~10 times more polygons than [its predecessor]({{< ref "sega-saturn">}}), here's a *Before & After* example that shows how model designs are not that limited anymore. Try to fiddle with them!

{{< side_by_side >}}
  {{< threejs_canvas model="sonic_r" class="toleft" >}}
Sonic R (1997) for the Saturn  
298 triangles
  {{< /threejs_canvas >}}
  {{< threejs_canvas model="sonic_adventure" class="toright" >}}
Sonic Adventure (1999) for the Dreamcast  
1001 triangles
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

### Video Modes

The video system was designed to support multiple types of screens and formats, thus the video encoder outputs to a single-shaped socket that supports the following type of signals:

- **Composite**: Combines the three signals needed to display video (chroma, luma, and sync) into a single one, requiring only a single-pin cable.
  - This is used on old PAL and NTSC TVs with an RCA connection.
- **S-Video**: Combines luma and sync while keeping chroma separated (two video lines in total).
- **RGB**: Sends separate Red-Green-Blue signals and provides different sync types to choose from (composite sync or extracted from video composite or S-Video).
  - A SCART cable will use this type. 
- **VGA**: Combines RGB with two sync signals (horizontal and vertical) resulting in five video lines in total. This enables the display of the largest resolution possible (720x480) in progressive mode (thus, this mode is often named '480p'). VGA has actually been the standard format/medium used by computer monitors for some time.
  - To use this type, Sega provided a VGA adapter as an extra accessory.

Now, the Dreamcast can't encode all of these at the same time, so the GPU and the Audio processor contain a register called **Image Mode** that coordinates which video/audio buses will be activated to generate the requested signal. The CPU detects the type of cable inserted (by checking which 'select bits' of the video connector are active) and writes the required values on the GPU. Finally, the values are forwarded to the Audio processor.

Since VGA is strictly a progressive type of signal (as opposed to the traditional *interlaced*), some compatibility issues arose with games that were only designed for interlaced video. These explicitly state in their code that the game won't display on VGA, so the CPU will block the game until the user swaps out the VGA cable for another type.

---

## Audio

The Audio functionality is handled by a custom chip called **AICA** made by Yamaha, it's an improved version of the [SCSP used in the Saturn]({{< ref "sega-saturn#audio" >}}) and is composed of four components:

- The **Sound Integrated Circuit** or 'IC': A set of modules (synthesiser, DSP, and mixer) that generates the audio signal and applies effects on it. It supports up to **64 PCM channels** with a resolution of **16 or 8 bits** and a sampling rate of **44.1 kHz**. Overall, this is the optimal quality for playing audio. 
  - Additionally, it includes an **ADPCM decoder** to offload some work from the CPU.
  - Curiously enough, it also provides **two MIDI pins** to connect a MIDI instrument, although this is meant to be used during development.
- **2 MB of SDRAM**: Stores sound data and programs. It's filled by the main CPU using DMA.
- An **ARM7DI** running at ~2.82 MHz: Controls the Sound IC. This CPU is programmed by booting a small software (called [driver]({{< ref "super-nintendo#audio" >}})) stored in SRAM which interprets the audio data and manipulates the Sound IC accordingly. 
  - If you wonder, a similar CPU is also used [here]({{< ref "game-boy-advance">}}).
- **Memory Controller**: Interfaces the 2 MB of SDRAM.

To help with development, the official SDK included multiple sound drivers for different needs (sequencing, decoding, etc).

### Evolution

We've come so far since the days of the [Mega Drive/Genesis]({{< ref "mega-drive-genesis#audio" >}}), in order to show how much progress was made in sound synthesis, here's an example of two games, one for the Mega Drive and the other for the Dreamcast, that used the same composition:

{{< side_by_side >}}
  {{< video src="megadrive" class="toleft" >}}
Sonic 3D Blast (1996) for the Mega Drive  
The predecessor performs FM synthesis to generate audio signals on the fly
  {{< /video >}}

  {{< video src="dreamcast" class="toright" >}}
Sonic Adventure (1999) for the Dreamcast  
The new audio subsystem processes PCM samples without any hassle
  {{< /video >}}
{{< /side_by_side >}}

Instead of programming an FM chip, the composers of Sonic Adventure produced their soundtrack in-house and then encoded it to 'ADX', a lossy format developed by CRI Middleware. Hence, it only uses two of the 64 PCM channels (stereo).

ADX compression enables the game to decode and stream the data from the GD-ROM to the Sound IC without running out of memory or bandwidth. The driver can be implemented in many ways, as there are multiple multiple approaches to balance the workload of the main CPU and ARM7.

### Staying alive

Somehow, this chip is also responsible for providing a **Real Time Clock** (RTC) to the BIOS, it's also connected to a clock battery to continue working without AC power.

---

## Operating System

2 MB of 'System ROM' stores a **BIOS** that bootstraps the game or a small shell when the console is switched on.

The BIOS also contains routines that games use to simplify I/O functions, like reading from the GD-ROM drive.

### Shell

If there isn't a valid game disc inserted, the console proceeds to boot the graphic shell.

{{< float_group >}}
{{< figure_img float="true" src="shell.png" alt="Dreamcast Shell" >}}
Shell after booting without a disc
{{< /figure_img >}}

{{% inner_markdown %}}
The shell contains a simple graphical user interface to enable the user to perform basic but necessary tasks like:

- Start the game, if it hasn't already.
- Manipulate the save data stored in the VMU (more details about this device later!).
- Play music, if there's an Audio CD inserted.
- Change some settings (Date, Time, Sound, etc).
{{% /inner_markdown %}}

{{< /float_group >}}

### Windows CE

Ever since the Dreamcast's announcement, it was said that the console can run **Windows CE**: a stripped-down version of Windows designed for use on embedded devices. This is a bit misleading considering some users would expect to see a full Windows CE desktop environment running on their console.

{{< float_group >}}
{{< figure_img float="true" src="windows_ce.jpeg" alt="Windows CE" >}}
Windows CE logo stamped on the front of the case
{{< /figure_img >}}

{{% inner_markdown %}}
In reality, the purpose of this 'OS' was very similar to what Nintendo did with [the Nintendo 64]({{< ref "nintendo-64#operating-system" >}}): to provide programmers with a fair layer of abstraction to simplify certain operations.

Microsoft worked with Sega to bring Windows CE to the Dreamcast. The result was a subset of CE with the minimal components needed to provide graphics, audio and debugging. This included the use of Microsoft's star IDE, **Visual Studio**, for development.
{{% /inner_markdown %}}

{{< /float_group >}}

Some developers found this option very attractive. Since the audio-graphics framework included with CE was none other than **DirectX 6**, thousands of PC games of that era could, in theory, be easily ported to the Dreamcast...

However, the architectural differences between the Dreamcast and the conventional PC were too great to ignore. Also, embedding this system increased the game's loading time (after all, the 'OS' had to be loaded from a disc) and Windows CE happened to eat a substantial part of resources from the Dreamcast (*not surprisingly, PCs were already suffering from that*).

In the end, 'Windows CE for Dreamcast' was just another SDK of choice for developers (it's commonly referred to as **Dragon SDK**). Nonetheless, a considerable number of Dreamcast games ended up choosing Windows APIs and DirectX.

---

## I/O

The GPU also includes another module for handling most of the I/O called **System Bus**. It provides the following interfaces:
- The **G1** interface: Where the **BIOS ROM** along with its saved configuration and the **GD-ROM** content can be accessed.
- The **G2** interface: Provides access to the **Modem** and **Sound Controller**.
- The **Maple** interface: Transfers chunks of data between the controllers (along with the accessories connected to them) and the CPU. It's a **serial bus** and provides a dedicated DMA.
- The **SH-4** interface: Connects the main CPU for general purpose communications.
- The **DDT** interface: Takes control of the CPU bus to access its main memory during DMA transfers.
- The **PVR** interface: Connects the CPU with the Tile Accelerator using a dedicated DMA.

---

## Games

Development was mainly done in **C** or **C++**. At first, C was the recommended choice since the available C++ compilers were initially very limited in functionality.

Sega also provided development hardware in the form of a PC-like tower called the **Sega Katana Development Box**. This is Dreamcast hardware with enhanced I/O for development. It also came with a CD containing the official **Katana SDK** and tools to be installed on a Windows 98 PC.

In the case developers chose the Dragon SDK instead, DirectX 6.0 and Visual C++ 6.0 were also available to them.

### Medium

Games are stored in GD-ROMs, which are just CD-ROMs with a higher density of pits (reaching a gigabyte of capacity). The speed is 12x, which is *not too shabby* compared to Saturn's 2x CD reader.

### Online platform

The Dreamcast shipped with a **modem** module installed which games could use to 'call' a dial-up service for online gaming. Sega provided two services: **SegaNet** (used in America and Japan) and **Dreamarena** (the European counterpart).

Players registered with a service using **DreamKey**, an extra disc that was bundled with some games. DreamKey provided a web browser to register an account. Initially, DreamKey came as a pre-configured service depending on the region, but later revisions allowed users to alter its ISP settings to connect to any of them.

There was also a Dreamcast-branded keyboard and mouse available to buy, just in case the user fancied surfing the net *PC-style*.

Unfortunately, SegaNet and Dreamarena were discontinued two years after launch. Thus, games that exclusively relied on them became unusable, unless such services are emulated using extra tools (like the DreamPi, a Raspberry Pi image that replicates them with the help of servers maintained by a community of users).

### Interactive memory card

Another innovative feature that the Dreamcast featured was the **Visual Memory Unit** or 'VMU'. It is attached to the controller and, aside from serving as a memory card, is a fully-fledged device that includes:

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
    {{< tab_figure_img name="VMU" active="true" src="vmu.png" alt="VMU" >}}
VMU detached from the controller
    {{< /tab_figure_img >}}
    {{< tab_figure_img name="Detached" src="controller.png" alt="Controller" >}}
Controller without VMU attached
    {{< /tab_figure_img >}}
    {{< tab_figure_img src="controller-vmu.png" alt="Controller with VMU" >}}
Controller with VMU attached
    {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
- A **Sanyo LC86K87**: An 8-bit low power CPU.
- A **32x48 Monochrome LCD** with four additional icons: Commanded using 196 B of XRAM (external RAM) as frame-buffer.
- **Two serial connectors**: One for IN and the other for OUT.
- **Six physical buttons**: Used when the VMU is detached from the controller.
- A **16 KB Mask-ROM**: Stores the BIOS-IPL.
- **64 KB of Flash**: 32 KB for storing a single program (transferred from the console) and the other 32 KB for keeping Dreamcast's saves.
- **512 B of RAM**: 256 B is reserved for the system, leaving only 256 B available for the program.
{{% /inner_markdown %}}

{{< /float_group >}}

The VMU has two modes of operation:

- **Attached to the controller**: The official controller has two slots to connect VMUs and other accessories with the same shape, if the VMU is inserted on the first slot (visible from the front of the controller), it can display drawings during gameplay. Moreover, the Dreamcast can store saves and a program on the VMU.
- **Detached from the controller**: The gadget becomes a Tamagotchi-like device with a clock, save manager and can also run whatever program the Dreamcast previously transferred. Two VMUs can be connected to share content as well.

---

## Anti-Piracy & Homebrew

Using the proprietary GD-ROM format helped to inhibit the production of unauthorised copies of games (and running them on other consoles). Dreamcast games are also *region-locked* meaning that a console will refuse to run a game intended for a different region.

### Defeating it

In practice, the anti-piracy measures were *utterly* useless due to Sega leaving a huge backdoor open: **MIL-CD**. Music Interactive Live-CD or 'MIL-CD' is a format created by Sega to extend an Audio-CD with interactive programs... and the Dreamcast is compatible with it.

Unauthorised commercial discs (cheat loaders, movie players, etc) disguised as MIL-CDs to run on the console without Sega's approval. Later on, different hacking communities dissected this exploit and came up with a workaround to boot pirated games using CD-ROMs. This caused an unstoppable wave of ISOs to be released on the net.

Some problems surfaced afterwards: Although GD-ROMs can store a gigabyte of data, CD-ROMs can only fit ~700 MB, so how could 'rippers' shrink the bigger games to fit on a CD? By re-compressing music and graphics until it fits. They may even try to split it into two discs. After all, game data is not a single blob anymore (like on an old cartridge), but is now organised hierarchically into files and directories.

---

## That's all folks

{{< figure_img src="folks.png" alt="My Dreamcast" class="centered-container" >}}
A Dreamcast I had to get in order to write lots of stuff here  
Not too bad for its age!
{{< /figure_img >}}

I hope you enjoyed reading the article. I finished writing it during the start of my final year at uni.

I'll probably be very busy from now on, but I do enjoy writing these articles so hopefully you'll get the next one in a few weeks!

Until next time!  
Rodrigo