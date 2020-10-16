---
name: PlayStation
subtitle: A promising newcomer
date: 2019-08-08
releaseDate: 1994-12-03
generation: 5
cover: ps1
javascript:
  - 'threejs'
top_tabs:
  Model:
    caption: "The original Playstation. Released on 03/12/1994 in Japan, 09/09/1995 in North America, 29/09/1995 in Europe"
    file: "international"
  Motherboard:
    caption: "Showing model 'SCPH-1000'. Remaining DRAM is fitted in the back. Later models included SG-RAM instead of VRAM and removed most of the I/O"
  Diagram: { }
# SEO
title: PlayStation Architecture
---

## A quick introduction

Sony knew that 3D hardware can get very messy to develop for, for this reason, their new console will keep its design *simple* and *practical*... Although this may come with a cost!

---

## CPU

The main processor is a modification of LSI's **CoreWare CW33300** which, at the same time, is binary-compatible with SGI's **MIPS R3051**. This chip runs at 33.87 MHz and it features:
- The **MIPS I** ISA: A 32-bit RISC instruction set, it includes multiplication and division instructions.
- **One ALU and one shifter**.
- **5-stage pipeline**: Up to five instructions can be executed simultaneously (a detailed explanation can be found in a [previous article]({{% ref)).
- **4 KB instruction cache** and **1 KB of data cache** (the original CoreWare CW33300 contained 2 KB of data cache): The data cache is actually **Scratchpad RAM**, meaning that it can have other uses apart from behaving as L1 cache.

Like other MIPS R3000-based CPUs, it supported configurations with up to four coprocessors, Sony customised it with two:
- **System Control Coprocessor** or 'COP0': A MMU that provides virtual memory by using a Translation Lookaside Buffer or 'TLB'. Not all the features that come with virtual memory are available though, for example, memory protection is disabled since games are programmed for bare-metal (without running from an OS). On the other hand, this MMU still gives interrupt support, exception handling and breakpoints, these are used for debugging.
- **Geometry Transformation Engine** or 'GTE': A special math processor used for vector and matrix calculations. While only containing a fixed-point unit, it still provides useful operations for 3D graphics such as lighting and polygon transformations.

Additionally, there's an extra processor found in the main CPU:
- **Motion Decoder** or 'MDEC': Provides **full motion video** playback (known as 'FMV') and transfers the decoded frames to the GPU using DMA.

#### Taking over the CPU

Sometimes any subsystem (graphics, audio or CD) may require large chunks of data at a fast rate, however the CPU may not be able to *always* keep up with the demand.

For this reason, the CD-ROM Controller, MDEC, GPU, SPU and the Parallel port have access to an exclusive **DMA controller** that takes control of the main bus whenever they require it.

---

## Graphics

A large part of the graphics pipeline is carried out by the GTE. This includes perspective transformation, which projects the 3D space onto a 2D plane using the camera's perspective. The processed data is then sent to Sony's proprietary GPU for rendering.

#### Drawing the scene

If you've been reading the [Sega Saturn article]({{< ref "sega-saturn">}}), let me tell you that the design of this GPU *a lot* simpler!

Insomniac's *Spyro: Year of the Dragon* will be used as example to show how a scene is drawn.

{{< tabs >}}

  {{< tab name="Models" active="true" >}}

{{< float_block >}}
  {{< linked_img src="spyro/wireframes.png" alt="Models" >}} <figcaption class="caption">Primitive view of the scene</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
To start with, the GPU uses **triangles as primitives** to form 3D models. Being the only available primitive means that backgrounds and foregrounds make no difference in terms of composition (both are made of triangles). 2D games inherit the same nature: They are just flat polygons (two triangles joined to form a quadrangle). The GPU also includes routines for handling quadrangles as sprites.


The CPU sends geometry data to the GPU by filling its internal 64 byte FIFO buffer with **commands** (up to three). Basically, a command states how and where to draw one primitive. Once the geometry is received, **clipping** is applied to skip operations over unseen polygons (residing outside the camera's viewport).

Compared to the more complex [Sega Saturn]({{< ref "sega-saturn">}}), the GPU only requires a **single frame-buffer**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Shaders" >}}

{{< float_block >}}
  {{< linked_img src="spyro/shaders.png" alt="Shaders" >}} <figcaption class="caption">Gouraud shading in action</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
In order to apply lighting effects over these polygons, the system provides two algorithms:

- **Flat shading**: Each primitive has a constant light level.
- **Gouraud shading**: Each primitive's vertex has its own light level, then the brightness between each point is automatically interpolated. As you can imagine the results are more realistic. On the other hand, this algorithm is not available for sprites.

The reason of having this choice comes down to the fact that Flat shading provides ~2.5 times more polygons per second than Gouraud, so it's important to optimise which polygons need a more realistic shading than others.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Textures" >}}

{{< float_block >}}
  {{< linked_img src="spyro/result.png" alt="Textures" >}} <figcaption class="caption">Textures applied (*Tada!*)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Finally, shaded surfaces are blended with texture maps to produce the final result.

The GPU performs a very *inexpensive* routine called **Affine Texture Mapping** to stamp textures on our polygons. This technique only operates using 2D coordinates (X/Y values) while discarding the third coordinate used for perspective (Z-value).

The unit also includes the following effects available to use:

- **Semi-Transparency**: Simulates light passing through multiple textures.
- **Dithering**: Soften sudden changes in colour while adhering to the same colour palette.
- **Fog**: Simulates smoke in the environment.

It's worth mentioning that the PS1 happened to excel at those effects!
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Designs

Here are some examples of game characters designed from the ground up for the 3D era, they are interactive so I encourage you to check them out!

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="spyro" >}}
    <figcaption class="caption">Spyro the Dragon (1998)
    <br>413 triangles</figcaption>
  </div>

<div class="toright canvas-model">
    {{< threejs_canvas model="crash" >}}
    <figcaption class="caption">Crash Bandicoot (1996)
    <br>732 triangles</figcaption>
  </div>
{{< /side_by_side >}}

#### Playing with VRAM

The system features 1 MB of VRAM that will be used to store the frame-buffer, textures and other resources the GPU will need to access almost instantly. In theory, with the available amount of VRAM one could allocate a *massive* frame-buffer of 1024×512 pixels with 16-bit colours or a *realistic* one of 960×512 pixels with 24-bit colours allowing to draw the best frames any game has ever shown... This sounds pretty impressive right? Well, it does raise a couple of issues, for instance:
- Those dimensions will have to be rescaled to follow a standardised definition so the video encoder can broadcast it to conventional TVs.
- How is the GPU going to be able to draw anything in time if there is no space left for the rest of materials (i.e.textures, colour tables, etc)?
- The PS1's GPU can only draw frame-buffers with up to 640×480 pixels and 16bpp colours.

Alright, so let's have a 16 bpp 640x480 buffer instead, which leaves 424 KB of VRAM for materials. So far so good? Again, such resolution may be fine on CRT monitors, but not really noticeable on those 90s TVs everyone had at their homes. Then, is there any way to optimise the frame-buffer? Introducing **adjustable frame-buffers**.

In essence, instead of wasting valuable VRAM by using 'unappreciated' resolutions, this console's GPU allows to decrease the dimensions of the frame-buffer to effectively increment the space available for other resources.  
A common setup consisted in dividing the 640x480 frame-buffer into two 320x480 ones, then using a technique called **page-flipping** to render multiple scenes at the same time. Page-flipping consists in switching the location of the frame for display between the two available whenever the game wants it, allowing the game to render one scene while displaying another, thus hiding any flickering effect and improving loading times (something that the player will certainly appreciate!).

Overall, our suggested layout only consumes 600 KB of VRAM. The rest (424 KB) was often used to store colour lookup-tables and textures that, combined with **2 KB of texture cache available**, results in a very convenient and efficient setup.

Finally, it is worth mentioning that VRAM can be mapped using **multiple colour depths simultaneously**, meaning that we could allocate a 16 bpp frame-buffer next to 24 bpp bitmaps (e.g. FMV frames). This is another feature facilitating further optimisation of space.

#### Secrets and Limitations

Whereas the PS1 had a very simple and suitable architecture, problems ended up arising anyway. Surprisingly, certain issues were tackled with very clever workarounds!

{{< tabs >}}

{{< tab active="true" name="Wobbling textures" >}}

{{< float_block >}}
  {{< video src="ffviii" >}} <figcaption class="caption">Textures jiggle a bit while they move <br>Square Soft's Final Fantasy VIII (1999)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The routines used for handling geometry and applying textures were known to carry some inaccuracies:

- GPU coordinates are **limited to fixed-point units**: That means no decimals (thus no precision), contributing to sudden jumps when polygons move slightly.
- As you know, affine transformations have **no sense of depth**, which may confuse the user's perception when the camera is close to the model and is positioned perpendicular to the viewer.
- Finally, the smallest size of a polygon in this system **is a pixel**, making small polygons (no matter how small) to be rendered as a single pixel. Now, this means that textured polygons may lose part of their texture if their size becomes smaller than a pixel. Furthermore, complex models may suffer from edges constantly shaking.
  - This effect is more apparent when using a small frame-buffer or rendering models at a distance. Modern systems tackled this by implementing sub-pixel resolution methods, which handles polygons using fractions of pixels.

These explain why users may notice instabilities while playing some games. The effect is also referred as **texture warping**, some games often resorted to **tessellation** (dividing a big polygon into smaller ones) in order to reduce these unwanted effects, others just reduced the amount of textures in favour of **solid colours**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Visibility approach" >}}

{{< float_block >}}
  {{< linked_img src="crash.jpg" alt="Crash Bandicoot" >}} <figcaption class="caption">This game used pre-ordered geometry to gain performance <br>Hence its camera only moves backwards or forwards <br>Crash Bandicoot (1996)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Just like the [competition]({{< ref "sega-saturn" >}}), the PS1 doesn't include any hardware feature that solves the [}}#an-introduction-to-the-visibility-problem">visibility problem]({{< ref). Nonetheless, the GPU supports sorted polygons by providing an **ordering table**: A dedicated table where each entry is indexed using a depth value (also called 'Z-value') and contains the address where the GPU command resides.

The CPU needs to manually sort the polygons first, and then reference them in the correct entry of the table, which will be handled by the GPU. This process will enable the GPU to render the geometry in the correct order.

Multiple DMA functions are provided to assist the CPU/GPU with the creation and traversal of this table.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Pre-rendered graphics" >}}

{{< float_block >}}
  {{< linked_img src="prerendered.png" alt="Pre-rendered background" >}} <figcaption class="caption">Scene with Pre-rendered background <br>The way models interact and move is essential to *trick* the user's perception <br>Square Soft's Final Fantasy VII (1997)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
If a game strived for a more realistic scenery than the GPU could provide, one available option was to stack two triangles and use the Motion Decoder to feed **pre-rendered cinematic** on them. FMV video could weight a lot of space, luckily the CD-ROM was prepared for this.

Some games relied on it specifically for composing backgrounds and, honestly, it was quite convincing to see them in a CRT TV, obviously modern emulators with upscaling capabilities will tell on very quickly.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Audio

The **Sound Processing Unit** or 'SPU' takes care of this. It supports the enormous amount of **24 channels** of 16-bit **ADPCM samples** (a more efficient version of the well-known PCM sampling) with a sampling rate of 44.1 KHz (Audio CD quality).

This chip also provides the following capabilities:

- **Pitch modulation**: As the name suggests, games can automatically alter the pitch of their samples instead of needing to store extra ones. Useful for music sequencing.
- **ADSR Envelope**: These are a set of properties available for sound modulation.
- **Looping**: This allows to instruct the system to play a piece of audio repeatedly.
- **Digital reverb**: Simulates the sample being played inside an specific atmosphere in order immerse the player.

512 KB of SRAM are provided as audio buffer. This memory is accessible from the CPU and CD controller.  
Games only have 508 KB available to store samples, the rest is reserved by the SPU to process Audio CD music. This amount is reduced even further if reverb is activated.

---

## Games

Similarly to the Saturn's boot process, after turning this console on, it will:
1. Execute a small OS found in the BIOS ROM.
    1. Initialise the hardware.
2. Display the splash screen.
3. If there is a CD inserted, the CD-ROM controller will check if it's genuine:
    - **It is**: The controller will allow to read its content.
        1. The CPU will look for 'SYSTEM.CNF' and continue execution from there.
    - **It's not**: The CPU will display an error message.
4. With no CD inserted, the CPU will open the memory card manager, the user is now in control.
    1. Inside the manager there's an option to open the CD audio player.

Games have all the facilities that the CD medium provides: Large storage (640 MB), good audio quality and a 'not-so-slow' read speed thanks to the 2x drive. Additionally, there are two I/O ports (**Serial** and **Parallel**) available for add-ons. However, these were removed in later revisions of the console due to lack of adoption and the fact that they could potentially be used to crack the copy protection system.

The SDK provided C libraries which used **BIOS routines** to access the hardware. This is the main factor that helped to emulate the PS1 on a wide range of platforms.

Along with the SDK, Sony also distributed specialised hardware like the **DTL-H2000**, a dual-slot ISA card containing the internals and I/O of the PS1, plus extra circuitry for debugging purposes. The board has access to the host's hard drive and can execute PS1 software without restrictions. Software and drivers used to communicate with the card ran on PCs with Windows 3.1 or 95.

#### I/O design

The port of the controller and the Memory Card are electrically identical so the address of each one is hardcoded, Sony altered the physical shape of the ports to avoid accidents.

Communication with these devices is accomplished using a serial interface. Commands sent from the console will be delivered to one of the two slots (either 'mem. card 0' and 'controller 0', or 'mem. card 1' and 'controller 1'). Then, both accessories will answer with their unique identifiers, this will allow the console to focus on a particular type of device (memory card or controller) from now on.

---

## Anti-piracy / Region Lock

Like any other optical media, in order to fetch data from a CD, a laser beam is used to read the **pits** (zeroes) and **lands** (ones) from the track of the disc. Now, conventional discs are not 100% flat and they often have tiny fluctuations in their tracks. These *defects* are completely unnoticeable while reading the data since lasers can automatically calibrate themselves as they read.

This is what Sony based their copy protection on: The CD reader of the PS1 will only accept discs whose specific region (sector 4) is engraved using a defined frequency informally known as **Wobble Groove**, which is only applied during mastering and cannot be replicated through conventional burners. This sector in particular will contain one of the following set of characters (each representing a different piece of information):
- **SCEA** → Sony Computer Entertainment of America.
- **SCEE** → Sony Computer Entertainment of Europe.
- **SCEI** → Sony Computer Entertainment of Japan.

As you can imagine, the reader applies **region-locking** using this technique as well.

On the other side, this check is only executed once at the start, so manually swapping the disc just after passing the check can defeat this protection... with the risk of damaging the drive.  
In any case, later games took matters into their own hands and often reinitialised the drive in-game so the check would be executed again, in an effort to prevent users from performing this trick.

Alternatively, tiny boards programmed to mock the wobble signal could be soldered in the console. These boards are known as **Modchips** and, while legally questionable, were incredibly popular.

The use of emulators were seen as a threat for publishers as well. As a result, some games included their own checks (mostly checksums) to combat any type of unauthorised use or modification.  
One of the checks I was told consisted in deliberately reinitialising the drive and then making it read specific sectors that would not pass the wobble groove check. If this managed to unlock the drive anyway, the game (still residing in RAM) would *happily* reveal its anti-piracy material. Notice that this approach can also affect modded consoles using genuine games.

---

## Sources / Keep Reading

#### General

- [**Technical Specifications (Wikipedia)**](https://en.wikipedia.org/wiki/PlayStation_technical_specifications)
- [**Unofficial (yet very complete) development manual**](http://hitmen.c02.at/files/docs/psx/psx.pdf)
- [**Video with more details about the architecture**](https://www.youtube.com/watch?v=MPXpH2hxuNc)
- [**Different revisions of the console**](https://en.wikipedia.org/wiki/PlayStation_models)

#### CPU

- [**Source code of emulator**](https://github.com/mamedev/mame/blob/master/src/devices/cpu/psx/psx.cpp)

#### Graphics

- [**Retrocomputing thread discussing the reasons 3D models jiggled**](https://retrocomputing.stackexchange.com/questions/5019/why-do-3d-models-on-the-playstation-1-wobble-so-much)
- [**More examples of unstable polygons**](https://www.youtube.com/watch?v=nqw2HMUrNiA)
- [**The Models Resource** (Archived)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)
- [**Making of Crash Bandicoot, written by the original developers**](https://all-things-andy-gavin.com/2011/02/02/making-crash-bandicoot-part-1/)

#### Games

- [**More about the Sony DTL-H2000** (Archived)](http://web.archive.org/web/20181119231457/http://www.psxdev.net/forum/viewtopic.php?t=103)

#### Copy protection

- [**'Wobble groove' in detail**](https://www.youtube.com/watch?v=XUwSOfQ1D3c)
- [**Open-source modchip using an Arduino**](https://github.com/kalymos/PsNee)

#### Photography

- Motherboard and console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Diagrams and game screenshots: **Me**