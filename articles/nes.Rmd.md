---
long_title: "Architecture of the Nintendo Entertainment System (NES)"
short_title: "Nintendo Entertainment System (NES) Architecture"
name: NES
long_name: Nintendo Entertainment System (NES)
subtitle: More than a 6502 machine
date: 2019-01-25
release_date: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - title: "International"
      file: international
      caption: "The NES.<br>Released on 18/10/1985 in America and 01/09/1986 in Europe."
      active: true
    - title: "Japanese"
      file: japanese
      caption: "The Famicom.<br>Released on 15/07/1983 in Japan."
  Motherboard:
      caption: "Showing the 'NES' variant."
  Diagram: {}

# Historical
aliases: [/projects/consoles/nes]
---

## A quick introduction

At first glance, the NES appears to be just another 6502 computer, with a sophisticated case and a controller.

And while this is *technically* true, let me show you why the CPU is not the *central* part of this system.

## {.supporting-imagery}

## Models and variants

![A typical Betamax recorder. This and similar appliances influenced the international design of the NES. I spotted this particular one at The Centre for Computing History (Cambridge, UK) when I visited in August 2024.](betacord.webp)

Nintendo ended up shipping lots of different variants of the same console across the world [@general-variants] and even though they all share the same architecture, many look dramatically different and some may include built-in accessories. So, to keep it simple for this article, I'll focus on the two most popular revisions:

- The **Family Computer** (known as *Famicom*) was the first incarnation, but was only released in Japan. This toy-looking design features two non-removable controllers (from which the second controller bundles an internal microphone), a front socket for the light gun (called *Zapper*), RF video out (using NTSC-J signal) and extra pins in the cartridge slot to expand the audio capabilities.
- The **Nintendo Entertainment System** (known as *NES*) was the redesigned edition for western audiences living in North America, Europe and Oceania; with a look and mechanism that matches the common VHS/Betamax player. On the technical side, the controllers are now detachable (and microphone-lacking) and the video out has been improved with extra NTSC/PAL composite RCA connectors, although the audio expansion has been replaced with an anti-piracy subsystem. To top it off, the bottom of the case seals an 'expansion port' that was left unused, along with extra cartridge pins that communicate to that port [@general-cartridge].

Because the author grew up with the 'NES' name, I'll default to using that term to refer to the console in general, but I will switch to the 'Famicom' name when referring to unique capabilities only found in the Japanese variant.

## CPU

The NES's CPU is a **Ricoh 2A03** [@cpu-cpu], which is based on the popular 8-bit **MOS Technology 6502** and runs at **1.79 MHz** (or 1.66 MHz in PAL systems).

### A bit of context

The CPU market in the late 70s and early 80s was quite diverse. 

::: {.subfigures .side-by-side}

![A Commodore PET, carrying a 6502 CPU.](pet.webp){.toleft}

![A Tandy TRS-80, carrying a Z80 CPU.](tandy.webp){.toright}

The panorama of late-70s computers, also provided by The Centre for Computing History (Cambridge, UK).

:::

If a company wanted to build an affordable microcomputer, the following options were available:

- The **Intel 8080**: a popular CPU featured in the *Altair*, the first 'personal' computer. It has an 8-bit data bus and a 16-bit address bus.
- The **Zilog Z80**: an 'unofficial' version of the 8080 enhanced with more instructions, registers and internal components. It was sold at a cheaper price and could still execute 8080 programs [@cpu-re_1977, p. 86]. Amstrad and Sinclair (among others) chose this CPU. 
- The **Motorola 6800**: another 8-bit CPU designed by Motorola, it contains a completely different instruction set. Many do-it-yourself computer kits, synthesisers and all-in-one computers included the 6800.

As if these options weren't enough, another company named **MOS** appeared on the market and offered a redesigned version of the 6800: the **6502**. While incompatible with the rest, the new chip was much *much* less expensive to produce [@cpu-summary_costs] [@cpu-re_1981, p. 76] and it was only a matter of time before iconic computer makers (Commodore, Tandy, Apple, Atari, Acorn and so forth) chose the 6502 to power their machines.

Back in Japan, Nintendo needed something inexpensive but familiar to develop for, so they selected the 6502. **Ricoh**, their CPU supplier, successfully produced a 6502-compatible CPU.

#### Ricoh's licensing enigma

*How* Ricoh managed to clone the 6502 isn't clear to this day. One would expect MOS to have licensed the chip design to Ricoh, but there are many contradictions to this:

- Both Ricoh's and MOS's variants feature the same layout, but Ricoh's contains severed buses (disabling certain functions) [@cpu-differences]. I go into more details later.
- A document explicitly stating that MOS licensed the 6502 to Ricoh is yet to be found.
- An article published in 2008 by Nikkei Trendy states that Ricoh licensed from Rockwell, an authorised chip manufacturer [@cpu-trendi]. However, it's debatable whether a second source was able to provide IP to a third party, much less with MOS's approval.
- It wouldn't be the first time Nintendo got away with circumventing IP rights, as *Ikegami Tsushinki v. Nintendo* ruled in Japan that Nintendo didn't own the code of the original Donkey Kong [@cpu-dk].

#### Scrapped functions

The Ricoh 2A03 omits the **Binary-Coded Decimal** (BCD) mode originally included in the 6502 [@cpu-visualbcd]. BCD encodes each decimal digit of a number as a separate 4-bit binary. The 6502 uses 8-bit 'words' - meaning that each word stores two decimal digits.

As an example for the curious, the decimal number `42` is represented as:

- `0010 1010` in binary.
- `0100 0010` in BCD.

We could go on and on talking about it, but to give an outline: BCD is useful for applications that require treating each decimal place separately (for instance, a digital clock). However, it requires more storage since each 8-bit word can only encode up to the decimal number `99` - whereas traditional binary can encode up to `255`.

In any case, Ricoh deliberately broke BCD mode in its chip by severing the control lines that activate it. This was presumably done to avoid paying royalties to MOS, since BCD was patented by them (and the legislation that enabled to copyright integrated circuit layouts in the United States wasn't enacted until 1984 [@cpu-protection_act]).

### Memory

Both Ricoh 2A03 and MOS 6502 feature an **8-bit data bus** and a **16-bit address bus**, which allows them to access up to **64 KB of memory**. So, how did Nintendo fill that memory space?

On one side, the motherboard contains a chip providing **2 KB of Static RAM** (SRAM) [@cpu-sample_ram]. Nintendo calls this area 'Work RAM' (WRAM) and can be used to store:

- Variables for handling the game state and/or to look up information.
- The 'stack', which temporarily saves register values while the CPU is executing sub-routines.
- A 'buffer area' so the CPU can copy large data between two locations.

On the other side, the components of the system are **memory-mapped** [@cpu-cpu_map], meaning that they are accessed using memory addresses, thus, they occupy part of the CPU's address space. The CPU's memory space is therefore filled with addresses pointing to the game cartridge, WRAM, the PPU, the APU and two controllers (don't worry about each component, as they are explained throughout this article).

#### Cartridge/game data

Just in case you don't know, NES games are distributed in the form of cartridges, and the cartridge's buses connect directly to the CPU.

Nintendo wired up the cartridge lines in a way that only 49120 Bytes (~ 49.97 KB) of cartridge data can be accessed [@cpu-cpu_map]. Now, what do I mean with 'cartridge data'? Well, any chip connected to those buses, for instance:

- A **Program ROM** where the game's program resides. This excludes the graphics data, as you'll later see in the 'Graphics' section. Naturally and unlike the other chips, this one is mandatory.
- **RAM chips** to extend WRAM.
- A **battery-packed RAM chip** to store saves.

The fact there are different combinations comes down to the fact the CPU doesn't care about what kind of component is reading from, it only sees memory locations. So is up to game studios to choose (or come up with) a feasible layout to fit their game in.

![PCB of Super Mario Bros [@photography-nrom].](nrom.png){.tabs-nested .active title="Original"}

![The same PCB with important parts labelled. The meaning of the 'Lockout' chip is explained in the 'Anti-piracy' section.](nrom_marked.png){.tabs-nested-last title="Marked"}

For example, Nintendo's 'Super Mario Bros' used a layout they call *NES-NROM-256* and consists of 32 KB of program ROM and 8 KB of 'Character ROM' for graphics (we'll see more about it in the 'Graphics' section) [@cpu-nrom]. *NES-NROM-256* was also prepared to house up to 3 KB of extra WRAM, though the game doesn't make use of it.

#### Going beyond existing capabilities

One of the big limitations of 16-bit address buses (affecting 3rd and 4th-generation consoles) is their compact address space. Nowadays, 32-bit computers can address up to 4 GB of memory (and 64-bit machines lavishly enjoy up to 16 exabytes) so this is not a concern anymore, but back then the NES only had a 64 KB address space and a great part of it was consumed by the memory-mapped hardware (something [competitors avoided](master-system#accessing-the-rest-of-the-components)).

So, did this mean game studios could only develop games that didn't exceed the 49.97 KB limit? Absolutely not! If history taught us something, is that there's always a clever solution to a challenging problem; and this issue was tackled with a **Mapper**.

![Simplified representation of how a mapper extends the addressing capabilities of the CPU. With the inclusion of a mapper, the CPU can access extra banks (groups of addresses) of a large Program ROM. Although the game/program has the new task of manually switching between banks whenever needed.](mapper/mapper.png){.tabs-nested .active title="With mapper"}

![The same setup but without a mapper installed. While simpler and inexpensive, the CPU can only access a finite number of banks.](mapper/no_mapper.png){.tabs-nested-last title="Without mapper"}

A mapper is an extra chip included in the cartridge that sits between the memory chips and the console's address lines. Its main job is to extend the address space so developers may fit more chips. This is done by **bank switching**: Memory addresses are grouped into banks, and the mapper provides switches (controlled through memory addresses) to alternate between banks. Now, the CPU still sees the same amount of memory, so it's the game that's been programmed with a mapper present in charge of operating it. Due to their cost-effectiveness, mappers were the order of the day in 80s-to-early 90s technology.

![PCB of Super Mario Bros 2 [@photography-tsrom]. Super Mario Bros 3 also uses this layout but bundles a 256 KB Program ROM instead.](tsrom.png){.tabs-nested .active title="Original"}

![The same picture with important parts labelled. At first, I thought the extra WRAM was for storing saves, but then I realised there are no saves in this game (and there isn't a battery either). In reality, that RAM chip is used to store a decompressed level.](tsrom_marked.png){.tabs-nested-last title="Marked"}

Back to the NES, games like 'Super Mario Bros 2' and 'Super Mario Bros 3' shipped with the 'MMC3' mapper (made by Nintendo) in their cartridges. For comparison, MMC3 provided up to 512 KB of space for the Program ROM, up to 256 KB for Character memory and up to 8 KB for extra WRAM [@cpu-mmc3]. You can now see why 'Super Mario Bros 3' differs significantly in quality compared to the first installment.

All in all, while this console may appear limited while examining its internal features, Nintendo made sure it could adapt as technology evolves. On the other side, while this technique helped to keep the costs down of the console, it shifted part of the burden to the game cartridge. So, game quality and cartridge costs were two concerns game studios had to balance.

## Graphics

Graphics are generated by a proprietary chip called the **Picture Processing Unit** (PPU). This is one of the chips that give the NES an *identity*. To put it in another way, anyone can pick up a 6502 CPU from the hardware store, so why is the NES any different from, say, an Apple 2 or a Commodore 64? Well, what distinguishes the NES from other machines are the chips that surround the CPU: The PPU and the APU. These make up the NES' unique graphics and audio capabilities, respectively.

That being said, the PPU renders 2D graphics called **sprites** and **backgrounds**, outputting the result to the video signal.

### Organising the content

![Memory architecture of the PPU](ppu_content.png)

To render something on the screen, the PPU must know *what* graphics to draw, *where* on the screen to place them; and *how* to draw them (i.e. which palette to use).

To answer these questions, the PPU came pre-programmed with a different memory map that looks for the following type of data:

- Graphics data is pulled from the game cartridge, which contains a dedicated chip called **Character Memory** that stores the 2D drawings (called **tiles**) organised into a data structure named **Pattern table**. Character Memory materialises in the form of 'Read Only Memory' (ROM) or 'Random Access Memory' (RAM) depending on whether the game ships with an immutable set of graphics or the CPU must intervene, respectively.
  - The PPU addresses **up to 8 KB** of Character memory organised into two groups of 4 KB each.
- Meta-data telling the PPU 'where' and 'how' to draw graphics is found in other areas:
  - A separate **2 KB of SRAM** is fitted onto the motherboard, this time dedicated to graphics-related data. Nintendo calls this space **Video RAM** (VRAM) and it stores two data structures called **Nametables**.
  - The PPU embeds **256 B** of DRAM to store the **Object Attribute Memory** (OAM).
  - Lastly, the PPU also houses **4 B** of memory to define colour palettes.

Don't worry about the new terminology, the meaning of these data structures is discussed step-by-step in the following paragraphs.

### Constructing the frame

As with its contemporaries, this chip is designed for the behaviour of a CRT display. There is no frame buffer as such: the PPU will render in step with the CRT's beam, building the image on the fly.

The PPU draws frames with a fixed dimension of **256x240 pixels** [@graphics-overscan]. Alas, due to the discrepancies in analogue video standards across the world, the image will differ in appearance depending on the region of the appliance (NTSC or PAL) from which is displayed. In a nutshell, NTSC televisions will crop the top and bottom edges to accommodate overscan (only ~224 scanlines are visible), so these edges are considered 'danger zones' by developers when deciding where to place elements on the game. On the other hand, PAL tellies won't crop the edges but will show extra black bars to fill the taller signal (PAL uses 288 scanlines).

Behind the scenes, the frame that the PPU outputs is composed of two different layers. For demonstration purposes, let's use *Super Mario Bros.* to show how this works:

#### Tiles {.tabs .active}

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined max_subfigures=1}

![Two pattern tables with multiple tiles squashed together.](ppu_mario/chr_map.png){.active title="All"}

![A single tile.](ppu_mario/single.png){title="Single"}

Tiles Found in Character ROM (for demonstration purposes a default palette is being used).

:::

To begin with, the PPU uses **tiles** as a basic ingredient for producing sprites and backgrounds.

The NES defines tiles as basic **8x8 pixel maps**, these are stored in **Character memory** (residing in the game cartridge) and organised into a big data structure called **Pattern Table** [@graphics-rust]. Each tile occupies 16 B and a Pattern table houses 256 tiles [@graphics-pattern]. Since the PPU addresses up to 8 KB of Character memory, it can access up to two Pattern tables.

Inside a tile, each of its pixels is encoded using a 2-bit value, which references one of four colours from a palette. Programmers can define up to eight palettes (four for background and the other for sprites). The colours referenced on each palette point to a 'master palette' made of 64 colours [@graphics-palettes], representing all the colours this console can produce. Palettes are made of four colours, though one is reserved for `transparent`.

To start drawing something on the screen, games populate a set of tables with references to tiles in Character memory. Each table is responsible for one layer (sprite or background) of the frame. Then, the PPU reads from those tables and composes the scanlines that will be beamed by the CRT gun.

I will now explain how each layer/table works and how they differ in terms of functionality.

#### Background Layer {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Allocated background map.](ppu_mario/nametable.png){.active title="Overall"}

![Allocated background map with selected area marked.](ppu_mario/nametable_marked.png){title="Selected"}

Background map set up with vertical mirroring, which enables smooth horizontal scrolling. However, only one half can be used.

:::

The background layer is a 512x480 pixel map containing static tiles [@graphics-nametables]. You may recall that the viewable frame is much smaller, so the game decides which part of the layer is selected for display. Games can also move the viewable area during gameplay; that's how the **scrolling effect** is accomplished.

To save memory, groups of four tiles are combined into 16x16 pixel maps called **blocks**, within which all tiles share a colour palette.

**Nametables** (stored in VRAM) specify which tiles to display in the background layer. The PPU looks for four 1024-byte Nametables, each one corresponding to a quadrant of the layer. However, there are only 2 KB of VRAM available! As a consequence, only two Nametables can be stored without additional hardware from the cartridge. Yet, the remaining two still have to be addressed somewhere: most games just point the remaining two where the first two are (this is called **mirroring**).

While this architecture may seem flawed at first, it was designed to keep costs down while providing simple **expandability**: if games needed a wider background, extra VRAM could be included in the cartridge.

The last bytes of each Nametable store a 64-byte **Attribute table** that specifies which colour palette is assigned to each block [@graphics-attribute_table].

#### Sprite Layer {.tab}

![Rendered sprite layer.](ppu_mario/sprite_layer.png){.tab-float .pixel}

Sprites are tiles that can move around the screen. They can also overlap each other, or appear behind the background. The viewable graphic will be decided based on its priority value (it's the same concept as 'layers' in traditional graphic design software).

The **Object Attribute Memory** (OAM) table specifies which tiles will be used as sprites [@graphics-oam]. In addition to the tile index, each entry contains an (x,y) position and multiple attributes (colour palette, a priority and flip flags). This table is stored in a 256-byte DRAM found in the PPU chip.

The OAM table can be filled by the CPU. However, this can be pretty slow in practice (and risks corrupting the frame if not done at the right time), as a consequence, the PPU contains a small component called **Direct Memory Access** or 'DMA' which can be programmed (by altering the PPU's registers) to fetch the table from WRAM. With DMA, it's guaranteed that the table will be uploaded when the next frame is drawn, but bear in mind that the CPU will be halted during the transfer!

The PPU is limited to **eight sprites per scanline** and up to **64 sprites per frame**. The scanline limit can be exceeded thanks to a technique called 'OAM order rotation', where the game manually alters the order of entries in OAM. This makes the PPU render a different sprite set at each frame, and the speed of the CRT beam will trick the user into seeing more sprites than allowed. However, they will also appear to flicker on-screen.

#### Background split {.tab}

![Rendered background layer highlighting the two portions with different scrolling values defined. Only the second portion scrolls as Mario moves.](ppu_mario/split.png){.tab-float .pixel}

Before we move on, there's something I haven't told you yet. If you play Super Mario Bros, you'll notice that when Mario moves, the scene scrolls without a hitch. However, you'll also observe that the top area (where the stats are) remains static **even though both portions are part of the same background layer!** So, what is happening here? Well, the game is altering the scrolling values mid-frame to show the overworld and the stats (residing in a fixed portion of the background) at the same time. The NES doesn't provide this feature natively, but the game deduces the timings by observing the state of the PPU (manifested through its status register [@graphics-ppustatus]).

To accomplish this, games perform a technique called **Sprite 0 Hit**. Super Mario Bros instructs the PPU to render a dummy sprite behind the coin, this happens to be the first sprite drawn within the frame. After the PPU beams it, it updates its status register with a flag that denotes that the first sprite (a.k.a 'sprite 0') has been drawn. Meanwhile, the game is constantly checking mid-frame if the sprite 0 status has been flagged (a.k.a 'hit'), if that happens, the game proceeds to update the scrolling property of the background table to shift it to where Mario is.

Overall, 'Sprite 0 Hit' is a very delicate procedure, as it's easy to mess up the timings (sprite 0's flag is not cleared after polling it, which leads to 'duplicated' positives [@graphics-chibiakumas]). Furthermore, since this routine repeats indefinitely, it can be quite expensive (in terms of CPU cycles) to execute. On the bright side, later mappers took over this function with the use of automatic interrupts that are triggered whenever an arbitrary scan-line is hit [@graphics-nesdoug] (a much more efficient technique), which significantly improved the visual capabilities of Super Mario Bros 3, for instance.

#### Result {.tab}

![Tada!](ppu_mario/result.png){.tab-float .pixel}

Once the frame is finished, it's time to move on to the next one!

However, the CPU can't modify any table that's currently being used by the PPU, otherwise, artefacts may show up on the screen. So, when all scanlines are completed, the PPU triggers the **Vertical Blank** (V-Blank) interrupt on the CPU [@graphics-vblank]. This notifies the game that it can start updating the tables without tearing the picture currently displayed. At that moment the CRT's beam is pointing below the visible area of the screen, into the overscan (or bottom border area).

Only a handful of PPU registers can be updated outside the V-Blank window [@graphics-outside_vblank], which explains the ability to scroll the background layer mid-frame.

### Secrets and limitations {.tabs-close}

If you're thinking that a frame-buffer system with memory allocated to store the full-frame would have been preferable: RAM costs were very high, and the console's goal was to be affordable. Let me now show you why this design proved to be very efficient and flexible.

#### Multi-Scrolling {.tabs .active}

![Super Mario Bros. 2. Nametable setup for vertical scrolling (horizontal mirroring).](secrets/multiscrolling_mirror.png){.tab-float .pixel}

![Super Mario Bros. 3. Mario can run and fly, so the PPU needs to scroll diagonally. Notice the right edge showing the wrong colour palette! The left edge has a mask applied.](secrets/multiscrolling.png){.tab-float .pixel}

Some games require the main character to move vertically – thus the nametable will be set up with **horizontal mirroring**. Other games need their character to move left and right, and so use **vertical mirroring** instead.

Either type of mirroring will allow the PPU to update background tiles without the user noticing: there is plenty of space to scroll while new tiles are being rendered at a distance.

But what if the character wants to move diagonally? The PPU can scroll in any direction, but without extra VRAM, the edges are forced to share the same colour palette (remember that tiles are grouped in blocks).

This is why some games like *Super Mario Bros. 3* show strange graphics at the right edge of the screen while Mario moves (the game is set up for vertical scrolling) [@graphics-seam]. It's possible that they needed to minimise the hardware cost per cartridge (as this game has already a powerful mapper installed).

As an interesting *fix*: the PPU allowed developers to apply a vertical mask on top of tiles, effectively hiding part of the glitchy area.

#### Tile-Swapping {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1 .pixel}

![Early scan lines.](secrets/multiplexing_1.png){.pixel .active title="Early"}

![Late scan lines.](secrets/multiplexing_2.png){.pixel title="Late"}

![Actual frame displayed to the user.](secrets/multiplexing_complete.png){.pixel title="Displayed"}

Hypothetical frames rendered using tiles available during specific scan lines.

:::

Another speciality of Super Mario Bros. 3 is the amount of graphics it can display.

This game displays more background tiles than is strictly permitted. So how is it doing that? If we take two screen captures at different times while the display is generated, we can see that the final frame is actually composed of *two* different frames.

This is another wizardry of the MMC3 mapper, which was not only used to access extra space in the Program ROM, but also extends the Character ROM space by connecting two different Character chips. By checking which part of the screen the PPU is requesting, the mapper will redirect to one chip or the other – thus allowing more unique tiles on-screen than was originally supported [@graphics-n3s].

#### Curious behaviour {.tab}

Throughout my research, I came across many interesting articles that explain unusual behaviour of the PPU, so I thought to mention some here:

- Unlike the [Master System's VDP](master-system#graphics), which generates RGB colours that are later encoded into NTSC/PAL signals for broadcasting, the **NES' PPU does all at once** [@graphics-palettes]. Hence, there isn't a one-to-one connection between the colours of the PPU master palette and the standard RGB colourspace (widely adopted by present technology). This leaves some room for interpretation and, as a consequence, various emulators will display a different palette.
  - The discrepancies between RGB palettes are most evident with Tim Worthington's DIY kit that adds RGB signal output to the NES, since it also implements a switch that chooses between three pre-defined palettes [@graphics-nesrgb].
- The master palette contains a **'cursed' colour** (`$0D`) which might mess up the NTSC TV signal [@graphics-cursed_colour]. Well, what happens is that some TVs mistake the signal to display that colour with the blanking signal, so flickering may occur.
- The PPU relies on DRAM to store its Object Attribute Memory (OAM). Now, DRAM requires to be refreshed constantly to prevent loss of data (unlike SRAM), and it so happens that the PPU won't refresh DRAM when it's not rendering the frame [@graphics-oam]. This manifests during vertical blanking. For this reason, it is advised against updating OAM outside vertical blanking, since the non-refreshing period happening during V-blank will have corrupted part of the table.
  - The PPU variant for PAL systems is not affected by this, as it refreshes during V-Blank (which lasts longer on PAL systems).

## Audio {.tabs-close}

A dedicated component called **Audio Processing Unit** (APU) provides this service [@audio-apu]. Ricoh embedded it inside the CPU chip presumably to avoid unlicensed cloning of both CPU and APU.

### Functionality

This audio circuitry is commonly referred to as a **Programmable Sound Generator** (PSG), which vaguely implies that it can only produce a pre-defined set of waveforms, that's *mostly* true in this case. The APU sequences audio data over **five channels** of audio – each one reserved for a specific waveform or signal. Each channel contains different properties that alter the waveform's pitch, sound, volume and/or duration. They are continuously mixed and sent through the output audio signal.

The APU's functionality is exposed through memory addresses, the CPU reads the music-related data found in the Program ROM and programs the APU accordingly.

Furthermore, the Famicom model in particular provides cartridge pins that send the mixed audio signal to the cartridge, so the latter can mix it with **extra channels** (requiring additional chips) [@general-cartridge].

Let's now go over the five channels the APU provides [@audio-review]:

#### Pulse {.tabs .active}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the pulse 1 channel.](pulse_single_1){video="true" title="Pulse 1"}

![Oscilloscope view of the pulse 2 channel.](pulse_single_2){.active video="true" title="Pulse 2"}

![Oscilloscope view of all audio channels.](pulse_full){video="true" title="Complete"}

Mother (1989).

:::

The first **two channels** produce **pulse waves** [@audio-apupulse]. When heard, they exhibit a very distinct *beep* sound that is mainly used for **melody or sound effects**. The respective sequencer can generate three types of pulse waves, made from varying the pulse width (a.k.a. **duty cycle**). The circuits are also connected to a **sweep unit** (allowing to bend the pitch) and an **envelope generator** to lower the volume over time (a.k.a. **decay**).

Most games use one pulse channel for melody and the other for accompaniment. You'll often find that when a game needs to play a sound effect, the accompaniment channel is switched to play the effect and then returns to accompanying. This avoids interrupting the melody during gameplay.

I think it's fair to say that pulse waves are one of the emblems of this generation of consoles. I assume its adoption was purely made for cost-effective reasons: the (limited) CPU can only process so much data at a time, and pulse waves are ideal in the sense they don't require many parameters to play simple melodies (which, in turn, frees up CPU cycles for other operations).

#### Triangle {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the triangle channel.](triangle_single){.active video="true" title="Triangle"}

![Oscilloscope view of all audio channels.](triangle_full){video="true" title="Complete"}

Mother (1989).

:::

One of the specialities of the APU, when compared to the competition, is the ability to produce **triangle waves**. These are often used as a **bassline** for the melody. Moreover, by modifying its pitch dramatically one can also use it for **percussion**.

The APU has one channel reserved for this type of wave. Behind the scenes, a dedicated sequencer takes 32 cycles to generate a triangle signal [@audio-aputriangle], this limitation makes the resulting triangle waveform take the shape of a step ladder.

On the other side, the respective circuitry does not provide volume control. In any case, some games found other ways by fiddling with the mixer's volume control.

#### Noise {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the noise channel.](noise_single){.active video="true" title="Noise"}

![Oscilloscope view of all audio channels.](noise_full){video="true" title="Complete"}

Mother (1989).

:::

The concept of 'Noise' is attributed to a series of waveforms that don't follow any pattern or order. In turn, our ears interpret it as white static. Having said that, the APU allocates one channel that can play different kinds of noise.

Behind the scenes, the noise generator relies on an envelope generator (similar to the Pulse channel) which gets randomly muted by an OR gate [@audio-apunoise]. The condition for muting depends on the value of a 15-bit shift register connected to a feedback loop. All in all, this makes the circuitry output a signal with *pseudo-unpredictable* patterns, and therefore noise.

In terms of control, 4 bits alter the period of the envelope generator and one bit alters the 'Mode' of the shift register. That leaves 32 noise presets available. Half (16) of these presets produce **clean static**, and the other half produce **robotic static**.

Generally speaking, games use the noise channel for percussion or ambient effects.

#### Sample {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Oscilloscope view of the sample channel.](sample_single){.active video="true" title="Sample"}

![Oscilloscope view of all audio channels.](sample_full){video="true" title="Complete"}

Mother (1989).

:::

Samples are recorded pieces of music that can be replayed. As you can see, samples are not limited to a single waveform, but they consume a lot more space.

The APU has one channel dedicated to samples. Here, samples are limited to **7-bit resolution** (encoded with values from `0` to `127`) and a **~15.74 kHz sampling rate** [@audio-2a03ref]. To program this channel, games can either stream 7-bit values (which steals a lot of cycles and storage) or use **delta modulation** to only encode the variation between the next sample and the previous one.

The delta modulation implementation in the APU only receives 1-bit values, this means games can only tell if the sample increments or decrements by `1` every time the counter kicks in. So, at the cost of fidelity, delta modulation can save games from having to stream continuous values to the APU.

Since programming this channel takes longer space and CPU cycles, games normally store small pieces (like drum samples) that can be played repeatedly. Be as it may, throughout the lifetime of the NES, numerous developers have come up with clever uses for this channel.

### Secrets and limitations {.tabs-close}

While the APU was not comparable to the quality of vinyl, cassette or CD, programmers did find ways of expanding its capability, mainly thanks to the modular architecture of the NES.

#### Extra Channels {.tabs .active}

![Oscilloscope view of Castlevania III (USA/Europe, 1989).](castlevania_usa){.tabs-nested .tab-float .active video="true" title="American"}

![Oscilloscope view of Akumajō Densetsu (Japanese version of Castlevania III, 1989).](castlevania_jap){.tabs-nested-last video="true" title="Japanese"}

Remember that the Famicom provided exclusive cartridge pins available for sound expansion? Well, games like *Castlevania 3* took advantage of this and bundled an extra chip called **Konami VRC6**, which added **two extra pulse waves and a sawtooth wave** to the mix.

Take a look at the two examples that show the difference between the Japanese and the American versions of the game (the latter runs on the NES variant, which didn't provide sound expansion capabilities).

#### Tremolo {.tab}

![Oscilloscope view of Final Fantasy III (1990).](tremolo_full){.tab-float video="true"}

Instead of incrementing cartridge costs, some games prioritised creativity over technology to add more channels.

In this example, Final Fantasy III came up with the idea of using tremolo effects to give the feeling of extra channels.

### A more refined observation {.tabs-close}

Now that you've got a glance at what the APU is capable of, let me show you an alternative method for observing how its sound behaves. This not only will complement what you already know about the APU, but it will also provide a more objective examination, especially since it doesn't rely on your ears anymore.

First things first, let's start with a quick introduction to sound theory.

Thanks to the principles of **Fourier Analysis**, we can decompose every single sound we hear into a **sum of sine waves** of different frequencies and amplitudes [@audio-complexwaveforms]. The bassiest sine wave (at the lowest frequency) is called **fundamental** and the rest are called **overtones**. If you add the fundamental wave and its overtones, you get the original sound back. Having said that, with sounds that have a recognisable pitch, you'll find most (if not all) overtones have frequencies that are multiples of the fundamental frequency. Thus, these overtones are called **harmonics** [@audio-harmonics].

Harmonics will become a recurrent topic in this section, as waveforms such as pulses, triangles and sawtooths follow a formula that dictates the harmonics they contain. Otherwise, these waveforms may deviate from their 'perfect' shape.

#### Introduction to spectrograms

As sine waves are now the key ingredient that can make up any sound, we can now analyse the sounds that we hear by its sine waves. Now, for any kind of data analysis, there's nothing more convenient than plotting a graph to organise vast amounts of information. Well, in the case of sound analysis, we've got **Spectrograms**. These encode all the information of an audio sample in a single plot. The X-axis denotes time (in seconds), the Y-axis denotes the frequencies (in Hz) of sine waves produced during that time and the Z-axis (colour brightness of each dot) denotes the power/loudness (in decibels) of each frequency.

![Example of spectrogram visualising six seconds of a Pulse channel.](spectrogram_example){video="true"}

As you can observe from this example, each horizontal line (a.k.a sequences of dots) corresponds to a sine wave (the lowest is the fundamental, while the rest are the harmonics) and their brightness indicates the amplitude. With this in mind, we can extract the following information:

- Over time, horizontal lines tend to drastically displace. That's the pitch of the Pulse channel changing.
- The start of each note is loud (the dots are bright) followed by a quick quell. That's the APU's envelope control in action.
  - Moreover, at the end of the decay period, a bright vertical line shows up. It's quite brief so it's not easily audible but, in any case, that's noise (a short popping sound) and I suspect it's a deficiency of the APU.
- Holding a note for more than 0.25 seconds makes **vibrato** emerge (continuous fluctuations in the Y-axis). I'm not sure if that's intentional (using the sweep function) or an adverse effect of the APU.

Notice how most of these observations are not easily derived by just hearing an audio simple, this is the reason for writing this section.

#### Plotting the APU

To study the NES' APU, I've compiled five spectrograms, each corresponding to a channel of the APU using the previous examples. Alongside them, you'll find my attempt at unravelling what the data is exhibiting.

Before we start, I must confess that to be able to gather the data without inaccuracies (like extra noise), there have been some compromises. The recordings were obtained using a Windows program called 'towave', which uses **band-limited synthesis** to solve a fundamental problem with the emulation of PSG-based audio chips. Basically, pulses, triangles and sawtooths are made of **infinite harmonics**. However, that doesn't mix well with modern sound cards that are limited to 44.1 kHz samples. So, a technique called 'band-limited synthesis' is used to choose the right harmonics within the sound card's limit. All in all, this technique provides a feasible balance between performance, accuracy and aliasing prevention. However, the data may not be 100% identical to its analogue counterpart (which, by contrast, would also introduce other issues, like noise from the recording equipment), but I believe it is to an acceptable degree and, most importantly, it does the job for this section of the article.

That being said, let's get on with the analysis.

##### Pulse {.tabs .active}

![Spectrogram of the Pulse 1 channel.](spectrograms/eb0_pulse_nes.png){.tab-float}

The theory says that a pulse tone only contains odd harmonics. In other words, the fundamental is combined with its third harmonic, fifth and so forth. Moreover, each harmonic decreases its amplitude as further away it is from the fundamental. The amplitude formula is `amplitude = 1 ÷ harmonic number` [@audio-pulse].

Hence, notice how the brightness of each harmonic on the spectrogram dims the higher it is on the Y-axis. However, the APU's pulse waves also seem to exhibit the aforementioned vibrato effect which increases at each harmonic number. Moreover, areas of the spectrogram that should be empty of any sound contain hushed overtones (possibly the result of noise and other imperfections).

##### Triangle {.tab}

![Spectrogram of the Triangle channel.](spectrograms/eb0_triangle_nes.png){.tab-float}

A triangle wave is also made of odd harmonics but with a faster decrease of their amplitude (where `amplitude = 1 ÷ harmonic number²` [@audio-triangle]).

However, this is not what's shown here, the step ladder-shaped triangle that the APU produces leads to extra harmonics and increased amplitudes.

##### Noise {.tab}

![Spectrogram of the Noise channel.](spectrograms/eb0_noise_nes.png){.tab-float}

Naturally, noise doesn't abide by the rules of harmonics and may randomly fill the whole frequency space, hence the lack of an easily recognisable pitch.

Although, by following the timeline you can differentiate the different noise presets the APU provides, each exhibiting a distinct set of overtones.

##### Sample {.tab}

![Spectrogram of the Sample channel.](spectrograms/eb0_dcm_nes.png){.tab-float}

Unlike the previous channels, the sample channel only plays back whatever low-resolution recording the developer feeds to the APU. Considering the example played a drum kit, I can't see any identifiable treats on the spectrogram (apart from similarities to white noise).

##### Sawtooth {.tab}

![Spectrogram of the VRC6's Sawtooth channel.](spectrograms/castlevania_saw_nes.png){.tab-float}

As a bonus, let's also check out the Sawtooth channel from the VRC6 expansion. To begin with, a perfect Sawtooth wave is made of all the harmonics and each exhibits decreasing amplitudes (where `amplitude = 1 ÷ harmonic number` [@audio-sawtooth]).

This is quite a requirement for digital equipment and it's naturally unaffordable for a game cartridge (it may not even need such perfection!). So, similarly to the APU's triangle waves, the VRC6 sequences Sawtooth waves in 7 cycles (and thus produces similar step ladder effects).

Consequently, the respective spectrogram is very messy, the VRC6's approximation techniques fill the wave with extra harmonics in many places.

#### Conclusion {.tabs-close}

Well, it seems that the NES' synthetic waveforms are nowhere near shaped as the theory dictates. Does this mean the APU is flawed? No! The way the APU was designed ended up granting this console unique and identifiable sounds - and these properties, whether intentional or not, made the spectrograms display unusual results.

As a side note, perfect geometry may be pleasant to look at with our eyes, but curiously enough, our ears are not particularly fond of waveforms with perfect edges! (you may start hearing popping noises).

Looking forward, sound analysis using spectrograms will come in handy in other articles, whether it's for a simple analysis or for making comparisons with other systems. Please note that these graphs are not *the mother lode* tool by all means, especially with sound samples that have been mixed up with too many channels/instruments (greatly difficulting its decomposition). But I think they will provide a solid start for any kind of objective study.

## Games

NES games are mainly written in the 6502 assembly language and reside in the **Program ROM**, while the game's graphics (tiles) are stored in **Character memory**.

Furthermore, games were sold (or rented out) at retail stores under the approval of Nintendo.

### The alternative medium

Even though it was only released in Japan, I thought this would be a good opportunity to introduce a short-lived but peculiar add-on that, just like the mappers, brought in more capabilities to this console. This peripheral was called **Famicom Disk System** (FDS) and shipped in 1986 (~3 years after the Famicom). It had the shape of an external floppy reader and came bundled with an odd-shaped cartridge called 'RAM adapter'.

::: {.subfigures .side-by-side}

![The drive [@photography-amos], where floppies are inserted (the photo shows a cardboard floppy inserted for protection). It either runs on six C batteries (1.5 V each) or a 3.6 W AC adapter.](fds/drive.png){.toleft .no-borders}

![The RAM adapter [@photography-amos], fitted on the cartridge slot of the Famicom and connected to the drive though a cable.](fds/ram.png){.toright .no-borders}

The two components that make up the Famicom Disk System (FDS).

:::

The Famicom Disk System added the following services to the Famicom:

- A new medium of distribution for games called **Famicom Disk** [@games-fds]. Based on the 'Quick Disk' by Mitsumi, it provides **~64 KB of data** per side and it's re-writable.
- An **extra audio channel** that uses [wavetable synthesis](game-boy#tab-7-3-wave) [@games-fds_audio].

![FDS equipment mounted onto the Famicom [@photography-amos].](fds/mounted.png){.open-float .no-borders}

Since the floppy is a single medium (as opposed to the multi-chip cartridges), all game data needs to be squashed inside, though it's kept organised with the use of a proprietary **file system**.

Nonetheless, the Famicom/NES strictly requires segregated Program and Character memory to function, so that's the job of the 'RAM adapter' to sort out. This component houses **32 KB of Program RAM** and **8 KB of Character RAM** to buffer the game data read from the floppy disc, and in doing so, it enables the console to read from it as if it were a cartridge-based game.

{.close-float}

To operate the drive, the RAM adapter embeds an additional 8 KB ROM to store a **BIOS** [@games-fds_bios]. This program performs the following tasks:

- Loads a splash animation.
- Bootstraps the game from the floppy. Behind the scenes, the BIOS contains routines to copy the contents of the floppy to the respective memory chip, so the console can read it.
- Provides I/O APIs for games to use, such as traversing the disks' file system.

![Example of two retail games for the FDS. The blue 'flavour' of the disc was also dust proof.](fds/floppies.jpg){.toleft}

![The FDS splash animation, waiting for the user to... uhm... insert a game.](fds_bios){.toright video="true"}

During that era, Nintendo deployed some 'kiosks' at retail stores so users could bring their floppies and overwrite them with a new game at a reduced price.

Unfortunately, after a few years of lifespan, the Famicom Disk System was discontinued and subsequent games returned to the cartridge medium. On the bright side, there were new mappers available with similar (or better) capabilities compared to the FDS' functions.

## Anti-piracy and region lock

Nintendo was able to block unauthorised publishing thanks to the inclusion of a proprietary **lockout** chip called the **Checking Integrated Circuit** (CIC) [@anti_piracy-cic]. It is located in the console and is connected to the reset signal (and thus not easily removed).

This chip runs **10NES**, an internal program that checks for the existence of another lockout chip in the game cartridge. If that check fails then the console is sent into an infinite reset.

Both lockout chips are in constant communication during the console's uptime. This system can be defeated by cutting one of the pins on the console's lockout, which leaves the chip in an idle state. Alternatively, sending it a -5V signal can freeze it.

The CIC exists as a result of the fear caused by the video game crash of 1983. Nintendo's then-president Hiroshi Yamauchi decided that in order to enforce good quality games, they would be in charge of approving every single one of them [@anti_piracy-vindicator].

You'll notice that the Japanese model of the console, the Famicom, was released before 1983's crash. That's why neither game cartridges nor the console includes CIC circuitry [@anti_piracy-nocic], instead, the pins are used for optional sound expansion.

## That's all folks
