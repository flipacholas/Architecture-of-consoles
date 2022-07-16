---
name: Wii
date: 2020-01-05
subtitle: Nowe metody innowacji
releaseDate: 2006-11-19
generation: 7
published: true
cover: wii
javascript:
  - 'threejs'
top_tabs:
  Model:
    file: international
    caption: "Wii\n<br>Wydane 19.11.2006 w Ameryce, 02.12.2006 w Japonii i 08.12.2006 w Europie"
  Motherboard:
    caption: "Pokazuję wersję 'RVL-CPU-40', wcześniejsze wersje miały znacznie większy proces produkcyjny, a późniejsze usunęły większość WE/WY Gamecube'a.\n<br>NAND Flash jest zamontowany z tyłu"
  Diagram:
    caption: Ważne magistrale danych są oznaczone ich szerokością i prędkością
# SEO
title: Architektura Wii
#Historical
aliases:
  - /projects/consoles/wii/
---

## Szybkie wprowadzenie

Mimo że na Wii brakowało najnowocześniejszej grafiki, którą cieszyli się jej konkurenci, nowe rodzaje sterowania i innowacyjne oprogramowanie dały tej konsoli nowe obszary, którymi można się chwalić.

Tutaj przeanalizujemy każdy aspekt tej konsoli, od znanego już sprzętu po przeoczany system bezpieczeństwa, w tym jego główne wady.

Szybka Uwaga: Niektóre sekcje pokrywają się z częścią poprzedniego artykułu o [Gamecube]({{< ref "gamecube">}}), więc zamiast powtarzać informacje, po prostu umieszczę link do odpowiedniej części artykułu.

---

## Kontrolery nowej generacji

Zacznijmy od omówienia jednego z najbardziej charakterystycznych aspektów tej konsoli: Kontrolerów.

Główne urządzenie to nic innego jak **Pilot Wii** (zwany także 'Wiimote'), gadżet o kształcie podobnym do pilota telewizora, który zawiera wiele rodzajów elementów sterujących wejściem:
- Na początek ma **zestaw fizycznych przycisków**, które są używane jak każdy konwencjonalny kontroler.
- Zawiera również **akcelerometr** do wykrywania zmian orientacji, jest to główny składnik używany do wykrywania ruchu.
- Wreszcie zawiera **kamerę na podczerwień**, która w połączeniu z akcelerometrem i przetwarzaniem Wii może być używana do wskazywania ekranu.
  - Ten czujnik wymaga **Listwy Czujnikowej** (dołączonej do konsoli). Listwa zawiera dwa zestawy diod podczerwieni, które kamera może wykryć, Wii wykorzystuje triangulację do obliczenia pozycji Wiimote'a względem telewizora.

Pilot jest zasilany przez **BCM2042** firmy Broadcom, chip, który zawiera wszystkie niezbędne obwody, aby stać się niezależnym urządzeniem Bluetooth (procesor, RAM, ROM i oczywiście moduł Bluetooth). Chociaż Wiimote jest zaprogramowany do przestrzegania protokołu 'Bluetooth HID', by być identyfikowany jako urządzenie wejściowe, nie jest zgodny ze standardową metodą wymiany danych (być może, aby uniemożliwić używanie w systemach innych niż Wii).

Wreszcie, Wiimote zawiera również **16 KB EEPROM** do przechowywania danych użytkownika oraz **mały głośnik** ograniczony do próbek niskiej jakości (3 kHz 4-bit ADPCM lub 1,5 kHz 8-bit PCM).

### Rozszerzenia

Nintendo dostarczyło ten system z innym kontrolerem, który ma być używany po drugiej stronie, **Nunchuk**, ten ma własny akcelerometr, joystick i dwa przyciski. Jest podłączony do niestandardowego 6-stykowego portu producenta w Wiimote.

{{< figure_img src="controllers/both.jpg" alt="Wiimote i Nunchuk" class="centered-container" >}}
Nunchuk i Wiimote, odpowiednio
{{< /figure_img >}}

Dla tego portu zbudowano również inne akcesoria, z których każde zapewniało inny rodzaj wejścia.

---

## CPU

Po sukcesie Gekko, IBM prawdopodobnie przechwycił ten projekt i przemianował go na '750CL', aby inni producenci mogli go używać. Następnie, gdy Nintendo poprosiło o nowy procesor do użycia z ich nową konsolą, wciąż znaną jako 'Revolution' (stąd prefiks RVL na ich standardowych płytach głównych), IBM i Nintendo zgodziły się na użycie 750CL z zegarem 1,5 raza szybszym od Gekko. Ten procesor jest znany jako **Broadway** i działa z prędkością **729 MHz**.

Po zapoznaniu się z [Gekko]({{< ref "gamecube#cpu" >}}) obawiam się, że nie ma zbyt wielu zmian w nowym procesorze. Może to być jednak zaletą: Developerzy GameCube mogli od razu rozpocząć tworzenie nowych gier Wii dzięki całemu doświadczeniu, które zdobyli z Gekko. Co więcej, fakt, że Broadway działa 1,5 raza szybciej niż pierwotny procesor, pozwoli im dążyć do większej liczby funkcji i lepszej jakości.

### A co z pamięcią?

Ta jest ciekawa, stary układ pamięci Gamecube został przearanżowany i wzbogacony o następujące zmiany:

- Splash (24 MB 1T-SRAM) znajduje się teraz w pakiecie GPU i jest teraz określany jako **MEM1**.
- ARAM-u (16 MB szeregowej pamięci SDRAM) już dawno **nie ma**, jednak...
- Dostępny jest nowy układ pamięci, **MEM2**, który zawiera **64 MB GDDR3 SDRAM** ogólnego przeznaczenia.
  - Ten rodzaj pamięci jest oparty na tradycyjnym systemie DDR2, ale został ulepszony z wyższą przepustowością (~2 razy większą od oryginalnej szybkości transferu) i zmniejszonym zużyciem energii, co jest idealne dla GPU.

### Kompatybilność wsteczna

(Na razie) Możesz myśleć o tej konsoli jako o nadzbiorze GameCube i jako taka, kompatybilność z poprzednią generacją gier jest naturalnie dziedziczona. Biorąc to pod uwagę, aby uczynić Wii *w pełni* wstecznie kompatybilnym, stary zestaw portów zewnętrznych został przeniesiony na Wii - porty kontrolerów GameCube i kart pamięci. Istnieje jednak nowe ograniczenie: nowa mapa pamięci jest niekompatybilna ze starą. Więc w oprogramowaniu została zaimplementowana cienka warstwa 'emulacji' (więcej szczegółów w sekcji 'WE/WY' i 'System operacyjny').

Jeśli chodzi o akcesoria GameCube korzystające z gniazda Serial/Hi-Speed, obawiam się, że Wii nie zawierało tych portów, więc te akcesoria nie mogą być tutaj używane.

W późniejszych latach nowe wersje Wii niestety pozbyły się ich.

---

## Grafika

Nowy procesor graficzny nazywa się **Hollywood**. Nadal wykonuje te same zadania, które [Flipper]({{< ref "gamecube#graphics" >}}) wykonywał dawniej, ale cieszy się 1,5x szybszym zegarem (**243 MHz**). Ten wzrost oznacza, że więcej geometrii i efektów może być przetwarzanych w tej samej jednostce czasu.

### Funkcjonalność

Silnikiem 3D nadal jest [Flipper]({{< ref "gamecube#graphics" >}}), ale teraz nazywa się **GX**. Więc zamiast powtarzać przegląd potoku, wspomnę o kilku interesujących zmianach projektowych, które musiały przejść gry:

{{< tabs >}}

{{< tab active="true" name="Standardowy Widescreen" >}}

{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_img name="Tradycyjny" active="true" src="mario/4_3.png" alt="Mario Galaxy" >}}
Tryb 4:3
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Szeroki" src="mario/16_9.png" alt="Widescreen Mario Galaxy" >}}
Tryb 16:9, skomponowany przez enkoder wideo
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Rozciągnięty" src="mario/16_9_stretched.png" alt="Widescreen Rozciągnięty Mario Galaxy" >}}
Tryb 16:9 wyświetlany na telewizorze widescreen
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Super Mario Galaxy (2007){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
W grach Gamecube brakowało odpowiedniego wsparcia dla wyświetlaczy widescreen (czyli komponowania klatek 16:9, odchodząc od tradycyjnego 4:3). Niemniej jednak Flipper był już w stanie to zrobić, a kilka gier zapewniało opcje jego aktywacji, chociaż nadal było to uważane za ekskluzywną funkcję.

Prawdę mówiąc, enkoder wideo zawsze wysyła ramkę zgodną z PAL lub NTSC, więc ta funkcja 'widescreen' działa poprzez rysowanie szerszego wewnętrznego bufora klatek i pozwalanie enkoderowi wideo na zgniecenie go, aby zmieścił się w sygnale 480i lub 576i. Następnie telewizor 16:9 rozciągnie obraz, aby ostateczny wynik mógł wyglądać *mniej więcej* we właściwych proporcjach. Ta technika nie jest nowa, została wykorzystana w projekcji filmowej i jest określana jako **anamorficzny widescreen**.

Wracając do sedna, Wii ustandaryzowało tę funkcję, umożliwiając aktywację 'trybu widescreen' w ustawieniach systemowych, co z kolei promowało jego _szerokie_ przyjęcie (zamierzona gra słów).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Interakcja z Ekranem" >}}
{{< figure_img float="true" src="mario/mario_galaxy.png" alt="Super Mario Galaxy" >}}
Super Mario Galaxy (2007)  
Możesz podnieść gwiazdy, wskazując na nie
{{< /figure_img >}}

{{% inner_markdown %}}
Nowy i śmiały projekt kontrolera oznaczał nowe rodzaje interakcji w grach Wii. Ponieważ WiiMote umożliwiał użytkownikom wskazywanie ekranu, niektóre gry, takie jak *Super Mario Galaxy* lub *The Legend of Zelda: Twilight Princess* użyło tej funkcji, aby umożliwić graczowi interakcję ze scenerią.

W raporcie zatytułowanym _Myth Debugging: Is the Wii More Demanding to Emulate than the GameCube?_, developerzy emulatora Dolphin wyjaśniają, że gry takie jak Super Mario Galaxy i inne pierwszoosobowe strzelanki polegają na [wbudowanym buforze-Z]({{< ref "gamecube#tab-1-4-render" >}}), aby zidentyfikować obiekt, na który wskazuje Wiimote i/lub sprawdzić, jak daleko obiekt znajduje się od kursora Wiimote.

Nie jest to nowa funkcja per se, ale nowatorskie wykorzystanie obecnych możliwości. Gry Gamecube nie były zależne od wielofunkcyjnego kontrolera z funkcją wskaźnika. Teraz gracze mogą sterować postacią i jednocześnie wskazywać na ekran.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Zaktualizowane Design'y

Dodatkowa szybkość Broadwaya i Hollywood w połączeniu z awangardowymi projektami przyniosła pewne ulepszenia modeli postaci. Może nie są tak znaczące jak w innych generacjach, ale nadal są zauważalne i doceniane.

{{< side_by_side >}}
  {{< threejs_canvas model="mario_melee" class="toleft" >}}
Super Smash Bros. Melee (2001) dla GC  
2,494 trójkąty
  {{< /threejs_canvas >}}

  {{< threejs_canvas model="mario_brawl" class="toright" >}}
Super Smash Bros Brawl (2008) dla Wii  
3,049 trójkątów
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

{{< side_by_side >}}
  {{< threejs_canvas model="sonic_dx_tails" class="toleft" >}}
Sonic Adventure DX (2003) dla Gamecube  
1,985 trójkątów
  {{< /threejs_canvas >}}

  {{< threejs_canvas model="tails_brawl" class="toright" >}}
Super Smash Bros Brawl (2008) dla Wii  
3644 trójkąty
  {{< /threejs_canvas >}}
{{< /side_by_side >}}

### Sygnał Wideo

Co zaskakujące, ta konsola nie używa już starego portu [Multi Out]({{< ref "super-nintendo.md#a-convenient-video-out" >}}), ale jego odmianę o nazwie **AV Multi Out** (to tyle jeśli chodzi o nazwę) o nieco innym kształcie. Ten przenosi wszystkie poprzednie sygnały plus **YPbPr** (znany jako 'komponent'). Zawiera również niektóre linie danych, których system używa do identyfikacji typu podłączonego kabla.

Niestety, to medium dziedziczy te same [ograniczenia]({{< ref "gamecube.md#connections" >}}) co Gamecube. Oznacza to brak S-Video w systemach PAL i brak RGB w systemach NTSC. Ponadto RGB może nadawać tylko sygnały z przeplotem (bez progresywnego). Z drugiej strony Nintendo w końcu dostarczyło kabel SCART (jako dodatkowe akcesorium), który w końcu wykorzystuje linie RGB (pamiętaj, że zostały one zignorowane od czasów [SNES]({{< ref "super-nintendo#a-convenient-video-out" >}})).

---

## Dźwięk

Wii zawiera ten sam **DSP Macronix**, który znajduje się w [Gamecube]({{< ref "gamecube#audio" >}}), możesz rzucić okiem na ten artykuł w celu szczegółowej analizy.

W porównaniu z Gamecube jedyną poważną zmianą jest to, że skoro ARAM zniknął, MEM1 lub MEM2 mogą być używane jako bufor audio.

---

## WE/WY

Podsystem WE/WY tej konsoli naprawdę zmienia zasady gry (*wybaczcie grę słów*). Interfejsy są teraz kontrolowane przez jeden moduł, który będzie również dbał o bezpieczeństwo. Mówię tu o **Starlet**.

### Ukryty koprocesor

Starlet to po prostu procesor **ARM926EJ-S** podłączony do większości wewnętrznych komponentów tej konsoli. Znajduje się w Hollywood, działa z częstotliwością **243 MHz** (tak samo jak Hollywood) i zawiera również własny ROM i RAM. W ten sposób możesz uznać Starlet za niezależny komputer działający obok głównego procesora.

Rdzeń jest podobny do tego używanego w [Nintendo DS]({{< ref "nintendo-ds" >}}), z wyjątkiem dwóch 'specjalnych' dodatków:
- Litery 'J' w nazwie modelu, która oznacza dołączenie **Jazelle**: dedykowanej jednostki wykonującej 8-bitowy kod bajtowy Java. Programy Java nadal będą zależne od maszyny wirtualnej (znanej jako 'JVM'), ale niektóre kody operacyjne mogą być wykonywane bezpośrednio z procesora. Ogólnie rzecz biorąc, może to przyspieszyć wykonanie skompilowanego kodu Java.
- Dedykowanej **Jednostki Zarządzania Pamięcią** (MMU) umożliwiającej korzystanie z pamięci wirtualnej. Przydatne dla systemów operacyjnych ogólnego przeznaczenia.

Te ulepszenia są nieco 'dziwne', ponieważ są całkowicie nieużywane na Wii. Niemniej jednak Nintendo wybrało ten rdzeń dla Starlet. Przypomina mi to pierwszego iPhone'a (2G), który również zawierał procesor ARM z Jazelle (również zupełnie nieużywany).

Jeśli zastanawiasz się, Jazelle nigdy się nie przyjęło. Po kilku iteracjach odkryto, że Java Bytecode po prostu działa lepiej na oprogramowaniu. Później ARM zastąpił Jazelle wprowadzając "Thumb-2EE”, a w momencie pisania (czerwiec 2021 r.) obie te jednostki zostały wycofane.

{{< float_group >}}
{{< figure_img_distributed float="true" src1="console/front.jpeg" alt1="Przód Wii" src2="console/back.jpeg" alt2="Tył Wii" >}} Zewnętrzne WE/WY na Wii  
Ciemne & małe przednie gniazdo to czytnik kart SD
{{< /figure_img_distributed >}}

{{% inner_markdown %}}
Idąc dalej, ten 'procesor WE/WY' ma za zadanie rozstrzygać dostęp między wieloma WE/WY i Broadwayem, a tym samym dba o bezpieczeństwo (które decyduje, czy zezwolić na dostęp, czy nie). Jest to szczególnie ważne, jeśli chodzi na przykład o przyznanie dostępu do pamięci NAND, w której przechowywany jest główny system operacyjny i dane użytkownika.

Chip dziedziczy również niektóre technologie z ARM, takie jak **Advanced Microcontroller Bus Architecture** (AMBA), protokół, który ułatwia komunikację między urządzeniami za pomocą zestawu wyspecjalizowanych magistral.
{{% /inner_markdown %}}

{{< /float_group >}}

Powiedziawszy to, Nintendo podłączyło WE/WY w sposób, który wykorzystuje dwie magistrale AMBA:
- **Magistrala AHB** (Magistrala o Wysokiej Wydajności AMBA [ang. AMBA High-performance Bus]): Jak sama nazwa wskazuje, jest przeznaczona do szybkiej komunikacji. Tutaj znajdujemy:
  - Interfejs **NAND**: Uzyskuje dostęp do 512 MB NAND Flash, która przechowuje system operacyjny i dane użytkownika.
  - Dwa interfejsy **Secure Digital Input Output** (SDIO): SDIO to protokół przeznaczony głównie do uzyskiwania dostępu do karty SD, ale w tym przypadku drugi służy do sterowania modułem Wi-Fi (802.11 b/g).
  - Kontroler **USB 2.0**: Obsługuje dwa zewnętrzne gniazda USB i wewnętrzną kartę Bluetooth 2.0.
  - Moduł **SHA-1** i **AES**: Zarezerwowany do zadań związanych z bezpieczeństwem (więcej szczegółów w sekcji 'Przeciwdziałanie-Piractwu').
- **Magistrala APB** (Zaawansowana Magistrala Peryferyjna [ang. Advanced Peripheral Bus]): Ta jest ograniczona do komponentów o niskiej wydajności, w tym:
  - **Interfejsu stacji dysków**: Podłącza czytnik dysków.
  - **Interfejsu szeregowego**: Łączy kontrolery Gamecube.
  - **Interfejsu zewnętrznego** \[ang. External Interface\] (EXI): Widzieliśmy już ten interfejs [wcześniej]({{< ref "gamecube#internal-io" >}}). Komunikuje się z innym sprzętem Gamecube, i jest używany do wstecznej kompatybilności.

### Utrzymanie kompatybilności

{{< float_group >}}
{{< figure_img float="true" src="gamecube-mode.png" alt="Wii ze sprzętem Gamecube" >}}
Wii z osprzętem Gamecube'a
{{< /figure_img >}}

{{% inner_markdown %}}
Wii zachowuje pełną wsteczną kompatybilność z grami Gamecube, mimo że system WE/WY uległ drastycznej zmianie. Dzieje się tak, ponieważ Starlet można przeprogramować, gdy uruchamiana jest gra Gamecube, aby wirtualnie ponownie mapować WE/WY na takie, jakiego spodziewałby się oryginalny Gamecube.

Dodatkowo układ zegara czasu rzeczywistego zawiera zapasową pamięć ROM, która przechowuje czcionki bitmapowe (zestaw Łaciński i Japoński) używane przez gry Gamecube; i SRAM do zapisywania [ustawień związanych z IPL]({{< ref "gamecube#operating-system" >}}).
{{% /inner_markdown %}}

{{< /float_group >}}

---

## System Operacyjny

Ogólnie rzecz biorąc, w Wii znajdują się **dwa systemy operacyjne**. Jeden wykonywany jest w Broadway'u (głównym CPU), a drugi w Starlet (CPU WE/WY). Oba znajdują się w tych 512 MB NAND i mogą być aktualizowane.

### System operacyjny Starlet

Starlet sam w sobie to już ciekawy sprzęt, ale jego oprogramowanie jest jeszcze bardziej intrygujące. Widzisz, ten system operacyjny nie tylko ma pełny dostęp do każdego zakątka konsoli, ale jest także pierwszą rzeczą uruchamianą na konsoli po wciśnięciu przycisku zasilania.

Starlet uruchamia system *nieoficjalnie* określany jako **System Operacyjny Wejścia/Wyjścia** [ang. Input/Output Operating System] lub 'IOS' (proszę, nie myl tego z iOS firmy Apple). IOS to w pełni funkcjonalny system operacyjny składający się z:
  - **Mikrojądra**: Kontroluje procesor ARM9, wykonuje **procesy** i komunikuje się z innym sprzętem przy użyciu **sterowników**.
  - **Sterowników**: Umożliwiają komunikację ze sprzętem znajdującym się poza procesorem (WE/WY).
  - **Procesów**: Wykonują zadanie, takie jak zarządzanie siecią lub wdrażanie systemu plików.
  - **Rdzenia kryptograficznego**: Przyspiesza operacje związane z szyfrowaniem (**Tylko AES i SHA-1**).

Mając to na uwadze, **głównym zadaniem IOS jest odciążenie głównego procesora** poprzez abstrakcję WE/WY i bezpieczeństwa. Z tego powodu programiści nie muszą się nimi martwić. Aby to osiągnąć, Starlet **rezerwuje od 12 do 16 MB** RAM GDDR3 dla swoich zadań, reszta jest używana przez Broadway i GX.

Broadway i Starlet komunikują się ze sobą za pomocą **Komunikacji Międzyprocesowej** lub protokołu 'IPC': W skrócie, oba procesory dzielą po dwa rejestry. Jeden CPU może zapisywać w rejestrach drugiego (zapisane dane mogą reprezentować polecenie lub wartość), a stamtąd CPU odbiorca może wykonać funkcję w odpowiedzi.

System aktualizacji IOS **jest nieco skomplikowany**: Zaktualizowane wersje IOS nie są instalowane na starych, ale w innym gnieździe (zarezerwowany obszar w NAND dla IOS jest podzielony na 'gniazda'). Jest to wyłącznie **ze względu na kompatybilność**, ponieważ pozwala starszemu oprogramowaniu Wii na używanie tej samej wersji IOS, dla której zostało opracowane.

Nintendo często publikowało aktualizacje IOS w celu poprawy obsługi sprzętu (co było konieczne, gdy dostarczono nowe akcesorium). Jest tylko **jeden wyjątek**, gdy aktualizacje IOS faktycznie zastępują starsze: Gdy wykryto, że konkretna wersja ma lukę, którą można wykorzystać. Robione to było tylko ze względów bezpieczeństwa.

Po włożeniu gry Gamecube dzieje się coś innego: Startlet zamiast tego uruchamia **MIOS**. Ten wariant IOS po prostu nakazuje Starletowi emulację oryginalnego [IPL]({{< ref "gamecube#operating-system" >}}).

### System operacyjny Broadway

Ten jest powszechnie znany jako **Menu Systemowe** i działa na głównym procesorze PowerPC (**Broadway**).

{{< float_group >}}

{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Menu" active="true" src="system/home.png" alt="Ekran głównego Menu Systemowego" >}}
Menu systemowe z _wieloma_ zainstalowanymi kanałami
  {{< /figure_img >}}
  {{< tab_figure_img name="Ustawienia" src="system/settings.png" alt="Ustawienia Wii" >}}
Menu ustawień służące do zmiany ustawień
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Tablica Kontaktowa" src="system/mail.png" alt="Tablica Kontaktowa Wii" >}}
Tablica kontaktowa przechowuje listy pogrupowane według daty
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
W porównaniu z IOS nie uważałbym tego za 'w pełni rozwinięty' system operacyjny, ale bardziej za 'program', który pozwala użytkownikowi wykonywać następujące operacje:

- **Uruchom grę Wii/Gamecube**: Tylko wtedy, gdy włożona jest prawidłowa gra.
- **Zmień ustawienia konsoli**: W tym między innymi godzinę, datę, tryb wideo lub lokalizację czujnika.
- **Uruchom aplikacje**: Jedną z nowości tej konsoli jest możliwość instalacji niewielkich gier Wii (tzw. 'WiiWare'), gier retro (gry 'Virtual Console') lub po prostu wygodnych aplikacji (takich jak przeglądarka internetowa). Nintendo nazwało je **kanałami**, ale w systemie operacyjnym są one również określane jako **tytuły**.
  - Użytkownicy mogą pobierać/kupować kanały za pośrednictwem wstępnie zainstalowanego kanału o nazwie **Kanał Sklepowy Wii**.
  - Tytuły Virtual Console zawierały emulator do samodzielnego uruchamiania gry. Co ciekawe, emulator nie jest współdzielony w systemie ani nawet pomiędzy grami z tej samej platformy. Pozwala to zoptymalizować emulator dla określonych gier.
- **Wysyłaj/Odbieraj wiadomości**: Wii mają unikalny identyfikator (wypalony w ich chipie SEEPROM), który można udostępniać w celu wymiany wiadomości między innymi Wii. Wiadomości można zobaczyć na **Tablicy Kontaktowej**.
  - Gry Nintendo i Wii również używały tego medium do dostarczania biuletynu.
{{% /inner_markdown %}}

{{< /float_group >}}

Podobnie jak IOS, Nintendo również wydało wiele aktualizacji tego systemu. Niektóre naprawiły błędy bezpieczeństwa, inne dodały więcej funkcji. Godną uwagi nową funkcją była możliwość przechowywania kanałów na karcie SD.

Każdy program działający na Broadwayu (w tym Menu Systemowe) działa w oparciu o określoną wersję IOS. Po uruchomieniu gry lub kanału Starlet uruchamia się ponownie, używając zadeklarowanej wymaganej wersji IOS.

### Nośnik aktualizacji

Nintendo nazywa je **aktualizacjami systemowymi (ang. System updates)**. Zawierają one oba systemy w tym samym pakiecie i używają numerów porządkowych do oznaczania wersji. Ostatnia znana wersja to `4.3E`, wydana w czerwcu 2010 r.

Pakiety aktualizacji systemu można pobrać z serwerów Nintendo lub dysków z grami. Użytkownicy mogą ręcznie sprawdzać dostępność aktualizacji za pomocą Menu Systemowego. Aktualizacje są wymuszane, jeśli gra wymaga określonej wersji systemu IOS, która nie jest zainstalowana (a płyta zawiera wymagane pakiety).

### Sekwencja rozruchowa

Do tej pory omówiliśmy dwa bardzo różne systemy operacyjne, które znajdują się w tej konsoli i działają jednocześnie. Wydaje się to dość proste, chociaż oba muszą być starannie skoordynowane podczas uruchamiania konsoli, aby później działały poprawnie.

Biorąc to pod uwagę, proces uruchamiania tej konsoli wygląda następująco:

1. Użytkownik naciska przycisk ON na konsoli.
2. Etap **Boot0**: Starlet uruchamia program znajdujący się we wbudowanym Mask ROM-ie (1,5 KB).
    - Krótko mówiąc, Starlet odszyfrowuje i sprawdza integralność pierwszych 96 KB NAND, następnie oblicza swój hash i porównuje go z zapisanym hashem znalezionym we wbudowanej pamięci OTP Starlet. Jeśli oba hashe nie pasują, konsola jest indukowana w nieskończoną pętlę.
3. Etap **Boot1**: Starlet uruchamia mały program znaleziony we wspomnianym 96 KB NAND.
    - Ten program po prostu poinstruuje Starlet, aby zainicjowała (wyczyściła) 64 MB pamięci RAM GDDR3, a następnie odszyfrowała i zweryfikowała resztę NAND.
4. Etap **Boot2**: Starlet ładuje początkowy system IOS (potrzebny do Menu Systemowego), a następnie uruchamia Broadway.
5. Broadway uruchamia Menu Systemowe. Użytkownik ma teraz kontrolę.

---

## Gry

Chociaż nowe gry nie zawsze przynosiły znaczne skoki graficzne, zaskakiwały użytkowników liczbą funkcji, które mogą teraz zaoferować. Stało się tak dzięki nowym usługom dostarczonym przez Nintendo wraz z premierą konsoli, począwszy od nowego zestawu elementów sterujących, a skończywszy na standardowej infrastrukturze online (WiiConnect24), która umożliwiała *darmowe* granie online.

### Nośnik danych

Gry Wii są dystrybuowane przy użyciu zastrzeżonego formatu dysku o nazwie **Wii Optical Disc** (wiem, nazwa nie mogła być bardziej oczywista). W każdym razie Matsushita/Panasonic zaprojektował ten format w oparciu o tradycyjną płytę DVD, dodając niestandardowe funkcje, takie jak **burst cutting area** na wewnętrznej części dysku, aby zapobiec nieautoryzowanym reprodukcjom.

{{< float_group >}}

{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Fizyczna" active="true" src="box_case.jpeg" alt="Standardowe pudełko do gry" >}}
Standardowe gry są prezentowane w fizycznym pudełku i na dysku
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Cyfrowe" src="system/store.png" alt="Kanał sklepowy" >}}
Małe gry (WiiWare) i emulowane gry (Virtual Console) można kupować i pobierać przez Kanał Sklepowy
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
Dysk Wii zapewnia **4,7 GB** (w przypadku jednowarstwowego) lub **8,54 GB** (w przypadku dwuwarstwowego) wolnego miejsca. Często zawierają **dwie partycje**: Pierwszą na aktualizacje systemu, a drugą na rzeczywistą grę.

Niektóre gry, takie jak *Super Smash Bros Brawl*, zawierały więcej partycji do przechowywania wielu gier Virtual Console, które były uruchamiane w głównej grze.
{{% /inner_markdown %}}

{{< /float_group >}}

### Tworzenie

Zgodnie z tradycją, Nintendo dostarczyło zestaw deweloperski. Ten nazywał się **NDEV** i miał kształt powiększonej czarnej _cegły_. NDEV jest dostarczany z rozszerzonym WE/WY i dwukrotnie większą ilością MEM2 (łącznie 128 MB) do celów debugowania.

Oficjalny pakiet oprogramowania nazywał się **Revolution SDK** i zawierał różne narzędzia, kompilatory, debuggery i frameworki do wykonywania prac programistycznych (głównie w ** C/C++**). Nintendo rozpowszechniało kolejne aktualizacje za pośrednictwem portalu internetowego o nazwie **Warioworld.com** (teraz offline/przekierowanego), do którego dostęp mieli tylko zatwierdzeni programiści.

Oficjalny SDK opiera się na wywołaniach IOS do interakcji ze sprzętem Wii, dlatego aktualizacje IOS są często skorelowane z aktualizacjami SDK.

### Powrót do Home

Biorąc pod uwagę wszystkie ulepszenia oprogramowania tej konsoli, może Cię zaskoczyć, że gry **wciąż działają na gołym metalu**, co oznacza, że mają pełną kontrolę nad Broadwayem, ale nie Starlet (stąd zaimplementowane są tutaj mechanizmy bezpieczeństwa). Nie trzeba dodawać, że zachowanie gry nadal podlega zatwierdzeniu przez Nintendo przed dystrybucją.

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Menu" active="true" src="ingame/home.png" alt="Menu home" >}}
Menu home pokazujące się w grze
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Przypomnienie" src="ingame/strap_warning.png" alt="Ostrzeżenie dotyczące Paska Wii" >}}
Ten ekran również musi być uwzględniony
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
Powiedziawszy to, istnieją pewne funkcje w różnych grach, które w jakiś sposób wyglądają strasznie identycznie. Czy pamiętasz na przykład słynne **Menu HOME**? Naciśnięcie przycisku 'HOME' na WiiMote uruchomi wyskakujące okienko w grze, umożliwiając użytkownikowi powrót do menu systemowego bez konieczności ponownego uruchamiania konsoli. Biorąc pod uwagę, że system operacyjny nie zapewnia tej funkcji, w jaki sposób wszystkim udało się stworzyć ten sam interfejs graficzny?

Odpowiedź jest prosta, Nintendo zawarło w swoim SDK kilka **obowiązkowych bibliotek**, które gry muszą zawierać. *Okazuje się*, że jedna z nich rysuje ten ekran. Co więcej, jest to powód dla którego, jak się przekonasz, tylko aplikacje homebrew zawierają 'oryginalne' projekty menu home.
{{% /inner_markdown %}}

{{< /float_group >}}

Oficjalne menu HOME jest jednym z około 200 wymagań, które gry musiały spełnić, zgodnie z dokumentem **Wii Programming Guidelines** (znajdującym się w oficjalnym SDK). Inne wymagania polegały na wyświetleniu ekranu 'Przypomnienie o Pasku Wii' (który jest tylko obrazem BMP) na początku gry, po którym następowała kolejna reguła, która dyktowała sposób interakcji z nim.

### Spersonalizowane tytuły

{{< float_group >}}
{{< tabs nested="true" float="true" >}}
  {{< tab_figure_img name="Edytor" active="true" src="system/mii-editor.png" alt="Edytor Mii" >}}
Kanał Mii pozwala bawić się własnym 'Mii'...
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Gra" src="ingame/music.png" alt="Wii Music" >}}
...które następnie pojawią się w Twoich grach  
Wii Music (2008)
  {{< /tab_figure_img >}}
{{< /tabs >}}

{{% inner_markdown %}}
Kolejną nową funkcją, na którą chciałbym zwrócić uwagę, jest wprowadzenie **Mii**, pewnego rodzaju awatarów, które użytkownicy mogą tworzyć za pomocą dedykowanego kanału o nazwie *Kanał Mii*.

Ale zabawa na tym się nie skończyła, ponieważ gry mogły również pobierać te nowo stworzone Mii, aby spersonalizować rozgrywkę.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## Przeciwdziałanie-Piractwu & Homebrew

Myślę, że liczba funkcji oferowanych przez tę konsolę czyniła ją bardzo atrakcyjną dla hakerów, ponieważ złamanie systemu bezpieczeństwa pozwoliłoby twórcom homebrew na zdobycie możliwości konsoli bez konieczności przechodzenia przez kontrole Nintendo. Tak czy inaczej, Wii skończyło z fantastyczną biblioteką Homebrew.

### Ochrona przed kopiowaniem

Zacznijmy od pospolitej ofiary: **Napędu dyskowego**.

Dyski Wii zawierają wyżej wspomniany obszar 'burst cutting', który jest niedostępny dla konwencjonalnych czytników. Tak więc w przypadku braku tego sterownik zawsze odmówi przeczytania treści.

{{< float_group >}}
{{< figure_img float="true" src="system/disc.png" alt="Ekran włóż płytę" >}}
Napęd dysków nie pozwoli nikomu przejść przez ten ekran, dopóki nie zostanie włożony prawidłowy dysk
{{< /figure_img >}}

{{% inner_markdown %}}
Deweloperzy Modchipa odkryli, że napęd zawiera interfejs debugowania o nazwie 'Serial Writer', chociaż ten port jest zablokowany do momentu wprowadzenia *tajnego klucza*. Jednak znalezienie klucza było kwestią czasu. Gdy to się stało, modderzy byli w stanie wyłączyć ochronę przed kopiowaniem, a następnie opracowali modchip, który zautomatyzował ten proces.

Matsushita opublikowało kolejne wersje tego napędu, blokujące interfejs debugowania, jednak odkryto inne wady czytnika, aby ponownie go włączyć.
{{% /inner_markdown %}}

{{< /float_group >}}

Warto wspomnieć, że główną zaletą modchipów było zwykłe piractwo, ponieważ zawartość dysku jest nadal zaszyfrowana, więc do uruchomienia niestandardowego kodu potrzeba było więcej badań i narzędzi.

Z drugiej strony, Gamecube homebrew był już możliwy do wykonania, wykorzystując [poprzednie wykryte exploity]({{< ref "gamecube#tab-5-3-honourable-mention" >}}) na poprzedniku.

### Szyfrowanie systemu

Jest to prawdopodobnie najbardziej złożona sekcja tej konsoli, ale jej nieustające badania otworzyły drzwi wielu utalentowanym programistom i niesamowitym programom.

Wewnętrzne zabezpieczania Wii były zaprojektowane wokół kilku szyfrów kryptograficznych (AES, RSA, ECC, SHA-1 i HMAC). Aby wyjaśnienia były łatwe do zrozumienia, przyjrzyjmy się każdej grupie z osobna:

{{< tabs >}}
{{< tab name="Współdzielone szyfrowanie" active="true" >}}

{{% inner_markdown %}}
Komunikacja między wieloma komponentami (NAND, dyskiem z grą i kartą SD) jest szyfrowana, aby uniknąć manipulacji. Nintendo wybrało system **klucza symetrycznego**, aby ją chronić, co oznacza, że Wii używa tego samego klucza do szyfrowania i odszyfrowywania swoich danych.

Starlet ma trzy klucze AES 128-bitowe przechowywane w swojej pamięci OTP, które są zapisywane raz podczas produkcji:

- **Klucz Wspólny**: Globalny klucz generowany przez Nintendo, który można znaleźć na wszystkich Wii. Służy do odszyfrowywania pierwszej warstwy szyfrowania używanej w kanałach i grach na dyskach (od teraz będę się do nich odnosić jako **tytuły**).
- **Klucz SD**: Ten służy do szyfrowania/odszyfrowywania danych przesyłanych na kartę SD i tylko menu systemowe może wykonywać te transfery.
  - Nintendo przechowywało kopię tego klucza w systemie IOS bez wyraźnego powodu.
- **Klucz NAND**: Ten klucz jest generowany losowo podczas procesu produkcyjnego (co oznacza, że jest unikalny dla każdego Wii) i służy do ochrony chipa NAND.

Dzięki temu widzimy, że Starlet odpowiada za szyfrowanie/odszyfrowywanie sensownych treści, dlatego ten procesor jest jedynym, który ma dostęp do poufnych danych.

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Łańcuch zaufania" >}}

{{% inner_markdown %}}
Tytuły zawierają kolejną warstwę zabezpieczeń, RSA-2048. Jest to **szyfr asymetryczny**, co oznacza, że potrzebujemy jednego klucza do zaszyfrowania zawartości i drugiego do jej odszyfrowania. W skrócie, pozwala to Nintendo na szyfrowanie tytułów za pomocą nieujawnionego klucza (zwanego 'kluczem prywatnym'), podczas gdy Wii odszyfrowuje je za pomocą 'klucza publicznego', który jest przechowywany w konsoli. Gdyby hakerzy zdobyli klucz publiczny, nie wystarczyłoby złamać systemu bezpieczeństwa, ponieważ nadal oczekuje się, że dane będą szyfrowane za pomocą klucza prywatnego, o którym wie tylko Nintendo.

Ponadto RSA jest wykorzystywany nie tylko do szyfrowania treści, ale również do sprawdzania integralności tego szyfrowania. Widzisz, Nintendo używa wielu kluczy, które są używane do podpisywania (szyfrowania) już zaszyfrowanych danych, tworząc łańcuch szyfrowania, którego jedynym celem jest upewnienie się, że:
- Każdy użyty klucz został autoryzowany przez Nintendo.
- Dane nie zostały zmienione i ponownie zaszyfrowane bez autoryzacji.

Podam przykład, jak to działa:
1. Nintendo tworzy klucz o nazwie `x`.
2. Nintendo programuje Starlet, aby ufała treściom podpisanym tylko kluczem `x`.
3. Jeśli Starlet stwierdzi, że będzie musiała odszyfrować tytuł kluczem `y`, zadziała tylko wtedy, gdy `y` zostanie podpisany kluczem `x`.

Nazywa się to **Łańcuchem zaufania**. Poza Wii technika ta jest powszechnie stosowana do ochrony większości naszej komunikacji na całym świecie (na przykład przeglądarki internetowe korzystające z protokołu HTTPS polegają na 'certyfikatach głównych' w celu weryfikacji autentyczności nieznanych certyfikatów).

{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Łańcuch Starlet" >}}

{{% inner_markdown %}}

OTP Starlet przechowuje klucze publiczne (co oznacza, że dla naszych celów może tylko odszyfrować i zweryfikować podpis treści). Jego łańcuch zaufania składa się z następujących kluczy:

- **Klucz Główny**: Podpisuje klucz organu certyfikacji.
  - Starlet musi tylko przechowywać ten (publiczny) klucz, reszta może zostać odszyfrowana (i następnie zaufana), jeśli została podpisana tym kluczem.
- **Klucz Organu Certyfikacji** (CA): Podpisuje klucze XS i CP.
- **Klucz XS**: Ten klucz podpisuje 'tickety', rodzaj danych, który zawiera listę kluczy AES potrzebnych do odszyfrowania tytułów (zwanych 'kluczami tytułów').
- **Klucz CP**: Po odszyfrowaniu tytułu przy użyciu odpowiedniego klucza tytułu, klucz CP jest używany do podpisywania metadanych tytułu (tzw. 'TMD').
  - Chociaż nie podpisuje treści per se, metadane zawierają skrót SHA-1, którego Starlet używa do weryfikacji integralności tych danych.

Jak widać, wszystko to **pozwala Nintendo być wyłącznym dystrybutorem treści**, co może być dobre dla studiów gier zaniepokojonych piractwem.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Więcej kluczy" >}}

{{% inner_markdown %}}
Ten system zawiera również parę **kluczy prywatnych i publicznych ECC**. Kryptografia Krzywych Eliptycznych (ECC) to kolejny algorytm podobny do RSA. W tym przypadku służy tylko do podpisywania treści przesyłanych za pośrednictwem karty SD. To właśnie uniemożliwia wykorzystanie zawartości skopiowanej z jednego Wii na innym.

Klucz ECC jest podpisany przez *jeszcze inny* klucz publiczny RSA o nazwie **MS**, co pozwoli Starlet zaufać kluczowi ECC.

Ostatnim kluczem używanym przez tę konsolę jest **klucz HMAC**, który korzysta z innego algorytmu łączącego hashe SHA1 i HMAC. Podczas procesu uruchamiania Starlet sprawdza, czy NAND nie został zmieniony przez sprzęt innej firmy. Aby to zrobić, oblicza hash SHA1 pamięci NAND i porównuje go z hashem zakodowanym, aby sprawdzić, czy pasują. Oprócz tego zapisany skrót jest podpisany przy użyciu klucza HMAC, aby upewnić się, że jest autentyczny.

Na koniec klucz HMAC jest przechowywany w SEEPROM (poza Starlet), a nie w OTP.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

Po tym wszystkim warto wspomnieć, że gdy system uruchamia gry GameCube, **żadna z wymienionych metod szyfrowania nie jest używana**. Zamiast tego Starlet sprawdzi tylko, czy gra uzyskuje dostęp tylko do wyznaczonych lokalizacji pamięci. Dzieje się tak, ponieważ 1/4 pamięci RAM GDDR3 jest przydzielona do symulacji starego [ARAM]({{< ref "gamecube#audio" >}}).

### Upadek szyfrowania

Zacznijmy od kluczy AES, algorytm może być trudny do złamania, ale jeśli klucze zostaną w jakiś sposób wyodrębnione (zwłaszcza wspólny klucz), ta warstwa bezpieczeństwa zostanie natychmiast anulowana. Dlatego głównym wyzwaniem jest **jak je wyodrębnić**.

{{< float_group >}}
{{< figure_img float="true" src="encryption.png" alt="Diagram bezpieczeństwa Starlet" >}}
Diagram bezpieczeństwa Starlet
{{< /figure_img >}}

{{% inner_markdown %}}
Więc, grupa hakerów o nazwie **Team Twiizers** odkryła, że brak podpisów w trybie Gamecube może być obiecującą powierzchnią do ataku. Nie tylko odkryli, że **3/4 z tego RAM GDDR3 nie zostaje wyczyszczone** po uruchomieniu programu GC, ale również odkryli, że poprzez pomostowanie niektórych punktów adresowych na płycie głównej (przy użyciu pary pęset), mogą zamienić wybrane banki RAM GDDR3, umożliwiając im dostęp do obszarów ograniczonych. I oto znaleziono tam klucze AES.
{{% /inner_markdown %}}
{{< /float_group >}}

Nie zapominajmy, że pozwala to odszyfrować tylko 'pierwszą warstwę' bezpieczeństwa, ale aby uruchomić niepodpisane programy (Homebrew), RSA również musi zostać złamany. Niestety może to być obliczeniowo niemożliwe... Chyba że istnieją wady w jego realizacji. Cóż, Team Twizzers nie poprzestali na tym, więc zaczęli analizować sposób kodowania IOS, skupiając się na jego funkcjach weryfikacji podpisów.

Weryfikacja podpisu RSA, bez wchodzenia w szczegóły, polega na porównaniu skrótu obliczonej operacji RSA z odszyfrowanym podpisem. Po kilku manipulacjach grupa odkryła coś zabawnego: **Nintendo zaimplementowało tę funkcję za pomocą `strcmp`** (porównanie 'ciągu' w C).

Dla osób nieznających C, `strcmp` jest procedurą używaną do sprawdzania, czy dwa łańcuchy są równe. Ta kompozycja zawiera: dwa łańcuchy i kompozycja, która określa nazwę produktu. Następnie, strcmp zaczyna porównywać każdy znak aż do osiągnięcia końca dowolnego ciągu. Łańcuchy w C to po prostu ciąg znaków zakończony znakiem `\0`, co oznacza, że strcmp przestaje porównywać, gdy dowolny łańcuch osiągnie `\0`. W związku z tym, tworząc tytuł Wii w taki sposób, że jego hash zawiera na początku zera, obliczenia Starlet RSA przekażą ciąg znaków zaczynający się od `\0` do strcmp. Zatem porównanie zawsze zwróci `równe`... **Tytuł jest podpisany!**

Jakby tego było mało, ta luka została wykryta w wielu wersjach IOS - a nawet w procedurach znalezionych na boot1 i boot2!

### Świt Homebrew

Po tym pozostało tylko jedno: uczynić exploita trwałym i zaimplementować 'przyjazne dla użytkownika' narzędzie, aby mógł bezproblemowo uruchamiać niestandardowe programy.

{{< float_group >}}
{{< figure_img float="true" src="system/twilight_hack.png" alt="Ekran kopiowania Twilight hack" >}}
Uruchamianie aplikacji innych firm początkowo odbywało się przy użyciu sfałszowanego zapisu gry
{{< /figure_img >}}

{{% inner_markdown %}}
Do tej pory exploity te wymagały użycia dodatkowego sprzętu, więc nie każdy użytkownik mógł z niego skorzystać... Dopóki Team Twizzers nie odkryli *kolejnego* exploita: Przepełnienia bufora gry.

Mam na myśli słynną **The Legend of Zelda: Twilight Princess** (nawiasem mówiąc, grę firmy Nintendo). TT odkryli, że plik zapisu gry może zostać zmodyfikowany, aby przepełnić liczbę znaków używanych do nazwania konia gracza. Tak więc, gdy gracz spróbuje odczytać przepełnioną nazwę, wywoła to reakcję łańcuchową kończącą się wykonaniem dowolnego kodu. Można to wykorzystać do uruchomienia, powiedzmy, programu ładującego.
{{% /inner_markdown %}}

{{< /float_group >}}

Ponieważ podpisy można było teraz sfałszować, ten spreparowany plik zapisu można było łatwo rozpowszechniać w sieci, aby inne osoby mogły z niego korzystać. W rezultacie społeczność homebrew mogła teraz uruchamiać swoje niestandardowe oprogramowanie.

### Stan stały

Podczas dalszej inżynierii wstecznej IOS odkryto, że sygnatury są sprawdzane tylko podczas instalacji tytułów, a nie podczas ich wykonywania.

{{< float_group >}}

{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_img name="Banner" active="true" src="system/homebrew_channel.png" alt="Kanał Homebrew" >}}
  {{< tab_img name="Wewnątrz" src="system/homebrew/menu.png" alt="Menu Kanału Homebrew" >}}
  {{< figcaption >}}
Najbardziej przyjazny dla użytkownika hack ze wszystkich.  
_Nieoficjalny_ kanał Homebrew (2008).
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Tak więc TT ponownie zabłysnęli. Ostrożnie stworzyli kanał do zainstalowania, który mógł załadować dowolne programy z karty SD. Gdyby kanał ten został zainstalowany przed podjęciem przez Nintendo działań mających na celu złagodzenie problemów związanych z bezpieczeństwem, wówczas Wii na stałe cieszyłaby się homebrew (niezależnie od łatania przez Nintendo ich błędów z podpisami w przyszłości, co uczynili).

**Kanał Homebrew** był tego wynikiem, ten tytuł pozwolił każdemu użytkownikowi na odpalenie programów homebrew, które mogły korzystać z pełnej kontroli nad tym systemem (z wszystkimi tego konsekwencjami).
{{% /inner_markdown %}}

{{< /float_group >}}

### Odpowiedź Nintendo

Z oczywistych powodów Nintendo wydała wiele aktualizacji systemowych, które naprawiły exploit z podpisami w wielu wersjach IOS, oraz zajęli się również wadliwymi etapami rozruchu, wypuszczając nowe wersje sprzętowe.

{{< float_group >}}
{{< figure_img float="true" src="system/updates.png" alt="Ekran aktualizacji" >}}
Przybędzie ich wiele
{{< /figure_img >}}

{{% inner_markdown %}}
W systemie tym wykryto jednak inne zasadnicze wady:

- Broadway może ponownie uruchomić Starlet z dowolną wersją IOS bez dodatkowych uprawnień, pozwalając na wykorzystanie wersji niezpatchowanych.
- Ukryte API IOS nadal mogą być używane bez specjalnych uprawnień, umożliwiając jeszcze większą nieupoważnioną kontrolę sprzętu.
- Napęd dysków może otrzymać polecenia odczytu konwencjonalnych DVD, a niektóre IOS zawierają ukryte wywołania tych poleceń. Było to szczególnie niepokojące z powodu piractwa.
{{% /inner_markdown %}}

{{< /float_group >}}

Więc, podsumowując, po tym wszystkim jedyne co pozostało to zabawa w kotka i myszkę. W ciągu następnych miesięcy odkryto kolejne różne exploity, które następnie Nintendo próbowało patchować jeden po drugim. Ta 'zabawa' trwała aż do końca życia konsoli i nie wydano już żadnych aktualizacji. Możemy założyć, że tutaj wygrała mysz.

Na chwilę pisania tego artykułu, luki tu wspomniane zostały załatane, ale również zastąpione obecnie działającymi.

Myślę, że nie ma żadnych sporów o wpływ, jaki scena hakerska wywarła na ten system, i kto może zapomnieć o ogromnej ilości homebrew która była udostępniona (istniał nawet 'sklep' homebrew, który był szybszy i oferował więcej wolności niż oficjalny Kanał Sklepowy Wii).

---

## To wszystko ludziska

Szczęśliwego nowego roku 2020!

Ten artykuł zajął mi trochę czasu, naiwnie przewidywałem, że skoro większość treści była już napisana dla Gamecube'a, będę musiał tylko napisać krótkie akapity i dodać linki tu i tam...

Myślę, że wyszedł bardziej informacyjny niż oczekiwałem, więc mam nadzieję, że spodobała ci się jego lektura.

Do następnego razu!  
Rodrigo
