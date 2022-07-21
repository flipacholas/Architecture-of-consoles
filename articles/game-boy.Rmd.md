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

```{r results="asis"}
supporting_imagery()
```

## CPU

Instead of placing many off-the-shelf chips on the motherboard, Nintendo opted for a single chip to house (and hide) most of the components, including the CPU. This type of chip is called 'System On Chip' (SoC) and the one found on the GameBoy is referred to as **DMG-CPU** or **Sharp LR35902** `r cite("cpu-realboy")`.

Having said that, the main processor is a **Sharp SM83** `r cite("cpu-gekkio")` and it's a mix between the Z80 and the Intel 8080. It runs at **~4.19 MHz**.

The Z80 is itself a superset of the 8080, so what does the SM83 actually has and has not from those two? `r cite("cpu-steil")`

- Neither Z80's `IX` and `IY` registers nor 8080's `IN` or `OUT` instructions are included: This means that [I/O ports](`r ref("master-system#accessing-the-rest-of-the-components")`) are not available. I'm not certain if that's just a measure to reduce costs, but one thing for sure is that components will have to be **completely memory-mapped** `r cite("cpu-fayzullin")`.
- Only 8080's set of registers are implemented.
- Includes Z80's extended instruction set. Although, only bit manipulation instructions are found.

Finally, they also added a **few new instructions** that are not present in either Z80 or 8080. I think explaining them one by one goes beyond the scope of this article, but the main idea is that they optimise certain operations conditioned by the way Nintendo/Sharp arranged the hardware.

### Memory available

Nintendo fitted **8 KB of RAM** for general purpose use (which they call **Work RAM** or 'WRAM') `r cite("cpu-nintendo")`. Notice that this is four times larger than what the [NES](`r ref("nes")`) included.

### Hardware access

The SM83 keeps an 8-bit data bus and a 16-bit address bus, so up to 64 KB of memory can be addressed. The memory map is composed of `r cite("cpu-mongenel")`:

- Cartridge space.
- WRAM and Display RAM.
- I/O (joypad, audio, graphics and LCD)
- Interrupt controls.

## Graphics

All graphics calculations are done by the CPU, and then the **Picture Processing Unit** or 'PPU' renders them. This is another component found inside DMG-CPU and it's actually based on the [predecessor's PPU](`r ref("nes#graphics")`).

The picture is displayed on an integrated LCD screen, it has a resolution of **160×144 pixels** and shows **4 shades of grey** (white, light grey, dark grey and black). But since the original Gameboy has a green LCD, graphics will look *greenish*.

If you've read the NES article before, you may remember that the PPU was designed to follow the CRT beam. However (and for obvious reasons), we got an LCD screen in the Gameboy. Well, the new PPU doesn't alter that part, since LCDs require to be refreshed too. In fact, some special effects achieved thanks to this behaviour will also be supported on the Gameboy.

### Organising the content

(ref:ppumemcaption) Memory architecture of the PPU.

```{r fig.cap="(ref:ppumemcaption)", fig.align='center', centered=TRUE}
image("ppu.png", "(ref:ppumemcaption)", class = "centered-container")
```

The PPU has **8 KB of VRAM** or 'Display RAM', which both PPU and CPU can access directly but not at the same time. Those 8 KB will contain most of the data the PPU will need to render graphics. The remaining bits will be stored inside the PPU instead, as they will require a faster access rate.

The game is in charge of populating the different areas with the correct type of data. Moreover, the PPU exposes registers so the game can instruct the PPU on how that data is organised (there are many rules, though).

### Constructing the frame

Let's see now how the PPU manages to draw stuff on the screen. For demonstration purposes, *Super Mario Land 2* will be used as an example:

(ref:tilestitle) Tiles

(ref:tilesfooter) Tiles found in the Pattern Table.

(ref:tilalltitle) All

(ref:tilallcaption) Multiple tiles.

(ref:tilgridtitle) Grid

(ref:tilgridcaption) Multiple tiles separated with a grid.

(ref:tilsingletitle) Single

(ref:tilsinglecaption) A single tile.

```{r fig.cap=c("(ref:tilallcaption)", "(ref:tilgridcaption)", "(ref:tilsinglecaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:tilestitle)", tab.active = TRUE, tab.first=TRUE, tab.nested=TRUE, tab.figure=TRUE, tab.float=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/tiles.png', "(ref:tilallcaption)", tab.name = "(ref:tilalltitle)", tab.active = TRUE)
image('ppu_mario/tiles_grid.png', "(ref:tilgridcaption)", tab.name = "(ref:tilgridtitle)")
image('ppu_mario/tiles_single.png', "(ref:tilsinglecaption)", tab.name = "(ref:tilsingletitle)")
figcaption("(ref:tilesfooter)")
```

The PPU uses **tiles** as a basic ingredient for rendering graphics, specifically, **sprites and backgrounds** `r cite("graphics-emu")`.

Tiles are just **8x8 bitmaps** stored in VRAM in a region called **Tile set** or 'Tile pattern table', each pixel corresponds to one of the four shades of grey available. Finally, tiles are grouped into two pattern tables.

In order to build the picture, tiles are referenced in another type of table called a **Tile map**. This information will tell the PPU where to render the tiles. Two maps are stored to construct different layers of the frame.

The next sections explain how tile maps are used to construct the layers.

(ref:bgtitle) Background Layer

(ref:bgfooter) Background map rendering process.

(ref:bgalltitle) Full

(ref:bgallcaption) Allocated Background map in VRAM.

(ref:bgseltitle) Selected

(ref:bgselcaption) Selected area of the Background map. Notice the selected part includes one portion of the top, this will be overlapped by the _Window layer_.

(ref:bgrentitle) Rendered

(ref:bgrencaption) Displayed Background map.

```{r fig.cap=c("(ref:bgallcaption)", "(ref:bgselcaption)", "(ref:bgrencaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:bgtitle)", tab.float=TRUE, tab.nested=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/background.png', "(ref:bgallcaption)", tab.name = "(ref:bgalltitle)", tab.active = TRUE)
image('ppu_mario/background_selected.png', "(ref:bgselcaption)", tab.name = "(ref:bgseltitle)")
image('ppu_mario/background_rendered.png', "(ref:bgrencaption)", tab.name = "(ref:bgrentitle)")
figcaption("(ref:bgfooter)")
```

The Background layer is a **256x256 pixel** (32x32 tiles) map containing **static tiles**. However, remember that only 160x144 is viewable on the screen, so the game decides which part is selected for display. Games can also move the viewable area during gameplay, that's how the **Scrolling Effect** is accomplished.

One of the two tile maps can be used to build the background layer.

(ref:wintitle) Window

(ref:winfooter) Window map rendering process.

(ref:winalltitle) Full

(ref:winallcaption) Allocated Window map.

(ref:winrentitle) Rendered

(ref:winrencaption) Displayed Window map. The game activates it during the last scan-lines. Hence, only the first rows are rendered at the bottom of the screen.

```{r fig.cap=c("(ref:winallcaption)", "(ref:winselcaption)", "(ref:winrencaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:wintitle)", tab.float=TRUE, tab.nested=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/window.png', "(ref:winallcaption)", tab.name = "(ref:winalltitle)", tab.active = TRUE)
image('ppu_mario/window_rendered.png', "(ref:winrencaption)", tab.name = "(ref:winrentitle)")
figcaption("(ref:winfooter)")
```

The Window is a **160x144 pixel** layer containing tiles displayed on top of the background and sprites. It doesn't scroll.

The remaining tile map can be assigned to the window layer. 

At first, this may sound like a silly feature. After all, the window layer overlaps everything else so what's it useful for? Well, both Background and Window can be used concurrently at different parts of the screen. This is accomplished by changing the `LCDCONT` register during specific scan lines.

Thus, games normally use it to display player stats, scores and other 'always-on' information.

(ref:spritestitle) Sprites

(ref:spritescaption) Rendered Sprite layer.

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)"}
image('ppu_mario/sprite.png', "(ref:spritescaption)", float=TRUE, class="pixel")
```

Sprites are tiles that can move independently around the screen. They can also overlap each other and appear behind the background, the viewable graphic will be decided based on a priority attribute.

They also have an extra colour available: **Transparent**. So, they can only display three different greys instead of four. Luckily, this layer allows defining two colour palettes to make use of every colour.

The **Object Attribute Memory** or 'OAM' is a map stored inside the PPU which specifies the tiles that will be used as sprites. Games fill this region by calling the **DMA unit** found inside the chip, the DMA fetches data from main RAM or game ROM to OAM.

Apart from the tile index, each entry contains the following attributes: X-Y position, colour palette, priority and flip flags (allowing to rotate the tile vertically and horizontally).

The PPU is limited to rendering up to **ten sprites per scan-line** and **up to 40 per frame**, overflowing this will result in sprites not being drawn.

(ref:resulttitle) Result

(ref:resultcaption) Final result. _Tada!_

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)"}
image('ppu_mario/result.png', "(ref:spritescaption)", float=TRUE, class="pixel")
```

Once the frame is finished, it's time to move on to the next one! However, the CPU can't modify the tables while the PPU is reading from VRAM, so the system provides a set of interrupts triggered when the PPU is idle. You can recall this behaviour from the times of the NES.

When a single scan-line is complete, the **Horizontal Blank** interrupt is called. This allows to fiddle with the part of the frame that has not yet been drawn.

When all scan-lines are complete, the **Vertical Blank** interrupt is called. The game can now update the graphics for the next frame.

There's an extra state called **OAM search** that is triggered at the start of the scan-line, at this point the PPU is processing which sprites will be displayed in that scan-line, so the game can update any region except OAM.

`r close_tabs()`

### Secrets and Limitations

The inclusion of the Window layer and extra interrupts allowed for new types of content and effects.

(ref:wobbletitle) Wobble effect

(ref:wobblecaption) The Legend of Zelda: Link's Awakening (1993). _Spoilers!_

```{r fig.cap="(ref:wobblecaption)", fig.align='center', tab.title="(ref:wobbletitle)", tab.first=TRUE, tab.active=TRUE, tab.last=TRUE}
video('zelda', "(ref:wobblecaption)", float=TRUE)
```

Horizontal interrupts allowed to alter the frame before being finished. This means that a different scrolling value could be applied at each line, resulting in each row of the frame being shifted at different paces.

This achieved a *Wobbling effect* (I'm not sure that's the official name of it). 

`r close_tabs()`

## Audio

The audio system is carried out by the **Audio Processing Unit** (APU), a [PSG chip](`r ref("master-system#audio")`) with four channels `r cite("audio-wiki")`.

### Functionality

Each channel is reserved for a type of wave-form:

(ref:pulsetitle) Pulse

(ref:pokemonfooter) Pokemon Red/Blue (1996).

(ref:pulse1tabtitle) Pulse 1

(ref:pulse1caption) Oscilloscope view of the pulse 1 channel.

(ref:pulse2tabtitle) Pulse 2

(ref:pulse2caption) Oscilloscope view of the pulse 2 channel.

(ref:audiofulltabtitle) Complete

(ref:audiofullcaption) Oscilloscope view of all audio channels.

```{r fig.cap=c("(ref:pulse1caption)", "(ref:pulse2caption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:pulsetitle)", tab.active = TRUE, tab.first=TRUE, tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('pulse_single_1', "(ref:pulse1caption)", tab.name = "(ref:pulse1tabtitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('pulse_single_2', "(ref:pulse2caption)", tab.name = "(ref:pulse2tabtitle)", caption.post = "(ref:pokemonfooter)")
video('pulse_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

Pulse waves have a very distinct *beep* sound that is mainly used for **melody or sound effects**.

The APU reserves two channels for one pulse wave each. These use one of four different tones constructed by varying their pulse width. The first channel has an exclusive **Sweep control** available.

Due to the limited number of channels, the melody will often be interrupted when effects have to be played as part of the gameplay. This is very noticeable in games like Pokemon Red/Blue when, during a battle, the Pokemon's cry will overlap all the channels used for music.

(ref:noisetitle) Noise

(ref:noisecaption) Oscilloscope view of the noise channel.

```{r fig.cap=c("(ref:noisecaption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:noisetitle)", tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('noise_single', "(ref:noisecaption)", tab.name = "(ref:noisetitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('noise_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

Noise is basically a set of random wave-forms that sound like white static. One channel is allocated for it.

Games use it for percussion or *ambient* effects.

This channel has only 2 tones available to use, one produces *clean static* and the other produces *robotic static*. Its frequency can also be controlled.

(ref:wavetitle) Wave

(ref:wavecaption) Oscilloscope view of the wave channel.

```{r fig.cap=c("(ref:wavecaption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:wavetitle)", tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('wave_single', "(ref:wavecaption)", tab.name = "(ref:wavetitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('wave_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

The APU allows defining a **custom wave-form** to be heard from its fourth channel. The wave is composed of 32 4-bit samples which are stored in a wavetable.

This channel also allows controlling its frequency (enabling it to produce different musical notes from the same entry) and volume.

`r close_tabs()`

### Secrets and Limitations

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

To be able to pass these checks, games had to include a copy of Nintendo's logo (in the form of tiles) in its ROM header `r cite("games-dhole")`, this way Nintendo could make use of **Copyright and Trademark** laws to control the distribution, *Clever huh?*. The Gameboy ROM also embeds a copy of the logo to be able to compare it.

That being said, the boot process is as follows `r cite("anti-piracy-boot")`:

1. After the console is switched on, the CPU starts reading at address **0x00** (Gameboy's ROM location).
2. RAM and Sound are initialised.
3. Nintendo logo is copied from the cartridge ROM to Display RAM, and then it's drawn at the top edge of the screen. If there is no cartridge inserted, the logo will contain garbage tiles. The same may happen if it's badly inserted.
4. The logo is scrolled down and the famous *po-ling* sound is played.
5. The game's Nintendo logo is matched against the one stored in the console's ROM, if the check fails the console freezes.
6. A quick **checksum** is done on the cartridge's ROM header to make sure the cartridge is correctly inserted, if the check fails the console freezes.
7. The console's ROM is removed from the memory map.
8. CPU starts executing the game.

Interestingly enough, the *Nintendo* logo displayed on the screen is not cleared from VRAM, so games can apply some animation and effects to introduce their own logo.

(ref:20ycaption) 20y, a homebrew demo that fiddles with the logo.

```{r fig.cap="(ref:20ycaption)", fig.align='center', centered=TRUE}
video("20y", "(ref:20ycaption)", class = "centered-container")
```

More anti-piracy measures can be implemented inside games, like checking the SRAM size (it's normally bigger in Bootlegs) and checksumming the ROM at random points of the game.

## That's all folks

