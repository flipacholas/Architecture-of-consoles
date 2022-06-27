---
name: Xbox
subtitle: Un temibile sfidante
date: 2020-06-26
releaseDate: 2001-11-15
cover: "xbox"
generation: 6
top_tabs:
  Model:
    file: international
    caption: "La prima Xbox\n<br>Rilasciata il 15/11/2001 negli Stati Uniti, il 22/02/2002 in Giappone e il 14/03/2002 in Europa"
  Motherboard:
    caption: "La prima versione della scheda madre\n<br>I controller sono collegati tramite una scheda figlia separata\n<br>I chip della SDRAM mancanti sono sul retro; sono presenti quattro slot di SDRAM non utilizzati"
  Diagram:
    caption: "Ogni controller è collegato a un hub USB separato"
# SEO
title: Architettura dell'Xbox
#Historical
aliases:
  - /projects/consoles/xbox/
---

## Breve introduzione

A quanto pare, Microsoft ha deciso di raccogliere il testimone di Sega. Qual è la loro proposta? Per gli sviluppatori, un sistema familiare e gradito; per gli utenti, servizi online molto apprezzati.

Per preservare la coerenza con le fonti utilizzate, le capacità di archiviazione saranno indicate in questo articolo sia nel sistema metrico (ossia in megabyte, o "MB"), che nello standard binario (ossia in mebibyte, o "MiB"); per questo:
- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

... e così via.

### Suggerimenti per la lettura

Dopo alcuni mesi dalla stesura di questo articolo, mi sono accorto che si tratta di uno dei più densi che abbia scritto finora. Dato che sull'Xbox c'è molto da dire, ho cercato di parlare di tutto.

Se siete davvero interessati a capire come funziona la console ma trovate che l'articolo sia difficile da seguire, il mio consiglio è di **prendervi tutto il tempo che vi serve**: l'articolo non va da nessuna parte. Concentratevi sulle parti che vi interessano, tenete il ritmo che più vi aggrada, date un'occhiata ai collegamenti nella sezione delle fonti se possono servirvi e, soprattutto, non mettetevi fretta: non c'è nessun esame!

---

## La CPU

Il processore utilizzato nell'Xbox è una versione leggermente modificata dell'**Intel Pentium III** (una CPU commerciale per computer) a una frequenza di **733 MHz**. Basta questo per ipotizzare che la console possa essere un PC sotto mentite spoglie: senza che vi risponda io, vi garantisco che alla fine dell'articolo sarete perfettamente in grado di farvi un'idea.

Ad ogni modo, al tempo i Pentium, assieme ad altre linee di CPU sviluppate e prodotte da Intel, erano incredibilmente popolari nel mercato dei computer. La quota di mercato di Intel era talmente vasta che le loro CPU erano leader di fatto in termini di qualità: quando l'utente tipico voleva un buon computer e aveva a disposizione un certo budget, gli bastava una *qualsiasi* macchina con una CPU Intel. Oggi sappiamo tutti che in realtà i fattori coinvolti sono molti di più, ma questa era l'immagine che, all'epoca, l'ufficio marketing di Intel riusciva a proiettare verso il pubblico.

### Informazioni tecniche

Ora che conosciamo in che posizione era Intel, torniamo a parlare della console. Durante le mie ricerche, speravo di trovare documenti tecnici tanto dettagliati quanto quelli disponibili per altre CPU (MIPS, SuperH, ARM ecc.); al contrario, mi sono imbattuto in un diluvio di definizioni da reparto marketing che è riuscito solo a ostacolarmi. Quindi, in questo articolo strutturerò a modo mio tutte le informazioni necessarie a comprendere il funzionamento della CPU. Proverò anche a introdurre alcuni dei termini usati da Intel nelle comunicazioni riguardanti questo processore.

Iniziamo:

{{< tabs >}}

{{< tab name="Il marchio" active="true" >}}

{{< figure_img float="true" src="cpu/branding.png" alt="Il marchio" >}}
Come è organizzata quest'analisi
{{< /figure_img >}}

{{% inner_markdown %}}
Partiamo dalle basi: la CPU dell'Xbox è identificata come **Pentium III**. Che vuol dire? All'inizio del terzo millennio, la serie Pentium rappresentava una nuova generazione di CPU. Il marchio identificava i nuovi processori "di alto livello", con tutte le scintillanti innovazioni in grado di rendere i computer superveloci, e aiutava gli acquirenti a scegliere cosa comprare se volevano *solo il meglio*.

I Pentium III sostituivano i Pentium II, che a loro volta sostituirono i primi Pentium. E non finisce qui: il primo processore Pentium andò a sostituire il precedente 80486, che altro non era che il sostituto dell'80386... insomma, avete capito. "Pentium" è principalmente un marchio commerciale e non è direttamente ricollegabile al funzionamento interno della CPU. Come in Inception: dobbiamo andare più a fondo!

Per evitare di perderci, ho organizzato le informazioni salienti in tre sezioni che, considerate integralmente, descrivono l'intero chip. Inizieremo con l'**Instruction Set Architecture** o "ISA" (l'insieme di istruzioni macchina utilizzate per far funzionare la CPU), quindi passeremo alla **microarchitettura** (il modo in cui l'ISA è implementato a livello hardware) e finiremo con il **core** (i componenti utilizzati per impacchettare la microarchitettura e formare uno specifico modello di CPU).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="L'ISA" >}}

{{% inner_markdown %}}
Era solo questione di tempo, dopo aver menzionato il nome "Intel": è ora di parlare del suo famoso instruction set, ossia **x86**.

La prima versione di x86 venne introdotta nel 1978 insieme all'**Intel 8086**, una CPU a 16 bit. Le CPU Intel rilasciate in seguito (la 80186, la 80286 e così via) servirono come occasioni per espandere costantemente le capacità di questa ISA con nuove istruzioni. Di conseguenza, la progressiva introduzione di nuove funzionalità (come la "modalità protetta" e la "modalità lunga") portò x86 a frammentarsi. Per risolvere questo problema, generalmente le applicazioni x86 moderne sono progettate per avere come punto di partenza l'ISA 80386 (nota anche come **IA-32** o **i386**), che, tra le altre cose, si basa su un'architettura a 32 bit.

In seguito, Intel potenziò IA-32 tramite delle **estensioni**, ossia nuove funzionalità che potevano essere incluse nelle CPU che utilizzavano IA-32 come instruction set; sarebbero poi stati i programmi a verificare se nella CPU era presente una determinata estensione. La CPU dell'Xbox include due estensioni:

- **MMX** (Multimedia Extension): aggiunge 57 istruzioni SIMD e 8 registri a 64 bit (utilizzati solo per valori interi) per velocizzare le operazioni vettoriali.
- **SSE** (Streaming SIMD Extension): un'altra estensione SIMD che risolve i punti deboli di MMX (il mancato supporto dei calcoli in virgola mobile e l'impossibilità di utilizzare l'unità in virgola mobile in parallelo). Aggiunge 56 nuove istruzioni e 8 registri a 128 bit (denominati "XMM") che contengono quattro `float` (numeri a virgola mobile) a 32 bit.

La buona notizia per gli sviluppatori è che poiché la CPU nella console sarebbe rimasta sempre la stessa, era possibile ottimizzare il codice per sfruttare appieno queste due estensioni in quanto sarebbero state sempre presenti.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="La microarchitettura" >}}

{{% inner_markdown %}}
Nello sviluppare circuiti per proprie CPU in grado di interpretare le istruzioni x86, Intel ha ideato moltissimi design diversi. Alcuni erano riservati per le nuove versioni della serie Pentium (ad esempio, Pentium 4), mentre altri venivano utilizzati nelle versioni “potenziate” dei processori Pentium (ad esempio, il “Pentium Pro”). Ad ogni modo, sin dal rilascio del primo Pentium, il modello della CPU e la microarchitettura non utilizzano più lo stesso nome. Un esempio pratico: l’80486 utilizza la microarchitettura 80486 (e solo quella), mentre la microarchitettura del primo Pentium si chiama “P5”.

La CPU dell’Xbox, così come tutti gli altri processori Pentium III, utilizza la **microarchitettura P6** (nota anche come “i686”). Si tratta di un’architettura di sesta generazione (la prima risale all’8086) caratterizzata da:
- Una *gigantesca* **pipeline a 14 stadi**: è possibile elaborare fino a 14 istruzioni in parallelo. Il rovescio della medaglia è che le istruzioni singole possono richiedere molti più cicli prima di essere elaborate. [Ecco una spiegazione scritta in precedenza]({{< ref "game-boy-advance#whats-new" >}}).
- **Esecuzione fuori ordine**: se possibile, la CPU riordina la sequenza di istruzioni per aumentare l’efficienza e le prestazioni.
- **Predizione speculativa**: una versione spinta della [predizione delle diramazioni]({{< ref "gamecube#features" >}}), in quanto la diramazione che la CPU prevede che sarà scelta viene eseguita.

Chiariti questi punti, è il caso di osservarli più attentamente. Si tratta di caratteristiche molto simili a quelle contenute nelle [console che abbiamo analizzato in precedenza]({{< ref "gamecube#features" >}}), per quanto le loro CPU siano molto diverse, in termini di design, dalle CPU Intel. Considerato sia il contesto storico che il design di x86, sarebbe stato molto difficile immaginare che Intel avrebbe mai potuto presentare, ad esempio, una CPU con pipeline. Eppure, ci sono riusciti; ecco come hanno fatto.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="CISC o RISC" >}}

{{% inner_markdown %}}
Tutte le console concorrenti analizzate in precedenza contengono una CPU **RISC**, mentre i processori x86 di Intel sono CPU **CISC**. Le CPU RISC sono note per il loro set di istruzioni semplificato rispetto a quello delle CPU CISC. Ad esempio, le CPU RISC non offrono istruzioni che consentono di manipolare i dati direttamente in memoria (obbligando quindi a passare dai registri).

Uno dei vantaggi dei processori RISC è che il loro approccio semplificato consente alle CPU di essere progettate in modo modulare, potenzialmente migliorando le prestazioni grazie a tecniche di elaborazione parallela. È proprio per questo che nelle CPU basate sulle architetture MIPS e PowerPC abbiamo visto il debutto di pipeline a stadi, design superscalari, esecuzione fuori ordine, predizione delle diramazioni e altro ancora. Al contrario, i processori CISC esistono da molto tempo prima dei processori RISC e sono stati sviluppati per venire incontro a necessità diverse. Di conseguenza, i loro design non sono flessibili come quelli dei processori RISC.

Torniamo alla nostra microarchitettura P6: il motivo di tanto interesse per il suo design è che anche se si tratta di CPU che comprendono solo un set di istruzioni CISC (x86), tali istruzioni vengono interpretate da un **microcodice** che viene eseguito dall’unità tramite micro operazioni di tipo RISC. In questo modo, Intel può sfruttare le ottimizzazioni tipiche dei processori RISC mantenendo la compatibilità con le istruzioni x86, grazie a un “livello CISC”.

Per quanto sia incorporato a livello hardware, il microcodice rimane programmabile: ciò permette a Intel di rilasciare correzioni per le proprie CPU anche dopo la produzione, ad esempio quando viene scoperto un bug o una vulnerabilità di sicurezza. Se avete letto gli articoli precedenti (ossia quelli sull’[N64]({{< ref "nintendo-64" >}}) o sulla [PS2]({{< ref "playstation-2" >}})), tenete presente che il microcodice di Intel **non è accessibile pubblicamente** (men che meno documentato) e Intel è la sola responsabile della sua gestione.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Il core" >}}
{{< figure_img float="true" src="cpu/core.png" alt="Il core" >}}
Il design di Coppermine
{{< /figure_img >}}

{{% inner_markdown %}}
Sono stati molti i chip a implementare la microarchitettura P6. Nello specifico, l'Xbox ne include un modello denominato **Coppermine**. Si tratta della seconda revisione del Pentium III, commercializzata in sostituzione del core "Katmai" e dotata delle specifiche seguenti:
- Cache **L1 da 32 KiB**: divisa tra 16 KiB per le istruzioni e 16 KiB per i dati.
- Cache **L2 da 128 KiB** integrata: una specifica un po' *strana*, dato che la versione commercializzata al pubblico di Coppermine è dotata di 256 KiB di cache L2. Tanto strana che Coppermine128 (il modello utilizzato per il marchio Intel "Celeron", l'alternativa a Pentium di fascia più bassa) aveva la stessa quantità di cache L2. È probabile che questa scelta sia stata dovuta a un tentativo di ridurre i costi di produzione e mantenere un prezzo di vendita competitivo.
- Un **front-side bus** da 133 MHz: si tratta del bus che collega la cache L2 al controller di memoria; ne parleremo ancora in seguito.
  - Intel lo chiama "front-side bus" per distinguerlo da un altro bus che collega la cache L2 (ossia la cache esterna) alla L1 (la cache interna). Quest'ultimo bus è denominato "back-side bus", una scelta terminologica piuttosto... "infelice" per il pubblico britannico.

Inoltre, Coppermine migliora l'implementazione originale della cache L2 aggiungendo la **Advanced Transfer Cache** e l'**Advanced System Buffering**: La cache L2 è all'interno del chip e la larghezza di banda dei bus è maggiore, per ridurre i colli di bottiglia del front-side bus.

Infine, il chip è connesso alla scheda madre tramite il socket "Micro-PGA2", anche se al contrario di quanto accade nelle altre console, la saldatura è realizzata con la tecnica "ball grid array" o BGA.
{{% /inner_markdown %}}

{{< /tab >}}

{{< /tabs >}}

### P6 e la fine dei Pentium numerati

È il momento di un'altra pillola di storia. Dopo l'era P6, i piani di Intel prevedevano come erede la microarchitettura "Netburst", effettivamente utilizzata nel Pentium IV. Dopo di ciò, la linea di successione si interrompe: la microarchitettura non aveva più margini di miglioramento. Un team Intel in Israele decise così di rivisitare P6 e sviluppare un successore più efficiente. Il risultato fu la famiglia di processori **Pentium M**, successivamente sviluppata fino alla microarchitettura (e al marchio) **Core**, che ha posto le basi per i design attuali.

### Architettura della scheda madre

A un certo punto della storia del PC, la complessità delle schede madri è arrivata a livelli tali da rendere necessario lo sviluppo ex-novo di design in grado di soddisfare in maniera efficiente i bisogni emergenti.

{{< float_group >}}
{{< figure_img float="true" src="cpu/motherboard.png" alt="Scheda madre" >}}
Diagramma della scheda madre dell’Xbox
{{< /figure_img >}}

{{% inner_markdown %}}
Il nuovo standard ruotava attorno a due chip impiegati nella gestione della maggior parte delle funzioni della scheda madre. Si tratta di:
- **Northbridge**: utilizzato come controller di memoria e interfacciato con la GPU.
- **Southbridge**: interfacciato con il resto del sistema I/O (ossia USB, ATA/SATA, PCI ecc.).

Questi chip prendono il nome collettivo di **chipset** e la loro importanza è tale da condizionare le funzionalità e le prestazioni di una scheda madre. Data la sua prossimità ai PC, anche la scheda madre dell’Xbox include due chip: **NV2A**, che combina northbridge e GPU, e **MCPX**, che gestisce il resto del sistema I/O.
{{% /inner_markdown %}}

{{< /float_group >}}

Ambedue i chip sono interconnessi mediante un bus dedicato, chiamato **HyperTransport**. Vale la pena sottolineare come anche alcune schede madri per PC impiegano la stessa tecnologia, anche se con una denominazione diversa (*nForce MCP-D*).

### Struttura della memoria

L’Xbox include un totale di **64 MiB di SDRAM DDR**, un tipo di RAM molto più veloce rispetto a quanto offerto dalla concorrenza. Tuttavia, si tratta di RAM condivisa tra tutti i componenti del sistema. Si parla pertanto, ancora una volta, di una **memoria ad accesso uniforme**, o di architettura “UMA” (dall’inglese “unified memory architecture”).

{{< float_group >}}
{{< figure_img float="true" src="cpu/memory.png" alt="Lo switching network" >}}
Una rappresentazione dello switching network.  
La GPU usa due banchi di RAM e la CPU ne usa un terzo, per ridurre il problema della contesa nell’accesso alla memoria
{{< /figure_img >}}

{{% inner_markdown %}}
Abbiamo già visto quanto, a volte, questo design possa creare [problemi]({{< ref "playstation-2#preventing-past-mishaps" >}}). I programmi possono provare a risolvere questi problemi distribuendo i dati in diversi banchi di memoria. A tale scopo, il chip NV2A utilizza uno **switching network** per consentire alle varie unità (CPU, GPU ecc.) di accedere contemporaneamente ai banchi di memoria.

Inoltre, la console dispone di un disco rigido interno che, *guarda caso,* contiene tre partizioni da 750 MiB ciascuna riservate all'archiviazione temporanea. In questo spazio la CPU può allocare alcuni dati provenienti dalla RAM principale allo scopo di alleggerirla, per poi ricaricarli quando vengono richiesti. Questo procedimento è manuale e non ha nulla a che vedere con la RAM virtuale.
{{% /inner_markdown %}}

{{< /float_group >}}

---

## La grafica

Come abbiamo visto, il processore grafico è all'interno del chip NV2A e, proprio come MCPX, anch'esso è prodotto da Nvidia.

{{< float_group >}}
{{< figure_img float="true" src="halo.png" alt="Halo" >}}
Halo (2001) in modalità 720p
{{< /figure_img >}}

{{% inner_markdown %}}
Nvidia è un'azienda da tempo nel settore dei processori grafici. I loro processori della serie *GeForce* sono tra le GPU più popolari sul mercato, in competizione diretta con la serie Radeon di Artx/ATI. Nel complesso, si tratta di una scelta che poneva solide basi per garantire una certa qualità grafica all'Xbox, che pur rimaneva il primo approccio di Microsoft nel mercato delle console.
{{% /inner_markdown %}}

{{< /float_group >}}

Anche se ora appare come una scelta scontata, *allora* si poteva dire la stessa cosa? Col senno di poi, considerata la storia recente, non è difficile capire perché Microsoft si sia rivolta a Nvidia piuttosto che ad altri marchi noti dell'epoca (3dfx, PowerVR, S3 ecc.); tuttavia, basta informarsi sul panorama del settore in quel periodo per rendersi conto di quanto complessa fosse la scelta.

Ad esempio, alla fine degli anni '90 la famosa serie "Voodoo 2" di 3dfx rappresentava circa il 70% del mercato PC, mentre Nvidia provava in tutti i modi a promuovere l'adozione della nuova "GeForce 256" (la prima scheda video della serie GeForce). In un contesto simile, la scelta di Microsoft suona più vicina all'azzardo che alla scommessa sicura. Invece, come sappiamo, il rischio fu ripagato in pieno.

> Nel 1999, Nvidia non era l'azienda leader del settore che è adesso. Erano nei guai. La nuova architettura GeForce era ancora giovane e a molti non piaceva. Ora sembra irrinunciabile, ma bisogna scavare negli archivi per scoprire perché e per quale motivo ho dovuto lottare per poterla includere nella console. Avevo ragione, ma non lo sapevo ancora. Ero molto preoccupato.
> 
> -- <cite>Seamus Blackley (co-creatore della prima Xbox)</cite>

Nel paragrafo seguente esamineremo il funzionamento del chip. Farò del mio meglio per evitare di restare impelagati nella terminologia e nel linguaggio del marketing, come nella sezione sulla CPU. Cominciamo dalle basi.

### Architettura e design

Il core della GPU presente nel chip NV2A si basa sulla famosa serie di GPU "GeForce3" e nella documentazione tecnica di Nvidia è noto anche col nome **NV20**.

{{< float_group >}}
{{< figure_img float="true" src="NV2A_Pipeline.png" alt="Design della pipeline di NV2A" >}}
Design della pipeline di NV2A
{{< /figure_img >}}

{{% inner_markdown %}}
Anche se la pipeline della GPU dell'Xbox si basa sull'architettura NV20, NV2A presenta alcune modifiche che lo rendono incompatibile con il resto della serie NV20 (ad esempio, è stato adattato per poter essere impiegato in un ambiente UMA).

Poiché nelle mie analisi ho scoperto molte caratteristiche che esulano dagli intenti di questo articolo, sentitevi liberi di consultare la bibliografia se qualcosa in questa sezione cattura la vostra attenzione. Inoltre, considerata la costante evoluzione terminologica di questo settore (che può causare una certa confusione), ho deciso di servirmi dei termini utilizzati da Microsoft e Nvidia nel periodo di commercializzazione dell'Xbox: se vi troverete a leggere altri articoli sull'architettura grafica da altre fonti, tenete presente questa nota.
{{% /inner_markdown %}}

{{< /float_group >}}

È arrivato il momento di scoprire come l'Xbox crea i fotogrammi. Alcuni passaggi sono molto simili a quelli relative al [Flipper]({{< ref "gamecube#graphics" >}}): se aveste difficoltà a seguire questo articolo, consultare quello relativo al GameCube potrebbe aiutarvi.

{{< tabs >}}
{{< tab name="Comandi" active="true" >}}
{{< figure_img float="true" src="pipeline/commands.png" alt="Schema di PFIFO" >}}
Fase dei comandi
{{< /figure_img >}}

{{% inner_markdown %}}
Prima di tutto, vediamo in che modo la GPU riceve comandi dalla CPU. La GPU contiene un gestore dei comandi chiamato **PFIFO**, che riceve ed elabora i comandi grafici (in un'area chiamata **pushbuffer**) in modalità FIFO; a questo punto, i comandi letti vengono inviati al motore **PGRAPH** (che si occupa dell'effettiva elaborazione grafica) e ad altri motori.

Come per Flipper, i comandi non devono integrare geometrie. Lo scambio dei dati tra PGRAPH e gli altri componenti della console può avvenire in diversi modi. Ad esempio, la CPU può allocare un buffer nella RAM contenente dati sui vertici, quindi ordinare alla GPU di recuperarli nella posizione dove sono memorizzati. Si tratta di un approccio potenzialmente efficiente, in quanto consente di evitare l'invio di geometrie duplicate.

I prossimi passaggi avvengono all'interno di PGRAPH.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Vertici" >}}
{{< figure_img float="true" src="pipeline/vertex.png" alt="Fase dei vertici" >}}
Fase dei vertici
{{< /figure_img >}}

{{% inner_markdown %}}
A questo punto, le cose si fanno particolarmente interessanti. In questo passaggio, la GPU permette di applicare trasformazioni ai vertici della geometria. Abbiamo già visto una funzionalità simile in Flipper, se non fosse che in questa GPU viene utilizzato un **motore programmabile**: gli sviluppatori potevano indicare quali operazioni sui vertici eseguire e quando, anziché essere costretti a seguire un programma predefinito. Ad ogni modo, se richiesto, NV2A può funzionare anche in modalità "fissa".

Questa fase è gestita da una **vertex unit**; NV2A ne contiene **due**. Ognuna di esse può caricare un programma contenente fino a 136 istruzioni (conosciuto anche come **microcodice**). Questo programma si chiama **vertex program** e viene caricato in runtime. Un vertex program può eseguire le operazioni seguenti:
- **operazioni aritmetiche** (addizione, moltiplicazione, minimo ecc.);
  - sono comprese alcune "funzioni helper" che supportano i calcoli grafici, ad esempio il prodotto scalare;
- **vertex swizzling** (ricombinazione e/o duplicazione).

In breve, la vertex unit elabora i vertici manipolandoli nei suoi registri. Ciò significa che una volta caricato il programma, 16 registri in sola lettura (chiamati "registri di input") vengono inizializzati con gli attributi di un vertice (ogni vettore contiene quattro componenti). Quindi, la vertex unit esegue il set di operazioni richiesto dal programma servendosi dei registri di input. I calcoli vengono supportati da 12 registri scrivibili e fino a 196 costanti. Infine, il vertice elaborato viene memorizzato in un altro blocco costituito da 11 registri scrivibili (ciascuno deputato a uno specifico scopo), che alimentano la fase successiva del processo. Il tutto si ripete per ogni vertice ricevuto.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Pixel" >}}
{{< figure_img float="true" src="pipeline/pixel.png" alt="Fase dei frammenti/pixel" >}}
Fase dei frammenti/pixel
{{< /figure_img >}}

{{% inner_markdown %}}
In questa fase, i vertici vengono trasformati in pixel. La procedura inizia a partire da un rasterizzatore, che genera pixel con cui disegnare ogni triangolo. Il rasterizzatore di NV2A è in grado di generare quattro pixel per ciclo.<!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while \[competitors embedded it\]()). -->È quindi il turno dei **4 texture shader**, che vengono utilizzati per recuperare le texture in memoria; inoltre, i texture shader possono applicare un filtro anisotropico, il mipmapping e lo **shadow buffering**. Quest'ultimo viene utilizzato per determinare se un pixel è visibile o se è coperto da una sorgente luminosa, in modo tale da applicare il colore corretto. A questo punto, la GPU può anche eseguire il clipping e un primo [Z-test]({{< ref "nintendo-64#modern-visible-surface-determination" >}}) (riducendo lo Z-buffer di quattro volte rispetto alle dimensioni originali per risparmiare banda, un aspetto fondamentale per migliorare le prestazioni).

I pixel risultanti vengono memorizzati in un **set di registri condivisi**, quindi ciclati in **8 combinatori di registri**, dove ogni combinatore applica delle operazioni aritmetiche. È possibile **programmare** questo processo utilizzando i **pixel shader** (un altro tipo di programma eseguito dalla GPU). A ciascun ciclo, ogni combinatore riceve i valori RGBA (RGB + canale alfa) contenuti nei set di registri. Quindi, in base alle operazioni definite dagli shader, applica ai valori le operazioni richieste e memorizza il risultato. Infine, una maggiore quantità di valori viene inviata al **combinatore finale**, l'unico a poter applicare colori speculari e/o nebbia.

I combinatori di registri sono programmabili, come accade per la [Texture Environment Unit]({{< ref "gamecube#tab-1-3-texture" >}}). Ciò significa che è possibile alterare i registri con combinazioni di impostazioni specifiche. Nel caso dell'Xbox, il gestore PFIFO legge i pushbuffer e imposta il PGRAPH, che include i combinatori di registri e i texture shader.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Post-processing" >}}
{{< figure_img float="true" src="pipeline/postprocessing.png" alt="Fase di post-processing" >}}
Fase di post-processing
{{< /figure_img >}}

{{% inner_markdown %}}
Prima che i pixel vengano scritti nel frame-buffer, NV2A si serve di quattro motori dedicati, chiamati **Raster Output Unit** o "ROP", per eseguire i test necessari (alfa, profondità e stencil) utilizzando i blocchi allocati allo scopo nella memoria principale. Infine, i pixel vengono scritti in blocchi (quattro pixel per ciclo) solo se i test vengono superati.

Per chiudere, occorre ricordare che è possibile applicare l'antialiasing al frame-buffer mediante una tecnica denominata **multisampling**. Essenzialmente, questa tecnica prevede che vengano eseguiti diversi campionamenti dei bordi dei poligoni, per poi calcolare una media dei campionamenti e creare l'immagine priva di aliasing. Si tratta di un approccio che sostituisce la funzione di antialiasing denominata "supersampling", più pesante dal punto di vista computazionale e in uso nelle GPU Nvidia precedenti.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### L'importanza della programmabilità

Credo che sia importante sottolineare quanto fosse importante questo nuovo modello di programmabilità fornito agli sviluppatori da Nvidia. In precedenza, buona parte della pipeline grafica veniva calcolata dalla CPU, mentre alla GPU spettava solo l'accelerazione delle operazioni di rasterizzazione. Con l'introduzione degli "shader" (sia pixel shader che vertex program), gli sviluppatori hanno potuto sfruttare le risorse della GPU per accelerare molti dei calcoli nella pipeline eseguiti precedentemente su CPU, liberando quest'ultima da un considerevole carico di lavoro.

L'idea alla base degli "shader" è stata introdotta per la prima volta dalla **Pixar** nel 1989, per estendere le funzionalità di **Renderman**, il loro pionieristico software per il rendering 3D. Erano tempi in cui la grafica 3D era principalmente appannaggio di apparecchiature industriali. Come abbiamo visto, anche se in seguito alcune console incorporarono [principi simili]({{< ref "nintendo-64#tab-1-1-reality-signal-processor" >}}), si dovette aspettare la gamma GeForce3 di Nvidia perché gli shader diventassero standard anche nel mercato consumer.

{{< float_group >}}

{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Vertex program" active="true" src="vertex" loop=true >}}
Animazione complessa calcolata in un vertex program
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Pixel shader" src="fragment" >}}
Vari effetti delle texture calcolati nei pixel shader
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}ChameleonMark (2002), una demo sviluppata da Nvidia per mostrare le potenzialità degli shader di GeForce3{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Grazie ai vertex program, ora la GPU può accelerare le trasformazioni dei modelli, i calcoli di illuminazione e la generazione di coordinate di texture. Quest'ultimo elemento è essenziale per comporre le [superfici di ordine superiore]({{< ref "playstation-2#infinite-worlds" >}}). In questo modo, la CPU può focalizzarsi su una migliore gestione della fisica, dell'intelligenza artificiale e delle scene.

Per quanto riguarda i pixel shader, gli sviluppatori possono manipolare le texture con varie tecniche (come multi-texturing, specular mapping, bump mapping, environment mapping ecc.) e ottenere così effetti diversi.

Grazie a questo nuovo approccio, in questo periodo emerge l'idea della **General Purpose GPU** o "GPGPU", che prevede l'assegnazione alla GPU di compiti tradizionalmente svolti solo su CPU. Il risultato è una GPU che non solo passa a occuparsi della maggior parte della pipeline grafica rispetto al passato, ma si comporta a tutti gli effetti come un co-processore, particolarmente efficiente nello svolgimento di calcoli specializzati (ad esempio, quelli relativi alla fisica). In futuro, questo nuovo approccio si svilupperà di pari passo con l'evoluzione delle GPU, sempre più potenti e flessibili. NV2A è il primo passo di questa nuova era, grazie alla combinazione tra le eccellenti capacità dell'hardware (vertex e pixel shader) e le API specializzate appositamente sviluppate (gli stati di OpenGL).
{{% /inner_markdown %}}

{{< /float_group >}}

Credo che gli shader saranno menzionati di frequente nei prossimi articoli. Per quanto riguarda questo articolo, è importante tenere presente che potrebbero essere considerati "grezzi" da alcune persone, che potrebbero spingersi ad affermare che i pixel shader qui descritti non siano neanche degli shader veri e propri (rispetto a quanto disponibile nelle GPU odierne).

### I frame dell'Xbox

Come da prassi per la sesta generazione, la risoluzione standard dei giochi Xbox è di **640x480** pixel. Certo, la GPU può comporre frame-buffer a risoluzioni fino a 4096x4096, ma l'hardware non sarebbe comunque in grado di garantire prestazioni accettabili. Di contro, la console permette di applicare le impostazioni dello schermo (ossia widescreen e "alta risoluzione") globalmente, anziché lasciare che siano gli sviluppatori a implementarle (come successe per il [GameCube/Wii]({{< ref "wii#tab-2-1-standardised-widescreen" >}})).

Il compito dell'encoder video è quello di trasmettere in un formato decodificabile dalla TV qualsiasi cosa sia nel frame-buffer. Ciò significa che le immagini widescreen vengono trasmesse in formato [anamorfico]({{< ref "wii#tab-2-1-standardised-widescreen" >}}), a meno che il gioco non produca immagini in HD (ossia in 720p o in 1080i, funzionalità offerta solo da una manciata di giochi).

Detto ciò: quanti tipi di segnali possono uscire dalla console? Un bel po'. Oltre al classico segnale composito PAL/NTSC, l'Xbox può produrre sia un segnale **YPbPr** (trasmesso tramite connettori "component", e che quindi richiede un cavo aggiuntivo) che un segnale RGB (compatibile con SCART e con VGA). Un'offerta tutto sommato ampia, che non richiede l'utilizzo di costosi adattatori o altri dispositivi.

---

## Audio

Il sottosistema audio dell'Xbox è stato fortemente influenzato dalle tecnologie impiegate nelle attrezzature audio professionali e sulle schede madri ATX; ciò non significa che non contenga alcune parti più interessanti. Il chip MCPX include due componenti audio: la **Audio Processing Unit** e la **Audio Controller Interface**.

La Audio Processing Unit, o "APU", è il processore audio dedicato, costituito a sua volta da tre sottocomponenti:
- il **Voice Processor** o "VP", un circuito specializzato deputato alla sintetizzazione di 256 voci a frequenza di campionamento di 48 KHz. Include due controlli di inviluppi DAHDSR e diversi filtri. Inoltre, 64 voci possono essere 3D (anziché stereo o mono). Come passaggio finale, il processore esegue la miscelazione e l'output delle voci tramite 32 canali, il cui volume è controllato a gruppi di 8 canali;
- il **Global Processor** o "GP", un DSP programmabile utilizzato per elaborare i dati audio provenienti dal VP e applicarvi diversi effetti.
  - Non tutti i canali provenienti dal VP vengono necessariamente inviati al GP: al contrario, è possibile che vengano inviati al VP solo alcuni gruppi ai quali devono essere applicati effetti specifici (come il riverbero);
- l'**Encode Processor** o EP, che (come suggerisce il nome) genera il segnale stereo finale a partire dai dati in uscita dal GP e lo invia alla RAM principale.
  - Le operazioni dell'EP possono essere svolte anche dal GP; ciò nonostante, avere un componente dedicato alla codifica permette alla console di utilizzare tecniche di encoding audio migliori, ad esempio il Dolby Digital.

L'APU elabora i dati audio, ma non può eseguirne l'output. Questo compito spetta all'ACI, che legge i dati audio nella RAM, li decodifica e li invia tramite la linea output audio come campioni PCM stereo (con risoluzione a 16 bit e frequenza di campionamento di 48 KHz).

---

## I/O

Come abbiamo visto prima, tutti gli accessi I/O sono concentrati nel sottosistema southbridge.

Il chip MCPX deriva dalla controparte per PC denominata **nVidia nForce Multimedia and Communications Processor**, in breve "MCP". Questa unità era utilizzata nelle schede madri che impiegavano il chipset nForce 220/415/420.

### Interfacce esterne

La console è dotata dei seguenti connettori esterni:
- **4 porte USB 1.1**, utilizzate per collegare i controller; la forma delle porte, tuttavia, è stata modificata per permettere di collegare solo controller Xbox.
  - Queste porte contengono anche un pin extra indicato come "Video Sync" e utilizzato nel collegamento di periferiche che interagiscono con quanto visualizzato a schermo;
- **una porta Ethernet 10/100BASE-TX**, utilizzata per i servizi online (di cui parleremo dopo). La funzionalità Ethernet si appoggia a un ricetrasmettitore (o "transceiver") dedicato, integrato nella scheda madre.

### Interfacce interne

MCPX offre anche le interfacce e i protocolli seguenti, utilizzati per il collegamento dei vari sottosistemi:
- **SMBus** (denominata anche I²C), un'interfaccia seriale che collega i componenti seguenti:
  - il **System Management Controller** o "SMC", che gestisce servizi di sistema come l'alimentazione e il controllo della temperatura e delle ventole. Si tratta di un microcontroller PIC16LC;
  - il **System Temperature Monitor** o "STM", un termometro digitale (ADM1032) di cui l'SMC si serve per rilevare un surriscaldamento;
  - una **EEPROM da 256 B**, una ROM riscrivibile dove sono memorizzati gli identificativi univoci della console (numero di serie, regione, indirizzo MAC Ethernet ecc.);
  - un **encoder video**, collegato principalmente alla GPU ma controllato tramite SMBus;
- **controller IDE**: si tratta di un protocollo standard largamente utilizzato nei computer per il collegamento di dischi rigidi, lettori ottici e altri dispositivi. Per collegare la scheda madre al lettore DVD e al disco rigido, viene impiegato un singolo cavo a nastro.
- **Low Pin Count** o "bus LPC": si tratta di un'altra interfaccia mutuata dal mondo PC, utilizzata non per il collegamento del *classico* BIOS, ma per comunicare con una ROM flash, dove è memorizzato un equivalente del BIOS. La capienza di questa memoria flash è di 1 MiB.

### Il controller

Il corposo controller di cui è dotata l'Xbox è noto in inglese col nomignolo **"The Duke"**. Complessivamente, i comandi non sono diversi da quelli dei controller utilizzati per le altre console, salvo che per l'utilizzo di una circuiteria analogica (a 8 bit) sui pulsanti frontali: ciò permetteva ai giochi di rilevare una pressione anche solo parziale della maggior parte dei pulsanti sul controller. Tuttavia, le critiche quasi unanimi al suo design originale portarono Microsoft a sostituirlo con una revisione, denominata **Controller S**, pochi mesi dopo la commercializzazione della console.

{{< side_by_side >}}
  {{< tabs nested="true" class="toleft" figure="true" >}}
    {{< tab_img name="Fronte" active="true" src="controller/duke_front.png" alt="Il lato frontale del controller Duke" >}}
    {{< tab_img name="Retro" src="controller/duke_back.png" alt="Il lato posteriore del controller Duke" >}}
    {{< figcaption >}}The Duke (2001){{< /figcaption >}}
  {{< /tabs >}}

  {{< tabs nested="true" class="toright" figure="true" >}}
    {{< tab_img name="Fronte" active="true" src="controller/s_front.png" alt="Il lato frontale della revisione S" >}}
    {{< tab_img name="Retro" src="controller/s_back.png" alt="Il lato posteriore della revisione S" >}}
    {{< figcaption >}}Il controller S (2002){{< /figcaption >}}
  {{< /tabs >}}
{{< /side_by_side >}}

Eppure, a ben vedere, entrambi i controller nascondono una particolarità: due porte **Memory Unit** dove collegare una scheda di memoria proprietaria, per consentire il trasferimento dei salvataggi tra più console. Inizialmente, credevo che questa caratteristica fosse stata ispirata da una [precedente console della concorrenza]({{< ref "dreamcast#interactive-memory-card" >}}). Pochi giorni dopo la pubblicazione di questo articolo, ho deciso di inviarlo a Seamus Blackley, il co-creatore della suddetta console, che mi ha risposto velocemente e in maniera illuminante. In merito a questa somiglianza con il Dreamcast, ha commentato così:

> La somiglianza col Dreamcast è una semplice distorsione causata dal senno di poi. Non è stata altro che una coincidenza.
> 
> -- <cite>Seamus Blackley</cite>

### L'adattatore necessario

Infine, è interessante notare come questi controller potevano essere collegati solo attraverso un **adattatore anti-inciampo** (in inglese, "breakaway dongle"), concepito per evitare spiacevoli incidenti nel caso in cui una persona fosse inciampata nel cavo.

> Credo che abbiano anche immaginato uno scenario in cui, se l'Xbox fosse stata posizionata in alto, uno strattone del controller avrebbe potuto far cadere l'intera console sulla testa dei bambini.
> 
> -- <cite>Un utente Xbox</cite>

---

## Il sistema operativo

Va bene, iniziamo dalle domande difficili.

> La console esegue **Windows**?

Purtroppo, credo che sia impossibile rispondere *precisamente* a questa domanda; la console monta una "versione" di Windows, ma non quella utilizzata nei normali PC destinati ai consumatori.

### La procedura di boot

Analogamente a quanto succede nelle macchine basate su Pentium, all'accensione del sistema la CPU inizia ad eseguire le istruzioni memorizzate nel **vettore di ripristino** (all'indirizzo `0xFFFF.FFF0`). Nell'Xbox, questo indirizzo punta a una ROM nascosta, contenuta nel chip MCPX (ne parleremo tra poco). MCPX contiene le routine che permettono di inizializzare il sistema di sicurezza e di proseguire con l'avvio utilizzando, invece, la ROM flash. Con le istruzioni contenute nella ROM flash, l'Xbox inizializza l'hardware, avvia un piccolo kernel (basato sul kernel di Windows NT) e visualizza su schermo l'animazione di avvio.

Durante l'inizializzazione sicura, la CPU entra in **modalità protetta**. Si tratta di un particolare cruciale, dato che le CPU con architettura x86 partono sempre in **modalità reale**, per mantenere la compatibilità con il primo processore (l'Intel 8086); tuttavia, quando i programmatori hanno bisogno di accedere alle funzionalità moderne della CPU (ad esempio, poter accedere a più di 1 MiB di memoria), devono abilitarle manualmente passando alla modalità protetta.

Il kernel, durante il suo avvio, inietta un microcodice nella CPU (non per programmarla, bensì per *aggiornarla*). Nell'ultimo passaggio, il kernel verifica la presenza di un DVD valido; se ne trova uno, lo esegue, altrimenti carica una shell interattiva per l'utente contenuta nel disco rigido.

### La schermata verde

Diamo un'occhiata al programma che viene caricato quando non è inserito nessun disco: la **dashboard**.

{{< tabs >}}
{{< tab name="Shell interattiva" active="true" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_img name="Home" active="true" src="shell/home.png" >}}
La schermata principale.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Impostazioni" src="shell/settings.png" >}}
Il menu delle impostazioni.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}La dashboard permette di accedere a vari servizi.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Le funzionalità offerte dalla dashboard non sono molto diverse da quelle del [menu Playstation]({{< ref "playstation-2#interactive-shell" >}}) o dell'[IPL del GameCube]({{< ref "gamecube#splash-and-shell" >}}). Vengono offerte tutte le funzioni che l'utente tipico si aspetterebbe di trovare, come la modifica di alcune impostazioni, la possibilità di spostare i file di salvataggio e la riproduzione di film su DVD o di CD audio.

Una funzionalità interessante della dashboard consiste nella capacità di copiare il contenuto di un CD audio e archiviarlo nel disco rigido della console. La musica memorizzata poteva quindi essere riprodotta all'interno di qualsiasi gioco, per "personalizzare" la colonna sonora. *Roba forte!*
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Aggiornamenti" >}}
{{% inner_markdown %}}
Ebbene sì: l'Xbox è la prima console della sua generazione a offrire *ufficialmente* la possibilità di ricevere aggiornamenti, per quanto sia possibile modificare solo i dati contenuti nel disco rigido. Il kernel (all'interno della ROM flash) non può essere sovrascritto, ma il sistema può patcharlo una volta caricato in memoria. Per questo, le patch del kernel vengono conservate nel disco rigido.

Gli aggiornamenti venivano distribuiti all'interno dei dischi di gioco, oppure potevano essere scaricati dal sistema quando era connesso al servizio Xbox Live (ne parliamo più avanti).

Alcuni aggiornamenti aggiungevano ulteriori funzionalità. Ad esempio, le prime unità della console non includevano Xbox Live: per installare il servizio, i file richiesti vennero inseriti sotto forma di aggiornamenti all'interno dei dischi di gioco. Per altri aggiornamenti, invece, lo scopo principale era la correzione delle vulnerabilità di sicurezza.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

---

## I giochi

Questo capitolo potrebbe causare qualche grattacapo, principalmente a causa della scarsa documentazione di base disponibile rispetto alle altre console. Sembra che Microsoft e Nvidia volessero spingere il più possibile gli sviluppatori a utilizzare le loro librerie, tralasciando tutto il resto. Una strategia... interessante.

Ad ogni modo, ho sfruttato la documentazione disponibile nel tentativo di fornire quante più informazioni possibili, senza tuttavia scendere troppo nel dettaglio.

### L'ecosistema di sviluppo

Lo sviluppo dei giochi per Xbox è particolarmente complesso sotto molti punti di vista, come librerie, terminologia e altro ancora. Per questo, ho deciso di separarlo per framework.

{{< tabs >}}

{{< tab name="Astrazione dall'hardware" active="true" >}}
{{< figure_img float="true" src="hal/general.png" alt="HAL" >}}
Rappresentazione dell'HAL
{{< /figure_img >}}

{{% inner_markdown %}}
Abbiamo notato come, per quanto elementi come il "coprocessore programmabile con microcodice" vengano inizialmente presentati in pompa magna, successivamente questi vengano sfruttati sempre meno una volta che gli sviluppatori si rendono conto della complessità del nuovo hardware.

Dal punto di vista degli sviluppatori, credo che uno dei vantaggi principali dell'Xbox fosse l'inclusione di librerie di alto livello molto utilizzate, in grado di gestire le funzionalità di basso livello con efficacia. La strategia di Microsoft e Nvidia consisteva quindi, anziché nel documentare dettagliatamente tutte le funzioni di basso livello dell'hardware, nel fornire una libreria di astrazione dall'hardware completa in grado di eseguire tutte le operazioni che gli sviluppatori si aspettavano senza che fosse richiesta una conoscenza completa del funzionamento interno dell'hardware.

Gli sviluppatori Xbox possono scegliere tra diversi SDK: alcuni "ufficiali" e più limitati, altri "ufficiosi" ma più flessibili. Diamo un'occhiata ai più conosciuti.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Microsoft XDK" >}}
{{< figure_img float="true" src="hal/xdk.png" alt="Fase dei vertici" >}}
La struttura di XDK
{{< /figure_img >}}

{{% inner_markdown %}}
L'Xbox Development Kit ("XDK") di Microsoft è l'SDK ufficiale dell'Xbox. Il pacchetto include molti strumenti, librerie e compilatori. È importante sottolineare come poteva essere integrato in Visual Studio .NET (versione 2002), che in quanto ad ambienti di sviluppo era *una bella bestiola*, sia in positivo che in negativo.

La libreria grafica era una versione modificata di **Direct3D 8.0**, ulteriormente espansa per includere le funzionalità specifiche dell'Xbox. DIrect3D era un set di API molto potente utilizzato per sviluppare codice compatibile con molte GPU diverse. Utilizzando questa libreria, gli sviluppatori potevano creare versioni Xbox dei loro giochi per PC senza dover effettuare troppe modifiche (in teoria); questo nonostante la sintassi utilizzata per i vertex program e per i pixel shader somigliasse fortemente al linguaggio assembly.

Le funzionalità audio erano fornite da **DirectSound**, che si occupa della Audio Processing Unit senza che gli sviluppatori dovessero occuparsi dello spostamento dei dati audio tra i vari componenti del sistema. Erano inoltre presenti alcune librerie di rete, molto utili per offrire le funzionalità online (ossia, il gioco in multiplayer). L'API di rete si basa su **Windows Sockets**, il tentativo con cui Microsoft ha provato a semplificare le comunicazioni di rete creando un protocollo esclusivo per Windows (per quanto il suo design si basi sulla sua controparte standardizzata, ossia socket BSD).

Per dare la possibilità di provare il sistema, Microsoft distribuì agli sviluppatori un kit di sviluppo hardware che consisteva in una normale console venduta al dettaglio, ma di colore verde, con il doppio della RAM rispetto alla versione per i consumatori (128 MiB in totale) e, a volte, un chip MCPX diverso (chiamato MCPX-2, che esegue il boot direttamente dalla ROM flash). Il kit di sviluppo includeva una versione potenziata della dashboard in grado di lanciare gli eseguibili firmati dall'XDK. Nei computer degli sviluppatori, **Xbox Neighbourhood** (un'applicazione per Windows) permetteva di collegare il kit di sviluppo a Visual Studio, per semplificare le fasi di distribuzione e debug. Per sperimentare con Xbox Live, Microsoft forniva anche server in modalità sandbox, ossia di prova.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="NXDK" >}}
{{< figure_img float="true" src="hal/nxdk.png" alt="NXDK" >}}
La struttura di NXDK.  
Anche se alcune librerie di basso livello sono incorporate in librerie di alto livello, sono comunque accessibili agli sviluppatori
{{< /figure_img >}}

{{% inner_markdown %}}
Per evitare che gli sviluppatori di software homebrew si trovassero impelagati in contenziosi legali relativi al copyright qualora avessero utilizzato l'SDK ufficiale, un gruppo di sviluppatori indipendenti da Microsoft creò un SDK alternativo chiamato **Open XDK**. Dopo alcuni anni, quando il suo sviluppo si interruppe, questo SDK indipendente venne "adottato" da un altro gruppo, che chiamò il nuovo fork **New XDK** o "nxdk".

Il progetto consisteva nell'ingegneria inversa dei componenti hardware di basso livello (il microcodice, i push-buffer ecc.) e nello sviluppo di nuove API in C/C++ che gestissero le chiamate di alto livello verso ogni componente del sistema.

Per semplificare il livello grafico senza utilizzare Direct3D, nxdk si affida al **compilatore CG**. CG è un linguaggio creato da Nvidia per lo sviluppo di shader. CG viene collegato ad altri compilatori per generare codice compatibile con l'Xbox. Durante la compilazione, il codice scritto in CG viene convertito in assembly compatibile con NV20, quindi questo viene ulteriormente tradotto utilizzando un compilatore creato ad hoc per generare microcodice e pushbuffer compatibili con NV2A. Inoltre, nxdk rendeva disponibili tutti gli altri compilatori utilizzabili per scrivere shader di basso livello, a vantaggio degli sviluppatori che non volevano utilizzare il linguaggio CG.

Le altre API disponibili gestiscono i servizi restanti (audio, rete ecc.). Nel complesso, questa libreria rende possibile un controllo più diretto sull'hardware rispetto all'SDK ufficiale, a patto di non utilizzare le API di Microsoft (che erano lo standard de-facto, per quanto totalmente proprietario). Ad ogni modo, nxdk rimane la scelta migliore per sviluppare software homebrew legali.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

### Il formato fisico

I giochi sono distribuiti su DVD a doppio strato (con capienza fino a 8,5 GB) e sono letti in un'unità DVD appositamente modificata che include protezioni antipirateria (nonostante utilizzi l'interfaccia ATA standard). È il caso di far notare come l'XDK includeva alcuni strumenti utili per modificare la disposizione dei dati sul disco: in questo modo, gli sviluppatori potevano migliorarne la velocità di lettura.

La console include anche un disco rigido interno di 8 GB, utilizzato per memorizzare i salvataggi o i contenuti temporanei. Il sistema contiene la dashboard, le impostazioni di Xbox Live e le impostazioni di rete.

### I servizi di rete

Niente più [modem]({{< ref "dreamcast#online-platform" >}}) né [servizi sperimentali]({{< ref "playstation-2#network-service" >}}). L'Xbox includeva tutto quello che oggi diamo per scontato quando si tratta di offrire un servizio online di base: la connettività Ethernet e un'infrastruttura online centralizzata (chiamata **Xbox Live**).

{{< float_group >}}
{{< figure_img float="true" src="shell/live.png" alt="Xbox Live" >}}
Il logo di Xbox Live
{{< /figure_img >}}

{{% inner_markdown %}}
Xbox Live non permetteva solo di giocare in multiplayer online, ma includeva diverse altre funzionalità come l'audio in streaming, per la chat vocale in tempo reale.

Ma in cosa consisteva precisamente Xbox Live? L'ecosistema comprendeva una serie di servizi online interconnessi di cui gli sviluppatori potevano usufruire per costruire la propria piattaforma online. Ad esempio: uno dei servizi fornisce le funzionalità legate ai profili utente; gli studi di sviluppo potevano quindi utilizzarlo come metodo di autenticazione per accedere alle funzionalità online dei propri giochi. Nell'SDK ufficiale, Microsoft includeva alcune API deputate allo scambio di dati con i server di Xbox Live.

È importante sottolineare che era Microsoft a concedere l'accesso all'ecosistema Xbox Live: gli sviluppatori dovevano infatti registrarsi per ottenere le chiavi di autenticazione da utilizzare nei giochi.
{{% /inner_markdown %}}

{{< /float_group >}}

L'esperienza online vera e propria si svolge nel **Title Server**, un tipo di server che risponde ai client (le console Xbox) sparsi nel mondo e gestisce le comunicazioni in tempo reale. Microsoft includeva nell'SDK alcuni esempi per mostrare come programmare questi server, anche se si basavano su Windows e dovevano essere messi in funzione in data center basati su Windows Server.

### Una nuova tendenza ha inizio

Abbiamo analizzato l'implementazione di Xbox Live da parte di Microsoft e osservato l'impatto che ha avuto sul settore. Col senno di poi sembra tutto piuttosto chiaro: è come se la ricetta di base per un buon gioco online (una connessione Ethernet e l'infrastruttura adeguata) fosse sempre esistita, mentre erano alcune aziende a non voler investire in tal senso.

Tuttavia, questa era solo una parte dell'intera questione: Microsoft doveva infatti convincere anche gli utenti, che dovevano percepire il multiplayer online non solo come una funzione opzionale, ma come una parte fondamentale di alcuni giochi, fino a sentirne un vero e proprio bisogno. Altrimenti, gli sforzi di Microsoft si sarebbero risolti solo nell'ennesimo tentativo di rendere il gioco online una realtà.

> Prova a immaginare un mondo in cui nessun giocatore su console vuole giocare online e nessuno crede che l'architettura PC possa costituire la base di una console. Questo era il mondo di quell'epoca. Per quanto ora queste cose ci sembrino scontate, allora non lo erano.
> 
> -- <cite>Seamus Blackley</cite>

---

## Lotta alla pirateria e homebrew

La console implementa molte misure di sicurezza, indipendentemente dal fatto che contenga componenti "off-the-shelf", ossia disponibili largamente sul mercato.

In questa sezione parleremo più volte di crittografia RSA; l'argomento è stato già introdotto [nell'articolo sul Wii]({{< ref "wii#tab-7-2-chain-of-trust" >}}), che potete consultare se non avete familiarità con RSA o con altri sistemi di crittografia simmetrica o asimmetrica.

Iniziamo.

### Protezione anticopia dei DVD

I dischi di gioco sono protetti a livello sia logico che fisico per impedire la duplicazione non autorizzata (un fatto che ormai non dovrebbe sorprendere).

A livello logico, i dischi contengono alcune "trappole" che ingannano i lettori DVD tradizionali, rendendo "invisibile" gli effettivi contenuti di gioco. Ad esempio: la prima area del disco è formattata come un normale DVD; al suo interno è presente un messaggio di avviso che viene riprodotto quando il disco viene inserito in un sistema diverso dall'Xbox. I dati di gioco si trovano in una seconda partizione; tuttavia, i metadati necessari per trovarla non sono codificati nel formato che un normale lettore DVD si aspetterebbe, quindi solo un lettore apposito (quello dell'Xbox) è in grado di trovare i dati sul disco.

A livello fisico, nel momento in cui scrivo non si conosce ancora quali dati vengono scambiati tra l'unità e il disco per eseguire la validazione. Sul disco è presente un anello interno inaccessibile ai lettori tradizionali che contiene alcuni identificatori univoci; tuttavia, il modo in cui questi dati vengono utilizzati non è noto.

### Protezione del sistema

Diamo un'occhiata al resto delle misure di sicurezza implementate in origine nell'Xbox.

{{< tabs >}}

{{< tab name="Introduzione" active="true" >}}
{{% inner_markdown %}}
La ROM flash e la EEPROM, che contengono rispettivamente il BIOS e i dati sensibili, sono crittografate con una **chiave RC-4**. Una volta avviato il kernel, questo lancerà solo eseguibili firmati da Microsoft con **crittografia RSA**.

Il disco rigido è formattato in un filesystem completamente proprietario e senza alcuna documentazione, chiamato **FATX**.

Questa è, in breve, la catena di fiducia implementata da Microsoft. Sembra semplice, no? Però **c'è qualcosa di strano**: l'esecuzione è controllata dalla CPU, che però è un chip "off-the-shelf"... quindi come può interpretare i dati cifrati con RC-4? Facile: **non può**; questo significa che da qualche parte nella console è presente un codice non crittografato che definisce la prima fase della crittografia. È proprio su questo codice che si sono concentrate le attenzioni della maggior parte degli hacker e dei loro tentativi di violare la console dopo il lancio.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Alla ricerca del bootstrap" >}}

{{% inner_markdown %}}
Secondo la documentazione della stessa Intel, i loro processori vengono avviati all'indirizzo `0xFFFF.FFF0`; gli hacker hanno quindi cercato questo codice nella ROM flash (in questo modo, sarebbero arrivati alla chiave RC-4). È su questo obiettivo che si è concentrato il noto hacker e ricercatore **Andrew "bunnie" Huang** nel corso delle sue ricerche, dalle quali è emerso un fatto molto interessante: i primi 512 byte della ROM flash contengono sia routine di sicurezza che la stessa chiave... ma questo codice non funziona nel sistema venduto ai consumatori. La mia ipotesi è che Microsoft abbia lasciato nella ROM pezzi di codice provenienti da prototipi e unità di debug (forse accidentalmente, dato che svelano gli algoritmi utilizzati da Microsoft). Ad ogni modo, si trattava di *codice di scarto*, il che permise a bunnie di capire che il vero `0xFFFF.FFF0` non era nella ROM flash.

Per farla breve: `0xFFFF.FFF0` era **nascosto nel chip MCPX**, che conteneva una ROM da 512 B che veniva nascosta subito dopo la prima esecuzione. Non è stato facile estrarre i dati contenuti in questa ROM: bunnie dovette intercettare i dati scambiati nel bus HyperTransport per estrapolare la chiave RC-4 durante la trasmissione.

Quando bunnie pubblicò la chiave come frutto delle sue ricerche, Microsoft non fu per nulla contenta. La comunità hacker aveva quindi trovato il modo di controllare il primo livello di sicurezza della console, kernel incluso.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Uno sblocco permanente" >}}

{{% inner_markdown %}}
Violare la crittografia RSA non avrebbe mai dovuto essere una procedura semplice; tuttavia, ottenere l'accesso al kernel avrebbe implicato la possibilità di fare ingegneria inversa sul codice e sviluppare patch in grado di scavalcare completamente la protezione RSA. Il che è esattamente quello che successe, permettendo alla comunità homebrew di iniziare a sviluppare software. Ora chiunque poteva creare programmi eseguibili su un'Xbox modificata senza l'approvazione di Microsoft. Alcuni gruppi svilupparono versioni sostitutive e potenziate della dashboard originale, in grado addirittura di eseguire Linux.

Tuttavia, per usufruire dei software homebrew gli utenti dovevano modificare il BIOS delle loro console utilizzando apparecchiature speciali, sicuramente non alla portata di tutti. Negli anni successivi, vennero scoperte nuove vulnerabilità compatibili con l'esecuzione delle istruzioni di hacking; una di queste consisteva nell'utilizzare una versione appositamente modificata del file di salvataggio di "007: Agent Under Fire" o "Splinter Cell", in grado di generare un buffer overflow sfruttabile per eseguire uno strumento homebrew che installava un exploit permanente nel disco rigido. Questo "sblocco permanente" era reso possibile da un altro buffer overflow di cui poteva essere vittima la dashboard originale e innescato da un file di font appositamente alterato (questo tipo di file, infatti, non aveva bisogno di essere firmato).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="I modchip" >}}

{{% inner_markdown %}}
A beneficio dei pirati apparvero, immancabilmente, i **modchip**: anziché attaccare il lettore DVD, questi scavalcavano la ROM flash sfruttando il fatto che la ROM nel chip MCPX poteva avviare un BIOS secondario qualora ne avesse trovato uno su una porta LPC non utilizzata.

Il BIOS modificato conteneva routine patchate che rendevano possibile leggere qualsiasi disco di gioco (specialmente i normali DVD disponibili in commercio).
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="La controffensiva" >}}

{{% inner_markdown %}}
Microsoft rilasciò molte revisioni hardware, allo scopo di ridurre la superficie elettronica d'attacco e minimizzare i costi. Nel frattempo, rilasciò diversi aggiornamenti software nel tentativo di ridurre il numero di vulnerabilità nel kernel e nella dashboard. Tuttavia, la permanenza di alcune vulnerabilità nei giochi diede il via alla solita rincorsa tra inseguiti e inseguitori.

È il caso di far notare come anche Xbox Live fu un efficace dissuasore dalla pirateria. Microsoft era in grado di bloccare l'accesso a Xbox Live nelle console che contenevano modifiche non autorizzate: gli utenti dovevano quindi tenere in considerazione anche questo fattore prima di modificare le loro console al solo scopo di giocare con copie piratate.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## È tutto!

Dopo alcuni mesi passati tra scadenze ed esami, sono finalmente riuscito a terminare un articolo. Ammetto che anche in questo prosegue la tendenza ad aggiungere *troppe* informazioni e curiosità; ma per quanto le prime fasi di ricerca siano state difficoltose (e frustranti), sono estremamente grato all'inimitabile comunità di XboxDev per tutto il supporto fornito e le informazioni che mi hanno aiutato a trovare.

Se volete saperne di più su questa console, XboxDev sta ancora lavorando su nxdk (e su altri emulatori), con l'ambizione di rendere possibile ciò che era considerato finora impossibile nel settore homebrew su Xbox; per maggiori informazioni, date un'occhiata al loro progetto.

Dal canto mio, mi prenderò alcuni giorni per riflettere sul *prossimo articolo*, col quale chissà, forse torneremo indietro nel tempo di alcune generazioni per analizzare una console di cui mi ero dimenticato.

Alla prossima!  
Rodrigo