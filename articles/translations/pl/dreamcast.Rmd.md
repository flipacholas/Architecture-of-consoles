---
short_title: Architektura Dreamcast
long_title: Architektura Dreamcast
name: Dreamcast
date: 2019-10-07
release_date: 1998-11-27
subtitle: Ostatnia próba
generation: 6
javascript:
  - 'threejs'
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "Dreamcast.<br>Wydany 29.11.1998 w Japonii, 09.09.1999 w Ameryce i 14.10.1999 w Europie."
  Motherboard:
    caption: "Pokazuję wersję 'VA1'.<br>Podczas gdy oficjalna dokumentacja podaje, że system zawiera 128 KB pamięci flash, ta płyta główna zawiera z jakiegoś powodu układ 256 KB EEPROM.<br>Porty baterii i kontrolera znajdują się na karcie towarzyszącej o nazwie 'Panel przedni'."
  Diagram:
    caption: "Ważne magistrale danych są oznaczone ich szerokością i prędkością."
#Historical
aliases:
  - /projects/consoles/dreamcast/
---

## Wstęp

Sega Dreamcast wprowadziła wiele nowych funkcji w stosunku do swojego poprzednika (Sega [Saturn](sega-saturn)), aby spodobać się zarówno twórcom gier, jak i graczom konsolowym. Chociaż była to ostatnia próba podbicia rynku konsol przez firmę Sega, niektóre z technologii, które były pionierami w Dreamcast, przeniosły się na przyszłe urządzenia głównego nurtu.

```{r results="asis"}
supporting_imagery()
```

## CPU

Nic dziwnego, że Sega ponownie wybrała Hitachi do opracowania swojego procesora. Jeśli czytałeś [poprzedni artykuł o Sega Saturn](sega-saturn) to pozwólcie, że przedstawię Wam następną generację procesora SH: **SH-4** działający z niesamowitą prędkością **200 MHz** [@cpu-spec]. Więc co jest interesującego w tym procesorze?

- **Potok 5-stopniowy**: Do pięciu instrukcji może być przetwarzanych jednocześnie (szczegółowe wyjaśnienie można znaleźć w [poprzednim artykule](sega-saturn#cpu)).
  - Potokowanie instrukcji można teraz znaleźć wszędzie w tej generacji konsol i od teraz będzie standardem.
- **Dwukierunkowa superskalarność**: Nowy typ równoległości, w którym procesor może przetwarzać więcej niż jedną instrukcję (w tym przypadku dwie) na każdym etapie potoku, co skutkuje większą liczbą instrukcji wykonywanych na sekundę.
- Dedykowana **Jednostka Zmiennoprzecinkowa** lub 'FPU': Oblicza 32-bitowe liczby dziesiętne (*pojedynczej precyzji*) i 64-bitowe (*podwójnej precyzji*).
- **Pamięć podręczna instrukcji** 8KB i **pamięć podręczna danych** 16 KB: Ten stosunek jest dość ciekawy, ponieważ konsole zwykle zawierają więcej pamięci podręcznej instrukcji niż pamięci podręcznej danych. Jednak SH-4 pozwala na podzielenie pamięci podręcznej danych na dwie sekcje: 8 KB *Scratchpad* (szybki RAM) i 8 KB pamięci podręcznej danych.
- **32-bitowa architektura wewnętrzna** przy zachowaniu **16-bitowego zestawu instrukcji** (SuperH ISA): Podobnie jak SH-2, zwiększa to gęstość kodu i zmniejsza koszty ogólne magistrali, jednocześnie korzystając z zalet architektury 32-bitowej.
- **Zewnętrzna magistrala 64-bitowa**: Kluczowa dla manipulowania wartościami 64-bitowymi (np. podwójnej precyzji i long) bez marnowania dodatkowych cykli.

### Dodatkowa praca

Typowe obowiązki CPU konsoli do gier obejmują obsługę logiki gry, prowadzenie wrogiej sztucznej inteligencji i dostarczanie instrukcji GPU. W Dreamcast SH-4 jest również zaangażowany w większość potoku graficznego, przetwarzając dane geometryczne, takie jak obliczanie przekształceń perspektywy. W rezultacie zawiera **128-bitową jednostkę SIMD**, która może przyspieszyć operacje wektorowe [@cpu-arch].

### Polepszenie dostępu do pamięci

Procesor zawiera dedykowaną **Jednostkę Zarządzania Pamięcią** lub 'MMU' do adresowania wirtualnego, jest to pomocne, ponieważ fizyczna przestrzeń adresowa pamięci tego procesora ma **szerokość 29 bitów**. Tak więc za pomocą czterech TLB programiści mogą używać adresów 32-bitowych bez pogorszenia wydajności.

Ponieważ do adresowania potrzeba tylko 29 bitów, dodatkowe trzy bity kontrolują ochronę pamięci, odpowiednio zmieniając mapę pamięci i omijając pamięć podręczną \[@cpu-marcus\] \[@cpu-akiba\].

Programista decyduje, czy korzystać z tych funkcji, czy nie. Gry dla tego systemu z pewnością niekoniecznie _wymagają_ ochrony pamięci, a MMU musi być ręcznie włączane przy starcie.

### Brak UMA ale...

Chociaż ten system nie jest zaprojektowany w oparciu o ścisłą architekturę zunifikowanej pamięci, [jak znany konkurent](nintendo-64#simplified-memory-access), **deleguje dostęp WE/WY do GPU**. Oznacza to, że jeśli procesor musi pobrać coś, co jest poza dedykowaną RAM lub interfejsem szeregowym (który jest również podłączony), będzie musiał zażądać GPU i poczekać, jeśli to konieczne.

### Zapytania specjalne

Ten CPU posiada również unikalną funkcjonalność o nazwie **Równoległe WE/WY** lub 'PIO', która służy do manipulowania wieloma lokalizacjami WE/WY w tym samym czasie. Sega podłączyło te piny, aby CPU mógł manipulować **trybem wideo** GPU (więcej szczegółów na ten temat później).

## Grafika

Pakiet GPU to wykonany na zamówienie układ o nazwie **Holly** działający z częstotliwością **100 MHz**, zaprojektowany przez firmę VideoLogic (obecnie znana jako Imagination Technologies) i wyprodukowany przez NEC. Tak się składa, że rdzeniem 3D Holly jest **PowerVR2** firmy Videologic (nazywany również 'PowerVR Series2' i 'CLX2').

![Sonic Adventure (1999).](sonic.png) {.open-float}

Firma VideoLogic wybrała alternatywne podejście do budowy swojego silnika 3D o nazwie **Opóźnione Renderowanie Oparte na Kafelkach** (lub TBDR).

Zamiast renderować całą klatkę naraz (jak to robią tradycyjne **Renderery w Trybie Natychmiastowym** lub 'IMR' [@graphics-arch]), TBDR dzieli obszar renderowania na wiele sekcji zwanych 'kafelki'. Następnie wykonuje proces renderowania na każdym kafelku osobno, a wynik jest łączony, tworząc ostateczną klatkę [@graphics-powervr].

`r close_float_group(with_markdown = TRUE)`

Ta innowacyjna konstrukcja przynosi ciekawe korzyści:

- Może być znacznie **zrównoleglony**, co znacznie zmniejsza wykorzystanie przepustowości i zużycie energii.
- Implementuje sprytne rozwiązanie [**problemu z widocznością**](sega-saturn#an-introduction-to-the-visibility-problem) automatycznie sortując wielokąty **od przodu do tyłu** a następnie przeprowadzając [testy z](nintendo-64#modern-visible-surface-determination) na pierwszych etapach potoku. Połączenie tych zadań nie tylko rozwiązuje pierwotny problem, ale także **zapobiega przerysowywaniu** (rasteryzacji ukrytych wielokątów) która marnuje zasoby i obniża wydajność.

Nic dziwnego, że firma Imagination wykorzystała tę wydajną technologię do budowy rdzeni PowerVR z serii 4, które zasilały niewiarygodną liczbę urządzeń, w tym pierwszą generację iPhone'a, iPhone'a 3G, Nokię N95 i Della Axim x51.

### Architektura

Przyjrzyjmy się dwóm głównym komponentom GPU Dreamcasta [@graphics-marcus]:

#### Tile Accelerator {.tabs.active}

![Architektura Tile Accelerator.](tile_accelerator.png) {.tab-float}

Przed rozpoczęciem procesu renderowania komponent znany jako **Tile Accelerator** wykonuje wstępne przetwarzanie. Rozpoczyna się od przydzielenia kilku pojemników 32x32 kafelków, w których zostanie wyrenderowana geometria.

Następnie Tile Accelerator:

1. Pobiera dane geometrii i polecenia rysowania wydawane przez CPU (za pomocą DMA lub tradycyjnych transferów).
2. Przekonwertuje te dane na format *wewnętrzny*.
3. Rozprowadzi geometrię do każdego pojemnika na podstawie jego współrzędnych. Przycięta geometria również zostanie odrzucona.
4. Wygeneruje wynikowe listy wyświetlania.

Te listy wyświetlania są następnie interpretowane przez silnik 3D: PowerVR2.

#### Rdzeń PowerVR2 {.tab}

![Architektura Rdzenia PowerVR2.](powervr2.png) {.tab-float}

W tym miejscu grafika zostaje ożywiona, listy wyświetlania otrzymane od TA informują rdzeń o renderowaniu geometrii pojedynczego kafelka przy użyciu **wewnętrznego bufora-klatki**. Proces wygląda następująco:

1. **Image Synthesis Processor** lub 'ISP' pobiera prymitywy (trójkąty lub czworokąty) i wykonuje **Usuwanie ukrytych powierzchni** aby usunąć niewidoczne wielokąty. Następnie, po obliczeniu buforów Z i szablonów, dane przechodzą przez **Testowanie Głębokości**, aby uniknąć renderowania wielokątów, które pojawiałyby się za innymi i **Testy Szablonowe**, aby usunąć geometrię, która nie będzie widoczna, jeśli znajduje się za wielokątem 2D (nazywanym również **Maską**).
    - Zwróć uwagę na to, że testy te są przeprowadzane na początku potoku. W przeciwieństwie do tego poprzednie konsole [używające późnego buforowania Z](nintendo-64#modern-visible-surface-determination) odrzucają geometrię na końcu potoku. Podejście ISP zapobiega przetwarzaniu geometrii, która ostatecznie zostanie odrzucona [@graphics-surface], oszczędzając w ten sposób zasoby.
2. **Texture and Shading Processor** lub 'TSP' stosuje kolorowanie, cieniowanie i wiele efektów na obszarze kafelka.
    - Tekstury nie są stosowane, dopóki kafelek nie zostanie wyeksportowany, co oznacza, że pojawiające się przerysowanie (jeśli występuje) nie obniży współczynnika wypełniania.

Po zakończeniu operacji wyrenderowany kafelek jest zapisywany w głównym buforze klatki w VRAM. Ten proces jest powtarzany, aż wszystkie kafelki zostaną ukończone. Po zakończeniu wynikowy bufor ramki jest pobierany przez **enkoder wideo** i wysyłany przez sygnał wideo.

### Szersza perspektywa {.tabs-close}

Oprócz wyraźnej różnicy architektonicznej, Texture and Shading Processor ma wiele możliwości, które dają wyobrażenie o tym, jak daleko ta konsola jest od starego [Saturna](sega-saturn). Oto kilka godnych uwagi przykładów:

- **Mieszanie alfa**: Łączy kolory nakładających się warstw, aby uzyskać efekty przezroczystości.
  - Proces stosowany do stosowania przezroczystości w tym systemie nazywa się **przezroczystością niezależną od kolejności**. Algorytm automatycznie sortuje prymitywy przed zmieszaniem ich kolorów i chociaż spowalnia to proces renderowania, unika polegania na tym, że sama gra wykonuje wszystkie czynności ręcznie. Z tego powodu gry Dreamcast są wybitne w wyświetlaniu przezroczystych obiektów.
  - W połączeniu z systemem opartym na kafelkach przezroczystość niezależna od kolejności całkowicie rozwiązuje poprzednie [wpadki](sega-saturn#the-transparency-issue).
- **Mip-Mapping**: Automatycznie wybiera pomniejszoną wersję tekstury w zależności od wymaganego poziomu szczegółowości. Ma to na celu zapobieganie przetwarzaniu dużych tekstur, które byłyby widoczne z dużej odległości od kamery (co byłoby marnowaniem mocy obliczeniowej i powodowałoby aliasing).
- **Mapowanie środowiska**: Stosuje odbicia na teksturach.
- **Filtrowanie Dwuliniowe, Trójliniowe i Anizotropowe**: Są to różne algorytmy używane do wygładzania tekstur i zapobiegania pikselizacji. Są one uporządkowane od 'najgorszych' do 'najlepszych', gdzie wynikowa jakość każdego z nich jest wprost proporcjonalna do ilości wymaganych obliczeń.
  - To ogromny krok naprzód w stosunku do Saturna, ponieważ nie zapewniał żadnego filtra tekstur!
- **Mapowanie wypukłości**: Symuluje defekty na powierzchniach bez wydawania dodatkowych wielokątów.

### W rezultacie...

Holly może teraz narysować ~10 razy więcej wielokątów niż [jego poprzednik](sega-saturn), oto przykład *Przed & Po* który pokazuje, że projekty modeli nie są już tak ograniczone. Spróbuj się z nimi pobawić!

(ref:sonicrmodelcaption) Sonic R (1997) dla Saturna.<br>286 trójkątów (lub 185 czworokątów).

(ref:sonicadvmodelcaption) Sonic Adventure (1999) dla Dreamcasta.<br>1001 trójkątów.

```{r fig.cap=c("(ref:sonicrmodelcaption)", "(ref:sonicadvmodelcaption)"), side_by_side=TRUE, fig.pos = "H"}
model_viewer('sonic_r_saturn', class="toleft", "(ref:sonicrmodelcaption)")
model_viewer('sonic_adventure_dc', class="toright", "(ref:sonicadvmodelcaption)")
```

### Tryby Wideo

System wideo został zaprojektowany do obsługi wielu typów ekranów i formatów, dlatego wideoenkoder wysyła sygnał do gniazda o jednym kształcie, które obsługuje następujące rodzaje sygnałów:

- **Kompozytowy**: łączy trzy sygnały potrzebne do wyświetlania wideo (kolorystyka, jasność i synchronizacja) w jeden, wymagając tylko jednopinowego kabla.
  - Jest to używane w starych telewizorach PAL i NTSC z połączeniem RCA.
- **S-Video**: Łączy jasność i synchronizację przy jednoczesnym zachowaniu separacji barwy (łącznie dwie linie sygnału wideo).
- **RGB**: Wysyła oddzielne sygnały czerwony-zielony-niebieski i zapewnia różne typy synchronizacji do wyboru (synchronizacja kompozytowa lub wyodrębniona z kompozytowego wideo lub S-Video).
  - Tego typu użyje kabel SCART.
- **VGA**: Łączy RGB z dwoma sygnałami synchronizacji (poziomym i pionowym), dając w sumie pięć linii sygnału wideo. Umożliwia to wyświetlanie w największej możliwej rozdzielczości (720x480) w trybie progresywnym (stąd ten tryb jest często nazywany '480p'). VGA był przez pewien czas standardowym formatem/medium używanym przez monitory komputerowe.
  - Aby użyć tego typu, Sega dostarczyła adapter VGA jako dodatkowe akcesorium.

Dreamcast nie może zakodować ich wszystkich jednocześnie, więc GPU i procesor Audio zawierają rejestr o nazwie **Tryb Obrazu**, który koordynuje, które magistrale wideo/audio zostaną aktywowanie, aby wygenerować żądany sygnał. Procesor wykrywa rodzaj włożonego kabla (sprawdzając, które 'wybrane bity' złącza wideo są aktywne) i zapisuje wymagane wartości na GPU. Na koniec wartości są przekazywane do procesora Audio.

Ponieważ VGA jest ściśle progresywnym typem sygnału (w przeciwieństwie do tradycyjnego sygnału *z przeplotem*), pojawiły się pewne problemy ze zgodnością z grami, które zostały zaprojektowane tylko dla wideo z przeplotem. Te wyraźnie stwierdzają w swoim kodzie, że gra nie będzie wyświetlana na VGA, więc procesor zablokuje grę, dopóki użytkownik nie wymieni kabla VGA na inny typ.

## Dźwięk

Funkcjonalność audio jest obsługiwana przez niestandardowy układ o nazwie **AICA** firmy Yamaha, jest to ulepszona wersja [SCSP używanego w Saturnie](sega-saturn#audio) i składa się z czterech komponentów:

- **Sound Integrated Circuit** lub 'IC': Zestaw modułów (syntezator, DSP i mikser) generujący sygnał audio i nakładający na niego efekty. Obsługuje do **64 kanałów PCM** z rozdzielczością **16 lub 8 bitów** i częstotliwością próbkowania **44,1 kHz**. Ogólnie rzecz biorąc, jest to optymalna jakość odtwarzania dźwięku.
  - Dodatkowo zawiera **dekoder ADPCM**, aby odciążyć część pracy z procesora.
  - Co ciekawe, zapewnia również **dwa piny MIDI** do podłączenia instrumentu MIDI, chociaż ma to być używane podczas programowania.
- **2 MB SDRAM-u**: Przechowuje dane dźwiękowe i programy. Jest wypełniany przez główny procesor za pomocą DMA.
- **ARM7DI** działający z częstotliwością ~2,82 MHz: Kontroluje Sound IC. Ten procesor jest programowany przez uruchomienie małego oprogramowania (zwanego [sterownikiem](super-nintendo#audio)) przechowywanego w pamięci SRAM, które odpowiednio interpretuje dane audio i manipuluje Sound IC.
  - Jeśli się zastanawiasz, podobny procesor można znaleźć w [Game Boy Advance](game-boy-advance).
- **Kontroler Pamięci**: Oddziałuje na 2 MB SDRAM.

Aby pomóc w programowaniu, oficjalny SDK zawierał wiele sterowników dźwięku do różnych potrzeb (sekwencjonowanie, dekodowanie, itp).

### Ewolucja

Zaszliśmy tak daleko od czasów [Mega Drive/Genesis](mega-drive-genesis#audio), aby pokazać, jak bardzo poczyniono postępy w syntezie dźwięku, oto przykład dwóch gier, jednej dla Mega Drive i drugiej dla Dreamcast, które wykorzystywały tą samą kompozycję:

![Sonic 3D Blast (1996) dla Mega Drive.<br>Poprzednik wykonuje syntezę FM w celu generowania sygnałów audio w locie.](megadrive){.toleft video="true"}

![Sonic Adventure (1999) dla Dreamcastu.<br>Nowy podsystem audio przetwarza próbki PCM bez żadnych problemów.](dreamcast){.toright video="true"}

Zamiast programować chip FM, kompozytorzy Sonic Adventure wyprodukowali własną ścieżkę dźwiękową, a następnie zakodowali ją w 'ADX', stratnym formacie opracowanym przez CRI Middleware. W związku z tym wykorzystuje tylko dwa z 64 kanałów PCM (stereo).

Kompresja ADX umożliwia grze dekodowanie i przesyłanie strumieniowe danych z GD-ROM do Sound IC bez wyczerpania pamięci lub przepustowości. Sterownik można zaimplementować na wiele sposobów, ponieważ istnieje wiele różnych podejść do zrównoważenia obciążenia głównego CPU i ARM7.

### Pozostając przy życiu

Z jakiegoś powodu ten układ jest również odpowiedzialny za dostarczanie **Zegara Czasu Rzeczywistego** (RTC) do BIOS-u, jest również podłączony do baterii zegara, aby kontynuować pracę bez zasilania prądem zmiennym.

## System Operacyjny

2 MB 'System ROM' przechowuje **BIOS**, który uruchamia grę lub małą powłokę, gdy konsola jest włączona.

BIOS zawiera również procedury, których używają gry do uproszczenia funkcji WE/WY [@games-redream], takich jak odczyt z napędu GD-ROM.

### Powłoka

Jeśli nie ma włożonej prawidłowej płyty z grą, konsola uruchomi powłokę graficzną.

![Powłoka po uruchomieniu bez dysku.](shell.png) {.open-float}

Powłoka zawiera prosty graficzny interfejs użytkownika, który umożliwia użytkownikowi wykonywanie podstawowych, ale niezbędnych zadań, takich jak:

- Uruchomienie gry, jeśli jeszcze nie została uruchomiona.
- Manipulowanie danymi zapisu przechowywanymi w VMU (więcej szczegółów na temat tego urządzenia później!).
- Odtwarzanie muzyki, jeśli jest włożona płyta Audio CD.
- Zmienianie niektórych ustawień (Data, Czas, Dźwięk, itp).

`r close_float_group(with_markdown = TRUE)`

### Windows CE

Od czasu ogłoszenia Dreamcasta mówiono, że na konsoli można uruchomić **Windows CE**: uproszczoną wersję systemu Windows zaprojektowaną do użytku na urządzeniach osadzonych. Jest to trochę mylące, biorąc pod uwagę, że niektórzy użytkownicy spodziewaliby się, że na ich konsoli będzie działać pełne środowisko graficzne Windows CE.

![Logo Windows CE wybite z przodu obudowy.](windows_ce.jpeg) {.open-float}

W rzeczywistości cel tego 'OS' był bardzo podobny do tego, co Nintendo zrobiło z [Nintendo 64](nintendo-64#operating-system): zapewnienie programistom rzetelnej warstwy abstrakcji w celu uproszczenia niektórych operacji.

Microsoft współpracował z firmą Sega, aby wprowadzić system Windows CE do Dreamcasta [@games-sdk]. W rezultacie powstał podzbiór CE z minimalnymi komponentami potrzebnymi do zapewnienia grafiki, dźwięku i debugowania. Obejmowało to użycie najlepszego IDE Microsoftu, **Visual Studio**, do rozwoju.

`r close_float_group(with_markdown = TRUE)`

Niektórzy programiści uznali tę opcję za bardzo atrakcyjną. Ponieważ struktura audio-graficzna dołączona do CE była niczym innym jak **DirectX 6**, tysiące gier na PC z tamtej epoki możnaby teoretycznie łatwo przenieść na Dreamcasta...

Jednak różnice architektoniczne między Dreamcastem a konwencjonalnym komputerem PC były zbyt duże, aby je zignorować [@games-direct]. Ponadto osadzenie tego systemu wydłużyło czas ładowania gry (w końcu 'system operacyjny' musiał być ładowany z dysku), a Windows CE zużywał znaczną część zasobów Dreamcasta (*nie jest to zaskoczeniem - komputery PC już cierpiały z tego powodu*).

Ostatecznie 'Windows CE dla Dreamcast' był po prostu kolejnym zestawem SDK wybieranym przez programistów (powszechnie określany jako **Dragon SDK**). Niemniej jednak znaczna liczba gier Dreamcast ostatecznie wybrała Windows API i DirectX.

## WE/WY

GPU zawiera również inny moduł do obsługi większości WE/WY o nazwie **System Bus**. Zapewnia następujące interfejsy:

- Interfejs **G1**: Gdzie możliwy jest dostęp do **BIOS ROM** wraz z zapisaną konfiguracją i zawartości **GD-ROM**.
- Interfejs **G2**: Zapewnia dostęp do **Modemu** i **Kontrolera Dźwięku**.
- Interfejs **Maple**: Przesyła porcje danych między kontrolerami (wraz z podłączonymi do nich akcesoriami) a procesorem. Jest to **magistrala szeregowa** i zapewnia dedykowane DMA.
- Interfejs **SH-4**: Łączy główny procesor w celu komunikacji ogólnego przeznaczenia.
- Interfejs **DDT**: Przejmuje kontrolę nad magistralą procesora, aby uzyskać dostęp do pamięci głównej podczas transferów DMA.
- Interfejs **PVR**: Łączy procesor z Tile Acceleratorem za pomocą dedykowanego DMA.

## Gry

Programowanie odbywało się głównie w **C** lub **C++**. Początkowo zalecanym wyborem było C, ponieważ dostępne kompilatory C++ były początkowo bardzo ograniczone pod względem funkcjonalności.

Firma Sega dostarczyła również sprzęt programistyczny w postaci wieży podobnej do komputera PC o nazwie **Sega Katana Development Box**. To jest sprzęt Dreamcast z ulepszonymi WE/WY do rozwoju. Był również dostarczany z płytą CD zawierającą oficjalny **Katana SDK** i narzędzia do zainstalowania na komputerze z systemem Windows 98.

W przypadku, gdy programiści wybrali Dragon SDK, dostępne były również DirectX 6.0 i Visual C++ 6.0.

### Nośnik danych

Gry są przechowywane na GD-ROM-ach, które są po prostu CD-ROM-ami o większej gęstości pitów (osiągającej gigabajt pojemności). Prędkość wynosi 12x, co jest *całkiem niezłe* w porównaniu z czytnikiem CD 2x Saturna.

### Platforma online

Dreamcast jest dostarczany z zainstalowanym modułem **modem**, którego gry mogą używać do 'wywoływania' usługi dial-up do gier online. Firma Sega świadczyła dwie usługi: **SegaNet** (używaną w Ameryce i Japonii) oraz **Dreamarena** (europejski odpowiednik).

Gracze rejestrowali się w usłudze przy użyciu **DreamKey**, dodatkowej płyty dołączonej do niektórych gier. DreamKey dostarczał przeglądarkę internetową do rejestracji konta. Początkowo DreamKey był wstępnie skonfigurowaną usługą w zależności od regionu, ale późniejsze wersje pozwoliły użytkownikom zmienić ustawienia ISP, aby połączyć się z dowolnym z nich.

Do kupienia była również klawiatura i mysz marki Dreamcast, na wypadek gdyby użytkownik chciał surfować po sieci *w stylu PC*.

Niestety SegaNet i Dreamarena zostały porzucone dwa lata po premierze. W ten sposób gry, które opierały się wyłącznie na nich, stały się bezużyteczne, chyba że takie usługi są emulowane za pomocą dodatkowych narzędzi (takich jak DreamPi, obraz Raspberry Pi, który replikuje je za pomocą serwerów obsługiwanych przez społeczność użytkowników).

### Interaktywna karta pamięci

Inną innowacyjną funkcją, którą zaprezentował Dreamcast, była **Visual Memory Unit** lub 'VMU'. Jest dołączony do kontrolera i oprócz tego, że służy jako karta pamięci, jest pełnoprawnym urządzeniem, które zawiera [@games-vmu]:

![VMU odłączony od kontrolera.](vmu.png){.tabs-nested .active .open-float .tab-float title="VMU"}

![Kontroler bez podłączonego VMU.](controller.png){.tab-nested title="Osobny"}

![Kontroler z dołączonym VMU.](controller-vmu.png){.tabs-nested-last title="Przytwierdzony"}

- **Sanyo LC86K87**: 8-bitowy procesor o małej mocy.
- **Monochromatyczny wyświetlacz LCD 32x48** z czterema dodatkowymi ikonami: Sterowany przy użyciu 196B XRAM (zewnętrznej pamięci RAM) jako bufora klatki.
- **Dwa złącza szeregowe**: Jedno dla WEJŚCIA, a drugie dla WYJŚCIA.
- **Sześć fizycznych przycisków**: Używane, gdy VMU jest odłączony od kontrolera.
- **16 KB Mask ROM**: Przechowuje BIOS-IPL.
- **64 KB Flash**: 32 KB do przechowywania pojedynczego programu (przeniesionego z konsoli) i pozostałe 32 KB do przechowywania zapisów Dreamcast.
- **512 B RAM**: 256 B jest zarezerwowane dla systemu, pozostawiając tylko 256 B dostępne dla programu.

`r close_float_group(with_markdown = TRUE)`

VMU ma dwa tryby pracy:

- **Dołączony do kontrolera**: Oficjalny kontroler ma dwa gniazda do podłączenia VMU i innych akcesoriów o tym samym kształcie, jeśli VMU jest włożony do pierwszego gniazda (widoczne z przodu kontrolera), może wyświetlać rysunki podczas gry. Co więcej, Dreamcast może przechowywać sejwy i program na VMU.
- **Odłączony od kontrolera**: Gadżet staje się urządzeniem podobnym do Tamagotchi z zegarem, menedżerem zapisów i może również uruchamiać dowolny program, który wcześniej przesłał Dreamcast. Można również podłączyć dwa VMU, aby udostępniać zawartość.

## Przeciwdziałanie-Piractwu & Homebrew

Korzystanie z zastrzeżonego formatu GD-ROM pomogło zahamować produkcję nieautoryzowanych kopii gier (i uruchamianie ich na innych konsolach). Gry Dreamcast są również *zablokowane na region*, co oznacza, że konsola odmówi uruchomienia gry przeznaczonej dla innego regionu.

### Pokonywanie

W praktyce środki antypirackie były *całkowicie* bezużyteczne, ponieważ firma Sega pozostawiła otwarte drzwi: **MIL-CD**. Music Interactive Live-CD lub 'MIL-CD' to format stworzony przez firmę Sega w celu rozszerzenia płyty Audio-CD o programy interaktywne... i Dreamcast jest z nim kompatybilny [@anti_piracy-history].

Nieautoryzowane płyty komercyjne (programy ładujące kody, odtwarzacze filmów, itp.) zamaskowane jako płyty MIL-CD mogły działać na konsoli bez zgody firmy Sega. Później różne społeczności hakerów przeanalizowały ten exploit i opracowały obejście umożliwiające uruchamianie pirackich gier przy użyciu dysków CD-ROM. To spowodowało, że w sieci pojawiła się niepowstrzymana fala obrazów ISO.

Później pojawiły się pewne problemy: Chociaż GD-ROM-y mogą przechowywać gigabajt danych, CD-ROM-y mogą zmieścić tylko ~700 MB, więc jak 'ripperzy' mogą zmniejszyć większe gry, aby zmieściły się na płycie CD? Ponownie kompresując muzykę i grafikę, aż będą pasować. Mogą nawet próbować podzielić je na dwa dyski. W końcu dane gry nie są już pojedynczą blokiem (jak na starym kartridżu), ale są teraz zorganizowane hierarchicznie w pliki i katalogi.

## To wszystko ludziska

![Dreamcast, którego musiałem zdobyć, żeby napisać tu dużo rzeczy.<br>Niezły jak na swój wiek!](folks.png)

Mam nadzieję, że podobało Ci się czytanie tego artykułu. Skończyłem go pisać na początku mojego ostatniego roku na uniwersytecie.

Prawdopodobnie od teraz będę bardzo zajęty, ale lubię pisać te artykuły, więc mam nadzieję, że za kilka tygodni dostaniesz następny!

Do następnego razu!  
Rodrigo
