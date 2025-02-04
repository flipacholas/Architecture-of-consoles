---
short_title: Game Boy'un / Color'un Mimarisi
long_title: Game Boy'un / Color'un Mimarisi
name: Game Boy / Color
subtitle: Basit ve taşınabilir
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Models:
    - 
      title: "Orijinal"
      caption: "Orjinal Game Boy.<br>Japonya'da 21/04/1989, Amerika'da 31/07/1989 ve 28/09/1990'da Avrupa'da satışa sunuldu."
      img_class: reduced-width
      file: international
      active: true
    - 
      title: "Color"
      caption: "Game Boy Color adlı yeni nesil halefi.<br>21/10/1998 tarihinde Japonya'da, 18/11/1998 tarihinde Kuzey Amerika'da, 23/11/1998 tarihinde Avrupa'da piyasaya sürüldü."
      img_class: reduced-width
      file: color
  Motherboard:
    caption: "Revizyon '04' gösteriliyor. Not: 'DMG' orijinal Game Boy modelinin adlarından biridir."
  Diagram:
    caption: "Orijinal Game Boy'un."
#Historical
aliases:
  - "/projects/consoles/game-boy/"
  - "game-boy-color"
---

## Hızlı bir giriş

Game Boy serisi, NES'in sınırlı güce sahip taşınabilir bir versiyonu olarak hayal edilebilir, ancak çok ilginç yeni işlevler içerdiğini göreceksiniz.

### Gökkuşağı analizi

Bu konsolun muazzam popülaritesi, çeşitli revizyonlarla sonuçlandı (örneğin, Game Boy Pocket, Light ve hatta [Super Nintendo](super-nintendo) kartuşları şeklinde). Aslında, Game Boy markası iki nesli kapsar. 4. nesilde, monokrom Game Boy ve onun revizyonlarını buluyoruz ve bir sonraki nesilde Game Boy Color (Virtual Boy'un [ölümünden](virtual-boy) sonra piyasaya sürüldü) yer alıyor. İyi haber şu ki bu makale her iki ucu da kapsıyor. Sonuç olarak, Game Boy'un nasıl çalıştığını ve teknolojisinin daha sonra Game Boy Color'a nasıl dönüştüğünü iyi bir şekilde anlayacaksınız.

## {.supporting-imagery}

## İşlemci (CPU)

Nintendo, anakarta birçok hazır yonga yerleştirmek yerine, CPU dahil bileşenlerin çoğunu barındırmak (ve gizlemek) için **tek bir yongayı** seçti. Bu tür çiplere **System On a Chip** (SoC) adı verilir ve bu durumda, Nintendo'nun ihtiyaçlarına (diğer şeylerin yanı sıra güç verimliliği, korsanlığa karşı koruma ve ekstra G/Ç) göre uyarlanmasına olanak tanıyan bu konsol için özel olarak üretilmiştir. Aynı zamanda, bu çip herhangi bir perakende kataloğunda bulunamaz, yani o zamanki rakipler bunu klonlamakta daha zorlandılar.

Bununla birlikte, Game Boy'da bulunan SoC, **DMG-CPU** veya **Sharp LR35902** [@cpu-realboy] olarak adlandırılır ve adından da anlaşılacağı gibi **Sharp Corporation** tarafından üretilmiştir. Bu şirket, Ricoh ([NES'in CPU tedarikçisi](nes#a-bit-of-context)) ile birlikte Nintendo ile yakın bir ilişki içindeydi.

### CPU çekirdeği

DMG-CPU'da ana işlemci bir **Sharp SM83** [@cpu-gekkio] ve Z80 ([Sega Master System](master-system#cpu)'de kullanılan aynı CPU) ile Intel 8080 arasında bir karışımdır. Bu işlemci **~4,19 MHz** hızında çalışır, bu da ortalama 1 MHz CPU'dan daha hızlıdır, ancak saat hızlarının [aldatıcı](master-system#relative-comparison) olabileceğini unutmayın.

![DMG-CPU, Game Boy'un anakartında bulunur.](dmg_cpu.png)

Şimdi, <a href=“master-system”>Master System analizini</a> yaptığımda, Z80'in 8080'in bir üst kümesi olduğunu açıklamıştım. Peki, SM83'ün bu ikisinden neyi eksik ve neyi fazla? [@cpu-z80_comparison]

- Ne Z80'in `IX` veya `IY` kayıtları ne de 8080'in `IN` veya `OUT` komutları dahil edilmiştir. Bu, <a href=“master-system#accessing-the-rest-of-the-components”>I/O portlarının</a> kullanılamayacağı anlamına gelir. Bunun sadece maliyetleri düşürmek için bir önlem olup olmadığından emin değilim, ancak kesin olan bir şey var ki bileşenlerin **tamamen bellek eşlemeli** olması gerekecek [@cpu-fayzullin].
- Yalnızca Intel 8080'in register seti uygulanmıştır. Sonuç olarak, <strong x-id=“1”>14 register'lı Z80'in aksine (bir 'alternatif' setin eklenmesi nedeniyle) sadece yedi genel amaçlı register</strong> vardır.
- Z80'in genişletilmiş komut setinin bir kısmını (sadece bit manipülasyon talimatlarını) içerir.

Ayrıca Sharp, Z80 ya da 8080'de bulunmayan <strong x-id=“1”>birkaç yeni register</strong> da eklemiştir. Nintendo/Sharp'ın donanımı düzenleme biçimiyle ilgili belirli işlemleri optimize ederler. Bunun bir örneği de yeni `LDH` komutudur ('load from high memory' [@cpu-ldh] anlamına gelir), bu komut özellikle memory map'in son 256 baytında (adreslerin `$ff00`'dan başladığı yerde) çalışmak üzere eklenmiştir ve en önemlisi bir bayt daha az yer kaplar (ve dolayısıyla daha hızlıdır).

#### Renk etkisi

![Game Color'ın Ana Kartı [@photography-amos].](motherboard_cgb.png){.tabs-nested .active title="Orjinal"}

![Önemli kısımları etiketlenmiş aynı resim.](motherboard_cgb_marked.png){.tabs-nested-last title="İşaretlenmiş"}

Yaklaşık 10 yıl sonra, [Virtual Boy](virtual-boy) ve onun [ileri teknoloji donanımının](virtual-boy#cpu) terk edilmesinden sonra, mütevazı bir halef geldi: **Game Boy Color**. İçinde, birkaç eklemeler taşıyan yeni bir SoC olan **CPU CGB** bulunmaktadır. Ancak, SM83 CPU çekirdeği, saat hızı iki katına çıkmış (yani **~8.38 MHz** hızında) olmasına rağmen aynı kalıyor.

On yıl sonra Nintendo'nun aynı CPU'yu kullanmaya devam edeceğini düşünmek zor, ancak böyle bir karar şu avantajlarla birlikte gelir:

- Geliştiriciler, edindikleri yetenekleri yeni konsolu programlamak için yeniden kullanabilirler.
- Yeni bir mimari için sistemlerini yeniden tasarlamalarına gerek olmadığı için maliyet tasarrufu sağlanır.
- Geriye dönük uyumluluk, önemli bir çaba harcamadan mümkün hale gelir. Aslında, Nintendo bunu CPU CGB üzerinde iki çalışma modu programlayarak uyguladı:
  - **Normal mod**: SM83 **~4.19 MHz** hızında çalışır.
  - **Dual-speed mod**: SM83 **~8.38 MHz** hızında çalışır.

Ancak, bu, 90'ların sonları standartlarına göre eski teknoloji benimsemenin maliyetiyle gelir. [CPU pazarının durumuna](playstation#tab-1-1-a-bit-of-history) bakarak Nintendo'nun kaçırdığı fırsatları fark etmek için yeterli (adil olmak gerekirse, Nintendo [Virtual Boy](virtual-boy) ile denedi).

### Donanımsal erişim

SM83'de bir **8 bitlik veri yolu** ve bir **16 bitlik adres yolu** vardır, böylece **64 KB'a kadar bellek** adreslenebilir. Memory map esas olarak aşağıdaki uç noktalardan oluşur [@cpu-memory_map]:

- Game Pak (Oyun Kartuşu) alanı.
- Work RAM'i (WRAM), High RAM (HRAM) ve Display RAM'i (VRAM).
- I/O (joypad, ses, grafik ve LCD).
- Interrupt kontrolleri.

Bunlar, makale boyunca açıklanacaktır.

### Kullanılabilir hafıza

![DMG'nin (orijinal Game Boy) bellek mimarisi. PPU, VRAM erişimini yönetir.](dmg-ram.png)

Nintendo anakarta **8 KB RAM** yerleştirmiştir, bu genel amaçlı kullanım içindir (ki buna **Work RAM'i** veya 'WRAM' adını vermişlerdir) [@cpu-nintendo]. Bunun [NES](nes)'in içerdiğinden dört kat daha büyük olduğuna dikkat edin.

SoC'de yer alan ilave **127 B** RAM var. **High RAM** veya 'HRAM' olarak adlandırılır ve SM83'ün benzersiz `LDH` talimatı ile daha hızlı erişilebilen veriler için küçük bir alan sağlar. Bu, 6502'nin bellek konumuna dayalı performansı optimize eden "Zero Page" moduna [@cpu-zero_page] çok benzer. Şimdi, High RAM teknik olarak genel RAM'den daha hızlı erişilebilir değildir, ancak CPU için öncelikli bir alandır. DMA bileşenini tartıştığım 'Grafikler' bölümüne ulaştığınızda bunun ne anlama geldiğini göreceksiniz.

![CGB'nin (Game Boy Color) genişletilmiş bellek mimarisi. Yine PPU, VRAM'e erişim konusunda hakemlik yapar.](cgb-ram.png)

Daha sonra, Color varyantıyla birlikte, Nintendo WRAM'i **32 KB**'ye genişletti. Ancak, CPU değişmeden kaldığı için (özellikle adresleme yetenekleri), mevcut adres alanı taşmadan tüm yeni belleği bağlamak mümkün değildir. Bununla başa çıkmak için, Nintendo'nun mühendisleri [bank switching](nes#going-beyond-existing-capabilities) tekniğini kullandı. Başlangıçta [NES kartuşlarında](nes#cartridgegame-data) bulunan, Game Boy Color yalnızca 8 KB bellek alanı kullanarak bu 32 KB'ye erişmek için aynı prensibi kullanır. Hile basittir: son 4 KB, yedi farklı bank kullanılarak değiştirilebilir. Sonuç olarak, CPU bir bank değiştirici işlevi gören ekstra bir kayıt ("`SVBK`" adı verilen) sağlar, geliştiricilerin genişletilmiş belleği incelemek için bunu kullanmaları gerekir.

## Grafikler

Tüm grafik hesaplamaları CPU tarafından yapılıyor, daha sonra **Resim İşleme Ünitesi** veya 'PPU' ekrana gönderiyor. Bu, DMG-CPU içinde bulunan bir başka bileşendir ve [selefi grafik çipinin](nes#graphics) geliştirilmiş bir versiyonu olarak tanımlanabilir (aynı adla).

Resim entegre bir LCD ekranda görüntülenir, **160×144 piksel** çözünürlüğe sahiptir ve **4 gri tonu** (beyaz, açık gri, koyu gri ve siyah) gösterir. Ancak orijinal Game Boy yeşil bir LCD'ye sahip olduğundan, resim *yeşilimsi* görünecektir.

NES makalesini daha önce okuduysanız, PPU'nun CRT ışınını takip edecek şekilde tasarlandığını hatırlayabilirsiniz. Ancak (ve bariz nedenlerden dolayı), Game Boy'da bir LCD ekranımız var. Eh, yeni PPU da LCD'lerin de yenilenmesi gerektiğinden dolayı bu metodolojiyi izliyor. Böyle yaparak, bu konsol [CRT tabanlı efektler](nes#tab-1-4-background-split) sayesinde NES geliştiricilerinin daha önce yaratıcı içerikler sunabilmesini sağladı.

### İçeriğin düzenlenmesi

![PPU'nun bellek mimarisi.](ppu.png)

PPU, **8 KB VRAM** veya 'Display RAM'i ile bağlantılıdır. Bunu yaparken, CPU'ya kontrollü erişim sağlar. Bu 8 KB, PPU'nun grafikleri işlemek için ihtiyaç duyacağı verilerin çoğunu içerecektir. Geri kalanlar daha hızlı erişim hızları gerektirdiğinden PPU içinde depolanacaktır.

Oyun, farklı alanların doğru veri türleriyle doldurulmasından sorumludur. Dahası, PPU kayıtları açığa çıkarır, böylece oyun PPU'ya bu verilerin nasıl düzenleneceği konusunda talimat verebilir. Bununla birlikte, uyulması gereken birçok kural vardır (bunları ilerleyen bölümlerde göreceksiniz).

### Kare Oluşturulması

Şimdi PPU'nun ekrana bir şeyler çizmeyi nasıl başardığını görelim. Gösterim amacıyla, *Super Mario Land 2* örnek olarak kullanılacaktır.

#### Kareler {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Birden fazla karo.](ppu_mario/tiles.png){.active title="Hepsi"}

![Bir ızgara ile ayrılmış birden fazla karo.](ppu_mario/tiles_grid.png){title="Izgara"}

![Tek bir karo.](ppu_mario/tiles_single.png){title="Tek"}

Desen Tablosunda bulunan karolar.

:::

PPU, grafikleri, özellikle de **sprite ve arka planları** oluşturmak için temel bir bileşen olarak **tiles** kullanır [@graphics-overview].

Döşemeler sadece **8x8 bitmapler** olup VRAM'de **Döşeme seti** veya 'Döşeme desen tablosu' adı verilen bir bölgede saklanır ve her piksel mevcut dört gri tonundan birine karşılık gelir. Ancak pratikte gri tonları bir 'renk' paleti aracılığıyla seçilir. Tek renkli Game Boy'lar, bu paletleri tanımlayan register'lar içerir. Daha önce söylediğim gibi, seçim yapabileceğiniz sadece dört renk/gri tonu var, bu yüzden tek bir 8-bit register dört tonluk bir paleti sorunsuzca barındırabilir. Bununla birlikte, sistem **sınırlı kullanımlı** üç register (dolayısıyla, **üç programlanabilir palet**) sağlar (daha fazlası daha sonra açıklanacak).

Ayrıca, tile'lar **iki desen tablosunda** gruplandırılır.

Resmi oluşturmak için karolara **Tile map** adı verilen başka bir tablo türünde başvurulur. Bu bilgi PPU'ya karoların nerede işleneceğini söyleyecektir. Çerçevenin farklı katmanlarını oluşturmak için iki harita saklanır.

Sonraki bölümlerde karo haritalarının katmanları oluşturmak için nasıl kullanıldığı açıklanmaktadır.

#### Arka Plan Katmanı {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![VRAM'de tahsis edilmiş Arka Plan haritası.](ppu_mario/background.png){.active title="Full"}

![Arka Plan haritasının seçilen alanı. Seçili kısmın üst kısmın bir bölümünü içerdiğine dikkat edin, bu _Window_ katmanı ile örtüşecektir.](ppu_mario/background_selected.png){title="Seçildi"}

![Görüntülenen Background haritası.](ppu_mario/background_rendered.png){title="İşlenmiş"}

Arka plan haritası oluşturma işlemi.

:::

Arka Plan katmanı **256x256 piksel** (32x32 karo) bir harita olup **statik karolar** içerir. Ancak, ekranda yalnızca 160x144'ün görüntülenebilir olduğunu unutmayın, bu nedenle hangi bölümün görüntülenmek üzere seçileceğine oyun karar verir. Oyunlar ayrıca oyun sırasında görüntülenebilir alanı hareket ettirebilir, **Scrolling Effect** bu şekilde gerçekleştirilir.

İki karo haritasından biri arka plan katmanını oluşturmak için kullanılabilir. Ayrıca, bu katman için yalnızca **bir palet** mevcuttur.

#### Pencere {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Ayrılmış Window haritası.](ppu_mario/window.png){.active title="Full"}

![Görüntülenen Window haritası. Oyun, son scanline'lar sırasında bunu etkinleştirir. Bu nedenle, ekranın alt kısmında yalnızca ilk satırlar gösterilir.](ppu_mario/window_rendered.png){title="Seçildi"}

Pencere oluşturma işlemi.

:::

Pencere, arka plan ve sprite'ların üstünde görüntülenen kutucukları içeren bir **160x144 piksel** katmandır. Bu katman **kaymıyor**.

Geriye kalan döşeme haritası Window katmanına atanabilir, ayrıca Arka Plan katmanıyla aynı paleti paylaşır.

Sonuç olarak, bu aptalca bir özellik gibi gelebilir. Pencerenin saydam olmaması ve Dolayısıyla Arka Planı tamamen gizlemesi nedeniyle, 'Bu ne işe yarar?' diye merak edebilirsiniz. Hem Arka Plan hem de Pencere, ekranın farklı bölümlerinde **aynı anda** kullanılabilir. Bu, esas olarak ekranın altında bilgi görüntülemek için tasarlanmıştır, ancak NES'te bu, [karmaşık ve zamanlanmış yazmalar](nes#tab-1-4-background-split) gerektirirken, Game Boy'un PPU'su bunu otomatik olarak halledebilir.

Bu nedenle, oyunlar normalde bunu oyuncu istatistiklerini, puanları ve diğer 'her zaman açık' bilgileri görüntülemek için kullanır.

#### Spritelar {.tab}

![Render edilmiş Sprite katmanı.](ppu_mario/sprite.png) {.tab-float.pixel}

Sprite'lar ekran etrafında bağımsız olarak hareket edebilen kutucuklardır. Ayrıca birbirleriyle örtüşebilir ve arka planın arkasında görünebilirler, görüntülenebilir grafik bir öncelik özelliğine göre kararlaştırılacaktır.

Bu katman ayrıca bir ekstra renk seçeneği gerektirir: **Transparent**. Öncelikle, bu, dört yerine yalnızca üç farklı gri görüntüleyebilecekleri anlamına gelir. Neyse ki, **iki özel palet** seçeneği sunulan bu katman özellikle sağlanmıştır.

**Object Attribute Memory** veya 'OAM', PPU içinde depolanan ve sprite olarak kullanılacak karoları belirten bir haritadır. Bir karo haritası kullanmak yerine, **spritelar OAM'de tanımlanır**. Oyunlar genellikle bu bölgeyi çipin içinde bulunan **OAM DMA birimini** çağırarak doldurur, DMA verileri ana RAM'den veya oyun ROM'undan alır ve OAM'a gönderir. Şimdi, DMA çalışırken, CPU harici belleğe erişemez (bu süre zarfında HRAM kullanmanın önemi buradan gelir).

Tile indeksinden ayrı olarak, her giriş aşağıdaki öznitelikleri içerir: X-Y konumu, renk paleti, öncelik ve çevirme bayrakları (Tile dikey ve yatay olarak döndürmeye izin verir).

PPU, tarama satırı başına **on sprite** ve kare başına **40 sprite** oluşturma ile sınırlıdır, bunun aşılması sprite'ların çizilmemesine neden olur.

#### Sonuç {.tab}

![Son sonuç. _Tada!_](ppu_mario/result.png) {.tab-float.pixel}

Kare tamamlandığında, bir sonrakine geçme zamanı gelmiş demektir! Ancak, PPU VRAM'den okuma yaparken CPU tabloları değiştiremez, bu nedenle sistem PPU boştayken tetiklenen bir dizi kesme sağlar. Bu davranışı NES zamanlarından hatırlayabilirsiniz.

Tek bir scanline tamamlandığında, **Horizontal Blank** dönemi başlar. Bu, çerçevenin henüz çizilmemiş kısmıyla oynamanıza olanak tanır.

Tüm scanline'lar tamamlandığında, **Vertical Blank** periyodu başlar ve özel bir interrupt çağrılır. Oyun artık grafikleri bir sonraki kare için güncelleyebilir.

Tarama çizgisinin başlangıcında tetiklenen **OAM search** adında ekstra bir durum vardır, bu noktada PPU o tarama çizgisinde hangi sprite'ların görüntüleneceğini işler, böylece oyun OAM hariç herhangi bir bölgeyi güncelleyebilir.

### Sırlar ve Sınırlamalar {.tabs-close}

Pencere katmanının ve ekstra kesintilerin dahil edilmesi, yeni içerik ve efekt türlerine olanak sağladı.

#### Wobble Effect {.tabs.active}

![The Legend of Zelda: Link's Awakening (1993). _Spoiler!_](zelda){.tab-float video="true"}

Horizontal interrupts, tamamlanmadan önce çerçevenin değiştirilmesine izin verir. Bu, her satıra farklı bir kaydırma değeri uygulanabileceği ve çerçevenin her satırının farklı hızlarda kaydırılacağı anlamına gelir.

Bu, ilginç bir *Wobbling etkisi* (bunun resmi adının bu olduğundan emin değilim) elde etti.

### Renk eklemeleri {.tabs-close}

Game Boy Color'ın PPU'su, orijinalinin genişletilmiş bir versiyonu gibi davranır. 'Color' modelinin bu markaya ek olarak neler getirdiğini şimdi göreceksiniz.

#### Çalışma modları

![The Legend of Zelda: Link's Awakening DX (1998).<br>Bir CGB modunda çalışan hibrit bir Game Boy Color oyunu.](ppu_color/zelda.png) {.toleft.pixel}

![Game Boy Color'da görüldüğü gibi, Super Mario Land 2. Bu, renkli bir palet ekler ve DMG modunda çalışır.](ppu_color/dmg_mario.png) {.toright.pixel}

Öncelikle, uyumluluk nedenleriyle, yeni PPU **iki çalışma moduna** sahiptir. Yine de Nintendo, sadece monokrom oyunlarda bile Color kullanıcılarının iyileştirmeleri görmesini istedi. Sonuç olarak, iki çalışma modu aşağıdaki gibidir:

- **CGB modu**: Yeni Game Boy Color oyunlarını oluşturan tüm görsel iyileştirmelerle genişletilmiş PPU modu.
- **DMG modu**: Tüm ekstra özelliklerin devre dışı bırakıldığı geleneksel mod. Ancak, 'İşletim Sistemi' bölümünde göreceksiniz ki monokrom oyunlar yine de renk paletleriyle geliştirilmiştir.

#### (Yeni) İçeriğin düzenlenmesi

Artık CGB anakartında **16 KB VRAM** bulunuyor, bu da orijinal VRAM miktarının iki katıdır. CPU'nun adresleme sınırlamaları nedeniyle, bu yeni düzen **iki 8 KB bank** şeklinde uygulanmıştır, bir anahtar görevi gören yeni bir kayıtçı (`VBK` olarak adlandırılır) ile. Öte yandan, PPU her iki bank'a aynı anda erişebilir. Günün sonunda, bu, programcıların yalnızca `VBK` yardımıyla VRAM bankalarını doldurmaları ve ardından döşeme haritasında döşemenin bulunduğu bankayı belirtmeleri gerektiği anlamına gelir, böylece PPU geri kalanıyla ilgilenebilir.

Öyleyse, fazladan VRAM ile ne yapabilirsiniz? Birçok şey:

- İki kat daha fazla tile depolayabilir.
- Daha fazla renk yelpazesi sunan daha fazla palet saklayabilir.
- Daha fazla efekt kodlamak ve ek paletleri ele almak için tile metadata alanını genişletebilir.

##### Görseller

Yeni PPU sayesinde, programcılar artık **32.768 renkten** oluşan renk paletlerini tanımlayabilirler.

Öncelikle, geliştiriciler şimdi **Palette Memory** adı verilen yeni bir alanı doldurmalıdır, bu alan en fazla **on altı renk paleti** (yarısı Arka Plan ve Pencere için, yarısı sprite'lar için) saklar ve bunlar **dört rengi** kodlar [@cpu-nintendo]. Her giriş 16-bit değerinde (2 bayt) ve yalnızca 15 bit kullanılır. Palette Memory, CPU tarafından adreslenmez, ancak bu belleği üzerine yazmak için yeni bir register tampon olarak kullanılır ([Super Nintendo](super-nintendo#organising-the-content)'da bulunan bir metodoloji). Genel olarak, CPU paletleri bu şekilde tanımlar.

Bununla birlikte, Arka Plan ve Pencere karoları bu sekiz paletin herhangi birine referans verebilir. Aynı durum Sprite döşemelerinde de olur, ancak bir giriş 'şeffaf' renk için ayrıldığından üç renkli paletlerle sınırlıdırlar.

##### Ekstra alan

Devam ediyoruz, **Tile setleri şimdi iki kat daha büyük**. Böylece, programcılar VRAM'e iki kat daha fazla tile depolayabilirler. **Background/Window Tile maps da genişletildi**, bu da ekstra metadata kodlanmasıyla sonuçlandı. Sonuç olarak, bu katmanların yeteneklerini genişletildi. Örneğin, tile'lar artık **yatay ve dikey olarak çevrilebilir**, böylece oyun VRAM'de yinelenen grafikleri depolamaktan kurtulur (bu da, daha fazla benzersiz içerik çizmek için kullanılabilir).

Ayrıca, CPU CGB, **bir ekstra DMA ünitesi** de içerir, bu ünite Game Pak veya WRAM içeriğini VRAM'e kopyalayabilir. Bu [@cpu-nintendo] İki modda çalışır:

- **General-purpose DMA**: Transfer herhangi bir zamanda gerçekleşecek ve DMA, diğer bellek erişimlerinin önceliğini alacaktır. Bu nedenle, programcıların bu bileşeni ne zaman (scanline esnasında mı yoksa dışında mı) ve nasıl (kopyalanacak veri miktarı) kullanacakları konusunda dikkatli olmaları gerekir, çünkü yanlış kullanım ekran yırtılmasına yol açabilir (transfer sırasında VRAM erişimi engellenecektir).
- **Horizontal Blank (H-Blank) DMA**: Aktarım sadece H-Blank dönemlerinde gerçekleşecektir. Bu, ekran bozukluklarını önler ancak içeriği yalnızca 16 Baytlık gruplar halinde aktarabilir ve LCD taraması sırasında duraklar.

Bir kez daha, bu birim programcılara dönemlerin başlangıçta boşta bırakıldığı için daha zengin içerik sağlama konusunda yeni olanaklar sunar.

## Ses

Ses sistemi, dört kanallı bir [PSG yongası](master-system#audio) olan **Ses İşleme Birimi** (APU) tarafından gerçekleştirilir [@audio-overview].

İlginçtir ki, bu modeller arasında evrim geçirmeyen birkaç bölümden biridir. Aslında, osilatörlerin hızını değiştirirseniz, daha 'iyi' sesler duymazsınız, sadece daha yüksek bir pitch duyarsınız, bu yüzden hızlandırılamaz bile.

### Fonksiyonellik

Dört kanalın her biri bir dalga biçimi türü için ayrılmıştır:

#### Nabız {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Pulse 1 kanalının osiloskop görünümü.](pulse_single_1){.active video="true" title="Pulse 1"}

![Pulse 2 kanalının osiloskop görünümü.](pulse_single_2){video="true" title="Pulse 2"}

![Tüm ses kanallarının osiloskop görünümü.](pulse_full){video="true" title="Sonuç"}

Pokemon Red/Blue (1996).

:::

Pulse dalgaları, çoğunlukla **melodi veya ses efektleri** için kullanılan çok belirgin bir *bip* sesine sahiptir.

APU her biri bir pulse dalgası için iki kanal ayırır. Bunlar, pulse genişlikleri değiştirilerek oluşturulan dört farklı tondan birini kullanır. İlk kanalda özel bir **Sweep kontrolü** mevcuttur.

Sınırlı sayıda kanal nedeniyle, oyunun bir parçası olarak efektlerin çalınması gerektiğinde melodi sık sık kesintiye uğrayacaktır. Bu, Pokemon Red/Blue gibi oyunlarda, bir savaş sırasında Pokemon'un çığlığı müzik için kullanılan tüm kanallarla çakıştığında çok belirgindir; bu yüzden hiçbir Pokémon savaş müziği perküsyon kullanmaz.

#### Dalga {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Wave kanalının osiloskop görünümü.](wave_single){.active video="true" title="Dalga"}

![Tüm kanallarının osiloskop görünümü.](wave_full){video="true" title="Tamamlandı"}

Pokemon Red/Blue (1996).

:::

APU, üçüncü kanalından duyulmak üzere bir **özel dalga formu** tanımlanmasına izin verir. Dalga, bir dalga tablosunda saklanan 32 adet 4 bitlik örnekten oluşur.

Bu kanal aynı zamanda frekansını (aynı girişten farklı müzik notaları üretmesini sağlar) ve ses seviyesini kontrol etmeyi sağlar.

#### Ses {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Noise kanalının osiloskop görünümü.](noise_single){.active video="true" title="Ses"}

![Tüm ses kanallarının osiloskop görünümü.](noise_full){video="true" title="Sonuç"}

Pokemon Red/Blue (1996).

:::

Ses temelde beyaz statik gibi ses çıkaran bir dizi rastgele dalga biçimidir. Bunun için bir kanal tahsis edilmiştir.

Oyunlar bunu perküsyon veya *ambient* efektleri için kullanır.

Bu kanalda kullanılabilecek sadece 2 ton vardır, biri *temiz statik*, diğeri ise *robotik statik* üretir. Frekansı da kontrol edilebilir.

### Sırlar ve Sınırlamalar {.tabs-close}

Mikser stereo ses çıkışı verir, böylece kanallar sol tarafa veya sağ tarafa atanabilir ancak bunu yalnızca kulaklıklardan duymak mümkündür! Çünkü hoparlör monodur.

Ayrıca, mixer çipi de kartuş üzerindeki özel bir pine bağlanarak, kartuşun analog sesi vermesi şartıyla (yalnızca ekstra donanımla mümkündür) bir **ekstra kanal** akışına izin verir. Bununla birlikte, piyasadaki hiçbir oyun bu özelliği kullanmadı ve [Game Boy Advance](game-boy-advance)'ın [geri uyumlu alt sistemi](game-boy-advance#becoming-a-game-boy-color) üzerinde de bu pini bulamazsınız.

## İşletim Sistemi

NES'in aksine, doğrudan oyuna başlayan Game Boy, her zaman dahili **256 Byte ROM** ile başlaması ve ardından oyun koduna geçmesi için tasarlanmıştı. Önyükleme süreci şu şekildedir [@operating_system-boot]:

1. Konsol açıldıktan sonra, CPU **0x0000** adresinden (Game Boy'un ROM konumu) okumaya başlar.
2. RAM ve APU başlatılır.
3. Nintendo logosu kartuş ROM'undan Display RAM'e kopyalanır ve ardından ekranın üst kenarına çizilir. Kartuş takılı değilse, logo bozuk tile'lar içerecektir. Kötü yerleştirilmişse de aynı şey olabilir.
4. Logo aşağı kaydırılır ve ünlü *po-ling* sesi çalınır.
5. Oyunun Nintendo logosu, konsolun ROM'unda kayıtlı olanla eşleştirilir. Daha sonra, kartuşun doğru takıldığından emin olmak için kartuşun ROM başlığında hızlı bir **checksum** yapılır. Bu kontrollerin herhangi biri başarısız olursa, konsol donar.
6. Konsolun ROM'u bellek haritasından kaldırılır.
7. CPU oyunu yürütmeye başlar.

İlginçtir ki ekranda görüntülenen *Nintendo* logosu VRAM'den temizlenmez, bu nedenle oyunlar kendi logolarını tanıtmak için bazı animasyon ve efektler uygulayabilir.

![Game Boy'un açılış ekranı ve logoyla uğraşan bir homebrew demosu olan 20y.](20y){video="true"}

### Gözden geçirilmiş dizi

![Game Boy Color'ın açılış ekranı, logo artık kaymıyor ancak gökkuşağı efekti içeriyor.](boot_cgb.png) {.pixel}

Bir kez daha, Game Boy Color durumunda, ROM'un içeriğinde köklü değişiklikler buluyoruz. Örneğin, ROM'un boyutu şimdi **2 KB** [@isletim_sistemi-boot_cgb] ve rutinleri yeni bir davranış sergiler:

- Önyükleme dizisi artık yalnızca Game Boy veya Game Boy Color oyununun eklenip eklenmediğini kontrol edecek ve ardından DMG veya CGB modunu etkinleştirmek için ilgili kayıtları ayarlayacaktır. Önyükleme kodu, CGB oyunları için farklı şekilde yapılandırılmış olan belirli oyun meta verilerini (kartuşun ROM'u içinde) kontrol eder.
- DMG oyunu yerleştirildiğinde, önyükleme programı hesaplanan paletlerle Palette RAM'i dolduracaktır, bunlar, oyunun meta verilerine de dayanan basit ama akıllıca bir algoritmaya dayanmaktadır. Bu, yeni nesil konsolda çalışırken monokrom oyunların renkli görünmesini sağlar.
  - Bu aşamada, kullanıcı önyükleme kodunun renk paletini değiştirecek bir tuş kombinasyonu basabilir.
- Nintendo logosu kontrolü artık HRAM'ı da kullanıyor (ancak boşuna).

## Oyunlar

Oyunlar assembly ile yazılır ve maksimum **32 KB** boyuta sahiptirler, bunun nedeni mevcut adres alanının sınırlı olmasıdır. Ancak bir **Memory Bank Controller** ([mapper](nes#going-beyond-existing-capabilities)) kullanımı ile oyunlar daha büyük boyutlara ulaşabilir. Piyasada bulunan en büyük Game Pak (kartuş) 1 MB ROM'a sahiptir (Game Boy Color'da ise 8 MB'dır).

Game Pak'lar, oyun kayıtlarını tutmak için SRAM'ın yanı sıra gerçek zamanlı bir saat ve harici bir pil içerebilir, ancak bunların hepsi isteğe bağlıdır.

### Kartuş türleri

Game Boy Color'un desteklediği iki işletim modu nedeniyle, Nintendo **üç farklı türde** Game Boy oyununu listeledi:

- **Game Boy**: Tüm Game Boy modelleriyle tamamen uyumlu bir tür. Her zaman DMG modunda çalışır.
- **Game Boy Color enhanced**: Bağlı olduğu konsola bağlı olarak farklı çalışan bir hibrit. Monokrom modelle tamamen uyumlu olsa da (CGB modunda çalıştığı için) Game Boy Color'da çalışırken görsel olarak geliştirilecektir. Bununla birlikte, genel işlevsellik, Game Boy ile uyumluluğu sürdürmek için sınırlı kalacaktır.
- **Game Boy Color exclusive**: Yalnızca Game Boy Color ile uyumlu ancak donanımının tüm avantajlarından yararlanıyor.

### Dış iletişim

![Farklı konsol çeşitleri için Game Boy Link kablosu [@fotografi-amos]. Orijinal konsolun piyasaya sürülmesinden bu yana, sonraki revizyonlar soketi küçültmüştür, ancak aynı sayıda pini korumuştur.](link_cable.png) {.no-borders}

İlk kez oyunlar, çok oyunculu işlevsellik sağlayan **Game Boy Link** kablosunu kullanarak diğer Game Boy'larla iletişim kurabilir, örneğin arayüz çok ilkel bir **seri bağlantı** türüne dayanır.

Orijinal Game Boy, bilgiyi saniyede 8 Kbit (1 KB/sn) hızla aktarırken, Color varyantı saniyede 512 Kbit'e kadar (64 KB/sn) ulaşabilir, bu daha yüksek hız 'high speed' modu olarak biliniyordu.

İkinci varyant ayrıca bir **kızılötesi yayıcı ve alıcı** (sırasıyla bir LED ve bir fototransistörden yapılmış) ile birlikte gelir [@cpu-nintendo]. Bu, Pokemon Gold ve benzeri oyunlarda görüldüğü gibi, Game Boy Color'lar arasında kablosuz veri alışverişini mümkün kıldı. Ancak, sistemin herhangi bir iletişim protokolü uygulamadığını, yalnızca IR sensörünün eylemini (yayma veya alma), aktarılan tek bit (`0` veya `1`) ve alınan son biti kodlayan tek bir kayıt (`RP`) içerdiğini göreceksiniz. Yine de, geliştiricilerin işini kolaylaştırmak için Nintendo, resmi Game Boy Geliştirici kılavuzuna [@cpu-nintendo] bir referans uygulaması dahil etti.

## Korsanlıkla Mücadele

'İşletim Sistemi' bölümünde gördüğünüz gibi, konsol asla bir oyunu hemen çalıştırmaz, önce **yetkisiz kartuşların çalıştırılmasını önleyen** ve ayrıca kartuşun **doğru takıldığından emin olan** bir dizi kontrol gerçekleştirir.

Bu kontrollerden geçebilmek için, oyunların ROM başlığına Nintendo'nun logosunun bir kopyasını (kutucuklar şeklinde) eklemesi gerekiyordu [@gamesdhole], bu şekilde Nintendo dağıtımı kontrol etmek için **Telif Hakkı ve Ticari Marka** yasalarından yararlanabilirdi. *Oldukça zekice, değil mi?*

Tersine, _Sega v. Accolade_ davası sonrasında, bu tür gereksinimler adil kullanım kapsamına girdiğinden, herhangi bir şirkete telif hakkıyla korunan logoları kullanma hakkı verildi.

Yine de, oyunların içinde SRAM boyutunu kontrol etmek (normalde Bootleg'lerde daha büyüktür) ve oyunun rastgele noktalarında ROM'u sağlama toplamı almak gibi daha fazla korsan karşıtı önlem uygulanabilir.

## Hepsi bu kadar
