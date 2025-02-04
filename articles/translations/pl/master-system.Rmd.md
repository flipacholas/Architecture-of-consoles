---
short_title: Architektura Sega Master System
long_title: Architektura Sega Master System
name: Sega Master System
subtitle: Potężny sam w sobie
date: 2020-10-12
release_date: 1985-10-20
generation: 3
cover: mastersystem
javascript:
  - 'audioswitcher'
published: true
top_tabs:
  Model:
    caption: "Sega Master System.<br>Wydany 20.10.1985 w Japonii, 09.1986 w Ameryce i 06.1987 w Europie"
  Motherboard:
    caption: "Chip dźwiękowy jest osadzony w procesorze wideo."
  Diagram:
    caption: ""
#Historical
aliases:
  - /projects/consoles/master-system/
---

## Szybkie wprowadzenie

Master System pochodzi z długiej linii sukcesji. To, co zaczęło się jako kolekcja gotowych komponentów, teraz zyskało nową tożsamość dzięki inżynierii Sega.

## Modele i warianty

Na początku byłem trochę zdezorientowany, czytając o różnych modelach, które firma Sega sprzedawała, więc oto podsumowanie głównych omawianych modeli, aby uniknąć dalszych nieporozumień:

- **Sega Mark III**: Pierwsza konsola z tą architekturą, wydana tylko w Japonii.
- **Sega Master System** (Europa i Ameryka): Rebranding Mark III z nową obudową, chipem BIOS ROM i innym gniazdem na kartridże.
- **Sega Master System** (Japonia): Europejski/amerykański system Master z gniazdem kartridża Mark III, nowym chipem FM i gniazdem jack na 'okulary 3D'. Brakuje jednak przycisku `RESET`.

Odtąd będę używał terminu 'Master System' lub 'SMS' w odniesieniu do nich wszystkich, z wyjątkiem mówienia o ekskluzywnych funkcjach konkretnego modelu.

## {.supporting-imagery}

## CPU

Sega zdecydowała się na pełnoprawny procesor **Zilog Z80** pracujący z częstotliwością **~3,58 MHz**. Popularny wybór używany przez maszyny takie jak ZX Spectrum i Amstrad CPC.

Procesor Z80 ma ciekawą historię, ponieważ jego autorem jest nikt inny jak twórcy Intela 8080, którzy przestali interesować się kierunkiem, w jakim zmierza Intel, i w 1974 roku postanowili założyć własną firmę krzemową, Zilog. Ich debiutujący produkt można uznać za nieoficjalnego następcę Intel 8080, który zawiera:

- **ISA Z80**: Zestaw instrukcji zgodny z Intel 8080, ale rozszerzony o wiele innych instrukcji. Obsługuje słowa **8-bitowe**.
- **8-bitowa magistrala danych**, idealna do przenoszenia 8-bitowych danych. Większe wartości zużywają dodatkowe cykle CPU.
- **Czternaście 8-bitowych rejestrów ogólnego przeznaczenia** [@cpu-registers]: To całkiem dużo, biorąc pod uwagę, ze [CPU 6502](nes#cpu) miał tylko trzy (`X`, `Y` i `A`). Jednakże rejestr Z80 zawiera pewne zastrzeżenia (lub zalety, w zależności od tego, jak na to patrzysz):
  - Tylko **siedem rejestrów** jest dostępnych jednocześnie, pozostałe siedem jest nazywanych 'Rejestrami Alternatywnymi' i musi być zamieniane z pierwszym zestawem, aby mieć do nich dostęp. To jest powiązane z zasadą [przełączania banków](nes#going-beyond-existing-capabilities). Z80 dostarcza również specjalistyczne instrukcje, takie jak `EX` i `EXX`, aby przenieść zawartość pomiędzy każdym zestawem.
  - W ramach każdego zestawu sześć 8-bitowych rejestrów może być również zestawionych razem, aby dostarczyć do **trzech 16-bitowych rejestrów**, umożliwiających manipulowania większymi wartościami.
- **16-bitowa magistrala**, konsekwencje są wyjaśnione w następnej sekcji.
- **4-bitowe ALU**: Może to być nieco szokujące, ale oznacza to po prostu, że obliczenia wykonywane na wartościach 8-bitowych wymagają dwukrotnie większej liczby cykli.

Zdjęcie płyty głównej na początku artykułu przedstawia procesor NEC D780C-1, oznacza to po prostu, że SEGA używała chipów produkcji różnych producentów, inne wersje zawierały nawet chip wyprodukowany przez Zilog. Ale w tym artykule nie ma znaczenia, kto wyprodukował procesor, ponieważ wewnętrzne funkcje pozostają takie same.

### Porównanie względne

Zauważ, że procesor 6502 (znaleziony w [NES](nes#cpu)) działa tylko na ~2 MHz. Jest to około połowy prędkości chipu Master System. W połączeniu z większym rejestrem Z80 można by pomyśleć, że Master System powinien bez wątpienia przewyższać NES.

I odwrotnie, jeśli się w to zgłębimy, zobaczysz, że 6502 zawiera większe (8-bitowe) ALU. Tak więc operacje arytmetyczne, które mogą zająć tylko dwa cykle w 6502, **zużywają cztery** na Z80. Ostatecznie, pokazuje to że względne cechy takie jak prędkość CPU lub rozmiar rejestru, gdy analizowane są osobno, mogą być zwodnicze. Zarówno Z80, jak i 6502 mają swoje mocne i słabe strony, jeśli chodzi o różne zadania. Wszystko zależy od umiejętności programisty.

### Dostępna pamięć

Jak wspomniano wcześniej, Z80 ma 16-bitową magistralę adresową, więc procesor może wykryć do **64 KB pamięci**. W mapie pamięci znajdziesz **8 KB pamięci RAM** do ogólnego użytku [@cpu-map], która jest dublowana w kolejnym bloku 8 KB. Wreszcie mapowane jest również **do 48 KB ROM-u gier**.

### Dostęp do pozostałych komponentów

Jak można wyczytać z poprzedniego akapitu, w przestrzeni adresowej znajduje się tylko główna pamięć RAM i część pamięci ROM kartridża, więc jak program może uzyskać dostęp do innych komponentów? Cóż, w przeciwieństwie do [Famicom'u/NES-a](nes) Nintendo, nie cały sprzęt Master System jest mapowany przy użyciu lokalizacji pamięci. Zamiast tego niektóre urządzenia peryferyjne znajdują się w **przestrzeni WE/WY**.

Dzieje się tak, ponieważ rodzina Z80 zawiera interesującą funkcję o nazwie **porty WE/WY**, która umożliwia procesorowi komunikację z innym sprzętem bez wyczerpania adresów pamięci. W tym celu istnieje oddzielna przestrzeń adresowa dla 'urządzeń WE/WY' o nazwie **porty** i obie mają tę samą magistralę danych i adresową. Różnica polega jednak na tym, że porty są odczytywane i zapisywane odpowiednio za pomocą instrukcji `IN` i `OUT` - w przeciwieństwie do tradycyjnej instrukcji ładowania/przechowywania (`LD`).

Kiedy wykonywana jest instrukcja `IN` lub `OUT`, Z80 ustawia linie adresowe wskazujące na urządzenie peryferyjne (które może być na przykład klawiaturą), flaguje swój pin `IORQ` wskazujący, że żądanie I/O zostało zainicjowane i ostatecznie oznacza pin `RD` lub pin `WR` zależnie od tego, czy jest to odpowiednio kod instrukcji `IN` czy `OUT`. Zaadresowane urządzenie peryferyjne musi ręcznie sprawdzić magistralę adresową i piny WE/WY oraz wykonać wymaganą operację. W przypadku instrukcji `IN`, CPU zapisze odebraną wartość w predefiniowanym rejestrze.

![Układ adresowania SMS.](addressing.png)

Sposób, w jaki SEGA połączyła procesor z resztą komponentów, umożliwia nie tylko dostęp do wartości, ale także pokazywanie/ukrywanie niektórych komponentów przed pojawieniem się na mapie pamięci.

Co ciekawe, [Game Boy](game-boy#cpu) miał 'wariant' Z80, który całkowicie pomijał porty WE/WY. Musiał więc zmieścić wszystko w mapie pamięci.

### Kompatybilność wsteczna

Architektura tej konsoli jest bardzo podobna do jej poprzednika, **Sega SG-1000**, więc Master System zdołał uzyskać wsteczną kompatybilność z SG-1000. Chociaż dotyczy to tylko japońskiego wariantu, ponieważ pozostałe zawierają inne gniazdo kartridża.

## Grafika

Rysunki na ekranie są tworzone przez niestandardowy układ o nazwie **Video Display Processor** lub 'VDP'. Wewnętrznie ma ten sam projekt, co Texas Instrument TMS9918 (używany w SG-1000) [@graphics-texas], ale wzbogacony o więcej funkcji, które omówimy w następnych sekcjach.

### Organizowanie treści

![Architektura pamięci VDP.](vdp.png)

Obok VDP znajduje się **16 KB VRAM**, do którego tylko VDP może uzyskać dostęp za pomocą **16-bitowej magistrali danych** (Sega zmodyfikowała oryginalny projekt, aby uzyskać dostęp do dwóch układów pamięci z 8-bitowymi magistralami w tym samym czasie [@cpu-service]). Jeśli ponownie spojrzysz na obraz płyty głównej, zauważysz, że układy RAM i VRAM są mniej więcej takie same, z wyjątkiem tego, że VRAM używa modelu układu kończącego się na '20', który ma mniejsze opóźnienie [@cpu-nec].

W przypadku systemu Master, VRAM zawiera wszystko, czego VDP będzie wymagał do renderowania (z wyjątkiem Kolor RAM-u). Procesor wypełnia VRAM zapisując do określonych rejestrów VDP, które z kolei przekazują wartości do VRAM-u. Ponieważ dostęp do VDP odbywa się za pomocą portów WE/WY, CPU musi używać instrukcji `IN` i `OUT`.

### Konstruowanie klatki

VDP renderuje klatki z rozdzielczością **do 256x192 pikseli**. Kolejne poprawki dodały obsługę 256x224 px i 256x240 px, jednak aby zachować kompatybilność ze wszystkimi modelami, deweloperzy trzymali się standardowej rozdzielczości. Ten chip ma ten sam *tryb działania*, co [PPU](nes#constructing-the-frame) firmy Nintendo, innymi słowy, grafika jest renderowana na bierząco.

Z drugiej strony VDP ma cztery różne tryby działania, które zmieniają charakterystykę klatki (głębia kolorów i rozdzielczość):

- **Tryb 0 do III**: Odziedziczony po TMS9918 znalezionym w SG-1000. Dołączone dla wstecznej kompatybilności, chociaż każda gra SMS może z nich korzystać.
- **Tryb IV**: Tryb natywny Master System, który umożliwia dostęp do wszystkich najnowocześniejszych funkcji VDP. Podczas tej analizy skupimy się na nim!

Zobaczmy teraz, jak rysowane są klatki krok po kroku. W tym celu pożyczę zasoby z *Sonic The Hedgehog*. Ponadto, aby ułatwić wyjaśnienie, skupię się na standardowym układzie pamięci, który Sega sugeruje do organizowania zawartości graficznej (pamiętaj tylko, że VDP jest pod tym względem bardzo elastyczny, więc gry mogą go optymalizować).

#### Kafelki {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Wszystkie kafelki.](sonic/tiles.png){.active title="Wszystkie"}

![Pojedynczy kafelek.](sonic/tile.png){title="Pojedynczy"}

Kafelki znalezione w VRAM.

:::

Tryb IV jest oparty na **systemie kafelków**. Aby przypomnieć [poprzednie wyjaśnienia](nes#tab-1-1-tiles) dotyczące silników kafelków, kafelki to po prostu **mapy bitowe 8x8 pikseli**, które renderer pobiera, aby narysować grafikę gry. W przypadku VDP klatka składa się z dwóch płaszczyzn, warstwy tła i warstwy sprite.

Wewnątrz VRAM znajduje się obszar dedykowany kafelkom nazywany **Character generator** (Sega nazywa kafelki 'Characters') i jest ustawiony by mieć **długość 14 KB**. Każdy kafelek zajmuje 32 bajty, dzięki czemu możemy przechowywać do 448 kafelków.

Na każdym kafelku zdefiniowane są 64 piksele, VDP zakłada, że każdy piksel jest zdefiniowany przez 4 bity, co oznacza, że **można wybrać do 16 kolorów**. Te bity odnoszą się do pojedynczego wpisu w **Kolor RAM-ie** lub 'CRAM'. Ten obszar znajduje się wewnątrz VDP i przechowuje palety kolorów. Systemy palety kolorów pomagają zmniejszyć rozmiar kafelków w pamięci i umożliwiają programistom zmianę kolorów bez przechowywania wielu kopii.

Kolor RAM przechowuje **dwie palety po 16 kolorów każda**. Każdy wpis ma szerokość 6 bitów, a każdy zestaw 2 bitów definiuje jeden kolor z modelu RGB. Oznacza to, że do wyboru są 64 kolory.

#### Warstwa Tła {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Przydzielona mapa Ekranu.](sonic/tilemap.png){.active title="Całkowita"}

![Przydzielona mapa Ekranu z zaznaczonym wybranym obszarem.](sonic/tilemap_marked.png){title="Wybrany"}

Przydzielona mapa Ekranu z zaznaczonym wybranym obszarem.

:::

Warstwa tła to duża płaszczyzna, na której rysowane są statyczne kafelki. Aby umieścić coś tutaj, istnieje inny obszar pamięci VRAM o nazwie **Mapa Ekranu** [ang. Screen map], który ma 1,75 KB.

Umożliwia to programistom zbudowanie warstwy 896 kafelków (32x28 kafelków) [@graphics-rdp], ale jeśli policzymy, zobaczymy, że ta warstwa jest większa niż rozdzielczość wyświetlania tej konsoli. Rzeczywistość jest taka, że widocznych jest tylko 768 kafelków (32x24 kafelki), więc widoczny obszar jest ręcznie wybierany zgodnie z wolą programisty. W związku z tym, powoli zmieniając współrzędne X i Y wybranego obszaru, uzyskuje się **efekt przewijania**.

Każdy wpis mapy ma 2 bajty szerokości (tak szerokie, jak magistrala danych VDP) i zawiera adres kafelka w 'Character generator' oraz następujące atrybuty:

- **Odwracanie w poziomie i w pionie**.
- **Bit priorytetu** (czy narysować część, czy całą płytkę przed sprite'ami).
- Użyta **paleta kolorów**.

Co ciekawe, we wpisie znajdują się 3 nieużywane bity, które gra może wykorzystać do innych celów (np. dodatkowe flagi wspomagające silnik gry).

#### Sprite'y {.tab}

![Renderowana warstwa Sprite.](sonic/sprites.png) {.tab-float.pixel}

Sprite to po prostu kafelki, które poruszają się swobodnie. VDP może zrastrować **do 64 sprite'ów** przy użyciu jednego kafelka (8x8 px) lub dwóch kafelków ułożonych pionowo (8x16 px).

**Tabela Atrybutów Sprite** [ang. Sprite Attribute Table] to 256-bajtowy obszar w VRAM, który zawiera tablicę wszystkich zdefiniowanych sprite'ów, jej wpisy są podobne do warstwy tła, z wyjątkiem tego, że każdy sprite zawiera dwie dodatkowe wartości reprezentujące współrzędne X/Y.

VDP jest ograniczone do **do ośmiu sprite’ów na poziomą linię skanowania** [@graphics-macdonald]. Ponadto, jeśli wiele sprite'ów nakłada się na siebie, wyświetlany będzie pierwszy na liście.

#### Rezultat {.tab}

![Tada!](sonic/result.png) {.tab-float.pixel}

VDP automatycznie łączy dwie warstwy, tworząc ostateczną klatkę. Proces renderowania odbywa się linia po linii skanowania, więc VDP tak naprawdę nie wie, jak będzie wyglądać klatka, widzi ją tylko użytkownik, gdy obraz jest tworzony na telewizorze.

Jeśli spojrzysz na przykładowy obraz, możesz zauważyć, że klatka ma pionową kolumnę po lewej stronie. Dzieje się tak, ponieważ mapa ekranu jest wystarczająco wysoka, aby umożliwić przewijanie w pionie bez powstawania artefaktów, **ale nie jest wystarczająco szeroka, aby przewijać w poziomie**. Tak więc VDP może **zamaskować** lewą stronę kolumną o rozmiarze 8 pikseli, aby chronić obraz przed pokazywaniem kafelków pośrednich.

Aby zaktualizować grafikę dla następnej klatki bez przerywania aktualnie wyświetlanego obrazu, VDP wysyła dwa typy **przerwań** do procesora. Jedno, które informuje, że telewizor CRT zakończył przesyłanie wybranej liczby linii skanowania (nazywane **przerwaniem poziomym**), a drugie, gdy CRT zakończył rysowanie ostatniej linii skanowania (nazywane **przerwaniem pionowym**) oznaczające zakończenie klatki. Podczas tych zdarzeń wiązka CRT jest ponownie przydzielana, aby narysować następną linię skanowania (**interwał wygaszania**), więc jakakolwiek zmiana stanu VDP nie zrujnuje istniejącego obrazu. Wygaszanie poziome ma krótsze ramy czasowe niż wygaszanie pionowe, ale nadal pozwala na zmianę takich rzeczy, jak paleta kolorów. To może przynieść pewne efekty.

### Sekrety i ograniczenia {.tabs-close}

Na pierwszy rzut oka VDP może wydawać się kolejnym chipem o minimalnej funkcjonalności, którą teraz uważamy za pewnik. Odwróciło to jednak wiele uwagi od oferty Nintendo w tamtym czasie. A więc, dlaczego tak było?

#### Wykrywanie kolizji {.tabs.active}

Po pierwsze, VDP był w stanie **określić, czy dwa sprite'y kolidują ze sobą**. Dokonano tego sprawdzając jego rejestr `status` [@graphics-collision]. Nie mógł wykryć, które konkretnie, ale to ograniczenie zostało rozwiązane poprzez odczytywanie również innych rejestrów, takich jak `licznik linii skanowania` [ang. scan-line counter]. Można to sobie wyobrazić jako metodę 'triangulacji'.

Ta funkcja nie jest jednak nowa, ponieważ TMS9918 również ją zawierał, dlatego SG-1000 również miał wykrywanie kolizji.

#### Potrzeba modułowości {.tab}

Kiedy wcześniej analizowałem projekt PPU Nintendo, położyłem nacisk na jego architekturę pamięci wewnętrznej. Chociaż był ograniczony, niektóre ograniczenia były [całkiem korzystne](nes#secrets-and-limitations), ponieważ umożliwiły rozbudowę systemu za pomocą dodatkowego sprzętu zawartego w kartridżu z grą, co pozwalało obniżyć koszty.

VDP nie korzysta z tego podejścia modułowego. Zamiast tego Sega wprowadziła inne rozwiązanie, które z kolei oszczędza koszty kartridży. Przykładem tego są mniejsza warstwa tła i przerwania poziome.

#### Okulary 3D {.tab}

![Okulary 3-D Sega [@photography-amos].<br>Wariant amerykański połączony przez port karty.](glasses.png) {.tab-float.no-borders}

Okazuje się, że Sega dostarczyła także **'okulary 3D'** jako oficjalne akcesorium! Okulary działały zsynchronizowane z CRT. W trakcie rozgrywki gra zmienia położenie obiektów pomiędzy klatkami. Każda soczewka ma ekran LCD, który wyłącza się na czarno, aby zablokować widok. Tak więc właściwa kombinacja migotania grafiki i naprzemiennych przesłon w końcu tworzy w głowie stereoskopowy obraz. Tak więc otrzymujemy efekt '3D'.

Migawki są sterowane z kilku adresów pamięci, ale żaden z nich nie poinformuje konsoli, czy są podłączone okulary, więc gry obsługujące to akcesorium zawierają opcję ustawień, która pozwala użytkownikowi ręcznie aktywować tę funkcję.

Kontrolery LCD są połączone kablem typu jack, który jest podłączony do konsoli. Wersje europejskie i amerykańskie nie zawierały wejścia jack, więc polegają na porcie karty do podłączenia adaptera (więcej o gnieździe na kartę zobaczymy później).

### Wyjście Wideo {.tabs-close}

Złącze wyjścia wideo tego systemu jest *niesamowicie* poręczne. Wysyła sygnały **kompozytowe** i **RGB**, które można sobie wyobrazić jako dwie 'ekstremalne' jakości wideo.

Minusem jest to, że nie zapewnia 'synchronizacji kompozytowej' [ang. composite sync], więc korzystanie z RGB będzie wymagało przechwycenia sygnału synchronizacji z kompozytu, a jego jakość nie jest optymalna.

## Dźwięk

Możliwości audio tej konsoli są w dużym stopniu zgodne z resztą konsol z lat 80-tych. Wewnątrz układu VDP znajduje się nieco dostosowana wersja **Texas Instruments SN76489** [@audio-sn76489], która jest **Programowalnym Generatorem Dźwięku** lub 'PSG'. Jest to ten sam typ, który jest używany w NES/Famicom, choć ma inne funkcje.

### Funkcjonalność

PSG może syntetyzować tylko ograniczony zestaw fal, każdy kanał przydziela pojedyńczą falę. Wcześniej przedstawiłem kilka PSG w artykule [NES](nes#audio) i [Game Boy](game-boy#audio), jeśli chcesz przeczytać więcej o tego rodzaju syntezie dźwięku.

W SMS, PSG programuje się zmieniając swój zestaw zewnętrznych rejestrów za pomocą wyżej wymienionych portów I/O.

Przyjrzyjmy się teraz każdemu typowi fali, jaki może wygenerować SN76489:

#### Impuls {.tabs.active}

![Sonic The Hedgehog (1991).<br>Kanal impulsu.](pulse){.tab-float video="true"}

Fale impulsowe/tonowe wytwarzają ten kultowy dźwięk z generacji 8-bitowej. Fala dźwiękowa jest generowana przez podtrzymanie napięcia, utrzymywanie go na stałym poziomie, a następnie całkowite obniżenie. Powtarzaj to w stałym tempie, a usłyszysz dźwięk.

Okres fali określi częstotliwość dźwięku (nuta). Jego cykl pracy wpływa na barwę.

Wszystko to jest obsługiwane przez PSG, które może wytwarzać **trzy fale impulsowe w tym samym czasie**. W szczególności SN76489 udostępnia 10-bitowy licznik na każdym kanale, który będzie używany wewnętrznie do zatrzaskiwania z dużą szybkością, co skutkuje falą impulsową o programowalnej częstotliwości.

#### Szum {.tab}

![Sonic The Hedgehog (1991).<br>Kanal szum.](noise){.tab-float video="true"}

Szum to rodzaj sygnału, który jest związany z zakłóceniami. Kiedy jest wysyłany do głośnika, brzmi jak statyczny.

SN76489 zawiera rejestr przesuwny z liniowym sprzężeniem zwrotnym lub 'LFSR', który jest zasilany pewnymi danymi wejściowymi i przełączany w celu wygenerowania sygnału pseudolosowego. Zawiera pewne parametry do przełączania między **szumem białym** i **szumem okresowym**, ten ostatni kradnie trzeci kanał impulsowy i podaje go przez LFSR, aby wytworzyć dźwięk podobny do basu (impuls o cztery oktawy niższy).

Używany oscylator można również zmienić, aby zmienić wysokość tonu, do wyboru są cztery opcje.

Gry zwykle używają kanału szumu do **perkusji i/lub efektów dźwiękowych**.

#### Zmiksowane {.tab}

![Sonic The Hedgehog (1991).<br>Wszystkie kanały audio.](pulse_complete){.tab-float video="true"}

Do tej pory omówiliśmy, co każdy kanał robi osobno, ale telewizor będzie po prostu odbierał sygnał mono ze wszystkimi kanałami zmiksowanymi przez PSG.

Wreszcie, układ zawiera również programowalne tłumiki używane do obniżania decybeli każdego kanału, skutecznie działające jako **regulacja głośności**.

### Sekrety i ograniczenia {.tabs-close}

Podobnie jak VDP, PSG jest oczywisty, ale kryje kilka interesujących funkcji:

#### Rozszerzenie FM {.tabs.active}

![Double Dragon (1987).](){audio_switcher="true" src1="psg" src2="fm" label1="PSG" label2="FM" .float}

Japońska wersja Master System zawiera dodatkowy układ audio firmy Yamaha o nazwie **YM2413**. Drastycznie różni się od poprzedniego PSG, ponieważ do generowania dźwięku wykorzystuje technikę **modulacji częstotliwości**. Jeśli jesteś zainteresowany, pokrótce wyjaśniłem, jak to działa, w [artykule o Mega Drive](mega-drive-genesis#tab-2-1-yamaha-ym2612).

Ten dokładny układ ma **dziewięć kanałów** dźwięku. Każdy kanał może wybrać jeden z 16 zaprogramowanych instrumentów lub zdefiniować własny, programując nośną i modulator. Niestety w danym momencie dozwolony jest tylko jeden instrument niestandardowy. Z drugiej strony nowy instrument zapewnia kilka interesujących funkcji, takich jak sterowanie obwiednią ADSR i sprzężenie zwrotne.

YM2413 ma również drugi tryb działania zwany **tryb rytmu**, który zamiast tego zapewnia **sześć kanałów** uzupełnionych o **pięć dodatkowych kanałów** tylko dla instrumentów rytmicznych.

Ostateczny dźwięk jest generowany przez YM2413, który miksuje własne kanały z kanałami PSG.

Wersja Mark III nie zawierała tego układu, ale FM był dostępny jako jednostka rozszerzająca o nazwie **FM Sound Unit**. Reszta (europejski i amerykański Master Systems) musiała trzymać się PSG, chociaż w końcu pojawiły się instalacje innych firm.

#### Dokładność emulacji {.tab}

![Porównanie fal impulsowych za pomocą emulatorów.<br>NES pokazuje pewne zanikanie, podczas gdy SMS mają kształt kwadratu.](decay.jpg) {.tab-float}

Czytając SMS Power (stronę internetową, która zbiera wiele informacji technicznych o systemie), natknąłem się na jedną interesującą sekcję o nazwie 'The imperfect SN76489' [@audio-sn76489] który omawia pewne rozbieżności, które napotkałem podczas pisania artykułu.

Jeśli ponownie spojrzysz na przykładowy film przedstawiający falę impulsową, zobaczysz, że jest ona wizualizowana za pomocą prawie idealnej fali prostokątnej. Generator impulsów działa poprzez zatrzaśnięcie napięcia w celu wygenerowania tonu. Jednak w obwodach elektrycznych komponenty nie przechodzą natychmiast od zera do jedynki (lub odwrotnie), zawsze występuje okres przejściowy (głównie przypisywany istnieniu filtrów w obwodzie elektrycznym).

Teraz używam emulatorów do przechwytywania oddzielnych kanałów i unikania zakłóceń podczas nagrywania, chociaż emulatory nie zawsze uwzględniają czynnik przejściowy, więc ich wynik może być bliższy 'idealnemu scenariuszowi' niż realistycznemu w elektronice lat 80-tych. Spójrz na przykładowy obraz. Obie próbki pochodzą z emulatorów, ale wydaje się, że NES-owa potencjalnie wykazuje zachowanie bliższe światu analogowemu.

W tej chwili nie mam niezbędnych narzędzi, aby potwierdzić, czy SMS powinien wykazywać podobne zachowanie. Ale jeśli tak, nie oznacza to, że to, co usłyszałeś, jest niepoprawne, tylko ma nieco inną głośność (ledwo zauważalnej).

#### Odtwarzanie próbek {.tab}

![Alex Kidd - The Lost Stars (1986).<br>1-bitowa próbka PCM.](ball){.tab-float video="true"}

Chociaż SN76489 nie ma [kanału PCM](nes#tab-3-4-sample) do odtwarzania próbek, jest kilka sztuczek które można wykorzystać do symulacji tej funkcji.

Opierają się one na kanałach impulsowych, odkryto, że jeśli poziom tonu jest ustalony na `1`, poziom głośności (który zmienia amplitudę) będzie warunkował kształt przebiegu.

smspower.org opisuje różne projekty, które pozwalają na odtwarzanie 1-bitowych, 4-bitowych i 8-bitowych próbek PCM. Chociaż wymagania dotyczące pamięci gwałtownie wzrastają wraz z większą rozdzielczością i szybkością próbki, najlepsze zastosowania tych exploitów można znaleźć w grach homebrew.

Warto zauważyć, że próbki strumieniowe pochłaniają dużą liczbę cykli procesora, a w tym systemie jest tylko jeden procesor, więc gra może wymagać zatrzymania na krótki czas.

## WE/WY {.tabs-close}

Podobnie jak inne systemy tej generacji, procesor odpowiada głównie za obsługę WE/WY. W tym przypadku procesor Z80 jest wyjątkowy ze względu na specjalne adresowanie WE/WY, ale nadal będą cykle procesora spędzane na przemieszczaniu bitów wokół komponentów.

Z drugiej strony, SMS używa dedykowanego układu **kontrolera WE/WY**, aby nie tylko łączyć kontrolery, ale także włączać i wyłączać części systemu, co zmieni mapę adresową. Co więcej, ten kontroler jest niezbędny do obsługi rozszerzenia FM, ponieważ moduł FM udostępnia porty, które są w konflikcie z resztą systemu (w sensie, bez interwencji układu WE/WY).

### Dostępne interfejsy

Oprócz dwóch portów kontrolerów, system zawiera jedno własnościowe gniazdo kartridży, jedno gniazdo 'Sega Card' i jedno gniazdo rozszerzeń zarezerwowane dla 'przyszłych akcesoriów'. To ostatnie nigdy nie było używane, z wyjątkiem rozszerzenia FM w Mark III. Nawet wtedy, SMS i Mark III miały inny projekt portu rozszerzeń [@cpu-mk3].

### Górne przerywacze

Inną specjalnością tej konsoli jest to, że zawiera dwa przyciski na górze obudowy, `PAUSE` i `RESET`, możesz spróbować zgadnąć, co robią!

![Górna część obudowy [@photography-amos].](top.png) {.open-float}

Kiedy przycisk `PAUSE` jest wciśnięty, niemaskowalne przerwanie jest wysyłane do procesora [@io-pause]. Wektor przerwań jest przechowywany w samej grze, co oznacza, że od gry zależy odebranie tego naciśnięcia.

Dla kontrastu i z jakiegoś dziwnego powodu przycisk `RESET` jest obsługiwany jak naciśnięcie klawisza na kontrolerze.

{.close-float}

## System Operacyjny

Na płycie głównej znajduje się mały **8 KB BIOS ROM**, który jest uruchamiany przy każdym włączeniu konsoli. Sam program nie pasuje do kategorii 'Systemu Operacyjnego', jest raczej **Boot Managerem**.

### Selektor nośnika

Głównym celem BIOS-u jest załadowanie prawidłowej gry (z dowolnego gniazda gry) z następującym priorytetem: Sega Card, kartridż i moduł rozszerzeń.

Proces rozruchu działa następująco:

1. Konosla zostaje włączona.
1. BIOS kopiuje część swojego kodu do głównego RAM.
    - Jest to kluczowy krok, ponieważ program zacznie manipulować portami WE/WY, co w pewnym momencie uniemożliwi dostęp do ROM-u!
1. Pokaż ekran powitalny (Tylko w USA/Europie).
2. Sprawdź każdy slot w poszukiwaniu poprawnej gry.
    - Odbywa się to poprzez komunikację z chipem kontrolera WE/WY, aby aktywować wymagane gniazdo. Następnie program startowy kopiuje nagłówek gry (16 bajtów) z każdego slotu, aby sprawdzić, czy zawartość gry jest prawidłowa (czyli prawidłowo włożona). Nagłówek musi mieć zakodowane `TMR SEGA`.
3. Przeprowadzane jest sprawdzenie regionu.
5. Wykonywanie przekierowane zostaje na grę.

### Ekran-niespodzianka

Jeśli którykolwiek z testów się nie powiedzie, konsola będzie się zapętlać w nieskończoność, wyświetlając ekran, który prosi użytkownika o włożenie prawidłowej gry:

![USA/Europejski komunikat o błędzie (po wstępnym ekranie powitalnym).](bios/usa){.toleft video="true"}

![Japoński komunikat o 'błędzie' (ulepszony przez układ FM!).](bios/japanese){.toright video="true"}

Jak widzisz, pomiędzy regionami istnieją pewne kreatywne różnice. Kiedy pierwszy raz usłyszałem japoński, myślałem, że pochodzi od Electric Light Orchestra (zespół), ale tak naprawdę pochodzi ze Space Harrier (gra). Przy okazji, efekt perspektywy na podłodze uzyskuje się poprzez zmianę palet kolorów.

### Więcej różnic regionalnych

Ponieważ japoński wariant był wstecznie kompatybilny z SG-1000, sprawdzanie nagłówka jest zastępowane 'kontrolą integralności', która zamiast tego odczytuje dane z pierwszych 256 bajtów wiele razy, aby wykryć, czy to śmieci.

Co więcej, Mark III nie ma BIOS-u, więc gniazda są aktywowane przełącznikami sprzętowymi, a kartridż jest medium o najwyższym priorytecie.

### Możliwość aktualizacji i nowsze układy BIOS

ROM-u systemu BIOS ze względu na jego charakter **nie można aktualizować**. Chociaż wraz z wejściem na rynek nowych wersji konsoli odkryto, że Sega zaktualizowała również program BIOS.

Późniejsze BIOS-y zawierały nawet całą grę! W konsekwencji chip ROM stał się większy i towarzyszył mu dedykowany mapper.

## Gry

Krótko mówiąc, gry są napisane w zwykłym asemblerze Z80, i tyle. Jeśli czytałeś artykuły o późniejszych konsolach, nie ma tutaj kompilatorów ani oprogramowania wspomagającego (poza asemblerem).

### Nośnik danych

Master System udostępnia dwa różne nośniki dystrybucji gier:

- **Kartridż**: najczęściej stosowany, do 48 KB pamięci. Jednak dzięki dołączeniu mapera system może uzyskać dostęp do szerszej przestrzeni i/lub obsługiwać inne układy, takie jak pamięć RAM, która może być używana do przechowywania zapisów.
  - Sega dostarczyła oficjalne mapery dla programistów o nazwie 'Paging Chips', najpotężniejszy z nich mapuje do 512 KB pamięci!
- **Sega Card**: Ma bardzo cienką obudowę i jest tańszy w produkcji. Można zaadresować do 32 KB pamięci. Ponieważ SEGA nigdy nie zaprojektowała mapera dla tego medium, największa karta na rynku miała 32 KB ROM.

## Przeciwdziałanie-Piractwu & Blokowanie Regionu

W przeciwieństwie do Nintendo, Sega nie zastosowała agresywnych metod kontrolowania dystrybucji swoich gier, jednak uniemożliwiła systemom amerykańskim i europejskim uruchamianie japońskich gier, zmieniając kształt gniazda kartridża i używając innego sprawdzania nagłówka pamięci ROM.

Ponadto systemy amerykańskie i europejskie musiały zawierać w nagłówku wspomniany kod `TMR SEGA`. Więc, to umożliwiło im zapobieganie nieautoryzowanej dystrybucji poprzez wykorzystanie praw dotyczących znaków towarowych.

## To wszystko ludziska

Uprzednie napisanie artykułu o Nintendo DS naprawdę pokazuje, jak skomplikowana stała się technologia. Master System jest w tym sensie bardzo prosty, nawet po pewnym 'technicznym czepianiu się dziury', o którym być może powiedziałem tu i tam.

W każdym razie mam nadzieję, że ten artykuł pomógł ci uzyskać ogólne wrażenie na temat stanu technologii od początku do połowy lat 80-tych. Chciałbym również podziękować społeczności smspower.org i Discordowi /r/Emulation za przeczytanie pierwszego szkicu i wskazanie *mnóstwa* błędów i sugestii.

Do następnego razu!  
Rodrigo

*Ten artykuł został poświęcony pamięci Jacinto 'Pocho' Fornasier.*
