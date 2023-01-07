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
cover: dreamcast
top_tabs:
  Model:
    file: international
    caption: "Dreamcast.<br>Japonya'da 29/11/1998, Amerika'da 09/09/1999 ve 14/10/1999'da Avrupa'da satışa sunuldu."
  Motherboard:
    caption: "Revizyon 'VA1' olarak gösteriliyor.<br>Resmi dokümanlarda sistemin 128KB flash bellek içerdiği belirtilirken, bu anakart nedense bunun yerine 256KB EEPROM yongası içeriyor.<br>Pil ve denetleyici bağlantı noktaları 'Ön panel' adı verilen bir ek kartta bulunuyor."
  Diagram:
    caption: "Önemli veri yolları genişlikleri ve hızları ile etiketlenmiştir."
#Historical
aliases:
  - /projects/consoles/dreamcast/
---

## Giriş

Sega Dreamcast, hem oyun geliştiricilerine hem de konsol oyuncularına hitap etmek için selefine ([Satürn](sega-saturn)) göre birçok yeni özellik sundu. Bu Sega'nın konsol pazarını fethetmek için son girişimi olsa da, Dreamcast'te öncülük edilen bazı teknolojiler devam etti ve gelecekteki ana akım cihazlara taşındı.

```{r results="asis"}
supporting_imagery()
```

## İşlemci (CPU)

Şaşırtıcı olmayan bir şekilde, Sega CPU'larını geliştirmek için yine Hitachi'yi seçti. Eğer [Sega Saturn hakkındaki bir önceki makaleyi](sega-saturn) okuduysanız, işte size yeni nesil SH işlemcisini sunuyorum: **200 MHz** [@cpu-spec] hızında çalışan **SH-4**. Peki, bu işlemci hakkında ilginç olan ne?

- **5 aşamalı boru hattı**: Beş adede kadar talimat aynı anda uçuşta olabilir (ayrıntılı bir açıklama [önceki bir makalede bulunabilir](sega-saturn#cpu)).
  - Komut ardışık dizilimi artık bu nesil konsolların her yerinde bulunuyor ve bundan sonra standart olacak.
- **2 yönlü süper skaler**: CPU'nun boru hattının her aşamasında birden fazla talimatı (bu durumda iki) işleyebildiği ve saniyede daha fazla talimatın yürütülmesini sağlayan yeni bir paralellik türü.
- Özel bir **Kayan Nokta Birimi** veya 'FPU': 32 bit ondalık sayıları (*kayan noktalar*) ve 64 bit olanları (*çiftler*) hesaplar.
- 8 KB **komut önbelleği** ve 16 KB **veri önbelleği**: Konsollar veri önbelleğinden daha fazla komut önbelleği içerme eğiliminde olduğundan bu oran oldukça ilginçtir. Ancak SH-4, veri önbelleğinin iki bölüme ayrılmasına izin verir: 8 KB *Scratchpad* (hızlı RAM) ve 8 KB veri önbelleği.
- **16 bit komut setini** (SuperH ISA) korurken **32 bit dahili mimari:** Tıpkı SH-2 gibi, bu da kod yoğunluğunu artırır ve 32 bit mimarinin avantajlarından yararlanmaya devam ederken veri yolu ek yüklerini azaltır.
- **Harici 64 bit veri yolu**: Fazladan döngü harcamadan 64 bit değerleri (örneğin çiftler ve uzunlar) işlemek için kritiktir.

### Ekstra iş

Bir oyun konsolu CPU'sunun ortak işleri arasında bir oyunun mantığını idare etmek, düşman yapay zekasını çalıştırmak ve GPU'yu talimatlarla beslemek yer alır. Dreamcast'te SH-4, perspektif dönüşümlerinin hesaplanması gibi geometri verilerini işleyerek grafik işlem hattının çoğunda da yer alır. Sonuç olarak, vektör işlemlerini hızlandırabilen **128 bit SIMD** birimi içerir [@cpu-arch].

### Bellek erişiminin iyileştirilmesi

CPU, sanal adresleme için özel bir **Bellek Yönetim Birimi** veya 'MMU' içerir, bu CPU'nun fiziksel bellek adres alanı **29 bit genişliğinde** olduğu için bu yararlıdır. Böylece dört TLB'nin yardımıyla programcılar 32 bitlik adresleri performans kaybına uğramadan kullanabilirler.

Adresleme için sadece 29 bit gerektiğinden, fazladan üç bit bellek korumasını kontrol eder, sırasıyla bellek haritasını değiştirir ve önbelleği atlatır \[@cpu-marcus\] \[@cpu-akiba\].

Bu özelliklerin kullanılıp kullanılmayacağına programcı karar verir. Bu sistem için oyunlar kesinlikle bellek korumasına _ihtiyaç duymaz_ ve MMU'nun açılışta manuel olarak etkinleştirilmesi gerekir.

### UMA yok ama...

Bu sistem, [tanınmış bir rakibi](nintendo-64#simplified-memory-access) gibi katı Birleşik Bellek Mimarisi (Unified Memory Architecture veya UMA) etrafında tasarlanmamış olsa da, **I/O erişimini GPU'ya devretmektedir**. Bu da CPU'nun kendi özel RAM'i ya da seri arayüzü (ki bunlar da bağlı) dışında bir şey alması gerektiğinde GPU'dan talepte bulunması ve gerekirse beklemesi gerektiği anlamına geliyor.

### Özel sorgular

Bu CPU aynı zamanda **Paralel I/O** veya 'PIO' olarak adlandırılan ve aynı anda birden fazla I/O konumunu manipüle etmek için kullanılan benzersiz bir işleve sahiptir. Sega bu pinleri CPU'nun GPU'nun **video modunu** manipüle edebilmesi için bağlamıştır (bu konuda daha sonra daha fazla ayrıntı verilecektir).

## Grafikler

GPU paketi **100 MHz hızında** çalışan **Holly** adlı özel yapım bir çiptir, VideoLogic (şimdi Imagination Technologies olarak biliniyor) tarafından tasarlanmış ve NEC tarafından üretilmiştir. Holly'nin 3D çekirdeği Videologic'in **PowerVR2'si** ('PowerVR Series2' ve 'CLX2' olarak da adlandırılır).

![Sonic Adventure (1999).](sonic.png) {.open-float}

VideoLogic, 3D motorunun yapımı için **Karo Tabanlı Ertelenmiş Görüntü Oluşturma** (TBDR) adı verilen alternatif bir yaklaşım seçti.

TBDR, tüm kareyi bir kerede oluşturmak yerine (geleneksel **Anında Mod Oluşturucuların** veya 'IMR'nin yaptığı gibi [@graphics-arch]), oluşturma alanını 'karo' adı verilen birden çok bölüme ayırır. Ardından, her bir karo üzerinde ayrı ayrı render işlemini gerçekleştirir ve sonuç nihai kareyi oluşturmak için birleştirilir [@graphics-powervr].

`r close_float_group(with_markdown = TRUE)`

Bu yenilikçi tasarım ilginç avantajları da beraberinde getiriyor:

- Büyük ölçüde **paralelleştirilebilir**, bu da bant genişliğini ve güç kullanımını önemli ölçüde azaltır.
- Çokgenleri otomatik olarak **önden arkaya doğru** sıralayarak ve ardından boru hattının ilk aşamalarında [z-testleri](nintendo-64#modern-visible-surface-determination) gerçekleştirerek [**görünürlük sorununa**](sega-saturn#an-introduction-to-the-visibility-problem) akıllıca bir çözüm uygular. Bu görevlerin kombinasyonu yalnızca orijinal sorunu çözmekle kalmaz, aynı zamanda kaynakları boşa harcayan ve performansı düşüren **aşırı çizimi (gizli çokgenlerin rasterleştirilmesi) de önler.**.

Imagination'ın bu verimli teknolojiyi ileriye taşıyarak ilk nesil iPhone, iPhone 3G, Nokia N95 ve Dell Axim x51 gibi inanılmaz sayıda cihaza güç veren Seri 4 PowerVR çekirdeklerini üretmesi hiç de şaşırtıcı değil.

### Mimari

Dreamcast'in GPU'sunun iki ana bileşenine bir göz atalım [@graphics-marcus]:

#### Karo Hızlandırıcı {.tabs.active}

![Karo Hızlandırıcının Mimarisi.](tile_accelerator.png) {.tab-float}

Render işlemi başlamadan önce, **Döşeme Hızlandırıcı** olarak bilinen bir bileşen ön işleme gerçekleştirir. Geometrinin işleneceği birkaç 32x32 karo kutusunu tahsis ederek başlar.

Ardından, Karo Hızlandırıcı:

1. CPU tarafından yayınlanan geometri verilerini ve çizim komutlarını alın (DMA veya geleneksel aktarımları kullanarak).
2. Bu verileri *dahili* bir formata dönüştürür.
3. Geometriyi koordinatlarına göre her bir bölmeye dağıtır. Kırpılmış geometri de atılacaktır.
4. Ortaya çıkan Görüntüleme Listelerini oluşturur.

Bu Ekran Listeleri daha sonra 3D motoru tarafından yorumlanır: PowerVR2.

#### PowerVR2 Çekirdek {.tab}

![PowerVR2 Çekirdeğinin Mimarisi.](powervr2.png) {.tab-float}

Grafiklerin hayata geçirildiği yer burasıdır, TA'dan alınan Görüntü Listeleri çekirdeğe **dahili bir çerçeve arabelleği** kullanarak tek bir karenin geometrisini oluşturmasını söyler. Süreç aşağıdaki gibidir:

1. **Görüntü Sentez İşlemcisi** veya 'ISP' ilkelleri (üçgenler veya dörtlüler) alır ve görünmeyen çokgenleri kaldırmak için **Gizli Yüzey Kaldırma işlemini** gerçekleştirir. Ardından, Z tamponlarını ve şablon tamponlarını hesapladıktan sonra veriler, diğerlerinin arkasında görünecek çokgenlerin işlenmesini önlemek için **Derinlik Testinden** ve bir 2B çokgenin (**Maske olarak** da adlandırılır) arkasında yer almaları halinde görünmeyecek geometriyi ayıklamak için Şablon **Testlerinden geçer.**
    - Bu testlerin boru hattının başlangıcında nasıl etkin bir şekilde gerçekleştirildiğine dikkat edin. Bunun aksine, [geç z-tamponlama kullanan](nintendo-64#modern-visible-surface-determination) önceki konsollar geometriyi işlem hattının sonunda atmaktadır. ISP yaklaşımı, sonunda atılacak olan geometrinin işlenmesini önler [@graphics-surface] ve böylece kaynak tasarrufu sağlar.
2. **Doku ve Gölgelendirme İşlemcisi** veya 'TSP' karo alanı üzerinde renklendirme, gölgelendirme ve çoklu efektler uygular.
    - Dokular karo dışa aktarılana kadar uygulanmaz, yani ortaya çıkan fazla çizim (varsa) dolgu oranını düşürmez.

İşlem tamamlandıktan sonra, işlenen karo VRAM'deki ana çerçeve arabelleğine yazılır. Bu işlem tüm karolar bitene kadar tekrarlanır. İşlem tamamlandığında, ortaya çıkan çerçeve arabelleği **Video kodlayıcı** tarafından seçilir ve video sinyali aracılığıyla gönderilir.

### Büyük resim {.tabs-close}

Açık mimari farkın yanı sıra, Doku ve Gölgelendirme İşlemcisi, bu konsolun eski [Saturn'den](sega-saturn) ne kadar uzak olduğu hakkında bir fikir veren birçok yetenekle birlikte geliyor. İşte bazı önemli örnekler:

- **Alfa karıştırma**: Şeffaflık efektleri elde etmek için üst üste binen katmanların renklerini birleştirir.
  - Bu sistemde saydamlık uygulamak için kullanılan işleme sıralamadan **bağımsız saydamlık** denir. Algoritma, renklerini karıştırmadan önce ilkelleri otomatik olarak sıralar ve bu işlem render işlemini yavaşlatsa da, tüm sıralamayı manuel olarak yapmak için oyunun kendisine güvenilmesini önler. Bu nedenle Dreamcast oyunları şeffaf nesneleri görüntülemede mükemmeldi.
  - Karo tabanlı sistemle birlikte, siparişten bağımsız şeffaflık önceki [aksaklıkları](sega-saturn#the-transparency-issue) tamamen giderir.
- **Mip-Mapping**: Gerekli ayrıntı düzeyine bağlı olarak dokunun küçültülmüş bir sürümünü otomatik olarak seçer. Bu, kameradan uzakta görülebilecek büyük dokuların işlenmesini önlemek için yapılır (bu işlem gücü kaybı olur ve örtüşme üretir).
- **Ortam eşleme**: Dokular üzerinde yansımalar uygular.
- **Bilinear, Trilinear ve anizotropik filtreleme**: Bunlar dokuları yumuşatmak ve pikselleşmeyi önlemek için kullanılan farklı algoritmalardır. Bunlar 'en kötü'den 'en iyi'ye doğru sıralanır ve her birinin sonuç kalitesi, gereken hesaplama miktarıyla doğru orantılıdır.
  - Bu Saturn'e göre büyük bir adım, çünkü önceki model herhangi bir doku filtresi sağlamıyordu!
- **Bump mapping**: Fazladan poligon harcamadan yüzeylerdeki kusurları simüle eder.

### Detay Kazanma

Holly artık [selefine](sega-saturn) göre ~10 kat daha fazla poligon çizebiliyor, işte model tasarımlarının artık o kadar da sınırlı olmadığını gösteren bir *Önce & Sonra* örneği. Onlarla oynamaya çalışın!

![Satürn için Sonic R (1997).<br>286 üçgen (veya 185 dörtgen).](sonic_r_saturn){.toleft model3d="true"}

![DC için Sonic Adventure (1999).<br>1001 üçgen.](sonic_adventure_dc){.toright model3d="true"}

### Video Modları

Video sistemi birden fazla ekran türünü ve formatı destekleyecek şekilde tasarlanmıştır, bu nedenle video kodlayıcı aşağıdaki sinyal türlerini destekleyen tek şekilli bir sokete çıkış verir:

- **Kompozit**: Video görüntülemek için gereken üç sinyali (chroma, luma ve sync) tek bir sinyalde birleştirir ve yalnızca tek pimli bir kablo gerektirir.
  - Bu, RCA bağlantılı eski PAL ve NTSC TV'lerde kullanılır.
- **S-Video**: Kromayı ayrı tutarken luma ve senkronizasyonu birleştirir (toplamda iki video hattı).
- **RGB**: Ayrı Kırmızı-Yeşil-Mavi sinyalleri gönderir ve aralarından seçim yapabileceğiniz farklı senkronizasyon türleri sağlar (kompozit senkronizasyon veya kompozit veya S-Video videodan çıkarılmış).
  - Bir SCART kablosu bu tipi kullanacaktır.
- **VGA**: RGB'yi iki senkronizasyon sinyali (yatay ve dikey) ile birleştirerek toplamda beş video hattı elde eder. Bu, aşamalı modda mümkün olan en yüksek çözünürlüğün (720x480) görüntülenmesini sağlar (bu nedenle, bu mod genellikle '480p' olarak adlandırılır). VGA aslında bir süredir bilgisayar monitörleri tarafından kullanılan standart format/ortam olmuştur.
  - Bu türü kullanmak için Sega ekstra bir aksesuar olarak bir VGA adaptörü sağlamıştır.

Şimdi, Dreamcast bunların hepsini aynı anda kodlayamaz, bu nedenle GPU ve Ses işlemcisi, istenen sinyali oluşturmak için hangi video/ses veri yollarının etkinleştirileceğini koordine eden **Görüntü Modu** adlı bir kayıt içerir. CPU, takılan kablonun türünü algılar (video konektörünün hangi 'seçme bitlerinin' aktif olduğunu kontrol ederek) ve GPU'ya gerekli değerleri yazar. Son olarak, değerler Ses işlemcisine iletilir.

VGA kesinlikle aşamalı bir sinyal türü olduğundan (geleneksel *taramalı* sinyalin aksine), yalnızca taramalı video için tasarlanmış oyunlarda bazı uyumluluk sorunları ortaya çıkmıştır. Bunlar, kodlarında oyunun VGA'da görüntülenmeyeceğini açıkça belirtmektedir, bu nedenle CPU, kullanıcı VGA kablosunu başka bir türle değiştirene kadar oyunu engelleyecektir.

## Ses

Ses işlevselliği, Yamaha tarafından üretilen **AICA** adlı özel bir çip tarafından gerçekleştiriliyor; bu çip [Saturn'de kullanılan SCSP'nin](sega-saturn#audio) geliştirilmiş bir versiyonu ve dört bileşenden oluşuyor:

- **Ses Entegre Devresi** veya 'IC': Ses sinyalini üreten ve üzerinde efektler uygulayan bir dizi modül (sentezleyici, DSP ve karıştırıcı). **16 veya 8 bit** çözünürlüğe ve **44,1 kHz** örnekleme hızına sahip **64 adede kadar PCM kanalını** destekler. Genel olarak, bu ses çalmak için en uygun kalitedir.
  - Ayrıca, CPU'dan bazı işleri boşaltmak için bir **ADPCM kod çözücü** içerir.
  - İlginçtir ki, bir MIDI enstrümanı bağlamak için **iki MIDI pini** de sağlar, ancak bu geliştirme sırasında kullanılmak üzere tasarlanmıştır.
- **2 MB SDRAM**: Ses verilerini ve programları depolar. Ana CPU tarafından DMA kullanılarak doldurulur.
- 2,82 MHz hızında çalışan bir **ARM7DI:** Ses IC'sini kontrol eder. Bu CPU, ses verilerini yorumlayan ve Ses IC'sini buna göre manipüle eden SRAM'de depolanan küçük bir yazılım ([sürücü](super-nintendo#audio) olarak adlandırılır) önyüklenerek programlanır.
  - Eğer merak ediyorsanız, benzer bir CPU [Game Boy Advance](game-boy-advance)'de de bulunmaktadır.
- **Bellek Denetleyicisi**: 2 MB SDRAM'in arayüzünü oluşturur.

Geliştirmeye yardımcı olmak için, resmi SDK farklı ihtiyaçlar (sıralama, kod çözme, vb.) için birden fazla ses sürücüsü içeriyordu.

### Evrim

[Mega Drive/Genesis](mega-drive-genesis#audio) günlerinden bu yana çok yol kat ettik, ses sentezinde ne kadar ilerleme kaydedildiğini göstermek için, burada aynı kompozisyonu kullanan biri Mega Drive ve diğeri Dreamcast için iki oyun örneği var:

![Mega Drive için Sonic 3D Blast (1996).<br>Bir önceki model, anında ses sinyalleri üretmek için FM sentezi gerçekleştirir.](megadrive){.toleft video="true"}

![Dreamcast için Sonic Adventure (1999).<br> Yeni ses alt sistemi PCM örneklerini sorunsuz bir şekilde işler.](dreamcast){.toright video="true"}

Sonic Adventure'ın bestecileri bir FM çipi programlamak yerine müziklerini kendi bünyelerinde üretmiş ve daha sonra CRI Middleware tarafından geliştirilen kayıplı bir format olan 'ADX'e kodlamışlardır. Dolayısıyla, 64 PCM kanalından yalnızca ikisini (stereo) kullanır.

ADX sıkıştırması, oyunun bellek veya bant genişliği tükenmeden GD-ROM'dan Ses IC'sine veri çözmesini ve aktarmasını sağlar. Ana CPU ve ARM7'nin iş yükünü dengelemek için birden fazla yaklaşım olduğundan sürücü birçok şekilde uygulanabilir.

### Uyanık Kalma

Bir şekilde, bu çip aynı zamanda BIOS'a **Gerçek Zaman Saati** (RTC) sağlamaktan da sorumludur, ayrıca AC gücü olmadan çalışmaya devam etmek için bir saat piline bağlıdır.

## İşletim Sistemi

2 MB'lık 'Sistem ROM'u, konsol açıldığında oyunu başlatan bir **BIOS** veya küçük bir kabuk depolar.

BIOS ayrıca GD-ROM sürücüsünden okuma gibi I/O işlevlerini [@games-redream] basitleştirmek için oyunların kullandığı rutinler içerir.

### Shell

Geçerli bir oyun diski takılı değilse, konsol grafik kabuğunu önyüklemeye devam eder.

![Disk olmadan önyükleme yaptıktan sonra shell.](shell.png) {.open-float}

Shell, kullanıcının aşağıdaki gibi temel ancak gerekli görevleri yerine getirmesini sağlamak için basit bir grafik kullanıcı arayüzü içerir:

- Henüz başlamadıysa oyunu başlatır.
- VMU'da depolanan kayıt verilerini değiştirin (bu cihaz hakkında daha sonra daha fazla ayrıntı!).
- Takılı bir Ses CD'si varsa müzik çalar.
- Bazı ayarları değiştirin (Tarih, Saat, Ses, vb.).

`r close_float_group(with_markdown = TRUE)`

### Windows CE

Dreamcast'in duyurulmasından bu yana, konsolun **Windows CE'yi** çalıştırabileceği söyleniyordu: Windows'un gömülü cihazlarda kullanılmak üzere tasarlanmış sadeleştirilmiş bir sürümü. Bazı kullanıcıların konsollarında tam bir Windows CE masaüstü ortamı görmeyi bekledikleri düşünüldüğünde bu biraz yanıltıcıdır.

![Windows CE logosu kasanın ön tarafına damgalanmıştır.](windows_ce.jpeg) {.open-float}

Gerçekte, bu 'işletim sisteminin' amacı Nintendo'nun [Nintendo 64](nintendo-64#operating-system) ile yaptığına çok benziyordu: programcılara belirli işlemleri basitleştirmek için adil bir soyutlama katmanı sağlamak.

Microsoft, Windows CE'yi Dreamcast'e getirmek için Sega ile birlikte çalıştı [@games-sdk]. Sonuç, grafik, ses ve hata ayıklama sağlamak için gereken minimum bileşenlere sahip bir CE alt kümesiydi. Bu, geliştirme için Microsoft'un yıldız IDE'si **Visual Studio'**nun kullanımını içeriyordu.

`r close_float_group(with_markdown = TRUE)`

Bazı geliştiriciler bu seçeneği çok cazip buldu. CE ile birlikte gelen ses-grafik çerçevesi **DirectX 6'dan başkası olmadığından,** o dönemin binlerce PC oyunu teorik olarak Dreamcast'e kolayca taşınabilirdi...

Ancak Dreamcast ile geleneksel PC arasındaki mimari farklılıklar göz ardı edilemeyecek kadar büyüktü [@games-direct]. Ayrıca, bu sistemin yerleştirilmesi oyunun yükleme süresini artırdı (sonuçta, 'OS'nin bir diskten yüklenmesi gerekiyordu) ve Windows CE, Dreamcast'in kaynaklarının önemli bir kısmını tüketti (*şaşırtıcı olmayan bir şekilde, PC'ler zaten bundan muzdaripti*).

Sonunda, 'Dreamcast için Windows CE' geliştiriciler için tercih edilen bir başka SDK oldu (genellikle **Dragon SDK** olarak anılır). Bununla birlikte, önemli sayıda Dreamcast oyunu Windows API'lerini ve DirectX'i seçti.

## Giriş/Çıkış

GPU ayrıca **Sistem Veriyolu** adı verilen I/O'nun çoğunu işlemek için başka bir modül içerir. Aşağıdaki arayüzleri sağlar:

- **G1** arayüzü: **BIOS ROM'un** kayıtlı yapılandırması ve **GD-ROM** içeriği ile birlikte erişilebildiği yer.
- **G2** arayüzü: **Modem** ve **Ses Denetleyicisine** erişim sağlar.
- **Maple** arayüzü: Kontrolörler (bunlara bağlı aksesuarlarla birlikte) ve CPU arasında veri parçalarını aktarır. Bu bir **seri veri yoludur** ve özel bir DMA sağlar.
- **SH-4** arayüzü: Genel amaçlı iletişim için ana CPU'yu bağlar.
- **DDT** arayüzü: DMA aktarımları sırasında ana belleğe erişmek için CPU veriyolunun kontrolünü ele alır.
- **PVR** arayüzü: CPU'yu özel bir DMA kullanarak Karo Hızlandırıcıya bağlar.

## Oyunlar

Geliştirme esas olarak **C** veya **C++ ile** yapılmıştır. Mevcut C++ derleyicileri başlangıçta işlevsellik açısından çok sınırlı olduğundan, ilk başta C önerilen seçimdi.

Sega ayrıca **Sega Katana Geliştirme Kutusu** adı verilen PC benzeri bir kule şeklinde geliştirme donanımı da sağlamıştır. Bu, geliştirme için geliştirilmiş I/O'ya sahip Dreamcast donanımıdır. Ayrıca resmi **Katana SDK** ve Windows 98 PC'ye kurulacak araçları içeren bir CD ile birlikte geldi.

Geliştiricilerin bunun yerine Dragon SDK'yı seçmesi durumunda, DirectX 6.0 ve Visual C++ 6.0 da onlar için kullanılabilirdi.

### Medium

Oyunlar GD-ROM'larda saklanır, bunlar sadece daha yüksek yoğunlukta çukurlara sahip CD-ROM'lardır (bir gigabayt kapasiteye ulaşır). Hız 12x, Saturn'ün 2x CD okuyucusuna kıyasla *çok da kötü değil*.

### Çevrimiçi platform

Dreamcast, oyunların çevrimiçi oyun için çevirmeli bir hizmeti 'aramak' için kullanabileceği bir **modem** modülü yüklü olarak gönderildi. Sega iki hizmet sağlıyordu: **SegaNet** (Amerika ve Japonya'da kullanılan) ve **Dreamarena** (Avrupa'daki muadili).

Oyuncular, bazı oyunlarla birlikte verilen ekstra bir disk olan **DreamKey'i** kullanarak bir hizmete kaydoldular. DreamKey bir hesap açmak için bir web tarayıcısı sağlıyordu. Başlangıçta, DreamKey bölgeye bağlı olarak önceden yapılandırılmış bir hizmet olarak geldi, ancak daha sonraki revizyonlar kullanıcıların herhangi birine bağlanmak için ISS ayarlarını değiştirmelerine izin verdi.

Ayrıca, kullanıcının internette *PC tarzı* sörf yapmak istemesi durumunda satın alınabilecek Dreamcast markalı bir klavye ve fare de vardı.

Ne yazık ki, SegaNet ve Dreamarena piyasaya sürüldükten iki yıl sonra durduruldu. Bu nedenle, bu tür hizmetler ekstra araçlar kullanılarak taklit edilmediği sürece (DreamPi gibi, bir kullanıcı topluluğu tarafından tutulan sunucuların yardımıyla bunları kopyalayan bir Raspberry Pi görüntüsü), yalnızca bunlara dayanan oyunlar kullanılamaz hale geldi.

### Etkileşimli hafıza kartı

Dreamcast'in sahip olduğu bir diğer yenilikçi özellik de **Görsel Bellek Birimi** veya 'VMU' idi. Kontrol cihazına takılıdır ve bir bellek kartı olarak hizmet etmenin yanı sıra, [@games-vmu] içeren tam teşekküllü bir cihazdır:

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

`r close_float_group(with_markdown = TRUE)`

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
