---
long_title: Architektura Wii
short_title: Architektura Wii
name: Wii
subtitle: Jedinečné metody inovace
date: 2020-01-05
release_date: 2006-11-19
generation: 7
published: true
cover: wii
javascript:
  - 'threejs'
seo_image_pos: "Top"
top_tabs:
  Model:
    file: international
    caption: "Wii.<br>Vydáno 19. listopadu 2006 ve Spojených státech, 2. prosince 2006 v Japonsku a 8. prosince 2006 v Evropě."
  Motherboard:
    caption: "Kód revize 'RVL-CPU-40', předchozí revize měly značně větší výrobní proces a pozdější revize odňaly většinu hardwarové zpětné kompatibility s GameCube.<br>NAND flash úložiště se nachází zespod."
    bib_source: photography-okqubit
  Diagram:
    caption: Důležité datové sběrnice jsou označeny svou šířkou a rychlostí.
#Historical
aliases:
  - /projects/consoles/wii/
---

## Rychlý úvod

Přestože Wii postrádalo nejnovější grafiku, jakou disponovali jeho konkurenti, nové způsoby ovládání a inovativní software umožnily této konzoli vystoupit z řady.

Zde budeme analyzovat každý aspekt této konzole, od již známého hardwaru až po přehlížený bezpečnostní systém, včetně jeho hlavních nedostatků.

Rychlá poznámka: Některé sekce se překrývají s předchozím článkem o [GameCube](gamecube), proto místo opakování informací jen uvedu odkaz na příslušnou část článku.

## {.supporting-imagery}

## Nadčasové ovladače

Začněme jedním z nejnápadnějších aspektů této konzole: ovladačem.

Tímto zařízením není nic jiného než **Wii Remote** (také nazývaný „Wiimote“), který svým tvarem nápadně připomíná dálkové ovládání k TV a který obsahuje mnoho vstupních zařízení:

- V prvé řadě má **fyzická tlačítka**, která se mačkají jako na jakémkoli běžném ovladači.
- Obsahuje také **akcelerometr** pro detekci změn orientace, toto je hlavní komponenta používaná pro snímání pohybu.
- A také obsahuje **infračervenou kameru**, kterou lze v kombinaci s akcelerometrem a výpočetním výkonem Wii použít k ukazování na obrazovku.
  - Tento senzor vyžaduje **Sensor Bar** (přibalen ke každé konzoli). Sensor Bar obsahuje dvě řady infračervených LED diod, které kamera snímá, a Wii využívá triangulaci k výpočtu polohy Wiimote od televize.

Ovladač je poháněn čipem **Broadcom BCM2042** [@io-wiimote], který obsahuje všechny nezbytné součástky, aby se stal nezávislým Bluetooth zařízením (CPU, RAM, ROM a samozřejmě Bluetooth modul). Přestože je Wiimote naprogramován tak, aby odpovídal protokolu „Bluetooth HID“, a byl tak identifikován jako vstupní zařízení, jeho způsob výměny dat se spárovaným zařízením není úplně standardní (zřejmě z důvodu zabránění připojení k jinému zařízení, než je Wii).

A na závěr Wiimote obsahuje také **16 KB EEPROM** pro ukládání uživatelských dat a **malý reproduktor** omezený na vzorky nízké kvality (3 kHz 4-bit ADPCM nebo 1,5 kHz 8-bit PCM).

### Rozšíření

Nintendo dodávalo tuto konzoli s dalším ovladačem, který byl určen do druhé ruky, nesl jméno **Nunchuk**, má svůj vlastní akcelerometr, joystick a dvě tlačítka. Připojoval se k 6-pinovému proprietárnímu portu na Wiimotu.

![Nunchuk a Wiimote [@photography-amos].](controllers/both.jpg)

Pro tento port bylo vyrobeno i další příslušenství, z nichž každé umožňovalo různé typy vstupů.

## CPU

Po úspěchu procesoru Gekko si společnost IBM pravděpodobně návrh tohoto procesoru osvojila a pojmenovala jej jako „750CL“ a umožnila jeho použití dalším výrobcům [@cpu-ibmcl]. Když pak Nintendo požádalo o vyvinutí nového CPU do své nadcházející konzole, stále známé jako „Revolution“ (odtud prefix RVL na základních deskách), dohodlo se s IBM na použití 750CL taktovaného 1,5krát větší rychlostí než Gekko. Výsledný CPU je známý jako **Broadway** [@cpu-ibmuser] a je taktován na **729 MHz**.

Po přezkoumání [Gekka](gamecube#cpu) se obávám, že v tomto novém CPU se nenachází mnoho změn. Ovšem to může být výhoda: GameCube vývojáři mohli začít vyvíjet své nové Wii hry ihned, a to díky všem zkušenostem, které získali s Gekkem. Navíc skutečnost, že Broadway dosahuje 1,5násobku původní rychlosti, jim umožnilo dosáhnout více funkcí a lepší kvality.

### A co paměť?

Tohle je zajímavý detail, staré rozložení paměti z GameCube bylo přeuspořádáno a rozšířeno o následující změny:

- Splash (24 MB 1T-SRAM) se nyní nachází uvnitř SoC Hollywood (popsán později) a nyní se nazývá **MEM1** [@operating_system-memory].
- ARAM (16 MB sériové SDRAM) je **ta tam**, nicméně...
- Je tu nový paměťový čip, **MEM2**, který obsahuje **64 MB GDDR3 SDRAM** pro všemožné účely.
  - Tento typ paměti je založen na tradičním systému DDR2, ale je vylepšen větší propustností (~2krát větší původní přenosová rychlost) a sníženou spotřebou energie, což je ideální pro GPU.

### Zpětná kompatibilita

Prozatím si můžete tuto nástupnickou konzoli představit jako evoluci GameCube, a proto je kompatibilita s předchozí generací her přirozeně zachována. Ovšem aby bylo Wii s GameCube zpětně kompatibilní *úplně*, musely být na Wii zachovány porty na GameCube ovladače a paměťové karty. A také vzniklo nové omezení: nové rozložení paměťové sady je nekompatibilní se starým. Proto byla do softwaru implementována tenká vrstva „emulace“ (více podrobností v sekci „I/O“ a „Operační systém“).

Pokud jde o příslušenství GameCube využívající sériový/Hi-Speed port, obávám se, že Wii tyto porty nemá, takže toto příslušenství s ním použít nelze.

Později byly GameCube porty bohužel odstraněny z nových revizí Wii také.

## Grafika

Podobně jako GameCube (kde byla grafika, rozhraní I/O a audio zkombinovány do jediného balíčku nazvaného Flipper) i Wii tento princip přejalo a vedle Broadwaye usadilo velký čip **Hollywood**. V něm najdeme grafický subsystém, který je v podstatě totožný s Flipperem, akorát s drobnými úpravami.

Takže GPU v Hollywoodu stále zastával stejnou práci jako jeho [protějšek Flipper](gamecube#graphics), akorát si mohl užívat **1,5krát** větší rychlost (**243 MHz**). Toto navýšení rychlosti znamenalo, že během stejné časové jednotky mohlo být zpracováno více geometrie a efektů.

### Funkce

Jelikož 3D engine byl stále totožný tomu z [Flipperu](gamecube#graphics), zmíním místo opakování jeho schématu některé zajímavé designové změny, kterými musely hry projít:

#### Širokoúhlý obraz jako standard {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Režim 4:3.](mario/4_3.png){.active title="Tradiční"}

![Režim 16:9, tak jak je sestaven z video enkodéru.](mario/16_9.png){title="Široký"}

![Režim 16:9, tak jak je zobrazen na širokoúhlém televizoru.](mario/16_9_stretched.png){title="Roztažený"}

Super Mario Galaxy (2007).

:::

Hry na GameCube neměly řádnou podporu pro širokoúhlé zobrazení (to znamená, že neuměly sestavit snímky ve formátu 16:9, na rozdíl od tradičního 4:3). Nicméně GPU ve Flipperu to již zvládal, a pár her tuto možnost nabízelo, přesto to bylo považováno za neobvyklou funkci.

Tak či tak, framebuffer zůstává totožný, a video enkodér tedy stále produkuje snímky v souladu s PAL nebo NTSC specifikací. Takže „širokoúhlého“ formátu se dosáhne **rozšířením úhlu pohledu** v promítací matrici. Výsledkem je obraz vykreslený s větším úhlem pohledu, který se nyní zdá být vodorovně zmáčknutý. Zde však hraje svou roli také širokoúhlá televize, protože ta následně 4:3 snímek (přicházející z konzole) roztáhne, a zobrazený obraz tak bude vypadat *víceméně* ve správném poměru. Také stojí za zmínku, že tato technika není nová, používala se při promítání filmů a nazývá se **anamorfický widescreen**. Také je zábavné, že se vývojáři SNES her potýkali s [opačným problémem](super-nintendo#display-modalities).

Vraťme se ale k Wii. Wii tuto funkci standardizovalo tím, že umožňovalo snadno aktivovat „širokoúhlý režim“ přímo z jeho systémových nastavení, což vedlo k _rozšířenému_ povědomí o této funkci (slovní hříčka není náhodná).

#### Interakce s obrazovkou {.tab}

![Super Mario Galaxy (2007).<br>Kousky hvězd seberete zamířením na ně.](mario/mario_galaxy.png) {.tab-float}

Nový a neotřelý design ovladače znamenal nové způsoby interakce ve Wii hrách. Protože Wiimote umožňuje mířit na obrazovku, některé hry jako *Super Mario Galaxy* nebo *The Legend of Zelda: Twilight Princess* použily tuto funkci k tomu, aby hráči mohli interagovat s herním prostředím.

V článku nazvaném _Myth Debugging: Is the Wii More Demanding to Emulate than the GameCube?_ [@graphics-dolphin], vývojáři emulátoru Dolphin vysvětlují, že hry jako Super Mario Galaxy nebo first-person střílečky spoléhají na [zabudovaný z-buffer](gamecube#tab-1-4-render) pro identifikaci objektu, na který Wiimote míří a/nebo hlídá, jak daleko je nějaký objekt od Wiimote kurzoru.

Nejedná se o novou funkci jako takovou, ale o nové využití současné funkcionality. GameCube hry nepotřebovaly víceúčelový ovladač s funkcí kurzoru. Nyní mohou hráči ovládat postavu a zároveň mířit na obrazovku.

### Vylepšený design {.tabs-close}

Dodatečné megahertzy Broadwaye a Hollywoodu spolu s průkopnickým designem přinesly určitá vylepšení v modelech postav. Nemusí být tak významná jako u ostatních generací, ale stále si jich lze všimnout a jsou ceněna.

![Super Smash Bros. Melee (2001) na GC.<br>4718 trojúhelníků.](mario_melee_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) na Wii.<br>5455 trojúhelníků.](mario_brawl_wii){.toright model3d="true"}

![Sonix DX (2003) na GC.<br>1985 trojůhelníků.](tails_dx_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) na Wii.<br>3644 trojúhelníků.](tails_brawl_wii){.toright model3d="true"}

### Video signál

Je překvapivé, že tato konzole již nepoužívá starý [Multi Out](super-nintendo.md#a-convenient-video-out) port ale jeho variaci s názvem **AV Multi Out** (tolik k jménu) s mírně odlišným tvarem. Ten přenáší všechny signály jako předchůdce plus **YPbPr** (známý jako „component“) [@graphics-video]. Obsahuje také pár kontaktů, které systém používá k identifikaci typu připojeného kabelu.

Bohužel, tento způsob připojení zdědil stejná [omezení](gamecube.md#connections), která měl i GameCube. Tedy žádné S-Video na PAL systémech a žádné RGB na NTSC systémech. Kromě toho RGB přenášelo pouze prokládaný (interlaced) obraz.

## Zvuk

Wii mělo stejný **Macronix DSP** čip jako [GameCube](gamecube#audio). Vizte tento článek, kde najdete podrobnou analýzu.

Jedinou velkou změnou oproti GameCube je, že jako vyrovnávací paměť určenou pro audio lze použít buď MEM1 nebo MEM2, neboť ARAM již nebyla k dispozici.

## I/O

I/O subsystém této konzole je skutečně přelomový. Rozhraní jsou nyní řízena jediným modulem, který se stará i o bezpečnost. Řeč je o čipu **Starlet**.

### Skrytý koprocesor

Starlet je jen procesor **ARM926EJ-S** spojený s většinou vnitřních součástí této konzole. Nachází se uvnitř Hollywoodu, běží na **243 MHz** (stejně jako Hollywood) a obsahuje také vlastní ROM a RAM. Proto lze brát Starlet jako nezávislý systém, který běží po boku hlavního procesoru.

![Hlavní schéma architektury Wii. Všimněte si, jak je Starlet schopen ovládat většinu I/O a dokonce může některé před Broadwayem i skrýt.](diagram.png)

Jeho jádro je podobné jádru použitému v [Nintendu DS](nintendo-ds), s výjimkou dvou vylepšení:

- „J“ v názvu jeho modelu označuje, že jeho součástí je **Jazelle**: speciální jednotka, která zpracovává 8bitový Java Bytecode. Java programy by stále závisely na virtuálním stroji (známém jako „JVM“), ale některé opkódy mohou být spuštěny přímo z CPU. Celkově to mohlo urychlit spuštění kompilovaného Java kódu.
- Specializovaná **jednotka řízení paměti (MMU)** pro zavedení virtuální paměti. Užitečné pro univerzální operační systémy.

Tato vylepšení jsou trochu zvláštní, neboť je Wii vůbec nepoužívalo. Nicméně Nintendo pro Starlet vybralo právě toto jádro. To mi připomíná, že první iPhone (2G), který v sobě měl také ARM CPU s Jazelle, tato vylepšení také vůbec nevyužíval.

Pokud vás to zajímá, Jazelle se nikdy neuchytil. Po různých iteracích se zjistilo, že Java Bytecode prostě běží rychleji na softwaru. ARM později nahradil Jazelle za „Thumb-2EE“ a v době psaní tohoto článku (červen 2021) už byly obě tyto jednotky stejně dávno vyřazeny.

::: {.subfigures .distributed .open-float .float max_subfigures=1}

![](console/front.jpeg)

![](console/back.jpeg)

Externí konektory na Wii.<br>To malé tmavé vepředu je slot na SD karty.

:::

A pokračujeme dále. Tento I/O procesor rozhoduje o přístupu k různým rozhraním a k Broadwayi, a přitom se také stará o bezpečnost (rozhoduje o tom, zda povolit přístup či nikoli). To je obzvláště důležité, pokud jde o poskytování přístupu k NAND, kde je uložen operační systém a také uživatelská data.

Čip také zdědil některé technologie od ARM, jako např. **Advanced Microcontroller Bus Architecture** (AMBA), protokol, který usnadňuje komunikaci mezi zařízeními používajícími sadu specializovaných sběrnic.

{.close-float}

S ohledem na to Nintendo připojilo I/O způsobem, který využívá dvě AMBA sběrnice [@io-diagram]:

- **AHB Bus** (AMBA High-performance Bus): jak prozrazuje název, je určena pro vysokorychlostní přenos dat. Zde můžeme najít:
  - **NAND** rozhraní: přístup k 512 MB NAND Flash, na které je uložen operační systém a uživatelská data.
  - Dvě rozhraní **Secure Digital Input Output** (SDIO): SDIO je protokol určený hlavně pro přístup k SD kartě, ale v tomto případě se druhé z rozhraní využívá pro ovládání Wi-Fi modulu (802.11 b/g).
  - **USB 2.0** Controler: rozhraní dvou externích USB slotů a vnitřního daughterboardu s Bluetooth 2.0.
  - Modul **SHA-1** a **AES**: vyhrazeny pro zabezpečení (více detailů v sekci „Ochrana proti pirátství“).
- **APB Bus** (Advanced Peripheral Bus): vyhrazeno na málo výkonné komponenty jako:
  - **Drive interface**: připojení DVD mechaniky.
  - **Serial Interface**: pro připojení GameCube ovladačů.
  - **External Interface** (EXI): toto jsme už [někde](gamecube#internal-io) viděli. Určeno pro komunikaci se zbylým hardwarem z GameCube kvůli zpětné kompatibilitě.

### Zachování kompatibility

![Wii používající GameCube příslušenství [@photography-amos].](gamecube-mode.png) {.open-float.no-borders}

Wii zachovává plnou zpětnou kompatibilitu s GameCube hrami i přesto, že se I/O systém drasticky změnil. Je toho dosaženo tak, že Starlet je naprogramován, aby po spuštění GameCube hry přemapoval I/O způsobem, že hra najde takový hardware, který očekává.

Kromě toho hodinový čip obsahuje trochu volné ROM, v níž jsou uložena bitmapová písma (latinka a japonský skript) používaná GameCube hrami; a SRAM pro uložení nastavení [BIOSu](gamecube#operating-system).

{.close-float}

## Operační systém

Obecně řečeno, ve Wii jsou **dva operační systémy**. Jeden běží na Broadwayi (hlavní CPU) a druhý na Starletu (I/O CPU). Oba systémy jsou uloženy v 512 MB paměti NAND a mohou být aktualizovány.

### OS Starletu

Starlet je už tak celkem zajímavý kousek hardwaru, ale jeho software je ještě zajímavější. Víte, nejen že tento OS má úplný přístup ke každé součástce v konzoli, ale je to také první věc, která se zapne po stisknutí tlačítka napájení.

Starlet běží na systému *neoficiálně* nazývaném **Input/Output Operating System** neboli „IOS“ (nepleťte si to prosím se systémem iOS od Applu) [@opering_system-ios]. IOS je plně funkční operační systém složený z:

- **Microkernelu**: ovládá procesor ARM9, spouští **procesy** a komunikuje s jiným hardwarem pomocí **ovladačů**.
- **Ovladačů**: umožňují komunikaci s hardwarem mimo CPU (I/O).
- **Procesů**: provádí úkoly jako je správa sítě nebo zavedení souborového systému.
- **Kryptografického jádra**: zrychluje šifrování (**pouze AES a SHA-1**).

S ohledem na to je **hlavním úkolem IOSu snížit pracovní zátěž hlavního CPU** abstraktováním I/O a zabezpečení. Z tohoto důvodu se programátoři nemusí těmito záležitostmi zabývat. Aby toho šlo dosáhnout, Starlet **si pro sebe zabírá mezi 12 a 16 MB** GDDR3 RAM, zbytek používá Broadway a GPU.

Broadway and Starlet spolu komunikují pomocí protokolu **Inter-Process Communication** neboli „IPC“: Ve zkratce sdílejí oba CPU dva registry. Jedno CPU může zapisovat do registrů druhého (zapisované údaje mohou představovat příkaz nebo hodnotu) a odtamtud přijímající CPU může plnit funkci jako odpověď.

Systém aktualizací IOS **je trochu záludný**: Aktualizované verze IOS nenahrazují staré verze, místo toho jsou uloženy do nového slotu (vyhrazená oblast v NAND pro IOS je rozdělena na „sloty“). To je čistě **z důvodů kompatibilty**, protože to umožňuje staršímu Wii softwaru nadále používat stejnou verzi IOS, pro kterou byl vyvinut.

Nintendo často vydávalo IOS aktualizace s cílem zlepšit podporu hardwaru (což bylo nezbytné, když bylo vydáno nové příslušenství). Existuje pouze **jedna výjimka**, a to když aktualizace IOS skutečně nahradila starší verze: když byla v určité verzi zjištěna zranitelnost. To bylo čistě z bezpečnostních důvodů.

Když je vložena GameCube hra, dojde k něčemu jinému: Starlet spustí **MIOS**. Tato varianta IOS pouze nařídí Starletu emulovat původní [BIOS](gamecube#operating-system).

### OS Broadwaye

Tento je obecně znám jako **System Menu** a ve skutečnosti běží na hlavním PowerPC CPU (**Broadway**).

![Systémové menu se _spoustou_ nainstalovaných kanálů.](system/home.png){.tabs-nested .active title="Home"}

![Nastavení slouží ke změně nastavení.](system/settings.png){.tab-nested title="Nastavení"}

![Na nástěnce jsou dopisy seskupené podle data.](system/mail.png){.tabs-nested-last title="Message Board"}

Ve srovnání s IOS bych to nepovažoval za „plnohodnotný“ OS, ale spíše za „program“, který uživateli umožňuje provádět následující operace:

- **Spustit Wii/GameCube hru**: pouze pokud je vložena originální hra.
- **Změnit nastavení konzole**: mimo jiné včetně času, data, režimu videa nebo polohy sensor baru.
- **Spustit aplikace**: Jedna z novinek této konzole je schopnost instalovat malé Wii hry (nazývané „WiiWare“), retro hry („Virtual Console“) nebo jen pohodlné aplikace (jako je internetový prohlížeč). Nintendo je nazývalo **kanály**, ale je OS je označuje jako **tituly**.
  - Uživatelé si mohli stáhnout/koupit kanály prostřednictvím předinstalovaného kanálu **Wii Shop Channel**.
  - Tituly Virtual Console v sobě obsahovaly emulátor, který spouštěl samotnou hru. Je dost zvláštní, že emulátor není sdílen napříč systémem ani mezi hrami stejné platformy. To umožňuje optimalizovat emulátor pro konkrétní hry.
- **Odesílat/přijímat zprávy**: Wii mají jedinečné ID (natvrdo zapsané v jejich SEEPROM čipu), které lze sdílet pro výměnu zpráv mezi ostatními Wii. Zprávy najdeme v **Message Board**u.
  - Nintendo i hry samotné toto médium používaly také k posílání newsletterů.

Nintendo vydávalo aktualizace tomuto systému podobně jako IOSu. Některé opravily bezpečnostní díry, jiné přidaly další funkce. Významnou novou funkcí byla možnost ukládat kanály na SD kartu.

Jakýkoliv program běžící na Broadwayi (včetně System Menu) se spoléhá na konkrétní verzi IOS. Po spuštění hry nebo kanálu se Starlet restartuje, a to do deklarované verze IOS.

### Podoba aktualizací

Nintendo je označuje jako **System updates**. Obsahují oba operační systémy ve stejném balíčku a používají normální čísla verzí. Poslední známá verze je `4.3E`, vydána v červnu 2010.

Balíčky aktualizací systému lze načíst z Nintendo serverů nebo herních disků. Uživatelé mohou aktualizace zkontrolovat ručně pomocí systémové nabídky. Aktualizace jsou vynuceny, pokud hra vyžaduje konkrétní verzi IOS, která není nainstalována (a disk obsahuje požadované balíčky).

### Sekvence spouštění

Zatím jsme si představili dva zcela odlišné operační systémy, které se nacházejí v této konzoli a které běží současně. To se zdá poměrně jednoduché, ale oba musí být během spouštění konzole pečlivě koordinovány, aby poté řádně fungovaly.

Proces spouštění této konzole je následující [@opering_system-boot]:

1. Uživatel stiskne tlačítko POWER na konzoli.
2. Fáze **Boot0**: Starlet spouští napevno uložený program, který se nachází v jeho vestavěné MASK ROM (1.5 KB).
    - Ve zkratce Starlet dešifruje a zkontroluje integritu prvních 96 KB NAND, poté vypočítá svůj hash a porovná jej s uloženou hodnotou nacházející se na vestavěné OTP paměti. Pokud se oba hashe neshodují, pak se konzole restartuje v nekonečné smyčce.
3. Fáze **Boot1**: Starlet spouští malý program nacházející se ve zmíněných 96 KB na NAND.
    - Tento program pouze instruuje Starlet, aby inicializoval (vyčistil) 64 MB GDDR3 RAM, poté dešifruje a ověří zbytek NAND.
4. Fáze **Boot2**: Starlet načte počáteční IOS (potřebný pro System Menu) a pak spustí Broadway.
5. Broadway spustí System Menu. Uživatel nyní může převzít ovládání.

## Hry

I když nové hry nepřišly vždy se značnými grafickými skoky, překvapily uživatele počtem funkcí, které mohly nyní nabídnout. Bylo to díky novým službám, které Nintendo spustilo při uvedení konzole; od zbrusu nového ovládání po standardizovanou online infrastrukturu (WiiConnect24), která umožnila hraní online *zdarma*.

### Médium

Wii hry jsou distribuovány na proprietárním diskovém formátu zvaném **Wii Optical Disc** (chápu, více zřejmý ten název už být nemůže). Matsushita/Panasonic vytvořily tento formát na základě tradičního formátu DVD a přidaly nestandardní vlastnosti jako **burst cutting area** na vnitřním okraji disku, jež měla zabránit neoprávněným reprodukcím.

![Standardní hry byly nabízeny na disku v krabičce.](box_case.jpeg){.tabs-nested .open-float .tab-float .active title="Fyzická"}

![Menší hry (WiiWare) a emulované hry (Virtual console) mohly být zakoupeny a staženy z Wii Shop Channelu.](system/store.png){.tabs-nested-last title="Digitální"}

Volného místa Wii disk poskytuje buď **4.7 GB** (je-li jednovrstvý) nebo **8.54 GB** (je-li dvouvrstvý). Často obsahují **dva oddíly**: první oddíl pro aktualizace systému a druhý pro samotnou hru.

Některé hry jako třeba *Super Smash Bros. Brawl* obsahovaly více oddílů, kde byly uloženy Virtual Console hry, které se daly spustit uvnitř hlavní hry.

{.close-float}

### Vývoj

Již tradičně Nintendo dodávalo vývojářský kit. Tento se nazýval **NDEV** [@games-ndev] a měl tvar obří černé _cihly_. NDEV byl dodáván s obohaceným I/O a dvojnásobným množství MEM2 (128 MB celkem) pro debuggovací účely.

Oficiální softwarová sada byla pojmenována **Revolution SDK** [@games-rsdk] a obsahovala různé nástroje, kompilátory, ladiče a frameworky pro práci na vývoji (většinou v **C/C++**). Nintendo distribuovalo následné aktualizace prostřednictvím webového portálu s názvem **Warioworld.com** (nyní offline/přesměrován), ke kterému měli přístup pouze schválení vývojáři.

Oficiální SDK spoléhá na IOS volání za účelem interakce s hardwarem Wii, a proto byly aktualizace IOS často v korelaci s aktualizacemi SDK.

### Návrat do hlavního menu

S ohledem na veškerý pokrok softwaru v této konzoli vás může překvapovat, že hry **stále běží na holém kovu**. To znamená, že mají úplnou kontrolu nad Broadwayem, ale ne nad Starletem (proto jsou bezpečnostní mechanismy právě v něm). Netřeba zmiňovat, že chování hry je stále před vydáním podmíněno schválením ze strany Nintenda.

![HOME Menu zobrazeno v průběhu hry.](ingame/home.png){.tabs-nested .open-float .tab-float .active title="Menu"}

![Tato obrazovka musí být zahrnuta také.](ingame/strap_warning.png){.tabs-nested-last title="Upomínka"}

Přesto jsou jisté prvky, které vypadají identicky napříč různými hrami. Vzpomenete si například na slavné **HOME Menu**? Stisknutím tlačítka „HOME“ na Wiimotu se ve hře zobrazí vyskakovací okno, které uživateli umožňuje vrátit se do systémového menu bez nutnosti restartovat konzoli. Vzhledem k tomu, že tuto funkci neposkytuje OS, co tedy vysvětluje, že v úplně každé hře toto grafické rozhraní vypadá stejně?

Odpověď je jednoduchá. Nintendo poskytovalo v rámci SDK pár **povinných knihoven**, které hry musely obsahovat. *Ejhle*, jedna z nich vykresluje onu známou obrazovku. Navíc toto je důvod, proč si můžete všimnout, že pouze homebrew aplikace mají svou vlastní podobu HOME Menu.

{.close-float}

Oficiální HOME Menu je jedním ze zhruba 200 požadavků, které hry musely obsahovat, jak je uvedeno v dokumentu **Wii Programming Guidelines** (nacházející se v oficiálním SDK). Dalším požadavkem bylo např. zobrazení obrazovky „Wii Strap reminder“ (což je pouze BMP obrázek) na začátku hry, nebo pravidlo, jak s touto obrazovkou zacházet.

### Personalizační tituly

![Mii Channel umožňuje vytvořit si vlastní postavičky 'Mii'...](system/mii-editor.png){.tabs-nested .open-float .tab-float .active title="Editor"}

![...které se pak objevují ve vašich hrách.<br>Wii Music (2008).](ingame/music.png){.tabs-nested-last title="Hra"}

Další novou funkcí, kterou bych chtěl zdůraznit, je uvedení **postaviček Mii**, což jsou jakési avatary, které si uživatelé mohli vytvořit pomocí kanálu s názvem *Mii Channel*.

Tím ale zábava s Mii nekončila, protože některé hry tyto uživateli vytvořené postavičky vkládaly do her, čímž obohacovaly zážitek.

{.close-float}

## Ochrana proti pirátství a homebrew

Myslím, že díky nepřebernému množství funkcí, která tato konzole nabízela, se Wii stalo velice atraktivním pro hackery, neboť prolomení bezpečnostních mechanismů umožňovalo nelicencovaným vývojářům využít veškerý jeho potenciál i bez vědomí Nintenda. Ve výsledku se Wii může těšit z fantastické knihovny homebrew aplikací.

### Ochrana proti kopírování

Začneme nejčastější obětí: **diskovou mechanikou**.

Wii disky na sobě mají výše zmíněnou „burst cutting“ oblast, ke které se běžnými čtečkami disků nelze dostat. Pokud mechanika nenajde tento ochranný prvek, odmítne čtení zbylých dat na disku.

![Disková mechanika vás nepustí dál, dokud do ní nevložíte platný disk.](system/disc.png) {.open-float}

Tvůrci modchipů zjistili, že mechanika obsahovala ladicí rozhraní s názvem „Serial Writer“ [@anti_piracy-disc], nicméně přístup k němu je zablokovaný, dokud není zadán *tajný klíč*. Přesto byla jen otázka času, kdy bude klíč objeven. Jakmile k tomu došlo, moddeři byli schopni vypnout ochranu proti kopírování a následně vyvinuli modchip, který tento proces zautomatizoval.

Matsushita vydala další revize této mechaniky, která ladící rozhraní skryla, přesto byly v mechanice objeveny další chyby, které rozhraní znovu povolily.

{.close-float}

Stojí za zmínku, že hlavním přínosem modchipů bylo prosté pirátství, protože obsah disku je šifrován, takže ke spuštění vlastního kódu bylo stále zapotřebí více výzkumu a nástrojů.

GameCube homebrew bylo možné spustit pomocí [dříve objevených](gamecube#tab-5-3-honourable-mention) exploitů, kterými trpěla konzole-předchůdce.

### Šifrování systému

Toto je nejspíše nejkomplexnější aspekt této konzole, nicméně její nikdy neutichající výzkum otevřel dveře mnoha talentovaným vývojářům a úžasným programům.

Zabezpečení Wii bylo navrženo kolem několika kryptografických metod (AES, RSA, ECC, SHA-1 a HMAC). Abychom se ve vysvětlování neztratili, podíváme se na každý aspekt zvlášť:

#### Sdílené šifrování {.tabs.active}

Komunikace mezi mnoha komponenty (NAND, herním diskem a SD kartou) byla šifrována, aby nedošlo k nedovolenému zásahu. Nintendo k ochraně zvolilo **symetrický klíč** [@anti_piracy-security], což znamená, že Wii používá stejný klíč k zašifrování a dešifrování svých dat.

Starlet obsahuje tři 128bitové AES klíče uložené v OTP paměti [@anti_piracy-key], které jsou do něj permanentně uloženy během výroby:

- **Common key**: globální klíč vygenerovaný Nintendem se nachází na všech Wii. Používá se k dešifrování první vrstvy šifrování kanálů a her na discích (odteď je budeme označovat souhrnným názvem **tituly**).
- **SD Key**: tento je použit pro šifrování/dešifrování dat přenášených na SD kartu, a pouze System Menu může provádět tyto přenosy.
  - Z nějakého důvodu Nintendo vložilo kopii tohoto klíče dovnitř IOSu.
- **NAND Key**: tento klíč je náhodně generován během výrobního procesu (což znamená, že je unikátní pro každé Wii) a používá se k ochraně NAND čipu.

Díky tomu můžeme vidět, že Starlet má na starosti šifrování/dešifrování citlivého obsahu, proto je toto CPU jediným procesorem, který má přístup k důvěrným datům.

#### Řetěz důvěry {.tab}

Tituly obsahují další bezpečnostní vrstvu, RSA-2048. Toto je **asymetrický klíč**, což znamená, že potřebujeme jeden klíč k šifrování obsahu a jiný k jeho dešifrování. Jednoduše to znamená, že Nintendo šifruje tituly pomocí neznámého klíče (private key) a Wii je dešifruje pomocí veřejného klíče (public key), jenž je uložen v konzoli. Pokud by hackeři získali veřejný klíč, nestačilo by to k prolomení bezpečnostního systému, protože se stále očekává, že data jsou šifrována pomocí neznámého klíče, který zná pouze Nintendo.

Kromě toho se RSA nepoužívá pouze pro šifrování obsahu, ale také pro kontrolu integrity tohoto šifrování. Ono Nintendo totiž používá několik klíčů k podpisu (zašifrování) již zašifrovaných dat, což dohromady tvoří řetěz šifer s jediným cílem zajistit, že:

- každý použitý klíč byl schválen Nintendem.
- data nebyla pozměněna a přešifrována bez oprávnění.

Udvedu příklad toho, jak to funguje:

1. Nintendo vytvoří klíč `x`.
2. Nintendo naprogramuje Starlet tak, aby věřil pouze datům, která jsou podepsaná klíčem `x`.
3. Pokud má Starlet dešifrovat titul pomocí klíče `y`, uskuteční tak pouze tehdy, jestli `y` byl podepsán klíčem `x`.

Tomuto postupu se přezdívá **Chain of trust** (řetěz důvěry). Mimo Wii se tato technika běžně používá k ochraně většiny naší komunikace po celém světě (například webové prohlížeče používající HTTPS spoléhají na „root certifikáty“, aby ověřily pravost neznámých certifikátů).

#### Řetěz Starletu {.tab}

OTP paměť Starletu ukládá veřejné klíče (což znamená, že pro naše účely může pouze dešifrovat a ověřit podpis obsahu). Jeho řetěz důvěry se skládá z následujících klíčů [@anti_piracy-certificate]:

- **Root Key**: podepisuje CA klíč.
  - Starlet potřebuje uložit pouze tento (veřejný) klíč, zbytek může být dešifrován (a následně důvěřován), za předpokladu, že je podepsán tímto klíčem.
- **Certification Authority Key** (CA): podepisuje XS a CP klíče.
- **XS Key**: tento klíč podepisuje „tikety“ – typ dat, který obsahuje seznam AES klíčů (nazvaných 'title keys') potřebných k dešifrování titulů.
- **CP Key**: jakmile je titul dešifrován pomocí příslušného názvu klíče, CP klíč se používá k podpisu metadat titulu (nazývaná „TMD“).
  - Ačkoli obsah sám o sobě nepodepisuje, metadata obsahují hash SHA-1, který Starlet používá k ověření integrity těchto dat.

Jak vidíte, toto všechno **umožňuje Nintendu být jediným distributorem her**, což je dobrá zpráva pro herní studia obávající se pirátství.

#### Další klíče {.tab}

Tento systém také obsahuje jeden pár **soukromých a veřejných ECC klíčů**. Elliptic Curve Cryptography (ECC) je další šiforvací algoritmus podobný RSA. V tomto případě se používá pouze k podpisu obsahu přenášeného na SD kartu i z ní. To zabraňuje tomu, aby obsah přenesený z jednoho Wii bylo možné spustit v jiném.

Klíč ECC je podepsán *dalším* veřejným klíčem RSA s názvem **MS**, který umožňuje Starletu důvěřovat ECC.

Poslední klíč, který tato konzole používá, je **HMAC klíč**, který je založen na jiném algoritmu, jenž kombinuje hash SHA-1 a HMAC. Starlet během bootování kontroluje, že NAND nebyla pozměněna hardwarem třetích stran. Kvůli tomu spočítá hash SHA-1 celé NAND a porovná jej s natvrdo uloženým hashem pro kontrolu, zda se shodují. A ke všemu je tento natvrdo uložený hash podepsán HMAC klíčem, aby bylo zajištěno, že je autentický.

Poznámka na konec: klíč HMAC je uložen v SEEPROM (nacházející se mimo Starlet), ne v OTP.

#### Pozorování {.tabs-close}

Po tomhle všem stojí za zmínku, že když systém spouští GameCube hry, **není použita žádná z uvedených šifrovacích metod**. Namísto toho Starlet pouze ověří, že hra má přístup pouze k určité části paměti. Je to proto, že 1/4 z GDDR3 RAM je přiděleno k simulaci původní [ARAM](gamecube#audio).

### Prolomení šifrování

Začněme klíčem AES. Jeho algoritmus se dá jen těžko prolomit, ale pokud jsou klíče nějakým způsobem extrahovány (zejména společný klíč), tato vrstva bezpečnosti okamžitě padá. Hlavní výzvou je tedy **jejich extrakce**.

![Schéma zabezpečení: Starlet.](encryption.png) {.open-float}

Skupina hackerů s názvem **Team Twiizers** zjistila, že nedostatek šifrování v GameCube režimu může být skvělou příležitostí k prolomení [@cpu-ccc]. Nejenže zjistili, že **3/4 z té GDDR3 RAM nebyly po spuštění GC módu vymazány**, ale že je také možné přemostění adresních bodů na základní desce pomocí pinzety. To umožnilo přístup do zakázané oblasti v paměti a následné přečtení těchto nevymazaných dat. A světe div se, v nevymazané části paměti byly nalezené AES klíče.

{.close-float}

Nezapomínejme, že to umožňuje pouze dešifrovat „první vrstvu“ zabezpečení, ale aby bylo možné spustit nepodepsané programy (homebrew), RSA musí být prolomeno také. Bohužel to může být výpočetně nemožné... Nejsou-li v jeho implementaci nedostatky. Členové Teamu Twiizers však nepolevili, takže začali prozkoumávat, jak byl IOS naprogramován, a zaměřili se na jeho funkci ověřování podpisů.

Ověření podpisů RSA, aniž bychom zacházeli do přílišných podrobností, funguje srovnáním hashe vypočítaného RSA s dešifrovaným podpisem. Tým po určitém vrtání zjistil naprostou šílenost: **Nintendo implementovalo tuto funkci pomocí `strncmp`** (porovnávání stringů v C).

Pro lidi, kteří programovat v C neumí, `strncmp` je rutina, která se používá k ověření, že jsou nějaké dva stringy identické. Tato metoda přijímá tři parametry: dva stringy a jeden integer, přičemž tento integer uvádí, kolik znaků ze stringu se má porovnat. Poté `strncmp` začne porovnávat každý znak, dokud není dosaženo konce stringu (nebo počtu znaků vymezeném integerem). Sringy v C jsou jen řetězce znaků ukončené znakem `\0`. To znamená, že `strncmp` přestane porovnávat, jakmile nějaký string dosáhne `\0`. Proto vytvořením Wii titulu, jehož hash na začátku obsahuje `\0` dojde k tomu, že RSA výpočty Starletu budou porovnávat velmi krátké hashe (či dokonce prázdné) se značnou šancí na kolize (odlišná data, která vytvářejí stejnou hodnotu hashe). Stačí malý brute-forcing a rutina strncmp vrátí, že stringy jsou `identické`... **Titul je podepsán!**

Aby toho nebylo málo, tato vada byla objevena i v několika verzích IOSu –⁠⁠⁠⁠⁠⁠ a dokonce i v rutinách v boot1 a boot2!

### Úsvit Homebrew

Poté zbývala jen jedna věc: vytvořit k exploitu trvalý přístup a vyvinout uživatelský přívětivý nástroj, pomocí kterého by se daly snadno spouštět vlastní programy.

![Spuštění aplikací třetích stran bylo původně prováděno použitím hacknutého savu.](system/twilight_hack.png) {.open-float}

Doposud zneužití těchto chyb vyžadovalo použití zvláštního hardwaru, takže se k tomu nedostal každý... Dokud Team Twiizers nenalezl *další* zranitelnost: game buffer overflow (přetečení paměti).

Mám na mysli slavnou **The Legend of Zelda: Twilight Princess** (hra od samotného Nintenda, mimochodem). TT zjistil, že uložený save lze upravit tak, aby jméno hráčova koně mělo více znaků, než je dovoleno. Když se tedy hráč pokusí spustit hru s přetečeným jménem koně, spustí by řetězovou reakci ústící v arbitrary code execution. To by bylo možné použít ke spuštění, řekněme, loaderu programů.

{.close-float}

Vzhledem k tomu, že podpisy již mohly být padělány, byl tento upravený save snadno distribuován na netu, aby jej mohli použít ostatní. V důsledku toho homebrew komunita nyní mohla spouštět svůj vlastní software.

### Trvalý stav

Jak byl IOS podrobován reverznímu inženýrství, bylo objeveno, že podpisy se hlídají pouze při instalaci titulů, ne při jejich běhu.

::: {.subfigures .open-float .tabs-nested .tab-float}

![](system/homebrew_channel.png){.tab-nested .active title="Banner"}

![](system/homebrew/menu.png){.tab-nested title="Uvnitř"}

_Neoficiální_ Homebrew Channel (2008).<br>Pravděpodobně uživatelsky nejpřívětivější hack všech dob.

:::

TT to tedy znovu dokázal. Pečlivě vytvořili instalovatelný kanál, který uměl načítat libovolné programy z SD karty. Pokud by tento kanál byl nainstalován dříve, než Nintendo přijme opatření k opravení bezpečnostních děr, pak by se Wii mohlo z homebrew těšit nadobro (i kdyby Nintendo opravilo chyby v podepisování, k čemuž nakonec došlo).

Výsledkem byl **Homebrew Channel**, jenž umožňuje každému uživateli spouštět homebrew aplikace, které mají plnou kontrolu na celým systémem (s veškerými důsledky, které to s sebou nese).

{.close-float}

### Reakce Nintenda

Ze zřejmých důvodů Nintendo vydalo několik aktualizací systému, které opravily chyby v digitálních podpisech několika verzí IOS, a také se postaralo o chyby ve spouštěcím procesu konzole vydáním hardwarových revizí.

![Tohleto se stávalo často.](system/updates.png) {.open-float}

V tomto systému však stále byly zjištěny zásadní nedostatky:

- Broadway může restartovat Starlet do jakékoliv verze IOS bez dalších oprávnění, což umožnilo využívat neopravené verze.
- Skryté IOS API mohou být stále používány bez zvláštních oprávnění, což umožnilo ovládat harware bez oprávnění ještě více.
- DVD mechanika může přijímat příkazy ke čtení běžných DVD a některé IOS obsahovaly skrytá volání k odesílání těchto příkazů. To bylo obzvlášť znepokojivé z důvodu pirátství.

{.close-float}

Abych to nějak uzavřel, tak jediné, co zůstalo, byla vlastně hra na kočku a myš. V příštích několika měsících byly objeveny jiné exploity, které se Nintendo postupně pokoušelo záplatovat. Tato „hra“ pokračovala, až dokud konzole nedosáhla konce své životnosti a žádné další aktualizace poté již nebyly vydány. Můžeme předpokládat, že myš to tentokrát vyhrála.

V době psaní tohoto článku již byly všechny uvedené exploity opraveny, ale také nahrazeny novými, fungujícími.

Domnívám se, že o dopadu hackerské scény na tento systém není pochyb, a kdo může zapomenout na obrovské množství homebrew, které bylo k dispozici (dokonce existuje i homebrew „appstore“, který byl rychlejší a svobodnější než oficiální „Wii Shop Channel“).

## To je vše, přátelé

Šťastný nový rok 2020!

Tento článek mi dal ale zabrat. Naivně jsem předpovídal, že většina věcí již byla obsažena v analýze GameCube a že budu muset dopsat jen pár odstavečků a že tu a tam doplním odkazy...

Myslím, že byl takový více informativní než jsem čekal, takže doufám, že jste si krásně početli.

Tak zase příště!  
Rodrigo
