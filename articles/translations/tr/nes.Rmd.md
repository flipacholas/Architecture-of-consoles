---
long_title: "Nintendo Entertainment System'in (NES) Mimarisi"
short_title: "Nintendo Entertainment System'in (NES) Mimarisi"
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
      title: "Japonya"
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

## {.supporting-imagery}

## Modeller ve varyantlar

![Tipik bir Betamax kaydedicisi. Bu ve benzeri cihazlar NES'in uluslararası tasarımını etkilemiştir. Ağustos 2024'te ziyaret ettiğim The Centre for Computing History'de (Cambridge, Birleşik Krallık) özellikle bunu gördüm.](betacord.webp)

Nintendo, dünya çapında aynı konsolun çok sayıda farklı varyantını gönderdi [@general-variants] ve hepsi aynı mimariyi paylaşsa da, çoğu önemli ölçüde farklı görünüyor ve bazıları yerleşik aksesuarlar içerebiliyor. Bu nedenle, bu makaleyi basit tutmak için en popüler iki revizyona odaklanacağım:

- **Family Computer** (*Famicom* olarak bilinir) ilk enkarnasyondu, ancak sadece Japonya'da piyasaya sürüldü. Bu oyuncak görünümlü tasarımda iki adet çıkarılamayan kontrolör (ikinci kontrolörün iç mikrofonu içerir), ışık tabancası (*Zapper* adlı) için ön soket, RF video çıkışı (NTSC-J sinyali kullanarak) ve ses yeteneklerini genişletmek için kartuş yuvasında ekstra pinler bulunur.
- **Nintendo Entertainment System** (*NES* olarak bilinir), Kuzey Amerika, Avrupa ve Okyanusya'da yaşayan batılı kitleler için yeniden tasarlanmış bir versiyondu; normal bir VHS/Betamax oynatıcı ile aynı görünüm ve mekanizmaya sahiptir. Teknik açıdan, kontrolörler artık sökülebilir (ve mikrofonsuz) ve video çıkışı ekstra NTSC/PAL kompozit RCA konektörleriyle geliştirildi, ancak ses genişletmesi korsan karşıtı bir alt sistemle değiştirildi. Üstüne üstlük, kasanın alt kısmında kullanılmayan bir 'genişleme portu' ve bu portla iletişim kuran ekstra kartuş pinleri [@general-cartridge] bulunmaktadır.

Yazar 'NES' adıyla büyüdüğü için, genel olarak konsola atıfta bulunmak için bu terimi kullanacağım, ancak yalnızca Japon varyantında bulunan benzersiz özelliklere atıfta bulunurken 'Famicom' adına geçeceğim.

## İşlemci (CPU)

NES'in CPU'su popüler 8-bit **MOS Technology 6502** tabanlı bir **Ricoh 2A03** [@cpu-cpu] olup **1,79 MHz** (veya PAL sistemlerinde 1,66 MHz) hızında çalışır.

### Biraz bağlam

70'lerin sonu ve 80'lerin başında CPU pazarı oldukça çeşitliydi.

::: {.subfigures .side-by-side}

![Bir Commodore PET, 6502 CPU taşıyor.](pet.webp) {.toleft}

![Bir Tandy TRS-80, 6502 CPU taşıyor.](tandy.webp) {.toright}

70'lerin sonundaki bilgisayarların panoraması da The Centre for Computing History (Cambridge, Birleşik Krallık) tarafından sağlanmıştır.

:::

Eğer bir şirket uygun fiyatlı bir mikrobilgisayar üretmek istiyorsa, aşağıdaki seçenekler mevcuttu:

- **Intel 8080**: ilk 'kişisel' bilgisayar olan *Altair*'de yer alan popüler bir CPU. 8 bitlik bir veri yolu ve 16 bitlik bir adres yolu vardır.
- **Zilog Z80**: 8080'in daha fazla talimat, kayıt ve dahili bileşenle geliştirilmiş 'resmi olmayan' bir versiyonu. Daha ucuz bir fiyata satıldı ve hala 8080 programlarını çalıştırabiliyordu [@cpu-re_1977, s. 86]. Amstrad ve Sinclair (diğerlerinin yanı sıra) bu CPU'yu seçti.
- **Motorola 6800**: Motorola tarafından tasarlanan bir başka 8-bit CPU, tamamen farklı bir komut seti içerir. Birçok kendin yap bilgisayar kiti, sentezleyici ve hepsi bir arada bilgisayar 6800'ü içeriyordu.

Bu seçenekler yetmezmiş gibi, **MOS** adında başka bir şirket piyasaya çıktı ve 6800'ün yeniden tasarlanmış bir versiyonunu sundu: **6502**. Diğerleriyle uyumsuz olsa da, yeni çipin üretimi çok *çok* daha ucuzdu \[@cpu-summary_costs\] \[@cpu-re_1981, s. 76\] ve en ünlü bilgisayar üreticilerinin (Commodore, Apple, Atari, Acorn ve diğerleri) makinelerine güç sağlamak için 6502'yi seçmeleri sadece bir zaman meselesiydi.

Japonya'da Nintendo'nun ucuz ama geliştirmeye uygun bir şeye ihtiyacı vardı, bu yüzden 6502'yi seçtiler. CPU tedarikçisi **Ricoh**, 6502 uyumlu bir CPU'yu başarıyla üretti.

#### Ricoh'nun lisanslama muamması

Ricoh'un 6502'yi *nasıl* klonlamayı başardığı bugün bile net değil. MOS'un çip tasarımını Ricoh'a lisanslamış olması beklenebilir, ancak bununla ilgili birçok çelişki var:

- Hem Ricoh'un hem de MOS'un varyantları aynı düzene sahiptir, ancak Ricoh'unki kesintili veri yolları içerir (belirli işlevleri devre dışı bırakır) [@cpu-differences]. Daha sonra daha fazla ayrıntıya gireceğim.
- MOS'un 6502'yi Ricoh'a lisansladığını açıkça belirten bir belge henüz bulunamamıştır.
- Nikkei Trendy tarafından 2008 yılında yayınlanan bir makalede Ricoh'un yetkili bir çip üreticisi olan Rockwell'den lisans aldığı belirtilmektedir [@cpu-trendi]. Ancak, MOS'un onayı bir yana, ikinci bir kaynağın üçüncü bir tarafa IP sağlayıp sağlayamayacağı tartışmalıdır.
- Nintendo'nun fikri mülkiyet haklarını aşması ilk kez değil, çünkü Japonya'da *Ikegami Tsushinki v. Nintendo* davasında, Nintendo'nun orijinal Donkey Kong koduna sahip olmadığına karar verildi [@cpu-dk].

#### Hurdaya çıkarılan fonksiyonlar

Ricoh 2A03, orijinal olarak 6502'de bulunan **Binary-Coded Decimal** (BCD) modunu içermemektedir [@cpu-visualbcd]. BCD, bir sayının her ondalık basamağını ayrı bir 4 bitlik ikili olarak kodlar. 6502, 8 bit 'words' kullanır - yani her word iki ondalık basamak depolar.

Meraklısı için bir örnek olarak, ondalık sayı `42` olarak gösterilir:

- `0010 1010` binary'de.
- `0100 0010` BCD'de.

Bu konuda konuşmaya devam edebiliriz, ancak ana hatlarını vermek gerekirse: BCD, her ondalık basamağın ayrı ayrı ele alınmasını gerektiren uygulamalar için kullanışlıdır (örneğin, dijital bir saat). Bununla birlikte, her 8 bitlik kelime yalnızca `99` ondalık sayısına kadar kodlayabildiğinden daha fazla depolama gerektirir - oysa geleneksel ikili `255`'e kadar kodlayabilir.

6502, 8 bit 'word' kullanır - yani her kelime iki ondalık basamak depolar. Bu muhtemelen MOS'a telif ücreti ödememek için yapıldı, çünkü BCD'nin patenti onlar tarafından alınmıştı (ve Amerika Birleşik Devletleri'nde entegre devre düzenlerinin telif hakkını sağlayan mevzuat 1984 yılına kadar yürürlüğe girmemişti [@cpu-protection_act]).

### Bellek

Hem Ricoh 2A03 hem de MOS 6502, **8-bit veri yolu** ve **16-bit adres yoluna** sahiptir, bu da **64 KB'a kadar belleğe** erişmelerini sağlar. Peki, Nintendo bu bellek alanını nasıl doldurdu?

Anakartın bir tarafında **2 KB Statik RAM** (SRAM) [@cpu-sample_ram] sağlayan bir yonga bulunmaktadır. Nintendo bu alanı 'Work RAM'i' (WRAM) olarak adlandırır ve depolamak için kullanılabilir:

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

Örneğin, Nintendo'nun 'Super Mario Bros' oyunu *NES-NROM-256* adını verdikleri bir düzen kullanmıştır ve 32 KB program ROM'u ve grafikler için 8 KB 'Karakter ROM'undan oluşmaktadır ('Grafikler' bölümünde daha fazlasını göreceğiz) [@cpu-nrom]. *NES-NROM-256* ayrıca 3 KB'a kadar ekstra WRAM barındıracak şekilde hazırlanmıştır, ancak oyun bunu kullanmamaktadır.

#### Mevcut yeteneklerin ötesine geçmek

16-bit adres veri yollarının en büyük sınırlamalarından biri (3. ve 4. nesil konsolları etkileyen) kompakt adres alanlarıdır. Günümüzde 32-bit bilgisayarlar 4 GB'a kadar bellek adresleyebilmektedir (ve 64-bit makineler 16 exabyte'a kadar cömertçe kullanmaktadır), bu nedenle bu artık bir sorun değildir, ancak o zamanlar NES'in yalnızca 64 KB adres alanı vardı ve bunun büyük bir kısmı bellek eşlemeli donanım tarafından tüketiliyordu ([rakiplerin kaçındığı bir şey](master-system#accessing-the-rest-of-the-components)).

Peki bu, oyun stüdyolarının yalnızca 49,97 KB sınırını aşmayan oyunlar geliştirebileceği anlamına mı geliyordu? Kesinlikle değil! Tarih bize bir şey öğrettiyse, o da zorlu bir soruna her zaman akıllıca bir çözüm bulunabileceğidir; ve bu sorun bir **Mapper** ile ele alındı.

![Bir eşleyicinin CPU'nun adresleme yeteneklerini nasıl genişlettiğinin basitleştirilmiş gösterimi. CPU, bir mapper'ın dahil edilmesiyle, büyük bir Program ROM'unun ekstra bankalarına (adres grupları) erişebilir. Her ne kadar oyun/program gerektiğinde bankalar arasında manuel geçiş yapma gibi yeni bir göreve sahip olsa da.](mapper/mapper.png){.tabs-nested .active title="Mapper ile"}

![Aynı kurulum ancak eşleyici yüklü değil. Daha basit ve ucuz olsa da, CPU yalnızca sınırlı sayıda bankaya erişebilir.](mapper/no_mapper.png){.tabs-nested-last title="Mapper olmadan"}

Eşleyici, kartuşta bulunan ve bellek yongaları ile konsolun adres hatları arasında yer alan ekstra bir yongadır. Ana görevi, geliştiricilerin daha fazla yonga sığdırabilmesi için adres alanını genişletmektir. Bu **bank switching** ile yapılır: Bellek adresleri bankalar halinde gruplandırılır ve eşleyici bankalar arasında geçiş yapmak için anahtarlar (bellek adresleri aracılığıyla kontrol edilir) sağlar. Şimdi, CPU hala aynı miktarda bellek görüyor, bu yüzden onu çalıştırmaktan sorumlu bir eşleyici ile programlanmış olan oyun. Maliyet etkinliği nedeniyle, 80'lerden 90'ların başlarına kadar teknolojide haritacılar ön plandaydı.

![Super Mario Bros 2'ün PCB'si [@photography-tsrom]. Super Mario Bros 3'de bu tasarıma sahip ama 256 KB Program ROM ile birlikte gelir.](tsrom.png){.tabs-nested .active title="Orjinal"}

![Önemli kısımları etiketlenmiş aynı resim. İlk başta, ekstra WRAM'in kayıtları saklamak için olduğunu düşündüm, ancak daha sonra bu oyunda kayıt olmadığını fark ettim (ve bir pil de yok). Gerçekte, bu RAM yongası sıkıştırılmış bir seviyeyi depolamak için kullanılır.](tsrom_marked.png){.tabs-nested-last title="İşaretli"}

NES'e geri dönersek, 'Super Mario Bros 2' ve 'Super Mario Bros 3' gibi oyunlar kartuşlarında 'MMC3' eşleyici (Nintendo tarafından yapılmıştır) ile birlikte gönderilmiştir. Karşılaştırma için MMC3, Program ROM'u için 512 KB'a kadar, Karakter belleği için 256 KB'a kadar ve ekstra WRAM için 8 KB'a kadar alan sağlamıştır [@cpu-mmc3]. Şimdi 'Super Mario Bros 3'ün ilk taksitle karşılaştırıldığında neden kalite açısından önemli ölçüde farklı olduğunu görebilirsiniz.

Sonuç olarak, bu konsol dahili özelliklerini incelerken sınırlı görünse de, Nintendo teknoloji geliştikçe uyum sağlayabileceğinden emin oldu. Diğer taraftan, bu teknik konsolun maliyetlerini düşük tutmaya yardımcı olurken, yükün bir kısmını oyun kartuşuna kaydırdı. Dolayısıyla, oyun kalitesi ve kartuş maliyetleri, oyun stüdyolarının dengelemek zorunda olduğu iki endişeydi.

## Grafikler

Grafikler **Picture Processing Unit** (PPU) adı verilen özel bir çip tarafından üretilir. Bu, NES'e *kimlik* kazandıran çiplerden biridir. Başka bir şekilde ifade etmek gerekirse, herkes nalburdan 6502 CPU alabilir, o halde NES'in örneğin bir Apple 2 veya Commodore 64'ten ne farkı var? NES'i diğer makinelerden ayıran şey CPU'yu çevreleyen çiplerdir: PPU ve APU. Bunlar sırasıyla NES'in benzersiz grafik ve ses özelliklerini oluşturur.

Bununla birlikte PPU, **sprites** ve **backgrounds** adı verilen 2D grafikleri işleyerek sonucu video sinyaline aktarır.

### İçeriğin düzenlenmesi

![PPU'nun bellek mimarisi](ppu_content.png)

Ekranda bir şey oluşturmak için PPU'nun *hangi* grafikleri çizeceğini, *ekranda nereye* yerleştireceğini ve *nasıl* çizeceğini (yani hangi paleti kullanacağını) bilmesi gerekir.

Bu soruları yanıtlamak için PPU, aşağıdaki veri türlerini arayan farklı bir bellek haritası ile önceden programlanmış olarak gelir:

- Grafik verileri, **Character Memory** adı verilen ve 2B çizimleri (**tiles** adı verilen) **Pattern table** adlı bir veri yapısında düzenlenmiş olarak saklayan özel bir çip içeren oyun kartuşundan alınır. Character Memory, oyunun değişmez bir grafik setiyle birlikte gelmesine veya CPU'nun müdahale etmesi gerekmesine bağlı olarak sırasıyla 'Salt Okunur Bellek' (ROM) veya 'Rastgele Erişimli Bellek' (RAM) şeklinde gerçekleşir.
  - PPU, her biri 4 KB'lık iki grup halinde düzenlenmiş **8 KB'a kadar** Character Memory adresler.
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

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined max_subfigures=1}

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

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

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

PPU, **tarama çizgisi başına sekiz sprite** ve **kare başına 64 sprite** ile sınırlıdır. 'OAM order rotation' adı verilen ve oyunun OAM'deki girişlerin sırasını manuel olarak değiştirdiği bir teknik sayesinde tarama çizgisi sınırı aşılabilir. Bu, PPU'nun her karede farklı bir sprite seti oluşturmasını sağlar ve CRT ışınının hızı, kullanıcıyı izin verilenden daha fazla sprite görmesi için kandırır. Ancak ekranda titriyor gibi de görüneceklerdir.

#### Arka plan ayrımı {.tab}

![Farklı kaydırma değerleri tanımlanmış iki bölümü vurgulayan işlenmiş arka plan katmanı. Mario hareket ettikçe sadece ikinci kısım kayar.](ppu_mario/split.png) {.tab-float.pixel}

Devam etmeden önce, size henüz söylemediğim bir şey var. Super Mario Bros oynarsanız, Mario hareket ettiğinde sahnenin aksamadan ilerlediğini fark edeceksiniz. Ancak, **her iki bölüm de aynı arka plan katmanının parçası olmasına rağmen** üst alanın (istatistiklerin olduğu yer) sabit kaldığını da gözlemleyeceksiniz! Peki, burada ne oluyor? Oyun, karenin ortasında kaydırma değerlerini değiştirerek yeryüzünü ve istatistikleri (arka planın sabit bir bölümünde yer alan) aynı anda gösteriyor. NES bu özelliği yerel olarak sağlamaz, ancak oyun PPU'nun durumunu gözlemleyerek zamanlamaları çıkarır (durum kaydı [@graphics-ppustatus] aracılığıyla gösterilir).

Bunu başarmak için oyunlar **Sprite 0 Hit** adı verilen bir teknik uygular. Super Mario Bros, PPU'ya madeni paranın arkasında sahte bir sprite oluşturmasını söyler, bu kare içinde çizilen ilk sprite olur. PPU bunu ışınladıktan sonra, durum kaydını ilk sprite'ın (diğer adıyla 'sprite 0') çizildiğini belirten bir bayrakla günceller. Bu arada oyun, sprite 0 durumunun işaretlenip işaretlenmediğini (diğer bir deyişle 'isabet') sürekli olarak kare ortasında kontrol eder, eğer bu olursa, oyun Mario'nun bulunduğu yere kaydırmak için arka plan tablosunun kaydırma özelliğini güncellemeye devam eder.

Genel olarak, 'Sprite 0 Hit' çok hassas bir prosedürdür, çünkü zamanlamaları karıştırmak kolaydır (sprite 0'ın bayrağı yoklandıktan sonra temizlenmez, bu da 'yinelenen' pozitiflere yol açar \[@graphics-chibiakumas]). Ayrıca, bu rutin süresiz olarak tekrarlandığından, yürütülmesi oldukça pahalı (CPU döngüleri açısından) olabilir. İşin iyi tarafı, daha sonraki haritacılar bu işlevi, örneğin Super Mario Bros 3'ün görsel yeteneklerini önemli ölçüde geliştiren rastgele bir tarama çizgisine [@graphics-nesdoug\] (çok daha verimli bir teknik) her vurulduğunda tetiklenen otomatik kesintiler kullanarak devraldılar.

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

::: {.subfigures .tabs-nested .tab-float max_subfigures=1 .pixel}

![Erken tarama çizgisi.](secrets/multiplexing_1.png){.pixel .active title="Erken"}

![Geç tarama çizgisi.](secrets/multiplexing_2.png){.pixel title="Geç"}

![Kullanıcıya gösterilen son görüntü.](secrets/multiplexing_complete.png){.pixel title="Görüntülenen"}

Spesifik tarama hatları sırasında mevcut olan karolar kullanılarak oluşturulan varsayımsal kareler.

:::

Super Mario Bros. 3'ün bir başka özelliği de görüntüleyebildiği grafik miktarıdır.

Bu oyun, kesinlikle izin verilenden daha fazla arka plan taşı görüntüler. Peki bunu nasıl yapıyor? Ekran oluşturulurken farklı zamanlarda iki ekran görüntüsü alırsak, son karenin aslında *iki* farklı kareden oluştuğunu görebiliriz.

Bu, MMC3 eşleyicisinin bir başka sihirbazlığıdır ve sadece Program ROM'unda fazladan alana erişmek için kullanılmaz, aynı zamanda iki farklı Karakter yongasını bağlayarak Karakter ROM alanını da genişletir. PPU'nun ekranın hangi bölümünü talep ettiğini kontrol ederek, eşleyici bir çipe veya diğerine yönlendirecek - böylece ekranda başlangıçta desteklenenden daha fazla benzersiz kutucuğa izin verecektir [@graphics-n3s].

#### Meraklı davranışlar {.tab}

Araştırmam boyunca, PPU'nun olağandışı davranışlarını açıklayan birçok ilginç makaleye rastladım, bu yüzden bazılarından burada bahsetmeyi düşündüm:

- Daha sonra yayın için NTSC/PAL sinyallerine kodlanan RGB renkleri üreten [Master System'in VDP](master-system#graphics)'sinin aksine, **NES'in PPU'su hepsini bir kerede yapar** [@graphics-palettes]. Bu nedenle, PPU ana paletinin renkleri ile standart RGB renk alanı (mevcut teknoloji tarafından yaygın olarak benimsenmiştir) arasında bire bir bağlantı yoktur. Bu, yorumlama için bir miktar alan bırakır ve sonuç olarak, çeşitli emülatörler farklı bir palet gösterecektir.
  - RGB paletleri arasındaki tutarsızlıklar en çok Tim Worthington'ın NES'e RGB sinyal çıkışı ekleyen DIY kitinde belirgindir, çünkü önceden tanımlanmış üç palet arasında seçim yapan bir anahtar da uygulamaktadır [@graphics-nesrgb].
- Ana palet, NTSC TV sinyalini bozan bir **'lanetli' renk** (`$0D`) içerir [@graphics-cursed_colour]. Bazı TV'ler bu rengi gösterecek sinyali boşluk sinyali ile karıştırır, bu nedenle titreme meydana gelebilir.
- PPU, Nesne Öznitelik Belleğini (OAM) depolamak için DRAM'e güvenir. Şimdi, DRAM'in veri kaybını önlemek için sürekli olarak yenilenmesi gerekir (SRAM'in aksine) ve PPU, kareyi oluşturmadığında DRAM'i yenilemeyecektir [@graphics-oam]. Bu durum dikey karartma sırasında ortaya çıkar. Bu nedenle, V-boşluk sırasında meydana gelen yenilemesiz dönem tablonun bir kısmını bozacağından, OAM'nin dikey boşluk dışında güncellenmemesi tavsiye edilir.
  - PAL sistemleri için PPU varyantı bundan etkilenmez, çünkü V-Blank sırasında yenilenir (PAL sistemlerinde daha uzun sürer).

## Ses {.tabs-close}

**Ses İşleme Birimi** (APU) adı verilen özel bir bileşen bu hizmeti sağlar [@audio-apu]. Ricoh, muhtemelen hem CPU hem de APU'nun lisanssız klonlanmasını önlemek için bunu CPU çipinin içine yerleştirdi.

### Fonksiyonellik

Bu ses devresi genellikle **Programlanabilir Ses Üreteci** (PSG) olarak adlandırılır, bu da belirsiz bir şekilde yalnızca önceden tanımlanmış bir dizi dalga formu üretebileceğini ima eder, bu durumda *çoğunlukla* doğrudur. APU, ses verilerini **beş ses kanalı** üzerinden sıralar - her biri belirli bir dalga formu veya sinyal için ayrılmıştır. Her kanal, dalga biçiminin perdesini, sesini, ses seviyesini ve/veya süresini değiştiren farklı özellikler içerir. Bunlar sürekli olarak karıştırılır ve çıkış ses sinyali aracılığıyla gönderilir.

APU'nun işlevselliği bellek adresleri aracılığıyla ortaya çıkar, CPU Program ROM'da bulunan müzikle ilgili verileri okur ve APU'yu buna göre programlar.

Ayrıca, özellikle Famicom modeli, karışık ses sinyalini kartuşa gönderen kartuş pinleri sağlar, böylece ikincisi **ekstra kanallar** (ek çipler gerektirir) ile karıştırabilir [@general-cartridge].

Şimdi APU'nun sağladığı beş kanalın üzerinden geçelim [@audio-review]:

#### Nabız {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Pulse 1 kanalının osiloskop görünümü.](pulse_single_1){video="true" title="Pulse 1"}

![Pulse 2 kanalının osiloskop görünümü.](pulse_single_2){.active video="true" title="Pulse 2"}

![Tüm ses kanallarının osiloskop görünümü.](pulse_full){video="true" title="Tamamı"}

Mother (1989).

:::

İlk **iki kanal** **pulse dalgaları** üretir [@audio-apupulse]. Duyulduğunda, çoğunlukla **melodi veya ses efektleri** için kullanılan çok belirgin bir *bip* sesi sergilerler. İlgili sıralayıcı, darbe genişliğini değiştirerek üç tip darbe dalgası üretebilir (diğer adıyla **görev döngüsü**). Devreler ayrıca bir **sweep ünitesine** (perdeyi bükmeye izin verir) ve zamanla ses seviyesini düşürmek için bir **envelope generator** bağlıdır (diğer adıyla **decay**).

Çoğu oyun bir pulse kanalını melodi için, diğerini ise eşlik için kullanır. Bir oyunun bir ses efekti çalması gerektiğinde, eşlik kanalının efekti çalmak için değiştirildiğini ve ardından eşlik etmeye geri döndüğünü sık sık göreceksiniz. Bu, oyun sırasında melodinin kesintiye uğramasını önler.

Darbe dalgalarının bu nesil konsolların simgelerinden biri olduğunu söylemek doğru olur. Bunun benimsenmesinin tamamen maliyet etkin nedenlerle yapıldığını varsayıyorum: (sınırlı) CPU bir seferde yalnızca çok fazla veri işleyebilir ve pulse dalgaları basit melodileri çalmak için çok fazla parametre gerektirmedikleri için idealdir (bu da CPU döngülerini diğer işlemler için serbest bırakır).

#### Triangle {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Triangle kanalın osiloskop görünümü.](triangle_single){.active video="true" title="Triangle"}

![Tüm ses kanallarının osiloskop görünümü.](triangle_full){video="true" title="Tamamı"}

Mother (1989).

:::

APU'nun rakipleriyle karşılaştırıldığında öne çıkan özelliklerinden biri **triangle (üçgen) dalgalar** üretebilmesidir. Bunlar genellikle melodi için bir **bassline** olarak kullanılır. Dahası, perdesini dramatik bir şekilde değiştirerek **perküsyon** için de kullanılabilir.

APU'nun bu tür dalgalar için ayrılmış bir kanalı vardır. Sahne arkasında, özel bir sıralayıcı bir üçgen sinyali oluşturmak için 32 döngü alır [@audio-aputriangle], bu sınırlama ortaya çıkan üçgen dalga formunun bir adım merdiveni şeklini almasına neden olur.

Diğer tarafta, ilgili devre ses seviyesi kontrolü sağlamaz. Her halükarda, bazı oyunlar mikserin ses kontrolüyle oynayarak başka yollar buldu.

#### Ses {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Noise kanalının osiloskop görünümü.](noise_single){.active video="true" title="Ses"}

![Tüm ses kanallarının osiloskop görünümü.](noise_full){video="true" title="Tamamı"}

Mother (1989).

:::

'Ses' kavramı, herhangi bir desen veya düzeni takip etmeyen bir dizi dalga formuna atfedilir. Buna karşılık, kulaklarımız bunu beyaz statik olarak yorumlar. Bunu söyledikten sonra, APU farklı ses türlerini çalabilen bir kanal tahsis eder.

Sahne arkasında, ses üreteci bir OR kapısı [@audio-apunoise] tarafından rastgele susturulan bir zarf üretecine (Pulse kanalına benzer) dayanır. Susturma koşulu, bir geri besleme döngüsüne bağlı 15 bitlik bir kaydırma yazmacının değerine bağlıdır. Sonuç olarak bu, devrenin *sözde öngörülemeyen* desenlere, ve dolayısıyla sese sahip bir sinyal vermesine neden olur.

Kontrol açısından, 4 bit zarf üretecinin periyodunu değiştirir ve bir bit kaydırma kaydedicisinin 'Modunu' değiştirir. Geriye 32 ses ön ayarı kalıyor. Bu ön ayarların yarısı (16) **temiz statik**, diğer yarısı ise **robotik statik** üretir.

Genel olarak, oyunlar ses kanalını perküsyon veya ortam efektleri için kullanır.

#### Örnek {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Örnek kanalın osiloskop görünümü.](sample_single){.active video="true" title="Örnek"}

![Tüm ses kanallarının osiloskop görünümü.](sample_full){video="true" title="Son Hali"}

Mother (1989).

:::

Örnekler, tekrar çalınabilen kaydedilmiş müzik parçalarıdır. Gördüğünüz gibi, örnekler tek bir dalga formuyla sınırlı değildir, ancak çok daha fazla alan tüketirler.

APU'nun örneklere ayrılmış bir kanalı vardır. Burada örnekler **7 bit çözünürlük** (`0` ile `127` arasındaki değerlerle kodlanır) ve **~15,74 kHz örnekleme hızı** ile sınırlıdır [@audio-2a03ref]. Bu kanalı programlamak için, oyunlar 7 bitlik değerleri yayınlayabilir (bu da çok fazla döngü ve depolama alanı çalar) veya yalnızca bir sonraki örnek ile bir önceki arasındaki değişimi kodlamak için **delta modülasyonu** kullanabilir.

APU'daki delta modülasyonu uygulaması yalnızca 1 bitlik değerler alır, bu da oyunların, sayaç her devreye girdiğinde örneğin yalnızca `1` arttığını veya azaldığını söyleyebileceği anlamına gelir. Dolayısıyla, aslına uygunluk pahasına, delta modülasyonu oyunları APU'ya sürekli değerler aktarmak zorunda kalmaktan kurtarabilir.

Bu kanalı programlamak daha fazla alan ve CPU döngüsü gerektirdiğinden, oyunlar normalde tekrar tekrar çalınabilen küçük parçaları (davul örnekleri gibi) depolar. Her ne olursa olsun, NES'in ömrü boyunca çok sayıda geliştirici bu kanal için akıllıca kullanımlar buldu.

### Sırlar ve sınırlamalar {.tabs-close}

APU bir plak, kaset veya CD'nin kalitesiyle karşılaştırılamazken, programcılar NES'in modüler mimarisi sayesinde kapasitesini genişletmenin yollarını buldular.

#### Ekstra Kanallar {.tabs.active}

![Castlevania III'ün osiloskop görüntüsü (ABD/Avrupa, 1989).](castlevania_usa){.tabs-nested .tab-float .active video="true" title="Amerikan"}

![Akumajō Densetsu'nun (Castlevania III'ün Japonca versiyonu, 1989) osiloskop görünümü.](castlevania_jap){.tabs-nested-last video="true" title="Japon"}

Famicom'un ses genişletme için özel kartuş pinleri sağladığını hatırlıyor musunuz? *Castlevania 3* gibi oyunlar bu avantajdan yararlandı ve **Konami VRC6** adında ekstra bir çip paketledi, bu da karışıma **iki ekstra darbe dalgası ve bir testere dişi dalgası** ekledi.

Oyunun Japon ve Amerikan versiyonları arasındaki farkı gösteren iki örneğe bir göz atın (ikincisi, ses genişletme özellikleri sağlamayan NES varyantında çalışır).

#### Tremolo {.tab}

![Final Fantasy III'ün (1990) osiloskop görünümü.](tremolo_full){.tab-float video="true"}

Kartuş maliyetlerini artırmak yerine, bazı oyunlar daha fazla kanal eklemek için teknolojiden ziyade yaratıcılığa öncelik verdi.

Bu örnekte Final Fantasy III, ekstra kanal hissi vermek için tremolo efektlerini kullanma fikriyle ortaya çıkmıştır.

### Daha rafine bir gözlem {.tabs-close}

Şimdi APU'nun neler yapabildiğine bir göz attığınıza göre, sesin nasıl davrandığını gözlemlemek için size alternatif bir yöntem göstereyim. Bu sadece APU hakkında zaten bildiklerinizi tamamlamakla kalmayacak, aynı zamanda daha objektif bir inceleme sağlayacaktır, özellikle de artık kulaklarınıza güvenmediğinden.

Her şeyden önce, ses teorisine hızlı bir girişle başlayalım.

**Fourier Analizi** ilkeleri sayesinde, duyduğumuz her bir sesi farklı frekans ve genliklere sahip **sinüs dalgalarının bir toplamına** ayrıştırabiliriz [@audio-complexwaveforms]. En bas sinüs dalgasına (en düşük frekansta) **fundamental** ve diğerlerine **overtonlar** denir. Temel dalgayı ve üst tonlarını eklerseniz, orijinal sesi geri alırsınız. Bununla birlikte, tanınabilir bir perdeye sahip seslerde, çoğu (hepsi olmasa da) üst tonun temel frekansın katları olan frekanslara sahip olduğunu göreceksiniz. Bu nedenle, bu üst tonlara **harmonikler** [@audio-harmonics] adı verilir.

Pusle, triangle ve testere dişleri gibi dalga biçimleri, içerdikleri harmonikleri belirleyen bir formülü takip ettiğinden, harmonikler bu bölümde tekrar eden bir konu haline gelecektir. Aksi takdirde, bu dalga biçimleri 'mükemmel' şekillerinden sapabilir.

#### Spektrogramlara giriş

Sinüs dalgaları artık herhangi bir sesi oluşturabilecek temel bileşen olduğundan, artık duyduğumuz sesleri sinüs dalgalarına göre analiz edebiliriz. Şimdi, her türlü veri analizi için, büyük miktarda bilgiyi düzenlemek üzere bir grafik çizmekten daha kullanışlı bir şey yoktur. Ses analizi durumunda, **Spektrogramlarımız** var. Bunlar, bir ses örneğinin tüm bilgilerini tek bir grafikte kodlar. X ekseni zamanı (saniye cinsinden), Y ekseni bu süre zarfında üretilen sinüs dalgalarının frekanslarını (Hz cinsinden) ve Z ekseni (her noktanın renk parlaklığı) her frekansın gücünü/ses yüksekliğini (desibel cinsinden) gösterir.

![Bir Ses kanalının altı saniyesini görselleştiren spektrogram örneği.](spectrogram_example){video="true"}

Bu örnekten de görebileceğiniz gibi, her yatay çizgi (diğer bir deyişle nokta dizileri) bir sinüs dalgasına karşılık gelir (en düşük temel, diğerleri ise harmoniklerdir) ve parlaklıkları genliği gösterir. Bunu göz önünde bulundurarak aşağıdaki bilgileri çıkarabiliriz:

- Zamanla yatay çizgiler büyük ölçüde yer değiştirme eğilimindedir. Pulse kanalının ses aralığı değişiyor.
- Her notanın başlangıcı gürültülüdür ( parlak noktalar) ve ardından hızlı bir sessizlik gelir. Bu APU'nun iş başındaki envelope kontrolünün göstergesidir.
  - Dahası, bozulma süresinin sonunda parlak bir dikey çizgi ortaya çıkar. Oldukça kısa olduğu için kolayca duyulmuyor ama her durumda bu bir gürültü (kısa patlama sesi) ve bunun APU'nun bir eksikliğinden kaynaklandığını düşünüyorum.
- Bir notayı 0,25 saniyeden daha uzun süre tutmak **vibrato** ortaya çıkarır (Y ekseninde sürekli dalgalanmalar). Bunun kasıtlı mı ( sweep fonksiyonunu kullanarak) yoksa APU'nun olumsuz bir etkisi mi olduğundan emin değilim.

Bu gözlemlerin çoğunun sadece basit bir ses duyarak kolayca elde edilemeyeceğine dikkat edin, bu bölümün yazılmasının nedeni budur.

#### APU'nun Çizilmesi

NES'in APU'sunu incelemek için, önceki örnekleri kullanarak her biri APU'nun bir kanalına karşılık gelen beş spektrogram derledim. Bunların yanı sıra, verilerin neyi gösterdiğini çözme girişimimi de bulacaksınız.

Başlamadan önce, verileri hatasız (ekstra gürültü gibi) toplayabilmek için bazı ödünler verdiğimi itiraf etmeliyim. Kayıtlar, PSG tabanlı ses yongalarının emülasyonu ile ilgili temel bir sorunu çözmek için **band sınırlı sentez** kullanan 'towave' adlı bir Windows programı kullanılarak elde edildi. Basitçe, darbeler, üçgenler ve testere dişleri **sonsuz harmoniklerden** oluşur. Ancak bu, 44.1 kHz örneklerle sınırlı olan modern ses kartlarıyla iyi bir uyum sağlamaz. Bu nedenle, ses kartının limiti dahilinde doğru harmonikleri seçmek için 'band-limited synthesis' (bant sınırlı sentez) adı verilen bir teknik kullanılır. Sonuç olarak, bu teknik performans, doğruluk ve örtüşme önleme arasında uygun bir denge sağlar. Bununla birlikte, veriler analog muadiliyle %100 aynı olmayabilir (aksine, kayıt ekipmanından kaynaklanan ses gibi başka sorunlar da ortaya çıkacaktır), ancak kabul edilebilir bir derecede olduğuna ve en önemlisi, makalenin bu bölümü için işi yaptığına inanıyorum.

Bununla birlikte, analize devam edelim.

##### Nabız {.tabs.active}

![Pulse 1 kanalının spektrogramı.](spectrograms/eb0_pulse_nes.png) {.tab-float}

Teori, bir darbe tonunun yalnızca tek harmonikler içerdiğini söyler. Başka bir deyişle, temel, üçüncü harmonik, beşinci ve benzeri ile birleştirilir. Dahası, her bir harmonik temelden uzaklaştıkça genliği azalır. Genlik formülü `genlik = 1 ÷ harmonik sayısı` [@audio-pulse] şeklindedir.

Bu nedenle, spektrogramdaki her bir harmoniğin parlaklığının Y ekseninde yükseldikçe nasıl azaldığına dikkat edin. Bununla birlikte, APU'nun darbe dalgaları da her harmonik sayısında artan ve yukarıda bahsedilen vibrato etkisini sergiliyor gibi görünmektedir. Dahası, spektrogramın herhangi bir sesten arındırılmış olması gereken alanları (muhtemelen gürültü ve diğer kusurların sonucu olarak) sessiz tonlar içermektedir.

##### Triangle {.tab}

![Triangle kanalının spektrogramı.](spectrograms/eb0_triangle_nes.png) {.tab-float}

Tringle dalga da tek harmoniklerden oluşur ancak genlikleri daha hızlı azalır (burada `genlik = 1 ÷ harmonik sayısı²` [@audio-triangle]).

Ancak burada gösterilen bu değildir, APU'nun ürettiği basamak merdiven şeklindeki üçgenler ekstra harmoniklere ve artan genliklere yol açar.

##### Ses {.tab}

![Ses kanalının spektrogramı.](spectrograms/eb0_noise_nes.png) {.tab-float}

Doğal olarak, gürültü harmonik kurallarına uymaz ve tüm frekans alanını rastgele doldurabilir, dolayısıyla kolayca tanınabilir bir perde yoktur.

Bununla birlikte, zaman çizelgesini takip ederek APU'nun sağladığı ve her biri farklı bir dizi ton sergileyen farklı gürültü ön ayarlarını ayırt edebilirsiniz.

##### Örnek {.tab}

![Örnek kanalının spektrogramı.](spectrograms/eb0_dcm_nes.png) {.tab-float}

Önceki kanalların aksine, örnek kanalı yalnızca geliştiricinin APU'ya beslediği düşük çözünürlüklü kaydı oynatır. Örnekte bir davul seti çalındığı düşünüldüğünde, spektrogramda tanımlanabilir herhangi bir muamele göremiyorum (beyaz gürültüye benzerlikler dışında).

##### Sawtooth {.tab}

![VRC6'nın Sawtooth kanalının spektrogramı.](spectrograms/castlevania_saw_nes.png) {.tab-float}

Bonus olarak, VRC6 genişlemesinden Sawtooth kanalına da göz atalım. Başlangıç olarak, mükemmel bir Sawtooth dalgası tüm harmoniklerden oluşur ve her biri azalan genlikler sergiler (burada `genlik = 1 ÷ harmoni sayısı` [@audio-sawtooth]).

Bu, dijital ekipmanlar için oldukça önemli bir gerekliliktir ve doğal olarak bir oyun kartuşu için karşılanamaz (böyle bir mükemmelliğe ihtiyacı bile olmayabilir!). Dolayısıyla, APU'nun üçgen dalgalarına benzer şekilde, VRC6 Testere dişi dalgalarını 7 döngüde sıralar (ve böylece benzer basamak merdiveni efektleri üretir).

Sonuç olarak, ilgili spektrogram çok dağınıktır, VRC6'nın yaklaşım teknikleri dalgayı birçok yerde ekstra harmoniklerle doldurur.

#### Sonuç {.tabs-close}

Görünüşe göre NES'in sentetik dalga formları teoride belirtildiği gibi şekillendirilmiş değil. Bu APU'nun hatalı olduğu anlamına mı geliyor? Hayır! APU'nun tasarlanma şekli, bu konsola benzersiz ve tanımlanabilir sesler kazandırdı ve bu özellikler, kasıtlı olsun ya da olmasın, spektrogramların olağandışı sonuçlar göstermesine neden oldu.

Bir ek not olarak, mükemmel geometriye gözlerimizle bakmak hoş olabilir, ancak ilginç bir şekilde kulaklarımız mükemmel kenarlara sahip dalga formlarından pek hoşlanmaz! (patlama sesleri duymaya başlayabilirsiniz).

İleriye dönük olarak, spektrogramları kullanarak ses analizi yapmak, ister basit bir analiz için ister diğer sistemlerle karşılaştırmalar yapmak için olsun, diğer makalelerde kullanışlı olacaktır. Lütfen bu grafiklerin *the mother lode* aracı olmadığını, özellikle de çok fazla kanal/enstrümanla karıştırılmış (ayrıştırılmasını büyük ölçüde zorlaştıran) ses örneklerinde kullanılabileceğini unutmayın. Ancak her türlü objektif çalışma için sağlam bir başlangıç sağlayacaklarını düşünüyorum.

## Oyunlar

NES oyunları temel olarak 6502 assembly dilinde yazılır ve **Program ROM**'unda bulunurken, oyunun grafikleri (karolar) **Character memory**'de saklanır.

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
- [Wavetable synthesis](game-boy#tab-7-3-wave) [@games-fds_audio] kullanan bir **ekstra ses kanalı**.

![FDS ekipmanı Famicom'a monte edildi.](fds/mounted.png) {.open-float.no-borders}

Disket tek bir ortam olduğundan (çok çipli kartuşların aksine), tüm oyun verilerinin içine sıkıştırılması gerekir, ancak özel bir **dosya sistemi** kullanılarak düzenli tutulur.

Bununla birlikte, Famicom/NES'in çalışması için kesinlikle ayrılmış Program ve Character memory gerekir, bu yüzden bunu çözmek 'RAM adaptörünün' işidir. Bu bileşen, disketten okunan oyun verilerini tamponlamak için **32 KB Program RAM** ve **8 KB Character RAM** barındırır ve bunu yaparken konsolun kartuş tabanlı bir oyunmuş gibi okumasını sağlar.

{.close-float}

Sürücüyü çalıştırmak için RAM adaptörü bir **BIOS** [@games-fds_bios] depolamak için ek bir 8 KB ROM yerleştirir. Bu program aşağıdaki görevleri yerine getirir:

- Bir sıçrama animasyonu yükler.
- Oyunu disketten başlatır. BIOS, perde arkasında disketin içeriğini ilgili bellek yongasına kopyalayan rutinler içerir, böylece konsol bunu okuyabilir.
- Oyunların kullanması için disklerin dosya sisteminde gezinme gibi I/O API'leri sağlar.

![FDS için iki perakende oyun örneği. Diskin mavi 'aroması' da toz geçirmezdi.](fds/floppies.jpg) {.toleft}

![FDS açılış animasyonu, kullanıcının... şey... bir oyun eklemesini bekliyor.](fds_bios){.toright video="true"}

O dönemde Nintendo, perakende mağazalarında bazı 'kiosks'lar kurdu, böylece kullanıcılar disketlerini getirip indirimli bir fiyata yeni bir oyunla üzerine yazabiliyorlardı.

Ne yazık ki, birkaç yıllık kullanım ömrünün ardından Famicom Disk Sistemi kullanımdan kaldırıldı ve sonraki oyunlar kartuş ortamına geri döndü. İşin iyi tarafı, FDS'nin işlevlerine kıyasla benzer (veya daha iyi) yeteneklere sahip yeni haritalar mevcuttu.

## Korsanla mücadele ve bölge kilidi

Nintendo, **Checking Integrated Circuit** (CIC) [@anti_piracy-cic] adı verilen tescilli bir **lockout** çipi sayesinde izinsiz yayınları engelleyebilmiştir. Konsolda bulunur ve sıfırlama sinyaline bağlıdır (ve bu nedenle kolayca çıkarılamaz).

Bu çip, oyun kartuşunda başka bir kilitleme çipinin varlığını kontrol eden dahili bir program olan **10NES**'i çalıştırır. Bu kontrol başarısız olursa konsol sonsuz sıfırlamaya gönderilir.

Her iki kilitleme yongası da konsolun çalışma süresi boyunca sürekli iletişim halindedir. Bu sistem, çipi boşta bırakan konsolun kilitleme pimlerinden biri kesilerek etkisiz hale getirilebilir. Alternatif olarak, -5V'luk bir sinyal göndermek onu dondurabilir.

CIC, 1983'teki video oyunu çöküşünün neden olduğu korkunun bir sonucu olarak var olmuştur. Nintendo'nun o zamanki başkanı Hiroshi Yamauchi, kaliteli oyunları zorunlu kılmak için her birini onaylamaktan sorumlu olacaklarına karar verdi [@anti_piracy-vindicator].

Konsolun Japon modeli olan Famicom'un 1983'teki çöküşten önce piyasaya sürüldüğünü fark edeceksiniz. Bu nedenle ne oyun kartuşları ne de konsol CIC devresi [@anti_piracy-nocic] içermez, bunun yerine pinler isteğe bağlı ses genişletme için kullanılır.

## Hepsi bu kadar
