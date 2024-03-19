---
short_title: Architektura Nintendo 64
long_title: Architektura Nintendo 64
name: Nintendo 64
release_date: 1996-06-23
date: 2019-09-12
generation: 5
subtitle: Potężny i skomplikowany!
cover: nintendo64
javascript:
  - 'threejs'
top_tabs:
  Model:
    caption: "Nintendo 64.<br> Wydany 23/06/1996 w Japonii, 29/09/1996 w Ameryce i 01/03/1997 w Europie"
    file: "international"
  Motherboard:
    caption: "Pokazuję wersję 'NUS-CPU-03'.<br>Późniejsze zmniejszyły liczbę chipów wymaganych do kodowania AV.<br>Złącze Disk Drive znajduje się z tyłu"
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nintendo-64/
---

## Szybkie wprowadzenie

Celem Nintendo było dostarczenie graczom *najlepszej* możliwej grafiki, w tym celu sprzymierzyli się z jednym z największych graczy w grafice komputerowej, aby wyprodukować *najlepszy* układ graficzny.

Rezultatem była ładnie wyglądającą konsola dla rodziny... i ponad 500 stronicowy podręcznik programisty.

Nie przejmuj się. Obiecuję że ten artykuł nie będzie *taki* długi... Baw się dobrze!

## {.supporting-imagery}

## CPU

Główny procesor Nintendo 64 wywodzi się z układu **MIPS R4000**, nowego awangardowego procesora [MIPS](playstation#tab-1-2-mips-and-sony). Wypuszczone w 1991, największą nowością serii R4000 było dodanie obsługi **64-bitowych instrukcji **, będących wynikiem poszerzenia szerokości szyn, rejestrów i jednostek obliczeniowych, by efektywnie manipulować wartościami 64-bitowymi. Deweloperzy z drugiej strony uzyskali wyżej rzeczone możliwości poprzez nowy zestaw instrukcji **MIPS III**. Ogólnie rzecz biorąc, R4000 umożliwił nowym aplikacjom manipulowanie większymi blokami danych bez wykorzystywania dodatkowych cykli.

W przypadku konsoli nowej generacji Nintendo rozważało wprowadzenie sprzętu przemysłowego na konsole domowe. W przeciwieństwie do firmy [Sony](playstation#cpu), która posiadała wiele własnych komponentów i potrzebowała jedynie drugiego źródła MIPS, Nintendo bezpośrednio współpracowało z właścicielami MIPS (i wieloma graficznymi stacjami roboczymi), aby zaprojektować cały ich ekosystem. Tą firmą było **Silicon Graphics** (SGI).

Wracając do siedziby SGI, R4000 był drogim produktem (około $400 [@cpu-r4000demo]), co czyniło go niepraktycznym w użyciu w konsoli do gier. Jednak Nintendo nie chciało poddawać się, więc użyli niskobudżetowego wariantu zwanego **R4300i**, którego drugim źródłem stał się NEC.

Ostatecznie, wyborem Nintendo i SGI stał się procesor **NEC VR4300** działający z prędkością **93.75 MHz** [@cpu-anatomy]. Jest to binarnie kompatybilna wersja układu MIPS R4300i, który zawiera [@cpu-nec]:

- Posiada dwa tryby działania:
  - **Tryb 32-bitowy**: Tradycyjny tryb, w którym procesor zachowuje się jak procesor z zestawem instrukcji MIPS-II. Nie ma niczego specjalnego w tym trybie z wyjątkiem tego, że wszelkie nowe funkcje są wyłączone.
  - **Tryb 64-bitowy**: Tryb 'natywny' gdzie wszelkie 64-bitowe rozszerzenia są dostępne. Jest też binarnie kompatybilny z 32-bitowymi aplikacjami.
- **32 rejestry ogólnego przeznaczenia**: są 32-bitowe w 'trybie 32-bitowym' i 64-bitowe w 'trybie 64-bitowym'.
- Zbiór instrukcji **MIPS III**: Zestaw instrukcji będący następcą poprzedniego zestawu instrukcji MIPS II. Zawiera nowe kody operacyjne obliczające 64-bitowe słowa zwane 'podwójnymi słowami. Wreszcie, instrukcje są zawsze **32-bitowe**, niezależnie od trybu działania.
  - Warto wspomnieć, że od czasu MIPS II, [miejsca na opóźnienia ładowania](playstation#delay-galore) są przestarzałe, choć nadal występują opóźnienia gałęzi.
- Wewnętrznie **64-bitowa szyna jest** podłączona do **zewnętrznej 32-bitowej magistrali transmisji danych**: chociaż podwójne słowa nie pogorszają wydajności, gdy są obsługiwane wewnętrznie, Procesor nadal będzie musiał zużywać dodatkowe cykle, aby przenieść 64-bitowe dane przez resztę systemu.
  - To jest jedno z cięć wariantu R4300i (R4000 posiada pełną 64-bitową szynę danych).
- **32-bitowa linia adresowa**: Może zaadresować do 4 GB fizycznej pamięci.
- **Potok 5-stopniowy**: Do pięciu instrukcji może być przetwarzanych jednocześnie (szczegółowe objaśnienie można znaleźć w [poprzednim artykule](sega-saturn#cpu)).
- **24 KB pamięci cache L1**: podzielone na 16 KB dla instrukcji i 8 KB dla danych.

Wewnętrzna **Jednostka zmiennoprzecinkowa** (FPU) jest również zawarta w tym pakiecie. VR4300 określa ją jako koprocesor (CP1), jednakże jednostka jest zainstalowana obok ALU i jest dostępna tylko za pośrednictwem wewnętrznego potoku procesora ALU, co oznacza, że nie ma wspólnego przetwarzania per se. Z drugiej strony FPU nadal posiada dedykowany rejestr przyspieszający operacje oparte 32 i 64-bitowych liczbach zmiennoprzecinkowych. Ponadto jednostka ta przestrzega standardu liczb zmiennoprzecinkowych IEEE754.

### Uproszczony dostęp do pamięci

Sposób wykorzystania pamięci RAM jest zgodny z **jednolitą architekturą pamięci** lub 'UMA', gdzie całą dostępna pamięć jest zlokalizowana wyłącznie w jednym miejscu, a każdy komponent, który wymaga pamięci RAM, ma dostęp do tej wspólnej lokalizacji. Komponentem arbitrażowym jest w tym przypadku GPU.

Powodem wybrania tego projektu jest fakt, że pozwala on znaczne zaoszczędzić na kosztach produkcji, ale z drugiej strony zwiększa szanse konfliktu w dostępie do pamięci, gdy nie jest zarządzany właściwie.

### Brak kontrolera DMA?

Ze względu na ujednoliconą architekturę pamięci, CPU nie posiada już bezpośredniego dostępu do pamięci RAM, więc GPU będzie również zapewniać funkcjonalność DMA.

### Projekt pamięci

Oprócz UMA organizacja pamięci RAM jest nieco skomplikowana, więc postaram się utrzymać to prostym do zrozumienia. Oto przedstawiam...

System zawiera **4,5 MB RAM** która jest połączona za pomocą **9-bitowej** linii danych, gdzie dziewiąty bit jest zarezerwowany dla GPU (więcej szczegółów w sekcji "Grafika"). W rezultacie każdy komponent z wyjątkiem GPU może zobaczyć **do 4 MB**.

![Układ pamięci tego systemu. Zakładam, że prędkość magistrali CPU-RCP jest albo prędkością zegara RCP, albo prędkością CPU, ale jeszcze nie mogłem tego potwierdzić.](memory.png)

Typ pamięci RAM zamontowanej w płytce nazywa się **Rambus DRAM** (RDRAM) [@cpu-memory], był to kolejny projekt, który konkurował z SDRAM, aby stać się nowym standardem. RDRAM jest połączony **szeregowo** (gdzie transfery są wykonywane bit po bicie), podczas gdy SDRAM używa **równoległego połączenia** (transfer wielu bitów jednocześnie).

Opóźnienie RDRAM jest wprost proporcjonalne do liczby zainstalowanych banków [@cpu-rdram], więc w tym przypadku, z ilością zainstalowanej pamięci RAM w tym systemie, wynikiem są znaczące opóźnienia (post na forum beyond3d donosi że opóźnienie wynosi około **640 ns** [@cpu-beyondrsp]). Chociaż jest to kompensowane wysoką prędkością zegara pamięci wynoszącą **250 MHz** (~2,6 razy szybciej niż CPU). Nintendo twierdzi, że RDRAM może zapewnić szybki transfer danych do 500 MB/s zapisu i odczytu.

Ponadto toczy się kolejna dyskusja na forum beyond3d, która twierdzi, że Nintendo wybrało moduły pamięci NEC uPD488170L dla swojej konsoli [@cpu-beyondrdram]. Te chipy wdrażają technologię o nazwie "Rambus Signaling Logic", która podwaja szybkość transferu [@cpu-data]. Co może wyjaśniać, dlaczego w niektórych źródłach 'efektywna' prędkość wynosi 500 MHz.

Wreszcie, ilość dostępnego RAM na tej konsoli **może zostać rozszerzona** poprzez zainstalowanie akcesorium *Expansion Pak*: małe ładne pudełko zawierające dodatkowe 4 MB pamięci RAM. Co ciekawe, wolne banki pamięci RAM muszą być obsadzone terminatorami, w efekcie konsola była dostarczana wraz z zainstalowanym terminatorem (zwanym *Jumper Pak*) zainstalowanym w miejsce slotu Expansion Pak. Teraz możesz zapytać, co by się stało, jeśli włączysz konsolę bez zainstalowanego *rozszerzenia*? **Dosłownie nic,** uzyskasz pusty ekran!

### Zarządzanie pamięcią

VR4300 zawiera kolejny koprocesor o nazwie **System Control Coprocessor** (CP0), który składa się z **Jednostki Zarządzania Pamięcią** (MMU) i **Buforu Translacji Stron** (TLB), która decyduje, jak pamięć jest zorganizowana i buforowana. VR4300 może uzyskać dostęp do 32-bitowych adresów pamięci o rozmiarze do 4 GB, ale jak widzieliśmy, nie mamy 4 GB pamięci RAM w tej konsoli (nawet po uwzględnieniu pamięci mapowanej I/O). Tak więc MMU przejmuje adres pamięci i dostarcza użyteczną mapę pamięci, w której pamięć fizyczna jest wielokrotnie duplikowana. W konsekwencji adresy pamięci traktowane są jako 'adresy wirtualne' (w przeciwieństwie do 'adresów fizycznych'). Ponadto TLB umożliwia programistom zdefiniowanie własnej mapy pamięci w niektórych przypadkach bez (znaczących) kar za wydajności.

Początkowo może to wydawać się zbędne, ale każde lusterko (zwane 'segmentem') jest podłączone do innych obwodów (tj. Pamięć podręczna L1, nie cachowana, adres TLB), więc programiści mogą zoptymalizować wykorzystanie poprzez wybór najodpowiedniejszego segmentu w zależności od potrzeb.

Niektóre segmenty mają na celu odseparowanie lokalizacji 'jądra' od lokalizacji 'użytkownika' dla celów bezpieczeństwa. N64 zawsze działa w trybie 'jądra', w związku z czym segment 'jądra spoza TLB' (zwany 'KSEG0') jest najczęstszym segmentem dla gier.

MMU może również działać w trybie 64-bitowym, gdzie adresy pamięci są 40-bitowe. Oznacza to, że wirtualna przestrzeń adresowa obejmuje adresy o wielkości 1 TB... ale myślę, że Nintendo 64 nie wykorzysta tego!

## Grafika

To, co widzisz na ekranie, jest wytwarzane przez ogromny chip, zaprojektowany przez Silicon Graphics zwany **Reality Co-Processor** działający z prędkością **62.5 MHz**. Ten pakiet zawiera *wiele* obwodów, więc nie martw się, jeśli masz trudności ze śledzeniem, podsystem graficzny ma bardzo złożoną strukturę!

Ten projekt opiera się na filozofii, że GPU nie ma być 'prostym' rasteryzatorem jak u [konkurenta](playstation#graphics). Zamiast tego, powinien być również w stanie **przyśpieszyć obliczenia geometrii** (odciążając CPU) a do tego potrzebne będzie więcej obwodów.

### Architektura

Ten chip jest podzielony na trzy główne moduły, z których dwa są używane do przetwarzania grafiki:

#### Reality Signal Processor {.tabs.active}

![Architektura Reality Signal Processor (RSP).](RSP.png) {.tab-float}

Znany również jako **RSP** to tylko inny pakiet CPU składający się z:

- **Scalar Unit**: Inna okrojona pochodna MIPS R4000. Tym razem wdraża on jedynie podzbiór MIPS III ISA, a tym samym brakuje wielu funkcji ogólnego celu (tj. przerwania i wyjątki), 64-bitowe rozszerzenie, mnożenie i podziały.
- **Vector Unit**: koprocesor wykonujący operacje wektorowe posiadający 32 128-bitowe rejestry. Każdy rejestr jest *podzielony* na osiem części, aby obsługiwać jednocześnie osiem 16-bitowych wektorów (podobnie jak instrukcje SIMD na tradycyjnych procesorach). Jak widzisz, ten komponent mocno odciąża jednostkę skalarną.
- **System Control**: Inny koprocesor, który zapewnia funkcjonalność DMA i steruje sąsiednim modułem RDP (więcej o tym później).

Aby korzystać z tego modułu, CPU przechowuje w pamięci RAM serię poleceń o nazwie **Lista wyświetlania** wraz z danymi, które będą manipulowane, następnie RSP odczytuje listę i wykonuje wymagane operacje. Dostępne funkcje obejmują transformacje geometrii (takie jak projekcja perspektywy), przycinanie i oświetlenie.

Wydaje się to proste, ale w jaki sposób wykonuje te operacje? Oto interesująca część: W przeciwieństwie do konkurentów (PS1 i Saturn), **silnik geometrii nie jest stałofunkcyjny**. Zamiast tego, RSP zawiera trochę pamięci (4 KB dla instrukcji i 4 KB dla danych) do przechowywania **mikrokodu** [@audio_video-ultra]: Mały program, z nie więcej niż 1000 instrukcjami, który **wdraża potok graficzny**. Innymi słowy, ukierunkowuje on Scalar Unit, w jaki sposób powinien operować danymi graficznymi. Mikrokod jest dostarczany przez CPU podczas jego pracy.

Nintendo dostarczył różne mikrokody do wyboru z [@audio_video-microcodes] i, podobnie jak [tryby tła SNES-a](super-nintendo#graphics), każdy z nich wykorzystuje zasoby w inny sposób.

#### Reality Display Processor {.tab}

![Architektura Reality Display Processor (RDP).](RDP.png) {.tab-float}

Po zakończeniu przetwarzania danych wielokątów RSP rozpocznie wysyłanie **poleceń rasteryzacji** do następnego modułu, **RDP**, aby narysować klatkę. Te polecenia są wysyłane za pomocą dedykowanej magistrali o nazwie **XBUS** lub za pośrednictwem głównej RAM.

RDP jest kolejnym procesorem (tym razem z ustaloną funkcją), który zawiera wiele silników do rasteryzacji, mapowania tekstur na nasze wielokąty, mieszania kolorów i utworzenie nowej klatki.

Może przetwarzać **trójkąty** lub **prostokąty** jako prymitywy, te ostatnie są przydatne do rysowania sprite'ów. Potok rasteryzacji RDP zawiera następujące bloki:

- **Rasteryzator**: Zamienia prymitywy (złożone z wierzchołków) na piksele.
- **Jednostka teksturująca**: Przetwarza tekstury przy użyciu 4 KB dedykowanej pamięci (nazywanej „TMEM”) pozwalającej na użycie maksymalnie ośmiu kafelków do teksturowania. Może ona wykonywać następujące operacje:
  - **Filtrowanie dwuliniowe**: Mapuje wybraną teksturę 2D do kształtu 3D i wygładza ją, aby uniknąć aliasingu (spowodowanego nadmiernym pobieraniem próbek).
    - Jednakże filtr 'kompletny' wymagałby czterech punktów do przeprowadzenia interpolacji. Ta konsola używa tylko trzech (**interpolacji trójkątnej**) skutkującej pewnymi anomaliami. W związku z tym niektóre teksty będą musiały zostać wcześniej 'dostosowane'.
  - **Mip-Mapping**: Automatycznie wybiera pomniejszoną wersję tekstury w zależności od wymaganego **poziomu szczegółowości**. Pozwala to uniknąć przetwarzania dużych tekstur, które byłyby widziane daleko od kamery i zapobiega aliasingowi (produkt niedostatecznego pobierania próbek).
    - Jeśli włączone, RDP mapuje tekstury używając zamiast tego **filtrowania trójliniowego**. Ten nowy algorytm będzie również interpolował między mipmapami, aby złagodzić nagłe zmiany poziomu szczegółowości.
  - **Korekta perspektywy**: Wybrany algorytm do mapowania tekstur na trójkąty. W odróżnieniu od [innych algorytmów odwzorowujących](playstation#graphics), ten uwzględnia głębokość każdego prymitywu, osiągając przy tym lepsze wyniki.
- **Mieszacz kolorów**: Miesza i interpoluje wiele warstw kolorów (na przykład w celu zastosowania cieni).
- **Blender**: Miesza piksele z bieżącym buforem klatki, aby zastosować przezroczystość, antyaliasy, mgłę i dithering. Wykonuje on również buforowanie głębokości (więcej o tym później więcej).
- **Interfejs pamięci**: Używany przez poprzednie bloki do odczytu i zapisywania bieżącego bufora klatki w pamięci RAM i/lub wypełnienia TMEM.

RDP zapewnia cztery różne tryby działania, każdy tryb łączy te bloki w różny sposób w celu optymalizacji określonych operacji.

Ponieważ ten moduł stale aktualizuje bufor klatki, obsługuje RAM bardzo różnie: Pamiętasz o nietypowym 9-bitowym 'bajcie'? Dziewiąty bit jest używany do obliczeń związanych z buforem klatki (buforowanie głębi i antyaliasing) i może być obsługiwany tylko przez interfejs pamięci.

#### Pozostałe kroki {.tabs-close}

Powstała klatka musi zostać wysłana do **Kodera Wideo**, żeby wyświetlić ją na ekranie (**DMA** i komponent **Interfejsu Wideo** są niezbędne do osiągnięcia tego).

Teoretyczne maksymalne możliwości to 24-bitowa głębokość kolorów (16,8 milionów kolorów) i rozdzielczość 640x480 (lub 720x576 w regionie PAL). Wymieniam to jako 'teoretyczne', ponieważ korzystanie z maksymalnych możliwości może być zasobożerne, programiści będą skłaniać się do korzystania z gorszych trybów, aby uwolnić wystarczającą ilość zasobów dla innych usług.

### Szybka demonstracja

Umieśćmy wszystkie poprzednie wyjaśnienia w perspektywie. Użyję w tym celu *Super Mario 64*, aby pokazać, w skrócie, jak komponowana jest klatka:

#### Przetwarzanie Wierzchołków {.tabs.active}

![Widok naszej sceny złożony z prymitywów. Aby zaoszczędzić ilość wielokątów, niektóre znaki są modelowane za pomocą duszków (quads)](mario/wireframe.jpg) {.tab-float}

Początkowo nasze zasoby (modele 3D itp.) znajdują się w pamięci ROM kartridża, jednak aby utrzymać stałą przepustowość, musimy najpierw skopiować je do pamięci RAM. W niektórych przypadkach można znaleźć dane wstępnie skompresowane w kartridżu, więc procesor musi je dekompresować przed ich użyciem.

Kiedy to zostanie zrobione, nadszedł czas, aby stworzyć scenerię za pomocą naszych modeli. Procesor może samodzielnie przeprowadzić cały potok, ale to może zająć *wieki*, wiele zadań jest więc delegowanych do RCP. CPU zamiast tego wyśle polecenia do RCP. Zadania te są realizowane w następujący sposób:

1. Utwórz Listę Wyświetlania, która zawiera operacje, które mają być wykonane przez RSP i przechowaj je w pamięci RAM.
2. Wskaż RSP, gdzie znajduje się lista wyświetlania.
3. Wyślij mikrokod do RSP, aby uruchomić Scalar Unit.

Później, RSP rozpocznie wykonywanie pierwszej serii zadań, a wynik zostanie wysłany do RDP w formie poleceń rasteryzacji.

#### Przetwarzanie pikseli {.tab}

![Wyrenderowana ramka (_Tada!_).](mario/result.png) {.tab-float}

Dotychczas udało nam się przetworzyć nasze dane i zastosować na nich pewne efekty, ale nadal potrzebujemy:

- Dokonać rasteryzacji, zaaplikować tekstury i inne efekty.
- Wyświetlić bufor klatki.

Jak się możecie domyślać, zadania te są wykonywane przez RDP. Aby to wykonać, tekstury muszą zostać skopiowane z pamięci RAM do TMEM przy użyciu DMA.

RDP ma stały potok, ale możemy wybrać optymalny tryb działania na podstawie bieżącego zadania w celu poprawy ilości klatek na sekundę.

Gdy RDP zakończy przetwarzanie danych, zapisze ostateczną bitmapę do bufora klatki w pamięci RAM. Następnie procesor musi przenieść nową ramkę do **Interfejsu Wideo** (VI), najlepiej za pomocą DMA. Z kolei VI przekaże go do **Enkodera Wideo** w celu wyświetlenia na telewizorze.

### Projekty {.tabs-close}

Oto kilka przykładów poprzednich projektów 2D dla [Super Nintendo](super-nintendo), które zostały przeprojektowane dla nowej ery 3D, są interaktywne, więc zachęcam Cię do ich sprawdzenia!

![The Legend of Zelda: Ocarina of Time (1998).<br>704 wierzchołków.](link_ocarina_64){.toleft model3d="true"}

![Kirby 64: The Crystal Shards (2000).<br>516 wierzchołków.](kirby_cristals_64){.toright model3d="true"}

### Współczesne oznaczanie widocznych wierzchołków

Jeśli przeczytałeś o poprzednich konsolach, napotkałeś niekończący się problem dotyczący [widoczności wierzchołków](sega-saturn#an-introduction-to-the-visibility-problem) i możesz teraz myśleć, że sortowanie wielokątów jest jedynym sposobem na wyjście z tego. Cóż, po raz pierwszy w tej serii, RDP oferuje podejście oparte na sprzęcie o nazwie **buforowanie Z** (ang. Z-buffering). W skrócie RDP przydziela dodatkowy bufor o nazwie **Bufor Z** w pamięci. Ma on takie same wymiary jak bufor klatki, ale zamiast przechowywać wartości RGB, każdy wpis zawiera głębokość (wartość Z) najbliższego piksela względem kamery.

Gdy RDP dokonuje rasteryzacji wektorów, wartość z nowego piksela porównywana jest z odpowiednią wartością w buforze Z. Jeśli nowy piksel zawiera mniejszą wartość 'z' oznacza to, że nowy piksel jest umieszczony przed poprzednim, więc jest nakładany na bufor ramki i z-bufor jest również zaktualizowany. W przeciwnym razie piksel zostaje odrzucony.

Ogółem jest to bardzo pożądane uzupełnienie: Programiści nie muszą już martwić się implementacją [opartych na oprogramowaniu](playstation#tab-4-2-visibility-approach) metod sortowania wielokątów, które zużywają wiele zasobów procesora. Jednakże bufor Z nie zachowa Cię przed używaniem niepotrzebnej geometrii (odrzuconej lub przesadzonej, obie zużywając zasoby). W tym celu silniki gier mogą wybrać dodanie algorytmu **wycinania okluzji** w celu odrzucenia zasłoniętej geometrii tak szybko, jak to możliwe.

### Sekrety i ograniczenia

SGI zainwestowało wiele technologii do tego systemu. Była to jednak konsola przeznaczona dla gospodarstw domowych i jako taka musiała utrzymać swoje koszty na niskim poziomie. Niektóre trudne decyzje doprowadziły do trudności dla programistów:

#### Zastoje Potoku {.tabs.active}

Ze względu na ogromną liczbę komponentów i operacji w potoku graficznym, RCP był w końcu bardzo podatny na **przestoje**: niepożądana sytuacja, w której komponenty pozostają na biegu jałowym przez znaczne okresy, ponieważ wymagane dane są opóźnione z tyłu potoku.

Będzie to zawsze skutkowało degradacją wydajności i będzie należało do zadań programisty unikanie ich. Jednakże żeby ułatwić pracę, niektóre procesory takie jak Scalar Unit implementują funkcję o nazwie **Bypassing**, która umożliwia wykonywanie podobnych instrukcji w szybszym tempie, pomijając niektóre etapy wykonania, które można pominąć.

Na przykład, jeśli musimy obliczyć sekwencyjne instrukcje `ADD`, nie ma potrzeby zapisywania wyników z powrotem do rejestru, a następnie odczytywania go z powrotem za każdym razem, gdy `ADD` zostanie zakończony. Zamiast tego możemy nadal używać tego samego rejestru dla wszystkich sum i wykonać zapis po zakończeniu ostatniego `ADD`.

#### Pamięć tekstur {.tab}

RDP opiera się na 4 KB TMEM (Pamięć Tekstur) jako na pojedynczym źródle ładowania tekstur. Niestety w praktyce 4 KB okazało się niewystarczające dla tekstur o wysokiej rozdzielczości. Ponadto w przypadku stosowania mipmappingu dostępna ilość pamięci zostaje zmniejszona do połowy.

W rezultacie niektóre gry używały pojedynczych kolorów z cieniowaniem Gouraud (jak *Super Mario 64*) a inne opierały się na teksturach wstępnie przeliczonych (np. tam, gdzie trzeba było mieszać wiele warstw).

### Uniwersalne wyjście wideo {.tabs-close}

Nintendo nadal używało 'uniwersalnego' portu [Multi Out](super-nintendo.md#a-convenient-video-out) znalezionego na jego poprzedniku, zła wiadomość jest taka, że **nie wysyła już on sygnału RGB!** Wygląda na to, że jest to kolejny środek pozwalający zaoszczędzić koszty, ponieważ RGB i tak nie było używane w poprzedniej konsoli.

Dobrą wiadomością jest to, że trzy linie nadal mogą zostać zrekonstruowane w pierwszych wersjach poprzez dolutowanie niektórych kabli i zainstalowanie niedrogiego wzmacniacza sygnału. Wynika to z faktu, że cyfrowo-analogowy przetwornik wideo przesyła sygnał RGB do enkodera wideo. Jednak późniejsze wersje płyty głównej połączyły oba chipy, więc jedyną pozostałą opcją jest obejście przetwornika C/A i enkodera razem z niestandardowym obwodem, który eksponuje te sygnały.

## Dźwięk

Zanim przejdziemy do szczegółów, zdefiniujmy dwa punkty końcowe podsystemu audio N64:

- Naszym punktem wyjścia jest pamięć ROM kartridża, zawiera ona dane, które tylko CPU może zinterpretować.
- Punktem końcowym jest **konwerter cyfrowo-analogowy** lub 'C/A', który rozumie tylko *surowe próbki dźwiękowe*.

Jak połączymy oba zakończenia? Konsole zazwyczaj zawierają dedykowany chip, który wykonuje dla nas tę pracę. Niestety, Nintendo 64 **nie posiada takiego dedykowanego chipu**, więc to zadanie jest rozłożone pomiędzy te komponenty:

- **Główny procesor**: przenosi dane audio z ROM gry do RAM, następnie inicjuje **Listy Audio** do użycia przez RSP.
- **RSP**: Używając jeszcze większej liczby mikrokodów, interpretuje listy audio przechowywane wcześniej w pamięci RAM i wykonuje wymagane operacje na danych dźwięku, które, na przykład, mogą obejmować:
  - Dekompresowanie **próbek ADPCM** i stosowanie efektów.
  - Sekwencjonowanie i mieszanie **danych MIDI** przy użyciu **banków audio** przechowywanych w pamięci RAM.

Wynikające z tego dane są, zgodnie z oczekiwaniami, danymi audio. Jest to następnie wysyłane do **Interfejsu Audio** lub bloku 'AI', który następnie przeniesie to do przetwornika cyfrowo-analogowego. Wynikające z tego dane audio zawierają dwa kanały (ponieważ nasz system jest stereo) z 16-bitową rozdzielczością.

![Przegląd sposobu, w jaki potok audio jest często zaprogramowany.](Audio.png)

### Repertuar

Czas na usłyszenie ścieżek dźwiękowych utworzonych dla N64. Jest zbyt wiele (dobrych) tytułów do wspomnienia w tym artykule, więc przedstawiam tu niektóre z nich, które przykuły moją uwagę:

![The Legend of Zelda: Majora's Mask (2000).<br> Muzyka tej gry jest powiązana z jej śmieszną atmosferą.](observatory){.toleft video="true"}

![Bomberman Hero (1998).<br>Ta gra ma ciekawą i unikalną ścieżkę dźwiękową wzorowaną na muzyce house.](redial){.toright video="true"}

### Sekrety i ograniczenia

Ze względu na ten projekt, ograniczenia będą zależeć od implementacji:

- Częstotliwość próbkowania może wynosić do **44,1 kHz**, ale przy użyciu najwyższej szybkości skradnie wiele cykli CPU.
- Nie ma ścisłego limitu w liczbie kanałów, wszystko zależy od zdolności RSP do miksowania ich (często około 16–24 kanałów, jeżeli przetwarza ADPCM lub ~100 w przypadku PCM).
- Pamięć jest kolejnym problemem, podczas gdy konkurenci opierali się na większych nośnikach (tzn. CD-ROM) i dedykowana pamięć dźwiękowa, kartridże Nintendo 64 mieszczą znacznie mniej danych (nie mówiąc o danych dźwiękowych) i muszą dzielić się pamięcią główną z innymi komponentami.

Z tych powodów gracze mogą zauważyć, że porty N64 zawierają muzykę o niższej jakości lub powtarzające się utwory. Chociaż powszechnym obejściem jest wdrożenie sekwencera muzycznego, który w trakcie wykonania 'tworzy' próbki przy użyciu wcześniej wykonanego zestawu dźwięków (podobnie do muzyki MIDI).

## System Operacyjny

Podobnie jak w przypadku gier PS1 i Saturn, gry N64 pisane są bezpośrednio na sprzęt. Nie ma jednak dostępnych procedur BIOS, aby uprościć niektóre operacje. Jako substytut, **gry osadzają mały system operacyjny**, który zapewnia odpowiednią ilość abstrakcji do skutecznej obsługi CPU, GPU i We/Wy.

To nie jest konwencjonalny *desktopów OS* który, możemy sobie wyobrazić na początku, jest to tylko mikrojądro z najmniejszym możliwym śladem, które zapewnia następującą funkcjonalność:

- Wielowątkowość przy użyciu przekazywania komunikatów (nie zapomnij, że CPU jest jednordzeniowy).
- Harmonogram i wywłaszczenie.
- Uproszczone rejestry i dostęp do portów We/Wy.

Ogólnie rzecz biorąc, funkcje te mają kluczowe znaczenie dla organizacji zadań audio, wideo i logiki gry, które muszą działać jednocześnie.

Kernel jest automatycznie osadzony przez użycie bibliotek Nintendo. Co więcej, jeśli programiści zdecydują się nie umieszczać jednej z bibliotek, odpowiednia część jądra jest usuwana, aby uniknąć marnowania miejsca na kartridżu.

### Proces rozruchu

W odróżnieniu od poprzednich systemów opartych na kartridżach Nintendo 64 stosuje zaawansowany proces rozruchu w celu przygotowania całego swojego sprzętu przed rozpoczęciem gry. Wykonywane jest to jak tylko użytkownik włączy konsolę i jest bardzo zbliżone do ówczesnych konsol opartych o CD, które zawierają [BIOS](playstation#operating-system) lub [IPL](sega-saturn#operating-system).

Te procedury są również określane jako **Program Inicjujący** (IPL) i działają w następujący sposób [@operating_system-ipl]:

1. Użytkownik włącza konsolę.
2. **PIF-NUS** (oddzielny chip na płycie głównej) wprowadza główny procesor w nieskończony reset do momentu, gdy PIF-NUS zweryfikuje chip CIC znaleziony w kartridżu gry.
    - PIF-NUS i chip CIC są dogłębniej wyjaśnione odpowiednio w sekcji I/O oraz w sekcji dotyczącej zwalczania piractwa.
2. Jeśli proces weryfikacji zakończył się pomyślnie, CPU rozpoczyna wykonanie pod adresem `0xBFC00000`. Ten adres wskazuje **wewnętrzny ROM** w PIF-NUS, w szczególności pierwszy etap rozruchu o nazwie **IPL1**.
3. IPL1 inicjuje część sprzętu (rejestry CPU, interfejs równoległy i RCP) i kopiuje następny etap (**IPL2**) z wewnętrznego ROM do pamięci RSP w celu szybszego wykonania. Następnie przekierowuje tam wykonywanie instrukcji.
4. IPL2 inicjuje pamięci podręcznej RDRAM i CPU. Następnie kopiuje pierwszy megabajt pamięci ROM gry do RDRAM. Ten blok zawiera następny etap rozruchu o nazwie **IPL3**.
5. IPL3 uruchamia system operacyjny (tj. wirtualną pamięć i wektory wyjątków), ustawia stan programu (tj. wskaźnik stosu) i wreszcie przechodzi do wywołania funkcji startowej gry.

Ponieważ IPL3 znajduje się w kartridżu do gry, nie każdy kartridż z grą zawiera ten sam kod. Przypuszczalnie warianty są skorelowane z innym chipem CIC występującym w kartridżu.

## WE/WY

Jak wiesz teraz, porty We/Wy nie jest bezpośrednio połączone z CPU, więc trzeci moduł RCP (który do tej pory nie wspomniałem) służy jako **interfejs We/Wy**, jest to blok obsługujący komunikację z CPU, kontrolerami, kartridżem do gry i prztwornikami cyfrowo-analogowymi wideo i audio.

### Akcesoria

Kontroler Nintendo 64 obejmuje złącze używane do podłączania akcesoriów. Przykłady akcesoriów komercyjnych obejmują:

- **Controller Pak**: Inny nośnik (podobny do karty pamięci *Sony*) używany do przechowywania danych i udostępniania ich innym konsolom.
- **Rumble Pak**: zawiera mały silnik zapewniający heptyczne wibracje, przydatne do 'zanurzenia' gracza w niektórych grach.

Wszystkie akcesoria podłączone do kontrolera są zarządzane przez **PIF-NUS**, ukryty blok, który również obsługuje bezpieczeństwo. RCP komunikuje się do PIF używając "naprawdę wolnej" (słowa z instrukcji programowania) **szeregowej magistrali**.

## Gry

Nintendo pozostało przy korzystaniu z kartridżów, zamiast przerzucić się na dyski optyczne. W rezultacie gry cieszyły się większą szybkością odczytu (według Nintendo, średnio 5 MB/s), jednocześnie będąc droższymi w produkcji. Największy dostępny rozmiar kartridża to 64 MB.

Wewnątrz kartridżów producenci mogą zamieszczać dodatkową pamięć (w formie *EEPROM*, *Flash* lub *SRAM* z baterią), aby przechowywać zapisy. Mimo że nie jest to ścisły wymóg, ponieważ niektóre akcesoria również mogłyby być wykorzystywane do przechowywania zapisów.

Kartridże komunikują się z RCP przy użyciu dedykowanej 16-bitowej magistrali o nazwie **Parallel Bus** (PBUS) lub 'Parallel Interface' (PI).

### Źródłowy zestaw programistyczny

Ogólnie rzecz biorąc, rozwój odbywał się głównie w **C** i **asemblerze**, który był często wymagany do osiągnięcia lepszych rezultatów. Chociaż widzieliśmy, że ten system zapewnia operacje 64-bitowe, nowe instrukcje były rzadko używane, ponieważ w praktyce 32-bitowe instrukcje były szybsze w wykonywaniu (ponieważ zarówno R4300i, jak i VR4300 mają 32-bitową szynę danych).

Biblioteki w oficjalnym SDK zawierają kilka warstw abstrakcji do kontroli RCP. Przykładowo, struktury C takie jak **Graficzny Interfejs Binarny** lub 'GBI' zostały zaprojektowany tak, aby łatwiej było zestawiać niezbędne listy wyświetlania. To samo dotyczy funkcji audio (ich struktura była nazywana **Binarnym Interfejsem Audio** lub 'ABI').

Pod względem tworzenia mikrokodów, Nintendo dostarczyło już zestaw gotowych mikrokodów do wyboru. Jeśli jednak deweloperzy chcieliby go dostosować, to rzeczywiście będzie to trudne zadanie: Zestaw instrukcji Scalar Unit nie był początkowo udokumentowany, ale później Nintendo zmieniło swoje stanowisko, a SGI w końcu opublikowało pewną dokumentację dla programowania mikrokodów.

Sprzęt używany do rozwoju obejmuje stacje robocze dostarczone przez SGI [@games-devkit], podobnie jak maszyna **Indy**, która miała dodatkową płytę córkę o nazwie **U64**, która zawiera sprzęt oraz interfejsy We/Wy konsoli detalicznej. Narzędzia zostały dostarczone również dla komputerów z systemem Windows [@games-u64].

Inne narzędzia firm trzecich składały się z niestandardowego kartridża z długim kablem podłączonym do stacji roboczej. Kartridż pasujący do detalicznego Nintendo 64, ale zawierał wewnętrzne obwody w celu przekierowania żądań "odczytu" z konsoli do pamięci RAM stacji roboczej. Proces wdrożenia/debugowania jest przeprowadzony poprzez przeniesienie kopii gry do pamięci RAM, a następnie, kiedy konsola zostaje włączona, zaczyna wczytywanie danych stamtąd.

### Alternatywny nośnik

Ponadto PBUS rozgałęzia się do innego złącza u dołu płyty głównej N64. To miało być używane przez jeszcze niewydany **Nintendo 64 Disk Drive** (64DD), jako rodzaj 'dodatkowego piętra', zawierającego zastrzeżony czytnik dysków magnetycznych [@games-dd]. Jego dyskietki zapewniają do 64 MB pojemności. Podczas gdy tylko wydane w Japonii, dysk otworzył drzwi do alternatywnego (i tańszego) medium dystrybucji gier.

![Nintendo 64 Disk Drive [@photography-amos].<br>Wydany 01/12/1999 w Japonii.](64dd/module.png){.open-float .tabs-nested .tab-float .active title="Moduł"}

![64DD podłączony do konsoli [@photography-amos].](64dd/attached.png){.tabs-nested-last title="Przytwierdzony"}

Nośnik magnetyczny jest wolniejszy niż kartridże, z prędkością transferu do 1 MB/s, ale nadal szybsze niż czytnik CD-ROM 4x prędkości. Dyski są dwustronne i działają w systemie 'Stałej Prędkości kątowej' (jak późniejszy [mini DVD](gamecube#medium)). Najmniejszy czytelny obszar nazywany jest 'blokiem' i jest to połowa okręgu współśrodkowego.

Nie ma pamięci buforowej dołączonej do czytnika, więc odczytane bity są przechowywane w RDRAM przed ich wykonaniem. Nintendo dołączyło jednostkę rozbudowy pamięci RAM z 64DD, aby zrekompensować nagłe zapotrzebowanie na większą ilość pamięci RAM (z wyjątkiem standaryzacji rozszerzonego RAM dla wszystkich gier 64DD).

{.close-float}

Ponadto części dysku mogą być ponownie zapisywane, aby umożliwić zapisywanie danych, ilość powierzchni zapisywalnej zależy od rodzaju używanego dysku (Nintendo dostarczyło 7 typów). Po stronie oprogramowania dane gry są zorganizowane w system plików o nazwie 'Multi File System' (MFS) dostarczanym przez Nintendo z ich SDK. Gry mogą uzyskać dostęp do danych dysku za pomocą systemu plików lub blokowania, ta ostatnia opiera się na innej bibliotece o nazwie „Leo” dla funkcji niskiego poziomu.

Dysk trzyma również wewnętrzny ROM (nazywany 'DDROM'), który przechowuje kod N64 wykonywany w bootstrapie dysku i wyświetla animację powitalną. Działa to jako nowy etap IPL dodany do tradycyjnego procesu rozruchu. ROM przechowuje również czcionki (łacińskie i Kanji) oraz niektóre dźwięki. ROM znajduje się tylko w jednostkach detalicznych, ponieważ jednostki rozwojowe opierały się na zewnętrznych programach ładowanych przez zestaw deweloperski.

## Przeciwdziałanie-Piractwu / Blokowanie Regionu

System walki z piractwem jest kontynuacją [SNES CIC](super-nintendo.md#anti-piracy--region-lock). Jak wiesz, wykrywanie, blokowanie regionu i bootlegów jest możliwe dzięki chipowi CIC (który musi być obecny w każdym *autoryzowanym* kartridżu z grą) [@anti_piracy-cic], Nintendo 64 ulepszyło ten system, wymagając od różnych gier posiadania określonego wariantu chipów CIC. Dzięki temu upewnia się, że kartridż nie jest podrobiony lub zawiera klon CIC. PIF przeprowadza kontrole sumy kontrolnej na początku i podczas rozgrywki w celu nadzorowania bieżącego układu CIC zainstalowanego w kartridżu.

Jeśli z jakiegoś powodu PIF uważa, że obecny kartridż jest nieważny, spowoduje to trwałe zamrożenie konsoli.

Blokada regionu została utworzona poprzez nieznaczną zmianę kształtu kartridża pomiędzy różnymi regionami, a w efekcie użytkownik nie mógł fizycznie włożyć gry N64 z innego regionu.

Ogólnie rzecz biorąc, piractwo nie wzbudzało zbyt dużego zaniepokojenia dzięki wykorzystaniu nośnika kartridżowego, chociaż ceny gier były trzy razy wyższe niż ceny oparte na płytach CD.

### Nieużywane porty

Co zabawne, Nintendo pozostawiło jedną furtkę otwartą: **port Disk Drive**.

![64DD podłączony do konsoli [@photography-amos].](v64/attached.png){.open-float .tabs-nested .tab-float .active title="Załączony"}

![Tył V64 [@photography-amos], pokazujący kilka ciekawych wyjść Audio/Wideo.](v64/back.png){.tabs-nested-last title="Tył"}

Kilka przedsiębiorstw odtworzyło strukturę interfejsu w celu opracowania własnego sprzętu, a niektóre z tych produktów stały się przedmiotem piractwa.

Myślę, że warto wspomnieć o **Doctor v64**, to urządzenie ma taki sam kształt jak Disc Drive, ale zamiast tego zawiera dysk CD-ROM.

Rozszerzenie może skopiować zawartość kartridża do płyty CD, jak i w drugą stronę (odczyt plików ROM z płyt CD) jest również możliwy.

{.close-float}

### Emulacja

Kiedy byłem dzieckiem, grałem w kilka gier N64 na maszynie Pentium II przy użyciu emulatora, nie było to *aż tak złe*, ale w późniejszych latach zastanawiałem się *jak to kurka możliwe*, że byłem w stanie z radością emulować złożoną 64-bitową maszynę, podczas gdy mój PC ledwo posiadał wystarczającą ilość pamięci RAM do utrzymania zintegrowanego układu graficznego przy życiu.

Prawda jest, choć emulowanie architektury tej konsoli może być skomplikowane, tak mikrokod daje podpowiedzi na temat tego co konsola próbuje zrobić, i ponieważ emulatory *nie muszą być* dokładne, są w stanie zastosować wystarczającą optymalizację w celu zapewnienia większej wydajności w zamian za wierność emulacji.

Innym powodem jest to 64-bitowa instrukcje są prawie nieużywane przez gry, szybkość emulacji prawie nie zostałaby obniżona, gdy działałaby na 32-bitowej maszynie hosta.

## To wszystko ludziska

![Mój _współdzielony_ N64 w domu przyjaciół.<br> Podczas gdy chciałem konsoli tylko do tego artykułu, mój przyjaciel zawsze chciał posiadać N64 DD, więc kupiliśmy kompletny (ale kosztowny) japoński D64 DD wspólnie, aby uniknąć zbyt dużych wydatków indywidualnych. Następnie zainstalowałem N64RGB, abyśmy mogli podłączyć ją do współczesnego telewizora; a wynikiem jest zadowalająca zabawa (i temat dyskusji!).](n64.jpeg)

Muszę przyznać, że ten artykuł może być najdłuższy, jaki kiedykolwiek napisałem, ale mam nadzieję, że okazał się bardzo przyjemny w czytaniu!

Prawdopodobnie następne kilka dni spędzę na to, aby zmienić niektóre rzeczy na stronie zamiast pisać następny artykuł.

Do następnego razu!  
Rodrigo
