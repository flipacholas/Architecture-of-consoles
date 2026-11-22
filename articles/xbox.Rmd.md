---
short_title: Xbox Architecture
long_title: Architecture of the Xbox
name: Xbox
subtitle: A feared latecomer
date: 2020-06-26
release_date: 2001-11-15
cover: "xbox"
generation: 6
seo_image_pos: "Bottom"
top_tabs:
  Model:
    latex_height: 90
    file: international
    caption: "The original Xbox.<br>Released on 15/11/2001 in America, 22/02/2002 in Japan and 14/03/2002 in Europe."
  Motherboard:
    caption: "Showing first revision.<br>Controllers connect via a separate daughterboard.<br>Missing SDRAM chips are on the back and there are four unpopulated SDRAM slots."
  Diagram:
    caption: "Each controller is connected to a separate USB hub."

# Historical
aliases: [/projects/consoles/xbox/]
---

## A quick introduction

It seems that Microsoft has decided to pick up where Sega left off. Their offering? A system that combines familiar components appreciated by developers, with online services welcomed by users.

### More precise units

To keep consistency with its sources, this article distinguishes storage units between the metric prefix (i.e. megabytes or 'MB') and the standardised binary prefix (i.e. mebibytes or 'MiB'), thus:

- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

... and so forth.

### Reading tips {.interactive-only}

Some months after finishing this write-up, I've realised this is one of the densest write-ups I have done. There's just a lot of stuff happening inside the Xbox and I have tried to mention most of it.

Now, if you're really interested in understanding this system but find this article difficult to follow, my advice to you is: **take your time** - the article is not going anywhere. Focus on what you like, read at your own pace, check out the links on the 'Sources' section for support and, finally, don't put pressure on yourself - there's no exam!

## {.supporting-imagery}

## CPU

The processor included in this console is a slightly customised version of the famous **Intel Pentium III** (an off-the-shelf CPU for computers) running at **733 MHz**. With this, one can assume this console is just a PC behind the scenes... I don't want to spoil it, but I promise that by the end of the article you will be able to reach your own conclusion.

![The Intel Pentium III package on the Xbox's motherboard.](cpu.jpg){latex_width="90%"}

Anyhow, Pentiums, along with other lines of CPUs designed and manufactured by Intel, were incredibly popular in the computer market. Such was Intel's market dominance that they became the de facto benchmark for quality: as an average user, if you wanted a good computer and had the budget, you only had to look for *something* carrying an Intel CPU. We all know by now that there are many other factors involved, but that was the image the marketing team at Intel managed to project.

### The technical aspect

Now that we positioned Intel on the map, let's go back to the console itself. During my research, I expected to find documentation with the same level of depth as other CPUs (e.g. MIPS, SuperH, ARM, etc). Instead, I stumbled across an excessive amount of marketing jargon that only diverted my search. So, for this article, I've put together a structure to organise all the necessary information to understand how this CPU works. Furthermore, I will also explain the terminology that Intel used to market this CPU.

Having said that, let us take a look:

#### Branding {.tabs .active}

![How this study is organised.](_diagrams/cpu/branding.png){.tab-float}

First things first, the Xbox's CPU is identified as a **Pentium III**, but what does that tell us? Back in the early noughties, the Pentium series represented the next generation of CPUs. They were the 'new high-end' that brought together all the fancy technology that made computers super fast. Plus, it helped buyers decide which CPU to look for if they wanted *the best of the best*.

The Pentium III replaced the Pentium II, which in turn succeeded the original Pentium. Before that, the first Pentium replaced the 80486, which in turn succeeded the 80386... You get the idea. What matters here is that 'Pentium' is mainly a brand name, rather than something directly associated with the CPU's inner workings. Therefore, we must dig deeper!

To dive further and not get lost along the way, I have catalogued the information into three sections which, combined, make up the chip. These are: 

- The **Instruction Set Architecture** (ISA): the group of instructions used to command the CPU.
- The **Microarchitecture**: how the ISA is implemented in silicon.
- The **Core**: the collection of components that package the microarchitecture to form a specific CPU model.

#### The ISA {.tab}

![An Intel 80386 (1985) - or compatible - was the minimum requirement for mid-90s applications.](photos/i386.webp){.tab-float latex_width="90%"}

Indeed, once I mention 'Intel', it's only a matter of time before I introduce none other than **x86**, its instruction set.

The first form of x86 debuted with the release of the **Intel 8086** in 1978, the 16-bit successor of the [8080](nes#a-bit-of-context). Back then, its design was tackling the same needs and limitations as its competitors, namely the [Motorola 68000](mega-drive-genesis#powering-a-decade). What Intel engineers didn't anticipate, however, was that the 8086 family would carry on for so long!

After the 8086, the PC/Windows market just kept expanding, prompting Intel to release incremental upgrades (the *80186*, *80286*, and so on) and subsequently expand the ISA with more instructions [@cpu-thompson]. Consequently, x86 compatibility started to fragment as more groundbreaking features were added (such as 'protected mode' and 'long mode'). To tackle this, x86 applications in the mid-90s commonly targeted the *80386* ISA (also called **IA-32** or **i386**) as a baseline. Among other things, this standardised the use of a **32-bit environment**.

In the meantime, Intel kept enhancing IA-32 through the use of **extensions**. These added new but **optional functions**, meaning they may or may not be included in an IA-32 CPU. Thus, programs had to query the CPU to check whether a particular enhancement is present. The Xbox's CPU includes two extensions:

- **MMX** (Multimedia Extension): Adds 57 [SIMD instructions](sega-saturn#revised-methodologies) and eight 64-bit registers for integer operations, enabling to speed up vector operations.
- **SSE** (Streaming SIMD extension): Another SIMD extension, this time addressing the criticism of MMX (lack of floating-point number support and inability to operate the floating-point unit in parallel). It adds 56 instructions and eight 128-bit registers (called 'XMM'), each capable of holding four 32-bit `floats`.

The good news is that, since the console will always have the same CPU, programmers can optimise their code to take full advantage of these extensions, knowing that they will always be present.

#### The Microarchitecture {.tab}

When it comes to designing a circuit that can interpret x86 instructions, Intel has come up with many different microarchitectures for their CPUs. Some debuted alongside a new Pentium generation (e.g. *Pentium 4*), while others first appeared in an 'enhanced' version of a Pentium (such as the *Pentium Pro*). Nevertheless, since the introduction of the first Pentium, the CPU model and its underlying microarchitecture no longer share the same name. For example, the 80486 uses the 80486 microarchitecture (and no other), whereas the original Pentium is based on the 'P5' microarchitecture.

Now, the Xbox CPU, like other Pentium III processors, uses the **P6 Microarchitecture** (also known as 'i686'). This represents the 6th generation (counting from the 8086) and features:

- A *massive* **14-stage pipeline**: Meaning up to 14 instructions can be processed in parallel. On the other hand, individual instructions may take several cycles to complete, which explains why those 733 MHz don't make this CPU faster than others. See [my previous introduction to pipelining](game-boy-advance#tab-2-1-the-core) if you want to know more.
- **Out-of-order execution**: When possible, the CPU reorders the sequence of instructions to improve efficiency and performance.
- **Dynamic execution**: The P6 is both an out-of-order and [superscalar](dreamcast#the-offering) design. Thus, the traditional [branch predictor](gamecube#the-powerpc-gekko) has been combined with other techniques called 'speculative execution' and 'data-flow analysis' to help reduce pipeline stalls even further.

Before moving on, take a closer look at these features. They look familiar, as similar techniques have shown up in [previous consoles](gamecube#the-powerpc-gekko). However, the underlying design of the other CPUs differs considerably from Intel's. Historically, one could argue that the limitations of x86 would've never allowed Intel to produce, let's say, a pipelined CPU. Yet they managed to do just that, so let us see why and how...

#### CISC or RISC {.tab}

![Intel's Pentium Pro (1995) debuted the P6 microarchitecture, bridging the gap between CISC and RISC advancements in the x86 world. This came as a surprise to many - particularly those who believed x86's days were numbered.](photos/pentiumpro.webp){.tab-float latex_width="90%"}

The competition happens to feature CPUs designed around the [**RISC guidelines**](playstation#tab-1-1-a-bit-of-history), whereas Intel's x86 line inherits the old teachings, thereby being placed in the **CISC** group. RISC CPUs are known for having an intentionally simplified design compared to CISC processors. This includes, for instance, implementing a **load–store architecture**, which only provides instructions that operate on values from registers (as opposed to operating directly from memory).

One advantage of RISC CPUs is that their simplistic approach lends itself to more **modular designs**. This, in turn, can be exploited to implement **parallelism techniques**, improving performance. This is why we have seen CPUs like [MIPS](playstation-2#a-special-order-for-sony) and [PowerPC](gamecube#the-powerpc-gekko) debuting pipeline stages, superscalar designs, out-of-order execution, branch prediction, etc. Now, to be fair, CISC processors predate the RISC wave, and they aimed at solving different needs. Consequently, their designs are not as flexible as RISC CPUs.

Back to the original question, the P6 is an interesting design, because while this CPU only understands a CISC instruction set (x86), a subset of its opcodes is interpreted using **microcode** (small instructions that tell the CPU how to execute complex ones). Most importantly, the unit that executes microcode **follows the load-store model** [@cpu-gwennap]. This is because the P6 architecture was authored by the former engineers of the **Intel i960** (a once-promising RISC CPU). All in all, this enabled Intel to incorporate advantages associated with RISC processors without sacrificing compatibility with the established x86 ISA. It's fair to say that as time went on, terms like 'CISC' and 'RISC' have become too ambiguous when used to categorise any modern CPU.

As a side note, microcode is already embedded in the silicon, but it can also be **patched**. This allows Intel to fix bugs or security vulnerabilities after their CPUs leave the production line. If you have read my previous articles (i.e. [N64](nintendo-64) or [PS2](playstation-2)), bear in mind that Intel's microcode is **not publicly accessible** (let alone documented) and Intel is its sole maintainer.

#### The Core {.tab}

![The Coppermine design.](_diagrams/cpu/core.png){.tab-float}

Intel shipped numerous chips that implemented the P6 microarchitecture. The Xbox's choice is **Coppermine**, a model that belongs to the second revision of the Pentium III (replacing the *Katmai* core) and features the following components:

- **32 KiB L1** cache: Divided equally between 16 KiB for instructions and 16 KiB for data.
- Integrated **128 KiB L2** cache: This is rather odd since the off-the-shelf Coppermine came with 256 KiB of L2 cache [@cpu-anand]. In fact, the *Coppermine-128* variant (sold under Intel's *Celeron* brand, the budget alternative) had the same amount of L2 cache [@cpu-shvets]. It seems Microsoft chose the latter to reduce manufacturing costs and keep this console at a competitive price.
- **133 MHz Front-side bus**: Connects the L2 cache to the outside bus (called 'Northbridge' - we'll explore this more later).
  - Intel uses the term 'Front-side bus' to distinguish it from another internal connection called 'Back-side bus', which connects the CPU core to the L2 cache.

Coppermine also introduced two 'enhancements' to its original L2 cache architecture: these are the **Advanced Transfer Cache** and the **Advanced System Buffering**. To sum them up, L2 cache is integrated inside the CPU chip and their buses are wider, helping reduce possible bottlenecks in the Front-side bus.

Finally, the original chip relied on the 'Micro-PGA2' socket to fit into a PC motherboard. With the Xbox, the CPU is soldered directly using a Ball Grid Array (BGA) package.

### P6 and the end of Pentium numbers {.tabs-close}

Here's a bit more history. After the years of the P6, Intel planned to succeed it with the *NetBurst* microarchitecture (featured in the *Pentium 4*). However, the line of succession also ended there. Even though NetBurst implemented many contemporary techniques [@cpu-netburst], it also suffered from excessive power consumption, heat emission (an average TDP of 85 W [@cpu-pentium4]) and scalability issues - all of which impeded the design from being continued any further.

Consequently, Intel turned its attention to a team in Haifa, Israel which had been developing a low-power P6 derivative - the **Pentium M** - and would now be tasked with delivering a new line of processors that prioritised performance per watt. The first result was the **Yonah** core, a slight improvement of the P6 design. Yonah was released in 2006, and reached the shelves as **Core Solo** or **Core Duo**. With it, power dissipation dropped to reasonable numbers (between 27 W [@cpu-coresolo] and 31 W [@cpu-coreduo], depending on the variant).

Months later, Intel introduced the **Core microarchitecture**; it became the flagship successor of P6 (and NetBurst) and, confusingly enough, was marketed under the **Core 2** brand. Over the following years, subsequent microarchitectures improved on many aspects, but they also managed to re-incorporate forgotten elements from NetBurst without repeating the same mistakes.

On a curious note, I wonder whether the naming of the Yonah core is meant as a metaphor for the *Book of Jonah*, particularly connecting the event where the prophet, in the end, managed to correct his path to save the city of Nineveh. Though it's also possible I'm overthinking this.

### Motherboard architecture

By the late 80s, PC motherboards had grown so much in complexity that new designs were developed to efficiently tackle growing needs.

The final standard wasn't settled overnight, but gradually emerged as multiple manufacturers kept simplifying their designs and integrating multiple circuits. The outcome was a model that relied on **two dedicated chips** to handle most of the motherboard's functions:

- The **Northbridge**: Serves as a memory controller and interfaces the GPU.
- The **Southbridge**: Interfaces the rest of I/O (e.g. USB, ATA/SATA, PCI, etc).

The combination of these chips is called **chipset**, and it plays an important role in determining the capabilities and performance of a motherboard.

![Overview of Xbox Motherboard.](_diagrams/cpu/motherboard.png)

The Xbox, being so close to a PC, includes two chips as well:

- The **NV2A**, a combination of Northbridge and GPU.
- The **MCPX**, which handles the remaining I/O.

Both chips were designed and manufactured by **Nvidia**, and are interconnected through a specialised bus called **HyperTransport**. It's worth pointing out that some PC motherboards also included this technology, just with a different name (*nForce MCP-D*).

### Memory layout

The Xbox includes a total of **64 MiB of Double Data Rate (DDR) SDRAM**, using four 16 MiB banks. This type of RAM is very fast (400 MHz) compared to what the competition offered, so it is no wonder why DDR emerged as the de facto leader of PC memory, spawning numerous generations and variants since then.

Moreover, the Northbridge is optimised for this arrangement by adopting **burst transfers**, where data transfer is done in batches of 128 bits. This is possible because each RAM slot is connected to a 32-bit data bus.

Be that as it may, the Xbox's RAM is also shared across all components of this system. Hence, once more, we find ourselves in front of another [**Unified Memory Architecture**](nintendo-64#simplified-memory-access) or 'UMA' layout.

#### Unused space

If you look closely at the Xbox's motherboard, you'll notice that it actually includes **eight bank slots**. The memory layout I described is still correct, with the addition that each bank pair shares the same buses. I presume this is a remnant of the original 128 MiB configuration that shipped with development kits. The consumer model populates the chips in a way that each bank keeps its own dedicated bus.

![Memory chips around the NV2A GPU/Northbridge [@photography-amos]. This motherboard revision includes eight memory sockets (4 on the front, 4 on the back), but only half are populated.](memory.webp)

As DDR SDRAM is relatively easy to source, the homebrew community has published modification guides to install the extra banks and restore 'dev kit' capacity. The only limitation is that retail games have been designed to expect 64 MiB and won't take advantage of the extra bits.

One thing to note is that, because memory pairs share the same buses, the timings differ between the four and eight-bank configurations. So, the Xbox always operates on the slowest speed, a common denominator to avoid having two different variants of this console.

#### Improving memory flow

We have previously seen how [troublesome](playstation-2#preventing-past-mishaps) UMA can sometimes be. Nonetheless, Microsoft and Nvidia alleviated its issues by implementing burst transfers. Moreover, the NV2A also implements a **switching network** that arbitrates access to the memory banks among different units (CPU, GPU, etc), allowing them to access separate banks concurrently [@cpu-huang] [@cpu-informit].

![Representation of the switching network. The GPU uses two banks while the CPU uses a different one, reducing contention in the process.](_diagrams/cpu/memory.png)

Later in this article, you'll see that the console also features an **internal hard disk**, but for now it's worth pointing out that the HDD includes **three partitions of 750 MiB**, each reserved for **temporary storage**. The CPU can offload some of its data from main RAM to the HDD, and retrieve it whenever needed. Bear in mind this is a manual process and does not involve [virtual memory](gamecube#making-the-most-of-aram).

## Graphics

As we've seen before, the graphics processor resides in the NV2A chip and, just like the MCPX, it is manufactured by Nvidia.

![The Nvidia NV2A package on the Xbox's motherboard.](gpu.jpg){latex_width="90%"}

This company has been in the graphics business for a long time. Their *GeForce* series remains one of the most popular GPU brands in the PC market, competing directly with the Radeon series (then associated with [ArtX/ATI](gamecube#graphics)). Overall, this gave Microsoft a good head start on the graphics capabilities of the Xbox, considering it was their first attempt at entering the console market.

### An uneasy bet

In hindsight, it's easy to connect the dots in the present, but was it *really* such an obvious decision to make back then? Microsoft had too much at stake, and Nvidia was still a relatively minor player among the established brands (3dfx, PowerVR, S3, etc.). This panorama made the choice much more complex.

For instance, 3dfx's popular *Voodoo 2* series held around 70% of the PC market share by the end of the 90s [@graphics-ign], while Nvidia was struggling to promote adoption of their new *GeForce 256* (the first of the GeForce series). In that context, Microsoft's choice sounds more like a gamble than a safe bet, but as we know now, this risk eventually paid off.

> Nvidia was NOT the #1 player they are now, in 1999. They were in trouble. The new GeForce architecture was still young and lots of people didn’t like it. Now it seems forgone. But you’d need to research that history to know that, and why I had to fight to use it. I was right but I didn’t know I was right; I was very worried.
>
> -- <cite>Seamus Blackley (co-author of the Xbox)</cite>

In the following section, we'll examine the inner workings of this chip. I'm afraid we'll find ourselves mixed in a lot of terminology and marketing jargon, just like the CPU section. But fear not! I will start with the basics.

### Architecture and design

The GPU core found inside the NV2A is based on the popular *GeForce3* series of graphics processors [@graphics-mslusarz] [@graphics-g3arch]; it's also referred to as **NV20** in Nvidia's technical documents.

![Halo (2001) running on the Xbox in 720p mode.](halo.png)

Please note that, while the Xbox's GPU pipeline is based on the NV20 architecture, the NV2A has some modifications that make it incompatible with other NV20-based products. Most importantly, it has been adapted to work in a UMA environment.

![Pipeline design of the NV2A.](_diagrams/gpu/pipeline.png){.open-float}

The units analysed contain a lot more features that go beyond the scope of this article, so I recommend consulting the sources if this section catches your attention. Also, since graphics terminology has evolved considerably over time (which can lead to some confusion), I've decided to stick to the terminology used by Microsoft and Nvidia during the Xbox years. Remember this if you plan to explore other graphics-related articles from other sources.

{.close-float}

With that said, let's take a look at how frames are drawn on the Xbox. Some explanations are very similar to GameCube's [Flipper](gamecube#graphics), so you may benefit from reading that article as well, particularly if you struggle to follow this one.

#### Commands {.tabs .active}

![Commands stage diagram.](_diagrams/gpu/commands.png){.tab-float}

First and foremost, let's explain how the GPU receives commands from the CPU. For this purpose, the GPU contains a command processor called **PFIFO** that effectively fetches and processes graphics commands located in an area known as **Pushbuffer**. These are processed in a First In, First Out (FIFO) manner, and subsequently delivered to **PGRAPH** (the block in charge of graphics processing) and other engines.

Like Flipper, NV2A doesn't require geometry to be embedded in the command itself. PGRAPH provides many ways of submitting graphics data. For instance, the CPU can allocate a buffer in RAM containing vertex data and then instruct the GPU to fetch data from that location. This approach can be more efficient, as it prevents sending duplicated geometry.

From now on, the following explanations focus on the operations handled by PGRAPH.

#### Vertex {.tab}

![Vertex stage diagram.](_diagrams/gpu/vertex.png){.tab-float}

This is a particularly interesting part of this GPU. At this stage, the GPU can apply **vertex transformations** to our geometry. We've already seen similar functionality in Flipper, but unlike that GPU, the NV2A uses a **programmable engine**. This means that developers may specify which vertex operations are performed and how, as opposed to relying on a predefined sequence. Although, the NV2A can also operate in 'fixed' mode when required.

This stage is handled by a **Vertex Unit** and the NV2A features **two of them**. Each can load a program containing up to **136 instructions** (also called **microcode**) [@graphics-vertex_shader]. This program is referred to as a **vertex program** and it's loaded at runtime. A vertex program can perform the following operations [@graphics-koppelman]:

- **Arithmetic operations** (e.g. addition, multiplication, minimum, etc). This includes various 'helper functions' to assist graphics-related tasks, such as dot products.
- **Vertex swizzling** (rearranging and/or duplicating vector components).

In a nutshell, the Vertex Unit processes vertices by manipulating them in its registers:

1. Once a program is loaded, 16 read-only registers (called 'input registers') are populated with the attributes of the incoming vertex (each register holds a four-component vector).
2. Afterwards, the unit performs the set of operations (specified by the program) using these input registers.
    - To assist with the computations, 12 writeable temporary registers and up to 192 constant registers are provided.

Finally, the resulting vertex is written to another block of 11 output registers, each one dedicated to a specific purpose. These are then passed on to the next stage, and the process is repeated for every vertex received.

#### Fragment {.tab}

![Fragment/Pixel stage diagram.](_diagrams/gpu/pixel.png){.tab-float}

At this stage, the GPU performs **rasterisation**, where vertices are transformed into pixels. The process starts with a rasteriser that generates pixels to draw each triangle. The NV2A's rasteriser can generate up to **four pixels per cycle**. <!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while [competitors embedded it]()). -->

Next, **four texture shaders** fetch textures from memory [@graphics-domine]. These also offer to automatically apply **anisotropic filtering**, **mipmapping** and **shadow buffering**. The latter is used to determine whether a pixel is visible or overshadowed by the lighting source, so the correct colour can be applied.

At this point, the GPU also offers to perform **clipping** and an early [Z-test](nintendo-64#modern-visible-surface-determination). The NV2A compresses the Z-buffer to four times its original size to save bandwidth, contributing to a lot of performance improvements.

The resulting pixels are stored in a **set of shared registers** and then cycled through **eight register combiners**, where each one applies arithmetic operations on them. This process is **programmable** with the use of **fragment shaders** (another type of program executed by the GPU) [@graphics-wasson]. At each cycle, each combiner receives RGBA values (Red, Green, Blue, and Alpha) from the register set [@graphics-spitzer]. Then, based on the operation set by the shader, it operates on these values and writes the result back to the register set. Finally, the results are passed to a separate **final combiner**, which produces the final colour but can also incorporate specular highlights (i.e. bright spots) and/or fog.

Register combiners are programmable in a similar fashion to the [Texture Environment Unit](gamecube#tab-3-3-texture). That is, developers alter its registers with a specific combination of settings. In the case of the Xbox, PFIFO reads commands from pushbuffers to set up PGRAPH, which includes the register combiners and texture units.

#### Post-processing {.tab}

![Post-processing stage diagram.](_diagrams/gpu/postprocessing.png){.tab-float}

Before the pixels are written to the framebuffer, the NV2A uses four dedicated engines called **Raster Output Unit** or 'ROP' to perform necessary tests (alpha, depth, and stencil). These operations rely on allocated buffers in main memory. Finally, pixels that pass these tests are then written to the framebuffer, at a rate of four per cycle.

Moreover, the framebuffer can be anti-aliased using a technique called **MultiSample Anti-Aliasing (MSAA)** [@graphics-geforce3]. Essentially, MSAA samples the area of polygons multiple times at different offset positions. Afterwards, all samples are averaged to form a smoother image. This approach replaced the previous (and more resource-hungry) SuperSampling Anti-Aliasing (SSAA), used by previous Nvidia GPUs.

### The importance of programmability {.tabs-close}

I find it pivotal to emphasise just how significant Nvidia's new programmability model was for developers. Years before, much of the work involved in preparing a 3D scene was handled by the CPU, leaving the GPU to accelerate rasterising operations. With the introduction of programmable 'shaders' (referring to both fragment shaders and vertex programs), programmers could take advantage of the resources of the GPU to accelerate many computations in the pipeline, offloading a great amount of work from the CPU.

::: {.subfigures .side-by-side}

![Complex animation achieved by vertex program.](vertex){.toleft video="true" latex_width="75%"}

![Different texture effects computed by fragment shaders.](fragment){.toright video="true" latex_width="75%"}

*ChameleonMark* (2002), a demo developed by Nvidia to showcase GeForce3's shaders.

:::

The concept of 'shaders' was introduced by **Pixar** in 1989 as a method to extend **Renderman** [@graphics-pixar], their pioneering software used for 3D rendering. This was back in the time when 3D graphics were mainly associated with expensive industrial equipment. Later on, we saw how certain consoles incorporated [similar principles](nintendo-64#tab-1-1-reality-signal-processor), but it wasn't until Nvidia released their GeForce3 line in 2001 that shaders became a standard in the consumer market.

#### What lies ahead

Thanks to vertex programs, the GPU can now accelerate model transformations, lighting calculations, and texture-coordinate generation. The latter is essential for composing [higher-order surfaces](playstation-2#infinite-worlds). With this, the CPU can concentrate on providing better physics, AI, and scene management.

In the case of fragment shaders, developers can manipulate and blend textures in multiple ways to achieve different effects, such as multi-texturing, specular mapping, bump mapping, environment mapping, and so on.

A new programming concept that emerges thanks to this approach is the **General-Purpose computing on GPU** or 'GPGPU', which consists of assigning tasks to the GPU that would otherwise be handled by the CPU. So not only has the GPU taken over most of the graphics pipeline, but it can now also act as an efficient co-processor for specialised computations (e.g. physics calculations). This is a new area that will evolve as GPUs become more powerful and flexible. However, the NV2A was already able to achieve this thanks to a combination of hardware capabilities (vertex & fragment shaders) and new APIs developed (OpenGL's 'state programs').

I have a feeling that shaders will feature regularly in future articles. Please remember that in this article, however, they may be considered a bit 'primitive' and some people may argue that fragment shaders are not even 'shaders' (compared to what GPUs offer nowadays).

### Outputting the video

Arriving last in its generation and featuring PC-based graphics hardware, the Xbox offered the best of both worlds when it came to video output quality.

#### The Xbox's frame

Nvidia's chip was internally able to handle 4096 x 4096-pixel framebuffers, not necessarily with acceptable performance, but at a time when the target resolution of games was around 640 x 480 pixels, Xbox titles were in a comfortable position to support the following modernities, which could be toggled system-wide:

- **Progressive scan (480p)**: I've already [discussed this feature in detail](dreamcast#tab-2-1-bridging-the-divide), but it's fair to say that with the Xbox, the majority of games took advantage of it [@graphics-alternates] without suffering any [sudden drop in support](gamecube#the-external-connections) afterwards.
- **'High-definition' output (720p and 1080i)**: These were more experimental and only enjoyed small adoption due to limited TV compatibility and performance implications.
- **Widescreen (16:9 ratio)**: Plenty of games also supported this extended aspect ratio, although its implementation differed depending on the output mode:
    - Standard definition (PAL, NTSC or 480p) was limited to [anamorphic widescreen](wii#tab-1-1-standardised-widescreen).
    - High-definition modes (720p and 1080i), albeit less common, supported widescreen natively.

In charge of this was the Video Encoder, initially made from off-the-shelf parts that changed across motherboard revisions (first by Conexant, later by Focus Enhancements). Late revisions contained in-house circuitry (called *Xcalibur*) [@graphics-encoder].

In any case, the video output supports [Digital Television (DTV)](dreamcast#tab-2-2-the-accompanying-socket) timings, meaning it conforms to modern TV standards.

#### The output socket

Being the first console of Microsoft meant they didn't need to preserve any legacy video connectors. So, what was Microsoft's debut? A **multi-purpose socket** that provides [@graphics-pinout]:

- Stereo audio, in both **analogue** and **SP-DIF** (digital) form.
- **Composite**, **S-Video**, **YPbPr**, and **RGB** video signals.

To use any of these, Microsoft shipped 'AV Packs' containing different adaptors, such as RCA, SCART, and component video [@graphics-av_cables].

![An AV Pack featuring digital audio and component video [@photography-amos].](console/av_adapter.webp){.no-borders latex_width="80%"}

At one point, however, Microsoft experimented with VGA support, but this never made it beyond development kits. Retail units lack the HSync and VSync signals required for VGA output [@graphics-vga].

## Audio

The audio subsystem of this console was heavily influenced by an interesting mix of established and cutting-edge technologies:

- Advanced Technology Extended (ATX) motherboards and their affinity for *AC'97* (Intel's audio standard).
- Microsoft's internal developments, particularly *DirectSound*.
- Professional audio equipment, and its offerings beyond the consumer market.
- The [mistakes](nintendo-64#audio) and [successes](playstation#audio) of competitors, including their [latest ventures](playstation-2#beyond-stereo).

This resulted in two very powerful audio components: the **Audio Processing Unit** and the **Audio Controller Interface**, both integrated into the MCPX package.

### The processor

The Audio Processing Unit (APU) is, as its name indicates, the dedicated audio processor. Microsoft packed a lot of technology into it, which groups into three sub-components [@audio-apu]:

- The **Voice Processor** or 'VP': A specialised circuit that can synthesise 256 voices at a sampling rate of 48 kHz. It also includes two DAHDSR envelope controls and various filters. The processor ultimately mixes these voices and outputs them through 32 channels, with each group of eight channels having its own volume control.
- The **Global Processor** or 'GP': A programmable Digital Signal Processor (DSP) that can apply effects to the audio data coming from the VP.
  - Its use is optional. In fact, it also allows to route a subset of the VP's channels to apply specific effects (such as reverb) exclusively to those.
- The **Encode Processor** or 'EP': Another programmable DSP. It generates the final audio output and stores it in main RAM.
  - Isn't it curious that Microsoft had to build a dedicated DSP for such a seemingly simple task? The answer lies at the end of this section.

### The interface

The APU is responsible for processing audio data, but can't output sound directly. The latter is the job of the Audio Controller Interface (ACI), which reads audio data from RAM, decodes it, and sends the resulting PCM stereo sample (16-bit resolution with a sampling rate of 48 kHz) to the audio output.

### Betting big on surround sound

While competitors offered some degree of surround-sound support through software libraries, Microsoft went a step further by licensing technology from **Sensaura** to accelerate this task using dedicated hardware [@audio-sensaura]. This allowed 64 of the available voices from the Voice Processor to be positioned in a [3D space](playstation-2#beyond-stereo) using **Sensaura 3D Positional Audio** (S-3DPA). It also provided the following effects:

- **EnvironmentFX**: A reverb engine that simulates environmental acoustics, including echoes. Its implementation is compliant with a standard called 'Interactive 3D Audio Rendering Guidelines Level 2.0' (I3DL2).
- **ZoomFX**: To simulate large-scale objects emitting sound, like waterfalls and crowds.
- **MacroFX**: To simulate sounds within a one-meter radius.

The Encode Processor, meanwhile, can encode the resulting audio into a surround-sound signal that follows [Dolby Digital's codecs](playstation-2#beyond-stereo).

#### A battle of egos

Achieving this level of licensing and dedication wasn't without its hurdles. While engineers were busy researching and implementing attractive surround capabilities, the other side of Microsoft wouldn't give in on royalties without a battle. After all, both Microsoft and Dolby believed they had the upper hand in the negotiation.

> Let’s just say that getting two companies, each used to being in a ....very strong.... negotiating position to come to an agreement was challenging. (...) It all came down to one evening. We got a call from a production Manager, who was at the factory in Guadalajara, Mexico. That next morning, they were scheduled to start pre-production of the first prototype Xboxes. They essentially said: “we’ve got two molds for the Xbox outer casing ready to go. One has the Dolby logo on it, one doesn’t. We need to know which one we should use...by tomorrow morning.” [@audio-schmidt]
>
> -- <cite>Brian Schmidt, Xbox's audio architect. </cite>

I think this anecdote paints a good picture of how fragile Microsoft's commitment was to proving itself to developers and consumers.

## I/O

As I mentioned earlier, we have a 'Southbridge' subsystem which concentrates all I/O access. This is implemented by the MCPX chip.

Incidentally, the MCPX is derived from its PC counterpart, called **Nvidia nForce Multimedia and Communications Processor**, or simply 'MCP'. This component was found on motherboards featuring the nForce 220/415/420 chipsets [@io-reactos].

### External interfaces

![Front of the console [@photography-amos], showing the controller ports.](console/front.webp){.toleft .no-borders}

![Back of the console [@photography-amos], showing the power socket, proprietary AV output, and Ethernet port.](console/back.webp){.toright .no-borders}

The console provides the following external connectors:

- **Four USB 1.1 ports**: Used to connect the controllers. Be that as it may, the external shape of the port has been modified to only allow Xbox controllers.
  - These ports also contain an extra pin called 'Video Sync', which allows peripherals to synchronise with the screen's refresh (needed for light guns).
- **10/100BASE-TX Ethernet port**: Used for online services (I explain more details later). The actual Ethernet functionality is handled by a separate transceiver located on the motherboard.

### Internal interfaces

The MCPX also provides plenty of interfaces and protocols to interconnect different subsystems:

- **System Management Bus (SMBus)**: It's a serial interface based on I²C, connecting these components:
  - **System Management Controller** or 'SMC': Manages multiple system services, such as power, temperature, and fan control. Behind the scenes, it's actually a *PIC16LC* microcontroller.
  - **System Temperature Monitor** or 'STM': A digital thermometer (*ADM1032*) used by the SMC to detect overheating.
  - A **256-byte EEPROM**: A re-writable ROM that stores unique identifiers (e.g. serial number, region, Ethernet MAC address, etc.).
  - **Video Encoder**: Although primarily connected to the GPU, it is also controlled through the SMBus.
- **IDE Controller**: This is a standard protocol widely used on PCs to communicate with hard drives, optical readers, and so forth. A single wide ribbon cable connects the motherboard to both the DVD drive and the 8 GB HDD.
- **Low Pin Count (LPC)** bus: Another interface borrowed from the PC architecture, but instead of connecting the *good old* PC BIOS, it communicates with a **Flash ROM**, which in turn stores the equivalent of a BIOS.
    - The Flash is **1 MiB** large (later reduced to 258 KiB in subsequent revisions) and technically **writable**, but the latter only happens during manufacturing [@anti_piracy-steil]. Just before the console leaves the factory, the Flash ROM is placed in a write-protected configuration.

### The controller

The Xbox shipped with a bulky controller called **The Duke**. Its set of inputs wasn't particularly different from what the competition offered... except for the usage of 8-bit analogue circuitry on the face buttons, allowing games to detect 'half-presses' on most of the button set.

::: {.subfigures .side-by-side}

![](controller/duke_front.png){.toleft .no-borders}

![](controller/duke_back.png){.toright .no-borders}

The Duke (2001) [@photography-amos].

:::

In any case, the Duke attracted so much criticism that Microsoft replaced it with a smaller revision called **Controller S**, just months after the console's launch.

::: {.subfigures .side-by-side}

![](controller/s_front.png){.toleft .no-borders}

![](controller/s_back.png){.toright .no-borders}

Controller S (2002) [@photography-amos].

:::

On closer inspection, both controllers reveal something interesting: two **Memory Unit** slots for proprietary memory cards, allowing players to share saves between consoles. Upon noticing this, I instantly assumed this feature had been inherited from a [previous competitor](dreamcast#the-interactive-memory-card), now retired from the console market. However, a few days after publishing this article, I reached out to Seamus Blackley (the co-author of Xbox), who quickly replied with very interesting comments. Regarding the similarities with the Dreamcast, he told me:

> The relationship to Dreamcast is just historical bias. That was accidental.
>
> -- <cite>Seamus Blackley</cite>

#### A required adapter

Another interesting fact about these controllers is that their cable comes with a **breakaway dongle**. This was intended to prevent accidents if someone happened to trip over the cable.

> I think they also planned for where an Xbox was on a high shelf and yanking the controller would pull the whole heavy console down on some kid’s head.
> 
> -- <cite>Xbox user</cite>

#### The other controller

Looking beyond the gaming industry, Dolby also had a stake in the DVD market, thanks to a licensing model that charged royalties for DVD players sold. This wasn't easy to avoid, as Dolby Digital support was part of the DVD video specification.

![The Xbox DVD Movie Playback Kit [@photography-amos].](console/remote.webp){.no-borders latex_width="80%"}

As an adopter of the DVD (and potentially DVD movies), Microsoft formulated an interesting approach to avoid adding this expense to their consoles: rather than including DVD playback software inside the box, they released an optional 'Xbox DVD Movie Playback Kit' that consisted of a standard remote control and an Infrared receiver [@io-remote].

Inside the receiver was a ROM storing the DVD playback software. This was loaded at runtime without any possibility of being installed into the HDD, part of a strategy to provide support for DVD movies without paying royalties per console sold.

## Operating System

All right, let's start by addressing the elephant in the room.

> Does it run **Windows**?

I'm afraid this is a *yes and no* answer. There is a 'Windows' present in this console, but not in the form that conventional PC users might expect.

First things first, the Xbox's operating system is composed of a **Kernel** and **userland applications** (such as the *Dashboard*). These are stored separately: the kernel resides in the 1 MiB Flash ROM, while the Dashboard is stored on the HDD.

The kernel borrows a significant portion of its codebase from **Windows 2000** [@operating_system-os], which itself belongs to the broader **Windows NT** architecture. The result is a stripped-down Windows 2000 kernel that only embeds the necessary components for the Xbox hardware. These are compressed and packaged into a single executable [@operating_system-kernel] for optimal memory efficiency. All in all, you can think of it as a highly optimised Windows machine designed exclusively for gaming.

It's worth pointing out that the Xbox project was headed by the **DirectX team** [@operating_system-renegades]. Thus, its origins are unrelated to the Windows CE team [that brought its APIs to the Dreamcast](dreamcast#windows-ce).

### Boot Process

As with Pentium machines, upon booting up the system, the CPU starts executing instructions found at the **reset vector** (address `0xFFFF.FFF0`) [@anti_piracy-boot]. On the Xbox, this address points to a hidden ROM inside the MCPX (more details are covered in the 'Anti-Piracy and Homebrew' section). The MCPX contains routines that set up the security system and continue the boot process from the Flash ROM. The latter then takes care of initialising the remaining hardware, loading the kernel, and showing the splash animation.

During security initialisation, the CPU switches to **protected mode**. This is critical because x86 CPUs always start in **real mode** to maintain compatibility with the original Intel 8086. However, if programmers need to access modern features (such as being able to access more than 1 MiB of memory), the processor must be switched to protected mode. 

When the kernel loads, it injects microcode into the CPU (not to program it, but rather to **update it**). Finally, the kernel looks for the presence of a valid DVD disc. If one is found, the console launches it. Otherwise, it loads a user-interactive shell stored on the hard drive.

### The green screen

Let's take a look now at the program that the Xbox loads when there isn't a game disc inserted: The **Dashboard**.

#### Interactive shell {.tabs .active}

::: {.subfigures .tabs-nested .tab-float}

![Home screen.](shell/home.png){.active latex_width="80%" title="Home"}

![Settings screen.](shell/settings.png){latex_width="80%" title="Settings"}

The Dashboard offers access to multiple services.

:::

In terms of functionality, the Dashboard is not very different from the [PlayStation menu](playstation-2#interactive-shell), or the [GameCube's IPL](gamecube#interactive-shell). It essentially includes all the functions typical users would expect, like tweaking settings, managing saves, playing DVD movies or audio CDs, and so forth.

One thing worth mentioning is the Dashboard's ability to rip music from audio CDs and store it on the HDD. Games can subsequently access this music to let the player personalise their soundtracks. *Far out!*

#### Updatability {.tab}

Well yes, the software in this console can be updated, but only the contents of the HDD are writable. The kernel part (stored in Flash ROM) can't be re-written. However, the system can patch it after it's loaded in memory. Thus, update packages may include Dashboard revisions and kernel patches.

Even so, some updates managed to introduce new functionality. For instance, the first Xbox units didn't include *Xbox Live* (more about this in the next section), so games bundled the required files in the form of an update to install the service. Other updates focused on fixing security vulnerabilities.

Throughout the Xbox's lifetime, updates were distributed through retail games and could also be downloaded by the system after connecting to the *Xbox Live* network service.

## Games {.tabs-close}

If you've been reading this article series from the beginning, this part may look a bit superficial at first, mainly due to the lack of low-level documentation compared to other consoles. It seemed that Microsoft and Nvidia really wanted developers to use their libraries and leave everything else to them. It's an interesting strategy nonetheless.

In any case, I've tried to make this section as informative as possible, just with a different perspective.

### Development ecosystem

Game development on this console involves a rather complex ecosystem of libraries and terminology. So I organised the information into separate frameworks.

#### Hardware Abstraction {.tabs .active}

![Representation of the Hardware Abstraction Layer (HAL) model.](_diagrams/hal/general.png){.tab-float}

We have seen how elements like a 'programmable coprocessor with microcode' tend to come with a lot of fanfare at first, only for it to gradually dissipate as developers discover the actual complexity of the new hardware.

From the developer's perspective, I believe one of the selling points of this console was the inclusion of popular high-level libraries that could manage low-level functionality efficiently. Microsoft and Nvidia's strategy was to provide a fully-fledged Hardware Abstraction Layer (HAL) rather than requiring developers to understand every low-level aspect of the hardware themselves.

There are multiple Software Development Kits (SDKs) available for developing on this console, some 'official' and restrictive, while others 'not-so-official' but flexible. We'll take a look at the most popular ones.

#### Microsoft XDK {.tab}

![Structure of the Xbox Development Kit (XDK).](_diagrams/hal/xdk.png){.tab-float}

Microsoft's **Xbox Development Kit**, or 'XDK', is the official SDK for Xbox development. It's a package that contains many tools, libraries, and compilers. Most notably, it was designed to work alongside **Visual Studio .NET 2002**, which was *quite an IDE* from that time, for better or worse!

The graphics library is a customised implementation of **Direct3D 8.0**, extended with Xbox-specific features. Direct3D was a very powerful graphics API used to support a wide range of GPUs. With this, PC developers had the opportunity to port their PC games to Xbox without implementing too many changes (at least in theory!). Although, the syntax used to write vertex programs and pixel shaders resembles downright assembly.

Apart from that, audio functionality is provided by **DirectSound**, which takes care of the APU without the need to worry about manually moving audio data.

There are some network libraries as well, making it relatively straightforward to implement online functionality (i.e. multiplayer). The networking API relies on **Windows Sockets** (Winsock), which was Microsoft's attempt to simplify network communications using a Windows-only protocol (albeit having a design based on its standardised counterpart, *BSD sockets*).

To try all of this out, Microsoft distributed their own development kit hardware. These were just retail units tinted in green and equipped with twice as much main RAM (128 MiB in total). Some revisions also featured a different MCPX chip, called MCPX-2, which directly boots from the Flash ROM. The units also ran an enhanced version of the dashboard which launched executables signed by the XDK. On the workstation's side, the **Xbox Neighbourhood** (a Windows application) was used to link the dev kit with Visual Studio, enabling easy deployment and debugging. To experiment with Xbox Live, Microsoft provided sandboxed servers as well.

#### NXDK {.tab}

![Structure of the 'New XDK' (NXDK). Notice that even though some low-level libraries are wrapped by high-level ones, developer can still access them directly.](_diagrams/hal/nxdk.png){.tab-float}

To avoid potential copyright litigation against homebrew developers using Microsoft's SDK, a group of developers unaffiliated with the company created an open-source replacement called **Open XDK**. Some years later, its development stalled, so another group picked it up and continued development under a new name: **New XDK** or 'nxdk'.

The ongoing project reverse-engineered many low-level hardware components (microcode, pushbuffers, etc.) and designed a new API in C/C++ that provides higher-level calls for each portion of the system.

To simplify the graphics layer without relying on Direct3D, nxdk uses the **Cg compiler** [@games-nxdk]. Cg is a programming language created by Nvidia for developing shaders. It is chained with other compilers to generate Xbox-compatible code. During compilation, Cg code is translated into NV20-compatible assembly, which is then parsed by a custom compiler to generate NV2A-compatible microcode and pushbuffer. For those who don't want to use Cg, nxdk also exposes the underlying tools to write low-level shaders.

The remaining APIs handle other services such as audio, networking, and so forth. All in all, nxdk gives greater control of the hardware at the cost of foregoing Microsoft's development amenities. However, nxdk remains the most adequate option for developing legal homebrew programs.

### Medium {.tabs-close}

Following the trend, games are distributed on [dual-layer DVD discs](playstation-2#inside-the-dvd), which can hold up to **8.5 GB**. These are read by a customised DVD drive that includes anti-piracy protections (despite relying on a standard interface, ATA). It's worth mentioning that the XDK included some tools to customise the layout of data on the disc, allowing programmers to improve read performance.

Now, the console also includes an internal **8 GB HDD**. Games use it to store save data and cache temporary content. The system, on the other hand, uses it to store the dashboard, Xbox Live settings, and network configuration.

### Network service

Forget about [telephone connections](dreamcast#the-ambitious-online-platform) or [half-baked services](playstation-2#network-service). The Xbox included everything that we nowadays take for granted to deliver a decent online service: Ethernet connectivity and a centralised online infrastructure, called **Xbox Live**.

![Xbox Live Logo.](shell/live.png)

But what exactly was Xbox Live? Launched in 2002, it was a collection of interconnected online services that Microsoft deployed for game studios to build their online experiences [@games-multerer]. For instance, one of these services provided user profiles, so games could use it as an authentication method when accessing online multiplayer. In the official SDK, Microsoft included APIs to talk with the Xbox Live servers.

All in all, Xbox Live offered game companies the following infrastructure [@games-unveil]:

- Online profiles, known as *Gametags*, and friends lists.
- Matchmaking, either randomly (*Quickmatch*) or based on player skills (*OptiMatch*).
- Downloadable content.

Players paid Microsoft a subscription fee to access Xbox Live. Promotional bundles, like the *Xbox Live Starter Kit*, included a disc with demos, a headset called *Xbox Communicator*, and 12 months of Xbox Live.

The service retired in 2010, allowing Microsoft to focus on their [next-generation offerings](xbox-360#network-service).

### The job of the game developer

It's important to point out that Microsoft controlled whom to grant Xbox Live access to, so developers had to register with Microsoft to obtain the necessary authentication keys.

The actual online experience happened on the **Title Server**: a type of server that answered to clients (Xbox consoles) around the world and handled real-time communication. Microsoft included in their SDK some samples to show how to build these servers, although they relied on Windows-based systems and were intended to be deployed in data centres running Windows Server, the .NET platform, and their C# runtime.

### The start of a new trend

After studying Microsoft's implementation of Xbox Live and the impact it had on the industry, it now sounds pretty obvious, right? It's almost as if the recipe for 'successful online gaming' (a.k.a. Ethernet + Infrastructure) had always been there, but not every company was willing to invest in it.

As it turns out, things weren't that simple. Microsoft also had to convince users they 'needed' this functionality, that online multiplayer wasn't merely an optional addition, but a fundamental part of the gaming experience. Otherwise, Microsoft's efforts might have amounted to another 'online attempt'. 

> Try imagining a world where no console gamer wanted online games, and where nobody believed a PC architecture could be a console. That was really how it was. Now it seems obvious BUT IT WASN'T.
>
> -- <cite>Seamus Blackley</cite>

## Anti-Piracy and Homebrew

Independently of whether this console contains off-the-shelf components or not, there's a fair amount of security measures implemented. Let's take a look.

### DVD copy protection {.tabs .active}

Xbox Game Discs are protected both logically and physically to prevent unauthorised duplication, which shouldn't come as a surprise.

On the logical side, game discs included a couple of 'traps' to trick conventional DVD readers and prevent them from finding the actual game content. For instance, the first area of the disc is formatted like a conventional DVD movie, and inside that section there's a warning message that plays when inserted into a non-Xbox system. In reality, game data resides in a second partition, but the metadata required to locate that partition is not encoded in a format conventional DVD readers recognise, meaning that only a specialised reader (such as the Xbox's) can find the game.

On the physical side, I'm afraid that at this time, it's not publicly known what data the driver and the disc exchange to perform validation. The disc contains an inaccessible inner ring (to conventional readers) that stores unique identifiers, although how this information is used is still unknown.

### System protection {.tab}

The versatility of its I/O and the rich amount of services meant this console could do much more than just read DVDs, making the attack surface significantly larger. Now, this section is interesting but also very extensive, so for a complete study, I recommend reading *Hacking the Xbox* by Andrew Huang and *17 Mistakes Microsoft Made in the Xbox Security System* by Michael Steil. Having said that, let's describe the two main fronts: the bootloader and HDD.

The Flash ROM was specifically chosen to store the bootloader and kernel, while the EEPROM holds the sensitive data unique to the console. Both contents are encrypted using a **Rivest Cipher 4 (RC4) key** [@anti_piracy-boot]. The kernel is hashed with SHA-1 to confirm it hasn't been tampered with [@anti_piracy-paul], and after the kernel boots, it will only execute binaries signed by Microsoft using **RSA**, an asymmetric cryptographic system in which only Microsoft possesses the private key.

Microsoft also protected its most exposed component, the 8 GB ATA hard drive, by implementing the following security methods in the kernel:

- **Password locking**: The ATA specification allows drives to be password-locked and will refuse to answer to commands until the correct password is entered. So, the Xbox's HDD comes 'locked' with a password derived from a unique key in EEPROM and the HDD's model and serial number [@anti_piracy-hdd_lock]. The kernel always unlocks the HDD at boot and refuses to continue if the process wasn't successful. This prevented users from examining the HDD's contents and/or swapping the drive.
- **Hardcoded partition locations**, to deter users from installing a larger drive [@anti_piracy-bios].
- **FATX**: Microsoft implemented this proprietary and undocumented filesystem as another layer of obfuscation.

### Defeat {.tabs-close}

This was a brief introduction to the chain of trust that Microsoft implemented. Seems pretty simple, right? Well, **something's odd**: execution is controlled by the CPU, but Microsoft relied on an off-the-shelf processor. So, how does it understand data encrypted with RC4?

The answer is that **it doesn't**. So, somewhere in this console is an unencrypted piece of code that sets up the encryption system. This code, in particular, was the prime target for hackers who strived to crack this console after its release.

#### Bootstrap search {.tabs .active}

It's officially documented by Intel that their processors start execution at address `0xFFFF.FFF0`. With that in mind, one might inspect that location in the Flash ROM in the hope it would reveal the bootstrap code. This is precisely what a popular hacker and researcher **Andrew 'bunnie' Huang** attempted as part of his academic research.

His initial findings were quite interesting: the upper 512 bytes of the Flash ROM **expose security routines**, including a strange 'RC5' key that wasn't even used by retail systems (as they rely on RC4) [@cpu-huang]. It is widely speculated that Microsoft accidentally left that code from prototype/debug units. Nevertheless, despite being exceptionally revealing, bunnie realised that the real `0xFFFF.FFF0` wasn't in the Flash ROM.

To make a long story short, after some experiments, `0xFFFF.FFF0` turned out to be **hidden in the MCPX chip**: it contained a **hidden 512-byte ROM** that executed once and hid afterwards. This ROM was not easy to extract, so bunnie resorted to tapping the HyperTransport bus to capture the RC4 key as it was transmitted. Since RC4 is a symmetrical encryption algorithm, once you have the key, the protection falls apart.

And so it happened: bunnie published the key as part of his research, and Microsoft was not happy. The hacking community soon chimed in by making new discoveries regarding the MCPX boot code, Flash ROM content, and the kernel.

#### The second wave {.tab}

What followed was an uncontrollable sequence of events that ultimately opened the door to both homebrew software (including booting Linux) and game piracy.

It so happens 512 bytes can only do so much when it comes to initialising the encryption system and handing over control to the Flash ROM. Thus, Microsoft split the first stage of the bootloader into two parts: the Flash ROM stores custom commands for the bootloader, and the MCPX code acts as an interpreter to execute those commands. These commands were called **X-Codes**, and the location in Flash where they reside was called **jam table**. Most importantly, since at that stage the encryption system is not initialised yet, jam tables could only be stored in **unencrypted form**.

This prompted *Michael Steil* and *Visor* to find crucial vulnerabilities in X-Codes execution, which eventually allowed to take control of the CPU without involving RC4. Visor discovered that, in the case of a verification failure during boot, Microsoft programmed it so the CPU would jump to address `0xFFFF.FFFA` in the Flash ROM. From there, it would execute fault-handling routines until reaching `0xFFFF.FFFF`, where the CPU was supposed to halt. However, Intel CPUs roll over to `0x0000.0000`, where main RAM resides (making the CPU halt for other reasons...). This was another prototype leftover, suggesting that Microsoft initially employed **AMD CPUs** (they behave differently). Anyway, this meant replacing the contents of the jam table with X-Codes that overrode the contents of `0x0000.0000` would automatically grant arbitrary code execution.

Steil, on the other hand, developed a different solution that instead redirected execution to the 'stub' bootloader included in Flash, allowing hackers to inject their own code directly into the Flash. This discovery was called 'MIST' [@anti_piracy-exploits].

#### Modchips {.tabs-close}

Commercial solutions in the form of **modchips** eventually emerged. These mainly fiddled with the boot process (involving the Flash ROM boot stage) to allow execution of custom code.

Earlier revisions were connected in parallel with the Flash ROM [@anti_piracy-steil], tampering with specific routines. Later revisions took advantage of the unused LPC port to trick the boot process into continuing execution from their custom ROM.

Their payload often consisted of patched routines or complete bootloader and kernel replacements. One particular unexpected application included bypassing the disc authentication mechanism.

Eventually, homebrew programs were developed to allow to overwrite the contents of the stock Flash ROM, although this required hardware modifications to disable the write protection.

#### A user-friendly permanent unlock 

The kernel routinely checks whether executables were signed using Microsoft's RSA-2048 key, and cracking this key is computationally unfeasible. However, once hackers gained access to the kernel in Flash ROM, it became easier to reverse engineer it and develop patches that could nullify RSA altogether.

Once these patches were published, they opened a wide door to the Homebrew community. In other words, anyone could now develop programs that would execute on a modified Xbox without the approval of Microsoft. Some groups even developed replacements for the original Dashboard which could do more functions, such as booting Linux!

Initially, however, this still required users to modify their motherboard using the aforementioned hardware, which not everyone could do. In later years, game exploits were discovered that could easily bootstrap the main payload; one of them consisted of running a forged save file of *007: Agent Under Fire* or *Splinter Cell*. These files would generate a buffer overflow to kickstart a homebrew tool, and it was possible due to another fundamental weakness in the system: **games always ran in kernel mode** [@anti_piracy-steil].

An even more user-friendly, game-independent exploit finally materialised when hackers discovered yet-another buffer overflow in the original Dashboard. This relied on a forged font file [@anti_piracy-pheonix] which the system never verified.

#### The response

Microsoft rushed to release several hardware revisions throughout its lifespan, from motherboard `1.1` to `1.6`. These attempted to fortify security by [@anti_piracy-boot]:

- Introducing a new boot stage called **Flash Boot Loader** (FBL). This runs after MCPX's boot code and before the Flash ROM's. The FBL code is stored in Flash ROM.
    - Its program verifies sensitive parts of Flash ROM using SHA-1. The reference hashes are signed with RSA.
    - FBL is itself hashed using **Tiny Encryption Algorithm** (TEA)... even though it's advised against due to easy collisions.
- Reducing the amount of exposure in their electronics. This also lowered manufacturing costs.

... but it didn't matter, as the new weaknesses led to hacking developments in a matter of days.

Meanwhile, software updates were released that updated both the Kernel and the Dashboard in an effort to reduce the attack surface. However, certain applications couldn't be easily updated or blocked, and what remained was another 'cat-and-mouse' game.

Another interesting method to mention was Microsoft's **indirect deterrents** against modifications: Microsoft was able to block Xbox Live to consoles carrying unauthorised modifications. This presented a trade-off that users had to consider before hacking their consoles, particularly if their only goal was to play pirated copies.

## That's all folks

After a couple of months with deadlines and exams in the middle, the next article is finally finished. I admit this one continues the trend of adding *too much* information and trivia, but while this research started with small (and frustrating) steps, I'm glad I found a lot of support from one special community, XboxDev, that helped me gather lots of information.

For anyone who would like to know more about this console, XboxDev is actively working on nxdk (along with different emulators) which strives to do things that were previously considered impossible in Xbox homebrew, so I suggest visiting their community for more information.

As for me, I'm going to take a few days to think carefully about the *next article* (and potentially go back a couple of generations to analyse a console I forgot about).

Until next time!  
Rodrigo
