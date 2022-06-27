---
name: Game Boy
subtitle: Prosty i przenośny
date: 2019-02-21
releaseDate: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "Oryginalny Game Boy\n<br>Wydany 21.04.1989 w Japonii, 31.07.1989 w Ameryce i 28.09.1990 w Europie"
    img_class: reduced-width
  Motherboard:
    caption: "Pokazuję wersję '04'. Zwróć uwagę, że 'DMG' to identyfikator oryginalnego modelu Game Boya."
  Diagram: { }
# SEO
title: Architektura Game Boya
#Historical
aliases:
  - /projects/consoles/game-boy/
---

## Szybkie wprowadzenie

Game Boya można sobie wyobrazić jako przenośną wersję NES o ograniczonej mocy, ale zobaczysz, że zawiera on bardzo interesującą nową funkcjonalność.

---

## CPU

Zamiast umieszczać wiele gotowych chipów na płycie głównej, Nintendo zdecydowało się na pojedynczy chip do przechowywania (i ukrywania) większości komponentów, w tym procesora. Ten typ chipa nazywa się 'System On Chip' (SoC), a ten znaleziony na Game Boyu jest określany jako **DMG-CPU** lub **Sharp LR35902**.

To powiedziawszy, głównym procesorem jest **Sharp SM83** i jest to mieszanka Z80 i Intel 8080. Działa z częstotliwością **~4,19 MHz**.

Sam Z80 jest nadzbiorem 8080, więc co tak naprawdę SM83 ma, a czego nie ma z tych dwóch?
- Ani rejestry `IX` i `IY` Z80, ani instrukcje `IN` lub `OUT` 8080 nie są dołączone: Oznacza to, że [porty WE/WY]({{< ref "master-system#accessing-the-rest-of-the-components" >}}) nie są dostępne. Nie jestem pewien, czy to tylko środek do redukcji kosztów, ale jedno jest pewne: komponenty będą musiały być **całkowicie zmapowane w pamięci**.
- Zaimplementowano tylko zestaw rejestrów 8080.
- Zawiera rozszerzony zestaw instrukcji Z80. Chociaż znajdują się tylko instrukcje manipulacji bitami.

Na koniec dodali także **kilka nowych instrukcji**, których nie ma ani w Z80, ani w 8080. Myślę, że wyjaśnianie ich pojedynczo wykracza poza zakres tego artykułu, ale główną ideą jest to, że optymalizują one pewne operacje uwarunkowane sposobem, w jaki Nintendo/Sharp rozmieścili sprzęt.

### Dostępna pamięć

Nintendo zamontowało **8 KB RAM** do użytku ogólnego (nazywanego przez nich **Work RAM** lub 'WRAM'). Zauważ, że jest to czterokrotnie więcej niż ta, którą zawierał [NES]({{< ref "nes" >}}).

### Dostęp sprzętowy

SM83 obsługuje 8-bitową magistralę danych i 16-bitową magistralę adresową, dzięki czemu można zaadresować do 64 KB pamięci. Mapa pamięci składa się z:
  - Miejsca na kartridż.
  - WRAM i RAM Wyświetlacza.
  - WE/WY (joypad, dźwięk, grafika i LCD)
  - Kontroli przerwań.

---

## Grafika

Wszystkie obliczenia graficzne są wykonywane przez procesor, a następnie renderowane są przez **Picture Processing Unit** lub 'PPU'. Jest to kolejny komponent znajdujący się wewnątrz DMG-CPU i faktycznie jest oparty na [PPU poprzednika]({{< ref "nes#graphics" >}}).

Obraz jest wyświetlany na zintegrowanym ekranie LCD, który ma rozdzielczość **160×144 pikseli** i może wyświetlać **4 odcienie szarości** (biały, jasnoszary, ciemnoszary i czarny). Ale ponieważ oryginalny Gameboy ma zielony wyświetlacz LCD, grafika będzie wyglądać *zielonawo*.

Jeśli czytałeś wcześniej artykuł NES, być może pamiętasz, że PPU został zaprojektowany do podążania za wiązką CRT. Jednak (z oczywistych względów) w Gameboyu dostaliśmy ekran LCD. Nowy PPU nie zmienia tej części, ponieważ wyświetlacze LCD również wymagają odświeżenia. W rzeczywistości niektóre efekty specjalne uzyskane dzięki temu zachowaniu będą również obsługiwane na Gameboyu.

### Organizowanie treści

{{< figure_img src="ppu.png" alt="Diagram Pamięci PPU" img_class="no-borders" class="centered-container" >}}
Architektura pamięci PPU
{{< /figure_img >}}

PPU ma **8 KB VRAM** lub 'RAM-u Wyświetlacza', do której zarówno PPU, jak i procesor mogą uzyskać bezpośredni dostęp, ale nie w tym samym czasie. Te 8 KB będą zawierać większość danych potrzebnych PPU do renderowania grafiki. Pozostałe części będą przechowywane wewnątrz PPU, ponieważ będą wymagały szybszego dostępu.

Gra jest odpowiedzialna za wypełnianie różnych obszarów właściwymi typami danych. Co więcej, PPU ujawnia rejestry, dzięki czemu gra może poinstruować PPU, jak te dane są zorganizowane (jest jednak wiele reguł).

### Konstruowanie klatki

Zobaczmy, jak PPU radzi sobie z rysowaniem rzeczy na ekranie. W celach demonstracyjnych jako przykład posłuży *Super Mario Land 2*:

{{< tabs >}}

{{< tab name="Kafelki" active="true" >}}
{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Wszystkie" active="true" src="ppu_mario/tiles.png" >}}
Wiele kafelków.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Siatka" src="ppu_mario/tiles_grid.png" >}}
Wiele kafelków oddzielonych siatką.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Pojedynczy" src="ppu_mario/tiles_single.png" >}}
Pojedynczy kafelek.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Kafelki znalezione w Tabeli Wzorów.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
PPU używa **kafelków** jako podstawowego składnika renderowania grafiki, w szczególności **spriteów i tła**.

Kafelki to po prostu **bitmapy 8x8** przechowywane w VRAM w regionie o nazwie **Zestaw kafelków** lub 'Tabela wzorców kafelków', i każdy piksel odpowiada jednemu z czterech dostępnych odcieni szarości. Na koniec kafelki są grupowane w dwie tabele wzorów.

Aby zbudować obraz, do kafelków odwołuje się inny typ tabeli zwany **Mapa kafelków**. Ta informacja powie PPU, gdzie renderować kafelki. Przechowywane są dwie mapy, aby skonstruować różne warstwy klatki.

W następnych sekcjach wyjaśniono, w jaki sposób mapy kafelków są używane do tworzenia warstw.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Tło" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Pełne" active="true" src="ppu_mario/background.png" >}}
Przydzielona mapa tła w VRAM.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Wybrana" src="ppu_mario/background_selected.png" >}}
Wybrany obszar mapy tła.  
Zauważ, że wybrana część zawiera kawałek górnej części, która będzie nałożona na _warstwę okna_.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Wyrenderowane" src="ppu_mario/background_rendered.png" >}}
Wyświetlana mapa tła.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Proces renderowania mapy tła.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Warstwa tła to mapa **256x256 pikseli** (32x32 kafelki) zawierająca **statyczne kafelki**. Pamiętaj jednak, że na ekranie widać tylko 160x144, więc gra decyduje, która część zostanie wybrana do wyświetlenia. Gry mogą również przesuwać widoczny obszar podczas rozgrywki, w ten sposób realizowany jest **Efekt Przewijania**.

Do zbudowania warstwy tła można użyć jednej z dwóch map kafelków.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Okno" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Pełne" active="true" src="ppu_mario/window.png" alt="Okno" >}}
Przydzielona mapa okna.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Wyrenderowane" src="ppu_mario/window_rendered.png" alt="Wyrenderowane Okno" >}}
Wyświetlana mapa okna. Gra aktywuje ją podczas ostatnich linii skanowania. W związku z tym na dole ekranu renderowane są tylko pierwsze wiersze.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Proces renderowania mapy okna.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Okno to warstwa **160x144 pikseli** zawierająca kafelki wyświetlane na górze tła i spriteów. Nie przewija się.

Pozostałą mapę kafelkową można przypisać do warstwy okna.

Na początku może to brzmieć jak głupia funkcja. W końcu warstwa okna nakłada się na wszystko inne, więc do czego jest przydatna? Cóż, zarówno Tło, jak i Okno mogą być używane jednocześnie w różnych częściach ekranu. Jest to realizowane przez zmianę rejestru `LCDCONT` podczas określonych linii skanowania.

Dlatego gry zwykle używają go do wyświetlania statystyk gracza, wyników i innych 'zawsze włączonych' informacji.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprite'y" >}}

{{< figure_img float="true" class="pixel" src="ppu_mario/sprite.png" alt="Sprite'y" >}}
Renderowana warstwa Sprite
{{< /figure_img >}}

{{% inner_markdown %}}
Sprite'y to kafelki, które mogą poruszać się niezależnie po ekranie. Mogą również nakładać się na siebie i pojawiać się za tłem, widoczna grafika zostanie określona na podstawie atrybutu priorytetu.

Mają też dostępny dodatkowy kolor: **Przezroczysty**. Tak więc mogą wyświetlać tylko trzy różne odcienie szarości zamiast czterech. Na szczęście ta warstwa pozwala zdefiniować dwie palety kolorów, aby wykorzystać każdy kolor.

**Object Attribute Memory** lub 'OAM' to mapa przechowywana w PPU, która określa kafelki, które będą używane jako sprite'y. Gry wypełniają ten region, wywołując **jednostkę DMA** znajdującą się w chipie, DMA pobiera dane z głównej pamięci RAM lub ROM gry do OAM.

Każdy wpis oprócz indeksu kafelka zawiera następujące atrybuty: pozycja X-Y, paleta kolorów, priorytet i flagi odwracania (umożliwiające obracanie kafelka w pionie i poziomie).

PPU jest ograniczone do renderowania do **10 sprite'ów na linię skanowania** i **do 40 na klatkę**, przepełnienie spowoduje to, że sprite'y nie zostaną narysowane.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Rezultat" >}}

{{< figure_img float="true" class="pixel" src="ppu_mario/result.png" alt="Rezultat" >}}
Ostateczny rezultat. _Tada!_
{{< /figure_img >}}

{{% inner_markdown %}}
Gdy klatka jest gotowa, czas przejść do następnej! Jednak procesor nie może modyfikować tabel, gdy PPU czyta z VRAM, więc system zapewnia zestaw przerwań wyzwalanych, gdy PPU jest bezczynny. Możesz pamiętać to zachowanie z czasów NES.

Po zakończeniu pojedynczej linii skanowania wywoływane jest przerwanie **Poziome Blank**. Pozwala to pobawić się częścią klatki, która nie została jeszcze narysowana.

Gdy wszystkie linie skanowania zostaną zakończone, wywoływane jest przerwanie **Pionowe Blank**. Gra może teraz zaktualizować grafikę do następnej klatki.

Istnieje dodatkowy stan zwany **wyszukiwanie OAM**, który jest wyzwalany na początku linii skanowania, w tym momencie PPU przetwarza, które sprite'y będą wyświetlane w tej linii skanowania, dzięki czemu gra może aktualizować dowolny region z wyjątkiem OAM.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Sekrety i Ograniczenia

Włączenie warstwy okna i dodatkowych przerwań pozwoliło na wprowadzenie nowych rodzajów treści i efektów.

{{< tabs >}}
{{< tab name="Efekt chybotania" active="true" >}}
{{< video src="zelda" float="true" >}}
The Legend of Zelda: Link's Awakening (1993) _Spoilery!_
{{< /video >}}

{{% inner_markdown %}}
Poziome przerwania pozwoliły zmienić klatkę przed zakończeniem. Oznacza to, że w każdym wierszu można zastosować inną wartość przewijania, co spowoduje, że każdy wiersz ramki zostanie przesunięty w inny sposób.

Osiągnęło to *efekt chybotania* (nie jestem pewien, czy to oficjalna nazwa).
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Dźwięk

System audio jest realizowany przez **Audio Processing Unit** (APU) — [czip PSG]({{< ref "master-system#audio" >}}) z czterema kanałami.

### Funkcjonalność

Każdy kanał jest zarezerwowany dla typu fali:

{{< tabs >}}

{{< tab name="Impuls (Pulse)" active="true" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Impuls 1 (Pulse 1)" src="pulse_single_1" >}}
Kanał Impuls 1.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Impuls 2 (Pulse 2)" src="pulse_single_2" active="true" >}}
Kanał Impuls 2.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Kompletny" src="pulse_full" >}}
Wszystkie kanały audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}
Pokemon Red/Blue (1996).
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}

Fale impulsowe mają bardzo wyraźny dźwięk *bip*, który jest używany głównie do **melodii lub efektów dźwiękowych**.

APU rezerwuje dwa kanały dla jednej fali impulsowej każdy. Wykorzystują one jeden z czterech różnych tonów zbudowanych przez zmianę szerokości impulsu. Pierwszy kanał ma dostępną wyłączną **kontrolę sweep**.

Ze względu na ograniczoną liczbę kanałów melodia często jest przerywana, gdy efekty muszą być odtwarzane jako część rozgrywki. Jest to bardzo widoczne w grach takich jak Pokemon Red/Blue, kiedy podczas bitwy dźwięk Pokemona będzie nakładał się na wszystkie kanały używane do odtwarzania muzyki.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Szum (Noise)" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Szum (Noise)" active="true" src="noise_single" >}}
Kanał Szum.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Kompletny" src="noise_full" >}}
Wszystkie kanały audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Pokemon Red/Blue (1996).{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Szum to w zasadzie zestaw losowych kształtów fal, które brzmią jak biały szum. Przydzielony jest dla niego jeden kanał.

Gry używają go do efektów perkusyjnych lub *ambient*.

Ten kanał ma do dyspozycji tylko 2 tony, z których jeden generuje *czysty szum*, a drugi wytwarza *robotyczny szum*. Jego częstotliwość również może być kontrolowana.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Fala (Wave)" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Fala (Wave)" active="true" src="wave_single" >}}
Kanał Fala.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Kompletny" src="wave_full" >}}
Wszystkie kanały audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Pokemon Red/Blue (1996){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
APU umożliwia zdefiniowanie **niestandardowego kształtu fali**, który ma być słyszalny z czwartego kanału. Fala składa się z 32 4-bitowych próbek, które są przechowywane w tablicy fal.

Kanał ten pozwala również kontrolować jego częstotliwość (umożliwiając wytwarzanie różnych nut muzycznych z tej samej próbki) i głośność.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

### Sekrety i Ograniczenia

Mikser wyprowadza dźwięk stereo, więc kanały można przypisać do lewej lub prawej strony. Można to jednak usłyszeć tylko w słuchawkach! Głośnik jest monofoniczny.

Co więcej, układ miksera jest również podłączony do dedykowanego pinu na kartridżu, co umożliwia strumieniowe przesyłanie **dodatkowego kanału** pod warunkiem, że kartridż musi faktycznie wyprowadzać dźwięk analogowy (możliwe tylko z dodatkowym sprzętem). Żadna gra na rynku nie korzystała z tej funkcji.

---

## Gry

Gry są napisane w asemblerze i mają maksymalny rozmiar **32 KB**, jest to spowodowane ograniczoną dostępną przestrzenią adresową. Jednak przy użyciu **Memory Bank Controllera** (mappera) gry mogą osiągać większe rozmiary. Największy dostępny na rynku kartridż ma 1 MB ROM.

Kartridże mogą zawierać zegar czasu rzeczywistego i zewnętrzną baterię wraz z pamięcią SRAM do przechowywania zapisów.

### Komunikacja zewnętrzna

Po raz pierwszy gry mogą komunikować się z innymi konsolami za pomocą kabla **Game Boy Link**, który zapewnia funkcję gry wieloosobowej. Interfejs wykorzystuje bardzo prymitywny typ **połączenia szeregowego**.

---

## Przeciwdziałanie-Piractwu

Ta konsola zawiera **256 bajtów ROM** ułożone w stos w procesorze, które jest używane do ładowania ROM kartridża. Jednak nie uruchamia gry od razu. Najpierw wykonuje serię testów, które **zapobiegają wykonaniu nieautoryzowanych kartridży**, a także upewniają się, że kartridż jest **prawidłowo włożony**.

Aby przejść te kontrole, gry musiały zawierać kopię logo Nintendo (w postaci kafelków) w nagłówku ROM, w ten sposób Nintendo mogło wykorzystać przepisy o **Prawach Autorskich i Znakach Towarowych** do regulowania dystrybucji. *Mądre, co?* Bootowy ROM Gameboya zawiera również kopię logo, aby móc je porównać.

Biorąc to pod uwagę, proces uruchamiania wygląda następująco:

1. Po włączeniu konsoli procesor zaczyna czytać pod adresem **0x00** (lokalizacja ROM Gameboya).
1. RAM i Dźwięk są inicjowane.
2. Logo Nintendo jest kopiowane z ROM-u Kartridża do RAM-u Wyświetlacza, a następnie rysowane przy górnej krawędzi ekranu. Jeśli nie ma włożonego kartridża, logo będzie zawierało kafelki śmieci. To samo może się zdarzyć, jeśli jest źle włożony.
2. Logo jest przewijane w dół i odtwarzany jest słynny dźwięk *po-ling*.
3. Logo Nintendo gry jest dopasowywane do tego przechowywanego w ROM konsoli, jeśli test się nie powiedzie, konsola zawiesza się.
4. Szybka **suma kontrolna** jest wykonywana na nagłówku ROM kartridża, aby upewnić się, że kartridż jest prawidłowo włożony, jeśli test się nie powiedzie, konsola zawiesza się.
5. ROM konsoli jest usuwany z mapy pamięci.
6. Procesor rozpoczyna grę.

Co ciekawe, logo *Nintendo* wyświetlane na ekranie nie jest usuwane z VRAM, więc gry mogą zastosować animacje i efekty, aby wprowadzić własne logo.

{{< video src="20y" class="centered-container" >}}
20y, demo homebrew, które bawi się logo
{{< /video >}}

W grach można zastosować więcej środków antypirackich, takich jak sprawdzanie rozmiaru SRAM (zwykle jest on większy w Bootlegach) i sprawdzanie sum kontrolnych pamięci ROM w losowych punktach gry.

---

## To wszystko ludziska
