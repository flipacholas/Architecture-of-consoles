---
long_title: "Architecture of the Neo Geo"
short_title: "Neo Geo Architecture"
name: Neo Geo
long_name: Neo Geo
subtitle: The arcade at home
date: 2024-06-19
release_date: 1990-04-26
generation: 4
aliases: ["/writings/consoles/neogeo-private-330"]
cover: neogeo
top_tabs:
  Model:
    caption: "The Neo Geo 'AES'.<br>Released on 26/04/1990 in Japan and on 01/07/1991 America and Europe"
    file: "international"
  Motherboard:
    caption: "Showing revision 'NEO-AES3-6'.<br>I took this photo using the model I bought. Also, the previous owner re-arranged some capacitors to improve the video signal."
  Diagram: {}
---

## A quick introduction

Straight from the arcade world, the Neo Geo was, without a doubt, the most expensive hardware of the 4th generation. This begs the question: how capable was it and how did it compare with the rest?

In this entry, we'll take a look at the result of one company (SNK) setting budget restrictions aside and shipping a product meant to please both arcade owners and rich households.

## {.supporting-imagery}

### Models and variants

The Neo Geo is exceptional in the sense it was designed for two markets at the same time: the arcade and the home. Consequently, SNK produced a single architecture which was sold in the form of **two variants**, each taking into account a particular market:

- The **Multi Video System** or 'MVS' is the form tailored for arcade centres. Its I/O follows the 'JAMMA' specification (with some deviations [@general-jamma]), facilitating its integration with arcade cabinets. Alongside, its specialised motherboard provides extra circuitry for handling coin-based payments. Be as it may, throughout its lifetime, SNK ended up shipping tons of revisions, some of which can fit up to six game cartridges at once [@general-mvs_boards].
- The **Advanced Entertainment System** or 'AES' is the simplified design for homes. While smaller and with reduced I/O, it doesn't compromise its fundamental capabilities.

I suppose I should also mention the **Neo Geo CD**, which is the successor of the AES variant carrying the obvious adoption of the CD-ROM for game distribution. Just like its previous forms, there were multiple revisions of this model.

In any case, for this analysis, I'll be focusing on the AES variant, as that's the one I've got with me. I will, nevertheless, make deserved references to the MVS when it comes to bonus functionality.

## CPU

The system features a **dual-processor** configuration composed of a **Motorola 68000** and a **Zilog Z80** CPU, two big icons of 80s electronics. The 68000 is tasked with executing the **main program** (also known as _the game_) while the Z80 is relegated to **sound management** [@cpu-z80]. You may recall identical roles within the [Mega Drive/Genesis](mega-drive-genesis), but that's where the similarities stop, as the chip variants and layouts are somewhat divergent.

![The Motorola 68HC000 chip inside the Neo Geo, this one is second-sourced from Toshiba.](cpu/photo.jpg)

To give you an example, the Neo Geo's 68000 runs at **12 MHz** (4 MHz faster than the Mega Drive's). Additionally, the specific chip fitted is the **68HC000**, a variant distinguished by its composition of CMOS gates (as opposed to the previous and less-efficient NMOS process).

If you want to know what's inside the 68000 and the Z80, I have done previous analyses in the [Mega Drive/Genesis](mega-drive-genesis#cpu) and the [Master System](master-system#cpu) articles, respectively. Thus, for the Neo Geo's study, I will focus on their new uses.

### A multitude of support chips

The most notable difference between the Neo Geo and any home console of the same generation is that, during the design of the former, SNK wasn't constrained by the budget limitations of the average household. In doing so, the motherboard features an extraordinary amount of integrated controllers that not only remove many bottlenecks of the CPUs but also expand their capabilities.

The list of chips is tedious, but the most confusing part is that throughout revisions SNK re-designed the chipset, either by unifying multiple chips or splitting them. So, for now, let me tell you that the motherboard contains the following groups of 'accelerators':

- **68k Controllers**: To seamlessly switch between vector tables. I explain this area in the next paragraphs.
- **Z80 Controllers**: To alleviate the memory addressing limitations.
- **I/O Arbiters**: To delegate all I/O operations, saving cycles in the way.
- **Video Display Controller**: To perform all graphics operations. This will be discussed in a dedicated section called 'Graphics'.
- **Cabinet controllers**: For the arcade variant only (the 'MVS'). These handle the coin slot, provide a scoreboard and perform system diagnostics.
- **Home controllers**: Likewise but only found on the home variant (the 'AES'). In this group, we find a video encoder that generates TV-compliant video signals.

From now on, I'll use these titles to refer to any group throughout the article. In some cases, I will also name specific chips within the groups to denote special importance.

### Sophisticated I/O handling

Let me introduce you to a noble capability of the 68000 which I didn't cover until now: the **vectorised interrupt table**. Earlier CPUs like the [6502](nes#cpu) and the [Z80](master-system#cpu) had a constrained design to handle I/O communication. Whenever an external device wanted the attention of the CPU, the former had to send an interrupt request which, in turn, the CPU had to run a software routine to process it. The amount of information during the request was very limited, meaning the CPUs needed to spend extra cycles to investigate 'who' triggered the interrupt and 'why' was the CPU interrupted.

To be fair, CPUs like the Z80 provided extra circuitry to allow the peripheral to tell the CPU which instruction to execute next. Nevertheless, this wasn't widely adopted.

The 68000 improved this technique by treating interrupts as **exceptions**. Peripherals are required to identify themselves once they interrupt the CPU [@cpu-68k_int]. Then, the latter uses the identifier to compose a memory address, which in turn points to a tailored software routine. All in all, this enables programmers to handle I/O communication efficiently, as they can now catalog their dedicated interrupt handlers in a memory area called **autovector table**, and the CPU will execute each automatically.

The Neo Geo takes advantage of this design with the addition of a controller chip called the **NEO-E0**. Essentially, both the Neo Geo's Operating System and the game implement a dedicated exception table, this is because the OS' table focuses on hardware interfacing and the game's one is used during normal execution. However, the 68000 is only aware of one at a time. So, the NEO-E0 provides automatic [bank switching](nes#going-beyond-existing-capabilities) that makes the 68000 read from the right table at all times.

### Inter-process communication

Whenever there's a system featuring a dual-processor layout, there must be a way for both CPUs to communicate. In this console, SNK implemented inter-process communication (IPC) with the use of an **I/O arbiter** chip. The latter exposes a single 8-bit register, which the 68000 can write to [@cpu-68k_z80].

![Representation of the IPC channel between the 68000 and the Z80.](cpu/ipc.png)

Once the register is updated, the arbiter will send a non-maskable interrupt to the Z80. From then on, the Z80 may read the value (mapped at a [port](master-system#accessing-the-rest-of-the-components)) and react accordingly. This includes writing back to that register, so the 68000 receives new data. However, for some reason, the 68000 will not be interrupted (so it will need to do some sort of polling).

The specific I/O arbiter chip has changed throughout different motherboard revisions. It was first implemented with the 'PRO-B0' chip and then replaced with the 'NEO-C1'.

### Anti-unpleasantries

Speaking of interruptions, something that arcade cabinets can't afford is requiring constant maintenance.

We are no strangers to games suddenly freezing due to a software bug. The fix is no mystery either: just power cycle and start the game again. Now, imagine if this would happen to an arcade cabinet. Unless the owner happens to be near, the broken cabinet would only be losing money.

The Neo Geo - having inherited the requirements of coin-slot machines - provides an 'auto-repair' process by bundling a **Watchdog program**. This is found in the I/O arbiter chip [@cpu-watchdog].

![Arcade cabinets can't afford to be continuously serviced. They're already busy trying to lure players.](games/dragon.png){.pixel}

The system works as follows: games are required to periodically notify the watchdog of its presence (by writing to a particular register). In the unfortunate event the Watchdog doesn't receive a ping after a certain amount of time, it will assume the program froze and proceed to reset the system.

Thanks to this, MVS owners didn't need to keep an eye on their cabinets. Meanwhile, those lucky kids who could afford an AES system didn't even have to move from their luxury sofa whenever their games crashed.

If it wasn't enough, MVS motherboards complement this system by fitting an extra microcontroller, the **NEO-F0**. This drives the **coin lockout** mechanism, preventing users from wasting coins on failed systems [@cpu-coin_lockout], among other things.

### Memory available

Memory is scattered across the motherboard and the game cartridge (which, by the way, is massive in size). Remember this is an expensive system, so memory bandwidth and capacity were top priorities.

In the case of general-purpose memory, there are **64 KB of RAM** available to the 68000. Meanwhile, the Z80 is provided with **2 KB**.

MVS models additionally include another **64 KB**. It's plugged into a battery and meant to store game scores, so there's no use for general purposes, however.

Curiously enough, this system doesn't feature any sort of **Direct Memory Access** (DMA) to speed up memory transfers. I presume it's not a requirement considering the amount of dedicated buses.

## Graphics

The Neo Geo is the pinnacle of tile-based graphics, its richness of colours is comparable to [frame-buffers-based systems](sega-saturn#graphics) (which only became affordable four years later). Be as it may, when it comes to effects, the [Super Nintendo](super-nintendo#graphics) has managed to do more with less.

### Design

The graphics subsystem revolves around the typical **Video Display Controller** (VDC) model, but this time is surrounded by a considerable number of components. The result is a console that can display an immense amount of sprites, and such is the number that the iconic [background layers](nes#tab-5-2-background-layer) are no longer relevant. This makes sense as backgrounds were originally devised to tackle the limited number of sprites.

::: {.subfigures .tabs-nested .pixel}

![Metal Slug 3 (2000).](games/metalslug3.png){.active title="Metal Slug"}

![Neo Turf Masters (1996).](games/turfmast.png){title="Golf"}

![Neo Drift Out (1996).](games/neodrift.png){title="Drift Out"}

![Art of Fighting 3: The Path of the Warrior (1996).](games/artoffight.png){title="Fighting"}

![Windjammers (1993).](games/windjammers.png){title="Windjammers"}

Example of Neo Geo games.

:::

That being said, the console broadcasts a frame in compliance with NTSC and PAL signals (the choice depends on the region). In this case, they carry a dimension of **320 x 224 pixels** (for NTSC) or **320 x 256 pixels** (for PAL) [@graphics-frame_size].

#### The chipset

The group in charge of drawing on the TV has evolved as new revisions were released. Over the years, SNK has alternated between NEC and Fujitsu to manufacture their integrated circuits.

::: {.subfigures .side-by-side max_subfigures=1}

![The 'LSPC-A2' chip.](vdc/lspca2.jpg){.toleft}

![The 'NEO-B1' chip.](vdc/neob1.jpg){.toright}

In my motherboard revision, these two components make up the Video Display Processor (VDC). Both were produced by Fujitsu.

:::

For explanation purposes, let's start by describing the most notable chips from the set:

- The **Motorola 68000**. You already know it as the main CPU. Its main role is to fill the VRAM and Palette RAM; and set up the VDC (using a set of exposed registers).
- The **Video Display Processor** (VDC): Based on the information stored on multiple memory chips, it generates scan lines and sends them to 'Palette RAM', which will, in turn, generate the required color pixels.
  - The physical appearance of the VDC will vary depending on the motherboard model. For instance, the first revision housed three chips (the 'LSPC-A0', 'NEO-B0' and 'NEO-C0'), whereas later ones use a combination of the 'LSPC-A2' and the 'NEO-B1'.
- The **Video DAC**: Converts the color stream received from 'Palette RAM' into a video signal.

Don't worry if the previous explanation is too dense. All of this will be explained more calmly in the following sections. Now, let's move on to analysing the information these chips operate.

### Organising the content

Graphics data is distributed across **two separate boards**. The first one is the **console's motherboard** and the other one is found in the **game cartridge**. You may recall a similar model implemented in the [NES/Famicom](nes#cartridgegame-data).

![Memory architecture of the graphics subsystem](vdc/content.png)

In the motherboard area, we find:

- **68 KB of VRAM**: Stores sprite attributes and tile references.
  - Physically, VRAM is divided into **4 KB of 'Fast' VRAM** and **64 KB of 'Slow' VRAM**. Each one is connected through a dedicated bus and exhibits drastically different latency. This conditions the number of cycles the CPU must wait after updating its content.
- **Four line buffers**: Store the scan lines rendered by the VDC. They only encode tile data that must be combined with other data in Palette RAM.
- **16 KB of Palette RAM**: Stores the palettes of colours used by the graphics. This block is also tasked with sending pixel colours to the Video DAC.
- **64 KB of 'L0 ROM'**: It so happens the VDC can **shrink sprites**. To carry out this, the 'L0 ROM' stores lookup tables that tell the VDC which scan lines to render based on the zoom value requested. [@graphics-l0_rom].

The respective board in the game cartridge is referred to as **CHA Board** and fits the following:

- Multiple **C ROM** chips: Stores tiles for the Sprite Layer.
- An **S ROM**: Stores tiles for the 'Fix' Layer (I explain this layer in the next section).

### Constructing the frame

Now that you've had a glance at the most important components, let me give you a quick summary of how the graphics subsystem turns data into an image. In other words, how it renders a frame:

1. The CPU fills VRAM with the required references for the sprite layer and Fix layer.
2. Based on the information in VRAM, the VDC fetches graphics from C ROMs and the S ROM; and stores them in the line buffers.
3. While the fetching occurs, the VDC uses the information on the line buffers to latch Palette RAM. This makes the latter send color pixel data to the Video DAC.
4. The Video DAC outputs a video signal to the TV.

Based on these steps, let me give you a thorough explanation using _Art of Fighting 3_ as an example.

#### Tiles {.tabs .active}

![Some tiles found in C ROM.](aof/tiles.jpg){.tab-float .pixel}

Traditionally known as 8 x 8 pixel bitmaps, tiles are the primary ingredient to build the graphics of the 4th generation. Due to its unique attributes, The Neo Geo only draws two types of graphics: **fix tiles** and **sprites**. Thus, tiles are encoded and stored separately depending on the graphic type. In both cases, each pixel is encoded using **4 bits** (in other words, 4 bpp), meaning it can select up to **15 colours plus transparency**.

In terms of colour composition, Palette RAM allows storing **256 colour palettes** [@graphics-palettes]. Each palette is made of sixteen colours referencing **16-bit RGB** values. Among all the available slots, there are two reserved entries: The 'Reference color', which must be hardcoded to `$8000` (pure black); and the 'Backdrop color' which encodes an arbitrary colour that is displayed in the absence of tiles.

The CPU may update Palette RAM at any moment, but it should only do so during blanking periods. Otherwise, a snowing effect will appear.

#### Fixed plane {.tab}

![The Fixed plane with a black backdrop.](aof/fixed_plane.png){.tab-float .pixel}

The Fixed plane is a layer **320 x 256 pixels** (40 x 32 tiles) wide [@graphics-fixed_layer], pretty much the entire screen. Its tiles displayed are completely static and only have access to 16 colour palettes. In practice, this is used to display 'Always on' information, similar to the [window layer](game-boy#tab-3-3-window) of the Game Boy.

When broadcasted to a CRT screen, the displayed area will be smaller. Thus, its [safe area](nes#constructing-the-frame) is 38 x 28 tiles wide.

Fixed tiles are declared in a region of VRAM called **Fix map**. It's in the form of a 40 x 32 table and each entry corresponds to a position on the screen.

S ROM stores the exclusive tiles for the fixed plane in the cartridge. The VDC can only address up to 128 KB, but this limitation can be extended by bundling a [mapper](nes#going-beyond-existing-capabilities) [@graphics-fix_bankswitching].

#### Sprites {.tab}

![The Sprite layer, notice how it's used for all the areas of the scene (not just the two characters).](aof/sprites.png){.tab-float .pixel}

As you may know, sprites are **freely moving tiles**. Yet, in the case of the Neo Geo, the tiles used for sprites are exceptionally **16 x 16 pixels** wide. With that in mind, the VDC can compose sprites made of **16 x 16 pixels** (1 x 1 tiles) to **16 x 512 pixels** (1 x 32 tiles). The reason for providing strangely tall compositions is because any sprite can also be **combined horizontally** by 'attaching' them to the previous one (though they still count as separate sprites). Overall, there's a limit of **96 sprites per scan-line** and **381 per frame** [@graphics-sprites], a large number compared to its competitors.

In terms of effects, sprites can be **flipped** and/or **shrank**. These attributes are stored in the 'fast' block of VRAM, allowing the CPU to update them without considerable latency.

Due to their complexity, sprites are encoded in four areas in VRAM called **Sprite Control Blocks** (SCB), each one stores the following attributes [@graphics-beginners]:

- **SCB1**: Tile reference, colour palette reference and X/Y flip.
- **SCB2**: Horizontal and vertical shrink.
- **SCB3**: Y Position, vertical size (in terms of tiles) and if the sprite is attached to the previous one.
  - Sprite attachment will make the second sprite inherit the predecessor's attributes, except for the horizontal shrink.
- **SCB4**: X Position.

As you can see, for practical reasons, the last three blocks are stored in fast VRAM, while the first one is stored in slow VRAM [@graphics-vram].

#### Result {.tab}

![Resulting frame shown on the screen.](aof/result.png){.tab-float .pixel}

While [competitors](nes#constructing-the-frame) are known for rendering at the pace of the CRT beam, the Neo Geo implements a **line buffer rendering** system. With the latter, the console stores the rendered scan lines in the aforementioned buffer, instead of beaming them directly to the screen. Since the VDC incorporates enough memory for two scan lines, this allows the VDC to render one scan line while displaying the other one, thereby enabling games to update graphics during **active display** (aside from [H-Blank](game-boy#tab-3-5-result) and [V-Blank](nes#tab-5-5-result)).

Truth to be told, the VDC will still be accessing VRAM at the same time, so the CPU must wait between 12 and 16 cycles [@graphics-vram] (depending on the operation) before accessing VRAM again, otherwise the next operation will be ignored (hence the use of two VRAM variants).

#### Broadcasting the frame {.tabs-close}

To broadcast the rendered scan lines, the process varies depending on the console variant (AES or MVS).

The home model adds a video encoder to generate the composite and RGB signals. The A/V connector is somewhat similar to the [Master System](master-system#video-output) and [MegaDrive's](mega-drive-genesis#video-output) but not interchangeable.

There's nothing of the sort in the MVS model, as the JAMMA protocol leaves that task to the cabinet's monitor. So the motherboard sends a raw RGB + sync signal.

## Audio

Remember the Z80 CPU I mentioned at the start of the article? It's still the typical **4 MHz** CPU you've seen in many other consoles and microcomputers, but now it's got the privilege to drive a very versatile sound chip: The **Yamaha YM2610**.

![The Yamaha YM2610 paired with the YM3016 below. Both chips are needed to output audio.](audio/yamaha.jpg)

The YM2610 is another [FM synthesiser](mega-drive-genesis#tab-8-1-yamaha-ym2612) from the popular Japanese manufacturer. It's closely similar to the [YM2612](mega-drive-genesis#tab-8-1-yamaha-ym2612) featured in the [Mega Drive](mega-drive-genesis#audio), but don't let its name fool you because the Neo Geo one is a bit more premium (albeit with some compromises).

Both the YM2610 and YM2612 are part of the high-end 'OPL' series, meaning Yamaha enables four operators per FM channel. Now, the Neo Geo's chip features **four FM channels** which, to be fair, is two less than the YM2612. That's as far as the weaknesses go.

The FM channels are provided with a **Low-frequency Oscillator** (LFO) to modulate either the amplitude or frequency of the operators. The LFO contains a pre-defined set of frequencies ranging from 3.98 Hz to 72.2 Hz [@audio-fm].

![Metal Slug 2 (1998), showing the FM and ADPCM channels used for music.](metalslug){.open-float video="true"}

Now, even though the YM2610 is known for its FM synthesis, it houses additional functions that won't go unnoticed by music composers [@audio-app_manual2]:

- A **Software-controlled Sound Generator** (SSG): This is Yamaha's name for a [Programmable Sound Generator](nes#audio) (PSG). In essence, the chip bundles a legacy Yamaha YM2149 inside, this can generate **three square waves** with optional **noise**. It also comes with a 16-bit envelope control.
  - The original YM2149 came with an interface called 'I/O ports' to transfer data between the CPU and another component [@audio-ym2149]. Since this is now irrelevant, the embedded variant in the YM2610 doesn't include it.
- Seven **ADPCM channels**: Enables to play **samples** using the 4-bit ADPCM format. These channels are split into two groups:
  - ADPCM-A: Made of six channels, they provide a sample resolution of **12-bits** and a sampling frequency of **~18.5 kHz**
  - ADPCM-B: This is the remainder channel with a **16-bit** resolution and a sampling frequency of up to **~55.56 kHz** (better than CD quality).

{.close-float}

The YM2610 is connected to two memory chips in the cartridge called **V ROMs**. These store the ADPCM samples and there's one V ROM per ADPCM group.

All in all, the audio subsystem is a combination of the 3rd, 4th and (then unreleased) 5th generation of consoles. However, unlike the [CD medium](sega-saturn#the-compact-disc-cd), Neo Geo cartridges can only store so much, so the ADPCM channels are mainly bottlenecked by storage limitations. Consequently, adoption was disparate, some games found clever uses for ADPCM-B while others limited it to effects, voices and percussion [@audio-dissection].

Finally, the YM2610 is paired with another chip to work, the **Yamaha YM3016**. The latter is a dedicated 16-bit **Digital-to-Analogue Converter** (DAC), which takes the digital signal output from the YM2610 and converts it to analogue audio (that speakers understand).

### The conductor

For the audio chip to do anything meaningful, the Z80 must command it properly. So, the Z80 runs a program stored in yet-another distinct chip called **M1 ROM**. This is found inside the game cartridge, particularly, within the CHA Board. The program for the Z80 is often referred to as **sound driver**.

![The Zilog Z80, NEO-D0 and surroundings.](audio/z80.jpg)

There were many sound drivers found in the market, some focused on programming all audio channels, while others even ventured into mixing audio samples to overcome the seven-channel limitation [@audio-drivers]. Curiously enough, this is something that some Mega Drive games also [experimented with](mega-drive-genesis#cracking-sampling).

On a similar note, the Z80 is paired with a proprietary controller called **NEO-D0**, the latter acts as a memory bank and I/O controller.

## Operating system

There's a **128 KB 'System' ROM** found on the motherboard [@operating_system-bios_rom], this serves as the typical **BIOS**, providing:

- A **bootloader**, to initialise the hardware, perform a self-test and bootstrap the splash animation.
- A **configuration shell** (only on MVS models).
- Many **system routines** that abstract hardware operations.

### The visual services

After powering on the console, the user will notice a splash screen known as the **Eyecatcher** [@operating_system-eyecatcher]. Its code is stored in the System ROM, but the graphic assets (the Neo Geo logo and other labels) are fetched from the cartridge's C ROM and S ROM.

![The Eyecatcher. It relies on tiles supplied by the cartridge. This is why you also see an additional 'Max 330 Mega', here the game is trying to show off the storage capacity of the cartridge.](system/eyecatcher.jpg){.toleft .pixel}

![The Test Menu, showing many maintenance functions required to set up the arcade cabinet.](system/testmenu.jpg){.toright .pixel}

The MVS system also provides a **Test Menu** to configure the cabinet (i.e. adjust the calendar, test the components, etc.). This menu is triggered by flipping one of the DIP switches of the MVS (inaccessible to the end user). Consequently, its motherboard bundles an S ROM that stores the Fix tiles for this menu.

### The System routines

Apart from the visual aspects, the System ROM provides software routines to operate the hardware, this includes:

- Getting the button presses from the controllers and, in the case of the MVS, the coin slots.
- Access the Memory Card.
- Initialise VRAM with the proper structure for the sprite blocks and the Fix tile map.

Interestingly enough, system routines may, in turn, call game routines to continue some operations. This means games are also tasked with implementing certain calls [@operating_system-user_routine] [@operating_system-system_io]. For instance, the System ROM may request the following from games:

- Show the game's boot animation.
- Start the game demo. This is what you see before inserting a coin.
- Play the 'coin inserted' sound.
- Begin the game after enough coins are inserted.

As you can see, most of the low-end I/O is delegated to the System ROM, games instead focus in providing their unique content.

## Games

To start with, games are written in **68k** and **Z80 assembly**. It will be during the next generation of consoles (with the arrival of [new CPUs](playstation#tab-1-1-a-bit-of-history)) that programming languages will become part of the toolbox.

### Distribution medium

Following the trend of the 4th generation, the Neo Geo relies on cartridges as its only medium for games. 

Nevertheless, I have to say Neo Geo cartridges are the biggest ones I've seen for a home console. This is because of the amount of dedicated chips housed. Unlike the [Super Nintendo](super-nintendo), whose games could [optionally expand](super-nintendo#beyond-convention) the capabilities of the console, Neo Geo games are strictly required to bundle a considerable amount of circuitry.

![Generic cartridge for the Neo Geo [@photography-amos]. Notice the two boards sticking out.](cartridge.png){.no-borders}

I already mentioned some parts of the cartridge system in previous sections, but let me give you a quick summary so you get a proper glance at how this system works.

As you may know, Neo Geo cartridges are made of **two boards**. The first one is called **PROG Board** and houses the following ROM chips:

- **P ROM**: Stores the program the 68000 executes. It's connected with a 16-bit data bus and up to **2 MB** can be addressed (more will require a mapper)
- **V ROM**: Stores ADPCM audio samples read by the YM2610.

By contrast, the second board is the aforementioned **CHA Board** and embeds the following:

- The **C ROMs** and **S ROM**. These store sprite tiles and Fix tiles, respectively.
- An **M1 ROM**: Stores the Z80 program. As you may know, the addressing capabilities of the Z80 are [very limited](master-system#memory-available), so M1 ROM access is interfaced by a dedicated controller called **NEO-ZMC** (another chip on the cartridge) which provides [memory banking](nes#going-beyond-existing-capabilities), enabling up to **64 KB** to be addressed (though it may be expanded by adding another mapper into the CHA Board).

### Keeping progress

As a home-arcade hybrid, SNK provided a rudimentary accessory to keep track of scores and progress: a proprietary **Memory Card**

![The Memory Card [@photography-amos].](memorycard.png){.no-borders}

The Memory Card worked on both AES and MVS models. Options ranged from **2 KB** to **16 KB** of battery-backed SRAM [@games-memory_card], though in all cases the battery was unserviceable.

The save data was structured in the form of 64 B blocks distributed across five segments, while the System ROM provided I/O routines to operate it.

Having said that, whether arcade cabinets were actually 'prepared' for Memory Cards... That was another story.

> I knew about the Memory cards from magazines, but official Neo Geo cabinets were rare in my town. Instead, we had plenty 'Video Sonic' cabinets supplied by Segasa. Some lacked all the buttons required to play properly, let alone a Memory Card slot!
> 
> -- <cite>Proud Neo Geo connoisseur from Jerez, Spain</cite>

## Anti-piracy & Homebrew

Both consoles and games were a target for unauthorised parties, many of whom ended up producing their own replicas [@anti_piracy-bootlegs]. Bootlegged hardware was made from a combination of off-the-shelf and reverse-engineered chips, aiming to sell a cheaper alternative to SNK's hardware.

Thus, on one side, you've got SNK trying to fend off bootleggers. On the other, game studios were enforcing that their code only ran on a genuine console and cartridge.

### Combating fake consoles

Neither SNK nor game studios favoured running on bootlegged hardware (whether it's a Neo Geo clone or a bootleg cartridge). Hence, many games bundled routines that fiddle with very particular areas of the motherboard (i.e. status flags, the real-time clock, the Watchdog, etc.) in an attempt to spot any defects from clone boards [@anti_piracy-slot_checks]. Whenever any of these checks failed, the game would display a warning message and refuse to proceed.

### Fighting fake cartridges

Considering cartridges housed an exceptional amount of integrated circuits, games implemented creative measures to combat piracy.

Starting with the common techniques, the System ROM contains a portion of data called **Security Code** that is compared side-by-side with the cartridge's P ROM during boot [@anti_piracy-security_code]. The bootloader will proceed only if both areas match.

Additionally, games like _Fatal Fury 2_ also fiddle with its companion 'PRO-CT0' chip (a multiplexer for the CHA Board) to make sure the program is residing in an official cartridge [@anti_piracy-fatal_fury]. Otherwise, it assumes it's been cloned, triggering many anti-piracy **unpleasantries** (such as making the opponent invincible).

Later on, newer entries also stored **encrypted data** in C ROM, designed to be seamlessly decrypted by the 'NEO-CMC' (found on the CHA Board) [@anti_piracy-neo_cmc]. This chip contained a set of unusual XOR functions to deobfuscate the graphics data before it reached the VDC [@anti_piracy-mame_cmc].

For extreme measures, games like 'Metal Slug X' bundled specialised ICs in their PROG board that complicated things further [@anti_piracy-progeop]. They behaved similarly to the [Nintendo's CIC system](nes#anti-piracy-and-region-lock), requiring constant game-chip communication.

All in all, this tells you that the anti-piracy battle was just another cat-and-mouse game. However, in this case, SNK was trying to get both home and arcade owners away from cheaper knock-offs.

### Post-market utilities

Once the lifecycle of this console reached an end, a new phase emerged: **Homebrew** (running homemade software and/or hardware). At the time of this writing (June 2024), the most common practices are **hardware modifications**, **firmware replacement** and **Flashcarts**.

Thanks to its enthusiastic community, there are many online resources available that teach how to correct and/or modify MVS and AES hardware. These include converting MVS models to work outside cabinets (the so-called **consolisation**) while others focus on tackling deficiencies in particular models. For instance, the AES model I own had its video encoder bypassed, improving the quality of the RGB output.

On the firmware side, there have been developments like the 'UNIVERSE BIOS'/UniBIOS. This is the equivalent of a **custom firmware** made to replace the original System ROM. Behind the scenes, the UniBIOS is made of an MVS' System ROM modified to provide the additional abilities [@anti_piracy-unibios]:

- Swap between 'MVS' and 'AES' mode. This is very useful for AES systems as games have a hardcoded number of coins/lives, and switching to MVS mode enables to increase this amount arbitrarily.
- Change the region. Some games are censored depending on the region they run on.
- Activate cheats.

Finally, to run arbitrary code in this console, stores are selling third-party flashcarts which can reprogram itself to replicate any past CHA and PROG boards. Due to their complexity, they're not exactly affordable, but that's another product Neo Geo owners have available nowadays.

## That's all folks

![My Neo Geo AES. I bought it out of curiosity because the seller was offering a complete set with a Flashcart and two controllers. Half a year later it became a good excuse to write an article about it.](myneogeo.webp)

Curiously enough, the last Neo Geo game was released in 2004, almost 14 years after its release. That's quite an achievement for a household/arcade console, and I hope that this analysis has helped you understand these outcomes from a technical perspective.

From my side, I found it engaging to return to a 2D console after spending so many words on the [Nintendo 3DS](nintendo-3ds), this helped me revisit the 'classic' writing structure that I adopted during the website's beginnings in 2019.

Thinking about the follow-up articles, I've still got two avenues to explore: Continue the modern hardware timeline after the [Nintendo 3DS](nintendo-3ds), or proceed with another 2D console: The Pippin. The latter would help me introduce the PowerPC CPU, while modern hardware would enable me to talk about the ARMv7 and the ARMv8. We'll see!

Until next time!  
Rodrigo
