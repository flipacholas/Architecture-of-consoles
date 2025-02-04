---
short_title: Architektura Game Boy Advance
long_title: Architektura Game Boy Advance
name: Game Boy Advance
date: 2019-08-18
release_date: 2001-03-21
generation: 6
subtitle: Jeden chip, by wszystkimi rządzić
cover: gba
top_tabs:
  Model:
    file: international
    caption: "Oryginalny Game Boy Advance.<br>Wydany 21.03.2001 w Japonii, 11.06.2001 w Ameryce i 22.06.2001 w Europie."
  Motherboard:
    caption: "Pokazuję wersję '03'. Zauważ, że 'AGB' to identyfikator modelu Game Boy Advance.<br>Gniazdo kartridża i wzmacniacz audio znajdują się z tyłu."
    extension: "jpg"
    bib_source: photography-gekkio
  Diagram:
    caption: "Każda magistrala danych jest oznaczona jej szerokością.<br>Pokazany układ AGB Game Pak nie zawiera mapera (ponieważ nowy procesor jest w stanie obsłużyć znacznie więcej pamięci), chociaż gry z dużym ROM mogą nadal go używać."
#Historical
aliases:
  - /projects/consoles/game-boy-advance/
---

## Szybkie wprowadzenie

Wewnętrzna konstrukcja Game Boy Advance jest dość imponująca jak na przenośną konsolę, która działa na dwóch bateriach AA.

Ta konsola będzie nadal korzystać z *popisowego* procesora graficznego firmy Nintendo. Ponadto wprowadzi ona stosunkowo nowy CPU od brytyjskiej firmy, która w nadchodzących latach wzrośnie w popularności.

## {.supporting-imagery}

## CPU

Większość komponentów jest połączona w jeden pakiet o nazwie **CPU AGB**. Ten pakiet zawiera dwa zupełnie różne procesory:

- **Sharp SM83** działający z szybkością **8,4 lub 4,2 MHz**: *Jeśli to nie ten sam procesor, co w Game Boyu!* Jest skutecznie używany do uruchamiania gier Game Boy (**DMG**) i Game Boy Color (**CGB**). Oto [mój poprzedni artykuł](game-boy), jeśli chcesz dowiedzieć się więcej na tych konsol.
- **ARM7TDMI** działający z częstotliwością **16,78 MHz**: jest to nowy procesor, na którym się skupimy i który obsługuje gry Game Boy Advance.

Zauważ, że oba procesory **nigdy nie będą działać w tym samym czasie** ani nie będą wykonywać żadnego fantazyjnego współprzetwarzania. **Jedynym** powodem uwzględnienia *bardzo* starego Sharpa jest **wsteczna zgodność**.

Biorąc to pod uwagę, zanim opiszemy układ ARM, uważam, że warto zacząć od historii stojącej za tą marką procesorów.

### Cud z Cambridge

Historia początków procesora ARM i jego późniejszej sławy jest porywająca. Znajdujemy tu połączenie inwestycji publicznych, wykładniczego wzrostu, niefortunnych decyzji i partnerstw na odległość.

#### Powstanie Acorn Computers {.tabs.active}

![Zdjęcie BBC Micro z pudełkiem dyskietek 5¼ na górze [@photography-bbc_micro], pierwsza dyskietka to Elite.](bbc_micro.jpg) {.tab-float}

Koniec lat 70. w Wielkiej Brytanii naznaczony był początkiem przymusowego przejścia od gospodarki interwencjonistycznej do wolnego rynku. W czasie tej burzy firmy z siedzibą w Cambridge, takie jak **Acorn Computers** wraz z Sinclairem i tym podobnymi, sprzedawały zestawy komputerowe laboratoriom i hobbystom. Podobnie jak przedsiębiorstwa Amerykańskie i Japońskie, komputery Acorna opierały się na [procesorze 6502](nes#cpu) i zastrzeżonym dialekcie języka BASIC.

Na początku lat 80. interesy ministerialne w nowym rządzie brytyjskim doprowadziły do stworzenia projektu mającego na celu podniesienie poziomu umiejętności obsługi komputera w szkołach [@cpu-clp]. Dzięki nadchodzącemu komputerowi domowemu 'Proton' firmy Acorn, firma otrzymała kontrakt na budowę niedrogiego komputera, który spełni wizję rządu. W rezultacie powstał **BBC Micro** (nazywany 'Beeb'), który odniósł znaczący sukces wśród szkół, nauczycieli i uczniów. W Micro, firma Acorn zastosowała awangardowy **interfejs 'Tube'**, który mógł rozszerzyć komputer o **drugi procesor**. Otworzyłoby to drogę do kolejnej dużej inwestycji Acorn.

Podczas opracowywania kolejnego produktu, tym razem przeznaczonego dla przedsiębiorstw, firma Acorn nie znalazła odpowiedniego procesora, który mógłby zastąpić model 6502. Presja na innowacje w stosunku do konkurencji Japońskiej i Amerykańskiej, w połączeniu z niefortunnym planowaniem, postawiła firmę Acorn w trudnej sytuacji finansowej. Dlatego nowy oddział firmy Acorn otrzymał zadanie wyprodukowania atrakcyjnego procesora. Aby obejść niedawne ograniczenia firmy Acorn, zespół ds. CPU oparł swoją architekturę na naukach zawartych w artykule badawczym zatytułowanym **The Case for the Reduced Instruction Set Computer** [@cpu-patterson] i jego prototypie, **procesorze RISC** [@cpu-furber]. Wreszcie w 1985 roku firma Acorn dostarczyła **procesor ARM1** jako moduł Tube dla BBC Micro, ale był on sprzedawany wyłącznie do celów badawczo-rozwojowych. Dopiero w 1987 r., wraz z wprowadzeniem na rynek pierwszego komputera **Acorn Archimedes**, chipy ARM (wówczas procesor ARM2) odegrają kluczową rolę.

#### Nowe przedsięwzięcie procesorowe {.tab}

![Późny model Newtona... po tym jak się nim bawiłem.](newton.jpg) {.tab-float}

Podczas komercjalizacji Acorn Archimedes, firma Apple zafascynowała się energooszczędnymi procesorami Acorn, ale amerykańska firma nadal nie była przekonana, że najnowszy procesor ARM3 firmy Acorn będzie odpowiedni dla nowego projektu Apple, **Newtona**. Jednak zamiast odejść (w końcu Acorn był konkurentem), oboje omówili możliwość rozwinięcia ARM3 tak, aby spełniał wymagania Apple [@cpu-furber], a mianowicie **elastyczną częstotliwość taktowania**, **zintegrowane [MMU](nintendo-64#memory-management)** i **kompletne adresowanie 32-bitowe**.

Ta współpraca szybko przerodziła się w partnerstwo, w ramach którego Acorn, Apple i VLSI (producent chipów ARM) założyły nową firmę skupiającą się wyłącznie na opracowywaniu procesorów ARM. Inwestycję zapewnił Apple (pozyskując 43% udziałów), Acorn udostępnił kadrę, a produkcją zajęła się VLSI. W 1990 roku powstała firma **Advanced RISC Machines (ARM) Ltd**, której prezesem wykonawczym został Robin Saxby.

Wiele lat później Apple w końcu wypuścił **Newton MessagePad** zasilany przez **ARM610**, jeden z chipów ARM nowej generacji z uwzględnieniem wskazówek Apple. W międzyczasie firma Acorn wypuściła także **RiscPC** wykorzystujący nowe procesory.

Teraz, gdy Acorn i Apple pozostały na rynku komputerów/urządzeń przenośnych, ARM opracowało radykalny model biznesowy. Oddalając się od produkcji, wizja Saxby'ego obejmowała **licencjonowanie własności intelektualnej ARM** w postaci **projektów procesorów** i ich **zestawu instrukcji** [@cpu-saxby]. Dzięki temu ARM zyskał klientów spoza branży komputerowej, takich jak Texas Instruments [@cpu-arm_history], który później połączył firmę z wschodzącym rynkiem mobilnym (którego kulminacją była Nokia 6110) i dekoderami. W kolejnych latach technologia ARM zostanie wbudowana w miliardy urządzeń mobilnych [@cpu-arm_history].

### Partnerstwo z Nintendo {.tabs-close}

Wracając do Japonii, i dzięki [analizie Game Boya](game-boy) dowiedzieliśmy się, że strategia sprzętowa Nintendo dotycząca systemów przenośnych faworyzuje model [System na Czipie (SoC)](game-boy#cpu). Umożliwiło to firmie ukrycie niedrogiej, gotowej technologii i połączenie jej z wewnętrznymi rozwiązaniami. Dzięki temu nowa konsola może być wyjątkowa i konkurencyjna.

![CPU AGB, mieszczący procesor ARM7TDMI (wśród wielu innych komponentów).](cpu_agb.png)

Na szczęście model licencjonowania ARM idealnie wpasował się w te potrzeby. Ponadto dobra reputacja w sektorze mobilnym rozprzestrzeniła dobre słowo na temat ARM, zwłaszcza w okresie rozkwitu platformy mobilnej **ARM7TDMI**, procesora który otrzymał wsparcie od Nokii w celu maksymalizacji wydajność przy ograniczeniach mocy i pamięci. Cóż, los chciał, że Nintendo i ARM ostatecznie zostały partnerami, a na procesor Game Boy Advance został wybrany ARM7TDMI.

Przyjrzyjmy się teraz temu, co ten chip oferuje programistom.

#### Zestaw instrukcji {.tabs.active}

Po pierwsze, ARM7TDMI implementuje zestaw instrukcji **ARMv4**, następcę ARMv3. Oznacza to:

- **Projekt oparty na architekturze RISC**: Jak wyjaśniono wcześniej, na architekrurę procesorów ARM wpływ miała publikacja Uniwersytetu w Berkley zatytułowana 'The Case for the Reduced Instruction Set Computer' [@cpu-patterson]. Jej badania przedstawiają szereg wytycznych dotyczących projektowania skalowalnych procesorów i bronią stosowania [architektury załaduj-zapisz](xbox#tab-1-4-cisc-or-risc), stałego rozmiaru instrukcji i dużego pliku rejestru. Wiele z nich było nieobecnych na zaludnionym rynku procesorów (tj. [Intel 8086](xbox#cpu), [MOS 6502](nes#cpu), <a href= „master-system#cpu”>Zilog Z80</a> i [Motorola 68000](mega-drive-genesis#cpu)), ale wywarły wpływ na projektowanie nowych linii procesorów w latach 80-tych i lata 90-te.
- **Wykonywanie warunkowe**: Specyficzna cecha ARM ISA. Zasadniczo prawie każda instrukcja zawiera warunek decydujący o tym, czy należy ją wykonać. Zwykle inne procesory wykonują proces 'porównaj i przeskocz' (zwany także 'rozgałęzianiem'), gdy procesor osiągnie skrzyżowanie. Natomiast instrukcje ARM są uwarunkowane wynikiem poprzedniego porównania. Jest to możliwe, ponieważ pierwsze cztery bity rozkazów ARM są zarezerwowane dla warunku (tj. równego, nierównego, itp). Podsumowując, zmniejsza to złożoność kodu ARM, ponieważ wykonywanie warunkowe zapewnia czystszy projekt procedur, w przeciwieństwie do rozgałęzień i podziału podprogramów.
- Instrukcje mnożenia **32-bitowego** i **64-bitowego**: Dodano w ARM v4. Ponadto operacje 64-bitowe wyprowadzają wynik w dwóch rejestrach.

#### Rdzeń {.tab}

Teraz czas sprawdzić silikon. ARM7TDMI to okrojona wersja ARM710 z ciekawymi dodatkami. Rdzeń zawiera \[@cpu-arm\] \[@cpu-furber\]:

- **Szesnaście 32-bitowych rejestrów ogólnego przeznaczenia**: Chociaż jest to duży krok naprzód w porównaniu z siedmioma [8-bitowymi rejestrami SM83/Game Boya](game-boy#the-cpu-core). Jest to kompromis z wytycznymi RISC, które zamiast tego wymagały trzydziestu dwóch rejestrów 32-bitowych. Dzieje się tak, ponieważ ARM opowiadał się za utrzymaniem małego rozmiaru krzemu [@cpu-furber].
- **32-bitowa magistrala danych i ALU**: Może obsługiwać wartości 32-bitowe bez zużywania dodatkowych cykli.
- **Czyste adresowanie 32-bitowe**: To część wkładu firmy Apple. Pierwsze trzy procesory ARM wykorzystywały 26-bitowe adresy pamięci w celu optymalizacji wydajności (połączony Licznik Programów i Rejestr Stanu mogły zmieścić się w jednym 32-bitowym słowie) w zamian za możliwość adresowania pamięci (można było uzyskać dostęp do 64 MB pamięci). Kolejna seria, ARM6 (z ISA ARMv3), zaimplementowała 32-bitową logikę adresowania, ale zachowała tryb kompatybilności wstecznej dla starego kodu. Teraz ARM7TDMI (skoncentrowany na urządzeniach mobilnych) zrezygnował z trybu 26-bitowego i obsługuje tylko logikę dla adresów 32-bitowych (zmniejszając ilość potrzebnego krzemu).
- **Trzyetapowy potok**: Wykonywanie instrukcji jest podzielone na trzy *etapy*. Procesor pobierze, zdekoduje i wykona do trzech instrukcji jednocześnie. Umożliwia to maksymalne wykorzystanie zasobów procesora (co zmniejsza bezczynność krzemu), jednocześnie zwiększając liczbę instrukcji wykonywanych na jednostkę czasu.
  - Dzięki takiemu projektowi wykonywanie warunkowe jest bardziej wydajne niż rozgałęzianie.
- **Brak [Jednostki Zarządzania Pamięcią (MMU)](nintendo-64#memory-management)**: Od czasu ARM1 firma ARM dostarczała rozwiązanie MMU. Najpierw jako koprocesor 'MEMC', a następnie zintegrowany z ARM610. ARM7TDMI wydaje się być jedynym w swojej serii, który go nie zapewnia, potencjalnie ze względu na brak zainteresowania (wczesne urządzenia mobilne nie wymagały zaawansowanej pamięci wirtualnej).
- **Brak pamięci podręcznej**: Kolejna redukcja kosztów tego układu, ponieważ poprzednie układy ARM zawierały pamięć podręczną.

Wreszcie wszystko to może działać z zasilaczem **3 wolt** [@cpu-furber]. Jest to ogromny krok w kierunku komputerów mobilnych, gdyż wcześniejsze rdzenie wymagały zasilania 5 wolt.

#### Wyciskanie wydajności {.tab}

Jedna z wad architektury załaduj-zapisz doprowadziła do tego, że kod ARM był bardzo skromny. Konkurenci tacy jak x86 mogliby wykonywać te same zadania, używając mniejszych ilości kodu i wymagając mniej pamięci. W rezultacie, kiedy ARM zaczął sprzedawać firmom telekomunikacyjnym, Nokia nie była z tego zadowolona [@cpu-arm_history]. Rozmiar instrukcji ARM oznaczał, że sprzęt Nokii składający się z 16-bitowych magistrali oraz ograniczonej pamięci i pamięci masowej – a wszystko to w celu oszczędności kosztów i energii – spowodowałby, że procesor byłby nieefektywny i powodowałby wąskie gardło. Dlatego ARM wymyśliło rozwiązanie: **Zestaw instrukcji Thumb**.

Thumb jest podzbiorem zestawu instrukcji ARM, którego instrukcje są kodowane w **16-bitowych słowach** (a nie 32-bitowych) [@cpu-thomas]. Będąc 16-bitowymi, instrukcje Thumb wymagają **połowy szerokości magistrali** i zajmują **połowę pamięci**.

Głównym kompromisem jest to, że **Thumb nie oferuje wykonywania warunkowego**, zamiast tego polega na rozgałęzianiu. Dodatkowo jego kody operacyjne do przetwarzania danych używają tylko formatu dwuadresowego, a nie trzyadresowego i mają dostęp tylko do dolnej połowy pliku rejestru (a więc dostępne jest **tylko osiem rejestrów ogólnego przeznaczenia**). Ponieważ instrukcje Thumb oferują tylko funkcjonalny podzbiór ARM, może być konieczne napisanie większej liczby instrukcji, aby osiągnąć ten sam efekt.

W praktyce, Thumb wykorzystuje 70% miejsca w kodzie ARM. W przypadku pamięci 16-bitowej, Thumb działa *szybciej* niż ARM. W razie potrzeby instrukcje ARM i Thumb można mieszać w tym samym programie (nazywanym *interworking*), dzięki czemu programiści mogą wybrać, kiedy i gdzie użyć każdego trybu.

#### Rozszerzenia {.tab}

ARM7TDMI jest w istocie rdzeniem zgodnym z ARMv3 z dodatkami. Jest to wspomniane w nazwie (*TDMI*), co oznacza:

- **T** → **Thumb**: Dołączenie zestawu instrukcji Thumb.
- **D** → **Debug Extensions (Rozszerzenia Debugowania)**: Zapewniają debugowanie JTAG.
- **M** → **Enhanced Multiplier (Ulepszony Mnożnik)**: Poprzednie rdzenie ARM wymagały wielu cykli do obliczenia pełnych 32-bitowych mnożeń, to ulepszenie redukuje je do zaledwie kilku.
- **I** → **EmbeddedICE macrocell**: Włącza sprzętowe przerwania, punkty obserwacyjne i umożliwia zatrzymanie systemu na czas debugowania kodu. Ułatwia to tworzenie programów dla tego procesora.

Ogólnie rzecz biorąc, uczyniło to ARM7TDMI atrakcyjnym rozwiązaniem dla urządzeń mobilnych i wbudowanych.

### Lokalizacje pamięci {.tabs-close}

Dołączenie Thumb miało szczególnie duży wpływ na ostateczny projekt tej konsoli. Nintendo połączyło magistrale 16-bitowe i 32-bitowe między różnymi modułami, aby obniżyć koszty, jednocześnie zapewniając programistom niezbędne zasoby do optymalizacji kodu.

![Architektura pamięci tego systemu.](memory.png)

Pamięć Game Boy Advance do wykorzystania jest podzielona na następujące lokalizacje (w kolejności od najszybszej do najwolniejszej) [@cpu-vijn]:

- **IWRAM** (wewnętrzny WRAM) → 32-bitowy z 32 KB: Przydatny do przechowywania instrukcji ARM.
- **VRAM** (Wideo RAM) → 16-bitowy z 96 KB: Chociaż ten blok jest przeznaczony na dane graficzne (wyjaśniono to w następnej części tego artykułu), nadal można go znaleźć w mapie pamięci procesora, więc programiści mogą przechowywać tu inne dane, jeśli IWRAM nie wystarcza.
- **EWRAM** (zewnętrzny WRAM) → 16-bitowy z 256 KB: Osobny układ obok CPU AGB. Jest optymalny do przechowywania instrukcji Thumb i danych w małych porcjach. Z drugiej strony dostęp do chipa może być do sześciu razy wolniejszy w porównaniu z IWRAM.
- **Game PAK ROM** → 16-bitowy ze zmiennym rozmiarem: To jest miejsce, w którym uzyskuje się dostęp do pamięci ROM kartridża. Chociaż może zapewniać jedną z najwolniejszych szybkości, jest również odzwierciedlany w mapie pamięci, aby zarządzać różnymi szybkościami dostępu. Dodatkowo Nintendo dołączyło **Bufor Prefetch**, który łączy się z kartridżem, aby złagodzić nadmierne przeciąganie. Ten komponent niezależnie buforuje ciągłe adresy, gdy procesor nie uzyskuje dostępu do kartridża, może przechowywać do ośmiu 16-bitowych słów.
  - W praktyce jednak procesor rzadko pozwala Buforowi Prefetch wykonać swoją pracę. Ponieważ domyślnie będzie pobierać instrukcje z kartridża, aby kontynuować wykonywanie \[@cpu-pfau\] (dlatego IWRAM i EWRAM są tak ważne).
- **Game PAK RAM** → 8-bitowy o zmiennym rozmiarze: To jest miejsce, w którym uzyskuje się dostęp do pamięci RAM (SRAM lub Pamięci Flash).
  - Jest to ściśle 8-bitowa magistrala (procesor zobaczy 'śmieci' w nieużywanych bitach) i z tego powodu Nintendo twierdzi, że może być obsługiwana tylko przez ich biblioteki.

Chociaż ta konsola była sprzedawana jako system 32-bitowy, większość jej pamięci jest dostępna tylko przez 16-bitową magistralę, co oznacza, że gry będą w większości używać zestawu instrukcji Thumb, aby uniknąć wydawania dwóch cykli na pobieranie instrukcji. Tylko w bardzo wyjątkowych okolicznościach (tj. konieczności użycia instrukcji nie znajdujących się na Thumb podczas przechowywania ich w IWRAM) programiści skorzystają z zestawu instrukcji ARM.

### Stawanie się Game Boy Color

Oprócz włączenia [osprzętu GBC](game-boy) (Sharp SM83, oryginalny BIOS, tryby audio i wideo, kompatybilne gniazdo kartridży itd.), do zapewnienia kompatybilności wstecznej wymagane są dwie dodatkowe funkcje.

Od strony sprzętowej konsola opiera się na przełącznikach, aby wykryć, czy włożono kartridż Game Boy lub Game Boy Color. **Detektor kształtu** w gnieździe kartridża skutecznie identyfikuje typ kartridża i pozwala procesorowi odczytać jego stan. Zakłada się, że jakiś element CPU AGB odczytuje tę wartość i automatycznie wyłącza sprzęt, który nie jest potrzebny w trybie GBC.

Od strony oprogramowania istnieje specjalny 16-bitowy rejestr o nazwie `REG_DISPCNT`, który może zmieniać wiele właściwości wyświetlacza, ale jeden z jego bitów ustawia konsolę w 'tryb GBC' [@cpu-diaz]. Początkowo miałem trudności ze zrozumieniem, kiedy dokładnie GBA próbuje zaktualizować ten rejestr. Na szczęście niektórzy programiści pomogli to wyjaśnić:

> Myślę, że to, co dzieje się podczas rozruchu GBC, polega na tym, że sprawdza przełącznik (czytelny pod adresem REG_WAITCNT 0x4000024), zanika (bardzo szybkie zanikanie, trudne do zauważenia), a następnie w końcu przełącza się w tryb GBC (BIOS zapisuje do REG_DISPCNT 0x4000000), zatrzymując ARM7.
> 
> Jedynym brakującym elementem układanki jest to, co by się stało, gdyby usunięto część obudowy kartridża GBC, aby przełącznik nie był już wciskany, a następnie programowo przełączono tryb na tryb GBC. W tym przypadku może pomóc tryb Multi-boot. Nie jestem pewien, czy przełącznik musi być wciśnięty, aby magistrala kartridża GBC działała poprawnie, czy po prostu działa. Jestem skłonny założyć, że przełącznik jest niezbędny do działania magistrali, ale to tylko przypuszczenie.
> 
> -- <cite>Dan Weiss (znany jako Dwedit, obecny opiekun PocketNES i Goomba Color)</cite>

## Grafika

Zanim zaczniemy, znajdziesz w systemie mieszankę [SNES-a](super-nintendo.md#graphics) i [Game Boy'a](game-boy#graphics), a rdzeniem graficznym nadal jest dobrze znany silnik 2D o nazwie **PPU**. Zalecam przeczytanie tych artykułów przed kontynuowaniem, ponieważ powrócę do wielu wcześniej wyjaśnionych koncepcji.

W porównaniu z poprzednimi [Game Boy'ami](game-boy) mamy teraz ekran LCD, który może wyświetlać do **32 768 kolorów** (15 bitów). Ma rozdzielczość **240 x 160 pikseli** i częstotliwość odświeżania **~60Hz**.

### Organizowanie treści

![Architektura pamięci PPU.](ppu.png)

Grafika jest rozproszona w następujących obszarach pamięci:

- 96 KB 16-bitowego **VRAM-u** (RAM-u Wideo): Gdzie 64 KB przechowuje tła i 32 KB przechowuje sprite'y.
- 1 KB 32-bitowego **OAM-u** (Pamięci Atrybutów Obiektów): Przechowuje do 128 wpisów sprite (nie grafiki, tylko indeksy i atrybuty). Jego magistrala jest zoptymalizowana pod kątem szybkiego renderowania.
- 1 KB 16-bitowego **PAL RAM-u** (RAM-u Palety): Przechowuje dwie palety, jedną dla tła, a drugą dla sprite'ów. Każda paleta zawiera 256 wpisów 15-bitowych kolorów, przy czym kolor `0` jest *przezroczysty*.

### Konstruowanie klatki

Jeśli czytałeś poprzednie artykuły, GBA będzie Ci znajomy, chociaż istnieją dodatkowe funkcje, które mogą Cię zaskoczyć, i nie zapominaj, że ta konsola działa na dwóch bateriach AA.

Wypożyczę grafikę z *Sonic Advance 3* firmy Sega, aby pokazać, jak składa się klatka.

#### Kafelki {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Te dwa bloki są wykonane z Kafelków 4 bpp.](sonic/tiles1.png){.active title="Bloki 1-2"}

![Możesz zauważyć tutaj dziwne pionowe wzory, nie są to grafiki, ale 'Mapy Kafelków' (patrz następna sekcja).](sonic/tiles2.png){title="Bloki 3-4"}

![Te dwa bloki są zarezerwowane dla sprite'ów.](sonic/tilesobj.png){title="Bloki 5-6"}

Para Charblock'ów znajdująca się w VRAM.

:::

Kafelki GBA są ściśle **bitmapami 8x8 pikseli**, mogą używać 16 kolorów (4 bpp) lub 256 kolorów (8 bpp). Kafelki 4 bpp zajmują 32 bajty, a 8 bpp zajmują 64 bajty.

Kafelki mogą być przechowywane w dowolnym miejscu w pamięci VRAM, jednak PPU chce je pogrupować w **charblock'i**: Regiony **16 KB**. Każdy blok jest zarezerwowany dla określonego typu warstwy (tła i sprite'ów), a programiści decydują, gdzie zaczyna się każdy charblock. Może to spowodować pewne nakładanie się, co w konsekwencji umożliwia dwóm charblock'om współdzielenie tych samych kafelków.

Ze względu na rozmiar charblock'a, w jednym bloku można przechowywać do 256 płytek 8 bpp lub 512 płytek 4 bpp. Dozwolonych jest do sześciu charblock'ów, które łącznie wymagają 96 KB pamięci: dokładnej ilości pamięci VRAM, jaką ma ta konsola.

Tylko cztery charblock'i mogą być użyte jako tła, a dwa mogą być użyte jako sprite'y.

#### Tła {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Warstwa Tła 0 (BG0).](sonic/bg0.png){.active title="Warstwa 0"}

![Warstwa Tła 2 (BG2).](sonic/bg2.png){title="Warstwa 2"}

![Warstwa Tła 3 (BG3).<br>Ta konkretna warstwa będzie przesuwana poziomo na pewnych liniach skanowania, aby zasymulować efekty wody.](sonic/bg3.png){title="Warstwa 3"}

Statyczne warstwy tła w użyciu.

:::

Warstwa tła tego systemu znacznie się poprawiła od czasu Game Boy Color. W końcu zawiera niektóre funkcje znalezione w [Super Nintendo](super-nintendo) (pamiętasz [przekształcenia afiniczne](super-nintendo#that-feature)?).

PPU może narysować do czterech warstw tła. Możliwości każdego z nich będą zależeć od wybranego trybu działania [@graphics-tiles]:

- **Tryb 0**: Zapewnia cztery warstwy statyczne.
- **Tryb 1**: Dostępne są tylko trzy warstwy, chociaż jedna z nich jest **afiniczna** (można ją obracać i/lub skalować).
- **Tryb 2**: Dostarcza dwie warstwy afiniczne.

Każda warstwa ma wymiar do 512x512 pikseli. Jeśli jest afiniczna, to będzie miała rozmiar do 1024x1024 pikseli.

Fragment danych definiujący warstwę tła to **Mapa Kafelków**. Aby wdrożyć ją w sposób zrozumiały dla PPU, programiści używają **screenblock'ów**, konstrukcji definiującej części warstwy tła (32x32 kafelki). Screenblock zajmuje tylko 2 KB, ale do zbudowania całej warstwy będzie potrzebny więcej niż jeden. Programiści mogą umieścić je gdziekolwiek wewnątrz charblocków tła, co oznacza, że nie wszystkie wpisy kafelków będą zawierały grafikę!

#### Sprite'y {.tab}

![Renderowana warstwa Sprite](sonic/sprites.png) {.tab-float.pixel}

Rozmiar sprite może mieć szerokość do 64x64 pikseli, jak na tak mały ekran będą okupywać jego dużą część.

Jeśli to nie było wystarczające, PPU może teraz zastosować **transformacje afiniczne** do sprite'ów!

Wpisy sprtie mają szerokość 32 bitów, a ich wartości można podzielić na dwie grupy:

- **Atrybuty**: Zawiera pozycję x/y, przesunięcie h/v, rozmiar, kształt (kwadratowy lub prostokątny), typ sprite'u (afiniczny lub regularny) i położenie pierwszego kafelka.
- **Dane afiniczne**: Używany tylko wtedy, gdy sprite jest afiniczny, określają skalowanie i obrót.

#### Rezultat {.tab}

![Wszystkie warstwy scalone (_Tada!_).](sonic/result.png) {.tab-float.pixel}

Jak zawsze, PPU automatycznie połączy wszystkie warstwy, ale to jeszcze nie koniec! System może również zastosować kilka efektów na tych warstwach:

- **Mozaika**: sprawia, że kafelki wyglądają bardziej *kwadratowo*.
- **Mieszanie alfa**: Łączy kolory nakładających się warstw, aby uzyskać efekty przezroczystości.
- **Okienkowanie**: Dzieli ekran na dwa różne *okna*, gdzie każde może mieć własne oddzielne grafiki i efekty, zewnętrzna strefa obu okien może być również renderowana za pomocą kafelków.

Z drugiej strony, aby zaktualizować klatkę, dostępne jest wiele opcji:

- Wydaj polecenie **CPU**: Procesor ma teraz pełny dostęp do pamięci VRAM, kiedy tylko chce. Może jednak powodować niechciane artefakty, jeżeli zmieni niektóre dane w połowie klatki, więc oczekiwanie na VBlank/HBlank (*tradycyjny sposób*) pozostaje w większości przypadków najbezpieczniejszą opcją.
- Użyj **Kontrolera DMA**: DMA zapewnia ~10-cio krotnie szybszy transfer i może być zaplanowany podczas VBlank i HBlank. Ta konsola zapewnia 4 kanały DMA (dwa przeznaczone na dźwięk, jeden do operacji krytycznych, a ostatni do celów ogólnych). Miej na uwadzę, że kontroler zatrzyma procesor podczas operacji (chociaż praktycznie tego nie zauważy!).

### Poza Kafelkami {.tabs-close}

Czasami możemy chcieć utworzyć tło, z którego tile engine nie będzie w stanie narysować wszystkich wymaganych grafiki. Nowoczesne konsole rozwiązały ten problem, implementując architekturę **bufora-klatek**, ale nie jest to możliwe, gdy jest bardzo mało RAM... Cóż, tak się składa, że GBA ma 96 KB pamięci VRAM, co wystarcza do przydzielenia **bitmapy** o wymiarach naszego ekranu LCD.

Dobrą wiadomością jest to, że PPU faktycznie implementuje tę funkcjonalność, uwzględniając trzy dodatkowe tryby, zwane **trybami bitmapy** [@graphics-bitmap]:

- **Tryb 3**: Przydziela pojedynczą w pełni kolorową klatkę (16 bpp, 32,768 kolorów).
- **Tryb 4**: Zapewnia dwie klatki z połową kolorów (8 bpp, 256 kolorów) każda.
- **Tryb 5**: Dwie w pełni kolorowe klatki, każda o połowę mniejsza (160x128 pikseli).

Powodem posiadania dwóch bitmap jest włączenie **przewracania-stron**: Rysowanie na wyświetlanej bitmapie może ujawnić pewne dziwne artefakty podczas procesu. Jeśli zamiast tego manipulujemy inną, żaden z błędów nie zostanie pokazany użytkownikowi. Po zakończeniu drugiej bitmapy, PPU można zaktualizować, aby wskazywał na drugą, skutecznie zamieniając wyświetlaną klatkę.

::: {.subfigures .tabs-nested .open-float .tab-float .pixel}

![Super Monkey Ball Jr. (2002).<br>Tryb bitmapowy umożliwił procesorowi dostarczenie podstawowej grafiki 3D do scenerii.<br>Obiekty pierwszego planu to sprite'y (oddzielna warstwa).](bitmap/monkey.png){.active title="3D"}

![Demo Tonc'a.<br>Renderowana bitmapa z kilkoma prymitywami.<br>Zauważ, że ekran nie pokazuje znaczących wzorów generowanych przez silniki kafelków.](bitmap/demo.png){title="Demo"}

![SpongeBob Kanciastoporty Nickelodeon'a.<br>Odcinek rozpowszechniany jako kartridż Gba Video (oczywiście był mocno skompresowany).](bitmap/spongebob.png){title="Wideo"}

Przykłady programów wykorzystujących tryby bitmapowe.

:::

Ogólnie rzecz biorąc, brzmi to jak najnowocześniejsza funkcja, jednak większość gier trzymała się silnika kafelków. Czemu? Ponieważ w praktyce **kosztuje dużo zasobów procesora**.

Widzisz, podczas korzystania z silnika kafelków procesor może delegować większość obliczeń do układu graficznego. W przeciwieństwie do tego, system buforowania klatki, który zapewnia PPU, ogranicza się tylko do wyświetlania tego segmentu pamięci jako **pojedynczej warstwy tła**, co oznacza brak indywidualnych transformacji afinicznych, warstw lub efektów, chyba że procesor je obliczy. Ponadto, bufor-klatki zajmuje 80 KB pamięci, więc tylko 16 KB (połowa) jest dostępnych do przechowywania kafelków sprite'ów.

{.close-float}

Z tego powodu te tryby są używane oszczędnie, na przykład do odtwarzania wideo (**Game Boy Advance Video** całkowicie na tym polegał) lub renderowania **Geometrii 3D** za pomocą procesora. W każdym razie wyniki były co najmniej imponujące.

## Dźwięk

GBA posiada **2-kanałowy odtwarzacz próbek**, który działa w połączeniu ze starszym systemem dźwięku Game Boy'a.

### Funkcjonalność

Oto podział na komponenty audio przy użyciu *Sonic Advance 2* jako przykładu:

#### PCM {.tabs.active}

![Kanały wyłącznie PCM.](pcm){.tab-float video="true"}

Nowy system dźwiękowy może teraz odtwarzać próbki PCM, udostępnia dwa kanały o nazwie **Direct Sound**, w których odbiera próbki za pomocą **kolejki FIFO** (zaimplementowany jako 16-bajtowy bufor).

Próbki są **8-bitowe** i **znakowane** (zakodowane w wartościach od -128 do 127). Domyślna częstotliwość próbkowania to 32 kHz, chociaż zależy ona od każdej gry: ponieważ wyższa częstotliwość oznacza większy rozmiar i więcej cykli procesora, nie każda gra będzie zużywać tę samą ilość zasobów na zasilanie układu audio.

**DMA** jest niezbędne, aby uniknąć zapychania cykli procesora. Dostępne są również **liczniki czasu**, które umożliwiają synchronizację z kolejką.

#### PSG {.tab}

![Kanały wyłącznie PSG.](psg){.tab-float video="true"}

Chociaż podsystem Game Boy nie współdzieli swojego procesora, daje dostęp do PSG. Ze względu na kompatybilność jest to ten sam projekt, który można znaleźć w oryginalnym Game Boy'u. Wcześniej napisałem [ten artykuł](game-boy#audio), który szczegółowo omawia każdy kanał.

Większość gier GBA używała go do akompaniamentu lub efektów. Późniejsze tytuły zoptymalizują swoją muzykę pod kątem PCM, a PSG nie będzie używane.

#### Połączone {.tab}

![Tada!](complete){.tab-float video="true"}

Wreszcie wszystko jest automatycznie miksowane i przesyłane przez gniazdo głośnika/słuchawek.

Mimo że GBA ma tylko dwa kanały PCM, niektóre gry mogą magicznie odtwarzać więcej niż dwie równoczesne próbki. Jak to możliwe? Cóż, chociaż posiadanie tylko dwóch kanałów może wydawać się nieco słabe na papierze, główny procesor może wykorzystać niektóre ze swoich cykli do zapewnienia zarówno sekwencjonowania dźwięku, jak i miksowania \[@audio-programming\] (co powinno dać wyobrażenie o tym, jak potężny jest ARM7!). Ponadto w sekcji 'System Operacyjny' dowiesz się, że BIOS ROM zawiera sekwencer audio!

### Podwójna korzyść {.tabs-close}

Niektóre gry szły dalej w dualizmie PCM-PSG i 'zamieniały' wiodący układ w zależności od kontekstu.

W tej grze (*Mother 3*) gracz może wejść do dwóch różnych pokoi, jednego *stosunkowo normalnego* i drugiego z atmosferą *nostalgiczną*. W zależności od pokoju, w którym znajduje się postać, ten sam utwór będzie brzmieć *nowocześnie* lub *8bit-owo*.

![W normalnym pomieszczeniu używa się wyłącznie PCM.](crackers/cinema){.toleft video="true"}

![Pokój nostalgiczny, muzykę prowadzi PSG.](crackers/8bit){.toright video="true"}

## System Operacyjny

Wektor resetowania ARM7 to `0x00000000`, co wskazuje na **16 KB BIOS ROM**. Oznacza to, że Game Boy Advance najpierw uruchamia się z BIOS-u, który z kolei pokazuje ikoniczny ekran powitalny, a następnie decyduje, czy załadować grę, czy nie.

Ten ROM przechowuje również procedury oprogramowania, które gry mogą wywoływać w celu uproszczenia pewnych operacji i zmniejszenia rozmiaru kartridża [@operating_system-vijn]. Obejmują one:

- **Funkcje arytmetyczne**: Procedury wykonywania Dzielenia, Pierwiastka Kwadratowego i Tangensa Łuku.
- **Obliczanie macierzy afinicznej**: Mając wartość i kąt 'powiększenia', oblicza macierz afiniczną, która zostanie wprowadzona do PPU w celu skalowania/obracania tła lub sprite.
  - Istnieją dwie funkcje, jedna dla sprite, a druga dla tła. Ich parametry są nieco inne, ale idea jest taka sama.
- **Funkcje dekompresyjne**: Implementuje algorytmy dekompresji, w tym Run-Length, LZ77 i Huffman. Zapewnia również rozpakowywanie bitów i różnicę sekwencyjną.
- **Kopia pamięci**: Dwie funkcje poruszające pamięć. Pierwsza z nich kopiuje bloki 32-bajtowe przy użyciu specjalnego kodu operacji dla tego typu transferu (`LDMIA` do załadowania i `SDMIA` do przechowywania) tylko raz. Druga kopiuje 2-bajtowe lub 4-bajtowe bloki przy użyciu powtarzających się, odpowiednio, opkodów `LDRH/STRH` lub `LDMIA/STMIA`. Tak więc druga funkcja jest bardziej elastyczna, ale nie tak szybka.
- **Dźwięk**: Implementuje kompletny sekwencer MIDI! Zawiera wiele funkcji do sterowania nim.
- **Interfejs zasilania**: Skróty do resetowania, czyszczenia większości pamięci RAM, zatrzymywania procesora do momentu pojawienia się określonego zdarzenia (V-blank lub niestandardowego) lub przełączenia w 'tryb niskiego poboru mocy'.
- **Multi-boot**: Przesyła program do innego GBA i uruchamia go. Więcej szczegółów znajdziesz w sekcji 'Gry'.

BIOS jest podłączony przez 32-bitową magistralę i jest zaimplementowany za pomocą kombinacji instrukcji Arm i Thumb, chociaż te ostatnie są najbardziej widoczne.

Pamiętaj też, że wszystko to będzie działać tylko na ARM7. Innymi słowy, nie ma dostępnej akceleracji sprzętowej, która przyspieszałaby te operacje. Dlatego Nintendo udostępniło całą tę funkcjonalność za pomocą oprogramowania.

## Gry

Programowanie dla GBA miało wspólne metodologie z [Super Nintendo](super-nintendo), ale odziedziczyło także wszystkie osiągnięcia z początku XXI wieku: Standaryzowane języki wysokiego poziomu, niezawodne kompilatory, debugowalne procesory RISC, niezastrzeżone stacje robocze do programowania, porównywalnie lepsza dokumentacja i... Dostęp do Internetu!

Programy GBA są w większości napisane w **C** z sekcjami krytycznymi dla wydajności w **asemblerze** (ARM i Thumb), aby zaoszczędzić cykle. Nintendo dostarczyło SDK z bibliotekami i kompilatorami.

Gry są dystrybuowane w nowym, zastrzeżonym formacie kartridża, który nadal nazywa się **Game Pak**, ale ma mniejszą konstrukcję.

### Dostęp do danych kartridża

Chociaż ARM7 ma 32-bitową magistralę adresową, do modułu podłączone są tylko 24 linie adresowe. Teoretycznie oznacza to, że na kartridżu można uzyskać dostęp do 16 MB bez konieczności stosowania mappera. Jednak mapa pamięci pokazuje, że dostępne jest **32 MB danych kartridża**. Więc co się tutaj dzieje? Prawda jest taka, że Gamepak używa **adresów 25-bitowych** (co wyjaśnia blok 32 MB), ale jego najniższy bit jest ustawiony na zero. Zatem ustawione są tylko 24 pozostałe bity. Tak działa adresowanie Game Pak.

Czy to oznacza, że dane znajdujące się pod nieparzystymi adresami (z najmniej znaczącym bitem na '1') będą niedostępne? Nie, ponieważ magistrala danych jest 16-bitowa: Dla każdego transferu, CPU/DMA pobierze zlokalizowany bajt plus następny, umożliwiając odczytanie zarówno adresów parzystych, jak i nieparzystych. Jak widać, jest to po prostu kolejna praca inżynierska, która w pełni wykorzystuje możliwości sprzętu przy jednoczesnym obniżeniu kosztów.

### Przestrzeń RAM kartridża

Aby przechowywać zapisy, Game Pak'i mogą zawierać [@games-ziegler]:

- **SRAM**: Wymagają one baterii do przechowywania zawartości i mogą mieć rozmiar do 64 KB (chociaż gry komercyjne nie przekraczały 32 KB). Dostęp do niego uzyskuje się poprzez mapę pamięci GBA.
- **Flash ROM**: Podobny do SRAM, ale bez baterii, może mieć rozmiar do 128 KB.
- **EEPROM**: Wymagają one połączenia szeregowego i teoretycznie mogą mieć dowolny rozmiar (często do 8 KB).

### Akcesoria

To samo [gniazdo Game Boy Link](game-boy#external-communications) zapewniało możliwość gry wieloosobowej. Chociaż z jakiegoś powodu nie ma już czujnika podczerwieni (być może zbyt zawodnego w przypadku dużych transferów).

Dodatkowo BIOS GBA zaimplementował specjalną funkcję wewnętrznie znaną jako **Multi-boot**: Inna konsola (GBA lub GameCube) może wysłać działającą grę do pamięci EWRAM odbiornika, a następnie ten ostatni uruchamiałby się stamtąd (zamiast pobierać z pakietu Game Pak).

## Przeciwdziałanie-Piractwu & Homebrew

Ogólnie rzecz biorąc, używanie zastrzeżonych kartridży stanowiło dużą barierę w porównaniu z ciągłą grą w kotka i myszkę, z którą musieli walczyć inni producenci konsol podczas korzystania z CD-ROM-u.

Aby walczyć z *bootlegami* (nieautoryzowanymi reprodukcjami), BIOS GBA wbudował [ten sam proces sprawdzania podczasu uruchamiania](game-boy#anti-piracy), który można znaleźć w oryginalnym Game Boy'u.

### Flashcarty

Gdy pamięć masowa stała się bardziej przystępna cenowo, na rynku pojawił się nowy typ kartridża. **Flashcarty** wyglądały jak zwykłe Game Pak'i, ale miały pamięć wielokrotnego zapisu lub gniazdo kart pamięci. Umożliwiło to użytkownikom odtwarzanie plików ROM gier na konsoli. Koncepcja ta nie jest w rzeczywistości nowa, deweloperzy stosowali wewnętrznie podobne narzędzia do testowania swoich gier na prawdziwej konsoli (i producenci dostarczyli sprzęt, aby to umożliwić).

Wcześniejsze rozwiązania obejmowały wypalaną pamięć NOR Flash (do 32 MB) i zasilany bateryjnie SRAM. Aby załadować pliki binarne do kartridża, był on dostarczany z kablem Link-to-USB, który był używany z GBA i komputerem PC z systemem Windows XP. Korzystając z autorskiego oprogramowania i sterowników do flashowania chipów, komputer wgrywał do GBA program multi-boot, który z kolei posłużył do przesłania pliku binarnego gry z komputera PC do Flashcarta (włożonego do GBA). Ogólnie rzecz biorąc, całe zadanie przesyłania gry zostało uznane za *zbyt powolne*. Późniejsze Flashcarty (takie jak 'EZ-Flash') oferowały większą pamięć i możliwość programowania bez konieczności używania GBA jako pośrednika [@anti_piracy-ezflash]. Te ostatnie opierały się na pamięci wymiennej (SD, MiniSD, MicroSD lub cokolwiek innego).

Dostępność komercyjna tych kartridży okazała się **prawną szarą strefą**: Nintendo potępiło ich używanie ze względu na umożliwienie piractwa, podczas gdy niektórzy użytkownicy bronili, że jest to jedyna metoda uruchamiania **Homebrew** (programów stworzonych poza studiami gier, a co za tym idzie bez zgody Nintendo). Argument Nintendo był poparty faktem, że flashery, takie jak EZ-Writer, pomagały użytkownikom w łataniu ROM-ów z grami, aby mogły bez problemów działać w kartridża EZ-Flash. Po bataliach prawnych Nintendo kartridże te zostały zakazane w niektórych krajach (np. Wielkiej Brytanii). Niemniej jednak przetrwały na całym świecie.

## To wszystko ludziska

![Mój GBA i kilka gier.<br>Szkoda, że nie ma podświetlenia!](mygba.png)
