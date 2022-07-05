---
name: Mega Drive / Genesis
subtitle: New techniques of composition
date: 2019-05-18
releaseDate: 1988-10-29
generation: 4
cover: megadrive
top_tabs:
  Models:
    - title: "Japanese"
      caption: "The Mega Drive
      <br>Released on 29/10/1988 in Japan"
    - title: "American"
      caption: "The Genesis
      <br>Released on 14/08/1989 in America"
    - title: "European"
      caption: "The Mega Drive
      <br>Released on 09/1990 in Europe"
      active: true
  Motherboard:
    caption: "Showing the Japanese 'VA0' revision
    <br>Notice the unusual daughterboard on top of the VDP used to fix post-manufacturing glitches (properly corrected in later revisions)."
    extension: "jpg"
  Diagram: {}

# SEO
title: Mega Drive Architecture

# Historical
aliases: [/projects/consoles/mega-drive-genesis/]
---

## A quick introduction

Sega (and their TV ads) want you to know: Developers can't come up with decent games unless the console provides faster graphics and richer sounds.

Their new system includes lots of *already familiar* components ready to be programmed. This means that, in theory, developers would only need to learn about Sega's new GPU... right?

---

## CPU

This console has two general-purpose processors.

Firstly, we've got a **Motorola 68000** running at **~7.6MHz**, a popular processor already present in many computers at that time, such as the Amiga, the (original) Macintosh, the Atari ST... Curiously enough, each one of them succeeded its '6502 predecessor' and while the [Master System]({{< ref "master-system" >}}) (Mega Drive's precursor) doesn't use a 6502 CPU, the [NES]({{< ref "nes" >}}) did (and in some way, Sega's goal was to win Nintendo consumers over). All in all, you can see a bit of correlation between the evolution of computers and game console technology.

Back on topic, the 68k has the role of 'main' CPU and it will be used for game logic, handling I/O and graphics calculations. It has the following capabilities {{< cite "cpu-user" >}}:
  - **68000 ISA**: A new instruction set with plenty of features, including a set of multiplication and division instructions. Some instructions are 8-bit long (called 'byte'), others are 16-bit long (called 'word') and the rest are 32-bit long (called 'long-word').
  - **32-bit registers**: This is a big step, considering the 6502 and Z80 only have 8-bit registers.
  - **16-bit ALU**: Meaning it needs extra cycles to compute arithmetic operation on 32-bit numbers, but it's fine on 16-bit/8-bit ones.
  - External **16-bit data bus**: As you can see, while this CPU has some '32-bit capabilities', it hasn't been designed to be a complete 32-bit machine. The width of this bus implies better performance when moving 16-bit data around.
    - Interestingly enough, Motorola debuted a complete 32-bit CPU, the **68020**, four years before this console's release. But I imagine costs would've skyrocketed had Sega chosen the latter chip.
  - **24‑bit address bus**: This means that **up to 16 MB of memory can be accessed**. Memory addresses are still encoded with 32-bit values inside the CPU (the upper byte is just discarded). Having said that, the bus is physically connected to {{< cite "cpu-memorymap" >}}:
    - 64 KB of general-purpose RAM.
    - Cartridge ROM (up to 4 MB).
    - Two Controllers.
    - The Video Display Processor's registers, ports and DMA.
    - Motherboard's registers (identifies the console).
    - Expansion ports (used for 'future' accessories).
    - The second CPU's RAM, intermediated by the *bus arbiter*.

(If you wonder the reason behind using 24-bit addresses with a CPU that can handle 32-bit words, I doubt that in the 80s many were asking to manage 4 GB of RAM and adding unused lines is costly in terms of performance and money).

Secondly, there's another CPU fitted in this console, a **Zilog Z80** running at **~3.5 MHz**. This is the same processor found on the [Master System]({{< ref "master-system#cpu" >}}) and it's mainly used for **sound control**. It features {{< cite "cpu-z80manual" >}}:
  - **Z80 ISA**: An extension of the Intel 8080 ISA, it handles **8-bit** words.
  - **8-bit registers** and **8-bit data bus**: _No surprises here_.
  - **4-bit ALU**: This may be a bit shocking, but it managed to handle 8-bit operations without problems, it just takes twice the cycles to compute.
    - Notice how the 6502 runs at ~2 MHz in [some systems]({{< ref "nes#cpu" >}}) while this one almost reaches 4 MHz: Clock speed doesn't make the Z80 faster per se, but helps to balance the lack of transistors in some areas.
  - **16-bit address bus** with the following address map {{< cite "cpu-z80map" >}}:
    - 8 KB of RAM.
    - Two sound chips.
    - 68000's RAM (again, handled by the bus arbiter).

Finally, **both CPUs run in parallel**.

### Memory available

The main CPU contains **64 KB** of dedicated RAM to store general-purpose data and the Z80 contains **8 KB** of RAM for sound-related operations.

#### Intercommunication

Sega chose two independent processors that have **no awareness of each other**, so how can games manage both at the same time? Well, the main program is executed in the 68000, and this CPU can subsequently write on Z80's RAM. So, the 68000 can send a program to the Z80's RAM and instruct the Z80 to load it (by sending a reset signal to the Z80) {{< cite "cpu-using_z80" >}}. Once the Z80 is under control, it can then be used to manage the sound sub-system and move memory around using the previously described method, all of this while the 68000 is taking care of other operations.

{{< figure_img src="memory.png" alt="Memory Diagram" class="centered-container" img_class="no-borders" >}}
Memory architecture of the Mega Drive/Genesis
{{< /figure_img >}}

Because one CPU will have to step in the other's CPU bus and both can't use it at the same time, there's an extra component called **Bus arbiter** that must be activated to stall either processor, so memory can be written without hazards.

It's important to point out that this design can underperform if not managed properly, so games will have to take special care of the bus arbiter and watch for not stalling either CPU for longer than needed.

---

## Graphics

The answer is _Blast Processing!_, what else do you need to know?

Now, if you want to know the _real_ answer: graphics data is processed by the 68000 and rendered on a proprietary chip called **Video Display Processor** (or 'VDP' for short) which then sends the resulting frame (in the form of scan lines) for display.

The VDP runs at **~13 MHz** and supports multiple resolution modes depending on the region: Up to **320x224 pixels** in NTSC and up to **320x240 pixels** in PAL.

### Behind the multiple display resolutions

Technically speaking, the VDP can either fit 40 or 32 columns of [tiles]({{< ref "master-system#tiles" >}}) per scan-line, and the number of tile rows depends on the region (28 in NTSC or 30 in PAL) {{< cite "graphics-keil" >}}. Although, most PAL games don't bother with the extra tiles allowed in PAL systems (as they probably need to keep consistency between the two regions, and NTSC is the common denominator) so they instruct the VDP to render with 28 rows (as they would do in NTSC systems). Thus, the VDP has no option but to fill the unused area with a backdrop colour (also used during overscan).

You can see which PAL games render in NTSC mode by checking the `Mode Set Register #2` in an emulator with debugging capabilities (i.e. Exodus). If the fourth bit from the right is `0`, then the VDP is running in NTSC mode {{< cite "graphics-resolution" >}}.

Furthermore, there's an additional parameter that can be set on the VDP to stack two tiles to form **8x16 maps** and then treat them as a single tile. Hence, doubling the vertical resolution. However, this halves the refresh rate as frames are now rendered with interlacing (one frame renders even scan-lines, the next beams odd ones, and so forth) so it's more limited in terms of functionality. The multiplayer mode of Sonic 2 and 3 are a good representation of this mode.

Finally, it's worth pointing out that the VDP automatically takes care of adding padding for the overscan area, so games don't have to worry about which areas are safe to draw graphics into (as it happened with the [NES' 'danger zones']({{< ref "nes#constructing-the-frame" >}}))

### Organising the content

In terms of processing the graphics data, this chip has two modes of operations:
- **Mode IV**: Legacy mode that behaves like its [predecessor]({{< ref "master-system#graphics" >}}).
  - This doesn't mean this console will play Master System games right away, as an additional accessory (the *Power Base Converter*) is required to fit previous cartridges on this console. The converter will also instruct the I/O chip to put the Z80 in control.
- **Mode V**: Native mode of operation, we'll focus on this one.

What about Mode 0 to III? Well, these belong to the even older *SG-1000* and the Mega Drive doesn't support them.

As an interesting note, I've been later told by a former developer of this system that the command structure of Mode V (used to control the VDP within the game) inherits the design from the TMS9918 (the famous video chip used in the SG-1000) {{< cite "graphics-clarification" >}}. This made it easier for third-party developers to use Mode V without depending on the official documentation (and subsequent licensing costs).

#### Memory available

{{< figure_img src="VDP_architecture.png" alt="VDP Diagram" class="centered-container" >}}
Memory architecture of the VDP
{{< /figure_img >}}

The graphics content is distributed across three regions of memory {{< cite "graphics-keil" >}}:
- 64 KB **VRAM** (Video RAM): Contains most of the graphics data.
- 80 B **VSRAM** (Vertical Scroll RAM): The VDP supports vertical and horizontal scrolling and V-scroll values are stored in this separate space, for some reason.
- 128 B **CRAM** (Colour RAM): Stores four palette entries with 16 colours each (including `transparent`), the system provides 512 colours to choose from. Additionally, *Highlight* and *Shadow* effects can be applied to each palette to achieve a wider range of colours per palette.

### Constructing the frame

The following section explains how the VDP draws each frame, for demonstration purposes *Sonic The Hedgehog* is used as an example. Before starting, I recommend checking out the _modus operandi_ of its [predecessor]({{< ref "master-system#graphics" >}}) since there will be a lot revisited in here.

{{< tabs >}}
{{< tab name="Tiles" active="true" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="All" active="true" src="vdp_sonic/tiles.png" >}}
Multiple tiles squashed together.  
For demonstration purposes, a default palette is being used.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Single" src="vdp_sonic/tiles_single.png" >}}
A single 8x8 pixel tile.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}
Some tiles found in VRAM.
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Just like Nintendo's PPU, The VDP is a tile-based engine and as such it uses **tiles** (basic 8x8 bitmaps) to compose graphic planes. In the case of the VDP, each tile is encoded with a 4-byte-long array, where each 4-bit entry corresponds to a pixel and its value corresponds to a colour entry (pointing to a colour palette).

Game cartridges stores tiles in their **ROM** (found in their cartridge) but they have to be copied to **VRAM** so the VDP can read them {{< cite "graphics-ports" >}}. Traditionally, this was only possible during specific time frames and handled by the CPU, fortunately, this console added special circuitry to offload this task to the VDP (we'll get into details later on).

Tiles are used to build a total of **four planes** which, once merged together, form the frame seen on the screen. Also, planes' tiles will overlap with each other, so the VDP will decide which tile is going to be visible based on the type of plane and the tile's **priority value**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background" >}}

{{< tabs nested="true" class="pixel" float="true" >}}
  {{< tab_figure_img name="Full" active="true" src="vdp_sonic/layer2.png" >}}
Allocated Background map.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Selected" src="vdp_sonic/layer2_selected.png" >}}
Allocated Background map with selected area marked.
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
The Background plane, also known as **Plane B** is a scrollable tilemap (set of tiles) containing **static tiles** {{< cite "graphics-macdonald" >}}.

This plane can have six different dimensions: 256x256, 256x512, 256x1024, 512x256, 512x512, 1024x256. Programmers may select the dimension that adheres better to the type of scrolling that will be required.

Each tile can be **flipped horizontally and/or vertically** and have a **priority** set.

On the showed example you'll notice that the selected area for display is not a square... *It doesn't have to be!*. The VDP allows to set up horizontal scrolling values for the whole frame, each individual scan-line or every eight pixels. This means that developers can shape the selected area like a rhomboid and alter its angles as the player moves to simulate **perspective effects**. Tricks like this one don't corrupt the plane, the VDP fetches each (selected) horizontal line and builds a regular frame from it.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Foreground" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Full" active="true" src="vdp_sonic/layer1.png" >}}
Allocated Foreground plane.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Selected" src="vdp_sonic/layer1_selected.png" >}}
Allocated Foreground plane with selected area marked.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Example of Foreground plane, the Window Plane is not used.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
The Foreground plane, also known as **Plane A** {{< cite "graphics-macdonald" >}}, has the same properties as the Background Plane except this plane has a **higher priority**, so tiles rendered here will inherently be on top of the Background Plane.

Additionally, this plane allows to divide itself to form a new *sub-plane*: The **Window Plane**. The only difference is that the latter doesn't scroll.

All in all, you can see the new priority values and separate planes enable game designers to bring new types of scenery. Furthermore, by using different scroll speeds on each plane, a **parallax effect** can be achieved.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}

{{< tabs nested="true" float="true" class="pixel" >}}
  {{< tab_figure_img name="Full" active="true" src="vdp_sonic/sprite.png" >}}
Allocated Sprite layer.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Selected" src="vdp_sonic/sprite_selected.png" >}}
Allocated Sprite layer with selected area marked.
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
In this plane, tiles are treated as **sprites**. They are positioned in a **512x512 pixel** map and only a part of it (VDP's output resolution) is selected for display. This is convenient for hiding unwanted sprites or preparing others that will be shown in the future. The VDP also provides an old [collision detection]({{< ref "master-system#tab-4-1-collision-detection" >}}) function.

Sprites are formed by combining up to 4x4 tiles (32x32 pixel map) and selecting up to 16 colours (including *transparent*). If a bigger sprite is needed, then multiple sprites can be combined into one.

There can only be a maximum of 20 sprites per scan-line and 80 per screen (overflowing this will corrupt the whole layer).

The region in VRAM where Sprites are defined is called **Sprite Attribute Table** {{< cite "graphics-macdonald" >}} and each entry contains the tile index, layer coordinates (x and y), `link` value (manages which sprites are drawn first), priority (the sprite with the highest priority is the one to be displayed during overlaps), colour palette index and vertical and horizontal flip.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Frame" class="pixel" src="vdp_sonic/result.png" alt="Result" active="true" >}}
Resulting frame.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="With overscan" src="vdp_sonic/overscan.png" >}}
Frame broadcasted to the TV (NTSC format), the VDP automatically covers the frame with overscan area that most CRT TVs will hide.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Tada!{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
While the frame is being drawn, the system will sequentially call different interrupt routines depending on where the CRT's beam is pointing to. As you have probably seen in previous consoles, this allows the CPU to work on the next frame (or alter the current one).

Conventionally, there are two types of interrupts called: **H-Blank** (every horizontal line) and **V-Blank** (every frame).

H-Blank is called numerous times but is limited to executing short routines. Also, only CRAM and VSRAM are accessible, so games can only update their colour palettes or vertically scroll their planes.

V-Blank allows for longer routines with the drawback of being called only 50 or 60 times per second (depending on the console's region), but it's able to access all memory locations.

Notice that the overscan area in the example exhibits some random coloured dots at the bottom right corner. This is popularly known as **CRAM dots** and what's happening is that the CPU is updating the palettes in CRAM at the same time the VDP is beaming the remaining scan-lines (in the example, this happens during overscan). This conflict makes the VDP fetch whatever value the CPU is writing at that time (as opposed to the required location in CRAM) so the image gets corrupted. However, since in this case the game only updates CRAM at overscan, this anomaly goes unnoticed on traditional CRTs. Other games attempt to update the palettes mid-frame to achieve more colours, with the cost of having to balance the appearance of CRAM dots.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

### A dedicated transfer unit

So far we've discussed what the CPU can do to update frames, but what about the VDP? Does it provide something more specialised? Well yes, this chip features a **Direct Memory Access** ('DMA' for short) that allows moving data between memory locations at a **faster rate** and **without the intervention of the CPU**.

The DMA can be activated during H-Blank, V-Blank or active state (outside any interrupt), and can be used to write over VRAM, CRAM, and/or VSRAM {{< cite "graphics-macdonald" >}}. Additionally, during CPU RAM transfers using DMA, the CPU bus will be blocked, so good planning is critical to achieving performance.

Exceptional use of these features may provide high-resolution graphics, fluid parallax scrolling and high frame rates. Moreover, your game may also be featured on TV ads with lots of *Blast Processing!* signs all over it.

### Video Output

The first design of this console (commonly referred to as the 'Model 1') has the same video out port of [the Master System]({{< ref "master-system#video-output" >}}). The subsequent 'Model 2' and 'Model 3' switched to a mini-DIN port instead.

---

## Audio

The audio capabilities of this console are a bit unorthodox, to say the least. On one side, the Mega Drive provides existing audio technology from the previous generation, on the other side, it adds a new (but complicated) synthesis technique on top of the existing one. So, in some ways, you get both generations.

That being said, the Mega Drive houses two sound chips: A **Yamaha YM2612** and a **Texas Instruments SN76489**.

### Functionality

Let's now see what each chip offers, as each one is *very* different.

{{< tabs >}}
{{< tab active="true" name="Yamaha YM2612" >}}
{{< tabs float="true" nested="true" figure="true" >}}
  {{< tab_figure_video name="FM" active="true" src="fm_single" >}}
FM channels.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="PCM Sample" src="pcm_single" >}}
PCM channel.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Sonic The Hedgehog (1991).{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
The **Yamaha YM2612** is an **FM synthesiser** {{< cite "audio-ymwiki" >}} that runs at the 68000 speed and supplies **six FM channels**, where one can play **PCM samples** (with 8-bit resolution and 32 kHz sampling rate).

Frequency modulation or 'FM' synthesis is one of many professional techniques used for synthesising sound, it significantly rose in popularity during the 80s and made way to completely new sounds (many of which you can find by listening to the pop hits from that era).

In an *incredibly simplified* nutshell, the FM algorithm takes a single waveform (**carrier**) and alters its frequency using another waveform (**modulator**), and the result is a new waveform with a different sound. The carrier-modulator combination is called **operator** and multiple operators can be chained together to form the final waveform. Different combinations achieve different results. This chip allows 4 operators per channel.

Compared to traditional PSG synthesisers, this was a drastic improvement: You were no longer stuck with *pre-defined* waveforms.
{{% /inner_markdown %}}

{{< /tab >}}
  
{{< tab name="Texas Instruments SN76489" >}}
{{< video float="true" src="psg_single" >}}
PSG Channels.  
Sonic The Hedgehog (1991).
{{< /video >}}

{{% inner_markdown %}}
The **Texas Instruments SN76489** is a **PSG chip** that can produce three pulse waves and one noise.

This is actually the original Master System's [sound chip]({{< ref "master-system#audio" >}}) and it's embedded in the VDP. It runs at the speed of the Z80.

Notice the 'Pulse 3' channel remains unused. This is because the game uses a mode for the noise channel that reserves the third pulse channel for modulation {{< cite "audio-sonic" >}}, a function featured on the Master System as well.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Mixed" >}}
{{< video src="complete" float="true" >}}
All audio channels.  
Sonic The Hedgehog (1991).
{{< /video >}}

{{% inner_markdown %}}
Both chips can output sound at the same time, then an extra component called 'audio mixer' is tasked with receiving both signals and mixing them.

Finally, the resulting analogue signal is sent through the audio output.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### The conductor

In theory, the Z80's memory map suggests the Z80 is the **only CPU** capable of commanding those two chips (which may a relief for the 68000 since the latter is already fed up with other tasks). In practice, however, the Z80 can be shut down so the 68000 has access to the YM2612 (but not the SN76489) {{< cite "audio-68kym" >}}. So, for simplicity purposes, in this article, we are going to assume audio tasks are designated to the Z80.

Moving on, the Z80 is an independent processor by itself, so it needs its own program (stored in the 8 KB of RAM available) to be able to interpret the music data received from the 68000 and effectively manipulate the two sound chips accordingly. This program is called **sequencer** or **driver**.

### Cracking sampling

Some music composers may decide to focus on the PCM channel to play more truthful sounds, and for that, games will need to continuously sequence and stream their music using the rest of RAM available. The main constraint is that to fill that memory, the main bus has to be **blocked** first (so no commands or samples can be sent to the audio chips during that timeframe). Otherwise, sound anomalies may appear (muting, frozen notes, low sample rates, etc).

For that reason, I've decided to dedicate this section for a few instances of games which successfully managed to overcome the aforementioned constraint. Instead of just sticking with ordinary drum kits, some games found incredible ways to stream richer samples to that single PCM channel, check out these examples:

{{< side_by_side >}}
  {{< tabs nested="true" class="toleft" figure="true" >}}
    {{< tab_figure_video name="PCM Sample" active="true" src="good_sampling/sonic_pcm" >}}
PCM channel.
    {{< /tab_figure_video >}}
    {{< tab_figure_video name="Complete" src="good_sampling/sonic_complete" >}}
All audio channels.
    {{< /tab_figure_video >}}
    {{< figcaption group="true" >}}
Sonic The Hedgehog 3 (1994).  
This is one of the tracks said to be co-authored by Michael Jackson. In any case, the overall soundtrack had a distinctive beat compared to its predecessors.
    {{< /figcaption >}}
  {{< /tabs >}}
  {{< tabs nested="true" class="toright" >}}
    {{< tab_figure_video name="PCM Sample/Complete" active="true" src="good_sampling/randy_pcm" >}}
PCM channel.  
(Only one channel is used).  
Toy Story (1995).  
This is sequenced in real time with the help of the 68000 {{< cite "audio-gamehut" >}}. A very intensive task, meaning it could only be played at very particular points of the game (i.e. the main menu)
    {{< /tab_figure_video >}}
  {{< /tabs >}}
{{< /side_by_side >}}

I know they are nowhere near CD-quality (16-bit at 44.1 kHz), but bear in mind those sounds were once deemed impossible to reproduce in this console and I'm not even emphasising how much progress this represents compared to the previous generation, so they certainly deserve some merit at least!

### Assisted FM Composition

If programming an FM synthesiser was already considered complicated using the Yamaha DX7's dashboard, imagine the headaches of composing music with only 68k assembly...

Luckily, Sega later distributed a piece of software for MS-DOS PCs called **GEMS** to facilitate the composition (and debugging) of Mega Drive music {{< cite "audio-gst" >}}. It was a very complete tool, among many things it included lots of *patches* (pre-configured operators to choose from), which would also explain why some games have *very* similar sounds.

Moreover, the audio subsystem enabled games to instantiate more channels than allowed and assign each one a **priority value**, then when the console would play the music, it dynamically dispatched the music channels to the available slots based on priorities. Additionally, channels with a high priority but without music could be automatically skipped.

Channels also contained some **logic** by implementing conditionals inside their data, enabling music to 'evolve' depending on how the player progresses in the game.

### (Bonus) Mega CD Sound

Here's an interesting fact: The Mega CD add-on provided 2 extra channels for CD Audio (among other things). One of its most famous games, Sonic CD, had very impressive music quality but like all games it had to loop, the problem was that looping music on a 1x CD reader exposed noticeable gaps, so the game included loop fillers that were executed from another PCM chip while the CD header was returning to the start.

These fillers are only found on early betas of the game and they didn't make it for the release, the 2011 remake finally included them. This is one of the levels of the game:

{{< side_by_side >}}
  {{< audio src="sonic_cd_original" class="toleft" >}}
MegaCD version (1993)
  {{< /audio >}}
  {{< audio src="sonic_cd_remaster" class="toright" >}}
Remastered version (2011)
  {{< /audio >}}
{{< /side_by_side >}}

Do you notice the gap in the Mega CD's version?

---

## Games

Games are mainly written in **68000 assembly** while the sound driver is implemented with **Z80 assembly**. Both reside in the cartridge ROM and can size up to 4 MB without the need for a [mapper]({{< ref "nes#going-beyond-existing-capabilities" >}}).

### Extra functions

In terms of expandability, this design wasn't as modular as the [NES]({{< ref "nes" >}}) or the [SNES]({{< ref "super-nintendo" >}}). Hence, laters add-ons like the 32x (which included a new chipset that takes over the 68k) had to bypass the VDP (hence the need for the 'Connector Cable').

Only one custom chip was produced for cartridges, the **Sega Virtua Processor** {{< cite "games-virtua" >}} (a rebranding of the Samsung SSP1601, a 16-bit Digital Signal Processor), which produced polygons subsequently encoded in the form of tiles (so the VDP can read them). In any case, only one game shipped with it, as the SVP turned out very expensive to produce.

### Early network attempts

Before online services became widely adopted (and standardised), Sega tried its luck with **Sega Meganet**, a dial-up service for games to use. Meganet required users to purchase a separate accessory called _Sega Mega Modem_, then plug it in at the back of the console (where the DE-9 connector was found) and finally, connect it to the phone line. Games would then treat the modem unit as another controller, with the addition of communicating with it serially {{< cite "games-ioports" >}} (as opposed to the parallel encoding used for the controllers). 

Be as it may, this feature only lasted a couple of years before Sega removed the DE-9 connector on future revisions and shut down the service altogether.

---

## Anti-piracy / Region Lock

To block imported games, Sega slightly changed the shape of the cartridge slot between regions though it kept the same pinouts. Games could also perform 'region locking' by checking the value of the `Version` register (which outputs the region value).

There were two easy ways to bypass this. First, by buying one of those third-party cartridge converters. Or second, by disassembling the console and bridging the pins on the motherboard that alter the value of the `Version` register.

When it comes to software anti-piracy measures, the easiest check was on the SRAM size: Bootleg cartridges had more space than needed to fit any game, so games checked for the expected size on the startup. Programmers could also implement extra checksum checks at random points of the game in case hackers were to remove the initial SRAM checks. The only way to defeat this was to tediously find all the checks and remove them one by one...

---

## That's all folks
