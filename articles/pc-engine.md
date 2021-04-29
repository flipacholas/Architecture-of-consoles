---
name: PC Engine / TurboGrafx-16
subtitle: A small but quick leap
date: 2020-12-18
releaseDate: 1987-10-30
cover: "pcengine"
javascript: []
generation: 4
top_tabs:
  Models:
    Japanese:
      caption: "The PC Engine
      <br>Released on 30/10/1987 in Japan"
    International:
      caption: "The TurboGrafx-16
      <br>Released on 29/08/1989 in America, 22/11/1989 in France and 1990 in the UK and Spain"
  Motherboard:
    caption: "Showing the TurboGrafx's motherboard
    <br>The PC Engine one was considerably smaller and stacked"
  Diagram:
    caption: "Looks pretty simple, huh?"

# SEO
title: PC Engine / TurboGrafx-16 Architecture

# Historical
aliases: [/projects/consoles/pc-engine/]
---

## A quick introduction

Hudson and NEC joined forces to kickstart the 4th generation of consoles. Unfortunately, their efforts will be eventually eclipsed once the competition arrives. Nonetheless, their console will stay on the top as one of the most compact designs in the market.

---

## Models and variants

Just like the Master system, NEC shipped a lot of revisions and strange variations which can be a bit troubling to follow at first. So, for future reference, here are the most important models:
- **PC Engine**: The first console featuring this architecture, but only released in Japan. It looks like a small white box with minimal ports.
- **TurboGrafx-16**: Redesigned PC Engine for the American market. It has the same features with a different external appearance. Reminds me of the [NES/Famicom adaptation]({{< ref "nes" >}}).
- **Supergrafx**: Enhanced version of the PC Engine with a completely different case and new hardware. Its exclusive features are out of the scope of this article.
- **PC Engine Duo/TurboDuo**: A combination of the PC Engine/TurboGrafx and the aftermarket CD-ROM² expansion.

This article will focus on the PC Engine/TurboGrafx-16, but will also discuss the expansions that led to the release of the PC Engine Duo/TurboDuo.

---

## CPU

Inside this console, we find the **HuC6280**, a chip made by **Hudson Soft** that houses two components. One of them is the CPU, which can operate at two speeds: **~1.79 MHz** and **~7.16 MHz**.

The HuC6280 is not an off-the-shelf component, like the [Z80]({{< ref "master-system#cpu" >}}), but instead a proprietary CPU designed by NEC. After checking out the official docs, it does seem to replicate a lot of behaviour from the famous **MOS 6502** and **WDC's 65C02**. That being said, what does all of this mean for the programmer?

Before we start, we've already covered some parts of the MOS 6502 [here]({{< ref "nes#cpu" >}}) and also described a 16-bit variant made by WDC and Ricoh [here]({{< ref "super-nintendo#cpu" >}}), in case you want to check them out first.

The 65C02 is another modified version of the MOS 6502 made by Western Design Center, it's a lot more efficient due to its manufacturing approach (CMOS). From the software side, the 65C02 adds a couple of new instructions and modifies the behaviour of some existing ones.

We could talk a lot more about the 65C02, but I think it's better to focus on the features added by Hudson (complementing the 65C02) because they are critical to efficiently operate this console. Compared to the 65C02, the HuC6280 has:
- **More opcodes**, some targeting the components surrounding the CPU.
- An **8-bit I/O port** used to interface external components.
- An **interrupt controller** to receive interrupts from other devices.
- A **timer**. It works by counting down from a specified value and when it finishes, it dispatches an interrupt to the CPU.

Finally, there are **8 KB of RAM** available for general purpose.

#### Memory access

One thing I didn't mention *yet* is that NEC also added a **Memory Management Unit** or 'MMU' next to the CPU, allowing to handle **21-bit addresses** (remember the original 6502 only has 16-bit addresses). Thus, the amount of memory that can be accessed raises from 64 KB to **2 MB**.

This MMU is very different from any modern-day MMU. The one found in the PC Engine is composed of **eight 8-bit registers** (called **Mapping Register** or 'MPR') which are combined with the CPU's 16 address lines to form a 21-bit address bus.

{{< centered_container >}}
  {{< linked_img src="mmu.png" alt="MMU Address Diagram" >}}
  <figcaption class="caption">Addressing approach of the MMU</figcaption>
{{< /centered_container >}}

This works as follows:
1. The CPU can read from and/or write to any MPR using the special `TAM` and `TMA` instructions, respectively.
2. When physical memory is accessed, the MMU reserves the address lines A13-A16 from the CPU to select one of the eight MPRs.
3. The MMU then combines the CPU lines A0-A12 with the 8-bit value of the selected MPR to set A13-A20. Resulting in a 21-bit address.

Consequently, this MMU groups physical memory in 8 KB pages (12 CPU lines = 8 KB pages), which is the amount of memory the CPU can access without swapping out the MPR nor altering its value.

Anyway, don't overwhelm yourself if you have trouble understanding it (it's just an unconventional modus operandi some may find interesting).

---

## Graphics

This is taken care by the **Hudson Soft HuC6270**, a separate chip also referred to as the **Video Display Controller** or 'VDC'. The HuC6270 will draw everything that the player will see on the screen and its functionality is very similar to [Sega's counterpart]({{< ref "master-system#graphics" >}}), so please check out that article beforehand since I'll focus on what's different with Hudson's offering.

#### Organising the content

First things first, the VDC is a **tile engine** (pretty much the standard until the 5th generation showed up) but notice how the PC Engine includes **64 KB of VRAM** which is a significant amount compared to the competition. This may lead to a new type of content, which we'll check later on.

{{< centered_container >}}
  {{< linked_img src="graphics/vdc.png" alt="VDC Memory Diagram" >}}
  <figcaption class="caption">Memory architecture of the VDP</figcaption>
{{< /centered_container >}}

The way graphics data is arranged is a bit confusing: Both CPU and VDC use 16-bit addresses, but while the CPU can only handle 8-bit words, the HuC6270 stores 16-bit words in VRAM. This means that a single address in RAM contains a byte while an address in VRAM stores two bytes, so developers had to watch out for that discrepancy when transferring data to VRAM.

The reason for this comes down to the way Hudson organised the circuitry: The VDC has a 16-bit address bus but **only the first 15 lines are controlled** (the last one is always set to '0'), so odd addresses are fetched from the second byte. I don't know why Hudson went down this path, but I do know that all of this would make better sense if the system had instead 128 KB of VRAM (since a 16-bit address bus can only access up to 64 KB, so by somehow doubling the data bus, up to 128 KB can be retrieved). Maybe that was the original plan for Hudson/NEC?

#### Constructing the frame

Aside from the aforementioned granularity oddity, functionality-wise the VDP is very simple. The subsystem has three main components: The **VDC** and **VRAM**, which we already discussed - and the **video encoder** (also called 'VEC', we'll see more about it in due time).

The system can use multiple resolutions, this is because the game can alter a set of registers that act as parameters for controlling the display timings, which in turn alter when the CRT starts beaming the frame (as opposed to overscan). The minimum resolution is **256 × 224 pixels**, while some homebrew has proved this system can go as high as **512 × 240 pixels**.

Now let's see how a frame is drawn step by step, for this, I'll borrow *Bonk's Adventure*'s assets.

{{< tabs >}}
{{< tab active="true" name="Tiles" >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel desktop-margined" >}}
    {{< tab name="Background" active="true" >}}
      {{< linked_img src="graphics/tiles_background.png" >}}
    {{< /tab >}}
    {{< tab name="Sprites" >}}
      {{< linked_img src="graphics/tiles_sprites.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Some tiles found in VRAM</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
As a quick reminder, tiles are just **8x8 pixel bitmaps** that the renderer fetches to draw portions of the screen. With the VDC, the frame is composed of two planes: The background layer and the sprite layer.

Inside VRAM there's an area called **Character generator** where tiles exclusive to the background layer are defined. Each pixel of a tile occupies four bits, so it can use up to 16 colours. In theory, up to 4096 background tiles can be defined, but this is less in practice due to VRAM being much smaller.

Sprites, on the other hand, are drawn using tiles from a separate memory location in VRAM, this is called **Sprite Generator** and differs from the previous Character generator as tiles here are **16x16 pixels** wide.  

The video encoder is a separate chip that stores **32 colour palettes** (16 for the background and 16 for sprites), each palette stores 16 colours and each colour is 9-bit wide (3 bits for Red + 3 bits for Green + 3 bits for Blue).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Storing tiles" >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Background" active="true" >}}
      {{< linked_img src="graphics/storagetiles.png" >}}
      <figcaption class="caption">How a single background tile is structured</figcaption>
    {{< /tab >}}
    {{< tab name="Sprites" >}}
      {{< linked_img src="graphics/storagetiles_sprites.png" class="pico" >}}
      <figcaption class="caption">How a single sprite tile is structured</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
(This section is written for those interested in how Hudson took advantage of 64 KB of VRAM with that 16-bit granularity, but you don't have to understand it completely to be able to follow the rest of the article).

So far we've discussed that each pixel of a tile is stored using 4 bits (or a half byte, also called a **nibble**). Now, Hudson dictates that **tiles are composed of four 8x8 bitmaps** (called 'CH0', 'CH1', 'CH2' and 'CH3', respectively). Each map is 1-bit wide, but once the four are combined, they form the final tile with 4-bit pixels.

Also, remember the 16-bit alignment on VRAM? **Each 16-bit word stores a single row of two 1-bit bitmaps** (8 rows + 8 rows). So, after writing eight entries, two maps will be stored (instead of just one). Please have a look at the diagrams to help understand this better.

The same happens with Sprite tiles, but since they are 16x16 bitmaps, each bitmap occupies 16 words. To put it another way, to store a single sprite tile, 64 words are taken (equivalent to 8 bytes in VRAM).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background Layer" >}}

{{< float_block >}}
  {{< linked_img src="graphics/background.png" class="pixel" >}}
  <figcaption class="caption">Rendered background layer</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The background layer is constructed by filling the **Background Attribute Table** with entries in VRAM, the position of each entry defines X/Y coordinate of the tile in the screen. Each entry contains the tile index from the Character Generator and the colour palette.

The maximum dimension of this layer is **1024 x 512 pixels** (128 x 64 tiles), but programmers can set up a 256 x 256 pixels (32 x 32 tiles) layer as the minimum.

As always, this layer is scrollable by changing the value of some registers in the VDC.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprite Layer" >}}

{{< float_block >}}
  {{< linked_img src="graphics/sprites.png" class="pixel" >}}
  <figcaption class="caption">Rendered Sprite layer</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The VDC contains an internal memory called **Sprite Attribute Table Buffer** where up to 64 sprites can be defined. Each entry of the table stores the independent X/Y position, colour palette, tile index and H/V flip. Furthermore, there's an attribute allowing to combine a sprite with another one.

Each entry is 8 bytes long, although some space is wasted due to the 16-bit granularity.

To top it all off, **the CPU can't access this table**, so it needs to be completed in VRAM and then activate a DMA channel to copy it to the VDC (so the latter can use it).

Regarding limitations, there can only be up to 16 sprites per scan-line. On the other side, interrupts can be set so the game can be notified when there's been a case of sprite overflow or [collision]({{< ref "master-system#tab-4-1-collision-detection" >}}).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}

{{< float_block >}}
  {{< linked_img src="graphics/complete.png" class="pixel" alt="Result" >}}
  <figcaption class="caption">Tada!</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
So far we've seen how the VDC does all the heavy work, but the last task is actually delegated to the **Video Encoder** or 'VCE'. Hudson named this chip **HuC6260** and its basic function is to receive 9-bit data streams from the VDC, apply the colour palettes, and send the result to the TV (in the form of an analogue signal).

If you read previous articles you may be familiar with the importance of timing. Well, here is no exception: To avoid unwanted artefacts (such as 'snow'), the VCE can only be updated during a vertical interrupt.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Video Output

The Video encoder outputs RGB (along with Sync) and YPbPr, this is ideal for use with a SCART cable or component cable, respectively. This looks good so far!...

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="RF" active="true" >}}
      {{< linked_img src="rfport.jpg" >}}
      <figcaption class="caption">RF port at the right side of the PC Engine</figcaption>
    {{< /tab >}}
    {{< tab name="Accessory" >}}
      {{< linked_img src="turbo_booster.png" >}}
      <figcaption class="caption">The 'Turbo Booster' for the TurboGrafx-16
      <br>Connected to the Expansion port to supply video composite and power</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
... Unfortunately, Hudson decided to fit an RF modulator as the only way to get video out-of-the-box, so it's not that great after all. But then again, the PC Engine was designed in the 80s, so this approach guaranteed compatibility with all kinds of TVs (assuming they are from the same region of the console).

On the bright side, the Expansion Port contains pins that carry RGB video and multiple sync types, but they will need an external accessory to be able to advantage of them.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Audio

The PC Engine contains a [Programmable Sound Generator]({{< ref "master-system#audio" >}}) or 'PSG' like many other third-generation consoles analysed in this website. On the other side, this PSG particularly relies on the **waveform memory** (also called 'waveform buffer') approach to synthesise sound, as opposed to using a pre-defined set of waveforms (i.e. pulse, triangle, etc). Waveform memory enables programmers to define their own waveforms, which gives more flexibility for soundtrack arrangements.

{{< float_group >}}

{{< float_block >}}
  {{< video src="rtype" >}}
  <figcaption class="caption">R-Type (1987)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
There are **six channels** in this system, each one is set up by writing to a set of registers, there's quite a lot of attributes available to customise so here are the most important ones:
- **Waveform shape**: The waveform cycle is generated using thirty-two 5-bit values, each one corresponds to the amplitude of the wave at a particular time.
  - Some games like *Fire Pro Wrestling 2* and *Bloody Wolf* even altered the values during playback to obtain special sounds (at the expense of audible clicks during transfers).
- **Frequency control**: Two 8-bit registers alter the frequency of the channel to produce different musical notes with the same waveform.
- **Amplitude level**: A single 8-bit register stores two 4-bits values that specify how loud the channel will sound. The two values correspond to the 'Left' and 'Right' **panning control**, a pioneering feature compared to what the competition offered.

{{% /inner_markdown %}}

{{< /float_group >}}

#### Extra functions

Some groups of channels have other modes of operations. For instance, the last two channels are connected to a **noise generator**. Moreover, the second channel can act as a **Low-frequency oscillator** to modulate the first channel, much like [FM synthesis]({{< ref "mega-drive-genesis#audio" >}}).

Speaking of modes, there's another one available called **Direct D/A (DDA)** which enables the CPU to write over the audio buffer directly (bypassing the PSG). Consequently, the PC Engine can play **PCM samples**. These are still 5-bit and tightly dependent on CPU cycles. Although, two channels can be combined to reproduce 10-bit samples! I strongly suggest you to check out the 'Sources' section to listen to examples.

All in all, this makes the PC Engine's PSG an incredibly flexible piece of hardware.

#### Final sound

The PSG will mix everything and output a stereo signal. But again, unless you find a way to bypass the RF port, you'll only hear mono from your telly.

---

## I/O

This console had a massive amount of accessories, some of which completely enhanced the console internally (by providing more RAM, for instance) and externally (by adding more ports and/or the possibility to read other storage mediums).

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="expansion.jpg" >}}
  <figcaption class="caption">Expansion port at the back of the PC Engine</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This is greatly attributed to the inclusion of the **Expansion Port** on the back of the console, which gave access to the following components:
- **+5 Volts line**: To power the accessory.
- **A Voltage input line**: To bypass the original PSU and power the console through the accessory.
- **Audio Input and Output**: Which can receive the stereo audio output and/or send audio from an input line.
- **CPU data and address lines**: Enables the CPU to access the accessory and vice versa.
- **VDC data lines** (those that go to the Video encoder): Allows the accessory to tap those lines, I'm not sure what for though.
- **CPU control lines**: Reads the HuCard and CD detection flag, speed mode used and also has access to interrupt lines.
- **Analogue video out**: Including RGB + Vertical Sync and Horizontal Sync + Composite sync.
{{% /inner_markdown %}}

{{< /float_group >}}

I'm curious to know to which extent NEC or Hudson envisioned this console, were they trying to build some sort of 'modular console' that could be upgraded to step into the next generation?  

#### The rest

I still haven't mentioned the controller (or Joypad, as some places call it). It's very similar to the other offerings. The console only allows to connect one of them and games check a single address in memory to get the key presses. That address returns a 4-bit value.

---

## Operating System

There's no internal ROM or 'BIOS' or anything else that runs before the game, thus we don't have an Operating System here. The reset vector is found at address `$FFFE` and `$FFFF` (remember that the internal registers are 8-bit but memory addresses are 16-bit, hence we need two words to initialise the program counter) and those addresses point to the game card.

This also means that it's up to the programmer to take care of 'housekeeping' (i.e. initialising memory, set up the MMU and so on...).

---

## Games

Programs are written in 6502 assembly, spiced with extra 65C02 opcodes and the ones introduced by Hudson.

#### Standard Medium

Instead of relying on those *chunky and boring* cartridges everyone else seems to like, NEC/Hudson devised yet another medium, this time with the size of a credit card, called **HuCard**. They derive from an older medium called 'Bee Card', used by some MSX games.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="hucard.jpg" class="pico" >}}
  <figcaption class="caption">Typical retail game</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Curiously enough, they are very similar to the [Sega Card]({{< ref "master-system#medium" >}}) but they contain 38 pins instead of 35. Internally, the differences are more significant:
- The CPU memory map can access up to **1 MB of HuCard memory without a mapper**, hence the majority of games stuck with 1 MB ROMs. Meanwhile, games like 'Street Fighter II' resided in a 2.5 MB ROM!
- There's an **Audio In** pin which seems to allow the card to feed the console an extra audio channel. 
{{% /inner_markdown %}}

{{< /float_group >}}

#### CD-ROM Expansion

That expansion port opened the door to such a massive amount of accessories and expansion units that they eventually turned a simple PC Engine into something completely different. As if wasn't enough, the HuCard slot complemented the possibilities of expansion. What I mean to say is that for this article, I'll have to focus on the notable upgrades (some of which were included in future revisions of the console).

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Reader" active="true" >}}
      {{< linked_img src="cd/reader.png" >}}
      <figcaption class="caption">CD-ROM² Reader (TurboGrafx-16 version)
      <br>Were they in the Discman business back then?</figcaption>
    {{< /tab >}}
    {{< tab name="Compartment" >}}
      {{< linked_img src="cd/compartment.png" >}}
      <figcaption class="caption">Base required to connect the console with the reader
      <br>It also supplies power and composite video out</figcaption>
    {{< /tab >}}
    {{< tab name="Fitted" >}}
      {{< linked_img src="cd/fitted.png" >}}
      <figcaption class="caption">How it looked with everything fitted</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Let's take a look at the **CD-ROM²** expansion, which consists in a CD-ROM reader and a special HuCard called **System Card**, the latter acted as a **BIOS** to bootstrap the game and provide some routines to interface the reader. Internally, the reader included **64 KB of RAM** for general purpose, another **64 KB of RAM** to stream ADPCM samples and finally, **2 KB** for save data. As you can guess, this enabled game developers to take the advantage of extra storage and CD audio, while giving publishers the economic relief to distribute their games in a non-proprietary medium.

Years later, NEC released another CD module called **Super CD-ROM²** and while the reader's hardware remained mostly the same, its general-purpose RAM was incremented to 256 KB. Customers that already owned a CD-ROM² unit were able to 'update it' by purchasing the **Super System Card**, which included the extra RAM (and one more I/O routine!). On top of all that, NEC later shipped new BIOS updates called **Arcade Card**, which in this case contained **2 MB of RAM**.
{{% /inner_markdown %}}
{{< /float_group >}}

CD-based games strictly depended on the BIOS card they were developed for, although newer cards were backwards compatible (with some exceptions). Thus, the Arcade Card is the preferable choice for users that want to play *almost* all CD-ROM based games. I mentioned 'almost' since third party companies also released their own BIOS cards (i.e. 'Games Express CD Card'), and these were required to play games specifically from that studio.

With the Super CD-ROM², NEC/Hudson also shipped a new variant of the PC-Engine/TurboGrafx-16 called **Turbo Duo** that bundled the console, reader and BIOS card in a single package.

#### Other expansions

If you are curious about other expansions released, check out the catalogue link at the 'Sources' section (end of the article).

---

## Anti-Piracy and Homebrew

HuCards are region-locked, meaning that a PC-Engine game won't work on a TurboGrafx-16 (or vice versa) out-of-the-box. This is because the data lines are inverted on the American version. As you can guess, this isn't something a cheap adapter can't tackle. However, games may include routines to check the system's region.

On the other side, CD-ROM games are neither region-locked nor copy-protected, but bear in mind that they still need a System Card to bootstrap them (which is region-locked).

---

## That's all folks

{{< centered_container >}}
  {{< linked_img src="mypc.jpg" alt="My PC Engine" >}}
  <figcaption class="caption">My PC Engine displaying from an RF cable (<i>nostalgic purposes... I think</i>)
  <br>Big thanks to Matt for donating it!</figcaption>
{{< /centered_container >}}

This is it! You've just read the last article of the year.

2020 has been an 'interesting' year for the website (setting aside all those negative events that have affected all of us). The year started with the [Wii article]({{< ref "wii" >}}) and then carried on with the [PS2]({{< ref "playstation-2" >}}), [Xbox]({{< ref "xbox" >}}), [Nintendo DS]({{< ref "nintendo-ds" >}}), [Master System]({{< ref "master-system" >}}) and finally, this one.

A lot has changed in the number of visitors too. It had a slow start at first, but then during the summer, it caught an amazingly high number of visits. I want to thank Hackernews users, OSNews, Reddit, Gigazine, Youtubers, Twitter users and lots of other forums and people for sharing the articles. And of course, a big shoutout to the donors whose generosity helped keep the site afloat!

Have a nice Christmas, happy New Year 2021 and until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- nfggames.com, [**101 Secrets of the PC Engine**](https://nfggames.com/games/pce/)
- MagicKit, [**PC Engine Hardware**](http://www.magicengine.com/mkit/)
- Charles MacDonald, [**TurboGrafx-16 Hardware Notes**](https://www.romhacking.net/documents/302/)

#### CPU

- NEC, **HuC6280 - CMOS 8-bit Microprocessor Hardware Manual**
- NEC, **HuC6280 - CMOS 8-bit Microprocessor Software Manual**
- Bruce Clark, [**WDC 65C02 Opcodes**](http://www.6502.org/tutorials/65c02opcodes.html#5)

#### Graphics

- NEC, **HuC6260 - CMOS Video Color Encoder Manual**
- NEC, **HuC6270 - CMOS Video Display Controller Manual**
- ChibiAkumas, [**Hardware Sprites on the PC Engine (TurboGrafx-16)**](https://www.chibiakumas.com/6502/platform4.php#LessonP33)
- Chris Covell, [**PC-Engine/Turbografx Assembly Programming Tutorials** (Youtube)](https://www.chrismcovell.com/PCEdev/)

#### I/O

- Game Station X, [**The PC Engine Exp Connector**](https://gamesx.com/misctech/pcebp.php)
- Game Station X, [**TurboGrafx/Duo/PC-Engine Controller Data**](https://gamesx.com/controldata/turbocont.htm)

#### Audio

- NEC, **HuC6280 - CMOS Programmable Sound Generator Manual**
- youtube.com, [**Stage 1 -Town- Bloody Wolf OST** (runtime waveform example)](https://www.youtube.com/watch?v=iuUymmU7UWs)
- youtube.com, [**Fire Pro 2 OST** (runtime waveform example)](https://www.youtube.com/watch?v=V845pHyaePk)
- youtube.com, [**Batman OST** (5-bit PCM example)](https://www.youtube.com/watch?v=dGAhM8L-bT8)
- youtube.com, [**ADPCM demo PC Engine** (10-bit PCM example)](https://www.youtube.com/watch?v=jdWYOHPV6Uw)

#### Operating System

- shu.sheldows.com, [**Memory Map**](http://shu.sheldows.com/shu/download/pcedocs/pce_memory.html)

#### Games

- pc-engine.co.uk, [**Accessories Catalogue**](http://www.pc-engine.co.uk/?section=accessories)
- hardwarebook.info, [**HuCard Pinout**](http://www.hardwarebook.info/HuCard)

#### Photography

- Console (studio photos): [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams, casual photos and game screenshots: **Me**