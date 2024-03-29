---
short_title: Dreamcast Architecture
long_title: Architecture of the Dreamcast
name: Dreamcast
date: 2019-10-07
release_date: 1998-11-27
subtitle: One last attempt
generation: 6
javascript: ['threejs', 'audioswitcher']
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "The Dreamcast.<br>Released on 29/11/1998 in Japan, 09/09/1999 in America and 14/10/1999 in Europe."
  Motherboard:
    caption: "Showing revision 'VA1'.<br>While the official docs state that the system contains 128KB of flash memory, this motherboard happens to include a 256KB EEPROM chip for some reason instead.<br>Battery and controller ports are found in a daughterboard called 'Front panel'."
  Diagram:
    caption: "Important data buses are labelled with their width and speed."

# Historical
aliases: [/projects/consoles/dreamcast/]
---

## Introduction

The Sega Dreamcast introduced many new features over its predecessor (the [Saturn](sega-saturn)) to appeal to both game developers and console gamers. While this was Sega's last attempt to conquer the console market, some of the technologies that were pioneered in the Dreamcast carried on and into future mainstream devices.

## {.supporting-imagery}

## CPU

Things were progressing smoothly for Hitachi, their [iconic SuperH chips](sega-saturn#cpu) had found multiple clients and the company was now ready for a fourth installment of the series. Their new entry would combine embedded capabilities with functionality enjoyed by 3D games.

Sega, being one the early adopters of the SuperH, unsurprisingly chose Hitachi's emerging chip to power their new console. Hence, the Dreamcast carries an **SH-4 CPU** running at a whopping **200 MHz** [@cpu-spec]. Also, to [make things right](sega-saturn#the-final-product) this time, there's **only one fully equipped CPU**.

### The offering

That being said, what's interesting about this new processor?

Well, to start with, the SH-4 follows up as a superset of the previous models, meaning it inherits all the [existing features](sega-saturn#cpu) of the SuperH line, including a **32-bit RISC architecture**, a **5-stage pipeline** and a **16-bit instruction set**. Alas, it also heirs [control hazards](playstation#delay-galore).

![The SH-4 chip.](sh4.jpg)

On top of that, as the next-generation CPU, it debuts many improvements that go beyond the scope of embedded applications [@cpu-arch]:

- A **2-way superscalar** pipeline: A novel parallelism technique where the CPU can process more than one instruction (two in this case) in each stage of the pipeline. This results in more instructions executed per second.
  - Behind the scenes, the CPU is distributing the two instructions to two different (and available) execution units. Hence - with the exception of a very small group - all instructions have to be of different types (i.e. branching, arithmetic, etc) to be parallelised [@cpu-soft_doc]. Otherwise, they can only be executed sequentially. Be as it may, the SH-4 also exhibits a subset of instructions that can never be parallelised, as they contain too many dependencies to make it feasible.
- **8 KB instruction cache** and **16 KB data cache**: This ratio is rather curious since consoles tend to include more instruction cache than data cache. However, the SH-4 allows the data cache to be split into two sections: **8 KB of Scratchpad** (fast RAM) and **8 KB of data cache**.

#### Special work

The common chores of a game console CPU include handling the game's logic, running the enemy AI and keeping the GPU fed with drawing tasks.

With the Dreamcast, you will see that the GPU only covers the tasks of a [rasteriser](playstation#graphics). So, the CPU must get involved with the majority of the graphics pipeline. This means the CPU will be [processing](playstation#tab-2-2-geometry-transformation-engine) vast amounts of geometry data (such as computing perspective transformations). Now, to make sure the CPU can sustain this role, Sega and Hitachi collaborated to incorporate two crucial extras into the SH-4.

The first addition is a dedicated **64-bit Floating-Point Unit** (FPU). This component computes 32-bit decimal numbers (the so-called 'single-precision' or 'floats') or 64-bit ones (the 'double-precision' or 'doubles' type) abiding by the **IEEE-754 standard**. Its register file is made of **thirty-two 32-bit registers**, but they can also be combined into a different group of **sixteen 64-bit registers**, this is what enables the unit to operate doubles.

If that wasn't enough, Hitachi took a step forward with the FPU and implemented extra logic to form another register group, this time made of **eight 128-bit registers**. In it, each register now stores four 32-bit floats or, in other words, **128-bit vectors**. This format is optimal for graphics-related operations. 

To make good use of the new vectors, the FPU includes specialised instructions for operating them, much like what the [Saturn Control Unit](sega-saturn#the-third-processor-and-counting) provided, except the industry is now a bit more standardised. The new instructions constitute what is often known as **Single Instruction Multiple Data** (SIMD) and may perform the following algebraic operations:

- Dot product.
- The sum of squares.
- Matrix multiplication.

The second addition is the SH-4's external bus, which is now **64-bit wide**, enabling the CPU to transfer pairs of 32-bit values at the same time. This is another improvement that adds up to the overall performance of this CPU.

#### Memory & access

The Dreamcasts houses **16 MB of SDRAM**, and it's directly connected to the CPU using a **100 MHz** bus (half the CPU speed).

Conversely, the memory's data bus is only 32-bit wide [@cpu-spec]. Does this mean the CPU's shiny 64-bit bus is wasted? No, because the RAM is installed using **two 8 MB banks**. So, each chip is connected to half of the CPU's bus lines.

![Memory diagram.](memory.png)

To access this memory, the CPU includes a dedicated **Memory Management Unit** or 'MMU' for virtual addressing, this is helpful since the physical memory address space of this CPU happens to be **29 bits wide**. Additionally, thanks to the incorporation of four **Translation Lookaside Buffers** (TLBs), programmers can use 32-bit addresses without hitting performance penalties.

Now, since only 29 bits are needed for addressing, the extra three bits control memory protection, alternating the memory map and circumventing the cache, respectively [@cpu-marcus] [@cpu-akiba].

Ultimately, programmers decide whether to use these features or not. Games for this system certainly don't require memory protection and the MMU must be manually enabled at boot.

### No UMA but...

While this system is not designed around the strict Unified Memory Architecture like a [well-known competitor](nintendo-64#simplified-memory-access), it does **delegate I/O access to the GPU**. That means that if the CPU has to fetch anything that's beyond its dedicated RAM or a serial interface (which is also connected), it will have to request the GPU and wait if necessary.

This CPU also features a unique functionality called **Parallel I/O** or 'PIO' that is used to manipulate multiple I/O locations at the same time. Sega wired up these pins so the CPU can manipulate the GPU's **video mode** (more details are explained in the 'Graphics' section).

### End of the line

Even after all the advantages described, I'm afraid the SuperH series didn't progress significantly after its last major user, the Dreamcast, left the stores. After the popularity of the SH-4, Hitachi (or Renesas Electronics, the current owners) haven't been able to replicate the same level of success, and considering the embedded/handheld market has since favoured [ARM](game-boy-advance#cpu) (thanks to [StrongARM](nintendo-ds#arms-new-territories)), I don't see Renesas continuing Hitachi's invention anytime soon.

The good thing about computing, however, is that technological progress tends to spread beyond the boundaries of brands and companies. For instance, the SH's compressed instruction technique has carried on with ARM's [Thumb mode](game-boy-advance#tab-2-3-squeezing-performance) (a secondary 16-bit ISA) [@cpu-lwn]. Furthermore, in 2012, a volunteer-driven project was started to produce a modern SuperH-compatible CPU, called the 'J2' [@cpu-jcore].

## Graphics

The GPU package is a custom-made chip called **Holly** running at **100 MHz**. Unlike previous in-house designs, Sega now partnered with VideoLogic (now known as Imagination Technologies) to provide them with a competitive 3D accelerator.

![The Holly chip (after removing the thermal pads) and the video encoder.](holly.jpg)

Inside Holly, we can find VideoLogic's exclusive graphics circuit called **PowerVR2** (also referred to as 'PowerVR Series2' and 'CLX2'), it's based on their previous PowerVR GPUs but tailored for the Dreamcast.

### Architecture

VideoLogic chose an alternative approach for the construction of their 3D engine called **Tile-Based Deferred Rendering** (TBDR).

Instead of rendering a whole frame at once (as traditional **Immediate Mode Renderers** or 'IMR' do [@graphics-arch]), TBDR divides the rendering area into multiple sections called 'tiles'. Then, it carries out the rendering process on each tile individually and the result is combined to form the final frame [@graphics-powervr].

![Sonic Adventure (1999).](sonic.png)

This innovative design brings interesting advantages:

- It can be greatly **parallelised**, which significantly reduces bandwidth and power usage.
- It implements a clever solution to the [**visibility problem**](sega-saturn#an-introduction-to-the-visibility-problem) by automatically sorting the polygons **from front to back** and then performing [z-tests](nintendo-64#modern-visible-surface-determination) at the first stages of the pipeline. The combination of these tasks not only solves the original issue, but also **prevents overdraw** (rasterisation of hidden polygons) which wastes resources and degrades performance.

It's no surprise that Imagination took this efficient technology forward to build the Series 4 PowerVR cores which powered an incredible number of devices, including the first generation of iPhone, the iPhone 3G, the Nokia N95 and the Dell Axim x51.

### Construction

Let's take a look at the two main components of the Dreamcast's GPU [@graphics-marcus]:

#### Tile Accelerator {.tabs .active}

![Architecture of the Tile Accelerator.](tile_accelerator.png){.tab-float}

Before the rendering process starts, a component known as the **Tile Accelerator** performs pre-processing. It starts by allocating several 32x32 tile bins into which the geometry will be rendered.

Then, the Tile Accelerator will:

1. Grab the geometry data and drawing commands issued by the CPU (either using DMA or traditional transfers).
2. Convert this data into an *internal* format.
3. Distribute the geometry to each bin based on its coordinates. Clipped geometry will be discarded as well.
4. Generate the resulting Display Lists.

These Display Lists are then interpreted by the 3D engine: The PowerVR2.

#### PowerVR2 Core {.tab}

![Architecture of the PowerVR2 Core.](powervr2.png){.tab-float}

Here is where the graphics are brought to life, the Display Lists received from the TA tell the core to render the geometry of a single tile using an **internal frame-buffer**. The process is as follows:

1. The **Image Synthesis Processor** or 'ISP' fetches the primitives (either triangles or quads) and performs **Hidden-Surface Removal** to remove unseen polygons. Then, after calculating its Z-buffers and stencil buffers, the data goes through **Depth Testing** to avoid rendering polygons that would appear behind others and **Stencil Tests** to cull geometry that won't be visible if they are located behind a 2D polygon (also called **Mask**).
    - Notice how these tests are effectively carried out at the start of the pipeline. In contrast, previous consoles [using late z-buffering](nintendo-64#modern-visible-surface-determination) discard the geometry at the end of the pipeline. The ISP approach prevents processing the geometry that will eventually be discarded [@graphics-surface], thereby saving resources.
2. The **Texture and Shading Processor** or 'TSP' applies colouring, shading, and multiple effects over the tile area.
    - Textures are not applied until the tile is exported, meaning that emerging overdraw (if any) will not lower the fill rate.

After the operation is completed, the rendered tile is written to the main frame-buffer in VRAM. This process is repeated until all tiles are finished. Once complete, the resulting frame-buffer is picked by the **Video encoder** and sent through the video signal.

### The big picture {.tabs-close}

Apart from the clear architectural difference, the Texture and Shading Processor comes with many capabilities that give one an idea of how distant this console is from the old [Saturn](sega-saturn). Here are some notable examples:

- **Alpha blending**: Combines colours of overlapping layers to achieve transparency effects.
  - The process used for applying transparency in this system is called **order-independent transparency**. The algorithm automatically sorts the primitives before blending their colours, and while this slows down the rendering process, it avoids relying on the game itself to do all the sorting manually. For this reason, Dreamcast games excelled in displaying transparent objects.
  - Combined with the tile-based system, order-independent transparency completely addresses previous [mishaps](sega-saturn#the-transparency-issue).
- **Mip-Mapping**: Automatically selects a scaled-down version of the texture depending on the level of detail required. This is done to prevent processing large textures that would be seen far away from the camera (which would be a waste of processing power and produce aliasing).
- **Environment mapping**: Applies reflections on textures.
- **Bilinear, Trilinear and anisotropic filtering**: These are different algorithms used to smooth the textures and prevent pixelation. They are ordered from 'worst' to 'best', where the resulting quality of each one is directly proportional to the amount of computation required.
  - This is a huge step up from the Saturn since the former didn't provide any texture filter!
- **Bump mapping**: Simulates defects on surfaces without spending extra polygons.

### Gaining detail

Holly can now draw ~10 times more polygons than [its predecessor](sega-saturn), here's a *Before & After* example that shows how model designs are not that limited anymore. Try to fiddle with them!

![Sonic R (1997) for the Saturn.<br>286 triangles (or 185 quadrilaterals).](sonic_r_saturn){.toleft model3d="true"}

![Sonic Adventure (1999) for the Dreamcast.<br>1001 triangles.](sonic_adventure_dc){.toright model3d="true"}

### Video Modes

The video system was designed to support multiple types of screens and formats, thus the video encoder outputs to a single-shaped socket that supports the following type of signals:

- **Composite**: Combines the three signals needed to display video (chroma, luma and sync) into a single one, requiring only a single-pin cable.
  - This is used on old PAL and NTSC TVs with an RCA connection.
- **S-Video**: Combines luma and sync while keeping chroma separated (two video lines in total).
- **RGB**: Sends separate Red-Green-Blue signals and provides different sync types to choose from (composite sync or extracted from video composite or S-Video).
  - A SCART cable will use this type. 
- **VGA**: Combines RGB with two sync signals (horizontal and vertical) resulting in five video lines in total. This enables the display of the largest resolution possible (720x480) in progressive mode (thus, this mode is often named '480p'). VGA has actually been the standard format/medium used by computer monitors for some time.
  - To use this type, Sega provided a VGA adapter as an extra accessory.

Now, the Dreamcast can't encode all of these at the same time, so the GPU and the Audio processor contain a register called **Image Mode** that coordinates which video/audio buses will be activated to generate the requested signal. The CPU detects the type of cable inserted (by checking which 'select bits' of the video connector are active) and writes the required values on the GPU. Finally, the values are forwarded to the Audio processor.

Since VGA is strictly a progressive type of signal (as opposed to the traditional *interlaced*), some compatibility issues arose with games that were only designed for interlaced video. These explicitly state in their code that the game won't display on VGA, so the CPU will block the game until the user swaps out the VGA cable for another type.

## Audio

The Audio functionality is handled by a custom chip called **AICA** made by Yamaha, it's an improved version of the [SCSP used in the Saturn](sega-saturn#audio) and is composed of four components:

- The **Sound Integrated Circuit** or 'IC': A set of modules (synthesiser, DSP and mixer) that generates the audio signal and applies effects on it. It supports up to **64 PCM channels** with a resolution of **16 or 8 bits** and a sampling rate of **44.1 kHz**. Overall, this is the optimal quality for playing audio. 
  - Additionally, it includes an **ADPCM decoder** to offload some work from the CPU.
  - Curiously enough, it also provides **two MIDI pins** to connect a MIDI instrument, although this is meant to be used during development.
- **2 MB of SDRAM**: Stores sound data and programs. It's filled by the main CPU using DMA.
- An **ARM7DI** running at **~2.82 MHz**: Controls the Sound IC. This CPU is programmed by booting a small software (called [driver](super-nintendo#audio)) stored in SRAM which interprets the audio data and manipulates the Sound IC accordingly. 
  - If you wonder, a similar but beefier CPU is also found in the [Game Boy Advance](game-boy-advance).
- **Memory Controller**: Interfaces the 2 MB of SDRAM.

To help with development, the official SDK included multiple sound drivers for different needs (sequencing, decoding, etc).

### Evolution

We've come so far since the days of the [Mega Drive/Genesis](mega-drive-genesis#audio), in order to show how much progress was made in sound synthesis, here's an example of two games, one for the Mega Drive and the other for the Dreamcast, that used the same composition:

![Sonic 3D Blast (1996) for the Mega Drive / Genesis.<br>The predecessor performs FM synthesis to generate audio signals on the fly.](megadrive){.toleft video="true"}

![Sonic Adventure (1999) for the Dreamcast.<br>The new audio subsystem processes PCM samples without any hassle.](dreamcast){.toright video="true"}

You can also try this widget if you want to switch between the two while playing the score:

![**MegaDrive / Genesis:** Sonic 3D Blast (1996).<br>**Dreamcast:** Sonic Adventure (1999).](){audio_switcher="true" src1="megadrive" src2="dreamcast" label1="MegaDrive / Genesis" label2="Dreamcast" .centered-container .video-file}

#### Explanation

Instead of programming an FM chip, the composers of Sonic Adventure produced their soundtrack in-house and then encoded it to 'ADX', a lossy format developed by CRI Middleware. Hence, it only uses two of the 64 PCM channels (stereo).

ADX compression enables the game to decode and stream the data from the GD-ROM to the Sound IC without running out of memory or bandwidth. Furthermore, the respective driver could be implemented in many ways, as there were multiple approaches available to balance the workload of the main CPU and ARM7.

### Staying alive

Somehow, this chip is also responsible for providing a **Real Time Clock** (RTC) to the BIOS, it's also connected to a clock battery to continue working without AC power.

## Operating System

There are **2 MB of 'System ROM'** that stores a **BIOS**. This is the first location the CPU reads from when starting up. The ROM contains code that instructs the CPU to either bootstrap the game or show the shell.

Furthermore, the BIOS also contains routines that games may use to simplify I/O functions [@games-redream], like reading from the GD-ROM drive.

### Interactive shell

Much like the [Sega Saturn](sega-saturn#interactive-shell), if there isn't a valid game disc inserted, the console will launch a visual shell.

![Shell after booting without a disc.](shell.png)

The shell contains a simple graphical user interface to enable the user to perform basic but necessary tasks like:

- Start the game, if it hasn't already.
- Manipulate the save data stored in the VMU (more details about this device are explained later).
- Play music, if there's an Audio CD inserted.
- Change certain settings like date, time, sound and so forth.

### Windows CE

Ever since the Dreamcast's announcement, it was said that the console could run **Windows CE**. In fact, you only have to look at the console's front case to notice a stamp by Microsoft... What's going on here?

![The Windows CE seal on the front of the console. Coincidentally, you may find similar labels on other handheld equipment from the same era.](windows_ce.jpeg)

First things first, what exactly is 'Windows CE'? Well, it's just one of the many projects Microsoft embarked on during the mid-90s to conquer territories beyond the conventional PC market. In the wake of [more efficient CPU architectures](playstation#tab-1-1-a-bit-of-history) than x86 and the [emergence of handheld computing](game-boy-advance#tab-1-2-a-new-cpu-venture), a new team at Microsoft was tasked with bringing the Windows ecosystem into those new devices [@operating_system-ce_one].

This led to the creation of Windows CE: a complete operating system, developed from the ground up, that provided a subset of the existing Windows APIs, development tools and services.

![Windows CE 3.0 (2000), bundled with a desktop and a couple of business apps.](wince_desktop.png)

Unlike Windows 95 or NT, the new system presented the following advantages:

- It ran on a wide range of CPU architectures [@operating_system-ce_archs], including the SuperH and even the [NEC/MIPS VR4300](nintendo-64#cpu) (used by the Nintendo 64).
- It was designed with constrained hardware in mind, such as a limited power source (AA batteries), memory (2 MB RAM) and storage (4 MB ROM).

Moreover, Windows CE was sold as a set of **building blocks** for manufacturers, allowing them to cherry-pick which components to bundle. Manufacturers could then integrate it into a variety of hardware (such as handhelds, point-of-sale terminals or even automotive systems). Nevertheless, Windows CE gained significant notice as the OS powering Palmtops (the precursor of PDAs), effectively competing against the Apple Newton and Palm.

As the cellphone industry surged throughout the late 90s, Microsoft made use of Windows CE's modularity to build **PocketPC**, another operating system (later known as 'Windows Mobile') entirely focused on PDAs and phones.

::: {.subfigures .side-by-side max_subfigures=1}

![Windows CE 2.11 'Palm-size PC Edition' 1.2 (1999)](windowsce_pocket.png){.toleft}

![PocketPC (2000), based on Windows CE 3.](pocketpc2000.png){.toright}

Microsoft's attempts to push Windows CE into the PDA market.

:::

Now, what does all of this have to do with the Dreamcast? At some point in time, Microsoft and Sega ventured the idea of producing a Windows CE package for the Dreamcast [@games-sdk]. After all, Windows CE was already running on top of Hitachi's CPUs. In the end, this didn't materialise in a full desktop interface or integration with Microsoft's online services. Instead, it served as an **optional layer of abstraction to simplify hardware operations**.

Similar to what Nintendo provided with [the Nintendo 64](nintendo-64#operating-system), Microsoft shipped an SDK (commonly referred to as **Dragon SDK**), based on Windows CE, to program applications for the Sega Dreamcast. The libraries comprised a subset of CE with the minimal components needed to assist with graphics, audio and debugging. As part of 'joining the Microsoft club', developers could now make use of Microsoft's star IDE (**Visual Studio 6.0**) and **Visual C++ 6.0** for development.

Some developers found this option very attractive. Since the audio/graphics framework included with CE was none other than **DirectX 6**, plenty of PC games could, in theory, be easily ported to the Dreamcast. However, the architectural differences between the Dreamcast and the conventional PC were too great to ignore. So, in the end, programmers had to go through great deals of optimisation to reach optimal performance [@games-direct]. Furthermore, since the Dreamcast's BIOS didn't bundle any of Microsoft's frameworks, the SDK had to be statically linked to the game. Thus, compared to Sega's closer-to-metal libraries, Dragon SDK increased the game's loading times (after all, a separate 'OS' had to be loaded from the game disc) and, throughout the game's execution, the Windows CE layer happened to eat a substantial amount of resources.

In conclusion, 'Windows CE for Dreamcast' was just a secondary SDK of choice for developers. Nonetheless, a considerable number of Dreamcast games ended up making use of it.

If you're curious about the fate of Windows CE and Windows Mobile, things didn't go particularly well for Microsoft in the handheld market. Windows CE was abandoned in 2013. The year before, the CE-based core of Windows Mobile was replaced with Windows NT (aligning with the desktop-based Windows 8). Nevertheless, Windows Mobile eventually accepted defeat against newer platforms (Android and iOS) and was discontinued in 2019.

## I/O

The GPU also includes another module for handling most of the I/O called **System Bus**. It provides the following interfaces:

- The **G1** interface: Where the **BIOS ROM** along with its saved configuration and the **GD-ROM** content can be accessed.
- The **G2** interface: Provides access to the **Modem** and **Sound Controller**.
- The **Maple** interface: Transfers chunks of data between the controllers (along with the accessories connected to them) and the CPU. It's a **serial bus** and provides a dedicated DMA.
- The **SH-4** interface: Connects the main CPU for general-purpose communications.
- The **DDT** interface: Takes control of the CPU bus to access its main memory during DMA transfers.
- The **PVR** interface: Connects the CPU with the Tile Accelerator using a dedicated DMA.

## Games

Development was mainly done in **C** or **C++**. At first, C was the recommended choice since the available C++ compilers were initially very limited in functionality (and quality).

Sega also provided development hardware in the form of a PC-like tower called the **Sega Katana Development Box**. This houses Dreamcast hardware with enhanced I/O for development. It also shipped with a CD containing the official **Katana SDK** to be installed on a Windows 98 PC.

As mentioned before, developers also had the choice of adopting Microsoft's Dragon SDK on top of Sega's. In that case, DirectX 6.0 and Visual C++ 6.0 would be available.

### Medium

Games are stored in GD-ROMs, which are just [CD-ROMs](sega-saturn#the-compact-disc-cd) with a higher density of pits (reaching a gigabyte of capacity). The speed is 12x, which is *not too shabby* compared to Saturn's 2x CD reader.

### Online platform

The Dreamcast shipped with a **modem** module that allowed games to be 'dial-up' connected to Sega's online gaming platform. The platform was known as SegaNet in America and Japan and as Dreamarena in Europe. Players could register for the service using **DreamKey**, a disc that also provided NetFront web browser for account registration. At first, DreamKey was pre-configured for a specific region, but later versions allowed users to change the ISP settings to connect to any of the services.

Sega worked with GTE Internetworking on the technology backbone for SegaNet, which provided high-performance connectivity for national and local access. The KAGE API was the main component of Sega's attempt to make the Dreamcast successful as an online platform. KAGE was Sega's own API specifically for accessing the gaming features of SegaNet, and developers could make use of it to ensure games featured the lowest latency on the internet. Sega aimed to get the latency below 200 milliseconds, which is lower than typical internet connections. Development support was provided by SOA and Sega.com to help developers work with online games. KAGE was made available later that month.

Because it used dial-up, Sega released an accessory called the '**DreamPhone**'. This enabled you to talk on the phone while playing games. It was available directly from Sega using 4000 Dream points accumulated through Dream point certificates included with software sold only in Japan. It is extremely rare and little is known about it, as it was only available from Sega.

You can replace the modem adapter with a broadband adapter to get faster connections. However, Broadband adapters were not sold in Europe, but they were not region coded. But if you do so, you cannot talk to DreamPhone, and only a few games supported the broadband adapter. Broadband users would probably end up with an advantage over modem adapter users. A game lobby will determine if someone has a broadband connection and will prevent someone with a broadband adapter from joining the lobby of someone with a modem adapter. And vice versa.

To enhance the browsing experience, a Dreamcast-branded keyboard and mouse were also available for purchase, in case users preferred a PC-style browsing experience. The mouse also had one more button called 'programmable button', 'side button' or 'thumb button'. But it is not clear how to program this programmable button. At the same time, some developers integrated the Mouse and Keyboard into their games. Games like Quake 3, Unreal Tournament, and Outtrigger are some of them.

- **European Online Service**

  The European online service, Dreamarena, was a free dial-up-based platform created through a partnership between ICL, BT, and various ISPs. Although the service was free in the UK, other European countries had different access requirements and prices, which made the service expensive for some users. By September 2000, Dreamarena had reached 300,000 registrations, with over half of these users being "active or very active."

- **America Online Service**
  
  Sega announced a new business strategy in 2000 with the creation of a new company called Sega.com, Inc. The company was designed to function as an internet service provider (ISP) and launch an internet service called "SegaNet" later that year. Sega.com's website would be featuring chat, instant messaging, message boards, and other functionality. The company aimed to attract a user base similar to that of AOL. By signing up for two years of SegaNet service, users received a free Dreamcast system and keyboard.
  
  SegaNet was launched in September 7, 2000. SegaNet was not free, and players needed to pay a monthly subscription fee to use the service. The subscription cost $21.95 per month, and it provided unlimited access to the service. Including online access for the games. A puzzle game, Sega Swirl, that was given away for free to SegaNet subscribers. However, the subscription fee for SegaNet was seen as a major drawback by some players.

  In July 2001 Sega made an agreement with ISP provider Earthlink. With this agreement EarthLink became SegaNet's preffered ISP. Existing SegaNet ISP subscribers were seamlessly transitioned to EarthLink accounts and were able to continue enjoying the online console gaming experience of SegaNet for only $19.95 per month.
  
  The service was not just for Dreamcast owners. PC owners could also sign up and use the service on their PCs instead of, or in conjunction with, their existing ISP. Subscribe to the service and receive a free Dreamcast, or if you already owned a Dreamcast, receive a cheque from Sega for $200, the price of your Dreamcast purchase.
  
  SegaNet reached 100,000 subscribers a month after its launch

- **Japan Online Service**

  In Japan DreamKey came free with the Dreamcast.

- **Australia and New Zeland Online Service**

  In Australia and New Zeland, Sega partnered with Telstra to develop an online service for the Dreamcast, but it was unavailable at launch due to delays in development and testing. Users had to use Telstra's Big Pond service and the Internet access disc came with 150 hours of free Internet access. The browser went to the Comma web portal powered by LookSmart's search engine.

Despite the popularity, both SegaNet and Dreamarena faced criticism and legal issues. SegaNet's online service was discontinued on July 20, 2001, and Dreamarena's was discontinued on February 28, 2002. However, the online game servers for both platforms remained active until September 30, 2003. Users who did not register for the service before its closure were unable to access the internet using the Dreamcast. The SegaNet platform was shut down on September 30, 2003, and Dreamarena was shut down on February 28, 2003. Thus, games that exclusively relied on them became unusable, unless such services are emulated using extra tools (like the DreamPi, a Raspberry Pi image that replicates them with the help of servers maintained by a community of users).

- **Taiwan**
  In Taiwan, Sega partnered with GigaMedia. It also included localized web site that features game summaries, online community services, e-commerce services and other online game content services as featured on America and Europe.
  
### Interactive memory card

Another innovative feature of the Dreamcast was the **Visual Memory Unit** or 'VMU'. It is attached to the controller and, aside from serving as a memory card, is a fully-fledged device that includes [@games-vmu]:

![VMU detached from the controller.](vmu.png){.tabs-nested .active .open-float .tab-float title="VMU"}

![Controller without VMU attached.](controller.png){.tab-nested title="Detached"}

![Controller with VMU attached.](controller-vmu.png){.tabs-nested-last title="Attached"}

- A **Sanyo LC86K87**: An 8-bit low-power CPU.
- A **32x48 Monochrome LCD** with four additional icons: Commanded using 196 B of XRAM (eXternal RAM) as a frame buffer.
- **Two serial connectors**: One for IN and the other for OUT.
- **Six physical buttons**: Used when the VMU is detached from the controller.
- A **16 KB Mask-ROM**: Stores the BIOS-IPL.
- **64 KB of Flash**: 32 KB for storing a single program (transferred from the console) and the other 32 KB for keeping Dreamcast's saves.
- **512 B of RAM**: 256 B is reserved for the system, leaving only 256 B available for the program.

{.close-float}

The VMU has two modes of operation:

- **Attached to the controller**: The official controller has two slots to connect VMUs and other accessories with the same shape, if the VMU is inserted on the first slot (visible from the front of the controller), it can display drawings during gameplay. Moreover, the Dreamcast can store saves and a program on the VMU.
- **Detached from the controller**: The gadget becomes a Tamagotchi-like device with a clock and save manager, and can also run whatever program the Dreamcast previously transferred. Two VMUs can be connected to share content as well.

## Anti-Piracy & Homebrew

Using the proprietary GD-ROM format helped to inhibit the production of unauthorised copies of games (and running them on other consoles). Dreamcast games are also *region-locked* meaning that a console will refuse to run a game intended for a different region.

### Defeating it

In practice, the anti-piracy measures were *utterly* useless due to Sega leaving a huge backdoor open: **MIL-CD**. Music Interactive Live-CD or 'MIL-CD' is a format created by Sega to extend an Audio-CD with interactive programs... and the Dreamcast is compatible with it [@anti_piracy-history].

Eventually, unauthorised commercial discs (cheat loaders, movie players, etc) disguised as MIL-CDs ran on the console without Sega's approval. Later on, different hacking communities dissected this exploit and came up with a workaround to boot pirated games using CD-ROMs. This caused an unstoppable wave of ISOs to be released on the net.

Some problems surfaced afterwards: Although GD-ROMs can store a gigabyte of data, CD-ROMs can only fit ~700 MB, so how could 'rippers' shrink the bigger games to fit on a CD? By re-compressing music and graphics until it fits. They may even try to split it into two discs. After all, game data is not a single blob anymore (like on an old cartridge), but is now organised hierarchically into files and directories.

## That's all folks

![A Dreamcast I had to get in order to write lots of stuff here.<br>Not too bad for its age!](folks.png)

I hope you enjoyed reading the article. I finished writing it at the start of my final year at uni.

I'll probably be very busy from now on, but I do enjoy writing these articles so hopefully you'll get the next one in a few weeks!

Until next time!  
Rodrigo
