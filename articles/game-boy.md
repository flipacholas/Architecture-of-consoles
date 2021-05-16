---
name: Game Boy
subtitle: Simple and portable
date: 2019-02-21
releaseDate: 1989-04-21
generation: 4
javascript: ['plyr']
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "The original Game Boy
    <br>Released on 21/04/1989 in Japan, 31/07/1989 in America and 28/09/1990 in Europe"
    img_class: reduced-width
  Motherboard:
    caption: "Showing revision '04'. Note that 'DMG' is the identifier of the original Game Boy model."
  Diagram: {}

# SEO
title: Game boy Architecture

# Historical
aliases: [/projects/consoles/game-boy/]
---

## A quick introduction

The Game Boy can be imagined as a portable version of the NES with limited power, but you'll see that it included very interesting new functionality.

---

## CPU

Instead of placing many off-the-shelf chips on the motherboard, Nintendo opted for a single chip to house (and hide) most of the components, including the CPU. This type of chip is called 'System On Chip' (SoC) and the one found on the GameBoy is referred to as **DMG-CPU** or **Sharp LR35902**.

Having said that, the main processor is a **Sharp SM83** and it's a mix between the Z80 and the Intel 8080. It runs at **~4.19 MHz**.

The Z80 is itself a superset of the 8080, so what does the SM83 actually has and has not from those two?
- Neither Z80's `IX` and `IY` registers nor 8080's `IN` or `OUT` instructions are included: This means that [I/O ports]({{< ref "master-system#accessing-the-rest-of-the-components" >}}) are not available. I'm not certain if that's just a measure to reduce costs, but one thing for sure is that components will have to be **completely memory-mapped**.
- Only 8080's set of registers are implemented.
- Includes Z80's extended instruction set. Although, only bit manipulation instructions are found.

Finally, they also added a **few new instructions** that are not present in either Z80 or 8080. I think explaining them one by one goes beyond the scope of this article, but the main idea is that they optimise certain operations conditioned by the way Nintendo/Sharp arranged the hardware.

#### Memory available

Nintendo fitted **8 KB of RAM** for general purpose use (which they call **Work RAM** or 'WRAM'). Notice that this is four times larger than what the [NES]({{< ref "nes" >}}) included.

#### Hardware access

The SM83 keeps an 8-bit data bus and a 16-bit address bus, so up to 64 KB of memory can be addressed. The memory map is composed of:
  - Cartridge space.
  - WRAM and Display RAM.
  - I/O (joypad, audio, graphics and LCD)
  - Interrupt controls.

---

## Graphics

All graphics calculations are done by the CPU, then the **Picture Processing Unit** or 'PPU' renders them. This is another component found inside DMG-CPU and it's actually based on the [predecessor's PPU]({{< ref "nes#graphics" >}}).

The picture is displayed in an integrated LCD screen, it has a resolution of **160×144 pixels** and shows **4 shades of grey** (white, light grey, dark grey and black). But since the original Gameboy has a green LCD, graphics will look *greenish*.

If you've read the NES article before, you may remember that the PPU was designed to follow the CRT beam. However (and for obvious reasons), we got an LCD screen in the Gameboy. Well, the new PPU doesn't alter that part, since LCDs require to be refreshed too. In fact, some special effects achieved thanks to this behaviour will also be supported on the Gameboy.

#### Organising the content

{{< centered_container >}}
  {{< linked_img src="ppu.png" alt="PPU Memory Diagram" >}}
  <figcaption class="caption">Memory architecture of the PPU</figcaption>
{{< /centered_container >}}

The PPU has **8 KB of VRAM** or 'Display RAM', which both PPU and CPU can access directly but not at the same time. Those 8 KB will contain most of the data the PPU will need to render graphics. The remaining bits will be stored inside the PPU instead, as they will require a faster access rate.

The game is in charge of populating the different areas with the correct type of data. Moreover, the PPU exposes registers so the game can instruct the PPU how that data is organised (there are many rules, though).

#### Constructing the frame

Let's see now how the PPU manages to draw stuff on the screen. For demonstration purposes, *Super Mario Land 2* will be used as an example:

{{< tabs >}}

{{< tab name="Tiles" active="true" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel desktop-margined" >}}
    {{< tab name="All" active="true">}}
      {{< linked_img src="ppu_mario/tiles.png" >}}
    {{< /tab >}}
    {{< tab name="Grid">}}
      {{< linked_img src="ppu_mario/tiles_grid.png" >}}
    {{< /tab >}}
    {{< tab name="Single">}}
      {{< linked_img src="ppu_mario/tiles_single.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Tiles Found in the Pattern Table</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The PPU uses **tiles** as a basic ingredient for rendering graphics, specifically, **sprites and backgrounds**.

Tiles are just **8x8 bitmaps** stored in VRAM in a region called **Tile set** or 'Tile pattern table', each pixel corresponds to one of the four shades of grey available. Finally, tiles are grouped into two pattern tables.

In order to build the picture, tiles are referenced in another type of table called **Tile map**. This information will tell the PPU where to render the tiles. Two maps are stored to construct different layers of the frame.

The next sections explain how tile maps are used to construct the layers.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true">}}
      {{< linked_img src="ppu_mario/background.png" >}}
      <figcaption class="caption">Background map</figcaption>
    {{< /tab >}}
    {{< tab name="Selected">}}
      {{< linked_img src="ppu_mario/background_selected.png" >}}
      <figcaption class="caption">Selected Background map
      <br>Notice the selected part includes one portion of the top, this will be overlapped by the <i>Window layer</i></figcaption>
    {{< /tab >}}
    {{< tab name="Rendered">}}
      {{< linked_img src="ppu_mario/background_rendered.png" >}}
      <figcaption class="caption">Rendered Background map</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}
{{% inner_markdown %}}
The Background layer is a **256x256 pixel** (32x32 tiles) map containing **static tiles**. However, remember that only 160x144 is viewable on the screen, so the game decides which part is selected for display. Games can also move the viewable area during gameplay, that's how the **Scrolling Effect** is accomplished.

One of the two tile maps can be used to build the background layer.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Window" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true">}}
      {{< linked_img class="pixel" src="ppu_mario/window.png" alt="Window" >}}
      <figcaption class="caption">Window map fully displayed</figcaption>
    {{< /tab >}}
    {{< tab name="Rendered">}}
      {{< linked_img src="ppu_mario/window_rendered.png" alt="Rendered Window" >}}
      <figcaption class="caption">The game will activate it during the last scan-lines, hence only the first rows are rendered at the bottom of the screen</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
The Window is a **160x144 pixel** layer containing tiles displayed on top of the background and sprites. It doesn't scroll.

The remaining tile map can be assigned to the window layer. 

At first, this may sound like a silly feature. After all, the window layer overlaps everything else so what's useful for? Well, both Background and Window can be used concurrently at different parts of the screen. This is accomplished by changing the `LCDCONT` register during specific scan-lines.

Thus, games normally use it to display player stats, score and other 'always-on' information.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="ppu_mario/sprite.png" alt="Sprites" >}}
  <figcaption class="caption">Rendered Sprite layer</figcaption>    
{{< /float_block >}}

{{% inner_markdown %}}
Sprites are tiles that can move independently around the screen. They can also overlap each other and appear behind the background, the viewable graphic will be decided based on a priority attribute.

They also have an extra colour available: **Transparent**. So, they can only display three different greys instead of four. Luckily, this layer allows defining two colour palettes make use of every colour.

The **Object Attribute Memory** or 'OAM' is a map stored inside the PPU which specifies the tiles that will be used as sprites. Games fill this region by calling the **DMA unit** found inside the chip, the DMA fetches data from main RAM or game ROM to OAM.

Apart from the tile index, each entry contains the following attributes: X-Y position, colour palette, priority and flip flags (allowing to rotate the tile vertically and horizontally).

The PPU is limited to rendering up to **ten sprites per scan-line** and **up to 40 per frame**, overflowing this will result in sprites not being drawn.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="ppu_mario/result.png" alt="Result" >}}
  <figcaption class="caption">Tada!</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Once the frame is finished, it's time to move on to the next one! However, the CPU can't modify the tables while the PPU is reading from VRAM, so the system provides a set of interrupts triggered when the PPU is idle. You can recall this behaviour from the times of the NES.

When a single scan-line is complete, the **Horizontal Blank** interrupt is called. This allows to fiddle with the part of the frame that has not yet been drawn.

When all scan-lines are complete, the **Vertical Blank** interrupt is called. The game can now update the graphics for the next frame.

There's an extra state called **OAM search** that is triggered at the start of the scan-line, at this point the PPU is processing which sprites will be displayed in that scan-line, so the game can update any region except OAM.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Secrets and Limitations

The inclusion of the Window layer and extra interrupts allowed for new types of content and effects.

{{< tabs >}}
{{< tab name="Wobble effect" active="true" >}}

{{< float_block >}}
{{< video src="zelda" >}}
<figcaption class="caption">The Legend of Zelda: Link's Awakening (1993) <i>Spoilers!</i></figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Horizontal interrupts allowed to alter the frame before being finished. This means that a different scrolling value could be applied at each line, resulting in each row of the frame being displaced differently.

This achieved a *Wobbling effect* (I'm not sure that's the official name of it). 
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Audio

The audio system is carried out by the **Audio Processing Unit** (APU), a [PSG chip]({{< ref "master-system#audio" >}}) with four channels, each one reserved for a type of wave-form:

{{< tabs >}}

{{< tab name="Pulse" active="true" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Pulse 1" active="true">}}
      {{< video src="pulse_single_1" >}}
    {{< /tab >}}
    {{< tab name="Pulse 2">}}
      {{< video src="pulse_single_2" >}}
    {{< /tab >}}
    {{< tab name="Complete">}}
      {{< video src="pulse_full" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Pokemon Red/Blue (1996)</figcaption>
{{< /float_block >}}
{{% inner_markdown %}}

Pulse waves have a very distinct *beep* sound that is mainly used for **melody or sound effects**.

The APU reserves two channels for one pulse-wave each. These use one of four different tones constructed by varying its pulse width. The first channel has an exclusive **Sweep control** available.

Due to the limited number of channels, the melody will often be interrupted when effects have to be played as part of the gameplay. This is very noticeable in games like Pokemon Red/Blue when, during a battle, the Pokemon's cry will overlap all the channels used for music.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Noise" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Noise" active="true">}}
      {{< video src="noise_single" >}}
    {{< /tab >}}
    {{< tab name="Complete">}}
      {{< video src="noise_full" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Pokemon Red/Blue (1996)</figcaption>
{{< /float_block >}}
{{% inner_markdown %}}

Noise is basically a set of random wave-forms that sound like white static. One channel is allocated for it.

Games use it for percussion or *ambient* effects.

This channel has only 2 tones available to use, one produces *clean static* and the other produces *robotic static*. Its frequency can also be controlled.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Wave" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Wave" active="true">}}
      {{< video src="wave_single" >}}
    {{< /tab >}}
    {{< tab name="Complete">}}
      {{< video src="wave_full" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Pokemon Red/Blue (1996)</figcaption>
{{< /float_block >}}
{{% inner_markdown %}}

The APU allows to define a **custom wave-form** to be heard from its fourth channel. The wave is composed of 32 4-bit samples which are stored in a wavetable.

This channel also allows to control its frequency (enabling to produce different musical notes from the same sample) and volume.

{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

#### Secrets and Limitations

The mixer outputs stereo sound, so the channels can be assigned to the left side or on the right one, this is only possible to hear from the headphones though! The speaker is mono.

Furthermore, the mixer chip is also connected to a dedicated pin on the cartridge, allowing to stream an **extra channel** with the condition that the cartridge has to actually output the analogue sound (only possible with extra hardware). No game in the market ended up using this feature.

---

## Games

Games are written in assembly and they have a maximum size of **32 KB**, this is due to the limited address space available. However, with the use of a **Memory Bank Controller** (mapper), games can reach bigger sizes. The biggest cartridge found in the market has a 1 MB ROM.

Cartridges can include a real-time clock and an external battery along with SRAM to hold saves.

#### External communications

For the first time, games can communicate with other consoles with the use of a **Game Boy Link** cable, which provides multiplayer functionality. The interface uses a very primitive type of **serial connection**.

---

## Anti-piracy

This console contains a **256 Byte ROM** stacked in the CPU that is used to bootstrap the cartridge's ROM.
It doesn't run the game right away however, it first executes a series of checks that **prevent the execution of unauthorised cartridges** and also makes sure the cartridge is **correctly inserted**.

To be able to pass these checks, games had to include a copy of Nintendo's logo (in the form of tiles) in its ROM header, this way Nintendo could make use of **Copyright and Trademark** laws to control the distribution, *Clever huh?*. The Gameboy ROM also embeds a copy of the logo to be able to compare it.

That being said, the boot process is as follows:

1. After the console is switched on, the CPU starts reading at address **0x00** (Gameboy's ROM location).
1. RAM and Sound are initialised.
2. Nintendo logo is copied from the cartridge ROM to Display RAM, then it's drawn at the top edge of the screen. If there is no cartridge inserted, the logo will contain garbage tiles. The same may happen if it's badly inserted.
2. The logo is scrolled down and the famous *po-ling* sound is played.
3. The game's Nintendo logo is matched against the one stored in the console's ROM, if the check fails the console freezes.
4. A quick **checksum** is done on the cartridge's ROM header to make sure the cartridge is correctly inserted, if check fails the console freezes.
5. The console's ROM is removed from the memory map.
6. CPU starts executing the game.

Interestingly enough, the *Nintendo* logo displayed on the screen is not cleared from VRAM, so games can apply some animation and effects to introduce their own logo.

{{< centered_container >}}
  {{< video src="20y" >}}
  <figcaption class="caption">20y, a homebrew demo that fiddles with the logo</figcaption>
{{< /centered_container >}}

More anti-piracy measures can be implemented inside games, like checking the SRAM size (it's normally bigger in Bootlegs) and checksumming the ROM at random points of the game.

---

## Sources / Keep Reading

#### General

- Nintendo, [**Gameboy Programming Manual**](https://archive.org/details/GameBoyProgManVer1.1), Version 1.1
- Joonas Javanainen (gekkio), [**Game Boy: Complete Technical Reference**](https://gekkio.fi/files/gb-docs/gbctr.pdf)
- RealBoy, [**Emulating The Core** (Series)](https://realboyemulator.wordpress.com/posts/)
- Michael Steil, [**The Ultimate Game Boy Talk (33c3)**](https://www.youtube.com/watch?v=HyzD8pNlpwI)
- Gekkio and contributors, [**Game Boy hardware database**](https://gbhwdb.gekkio.fi/consoles/dmg/)

#### CPU

- Randy Mongenel, [**GameBoy Memory Map**](http://gameboy.mongenel.com/dmg/asmmemmap.html)
- Marat Fayzullin, [**Programming Info**](https://fms.komkon.org/GameBoy/Tech/Software.html)

#### Graphics

- codeslinger.co.uk, [**Graphics Emulation** (Archived)](https://web.archive.org/web/20181011213834/http://www.codeslinger.co.uk/pages/projects/gameboy/graphics.html)

#### Audio

- Gameboy Development Wiki, [**Gameboy sound hardware**](https://gbdev.gg8.se/wiki/articles/Gameboy_sound_hardware)

#### Games

- Retro Game Mechanics Explained, [**GB Super Mario Land 2 - Memory Exploration** (Youtube)](https://www.youtube.com/watch?v=FPzuYWbnln4)
- Dhole, [**Booting the GameBoy with a custom logo**](https://dhole.github.io/post/gameboy_custom_logo/)

#### AntiPiracy

- RealBoy, [**A Look At The Game Boy Bootstrap: Let The Fun Begin!**](https://realboyemulator.wordpress.com/2013/01/03/a-look-at-the-game-boy-bootstrap-let-the-fun-begin/)

#### Photography

- Motherboard and console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams and game screenshots: [**Rodrigo Copetti (Me)**](https://www.copetti.org/)
