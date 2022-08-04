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
aliases: [/projects/consoles/super-nintendo/]
---

## A quick introduction

Nintendo managed to bring the next generation of graphics and sounds without using expensive off-the-shelf components. Consequently, the Super Nintendo was **designed with expandability in mind**: In a world where CPUs are evolving faster than the speed of light, Nintendo depended on game cartridges to make its console shine.

```{r results="asis"}
supporting_imagery()
```

## CPU

The main processor is a **Ricoh 5A22**. It's based on the **Western 65C816**, a 16-bit upgrade of the classic [MOS Technology 6502](`r ref("nes#cpu")`). Since the SNES shares the same foundation as the NES' CPU, there's a slight possibility that the SNES was originally planned to be compatible with NES games.

The CPU employs a **variable clock speed** that will reach up to **3.58 MHz** during register operations and down to **1.79 MHz** when accessing slow external buses (i.e. the serial/controller port).

In summary, the 5A22 features:

- A **65816 ISA**: A 16-bit instruction set which extends the original 6502 ISA, but doesn't implement undocumented instructions some NES games resorted to use `r cite("cpu-opcodes")`.
  - The [broken BCD mode](`r ref("nes#scrapped-functions")`) is **working** again.
- **16-bit registers**. Furthermore, the accumulator (where arithmetic operations are performed) and index register (used to compute memory addresses) can switch between 16-bit and 8-bit modes.
- New **16-bit multiplication** and **division units** added by Ricoh, which provide the CPU with the ability to carry out these types of operations by hardware (the 65C816 doesn't include any dedicated instructions for multiplication or division).
- **8-bit external data bus**: Meaning that it takes twice the cycles to move its 16-bit registers across external memory!

### Ricoh's additions

Apart from the extra registers, Ricoh customised the core design to include **two exclusive DMAs** (Direct Memory Access) that enable to move data around without the intervention of the CPU (resulting in faster speeds).

For this design to work, regions of memory are referenced using two different address buses `r cite("cpu-manual")`:

- 24-bit **'A Bus'** controlled by the CPU: Connects the cartridge, CPU and WRAM.
- 8-bit **'B Bus'** controlled by the S-PPU: Connects the cartridge, CPU, WRAM, S-PPU and the Audio CPU.

When a DMA is being set up, the *origin* must come from a different bus than the *destination*.

There are two DMAs to choose from depending on the needs `r cite("graphics-piepgrass")`:

- **General Purpose DMA**: Performs transfers at any time, bear in mind that the CPU is stopped until the transfer is finished.
- **Horizontal DMA** (HDMA): Performs a small transfer after each horizontal scan (while the CRT beam is preparing to draw the next row). This avoids interrupting the CPU for long intervals but transfers are limited to 4 bytes per scan-line.

The system provides eight *channels* to set up DMA transfers, thus enabling to dispatch up to eight independent transfers at once.

#### Segmentation Fault

This console also features a special 'anomaly' called **Open Bus**: If there is an instruction trying to read from an unmapped/invalid address, the last value read is supplied instead (the CPU stores this value in a register called **Memory Data Register** or `MDR`).

I guess this is Ricoh's way of applying the popular 'keep calm and carry on' philosophy whenever the program reaches the unexpected. After all, from the user's perspective, carrying on with the execution can be more favourable than a crash... at the expense of the game becoming unpredictable.

## Graphics

Before we go in-depth I strongly recommend reading the [NES article](`r ref("nes")`) first since it introduces useful concepts that will be revisited here.

### Design

Nintendo improved their previous architecture by using two different *PPU* chips to build the graphics sub-system, both combined are known as **Super PPU** or 'S-PPU'.

The system outputs a standard resolution of 256x224 `r cite("graphics-guide")`. PAL systems output 256×240, however, most games don't use the extra pixels and show a *letterbox* (black lines) instead.

Overall, both PPU packages are designed to serve different functionality `r cite("cpu-manual")`:

- **PPU 1**: Renders graphics (*tiles*) and applies transformations on them (rotation and scaling).
- **PPU 2**: Provides effects such as *window*, *mosaic* and *fades* over the rendered graphics.

This separation, from the programming point of view, is unnecessary since both chips are virtually treated as one.

### Organising the content

(ref:sppucaption) Memory architecture of the S-PPU.

```{r fig.cap="(ref:sppucaption)", fig.align='center', centered=TRUE}
image("SPPU_architecture.png", "(ref:sppucaption)", class = "centered-container")
```

Graphics data is distributed across three regions of memory:

- 64 KB **VRAM** (Video RAM): Stores tiles and maps (tables) used to build background layers.
- 512 B **CGRAM** (Colour Graphics RAM): Fits 512 colour palette entries, each entry has the size of a *word* (16 bits).
- 544 B **OAM** (Object Attribute Memory): Contains tables with references of 128 tiles that will be used as *Sprites* along with their attributes.

### Constructing the frame

For demonstration purposes, *Super Mario World* will be used to show how graphics are rendered.

(ref:tilestitle) Tiles

(ref:tilescaption) Some 16x16 Tiles found in VRAM.

```{r fig.cap="(ref:tilescaption)", fig.align='center', tab.title="(ref:tilestitle)", tab.first=TRUE, tab.active=TRUE}
image('sppu_mario/tiles.png', "(ref:tilescaption)", float=TRUE, class="pixel")
```

Just like its predecessor, the S-PPU uses tiles to build sophisticated graphics. Although, there are significant improvements compared to the original PPU:

- Game cartridges no longer connect directly with the PPU so tiles will have to be copied to VRAM first (just like Sega's Mega Drive). DMA comes in very handy for these occasions.
- Tiles are no longer restricted to their traditional dimension (8x8 pixels), from now on they can also be **16x16 pixels** wide.
- When tiles are stored in memory, these will be compressed depending on how many colours per pixel they need to use. The unit of size is *bpp* (bits per pixel), the minimum is **2bpp** (each pixel only occupies two bits in memory and has only 4 colours available) while the maximum is **8bpp**, which allows using 256 colours (at the expense of requiring a whole byte).

(ref:backgroundtitle) Background

(ref:backlayermapcaption) Background Layer

(ref:backlayerrendcaption) Rendered Background Layer

(ref:backlayertitle) Layer

(ref:backmapsfooter) Background maps in VRAM.

(ref:backcombinedtitle) Combined

(ref:backcombinedcaption) Rendered Background Layers combined.

(ref:backlayersfooter) Rendered Background layers after selection and transparency are applied.

```{r fig.cap=c("(ref:backmapsfooter)", "(ref:backlayersfooter)"), fig.align='center', tab.title="(ref:backgroundtitle)", out.width = standard_figure_width}

number_caption <- function(caption, number) {
  return(stringr::str_replace_all(caption, "-NMBR-", number))
}
  
background_map_tab <- function(number, src, active=FALSE) {
  return(image(src, caption=paste0("(ref:backlayermapcaption)", " (BG", number,")."), tab.name=paste0("(ref:backlayertitle) ", number), tab.active=active))
}

background_rend_tab <- function(number, src, active=FALSE) {
  return(image(src, caption=paste0("(ref:backlayerrendcaption)", " (BG", number,")."), tab.name=paste0("(ref:backlayertitle) ", number), tab.active=active))
}

tabs(nested=TRUE, class="pixel", float=TRUE, figure=TRUE, content=c(
  background_map_tab("1", 'sppu_mario/background1_map.png', active=TRUE), 
  background_map_tab("2", 'sppu_mario/background2_map.png'), 
  background_map_tab("3", 'sppu_mario/background3_map.png'),
  figcaption("(ref:backmapsfooter)"))
)

tabs(nested=TRUE, class="pixel", float=TRUE, figure=TRUE, content=c(
  background_rend_tab("1", 'sppu_mario/background1.png', active=TRUE),
  background_rend_tab("2", 'sppu_mario/background2.png'),
  background_rend_tab("3", 'sppu_mario/background3.png'),
  image('sppu_mario/background_complete.png', caption="(ref:backcombinedcaption)", tab.name = "(ref:backcombinedtitle)", caption.post = "(ref:backlayersfooter)"), 
  figcaption("(ref:backlayersfooter)"))
)
```

The Super Nintendo can generate up to four different background planes. Using either 8x8 or 16x16 tiles, blocks will take up to 32x32 pixels (2x2 tiles). That being said, the size of each background layer can be up to 1024x1024 pixels wide (32x32 tiles). The region in VRAM where these layers are configured is called **Tilemap** and is structured as a table (continuous values in memory).

Each entry in the Tilemap contains the following attributes:

- Vertical & Horizontal Flip values.
- Priority (either '0' or '1').
- Palette reference from CRAM.
- Tile reference.

As always, these planes are scrollable, though the number of features available (colour, number of layers, independent scrolling region and size of selection) will depend on the **Background mode** selected.

`r tab.simple("Modes")`

This system provides eight background modes to choose from, each one provides a different set of features `r cite("cpu-rgme")`:

- **Mode 0**: 4 layers with 4 colours each.
  - This is very limited in colours since it's the only one allowing the most number of layers.
- **Mode 1**: 2 layers with 16 colours each + 1 layer with 4 colours.
  - One layer can be split into foreground and background.
  - This is the most common one.
- **Mode 2**: 2 layers with 16 colours each.
  - This mode has an extra effect: Layers can have each of their columns scrolled independently (Similar to the [GameBoy](`r ref("game-boy#graphics")`)).
- **Mode 3**: 1 Background layer with 128 colours + 1 Background with 16 colours.
  - Colours can be set as RGB values instead of using CGRAM references.
- **Mode 4**: Mode 2 and 3 combined (Column scroll + RGB colour mapping).
  - The first layer has 128 colours and the second one only has 4.
- **Mode 5**: 1 layer with 16 colours + 1 layer with 4 colours.
  - The selected area has a resolution of 224 x 512 pixels which will be squashed to fit on the screen (interlacing).
  - This is very useful when the screen has to be split (during multiplayer, for instance).
- **Mode 6**: Combination of Mode 2 and 5 (High resolution + column scrolling).
  - Only one layer is allowed with 32 colours.

As you can see, programmers now have the choice to prioritize either the number of colours, layers, effects or resolution of the selected area.

(ref:spritestitle) Tiles

(ref:spritescaption) Rendered Sprite layer.

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)"}
image('sppu_mario/sprites.png', "(ref:spritescaption)", float=TRUE, class="pixel")
```

An area on memory called **Object Attribute Memory** (OAM) stores a table with references of up to 128 sprites with these properties `r cite("graphics-guidelines")`:

- **Size**: The PPU can combine up to 16 small Tiles in the form of 4x4 to build a sprite.
- **Tile References**.  
- **Screen Position**: Only sprites positioned inside the visible area will be rendered.
- **Priority**: Since multiple layers overlap each other, the graphic with the highest priority will be shown, this is also determined by the background mode in use.
- **Colour palette slot**: 9 slots to choose from CGRAM.
- **X/Y Flip**.

The S-PPU can draw up to 32 sprites per scan-line (overflowing this will only make it discard the ones with the lowest priority).

(ref:resulttitle) Result

(ref:resultcaption) Tada!

```{r fig.cap="(ref:resultcaption)", fig.align='center', tab.title="(ref:resulttitle)", tab.last=TRUE}
image('sppu_mario/complete.png', "(ref:resultcaption)", float=TRUE, class="pixel")
```

The S-PPU draws each scan-line on-the-fly by first processing the respective portion of each layer and then mixing them together.

One of the main constraints of NES games was the fact that they could only update their graphics during **V-Blank**, the moment from which the CRT's beam was returning to the starting point provided with a reasonable time frame to reshuffle some tiles without breaking the image.

Well, now thanks to the new capabilities of the SNES, this limitation gained a different meaning.

You see, because DMA/HDMA allows performing memory transfers without waiting for V-Scan `r cite("cpu-dma")`, games can now update tiles, colours and registers without waiting for the whole frame to be drawn. However, we can think beyond that: Since games can now change the S-PPU settings during **mid-frame**, this means that it's possible to activate different background modes **at different stages of the same frame**, opening the door to new and original game designs!

`r close_tabs()`

### Unique features

Truth to be told, I still haven't mentioned the most important characteristic of this console...

(ref:mode7bgcaption) Rendered Background layer.

(ref:mode7maptitle) Map

(ref:mode7mapcaption) Allocated Background map.

(ref:mode7disptitle) Displayed

(ref:mode7dispcaption) Rendered frame on the screen.<br>The first quarter of scan-lines use another mode to simulate distance, Mode 7 starts at the second quarter (this is possible thanks to HDMA).

(ref:mode7footer) F-Zero (1990).

```{r fig.cap=c("(ref:mode7bgcaption)", "(ref:mode7mapcaption)", "(ref:mode7dispcaption)"), open_float_group=TRUE, tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE}
image("mode7/layer.png", "(ref:mode7bgcaption)", tab.name="(ref:backgroundtitle)", tab.active=TRUE)
image("mode7/map.png", "(ref:mode7mapcaption)", tab.name="(ref:mode7maptitle)")
image("mode7/displayed.png", "(ref:mode7dispcaption)", tab.name="(ref:mode7disptitle)", caption.post="(ref:mode7footer)")
figcaption("(ref:mode7footer)")
```

Introducing **Mode 7**, *yet another* background mode, but this time, with a completely different way of working. While it can only render a single 8bpp background layer, it provides the exclusive ability to apply the following **affine transformations** on that plane `r cite("cpu-rgme")`:

- Translation.
- Scaling.
- Rotation.
- Reflection.
- Shearing.

The S-PPU uses a **rotation matrix** to control the parameters of this mode. I won't go into the math here, but depending on the desired effect, the CPU will have to perform some trigonometric functions (sine and cosine) to fill the entries of this table accordingly. This is really expensive for the 65C816, even with the use of fixed-point math. Luckily, with the 5A22, Ricoh added multiplication and division registers to offload some cycles.

`r close_float_group(with_markdown = TRUE)`

By the way, notice that the list of transformations doesn't mention **perspective**, which is what you see in the example game (F-Zero). This is achieved by altering the rotation matrix at each HDMA call, creating a pseudo-3D effect in the process.

Finally, due to the high number of calculations needed, the memory map is changed to optimise the pipeline of the two PPUs, the first one processes the **Tilemap** (where tiles are referenced) while the other fetches the **Tileset** (where tiles are stored).

### A convenient video out

All of the aforementioned advancements will be futile unless the console sends the picture to the TV in a format both can understand. With the Super Nintendo, the company debuted some sort of *universal-but-proprietary* connection called **Multi Out** which can transport many types of signals at the same time, including **Composite**, **S-Video** and **RGB** `r cite("graphics-pinouts")`.

Along with the console, Nintendo included a 'Multi Out to composite' cable since that was pretty much the common denominator of TVs back then. In Europe however, the **SCART** port was also very popular as many set-top boxes and VCRs relied on it. A great thing about SCART is that it can also carry many types of signals, this enables AV equipment to use the most optimal signal type without encountering compatibility issues. Unfortunately, Nintendo never shipped an official SCART cable that took advantage of the RGB pins exposed in the Super Nintendo.

Nonetheless, Nintendo altered the pinout of its PAL consoles to comply with the SCART protocol, and in doing so it replaced the 'composite sync' signal for a 12 Volts one (which tells the TV to set the 4:3 aspect ratio). So, even though Multi out is 'universal', the resulting RGB cables, if any, are region-specific.

I think the real benefits of Multi Out started to become evident during present times, as it allowed users to take advantage of the RGB signal with their state-of-the-art tellies without tampering with the internals of this console. Although, unlike composite and S-Video, RGB requires an extra 'sync' signal. For this, the cable can be wired up to capture the sync signal from composite or s-video; or for best results, use a dedicated sync line called 'composite sync'. But, as mentioned in the previous paragraph, only NTSC consoles carried the latter.

## Audio

This console provided some unique audio capabilities thanks to a dedicated set of chips designed by no other than **Sony**. The most important components of the audio subsystem are:

- **The S-DSP**: Plays ADPCM samples across eight different channels, they are mixed and sent through the audio output. The DSP is capable of manipulating samples with 16-bit resolution and a sampling rate of 32 kHz, it also provides:
  - **Stereo Panning**: Distributes our channels to provide stereo sound.
  - **ADSR envelope control**: Sets how the volume changes at different times.
  - **Delay**: Simulates *echo*, it also includes a frequency filter to cut out some frequencies during the feedback. Do not confuse this with *Reverb*!
  - **Noise generator**: Creates random waveforms that sound like white static.
  - **Pitch modulation**: Allows some channels to distort others. Similar to FM synthesis (used by [its competitor](`r ref("mega-drive-genesis#audio")`)).
- **The SPC700 CPU**: Also named 'S-SMP', it's an independent 8-bit CPU that communicates with the DSP and receives commands from the main CPU `r cite("audio-smp")`.
- **64 KB of PSRAM**: Stores audio data and programs. The main CPU is responsible for filling this up.
  - If 'Delay' is activated, some space will be allocated for feedback data (this is actually very dangerous, since if not used properly it can override some of our data!).

This sub-system functions independently: When the console is turned on, the SPC700 boots a 64-byte internal ROM that enables it to receive commands from the main CPU `r cite("audio-spc")`. After that, it stays idle.

(ref:sfmelodytitle) Melody

(ref:sfmelodycaption) Channels used for melody.

(ref:sfdrumstitle) Drums

(ref:sfdrumscaption) Drums are discriminated for demonstration purposes.

(ref:sfcompletetitle) Complete

(ref:sfcompletecaption) All audio channels.

(ref:sffooter) StarFox (1993).

```{r fig.cap=c("(ref:sfmelodycaption)", "(ref:sfdrumscaption)", "(ref:sfcompletecaption)"), open_float_group=TRUE, tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE}
video("melody", "(ref:sfmelodycaption)", tab.name="(ref:sfmelodytitle)", tab.active=TRUE)
video("drums", "(ref:sfdrumscaption)", tab.name="(ref:sfdrumstitle)")
video("complete", "(ref:sfcompletecaption)", tab.name="(ref:sfcompletetitle)", caption.post="(ref:sffooter)")
figcaption("(ref:sffooter)")
```

In order for the S-SMP to start doing some useful work, it needs to load a type of program called **Sound Driver**. The latter instructs the chip on how to manipulate the raw audio data that the main CPU sends to PSRAM. The driver also directs how to command the S-DSP.

As you can see, the sound subsystem was a huge advancement compared to the previous generation, but it was challenging to program as well. The documentation that Nintendo provided was notably known for including unintelligible sections and skipping important features altogether, so it was up to the programmers to carry out their own research.

As a consequence, there were tons of different sound drivers found on the market `r cite("audio-drivers")`, and some of them ended up uncovering impressive features. The flexibility that this system allowed meant that programmers could make their soundtrack shine or fade into oblivion... 

`r close_float_group(with_markdown = TRUE)`

### Pitch control

Pitch modulation enabled to play different notes using the same sample, the S-SMP also included a useful bender to alter the pitch in a smooth manner. Take a look at this extracted channel from Mother 2/Earthbound, both examples come from the original soundtrack, however, the first one has the pitch control disabled.

(ref:nopitchcaption) No pitch bend.

(ref:pitchcaption) With pitch bend enabled.

```{r fig.cap=c("(ref:nopitchcaption)", "(ref:pitchcaption)"), side_by_side=TRUE, fig.pos = "H"}
video('pitch/no_pitch', class="toleft", "(ref:nopitchcaption)")
video('pitch/pitch', class="toright", "(ref:pitchcaption)")
```

### Evolution from the NES

In order to demonstrate the evolution of sounds from the NES to the Super NES, here are two music scores, one from a NES game and another from its Super NES sequel. Both used the same composition:

(ref:nessnowcaption) Mother (1989).

(ref:snessnowcaption) Mother 2/Earthbound (1994).

```{r fig.cap=c("(ref:nessnowcaption)", "(ref:snessnowcaption)"), side_by_side=TRUE, fig.pos = "H"}
video('snowman_nes', class="toleft", "(ref:nessnowcaption)")
video('snowman_snes', class="toright", "(ref:snessnowcaption)")
```

### Advanced usage

(ref:kirbyfooter) Kirby's Dream Land 3 (1997).

```{r fig.cap=c("(ref:sfmelodycaption)", "(ref:sfdrumscaption)", "(ref:sfcompletecaption)"), open_float_group=TRUE, tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE}
video("kirby/trebble", "(ref:sfmelodycaption)", tab.name="(ref:sfmelodytitle)", tab.active=TRUE)
video("kirby/drums", "(ref:sfdrumscaption)", tab.name="(ref:sfdrumstitle)")
video("kirby/complete", "(ref:sfcompletecaption)", tab.name="(ref:sfcompletetitle)", caption.post="(ref:kirbyfooter)")
figcaption("(ref:kirbyfooter)")
```

Here's a more instrument-rich composition that takes great advantage of pitch modulation, echo and envelope.

This combination of techniques allowed the music to only require five channels in total, leaving the other three for sound effects.

`r close_float_group(with_markdown = TRUE)`

### Stereo confusion

The DSP's volume controls are organised in chunks of 8-bits signed values `r cite("audio-gst")`, this means that the volume can be set up with **negative values**. *But hang on*, if '0' means mute, what would a number like '-1' do? Well, it will **invert the signal**.

This is notably used for creating a special **surrounding effect**, which is accomplished by setting the stereo channels to output **out of phase** (one channel outputs the normal signal and the other outputs the same signal but inverted). 

Unfortunately, abusing this feature results in very unpleasant results (e.g. the feeling that the music is coming from inside your head), so you will notice that most SNES emulators just skip this setting altogether.

Additionally, out-of-phase stereo gets cancelled out on mono devices, so games included a 'stereo-mono' selector to avoid muting their own soundtrack.

## Games

Overall, games are written in **65816 assembly** and when it comes to designing the cartridge, there are two ways of electrically connecting the address pins between the ROM and the CPU `r cite("games-bazzinotti")`:

- **LoROM Model**: Data is available in 32 KB chunks with 128 banks to choose.
- **HiROM Model**: Data is available in 64 KB chunks with 64 banks to choose.

### Expansion

The modular architecture of the Super Nintendo allows for numerous types of **Enhancement chips** that are included on cartridges and provide extra features such as the 'SuperFX' for 3D polygon composition or the 'SA-1' for co-processing, some of these chips complemented already complex functions like Mode 7 (which allowed to transform the background but not the sprites).

### Recognisable behaviour

Have you ever wondered what causes games to lag? When the V-Blank interrupt is called to allow graphics update, sometimes the game is still executing some heavy code and skips the V-Blank window, graphics can't be updated until the next V-Blank call and since the frame wasn't updated, this is manifested as a drop in frame-rate. It can also happen the other way around, extensive processing during a V-Blank won't allow the PPU to output the video signal (as the bus is blocked). Thus, black lines during a scan will be shown, although this is barely noticeable since the frames update 50 or 60 times per second.

## Anti-piracy / Region Lock

Cartridges are physically different between regions, so they won't fit on consoles from different regions. This was addressed by using third-party adapters.

This console, like the NES, still incorporates the **10NES** system, which locks any non-authorised distributors. This was cracked when the CIC chip was successfully cloned.

To protect against bootleg cartridges, games also included a chain of piracy checks:

1. Comparing the SRAM size (bootlegs normally include a bigger size to fit any game).
2. A series of checksums on the code that check if the previous check was removed. These checks would be dispersed at different stages of the game, so they'd be difficult to find.

This could be defeated by manually removing these routines but would take a long time to find all of them. After all, they would be scattered around the game only to upset the player (and hopefully make them buy a legitimate copy...). Truth to be told, you'll notice that most ROMs surfing the internet had all their piracy checks removed.

## That's all folks

(ref:glasgowcaption) My modded SNES with an American cartridge.<br>That game was only released in the states. Luckily, a lad was selling it in Glasgow!

```{r fig.cap="(ref:glasgowcaption)", fig.align='center', centered=TRUE}
image("mysnes.png", "(ref:glasgowcaption)", class = "centered-container")
```
