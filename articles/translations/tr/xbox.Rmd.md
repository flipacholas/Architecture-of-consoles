---
short_title: Xbox'ın Mimarisi
long_title: Xbox'ın Mimarisi
name: Xbox
subtitle: Korkulan bir rakip
date: 2020-06-26
release_date: 2001-11-15
cover: "xbox"
generation: 6
seo_image_pos: "Bottom"
top_tabs:
  Model:
    file: international
    caption: "Orijinal Xbox.<br>15/11/2001 tarihinde Amerika'da, 22/02/2002 tarihinde Japonya'da ve 14/03/2002 tarihinde Avrupa'da piyasaya sürüldü."
  Motherboard:
    caption: "İlk revizyonu gösteriliyor.<br>Kontrolcüler ayrı bir yardımcı kart kullanılarak takılır.<br>Eksik SDRAM yongaları arka tarafta ve dört kullanılmayan SDRAM yuvası bulunmaktadır."
  Diagram:
    caption: "Her kontrolcü ayrı bir USB hub'ına bağlıdır."
#Historical
aliases:
  - /projects/consoles/xbox/
---

## Hızlı bir giriş

Görünüşe göre Microsoft, Sega'nın bıraktığı yerden devam etmeye karar verdi. Teklifleri mi? Geliştiriciler tarafından takdir edilen tanıdık özelliklere sahip bir sistem ve kullanıcılar tarafından hoş karşılanan çevrim içi hizmetler.

Lütfen bu makalenin diğer kaynaklarla tutarlılık sağlamak amacıyla depolama birimlerini metrik önek (örneğin megabayt veya 'MB') ve standartlaştırılmış ikili önek (örneğin mebibayt veya 'MiB') arasında ayırdığını unutmayın, bu nedenle:

- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

... ve benzeri.

### Okuma İpuçları

Bu yazıyı yazdıktan birkaç ay sonra fark ettim ki bu, yaptığım en yoğun yazılardan biri. Xbox'ın içinde gerçekten çok şey oluyor ve ben çoğunu anlatmaya çalıştım.

Şimdi, eğer gerçekten bu sistemi anlamakla ilgileniyorsanız ve bu makaleyi takip etmekte zorlanıyorsanız, size verdiğim tavsiye şu: **Rahat olun**, makale başka bir yere gitmiyor. Kendinize ilginç gelen konulara odaklanın, kendi hızınızda okuyun, destek için 'Kaynaklar' bölümündeki bağlantılara göz atın ve sonunda kendinize baskı yapmayın, burada bir sınav yok!

## {.supporting-imagery}

## İşlemci (CPU)

Bu konsolda bulunan işlemci, ünlü **Intel Pentium III**'ün (bilgisayarlar için hazır bir CPU) hafif özelleştirilmiş bir versiyonudur ve **733 MHz** hızında çalışır. Bu sayede bu konsolun sahne arkasında sadece bir PC olduğunu varsayabiliriz... Size cevabı söylemeyeceğim, ancak makalenin sonunda kendi sonucunuza ulaşabileceğinizi vaat ediyorum.

![Xbox'ın anakartındaki Intel Pentium III paketi.](cpu.jpg)

Neyse, Pentium'lar, Intel tarafından tasarlanan ve üretilen diğer CPU hatlarıyla birlikte bilgisayar pazarında son derece popülerdi. Intel'in pazar payı o kadar büyüktü ki, kalite açısından bir referans noktası haline geldiler: Tipik bir kullanıcı olarak, iyi bir bilgisayar istiyorsanız ve bütçeniz varsa, sadece bir Intel CPU taşıyan bir şey *aramanız* yeterliydi. Şimdiye kadar daha fazla faktör olduğunu biliyoruz, ancak bu, Intel'in pazarlama ekibinin başardığı şeydi.

### Teknik bilgi

Intel'i haritada konumlandırdıktan sonra, şimdi bu konsolun konusuna geri dönelim. Araştırmalarım sırasında, diğer CPU'lar (MIPS, SuperH, ARM vb.) gibi derinlikte belgelere rastlamayı bekliyordum, ancak bunun yerine arama yönümü saptıran aşırı miktarda pazarlama terimi buldum. Bu nedenle, bu makale için bu CPU'nun nasıl çalıştığını anlamak için gerekli tüm bilgileri düzenleyecek bir yapı oluşturdum. Ayrıca Intel'in bu CPU'yu markalandırmak için kullandığı bazı terimleri de tanıtmaya çalışacağım.

Bununla birlikte, şöyle bir göz atalım:

#### Marka {.tabs.active}

![Bu çalışma nasıl düzenlenmiştir.](cpu/branding.png) {.tab-float}

İlk olarak, Xbox'ın CPU'su **Pentium III** olarak tanımlanır. Peki, bu ne anlama geliyor? O dönemde (erken 2000'ler), Pentium serisi, bilgisayarları süper hızlı yapan tüm şık teknolojiyi bir araya getiren 'yeni yüksek kalite'yi temsil ediyordu ve alıcıların *en iyisi*ni istiyorsa hangi CPU'yu satın alması gerektiğine karar vermelerine yardımcı oluyordu.

Pentium III, Pentium II'nin yerine geçti, ki bu da orijinal Pentium'un yerine geçti. Dahası, ilk Pentium çıktığında, 80486'nın yerine geçti, ki bu da 80386'nın yerine geçti... Demek istediğimi anlamışsınızdır. Önemli olan 'Pentium'un esasen bir marka adı olduğu, iç işleyişle doğrudan ilişkilendirilmediğidir. Bu nedenle, daha da derinlere inmeliyiz!

Daha fazla kaybolmamak ve derinleşmek için, bilgiyi üç bölüme kataloglamış bulunmaktayım: **Instruction Set Architecture (ISA)** veya 'ISA' (CPU'ya komut vermek için kullanılan talimatlar grubu), **Mikro mimari** (ISA'nın silikonda nasıl uygulandığı) ve **Core** (belirli CPU modelini oluşturmak için mikro mimariyi paketlemek için kullanılan bileşen seti).

#### ISA {.tab}

Gerçekten de 'Intel'den bir kez bahsettiğimde, ünlü <strong x-id=“1”>x86</strong> komut setini tanıtmam an meselesidir.

x86'nın ilk biçimi, 1978'de piyasaya sürülen 16-bit bir CPU olan **Intel 8086** ile ortaya çıktı. Sonrasında, ISA daha fazla Intel CPU'su piyasaya sürüldükçe sürekli olarak daha fazla talimatla genişletildi (80186, 80286 ve benzerleri) [@cpu-thompson]. Bu nedenle, x86, daha fazla çığır açan özellik eklenirken (örneğin 'koruma modu' ve 'uzun mod'), parçalanmaya başladı. Bu sorunu çözmek için modern x86 uygulamaları genellikle 80386 ISA'sına (ayrıca **IA-32** veya **i386** olarak adlandırılır) temel olarak, 32-bit bir ortamda çalışan bir başlangıç noktası olarak hedefler.

Sonuç olarak, Intel, IA-32'yi **uzantılar** kullanarak geliştirdi, bu da yeni işlevlerin bir IA-32 CPU'sunda olup olmadığına bağlı olarak değişebileceği anlamına geliyor. Programlar, belirli bir geliştirmenin mevcut olup olmadığını kontrol etmek için CPU'yu sorgular. Xbox'ın CPU'su, iki uzantı içerir:

- **MMX** (Multimedya Uzantısı): Vektör işlemlerini hızlandırabilen 57 SIMD talimatı ve yalnızca tamsayıları içeren 8 adet 64-bit kayıt ekler.
- **SSE** (Akışkan SIMD uzantısı): MMX'in eleştirilerine (ondalık noktalı destek eksikliği ve ondalık noktalı birimi paralel olarak kullanamama) yanıt olarak eklenen başka bir SIMD türü uzantısıdır. 56 yeni talimat ekler ve dört 32-bit `float` değerini içeren sekiz 128-bit kayıta ('XMM' olarak adlandırılır) sahiptir.

İyi haber şu ki, konsol her zaman aynı CPU'ya sahip olacağından, programcılar kodlarını bu uzantıları kullanmak üzere optimize edebilirler çünkü bunlar her zaman mevcut olacaktır.

#### Mikromimari {.tab}

x86 talimatlarını yorumlayabilen bir devre inşa etme konusunda, Intel CPU'ları için çok sayıda farklı tasarım geliştirmiştir. Bazı tasarımlar, yeni bir Pentium Serisi'nin piyasaya sürülmesiyle (örneğin, Pentium 4) öne çıkarken, diğerleri Intel bir Pentium'un 'gelişmiş' bir versiyonunu piyasaya sürdüğünde (örneğin 'Pentium Pro') ortaya çıkar. Bununla birlikte, ilk Pentium'un piyasaya sürülmesinden bu yana, CPU modeli ve mikromimari artık aynı adı taşımamaktadır. Örneğin, 80486, yalnızca 80486 mikromimarisini kullanır (ve başka bir şey değil), ancak orijinal Pentium'un 'P5' mikromimarisine sahiptir.

Şimdi, Xbox CPU'su, geri kalan Pentium III işlemcileri ile birlikte **P6 Mikromimari**'yi (aynı zamanda 'i686' olarak da bilinir) kullanır. Bu, 8086'dan saymaya başladığımızda 6. nesildir ve şunları içerir:

- *Devasa* **14 Aşamalı Pipeline**: Bu, paralel olarak 14 talimatın işlenebileceği anlamına gelir. Öte yandan, bireysel talimatlar tamamlanmak için çok daha fazla döngü alabilir. [Daha önceki bir açıklamaya](game-boy-advance#tab-2-1-the-core) bakın.
- **Sıradışı Yürütme**: Mümkünse, CPU talimatların sırasını artırmak için yeniden düzenler ve verimliliği artırır.
- **Dinamik Yürütme**: P6, sıradışı ve [üst ölçekli](dreamcast#cpu) bir tasarım olduğundan. Geleneksel [dal tahmincisi](gamecube#the-powerpc-gekko) şimdi diğer tekniklerle birleştirilmiştir ('spekülatif yürütme' ve 'veri akış analizi'), yeni yeteneklerden faydalanmak için. Böylece, Pipeline durmaları daha da azaltır.

Bu özelliklere biraz daha yakından bakalım. Bu özellikler, [önceki konsollarla](gamecube#the-powerpc-gekko) çok benzer oldukları ortaya çıkıyor. Ancak diğer CPU'lar, tasarım açısından Intel olanlara kıyasla çok farklı. Tarihsel olarak, bir kişi, x86 tasarımının Intel'e örneğin bir boru hattına sahip bir CPU üretme yeteneği vermediği konusunda bir argüman yapabilirdi. Ancak bunu başardılar, peki neden...

#### CISC veya RISC {.tab}

İlginç bir şekilde, rekabette yer alan işlemciler [**RISC yönergeleri**](playstation#tab-1-1-a-bit-of-history) etrafında tasarlanmışken, Intel'in x86 işlemcileri bu şekilde tasarlanmamıştır ve bu nedenle **CISC** grubuna dâhil edilmektedir. RISC işlemcileri, CISC işlemcilerine kıyasla sadeleştirilmiş bir tasarıma sahip oldukları bilinmektedir. Bu, örneğin, (bellekten doğrudan işlem yapmanın aksine) yalnızca register'lardaki değerler üzerinde işlem yapan komutlar sağlayan bir **load-store mimarisinin** uygulanmasını içerir.

RISC işlemcilerinin avantajlarından biri, basit yaklaşımlarının CPU'larını modüler bir şekilde tasarlanmasına olanak tanımasıdır, bu da sıralı paralellik teknikleri ile performansı artırmak için kullanılabilir. Bu nedenle, MIPS ve PowerPC gibi CPU'lar, boru hattı aşamalarını, üst ölçekli tasarımları, sıradışı yürütme, dal tahminlemesi vb. gibi görmüştük. Öte yandan, CISC işlemcileri RISC işlemcilerinden daha eski ve daha farklı ihtiyaçları çözmeyi amaçlıyordu. Bu nedenle tasarımları, RISC İşlemciler kadar esnek değildir.

Orijinal soruya dönersek, P6 ilginç bir tasarımdır, çünkü bu CPU yalnızca CISC talimat kümesini (x86) anlar, ancak opcodes'ın bir alt kümesi **mikro kod** kullanılarak yorumlanır. En önemlisi, mikro kodu çalıştıran birim <strong x-id=“1”>yükle-depolama modeli</strong> [@cpu-gwennap] etrafında oluşturulmuştur. Bunun nedeni, P6 mimarisinin <strong x-id=“1”>Intel i960</strong>'ın (Intel'in bir zamanlar umut vaat eden RISC CPU'su) eski mühendisleri tarafından yazılmış olmasıdır. Sonuç olarak, Intel'in tarihi x86 ISA ile uyumluluğu bozmadan RISC işlemcilerle benzer avantajlar elde etmesine izin verdi. Zaman geçtikçe, 'CISC' ve 'RISC' gibi terimlerin herhangi bir modern CPU'yu kategorize etmek için çok belirsiz hale geldiğini söylemek doğru olacaktır.

Bir yan not olarak, mikrokod zaten silikona gömülüdür, ancak Intel'in bir hata veya güvenlik açığı keşfedildiğinde CPU'larını üretimden sonra düzeltmesine izin vererek güncellenebilir. Önceki makaleleri okuduysanız (örneğin [N64](nintendo-64) veya [PS2](playstation-2)), Intel'in mikro kodu **genel olarak erişilebilir değildir** (daha da belgelenmiş değil) ve Intel, tek 'bakıcı' dır.

#### Çekirdek {.tab}

![Coppermine dizaynı.](cpu/core.png) {.tab-float}

Intel, P6 mikromimarisini uygulayan birçok çip sevk etti. Xbox, **Coppermine** adlı bir model içerir. Bu aynı zamanda Pentium III'ün ikinci revizyonu olarak markalanmıştır ('Katmai' çekirdeğinin yerini alır) ve şu bileşenlere sahiptir:

- **32 KiB L1** önbellek: Talimatlar için 16 KiB, veriler için 16 KiB arasında bölünmüştür.
- Entegre **128 KiB L2** önbelleği: Bu biraz *garip*, çünkü hazır Coppermine'nin 256 KiB L2 önbelleği bulunmaktadır [@cpu-anand]. Aslında, Coppermine128 (Intel 'Celeron' markasında bulunan, düşük seviyeli Pentium alternatifi) aynı miktarda L2'ye sahiptir [@cpu-shvets]. Bu nedenle, bu muhtemelen üretim maliyetlerini düşürmek ve bu konsolu rekabetçi bir fiyatta tutmak için yapılmış olabilir.
- 133 MHz **Front-side bus (Ön taraf veriyolu)**: Bu, L2 önbelleği ile bellek denetleyicisini bağlayan otobüstür, bununla ilgili daha fazla bilgiye daha sonra göz atacağız.
  - Intel buna 'Front-side bus' adını verir, bu, L2 (harici önbellek) ile L1 (iç önbellek) arasında bağlantı kuran başka bir otobüsten ayırt etmek içindir. İkinci otobüs 'Back-side bus' olarak adlandırılır.

Coppermine ayrıca L2 önbelleğin orijinal uygulamasına iki 'geliştirme' ekler, bunlar **Advanced Transfer Cache (Gelişmiş Transfer Önbellek)** ve **Advanced System Buffering (Gelişmiş Sistem Tamponlama)**'dır. Özetlemek gerekirse, L2 önbellek çipte yer alır ve veriyolları daha geniştir, bu da Ön yüz veriyolunda olası darboğazları azaltmaya yardımcı olur.

Son olarak, çip, anakart üzerine takılan 'Micro-PGA2' soketini kullanır, ancak diğer tüm konsollar gibi, Xbox'un üzerine 'Ball Grid Array' veya 'BGA' ile lehimlenmiştir.

### P6 ve Pentium numaralarının sonu {.tabs-close}

İşte biraz daha tarih. P6 yıllarının ardından Intel, 'NetBurst' mikromimarisini (Pentium IV'de kullanılan) başarılı bir şekilde takip etmeyi planladı. Ancak, ardıllık çizgisi de burada sona erdi. NetBurst birçok modern tekniği uygulamasına rağmen [@cpu-netburst], aşırı güç tüketimi, ısı emisyonu (ortalama 85 W [@cpu-pentium4]) ve ölçeklendirilebilirlik sorunlarından dolayı sıkıntılar yaşamıştır - tüm bunlar tasarımın daha fazla devam etmesini engellemiştir.

Sonuç olarak, bu durum İsrail'deki Intel ekibini düşük güçlü P6 CPU'larını - 'Pentium M' - yeniden gözden geçirmeye ve daha güçlü bir ardıl geliştirmeye sevk etti. İlk sonuç **Yonah** çekirdeği idi, P6 tasarımının hafif bir gelişimi, **Core Solo** veya **Core Duo** olarak markalandı. Bununla birlikte, güç tüketimi makul rakamlara düştü (varyanta bağlı olarak 27 W [@cpu-coresolo] ile 31 W [@cpu-coreduo] arasında değişmektedir). Aylar sonra, <strong x-id=“1”>Core</strong> mikromimarisi P6'nın (ve NetBurst'ün) amiral gemisi ardılı oldu ve yeterince kafa karıştırıcı bir biçimde raflara <strong x-id=“1”>Core 2</strong> işlemci serisi olarak ulaştı. Yıllar içinde, ardışık mikromimari gelişmeleri birçok yönü iyileştirdi, ancak aynı hataları tekrarlamadan NetBurst'ten unutulan unsurları yeniden dahil etmeyi başardı.

İlginç bir not olarak, Yonah çekirdeğinin isimlendirilmesinin Yunus Kitabı için bir metafor olup olmadığını merak ediyorum, özellikle de peygamberin sonunda Ninova şehrini kurtarmak için yolunu düzeltmeyi başardığı olayla bağlantılı olarak. Gerçi bunu fazla düşünüyor da olabilirim.

### Anakart Mimarisi

PC tarihinin bir noktasında, anakartlar o kadar karmaşık hale geldi ki, yeni ihtiyaçları etkili bir şekilde ele almak için temelden tasarlanmış yeni tasarımlar geliştirilmek zorunda kaldı.

![Xbox Anakart Genel Bakış.](cpu/motherboard.png) {.open-float}

Geliştirilen yeni standart, çoğu anakart fonksiyonunu işlemek için iki ayrılmış yonga kullanmaya dayanıyordu. Bu yongalar şunlardır:

- **Northbridge (Kuzey Köprüsü)**: Bellek denetleyicisi olarak hizmet verir ve GPU ile arayüz sağlar.
- **Southbridge (Güney Köprüsü)**: Geri kalan I/O ile arayüz sağlar (örneğin  USB, ATA/SATA, PCI vb.)

Bu yongaların birleşimi **yonga seti** olarak adlandırılır ve bir anakartın yeteneklerini ve performansını koşullandırmak için yeterince önemlidir. Xbox, bir PC'ye çok yakın olduğu için, iki yongayı da içerir: **NV2A**, Northbridge ve GPU'nun bir kombinasyonu; ve **MCPX**, geri kalan I/O'yu işler.

{.close-float}

Her iki yonga da **HyperTransport** adlı özel bir otobüsü kullanarak birbirine bağlıdır. Bu teknolojiyi bazı PC anakartları da içeriyordu, sadece farklı bir marka ile (*nForce MCP-D*).

### Bellek Düzeni

Xbox, toplamda **64 MiB DDR SDRAM** içerir; bu tür RAM, rakiplerin sunduğuna göre çok hızlıdır. Ancak, bu RAM türü tüm sistem bileşenleri arasında paylaşılmıştır. Dolayısıyla, bir kez daha, karşımıza başka bir **unified memory architecture (birleşik bellek mimarisi)** veya 'UMA' düzeni çıkıyor.

![Anahtarlama ağının temsili. GPU iki banka kullanırken, CPU farklı bir banka kullanır ve bu süreçte çatışmayı azaltır.](cpu/memory.png)

Daha önce [ne kadar sorunlu](playstation-2#preventing-past-mishaps) olabileceğini görmüştük. Yine de, programlar verilerini belleğin farklı bankalarına yayarak bu sorunu ele alabilir. NV2A, farklı ünitelerin (CPU, GPU vb.) aynı anda bunlara erişmesini sağlayan bir **anahtarlama ağı** uygular \[@cpu-huang\] \[@cpu-informit\].

Ayrıca, konsol dahili bir sabit disk içerir ve *tesadüfen* geçici depolama için ayrılmış 750 MiB'lik üç bölüm içerir. CPU, bazı verilerini ana RAM'den taşıyabilir ve gerektiğinde geri yükleyebilir. Ancak, bu manuel bir süreçtir ve sanal RAM içermez.

## Grafikler

Daha önce gördüğümüz gibi, grafik işlemcisi NV2A çipinde bulunur ve MCPX gibi, Nvidia tarafından üretilmiştir.

![Xbox'ın anakartındaki Nvidia NV2A paketi.](gpu.jpg)

Bu şirket uzun süredir grafik işinde bulunmaktadır; *GeForce* serileri bilgisayar pazarında en popüler GPU markalarından biridir ve doğrudan ArtX/ATI'nin Radeon serisi ile rekabet eder. Genel olarak, bu, Microsoft'un konsol pazarındaki ilk denemesine rağmen Xbox'taki grafik kalitesi üzerinde iyi bir etki sağlar.

Her şey mantıklı görünüyor, ancak o zaman *gerçekten* bu kararı vermek kesin bir karar mıydı? Microsoft'un o dönemde Nvidia'yı diğer popüler markalardan (3dfx, PowerVR, S3, vb.) neden seçtiğini öğrenmek için şu anki tarihe güvenmek kolaydır, ancak o zamanlar rekabeti daha karmaşık hale getiren birçok seçenek vardı.

![Halo (2001) Xbox'ın 720p modunda çalışırken.](halo.png)

Örneğin, 3dfx'in popüler 'Voodoo 2' serisi, 90'ların sonuna kadar PC pazarının ~%70'ini elinde bulunduruyordu [@graphics-ign], Nvidia ise yeni 'GeForce 256'yi (GeForce serisinin ilk modeli) benimsetmekte zorlanıyordu. Bu durumda, Microsoft'un seçimi şimdi daha çok bir risk gibi görünüyor, ancak şu anda bildiğimiz gibi, bu risk sonunda karşılığını verdi.

> 1999'da Nvidia, şu an oldukları #1 oyuncu değillerdi. Sıkıntıdaydılar. Yeni GeForce mimarisi hala gençti ve birçok kişi onu beğenmiyordu. Şimdi her şey bitmiş gibi görünüyor. Ancak bunu bilmek için o tarihle ilgili araştırma yapmanız ve neden onu kullanmak için savaşmam gerektiğini bilmeniz gerekirdi. Haklıydım ama haklı olduğumu bilmiyordum; çok endişeliydim.
> 
> -- <cite>Seamus Blackley (Orijinal Xbox'ın ortak yazarı)</cite>

Sonraki bölümde, bu çipin iç işleyişini inceleyeceğiz. Şimdi, korkarım ki, CPU bölümü gibi birçok terim ve pazarlama terimi içinde karıştık, ama korkmayın! Temellerle başlayacağım.

### Mimari ve Tasarım

NV2A üzerinde bulunan GPU çekirdeği, popüler 'GeForce3' serisine dayanmaktadır \[@graphics-mslusarz\] \[@graphics-g3arch\], aynı zamanda Nvidia'nın teknik belgelerinde **NV20** olarak adlandırılır.

![NV2A'nın pipeline tasarımı.](NV2A_Pipeline.png) {.open-float}

Lütfen unutmayın ki, Xbox'ın GPU'sunun pipeline NV20 mimarisine dayanmasına rağmen, NV2A'nın geri kalan NV20 serisi ile uyumlu olmayan bazı modifikasyonlara sahiptir (en önemlisi, bir UMA ortamında çalışacak şekilde adapte edilmiştir).

Analiz edilen üniteler, bu makalenin kapsamını aşan birçok özelliğe sahiptir, bu nedenle bu bölüm ilginizi çekiyorsa kaynakları/bağlantıları kontrol etmenizi öneririm. Ayrıca, grafiklerle ilgili terimler sürekli evrildiği için (bu da bazı karışıklıklara yol açabilir), Xbox'ın yayınlandığı yıllarda Microsoft/Nvidia tarafından kullanılan terimlere güvenmeye karar verdim, bu nedenle diğer kaynaklardan daha fazla grafiklerle ilgili makaleler okumayı planlıyorsanız bunu hatırlayın.

{.close-float}

Bunu söylendikten sonra, Xbox'da karelerin nasıl çizildiğine bir göz atalım. Bazı açıklamalar GameCube'un [Flipper](gamecube#graphics) ile çok benzer olduğu için, bu makaleyi takip etmekte zorlanıyorsanız, bu makaleyi de okumanız size yardımcı olabilir.

#### Komutlar {.tabs.active}

![Komut aşaması.](pipeline/commands.png) {.tab-float}

İlk ve en önemlisi, GPU'nun CPU'dan komut alabilmesinin açıklanmasıdır. Bu işlem için GPU, grafik komutlarını etkin bir şekilde alıp işleyen bir komut işlemcisine sahiptir ve buna **PFIFO** denir (grafik komutları **Pushbuffer** olarak adlandırılır), ardından paketten çıkarılan komutlar **PGRAPH** (grafik işlemeden sorumlu blok) ve diğer motorlara iletilir.

Flipper gibi, geometrinin komut içine gömülmesi gerekmez. PGRAPH, grafik verilerini göndermek için birçok yöntem sunar. Örneğin, CPU, vertex verilerini içeren bir bellek içinde bir tampon ayırabilir ve ardından GPU'ya onları bu konumdan alması talimatını verebilir. Bu yaklaşım, tekrarlanan geometrinin gönderilmesini önlediği için verimli olabilir.

Sonraki açıklamalar PGRAPH içinde gerçekleşir.

#### Vertex {.tab}

![Vertex aşaması.](pipeline/vertex.png) {.tab-float}

Bu özellikle bu GPU için ilginç bir bölümdür. Bu aşamada GPU, geometrimiz üzerinde vertex dönüşümleri uygulama yeteneği sunar. Bu özelliği Flipper ile zaten görmüştük, ancak bu GPU, **programlanabilir bir motor** kullanır. Yani geliştiriciler, hangi vertex işlemlerinin nasıl gerçekleştirileceğini belirleyebilirler, önceden tanımlanmış bir programa güvenmek yerine. Bununla birlikte, NV2A aynı zamanda 'sabit' modda çalışabilir, eğer gerekiyorsa.

Bu aşama, bir **Vertex Ünitesi** tarafından yönetilir ve NV2A'da **iki tanesi vardır**. Her biri en fazla 136 komut içeren bir program yükleyebilir (aynı zamanda **mikrokod** olarak adlandırılır). Bu programa **vertex programı** denir ve çalışma zamanında yüklenir. Bir vertex programı şu işlemleri gerçekleştirebilir [@graphics-koppelman]:

- **Aritmetik işlemler** (örneğin toplama, çarpma, minimum vb.).
  - Bu, dot product gibi grafikle ilgili görevlere yardımcı olan 'yardımcı fonksiyonları' içerir.
- **Vertex Swizzling** (Yeniden düzenleme ve/veya çoğaltma).

Kısacası, vertex ünitesi, vertex'leri kayıtlarında manipüle ederek işler. Diğer bir deyişle, bir kere program yüklendikten sonra, 16 salt okunur kayıt ( 'input kayıtları' olarak adlandırılır), bir vertex'in öznitelikleriyle başlatılır (her vektör dört bileşen içerir). Daha sonra, ünite, giriş kayıtlarını kullanarak (program tarafından talimatlandırıldığı gibi) işlemin kümesini gerçekleştirir. Dahası, hesaplamaya yardımcı olması için 12 yazılabilir kayıt ve en fazla 196 sabit sağlanır. Son olarak, elde edilen vertex, başka bir blokta bulunan 11 yazılabilir kayda (her biri belirli bir amaçla sınırlı) saklanır ve bir sonraki aşamaya iletilir. Bu süreç, alınan her vertex için tekrarlanır.

#### Piksel {.tab}

![Fragment/Pixel aşaması.](pipeline/pixel.png) {.tab-float}

Bu aşamada, vertex'ler piksellere dönüştürülür. İşlem, her üçgeni çizmek için piksel üreten bir pikselleştirici (rasteriser) ile başlar. NV2A'nın pikselleştiricisi döngü başına dört piksel üretebilir.<!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while \[competitors embedded it\]()). -->Ardından, bellekten dokuları çekmek için **4 texture shader** kullanılır [@graphics-domine], bunlar aynı zamanda anizotropik filtreleme, mipharitalama ve **shadow buffering** uygulamak için otomatik olarak sunar. Gölgeleme işlemi, bir pikselin görünür mü yoksa aydınlatma kaynağı tarafından gölgelenmiş mi olduğunu test etmek için kullanılır, böylece doğru renk uygulanabilir. Bu noktada, GPU aynı zamanda kırpma ve erken bir [Z-testi](nintendo-64#modern-visible-surface-determination) gerçekleştirme olanağı da sunar (NV2A, Z-buffer'ı orijinal boyutunun dört katına sıkıştırarak bant genişliğini tasarruf etmeye katkıda bulunarak birçok performans iyileştirmesine neden olur).

Sonuçta elde edilen pikseller, bir **ortak kayıt kümesine** depolanır ve ardından her biri bunlara aritmetik işlemler uygulayan **8 register birleştirici**den geçirilir. Bu işlem, GPU tarafından yürütülen bir başka tür program olan **piksel shader**'larının kullanımıyla **programlanabilir** bir nitelik taşır [@graphics-wasson]. Her döngüde, her bir birleştirici, kayıt kümesinden RGBA değerlerini (RGB + Alpha) alır [@graphics-spitzer]. Ardından, shader tarafından belirlenen işleme dayanarak değerleri işler ve sonucu geri yazar. Son olarak, daha büyük bir miktar değer, yalnızca speküler renkleri karıştırabilir ve/veya sis ekleyebilen **son birleştiriciye** gönderilir.

Register birleştiricileri, [Texture Environment Unit](gamecube#tab-3-3-texture) ile benzer bir şekilde programlanabilir. Yani belirli bir kombinasyonu ayarlarıyla register'ları değiştirerek. Xbox'ın durumunda, PFIFO, PGRAPH'ı kurmak için pushbuffer'ları okur, bu da register birleştiricilerini ve doku shader'larını içerir.

#### Post-processing (İşlem Sonrası) {.tab}

![Post-processing aşaması.](pipeline/postprocessing.png) {.tab-float}

Piksellerin çerçeve belleğine yazılmadan önce, NV2A, ana bellekte ayrılmış blokları kullanarak gerekli testleri (alfa, derinlik ve damga) gerçekleştiren dört adanmış motor içeren **Raster Output Unit** veya 'ROP' içerir. Son olarak, piksel toplulukları (her biri dört piksel) yalnızca bu testleri geçerse geri yazılır.

Ayrıca, çerçeve belleği **multisampling (çok örnekleme)** bir teknik olan çok örneklemeli kullanılarak anti-aliasing uygulanabilir [@graphics-geforce3]. Aslında bu teknik, çokgenlerin kenarlarını, sürece eklenen farklı ofsetlerle birden çok kez örneklemektedir. Ardından, tüm örnekler ortalama alınır ve anti-aliased görüntüyü oluşturmak için kullanılır. Bu yaklaşım, önceki (ve daha fazla kaynak tüketen) anti-aliasing işlevini olan 'çok örneklemeli'yi, önceki Nvidia GPU'ları tarafından kullanılanı, değiştirdi.

### Programlanabilirliğin Önemi {.tabs-close}

Nvidia'nın geliştiricilere sunduğu yeni programlanabilirlik modelinin önemini vurgulamayı çok önemli buluyorum. Yıllar önce, grafik boru hattının büyük bir kısmı CPU tarafından hesaplanırken, GPU'ya pikselleştirme (rasterisation) işlemlerini hızlandırma görevi bırakılmıştı. 'Shader'lar (piksel shader'lar ve vertex programları için referans) tanıtıldığında, programcılar GPU'nun kaynaklarından faydalanarak boru hattındaki birçok hesaplamayı hızlandırabilir, CPU üzerindeki büyük bir iş yükünü hafifletebilir.

'Shader' kavramı, **Pixar'ın** 1989'da **Renderman**'ı genişletmek için geliştirdiği bir yöntem olarak tanıtıldı [@graphics-pixar], bu, 3D renderlama için kullanılan öncü yazılımlarından biriydi. Bu, 3D grafiklerin çoğunlukla endüstriyel ekipmanlar tarafından işlendiği zamanlara denk geliyor. Daha sonra, belirli konsolların [benzer prensipleri](nintendo-64#tab-1-1-reality-signal-processor) dahil ettiğini gördük, ancak Nvidia GeForce3 serisini piyasaya sürdüğünde, shader'lar tüketici pazarında bir standart haline geldi.

::: {.subfigures .open-float .tab-float .tabs-nested}

![Vertex programı tarafından elde edilen karmaşık animasyon.](vertex){.active video="true" title="Vertex Program"}

![Piksel shader'lar ile elde edilen farklı doku efektleri.](fragment){video="true" title="Pixel Shader"}

ChameleonMark (2002), GeForce3'ün shader'larını sergilemek için Nvidia tarafından geliştirilen bir demo.

:::

Vertex programları sayesinde GPU, model dönüşümlerini, aydınlatma hesaplamalarını ve doku koordinatı oluşturmayı hızlandırabilir. Sonuncusu, [Higher Order surfaces](playstation-2#infinite-worlds) oluşturmak için esastır. Bu sayede CPU, daha iyi fizik, yapay zeka ve sahne yönetimi sağlamaya odaklanabilir.

Piksel shader'ların durumunda, programcılar farklı efektler elde etmek için tekstürleri çoklu yollarla manipüle edip karıştırabilir, çoklu doku, speküler haritalama, çıkıntı haritalama, çevre haritalama vb.

Bu yaklaşımın getirdiği yeni bir programlama konsepti, **General Purpose GPU (Genel Amaçlı GPU)** veya 'GPGPU'dur ve GPU'ya özgü olmayan görevleri GPU'ya atamaktan oluşur. Bu, GPU'nun sadece grafik boru hattının çoğunu ele geçirmekle kalmayıp aynı zamanda özel hesaplamalar için etkili bir işlemci olarak hareket edebilmesi anlamına gelir (örneğin, fizik hesaplamaları). Bu, GPU'lar daha güçlü ve esnek hale geldikçe gelişecek yeni bir alandır. Ancak NV2A, donanım yeteneklerinin (vertex ve piksel shader'lar) ve geliştirilen özel API'ların (OpenGL'in 'state programları') bir kombinasyonu sayesinde bunu başarabiliyordu.

{.close-float}

Shader'ların gelecekteki makalelerde düzenli olarak ele alınacağını düşünüyorum. Ancak bu makalede, bunların biraz 'ilkel' olarak kabul edilebileceğini ve bazı insanların piksel shader'ların günümüz GPU'ları tarafından sunulanlarla hatta 'shader'lar' olarak kabul edilemeyeceğini iddia edebileceğini unutmayın.

### Xbox'ın Çerçevesi

Oyunların standart çözünürlüğü **640x480**'dir, bu altıncı neslin standartıdır. Ancak, bu kısıtlama sadece bir sayıdır: GPU, 4096x4096'ya kadar olan çerçeve belleklerini çizebilir, ancak donanımın kabul edilebilir performans sağlayıp sağlamayacağı ayrı bir konudur. Öte yandan, konsol, ekran ayarlarını genel olarak yapılandırma olanağı tanır, bu da pioner özellikleri (örneğin geniş ekran ve 'yüksek çözünürlük') geliştirmeye yönlendirebilir, geliştiricilerin bunları keşfetmelerini beklemek yerine ([Gamecube/Wii](wii#tab-1-1-standardised-widescreen)'de olduğu gibi).

Öte yandan, video kodlayıcı, çerçeve belleğinde ne varsa TV'niz tarafından anlaşılabilir bir formatta yayınlamaya çalışacaktır. Bu, oyunlar HD çıkış yapmadıkça (örneğin 720p veya 1080i, ki bunu sadece birkaç oyun yapar), geniş ekran görüntülerin [anamorfik](wii#tab-1-1-standardised-widescreen) hale geleceği anlamına gelir.

Bu söylendiğine göre, bu konsol hangi sinyalleri yayınlar? Oldukça fazla. Xbox, tipik PAL/NTSC kompozitin yanı sıra <strong x-id=“1”>YPbPr</strong> ('component' konektörlerini almak için ekstra bir aksesuar gerekir) ve RGB (hem SCART hem de VGA uyumlu) sunar. Sonuç olarak, pahalı adaptörlere ve benzeri şeylere ihtiyaç duymadan oldukça kullanışlıdır.

## Ses

Bu konsolun ses alt sistemi, profesyonel ses ekipmanı ve ATX anakart teknolojisinin etkisi altında büyük ölçüde geliştirilmiş olup, bazı özgün bileşenleri içermektedir. MCPX, iki ses bileşeni içerir: **Ses İşlem Birimi** ve **Ses Denetleyici Arabirimi**.

Ses İşlem Birimi veya 'APU', özel bir ses işlemcisidir ve üç alt bileşenden oluşur:

- **Voice Processor** veya 'VP': 48 kHz örnekleme hızında 256 ses sentezleyebilen özel bir devre içerir. Ayrıca iki DAHDSR zarf kontrolü ve çeşitli filtreler içerir. Ayrıca, 64 ses 3D olabilir (Stereo veya Mono'nun aksine). Sonunda, işlemci sesleri karıştırır ve 8 kanala sahip gruplarda kendi ses kontrolüne sahiptir.
- **Global Processor** veya 'GP': VP'den gelen ses verilerini işlemek ve çeşitli efektler uygulamak için kullanılan programlanabilir bir DSP.
  - VP'den gelen tüm kanalların buraya gitmesi gerekmez, bunun yerine belirli gruplar, üzerlerinde özel efektler (örneğin yankı) uygulamak üzere sadece buraya gönderilebilir.
- **Encode Processor** veya 'EP': İsminden de anlaşılacağı gibi, GP'den gelen son stereo sinyali üretir ve bunu ana RAM'de saklar.
  - EP'nin işlemlerinin GP tarafından da gerçekleştirilebileceğine dikkat edin, ancak kodlama için ayrı bir bileşene sahip olmak, konsolun Dolby Digital gibi daha iyi ses kodlama tekniklerini hedeflemesine olanak tanır.

APU yalnızca ses verilerini işler ancak çıkış yapamaz. Çıkış işlemi ACI'nin görevidir; ses verilerini RAM'den okur, çözümler ve sonucu PCM stereo örneği olarak gönderir (48 kHz örnekleme hızında 16 bit çözünürlük).

## Giriş/Çıkış

Daha önce belirttiğim gibi, tüm I/O erişimini bir araya getiren bir 'Southbridge' alt sistemimiz var. Bu Southbridge MCPX yongası aracılığıyla gerçekleştirilir.

![Konsol mimarisinin ana şeması. MCPX'in I/O'nun çoğunu nasıl kontrol ettiğine dikkat edin.](diagram.png)

Bu arada MCPX, PC muadili olan **nVidia nForce Multimedia and Communications Processor** veya 'MCP' den türetilmiştir. Bu, nForce 220/415/420 yonga setini kullanan anakartlarda bulunur [@io-reactos].

### Harici arabirimler

Konsol, şu harici bağlantı noktalarını içerir:

- **4 USB 1.1 portları**: Denetleyicileri bağlamak için kullanılır, ancak portun dış şekli yalnızca Xbox denetleyicilerine izin vermek üzere değiştirilmiştir.
  - Bu bağlantı noktalarında ayrıca, ekrana etkileşimli cihazlar bağlamak için kullanılan 'Video Sync' adlı ek bir pin bulunur.
- **10/100BASE-TX Ethernet portu**: Çevrim içi hizmetler için kullanılır (daha fazla ayrıntı daha sonra). Ethernet işlevi, anakartta bulunan ayrı bir transceiver tarafından gerçekleştirilir.

### Dahili arabirimler

MCPX ayrıca farklı alt sistemleri birbirine bağlamak için kullanılan aşağıdaki arayüzleri ve protokolleri sağlar:

- **SMBus**: I²C olarak da adlandırılan bu seri arayüz, bu bileşenleri birbirine bağlar:
  - **System Management Controller** veya 'SMC': Güç, sıcaklık ve fan kontrolü gibi çeşitli hizmetleri yönetir. Aslında bir PIC16LC mikrodenetleyici.
  - **System Temperature Monitor** veya 'STM': SMC tarafından aşırı ısınmayı tespit etmek için kullanılan bir dijital termometre (ADM1032).
  - Bir **256 B EEPROM**: Seri numarası, bölge, Ethernet MAC adresi vb. gibi benzersiz tanımlayıcıları depolayan yeniden yazılabilir bir ROM.
  - **Video Encoder**: Encoder GPU'ya bağlıdır, ancak SMBus üzerinden kontrol edilir.
- **IDE Controller**: Bu, Hard diskler, optik okuyucular vb. ile iletişim kurmak için PC'lerde geniş bir şekilde kullanılan standart bir protokoldür. Anakartı DVD sürücüsü ve HDD ile bağlamak için tek bir geniş şerit kablosu kullanılır.
- **Low Pin Count** veya 'LPC' veri yolu: Bu, PC'den alınan başka bir arayüz, ancak iyi *eski* PC BIOS'unu değil, bir BIOS karşılığını depolayan bir **Flash ROM** ile iletişim kurar. Flash, **1 MiB** boyutundadır.

### Kontrolcü

Xbox, **The Duke** adı verilen hantal bir kontrolörle geldi, giriş seti diğer rakiplerin sahip olduklarından farklı değildi... yüz düğmelerinde analog devre (8 bit genişliğinde) kullanımı dışında, oyunların düğme setinin çoğundan 'yarım basışları' algılamasına izin veriyordu. Öte yandan, The Duke o kadar çok eleştiri aldı ki Microsoft, konsolun piyasaya sürülmesinden aylar sonra onun yerine **Controller S** adlı yeni bir revizyonu çıkardı.

::: {.subfigures .toleft .tabs-nested}

![](controller/duke_front.png){.active title="Ön"}

![](controller/duke_back.png){title="Arka"}

The Duke (2001) [@photography-amos].

:::

::: {.subfigures .toright .tabs-nested}

![](controller/s_front.png){.active title="Ön"}

![](controller/s_back.png){title="Arka"}

Controller S (2002) [@photography-amos].

:::

Daha yakından bakıldığında, her iki denetleyici de özel bir bellek kartını takmak için iki **Memory Unit** yuvası içeriyordu, bu da kayıtların konsollar arasında paylaşılmasını sağlıyordu. Bunu fark ettikten hemen sonra, bu özelliğin bir [önceki rakipten](dreamcast#interactive-memory-card) miras alındığını varsaydım. Bu makaleyi yayınladıktan günler sonra, bu konsolun ortak yaratıcısı olan Seamus Blackley'a gönderdim ve o da bana oldukça ilginç yorumlarla hızlı bir şekilde yanıt verdi. Dreamcast benzerlikleriyle ilgili olarak, bana şunları söyledi:

> Dreamcast ile ilişkisi sadece tarihsel bir önyargıdır. Bu kazara oldu.
> 
> -- <cite>Seamus Blackley</cite>

### Gerekli bir adaptör

Bu denetleyicilerle ilgili başka ilginç bir gerçek de bunların sadece bir **breakaway dongle** olarak adlandırılan bir şekil adaptörü kullanılarak bağlanabilmesidir; bu, biri takıldığında kazaları önlemek için tasarlanmıştı.

> Sanırım aynı zamanda bir Xbox yüksek bir raftaysa ve birisi denetleyiciyi çekerse, bu ağır konsolu bir çocuğun başının üzerine çekecek şekilde plan yapmışlardı.
> 
> -- <cite>Xbox kullancısı</cite>

## İşletim Sistemi

Tamam, hadi odadaki fili ele alarak başlayalım.

> **Windows** çalıştırabilir mi?

Korkarım ki bu bir *evet ve hayır* cevabı: Bu konsolda bir 'Windows' var, ancak PC kullanıcılarının beklediği biçimde değil.

İlk olarak, Xbox'un işletim sistemi bir **Çekirdek** ve **kullanıcı arayüzü uygulamaları** (örneğin, Dashboard) oluşur. Bu, sırasıyla 1 MiB Flash ROM ve HDD'de saklanır.

Çekirdek, **Windows 2000'nin çekirdeğinden** önemli bir kod tabanını ödünç alır \[@operating_system-os\] (ki bu da modern **Windows NT** mimarisine dayanmaktadır). Sonuç olarak, Xbox donanımı için gerekli bileşenleri içeren kırpılmış bir Windows 2000 çekirdeği elde edilir. Bu nihayetinde optimal bellek verimliliği için tek bir yürütülebilir dosyada sıkıştırılır ve paketlenir. Sonuç olarak, oyun için özel olarak tasarlanmış, yüksek optimize edilmiş bir Windows makinesi olarak düşünebilirsiniz.

Xbox projesinin başında **DirectX ekibi** bulunuyordu [@operating_system-renegades]. Bu nedenle, kökeni [Dreamcast'e API'lerini getiren](dreamcast#windows-ce) Windows CE ekibiyle ilgili değildir.

### Önyükleme İşlemi

Pentium tabanlı sistemlerde olduğu gibi, sistem açıldığında CPU, **reset vektörü** (adres `0xFFFF.FFF0`) adresinde bulunan komutları çalıştırmaya başlar. Xbox için bu adres MCPX'te bulunan gizli bir ROM'a işaret eder (daha fazla ayrıntı 'Korsanla Mücadele ve Homebrew' bölümünde açıklanmıştır). MCPX, güvenlik sistemini başlatmak için rutinler içerir ve ardından Flash ROM'dan önyüklemeye devam eder. Flash ROM içinde, Xbox donanımını başlatır, küçük bir çekirdek önyükler ve karşılık gelen ekran animasyonunu gösterir.

Güvenli başlatma sırasında CPU, **korumalı moda** geçer. Bu kritiktir çünkü x86 CPU'lar ilk işlemciyle (Intel 8086) uyumluluğu korumak için her zaman **gerçek modda** başlar, ancak programcıların CPU'nun modern özelliklerine (1 MiB'den fazla belleğe erişebilmek gibi) erişmeleri gerekiyorsa, korumalı moda geçerek bunları manuel olarak etkinleştirmeleri gerekir.

Çekirdek yüklendiğinde, CPU'ya mikro kod enjekte eder (programlamak için değil, daha ziyade <em x-id=“3”>güncellemek</em> için). Son olarak, çekirdek geçerli bir DVD diski olup olmadığını kontrol eder. Eğer geçerli bir disk vardsa, çalıştırır. Aksi takdirde, Hard Drive'da depolanan bir kullanıcı etkileşimli kabuk yükler.

### Yeşil ekran

Şimdi, bir oyun diski takılmadığında Xbox'un yüklediği programı inceleyelim: **Dashboard**.

#### Etkileşimli kabuk {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Ana ekran.](shell/home.png){.active title="Ana Ekran"}

![Ayarlar ekranı.](shell/settings.png){title="Ayarlar"}

Dashboard birden çok hizmet sunar.

:::

Dashboard, [PlayStation menüsü](playstation-2#interactive-shell) veya [GameCube'un IPL'si](gamecube#splash-and-shell) ile karşılaştırıldığında işlevsellik açısından çok farklı değildir. Temelde, kullanıcıların bekleyebileceği tüm işlevleri içerir: Bazı ayarları düzenleme, kayıtları taşıma, DVD filmleri veya CD sesi oynatma; ve benzeri.

Bahsedilmeye değer bir şey, Dashboard'un ayrıca müziği bir ses CD'sinden kopyalayıp HDD'ye saklama yeteneği sunmasıdır. Bu müzik daha sonra herhangi bir oyundan alınabilir ve müzikleri 'kişiselleştirmek' için kullanılabilir. *İlginç!*

#### Güncellenebilirlik {.tab}

Evet, bu *resmi olarak* güncellenebilen ilk nesil konsol, ancak sadece HDD içeriği yazılabilir. Kernel kısmı (Flash ROM'da) yeniden yazılamaz, ancak sistem, belleğe yüklendikten sonra güncelleyebilir. Kernel güncellemeleri bu nedenle HDD'de saklanır.

Güncellemeler, perakende oyunlar aracılığıyla dağıtılır ve/veya sistem Xbox Live ağ hizmetine bağlandıktan sonra indirilir (daha fazlası ilerleyen bölümlerde).

Bazı güncellemeler, sistem işlevselliğini artırdı. Örneğin, ilk Xbox birimleri Xbox Live'ı içermiyordu, bu nedenle oyunlar, bu hizmeti yüklemek için bir güncelleme şeklinde gerekli dosyaları içeriyordu. Diğer güncellemeler güvenlik açıklarını düzeltmeye odaklandı.

## Oyunlar {.tabs-close}

Bu bölüm biraz kafa karıştırıcı olabilir, özellikle diğer konsollara göre düşük seviyeli belgelerin eksikliği nedeniyle. Microsoft/Nvidia'nın gerçekten geliştiricilerin kütüphanelerini kullanmalarını ve her şeyi unutmalarını istediği gibi görünüyor. Bu yine de ilginç bir stratejidir.

Her durumda, mümkün olduğunca bilgilendirici bir şekilde belgelemeye çalıştım, ancak çok derine inmeden.

### Geliştirme ekosistemi

Bu konsolda oyun geliştirmek, kütüphaneler, terimler ve benzeri açılardan oldukça karmaşıktır. Bu nedenle bunu Framework'lere göre ayırdım.

#### Donanım Soyutlama {.tabs.active}

![HAL'nin temsili.](hal/general.png) {.tab-float}

'Bir mikro kodlu programlanabilir yardımcı işlemci' gibi unsurların genellikle başta çok ses getirdiğini, ancak geliştiricilerin yeni donanımın gerçek karmaşıklığını keşfettikçe yavaşça azaldığını gördük.

Geliştirici perspektifinden, bu konsolun çekiciliğinden biri, düşük seviyeli işlevleri etkili bir şekilde yönetebilen popüler yüksek seviyeli kütüphanelerin bulunmasıydı. Microsoft ve Nvidia'nın stratejisi, donanımlarının her düşük seviyeli yönünü belgelemek yerine, donanımın iç işleyişi hakkında tam bilgi gerektirmeden geliştiricilerin bulmayı bekleyeceği işlemleri gerçekleştirebilen tam işlevli bir donanım soyutlama kütüphanesi sağlamaktan ibaretti.

Bu konsol için geliştirme yapmak için birkaç SDK bulunmaktadır; bazıları 'resmi' ve kısıtlayıcıyken, diğerleri 'pek de resmi olmayan' ama esnek olanlardır. En popüler olanlara bir göz atalım.

#### Microsoft XDK {.tab}

![XDK yapısı](hal/xdk.png) {.tab-float}

Microsoft'un Xbox Development Kit veya 'XDK', Xbox geliştirmek için kullanılan resmi SDK'dır. Bu, birçok aracı, kütüphaneyi ve derleyiciyi içeren bir pakettir. Özellikle, o dönem için *oldukça bir IDE* olan Visual Studio .NET (2002 sürümü) ile birlikte kullanılır.

Grafik kütüphanesi, Xbox özel özellikleri de içerecek şekilde genişletilen **Direct3D 8.0**'in özel bir uygulamasıdır. Direct3D, birçok GPU ile uyumlu kod geliştirmek için kullanılan çok güçlü bir API idi. Bu sayede, (teoride) PC geliştiricileri oyunlarını pek çok değişiklik yapmadan taşıma fırsatına sahipti. Ancak, vertex programları ve piksel shader'lar için kullanılan sözdizimi neredeyse tamamen montaj dilini anımsatır.

Bunun dışında, ses işlevselliği, ses verilerini taşıma konusunda endişe etmeden APU ile ilgilenen **DirectSound** tarafından sağlanır. Çok oyunculu gibi çevrim içi işlevselliğe ulaşmak için oldukça uygun olan bazı ağ kütüphaneleri de bulunmaktadır. Ağ API'si, yalnızca Windows tabanlı bir protokol kullanarak ağ iletişimini basitleştirmeye yönelik Microsoft'un girişimidini temsil eden **Windows Sockets** üzerine dayanmaktadır (ancak BSD soketlerine dayanan standartlaştırılmış bir tasarıma sahiptir).

Bunları denemek için Microsoft, kendi geliştirme kiti donanımlarını dağıttı, bu sadece çift RAM (toplamda 128 MiB) ile yeşille kaplı perakende bir ünitedir ve bazen farklı bir MCPX yongası içerir (Flash ROM'dan doğrudan önyükleme yapan MCPX-2 olarak adlandırılır). Bu, XDK tarafından imzalanmış yürütülebilir dosyaları başlatan bir geliştirme kiti sürümü içerir. Öte yandan, **Xbox Neighbourhood (Xbox Komşusu)** (bir Windows uygulaması), geliştirme kiti ile Visual Studio'yu bağlamak için kullanıldı ve böylece kolayca dağıtma ve hata ayıklama yapma imkanı sağlar. Xbox Live ile deneme yapmak için Microsoft ayrıca sandbox sunucuları sağladı.

#### NXDK {.tab}

![NXDK yapısı. Dikkat edilmesi gereken, bazı düşük seviye kütüphanelerin yüksek seviyeli olanlarla sarmalanmış olmasına rağmen, geliştirici tarafından hala erişilebilir olduğudur](hal/nxdk.png) {.tab-float}

Homebrew geliştiricilerine resmi SDK kullanımında Telif Hukuku davalarını önlemek için, Microsoft ile bağlantılı olmayan bir grup geliştirici, resmi SDK'nın bir alternatifi olan **Open XDK**'yı oluşturdu. Birkaç yıl sonra geliştirmesi durdu, bu nedenle başka bir grup onu devraldı ve yeni bir çatalı **New XDK** veya 'nxdk' olarak adlandırdı.

Devam eden proje, düşük seviyeli donanım bileşenlerini (mikro kod, push-buffer, vb.) tersine mühendislik yaptı ve sistemin her bölümüne yüksek seviyeli çağrılar sağlayan C/C++ dilinde yeni bir API tasarladı.

Direct3D'ye güvenmeden grafik katmanını basitleştirmek için nxdk, **CG derleyicisini** kullanır [@games-nxdk]. CG, shader geliştirmek için Nvidia tarafından oluşturulan bir dildir. CG, Xbox ile uyumlu kodu üretmek üzere diğer derleyicilerle zincirlenir. Derleme sırasında, CG kodu NV20 uyumlu montaj diline dönüştürülür, ardından bu, NV2A uyumlu mikro kodu ve pushbuffer üretmek için özel bir derleyici kullanılarak tekrar çevrilir. CG kullanmak istemeyenler için nxdk, düşük seviyeli shader'lar yazmak için geri kalan derleyicileri de açığa çıkarır.

Kullanılabilecek diğer API'lar, diğer hizmetleri yönetir (ses, ağ vb.). Sonuç olarak, bu kütüphane, Microsoft'un API'larını (tümüyle mülkiyeti olan de facto standart) kullanmamak karşılığında donanım üzerinde daha fazla kontrol sağlar. Bununla birlikte, nxdk, yasal Homebrew programları geliştirmek için en uygun seçenek olarak kalmaya devam eder.

### Medya Ortamı {.tabs-close}

Oyunlar çift katmanlı DVD diskler üzerine dağıtılır (8.5 GB'a kadar!), ve bunlar daha sonra özel bir DVD sürücüsü tarafından okunur ve bu sürücü, standart bir arabirim olan ATA'ya rağmen korsanlığa karşı korumalar içerir. XDK, verilerin diskin düzenini özelleştirmek için bazı araçlar içerdiğini belirtmekte fayda var, bu da programcıların okuma hızlarını iyileştirmelerine olanak tanır!

Şimdi, konsol aynı zamanda dahili 8 GB HDD içerir, oyunlar bunu kayıtları saklamak veya geçici içerik önbelleğini kullanmak için kullanır. Öte yandan sistem, dashboard, Xbox Live ayarları ve ağ ayarlarını saklar.

### Ağ hizmeti

[Modemlere](dreamcast#online-platform) veya [deneysel hizmetlere](playstation-2#network-service) takılmanıza gerek yok. Xbox, bir Ethernet bağlantısı ve merkezi bir çevrim içi altyapı (adı **Xbox Live**) sağlamak için şu anda genellikle kabul ettiğimiz her şeye sahipti.

![Xbox Live Logo.](shell/live.png) {.open-float}

Dahası, Xbox Live sadece çevrim içi çok oyunculu değil, aynı zamanda gerçek zamanlı sesli sohbet için ses paylaşımı da içeriyordu.

Ancak Xbox Live tam olarak nedir? Şirketlerin çevrim içi platformlarını oluşturmak için kullanabilecekleri bir dizi birbirine bağlı çevrim içi hizmetten ibaretti. Örneğin, hizmetlerden biri kullanıcı profillerini sağlar, böylece stüdyolar bir oyunun çevrim içi işlevlerine erişirken kimlik doğrulama yöntemi olarak kullanabilir. Resmi SDK'da, Microsoft, Xbox Live sunucuları ile iletişim kurmak için bazı API'ları içerir.

{.close-float}

Microsoft'un Xbox Live erişimini kime vereceğini kontrol ettiğini belirtmek önemlidir, bu nedenle geliştiricilerin oyunları tarafından kullanılacak kimlik doğrulama anahtarlarını almak için Microsoft'a kaydolmaları gerekecektir.

Gerçek çevrim içi deneyim, **Title Server (Başlık Sunucusu)**'da gerçekleşir, bu da dünya genelinde istemcilere (Xbox konsolları) cevap veren ve gerçek zamanlı iletişimi yöneten bir sunucu türüdür. Microsoft, SDK'larında bu tür sunucuları nasıl oluşturacaklarını göstermek için bazı örnekler sağlamıştır, ancak bunlar Windows sistemlere dayanıyor ve Windows Server çalıştıran veri merkezlerine dağıtılmak üzere tasarlanmıştır.

### Yeni bir trendin başlangıcı

Microsoft'un Xbox Live uygulamasını analiz ettikten sonra bunun sektör üzerindeki etkisini inceledik. şimdi oldukça açık görünüyor, değil mi? 'Doğru çevrim içi oyun' tarifi (yani Ethernet + Altyapı) her zaman oradaydı, ancak her şirket buna yatırım yapmak istemiyordu gibi görünüyor.

Aslında durum bu kadar basit değil: Microsoft, kullanıcılara bu işlevselliğe 'ihtiyaçları' olduğunu, çevrim içi çok oyunculu oyunun sadece isteğe bağlı bir ekleme olmadığını, ancak bazı oyunların temel bir parçası olduğunu 'ikna etmek zorundaydı. Aksi takdirde, Microsoft'un çabaları sadece bir başka 'çevrim içi girişim' anlamına gelecektir.

> Konsol oyuncularının çevrim içi oyun istemediği ve kimsenin bir PC mimarisinin bir konsol olabileceğine inanmadığı bir dünya hayal edin. İşte gerçekten öyleydi. Şimdi açık gibi görünüyor ama ÖYLE DEĞİLDİ.
> 
> -- <cite>Seamus Blackley</cite>

## Korsanla Mücadele ve Homebrew

Bu konsolun hazır bileşenler içerip içermediğinden bağımsız olarak, uygulanan bir dizi güvenlik önlemi bulunmaktadır.

Lütfen RSA şifrelemesinin burada tekrar eden bir konu olduğunu unutmayın, bunu daha önceki [Wii makalesinde](wii#tab-2-2-chain-of-trust) tanıtmıştım, bu nedenle RSA veya herhangi bir simetrik/asimetrik şifreleme sistemi hakkında bilgi sahibi değilseniz lütfen önce o makaleye göz atın.

Bunu söylendikten sonra, bir göz atalım.

### DVD kopya koruması

Oyun diskleri, yetkisiz kopyalamayı önlemek için hem mantıksal hem de fiziksel olarak korunmaktadır (bu şaşırtıcı olmamalı artık).

Mantıksal tarafta, oyun diskleri, geleneksel DVD okuyucularını aldatmak için birkaç 'tuzak' içerir, bu nedenle bunlar gerçek oyun içeriğini bulamazlar. Örneğin, diskin ilk alanı geleneksel bir DVD gibi biçimlendirilmiştir ve bu bölümün içinde, bir Xbox sistemine takılmadığı takdirde bir uyarı mesajı bulunur. Gerçekte, oyun verileri ikinci bir bölümde bulunur, ancak bu bölümü bulmak için gereken meta veri, geleneksel DVD okuyucularının beklediği bir formatta şifrelenmediği için sadece özel bir okuyucu (bu nedenle Xbox'unki) oyunu bulacaktır.

Fiziksel tarafta, maalesef bu zamanda sürücü ve disk arasındaki doğrulamayı gerçekleştirmek için ne tür veri değiş tokuşu yapıldığı halka açık değildir. Disk, erişilemeyen bir iç halka (geleneksel okuyucular tarafından) içerir ve bu verilerin nasıl kullanıldığı bilinmemektedir.

### Sistem Koruması

Microsoft'un uyguladığı güvenlik önlemlerinin geri kalanına bir göz atalım.

#### Giriş {.tabs.active}

BIOS'u içeren Flash ROM ve duyarlı verileri içeren EEPROM, bir **RC-4 anahtarı** kullanılarak şifrelenir. Çekirdek, BIOS'tan başlatıldıktan sonra, yalnızca Microsoft tarafından **RSA şifreleme** kullanılarak imzalanmış yürütülebilir dosyaları başlatacaktır.

Öte yandan, HDD tamamen özel ve belgelenmemiş bir dosya sistemi olan **FATX** ile biçimlendirilmiştir.

Bu, Microsoft'un uyguladığı güvenlik zincirine kısa bir girişti. Oldukça basit görünüyor, değil mi? Peki, **bir şey garip**: Yürütme, CPU tarafından kontrol edilir, ancak bu, hazır bir çiptir, bu nedenle RC-4 ile şifrelenmiş verileri nasıl anladı? Cevap şudur ki, **anlamaz**, bu nedenle bu konsolu piyasaya sürdükten sonra çatlamak için uğraşan hackerların hedefi olan şifrelemenin ilk aşamasını ayarlayan şifrelenmemiş bir kod bir yerde bulunmaktadır. Özellikle bu kod, piyasaya sürüldükten sonra bu konsolu kırmaya çalışan çoğu bilgisayar korsanının hedefiydi.

#### Bootstrap araması {.tab}

Intel'in işlemcilerinin yürütmeye başlayacağı adresin `0xFFFF.FFF0` olduğu resmi olarak belgelenmiş olduğundan, hacker'lar bu kodu Flash ROM'da aramaya odaklandılar (ki bu, RC-4 anahtarına yol açacaktı). Bu, bir hacker ve araştırmacı olan **Andrew 'bunnie' Huang**'un akademik araştırması sırasında denediği bir şeydi. Bulgular oldukça ilginçti: Flash ROM'un üst 512 byte'ı, anahtarı içeren güvenlik rutinlerini içeriyordu, ancak perakende sistem için çalışmıyordu [@cpu-huang]. Microsoft'un bu kodu prototip/hata ayıklama birimlerinden (muhtemelen kazara, çünkü bu blok, Microsoft'un uyguladığı algoritmaları ortaya koyuyor) kaldırmış olabileceğini düşünüyorum. Sonuç olarak, bu *çöp kod* olarak kabul edildi, bu nedenle bunnie gerçek `0xFFFF.FFF0`'un Flash ROM'da olmadığını fark etti.

Uzun lafın kısası, `0xFFFF.FFF0` **MCPX çipinde gizlenmişti**: Bu, bir kez yürütülen ve ardından gizlenen 512 B ROM içeriyordu. Bu ROM çıkartmak kolay değildi, bu yüzden bunnie, RC-4 anahtarını iletilirken yakalamak için HyperTransport veriyolunu dinlemeye başvurdu.

Ve böylece oldu, bunnie araştırmasıın bir parçası olarak anahtarı yayınladı ve Microsoft bundan memnun olmadı. Böylece hack topluluğu, bu konsolun ilk güvenlik katmanının kontrolünü ele geçirmenin bir yolunu buldu, ki bu çekirdeği içeriyordu.

#### Kalıcı Kilidin Açılması {.tab}

RSA katmanının çözülmesi hiç kolay olmadı, ancak hacker'lar çekirdeğe erişim sağladıklarında, bunu tersine mühendislik yapabilir ve RSA'yı tamamen etkisiz hale getirebilecek yamalar geliştirebilirdi. İşte bu nihayet gerçekleşti ve Homebrew topluluğuna kapı açıldı. Artık herkes, Microsoft'un onayına ihtiyaç duymadan değiştirilmiş bir Xbox'ta çalıştırılabilen programlar geliştirebilirdi. Bazı gruplar, Linux'u çalıştırma gibi daha fazla işlevi olan orijinal Dashboard'ın yerine geçen programlar geliştirdi!

Ancak bunun için kullanıcıların BIOS'larını özel donanım kullanarak değiştirmeleri gerekiyordu, ki bu da herkesin yapabileceği bir şey değildi. Daha sonraki yıllarda, ana hack'i kolayca önyükleyebilen yeni açıklar keşfedildi, bunlardan biri '007'nin sahte bir kayıt oyununu eklemekten oluşuyordu: Agent Under Fire' ya da 'Splinter Cell'in sahte bir kayıt oyununu eklemekten oluşuyordu ve bu oyun sabit diske kalıcı bir açık yükleyebilecek bir homebrew aracını başlatmak için bir arabellek taşması oluşturuyordu. Bu 'kalıcı açık' özelliği, orijinal Dashboard'ın sahte bir yazı tipi dosyası kullanılarak gerçekleştirilen başka bir arabellek taşması nedeniyle mümkündü \[@anti_piracy-pheonix\] (unutulmamalıdır ki yazı tiplerinin imzalanması gerekmiyordu).

#### Modifikasyon Çipleri (Modchips) {.tab}

İşin korsanlık tarafında **Modchips** de ortaya çıktı: Modçipler DVD sürücüsünü kurcalamak yerine, MCPX ROM'un kullanılmayan bir LPC portunda bulunması halinde ikincil bir BIOS'u önyükleyebileceği gerçeğinden yararlanarak Flash ROM'un üzerine bindi.

Yedek BIOS, her türlü oyun diskinin okunmasını etkinleştirebilen yamalı rutinleri içeriyordu (özellikle geleneksel olanları).

#### Tepki {.tab}

Microsoft, maliyetlerini düşürmek ve elektroniklerindeki maruziyeti azaltmak için birçok donanım revizyonu yayınladı. Aynı zamanda, hem Kernel'i hem de Dashboard'u güncelleyen yazılım güncellemeleri yayınlandı ve zayıflıkların sayısını azaltma çabası gösterildi. Ancak, bazı oyun güvenlik açıkları hala devam ediyordu ve geriye kalan şey başka bir 'fare ve kedi' oyunuydu.

Bahsetmekte fayda var ki, Xbox Live kendi başına bir korsanlık önleme mekanizmasıydı. Microsoft, Xbox Live'ı yetkisiz modifikasyonlara sahip konsollara engelleyebildi; bu, tipik kullanıcıların tek amacı korsan kopyaları oynatmak için konsollarını hacklemeden önce göz önünde bulundurmaları gereken bir değiş tokuştu.

## Hepsi bu kadar {.tabs-close}

Birkaç ay süren süreçte, araya sınavlar ve son teslim tarihleri girdiğinden, sonraki makale nihayet tamamlandı. Bunun *çok fazla* bilgi ve ıvır zıvır ekleme eğilimini sürdürdüğünü kabul ediyorum, ancak bu araştırma küçük (ve sinir bozucu) adımlarla başlamış olsa da, çok sayıda bilgi toplamama yardımcı olan özel bir topluluk olan XboxDev'den çok fazla destek bulduğum için mutluyum.

Bu konsol hakkında daha fazla bilgi edinmek isteyenler için XboxDev, daha önce Xbox homebrew'da imkansız olduğu düşünülen şeyleri yapmaya çalışan nxdk (farklı emülatörlerle birlikte) üzerinde aktif olarak çalışıyor, bu yüzden daha fazla bilgi için topluluklarını ziyaret etmenizi öneririm.

Kendi adıma, *bir sonraki makale* hakkında dikkatlice düşünmek için birkaç gün ayıracağım (ve muhtemelen unuttuğum bir konsolu analiz etmek için birkaç nesil geriye gideceğim).

Bir dahaki sefere kadar!  
Rodrigo
