---
short_title: Xbox架构
long_title: Xbox的架构
name: Xbox
subtitle: 令人生畏的竞争对手
date: 2020-06-26
release_date: 2001-11-15
cover: "xbox"
generation: 6
seo_image_pos: "Bottom"
top_tabs:
  Model:
    file: 国际版（International）
    caption: "原版Xbox<br>2001年11月15日于美国，2002年2月22日于日本，2002年3月14日于欧洲发售"
  Motherboard:
    caption: "显示第一次修订版。<br>手柄使用单独的子板插入。<br>背面缺少SDRAM芯片，有四个未填充的SDRAM插槽。"
  Diagram:
    caption: "每个手柄都连接到一个单独的USB集线器。"
#Historical
aliases:
  - /projects/consoles/xbox/
---

## 快速介绍

看来，微软已决定接手世嘉的工作。 他们提供了什么？ 一个开发人员喜欢、用户欢迎的在线服务系统。

请注意，为了与其他资料保持一致，本文将存储单位分为公制前缀（即megabytes（兆字节）或“MB”）和标准化二进制前缀（即mebibytes或“MiB”），因此：

- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

......以此类推。

### 阅读提示

写完这篇文章几个月后，我意识到这是我所写过的内容最密集的文章之一。 Xbox内部发生了很多事情，我已经尽量提到了其中的大部分。

现在，如果你真的有兴趣了解这套系统，但又觉得这篇文章难以理解，我给你的建议是：**慢慢来**，这篇文章哪儿也不会去。 专注于自己喜欢的内容，按照自己的节奏阅读，查看 “资料来源 ”部分的链接以获得支持，最后，不要给自己施加压力，因为没有考试！

## {.supporting-imagery}

## 中央处理器 (CPU)

这款游戏机所含的处理器是著名的英特尔**奔腾III（Pentium III）**（一种用于电脑的货架CPU）的略微定制版本，运行频率为**733 MHz**。 由此可以推测，这款游戏机背后只是一台个人电脑... ...我不会告诉你答案，但我保证，在文章的最后，你一定能得出自己的结论。

![Xbox主板上的英特尔奔腾III封装。](cpu.jpg)

无论如何，奔腾处理器以及英特尔公司设计和制造的其他系列CPU在计算机市场上大受欢迎。 英特尔的市场占有率之高，使其成为事实上的质量参照标准： 作为一个普通用户，如果你想要一台好电脑，又有足够的预算，那么你只需要寻找搭载英特尔CPU的*某型电脑*。 我们现在都知道，这其中还涉及到更多的因素，但这就是英特尔营销人员所计划的。

### 技术信息

既然我们已经将英特尔定位在地图上，那么让我们回到这款游戏机的主题上来。 在研究过程中，我本以为能找到与其他CPU（MIPS、SuperH、ARM等）一样深入的文档，但却偶然发现了过多的营销术语，这只会转移我的搜索方向。 因此，在这篇文章中，我想出了一个结构来组织所有必要的信息，这将有助于理解这种CPU的工作原理。 此外，我还将尝试介绍英特尔为这款CPU品牌所使用的一些术语。

说到这里，让我们来看看：

#### 牌子 {.tabs.active}

![本研究的组织方式](_diagrams/cpu/branding.png) {.tab-float}

首先，Xbox的CPU确定为**奔腾III**。 这意味着什么呢？ 在当时（20世纪90年代初），奔腾系列代表了新一代CPU。 它们是“新高端”，集合了所有能让电脑超快运行的花哨技术，还能帮助买家决定购买哪款CPU，以获得*最佳性能*。

奔腾III 取代了奔腾II，而奔腾II又取代了原来的奔腾。 此外，当第一代奔腾问世时，它取代了80486，而80486又取代了80386...... 你懂的。 重要的是，“奔腾”主要是一个品牌名称，与它的内部结构并无直接关联。 因此，我们必须深入研究！

为了深入探讨，不至于迷失方向，我把信息编成了三个部分，它们共同构成了芯片。 首先是**指令集架构**（Instruction Set Architecture，“ISA”）（用于指挥CPU的指令组）、**微架构**（如何在硅片中实现ISA）和**内核**（用于封装微架构以形成特定 CPU 型号的组件集）。

#### ISA {.tab}

![一颗英特尔80386（1985年），或其兼容型号是20世纪90年代中期应用程序的最低配置要求。](photos/i386.webp) {.tab-float}

事实上，一旦我提到“英特尔”，我迟早会介绍著名的x86，即它的指令集。

1978年，英特尔发布了16位CPU**英特尔8086**，这是x86的雏形。 此后，随着更多英特尔CPU的发布（80186、80286 等），ISA 不断扩展，指令也越来越多[@cpu-thompson]。 因此，随着更多突破性功能的加入（如“保护模式”和“长模式”），x86开始变得碎片化。 为了解决这个问题，现代x86应用程序通常以80386 ISA（也称为**IA-32**或**i386**）为基准，除其他外，该ISA可在32位环境中运行。

随后，英特尔通过使用**扩展功能**增强了 IA-32，这意味着IA-32 CPU可能包含也可能不包含新功能。 程序通过查询CPU来检查是否存在特定的增强功能。 Xbox的CPU包括两个扩展：

- **MMX**（Multimedia Extension，多媒体扩展）：增加了57条SIMD指令和8个64位寄存器（仅限整数），可加快矢量运算速度。
- **SSE**（Streaming SIMD extension，流SIMD扩展）： 另一种SIMD类型的扩展，解决了MMX的诟病（缺乏浮点支持和无法并行使用浮点单元）。 它增加了56条新指令和8个128位寄存器（称为“XMM”），可容纳4个32位浮点数`floats`。

好消息是，由于游戏机将始终使用相同的CPU，程序员可以优化代码以利用这些扩展，因为它们将始终存在。

#### 微架构 {.tab}

说到构建可解释x86指令的电路，英特尔公司为其CPU设计了多种不同的方案。 有些设计随着新奔腾系列（如 奔腾4）的发布而出现，而另一些设计则在英特尔发布“增强”版奔腾（如“奔腾Pro”）时出现。 不过，自第一代奔腾发布以来，CPU型号和微架构已不再使用相同的名称。 例如，80486使用的是80486微架构（而非其他），而最初的奔腾使用的是“P5”微架构。

现在，Xbox CPU和其他奔腾III处理器都使用**P6微架构**（也称为“i686”）。 这是第六代处理器（从8086开始计算），其特点如下：

- *庞大的***14级流水线**： 这意味着最多可并行处理14条指令。 另一方面，单个指令可能需要更多的周期才能完成。 [参见前面的解释](game-boy-advance#tab-2-1-the-core)。
- **乱序执行**： 在可能的情况下，CPU会重新安排指令顺序，以提高效率和性能。
- **动态执行**： 由于P6是一种乱序[超标量](dreamcast#cpu)设计。 传统的[分支预测器](gamecube#the-powerpc-gekko)现在与其他技术（“推测执行”和“数据流分析”）相结合，以利用新的功能。 这样就能进一步减少流水线停滞。

说到这里，请仔细看看这些功能。 恰好它们与[之前的游戏机](gamecube#the-powerpc-gekko)非常相似，然而，与英特尔的CPU相比，其他CPU在设计上却大相径庭。 从历史上看，可以说x86的设计根本不允许英特尔生产流水线式CPU。 然而，他们却做到了，让我们来看看原因何在...

#### CISC还是RISC {.tab}

![英特尔奔腾Pro处理器（1995年）首次采用了P6微架构，在x86领域架起了CISC与RISC技术发展的桥梁。 这一突破令许多人始料未及——尤其是那些曾断言x86架构时日无多的人士。](photos/pentiumpro.webp) {.tab-float}

竞争对手的CPU恰好是根据[**RISC准则**](playstation#tab-1-1-a-bit-of-history)设计的，而英特尔的x86并非如此，因此被归入**CISC**组。 与CISC CPU相比，RISC CPU以有意简化设计而著称。 例如，RISC CPU采用**存-取架构**，只提供从寄存器操作值的指令（而不是直接从内存操作）。

RISC处理器的优势之一在于其简单的设计方法使其CPU的设计具有模块化意识，而模块化意识反过来又可以利用并行技术来提高性能。 这就是我们看到MIPS和PowerPC等CPU首次采用流水线阶段、超标量设计、乱序执行、分支预测等技术的原因。 另一方面，CISC处理器早于RISC处理器，前者旨在解决不同的需求。 因此，它们的设计不如RISC CPU灵活。

回到最初的问题，P6是一种有趣的设计，因为虽然这种CPU只能理解CISC指令集（x86），但其操作码的子集可以用**微码**来解释。 最重要的是，执行微码的单元是围绕存-取模型构建的[@cpu-gwennap]。 这是因为P6体系结构是由**英特尔i960**（英特尔曾经寄予厚望的RISC CPU）的前工程师编写的。 总而言之，这使得英特尔在不破坏与历史悠久的x86 ISA兼容的情况下，获得了与 RISC 处理器类似的优势。 可以说，随着时间的推移，“CISC”和“RISC”等术语已经变得模糊不清，无法对任何现代CPU进行分类。

顺便提一句，微码已经嵌入芯片中，但它可以打补丁，只要发现错误或安全漏洞，英特尔就可以在生产后修复CPU。 如果您读过以前的文章（如[N64](nintendo-64)或[PS2](playstation-2)），请记住，英特尔的微码是**不公开**的（更不用说留文档了），英特尔是其唯一的“维护者”。

#### 内核 {.tab}

![“铜矿”设计图](_diagrams/cpu/core.png) {.tab-float}

英特尔推出了大量采用P6微架构的芯片。 Xbox包括一个名为**铜矿（Coppermine）**的型号。 它被称为奔腾III的第二次修订版（取代了“Katmai”内核），具有以下组件：

- **32 KiB 一级高速缓存（L1 Cache）**： 16 KiB用于指令，16 KiB用于数据。
- 集成**128 KiB 二级高速缓存（L2 Cache）**：这很奇怪，因为货架上的铜矿有256 KiB的L2[@cpu-anand]。 事实上，铜矿128（英特尔“赛扬”品牌中的低端奔腾替代品）也有相同数量的L2[@cpu-shvets]。 因此，这样做可能是为了降低制造成本，使这款游戏机保持有竞争力的价格。
- 133 MHz**前端总线（Front-side bus）**： 这是连接L2和内存控制器的总线，稍后我们将详细介绍。
  - 英特尔将其命名为“前端总线”，以区别于连接L2（外部缓存）和L1（内部缓存）的另一条总线。 后一条总线被称为“后端总线”（Back-side bus）......在英国使用这个名字很不吉利。

铜矿还在原有的L2基础上增加了两个“增强功能”，即**高级传输缓存（Advanced Transfer Cache）**和**高级系统缓冲（Advanced System Buffering）**。 总而言之，L2在芯片上，总线更宽，这有助于减少前端总线可能出现的瓶颈。

最后，芯片使用的是适合PC主板的“Micro-PGA2”插座，但与其他游戏机一样，Xbox焊上了个球栅阵列封装（Ball Grid Array，“BGA”）版的。

### P6和奔腾数字的终结  {.tabs-close}

下面是一些更多的历史。 在P6诞生多年后，英特尔计划用“NetBurst”微架构（奔腾4的特色）来继承它。 不过，继承关系也到此为止。 尽管NetBurst采用了许多现代技术[@cpu-netburst]，但也存在功耗过高、发热量大（平均85 W[@cpu-pentium4]）和可扩展性差等问题，所有这些都阻碍了该设计的进一步发展。

因此，这促使以色列的英特尔团队重新审视他们的低功耗P6 CPU（“奔腾 M”），并开发出功能更强大的后续产品。 第一个成果就是**Yonah**内核，它是对P6设计的轻微改进，被命名为**酷睿Solo**或**酷睿Duo**。 有了它，功耗降到了合理的数字（27 W [@cpu-coresolo]到 31 W [@cpu-coreduo]，取决于不同的变体）。 几个月后，后续的酷睿微架构成为P6（和NetBurst）的旗舰后继产品，令人困惑的是，它以**酷睿2 **CPU系列的形式出现在货架上。 多年来，后续的微架构在很多方面都有所改进，但它们也设法重新整合了NetBurst中被遗忘的元素，而没有重蹈覆辙。

说来奇怪，我不知道Yonah内核的命名是否是《约拿书》的隐喻，尤其是联系到先知最终改正了自己的道路，拯救了尼尼微城这一事件。 不过也有可能是我想多了。

### 主板架构

在PC发展史上的某个时期，主板变得越来越复杂，必须从头开始开发新的设计，才能有效地满足新出现的需求。

![Xbox主板概览](_diagrams/cpu/motherboard.png) {.open-float}

所制定的新标准依靠两个专用芯片来处理主板的大部分功能。 这两个芯片是：

- **北桥**： 用作内存控制器和GPU接口。
- **南桥**：接入其他I/O接口（如USB、 ATA/SATA、PCI 等）。

这些芯片的组合称为**芯片组**，其重要性足以决定主板的功能和性能。 Xbox与PC如此接近，因此也包含了两个芯片：**NV2A**是北桥和GPU的组合；**MCPX**负责处理其余的I/O接口。

{.close-float}

两块芯片通过一条名为**HyperTransport**的专用总线相互连接。 值得指出的是，一些PC主板也采用了这项技术，只是品牌不同而已（英伟达*nForce MCP-D*）。

### 内存布局

Xbox包含总容量为**64 MiB的DDR SDRAM**，与同类产品相比，这种内存的速度非常快。 不过，它也是系统所有组件共享的。 因此，我们又一次看到了**统一内存架构**（unified memory architecture，“UMA”）布局。

![交换网络的表示。 GPU使用两个内存库，而CPU使用另一个内存库，从而减少了过程中的竞争。](_diagrams/cpu/memory.png)

我们已经看到，这种设计有时会很[麻烦](playstation-2#preventing-past-mishaps)。 不过，程序可以通过将数据分散到不同的内存库来解决这个问题。 NV2A实现了一个**交换网络**，可以让不同的单元（CPU、GPU等）同时访问它们\[@cpu-huang\] \[@cpu-informit\]。

此外，游戏机有一个内置硬盘，而且恰好设置了三个分区，每个分区保留750 MiB用于临时存储。 CPU可以从主内存中卸载部分数据，然后在需要时再上传回来。 请注意，这是一个手动过程，不涉及虚拟内存。

## 图形

正如我们之前看到的，图形处理器位于NV2A芯片中，与MCPX一样，由英伟达制造。

![Xbox主板上的英伟达NV2A封装。
](gpu.jpg)

该公司在图形处理器领域已经有很长的历史，其*GeForce*系列是计算机市场上最受欢迎的GPU品牌之一，直接与ArtX/ATI的Radeon系列竞争。 总的来说，考虑到这是微软在游戏机市场的首次尝试，这为Xbox的图形质量提供了很好的筹码。

这一切似乎都合情合理，但当年的决定*真的*是正确的吗？ 我们很容易从现在的历史中找到微软选择英伟达而不是当时其他流行品牌（3dfx、PowerVR、S3等）的原因，但如果我们更多地了解一下当时的竞争情况，就会发现选择的全景要复杂得多。

![光环（Halo）（2001）在Xbox上以720p模式运行](halo.png)

例如，到90年代末，3dfx广受欢迎的“Voodoo 2”系列在PC市场上占据了约 70%的市场份额[@graphics-ign]，而英伟达则在努力推广新的“GeForce 256”（GeForce系列的第一款产品）。 在此之后，微软的选择听起来更像是一种冒险而非安全的赌注，但正如我们现在所知，这种冒险最终得到了回报。

> 1999 年，英伟达并不像现在一样是业内龙头。 他们遇到了麻烦。 新的GeForce架构还很年轻，很多人都不喜欢它。 现在看来这是理所当然的。 但你需要研究那段历史才能了解这一点，以及为什么我必须为之奋斗。 我当时是对的，但当时并不知道我是对的；我非常担忧。
> 
> ——<cite>Seamus Blackley，初代Xbox的合作制作者</cite>

下面，我们将研究这款芯片的内部工作原理。 现在，恐怕我们会发现自己就像CPU部分一样，掺杂了大量的专业术语和营销术语，但不用担心！ 我将从最基本的开始。

### 架构设计

NV2A的GPU内核基于流行的“GeForce3”系列GPU\[@graphics-mslusarz\] \[@graphics-g3arch\]，在英伟达的技术文档中也被称为NV20。

![NV2A的流水线设计](_diagrams/gpu/pipeline.png) {.open-float}

请注意，虽然Xbox的GPU流水线基于NV20架构，但NV2A有一些与NV20系列其他产品不兼容的修改（最重要的是，它已被调整为在统一内存架构环境中工作）。

所分析的设备还包含许多超出本文范围的功能，因此，如果这部分内容引起您的注意，我建议您查阅相关资料/参考文献。 此外，由于图形相关术语在不断演变（这可能会导致一些混淆），我决定采用微软/英伟达在Xbox时代使用的术语，因此如果你打算从其他来源阅读更多图形相关文章，请记住这一点。

{.close-float}

说完这些，让我们来看看Xbox是如何绘制帧的。 其中一些解释与GameCube的[Flipper](gamecube#graphics)非常相似，因此如果您在阅读这篇文章时遇到困难，也可以阅读一下这篇文章。

#### 指令 {.tabs.active}

![指令阶段](_diagrams/gpu/commands.png) {.tab-float}

首先要解释的是GPU如何从CPU接收命令。 为此，GPU包含一个名为**PFIFO**的命令处理器，它能以FIFO的方式有效地获取和处理图形命令（称为**Pushbuffer**），解压缩的命令随后会被传送到**PGRAPH**（负责图形处理的块）和其他引擎。

与Flipper一样，几何图形不必嵌入命令中。 PGRAPH提供了多种提交图形数据的方式。 例如，CPU可以在RAM中分配一个包含顶点数据的缓冲区，然后指示GPU从该位置获取数据。 这种方法效率很高，因为它可以避免发送重复的几何图形。

接下来的解释将在PGRAPH中进行。

#### 顶点 {.tab}

![顶点阶段](_diagrams/gpu/vertex.png) {.tab-float}

对于GPU而言，这是一个特别有趣的部分。 在这一阶段，GPU能够对我们的几何体进行顶点变换。 我们已经在Flipper上看到过这一功能，但与Flipper不同的是，这款GPU使用的是**可编程引擎**。 这意味着开发人员可以指定执行哪些顶点操作以及如何执行，而不是依赖于预定义的程序。 不过，如果需要，NV2A也可以在“固定”模式下运行。

这一阶段由**顶点单元**处理，NV2A有**两个**顶点单元。 每个顶点单元可以加载一个包含多达136条指令（也称为**微码**）的程序。 该程序称为**顶点程序**，在运行时加载。 顶点程序可执行以下操作[@graphics-koppelman]：

- **算术运算**（即加法、乘法、取最小值等）。
  - 这包括辅助图形相关任务的“辅助函数”，如点乘。
- **顶点旋转（Vertex Swizzling）**（重新排列和/或复制）。

简而言之，顶点单元通过在寄存器中操作顶点来处理顶点。 换句话说，程序加载完成后，16个只读寄存器（称为 “输入寄存器”）将被初始化为顶点属性（每个向量包含四个分量）。 然后，该单元利用输入寄存器执行一系列操作（由程序指示）。 此外，还提供了12个可写寄存器和多达196个常量来辅助计算。最后，生成的顶点被存储到另一个由 11 个可写寄存器（每个寄存器都有特定用途）组成的块中，并被传送到下一阶段。每个接收到的顶点都要重复这一过程。 最后，生成的顶点被存储到另一个由11个可写寄存器（每个寄存器都有特定用途）组成的块中，并被传送到下一阶段。 每个接收到的顶点都要重复这一过程。

#### 像素 {.tab}

![片段/像素阶段](_diagrams/gpu/pixel.png) {.tab-float}

在这一阶段，顶点被转换成像素。 这一过程由光栅化器开始，光栅化器生成像素来绘制每个三角形。 NV2A的光栅化器每个周期可生成四个像素。<!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while \[competitors embedded it\]()). -->之后，**4个纹理着色器**用于从内存中获取纹理[@graphics-domine]，这些着色器还能自动应用各向异性过滤、mipmapping 和**阴影缓冲（shadow buffering）**。 阴影缓冲用于测试像素是可见还是被光源遮挡，从而应用正确的颜色。 此时，GPU还可以执行剪切和早期[Z测试](nintendo-64#modern-visible-surface-determination)（NV2A将Z缓冲压缩为原来的四倍，以节省带宽，从而大大提高了性能）。

生成的像素存储在**一组共享寄存器**中，然后在**8个寄存器组合器**中循环，每个组合器对像素进行算术运算。 这一过程可通过**像素着色器**（GPU执行的另一种程序）进行**编程**[@graphics-wasson]。 在每个周期，每个组合器都会从寄存器组接收RGBA值（RGB+Alpha）[@graphics-spitzer]。 然后，根据着色器设置的操作，对数值进行运算并写回结果。 最后，大量数值被发送至**最终组合器**，该组合器可专门混合镜面色彩和/或雾气。

寄存器组合器的编程方式与纹[理环境单元（Texture Environment Unit）](gamecube#tab-3-3-texture)类似。 也就是说，通过特定的设置组合来改变寄存器。 在Xbox中，PFIFO读取推送缓冲区来设置PGRAPH，其中包括寄存器组合器和纹理着色器。

#### 后处理 {.tab}

![后处理阶段](_diagrams/gpu/postprocessing.png) {.tab-float}

在将像素写入帧缓冲区之前，NV2A包含四个称为光栅输出单元（Raster Output Unit，“ROP”）的专用引擎，它们使用主内存中分配的块执行必要的测试（Alpha、深度和模版）。 最后，只有通过这些测试的像素才会被写回（每个周期四个）。

此外，帧缓冲区还可以使用一种称为**多重采样**的技术进行抗锯齿处理[@graphics-geforce3]。 从本质上讲，这种技术对多边形的边缘进行多次采样，并在采样过程中添加不同的偏移量。 然后，对所有采样取平均值，形成抗锯齿图像。 这种方法取代了之前英伟达GPU使用的称为“超采样”的抗锯齿功能（更耗费资源）。

### 可编程性的重要性 {.tabs-close}

我认为强调英伟达为开发人员提供的全新可编程模式的重要性至关重要。 多年前，大部分图形流水线都是由CPU计算的，GPU只负责加速光栅化操作。 随着“着色器”（指像素着色器和顶点程序）的引入，程序员可以利用GPU的资源优势来加速流水线中的许多计算，从CPU卸下大量工作。

“着色器”的概念是由**皮克斯（Pixar）**公司于1989年提出的，作为一种扩展**Renderman**[@graphics-pixar]——皮克斯用于3D渲染的开创性软件——的方法。 当时，3D图形主要由工业设备处理。 后来，我们看到某些游戏机也采用了[类似的原理](nintendo-64#tab-1-1-reality-signal-processor)，但直到英伟达发布GeForce3系列，着色器才成为消费市场的标准。

::: {.subfigures .open-float .tab-float .tabs-nested}

![顶点程序实现的复杂动画](vertex){.active video="true" title="顶点程序"}

![通过像素着色器实现的不同纹理效果](fragment){video="true" title="像素着色器"}

ChameleonMark（2002年），英伟达为展示GeForce3着色器而开发的演示程序（跑分软件）。

:::

有了顶点程序，GPU现在可以加速模型转换、照明计算和纹理坐标生成。 后者对于合成[高阶曲面](playstation-2#infinite-worlds)至关重要。 这样，CPU 就可以集中精力提供更好的物理、AI和场景管理。

对于像素着色器，程序员可以通过多种方式处理和混合纹理，以实现不同的效果，如多重纹理、镜面贴图、凹凸贴图、环境贴图等。

**通用GPU（General Purpose GPU，“GPGPU”）**是由于这种方法而出现的一个新编程概念，它包括将原本完全由CPU完成的任务分配给 GPU。 因此，GPU不仅接管了大部分图形管道，现在还能作为高效的协处理器进行专业计算（如物理计算）。 这是一个新的领域，随着GPU变得更加强大和灵活，它还将不断发展。 不过，由于结合了硬件功能（顶点和像素着色器）和专门开发的应用程序接口（OpenGL的“状态程序”），NV2A已经能够做到这一点。

{.close-float}

我有一种预感，着色器将在今后的文章中经常出现。 但请记住，在本文中，着色器可能会被认为有点“原始”，有些人可能会认为像素着色器甚至算不上“着色器”（与现在的GPU相比）。

### Xbox的帧

游戏的标准分辨率为**640x480**，这几乎是第六代游戏的标准分辨率。 不过，这个限制只是一个数字： GPU可以绘制高达4096x4096的帧缓冲区，但这并不意味着硬件可以提供可接受的性能。 另一方面，游戏机允许对屏幕设置进行全局配置，这可能有助于推广先进功能（如宽屏幕和“高分辨率”），而不是等待开发人员去发现（如[Gamecube/Wii](wii#tab-1-1-standardised-widescreen)的情况）。

另一方面，视频编码器会尝试将帧缓冲区上的任何内容以电视机能理解的格式播放出来。 这意味着，除非游戏以高清格式输出（即720p或1080i，只有少数游戏会这样做），否则宽屏图像将变成[变形](wii#tab-1-1-standardised-widescreen)图像。

既然如此，那么这款游戏机会输出什么样的信号呢？ 相当多。 除了典型的PAL/NTSC复合信号外，Xbox 还提供**YPbPr**（需要额外配件才能获得“分量”连接器）和RGB（符合SCART和VGA标准）信号。 总而言之，无需昂贵的转换器，非常方便。

## 音频

该游戏机的音频子系统深受专业音频设备和ATX主板的影响，尽管其中确实包含一些奇特的部件。 MCPX包括两个音频组件：**音频处理单元（Audio Processing Unit）**和**音频控制器接口（Audio Controller Interface）**。

音频处理单元或“APU”是专用音频处理器，由三个子组件组成：

- **语音处理器（Voice Processor，“VP”）**： 这是一个专用电路，能以48 kHz的采样率合成256种声音。 它还包括两个DAHDSR包络控制和各种滤波器。 此外，64种声音可以是3D声音（而不是立体声或单声道）。 最后，处理器通过32个通道对声音进行混音和输出，其中8个通道组有各自的音量控制。
- **全局处理器（Global Processor，“GP”）**： 这是一个可编程的数字信号处理器，用于处理来自VP的音频数据并应用各种效果。
  - 并不是所有来自VP的通道都必须在这里进行处理，相反，可以将特定的组别发送到这里，专门对其应用特定的效果（如混响）。
- **编码处理器（Encode Processor，“EP”）**： 顾名思义，它生成来自GP的最终立体声信号，并将其存储在主RAM中。
  - 请注意，EP的操作也可由GP执行，但单独的编码组件可使游戏机采用更好的音频编码技术，如杜比数字技术。

APU只能处理音频数据，但不能输出音频数据。 后者是ACI的工作，它读取RAM中的音频数据，进行解码，并通过音频输出发送生成的PCM立体声采样（16位分辨率，采样率为48 kHz）。

## I/O

正如我之前提到的，我们有一个“南桥”子系统，它集中了所有I/O访问。 南桥由MCPX芯片实现。

![主机结构的主图. 请注意MCPX是如何控制大部分I/O的。](_diagrams/main.webp)

顺便提一下，MCPX源自PC上的**英伟达nForce多媒体和通信处理器（Multimedia and Communications Processor，“MCP”）**。 在使用nForce 220/415/420芯片组的主板上可以找到它[@io-reactos]。

### 外部接口

游戏机包括以下外部接口：

- **4个USB 1.1端口**： 用于连接控制器，但端口的外部形状经过修改，只允许连接Xbox控制器。
  - 这些端口还包含一个名为“视频同步”的额外针脚，用于连接与屏幕互动的外设。
- **10/100BASE-TX以太网端口**： 用于在线服务（稍后详述）。 实际以太网功能由主板上的一个独立收发器执行。

### 内部接口

MCPX还提供以下用于不同子系统互连的接口和协议：

- **SMBus**：也称为 I²C，是连接这些组件的串行接口：
  - **系统管理控制器（System Management Controller，“SMC”）**： 管理多种服务，如电源、温度和风扇控制。 它实际上是一个PIC16LC微控制器。
  - **系统温度监控器（System Temperature Monitor，“STM”）：SMC使用的数字温度计（ADM1032），用于检测过热。</li>
  - 一个**256 B EEPROM**：可重写ROM，用于存储唯一标识符（序列号、区域、以太网MAC地址等）。
  - **视频编码器**： 编码器主要连接到 GPU，但通过SMBus控制。</ul></li>
- **IDE控制器**： 这是PC上广泛使用的一种标准协议，用于与硬盘驱动器、光驱等进行通信。 使用一根宽带状电缆将主板与DVD驱动器和硬盘连接起来。
- **低引脚数（Low Pin Count，“LPC”）总线**： 这是从PC借来的另一种接口，但它不是连接*传统的*PC BIOS，而是与**闪存ROM**通信，而闪存ROM则存储相当于BIOS的内容。 闪存容量为1MiB。</ul>

### 控制器

Xbox配备了一款名为**“公爵”（The Duke）**的笨重控制器，其输入设置与其他竞争对手并无不同......只是在ABXY按键上使用了模拟电路（8 位宽），允许游戏检测大部分按键的“半按”状态。 另一方面，“公爵”受到了广泛的批评，以至于微软在游戏机发布几个月后就用名为**“控制器 S”（Controller S）**的新改版取代了它。

::: {.subfigures .toleft .tabs-nested}

![](controller/duke_front.png){.active title="正面"}

![](controller/duke_back.png){title="背面"}

“公爵”（2001）[@photography-amos]

:::

::: {.subfigures .toright .tabs-nested}

![](controller/s_front.png){.active title="正面"}

![](controller/s_back.png){title="背面"}

“控制器 S”（2002）[@photography-amos]

:::

仔细观察后发现，这两款手柄都有一些特别之处： 两个**记忆单元**插槽可以插入专用记忆卡，从而在游戏机之间共享保存内容。 在意识到这一点后，我立即认为这是从[之前的竞争对手](dreamcast#interactive-memory-card)那里继承来的功能。 然而，在这篇文章发表几天后，我把它寄给了这款游戏机的联合创始人Seamus Blackley，他很快就回复了我， 并发表了非常有趣的评论。关于与Dreamcast的相似之处，他告诉我说：

> 与Dreamcast的关系只是历史的偏见。 这是偶然的。
> 
> ——<cite>Seamus Blackley</cite>

### 所需的适配器

关于这些手柄，还有一个有趣的事实值得一提，那就是它们只能使用一种被称为**断开式加密狗（breakaway dongle）**的形状适配器来插入，这种设计是为了防止有人被绊倒时发生意外。

> 我认为他们还考虑到了Xbox放在高高的架子上，拽一下控制器就会把整个沉重的游戏机拽下来砸到孩子头上的情况。 
> 
> ——<cite>Xbox用户</cite>

## 操作系统

好吧，让我们先来解决这个房间里的大象。

> 它能运行**Windows系统**吗？

恐怕答案*是肯定的，也是否定的*： 游戏机中是有一个“Windows”，但不是传统PC用户所期望的那种形式。

首先，Xbox的操作系统由**内核（Kernel）**和**用户界面应用程序**（即Dashboard）组成。 它们分别存储在1 MiB闪存ROM和硬盘中。

内核借用了**Windows 2000内核**[@operating_system-os]的大量代码库（而Windows 2000内核又基于现代的**Windows NT**架构）。 结果是一个经过精简的Windows 2000内核，只嵌入了Xbox硬件所需的组件。 最后，这些组件被压缩打包成一个单一的可执行文件[@operating_system-kernel]，以优化内存效率。 总之，你可以把它看作是一台高度优化的Windows机器，专为游戏而设计。

值得注意的是，Xbox项目是由DirectX团队领导的[@operating_system-renegades]。 因此，它的起源与[将其API引入Dreamcast](dreamcast#windows-ce)的Windows CE团队无关。

### 启动过程

与奔腾电脑一样，系统启动后，CPU将开始执行复位向量（地址`0xFFFF.FFF0`）上的指令。 对于Xbox，该地址指向MCPX中的隐藏ROM（更多详情请参见 “反盗版和自制程序 ”部分）。 MCPX包含设置安全系统的例程，并将继续从闪存ROM启动。 在闪存ROM中，Xbox将初始化硬件、启动内核并显示开机动画。

在安全初始化过程中，CPU会转入**保护模式**。 这一点至关重要，因为x86 CPU始终以**实模式**启动，以保持与第一代处理器（英特尔8086）的兼容性，但是，如果程序员需要访问CPU的现代功能（例如可以访问超过1 MiB的内存），他们必须手动切换到保护模式才能启用这些功能。

内核加载时，会向CPU注入微码（不是编程，而是*更新*）。 最后，内核会查找是否存在有效的DVD光盘。 如果有，就运行它。 否则，它将加载存储在硬盘中的用户交互式shell界面。

### 绿色屏幕

现在我们来看看Xbox在没有插入游戏光盘时加载的程序： **仪表盘（Dashboard）**。

#### 交互界面 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![主界面。](shell/home.png){.active title="主页"}

![设置界面。](shell/settings.png){title="设置"}

Dashboard提供多种服务

:::

与[PlayStation菜单](playstation-2#interactive-shell)或[GameCube的IPL](gamecube#splash-and-shell)相比，Dashboard的功能并无太大区别。 它基本上包含了典型用户所期望的所有功能，如调整某些设置、移动存档、播放DVD电影或CD音频等等。

值得一提的是，Dashboard还可以从音频CD中翻录音乐并存储到硬盘中。 随后，可以从任何游戏中获取这些音乐，“个性化”游戏配乐。 *远在天边，近在眼前！*

#### 可更新性 {.tab}

是的，这是同代游戏机中首款可*官方*更新的游戏机，但只有硬盘内容可写。 内核部分（在闪存ROM中）不能重写，但系统可以在内存中加载后打补丁。 因此，内核补丁都存储在硬盘中。

更新通过零售游戏分发和/或连接Xbox Live网络服务后由系统下载（稍后详述）。

有些更新会增强系统的功能。 例如，第一代Xbox设备不包含Xbox Live，因此游戏会以更新的形式嵌入所需的文件，以安装该服务。 其他更新则侧重于修复安全漏洞。

## 游戏 {.tabs-close}

这部分可能会让人有些困惑，主要是因为与其他游戏机相比，它缺乏底层文档。 微软/英伟达似乎真的希望开发人员使用他们的库，而忘记其他一切。 不过，这是一种有趣的策略。

无论如何，我都尽量将文档内容翔实地记录下来，但又不会太深入。

### 开发生态

该游戏机的游戏开发在库、术语等方面非常复杂。 因此，我按照框架将其分开。

#### 硬件抽象 {.tabs.active}

![HAL（硬件抽象层）示意图](_diagrams/hal/general.png) {.tab-float}

我们已经看到，像“带微码的可编程协处理器”这样的元素一开始往往会大张旗鼓，但当开发者发现新硬件的真正复杂性后，这些元素就会慢慢消失。

从开发者的角度来看，我认为这款游戏机的卖点之一就是加入了能有效管理底层功能的流行高级库。 微软和英伟达的策略是，与其记录硬件的每一个底层方面，不如提供一个功能齐全的硬件抽象库，让开发者无需完全了解硬件的内部工作原理就能执行预期的操作。

目前有多种SDK可用于为该游戏机进行开发，有些是“官方”的，限制较多，有些则是“非官方”的，但很灵活。 下面我们就来看看最受欢迎的几种。

#### 微软XDK {.tab}

![XDK的结构](_diagrams/hal/xdk.png) {.tab-float}

微软的Xbox开发工具包（Xbox Development Kit，“XDK”）是用于Xbox开发的官方SDK。 它是一个包含许多工具、库和编译器的软件包。 最值得注意的是，它与Visual Studio .NET（2002版）一起使用，无论好坏，后者在当时都是*相当不错的IDE*。

图形库是对**Direct3D 8.0**的定制实现，并对其进行了扩展，以包含Xbox特有的功能。 Direct3D是一个非常强大的API，用于开发与许多GPU兼容的代码。 有了它，PC开发人员就有机会移植他们的PC游戏，而无需做太多改动（理论上如此！）。 不过，顶点程序和像素着色器使用的语法与汇编语言极为相似。

除此之外，音频功能由**DirectSound**提供，它负责APU，无需担心音频数据的移动。 此外，还有一些网络库，对于实现在线功能（即多人游戏）非常方便。 网络API依赖于**Windows Sockets**，这是微软为简化网络通信而尝试使用的Windows专用协议（尽管其设计基于标准化的对应协议BSD sockets）。

为了尝试这一切，微软分发了他们自己的开发套件硬件，这只是一个绿色的零售版游戏机，安装了双倍的RAM（总计128MiB），有时还安装了不同的MCPX芯片（称为MCPX-2，可直接从闪存ROM启动）。 它包含一个增强版Dashboard，可启动经XDK签名的可执行文件。 在另一端，**Xbox Neighbourhood**（一个Windows应用程序）用于将开发工具包与Visual Studio相连，从而方便部署和调试。 为了试用Xbox Live，微软还提供了沙盒服务器。

#### NXDK {.tab}

![NXDK的结构。 请注意，尽管一些低级库被高级库所包裹，但开发者仍可访问它们](_diagrams/hal/nxdk.png) {.tab-float}

为了避免使用官方SDK的自制软件开发者遭遇版权诉讼，一群与微软无关的开发者创建了一个名为**Open XDK**的官方SDK替代版本。 几年后，开发工作停止了，于是另一个开发小组接手了它，并将新分支命名为**“New XDK”**或“nxdk”。

该项目对底层硬件组件（微代码、推送缓冲区等）进行了逆向工程，并设计了新的C/C++应用程序接口，为系统的每个部分提供了高级调用。

为了在不依赖Direct3D的情况下简化图形层，nxdk使用了**CG 编译器**[@games-nxdk]。 CG是英伟达为开发着色器而创建的语言。 CG与其他编译器连锁生成与Xbox兼容的代码。 在编译过程中，CG代码会被转换为NV20兼容的汇编程序，然后再使用自定义编译器进行翻译，生成NV2A兼容的微代码和推送缓冲区。 对于不想使用CG的用户，nxdk还提供了其他编译器来编写底层着色器。

其他可用的API可处理其他服务（音频、网络等）。 总而言之，该库提供了更多的硬件控制（与官方SDK相比），代价是不使用微软的API（事实上的标准，尽管完全是专有的）。 不过，nxdk仍是开发合法自制软件的最佳选择。

### 存储介质 {.tabs-close}

游戏以双层DVD光盘（最大容量为8.5GB！）的形式发布，随后由包含反盗版保护功能的定制DVD驱动器读取（尽管使用的是标准接口ATA）。 值得一提的是，XDK包括一些定制光盘数据布局的工具，使程序员能够提高读取速度！

现在，游戏机还内置了一个8 GB HDD，游戏可以用它来存储保存内容或缓存临时内容。 系统则存储Dashboard、Xbox Live设置和网络设置。

### 网络服务

忘掉[调制解调器](dreamcast#online-platform)或[实验性服务](playstation-2#network-service)吧。 Xbox包含了如今我们认为理所当然的一切，以提供像样的在线服务： 以太网连接和集中式在线基础设施（称为**Xbox Live**）。

![Xbox Live的Logo](shell/live.png) {.open-float}

此外，Xbox Live不仅支持在线多人游戏，还包括其他功能，如用于实时语音聊天的音频流。

但Xbox Live到底是什么呢？ 嗯，它只是一个相互连接的在线服务集合，公司可以用它来建立自己的在线平台。 例如，其中一项服务提供用户个人信息，因此工作室在访问游戏的在线功能时可以将其用作验证方法。 在官方SDK中，微软包含了一些与Xbox Live服务器进行对话的API。

{.close-float}

需要指出的是，微软控制着向谁授予Xbox Live访问权，因此开发者必须向微软注册，以获得游戏将使用的认证密钥。

真正的在线体验发生在**标题服务器（Title Server）**中，这是一种与世界各地的客户端（Xbox游戏机）应答并处理实时通信的服务器。 微软在其SDK中提供了一些示例来演示如何构建这些服务器，不过这些示例依赖于Windows系统，而且必须部署在运行Windows Server的数据中心中。

### 新趋势的开始

在分析了微软推出的Xbox Live及其对整个行业的影响之后。 现在听起来很明显，对吗？ 好像“正规在线游戏”（即以太网+网络基础设施）的秘诀一直都在，但并非每家公司都愿意投资。

事实证明，事情没那么简单： 微软还必须让用户相信，他们“需要”这种功能，在线多人游戏不是可有可无的附加功能，而是某些游戏的基本组成部分。 否则，微软的努力将只是另一种“在线尝试”。

> 试着想象一下，在这样一个世界里，没有游戏机玩家想要在线游戏，也没有人相信PC架构可以成为游戏机。 事实就是如此。 但现在看来很明显，事实并非如此。
> 
> ——<cite>Seamus Blackley</cite>

## 反盗版和自制游戏

无论这款游戏机是否包含货架组件，都实施了相当多的安全措施。

请注意，RSA加密是这里的一个经常性话题，我曾在[Wii文章](wii#tab-2-2-chain-of-trust)中介绍过它，所以如果你不熟悉RSA或其他对称/非对称加密系统，请先看看那篇文章。

既然如此，我们就来看看。

### DVD防拷

游戏光盘受到逻辑和物理两方面的保护，以防止未经授权的复制（这一点现在应该不足为奇了）。

在逻辑方面，游戏光盘包含一些“陷阱”来欺骗传统的DVD阅读器，使它们无法找到实际的游戏内容。 例如，光盘的第一个区域格式与传统DVD相似，在该区域内，存放着如果插入非Xbox系统就会播放的一条警告信息。 实际上，游戏数据是在第二个分区中找到的，但找到该分区所需的元数据并不是以传统DVD光驱所期望的格式编码的，因此只有专门的光驱（因此是Xbox光驱）才能找到游戏。

在物理方面，恐怕目前还不清楚驱动程序和光盘交换哪些数据来进行验证。 光盘包含一个传统读取器无法读取的内环，其中存储了唯一的标识符，但目前还不知道这些数据是如何使用的。

### 系统保护

让我们来看看该系统最初实施的其他安全措施。

#### 简介 {.tabs.active}

分别包含BIOS和敏感数据的闪存ROM和EEPROM使用**RC-4密钥**加密。 内核从BIOS启动后，只能使用**RSA加密**启动由微软签名的可执行文件。

硬盘则使用完全专有且无文档记录的**FATX**文件系统进行格式化。

以上就是对微软实施的信任链的简要介绍。 看起来很简单吧？ 但**有一点很奇怪**：执行是由CPU控制的，但这是一个货架芯片，那么它如何理解用RC-4加密的数据呢？ 答案是**它不懂**，所以在游戏机的某个地方有一段未加密的代码，用来设置第一阶段的加密。 尤其是这段代码，是大多数黑客在这款游戏机推出后努力破解的目标。

#### 搜索引导程序 {.tab}

根据英特尔的官方记录，他们的处理器将在地址`0xFFFF.FFF0`开始执行，因此黑客们专注于在闪存ROM中搜索该代码（这反过来将导致RC-4密钥的破解）。 这就是著名黑客兼研究员**Andrew 'bunnie' Huang**在其学术研究中所做的尝试。 研究结果相当有趣： 闪存ROM的上部512字节包含包括密钥在内的安全例程，但对零售机子不起作用[@cpu-huang]。 我推测，微软可能在原型/调试机中留下了这些代码（可能是偶然的，因为这个区块暴露了微软应用的算法）。 总之，这段代码被认为是*垃圾代码*，因此bunnie意识到真正的`0xFFFF.FFF0`并不在闪存ROM中。

长话短说，`0xFFFF.FFF0`**隐藏在MCPX芯片中**： 它包含一个隐藏的512 B ROM，执行一次后就会隐藏起来。 要提取这个ROM并不容易，因此bunnie采用了窃听HyperTransport总线的方法，以便在RC-4密钥传输时捕捉到它。

就这样，bunnie把密钥作为他研究的一部分公布了出来，微软对此很不高兴。 于是，黑客社区找到了一种方法，获得了该游戏机第一安全层的控制权，其中包括内核。

#### 永久解锁 {.tab}

破解RSA层从来都不是一件容易的事，但既然黑客可以访问内核，那么现在就有可能对其进行逆向工程，并开发出可以让RSA完全失效的补丁。 瞧，这最终实现了，并为自制软件社区打开了大门。 现在，任何人都可以在未经微软批准的情况下，开发可在修改后的Xbox上执行的程序。 一些团体开发了可替代原始Dashboard的程序，这些程序可以执行更多功能，例如执行Linux！

不过，这需要用户使用专用硬件修改BIOS，而这并不是每个人都能做到的。 后来，人们又发现了新的漏洞，可以轻松启动主破解程序，其中之一就是插入一个伪造的007：浴火特工（007: Agent Under Fire）或细胞分裂（Splinter Cell）游戏存档，产生缓冲区溢出，从而启动自制工具，在硬盘中安装永久漏洞。 之所以能产生“永久漏洞”，是因为原始Dashboard存在另一个缓冲区溢出问题，这次是通过一个伪造的字体文件来触发的（注意，字体文件不需要签名）[@anti_piracy-pheonix]。

#### 自制芯片 {.tab}

在盗版方面，**改机芯片**也出现了：改机芯片并没有篡改DVD驱动器，而是利用MCPX ROM可以在未使用的LPC端口上启动辅助BIOS这一事实，覆盖了闪存ROM。

替代的BIOS包含经过修补的例程，可以读取任何类型的游戏光盘（尤其是传统光盘）。

#### 回应 {.tab}

微软发布了许多硬件修订版，减少了他们电子产品中的漏洞数量，节约了成本。 与此同时，微软还发布了软件更新，更新了内核和Dashboard，以减少漏洞数量。 然而，一些游戏漏洞依然存在，这又是一场“猫捉老鼠”的游戏。

还有一点需要提及的是，Xbox Live本身就是一种有效的防盗版机制。 微软能够阻止Xbox Live向那些未经授权修改的游戏机开放，这是一般用户在以玩盗版为唯一目的黑客攻击他们的游戏机之前必须考虑的一个权衡。

## 这就是全部了，伙计们。 {.tabs-close}

在经历了几个月的死线和考试之后，下一篇文章终于完成了。 我承认这篇文章延续了添加*过多*信息和琐事的趋势，但虽然这项研究是从一小步（令人沮丧的）开始的，但我很高兴我从一个特殊的社区XboxDev获得了很多支持，帮助我收集了大量信息。

对于想进一步了解这款游戏机的人来说，XboxDev正在积极开发nxdk（以及不同的模拟器），它致力于实现以前认为在Xbox自制游戏机中不可能实现的功能，因此我建议大家访问他们的社区了解更多信息。

就我而言，我将花几天时间仔细思考*下一篇文章*（并有可能回到几世代之前，分析一下我已经忘记的游戏机）。

下篇文章见！  
Rodrigo
