---
name: Nintendo DS
date: 2020-08-11
subtitle: Novel forms of interaction
releaseDate: 2004-11-21
cover: "nintendods"
javascript:
  - 'audioswitcher'
  - 'plyr'
  - 'threejs'
generation: 7
top_tabs:
  Models:
    Original:
      caption: "The original Nintendo DS (Blue edition)\n<br>Released on 21/11/2004 in America, 02/12/2004 in Japan and 11/03/2004 in Europe"
      active: true
    Lite:
      caption: "The Nintendo DS Lite (Black edition)\n<br>Released on 02/03/2006 in Japan, 11/06/2006 in America and 23/06/2006 in Europe"
  Motherboard:
    caption: "Showing first revision"
  Diagram:
    caption: "If you have trouble following the components: Top is only accessed by ARM9, bottom section is ARM7-only, middle section is shared"
# SEO
title: Nintendo DS Architecture
---

## A quick introduction

This console is an interesting answer to many needs that weren't possible to fulfil in the handheld ecosystem. There will be some innovation and a few compromises, but this combination may pave the way for new and ingenious content.

---

## CPU

As with Nintendo's [previous portable console]({{< ref "game-boy-advance">}}), the system revolves around a big chip named **CPU NTR**. 'NTR' is shorthand for 'Nitro', the codename of the original Nintendo DS.

Now, CPU NTR implements an interesting multi-processor architecture using two different ARM CPUs, this design was done before ARM Holdings officially released multi-processor solutions. So, their functioning may be considered a bit unorthodox taking into account the present technology available.

While this is not the first parallel system analysed for [this series]({{< ref "consoles">}}), its design is very different from the rest. For instance, we are not talking about the 'experimental' master-slave configuration that the [Saturn]({{< ref "sega-saturn">}}) debuted or the 'co-processor' approach found on the [PS1]({{< ref "playstation">}}) or [N64]({{< ref "nintendo-64">}}). The Nintendo DS includes two very independent computers that will perform exclusive operations, each one having a dedicated bus. This co-dependency will condition the overall performance of this console.

That being said, let's take a look now at the two CPUs:

{{< tabs >}}

{{< tab name="ARM7TDMI" active="true" >}}

{{< float_block >}}
  {{< linked_img src="cpu/arm7_core.png" alt="ARM7 Diagram" >}} <figcaption class="caption">ARM7 structure and components</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Starting with the more familiar one, the **ARM7TDMI** is the same CPU found on the [}}#cpu">GameBoy Advance]({{< ref) but now running at **~34 MHz** (double its original speed). It still includes all its original features (especially [}}#whats-new">Thumb]({{< ref)).

Now for the changes: Because Nintendo's engineers placed the ARM7 next to most of the I/O ports, this CPU will be tasked with arbitrating and assisting I/O operations. In fact, no other processor can directly connect to the I/O. As you can see, this is not the 'main' processor that will be in charge of the system, but rather the 'sub-processor' offloading the main CPU by passing data around many components.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="ARM946E-S" >}}

{{< float_block >}}
  {{< linked_img src="cpu/arm9_core.png" alt="ARM9 Diagram" >}} <figcaption class="caption">ARM9 structure and components</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Here is the 'main' CPU of the Nintendo DS running at **~67 MHz**. If you ignore the ill-fated ARM8 series, you could say the ARM946E-S is the 'next-gen' version of the ARM7. Part of the **ARM9 series**, this core in particular not only inherits all the features of the **ARM7TDMI** but also includes some additional bits:

- The **ARMv5TE ISA**: Compared to the previous v4, features some new instructions and a faster multiplier.
  - If you take a look at the core name, the letter 'E' means **Enhanced DSP** which implies that lots of these new instructions have to do with applications for signal processing.
- **5-stage Pipeline**: This is another increment from the previous 3-stage pipeline.
- **12 KB of L1 Cache**: The core now features cache, where 8 KB are allocated for instructions and 4 KB for data.
- **48 KB of Tightly-Coupled Memory** or 'TCM': Similar to [}}#cpu">Scratchpad memory]({{< ref), however this one discriminates between instructions (32 KB) and data (16 KB).

Nintendo also added the following components around it:
- A **Divisor and Square root unit** to speed up these type of operations (the ARM9 by itself is not capable of performing this type of math).
- A **Direct Memory Access Controller**: Accelerates memory transfers without depending on the CPU. Combined with the use of cache, both CPU and DMA can potentially work concurrently.
  - Cache and DMA can provide a lot of performance but also create new problems, such as data integrity. So programmers will have to manually maintain memory consistency by flushing the [}}#preventing-past-mishaps">write-buffer]({{< ref) before triggering DMA, for instance.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

I guess with hardware like this, it's easy to figure out the *real* reason kids loved this console, eh?

#### Interconnection

So far I've talked about how the two CPUs work individually. But to work as a whole, they require to co-operate constantly. To accomplish this, both CPUs directly 'talk' to each other using a dedicated **FIFO unit**, this block of data holds two 64-byte queues (up to 16 elements) for **bi-directional communication**.

{{< centered_container >}}
  {{< linked_img src="cpu/fifo.png" alt="FIFO Diagram" >}} <figcaption class="caption">Representation of FIFO unit</figcaption>
{{< /centered_container >}}

This works as follows: The 'sender' CPU (that effectively needs to send the other a message) places a 32-bit block of data in the queue, the CPU acting as a 'receiver' can then pull that block from the queue and perform the required operations with it.

Whenever there's a value written on the queue, either CPU can fetch it manually (**polling**) however, this requires to constantly check for new values (which can be expensive). Alternatively, an **interrupt unit** can be activated to notify the receiver whenever there's a new value in the queue.

#### Main memory

Just like its predecessor, RAM is spread around many different locations, enabling to prioritise data placement by speed of access. In summary, we have the following general-purpose memory available:

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="cpu/ram.png" alt="RAM Model" >}} <figcaption class="caption">RAM model of this console</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
- **32 KB of WRAM** (Work RAM) using a **32-bit** bus: To hold fast data shared between the ARM7 and ARM9.
  - Bear in mind that only one CPU can access the same address at a time.
- **64 KB of WRAM** using a **32-bit** bus: For fast data as well, but only accessible from the ARM7, like the GBA had.
- **4 MB of PSRAM** using a **16-bit** bus: A slower type, available from either CPU and it's controlled by a memory interface unit.
  - Pseudo SRAM or 'PSRAM' is a variant of DRAM which, by contrast, performs refreshes from within the chip. Therefore, behaving like SRAM (the faster, but more expensive alternative to DRAM). This design reminds me of [}}#clever-memory-system">1T&#8209;SRAM]({{< ref).
{{% /inner_markdown %}}
{{< /float_group >}}

#### Backwards compatibility

Even though the architecture is significantly different from its predecessor, it still managed to maintain the critical bits that would grant it native compatibility with GameBoy Advance games.

But for the DS to revert to an 'internal' GBA, the former includes a set of software routines that set the console in **AGB Compatibility Mode**. In doing so, it effectively halts the ARM9, disables most of the 'special' hardware, redirects the buses, puts the ARM7 in charge and slows it down at 16.78 MHz. Finally, the ARM7 proceeds to boot the original AGB BIOS which bootstraps the GamePak cartridge (just like an original GameBoy Advance). This mode still exhibits some features not found in the original console, such as displaying the game with black margins (we'll see in the next section that the new screen resolution happens to be bigger). Moreover, since the DS has two screens, users can set which screen will be used to display the GBA game.

Once in GBA mode **there's no going back**, the console must be reset to re-activate the rest of the hardware.

#### Secrets and limitations

With so many sophisticated components fitted in a single and inexpensive chip, it's no mystery that some issues emerged due to the way they were forced to work with each other.

{{< tabs >}}

{{< tab name="Unused Power" active=true >}}

{{% inner_markdown %}}
Sometimes I wonder how Nintendo planned the way the two CPU's would be used, and if they already assumed some performance would be hit by the design they chose.

Let me start with the ARM9, this CPU runs at twice the speed of the ARM7, but most (if not all) of the I/O depends on the ARM7, so the ARM9 is vulnerable to excessive stalling until the ARM7 answers. If that wasn't enough, **ARM9's external bus runs at half the speed**, so there are a few bottlenecks identified.

Additionally, the Main Memory bus is only **16-bit wide**. Thus, whenever any CPU needs to fetch a word (32-bit wide) from memory, the interface **stalls the CPU** (up to 3 'wait' cycles) until a full world is reconstructed. The worst impact happens when memory access is not sequential, which makes it stall for every single access. This issue will also arise when instructions are fetched (unfortunately, ARM didn't support sequential opcode fetching back then) which, to my dismay, also affects Thumb code (since every 16-bit fetch is done as a 32-bit block). On the other hand, this penalty (as some sources call it) can be alleviated by making full use of cache and TCM.

All in all, this means that in the worst case, the 'whooping' ARM9's 66 MHz horsepower is practically reduced to a mere ~8&nbsp;MHz. That is if the program makes an abysmal use of cache/TCM.
{{% /inner_markdown %}}

{{< /tab >}}


{{< tab name="A question about the hardware choice" >}}

{{% inner_markdown %}}
Back when I read about the CPU of the GameBoy Advance, I was really surprised by the potential of the ARM7: The CPU not only performed its designated tasks, but could also assist with others, such as providing audio sequencing or pseudo-3D graphics.

Now, during the commercialisation ARM7, ARM Holdings joined forces with DEC to design a high-end version of ARM's chips. For this, DEC grabbed the datapath design of their processor, **Alpha**, and mixed it with ARM's. The result was a new series of CPUs called **StrongARM** which was surprisingly *fast*. At the expense of removing certain features (Thumb and debug), DEC managed to cross the megahertz threshold by reaching speeds of up to 233 MHz. As a normal user prepared to buy a new ARM PC (let's say a *RiscPC*), you could either choose one with the old ARM710 at 40 MHz or another one with a StrongARM running ~582% faster. The impact of StrongARM was so disruptive that ARM Holdings absorbed some of StrongARMs features to produce their next line of CPUs, starting with ARM9. And the rest is history.

But here's where my question resides: Considering the new developments in the ARM world, why did Nintendo ultimately choose an awfully slow ARM9 combined by an even slower ARM7, instead of a faster ARM9 (or even a StrongARM)? To give you an idea, other companies like Apple just adopted the StrongARM with their Newton PDA line.

I don't mean to criticise Nintendo's choice, but I believe the amount of emerging technology was just too great for me to ignore. I guess their choice was done in an effort to preserve battery life and maintain production costs (by using the same CPU found in the GBA).
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

---

## Graphics

This section is a bit unusual because not only this console has multiple screens to draw, but also a combination of traditional tile engines working alongside a modern renderer.

Let's begin with the physical attributes: The Nintendo DS contains two LCD screens, each one being **256x192 pixels** wide, which is ~20% more pixels than the GBA. They can display 262,144 colours (18-bit)and refresh at ~60 Hz.

#### Architecture

The graphics subsystem can draw 2D and 3D objects. The former is composed of two-dimensional geometry that is filled with bitmaps of 8x8 pixels wide (called 'tiles'), while the latter draws three-dimensional objects (quadrangles and rectangles) using vertices.

Diving into the internal chip that operates those screens, we can observe this console has distinctive hardware for 2D and 3D geometry. The 2D data is operated by a familiar engine, the PPU (now just called **2D engine**), while 3D data is handled by a completely new subsystem. It's worth mentioning that while this is not the [first console]({{< ref "nintendo-64">}}) to debut 3D graphics, it's still the first one to include an in-house design to render 3D graphics.

{{< centered_container >}}
  {{< linked_img src="gpu/overall.png" alt="GPU Layout" >}} <figcaption class="caption">Layout of the different graphic units</figcaption>
{{< /centered_container >}}

Now, these engines must be linked to either screen, this not an issue for 2D-only games since there's one 2D engine for each screen. However, for those games who want to show off cutting-the-edge features, there's only one 3D engine available. As a consequence, 3D capabilities are only available on one screen at a time. But what about mixing 2D and 3D objects? Absolutely, let me explain each engine separately so we can discuss this afterwards.

#### Constructing a frame with 2D graphics

Before we review each stage, I recommend reading a [}}#graphics">previous article]({{< ref) about the GBA's PPU since here I'll just mention the changes that construct the 'next-gen' of 2D games. Also, since there are two engines, the first one is named **Main** while the second one is called **Sub**. This doesn't necessarily imply which screen is each one connected to. On the other side, 'Main' contains a bit more functions than 'Sub'.

To help the explanations, this time I'm going to borrow the assets of *New Super Mario Bros*.

{{< tabs >}}

{{< tab name="Tiles" active="true" >}}
{{< float_block >}}
  {{< linked_img class="pixel" src="mario/tiles.png" alt="Tiles" >}} <figcaption class="caption">Some tiles found in VRAM <br>For demonstration purposes, a default palette is used</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
By now we all know how a basic tile system works, but how are tiles particularly managed in this console? Well, there's a total of **656 KB of VRAM** available, and this chunk is split into different banks: Four 128 KB, one 64 KB, one 32 KB and three 16 KB. Programmers are free to fill the banks with drawings and then point the engine where the required data is. Both engines can read from any of these banks, but they can't access the same one concurrently.

Nonetheless, there are some limitations on how data can be distributed. For instance, the ARM7 can only access two 128 KB banks. At the same time, these two banks can't store sprites, and 'sub' is the only one capable of accessing the last 16 KB bank. The list goes on... but you get the idea.

One last thing, the 3D engine, which we will discuss later on, will access some of these banks to fetch textures.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background types" >}}

{{% inner_markdown %}}
Since the days of the [}}#graphics">Super Nintendo]({{< ref), the PPU has been leaning towards providing more flexibility for building background layers. 14 years later, we found ourselves with a chip that can fetch tiles and apply many **affine transformations** and if that wasn't enough, the layer can be ultimately built from a frame-buffer.

Before we discuss the different modes the 2D engine can operate to generate backgrounds, let me show you this list, which specify the types of backgrounds the engine can generate:
- **Character type group**: These background types follow the traditional tile system, the frame is rendered by filling it with tiles.
    - **Static** or 'Text' background: An ordinary background. Up to 512x512 pixels wide, 256 colours and 16 palettes. It includes all the typical [}}#tab-1-2-background">effects]({{< ref) (H/V flipping, H/V scrolling, mosaic, alpha blending) plus an extra 'fading' effect. Up to 1024 tiles can be used.
    - **Affine** background: A background with [}}#unique-features">affine transformations]({{< ref). However, it doesn't allow H/V flipping and can only fetch 256 tiles (one quarter from the maximum). The size of this layer is 1024x1024 pixels wide.
    - **Affine Extended** background: Same as affine but restores the full amount of tiles and supports H/V flip.
- **Bitmap type group**: Instead of processing tiles, the engine treats VRAM as a frame-buffer. All of them inherit all the effects available from the character affine background.
    - **256 colours Extended**: Uses a 512x512 px frame-buffer.
    - **Direct colour Extended**: Similar to the previous one but the frame-buffer now supports up to 32,768 colours (15-bit).
    - **Large screen**: Eats up a whole 128 KB chunk of VRAM to render a large 1024x512 px frame-buffer.

These modes can't be chosen arbitrary, instead, the console provides a series of **background modes** with different combinations set.

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background modes" >}}

{{< float_block >}}
    {{< tabs nested="true" class="pixel" >}}
    {{< tab name="Layer 0" active="true" >}}
      {{< linked_img src="mario/bg1.png" >}}
    {{< /tab >}}
    {{< tab name="Layer 2" >}}
      {{< linked_img src="mario/bg2.png" >}}
    {{< /tab >}}
    {{< tab name="Layer 3" >}}
      {{< linked_img src="mario/bg3.png" >}}
    {{< /tab >}}
  {{< /tabs >}} <figcaption class="caption">Affine background layers in use <br>Layer 0 is transformed in real-time to simulate the clouds moving</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Here the background types are put into action. 'Main' and 'Sub' provide six modes of operations, all of them generate four background layers, but each one will have different capabilities:
- **Mode 0**: 4 static layers.
- **Mode 1**: 3 static layers + 1 affine layer.
- **Mode 2**: 2 static layers + 2 affine layers.
- **Mode 3**: 3 static layers + 1 extended affine layer.
- **Mode 4**: 2 static layers + 1 affine layer + 1 extended affine layer.
- **Mode 5**: 2 static layers + 2 extended affine layers.
    - This is the most common mode used, since it's incredibly flexible.
- **Mode 6**: 1 static layer drawn by the 3D engine + 1 large screen.
    - Since there is only space for a single frame-buffer, this mode is only available on 'Main'.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="mario/sprites.png" alt="Sprites" >}} <figcaption class="caption">Rendered Sprite layer</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Sprites or 'Objects' inherit the same functionality from the GBA's PPU, but we have two considerable additions.

Firstly, OAM (the region where sprites entries are stored) is now **2 KB** wide, enabling to display up to 128 sprites per frame per screen. Thus, 1 KB is allocated per each engine.

Secondly, OAM can now reference **bitmaps from VRAM** as opposed to only using tiles and palettes. This is another departure from the tiling system. In fact, both sprite 'modes' can coexist in the same frame, since this option is set on each individual sprite.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Result" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="mario/halfcomplete.png" alt="Result" >}} <figcaption class="caption">All layers merged... is there something missing?</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
As each layer renders on-the-fly, the final stage is tasked with merging everything and send it to the selected screen. This is pretty much what happens with previous PPU-based consoles, does that mean we are done here?

Not yet! 'Main' still has to fetch a layer from another engine, the most powerful one.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### The 3D accelerator

If you played with a Nintendo DS before, you know by now that this console can display a *particular* amount of 3D graphics. Unlike some GBA games, these aren't processed by the CPU. Instead, CPU-NTR includes **two components** that compose the **3D engine**. Curiously enough, the design Nintendo applied reminds me to [}}#graphics">SGI's RCP]({{< ref).

If you go back and check the 'Background modes' section, you'll notice every mode has at least one static background, this is because you can fill that layer with graphics produced by the 3D engine. The only caveat is that only the 'Main' can do this, hence one of the reasons Mode 6 is available for 'Main' only.

{{< tabs >}}
  {{< tab active="true" name="Geometry Engine" >}}

{{< float_block >}}
  {{< linked_img src="gpu/geometry.png" alt="Geometry Engine diagram" >}} <figcaption class="caption">Architecture of the Geometry Engine</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
If you read any of the articles from the 4th or 5th generation, you may be wondering... Where's the [}}#graphics">SIMD processor]({{< ref)? That's a good question because the ARM9 is not particularly good at vector operations and I don't think the dedicated divider is enough. That's why Nintendo embedded a component called **Geometry Engine** that takes care of **vertex transformations**, **projection**, **lighting**, **clipping**, **culling** and **polygon sorting**, the latter one is essential to properly use the transparency features.

This engine has some strict limitations, specifically the count of polygons it can process: There's an extra **248 KB of RAM** available used to store processed geometry, this amount accounts for up to **2048 triangles or 1706 quadrilaterals**, although this limit is reduced by using polygon strips (as opposed to individual polys). To get a sense of this number, I suggest to check out the 'interactive models' sections at previous articles, you'll see that it's a concerning constraint, but don't forget that the screen resolution of this console is also much smaller... so that compensates a bit.

Anyway, this engine is commanded using a **Command FIFO** which is filled with data from the CPU or DMA. The FIFO stores 256 entries, yet it's complemented by another buffer called **PIPE** that stores four more commands (giving a total of 260).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Rendering Engine" >}}

{{< float_block >}}
  {{< linked_img src="gpu/rendering.png" alt="Rendering Engine diagram" >}} <figcaption class="caption">Architecture of the Rendering Engine</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This is the rasteriser in charge of generating pixels and applying texture mapping. To do the latter, it relies on **perspective correction** and **Gouraud shading**. Moreover, the unit provides modern features like [}}#modern-visible-surface-determination">Z-buffering]({{< ref), **alpha blending**, **stencil tests**, **fog** and **anti-aliasing**.

The rendering system is a bit unorthodox though: Instead of rendering to a frame-buffer, it employs **line buffer rendering** where it fills by scan-lines, similarly to the 2D engine. This is because the engine has to synchronise with the 2D drawer. Now, for each quadrangle, the renderer can only fill **one span per scan-line** and this can be a bit troubling, since the result will get messy if the quad is concave or has crossed edges, for instance.

On the bright side, the unit also provides **shadowing** and a distinct feature called **Toon Shading** (another name for [}}#creativity">Cel Shading]({{< ref)): Even though this unit is not [}}#importance-of-programmability">programmable]({{< ref), the lighting parameters can be altered to achieve a cartoony effect.
{{% /inner_markdown %}}

{{< /tab >}}

  {{< tab name="Result" >}}

{{< float_block >}}
  {{< linked_img class="pixel" src="mario/complete.png" alt="Complete frame" >}} <figcaption class="caption">Ah, that's more like it</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Instead of writing the results back to a frame-buffer for display, the rendering engine will write to a block called **Colour Buffer** which stores up to 48 scan-lines. Each scan-line is fetched by the 2D engine to fill the BG0 layer in a FIFO manner.

3D rendering starts before the 2D one, enabling the latter to apply transformations on the new layer if required. 'Main' also allows to capture the 2D, 3D or combined frame generated, blend it with another frame in VRAM and write the result back to VRAM, which can be displayed afterwards.

In terms of control, the rendering engine also allows altering its parameters during mid-frame, due to employing a mechanism based on double-buffering that keeps a copy of the old state until the current frame finishes being drawn. Thus, no tearing will appear.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Famous comparisons

Some of the first games released for this console attempt to resemble the ones from another console (namely, the Nintendo 64). So I wanted to give a quick summary of why players may see some substantial differences between the two versions:

{{< tabs >}}
{{< tab name="First example" active="true" >}}

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< linked_img class="pixel" src="comparison/mario_n64.png" alt="Complete frame" >}}
  <figcaption class="caption">Super Mario 64 (1996)
  <br>Rendered at 320×240 pixels</figcaption>
  </div>

<div class="toright canvas-model">
    {{< linked_img class="pixel" src="comparison/mario_nds.png" alt="Complete frame" >}}
    <figcaption class="caption">Super Mario 64 DS (2004)
    <br>Rendered at 256x192 pixels</figcaption>
  </div>
{{< /side_by_side >}}

{{< /tab >}}
{{< tab name="Second example" >}}

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< linked_img class="pixel" src="comparison/kart_n64.png" alt="Complete frame" >}}
  <figcaption class="caption">Mario Kart 64 (1996)
  <br>Rendered at 320×240 pixels</figcaption>
  </div>

<div class="toright canvas-model">
    {{< linked_img class="pixel" src="comparison/kart_nds.png" alt="Complete frame" >}}
    <figcaption class="caption">Mario Kart DS (2005)
    <br>Rendered at 256x192 pixels</figcaption>
  </div>
{{< /side_by_side >}}

{{< /tab >}}
{{< /tabs >}}

So, to explain what's happening here, I've organised the different explanations based on what some people said on forums:
- ***NDS' textures look more ***blocky****** → The rendering engine does not employ any filter, so textures are interpolated using 'nearest neighbour' approach.
- ***NDS' textures look ***richer****** → The rendering engine is not limited by a [}}#tab-4-2-texture-memory">4 KB TMEM]({{< ref), there's instead up to 512 KB of VRAM available (apart from compression mechanisms provided) so naturally more data can be loaded.
- ***NDS' models contain ***pixelated edges****** → NDS models are rendered at a lower resolution compared to the N64.
- *NDS' textures look **distorted** when seen from a distance* → The rendering engine doesn't employ floating-point coordinates or sub-pixel precision. Low resolution and lack of mip-mapping also attribute to aliasing.

That's pretty much in a nutshell, for more specialised cases, you'll have to dive deeper at both engines and possibly disassemble both games to investigate which functions are being used and how.

#### Interactive models

I've updated the wee model viewer to apply 'nearest neighbour', allowing to visualise Nintendo DS models using your GPU.

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="mario_ds" >}}
    <figcaption class="caption">New Super Mario Bros (2004)
    <br>636 triangles</figcaption>
  </div>

<div class="toright canvas-model">
    {{< threejs_canvas model="dalmatian" >}}
    <figcaption class="caption">Nintendogs (2005)
    <br>750 triangles</figcaption>
  </div>
{{< /side_by_side >}}

Despite the fact we talked about a lot of limitations of the graphics subsystem, lots of games did make really good use of it.

---

## Audio

Most of the audio improvements focus on enhancing those PCM channels that the GBA [}}#audio">featured]({{< ref). We've seen before that GBA games ultimately prioritised software sequencing over the PSG, and the result was quite impressive.

{{< float_group >}}

{{< float_block >}}
  {{< video src="window" >}} <figcaption class="caption">Last Window: The Secret of Cape West (2010) <br>Showing mixed stereo output</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Consequently, the new audio system features a total of **16 PCM channels**, allowing to shift the mixing task to the hardware. PCM samples can either be **8-bit** (*GBA-style*), **16-bit** (optimal resolution) or **ACPCM** (compressed form). In any case, the mixer produces a **32 kHz stereo signal** that can be played through the speaker (now stereo) or headphones. The mixer can also write the resulting stereo data to WRAM, enabling the sub-processor (ARM7) to apply some effects (such as reverb).

Are we forgetting something else? *Yes*, since this console runs GBA games, then it should have something that reassembles the PSG (whether through hardware or software). Lo and behold, the last 6 channels can become a PSG (where any of them synthesises either a pulse or a custom wave, and two of them create a pulse or noise).
{{% /inner_markdown %}}

{{< /float_group >}}

With all being said, does this mean that the Nintendo DS can finally play encoded music (i.e. MP3)? It's possible (in fact, a lot of homebrew programs implemented some form of it), but audio decoding takes up a lot of bandwidth and processing power. So audio sequencing still retains its place as the most feasible option.

#### Interactive comparison

I've constructed this interactive widget that will allow you to compare by yourself how the new audio system affected the new generation of soundtracks. Each widget plays the same score but allows you to alternate between the old and new arrangements (I suggest wearing headphones to really notice the difference). Give it a whirl!

{{< side_by_side >}}
  <div class="toleft audio-switcher">
    {{< audio_switcher src1="trial_gba"  label1="GB Advance"
                       src2="trial_nds"  label2="Nintendo DS" >}}
    <figcaption class="caption"><strong>GBA:</strong> Gyakuten Saiban (2001, JAP only)
    <br><strong>NDS:</strong> Phoenix Wright: Ace Attorney (2005)</figcaption>
  </div>

<div class="toright audio-switcher">
    {{< audio_switcher src1="yoshi_gba"  label1="GB Advance"
                       src2="yoshi_nds"  label2="Nintendo DS" >}}
    <figcaption class="caption"><strong>GBA:</strong> Mario Kart: Super Circuit (2001)
    <br><strong>NDS:</strong> Mario Kart DS (2005)</figcaption>
  </div>
{{< /side_by_side >}}

(If you have trouble listening to it, please {{< email "drop me a mail" >}} mentioning the browser and device you are using)

Be as it may, I had to boost the gain of the GBA soundtrack a little bit to normalise the loudness, which tends to affect the signal-to-noise ratio (just something to bear in mind while you switch between the two). Anyway, I hope you got a sense of how the sound subsystem has evolved.

#### Some struggles

Let me show you some tricky cases now, where the original console had some unique audio features that weren't straightforward to recreate for this console, but I'll let you be the judge of that:

{{< side_by_side >}}
  <div class="toleft audio-switcher">
    {{< audio_switcher src1="mario_snes" label1="SNES"
                       src2="mario_nds"       label2="Nintendo DS" >}}
    <figcaption class="caption"><strong>SNES:</strong> Super Mario Kart (1992)
    <br><strong>NDS:</strong> Mario Kart DS (2005)</figcaption>
  </div>

<div class="toright audio-switcher">
    {{< audio_switcher src1="sonic_megadrive" label1="Mega Drive"
                       src2="sonic_nds"       label2="Nintendo DS" >}}
    <figcaption class="caption"><strong>Mega Drive:</strong> Sonic 3D Blast (1996)
    <br><strong>NDS:</strong> Sonic Chronicles (2008)</figcaption>
  </div>
{{< /side_by_side >}}

As you can hear from the first example (especially in the last 10 seconds), it's a bit hard to compete with the features that the SNES' [}}#audio">S-SMP]({{< ref) provided.

I must confess the second one was put on purpose, I mean, what the *freck* happened there right? As if the new arrangement were initially done for an Atari console instead. If you ask me, I think the Nintendo DS could have handled some sort of FM to PCM re-sampling, so the new *minimalistic* arrangement may just be a creative approach.

---

## I/O

To make a long story short, I/O is strictly handled by the ARM7. In fact, you won't see much going on with that CPU apart from passing data around... which is too bad really.

#### Accessing cartridges and memory

There's an external memory interface connecting three endpoints: **Slot-1** (where Nintendo DS cards go), **Slot-2** (where GBA cartridges or accessories go) and the **4 MB of PSRAM** (Main memory). The interface can be accessed by both CPUs, but it contains registers that can be modified to prioritise one CPU over the other in case there are two request from the same bus at the same time.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="memoryaccess.png" alt="External memory Model" >}} <figcaption class="caption">External memory model with labelled data bus width</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Now, here's the important bit: DS cards are **not memory-mapped**, so for either CPU to read game data, the content must be copied to RAM first. This is done by sending to the cartridge blocks of 8-bit commands referencing 32-bit addresses. Afterwards, the data can be manually retrieved by pulling it from a 32-bit register or through DMA. The data bus is 8-bit wide but can reach speeds of up to **5.96 MB/sec** (as claimed by Nintendo).

The 'backup' chip used for saves (i.e. EEPROM, FLASH or FRAM) is accessed through an SPI bus (serial) which uses its own set of commands and it's connected to a 24-bit address bus.

{{% /inner_markdown %}}

{{< /float_group >}}

The Slot-2 cartridge is memory-mapped using the original pinout, but the addresses are shifted in DS mode to accommodate the hardware that provides expansion functionality (extra RAM, rumble, etc). Just like the GBA, the ROM bus is 16-bit wide and the RAM bus is 8-bit wide.

#### Peripherals

The ARM7 is also connected to another SPI node interfacing the **TouchScreen controller**, which operates the bottom screen's (it's the resistive type, requiring the use of a stylus); and the flash memory (which is where the *firmware* is stored, more details later on).

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Puzzle" active="true" >}}
      {{< linked_img src="puzzle.png" alt="Hotel Dusk Puzzle" >}} <figcaption class="caption">Hotel Dusk: Room 215 (2007) showing the aforementioned switchboard puzzle <br>To rescue the lassie, the player had to swipe the screen using two fingers at the same time to switch on the lights</figcaption>
    {{< /tab >}}
    {{< tab name="Fail" >}}
      {{< linked_img src="puzzle_fail.png" alt="Hotel Dusk Puzzle Fail" >}} <figcaption class="caption">But if you do it wrong...</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
A curious thing about this touchscreen is that apart from detecting the X/Y positions, it can also return the **diagonal position** (used to calculate the 'pressure value', which represents the area where pressure is being applied). Unfortunately, this was **never exposed in the official SDK**, so as far as I know, no game ended up using this undocumented feature (except homebrew).

Many have pointed out that 'Hotel Dusk: Room 215' relied on this feature for one of its puzzles, which required users to use two fingers at the same time. This is not the case however: After experimenting with the no$gba debugger, the puzzle does not make use of pressure data. Instead, it checks whether the x/y values alternate drastically. The game interprets this effect as if the user had pressed the screen with two fingers.
{{% /inner_markdown %}}

{{< /float_group >}}

Finally, in the same stack we find the **real-time clock** or 'RTC'.

#### Wireless network

Last but not least, the console contains a **Wireless controller** operating in the 2.4 GHz band that provides some innovative features:
- **Internet Play**: Enables any game to connect to a LAN network using a standard Wi-Fi connection.
- **Multi-card Play**: Up to 16 consoles communicate with each other using a proprietary protocol.
- **Single-card Play**: The game can upload a program to another DS which doesn't contain the game card.

---

## Operating System

I guess it's safe to say that by this generation every console now comes bundled with some sort of interactive interface. The NDS still inherits the previous operating system model consisting in lightweight APIs to simplify I/O access, but also provides a minimal user interface to tweak some settings and fiddle with some of its 'apps'.

Having said that, its operating system is diversified into multiple chips, so let's start with the ones that are read upon boot.

#### Entry point

At some point, the ARM7 and ARM9 will need to initialise the hardware and to do this, NTR-CPU includes two different small ROM chips:
- A **4 KB BIOS** connected to the ARM9's bus.
- A **16 KB BIOS** connected to the ARM7's bus.

When the console starts up, each CPU boots from its respective ROM. This is because their **reset vector** points to each chip (for reference, ARM9's vector is at `0xFFFF0000` while ARM7's one is `0x00000000`).

Moving forward, each BIOS stores two sets of routines: Boot code and interrupt calls. The latter is no stranger giving the history of the [}}#anti-piracy--homebrew">previous console]({{< ref), however the former has increased in complexity: Apart from initialising the hardware, ARM7's code will also handle security by running some checks on the DS cartridge (if there is one inserted).

After running the boot code, both CPUs will synchronise so they can start acting as a 'single machine': It turns out the ARM9 finishes loading way before the ARM7, so the ARM9 sends a 4-bit value to the ARM7, stalls on a semi-endless loop waiting for the ARM7 to respond and once it does, both 'cross the finish line' at the same time, that is to say, they are now in-sync.

#### Window of opportunity

If you have or had a DS, you probably noticed you can **only** play games if the cartridge was inserted before turning on the console. This is because the ARM7's BIOS carries out some checks on the cartridge during boot (more details on the last section) and if all the tests pass, ARM7's game executable is copied to WRAM and ARM9's one is copied to Main Memory.

If for some reason the executables are not copied (due to the cartridge not being valid or not found during boot), then the game can't be started and the user will have to reset the console to play one.

#### Interactive shell

Whether there's a game or not, the system will finish booting by loading an interactive shell. This is just a program that resides on an external **256 KB Flash** memory.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" class="pixel desktop-margined" >}}
    {{< tab name="Home" active="true" >}}
      {{< linked_img src="shell/home.png" >}} <figcaption class="caption">Home screen</figcaption>
    {{< /tab >}}
    {{< tab name="Splash" >}}
      {{< linked_img src="shell/welcome.png" >}} <figcaption class="caption">You see this screen every time you switch the DS on <br>The 'Nintendo' logo appears when there is a valid card inserted</figcaption>
    {{< /tab >}}
    {{< tab name="Settings" >}}
      {{< linked_img src="shell/settings.png" >}} <figcaption class="caption">Settings screen</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
The same chip stores the firmware along with some user settings (language, nickname, birthday, alarm and a welcome message) and some system settings (touchscreen calibration, first startup flag, firmware version and wifi settings).

The shell is more-or-less the same as the rest of its contemporaries. Users rely on it to start their game, change settings, download a game (using 'Download Play') or fiddle with **Pictochat**: an open chat room that talks with nearby Nintendo DSes.

It's worth emphasising that both read-only and writable data reside in the same re-writable chip, so it's theoretically possible to write over the firmware! Luckily (or for obvious reasons), Nintendo protected the upper quarter of the chip (64 KB) from being written by requiring a jumper placed on a point in the motherboard called `SL1`, which is exposed by removing the battery compartment. Nonetheless, over-writing the rest of the flash memory can still produces catastrophic results!
{{% /inner_markdown %}}

{{< /float_group >}}

#### Updatability

Lastly, this firmware ended up being updated by Nintendo a couple of times (5 to be precise) in an effort to patch some security vulnerabilities. The updates weren't installable by the user (recall the `SL1` protection). Instead, Nintendo embedded the updated firmware in the next lot manufactured.

---

## Games

Oh, there's a lot to talk about here, primarily because the capabilities of this console did inspire a lot of programmers and artists to come up with really innovative designs. Let's see...

#### Medium

This console runs games from three sources, where only two of them can make 'full' utilisation of the hardware:
{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="game.jpg" alt="Game" >}} <figcaption class="caption">Example of retail game</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
- **NDS or 'Slot-1' cartridge**: This is the main medium used to load native DS games. It's the only medium used for distribution.
- **GBA or 'Slot-2' cartridge**: This slot enables the console to play GameBoy Advance games natively and, since Slot-1 games can also access this origin, **expansion cartridges** can be plugged in to enhance NDS games. These provide things like more RAM, more input controls or feedback devices (i.e. *rumble pack*).
- **Download Play or 'Wireless MultiBoot'**: This is an evolved version of the original [}}#accessories">Multi-Boot]({{< ref) which enables another console with an NDS game to upload a program using Wireless communication. The downloaded content is stored in WRAM and booted after the transfer finishes. Since WRAM is volatile, the data will be lost on shut down.
  - Authorised retail stores used this function to deploy **Download Stations**, where users were invited to download game demos as part of their visit to the store.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Program structure

You've seen that the BIOS requires to be split up with separate code for ARM9 and ARM7, this is pretty much what also happens with games. Thus, NDS cards are structured with the following areas:

- **Header (4 KB)**: Contains the metadata (location of each executable, serial numbers, etc).
- **Secure Area (16 KB)**: Used for copy protection purposes. We'll get into more details in the last section of this article.
- **Main content (variable size)**: The rest of the card just contains the executables and game data (graphics, sound, etc). Retail games using Nintendo's SDK contain their data organised hierarchically with files and directories thanks to a built-in filesystem.

#### Development ecosystem

For game studios interested in developing games for this console, Nintendo distributed both hardware kits and SDKs with lots of utilities.

{{< tabs >}}

{{< tab name="The hardware" active="true" >}}
{{% inner_markdown %}}

The devkit, called **IS-NITRO-EMULATOR**, consisted of a medium-size blue box containing most of the DS's internal hardware and I/O. This is followed by a thick cable connected to a dummy Nintendo DS case, serving as a 'controller' and display. At request, the devkit was enhanced with optional capabilities like audio/video out, Wifi (by default it was emulated using Ethernet) and debugger. I was expecting the latter to be already included but I realised these units could also be used by test teams.

The kit reads DS cards, but a different type with a larger case and swappable backup chips. These cards are flashed using another unit called **IS-NITRO-WRITER**.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="The software" >}}

{{% inner_markdown %}}

The software kit included utilities for interacting with the devkit, the C/C++ toolchains and the hardware APIs. One point to mention is that the docs are explicit in forbidding any circumvention of their APIs to access the hardware directly: Even though **games will run on bare metal**, Nintendo won't approve their distribution if it executes 'prohibited' operations. This includes controlling the ARM7 directly, exceeding display resolution or turning off the LCDs.

There are understandable reasons for imposing these norms, such as to maintain quality levels. Although, to be honest, I think not allowing to use the ARM7 for more than just I/O tasks was a waste of potential...

{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

#### Freedom of interaction

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="kawashima.png" alt="Game" >}} <figcaption class="caption">Dr Kawashima's Brain Training (2005) <br>New categories of games attracted other audiences</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
With all the new forms of interaction available, studios had the opportunity to prioritise gameplay experience over graphics.

For the first time in consumer electronics, there was a touchscreen, microphone, wifi and a real-time clock packaged in the same console. Nevertheless, some games even presented new forms of interaction, such as instructing the user to hold the console sideways.
{{% /inner_markdown %}}
{{< /float_group >}}

#### Network service

After the success of a [}}#network-service">previous competitor]({{< ref), Nintendo joined the club of online multiplayer and deployed their centralised infrastructure. Games using the 'Internet Play' could connect to Nintendo's servers (called **Nintendo Wi-Fi Connection**) to enjoy some online gaming.

---

## Anti-Piracy and Homebrew

Even though DS cards weren't affected by the *curse of the compact disc*, Nintendo implemented some protection systems to keep control of game distribution.

{{< tabs >}}

{{< tab name="Encryption system" active="true" >}}
{{% inner_markdown %}}
The Nintendo DS mainly uses a symmetric encryption system to encrypt the communication between the Memory Interface and the Slot-1 card. Before we discuss how encryption is performed, let's talk about the algorithms used and how the keys (which will be used to perform encryption) are generated.

The 'Header' area of the card contains a value called **Gamecode** (the unique identifier of the game), the memory interface grabs this block to generate the **KEY1** and uses it to encrypt further commands sent to the card. KEY1 encryption is based on the **Blowfish algorithm**.

Afterwards, KEY1 is mixed with the internal clock and some other values of the cartridge header to generate a new key called **KEY2**. The fundamental difference with KEY1 is that the former uses random values to make it unpredictable. KEY2 encryption relies on multiple XOR and Shift operations to obfuscate the data.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="DS card validation" >}}
{{% inner_markdown %}}

As we've seen before, the BIOS includes some routines that validate the NDS card upon startup. This works as follows:
1. The console retrieves the chip ID of the cartridge, saves it on RAM and then proceeds to enable KEY1 encryption.
2. The first 2 KB of the 'Secure area' are copied to RAM as well. The first 8 B of this chunk stores a string called **Secure Area ID**, the next values contain some checksums (CRC16 type) and other metadata.
4. 'Secure Area ID' is already encrypted with KEY1, but it's encrypted again using KEY2 encryption and decrypted afterwards, if the decrypted value matches the string `encryObj` it means card validation **passed the first test**. After this, the string is destroyed to prevent revealing the algorithm. If the validation failed, the 2 KB of Secure Area are filled with garbage, preventing the rest of the card from being read.
5. The second test consists in retrieving the chip ID again and encrypt it with KEY2 a random amount of times (the seed depends on the internal clock). If the value of chip ID matches the first chip ID stored, the **second test passed**.
6. Finally, the rest of the Secure area is fetched in random order and re-constructed in RAM. After this, the firmware is executed.

If everything went well, the firmware will find the required executable of the card in RAM, allowing the user to boot up the game. Otherwise, the game selector will be shown greyed out.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Download Play protection" >}}
{{% inner_markdown %}}

Programs received via Download Play must be signed by Nintendo using an [}}#tab-7-2-chain-of-trust">RSA signature]({{< ref) (only Nintendo knows the private key).

This check is performed by the firmware.

{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

#### Defeat

If you were a homebrew user back then you probably ran across tons of options available to run this software. The truth is, before all the current cracks were discovered, hackers had a hard time circumventing Nintendo's complex anti-piracy system.

{{< tabs >}}

{{< tab name="Existing Slot-2" active="true" >}}
{{% inner_markdown %}}

Because the GBA subsystem still executes cartridges without any protection implemented (aside from [}}#anti-piracy">trademark tricks]({{< ref)), existing GBA flashcarts were still compatible with the NDS. This enabled to run GBA homebrew, which worked fine if you didn't mind missing out all the new functionality exclusive to DS games.

As always, flash cartridges also enabled to run pirated ROMs, but since Nintendo couldn't change the protection system of the GBA (as it could potentially render existing games unusable), the company just had to deal with it.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Enhanced Slot-2" >}}
{{% inner_markdown %}}

After more clandestine research was conducted on the DS BIOS and firmware, it was ultimately discovered that execution of an NDS card could be redirected to the GBA slot. The NDS card wasn't cracked yet, but this method allowed to provisionally bypass the security system of Slot-1 cards and execute **Slot-2 programs in DS mode**.

Thus, a new generation of Slot-2 flashcarts appeared on the market. They embed ARM9 code that is executed once bootstrapped from Slot-1. The bootstrap itself was accomplished using one of these methods discovered (called 'passthrough methods'):
- Using a **PassMe** card: This requires a genuine Slot-1 card. PassMe is some sort of adapter sitting between the Slot-1 and a genuine game. It basically tampers the header sent from the card to the console to trick the console into executing Slot-2 code, loading the Slot-2 cartridge in the process.
- Using **WifiMe**: Requires a PC with a compatible Wi-Fi card. With the use of a modified driver, it was possible to broadcast a customised program that could be downloaded to the DS using Download Play. Once booted up, it would redirect execution to Slot-2. This exploited the fact the firmware didn't check the RSA signatures of particular areas of the binary.
- Using **FlashMe**: A permanent solution. By chaining a previous method and bridging the `SL1` terminal, it was possible to run a homebrew program that could write over the firmware to boot from Slot-2 if a flash cartridge was present. This hack also removed the digital signature check to boot homebrew from Download Play as well.

As expected, Nintendo produced DS revisions that included an updated firmware which patched these tricks. So hackers tried to find other methods that focused on more BIOS exploitation (which is difficult to patch).

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Native Slot-1" >}}
{{% inner_markdown %}}

Martin Korth, the developer of the famous Nintendo DS emulator called 'NO$GBA', later managed to extract the BIOS and reverse engineer the Slot-1 security. With this, newer tools and documentation revealed the true mechanisms of Nintendo DS security. As you have seen in this article, the encryption system worked as long as nobody reversed it (this is a limitation of symmetric encryption systems, unlike asymmetric encryption systems, such as RSA where the private key is never stored).

Anyway, these led to a massive influx of **plug-and-play Slot-1 flashcards** which worked on any type of console and ran Nintendo DS programs **natively**. The passthrough method was also improved with the debut of 'NoPass' cards which allowed to load Slot-2 flashcarts without requiring a genuine game.

Since the encryption system couldn't be altered without making breaking changes that affect all existing retail games, Nintendo ultimately lost this battle. The only thing left was to pursue the legal route, just like they did with their previous console.

{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

This is my personal opinion, but it's really striking how simple are flashcards compared to other homebrew methods from previous consoles. In older articles, I've described that if users ultimately wanted to run homebrew programs or pirated games, they would have to get down in the rabbit hole and follow some sort of complicated method.

In the case of the DS, flashcards were literally sold just like retail games, and I bet it was truly concerning for game studios to see how painless was to resort to piracy.

Another thing, it's also surprising the amount of branded flashcards (ignoring all knock-offs) that appeared on the market. If you look it from a technical perspective, flashcards are just SD adapters. The only thing that differentiates one card from another is the boot code and SD reader. Some manufacturers also took more effort to design a better file browser (referred to as kernel/firmware) and include some additional hardware.

---

## That's all folks

{{< centered_container >}}
  {{< linked_img src="myds.jpg" alt="My DS" >}} <figcaption class="caption">My *current* DS used for this study <br>I actually sold my first one long time ago... I wonder where it's now</figcaption>
{{< /centered_container >}}

Alrighty! I think I covered 99% of what I wanted to talk about...

I hope I didn't sound too harsh when criticising some sections. Don't get me wrong, I still think this console is a good piece of engineering! But there are some defects which make me wonder if it's really a cost-effective compromise, or part of a design flaw. Truth to be told, that didn't stop 11-year old me from wanting one back then. So I guess that's pretty much 'mission accomplished' for the company!

I would also like to thank friends and the MelonDS community for taking the time to check out the draft and pointing out *lots* of corrections. The Nintendo DS allowed me to mention many topics I've been interested in writing about for a while and I was afraid I would end up biting off more than I could chew, but hopefully you've enjoyed this article.

Until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- Nintendo, 2006, **NITRO Programming Manual**, Version 1.44
- Martin Korth (No$GBA author), [**Gameboy Advance / Nintendo DS - Technical Info**](https://problemkaputt.de/gbatek.htm)
- osdl.sourceforge.net, [**A guide to homebrew development for the Nintendo DS**](http://osdl.sourceforge.net/main/documentation/misc/nintendo-DS/homebrew-guide/HomebrewForDS.html)
- Jaeden Amero, [**Introduction to Nintendo DS programming**](https://patater.com/files/projects/manual/manual.html)

#### CPU

- ARM Holdings, [**ARM946E-S Technical Reference Manual**](https://developer.arm.com/documentation/ddi0201/d/introduction/about-the-arm946e-s-processor)
- Chris Double, [**ARM9-ARM7 FIFO**](http://double.nz/nintendo_ds/nds_develop7.html)
- Dave Jaggar, [**ARM Microprocessor Rise and Fall** (Youtube)](https://www.youtube.com/watch?v=_6sh097Dk5k), Talks at Google

#### Graphics

- Arisotura (MelonDS dev), [**The DS GPU and its fun quirks**](http://melonds.kuribo64.net/comments.php?id=56)

#### Operating System

- Pocketheaven.com, [**DS Firmware** (Archived)](https://web.archive.org/web/20071127014755/http://wiki.pocketheaven.com/DS_Firmware)
- Pocketheaven.com, [**DS Bricker** (Archived)](https://web.archive.org/web/20100807234939/http://www.pocketheaven.com/ph/wiki/DS_Bricker#r0mloader.zip)
- psiloveomega, [**Booting the Nintendo DS – a technical summary**](https://corgids.wordpress.com/2017/07/28/booting-the-nintendo-ds-a-technical-summary/)

#### Games

- nsmbhd.net, [**IS NITRO EMULATOR listing**](https://nsmbhd.net/thread/4438-nintendo-ds-dev-hardware-is-nitro-emulator-and-co/#61422)

#### Anti-Piracy

- Acekard, **Acekard RPG 4.06 Source Code**
- ndsbackup.com, [**NDS WiFiMe** (Archived)](https://web.archive.org/web/20180827093014/https://www.ndsbackup.com/wifime.htm)
- Bruce Schneier, [**The Blowfish Encryption Algorithm**](https://www.schneier.com/academic/blowfish/)

#### Photography

- Console and motherboard: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- 3D models: [**The Models Resource**](https://www.models-resource.com/)
- Diagrams, casual photos and screenshots: **Me**