---
short_title: GameCube Mimarisi
long_title: GameCube Mimarisi
name: GameCube
release_date: 2001-09-14
date: 2019-11-19
subtitle: Güçlü ve kompakt
generation: 6
javascript:
  - 'threejs'
  - 'audioswitcher'
cover: gamecube
top_tabs:
  Model:
    file: international
    caption: "İlk ve tek GameCube.<br>14/09/2001 tarihinde Japonya'da, 18/11/2001 tarihinde Amerika'da ve 03/05/2002 tarihinde Avrupa'da piyasaya sürüldü."
  Motherboard:
    caption: "'DOL-CPU-10' modelimden alınmıştır, daha sonrakiler Seri Port 2 ve Dijital Çıkışı çıkarmıştır. Kodlayıcı yongası, genişletme, kontrolör ve PSU yuvaları diğer tarafta bulunur."
    bib_source: copetti
  Diagram:
    caption: "Her veri yolu genişliği ile etiketlenmiştir."
#Historical
aliases:
  - /projects/consoles/gamecube/
---

## Hızlı bir giriş

'3D denemeleri' günleri geride kaldı, Nintendo'nun yeni teklifi, yeni, orijinal ve görülmemiş içeriğe kapı açacak olan selefinden temiz ve güçlü bir kopuştan oluşuyor.

Bu mimarinin tasarımının, bu neslin en kompakt donanımlarından birini ortaya çıkardığını belirtmek gerekir. Bu durum, *slim* veya *lite* revizyonların olmamasıyla vurgulanmıştır.

## {.supporting-imagery}

## İşlemci (CPU)

SGI'ın grafik pazarındaki hakimiyetini kaybetmesinin ardından Nintendo'nun ortaklık kuracağı yeni oyunculara ihtiyacı vardı.

![Gekko'nun mimarisi.](cpu/cpu_features.png) {.open-float.no-borders}

Umut verici bir aday IBM gibi görünüyor: Ana bilgisayarlar üzerindeki ünlü çalışmalarının yanı sıra, yakın zamanda Motorola ve Apple ile Intel'in PC pazarındaki hakimiyetiyle rekabet edebilecek kadar güçlü bir CPU yaratmak için ittifak kurdular. Ortaya çıkan ürün, **PowerPC** adını taşıyan ve Apple'ın Macintosh'larının ve bazı gömülü sistemlerinin *%99'una güç vermek* üzere seçilen bir dizi işlemcidir.

Hızlı bir şekilde ilerlediğimizde, Nintendo'nun güçlü ama ucuz bir şeye ihtiyacı vardı, bu yüzden IBM bu kırmızı çizgilere uymak için eski tasarımlarından biri olan *PowerPC 750CXe*'yi (*Early-Summer 2001* olarak bilinen son iMac G3'te bulunan) aldı ve oyun geliştiricilerini memnun edecek yeteneklerle güçlendirdi. Sonuç **PowerPC Gekko** oldu ve **486 MHz** hızında çalışıyor.

{.close-float}

### Özellikler

Gekko'yu bu kadar özel yapan şeyin ne olduğunu bulalım ve bunu yapmak için önce 750CXe'nin sunduklarına bakmamız gerekiyor:

- **PowerPC ISA**: Fazla ayrıntıya girmeden, *başka* bir 32 bit RISC komut setidir. 750CXe v1.10 spesifikasyonunu uygular.
- Harici **64-bit veri yolu**: ISA 32 bitlik bir veri yoluna sığabilse de, daha geniş veri parçalarını (bir sonraki bölümde açıklanmıştır) performans cezalarına çarpmadan taşımamız gerekir.
- **Süper skaler**: Artık pipeline'nın her aşamasında birden fazla komut yürütülebilir.
- **Sıra dışı yürütme**: CPU, tüm birimlerinin çalışmasını sağlamak için talimat sırasını yeniden düzenleyebilir, böylece verimliliği ve performansı artırır.
- **İki Tamsayı Birimi**: Süper skaler ve sıra dışı ile birleştiğinde, temel olarak birim zamanda yapılan tamsayı işlemlerinin sayısını artırır.
- 32-bit ve 64-bit kayıtlara sahip **entegre FPU**: Kayan ve çift sayılarla yapılan işlemleri hızlandırır.
- **Dört aşamalı pipeline (bonus ile)**: [İşte](game-boy-advance#cpu) komut ardışık düzenine önceki bir giriş. 750CXe'de FPU işlemleri üç aşamaya daha bölünürken (toplamda 7 aşama), load-store işlemleri ikiye bölünmüştür (toplamda 5 aşama).
  - Sonuç olarak bu, komut verimini [kontrolden çıkmadan artırır](xbox#tab-1-3-the-microarchitecture).

Ek olarak ve RISC yapısı nedeniyle, bu CPU ayrıca belirli hesaplamaları hızlandırmak için özel birimler içerir [@cpu-stokes]:

- **Branch Prediction Unit**: Değerlendirilmesi gereken bir koşul olduğunda (CPU'nun 'A' yolunu mu yoksa 'B' yolunu mu izlemesi gerektiğine karar verecek olan), CPU bunun yerine önceki yürütmelere dayalı yollardan birini izleyecek ve ardından koşulu değerlendirecektir. Eğer tahmin doğru çıkarsa, CPU zaman kazanmış olacak, doğru çıkmazsa tersine dönecek ve doğru yolu izleyecektir.
- **Dedicated load-store unit**: Kayıtları işleyen birimler ile ana belleği işleyen birimleri birbirinden ayırır.
- **Entegre Bellek Yönetim Birimi**: CPU'dan gelen tüm bellek erişimlerinin arayüzünü oluşturur.
  - Önceki konsollar bu bileşeni başka bir yardımcı işlemci olarak içeriyordu, MMU artık aynı çipin içinde bulunuyor ve üretim maliyetlerini düşürüyor.

Ve elbette, bellek bant genişliğini hızlandırmak için bir miktar önbellek de dahil edilmiştir:

- **64 KB L1 cache**: Talimatlar için 32 KB ve veriler için 32 KB'a bölünmüştür.
- **256 KB L2 cache**: Bant genişliğini büyük ölçüde artıran talimatlar ve verilerle doldurulabilir.

### IBM'in geliştirmeleri

Önceki özelliklerin listesi (önceki nesillere göre) çok takdir edilse de, bu CPU hala oyun performansı açısından diğerlerinden geride kalır (unutmayalım ki bu hala bir genel amaçlı CPU'dur, elektronik tablolarda iyidir ancak *ortalama* fizikte). Bunu telafi etmek için IBM, Gekko'yu [@cpu-ibm] oluşturacak aşağıdaki ince ayarları ekledi:

- **50 yeni SIMD komutu** ile geliştirilmiş komut seti: Bunlar iki 32 bit kayan noktalı sayıyı veya bir 64 bit kayan noktalı sayıyı yalnızca bir döngü kullanarak işler. Sonuç olarak, yeni SIMD talimatları vektör hesaplamalarını hızlandıracak ve özellikle geometri dönüşümleri sırasında faydalı olacaktır.
  - Motorola'nın üst düzey G4 Mac'lerde bulunan SIMD uzantısı (AltiVec) ile karıştırılmamalıdır.
- **32 kayan nokta kaydı**: Bunlar yeni SIMD talimatlarıyla birlikte gelir.
- **Write Gather pipe**: Kullanılabilen özel bir bellek yazma mekanizması. Etkinleştirilirse, *tek vuruşlu* aktarımlar gerçekleştirmek yerine, tüm bellek yazma isteklerini %25 dolana kadar 128 baytlık bir arabellekte tutar, ardından istenen yazmaları 32 baytlık veri bloklarını bir kerede taşıyabilen *burst transaction* adlı bir teknik kullanarak gerçekleştirir.
  - Tahmin edebileceğiniz gibi bu, mevcut veri yollarının tam olarak kullanılmasını sağlayarak çok fazla bant genişliği tasarrufu sağlar.
- **Locked L1 cache**: Programlar 16 KB'lık L1 veri önbelleğini 'scratchpad' (inanılmaz hızlı bellek) olarak kullanmak üzere alabilirler.

Bu geliştirmeler, oyun mantığını (fizik, çarpışmalar, vb.) ele almanın yanı sıra, CPU'nun grafik işlem hattının bazı bölümlerini (geometri dönüşümleri, aydınlatma, vb.) kabul edilebilir bir performansla uygulamasına olanak tanıyacaktır. GPU yalnızca sınırlı sayıda işlemi hızlandırabildiği için bu çok önemlidir, bu nedenle nihai sonuç GPU'nun sınırlamalarına bağlı değildir.

### İleriye doğru bir adım mı yoksa geriye doğru bir adım mı?

> [Nintendo 64 makalenizde](nintendo-64), sistemin 64 bit CPU'ya sahip olduğunu, ancak GameCube'ün 32 bit olduğunu açıkladık. Nintendo konsollarının güçünü mü düşürdü?

Gerçekten de Gekko 32-bit PowerPC spesifikasyonunu uygularken MIPS R4300i 32-bit ve 64-bit modları arasında geçiş yapabilmektedir. Bunun bir gelişme olup olmadığını yanıtlamak için kendinize sormanız gerekir: Neden '64-bitliğe' ihtiyacınız olsun ki?

- 4 GB'den fazla belleği adreslemek için → GameCube'de bu miktarda bellek konumu yoktur, bu nedenle bu bir gereklilik değildir.
- Daha az döngü ve bant genişliği kullanarak daha büyük veri parçalarını çalıştırmak → Bu, Gekko'nun yeni SIMD talimatları, 64 bit FPU ve veri yolu ve yazma-toplama borusu tarafından kapsanmaktadır.
- Daha fazla reklam terimi bulmak için → Evet... Bunun artık insanları ikna ettiğini sanmıyorum.

Gördüğünüz gibi GameCube, '64-bit konsol' olarak adlandırılmadan 64-bit sistemlerin avantajlarından yararlanmaktadır. İşte bu yüzden siz ve ben iki karmaşık makineyi 'bit sayıları' ile özetleyemeyiz.

### Akıllı bellek sistemi

Yeni nesil mimarinin tasarımı sırasında, Nintendo'nun mimarları [önceki tasarımlarının](nintendo-64) ölüm sonrası analizini gerçekleştirdiler ve bazı yüksek gecikmeli bileşenlerle (RDRAM) birlikte Birleşik Bellek mimarisinin kullanılmasının darboğazların en büyük nedenlerinden birine yol açtığını keşfettiler (CPU döngülerinin neredeyse %50'si boşta çalışırken boşa harcanıyordu) [@cpu-gdc]. Ayrıca, birden fazla bağımsız birimin dahil edilmesi, bellek veri yolu için ilgili bir rekabete katkıda bulunmuştur.

Bu nedenle GameCube mühendisleri, **ayrılmış bellek alanı** sağlayan ve **düşük gecikmeli yongalar** kullanan yeni bir bellek sistemi geliştirdiler. Yeni tasarımla birlikte GPU ve CPU artık aynı RAM için rekabet etmeyecek (doluluk oranı sorunlarına neden olacak) çünkü GPU artık kendi dahili ve *şaşırtıcı derecede* hızlı belleğine sahip olacak. Diğer taraftan, GPU hala G/Ç'ye erişim konusunda hakemlik yapmaktan sorumlu olacaktır.

![Bu sistemin bellek düzeni.](cpu/memory.png)

Sonuç olarak iki ana otobüsle organize edilen bir sistem ortaya çıktı:

- **Kuzey Köprüsü**: 64 bit genişliğindedir ve CPU ile GPU'yu birbirine bağlar. CPU saatinden 3 kat daha yavaş çalışır, bu nedenle görevlerin GPU'ya çok fazla güvenmeyecek şekilde optimize edilmesi gerekecektir. DMA ve önbellek gibi diğer bileşenler kullanışlı olabilir.
- **Güney Köprüsü**: Aynı zamanda 64 bit genişliğindedir ve GPU'yu 'Splash' adı verilen **24 MB 1T-SRAM** ile bağlar. Bu RAM türü DRAM'den yapılmıştır (en popüler türdür ancak aynı zamanda daha ucuz ve yavaştır) ancak **SRAM** gibi davranması için ekstra devre ile geliştirilmiştir (daha hızlı ve pahalıdır, çoğunlukla önbellek için kullanılır). Veri yolu, muhtemelen GPU'nun CPU ve ana bellek arasında sabit bir bant genişliği sağlamasına olanak tanımak için **GPU'dan iki kat daha hızlıdır**.

Ayrıca, bu tasarım daha fazla belleğin bulunabileceği ek (ancak alışılmadık) bir veri yolu içerir:

- **Doğu Köprüsü**: GPU'yu **Audio RAM** veya 'ARAM' [@cpu-hitmen] adı verilen başka bir bellek yongasına bağlar. Genel amaçlı kullanım için **12 MB SRAM** sağlar, bu da *yedek* bellek için oldukça büyüktür. Ancak, veri yoluna normal yollardan (bellek adresleri) erişilemez. Bunun yerine, yalnızca DMA aracılığıyla erişilebilen 8 bitlik bir seri uç noktaya (GPU'dan iki kat ve CPU'dan dört kat daha yavaştır) bağlanır.

Genel olarak bu, ARAM'ın önemli miktarda RAM sağlarken, ses tamponu olarak hareket etmek veya belirli aksesuarlar tarafından kullanılmak gibi daha az kritik görevlerle sınırlı olacağı anlamına gelir (G/Ç bölümünde açıklanmıştır).

### Belleğin düzenlenmesi ve ARAM'ın sıralanması

Şimdiye kadar, kağıt üzerinde bellek özelliklerinin selefinden şüphesiz daha üstün olduğunu gördük, ancak hala iyileştirme için yer var. Örneğin Nintendo, ARAM'ı CPU'nun bellek haritasına dahil etmek için daha fazla donanım takabilirdi.

İlgili bir not olarak, Gekko'da kullanılan MMU'yu tekrar gözden geçirelim. CPU, 32 bit adres veri yolu ile 4 GB'a kadar belleğe erişebilir, ancak sistem bu miktarın yakınında bile değildir. Bu nedenle, boş (ve tahmin edilemez) bellek adreslerini açığa çıkarmamak için, 'sanal bellek' adresleme varsayılan olarak etkinleştirilir ve fiziksel adresleri daha güvenli, kolayca önbelleğe alınabilir ve sürekli bir 'sanal' adres haritası ile maskelenir [@cpu-dolphin_final].

Bu işi yapmak için, Gekko (ve diğer PowerPC mimarileri) sanal adresleri fiziksel adreslere aşağıdaki işlemle çevirir:

1. **Block Address Translation** (BAT) gerçekleştirin: Her bir çiftin bir sanal adres aralığını sürekli bir fiziksel adres aralığına eşlediği sekiz çift programlanabilir kayıt vardır (dördü veri ve dördü talimatlar için). MMU, bu aralıklar içinde bulunursa fiziksel adresi bulmaya çalışır.
2. BAT işe yaramadıysa, **Page Table**'ı okuyun: MMU ayrıca sayfaların fiziksel konumunu (sanal adres blokları) kataloglayan bir tablo da saklar.
    - MMU'nun bir sayfa tablosunu okuması zaman alabilir, bu nedenle son okumaları önbelleğe almak için bir **Translation look-aside buffer** (TLB) eklenmiştir.
    - X86 veya MIPS gibi diğer mimariler de sayfalama sağlar, ancak hepsi bir TLB sunmayacaktır.
3. Son olarak, istenen sanal adres hala çevrilemiyorsa, MMU CPU'da bir 'sayfa hatası' istisnası tetikler ve işletim sisteminin bundan sonra ne yapacağına karar vermesini sağlar.

Peki bunun geliştiriciler için ne faydası var? Nintendo'nun **Aram** kullanarak ana RAM'i sayfalama yardımıyla genişleten bazı kütüphaneler yayınladığı ortaya çıktı. Özetlemek gerekirse, ARAM adreslenebilir değildir, ancak CPU buradan veri almak ve depolamak için DMA'yı çağırabilir. Böylece CPU, diğer kaynaklara yer açmak için sayfaları ana RAM'den taşıyabilir ve bunları geçici olarak ARAM'de depolayabilir. Daha sonra, bir sayfa hatası oluştuğunda, işletim sistemi ARAM'deki eksik sayfaları aramak ve bunları ana RAM'deki orijinal konumlarına geri yüklemek için rutinler içerir.

Sonuç olarak, bazı akıllıca hilelerle, bu genel amaçlı yetenekler GameCube oyunlarının teknik olarak izin verilenden daha fazla bellekten yararlanmasını ve böylece daha yüksek kalite seviyelerine ulaşmasını sağladı. Yine de, bu tür hilelerin bazı performans cezalarıyla birlikte gelebileceğini akılda tutmak önemlidir (özellikle de hafife alınırlarsa).

## Grafikler

Bu, bu konsolun en kritik bölümlerinden biridir, temelde GameCube'ü bir *GameCube* yapar.

Bu konsolun GPU'sunun geçmişi bazı ilginç bağlantılara sahiptir: N64'ün SoC'sinin ([the RCP](nintendo-64#graphics)) yöneticisi Wei Yen, daha sonra ArtX'i kurdu ve yeni nesil çiplerini geliştirmek için Nintendo ile bir sözleşme imzaladı: **Flipper**.

![Super Mario Sunshine (2002).](sunshine.png) {.open-float}

Bir önceki iterasyona göre pek çok ilerleme kaydedildi, örneğin alt sistem ciddi şekilde basitleştirilerek tek (ama güçlü) bir çekirdeğe indirgendi.

Geliştirme süreci sırasında ArtX, ATI tarafından satın alındı ve bu şirket de altı yıl sonra AMD'ye satıldı. Bu nedenle, kasanın ön tarafında damgalanmış bir ATI etiketi görürsünüz.

{.close-float}

### Mimari ve Tasarım

Flipper birden fazla hizmeti işleyen karmaşık bir bloktur [@graphics-cheng], bu yüzden şimdilik grafik bileşenine odaklanalım (geometrimizi hayata geçirmekten sorumlu olduğu için). Bu alanı **GPU** veya **Grafik Motoru** olarak adlandıracağız ve eğer [N64 makalesini](nintendo-64#graphics) okuduysanız, sadece bileşenin kutudan çıktıktan sonra çalışır hale geldiğini bilmenizi isteriz, bu yüzden programcıların çalışmasını sağlamak için kod enjekte etme endişesi olmayacak. Bununla birlikte, özelleştirilebilir bazı ilginç parçalar olacaktır.

![Flipper'ın GPU'sunun boru hattı tasarımı.](flipper_pipeline.png)

Her zaman olduğu gibi, ekrana bir kare çizmek için verilerimiz GPU'nun boru hattından pompalanacaktır. Veri, dört aşamada gruplandırabileceğimiz birçok farklı bileşenden geçer:

#### Veritabanı {.tabs.active}

![Veritabanı aşama diyagramları.](flipper_pipeline/database.jpg) {.tab-float}

CPU ve GPU, ana RAM'de sabit uzunlukta bir **FIFO tamponu** kullanarak birbirleriyle iletişim kurar; bu, CPU'nun GPU'nun okuyacağı (ve sonunda görüntüleyeceği) çizim komutlarını yazacağı ayrılmış bir bölümdür, bu işlevsellik CPU ve GPU tarafından yerel olarak desteklenir.

Ayrıca, CPU ve GPU'nun aynı anda aynı FIFO'ya işaret etmesi gerekmez, bu nedenle GPU ilkini okurken CPU ayrı bir tane doldurabilir [@cpu-gdc]. Bu, boşta çalışmayı önler.

Geometrimizi oluşturmak için tek tek komutlar vermek karmaşık sahnelerde çok sıkıcı olabilir, bu nedenle resmi kütüphaneler, oyunun varlıklarından gerekli Görüntüleme Listelerini (önceden derlenmiş set FIFO komutları) oluşturan araçlar içeriyordu, bu yığının yalnızca GPU'nun bunları etkili bir şekilde görüntülemesine izin vermek için RAM'e kopyalanması gerekiyor.

GPU, FIFO'dan komutları almakla görevli bir **komut işlemcisi** içerir.

#### Geometri {.tab}

![Dolaylı mod kullanılarak Vertex aşama diyagramı.](flipper_pipeline/vertex.jpg) {.tab-float}

Burada ilkeller istenen manzaraya göre şekle dönüştürülür ve pikselleştirme (rasterisation) için hazırlanır. Motor bunu gerçekleştirmek için özel bir **Vertex ünitesi** veya 'VU' kullanır.

FIFO aracılığıyla verilen ilkelleri işlemek için iki **vertex modu** mevcuttur:

- **Doğrudan mod**: CPU her FIFO girişini gerekli tüm niteliklerle (konum, normal, renk, doku koordinatı veya matris indeksi) yayınlar. Veriler zaten önbelleğe alınmışsa kullanışlıdır.
- **Dolaylı mod**: FIFO girişi, öznitelik bilgisinin RAM'de nerede bulunduğunu belirten bir dizin değeri içerir, bu nedenle tepe biriminin bunu kendi başına araması gerekir. Bu veriler bir dizi olarak yapılandırılmıştır, bu nedenle VU'nun bu dizide gezinmesi için her tepe noktası girişinin dizinin nerede başladığını (**base pointer**), her girişin ne kadar uzun olduğunu (**stride**) ve tepe noktasının hangi konumda olduğunu (**index**) belirtmesi gerekir.

İlkel öğeler yüklendikten sonra **dönüştürülebilir**, **kırpılabilir**, **ışıklandırılabilir** (her bir tepe noktası Gouraud Gölgelendirme amaçları için enterpole edilebilen bir RGB değerine sahip olacaktır) ve son olarak **projelendirilebilir**.

#### Doku {.tab}

![Varsayılan kurulum kullanılarak doku aşaması diyagramı.](flipper_pipeline/texture.jpg) {.tab-float}

Şimdi sıra modellerimize doku ve efektler uygulamaya geldi ve bunun için GPU, piksellerimizi işleyecek birden fazla birim içeriyor. Şimdi, bu çok sofistike (ancak oldukça karmaşık) bir prosedürdür, bu nedenle takip etmekte zorlanırsanız, bunu pikselleri işleyen büyük bir montaj hattı olarak düşünün. Bununla birlikte, üç grup ünite mevcuttur:

- **Dört paralel Piksel birimi** ('piksel boru hatları' olarak da adlandırılır): İlkellerimizi rasterleştirir (piksellere dönüştürür). Dört ünitenin mevcut olması, her döngüde 2x2 piksele kadar dağıtım yapılmasını sağlar.
- **Her Piksel biriminin sonunda bir Doku eşleme birimi** (toplamda **dört tane**): Bunlar birlikte her döngüde ilkellerimiz (artık yalnızca pikseller) için sekiz adede kadar doku işler.
  - Ayrıca, kendini aynı önceliğin üzerine birden fazla doku katmanını birleştirmek için döndürebilir, bu özellik **Çoklu-Doku** olarak adlandırılır ve **detaylı dokular**, **environment mapping** (yansımalar) ve **düğüm haritalama** [@graphics-staff] gibi durumlarda kullanılabilir.
  - Son olarak, birim ayrıca bir **erken [z-tamponu](nintendo-64#modern-görünür-yüzey-belirleme)**, **mipmapping** (bunun yerine küçültülmüş bir doku işleme) sağlar, detay seviyesine göre) ve **anizotropik filtreleme** (eğimli dokularla daha fazla detay sağlayan [önceki filtrelere](nintendo-64#tab-1-2-reality-display-processor) göre memnuniyet verici bir gelişme).
- **Texture Environment unit** veya 'TEV': Çok güçlü bir programlanabilir 16 aşamalı renk karıştırıcı. Temel olarak çoklu [texelleri](playstation#tab-4-5-textures) (aydınlatma, dokular ve sabitler) birleştirerek çokgenlerimiz üzerinde uygulanacak muazzam miktarda doku efekti elde eder.
  - Ünite dört texel alarak çalışır ve bunlar daha sonra talep edilen işleme göre işlenir. Daha sonra, elde edilen texelleri yeni girdi olarak besleyebilir, böylece bir sonraki aşamada/döngüde ünite önceki sonuç üzerinde farklı bir işlem gerçekleştirebilir. Bu 'döngü' 15 iterasyona kadar sürebilir.
  - Her aşamada seçilebilecek 2^4 işlem vardır [@graphics-dolphin_uber] ve sonucun bir sonraki aşamada yeniden işlenebileceği düşünüldüğünde, ~5.64 × 10^511 olası permütasyon vardır!
  - Programcılar TEV'i çalışma zamanında kurarlar (yani her an değişebilir) ve bu çok önemlidir çünkü birçok orijinal malzeme ve efekte kapı açar.

Tüm bunlar, önbellek ve Scratchpad belleğine (hızlı RAM) bölünebilen 1 MB Doku belleği (1T-SRAM tipi) ile desteklenir. **SRTC (S3 Texture Compression) dokuları için gerçek zamanlı donanım dekompresyonu** da mevcut tek megabyte  belleğe daha fazla doku sığdırmak için kullanılabilir.

#### Render {.tab}

![Render aşaması diyagramı.](flipper_pipeline/render.png) {.tab-float}

Render işleminin son aşaması, sahnemize isteğe bağlı ancak yararlı bazı dokunuşlar uygulamayı içerir:

- **Sis**: Sisli bir ortamı etkili bir şekilde simüle etmek için TEV'deki son rengi sis sabit rengiyle karıştırır.
- **Z-karşılaştırma** bir geç aşama [Z-tamponlama](nintendo-64#modern-visible-surface-determination). Motor, Z tamponlama hesaplamaları için 2 MB gömülü 1T-SRAM kullanacaktır.
- **Blending**: Geçerli karenin renklerini önceki kare arabelleği ile birleştirir.
- **Dithering**: Çerçevenin üzerinde titreme uygular.

Ortaya çıkan çerçeve son olarak gömülü 1T-SRAM'deki çerçeve arabelleğine yazılır, ancak bu hala Flipper'ın içinde kilitlidir (alan 'Gömülü Çerçeve Arabelleği' veya 'EFB' olarak adlandırılır, ancak z-buffer'ı da içerir). Bu nedenle, TV'mizde görüntülemek için, **External Frame-Buffer** veya 'XFB'ye [@graphics-xfb] kopyalamamız gerekir; bu da **Video Interface** veya 'VI' ile alınabilir. Ayrıca kopyalama işlemi **Antialiasing** (bloklu kenarları azaltır), **Deflicker** (parlaklıktaki ani değişiklikleri yumuşatır), **RGB'den YUV'ye dönüştürme** (bellekte daha az yer kaplayan benzer bir format) ve **Y-scaling** (kareyi dikey olarak ölçeklendirir) gibi efektler uygulayabilir.

XFB alanının CPU tarafından da manipüle edilebildiğini belirtmek gerekir; bu, daha önce işlenmiş bitmap'leri son işlenmiş karemizle birleştirmesini sağlar; veya bazı oyunların EFB'ye sığamayan çok renkli zengin kareler işlemesi gerektiğinde, bunlar parçalar halinde işlenir ve daha sonra CPU tarafından birleştirilir (her zaman VI ile senkronize kalır).

### İnteraktif karşılaştırma {.tabs-close}

Tüm bunları bir perspektife oturtmanın zamanı geldi, programcıların bu konsolun yeni grafik özelliklerinden yararlanmak için önceki oyunlarının tasarımlarını nasıl geliştirdiklerine bir göz atın. Örneklerin interaktif olduğunu unutmayın!

#### Yükseltme {.tabs.active}

[Önceki nesilde](nintendo-64) poligon sayısı nedeniyle küçültülmek zorunda kalan ünlü Mario modeli bu nesil için tamamen yeniden tasarlandı, modelin düz yüzlerden buruşuk kollara nasıl evrildiğine yakından bakın.

![Super Smash Bros (1999) N64.<br>320 üçgen.](mario_smash_64){.toleft .nested model3d="true"}

![GC için Super Smash Bros. Melee (2001).<br>4,718 üçgen.](mario_melee_gc){.toright model3d="true"}

Sadece iki yıl içinde ne kadar çok ayrıntı kazanıldığı gerçekten etkileyici, değil mi?

#### Güncelleme {.tab}

Bu durumda, Sonic Team zaten [kendi benzersiz konsolları](dreamcast) için bir Sonic modeli tasarlamıştı, ancak oyunlarını GameCube'e taşıdıktan sonra modellerine daha fazla poligon ekleyebildiklerini ve daha iyi ayrıntılar elde ettiklerini gördüler.

![DC için Sonic Adventure (1999).<br>1,001 üçgen.](sonic_adventure_dc){.toleft .nested model3d="true"}

![DC için Sonic DX (2003).<br>1,993 üçgen.](sonic_dx_gc){.toright model3d="true"}

### Yaratıcılık {.tabs-close}

Bu boru hattının iç işleyişinden de görebileceğiniz gibi, grafik teknolojisi, programcıların artık işleme sürecinin belirli işlevlerini kontrol edebilecekleri noktaya kadar gelişmektedir.

![The Legend of Zelda: The Wind Waker (2003).](wind_waker.png) {.open-float}

Aynı dönemde, PC grafik kartları sabit işlevli boru hatlarını **shader çekirdekleri** (piksellerin nasıl çalıştırılacağını tanımlayan küçük programları çalıştıran birimler) lehine bir kenara bırakmaya başlıyordu. Flipper hala sabit işlevli bir GPU içeriyor, ancak TEV birimi gibi bileşenleri dahil ederek Nintendo'nun kendi gölgelendirici benzeri çözümünü sağladığı iddia edilebilir.

Sanırım bu yeni yeteneği kullanan oyunların en iyi örneklerinden biri, dokularının *karikatürümsü* görünmesini sağlamak için **Cel shading** olarak bilinen benzersiz bir renk/ışıklandırma tekniği uygulayan **The Legend of Zelda: Wind Waker**'dır.

{.close-float}

### Video çıkış sistemi

Video sinyali, 16,7 milyon renge (24 bit derinlik) kadar 640x480 piksele kadar (veya PAL'de 768×576 piksel) çözünürlük sunar. Ayrıca, sistem sinyalini **ilerleme modunda** (daha net bir görüntüye sahip, ancak o dönemde her TV bunu desteklemiyor olabilir) yayınlayabiliyordu.

XFB birden fazla boyuta sahip olabilir, bu nedenle uyumluluk nedenleriyle Video arayüzü, XFB'yi bölgeye göre TV ekranına sığacak şekilde yeniden örnekleyerek kareyi görüntülemek için elinden geleni yapacaktır.

### Bağlantılar

Konsolda bir değil, iki video çıkış konektörü bulunuyordu:

![A/V Bağlantıları arkada.](av_photo.jpg) {.open-float}

- Bir tanesi **Analog A/V** olarak adlandırılmış ve aslında eski güzel [Multi Out](super-nintendo.md#a-convenient-video-out). Bu en popüler olanıdır.
  - Bu konsolun PAL sürümü S-Video taşımaz ve NTSC sürümü RGB sağlamaz (bummer!).
- **Dijital A/V** adı verilen bir diğeri ise ses ve görüntüyü dijital olarak gönderir (günümüzde HDMI'a benzer şekilde ancak tamamen farklı bir protokol kullanarak!).
  - Nintendo bu sokete bağlanan bir komponent kablo seti çıkardı. Aynı fiş, dijital sinyali YPbPr'ye (optimum kalite) dönüştürmek için bir video DAC ve kodlayıcı içeriyordu.
  - Kablo ekstra bir aksesuar olarak satılıyordu ve şimdi bir tür kalıntı olarak kabul ediliyor!

{.close-float}

## Ses

Nintendo sonunda bu büyük görevi CPU-GPU'dan kurtarmak ve daha zengin sesler sağlamak için bazı özel ses devreleri sundu. Bu konsolda sundukları şey ise, Flipper içinde çalışan **Macronix** tarafından üretilen bağımsız bir **Dijital Sinyal İşlemcisi** veya 'DSP'.

DSP'nin görevi, ham ses verilerimiz üzerinde farklı işlemler gerçekleştirmek (örneğin, ses seviyesi değişiklikleri, örnekleme hızı dönüşümü, 3D ses efektleri, filtreleme, yankı, yankı, vb) ve ardından 2 kanallı bir PCM sinyali çıkarmaktır. Ancak tek başına çalışmaz, DSP diğer bileşenlerin yardımıyla ses sağlar.

İlk yoldaşı **Audio Interface** (AI), TV'de sonlanan ses sinyali aracılığıyla son örneği göndermekten sorumlu 16 bit stereo dijital-analog dönüştürücüdür. Yapay zeka, her 0.25ms'te sadece 32 bayt ses verisi işleyebilir, bu nedenle her ses örneğinin 2 bayt ağırlığı olduğunu ve stereo ses oluşturmak için iki taneye ihtiyaç duyulduğunu dikkate alırsak, Yapay zeka en fazla 32 kHz örnekleme hızına sahip 8 stereo örneğini karıştırabilir, *ses*!

Son olarak, ham ses verilerini depolamak için kullanılabilecek büyük (16 MB) ancak çok yavaş bir yedek bellek olan **Audio RAM** (ARAM) bloğumuz var. Oldukça fazla alan var, bu nedenle GPU bunu ek materyalleri (dokular gibi) depolamak için de kullanabilir. CPU'nun bu belleğe doğrudan erişimi yoktur, bu nedenle içeriği taşımak için DMA'ya başvuracaktır.

Daha iyi veya daha kötü, DSP mikrokod kullanılarak programlanabilir ([_endişe verici_](nintendo-64#audio)), ancak endişelenmeyin, çünkü resmi SDK zaten her oyun tarafından kullanılan genel amaçlı bir mikrokod içerir, ancak konsol açılış dizisi ve bazı Nintendo oyunlarında (_nasıl rahat_, çünkü Nintendo DSP'yi belgelememişti, bu yüzden sadece onu nasıl programlayabileceklerini biliyorlar).

Bununla birlikte, ses üretme süreci aşağıdaki gibi işler [@audio-bourdon]:

1. CPU, ham örnekleri ARAM'a taşımak için DMA'ya komut verir.
2. CPU, DSP'nin bu örnekleri nasıl çalıştırması gerektiğini bildiren bir komut listesi gönderir. Başka bir deyişle, mikrokod programını yükler (geliştiriciler için resmi olarak yalnızca bir tane mevcuttur).
3. DSP, ARAM'dan örnekleri alır, gerekli işlemleri uygular ve bunları iki kanala karıştırır. Son olarak, elde edilen verileri RAM üzerinde depolar.
4. AI, işlenmiş örnekleri RAM'den alır ve bunları ses sinyali aracılığıyla çıkarır.

### Sıkıştırma ve özgürlük

Her ne kadar *örnekleme çağına* çoktan ulaşmış olsak ve artık belirli dalga biçimlerine kilitlenmemiş olsak da, yeni ses sistemi yine de büyük bir gelişme. Yeni başlayanlar için, zorunlu [müzik sıralama](nintendo-64#audio) destanı sonsuza kadar gitti. Sistem artık, [Saturn](sega-saturn#audio) ve [PS1](playstation#audio)'in yıllar önce başardığı gibi, önceden üretilmiş müziği ses uç noktasına sorunsuz bir şekilde aktarabiliyor.

Size biri Nintendo 64 için diğeri de GameCube için piyasaya sürülen iki oyunu kullanarak bir örnek göstereyim. Her ikisinin de müzik notaları farklıdır ancak bağlam (düşman savaşı) aynıdır. Her iki sistemin tasarımını (paylaşımlı vs özel) göz önünde bulundurarak her iki parçanın ses kalitesinde nasıl farklılık gösterdiğine bir göz atın.

![N64 için Paper Mario (2000).<br> RSP tarafından anında sekanslandı.](PaperMario64){.toleft video="true"}

![Paper Mario: The Thousand-Year Door (2004) GC için.<br>DSP'ye aktarıldı.](PaperMario){.toright video="true"}

*Duyabileceğiniz gibi*, DSP nihayet müzik bestecilerine her zaman istedikleri esnekliği ve zenginliği verdi.

#### Ek malzeme

Daha doğrudan bir yan yana karşılaştırma için, bestecilerin düzenlemelerini GameCube ve selefi için nasıl uyarladıklarını gösteren bu interaktif widget'ı hazırladım. Burada, bir Nintendo 64 oyunu ve bir GameCube oyunu için aynı neşeli müzik kullanılıyor ve ortaya çıkan karşılaştırma GameCube'ün DSP'sinin teknik avantajlarını (bir kez daha) göstermemi sağlıyor.

![**Nintendo 64:** Kirby 64: The Crystal Shards (2000).<br>**GameCube:** Kirby Air Ride (2003).](){audio_switcher="true" src1="kirbycrystal_n64" src2="kirbyair_gc" label1="Nintendo 64" label2="GameCube" .centered-container}

Şimdi, her bir parçanın arkasında neler olduğunu görselleştirmek için, işte iki ilgili spektrogram. Başlamadan önce, bu tür çizelgelere aşina değilseniz, önceki NES makalemi, özellikle de [ses bölümünü](nes#audio) (bunları tanıttığım yer) okumanızı tavsiye ederim.

![Kirby 64: The Crystal Shards (2000) filmindeki PCM kanalının spektrogramı.](spectrograms/kirbycrystal_n64.png) {.toleft}

![Kirby Air Ride'daki (2003) PCM kanalının spektrogramı](spectrograms/kirbyair_gc.png) {.toright}

Dürüst olmak gerekirse, karışık parçaların spektrogramda ayrıştırılması zordur, ancak bundan bazı kalıplar çıkarmaya çalışabileceğime inanıyorum.

Öncelikle, GameCube parçasında neredeyse tüm frekans spektrumu eşit olarak kullanılıyor, bu da eşlik için kullanılan ek enstrümanlara (harmonikler ekleyen ve bu nedenle grafikte daha fazla alanı dolduran) bağlanabilir.

Son olarak, GameCube spektrogramındaki genlikler daha düzgün dağılmış görünüyor. Başka bir deyişle, her enstrümanın ses seviyesi farklı şekilde dengelenir ve reverb gibi efektler içerir. Sanırım bestecinin bu müziği üretirken asıl amaçladığı şey ikincisiydi ve bu tür kontroller GameCube'ün ses akışını desteklemesi sayesinde mümkün. Böylece besteciler, çalışma zamanında sıralama ve miks için konsola (ve sınırlamalarına) sıkı sıkıya bağlı olmak yerine, müziklerini sıralamak ve mikslemek için istedikleri herhangi bir aracı kullanabilirler.

Nintendo 64'ün aynı sonucu üretmekten tamamen aciz olduğunu söyleyemem. Ancak kesin olan bir şey var ki, Nintendo 64 dünyasında her bir ses işlevi ekstra döngülere ve/veya belleğe mal oluyor ve bu da oyunun diğer alanlarını etkileyebiliyor. Bu nedenle kaynakların rasyonel kullanılması gerekmektedir. Öte yandan, GameCube'ün büyük örnekleme desteği sayesinde, üretilen tüm partisyonu bir arada izlemek de mümkündür.

## Giriş/Çıkış

Görünüşe göre bu nesil genişletilebilirlik ve aksesuarlar üzerinde çok çalışıyor, GameCube birkaç yeni ilginç bağlantı noktası içeriyordu, ancak bazıları kullanılmadı.

### Dahili G/Ç

![GameCube'ün mimarisinin ana şeması. Burada, I/O'nun çoğunu kontrol eden 'Kuzey Köprüsü'nü buluyoruz.](diagram.png)

Flipper, CPU'yu diğer bileşenlerle arayüzlemekten sorumludur, bu nedenle ses ve grafik devrelerini içermenin yanı sıra, [@cpu-tree] 'den oluşan **Northbridge** adlı bir donanım koleksiyonu da sağlar:

- **Audio Interface** veya 'AI': Ses Kodlayıcıyı bağlar.
- **Video Interface** veya 'VI': Video Kodlayıcıyı bağlar.
- **Processor Interface** 'PI': CPU'yu (Gekko) bağlar.
- **Disk Interface** veya 'DI: Kendi başına bağımsız bir bilgisayar olarak düşünülebilecek DVD denetleyicisini bağlar.
- **Serial Interface** veya 'SI': Tescilli bir seri protokol kullanarak dört olası kontrol cihazını bağlar.
- **External Interface** veya 'EXI': Harici aksesuarları (daha fazlası aşağıda açıklanmıştır), Hafıza Kartını ve IPL BIOS'u Gerçek Zamanlı Saat veya 'RTC' ile birlikte bağlar. Bu cihazları yalnızca bir seri düğüm kullanarak bağlamak için SPI benzeri bir protokol kullanır.

Her arayüz, bazı davranışlarını değiştirmek için bir dizi kaydedici sunar.

### Opsiyonel Giriş/Çıkış

GameCube'ün kasasının altında, bazı widget'ları bağlamak için iki harici soket bulacaksınız.

![Kasanın alt kısmında kapalı aksesuar yuvaları.](accessories_covered.jpg){.tabs-nested .active .open-float .tab-float title="Kapaklı"}

![Kasanın alt kısmında açık aksesuar yuvaları.](accessories.jpg){.tabs-nested-last title="Kapaksız"}

Her ikisi de teknik olarak aynıdır (32 MHz'de çalışan seri veri yolu), ancak farklı aksesuarları barındırmak için farklı bir dış şekil ile sunulurlar:

- **Serial Port 1**: Nintendo bu yuva için Modem ve Geniş Bant adaptörleri gönderdi.
- **Serial Port 2**: Bu bağlantı noktasını kullanan hiçbir genel aksesuar gönderilmemiştir, ancak hata ayıklama araçları sağlayan bazı üçüncü taraf aksesuarlar bulabilirsiniz.

Bu portlar EXI yığını üzerinde çalıştırılır.

{.close-float}

### Olağandışı G/Ç

Serial portların yanında bulunan başka bir soketten hala bahsetmediğimi fark edeceksiniz: **Parallel Port**. Bu bağlantı noktası çok daha hızlıdır (80 MHz'de 8 bit, 32 MHz'de 1 bit), bu da Nintendo'nun **Hi-Speed Port** adını vermesinin nedeni olabilir. Ancak en alışılmadık kısım, bu bağlantı noktasının EXI aracılığıyla değil, ARAM aracılığıyla arayüzlenmiş olmasıdır!

Bugüne kadar bilinen tek resmi aksesuar, GameCube'ün altına ekstra bir kat olarak takılan ünlü **Game Boy Player** olup, [**Game Boy**](game-boy) ve [**Game Boy Advance**](game-boy-advance) oyunlarını yerel olarak oynamak için gerekli donanımı içeriyordu. Oynatıcı tüm ağır işleri kendisi yaparak ve ardından sonuçları (kareler ve ses verileri) GameCube'ün görüntü/ses için ilgili bileşenlere ileteceği ARAM'a göndererek çalışır.

### Tutarlı tasarım

Ne kadar çok aksesuar bağlarsanız bağlayın, konsolun her zaman kübik şeklini koruyacağını (ya da en azından korumaya çalışacağını) belirtmeye değer buldum.

## İşletim Sistemi

Konsolu açtıktan sonra CPU, BIOS/IPL çipinde bulunan **Dolphin OS** adlı bir işletim sistemini yüklemeye başlayacaktır, bu donanımın başlatılmasıyla ilgilenecek ve oyunların kullanması için bazı uygun sistem çağrıları ve global değişkenler sağlayacak çok minimal bir işletim sistemidir. Resmi SDK kullanılarak geliştirilen oyunlar, düşük seviyeli işlemler sırasında bu çağrıları dolaylı olarak yürütecektir.

![Resmi logo, açılış animasyonu bittikten sonra gösterilir.](ipl/splash.png)

### Sıçrama ekranı ve kabuk

Önyükleme işlemi tamamlandıktan sonra, işletim sistemi *gayri resmi* olarak **Ana Menü** adında küçük bir program yükleyecektir.

![Birden fazla ayarın mevcut olduğu Ana Menü.](ipl/menu.png){.tabs-nested .active title="Menü"}

![Saat ayarları.](ipl/clock.png){.tab-nested title="Saat"}

![Ses ayarları.](ipl/sound.png){.tabs-nested-last title="Ayarlar"}

Bu program ünlü açılış animasyonunu (GameCube logosunu çizen küçük küp) görüntülemekten ve eğer varsa oyunu yüklemekten sorumludur. *Geçerli* bir oyun olmaması durumunda, aralarından seçim yapabileceğiniz çeşitli seçenekler içeren küp şeklinde basit bir menü sunacaktır:

- Tarihi ve Saati Değiştirme.
- Oyunu DVD okuyucudan yükleme.
- Herhangi bir Hafıza Kartındaki kayıtları yönetme.
- Ses ayarlarını ve ekran konumunu değiştirme.

## Oyunlar

Nintendo, konsolları için oyun geliştirilmesine yardımcı olmak (ve teşvik etmek) için geliştiricilere birçok araç sağladı [@games-sdk]:

- **Dolphin SDK**: Resmi API'ler ve faydalı kütüphaneler seti. Bu, Flipper'ın GPU'sunu programlamaktan sorumlu **GX** kütüphanesini içerir.
- **C** ve**C++** Derleyiciler.
- **Debuggers** ve **Testers**: Resmi bir Dev Kit ile kullanılmak üzere tasarlanmıştır.
- **Cygnus**: Şimdi 'Cygwin' olarak bilinen bu program, temel olarak UNIX ortamını Windows'ta kopyalamak için kullanılır.
- **CodeWarrior**: *Bir zamanların* fiili IDE'si.
- Çeşitli **yardım araçları**; MusyX, Doku düzenleyicileri, Ekran Listeleri dışa aktarıcıları, USB programlayıcıları vb.
- Tonlarca ve tonlarca **dokümantasyon**! (PDF ve HTML olarak mevcuttur)

### Özel donanım

Yazılımın yanı sıra şirket, konsolun halka açık olarak piyasaya sürülmesinden önce ve sonra farklı donanım kitleri (fiyatları değişiyor) tedarik etti.

Muhtemelen bahsetmeye değer en popüler olanı **Dolphin Development Hardware** veya 'DDH' GameCube'ün I/O'sunun bir kısmını ve birçok geliştirmeye yardımcı donanımı içeren PC benzeri bir kuleden oluşuyordu [@games-devstuff], oyun bir Windows PC'de geliştirilirken esas olarak bir hata ayıklama istasyonu olarak kullanılıyordu.

### Medya Ortamı

Oyunlar **miniDVD** adı verilen özel bir diskten yüklenir, bu disk geleneksel bir DVD diskin neredeyse yarısı boyutundadır ve 1,4 GB'a kadar veri tutabilir.

İlginç bir gerçek olarak, disk okuyucu **Sabit Açısal Hız** veya 'CAV' ile çalışır, yani veriler diskin dış alanında bulunursa daha hızlı (3.125MB/s) ve iç alanda bulunursa daha yavaş (2MB/s) okunacaktır. Bu, etkilerin tam tersi olduğu **Sabit Doğrusal Hız** sistemlerinden (geleneksel CD/DVD okuyucular tarafından kullanılan) farklıdır.

Oyun kayıtları **Memory Card** adı verilen özel bir harici aksesuarda saklanır ve bunlardan iki tanesi için yeterli yuva vardır.

### Sıradışı kontrolörler

Nintendo, **GameBoy Link Cable** olarak bilinen ve [**Game Boy Advance**](game-boy-advance)'ı GC kontrolör portuna takan bir aksesuar gönderdi, böylece oyunlar GBA'ya küçük programlar yükleyebilir ve onu özel bir kontrolör olarak ele alabilirdi. Bu ilginç özellik, bazı oyunlarda benzersiz etkileşimler ve içerikler sağladı.

### Çevrim içi Platform

[rekabetin](dreamcast) aksine, Nintendo kullanıcıların çevrim içi içeriğe erişmek için ekstra aksesuar satın almalarını zorunlu kılmakla kalmadı, aynı zamanda yayıncıların güvenebileceği herhangi bir internet hizmeti de sunmadı [@games-online] ve geliştiricileri gerekli internet altyapısını sağlamaktan tek başına sorumlu kıldı.

Sonuç olarak, çevrim içi oyun oynamak mümkün bir özellik olsa da, yaygın olarak benimsenmedi ve yalnızca çok az sayıda oyun bundan yararlandı.

## Korsanla Mücadele ve Homebrew

Nintendo bir süredir bu oyunun içindeydi, bu nedenle yetkisiz yazılımların veya farklı bir bölgeden oyunların çalıştırılmasını önlemek için güvenlik mekanizmaları eklemeleri yeni bir haber değil. Ayrıca, GameCube'ün sağladığı yeni I/O aralığı nedeniyle, saldırı yüzeyi önemli ölçüde genişledi.

### Güvenlik mekanizmaları

Bunları şu alanlarda organize edebiliriz:

#### DVD denetleyicisi {.tabs.active}

Bu, disk ortamını kullanan ilk Nintendo konsolu olsa da, oyunların korsan kopyalarını oynamaya çalışmak kolay olmayacaktı. MiniDVD, verilerinin şifrelenmesinin yanı sıra diskin iç tarafında özel barkodlar kullanılarak korunmaktadır. Doğrulama ve şifre çözme işlemi sorunsuz bir şekilde çalışır: Sistem yalnızca verileri talep etmekle sınırlı kalırken miniDVD denetleyicisi bununla ilgilenir.

DVD okuyucuyu oluşturan donanım, yalnızca bir dizi komut kullanılarak erişilebilen bir kale duvarı olarak düşünülebilir. miniDVD denetleyicisi, takılan diskin orijinal olup olmadığına karar vermekle ilgilenecek özel bir CPU'ya sahiptir ve eğer değilse, ana CPU tarafından verilen hiçbir komut onu başka türlü okumaya ikna etmeyecektir.

**Yenilgi**: Diğer tüm kedi-fare oyunlarında olduğu gibi, üçüncü taraf şirketlerin okuyucuyu kandırabilecek mod çipleri oluşturmak için kontrol cihazını tersine mühendislikle başarılı bir şekilde geliştirmeleri sadece bir zaman meselesiydi. Ancak hiçbir mod çipinin bu konsolu, kasayı fiziksel olarak değiştirmeden sihirli bir şekilde geleneksel bir CD/DVD'ye uyduramayacağını unutmayın!

#### IPL ve EXI {.tab}

Bir başka olası istismar yolu da Homebrew programlarını yüklemek için mevcut harici I/O'yu kullanmaktır. Yine de, önce DVD okuyucuyu kırmadan, mevcut diğer tek seçenek GameCube'ün yüklediği ilk programın kontrolünü ele geçirmeye çalışmaktır ve bu da... IPL.

Bu, BIOS'u tersine mühendislikle değiştirerek ve çipi değiştirilmiş bir çiple değiştirerek, diyelim ki bir dosya okuyucuyu çalıştırabileceğiniz ve buradan aksesuar bağlantı noktalarından alınan programları çalıştırabileceğiniz anlamına gelir (ek donanımın takılı olduğu varsayılırsa).

Her ne olursa olsun, IPL çipi XOR koşullular ve bir Şifre-metni [@anti_piracy-ipl] kullanılarak şifrelenmiştir, bu da tersine mühendisliği 'imkansız' hale getirmektedir.

**Yenilgi**: Bilgisayar korsanları sonunda IPL'nin şifresini çözen donanımın, kullanılan Şifreli metni ele geçirmelerini sağlayan bir hata içerdiğini keşfetti [@anti_piracy-steil]. Bu sayede başka bir ROM oluşturulabilir ve aynı şifreleme ile şifrelenebilir, böylece GameCube bunu kendi ROM'u gibi önyükleyebilir!

Bu da yetmezmiş gibi, bilgisayar korsanları miniDVD okuyucuyu geleneksel DVD'leri yüklemesi için kandıracak yeni yöntemler de buldular.

#### Mansiyon Ödülü {.tab}

Bu iki mekanizma keşfedilmeden önce, aslında herhangi bir değişiklik yapmadan rastgele kod yüklemenin çok daha basit bir yolu vardı. Bu yöntem **çevrim içi protokolün ele geçirilmesinden** oluşuyordu.

*Phantasy Star Online* gibi bazı oyunlar, şirketin sunucularından güncellenmiş bir yürütülebilir dosya (DOL dosyası) indirilerek güncellenebilen kendi çevrim içi işlevlerini uyguladı ve ikincisi protokolünde herhangi bir güvenlik uygulamadı. Gördüğünüz gibi bu, gerçekleşmeyi bekleyen bir ortadaki adam saldırısıydı...

Uzun lafın kısası, sahte bir sunucuyu taklit ederek GameCube, sağlayabileceğiniz her türlü DOL'u indirir (ve çalıştırır). Bu da bilgisayar korsanlarının yalnızca orijinal oyuna ve geniş bant adaptörüne ihtiyaç duyduğu anlamına geliyor. Bu teknik **PSOload** [@anti_piracy-psoload] olarak bilinir.

## Hepsi bu kadar {.tabs-close}

![Yakın zamanda tavan arasından kurtardığım eski GameCube'um. Wii için sadece kumandaya ihtiyacım vardı (o zamanlar hepsini ikinci el almak daha ucuzdu!).](my_gamecube.jpeg)

İşte bu, **onuncu makale**!

Bu makalenin uzunluğuna kabaca bir sınır koymaya çalıştım ama anlamalısınız ki, teknoloji *o kadar karmaşık* hale geldi ki, yanlışlıkla önemli bir şeyi atlarsam, tüm konuyu takip etmek imkansız hale geliyor.

Her neyse, Flipper'ın GPU'sunun karmaşık boru hattını anlamama yardımcı oldukları için #dolphin-dev IRC topluluğuna teşekkür etmek istiyorum, bu adamlar birkaç yıldır GameCube emülatörünü geliştiriyorlar ve ne kadar çok şeye katlanmak zorunda kaldıkları gerçekten etkileyici.

Ve son olarak, eğer bu yazıyı ilginç bulduysanız lütfen [katkıda bulunmayı](support) düşünün. Elimden geldiğince eksiksiz olması için çabalıyorum ve bu süreçte bana aniden ne kadar zamana mal olduğunu unutuyorum, yine de iyi bir yatırım olduğunu düşünüyorum.

Bir dahaki sefere kadar!  
Rodrigo.
