---
long_title: Architektura Wii
short_title: Architektura Wii
name: Wii
subtitle: Unikalne techniki innowacji
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
    caption: "Le Wii.<br>Wydane 19.11.2006 w Ameryce, 02.12.2006 w Japonii i 08.12.2006 w Europie."
  Motherboard:
    caption: "Pokazuję wersję 'RVL-CPU-40', wcześniejsze wersje miały znacznie większy proces produkcyjny, a późniejsze usunęły większość WE/WY Gamecube'a.<br>NAND Flash jest zamontowany z tyłu."
    bib_source: photography-okqubit
  Diagram:
    caption: Ważne magistrale danych są oznaczone ich szerokością i prędkością.
#Historical
aliases:
  - /projects/consoles/wii/
---

## Szybkie wprowadzenie

Mimo że na Wii brakowało najnowocześniejszej grafiki, którą cieszyli się jej konkurenci, nowe rodzaje sterowania i innowacyjne oprogramowanie dały tej konsoli nowe obszary, którymi można się chwalić.

Tutaj przeanalizujemy każdy aspekt tej konsoli, od znanego już sprzętu po przeoczany system bezpieczeństwa, w tym jego główne wady.

Szybka Uwaga: Niektóre sekcje pokrywają się z częścią poprzedniego artykułu o [GameCube](gamecube), więc zamiast powtarzać informacje, po prostu umieszczę link do odpowiedniej części artykułu.

## {.supporting-imagery}

## Kontrolery nowej generacji

Zacznijmy od omówienia jednego z najbardziej charakterystycznych aspektów tej konsoli: Kontrolerów.

Główne urządzenie to nic innego jak **Pilot Wii** (zwany także 'Wiimote'), gadżet o kształcie podobnym do pilota telewizora, który zawiera wiele rodzajów elementów sterujących wejściem:

- Na początek ma **zestaw fizycznych przycisków**, które są używane jak każdy konwencjonalny kontroler.
- Zawiera również **akcelerometr** do wykrywania zmian orientacji, jest to główny składnik używany do wykrywania ruchu.
- Wreszcie zawiera **kamerę na podczerwień**, która w połączeniu z akcelerometrem i przetwarzaniem Wii może być używana do wskazywania ekranu.
  - Ten czujnik wymaga **Listwy Czujnikowej** (dołączonej do konsoli). Listwa zawiera dwa zestawy diod podczerwieni, które kamera może wykryć, Wii wykorzystuje triangulację do obliczenia pozycji Wiimote'a względem telewizora.

Pilot jest zasilany przez **BCM2042** [@io-wiimote] firmy Broadcom, chip, który zawiera wszystkie niezbędne obwody, aby stać się niezależnym urządzeniem Bluetooth (procesor, RAM, ROM i oczywiście moduł Bluetooth). Chociaż Wiimote jest zaprogramowany do przestrzegania protokołu 'Bluetooth HID', by być identyfikowany jako urządzenie wejściowe, nie jest zgodny ze standardową metodą wymiany danych (być może, aby uniemożliwić używanie w systemach innych niż Wii).

Wreszcie, Wiimote zawiera również **16 KB EEPROM** do przechowywania danych użytkownika oraz **mały głośnik** ograniczony do próbek niskiej jakości (3 kHz 4-bit ADPCM lub 1,5 kHz 8-bit PCM).

### Rozszerzenia

Nintendo dostarczyło ten system z innym kontrolerem, który ma być używany po drugiej stronie, **Nunchuk**, ten ma własny akcelerometr, joystick i dwa przyciski. Jest podłączony do niestandardowego 6-stykowego portu producenta w Wiimote.

![Nunchuk i Wiimote [@photography-amos], odpowiednio.](controllers/both.jpg)

Dla tego portu zbudowano również inne akcesoria, z których każde zapewniało inny rodzaj wejścia.

## CPU

Po sukcesie Gekko, IBM prawdopodobnie przechwycił ten projekt i przemianował go na '750CL', aby inni producenci mogli go używać [@cpu-ibmcl]. Następnie, gdy Nintendo poprosiło o nowy procesor do użycia z ich nową konsolą, wciąż znaną jako 'Revolution' (stąd prefiks RVL na ich standardowych płytach głównych), IBM i Nintendo zgodziły się na użycie 750CL z zegarem 1,5 raza szybszym od Gekko. Ten procesor jest znany jako **Broadway** [@cpu-ibmuser] i działa z prędkością **729 MHz**.

Po zapoznaniu się z [Gekko](gamecube#cpu) obawiam się, że nie ma zbyt wielu zmian w nowym procesorze. Może to być jednak zaletą: Developerzy GameCube mogli od razu rozpocząć tworzenie nowych gier Wii dzięki całemu doświadczeniu, które zdobyli z Gekko. Co więcej, fakt, że Broadway działa 1,5 raza szybciej niż pierwotny procesor, pozwoli im dążyć do większej liczby funkcji i lepszej jakości.

### A co z pamięcią?

Ta jest ciekawa, stary układ pamięci GameCube został przearanżowany i wzbogacony o następujące zmiany:

- Splash (24 MB 1T-SRAM) znajduje się teraz w pakiecie Hollywood SoC (wyjaśnione później) i jest teraz określany jako **MEM1** [@operating_system-memory].
- ARAM-u (16 MB szeregowej pamięci SDRAM) już dawno **nie ma**, jednak...
- Dostępny jest nowy układ pamięci, **MEM2**, który zawiera **64 MB GDDR3 SDRAM** ogólnego przeznaczenia.
  - Ten rodzaj pamięci jest oparty na tradycyjnym systemie DDR2, ale został ulepszony z wyższą przepustowością (~2 razy większą od oryginalnej szybkości transferu) i zmniejszonym zużyciem energii, co jest idealne dla GPU.

### Kompatybilność wsteczna

Na razie, możesz myśleć o tej konsoli jako o nadzbiorze GameCube i jako taka, kompatybilność z poprzednią generacją gier jest naturalnie dziedziczona. Biorąc to pod uwagę, aby uczynić Wii *w pełni* wstecznie kompatybilnym, stary zestaw portów zewnętrznych został przeniesiony na Wii - porty kontrolerów GameCube i kart pamięci. Istnieje jednak nowe ograniczenie: nowa mapa pamięci jest niekompatybilna ze starą. Więc w oprogramowaniu została zaimplementowana cienka warstwa 'emulacji' (więcej szczegółów w sekcji 'WE/WY' i 'System operacyjny').

Jeśli chodzi o akcesoria GameCube korzystające z gniazda Serial/Hi-Speed, obawiam się, że Wii nie zawierało tych portów, więc te akcesoria nie mogą być tutaj używane.

W późniejszych latach nowe wersje Wii niestety pozbyły się ich.

## Grafika

Podobnie jak w przypadku GameCube (gdzie elementy graficzne, interfejsy I/O i możliwości audio zostały połączone w jeden pakiet o nazwie "Flipper"), Wii podąża za tym przykładem i obok Broadway'a zawiera duży układ o nazwie **Hollywood**. Znajdziemy tutaj podsystem graficzny, który jest identyczny z Flipperem, choć z minimalnymi poprawkami.

Tak więc procesor graficzny Hollywood nadal wykonuje te same zadania, które wykonywał [jego odpowiednik we Flipperze](gamecube#graphics) dawniej, ale teraz ma **1.5x**-krotnie większą szybkością zegara (**243 MHz**). Ten wzrost oznacza, że więcej geometrii i efektów może być przetwarzanych w tej samej jednostce czasu.

### Funkcjonalność

Ponieważ silnik 3D jest nadal [Flippera](gamecube#graphics), zamiast powtarzać ten sam przegląd potoku, wspomnę o kilku interesujących zmianach projektowych, które musiały przejść gry:

#### Standardowy Widescreen {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Tryb 4:3.](mario/4_3.png){.active title="Tradycyjny"}

![Tryb 16:9, skomponowany przez enkoder wideo.](mario/16_9.png){title="Szeroki"}

![Tryb 16:9 wyświetlany na telewizorze widescreen.](mario/16_9_stretched.png){title="Rozciągnięty"}

Super Mario Galaxy (2007).

:::

Gry GameCube nie miały odpowiedniego wsparcia dla wyświetlaczy szerokoekranowych (czyli komponowania klatek 16:9, odbiegających od tradycyjnego 4:3). Niemniej jednak GPU Flippera był już w stanie to zrobić, a kilka gier zapewniało opcje jego aktywacji, chociaż nadal było to uważane za ekskluzywną funkcję.

Tak czy inaczej, bufor ramki pozostaje identyczny, a koder wideo nadal wysyła klatkę zgodną z PAL lub NTSC, więc ta funkcja "widescreen" jest realizowana przez **poszerzenie pola widzenia** w macierzy projekcji. Rezultatem jest renderowana scena z większym kątem widzenia, która jest teraz ściśnięta w poziomie. Jednak szerokoekranowy telewizor również odgrywa rolę w tym procesie, ponieważ następnie rozciągnie klatkę 4:3 (pochodzącą z konsoli), a wyświetlany obraz będzie wyglądał *mniej więcej* we właściwym stosunku. Jeśli jesteś ciekawy, ta technika nie jest nowa, była wykorzystywana w projekcji filmowej i jest określana jako **anamorficzny widescreen**. Zabawne jest też to, jak programiści SNES musieli radzić sobie z [odwrotnym efektem](super-nintendo#display-modalities).

Wracając do sedna, Wii ustandaryzowało tę funkcję, umożliwiając aktywację 'trybu widescreen' w ustawieniach systemowych, co z kolei promowało jego _szerokie_ przyjęcie (zamierzona gra słów).

#### Interakcja z Ekranem {.tab}

![Super Mario Galaxy (2007).<br>Możesz podnieść gwiazdy, wskazując na nie.](mario/mario_galaxy.png) {.tab-float}

Nowy i śmiały projekt kontrolera oznaczał nowe rodzaje interakcji w grach Wii. Ponieważ Wiimote umożliwiał użytkownikom wskazywanie ekranu, niektóre gry, takie jak *Super Mario Galaxy* lub *The Legend of Zelda: Twilight Princess* użyło tej funkcji, aby umożliwić graczowi interakcję ze scenerią.

W raporcie zatytułowanym _Myth Debugging: Is the Wii More Demanding to Emulate than the GameCube?_ [@graphics-dolphin], developerzy emulatora Dolphin wyjaśniają, że gry takie jak Super Mario Galaxy i inne pierwszoosobowe strzelanki polegają na [wbudowanym buforze-Z](gamecube#tab-3-4-render), aby zidentyfikować obiekt, na który wskazuje Wiimote i/lub sprawdzić, jak daleko obiekt znajduje się od kursora Wiimote.

Nie jest to nowa funkcja per se, ale nowatorskie wykorzystanie obecnych możliwości. Gry GameCube nie były zależne od wielofunkcyjnego kontrolera z funkcją wskaźnika. Teraz gracze mogą sterować postacią i jednocześnie wskazywać na ekran.

### Zaktualizowane Design'y {.tabs-close}

Dodatkowa szybkość Broadwaya i Hollywood w połączeniu z awangardowymi projektami przyniosła pewne ulepszenia modeli postaci. Może nie są tak znaczące jak w innych generacjach, ale nadal są zauważalne i doceniane.

![Super Smash Bros. Melee (2001) dla GC.<br>4,718 trójkąty.](mario_melee_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) dla Wii.<br>5,455 trójkąty.](mario_brawl_wii){.toright model3d="true"}

![Sonic Adventure DX (2003) dla Gamecube.<br>1,985 trójkątów.](tails_dx_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) dla Wii.<br>3,644 trójkąty.](tails_brawl_wii){.toright model3d="true"}

### Sygnał Wideo

Co zaskakujące, ta konsola nie używa już starego portu [Multi Out](super-nintendo.md#a-convenient-video-out), ale jego odmianę o nazwie **AV Multi Out** (to tyle jeśli chodzi o nazwę) o nieco innym kształcie. Ten przenosi wszystkie poprzednie sygnały plus **YPbPr** (znany jako 'komponent') [@graphics-video]. Zawiera również niektóre linie danych, których system używa do identyfikacji typu podłączonego kabla.

Niestety, to medium dziedziczy te same [ograniczenia](gamecube.md#connections) co GameCube. Oznacza to brak S-Video w systemach PAL i brak RGB w systemach NTSC. Ponadto RGB może nadawać tylko sygnały z przeplotem (bez progresywnego).

## Dźwięk

Wii zawiera ten sam **DSP Macronix**, który znajduje się w [GameCube](gamecube#audio), możesz rzucić okiem na ten artykuł w celu szczegółowej analizy.

W porównaniu z GameCube jedyną poważną zmianą jest to, że skoro ARAM zniknął, MEM1 lub MEM2 mogą być używane jako bufor audio.

## WE/WY

Podsystem WE/WY tej konsoli naprawdę zmienia zasady gry (*wybaczcie grę słów*). Interfejsy są teraz kontrolowane przez jeden moduł, który będzie również dbał o bezpieczeństwo. Mówię tu o **Starlet**.

### Ukryty koprocesor

Starlet to po prostu procesor **ARM926EJ-S** podłączony do większości wewnętrznych komponentów tej konsoli. Znajduje się w Hollywood, działa z częstotliwością **243 MHz** (tak samo jak Hollywood) i zawiera również własny ROM i RAM. W ten sposób możesz uznać Starlet za niezależny komputer działający obok głównego procesora.

![Główny schemat architektury Wii. Zwróć uwagę, jak Starlet jest w stanie kontrolować większość wejść/wyjść, a nawet ukryć niektóre przed Broadwayem.](diagram.png)

Rdzeń jest podobny do tego używanego w [Nintendo DS](nintendo-ds), z wyjątkiem dwóch 'specjalnych' dodatków:

- Litery 'J' w nazwie modelu, która oznacza dołączenie **Jazelle**: dedykowanej jednostki wykonującej 8-bitowy kod bajtowy Java. Programy Java nadal będą zależne od maszyny wirtualnej (znanej jako 'JVM'), ale niektóre kody operacyjne mogą być wykonywane bezpośrednio z procesora. Ogólnie rzecz biorąc, może to przyspieszyć wykonanie skompilowanego kodu Java.
- Dedykowanej **Jednostki Zarządzania Pamięcią** (MMU) umożliwiającej korzystanie z pamięci wirtualnej. Przydatne dla systemów operacyjnych ogólnego przeznaczenia.

Te ulepszenia są nieco 'dziwne', ponieważ są całkowicie nieużywane na Wii. Niemniej jednak Nintendo wybrało ten rdzeń dla Starlet. Przypomina mi to pierwszego iPhone'a (2G), który również zawierał procesor ARM z Jazelle (również zupełnie nieużywany).

Jeśli zastanawiasz się, Jazelle nigdy się nie przyjęło. Po kilku iteracjach odkryto, że Java Bytecode po prostu działa lepiej na oprogramowaniu. Później ARM zastąpił Jazelle wprowadzając "Thumb-2EE”, a w momencie pisania (czerwiec 2021 r.) obie te jednostki zostały wycofane.

::: {.subfigures .distributed .open-float .float max_subfigures=1}

![](console/front.jpeg)

![](console/back.jpeg)

Zewnętrzne WE/WY na Wii.<br>Ciemne & małe przednie gniazdo to czytnik kart SD.

:::

Idąc dalej, ten 'procesor WE/WY' ma za zadanie rozstrzygać dostęp między wieloma WE/WY i Broadwayem, a tym samym dba o bezpieczeństwo (które decyduje, czy zezwolić na dostęp, czy nie). Jest to szczególnie ważne, jeśli chodzi na przykład o przyznanie dostępu do pamięci NAND, w której przechowywany jest główny system operacyjny i dane użytkownika.

Chip dziedziczy również niektóre technologie z ARM, takie jak **Advanced Microcontroller Bus Architecture** (AMBA), protokół, który ułatwia komunikację między urządzeniami za pomocą zestawu wyspecjalizowanych magistral.

{.close-float}

Powiedziawszy to, Nintendo podłączyło WE/WY w sposób, który wykorzystuje dwie magistrale AMBA [@io-diagram]:

- **Magistrala AHB** (Magistrala o Wysokiej Wydajności AMBA [ang. AMBA High-performance Bus]): Jak sama nazwa wskazuje, jest przeznaczona do szybkiej komunikacji. Tutaj znajdujemy:
  - Interfejs **NAND**: Uzyskuje dostęp do 512 MB NAND Flash, która przechowuje system operacyjny i dane użytkownika.
  - Dwa interfejsy **Secure Digital Input Output** (SDIO): SDIO to protokół przeznaczony głównie do uzyskiwania dostępu do karty SD, ale w tym przypadku drugi służy do sterowania modułem Wi-Fi (802.11 b/g).
  - Kontroler **USB 2.0**: Obsługuje dwa zewnętrzne gniazda USB i wewnętrzną kartę Bluetooth 2.0.
  - Moduł **SHA-1** i **AES**: Zarezerwowany do zadań związanych z bezpieczeństwem (więcej szczegółów w sekcji 'Przeciwdziałanie-Piractwu').
- **Magistrala APB** (Zaawansowana Magistrala Peryferyjna [ang. Advanced Peripheral Bus]): Ta jest ograniczona do komponentów o niskiej wydajności, w tym:
  - **Interfejsu stacji dysków**: Podłącza czytnik dysków.
  - **Interfejsu szeregowego**: Łączy kontrolery GameCube.
  - **Interfejsu zewnętrznego** \[ang. External Interface\] (EXI): Widzieliśmy już ten interfejs [wcześniej](gamecube#internal-io). Komunikuje się z innym sprzętem GameCube, i jest używany do wstecznej kompatybilności.

### Utrzymanie kompatybilności

![Wii z akcesoriami GameCube [@photography-amos].](gamecube-mode.png) {.open-float.no-borders}

Wii zachowuje pełną wsteczną kompatybilność z grami GameCube, mimo że system WE/WY uległ drastycznej zmianie. Dzieje się tak, ponieważ Starlet można przeprogramować, gdy uruchamiana jest gra GameCube, aby wirtualnie ponownie mapować WE/WY na takie, jakiego spodziewałby się oryginalny GameCube.

Dodatkowo układ zegara czasu rzeczywistego zawiera zapasową pamięć ROM, która przechowuje czcionki bitmapowe (zestaw Łaciński i Japoński) używane przez gry GameCube; i SRAM do zapisywania [ustawień związanych z IPL](gamecube#operating-system).

{.close-float}

## System Operacyjny

Ogólnie rzecz biorąc, w Wii znajdują się **dwa systemy operacyjne**. Jeden wykonywany jest w Broadway'u (głównym CPU), a drugi w Starlet (CPU WE/WY). Oba znajdują się w tych 512 MB NAND i mogą być aktualizowane.

### System operacyjny Starlet

Starlet sam w sobie to już ciekawy sprzęt, ale jego oprogramowanie jest jeszcze bardziej intrygujące. Widzisz, ten system operacyjny nie tylko ma pełny dostęp do każdego zakątka konsoli, ale jest także pierwszą rzeczą uruchamianą na konsoli po wciśnięciu przycisku zasilania.

Starlet uruchamia system *nieoficjalnie* określany jako **System Operacyjny Wejścia/Wyjścia** [ang. Input/Output Operating System] lub 'IOS' (proszę, nie myl tego z iOS firmy Apple) [@operating_system-ios]. IOS to w pełni funkcjonalny system operacyjny składający się z:

- **Mikrojądra**: Kontroluje procesor ARM9, wykonuje **procesy** i komunikuje się z innym sprzętem przy użyciu **sterowników**.
- **Sterowników**: Umożliwiają komunikację ze sprzętem znajdującym się poza procesorem (WE/WY).
- **Procesów**: Wykonują zadanie, takie jak zarządzanie siecią lub wdrażanie systemu plików.
- **Rdzenia kryptograficznego**: Przyspiesza operacje związane z szyfrowaniem (**Tylko AES i SHA-1**).

Mając to na uwadze, **głównym zadaniem IOS jest odciążenie głównego procesora** poprzez abstrakcję WE/WY i bezpieczeństwa. Z tego powodu programiści nie muszą się nimi martwić. Aby to osiągnąć, Starlet **rezerwuje od 12 do 16 MB** RAM GDDR3 dla swoich zadań, reszta jest używana przez Broadway i GPU.

Broadway i Starlet komunikują się ze sobą za pomocą **Komunikacji Międzyprocesowej** lub protokołu 'IPC': W skrócie, oba procesory dzielą po dwa rejestry. Jeden CPU może zapisywać w rejestrach drugiego (zapisane dane mogą reprezentować polecenie lub wartość), a stamtąd CPU odbiorca może wykonać funkcję w odpowiedzi.

System aktualizacji IOS **jest nieco skomplikowany**: Zaktualizowane wersje IOS nie są instalowane na starych, ale w innym gnieździe (zarezerwowany obszar w NAND dla IOS jest podzielony na 'gniazda'). Jest to wyłącznie **ze względu na kompatybilność**, ponieważ pozwala starszemu oprogramowaniu Wii na używanie tej samej wersji IOS, dla której zostało opracowane.

Nintendo często publikowało aktualizacje IOS w celu poprawy obsługi sprzętu (co było konieczne, gdy dostarczono nowe akcesorium). Jest tylko **jeden wyjątek**, gdy aktualizacje IOS faktycznie zastępują starsze: Gdy wykryto, że konkretna wersja ma lukę, którą można wykorzystać. Robione to było tylko ze względów bezpieczeństwa.

Po włożeniu gry GameCube dzieje się coś innego: Startlet zamiast tego uruchamia **MIOS**. Ten wariant IOS po prostu nakazuje Starletowi emulację oryginalnego [IPL](gamecube#operating-system).

### System operacyjny Broadway

Ten jest powszechnie znany jako **Menu Systemowe** i działa na głównym procesorze PowerPC (**Broadway**).

![Menu systemowe z _wieloma_ zainstalowanymi kanałami.](system/home.png){.tabs-nested .active title="Home"}

![Menu ustawień służące do zmiany ustawień.](system/settings.png){.tab-nested title="Ustawienia"}

![Tablica kontaktowa przechowuje listy pogrupowane według daty.](system/mail.png){.tabs-nested-last title="Tablica Kontaktowa"}

W porównaniu z IOS nie uważałbym tego za 'w pełni rozwinięty' system operacyjny, ale bardziej za 'program', który pozwala użytkownikowi wykonywać następujące operacje:

- **Uruchom grę Wii/GameCube**: Tylko wtedy, gdy włożona jest prawidłowa gra.
- **Zmień ustawienia konsoli**: W tym między innymi godzinę, datę, tryb wideo lub lokalizację czujnika.
- **Uruchom aplikacje**: Jedną z nowości tej konsoli jest możliwość instalacji niewielkich gier Wii (tzw. 'WiiWare'), gier retro (gry 'Virtual Console') lub po prostu wygodnych aplikacji (takich jak przeglądarka internetowa). Nintendo nazwało je **kanałami**, ale w systemie operacyjnym są one również określane jako **tytuły**.
  - Użytkownicy mogą pobierać/kupować kanały za pośrednictwem wstępnie zainstalowanego kanału o nazwie **Kanał Sklepowy Wii**.
  - Tytuły Virtual Console zawierały emulator do samodzielnego uruchamiania gry. Co ciekawe, emulator nie jest współdzielony w systemie ani nawet pomiędzy grami z tej samej platformy. Pozwala to zoptymalizować emulator dla określonych gier.
- **Wysyłaj/Odbieraj wiadomości**: Wii mają unikalny identyfikator (wypalony w ich chipie SEEPROM), który można udostępniać w celu wymiany wiadomości między innymi Wii. Wiadomości można zobaczyć na **Tablicy Kontaktowej**.
  - Gry Nintendo i Wii również używały tego medium do dostarczania biuletynu.

Podobnie jak IOS, Nintendo również wydało wiele aktualizacji tego systemu. Niektóre naprawiły błędy bezpieczeństwa, inne dodały więcej funkcji. Godną uwagi nową funkcją była możliwość przechowywania kanałów na karcie SD.

Każdy program działający na Broadwayu (w tym Menu Systemowe) działa w oparciu o określoną wersję IOS. Po uruchomieniu gry lub kanału Starlet uruchamia się ponownie, używając zadeklarowanej wymaganej wersji IOS.

### Nośnik aktualizacji

Nintendo nazywa je **aktualizacjami systemowymi**. Zawierają one oba systemy w tym samym pakiecie i używają numerów porządkowych do oznaczania wersji. Ostatnia znana wersja to `4.3E`, wydana w czerwcu 2010 r.

Pakiety aktualizacji systemu można pobrać z serwerów Nintendo lub dysków z grami. Użytkownicy mogą ręcznie sprawdzać dostępność aktualizacji za pomocą Menu Systemowego. Aktualizacje są wymuszane, jeśli gra wymaga określonej wersji systemu IOS, która nie jest zainstalowana (a płyta zawiera wymagane pakiety).

### Sekwencja rozruchowa

Do tej pory omówiliśmy dwa bardzo różne systemy operacyjne, które znajdują się w tej konsoli i działają jednocześnie. Wydaje się to dość proste, chociaż oba muszą być starannie skoordynowane podczas uruchamiania konsoli, aby później działały poprawnie.

Biorąc to pod uwagę, proces uruchamiania wygląda następująco [@operating_system-boot]:

1. Użytkownik naciska przycisk ON na konsoli.
2. Etap **Boot0**: Starlet uruchamia program znajdujący się we wbudowanym Mask ROM-ie (1,5 KB).
    - Krótko mówiąc, Starlet odszyfrowuje i sprawdza integralność pierwszych 96 KB NAND, następnie oblicza swój hash i porównuje go z zapisanym hashem znalezionym we wbudowanej pamięci OTP Starlet. Jeśli oba hashe nie pasują, konsola jest indukowana w nieskończoną pętlę.
3. Etap **Boot1**: Starlet uruchamia mały program znaleziony we wspomnianym 96 KB NAND.
    - Ten program po prostu poinstruuje Starlet, aby zainicjowała (wyczyściła) 64 MB pamięci RAM GDDR3, a następnie odszyfrowała i zweryfikowała resztę NAND.
4. Etap **Boot2**: Starlet ładuje początkowy system IOS (potrzebny do Menu Systemowego), a następnie uruchamia Broadway.
5. Broadway uruchamia Menu Systemowe. Użytkownik ma teraz kontrolę.

## Gry

Chociaż nowe gry nie zawsze przynosiły znaczne skoki graficzne, zaskakiwały użytkowników liczbą funkcji, które mogą teraz zaoferować. Stało się tak dzięki nowym usługom dostarczonym przez Nintendo wraz z premierą konsoli, począwszy od nowego zestawu elementów sterujących, a skończywszy na standardowej infrastrukturze online (WiiConnect24), która umożliwiała *darmowe* granie online.

### Nośnik danych

Gry Wii są dystrybuowane przy użyciu zastrzeżonego formatu dysku o nazwie **Wii Optical Disc** (wiem, nazwa nie mogła być bardziej oczywista). W każdym razie Matsushita/Panasonic zaprojektował ten format w oparciu o tradycyjną płytę DVD, dodając niestandardowe funkcje, takie jak **burst cutting area** na wewnętrznej części dysku, aby zapobiec nieautoryzowanym reprodukcjom.

![Standardowe gry są prezentowane w fizycznym pudełku i na dysku.](box_case.jpeg){.tabs-nested .open-float .tab-float .active title="Fizyczny"}

![Małe gry (WiiWare) i emulowane gry (Virtual Console) można kupować i pobierać przez Kanał Sklepowy.](system/store.png){.tabs-nested-last title="Cyfrowy"}

Dysk Wii zapewnia **4,7 GB** (w przypadku jednowarstwowego) lub **8,54 GB** (w przypadku dwuwarstwowego) wolnego miejsca. Często zawierają **dwie partycje**: Pierwszą na aktualizacje systemu, a drugą na rzeczywistą grę.

Niektóre gry, takie jak *Super Smash Bros. Brawl*, zawierały więcej partycji do przechowywania wielu gier Virtual Console, które były uruchamiane w głównej grze.

{.close-float}

### Tworzenie

Zgodnie z tradycją, Nintendo dostarczyło zestaw deweloperski. Ten nazywał się **NDEV** [@games-ndev] i miał kształt powiększonej czarnej _cegły_. NDEV jest dostarczany z rozszerzonym WE/WY i dwukrotnie większą ilością MEM2 (łącznie 128 MB) do celów debugowania.

Oficjalny pakiet oprogramowania nazywał się **Revolution SDK** [@games-rsdk] i zawierał różne narzędzia, kompilatory, debuggery i frameworki do wykonywania prac programistycznych (głównie w ** C/C++**). Nintendo rozpowszechniało kolejne aktualizacje za pośrednictwem portalu internetowego o nazwie **Warioworld.com** (teraz offline/przekierowanego), do którego dostęp mieli tylko zatwierdzeni programiści.

Oficjalny SDK opiera się na wywołaniach IOS do interakcji ze sprzętem Wii, dlatego aktualizacje IOS są często skorelowane z aktualizacjami SDK.

### Powrót do Home

Biorąc pod uwagę wszystkie ulepszenia oprogramowania tej konsoli, może Cię zaskoczyć, że gry **wciąż działają na gołym metalu**, co oznacza, że mają pełną kontrolę nad Broadwayem, ale nie Starlet (stąd zaimplementowane są tutaj mechanizmy bezpieczeństwa). Nie trzeba dodawać, że zachowanie gry nadal podlega zatwierdzeniu przez Nintendo przed dystrybucją.

![Menu home pokazujące się w grze.](ingame/home.png){.tabs-nested .open-float .tab-float .active title="Menu"}

![Ten ekran również musi być uwzględniony.](ingame/strap_warning.png){.tabs-nested-last title="Przypomnienie"}

Powiedziawszy to, istnieją pewne funkcje w różnych grach, które w jakiś sposób wyglądają strasznie identycznie. Czy pamiętasz na przykład słynne **Menu HOME**? Naciśnięcie przycisku 'HOME' na Wiimote uruchomi wyskakujące okienko w grze, umożliwiając użytkownikowi powrót do menu systemowego bez konieczności ponownego uruchamiania konsoli. Biorąc pod uwagę, że system operacyjny nie zapewnia tej funkcji, w jaki sposób wszystkim udało się stworzyć ten sam interfejs graficzny?

Odpowiedź jest prosta, Nintendo zawarło w swoim SDK kilka **obowiązkowych bibliotek**, które gry muszą zawierać. *Okazuje się*, że jedna z nich rysuje ten ekran. Co więcej, jest to powód dla którego, jak się przekonasz, tylko aplikacje homebrew zawierają 'oryginalne' projekty menu home.

{.close-float}

Oficjalne menu HOME jest jednym z około 200 wymagań, które gry musiały spełnić, zgodnie z dokumentem **Wii Programming Guidelines** (znajdującym się w oficjalnym SDK). Inne wymagania polegały na wyświetleniu ekranu 'Przypomnienie o Pasku Wii' (który jest tylko obrazem BMP) na początku gry, po którym następowała kolejna reguła, która dyktowała sposób interakcji z nim.

### Spersonalizowane tytuły

![Kanał Mii pozwala bawić się własnym 'Mii'...](system/mii-editor.png){.tabs-nested .open-float .tab-float .active title="Edytor"}

![...który następnie pojawia się w twoich grach.<br>Wii Music (2008).](ingame/music.png){.tabs-nested-last title="Gra"}

Kolejną nową funkcją, na którą chciałbym zwrócić uwagę, jest wprowadzenie **Mii**, pewnego rodzaju awatarów, które użytkownicy mogą tworzyć za pomocą dedykowanego kanału o nazwie *Kanał Mii*.

Ale zabawa na tym się nie skończyła, ponieważ gry mogły również pobierać te nowo stworzone Mii, aby spersonalizować rozgrywkę.

{.close-float}

## Przeciwdziałanie-Piractwu & Homebrew

Myślę, że liczba funkcji oferowanych przez tę konsolę czyniła ją bardzo atrakcyjną dla hakerów, ponieważ złamanie systemu bezpieczeństwa pozwoliłoby twórcom homebrew na zdobycie możliwości konsoli bez konieczności przechodzenia przez kontrole Nintendo. Tak czy inaczej, Wii skończyło z fantastyczną biblioteką Homebrew.

### Ochrona przed kopiowaniem

Zacznijmy od pospolitej ofiary: **Napędu dyskowego**.

Dyski Wii zawierają wyżej wspomniany obszar 'burst cutting', który jest niedostępny dla konwencjonalnych czytników. Tak więc w przypadku braku tego sterownik zawsze odmówi przeczytania treści.

![Napęd dysków nie pozwoli nikomu przejść przez ten ekran, dopóki nie zostanie włożony prawidłowy dysk.](system/disc.png) {.open-float}

Deweloperzy Modchipa odkryli, że napęd zawiera interfejs debugowania o nazwie 'Serial Writer' [@anti_piracy-disc], chociaż ten port jest zablokowany do momentu wprowadzenia *tajnego klucza*. Jednak znalezienie klucza było kwestią czasu. Gdy to się stało, modderzy byli w stanie wyłączyć ochronę przed kopiowaniem, a następnie opracowali modchip, który zautomatyzował ten proces.

Matsushita opublikowało kolejne wersje tego napędu, blokujące interfejs debugowania, jednak odkryto inne wady czytnika, aby ponownie go włączyć.

{.close-float}

Warto wspomnieć, że główną zaletą modchipów było zwykłe piractwo, ponieważ zawartość dysku jest nadal zaszyfrowana, więc do uruchomienia niestandardowego kodu potrzeba było więcej badań i narzędzi.

Z drugiej strony, GameCube homebrew był już możliwy do wykonania, wykorzystując [poprzednie wykryte exploity](gamecube#tab-5-3-honourable-mention) na poprzedniku.

### Szyfrowanie systemu

Jest to prawdopodobnie najbardziej złożona sekcja tej konsoli, ale jej nieustające badania otworzyły drzwi wielu utalentowanym programistom i niesamowitym programom.

Wewnętrzne zabezpieczania Wii były zaprojektowane wokół kilku szyfrów kryptograficznych (AES, RSA, ECC, SHA-1 i HMAC). Aby wyjaśnienia były łatwe do zrozumienia, przyjrzyjmy się każdej grupie z osobna:

#### Współdzielone szyfrowanie {.tabs.active}

Komunikacja między wieloma komponentami (NAND, dyskiem z grą i kartą SD) jest szyfrowana, aby uniknąć manipulacji. Nintendo wybrało system **klucza symetrycznego**, aby ją chronić [@anti_piracy-security], co oznacza, że Wii używa tego samego klucza do szyfrowania i odszyfrowywania swoich danych.

Starlet ma trzy klucze AES 128-bitowe przechowywane w swojej pamięci OTP [@anti_piracy-keys], które są zapisywane raz podczas produkcji:

- **Klucz Wspólny**: Globalny klucz generowany przez Nintendo, który można znaleźć na wszystkich Wii. Służy do odszyfrowywania pierwszej warstwy szyfrowania używanej w kanałach i grach na dyskach (od teraz będę się do nich odnosić jako **tytuły**).
- **Klucz SD**: Ten służy do szyfrowania/odszyfrowywania danych przesyłanych na kartę SD i tylko menu systemowe może wykonywać te transfery.
  - Nintendo przechowywało kopię tego klucza w systemie IOS bez wyraźnego powodu.
- **Klucz NAND**: Ten klucz jest generowany losowo podczas procesu produkcyjnego (co oznacza, że jest unikalny dla każdego Wii) i służy do ochrony chipa NAND.

Dzięki temu widzimy, że Starlet odpowiada za szyfrowanie/odszyfrowywanie sensownych treści, dlatego ten procesor jest jedynym, który ma dostęp do poufnych danych.

#### Łańcuch zaufania {.tab}

Tytuły zawierają kolejną warstwę zabezpieczeń, RSA-2048. Jest to **szyfr asymetryczny**, co oznacza, że potrzebujemy jednego klucza do zaszyfrowania zawartości i drugiego do jej odszyfrowania. W skrócie, pozwala to Nintendo na szyfrowanie tytułów za pomocą nieujawnionego klucza (zwanego 'kluczem prywatnym'), podczas gdy Wii odszyfrowuje je za pomocą 'klucza publicznego', który jest przechowywany w konsoli. Gdyby hakerzy zdobyli klucz publiczny, nie wystarczyłoby złamać systemu bezpieczeństwa, ponieważ nadal oczekuje się, że dane będą szyfrowane za pomocą klucza prywatnego, o którym wie tylko Nintendo.

Ponadto RSA jest wykorzystywany nie tylko do szyfrowania treści, ale również do sprawdzania integralności tego szyfrowania. Widzisz, Nintendo używa wielu kluczy, które są używane do podpisywania (szyfrowania) już zaszyfrowanych danych, tworząc łańcuch szyfrowania, którego jedynym celem jest upewnienie się, że:

- Każdy użyty klucz został autoryzowany przez Nintendo.
- Dane nie zostały zmienione i ponownie zaszyfrowane bez autoryzacji.

Podam przykład, jak to działa:

1. Nintendo tworzy klucz o nazwie `x`.
2. Nintendo programuje Starlet, aby ufała treściom podpisanym tylko kluczem `x`.
3. Jeśli Starlet stwierdzi, że będzie musiała odszyfrować tytuł kluczem `y`, zadziała tylko wtedy, gdy `y` zostanie podpisany kluczem `x`.

Nazywa się to **Łańcuchem zaufania**. Poza Wii technika ta jest powszechnie stosowana do ochrony większości naszej komunikacji na całym świecie (na przykład przeglądarki internetowe korzystające z protokołu HTTPS polegają na 'certyfikatach głównych' w celu weryfikacji autentyczności nieznanych certyfikatów).

#### Łańcuch Starlet'a {.tab}

OTP Starlet przechowuje klucze publiczne (co oznacza, że dla naszych celów może tylko odszyfrować i zweryfikować podpis treści). Jego łańcuch zaufania składa się z następujących kluczy [@anti_piracy-certificate]:

- **Klucz Główny**: Podpisuje klucz organu certyfikacji.
  - Starlet musi tylko przechowywać ten (publiczny) klucz, reszta może zostać odszyfrowana (i następnie zaufana), jeśli została podpisana tym kluczem.
- **Klucz Organu Certyfikacji** (CA): Podpisuje klucze XS i CP.
- **Klucz XS**: Ten klucz podpisuje 'tickety', rodzaj danych, który zawiera listę kluczy AES potrzebnych do odszyfrowania tytułów (zwanych 'kluczami tytułów').
- **Klucz CP**: Po odszyfrowaniu tytułu przy użyciu odpowiedniego klucza tytułu, klucz CP jest używany do podpisywania metadanych tytułu (tzw. 'TMD').
  - Chociaż nie podpisuje treści per se, metadane zawierają skrót SHA-1, którego Starlet używa do weryfikacji integralności tych danych.

Jak widać, wszystko to **pozwala Nintendo być wyłącznym dystrybutorem treści**, co może być dobre dla studiów gier zaniepokojonych piractwem.

#### Więcej kluczy {.tab}

Ten system zawiera również parę **kluczy prywatnych i publicznych ECC**. Kryptografia Krzywych Eliptycznych (ECC) to kolejny algorytm podobny do RSA. W tym przypadku służy tylko do podpisywania treści przesyłanych za pośrednictwem karty SD. To właśnie uniemożliwia wykorzystanie zawartości skopiowanej z jednego Wii na innym.

Klucz ECC jest podpisany przez *jeszcze inny* klucz publiczny RSA o nazwie **MS**, co pozwoli Starlet zaufać kluczowi ECC.

Ostatnim kluczem używanym przez tę konsolę jest **klucz HMAC**, który korzysta z innego algorytmu łączącego hashe SHA-1 i HMAC. Podczas procesu uruchamiania Starlet sprawdza, czy NAND nie został zmieniony przez sprzęt innej firmy. Aby to zrobić, oblicza hash SHA-1 pamięci NAND i porównuje go z hashem zakodowanym, aby sprawdzić, czy pasują. Oprócz tego zapisany skrót jest podpisany przy użyciu klucza HMAC, aby upewnić się, że jest autentyczny.

Na koniec klucz HMAC jest przechowywany w SEEPROM (poza Starlet), a nie w OTP.

#### Obserwacje {.tabs-close}

Po tym wszystkim warto wspomnieć, że gdy system uruchamia gry GameCube, **żadna z wymienionych metod szyfrowania nie jest używana**. Zamiast tego Starlet sprawdzi tylko, czy gra uzyskuje dostęp tylko do wyznaczonych lokalizacji pamięci. Dzieje się tak, ponieważ 1/4 pamięci RAM GDDR3 jest przydzielona do symulacji starego [ARAM](gamecube#audio).

### Upadek szyfrowania

Zacznijmy od kluczy AES, algorytm może być trudny do złamania, ale jeśli klucze zostaną w jakiś sposób wyodrębnione (zwłaszcza wspólny klucz), ta warstwa bezpieczeństwa zostanie natychmiast anulowana. Dlatego głównym wyzwaniem jest **jak je wyodrębnić**.

![Diagram bezpieczeństwa Starlet.](encryption.png) {.open-float}

Więc, grupa hakerów o nazwie **Team Twiizers** odkryła, że brak podpisów w trybie GameCube może być obiecującą powierzchnią do ataku [@cpu-ccc]. Nie tylko odkryli, że **3/4 z tego RAM GDDR3 nie zostaje wyczyszczone** po uruchomieniu programu GC, ale również odkryli, że poprzez pomostowanie niektórych punktów adresowych na płycie głównej (przy użyciu pary pęset), mogą zamienić wybrane banki RAM GDDR3, umożliwiając im dostęp do obszarów ograniczonych. I oto znaleziono tam klucze AES.

{.close-float}

Nie zapominajmy, że pozwala to odszyfrować tylko 'pierwszą warstwę' bezpieczeństwa, ale aby uruchomić niepodpisane programy (Homebrew), RSA również musi zostać złamany. Niestety może to być obliczeniowo niemożliwe... Chyba że istnieją wady w jego realizacji. Cóż, Team Twizzers nie poprzestali na tym, więc zaczęli analizować sposób kodowania IOS, skupiając się na jego funkcjach weryfikacji podpisów.

Weryfikacja podpisu RSA, bez wchodzenia w szczegóły, polega na porównaniu skrótu obliczonej operacji RSA z odszyfrowanym podpisem. Po kilku manipulacjach grupa odkryła coś zabawnego: **Nintendo zaimplementowało tę funkcję za pomocą `strncmp`** (porównanie 'ciągu' w C).

Dla osób nieznających C, `strncmp` jest procedurą używaną do sprawdzania, czy dwa łańcuchy są równe. Ta kompozycja zawiera: dwa łańcuchy i jedną liczbę całkowitą, ta ostatnia określa liczbę znaków do porównania. Następnie, `strncmp` zaczyna porównywać każdy znak aż do osiągnięcia końca dowolnego ciągu (lub licznika znaków). Łańcuchy w C to po prostu ciąg znaków zakończony znakiem `\0`, co oznacza, że `strncmp` przestaje porównywać, gdy dowolny łańcuch osiągnie `\0`. Dlatego też, tworząc tytuł Wii w taki sposób, aby jego hash zawierał na początku `\0`, obliczenia RSA Starleta spowodują, że porównane zostaną bardzo krótkie hashe (lub nawet puste) ze znacznymi szansami na kolizję (różne dane, które dają tę samą wartość hash). Ostatecznie, przy użyciu możliwej ilości brute-forceowania, umożliwiło to porównanie i zwrócenie `równość`... **Tytuł jest podpisany!**

Jakby tego było mało, ta luka została wykryta w wielu wersjach IOS - a nawet w procedurach znalezionych na boot1 i boot2!

### Świt Homebrew

Po tym pozostało tylko jedno: uczynić exploita trwałym i zaimplementować 'przyjazne dla użytkownika' narzędzie, aby mógł bezproblemowo uruchamiać niestandardowe programy.

![Uruchamianie aplikacji innych firm początkowo odbywało się przy użyciu sfałszowanego zapisu gry.](system/twilight_hack.png) {.open-float}

Do tej pory exploity te wymagały użycia dodatkowego sprzętu, więc nie każdy użytkownik mógł z niego skorzystać... Dopóki Team Twizzers nie odkryli *kolejnego* exploita: Przepełnienia bufora gry.

Mam na myśli słynną **The Legend of Zelda: Twilight Princess** (nawiasem mówiąc, grę firmy Nintendo). TT odkryli, że plik zapisu gry może zostać zmodyfikowany, aby przepełnić liczbę znaków używanych do nazwania konia gracza. Tak więc, gdy gracz spróbuje odczytać przepełnioną nazwę, wywoła to reakcję łańcuchową kończącą się wykonaniem dowolnego kodu. Można to wykorzystać do uruchomienia, powiedzmy, programu ładującego.

{.close-float}

Ponieważ podpisy można było teraz sfałszować, ten spreparowany plik zapisu można było łatwo rozpowszechniać w sieci, aby inne osoby mogły z niego korzystać. W rezultacie społeczność homebrew mogła teraz uruchamiać swoje niestandardowe oprogramowanie.

### Stan stały

Podczas dalszej inżynierii wstecznej IOS odkryto, że sygnatury są sprawdzane tylko podczas instalacji tytułów, a nie podczas ich wykonywania.

::: {.subfigures .open-float .tabs-nested .tab-float}

![](system/homebrew_channel.png){.tab-nested .active title="Baner"}

![](system/homebrew/menu.png){.tab-nested title="Wewnątrz"}

_Nieoficjalny_ kanał Homebrew (2008).<br>Najbardziej przyjazny dla użytkownika hack ze wszystkich.

:::

Tak więc TT ponownie zabłysnęli. Ostrożnie stworzyli kanał do zainstalowania, który mógł załadować dowolne programy z karty SD. Gdyby kanał ten został zainstalowany przed podjęciem przez Nintendo działań mających na celu złagodzenie problemów związanych z bezpieczeństwem, wówczas Wii na stałe cieszyłaby się homebrew (niezależnie od łatania przez Nintendo ich błędów z podpisami w przyszłości, co uczynili).

**Kanał Homebrew** był tego wynikiem, ten tytuł pozwolił każdemu użytkownikowi na odpalenie programów homebrew, które mogły korzystać z pełnej kontroli nad tym systemem (z wszystkimi tego konsekwencjami).

{.close-float}

### Odpowiedź Nintendo

Z oczywistych powodów Nintendo wydała wiele aktualizacji systemowych, które naprawiły exploit z podpisami w wielu wersjach IOS, oraz zajęli się również wadliwymi etapami rozruchu, wypuszczając nowe wersje sprzętowe.

![Przybędzie ich wiele.](system/updates.png) {.open-float}

W systemie tym wykryto jednak inne zasadnicze wady:

- Broadway może ponownie uruchomić Starlet z dowolną wersją IOS bez dodatkowych uprawnień, pozwalając na wykorzystanie wersji niezpatchowanych.
- Ukryte API IOS nadal mogą być używane bez specjalnych uprawnień, umożliwiając jeszcze większą nieupoważnioną kontrolę sprzętu.
- Napęd dysków może otrzymać polecenia odczytu konwencjonalnych DVD, a niektóre IOS zawierają ukryte wywołania tych poleceń. Było to szczególnie niepokojące z powodu piractwa.

{.close-float}

Więc, podsumowując, po tym wszystkim jedyne co pozostało to zabawa w kotka i myszkę. W ciągu następnych miesięcy odkryto kolejne różne exploity, które następnie Nintendo próbowało patchować jeden po drugim. Ta 'zabawa' trwała aż do końca życia konsoli i nie wydano już żadnych aktualizacji. Możemy założyć, że tutaj wygrała mysz.

Na chwilę pisania tego artykułu, luki tu wspomniane zostały załatane, ale również zastąpione obecnie działającymi.

Myślę, że nie ma żadnych sporów o wpływ, jaki scena hakerska wywarła na ten system, i kto może zapomnieć o ogromnej ilości homebrew która była udostępniona (istniał nawet 'sklep' homebrew, który był szybszy i oferował więcej wolności niż oficjalny Kanał Sklepowy Wii).

## To wszystko ludziska

Szczęśliwego nowego roku 2020!

Ten artykuł zajął mi trochę czasu, naiwnie przewidywałem, że skoro większość treści była już napisana dla GameCube'a, będę musiał tylko napisać krótkie akapity i dodać linki tu i tam...

Myślę, że wyszedł bardziej informacyjny niż oczekiwałem, więc mam nadzieję, że spodobała ci się jego lektura.

Do następnego razu!  
Rodrigo
