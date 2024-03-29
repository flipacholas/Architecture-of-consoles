---
short_title: PlayStation 2 Mimarisi
long_title: PlayStation 2 Mimarisi
long_name: PlayStation 2
name: PS2
subtitle: Kalanları gölgede bırakıyor
date: 2020-04-08
release_date: 2000-03-04
javascript:
  - 'threejs'
generation: 6
top_tabs:
  Model:
    file: international
    caption: "Orijinal PlayStation 2.<br>Piyasaya sürülme; 04/03/2000'de Japonya, 26/10/2000'de Amerika ve 24/11/2000'de Avrupa"
  Motherboard:
    caption: "Sadece Japonya'da pisayasa sürülen SCPH-10000 Modelinin 'GH-001' revizyonu gösteriliyor.<br>Aldığımız bağışlar sayesinde, bu modeli satın alıp, Çiplerin büyük çoğunluğunu tespit edebilmemi sağlayan bir fotoğraf çekebildim.<br>Sağ alt köşedeki çipin 4 MB BIOS ROM olduğunu tahmin ediyorum"
    bib_source: copetti
  Diagram:
    caption: "Orijinal dizayn (revizyon 'SCPH-10000'de uygulanan hâli).<br>Her veriyolu için genişliği ve hızı üstüne yazılarak gösteriliyor.<br>Bu mimari birçok revizyona uğradı, aşağıda daha fazla detaylandırılacak"
#Historical
aliases:
  - /projects/consoles/playstation-2/
---

## Kısa bir giriş

PlayStation 2, neslinin en güçlü konsollarından biri değildi. Ne var ki diğer şirketlerin hayal bile edemeyeceği bir popülerlik seviyesine ulaşmayı başardı.

Bu makine, [Orijinal PlayStation](playstation)'ın basitliğine yakın bile değildi ancak neden [önceki karmaşık konsollar](sega-saturn)la aynı kaderi paylaşmadığını irdeleyeceğiz.

## {.supporting-imagery}

## İşlemci (CPU)

Bu konsolun tam kalbinde, **Emotion Engine** ya da 'EE' denilen, Sony tarafından dizayn edilen, **~294.91** MHz'de çalışan güçlü bir paket (çipler bütünü) görüyoruz [@cpu-rockin]. Çipset, birden fazla bileşeni içinde bulunduruyor ve bunlardan biri de Ana İşlemci (CPU). Geri kalan kısımları ise belirli görevleri hızlandırmak için CPU'nun kullanımındadır.

### Çipteki Lider

Ana işlemci çekirdeği, **MIPS R5900 uyumlu** bir işlemci fakat birçok gelişmiş özellikle beraber geliyor. Bu, konsol açıldığı andan itibaren talimat (CPU Instruction) yürütmeye başlayan ilk çiptir. İşlemci, aşağıda sıralanan özelliklere sahiptir:

- **MIPS III ISA**: 64-bit RISC talimat seti (Instruction Set). *Bir saniye, bana mı öyle geliyor, yoksa bu [rakip konsol](nintendo-64#cpu)da bulunanla aynı ISA mı?* Pek de öyle değil. Sony, **MIPS IV**'ten bazı talimatları (Prefetch/Önbelleğe alma ve Conditional Move/Koşullu Taşıma) ve beraberinde **multimedya talimatları** dedikleri kendi SIMD Uzantılarını ekleyerek ISA'yı (Instruction Set Architecture / Talimat Seti Mimarisi'ni) geliştirdi.
- **32 ekstra 128-bit CPU Kaydı (Register)**: Bir diğer geliştirme. Bunlar, multimedya talimatlarını işlemeyi daha iyi başardılar ve vektör işleme için de çok kullanışlılardır.
  - Bu işlemci kayıtlarına (register) 128-bit'lik bir veri yolu vasıtasıyla erişim sağlanırken, CPU'nun geri kalanı dâhilî 64-bit'lik veri yolunu kullanır.
- **2 yollu süperskaler**: İki talimata kadar paralel yürütme yapar.
- **24 KB L1 önbelleği**: Talimatlar için 16 KB ve veriler için 8 KB'a bölünmüştür.
  - Ayrıca bir **ön belleğe alma fonksiyonu** uygulayarak talimat ve verileri çağırılmadan önce önbelleğe alır. Bu işlem, hafızadaki hangi konumların daha sık ulaşıldığını belirleyen bir ilave devre sayesinde gerçekleşir.
- **16 KB Karalama Belleği (Scratchpad RAM)**: 'Hızlı RAM' olarak da bilinir.
- **Hafıza yönetim birimi**: Bellek erişimini sistemin geri kalanıyla koordine eder.

Çekirdek, 32-bit kayan noktalı sayılarla (C'de `float` olarak da bilinir) olan işlemleri hızlandıran bir **ayrılmış kayan nokta işleme birimi** ('COP1' olarak adlandırılır) ile tamamlanmıştır. Bu alışılmadık bir parçadır çünkü IEEE 754 standardını takip etmez ve bunun en bariz olanı `infinity` (sonsuzluk) değerinin olmayışıdır (bunun yerine `0` hesaplanır) [@cpu-krysto].

### Tanıdık bir hafıza seçimi

Emotion Engine'in yanında iki adet 16 MB RAM bloğu olmak üzere toplam **32 MB** ana hafızası vardır. Kullanılan hafızaya 16-bit veri yolu ile ulaşılıyor, türü ise  **RDRAM**. ([*dejavu!*](nintendo-64#memory-design))

![Emotion Engine'in bellek dizaynı. Tıkanıklığın nerede ortaya çıkacağını tahmin edebilirsiniz.](MemoryArch.png)

İlk başta, bunu duyması biraz hayal kırıklığı yaşatabilir zira bunun aksine Emotion Engine'in dahili veriyolu 128-bit gibi büyük bir genişliğe sahip. Ancak, RAM çipleri; iki çipi bağımsız iki 16-bit veriyolu (her çipe 1 veriyolu) ile bağlamak suretiyle elde edilen **çift kanallı mimariyi** kullanacak şekilde stratejik olarak konumlandırılmıştır. Ortaya çıkan kurulumla teorik olarak 3,2 GB/sn'lik bir hız elde edilir, dolayısıyla hafıza gecikmesinin bu konsolda bir sorun olmayacağından emin olabilirsiniz!

Emotion Engine'in köşesinde, ana hafıza ile Karalama belleği arasında ya da ana hafıza ile EE içindeki bileşenler arasında veri transferi yapan güçlü bir **Doğrudan Bellek Erişimi Kontrolcüsü** (DMA Controller) ya da 'DMAC' var.

Veri aktarımı 128-bit'lik öbekler halinde yapılır fakat işin ilginç kısmı: Her sekiz öbekte bir ana veriyolunun kilidi geçici olarak açılır. Bu, paralelde (10 adede kadar) diğer DMA aktarımları ya da İşlemci'nin (CPU) ana veriyolunu kullanabilmesi için kapı aralar. Bu *işleyiş biçimi (modus operandi)* için **dilim modu (slice mode)** denir ve bu DMA birimindeki birçok mevcut moddan biridir. Şunu aklınızda bulundurun ki dilim modu, ana veriyolundaki gecikmeleri azaltmayı, DMA transferi genelinde bir yavaşlama pahasına yapar.

### Geçmiş aksiliklerin önlemesi

İstesek de istemesek de Emotion Engine'in içinde meydana gelen trafiğin büyüklüğü sebebiyle, bu dizayn, sonunda **Birleşik Hafıza Mimarisi ya da kısa adıyla UMA**'nın getirdiği sonuçlardan muzdarip olacaktır. Şöyle ki: Birden çok bağımsız bileşen, ana hafızaya aynı anda eşirmeye çalışıp tıkanıklığa sebebiyet verecek. Aslında bu sorunu düzelmek için Sony, devamlı hafıza ihtiyacını şu yollarla hafifletti:

- İşlemciyi **çok sayıda önbellekle** donattı. Böylece, sadece zorunlu gereklilik durumlarında ana hafızaya erişilmesi gerekir.
    - Bu makalede bahsedilen önbellek/karalama belleklerinin %99'u bu sebepten mevcuttur.
- 128-Byte'lık **Geri Yazma Arabelleği (Write Back Buffer)** eklendi: [Yazma Biriktirme Borusu'na (Write Gather Pipe)](gamecube#ibms-enhancements) çok benzerdir ancak, %25'i dolana kadar beklemek yerine, ilk olarak veriyolunun durumunu (tıkanık mı açık mı) kontrol eder.

Bu, önbellekten yararlanabilecek uygulamalar için çok elverişli görünebilir ancak ya Görüntü Listeleri'ni (Display Lists) manipüle etme gibi önbelleği kullanmaması gereken işlemler ne olacak? Neyse ki İşlemci, **sadece ve sadece** Geri Yazma Arabelleğini kullanan **Önbelleksiz (UnCached) Mod** adlı bir diğer hafıza erişim moduna sahiptir. Böylelikle Önbelleği (*Önbellek kayıplarını*) düzeltmek için İşlemci döngüsü heba edilmez.

Dahası, **Önbelleksiz hızlandırılmış mod** da mevcut. Bu mod, hafızadaki ardışık adreslerin okunmasını hızlandırmak için bir arabellek ekler.

### Diğer ilginç parçalar

Aynı Emotion Engine paketi içinde **Görüntü İşlem Birimi (Image Processing Unit)** ya da 'IPU' denilen, **görüntü çözmeye (decompression)** yarayan bir diğer işlemci var. [MDEC](playstation#tab-2-3-motion-decoder)'nin halefi olan IPU, bir oyunun MPEG2 görüntülerini (movie) Ana İşlemciyi (CPU) meşgul etmeden çözmesi gerektiğinde kullanışlı olabilir.

Uzun lafın kısası, oyun, sıkıştırılmış görüntü akışını IPU'ya (umarız DMA kullanarak) gönderir ve bu GPU'nun (Grafik İşlemci) ekranda gösterebileceği bir formata çevrilir. Ayrıca PS2'nin İşletim Sistemi de DVD oynatma için IPU'dan istifade eder.

Son olarak, IPU ayrıca sıkıştırılmış **Yüksek çözünürlüklü dokuları (Hi-res textures)** da işler. Bu da İşlemci (CPU) kullanımını ve büyük transferleri azaltır.

## Yardımcı İşlemciler (Co CPU'lar)

Rakipleri [son ürünlerini](dreamcast) piyasaya süreli iki yıl olmuştu. Önceki makalemizi okuduktan sonra bu makaleyi okumaya okumaya başladıysanız, tahminimce PS2'yi o zamanlarki gücüne kavuşturan 'o şeyi' görmeyi *hâlâ* bekliyorsunuz. Öyleyse, müsaadenizle, Sony'nin Emotion Engine'e sığdırdığı, *çok* önemli bir bileşen setini sizlere duyurayım: o güçlü **Vektör İşlem Birimleri (Vector Processing Units)** nâm-ı diğer 'VPU'.

### Mimari

Vektör İşlem Birimi, vektörleri ve bilhassa dört kayan noktalı (`float`) sayıdan oluşan vektörleri işlemek için tasarlanmış küçük bağımsız bir işlemcidir. Bu işlemciler o kadar hızlıdırlar ki **işlem başına tek döngü** harcarlar, bu ise vektör işleme için aşırı elverişlidir. Buna karşın CPU'nun FPU'suyla aynı standart dışı (IEEE 754'e uymayan) davranışı sergilerler.

VPU'lar aşağıdaki bileşenlerden oluşur:

- Birkaç **Vektör Birimi Hafızası (Vector Unit Memory)** ya da 'VU Hafızası': Vektör Birimi için çalışma alanı olarak kullanılır. Burada işlenmesi gereken değerler ya da önceki işlemlerin sonuçları depolanır.
- Bir **Vektör Birimi (Vector Unit)**: İşlemcinin çekirdeğidir. 'VU Hafızası'nda bulunan verilerin nasıl işlenmesi gerektiğini bildiren (**Mikroprogram** denen) bir yazılımı saklamak için biraz hafıza içerir (**Mikro Hafıza/Micro Memory** adı verilir).
  - Bir **64-bit ISA** uygular ve çalıştırma birimi ise **iki paralel alt birime ayrılmıştır**. Bunlardan biri, kayan noktalı sayılarla (float) çarpma ya da toplama, diğeri ise kayan noktalılarda bölme ya da tam sayılarda (integer) işlem yapar. Bu, kayan noktalı sayılar ve tam sayılarla **eş zamanlı olarak** işlem yapabilmeyi mümkün kılar.
- Bir **Vektör Arayüzü**: Ana hafızadan, Vektör Birimi'nin anlayabileceği bir formatta gelen vertex verilerinin (vertex data) otomatik olarak sıkıştırmasını açar (decompress eder). Ayrıca, bu birim mikroprogramları Mikro Hafıza'ya transfer edebilir.

### İşlevsellik

Vektör biriminin çalışması için 'başlatılması' gerekir. Ana İşlemci (CPU), bu iş için olan mikrokodu sağlamaktan sorumludur.

Emotion Engine'in içine konmuş **iki VPU** vardır ancak bunlar farklı şekilde ayarlanmış olup, farklı kullanım ve optimizasyonlara elverişlidirler.

#### Vektör İşlem Birimi 0 {.tabs.active}

![VPU0 Mimarisi.](VU0.png) {.tab-float}

İlk VPU yani **VPU0**, CPU ile diğer vektör birimi (VPU1) arasında konumlandırılmıştır. Ana işlemciye 'yardımcı' rolde bulunur.

VPU0, iki adet işlem moduna sahiptir:

- **Mikro Mod**: Bu mod 'geleneksel mod'dur. VPU, Mikro Hafıza'da bulunan bir mikroprogramın 'mikro talimatlarını' bağımsızca yürütecektir.
- **Makro Mod**: VPU0, CPU için 'COP2'ye (Yardımcı İşlemci 2'ye) dönüşür ve belli bir 128-bit veriyolu aracılığıyla CPU'dan alınan 'makro talimatlar'ı çalıştırır.
  - Makro talimatlar, micro talimatlarla aynı işlevselliğe sahiptir ancak farklı opcode'lar (assembly kodları) kullanır. Buna rağmen, VPU yürütme birimi artık ayrık değildir (yani aynı zamanda sadece 1 talimat yürütebilir).
  - Bu mod VPU0'ın tüm bileşenlerinden tamamen yararlanamasa da CPU'nun vektör işlemlerini hızlandırmaya devam eder. Üstelik, basitlik açısından, bir yardımcı işlemciyi programlamak, bağımsız bir birimi programlamaktan daha kolaydır (bilgisayar programlayıcıları bunu kullanışlı bulacaktır).

VPU0'ın hafıza haritası (memory map'i), ek olarak diğer VPU'nun bazı işlemci kayıtlarına (register) ve bayraklarına (flag) da erişebilir ki bu muhtemelen diğer VPU'nun durumunu kontrol etmek ya da gerçekleştirdiği işlemlerin sonuçlarını hızlıca okuyabilmek içindir.

#### Vektör İşlem Birimi 1 {.tab}

![VPU1 Mimarisi.](VUP1.png) {.tab-float}

Mevcut ikinci VPU, yani **VPU1** ise VPU0'ın dört katı mikro hafıza ve VU Hafızasıyla yükseltilmiş bir versiyonudur. Dahası, bu birim **Temel İşlev Birimi (Elementary Function Unit)** ya da 'EFU' adı verilen; üstel ve trigonometrik fonksiyonların yürütülmesini hızlandıran ilâve bir bileşen içerir.

VPU1 konum olarak, VPU0 ve (GPU'ya çıkan kapı olan) Grafik Arayüzü'nün arasında bulunur, bu sebeple, ana veri yolunu kullanmadan ve olabildiğince hızlı şekilde GPU'yu geometriyle beslemek için ekstra veri yolları içerir.

Öte yandan VPU1, konumu gereği, **sadece mikro modda çalışır**.

VPU'nun trigonometrik işlemler için dizayn edildiği çok açık ve GPU için bir ön işlemci olarak hizmet edebilir. Genellikle bu sebepten, meşhur Görüntü Listeleri'ni teslim etmekle yükümlendirilir.

### Sonsuz dünyalar {.tabs-close}

Bu birimlerden kullanışlı bir şekilde istifâde etme yöntemi de **prosedürel oluşturmadır**. Diğer bir ifadeyle, sahneyi, önceden kodlanmış geometri (yer ve nesne şekilleri) ile oluşturmak yerine, VPU'nun bunu algoritmalar kullanarak oluşturmasına izin vermektir. Bu durumda VPU, **geometriyi matematiksel fonksiyonlar hesaplayarak oluşturur** ve daha sonra bunlar GPU tarafından çevrilip (örn. üçgenler, çizgiler ve dörtgenlere), en nihayetinde sahneyi çizmek için kullanılır.

Kesin bir biçimde yazılmış verileri kullanmakla kıyaslandığında prosedürel içerik, paralelize edilmiş görevler için idealdir, çok az depolama gerektirir ve değişkendir (programlayıcılar parametrelerle oynayarak farklı sonuçlar elde edebilir) [@cpu-green]. Pek çok alan bu teknikten son derece yarar sağlayabilir:

- **Karmaşık yüzeyler** (örn. küre ve tekerlek).
- **Dünya işleme (render)** (örn. doğal yer şekilleri, partiküller, ağaçlar).
- **Bézier eğrileri** (bilgisayar grafiklerinde çok bilinen bir denklem olarak eğri çizmede kullanılırlar), bu eğriler bir **Bézier yüzeyine** (kesin bir geometridir) dönüştürülür ve gereken detay seviyesine (level of detail) göre farklı keskinlik derecelerini destekler.

Öte yandan prosedürel içerik, animasyonlarda sıkıntı oluşturabilir ve hattâ algoritma çok karmaşıksa VPU geometriyi (üç boyutlu şekli) gereken zamanda oluşturamayabilir.

Özetle, prosedürel işleme (procedural rendering) yeni bir teknik değil, ancak VPU'lar sayesinde daha fazla optimizasyon ve daha zengin grafik olanaklarına kapı aralıyor. Her şeye rağmen bu uygulaması kolay bir teknik değildir ve Sony Ar-Ge, konsollarında kullanılmak üzere farklı yaklaşımları izah eden bir çok belge yayınlamıştır.

### Gidişâta sen karar vereceksin

Bu yeni eklemelerle, programlayıcılar artık kendi grafik motorlarını tasarlamak konusunda oldukça esnek davranabilirler. Buna yardımcı olmak adına Sony, verimli boru hattı (pipeline) tasarımları ortaya çıkarıp bunları belgelemek için ekstra kaynak harcadı. Aşağıdakiler, farklı iş yükü çeşitleri için optimize edilmiş grafik boru hattı örnekleridir [@cpu-stokes]:

![Paralel boru hattı (pipeline) dizaynı.](Parallel.png){.tabs-nested .active title="Paralel"}

![Seri boru hattı dizaynı.](Serial.png){.tabs-nested-last title="Seri"}

Birinci örnekteki **Paralel** diazyn için; İşlemci, makro moddaki VPU0 ile takım hâlindedir ki VPU1 ile paralel olarak geometri işleyebilsin. CPU&VPU0 grubu, VPU1'in ana hafızadan veri aldığı yol olan ana veri yolunu kullanmaktan kaçınmak için, karalama belleğinden ve önbellekten tamamıyla istifade eder. Sonuç olarak, her iki görüntü işleme grubu da aynı anda kendi payına düşen Görüntü Listelerini GPU'ya gönderir.

İkinci örnekte görülen **Seri** dizayn ise, CPU&VPU0 grubunun VPU1 için bir ön işlemci olarak çalıştığı bir diğer yaklaşımı öne sürer. İlk aşama, geometrinin tümünü alıp işlerken, VP1 ise peşi sıra bunları Görüntü Listelerine dönüştürür.

Şu ana kadarkiler teorik açıdan örneklerdi ancak daha 'gerçeğe uygun' bir uygulanışı anlatması açısından, Jor Burton'un kendi yaptıkları PS2 oyunlarından birine ilişkin yayınladığı bir videoyu referans verebilirim [@cpu-burton].

![Crash Bandicoot: The Wrath of Cortex (2001). Partiküller, mumun alevini ve pencere camından sızan ışığı meydana getiriyor.](Crash.jpg) {.open-float}

Traveller's Tales'in eski yönetmeni, takımının nasıl tamamen VPU1'in içerisinde olan bir partikül sistemi oluştırmayı başardığını açıkladı. Kısaca anlatmak gerekirse, VPU1, önceden oluşturulmuş bir veri tabanını VU Hafızasından okumaya odaklandı. Bu veri tabanı, her an, başka bir bileşene ihtiyaç duymadan partiküllerin koordinatlarını hesaplamada kullanıldı. İşlemin sonucu doğrudan Görüntü Listelerine dönüştürülüp gönderilebildi.

{.close-float}

Bu yaklaşımla, CPU büyük ölçüde yükten kurtuldu ve AI (yapay zeka) ve fizik işleme gibi diğer işlemleri halledebildi.

Başka bir çok örnek anlatılabilir ancak işin özü: Şu halde en uygun düzeni oluşturmak programlayıcıya bağlı ve bu da iyi bir şey.

## Grafikler

Emotion Engine tarafından yapılan tüm bu işler göz önüne alındığında, geriye bir şey kaldı mı? İşte kalan son adım: Görüntüleme!

![Final Fantasy X (2001).](ffx.jpg) {.open-float}

Tam da bu iş için özelleştirilmiş, basit fakat hızlı bir çip mevcuttur: **Grafik Sentezleyici (Graphics Synthesizer)** ya da 'GS' ve bu **~147.46 MHz**'de çalışıyor. Görüntü işlemenin tümünü kendi içinde halledebilmek için **4 MB DDRAM**'i gömülü olarak içerir. Böylece, ana hafızaya erişim ihtiyacı ortadan kalkar. Gömülü RAM, ihtiyaç duyulan veri türüne göre farklı veri yollarıyla bağlanmıştır.

GS, bu sitede [daha önce ele anınan](gamecube#graphics) diğer grafik sistemlerinden daha az özelliğe sahiptir. Buna rağmen, yaptığı işte oldukça hızlıdır.

{.close-float}

### Mimari ve Tasarım

Bu GPU, sadece **rasterizasyon** yapar ve bu da... Pikselleri oluşturma, dokuları kaplama (texture mapping), ışık ve diğer diğer bazı efektleri uygulamadır. Bu demek oluyor ki vertex dönüşümleri yapamaz (bunlar VPU'larca üstlenilir). Ayrıca bu, sabit işlevli bir boru hattıdır yani [hayal gücünüze kalmış ince ayarlar](gamecube#creativity) çekilemez ya da [gölgelendiriciler (shader)](xbox#graphics) yoktur, sâbit bir gölgelendirme modeline saplanmış durumdasınız (bu model örn. Gouraud Shading'dir).

![Grafik Sentezleyici'nin boru hattı dizaynı](GS_Pipeline.png)

Oldukça basit görünüyor, değil mi? Öyleyse, daha derinlere dalıp her bir aşamada neler döndüğüne bakalım.

#### Ön İşleme {.tabs.active}

![Ön İşleme aşaması.](gs_pipeline/Preprocessing.png) {.tab-float}

Emotion Engine, Grafik Sentezleyiciyi, gömülü DRAM'ini gerekli malzemelerle (**Texture bitmap**'ler ve 'CLUT' olarak da bilinen **Colour Lookup Table**'lar ile) doldurarak tezelden başlatır, GS'yi onun işlemci kayıtlarına değerler atayarak yapılandırır ve son olarak GS'ye ekranın belirli konumlarına temel şekilleri (noktalar, çizgiler, üçgenler, sprite'lar, vb.) çizmesi talimatını veren çizim komutlarını (Görüntü Listeleri) gönderir.

Ayrıca, GS, ileriki hesaplamalarda gerekecek olan bazı değerleri de ön işler. En kayda değer olanı, çizimler sırasında interpolasyon için kullanılacak olan **Digital Differential Algorithm (Sayısal Türev Alma Algoritması)** değeridir.

#### Pikselleştirme (Rasterisation) {.tab}

![Pikselleştirme (rasterisation) aşaması.](gs_pipeline/Rasterizing.png) {.tab-float}

Önceden hesaplanmış olan verileri kullanarak, işleyici (renderlayıcı), temel şekilleri (çizgi, üçgen vb.) kullanarak pikselleri oluşturur. Bu birim eş zamanlı olarak 8 piksel (dokularla) ya da 16 piksel (dokular olmadan) oluşturabilir. Her piksel girdisi, aşağıda sıralanan hesaplanmış özellikleri içerir:

- **RGBA**: Kırmızı, Yeşil, Mavi ve Alfa (Opaklık) geçişlerine karşılık gelir. (İng. Red, Green, Blue, Alfa'nın baş harfleridir).
- **Z değeri**: Sonraki aşamalarda derinlik analizinde kullanılır.
- **Sis**: İsteğe bağlıdır, ortamda sis simüle etmeye yarar.
- **Doku Özellikleri**: Dokunun (texture) DRAM'deki adresini ve sonraki aşamada kullanılmak üzere dokunun (koordinat, level of detail yani detay seviyesi, filtre ve) sair özelliklerini barındırır.

Ayrıca çerçeve dışında kalan çokgenleri kesip atmak için **Scissoring Testleri** de gerçekleştirir (X, Y değerlerini baz alır) ve bazı piksel özellikleri daha ileriki kontroller için 'Piksel Testi' aşamasına intikal eder. Ayrıca, **Işıklandırma** da (İng. Lightning) mevcut iki seçenekle gerçekleşir: **Gouraud** ve **Flat** (yani Düz).

Paket, 'Doku Kaplama' (Texture Mapping) motoruna taşınır, ancak her özellik, ilgili iş için özelleştirilmiş bir 'alt motor' tarafından işlenir ve bu, farklı özelliklerin paralel olarak işlenmesine imkân verir.

#### Doku Kaplama {.tab}

![Doku kaplama aşaması](gs_pipeline/Textures.png) {.tab-float}

Bu aşama, her seferde 16'ya kadar pikseli işleyebilen geniş bir Piksel Birimi tarafından gerçekleştirilir, burada dokular çokgenlerin üstüne kaplanır (bunlar artık pikseldirler). Dahası, sis ve kenar yumuşatma (anti-aliasing) efektleri de burada uygulanır.

Doku haritaları, DRAM'in **Doku Arabelleği** olarak adlandırılan bir bölümünden getirilirler, kaldı ki bölümle de **Doku Sayfa Arabelleği** denilen ayrı bir yer sayesinde bağlantı kurulur ve bu, dokular için bir önbellekleme mekanizması görevinde gibi görünüyor. Ayrıca CLUT'lar da bu sayfa sistemiyle haritalanır. Her iki eleman da **512-bit veri yolu** kullanılarak taşınıp alınır.

Piksel Birimi, temel şekiller üzerine dokuları giydirirken **perspektif düzeltme** gerçekleştirir (önceki kosoldaki [affine mapping](playstation#tab-4-5-textures) yaklaşımına göre fevkalâde bir iyileştirme). Dahası, **bilineer ve trilineer filtreleme** de gerçekleştirebilir ki bunlardan ikincisi, mipmap'lenmiş dokularla beraber kullanılır.

#### Testler {.tab}

![Piksel Testi aşaması](gs_pipeline/Tests.png) {.tab-float}

Burada, belli pikseller, eğer birtakım gereksinimleri karşılamazlarsa atılırlar. Konusu açılmışken, bu işlem için aşağıdaki testler uygulanır:

- **Alpha Testi**: Bir pikselin alpha (transparanlık) değerini, onun 'standart' değeriyle kıyaslar. Sebebi ise bazı durumlarda, alpha değeri belli bir aralıkta olmalıdır ya da keyfî bir değerin altında/üstünde olmamalıdır.
- **Hedef Alpha Testi**: Bu, pikselin alpha değerini, onu frame buffer'a (çerçeve arabelleği'ne) çizmeden önce bir kez daha kontrol eder.
- **Derinlik Testi**: Pikselin Z değerini, [Z buffer'da (Derinlik Arabelleği'nde)](nintendo-64#modern-visible-surface-determination) karşılık gelen Z değeriyle kıyaslar. Buysa başka piksellerin arkasında kalarak görünmeyecek olan piksellerin işlenmesini önler.

#### Son İşleme (Post-Processing) {.tab}

![Son İşleme aşaması.](gs_pipeline/Postprocessing.png) {.tab-float}

Son aşamada yeni piksellerimize yerel DRAM'de bulunan önceki çerçeve arabelleği (frame-buffer) kullanılarak bazı efektler uygulanabilir:

- **Alpha Karıştırma**: Hafızadaki mevcut (işlenmiş çereveyi tutan) arabellekle bir öncekinin renklerini birbirine karıştırır.
- **Noktalı Tonlama (Dithering)**: Büyük RGBA değerlerinin kırpılması gerekir, ancak tonlama (dithering) uygulanarak keskinlik kaybı bir nebze giderilir.
- **Renk Kıstırma (Color Clamping)**: Alpha Karıştırma gibi işlemler uygulandıktan sonra, yeni RGB değerleri geçerli aralığın (0 ilâ 255) dışında kalabilir ve kıstırma da değeri bu aralığa atar.
- **Biçimlendirme**: Bu, boru hattında oluşturulup son aşamadan geçmiş çerçeve arabelleğini, hafızada saklanabilecek bir biçime dönüştürür.

Son olarak, yeni çerçeve arabelleği (yani ekrana gidecek çıktı), güncellenen yeni Z-buffer ile birlikte, **1024-bit veri yolu** üzerinden hafızaya yazılır.

### Daha fazla Son İşleme {.tabs-close}

GS'nin içinde, **Programlanabilir CRT Kontrolcüsü** ya da 'PCRTC' dedilen, ayrılmış bir bileşen bulunur ve bu, hafızadaki çerçeve arabelleğini, Video çıkışına gönderir ki televizyon ekranında görebilin. Ama hepsi bu da değil: Ayrıyeten, **Birleştirme Devresi** adlı özel bir blok içerir ki bu iki farklı çerçeve arabelleğini Alpha kaynaştırma yapabilir (Eğer oyun, yenisini şekillendirmek için önceki çerçeveyi tekrar kullanmak istiyorsa kullanışlıdır). Oluşturulan çerçeve, video sinyalleri yoluyla çıktı olarak verilebilir ve/veya hafızaya geri yazılabilir.

### İyileştirilmiş Modellemeler

Bahsettiğimiz bütün şeyler, beraberinde, hâlihazırda meşhur karakterler yenilenirken daha iyi dizaynlar getirecektir. Şimdi şu 'Öncesi & Sonrası'na bir bakın:

![PS1'deki Crash Bandicoot (1996).<br>732 üçgene sahip.](crash_ps1){.toleft model3d="true"}

![Crash Bandicoot: The Wrath of Cortex (2001).<br>2226 üçgene sahip.](crash_wrath_ps2){.toright model3d="true"}

Bunlar da yeni oyun serilerinden karakterler, tepeden tırnağa yüksek detay seviyeleriyle modellenmişler:

![Kingdom Hearts (2002).<br>2744 üçgene sahip.](sora_kh1_ps2){.toleft model3d="true"}

![Dragon Quest VIII (2004).<br>2700 üçgene sahip.](hero_ps2){.toright model3d="true"}

Bahse değer *Dragon Quest* gibi oyunlar, **Cel Shading** denilen ([daha önce bahsettiğim](gamecube#creativity) bir tabir), özgü bir ışıklandırma modeli uyguladılar, ancak önceki makalelerde açıkladıklarımda bundan sorumlu olan GPU idi. PS2'de ise, gerekli renk hesaplamaları muhtemelen Emotion Engine tarafından yapılıyor, çünkü GS, diğer GPU'lar kadar esnetilebilir değil.

### Video Çıkışı

Dah önce belirttiğimiz gibi PCRTC, çerçeve arabelleğini video sinyalleri aracılığıyla gönderir. Arayüz, çeşitli formatlar yoluyla video sinyalini yayınlayabilir (herhangi bir grafik bölgesinden Televizyonlar üzerinde çalışabilmesi için) [@graphics-opl]:

- **PAL**: 640x540 pikselde 50 Hz'e kadar, progressive (düz ilerlemeli taramayla 576p) ya da interlaced (boşluklu geçişli taramayla 576i) olarak gönderebilir.

<!-- PAL: Up to 720x540 in 576i mode. -->.
  - Pazarda 576p kullanan oyun yoktur.
<!-- The hardware does support 576p, but no games found in the market supported 576p. --> Bazıları progressive modu desteklese de 480p modda destekler.
- **NTSC**: 640x464 pikselde 60 Hz'e kadar, progressive (480p) ya da interlaced (480i) olarak destekler.

<!-- NTSC: Up to 640x464 in 480i mode. -->.
- **VESA**: 1280x1024 piksele kadar.
- **DTV**: Progressive modda 768x480 ya da interlaced modda 768x960 gibi *sayıca muazzam büyüklükte* piksele çıkabilir. <!-- <strong x-id="1">DTV</strong>: Up to the 
  
  *enormous amount* of 640x480 pixels in progressive mode or 1440x960 in interlaced mode. -.
  - PCRTC ayarlarında birtakım oynamalarla, bir oyun, 1080p (2048x1536) çıktı vermeye zorlanabilir. Ancak bu mod belgelenmemiştir ve bu sebeple öngörülemeyecek davranışlara gebedir.
  - Yani PS2, 'HD görüntü' verebiliyor mu? Teknik olarak... evet. Ancak, çoğu oyun stüdyosunun, o zamanlar yaygın olmayan bir format uğruna performansı riske etmiş olacağını sanmam.

<!-- Technically... no, but I don't think most game studios risked the performance penalty for a format that was bad. -->

![Konsolun arkadan görünümünün sağ kısmı [@photography-amos]. A/C yuvası, Dijital ses yuvası ve AV Çoklu Çıkışı görülebiliyor.](photos/ps2_back.png) {.open-float}

Seçilebilecek birçok mod var ancak 2000'lerin başlarında gerçekleşen format benimsemeleriyle hespi gözden düşüyor ve seçenekler sadece PAL ve NTSC'ye kadar daralıyor. Ayrıca, PAL her ne kadar NTSC'den daha yüksek çözünürlük sunsa da, NTSC oyunlarının bazı Avrupa versiyonlarında, kullanılmayan piksel satırlarını maskelemek için letterboxing'e sığınılmış ve 50 Hz sınırına uymak için ekran yenileme hızı yavaşlatılmış (ben bunlara 'kötü port' diyorum!) (ÇN: Dolayısıyla görüntü küçülüp, oyun yavaşlıyor).

{.close-float}

Video çıkış yuvası (**Multi A/V**) çok kullanışlıdır. RGB, Komponent (Component), S-Video ve kompozit (composite) sinyali taşır. Yani bütün önemli sinyaller, tescilli adaptörler ya da dâhilî modifikasyonlar gerektirmeden burada bulunuyor.

## Ses

Yeni ses çipi, eski [SPU](playstation#audio)'ya kıyasla marjinal geliştirmelere uğramıştır ve adı da... **SPU2**! Geliştirmelere **2 MB dâhilî hafıza** eklenmesi ve **kullanılabilir 48 ses kanalı** da (ki orijinalinin 2 katıdır) dâhildir.

SPU2, kendi içinde iki işlemciden oluşmuştur, bunlar **CORE0** ve **CORE1**'dir ve ~36.86 MHz'de çalışırlar. Her biri **24 kanal** işler.

İşin ilginç tarafı, sonuçta bunlar iki ayrı işlemcidir ve onları yapılandırmak için, onların özel işlemci kayıtlarında değişiklik yapmanız gerek. Fakat Sony, geliştiricileri, farklı işlemciye ait kayıt setlerinin 1/48000 saniye arayla değiştirilmesi gerektiği konusunda uyardı. Eğer çok acele ederseniz, SPU2'nin davranışları tahmin edilemez olur!

SPU2, orijinal SPU'da bulunan efektlerin aynısını miras alır. Kullanıma sunulan hafıza, bir 'çalışma alanı' olarak kullanılır: İşlenmemiş dalga biçimini depolayabilir ve onu işleyip efektler uygulamak için ektra alan ayırabilirsiniz.

Sonunda, çip, **stereo çıkış** hazırlamak için bütün kanalları karıştırır. Şimdi, gelelim ilginç kısma: SPU2, karıştırılmış stereo örneği yeni girdi olarak alarak kendini besleyebilir ve bu EE'nin ona (örneğin başka seslerle karıştırması için) erişmesine ya da daha fazla efekt (örn. reverb (yankı), eko, delay (gecikme) gibi) eklemeye devam edebilmesine olanak tanır.

![Kingdom Hearts II (2005). Yankı (reverb) olmadan.](goomy_noreverb){.toleft video="true"}

![Kingdom Hearts II (2005). Yankıyla.](goomy){.toright video="true"}

### Ses çıkışı

Ses sinyali çıkışı, iki ortam aracılığıyla sağlanır:

- **Dijital Ses**: Sony/Philips Dijital Arayüzü (Interface) ya da 'S/PDIF' olarak bilinir.
- **Analog Ses**: Dijital'den analog'a çeviriciden geçip Multi A/V çıkışında sona erer.

## Giriş/Çıkış

PS2'nin Giriş/Çıkışı karmaşık değildir, buna rağmen konsolun çok sayıda revizyonuyla, çeşitli dahilî ve haricî arayüzler değişikliğe uğramıştır.

Başlangıç olarak, farklı birimler arsındaki iletişimi sağlayan ayrılmış bir işlemci bulunur ve bu işlemci, **PlayStation 1**'de bulunan [orijinal MIPS R3000 tabanlı çekirdekten](playstation#cpu) başkası değildir. Bu seferki adıysa **IOP** ve 37.5 MHz'de çalışıyor ve bir 32-bit veri yolu kullanıyor [@io-buses].

IOP, **Sistem Arayüzü (System Interface)** ya da 'SIF' denilen özelleştirilmiş bir Giriş/Çıkış arayüzü kullanarak Emotion Engine'le iletişim kuruyor ve iki uç da birbirine veri transferi yapmak için kendi DMA birimlerini kullanıyor. Ayrıca IOP, bir arabellek olarak kullanılmak üzere kendi hafızasını içeriyor.

IOP, ön dıştaki yuvalara, DVD kontrolcüsüne, SPU2'ye, BIOS ROM'a ve PC card yuvasına erişim sağlıyor.

<!-- The IOP gives access to the front ports, CD-ROM controller, SPU2, the BIOS ROM and the PC card slot. -->

### Miras alınan uyumluluk

Orijinal CPU'yu dâhil etmek suretiyle bir şekilde PS1 uyumluluğu sağlanabileceğini düşünebiliriz. Buna uygun şekilde IOP, PS1 CPU'sunun yardımcı sistemini oluşturan diğer bileşenleri de içeriyor. Dahası çekirdeğe, PS1 hızında çalışacak şekilde hız düşürme yapılabiliyor. Maalesef SPU2, PS1'e karşın çok fazla değişikliğe uğradı fakat bunun için de Emotion Engine, eski SPU'yu taklit (emüle) etmek amacıyla 'yeniden işlevlendiriliyor'.

Konsolun sonraki revizyonlarında IOP, bir **Power PC 401 'Deckard'** ve **4 MB SDRAM** ile (öncekinden 2 MB daha fazla) değiştirildi, geriye dönük uyumluluk devam etti ancak bu sefer yazılım yoluyladır.

### Mevcut arayüzler

Konsol, orijinal PlayStation'da bulunan eski ön dış bağlantı noktalarını barındırdı ve ayrıca ilk bakışta ümit verici görünen birtakım 'deneysel' arayüzler de sundu.

![PS2'nin önü, Kontrolcü ve Hafıza Kartı (MemoryCard) dâhil yaygın yuvalar gösteriliyor, ayrıca yeni USB'ler ve i.Link de [@photography-amos].](photos/ps2_front.png) {.open-float}

En meşhur ilave ise: **İki adet USB 1.1 yuvası**. Büyük ölçüde üçüncü parti eklentiler tarafından benimsenildi ve bunlar, gelecekteki tüm revizyonlarda da bulunmaya devam etti.

Peki ya 'deneysel' olanlar? İlk olarak, başlangıçta bir dış **i.Link portu** yuvası bulunuyordu (ayrıca IEEE 1394 ya da Apple camiasında 'FireWire' olarak da bilinir). Bu yuva, yerel çok oyunculuyu mümkün kılmak için iki PS2'yi bağlamada kullanıldı ancak üçüncü revizyonla kaldırılı (muhtemelen yerini 'Network card'a bıraktı, aşağıda daha fazla detay var).

{.close-float}

Konolsun arkasında, **PC card**'lar için de bir yuva var, Sony'den iki ekstra yuva getiren 'Network Adaptor card' alabiliyordunuz. Biri Ethernet kablosu bağmak için, diğeri de bir özel ürün bağlamak ve yine Sony tarafından satılan bir harici 'Hard Disk Sürücüsü Birimi' (HDD) takmak içindir. Bir sürücüye sahip olmak, daha hızlı yükleme süreleri için oyunların geçici verileri depolamasına (ya da kalıcı olarak burada kurulmalarına) imkan tanır. Gerçi bu özelliği sadece birkaç oyun kullanmıştır.

![PS2'nin arkasındaki Sabit disk bölmesi (kapağı çıkarılmış olarak) gösteriliyor [@photography-amos].](photos/back_bay.png){.tabs-nested .open-float .tab-float .active title="Giriş Bölmesi"}

![Ağ adaptörünün (Network adaptor) önden görünümü [@photography-amos]. Bu özel model, modem ve Ethernet girişleri barındırdı.](photos/harddrive_adaptor_front.png){.tab-nested title="Ön Yüz"}

![Ağ adaptörü (Network adaptor) arkadan görünüşü [@photography-amos], bir sabit disk takılmış hâlde.](photos/harddrive_adaptor_back.png){.tab-nested title="Arka"}

![Slim modelinin arkasındaki sabit Ethernet yuvası gösteriliyor.](photos/ps2_slim_back.jpg){.tabs-nested-last title="İnce Yüz"}

Sonraki revizyonlarda PCMCIA yuvası, konsola 3.5" (inçlik) bir Hard disk sürücüsü sığdırılabilecek bir **Expansion Bay (Genişletme Bölmesi)** ile değiştirildi. Öncelikle, sadece Modem ve/veya Ethernet girişlerini (modele göre değişir) bulundurmakla kalmayıp aynı zamanda bir ATA-66 hard disk için gerekli bağlantıları da bulunduran bir **Network adaptor** almanız gerekliydi. 'Slim' revizyonlarıyla bu özellik tamamen kaldırıldı ancak arkada kalıcı olarak bulunacak bir Ethernet yuvası bırakıldı. Buna ek olarak, yeni revizyonla yeni bir ön yuva olan **kızılötesi sensör** eklendi.

{.close-float}

Donatılmış olan Ethernet alıcı/vericisi, 100 Mbps'ye (12.5 MB/s) kadar transfer hızlarını destekler. Ancak **gözlemlenen hız düşüklüğü kötü şöhretle bilinir** (bazı durumlarda 2 MB/s'ye kadar düşer). Bunun açıklaması ise görece basittir: Kullanılabilir iletişim ağına (network) erişmek için standart 'OSI Modeli'nin tüm katmanları uygulanmalıdır, alıcı/verici ise bütünün sadece bir parçası. İşin geri kalanı genel olarak IOP'nin emrindedir (ki yazılımla yapılır) fakat IOP'nin sınırlı performansı yüzünden [@io-bottleneck], darboğaz meydana gelir.

### Etkileşimli eklentiler

Kontrolcülerinin (oyun kolu) yeni versiyonu olan **DualShock 2**, DualShock'un biraz geliştirilmiş versiyonudur.

![DualShock 2 kontrolcüsü [@photography-amos].](photos/dualshock2.png){.tabs-nested .open-float .tab-float .active title="DualShock 2"}

![Orijinal Hafıza Kartı (Memory Card) (8 MB modeli) [@photography-amos].](photos/memorycard.png){.tabs-nested-last title="Hafıza Kartı"}

Orijinal PlayStation zamanlarında, orijinal kontrolcünün farklı özellikler barındıran birçok revizyonu piyasaya sürüldü (aynı zamanda pazara ayrışma getirdi). Artık geliştiriciler lehine, öncesinde gelen bütün özellikleri kendinde toplayan tek bir kontrolcü vardır.

DualShock'la kıyaslandığında yeni versiyon ince bir yeniden dizayn geçirdi, iki analog çubuğu ve iki titreşim motorunu sırasıyla daha zengin girdi alma ve etkili geri bildirim verme adına ekledi.

{.close-float}

Kontrolcü giriş yuvasının yanındaysa PS1 ve PS2 kartlarıyla uyumlu **Hafıza kartı yuvası** bulunuyor. Yeni kartlara, **MagicGate** olarak anılan güvenlik sebepleriyle ekstra devre gömüldü, bu da oyunların, farklı hafıza kartlarına veri transferini engelleyebilmesine imkan tanıdı.

## İşletim Sistemi

Anakartın üstüne yerleşmiş, kullanıcıların etkileşime geçebileceği bir shell (sistem kullanıcı arayüzü) menüsü açmak için muazzam miktarda kodu depolayan bir **4 MB ROM** çipi bulunur, ancak buna ek olarak Giriş/Çıkış erişimini kolaylaştırmak için oyunların dayandığı sistem çağrılarını (system call) da gerçekleştirir.

![Konsol başlatıldıktan sonra görünen açılış animasyonu.](bios/animated.jpg){.tabs-nested .active title="Önyükleme animasyonu"}

![Geçerli bir PS2 oyunu takıldıktan sonra PS2 logosu gösteriliyor.](bios/game_splash.jpg){.tabs-nested-last title="Geçerli disk"}

Önyükleme ardından CPU, ROM'daki talimatları ve üzerine şunları çalıştırır:

1. Donanımı ilk kullanıma hazırlar.
2. RAM'e bir **Kernel (sistem çekirdeği)** açar, bu ise sistem çağrılarıyla ilgilenir ve de çoklu iş parçacığı (multi-threading) desteği sağlar (iş birlikçi ve öncelik tabanlıdır).
3. IOP işlemcisini başlatıp, ona **modüller** gönderir; bu, IOP'nin konsolun donanımını yönetmesine izin verir. Son olarak, IOP bir 'komut bekleme' hâline geçirilir.
    - Modüllerin kullanılması, Sony'nin IOP'yi değiştirmeden yeni PS2 donanım revizyonları yayınlamasına imkân sağladı, bu da üretim mâliyetlerini düşürdü.
3. Açılış animasyonu ve shell menüsünü gösteren program olan 'OSDSYS'i açar.

### Etkileşimli kabuk (Interactive shell)

PS2 kabuğunun (PS2 shell) işlevselliği, diğer 6. nesil konsollarla hemen hemen aynı düzeydedir.

![İlk gösterilen menü. Herhangi bir disk yerleştirilmediğinde ortaya çıkar.](bios/menu.jpg){.tabs-nested .active title="Menü"}

![Hafıza Kartı tarayıcısı.](bios/mem_list.jpg){.tab-nested title="Tarayıcı"}

![Kayıt tarayıcısı. Bir hafıza kartı seçildiğinde ortaya çıkar.](bios/save_list.jpg){.tab-nested title="Kayıtlar"}

![Kayıt düzenleyici, bir kaydın seçiminden sonra gösterilir.](bios/save_editor.jpg){.tab-nested title="Düzenleyici"}

![Sistem Yapılandırma.](bios/options.jpg){.tabs-nested-last title="Seçenekler"}

Kabuk, gündelik işlemleri gerçekleştirmeyi sağlayan kullanışlı kısımlar sunar, örneğin hafıza kartındaki kayıtların düzenlenmesi gibi. Bununla birlikte, bazı fevkalade seçenekler de sunar, meselâ geçerli video modunu değiştirmek gibi.

## Oyunlar

Bu sistemin 2000'ler boyunca ulaştığı şöhret seviyesinin emsâli görülmemiştir, vadesini doldurduğunda (2013'te, çıkışından 13 yıl sonra!) oyun kütüphanesi 1850 oyunla doluydu [@games-antista].

![Mr Moskeeto (2001). Ne zaman biri PS2 oyunlarının bolluğundan dem vursa, bunu hatırlarım.](mr_moskeeto.jpg) {.open-float}

Burada gerçekleşenler etkileyicidir. PS2, 'programlayıcı dostu' bir mimariye sahip değildir (bir PC programcısının bakış açısından görüldüğü gibi), ancak bu kadar çok sayıda oyun geliştirildiğinde, ben de daha fazla faktörün dahil olup olmadığını merak ediyorum (meselâ 'lisans indirimleri', düşük dağıtım maliyetleri, geliştirme maliyeti, küçük form faktörü vesaire).

{.close-float}

### Geliştirme ekosistemi

Sony, oyun geliştirilmesine yardımcı olacak donanım ve yazılımı temin etti.

Yazılım tarafında, aşağıdakileri içeren **PlayStation 2 SDK** (PS2 Yazılım Geliştirme Kiti) mevcuttur [@games-sdkkit]:

- Emotion Engine toolchain (araç seti): Bir **C** ve **C++** derleyicileri (compilers), işlemci talimatı derleyicileri (assemblers), bağlayıcılar (linkers) ve EE'nin bütün bileşenlerini kontrol etmek için hata ayıklarıcılar (debuggers) setidir. Ana İşlemci (CPU) temelde C/C++ kullanılarak programlandı ancak vektör birimleri gibi performans bakımından kritik bileşenler assembly (mikrokod/makrokod) kullanılarak programlandı.
  - Pakete ayrıca, gerçek donanıma göndermeden kodu yaklaşık olarak test edebilen bir 'Emotion Engine simülatörü' de dahildi, gerçi simülatör fiziksel EE çipi kadar doğru değildi.
  - Bu araçların tümü Linux, Solaris ve Windows'ta çalışır. Daha sonraki varyantı Cygnus ortamında çalıştı.
- Düşük seviye kütüphaneler (Low-level libraries): Birçok sistem fonksiyonu için arabirin oluşturur (BIOS çağrıları / BIOS call kullanarak).
- Kullanım performansını gösteren 'Analiz' (Analysis) araçları.
- Resmî geliştirme donanımıyla bağlantıyı sağlayan ilâve araçlar.

Donanım tarafında Sony, oyunları kurum içi çalıştırma ve hata ayıklama için stüdyolara mahsus donanım sağladı. İlk geliştirme kitleri (devkits), PS2'nin pisayasa sürülmemiş donanımını replika etmek için birarada yığılmış çıplak (kasasız) kartlardı. Sonraki kitler (**Development Tool** yani Geliştirme Araçları adını alanlar), daha tatmin edici bir görünüşe, gelişmiş Giriş/Çıkışa (I/O) ve oyunları aynı şartlarda geliştirip dağıtma için PS2 donanımıyla çalışan bileşik iş yeri donanımına (RedHat 5.2 çalıştırır) sahipti.

Geliştirme kiti (DevKit), resmî SDK ve CodeWarrior (meşhur bir IDE yani bütünleşik geliştirme ortamıdır) birleşimi, kullanılan en popüler düzeneklerdendi.

### Medya Ortamı

Disk sürücü, hem DVD hem de CD okuyabilir yani oyunlar her iki fiziksel formatla da dağıtılabilir. Ancak bariz sebeplerden ötürü, çoğu oyunu DVD formatında bulacaksınız.

![Kingdom Hearts II (2005). Tipik perâkende oyun kutusu ve diski.](kh2_box.jpeg) {.open-float}

DVD'ler, (en yaygın DVD 'alt türü' olan) DVD-5 biçiminde **4.7 GB**'a kadar ya da DVD-9 için (çift katmanlı versiyonu, daha az yaygındır) 8.5 GB'a kadar veri tutabilir [@games-dvd]. Aslında üçüncü bir format olan çift taraflı DVD-10 da vardır ancak hiçbir oyun bunu kullanmadı.

Kullanılan medya türü sayesinde, sadece oyunlar değil, filmler de oynatılabiliyordu. Yine de, film formatında bir DVD'yi okuyabilmek için ilgili kod çözücü gerekiyordu ve bunun için, PS2 ilk zamanlarda gerekli dijital aracı bir hafıza kartına (PS2 Memory Card) dahil etmişti (sonuçta hafıza kartı da bir depolama medyası/ortamıdır) fakat sonraki modeller ise BIOS ROM'a halihazırda kurulmuş bir DVD yazılımı ile çıktı.

{.close-float}

Hızlarına gelince, CD-ROM'lar 24x hızda (yani 3.6 MB/sn) okunuyordu ve DVD-ROM'lar 4x hızda (5.28 MB/sn) okunuyordu [@games-transfer].

### Ağ hizmeti

Görmüş olduğunuz üzere, konsolun genel ağ özellikleri, ilk piyasaya sürülüşünden dört yıl sonraki revizyonlara kadar standarda kavuşmamıştı. Benzer şekilde, eğer oyun stüdyoları çevrim içi hizmetler (çok oyunculu gibi) sağlamak istiyorlarsa, gerekli altyapıyı sağlamakla yükümlü idiler. Sonraki yıllarda Sony, **Dinamik Ağ Kimliği Doğrulama Sistemi (Dynamic Network Authentication System)** ya da 'DNAS'ı işleve soktu, bu çevrim içi bir hizmet değildi fakat korsan oyunların çevrim içine bağlanmasını engelleyen bir doğrulama sistemiydi.

### Alışılmadık bir oyun türü

Sony, *güzel grafikli* tüm bu oyunların yanı sıra, 'Kondara' tabanlı (ki bu da Red Hat 6 tabanlıdır) bir Linux dağıtımını iki CD halinde (ilk disk 'Runtime Environment' yani program çalıştırma ortamı ve ikincisi 'Software Packages' yani yazılım paketleridir), VGA adaptörü, USB Klavye ve Fare ve ek olarak bazı geliştirici kılavuzları ile birlikte piyasaya sürdü. Paket, **Linux Kit** olarak bilinirdi ve ilk DVD'yi başlatarak OS'i (işletim sistemini) çalıştırabilir ve herhangi bir *old school (eski usul)* Linux ortamı gibi devam edebilirdiniz. Tabii ki Linux dağıtımını kurmak için konsola bir sabit disk (Hard drive) takmanız gerekirdi. Kurulduktan sonra, bu işletim sistemini başlatmak için her zaman ilk DVD'ye ihtiyaç vardı.

Linux Kit'e, EE'ye yönelik (glibc 2.2.2 ile gcc 2.95.2) derleyiciler (compilers), vektör birimlerine yönelik talimat derleyiciler (assemblers) ve beraberinde Grafik Sentezleyici'de hızlandırılmış bir pencere sistemi (XFree86 3.3.6) [@games-linux]. Genel anlamda bu, kulağa ilginç bir ortam gibi geliyor. Aslında, okuduğum araştırma makalelerinin biri bu kurulumla oluşturulmuştu.

## Korsanla Mücadele ve Ev Yapımı (Homebrew)

Bu alanda konuşulacak çok şey var, hadi DVD okuyucuyla başlayalım, olur mu?

### Kopya Koruma

Bu kısım bilhassa oyun stüdyolarını endişeye düşürüyordu zira bu konsol, oyunları depolamak için pek uygun fiyatlı bir disk biçimi kullandı ve bu da hayli yüksek bir korsana düşme riski getirdi.

![Bu ekran, sürücü arızalıysa görülebilir... ya da korsan bir kopya takıldıysa.](bios/rsod.jpg) {.open-float}

İşletim sistemi, bir oyunu açma işini DVD okuyucuya özel komutlar göndererek yapar. Özel olarak oyun içeriğini okumak için kullanılan komutlar, standart DVD komutlarından (yani DVD filmi okunmasından) çok farklı davranır. Tescilli oyunların, diskin daha içeri kısımlarında, dosya sisteminin (filesystem) adını, konumunu ve boyutunu işaretleyen ve erişim alanının dışında kalan bir 'yerleştirme dosyası' (map file) bulundurduğu ortaya çıktı. DVD'den bir oyun diskini okuması istendiğinde, diskte her zaman yerleştirme dosyası (map file) yardımıyla yön bulur; bu da demektir ki yerleştirme dosyasını bulundurmayan bir korsan kopyanın okunması imkânsızdır. Bu, başka bir bölgenin konsolunda çalışan ithal oyunların çalıştırılmasını engelleyen bir bölge kilidi sistemiyle bütünleşmiştir.

{.close-float}

### Keşfedilen açıklar

Bu konsola dair en önemli bölümü açıkladık, şimdi de koruma mekanizmalarını atlatmayı başaran keşfedilmiş birkaç yönteme göz atalım.

#### Modifikasyon Çipleri (Modchips) {.tabs.active}

Kendi neslinde (ve öncekilerde) disk tabanlı sistemler kullanan diğer konsollardaki gibi, DVD yardımcı sisteminin üçüncü taraf şirketlerce tersine mühendisliğe uğraması an meselesiydi. Buradaki hedef, sürücüyü, erişim alanı dışındaki yerleştirme dosyasını (map file) kullanmayı gerektirmeden dosya sisteminde gezinmeye zorlayacak kullanışlı bir açık yakalamaktı.

Sonunda bu, aynı zamanda bölge kilidi kısıtlamalarını da askıya alan **modchipler** şeklinde gerçekleşti.

#### Hileler {.tab}

Takılması için lehim kabiliyeti gerektiren modchiplerin yanı sıra, gayriresmî fakat 'orijinal' diskler pazarda yerini aldı. Bunlar, bölge kilidini aşmayı ve işletim sistemine yama yaparak oyun içi hileleri kullanmayı mümkün kıldı. Dahası, 'hile diskleri' (cheat discs), konsolun modifiye edilmesini gerektirmeme avantajına da sahipti. Düşünceme göre bu bahse en iyi örnek *CodeBreaker*'dır.

#### Disk değiştirme {.tab}

Son gelişmeler arasında bir hileli teknik daha ortaya çıktı. Bu sefer, okuyucunun hatalı sektörlere müdahale davranışından faydalanılıyor. **Swap Magic** bir diğer 'orijinal' disk gibi görünüyor ancak içindeki 'oyun' DVD'ye kasten yapılmış bir hatalı sektördeki, aslında var olmayan bir çalıştırılabilir (executable) dosyayı okumasını söylüyor, böylece sürücüyü büsbütün donduruyor [@anti_piracy-hacking]. Kullanıclar için mevcut diski 'orijinal' olmayan bir diskle değiştirebilecekleri bir fırsat penceresi aralanıyor. Sonrasında, hâlen hafızada bulunan Swap Magic, yeni diskin ana çalıştırılabilir dosyasını önyüklüyor ve sonunda gerçek bir oyun açılıyor. Tüm bunlar, sürücü hâlâ orijinal bir diskin takılı olduğunu zannederken gerçekleşiyor.

Bu işlem, illâki konsolda bir değişiklik yapılmasını gerektirmez. Fakat modele bağlı olarak, sürücünün disk çıkarma sensörünü bloke edecek şekilde, PS2'nin dış kasasının kurcalanması gerekebilir. Bazı durumlarda, belli yerlere pamuk yerleştirmek iş görecektir.

#### PS1 taşması (overflow) {.tab}

PS2, PS1 oyunlarının emülasyonunu optimize ederken kullanılacak bilgileri içeren, `TITLE.DB` adı verilen bir veri tabanı dosyasını Hafıza Kartında (MemoryCard) saklar [@anti_piracy-grand]. İşletim sistemi, bir PS1 oyunu yerleştirildiğinde veri tabanı dosyasını getirir ve bütün dosyayı hafızadaki sabit bir adrese açar (*ihmâl bir*). Bilgi derleyici, C'de dizileri (harf ve diğer karakterler zinciri) bir yerden ötekine kopyalamaya yarayan **`strncpy()`** fonksiyonu kullanılarak uygulandı.

C'ye aşina olanlarınız, muhtemelen nereye varacağımı tahmin ettiniz. Olay şu ki `strncpy()`, bir dizi ne kadar uzun bilmiyor, yani (zincirin sonuna `\0` yazmak suretiyle) bitirilmediği sürece kopyalama 'sonsuza dek' devam edecek (ve öngörülemeyen sonuçları olacak!). Şanslarına bu fonksiyon, tercihen kullanılabilen, kopyalanacak maksimum byte sayısını belirleyen bir parametre içeriyor ve böylece kopyayı arabellek taşmalarından koruyor. Fakat şu gülünç gelebilir ve öyledir ki **Sony, bu parametreyi kullanmadı**, hem de her veri tabanı girdisinin 256 byte'lık sabit bir boyuta sahip olmasına rağmen (*ihmal iki*).

RAM daha yakından incelendiğinde, TITLE.DB'nin, yürütülmekte olan mevcut fonksiyon bittikten sonra dönülecek adresi belirtmek üzere **kayıtlanmış bir işlemci kaydının,** `$ra`**'nın yanına** kopyalandığı görülür (*ihmal üç*) ve bu da <0>Bağımsızlık Açığına (The Independence Exploit)</0> yol açar: Büyük bir dizi içeren bir Title.db oluşturun, içine bir çalıştırılabilir dosya yerleştirin ve bu dizeyi öyle bir hazırlayın ki `$ra`'nın içeriği, bu çalıştırılabilir dosyayı işaretleyecek şekle dönüşerek hükümsüz kalsız. Eğer bu dosyayı (başka bir alet ya da PC USB adaptörü yardımıyla) kendi Hafıza Kartınıza (MemoryCard) aktarmayı başarırsanız, kendinize basit bir ev yapımı başlatıcısı (Homebrew launcher) hazırlamış olursunuz.

Slim revizyonu yayınlandıktan sonra, bu açık giderildi (*Nasıl oldu merak ediyoum*). İlginçtir ki bu, acemice kod meydana getiren son [falso](wii#the-fall-of-encryption) değildi.

### Yarı kalıcı bir yazılımsal kilit kaldırma {.tabs-close}

Bir süre önce, bu konsolun BIOS'unun Hafıza Kartı kullanılarak güncelleştirilebildiği keşfedildi, bu özellik uygulamada hiçbir zaman kullanılmadı ancak konsoldan da kaldırılmadı (en azından konsolun ömrünün büyük kısmı boyunca). Bundan yola çıkan hackerlar, eğer Hafıza Kartına özel bir yazılım kurmanın bir yolunu bulurlarsa, BIOS'un önyüklemede her zaman bunu çalıştıracağının farkına vardılar. Bu keşif **Free MCBoot**'a zemin hazırladı, bu program kendini 'güncelleme verisi' olarak takdim edip orijinal shell'i, **Homebrew (ev yapımı)** çalıştırabilen bir shell'le değiştirdi.

Aklınızda bulunsun ki bu değişiklik kalıcı değildir, 'Free MCBoot' kurulu bir Hafıza Kartı, konsol başlatılmadan önce takılmalıdır. Ek olarak, bu yazılım sonuçta bir şekilde kurulmalı, yani kurucuyu başlatmak bir diğer açıktan (örneğin disk değiştirme) faydalanılmalıdır.

### Daha fazla disk hilesi

Free MCBoot'un yayınlanmasının ardından hemen aynı sene bir diğer tatkik keşfedildi: Oyunları DVD filmleri olarak göstermek, modchip'e gerek kalmadan tescillenmemiş oyun kopyalarının okumasını mümkün kıldı.

Bu, yalnızca oyun kopyasına sahte metadata (dosya format ön bilgisi) ve sadece DVD filmlerinde kullanılan bölümlendirmeler eklemek suretiyle yama yapmayı gerektirdi. Sonrasında ise aktarılmış (disk burn) kopya, konsola takıldığında sürücü bunu geri atmaz ancak oyunu da çalıştırmaz. Buna rağmen **ESR** adı verilen bir Homebrew programı yardımıyla oyun başlatılabilir.

## Hepsi bu kadar

Makalenin sonuna ulaştığınız için sizi tebrik ediyor ve teşekkürlerimi sunuyorum! Açıkçası, bahsedilecek o kadar şey var ki, bunu bitirdikten sonra, okuyucular eninde sonunda PlayStation'la ilgili şeylerden bıkarlar mı, diye düşündüm.

Her neyse, bütün ciddiyetimle, umuyorum ki bu makaleyi okuduktan sonra yeni şeyler keşfetmişsinizdir ve olur da yorum yapmak isterseniz [benimle iletişime geçmekten](code>r ref(about_url, root = TRUE)</code) çekinmeyin.

Bir dahaki sefere kadar hoşça kalın!  
Rodrigo
