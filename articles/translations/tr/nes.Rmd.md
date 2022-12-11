---
long_title: "Nintendo Entertainment System (NES) Mimarisi"
short_title: "Nintendo Entertainment System (NES) Mimarisi"
name: NES
long_name: Nintendo Entertainment System (NES)
subtitle: 6502 makineden daha fazlası
date: 2019-01-25
release_date: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - 
      title: "Uluslararası"
      file: international
      caption: "NES.<br>18/10/1985 tarihinde Amerika'da ve 01/09/1986 tarihinde Avrupa'da piyasaya sürüldü."
      active: true
    - 
      title: "Japonca"
      file: japanese
      caption: "Famicom.<br>15/07/1983 tarihinde Japonya'da piyasaya sürüldü."
  Motherboard:
    caption: "\"NES\" varyantı gösteriliyor."
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nes
---

## Hızlı bir giriş

İlk bakışta NES, sofistike bir kasaya ve bir denetleyiciye sahip başka bir 6502 bilgisayar gibi görünüyor.

Bu *teknik* olarak doğru olsa da, CPU'nun neden bu sistemin *merkezi* parçası olmadığını size göstermeme izin verin.

```{r results="asis"}
supporting_imagery()
```

## Modeller ve varyantlar

Nintendo, dünya çapında aynı konsolun çok sayıda farklı varyantını gönderdi [@general-variants] ve hepsi aynı mimariyi paylaşsa da, çoğu önemli ölçüde farklı görünüyor ve bazıları yerleşik aksesuarlar içerebiliyor. Bu nedenle, bu makaleyi basit tutmak için en popüler iki revizyona odaklanacağım:

- **Family Computer** (_Famicom_ olarak bilinir) ilk enkarnasyondu, ancak yalnızca Japonya'da piyasaya sürüldü. Bu oyuncak görünümlü tasarımda iki adet çıkarılamayan kontrolör (ikinci kontrolörde dahili bir mikrofon bulunur), ışık tabancası için bir ön soket (_Zapper_ olarak adlandırılır), RF video çıkışı (NTSC-J sinyalini kullanır) ve ses özelliklerini genişletmek için kartuş yuvasında ekstra pinler bulunur.
- **Nintendo Entertainment System** (_NES_ olarak bilinir) Kuzey Amerika, Avrupa ve Okyanusya'da yaşayan batılı izleyiciler için yeniden tasarlanmış bir sürümdü; tipik bir set üstü kutuyla eşleşen bir görünüme sahipti. Teknik açıdan, kontrolörler artık sökülebilir (ve mikrofonsuz) ve video çıkışı ekstra NTSC/PAL kompozit RCA konektörleriyle geliştirildi, ancak ses genişletmesi korsan karşıtı bir alt sistemle değiştirildi. Üstüne üstlük, kasanın alt kısmında kullanılmayan bir 'genişleme portu' ve bu portla iletişim kuran ekstra kartuş pinleri [@general-cartridge] bulunmaktadır.

Yazar 'NES' adıyla büyüdüğü için, genel olarak konsola atıfta bulunmak için bu terimi kullanacağım, ancak yalnızca Japon varyantında bulunan benzersiz özelliklere atıfta bulunurken 'Famicom' adına geçeceğim.

## İşlemci (CPU)

NES'in CPU'su popüler 8-bit **MOS Technology 6502** tabanlı bir **Ricoh 2A03** [@cpu-cpu] olup **1,79 MHz** (veya PAL sistemlerinde 1,66 MHz) hızında çalışır.

### Biraz bağlam

70'lerin sonu ve 80'lerin başında CPU pazarı oldukça çeşitliydi. Eğer bir şirket uygun fiyatlı bir mikrobilgisayar üretmek istiyorsa, aşağıdaki seçenekler mevcuttu:

- **Intel 8080**: ilk 'kişisel' bilgisayar olan *Altair*'de yer alan popüler bir CPU. 8 bitlik bir veri yolu ve 16 bitlik bir adres yolu vardır.
- **Zilog Z80**: 8080'in daha fazla talimat, kayıt ve dahili bileşenle geliştirilmiş 'resmi olmayan' bir versiyonu. Daha ucuz bir fiyata satıldı ve hala 8080 programlarını çalıştırabiliyordu [@cpu-re_1977, s. 86]. Amstrad ve Sinclair (diğerlerinin yanı sıra) bu CPU'yu seçti.
- **Motorola 6800**: Motorola tarafından tasarlanan bir başka 8-bit CPU, tamamen farklı bir komut seti içerir. Birçok kendin yap bilgisayar kiti, sentezleyici ve hepsi bir arada bilgisayar 6800'ü içeriyordu.

Bu seçenekler yetmezmiş gibi, **MOS** adında başka bir şirket piyasaya çıktı ve 6800'ün yeniden tasarlanmış bir versiyonunu sundu: **6502**. Diğerleriyle uyumsuz olsa da, yeni çipin üretimi çok *çok* daha ucuzdu \[@cpu-summary_costs\] \[@cpu-re_1981, s. 76\] ve en ünlü bilgisayar üreticilerinin (Commodore, Apple, Atari, Acorn ve diğerleri) makinelerine güç sağlamak için 6502'yi seçmeleri sadece bir zaman meselesiydi.

Japonya'da Nintendo'nun ucuz ama geliştirmeye uygun bir şeye ihtiyacı vardı, bu yüzden 6502'yi seçtiler. CPU tedarikçisi **Ricoh**, 6502 uyumlu bir CPU'yu başarıyla üretti.

#### Ricoh'nun lisanslama muamması

Ricoh'un 6502'yi *nasıl* klonlamayı başardığı bugün bile net değil. MOS'un çip tasarımını Ricoh'a lisanslamış olması beklenebilir, ancak bununla ilgili birçok çelişki var:

- Hem Ricoh'un hem de MOS'un versiyonu aynı düzene sahiptir, ancak Ricoh'unki kesilmiş veri yolları içerir (belirli işlevleri devre dışı bırakır) [@cpu-differences]. Daha sonra daha fazla ayrıntıya gireceğim.
- MOS'un 6502'yi Ricoh'a lisansladığını açıkça belirten bir belge henüz bulunamamıştır.
- Nikkei Trendy tarafından 2008 yılında yayınlanan bir makalede Ricoh'un yetkili bir çip üreticisi olan Rockwell'den lisans aldığı belirtilmektedir [@cpu-trendi]. İkinci bir kaynağın, en azından MOS'un onayıyla, üçüncü bir tarafa IP sağlayıp sağlayamadığı tartışmalı olsa da.
- Bu, Nintendo'nun fikri mülkiyet haklarını çiğneyerek paçayı kurtardığı ilk olay olmayacaktı, zira *Ikegami Tsushinki v. Nintendo* Japonya'da Nintendo'nun orijinal Donkey Kong'un [@cpu-dk] koduna sahip olmadığına karar verdi.

#### Hurdaya çıkarılan fonksiyonlar

Ricoh 2A03, orijinal olarak 6502'de bulunan **Binary-Coded Decimal** (BCD) modunu içermemektedir [@cpu-visualbcd]. BCD, bir sayının her ondalık basamağını ayrı bir 4 bitlik ikili olarak kodlar. 6502, 8 bit 'kelime' kullanır - yani her kelime iki ondalık basamak depolar.

Meraklısı için bir örnek olarak, ondalık sayı `42` olarak gösterilir:

- `0010 1010` binary'de.
- `0100 0010` BCD'de.

Bu konuda konuşmaya devam edebiliriz, ancak ana hatlarını vermek gerekirse: BCD, her ondalık basamağın ayrı ayrı ele alınmasını gerektiren uygulamalar için kullanışlıdır (örneğin, dijital bir saat). Bununla birlikte, her 8 bitlik kelime yalnızca `99` ondalık sayısına kadar kodlayabildiğinden daha fazla depolama gerektirir - oysa geleneksel ikili `255`'e kadar kodlayabilir.

6502, 8 bit 'kelime' kullanır - yani her kelime iki ondalık basamak depolar. Bu muhtemelen MOS'a telif ücreti ödememek için yapıldı, çünkü BCD'nin patenti onlar tarafından alınmıştı (ve Amerika Birleşik Devletleri'nde entegre devre düzenlerinin telif hakkını sağlayan mevzuat 1984 yılına kadar yürürlüğe girmemişti [@cpu-protection_act).

### Bellek

Hem Ricoh 2A03 hem de MOS 6502, **8-bit veri yolu** ve **16-bit adres yoluna** sahiptir, bu da **64 KB'a kadar belleğe** erişmelerini sağlar. Peki, Nintendo bu bellek alanını nasıl doldurdu?

Anakartın bir tarafında **2 KB Statik RAM** (SRAM) [@cpu-sample_ram] sağlayan bir yonga bulunmaktadır. Nintendo bu alanı 'İş RAM'i' (WRAM) olarak adlandırır ve depolamak için kullanılabilir:

- Oyun durumunu işlemek ve/veya bilgi aramak için değişkenler.
- CPU alt rutinleri yürütürken kayıt değerlerini geçici olarak kaydeden 'yığın'.
- CPU'nun iki konum arasında büyük verileri kopyalayabilmesi için bir 'tampon alan'.

Diğer taraftan, sistemin bileşenleri **memory-mapped** [@cpu-cpu_map], yani bellek adresleri kullanılarak erişilirler, dolayısıyla CPU'nun adres alanının bir kısmını işgal ederler. Bu nedenle CPU'nun bellek alanı oyun kartuşu, WRAM, PPU, APU ve iki denetleyiciye işaret eden adreslerle doldurulur (bu makale boyunca açıklandıkları için her bir bileşen hakkında endişelenmeyin).

#### Kartuş/oyun verileri

Bilmiyorsanız söyleyeyim, NES oyunları kartuşlar halinde dağıtılır ve kartuşların veri yolları doğrudan CPU'ya bağlanır.

Nintendo kartuş hatlarını sadece 49120 Bayt (~ 49.97 KB) kartuş verisine erişilebilecek şekilde bağladı [@cpu-cpu_map]. Şimdi, 'kartuş verileri' ile ne demek istiyorum? Örneğin, bu veri yollarına bağlı herhangi bir çip:

- Oyunun programının bulunduğu bir **Program ROM**'u. Bu, daha sonra 'Grafikler' bölümünde göreceğiniz gibi grafik verilerini hariç tutar. Doğal olarak ve diğer çiplerden farklı olarak, bu çip zorunludur.
- WRAM'i genişletmek için **RAM çipleri**.
- Kayıtları saklamak için bir **pilden güç alan RAM çipi**.

Farklı kombinasyonların olması, CPU'nun ne tür bir bileşenden okuma yapıldığını önemsememesi, sadece bellek konumlarını görmesi gerçeğinden kaynaklanmaktadır. Dolayısıyla, oyunlarını sığdırmak için uygun bir düzen seçmek (veya bulmak) oyun stüdyolarına kalmıştır.

![Super Mario Bros'un PCB'si [@photography-nrom].](nrom.png){.tabs-nested .active title="Orjinal"}

![Önemli parçaları etiketlenmiş aynı PCB. 'Kilitleme' çipinin anlamı 'Korsanla Mücadele' bölümünde açıklanmıştır.](nrom_marked.png){.tabs-nested-last title="İşaretli"}

Örneğin, Nintendo'nun 'Super Mario Bros' oyunu _NES-NROM-256_ adını verdikleri bir düzen kullanmıştır ve 32 KB program ROM'u ve grafikler için 8 KB 'Karakter RAM'inden oluşmaktadır ('Grafikler' bölümünde daha fazlasını göreceğiz) [@cpu-nrom]. _NES-NROM-256_ ayrıca 3 KB'a kadar ekstra WRAM barındıracak şekilde hazırlanmıştır, ancak oyun bunu kullanmamaktadır.

#### Mevcut yeteneklerin ötesine geçmek

16-bit adres veri yollarının en büyük sınırlamalarından biri (3. ve 4. nesil konsolları etkileyen) kompakt adres alanlarıdır. Günümüzde 32-bit bilgisayarlar 4 GB'a kadar bellek adresleyebilmektedir (ve 64-bit makineler 16 exabyte'a kadar cömertçe kullanmaktadır), bu nedenle bu artık bir sorun değildir, ancak o zamanlar NES'in yalnızca 64 KB adres alanı vardı ve bunun büyük bir kısmı bellek eşlemeli donanım tarafından tüketiliyordu ([rakiplerin kaçındığı bir şey](master-system#accessing-the-rest-of-the-components)).

Peki bu, oyun stüdyolarının yalnızca 49,97 KB sınırını aşmayan oyunlar geliştirebileceği anlamına mı geliyordu? Kesinlikle değil! Tarih bize bir şey öğrettiyse, o da zorlu bir soruna her zaman akıllıca bir çözüm bulunabileceğidir; ve bu sorun bir **Mapper** ile ele alındı.

![Bir eşleyicinin CPU'nun adresleme yeteneklerini nasıl genişlettiğinin basitleştirilmiş gösterimi. CPU, bir mapper'ın dahil edilmesiyle, büyük bir Program ROM'unun ekstra bankalarına (adres grupları) erişebilir. Her ne kadar oyun/program gerektiğinde bankalar arasında manuel geçiş yapma gibi yeni bir göreve sahip olsa da.](mapper/mapper.png){.tabs-nested .active title="Mapper ile"}

![Aynı kurulum ancak eşleyici yüklü değil. Daha basit ve ucuz olsa da, CPU yalnızca sınırlı sayıda bankaya erişebilir.](mapper/no_mapper.png){.tabs-nested-last title="Mapper olmadan"}

Eşleyici, kartuşta bulunan ve bellek yongaları ile konsolun adres hatları arasında yer alan ekstra bir yongadır. Ana görevi, geliştiricilerin daha fazla yonga sığdırabilmesi için adres alanını genişletmektir. Bu **bank switching** ile yapılır: Bellek adresleri bankalar halinde gruplandırılır ve eşleyici bankalar arasında geçiş yapmak için anahtarlar (bellek adresleri aracılığıyla kontrol edilir) sağlar. Şimdi, CPU hala aynı miktarda bellek görüyor, bu yüzden onu çalıştırmaktan sorumlu bir eşleyici ile programlanmış olan oyun. Maliyet etkinliği nedeniyle, 80'lerden 90'ların başlarına kadar teknolojide haritacılar ön plandaydı.

![Super Mario Bros 3'ün PCB'si [@photography-tsrom]](tsrom.png){.tabs-nested .active title="Orjinal"}

![Önemli kısımları etiketlenmiş aynı resim. İlk başta, ekstra WRAM'in kayıtları saklamak için olduğunu düşündüm, ancak daha sonra bu oyunda kayıt olmadığını fark ettim (ve bir pil de yok). Gerçekte, bu RAM yongası sıkıştırılmış bir seviyeyi depolamak için kullanılır.](tsrom_marked.png){.tabs-nested-last title="İşaretli"}

NES'e geri dönersek, ünlü bir örnek, kartuşunda 'MMC3' eşleyici (Nintendo tarafından yapılmıştır) ile birlikte gönderilen 'Super Mario Bros 3'tür. Karşılaştırma için MMC3, Program ROM'u için 512 KB'a kadar, Karakter belleği için 256 KB'a kadar ve ekstra WRAM için 8 KB'a kadar alan sağlamıştır [@cpu-mmc3]. Şimdi 'Super Mario Bros 3'ün ilk taksitle karşılaştırıldığında neden kalite açısından önemli ölçüde farklı olduğunu görebilirsiniz.

Sonuç olarak, bu konsol dahili özelliklerini incelerken sınırlı görünse de, Nintendo teknoloji geliştikçe uyum sağlayabileceğinden emin oldu. Diğer taraftan, bu teknik konsolun maliyetlerini düşük tutmaya yardımcı olurken, yükün bir kısmını oyun kartuşuna kaydırdı. Dolayısıyla, oyun kalitesi ve kartuş maliyetleri, oyun stüdyolarının dengelemek zorunda olduğu iki endişeydi.

## Grafikler

Grafikler **Picture Processing Unit** (PPU) adı verilen özel bir çip tarafından üretilir. Bu, NES'e *kimlik* kazandıran çiplerden biridir. Başka bir şekilde ifade etmek gerekirse, herkes nalburdan 6502 CPU alabilir, o halde NES'in örneğin bir Apple 2 veya Commodore 64'ten ne farkı var? NES'i diğer makinelerden ayıran şey CPU'yu çevreleyen çiplerdir: PPU ve APU. Bunlar sırasıyla NES'in benzersiz grafik ve ses özelliklerini oluşturur.

Bununla birlikte PPU, **sprites** ve **backgrounds** adı verilen 2D grafikleri işleyerek sonucu video sinyaline aktarır.

### İçeriğin düzenlenmesi

![PPU'nun bellek mimarisi](ppu_content.png)

Ekranda bir şey oluşturmak için PPU'nun *hangi* grafikleri çizeceğini, *ekranda nereye* yerleştireceğini ve *nasıl* çizeceğini (yani hangi paleti kullanacağını) bilmesi gerekir.

Bu soruları yanıtlamak için PPU, aşağıdaki veri türlerini arayan farklı bir bellek haritası ile önceden programlanmış olarak gelir:

- Grafik verileri, **Karakter Belleği** adı verilen ve 2B çizimleri (**tiles** adı verilen) **Pattern table** adlı bir veri yapısında düzenlenmiş olarak saklayan özel bir çip içeren oyun kartuşundan alınır. Karakter Belleği, oyunun değişmez bir grafik setiyle birlikte gelmesine veya CPU'nun müdahale etmesi gerekmesine bağlı olarak sırasıyla 'Salt Okunur Bellek' (ROM) veya 'Rastgele Erişimli Bellek' (RAM) şeklinde gerçekleşir.
  - PPU, her biri 4 KB'lık iki grup halinde düzenlenmiş **8 KB'a kadar** Karakter belleğini adresler.
- PPU'ya grafiklerin 'nerede' ve 'nasıl' çizileceğini söyleyen meta veriler başka alanlarda bulunur:
  - Anakarta ayrı bir **2 KB SRAM** yerleştirilmiştir ve bu kez grafikle ilgili verilere ayrılmıştır. Nintendo bu alanı **Video RAM** (VRAM) olarak adlandırır ve **Nametables** adı verilen iki veri yapısını depolar.
  - PPU, **Object Attribute Memory**'yi (OAM) saklamak için **256 B** DRAM yerleştirir.
  - Son olarak PPU, renk paletlerini tanımlamak için **4 B** bellek de barındırıyor.

Yeni terminoloji konusunda endişelenmeyin, bu veri yapılarının anlamı aşağıdaki paragraflarda adım adım ele alınmaktadır.

### Kare Oluşturulması

Çağdaşlarında olduğu gibi, bu çip de CRT ekran davranışı için tasarlanmıştır. Böyle bir kare tamponu yoktur: PPU, CRT'nin ışınıyla adım adım işleyerek görüntüyü anında oluşturur.

PPU, **256x240 piksel** [@graphics-overscan] sabit boyutunda çerçeveler çizer. Ne yazık ki, dünya genelinde analog video standartlarındaki farklılıklar nedeniyle, görüntü, cihazın görüntülendiği bölgeye (NTSC veya PAL) bağlı olarak görünüm açısından farklılık gösterecektir. Özetle, NTSC televizyonlar aşırı taramaya uyum sağlamak için üst ve alt kenarları kırpacaktır (yalnızca ~224 tarama çizgisi görünür), bu nedenle bu kenarlar geliştiriciler tarafından oyunda öğelerin nereye yerleştirileceğine karar verirken 'tehlikeli bölgeler' olarak kabul edilir. Öte yandan, PAL televizyonlar kenarları kırpmaz, ancak daha uzun sinyali doldurmak için ekstra siyah çubuklar gösterir (PAL 288 tarama çizgisi kullanır).

Sahne arkasında, PPU'nun çıkardığı çerçeve iki farklı katmandan oluşur. Gösterim amacıyla, bunun nasıl çalıştığını göstermek için *Super Mario Bros.*'u kullanalım:

#### Kareler {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined}

![Birden fazla karonun birbirine sıkıştırıldığı iki desen tablosu.](ppu_mario/chr_map.png){.active title="Hepsi"}

![Tek bir karo.](ppu_mario/single.png){title="Tek"}

Karakter ROM'unda Bulunan Karolar (gösterim amacıyla varsayılan bir palet kullanılmaktadır).

:::

Başlangıç olarak PPU, sprite ve arka plan üretmek için temel bir bileşen olarak **tiles** kullanır.

NES karoları temel **8x8 piksel haritaları** olarak tanımlar, bunlar **Karakter belleğinde** (oyun kartuşunda bulunur) saklanır ve **Patern Tablosu** [@graphics-rust] adı verilen büyük bir veri yapısında düzenlenir. Her karo 16 B kaplar ve bir Desen tablosu 256 karo barındırır [@graphics-pattern]. PPU 8 KB'a kadar Karakter belleğini adreslediğinden, en fazla iki Desen tablosuna erişebilir.

Bir karonun içindeki piksellerin her biri, bir paletteki dört renkten birine referans veren 2 bitlik bir değer kullanılarak kodlanır. Programcılar sekiz adede kadar palet tanımlayabilirler (dördü arka plan için ve diğeri sprite'lar için). Her palette referans verilen renkler, bu konsolun üretebileceği tüm renkleri temsil eden 64 renkten [@graphics-palettes] oluşan bir 'ana palete' işaret eder. Paletler dört renkten oluşur, ancak bir renk `transparent` için ayrılmıştır.

Ekranda bir şey çizmeye başlamak için, oyunlar Karakter belleğindeki karolara referanslar içeren bir dizi tabloyu doldurur. Her tablo çerçevenin bir katmanından (sprite veya arka plan) sorumludur. Daha sonra, PPU bu tablolardan okur ve CRT tabancası tarafından ışınlanacak tarama çizgilerini oluşturur.

Şimdi her bir katmanın/tablonun nasıl çalıştığını ve işlevsellik açısından nasıl farklılık gösterdiğini açıklayacağım.

#### Arka Plan Katmanı {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Ayrılan arka plan haritası.](ppu_mario/nametable.png){.active title="Genel"}

![Seçilen alanın işaretlendiği tahsis edilmiş arka plan haritası.](ppu_mario/nametable_marked.png){title="Seçildi"}

Arka plan haritası, sorunsuz yatay kaydırma sağlayan dikey yansıtma ile ayarlanmıştır. Ancak, sadece bir yarısı kullanılabilir.

:::

Arka plan katmanı, statik kutucuklar içeren 512x480 piksellik bir haritadır [@graphics-nametables]. Görüntülenebilir çerçevenin çok daha küçük olduğunu hatırlayabilirsiniz, bu nedenle katmanın hangi kısmının görüntülenmek üzere seçileceğine oyun karar verir. Oyunlar ayrıca oyun sırasında görüntülenebilir alanı hareket ettirebilir; **kaydırma efekti** bu şekilde gerçekleştirilir.

Bellekten tasarruf etmek için, dört karodan oluşan gruplar **blok** adı verilen 16x16 piksel haritalar halinde birleştirilir ve bu haritalar içinde tüm karolar bir renk paletini paylaşır.

**Nametables** (VRAM'de saklanır) arka plan katmanında hangi karoların görüntüleneceğini belirtir. PPU, her biri katmanın bir çeyreğine karşılık gelen dört adet 1024 baytlık Nametable arar. Ancak, yalnızca 2 KB VRAM kullanılabilir! Sonuç olarak, kartuştan ek donanım olmadan yalnızca iki Nametable depolanabilir. Gerçi kalan ikisinin hala bir yerde ele alınması gerekir: çoğu oyun kalan ikisini ilk ikisinin olduğu yere yönlendirir (buna **mirroring** denir).

Bu mimari ilk başta kusurlu görünse de, basit **genişleyebilirlik** sağlarken maliyetleri düşük tutmak için tasarlanmıştır: oyunlar daha geniş bir arka plana ihtiyaç duyarsa, kartuşa ekstra VRAM dahil edilebilir.

Her İsim Tablosunun son baytları, her bloğa hangi renk paletinin atanacağını belirten 64 baytlık bir **Öznitelik tablosu** depolar [@graphics-attribute_table].

#### Sprite Katmanı {.tab}

![Render edilmiş sprite katmanı.](ppu_mario/sprite_layer.png) {.tab-float.pixel}

Sprite'lar ekran etrafında hareket edebilen kutucuklardır. Ayrıca birbirleriyle örtüşebilir veya arka planın arkasında görünebilirler. Görüntülenebilir grafiğe öncelik değerine göre karar verilecektir (geleneksel grafik tasarım yazılımındaki 'katmanlar' ile aynı kavramdır).

**Object Attribute Memory** (OAM) tablosu hangi karoların sprite olarak kullanılacağını belirtir [@graphics-oam]. Karo indeksine ek olarak, her giriş bir (x,y) konumu ve birden fazla nitelik (renk paleti, bir öncelik ve çevirme bayrakları) içerir. Bu tablo PPU yongasında bulunan 256 baytlık bir DRAM'de saklanır.

OAM tablosu CPU tarafından doldurulabilir. Ancak, bu pratikte oldukça yavaş olabilir (ve doğru zamanda yapılmazsa çerçeveyi bozma riski taşır), sonuç olarak PPU, tabloyu WRAM'den almak için programlanabilen (PPU'nun kayıtlarını değiştirerek) **Direct Memory Access** veya 'DMA' adı verilen küçük bir bileşen içerir. DMA ile, bir sonraki kare çizildiğinde tablonun yükleneceği garanti edilir, ancak aktarım sırasında CPU'nun durdurulacağını unutmayın!

PPU, tarama çizgisi başına sekiz sprite ve kare başına 64 sprite ile sınırlıdır. PPU'nun çoklama becerileri sayesinde tarama çizgisi sınırı aşılabilir. Başka bir deyişle, PPU taramalar arasında sprite'ları otomatik olarak değiştirecektir; ancak bunlar ekranda titriyor gibi görünecektir.

#### Arka plan ayrımı {.tab}

![Farklı kaydırma değerleri tanımlanmış iki bölümü vurgulayan işlenmiş arka plan katmanı. Mario hareket ettikçe sadece ikinci kısım kayar.](ppu_mario/split.png) {.tab-float.pixel}

Devam etmeden önce, size henüz söylemediğim bir şey var. Super Mario Bros oynarsanız, Mario hareket ettiğinde sahnenin aksamadan ilerlediğini fark edeceksiniz. Ancak, her iki bölüm de aynı arka plan katmanının parçası olmasına rağmen üst alanın (istatistiklerin olduğu yer) sabit kaldığını da gözlemleyeceksiniz **!** Peki, burada ne oluyor? Oyun, karenin ortasında kaydırma değerlerini değiştirerek yeryüzünü ve istatistikleri (arka planın sabit bir bölümünde yer alan) aynı anda gösteriyor. NES bu özelliği yerel olarak sağlamaz, ancak oyun PPU'nun durumunu gözlemleyerek zamanlamaları çıkarır (durum kaydı [@graphics-ppustatus] aracılığıyla gösterilir).

Bunu başarmak için oyunlar **Sprite 0 Hit** adı verilen bir teknik uygular. Super Mario Bros, PPU'ya madeni paranın arkasında sahte bir sprite oluşturmasını söyler, bu kare içinde çizilen ilk sprite olur. PPU bunu ışınladıktan sonra, durum kaydını ilk sprite'ın (diğer adıyla 'sprite 0') çizildiğini belirten bir bayrakla günceller. Bu arada oyun, sprite 0 durumunun işaretlenip işaretlenmediğini (diğer bir deyişle 'isabet') sürekli olarak kare ortasında kontrol eder, eğer bu olursa, oyun Mario'nun bulunduğu yere kaydırmak için arka plan tablosunun kaydırma özelliğini güncellemeye devam eder.

Genel olarak, 'Sprite 0 Hit' çok hassas bir prosedürdür, çünkü zamanlamaları karıştırmak kolaydır (sprite 0'ın bayrağı yoklandıktan sonra temizlenmez, bu da 'yinelenen' pozitiflere yol açar \[@graphics-chibiakumas). Ayrıca, bu rutin süresiz olarak tekrarlandığından, yürütülmesi oldukça pahalı (CPU döngüleri açısından) olabilir. İşin iyi tarafı, daha sonraki haritacılar bu işlevi, örneğin Super Mario Bros 3'ün görsel yeteneklerini önemli ölçüde geliştiren rastgele bir tarama çizgisine [@graphics-nesdoug\] (çok daha verimli bir teknik) her vurulduğunda tetiklenen otomatik kesintiler kullanarak devraldılar.

#### Sonuç {.tab}

![Tada!](ppu_mario/result.png) {.tab-float.pixel}

Kare tamamlandığında, bir sonrakine geçme zamanı gelmiş demektir!

Ancak CPU, PPU tarafından kullanılmakta olan herhangi bir tabloyu değiştiremez, aksi takdirde ekranda artefaktlar görünebilir. Böylece, tüm tarama satırları tamamlandığında, PPU CPU'da **Vertical Blank** (V-Blank) kesmesini tetikler [@graphics-vblank]. Bu, oyuna o anda görüntülenen resmi yırtmadan tabloları güncellemeye başlayabileceğini bildirir. O anda CRT'nin ışını ekranın görünür alanının altına, aşırı taramaya (veya alt kenarlık alanına) işaret eder.

V-Blank penceresinin [@graphics-outside_vblank] dışında yalnızca bir avuç PPU kaydı güncellenebilir, bu da arka plan katmanını kare ortasında kaydırma yeteneğini açıklar.

### Sırlar ve sınırlamalar {.tabs-close}

Tam kareyi saklamak için bellek tahsis edilmiş bir kare arabelleği sisteminin tercih edilebileceğini düşünüyorsanız: RAM maliyetleri çok yüksekti ve konsolun amacı uygun fiyatlı olmaktı. Şimdi size bu tasarımın neden çok verimli ve esnek olduğunu göstereyim.

#### Multi-Scrolling {.tabs.active}

![Super Mario Bros. 2. Dikey kaydırma için isim tablosu kurulumu (yatay yansıtma).](secrets/multiscrolling_mirror.png) {.tab-float.pixel}

![Super Mario Bros. 3. Mario koşabilir ve uçabilir, bu yüzden PPU'nun çapraz kaydırma yapması gerekir. Sağ kenarın yanlış renk paletini gösterdiğine dikkat edin! Sol kenara bir maske uygulanmıştır.](secrets/multiscrolling.png) {.tab-float.pixel}

Bazı oyunlar ana karakterin dikey olarak hareket etmesini gerektirir - bu nedenle isim tablosu **yatay yansıtma** ile kurulacaktır. Diğer oyunlar karakterlerinin sola ve sağa hareket etmesine ihtiyaç duyar ve bu nedenle bunun yerine **dikey yansıtma** kullanır.

Her iki yansıtma türü de PPU'nun kullanıcı fark etmeden arka plan döşemelerini güncellemesine izin verecektir: yeni döşemeler uzaktan işlenirken kaydırmak için bolca alan vardır.

Peki ya karakter çapraz hareket etmek isterse? PPU herhangi bir yönde kaydırma yapabilir, ancak ekstra VRAM olmadan kenarlar aynı renk paletini paylaşmaya zorlanır (karoların bloklar halinde gruplandığını unutmayın).

Bu yüzden *Super Mario Bros. 3* gibi bazı oyunlar Mario hareket ederken ekranın sağ kenarında garip grafikler gösterir (oyun dikey kaydırma için ayarlanmıştır) [@graphics-seam]. Kartuş başına donanım maliyetini en aza indirmeleri gerekmiş olabilir (çünkü bu oyunda zaten güçlü bir eşleyici yüklü).

İlginç bir *düzeltme* olarak: PPU, geliştiricilerin karoların üzerine dikey bir maske uygulamasına izin vererek hatalı alanın bir kısmını etkili bir şekilde gizledi.

#### Karo Değiştirme {.tab}

![İlk tarama satırları sırasında mevcut karolar kullanılarak işlenmiş olsaydı varsayımsal olurdu.](secrets/multiplexing_1.png){.tabs-nested .tab-float .pixel .active title="Tarama 1"}

![Daha sonraki tarama satırlarında mevcut olan karolar kullanılarak işlenmiş olsaydı varsayımsal olurdu.](secrets/multiplexing_2.png){.tab-nested .pixel title="Tarama 2"}

![Kullanıcıya gösterilen gerçek çerçeve.](secrets/multiplexing_complete.png){.tabs-nested-last .pixel title="Görüntüleniyor"}

Super Mario Bros. 3'ün bir başka özelliği de görüntüleyebildiği grafik miktarıdır.

Bu oyun, kesinlikle izin verilenden daha fazla arka plan taşı görüntüler. Peki bunu nasıl yapıyor? Ekran oluşturulurken farklı zamanlarda iki ekran görüntüsü alırsak, son karenin aslında *iki* farklı kareden oluştuğunu görebiliriz.

Bu, MMC3 eşleyicisinin bir başka sihirbazlığıdır ve sadece Program ROM'unda fazladan alana erişmek için kullanılmaz, aynı zamanda iki farklı Karakter yongasını bağlayarak Karakter ROM alanını da genişletir. PPU'nun ekranın hangi bölümünü talep ettiğini kontrol ederek, eşleyici bir çipe veya diğerine yönlendirecek - böylece ekranda başlangıçta desteklenenden daha fazla benzersiz kutucuğa izin verecektir [@graphics-n3s].

#### Meraklı davranışlar {.tab}

Araştırmam boyunca, PPU'nun olağandışı davranışlarını açıklayan birçok ilginç makaleye rastladım, bu yüzden bazılarından burada bahsetmeyi düşündüm:

- Daha sonra yayın için NTSC/PAL sinyallerine kodlanan RGB renkleri üreten [Master System'in VDP](master-system#graphics)'sinin aksine, **NES'in PPU'su hepsini bir kerede yapar** [@graphics-palettes]. Bu nedenle, PPU ana paletinin renkleri ile standart RGB renk alanı (mevcut teknoloji tarafından yaygın olarak benimsenmiştir) arasında bire bir bağlantı yoktur. Bu, yorumlama için bir miktar alan bırakır ve sonuç olarak, çeşitli emülatörler farklı bir palet gösterecektir.
  - RGB paletleri arasındaki tutarsızlıklar en çok Tim Worthington'ın NES'e RGB sinyal çıkışı ekleyen DIY kitinde belirgindir, çünkü önceden tanımlanmış üç palet arasında seçim yapan bir anahtar da uygulamaktadır [@graphics-nesrgb].
- Ana palet, NTSC TV sinyalini bozabilecek bir **'lanetli' renk** (`$0D`) içerir [@graphics-cursed_colour]. Peki, ne olur, TV bu rengi görüntülemek için sinyali boşluk sinyali ile karıştırır, bu nedenle titreme meydana gelebilir.
- PPU, Nesne Öznitelik Belleğini (OAM) depolamak için DRAM'e güvenir. Şimdi, DRAM'in veri kaybını önlemek için sürekli olarak yenilenmesi gerekir (SRAM'in aksine) ve PPU, kareyi oluşturmadığında DRAM'i yenilemeyecektir [@graphics-oam]. Bu durum dikey karartma sırasında ortaya çıkar. Bu nedenle, V-boşluk sırasında meydana gelen yenilemesiz dönem tablonun bir kısmını bozacağından, OAM'nin dikey boşluk dışında güncellenmemesi tavsiye edilir.
  - PAL sistemleri için PPU varyantı bundan etkilenmez, çünkü V-Blank sırasında yenilenir (PAL sistemlerinde daha uzun sürer).

## Ses {.tabs-close}

**Ses İşleme Birimi** (APU) adı verilen özel bir bileşen bu hizmeti sağlar [@audio-apu]. Ricoh, muhtemelen hem CPU hem de APU'nun lisanssız klonlanmasını önlemek için bunu CPU çipinin içine yerleştirdi.

### Fonksiyonellik

Bu ses yongası bir **Programlanabilir Ses Üreteci** (PSG), yani sadece önceden tanımlanmış dalga formları üretebiliyor. CPU tarafından kontrol ediliyor.

APU, ses verilerini her biri belirli bir dalga formu için ayrılmış beş ses kanalı üzerinden sıralar. Müzik verileri Program ROM'unda bulunur. Her dalga formu, belirli bir nota, ses veya ses seviyesi üretmek için değiştirilebilen farklı özellikler içerir. Çoğu oyunda bir nabız kanalı melodi için, diğeri ise eşlik için kullanılır.

Dahası, Famicom modeli karışık ses sinyalini kartuşa gönderen kartuş pinleri içerir, böylece ikincisi ekstra kanallarla karıştırabilir (ekstra çipler gerektirir) [@general-cartridge].

Şimdi APU [@audio-review] tarafından sentezlenen dalga formlarının türünü tartışalım:

#### Nabız {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Dalga 1 kanalının osiloskop görünümü.](pulse_single_1){.active video="true" title="Nabız 1"}

![Dalga 2 kanalının osiloskop görünümü.](pulse_single_2){video="true" title="Nabız 2"}

![Tüm ses kanallarının osiloskop görünümü.](pulse_full){video="true" title="Tamamlandı"}

Mother (1989).

:::

Nabız dalgaları, çoğunlukla **melodi veya ses efektleri** için kullanılan çok belirgin bir *bip* sesine sahiptir.

APU darbe dalgaları için iki kanal ayırır. Her biri, darbe genişliklerini değiştirerek üretilen üç farklı sesten birini kullanabilir.

Çoğu oyunda bir nabız kanalı melodi için, diğeri ise eşlik için kullanılır.

Oyunun bir ses efekti çalması gerektiğinde, eşlik kanalı efekti çalmak için değiştirilir ve ardından eşlik etmeye geri döner. Bu, oyun sırasında melodinin kesintiye uğramasını önler.

#### Üçgen {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Üçgen kanalın osiloskop görünümü.](triangle_single){.active video="true" title="Üçgen"}

![Tüm ses kanallarının osiloskop görünümü.](triangle_full){video="true" title="Tamamlandı"}

Mother (1989).

:::

Bu dalga biçimi melodi için bir bas çizgisi görevi görür. Perdesini dramatik bir şekilde değiştirmek de perküsyon üretebilir.

APU'nun bu tür dalgalar için ayrılmış bir kanalı vardır.

Bu kanalın ses seviyesi kontrol edilemez, çünkü muhtemelen ses seviyesi kontrolü üçgeni oluşturmak için kullanılır.

#### Ses {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Gürültü kanalının osiloskop görünümü.](noise_single){.active video="true" title="Ses"}

![Tüm ses kanallarının osiloskop görünümü.](noise_full){video="true" title="Tamamlandı"}

Mother (1989).

:::

Gürültü temelde beyaz statik gibi ses çıkaran bir dizi rastgele dalga biçimidir. Bunun için bir kanal tahsis edilmiştir.

Oyunlar gürültü kanalını perküsyon veya *ambient* efektleri için kullanır.

Bu kanalda sadece 32 *ön ayar* mevcuttur. Bu ön ayarların yarısı (16) **temiz statik**, diğer yarısı ise **robotik statik** üretir.

#### Örnek {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Örnek kanalın osiloskop görünümü.](sample_single){.active video="true" title="Örnek"}

![Tüm ses kanallarının osiloskop görünümü.](sample_full){video="true" title="Tamamlandı"}

Mother (1989).

:::

Örnekler, tekrar çalınabilen kaydedilmiş müzik parçalarıdır. Gördüğünüz gibi, örnekler tek bir dalga formuyla sınırlı değildir, ancak çok daha fazla yer kaplarlar.

APU'nun örneklere ayrılmış bir kanalı vardır. APU ile örnekler **7 bit çözünürlük** (`0` ile `127` arasındaki değerlerle kodlanır) ve **~15,74 KHz örnekleme hızı** ile sınırlıdır [@audio-2a03ref]. Bu kanalı programlamak için, oyunlar 7 bitlik değerleri yayınlayabilir (bu da çok fazla döngü ve depolama alanı çalar) veya yalnızca bir sonraki örnek ile bir önceki arasındaki değişimi kodlamak için **delta modülasyonu** kullanabilir.

APU'daki delta modülasyonu uygulaması yalnızca 1 bitlik değerler alır, bu da oyunların, sayaç her devreye girdiğinde örneğin yalnızca `1` arttığını veya azaldığını söyleyebileceği anlamına gelir. Dolayısıyla, aslına uygunluk pahasına, delta modülasyonu oyunları APU'ya sürekli değerler aktarmak zorunda kalmaktan kurtarabilir.

Bu kanalı programlamak daha fazla alan ve CPU döngüsü gerektirdiğinden, oyunlar normalde tekrar tekrar çalınabilen küçük parçaları (davul örnekleri gibi) depolar. Ancak NES'in ömrü boyunca birçok stüdyo bu kanalı akıllıca kullanmayı başardı.

### Sırlar ve sınırlamalar {.tabs-close}

APU bir plak, kaset veya CD'nin kalitesiyle karşılaştırılamazken, programcılar NES'in modüler mimarisi sayesinde kapasitesini genişletmenin yollarını buldular.

#### Ekstra Kanallar {.tabs.active}

![Castlevania III'ün osiloskop görüntüsü (ABD/Avrupa, 1989).](castlevania_usa){.tabs-nested .tab-float .active video="true" title="Amerikan"}

![Akumajō Densetsu'nun (Castlevania III'ün Japonca versiyonu, 1989) osiloskop görünümü.](castlevania_jap){.tabs-nested-last video="true" title="Japon"}

Famicom'un ses genişletme için özel kartuş pinleri sağladığını hatırlıyor musunuz? *Castlevania 3* gibi oyunlar bu avantajdan yararlandı ve **Konami VRC6** adında ekstra bir çip paketledi, bu da karışıma **iki ekstra darbe dalgası ve bir testere dişi dalgası** ekledi.

Oyunun Japon versiyonu ile Amerikan versiyonları arasındaki farkı gösteren bu videoya bir göz atın (ikincisi, ses genişletme özellikleri sağlamayan NES'te çalışır).

#### Tremolo {.tab}

![Final Fantasy III'ün (1990) osiloskop görünümü.](tremolo_full){.tab-float video="true"}

Kartuş maliyetlerini artırmak yerine, bazı oyunlar daha fazla kanal eklemek için teknolojiden ziyade yaratıcılığa öncelik verdi.

Bu örnekte Final Fantasy III, ekstra kanalları 'simüle etmek' için tremolo efektlerini kullanma fikrini ortaya atmıştır.

## Oyunlar {.tabs-close}

NES oyunları temel olarak 6502 assembly dilinde yazılır ve **Program ROM**'unda bulunurken, oyunun grafikleri (karolar) **Karakter belleğinde** saklanır.

Ayrıca, oyunlar Nintendo'nun onayı altında perakende mağazalarında satıldı (veya kiralandı).

### Alternatif ortam

Sadece Japonya'da piyasaya sürülmüş olsa da, bunun kısa ömürlü ama tıpkı haritacılar gibi bu konsola daha fazla yetenek kazandıran tuhaf bir eklentiyi tanıtmak için iyi bir fırsat olacağını düşündüm. Bu çevre birimi **Famicom Disk System** (FDS) olarak adlandırıldı ve 1986'da (Famicom'dan ~3 yıl sonra) piyasaya sürüldü. Harici bir disket okuyucu şeklindeydi ve 'RAM adaptörü' adı verilen garip şekilli bir kartuşla birlikte geliyordu.

::: {.subfigures .side-by-side}

![Disketlerin yerleştirildiği sürücü (fotoğrafta koruma için yerleştirilmiş bir karton disket gösterilmektedir). Altı adet C pille (her biri 1,5 V) ya da 3,6 W AC adaptörle çalışır.](fds/drive.png) {.toleft.no-borders}

![RAM adaptörü, Famicom'un kartuş yuvasına takılır ve bir kablo ile sürücüye bağlanır.](fds/ram.png) {.toright.no-borders}

Famicom Disk Sistemini (FDS) oluşturan iki bileşen.

:::

Famicom Disk Sistemi, Famicom'a aşağıdaki hizmetleri ekledi:

- Oyunlar için **Famicom Disk** [@games-fds] adlı yeni bir dağıtım aracı. Mitsumi'nin 'Quick Disk'ini temel alan bu disk, her bir taraf için **~64 KB veri** sağlar ve yeniden yazılabilir.
- [wavetable synthesis](game-boy#tab-7-3-wave) [@games-fds_audio] kullanan bir **ekstra ses kanalı**.

![FDS ekipmanı Famicom'a monte edildi.](fds/mounted.png) {.open-float.no-borders}

Disket tek bir ortam olduğundan (çok çipli kartuşların aksine), tüm oyun verilerinin içine sıkıştırılması gerekir, ancak özel bir **dosya sistemi** kullanılarak düzenli tutulur.

Bununla birlikte, Famicom/NES'in çalışması için kesinlikle ayrılmış Program ve Karakter belleği gerekir, bu yüzden bunu çözmek 'RAM adaptörünün' işidir. Bu bileşen, disketten okunan oyun verilerini tamponlamak için **32 KB Program RAM** ve **8 KB Karakter RAM** barındırır ve bunu yaparken konsolun kartuş tabanlı bir oyunmuş gibi okumasını sağlar.

`r close_float_group(with_markdown = TRUE)`

Sürücüyü çalıştırmak için RAM adaptörü bir **BIOS** [@games-fds_bios] depolamak için ek bir 8 KB ROM yerleştirir. Bu program aşağıdaki görevleri yerine getirir:

- Bir sıçrama animasyonu yükler.
- Oyunu disketten başlatır. BIOS, perde arkasında disketin içeriğini ilgili bellek yongasına kopyalayan rutinler içerir, böylece konsol bunu okuyabilir.
- Oyunların kullanması için disklerin dosya sisteminde gezinme gibi I/O API'leri sağlar.

![FDS için iki perakende oyun örneği. Diskin mavi 'aroması' da toz geçirmezdi.](fds/floppies.jpg) {.toleft}

![FDS açılış animasyonu, kullanıcının... uhm... bir oyun eklemesini bekliyor.](fds_bios){.toright video="true"}

O dönemde Nintendo, perakende mağazalarında bazı 'kiosk'lar kurdu, böylece kullanıcılar disketlerini getirip indirimli bir fiyata yeni bir oyunla üzerine yazabiliyorlardı.

Ne yazık ki, birkaç yıllık kullanım ömrünün ardından Famicom Disk Sistemi kullanımdan kaldırıldı ve sonraki oyunlar kartuş ortamına geri döndü. İşin iyi tarafı, FDS'nin işlevlerine kıyasla benzer (veya daha iyi) yeteneklere sahip yeni haritalar mevcuttu.

## Korsanla mücadele ve bölge kilidi

Nintendo, **Checking Integrated Circuit** (CIC) [@anti_piracy-cic] adı verilen tescilli bir **lockout** çipi sayesinde izinsiz yayınları engelleyebilmiştir. Konsolda bulunur ve sıfırlama sinyaline bağlıdır (ve bu nedenle kolayca çıkarılamaz).

Bu çip, oyun kartuşunda başka bir kilitleme çipinin varlığını kontrol eden dahili bir program olan **10NES**'i çalıştırır. Bu kontrol başarısız olursa konsol sonsuz sıfırlamaya gönderilir.

Her iki kilitleme yongası da konsolun çalışma süresi boyunca sürekli iletişim halindedir. Bu sistem, çipi boşta bırakan konsolun kilitleme pimlerinden biri kesilerek etkisiz hale getirilebilir. Alternatif olarak, -5V'luk bir sinyal göndermek onu dondurabilir.

CIC, 1983'teki video oyunu çöküşünün neden olduğu korkunun bir sonucu olarak var olmuştur. Nintendo'nun o zamanki başkanı Hiroshi Yamauchi, kaliteli oyunları zorunlu kılmak için her birini onaylamaktan sorumlu olacaklarına karar verdi [@anti_piracy-vindicator].

Konsolun Japon modeli olan Famicom'un 1983'teki çöküşten önce piyasaya sürüldüğünü fark edeceksiniz. Bu nedenle ne oyun kartuşları ne de konsol CIC devresi [@anti_piracy-nocic] içermez, bunun yerine pinler isteğe bağlı ses genişletme için kullanılır.

## Hepsi bu kadar
