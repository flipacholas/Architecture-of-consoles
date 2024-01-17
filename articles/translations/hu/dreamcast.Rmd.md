---
short_title: Dreamcast felépítés
long_title: A Dreamcast felépítése
name: Dreamcast
date: 2019-10-07
release_date: 1998-11-27
subtitle: Az utolsó kísérlet
generation: 6
javascript:
  - 'threejs'
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "The Dreamcast.<br>1998. November 29-én jelent meg Japánban, 1999. Szeptember 9-én Amerikában és 1999. Október 14-én Európában."
  Motherboard:
    caption: "A 'VA1' revízió.<br>Bár a hivatalos dokumentáció szerint 128 KB flash memóriával volt szerelve, valamilyen oknál fogva ezen az alaplapon ténylegesen egy 256 KB-os EEPROM chip van.<br>Az akkumulátor és a kontroller portok a 'Front panel' ('Előlap') elnevezésű kiegészítő nyomtatott áramkörön voltak."
  Diagram:
    caption: "A lényeges adatbuszokon jelölve volt a buszszélesség és a sebesség is."
#Historical
aliases:
  - /projects/consoles/dreamcast/
---

## Bevezetés

A Sega Dreamcast számos újdonságokot vezetett be az elődjéhez (a [Saturn](sega-saturn)-hoz) képest, hogy mind a játékfejlesztők, mind pedig a játékosok számára vonzó legyen. Bár ez volt a Sega utolsó kísérlete a játékkonzol-piac meghódítására, a Dreamcastben úttörő szerepet játszó technológiák közül néhány tovább élt a későbbi jelentősebb konzolokban is.

## {.supporting-imagery}

## CPU

Nem meglepő módon a Sega ismét a Hitachit választotta a CPU kifejlesztésére. Ha már olvastad [az előző cikket a Sega Saturn](sega-saturn)-ról, akkor, láss csodát, íme az SH processzor következő generációja: az **SH-4** ami döbbenetes **200 MHz** [@cpu-spec] sebességen futott.. Szóval, mi is olyan érdekes ebben a CPU-ban?

- **5 lépcsős futószalag**: akár öt utasítás is lehetett folyamatban egyszerre (részletesebb magyarázat található az [előző cikkben](sega-saturn#cpu)).
  - Az utasítás-futószalagok ennek a generációnak minden játékkonzoljában megtalálhatóak voltak, és innentől kezdve ez mindegyik új generációnak az alapja lett.
- **2 utas szuperskalár architektúra**: A párhuzamos feldolgozás új formája, ahol a CPU egynél több utasítást (ebben az esetben kettőt) képes feldolgozni a futószalag minden egyes szakaszában, ami több utasítás másodpercenkénti végrehajtását eredményezi.
- Dedikált **Floating-Point Unit (Lebegőpontos egység)** vagy 'FPU': 32 bites (*lebegőpontos*) és 64 bites ( *duplapontosságú*) számítások elvégzésre alkalmas.
- 8 KB **utasítás gyorsítótár** és 16 KB **adat gyorsítótár**: A két gyorsítótár közötti arány meglehetősen érdekes, mert a játékkonzolok általában nagyobb utasítás gyorsítótárat szoktak használni, mint adat gyorsítótárat. Ám az SH-4 lehetővét tette, hogy az adat gyorsítótárat két egyenlő részre bontsák: így lehet 8 KB *Scratchpad* (gyors memória) és 8 KB adatgyorsítótár.
- **32 bites belső felépítés**: miközben megtartották a **16 bites utasításkészletet** (a SuperH ISA-t): Ahogy az SH-2-nél is, ez növelte a kódsűrűséget és csökkentette az adatbuszok terhelését, miközben a 32 bites felépítés minden előnye megmaradt.
- **Külső 64 bites busz**: Elengedhetetlen a 64 bites értékek (pl.: a hosszú és duplahosszú egészek) számításaihoz felesleges CPU órajelciklusok elégetése nélkül.

### Extra meló

A játékkonzolok CPU-jának általános feladatai közé tartozik a játék logikájának kezelése, az ellenséget vezérlő MI futtatása és a GPU utasításokkal való ellátása. A Dreamcastban az SH-4 a grafikus futószalag nagy részében is szerepet kap, így a geometriai adatok feldolgozásában, például a perspektíva-transzformációk kiszámításában. Ennek eredményeként tartalmaz egy **128 bites SIMD** egységet, amely képes a vektorműveleteket [@cpu-arch] gyorsítani.

### A memória elérés gyorsítása

A CPU tartalmaz egy dedikált **Memory Management Unit (Memória Kezelő Egységet)** vagy 'MMU' egységet a virtuális címzéshez, ami azért hasznos, mert a CPU fizikai memória címtartománya **29 bit széles**. Így négy TLB segítségével a programozók 32 bites címeket használhattak anélkül, hogy a teljesítmény csökkent volna.

Mivel csak 29 bitre volt szükség a címzésekhez, az fennmaradó három bit vezérelte a memóriavédelmet, a memória térkép váltását és a gyorsítótár kikerülését \[@cpu-marcus\] \[@cpu-akiba\].

Csak a programozón múlt, hogy használja ezeket a funkciókat vagy nem. Az erre a rendszerre szánt játékokhoz nem feltétlenül volt _szükséges_ a memóriavédelem, és az MMU-t manuálisan kellett engedélyezni a rendszerindításkor.

### Nem UMA, de...

Bár ezt a rendszert nem a szigorú Unified Memory Architcture (Egységes Memória Felépítés) szerint tervezték, mint egy [jól ismert versenytársát](nintendo-64#simplified-memory-access), ettől függetlenül **lehetővé teszi a GPU I/O elérést**. Ez azt jelenti, hogy ha a CPU-nak olyan adatra volt szüksége, ami a dedikált memórián vagy a soros interfészen (ami szintén csatlakoztatva lehetett) kívül volt, akkor a GPU-t kellett megkérnie, és várnia is szükség esetén.

### Különleges kérések

Ennek a CPU-nak volt egy egyedi funkciója is, amit **Parallel I/O (Párhuzamos I/O)**-nak vagy 'PIO'-nak hívtak, és ami egyszerre több I/O címet tudott módosítani. A Sega úgy kötötte be ezeket, hogy a CPU a GPU **videó módját** tudta ezzel állítani (részletesen később foglalkozunk ezzel).

## Grafika

A GPU csomag egy egyedi tervezésű chip volt, amit **Holly**-nak hívtak, és **100 MHz**-en futott. A VideoLogic tervezte (amit ma már Imagination Technologies-nek hívnak), és az NEC gyártotta. A Holly 3D része teljes egészében a VideoLogic **PowerVR2** volt (amit 'PowerVR Series2'-nek és 'CLX2'-nek is neveztek).

![Sonic Adventure (1999).](sonic.png) {.open-float}

A VideoLogic a 3D motorjuk fejlesztésénél egy alternatív megközelítést alkalmazott, amit **Tile-Based Deferred Rendering (Csempe Alapú Összefűzött Renderelés) **-nek (TBDR) neveztek.

Ahelyett, hogy az egész képet egyben renderelték volna (ahogy azt a hagyományos **Immediate Mode Render (Azonnali Renderelő)**-k vagy 'IMR'-ek csinálják [@graphics-arch]), a TBDR felosztotta a renderelendő képet több, 'csempének' nevezett részletre. Ezután az egyes csempéket külön-külön renderelte le, majd az eredményeket összefűzte a végső képpé [@graphics-powervr].

{.close-float}

Ez az előremutató fejlesztés érdekes előnyökkel járt:

- Nagyszerűen **párhuzamosítható** volt, ami jelentősen csökkentette a sávszélesség- és a teljesítményigényt.
- Egy nagyon okos megoldást alkalmazott a [**láthatósági probléma**](sega-saturn#an-introduction-to-the-visibility-problem) megoldására: automatikusan sorbarendezte a poligonokat**előről hátrafelé** és már a futószalag első állomásain végrehajtotta a [z-teszteket](nintendo-64#modern-visible-surface-determination). Ezeknek a feladatoknak a kombinálása nem csak az eredeti problémát oldotta meg, de **megelőzte a felülrajzolást** is (amikor egyébként más által takart poligonokat kell raszterizálni) - ami szimplán erőforrás-pazarlás volt és csökkentette a teljesítményt.

Nem meglepő, hogy az Imagination ezt a hatékony technológiát alkalmazta a következő tervezése során, és a Series 4 PowerVR magok elképesztő mennyiségű eszközben voltak megtalálhatóak, beleértve az iPhone első generációját, az iPhone 3G-t, a Nokia N95-t, és a Dell Axim x51-et is.

### Felépítés

Vessünk egy pillantást a Dreamcast GPU-jának két fő komponensére [@graphics-marcus]:

#### Csempe-gyorsító {.tabs.active}

![A Tile Accelerator (Csempegyorsító) felépítése.](tile_accelerator.png) {.tab-float}

Mielőtt elkezdődik a renderelés, a **Tile Accelerator (Csempe gyorsító)**-nak nevezett részegység hajtja végre az előfeldolgozási feladatokat. Először is lefoglal több 32x32-es csempekockát, amelyekbe a geometria renderelésre kerül.

Ezután a csempe-gyorsító:

1. Átveszi a CPU által kiadott geometriai adatokat és rajzparancsokat (DMA vagy hagyományos adatátvitel segítségével).
2. Ezeket az adatok egy *belső* formátumra alakítja át.
3. A koordináták alapján szétosztja a geometriai adatokat a csempekockák között. A levágott geometriai adatokat eldobja.
4. Legenerálja az eredményül kapott Display List (Megjelenítési listák)-at.

Ezeket a megjelenítési listákat végül a PowerVR2 3D motor dolgozza fel.

#### A PowerVR2 mag {.tab}

![A PowerVR2 mag felépítése.](powervr2.png) {.tab-float}

Ez az a rész, ahol a grafika "életre kel": a csempe-gyorsítótól kapott megjelenítési listák alapján a grafikus mag rendereli le egyetlen csempe geometriáját a **belső képkocka puffer** használatával. A folyamat a következő:

1. A **Image Synthesis Processor** vagy "ISP" lekérdezi a primitíveket (háromszögeket vagy négyszögeket), és elvégzi a **Hidden-Surface Removal** műveletet a nem látható sokszögek eltávolítására. Miután megtörtént a Z-pufferek és a sablonpufferek kiszámolása, az adatok átmennek a **Depth Testing (Mélység Teszt)**-en, hogy elkerüljék az olyan sokszögek renderelését, amelyek más sokszögek mögött jelennének meg, valamint a **Stencil Tests (Sablon teszt)**-en, ami azokat a geometriai adatokat szűri ki, amelyek nem lennének láthatóak, ha egy 2D-s sokszög mögött helyezkednek el (ezt **maszkolásnak** is hívják).
    - Figyeljük meg, hogy ezeket a teszteket már a futószalag első állomásán elvégzik. Ezzel ellentétben a korábbi játékkonzolok [késői z-bufferelést](nintendo-64#modern-visible-surface-determination) alkalmaztak, és a futószalag végén dobták el a geometriai adatokat. Az ISP által biztosított módszer megelőzi az olyan geometriai adatok feldolgozását, amelyek végül eldobásra kerülnének [@graphics-surface], így takarékoskodik az erőforrásokkal.
2. A **Texture and Shading Processor (Textúrázó és árnyékoló processzor)** vagy 'TSP' végzi el a színezést, árnyékolást és más effekteket a csempekockákon.
    - A textúrák egészen az exportálásig nem kerülnek a csempekockára, így az esetleges túlrajzolás nem csökkenti a kitöltési teljesítményt.

Miután a műveletet befejeződik, a renderelt csempe a fő képpufferbe íródik a VRAM-ban. Ez a folyamat addig ismétlődik, amíg az összes csempe el nem készül. Ha ez megtörtént, a kapott képpuffert a **Videókódoló** felolvassa, és megjeleníti a videókimeneten keresztül.

### A nagy kép {.tabs-close}

Eltekintve a nyilvánvaló felépítésbeli különbségektől is, a TSP sok olyan képességgel rendelkezett, amelyekből kiderül, hogy mennyire távol állt ez a játékkonzol a régi [Saturn](sega-saturn)-tól. Íme néhány figyelemre méltó példa:

- **Alpha blending (Alfa keverés)**: Az egymást fedő rétegek színeinek keverése, hogy átlátszó hatást keltsen.
  - Ebben a rendszerben az átlátszóság alkalmazására használt eljárást **sorrendtől független átlátszóságnak** nevezik. Az algoritmus automatikusan sorbarendezi a primitíveket a színek keverése előtt, és bár ez lelassítja a renderelési folyamatot, elkerülhető, hogy a játéknak magának kelljen elvégeznie a rendezést. Pontosan emiatt a Dreamcast játékok remekeltek az átlátszó objektumok megjelenítésében.
  - A csempe alapú rendszer, kombinálva a sorrendfüggetlen átlátszósággal teljes megszüntette a korábbi [hibákat](sega-saturn#the-transparency-issue).
- **Mip-Mapping**: A kívánt részletességtől függően automatikusan kiválasztja a textúra lekicsinyített verzióját. Ezt azért csinálja, hogy elkerülje a feleslegesen nagyméretű textúrák feldolgozását, amelyek a kamerától amúgy is távolabb lennének csak láthatóak (ami így teljesítménypazarlás lenne, és a textúrák szélén látható aliasinget, fűrészfogazást eredményezne).
- **Environment mapping**: Tükröződéseket hoz létre a textúrákon.
- **Bilinear, Trilinear és anisotropic szűről**: Ezek különböző algoritmusok, amelyeket a textúrák simítására és a pixelesedés megakadályozására használnak. Ez a sorrend a 'legrosszabbtól' a 'legjobbig', és a kapott eredmény arányos a szükséges számítási teljesítménnyel.
  - Ez hatalmas előrelépés a Saturnhoz képest, ami egyáltalán nem tudott semmilyen textúra szűrőt használni!
- **Bump mapping**: Fényekkel és árnyékokkal szimulálja a felületek hibáit (bemélyedések, stb.), anélkül, hogy további sokszögeket kellene alkalmazni a formák megváltoztatására.

### Egyre több részlet

A Holly így már közel tízszer több sokszöget tudott kirajzolni, mint [az elődje](sega-saturn). Itt van egy gyors *előtte - utána* összehasonlítás, ami bemutatja, hogy a modellterveknek már nem kellett olyan korlátozottaknak lennie, mint előtte. Nézd meg alaposabban minden oldalról!

![Sonic R (1997) a Saturnon: <br>286 háromszög (vagy 185 négyszög).](sonic_r_saturn){.toleft model3d="true"}

![Sonic Adventure (1999) a Dreamcasten: <br>1001 hármszög.](sonic_adventure_dc){.toright model3d="true"}

### Videó módok

A videórendszert úgy tervezték, hogy többféle képernyőtípust és formátumot támogasson, ezért a videókódoló a kimeneti jelet egy egységes aljzatra küldte ki, az alábbi jeltípusokkal:

- **Kompozit**: A videójel megjelenítéséhez szükséges három jelet (chroma, luma és szinkron) kombinálja egyetlen jelbe, így csak egy egyszerű, egypólusú kábel szükséges hozzá.
  - Ezeket a régi PAL és NTSC TV-ken használták RCA kábellel.
- **S-Video**: A luma és a szinkron jeleket kombinálta, és megtartotta a chroma jelet külön (így két vezeték kellett összesen).
- **RGB**: Külön piros-zöld-kék színcsatorna jeleket használt, és különböző szinkronizálási típusok közül lehetett választani (kompozit szinkronizálás vagy a kompozit vagy S-Video videójelből használt szinkronizálás).
  - A SCART kábel használja ezt a jeltípust.
- **VGA**: az RGB jelet két szinkronjellel (víszintes és függőleges) kombinálja, így összesen öt vezeték kell hozzá összesen. Ez tette lehetőve a legmagasabb felbontást (720x480) progresszív módban (emiatt ezt a videómódot gyakran nevezték 480p-nek is). Ekkora a VGA igazából a számítógépes monitorok szabványos formátumat volt már egy ideje.
  - Ennek a videójel formátumnak a használatához a Sega egy VGA adaptert is árult kiegészítőként.

A Dreamcast nem tudja ezeket egyszerre kódolni, ezért a GPU és az audioprocesszor tartalmaz egy **Image Mode (Kép Mód)** nevű regisztert, amely koordinálja, hogy melyik videó/audió busz lesz aktiválva a kért jel létrehozásához. A CPU érzékeli a csatlakoztatott kábel típusát (a videócsatlakozó aktív "kiválasztó bitjeinek" ellenőrzésével), és a GPU-ba írja a szükséges értékeket. Legvégül a GPU a szükséges beállításokat továbbítja az audióprocesszornak.

Mivel a VGA szigorúan progresszív típusú jel (szemben a hagyományos *váltottsoros* jelekkel), néhány kompatibilitási felmerül olyan játékoknál, amelyeket csak interlaced videóhoz terveztek. Ezek kódjában direkt szerepel, hogy a játék nem működik VGA-n, így a CPU blokkolja a játékot, amíg a felhasználó ki nem cseréli a VGA-kábelt egy másik típusra.

## Audió

A hangfunkciókat a Yamaha által tervezett **AICA** nevű egyedi chip kezeli, amely a Saturnban használt [SCSP](sega-saturn#audio) továbbfejlesztett változata, és négy komponensből áll:

- A **Sound Integrated Circuit (Hang integrált áramkör)** vagy "IC": három modulból áll (szintetizátor, DSP és keverő), amely a hangjelet generálja és effektezi azt. Maximum **64 PCM csatornát** támogat **16 vagy 8 bites** felbontással, **44.1 kHz** mintavételezési frekvencia mellett. Összességében ez az optimális minőség az audióejátszáshoz.
  - Ezenkívül tartalmaz egy **ADPCM dekódert** is, hogy a tehermentesítse a CPU-t a feladatok egy része alól.
  - Érdekes módon, bizosít **két MIDI csatlakozótűt** is egy MIDI hangszer csatlakoztatására, bár ez a fejlesztőknek lett szánva.
- **2 MB SDRAM**: Hangadatokat és programokat tárol. A fő CPU DMA segítségével másolta ide az adatokat.
- Egy **ARM7DI**, amely ~2,82 MHz-en fut: vezérli a hang IC-t. Ezt a CPU-t egy SRAM-ban tárolt kis szoftver ([eszközmeghajtó](super-nintendo#audio)) indításával programozzák, amely értelmezi az audióadatokat és ennek megfelelően manipulálja a Sound IC-t.
  - Ha esetleg tudni akarod: hasonló CPU található a [Game Boy Advance](game-boy-advance)-ban is.
- **Memóriavezérlő**: Interfész a 2 MB SDRAM memóriához.

A fejlesztés megkönnyítése érdekében a hivatalos SDK több hang eszközmeghajtót is tartalmazott a különböző igényekhez (szekvenálás, dekódolás stb.).

### Fejlődés

A [Mega Drive/Genesis](mega-drive-genesis#audio) óta nagyon messzire jutottunk. Ahhoz, hogy megmutassuk, mekkora előrelépés történt a hangszintézis terén, íme egy példa két játékra, az egyik Mega Drive-on, a másik Dreamcaston, amelyek ugyanazt a kompozíciót használták:

![Sonic 3D Blast (1996) a Mega Drive-on.<br>Az előd FM-szintézist alkalmaz a hangjelek menet közbeni előállításához.](megadrive){.toleft video="true"}

![Sonic Adventure (1999) a Dreamcaston: <br>Az új audió alrendszer gond nélkül feldolgozza a PCM-mintákat.](dreamcast){.toright video="true"}

Az FM chip programozása helyett a Sonic Adventure zeneszerzői házon belül készítették el a hangsávot, majd a CRI Middleware által kifejlesztett "ADX" veszteséges formátumba kódolták. Emiatt van az, hogy a 64 PCM-csatornából csak kettőt használ (sztereó).

Az ADX tömörítés lehetővé tette, hogy a játék közvetlenül a GD-ROM-ról folyamatosan olvassa és dekódolja az adatokat a Sound IC-re anélkül, hogy a memória vagy a sávszélesség elfogyna. Az illesztőprogram sokféleképpen megvalósítható, mivel többféle megközelítés létezik a fő CPU és az ARM7 közötti terhelés elosztására.

### Életben maradni

Valamért ez a chip felelős egy **Real Time Clock (Valósidejű óra)** (RTC) biztosításáért is, amit a BIOS használ - ez egy óraelemhez is csatlakozik, hogy táp nélkül is működjön.

## Operációs rendszer

A 2 MB "System ROM" tárolja a **BIOS**-t, amely a konzol bekapcsolásakor elindítja a játékot vagy egy kis keretprogramot.

A BIOS olyan rutinokat is tartalmaz, amelyeket a játékok az I/O funkciók egyszerűsítésére használnak [@games-redream], mint például a GD-ROM meghajtóról való olvasás.

### Keretprogram

Ha nincs érvényes játéklemez behelyezve, a konzol elindítja a grafikus keretprogramot.

![A keretprogram lemez nélküli indítás esetén.](shell.png) {.open-float}

Ez egy egyszerű grafikus felhasználói felületet tartalmaz, amely lehetővé teszi a felhasználó számára olyan alapvető, de szükséges feladatok elvégzését, mint:

- A játék elindítása (ha még nem történt meg).
- A VMU-ban lévő mentett adatok kezelése (erről az eszközről részletesebben kicsit később!).
- Zene lejátszása, ha audió CD van behelyezve.
- Néhány beállítás megváltoztatása (dátum, idő, hang, stb.).

{.close-float}

### Windows CE

A Dreamcast bejelentése óta lehetett azt hallani, hogy a konzol képes a **Windows CE** futtatására: ez a Windows egy lecsupaszított verziója, amelyet beágyazott eszközökön való használatra terveztek. Ez egy kicsit félrevezető volt, tekintve, hogy egyes felhasználók azt várták, hogy a konzoljukon egy teljes Windows CE asztali környezet fog futni.

![A Windows CE logó a ház elejére nyomva.](windows_ce.jpeg) {.open-float}

Valójában ennek az "operációs rendszernek" a célja nagyon hasonló volt ahhoz, amit a Nintendo a [a Nintendo 64](nintendo-64#operating-system) rendszerrel tett: a programozók számára egy jelentős absztrakciós réteget biztosítani bizonyos műveletek egyszerűsítésére.

A Microsoft együttműködött a Segával, hogy a Windows CE-t a Dreamcastra is eljuttassa [@games-sdk]. Az eredmény egy olyan lecsupaszított Windows CE lett, amely a grafikához, hanghoz és hibakereséshez szükséges minimális komponensekkel rendelkezett. Ez magában foglalta a Microsoft csúcs integrált fejlesztőkörnyezetét, a **Visual Studio**-t is, amit a fejlesztők használhattak a játékokhoz.

{.close-float}

Néhány fejlesztő nagyon vonzónak találta ezt a lehetőséget. Mivel a CE-hez mellékelt multimédia keretrendszer nem más volt, mint a **DirectX 6**, a kor több ezer PC-s játéka elméletileg könnyen átültethető volt a Dreamcastra...

A Dreamcast és a hagyományos PC közötti tervezési különbségek azonban túl nagyok voltak ahhoz, hogy figyelmen kívül lehessen hagyni őket [@games-direct]. Emellett ennek a rendszernek a beágyazása megnövelte a játékok betöltési idejét (elvégre az operációs rendszert egy lemezről kellett betölteni), és a Windows CE történetesen a Dreamcast erőforrásainak jelentős részét felemésztette (*nem meglepő módon a PC-k is szenvedtek már ettől*).

Végül a "Windows CE for Dreamcast" csak egy újabb SDK volt a fejlesztők számára (általában **Dragon SDK** néven emlegették). Mindazonáltal jelentős számú Dreamcast játék fejlesztője választotta végük a Windows API-kat és a DirectX-et.

## I/O

A GPU tartalmazott még egy modult, ami az I/O nagy részét kezelte, ez volt az úgynevezett **System Bus**. A következő interfészeket biztosította:

- A **G1** interfész: a **BIOS ROM** és a mentett beállításai valamint a **GD-ROM** tartalma volt elérhető ezen keresztül.
- A **G2** interfész: a **modem** és a **hang vezérlő** elérését tette lehetővé.
- A **Maple** interfész: Adatkötegeket továbbított a vezérlők (és a hozzájuk csatlakoztatott tartozékokkal) valamint a CPU között. Ez egy **soros busz** volt, saját DMA-val.
- Az **SH-4** interfész: a CPU-hoz kapcsolódott általános célú kommunikációra.
- A **DDT** interfész: a CPU buszt vezérelte, a DMA átvitelek alatti memória eléréshez.
- A **PVR** interfész: A CPU-t kapcsolta össze a Tile Accelerator (Csempe Gyorsítóval), saját DMA használatával.

## Játékok

A fejlesztés legnagyobbrészt **C**-ben vagy **C++**-ban folyt. Eleinte a C volt az ajánlott választás, mivel a rendelkezésre álló C++ fordítók kezdetben nagyon korlátozott funkcionalitásúak voltak.

A Sega fejlesztői hardvert is biztosított egy PC-szerű torony formájában, amelyet **Sega Katana Development Box**-nak hívtak. Ez egy Dreamcast hardver volt, bővített I/O képességekkel a fejlesztés megkönnyítése érdekében. Tartalmazott egy CD-t is, amelyen a hivatalos **Katana SDK** és egy Windows 98-at futtató PC-re telepíthető eszközök voltak.

Abban az esetben, ha a fejlesztők inkább a Dragon SDK-t választották, a DirectX 6.0 és a Visual C++ 6.0 is elérhető volt számukra.

### Adattároló

A játékok a GD-ROM-on voltak, amik igazából csak nagyobb pitsűrűségű CD-ROM-ok voltak (a kapacitás elérte az 1 Gigabyte-ot). A sebesség 12x-es, ami *nem gyenge* a Saturn 2x CD olvasójához képest.

### Online platform

A Dreamcastot egy beépített **modem** egységgel szállították, amellyel a játékok "behívhattak" egy betárcsázós szolgáltatásba az online játékhoz. A Sega két ilyen szolgáltatást nyújtott: a **SegaNet**-et (Amerikában és Japánban) és a **Dreamarena**-t (ez volt az európai megfelelője).

A játékosok a szolgáltatáshoz a **DreamKey**, egy extra lemez segítségével regisztráltak, amelyet egyes játékokhoz mellékeltek. A DreamKey egy webböngészőt biztosított, amivel regisztrálni lehetett egy felhasználói fiókot. Kezdetben a DreamKey a régiótól függően előre konfigurált szolgáltatásként jelent meg, de a későbbi módosítások lehetővé tették a felhasználók számára, hogy megváltoztassák az internetszolgáltató beállításait, és így bármelyikhez csatlakozhassanak.

Egy Dreamcast márkájú billentyűzet és egér is megvásárolható volt, ha a felhasználó *PC-stílusban* szeretett volna netezni.

Sajnálatos módon a SegaNet és a Dreamarena alig két évvel a megjelenés után megszűnt. Így azok a játékok, amelyek kizárólag ezekre támaszkodtak, használhatatlanná váltak, kivéve, ha az ilyen szolgáltatásokat extra eszközökkel emulálják (mint például a DreamPi, egy Raspberry Pi-re telepíthető rendszer, amely a felhasználói közösség által fenntartott szerverek segítségével replikálja az eredeti Sega szolgáltatásokat).

### Interaktív memóriakártya

A Dreamcast egy másik innovatív funkciója a **Vizuális memóriaegység** vagy "VMU" volt. A kontrollerhez csatlakozott, és azon kívül, hogy memóriakártyaként is szolgált, egy teljes értékű eszköz volt, ami a következőket tartalmazta [@games-vmu]:

![A VMU lecsatlakoztatva a kontrollerről.](vmu.png){.tabs-nested .active .open-float .tab-float title="VMU"}

![A kontroller a VMU nélkül.](controller.png){.tab-nested title="Lecsatlakoztatva"}

![A kontroller csatlakoztatott VMU-val.](controller-vmu.png){.tabs-nested-last title="Csatlakoztatva"}

- **Sanyo LC86K87**: Egy 8-bites, alacsony fogyasztású CPU.
- **32x48 monkróm LCD** négy további ikonnal: ezeket az XRAM (external RAM) 196 byte-jával lehetett vezérelni (mint frame-buffer).
- **Két soros csatlakozó**: Egy ki és egy bemenet.
- **Hat fizikai gomb**: Akkor használatos, amikor a VMU nem csatlakozott a kontrollerre.
- **16 KB Mask-ROM**: a BIOS-IPL-t tárolja.
- **64 KB Flash**: 32 KB egy (a konzolról átvett) program tárolására, a másik 32 KB pedig a Dreamcast mentéseinek tárolására.
- **512 byte RAM**: 256 byte a rendszer számára van fenntartva, így csak 256 byte áll rendelkezésre a program számára.

{.close-float}

A VMU két módban tudott működni:

- **A kontrollerhez csatlakoztatva**: A hivatalos kontroller két aljzattal rendelkezik a VMU-k és más, azonos formátumú kiegészítők csatlakoztatására, ha a VMU az első (a kontroller előlapjáról látható) nyílásba van behelyezve, akkor játék közben rajzokat tud megjeleníteni. Ezen kívül a Dreamcast képes mentéseket és egy programot tárolni a VMU-n.
- **A vezérlőről leválasztva**: A kütyü egy Tamagotchi-szerű eszközzé válik órával, mentéskezelővel, és képes futtatni bármilyen programot, amit a Dreamcastról korábban átmásoltak. Két VMU össze is kapcsolható a tartalmak megosztására.

## Anti-Piracy & Homebrew

A teljesen egyedi GD-ROM formátum használata megakadályozta a játékok engedély nélküli másolását (és más konzolokon való futtatását). A Dreamcast-játékok *régiózárral* is el vannak látva, ami azt jelenti, hogy a konzol nem hajlandó futtatni egy más földrajzi régióba szánt játékot.

### ... és a legyőzése

A gyarkorlatban ezek a kalózkodást megakadályozó technikák *hihetetlenül* haszontalanaok voltak, ugyanis a Sega nyitva hagyott egy hatalmas hátsó bejáratot: a **MIL-CD**-t. A Music Interactive Live-CD vagy 'MIL-CD' egy olyan formátum, amelyet a Sega hozott létre, hogy egy Audió CD-t interaktív programokkal bővítsen... és a Dreamcast kompatibilis volt ezzel [@anti_piracy-history].

Így végül a nem engedélyezett lemezek (csalások, filmlejátszók stb.) MIL-CD-nek álcázták magukat, hogy a Sega jóváhagyása nélkül fussanak a konzolon. Később a különböző hacker csoprtok kivesézték ezt a biztonsági rést, és találtak egy megoldást a kalózjátékok hagyományos CD-ROM-ról történő indítására. Ez az kalóz ISO-k megállíthatatlan hullámát indította el a neten.

Néhány probléma felmerült azért: Bár a GD-ROM-ok egy gigabájtnyi adatot képesek tárolni, a CD-ROM-okra csak ~700 MB fért el. Vajon hogyan tudták a "ripperek" a nagyobb játékokat úgy lekicsinyíteni, hogy elférjenek egy CD-n? A zene és a grafika újratömörítésével, amíg el nem fért. Még az is előfordult, hogy két lemezre szétszedték a játékokat. Végül is a játékadatok már nem egyetlen összefüggő bináris (mint a régi cartridge-okon), hanem hierarchikusan, fájlokba és könyvtárakba vannak szervezve.

## Ennyi volt, srácok

![Egy Dreamcast, amit szereztem, hogy mindent leírhassak róla ide.<br>A korához képest nem is olyan rossz!](folks.png)

Remélem, tetszett ez a cikk. Az utolsó egyetemi évem elején fejeztem be az írását.

Mostantól valószínűleg elég elfoglalt leszek, de nagyon élvezem ezeket a cikkeket írni, így remélhetőleg néhány héten belül megkapjátok a következőt!

Addig is:   
Rodrigo
