---
name: PC Engine / TurboGrafx-16
short_title: PC Engine'in / TurboGrafx-16'nın Mimarisi
long_title: PC Engine'in / TurboGrafx-16'nin mimarisi
subtitle: Küçük ama hızlı bir sıçrama
date: 2020-12-18
release_date: 1987-10-30
cover: "pcengine"
generation: 4
seo_image_pos: "Bottom"
top_tabs:
  Models:
    - 
      title: "Japonya"
      caption: "PC Engine.<br>30/10/1987 tarihinde Japonya'da piyasaya sürüldü."
      file: japanese
    - 
      title: "Uluslararası"
      caption: "TurboGrafx-16.<br>Amerika'da 29/08/1989'da, Fransa'da 22/11/1989'da ve Birleşik Krallık ile İspanya'da 1990'da piyasaya sürüldü."
      active: true
      file: international
  Motherboard:
    caption: "TurboGrafx'in anakartını gösteriyor.<br> PC Engine'ininki oldukça daha küçüktü ve üst üste yerleştirilmişti."
  Diagram:
    caption: "Oldukça basit görünüyor, değil mi?"
#Historical
aliases:
  - /projects/consoles/pc-engine/
---

## Hızlı bir giriş

Hudson ve NEC, oyun konsollarının dördüncü neslini başlatmak için güçlerini birleştirdi. Ne yazık ki, rekabet geldiğinde çabaları sonunda gölgede kalacak. Yine de, konsolları pazardaki en kompakt tasarımlardan biri olarak üst sırada kalacak.

## Modeller ve varyantlar

Master System gibi, NEC de birçok revizyon ve garip varyasyonları piyasaya sürdü, ki bunlar başlangıçta takip etmesi biraz zor olabilir. Bu nedenle, ileride başvurmak üzere, işte en önemli modeller:

- **PC Engine**: Bu mimariyi kullanan ilk konsol, ancak sadece Japonya'da piyasaya sürüldü. Minimal bağlantı noktalarına sahip küçük beyaz bir kutu gibi görünüyor.
- <strong x-id=“1”>TurboGrafx-16</strong>: Amerikan pazarı için yeniden tasarlanmış PC Engine. Farklı bir dış görünüm ile aynı özelliklere sahiptir. Bana <a href=“nes”>NES/Famicom uyarlamasını</a> hatırlattı.
- <strong x-id=“1”>Supergrafx</strong>: PC Engine'in tamamen farklı bir kasa ve yeni donanımla geliştirilmiş versiyonu. Ayrıcalıklı özellikleri bu makalenin kapsamı dışındadır.
- <strong x-id=“1”>PC Engine Duo/TurboDuo</strong>: PC Engine/TurboGrafx ve satış sonrası CD-ROM² genişlemesinin bir kombinasyonu.

Bu makale PC Engine/TurboGrafx-16'ya odaklanacak, ancak PC Engine Duo/TurboDuo'nun piyasaya sürülmesine yol açan genişletmeleri de tartışacaktır.

## {.supporting-imagery}

## İşlemci (CPU)

Bu konsolun içinde, <strong x-id=“1”>Hudson Soft</strong> tarafından üretilen ve iki bileşeni barındıran bir çip olan <strong x-id=“1”>HuC6280</strong>'i buluyoruz. One of them is the CPU, which can operate at two speeds: <strong x-id=“1”>~1.79 MHz</strong> and <strong x-id=“1”>~7.16 MHz</strong>.

HuC6280, <a href=“master-system#cpu”>Z80</a> gibi hazır bir bileşen değil, NEC tarafından tasarlanmış özel bir CPU'dur. Resmi dokümanları inceledikten sonra, ünlü <strong x-id=“1”>MOS 6502</strong> ve <strong x-id=“1”>WDC'nin 65C02</strong>'sinin birçok davranışını kopyaladığı görülüyor. Bununla birlikte, tüm bunlar yazılımcı için ne anlama geliyor?

Başlamadan önce, MOS 6502'nin bazı parçalarını <a href=“nes#cpu”>NES makalesinde</a> zaten ele aldık ve ayrıca WDC ve Ricoh tarafından yapılan 16 bitlik bir varyantı <a href=“super-nintendo#cpu”> SNES makalesinde</a> anlattık, belki önce bunlara bir göz atmak isteyebilirsiniz.

65C02, Western Design Center tarafından üretilen MOS 6502'nin değiştirilmiş bir başka versiyonudur, üretim yaklaşımı (CMOS) nedeniyle çok daha verimlidir. Yazılım tarafında, 65C02 birkaç yeni talimat ekler ve bazı mevcut talimatların davranışını değiştirir [@cpu-opcodes].

65C02 hakkında çok daha fazla konuşabiliriz, ancak Hudson tarafından eklenen (65C02'yi tamamlayan) özelliklere odaklanmanın daha iyi olacağını düşünüyorum çünkü bunlar bu konsolu verimli bir şekilde çalıştırmak için kritik öneme sahip. 65C02 ile karşılaştırıldığında, HuC6280 [@cpu-hardware]:

- Bazıları CPU'yu çevreleyen bileşenleri hedef alan daha fazla <strong x-id=“1”>opcode</strong>.
- Harici bileşenleri arayüzlemek için kullanılan bir <strong x-id=“1”>8-bit I/O portu</strong>.
- Diğer cihazlardan gelen kesintileri almak için bir <strong x-id=“1”>interrupt controller</strong>.
- **Zamanlayıcı**. Belirli bir değerden geriye doğru sayarak çalışır ve bittiğinde CPU'ya bir interrupt gönderir.

Son olarak, genel amaç için <strong x-id=“1”>8 KB RAM</strong> bulunmaktadır.

### Bellek erişimi

*Henüz* bahsetmediğim bir şey de NEC'in CPU'nun yanına bir <strong x-id=“1”>Memory Management Unit</strong> veya 'MMU' ekleyerek <strong x-id=“1”>21-bit adresleri</strong> işlemesini sağlamasıdır (orijinal 6502'nin yalnızca 16-bit adresleri olduğunu unutmayın) [@cpu-hardware_manual]. Böylece erişilebilen bellek miktarı 64 KB'den <strong x-id=“1”>2 MB</strong>'a yükselir.

Bu MMU günümüz MMU'larından çok farklıdır, ilkinin bir <a href=“nes#going-beyond-existing-capabilities”>mapper</a>'a daha yakın olduğunu söyleyebilirim. Bununla birlikte, PC Engine'de bulunan MMU, 21 bitlik bir adres veri yolu oluşturmak için CPU'nun 16 adres hattıyla birleştirilen <strong x-id=“1”>eight 8-bit register</strong>'dan (<strong x-id=“1”>Mapping Register</strong> veya 'MPR' olarak adlandırılır) oluşur.

![MMU'nun adresleme yaklaşımı.](_diagrams/mmu.png)

Bu şu şekilde çalışır:

1. CPU, sırasıyla özel `TAM` ve `TMA` talimatlarını kullanarak herhangi bir MPR'den okuyabilir ve/veya MPR'ye yazabilir.
2. Fiziksel belleğe erişildiğinde, MMU sekiz MPR'den birini seçmek için CPU'dan A13-A15 adres hatlarını ayırır.
3. Nihai adres, CPU hatları (A0-A12'ye yerleştirilir) ile MMU hatları (A13-A20'ye yerleştirilir, burada çıkış seçilen MPR'nin 8 bitlik değeridir) birleştirilerek elde edilir ve 21 bitlik bir adres elde edilir.

Sonuç olarak, bu MMU fiziksel belleği 8 KB sayfalar halinde gruplandırır (13 CPU satırı = 8 KB sayfa), bu da CPU'nun MPR'yi değiştirmeden veya değerini değiştirmeden erişebileceği bellek miktarıdır.

Her neyse, anlamakta güçlük çekiyorsanız kendinizi bunaltmayın (bu sadece bazılarının ilginç bulabileceği alışılmadık bir yöntemdir).

## Grafikler

Grafikler, <strong x-id=“1”>Hudson Soft HuC6270</strong> tarafından halledilir, <strong x-id=“1”>Video Ekran Denetleyicisi</strong> veya 'VDC' olarak da adlandırılan ayrı bir çip. HuC6270 oyuncunun ekranda göreceği her şeyi çizer ve işlevi <a href=“master-system#graphics”>Sega'nın muadiline</a> çok benzemektedir, bu nedenle Hudson'ın sunduğu üründe neyin farklı olduğuna odaklanacağım için lütfen önceden bu makaleye bir göz atın.

### İçeriğin düzenlenmesi

Öncelikle, VDC bir <strong x-id=“1”>tile engine</strong> (5. nesil ortaya çıkana kadar hemen hemen standart) ancak PC Engine'in rakiplerine kıyasla önemli bir miktar olan <strong x-id=“1”>64 KB VRAM</strong> içerdiğine dikkat edin. Bu, daha sonra kontrol edeceğimiz yeni bir içerik türüne yol açabilir.

![VDP'nin bellek mimarisi.](_diagrams/vdc.png)

Grafik verilerinin düzenlenme şekli biraz kafa karıştırıcıdır: Hem CPU hem de VDC 16 bit adresler kullanır, ancak CPU yalnızca 8 bit kelimeleri işleyebilirken, HuC6270 16 bit kelimeleri VRAM'de saklar [@graphics-chibi]. Bu, RAM'deki tek bir adresin bir bayt içerdiği, VRAM'deki bir adresin ise iki bayt depoladığı anlamına gelir, bu nedenle geliştiricilerin VRAM'e veri aktarırken bu tutarsızlığa dikkat etmeleri gerekir.

Bunun nedeni Hudson'ın devreyi düzenleme şeklinden kaynaklanmaktadır: VDC'nin 16 bitlik bir adres veriyolu vardır ancak <strong x-id=“1”>sadece ilk 15 satır kontrol edilir</strong> (son satır her zaman '0' olarak ayarlanır), bu nedenle tek adresler ikinci bayttan alınır. Hudson'ın neden bu yola girdiğini bilmiyorum, ancak sistemde 128 KB VRAM olsaydı tüm bunların daha mantıklı olacağını biliyorum (çünkü 16 bitlik bir adres veriyolu yalnızca 64 KB'a kadar erişebilir, bu nedenle veri veriyolunu bir şekilde iki katına çıkararak 128 KB'a kadar erişilebilir). Belki de Hudson/NEC için asıl plan buydu?

### Kare Oluşturulması

Yukarıda bahsedilen ayrıntı tuhaflığı bir yana, işlevsellik açısından VDP çok basittir. Alt sistemin üç ana bileşeni vardır: Daha önce bahsettiğimiz **VDC** ve **VRAM** - ve **video kodlayıcı** ('VEC' olarak da adlandırılır, zamanı geldiğinde daha fazla göreceğiz).

Sistem birden fazla çözünürlük kullanabilir, bunun nedeni oyunun ekran zamanlamalarını kontrol etmek için parametre görevi gören bir dizi kaydı değiştirebilmesidir, bu da CRT'nin kareyi ne zaman ışınlamaya başlayacağını değiştirir (aşırı taramanın aksine). Minimum çözünürlük <strong x-id=“1”>256 × 224 piksel</strong> [@graphics-vdc_manual] iken, bazı homebrew'lar bu sistemin <strong x-id=“1”>512 × 240 piksele</strong> kadar çıkabileceğini kanıtlamıştır.

Şimdi bir çerçevenin adım adım nasıl çizildiğini görelim, bunun için <em x-id=“3”>Bonk's Adventure</em>'ın varlıklarını ödünç alacağım.

#### Karolar {.tabs.active}

![VRAM'de bulunan arka plan karoları.](graphics/tiles_background.png){.tabs-nested .active .tab-float .pixel .desktop-margined title="Arka plan"}

![VRAM'de bulunan Sprite karoları.](graphics/tiles_sprites.png){.tabs-nested-last .pixel title="Sprite'lar"}

Hızlı bir hatırlatma olarak, kutucuklar sadece <strong x-id=“1”>8x8 piksel bitmap</strong>lerdir ve renderer ekranın bazı bölümlerini çizmek için alır. VDC ile çerçeve iki düzlemden oluşur: Arka plan katmanı ve sprite katmanı.

VRAM içinde arka plan katmanına özel karoların tanımlandığı <strong x-id=“1”>Character generator</strong> adlı bir alan vardır. Bir karonun her pikseli dört bit kaplar, bu nedenle 16 renge kadar kullanabilir. Teorik olarak 4096 arka plan karesi tanımlanabilir, ancak VRAM'in çok daha küçük olması nedeniyle pratikte bu sayı daha azdır.

Öte yandan, Sprite'lar VRAM'deki ayrı bir bellek konumundan karolar kullanılarak çizilir, buna <strong x-id=“1”>Sprite Generator</strong> denir ve buradaki karolar <strong x-id=“1”>16x16 piksel</strong> genişliğinde olduğu için önceki Karakter oluşturucudan farklıdır.

Video kodlayıcı, <strong x-id=“1”>32 renk paleti</strong> (arka plan için 16 ve sprite'lar için 16) [@graphics-encoder_manual] depolayan ayrı bir çiptir, her palet 16 renk depolar ve her renk 9 bit genişliğindedir (Kırmızı için 3 bit + Yeşil için 3 bit + Mavi için 3 bit).

#### Karoların saklanması {.tab}

![Arka plandaki tek bir tile'ın yapılandırılması.](_diagrams/tiles.png){.tabs-nested .active .tab-float title="Arka plan"}

![Tek bir sprite karosu yapılandırılması.](_diagrams/tiles_sprites.png){.tabs-nested-last title="Sprite'lar"}

(Bu bölüm Hudson'ın 64 KB VRAM'den bu 16 bitlik tanecikle nasıl yararlandığını merak edenler için yazılmıştır, ancak makalenin geri kalanını takip edebilmek için bunu tamamen anlamanız gerekmez).

Şimdiye kadar bir karonun her bir pikselinin 4 bit (ya da <strong x-id=“1”>nibble</strong> olarak da adlandırılan yarım bayt) kullanılarak saklandığını tartıştık. Şimdi, Hudson <strong x-id=“1”>tepelerin dört adet 8x8 bitmap'den</strong> (sırasıyla 'CH0', 'CH1', 'CH2' ve 'CH3' olarak adlandırılır) oluştuğunu belirtir. Her harita 1 bit genişliğindedir, ancak dördü birleştirildiğinde 4 bit pikselli son karoyu oluştururlar.

Ayrıca, VRAM üzerindeki 16 bit hizalamayı hatırlıyor musunuz? <strong x-id=“1”>Her 16 bitlik kelime iki adet 1 bitlik bitmap</strong>in tek bir satırını saklar (8 satır + 8 satır). Böylece, sekiz girdi yazdıktan sonra, iki harita saklanacaktır (sadece bir yerine). Bunu daha iyi anlamanıza yardımcı olması için lütfen şemalara bir göz atın.

Aynı şey Sprite karoları için de geçerlidir, ancak bunlar 16x16 bitmap olduğundan, her bitmap 16 kelime kaplar. Başka bir deyişle, tek bir sprite karosunu saklamak için 64 kelime alınır (VRAM'de 8 bayta eşdeğerdir).

#### Arka Plan Katmanı {.tab}

![Render edilmiş arka plan katmanı.](graphics/background.png) {.tab-float.pixel}

Arka plan katmanı, <strong x-id=“1”>Background Attribute Table</strong> VRAM'deki girdilerle doldurularak oluşturulur, her girdinin konumu ekrandaki karonun X/Y koordinatını tanımlar. Her girdi Karakter Oluşturucu'dan karo indeksini ve renk paletini içerir.

Bu katmanın maksimum boyutu <strong x-id=“1”>1024 x 512 pikseldir</strong> (128 x 64 karo), ancak programcılar minimum olarak 256 x 256 piksel (32 x 32 karo) katman ayarlayabilirler.

Her zaman olduğu gibi, bu katman VDC'deki bazı kayıtların değeri değiştirilerek kaydırılabilir.

#### Sprite Katmanı {.tab}

![Render edilmiş Sprite katmanı.](graphics/sprites.png) {.tab-float.pixel}

VDC, 64 adede kadar sprite tanımlanabilen <strong x-id=“1”>Sprite Attribute Table Buffer</strong> adlı dahili bir bellek içerir. Tablonun her bir girişi bağımsız X/Y konumunu, renk paletini, karo indeksini ve H/V çevirmeyi saklar. Ayrıca, bir sprite'ı başka bir sprite ile birleştirmeye izin veren bir özellik vardır.

Her giriş 8 bayt uzunluğundadır, ancak 16 bitlik ayrıntı düzeyi nedeniyle bir miktar alan boşa harcanır.

Hepsinden önemlisi, <strong x-id=“1”>CPU bu tabloya erişemez</strong>, bu nedenle VRAM'de tamamlanması ve ardından VDC'ye kopyalamak için bir DMA kanalının etkinleştirilmesi gerekir (böylece ikincisi onu kullanabilir).

Sınırlamalarla ilgili olarak, tarama çizgisi başına en fazla 16 sprite olabilir. Diğer taraftan, sprite taşması veya <a href=“master-system#tab-4-1-collision-detection”>çarpışma</a> olduğunda oyunun bilgilendirilebilmesi için kesmeler ayarlanabilir.

#### Sonuç {.tab}

![Tada!](graphics/complete.png) {.tab-float.pixel}

Şimdiye kadar VDC'nin tüm ağır işleri nasıl yaptığını gördük, ancak son görev aslında <strong x-id=“1”>Video Encoder</strong> veya 'VCE'ye devredilmiştir. Hudson bu çipe <strong x-id=“1”>HuC6260</strong> adını vermiştir ve temel işlevi VDC'den 9 bitlik veri akışlarını almak, renk paletlerini uygulamak ve sonucu TV'ye (analog sinyal şeklinde) göndermektir.

Önceki makaleleri okuduysanız zamanlamanın önemine aşina olabilirsiniz. Burada bir istisna yok: İstenmeyen artefaktlardan ('kar' gibi) kaçınmak için, VCE yalnızca dikey bir kesinti sırasında güncellenebilir.

### Video Çıkışı {.tabs-close}

Video kodlayıcı RGB (Sync ile birlikte) ve YPbPr çıkışı verir, bu sırasıyla bir SCART kablosu veya komponent kablosuyla kullanım için idealdir. Şimdiye kadar iyi görünüyor!...

![PC Engine'in sağ tarafındaki RF bağlantı noktası.](rfport.jpg){.tabs-nested .active .open-float .tab-float title="RF"}

![TurboGrafx-16 [@photography-amos] için 'Turbo Booster', video kompozit ve güç sağlamak için Genişleme portuna bağlanır.](turbo_booster.png){.tabs-nested-last title="Aksesuar"}

... Ne yazık ki Hudson, kutudan video çıkarmanın tek yolu olarak bir RF modülatörü takmaya karar verdi, bu yüzden o kadar da harika değil. Ancak yine de, PC Engine 80'lerde tasarlandı, bu nedenle bu yaklaşım her türlü TV ile uyumluluğu garanti etti (konsolun aynı bölgesinden olduklarını varsayarak).

İşin iyi tarafı, Genişleme Bağlantı Noktası RGB video ve çoklu senkronizasyon türlerini taşıyan pinler içeriyor, ancak bunlardan yararlanabilmek için harici bir aksesuara ihtiyaç duyacaklar.

{.close-float}

## Ses

PC Engine, bu web sitesinde analiz edilen diğer birçok üçüncü nesil konsol gibi bir <a href=“master-system#audio”>Programmable Sound Generator</a> veya 'PSG' içerir. Diğer taraftan, bu PSG özellikle ses sentezlemek için önceden tanımlanmış bir dizi dalga formu (örn. pulse, triangle, vb.) kullanmak yerine <strong x-id=“1”>waveform memory</strong> ('waveform buffer' olarak da adlandırılır) yaklaşımına dayanır. Waveform memory, programcıların kendi waveformlarını tanımlamalarına olanak tanıyarak soundtrack düzenlemeleri için daha fazla esneklik sağlar.

![R-Type (1987).](rtype){.open-float video="true"}

Bu sistemde <strong x-id=“1”>altı ses kanalı</strong> vardır [@audio-manual], her biri bir dizi kayda yazılarak ayarlanır. İkincisi, aşağıdakileri içeren kanalların niteliklerini depolar:

- **Waveform shape**: Dalga biçimi döngüsü, her biri belirli bir zamanda dalganın genliğine karşılık gelen 32 adet 5 bitlik değer kullanılarak oluşturulur.
  - *Fire Pro Wrestling 2* ve *Bloody Wolf* gibi bazı oyunlar özel sesler elde etmek için oynatma sırasında değerleri bile değiştirmiştir (aktarımlar sırasında duyulabilir tıklamalar pahasına).
- **Frequency control**: İki adet 8-bit register, aynı dalga formuyla farklı müzik notaları üretmek için kanalın frekansını değiştirir.
- **Amplitude level**: Tek bir 8 bitlik kayıt, kanalın ne kadar yüksek ses çıkaracağını belirleyen iki adet 4 bitlik değeri saklar. Bu iki değer 'Sol' ve 'Sağ' **kaydırma kontrolüne** karşılık gelir ve rakiplerin sunduklarına kıyasla öncü bir özelliktir.

{.close-float}

### İlave fonksiyonlar

Bazı kanal gruplarının başka çalışma modları vardır. Örneğin, son iki kanal bir <strong x-id=“1”>gürültü üretecine</strong> bağlıdır. Dahası, ikinci kanal birinci kanalı modüle etmek için bir **Düşük frekanslı osilatör** olarak işlev görebilir (başlangıçta bunun <a href=“mega-drive-genesis#audio”>FM synthesis</a> ile eşanlamlı olduğunu varsaydım, ancak LFO'lar daha çok bir <strong x-id=“1”>vibrato etkisi</strong> elde eden bir 'alt küme'dir).

Modlardan bahsetmişken, <strong x-id=“1”>Direct D/A (DDA)</strong> adı verilen ve CPU'nun ses tamponu üzerine doğrudan yazmasını (PSG'yi atlayarak) sağlayan bir mod daha mevcuttur. Sonuç olarak, PC Engine <strong x-id=“1”>PCM örneklerini</strong> çalabilir. Bunlar hala 5 bittir ve CPU döngülerine sıkı sıkıya bağlıdır. Bununla birlikte, iki kanal birleştirilerek 10 bitlik örnekler üretilebilir! Örnekleri dinlemek için 'Kaynaklar' bölümüne göz atmanızı şiddetle tavsiye ederim [@audio-demo].

Sonuç olarak bu, PC Engine'in PSG'sini inanılmaz derecede esnek bir donanım parçası haline getiriyor.

### Son ses

PSG her şeyi karıştıracak ve bir stereo sinyal çıkışı sağlayacaktır. Ancak yine de, RF bağlantı noktasını atlamanın bir yolunu bulamazsanız, televizyonunuzdan yalnızca mono ses duyarsınız.

## Giriş/Çıkış

Bu konsol, bazıları konsolu dahili olarak (örneğin daha fazla RAM sağlayarak) ve harici olarak (daha fazla bağlantı noktası ve/veya diğer depolama ortamlarını okuma imkanı ekleyerek) tamamen geliştiren çok sayıda aksesuara sahipti.

![PC Engine'in arkasındaki genişleme portu.](expansion.jpg) {.open-float}

Bu da konsolun arkasında bulunan ve aşağıdaki bileşenlere erişimi mümkün kılan <strong x-id=“1”>Expansion Port</strong>'na bağlanmaktadır [@io-exp]:

- <strong x-id=“1”>+5 Volt hattı</strong>: Aksesuara güç sağlamak için.
- <strong x-id=“1”>A Voltaj giriş hattı</strong>: Orijinal PSU'yu atlamak ve konsola aksesuar üzerinden güç sağlamak için.
- <strong x-id=“1”>Ses Girişi ve Çıkışı</strong>: Stereo ses çıkışını alabilir ve/veya bir giriş hattından ses gönderebilir.
- <strong x-id=“1”>CPU veri ve adres hatları</strong>: CPU'nun aksesuara erişmesini sağlar ve bunun tersi de geçerlidir.
- <strong x-id=“1”>VDC veri hatları</strong> (Video kodlayıcıya gidenler): Aksesuarın bu hatlara dokunmasına izin verir, ancak ne için olduğundan emin değilim.
- <strong x-id=“1”>CPU kontrol hatları</strong>: HuCard ve CD algılama bayrağını, kullanılan hız modunu okur ve ayrıca kesme hatlarına erişimi vardır.
- <strong x-id=“1”>Analog video çıkışı</strong>: RGB + Vertical Sync ve Horizontal Sync + Composite sync dahil.

{.close-float}

NEC veya Hudson'ın bu konsolu ne ölçüde tasarladığını merak ediyorum, bir sonraki nesle adım atmak için yükseltilebilecek bir tür 'modüler konsol' inşa etmeye mi çalışıyorlardı?

### Diğerleri

Hâlâ kontrol cihazından (ya da bazı yerlerdeki adıyla Joypad'den) bahsetmedim. Diğer seçeneklere çok benziyor. Konsol sadece bir kontrolör portu sağlar ve oyunlar tuşlara basmak için bellekteki tek bir adresi kontrol eder. Bu adres 4 bitlik bir değer [@io-data] döndürür.

## İşletim Sistemi

Dahili ROM veya 'BIOS' ya da oyundan önce çalışan başka bir şey yok, dolayısıyla burada bir İşletim Sistemimiz yok. Sıfırlama vektörü `$FFFE` ve `$FFFF` adreslerinde bulunur (dahili kayıtların 8 bit olduğunu ancak bellek adreslerinin 16 bit olduğunu unutmayın, bu nedenle program sayacını başlatmak için iki kelimeye ihtiyacımız var) ve bu adresler oyun kartını işaret eder [@operating_system-sheldows].

Bu aynı zamanda 'temizlik' işlerinin (yani belleğin başlatılması, MMU'nun kurulması vb.) programcıya bağlı olduğu anlamına gelir.

## Oyunlar

Programlar 6502 assembly ile yazılır, ekstra 65C02 opcode'ları ve Hudson tarafından eklenenlerle zenginleştirilir.

### Standart Ortam

NEC/Hudson, herkesin sevdiği o <em x-id=“3”>hantal ve sıkıcı</em> kartuşlara güvenmek yerine, bu kez kredi kartı boyutunda <strong x-id=“1”>HuCard</strong> adında başka bir araç tasarladı. Bazı MSX oyunları tarafından kullanılan 'Bee Card' adlı eski bir ortamdan türemişlerdir.

![Tipik bir perakende oyunu.](hucard.jpg) {.open-float.pico}

İlginçtir ki, <a href=“master-system#medium”>Sega Card</a>'a çok benzerler ancak 35 yerine 38 pin içerirler [@games-pinout]. Dahili olarak, farklılıklar daha önemlidir:

- CPU bellek haritası, eşleyici olmadan <strong x-id=“1”>1 MB HuCard belleğe kadar erişebilir</strong>, bu nedenle oyunların çoğu 1 MB ROM'lara bağlı kalmıştır. Bu arada, 'Street Fighter II' gibi oyunlar 2,5 MB'lık bir ROM'da yer alıyordu! [@games-secrets]
- Kartın konsolu ekstra bir ses kanalıyla beslemesini sağlayan bir <strong x-id=“1”>Audio In</strong> pini var.

{.close-float}

### CD-ROM Eklentisi

Bu genişleme portu o kadar çok sayıda aksesuar ve genişleme birimine kapı açtı ki, sonunda basit bir PC Engine'i tamamen farklı bir şeye dönüştürdüler. Bu da yetmezmiş gibi, HuCard yuvası genişleme olanaklarını tamamlıyordu. Demek istediğim, bu makale için kayda değer yükseltmelere (bazıları konsolun gelecekteki revizyonlarına dahil edildi) odaklanmam gerekecek.

![CD-ROM² Okuyucu (TurboGrafx-16 sürümü) [@photography-amos]. O zamanlar Discman işinde miydiler?](cd/reader.png){.tabs-nested .active .open-float .tab-float title="CD Okuyucu"}

![Konsolu okuyucuya bağlamak için gerekli taban [@photography-amos]. Ayrıca güç ve kompozit video çıkışı da sağlar.](cd/compartment.png){.tab-nested title="Taban"}

![Her şey takılıyken nasıl görünüyordu [@photography-amos].](cd/fitted.png){.tabs-nested-last title="Takılı"}

Bir CD-ROM okuyucu ve **Sistem Kartı** adı verilen özel bir HuCard'dan oluşan **CD-ROM²** genişlemesine bir göz atalım, ikincisi oyunu başlatmak için bir **BIOS** görevi görür ve okuyucuyu arayüzlemek için bazı rutinler sağlar. Dahili olarak, okuyucu genel amaç için **64 KB RAM**, ADPCM örneklerini yayınlamak için başka bir <strong x-id=“1”>64 KB RAM</strong> ve son olarak verileri kaydetmek için <strong x-id=“1”>2 KB</strong> içeriyordu. Tahmin edebileceğiniz gibi, bu durum oyun geliştiricilerinin ekstra depolama ve CD ses avantajından yararlanmasını sağlarken, yayıncılara da oyunlarını tescilli olmayan bir ortamda dağıtmaları için ekonomik bir rahatlama sağladı.

{.close-float}

Yıllar sonra NEC, <strong x-id=“1”>Super CD-ROM²</strong> adında başka bir CD modülü çıkardı ve okuyucunun donanımı çoğunlukla aynı kalırken, genel amaçlı RAM'i 256 KB'a yükseltildi. Halihazırda bir CD-ROM² ünitesine sahip olan müşteriler, ekstra RAM (ve bir I/O rutini daha!) içeren <strong x-id=“1”>Super System Card</strong> satın alarak üniteyi 'güncelleyebiliyorlardı'. Tüm bunların ötesinde, NEC daha sonra <strong x-id=“1”>Arcade Card</strong> adı verilen ve bu durumda <strong x-id=“1”>2 MB RAM</strong> içeren yeni BIOS güncellemeleri gönderdi.

CD tabanlı oyunlar kesinlikle geliştirildikleri BIOS kartına bağlıydı, ancak yeni kartlar geriye dönük olarak uyumluydu (bazı istisnalar dışında). Bu nedenle Arcade Card, <em x-id=“3”>neredeyse</em> tüm CD-ROM tabanlı oyunları oynamak isteyen kullanıcılar için tercih edilebilir bir seçimdir. 'Neredeyse' dedim çünkü üçüncü parti şirketler de kendi BIOS kartlarını (yani 'Games Express CD Card') çıkardılar ve bunlar özellikle o stüdyonun oyunlarını oynamak için gerekliydi.

Super CD-ROM² ile birlikte NEC/Hudson, konsol, okuyucu ve BIOS kartını tek bir pakette toplayan <strong x-id=“1”>Turbo Duo</strong> adlı yeni bir PC-Engine/TurboGrafx-16 çeşidini de piyasaya sürdü.

### Diğer genişletmeler

Yayınlanan diğer genişletmeleri merak ediyorsanız, çevrim içi kataloğa [@games-accessories] göz atın.

## Korsanla Mücadele ve Homebrew

HuCard'lar bölge kilitlidir, yani bir PC-Engine oyunu TurboGrafx-16'da (veya tam tersi) kutudan çıktığı gibi çalışmayacaktır. Bunun nedeni, veri hatlarının sırasının Amerikan versiyonunda tersine çevrilmiş olmasıdır. Tahmin edebileceğiniz gibi, bu ucuz bir adaptörün üstesinden gelemeyeceği bir şey değil. Ancak, oyunlar sistemın bölgesini kontrol etmek için rutinler içerebilir.

Diğer taraftan, CD-ROM oyunları ne bölge kilitlidir ne de kopya korumalıdır, ancak önyükleme için hala bir Sistem Kartına ihtiyaç duyduklarını unutmayın (bu da bölge kilitlidir).

## Hepsi bu kadar

![PC Engine'imin RF kablosu üzerinden görüntüleniyor (_nostaljik amaçlar için... sanırım_). Matt'e bağış için çok teşekkür ederiz!](mypc.jpg)

İşte bu! Yılın son makalesini şimdi okudunuz.

2020, web sitesi için 'ilginç' bir yıl oldu (hepimizi etkileyen tüm olumsuz olayları bir kenara bırakalım). Yıl, [Wii makalesi](wii) ile başladı ve ardından [PS2](playstation-2), [Xbox](xbox), [Nintendo DS](nintendo-ds), [Master System](master-system) ve nihayetinde bu makale ile devam etti.

Ziyaretçi sayısında da birçok değişiklik oldu. İlk başta yavaş bir başlangıcı oldu, ancak sonra yaz aylarında şaşırtıcı derecede yüksek bir ziyaret sayısına ulaştı. Hacker News kullanıcılarına, OSNews'e, Reddit'e, Gigazine'e, YouTuber'lara, Twitter kullanıcılarına ve birçok diğer forum ve kişiye makaleleri paylaştıkları için teşekkür etmek istiyorum. Ve elbette, sitenin ayakta kalmasına yardımcı olan cömert bağışçılara büyük bir teşekkür ediyorum!

İyi bir Noel ve mutlu bir 2021 yılı geçirmenizi dilerim! Hoşça kalın!  
Rodrigo
