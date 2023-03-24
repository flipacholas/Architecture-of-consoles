---
short_title: Super Nintendo Architecture
long_title: Architecture of the Super Nintendo
name: Super Nintendo
subtitle: Old hardware with mind-blowing features
date: 2019-06-28
release_date: 1990-11-21
generation: 4
cover: snes
published: true
top_tabs:
  Models:
    - title: "International"
      file: international
      caption: "The Super Nintendo (in Europe) or Super Famicom (in Japan).<br>Released on 21/11/1990 in Japan and 11/04/1992 in Europe."
      active: true
    - title: "American"
      file: american
      caption: "The Super Nintendo.<br>Released on 13/08/1991 in America."
  Motherboard:
    caption: "Showing revision 'SNS-RGB-CPU-01'.<br>Earlier revisions had the Sound Subsystem connected as a daughterboard, later ones unified both PPUs."
    bib_source: photography-yaca
    extension: "jpg"
  Diagram: 
    caption: "Bus 'A' and 'B' are address buses, the data bus follows the trail of bus 'B' and it's 8 bits wide."

# Historical
aliases:
  - /projects/consoles/super-nintendo/
  - /writings/consoles/snes/
---

## A quick introduction

It seems Nintendo managed to bring the next generation of graphics and sounds without using expensive off-the-shelf components. But there's a catch: the new console was also **designed with expandability in mind**. In a world where CPUs are evolving faster than the speed of light, Nintendo ultimately depended on game cartridges to make its console shine.

```{r results="asis"}
supporting_imagery()
```

## CPU

The Super Nintendo's choice of processor is a peculiar one. Unlike [its competition](mega-drive-genesis#cpu) bundling a fully-fledged 68000, the SNES' chip is not a radical break from its predecessor. To recap, the NES employed a [modified 6502](nes#cpu) CPU, an admired ingredient of late-70s and early-80s computers. Now, to pave way for the new decade (the 90s), Nintendo went for a more conservative (and cheaper) solution: the **65C816**, a 16-bit extension of the 6502.

### Modernising the 6502

The 65C816 CPU originates at Western Design Center, particularly from Bill Mensch, a former member of the 6502 team at MOS and, before that, part of the 6800 team at Motorola. In 1978, one year after leaving MOS, Mensch founded Western Design Center (WDC), a semiconductor company that ships clones of the MOS 6502 with attractive enhancements (i.e CMOS design, extra opcodes, circuitry fixes, new addressing modes, etc).

One day, WDC got approached by Apple to design a backwards-compatible variant of the 6502 that could process larger amounts of data. This became the 65C816 which, after some setbacks during development, Apple finally shipped with a new machine called Apple IIGS.

Meanwhile, Nintendo was enjoying a good relationship with Ricoh and their set of bespoken chips for the NES. I haven't found the exact document outlining what connected Ricoh with WDC, but what I can confirm is that at some point in time, WDC agreed to license their 65C816 designs to Ricoh [@cpu-interview]. Consequently, the latter tailored it to the new requirements of the Super Nintendo and became the **Ricoh 5A22**, exclusively supplied to Nintendo.

### The new CPU

As you've seen before, the main processor of this console is the **Ricoh 5A22**, a 16-bit CPU.

Unlike the Apple IIGS, which enjoyed backward compatibility with Apple II software, the Super Nintendo is **not compatible with NES games** (although, by looking at the choice of processor, there's a slight possibility that the SNES was originally planned to be compatible with NES games, who knows).

Moving on, the CPU employs a **variable clock speed** that will reach up to **3.58 MHz** during register operations and down to **1.79 MHz** when accessing the slowest buses (i.e. the serial/controller port).

In summary, the 5A22 features:

- The **65816 ISA**: The debuting 16-bit instruction of the 65C816. It's based on the 6502 ISA but doesn't implement undocumented instructions some NES games resorted to [@cpu-unoffopcodes].
  - The size of instructions can vary between 1 byte (8 bits) and 4 bytes (32 bits) depending on how memory addresses are referenced (a.k.a the 'addressing mode' used) [@cpu-isaref].
  - The [broken BCD mode](nes#scrapped-functions) is **working** again (I'm guessing as a consequence of _appropriate_ licensing).
- **10 different modes of operation**: Due to the combination of a backward compatibility mode, the return of the BCD mode (missing on the NES) and the ability to switch between groups of 16-bit and 8-bit registers [@cpu-opcodes], developers can utilise this CPU using different combinations of these.
  - Unlike later [MIPS CPUs](nintendo-64#cpu), there isn't a mixed instruction set with dedicated opcodes for 8-bit and 16-bit words. Instead, the same instruction set will be interpreted differently based on the mode activated.
  - For compatibility reasons, the CPU always starts in 'emulation' mode (pure 6502) and it's up to the program to switch to a particular 'native' mode to enable 16-bit functionality. On a side note, it's amusing how Intel's x86 still applies the [same _modus operandi_](xbox#boot-process) on their modern CPUs.
- **16-bit registers**. Also and due to the different modes of operation, the accumulator (where arithmetic operations are performed) and index register (used to compute memory addresses) can switch between 16-bit and 8-bit modes.
- **16-bit internal data bus** and an **8-bit external data bus**: Meaning that there're performance penalties for moving values larger than 8 bits across memory (the CPU spends additional cycles), especially considering most instructions are 16-bit long. Although, the overall cost is relative since Ricoh's variant includes DMA units (explained later on) and the CPU clock will vary depending on the operation.
- **24-bit address space**: Allowing the CPU to access **up to 16 MB worth of memory**. Just like the [Motorola 68000](mega-drive-genesis#cpu), except that the 65C816 gets its 24-bit addresses by combining extra 8-bit registers (`DBR` and `PBR`) with the original 16-bit addressing lines of the 6502 [@cpu-chibi]. Overall, this methodology is similar to using an [internal mapper](pc-engine#memory-access).

### Ricoh's additions

The 65C816 was an acceptable general-purpose CPU for its decade (the 80s). Yet, Nintendo planned its console to last throughout the 90s. So, Ricoh had to step up its game, if you pardon the pun. 

As a consequence, the first apparent upgrade was the addition of **16-bit multiplication** and **division units**, which provide the CPU with the ability to carry out these types of operations by hardware (the 65C816 doesn't include any dedicated instructions for this type of arithmetic).

#### Speedy memory access

The second addition were **two exclusive DMAs** (Direct Memory Access) that enable to move data around without the intervention of the CPU (resulting in faster speeds). For this design to work, regions of memory are referenced using two different address buses [@cpu-manual]:

- 24-bit **'A Bus'** controlled by the CPU: Connects the cartridge, CPU and WRAM.
- 8-bit **'B Bus'** controlled by the S-PPU: Connects the cartridge, CPU, WRAM, S-PPU and the Audio CPU.

When a DMA is being set up, the *origin* must come from a different bus than the *destination*.

Furthermore, the two DMAs are not identical and provide very distinct functionality [@graphics-piepgrass]:

- **General Purpose DMA** performs transfers at any time. The CPU is stopped until the transfer is finished.
- **Horizontal DMA** (HDMA) performs a small transfer after each horizontal scan (while the CRT beam is preparing to draw the next row). This avoids interrupting the CPU for long intervals but transfers are limited to 4 bytes per scanline.

Finally, the system provides **eight channels** to set up DMA transfers, thus enabling to dispatch up to eight independent transfers at once.

#### Segmentation Fault

This console also features a special 'anomaly' called **Open Bus**: If there is an instruction trying to read from an unmapped/invalid address, the last value read is supplied instead (the CPU stores this value in a register called **Memory Data Register** or `MDR`) and execution carries on in an unpredictable state.

For comparison, the 68000 uses a vector table to handle exceptions, so execution will be redirected whenever a fault is detected.

## Graphics

After everything said so far, let me tell you that the graphic subsystem of this console is a true work of engineering. With such a constrained CPU, one would imagine the SNES could never cast a shadow on its competitor featuring a '32-bit' Motorola 68000. Yet, Nintendo and Ricoh engineers managed to find clever tricks that take advantage of the way CRT displays behave, thereby expanding the capabilities of this console without requiring expensive state-of-art components.

In any case, before we go in-depth I strongly recommend reading the [NES article](nes#graphics) first since it introduces useful concepts that will be revisited here.

### Design

As with any other console of its generation, the Super Nintendo draws graphics using 2D tiles (8 x 8 pixels). The NES originally accomplished this by bundling the signature 'Picture Processing Unit' (PPU) that beams the image in pace with a CRT screen. The Super Nintendo follows suit but it now implements more sophisticated techniques to obtain richer results.

#### The chipset

The Super Nintendo houses two different PPU chips that constitute the graphics subsystem, which combined are known as **Super PPU** or 'S-PPU'. Both PPU packages are designed to serve different functionality [@cpu-manual]:

- **PPU 1**: Renders graphics (tiles) and applies transformations on them (rotation and scaling).
- **PPU 2**: Provides effects such as *window*, *mosaic* and *fades* over the rendered graphics.

This separation, from the programming point of view, is redundant since both chips are virtually treated as one.

#### Display modalities

The system outputs a standard resolution of **256 x 224 pixels** at **~60 Hz** (NTSC) [@graphics-guide]. The European PAL variant outputs **256 × 240 pixels** at **~50 Hz** instead (to abide by the PAL specification). Be as it may, most games don't use the extra pixels and show a *letterbox* (black lines) instead.

Now, here's the tricky part, traditional TVs have an aspect ratio of 4:3. Yet, if you do the math, the Super Nintendo's output resolution has an **aspect ratio of 8:7**. Consequently, after beaming the picture on the TV, it will look **horizontally stretched**, as if it were a **292 x 224 pixels** frame instead (in the case of the NTSC variant) [@graphics-aspect]. To put it in another way, you could say pixels on the Super Nintendo have an aspect ratio of 8:7 instead of being 'perfectly squared'.

::: {.subfigures .side-by-side .pixel}

![Rendered frame with a resolution of 256 x 224 pixels. This is what the console sends to the TV.](stretch/internal.png){.toleft .snesratiosmaller}

![Stretched frame as seen from the TV (with an apparent resolution of 292 x 224 pixels).](stretch/external.png){.toright .snesratiobigger}

Kirby's Dream Land 3 (1997).

:::

Some games like 'The Legend of Zelda: A Link to the Past' account for this factor so shapes are explicitly squashed by design, which looks correct after being stretched by the TV. This, however, is an exceptional case since the majority of games take no extra measure to account for this factor.

### Organising the content

![Memory architecture of the S-PPU.](SPPU_architecture.png){.no-borders}

Due to costs and performance reasons, graphics data is distributed across three regions of memory:

- 64 KB **VRAM** (Video RAM): Stores tiles and maps (tables) used to build background layers.
- 512 B **CGRAM** (Colour Graphics RAM): Fits 512 colour palette entries, each entry has the size of a *word* (16 bits).
- 544 B **OAM** (Object Attribute Memory): Contains tables with references of 128 tiles that will be used as *Sprites* along with their attributes.

### Constructing the frame

Let's now see how a frame is rendered on the console and subsequently displayed on TV. For demonstration purposes, *Super Mario World* will be used as an example.

#### Tiles {.tabs .active}

![Some 16x16 Tiles found in VRAM.](sppu_mario/tiles.png){.tab-float .pixel}

Just like its predecessor, the S-PPU uses tiles to build sophisticated graphics. Although, there are significant improvements compared to the original PPU:

- Game **cartridges are no longer wired to the PPU** so tiles will have to be copied to VRAM first (just like Sega's Mega Drive). Here is where the DMA unit comes in very handy.
- Tiles are no longer restricted to their traditional dimension (8x8 pixels), from now on they can also be up to **16x16 pixels** wide.
- When tiles are stored in memory, these will be compressed depending on how many colours per pixel they need to use. The unit of size is **bpp** (bits per pixel). The minimum value is **2bpp** (where each pixel only occupies two bits in memory and has only 4 colours available) while the maximum is **8bpp**, which encodes up to 256 colours (at the expense of consuming a whole byte).

#### Background {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Background Layer 1 (BG1).](sppu_mario/background1_map.png){.active title="Layer 1"}

![Background Layer 2 (BG2).](sppu_mario/background2_map.png){title="Layer 2"}

![Background Layer 3 (BG3).](sppu_mario/background3_map.png){title="Layer 3"}

Background maps in VRAM.

:::

::: {.subfigures .tabs-nested .tab-float .pixel}

![Rendered Background Layer 1 (BG1).](sppu_mario/background1.png){.active title="Layer 1"}

![Rendered Background Layer 2 (BG2).](sppu_mario/background2.png){title="Layer 2"}

![Rendered Background Layer 3 (BG3).](sppu_mario/background3.png){title="Layer 3"}

![Rendered Background Layers combined.](sppu_mario/background_complete.png){title="Combined"}

Rendered Background layers after selection and transparency are applied.

:::

The Super Nintendo can generate up to four different background planes. Using either 8x8 or 16x16 tiles, blocks are made of 32x32 pixels (2x2 tiles). That being said, the size of each background layer can be up to 1024x1024 pixels wide (32x32 tiles). The region in VRAM where these layers are configured is called **Tilemap** and is structured as a table (continuous values in memory).

Each entry in the Tilemap contains the following attributes:

- Vertical & Horizontal Flip values.
- Priority (either `0` or `1`).
- Palette reference from CRAM.
- Tile reference.

As always, these planes are scrollable, though the number of features available (colour, number of layers, independent scrolling region and size of selection) will depend on the **Background mode** activated on the S-PPU, which brings us to the next section...

#### Modes {.tab}

The S-PPU provides many operations for backgrounds, but these can't be chosen arbitrarily. Instead, there are eight background modes to choose from, each one providing a different set of features [@cpu-rgme]:

- **Mode 0**: 4 layers with 4 colours each.
  - The colour palette is particularly bland since this mode is the only one allowing the largest number of layers.
- **Mode 1**: 2 layers with 16 colours each + 1 layer with 4 colours.
  - One layer can be split into foreground and background.
  - This is the most common mode used.
- **Mode 2**: 2 layers with 16 colours each.
  - This mode has an extra effect: Layers can have each of their columns scrolled independently (similarly to [GameBoy](game-boy#graphics) effects but vertically scrolled).
- **Mode 3**: 1 Background layer with 128 colours + 1 Background with 16 colours.
  - Colours can be set as RGB values instead of using CGRAM references.
- **Mode 4**: Mode 2 and 3 combined (Column scroll + RGB colour mapping).
  - The first layer has 128 colours and the second one only has 4.
- **Mode 5**: 1 layer with 16 colours + 1 layer with 4 colours.
  - The selected area will have an outstanding resolution of **512 x 224 pixels** which will be squashed horizontally to fit on the screen (the output frame is still 256 x 224 pixels!). This comes at the cost of rendering 16 x 8 pixels tiles as 8 x 8 ones, and 16 x 16 pixels tiles as 8 x 16 ones.
  - Furthermore, the vertical resolution can be extended as well by activating **interlacing** (reaching **512 x 448 pixels**, which is now proportional to the output dimension). In exchange, the same squashing effect will affect tiles but now vertically too. This is useful for cases when the screen has to display extra amounts of information (i.e. during multiplayer/split-screen).
- **Mode 6**: Combination of Mode 2 and 5 (high resolution + column scrolling), but only one layer with 32 colours is allowed.

As you can see, programmers now have the choice of prioritising either the number of colours, layers, effects or resolution of the selected area.

#### Sprites {.tab}

![Rendered Sprite layer.](sppu_mario/sprites.png){.tab-float .pixel}

An area on memory called **Object Attribute Memory** (OAM) stores a table with references of up to 128 sprites with these properties [@graphics-guidelines]:

- **Size**: The PPU can combine up to 16 small tiles in the form of a 4x4 tiles quadrant to build a sprite.
- **Tile References**: The value points to the tiles used to draw the sprite.
- **Screen Position**. Only sprites positioned inside the visible area will be rendered.
- **Priority**: Since multiple layers overlap each other, the graphic with the highest priority will be shown, this is also determined by the background mode in use.
- **Colour palette slot**, allowing 9 slots to choose from CGRAM.
- **X/Y Flip**.

The S-PPU can draw up to 32 sprites per scanline, overflowing this will only make the S-PPU discard the ones with the lowest priority.

#### Result {.tab}

![Tada!](sppu_mario/complete.png){.tab-float .pixel}

The S-PPU draws each scanline on-the-fly by first processing the respective portion of each layer and then mixing them together.

One of the main constraints of NES games was the fact that they could only update their graphics during **V-Blank**. The moment from which the CRT's beam was returning to the starting point provided a reasonable timeframe to reshuffle some tiles without breaking the image.

Well, now thanks to the new capabilities of the SNES, this limitation gained a different meaning.

You see, because the new DMA/HDMA units now enable programmers to perform memory transfers without waiting for V-Scan [@cpu-dma], games can now update tiles, colours and registers without waiting for the whole frame to be drawn. In fact, we can go beyond that: Since games can now **change the S-PPU settings during mid-frame**, this means that it's possible to **activate different background modes at different stages of the same frame**, opening the door to new and original game designs!

### _That_ feature {.tabs-close}

Truth to be told, I still haven't mentioned the most important characteristic of this console...

::: {.subfigures .tabs-nested .tab-float .open-float .pixel}

![Rendered Background layer.](mode7/layer.png){.active title="Background"}

![Allocated Background map.](mode7/map.png){title="Map"}

![Rendered frame on the screen.<br>The first quarter of scan-lines uses another mode to simulate distance, Mode 7 starts at the second quarter (this is possible thanks to HDMA).](mode7/displayed.png){title="Displayed"}

F-Zero (1990).

:::

Introducing **Mode 7**, *yet another* background mode, but this time, with a completely different way of working. While it can only render a single 8 bpp background layer, it provides the exclusive ability to apply the following **affine transformations** on that plane [@cpu-rgme]:

- Translation.
- Scaling.
- Rotation.
- Reflection.
- Shearing.

The S-PPU is controlled with a **rotation matrix** to alter the parameters of this mode. I won't go into the linear algebra here, but depending on the desired effect, the CPU will have to perform some trigonometric functions (sine and cosine) to fill the entries of this table accordingly. This is really expensive for the 65C816, even with the use of fixed-point precision. For this reason, with the 5A22, Ricoh added multiplication and division registers to offload some cycles.

`r close_float_group(with_markdown = TRUE)`

By the way, notice that the list of transformations doesn't mention **perspective**, which is what you see in the example game (F-Zero). This is achieved by altering the rotation matrix at each HDMA call, creating a pseudo-3D effect in the process. That should give you an idea about how practical the S-PPU is!

Finally, due to the high number of calculations needed, the memory map is changed to optimise the pipeline of the two PPUs, the first one processes the **Tilemap** (where tiles are referenced) while the other fetches the **Tileset** (where tiles are stored).

### Cause of frame drops

One another topic, have you ever wondered what causes games to lag? When the V-Blank interrupt is called to allow graphics update, sometimes the game is still executing some heavy code and skips the V-Blank window, graphics can't be updated until the next V-Blank call and since the frame wasn't updated, this is manifested as a drop in frame-rate.

This can also happen the other way around, extensive processing during a V-Blank won't allow the PPU to output the video signal (as the bus is blocked). Thus, black lines during a scan will be shown, although this is barely noticeable since the frames update 50 or 60 times per second.

### A convenient video out

All of the aforementioned advancements will be futile unless the console sends the picture to the TV through a medium both can understand. With the Super Nintendo, the company debuted some sort of *universal-but-proprietary* connection called **Multi Out** which can transport many types of signals at the same time, including **Composite**, **S-Video** and **RGB** [@graphics-pinouts].

Along with the console, Nintendo bundled a 'Multi Out to composite' cable since that was pretty much the common denominator of TVs back then. In Europe however, the **SCART** port was also very popular as many set-top boxes and VCRs relied on it. A great thing about SCART is that it can also carry many types of signals, this enables AV equipment to use the most optimal signal type without encountering compatibility issues. Unfortunately, Nintendo never shipped an official SCART cable that took advantage of the RGB pins exposed in the Super Nintendo.

Nonetheless, Nintendo altered the pinout of its PAL consoles to comply with the SCART protocol, and in doing so it replaced the 'composite sync' pin with a 12 Volts one (which tells the TV to set the 4:3 aspect ratio). So, even though Multi out is 'universal', the resulting RGB cables, if any, are region-specific.

I think the real benefits of Multi Out started to become evident during present times, as it allowed users to take advantage of the RGB signal with their state-of-the-art tellies without tampering with the internals of this console. Although, unlike composite and S-Video, RGB requires an extra 'sync' signal. For this, the cable can be wired up to capture the sync signal from composite or S-Video; or for best results, use a dedicated sync line called 'composite sync'. But, as mentioned in the previous paragraph, only NTSC consoles carried the latter.

## Audio

Just like graphical abilities, the audio department of this console has gone through a significant revamp. I would say it even provides the most advanced synthesising techniques of its generation.

### Architecture

Some companies [partnered with Yamaha](mega-drive-genesis#audio), others devised an [in-house solution](pc-engine#audio). Well, Nintendo partnered with **Sony** (the electronics conglomerate and authors of the _Walkman_) so they provide them with a sophisticated synthesiser. And so, Sony gave them two processors: a DSP that can sequence and mix samples; and a CPU that drives that DSP. 

Hence, the resulting audio subsystem of this console is composed of:

- **The S-DSP**: Plays ADPCM samples across eight different channels, they are mixed and sent through the audio output. The DSP is capable of manipulating samples with 16-bit resolution and a sampling rate of 32 kHz, it also provides:
  - **Stereo Panning**: Distributes our channels to provide stereo sound.
  - **ADSR envelope control**: Sets how the volume changes at different times.
  - **Delay**: Simulates echo effects. It also includes a frequency filter to cut out some frequencies during the feedback. Don't confuse this with *Reverb*!
  - **Noise generator**: Generates pseudo-random waveforms that sound like white static.
  - **Pitch modulation**: Allows some channels to distort others. Similar to FM synthesis (used by [its competitor](mega-drive-genesis#audio)).
- **The SPC700 CPU**: Also named 'S-SMP', it's an independent 8-bit CPU that communicates with the DSP and receives commands from the main CPU [@audio-smp].
- **64 KB of PSRAM**: Stores audio data and programs. The main CPU is responsible for filling this up.
  - If 'Delay' is activated, some space will be allocated for feedback data (this is actually very dangerous, since if not used carefully, it can override existing data!).

This block functions in parallel with the main CPU. When the console is turned on, the SPC700 boots a 64-byte internal ROM that sets it up to receive commands from the main CPU [@audio-spc]. After that, it stays idle.

::: {.subfigures .tabs-nested .tab-float .open-float}

![Channels used for melody.](melody){.active video="true" title="Melody"}

![Drums are discriminated for demonstration purposes.](drums){video="true" title="Drums"}

![All audio channels.](complete){video="true" title="Complete"}

StarFox (1993).

:::

For the S-SMP to start doing useful work, it needs to load a type of program referred to as **Sound Driver**. The latter instructs the chip on how to manipulate the raw audio data that the main CPU sends to PSRAM and also how to command the S-DSP.

As you can see, the sound subsystem was a huge advancement compared to the previous generation, but it was challenging to program as well. The documentation that Nintendo provided was notable for including unintelligible explanations and skipping important features altogether, so it was up to the programmers to carry out their own research.

As a consequence, there were tons of different sound drivers found on the market [@audio-drivers], and some of them ended up uncovering impressive features.

`r close_float_group(with_markdown = TRUE)`

### Pitch control

Pitch bending enables to produce distinct notes using the same sample. Well, the S-SMP included a useful bender to alter the pitch in a smooth manner. Take a look at this extracted channel from Mother 2/Earthbound, both examples come from the original soundtrack, however, the first one has the pitch control disabled.

![No pitch bend.](pitch/no_pitch){.toleft video="true"}

![With pitch bend enabled.](pitch/pitch){.toright video="true"}

### Evolution from the NES

In order to demonstrate the evolution of sounds from the NES to the Super NES, here are two music scores, one from an NES game and another from its Super NES sequel. Both used the same composition:

![Mother (1989).](snowman_nes){.toleft video="true"}

![Mother 2/Earthbound (1994).](snowman_snes){.toright video="true"}

### Advanced usage

::: {.subfigures .tabs-nested .tab-float .open-float}

![Channels used for melody.](kirby/trebble){.active video="true" title="Melody"}

![Drums are discriminated for demonstration purposes.](kirby/drums){video="true" title="Drums"}

![All audio channels.](kirby/complete){video="true" title="Complete"}

Kirby's Dream Land 3 (1997).

:::

Here's a more instrument-rich composition that takes great advantage of pitch bending, echo and envelope.

This combination of techniques allowed the music to only require five channels in total, leaving the other three for sound effects.

`r close_float_group(with_markdown = TRUE)`

### Stereo confusion

The DSP's volume controls are organised in chunks of signed 8 bits values [@audio-gst], this means that the volume can be set up with **negative values**. *But hang on*, if '0' means mute, what would a number like '-1' do? Well, it will **invert the signal**.

This is particularly used for creating a special **surrounding effect**, which is accomplished by setting the stereo channels to output **out of phase** (one channel outputs the normal signal and the other outputs the same signal but inverted).

Unfortunately, abusing this feature results in very unpleasant results (e.g. the feeling that the music is coming from inside your head), so you will notice that most SNES emulators just skip this setting altogether.

Additionally, out-of-phase stereo gets cancelled out on mono devices, so those games had to include a stereo/mono selector to avoid muting their own soundtrack.

## Games

To be blunt, the main program is written in plain **65816 assembly** and the music driver is written in **SPC700 assembly**. I'm afraid you won't find any of the commodities available on 21st-century-equipment.

There were, however, some tools distributed by Nintendo, Intelligent Systems and Ricoh to make life easier for programmers, these included [@games-sdk]:

- Different types of **development units** with the capacity of being controlled by a **debugger** from a host machine (typically running MS-DOS).
- **Flashable cartridges** (a.k.a _Flashcarts_). Not for _piracy_ but to enable developers to try out their code on retail units.
- 65816 and SPC700 **assemblers**.
- **Development Manuals** which explain from a low-level perspective how this console works. These may also include guidelines and norms developers must abide by to get their game approved by Nintendo (required for distribution).

Curiously enough, multiple game studios like Argonaut Software, Accolade, SN Systems and so forth also developed their own in-house equipment that provided more capabilities than the official offerings (i.e. memory editor, floppy disk reader, devkit as an ISA daughterboard, etc) [@games-devkit].

### Cartridge configuration

When it comes to accessing the cartridge, things get a lot more confusing compared to the relatively simpler mapping model of the NES. I find it interesting nonetheless, especially for understanding how it could be expanded.

The 65C816 uses a 24-bit address bus, which allows it to access up to 16 MB worth of data. However and due to the way this console is designed, part of the address space is consumed by [memory-mapped components](nes#memory). Moreover, 65C816 only comes with 16 address lines, which are then combined with an internal register to form a 24-bit address. This is analogous to housing an [internal mapper](pc-engine#memory-access) and requiring [bank switching](nes#cartridgegame-data) to access extra data beyond the boundaries of the address bus. If you read other articles of the same generation, you'll find this methodology somewhat familiar.

![Example cartridge boards designed with different configurations [@photography-amos].<br>From top to bottom: LoROM (dual ROM with battery-backed SRAM), LoROM (single ROM with battery-backed SRAM) and LoROM (single ROM)](cartridges.png){.no-borders}

That being said, when it comes to designing the cartridge, there are many ways of electrically connecting the address pins between the ROM and the CPU. Each takes advantage of bank switching in a different way. There are two fundamental models that enables accessing up to **4 MB of ROM** and **64 KB of SRAM** [@games-memory], and they work like this:

- With the **LoROM Model**, ROM Data is available in 32 KB chunks with 128 banks to choose from [@games-bazzinotti]. SRAM, on the other side, fits in two banks, but it's been made accessible across 15 banks where ROM data can also be found.
  - This will mean the game/program may need to perform significant bank switching during execution. On the other side, half of the banks are mapped to part of WRAM as well (meaning ROM, SRAM and WRAM can be accessed without switching banks).
- With the **HiROM Model**, ROM space now occupies full banks, meaning data is available in 64 KB chunks with 64 banks to choose from. This requires less bank switching at the cost of not being able to read ROM, WRAM and SRAM within the same bank.

In both cases, a large part of the ROM space is mirrored across the leftover area of the CPU's address space, but here's the interesting part: one half of the space can be read at ~2.68 MHz while the other can reach **3.58 MHz**. This will only happen if the ROM chip supports the higher speed and the CPU's `FastROM` flag is enabled [@games-registers].

Something I haven't mentioned yet is that cartridges that bundle SRAM also need to house a **MAD-1** (or similar) chip, which performs address decoding. This is especially needed for properly latching the pins that select between ROM and SRAM chips [@games-pcb].

#### Beyond convention

Now, if programmers need more space, that's when derivative models of LoROM and HiROM come into place. For instance, two variations commonly referred to as **ExHiROM** and **ExLoROM** expand the ROM's addressing space by reducing the mirrored area. Both can access **~7.9 MB of ROM**.

![Star Fox (1993) uses the Super FX GSU chip to render 3D surfaces (the S-PPU only sees a background layer made of _erratic_ tiles).](fox.png){.open-float .pixel}

Alternatively and most importantly, LoRom and HiROM can also be adapted to house **enhancement chips** in the cartridge. These are additional processors that expand the capabilities of the console. To name a few examples of new configurations:

- The **MMC** model enables bank switching by the local enhancement chip. This was used by components like the **S-DD1** or the **SPC7110**, both hardware tile decompressors.
- The **Super Accelerator System** model was designed for the much-acclaimed **SA-1**, a secondary **65C816-based CPU** that operates at **10.74 MHz** and bundles extra functionality. It builds upon the MMC model with extra circuitry for the additional memory-mapped components.
- Two extra configurations, one based on LoROM and the other on HiROM, make way for the **DSP** series of chips. These are the same derivative of the NEC µPD77C25 CPU, but running a different program. They provide vector and matrix computations [@games-dsp] and different games have used it to compute affine transformations, graphics decompressing and shortest path calculations.
- The **SFX** model targets the popular **Super FX GSU** chip. This configuration maps up to 8 MB of ROM, where 2 MB are shared between the main CPU and the Super FX. The rest of the address space also includes additional backup RAM and SRAM. The Super FX is a proprietary processor made by Argonaut that specialises in **3D surface rendering** and **2D affine transformations**, the results are then streamed as tiles for the S-PPU to be able to display. A handful of notable games used it for drawing 3D models and/or extending Mode 7 for sprites (as Mode 7 can only transform backgrounds).

`r close_float_group()`

It's difficult to ignore the impact this engineering made on games during the 90s, many of which managed to exceed the expectations of this console without requiring expansion modules and whatnot. You could say this difficulted Nintendo's plans to deliver a successor of this console (which may explain why advanced games like Starfox 2 were cancelled).

## Anti-piracy / Region Lock

With the Super Nintendo, Nintendo once again became the sole authority on game distribution. Thus, in an attempt to enforce the company's rules, engineers devised three different layers to protect cartridges against third-party distribution (and subsequently circumvention of royalties).

Firstly, the external shape of cartridges **is different between regions**, so they won't fit on consoles from different regions. Be as it may, anybody could easily go around this by using a third-party adapter.

Secondly, this console, like the NES, still incorporates the [**10NES** protection system](nes#anti-piracy-and-region-lock) to lock out non-authorised distributors. Be as it may, the CIC chip was eventually cloned.

As a final layer (and specially made to protect against bootlegging) games also included a chain of piracy checks like:

1. Comparing the SRAM size (bootlegs normally include large SRAM blocks to fit any kind of game).
2. A series of checksums on the code to check if the previous comparison was removed. These checks would be dispersed at different stages of the game, so they'd be difficult to find.

This could be nullified by manually removing these routines but, naturally, it would take significant time to find all of them. After all, they would be scattered around the game only to upset the player (and eventually make them buy a legitimate copy). Truth to be told, you'll notice that most ROMs surfing the internet had all their piracy checks removed.

## That's all folks

![My modded SNES with an American cartridge.<br>That game was only released in the states. Luckily, a lad was selling it in Glasgow!](mysnes.png)
