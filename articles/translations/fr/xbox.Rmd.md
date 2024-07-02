---
short_title: Architecture de la Xbox
long_title: Architecture de la Xbox
name: Xbox
subtitle: Un concurrent redouté
date: 2020-06-26
release_date: 2001-11-15
cover: "xbox"
generation: 6
seo_image_pos: "Bottom"
top_tabs:
  Model:
    file: international
    caption: "La Xbox originale.<br>Sortie le 15/11/2001 en Amérique, le 22/02/2002 au Japon et le 14/03/2002 en Europe."
  Motherboard:
    caption: "Affichage de la première révision.<br>Les contrôleurs sont branchés à l'aide d'une carte fille séparée.<br>Les puces SDRAM manquantes sont à l'arrière et il y a quatre emplacements SDRAM non peuplés."
  Diagram:
    caption: "Chaque contrôleur est connecté à un hub USB séparé."
#Historical
aliases:
  - /projects/consoles/xbox/
---

## Une brève introduction

Il semble que Microsoft ait décidé de reprendre là où Sega s'est arrêté. Leur offre ? Un système avec des similitudes appréciées par les développeurs et des services en ligne accueillis par les utilisateurs.

Veuillez noter que pour maintenir la cohérence avec d'autres sources, cet article sépare les unités de stockage entre le préfixe métrique (c'est-à-dire mégaoctets ou 'MB') et le préfixe binaire standardisé (c'est-à-dire mébioctets ou 'MiB'), ainsi :

- 1 MB = 1000 KB
- 1 MiB = 1024 KiB

... et ainsi de suite.

### Conseils de lecture

Quelques mois après avoir écrit ceci, j'ai réalisé que c'est l'un des textes les plus denses que j'ai rédigés. Il se passe juste beaucoup de choses à l'intérieur de la Xbox et j'ai essayé de mentionner la plupart d'entre elles.

Maintenant, si vous êtes vraiment intéressé à comprendre ce système mais que vous trouvez cet article difficile à suivre, mon conseil est : **Prenez votre temps**, l'article ne va nulle part. Concentrez-vous sur ce que vous aimez, lisez à votre propre rythme, consultez les liens dans la section 'Sources' pour obtenir de l'aide et enfin, ne vous mettez pas la pression, il n'y a pas d'examen !

## {.supporting-imagery}

## CPU

Le processeur inclus dans cette console est une version légèrement personnalisée du célèbre **Intel Pentium III** (un CPU standard pour ordinateurs) fonctionnant à **733 MHz**. Avec cela, on peut supposer que cette console n'est qu'un PC en coulisses... Je ne vous donnerai pas la réponse, mais je promets qu'à la fin de l'article, vous serez en mesure de parvenir à votre propre conclusion.

![Le package Intel Pentium III sur la carte mère de la Xbox.](cpu.jpg)

Quoi qu'il en soit, les Pentiums, ainsi que d'autres lignes de CPU conçus et fabriqués par Intel, étaient incroyablement populaires sur le marché de l'informatique. La part de marché d'Intel était telle qu'ils sont devenus le point de référence par défaut pour la qualité : en tant qu'utilisateur typique, si vous vouliez un bon ordinateur et aviez le budget, vous n'aviez qu'à chercher *quelque chose* équipé d'un CPU Intel. Nous savons tous maintenant qu'il y a plus de facteurs impliqués, mais c'est ce que les gars du marketing chez Intel ont réussi à projeter.

### Informations techniques

Maintenant que nous avons positionné Intel sur la carte, revenons au sujet de cette console. Lors de mes recherches, je m'attendais à trouver de la documentation avec le même niveau de profondeur que d'autres CPUs (MIPS, SuperH, ARM, etc), mais au lieu de cela, je suis tombé sur une quantité excessive de termes marketing qui n'ont fait que détourner ma recherche. Donc, pour cet article, j'ai élaboré une structure pour organiser toutes les informations nécessaires qui aideront à comprendre comment fonctionne ce CPU. De plus, j'essaierai d'introduire certains termes que Intel a utilisé pour marquer ce CPU.

Cela dit, jetons un coup d'œil :

#### Marque {.tabs.active}

![Comment cette étude est organisée.](cpu/branding.png) {.tab-float}

Tout d'abord, le CPU de la Xbox est identifié comme un **Pentium III**. Alors, qu'est-ce que cela signifie ? À l'époque (début des années 2000), la série Pentium représentait la nouvelle génération de CPU. Ils étaient le 'nouveau haut de gamme' qui regroupait toutes les technologies sophistiquées qui rendaient les ordinateurs super rapides, en plus cela aidait les acheteurs à décider quel CPU ils devaient acheter s'ils voulaient *le meilleur du meilleur*.

Le Pentium III a remplacé le Pentium II, qui à son tour a remplacé le Pentium original. De plus, lorsque le premier Pentium est sorti, il a remplacé le 80486, qui à son tour a remplacé le 80386... Vous avez compris l'idée. Ce qui compte, c'est que 'Pentium' est principalement un nom de marque, il n'est pas directement associé à son fonctionnement interne. Par conséquent, nous devons aller plus loin !

Pour aller plus loin et ne pas se perdre en chemin, j'ai catalogué les informations en trois sections qui, combinées, constituent la puce. La première est l'**Architecture de l'ensemble d'instructions** ou 'ISA' (le groupe d'instructions utilisé pour commander le CPU), **Microarchitecture** (comment l'ISA est mise en œuvre en silicium) et le **Cœur** (quel ensemble de composants sont utilisés pour emballer la microarchitecture pour former le modèle de CPU spécifique).

#### L'ISA {.tab}

En effet, une fois que je mentionne 'Intel', il est question de temps avant que je présente le célèbre **x86**, son ensemble d'instructions.

La première forme de x86 a fait ses débuts avec la sortie de l'**Intel 8086** en 1978, un CPU 16 bits. Ensuite, l'ISA a été constamment élargie avec plus d'instructions à mesure que de nouveaux processeurs Intel étaient lancés (80186, 80286 et ainsi de suite) [@cpu-thompson]. Par conséquent, le x86 a commencé à se fragmenter à mesure que de nouvelles fonctionnalités révolutionnaires étaient ajoutées (c'est-à-dire le 'mode protégé' et le 'mode long'). Pour faire face à cela, les applications x86 modernes ciblent couramment l'ISA 80386 (également appelée **IA-32** ou **i386**) comme base, qui, entre autres, fonctionne dans un environnement 32 bits.

Par la suite, Intel a amélioré l'IA-32 avec l'utilisation d'**extensions**, ce qui signifie que les nouvelles fonctions peuvent ou non être incluses dans un CPU IA-32. Les programmes interrogent le CPU pour vérifier si une amélioration spécifique est présente. Le CPU de la Xbox comprend deux extensions :

- **MMX** (Extension multimédia): Ajoute 57 instructions SIMD et 8 registres de 64 bits (entiers uniquement) qui peuvent accélérer les opérations vectorielles.
- **SSE** (Streaming SIMD extension) : Une autre extension de type SIMD qui répond à la critique de MMX (manque de support en virgule flottante et incapacité à utiliser l'unité de virgule flottante en parallèle). Il ajoute 56 nouvelles instructions et huit registres de 128 bits (appelés 'XMM') qui contiennent quatre `floats` de 32 bits.

La bonne nouvelle est que, puisque la console aura toujours le même CPU, les programmeurs peuvent optimiser leur code pour exploiter ces extensions car elles seront toujours présentes.

#### La Microarchitecture {.tab}

Quand il s'agit de construire un circuit qui peut interpréter les instructions x86, Intel a proposé tellement de conceptions différentes pour leurs CPU. Certaines conceptions ont été présentées avec la sortie d'une nouvelle série Pentium (c'est-à-dire. Pentium 4) tandis que d'autres ont été présentés lorsque Intel a sorti une version 'améliorée' d'un Pentium (comme le 'Pentium Pro'). Néanmoins, depuis la sortie du premier Pentium, le modèle de CPU et la microarchitecture ne portent plus le même nom. Par exemple, le 80486 utilise la microarchitecture 80486 (et aucune autre), cependant, le Pentium original a la microarchitecture 'P5'.

Maintenant, le CPU de la Xbox, ainsi que le reste des processeurs Pentium III, utilisent la **Microarchitecture P6** (également connue sous le nom 'i686'). C'est la 6ème génération (en comptant à partir du 8086) qui présente :

- Un *énorme* **pipeline de 14 étapes**: Cela signifie que jusqu'à 14 instructions peuvent être traitées en parallèle. D'un autre côté, les instructions individuelles peuvent prendre beaucoup plus de cycles pour être complétées. Voir [une explication précédente](game-boy-advance#the-core).
- **Exécution hors ordre**: Si possible, le CPU réorganise la séquence d'instructions pour augmenter l'efficacité et la performance.
- **Exécution dynamique** : Puisque le P6 est une conception hors d'ordre et [superscalaire](dreamcast#cpu). Le traditionnel [prédictor de branche](gamecube#features) est maintenant combiné avec d'autres techniques ('exécution spéculative' et 'analyse de flux de données') pour tirer parti des nouvelles capacités. Ce faisant, il réduit encore plus les arrêts de pipeline.

Cela dit, prenez un plus près look à ces caractéristiques. Il se trouve qu'elles sont très similaires aux [consoles précédentes](gamecube#features), cependant, les autres CPU sont très différents en termes de conception par rapport à ceux d'Intel. Historiquement, on pourrait soutenir que la conception du x86 n'aurait jamais permis à Intel de produire, disons, un CPU en pipeline. Pourtant, ils ont réussi à le faire, voyons pourquoi...

#### CISC ou RISC {.tab}

La concurrence se trouve à présenter des CPUs conçus autour des [**directives RISC**](playstation#tab-1-1-a-bit-of-history), alors que le x86 d'Intel ne l'est pas, se retrouvant ainsi dans le groupe **CISC**. Les CPUs RISC sont connus pour avoir un design intentionnellement simplifié par rapport aux CPUs CISC. Cela comprend, par exemple, la mise en œuvre d'une **architecture de chargement-stockage**, qui ne fournit que des instructions qui opèrent des valeurs à partir de registres (au lieu d'opérer directement à partir de la mémoire).

Un des avantages des processeurs RISC est que leur approche simpliste permet à leurs CPU d'être conçus avec un sens modulaire, qui peut à son tour être exploité pour améliorer les performances avec des techniques de parallélisme. C'est pourquoi nous avons vu des CPU comme MIPS et PowerPC débuter des étapes de pipeline, des conceptions superscalaires, l'exécution hors ordre, la prédiction de branchement, etc. De l'autre côté, les processeurs CISC précèdent les processeurs RISC et les premiers visaient à résoudre des besoins différents. Par conséquent, leurs conceptions ne sont pas aussi flexibles que les CPUs RISC.

Pour revenir à la question initiale, le P6 est une conception intéressante, car bien que ce CPU ne comprenne qu'un ensemble d'instructions CISC (x86), un sous-ensemble de ses opcodes est interprété en utilisant **microcode**. Plus important encore, l'unité qui exécute le microcode **est construite autour du modèle de chargement-stockage** [@cpu-gwennap]. C'est parce que l'architecture P6 a été conçue par les anciens ingénieurs de l'**Intel i960** (un CPU RISC autrefois prometteur d'Intel). En somme, cela a permis à Intel d'obtenir des avantages similaires à ceux des processeurs RISC sans rompre la compatibilité avec l'ISA x86 historique. Il est juste de dire qu'avec le temps, des termes comme 'CISC' et 'RISC' sont devenus trop ambigus pour catégoriser n'importe quel CPU moderne.

En passant, le microcode est déjà intégré dans le silicium mais il peut être corrigé, permettant à Intel de réparer ses CPUs après production chaque fois qu'un bug ou une vulnérabilité de sécurité est découvert. Si vous avez lu les articles précédents (c'est-à-dire [N64](nintendo-64) ou [PS2](playstation-2)), gardez à l'esprit que le microcode d'Intel n'est **pas accessible au public** (encore moins documenté) et Intel est son seul 'mainteneur'.

#### Le cœur du CPU {.tab}

![Le design de Coppermine.](cpu/core.png) {.tab-float}

Intel a expédié de nombreux puces qui implémentaient la microarchitecture P6. La Xbox comprend un modèle appelé **Coppermine**. Il a été commercialisé comme la deuxième révision du Pentium III (remplaçant le cœur 'Katmai') et comprend les composants suivants :

- **32 KiB de cache L1** : divisés entre 16 KiB pour les instructions et 16 KiB pour les données.
- Cache L2 intégré de **128 KiB** : C'est *étrange* puisque le Coppermine de série a 256 KiB de L2 [@cpu-anand]. En fait, le Coppermine128 (que l'on trouve dans la marque 'Celeron' d'Intel, l'alternative bas de gamme de Pentium) a la même quantité de L2 [@cpu-shvets]. Par conséquent, cela a probablement été fait pour réduire les coûts de fabrication et maintenir cette console à un prix compétitif.
- 133 MHz **Bus frontal**: C'est le bus qui relie le cache L2 au contrôleur de mémoire, nous en verrons plus à ce sujet plus tard.
  - Intel l'appelle 'Front-side bus' pour le distinguer d'un autre bus qui connecte L2 (cache externe) avec L1 (cache interne). Ce dernier bus est appelé 'Back-side bus'... et c'est un nom malheureux à utiliser au Royaume-Uni.

Coppermine ajoute également deux 'améliorations' à leur mise en œuvre originale de cache L2, il s'agit du **Advanced Transfer Cache** et du **Advanced System Buffering**. Pour les résumer, le cache L2 est sur la puce et leurs bus sont plus larges, ce qui aide à réduire les éventuels goulets d'étranglement dans le bus frontal.

Enfin, la puce utilise le socket 'Micro-PGA2' pour s'adapter sur la carte mère, mais comme toute autre console, la Xbox l'a soudée avec un Ball Grid Array ou 'BGA'.

### P6 et la fin des nombres Pentium {.tabs-close}

Voici un peu plus d'histoire. Après les années du P6, Intel prévoyait de le succéder avec la microarchitecture 'NetBurst' (présente dans le Pentium IV). Cependant, la ligne de succession s'est également terminée là. Bien que NetBurst ait mis en œuvre de nombreuses techniques contemporaines [@cpu-netburst], il a également souffert d'une consommation d'énergie excessive, d'une émission de chaleur (en moyenne de 85 W [@cpu-pentium4]) et de problèmes de scalabilité - tous ces éléments ont empêché la conception d'aller plus loin.

En conséquence, cela a incité une équipe Intel en Israël à revisiter leur CPU P6 à faible consommation - le 'Pentium M' - et à développer un successeur plus puissant. Le premier résultat a été le cœur **Yonah**, une légère amélioration de la conception du P6, commercialisée sous le nom de **Core Solo** ou **Core Duo**. Avec cela, la dissipation de la puissance est tombée à des chiffres raisonnables (entre 27 W [@cpu-coresolo] et 31 W [@cpu-coreduo], selon la variante). Des mois plus tard, la suite **Core** est devenue le successeur phare de P6 (et NetBurst) et, de manière assez confuse, a atteint les étagères sous la forme de la ligne de CPU **Core 2**. Au fil des années, les microarchitectures suivantes ont amélioré de nombreux aspects, mais elles ont également réussi à réincorporer des éléments oubliés de NetBurst sans répéter les mêmes erreurs.

Sur une note curieuse, je me demande si le nom du cœur Yonah est destiné à être une métaphore pour le Livre de Jonas, en particulier en reliant l'événement où le prophète, à la fin, a réussi à corriger sa voie pour sauver la ville de Ninive. Bien qu'il soit également possible que je surpense cela.

### Architecture de la carte mère

À un moment donné dans l'histoire du PC, les cartes mères ont tellement gagné en complexité que de nouvelles conceptions ont dû être développées à partir de zéro pour répondre efficacement aux besoins émergents.

![Vue d'ensemble de la carte mère Xbox.](cpu/motherboard.png) {.open-float}

Le nouveau standard développé repose sur deux puces dédiées pour gérer la plupart des fonctions de la carte mère. Ces puces sont :

- Le **Northbridge**: Sert de contrôleur de mémoire et interface le GPU.
- Le **Southbridge**: Interface le reste de l'I/O (c'est-à-dire USB, ATA/SATA, PCI, etc)

La combinaison de ces puces est appelée **jeu de puces** et elles sont suffisamment importantes pour conditionner les capacités et les performances d'une carte mère. La Xbox, étant si proche d'un PC, comprend également deux puces : Le **NV2A**, une combinaison de Northbridge et de GPU ; et le **MCPX** qui gère le reste de l'I/O.

{.close-float}

Les deux puces sont interconnectées à l'aide d'un bus spécialisé appelé **HyperTransport**. Il convient de souligner que certaines cartes mères de PC ont également inclus cette technologie, juste avec une marque différente (*nForce MCP-D*).

### Disposition de la mémoire

La Xbox comprend un total de **64 MiB de DDR SDRAM**, ce type de RAM est très rapide par rapport à ce que la concurrence offre. Cependant, elle est également partagée entre tous les composants de ce système. Par conséquent, une fois de plus, nous nous trouvons devant une autre **architecture de mémoire unifiée** ou disposition 'UMA'.

![Représentation du réseau de commutation. Le GPU utilise deux banques tandis que le CPU en utilise une différente, réduisant ainsi la contention dans le processus.](cpu/memory.png)

Nous avons déjà vu à quel point [ce design peut parfois être problématique](playstation-2#preventing-past-mishaps). Néanmoins, les programmes peuvent résoudre ce problème en répartissant leurs données entre différentes banques de mémoire. NV2A implémente un **réseau de commutation** qui permet à différentes unités (CPU, GPU, etc) d'y accéder simultanément \[@cpu-huang\] \[@cpu-informit\].

De plus, la console dispose d'un disque dur interne, et il *se trouve* qu'il est configuré avec trois partitions de 750 MiB chacune réservées pour le stockage temporaire. Le CPU peut décharger certaines de ses données de la RAM principale, puis les télécharger à nouveau lorsque c'est nécessaire. Gardez à l'esprit que c'est un processus manuel et n'implique pas de RAM virtuelle.

## Graphismes

Comme nous l'avons vu précédemment, le processeur graphique réside dans la puce NV2A et, tout comme le MCPX, il est fabriqué par Nvidia.

![Le package Nvidia NV2A sur la carte mère de la Xbox.](gpu.jpg)

Cette entreprise est dans le domaine des graphiques depuis longtemps, leur série *GeForce* est l'une des marques de GPU les plus populaires sur le marché informatique, en concurrence directe avec la série Radeon de ArtX/ATI. Dans l'ensemble, cela offre un bon levier sur la qualité des graphiques trouvés dans la Xbox, compte tenu du fait que c'est la première tentative de Microsoft sur le marché des consoles.

Tout cela semble raisonnable, mais était-ce *vraiment* une décision certaine à prendre à l'époque ? Il est facile de se baser sur l'histoire actuelle pour comprendre pourquoi Microsoft a choisi Nvidia plutôt que d'autres marques populaires de l'époque (3dfx, PowerVR, S3, etc), mais si nous en apprenons davantage sur la compétition de l'époque, le panorama des options le rendait beaucoup plus complexe.

![Halo (2001) fonctionnant sur la Xbox en mode 720p.](halo.png)

Par exemple, la populaire série 'Voodoo 2' de 3dfx détenait environ 70% des parts de marché sur le marché des PC à la fin des années 90 [@graphics-ign], tandis que Nvidia avait du mal à promouvoir l'adoption de la nouvelle 'GeForce 256' (la première de la série GeForce). Après cela, le choix de Microsoft ressemble maintenant plus à un risque qu'à un pari sûr, mais comme nous le savons maintenant, ce risque a fini par payer.

> Nvidia n'était PAS le joueur numéro 1 qu'ils sont maintenant, en 1999. Ils étaient en difficulté. La nouvelle architecture GeForce était encore jeune et beaucoup de gens ne l'aimaient pas. Maintenant, cela semble acquis. Mais il faudrait rechercher cette histoire pour savoir cela, et pourquoi j'ai dû me battre pour l'utiliser. J'avais raison mais je ne savais pas que j'avais raison; j'étais très inquiet.
> 
> -- <cite>Seamus Blackley (Co-auteur de la Xbox originale)</cite>

Dans la section suivante, nous examinerons le fonctionnement interne de cette puce. Maintenant, je crains que nous nous retrouvions mêlés à beaucoup de terminologie et de termes marketing tout comme la section CPU, mais n'ayez pas peur ! Je vais commencer par les bases.

### Architecture et design

Le cœur du GPU trouvé sur le NV2A est basé sur la populaire série de GPUs 'GeForce3' \[@graphics-mslusarz\] \[@graphics-g3arch\], il est également appelé **NV20** dans les documents techniques de Nvidia.

![Conception du pipeline du NV2A.](NV2A_Pipeline.png) {.open-float}

Veuillez noter que, bien que le pipeline du GPU de la Xbox soit basé sur l'architecture NV20, le NV2A a quelques modifications qui ne sont pas compatibles avec le reste de la série NV20 (plus important encore, il a été adapté pour fonctionner dans un environnement UMA).

Les unités analysées contiennent beaucoup plus de caractéristiques qui vont au-delà du cadre de cet article, donc je recommande de consulter les sources/références si cette section retient votre attention. De plus, puisque la terminologie liée aux graphiques évolue constamment (ce qui peut conduire à une certaine confusion), j'ai décidé de me baser sur les termes utilisés par Microsoft/Nvidia pendant les années de la Xbox, alors souvenez-vous de cela si vous prévoyez de lire d'autres articles liés aux graphiques provenant d'autres sources.

{.close-float}

Cela étant dit, jetons un coup d'oeil à comment les images sont dessinées sur la Xbox. Certaines explications sont très similaires à celles du [Flipper](gamecube#graphics) de la GameCube, donc vous pouvez également bénéficier de la lecture de cet article si vous avez du mal à suivre celui-ci.

#### Commandes {.tabs.active}

![Étape des commandes.](pipeline/commands.png) {.tab-float}

Premièrement et avant tout, il faut expliquer comment le GPU peut recevoir des commandes du CPU. Pour cela, le GPU contient un processeur de commandes appelé **PFIFO** qui récupère et traite efficacement les commandes graphiques (appelées **Pushbuffer**) de manière FIFO, les commandes déballées sont ensuite livrées au **PGRAPH** (le bloc en charge du traitement graphique) et à d'autres moteurs.

Comme Flipper, la géométrie n'a pas à être intégrée dans la commande. PGRAPH propose de nombreuses façons de soumettre des données graphiques. Par exemple, le CPU peut allouer un tampon en RAM contenant des données de sommet et ensuite instruire le GPU pour qu'il les récupère à partir de cet emplacement. Cette approche peut être efficace, car elle évite d'envoyer de la géométrie dupliquée.

Les explications suivantes se produisent dans PGRAPH.

#### Sommet {.tab}

![Étape des sommets.](pipeline/vertex.png) {.tab-float}

C'est une section intéressante pour ce GPU en particulier. À ce stade, le GPU offre la possibilité d'appliquer des transformations de sommets à notre géométrie. Nous avons déjà vu cette fonctionnalité avec Flipper, mais contrairement à ce GPU, celui-ci utilise un **moteur programmable**. Cela signifie que les développeurs peuvent spécifier quelles opérations de sommet sont effectuées et comment, au lieu de compter sur un programme pré-défini. Bien que, le NV2A peut également fonctionner en mode 'fixe', si nécessaire.

Cette étape est gérée par une **Unité Vertex** et le NV2A dispose de **deux d'entre elles**. Chacun peut charger un programme contenant jusqu'à 136 instructions (aussi appelé **microcode**). Ce programme est appelé **programme de sommet** et il est chargé à l'exécution. Un programme de sommet peut effectuer les opérations suivantes [@graphics-koppelman]:

- **Opérations arithmétiques** (c'est-à-dire addition, multiplication, minimum, etc).
  - Cela comprend des 'fonctions d'aide' pour aider les tâches liées aux graphiques, comme le produit scalaire.
- **Vertex Swizzling** (Réarrangement et/ou duplication).

En un mot, l'unité de sommet traite les sommets en les manipulant dans ses registres. En d'autres termes, une fois le programme chargé, 16 registres en lecture seule (appelés 'registres d'entrée') sont initialisés avec les attributs d'un sommet (chaque vecteur contient quatre composants). Ensuite, l'unité effectue l'ensemble des opérations (instructées par le programme) en utilisant les registres d'entrée. De plus, 12 registres inscriptibles et jusqu'à 196 constantes sont fournis pour aider le calcul. Enfin, le sommet résultant est stocké dans un autre bloc de 11 registres inscriptibles (chacun limité à un objectif spécifique), qui sont transmis à l'étape suivante. Ce processus est répété pour chaque sommet reçu.

#### Pixel {.tab}

![Étape Fragment/Pixel.](pipeline/pixel.png) {.tab-float}

À cette étape, les sommets sont transformés en pixels. Le processus commence avec un rasteriseur qui génère des pixels pour dessiner chaque triangle. Le rasteriseur de la NV2A peut générer quatre pixels par cycle.<!-- Nvidia designed a memory system called 'Lightspeed Memory Architecture' which, among other things, compresses the Z-buffer to four times its original size which enables to increase bandwidth (since it has to be accessed from main memory, while \[competitors embedded it\]()). -->Ensuite, **4 shaders de texture** sont utilisés pour récupérer les textures de la mémoire [@graphics-domine], ceux-ci offrent également d'appliquer automatiquement un filtrage anisotrope, mipmapping et **tampon d'ombre**. Ce dernier est utilisé pour tester si un pixel est visible ou éclipsé par la source d'éclairage, afin que la bonne couleur puisse être appliquée. À ce stade, le GPU offre également de réaliser un découpage et un [Z-test](nintendo-64#modern-visible-surface-determination) précoce (le NV2A compresse le Z-buffer quatre fois sa taille originale pour économiser de la bande passante, contribuant à de nombreuses améliorations de performance).

Les pixels résultants sont stockés dans un **ensemble de registres partagés** puis cyclés à travers **8 combinateurs de registres**, où chacun applique des opérations arithmétiques sur eux. Ce processus est **programmable** à l'aide de **shaders de pixels** (un autre type de programme exécuté par le GPU) [@graphics-wasson]. À chaque cycle, chaque combinateur reçoit des valeurs RGBA (RGB + Alpha) de l'ensemble de registres [@graphics-spitzer]. Ensuite, en fonction de l'opération définie par le shader, il va opérer les valeurs et écrire le résultat. Enfin, un plus grand nombre de valeurs sont envoyées au **combinateur final** qui peut exclusivement mélanger des couleurs spéculaires et/ou du brouillard.

Les combinateurs de registres sont programmables de manière similaire à l'[Unité d'Environnement de Texture](gamecube#tab-1-3-texture). C'est-à-dire, en modifiant ses registres avec une combinaison spécifique de paramètres. Dans le cas de la Xbox, le PFIFO lit les pushbuffers pour configurer le PGRAPH, qui comprend les combinateurs de registres et les shaders de texture.

#### Post-traitement {.tab}

![Étape de post-traitement.](pipeline/postprocessing.png) {.tab-float}

Avant que les pixels ne soient écrits dans le tampon d'image, le NV2A contient quatre moteurs dédiés appelés **Unité de Sortie Raster** ou 'ROP' qui effectuent les tests nécessaires (alpha, profondeur et stencil) en utilisant des blocs alloués dans la mémoire principale. Enfin, des lots de pixels (quatre par cycle) ne sont réécrits que s'ils passent ces tests.

De plus, le tampon de trame peut être anti-crénelé en utilisant une technique appelée **échantillonnage multiple** [@graphics-geforce3]. Essentiellement, cette technique échantillonne les bords des polygones plusieurs fois avec différents décalages ajoutés dans le processus. Ensuite, tous les échantillons sont moyennés pour former l'image anti-aliasée. Cette approche a remplacé la fonction d'anti-aliasing précédente (et plus gourmande en ressources) appelée 'supersampling', utilisée par les précédents GPU Nvidia.

### L'importance de la programmabilité {.tabs-close}

Je trouve essentiel de souligner l'importance du nouveau modèle de programmabilité que Nvidia a fourni aux développeurs. Il y a des années, la plupart du pipeline graphique était calculée par le CPU, laissant le GPU accélérer les opérations de rasterisation. Avec l'introduction des 'shaders' (se référant à la fois aux shaders de pixels et aux programmes de sommets), les programmeurs peuvent tirer parti des ressources du GPU pour accélérer de nombreux calculs dans le pipeline, déchargeant une grande quantité de travail du CPU.

Le concept de 'shaders' a été introduit par **Pixar** en 1989 comme une méthode pour étendre **Renderman** [@graphics-pixar], leur logiciel pionnier utilisé pour le rendu 3D. C'était à l'époque où les graphiques 3D étaient principalement gérés par du matériel industriel. Plus tard, nous avons vu comment certaines consoles ont incorporé [des principes similaires](nintendo-64#tab-1-1-reality-signal-processor), mais ce n'est que lorsque Nvidia a lancé sa ligne GeForce3 que les shaders sont devenus une norme sur le marché grand public.

::: {.subfigures .open-float .tab-float .tabs-nested}

![Animation complexe réalisée par programme de sommet.](vertex){.active video="true" title="Programme de Sommet"}

![Différents effets de texture réalisés par des shaders de pixels.](fragment){video="true" title="Shader de Pixel"}

ChameleonMark (2002), une démo développée par Nvidia pour montrer les shaders de GeForce3.

:::

Grâce aux programmes de sommets, le GPU peut maintenant accélérer les transformations de modèles, les calculs d'éclairage et la génération de coordonnées de texture. Ce dernier est essentiel pour composer [des surfaces d'ordre supérieur](playstation-2#infinite-worlds). Avec cela, le CPU peut se concentrer sur l'amélioration de la physique, de l'IA et de la gestion de scène.

Dans le cas des pixel shaders, les programmeurs peuvent manipuler et mélanger les textures de plusieurs façons pour obtenir différents effets tels que le multi-texturing, le mappage spéculaire, le mappage des bosses, le mappage de l'environnement, etc.

Un nouveau concept de programmation qui émerge grâce à cette approche est le **GPU à usage général** ou 'GPGPU', qui consiste à assigner des tâches au GPU qui auraient été exclusivement réalisées par le CPU. Ainsi, le GPU a non seulement pris en charge la majeure partie du pipeline graphique, mais peut maintenant agir comme un coprocesseur efficace pour des calculs spécialisés (c'est-à-dire des calculs physiques). C'est un nouveau domaine qui évoluera à mesure que les GPU deviendront plus puissants et flexibles. Cependant, le NV2A était déjà capable d'obtenir cela grâce à une combinaison de capacités matérielles (vertex & pixel shaders) et d'API spécialisées développées (programmes d'état d'OpenGL).

{.close-float}

J'ai le sentiment que les shaders seront régulièrement revisités dans les futurs articles. Veuillez vous rappeler que dans cet article, cependant, ils peuvent être considérés comme un peu 'primitifs' et certaines personnes peuvent soutenir que les shaders de pixels ne sont même pas des 'shaders' (comparés à ce que les GPU offrent de nos jours).

### Le cadre de la Xbox

La résolution standard des jeux est de **640x480**, c'est à peu près la norme dans la sixième génération. Cependant, cette contrainte n'est qu'un nombre : Le GPU peut dessiner des tampons de trame allant jusqu'à 4096x4096, mais cela ne signifie pas que le matériel fournirait des performances acceptables. D'un autre côté, la console permet de configurer ses réglages d'écran globalement, ce qui peut aider à promouvoir des fonctionnalités pionnières (c'est-à-dire l'écran large et la 'haute résolution') au lieu d'attendre que les développeurs les découvrent (comme cela s'est passé avec le [Gamecube/Wii](wii#tab-2-1-standardised-widescreen)).

L'encodeur vidéo, quant à lui, essaiera de diffuser tout ce qui se trouve sur le tampon de trame dans un format que votre téléviseur comprendra. Cela signifie que les images en format large deviendront [anamorphiques](wii#tab-2-1-standardised-widescreen) à moins que le jeu ne soit diffusé en HD (c'est-à-dire en 720p ou 1080i, ce que seuls quelques jeux font).

Cela étant dit, quel genre de signaux cette console diffuse-t-elle ? Assez beaucoup. Outre le composite PAL/NTSC typique, la Xbox propose **YPbPr** (nécessitant un accessoire supplémentaire pour obtenir les connecteurs 'composants') et RGB (compatible SCART et VGA). En somme, très pratique sans nécessiter d'adaptateurs coûteux et autres.

## Audio

Le sous-système audio de cette console a été fortement influencé par la technologie des équipements audio professionnels et des cartes mères ATX, bien qu'il contienne quelques pièces particulières. Le MCPX comprend deux composants audio, l'**Unité de Traitement Audio** et l'**Interface de Contrôle Audio'**.

L'unité de traitement audio ou 'APU' est le processeur audio dédié et est composée de trois sous-composants :

- Le **Processeur de Voix** ou 'VP' : Un circuit spécialisé qui peut synthétiser 256 voix à un taux d'échantillonnage de 48 kHz. Il comprend également deux contrôles d'enveloppe DAHDSR et divers filtres. De plus, 64 voix peuvent être en 3D (par opposition à Stéréo ou Mono). À la fin, le processeur mixe et sort les voix à travers 32 canaux, où des groupes de 8 canaux ont leur propre contrôle de volume.
- Le **Processeur Global** ou 'GP': un DSP programmable utilisé pour traiter les données audio du VP et appliquer divers effets dessus.
  - Tous les canaux provenant du VP n'ont pas à être alimentés ici, au lieu de cela, des groupes spécifiques peuvent être envoyés pour appliquer des effets spécifiques (c'est-à-dire réverbération) exclusivement sur eux.
- Le **Processeur d'encodage** ou 'EP': Comme son nom l'indique, il génère le signal stéréo final provenant du GP et le stocke dans la RAM principale.
  - Notez que les opérations de l'EP peuvent également être effectuées par le GP, mais avoir un composant séparé pour l'encodage permet à la console de viser de meilleures techniques d'encodage audio, comme le Dolby Digital.

L'APU ne traite que les données audio mais ne peut pas les émettre. Ce dernier est le travail de l'ACI, qui lit les données audio en RAM, les décode et envoie l'échantillon stéréo PCM résultant (résolution de 16 bits avec une fréquence d'échantillonnage de 48 kHz) par la sortie audio.

## E/S

Comme je l'ai mentionné précédemment, nous avons un sous-système 'Southbridge' qui concentre tous les accès I/O. Ce Southbridge est implémenté par la puce MCPX.

![Diagramme principal de l'architecture de la console. Remarquez comment le MCPX contrôle la plupart des E/S.](diagram.png)

Incidentalement, le MCPX dérive de son homologue PC appelé **nVidia nForce Multimedia and Communications Processor** ou 'MCP'. On le trouve sur les cartes mères utilisant le chipset nForce 220/415/420 [@io-reactos].

### Interfaces externes

La console comprend les connecteurs externes suivants :

- **4 ports USB 1.1**: Utilisés pour connecter les contrôleurs, cependant, la forme externe du port est modifiée pour ne permettre que les contrôleurs Xbox.
  - Ces ports contiennent également une broche supplémentaire appelée 'Video Sync' pour connecter des périphériques qui interagissent avec l'écran.
- **Port Ethernet 10/100BASE-TX**: Utilisé pour les services en ligne (plus de détails plus tard). La fonction Ethernet réelle est réalisée par un transceiver séparé trouvé sur la carte mère.

### Interfaces internes

Le MCPX fournit également les interfaces et protocoles suivants utilisés pour interconnecter différents sous-systèmes :

- **SMBus**: Également appelé I²C, c'est une interface série qui connecte ces composants :
  - **Contrôleur de gestion du système** ou 'SMC': Gère plusieurs services, tels que l'alimentation, la température et le contrôle du ventilateur. C'est en fait un microcontrôleur PIC16LC.
  - **Moniteur de température du système** ou 'STM': Un thermomètre numérique (ADM1032) utilisé par le SMC pour détecter la surchauffe.
  - Une **EEPROM de 256 B** : Une ROM réinscriptible qui stocke des identifiants uniques (numéro de série, région, adresse MAC Ethernet, etc).
  - **Encodeur vidéo**: L'encodeur est principalement connecté au GPU mais contrôlé via le SMBus.
- **Contrôleur IDE**: Il s'agit d'un protocole standard largement utilisé sur les PC pour communiquer avec les disques durs, les lecteurs optiques, etc. Un seul câble ruban large est utilisé pour connecter la carte mère au lecteur de DVD et au disque dur.
- **Low Pin Count** ou bus 'LPC' : Il s'agit d'une autre interface empruntée au PC, mais au lieu de connecter le *bon vieux* BIOS du PC, il communique avec une **Flash ROM**, qui à son tour stocke l'équivalent d'un BIOS. La Flash est **1 MiB** de taille.

### Le contrôleur

La Xbox est livrée avec une manette volumineuse appelée **The Duke**, son ensemble d'entrées n'est pas différent de ce que les autres concurrents avaient... sauf pour l'utilisation de circuits analogiques (8 bits de large) sur les boutons de face, permettant aux jeux de détecter des 'demi-pressions' de la plupart des boutons. D'un autre côté, le Duke a été tellement critiqué que Microsoft l'a remplacé par une nouvelle révision appelée **Controller S** quelques mois après la sortie de la console.

::: {.subfigures .toleft .tabs-nested}

![](controller/duke_front.png){.active title="Avant"}

![](controller/duke_back.png){title="Arrière"}

Le Duke (2001) [@photography-amos].

:::

::: {.subfigures .toright .tabs-nested}

![](controller/s_front.png){.active title="Avant"}

![](controller/s_back.png){title="Arrière"}

Controller S (2002) [@photography-amos].

:::

En y regardant de plus près, les deux contrôleurs comprenaient quelque chose de spécial : Deux emplacements pour **Unité de Mémoire** pour brancher une carte mémoire propriétaire, permettant de partager des sauvegardes entre les consoles. En réalisant cela, j'ai immédiatement supposé que cette fonctionnalité avait été héritée d'un [concurrent précédent](dreamcast#interactive-memory-card). Cependant, quelques jours après la publication de cet article, je l'ai envoyé à Seamus Blackley, le co-créateur de cette console, qui a rapidement répondu avec des commentaires très intéressants. Concernant les similitudes avec la Dreamcast, il m'a dit :

> Le lien avec Dreamcast n'est qu'un biais historique. C'était accidentel.
> 
> -- <cite>Seamus Blackley</cite>

### Un adaptateur requis

Un autre fait intéressant à mentionner à propos de ces contrôleurs est qu'ils ne peuvent être branchés qu'à l'aide d'un adaptateur de forme appelé **dongle de rupture**, cela a été conçu dans un effort pour prévenir les accidents lorsque quelqu'un trébuche dessus.

> Je pense qu'ils ont également prévu le cas où une Xbox serait sur une étagère haute et où tirer sur le contrôleur ferait tomber toute la console lourde sur la tête d'un enfant.
> 
> -- <cite>Utilisateur de Xbox</cite>

## Système d'exploitation

D'accord, commençons par aborder le sujet qui fâche.

> Est-ce que ça fonctionne avec **Windows**?

J'ai peur que ce soit une réponse *oui et non* : Il y a un 'Windows' présent dans cette console, mais pas sous la forme que les utilisateurs de PC conventionnels attendraient.

Pour commencer, le système d'exploitation de la Xbox est composé d'un **Kernel** et **d'applications utilisateur** (c'est-à-dire le tableau de bord). Ces éléments sont stockés dans la Flash ROM de 1 MiB et le disque dur, respectivement.

Le Kernel emprunte une base de code significative au **noyau de Windows 2000** \[@operating_system-os\] (qui, à son tour, est basé sur l'architecture moderne **Windows NT**). Le résultat est un noyau Windows 2000 dépouillé qui n'intègre que les composants nécessaires pour le matériel Xbox. Ces éléments sont finalement compressés et emballés dans un seul exécutable [@operating_system-kernel] pour une efficacité mémoire optimale. En somme, vous pouvez le considérer comme une machine Windows hautement optimisée conçue exclusivement pour le jeu.

Il vaut la peine de souligner que le projet Xbox était dirigé par l'équipe **DirectX** [@operating_system-renegades]. Ainsi, son origine n'a rien à voir avec l'équipe de Windows CE [qui a apporté ses APIs à la Dreamcast](dreamcast#windows-ce).

### Processus de démarrage

Comme avec les machines Pentium, lors du démarrage du système, le CPU commencera à exécuter les instructions trouvées à la **vector de réinitialisation** (adresse `0xFFFF.FFF0`). Pour la Xbox, cette adresse pointe vers une ROM cachée trouvée dans le MCPX (plus de détails sont expliqués dans la section 'Anti-Piratage et Homebrew'). Le MCPX contient des routines pour mettre en place le système de sécurité et continuer à démarrer à partir de la ROM Flash à la place. Dans la ROM Flash, la Xbox initialisera son matériel, démarrera le Kernel et montrera l'animation de démarrage.

Pendant l'initialisation de la sécurité, le CPU passe en **mode protégé**. C'est crucial car les processeurs x86 démarrent toujours en **mode réel** pour maintenir la compatibilité avec le premier processeur (l'Intel 8086), cependant, si les programmeurs ont besoin d'accéder aux fonctionnalités modernes du CPU (comme la possibilité d'accéder à plus de 1 MiB de mémoire), ils doivent les activer manuellement en passant en mode protégé.

Lorsque le Kernel se charge, il injecte du microcode dans le CPU (non pas pour le programmer, mais plutôt pour le *mettre à jour*). Enfin, le Kernel cherche la présence d'un disque DVD valide. S'il y en a un, il l'exécute. Sinon, il chargera une coquille interactive utilisateur stockée dans le disque dur.

### L'écran vert

Jetons maintenant un coup d'œil au programme que la Xbox charge lorsqu'il n'y a pas de disque de jeu inséré : Le **Tableau de bord**.

#### Interface système {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Écran d'accueil.](shell/home.png){.active title="Accueil"}

![Écran de paramètres.](shell/settings.png){title="Paramètres"}

Le tableau de bord offre plusieurs services.

:::

Le tableau de bord n'est pas très différent en termes de fonctionnalités par rapport au [menu PlayStation](playstation-2#interactive-shell), ou à l'[IPL de la GameCube](gamecube#splash-and-shell). Il comprend essentiellement toutes les fonctions que les utilisateurs typiques attendent, comme pouvoir modifier certains paramètres, déplacer des sauvegardes, lire des films DVD ou de l'audio CD ; et ainsi de suite.

Il convient de mentionner que le tableau de bord permet également de copier de la musique à partir d'un CD audio et de la stocker sur le disque dur. Cette musique peut ensuite être récupérée à partir de n'importe quel jeu pour 'personnaliser' sa bande sonore. *Loin de là!*

#### Mise à jour {.tab}

Eh bien oui, c'est la première console de sa génération à être *officiellement* mise à jour, mais seuls les contenus du HDD sont inscriptibles. La partie Kernel (dans la ROM Flash) ne peut pas être réécrite, mais le système peut la corriger après qu'elle a été chargée en mémoire. Les correctifs du noyau sont donc stockés dans le HDD.

Les mises à jour sont distribuées par le biais de jeux de détail et/ou téléchargées par le système après connexion au service réseau Xbox Live (plus d'informations à ce sujet plus tard).

Certaines mises à jour ont amélioré la fonctionnalité du système. Par exemple, les premières unités Xbox n'incluaient pas Xbox Live, donc les jeux ont intégré les fichiers nécessaires sous la forme d'une mise à jour pour installer ce service. D'autres mises à jour se sont concentrées sur la correction des vulnérabilités de sécurité.

## Jeux {.tabs-close}

Cette partie peut être un peu déroutante, principalement en raison du manque de documentation de bas niveau par rapport à d'autres consoles. Il semblait que Microsoft/Nvidia voulait vraiment que les développeurs utilisent leurs bibliothèques et oublient tout le reste. C'est néanmoins une stratégie intéressante.

Dans tous les cas, j'ai essayé de le documenter de manière aussi informative que possible, sans entrer trop en profondeur.

### L'écosystème de développement

Le développement de jeux sur cette console est très complexe en termes de bibliothèques, de terminologie, etc. J'ai donc séparé cela par cadres.

#### Abstraction Matérielle {.tabs.active}

![Représentation de HAL.](hal/general.png) {.tab-float}

Nous avons vu comment des éléments comme un 'coprocesseur programmable avec microcode' ont tendance à venir avec beaucoup de fanfare au début, mais se dissipent lentement une fois que les développeurs découvrent la véritable complexité du nouveau matériel.

Du point de vue du développeur, je pense que l'un des points de vente de cette console était l'inclusion de bibliothèques de haut niveau populaires qui pouvaient gérer efficacement les fonctionnalités de bas niveau. La stratégie de Microsoft et Nvidia consistait à, au lieu de documenter chaque aspect de bas niveau de leur matériel, ils ont simplement fourni une bibliothèque d'abstraction matérielle entièrement fonctionnelle qui pouvait effectuer les opérations que les développeurs s'attendraient à trouver sans nécessiter une connaissance complète du fonctionnement interne du matériel.

Il existe plusieurs SDK disponibles pour développer pour cette console, certains 'officiels' et restrictifs, tandis que d'autres 'pas si officiels' mais flexibles. Nous allons jeter un coup d'œil aux plus populaires.

#### Microsoft XDK {.tab}

![Structure XDK](hal/xdk.png) {.tab-float}

Le Kit de Développement Xbox de Microsoft ou 'XDK' est le SDK officiel utilisé pour le développement Xbox. C'est un package qui contient de nombreux outils, bibliothèques et compilateurs. Plus notablement, il est utilisé aux côtés de Visual Studio .NET (version 2002) qui était *un IDE assez* de ce temps, pour le meilleur ou pour le pire.

La bibliothèque graphique est une implémentation personnalisée de **Direct3D 8.0**, qui a été étendue pour inclure des fonctionnalités spécifiques à Xbox. Direct3D était une API très puissante utilisée pour développer du code compatible avec de nombreux GPU. Avec cela, les développeurs de PC ont l'opportunité de porter leurs jeux PC sans trop de modifications (en théorie!). Cependant, la syntaxe utilisée pour les programmes de sommet et les shaders de pixel ressemble carrément à l'assemblage.

En dehors de cela, la fonctionnalité audio est fournie par **DirectSound** qui s'occupe de l'APU sans avoir à se soucier de déplacer les données audio. Il y a aussi quelques bibliothèques réseau, qui sont très pratiques pour obtenir une fonctionnalité en ligne (c'est-à-dire multijoueur). L'API réseau repose sur **Windows Sockets**, qui était la tentative de Microsoft de simplifier les communications réseau en utilisant un protocole uniquement Windows (bien qu'ayant un design basé sur son homologue standardisé, les sockets BSD).

Pour essayer tout cela, Microsoft a distribué son propre kit de développement matériel qui n'est qu'une unité de vente teintée en vert avec le double de RAM installée (128 MiB au total) et, parfois, une puce MCPX différente (appelée MCPX-2, qui démarre directement à partir de la ROM Flash). Il contient une version améliorée du tableau de bord qui lance des exécutables signés par le XDK. De l'autre côté, le **Xbox Neighbourhood** (une application Windows) a été utilisé pour relier le kit de développement à Visual Studio, permettant un déploiement et un débogage faciles. Pour expérimenter avec Xbox Live, Microsoft a également fourni des serveurs sandboxés.

#### NXDK {.tab}

![Structure NXDK. Remarquez que même si certaines bibliothèques de bas niveau sont enveloppées avec des bibliothèques de haut niveau, elles sont toujours accessibles au développeur](hal/nxdk.png) {.tab-float}

Pour éviter les litiges sur le droit d'auteur aux développeurs de Homebrew utilisant le SDK officiel, un groupe de développeurs non affiliés à Microsoft a créé un remplacement du SDK officiel appelé **Open XDK**. Après quelques années, son développement a cessé, donc un autre groupe l'a repris à partir de là et a nommé la nouvelle fourche **New XDK** ou 'nxdk'.

Le projet en cours a rétro-conçu les composants matériels de bas niveau (microcode, tampons de poussée, etc) et a conçu une nouvelle API en C/C++ qui fournit des appels de haut niveau à chaque partie du système.

Pour simplifier la couche graphique sans dépendre de Direct3D, nxdk utilise le **compilateur CG** [@games-nxdk]. CG est un langage créé par Nvidia pour développer des shaders. CG est enchaîné avec d'autres compilateurs pour générer du code compatible Xbox. Lors de la compilation, le code CG est converti en assemblage compatible NV20, puis, cela est à nouveau traduit en utilisant un compilateur personnalisé pour générer du microcode compatible NV2A et pushbuffer. Pour ceux qui ne veulent pas utiliser CG, nxdk expose également le reste des compilateurs pour écrire des shaders de bas niveau.

Le reste des API disponibles gère d'autres services (audio, réseau, etc). En somme, cette bibliothèque donne plus de contrôle sur le matériel (contrairement au SDK officiel) en échange de l'absence d'utilisation des API de Microsoft (la norme de facto, bien que totalement propriétaire). Cependant, nxdk reste l'option la plus adéquate pour développer des programmes Homebrew légaux.

### Support {.tabs-close}

Les jeux sont distribués sur des disques DVD à double couche (jusqu'à 8,5 Go !), et ils sont ensuite lus par un lecteur de DVD personnalisé qui comprend des protections anti-piratage (malgré l'utilisation d'une interface standard, ATA). Il convient de mentionner que le XDK incluait certains outils pour personnaliser la disposition des données dans le disque, permettant aux programmeurs d'améliorer les vitesses de lecture !

Maintenant, la console comprend également un disque dur interne de 8 Go, les jeux l'utilisent pour stocker des sauvegardes ou mettre en cache du contenu temporaire. Le système, quant à lui, stocke le tableau de bord, les paramètres Xbox Live et les paramètres réseau.

### Les services réseaux

Oubliez les [modems](dreamcast#online-platform) ou les [services expérimentaux](playstation-2#network-service). La Xbox incluait tout ce que nous tenons pour acquis aujourd'hui pour fournir un service en ligne décent : une connexion Ethernet et une infrastructure en ligne centralisée (appelée **Xbox Live**).

![Logo Xbox Live.](shell/live.png) {.open-float}

De plus, Xbox Live ne permettait pas seulement le multijoueur en ligne, mais incluait également d'autres fonctionnalités comme le streaming audio pour le chat vocal en temps réel.

Mais qu'est-ce que Xbox Live exactement ? Eh bien, ce n'est qu'une collection de services en ligne interconnectés que les entreprises peuvent utiliser pour construire leur plateforme en ligne. Par exemple, l'un des services fournit des profils d'utilisateurs, donc les studios peuvent l'utiliser comme méthode d'authentification lors de l'accès aux fonctionnalités en ligne d'un jeu. Dans le SDK officiel, Microsoft inclut certaines API pour communiquer avec les serveurs Xbox Live.

{.close-float}

Il est important de souligner que Microsoft contrôle à qui accorder l'accès à Xbox Live, donc les développeurs devront s'inscrire auprès de Microsoft pour obtenir les clés d'authentification qui seront utilisées par leurs jeux.

La véritable expérience en ligne se produit dans le **serveur de titre**, qui est le type de serveur qui répond aux clients (consoles Xbox) dans le monde entier et gère la communication en temps réel. Microsoft a inclus dans leur SDK quelques exemples pour montrer comment construire ces serveurs, bien qu'ils reposaient sur des systèmes Windows et étaient destinés à être déployés dans des centres de données exécutant Windows Server.

### Le début d'une nouvelle tendance

Après avoir analysé l'implémentation de Xbox Live par Microsoft et regardé l'impact qu'elle a eu sur l'industrie. Cela semble maintenant assez évident, n'est-ce pas ? Comme si la recette pour un 'jeu en ligne correct' (a.k.a Ethernet + Infrastructure) était toujours là, mais toutes les entreprises ne voulaient pas y investir.

Il s'avère que ce n'est pas si simple : Microsoft a également dû convaincre les utilisateurs qu'ils 'avaient besoin' de cette fonctionnalité, que le multijoueur en ligne n'était pas seulement un ajout optionnel, mais une partie fondamentale de certains jeux. Sinon, les efforts de Microsoft ne représenteraient qu'une autre 'tentative en ligne'.

> Essayez d'imaginer un monde où aucun joueur de console ne voulait de jeux en ligne, et où personne ne croyait qu'une architecture de PC pouvait être une console. C'était vraiment comme ça. Maintenant, cela semble évident MAIS CE N'ÉTAIT PAS LE CAS.
> 
> -- <cite>Seamus Blackley</cite>

## Anti-Piratage et Homebrew

Indépendamment du fait que cette console contient des composants standard ou non, un certain nombre de mesures de sécurité sont mises en œuvre.

Veuillez noter que le chiffrement RSA est un sujet récurrent ici, je l'ai déjà introduit dans l'article sur la [Wii](wii#tab-7-2-chain-of-trust), donc si vous n'êtes pas familier avec le RSA ou tout autre système de chiffrement symétrique/asymétrique, veuillez d'abord consulter cet article.

Cela étant dit, jetons un coup d'œil.

### Protection contre la copie de DVD

Les disques de jeu sont protégés à la fois logiquement et physiquement pour empêcher la duplication non autorisée (ce ne devrait pas être une surprise à présent).

Du côté logique, les disques de jeu comprennent quelques 'pièges' pour tromper les lecteurs de DVD conventionnels afin qu'ils ne puissent pas trouver le contenu du jeu réel. Par exemple, la première zone du disque est formatée comme un DVD conventionnel, et à l'intérieur de cette section, il y a un message d'avertissement qui se joue s'il est inséré dans un système non Xbox. En réalité, les données de jeu se trouvent sur une deuxième partition, mais les métadonnées nécessaires pour trouver cette partition ne sont pas encodées dans un format que les lecteurs de DVD conventionnels attendent, donc seul un lecteur spécialisé (d'où celui de la Xbox) trouvera le jeu.

Du côté physique, j'ai peur qu'à cette époque, on ne sache pas encore publiquement quelles données le pilote et le disque échangent pour effectuer la validation. Le disque contient un anneau interne inaccessible (par les lecteurs conventionnels) qui stocke des identifiants uniques, mais on ne sait pas comment ces données sont utilisées.

### Protection du système

Passons en revue le reste des mesures de sécurité que ce système a initialement mises en œuvre.

#### Introduction {.tabs.active}

La ROM Flash et l'EEPROM contenant le 'BIOS' et les données sensibles, respectivement, sont cryptées à l'aide d'une **clé RC-4**. Après que le Kernel a été démarré à partir du BIOS, il ne lancera que des exécutables signés par Microsoft en utilisant le **chiffrement RSA**.

Le disque dur, de l'autre côté, est formaté avec un système de fichiers complètement propriétaire et non documenté appelé **FATX**.

Ceci était une brève introduction à la chaîne de confiance que Microsoft a mise en place. Cela semble plutôt simple, non ? Eh bien, **quelque chose est étrange** : L'exécution est contrôlée par le CPU, mais c'est une puce standard, alors comment comprend-elle les données cryptées avec RC-4 ? La réponse est que **elle ne comprend pas**, donc quelque part dans cette console se trouve un morceau de code non crypté qui met en place la première étape du cryptage. Ce code, en particulier, a été la cible de la plupart des pirates qui se sont efforcés de craquer cette console après son lancement.

#### Recherche de démarrage {.tab}

Comme il est officiellement documenté par Intel que leurs processeurs commencent l'exécution à l'adresse `0xFFFF.FFF0`, les hackers se sont concentrés sur la recherche de ce code dans la ROM Flash (qui à son tour mènera à la clé RC-4). C'est ce qu'a tenté de faire un hacker et chercheur populaire nommé **Andrew 'bunnie' Huang** lors de ses recherches académiques. Les résultats étaient assez intéressants : Les 512 octets supérieurs de la ROM Flash contiennent des routines de sécurité, y compris la clé, mais cela ne fonctionne pas pour le système de vente au détail [@cpu-huang]. Je suppose que Microsoft a peut-être laissé ce code des unités de prototype/debug (peut-être par accident, car ce bloc expose les algorithmes que Microsoft a appliqués). En conclusion, cela a été considéré comme *code indésirable* donc bunnie a réalisé que le vrai `0xFFFF.FFF0` n'était pas dans la ROM Flash.

Pour faire court, `0xFFFF.FFF0` était **caché dans la puce MCPX** : Elle contenait une ROM cachée de 512 B qui serait exécutée une fois et cachée par la suite. Cette ROM n'était pas facile à extraire, donc bunnie a eu recours à la capture du bus HyperTransport pour attraper la clé RC-4 une fois qu'elle était transmise.

Et c'est ainsi que cela s'est passé, bunnie a publié la clé dans le cadre de ses recherches et Microsoft n'était pas content de cela. Ainsi, la communauté des hackers a trouvé un moyen de prendre le contrôle de la première couche de sécurité de cette console, qui comprenait le Kernel.

#### Déblocage permanent {.tab}

Casser la couche RSA n'était jamais censé être facile, mais puisque les hackers ont eu accès au Kernel, il serait désormais possible de le rétroconcevoir et de développer des correctifs qui pourraient annuler le RSA. Et voilà, cela a finalement eu lieu et a ouvert la porte à la communauté du Homebrew. Tout le monde pouvait désormais développer des programmes qui pouvaient être exécutés sur une Xbox modifiée sans l'approbation de Microsoft. Certains groupes ont développé des remplacements pour le tableau de bord original qui pouvaient faire plus de fonctions, comme exécuter Linux !

Cependant, cela nécessitait que les utilisateurs modifient leur BIOS en utilisant du matériel spécialisé, ce que tout le monde ne pouvait pas faire. Dans les années suivantes, de nouvelles failles ont été découvertes qui pouvaient facilement amorcer le piratage principal, l'une d'elles consistait à insérer une sauvegarde forgée de '007 : Agent Under Fire' ou 'Splinter Cell' qui générerait un débordement de tampon pour démarrer un outil homebrew qui pourrait installer une faille permanente dans le disque dur. L' 'exploit permanent' était possible parce que le Dashboard original était sujet à un autre débordement de tampon en utilisant un fichier de police forgé \[@anti_piracy-pheonix\] (notez que les polices n'avaient pas besoin d'être signées).

#### Modchips {.tab}

Du côté de la piraterie, des **Modchips** sont également apparus : Au lieu de manipuler le lecteur DVD, les modchips chevauchent la ROM Flash en exploitant le fait que la ROM MCPX peut démarrer un BIOS secondaire si elle est trouvée sur un port LPC inutilisé.

Le BIOS de substitution contenait des routines patchées qui pouvaient permettre la lecture de n'importe quel type de disque de jeu (surtout les disques conventionnels).

#### La réponse {.tab}

Microsoft a publié de nombreuses révisions de matériel réduisant la quantité d'exposition dans leurs électroniques et économisant les coûts. Pendant ce temps, des mises à jour logicielles ont été publiées qui mettaient à jour à la fois le noyau et le tableau de bord dans le but de réduire le nombre de vulnérabilités. Cependant, certaines exploitations de jeux ont réussi à persister et ce qui restait était un autre jeu du 'chat et de la souris'.

Un autre point à mentionner est que Xbox Live était en lui-même un mécanisme de prévention efficace contre le piratage. Microsoft a pu bloquer Xbox Live à ces consoles avec des modifications non autorisées, ce qui était un compromis que les utilisateurs typiques devaient considérer avant de pirater leurs consoles avec le seul but de jouer à des copies piratées.

## C'est tout pour le moment {.tabs-close}

Après quelques mois avec des délais et des examens au milieu, le prochain article a enfin été terminé. J'admets que celui-ci continue la tendance à ajouter *trop* d'informations et de trivia, mais alors que cette recherche a commencé par de petits pas (et frustrants), je suis content d'avoir trouvé beaucoup de soutien d'une communauté spéciale, XboxDev, qui m'a aidé à rassembler beaucoup d'informations.

Pour ceux qui aimeraient en savoir plus sur cette console, XboxDev travaille activement sur nxdk (ainsi que sur différents émulateurs) qui s'efforce de faire des choses qui étaient auparavant considérées comme impossibles dans le homebrew Xbox, donc je suggère de visiter leur communauté pour plus d'informations.

De mon côté, je vais prendre quelques jours pour réfléchir attentivement au *prochain article* (et potentiellement revenir quelques générations en arrière pour analyser une console que j'ai oubliée).

À la prochaine !  
Rodrigo
