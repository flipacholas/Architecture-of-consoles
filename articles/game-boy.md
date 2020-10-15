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
    caption: "The original Game Boy. Released on 21/04/1999 in Japan, 31/07/1989 in America and 28/09/1990 in Europe"
    img_class: reduced-width
  Motherboard:
    caption: "Showing revision '04'. Note that 'DMG' is the identifier of the original Game Boy model."
  Diagram: {}

# SEO
title: Game boy Architecture
---

## A quick introduction

The Game Boy can be imagined as a portable version of the NES with limited power, but you'll see that it included very interesting new functionality.

---

## CPU

The main processor is a **Sharp LR35902**, a mix between the Z80 and the Intel 8080 that runs at ~4.19 MHz.

The Z80 is itself a superset of the 8080, so what does the Sharp actually has and has not from those two?
- Neither Z80's *IX* and *IY* registers nor 8080's *IN* or *OUT* instructions are included: [I/O]({{< ref "master-system" >}}#accessing-the-rest-of-the-components) is now completely **memory mapped** so these are not needed.
- Only 8080's registers are implemented.
- Includes Z80's extended instruction set, however only bit manipulation instructions are found.
- Keeps a 8-bit data bus and a 16-bit address bus: Up to 64 KB of memory can be addressed, the memory map is composed of:
  - Cartridge ROM.
  - WRAM and Display RAM.
  - I/O and interrupt enabler.

---

## Graphics

All graphics calculations are done by the CPU, then the **Picture Processing Unit** renders them.
The picture is displayed in an integrated LCD screen, it has a resolution of 160×144 pixels and supports 4 shades of grey (white, light grey, dark grey and black). In practice, however, since the original gameboy has a green LCD, graphics will look *greenish*.

In order to build the picture, the PPU first looks for tile references from a different set of tables stored in **Display RAM** which were previously populated by the game, each table is used to build a layer of the frame.

Each layer also allows to store a different colour palette: Even that the GameBoy only has 4 colours, palettes can create a different combination of those, allowing to alternate colours without having to store extra graphics.

For demonstration purposes, *Super Mario Land 2* will be used as example to show the process of rendering the frame:

{{< tabs >}}

{{< tab name="Tiles" active="true" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel desktop-margined" >}}
    {{< tab name="All" active="true">}}
      {{< linked_img src="ppu_mario/tiles.png" >}}
    {{< /tab >}}
    {{< tab name="Single">}}
      {{< linked_img src="ppu_mario/tiles_single.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Tiles Found in the Pattern Table
  <br>(For demonstration purposes a default palette is being used)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The PPU uses **tiles** as a basic ingredient for producing sprites and backgrounds.

The Game Boy defines tiles as basic 8x8 maps, these are stored in the **Program ROM** (cartridge) but, in order to access them, they have to be copied to **Display RAM** during specific events (these are discussed later).

In Display RAM tiles are organised in **Pattern Tables** and there's enough space for two of them. Each one will be accessed by different layers.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Full" active="true">}}
      {{< linked_img src="ppu_mario/background_grid.png" >}}
    {{< /tab >}}
    {{< tab name="Selected">}}
      {{< linked_img src="ppu_mario/background_selected.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Rendered Background map
  <br>Notice the selected part includes one portion of the top, this will be overlapped by the <i>Window layer</i>.</figcaption>
{{< /float_block >}}
{{% inner_markdown %}}

The Background layer is a 256x256 map containing **static tiles**, however only 160x144 is viewable on the screen, so the game decides which part is selected for display. Games can also move the viewable area during gameplay, that's how the **Scrolling Effect** is accomplished.

The **Background Tile Table** is used to store references of tiles to be displayed as background.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Window" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="ppu_mario/window_grid.png" alt="Window" >}}
  <figcaption class="caption">Rendered Window map showing remaining lives and scores</figcaption>    
{{< /float_block >}}

{{% inner_markdown %}}

The Window is a 160x144 layer containing tiles that can be displayed on top of the background and sprites, it doesn't scroll. Games normally use it to display player stats, score and other *always-on* information.

The **Window Tile Table** selects the tiles to be used in this layer, it has the same structure as the Background Table.

The Background and Window can be used concurrently with each one using different parts of the screen. This is accomplished by changing the **LCDCONT** register during specific scan-lines.

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="ppu_mario/sprite.png" alt="Sprites" >}}
  <figcaption class="caption">Rendered Sprite layer</figcaption>    
{{< /float_block >}}

{{% inner_markdown %}}

Sprites are tiles that can move around the screen. They can also overlap each other and appear behind the background, the viewable graphic will be decided based on its priority value.

Sprites have an extra colour available: **Transparent**. This also means that they only can display 3 different greys instead of 4. Luckily, this layer allows to store two palettes in order to use every colour.

The **Object Attribute Memory** (OAM) table specifies which tiles will be used as sprites. In addition to the tile index, each entry contains an (x,y) position and multiple attributes: Colour palette, a priority and flip flags (allows to rotate the tile).

The PPU is limited to ten sprites per scan-line and up to 40 per frame, overflowing this will result in sprites not being drawn.

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="ppu_mario/result.png" alt="Result" >}}
  <figcaption class="caption">Tada!</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}

Once the frame is finished, it's time to move on to the next one! However, the CPU can't modify the tables while the PPU is using them, so the system provides a set of interrupts triggered when the PPU is idle. This design is similar to the NES' PPU, which was built for CRT displays.

When a single scan-line is complete, the **Horizontal Blank** interrupt is called, this allows to fiddle with the part of frame that has not yet drawn.

When all scan-lines are complete the **Vertical Blank** interrupt is called. The game can now update the graphics for the next frame.

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

This achieved a *Wobbling effect*.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Audio

The audio system is carried out by the **Audio Processing Unit** (APU), a PSG chip with four channels, each one reserved for a type of wave-form:

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

The APU reserves two channels for one pulse-wave each. These channels use one of four different tones constructed by varying its pulse width. The first channel has an exclusive *Sweep* control available.

Due to the limited number of channels, melody will often be interrupted when effects have to be played as part of the gameplay. This is very noticeable in games like Pokemon Red/Blue when, during a battle, the Pokemon's *cry* will overlap all the channels used for music.

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

The APU allows to store **32 custom wave-forms** to be played in its forth channel. Each wave is composed of a 4-bit sample that will be played repeatedly.

This channel also allows to control its frequency (enabling to produce different musical notes from the same sample) and volume.

{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

#### Secrets and Limitations

The mixer outputs stereo sound, so the channels can be programmed on the left side or on the right one, this is only possible to hear from the headphones though! The speaker is mono.

The mixer chip is also connected to a specific pin of the cartridge, allowing to include an extra channel with the condition that the cartridge has to actually output the sound (only possible using extra hardware). No game in the market ended up using this feature.

---

## Games

Games have a maximum size of 32 KB, this is due to the limited address space available, but if the cartridge uses a Memory Bank Controller it can reach bigger sizes, the biggest cartridge found was 1 MB.
They can include a real time clock and an external battery along with SRAM to hold saves.

For the first time games can also communicate with other consoles thanks to the **Game Boy Link** cable which provides multiplayer functionality.

---

## Anti-piracy

This console contains a **256 Byte ROM** stacked in the CPU that is used to bootstrap the cartridge's ROM.
It doesn't run the game right away however, instead it executes a series of checks that **prevented unauthorised cartridges from working** and also made sure the cartridge was **correctly inserted**.

To be able to pass these checks, games had to include a copy of Nintendo's logo (in the form of tiles) in its ROM header, this way Nintendo could make use of **Copyright and Trademark** laws to control the distribution, *Clever huh?*. The bootstrap ROM included a copy of this logo as well.

That being said, the boot process is as follows:

1. After the console is switched on, the CPU starts reading at address **0x00** (bootstrap ROM location).
1. RAM and Sound are initialised.
2. *Nintendo* logo is copied from the cartridge ROM to Display RAM, then it's drawn at the top edge of the screen. If there is no cartridge inserted, the logo won't be drawn. If it's badly inserted, the logo may be shown corrupted.
2. Logo is scrolled down and the famous *po-ling* sound is played.
3. The game's *Nintendo* logo is **matched against** the one stored in the bootstrap ROM, if the check fails the console freezes.
4. A quick **checksum** is done on the cartridge's ROM header to make sure the cartridge is correctly inserted, if check fails the console freezes.
5. Bootstrap ROM is **unloaded** from memory map.
6. Starts execution of instructions found in the cartridge. Interestingly enough, the *Nintendo* logo displayed on the screen is not cleared from Display RAM, so games can apply some animation and effects to introduce their own logo.

{{< centered_container >}}
  {{< video src="20y" >}}
  <figcaption class="caption">20y, a demo that fiddles with the logo</figcaption>
{{< /centered_container >}}

More anti-piracy measures can be implemented inside the games, like checking the SRAM size (is normally bigger in Bootlegs) and checksumming the ROM at random points of the game.

---

## Sources / Keep Reading

#### General

- [**Interesting series of articles for constructing an emulator**](https://realboyemulator.wordpress.com/2013/01/03/a-look-at-the-game-boy-bootstrap-let-the-fun-begin/)
- [**Official Game Boy Programming Manual**](https://archive.org/details/GameBoyProgManVer1.1)
- [**Interesting Game Boy talk**](https://www.youtube.com/watch?v=HyzD8pNlpwI)
- [**Different models of the Game Boy**](https://gbhwdb.gekkio.fi/consoles/dmg/)

#### CPU

- [**Memory Map in detail**](http://gameboy.mongenel.com/dmg/asmmemmap.html)
- [**Programming manuals**](https://fms.komkon.org/GameBoy/Tech/Software.html)

#### Graphics

- [**More details about GameBoy's graphics (Archived)**](https://web.archive.org/web/20181011213834/http://www.codeslinger.co.uk/pages/projects/gameboy/graphics.html)

#### Games

- [**Glitching Mario until it becomes an interactive memory editor**](https://www.youtube.com/watch?v=FPzuYWbnln4)
- [**Game Boy Development Wiki**](http://gbdev.gg8.se/wiki/articles/Main_Page)
- [**Fiddling with the boot process**](https://dhole.github.io/post/gameboy_custom_logo/)

#### Photography

- Motherboard and console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams and game screenshots: **Me**
