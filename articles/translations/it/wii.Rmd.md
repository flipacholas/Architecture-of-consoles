---
long_title: Architettura della Wii
short_title: Architettura Wii
name: Wii
subtitle: Tecniche d'innovazione uniche
date: 2020-01-05
release_date: 2006-11-19
generation: 7
published: true
cover: wii
javascript:
  - 'threejs'
seo_image_pos: "Top"
top_tabs:
  Model:
    file: international
    caption: "La Wii <br>Rilasciats il 19/11/2006 negli Stati Uniti, il 02/12/2006 in Giappone e l'08/12/2006 in Europa."
  Motherboard:
    caption: "Mostrando la revisione 'RVL-CPU-40', le revisioni precedenti hanno avuto un processo di fabbricazione significativamente più grande e successivamente quelli hanno rimosso la maggior parte degli I/O del Gamecube.<br>NAND Flash è montato sul retro."
    bib_source: photography-okqubit
  Diagram:
    caption: I data bus più importanti sono indicati con la loro larghezza e velocità.
#Historical
aliases:
  - /projects/consoles/wii/
---

## Breve introduzione

Nonostante non potesse vantare una grafica all'ultimo grido come i suoi concorrenti, il Wii aveva dalla sua parte un sistema di controllo totalmente nuovo e software innovativi.

In questo articolo analizzeremo ogni aspetto della console: dal suo hardware, già noto, al trascurato sistema di sicurezza con i suoi principali difetti.

Nota: Alcuni capitoli si sovrappongono a parti dell'articolo precedente sul [GameCube](gamecube), quindi invece di ripetere le stesse informazioni inserirò un collegamento alla specifica parte dell'articolo.

## {.supporting-imagery}

## Una nuova generazione di controller

Iniziamo da uno degli elementi più iconici di questa console: i suoi controller.

Il dispositivo principale è l'ormai classico **telecomando Wii** (noto in inglese come "Wii Remote" o "Wiimote", come lo chiameremo da qui in avanti), un oggetto dalla forma simile a quella di un normale telecomando per TV che includeva diversi input:

- Innanzitutto, un **set di pulsanti fisici** utilizzati alla stregua di quelli presenti in un normale controller.
- Un **accelerometro** per rilevare le variazioni dell'orientamento (il componente principale del sistema di rilevamento del movimento).
- Infine, una **telecamera a infrarossi** che, in combinazione con l'accelerometro e con alcuni calcoli eseguiti sul Wii, poteva essere utilizzata per puntare lo schermo.
  - Questa telecamera richiedeva l'uso della **barra sensore** (inclusa con la console). La barra conteneva due gruppi di LED infrarossi visibili dalla telecamera; il Wii poteva quindi triangolare i dati per calcolare la posizione del telecomando Wii rispetto alla TV.

Il telecomando funziona grazie a un chip **Broadcom BCM2042**, un chip che include tutti i componenti necessari affinché possa essere utilizzato come dispositivo Bluetooth indipendente (CPU, RAM, ROM e, ovviamente, un modulo Bluetooth). Benché il Wiimote sia programmato per essere identificato come dispositivo di input secondo il protocollo "Bluetooth HID", i dati vengono scambiati tramite un metodo non standard (probabilmente per impedire che possa essere utilizzato su sistemi diversi dal Wii).

Infine, nel Wiimote è presente una **EEPROM da 16 KB**, per memorizzare i dati dell'utente, e un **piccolo altoparlante** in grado di riprodurre solo suoni a bassa qualità (ADPCM 3 kHz a 4 bit o PCM 1,5 kHz a 8 bit).

### Espansioni

Nintendo includeva nella confezione un altro controller da impugnare nella mano opposta, il **Nunchuk**, costituito da un suo accelerometro, una levetta analogica e due pulsanti. Il Nunchuck si collega al Wiimote tramite una porta proprietaria a 6 pin.

![Nunchuk e Wiimote [@photography-amos], rispettivamente.](controllers/both.jpg)

Sono stati prodotti altri accessori in grado di collegarsi a questa porta, ciascuno con input di diverso tipo.

## La CPU

Dopo il successo di Gekko, IBM presumibilmente ha afferrato questo design e l'ha ri-marchiato come '750CL' affinché potesse essere utilizzato da altri produttori [@cpu-ibmcl]. Quando Nintendo chiese una nuova CPU da utilizzare per la futura console, nota ancora con il nome in codice "Revolution" (da cui il prefisso RVL presente sulle schede madri), IBM si accordò con l'azienda giapponese per utilizzare un 750CL con una velocità di clock pari a una volta e mezzo quella di Gekko. Questa CPU, nota come **Broadway** [@cpu-ibmuser], opera a una frequenza di **729 MHz**.

Dopo aver riesaminato [Gekko](gamecube#cpu), credo che le novità nella nuova CPU siano piuttosto limitate. Tuttavia, questa familiarità poteva rappresentare un vantaggio: gli sviluppatori GameCube potevano iniziare a sviluppare nuovi giochi per Wii fin da subito, grazie all'esperienza accumulata con Gekko. Inoltre, l'aumento della velocità di clock di Broadway gli permetteva di aggiungere funzionalità ai software e incrementarne la qualità.

### E la memoria?

Contrariamente a quanto ci si aspetterebbe considerando la somiglianza della CPU, la vecchia struttura di memoria del GameCube è stata riprogettata e migliorata con le modifiche seguenti:

- Splash (24 MB di 1T-SRAM) ora risiede all'interno del SoC Hollywood (spiegato più avanti) e ora si chiama **MEM1** [@operating_system-memory].
- La ARAM (una SDRAM seriale da 16 MB) è **assente**, ma...
- C'è un nuovo chip di memoria, **MEM2**, che include **64 MB di SDRAM GDDR3** per scopi generici.
  - Questo tipo di memoria si basa sul normale sistema DDR2, migliorandolo grazie a larghezze di banda maggiori (circa il doppio rispetto alle velocità di trasferimento originali) e un consumo energetico ridotto, ideale per le GPU.

### Retrocompatibilità

Arrivati a questo punto, possiamo dire che questa console è una versione potenziata del GameCube, tanto che il Wii eredita la compatibilità con i giochi della generazione precedente. Per rendere il Wii *pienamente* retrocompatibile, si è reso necessario includere nel sistema tutte le vecchie porte esterne: quelle dei controller del GameCube e quelle delle schede memoria. Ecco però la prima limitazione: la nuova mappa della memoria non è compatibile con quella vecchia. Per questo motivo, è stato implementato nel software un semplice strato di "emulazione" (ulteriori informazioni nei capitoli "I/O" e "Sistema operativo").

Gli accessori del GameCube che utilizzavano la porta seriale e la porta ad alta velocità non possono essere utilizzati con il Wii, data l'assenza di tali porte.

Le revisioni hardware degli anni seguenti hanno purtroppo visto la rimozione delle porte del GameCube.

## La grafica

Analogamente al GameCube (dove il componente grafico, le interfacce I/O e le capacità audio erano combinate in un unico pacchetto chiamato 'Flipper'), il Wii segue lo stesso schema e ospita un grande chip accanto a Broadway chiamato **Hollywood**. Al suo interno troviamo il sottosistema grafico che, a dire il vero, è identico a quello di Flipper con minime correzioni.

Pertanto, la GPU di Hollywood esegue ancora gli stessi compiti che la controparte di [Flipper](gamecube#graphics) eseguiva a suo tempo, ma ora gode di una velocità di clock **1,5 volte** superiore (**243 MHz**). Questo aumento della velocità permette di elaborare un numero maggiore di geometrie ed effetti a parità di unità di tempo.

### Funzionalità

Poiché il motore 3D è ancora quello di [Flipper](gamecube#graphics), invece di ripetere la panoramica della pipeline, menzionerò alcune interessanti modifiche al design che i giochi hanno dovuto subire:

#### Widescreen standardizzato {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Modalità 4:3.](mario/4_3.png){.active title="Tradizionale"}

![Modalità 16:9 prodotta dall'encoder video.](mario/16_9.png){title="Ampio"}

![Modalità 16:9 visualizzata su una TV widescreen.](mario/16_9_stretched.png){title="Esteso"}

Super Mario Galaxy (2007).

:::

I giochi per GameCube non disponevano di un vero e proprio supporto agli schermi in 16:9, una novità rispetto al classico formato 4:3. Ciononostante, la GPU di Flipper era già in grado di farlo e una manciata di giochi prevedevano opzioni per attivarla, sebbene fosse ancora considerata una funzionalità esclusiva.

Comunque sia, il framebuffer rimane identico e il codificatore video produce ancora un fotogramma conforme a PAL o NTSC, quindi questa funzione 'widescreen' viene invece eseguita **allargando il campo visivo** nella matrice di proiezione. Il risultato è una scena renderizzata con un angolo di visuale più ampio che appare ora schiacciata orizzontalmente. Tuttavia, anche la TV widescreen gioca un ruolo in questo processo, poiché successivamente estenderà il fotogramma 4:3 (proveniente dalla console) e l'immagine visualizzata apparirà così *più o meno* nel rapporto corretto. Se siete curiosi, questa tecnica non è nuova; viene utilizzata nella proiezione dei film ed è chiamata **widescreen anamorfico**. Inoltre, è interessante notare come gli sviluppatori del SNES dovessero affrontare l'[effetto opposto](super-nintendo#display-modalities).

Tornando al Wii, la console ha standardizzato questa funzionalità permettendo di attivare una modalità "panoramica" dalle impostazioni del sistema, usata anche per migliorare i _panorami_ in molti giochi (battuta pessima, lo ammetto).

#### Interazione con lo schermo {.tab}

![Super Mario Galaxy (2007).<br>Il giocatore può raccogliere le astroschegge puntandole con il Wiimote.](mario/mario_galaxy.png) {.tab-float}

L'innovativo sistema di controllo rendeva possibili nuovi tipi di interazioni all'interno dei giochi per Wii. Dal momento che il WiiMote consentiva agli utenti di puntare lo schermo, alcuni giochi come *Super Mario Galaxy* o *The Legend of Zelda: Twilight Princess* sfruttavano questa funzionalità per far interagire il giocatore con lo scenario.

Nel rapporto intitolato _Debugging Myths: Is the Wii More Demanding to Emulate than the GameCube?_ [@graphics-dolphin], gli sviluppatori dell'emulatore Dolphin spiegano che giochi come Super Mario Galaxy e altri sparatutto in prima persona si affidano allo [z-buffer integrato](gamecube#tab-3-4-render) per identificare l'oggetto verso cui punta il Wiimote e/o controllare quanto dista l'oggetto dal cursore del Wiimote.

Non si tratta di una funzionalità nuova in assoluto, bensì di un nuovo modo di utilizzare funzionalità già esistenti. I giochi per GameCube non usavano un controller multiuso con una funzione di puntamento integrata. Ora, invece, i giocatori avrebbero potuto controllare il proprio personaggio e puntare lo schermo contemporaneamente.

### Design aggiornati {.tabs-close}

Grazie ai megahertz aggiuntivi di Broadway e Hollywood e a design all'avanguardia, nei giochi per GameCube è stato possibile migliorare i modelli dei personaggi. Anche se non si è trattato di un salto generazionale pari a quello dei cicli di console precedenti, il miglioramento è stato comunque sensibile e apprezzato.

![Super Smash Bros. Melee (2001) per GC.<br>4718 triangoli.](mario_melee_gc){.toleft model3d="true"}

![Super Smash Bros Brawl (2008) per Wii.<br>5455 triangoli.](mario_brawl_wii){.toright model3d="true"}

![Sonic DX (2003) per GameCube.<br>1985 triangoli.](tails_dx_gc){.toleft model3d="true"}

![Super Smash Bros. Brawl (2008) per Wii.<br>3644 triangoli.](tails_brawl_wii){.toright model3d="true"}

### Segnale video

Sorpresa: il Wii non usa più l'ormai classica porta Multi Out del [Super Nintendo](super-nintendo.md#a-convenient-video-out), ma una sua variante denominata **AV Multi Out** (poca fantasia da quelle parti...), con una forma leggermente diversa. Questo connettore supporta tutti i segnali usati in precedenza, con l'aggiunta dello spazio colori **YPbPr** (conosciuto anche come 'component') [@graphics-video]. Inoltre, nella porta sono presenti alcune linee dati utilizzate dal sistema per identificare il tipo di cavo inserito.

Purtroppo, questo design eredita le stesse [limitazioni](gamecube.md#connections) che caratterizzavano il GameCube. Niente S-Video sui sistemi PAL, così come niente RGB sui sistemi NTSC. Inoltre, l'RGB può trasmettere solo segnali interlacciati, quindi niente segnali progressivi.

## Audio

Il Wii include lo stesso **DSP Macronix** presente nel [GameCube](gamecube#audio), quindi potete fare riferimento a quell'articolo per un'analisi dettagliata.

Rispetto al GameCube, l'unico cambiamento principale è rappresentato dal fatto che è possibile utilizzare MEM1 o MEM2 come buffer audio, data la scomparsa della memoria ARAM.

## I/O

Il sottosistema I/O del Wii è una vera rivoluzione (*per restare in tema con il suo nome in codice*). Le interfacce, infatti, sono ora controllate da un solo modulo deputato anche alla gestione della sicurezza della console. Si tratta del modulo **Starlet**.

### Il coprocessore nascosto

Starlet non è altro che una CPU **ARM926EJ-S** collegata alla maggior parte dei componenti interni della console. Localizzata all'interno di Hollywood, opera a **243 MHz** (come Hollywood) e dispone di RAM e ROM proprie. È quindi possibile considerare Starlet come un computer indipendente in esecuzione parallela rispetto alla CPU principale.

![Schema principale dell'architettura del Wii. Da notare come Starlet sia in grado di controllare la maggior parte degli I/O, e persino nasconderne alcuni da Broadway.](diagram.png)

La CPU è simile a quella utilizzata nel [Nintendo DS](nintendo-ds), salvo per due aggiunte particolari:

- La “J” nel nome indica l’inclusione di **Jazelle**, un’unità dedicata che esegue Java Bytecode a 8 bit. Anche se i programmi Java continuano a dipendere dalla macchina virtuale (nota con la sigla “JVM”), alcuni opcode possono essere eseguiti direttamente dalla CPU. Nel complesso, era così possibile velocizzare l’esecuzione del codice Java compilato.
- Una **unità di gestione della memoria** (Memory Management Unit, MMU) dedicata, per permettere di gestire la memoria virtuale. Utile nei sistemi operativi generici.

Come detto, si tratta miglioramenti “particolari” in quanto non sono mai stati sfruttati dal Wii. Nonostante ciò, Nintendo ha comunque scelto di utilizzare questa CPU per Starlet. Anche il primo iPhone (2G) includeva una CPU ARM con Jazelle, e anche in questo caso la funzionalità è rimasta inutilizzata.

Alla fine, la tecnologia Jazelle non è mai decollata. Dopo alcune iterazioni si è appurato che, molto semplicemente, Java Bytecode girava meglio su software. In seguito, ARM ha rimpiazzato Jazelle con "Thumb-2EE"; nel momento in cui scrivo (giugno 2021), sia Jazelle che Thumb-2EE sono stati abbandonati.

::: {.subfigures .distributed .open-float .float max_subfigures=1}

![](console/front.jpeg)

![](console/back.jpeg)

I/O esterno sulla Wii.<br>Lo slot anteriore piccolo e scuro è un lettore di schede SD.

:::

A questa “CPU I/O” è assegnato il compito di gestire l’accesso tra i vari dispositivi I/O e Broadway; nel fare ciò, si occupa anche della sicurezza della console, decidendo se consentire o negare i vari accessi. Si tratta di un compito particolarmente importante nei casi in cui coinvolge l’accesso, ad esempio, alla NAND, che contiene i dati del sistema operativo principale e dell’utente.

Il chip utilizza alcune tecnologie ARM come l’**Advanced Microcontroller Bus Architecture** (AMBA), un protocollo che semplifica la comunicazione tra dispositivi attraverso un set di bus specializzati.

{.close-float}

Detto questo, Nintendo ha collegato l' I/O in un modo che fa uso di due AMBA bus:

- Il bus **AHB** (AMBA High-performance Bus, AHB Bus): come suggerisce il nome, è progettato per le comunicazioni ad alta velocità. Qui troviamo:
  - L’interfaccia **NAND**: accede a una NAND flash da 512 MB dove sono memorizzati il sistema operativo e i dati dell’utente.
  - Due interfacce **Secure Digital Input Output** (SDIO): SDIO è un protocollo progettato principalmente per l’accesso alle schede SD, anche se in questo caso la seconda delle due interfacce viene utilizzata per controllare il modulo Wi-Fi (802.11 b/g).
  - Un controller **USB 2.0**: gestisce le due prese USB utilizzabili dall'utente e una scheda figlia Bluetooth 2.0.
  - Un modulo **SHA-1** e **AES**: delegato ai compiti di sicurezza (maggiori informazioni nel capitolo “Antipirateria”).
- Il bus **APB** (Advanced Peripheral Bus): limitato ai componenti a basse prestazioni, che includono:
  - L’**interfaccia disco**: collegata al lettore dei dischi.
  - L’**interfaccia seriale**: collegata ai controller del GameCube.
  - L’**interfaccia esterna** (External Interface, EXI): la conosciamo [già](gamecube#internal-io). Si occupa delle comunicazioni con il resto degli hardware del GameCube ed è utilizzata per la retrocompatibilità.

### Come viene mantenuta la retrocompatibilità

![Wii che utilizza l'attrezzatura del GameCube [@photography-amos].](gamecube-mode.png) {.open-float.no-borders}

Nonostante il sistema I/O sia stato modificato radicalmente, il Wii è pienamente retrocompatibile con i giochi per GameCube. Starlet viene infatti riprogrammato quando si esegue un gioco per GameCube, rimappando virtualmente i dispositivi di I/O secondo la stessa configurazione del GameCube originale.

Inoltre, nel chip real-time clock è presente della ROM aggiuntiva che contiene i font bitmap (sia per l’alfabeto latino che per il giapponese) utilizzati nei giochi per GameCube, e una SRAM per salvare le impostazioni [relative all’IPL](gamecube#operating-system).

{.close-float}

## Il sistema operativo

In generale, il Wii contiene **due sistemi operativi**. Uno viene eseguito su Broadway (la CPU principale), mentre l'altro viene eseguito su Starlet (la CPU di I/O). Entrambi sono memorizzati nei 512 MB di memoria NAND e possono essere aggiornati.

### Il SO di Starlet

Come se Starlet non fosse già interessante sul lato hardware, lo è anche (e soprattutto) per quanto riguarda il software. Il SO di Starlet, infatti, non solo ha un accesso completo a ogni singolo componente della console, ma è il primo codice a essere eseguito quando viene premuto il pulsante di accensione.

Starlet contiene un SO noto *in maniera non ufficiale* come **Input/Output Operating System**, o “IOS” (da non confondere con iOS di Apple) [@operating_system-ios]. IOS è un sistema operativo praticamente completo, che include:

- Un **microkernel**: controlla la CPU ARM9, esegue **i processi** e dialoga con il resto dell’hardware tramite i **driver**.
- I **driver**: permettono di dialogare con l’hardware esterno alla CPU (I/O).
- I **processi**: si occupano di un’attività, ad esempio la gestione della rete o l’implementazione del file system.
- Un **core crittografico**: accelera le operazioni di crittografia (**solo AES e SHA-1**).

Con tutto questo a sua disposizione, **il compito principale di IOS è quello di alleggerire il carico di lavoro sulla CPU principale** occupandosi dell’I/O e della sicurezza. Gli sviluppatori, quindi, non dovevano preoccuparsi di questi aspetti. Per assolvere il suo compito, Starlet **si riserva tra 12 e 16 MB** di RAM GDDR3, mentre il resto viene utilizzato da Broadway e da GPU.

Broadway e Starlet dialogano tra loro utilizzando un protocollo **Inter-Process Communication**, o “IPC”: in parole povere, le due CPU condividono due registri ciascuna. Una CPU può scrivere nei registri dell’altra (con dati che possono rappresentare un comando o un valore) e, come risposta, la CPU ricevente può eseguire una funzione.

Il sistema di aggiornamento di IOS **è un po’ complesso**: le versioni aggiornate di IOS non sono installate sovrascrivendo le precedenti, ma vengono memorizzate in un altro slot (l’area della NAND riservata a IOS è infatti divisa in “slot”). Ciò avviene solamente **per motivi di compatibilità**: in questo modo, i software per Wii sviluppati per una versione di IOS precedente possono continuare a utilizzare la versione di IOS per cui sono stati sviluppati.

Nintendo rilasciava spesso aggiornamenti di IOS per migliorare il supporto hardware (ciò si rendeva necessario quando veniva messo in commercio un nuovo accessorio). **Solo in un caso** gli aggiornamenti di IOS sostituivano le versioni precedenti: quando in una determinata versione veniva scoperta una vulnerabilità sfruttabile. Le motivazioni, quindi, erano puramente legate alla sicurezza.

Quando viene inserito un gioco per GameCube, Starlet avvia invece un **MIOS**. Si tratta di una variante di IOS che chiede a Starlet di emulare l’[IPL](gamecube#operating-system) originale.

### Il SO di Broadway

Questo sistema operativo è conosciuto più comunemente come **menu Wii** e viene eseguito sulla CPU PowerPC principale (**Broadway**).

![Il menu Wii con svariati canali installati.](system/home.png){.tabs-nested .active title="Home"}

![Il menu utilizzato per modificare le impostazioni.](system/settings.png){.tab-nested title="Impostazioni"}

![La bacheca contiene le lettere raggruppate per data.](system/mail.png){.tabs-nested-last title="Bacheca messaggi"}

Se confrontato con IOS, non definirei “completo” questo SO: si tratta più di un programma che consente all’utente di eseguire le operazioni seguenti:

- **Avviare il gioco per Wii/GameCube**, solo se è stato inserito un disco valido.
- **Modificare le impostazioni della console**, come ora e data, modalità video, posizione della barra sensore e altre ancora.
- **Eseguire applicazioni**: una delle novità di questa console è rappresentata dalla possibilità di installare giochi per Wii di piccole dimensioni (chiamati "WiiWare"), videogiochi del passato (i giochi della "Virtual Console") o semplici applicazioni per uso pratico (come un browser internet). Queste applicazioni sono note ufficialmente come **canali**, anche se a volte all'interno del SO vengono chiamate **titoli**.
  - Gli utenti possono scaricare/acquistare i canali attraverso un canale preinstallato chiamato **Canale Wii Shop**.
  - I titoli della Virtual Console includono un emulatore utilizzato per eseguire il gioco stesso. Una caratteristica interessante dei titoli Virtual Console è che gli emulatori non sono condivisi né con il sistema in generale, né con gli altri giochi Virtual Console appartenenti alla stessa piattaforma. In questo modo, ogni gioco può girare su un emulatore appositamente ottimizzato.
- **Ricevere/inviare messaggi**: i Wii dispongono di un codice identificativo univoco (impresso nel chip SEEPROM) che può essere condiviso per consentire lo scambio di messaggi con altri Wii. I messaggi possono essere visualizzati nella **Bacheca messaggi**.
  - Sia Nintendo stessa che i giochi per Wii utilizzavano questa funzionalità anche per inviare alle console una newsletter.

Come per IOS, anche il menu Wii è stato aggiornato numerose volte. Alcuni aggiornamenti correggevano falle di sicurezza, mentre altri aggiungevano nuove funzioni. Tra queste, una delle più importanti è stata la possibilità di memorizzare i canali nelle schede SD.

Ogni programma in esecuzione su Broadway (compreso il menu Wii) richiede una versione specifica di IOS per funzionare. Quando viene avviato un gioco o un canale, Starlet si riavvia utilizzando la versione dichiarata di IOS richiesta.

### I pacchetti di aggiornamento

Nel riferirsi agli aggiornamenti, Nintendo utilizza la denominazione **Aggiornamenti di sistema**. Ogni pacchetto di aggiornamento contiene entrambi i sistemi operativi ed è identificato nel sistema di versionamento mediante numeri cardinali. L'ultima versione conosciuta è la `4.3E`, rilasciata nel giugno 2010.

I pacchetti di aggiornamento del sistema possono essere scaricati dai server di Nintendo o installati dai dischi di gioco. Gli utenti possono verificare manualmente la disponibilità di aggiornamenti tramite il menu Wii. Se un gioco richiede una versione specifica di IOS non installata nella console, qualora il disco di gioco contenga i pacchetti necessari l'aggiornamento viene forzato.

### Sequenza di boot

Finora abbiamo esaminato i due sistemi operativi, caratteristici per diversità, presenti nella console ed eseguiti simultaneamente. Ma le cose non sono semplici come potrebbero sembrare: infatti, i due SO devono essere coordinati con particolare attenzione durante la sequenza di avvio della console per poter funzionare correttamente in seguito.

La sequenza di boot della console è quindi la seguente [@operating_system-boot]:

1. L'utente preme il tasto ON sulla console.
2. Fase **Boot0**: Starlet esegue un programma a livello hardware contenuto nella Mask ROM integrata (da 1,5 KB).
    - In breve: Starlet decritta e verifica l'integrità dei primi 96 KB della NAND, quindi ne calcola l'hash e lo confronta con un hash salvato nella memoria OTP integrata in Starlet stesso. Se i due hash non coincidono, la console entra in un loop infinito.
3. Fase **Boot1**: Starlet esegue un piccolo programma contenuto nei 96 KB di NAND appena decrittati.
    - Il programma chiede a Starlet di inizializzare (pulire) la RAM GDDR3 da 64 MB, per poi decrittare e verificare il resto della NAND.
4. Fase **Boot2**: Starlet carica l'IOS iniziale (richiesto per eseguire il menu Wii), quindi avvia Broadway.
5. Broadway avvia il menu Wii. Da questo momento, l'utente prende il controllo.

## Giochi

Anche se la grafica dei nuovi giochi non era propriamente rivoluzionaria, gli utenti potevano comunque godere di nuove funzionalità in grado di sorprenderli. Merito dei nuovi dispositivi e servizi resi disponibili da Nintendo sin dal lancio della console: dai nuovi controller, fino a un'infrastruttura online standardizzata (WiiConnect24) che permetteva di giocare su internet *gratis*.

### Il formato fisico

I giochi per Wii sono distribuiti su un disco di formato proprietario, il **Wii Optical Disc** ("disco ottico Wii", un nome molto descrittivo). Si tratta di un formato progettato da Matsushita/Panasonic a partire dai normali DVD, con l'aggiunta di alcune peculiarità non standard come una **burst cutting area** lungo il bordo interno del disco, per impedire le copie non autorizzate.

![I giochi standard sono distribuiti fisicamente su un disco all'interno di una custodia.](box_case.jpeg){.tabs-nested .open-float .tab-float .active title="Fisico"}

![I giochi più piccoli (WiiWare) e i titoli emulati (Virtual Console) possono essere acquistati e scaricati nel Canale Wii Shop.](system/store.png){.tabs-nested-last title="Digitale"}

I dischi di gioco per Wii possono contenere **4,7 GB** (nel formato a strato singolo) o **8,54 GB** (nel formato a doppio strato). Spesso contengono **due partizioni**: una per gli aggiornamenti del sistema, l'altra per l'effettivo software di gioco.

Alcuni dischi di gioco, come nel caso di *Super Smash Bros. Brawl*, contenevano più partizioni con all'interno diversi giochi Virtual Console, eseguibili all'interno del gioco principale.

{.close-float}

### Lo sviluppo sulla console

Come sempre, Nintendo inviava agli sviluppatori un kit di sviluppo. Quello del Wii si chiamava **NDEV** [@games-ndev] e si presentava con le fattezze di un grosso _mattone_ nero. NDEV offriva ulteriori opzioni di I/O e una dimensione della MEM2 raddoppiata (128 MB in totale), per scopi di debug.

La suite di software ufficiale si chiamava **Revolution SDK** e includeva vari strumenti utili durante lo sviluppo, come compilatori, debugger e framework (scritti prevalentemente in **C/C++**). Nintendo ha distribuito gli aggiornamenti tramite il sito web **Warioworld.com** (che ora reindirizza verso un altro sito di Nintendo), accessibile solo dagli sviluppatori verificati.

L'SDK ufficiale si basa su chiamate ad IOS per interagire con l'hardware di Wii; è per questo che gli aggiornamenti di IOS vanno spesso a braccetto con gli aggiornamenti dell'SDK.

### Il menu HOME

Considerati tutti i progressi di questa console sul lato software, può sorprendere il fatto che i giochi **vengono comunque eseguiti "bare metal"**, ossia che hanno il controllo completo delle risorse hardware di Broadway (ma non di Starlet, motivo per cui è proprio questa CPU a occuparsi delle misure di sicurezza). Naturalmente, il codice di gioco deve comunque essere approvato da Nintendo prima che possa essere distribuito.

![Il menu Home visualizzato in partita.](ingame/home.png){.tabs-nested .open-float .tab-float .active title="Menu"}

![Anche questa schermata doveva essere inclusa.](ingame/strap_warning.png){.tabs-nested-last title="Promemoria"}

Detto questo, alcune funzionalità che compaiono in molti giochi diversi sono stranamente simili. Ad esempio: ricordate il famoso **menu HOME**? Premendo il pulsante "HOME" sul Wiimote, una schermata sovrimpressa sul gioco permette all'utente di tornare al menu Wii senza dover riavviare la console. Ma dato che il SO non offre una simile funzionalità, com'è possibile che tutti i giochi offrivano la stessa interfaccia grafica?

La risposta è semplice: Nintendo includeva nell'SDK alcune **librerie obbligatorie** che dovevano essere incluse nei giochi. *E guarda un po'*, una di queste si occupa di creare proprio quella schermata. È lo stesso motivo per cui solo le applicazioni homebrew utilizzano design originali per il menu HOME.

{.close-float}

Il menu HOME ufficiale è uno dei circa 200 elementi che i giochi dovevano includere, come indicato nel documento **Wii Programming Guidelines** (le linee guida per la programmazione sulla console, fornite con l'SDK ufficiale). Tra gli elementi da includere vi era la schermata con il promemoria riguardante il laccetto da indossare (una semplice immagine BMP) visualizzata all'avvio del gioco, con cui l'utente poteva interagire solo nei modi consentiti da Nintendo.

### La personalizzazione nei giochi

![Il Canale Mii permetteva di personalizzare il proprio "Mii"...](system/mii-editor.png){.tabs-nested .open-float .tab-float .active title="Editor"}

![...che sarebbe poi comparso nei vari giochi.<br>Wii Music (2008).](ingame/music.png){.tabs-nested-last title="Gioco"}

Un'altra caratteristica da enfatizzare sono i **Mii**, degli avatar che gli utenti potevano creare nell'apposito *Canale Mii*.

Ma non finisce qui: i giochi potevano utilizzare i Mii creati per personalizzare l'esperienza di gioco.

{.close-float}

## Lotta alla pirateria e homebrew

La moltitudine di funzionalità offerte hanno reso il Wii molto attraente per la scena dell'hacking. La violazione del sistema di sicurezza avrebbe permesso agli sviluppatori homebrew di sfruttare tutte le potenzialità della console senza dover sottostare al controllo di Nintendo. Effettivamente, il Wii finì per offrire una ricchissima libreria di software homebrew.

### Protezione anticopia

Iniziamo dai soliti sospetti: **l'unità disco**.

Come abbiamo visto, i dischi per Wii presentavano una "burst cutting area" inaccessibile dai lettori normali. In mancanza di questa zona, il disco non veniva letto.

![La console non si schioda da questa schermata finché non viene inserito un disco valido.](system/disc.png) {.open-float}

Gli sviluppatori di modchip scoprirono che l'unità disco conteneva un'interfaccia di debug chiamata "Serial Writer" [@anti_piracy-disc], una porta accessibile solo inserendo una *chiave segreta*. Era solo una questione di tempo prima che la chiave fosse scoperta. I modder poterono quindi disabilitare la protezione anticopia e, successivamente, sviluppare un modchip in grado di automatizzare la procedura.

Matsushita rilasciò ulteriori revisioni dell'unità disco in cui l'interfaccia di debug era offuscata; tuttavia, altre falle permisero di abilitare nuovamente quest'interfaccia.

{.close-float}

Vale la pena sottolineare come lo scopo principale del modchip era la pura pirateria; poiché il contenuto del disco rimaneva comunque criptato, furono necessarie ulteriori ricerche prima di poter eseguire codice personalizzato.

Al contrario, gli homebrew per GameCube potevano essere eseguiti fin da subito grazie agli [exploit precedenti](gamecube#tab-5-3-honourable-mention) scoperti sulla vecchia console.

### La crittografia del sistema

Quello della crittografia è probabilmente l’aspetto più complesso della console e, contemporaneamente, ciò che ha messo in risalto l’abilità di molti sviluppatori, tra un’incessante attività di ricerca e lo sviluppo di programmi sorprendenti.

La sicurezza interna del Wii ruotava attorno a pochi algoritmi crittografici (AES, RSA, ECC, SHA-1 e HMAC). Esaminiamo ciascun gruppo separatamente, per non complicare la spiegazione:

#### Crittografia condivisa {.tabs.active}

Per proteggere il sistema dalle manipolazioni, le comunicazioni tra più componenti (NAND, disco di gioco e scheda SD) sono crittografate. La scelta di Nintendo è ricaduta su un sistema a **chiavi simmetriche** [@anti_piracy-security]: ciò significa che il Wii usa la stessa chiave sia per criptare che per decriptare i dati.

Nella memoria OTP di Starlet sono conservate tre chiavi AES a 128 bit [@anti_piracy-keys], scritte in hardware durante la fabbricazione:

- **Chiave comune**: una chiave globale generata da Nintendo e contenuta in tutti i Wii, utilizzata per decriptare il primo livello crittografico dei canali e dei giochi su disco (d’ora in avanti chiamati **titoli** per comodità).
- **Chiave SD**: la chiave utilizzata per criptare/decriptare i dati trasferiti sulla scheda SD; solo il menu Wii può trasferire tali dati.
  - Per motivi ancora incerti, Nintendo ha inserito una copia di questa chiave all’interno di IOS.
- **Chiave NAND**: una chiave generata casualmente durante la fabbricazione della console (quindi univoca per ogni Wii), utilizzata per proteggere il chip NAND.

Starlet è la sola CPU ad avere accesso ai dati riservati, ed è pertanto lei a occuparsi di criptare e decriptare i contenuti sensibili.

#### Catena di fiducia {.tab}

I titoli contengono un ulteriore livello di sicurezza: RSA-2048. Si tratta di un **algoritmo asimmetrico**: sono necessarie una chiave per criptare un dato e una seconda chiave per decriptarlo. In questo modo, Nintendo è in grado di criptare i titoli utilizzando una chiave conosciuta solo dall'azienda stessa (ossia una "chiave privata"), mentre il Wii li decripta utilizzando una "chiave pubblica" memorizzata nella console. Anche se un hacker fosse entrato in possesso della chiave pubblica, questa non sarebbe bastata per violare il sistema di sicurezza, in quanto i dati dovevano comunque essere criptati utilizzando la chiave privata nota solo a Nintendo.

Inoltre, RSA non è utilizzato solo per criptare i dati, ma anche per controllare l'integrità della crittografia stessa. Nintendo utilizza più chiavi al solo scopo di firmare (ossia criptare) i dati già crittografati, generando quindi una catena crittografica per assicurarsi che:

- ogni singola chiave utilizzata sia stata autorizzata da Nintendo;
- i dati non siano stati alterati, quindi criptati nuovamente senza autorizzazione.

Il sistema funziona in questo modo:

1. Nintendo crea una chiave denominata `x`.
2. Nintendo programma Starlet in modo tale che la CPU si fidi solo dei contenuti firmati con la chiave `x`.
3. Se Starlet si ritrova a dover decriptare un titolo con una chiave `y`, procederà solo se `y` è stato firmato con la chiave `x`.

Questo meccanismo prende il nome di **catena di fiducia**. Oltre al Wii, è spesso utilizzato per proteggere gran parte delle telecomunicazioni in tutto il mondo (ad esempio: per verificare l'autenticità dei certificati sconosciuti, i browser che usano l'HTTPS sfruttano i "certificati radice").

#### Catena di Starlet {.tab}

Le chiavi pubbliche sono conservate nella memoria OTP di Starlet (che quindi, per quello che interessa a noi, può solo decriptare e verificare le firme dei dati). La catena di fiducia è composta dalle chiavi seguenti [@anti_piracy-certificate]:

- **Chiave radice**: firma la chiave CA.
  - Questa chiave (pubblica) è l'unico elemento che serve a Starlet; il resto può essere decriptato (e viene quindi considerato attendibile) se è stato firmato con questa chiave.
- La **chiave dell'autorità di certificazione** (CA): firma le chiavi XS e CP.
- La **chiave XS**: firma i "ticket", un tipo di dati che contiene un elenco di chiavi AES di cui il sistema ha bisogno per decriptare i titoli (denominate "chiavi dei titoli").
- La **chiave CP**: una volta decriptato un titolo con la relativa chiave del titolo, la chiave CP firma i metadati del titolo (i cosiddetti "TMD").
  - I metadati includono un hash SHA-1 (con cui non vengono firmati ulteriori dati) utilizzato da Starlet per verificare l'integrità del dati a cui si riferisce.

Le implicazioni sono evidenti: in questo modo, **Nintendo è l'unico distributore di contenuti autorizzato**. Un ottimo risultato per tutte le case di sviluppo preoccupate dalla pirateria.

#### Altre chiavi {.tab}

Nel sistema è presente anche un paio di **chiavi ECC pubbliche e private**. La crittografia ellittica (in inglese "Elliptic Curve Cryptography", ECC) è un algoritmo simile all'RSA. Queste chiavi vengono utilizzate solo per firmare i dati in entrata e in uscita dalla scheda SD. In questo modo, i dati copiati nella scheda SD su un Wii non possono essere utilizzati su un secondo Wii.

La chiave ECC è firmata con *l'ennesima* chiave pubblica RSA, denominata **MS**: in questo modo, Starlet considera affidabile la chiave ECC.

L'ultima chiave utilizzata dal Wii è la **chiave HMAC**, che impiega un altro algoritmo che combina gli hash SHA-1 con HMAC. Durante la sequenza di boot, Starlet verifica che la NAND non sia stata alterata da hardware di terze parti. Per farlo, calcola l'hash SHA-1 della NAND e verifica che corrisponda a un hash memorizzato a livello hardware. Oltre a ciò, l'hash salvato viene firmato utilizzando la chiave HMAC per renderne certa l'autenticità.

Per finire: la chiave HMAC è memorizzata nella SEEPROM (al di fuori di Starlet), non nella memoria OTP.

#### Osservazioni {.tabs-close}

Dopo questa disamina, vale la pena sottolineare che quando il sistema esegue un gioco per GameCube, **nessuno dei metodi crittografici appena descritti viene utilizzato**. Starlet si limita a verificare che il gioco possa accedere solo alla memoria allocata ad esso. Questo perché 1/4 della RAM GDDR3 viene utilizzato per simulare la vecchia [ARAM](gamecube#audio).

### Come la crittografia è stata sconfitta

Iniziamo con le chiavi AES. Benché l'algoritmo fosse difficile da violare, se si fosse riusciti a estrarre le chiavi (soprattutto la chiave comune), lo strato di sicurezza corrispondente sarebbe subito venuto meno. La prima sfida, quindi, fu quella di **trovare un modo per estrarre le chiavi**.

![Il diagramma della sicurezza di Starlet.](encryption.png) {.open-float}

Un gruppo di hacker noto come **Team Twiizers** (un gioco di parole con “tweezer”, la parola inglese che indica le normali pinzette) si rese conto che l’assenza di firme nella modalità GameCube poteva costituire una buona superficie di attacco [@cpu-ccc]. Il team scoprì non solo che **tre-quarti della RAM GDDR3 non venivano ripuliti** dopo l’esecuzione di un gioco per GameCube, ma anche che creando un ponte tra alcuni punti sulla scheda madre (utilizzando, guarda caso, proprio un paio di pinzette) era possibile invertire i banchi della RAM GDDR3 scelti, permettendo di accedere ad aree riservate della RAM. Sorpresa: in queste aree erano presenti anche le chiavi AES.

{.close-float}

Meglio non correre troppo: in questo modo era possibile decriptare il "primo strato" del sistema di sicurezza, ma per eseguire programmi non firmati (homebrew) sarebbe stato necessario violare anche l'algoritmo RSA. Un'impresa potenzialmente impossibile, dal punto di vista della potenza di calcolo richiesta... a meno che l'algoritmo non fosse stato implementato male. Il Team Twiizers proseguì con le ricerche, iniziando quindi ad analizzare in che modo IOS fosse scritto e concentrandosi sulle funzioni di verifica delle firme.

Senza scendere troppo nel dettaglio, la verifica delle firme RSA funziona confrontando l'hash dell'operazione RSA eseguita con la firma decriptata. Dopo qualche smanettamento, il gruppo scoprì qualcosa di ridicolo nella sua banalità: **Nintendo aveva implementato questa funzione utilizzando `strncmp`** (in C, la funzione di confronto tra due stringhe).

Una spiegazione per chi non conosce il linguaggio C: `strncmp` è una routine utilizzata per verificare se due stringhe sono uguali. Questa funzione si serve di tre parametri: due stringhe e un intero, con quest'ultimo che indica il numero di caratteri da confrontare. Quando vengono inseriti, `strncmp` inizia a confrontare ogni carattere fino ad arrivare alla fine di una delle due stringhe (o quando il contatore di caratteri è raggiunto). In C, le stringhe sono una serie di caratteri terminati da un carattere `\0`: ciò significa che una volta raggiunto `\0`, `strncmp` smette di confrontare i caratteri. Quindi, scrivendo un titolo per Wii in modo tale da far sì che il suo hash contenga `\0` sin dall'inizio, i calcoli RSA di Starlet finiranno per confrontare hash molto brevi (o addirittura vuoti) con notevoli possibilità di collisione (dati diversi che producono lo stesso valore di hash). In ultima analisi, utilizzando una quantità fattibile di attacchi brute-force, questo consentiva al confronto di restituire `equal`... **Titolo è firmato!**

Come se non bastasse, questa falla era presente non solo in diverse versioni di IOS, ma persino nelle routine delle fasi boot1 e boot2!

### L'alba degli homebrew

A questo punto, mancava solo un tassello cruciale: rendere l'exploit permanente sviluppando uno strumento facile da usare per l'utente finale, per poter eseguire programmi custom su Wii senza grattacapi.

![Inizialmente era possibile eseguire applicazioni di terze parti servendosi di un file di salvataggio modificato.](system/twilight_hack.png) {.open-float}

Gli exploit esaminati finora richiedevano l'uso di hardware aggiuntivo e non erano quindi alla portata di chiunque. Sarebbe stato ancora una volta il Team Twiizers a sbrogliare la matassa, scoprendo *l'ennesimo* exploit: un buffer overflow in gioco.

L'exploit fu scoperto nel famoso **The Legend of Zelda: Twilight Princess** (sviluppato dalla stessa Nintendo). Il team scoprì che all'interno del file di salvataggio del gioco era possibile modificare il numero dei caratteri utilizzati per il nome del cavallo del giocatore in modo tale da causare un overflow. Quando il gioco avrebbe provato a leggere questo nome, l'overflow avrebbe innescato una reazione a catena che sarebbe finita con la possibilità di eseguire codice arbitrario, che poteva quindi essere scritto in modo tale da far partire, ad esempio, un caricatore di programmi.

{.close-float}

Poiché ormai si potevano creare firme contraffatte, il file di salvataggio modificato venne reso disponibile per chiunque tramite la rete. Ora la comunità homebrew poteva eseguire i propri software custom.

### La ricerca di un exploit permanente

Durante l'analisi di IOS, si scoprì che le firme venivano controllate solo durante l'installazione dei titoli, e non durante la loro esecuzione.

::: {.subfigures .open-float .tabs-nested .tab-float}

![](system/homebrew_channel.png){.tab-nested .active title="Splash"}

![](system/homebrew/menu.png){.tab-nested title="Interno"}

Il canale Homebrew _non ufficiale_ (2008).<br>Probabilmente l'hack più user-friendly di tutti i tempi.

:::

Il Team Twiizers ne combinò un'altra delle sue. Con molta perizia, crearono un canale installabile in grado di caricare programmi arbitrari memorizzati nella scheda SD. Se il canale fosse stato installato prima della risoluzione dei problemi di sicurezza da parte di Nintendo, sarebbe quindi stato possibile eseguire permanentemente titoli homebrew sul Wii a prescindere dalla correzione delle falle di sicurezza nei processi di firma da parte di Nintendo (cosa che effettivamente avvenne).

Il risultato di questo sforzo fu il **canale Homebrew**, un titolo che consentiva a chiunque di avviare programmi homebrew che sfruttavano il controllo completo del sistema (con tutte le implicazioni del caso).

{.close-float}

### La risposta di Nintendo

Naturalmente, Nintendo rilasciò molti aggiornamenti di sistema per correggere gli exploit relativi alle firme presenti su diverse versioni di IOS, mentre la sequenza di boot fu modificata nelle revisioni successive dell'hardware.

![Sì, erano piuttosto frequenti.](system/updates.png) {.open-float}

Tuttavia, il sistema continuava a presentare dei difetti di base:

- Broadway poteva riavviare Starlet facendogli eseguire qualsiasi versione di IOS senza permessi aggiuntivi: un comportamento che permetteva di sfruttare le vulnerabilità in versioni senza patch.
- Rimaneva possibile utilizzare le API nascoste di IOS senza ulteriori privilegi, consentendo un grado di controllo non autorizzato dell'hardware ancora maggiore.
- Era possibile inviare comandi affinché l'unità disco leggesse normali DVD, e alcune versioni di IOS contenevano chiamate nascoste che servivano proprio a mandare questo tipo di comandi. Il che, per Nintendo, era una possibilità particolarmente preoccupante per quanto riguardava la pirateria.

{.close-float}

Alla fine, possiamo riassumere il resto della storia di questa console come un gioco a inseguimento. Ogni volta che venivano trovate nuove vulnerabilità, Nintendo si affrettava a rilasciare patch per provare a correggere i problemi riscontrati. Questa dinamica continuò fino alla fine del ciclo di vita della console, quando non vennero più rilasciati aggiornamenti di sistema. Alla fine, quindi, hanno vinto gli inseguiti.

Al momento della stesura di questo articolo, anche se le vulnerabilità qui descritte sono state già corrette, queste sono state sostituite da exploit ancora sfruttabili.

Il Wii rimarrà nella storia per i notevoli trascorsi della scena hacker sulla console, che includono una quantità immensa di titoli homebrew sviluppati appositamente per il sistema (grazie anche al "negozio" Homebrew, che non solo conteneva più titoli gratuiti rispetto al Canale Wii Shop, ma che poteva vantare addirittura una migliore velocità).

## È tutto!

Buon 2020!

Questo articolo ha richiesto un bel po' di tempo. Credevo, forse ingenuamente, che dal momento che molti dei contenuti erano già stati scritti per il GameCube, mi sarei limitato ad aggiungere qualche piccola sezione, un collegamento ogni tanto...

È venuto fuori un articolo molto più dettagliato di quanto avessi potuto immaginare. Spero che sia stato interessante.

Alla prossima!  
Rodrigo
