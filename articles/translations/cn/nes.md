---
name: NES
subtitle: 不止 6502
date: 2019-01-25
releaseDate: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - 
      title: "国际版（International）"
      caption: "NES\n<br>于 1985 年 10 月 18 日在美国发布，于 1986 年 9 月 1 日在欧洲发布"
      active: true
    - 
      title: "日版（Japanese）"
      caption: "Famicom（FC）\n<br>1983 年 7 月 15 日在日本发布"
  Motherboard:
    caption: "“NES”变种"
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nes
# SEO
title: NES 的架构
---

## 引言

初看上去，NES 只不过是一台普普通通的，带有华丽外壳和手柄的 6502 兼容机。

从*技术*上看，确实是这样，但是让我展示给你看，为什么说 CPU 不是这个系统的*核心*部分。

---

## 型号和变种

对于同一款游戏机，任天堂总是将其衍生出各种变种在全世界不同地区发行{{< cite "general-variants" >}}，最后就出现了很多看起来不一样，但是架构相同（组件上可能有变化）的主机。 简要起见，我只介绍两种最流行的版本：
- </em>**Family Computer**（又称 _Famicom_）是最初的版本，只在日本发售。 Famicom 的外壳有明显的玩具风格，带有两个不可移除的手柄（第二个手柄带麦克风），前面有可以接入光枪的接口（美版光枪被称为“_Zapper_”），RF 视频输出（使用 NTSC-J 制式），以及卡带插槽有额外的用来扩展音频功能的引脚。
- **Nintendo Entertainment System**（又称_NES_），是为北美、欧洲和澳洲用户重新设计的版本，看起来像一个机顶盒。 从技术上看，NES 的手柄可以插拔（没有麦克风），视频输出增加了 NTSC 或 PAL 制式的复合 RCA 接口，而音频扩展引脚则被反盗版系统占用。 最重要的是，外壳的底部安装有一个从未被使用的“扩展端口”，卡带可以从额外引脚与该端口通信 {{< cite "general-cartridge" >}}。

因为是“NES”这个名字伴随着作者一起长大的，我将用这个名字泛指这台主机，但当介绍某些只有日版机器才有的功能时，我会改用“Famicom”这个名字。

---

## CPU

NES 的 CPU 是**理光 2A03**{{< cite "cpu-cpu" >}}，该 CPU 基于当时最流行的 **MOS Technology 6502** 8 位 CPU，能以 **1.79 MHz** 的速度运行（或者在 PAL 系统上以 1.66 MHz 的速度运行）。

### 相关背景

70 年代末到 80 年代初的 CPU 市场可谓是百花齐放。 如果某公司想制造一款便宜的微型电脑，它可以有以下选项:
- **Intel 8080**：一款流行的 CPU，应用于第一台"个人"电脑 *Altair*。 它拥有 8 位的数据总线和 16 位的地址总线。
- **Zilog Z80**：一款"非官方"版本的 8080 处理器，较原版有更多的指令、寄存器和内部组件。 它售价更低且兼容 8080 程序{{< cite "cpu-re_1977-86" >}}。 Amstrad 和 Sinclair（等公司）选择了这款CPU。
- **Motorola 6800**：另一款由摩托罗拉（Motorola）设计的 8 位 CPU，包含完全不同的指令集。 许多 DIY 电脑设备、合成器以及一体机使用了 6800。

如何这些选项都不够，市场上还有一家名叫 **MOS** 的公司，他们开发了一款 6800 脱胎换骨版 CPU：**6502**。 虽然与其他 CPU 不兼容，但这款新的芯片造价*非常*便宜{{< cite "cpu-summary_costs" >}}{{< cite "cpu-re_1981-76" >}} ，那些大名鼎鼎的电脑制造商（Commodore，Apple，Atari，Acorn 等）使用 6502 制造电脑只不过是时间问题。

说回日本，彼时的任天堂正需要一款廉价但是开发者比较熟悉的 CPU，于是他们相中了6502。 **理光**，他们的 CPU 供应商，也成功地制造了一款兼容 6502 的 CPU。

#### 理光的许可之谜

关于理光*如何*设法克隆 6502，时至今日已无人能说清了。 有人认为 MOS 向理光授权了芯片设计，但此事存在诸多矛盾之处：
- 虽然理光版和 MOS 版的芯片都有相同的布局，但理光的版本有被断开的总线（禁用了某些功能）{{< cite "cpu-diffences" >}}。 稍后我会详细介绍。
- 没有找到明确说明 MOS 向理光授权 6502 的文档。
- Nikkei Trendy 在 2008 年发表的一篇文章说，理光从罗克韦尔（Rockwell）获得了许可，而罗克韦尔是授权的芯片制造商{{< cite "cpu-trendi" >}}。 虽然第二方能否向第三方提供 IP 仍有争议，但至少经过了 MOS 批准。
- 这不是任天堂第一次逃避知识产权，例如 *Ikegami Tsushinki 诉 Nintendo* 一案中任天堂在日本就被裁定不拥有原版大金刚街机（Donkey Kong）的源代码{{< cite "cpu-dk" >}}。

#### 被阉割的功能

理光 2A03 阉割了原先包含在 6502 中的 **Binary-Coded Decimal **（BCD）模式{{< cite "cpu-visualbcd" >}}。 BCD 将每个十进制数字编码为独立的 4 位二进制数。 6502 的字（word）大小为 8 比特（8 个二进制位）——这意味着每个字可以存储两个十进制数。

举个例子，十进制数字 `42` 可以表示为：
- `0010 1010` 二进制
- `0100 0010` BCD 码

我们可以继续谈下去，不过这里给出一个基本概念：BCD 对那些需要分别处理每个十进制数的应用很有用（例如，数字时钟）（译注：BCD 码的进位更好计算）。 但是，BCD 编码需要更多存储空间，因为每个字最大只能表示到十进制数的 `99`（而传统二进制编码中，一个 8 bits 大小的字能表示到 `255`）。

无论如何，理光通过切断激活 BCD 模式的控制线故意破坏了 BCD 模式。 这样做可能是为了避免向 MOS 支付版税，因为 BCD 是 MOS 的专利（美国针对集成电路布局的版权法直到 1984 年才颁布{{< cite "cpu-protection_act" >}}）。

### 内存

理光 2A03 和 MOS 6502 都有 **8 位数据总线** 和 **16 位地址总线**，这使得它们能访问最大 **64 KB 的内存**。 那么，任天堂是如何填充地址空间的？

其一，主板包含一个 **2 KB 静态内存（Static RAM，SRAM）** {{< cite "cpu-sample_ram" >}} 芯片。 任天堂称这个区域为“工作内存”（Work RAM，WRAM），它可以用来存储：
- 用来处理游戏状态或查找信息的变量。
- 当 CPU 执行子例程时，用来临时保存寄存器值的“栈（Stack）”。
- 作为“缓冲区”，CPU 可以利用它在两个位置之间复制大量数据。

其二，系统组件是**内存映射（memory-mapped）** {{< cite "cpu-cpu_map" >}} 的，这意味着需要使用内存地址来访问它们，这也占用了一部分 CPU 的地址空间。 因此 CPU 的内存空间包含了游戏卡带、WRAM、PPU、APU 和两个手柄 （不用害怕这些名词，因为它们在这篇文章都有解释）。

#### 卡带 / 游戏数据

以防万一你不知道，NES 游戏以卡带（cartridge）的形式分发，而卡带总线又直接连接到 CPU。

任天堂连接卡带的方式使得 CPU 只能访问 49120 字节（约 49.97 KB）的卡带数据（cartridge data） {{< cite "cpu-cpu_map" >}}。 那么，“卡带数据”是什么意思？ 呃，任何连接到这些总线的芯片都算，例如：
- 游戏程序所在的 **程序 ROM（Program ROM）**。 其中不包含任何图形数据，稍后你会在“图形”部分看到。 自然，不像其它芯片，这个是芯片是必须要有的。
- 用于扩展 WRAM 的 **RAM 芯片**。
- 用于保存存档的**内含电池的 RAM 芯片**

之所以有这么多不同的组合，是因为 CPU 不关心正在读取的组件类型，它只关心内存位置。 所以游戏开发商可以选择（或者设计出）一种适合他们游戏的可行布局。

{{< tabs nested="true" centered="true" >}}
  {{< tab_figure_img name="原始照片" src="nrom.png" active="true" >}}
《超级马力欧兄弟》（Super Mario Bros）{{< cite "photography-nrom" >}} 的PCB。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="包含部件标签的照片" src="nrom_marked.png" >}}
带有重要部件标签的相同照片  
“Lockout” 芯片的含义会在“反盗版”部分解释。
  {{< /tab_figure_img >}}
{{< /tabs >}}

例如，任天堂的《超级马力欧兄弟》（Super Mario Bros）”使用了他们称之为 _NES-NROM-256_ 的布局，该布局由 32KB 的程序 ROM 和 8 KB 的图形用“Character RAM”组成（我们将在“图形”部分中看到更多关于它的信息）{{< cite "cpu-nrom" >}}。 _NES-NROM-256_ 还提供了 3 KB 额外的 WRAM，尽管游戏没有使用它。

#### 超越限制

16 位地址总线的一大限制（影响第三代和第四代游戏主机）是其紧凑的地址空间。 如今，32 位计算机最大可以寻址 4 GB 的内存（而 64 位计算机可以奢侈地享用最大 16 EB 的内存）所以不再有人关心这个问题。但回到当时，NES 只有 64 KB 的地址空间，而且其中很大一部分被内存映射硬件占用（[竞争对手避免了这个问题]({{< ref "master-system#accessing-the-rest-of-the-components" >}})）。

所以，这是否意味着游戏开发商只能开发不超过 49.97 KB 限制的游戏？ 绝对不是！ 如果说我们能从历史中学到什么，那就是对挑战性的问题总有一个聪明的解决办法；而这个问题是被 **Mapper** 解决的。

{{< tabs nested="true" centered="true" >}}
  {{< tab_figure_img name="包含 Mapper" src="mapper/mapper.png" active="true" >}}
Mapper 如何扩展 CPU 寻址能力的简化表示。  
通过 Mapper，CPU 可以访问大型程序 ROM 的额外 Bank（地址组）。 不过游戏或程序现在有了在需要时手动切换 Bank 的新任务。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="不包含 Mapper" src="mapper/no_mapper.png" >}}
相同的配置但是没有安装 Mapper。  
虽然简单且便宜，但是 CPU 只能访问有限数量的 Bank。
  {{< /tab_figure_img >}}
{{< /tabs >}}

Mapper 是卡带中包含的额外芯片，位于内存芯片和主机地址线之间。 它的主要工作是扩展地址空间，以便开发人员可以安装更多的芯片。 这是通过 **Bank (地址组) 切换**实现的：内存地址被组织成 Bank，Mapper 提供开关（通过读写特定内存地址控制）在 Bank 之间切换。 现在，CPU 仍然看到相同数量的内存，所以游戏需要针对操作 Mapper 进行编程。 由于成本效益问题，Mapper 是 80 年代至 90 年代初的主流技术。

{{< tabs nested="true" centered="true" >}}
  {{< tab_figure_img name="原始照片" src="tsrom.png" active="true" >}}
《超级马力欧兄弟 3》（Super Mario Bros 3）{{< cite "photography-tsrom" >}} 的 PCB。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="包含部件标签的照片" src="tsrom_marked.png" >}}
标有重要部件标签的相同照片。  
一开始，我以为额外的 WRAM 是用来存储存档的，但后来我意识到这个游戏没有存档（而且也没有电池）。 事实上，这个 RAM 芯片是用来储存解压后的关卡数据的。
  {{< /tab_figure_img >}}
{{< /tabs >}}

回到 NES，一个著名的例子是《超级马力欧兄弟3》（Super Mario Bros 3）”，它的卡带包含了“MMC 3” Mapper（由任天堂制造）。 作为比较，MMC3 提供了最大 512 KB 的程序 ROM，最大 256 KB 的 Character memory 和最大 8 KB 的额外 WRAM {{< cite "cpu-mmc3" >}}。 您现在可以看出为什么“超级马力欧兄弟 3”的游戏内容比初代“超级马力欧兄弟”多很多了。

总而言之，虽然这台主机的内置功能存在一些限制，但是任天堂能确保它适应技术的发展。 另一方面，虽然这种技术有助于降低游戏机的成本，但它将部分成本转嫁到了游戏卡带上。 所以，游戏开发商必须在游戏质量和卡带成本之间做出权衡。

---

## 图形

图形是由一个名为**图像处理单元（Picture Processing Unit，PPU）**的专有芯片生成的。 这是 NES 的*标志性*芯片 换句话说，所有人都可以从硬件商店购买 6502 CPU，那为什么说 NES 与 Apple 2 或 Commodore 64 不同呢？ 嗯，NES 与其他机器的区别就在于 CPU 外围的芯片：PPU 和 APU。 这些分别造就了 NES 独特的图形和音频功能。

就是说，PPU 渲染被称为**精灵图（sprite）**和**背景（Background）**的 2D 图形，将渲染结果输出为视频信号。

### 硬件组织

{{< figure_img src="ppu_content.png" alt="PPU 内存架构图" class="centered-container" >}}
PPU 的内存架构
{{< /figure_img >}}

为了在屏幕上渲染图形，PPU 必须知道要绘制*什么*图形，将它们放置在屏幕上的*哪个*位置；以及*如何*绘制它们（即，使用哪个调色板）。

为了回答这些问题，PPU 被预编程为使用不同内存映射，查找下列类型的数据：
- 图形数据是从游戏卡带中提取的。它包含一个专用芯片，叫做 **Character Memory** ，用于存储 2D 绘图（被称为 **图块 (tile)**），这些数据被组织成一个名为**Pattern table** 的数据结构。 Character Memory 以“只读内存”（ROM）或“随机存取内存”（RAM）的形式存在，具体使用哪种取决于游戏使用不可变的图形，还是使用可以被 CPU 修改的图形。
  - PPU 可以访问被分为两组的，**最大 8 KB** 的 Character Memory，其中每组 4 KB。
- 告诉 PPU “在哪里”和“如何”绘制图形的元数据可以在其他区域找到：
  - 主板上安装了一个单独的 **2 KB SRAM**，专用于图形相关的数据。 任天堂称之为 **Video RAM**（VRAM），它存储两个称为 **Nametable** 的数据结构。
  - PPU 内置 **256 B** 的 DRAM 用于存储 **Object Attribute Memory**（OAM）。
  - 最后，PPU 还拥有 **4 B**的内存用于定义调色盘。

不用担心新术语，这些数据结构的含义将在下面的段落中逐步讨论。

### 构造帧

与其同时代产品一样，该芯片专为 CRT 显示器特性而设计。 没有帧缓冲：PPU 与 CRT 的电子枪同步渲染，即时构建图像。

PPU 绘制尺寸固定为 **256x240 像素**的帧{{< cite "graphics-overscan" >}}。 哎呀，因为世界各地模拟电视标准不同，图像会因为显示设备的制式（NTSC 或 PAL）而存在差异。 简而言之，NTSC 电视会裁剪顶部和底部边缘以适应过扫描（只有约 244 行扫描线可见），所以开发人员在游戏中放置元素的时候将这些边缘视为“危险区域。” 另一方面，PAL 电视不会裁剪边缘，但是会用黑边来填充比 NTSC 多出来的扫描线（PAL使用 288 行扫描线）。

在幕后，PPU 输出的帧由两层组成。 出于演示目的，让我们用 *《超级马力欧兄弟》* 来展示它是如何工作的：

{{< tabs >}}
{{< tab name="图块" active="true" >}}
{{< tabs nested="true" float="true" class="pixel desktop-margined" figure="true" >}}
  {{< tab_figure_img name="全部图块" active="true" src="ppu_mario/chr_map.png" >}}
两个由很多图块组成的 pattern table 上下拼接在一起。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="单个图块" src="ppu_mario/single.png" >}}
一个图块。
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}
在 Character ROM 中找到的图块。  
（为了演示目的，使用了默认调色板）
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
首先，PPU 使用**图块（tile）**作为精灵图（sprite）和背景（background）的构成。

NES 将图块定义为基本的 **8x8 像素图**，它们存储在 **Character Memory**（位于游戏卡带中）并组织成被为 **Pattern Table** {{< cite "graphics-rust" >}} 的大型数据结构。 每个图块占用 16 B，一个 Pattern Table 包含 256 个图块{{< cite "graphics-pattern" >}}。 由于 PPU 最大可寻址 8 KB 的 Character Memory，因此它最多可以同时访问两个 Pattern Table。

在图块内部，每个像素使用 2-bits 编码，每个像素可以引用调色板 4 种颜色中的一种。 程序员最多可以定义 8 个调色板（4 个用于背景，其它的用于精灵图）。 每个调色板上引用的颜色指向由 64 种颜色 {{< cite "graphics-palettes" >}} 组成的“主调色板”，主调色板代表该主机可以生成的所有颜色。 调色板由 4 种颜色组成，其中一个被保留用来 `表示透明`。

要开始在屏幕上绘制一些东西，游戏会设置一些表格，其中引用了 Character Memory 中的图块。 每个表负责每一帧中的一层（精灵图或者背景）。 然后，PPU 读取这些表，将其组装成 CRT 电子枪生成的扫描线。

我现在将解释每个层/表是如何工作的，以及它们在功能方面有何不同。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="背景层" >}}
{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="总体" active="true" src="ppu_mario/nametable.png" >}}
填充好的背景图。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="选定区域" src="ppu_mario/nametable_marked.png" >}}
填充好的背景图，选定区域被标记。
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}
设置有垂直镜像的背景图，可实现平滑的水平卷动。 但是，只有一半可用。
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
背景层是一个静态图块组成的 512x480 的像素图 {{< cite "graphics-nametables" >}}。 你可能还记得可见帧要小得多，所以由游戏来决定显示哪一部分图层。 游戏还可以在运行过程中移动可见区域：**卷轴效果**就是这样实现的。

为了节约内存，4 个图块被组合成称为 **块（Block）**的 16x16 像素图，其中所有图块共享同一个调色板。

**Nametable**（保存在 VRAM中）指定在背景层中显示哪些图块。 PPU 查找 4 个 1024 Bytes 大小的 Nametable，每个 Nametables 对应背景层的一个象限。 但是，只有 2 KB 的 VRAM 可用！ 因此，如果卡带中没有额外的 VRAM，则只能存储 2 个 Nametable。 就算不添加额外的 VRAM，PPU 仍然需要指定剩下两个 Nametable 的地址。大多数游戏只是简单的让后两个 Nametable 与前两个 Nametable 使用相同的地址（这被称之为**镜像**）。

这种架构初看可能存在缺陷，但是它旨在降低成本，同时提供简单的 **可扩展性**：如果游戏需要更宽的背景，可以在卡带中包含额外的 VRAM。

每个 Nametable 的最后 64 字节用来存储**属性表（Attribute table）**，该表用来指定每个块（Block，由 4 个图块组成）的调色板{{< cite "graphics-attribute_table" >}}。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="精灵图层" >}}
{{< figure_img float="true" src="ppu_mario/sprite_layer.png" class="pixel" alt="精灵图层" >}}
渲染精灵图层
{{< /figure_img >}}

{{% inner_markdown %}}
精灵图（Sprite）是可以在屏幕上移动的图块（Tile）。 它们可以相互重叠，或者出现在背景后面。 可见性由它自身优先级数值决定（与传统图形设计软件中的“图层”概念相同）。

**Object Attribute Memory**（OAM）表指定了哪些图块被用作精灵图 {{< cite "graphics-oam" >}}。 除了图块索引外，每个条目还包含了 (x, y) 坐标以及多个属性（调色盘，优先级和翻转标志）。 该表保存在 PPU 芯片的 256 Bytes DRAM 中。

OAM 表可以由 CPU 填充。 但是实践中这样做会很慢（并且如果没有在正确的实时间点完成，会有破坏帧图像的风险），因此，PPU 包含了一个名为**直接内存访问（Direct Memory Access）**或“DMA”的小组件，可以通过对它编程（方法是修改 PPU 的寄存器）来实现从 WRAM 复制数据到 OAM。 使用 DMA，可以保证数据表会在下一帧绘制开始前传输结束，但请记住，CPU 会在传输过程中暂停工作！

PPU 被限制为每条扫描线 8 个精灵图，每帧最多 64 个。 感谢 PPU 的多路复用技术，扫描线限制是可以被突破的。 换句话说，PPU 在扫描之间会自动交替显示超出数量限制的精灵图，但是这会导致屏幕上的精灵图发生闪烁。

{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="背景分割" >}}
{{< figure_img float="true" src="ppu_mario/split.png" class="pixel" alt="背景分割" >}}
渲染结束的背景层，注意着重标记两个部分，这两个部分使用了不同的卷轴坐标值。 只有第二个部分会随着马力欧的移动而卷动。
{{< /figure_img >}}

{{% inner_markdown %}}
在继续之前，有一件事我还没告诉你。 如果你玩《超级马力欧兄弟》，你会注意到马力欧移动的时候，场景滚动很流畅。 但是，你会注意到顶部区域（统计数据所在的位置）保持静止，**即使这两个部分都是同一个背景层！**那么，这里发生了什么？ 好吧，游戏在帧生成的过程中（即所谓的 mid-frame）改变了卷轴滚动值，这样就可以显示当前世界名和统计数据（位于背景顶部的固定部分）。 NES 本身不提供这个功能，但是游戏可以通过观察 PPU 状态（主要是通过其状态寄存器{{< cite "graphics-ppustatus" >}}）来推断时机。

为了实现这一点，游戏会使用一种叫 **Sprite 0 Hit** 的技术。 超级马力欧兄弟控制 PPU 在顶部状态栏的硬币后面渲染一个隐藏的精灵图，这恰好是一帧内绘制的第一个精灵图。 在 PPU 控制的电子束绘制完成第一个精灵图后，PPU 会在它的状态寄存器更新一个标志，该标志表示第一个精灵图（又名“Sprite 0”）绘制完成。 同时，游戏会在帧生成过程中（mid-frame）不停检测 PPU 是否标记了“Sprite 0”状态（又被称为“Hit”，命中），如果命中，游戏会更新背景表的卷轴滚动属性，将可见区域移动到马力欧所在的位置。

总的来说，“Sprite 0 Hit”是一个非常微妙的操作，因为它很容易错过时机（Sprite 0 标志如果没有被及时清除，会导致重复的错误操作{{< cite "graphics-chibiakumas" >}}）。 此外，由于检测 PPU 状态需要循环执行，执行的成本会很昂贵（就 CPU 周期而言）。 从好的方面说，后续的 Mapper 通过使用自动中断接管了这一操作，当任意扫描线命中时会触发中断{{< cite "graphics-nesdoug" >}}（这是更高效的技术）。例如，《超级马力欧兄弟 3》使用这一技术显著提高了图像表现。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="结果" >}}
{{< figure_img float="true" src="ppu_mario/result.png" class="pixel" alt="结果" >}}
嗒哒——！
{{< /figure_img >}}

{{% inner_markdown %}}
完成了一帧，是时候继续下一帧了！

但是，CPU 不能修改 PPU 正在使用的任何表的内容，否则屏幕上会出现伪影。 所以，当所有扫描线完成后，PPU 会在 CPU 上触发**垂直消隐（Vertical Blank，V-Blank）**中断{{< cite "graphics-vblank" >}}。 这会通知游戏，现在可以开始更新表而不会破坏当前显示的图像了。 此时，CRT 电子束指向屏幕的可见区域下方，进入了过扫描区域（或底部边框区域）。

只有少数 PPU 寄存器可以在垂直消隐期间以外 {{< cite "graphics-outside_vblank" >}} 被修改，这解释了为什么在帧生成过程中修改背景卷轴位置不会破坏图像。
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

### 秘密和限制

如果你认为分配内存来存储完整帧缓冲的设计更可取：这会导致非常高的内存消耗，而游戏主机的设计目标是合理的价格。 让我展示给你看为什么 NES 的设计是非常有效且灵活的。

{{< tabs >}}
{{< tab active="true" name="多重卷轴滚动" >}}
{{< figure_img float="true" src="secrets/multiscrolling_mirror.png" class="pixel" alt="Nametable 配置" >}}
《超级马力欧兄弟2（USA）》  
垂直滚动的 Nametable 配置（水平镜像）  
游戏角色正在爬山。 当可见区域在底部时，PPU 有时间来渲染顶部。
{{< /figure_img >}}
{{< figure_img float="true" src="secrets/multiscrolling.png" class="pixel" alt="多重卷轴滚动" >}}
《超级马力欧兄弟 3》 马力欧可以边跑边飞，所以需要 PPU 能沿着对角线方向卷动。 注意右侧边缘显示了错误的调色板！ 左侧边缘的错误被遮住了。
{{< /figure_img >}}

{{% inner_markdown %}}
有些游戏需要主角能垂直移动——因此 Nametable 被设置为**水平镜像**。 另一些游戏需要主角左右移动，所以使用**垂直镜像**。

任何一种镜像方式都允许 PPU 在不引起用户注意的情况下更新背景图块：在可见区域外的一定距离上更新图块。

但是如果角色想沿对角线方向移动怎么办？ PPU 可以向任何方向滚动，但是如果没有额外的 VRAM，边缘会被迫共享同样的调色板（记住，图块被组织成块 ）。

这就是为什么类似*《超级马力欧兄弟 3》*这样的游戏在马力欧移动的时候会在屏幕边缘显示奇怪的图形（游戏被配置为垂直卷轴）{{< cite "graphics-seam" >}}。 这可能是因为他们需要最小化每个卡带的硬件成本（因为这个游戏已经使用了功能非常强大的 Mapper）。

一个有趣的*修复*方案：PPU 允许开发者在图块上应用一个垂直蒙版，可以有效地隐藏部分错误渲染的区域。
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="图块切换技巧" >}}

{{< tabs nested="true" float="true" class="pixel" >}}
  {{< tab_figure_img name="扫描 1" active="true" src="secrets/multiplexing_1.png" >}}
假设这是在扫描第一部分扫描线时，使用这时可用图块渲染的图像。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="扫描 2" src="secrets/multiplexing_2.png" >}}
假设这是在稍后扫描第二部分时，使用这时可用图块渲染的图像。
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="最终显示结果" src="secrets/multiplexing_complete.png" >}}
用户实际看到的帧图像。
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
《超级马力欧兄弟 3》的另外一个特点是它能显示的图形数量。

这个游戏显示的背景图块种类数量超出了 NES 的严格限制。 那么它是怎么做到的？ 如果能在图像生成时捕获两张截图，我们就可以看到最终显示的帧实际上是由*两个*不同的帧合成的。

这是 MMC3 Mapper 的另外一个神奇之处，它不仅提供了额外的程序 ROM 空间，还通过连接两个 Character ROM 芯片扩展了 Character ROM 空间。 通过检查 PPU 正在请求屏幕的哪一个部分，Mapper 会重定向 PPU 到两个芯片的访问，比起原始设计，屏幕上现在能同时显示更多的图块种类{{< cite "graphics-n3s" >}}。
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="奇怪行为" >}}
{{% inner_markdown %}}
在我的研究中，我遇到了许多描述 PPU 不常见行为的有趣文章，所以我想在这里提及其中的一些：

- [Master System 的 VDP]({{< ref "master-system#graphics" >}}) 与 NES 的 PPU 不同，Master System 的 VDP 生成 RGB 信号，然后转换为 NTSC 或 PAL 制式信号，**NES 的 PPU 则是一次性完成全部工作** {{< cite "graphics-palettes" >}}。 因此，PPU 主调色板的颜色和标准 RGB 色彩空间（目前的主流技术）之间没有一一对应的关系。 这为使用 RGB 定义 NES 的色彩留下了一定的空间，因此各种模拟器将显示不同的调色板。
  - RGB 调色板之间的差异在 Tim Worthington 的 DIY 套件中最为明显，该套件为 NES 添加了 RGB 信号输出，它还实现了一个在预定义调色板之间切换的开关{{< cite "graphics-nesrgb" >}}。
- 主调色板中有一个**“被诅咒”的颜色**（`$0D`），这种颜色会弄乱 NTSC 电视信号{{< cite "graphics-cursed_colour" >}}。 其实，这是因为电视错误的将这种颜色的信号理解为消隐信号（译注：这与模拟电视信号的实现有关，亮度信号中比黑电平更“黑”的信号会被识别为消隐信号），所以屏幕可能会出现闪烁。
- PPU 依赖 DRAM 存储 Object Attribute Memory（OAM）。 DRAM 需要不停的刷新来保持数据（不同于 SRAM），而 PPU 不渲染帧的时候 DRAM 不会刷新 {{< cite "graphics-oam" >}}。 这主要发生在垂直消隐期间。 出于这个原因，不推荐在垂直消隐期间以外更新 OAM，因为垂直消隐期间的不刷新行为会破坏部分 OAM 数据（译注：写入操作会刷新 DRAM，垂直消隐期间更新 OAM 相当于手动刷新 DRAM）。
  - PPU 的 PAL 变体不受该问题影响，因为 PAL 版在垂直消隐期间会自动刷新 DRAM （PAL 制式的垂直消隐时间更长）。
{{% /inner_markdown %}}
{{< /tab >}}
{{< /tabs >}}

---

## 音频

被称为 **音频处理单元（Audio Processing Unit）**（APU）的组件负责提供音频服务{{< cite "audio-apu" >}}。 理光将其嵌入 CPU 芯片中，大概是为了避免 CPU 和 APU 的未经许可的克隆。

### 功能

该音频芯片是一个**可编程声音生成器（Programmable Sound Generator，PSG）**，这意味着它只能生成预先定义的波形。 它由 CPU 控制。

APU 为音频数据提供了 5 个音频通道，每个通道能生成特定的波形。 音乐数据保存在程序 ROM 中。 每种波形包括不同的属性，更改这些属性可以控制波形的音调，音效或音量。 这五个通道混合后被发往音频信号。

此外，Famicom 型号的卡带包含了特殊引脚，混合后的音频可以被发送到这个引脚，然后卡带可以将卡带内部的音频通道（需要额外芯片）与之混合{{< cite "general-cartridge" >}}。

现在让我们讨论 APU 合成的波形类型{{< cite "audio-review" >}}：

{{< tabs >}}

{{< tab active="true" name="脉冲" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="脉冲 1" active="true" src="pulse_single_1" >}}
脉冲 1 通道。
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="脉冲 2" src="pulse_single_2" >}}
脉冲 2 通道。
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="完整" src="pulse_full" >}}
所有音频通道。
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Mother (1989)。{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
脉冲波形有非常明显的*哔*声，所以它主要被用于**旋律或音效**。

APU 为脉冲波形预留了两个通道。 每一个都可以通过更改脉冲宽度生成三种不同的声音中的一种。

大多数游戏使用一个脉冲通道播放旋律，另外一个播放伴奏。

当游戏需要播放音效时，播放伴奏的频道改为播放音效，然后切换回伴奏。 这样可以避免在游戏过程中打断旋律。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="三角波" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="三角波" active="true" src="triangle_single" >}}
三角波通道。
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="完整" src="triangle_full" >}}
所有音频通道。
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Mother (1989){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
这种波形用作旋律中的低音声部。 快速修改其音高也可以生成打击乐。

APU 为这种类型的波形保留了一个通道。

这个通道的音量无法控制，可能是因为音量控制被用于构造三角波。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="噪音" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="噪音" active="true" src="noise_single" >}}
噪音通道。
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="完整" src="noise_full" >}}
所有音频通道。
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Mother (1989){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
噪音波形基本上是听起来像白色静电噪音的随机波形。 噪音波形被分配了一个通道。

游戏使用噪音通道作为打击乐或者*环境*效果。

这个通道只有 32 种*预设*可用。 其中一半（16）种预设用来产生**干净的静电噪音**，另外一半用来产生**机器噪音**。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="采样" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="采样" active="true" src="sample_single" >}}
采样通道。
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="完整" src="sample_full" >}}
所有音频通道。
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Mother (1989){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
采样是可以回放的录制的音乐片段。 如你所见，采样不限制于单个波形，但是它们占用了很多空间。

APU 有一个专门的通道用来播放采样。 APU 的采样被限制为 **7 bits 解析度**（可以从 `0` 编码到 `127`），**采样率约为 15.74 KHz**{{< cite "audio-2a03ref" >}}。 要对该通道编程，游戏可以串流 7-bit 数据（这会占用大量的 CPU 周期和存储空间）或使用 **delta 调制**，只编码下一个采样和前一个采样之间的变化。

APU 中的 delta 调制实现只接受 1-bit 值，这意味着游戏只能在每次计数器增加时说明采样增加减少`1`。 所以，以保真度为代价，delta 调制可以让游戏不必将连续值串流到 APU。

由于对该通道编程需要消耗较大的空间和更多的 CPU 周期，游戏通常会储存可以重复播放的小片段（比如鼓声采样）。 但是在 NES 的整个生命周期中，许多游戏开发商都想出了办法巧妙地利用这个通道。
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### 秘密和限制

虽然 APU 的音频质量无法和黑胶唱片、磁带或 CD 相媲美，但是程序员确实找到了扩展其功能的办法，这要归功于 NES 的模块化架构。

{{< tabs >}}

{{< tab name="额外通道" active="true" >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_video name="美国" active="true" src="castlevania_usa" >}}
Castlevania III (USA/Europe, 1989)
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="日本" src="castlevania_jap" >}}
悪魔城伝説（Japan, 1989）
  {{< /tab_figure_video >}}
{{< /tabs >}}

{{% inner_markdown %}}
还记得 Famicom 提供了独占的卡带引脚用来扩展音频吗？ 嗯，像*悪魔城伝説*这样的游戏就利用了这一点，它捆绑了一个被称为 **Konami VRC6** 的芯片，添加了**两个额外的脉冲波形和一个锯齿波**。

视频中显示了这个游戏的日版和美版之间的差异（后者在没有提供音频扩展功能的 NES 上运行）。
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="颤音" >}}
{{< video src="tremolo_full" float="true" >}}
Final Fantasy III (1990)
{{< /video >}}

{{% inner_markdown %}}
比起增加卡带成本，有些游戏创造性的应用现有技术增加了更多通道。

例如，Final Fantasy III（USA）想出了使用颤音效果来“模拟”额外通道的办法。
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## 游戏

NES 游戏主要用 6502 汇编编写，并保存在**程序 ROM**中，而游戏的图形（Tile）保存在 **Character Memory** 中。

此外，游戏在任天堂许可的零售店销售（或出租）。

### 其它存储介质

尽管它只在日本发售，但是我想这是介绍这个寿命短暂但奇特的附加组件的好机会。就像 Mapper 一样，它为这台主机增加了更多功能。 这个外围设备被称为 **Famicom Disk System**（Famicom 磁碟机，FDS），它于 1986 年上架（约为 Famicom 发售后第三年）。 它有着外置软盘读取器一样的形状，并且捆绑了一个被称为“RAM 适配器”的奇怪形状的卡带。

{{< side_by_side >}}
  {{< figure_img src="fds/drive.png" class="toleft" img_class="no-borders" >}}
插入软盘的驱动器（图片中插入的是保护驱动器用的软盘形状的硬纸板）。 它可以使用 6 节 C 型电池（即 2 号电池，每节 1.5 V）或 3.6 W 的交流适配器运行。
{{< /figure_img >}}

  {{< figure_img src="fds/ram.png" class="toright" img_class="no-borders" >}}
RAM 适配器，安装在 Famicom 卡带插槽上，通过电缆连接到驱动器。
{{< /figure_img >}}
  {{< figcaption group="true" >}}
组成 Famicom Disk System（FDS）的两个组件。
  {{< /figcaption >}}
{{< /side_by_side >}}

Famicom Disk System 为 Famicom 增加了以下服务：
- 一种名为 **Famicom Disk** 的新游戏分发介质{{< cite "games-fds" >}}。 基于三美（Mitsumi）公司的“Quick Disk”，它提供了每一面**约 64 KB** 的可读写数据存储功能。
- 一个**额外音频通道**，使用了[波表合成（wavetable synthesis）]({{< ref "game-boy##tab-7-3-wave" >}})技术{{< cite "games-fds_audio" >}}。

{{< float_group >}}
{{< figure_img float="true" src="fds/mounted.png" img_class="no-borders" >}}
安装在 Famicom 上的 FDS。
{{< /figure_img >}}

{{% inner_markdown %}}
因为软盘是单一介质（相对于多芯片卡带），所有游戏数据需要被压缩进软盘。FDS 使用专有**文件系统**组织文件。

尽管如此，Famicom/NES 需要 Program Memory 和 Character Memory 才能工作，因此需要“RAM 适配器”来完成这个工作。 该组件包含 **32 KB 的Program RAM** 和 **8 KB 的 Character RAM** 来缓存软盘上的游戏数据。这样，主机就能从软盘读取，如同这是个基于卡带的游戏一样。
{{% /inner_markdown %}}
{{< /float_group >}}

为了操作驱动器，RAM 适配器嵌入了额外的 8 KB ROM 用来存储 **BIOS** {{< cite "games-fds_bios" >}}。 该程序用来执行以下任务：
- 加载初始动画。
- 从软盘启动游戏。 在幕后，BIOS 包含了一个从软盘向各个内存芯片复制数据的例程，这样主机就能读取这些数据。
- 为游戏提供 I/O API，例如遍历磁盘的文件系统。

{{< side_by_side >}}
  {{< figure_img src="fds/floppies.jpg" class="toleft" >}}
FDS 的两个零售游戏示例。  
蓝色的磁盘版本有防尘功能。
{{< /figure_img >}}
{{< video src="fds_bios" class="toright" >}}
FDS 初始动画，等待用户来……呃……插入游戏。
{{< /video >}}
{{< /side_by_side >}}

在那个年代，任天堂在零售店部署了一些“信息亭（kiosks）”，这样用户就能带上他们的软盘，以较低的价格用新游戏覆盖软盘。

不幸的是，几年以后，Famicom Disk System 停产，随后游戏又回到了卡带介质。 好的一面是，与 FDS 的功能相比，一些新 Mapper 具有相似（或更强）的功能。

---

## 反盗版和锁区

任天堂能阻止未授权的游戏发行，这要归功于 NES 中包含了一个叫 **Checking Integrated Circuit**（CIC）{{< cite "anti_piracy-cic" >}} 的专有**锁定**芯片。 它位于主机中，连接了复位信号（因此不容易被移除）。

该芯片运行一个叫 **10NES** 的内部程序，用来检查游戏卡带中是否存在另外一个锁定芯片。 如果检查失败，控制台就会被无限复位。

控制台正常运行期间，两个锁定芯片都在不停通信。 可以通过切断控制台锁定芯片上的一个引脚来破坏该系统，这会让芯片处于空闲状态。 或者，向它发送 -5V 信号就可以冻结它。

CIC 的存在的原因是 1983 年电子游戏大崩溃引起的恐惧。 任天堂时任社长山内溥决定，为了推行高质量游戏，它们将负责审批每一个游戏 {{< cite "anti_piracy-vindicator" >}}。

你会注意到日版 Famicom 主机是在 1983 年大崩溃之前发布的。 这就是为什么游戏卡带和主机都不包含 CIC 电路 {{< cite "anti_piracy-nocic" >}}，相反，引脚用于可选的音频扩展。

---

## 这就是全部了，伙计们。