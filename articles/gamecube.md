---
name: GameCube
releaseDate: 2001-09-14
date: 2019-11-19
subtitle: Powerful and compact
generation: 6
javascript: ['threejs', 'plyr']
cover: gamecube
top_tabs:
  Model:
    file: international
    caption: "The one and only GameCube
    <br>Released on 14/09/2001 in Japan, 18/11/2001 in America and 03/05/2002 in Europe"
  Motherboard:
    caption: "Showing revision 'DOL-CPU-10', later ones removed Serial Port 2 and Digital Out
    <br>Encoder chip, expansion, controller and PSU slots are on the other side"
  Diagram:
    caption: "Each data bus is labelled with its width."

# SEO
title: GameCube Architecture

# Historical
aliases: [/projects/consoles/gamecube/]
---

## A quick introduction

Gone are the days of '3D-attempts', Nintendo's new offering consists in a clean and powerful break from its predecessor that will open the door to new, original and unseen content.

It's worth pointing out that the design of this architecture led to one of the most compact hardware of this generation, attributing at the lack of *slims* or *lite* revisions.

---

## CPU

After the loss of SGI's dominance in the graphics market, Nintendo needed new players to partner up with.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="cpu_features.png" alt="Construction of Gecko" >}}
  <figcaption class="caption">Construction of Gecko</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
A promising candidate seems to be IBM: Apart from their famous work on mainframes, they recently formed an alliance with Motorola and Apple to create a CPU powerful enough to compete with Intel's ruling in the PC market. The resulting product is a series of processors carrying the name **PowerPC**, which were selected to *power* 99% of Apple's Macintoshes and some embedded systems.

Fast forward, Nintendo required something powerful but cheap, so in order to comply with those red lines, IBM grabbed one of its past designs, the *PowerPC 750CXe* (found on the late iMac G3, known as the *Early-Summer 2001*), and beefed it up with capabilities that would please game developers. The result was the **PowerPC Gecko** and runs at 486 MHz. 
{{% /inner_markdown %}}

{{< /float_group >}}

#### Features

Let's find out what makes Gecko so special, and to do that we need to first have to look at the offerings of the 750CXe:

- **PowerPC ISA**: Without going into much detail, it's *another* 32-bit RISC instruction set. The 750CXe implements the v1.10 specification.
- **Super-scalar**: More than one instruction can now be executed in each stage of the pipeline.
- **Out-of-order execution**: The CPU can re-order the sequence of instructions to keep all of its units working, thus increasing efficiency and performance.
- **Two Integer Units**: Combined with super-scalar and out-of-order, it basically increments the amount of integer operations done per unit of time.
- External **64-bit data bus**: While the ISA can fit in a 32-bit bus, we still need to move other longer types (explained in the next paragraph) without hitting performance penalties.
- **Integrated 7-stage FPU** with 32-bit and 64-bit registers: Accelerates operations with floats and doubles. It's pipelined so up to seven FPU instructions can be queued in parallel.

Additionally and due to its RISC nature, this CPU also includes dedicated units to speed up specific computations:

- **Branch Prediction Unit**: Whenever there is a condition that needs to be evaluated (which would decide if the CPU should follow path 'A' or path 'B'), the CPU will instead follow one of the paths based on previous executions and then evaluate the condition. If the prediction happens to be right then the CPU will have saved some time, if not, it will reverse and follow the correct path.
- **Dedicated load-store unit**: Separates the units that manipulates registers and the one handling main memory.
- **Integrated Memory Management Unit**: Previous consoles included this component as another co-processor, the MMU is now found inside the same chip, reducing manufacturing costs.

And, of course, some cache is also included to speed up memory bandwidth:

- **64 KB L1 cache**: Divided in 32 KB for instructions and 32 KB for data.
- **256 KB L2 cache**: It can be filled with instructions and data, which greatly improves bandwidth.

#### IBM's enhancements

While the previous lists of features are very appreciated (compared to previous generations), this CPU still falls behind others on gaming performance (let's not forget that this is still a general-purpose CPU, good at spreadsheets but *average* at physics). To compensate, IBM added the following tweaks that will constitute Gecko:

- Enhanced instruction set with **50 new SIMD instructions**: Speeds up vector calculations, particularly useful during geometry transformations. Not to be confused with Motorola's SIMD extension (AltiVec) which can be found on high-end G4 Macs.
- **32 Floating-point registers**: Since more numbers can now be stored in the FPU, less memory access is required.
- **Write Gather pipe**: A special memory writing mechanism available to use. If enabled, instead of performing *single-beat* transfers, it holds all memory write requests in a 128-byte buffer until it's 25% full, then performs the requested writes using a technique called *burst transaction* which can move blocks of 32 bytes of data at once.
  - As you can imagine, this saves a lot of bandwidth by making full utilisation of the available buses.
- **Locked L1 cache**: Programs can take away 16 KB of L1 data cache to use as scratchpad (incredibly fast memory).

With all of this, game developers will be able to fit code which takes care of generating and/or transforming our geometry (vertices in this case), and this is really important, since the CPU can now perform some operations of which the GPU can't do (more details later on).

#### Clever memory system

During the design of the next-gen architecture, Nintendo's architects performed a post-mortem analysis of [their previous design]({{< ref "nintendo-64" >}}) and discovered that using a Unified Memory architecture together with some high-latency components (RDRAM) resulted in one of the biggest causes of bottleneck (almost 50% of CPU cycles were wasted while idling), in addition, the inclusion of multiple independent units contributed to a concerning competition for the memory bus.

For that reason, Gamecube architects came up with a new memory system strictly based on **providing dedicated memory space** and **using low-latency chips**. With the new design, GPU and CPU will no longer compete for the same RAM (causing fill-rate issues) since the GPU will now have its own internal and *amazingly* fast memory. On the other side, the GPU will still be in charge of arbitrating access to I/O too.

The result was a system organised with two main buses:

- The **Northbridge**: It's 64-bit wide and connects the CPU with the GPU. It runs 3 times slower than the CPU clock, so tasks will have to be optimised to not rely so much on the GPU. Other components like the DMA and cache may come in handy.
- The **Southbridge**: It's also 64-bit wide and connects the GPU with 24 MB of 1T&#8209;SRAM called **Splash**, this type of RAM is made of DRAM (which is the most popular type of RAM but also cheaper and slow) however it's enhanced with extra circuitry to **behave like SRAM** (faster and expensive, mostly used for cache). The bus is **twice as fast as the GPU**, possibly to enable the GPU to provide a steady bandwidth between the CPU and main memory.

Additionally, this design contains an additional (yet unusual) bus where more memory can be found:

- The **Eastbridge**: It connects the GPU with another memory chip called **Audio RAM** or 'ARAM'. It provides 12 MB of SRAM available for general purpose, which is pretty big for 'spare memory'. However, the bus is not accessible through normal means (memory addresses), instead it's interfaced by a 8-bit serial protocol clocked two times slower than the GPU and four times slower than the CPU. Overall, this means that while it provides a considerable amount of RAM, it will be limited to less critical tasks, like acting as an audio buffer or being used by certain accessories (more details later on!).

---

## Graphics

This is one of the most critical sections of this console, it basically makes the Gamecube, a *Gamecube*.

The history of this console's GPU has some interesting connections: Wei Yen, the director of N64's GPU ([the RCP]({{< ref "nintendo-64#graphics" >}})), later founded Artx and landed a contract with Nintendo to develop their next-gen GPU: **Flipper**.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="sunshine.png" alt="Super Mario Sunshine" >}}
  <figcaption class="caption">Super Mario Sunshine (2002)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
There were lots of advancements done from the previous iteration, for instance, the subsystem was severely simplified down to a single (but powerful) core.

During the development process, ArtX got acquired by ATI, which in turn was sold to AMD six years later. Hence, this is why you see an ATI sticker stamped on the front of the case.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Architecture and design

Flipper handles multiple services, so let's focus on the graphics component for now (since it's the one responsible for bringing our geometry to life). If you've been reading the [N64 article]({{< ref "nintendo-64#graphics" >}}), just letting you know that the core is now functional out of the box, so programmers won't need to worry about injecting code to make it work, nonetheless, there will some interesting parts that are customisable.

{{< centered_container >}}
  {{< linked_img src="flipper_pipeline.png" alt="Pipeline design of Flipper" >}}
  <figcaption class="caption">Pipeline design of Flipper</figcaption>
{{< /centered_container >}}

As always, in order to draw a frame on the screen, our data will be pumped through Flipper's pipeline. Data goes through lots of different components which we can group in four stages:

{{< tabs >}}

{{< tab active="true" name="Database" >}}

{{< float_block >}}
  {{< linked_img src="flipper_pipeline/database.jpg" alt="Database stage diagram" >}}
  <figcaption class="caption">Database stage diagram</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The CPU and GPU communicate to each other using a fixed-length **FIFO buffer** in main RAM, this is a reserved portion where the CPU will write drawing commands that the GPU will read (and eventually display), this functionality is natively supported by the CPU and GPU.

Furthermore, the CPU and GPU don't have to be pointing at the same FIFO at the same time, so the CPU can fill another one will the GPU is reading the first one (which can avoid waiting states). 

Issuing individual commands to construct our geometry can get very tedious with complex scenes, so official libraries included tools that generated the required Display Lists (pre-compiled set FIFO commands) from our assets, this chunk only needs to be copied to RAM in order to let the GPU effectively display them.

The GPU contains a **command processor** which is charge of fetching commands from FIFO.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Geometry" >}}

{{< float_block >}}
  {{< linked_img src="flipper_pipeline/vertex.jpg" alt="Vertex stage diagram" >}}
  <figcaption class="caption">Vertex stage diagram using indirect mode</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Here primitives are transformed to shape accordingly for the desired scenery and prepared for rasterising. The engine uses a dedicated **Vertex unit** or 'VU' to accomplish this.

There are two **vertex modes** available to handle primitives issued through FIFO:

- **Direct mode**: The CPU issues each FIFO entry with all required attributes (position, normal, colour, texture coordinate, or matrix index). Useful when the data is already cached.
- **Indirect mode**: The FIFO entry contains an index value that specifies where the attribute information is located in RAM, so the vertex unit needs to look it up by itself. This data is structured as an array, so in order for the VU to traverse it, each vertex entry must specify where the array starts (**base pointer**), how long is each entry (**stride**) and at which position the vertex is (**index**).

Once loaded, the primitives can be **transformed**, **clipped**, **lighted** (each vertex will have an RGB value that can also be interpolated for Gouraud Shading purposes) and finally, **projected**.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Texture" >}}

{{< float_block >}}
  {{< linked_img src="flipper_pipeline/texture.jpg" alt="Texture stage diagram" >}}
  <figcaption class="caption">Texture stage diagram using a default setup</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Now it's time to apply textures and effects to our models, and for that Flipper includes multiple units which will process our pixels. Now, this is a very sophisticated (yet quite complex) procedure, so if you find it difficult to follow, just think of it as a big assembly line that process pixels. Having said that, there are three groups of units available:

- **Four parallel Pixel units** (also referred pixel pipelines): Rasterises our primitives (converts them to pixels). Having four units available enables to deliver up to 2x2 pixels with each cycle. 
- **One Texture mapping unit** at the end of each Pixel unit (giving **four in total**): Together they process up to eight textures for our primitives (now mere pixels) at each cycle.
  - It can also loop itself to merge multiple texture layers over the same primitive, this feature is called **Multi-Texturing** and can be used to achieve **detailed textures**, **environment mapping** (reflections) and **bump mapping**, for instance.
  - Finally, the unit also provides an **early [z-buffer]({{< ref "nintendo-64#modern-visible-surface-determination" >}})**, **mipmapping** (processing a downsized texture instead, based on the level of detail) and **anisotropic filtering** (a welcoming improvement over the [previous filters]({{< ref "nintendo-64#tab-1-2-reality-display-processor" >}}) that provides greater detail with sloped textures). 
- **Texture Environment unit** or 'TEV': A very powerful programmable 16-stage colour blender. It basically combines multiple pixels (lighting, textures and constants) to achieve an immense amount of texture effects that will be applied over our primitives.
  - The unit works by receiving four pixel colours which are then processed based on the operation requested. Afterwards, it can feed itself the resulting pixels as new input, so at the next stage/cycle, the unit can perform a different type of operation over the previous result. This 'loop' can last up to 15 iterations.
  - Each stage has 2<sup>4</sup> operations to choose from and since the result can be re-processed at the next stage, there are around 5.64 × 10<sup>511</sup> possible permutations!
  - Programmers set up the TEV at runtime (meaning it can change any time) and this is really important since it opens the door to lots of original materials and effects.

All of this is assisted by 1 MB of Texture memory (1T-SRAM type) which can be split into cache and Scratchpad memory (fast RAM). **Real-time hardware decompression** for SRTC (S3 Texture Compression) textures is also available to fit more textures in that single meg. of memory available.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Render" >}}

{{< float_block >}}
  {{< linked_img src="flipper_pipeline/render.jpg" alt="Render stage diagram" >}}
  <figcaption class="caption">Render stage diagram</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The final stage of the rendering process includes applying some optional but useful touches to our scene:

- **Fog**: Blends the last colour from the TEV with a fog constant colour to effectively simulate a foggy environment.
- **Z-compare**: A late-stage [Z-buffer]({{< ref "nintendo-64#modern-visible-surface-determination" >}}). The engine will use 2 MB of embedded 1T-SRAM for Z-buffering calculations.
- **Blending**: Combines the colours of the current frame with the previous frame-buffer.
- **Dithering**: As the name indicates, applies dithering over our frame.

The resulting frame is finally written to the **Embedded Frame Buffer** or 'EFB', but this is still locked inside Flipper. So in order to display it on our TV, we have to copy it to the **External Frame-Buffer** or 'XFB', which can be picked up the **Video Interface** or 'VI'. Besides, the copy process can apply effects like **Antialiasing** (reduces blocky edges), **Deflicker** (smooths sudden changes in brightness), **RGB to YUV conversion** (a similar format that occupies less space in memory) and **Y-scaling** (vertically scales the frame).

It's worth mentioning that the XFB area can also be manipulated by the CPU, this enables to combine previously-rendered bitmaps with our just-rendered frame, or when certain games need to render very colour-rich frames which can't fit in the EFB, so they are rendered in parts and merged by the CPU afterwards (always keeping in-sync with the VI).
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

#### Interactive comparison

Time to put all of this into perspective, check out how programmers evolved the designs of their previous games to take advantage of the new graphics capabilities of this console. Don't forget the examples are interactive!

{{< tabs >}}
{{< tab name="The upgrade" active="true" >}}

{{% inner_markdown %}}
The famous Mario model which had to be stripped down due to polygon counting on the [previous generation]({{< ref "nintendo-64">}}) got completely redesigned for this one, take a closer look of how the model evolved from plain faces to wrinkled sleeves.
{{% /inner_markdown %}}

{{< side_by_side nested=true >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="mario_smash" >}}
    <figcaption class="caption">Super Smash Bros (1999) for the N64
    <br>229 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="mario_melee" >}}
    <figcaption class="caption">Super Smash Bros. Melee (2001) for the GC
    <br>2,494 triangles</figcaption>
  </div>
{{< /side_by_side >}}

{{< /tab >}}
{{< tab name="The update" >}}

{{% inner_markdown %}}
In this case, Sonic Team already designed a Sonic model for [their unique console]({{< ref "dreamcast">}}), but after porting their game to the Gamecube they found themselves able to add more polygons to their model, achieving better detail.
{{% /inner_markdown %}}

{{< side_by_side nested=true >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="sonic_adventure" >}}
    <figcaption class="caption">Sonic Adventure (1999) for the DC
    <br>1001 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="sonic_dx" >}}
    <figcaption class="caption">Sonic DX (2003) for the GC
    <br>1,993 triangles</figcaption>
  </div>
{{< /side_by_side >}}

{{< /tab >}}
{{< /tabs >}}

It's really impressive how much detail has been gained in just two years, eh?

#### Creativity

As you can see from the inner working of this pipeline, graphics technology has been evolving to point that programmers can now take control of certain functions of the rendering process.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="wind_waker.png" alt="Zelda Wind Waker" >}}
  <figcaption class="caption">The Legend of Zelda: The Wind Waker (2003)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
During the same time, PC graphics cards were starting to discard fixed-function pipelines in favour of **shader cores** (units that run small programs which define how pixels are operated). Flipper is still a fixed-function GPU, however by including components such as the TEV unit, one could argue that Nintendo provided their own shader-like solution.

I guess one of the best examples of games that exploited this new capability is **The Legend of Zelda: Wind Waker** which implements a unique colour/lighting technique known as **Cel shading** to make its textures look *cartoonish*.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Video output system

The video signal outputs a resolution of up to 640x480 pixels (or 768×576 px in PAL) with up to 16.7 colours (24-bit depth). Additionally, the system could broadcast its signal in **progressive mode** (which has a clearer image, but not every TV may have supported it during that time).

The XFB can have multiple dimensions, so for compatibility reasons, the Video interface will try its best to display the frame by re-sampling the XFB to fit the TV screen based on the region.

#### Connections

The console included not one, but two video output connectors:

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="av_photo.jpg" alt="A/V photo" >}}
  <figcaption class="caption">A/V Connections on the back</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
- One named **Analog A/V** which is actually the good-old [Multi Out]({{< ref "super-nintendo.md#a-convenient-video-out" >}}). This is the most popular one.
  - The PAL version of this console doesn't carry S-Video and the NTSC one doesn't provide RGB (bummer!).
- Another one called **Digital A/V** which sends audio and video in digital form (similarly to HDMI nowadays but using a completely different protocol!).
  - Nintendo released a component cable set that connected to this socket. The same plug incorporated a video DAC and encoder to convert the digital signal into YPbPr (optimal quality).
  - The cable was sold as an extra accessory and now is considered some sort of a relic!
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Audio

Nintendo finally delivered some dedicated audio circuitry to offload the huge task from the CPU-GPU and provide richer sounds. Their new solution is an independent **Digital Signal Processor** or 'DSP' manufactured by **Macronix** running inside Flipper.

The DSP's job consists in performing different operations over our raw audio data (e.g. volume changes, sample rate conversion, 3D sound effects, filtering, etc) and at the end output a 2-channel sample. It doesn't work alone however, the DSP delivers audio with the help of other components.

Its first companion is the **Audio Interface** or 'AI', a 16-bit stereo digital-to-analogue converter responsible for sending the final sample through the audio signal that ends on the TV. The AI can only process 32 bytes of audio data every 0.25ms, so if we take into account that each sound sample weights 2 bytes and we need two to create stereo sound, the AI will be able to mix up to eight stereo samples with up to 32 kHz of sampling rate, *sound*!

Finally, we have the **Audio RAM** or 'ARAM' block, which is a large (16 MB) but very slow spare memory that can be used store raw sound data. There's quite a lot of space, so the GPU can also use it store additional material (like textures). The CPU doesn't have direct access to this memory so it will resort to DMA to move content around.

That being said, the process of generating sound works as follows:
1. CPU commands DMA to move raw samples to ARAM.
2. CPU sends a list of commands that instruct how the DSP should operate these samples.
3. DSP fetches samples from ARAM, applies the required operations and mixes them into two channels. Finally, it stores the resulting data on RAM.
4. AI fetches processed samples from RAM and outputs it through the audio signal.

#### Compression and freedom

While we've already reached the *sampling age* and we are not locked to specific waveforms any more, the new sound system is still a huge improvement. Let me show you an example using two games, one released for the Nintendo 64 and its sequel released for the GameCube. Both have different music scores but the context (enemy battle) is the same. Take a look at how both tracks differ in sound quality, taking into account the design of each system (shared vs dedicated).

{{< side_by_side >}}
  <div class="toleft">
    {{< video src="PaperMario64" >}}
    <figcaption class="caption">Paper Mario (2000) for the N64</figcaption>
  </div>

  <div class="toright">
    {{< video src="PaperMario" >}}
    <figcaption class="caption">Paper Mario: The Thousand-Year Door (2004) for the GC</figcaption>
  </div>
{{< /side_by_side >}}

*As you can hear*, dedicated audio hardware is a drastic improvement, allowing for a wider range of frequencies, sampling rates and additional effects.

---

## I/O

It seems that this generation is putting a lot of work in expandability and accessories, the Gamecube included a couple of new interesting ports, although some of them remained unused.

#### Internal I/O

Flipper is in charge of interfacing the CPU with the rest of components so apart from including sound and graphics circuitry, it also provides a collection of hardware named **(internal) Northbridge** composed of:
- **Audio Interface** or 'AI': Connects the Audio Encoder.
- **Video Interface** or 'VI': Connects the Video Encoder.
- **Processor Interface** 'PI': Connects the CPU (Gecko).
- **Disk Interface** or 'DI: Connects the DVD controller, which can be considered an independent computer per se.
- **Serial Interface** or 'SI': Connects four possible controllers using a proprietary serial protocol.
- **External Interface** or 'EXI': Connects the external accessories (more explained below), Memory Card and the IPL BIOS along with the Real-Time Clock or 'RTC'. It uses an SPI-like protocol to connect these devices using only one serial node.

Each interface includes its own set of registers that allows to alter some of its behaviour.

#### Optional I/O

On the bottom of the Gamecube's case you'll find two external sockets to connect some widgets.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Covered" active="true" >}}
      {{< linked_img src="accessories_covered.jpg" >}}
    {{< /tab >}}
    {{< tab name="Uncovered" >}}
      {{< linked_img src="accessories.jpg" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Accessory slots at the bottom of the case</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Both are technically identical (serial bus running at 32 MHz), yet they are presented with a different external shape in order to accommodate different accessories:

- **Serial Port 1**: Nintendo shipped Modem and Broadband adapters for this slot.
- **Serial Port 2**: No public accessory shipped using this port, but you may find some third-parties accessories that provided debugging tools.

These ports are operated on the EXI stack.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Unusual I/O

You'll notice I still haven't mentioned another available socket found next to the serial ports: The **Parallel Port**. This port happens to be much faster (8-bit at 80 MHz vs 1-bit at 32 MHz) which may be the reason Nintendo called it **Hi-Speed Port**. But the most unusual part is that this port is not interfaced through EXI, but through ARAM!

The only official accessory known to date is the famous **Game Boy Player** which plugged in as an extra floor under the Gamecube, it contained the necessary hardware to natively play [**Game Boy**]({{< ref "game-boy">}}) and [**Game Boy Advance**]({{< ref "game-boy-advance">}}) games. The Player works by doing all the heavy work itself and then sending the results (frames and audio data) to ARAM which the GameCube forwards to the respective components for display/sound.

#### Consistent design

I found it worth pointing out that no matter how many accessories you connect, the console will always keep its cubic shape (or at least attempt to).

---

## Operating System

Upon turning on the console, the CPU will start loading an operating system called **Dolphin OS** found on the BIOS/IPL chip, this is a very minimal OS that will take care of initialising the hardware and providing some convenient system calls and global variables for games to use. Games developed using the official SDK will implicitly execute these calls during low-level operations.

{{< centered_container >}}
  {{< linked_img src="ipl/splash.png" alt="Gamecube Splash screen" >}}
  <figcaption class="caption">The official logo, shown after the boot animation finishes</figcaption>
{{< /centered_container >}}

#### Splash and shell

After finishing the boot process, the OS will load a small program *unofficially* called **Main Menu**.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Menu" active="true" >}}
      {{< linked_img src="ipl/menu.png" >}}
    {{< /tab >}}
    {{< tab name="Clock" >}}
      {{< linked_img src="ipl/clock.png" >}}
    {{< /tab >}}
    {{< tab name="Options" >}}
      {{< linked_img src="ipl/sound.png" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">Main Menu with multiple settings available</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
This program is responsible for displaying the famous splash animation (the wee cube drawing a Gamecube logo) and loading the game if there is one inserted. In the absence of a *valid* game, it will then provide a simple cube-shaped menu with various options to choose from:

- Change Date and Clock.
- Load the game from the DVD reader.
- Manage saves on any Memory Card.
- Change sound settings and screen position.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Games

Nintendo provided developers with lots of tools to assist (and encourage) the development of games for their console:

- **Dolphin SDK**: The official set of APIs and useful libraries.
- **C** and **C++** Compilers.
- **Debuggers** and **Testers**: Meant to be used with an official Dev Kit.
- **Cygnus**: Now known as 'Cygwin', it's basically used to replicate the UNIX environment on Windows.
- **CodeWarrior**: A de-facto IDE *back in the days*.
- Various **assistance tools** such as MusyX, Texture editors, Display Lists exporters, USB programmers, etc.
- Tons and tons of **documentation**! (available in PDF and HTML)

#### Specialised hardware

Apart from the software, the company supplied different hardware kits (which range in price) before and after the console was publicly released.

Probably the most popular one worth mentioning is the **Dolphin Development Hardware** or 'DDH' which consisted in a PC-like tower containing some of the Gamecube's I/O and lots of dev-assisting hardware, it was mainly used as a debugging station while the game was being developed on a Windows PC.

#### Medium

Games are loaded from a proprietary disc called **miniDVD**, it's almost half the size of a traditional DVD disc and can hold up to 1.4 GB of data.

As an interesting fact, the disc reader operates in a **Constant Angular Velocity** or 'CAV' meaning that its data will be read at a faster rate if its found in the outer area of the disc (3.125MB/s) and slower if it's found in the inner area (2MB/s). This differs from **Constant Linear Velocity** systems (used by traditional CD/DVD readers) where the effects are the opposite.

Game saves are stored in a proprietary external accessory called **Memory Card** and there are enough slots for two of them.

#### New controllers

Nintendo shipped an accessory known as the **GameBoy Link Cable** which plugged a [**Game Boy Advance**]({{< ref "game-boy-advance">}}) into the GC controller port, so games could upload small programs to the GBA and treat it as a special controller. This interesting feature enabled unique interactions and content in some games.

#### Online Platform

Well, unlike [the competition]({{< ref "dreamcast">}}), not only Nintendo required users to buy extra accessories to access online content, but they also didn't deploy any internet service that publishers could rely on, making developers solely responsible for providing the necessary internet infrastructure.

As a result, while online gaming was a possible feature, it didn't get widely adopted and only a tiny amount of games made use of this.

---

## Anti-Piracy & Homebrew

Nintendo has been in this game for quite some time, so it's no news that they included security mechanisms to prevent running unauthorised software or games from a different region. Furthermore, due to the new range of I/O that the Gamecube provides, the attack surface got significantly larger. So we can organise it into these areas:

{{< tabs >}}

{{< tab name="DVD controller" active="true" >}}{{% inner_markdown %}}

Even though this is the first Nintendo console to use the disc medium, attempting to play pirated copies of games just wasn't going to be easy. The miniDVD is protected by using proprietary bar-codes on the inner side of the disc, in addition to having its data encrypted. The validation and decryption process works seamlessly: The miniDVD controller takes care it while the system is limited on only requesting the data.

The hardware composing the DVD reader can be imagined as a fortress wall which is only accessed using a series of commands, the miniDVD controller features a proprietary CPU that will take care of deciding if the inserted disc is genuine or not, and if it's not, no command issued by the main CPU will convince to read it otherwise.

#### Defeat

As any other cat-and-mouse game, it was just a matter of time before third-party companies successfully reverse-engineered the controller to build mod-chips that could trick the reader. But bear in mind that no mod-chip will make this console magically fit a conventional CD/DVD without physically altering the case!

{{% /inner_markdown %}}{{< /tab >}}

{{< tab name="IPL and EXI" >}}{{% inner_markdown %}}

Another possible path of exploitation consists in using the external I/O available to load Homebrew programs. Although, without cracking the DVD reader first, the only other option available is to try to take control of the first program that the Gamecube loads, and that is... The IPL.

That means that by reversing engineering the BIOS and replacing the chip with a modified one, one would be able to run, let's say, a file reader, and from there execute programs received from the accessory ports (assuming the additional hardware is plugged in).

Although at first this is not that simple: The IPL chip is encrypted using XOR conditionals and a Ciphertext, making it 'impossible' to reverse engineer.

#### (Second) Defeat

Some people eventually discovered that the hardware that handles the decryption of the IPL contained a bug which allowed to capture the Ciphertext used. With this, another ROM could be constructed and encrypted with the same cypher so the Gamecube boots it as its own!

As if that wasn't enough, hackers discovered methods to trick the miniDVD reader into loading conventional DVDs.

{{% /inner_markdown %}}{{< /tab >}}

{{< tab name="Honourable Mention" >}}{{% inner_markdown %}}

Before those two mechanisms were discovered, there was actually a much simpler way of loading arbitrary code without any modification whatsoever, it simply consisted in **hijacking the online protocol**.

Some games like *Phantasy Star Online* implemented their own online functionality, this could be updated by downloading an updated executable (DOL file) from the company's servers, so as you can see, this was a man-in-the-middle attack waiting to happen...

Long story short, by spoofing a fake server the Gamecube would just download (and execute) whatever DOL you could provide. That means hackers only needed the original game and the broadband adapter. This technique is known as **PSOload**.

{{% /inner_markdown %}}{{< /tab >}}
{{< /tabs >}}

---

## That's all folks

{{< centered_container >}}
  {{< linked_img src="my_gamecube.jpeg" alt="My Gamecube" >}}
  <figcaption class="caption">My old Gamecube recently rescued from the attic
  <br>I bought it just cause I needed the controller for the Wii
  <br>(back then it was cheaper to buy the whole second hand lot!)</figcaption>
{{< /centered_container >}}

Well, this is it, the **10<sup>th</sup> article**!

I really tried to set a rough limit on the length of this article but you have to understand, technology has gotten *so complex* that if I accidentally skip anything important, the whole topic gets impossible to follow.

Anyway, I'd like to thank the #dolphin-dev irc community for helping me understand the complicated pipeline of Flipper, these guys have been developing the Gamecube emulator for quite some years now and it's really impressive how much they had to put up with.

And finally, please consider [contributing]({{< ref "support" >}}) if you found it an interesting read. I strive to make it as complete as I can, and in the progress I forget how much time it's suddenly costing me, I find it a good investment nonetheless.

Until next time!  
Rodrigo.

---

## Sources / Keep Reading

#### General / CPU

- [**Unofficial yet very complete Hardware Documentation**](http://hitmen.c02.at/files/yagcd/yagcd/)
- [**Hardware analysis by Anandtech**](https://www.anandtech.com/show/858/2)
- [**Gamecube technical specifications (Wikipedia)**](https://en.wikipedia.org/wiki/GameCube_technical_specifications)
- [**Great Game Developers Conference 2002 Talk**](https://www.gdcvault.com/play/1022547/Nintendo-GameCube-Programming)
- [**Old Ars Technica article describing the PowerPC 750**](https://arstechnica.com/features/2004/10/ppc-2/)
- [**List of hardware acronyms**](https://www.gc-forever.com/wiki/index.php?title=Hardware_Acronyms)
- [**(Linux) Kernel report of the GC device tree**](https://www.kernel.org/doc/Documentation/devicetree/bindings/powerpc/nintendo/gamecube.txt)

#### Graphics

- [**Ace's Hardware (Archived)**](https://web.archive.org/web/20040208044032/http://www.aceshardware.com/read.jsp?id=60000288)
- [**Programming Flipper using libogc**](https://devkitpro.org/wiki/libogc/GX)
- [**Stanford's guess lecture with ATI's director**](http://www.graphics.stanford.edu/courses/cs448a-01-fall/lectures/tvh/tvh.2up.pdf)
- [**Good introduction about shaders in general**](https://gamedevelopment.tutsplus.com/tutorials/a-beginners-guide-to-coding-graphics-shaders--cms-23313)
- [**3D Pipelining**](https://www.extremetech.com/computing/49076-extremetech-3d-pipeline-tutorial)
- **Nintendo Graphics Library (GX) manual**
- [**Graphics capabilities**](http://www.nintendoworldreport.com/guide/1786/gamecube-faq-ngc-graphical-abilities)
- [**Games with different EFB-XFB behaviour**](https://dolphin-emu.org/blog/2017/11/19/hybridxfb/)
- [**More about shaders and emulation**](https://dolphin-emu.org/blog/2017/07/30/ubershaders/)

#### Audio

- [**Emulating the Audio Subsystem**](https://blog.lse.epita.fr/articles/38-emulating-the-gamecube-audio-processing-in-dolphin.html)

#### Games

- [**List of development software**](http://web.archive.org/web/20191119113112/https://assemblergames.com/threads/dolphin-sdk-compilers-and-cracks-tools-and-more.69956/)
- [**List of development hardware**](http://web.archive.org/web/20191113075154/https://assemblergames.com/threads/comprehensive-list-of-gamecube-dev-stuff.29487/)
- [**Gamecube Online functionality (Wikipedia)**](https://en.wikipedia.org/wiki/GameCube_online_functionality)
- [**PSOLoad**](https://www.gc-forever.com/wiki/index.php?title=PSOload)

#### Photography

- Console: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Motherboard: [**Bilgisayarlarım Gallery** (Archived)](https://web.archive.org/web/20180831022041/http://bilgisayarlarim.com/Nintendo/Gamecube_(Resident%20Evil)/index.html) (I removed the background)
- Diagrams, non-studio photos and game screenshots: **Me and my terrible camera** (I promise I'll get something better!)