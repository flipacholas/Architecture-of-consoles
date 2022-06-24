---
name: Game Boy
subtitle: Simple et portable
date: 2019-02-21
releaseDate: 1989-04-21
generation: 4
cover: gameboy
top_tabs:
  Model:
    file: international
    caption: "La Game Boy originale\n<br>Sortie le 21/04/1989 au Japon, le 31/07/1989 en Amérique et le 28/09/1990 en Europe"
    img_class: reduced-width
  Motherboard:
    caption: "Image montrant la révision \"04\". Veuillez noter que \"DMG\" est l'identifiant du modèle original de la Game Boy."
  Diagram: { }
# SEO
title: Architecture de la Game Boy
#Historical
aliases:
  - /projects/consoles/game-boy/
---

## Une brève introduction

La Game Boy peut être vue comme une version portable de la NES avec une puissance limitée, mais vous verrez qu'elle intégrait de nouvelles fonctionnalités très intéressantes.

---

## Processeur

Au lieu de placer les puces standards sur la carte mère, Nintendo a opté pour une seule puce qui contient (et dissimule) une bonne partie des composants, dont le processeur. Ce type de puce est appelé "System On Chip (SoC, ou "système sur une puce") et celui qui se trouve sur la Game Boy est désigné sous le nom de **DMG-CPU** ou **Sharp LR35902**.

Ceci étant dit, le processeur principal est un **Sharp SM83**, un mélange entre le Z80 et l'Intel 8080. Il est cadencé à **~ 4,19 MHz**.

Sachant que le Z80 est lui-même un produit du 8080, qu'est-ce que le SM83 a et n'a pas de ces deux processeurs ?
- Ni les registres `IX` et `IY` du Z80 ni les instructions `IN` ou `OUT` du 8080 ne sont incluses : cela signifie que les [ports E/S]({{< ref "master-system#accessing-the-rest-of-the-components" >}}) ne sont pas disponibles. Je ne suis pas certain que ce soit juste une mesure de réduction des coûts, mais une chose est sûre, c'est que les composants devront être **complètement mappés en mémoire**.
- Seul le jeu de registres du 8080 est implémenté.
- Comprend le jeu d'instructions étendu du Z80. On ne trouve cependant que des instructions de manipulation de bits.

Enfin, ils ont également ajouté **quelques nouvelles instructions** qui ne sont présentes ni dans le Z80 ni dans le 8080. Je pense que les expliquer une à une dépasse le cadre de cet article, mais l'idée principale est qu'ils optimisent certaines opérations conditionnées par la façon dont Nintendo/Sharp ont conçu l'architecture.

### Mémoire disponible

Nintendo a incorporé **8 Ko de RAM** pour un usage général (qu'ils appellent **Work RAM** ou "WRAM"). Notez que c'est quatre fois plus que ce que la [NES]({{< ref "nes" >}}) avait.

### Accès hardware

Le SM83 conserve un bus de données de 8 bits et un bus d'adresse de 16 bits, ce qui permet d'adresser jusqu'à 64 Ko de mémoire. La cartographie mémoire se présente comme ceci :
  - Mémoire de la cartouche ;
  - WRAM et Display RAM ;
  - E/S (manette de jeu, audio, affichage et LCD) ;
  - Contrôles d'interruption.

---

## Graphismes

Tous les calculs graphiques sont effectués par le CPU, puis le **Picture Processing Unit** (ou "PPU") en assure le rendu. Il s'agit d'un autre composant trouvé dans le DMG-CPU et il est en réalité basé sur le [PPU de son prédécesseur]({{< ref "nes#graphics" >}}).

Le rendu est affiché sur un écran LCD intégré, celui-ci a une résolution de **160x144 pixels** et peut afficher **4 nuances de gris** (blanc, gris clair, gris foncé et noir). Mais comme la Game Boy originale a un écran LCD vert, les graphismes auront un aspect *verdâtre*.

Si vous avez lu l'article sur la NES, vous vous souvenez peut-être que le PPU a été conçu pour se conformer au faisceau du CRT (tube cathodique). Pourtant (et pour des raisons évidentes), nous avons un écran LCD sur la Game Boy. Eh bien, le PPU n'y change rien, puisque les écrans LCD doivent également être rafraîchis. En fait, certains effets spéciaux obtenus grâce à ce comportement seront également pris en charge sur la Game Boy.

### Organiser le contenu

{{< figure_img src="ppu.png" alt="Diagramme de la mémoire du PPU" img_class="no-borders" class="centered-container" >}}
Architecture mémoire du PPU
{{< /figure_img >}}

Le PPU possède **8 Ko de RAM**, ou "Display RAM", auxquels le PPU et le CPU peuvent accéder directement (mais pas en même temps). Ces 8 Ko contiendront la plupart des données dont le PPU aura besoin pour effectuer les rendus. Les bits restants seront plutôt stockés à l'intérieur du PPU, car ils nécessiteront un taux d'accès plus rapide.

Le jeu est chargé de remplir les différentes zones avec les bons types de données. De plus, le PPU expose les registres afin que le jeu puisse lui indiquer comment organiser ces données (il existe toutefois de nombreuses contraintes).

### Construire l'image (frame)

Voyons maintenant comment le PPU parvient à dessiner des choses sur l'écran. Pour illustrer notre propos, *Super Mario Land 2* sera utilisé comme exemple :

{{< tabs >}}

{{< tab name="Tuiles" active="true" >}}
{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Tout" active="true" src="ppu_mario/tiles.png" >}}
Plusieurs tuiles.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Grille" src="ppu_mario/tiles_grid.png" >}}
Plusieurs tuiles séparées par une grille.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Seule" src="ppu_mario/tiles_single.png" >}}
Une seule tuile.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Tuiles trouvées dans la Pattern Table.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Le PPU utilise les **tuiles** comme ingrédient de base pour le rendu graphique, ou plus précisément, **les sprites et les arrière-plans (background)**.

Les tuiles sont uniquement des **bitmaps 8x8** stockés dans la VRAM dans une région appelée **Tile set** ou "Tile pattern table", chaque pixel correspondant à l'une des quatre nuances de gris disponibles. Enfin, les tuiles sont regroupées dans deux tables de motifs (patterns).

Afin de construire l'image, les tuiles sont référencées dans un autre type de table appelé **Tile map**. Cette information va indiquer au PPU où il doit afficher les tuiles. Deux maps sont stockées pour construire les différentes couches de l'image.

Les prochaines sections détaillent comment les tile maps sont utilisées pour construire les couches.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Background" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Entier" active="true" src="ppu_mario/background.png" >}}
Background map allouée dans la VRAM.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Sélection" src="ppu_mario/background_selected.png" >}}
Zone sélectionnée sur la Background map.  
Notez que la partie sélectionnée comprend une portion du haut, qui sera recouverte par la _couche Window_.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Rendu" src="ppu_mario/background_rendered.png" >}}
Background map affichée.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Processus de rendu de la couche Background.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
La couche Background est une map de **256x256 pixels** (avec des tuiles de 32x32) qui contient des **tuiles statiques**. Cependant, n'oubliez pas que seulement 160x144 pixels sont visibles à l'écran, c'est donc le jeu qui décide quelle partie sera sélectionnée pour l'affichage. Les jeux peuvent également déplacer la zone visible au cours du jeu, c'est ainsi que l'on obtient le **Scrolling Effect** (effet de défilement).

Une des deux tile maps peut être utilisée pour construire la couche de fond (background).
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Window" >}}

{{< tabs nested="true" float="true" class="pixel" figure="true" >}}
  {{< tab_figure_img name="Complet" active="true" src="ppu_mario/window.png" alt="Window" >}}
Map Window allouée.
  {{< /tab_figure_img >}}
  {{< tab_figure_img name="Rendu" src="ppu_mario/window_rendered.png" alt="Rendu de Window" >}}
Map Window affichée. Le jeu l'active sur les dernières lignes de balayage. Par conséquent, seules les premières lignes sont affichées en bas de l'écran.
  {{< /tab_figure_img >}}
  {{< figcaption group="true" >}}Processus de rendu de la couche Window.{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
La couche Window est une couche de **160x144 pixels** contenant des tuiles affichées au-dessus de l'arrière-plan (background) et des sprites. Elle ne défile pas.

La tile map restante pourra être assignée à la couche fenêtre (Window).

Au premier abord, cela peut sembler être une fonctionnalité absurde. Après tout, la couche Window recouvre tout le reste, alors à quoi cela peut-il servir ? Eh bien, les couches Background et Window peuvent être utilisées simultanément à différents endroits de l'écran. Cela est accompli en modifiant le registre `LCDCONT` pendant des lignes de balayage spécifiques.

De ce fait, les jeux l'utilisent généralement pour afficher les statistiques des joueurs, le score et d'autres informations "persistantes".
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Sprites" >}}

{{< figure_img float="true" class="pixel" src="ppu_mario/sprite.png" alt="Sprites" >}}
Rendu de la couche Sprite
{{< /figure_img >}}

{{% inner_markdown %}}
Les sprites sont des tuiles qui peuvent se déplacer indépendamment sur l'écran. Ils peuvent également se chevaucher et apparaître derrière l'arrière-plan (background), l'élément visible sera déterminé en fonction d'un attribut de priorité.

Ils ont aussi une couleur supplémentaire disponible : **Transparent**. Ils ne peuvent donc afficher que trois nuances de gris au lieu de quatre. Heureusement, cette couche permet de définir deux palettes de couleurs afin de pouvoir utiliser chaque couleur.

L'**Object Attribute Memory** (ou "OAM") est une map stockée dans le PPU qui spécifie les tuiles qui seront utilisées comme sprites. Les jeux remplissent cette région en appelant l'**unité DMA** qui se trouve à l'intérieur de la puce ; le DMA récupère les données de la RAM principale ou de la ROM du jeu et les envoie à l'OAM.

Outre l'index de la tuile, chaque entrée contient les attributs suivants : position X-Y, palette de couleurs, priorité et "flip flags" (permettant de retourner les tuiles verticalement et horizontalement).

Le PPU est limité au rendu **jusqu'à dix sprites par ligne de balayage** et **jusqu'à 40 par trame**, le dépassement de cette limite aura pour conséquence que ces sprites ne seront pas dessinés.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Résultat" >}}

{{< figure_img float="true" class="pixel" src="ppu_mario/result.png" alt="Résultat" >}}
Résultat final. _Tada !_
{{< /figure_img >}}

{{% inner_markdown %}}
Une fois que la trame est terminée, il est temps de passer à la suivante ! Toutefois, le processeur ne peut pas modifier les tables pendant que le PPU est en train de les lire depuis la VRAM, c'est pourquoi le système prévoit un ensemble d'interruptions déclenchées lorsque le PPU est inactif. On peut retrouver ce comportement chez la NES.

Lorsqu'une ligne de balayage est complétée, l'interruption **Horizontal Blank** est appelée. Cela permet de jouer avec la partie du cadre qui n'a pas encore été dessinée.

Lorsque toutes les lignes de balayage sont complétées, l'interruption **Vertical Blank** est appelée. Le jeu peut maintenant mettre à jour l'affichage pour la trame suivante.

Il existe un état supplémentaire appelé **OAM search** qui est déclenché au début de la ligne de balayage ; à ce stade, le PPU traite quels sprites seront affichés dans cette ligne de balayage, pour que le jeu puisse mettre à jour n'importe quelle région, sauf l'OAM.
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

### Secrets et limitations

L'inclusion de la couche Window et d'interruptions supplémentaires a permis l'apparitions de nouveaux types de contenus et d'effets.

{{< tabs >}}
{{< tab name="Effet d'oscillation" active="true" >}}
{{< video src="zelda" float="true" >}}
The Legend of Zelda: Link's Awakening (1993) _Spoilers !_
{{< /video >}}

{{% inner_markdown %}}
Les interruptions horizontales ont permis de modifier la trame (frame) avant d'être terminée. Cela signifie qu'une valeur de défilement différente peut être appliquée à chaque ligne, ce qui entraînerait un déplacement différent pour chaque ligne de la trame.

On obtient un *effet d'oscillation* (je ne suis pas sûr que ce soit le nom officiel).
{{% /inner_markdown %}}

{{< /tab >}}
{{< /tabs >}}

---

## Audio

Le système audio est assuré par l'**Audio Processing Unit** (APU), une [puce PSG]({{< ref "master-system#audio" >}}) comportant quatre canaux.

### Fonctionnalités

Chaque canal est dédié à un type de forme d'onde :

{{< tabs >}}

{{< tab name="Pulse" active="true" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Pulse 1" src="pulse_single_1" >}}
Canal Pulse 1.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Pulse 2" src="pulse_single_2" active="true" >}}
Canal Pulse 2.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Complet" src="pulse_full" >}}
Tous les canaux audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}
Pokémon Rouge/Bleu (1996).
  {{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}

Les ondes d'impulsion (Pulse) ont un *bip* très distinct qui est principalement utilisé pour les **mélodies et les effets sonores**.

L'APU réserve deux canaux pour une onde d'impulsion chacune. Elles utilisent l'une des quatres tonalités différentes construites en faisant varier la largeur d'impulsion. Le premier canal dispose d'un **contrôle de balayage (Sweep control)** exclusif.

En raison du nombre limité de canaux, la mélodie est souvent interrompue lorsque des effets sonores sont joués dans le jeu. Ce phénomène est très audible dans des jeux comme Pokémon Rouge/Bleu quand, au cours d'un combat, le cri du Pokémon chevauche tous les canaux utilisés pour la musique.
{{% /inner_markdown %}}
{{< /tab >}}

{{< tab name="Noise" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Noise (bruit)" active="true" src="noise_single" >}}
Canal Noise.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Complet" src="noise_full" >}}
Tous les canaux audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Pokémon Rouge/Bleu (1996).{{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
Le bruit (Noise) est essentiellement un ensemble de formes d'ondes aléatoires qui ressemblent à des parasites blancs (white static). Un canal lui est dédié.

Les jeux l'utilisent pour les effets de percussion ou les effets *ambiants*.

Ce canal n'a que 2 tonalités disponibles, l'une produisant un son *statique propre* (clean static) et l'autre un son *statique robotique* (robotic static). Sa fréquence peut également être contrôlée.
{{% /inner_markdown %}}

{{< /tab >}}

{{< tab name="Onde" >}}
{{< tabs nested="true" float="true" figure="true" >}}
  {{< tab_figure_video name="Wave" active="true" src="wave_single" >}}
Canal Wave.
  {{< /tab_figure_video >}}
  {{< tab_figure_video name="Complet" src="wave_full" >}}
Tous les canaux audio.
  {{< /tab_figure_video >}}
  {{< figcaption group="true" >}}Pokémon Rouge/Bleu (1996){{< /figcaption >}}
{{< /tabs >}}

{{% inner_markdown %}}
L'APU permet de définir une **forme d'onde personnalisée** à entendre depuis son quatrième canal. L'onde est composée de 32 échantillons de 4 bits qui peuvent être stockés dans une table d'ondes.

Ce canal permet également de contrôler sa fréquence (permettant de produire différentes notes musicales à partir du même échantillon) et son volume.
{{% /inner_markdown %}}
{{< /tab >}}

{{< /tabs >}}

### Secrets et limitations

Le mixeur donne du son stéréo, les canaux peuvent donc être assignés au côté gauche ou au côté droit, mais cela n'est possible à entendre qu'avec un casque ou des écouteurs ! Le haut-parleur est mono.

Par ailleurs, la puce de mixage est aussi connectée à une broche dédiée sur la cartouche, lui permettant ainsi de diffuser du son sur un **canal supplémentaire**, à condition que celle-ci produise effectivement le son analogique (uniquement possible avec du matériel supplémentaire). Aucun jeu sur le marché n'a utilisé cette fonctionnalité.

---

## Jeux

Les jeux sont écrits en langage assembleur et ont une taille maximale de **32 Ko**, en raison de l'espace d'adressage limité disponible. Cependant, avec l'utilisation d'un contrôleur **Memory Bank Controller**, les jeux peuvent atteindre des tailles plus grandes. La plus grande cartouche trouvée sur le marché contient une ROM de 1 Mo.

Les cartouches peuvent inclure une horloge en temps réel et une batterie externe, ainsi que de la SRAM pour conserver les sauvegardes.

### Communications externes

Pour la première fois, les jeux peuvent communiquer avec d'autres consoles à l'aide d'un câble **Game Boy Link**, qui fournit des fonctionnalités multijoueur. L'interface utilise un type très rudimentaire de **transmission série**.

---

## Anti-piratage

La console contient une **ROM de 256 octets** stockée dans le processeur qui est utilisée pour amorcer la ROM de la cartouche. Elle ne lance cependant pas le jeu tout de suite, elle exécute d'abord une série de vérifications qui **empêchent l'exécution de cartouches non autorisées** et s'assure également que la cartouche est **correctement insérée**.

Pour pouvoir passer ces vérifications, les jeux devaient inclure une copie du logo de Nintendo (sous forme de tuiles) dans l'en-tête de la ROM ; de cette façon, Nintendo pourrait utiliser les lois sur le **Copyright et le Trademark** pour contrôler la distribution, *Malinx le Lynx, non ?*. La ROM de la Game Boy intègre également une copie du logo pour pouvoir le comparer.

Ceci étant dit, le processus d'amorçage est le suivant :

1. Une fois que la console est allumée, le processeur commence à lire à l'adresse **0x00** (emplacement de la ROM de la Game Boy).
1. Le son et la RAM sont initialisés.
2. Le logo Nintendo est copié de la ROM de la cartouche vers la Display RAM pour être ensuite dessiné sur la partie haute de l'écran. S'il n'y a pas de cartouche insérée, le logo contiendra des tuiles corrompues. La même chose peut se produire si elle est mal insérée.
2. Le logo défile vers le bas et le fameux son *po-ling* est joué.
3. Le logo Nintendo du jeu est comparé à celui stocké dans la ROM de la console ; si la vérification échoue, la console se bloque.
4. Une rapide **somme de contrôle** est effectuée sur l'en-tête de la ROM de la cartouche pour s'assurer que celle-ci est correctement insérée ; si la vérification échoue, la console se bloque.
5. La ROM de la console est effacée de la mémoire (memory map).
6. Le processeur commence à exécuter le jeu.

Il est intéressant de noter que le logo *Nintendo* affiché à l'écran n'est pas effacé de la VRAM, de sorte que les jeux puissent appliquer des animations ou des effets pour introduire leur propre logo.

{{< video src="20y" class="centered-container" >}}
20y, une démonstration homebrew qui joue avec le logo
{{< /video >}}

D'autres mesures anti-piratage peuvent être mises en œuvre dans les jeux, comme la vérification de la taille de la SRAM (souvent plus grande dans les contrefaçons) ou faire des sommes de contrôle de la ROM à des endroits aléatoires du jeu.

---

## C'est tout pour le moment
