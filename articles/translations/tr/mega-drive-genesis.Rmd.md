---
short_title: Mega Drive'ın / Genesis'in Mimarisi
title: Mega Drive'ın / Genesis'in Mimarisi
name: Mega Drive/Genesis
subtitle: Yeni kompozisyon teknikleri
date: 2019-05-18
release_date: 1988-10-29
generation: 4
cover: megadrive
seo_image_pos: "Bottom"
top_tabs:
  Models:
    - 
      title: "Japonya"
      caption: "Mega Drive.<br>29/10/1988 tarihinde Japonya'da piyasaya sürüldü."
      file: japanese
    - 
      title: "Amerika"
      caption: "Genesis.<br>14/08/1989 tarihinde Amerika'da yayınlandı."
      file: american
    - 
      title: "Avrupa"
      caption: "Mega Drive.<br>09/1990 tarihinde Avrupa'da piyasaya sürüldü."
      file: european
      active: true
  Motherboard:
    caption: "Japon 'VA0' revizyonunu gösteriyor.<br>Üretim sonrası hataları düzeltmek için kullanılan (sonraki revizyonlarda düzgün bir şekilde düzeltilen) VDP'nin üstündeki alışılmadık ek karta dikkat edin."
    extension: "jpg"
    bib_source: photography-okqubit
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/mega-drive-genesis/
---

## Hızlı bir giriş

Sega (ve TV reklamları) bilmenizi istiyor: Konsol daha hızlı grafikler ve daha zengin sesler sağlamadığı sürece geliştiriciler iyi oyunlar üretemezler.

Yeni sistemleri, programlanmaya hazır çok sayıda *zaten tanıdık* bileşen içeriyor. Bu da teorik olarak geliştiricilerin sadece Sega'nın yeni GPU'su hakkında bilgi sahibi olmaları gerektiği anlamına geliyor... değil mi?

## {.supporting-imagery}

## İşlemci (CPU)

Bu konsolun iki genel amaçlı işlemcisi vardır.

### Lider

İlk olarak, elimizde **~7.6MHz** hızında çalışan bir **Motorola 68000** var, o zamanlar Amiga, (orijinal) Macintosh, Atari ST gibi birçok bilgisayarda bulunan popüler bir işlemci... İlginçtir ki, bu bilgisayarların her biri '6502 selefi'nin yerini almıştır ve [Master System](master-system) (Mega Drive'ın öncüsü) 6502 CPU kullanmazken, [NES](nes) kullanmıştır (ve bir şekilde Sega'nın amacı Nintendo tüketicilerini kazanmaktı). Sonuç olarak, bilgisayarların evrimi ile oyun konsolu teknolojisi arasında bir miktar korelasyon görebilirsiniz.

![Mega Drive'daki Motorola 68000 çipi, bu çip Hitachi'den ikinci kaynaktan alınmıştır.](m68000.jpg)

Konuya dönersek, 68k 'ana' CPU rolüne sahiptir ve oyun mantığı, I/O ve grafik hesaplamaları için görevlendirilecektir. Aşağıdaki yeteneklere sahiptir [@cpu-user]:

- **68000 ISA**: Bir dizi çarpma ve bölme işlem kodu da dahil olmak üzere birçok özelliğe sahip yeni bir komut seti. Bazı talimatlar 8 bit uzunluğunda ('byte' olarak adlandırılır), diğerleri 16 bit uzunluğunda ('word' olarak adlandırılır) ve geri kalanı 32 bit uzunluğundadır ('long-word' olarak adlandırılır).
- **On altı genel amaçlı 32 bit register**. Bu CPU'nun seti sekiz 'data register' (aritmetik hesaplamaların yapılabildiği) ve sekiz 'address register' (yalnızca bellek adreslerini saklamak için kullanılır) olarak ikiye ayırdığını unutmayın.
  - Yine de, 6502 ve Z80'in yalnızca 8 bitlik register sağladığı düşünüldüğünde bu büyük bir adımdır.
- **16-bit ALU**: Yani 32 bitlik sayılarda aritmetik işlemleri hesaplamak için fazladan döngüye ihtiyaç duyar, ancak 16 bit/8 bitlik sayılarda sorun yaşamaz.
- Harici **16-bit veri yolu**: Gördüğünüz gibi, bu CPU bazı '32-bit yeteneklere' sahip olsa da, tam bir 32-bit makine olarak tasarlanmamıştır. Bu veri yolunun genişliği, 16 bitlik verileri taşırken daha iyi performans anlamına gelir.
  - İlginçtir ki Motorola, bu konsolun piyasaya sürülmesinden dört yıl önce tam bir 32-bit CPU olan **68020**'yi piyasaya sürmüştür. Ancak Sega ikinci çipi seçseydi maliyetlerin fırlayacağını tahmin ediyorum.
- **24 bit adres veriyolu**: Bu, **16 MB'a kadar belleğe erişilebileceği** anlamına gelir. Bellek adresleri CPU içinde hala 32 bitlik değerlerle kodlanır (sadece üst bayt atılır). Bunu söyledikten sonra, Mega Drive ile veri yolu fiziksel olarak [@cpu-memorymap]'e bağlanır:
  - 64 KB genel amaçlı RAM.
  - Kartuş ROM (4 MB'a kadar).
  - İki Kontrolör.
  - Video Görüntü İşlemcisinin kayıtları, bağlantı noktaları ve DMA.
  - Anakart kayıtları (konsolu tanımlar).
  - Genişleme portları ('gelecekteki' aksesuarlar için kullanılır).
  - İkinci CPU'nun RAM'i, *bus arbiter* tarafından aracılık edilir.

Eğer 32 bitlik registerları işleyebilen bir CPU'da 24 bitlik adreslerin kullanılmasının ardındaki nedeni merak ediyorsanız: o dönemin ekipmanları 4 GB belleği yönetmek için pek de uygun değildi. Kullanılmayan hatların uygulanmasının performans ve para açısından maliyetli olduğu göz önüne alındığında, Motorola 32 bit kayıtlar ve 24 adres hattı ile mantıklı bir uzlaşmaya vararak geliştiricileri beş yıl sonra tam 32 bit CPU'nun (68020) gelişine hazırladı.

#### Tuhaf bir talimat seti

80'lerin [RISC devrimi](playstation#tab-1-1-a-bit-of-history) öncesinde, talimat setlerinin nasıl tasarlandığını birleştirmeye çalışan önceki bir dalgaydı. Özünde, 70'lerin tüketici CPU'ları (6502 veya 8080 gibi) belleğin nasıl erişileceğini önceden tanımlamış talimatlar sağlar (buna 'adresleme modu' denir). Motorola, 68000 ile talimat işlevini (opcode) adresleme modundan ayırdı ve bu sonuncuyu sadece başka bir parametre (operand) haline getirdi. Bunu yaparak, geliştiriciler artık (ihtiyaçlarına göre) en optimal adresleme moduyla aynı opcodları kullanabilir.

Bu ilkeye **instruction set orthogonality** adı verilir ve 70'lerin sonlarında yeni nesil CPU'ları büyük ölçüde etkilemiştir, ancak [RISC tasarımlarının](game-boy-advance#tab-1-1-the-rise-of-acorn-computers) devreye girmesiyle hızla dağılmış ve yükü etkili bir şekilde derleyicilere kaydırmıştır. Her durumda, Motorola 68k serisi 80'ler boyunca büyük popülariteye sahip oldu ve şirketlerin başka bir satıcıya geçiş yapmaya başlaması 90'ların başlarına kadar sürmedi.

### İkinci muz

Bu konsolda **~3,5 MHz** hızında çalışan bir **Zilog Z80** CPU daha bulunmaktadır. Bu, daha önce [Master System'in makalesinde](master-system#cpu) analiz edilen işlemcinin aynısıdır.

Z80 genel olarak **ses kontrol** için kullanıldı. Böylece, **16-bit adres veri yolu** aşağıdakilerden oluşur [@cpu-z80map]:

- 8KB RAM.
- İki ses çipi.
- 68000'in RAM'i (yine bus arbiter tarafından idare edilir).

Son olarak, **her iki CPU'nun da paralel** çalıştığına dikkat etmek önemlidir.

### Kullanılabilir hafıza

Ana CPU, genel amaçlı verileri depolamak için **64 KB** özel RAM içerir ve Z80, sesle ilgili işlemler için **8 KB** RAM içerir.

#### İletişim

Sega, **birbirinden habersiz** iki bağımsız işlemci seçti, peki oyunlar ikisini aynı anda nasıl yönetebilir? Ana program 68000'de yürütülür ve bu CPU daha sonra Z80'in RAM'ine yazabilir. Böylece 68000, Z80'in RAM'ine bir program gönderebilir ve Z80'e bunu yüklemesini söyleyebilir (Z80'e bir reset sinyali göndererek) [@cpu-using_z80]. Z80 kontrol altına alındıktan sonra, 68000 diğer işlemlerle ilgilenirken, ses alt sistemini yönetmek ve daha önce açıklanan yöntemi kullanarak belleği hareket ettirmek için kullanılabilir.

![Mega Drive/Genesis'in bellek mimarisi.](memory.png) {.no-borders}

Bir CPU diğerinin CPU veri yoluna girmek zorunda kalacağından ve her ikisi de aynı anda kullanamayacağından, her iki işlemciyi de durdurmak için etkinleştirilmesi gereken **Bus arbiter** adlı ekstra bir bileşen vardır, böylece bellek tehlikesiz bir şekilde yazılabilir.

Bu tasarımın düzgün bir şekilde yönetilmediği takdirde düşük performans gösterebileceğini belirtmek önemlidir, bu nedenle oyunların veri yolu hakemine özel dikkat göstermesi ve her iki CPU'yu da gerekenden daha uzun süre durdurmamaya dikkat etmesi gerekecektir.

## Grafikler

Cevap _Blast Processing!_, başka ne bilmeniz gerekiyor?

Tamam, _gerçek_ cevabı bilmek istiyorsanız: grafik verileri 68000 tarafından işlenir ve **Video Display Processor** (veya kısaca 'VDP') adı verilen özel bir çipte işlenir ve daha sonra ortaya çıkan kareyi (tarama çizgileri şeklinde) görüntülenmek üzere gönderir.

VDP **~13 MHz** hızında çalışır ve bölgeye bağlı olarak birden fazla çözünürlük modunu destekler: NTSC'de **320x224 piksele** kadar ve PAL'de **320x240 piksele** kadar.

### Çoklu ekran çözünürlüklerinin arkasında

Teknik olarak, VDP tarama satırı başına 40 veya 32 sütun [tiles](master-system#tab-1-1-tiles) sığdırabilir ve karo satırlarının sayısı bölgeye bağlıdır (NTSC'de 28 veya PAL'de 30) [@graphics-keil]. Yine de, çoğu PAL oyunu PAL sistemlerinde izin verilen ekstra karolarla uğraşmaz (muhtemelen iki bölge arasında tutarlılığı korumaları gerektiğinden ve NTSC ortak paydadır), bu nedenle VDP'ye 28 satırla (NTSC sistemlerinde yapacakları gibi) işleme talimatı verirler. Bu nedenle, VDP'nin kullanılmayan alanı bir arka plan rengiyle (aşırı tarama sırasında da kullanılır) doldurmaktan başka seçeneği yoktur.

Hata ayıklama özelliklerine sahip bir emülatörde `Mode Set Register #2`'yi kontrol ederek hangi PAL oyunlarının NTSC modunda işlendiğini görebilirsiniz (örn. Exodus). Sağdan dördüncü bit `0` ise, VDP NTSC modunda [@graphics-resolution] çalışıyor demektir.

![Sonic 2'de (1992) hızlı bir çok oyunculu mod sağlamak için oyun, bunun yerine 8x16 piksel karolar kullanarak (diğer değişikliklerle birlikte) tek oyunculu bir seviye oluşturmak için 'taramalı modu' etkinleştirir.](twopsonic/sonic2.png) {.side-by-side.toleft.pixel}

![Buna karşılık, Sonic 3'ün (1994) daha sofistike çok oyunculu modu, tek oyunculu seviyelerden ayrı olan özel 8x8 piksel karolara dayanır.](twopsonic/sonic3.png) {.toright.pixel}

Ayrıca, VDP'de **8x16 haritalar** oluşturmak üzere iki karoyu yığmak ve ardından bunları tek bir karo olarak ele almak için ayarlanabilen ek bir parametre vardır. Böylece dikey çözünürlük iki katına çıkar. Ancak bu, kareler artık taramalı olarak işlendiğinden (bir kare çift tarama çizgilerini, bir sonraki tek tarama çizgilerini ve benzerlerini işler) yenileme hızını yarıya indirir, bu nedenle işlevsellik açısından daha sınırlıdır. Sonic 2 ve 3'ün çok oyunculu modu bu modun iyi bir temsilidir.

Son olarak, VDP'nin aşırı tarama alanı için otomatik olarak dolgu eklediğini belirtmek gerekir, böylece oyunların hangi alanlara grafik çizmenin güvenli olduğu konusunda endişelenmesine gerek kalmaz ([NES'in 'tehlikeli bölgeleri'](nes#constructing-the-frame) ile olduğu gibi)

### İçeriğin düzenlenmesi

Grafik verilerinin işlenmesi açısından, bu çipin iki işlem modu vardır:

- **Mode IV**: [önceki](master-system#graphics) gibi davranan eski mod.
  - Bu, bu konsolun Master System oyunlarını hemen oynayacağı anlamına gelmez, çünkü önceki kartuşları bu konsola takmak için ek bir aksesuar (*Power Base Converter*) gereklidir. Dönüştürücü ayrıca I/O çipine Z80'i kontrol etmesi için talimat verecektir.
- **Mode V**: Yerel çalışma modu, biz buna odaklanacağız.

Peki ya Mod 0 ila III? Bunlar daha da eski olan *SG-1000*'e ait ve Mega Drive bunları desteklemiyor.

İlginç bir not olarak, daha sonra bu sistemin eski bir geliştiricisi tarafından Mod V'in komut yapısının (oyun içinde VDP'yi kontrol etmek için kullanılır) tasarımını TMS9918'den (SG-1000'de kullanılan ünlü video çipi) miras aldığı söylendi [@graphics-clarification]. Bu, üçüncü taraf geliştiricilerin resmi belgelere (ve ardından gelen lisans maliyetlerine) bağlı kalmadan Mode V'i kullanmalarını kolaylaştırdı.

#### Kullanılabilir hafıza

![VDP'nin bellek mimarisi.](VDP_architecture.png) {.no-borders}

Grafik içeriği belleğin üç bölgesine dağıtılır [@graphics-keil]:

- 64 KB **VRAM** (Video RAM): Grafik verilerinin çoğunu içerir.
- 80 B **VSRAM** (Dikey Kaydırma RAM'i): VDP dikey ve yatay kaydırmayı destekler ve V-scroll değerleri bazı nedenlerden dolayı bu ayrı alanda saklanır.
- 128 B **CRAM** (Renk RAM'i): Her biri 16 renk içeren dört palet girişini depolar (`transparent` dahil), sistem seçim için 512 renk sağlar. Ayrıca, *Highlight* ve *Shadow* efektleri her palete uygulanarak palet başına daha geniş bir renk yelpazesi elde edilebilir.

### Kare Oluşturulması

Aşağıdaki bölümde VDP'nin her bir kareyi nasıl çizdiği açıklanmaktadır, örnek olarak *Sonic The Hedgehog* kullanılmıştır. Başlamadan önce, [önceki](master-system#graphics) _modus operandi_'ye göz atmanızı tavsiye ederim çünkü burada birçok şey yeniden ele alınacaktır.

#### Kareler {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Birden fazla karo birbirine sıkıştırılmış. Gösterim amacıyla varsayılan bir palet kullanılmaktadır.](vdp_sonic/tiles.png){.active title="Hepsi"}

![Tek bir 8x8 piksel karo.](vdp_sonic/tiles_single.png){title="Tek"}

VRAM'de bazı karolar bulundu.

:::

Tıpkı Nintendo'nun PPU'su gibi, VDP de karo tabanlı bir motordur ve bu nedenle grafik düzlemleri oluşturmak için **tiles** (temel 8x8 bitmapler) kullanır. VDP durumunda, her bir karo 4 bayt uzunluğunda bir dizi ile kodlanır, burada her 4 bitlik giriş bir piksele karşılık gelir ve değeri bir renk girişine karşılık gelir (bir renk paletine işaret eder).

Oyun kartuşları karoları **ROM**'larında saklar (kartuşlarında bulunur) ancak VDP'nin bunları okuyabilmesi için **VRAM**'a kopyalanmaları gerekir [@graphics-ports]. Geleneksel olarak, bu yalnızca belirli zaman dilimlerinde mümkündü ve CPU tarafından gerçekleştiriliyordu, neyse ki bu konsol bu görevi VDP'ye yüklemek için özel devre ekledi (ayrıntılara daha sonra gireceğiz).

Karolar toplam **dört düzlem** oluşturmak için kullanılır ve bunlar bir araya getirildiğinde ekranda görülen çerçeveyi oluşturur. Ayrıca, uçakların karoları birbiriyle çakışacaktır, bu nedenle VDP hangi karonun görünür olacağına uçak türüne ve karonun **öncelik değerine** göre karar verecektir.

#### Arka Plan {.tab}

![Ayrılan arka plan haritası.](vdp_sonic/layer2.png){.tabs-nested .active .tab-float .pixel title="Tamamı"}

![Seçilen alanın işaretlendiği Tahsis Edilmiş Arka Plan haritası.](vdp_sonic/layer2_selected.png){.tabs-nested-last .pixel title="Seçildi"}

**Plane B** olarak da bilinen Arka Plan düzlemi, **statik tiles** [@graphics-macdonald] içeren kaydırılabilir bir döşeme haritasıdır (döşeme kümesi).

Bu düzlem altı farklı boyuta sahip olabilir: 256x256, 256x512, 256x1024, 512x256, 512x512, 1024x256. Programcılar, gerekli olacak kaydırma türüne daha iyi uyan boyutu seçebilirler.

Her karo **yatay ve/veya dikey olarak** çevrilebilir ve bir **öncelik** kümesine sahip olabilir.

Gösterilen örnekte, görüntülenmek üzere seçilen alanın bir kare olmadığını fark edeceksiniz... *Böyle olmak zorunda değil!*. VDP, tüm çerçeve, her bir tarama satırı veya her sekiz piksel için yatay kaydırma değerlerinin ayarlanmasına olanak tanır. Bu, geliştiricilerin seçilen alanı bir eşkenar dörtgen gibi şekillendirebileceği ve **perspektif efektlerini** simüle etmek için oyuncu hareket ettikçe açılarını değiştirebileceği anlamına gelir. Bunun gibi hileler düzlemi bozmaz, VDP her (seçilen) yatay çizgiyi alır ve ondan düzenli bir çerçeve oluşturur.

#### Ön plan {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Ayrılan ön plan haritası.](vdp_sonic/layer1.png){.active title="Tamamı"}

![Seçili alan işaretli Tahsis Edilmiş Ön Plan düzlemi.](vdp_sonic/layer1_selected.png){title="Seçili"}

Ön Plan düzlemi örneği, Pencere Düzlemi kullanılmaz.

:::

**Plane A** [@graphics-macdonald] olarak da bilinen Ön Plan düzlemi, bu düzlemin **daha yüksek önceliğe** sahip olması dışında Arka Plan Düzlemi ile aynı özelliklere sahiptir, bu nedenle burada işlenen karolar doğal olarak Arka Plan Düzleminin üstünde olacaktır.

Ayrıca, bu düzlem yeni bir *sub-plane* oluşturmak için kendini bölmeye izin verir: **Window Plane**. Tek fark, ikincisinin kaydırma yapmamasıdır.

Sonuç olarak, yeni öncelik değerlerinin ve ayrı uçakların oyun tasarımcılarına yeni manzara türleri sunma imkanı verdiğini görebilirsiniz. Ayrıca, her düzlemde farklı kaydırma hızları kullanılarak bir **paralaks etkisi** elde edilebilir.

#### Spritelar {.tab}

![Ayrılmış Sprite katmanı.](vdp_sonic/sprite.png){.tabs-nested .active .tab-float .pixel title="Tamamı"}

![Seçili alan işaretli olarak ayrılmış Sprite katmanı.](vdp_sonic/sprite_selected.png){.tabs-nested-last .pixel title="Seçili"}

Bu düzlemde karolar **sprites** olarak değerlendirilir. Bunlar bir **512x512 piksel** haritaya yerleştirilir ve sadece bir kısmı (VDP'nin çıkış çözünürlüğü) görüntülenmek üzere seçilir. Bu, istenmeyen sprite'ları gizlemek veya gelecekte gösterilecek diğerlerini hazırlamak için kullanışlıdır. VDP ayrıca eski bir [çarpışma algılama](master-system#tab-2-1-collision-detection) işlevi sağlar.

Sprite'lar 4x4'e kadar karo (32x32 piksel haritası) birleştirilerek ve 16 renge kadar (*transparent* dahil) seçilerek oluşturulur. Daha büyük bir sprite gerekiyorsa, birden fazla sprite birleştirilerek tek bir sprite haline getirilebilir.

Tarama çizgisi başına en fazla 20 ve ekran başına 80 sprite olabilir (bunun aşılması tüm katmanı bozacaktır).

VRAM'de Sprite'ların tanımlandığı bölge **Sprite Attribute Table** [@graphics-macdonald] olarak adlandırılır ve her giriş karo indeksi, katman koordinatları (x ve y), `link` değeri (hangi sprite'ların önce çizileceğini yönetir), öncelik (en yüksek önceliğe sahip sprite, çakışmalar sırasında görüntülenecek olandır), renk paleti indeksi ve dikey ve yatay çevirme içerir.

#### Sonuç {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Ortaya çıkan kare.](vdp_sonic/result.png){.active title="Çerçeve"}

![TV'ye yayınlanan çerçeve (NTSC formatı), VDP çerçeveyi otomatik olarak çoğu CRT TV'nin gizleyeceği overscan alanıyla kaplar.](vdp_sonic/overscan.png){title="Aşırı tarama ile"}

Tada!

:::

Kare çizilirken, sistem CRT'nin ışınının nereye baktığına bağlı olarak sırayla farklı kesme rutinlerini çağıracaktır. Muhtemelen önceki konsollarda gördüğünüz gibi, bu CPU'nun bir sonraki kare üzerinde çalışmasına (veya mevcut kareyi değiştirmesine) olanak tanır.

Geleneksel olarak, iki tür kesinti vardır: **H-Blank** (her yatay çizgi) ve **V-Blank** (her kare).

H-Blank birçok kez çağrılır ancak kısa rutinleri yürütmekle sınırlıdır. Ayrıca, yalnızca CRAM ve VSRAM'e erişilebilir, bu nedenle oyunlar yalnızca renk paletlerini güncelleyebilir veya düzlemlerini dikey olarak kaydırabilir.

V-Blank, saniyede yalnızca 50 veya 60 kez çağrılma dezavantajıyla (konsolun bölgesine bağlı olarak) daha uzun rutinlere izin verir, ancak tüm bellek konumlarına erişebilir.

Örnekteki aşırı tarama alanının sağ alt köşesinde bazı rastgele renkli noktalar bulunduğuna dikkat edin. Bu halk arasında **CRAM noktaları** olarak bilinir ve olan şey CPU'nun CRAM'deki paletleri güncellerken aynı zamanda VDP'nin kalan tarama hatlarını ışınlamasıdır (örnekte, bu aşırı tarama sırasında gerçekleşir). Bu çakışma, VDP'nin CPU'nun o anda yazdığı değeri getirmesine neden olur (CRAM'deki gerekli konumun aksine), böylece görüntü bozulur. Ancak, bu durumda oyun CRAM'i yalnızca aşırı taramada güncellediğinden, bu anormallik geleneksel CRT'lerde fark edilmez. Diğer oyunlar, CRAM noktalarının görünümünü dengelemek zorunda kalmak pahasına, daha fazla renk elde etmek için karenin ortasında paletleri güncellemeye çalışır.

### Özel bir transfer ünitesi {.tabs-close}

Şimdiye kadar CPU'nun çerçeveleri güncellemek için neler yapabileceğini tartıştık, peki ya VDP? Daha özel bir şey sağlıyor mu? Evet, bu çip, verilerin bellek konumları arasında **daha hızlı** ve **işlemcinin müdahalesi olmadan** taşınmasını sağlayan bir **Doğrudan Bellek Erişimi** (kısaca 'DMA') özelliğine sahiptir.

DMA, H-Blank, V-Blank veya aktif durumda (herhangi bir kesme dışında) etkinleştirilebilir ve VRAM, CRAM ve/veya VSRAM üzerine yazmak için kullanılabilir [@graphics-macdonald]. Ayrıca, DMA kullanan CPU RAM aktarımları sırasında CPU veri yolu bloke olur, bu nedenle iyi planlama performans elde etmek için kritik öneme sahiptir.

Bu özelliklerin olağanüstü kullanımı yüksek çözünürlüklü grafikler, akıcı paralaks kaydırma ve yüksek kare hızları sağlayabilir. Dahası, oyununuz TV reklamlarında da yer alabilir ve her yerinde *Blast Processing!* işaretleri bulunabilir.

### Video çıkışı

Bu konsolun ilk tasarımı (genellikle 'Model 1' olarak anılır) [Master System](master-system#video-output) ile aynı video çıkış portuna sahiptir. Sonraki 'Model 2' ve 'Model 3' bunun yerine bir mini-DIN bağlantı noktasına geçti.

## Ses

Bu konsolun ses özellikleri en hafif tabirle biraz alışılmışın dışında. Mega Drive bir yandan önceki nesilden mevcut ses teknolojisini sağlarken, diğer yandan mevcut olanın üzerine yeni (ancak karmaşık) bir sentez tekniği ekliyor. Yani, bazı açılardan, her iki nesle de sahip oluyorsunuz.

Bununla birlikte, Mega Drive iki ses yongası barındırır: Bir **Yamaha YM2612** ve bir **Texas Instruments SN76489**.

### Fonksiyonellik

Şimdi her biri *çok* farklı olduğu için her bir çipin neler sunduğuna bakalım.

#### Yamaha YM2612 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![FM kanalları.](fm_single){.active video="true" title="FM"}

![PCM kanalları.](pcm_single){video="true" title="PCM Örneği"}

Sonic The Hedgehog (1991).

:::

**Yamaha YM2612**, 68000 hızında çalışan ve **PCM örneklerini** (8 bit çözünürlük ve 32 kHz örnekleme hızı ile) çalabilen **altı FM kanalı** sağlayan bir **FM sentezleyicidir** [@audio-ymwiki].

Frekans modülasyonu veya 'FM' sentezi, ses sentezlemek için kullanılan birçok profesyonel teknikten biridir, 80'lerde popülaritesi önemli ölçüde artmış ve tamamen yeni seslere yol açmıştır (birçoğunu o dönemin pop hitlerini dinleyerek bulabilirsiniz).

*İnanılmaz derecede basitleştirilmiş* bir özetle, FM algoritması tek bir dalga formu (**taşıyıcı**) alır ve başka bir dalga formu (**modülatör**) kullanarak frekansını değiştirir. Sonuç, farklı bir sese sahip yeni bir dalga biçimidir. Taşıyıcı-modülatör kombinasyonuna **operatör** adı verilir ve nihai dalga formunu oluşturmak için birden fazla operatör birbirine zincirlenebilir. Farklı kombinasyonlar farklı sonuçlara ulaşır. Bu çip kanal başına 4 operatöre izin verir.

Geleneksel PSG sentezleyicilerle karşılaştırıldığında, bu ciddi bir gelişmeydi: Artık *önceden tanımlanmış* dalga formlarına takılıp kalmıyordunuz.

#### Texas Instruments SN76489 {.tab}

![PSG Kanalları.<br>Sonic The Hedgehog (1991).](psg_single){.tab-float video="true"}

**Texas Instruments SN76489**, üç darbe dalgası ve bir gürültü üretebilen bir **PSG çipidir**.

Bu aslında orijinal Master System'in [ses yongası](master-system#audio) ve VDP'nin içine gömülü. Z80 hızında çalışır.

'Pulse 3' kanalının kullanılmadığına dikkat edin. Bunun nedeni, oyunun gürültü kanalı için üçüncü darbe kanalını modülasyon [@audio-sonic] için ayıran bir mod kullanmasıdır; bu, Master System'de de bulunan bir işlevdir.

#### Birleştirilmiş {.tab}

![Tüm ses kanalları.<br>Sonic The Hedgehog (1991).](complete){.tab-float video="true"}

Her iki çip de aynı anda ses çıkışı sağlayabilir, daha sonra 'ses karıştırıcı' adı verilen ekstra bir bileşen her iki sinyali almak ve karıştırmakla görevlendirilir.

Son olarak, elde edilen analog sinyal ses çıkışı üzerinden gönderilir.

### Kondüktör {.tabs-close}

Teorik olarak, Z80'in bellek haritası, Z80'in bu iki çipe komut verebilen **tek CPU** olduğunu göstermektedir (bu, 68000 için bir rahatlama olabilir, çünkü ikincisi zaten diğer görevlerden bıkmıştır). Ancak pratikte Z80 kapatılabilir, böylece 68000'in YM2612'ye (ancak SN76489'a değil) erişimi olur [@audio-68kym]. Bu nedenle, basitlik amacıyla, bu makalede ses görevlerinin Z80'e atandığını varsayacağız.

Devam edersek, Z80 kendi başına bağımsız bir işlemcidir, bu nedenle 68000'den alınan müzik verilerini yorumlayabilmek ve iki ses yongasını buna göre etkili bir şekilde manipüle edebilmek için kendi programına (mevcut 8 KB RAM'de saklanır) ihtiyaç duyar. Bu program **sequencer** veya **driver** olarak adlandırılır.

### Kırma örnekleme

Bazı müzik bestecileri daha gerçekçi sesler çalmak için PCM kanalına odaklanmaya karar verebilir ve bunun için oyunların mevcut RAM'in geri kalanını kullanarak müziklerini sürekli olarak sıralaması ve yayınlaması gerekecektir. Ana kısıtlama, bu belleği doldurmak için önce ana veri yolunun **bloke** edilmesi gerektiğidir (yani bu zaman diliminde ses yongalarına hiçbir komut veya örnek gönderilemez). Aksi takdirde, ses anormallikleri ortaya çıkabilir (sessizlik, donmuş notalar, düşük örnekleme hızları, vb).

Bu nedenle, bu bölümü yukarıda bahsedilen kısıtlamanın üstesinden gelmeyi başaran birkaç oyun örneğine ayırmaya karar verdim. Sıradan davul kitlerine bağlı kalmak yerine, bazı oyunlar daha zengin örnekleri tek bir PCM kanalına aktarmanın inanılmaz yollarını buldu, bu örneklere göz atın:

::: {.subfigures .tabs-nested .toleft max_subfigures=1}

![PCM kanalları.](good_sampling/sonic_pcm){.active video="true" title="PCM Örneği"}

![Tüm ses kanalları.](good_sampling/sonic_complete){video="true" title="Tamamı"}

Sonic The Hedgehog 3 (1994).<br>Bu, Michael Jackson tarafından ortaklaşa yazıldığı söylenen parçalardan biridir. Her halükarda, genel müziğin öncekilere kıyasla farklı bir ritmi vardı.

:::

![PCM kanalı (kullanılan tek kanal).<br>Toy Story (1995).<br>Bu, 68000 [@audio-gamehut] yardımıyla gerçek zamanlı olarak sıralanır. Çok yoğun bir görev, yani sadece oyunun çok belirli noktalarında (örneğin ana menü) oynanabilir.](good_sampling/randy_pcm){.tabs-nested .tabs-nested-last .active .toright video="true" title="PCM Örneği/Tamamlanmış"}

CD kalitesine yakın olmadıklarını biliyorum (44.1 kHz'de 16-bit), ancak bu seslerin bir zamanlar bu konsolda yeniden üretilmesinin imkansız olduğunu ve bunun önceki nesle kıyasla ne kadar ilerlemeyi temsil ettiğini vurgulamadığımı unutmayın, bu yüzden kesinlikle en azından biraz hak ediyorlar!

### Destekli FM Kompozisyonu

Yamaha DX7'nin gösterge panelini kullanarak bir FM sentezleyiciyi programlamak zaten karmaşık sayılıyorsa, sadece 68k düzeneği ile müzik bestelemenin baş ağrısını hayal edin...

Neyse ki, Sega daha sonra Mega Drive müziğinin [@audio-gst] oluşturulmasını (ve hata ayıklamasını) kolaylaştırmak için MS-DOS PC'ler için **GEMS** adlı bir yazılım dağıttı. Çok eksiksiz bir araçtı, birçok şeyin yanı sıra çok sayıda *patch* (aralarından seçim yapabileceğiniz önceden yapılandırılmış operatörler) içeriyordu, bu da bazı oyunların neden *çok* benzer seslere sahip olduğunu açıklıyor.

Ayrıca, ses alt sistemi oyunların izin verilenden daha fazla kanal oluşturmasına ve her birine bir **öncelik değeri** atamasına olanak sağlamıştır, ardından konsol müziği çalacağı zaman, müzik kanallarını önceliklere göre mevcut yuvalara dinamik olarak göndermiştir. Ayrıca, yüksek önceliğe sahip ancak müziksiz kanallar otomatik olarak atlanabilir.

Kanallar ayrıca verilerinin içinde koşullular uygulayarak bazı **mantık** içeriyordu ve oyuncunun oyunda nasıl ilerlediğine bağlı olarak müziğin 'gelişmesini' sağlıyordu.

### (Bonus) Mega CD Sound {.interactive-only}

İşte ilginç bir gerçek: Mega CD eklentisi CD Ses için 2 ekstra kanal sağladı (diğer şeylerin yanı sıra). En ünlü oyunlarından biri olan Sonic CD çok etkileyici bir müzik kalitesine sahipti, ancak tüm oyunlar gibi döngüye girmesi gerekiyordu, sorun 1x CD okuyucuda müziğin döngüye girmesinin fark edilebilir boşluklara neden olmasıydı, bu nedenle oyun CD başlığı başlangıca dönerken başka bir PCM çipinden yürütülen döngü dolguları içeriyordu.

Bu dolgular sadece oyunun ilk betalarında bulunur ve piyasaya sürülmedi, 2011 yeniden yapımı nihayet onları içeriyordu. Bu, oyunun seviyelerinden biridir:

![MegaCD version (1993).](sonic_cd_original){.toleft audio="true"}

![Yeniden düzenlenmiş versiyon (2011).](sonic_cd_remaster){.toright audio="true"}

Mega CD'nin versiyonundaki boşluğu fark ettiniz mi?

## Oyunlar

Oyunlar çoğunlukla **68000 assembly** ile yazılırken, ses sürücüsü **Z80 assembly** ile uygulanmaktadır. Her ikisi de kartuş ROM'unda bulunur ve [mapper](nes#going-beyond-existing-capabilities) gerektirmeden 4 MB'a kadar boyutlandırılabilir.

### İlave fonksiyonlar

Genişletilebilirlik açısından, bu tasarım [NES](nes) veya [SNES](super-nintendo) kadar modüler değildi. Bu nedenle, 32x gibi daha sonraki eklentiler (68k'yı devralan yeni bir yonga seti içeren) VDP'yi atlamak zorundaydı (bu nedenle 'Konektör Kablosu' ihtiyacı).

Kartuşlar için sadece bir özel çip üretildi, **Sega Virtua Processor** \[@games-virtua\] (Samsung SSP1601'in yeniden markalanması, 16-bit Dijital Sinyal İşlemcisi), daha sonra karolar şeklinde kodlanmış çokgenler üretti (böylece VDP bunları okuyabilir). Her halükarda, SVP'nin üretiminin çok pahalı olduğu ortaya çıktığı için sadece bir oyun onunla birlikte gönderildi.

### İlk ağ girişimleri

Çevrim içi hizmetler yaygın olarak benimsenmeden (ve standartlaştırılmadan) önce Sega, oyunların kullanılması için çevirmeli bir hizmet olan **Sega Meganet** ile şansını denedi. Meganet, kullanıcıların _Sega Mega Modem_ adında ayrı bir aksesuar satın almalarını, ardından bunu konsolun arkasına (DE-9 konektörünün bulunduğu yere) takmalarını ve son olarak telefon hattına bağlamalarını gerektiriyordu. Oyunlar daha sonra modem ünitesini başka bir kontrolör olarak ele alacak ve onunla seri olarak iletişim kuracaktır \[@games-ioports\] (kontrolörler için kullanılan paralel kodlamanın aksine).

Her ne olursa olsun, bu özellik Sega'nın gelecekteki revizyonlarda DE-9 konektörünü kaldırmasından ve hizmeti tamamen kapatmasından önce sadece birkaç yıl sürdü.

## Korsanla mücadele ve bölge kilidi

Sega, ithal oyunları engellemek için kartuş yuvasının şeklini bölgeler arasında biraz değiştirdi, ancak aynı pin çıkışlarını korudu. Oyunlar ayrıca `Version` kaydının değerini kontrol ederek (bölge değerini çıkarır) 'bölge kilitleme' gerçekleştirebilir.

Bunu atlatmanın iki kolay yolu vardı. İlk olarak, şu üçüncü taraf kartuş dönüştürücülerden birini satın alarak. Ya da ikinci olarak, konsolu sökerek ve anakart üzerindeki `Version` kaydının değerini değiştiren pinleri köprüleyerek.

Yazılım korsanlığına karşı önlemler söz konusu olduğunda, en kolay kontrol SRAM boyutu üzerinde yapılıyordu: Kaçak kartuşlar herhangi bir oyunu sığdırmak için gerekenden daha fazla alana sahipti, bu nedenle oyunlar başlangıçta beklenen boyutu kontrol ediyordu. Programcılar ayrıca bilgisayar korsanlarının ilk SRAM kontrollerini kaldırması ihtimaline karşı oyunun rastgele noktalarında ekstra sağlama toplamı kontrolleri uygulayabilirler. Bunu yenmenin tek yolu, tüm çekleri sıkıcı bir şekilde bulmak ve tek tek kaldırmaktı...

## Hepsi bu kadar
