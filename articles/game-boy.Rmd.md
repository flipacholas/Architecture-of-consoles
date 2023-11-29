---
short_title: Game Boy Architecture
long_title: Architecture of the Game Boy
name: Game Boy
subtitle: Simple and portable
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "The original Game Boy.<br>Released on 21/04/1989 in Japan, 31/07/1989 in America and 28/09/1990 in Europe"
    img_class: reduced-width
  Motherboard:
    caption: "Showing revision '04'. Note that 'DMG' is the identifier of the original Game Boy model."
  Diagram: {}

# Historical
aliases: [/projects/consoles/game-boy/]
---

## A quick introduction

The Game Boy can be imagined as a portable version of the NES with limited power, but you'll see that it included very interesting new functionality.

## {.supporting-imagery}

## CPU

Instead of placing many off-the-shelf chips on the motherboard, Nintendo opted for a single chip to house (and hide) most of the components, including the CPU. This type of chip is called 'System On Chip' (SoC) and the one found on the Game Boy is referred to as **DMG-CPU** or **Sharp LR35902** [@cpu-realboy].

Having said that, the main processor is a **Sharp SM83** [@cpu-gekkio] and it's a mix between the Z80 and the Intel 8080. It runs at **~4.19 MHz**.

The Z80 is itself a superset of the 8080, so what does the SM83 actually has and has not from those two? [@cpu-steil]

- Neither of the Z80's `IX` or `IY` registers nor 8080's `IN` or `OUT` instructions are included: This means that [I/O ports](master-system#accessing-the-rest-of-the-components) are not available. I'm not certain if that's just a measure to reduce costs, but one thing for sure is that components will have to be **completely memory-mapped** [@cpu-fayzullin].
- Only 8080's set of registers are implemented.
- Includes Z80's extended instruction set. Although, only bit manipulation instructions are found.

Finally, they also added a **few new instructions** that are not present in either Z80 or 8080. I think explaining them one by one goes beyond the scope of this article, but the main idea is that they optimise certain operations conditioned by the way Nintendo/Sharp arranged the hardware.

### Memory available

Nintendo fitted **8 KB of RAM** for general purpose use (which they call **Work RAM** or 'WRAM') [@cpu-nintendo]. Notice that this is four times larger than what the [NES](nes) included.

### Hardware access

The SM83 keeps an 8-bit data bus and a 16-bit address bus, so up to 64 KB of memory can be addressed. The memory map is composed of [@cpu-mongenel]:

- Cartridge space.
- WRAM and Display RAM.
- I/O (joypad, audio, graphics and LCD)
- Interrupt controls.

## Graphics

All graphics calculations are done by the CPU, and then the **Picture Processing Unit** or 'PPU' renders them. This is another component found inside DMG-CPU and it's actually based on the [predecessor's PPU](nes#graphics).

The picture is displayed on an integrated LCD screen, it has a resolution of **160×144 pixels** and shows **4 shades of grey** (white, light grey, dark grey and black). But since the original Game Boy has a green LCD, graphics will look *greenish*.

If you've read the NES article before, you may remember that the PPU was designed to follow the CRT beam. However (and for obvious reasons), we got an LCD screen in the Game Boy. Well, the new PPU doesn't alter that part, since LCDs require to be refreshed too. In fact, some special effects achieved thanks to this behaviour will also be supported on the Game Boy.

### Organising the content

![Memory architecture of the PPU.](ppu.png)

The PPU has **8 KB of VRAM** or 'Display RAM', which both PPU and CPU can access directly but not at the same time. Those 8 KB will contain most of the data the PPU will need to render graphics. The remaining bits will be stored inside the PPU instead, as they will require a faster access rate.

The game is in charge of populating the different areas with the correct type of data. Moreover, the PPU exposes registers so the game can instruct the PPU on how that data is organised (there are many rules, though).

### Constructing the frame

Let's see now how the PPU manages to draw stuff on the screen. For demonstration purposes, *Super Mario Land 2* will be used as an example:

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Multiple tiles.](ppu_mario/tiles.png){.active title="All"}

![Multiple tiles separated with a grid.](ppu_mario/tiles_grid.png){title="Grid"}

![A single tile.](ppu_mario/tiles_single.png){title="Single"}

Tiles found in the Pattern Table.

:::

The PPU uses **tiles** as a basic ingredient for rendering graphics, specifically, **sprites and backgrounds** [@graphics-emu].

Tiles are just **8x8 bitmaps** stored in VRAM in a region called **Tile set** or 'Tile pattern table', each pixel corresponds to one of the four shades of grey available. Finally, tiles are grouped into two pattern tables.

In order to build the picture, tiles are referenced in another type of table called a **Tile map**. This information will tell the PPU where to render the tiles. Two maps are stored to construct different layers of the frame.

The next sections explain how tile maps are used to construct the layers.

#### Background Layer {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Allocated Background map in VRAM.](ppu_mario/background.png){.active title="Full"}

![Selected area of the Background map. Notice the selected part includes one portion of the top, this will be overlapped by the _Window layer_.](ppu_mario/background_selected.png){title="Selected"}

![Displayed Background map.](ppu_mario/background_rendered.png){title="Rendered"}

Background map rendering process.

:::

The Background layer is a **256x256 pixel** (32x32 tiles) map containing **static tiles**. However, remember that only 160x144 is viewable on the screen, so the game decides which part is selected for display. Games can also move the viewable area during gameplay, that's how the **Scrolling Effect** is accomplished.

One of the two tile maps can be used to build the background layer.

#### Window {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Allocated Window map.](ppu_mario/window.png){.active title="Full"}

![Displayed Window map. The game activates it during the last scan-lines. Hence, only the first rows are rendered at the bottom of the screen.](ppu_mario/window_rendered.png){title="Selected"}

Window map rendering process.

:::

The Window is a **160x144 pixel** layer containing tiles displayed on top of the background and sprites. It doesn't scroll.

The remaining tile map can be assigned to the window layer. 

At first, this may sound like a silly feature. After all, the window layer overlaps everything else so what's it useful for? Well, both Background and Window can be used concurrently at different parts of the screen. This is accomplished by changing the `LCDCONT` register during specific scan lines.

Thus, games normally use it to display player stats, scores and other 'always-on' information.

#### Sprites {.tab}

![Rendered Sprite layer.](ppu_mario/sprite.png){.tab-float .pixel}

Sprites are tiles that can move independently around the screen. They can also overlap each other and appear behind the background, the viewable graphic will be decided based on a priority attribute.

They also have an extra colour available: **Transparent**. So, they can only display three different greys instead of four. Luckily, this layer allows defining two colour palettes to make use of every colour.

The **Object Attribute Memory** or 'OAM' is a map stored inside the PPU which specifies the tiles that will be used as sprites. Games fill this region by calling the **DMA unit** found inside the chip, the DMA fetches data from main RAM or game ROM to OAM.

Apart from the tile index, each entry contains the following attributes: X-Y position, colour palette, priority and flip flags (allowing to rotate the tile vertically and horizontally).

The PPU is limited to rendering up to **ten sprites per scan-line** and **up to 40 per frame**, overflowing this will result in sprites not being drawn.

#### Result {.tab}

![Final result. _Tada!_](ppu_mario/result.png){.tab-float .pixel}

Once the frame is finished, it's time to move on to the next one! However, the CPU can't modify the tables while the PPU is reading from VRAM, so the system provides a set of interrupts triggered when the PPU is idle. You can recall this behaviour from the times of the NES.

When a single scan-line is complete, the **Horizontal Blank** interrupt is called. This allows to fiddle with the part of the frame that has not yet been drawn.

When all scan-lines are complete, the **Vertical Blank** interrupt is called. The game can now update the graphics for the next frame.

There's an extra state called **OAM search** that is triggered at the start of the scan-line, at this point the PPU is processing which sprites will be displayed in that scan-line, so the game can update any region except OAM.

### Secrets and Limitations {.tabs-close}

The inclusion of the Window layer and extra interrupts allowed for new types of content and effects.

#### Wobble effect {.tabs .active}

![The Legend of Zelda: Link's Awakening (1993). _Spoilers!_](zelda){.tab-float video="true"}

Horizontal interrupts allowed to alter the frame before being finished. This means that a different scrolling value could be applied at each line, resulting in each row of the frame being shifted at different paces.

This achieved a *Wobbling effect* (I'm not sure that's the official name of it).

## Audio {.tabs-close}

The audio system is carried out by the **Audio Processing Unit** (APU), a [PSG chip](master-system#audio) with four channels [@audio-wiki].

### Functionality

Each channel is reserved for a type of wave-form:

#### Pulse {.tabs .active}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the pulse 1 channel.](pulse_single_1){.active video="true" title="Pulse 1"}

![Oscilloscope view of the pulse 2 channel.](pulse_single_2){video="true" title="Pulse 2"}

![Oscilloscope view of all audio channels.](pulse_full){video="true" title="Complete"}

Pokemon Red/Blue (1996).

:::

Pulse waves have a very distinct *beep* sound that is mainly used for **melody or sound effects**.

The APU reserves two channels for one pulse wave each. These use one of four different tones constructed by varying their pulse width. The first channel has an exclusive **Sweep control** available.

Due to the limited number of channels, the melody will often be interrupted when effects have to be played as part of the gameplay. This is very noticeable in games like Pokemon Red/Blue when, during a battle, the Pokemon's cry will overlap all the channels used for music.

#### Noise {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the noise channel.](noise_single){.active video="true" title="Noise"}

![Oscilloscope view of all audio channels.](noise_full){video="true" title="Complete"}

Pokemon Red/Blue (1996).

:::

Noise is basically a set of random wave-forms that sound like white static. One channel is allocated for it.

Games use it for percussion or *ambient* effects.

This channel has only 2 tones available to use, one produces *clean static* and the other produces *robotic static*. Its frequency can also be controlled.

#### Wave {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the wave channel.](wave_single){.active video="true" title="Wave"}

![Oscilloscope view of all channels.](wave_full){video="true" title="Complete"}

Pokemon Red/Blue (1996).

:::

The APU allows defining a **custom wave-form** to be heard from its fourth channel. The wave is composed of 32 4-bit samples which are stored in a wavetable.

This channel also allows controlling its frequency (enabling it to produce different musical notes from the same entry) and volume.

### Secrets and Limitations {.tabs-close}

The mixer outputs stereo sound, so the channels can be assigned to the left side or on the right one, this is only possible to hear from the headphones though! The speaker is mono.

Furthermore, the mixer chip is also connected to a dedicated pin on the cartridge, allowing to stream an **extra channel** with the condition that the cartridge has to actually output the analogue sound (only possible with extra hardware). No game in the market ended up using this feature.

## Games

Games are written in assembly and they have a maximum size of **32 KB**, this is due to the limited address space available. However, with the use of a **Memory Bank Controller** (mapper), games can reach bigger sizes. The biggest cartridge found in the market has a 1 MB ROM.

Cartridges can include a real-time clock and an external battery along with SRAM to hold saves.

### External communications

For the first time, games can communicate with other Game Boys using of a **Game Boy Link** cable, which provides multiplayer functionality. The interface relies on a very primitive type of **serial connection**.

## Anti-piracy

This console contains a **256 Byte ROM** stacked in the CPU that is used to bootstrap the cartridge's ROM.
It doesn't run the game right away however, it first executes a series of checks that **prevent the execution of unauthorised cartridges** and also makes sure the cartridge is **correctly inserted**.

To be able to pass these checks, games had to include a copy of Nintendo's logo (in the form of tiles) in its ROM header [@games-dhole], this way Nintendo could make use of **Copyright and Trademark** laws to control the distribution, *Clever huh?*. The Game Boy ROM also embeds a copy of the logo to be able to compare it.

That being said, the boot process is as follows [@anti_piracy-boot]:

1. After the console is switched on, the CPU starts reading at address **0x00** (Game Boy's ROM location).
2. RAM and Sound are initialised.
3. Nintendo logo is copied from the cartridge ROM to Display RAM, and then it's drawn at the top edge of the screen. If there is no cartridge inserted, the logo will contain garbage tiles. The same may happen if it's badly inserted.
4. The logo is scrolled down and the famous *po-ling* sound is played.
5. The game's Nintendo logo is matched against the one stored in the console's ROM, if the check fails the console freezes.
6. A quick **checksum** is done on the cartridge's ROM header to make sure the cartridge is correctly inserted, if the check fails the console freezes.
7. The console's ROM is removed from the memory map.
8. CPU starts executing the game.

Interestingly enough, the *Nintendo* logo displayed on the screen is not cleared from VRAM, so games can apply some animation and effects to introduce their own logo.

![20y, a homebrew demo that fiddles with the logo.](20y){video="true"}

More anti-piracy measures can be implemented inside games, like checking the SRAM size (it's normally bigger in Bootlegs) and checksumming the ROM at random points of the game.

## That's all folks

