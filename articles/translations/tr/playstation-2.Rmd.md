---
short_title: PlayStation 2'nin Mimarisi
long_title: PlayStation 2'nin Mimarisi
long_name: PlayStation 2
name: PS2
subtitle: Üstlerini gölgede bırakıyor
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

Bu konsolun özünde, **Emotion Engine** ya da 'EE' denilen, Toshiba ve Sony tarafından [@cpu-cataldo] ortaklaşa dizayn edilen, **~294.91 MHz**'de çalışan güçlü bir paket (çipler bütünü) görüyoruz [@cpu-rockin].

![Emotion Engine, bu konsolun ilk anakart revizyonunda.](ee_chip.jpg)

Çipset, sayısız bileşen içeriyor ve bunlardan biri de Ana İşlemci (CPU). Geri kalan kısımları ise belirli görevleri hızlandırmak için CPU'nun kullanımındadır. Bu analiz için EE'yi üç kısma ayıracağız:

- **Lider:** Bütün yongayı kontrol eden asıl bileşendir.
- <strong x-id=“1”>Kullanılabilir bellek</strong>, işlemcilerin anlamlı işlemler yapabilmesi için çok önemli bir bileşen.
- **Co-processors**: Bunlar, belirli hesaplamaları hızlandırır.

### Çipteki Lider

Kısaca açıklarsak, Ana İşlemci, bu konsol için özel tasarlanmış bir MIPS işlemci çekirdeği olan **MIPS R5900**. Belki hatırlarsınız ki Sony, daha ilk [PlayStation](playstation)'dan (ikincil kaynak olan LSI'den alınan [MIPS R3000A](playstation#the-offering)'yı görüyoruz) [MIPS silikon çipiyle risk almıştı](playstation#tab-1-2-mips-and-sony). Sonraki nesilde ise MIPS 'R5900'le karşılaşıyoruz... ancak bu isim bizim için ne ifade ediyor?

Yeni sayıların ardında yatan şeyi anlamak için o dönemi kuşatan bir tarihî anlatıya bakalım.

#### Diğerlerini aşan başarı {.tabs.active}

<a href=“nintendo-64#cpu”>MIPS R4000</a>, <a href=“nintendo-64”>yakın rakibi</a> de dahil olmak üzere çok çeşitli sistemler tarafından benimsenen popüler bir işlemci serisiydi. Başarısı sayesinde MIPS, halka tek seferde pahası karşılanamayacak düzeyde gelişmeler (64-bit bilgi işleme, 8 aşamalı pipeline ve dahasını) getirdi.

İleri sararsak, sonraki büyük sıçrama 1995'te, **R10000**'in piyasaya sürülmesiyle gerçekleşti. Bu tarihte MIPS, [SGI](playstation#tab-1-2-mips-and-sony)'ın maddî yatırımıyla, R4000'in paralellik kâbiliyetini çok ileri taşıdı ve ilk kez şunun gibi teknikler ortaya çıktı [@cpu-yeager]:

- <strong x-id=“1”>Speculative execution</strong>: CPU, hesaplanmadan önce conditional branch sonucunu tahmin eder. Bu öngörüler, dâhilî 512 girdilik bir tabloda saklanan önceki işlemlerin sonuçlarına göre gerçekleştirilir. Koşul hesaplandığında, eğer öngörü doğru ise, İşlemci önemli zaman kazanmış olacak. Yok değilse, ekstra hesaplamalar yok edilir.
  - Bu sayede MIPS, sonunda sürekli tekrar eden bir sorunu ([control hazards](playstation#delay-galore)<!--Türkçesi için önce PS1 makalesi çevirilmeli-->) bir avantaja çevirdi.
  - Diğer işlemcilerde ise [dynamic branch prediction](gamecube#features) olarak anılan benzer bir işleyiş biçimi görebilirsiniz.
- **4-çıkışlı süperskaler** pipeline: [Pipeline tasarımına](sega-saturn#cpu) ek olarak, CPU artık pipeline'ın başlangıcında dört adede kadar talimat getirecek ve bunları ayrı birimlere dağıtarak CPU'nun bu talimatları aynı anda yürütmesini sağlayacaktır. Bunu yapmak suretiyle, İşlemci, daha üst derecede bir paralelliğe erişir.
- **Out-of-order execution**: Ayrıca İşlemci, birimlerini olabildiğince doldurmak için talimatların sırasını da yeniden düzenleyecek (hazard eklenmediği sürece).
- **128-bit'lik veri yoluna sahip L2 önbelleği**, aynı anda İşlemciye daha fazla veri çekmeyi mümkün kılar, bu da önceki geliştirmeler göz önüne alındığında bir gerekiliktir.

Böylesi bir yenilik, karmaşık bir dizayna mâl olur ancak sonuçta ortaya çıkan ürün, her şekilde ucuz değildi. SGI, bunu sadece üst düzey ekipmanlarla bir bütün hâlinde satıyordu ve bunu bir ev konsoluna taşımaya çalışmak söz konusu bile olamazdı.

#### Halk'a sunulan Üst düzey {.tab}

R10000'in ticârî kısıtlamalarının çoktan farkına varmış olan SGI/MIPS, **Quantum Effect Devices**'ı (QED), R10000'in orta ve düşük seviye pazar için makul fiyatlı bir versiyonunu geliştirmesi için işe aldı. Eski MIPS çalışanları tarafından kurulmuş bir şirket olan QED, MIPS çekirdeklerinin bütçeye uygun sektör için varyantlarını dizayn ediyordu.

Nihayet, QED, **R5000** adlı yeni bir işlemci çekirdeği ile döndü; bu, R10000'in kayda değer kesintilere uğramış hâliydi [@cpu-halfhill]:

- Sırasız yürütme özelliği, sıralı yürütmeye geri döndürülmüştü.
- Speculative execution kaldırıldı.
- Süperskalerlik iki tâlimatla (2-çıkışlı) kısıtlandı ve artık tam sayı tâlimatları paralelize edilmiyordu. Yine de kayan noktalı (floating point) tâlimatları diğerleriyle eşlenebiliyordu.
- Önceki eksiltmeler sebebiyle, L2, 64-bit veriyoluna indirildi.

Sonuç olarak bu, SGI'ın düşük bütçeli iş istasyonları gibi enerji (güç) tasarruflu donanım için ideal bir İşlemci hâline geldi. Her halükarda, QED'in <strong x-id=“1”>vektör/3D uygulamaları</strong> için cazip bir ürün olarak tutmayı planladığı gibi, küçültülmüş pipeline'ın hala eşzamanlı kayan nokta işlemleri gerçekleştirdiğini unutmayın. Yakında başka bir şirketin de bunu hızla not ettiğini göreceksiniz.

İlave not olarak, ilginçtir ki okyanusun öbür ucunda da benzer geliştirmeler vardır fakat aksi yönde: ARM çiplerini üst düzey pazara taşımak uğruna [ARM, DEC ile güçlerini birleştirdi](nintendo-ds#arms-new-territories).

#### Sony'e özel bir sipariş {.tabs-close}

Toshiba bir süredir MIPS lisansı sahibiydi [@cpu-toshiba] ve MIPS varyasyonları ve paketleri üretmeye yabancı değildi. Bir noktada Sony ve Toshiba, Sony'nin yakında çıkacak konsolu için özel olarak tasarlanmış bir CPU üretmek üzere güçlerini birleştirdi. Bu Toshiba için muazzam bir avantajdı: CPU'ların çoğu zaman farklı paydaşlardan gelen çok sayıda gereksinimi karşılaması gerekir ve bunu yaparken uzmanlaşmaya yönelik fırsatları kısıtlar. Artık sadece tek bir amaç vardı: **3D oyun**. Böylece her türlü <strong x-id=“1”>yenilik</strong> için yeterli alan sağlanmış olur.

Bununla birlikte Toshiba, uygun fiyatlı R5000 tasarımını aldı ve vektör işlemlerini hızlandırmak için ince ayar yaptı. Yeni çekirdek <strong x-id=“1”>R5900</strong> olarak adlandırılıyor ve aşağıdaki '3D' geliştirmeleri sunuyor [@cpu-stokes]:

- <strong x-id=“1”>MIPS III ISA</strong>'nın bir varyasyonu. Bu, daha önce <a href=“nintendo-64#cpu”>Nintendo 64</a>'te görülen orijinal 64 bit ISA'yı içerir, ancak ilginç işlem kodlarıyla genişletilmiştir. Sony, vektör hesaplamalarını hızlandırmak için ([SH-4](dreamcast#special-work)'e benzer, ancak yalnızca tamsayı) **multimedia instructions** adlı kendi SIMD uzantısının yanı sıra **MIPS IV**'ten bazı talimatlar (prefetch ve conditional move) ekledi.
  - Multimedya talimatları hala 32 bit genişliğindedir ancak bir seferde üç adede kadar 128 bit vektörü çalıştırabilir. Vektör aritmetiği, min/maks ve yeni vektörler oluşturmak için birçok skaler kombinasyon gibi işlemler sunarlar.
- <strong x-id=“1”>32 adet 128 bit genel amaçlı register</strong>: Toshiba markalı bir başka önemli geliştirme. Tipik <a href=“playstation#the-offering”>32-bit depolama</a> alanını unutun, şimdi 128-bit alanına adım attık. Bununla birlikte, işlemlerin çoğu mevcut alanın tamamını kullanmayacaktır (MIPS kelimeleri <a href=“nintendo-64#cpu”>hala 64 bit uzunluğundadır</a>). İşte bu noktada, yukarıda bahsedilen multimedya uzantısı denkleme dahil olur, çünkü seti genişletilmiş kayıt dosyasını tam olarak kullanacaktır.
  - Yeni talimatlar kullanıldığında, her bir register birçok skaler türünden oluşan vektörleri saklayabilir (iki 64 bitlik tamsayıdan on altı 8 bitlik olana kadar).
  - Performans kayıplarını önlemek için bu kayıtlara bir <strong x-id=“1”>128-bit veri yolu</strong> üzerinden erişilirken, CPU'nun geri kalanı dahili bir <strong x-id=“1”>64-bit veri yolu</strong> kullanır.
- İki **64-bit ALU**. Her biri 64 bit tamsayıları bağımsız olarak çalıştırabilir, ancak aynı zamanda bir <strong x-id=“1”>128 bit ALU</strong> olmak için birleşebilir. İkincisi, bu parlak multimedya işlem kodlarının arkasındaki beyindir.

Bunların yanı sıra, geliştiricilerin hoşuna gidebilecek başka iyileştirmeler de buluyoruz:

- <strong x-id=“1”>6 aşamalı pipeline</strong>: Bu, <a href=“playstation#the-offering”>öncekilere</a> kıyasla bir ek aşamadır.
- <strong x-id=“1”>2 yönlü superscalar</strong> yürütme: İki ALU sayesinde, iki adede kadar 64 bit tamsayı işlemi artık paralel olarak yürütülür.
  - Bu, MIPS R10000'ün kaybedilen bir başka avantajını geri kazandırır.
- **24 KB L1 önbelleği**: **Talimatlar için 16 KB** ve veriler için **8 KB'a bölünmüştür**.
  - Ayrıca bir **ön belleğe alma fonksiyonu** uygulayarak talimat ve verileri çağırılmadan önce önbelleğe alır. Bu işlem, hafızadaki hangi konumların daha sık ulaşıldığını belirleyen bir ilave devre sayesinde gerçekleşir.
- **16 KB Scratchpad RAM**: 'Hızlı RAM' olarak da bilinir.
- **Hafıza yönetim birimi**: Bellek erişimini sistemin geri kalanıyla koordine eder.

Çekirdek, 32-bit kayan noktalı sayılarla (C'de `float` olarak da bilinir) olan işlemleri hızlandıran bir **ayrılmış kayan nokta işleme birimi** ('COP1' olarak adlandırılır) ile tamamlanmıştır. Bu alışılmadık bir parçadır çünkü **IEEE 754 standardını takip etmez** ve bunun en bariz olanı **infinity** değerinin olmayışıdır (bunun yerine `0` hesaplanır) [@cpu-krysto]. Bunun dışında 32 adet 32 bitlik register'a sahiptir.

### Tanıdık bir hafıza seçimi

Emotion Engine'in yanında iki adet 16 MB RAM bloğu olmak üzere toplam **32 MB** ana hafızası vardır. Kullanılan hafızaya **16-bit veri yolu** ile ulaşılıyor, türü ise **RDRAM**. ([*dejavu!*](nintendo-64#memory-design)).

![Emotion Engine'in bellek dizaynı. Tıkanıklığın nerede ortaya çıkacağını tahmin edebilirsiniz.](MemoryArch.png)

İlk başta, bunu duyması biraz hayal kırıklığı yaşatabilir zira bunun aksine Emotion Engine'in dahili veriyolu 128-bit gibi büyük bir genişliğe sahip. Ancak, RAM çipleri; iki çipi bağımsız iki 16-bit veriyolu (her çipe 1 veriyolu) ile bağlamak suretiyle elde edilen **çift kanallı mimariyi** kullanacak şekilde stratejik olarak konumlandırılmıştır. Ortaya çıkan kurulumla teorik olarak 3,2 GB/sn'lik bir hız elde edilir, dolayısıyla hafıza gecikmesinin bu konsolda bir sorun olmayacağından emin olabilirsiniz!

Emotion Engine'in köşesinde, ana hafıza ile Karalama belleği arasında ya da ana hafıza ile EE içindeki bileşenler arasında veri transferi yapan güçlü bir **Doğrudan Bellek Erişimi Kontrolcüsü** (DMA Controller) ya da 'DMAC' var.

Veri aktarımı 128-bit'lik öbekler halinde yapılır fakat işin ilginç kısmı: Her sekiz öbekte bir ana veriyolunun kilidi geçici olarak açılır. Bu, paralelde (10 adede kadar) diğer DMA aktarımları ya da İşlemci'nin (CPU) ana veriyolunu kullanabilmesi için kapı aralar. Bu *işleyiş biçimi (modus operandi)* için **dilim modu (slice mode)** denir ve bu DMA birimindeki birçok mevcut moddan biridir. Şunu aklınızda bulundurun ki dilim modu, ana veriyolundaki gecikmeleri azaltmayı, DMA transferi genelinde bir yavaşlama pahasına yapar.

### Geçmiş aksiliklerin önlemesi

İstesek de istemesek de Emotion Engine'in içinde meydana gelen trafiğin büyüklüğü sebebiyle, bu dizayn, sonunda **Birleşik Hafıza Mimarisi ya da kısa adıyla UMA**'nın getirdiği sonuçlardan muzdarip olacaktır. Şöyle ki: Birden çok bağımsız bileşen, ana hafızaya aynı anda eşirmeye çalışıp tıkanıklığa sebebiyet verecek. Aslında bu sorunu düzelmek için Sony, devamlı hafıza ihtiyacını şu yollarla hafifletti:

- İşlemciyi **çok sayıda önbellekle** donattı. Böylece, sadece zorunlu gereklilik durumlarında ana hafızaya erişilmesi gerekir.
    - Bu makalede bahsedilen önbellek/karalama belleklerinin %99'u bu sebepten mevcuttur.
- 128-Byte'lık **Write Back Buffer** eklendi: [Write Gather Pipe'a](gamecube#ibms-enhancements) çok benzerdir ancak, %25'i dolana kadar beklemek yerine, ilk olarak veriyolunun durumunu (tıkanık mı açık mı) kontrol eder.

Bu, önbellekten yararlanabilecek uygulamalar için çok elverişli görünebilir ancak ya Görüntü Listeleri'ni (Display Lists) manipüle etme gibi önbelleği kullanmaması gereken işlemler ne olacak? Neyse ki İşlemci, **sadece ve sadece** Geri Yazma Arabelleğini kullanan **Önbelleksiz (UnCached) Mod** adlı bir diğer hafıza erişim moduna sahiptir. Böylelikle Önbelleği (*Önbellek kayıplarını*) düzeltmek için İşlemci döngüsü heba edilmez.

Dahası, **Önbelleksiz hızlandırılmış mod** da mevcut. Bu mod, hafızadaki ardışık adreslerin okunmasını hızlandırmak için bir arabellek ekler.

### Diğer ilginç parçalar

Aynı Emotion Engine paketinin içinde, <strong x-id=“1”>Image Processing Unit</strong> veya 'IPU' adı verilen ve bu kez <strong x-id=“1”>image decompression</strong> için tasarlanmış bir işlemci daha bulunuyor. [MDEC](playstation#tab-2-3-motion-decoder)'nin halefi olan IPU, bir oyunun MPEG2 görüntülerini (movie) Ana İşlemciyi (CPU) meşgul etmeden çözmesi gerektiğinde kullanışlı olabilir.

Uzun lafın kısası, oyun, sıkıştırılmış görüntü akışını IPU'ya (umarız DMA kullanarak) gönderir ve bu GPU'nun (Grafik İşlemci) ekranda gösterebileceği bir formata çevrilir. Ayrıca PS2'nin İşletim Sistemi de DVD oynatma için IPU'dan istifade eder.

Son olarak, IPU ayrıca sıkıştırılmış **Yüksek çözünürlüklü dokuları** da işler. Bu da işlemci kullanımını ve büyük transferleri azaltır.

## Yardımcı İşlemciler (Co CPU'lar)

Rakipleri [son ürünlerini](dreamcast) piyasaya süreli iki yıl olmuştu. Önceki makalemizi okuduktan sonra bu makaleyi okumaya okumaya başladıysanız, tahminimce PS2'yi o zamanlarki gücüne kavuşturan 'o şeyi' görmeyi *hâlâ* bekliyorsunuz. Öyleyse, müsaadenizle, Sony'nin Emotion Engine'e sığdırdığı, *çok* önemli bir bileşen setini sizlere duyurayım: o güçlü **Vector Processing Units** veya 'VPU'.

### Mimari

Vektör İşlem Birimi, vektörleri ve bilhassa dört kayan noktalı (`float`) sayıdan oluşan vektörleri işlemek için tasarlanmış küçük bağımsız bir işlemcidir. Bu işlemciler o kadar hızlıdırlar ki **işlem başına tek döngü** harcarlar, bu ise vektör işleme için aşırı elverişlidir. Buna karşın CPU'nun FPU'suyla aynı standart dışı (IEEE 754'e uymayan) davranışı sergilerler.

VPU'lar aşağıdaki bileşenlerden oluşur:

- Birkaç **Vector Unit Memory** ya da 'VU Hafızası': Vektör Birimi için çalışma alanı olarak kullanılır. Burada işlenmesi gereken değerler ya da önceki işlemlerin sonuçları depolanır.
- Bir **Vector Unit**: İşlemcinin çekirdeğidir. 'VU Hafızası'nda bulunan verilerin nasıl işlenmesi gerektiğini bildiren (**Mikroprogram** denen) bir yazılımı saklamak için biraz hafıza içerir (**Mikro Hafıza/Micro Memory** adı verilir).
  - Bir **64-bit ISA** uygular ve çalıştırma birimi ise **iki paralel alt birime ayrılmıştır**. Bunlardan biri, kayan noktalı sayılarla (float) çarpma ya da toplama, diğeri ise kayan noktalılarda bölme ya da tam sayılarda (integer) işlem yapar. Bu, kayan noktalı sayılar ve tam sayılarla **eş zamanlı olarak** işlem yapabilmeyi mümkün kılar.
- Bir **Vektör Arayüzü**: Ana hafızadan, Vektör Birimi'nin anlayabileceği bir formatta gelen vertex verilerinin (vertex data) otomatik olarak sıkıştırmasını açar (decompress eder). Ayrıca, bu birim mikroprogramları Micro Memory'e transfer edebilir.

### İşlevsellik

Vektör biriminin çalışması için 'başlatılması' gerekir. Ana İşlemci, bu iş için olan mikrokodu sağlamaktan sorumludur.

Emotion Engine'in içine konmuş **iki VPU** vardır ancak bunlar farklı şekilde ayarlanmış olup, farklı kullanım ve optimizasyonlara elverişlidirler.

#### Vector Processing Unit 0 {.tabs.active}

![VPU0 Mimarisi.](VU0.png) {.tab-float}

İlk VPU yani **VPU0**, CPU ile diğer vektör birimi (VPU1) arasında konumlandırılmıştır. Ana işlemciye 'yardımcı' rolde bulunur.

VPU0, iki adet işlem moduna sahiptir:

- **Mikro Mod**: Bu mod 'geleneksel mod'dur. VPU, Mikro Hafıza'da bulunan bir mikroprogramın 'mikro talimatlarını' bağımsızca yürütecektir.
- **Makro Mod**: VPU0, CPU için 'COP2'ye (Yardımcı İşlemci 2'ye) dönüşür ve belli bir 128-bit veriyolu aracılığıyla CPU'dan alınan 'makro talimatlar'ı çalıştırır.
  - Makro talimatlar, micro talimatlarla aynı işlevselliğe sahiptir ancak farklı opcode'lar (assembly kodları) kullanır. Buna rağmen, VPU yürütme birimi artık ayrık değildir (yani aynı zamanda sadece 1 talimat yürütebilir).
  - Bu mod VPU0'ın tüm bileşenlerinden tamamen yararlanamasa da CPU'nun vektör işlemlerini hızlandırmaya devam eder. Üstelik, basitlik açısından, bir yardımcı işlemciyi programlamak, bağımsız bir birimi programlamaktan daha kolaydır (bilgisayar programlayıcıları bunu kullanışlı bulacaktır).

VPU0'ın memory map'i, muhtemelen durumunu kontrol etmek veya diğer VPU tarafından yapılan bazı işlemlerin sonuçlarını hızlı bir şekilde okumak için diğer VPU'nun bazı register'larına ve flag'lere da erişebilir.

#### Vector Processing Unit 1 {.tab}

![VPU1 Mimarisi.](VUP1.png) {.tab-float}

Mevcut ikinci VPU, yani **VPU1** ise VPU0'ın dört katı mikro hafıza ve VU Hafızasıyla yükseltilmiş bir versiyonudur. Dahası, bu birim **Elementary Function Unit** ya da 'EFU' adı verilen; üstel ve trigonometrik fonksiyonların yürütülmesini hızlandıran ilâve bir bileşen içerir.

VPU1 konum olarak, VPU0 ve (GPU'ya çıkan kapı olan) Grafik Arayüzü'nün arasında bulunur, bu sebeple, ana veri yolunu kullanmadan ve olabildiğince hızlı şekilde GPU'yu geometriyle beslemek için ekstra veri yolları içerir.

Öte yandan VPU1, konumu gereği, **sadece mikro modda çalışır**.

VPU'nun trigonometrik işlemler için dizayn edildiği çok açık ve GPU için bir ön işlemci olarak hizmet edebilir. Genellikle bu sebepten, meşhur Görüntü Listeleri'ni teslim etmekle yükümlendirilir.

### Sonsuz dünyalar {.tabs-close}

Bu birimlerden kullanışlı bir şekilde istifâde etme yöntemi de **prosedürel oluşturmadır**. Diğer bir ifadeyle, sahneyi, önceden kodlanmış geometri (yer ve nesne şekilleri) ile oluşturmak yerine, VPU'nun bunu algoritmalar kullanarak oluşturmasına izin vermektir. Bu durumda VPU, **geometriyi matematiksel fonksiyonlar hesaplayarak oluşturur** ve daha sonra bunlar GPU tarafından çevrilip (örn. üçgenler, çizgiler ve dörtgenlere), en nihayetinde sahneyi çizmek için kullanılır.

Kesin bir biçimde yazılmış verileri kullanmakla kıyaslandığında prosedürel içerik, paralelize edilmiş görevler için idealdir, çok az depolama gerektirir ve değişkendir (programlayıcılar parametrelerle oynayarak farklı sonuçlar elde edebilir) [@cpu-green]. Pek çok alan bu teknikten son derece yarar sağlayabilir:

- **Karmaşık yüzeyler** (örn. küre ve tekerlek).
- **World rendering** (örn. arazi, partiküller, ağaçlar).
- **Bézier eğrileri**, bilgisayar grafiklerinde eğri çizmek için kullanılan yaygın bir denklem. Bunlar bir <strong x-id=“1”>Bézier yamasına</strong> (açık geometri) dönüştürülür ve gereken ayrıntı düzeyine bağlı olarak farklı hassasiyet derecelerini destekler.

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

Bu yaklaşımla, CPU büyük ölçüde yükten kurtuldu ve yapay zeka ve fizik işleme gibi diğer işlemleri halledebildi.

Başka bir çok örnek anlatılabilir ancak işin özü: Şu halde en uygun düzeni oluşturmak programlayıcıya bağlı ve bu da iyi bir şey.

## Grafikler

Emotion Engine tarafından yapılan tüm bu işler göz önüne alındığında, geriye bir şey kaldı mı? İşte kalan son adım: Görüntüleme!

![Final Fantasy X (2001).](ffx.jpg) {.open-float}

Tam da bu iş için özelleştirilmiş, basit fakat hızlı bir çip mevcuttur: **Grafik Sentezleyici (Graphics Synthesizer)** ya da 'GS' ve bu **~147.46 MHz**'de çalışıyor. Görüntü işlemenin tümünü kendi içinde halledebilmek için **4 MB DRAM**'i gömülü olarak içerir. Böylece, ana hafızaya erişim ihtiyacı ortadan kalkar. Gömülü RAM (eDRAM), ihtiyaç duyulan veri türüne göre farklı veri yollarıyla bağlanmıştır.

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

- **Digital Audio**: Sony/Philips Interface ya da 'S/PDIF' olarak bilinir.
- **Analog Ses**: Dijital'den analog'a çeviriciden geçip Multi A/V çıkışında sona erer.

## Giriş/Çıkış

Başlangıçta, PS2'nin I/O'su özellikle karmaşık değildi. Ancak, bu konsolun sonraki revizyonları hem iç hem de dış tasarımları tamamen bozdu. Dolayısıyla, genel olarak, bu konsol farklı revizyonlara dağıtılmış birçok G/Ç biçimi sergilemektedir.

### Özel İşlemci

Başlangıç olarak, farklı birimler arsındaki iletişimi sağlayan ayrılmış bir işlemci bulunur ve bu işlemci, **PlayStation 1**'de bulunan [orijinal MIPS R3000 tabanlı çekirdekten](playstation#cpu) başkası değildir. Bu kez **I/O Processor** (IOP) olarak adlandırılıyor ve <strong x-id=“1”>37,5 MHz</strong> hızında <strong x-id=“1”>32-bit veri yoluna</strong> bağlı olarak çalışıyor [@io-buses].

![Playstation 2'nin mimarisinin ana şeması. I/O İşlemcisinin I/O'nun çoğuna özel erişim gösterdiğine dikkat edin.](diagram.png)

IOP, **Sistem Arayüzü (System Interface)** ya da 'SIF' denilen özelleştirilmiş bir Giriş/Çıkış arayüzü kullanarak Emotion Engine'le iletişim kuruyor ve iki uç da birbirine veri transferi yapmak için kendi DMA birimlerini kullanıyor. IOP ayrıca tampon olarak kullanılan <strong x-id=“1”>2 MB <a href=“playstation#the-offering”>EDO RAM</a></strong> (tıpkı PS1'de olduğu gibi) özel belleğe sahiptir.

Sonuç olarak, bu işlemci ön bağlantı noktalarına, DVD denetleyicisine, SPU2'ye, BIOS ROM'a ve PC kartı yuvasına erişim sağlar.

Her ne olursa olsun, 'Slim' revizyonunun gelmesinden bir yıl sonra (2005), IOP yerine <strong x-id=“1”>PowerPC 401 'Deckard'</strong> (mikrodenetleyiciler için kesilmiş bir PowerPC 601), <strong x-id=“1”>4 MB SDRAM</strong> (öncekinden 2 MB daha fazla) ve bir Ethernet alıcı-vericisi (daha önce harici bir aksesuarda bulunan) içeren bir SoC yerleştirildi.

#### Geriye uyumluluk

Önceki modelin CPU'sunu içeren modeller için PS1 uyumluluğunun da paketin bir parçası olacağı düşünülebilir. Sony, uygun bir şekilde, bir PS1 diski takıldığında yüklenen bir PS1 emülatörü (`PS1DRV` olarak adlandırılır) ile paketlemiştir. Bu gerçekleştiğinde, IOP PS1 hızında çalışmak için düşük hızda çalıştırılır, EE <a href=“playstation#graphics”>eski GPU</a>'yu taklit etmek için 'yeniden kullanılır' ve SPU2 <a href=“playstation#audio”>orijinal SPU</a> gibi davranmak için yeniden eşlenir.

PowerPC tabanlı modellerde geriye dönük uyumluluk devam etti, ancak bunun yerine tam bir yazılım uygulaması yapıldı.

### Mevcut arayüzler

Konsol, orijinal PlayStation'da bulunan [eski ön dış bağlantı noktalarını](playstation#front-ports) barındırdı ve ayrıca ilk bakışta ümit verici görünen birtakım 'deneysel' arayüzler de sundu.

![Kontrolörler ve Hafıza Kartları dahil olmak üzere ortak bağlantı noktalarını gösteren PS2'nin ön yüzü. Ayrıca, yeni USB'ler ve i.Link bağlantı noktaları [@photography-amos].](photos/ps2_front.png)

En meşhur ilave ise: **İki adet USB 1.1 yuvası**. Teorik hızı 12 Mbps'dir, ancak bu büyük ölçüde IOP'nin bant genişliğine bağlıdır (ki bu oldukça yavaş olma eğilimindedir). Bununla birlikte, üçüncü taraf aksesuarlar tarafından yaygın olarak benimsenmiştir.

Çok uzun sürmeyen bazı 'deneyler' de oldu. İlk olarak, başlangıçta bir dış **i.Link portu** yuvası bulunuyordu (ayrıca IEEE 1394 ya da Apple camiasında 'FireWire' olarak da bilinir). Bu yuva, yerel çok oyunculuyu mümkün kılmak için iki PS2'yi bağlamada kullanıldı ancak üçüncü revizyonla kaldırılı (muhtemelen yerini 'Network card'a bıraktı, aşağıda daha fazla detay var).

#### Alışılmadık Ethernet + HDD kombinasyonu

Konsolun arkasında ayrıca <strong x-id=“1”>PC kartları</strong> için bir yuva vardı. Bunun için Sony'den iki ekstra arayüz sağlayan 'Ağ Adaptörü kartı' satın alabilirsiniz:

- Çevrimiçi çok oyunculu oyun için bir <strong x-id=“1”>Ethernet</strong> bağlantı noktası.
- Tescilli ve harici bir <strong x-id=“1”>Hard Disk Drive Unit</strong> bağlantı noktası: Bu Sony tarafından satılıyordu ve 40 GB alana sahip tipik bir 3,5” ATA sabit disk sürücüsü içeriyordu. Bir sürücüye sahip olmak, daha hızlı yükleme süreleri için oyunların geçici verileri depolamasına (ya da kalıcı olarak burada kurulmalarına) imkan tanır. Gerçi bu özelliği sadece birkaç oyun kullanmıştır.

Sonraki revizyonlarda PCMCIA yuvası, konsola 3.5 inçlik bir Hard disk sürücüsü sığdırılabilecek bir **Expansion Bay** ile değiştirildi. Öncelikle, sadece Modem ve/veya Ethernet girişlerini (modele göre değişir) bulundurmakla kalmayıp aynı zamanda bir ATA-66 hard disk için gerekli bağlantıları da bulunduran bir **Network adaptor** almanız gerekliydi.

![PS2'nin arkasındaki Sabit disk bölmesi (kapağı çıkarılmış olarak) gösteriliyor [@photography-amos].](photos/back_bay.png)

Perde arkasında, HDD'deki veriler 'PFS' [@io-fs] adı verilen bir dosya sistemi kullanılarak yapılandırılır. Garip bir şekilde, düzen bir bölümleme tablosu içermez, sadece 'Aligned Partition Allocation' (APA) adı verilen çok ilkel bir katalog içerir. Bunun nedeni Sony'nin yalnızca 40 GB sürücüler göndermesi olabilir. Dolayısıyla, ölçeklenebilirlik öncelikler listesinde yer almıyordu.

![Ağ adaptörünün (Network adaptor) önden görünümü [@photography-amos]. Bu özel model, modem ve Ethernet girişleri barındırdı.](photos/harddrive_adaptor_front.png) {.toleft.no-borders}

![Ağ adaptörü (Network adaptor) arkadan görünüşü [@photography-amos], bir sabit disk takılmış hâlde.](photos/harddrive_adaptor_back.png) {.toright.no-borders}

Ethernet alıcı/vericisi, 100 Mbps'ye (12.5 MB/s) kadar transfer hızlarını destekler. Ancak **gözlemlenen hız düşüklüğü kötü şöhretle bilinir** (bazı durumlarda 2 MB/s'ye kadar düşer). Bunun açıklaması ise görece basittir: Kullanılabilir iletişim ağına (network) erişmek için standart 'OSI Modeli'nin tüm katmanları uygulanmalıdır, alıcı/verici ise bütünün sadece bir parçası. İşin geri kalanı genel olarak IOP'nin emrindedir (ki yazılımla yapılır) fakat IOP'nin sınırlı performansı yüzünden [@io-bottleneck], darboğaz meydana gelir.

#### İnceltme

Slim revizyonu tüm Ethernet + HDD modelini revize etti: artık Expansion Bay yok, ancak arkaya kalıcı olarak bir ethernet bağlantı noktası takıldı (belirli modellerde bir Modem de vardı).

![Slim modelinin arkasındaki sabit Ethernet yuvası gösteriliyor.](photos/ps2_slim_back.jpg)

Buna ek olarak yeni revizyon, Sony markalı bir uzaktan kumanda (ayrı satılır) ile kullanılmak üzere yeni bir <strong x-id=“1”>infrared sensor</strong> ekledi.

### Etkileşimli eklentiler

Kontrolcülerinin (oyun kolu) yeni versiyonu olan **DualShock 2**, DualShock'un biraz geliştirilmiş versiyonudur.

![DualShock 2 kontrolcüsü [@photography-amos].](photos/dualshock2.png) {.toleft.no-borders}

![Orijinal Hafıza Kartı (8 MB modeli) [@photography-amos].](photos/memorycard.png) {.toright.no-borders}

Orijinal PlayStation zamanlarında, orijinal kontrolcünün farklı özellikler barındıran birçok revizyonu piyasaya sürüldü (aynı zamanda pazara ayrışma getirdi). Artık geliştiriciler lehine, öncesinde gelen bütün özellikleri kendinde toplayan tek bir kontrolcü vardır.

Orijinal DualShock ile karşılaştırıldığında, yeni versiyon hafif bir yeniden tasarıma sahiptir, sırasıyla daha zengin giriş ve geri bildirim için iki analog çubuk ve iki titreşim motoru içerir.

Kontrolcü giriş yuvasının yanındaysa PS1 ve PS2 kartlarıyla uyumlu **Hafıza kartı yuvası** bulunuyor. Yeni kartlara, **MagicGate** olarak anılan güvenlik sebepleriyle ekstra devre gömüldü, bu da oyunların, farklı hafıza kartlarına veri transferini engelleyebilmesine imkan tanıdı. IOP, içeriğin şifrelenmesi ve şifresinin çözülmesiyle ilgilenir ve bunu MagicGate yonga seti (Hafıza Kartının içinde bulunur) ve DVD sürücüsünün yardımıyla yapar, ikincisi şifreleme anahtarlarını paketler.

Ancak bazı üçüncü taraf Memory Card'ları MagicGate'i desteklemiyordu.

## İşletim Sistemi

Anakarta takılı bir <strong x-id=“1”>4 MB ROM</strong> yongası var, bu bir kabuk menüsü (kullanıcıların etkileşime girebileceği) yüklemek için kullanılan büyük miktarda kodu depolar, ancak aynı zamanda I / O erişimini basitleştirmek için sistem çağrıları sağlar \[@cpu-rockin\] (oyunların dayandığı).

![Konsol başlatıldıktan sonra görünen açılış animasyonu.](bios/animated.jpg){.tabs-nested .active title="Önyükleme animasyonu"}

![Geçerli bir PS2 oyunu takıldıktan sonra PS2 logosu gösteriliyor.](bios/game_splash.jpg){.tabs-nested-last title="Geçerli disk"}

Güç verildiğinde, hem MIPS R5900 hem de IOP `0xBFC00000` adresinden başlayacaktır (bu, tüm MIPS CPU'ları için imza sıfırlama vektörüdür). Bununla birlikte, beklenen çakışmaları ele almak için, bu adreste saklanan ilgili kod (BIOS ROM'a işaret eder), her CPU'nun tanımlayıcısına göre farklı bir konumda dallanmasını sağlayacaktır.

R5900 söz konusu olduğunda, CPU aşağıdaki adımları izleyecektir [@cpu-rockin]:

1. Donanımı ilk kullanıma hazırlar.
2. <strong x-id=“1”>Kernel</strong>'i ROM'dan RAM'e yükler. Çekirdek yüklendikten sonra, uygulamaların (çoğunlukla oyunların) donanımla etkileşime girmesi için bir soyutlama katmanı sağlayacaktır. Ayrıca, bir çoklu iş parçacığı API'si (işbirlikçi ve öncelik tabanlı) de sunar.
3. Çekirdek, bir çekirdek modülü olan `EELOAD`'u yükler ve bu da `OSDSYS`'i önyükler. `OSDSYS`, açılış animasyonunu ve shell menüsünü görüntüleyen programdır.

Öte yandan, IOP donanımının bir kısmını başlatacak ve ardından birden fazla <strong x-id=“1”>modül</strong> yükleyecektir, bunlar IOP'nin bu konsolun donanımına erişmesini sağlar. Son olarak, IOP bir 'komut bekleme' hâline geçirilir.

Modül kullanımının Sony'nin PS2'nin yeni donanım revizyonlarını IOP'yi değiştirmeden (değiştirene kadar) piyasaya sürmesine olanak sağladığını ve böylece bu süreçte bazı üretim maliyetlerini düşürdüğünü belirtmek gerekir.

### Etkileşimli kabuk (Interactive shell)

PS2 shell işlevselliği, diğer 6. nesil konsollarla hemen hemen aynı düzeydedir.

![İlk gösterilen menü. Herhangi bir disk yerleştirilmediğinde ortaya çıkar.](bios/menu.jpg){.tabs-nested .active title="Menü"}

![Hafıza Kartı tarayıcısı.](bios/mem_list.jpg){.tab-nested title="Tarayıcı"}

![Kayıt tarayıcısı. Bir hafıza kartı seçildiğinde ortaya çıkar.](bios/save_list.jpg){.tab-nested title="Kayıtlar"}

![Kayıt düzenleyici, bir kaydın seçiminden sonra gösterilir.](bios/save_editor.jpg){.tab-nested title="Düzenleyici"}

![Sistem Yapılandırma.](bios/options.jpg){.tabs-nested-last title="Seçenekler"}

Shell, hafıza kartında saklanan kaydetme verilerini değiştirmek veya saati değiştirmek gibi tipik işlemleri yönetmek için birden fazla kullanıcı arayüzünden oluşur. Bununla birlikte, bazı fevkalade seçenekler de sunar, meselâ geçerli video modunu değiştirmek gibi.

### Güncellenebilirlik

BIOS gerçekten de salt okunur bellekte saklanır, ancak bu Sony'nin fabrikadan çıktıktan sonra BIOS'u değiştirmesini engellememiştir. BIOS, perde arkasında gelecekte yapılacak değişiklikler için iki açık kapı bırakmıştır:

- Uygulamalar (oyunlar ve `OSDSYS`) <strong x-id=“1”>çekirdek rutinlerini çalışma zamanında</strong> yamalayabilir [@operating_system-kpatch]. Hem resmi SDK hem de resmi olmayan 'ps2sdk' bundan geniş ölçüde yararlandı, çünkü Sony mühendisleri daha sonra çekirdeklerinin hatalarla dolu olduğunu keşfettiler (<em x-id=“4”>kasıtlı olarak</em>).
- **`EELOAD`, Bellek Kartında ve/veya HDD'de [@operating_system-israelson] saklanan güncellenmiş bir `OSDSYS` ikili dosyasını arayacaktır**. Sony, DVD film oynatıcı ve HDD desteği eklemek için buna güveniyordu, çünkü bu sürücülerin hiçbiri bu konsolun önceki revizyonlarında paketlenmemişti.
  - Bu güncellemeler konsolla veya HDD kitiyle (Final Fantasy XI kutu setinin bir parçası) birlikte gelen yükleme disklerinde dağıtıldı.
  - Eski SDK'larla geriye dönük olarak uyumlu olması gereken çekirdeğin aksine, konsolun sonraki revizyonları BIOS ROM'unda önceki güncellemeleri gönderdi.
  - Dağıtımı kontrol etmek için ikili dosyalar, yalnızca Sony tarafından bilinen anahtarlar kullanılarak (teoride) simetrik bir şifreleme sistemi olan <strong x-id=“1”>Data Encryption Standard</strong> (DES) [@operating_system-kelftool] ile imzalanmalıdır. Ayrıca, ikili dosyalar MagicGate desteği olan bir depolama aygıtında saklanmalıdır.

Her halükarda, Sony sonunda geç PS2 modellerinde (BIOS sürümü `2.30` ile) ikinci yöntemi kaldırdı. Sanırım Sony daha fazla güncelleme planlamıyordu ve bu sadece saldırı yüzeyini artırdı.

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

Donanım tarafında Sony, oyunları kurum içi çalıştırma ve hata ayıklama için stüdyolara mahsus donanım sağladı. İlk geliştirme kitleri, PS2'nin pisayasa sürülmemiş donanımını replika etmek için birarada yığılmış kasasız kartlardı. Sonraki kitler (**Development Tool** yani Geliştirme Araçları adını alanlar), daha tatmin edici bir görünüşe, gelişmiş Giriş/Çıkışa (I/O) ve oyunları aynı şartlarda geliştirip dağıtma için PS2 donanımıyla çalışan bileşik iş yeri donanımına (RedHat 5.2 çalıştırır) sahipti.

Geliştirme kiti (DevKit), resmî SDK ve CodeWarrior (meşhur bir IDE yani bütünleşik geliştirme ortamıdır) birleşimi, kullanılan en popüler düzeneklerdendi.

### Medya Ortamı

Disk sürücü, hem DVD hem de CD okuyabilir yani oyunlar her iki fiziksel formatla da dağıtılabilir. Ancak bariz sebeplerden ötürü, çoğu oyunu DVD formatında bulacaksınız.

![Kingdom Hearts II (2005). Tipik perâkende oyun kutusu ve diski.](kh2_box.jpeg) {.open-float}

DVD'ler, **DVD-5** (en yaygın 'alt format') durumunda **4,7 GB** veya **DVD-9** (çift katmanlı versiyon, daha az yaygın) durumunda **8,5 GB** veri tutabilir [@games-dvd]. Aslında üçüncü bir format olan çift taraflı **DVD-10** da vardır ancak hiçbir oyun bunu kullanmadı.

Kullanılan medya türü sayesinde, sadece oyunlar değil, filmler de oynatılabiliyordu. Yine de, film formatında bir DVD'yi okuyabilmek için ilgili kod çözücü gerekiyordu ve bunun için, PS2 ilk zamanlarda gerekli dijital aracı bir hafıza kartına (PS2 Memory Card) dahil etmişti (sonuçta hafıza kartı da bir depolama medyası/ortamıdır) fakat sonraki modeller ise BIOS ROM'a halihazırda kurulmuş bir DVD yazılımı ile çıktı.

{.close-float}

Hızlarına gelince, CD-ROM'lar 24x hızda (yani 3.6 MB/sn) okunuyordu ve DVD-ROM'lar 4x hızda (5.28 MB/sn) okunuyordu [@games-transfer].

### Ağ hizmeti

Görmüş olduğunuz üzere, konsolun genel ağ özellikleri, ilk piyasaya sürülüşünden dört yıl sonraki revizyonlara kadar standarda kavuşmamıştı. Benzer şekilde, eğer oyun stüdyoları çevrim içi hizmetler (çok oyunculu gibi) sağlamak istiyorlarsa, gerekli altyapıyı sağlamakla yükümlü idiler. Sonraki yıllarda Sony, **Dinamik Ağ Kimliği Doğrulama Sistemi (Dynamic Network Authentication System)** ya da 'DNAS'ı işleve soktu, bu çevrim içi bir hizmet değildi fakat korsan oyunların çevrim içine bağlanmasını engelleyen bir doğrulama sistemiydi.

### Alışılmadık bir oyun türü

Sony, *güzel grafikli* tüm bu oyunların yanı sıra, 'Kondara' tabanlı (ki bu da Red Hat 6 tabanlıdır) bir Linux dağıtımını iki CD halinde (ilk disk 'Runtime Environment' yani program çalıştırma ortamı ve ikincisi 'Software Packages' yani yazılım paketleridir), VGA adaptörü, USB Klavye ve Fare ve ek olarak bazı geliştirici kılavuzları ile birlikte piyasaya sürdü. Paket, **Linux Kit** olarak bilinirdi ve ilk DVD'yi başlatarak işletim sistemini çalıştırabilir ve herhangi bir *old school* Linux ortamı gibi devam edebilirdiniz. Tabii ki Linux dağıtımını kurmak için konsola bir sabit disk (Hard drive) takmanız gerekirdi. Kurulduktan sonra, bu işletim sistemini başlatmak için her zaman ilk DVD'ye ihtiyaç vardı.

Linux Kit'e, EE'ye yönelik (glibc 2.2.2 ile gcc 2.95.2) derleyiciler (compilers), vektör birimlerine yönelik talimat derleyiciler (assemblers) ve beraberinde Grafik Sentezleyici'de hızlandırılmış bir pencere sistemi (XFree86 3.3.6) [@games-linux]. Genel anlamda bu, kulağa ilginç bir ortam gibi geliyor. Aslında, okuduğum araştırma makalelerinin biri bu kurulumla oluşturulmuştu.

## Korsanla Mücadele ve Ev Yapımı (Homebrew)

Bu alanda konuşulacak çok şey var, hadi DVD okuyucuyla başlayalım, olur mu?

### DVD kopya koruması

Bu alan özellikle oyun stüdyoları için endişe vericiydi, çünkü bu konsol oyunları dağıtmak için çok uygun fiyatlı bir disk formatı kullanıyordu. Dolayısıyla korsanlık riski yüksekti.

![Bu ekran, sürücü arızalıysa görülebilir... ya da korsan bir kopya takıldıysa.](bios/rsod.jpg)

İşletim sistemi, bir oyunu açma işini DVD okuyucuya özel komutlar göndererek yapar. Özel olarak oyun içeriğini okumak için kullanılan komutlar, standart DVD komutlarından (yani DVD filmi okunmasından) çok farklı davranır. Tescilli oyunların, diskin daha içeri kısımlarında, dosya sisteminin (filesystem) adını, konumunu ve boyutunu işaretleyen ve erişim alanının dışında kalan bir 'yerleştirme dosyası' (map file) bulundurduğu ortaya çıktı. DVD'den bir oyun diskini okuması istendiğinde, diskte her zaman yerleştirme dosyası (map file) yardımıyla yön bulur; bu da demektir ki yerleştirme dosyasını bulundurmayan bir korsan kopyanın okunması imkânsızdır.

Bu, başka bir bölgenin konsolunda çalışan ithal oyunların çalıştırılmasını engelleyen bir bölge kilidi sistemiyle bütünleşmiştir.

### Korumaları atlatmak

Bu konsola dair en önemli bölümü açıkladık, şimdi de koruma mekanizmalarını atlatmayı başaran keşfedilmiş birkaç yönteme göz atalım.

#### DVD sürücüsüne saldırma

PS2 mağazalara ulaşır ulaşmaz, DVD sürücüsünün 'kilidini açma' vaadiyle çok sayıda üçüncü parti ürün ortaya çıktı. Homebrew'u destekleyecek hiçbir işaret olmadığından (Linux çözümü dışında), korsanlık en büyük faydalanıcı oldu.

##### Modifikasyon Çipleri (Modchips) {.tabs.active}

Kendi neslinde (ve öncekilerde) disk tabanlı sistemler kullanan diğer konsollardaki gibi, DVD yardımcı sisteminin üçüncü taraf şirketlerce tersine mühendisliğe uğraması an meselesiydi. Buradaki hedef, sürücüyü, erişim alanı dışındaki yerleştirme dosyasını kullanmayı gerektirmeden dosya sisteminde gezinmeye zorlayacak kullanışlı bir açık yakalamaktı.

Sonunda bu, aynı zamanda bölge kilidi kısıtlamalarını da askıya alan **modchipler** şeklinde gerçekleşti.

##### Hileler {.tab}

Takılması için lehim kabiliyeti gerektiren modchiplerin yanı sıra, gayriresmî fakat 'orijinal' diskler pazarda yerini aldı. Bunlar, bölge korumasını kaldırmak ve oyun içi hileleri kullanmak için Çekirdeği yamaladı.

Dahası, 'hile diskleri', konsolun modifiye edilmesini gerektirmeme avantajına da sahipti. Düşünceme göre bu bahse en iyi örnek *CodeBreaker*'dır.

#### Disk değiştirme {.tab}

Son gelişmeler arasında bir hileli teknik daha ortaya çıktı. Bu sefer, okuyucunun hatalı sektörlere müdahale davranışından faydalanılıyor. **Swap Magic** bir diğer 'orijinal' disk gibi görünüyor ancak içindeki 'oyun' DVD'ye kasten yapılmış bir hatalı sektördeki, aslında var olmayan bir çalıştırılabilir dosyayı okumasını söylüyor, böylece sürücüyü büsbütün donduruyor [@anti_piracy-hacking]. Kullanıclar için mevcut diski 'orijinal' olmayan bir diskle değiştirebilecekleri bir fırsat penceresi aralanıyor. Sonrasında, hâlen hafızada bulunan Swap Magic, yeni diskin ana çalıştırılabilir dosyasını önyüklüyor ve sonunda gerçek bir oyun açılıyor. Tüm bunlar, sürücü hâlâ orijinal bir diskin takılı olduğunu zannederken gerçekleşiyor.

Bu işlem, illâki konsolda bir değişiklik yapılmasını gerektirmez. Fakat modele bağlı olarak, sürücünün disk çıkarma sensörünü bloke edecek şekilde, PS2'nin dış kasasının kurcalanması gerekebilir. Bazı modellerde, pamuk parçalarının belirli yerlere yerleştirilmesi de adım adım ilerlemenin bir parçasıydı.

#### Modçiplerden ayrılma {.tabs-close}

Zaman geçtikçe, bu konsol hakkında daha fazla araştırma toplandı ve paylaşıldı. Sonuç olarak, yeni ve daha sofistike keşifler, en azından temel olarak artık harici donanıma dayanmayan yeni bir geliştirme dalgasına yol açtı. Dahası, korsanlık artık ana odak noktası değildi. Bunun yerine, Sony'nin onayı olmadan üçüncü taraf programları çalıştırma yeteneği (<strong x-id=“1”>Homebrew</strong> olarak adlandırılır) hızla hedefler listesinin zirvesine yerleşti.

#### Independence overflow {.tabs.active}

PS2, PS1 oyunlarının emülasyonunu optimize ederken kullanılacak bilgileri içeren, `TITLE.DB` adı verilen bir veri tabanı dosyasını Hafıza Kartında (MemoryCard) saklar [@anti_piracy-grand]. İşletim sistemi, bir PS1 oyunu yerleştirildiğinde veri tabanı dosyasını getirir ve bütün dosyayı hafızadaki sabit bir adrese açar (*ihmâl bir*). Bilgi derleyici, C'de dizileri (harf ve diğer karakterler zinciri) bir yerden ötekine kopyalamaya yarayan **`strncpy()`** fonksiyonu kullanılarak uygulandı.

C'ye aşina olanlarınız, muhtemelen nereye varacağımı tahmin ettiniz. Olay şu ki `strncpy()`, bir dizi ne kadar uzun bilmiyor, yani (zincirin sonuna `\0` yazmak suretiyle) bitirilmediği sürece kopyalama 'sonsuza dek' devam edecek (ve öngörülemeyen sonuçları olacak!). Şanslarına bu fonksiyon, tercihen kullanılabilen, kopyalanacak maksimum byte sayısını belirleyen bir parametre içeriyor ve böylece kopyayı arabellek taşmalarından koruyor. Fakat şu gülünç gelebilir ve öyledir ki **Sony, bu parametreyi kullanmadı**, hem de her veri tabanı girdisinin 256 byte'lık sabit bir boyuta sahip olmasına rağmen (*ihmal iki*).

RAM'de daha yakından incelendiğinde, `TITLE.DB`, yürütülmekte olan mevcut işlev bittikten sonra geri dönülecek adresi belirten **kayıtlı bir register** olan `$ra`'nın yanına kopyalanır (*üçüncü vuruş*), bu da **The Independence Exploit**'e [@anti_piracy-independence] yol açar: Büyük bir string içeren bir `TITLE.DB` oluşturun, içine bir çalıştırılabilir dosya yerleştirin ve bu stringi `$ra`'nın çalıştırılabilir dosyayı gösterecek şekilde geçersiz kılınacağı şekilde tasarlayın. Bu dosyayı Hafıza Kartınıza yüklemeyi başarırsanız (başka bir istismar veya PC USB adaptörü aracılığıyla) kendinize basit bir <strong x-id=“1”>Homebrew launcher</strong> elde etmiş olursunuz.

Bu keşif 2003 yılında yayımlanmıştır. Sonuç olarak, ince revizyonla birlikte Sony, bu açığı yamalayan yeni bir BIOS ROM revizyonu gönderdi. İlginçtir ki bu, acemice kod meydana getiren son [falso](wii#the-fall-of-encryption) değildi.

#### The signature exploit {.tab}

Kasım 2007'de bir bilgisayar korsanlığı grubu **Memor32** [@anti_piracy-fmcb] adında tipik bir üçüncü taraf Hafıza Kartı satmaya başladı, ancak bu kartta nedense bir <strong x-id=“1”>FPGA</strong> ve bir <strong x-id=“1”>USB portu</strong> bulunuyordu. İnternet forumlarında <strong x-id=“1”>Memento</strong> adlı bir aygıt yazılımı ortaya çıkana kadar Memor32'nin gerçek doğası netleşmedi: Independence istismarında olduğu gibi hafıza kartından yetkisiz çalıştırılabilir dosyalar çalıştırmak.

Memento'nun uygulanması DVD oynatıcının imzasının kontrol edilme şeklindeki bir kusura dayanıyordu. İkililerin Sony'nin anahtarları kullanılarak imzalanması gerekirken <strong x-id=“1”>ikilinin bütünlüğünün kontrol edilmediği</strong> keşfedildi. Dolayısıyla, herhangi biri çalıştırılabilir kodu başka bir şeyle (hala aynı alana sığan) değiştirebilir ve işletim sistemi memnuniyetle çalıştıracaktır. Memento aygıt yazılımı bu istismarı kullanarak yükünü DVD oynatıcı içinde gizlemiş ve kullanıcıların oyunları (disk sürücüsünden ya da HDD'den) yüklemelerine olanak tanıyan birkaç yardımcı program eklemiştir.

Ancak Memor32 ve Memento'nun popülerliği, ücretsiz (ve başlangıçta açık kaynaklı olan) bir alternatifin ortaya çıkmasıyla kısa sürede yer değiştirdi: **FreeMCBoot**.

#### Evrensel çözüm {.tab}

Memento'nun ters mühendisliği yapıldıktan sonra, Memor32 gerektirmeyen bir alternatif internette ortaya çıktı. <strong x-id=“1”>FreeMCBoot</strong>, <strong x-id=“1”>herhangi bir MagicGate Hafıza Kartına</strong> yüklenebilmesi dışında aynı güvenlik açığından yararlanmıştır. Tek dezavantajı, yükleyiciyi başlatmak için hala başka bir açık (örneğin disk değiştirme) gerekmesiydi.

İlginçtir ki, FreeMCBoot'un kullanıcı arayüzü `OSDSYS`'den varlıkları ödünç alır, böylece diğer Homebrew'u başlatmak için tanıdık bir menü sağlar. Ayrıca, USB 1.1 bağlantı noktalarındaki yığın depolama aygıtlarına erişmek için API'ler eklemek üzere çekirdeğe yama yapar; bu, birçok homebrew uygulamasının ek dosyaları bulmak için güvendiği bir şeydir.

Ek olarak, yükleyici iki seçenek sunar: yalnızca mevcut konsol için gereken dosyaları yüklemek veya tüm PS2 varyantları için genel bir kurulum yüklemek. İlginçtir ki, ikinci seçeneği gerçekleştirmek oldukça zordu [@anti_piracy-fmcb]. Başlangıçta, yükleyici, alanın tükenmesini önlemek için Hafıza Kartının bölümleme tablosuyla oynayacaktı, bu özellikle güvenli olmayan bir şeydi.

Şans eseri 2011 yılında <a href=“playstation-3#os-security-hierarchy”>PlayStation 3'ün</a> <a href=“playstation-3”>güvenlik sistemi</a> [ele geçirildi](playstation-3#tab-17-4-the-fall-of-encryption) ve içinde saklı birçok sır açığa çıktı. Diğerlerinin yanı sıra, <a href=“playstation-3#backwards-compatibility”>PS2 geri uyumluluğu</a> için global olarak kullanılan MagicGate anahtarları koleksiyonu bulunmaktadır. O andan itibaren, PS2 çalıştırılabilir dosyaları oluşturmak için sınırlı DVD ikili imza hilesine başvurmak artık gerekli değildi. Ve böylece, `1.8b` sürümünden bu yana FreeMCBoot, PlayStation 2'de her türlü Homebrew'u çalıştırmak için en güvenli ve en popüler yöntem olarak konumunu korumuştur.

### Takip eden gelişmeler {.tabs-close}

Homebrew'u çalıştırma yolu daha erişilebilir hale geldiğinde, ilerleme önceki açıkların parlatılması ve Homebrew uygulamalarının geliştirilmesi şeklinde devam etti. Sonunculardan bazıları nihayetinde korsanlıkla ilgili işlevselliği kolaylaştırdı, ancak aynı zamanda işletim sisteminin sınırlı yeteneklerini de genişlettiler (örneğin, oyunlara yamalar sağlayarak), sanırım sonuçta kullanıcılarının niyetlerine bağlıydı.

Homebrew ile ilgili gelişmelerden birkaç önemli örnek vermek gerekirse:

- <strong x-id=“1”>FreeHDBoot</strong>: FreeMCBoot'un bunun yerine sistemin de önyükleme yapmaya çalışacağı HDD'ye yüklenen bir çeşidi.
- <strong x-id=“1”>ps2sdk</strong> ps2dev grubu tarafından [@anti_piracy-ps2sdk]: Telif hakkı ihlali riski olmadan PS2 Homebrew yazmak için resmi olmayan SDK. Proje, yalnızca belirli bileşenlere [@anti_piracy-ps2sdk_history] erişmek için gevşek kütüphaneler biçiminde bulunduğu ve zamanla Homebrew geliştiricilerinin yararına tek bir pakette birleştirildiği 2000 yılına dayanmaktadır.
- <strong x-id=“1”>LaunchELF</strong> birden fazla yazar tarafından [@anti_piracy-launchelf]: Ekstra yardımcı programlara sahip bir dosya yöneticisi. Daha sonra 'uLaunchELF' ve son olarak 'wLaunchELF' olarak devam etti.
- <strong x-id=“1”>Open USB Loader</strong> (OPL) birden fazla yazar tarafından [@anti_piracy-opl]: Disk görüntülerini birden fazla kaynaktan (HDD, USB, i.Link ve hatta Ethernet üzerinden SMB ve NMB) yama yetenekleriyle önyüklemeyi sağlar.
- Ffgriever [@anti_piracy-esr] tarafından **ESR**: Orijinal adı 'Vast CDVDV' olan bu uygulama, tüm sistemi (DVD sürücüsü de dahil olmak üzere) donanım değişikliği yapmadan bir oyunun yakılmış bir kopyasını çalıştırması için kandıran bir uygulamadır. ESR, yürütmeyi gerçek oyuna yönlendirmek için Emotion Engine ve IOP'nin kontrolünü ele geçirirken, orijinal oyun içeriği kendisini DVD filmi olarak gizlemek için yamalanmalıdır (böylece DVD sürücüsü bunu reddetmez).

İlerleyen yıllarda yeni açıklar da ortaya çıkacaktır:

- Krat0s [@anti_piracy-fortuna] tarafından <strong x-id=“1”>Fortuna</strong>: 2019'da yayınlanan ve tamamen farklı bir istismara dayanan alternatif bir Homebrew başlatıcısı. Bu kez, kaydetme yöneticisinin simge ayrıştırıcısında (`OSDSYS`'nin bir parçası) bir buffer overflow var [@anti_piracy-opentuna]. Ana avantajı, harici `OSDSYS` güncellemeleri için desteği kaldıran (dolayısıyla FreeMCBoot ile uyumsuz) son PS2 modelleriyle uyumluluktur.
- alexparrado tarafından <strong x-id=“1”>OpenTuna</strong> [@anti_piracy-opentuna]: Fortuna'ya açık kaynaklı bir alternatif, tersine mühendislikten kaynaklandı.
- cturt tarafından <strong x-id=“1”>FreeDVDBoot</strong> [@anti_piracy-freedvdboot]: 2020'de yayınlanan yeni bir güvenlik açığı. DVD oynatıcıda rastgele kod yürütme gerçekleştirmek için kullanılabilecek yeni bir buffer overflow'dan oluşur. Kullanıcıların FreeMCBoot yükleyicisi ve hatta ESR dahil olmak üzere istedikleri herhangi bir ikili dosyayı çalıştırmak için FreeDVDBoot ile bir DVD yazmaları yeterlidir (Sony bunun yıllar önce keşfedilmediği için çok şanslıydı! [diğerleri gibi değildi](dreamcast#defeating-it)).

## Hepsi bu kadar

Makalenin sonuna ulaştığınız için sizi tebrik ediyor ve teşekkürlerimi sunuyorum! Açıkçası, bahsedilecek o kadar şey var ki, bunu bitirdikten sonra, okuyucular eninde sonunda PlayStation'la ilgili şeylerden bıkarlar mı, diye düşündüm.

Her neyse, bütün ciddiyetimle, umuyorum ki bu makaleyi okuduktan sonra yeni şeyler keşfetmişsinizdir ve olur da yorum yapmak isterseniz [benimle iletişime geçmekten](code>r ref(about_url, root = TRUE)</code) çekinmeyin.

Bir dahaki sefere kadar hoşça kalın!  
Rodrigo
