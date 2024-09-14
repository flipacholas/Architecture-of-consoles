---
short_title: Architecture de la Game Boy / Color
long_title: Architecture de la Game Boy / Color
name: Game Boy / Color
subtitle: Simple et portable
date: 2019-02-21
release_date: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Models:
    - 
      title: "Original"
      caption: "La Game Boy originale.<br>Sortie le 21/04/1989 au Japon, le 31/07/1989 en Amérique et le 28/09/1990 en Europe."
      img_class: reduced-width
      file: international
      active: true
    - 
      title: "Couleur"
      caption: "Le successeur de nouvelle génération appelé Game Boy Color.<br>Sortie le 21/10/1998 au Japon, le 18/11/1998 en Amérique du Nord, et le 23/11/1998 en Europe."
      img_class: reduced-width
      file: color
  Motherboard:
    caption: "Image montrant la révision \"04\". Veuillez noter que \"DMG\" est l'identifiant du modèle original de la Game Boy."
  Diagram:
    caption: "Basé sur la Game Boy originale."
#Historical
aliases:
  - "/projects/consoles/game-boy/"
  - "game-boy-color"
---

## Une brève introduction

La série Game Boy peut être imaginée comme une version portable de la NES avec une puissance limitée, mais vous verrez qu'elle intégrait de nouvelles fonctionnalités très intéressantes.

### L'analyse arc-en-ciel

L'immense popularité de cette console a entraîné une variété de révisions (c'est-à-dire Game Boy Pocket, Light, et même sous forme de cartouches [Super Nintendo](super-nintendo)). En fait, la marque Game Boy couvre deux générations. Au sein de la 4ème génération, nous trouvons la Game Boy monochrome et ses révisions, et dans la génération suivante réside la Game Boy Color (livrée après la disparition de la [Virtual Boy](virtual-boy)). La bonne nouvelle est que cet article couvre les deux extrémités. Ainsi, à la fin, vous aurez une bonne compréhension de comment fonctionne la Game Boy et de comment sa technologie a ensuite évolué pour devenir la Game Boy Color.

## {.supporting-imagery}

## CPU

Au lieu de placer de nombreux puces standard sur la carte mère, Nintendo a opté pour une **conception monopuce** pour loger (et cacher) la plupart des composants, y compris le CPU. Ce type de puce est appelé **Système sur une puce** (SoC) et, dans ce cas, il a été spécifiquement conçu pour cette console, permettant à Nintendo de l'adapter à leurs besoins (efficacité énergétique, anti-piratage et E/S supplémentaires, entre autres). En même temps, cette puce ne se trouve dans aucun catalogue de vente au détail, ce qui signifie que les concurrents de l'époque avaient plus de mal à la cloner.

Cela dit, le SoC trouvé sur la Game Boy est appelé **DMG-CPU** ou **Sharp LR35902** [@cpu-realboy] et, comme son nom l'indique, il est fabriqué par **Sharp Corporation**. Cette entreprise, avec Ricoh (le fournisseur du [CPU de la NES](nes#a-bit-of-context)), a entretenu une relation étroite avec Nintendo.

### Le cœur du CPU

Au sein du DMG-CPU, le processeur principal est un **Sharp SM83** [@cpu-gekkio] et il est un mélange entre le Z80 (le même CPU utilisé sur la [Sega Master System](master-system#cpu)) et l'Intel 8080. Il fonctionne à **~4.19 MHz**, ce qui est plus rapide que le CPU moyen de 1 MHz, mais n'oubliez pas que les vitesses d'horloge [peuvent être trompeuses](master-system#relative-comparison).

![Le DMG-CPU, trouvé sur la carte mère de la Game Boy.](dmg_cpu.png)

Maintenant, quand j'ai fait l'[analyse du Master system](master-system), j'ai expliqué que le Z80 est lui-même un sur-ensemble du 8080. Alors, qu'est-ce que le SM83 a réellement et qu'est-ce qui lui manque par rapport à ces deux-là ? [@cpu-z80_comparison]

- Ni les registres `IX` ou `IY` du Z80 ni les instructions `IN` ou `OUT` du 8080 ne sont incluses. Cela signifie que les [ports E/S](master-system#accessing-the-rest-of-the-components) ne sont pas disponibles. Je ne suis pas certain de si c'est juste une mesure de réduction des couts, mais une chose est sûre : ces composants doivent être **totalement mappés en mémoire** [@cpu-fayzullin].
- Seul l'ensemble de registres de l'Intel 8080 est implémenté. Par conséquent, il n'y a **que sept registres à usage général**, contrairement au Z80 avec ses 14 registres (en raison de l'ajout d'un ensemble 'alternatif').
- Inclut une partie de l'ensemble d'instructions étendu du Z80 (seulement les instructions de manipulation de bits).

De plus, Sharp a également ajouté quelques **nouvelles instructions** qui ne sont présentes ni dans le Z80 ni dans le 8080. Ils optimisent certaines opérations liées à la façon dont Nintendo/Sharp a organisé le matériel. Un exemple est la nouvelle instruction `LDH` (signifiant 'load from high memory' [@cpu-ldh]), qui a été spécifiquement incluse pour fonctionner sur les derniers 256 octets de la carte mémoire (où les adresses commencent à `$ff00`) et, plus important encore, tient en un octet de moins (et est donc légèrement plus rapide).

#### L'effet de couleur

![Carte mère du Game Color [@photography-amos].](motherboard_cgb.png){.tabs-nested .active title="Original"}

![La même image sans la légende des parties importantes.](motherboard_cgb_marked.png){.tabs-nested-last title="Marqué"}

Presque 10 ans plus tard, après l'abandon du [Virtual Boy](virtual-boy) et de son [matériel de pointe](virtual-boy#cpu), un successeur modeste est arrivé : le **Game Boy Color**. À l'intérieur, il y a un nouveau SoC nommé **CPU CGB** qui apporte quelques ajouts. Cependant, son noyau de CPU SM83 reste le même, sauf pour sa vitesse d'horloge doublée (maintenant fonctionnant à **~8.38 MHz**).

Il est difficile de penser qu'après une décennie, Nintendo continuerait à regrouper le même CPU, mais une telle décision comporte les avantages suivants :

- Les développeurs peuvent réutiliser leurs compétences acquises pour programmer la nouvelle console.
- Il y a une économie de coûts en n'ayant pas à redessiner leur système pour fonctionner avec une nouvelle architecture.
- La compatibilité ascendante devient possible sans effort considérable. En fait, Nintendo l'a mis en œuvre en programmant deux modes de fonctionnement sur le CPU CGB :
  - **Mode normal** : Le SM83 fonctionne à **~4.19 MHz**.
  - **Mode à double vitesse** : Le SM83 fonctionne à **~8.38 MHz**.

Cependant, cela se fait au prix de l'adoption d'une technologie obsolète selon les normes de la fin des années 90. Il suffit de regarder l'[état du marché des CPU](playstation#tab-1-1-a-bit-of-history) pour remarquer les capacités dont Nintendo se privait (pour être juste, Nintendo a essayé avec le [Virtual Boy](virtual-boy)).

### Accès hardware

Le SM83 conserve un **bus de données de 8 bits** et un **bus d'adresses de 16 bits**, donc jusqu'à **64 KB de mémoire** peuvent être adressés. La carte mémoire est principalement composée des points d'extrémité suivants [@cpu-memory_map] :

- Espace de la Game Pak (la cartouche de jeu).
- Work RAM (WRAM), High RAM (HRAM) et Display RAM (VRAM).
- I/O (joypad, audio, graphiques et LCD).
- Contrôles d'interruption.

Ces éléments seront expliqués tout au long de cet article.

### Mémoire disponible

![Architecture de la mémoire du DMG (Game Boy original). Le PPU arbitre l'accès à la VRAM.](dmg-ram.png)

Nintendo a incorporé **8 Ko de RAM** sur la carte mère, ceci est pour un usage général (qu'ils appellent **Work RAM** ou « WRAM ») [@cpu-nintendo]. Notez que c'est quatre fois plus que ce qu'avait la [NES](nes).

Il y a un supplément de **127 B** de RAM logé dans le SoC. Il est appelé **High RAM** ou 'HRAM', et fournit un petit espace pour les données qui peuvent être accédées plus rapidement via l'instruction unique `LDH` du SM83. Ceci est très similaire au mode 'Zero Page' du 6502 [@cpu-zero_page], qui optimisait également les performances en fonction de l'emplacement de la mémoire. Maintenant, la High RAM n'est pas techniquement plus rapide à accéder que la RAM générale, mais c'est une zone priorisée pour le CPU. Vous comprendrez ce que cela signifie lorsque vous atteindrez la section 'Graphiques', où je discute du composant DMA.

![Architecture de mémoire étendue du CGB (Game Boy Color). Encore une fois, le PPU arbitre l'accès à la VRAM.](cgb-ram.png)

Plus tard, avec la variante Color, Nintendo a agrandi la WRAM à **32 KB**. Cependant, comme le CPU est resté inchangé (en particulier ses capacités d'adressage), il n'est pas possible de connecter toute la nouvelle mémoire sans d'abord déborder l'espace d'adressage disponible. Pour résoudre ce problème, les ingénieurs de Nintendo ont mis en œuvre la [commutation de banques](nes#going-beyond-existing-capabilities). Initialement trouvé dans les cartouches [NES](nes#cartridgegame-data), la Game Boy Color utilise le même principe pour accéder à ces 32 KB en utilisant seulement 8 KB d'espace mémoire. L'astuce est simple : les derniers 4 Ko peuvent être échangés en utilisant sept banques différentes. Par conséquent, le CPU intègre un registre supplémentaire (appelé `SVBK`) agissant comme le commutateur de banque, c'est ce que les développeurs doivent utiliser pour examiner la mémoire étendue.

## Graphismes

Tous les calculs graphiques sont réalisés par le CPU, avant que le **Picture Processing Unit** ou 'PPU' n'en fasse le rendu. C'est un autre composant que l'on trouve à l'intérieur du DMG-CPU et qui pourrait être décrit comme une version améliorée de la [puce graphique du prédécesseur](nes#graphics) (du même nom).

L'image est affichée sur un écran LCD intégré, elle a une résolution de **160×144 pixels** et, dans le cas du Game Boy monochrome, affiche **4 nuances de gris** (blanc, gris clair, gris foncé et noir). Comme le Game Boy original a un écran LCD vert, l'image aura une apparence *verdâtre*.

Si vous avez lu l'article sur la NES, vous vous souvenez peut-être que le PPU a été conçu pour se conformer au faisceau du CRT (tube cathodique). Cependant (et pour des raisons évidentes), nous avons un écran LCD dans la Game Boy. Eh bien, le nouveau PPU suit également cette méthodologie car les écrans LCD doivent également être rafraîchis. En faisant cela, cette console bénéficiera des [effets basés sur CRT](nes#tab-5-4-background-split) qui ont précédemment permis aux développeurs NES de proposer du contenu imaginatif.

### Organiser le contenu

![Architecture mémoire du PPU.](ppu.png)

Le PPU est connecté à **8 KB de VRAM** ou 'Display RAM'. Ce faisant, il fournit également un accès arbitré au CPU. Ces 8 Ko contiendront la plupart des données dont le PPU aura besoin pour effectuer les rendus. Les matériaux restants seront plutôt stockés à l'intérieur du PPU, car ils nécessitent des taux d'accès plus rapides.

Le jeu est chargé de remplir les différentes zones avec les bons types de données. De plus, le PPU expose des registres pour que le jeu puisse indiquer à la PPU comment ces données sont organisées. Cependant, il y a beaucoup de règles à suivre (vous le verrez dans les sections suivantes).

### Construire l'image (frame)

Voyons comment le PPU parvient à dessiner des choses sur l'écran. À des fins de démonstration, *Super Mario Land 2* sera utilisé comme exemple.

#### Tuiles {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![Plusieurs tuiles.](ppu_mario/tiles.png){.active title="Toutes"}

![Plusieurs tuiles séparées par une grille.](ppu_mario/tiles_grid.png){title="Grille"}

![Une seule tuile.](ppu_mario/tiles_single.png){title="Unique"}

Tuiles trouvées dans la Table des Motifs.

:::

Le PPU utilise des **tuiles** comme ingrédient de base pour le rendu graphique, spécifiquement, **les sprites et les arrière-plans** [@graphics-overview].

Les tuiles sont uniquement des **bitmaps 8x8** stockés dans la VRAM dans une région appelée **Tile set** ou « Tile pattern table », chaque pixel correspondant à l'une des quatre nuances de gris disponibles. En pratique, cependant, les nuances de gris sont sélectionnées grâce à une 'palette' de couleurs. Les Game Boys monochromes contiennent des registres qui définissent ces palettes. Comme expliqué précédemment, il n'y a que quatre couleurs/nuances de gris à choisir, donc un seul registre de 8 bits peut contenir une palette de quatre nuances sans problème. Cela dit, le système fournit trois registres (donc, **trois palettes programmables**) avec **utilisation restreinte** (plus de détails expliqués plus tard).

De plus, les tuiles sont regroupées en **deux tables de motifs**.

Pour construire l'image, les tuiles sont référencées dans un autre type de table appelé **Carte de tuiles**. Cette information va indiquer au PPU où il doit afficher les tuiles. Deux maps sont stockées pour construire les différentes couches de l'image.

Les prochaines sections détaillent comment les tile maps sont utilisées pour construire les couches.

#### La couche d''arrière-plan {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Background map allouée dans la VRAM.](ppu_mario/background.png){.active title="Complet"}

![Surface sélectionnée de l'arrière-plan. Notez que la partie sélectionnée comprend une portion du haut, celle-ci sera recouverte par la couche _Window_.](ppu_mario/background_selected.png){title="Sélectionné"}

![Background map affichée.](ppu_mario/background_rendered.png){title="Rendu"}

Processus de rendu de la carte d'arrière-plan.

:::

La couche Background est une map de **256x256 pixels** (avec des tuiles de 32x32) qui contient des **tuiles statiques**. Cependant, n'oubliez pas que seulement 160x144 pixels sont visibles à l'écran, c'est donc le jeu qui décide quelle partie sera sélectionnée pour l'affichage. Les jeux peuvent également déplacer la zone visible au cours du jeu, c'est ainsi que l'on obtient le **Scrolling Effect** (effet de défilement).

Une des deux tile maps peut être utilisée pour construire la couche de fond (background). De plus, il n'y a qu'**une seule palette** disponible pour cette couche.

#### Fenêtre {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Map Window allouée.](ppu_mario/window.png){.active title="Complet"}

![Map Window affichée. Le jeu l'active sur les dernières lignes de balayage. Par conséquent, seules les premières lignes sont affichées en bas de l'écran.](ppu_mario/window_rendered.png){title="Sélectionné"}

Processus de rendu de la carte de la fenêtre.

:::

La couche Window est une couche de **160x144 pixels** contenant des tuiles affichées au-dessus de l'arrière-plan (background) et des sprites. Cette couche **ne défile pas**.

La carte de tuiles restante peut être affectée à la couche Window, elle partage également la même palette avec la couche Background.

En somme, cela peut sembler être une fonctionnalité stupide. Comme la fenêtre n'a pas de transparence et occulte donc complètement l'arrière-plan, vous pouvez vous demander 'À quoi sert-elle ?'. Eh bien, les couches Background et Window peuvent être utilisées **simultanément** à différentes parties de l'écran. Cela est destiné à afficher des informations principalement en bas de l'écran, mais alors que sur la NES cela nécessitait de réaliser des [écritures complexes et chronométrées](nes#tab-5-4-background-split), le PPU de la Game Boy peut le gérer automatiquement.

Ainsi, les jeux l'utilisent normalement pour afficher les statistiques des joueurs, les scores et autres informations 'toujours actives'.

#### Sprites {.tab}

![Rendu de la couche Sprite.](ppu_mario/sprite.png) {.tab-float.pixel}

Les sprites sont des tuiles qui peuvent se déplacer indépendamment sur l'écran. Ils peuvent également se chevaucher et apparaître derrière l'arrière-plan (background), l'élément visible sera déterminé en fonction d'un attribut de priorité.

Cette couche a également une couleur supplémentaire disponible/requise : **Transparent**. Ils ne peuvent donc afficher que trois nuances de gris au lieu de quatre. Heureusement, cette couche en particulier est fournie avec **deux palettes dédiées** parmi lesquelles choisir.

La **Mémoire d'Attribut d'Objet** ou 'OAM' est une carte stockée à l'intérieur du PPU qui spécifie les tuiles qui seront utilisées comme sprites. Au lieu d'utiliser une carte de tuiles, **les sprites sont définis dans l'OAM**. Les jeux remplissent généralement cette région en appelant l'**unité OAM DMA** trouvée à l'intérieur de la puce, le DMA récupère les données de la RAM principale ou de la ROM du jeu et les envoie à l'OAM. Maintenant, pendant que le DMA est en cours, le CPU ne peut pas accéder à la mémoire externe (d'où l'importance d'utiliser la HRAM pendant cette période).

En plus de l'index de tuile, chaque entrée contient les attributs suivants : position X-Y, palette choisie, priorité et indicateurs de retournement (permettant de faire pivoter la tuile verticalement et horizontalement).

Le PPU est limité au rendu **jusqu'à dix sprites par ligne de balayage** et **jusqu'à 40 par frame**, le dépassement de cette limite aura pour conséquence que ces sprites ne seront pas dessinés.

#### Résultat {.tab}

![Résultat final. _Tada!_](ppu_mario/result.png) {.tab-float.pixel}

Une fois que la frame est terminée, il est temps de passer à la suivante ! Toutefois, le processeur ne peut pas modifier les tables pendant que le PPU est en train de les lire depuis la VRAM, c'est pourquoi le système prévoit un ensemble d'interruptions déclenchées lorsque le PPU est inactif. On peut retrouver ce comportement chez la NES.

Lorsqu'une seule ligne de balayage est terminée, la période de **Horizontal Blank** commence. Cela permet de jouer avec la partie du cadre qui n'a pas encore été dessinée.

Lorsque toutes les lignes de balayage sont terminées, la période de **Vertical Blank** commence, et une interruption dédiée est appelée. Le jeu peut maintenant mettre à jour l'affichage pour la frame suivante.

Il existe un état supplémentaire appelé **OAM search** qui est déclenché au début de la ligne de balayage ; à ce stade, le PPU traite quels sprites seront affichés dans cette ligne de balayage, pour que le jeu puisse mettre à jour n'importe quelle région, sauf l'OAM.

### Secrets et limitations {.tabs-close}

L'inclusion de la couche Window et d'interruptions supplémentaires a permis l'apparitions de nouveaux types de contenus et d'effets.

#### Effet de vacillement {.tabs.active}

![The Legend of Zelda: Link's Awakening (1993). _divulgâcheurs !_](zelda){.tab-float video="true"}

Les « horizontal interrupts » ont permis de modifier la frame avant d'être terminée. Cela signifie qu'une valeur de défilement différente pourrait être appliquée à chaque ligne, ce qui se traduit par un décalage de chaque ligne de la trame à des vitesses différentes.

Cela a permis d'obtenir un intéressant *effet de vacillement* (je ne suis pas sûr que ce soit son nom officiel).

### Les ajouts de couleurs {.tabs-close}

Le PPU de la Game Boy Color se comporte comme un super-ensemble de l'original. Vous allez maintenant voir quels sont les ajouts du soi-disant modèle 'Color' de cette marque.

#### Modes de fonctionnement

![The Legend of Zelda: Link's Awakening DX (1998).<br>Un jeu hybride Game Boy Color fonctionnant en mode CGB.](ppu_color/zelda.png) {.toleft.pixel}

![Super Mario Land 2, vu depuis une Game Boy Color. Ce dernier ajoute une palette colorisée et fonctionne en mode DMG.](ppu_color/dmg_mario.png) {.toright.pixel}

Pour commencer, pour des raisons de compatibilité, le nouveau PPU a **deux modes de fonctionnement**. Pourtant, Nintendo voulait que les utilisateurs de Color voient des améliorations même avec les jeux uniquement en monochrome. Par conséquent, les deux modes de fonctionnement sont les suivants :

- **Mode CGB**: Mode PPU étendu avec toutes les améliorations visuelles qui composent les nouveaux jeux Game Boy Color.
- **Mode DMG** : Mode traditionnel avec tous les extras désactivés. Cependant, vous verrez dans la section 'Système d'exploitation' que les jeux en monochrome sont néanmoins améliorés avec des palettes de couleurs.

#### Organiser le (nouveau) contenu

La carte mère CGB abrite maintenant **16 KB de VRAM** au lieu de cela, ce qui est le double de la quantité de VRAM originale. En raison des limitations d'adressage du CPU, cette nouvelle disposition est mise en œuvre sous la forme de **deux banques de 8 KB**, avec un nouveau registre (appelé `VBK`) agissant comme un commutateur. De l'autre côté, le PPU peut accéder aux deux banques en même temps. En fin de compte, cela signifie que les programmeurs n'ont besoin de remplir les banques de VRAM qu'avec l'aide de `VBK`, puis de spécifier dans la carte de tuiles la banque où se trouve la tuile, afin que le PPU puisse s'occuper du reste.

Cela étant dit, que pouvez-vous faire avec la VRAM supplémentaire ? Beaucoup de choses :

- Stockez deux fois plus de tuiles.
- Stockez plus de palettes, qui offrent également une gamme de couleurs plus large.
- Étendre l'espace des métadonnées de tuile pour coder plus d'effets et adresser des palettes supplémentaires.

##### Les visuels

Grâce au nouveau PPU, les programmeurs peuvent maintenant définir des palettes de couleurs avec **32 768 couleurs** parmi lesquelles choisir.

Premièrement, les développeurs doivent maintenant peupler une nouvelle zone appelée **Palette Memory**, qui stocke jusqu'à **seize palettes de couleurs** (la moitié pour l'arrière-plan et la fenêtre, la moitié pour les sprites) qui codent **quatre couleurs** [@cpu-nintendo]. Chaque entrée s'inscrit dans une valeur de 16 bits (2 octets) et seuls 15 bits sont utilisés. La Palette Memory n'est pas adressée par le CPU, cependant, un nouveau registre est utilisé comme tampon pour écrire sur cette mémoire (une méthodologie trouvée dans le [Super Nintendo](super-nintendo#organising-the-content)). En général, c'est ainsi que le CPU définit les palettes.

Cela dit, les tuiles d'arrière-plan et de fenêtre peuvent faire référence à l'une de ces huit palettes. Il en va de même pour les tuiles Sprite, sauf qu'elles sont contraintes à des palettes de trois couleurs, une entrée étant réservée pour la couleur 'transparente'.

##### L'espace supplémentaire

Ensuite, **Les ensembles de tuiles sont maintenant deux fois plus grands**. Ainsi, les programmeurs peuvent stocker le double de tuiles en VRAM. **Les cartes de tuiles d'arrière-plan/fenêtre ont également été étendues**, ce qui a pour conséquence que des métadonnées supplémentaires sont encodées. Par conséquent, l'extension des capacités de ces couches. Par exemple, leurs tuiles peuvent maintenant être **retournées horizontalement et verticalement**, ce qui évite au jeu de stocker des graphiques dupliqués en VRAM (ce qui, à son tour, peut être exploité pour dessiner un contenu plus unique).

De plus, le CPU CGB regroupe également **une unité DMA supplémentaire**, qui peut copier le contenu du Game Pak ou de la WRAM vers la VRAM. Il fonctionne en deux modes [@cpu-nintendo] :

- **DMA à usage général** : Le transfert aura lieu à tout moment et le DMA aura la priorité sur tout autre accès à la mémoire. Les programmeurs doivent donc être prudents quant à l'utilisation de ce composant (c'est-à-dire pendant ou en dehors de la numérisation) et comment (la quantité de données à copier), car une mauvaise utilisation peut entraîner un déchirement de l'écran (l'accès à la VRAM sera bloqué pendant le transfert).
- **DMA de balayage horizontal (H-Blank)** : Le transfert n'aura lieu que pendant les périodes de H-Blank. Cela évite les artefacts à l'écran, mais ne peut transférer du contenu que par lots de 16 octets, et fait des pauses pendant le balayage LCD.

Une fois de plus, cette unité offre aux programmeurs de nouvelles possibilités pour fournir un contenu plus riche, car elle profite des périodes initialement laissées pour l'inactivité.

## Audio

Le système audio est réalisé par l'**Audio Processing Unit** (APU), une [puce PSG](master-system#audio) avec quatre canaux [@audio-overview].

Curieusement, c'est l'une des rares sections qui n'a pas évolué entre les modèles. En fait, il ne peut même pas être accéléré, car si vous changez la vitesse des oscillateurs, vous n'entendrez pas de 'meilleurs' sons, mais une hauteur plus élevée.

### Fonctionnalités

Chacun des quatre canaux est réservé à un seul type de forme d'onde :

#### Signaux carrés {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Sortie de l'oscilloscope du canal 1 pour les signaux carrés.](pulse_single_1){.active video="true" title="Onde d'impulsion 1"}

![Sortie de l'oscilloscope du canal 2 pour les signaux carrés.](pulse_single_2){video="true" title="Onde d'impulsion 2"}

![Sortie de l'oscilloscope pour tous les canaux audio.](pulse_full){video="true" title="Complété"}

Pokémon Rouge/Bleu (1996).

:::

Les ondes d'impulsion (Pulse) ont un *bip* très distinct qui est principalement utilisé pour les **mélodies et les effets sonores**.

L'APU réserve deux canaux pour une onde d'impulsion chacun. Ces derniers utilisent l'un des quatre tons différents construits en variant leur largeur d'impulsion. Le premier canal dispose d'un **contrôle de balayage (Sweep control)** exclusif.

En raison du nombre limité de canaux, la mélodie est souvent interrompue lorsque des effets sonores sont joués dans le jeu. C'est très perceptible dans des jeux comme Pokemon Rouge/Bleu où, pendant un combat, le cri du Pokemon chevauche tous les canaux utilisés pour la musique ; c'est aussi pourquoi aucune musique de combat de Pokémon n'utilise de percussion.

#### Onde {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Vue oscilloscopique du canal d'onde.](wave_single){.active video="true" title="Onde"}

![Vue oscilloscopique de tous les canaux.](wave_full){video="true" title="Complété"}

Pokémon Rouge/Bleu (1996).

:::

L'APU permet de définir une **forme d'onde personnalisée** à entendre depuis son troisième canal. L'onde est composée de 32 échantillons de 4 bits qui peuvent être stockés dans une table d'ondes.

Ce canal permet également de contrôler sa fréquence (lui permettant de produire différentes notes musicales à partir de la même entrée) et le volume.

#### Le bruit {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Sortie de l'oscilloscope du canal dédié au bruit.](noise_single){.active video="true" title="Bruit"}

![Sortie de l'oscilloscope pour tous les canaux audio.](noise_full){video="true" title="Complété"}

Pokémon Rouge/Bleu (1996).

:::

Le bruit est essentiellement un ensemble de formes d'onde aléatoires qui ressemblent à du statique blanc. Un canal lui est dédié.

Les jeux l'utilisent pour les effets de percussion ou les effets *ambiants*.

Ce canal n'a que 2 tonalités disponibles, l'une produisant un son *statique propre* (clean static) et l'autre un son *statique robotique* (robotic static). Sa fréquence peut également être contrôlée.

### Secrets et limitations {.tabs-close}

Le mixeur donne du son stéréo, les canaux peuvent donc être assignés au côté gauche ou au côté droit, mais cela n'est possible à entendre qu'avec un casque ou des écouteurs ! Le haut-parleur est mono.

De plus, la puce de mixage est également connectée à une broche dédiée sur la cartouche, ce qui lui permet de diffuser un **canal supplémentaire** à condition que la cartouche émette le son analogue (seulement possible avec du matériel supplémentaire). Néanmoins, aucun jeu sur le marché n'a fini par utiliser cette fonctionnalité, et vous ne trouverez pas non plus cette broche sur le [Game Boy Advance](game-boy-advance)'s [sous-système compatible avec les versions antérieures](game-boy-advance#becoming-a-game-boy-color).

## Système d'exploitation

Contrairement à la NES, qui démarrait directement dans le jeu, la Game Boy a été conçue pour toujours démarrer à partir d'une **ROM de 256 octets** interne, puis passer au code du jeu. Le processus de démarrage est le suivant [@operating_system-boot]:

1. Une fois que la console est allumée, le processeur commence à lire à l'adresse **0x0000** (emplacement de la ROM de la Game Boy).
2. La RAM et l'APU sont initialisés.
3. Le logo Nintendo est copié de la ROM de la cartouche à la RAM d'affichage, puis il est dessiné en haut de l'écran. S'il n'y a pas de cartouche insérée, le logo contiendra des tuiles corrompues. Il peut en être de même si elle est mal insérée.
4. Le logo défile vers le bas et le fameux son *po-ling* est joué.
5. Le logo Nintendo du jeu est comparé à celui stocké dans la ROM de la console. Ensuite, une rapide **vérification de la somme de contrôle** est effectuée sur l'en-tête de la ROM de la cartouche pour s'assurer que la cartouche est correctement insérée. Si l'un de ces contrôles échoue, la console se bloque.
6. La ROM de la console est effacée de la mémoire (memory map).
7. Le processeur commence à exécuter le jeu.

Il est intéressant de noter que le logo *Nintendo* affiché à l'écran n'est pas effacé de la VRAM, de sorte que les jeux puissent appliquer des animations ou des effets pour introduire leur propre logo.

![L'écran de démarrage de la Game Boy avec 20y, un homebrew qui joue avec le logo.](20y){video="true"}

### La séquence révisée

![L'écran de démarrage de la Game Boy Color, le logo ne glisse plus mais il présente un effet arc-en-ciel.](boot_cgb.png) {.pixel}

Encore une fois, dans le cas de la Game Boy Color, nous trouvons des changements drastiques dans le contenu de la ROM. Par exemple, la taille de la ROM est maintenant de **2 KB** [@operating_system-boot_cgb] et les routines présentent un nouveau comportement :

- La séquence de démarrage vérifie maintenant si elle a un jeu Game Boy uniquement ou un jeu Game Boy Color inséré, puis régle les registres correspondants pour activer le mode DMG ou CGB. Le code de démarrage vérifie les métadonnées spécifiques du jeu (au sein de la ROM de la cartouche) qui sont structurées différemment pour les jeux CGB.
- Dans le cas où un jeu DMG est inséré, le programme de démarrage remplit également la RAM de la palette avec des palettes calculées, celles-ci sont basées sur un algorithme simple mais intelligent qui repose également sur les métadonnées du jeu. C'est ce qui fait que les jeux monochromes semblent colorés lorsqu'ils sont exécutés sur la console de nouvelle génération.
  - À ce stade, l'utilisateur peut appuyer sur une combinaison de boutons qui modifiera la palette de choix du code de démarrage.
- La vérification du logo Nintendo utilise désormais aussi le HRAM (sans succès, cependant).

## Jeux

Les jeux sont écrits en langage assembleur et ont une taille maximale de **32 Ko**, en raison de l'espace d'adressage limité disponible. Cependant, avec l'utilisation d'un **Contrôleur de Banque de Mémoire** ([mapper](nes#going-beyond-existing-capabilities)), les jeux peuvent atteindre des tailles plus importantes. Le plus grand Game Pak (cartouche) trouvé sur le marché a une ROM de 1 Mo (ou 8 Mo, dans le cas de la Game Boy Color).

Les Game Paks peuvent inclure une horloge en temps réel et une batterie externe avec de la SRAM pour conserver les sauvegardes, bien que tout cela soit facultatif.

### Types de cartouches

En raison des deux modes de fonctionnement pris en charge par la Game Boy Color, Nintendo a répertorié **trois types différents** de jeux Game Boy :

- **Game Boy** : Un type entièrement compatible avec tous les modèles de Game Boy. Il fonctionne toujours en mode DMG.
- **Game Boy Color améliorée** : Un hybride qui fonctionne différemment selon la console hôte. Bien qu'il soit entièrement compatible avec le modèle monochrome, il sera visuellement amélioré (en raison de son fonctionnement en mode CGB) lorsqu'il sera exécuté sur la Game Boy Color. Cependant, la fonctionnalité globale sera toujours limitée pour maintenir la compatibilité avec la Game Boy.
- **Exclusif à la Game Boy Color** : Compatible uniquement avec la Game Boy Color, mais il tire pleinement parti de son matériel.

### Communications externes

![Câble de liaison Game Boy pour plusieurs variantes de la console [@photography-amos]. Depuis la sortie de la console originale, les révisions ultérieures ont miniaturisé la prise tout en conservant le même nombre de broches.](link_cable.png) {.no-borders}

Pour la première fois, les jeux peuvent communiquer avec d'autres Game Boys à l'aide d'un câble **Game Boy Link**, qui offre une fonctionnalité multijoueur, par exemple. L'interface repose sur un type très primitif de **connexion série**.

La Game Boy originale transfère des informations à 8 Kbit/seconde (1 Ko/sec), tandis que la variante Color peut atteindre jusqu'à 512 Kbit/seconde (64 Ko/sec), cette dernière vitesse était connue sous le nom de mode 'haute vitesse'.

Cette dernière variante est également livrée avec un **émetteur et récepteur infrarouge** (composé d'une LED et d'un phototransistor, respectivement) [@cpu-nintendo]. Cela a rendu possible l'échange de données sans fil entre les Game Boy Colors, comme on peut le voir dans des jeux comme Pokemon Gold et similaires. Cependant, vous constaterez que le système n'implémente aucun protocole de communication, seulement un seul registre (`RP`) qui encode l'action du capteur IR (émettre ou recevoir), le seul bit (`0` ou `1`) étant transféré et le dernier bit reçu. Néanmoins, pour faciliter les choses aux développeurs, Nintendo a inclus une implémentation de référence dans le manuel officiel du développeur Game Boy [@cpu-nintendo].

## Anti-piratage

Comme vous l'avez vu dans la section 'Système d'exploitation', la console n'exécute jamais un jeu immédiatement, elle effectue d'abord une série de vérifications qui **empêchent l'exécution de cartouches non autorisées** et s'assure également que la cartouche est **correctement insérée**.

Pour pouvoir passer ces vérifications, les jeux devaient inclure une copie du logo de Nintendo (sous forme de tuiles) dans l'en-tête de la ROM [@games-dhole] ; de cette façon, Nintendo pourrait utiliser les lois sur le **Copyright et le Trademark** pour contrôler la distribution. *Plutôt malin, non ?*

Inversement, _Sega v. Accolade_ a plus tard accordé à toute entreprise le droit d'utiliser des logos protégés par des droits d'auteur pour ce type d'exigence, car cela relève de l'utilisation équitable.

Cela étant dit, d'autres mesures anti-piratage peuvent être mises en œuvre dans les jeux, comme la vérification de la taille de la SRAM (souvent plus grande dans les contrefaçons) ou faire des sommes de contrôle de la ROM à des endroits aléatoires du jeu.

## C'est tout pour le moment
