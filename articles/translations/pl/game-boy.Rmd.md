---
short_title: Architektura Game Boya
long_title: Architektura Game Boya
name: Game Boy
subtitle: Prosty i przenośny
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "Oryginalny Game Boy<br>Wydany 21.04.1989 w Japonii, 31.07.1989 w Ameryce i 28.09.1990 w Europie"
    img_class: reduced-width
  Motherboard:
    caption: "Pokazuję wersję '04'. Zwróć uwagę, że 'DMG' to identyfikator oryginalnego modelu Game Boya."
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/game-boy/
---

## Szybkie wprowadzenie

Game Boya można sobie wyobrazić jako przenośną wersję NES o ograniczonej mocy, ale zobaczysz, że zawiera on bardzo interesującą nową funkcjonalność.

```{r results="asis"}
supporting_imagery()
```

## CPU

Zamiast umieszczać wiele gotowych chipów na płycie głównej, Nintendo zdecydowało się na pojedynczy chip do przechowywania (i ukrywania) większości komponentów, w tym procesora. Ten typ chipa nazywa się 'System On Chip' (SoC), a ten znaleziony na Game Boyu jest określany jako **DMG-CPU** lub **Sharp LR35902** `r cite("cpu-realboy")`.

To powiedziawszy, głównym procesorem jest **Sharp SM83** `r cite("cpu-gekkio")` i jest to mieszanka Z80 i Intel 8080. Działa z częstotliwością **~4,19 MHz**.

Sam Z80 jest nadzbiorem 8080, więc co tak naprawdę SM83 ma, a czego nie ma z tych dwóch? `r cite("cpu-steil")`

- Ani rejestry `IX` i `IY` Z80, ani instrukcje `IN` lub `OUT` 8080 nie są dołączone: Oznacza to, że [porty WE/WY](code>r ref("master-system#accessing-the-rest-of-the-components")</code) nie są dostępne. Nie jestem pewien, czy to tylko środek do redukcji kosztów, ale jedno jest pewne: komponenty będą musiały być **całkowicie zmapowane w pamięci** `r cite("cpu-fayzullin")`.
- Zaimplementowano tylko zestaw rejestrów 8080.
- Zawiera rozszerzony zestaw instrukcji Z80. Chociaż znajdują się tylko instrukcje manipulacji bitami.

Na koniec dodali także **kilka nowych instrukcji**, których nie ma ani w Z80, ani w 8080. Myślę, że wyjaśnianie ich pojedynczo wykracza poza zakres tego artykułu, ale główną ideą jest to, że optymalizują one pewne operacje uwarunkowane sposobem, w jaki Nintendo/Sharp rozmieścili sprzęt.

### Dostępna pamięć

Nintendo zamontowało **8 KB RAM** do użytku ogólnego (nazywanego przez nich **Work RAM** lub 'WRAM') `r cite("cpu-nintendo")`. Zauważ, że jest to czterokrotnie więcej niż ta, którą zawierał [NES](code>r ref("nes")</code).

### Dostęp sprzętowy

SM83 obsługuje 8-bitową magistralę danych i 16-bitową magistralę adresową, dzięki czemu można zaadresować do 64 KB pamięci. Mapa pamięci składa się z `r cite("cpu-mongenel")`:

- Miejsca na kartridż.
- WRAM i RAM Wyświetlacza.
- WE/WY (joypad, dźwięk, grafika i LCD)
- Kontroli przerwań.

## Grafika

Wszystkie obliczenia graficzne są wykonywane przez procesor, a następnie renderowane są przez **Picture Processing Unit** lub 'PPU'. Jest to kolejny komponent znajdujący się wewnątrz DMG-CPU i faktycznie jest oparty na [PPU poprzednika](code>r ref("nes#graphics")</code).

Obraz jest wyświetlany na zintegrowanym ekranie LCD, który ma rozdzielczość **160×144 pikseli** i może wyświetlać **4 odcienie szarości** (biały, jasnoszary, ciemnoszary i czarny). Ale ponieważ oryginalny Gameboy ma zielony wyświetlacz LCD, grafika będzie wyglądać *zielonawo*.

Jeśli czytałeś wcześniej artykuł NES, być może pamiętasz, że PPU został zaprojektowany do podążania za wiązką CRT. Jednak (z oczywistych względów) w Gameboyu dostaliśmy ekran LCD. Nowy PPU nie zmienia tej części, ponieważ wyświetlacze LCD również wymagają odświeżenia. W rzeczywistości niektóre efekty specjalne uzyskane dzięki temu zachowaniu będą również obsługiwane na Gameboyu.

### Organizowanie treści

(ref:ppumemcaption) Architektura pamięci PPU.

```{r fig.cap="(ref:ppumemcaption)", fig.align='center', centered=TRUE}
image("ppu.png", "(ref:ppumemcaption)", class = "centered-container")
```

PPU ma **8 KB VRAM** lub 'RAM-u Wyświetlacza', do której zarówno PPU, jak i procesor mogą uzyskać bezpośredni dostęp, ale nie w tym samym czasie. Te 8 KB będą zawierać większość danych potrzebnych PPU do renderowania grafiki. Pozostałe części będą przechowywane wewnątrz PPU, ponieważ będą wymagały szybszego dostępu.

Gra jest odpowiedzialna za wypełnianie różnych obszarów właściwymi typami danych. Co więcej, PPU ujawnia rejestry, dzięki czemu gra może poinstruować PPU, jak te dane są zorganizowane (jest jednak wiele reguł).

### Konstruowanie klatki

Zobaczmy, jak PPU radzi sobie z rysowaniem rzeczy na ekranie. W celach demonstracyjnych jako przykład posłuży *Super Mario Land 2*:

(ref:tilestitle) Kafelki

(ref:tilesfooter) Kafelki znalezione w Tabeli Wzorów.

(ref:tilalltitle) Wszystkie

(ref:tilallcaption) Wiele kafelków.

(ref:tilgridtitle) Siatka

(ref:tilgridcaption) Wiele kafelków oddzielonych siatką.

(ref:tilsingletitle) Pojedynczy

(ref:tilsinglecaption) Pojedynczy kafelek.

```{r fig.cap=c("(ref:tilallcaption)", "(ref:tilgridcaption)", "(ref:tilsinglecaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:tilestitle)", tab.active = TRUE, tab.first=TRUE, tab.nested=TRUE, tab.figure=TRUE, tab.float=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/tiles.png', "(ref:tilallcaption)", tab.name = "(ref:tilalltitle)", tab.active = TRUE)
image('ppu_mario/tiles_grid.png', "(ref:tilgridcaption)", tab.name = "(ref:tilgridtitle)")
image('ppu_mario/tiles_single.png', "(ref:tilsinglecaption)", tab.name = "(ref:tilsingletitle)")
figcaption("(ref:tilesfooter)")
```

PPU używa **kafelków** jako podstawowego składnika renderowania grafiki, w szczególności **spriteów i tła** `r cite("graphics-emu")`.

Kafelki to po prostu **bitmapy 8x8** przechowywane w VRAM w regionie o nazwie **Zestaw kafelków** lub 'Tabela wzorców kafelków', i każdy piksel odpowiada jednemu z czterech dostępnych odcieni szarości. Na koniec kafelki są grupowane w dwie tabele wzorów.

Aby zbudować obraz, do kafelków odwołuje się inny typ tabeli zwany **Mapa kafelków**. Ta informacja powie PPU, gdzie renderować kafelki. Przechowywane są dwie mapy, aby skonstruować różne warstwy klatki.

W następnych sekcjach wyjaśniono, w jaki sposób mapy kafelków są używane do tworzenia warstw.

(ref:bgtitle) Warstwa Tła

(ref:bgfooter) Proces renderowania mapy tła.

(ref:bgalltitle) Pełne

(ref:bgallcaption) Przydzielona mapa tła w VRAM.

(ref:bgseltitle) Wybrana

(ref:bgselcaption) Wybrany obszar mapy tła. Zauważ, że wybrana część zawiera kawałek górnej części, która będzie nałożona na _warstwę okna_.

(ref:bgrentitle) Wyrenderowane

(ref:bgrencaption) Wyświetlana mapa tła.

```{r fig.cap=c("(ref:bgallcaption)", "(ref:bgselcaption)", "(ref:bgrencaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:bgtitle)", tab.float=TRUE, tab.nested=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/background.png', "(ref:bgallcaption)", tab.name = "(ref:bgalltitle)", tab.active = TRUE)
image('ppu_mario/background_selected.png', "(ref:bgselcaption)", tab.name = "(ref:bgseltitle)")
image('ppu_mario/background_rendered.png', "(ref:bgrencaption)", tab.name = "(ref:bgrentitle)")
figcaption("(ref:bgfooter)")
```

Warstwa tła to mapa **256x256 pikseli** (32x32 kafelki) zawierająca **statyczne kafelki**. Pamiętaj jednak, że na ekranie widać tylko 160x144, więc gra decyduje, która część zostanie wybrana do wyświetlenia. Gry mogą również przesuwać widoczny obszar podczas rozgrywki, w ten sposób realizowany jest **Efekt Przewijania**.

Do zbudowania warstwy tła można użyć jednej z dwóch map kafelków.

(ref:wintitle) Okno

(ref:winfooter) Proces renderowania mapy okna.

(ref:winalltitle) Pełne

(ref:winallcaption) Przydzielona mapa okna.

(ref:winrentitle) Wyrenderowane

(ref:winrencaption) Wyświetlana mapa okna. Gra aktywuje ją podczas ostatnich linii skanowania. W związku z tym na dole ekranu renderowane są tylko pierwsze wiersze.

```{r fig.cap=c("(ref:winallcaption)", "(ref:winselcaption)", "(ref:winrencaption)"), fig.align="center", out.width = split_figure_width, tab.title="(ref:wintitle)", tab.float=TRUE, tab.nested=TRUE, tab_class="pixel", fig.ncol = responsive_columns}
image('ppu_mario/window.png', "(ref:winallcaption)", tab.name = "(ref:winalltitle)", tab.active = TRUE)
image('ppu_mario/window_rendered.png', "(ref:winrencaption)", tab.name = "(ref:winrentitle)")
figcaption("(ref:winfooter)")
```

Okno to warstwa **160x144 pikseli** zawierająca kafelki wyświetlane na górze tła i spriteów. Nie przewija się.

Pozostałą mapę kafelkową można przypisać do warstwy okna.

Na początku może to brzmieć jak głupia funkcja. W końcu warstwa okna nakłada się na wszystko inne, więc do czego jest przydatna? Cóż, zarówno Tło, jak i Okno mogą być używane jednocześnie w różnych częściach ekranu. Jest to realizowane przez zmianę rejestru `LCDCONT` podczas określonych linii skanowania.

Dlatego gry zwykle używają go do wyświetlania statystyk gracza, wyników i innych 'zawsze włączonych' informacji.

(ref:spritestitle) Sprite'y

(ref:spritescaption) Renderowana warstwa Sprite.

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)"}
image('ppu_mario/sprite.png', "(ref:spritescaption)", float=TRUE, class="pixel")
```

Sprite'y to kafelki, które mogą poruszać się niezależnie po ekranie. Mogą również nakładać się na siebie i pojawiać się za tłem, widoczna grafika zostanie określona na podstawie atrybutu priorytetu.

Mają też dostępny dodatkowy kolor: **Przezroczysty**. Tak więc mogą wyświetlać tylko trzy różne odcienie szarości zamiast czterech. Na szczęście ta warstwa pozwala zdefiniować dwie palety kolorów, aby wykorzystać każdy kolor.

**Object Attribute Memory** lub 'OAM' to mapa przechowywana w PPU, która określa kafelki, które będą używane jako sprite'y. Gry wypełniają ten region, wywołując **jednostkę DMA** znajdującą się w chipie, DMA pobiera dane z głównej pamięci RAM lub ROM gry do OAM.

Każdy wpis oprócz indeksu kafelka zawiera następujące atrybuty: pozycja X-Y, paleta kolorów, priorytet i flagi odwracania (umożliwiające obracanie kafelka w pionie i poziomie).

PPU jest ograniczone do renderowania do **10 sprite'ów na linię skanowania** i **do 40 na klatkę**, przepełnienie spowoduje to, że sprite'y nie zostaną narysowane.

(ref:resulttitle) Rezultat

(ref:resultcaption) Ostateczny rezultat. _Tada!_

```{r fig.cap="(ref:spritescaption)", fig.align='center', tab.title="(ref:spritestitle)"}
image('ppu_mario/result.png', "(ref:spritescaption)", float=TRUE, class="pixel")
```

Gdy klatka jest gotowa, czas przejść do następnej! Jednak procesor nie może modyfikować tabel, gdy PPU czyta z VRAM, więc system zapewnia zestaw przerwań wyzwalanych, gdy PPU jest bezczynny. Możesz pamiętać to zachowanie z czasów NES.

Po zakończeniu pojedynczej linii skanowania wywoływane jest przerwanie **H-Blank**. Pozwala to pobawić się częścią klatki, która nie została jeszcze narysowana.

Gdy wszystkie linie skanowania zostaną zakończone, wywoływane jest przerwanie **V-Blank**. Gra może teraz zaktualizować grafikę do następnej klatki.

Istnieje dodatkowy stan zwany **wyszukiwanie OAM**, który jest wyzwalany na początku linii skanowania, w tym momencie PPU przetwarza, które sprite'y będą wyświetlane w tej linii skanowania, dzięki czemu gra może aktualizować dowolny region z wyjątkiem OAM.

`r close_tabs()`

### Sekrety i Ograniczenia

Włączenie warstwy okna i dodatkowych przerwań pozwoliło na wprowadzenie nowych rodzajów treści i efektów.

(ref:wobbletitle) Efekt chybotania

(ref:wobblecaption) The Legend of Zelda: Link's Awakening (1993). _Spoilery!_

```{r fig.cap="(ref:wobblecaption)", fig.align='center', tab.title="(ref:wobbletitle)", tab.first=TRUE, tab.active=TRUE, tab.last=TRUE}
video('zelda', "(ref:wobblecaption)", float=TRUE)
```

Poziome przerwania pozwoliły zmienić klatkę przed zakończeniem. Oznacza to, że w każdym wierszu można zastosować inną wartość przewijania, co spowoduje, że każdy wiersz ramki zostanie przesunięty w inny sposób.

Osiągnęło to *efekt chybotania* (nie jestem pewien, czy to oficjalna nazwa).

`r close_tabs()`

## Dźwięk

System audio jest realizowany przez **Audio Processing Unit** (APU) — [czip PSG](code>r ref("master-system#audio")</code) z czterema kanałami `r cite("audio-wiki")`.

### Funkcjonalność

Każdy kanał jest zarezerwowany dla typu fali:

(ref:pulsetitle) Impuls (Pulse)

(ref:pokemonfooter) Pokemon Red/Blue (1996).

(ref:pulse1tabtitle) Impuls 1 (Pulse 1)

(ref:pulse1caption) Spojrzenie na kanał Impuls 1 przez Oscyloskop.

(ref:pulse2tabtitle) Impuls 2 (Pulse 2)

(ref:pulse2caption) Spojrzenie na kanał Impuls 2 przez Oscyloskop.

(ref:audiofulltabtitle) Kompletny

(ref:audiofullcaption) Spojrzenie na wszystkie kanały przez Oscyloskop.

```{r fig.cap=c("(ref:pulse1caption)", "(ref:pulse2caption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:pulsetitle)", tab.active = TRUE, tab.first=TRUE, tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('pulse_single_1', "(ref:pulse1caption)", tab.name = "(ref:pulse1tabtitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('pulse_single_2', "(ref:pulse2caption)", tab.name = "(ref:pulse2tabtitle)", caption.post = "(ref:pokemonfooter)")
video('pulse_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

Fale impulsowe mają bardzo wyraźny dźwięk *bip*, który jest używany głównie do **melodii lub efektów dźwiękowych**.

APU rezerwuje dwa kanały dla jednej fali impulsowej każdy. Wykorzystują one jeden z czterech różnych tonów zbudowanych przez zmianę szerokości impulsu. Pierwszy kanał ma dostępną wyłączną **kontrolę sweep**.

Ze względu na ograniczoną liczbę kanałów melodia często jest przerywana, gdy efekty muszą być odtwarzane jako część rozgrywki. Jest to bardzo widoczne w grach takich jak Pokemon Red/Blue, kiedy podczas bitwy dźwięk Pokemona będzie nakładał się na wszystkie kanały używane do odtwarzania muzyki.

(ref:noisetitle) Szum (Noise)

(ref:noisecaption) Spojrzenie na kanał Szum przez Oscyloskop.

```{r fig.cap=c("(ref:noisecaption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:noisetitle)", tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('noise_single', "(ref:noisecaption)", tab.name = "(ref:noisetitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('noise_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

Szum to w zasadzie zestaw losowych kształtów fal, które brzmią jak biały szum. Przydzielony jest dla niego jeden kanał.

Gry używają go do efektów perkusyjnych lub *ambient*.

Ten kanał ma do dyspozycji tylko 2 tony, z których jeden generuje *czysty szum*, a drugi wytwarza *robotyczny szum*. Jego częstotliwość również może być kontrolowana.

(ref:wavetitle) Fala (Wave)

(ref:wavecaption) Spojrzenie na kanał Fala przez Oscyloskop.

```{r fig.cap=c("(ref:wavecaption)", "(ref:audiofullcaption)"), fig.align='center', tab.title="(ref:wavetitle)", tab.nested=TRUE, tab.float=TRUE, tab.figure=TRUE, tab_class="pixel", out.width = standard_figure_width}
video('wave_single', "(ref:wavecaption)", tab.name = "(ref:wavetitle)", caption.post = "(ref:pokemonfooter)", tab.active = TRUE)
video('wave_full', "(ref:audiofullcaption)", tab.name = "(ref:audiofulltabtitle)", caption.post = "(ref:pokemonfooter)")
figcaption("(ref:pokemonfooter)")
```

APU umożliwia zdefiniowanie **niestandardowego kształtu fali**, który ma być słyszalny z czwartego kanału. Fala składa się z 32 4-bitowych próbek, które są przechowywane w tablicy fal.

Kanał ten pozwala również kontrolować jego częstotliwość (umożliwiając wytwarzanie różnych nut muzycznych z tej samej próbki) i głośność.

`r close_tabs()`

### Sekrety i Ograniczenia

Mikser wyprowadza dźwięk stereo, więc kanały można przypisać do lewej lub prawej strony. Można to jednak usłyszeć tylko w słuchawkach! Głośnik jest monofoniczny.

Co więcej, układ miksera jest również podłączony do dedykowanego pinu na kartridżu, co umożliwia strumieniowe przesyłanie **dodatkowego kanału** pod warunkiem, że kartridż musi faktycznie wyprowadzać dźwięk analogowy (możliwe tylko z dodatkowym sprzętem). Żadna gra na rynku nie korzystała z tej funkcji.

## Gry

Gry są napisane w asemblerze i mają maksymalny rozmiar **32 KB**, jest to spowodowane ograniczoną dostępną przestrzenią adresową. Jednak przy użyciu **Memory Bank Controllera** (mappera) gry mogą osiągać większe rozmiary. Największy dostępny na rynku kartridż ma 1 MB ROM.

Kartridże mogą zawierać zegar czasu rzeczywistego i zewnętrzną baterię wraz z pamięcią SRAM do przechowywania zapisów.

### Komunikacja zewnętrzna

Po raz pierwszy gry mogą komunikować się z innymi konsolami za pomocą kabla **Game Boy Link**, który zapewnia funkcję gry wieloosobowej. Interfejs wykorzystuje bardzo prymitywny typ **połączenia szeregowego**.

## Przeciwdziałanie-Piractwu

Ta konsola zawiera **256 bajtów ROM** ułożone w stos w procesorze, które jest używane do ładowania ROM kartridża. Jednak nie uruchamia gry od razu. Najpierw wykonuje serię testów, które **zapobiegają wykonaniu nieautoryzowanych kartridży**, a także upewniają się, że kartridż jest **prawidłowo włożony**.

Aby przejść te kontrole, gry musiały zawierać kopię logo Nintendo (w postaci kafelków) w nagłówku ROM `r cite("games-dhole")`, w ten sposób Nintendo mogło wykorzystać przepisy o **Prawach Autorskich i Znakach Towarowych** do regulowania dystrybucji. *Mądre, co?* Bootowy ROM Gameboya zawiera również kopię logo, aby móc je porównać.

Biorąc to pod uwagę, proces uruchamiania wygląda następująco `r cite("anti-piracy-boot")`:

1. Po włączeniu konsoli procesor zaczyna czytać pod adresem **0x00** (lokalizacja ROM Gameboya).
2. RAM i Dźwięk są inicjowane.
3. Logo Nintendo jest kopiowane z ROM-u Kartridża do RAM-u Wyświetlacza, a następnie rysowane przy górnej krawędzi ekranu. Jeśli nie ma włożonego kartridża, logo będzie zawierało kafelki śmieci. To samo może się zdarzyć, jeśli jest źle włożony.
4. Logo jest przewijane w dół i odtwarzany jest słynny dźwięk *po-ling*.
5. Logo Nintendo gry jest dopasowywane do tego przechowywanego w ROM konsoli, jeśli test się nie powiedzie, konsola zawiesza się.
6. Szybka **suma kontrolna** jest wykonywana na nagłówku ROM kartridża, aby upewnić się, że kartridż jest prawidłowo włożony, jeśli test się nie powiedzie, konsola zawiesza się.
7. ROM konsoli jest usuwany z mapy pamięci.
8. Procesor rozpoczyna grę.

Co ciekawe, logo *Nintendo* wyświetlane na ekranie nie jest usuwane z VRAM, więc gry mogą zastosować animacje i efekty, aby wprowadzić własne logo.

(ref:20ycaption) 20y, demo homebrew, które bawi się logo.

```{r fig.cap="(ref:20ycaption)", fig.align='center', centered=TRUE}
video("20y", "(ref:20ycaption)", class = "centered-container")
```

W grach można zastosować więcej środków antypirackich, takich jak sprawdzanie rozmiaru SRAM (zwykle jest on większy w Bootlegach) i sprawdzanie sum kontrolnych pamięci ROM w losowych punktach gry.

## To wszystko ludziska

