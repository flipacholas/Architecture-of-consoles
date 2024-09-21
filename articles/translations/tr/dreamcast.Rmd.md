---
short_title: Dreamcast Mimarisi
long_title: Dreamcast Mimarisi
name: Dreamcast
date: 2019-10-07
release_date: 1998-11-27
subtitle: Son bir deneme
generation: 6
javascript:
  - 'threejs'
  - 'audioswitcher'
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "Dreamcast.<br>Japonya'da 29/11/1998, Amerika'da 09/09/1999 ve 14/10/1999'da Avrupa'da satışa sunuldu."
  Motherboard:
    caption: "Revizyon 'VA1' gösteriliyor.<br>Resmi dokümanlarda sistemin 128KB flash bellek içerdiği belirtilirken, bu anakart nedense 256KB EEPROM yongası içeriyor.<br>Pil ve denetleyici bağlantı noktaları 'Ön panel' adı verilen bir ek kartta bulunuyor."
  Diagram:
    caption: "Önemli veri yolları genişlikleri ve hızları ile etiketlenmiştir."
#Historical
aliases:
  - /projects/consoles/dreamcast/
---

## Giriş

Sega Dreamcast, hem oyun geliştiricilerine hem de konsol oyuncularına hitap etmek için selefine ([Satürn](sega-saturn)) göre birçok yeni özellik sundu. Bu Sega'nın konsol pazarını fethetmek için son girişimi olsa da, Dreamcast'te öncülük edilen bazı teknolojiler devam etti ve gelecekteki ana akım cihazlara taşındı.

## {.supporting-imagery}

## İşlemci (CPU)

Hitachi için işler sorunsuz ilerliyordu, <a href=“sega-saturn#cpu”>ikonik SuperH çipleri</a> birden fazla müşteri bulmuştu ve şirket artık serinin dördüncü sürümü için hazırdı. Yeni girişleri, gömülü yetenekleri 3D oyunların sahip olduğu işlevsellikle birleştirecek.

SuperH'yi ilk benimseyenlerden biri olan Sega, yeni konsollarına güç sağlamak için şaşırtıcı olmayan bir şekilde Hitachi'nin son sevkiyatını seçti. Bu nedenle Dreamcast'te **SH-4 CPU** bulunmaktadır ve bu işlemci **200 MHz** [@cpu-spec] hızında çalışmaktadır. Ayrıca, [bu sefer işleri yoluna koymak](sega-saturn#the-final-product) için, **sadece bir tam donanımlı CPU** var.

### Teklif

Bununla birlikte, bu yeni işlemci hakkında ilginç olan nedir?

Bununla birlikte, bu yeni işlemci hakkında ilginç olan nedir? Öncelikle, SH-4 önceki modellerin bir süper seti olarak devam ediyor, yani **32-bit RISC mimari**, **5 aşamalı pipeline** ve **16-bit instruction set** dahil olmak üzere SuperH serisinin mevcut [tüm özelliklerini](sega-saturn#cpu) miras alıyor. Ne yazık ki, aynı zamanda [control hazards](playstation#delay-galore)'ın da mirasçısıdır.

![SH-4 çipi.](sh4.jpg)

Bunun da ötesinde, yeni nesil CPU olarak, gömülü uygulamaların kapsamının ötesine geçen birçok iyileştirme sunuyor [@cpu-arch]:

- Bir **2-way superscalar** pipeline: CPU'nun boru hattının her aşamasında birden fazla komutu (bu durumda iki) işleyebildiği yeni bir paralellik tekniği. Bu da saniyede daha fazla talimatın yürütülmesine neden olur.
  - Perde arkasında, CPU iki talimatı iki farklı (kullanılabilir) yürütme birimine dağıtıyor. Bu nedenle - çok küçük bir grup hariç - tüm komutların paralelleştirilmesi için farklı türlerde (yani branching, aritmetik, vb.) olması gerekir [@cpu-soft_doc]. Aksi takdirde, yalnızca sıralı olarak yürütülebilirler. Her ne olursa olsun, SH-4 aynı zamanda asla paralelleştirilemeyecek bir talimat alt kümesi de sergiler, çünkü bunu mümkün kılmak için çok fazla bağımlılık içerirler.
- **8 KB instruction cache** ve **16 KB data cache**: Konsollar veri önbelleğinden daha fazla komut önbelleği içerme eğiliminde olduğundan bu oran oldukça ilginçtir. Ancak SH-4, veri önbelleğinin iki bölüme ayrılmasına izin verir: **8 KB Scratchpad** (hızlı RAM) ve **8 KB veri önbelleği**.

#### Özel bir çalışma

Bir oyun konsolu CPU'sunun ortak işleri arasında bir oyunun mantığını idare etmek, düşman yapay zekasını çalıştırmak ve GPU'yu instruction'la beslemek yer alır.

Dreamcast ile GPU'nun yalnızca [rasteriser](playstation#graphics) görevlerini yerine getirdiğini göreceksiniz. Dolayısıyla, CPU'nun grafik işlem hattının büyük bir kısmına dahil olması gerekir. Bu, CPU'nun büyük miktarda geometri verisini [işleyeceği](playstation#tab-2-2-geometry-transformation-engine) anlamına gelir (perspektif dönüşümlerini hesaplamak gibi). Şimdi, CPU'nun bu rolü sürdürebilmesini sağlamak için Sega ve Hitachi işbirliği yaparak SH-4'e iki önemli eklenti eklediler.

İlk eklenti özel bir **64-bit Kayan Nokta Birimi** (FPU). Bu bileşen, **IEEE-754 standardına** uyarak 32 bit ondalık sayıları ('tek hassasiyetli' veya 'float' olarak adlandırılır) veya 64 bit olanları ('çift hassasiyetli' veya 'double' türü) hesaplar. Register dosyası **otuz iki 32 bitlik register'dan** oluşur, ancak bunlar aynı zamanda **on altı 64 bitlik register'dan** oluşan farklı bir grupta birleştirilebilir, bu da birimin double işlem yapabilmesini sağlar.

Bu yeterli gelmediyse, Hitachi FPU ile bir adım daha ileri gitti ve bu kez **sekiz 128 bitlik register**'dan oluşan başka bir register grubu oluşturmak için ekstra logic uyguladı. İçinde her bir register artık dört adet 32 bit float ya da başka bir deyişle **128 bit vektör** olarak saklamaktadır. Bu format grafikle ilgili işlemler için idealdir.

Yeni vektörleri iyi bir şekilde kullanmak için FPU, [Saturn Control Unit](sega-saturn#the-third-processor-and-counting)'in sağladığı gibi bunları çalıştırmak için özel instruction'lar içerir, ancak sektör artık daha standart hale gelmiştir. Yeni talimatlar genellikle **Single Instruction Multiple Data** (SIMD) olarak bilinen bir yapıya sahiptir ve aşağıdaki cebirsel işlemleri gerçekleştirebilir:

- Nokta çarpım.
- Karelerin toplamı.
- Matris çarpımı.

İkinci eklenti ise SH-4'ün artık <strong x-id=“1”>64 bit genişliğinde</strong> olan ve CPU'nun 32 bitlik değer çiftlerini aynı anda aktarabilmesini sağlayan harici bus'ıdır. Bu, bu CPU'nun genel performansına katkıda bulunan bir başka gelişmedir.

#### Bellek & erişim

Dreamcast'lerde **16 MB SDRAM** bulunur ve **100 MHz** bus (CPU hızının yarısı) kullanılarak doğrudan CPU'ya bağlanır.

Buna karşılık, belleğin data bus'ı sadece 32 bit genişliğindedir [@cpu-spec]. Bu, CPU'nun 64-bit data bus'ının boşa gitti anlamına mı geliyor? Hayır, çünkü RAM <strong x-id=“1”>iki adet 8 MB bank</strong> kullanılarak takılmıştır. Yani, her bir çip CPU'nun data bus hatlarının yarısına bağlıdır.

![Bellek şeması.](memory.png)

Bu belleğe erişmek için CPU, sanal adresleme için özel bir <strong x-id=“1”>Memory Management Unit</strong> veya 'MMU' içerir, bu CPU'nun fiziksel bellek adres alanı <strong x-id=“1”>29 bit genişliğinde</strong> olduğu için bu yararlıdır. Ayrıca, dört <strong x-id=“1”>Translation Lookaside Buffers</strong> (TLB'ler) sayesinde programcılar 32 bit adresleri performans kaybı yaşamadan kullanabilirler.

Şimdi, adresleme için sadece 29 bit gerektiğinden, fazladan üç bit bellek korumasını kontrol eder, sırasıyla bellek haritasını değiştirir ve önbelleği atlatır \[@cpu-marcus\] \[@cpu-akiba\].

Nihayetinde, bu özelliklerin kullanılıp kullanılmayacağına programcı karar verir. Bu sistem için oyunlar kesinlikle bellek korumasına ihtiyaç duymaz ve MMU'nun açılışta manuel olarak etkinleştirilmesi gerekir.

### UMA yok ama...

Bu sistem, [tanınmış bir rakibi](nintendo-64#simplified-memory-access) gibi katı Unified Memory Architecture veya UMA etrafında tasarlanmamış olsa da, **G/Ç erişimini GPU'ya devretmektedir**. Bu da CPU'nun kendi özel RAM'i ya da seri arayüzü (ki bunlar da bağlı) dışında bir şey alması gerektiğinde GPU'dan talepte bulunması ve gerekirse beklemesi gerektiği anlamına geliyor.

Bu CPU aynı zamanda **Paralel I/O** veya 'PIO' olarak adlandırılan ve aynı anda birden fazla I/O konumunu manipüle etmek için kullanılan benzersiz bir işleve sahiptir. Sega bu pinleri CPU'nun GPU'nun <strong x-id=“1”>video modunu</strong> manipüle edebilmesi için bağladı (daha fazla ayrıntı 'Grafikler' bölümünde açıklanmıştır).

### Yolun sonu

Bahsedilen tüm avantajlara rağmen, korkarım ki SuperH serisi son büyük kullanıcısı Dreamcast'in mağazaları terk etmesinden sonra önemli bir ilerleme kaydedemedi. SH-4'ün popülerliğinden sonra Hitachi (ya da şu anki sahipleri olan Renesas Electronics) aynı başarı seviyesini tekrarlayamadı ve gömülü/el tipi pazarın o zamandan beri [ARM](game-boy-advance#cpu)'ı tercih ettiğini düşünürsek ([StrongARM](nintendo-ds#arms-new-territories) sayesinde), Renesas'ın Hitachi'nin buluşunu yakın zamanda sürdüreceğini düşünmüyorum.

Ancak bilgisayarla ilgili iyi olan bir şey varsa o da teknolojik ilerlemenin markaların ve şirketlerin sınırlarının ötesine yayılma eğiliminde olmasıdır. Örneğin, SH'nin sıkıştırılmış komut tekniği ARM'nin [Thumb modu](game-boy-advance#tab-2-3-squeezing-performance) (ikincil bir 16-bit ISA) ile devam etmiştir [@cpu-lwn]. Ayrıca, 2012 yılında, 'J2' [@cpu-jcore] adı verilen modern bir SuperH uyumlu CPU üretmek için gönüllülük esasına dayalı bir proje başlatılmıştır.

## Grafikler

GPU paketi, <strong x-id=“1”>100 MHz</strong> hızında çalışan <strong x-id=“1”>Holly</strong> adlı özel yapım bir çiptir. Önceki şirket içi tasarımların aksine Sega, rekabetçi bir 3D hızlandırıcı sağlamak için VideoLogic (şimdi Imagination Technologies olarak biliniyor) ile ortaklık kurdu.

![Holly chip (termal pedleri çıkardıktan sonra) ve video kodlayıcı.](holly.jpg)

Holly'nin içinde VideoLogic'in <strong x-id=“1”>PowerVR2</strong> ('PowerVR Series2' ve 'CLX2' olarak da anılır) adlı özel grafik devresini bulabiliriz, önceki PowerVR GPU'larını temel alır ancak Dreamcast için uyarlanmıştır.

### Mimari

VideoLogic, 3D motorunun yapımı için **Tile-Based Deferred Rendering** (TBDR) adı verilen alternatif bir yaklaşım seçti.

TBDR, tüm kareyi bir kerede oluşturmak yerine (geleneksel **Immediate Mode Renderers** veya 'IMR'nin yaptığı gibi [@graphics-arch]), oluşturma alanını 'karo' adı verilen birden çok bölüme ayırır. Ardından, her bir karo üzerinde ayrı ayrı render işlemini gerçekleştirir ve sonuç nihai kareyi oluşturmak için birleştirilir [@graphics-powervr].

![Sonic Adventure (1999).](sonic.png)

Bu yenilikçi tasarım ilginç avantajları da beraberinde getiriyor:

- Büyük ölçüde **paralelleştirilebilir**, bu da bant genişliğini ve güç kullanımını önemli ölçüde azaltır.
- Çokgenleri otomatik olarak **önden arkaya doğru** sıralayarak ve ardından boru hattının ilk aşamalarında [z-testleri](nintendo-64#modern-visible-surface-determination) gerçekleştirerek [**görünürlük sorununa**](sega-saturn#an-introduction-to-the-visibility-problem) akıllıca bir çözüm uygular. Bu görevlerin kombinasyonu yalnızca orijinal sorunu çözmekle kalmaz, aynı zamanda kaynakları boşa harcayan ve performansı düşüren **aşırı çizimi (gizli çokgenlerin pikselleştirmesi) de önler.**.

Imagination'ın bu verimli teknolojiyi ileriye taşıyarak ilk nesil iPhone, iPhone 3G, Nokia N95 ve Dell Axim x51 gibi inanılmaz sayıda cihaza güç veren Seri 4 PowerVR çekirdeklerini üretmesi hiç de şaşırtıcı değil.

### Mimarisi

Dreamcast'in GPU'sunun iki ana bileşenine bir göz atalım [@graphics-marcus]:

#### Tile Accelerator {.tabs.active}

![Karo Hızlandırıcının Mimarisi.](tile_accelerator.png) {.tab-float}

Render işlemi başlamadan önce, **Tile Accelerator** olarak bilinen bir bileşen ön işleme gerçekleştirir. İşlem, geometrinin render edileceği birkaç 32x32 karo kutusu tahsis ederek başlar.

Ardından, Tile Accelerator:

1. CPU tarafından verilen geometri verilerini ve çizim komutlarını alır (DMA veya geleneksel aktarımları kullanarak).
2. Bu verileri *dahili* bir formata dönüştürür.
3. Geometriyi koordinatlarına göre her birini dağıtır. Geriye kalan geometri ise atılacaktır.
4. Ortaya çıkan Display List'lerini oluşturur.

Bu Display List'ler daha sonra 3D motoru tarafından işlenir: PowerVR2.

#### PowerVR2 Çekirdeği {.tab}

![PowerVR2 Çekirdeğinin Mimarisi.](powervr2.png) {.tab-float}

Grafiklerin hayata geçirildiği yer burasıdır, Tile Accelerator'dan alınan Display Lists çekirdeğe **dahili bir çerçeve arabelleği** kullanarak tek bir karenin geometrisini oluşturmasını söyler. Süreç şöyledir:

1. **Image Synthesis Processor (Görüntü Sentez İşlemcisi)** veya 'ISP' ilkelleri (üçgenler veya dörtlüler) alır ve görünmeyen çokgenleri kaldırmak için **Hidden-Surface Removal (Gizli Yüzey Kaldırma işlemi)** gerçekleştirir. Ardından, Z tamponlarını ve şablon tamponlarını hesapladıktan sonra veriler, diğerlerinin arkasında görünecek çokgenlerin işlenmesini önlemek için **Depth Testing (Derinlik Testi)** ve bir 2B çokgenin (**Mask (Maske)** olarak da adlandırılır) arkasında yer almaları halinde görünmeyecek geometriyi ayıklamak için **Stencil Tests (Şablon Testleri)** işemlerinden geçer.
    - Bu testlerin boru hattının başlangıcında nasıl etkin bir şekilde gerçekleştirildiğine dikkat edin. Bunun aksine, [late z-buffering kullanan](nintendo-64#modern-visible-surface-determination) önceki konsollar geometriyi pipeline'ın sonunda atmaktadır. ISP yaklaşımı, sonunda çöpe gidecek geometrinin işlenmesini önler [@graphics-surface] ve böylece kaynak tasarrufu sağlar.
2. **Texture and Shading Processor (Doku ve Gölgelendirme İşlemcisi)** veya 'TSP' karo alanı üzerinde renklendirme, gölgelendirme ve çoklu efektler uygular.
    - Dokular karo dışa aktarılana kadar uygulanmaz, yani ortaya çıkan fazla çizim (varsa) dolgu oranını düşürmez.

İşlem tamamlandıktan sonra, işlenen karo VRAM'deki ana çerçeve arabelleğine yazılır. Bu işlem tüm karolar bitene kadar tekrarlanır. İşlem tamamlandığında, ortaya çıkan çerçeve arabelleği **Video kodlayıcı** tarafından seçilir ve video sinyali aracılığıyla gönderilir.

### Büyük resim {.tabs-close}

Açık mimari farkın yanı sıra, Texture and Shading Processor, bu konsolun eski [Saturn](sega-saturn)'den ne kadar uzak olduğu hakkında bir fikir veren birçok yetenekle birlikte gelir. İşte bazı önemli örnekler:

- **Alpha blending (Alfa karıştırma)**: Şeffaflık efektleri elde etmek için üst üste binen katmanların renklerini birleştirir.
  - Bu sistemde saydamlık uygulamak için kullanılan işleme sıralamadan **order-independent transparency (bağımsız saydamlık)** denir. Algoritma, renklerini karıştırmadan önce ilkelleri otomatik olarak sıralar ve bu işlem render işlemini yavaşlatsa da, tüm sıralamayı manuel olarak yapmak için oyunun kendisine güvenilmesini önler. Bu nedenle Dreamcast oyunları şeffaf nesneleri görüntülemede mükemmeldi.
  - Karo tabanlı sistemle birlikte, siparişten bağımsız şeffaflık önceki [aksaklıkları](sega-saturn#the-transparency-issue) tamamen giderir.
- **Mip-Mapping**: Gerekli ayrıntı düzeyine bağlı olarak dokunun küçültülmüş bir sürümünü otomatik olarak seçer. Bu, kameradan uzakta görülebilecek büyük dokuların işlenmesini önlemek için yapılır (bu işlem gücü kaybı olur ve örtüşme üretir).
- **Environment mapping**: Dokular üzerinde yansımalar uygular.
- **Bilinear, Trilinear ve anizotropik filtreleme**: Bu, dokuları yumuşatmak ve pikselleşmeyi önlemek için kullanılan farklı algoritmaları ifade eder. Bunlar 'en kötü'den 'en iyi'ye doğru sıralanır ve her birinin sonuç kalitesi, gereken hesaplama miktarıyla doğru orantılıdır.
  - Bu Saturn'e göre büyük bir adım, çünkü önceki model herhangi bir doku filtresi sağlamıyordu!
- **Bump mapping**: Fazladan poligon harcamadan yüzeylerdeki çıkıntıları simüle eder.

### Detay Kazanma

Holly artık [selefine](sega-saturn) göre ~10 kat daha fazla poligon çizebiliyor, işte model tasarımlarının artık o kadar da sınırlı olmadığını gösteren bir *Önce ve Sonra* örneği. Onları kurcalayın ve ne kadar fark olduğunu görün!

![Satürn için Sonic R (1997).<br>286 üçgen (veya 185 dörtgen).](sonic_r_saturn){.toleft model3d="true"}

![Dreamcast için Sonic Adventure (1999).<br>1001 üçgen.](sonic_adventure_dc){.toright model3d="true"}

### Video Modları

Video sistemi birden fazla ekran türünü ve formatı destekleyecek şekilde tasarlanmıştır, bu nedenle video kodlayıcı aşağıdaki sinyal türlerini destekleyen tek şekilli bir sokete çıkış verir:

- **Kompozit**: Video görüntülemek için gereken üç sinyali (chroma, luma ve sync) tek bir sinyalde birleştirir ve yalnızca tek pimli bir kablo gerektirir.
  - Bu, RCA bağlantılı eski PAL ve NTSC TV'lerde kullanılır.
- **S-Video**: Cromayı ayrı tutarken luma ve sync'i birleştirir (yani iki video hattı).
- **RGB**: Ayrı Kırmızı-Yeşil-Mavi sinyalleri gönderir ve aralarından seçim yapabileceğiniz farklı senkronizasyon türleri sağlar (kompozit senkronizasyon veya kompozit veya S-Video videodan çıkarılmış).
  - Bir SCART kablosu bu tipi kullanacaktır.
- **VGA**: RGB'yi iki senkronizasyon sinyali (yatay ve dikey) ile birleştirerek toplamda beş video hattı elde eder. Bu, aşamalı modda mümkün olan en yüksek çözünürlüğün (720x480) görüntülenmesini sağlar (bu nedenle, bu mod genellikle '480p' olarak adlandırılır). VGA aslında bir süredir bilgisayar monitörleri tarafından kullanılan standart format/ortam olmuştur.
  - Bu türü kullanmak için Sega ekstra bir aksesuar olarak bir VGA adaptörü sağlamıştır.

Şimdi, Dreamcast bunların hepsini aynı anda kodlayamaz, bu nedenle GPU ve Ses işlemcisi, istenen sinyali oluşturmak için hangi video/ses veri yollarının etkinleştirileceğini koordine eden **Görüntü Modu** adlı bir register içerir. CPU, takılan kablonun türünü algılar (video konektörünün hangi 'seçme bitlerinin' aktif olduğunu kontrol ederek) ve GPU'ya gerekli değerleri yazar. Son olarak, değerler Ses işlemcisine iletilir.

VGA kesinlikle aşamalı bir sinyal türü olduğundan (geleneksel *interlaced* sinyalin aksine), yalnızca interlaced video için tasarlanmış oyunlarda bazı uyumluluk sorunları ortaya çıkmıştır. Bu oyunların kodlarında açıkça belirtilmiştir ki oyun, VGA üzerinde görüntülenmeyecek ve kullanıcı VGA kablosunu başka bir türle değiştirene kadar CPU oyunu engelleyecektir.

## Ses

Ses işlevselliği, Yamaha tarafından üretilen özel bir çip olan **AICA** tarafından yönetilir. Bu, Satürn'de kullanılan [SCSP'nin](sega-saturn#audio) geliştirilmiş bir versiyonudur ve dört bileşenden oluşur:

- **Sound Integrated Circuit (Ses Entegre Devre)** veya 'IC': Ses sinyalini üreten ve üzerine efekt uygulayan bir dizi modülü (sentezleyici, DSP ve mixer) içeren bir devre. **64 PCM kanalına**, **16 veya 8 bit** çözünürlükle ve **44.1 kHz** örnekleme hızlarını destekler. Genel olarak, bu ses çalmak için en uygun kalitedir.
  - Ayrıca, CPU'nun iş yükünü azaltmak için bir **ADPCM çözücüsü** içerir.
  - İlginçtir ki, bir MIDI enstrümanı bağlamak için **iki MIDI pini** de sağlar, ancak bu geliştirme sırasında kullanılmak üzere tasarlanmıştır.
- **2 MB SDRAM**: Ses verilerini ve programları depolar. Ana CPU tarafından DMA kullanılarak doldurulur.
- Bir <strong x-id=“1”>ARM7DI</strong> <strong x-id=“1”>yaklaşık 2,82 MHz</strong> hızında çalışır: Ses IC'sini kontrol eder. Bu CPU, depolanan SRAM'deki küçük bir yazılımı ([sürücü](super-nintendo#audio) denilen) başlatarak programlanır. Bu yazılım, ses verilerini yorumlar ve buna göre Ses IC'yi manipüle eder.
  - Eğer merak ediyorsanız, benzer bir CPU [Game Boy Advance](game-boy-advance)'de de bulunmaktadır.
- **Bellek Denetleyicisi**: 2 MB SDRAM'in arayüzünü oluşturur.

Geliştirmeye yardımcı olmak için, resmi SDK farklı ihtiyaçlar (sequencing, decoding, vb.) için birden fazla ses sürücüsü içeriyordu.

### Evrim

[Mega Drive/Genesis](mega-drive-genesis#audio) günlerinden bu yana çok yol kat ettik, ses sentezinde ne kadar ilerleme kaydedildiğini göstermek için, burada aynı kompozisyonu kullanan biri Mega Drive ve diğeri Dreamcast için iki oyun örneği var:

![Sonic 3D Blast (1996) Mega Drive / Genesis<br>Bir önceki model, anında ses sinyalleri üretmek için FM sentezi gerçekleştirir.](megadrive){.toleft video="true"}

![Dreamcast için Sonic Adventure (1999).<br> Yeni ses alt sistemi PCM örneklerini sorunsuz bir şekilde işler.](dreamcast){.toright video="true"}

Şarkı çalarken iki konsol arasında geçiş yapmak istiyorsanız bu widget'ı deneyebilirsiniz:

![**MegaDrive / Genesis:** Sonic 3D Blast (1996).<br>**Dreamcast:** Sonic Adventure (1999).](){audio_switcher="true" src1="megadrive" src2="dreamcast" label1="MegaDrive / Genesis" label2="Dreamcast" .centered-container .video-file}

#### Açıklama

Sonic Adventure'ın bestecileri bir FM çipi programlamak yerine müziklerini kendi bünyelerinde üretmiş ve daha sonra CRI Middleware tarafından geliştirilen kayıplı bir format olan 'ADX'e kodlamışlardır. Dolayısıyla, 64 PCM kanalından yalnızca ikisini (stereo) kullanır.

ADX sıkıştırması, oyunun bellek veya bant genişliği sınırlaması olmadan GD-ROM'dan veriyi çözümlemesine ve akışa geçirmesine olanak tanır. Ayrıca, ana CPU ve ARM7'nin iş yükünü dengelemek için birden fazla yaklaşım mevcut olduğundan, ilgili sürücü birçok şekilde uygulanabilir.

### Uyanık Kalma

Bir şekilde, bu çip aynı zamanda BIOS'a **Gerçek Zaman Saati** (RTC) sağlamaktan da sorumludur, ayrıca AC gücü olmadan çalışmaya devam etmek için bir saat piline bağlıdır.

## İşletim Sistemi

Bir <strong x-id=“1”>BIOS</strong> depolayan <strong x-id=“1”>2 MB 'System ROM'</strong>u vardır. Bu, CPU'nun başlatılırken okuduğu ilk konumdur. ROM, CPU'ya oyunu başlatma ya da shell'i gösterme talimatı veren kodu içerir.

Dahası BIOS, GD-ROM sürücüsünden okuma gibi I/O işlevlerini [@games-redream] basitleştirmek için oyunların kullanabileceği rutinler de içerir.

### Etkileşimli shell

<a href=“sega-saturn#interactive-shell”>Sega Saturn</a>'de olduğu gibi, geçerli bir oyun diski takılı değilse, konsol görsel bir kabuk başlatacaktır.

![Disk olmadan önyükleme yaptıktan sonra shell.](shell.png)

Kabuk, kullanıcının aşağıdaki gibi temel ancak gerekli görevleri yerine getirmesini sağlamak için basit bir grafik kullanıcı arayüzü içerir:

- Henüz başlamadıysa oyunu başlatır.
- VMU'da depolanan kayıt verilerini değiştirmek (bu cihaz hakkında daha fazla ayrıntı daha sonra açıklanmaktadır).
- Takılı bir Ses CD'si varsa müzik çalar.
- Tarih, saat, ses ve benzeri gibi belirli ayarları değiştirmek.

### Windows CE

Dreamcast'in duyurulmasından bu yana, konsolun <strong x-id=“1”>Windows CE</strong>'yi çalıştırabileceği söyleniyordu. Hatta sadece konsolun ön kasasına baktığınızda bile Microsoft'un damgasını görebilirsiniz... Burada ne oluyor?

![Konsolun ön tarafındaki Windows CE etiketi. Tesadüfen, aynı döneme ait diğer el cihazlarında da benzer etiketler bulabilirsiniz.](windows_ce.jpeg)

Her şeyden önce, 'Windows CE' tam olarak nedir? Bu, Microsoft'un 90'lı yılların ortalarında geleneksel PC pazarının ötesindeki bölgeleri fethetmek için giriştiği pek çok projeden sadece biri. <a href=“playstation#tab-1-1-a-bit-of-history”>x86'dan daha verimli CPU mimarileri</a> ve <a href=“game-boy-advance#tab-1-2-a-new-cpu-venture”>el bilgisayarlarının ortaya çıkışının</a> ardından, Microsoft'ta yeni bir ekip Windows ekosistemini bu yeni cihazlara taşımakla görevlendirildi [@operating_system-ce_one].

Bu, Windows CE'nin ortaya çıkmasına yol açtı: mevcut Windows API'lerinin, geliştirme araçlarının ve hizmetlerinin bir alt kümesini sağlayan, sıfırdan geliştirilmiş eksiksiz bir işletim sistemi.

![Windows CE 3.0 (2000), bir masaüstü ve birkaç iş uygulaması ile birlikte gelmektedir.](wince_desktop.png)

Windows 95 ya da NT'nin aksine, yeni sistem aşağıdaki avantajları sunuyordu:

- SuperH ve hatta <a href=“nintendo-64#cpu”>NEC/MIPS VR4300</a> (Nintendo 64 tarafından kullanılan) dahil olmak üzere çok çeşitli CPU mimarilerinde [@operating_system-ce_archs] çalıştı.
- Sınırlı bir güç kaynağı (AA piller), bellek (2 MB RAM) ve depolama (4 MB ROM) gibi kısıtlı donanım göz önünde bulundurularak tasarlanmıştır.

![Windows CE 2.0 çalıştıran bir HP Palmtop PC 360LX (1997). Ağustos 2024'te ziyaret ettiğim The Centre for Computing History'de (Cambridge, Birleşik Krallık) özellikle bunu gördüm. Tesadüfe bakın ki, bu Palmtop bir SH-3 CPU barındırıyor!](palmtop.webp)

Dahası, Windows CE üreticiler için bir dizi <strong x-id=“1”>yapı taşı</strong> olarak satıldı ve hangi bileşenleri paketleyeceklerini seçmelerine izin verdi. Üreticiler daha sonra bunu çeşitli donanımlara (el bilgisayarları, satış noktası terminalleri ve hatta otomotiv sistemleri gibi) entegre edebilirler. Bununla birlikte Windows CE, Apple Newton ve Palm'a karşı etkin bir şekilde rekabet ederek Palmtop'lara (PDA'ların öncüsü) güç veren işletim sistemi olarak önemli ölçüde dikkat çekti.

Cep telefonu endüstrisi 90'ların sonunda yükselişe geçerken Microsoft, Windows CE'nin modülerliğinden yararlanarak tamamen PDA'lara ve telefonlara odaklanan bir başka işletim sistemi olan <strong x-id=“1”>PocketPC</strong>'yi (daha sonra 'Windows Mobile' olarak bilinecek) geliştirdi.

::: {.subfigures .side-by-side max_subfigures=1}

![Windows CE 2.11 'Palm-size PC Edition' 1.2 (1999)](windowsce_pocket.png) {.toleft}

![PocketPC (2000), Windows CE 3 tabanlı.](pocketpc2000.png) {.toright}

Microsoft'un Windows CE'yi PDA pazarına sokma girişimleri.

:::

Şimdi, tüm bunların Dreamcast ile ne ilgisi var? Zamanın bir noktasında Microsoft ve Sega, Dreamcast [@games-sdk] için bir Windows CE paketi üretme fikrini ortaya attı. Sonuçta, Windows CE zaten Hitachi'nin CPU'larının üzerinde çalışıyordu. Sonunda bu, eksiksiz bir masaüstü arayüzü veya Microsoft'un çevrimiçi hizmetleriyle entegrasyon olarak hayata geçmedi. Bunun yerine, donanım işlemlerini basitleştirmek için <strong x-id=“1”>isteğe bağlı bir soyutlama katmanı</strong> olarak görev yaptı.

Nintendo'nun <a href=“nintendo-64#operating-system”>Nintendo 64</a> ile sağladığına benzer şekilde Microsoft, Sega Dreamcast için uygulama programlamak üzere Windows CE tabanlı bir SDK (genellikle <strong x-id=“1”>Dragon SDK</strong> olarak adlandırılır) gönderdi. Kütüphaneler, grafik, ses ve hata ayıklamaya yardımcı olmak için gereken minimum bileşenlerle CE'nin bir alt kümesini içeriyordu. 'Microsoft kulübüne katılmanın' bir parçası olarak, geliştiriciler artık geliştirme için Microsoft'un yıldız IDE'sini (<strong x-id=“1”>Visual Studio 6.0</strong>) ve <strong x-id=“1”>Visual C++ 6.0</strong>'ı kullanabileceklerdi.

Bazı geliştiriciler bu seçeneği çok cazip buldu. CE ile birlikte gelen ses-grafik çerçevesi **DirectX 6'dan başkası olmadığından,** o dönemin çoğu PC oyunu teorik olarak Dreamcast'e kolayca taşınabilirdi. Ancak Dreamcast ile geleneksel PC arasındaki mimari farklılıklar göz ardı edilemeyecek kadar büyüktü. Sonuçta, programcılar optimum performansa ulaşmak için büyük bir optimizasyon sürecinden geçmek zorunda kaldılar [@games-direct]. Ayrıca, Dreamcast'in BIOS'u Microsoft'un framework'lerinden hiçbirini içermediği için SDK'nın oyuna statik olarak bağlanması gerekiyordu. Böylece, Sega'nın low-level kütüphanelerine kıyasla, Dragon SDK oyunun yükleme sürelerini artırdı (sonuçta, oyun diskinden ayrı bir 'işletim sistemi' yüklenmesi gerekiyordu) ve oyunun yürütülmesi boyunca Windows CE katmanı önemli miktarda kaynak tüketti.

Sonuç olarak, 'Dreamcast için Windows CE' geliştiriciler için sadece ikincil bir SDK tercihiydi. Bununla birlikte, önemli sayıda Dreamcast oyunu bu özelliği kullanmıştır.

Windows CE ve Windows Mobile'ın akıbetini merak ediyorsanız, Microsoft için taşınabilir bilgisayar pazarında işler pek de iyi gitmedi. Windows CE 2013 yılında kullanımdan kaldırılmıştır. Bir yıl önce, Windows Mobile'ın CE tabanlı çekirdeği Windows NT ile değiştirildi (masaüstü tabanlı Windows 8 ile uyumlu olarak). Bununla birlikte, Windows Mobile sonunda daha yeni platformlar (Android ve iOS) karşısında yenilgiyi kabul etti ve 2019'da kullanımdan kaldırıldı.

## Giriş/Çıkış

GPU ayrıca **Sistem Veriyolu** adı verilen G/Ç'nin çoğunu işlemek için başka bir modül içerir. Aşağıdaki arayüzleri sağlar:

- **G1** arayüzü: **BIOS ROM'un** kayıtlı yapılandırması ve **GD-ROM** içeriği ile birlikte erişilebildiği yer.
- **G2** arayüzü: **Modem** ve **Ses Denetleyicisine** erişim sağlar.
- **Maple** arayüzü: Kontrolörler (bunlara bağlı aksesuarlarla birlikte) ve CPU arasında veri parçalarını aktarır. Bu bir **seri veri yoludur** ve özel bir DMA sağlar.
- **SH-4** arayüzü: Genel amaçlı iletişim için ana CPU'yu bağlar.
- **DDT** arayüzü: DMA aktarımları sırasında ana belleğe erişmek için CPU veriyolunun kontrolünü ele alır.
- **PVR** arayüzü: CPU'yu özel bir DMA kullanarak Karo Hızlandırıcıya bağlar.

## Oyunlar

Geliştirme esas olarak **C** veya **C++ ile** yapılmıştır. Mevcut C++ derleyicileri başlangıçta işlevsellik (ve kalite) açısından çok sınırlı olduğundan, ilk başta C önerilen seçimdi.

Sega ayrıca **Sega Katana Geliştirme Kutusu** adı verilen PC benzeri bir kule şeklinde geliştirme donanımı da sağlamıştır. Bu, geliştirme için geliştirilmiş G/Ç'ye sahip Dreamcast donanımıdır. Ayrıca resmi **Katana SDK** ve Windows 98 PC'ye kurulacak araçları içeren bir CD ile birlikte geldi.

Daha önce de belirtildiği gibi, geliştiriciler Sega'nınkine ek olarak Microsoft'un Dragon SDK'sını benimseme seçeneğine de sahipti. Bu durumda, DirectX 6.0 ve Visual C++ 6.0 kullanılabilir olacaktır.

### Medya Ortamı

Oyunlar GD-ROM'larda saklanır; bunlar, daha yüksek yoğunluğa sahip [CD-ROM'lar](sega-saturn#the-compact-disc-cd) olup (bir gigabayt kapasitesine ulaşır), sadece çukurların daha yoğun bir şekilde yerleştirilmiş olduğu CD-ROM'lardır. Hızı 12x'dir, bu da Satürn'ün 2x CD okuyucusuna kıyasla *oldukça iyidir*.

### Çevrim içi platform

Dreamcast, oyunların çevrim içi oyun için çevirmeli bir hizmeti 'aramak' için kullanabileceği bir **modem** modülü yüklü olarak gönderildi. Sega iki hizmet sağlıyordu: **SegaNet** (Amerika ve Japonya'da kullanılan) ve **Dreamarena** (Avrupa'daki muadili).

Oyuncular, bazı oyunlarla birlikte verilen ekstra bir disk olan **DreamKey'i** kullanarak bir hizmete kaydoldular. DreamKey bir hesap açmak için bir web tarayıcısı sağlıyordu. Başlangıçta, DreamKey bölgeye bağlı olarak önceden yapılandırılmış bir hizmet olarak geldi, ancak daha sonraki revizyonlar kullanıcıların herhangi birine bağlanmak için ISS ayarlarını değiştirmelerine izin verdi.

Ayrıca, kullanıcının internette *PC tarzı* sörf yapmak istemesi durumunda satın alınabilecek Dreamcast markalı bir klavye ve fare de vardı.

Ne yazık ki, SegaNet ve Dreamarena piyasaya sürüldükten iki yıl sonra durduruldu. Bu nedenle, bu tür hizmetler ekstra araçlar kullanılarak taklit edilmediği sürece (DreamPi gibi, bir kullanıcı topluluğu tarafından tutulan sunucuların yardımıyla bunları kopyalayan bir Raspberry Pi görüntüsü), yalnızca bunlara dayanan oyunlar kullanılamaz hale geldi.

### Etkileşimli hafıza kartı

Dreamcast'in bir diğer yenilikçi özelliği de <strong x-id=“1”>Visual Memory Unit</strong> ya da 'VMU' idi. Kontrol cihazına takılıdır ve bir bellek kartı olarak hizmet etmenin yanı sıra, [@games-vmu] içeren tam teşekküllü bir cihazdır:

![VMU kontrolcüden ayrılmış.](vmu.png){.tabs-nested .active .open-float .tab-float title="VMU"}

![VMU takılı olmayan kontrolör.](controller.png){.tab-nested title="Ayrılmış"}

![VMU takılı kontrolör.](controller-vmu.png){.tabs-nested-last title="Ekli"}

- Bir **Sanyo LC86K87**: 8 bitlik düşük güçlü bir CPU.
- Dört ek simgeye sahip bir **32x48 Tek Renkli LCD:** Çerçeve tamponu olarak 196 B XRAM (harici RAM) kullanılarak komut verilir.
- **İki seri konektör**: Biri IN ve diğeri OUT için.
- **Altı fiziksel düğme**: VMU kontrol ünitesinden ayrıldığında kullanılır.
- **16 KB'lık bir Maske-ROM**: BIOS-IPL'yi depolar.
- **64 KB Flash**: 32 KB tek bir programı saklamak için (konsoldan aktarılan) ve diğer 32 KB Dreamcast'in kayıtlarını tutmak için.
- **512 B RAM**: 256 B sistem için ayrılmıştır ve program için yalnızca 256 B kullanılabilir.

{.close-float}

VMU'nun iki çalışma modu vardır:

- **Kontrol cihazına takılı**: Resmi kontrol cihazında VMU'ları ve aynı şekle sahip diğer aksesuarları bağlamak için iki yuva vardır, VMU ilk yuvaya takılırsa (kontrol cihazının önünden görülebilir), oyun sırasında çizimleri görüntüleyebilir. Ayrıca Dreamcast, VMU'da kayıtları ve bir programı saklayabilir.
- **Kumandadan ayrılmış**: Aygıt, saati, kayıt yöneticisi olan Tamagotchi benzeri bir cihaz haline gelir ve Dreamcast'in daha önce aktardığı programı da çalıştırabilir. İçerik paylaşmak için iki VMU da bağlanabilir.

## Korsanla Mücadele ve Homebrew

Tescilli GD-ROM formatının kullanılması, oyunların izinsiz kopyalarının üretilmesini (ve diğer konsollarda çalıştırılmasını) engellemeye yardımcı oldu. Dreamcast oyunları aynı zamanda *bölge kilitlidir,* yani bir konsol farklı bir bölge için tasarlanmış bir oyunu çalıştırmayı reddedecektir.

### Korsan Korumasını Baypaslamak

Uygulamada, Sega'nın büyük bir arka kapıyı açık bırakması nedeniyle korsan karşıtı önlemler *tamamen işe yaramazdı:* **MIL-CD**. Music Interactive Live-CD ya da 'MIL-CD' Sega tarafından bir Audio-CD'yi interaktif programlarla genişletmek için yaratılmış bir formattır... ve Dreamcast bununla uyumludur [@anti_piracy-history].

Sonunda, yetkisiz ticari diskler (hile yükleyiciler, film oynatıcılar, vb.) Sega'nın onayı olmadan konsolda çalışmak için MIL-CD'ler olarak gizlendi. Daha sonra, farklı bilgisayar korsanlığı toplulukları bu açığı inceledi ve CD-ROM'ları kullanarak korsan oyunları önyüklemek için geçici bir çözüm buldu. Bu da internette durdurulamaz bir ISO dalgasının yayılmasına neden oldu.

Sonrasında bazı sorunlar ortaya çıktı: GD-ROM'lar bir gigabayt veri depolayabilmesine rağmen, CD-ROM'lara sadece ~700 MB sığabiliyordu, peki 'ripper'lar daha büyük oyunları CD'ye sığacak şekilde nasıl küçültebilirdi? Sığdırana kadar müzik ve grafikleri yeniden sıkıştırarak. Hatta iki diske bölmeyi bile deneyebilirler. Sonuçta, oyun verileri artık tek bir blob değil (eski bir kartuştaki gibi), artık hiyerarşik olarak dosyalar ve dizinler halinde düzenleniyor.

## Hepsi bu kadar

![Buraya bir sürü şey yazabilmek için almak zorunda kaldığım bir Dreamcast.<br>Yaşına göre çok da kötü değil!](folks.png)

Umarım makaleyi okumaktan keyif almışsınızdır. Yazıyı üniversitedeki son yılımın başlangıcında bitirdim.

Muhtemelen bundan sonra çok meşgul olacağım, ancak bu makaleleri yazmaktan keyif alıyorum, bu yüzden umarım bir sonraki makaleyi birkaç hafta içinde alırsınız!

Bir dahaki sefere kadar!  
Rodrigo
