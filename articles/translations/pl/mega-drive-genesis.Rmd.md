---
short_title: Architektura Mega Drive / Genesis
title: Architektura Mega Drive / Genesis
name: Mega Drive / Genesis
subtitle: Nowe techniki kompozycji
date: 2019-05-18
release_date: 1988-10-29
generation: 4
cover: megadrive
top_tabs:
  Models:
    - 
      title: "Japoński"
      caption: "Mega Drive\n<br>Wydany 29.10.1988 w Japonii."
      file: japanese
    - 
      title: "Amerykański"
      caption: "Genesis\n<br>Wydany 14.08.1989 w Ameryce."
      file: american
    - 
      title: "Europejski"
      caption: "Mega Drive\n<br>Wydany 09.1990 w Europie."
      file: european
      active: true
  Motherboard:
    caption: "Pokazuje Japońską wersję 'VA0'.<br>Zwróć uwagę na niezwykłą płytę-córkę na górze VDP używaną do naprawy usterek poprodukcyjnych (poprawiona we właściwy sposób w późniejszych wersjach)."
    extension: "jpg"
    bib_source: photography-okqubit
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/mega-drive-genesis/
---

## Szybkie wprowadzenie

Sega (i ich reklamy telewizyjne) chcą, abyś wiedział: programiści nie mogą wymyślać przyzwoitych gier, chyba że konsola zapewnia szybszą grafikę i bogatsze dźwięki.

Ich nowy system zawiera wiele *już znanych* komponentów gotowych do zaprogramowania. Oznacza to, że teoretycznie programiści musieliby jedynie poznać nowe GPU Segi… prawda?

## {.supporting-imagery}

## CPU

Ta konsola ma dwa procesory ogólnego przeznaczenia.

### Przodownik

Po pierwsze, mamy **Motorolę 68000** działającą z częstotliwością **~7,6 MHz**, popularny procesor, który byl już obecny w wielu komputerach z tamtych czasów, takich jak Amiga, oryginalny Macintosh, Atari ST... Co ciekawe, każdy z tych komputerów zastąpił swojego 'poprzednika 6502' i podczas gdy [Master System](master-system) (prekursor Mega Drive) nie zawiera procesora 6502, robił to [NES](nes) (i w pewnym sensie celem Segi było przekonanie konsumentów Nintendo). Podsumowując, widać pewną korelację między ewolucją komputerów a technologią konsoli do gier.

![Chip Motorola 68000 w Mega Drive, ten został wtórnie wytworzony przez Hitachi.](m68000.jpg)

Wracając do tematu, 68k pełni rolę 'głównego' procesora i będzie przetwarzał logikę gry, obsługiwał WE/WY i obliczenia graficzne. Posiada następujące możliwości [@cpu-user]:

- **ISA 68000**: Nowy zestaw instrukcji z wieloma funkcjami, w tym zestawem kodów operacji mnożenia i dzielenia. Niektóre instrukcje mają długość 8 bitów (nazywane 'bajtem'), inne mają długość 16-bitową (nazywane 'słowem'), a pozostałe są 32-bitowe (nazywane 'długim-słowem').
- **Osiem 32-bitowych rejestrów ogólnego przeznaczenia**: To duży krok, biorąc pod uwagę, że 6502 i Z80 mają rejestry tylko 8-bitowe.
- **ALU 16-bitowe**: Oznacza to, że potrzebuje dodatkowych cykli do obliczenia operacji arytmetycznych na liczbach 32-bitowych, ale radzi sobie z liczbami 16-bitowymi/8-bitowymi.
- Zewnętrzna **magistrala danych 16-bitowa**: Jak widać, chociaż ten procesor ma pewne 'możliwości 32-bitowe', nie został zaprojektowany jako kompletna maszyna 32-bitowa. Szerokość tej magistrali oznacza lepszą wydajność podczas przenoszenia danych 16-bitowych.
  - Co ciekawe, Motorola zadebiutowała z kompletnym 32-bitowym procesorem, **68020**, cztery lata przed premierą tej konsoli. Ale wyobrażam sobie, że koszty wzrosłyby, gdyby Sega wybrała ten drugi układ.
- **Magistrala adresowa 24-bitowa**: Oznacza to, że można uzyskać dostęp do **maksymalnie 16 MB pamięci**. Adresy pamięci są nadal kodowane 32-bitowymi wartościami wewnątrz procesora (górny bajt jest po prostu odrzucany). To powiedziawszy, magistrala jest fizycznie podłączona do [@cpu-memorymap]:
  - 64 KB RAM ogólnego przeznaczenia.
  - ROM na kartridżu (do 4 MB).
  - Dwóch kontrolerów.
  - Rejestrów, portów i DMA procesora VDP.
  - Rejestrów płyty głównej (identyfikują konsolę).
  - Portów rozszerzeń (używanych do 'przyszłych' akcesoriów).
  - RAM drugiego procesora, pośredniczony przez *arbitra magistrali*.

Jeśli zastanawiasz się, dlaczego używa się 24-bitowych adresów w procesorze, który może obsłużyć 32-bitowe słowa, jest to ponieważ niewielka ilość sprzętu z tamtej epoki wymagała zarządzania 4 GB pamięci. Biorąc pod uwagę fakt, że wdrożenie niewykorzystanych linii jest kosztowne pod względem wydajności i pieniędzy, Motorola osiągnęła rozsądny kompromis (24 linie adresowe), który przygotowałby również programistów na pojawienie się w pełni 32-bitowego procesora (68020).

#### Specjalny zestaw instrukcji

Przed [rewolucją RISC](playstation#tab-1-1-a-bit-of-history) w latach 80. istniał ruch mający na celu skonsolidowanie sposobu projektowania zestawów instrukcji. Zasadniczo procesory konsumenckie z lat 70. (takie jak 6502 czy 8080) dostarczają instrukcje, które już wcześniej zdefiniowały sposób dostępu do pamięci (nazywa się to 'trybem adresowania'). W modelu 68000 Motorola oddzieliła funkcję instrukcji (kod operacji) od trybu adresowania, czyniąc ten ostatni jedynie kolejnym parametrem (operandem). Robiąc to, programiści mogli teraz używać tych samych kodów operacji z najbardziej optymalnym trybem adresowania (w oparciu o ich potrzeby).

Zasada ta nazywana jest **ortogonalnością zestawu instrukcji** i miała ogromny wpływ na nową generację procesorów pod koniec lat 70., ale szybko została rozproszona, gdy władzę przejęły [projekty RISC](game-boy-advance#tab-1-1-the-rise-of-acorn-computers), skutecznie przenosząc ciężar na procesor kompilatory. W każdym razie seria Motorola 68k cieszyła się dużą popularnością w latach 80. i dopiero na początku lat 90. firmy zaczęły przestawiać się na innego dostawcę.

### Towarzysz

W tej konsoli jest zainstalowany inny procesor, **Zilog Z80** działający z częstotliwością **~3,5 MHz**. Jest to ten sam procesor wcześniej analizowany w artykule o [Master System](master-system#cpu).

Z80 jest głównie używany do **sterowania dźwiękiem**. A zatem jego **16-bitowa magistrala adresu** składa się z następujących [@cpu-z80map]:

- 8 KB RAM.
- Dwa chipy dźwiękowe.
- RAM 68000 (ponownie, obsługiwany przez arbitra magistrali).

Na koniec należy zauważyć, że **oba procesory działają równolegle**.

### Dostępna pamięć

Główny procesor zawiera **64 KB** dedykowanego RAM do przechowywania danych ogólnego przeznaczenia, a Z80 zawiera **8 KB** RAM do operacji związanych z dźwiękiem.

#### Interkomunikacja

Sega wybrała dwa niezależne procesory, które **nie znają się nawzajem**, więc jak gry mogą zarządzać obydwoma jednocześnie? Cóż, główny program jest wykonywany w 68000, a ten procesor może następnie zapisywać w RAM Z80. Tak więc 68000 może wysłać program do pamięci RAM Z80 i poinstruować Z80, aby go załadował (przez wysłanie sygnału resetowania do Z80) [@cpu-using_z80]. Gdy Z80 jest już pod kontrolą, może być używany do zarządzania podsystemem dźwiękowym i przenoszenia pamięci za pomocą opisanej wcześniej metody, a wszystko to podczas gdy 68000 zajmuje się innymi operacjami.

![Architektura pamięci Mega Drive/Genesis.](memory.png) {.no-borders}

Ponieważ jeden procesor będzie musiał wkroczyć w magistralę procesora drugiego i oba nie mogą używać jej w tym samym czasie, istnieje dodatkowy składnik o nazwie **arbiter magistrali**, który należy aktywować, aby zatrzymać dowolny procesor, dzięki czemu pamięć może być zapisywana bez zagrożeń.

Ważne jest, aby podkreślić, że ten projekt może działać słabiej, jeśli nie jest odpowiednio zarządzany, więc gry będą musiały szczególnie uważać na arbitra magistrali i uważać, aby nie zatrzymywać procesora dłużej niż to konieczne.

## Grafika

Odpowiedź brzmi: _Blast Processing!_, co jeszcze musisz wiedzieć?

Okej, jeśli chcesz poznać _prawdziwą_ odpowiedź: dane graficzne są przetwarzane przez 68000 i renderowane na zastrzeżonym chipie o nazwie **Video Display Processor** (w skrócie 'VDP'), który następnie wysyła wynikową klatkę (w postaci linii skanowania) do wyświetlenia.

VDP działa z częstotliwością **~13 MHz** i obsługuje wiele trybów rozdzielczości w zależności od regionu: do **320x224 pikseli** w NTSC i maksymalnie **320x240 pikseli** w PAL.

### Powód wielu rozdzielczości wyświetlania

Technicznie rzecz biorąc, VDP może zmieścić 40 lub 32 kolumny [kafelków](master-system#tiles) na linię skanowania, a liczba kafelków na wiersz zależy od regionu (28 w NTSC lub 30 w PAL) [@graphics-keil]. Większość gier PAL nie przejmuje się dodatkowymi kafelkami dozwolonymi w systemach PAL (ponieważ prawdopodobnie muszą zachować spójność między dwoma regionami, a NTSC jest wspólnym mianownikiem), więc instruują VDP, aby renderował z 28 rzędami (tak jak zrobiłoby w systemach NTSC). W ten sposób VDP nie ma innego wyjścia, jak wypełnić nieużywany obszar kolorem tła (używanym również podczas overscan).

Możesz zobaczyć, które gry PAL są renderowane w trybie NTSC, sprawdzając `Mode Set Register #2` w emulatorze z możliwościami debugowania (tj. Exodus). Jeśli czwarty bit od prawej to `0`, to VDP działa w trybie NTSC [@graphics-resolution].

![Aby zapewnić szybki tryb dla wielu graczy w Sonic 2 (1992), gra aktywuje 'tryb z przeplotem', aby zamiast tego renderować poziom dla jednego gracza przy użyciu kafelków 8x16 pikseli (wraz z innymi zmianami).](twopsonic/sonic2.png) {.side-by-side.toleft.pixel}

![Z kolei bardziej wyrafinowany tryb wieloosobowy Sonic 3 (1994) opiera się na dedykowanych kafelkach 8x8 pikseli, które są oddzielone od poziomów dla jednego gracza.](twopsonic/sonic3.png) {.toright.pixel}

Co więcej, istnieje dodatkowy parametr, który można ustawić na VDP, aby ułożyć dwa kafelki, tworząc **mapy 8x16**, a następnie traktować je jako jeden kafelek. Podwaja to rozdzielczość pionową. Jednak zmniejsza to o połowę częstotliwość odświeżania, ponieważ klatki są teraz renderowane z przeplotem (jedna klatka renderuje parzyste linie skanowania, następna nieparzyste itd.), więc jest to bardziej ograniczone pod względem funkcjonalności. Tryb wieloosobowy w Sonic 2 jest dobrą reprezentacją tego trybu [@graphics-sonicmultip].

Na koniec warto zauważyć, że VDP automatycznie zajmuje się dodawaniem dopełnienia dla obszaru overscan, więc gry nie muszą się martwić o to, w które obszary można bezpiecznie rysować grafikę (jak to miało miejsce w przypadku ['stref niebezpiecznych' NES](nes#constructing-the-frame))

### Organizowanie treści

Pod względem przetwarzania danych graficznych ten układ ma dwa tryby działania:

- **Tryb IV**: Starszy tryb, który zachowuje się jak jego [poprzednik](master-system#graphics).
  - Nie oznacza to, że ta konsola będzie od razu odtwarzać gry z Master System, ponieważ wymagane jest dodatkowe akcesorium (*Power Base Converter*), aby dopasować poprzednie kartridże do tej konsoli. Konwerter poinstruuje również układ WE/WY, aby przejął kontrolę nad Z80.
- **Tryb V**: Natywny tryb działania, skupimy się na nim.

A co z Trybem 0 do III? Cóż, należą one do jeszcze starszego *SG-1000* i Mega Drive ich nie obsługuje.

Jako ciekawostka, były programista tego systemu powiedział mi później, że struktura poleceń Trybu V (używana do kontrolowania VDP w grze) dziedziczy projekt z TMS9918 (słynny układ wideo używany w SG-1000) [@graphics-clarification]. Ułatwiło to zewnętrznym programistom korzystanie z Trybu V bez uzależnienia od oficjalnej dokumentacji (i późniejszych kosztów licencji).

#### Dostępna pamięć

![Architektura pamięci VDP.](VDP_architecture.png) {.no-borders}

Zawartość graficzna jest rozłożona na trzy obszary pamięci [@graphics-keil]:

- 64 KB **VRAM** (RAM Wideo): Zawiera większość danych graficznych.
- 80 B **VSRAM** (RAM Przewijania w Pionie): VDP obsługuje przewijanie pionowe i poziome, a wartości przewijania V są z jakiegoś powodu przechowywane w tej oddzielnej przestrzeni.
- 128 B **CRAM** (RAM Koloru): Przechowuje cztery wpisy palety po 16 kolorów każdy (w tym `przezroczysty`), system zapewnia 512 kolorów do wyboru. Dodatkowo do każdej palety można zastosować efekty *Podświetlenia* i *Cieni*, aby uzyskać szerszy zakres kolorów na paletę.

### Konstruowanie klatki

Poniższa sekcja wyjaśnia, w jaki sposób VDP rysuje każdą klatkę, dla celów demonstracyjnych użyto jako przykładu *Sonic The Hedgehog*. Przed rozpoczęciem polecam zapoznanie się z _modus operandi_ jego [poprzednika](master-system#graphics) ponieważ będzie tu często powracać.

#### Kafelki {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Wiele kafelków zgniecionych razem. Do celów demonstracyjnych wykorzystywana jest paleta domyślna.](vdp_sonic/tiles.png){.active title="Wszystkie"}

![Pojedynczy kafelek 8x8 pikseli.](vdp_sonic/tiles_single.png){title="Pojedynczy"}

Niektóre kafelki znalezione w VRAM.

:::

Podobnie jak PPU Nintendo, VDP jest silnikiem opartym na kafelkach i jako taki wykorzystuje **kafelki** (podstawowe mapy bitowe 8x8) do komponowania płaszczyzn graficznych. W przypadku VDP każdy kafelek jest zakodowany za pomocą 4-bajtowej tablicy, gdzie każdy 4-bitowy wpis odpowiada pikselowi, a jego wartość odpowiada wpisowi koloru (wskazując na paletę kolorów).

Kartridże z grami przechowują kafelki w swoim **ROM** (znajdują się w kartridżu), ale muszą zostać skopiowane do **VRAM**, żeby VDP mógł je odczytać [@graphics-ports]. Tradycyjnie było to możliwe tylko w określonych ramach czasowych i obsługiwane przez procesor, na szczęście konsola ta dodała specjalne obwody, aby odciążyć to zadanie na VDP (szczegóły omówimy później).

Kafelki służą do zbudowania w sumie **czterech płaszczyzn**, które po połączeniu tworzą klatkę widzianą na ekranie. Ponadto kafelki płaszczyzn będą się na siebie nakładać, więc VDP zdecyduje, który kafelek będzie widoczny na podstawie typu płaszczyzny i **wartości priorytetu** kafelka.

#### Tło {.tab}

![Przydzielona mapa tła.](vdp_sonic/layer2.png){.tabs-nested .active .tab-float .pixel title="Pełny"}

![Przydzielona mapa tła z zaznaczonym wybranym obszarem.](vdp_sonic/layer2_selected.png){.tabs-nested-last .pixel title="Wybrany"}

Płaszczyzna tła, znana również jako **Płaszczyzna B**, to przewijalna mapa kafelków (zestaw kafelków) zawierająca **kafelki statyczne** [@graphics-macdonald].

Ta płaszczyzna może mieć sześć różnych wymiarów: 256x256, 256x512, 256x1024, 512x256, 512x512, 1024x256. Programiści mogą wybrać wymiar, który lepiej pasuje do rodzaju przewijania, który będzie wymagany.

Każdy kafelek może być **odwrócony w poziomie i/lub w pionie** i mieć ustawiony **priorytet**.

Na pokazanym przykładzie zauważysz, że obszar wybrany do wyświetlenia nie jest kwadratem... *Nie musi nim być!*. VDP umożliwia ustawienie wartości przewijania w poziomie dla całej klatki, każdej pojedynczej linii skanowania lub co osiem pikseli. Oznacza to, że programiści mogą kształtować wybrany obszar jak romb i zmieniać jego kąty podczas ruchu gracza, aby symulować **efekty perspektywy**. Sztuczki takie jak ten nie psują płaszczyzny, VDP pobiera każdą (wybraną) linię poziomą i buduje z niej zwykłą klatkę.

#### Pierwszy plan {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Przydzielona płaszczyzna pierwszego planu.](vdp_sonic/layer1.png){.active title="Pełny"}

![Przydzielona płaszczyzna pierwszego planu z zaznaczonym wybranym obszarem.](vdp_sonic/layer1_selected.png){title="Wybrany"}

Przykład płaszczyzny pierwszoplanowej, Płaszczyzna Okna nie jest używana.

:::

Płaszczyzna pierwszego planu, znana również jako **Płaszczyzna A** [@graphics-macdonald], ma te same właściwości co płaszczyzna tła, z wyjątkiem tego, że ta płaszczyzna ma **wyższy priorytet**, więc renderowane tutaj kafelki będą z natury znajdować się nad płaszczyzną tła.

Dodatkowo płaszczyzna ta pozwala się podzielić, tworząc nową *płaszczyznę*: **Płaszczyznę Okna**. Jedyna różnica polega na tym, że ta druga się nie przewija.

Podsumowując, możecie zobaczyć nowe wartości priorytetów, a oddzielne płaszczyzny umożliwiają projektantom gry wprowadzanie nowych rodzajów scenerii. Ponadto, używając różnych prędkości przewijania na każdej płaszczyźnie, można osiągnąć **efekt paralaksy**.

#### Sprite'y {.tab}

![Przydzielona warstwa Sprite.](vdp_sonic/sprite.png){.tabs-nested .active .tab-float .pixel title="Pełny"}

![Przydzielona warstwa Sprite z zaznaczonym wybranym obszarem.](vdp_sonic/sprite_selected.png){.tabs-nested-last .pixel title="Wybrany"}

Na tej płaszczyźnie kafelki są traktowane jako **sprite'y**. Są one umieszczone na mapie **512x512 pikseli** i tylko jej część (rozdzielczość wyjściowa VDP) jest wybierana do wyświetlania. Jest to przydatne do ukrywania niechcianych sprite'ów lub przygotowywania innych, które będą pokazywane w przyszłości. VDP udostępnia również starą funkcję [wykrywania kolizji](master-system#tab-4-1-collision-detection).

Sprite'y są tworzone przez połączenie maksymalnie 4x4 kafelków (mapa 32x32 piksele) i wybranie do 16 kolorów (w tym *przezroczyste*). Jeśli potrzebny jest większy sprite, można połączyć kilka spriteów w jeden.

Może być maksymalnie 20 sprite'ów na linię skanowania i 80 na ekran (przepełnienie spowoduje uszkodzenie całej warstwy).

Region w pamięci VRAM, w którym zdefiniowane są sprite'y, nazywa się **Tabela Atrybutów Spriteów** (Sprite Attribute Table) [@graphics-macdonald], a każdy wpis zawiera indeks kafelków, współrzędne warstwy (x i y), wartość `link` (zarządza, które sprite'y są rysowane jako pierwsze), priorytet (sprite o najwyższym priorytecie to ten, który ma być wyświetlany podczas nakładania się), indeks palety kolorów oraz odwracanie w pionie i poziomie.

#### Rezultat {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Wynikowa klatka.](vdp_sonic/result.png){.active title="Klatka"}

![Klatka transmitowana do telewizora (format NTSC), VDP automatycznie pokrywa klatkę obszarem overscan, który ukrywa większość telewizorów CRT.](vdp_sonic/overscan.png){title="Z overscanem"}

Tada!

:::

Podczas rysowania klatki system będzie kolejno wywoływał różne procedury przerwań w zależności od tego, na co wskazuje wiązka CRT. Jak zapewne widzieliście w poprzednich konsolach, pozwala to procesorowi pracować nad następną klatką (lub zmieniać obecną).

Konwencjonalnie istnieją dwa typy przerwań zwane: **H-Blank** (każda pozioma linia) i **V-Blank** (każda klatka).

H-Blank jest wywoływany wielokrotnie, ale ogranicza się do wykonywania krótkich procedur. Ponadto dostępne są tylko CRAM i VSRAM, więc gry mogą tylko aktualizować swoje palety kolorów lub przewijać w pionie swoje płaszczyzny.

V-Blank pozwala na dłuższe procedury z tą wadą, że jest wywoływany tylko 50 lub 60 razy na sekundę (w zależności od regionu konsoli), ale jest w stanie uzyskać dostęp do wszystkich lokalizacji pamięci.

Zauważ, że obszar overscan w przykładzie pokazuje losowo kolorowe kropki w prawym dolnym rogu. Jest to powszechnie znane jako **kropki CRAM** i dzieje się tak, gdyż procesor aktualizuje palety w CRAM w tym samym czasie, gdy VDP wysyła pozostałe linie skanowania (na przykład dzieje się tak podczas overscanu). Ten konflikt powoduje, że VDP pobiera dowolną wartość, którą w tym czasie zapisuje procesor (w przeciwieństwie do wymaganej lokalizacji w pamięci CRAM), więc obraz zostaje uszkodzony. Ponieważ jednak w tym przypadku gra aktualizuje pamięć CRAM tylko przy overscanie, ta anomalia pozostaje niezauważona na tradycyjnych CRT. Inne gry próbują aktualizować palety w trakcie rysowania klatki, aby uzyskać więcej kolorów, co wiąże się z koniecznością zrównoważenia pojawienia się kropek CRAM.

### Dedykowana jednostka transferowa {.tabs-close}

Do tej pory omówiliśmy, co procesor może zrobić, aby zaktualizować klatki, ale co z VDP? Czy zapewnia coś bardziej specjalistycznego? Cóż, tak, ten układ ma **Direct Memory Access** ('DMA' w skrócie) który umożliwia przenoszenie danych między lokalizacjami pamięci z **większą szybkością** i **bez interwencji procesora**.

DMA może być aktywowany w stanie H-Blank, V-Blank lub aktywnym (poza przerwaniem) i może być używany do zapisu w VRAM, CRAM i/lub VSRAM [@graphics-macdonald]. Dodatkowo, podczas transferów pamięci RAM procesora za pomocą DMA, magistrala procesora zostanie zablokowana, więc dobre planowanie ma kluczowe znaczenie dla osiągnięcia wydajności.

Wyjątkowe wykorzystanie tych funkcji może zapewnić grafikę o wysokiej rozdzielczości, płynne przewijanie paralaksy i wysoką liczbę klatek na sekundę. Co więcej, Twoja gra może również pojawiać się w reklamach telewizyjnych z dużą ilością oznaczeń *Blast Processing!*

### Wyjście Wideo

Pierwszy projekt tej konsoli (powszechnie określany jako 'Model 1') ma ten sam port wyjścia wideo co [Master System](master-system#video-output). W kolejnych 'Model 2' i 'Model 3' zamiast tego został użyty port mini-DIN.

## Dźwięk

Możliwości audio tej konsoli są co najmniej nieco niekonwencjonalne. Z jednej strony Mega Drive zapewnia istniejącą technologię audio z poprzedniej generacji, z drugiej dodaje nową (ale skomplikowaną) technikę syntezy do istniejącej. Tak więc w pewnym sensie otrzymujesz oba pokolenia.

Biorąc to pod uwagę, Mega Drive zawiera dwa układy dźwiękowe: **Yamaha YM2612** i **Texas Instruments SN76489**.

### Funkcjonalność

Zobaczmy teraz, co oferują te chipy, ponieważ każdy z nich jest *bardzo* inny.

#### Yamaha YM2612 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![Kanały FM.](fm_single){.active video="true" title="FM"}

![Kanał PCM.](pcm_single){video="true" title="Sample PCM"}

Sonic The Hedgehog (1991).

:::

**Yamaha YM2612** to **syntezator FM** [@audio-ymwiki], który działa z prędkością 68000 i obsługuje **sześć kanałów FM**, gdzie można odtwarzać **próbki PCM** (z rozdzielczością 8-bitową i częstotliwością próbkowania 32 kHz).

Modulacja częstotliwości lub synteza 'FM' to jedna z wielu profesjonalnych technik stosowanych do syntezy dźwięku, znacznie zyskała na popularności w latach 80-tych i utorowała drogę do zupełnie nowych dźwięków (z których wiele można znaleźć słuchając popowych hitów z tamtej epoki).

W *niesamowicie uproszczonym* skrócie, algorytm FM przyjmuje pojedynczy falę (**nośnik**) i zmienia jej częstotliwość za pomocą innej fali (**modulatora**). Rezultatem jest nowa fala z innym dźwiękiem. Kombinacja nośnik-modulator nazywana jest **operatorem** i można połączyć ze sobą wiele operatorów, aby utworzyć ostateczną falę. Różne kombinacje dają różne wyniki. Ten czip pozwala na 4 operatorów na kanał.

W porównaniu z tradycyjnymi syntezatorami PSG była to drastyczna poprawa: nie utknąłeś już z *wstępnie zdefiniowanymi* falami.

#### Texas Instruments SN76489 {.tab}

![Kanały PSG.<br>Sonic The Hedgehog (1991).](psg_single){.tab-float video="true"}

**Texas Instruments SN76489** to **czip PSG**, który może generować trzy fale pulsacyjne i jeden szum.

W rzeczywistości jest to [układ dźwiękowy](master-system#audio) oryginalnego systemu Master System i jest osadzony w VDP. Działa z prędkością Z80.

Zauważ, że kanał 'Impuls 3' pozostaje nieużywany. Dzieje się tak, ponieważ gra używa trybu dla kanału szumów, który rezerwuje trzeci kanał impulsowy do modulacji [@audio-sonic], funkcji która była dostępna również w Master System.

#### Zmiksowane {.tab}

![Wszystkie kanały audio.<br>Sonic The Hedgehog (1991).](complete){.tab-float video="true"}

Oba układy mogą odtwarzać dźwięk w tym samym czasie, a dodatkowy komponent zwany 'mikserem audio' ma za zadanie odbierać oba sygnały i je miksować.

Na koniec otrzymany sygnał analogowy jest przesyłany przez wyjście audio.

### Dyrygent {.tabs-close}

Teoretycznie mapa pamięci Z80 sugeruje, że Z80 jest **jedynym procesorem** zdolnym do sterowania tymi dwoma układami (co może być ulgą dla 68000, ponieważ ten ostatni ma już dość innych zadań). W praktyce jednak Z80 można wyłączyć, więc 68000 ma dostęp do YM2612 (ale nie SN76489) [@audio-68kym]. Tak więc, dla uproszczenia, w tym artykule przyjmiemy, że zadania audio są przypisane do Z80.

Idąc dalej, Z80 jest samodzielnym procesorem, więc potrzebuje własnego programu (przechowywanego w 8 KB dostępnej pamięci RAM), aby móc interpretować dane muzyczne otrzymane z 68000 i odpowiednio manipulować dwoma układami dźwiękowymi. Ten program nazywa się **sekwencer** lub **sterownik**.

### Próbkowanie krakingu

Niektórzy kompozytorzy muzyki mogą zdecydować się na skupienie się na kanale PCM, aby odtwarzać bardziej wierne dźwięki, a do tego gry będą musiały stale sekwencjonować i przesyłać strumieniowo swoją muzykę przy użyciu pozostałej dostępnej RAM. Głównym ograniczeniem jest to, że aby wypełnić tę pamięć, główna magistrala musi być najpierw **zablokowana** (więc żadne polecenia ani próbki nie mogą być wysyłane do układów audio w tym czasie). W przeciwnym razie mogą pojawić się anomalie dźwięku (wyciszanie, zamrożone nuty, niska częstotliwość próbkowania, itp).

Z tego powodu postanowiłem poświęcić tę sekcję kilku instancjom gier, którym udało się pokonać wspomniane wcześniej ograniczenie. Zamiast trzymać się zwykłych zestawów perkusyjnych, niektóre gry znalazły niesamowite sposoby na strumieniowe przesyłanie bogatszych próbek do tego pojedynczego kanału PCM, sprawdź te przykłady:

::: {.subfigures .tabs-nested .toleft max_subfigures=1}

![Kanał PCM.](good_sampling/sonic_pcm){.active video="true" title="Sample PCM"}

![Wszystkie kanały audio.](good_sampling/sonic_complete){video="true" title="Ukończone"}

Sonic The Hedgehog 3 (1994).<br>To jeden z utworów, których współautorem jest Michael Jackson. W każdym razie ogólna ścieżka dźwiękowa miała charakterystyczny rytm w porównaniu do swoich poprzedników.

:::

![Kanał PCM (jedyny używany kanał).<br>Toy Story (1995).<br>Jest to sekwencjonowane w czasie rzeczywistym za pomocą 68000 [@audio-gamehut]. Bardzo intensywne zadanie, co oznacza, że można je było rozegrać tylko w bardzo określonych punktach gry (tj. w menu głównym).](good_sampling/randy_pcm){.tabs-nested .tabs-nested-last .active .toright video="true" title="Samnple PCM/Komplete"}

Wiem, że nie zbliżają się one do jakości CD (16-bit przy 44,1 kHz), ale pamiętaj, że te dźwięki były kiedyś uważane za niemożliwe do odtworzenia na tej konsoli i nawet nie podkreślam, jak duży to postęp w porównaniu z poprzednią generacją, więc z pewnością zasługują przynajmniej na jakieś wspomnienie!

### Wspomagana Kompozycja FM

Jeśli programowanie syntezatora FM było już uważane za skomplikowane przy użyciu pulpitu nawigacyjnego Yamaha DX7, wyobraź sobie ból głowy związany z komponowaniem muzyki przy użyciu jedynie asemblera 68k...

Na szczęście Sega rozprowadziła później oprogramowanie dla komputerów z systemem MS-DOS o nazwie **GEMS**, aby ułatwić komponowanie (i debugowanie) muzyki z Mega Drive [@audio-gst]. Było to bardzo kompletne narzędzie, zawierało między innymi wiele *łat* (do wyboru wstępnie skonfigurowane operatory), które wyjaśniałyby również, dlaczego niektóre gry mają *bardzo* podobne dźwięki.

Co więcej, podsystem audio umożliwiał grom tworzenie większej liczby kanałów niż jest to dozwolone i przypisywanie każdemu z nich **wartości priorytetu**, a następnie, gdy konsola odtwarzała muzykę, dynamicznie wysyłała kanały muzyczne do dostępnych slotów w oparciu o priorytety. Dodatkowo kanały o wysokim priorytecie, ale bez muzyki mogą być automatycznie pomijane.

Kanały zawierały również pewną **logikę**, implementując warunki w swoich danych, umożliwiając muzyce 'ewoluowanie' w zależności od postępów gracza w grze.

### (Bonus) Dźwięk Mega CD {.interactive-only}

Oto ciekawostka: dodatek Mega CD zapewniał 2 dodatkowe kanały dla CD Audio (między innymi). Jedna z jej najsłynniejszych gier, Sonic CD, miała imponującą jakość muzyki, ale jak wszystkie gry musiała zapętlać się. Problem polegał na tym, że zapętlenie muzyki na 1x czytniku CD ujawniło zauważalne luki, więc gra zawierała wypełniacze pętli, które były wykonywane z innego układu PCM, podczas gdy nagłówek CD powracał do początku.

Te wypełniacze można znaleźć tylko we wczesnych wersjach beta gry i nie pojawiły się one w wydaniu, remake z 2011 roku w końcu je uwzględnił. Oto jeden z poziomów gry:

![Wersja MegaCD (1993).](sonic_cd_original){.toleft audio="true"}

![Wersja zremasterowana (2011).](sonic_cd_remaster){.toright audio="true"}

Czy zauważasz lukę w wersji Mega CD?

## Gry

Gry są pisane głównie w **asemblerze 68000**, podczas gdy sterownik dźwięku jest zaimplementowany w **asemblerze Z80**. Oba znajdują się w ROM kartridża i mogą mieć rozmiar do 4 MB bez potrzeby korzystania z [mappera](nes#going-beyond-existing-capabilities).

### Dodatkowe funkcje

Pod względem możliwości rozbudowy ten projekt nie był tak modułowy jak [NES](nes) czy [SNES](super-nintendo). Stąd późniejsze dodatki, takie jak 32x (który zawierał nowy chipset, który przejmuje 68k) musiały ominąć VDP (stąd potrzeba 'Connector Cable').

Wyprodukowano tylko jeden niestandardowy chip dla kartridży, **Sega Virtua Processor** \[@games-virtua\] (rebranding Samsunga SSP1601, 16-bitowego cyfrowego procesora sygnałowego), który wytworzył wielokąty następnie zakodowane w postaci kafelków (aby VDP mógł je odczytać). W każdym razie dostarczono z nim tylko jedną grę, ponieważ SVP okazał się bardzo drogi w produkcji.

### Wczesne próby networkingu

Zanim usługi online zostały powszechnie przyjęte (i ustandaryzowane), firma Sega próbowała szczęścia z **Sega Meganet**, usługą dial-up do gier. Meganet wymagał od użytkowników zakupu osobnego akcesorium o nazwie _Sega Mega Modem_, a następnie podłączenia go z tyłu konsoli (gdzie znajdywało się złącze DE-9) i na koniec, podłączenia go do linii telefonicznej. Gry traktowały wtedy modem jako inny kontroler, z dodatkiem komunikowania się z nim szeregowo \[@games-ioports\] (w przeciwieństwie do enkodowania równoległego używanego przez kontrolery).

Tak czy inaczej, ta funkcja trwała tylko kilka lat, zanim Sega usunęła złącze DE-9 w przyszłych wersjach i całkowicie wyłączyła usługę.

## Przeciwdziałanie-Piractwu / Blokowanie Regionu

Aby zablokować importowane gry, Sega nieznacznie zmieniła kształt gniazda kartridża między regionami, chociaż zachowała te same pinouty. Gry mogą również wykonywać 'blokowanie regionu', sprawdzając wartość rejestru `Version` (który wyprowadza wartość regionu).

Były dwa proste sposoby na ominięcie tego. Po pierwsze, kupując jeden z konwerterów kartridży innych firm. Albo po drugie, przez rozmontowanie konsoli i zmostkowanie pinów na płycie głównej, które zmieniają wartość rejestru `Version`.

Jeśli chodzi o zwalczanie piractwa oprogramowania, najłatwiej sprawdzić rozmiar pamięci SRAM: Bootlegi miały więcej miejsca niż potrzeba, aby zmieścić jakąkolwiek grę, więc gry były sprawdzane pod kątem oczekiwanego rozmiaru podczas uruchamiania. Programiści mogli również wprowadzić dodatkowe kontrole sum kontrolnych w losowych punktach gry na wypadek, gdyby hakerzy usunęli początkowe kontrole SRAM. Jedynym sposobem na pokonanie tego było żmudne odnalezienie wszystkich czeków i usunięcie ich jeden po drugim...

## To wszystko ludziska
