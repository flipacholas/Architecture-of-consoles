---
name: Mega Drive / Genesis
subtitle: New techniques of composition
date: 2019-05-18
releaseDate: 1988-10-29
generation: 4
cover: megadrive
javascript: ['plyr']
top_tabs:
  Models:
    European:
      caption: "The Mega Drive, released in 09/01/1989"
    American:
      caption: "The Genesis, released in 14/08/1989"
    Japanese:
      caption: "The Mega Drive, released in 29/10/1988"
  Motherboard:
    caption: "Showing Japanese model 'VA0'
    <br>Later revisions fixed problems that had to be patched with extra circuitry"
    extension: "jpg"
  Diagram: {}

# SEO
title: Mega Drive Architecture
---

## A quick introduction

Sega (and their tv ads) want you to know: It's impossible to bring descent games without faster graphics and richer sounds.

Their new system includes lots of *already familiar* components ready to be programmed. This means that, in theory, developers would only need to learn about Sega's new GPU... right?

---

## CPU

This console has two *general purpose* processors.

Firstly, we've got a **Motorola 68000** running at **~7.6MHz**, a popular processor already present in many computers at that time, such as the Amiga, the (original) Macintosh, the Atari ST... Curiously enough, each one of them succeeded its '6502 predecessor' and while the [Master System]({{< ref "master-system" >}}) (Mega Drive's precursor) doesn't use a 6502 CPU, the [NES]({{< ref "nes" >}}) did (and in some way, Sega's goal was to win Nintendo consumers over). All in all, you can see a bit of correlation between the evolution of computers and game console technology.

Back on topic, the 68k has the role of 'main' CPU and it will be used for game logic, handling I/O and graphics calculations. It has the following capabilities:
  - **68000 ISA**: A new instruction set with plenty of features, including a set of multiplication and division instructions. Some instructions are 8-bit long (byte), others 16-bit long (word) and the rest are 32-bit long (long-word).
  - **32-bit registers**: This is a big step, considering the 6502 and Z80 only have 8-bit registers.
  - **16-bit ALU**: Meaning it needs extra cycles to compute arithmetic operation on 32-bit numbers, but it's fine on 16-bit/8-bit ones.
  - External **16-bit data bus**: As you can see, while this CPU has some '32-bit capabilities', it hasn't been designed to be a complete 32-bit machine. The width of this bus implies better performance when moving 16-bit data around.
    - Interestingly enough, Motorola debuted a complete 32-bit CPU, the **68030**, two years before this console's release. But I imagine costs would've skyrocketed had Sega chosen the latter chip.
  - **24‑bit address bus**. This means that up to 16 MB of memory can be accessed, but addresses are still interpreted as 32-bit values inside the CPU (the upper byte is just discarded). The bus is physically connected to:
    - 64 KB of RAM.
    - Cartridge ROM (up to 4 MB).
    - Two Controllers.
    - **VDP**'s registers, ports and DMA.
    - Motherboard's registers (identifies the console).
    - Expansion ports (used for 'future' accessories).
    - Second CPU's RAM (Controller by a **bus arbiter**).

(If you wonder the reason behind using 24-bit addresses with a CPU that can handle 32-bit addresses, I doubt that in the 80s many were asking to manage 4 GB of RAM and adding unused lines is costly in terms of performance and money).

Secondly, there's another CPU fitted in this console, a **Zilog Z80** running at **~3.5 MHz**. This is the same processor found on the [Master System]({{< ref "master-system#cpu" >}}) and it's mainly used for **sound control**. It features:
  - **Z80 ISA**: An extension of the Motorola 6800 (not 68000!) ISA, it handles **8-bit** words.
  - **8-bit registers** and **8-bit data bus**: No surprises here.
  - **4-bit ALU**: This may be a bit shocking, but it managed to handle 8-bit operations without problems, it just takes two cycles per number.
    - Notice how the 6502 runs at ~2 MHz in [some systems]({{< ref "nes#cpu" >}}) while this ones almost reaches 4 MHz: Clock speed doesn't make the Z80 faster per se, but helps to balance the lack of transistors in some areas.
  - **16-bit address bus** with the following address map:
    - 8 KB of RAM.
    - Two sound chips.
    - 68000's RAM (again, handled by the **bus arbiter**).

#### Memory available

The main CPU contains **64 KB** of dedicated RAM to store general-purpose data and the Z80 contains **8 KB** of RAM for sound-related operations.

---

## Graphics

_Blast Processing!_

Graphics data is processed by the 68000 and rendered on a proprietary chip called **Video Display Processor** (or 'VDP' for short) which then sends the resulting frame for display.

The VDP runs at **~13 MHz** and supports multiple resolution modes depending on the region: Up to 320x224 pixels in NTSC and up to 320x240 pixels in PAL.

This chip has two modes of operations:
- **Mode IV**: Legacy mode that behaves like its [predecessor]({{< ref "master-system#graphics" >}}).
  - This doesn't mean this console will play Master System games automatically, an additional accessory (the *Power Base Converter*) is required to fit previous cartridges on this console, the converter will also instruct the I/O chip to put the Z80 in control.
- **Mode V**: Native mode of operation, we'll focus on this one.

What about Mode 0 to III? Well, these belong to the even older *SG-1000* and the Mega Drive doesn't support them.

#### Organising the content

{{< centered_container >}}
  {{< linked_img src="VDP_architecture.png" alt="VDP Diagram" >}}
  <figcaption class="caption">Memory architecture of the VDP</figcaption>
{{< /centered_container >}}

The graphics content is distributed across 3 regions of memory:
- 64 KB **VRAM** (Video RAM): Contains most of the graphics data.
- 80 B **VSRAM** (Vertical Scroll RAM): The VDP supports vertical and horizontal scrolling, V-scroll values are stored in this separate space.
- 128 B **CRAM** (Colour RAM): Stores four palette entries with 16 colours each (including *transparent*), the system provides 512 colours to choose from. Additionally, *Highlight* and *Shadow* effects can be applied to each palette to achieve a wider range of colours per palette.

#### Constructing the frame

The following section explains how the VDP draws each frame, for demonstration purposes *Sonic The Hedgehog* is used as example.

{{< tabs >}}
  {{< tab name="Tiles" active="true" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="All" active="true" >}}
      {{< linked_img src="vdp_sonic/tiles.png" >}}
    {{< /tab >}}
    {{< tab name="Single" >}}
      {{< linked_img src="vdp_sonic/tiles_single.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Tiles found in VRAM
  <br>(For demonstration purposes a default palette is being used)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Just like Nintendo's PPU, The VDP is a tile-based engine and as such it uses **tiles** (basic 8x8 bitmaps) to compose graphic planes. Each tile is coded in a simple 4-byte array where each 4-bit entry corresponds to a pixel and its value corresponds to a colour entry.

Game cartridges contain tiles in their **ROM** but they have to be copied to **VRAM** so the VDP can read them. Traditionally, this was only possible during specific time frames and handled by the CPU, fortunately this console added special circuitry to offload this task to the VDP (we'll get into details later on).

Tiles are be used to build a total of 4 **planes** which, merged together, form the frame seen on the screen. Planes' tiles will overlap with each other, so the VDP will decide which tile is going to be visible based on the type of plane and the tile's **priority value**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background" >}}


{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true" >}}
      {{< linked_img src="vdp_sonic/layer2.png" >}}
    {{< /tab >}}
    {{< tab name="Selected" >}}
      {{< linked_img src="vdp_sonic/layer2_selected.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Rendered Background</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Background plane, also known as **Plane B** is a scrollable *tilemap* containing **static tiles**.

This plane can have six different dimensions: 256x256, 256x512, 256x1024, 512x256, 512x512, 1024x256. The choice can be based on the type of scrolling that will be required.

Each tile can be **flipped horizontally and/or vertically** and have a **priority** set.

On the showed example you'll notice that the selected area for display is not a square... *It doesn't have to!* The VDP allows to set up horizontal scrolling values for the whole frame, each individual scan-line or for every eight pixels. This means that developers can shape the selected area like a rhomboid and alter its angles while the player moves in order to achieve a **Parallax effect**. Tricks like this one don't result in a distorted plane, the VDP fetches each (selected) horizontal line and builds a squared frame from it.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Foreground" >}}


{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true" >}}
      {{< linked_img src="vdp_sonic/layer1.png" >}}
    {{< /tab >}}
    {{< tab name="Selected" >}}
      {{< linked_img src="vdp_sonic/layer1_selected.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Rendered Foreground (No use of Window Plane)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Foreground plane, also known as **Plane A** has the same properties as the Background Plane except this plane has a **higher priority**, so tiles rendered here will inherently be on top of the Background Plane.

Additionally, this plane allows to divide itself to form a new *sub-plane*: The **Window Plane**. The only difference is that it doesn't scroll.

Compared to previous consoles, the combination of different priorities and the Window plane allows to achieve a more convincing illusion of depth.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}


{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true" >}}
      {{< linked_img src="vdp_sonic/sprite.png" >}}
    {{< /tab >}}
    {{< tab name="Selected" >}}
      {{< linked_img src="vdp_sonic/sprite_selected.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Rendered Sprite layer</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
In this plane, tiles are treated as **sprites**, they are positioned in a 512x512 map and only a part of it (VDP's output resolution) is selected for display. This is convenient for hiding unwanted sprites or pre-rendering others that will be shown in the future. The VDP also provides **collision detection** between sprites by checking its *status* register.

Sprites are formed by combining up to 4x4 tiles (32x32 map) and selecting up to 16 colours (including *transparent*), if a bigger sprite is needed then multiple sprites can be combined into one.

There can only be a maximum of 20 sprites per scan-line and 80 per screen (overflowing this will corrupt the whole layer).

The region in VRAM where Sprites are defined is called **Sprite Attribute Table** and each entry contains: Tile index, layer coordinates (x and y), *Link* value (manages which sprites are drawn first), priority (the sprite with highest priority is the one to be displayed during overlaps), colour palette index and vertical and horizontal flip.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}


{{< float_block >}}
  {{< linked_img class="pixel" src="vdp_sonic/result.png" alt="Result" >}}
  <figcaption class="caption">Tada!</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
While the frame is being drawn, the system will sequentially call different interrupt routines depending where the CRT's beam is pointing to. As you probably seen before in previous consoles, this allows the CPU to work on the next frame (or alter the current one).

Conventionally, there are two types of interrupts called: **H-Blank** (every horizontal line) and **V-Blank** (every frame).

H-Blank is called numerous times but is limited to executing short routines, only CRAM and VSRAM are accessible, so games can only update their colour palettes or vertically scroll their planes.

V-Blank allows for longer routines with the drawback of being called only 50-60 times per second, it's also able to access all memory locations.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

#### Extra

So far we've discussed what the CPU can do to update frames, but what about the VDP? This chip actually features **Direct Memory Access** ('DMA' for short) that allows to move memory around at a faster rate without the intervention of the CPU.

The DMA can be activated during H-Blank, V-Blank or active state (outside any interrupt), each one will have a different bandwidth. Additionally, during any DMA transfer the CPU will be blocked, this means the timing is critical to achieve performance.

If used correctly, you'll gain high resolution graphics, fluid parallax scrolling and high frame-rates. Moreover, your game may also be featured on TV ads with lots of *Blast Processing!* signs all over it.

---

## Audio

The Mega Drive has 2 sound chips with *very* different capabilities:

{{< tabs >}}

{{< tab active="true" name="Yamaha YM2612" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="FM" active="true" >}}
      {{< video src="fm_single" >}}
    {{< /tab >}}
    {{< tab name="PCM Sample" >}}
      {{< video src="pcm_single" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Sonic The Hedgehog (1991)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
An **FM synthesiser** that runs at the 68000 speed and supports **six FM channels**, one can be used to play **PCM samples** (8-bit resolution and 32 KHz sampling rate).

Frequency modulation or 'FM' synthesis is one of many professional techniques used for synthesising sound, it significantly rose in popularity during the 80s and made way to completely new sounds (many of which you can found listening to the tunes from that era).

In a *strict* nutshell, the FM algorithm takes a single waveform (**carrier**) and alters its frequency using another waveform (**modulator**), the result is a new waveform with a different frequency (and sound). The carrier-modulator combination is called **operator**, multiple operators can be *chained* together to form the final waveform, different combinations achieve different results. This chip allows 4 operators per channel.

Compared to traditional PSG synthesisers, this was a drastic improvement: You were no longer stuck with *pre-defined* waveforms.
{{% /inner_markdown %}}

{{< /tab >}}
  
{{< tab name="Texas Instruments SN76489" >}}

{{< float_block >}}
  {{< video src="psg_single" >}}
  <figcaption class="caption">Sonic The Hedgehog (1991)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
A **PSG chip** that can produce three pulse waves and one noise.

This is actually the original Master System's [sound chip]({{< ref "master-system#audio" >}}) and it's embedded in the VDP, it runs at the speed of the Z80.

Notice the 'Pulse 3' channel remains unused, this is indeed reserved to play the effects during gameplay.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Mixed" >}}

{{< float_block >}}
  {{< video src="complete" >}}
  <figcaption class="caption">Sonic The Hedgehog (1991)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Both chips can output sound at the same time, the audio mixer will then receive both signals and merge them before sending it through the audio output.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### The conductor

The Z80 is the **only CPU** capable of sending commands to those two chips, which is a relief for the 68000 since the latter is already fed up with other tasks.

However, let's not forget that the Z80 is an independent processor by itself, so it needs its own program (stored in the 8 KB of RAM available) which will enable it to interpret the music data received from the 68000 and effectively manipulate the two sound chips accordingly, this program is called a **sequencer** or **driver**. 

Now, programmers also needed to plan a way to continuously sequence and stream their music using the rest of RAM available. The main constraint is that in order to fill that memory, the main bus has to be **blocked** first (so no commands or samples can be sent to the audio chips during that timeframe). If this issue wasn't tackled properly, different sound anomalies could appear (muting, frozen notes, low sample rates, etc).

#### Cracking sampling

Instead of just sticking with ordinary drum kits, some games found incredible ways to stream richer samples to that single PCM channel, check out these examples:

{{< side_by_side >}}
  <div class="toleft">
    {{< tabs nested="true" >}}
      {{< tab name="PCM Sample" active="true" >}}
        {{< video src="good_sampling/sonic_pcm" >}}
      {{< /tab >}}
      {{< tab name="Complete" >}}
        {{< video src="good_sampling/sonic_complete" >}}
      {{< /tab >}}
    {{< /tabs >}}
    <figcaption class="caption">Sonic The Hedgehog 3 (1994)
    <br>This is one of the tracks said to be co-authored by Michael Jackson. In any case, the overall soundtrack had a distinctive beat compared to its predecessors.</figcaption>
  </div>

  <div class="toright">
    {{< tabs nested="true" >}}
      {{< tab name="PCM Sample/Complete" active="true" >}}
        {{< video src="good_sampling/randy_pcm" >}}
      {{< /tab >}}
    {{< /tabs >}}
    <figcaption class="caption">Toy Story (1995)
    <br>This is sequenced in real time with the help of the 68000. A very intensive task, meaning it could only be played at very particular points of the game (i.e. the main menu)</figcaption>
  </div>
{{< /side_by_side >}}

I know, they are nowhere near CD quality, but bear in mind those sounds were once deemed impossible to reproduce in this console and I'm not even emphasising how much progress this represents compared to the previous generation, so they certainly deserve some merit at least!

#### Assisted FM Composition

If programming an FM synthesiser was already considered complicated using the controls of an electronic keyboard (the Yamaha DX7 is a good example of this), imagine how much it was using only pure assembly...

Luckily, Sega ended up producing a piece of software for PC called **GEMS** to facilitate the composition (and debugging) of music. It was a very complete tool, among lot of things it included lots of *patches* (already configured operators to choose from), which would also explain why some games have *very* similar sounds.

The audio subsystem enabled games to create more channels than allowed and assign each one a **priority value**, then when the console would play the music, it dynamically dispatched the music channels to the available slots based on priorities. Additionally, channels with a high priority but without music could be automatically skipped.

Channels also contained some **logic** by implementing conditionals inside their data, this allows music to 'evolve' depending of how the player moves in the game.

#### (Bonus) Mega CD Sound

Here's an interesting fact: The Mega CD add-on provided 2 extra channels for CD Audio (among other things). One of its most famous games, Sonic CD, had very impressive music quality but like all games it had to loop, the problem was that looping music on a 1x CD reader had noticeable gaps, so the game included loop fillers that were executed from another PCM chip while the CD header was returning to the start.

These fillers are only found on early betas of the game and they didn't make it for the release, the remake finally included them. This is one of the levels of the game:

{{< side_by_side >}}
    <div class="toleft" style="text-align: end;">
    {{< audio src="sonic_cd_original" >}}
    <figcaption class="caption">MegaCD version (1993)</figcaption>
    </div>
    <div class="toright">
    {{< audio src="sonic_cd_remaster" >}}
    <figcaption class="caption">Remastered version (2011)</figcaption>
    </div>
{{< /side_by_side >}}

Have you noticed the gap on the Mega CD's version?

---

## Games

They are mainly written in 68k assembly (while the sound driver is in Z80 assembly) and reside in the cartridge ROM. They can size up to 4 MB without the need of a mapper.

In terms of expandability, this design wasn't as modular as the NES or the SNES, some add-ons like the 32x had to bypass the VDP (hence the need for the 'Connector Cable'). The same happened with the Mega CD, where in order to use the new stereo functionality on the TV, more cables had to be interconnected between modules.

Only one custom chip was produced for cartridges, the Sega Virtua Processor, among other things it helped to produce polygons, however only one game included it as it resulted very expensive to produce.

---

## Anti-piracy / Region Lock

To block imported games, Sega changed the shape of the cartridge slot between regions (it kept the same pinouts, though). Games could also block their execution by checking the value of the 'Version Register' which outputs the region value.
An easy way to bypass this was to either buy one of those shady cartridge converters or do some soldering to bridge some pins on the motherboard that alters the Version Register.

When it comes to Anti-Piracy measures, the easiest check was on the SRAM size: Bootleg cartridges had more space than needed to fit any game, so games checked for the expected size on the startup.
Programmers could also implement extra checksum checks at random points of the game in case hackers were to remove the SRAM checks. The only way to defeat this was to actually find the checks and remove them one by one... Although finding them was the trickiest part!

---

## Sources / Keep Reading

#### General

- [**Technical specifications in detail (Archived)**](https://web.archive.org/web/20190827225347/https://segaretro.org/Sega_Mega_Drive/Technical_specifications)
- [**Schematics**](https://gamesx.com/wiki/doku.php?id=schematics:console_related_schematics)
- [**Interesting architectural comparison with the SNES**](https://hackaday.com/2015/11/06/winning-the-console-wars-an-in-depth-architectural-study/)

#### Graphics

- [**VDP in detail**](https://megacatstudios.com/blogs/press/sega-genesis-mega-drive-vdp-graphics-guide-v1-2a-03-14-17)
- [**More about CRAM (Archived)**](https://web.archive.org/web/20190815115213/https://segaretro.org/Sega_Mega_Drive/Palettes_and_CRAM)
- [**VDP Ports**](https://wiki.megadrive.org/index.php?title=VDP_Ports)
- [**Long docs**](http://jiggawatt.org/genvdp.txt)
- [**DMA transfer rates (Archived)**](https://web.archive.org/web/20190815121028/https://segaretro.org/Sega_Mega_Drive/DMA)
- [**Bad usage of H-Blank resulting in 'CRAM Dots' (Archived)**](https://web.archive.org/web/20190815115213/https://segaretro.org/Sega_Mega_Drive/Palettes_and_CRAM)

#### Audio

- [**Basic FM in action**](https://www.youtube.com/watch?time_continue=16&v=Zk82DPrAugs)
- [**More about GEMS**](https://www.youtube.com/watch?v=WEvnZRCW_qc)
- [**More about the Yamaha chip**](https://en.wikipedia.org/wiki/Yamaha_YM2612)
- [**SegaRetro's article of GEMS (Archived)**](https://web.archive.org/web/20190830034717/https://segaretro.org/GEMS)
- [**Developer of ToyStory explains in-depth how their sounds were achieved**](https://www.youtube.com/watch?v=x3m3JrVImmU)

#### Photography

- Console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Motherboard: [**Okqubit**](https://www.okqubit.net/machines/gamecons.html)
- Diagrams and game screenshots: **Me**
