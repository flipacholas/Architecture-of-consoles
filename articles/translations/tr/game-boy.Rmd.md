---
short_title: Game Boy Mimarisi
long_title: Game Boy Mimarisi
name: Game Boy
subtitle: Basit ve taşınabilir
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "Orjinal Game Boy.<br>Japonya'da 21/04/1989, Amerika'da 31/07/1989 ve 28/09/1990'da Avrupa'da satışa sunuldu"
    img_class: reduced-width
  Motherboard:
    caption: "Revizyon '04' gösteriliyor. Not: 'DMG' orijinal Game Boy modelinin adlarından biridir."
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/game-boy/
---

## Hızlı bir giriş

Game Boy, NES'in sınırlı güce sahip taşınabilir bir versiyonu olarak hayal edilebilir, ancak çok ilginç yeni işlevler içerdiğini göreceksiniz.

```{r results="asis"}
supporting_imagery()
```

## İşlemci (CPU)

Nintendo, anakarta birçok hazır yonga yerleştirmek yerine, CPU dahil bileşenlerin çoğunu barındırmak (ve gizlemek) için tek bir yongayı seçti. Bu tür çiplere 'Çip Üzerinde Sistem' (SoC) adı verilir ve GameBoy'da bulunan çip **DMG-CPU** veya **Sharp LR35902** [@cpu-realboy] olarak adlandırılır.

Bunu söylediğimize göre, ana işlemci **Sharp SM83**dür [@cpu-gekkio] ve Z80 ile Intel 8080'in birleşiminden ortaya çıkmıştır. **Yaklaşık 4.19MHz** hızında çalışır.

Z80'in kendisi 8080'in bir üst kümesidir, peki SM83'ün aslında bu ikisinden farkları nelerdir? [@cpu-steil]

- Ne Z80'in `IX` ve `IY` kayıtları ne de 8080'in `IN` veya `OUT` komutları dahil edilmiştir: Bu, [I/O portlarının](master-system#accessing-the-rest-of-the-components) mevcut olmadığı anlamına gelir. Bunun sadece maliyetleri düşürmek için bir önlem olup olmadığından emin değilim, ancak kesin olan bir şey var ki bileşenlerin **tamamen bellek eşlemeli** olması gerekecek [@cpu-fayzullin].
- Yalnızca 8080'in komut kümesi uygulanmıştır.
- Z80'in genişletilmiş komut setini içerir. Bununla birlikte, sadece bit manipülasyon talimatları bulunur.

Son olarak, Z80 veya 8080'de bulunmayan **birkaç yeni talimat** da eklediler. Bunları tek tek açıklamanın bu makalenin kapsamını aştığını düşünüyorum, ancak ana fikir, Nintendo/Sharp'ın donanımı düzenleme biçimine göre koşullandırılmış belirli işlemleri optimize etmeleridir.

### Kullanılabilir hafıza

Nintendo genel amaçlı kullanım için (**Work RAM** veya 'WRAM' olarak adlandırdıkları) **8 KB RAM** yerleştirmiştir [@cpu-nintendo]. Bunun [NES](nes)'in içerdiğinden dört kat daha büyük olduğuna dikkat edin.

### Donanımsal erişim

SM83, 8 bitlik bir veri yolu ve 16 bitlik bir adres yolu bulundurur, böylece 64 KB'a kadar bellek adreslenebilir. Bellek haritası [@cpu-mongenel]'den oluşur:

- Kartuş alanı.
- WRAM ve Ekran RAM'i.
- I/O (joypad, ses, grafik ve LCD)
- Kesme kontrolleri.

## Grafikler

Tüm grafik hesaplamaları CPU tarafından yapılıyor, daha sonra **Resim İşleme Ünitesi** veya 'PPU' ekrana gönderiyor. Bu, DMG-CPU içinde bulunan başka bir bileşendir ve aslında [önceki PPU](nes#graphics)'ya dayanmaktadır.

Resim entegre bir LCD ekranda görüntülenir, **160×144 piksel** çözünürlüğe sahiptir ve **4 gri tonu** (beyaz, açık gri, koyu gri ve siyah) gösterir. Ancak orijinal Gameboy yeşil bir LCD'ye sahip olduğundan, grafikler *yeşilimsi* görünecektir.

NES makalesini daha önce okuduysanız, PPU'nun CRT ışınını takip edecek şekilde tasarlandığını hatırlayabilirsiniz. Ancak (ve bariz nedenlerden dolayı), Gameboy'da bir LCD ekranımız var. LCD'lerin de yenilenmesi gerektiğinden, yeni PPU bu kısmı değiştirmiyor. Hatta bu davranış sayesinde elde edilen bazı özel efektler Gameboy'da da desteklenebilir.

### İçeriğin düzenlenmesi

![PPU'nun bellek mimarisi.](ppu.png)

PPU'da **8 KB VRAM** veya hem PPU hem de CPU'nun doğrudan erişebildiği ancak aynı anda erişemediği 'Ekran RAM'i vardır. Bu 8 KB, PPU'nun grafikleri işlemek için ihtiyaç duyacağı verilerin çoğunu içerecektir. Kalan bitler, daha hızlı erişim hızı gerektireceğinden, bunun yerine PPU içinde depolanacaktır.

Oyun, farklı alanların doğru veri türleriyle doldurulmasından sorumludur. Dahası, PPU kayıtları açığa çıkarır, böylece oyun PPU'ya bu verilerin nasıl düzenleneceği konusunda talimat verebilir (yine de birçok kural vardır).

### Kare Oluşturulması

Şimdi PPU'nun ekrana bir şeyler çizmeyi nasıl başardığını görelim. Gösterim amacıyla, *Super Mario Land 2* örnek olarak kullanılacaktır:

#### Kareler {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Birden fazla karo.](ppu_mario/tiles.png){.active title="Hepsi"}

![Bir ızgara ile ayrılmış birden fazla karo.](ppu_mario/tiles_grid.png){title="Izgara"}

![Tek bir karo.](ppu_mario/tiles_single.png){title="Tek"}

Desen Tablosunda bulunan karolar.

:::

PPU, grafikleri, özellikle de **sprite ve arka planları** oluşturmak için temel bir bileşen olarak **tiles** kullanır [@graphics-emu].

Döşemeler sadece **8x8 bitmapler** olup VRAM'de **Döşeme seti** veya 'Döşeme desen tablosu' adı verilen bir bölgede saklanır ve her piksel mevcut dört gri tonundan birine karşılık gelir. Son olarak, karolar iki desen tablosunda gruplandırılır.

Resmi oluşturmak için karolara **Karo haritası** adı verilen başka bir tablo türünde başvurulur. Bu bilgi PPU'ya karoların nerede işleneceğini söyleyecektir. Çerçevenin farklı katmanlarını oluşturmak için iki harita saklanır.

Sonraki bölümlerde karo haritalarının katmanları oluşturmak için nasıl kullanıldığı açıklanmaktadır.

#### Arka Plan Katmanı {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![VRAM'de tahsis edilmiş Arka Plan haritası.](ppu_mario/background.png){.active title="Full"}

![Arka Plan haritasının seçilen alanı. Seçili kısmın üst kısmın bir bölümünü içerdiğine dikkat edin, bu _Window katmanı_ ile örtüşecektir.](ppu_mario/background_selected.png){title="Seçildi"}

![Görüntülenen Arka Plan haritası.](ppu_mario/background_rendered.png){title="İşlenmiş"}

Arka plan haritası oluşturma işlemi.

:::

Arka Plan katmanı **256x256 piksel** (32x32 karo) bir harita olup **statik karolar** içerir. Ancak, ekranda yalnızca 160x144'ün görüntülenebilir olduğunu unutmayın, bu nedenle hangi bölümün görüntülenmek üzere seçileceğine oyun karar verir. Oyunlar ayrıca oyun sırasında görüntülenebilir alanı hareket ettirebilir, **Scrolling Effect** bu şekilde gerçekleştirilir.

İki karo haritasından biri arka plan katmanını oluşturmak için kullanılabilir.

#### Pencere {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Ayrılmış Pencere haritası.](ppu_mario/window.png){.active title="Full"}

![Görüntülenen Pencere haritası. Oyun, son tarama çizgileri sırasında bunu etkinleştirir. Bu nedenle, ekranın alt kısmında yalnızca ilk satırlar gösterilir.](ppu_mario/window_rendered.png){title="Seçildi"}

Pencere oluşturma işlemi.

:::

Pencere, arka plan ve sprite'ların üstünde görüntülenen kutucukları içeren bir **160x144 piksel** katmandır. Kaydırmaz.

Kalan karo haritası pencere katmanına atanabilir.

İlk başta bu aptalca bir özellik gibi gelebilir. Sonuçta, pencere katmanı diğer her şeyle örtüşüyor, o halde ne işe yarıyor? Hem Arka Plan hem de Pencere, ekranın farklı bölümlerinde aynı anda kullanılabilir. Bu, belirli tarama satırları sırasında `LCDCONT` kaydı değiştirilerek gerçekleştirilir.

Bu nedenle, oyunlar normalde bunu oyuncu istatistiklerini, puanları ve diğer 'her zaman açık' bilgileri görüntülemek için kullanır.

#### Spritelar {.tab}

![Render edilmiş Sprite katmanı.](ppu_mario/sprite.png) {.tab-float.pixel}

Sprite'lar ekran etrafında bağımsız olarak hareket edebilen kutucuklardır. Ayrıca birbirleriyle örtüşebilir ve arka planın arkasında görünebilirler, görüntülenebilir grafik bir öncelik özelliğine göre kararlaştırılacaktır.

Ayrıca ekstra bir renk seçeneği de mevcuttur: **Transparent**. Dolayısıyla, dört yerine yalnızca üç farklı gri görüntüleyebilirler. Neyse ki bu katman, her renkten yararlanmak için iki renk paleti tanımlamaya izin veriyor.

**Object Attribute Memory** veya 'OAM', PPU içinde depolanan ve sprite olarak kullanılacak karoları belirten bir haritadır. Oyunlar bu bölgeyi çipin içinde bulunan **DMA birimini** çağırarak doldurur, DMA verileri ana RAM'den veya oyun ROM'undan OAM'ye getirir.

Tile indeksinden ayrı olarak, her giriş aşağıdaki öznitelikleri içerir: X-Y konumu, renk paleti, öncelik ve çevirme bayrakları (Tile dikey ve yatay olarak döndürmeye izin verir).

PPU, tarama satırı başına **on sprite** ve kare başına **40 sprite** oluşturma ile sınırlıdır, bunun aşılması sprite'ların çizilmemesine neden olur.

#### Sonuç {.tab}

![Son sonuç. _Tada!_](ppu_mario/result.png) {.tab-float.pixel}

Kare tamamlandığında, bir sonrakine geçme zamanı gelmiş demektir! Ancak, PPU VRAM'den okuma yaparken CPU tabloları değiştiremez, bu nedenle sistem PPU boştayken tetiklenen bir dizi kesme sağlar. Bu davranışı NES zamanlarından hatırlayabilirsiniz.

Tek bir tarama satırı tamamlandığında, **Horizontal Blank** kesmesi çağrılır. Bu, çerçevenin henüz çizilmemiş kısmıyla oynamanıza olanak tanır.

Tüm tarama satırları tamamlandığında, **Vertical Blank** kesmesi çağrılır. Oyun artık grafikleri bir sonraki kare için güncelleyebilir.

Tarama çizgisinin başlangıcında tetiklenen **OAM search** adında ekstra bir durum vardır, bu noktada PPU o tarama çizgisinde hangi sprite'ların görüntüleneceğini işler, böylece oyun OAM hariç herhangi bir bölgeyi güncelleyebilir.

### Sırlar ve Sınırlamalar {.tabs-close}

Pencere katmanının ve ekstra kesintilerin dahil edilmesi, yeni içerik ve efekt türlerine olanak sağladı.

#### Yalpalama etkisi {.tabs.active}

![The Legend of Zelda: Link's Awakening (1993). _Spoiler!_](zelda){.tab-float video="true"}

Yatay kesintiler, tamamlanmadan önce çerçevenin değiştirilmesine izin verir. Bu, her satıra farklı bir kaydırma değeri uygulanabileceği ve çerçevenin her satırının farklı hızlarda kaydırılacağı anlamına gelir.

Bu, bir *Yalpalanma etkisi* (bunun resmi adının bu olduğundan emin değilim) elde etti.

## Ses {.tabs-close}

Ses sistemi, dört kanallı bir [PSG yongası](master-system#audio) olan **Ses İşleme Birimi** (APU) tarafından gerçekleştirilir [@audio-wiki].

### Fonksiyonellik

Her kanal bir dalga biçimi türü için ayrılmıştır:

#### Nabız {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Dalga 1 kanalının osiloskop görünümü.](pulse_single_1){.active video="true" title="Nabız 1"}

![Dalga 2 kanalının osiloskop görünümü.](pulse_single_2){video="true" title="Nabız 2"}

![Tüm ses kanallarının osiloskop görünümü.](pulse_full){video="true" title="Tamamlandı"}

Pokemon Red/Blue (1996).

:::

Nabız dalgaları, çoğunlukla **melodi veya ses efektleri** için kullanılan çok belirgin bir *bip* sesine sahiptir.

APU her biri bir darbe dalgası için iki kanal ayırır. Bunlar, darbe genişlikleri değiştirilerek oluşturulan dört farklı tondan birini kullanır. İlk kanalda özel bir **Sweep kontrolü** mevcuttur.

Sınırlı sayıda kanal nedeniyle, oyunun bir parçası olarak efektlerin çalınması gerektiğinde melodi sık sık kesintiye uğrayacaktır. Bu, Pokemon Red/Blue gibi oyunlarda, bir savaş sırasında Pokemon'un çığlığı müzik için kullanılan tüm kanallarla çakıştığında çok belirgindir.

#### Ses {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Gürültü kanalının osiloskop görünümü.](noise_single){.active video="true" title="Ses"}

![Tüm ses kanallarının osiloskop görünümü.](noise_full){video="true" title="Tamamlandı"}

Pokemon Red/Blue (1996).

:::

Gürültü temelde beyaz statik gibi ses çıkaran bir dizi rastgele dalga formudur. Bunun için bir kanal tahsis edilmiştir.

Oyunlar bunu perküsyon veya *ambient* efektleri için kullanır.

Bu kanalda kullanılabilecek sadece 2 ton vardır, biri *temiz statik*, diğeri ise *robotik statik* üretir. Frekansı da kontrol edilebilir.

#### Dalga {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Dalga kanalının osiloskop görünümü.](wave_single){.active video="true" title="Dalga"}

![Tüm kanallarının osiloskop görünümü.](wave_full){video="true" title="Tamamlandı"}

Pokemon Red/Blue (1996).

:::

APU, dördüncü kanalından duyulmak üzere bir **özel dalga formu** tanımlanmasına izin verir. Dalga, bir dalga tablosunda saklanan 32 adet 4 bitlik örnekten oluşur.

Bu kanal aynı zamanda frekansını (aynı girişten farklı müzik notaları üretmesini sağlar) ve ses seviyesini kontrol etmeyi sağlar.

### Sırlar ve Sınırlamalar {.tabs-close}

Mikser stereo ses çıkışı verir, böylece kanallar sol tarafa veya sağ tarafa atanabilir, ancak bunu yalnızca kulaklıklardan duymak mümkündür! Hoparlör monodur.

Ayrıca, mikser çipi de kartuş üzerindeki özel bir pime bağlanarak, kartuşun analog sesi gerçekten vermesi şartıyla (yalnızca ekstra donanımla mümkündür) bir **ekstra kanal** akışına izin verir. Piyasadaki hiçbir oyun bu özelliği kullanmadı.

## Oyunlar

Oyunlar assembly ile yazılır ve maksimum **32 KB** boyuta sahiptirler, bunun nedeni mevcut adres alanının sınırlı olmasıdır. Ancak, bir **Bellek Bankası Denetleyicisi** (mapper) kullanıldığında, oyunlar daha büyük boyutlara ulaşabilir. Piyasada bulunan en büyük kartuş 1 MB ROM'a sahiptir.

Kartuşlar, oyun kayıtlarını tutmak için SRAM, saat ve harici bir pil içerebilir.

### Dış iletişim

İlk kez oyunlar, çok oyunculu işlevsellik sağlayan **Game Boy Link** kablosunu kullanarak diğer Game Boy'larla iletişim kurabilir. Arayüz çok ilkel bir **seri bağlantı** türüne dayanır.

## Korsanlıkla Mücadele

Bu konsol, kartuşun ROM'unu önyüklemek için kullanılan CPU'ya yerleştirilmiş bir **256 Bayt ROM** içerir. Ancak oyunu hemen çalıştırmaz, önce **yetkisiz kartuşların çalıştırılmasını önleyen** ve ayrıca kartuşun **doğru takıldığından emin olan** bir dizi kontrol gerçekleştirir.

Bu kontrollerden geçebilmek için, oyunların ROM başlığına Nintendo'nun logosunun bir kopyasını (kutucuklar şeklinde) eklemesi gerekiyordu [@gamesdhole], bu şekilde Nintendo dağıtımı kontrol etmek için **Telif Hakkı ve Ticari Marka** yasalarından yararlanabilirdi, *Zekice değil mi?*. Gameboy ROM'u aynı zamanda karşılaştırma yapabilmek için logonun bir kopyasını da içermektedir.

Bununla birlikte, önyükleme işlemi aşağıdaki gibidir [@anti_piracy-boot]:

1. Konsol açıldıktan sonra, CPU **0x00** adresinden (Gameboy'un ROM konumu) okumaya başlar.
2. RAM ve Ses başlatılır.
3. Nintendo logosu kartuş ROM'undan Display RAM'e kopyalanır ve ardından ekranın üst kenarına çizilir. Kartuş takılı değilse, logo çöp kutucukları içerecektir. Kötü yerleştirilmişse aynı şey olabilir.
4. Logo aşağı kaydırılır ve ünlü *po-ling* sesi çalınır.
5. Oyunun Nintendo logosu konsolun ROM'unda kayıtlı olanla eşleştirilir, kontrol başarısız olursa konsol donar.
6. Kartuşun doğru takıldığından emin olmak için kartuşun ROM başlığında hızlı bir **checksum** yapılır, kontrol başarısız olursa konsol donar.
7. Konsolun ROM'u bellek haritasından kaldırılır.
8. CPU oyunu yürütmeye başlar.

İlginçtir ki, ekranda görüntülenen *Nintendo* logosu VRAM'den temizlenmez, bu nedenle oyunlar kendi logolarını tanıtmak için bazı animasyon ve efektler uygulayabilir.

![20y, logo ile oynayan bir homebrew demosu.](20y){video="true"}

Oyunların içinde SRAM boyutunu kontrol etmek (normalde Bootleg'lerde daha büyüktür) ve oyunun rastgele noktalarında ROM'u sağlama toplamı almak gibi daha fazla korsan karşıtı önlem uygulanabilir.

## Hepsi bu kadar

