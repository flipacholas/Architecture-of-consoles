---
name: Wii
date: 2020-01-05
subtitle: New ways of innovation
releaseDate: 2006-11-19
generation: 7
published: true
cover: wii
javascript: ['threejs']
top_tabs:
  Model:
    file: international
    caption: "Le Wii
    <br>Released on 19/11/2006 in America, 02/12/2006 in Japan and 08/12/2006 in Europe"
  Motherboard:
    caption: "Showing revision 'RVL-CPU-40', earlier revisions had a significantly larger fabrication process and later ones removed most of the GC I/O
    <br>NAND Flash is fitted on the back"
  Diagram:
    caption: Important data buses are labelled with its width and speed

# SEO
title: Wii Architecture
---

## A quick introduction

Even though the Wii lacked the state of art graphics its competitors enjoyed, new types of control and innovative software gave this console new areas to brag about.

Here we will analyse every aspect of this console, from its already-familiar hardware to its overlooked security system, including its major flaws.

Quick Note: Some sections overlap part of the previous article about the [Gamecube]({{< ref "gamecube">}}), so instead of repeating the information I will just put a link to the respective part of the article.

---

## Next-gen controllers

Let's start by discussing one of the most iconic aspects of this console: The controllers.

The main device is no other than the **Wii Remote** (also called 'Wiimote'), a gadget with a similar shape of a TV remote that contains many types of input controls:
- For starters, it has a **set of buttons** which are used like any conventional controller.
- It also contains an **accelerometer** to detect orientation changes, this is the main component used to achieve motion sensing.
- Finally, it includes an **infrared camera** that, combined with the accelerometer and some Wii processing, can be used to point at the screen.
  - This sensor requires the **Sensor Bar** (included with the console). The bar contains two sets of infrared LEDs which the camera can sense, the Wii uses triangulation to calculate the position of the Wiimote from the TV.

The remote is powered by **Broadcom's BCM2042**, a chip that includes all the circuitry necessary to become an independent Bluetooth device (CPU, RAM, ROM and, of course, a Bluetooth module). While the Wiimote is programmed to follow the 'Bluetooth HID' protocol to be identified as an input device, it doesn't comply with the standard method of exchanging data (possibly to disallow being used on non-Wii systems).

Lastly, the Wiimote also includes **16 KB of EEPROM** to store user data and a **small speaker** limited to low-quality samples.

#### Expansions

Nintendo shipped this system with another controller to be used on the opposite hand, the **Nunchuk**, this one comes with its own accelerometer, joystick and two buttons. It's connected to a 6-pin proprietary port on the Wiimote.

{{< centered_container >}}
  {{< linked_img src="controllers/both.jpg" alt="Wiimote and Nunchuk" >}}
  <figcaption class="caption">Nunchuck and Wiimote, respectively</figcaption>
{{< /centered_container >}}

Other accessories were also built for this port, each one provided different types of input.

---

## CPU

After the success of Gecko, IBM presumably grabbed this design and re-branded it as '750CL' for other manufacturers to use. Then, when Nintendo requested a new CPU to use with their new console, still known as 'Revolution' (hence the RVL prefix on their stock motherboards, IBM and Nintendo agreed to use a 750CL clocked twice the speed as Gecko. This CPU is known as **Broadway** and runs at 729 MHz.

After having reviewed [Gecko]({{< ref "gamecube">}}#cpu), I'm afraid there aren't many changes found in the new CPU. However, this may be an advantage: GameCube developers were able to start developing their new Wii games right away thanks to all the experience they gained with Gecko. Moreover, the fact that Broadway runs twice the speed will allow them to push for more features and quality.

#### What about memory?

This one is an interesting bit, the old Gamecube memory layout has been re-arranged and enhanced with the following changes:

- Splash (24 MB of 1T-SRAM) now resides inside the GPU package, it is now referred as **MEM1**.
- ARAM (16 MB of serial SDRAM) is long **gone**, however...
- There's a new memory chip, **MEM2**, which includes 64 MB of GDDR3 RAM for general purpose.
  - This type of memory is based on the traditional DDR2 system, but revamped with higher bandwidths (~2 times the original transfer rates) and reduced power consumption, which is ideal for GPUs.

#### Backwards compatibility

(For now) You can think of this console as a superset of the GameCube and as such, compatibility with the previous generation of games is naturally inherited. That being said, in order to make the Wii *fully* backwards compatible, the old set of external ports were brought to the Wii, these include the GameCube controller and memory card ports.
There's a new constraint however: The new memory map is incompatible with the old one, thus an emulated layer of compatibility was implemented in software (more details later on).

Regarding the GameCube accessories using the Serial/Hi-Speed socket, I'm afraid the Wii didn't include these ports, so those accessories can't be used here.

In later years, new revisions of the Wii saw these ports removed, unfortunately.

---

## Graphics

The new graphics package is called **Hollywood**, it still performs the same tasks that [Flipper]({{< ref "gamecube">}}#graphics) did back in the day but enjoying twice the clock speed (243 MHz).

This speed increase means that more geometry can be processed during the same unit of time, so Wii games will be able to feature more polygons and better-quality textures compared to GC games.

The 3D engine is still [Flipper's]({{< ref "gamecube">}}#graphics) but now called **GX**. So instead of repeating the pipeline overview, I will mention some interesting design changes that games had to undergo:

{{< tabs >}}

{{< tab active="true" name="Standardised Widescreen" >}}


{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Traditional" active="true" >}}
      {{< linked_img src="mario/4_3.png" alt="Mario Galaxy" >}}
      <figcaption class="caption">4:3 mode
      <br>Super Mario Galaxy (2007)</figcaption>
    {{< /tab >}}
    {{< tab name="Wide" >}}
      {{< linked_img src="mario/16_9.png" alt="Mario Galaxy Widescreen" >}}
      <figcaption class="caption">16:9 mode, as composed by the video encoder
      <br>Super Mario Galaxy (2007)</figcaption>
    {{< /tab >}}
    {{< tab name="Stretched" >}}
      {{< linked_img src="mario/16_9_stretched.png" alt="Mario Galaxy Widescreen Stretched" >}}
      <figcaption class="caption">16:9 mode, as displayed on a widescreen TV
      <br>Super Mario Galaxy (2007)</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Gamecube games lacked of a proper support for widescreen displays (that is, composing 16:9 frames, in contrast with the traditional 4:3). Certain games provided options to activate it, although this was still considered an exclusive feature.

Truth to be told, the video encoder outputs a fixed 640x480 pixel frame, so this 'widescreen' feature works by drawing a wider internal frame and letting the video encoder squash it in order to fit in a 640x480 map. Afterwards, your 16:9 TV would stretch the image so the final result could look *more or less* in the correct ratio. This technique is not new, it has been used in film projection and it's referred as **Anamorphic widescreen**.

Back to the point, the Wii enabled this feature globally by allowing it to set it from its system settings, which in turn promoted its widely adoption.
{{% /inner_markdown %}}

{{< /tab >}}
{{< tab name="Screen Interaction" >}}

{{< float_block >}}
  {{< linked_img src="mario/mario_galaxy.png" alt="Vertex stage diagram" >}}
  <figcaption class="caption">Super Mario Galaxy (2007)
  <br>You can pick up the stars by pointing at them</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Changes in the input system meant new interactions available on Wii games. Since the WiiMote enabled users to point at the screen, some games like *Super Mario Galaxy* or *The Legend of Zelda: Twilight Princess* used this feature to allow the player to interact with the scenery.

At first glance, this would seem like a feature easy to implement, right? Well, it's actually more complicated. You see, the CPU doesn't know what elements the user is pointing at until the GPU renders the screen (remember that the CPU feeds the geometry but can't predict what the user will see).

This feature was accomplished by checking depth (z-value) of objects overlapped by the cursor, this can only be done using the embedded frame-buffer stored inside GX, which both Broadway and GX have access to.
{{% /inner_markdown %}}


{{< /tab >}}

{{< /tabs >}}

#### Updated Designs

The speed up of Hollywood combined with revamped character designs brought some improvements to character modelling. It may not be significant compared to other generations but it's still noticeable and appreciated.

{{< side_by_side >}}
  <div class="toleft canvas-model">
    {{< threejs_canvas model="sonic_dx_tails" >}}
    <figcaption class="caption">Sonic DX (2003) for the Gamecube
    <br>1985 triangles</figcaption>
  </div>

  <div class="toright canvas-model">
    {{< threejs_canvas model="tails_brawl" >}}
    <figcaption class="caption">Super Smash Bros Brawl (2008) for the Wii
    <br>3644 triangles</figcaption>
  </div>
{{< /side_by_side >}}

#### Video Signal

Surprisingly enough, this console doesn't use the old [Multi Out]({{< ref "super-nintendo.md" >}}#a-convenient-video-out) port anymore, but a variation of it called **AV Multi Out** (so much for a name) with a slightly different shape. This one carries all of the previous signals plus **YPbPr** (known as 'component'), which is as good as RGB but only requires two lines for video (as opposed to three). It also includes some data lines that the system uses to identify the type of cable plugged in.

Unfortunately, this medium inherits the same [limitations]({{< ref "gamecube.md" >}}#connections) of the Gamecube. That is, no S-Video on PAL systems and no RGB on NTSC ones. Also, RGB can only broadcast interlaced signals (no progressive). On the other side, Nintendo finally shipped a SCART cable (as an extra accessory) which finally makes use of the RGB lines.

---

## Audio

The Wii includes the same **Macronix DSP** found in the [Gamecube]({{< ref "gamecube">}}#audio), take a look at that link to get the detailed analysis.

Compared to the GC, the only major change is that, since ARAM is gone, either MEM1 or MEM2 can be used as audio buffer.

---

## I/O

The I/O subsystem of this console is a truly game changer (*if you'll pardon the pun*). The interfaces are now controlled by a single module that will take care of security too, I'm talking about **Starlet**.

#### The hidden co-processor

Starlet is just an **ARM926EJ-S** CPU wired up to most of the internal components of this console. It resides inside Hollywood, runs at **243 MHz** (same as Hollywood) and contains its own ROM and RAM too, so you can consider it an independent computer running alongside the main CPU.

The core is similar to the one used on the [Nintendo DS]({{< ref "nintendo-ds" >}}), with the exception of including two 'special' additions:
- A 'J' in its model name, which denotes the inclusion of **Jazelle**: A dedicated unit that executes 8-bit Java Bytecode, enabling to run compiled Java code without requiring a virtual machine (known as 'JVM').
- A dedicated **Memory management unit** or 'MMU' to enable virtual memory. Useful for general-purpose operating systems.

These enhancements are a bit 'weird' since they are completely unused on the Wii. Nonetheless, Nintendo chose that core for Starlet. This reminds me of the first iPhone (2G), which also included an ARM CPU with Jazelle (wasted as well). If you wonder, Jazelle never took off, after some iterations it was discovered that Java Bytecode just ran better on software.

{{< float_group >}}

{{< float_block >}}
  <div class="img-distributed">
  {{< linked_img src="console/front.jpeg" alt="Front of the Wii" class="super-reduced-width" >}}
  {{< linked_img src="console/back.jpeg" alt="Back of the Wii" class="super-reduced-width" >}}
  </div>
  <figcaption class="caption">External I/O on the Wii
  <br>The dark & small front slot is an SD card reader</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Moving on, this 'I/O CPU' is programmed to arbitrate access to different I/O from Broadway, and in doing so it also takes care of security (which decides to whether to allow access or not), this is specially crucial when it comes to granting access to NAND, which is where the main operating system and user data are stored.

The chip also inherits some technology from ARM, for instance, it implements the **Advanced Microcontroller Bus Architecture** or 'AMBA', a protocol that facilitates the communication between other devices using a set of specialised buses.
{{% /inner_markdown %}}

{{< /float_group >}}

Having said that, Nintendo wired up the I/O in a way that makes use of two AMBA buses:
- The **AHB Bus** (AMBA High-performance Bus): As the name indicates, it's designed for high-speed communication. In here we find:
  - The **NAND** Interface: Accesses 512 MB of NAND Flash that stores the operating system and user data.
  - Two **Secure Digital Input Output** or 'SDIO' interfaces: SDIO is a protocol mainly designed for accessing the SD card, but in this case, a second one is used to control the Wifi module (802.11 b/g).
  - A **USB 2.0** Controller: Plugs to a Bluetooth 2.0 board and two external USB sockets.
  - A **SHA-1** and **AES** module: Reserved for security tasks (more details later on).
- The **APB Bus** (Advanced Peripheral Bus): This one is restricted to low-performance components, including:
  - The **Drive interface**: Connects the disc reader.
  - The **Serial interface**: Connects the Gamecube controllers.
  - The **External Interface** or 'EXI': Communicates with other Gamecube hardware, used for backwards compatibility.

#### Maintaining compatibility

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="gamecube-mode.png" alt="Wii with Gamecube equipment" >}}
  <figcaption class="caption">Wii using Gamecube equipment</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
The Wii maintains full backwards compatibility with Gamecube games even though the original I/O system has changed drastically. This is because Starlet can be reprogrammed when a Gamecube game is executed to virtually re-map the I/O just like the original Gamecube would expect to find.

Additionally, the Real-Time Clock chip includes some spare ROM that stores bitmap fonts used by Gamecube games (the Latin and Japanese set) and SRAM to save [IPL-related]({{< ref "gamecube">}}#operating-system) settings.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Operating System

There are **two operating systems** residing in the Wii, one is executed on Broadway (main CPU) and the other one on Starlet (I/O CPU). Both reside inside those 512 MB of NAND memory and can be updated.

#### Starlet's OS

Starlet is already an interesting piece of hardware, but its software is even more intriguing. You see, not only this OS has complete access to every single corner of this console, but it's also the first thing that runs when the power button is pressed.

Starlet runs a system *unofficially* referred as **Input/Output Operating System** or 'IOS' (please, do not confuse this with Apple's iOS). IOS is a fully-featured operating system and it's basically composed of:
  - A **Microkernel**: Controls the ARM9 CPU, executes **processes** and talks with other hardware using **drivers**.
  - **Drivers**: Enables the communication with hardware outside the CPU (I/O).
  - **Processes**: They perform many tasks, such as network management and implementing a file system.
  - **Cryptographic core**: Accelerates encryption-related operations (**AES and SHA-1 only**).

With this in mind, the main job of IOS is to simplify the job of the main CPU by abstracting I/O and security. For that reason, programmers don't have to worry about those matters. In order to accomplish this, Starlet **reserves between 12 and 16 MB** of GDDR3 RAM for its own tasks, the rest is used by Broadway and GX.

Broadway and Starlet communicate with each other using an **Inter-Process Communication** or 'IPC' protocol: In a nutshell, this works by both CPUs sharing two registers each. One CPU can write on the other's registers (the written data can then be translated into a command or a value) and from there, the latter CPU can perform a function in response.

The update system of IOS **is a bit tricky**: Updated IOS versions are not installed on top of old ones, but in other slots instead. This is purely **for compatibility reasons**, since it allows older Wii software to keep working using the same IOS version which was developed for.

Nintendo often released IOS updates to improve hardware support (which was necessary when a new accessory was shipped). There's only **one exception** when IOS updates actually replace older ones: When a specific version was discovered to have an exploitable vulnerability. This was only for security reasons.

When a Gamecube game is inserted, a different thing happens: Startlet boots a **MIOS** instead. This IOS just limits Starlet to emulate the original [IPL]({{< ref "gamecube">}}#operating-system).

#### Broadway's OS

This one is commonly known as the **System Menu** and effectively runs on the main PowerPC CPU (**Broadway**).

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Home" active="true" >}}
      {{< linked_img src="system/home.png" alt="System Menu at the start" >}}
      <figcaption class="caption">System menu with <i>lots</i> of channels installed</figcaption>
    {{< /tab >}}
    {{< tab name="Settings" >}}
      {{< linked_img src="system/settings.png" alt="System settings" >}}
      <figcaption class="caption">Settings menu used to change settings</figcaption>
    {{< /tab >}}
    {{< tab name="Message Board" >}}
      {{< linked_img src="system/mail.png" alt="Message board" >}}
      <figcaption class="caption">The message board stores letters grouped by date</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Compared to IOS, I wouldn't consider this a 'fully fledged' OS, but more like a 'program' that allows the user to perform the following operations:

- **Start the Wii/Gamecube game**: Only if there is a valid one inserted.
- **Change consoles settings**: Includes time, date, video mode or sensor location, among others.
- **Run apps**: One of the novelties of this console is the ability to install small Wii games (called 'WiiWare'), retro games ('Virtual Console' games) or just convenient applications (such as an internet browser). Nintendo called these **channels**, but they are also referred as **titles** by the OS.
  - Users can download/buy channels through a pre-installed channel called **Wii Shop Channel**.
  - VC channels embedded an emulator to run the game itself, notice that such emulator is not shared across the system to enable optimisations for specific games.
- **Send/Receive messages**: Wiis have a unique ID (burned in their SEEPROM chip) which can be shared to exchange messages with other Wiis. Messages can be seen on the **Message Board**.
  - Nintendo and Wii games also used this medium as newsletter as well.
{{% /inner_markdown %}}

{{< /float_group >}}

Just like IOS, Nintendo released multiple updates to this system too. Some fixed security holes, others added more features. A notable new feature was the ability to store channels in the SD card.

Any program running on Broadway (including the System Menu) relies on a specific IOS version to work. When game or a channel is booted, Starlet reboots itself using the declared version of IOS needed.

#### Update medium

Nintendo refers to them as **System updates**, they contain the two OSs in the same package and use ordinal numbers for versioning, the last version known is `4.3E` released in June 2010.

System update packages can be fetched from Nintendo's Servers or game discs. Users can manually check for updates using the System Menu. Updates are forced if a game requires a specific version of IOS which is not installed and the disc happens to contain the required packages.

#### Boot sequence

So far we have discussed two very different operating systems that reside in this console and run concurrently. This seems fairly simple, although both have to be carefully coordinated during the start of the console in order to work properly afterwards. 
That being said, the boot process of this console is as follows:

1. User taps the ON button on the console.
2. **Boot0** stage: Starlet runs a small program found in its embedded Mask ROM (1.5 KB).
    - Here Starlet basically decrypts and checks the integrity of the first 96 KB of NAND, Starlet then calculates its hash and compares it against a saved key found on Starlet's embedded OTP memory, if the hashes do not match then the console is induced in an infinite loop.
3. **Boot1** stage: Starlet runs a small program found in that aforementioned 96 KB of NAND.
    - This program will just instruct Starlet to initialise (clear) the 64 MB of GDDR3 RAM, then decrypt and verify the rest of NAND.
4. **Boot2** stage: Starlet loads the initial IOS (needed for the System Menu) and then gives power to Broadway.
5. Broadway starts the System Menu, the user is now in control.

---

## Games

While new games did not always come with considerable graphical leaps, they did surprise users with the amount of features they could now offer. This was thanks to the number of services Nintendo deployed after this console launched, ranging from the new set of controls to a standardised online infrastructure (WiiConnect24) which enabled *free* online gaming.

#### Medium

Wii games are distributed using a proprietary disc format called **Wii Optical Disc** (I know, the name can't get be more obvious). Anyhow, Panasonic designed this format based on the traditional DVD disc while adding non-standard features, like a **burst cutting area** on the inner section of the disc to prevent unauthorised reproductions. Matsushita took care of manufacturing the drive.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Physical" active="true" >}}
      {{< linked_img src="box_case.jpeg" alt="Standard game box" >}}
      <figcaption class="caption">Standard games are presented in a physical box and disc</figcaption>
    {{< /tab >}}
    {{< tab name="Digital" >}}
      {{< linked_img src="system/store.png" alt="Store channel" >}}
      <figcaption class="caption">Small games (Wiibrew) and old titles (Virtual console) can be purchased and downloaded through the Shop Channel</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Space available ranges between 4.7 GB (if single-layer) or 8.54 GB (if double-layer). Wii discs often contain **two partitions**: The first one stores system updates and the other one, the actual game.

Some games like *Super Smash Bros Brawl* included more partitions to store multiple Virtual Console games, which could be executed inside the main game.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Development

As part of the tradition, Nintendo supplied a development kit. This one was called **NDEV** and shaped like a big black brick, it came with enhanced I/O and two times the amount of MEM2 (128 MB in total) for debugging purposes.

The official software suite provided was named **Revolution SDK** and it included various tools, compilers, debuggers and frameworks to carry out development (mostly in C/C++). Nintendo distributed subsequent updates through a web portal called **Warioworld.com** (now offline/redirected) which only approved developers could access.

The official SDK relies on IOS calls to interact with the Wii hardware, this is why IOS updates are often correlated to SDK updates.

#### Return to Home

Considering all the software advancements of this console, it may surprise you that games **still run on bare metal**, which means that they have complete control of Broadway, but not of Starlet (hence, security mechanisms are implemented here). Needless to say, while games may enjoy this full-control, their behaviour is still subject to Nintendo's approval in order to authorise their distribution.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Menu" active="true" >}}
      {{< linked_img src="ingame/home.png" alt="Home menu" >}}
      <figcaption class="caption">Home menu showing in-game</figcaption>
    {{< /tab >}}
    {{< tab name="Reminder" >}}
      {{< linked_img src="ingame/strap_warning.png" alt="Wii Strap warning" >}}
      <figcaption class="caption">This screen has to be included as well</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Having said that, there's one feature found in most, if not all, games and that is the **HOME Menu**, which allows returning to the System menu. Curiously enough, all games use the same design, but how did they all manage to have the same UI despite the fact the OS does not provide this feature?

The answer is simple, Nintendo included in their SDK some **mandatory libraries** that, *lo and behold*, draw that screen.
{{% /inner_markdown %}}

{{< /float_group >}}

This is actually one of the 200 or so requirements games had to include, as ruled by the **Wii Programming Guidelines** document (found of the official SDK). Other requirements consisted in displaying the 'Wii Strap reminder' screen (which is just a bmp image) at the start of the game and how to interact with it.

#### Personalised game

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Editor" active="true" >}}
      {{< linked_img src="system/mii-editor.png" alt="Mii editor" >}}
      <figcaption class="caption">The Mii channel allows to fiddle with your own 'Mii'...</figcaption>
    {{< /tab >}}
    {{< tab name="Game" >}}
      {{< linked_img src="ingame/music.png" alt="Wii Music" >}}
      <figcaption class="caption">...Which then appears in your games
      <br>Wii Music (2008)</figcaption>
    {{< /tab >}}
  {{< /tabs >}}
{{< /float_block >}}

{{% inner_markdown %}}
Another new feature I would like to emphasise is the introduction of **Mii**, some sort of avatars that users could create using a dedicated channel called *Mii Channel*.

But the fun didn't stop there, since games could also fetch these new-created Miis to personalise gameplay.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Anti-Piracy & Homebrew

With the amount of features that this console offered, it's no surprise that hackers would want to break the security measures to execute their own software. The Wii ended up having a fantastic Homebrew library, nonetheless.

#### Copy protection

Let's start with the most common victim: **The Disc drive**.

Wii discs include the mentioned burst cutting area which is inaccessible by conventional readers. So, in the absence of this, the driver will just refuse to read the content.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="system/disc.png" alt="Insert disc screen" >}}
  <figcaption class="caption">The disc drive won't let anyone pass this screen until a valid disc is found</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Modchip developers discovered that the drive contained a debug interface called 'Serial Writter', although this port is locked until a *secret key* is entered. But once that key was discovered, modders were able to disable the copy protection and subsequently developed a modchip that automatised this process.

Matsushita released further revisions of this drive obfuscating the debug interface, however other flaws in the reader were discovered to re-enable it again.
{{% /inner_markdown %}}

{{< /float_group >}}

It's worth mentioning that the main use for modchips was piracy. The disc content is encrypted, so more research and tools were needed to run Wii homebrew. Gamecube homebrew, on the other hand, was already possible by reproducing [previous exploits]({{< ref "gamecube" >}}#tab-5-3-honourable-mention) found on the Gamecube.

#### System encryption

This is probably the most complex section of this console, yet its widespread research opened the door for lots of new developers and amazing programs.  
The Wii designed its internal security using cryptographic ciphers (AES, RSA, ECC, SHA-1 and HMAC), let's take a look at each group:

{{< tabs >}}

{{< tab name="Shared encryption" active="true" >}}

{{% inner_markdown %}}
Communication with many components (NAND, game disc and SD card) is encrypted to avoid being tampered with. Nintendo chose a **symmetric key** system meaning that it uses a single cipher to decrypt and encrypt its data. Specifically, it uses 128-bit AES keys.

Starlet has three keys stored in its OTP memory, these are written once during manufacturing:

- **Common key**: A global key generated by Nintendo that's found on all Wiis, it is used to decrypt the first layer of encryption used on channels and disc based games (we'll refer them as **titles** from now on).
- **SD Key**: This one is used to encrypt/decrypt data transferred to the SD card, and only the System Menu can perform these transfers.
  - Nintendo stored this key inside IOS too for no clear reason.
- **NAND Key**: This key is randomly generated during manufacturing process (meaning that it's unique for every Wii) and it's used to protect the NAND chip.

As you can imagine by now, Starlet is in charge of handling encryption/decryption of protected content, this is why this CPU is the only one that has access to the most sensible data.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Chain of trust" >}}

{{% inner_markdown %}}
Titles contain another layer of security, RSA-2048. This is an **asymmetric cipher** algorithm (meaning that we need two keys to decrypt and encrypt the content, so it's not enough by obtaining one key). In a nutshell, this allows Nintendo to encrypt tiles using their undisclosed 'private key', while the Wii decrypts them using the stored 'public key'.

Not only RSA allows to protect content, but it can also be used to check the integrity of encryption. You see, Nintendo uses multiple keys that are used to sign (encrypt) one on top of the other, forming a chain of encryption with the only purpose of verifying author of those keys (Nintendo) and avoiding alterations. In our case, Starlet only trusts content signed with key 'x', so if it has to decrypt a title with key 'y', it will only proceed if 'y' has been signed with key 'x'. This is called a **Chain of trust** and it's commonly used to protect most of our communications in other areas as well (for instance, HTTPS).

Starlet's OTP stores public keys (meaning that, for our purposes, it can only decrypt and verify the signature of content). The chain of trust is made of the following keys:

- The **XS Key**: This key signs 'Tickets', a type of data that contains a list of AES keys needed to decrypt titles (called 'title keys').
- The **CP Key**: Once the title is decrypted using the respective title key, the CP key is used to sign the metadata of our title (called 'TMD').
  - While it doesn't sign the content per se, the metadata includes an SHA-1 hash that Starlet uses to verify the integrity of that data.
- The **Certification Authority Key** or 'CA': Signs the XS and CP keys.
- **Root Key**: Signs the CA key.
  - Starlet only needs to stores this (public) key, the rest can be decrypted (and subsequently trusted) if it's been signed by this key.

As you can see, all of this enables Nintendo to be the sole distributor of content, which can be a good thing for game studios concerned about piracy.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="More keys" >}}

{{% inner_markdown %}}
This system also contains its own **ECC private and public keys**, ECC (Elliptic Curve Cryptography) is another algorithm similar to RSA. In this case, it's only used to sign the content transferred through the SD card. This is what prevents content copied from one Wii to be used by another one.

The key is signed by *yet another* RSA public key called **MS**, which will allow Starlet to trust the ECC key.

The final key used by this console is the **HMAC key**, which uses another algorithm that combines SHA1 hashes and HMAC. During the boot process, Starlet checks that NAND hasn't been altered by third-party hardware, to do that it computes the SHA1 hash of NAND and compares it against a saved hash to check whether they match. In addition to that, the saved hash is signed using the HMAC key to validate its authenticity.

As a final note, HMAC is stored in SEEPROM (outside Starlet), not in OTP.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

After all this, it's worth mentioning that when the system runs GameCube games, **none of the mentioned encryption methods are used**. Instead, Starlet will only check that the game only accesses certain memory locations. This is because 1/4 of GDDR3 RAM is allocated to simulate old [ARAM]({{< ref "gamecube">}}#audio)).

#### The fall of encryption

Let's start with AES keys, the algorithm may be hard to crack, but if the keys are extracted somehow (especially the common key), that layer of security would be instantly nullified. Thus, the main challenge is **how to extract them**.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="encryption.png" alt="Starlet's security diagram" >}}
  <figcaption class="caption">Starlet's security diagram</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Well, a group of hackers called **Team Twiizers** found out that the lack of signatures on Gamecube mode may be a promising surface of exploitation, and not only they discovered that 3/4 of that GDDR3 RAM was not cleared after running a GC program, but they also found out that by bridging some address points on the motherboard (using a pair of tweezers, nonetheless) they could switch the accessible banks of GDDR3 RAM, allowing to fetch hidden data from those restricted areas. Lo and behold, they found the AES keys stored in there.
{{% /inner_markdown %}}
{{< /float_group >}}

Let's not forget that this only allows to decrypt the 'first layer' of security, but in order to execute unsigned programs (Homebrew), RSA has to be cracked too. Unfortunately, this can be computationally impossible... Unless there are flaws in its implementation. Well, Team Twizzers didn't stop there so they started reversing how IOS was implemented, focusing on its signature verification functions.

RSA signature verification, without going into too much detail, works by comparing the hash of the computed RSA operation against the decrypted signature. After some fiddling, these guys discovered something hilarious: Nintendo implemented this function using `strcmp` (*C's string compare*).

For people unfamiliar with C, `strcmp` is a function used for checking if two strings are equal. This method receives two strings and an integer (stating the number of characters to be compared) as parameters, then it starts comparing each character until the end of any string is reached. Strings in C are just a chain of characters terminated by a `\0` character, this means that `strcmp` stops comparing once any string reaches `\0`. Hence, by composing a Wii title in a way that its hash has zeroes at the beginning, Starlet RSA computations will input a `\0` string, making `strcmp` always return `equal`... **Title is signed!**

As if that wasn't enough, this flaw was discovered on multiple IOS versions (even in boot1 and boot2!).

#### The dawn of Homebrew

After this, there was only one thing left: Make the exploit permanent and implement a 'user-friendly' tool so it could run unsigned programs without hassle.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="system/twilight_hack.png" alt="Twilight hack copy screen" >}}
  <figcaption class="caption">Running third-party apps was initially done by using a forged save game</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Unfortunately, these exploits required the use of extra hardware, so not every user could make use of it... Until Team Twizzers discovered *yet another* exploit: A game buffer-overflow.

I'm referring to the famous **The Legend of Zelda: Twilight Princess** (a game by Nintendo, by the way). TT discovered that the game's save file could be modified to overflow the number of characters used for naming the player's horse, so when it runs it would trigger a chain reaction that can potentially execute, let's say, a program loader.
{{% /inner_markdown %}}

{{< /float_group >}}

Since signatures were no longer an issue, this forged save file could be distributed on the net for other people to use. This effectively happened, opening the door for the Homebrew community.

#### A permanent state

While further reversing IOS, it was discovered that signatures are only performed during the installation of titles, not during execution.

{{< float_group >}}

{{< float_block >}}
  {{< tabs nested="true" >}}
    {{< tab name="Splash" active="true" >}}
      {{< linked_img src="system/homebrew_channel.png" alt="Homebrew channel" >}}
    {{< /tab >}}
    {{< tab name="Inside" >}}
      {{< linked_img src="system/homebrew/menu.png" alt="Homebrew channel menu" >}}
    {{< /tab >}}
  {{< /tabs >}}
  <figcaption class="caption">The most user-friendly hack of all
  <br><i>Unofficial</i> Homebrew channel (2008)</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
Thus TT did it again, they carefully forged an installable channel that could load arbitrary programs from the SD card. If this channel was installed before Nintendo had taken action, then the Wii could enjoy homebrew in a permanent state (independently of Nintendo patching their signature flaws in the future, which they did).

**Homebrew Channel** was the result of this, it allowed any developer and hacker to write fully-fledged programs that could benefit from full control of this system.
{{% /inner_markdown %}}

{{< /float_group >}}

#### Nintendo's response

For obvious reasons, Nintendo issued several system updates that fixed the signature exploits on multiple versions of IOS, they also took care of their flawed boot stages by also releasing new hardware revisions.

{{< float_group >}}

{{< float_block >}}
  {{< linked_img src="system/updates.png" alt="Update screen" >}}
  <figcaption class="caption">Lots of these coming through</figcaption>
{{< /float_block >}}

{{% inner_markdown %}}
However, there were still fundamental flaws discovered in this system:

- Broadway can reboot Starlet to any IOS version without extra permissions: Allowing to exploit non-patched versions.
- Hidden IOS APIs can still be used without special privileges: Allowing even more unauthorised control of hardware.
- The disc drive can receive commands to read conventional DVDs and some IOS contained hidden calls to send those commands: This was particularly worrying for piracy reasons.

{{% /inner_markdown %}}
{{< /float_group >}}

So, to wrap this up, what was left after this was just a cat-and-mouse game. Over the next months, different exploits were discovered with Nintendo subsequently trying to patch them one after another. This 'game' continued until the console reached end of life and no more updates were issued (we can assume the mouse won this one).

At the time of writing, the exploits previously mentioned in this article have already been patched. But I guess there's no arguing about the impact they made afterwards, and who can forget the enormous amount of homebrew that was made available (somebody even created a homebrew 'store', which was faster and freer than the official one).

---

## That's all folks

Happy new year 2020!

This one took me quite a while, I naively predicted that since most of the content was already done for the Gamecube, I would just have to write short paragraphs and add links here and there...

I think it turned out more informative than I expected, so I hope you found it a nice read.

Until next time!  
Rodrigo

---

## Sources / Keep Reading

#### General

- **Official Documentation, from Revolution SDK 1.17**
- **Official RVL Hardware transition guide**
- [**Dolphin devs commenting the new requirements to emulate the Wii**](https://dolphin-emu.org/blog/2018/07/21/myth-debugging-wii-more-demanding-emulate-gamecube/)
- [**Motherboard Revisions**](http://forums.modretro.com/index.php?threads/wii-board-revisions-guide.14059/)
- [**Insightful 25C3 talk about Wii hacking**](https://www.youtube.com/watch?v=0rjaiNIc4W8)

#### CPU

- **IBM PowerPC 750CL User Manual**

#### Graphics

- [**Video connector breakdown**](https://wiibrew.org/wiki/Video_output)
- [**The Models Resource** (Archived)](https://web.archive.org/web/20200216025504/https://www.models-resource.com/)

#### I/O

- [**Wiimote disassembly**](http://wiibrew.org/wiki/Wiimote)

#### Operating System

- [**IOS in detail**](https://hackmii.com/2009/06/ios-history-build-process/)
- [**Memory map**](https://wiibrew.org/wiki/Memory_Map)
- [**Boot Process**](https://wiibrew.org/wiki/Boot_process)

#### Games

- [**NDEV Revisions**](https://wiki.mariocube.xyz/index.php/NDEV)
- [**Revolution SDK Revisions**](https://wiki.mariocube.xyz/index.php/Revolution_SDK)
- **Official Wii Programming guidelines**

#### Security

- [**Disc drive structure**](https://wiibrew.org/wiki/Hardware/Disc_Drive)
- [**Good security summary**](https://wiibrew.org/wiki/Wii_Security)
- [**List of encryption key used**](https://hackmii.com/2008/04/keys-keys-keys/)
- [**Certificate chain structure**](https://wiibrew.org/wiki/Certificate_chain)

#### Photography

- Professional Wii photos: [**Evan Amos Gallery**](https://commons.wikimedia.org/wiki/User:Evan-Amos)
- Motherboard: [**Htomari Gallery**](https://www.flickr.com/photos/htomari/) (I've removed the background)
- Diagrams, casual Wii photos and game screenshots: **Me**

#### Interesting

- [**Nice HCI projects using WiiMote**](http://johnnylee.net/projects/wii/)