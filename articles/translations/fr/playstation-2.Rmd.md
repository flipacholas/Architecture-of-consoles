---
short_title: Architecture de la PlayStation 2
long_title: Architecture matérielle de la PlayStation 2
long_name: PlayStation 2
name: PS2
subtitle: Éclipser les supérieurs
date: 2020-04-08
release_date: 2000-03-04
javascript:
  - 'threejs'
generation: 6
top_tabs:
  Model:
    file: international
    caption: "La première PlayStation 2.<br>Sortie le 04/03/2000 au Japon, le 26/10/2000 en Amérique et le 24/11/2000 en Europe"
  Motherboard:
    caption: "Voici la version 'GH-001' du modèle SPCH-10000 commercialisé uniquement au Japon.<br>Grâce aux dons reçus, j'ai pu acheter ce modèle et en prendre une photo de bonne qualité pour me permettre d'identifier la plupart des puces.<br>Je présume que la puce dans le coin inférieur droit est la ROM BIOS de 4MB"
    bib_source: copetti
  Diagram:
    caption: "Le design d'origine (mis en œuvre sur la version 'SPCH-10000').<br>Chaque bus de données est accompagné de ses dimension et vitesse.<br>Cette architecture a traversé de nombreuses versions, plus de détails ci-dessous"
#Historical
aliases:
  - /projects/consoles/playstation-2/
---

## Une brève introduction

La PlayStation 2 n'était pas parmi les consoles les plus puissantes de sa génération, et pourtant elle a atteint un niveau de popularité inconcevable pour les autres entreprises.

Cette machine est très loin d'être aussi simple que ne l'était la [PlayStation originale](playstation), mais nous verrons pourquoi elle n'a pas partagé le même destin que [d'autres consoles complexes](sega-saturn).

## {.supporting-imagery}

## CPU

Au cœur de cette console se trouve une puissante puce intitulée **Emotion Engine**, ou 'EE', un projet conjoint de Toshiba et Sony [@cpu-cataldo] cadencé à **~294.91 MHz** [@cpu-rockin].

![L'Emotion Engine, tel que vu sur la première révision de la carte mère de cette console.](ee_chip.jpg)

Ce jeu de puces contient de nombreux composants, l'un d'eux étant le processeur principal. Les autres sont à la disposition du processeur pour accélérer certaines tâches. Pour cette analyse, nous diviserons l'EE en trois sections :

- Le **leader** : Le composant principal qui contrôle l'ensemble de la puce.
- La **mémoire disponible**, un ingrédient crucial pour que les processeurs puissent effectuer des opérations significatives.
- Les **co-processeurs** : Ceux-ci accélèrent des calculs particuliers.

### Le leader

En résumé, le processeur principal est un **MIPS R5900**, un cœur MIPS exclusif conçu pour cette console. Vous vous souvenez peut-être lorsque Sony a commencé à [s'aventurer avec le silicium MIPS](playstation#tab-1-2-mips-and-sony) avec la toute première [PlayStation](playstation) (où l'on peut trouver un [MIPS R3000A](playstation#the-offering) secondé par LSI). Pour la prochaine génération, nous avons un MIPS 'R5900'... mais que signifie ce nom pour nous ?

Pour comprendre ce qui se cache derrière les nouveaux numéros, examinons un peu l'histoire entourant cette époque.

#### Succès surpassé {.tabs.active}

Le [MIPS R4000](nintendo-64#cpu) était une série de processeurs populaires adoptée par une large gamme de systèmes, y compris un [concurrent proche](nintendo-64). Grâce à son succès, MIPS a apporté à la masse une gamme d'avancées jadis inaccessibles (calculs 64 bits, pipelines à 8 étages, etc.).

Avançons dans le temps, et le prochain grand saut est arrivé en 1995, avec la sortie du **R10000**. Désormais sous le financement de [SGI](playstation#tab-1-2-mips-and-sony), MIPS a produit un nouveau processeur qui a poussé les capacités de parallélisme du R4000 beaucoup plus loin, en introduisant des techniques comme [@cpu-yeager] :

- **Exécution spéculative** : Le processeur prédit le résultat des branches conditionnelles avant qu'elles ne soient calculées. Les prédictions sont basées sur des résultats antérieurs stockés dans une table interne de 512 entrées. Une fois la condition calculée, si la prédiction se révèle correcte, le processeur a économisé un temps précieux. Sinon, les calculs supplémentaires sont écartés.
  - Grâce à cela, MIPS a enfin transformé un problème récurrent ([aléas de contrôle](playstation#delay-galore)) en avantage.
  - Dans d'autres processeurs, vous pouvez trouver une fonctionnalité similaire appelée [prédiction de branche dynamique](gamecube#features).
- **Pipeline superscalaire à 4 instructions** : En plus du [design pipeliné](sega-saturn#cpu), le processeur va maintenant rechercher jusqu'à quatre instructions au début du pipeline et les distribuer à travers des unités séparées, permettant ainsi au processeur d'exécuter ces instructions simultanément. En procédant ainsi, le processeur atteint un plus grand degré de parallélisme.
- **Exécution hors ordre** : Le processeur va également réorganiser la séquence des instructions pour essayer de remplir ses unités autant que possible (tant qu'aucun risque n'est ajouté).
- **Cache L2 avec un bus de 128 bits**, permettant d'amener plus de données dans le processeur à la fois, ce qui devient une nécessité grâce aux améliorations précédentes.

Une telle innovation a cependant un coût de conception complexe, et le produit final était tout sauf bon marché. SGI ne l'a inclus que dans des équipements haut de gamme, et toute tentative de l'amener dans une console de salon était hors de question.

#### Haut de gamme pour les masses {.tab}

Déjà conscient des limites commerciales du R10000, SGI/MIPS a engagé **Quantum Effect Devices** (QED) pour développer une version abordable du R10000 pour le marché de milieu à bas de gamme. En tant qu'entreprise fondée par d'anciens employés de MIPS, QED se spécialisait dans la conception de variantes de cœurs MIPS pour le secteur économique.

Finalement, QED est revenu avec un nouveau cœur appelé le **R5000**, il s'agissait d'un R10000 ayant subi des réductions significatives [@cpu-halfhill] :

- L'exécution out-of-order est rétablie à l'ordre in-order.
- L'exécution spéculative est supprimée.
- La superscalabilité est limitée à deux instructions (2-issue) et ne parallélise plus les instructions entières. Cependant, les instructions à virgule flottante peuvent encore être associées à d'autres.
- En raison des réductions précédentes, le L2 est réduit à un bus de 64 bits à la place.

Par conséquent, cela est devenu un CPU idéal pour alimenter des équipements économiques, tels que les stations de travail d'entrée de gamme de SGI. Dans tous les cas, notez que le pipeline réduit exécutait toujours des opérations à virgule flottante en simultané, comme si QED prévoyait de le garder attractif pour les **applications vectorielles/3D**. Vous verrez bientôt qu'une autre entreprise a rapidement pris note de cela également.

En passant, il est curieux d'observer qu'à l'autre bout du monde, il y avait des avancées similaires mais dans la direction opposée : [ARM s'est associé à DEC](nintendo-ds#arms-new-territories) dans le but de propulser les puces ARM vers le marché haut de gamme.

#### Une commande spéciale pour Sony {.tabs-close}

Toshiba était un licencié MIPS depuis un certain temps [@cpu-toshiba] et n'était pas étranger à la fabrication de variantes et de packages MIPS. À un moment donné, Sony et Toshiba ont uni leurs forces pour produire un CPU exclusivement conçu pour la future console de Sony. C'était un énorme avantage pour Toshiba : très souvent, les CPU doivent répondre à une large gamme d'exigences provenant de différentes parties prenantes, ce qui limite les opportunités de spécialisation. Maintenant, il n'y avait qu'un seul objectif : le **jeu 3D**. Ainsi, offrant suffisamment de place pour toutes sortes d'**innovation**.

Cela dit, Toshiba a fini par s'approprier le design abordable du R5000 et l'a modifié pour accélérer les opérations vectorielles. Le nouveau cœur est appelé **R5900** et présente les améliorations '3D' suivantes [@cpu-stokes] :

- Une variation de l'**ISA MIPS III**. Cela inclut l'ISA 64 bits original vu précédemment sur la [Nintendo 64](nintendo-64#cpu), mais étendu avec des opcodes intéressants. Sony a ajouté certaines instructions de **MIPS IV** (prefetch et déplacement conditionnel) avec leur propre extension SIMD appelée **instructions multimédia** pour accélérer les calculs vectoriels (similaire au [SH-4](dreamcast#special-work), mais uniquement entiers).
  - Les instructions multimédia sont encore de 32 bits de large mais peuvent opérer jusqu'à trois vecteurs de 128 bits à la fois. Elles offrent des opérations telles que l'arithmétique vectorielle, les min/max et de nombreuses combinaisons scalaires pour former de nouveaux vecteurs.
- **32 registres polyvalents de 128 bits** : Une autre amélioration significative marquée Toshiba. Oubliez le stockage typique en [32 bits](playstation#the-offering), nous avons maintenant pénétré dans le domaine des 128 bits. Toutefois, la majorité des opérations utiliseront difficilement tout l'espace disponible (les mots MIPS sont [toujours de 64 bits de long](nintendo-64#cpu)). C'est là que l'extension multimédia mentionnée précédemment entre en jeu, car son ensemble utilisera pleinement le fichier de registres étendus.
  - Lors de l'utilisation des nouvelles instructions, chaque registre peut stocker des vecteurs constitués de nombreux types de scalaires (allant de deux entiers 64 bits à seize entiers 8 bits).
  - Pour éviter les pénalités de performance, ces registres sont accessibles via un **bus 128 bits**, tandis que le reste du CPU utilise un bus de données interne de **64 bits**.
- Deux **ALU de 64 bits**. Chacune peut opérer des entiers 64 bits de manière indépendante, mais aussi se combiner pour devenir une **ALU de 128 bits**. Cette dernière est le cerveau derrière ces jolis opcodes multimédia.

En plus de cela, nous trouvons également d'autres améliorations que les développeurs pourraient bien accueillir :

- **Pipeline à 6 étages** : C'est un étage de plus par rapport aux [prédécesseurs](playstation#the-offering).
- Exécution **superscalaire à deux voies** : Grâce aux deux ALU, jusqu'à deux opérations entières de 64 bits sont désormais exécutées en parallèle.
  - Cela rétablit un autre avantage perdu du MIPS R10000.
- **24 KB de cache L1** : divisés entre **16 KB pour les instructions** et **8 KB pour les données**.
  - Le circuit dispose aussi d'une **fonction prefetch** pour mettre les instructions et les données en cache avant qu'elles soient sollicitées. Ceci est réalisé en ajoutant une logique supplémentaire capable d'identifier quelles zones mémoire sont le plus souvent sollicitées.
- **16 KB de RAM Scratchpad**, aussi connue sous le nom de 'Fast RAM'.
- **Unité de gestion mémoire** : fournit une interface d'accès à la mémoire pour le reste du système.

De plus, le processeur est complété par **une unité de calcul à virgule flottante dédiée** (intitulée 'COP1') qui accélère les opérations sur des nombre à virgule flottante 32 bit (aussi connu en tant que `float` en C). Il s'agit d'une particularité car elle **ne suit pas le standard IEEE 754**, le plus évident étant son absence d'`infinity` (calculé en tant que `0` à la place) [@cpu-krysto]. À part cela, il possède 32 registres de 32 bits.

### Un choix de mémoire reconnaissable

Aux côtés de l'Emotion Engine se trouvent deux blocs de 16 MB de mémoire RAM, soit un total de **32 MB** de mémoire principale. La mémoire utilisée est de type **RDRAM** ([*c'est du déjà-vu !*](nintendo-64#memory-design)) et elle est accessible à travers un **bus 16 bits**.

![Design mémoire de l'Emotion Engine. Vous pouvez deviner où l'engorgement va survenir.](MemoryArch.png)

Au départ, cela peut être décevant à entendre, en sachant que le bus interne de l'Emotion Engine est plus large avec ses 128 bits. Cependant, les puces de RAM sont stratégiquement placées conformément à l'**architecture dual-channel**, qui consiste à connecter les deux puces à l'aide de deux bus 16 bits indépendants (un bus par puce) pour améliorer le débit de données. La configuration qui en résulte donne théoriquement 3.2 GB/sec, sachez donc que la latence mémoire n'est pas un problème avec cette console !

A l'un des coins de l'Emotion Engine se trouve un puissant **Contrôleur DMA**, ou 'DMAC', qui transfère les données entre la mémoire principale et la mémoire Scratchpad, ou entre la mémoire principale et n'importe quel composant à l'intérieur de l'EE.

Les transferts de données se font par lots de 128 bits, mais voici la partie intéressante : tous les 8 lots, le bus principal est temporairement débloqué. Cela ouvre une petite fenêtre pour réaliser d'autres transferts DMA en parallèle (jusqu'à dix) ou laisser le processeur utiliser le bus principal. Ce *mode opératoire* s'appelle **slice mode** et est l'un des nombreux modes disponibles sur cette unité DMA. Gardez à l'esprit que si le slice mode réduit les blocages sur le bus principal, il le fait au prix d'un ralentissement général du transfert DMA.

### Éviter les mésaventures passées

Que nous le voulions ou non, avec la quantité de trafic ayant lieu à l'intérieur de l'Emotion Engine, ce design pourrait subir les conséquences de l'**architecture mémoire unifiée**, ou 'UMA'. C'est-à-dire : plusieurs composants indépendants, tentant simultanément d'accéder à la mémoire principale, menant à un embouteillage. Ainsi, pour corriger cette problématique, Sony a réduit le besoin constant de mémoire en :

- Embarquant dans leurs processeurs **beaucoup de mémoire cache**. Ainsi, l'accès à la mémoire principale n'est requis que si c'est absolument nécessaire.
    - Ce sera le motif de 99% des mentions de cache/Scratchpad dans ce document.
- Ajoutant un **Write Back Buffer** de 128 bits : très similaire au [Write Gather Pipe](gamecube#ibms-enhancements), sauf qu'au lieu d'attendre d'être rempli à 25%, il vérifiera d'abord le statut du bus (c'est-à-dire encombré ou libre).

Cela semble très pratique pour les applications pouvant bénéficier du cache, mais qu'en est-il de ces tâches, comme la manipulation des Display Lists, qui ne devraient pas utiliser de cache du tout ? Par chance, le processeur fournit un autre mode d'accès à la mémoire, intitulé **UnCached**, qui utilse **uniquement** le Write Back Buffer. Ainsi, il ne gaspillera pas des cycles à corriger le cache (résultat de *cache misses*).

De plus, le **UnCached accelerated mode** est également disponible. Celui-ci ajoute un buffer pour accélérer la lecture des adresses contiguës en mémoire.

### D'autres choses intéressantes

A l'intérieur du même bloc Emotion Engine, il y a encore un autre processeur, intitulé **Image Processing Unit**, ou 'IPU' ; cette fois conçu pour la **décompression d'image**. En tant que successeur du [MDEC](playstation#tab-2-3-motion-decoder), cet IPU s'avère utile quand un jeu a besoin de décoder une vidéo MPEG2 sans mobiliser le processeur principal.

En résumé, le jeu envoie un flux d'images compressées à l'IPU (de préférence en utilisant le DMA) qui sont alors décodées vers un format que la GPU peut afficher. Le système d'exploitation de la PS2 se repose également sur l'IPU pour la lecture de DVD.

Enfin, l'IPU traite également les **textures haute définition** compressées, ce qui allège la charge processeur et réduit les larges transferts.

## Coprocesseurs

Cela fait deux ans que la concurrence a présenté sa [dernière proposition](dreamcast). Si vous avez lu l'article en question et commencez juste à lire celui-ci, je suppose que vous attendez *toujours* pour 'le truc' qui rend la PS2 aussi puissante qu'elle le semblait alors. À présent, laissez-moi vous présenter un ensemble *très* important de composants que Sony a placé dans l'Emotion Engine, les **Vector Processing Units**, ou 'VPU'.

### Architecture

Un Vector Processing Unit est un petit processeur indépendant conçu pour traiter des vecteurs, en particulier des vecteurs de quatre `float`. Ces processeurs sont si rapides qu'ils n'effectuent qu'**un cycle par opération**, ce qui peut être extrêmement pratique pour le calcul géométrique. Bien qu'ils exposent un comportement non standardisé similaire au FPU du processeur.

Les VPUs comportent les composants suivants :

- De la **Vector Unit Memory**, ou'VU Mem' : utilisée comme espace de travail pour l'unité vectorielle. Elle stocke les valeurs devant être traitées et/ou les résultats des calculs précédents.
- Une **Vector Unit** : le cœur du processeur. Elle contient de la mémoire (intitulée **Micro Mémoire**) pour stocker un programme (appelé **Microprogramme**) indiquant à l'unité comment traiter les données stockées dans la 'VU Mem'.
  - Elle met en œuvre un jeu d'instructions **64 bits** et l'unité d'exécution est **séparée en deux sous-unités parallèles**. La première multiplie ou ajoute des flottants, alors que l'autre divise des flottants ou traite des entiers. Cela permet de traiter **simultanément** à la fois des flottants et des entiers.
- Une **Vector Interface** : décompresse automatiquement les données vectorielles en provenance de la mémoire principale, dans un format que l'unité vectorielle peut exploiter. Cette unité peut aussi transférer des microprogrammes vers la Micro Mémoire.

### Fonctionnalités

Pour pouvoir fonctionner, l'unité vectorielle nécessite d'être 'démarrée'. Pour ce faire, le processeur principal est chargé de fournir le microcode.

Il y a **deux VPUs** intégrées à l'Emotion Engine, mais elles sont organisées différemment, laissant place à différentes utilisations et optimisations.

#### Vector Processing Unit 0 {.tabs.active}

![Architecture du VPU0.](VU0.png) {.tab-float}

La première VPU, intitulée **VPU0**, est placée entre le processeur et l'autre unité vectorielle (VPU1). Elle est là pour 'assister' le processeur principal.

La VPU0 a deux modes de fonctionnement :

- **Micromode** : c'est le 'mode traditionnel'. La VPU exécute indépendamment des 'micro-instructions' d'un microprogramme stocké dans la Micro Mémoire.
- **Macromode** : La VPU0 devient le 'COP2' du processeur principal et exécute des 'macro-instructions', reçues depuis le processeur principal via un bus 128 bits dédié.
  - Les macro-instructions ont les mêmes fonctionnalités que les micro-instructions mais utilisent des codes opération différents. Néanmoins, l'unité d'exécution VPU n'est plus scindée (ce qui signifie qu'elle ne peut exécuter qu'une instruction à la fois).
  - Bien que ce mode n'exploite pas entièrement tous les composants de la VPU0, il accélère malgré tout les opérations vectorielles du processeur. En outre, en termes de simplicité, un coprocesseur est plus facile à programmer qu'une unité indépendante (c'est quelque chose que les programmeurs de PC apprécieront).

Le mappage mémoire de la VPU0 donne également accès à certains des registres et flags de l'autre VPU, vraisemblablement pour vérifier son état ou récupérer rapidement les résultats d'opérations qu'elle a réalisées.

#### Vector Processing Unit 1 {.tab}

![Architecture du VPU1.](VUP1.png) {.tab-float}

La deuxième VPU disponible, **VPU1**, est une version améliorée de la VPU1 avec quatre fois sa quantité de Micro Mémoire et VU Memory. En outre, cette unité comporte un composant additionnel intitulé **Elementary function unit**, ou 'EFU', qui accélère l'exécution des fonctions exponentielles et trigonométriques.

La VPU1 est située entre la VPU0 et la Graphics Interface (le 'portail' vers la GPU), elle comporte donc des bus additionnels pour fournir la géométrie au GPU aussi vite que possible sans utiliser le bus principal.

D'autre part, et du fait de son emplacement, la VPU1 **fonctionnement uniquement en Micromode**.

Il est évident que cette VPU a été conçue pour les calculs trigonométriques, et pourrait servir de préprocesseur pour la GPU. Ainsi, elle est souvent chargée de fournir les fameuses Display Lists.

### Des mondes infinis {.tabs-close}

Une approche utile qui peut être exploitée avec ces unités est la **génération procédurale**. En d'autres termes, au lieu de construire un niveau en utilisant de la géométrie définie en amont, laissez les VPUs la générer en utilisant des algorithmes. Dans ce cas, la VPU exécute des **fonctions mathématiques produisant de la géométrie** qui sera ensuite interprétée par le GPU (des triangles, lignes, quadrilatères, etc) et finalement utilisée pour rendre la scène.

En comparaison avec les données explicites, le contenu procédural est idéal pour les tâches parallélisées ; cela libère de la bande passante, requiert très peu d'espace de stockage, et est dynamique (les programmeurs peuvent définir des paramètres pour obtenir des résultats différents) [@cpu-green]. De nombreuses disciplines peuvent hautement tirer parti de cette technique :

- Les **surfaces complexes** (par exemple, les sphères et tores).
- Le **world rendering** (le terrain, les particules, les arbres).
- **Courbes de Bézier**, une équation très populaire en imagerie numérique, utilisée pour tracer des courbes. Elles sont transformées en une **surface de Bézier** (géométrie explicite) et permettent différents degrés de précision en fonction du niveau de détail requis.

D'un autre côté, le contenu procédural peut être difficile à animer et, si l'algorithme est trop complexe, la VPU pourrait ne pas générer la géométrie à temps.

Pour résumer, le rendu procédural n'est pas une technique nouvelle, mais grâce aux VPUs, il ouvre la porte à des optimisations plus poussées et des visuels plus riches. Néanmoins, ce n'est pas une technique simple à implémenter, et la R&D de Sony a publié divers documents décrivant des approches différentes pour l'utiliser sur leur console [@cpu-tumati].

### Vous choisissez le workflow

Avec ces ajouts, les programmeurs ont maintenant une grande flexibilité pour concevoir leurs moteurs graphiques. Pour aller en ce sens, Sony a mobilisé des ressources supplémentaires pour imaginer et documenter des designs de pipeline efficaces. Les exemples suivants illustrent des pipelines graphiques optimisés pour différentes tâches [@cpu-stokes] :

![Design d'un pipeline parallèle.](Parallel.png){.tabs-nested .active title="Parallèle"}

![Design d'un pipeline série.](Serial.png){.tabs-nested-last title="En série"}

Dans le premier exemple, le design **Parallèle**, le processeur est combiné avec la VPU0 en macromode pour produire de la géométrie en parallèle avec la VPU1. Le groupe CPU/VPU0 utilise pleinement la Scratchpad et le cache pour éviter d'utiliser le bus principal, sur lequel la VPU1 se repose pour récupérer des données depuis la mémoire principale. A la fin, les deux groupes de rendu envoient simultanément leur Display Lists respectives au GPU.

Le second exemple, le design **en Série**, propose une approche différente où le groupe CPU/VPU0 travaille en tant que préprocesseur pour la VPU1. La première étape consiste à récupérer et traiter la géométrie que la VP1 va ensuite transformer en Display List.

Il s'agissait jusqu'ici d'exemples d'un point de vue théorique, mais pour expliquer une implémentation plus 'pratique', je vais faire référence à une vidéo que Jon Burton a publié au sujet du développement d'un jeu PS2 [@cpu-burton].

![Crash Bandicoot: The Wrath of Cortex (2001). Les particules forment la flamme de la bougie et la lumière vient de la fenêtre vitrée.](Crash.jpg) {.open-float}

L'ancien directeur de Traveller's Tales a expliqué comment son équipe a conçu un système de particules totalement contenu dans la VPU1. En résumé, la VPU1 était concentrée sur la lecture d'une base de données préremplie sur sa mémoire VU. Cette base de données était utilisée pour calculer les coordonnées des particules à n'importe quel instant sans dépendre sur d'autres composants. Le résultat du calcul pouvait être transformé en Display Lists et envoyé aussitôt.

{.close-float}

Avec cette approche, le processeur principal était considérablement allégé, lui permettant de prendre en charge d'autres tâches comme l'IA et la physique.

Il y a de nombreux autres exemples de disponibles, mais pour résumer : c'est désormais au programmeur de trouver une configuration optimale, et cela est une bonne chose.

## Graphismes

En considérant tout le travail à la charge de l'Emotion Engine, reste-t-il quelque chose ? La dernière étape, en réalité : l'affichage !

![Final Fantasy X (2001).](ffx.jpg) {.open-float}

Il y a une puce simple mais rapide et spécialisée : le **Graphics Synthesizer**, ou 'GS', cadencé à **~147.46 MHz**. Il embarque **4 MB de DRAM** pour réaliser tout le traitement en interne. Et donc, cela réduit le besoin d'accéder à la mémoire principale. La RAM embarquée (eDRAM) est connectée par le biais de différents bus en fonction du type de données.

Le GS offre moins de fonctionnalités que d'autres systèmes graphiques [précédemment analysés](gamecube#graphics) sur ce site. Néanmoins, il est vraiment rapide dans son travail.

{.close-float}

### Architecture et design

Ce GPU ne fait que de la **rasterisation** et c'est tout... Génération de pixels, mappage de textures, application de la lumière et quelques autres effets. Cela signifie qu'il n'y a pas de transformations de points (celles-ci sont réalisées par les VPUs). Aussi, il s'agit d'un pipeline figé, donc pas d'[ajustements sophistiqués](gamecube#creativity) ni de [shaders](xbox#graphics), vous êtes coincés avec un modèle d'ombrage prédéfini (comme Gouraud).

![Design du pipeline du Graphics Synthesizer](GS_Pipeline.png)

Cela semble plutôt simple, non ? Bien, voyons plus en détails comment se déroule chaque étape.

#### Pré-traitement {.tabs.active}

![Étage de pré-traitement.](gs_pipeline/Preprocessing.png) {.tab-float}

L'Emotion Engine démarre le Graphics Synthesize en approvisionnant sa DRAM embarquée avec les materials nécessaires (**bitmaps des textures** et **Colour Lookup tables**, ces dernières étant aussi connues sous le nom de 'CLUT'), en assignant des valeurs sur les registres du GS pour le paramétrer, et enfin, en envoyant les commandes de rendu (Display Lists) qui déclenchent le rendu par le GS de primitives (points, lignes, triangles, sprites, etc) à des positions spécifiquent à l'écran.

En complément, le GS pré-traitera certaines valeurs qui seront nécessaires pour les prochains calculs. La plus notable étant le résultat de l'**algorithme d'analyse différentielle**, qui sera utilisée pour les interpolations durant le rendu.

#### Rastérisation {.tab}

![Étage de rastérisation.](gs_pipeline/Rasterizing.png) {.tab-float}

En utilisant les résultats des calculs précédents, le renderer génère des pixels à partir des primitives. Cette unité peut générer 8 pixels (avec textures) ou 16 pixels (sans textures) simultanément, avec pour chaque entrée de pixel le calcul des propriétés suivantes :

- **RGBA** : correspond à la combinaison de Rouge, Vert, Bleu et Alpha (transparence).
- **Z-value** : utilisée pour le test de profondeur dans les étapes suivantes.
- **Brouillard** : optionnel, pour simuler le brouillard environnant.
- **Propriétés de texture** : contient l'adresse de la texture dans la DRAM, et d'autres propriétés (coordonnées, niveau de détail, filtre, etc) qui seront utilisées dans la prochaine étape.

Il effectue également des **Scissoring Tests** pour éliminer les polygones en dehors de l'image (à partir de leurs coordonnées X/Y). Certaines propriétés des pixels sont transmis à l'étape du 'Pixel testing' pour des tests complémentaires. Le **lighting** est également fourni en sélectionnant une des deux options disponibles, **Gouraud** et **Flat**.

L'ensemble est alors livré au moteur de 'Texture mapping', mais chaque propriété est traitée par une 'sous-moteur' spécialisé, ce qui permet de gérer plusieurs propriétés simultanément.

#### Texturing {.tab}

![Étage de placage de textures](gs_pipeline/Textures.png) {.tab-float}

Cette étape est accomplie par une grande unité Pixel qui peut calculer jusqu'à 16 pixels en même temps : ici, les textures seront appliquées aux polygones (à présent des pixels). De plus, les effets de brouillard et d'anticrénelage sont appliqués ici.

Les matrices des textures sont récupérées depuis la DRAM dans une zone définie comme le **Texture buffer**, bien que cela soit interfacé via une zone séparée intitulée **Texture Page Buffer** qui semble agir comme un mécanisme de mise en cache des textures. Les CLUTs sont également mappés en utilisant ce système de pages. Les deux sont récupérés au travers d'un **bus 512 bits**.

L'unité pixel réalise de la **correction de perspective** pour appliquer les textures sur les primitives (une grande amélioration, par rapport à la précédente approche d'[affine mapping](playstation#tab-4-5-textures)). De plus, il permet aussi un **filtrage bilinéaire ou trilinéaire**, ce dernier étant utilisé en même temps que les textures à mipmapping.

#### Tests {.tab}

![Étage de test des pixels](gs_pipeline/Tests.png) {.tab-float}

Ici, certains pixels seront éliminés s'ils ne remplissent pas certains critères. Ceci étant dit, les tests suivants sont réalisés :

- L'**Alpha test** : compare la valeur d'alpha (transparence) du pixel avec la valeur 'standard'. Ceci parce que dans certains cas, la valeur d'alpha est requise dans un certain intervalle, ou supérieure/inférieure à une valeur arbitraire.
- Le **Destination Alpha test** : vérifie à nouveau la valeur d'alpha du pixel avant de le dessiner dans le frame-buffer.
- Le **Depth Test** : compare la valeur de coordonnée Z du pixel avec la valeur correspondante dans le [Z-buffer](nintendo-64#modern-visible-surface-determination). Cela évite de traiter des pixels qui seraient masqués derrière d'autres pixels.

#### Post-traitement {.tab}

![Étage de post-traitement](gs_pipeline/Postprocessing.png) {.tab-float}

La dernière étape peut appliquer des effets sur nos nouveaux pixels, en utilisant le précédent frame-buffer qui réside dans la DRAM locale :

- **Alpha Blending** : ou simulation de transparence ; mélange les couleurs du frame-buffer actuel avec le précédent en mémoire.
- **Dithering** : les grandes valeurs RGBA devront être réduites, un dithering peut donc être appliqué pour adoucir la perte de précision.
- **Colour Clamping** : après application de traitements comme l'Alpha Blending, la nouvelle valeur RGB pourrait dépasser l'intervalle valide (0-255), le clamping ramène donc la valeur dans l'intervalle.
- **Formatting** : fait la conversion du frame-buffer final, généré via le pipeline, vers un format qui peut être stocké en mémoire.

Enfin, le nouveau frame-buffer, ainsi que le Z-buffer mis à jour, sont écrits en mémoire via un **bus 1024 bits**.

### Encore plus de post-traitement {.tabs-close}

Il y a à l'intérieur du GS un composant dédié, intitulé **Programmable CRT Controller** ou 'PCRTC', qui transmet le frame-buffer en mémoire vers la sortie Vidéo, afin que vous puissiez voir l'image sur la TV. Mais ce n'est pas tout : il contient aussi un ensemble spécifique, intitulé **Merge Circuit**, qui permet d'effectuer un mélange d'alpha entre deux frame-buffers séparés (c'est utile lorsque les jeux souhaitent réutiliser l'image précédente pour constituer la nouvelle). L'image qui en résulte peut être transmise à travers le signal Vidéo et/ou être écrite en mémoire en retour.

### Des modèles plus détaillés

Tout ceci étant dit, cela nous a certainement apporté des programmes de conception plus aboutie, pour affiner des personnages déjà célèbres. Jetez un œil à cette comparaison 'Avant/Après' :

![Crash Bandicoot (1996) pour la PS1.<br>732 triangles.](crash_ps1){.toleft model3d="true"}

![Crash Bandicoot: The Wrath of Cortex (2001).<br>2226 triangles.](crash_wrath_ps2){.toright model3d="true"}

Voici des personnages de nouveaux opus, ils ont été modélisés de zéro avec un grand niveau de détails :

![Kingdom Hearts (2002).<br>2744 triangles.](sora_kh1_ps2){.toleft model3d="true"}

![Dragon Quest VIII (2004).<br>2700 triangles.](hero_ps2){.toright model3d="true"}

Il est important de mentionner que des jeux comme *Dragon Quest* ont implémenté un modèle de lighting personnalisé, intitulé **Cel Shading** (un nom que j'ai [déjà mentionné](gamecube#creativity)) ; toutefois, dans mes précédents articles j'ai expliqué que la GPU en était principalement responsable. Dans le cas de la PS2, les calculs de couleur requis sont vraisemblablement réalisés par l'Emotion Engine, puisque le GS n'est pas aussi flexible que les autres GPUs.

### Sortie Vidéo

Comme stipulé précédemment, le PCRTC transmet le frame-buffer à travers le signal Vidéo. L'interface peut émettre le signal vidéo à travers un large choix de formats (pour fonctionner avec les TVs de n'importe quelle région géographique) [@graphics-opl] :

- **PAL** : envoie jusqu'à 640x512 pixels en 50 Hz, soit en progressif (576p) soit entrelacé (576i).
  - Aucun jeu dans le commerce n'utilise le 576p. Bien que certains gèrent le mode progressif, ils le font en 480p.
- **NTSC** : jusqu'à 640x448 pixels à 60 Hz, soit en progressif (480p) soit en entrelacé (480i).
- **VESA** : jusqu'à 1280x1024 pixels.
- **DTV** : jusqu'à la *quantité énorme* de 720x480 pixels en mode progressif, ou 1920x1080 en mode entrelacé.
  - En ajustant les paramètres du PCRTC, un jeu peut également forcer la sortie en 1080p. Cependant, ce mode n'est pas documenté et est donc sujet à un comportement indéfini.
  - Est-ce que cela signifie que la PS2 peut 'afficher de la HD' ? Techniquement... oui, mais je ne pense pas, pour la plupart, que les studios aient risqué une perte de performances pour un format qui n'étaient pas encore populaire.

![Le côté droit de la console [@photography-amos], vu de l'arrière. Montre le port A/C, le port audio digital, et la sortie AV Multi.](photos/ps2_back.png) {.open-float}

Il y a une certaine quantité de modes possibles, mais cela se résume aux formats adoptés durant le début des années 2000, ce qui réduit donc à PAL et NTSC. Aussi, même si le PAL offrait une plus haute définition que le NTSC, certaines versions Européennes de jeux NTSC avaient recours à des bandes noires pour masquer les lignes horizontales non utilisées, et abaissaient la fréquence de rafraîchissement pour rester dans la limite des 50 Hz (je les appelle des 'mauvais portages' !).

{.close-float}

Le port de sortie Vidéo (**Multi A/V**) est vraiment pratique. Il transporte RGB, Component, S-Video et composite. Ainsi, tous les signaux importants sont disponibles, sans nécessiter des adaptateurs propriétaires ni des modifications internes.

## Audio

La nouvelle puce audio est une mise à jour incrémentielle de la vieille [SPU](playstation#audio), intitulée... **SPU2** ! Les améliorations comportent l’intégration de **2 MB de mémoire interne** et **48 canaux disponibles** (le double de la quantité originale).

La SPU2 est constituée de deux processeurs sonores, intitulés **CORE0** et **CORE1**, et est cadencée à ~36.86 MHz. Chaque processeur gère **24 canaux**.

Chose assez curieuse, ce sont malgré tout deux processeurs indépendants et, pour les configurer, il vous faut modifier leurs registres. Cependant, Sony a averti les développeurs que les deux ensembles de registres doivent être paramétrés avec un gap d' 1/48000 ème de seconde. Si vous vous empressez, le comportement de la SPU2 devient imprévisible !

La SPU2 hérite des effets disponibles sur le SPU d'origine. La mémoire fournie est utilisée comme un 'espace de travail' : vous pouvez y stocker des données waveform brutes et allouer de l'espace supplémentaire pour les traiter et leur appliquer des effets.

Enfin, la puce peut fusionner les canaux pour fournir une **sortie stéréo**. A présent, voici la partie intéressante : la SPU2 peut se fournir le mélange stéréo comme nouvelle entrée, permettant à l'EE d'y accéder (pour le mélanger avec de l'audio supplémentaire, par exemple), ou y ajouter des effets supplémentaires (tels que de la réverbération, de l'écho ou du délai).

![Kingdom Hearts II (2005). Sans réverbération.](goomy_noreverb){.toleft video="true"}

![Kingdom Hearts II (2005). Avec réverbération.](goomy){.toright video="true"}

### Sortie audio

Le signal audio est transmis par deux biais :

- **Audio numérique** : connu sous le nom de Sony/Philips Digital Interface ou 'S/PDIF'.
- **Audio analogique** : passe par le convertisseur numérique vers analogique, et termine au port Multi A/V.

## E/S

À l'origine, l'I/O de la PS2 n'était pas particulièrement complexe. Cependant, les révisions ultérieures de cette console ont complètement bouleversé à la fois le design interne et externe. Donc, dans l'ensemble, cette console présente de nombreuses formes d'I/O réparties sur différentes révisions.

### Le CPU spécial

Pour commencer, il y a un processeur dédié qui arbitre la communication entre les différents composants ; ce processeur n'est autre que le **processeur originel MIPS R3000-based** utilisé par la [PlayStation 1](playstation#cpu). Cette fois, il est appelé **Processeur I/O** (IOP) et tourne à **37,5 MHz** avec un **bus 32 bits** [@io-buses].

![Diagramme principal de l'architecture de la PlayStation 2. Remarquez que le Processeur I/O a un accès exclusif à la plupart des I/O.](diagram.png)

L'IOP communique avec l'Emotion Engine à travers une interface E/S spécialisée, intitulée **System Interface** ou 'SIF' ; les deux extrémités utilisent leurs unités DMA pour transférer des données entre elles. L'IOP dispose également de mémoire dédiée - **2 MB de [EDO RAM](playstation#the-offering)** (comme la PS1) - utilisée comme tampon.

En somme, ce processeur donne accès aux ports frontaux, au contrôleur DVD, la SPU2, la mémoire ROM du BIOS ainsi que la baie d'extension *PC Card*.

Quoi qu'il en soit, un an après l'arrivée de la révision 'Slim' (2005), l'IOP a été remplacé par un SoC qui intègre à la place un **PowerPC 401 'Deckard'** (une version réduite du PowerPC 601 pour microcontrôleurs), **4 MB de SDRAM** (2 MB de plus qu'auparavant) et un émetteur-récepteur Ethernet (auparavant trouvé dans un accessoire externe).

#### Rétrocompatibilité

Pour les modèles qui intègrent le processeur du prédécesseur, on peut supposer que la compatibilité PS1 ferait partie du package. Assez commodément, Sony a inclus un émulateur PS1 (appelé `PS1DRV`) qui se charge dès qu'un disque PS1 est inséré. Lorsque cela se produit, l'IOP est sous-cadencé pour fonctionner à la vitesse de la PS1, l'EE est 'ré-affecté' pour émuler l'[ancien GPU](playstation#graphics) et le SPU2 est reconfiguré pour se comporter comme le [SPU original](playstation#audio).

Avec les modèles basés sur PowerPC, la compatibilité ascendante a été maintenue mais par le biais d'une implémentation logicielle complète à la place.

### Interfaces disponibles

Cette console conservait les [précédents ports frontaux](playstation#front-ports), qui étaient disponibles sur la PlayStation d'origine ; elle proposait également quelques interfaces 'expérimentales' qui semblaient très prometteuses au départ.

![Avant de la PS2 montrant des ports communs incluant les contrôleurs et les cartes mémoire. De plus, les nouveaux USBs et les ports i.Link [@photography-amos].](photos/ps2_front.png)

L'ajout le plus populaire : **deux ports USB 1.1**. Sa vitesse théorique est de 12 Mbps mais cela dépend fortement de la bande passante de l'IOP (qui a tendance à être considérablement plus lente). Néanmoins, il a été largement adopté par des accessoires tiers.

Il y avait également quelques 'expériences' qui n'ont pas duré très longtemps. Par exemple, le port **i.Link** en façade (aussi connu sous le nom d'IEEE 1394, ou 'FireWire' dans l'écosystème Apple). Ce port était utilisé pour connecter deux PS2 pour permettre le multijoueur local, mais fut retiré après la troisième version (a priori remplacé par la 'Carte réseau', plus de détails ci-dessous).

#### La combinaison inhabituelle Ethernet + HDD

À l'arrière de la console, nous avions également un emplacement pour **cartes PC**. Pour cela, vous pouviez acheter la 'carte adaptateur réseau' de Sony qui fournit deux interfaces supplémentaires :

- Un port **Ethernet**, pour le multijoueur en ligne.
- Un port **disque dur** externe et propriétaire : Il était vendu par Sony et contenait un disque dur ATA 3.5" typique avec un espace de 40 Go. Il permettait aux jeux de stocker des données temporaires (ou de s'installer là de façon permanente) pour des temps de chargement réduits. Cela étant, seuls quelques jeux utilisèrent cette fonctionnalité.

Dans les versions suivantes, le port PCMCIA fut remplacé par une **Baie d'extension** permettant l'installation d'un disque dur 3.5" dans la console. Vous devez d'abord acheter une **adaptateur réseau** qui, en plus des connectiques Modem et/ou Ethernet (en fonction du modèle), comprenait aussi les connections nécessaires pour un disque dur ATA-66.

![Arrière de la PS2 montrant la baie disque (clapet de protection retiré) [@photography-amos].](photos/back_bay.png)

Sous le capot, les données dans le HDD sont structurées en utilisant un système de fichiers appelé 'PFS' [@io-fs]. Étrangement, la mise en page ne contient pas de table de partition, juste un catalogue très primaire appelé 'Aligned Partition Allocation' (APA). Ceci est peut-être dû au fait que Sony n'a expédié que des disques de 40 Go. Par conséquent, l'évolutivité n'était pas sur leur liste de priorités.

![Adaptateur réseau vu depuis l'avant [@photography-amos]. Ce modèle précis fournissait un modem et des ports Ethernet.](photos/harddrive_adaptor_front.png) {.toleft.no-borders}

![Adaptateur réseau vu de l'arrière [@photography-amos], avec un disque dur.](photos/harddrive_adaptor_back.png) {.toright.no-borders}

Le transpondeur Ethernet supportait des vitesses de transfert allant jusqu'à 100 Mbps (12,5 MB/s). Cependant, le **taux observé est bien inférieur** (réduit jusqu'à 2 MB/s dans certains cas). L'explication est relativement simple : afin d'aboutir à une communication réseau fonctionnelle, il faut implémenter toutes les couches du standard 'OSI' ; et l'émetteur-récepteur n'est qu'une des pièces du puzzle. Le reste est souvent délégué à l'IOP (et donc, en logiciel), mais de par les performances limitées de l'IOP [@io-bottleneck], cela mène à des goulots d'étranglement.

#### Affiner

La révision Slim a révisé tout le modèle Ethernet + HDD : il n'y a plus de baie d'extension, mais un port Ethernet est installé en permanence à l'arrière (certains modèles incluaient également un Modem).

![Arrière du modèle Slim, montrant un port Ethernet fixe.](photos/ps2_slim_back.jpg)

En plus de cela, la nouvelle révision a ajouté un nouveau **capteur infrarouge**, à utiliser avec une télécommande de marque Sony (vendue séparément).

### Accessoires interactifs

La nouvelle version de la manette, la **DualShock 2**, est une version légèrement améliorée de la DualShock.

![Manette DualShock 2 [@photography-amos].](photos/dualshock2.png) {.toleft.no-borders}

![Carte mémoire officielle (modèle 8 MB) [@photography-amos].](photos/memorycard.png) {.toright.no-borders}

À l'époque de la PlayStation d'origine, de multiples versions de la manette furent commercialisées, mettant en avant différentes fonctionnalités (et créant une fragmentation du marché). A présent, pour le bien des développeurs, il y avait un unique modèle réunissant toutes les caractéristiques précédentes.

En comparaison avec la DualShock, la nouvelle version proposait un léger restylage, disposait de deux sticks analogiques, ainsi que deux moteurs de vibration, respectivement pour des entrées plus précises et un meilleur feedback.

À côté du connecteur pour la manette se trouve l'**emplacement Carte Mémoire**, compatible avec les cartes PS1 et PS2. Les nouvelles cartes intègrent un circuit supplémentaire, pour des besoins de sécurité, connu sous le nom de **MagicGate**, qui permet aux jeux de bloquer les transferts de données entre différentes cartes mémoire. L'IOP prend en charge le chiffrement et le déchiffrement du contenu, et ce avec l'aide du chipset MagicGate (introduit dans la carte mémoire) et du lecteur DVD, ce dernier regroupant les clés de chiffrement.

Cependant, certaines cartes mémoire tierces ne prenaient pas en charge MagicGate.

## Système d'exploitation

Il y a sur la carte mère une puce de **4 MB de mémoire ROM** qui stocke une grande quantité de code, utilisé pour charger un menu de shell (avec lequel les utilisateurs peuvent interagir) mais qui offre aussi des appels système pour simplifier l'accès E/S \[@cpu-rockin\] (sur lesquels se reposent les jeux).

![Animation Splash après l'allumage de la console.](bios/animated.jpg){.tabs-nested .active title="Animation de démarrage"}

![Logo PS2 affiché après qu'un jeu PS2 ait été inséré.](bios/game_splash.jpg){.tabs-nested-last title="Disque valide"}

Lors de la réception de l'alimentation, le MIPS R5900 et l'IOP démarreront tous deux à l'adresse `0xBFC00000` (c'est le vecteur de réinitialisation signature de tous les processeurs MIPS). Pour gérer les conflits attendus, cependant, le code respectif stocké à cette adresse (pointant vers la mémoire ROM du BIOS) fera en sorte que chaque processeur branche à un endroit différent en fonction de son identifiant.

Dans le cas du R5900, le processeur suivra ces étapes [@cpu-rockin] :

1. Initialiser le matériel.
2. Charger le **noyau** de la ROM dans la RAM. Une fois chargé, le noyau fournira une couche d'abstraction aux applications (principalement les jeux) pour interagir avec le matériel. De plus, il expose également une API pour la multithread (coopératif et basé sur la priorité).
3. Le noyau charge `EELOAD`, un module de noyau qui, à son tour, amorce `OSDSYS`. Ce dernier est le programme qui affiche l'animation de démarrage et l'interface système.

De l'autre côté, l'IOP initialisera une partie de son matériel et chargera ensuite plusieurs **modules**, ceux-ci permettent à l'IOP d'accéder au matériel de cette console. A l'issue, l'IOP sera placé dans un état 'en attente de commande'.

Il convient de noter que l'utilisation des modules permet à Sony de commercialiser de nouvelles versions matérielles de la PS2 sans modifier l'IOP (jusqu'à ce qu'ils le fassent), réduisant ainsi certains coûts de production dans le processus.

### Interface système

Les fonctionnalités de l'interface système PS2 sont globalement les mêmes que pour les autres consoles de 6ème génération.

![Menu initial. Apparait quand aucun disque n'est inséré.](bios/menu.jpg){.tabs-nested .active title="Menu"}

![Menu des cartes mémoires.](bios/mem_list.jpg){.tab-nested title="Navigateur"}

![Menu des sauvegardes. S'affiche après avoir sélectionné une carte mémoire.](bios/save_list.jpg){.tab-nested title="Sauvegardes"}

![Éditeur de sauvegardes, s'affiche après avoir sélectionné une sauvegarde.](bios/save_editor.jpg){.tab-nested title="Éditeur"}

![Configuration système.](bios/options.jpg){.tabs-nested-last title="Options"}

L'interface est composée de plusieurs interfaces utilisateurs pour gérer les opérations typiques, comme manipuler les données sauvegardées stockées sur la carte mémoire ou modifier l'horloge. Elle permet aussi quelques options avancées, comme changer le mode de sortie vidéo.

### Mise à jour

Le BIOS est effectivement stocké dans une mémoire en lecture seule, mais cela n'a pas empêché Sony de le modifier après sa sortie d'usine. Vous voyez, en arrière-plan, le BIOS a laissé deux portes ouvertes pour des modifications futures :

- Les applications (jeux et `OSDSYS`) peuvent **modifier à chaud les routines du noyau en cours d'exécution** [@operating_system-kpatch]. Tant le SDK officiel que l'informel 'ps2sdk' ont fait un usage extensif de cela, car les ingénieurs de Sony ont ultérieurement découvert que leur noyau était truffé de bogues (_jeu de mots non intentionnel_).
- **`EELOAD` recherchera un binaire `OSDSYS` mis à jour stocké sur la carte mémoire et/ou le disque dur** [@operating_system-israelson]. Sony s'appuyait sur cela pour ajouter un lecteur de films DVD et la prise en charge du disque dur, car aucun de ces pilotes n'était intégré dans les premières révisions de cette console.
  - Ces mises à jour ont été distribuées sous forme de disques d'installation fournis avec la console ou le kit HDD (une partie du coffret Final Fantasy XI).
  - Contrairement au noyau, qui doit être compatible avec les anciens SDK, les révisions ultérieures de la console incluaient les mises à jour précédentes dans la mémoire ROM du BIOS.
  - Pour contrôler la distribution, les binaires doivent être signés avec le **Data Encryption Standard** (DES) [@operating_system-kelftool], un système de cryptage symétrique, en utilisant des clés connues uniquement par Sony (en théorie). De plus, les binaires doivent être stockés dans un appareil de stockage prenant en charge MagicGate.

Quoi qu'il en soit, Sony a fini par retirer la deuxième méthode dans les modèles PS2 tardifs (avec une version BIOS `2.30`). Je présume que Sony ne prévoyait pas d'autres mises à jour et que cela s'ajoutait simplement à la surface d'attaque.

## Jeux

Le niveau de popularité de cette console fut sans précédent durant les années 2000 ; en fin de vie (en 2013, après 13 ans !), la ludothèque regroupait 1850 titres [@games-antista].

![Mr Moskeeto (2001). Quand quelqu'un me parle de l'abondance des jeux PS2, je me rappelles de celui-ci.](mr_moskeeto.jpg) {.open-float}

Ce qui s'est passé est réellement impressionnant. La PS2 n'avait pas une architecture très amicale pour le programmeur (du moins, du point de vue d'un programmeur PC), et pourtant, avec un tel nombre de jeux développés pour cette console, beaucoup se demandent s'il n'y a pas eu d'autres raisons (comme l'absence de contraintes de licences, de bas couts de distribution, son petit format, et bien d'autres).

{.close-float}

### L'écosystème de développement

Sony fournissait du matériel et du logiciel pour faciliter le développement des jeux.

Pour le logiciel, elle fournisait le **PlayStation 2 SDK** qui incluait [@games-sdkkit]:

- La chaine de compilation de l'Emotion Engine : un ensemble de compilateurs, assembleurs, éditeurs de lien et débogueurs C/C++ utilisés pour contrôler chaque élement de l'EE. Le CPU principal était principalement programmé en C/C++, cependant, les composants critiques pour les performances, comme les unités vectorielles, étaient programmées en assembleur (microcode/macrocode).
  - Le pack incluait aussi un 'simulateur pour l'Emotion Engine' qui permettait de tester rapidement le code sans avoir à l'envoyer sur une vraie console, même si le simulateur n'était pas aussi précis qu'un Emotion Engine matériel.
  - Tous ces outils fonctionnaient sous Linux, Solaris et Windows. Ce dernier tournait sur l'environnement Cygnus.
- Des librairies bas-niveau : elles faisaient office d'interface de nombreuses fonctions systèmes (appels au BIOS inclus).
- Les outils d'analyse pour monitorer les performances.
- Des outils additionnels pour se connecter au matériel de développement officiel.

Pour le matériel, Sony fournissait aux studios du matériel dédié pour faire tourner et dégoguer les jeux "à domicile". Les premiers devkits étaient des cartes reliées ensemble pour répliquer le matériel encore non-mis sur le marché de la PS2. Les kits ultérieurs (appelés **Development Tool**), ont une apparaence plus présentable, des entrée-ssorties améliorées et combinent du matériel de station de travail (sous RedHat 5.2) avec du matériel de la PS2 afin de coder et déployer les jeux dans le même boitier [@games-devkit].

La combinaison du Devkit, du SDK officiel et de CodeWarrior (un célèbre IDE) était l'une des configurations les plus populaire.

### Support

Le lecteur de disque peut lire à la fois des CD et des DVD, ce qui fait que les jeux pouvaient être distribués dans les deux formats, mais pour des raisons évidentes, la plupart des titres de la console sont en format DVD.

![Kingdom Hearts II (2005). Boite de jeu typique et disque.](kh2_box.jpeg) {.open-float}

Les DVDs peuvent contenir jusqu'à **4.7 GB** de données dans le cas de **DVD-5** (le sous-format le plus courant) ou **8.5 GB** dans le cas de **DVD-9** (version double couche, moins courante) [@games-dvd]. Il y a en fait un troisième format, **DVD-10**, qui est double couche, mais aucun jeu ne l'a utilisé.

En raison du type de média utilisé, on pouvait jouer à des jeux, mais aussi lire des films. Après, cela demande à un décodeur d'être capable de lire le format du film sur DVD, et pour cela, la PS2 a initialement inclut les bits nécessaires sur la carte mémoire (parès tout, la carte mémoire est juste un support de stockage) mais les modèles ultérieurs avaient le logiciel adéquat pré-installé dans la ROM du BIOS.

{.close-float}

Pour ce qui est de la vitesse, les CD-ROMs sont lus à une vitesse de 4x (donc de 3.6 MB. s) et les DVD-ROMS sont lus à une vitesse de 4x(5.28 MB/s) [@games-transfer].

### Les services réseaux

Comme vous l'avez vu, les fonctionnalités réseaux de ces consoles n'étaient pas standardisées avant les révisions finales, qui sont arrivées quatre ans après la première mise sur le marché. De la même manière, les studios de jeux vidéos étaient en charge de fournir l'infrastructure nécessaire s'ils décidaient de fournir des services online (comme du multijoueurs). Dans les années suivantes, Sony déploya le **Dynamic Network Authentication System** ou 'DNAS'. Ce n'était pas un serveur online, mais un système d'authentification pour éviter aux jeux piratés de se connecter en ligne.

### Un type de jeu inhabituel

À part tous ces jeux avec leurs *graphismes de toute beauté*, Sony a sorti une distribution Linux basée sur 'Kondara' (elle-même basée sur RedHat 6) disponible sur deux DVD (le premier disque est appelé 'Runtime Environment' et le second 'Software Packages') avec un adaptateur VGA, un clavier USB et une souris ; plus quelques manuels de développeur. Ce pack était connu sous le nom de **Linux Kit** et vous pouviez exécuter l'OS en démarrant le premier DVD puis procéder comme dans n'importe quel environnement Linux *de la vieille école*. Il fallait évidemment un disque dur d'installé dans la console pour installer la distribution Linux. Une fois installée, le premier DVD était toujours requis pour démarrer l'OS.

Le Kit Linux incluait des compilateurs pour l'EE (gcc 2.95.2 avec la glibc 2.2.2) et des assembleurs pour les unités vectorielles, ainsi qu'un système de fenêtrage (XFree86 3.3.6) 'accélré' par le Graphics Synthesizer [@games-linux]. Dans l'ensemble, cela ressemblait à un environnement intéressant. En fait, l'un des papiers de recherche que j'ai lu pour rédiger cet article portait sur des travaux réalisés avec cette configuration.

## Anti-Piratage et Homebrew

Il y a beaucoup à dire à ce propos, alors commençons par le lecteur DVD?

### Protection contre la copie de DVD

Cette section était particulièrement préoccupante pour les studios de jeux, car cette console utilisait un format de disque très abordable pour distribuer des jeux. Ainsi, le risque de piratage était élevé.

![Ce message d'erreur peut apparaitre si le disque est cassé... Ou qu'une copie piratée est insérée.](bios/rsod.jpg)

Quand l'OS charge un jeu, il le fait en envoyant des commandes spécifiques au lecteur DVD. Ces commandes spécifiques lisaient le contenu du jeu d'une manière très différent des commandes DVD standards (i.e pour lire un film DVD). Il apparait que les jeux autorisés contenaient une 'map file' hors de portée dans la section interne du disque, qui indexait le système de fichiers en utilisant le nom, la position et la taille. Quand on demandait au lecteur DVD de lire un disque de jeu, il naviguait toujours dans le disque en utilisant la 'map file', ce qui veut dire qu'une copie piratée d'un jeu, qui n'incluait pas la 'map file' était impossible à lire.

Le tout était complémenté par un système de verrouillage géographique qui empêchait les jeux importés de fonctionner sur une console d'une région géographique différente.

### Contournement des protections

Après avoir expliqué les parties les plus critiques de cette console, passons en revue les différentes méthodes découvertes (et commercialisées) qui ont contourné les mécanismes de protection.

#### Attaque du lecteur DVD

Dès que la PS2 a atteint les magasins, de nombreux produits tiers sont apparus avec la promesse de 'déverrouiller' le lecteur DVD. Ne voyant aucun signe de support pour Homebrew (en dehors de la solution Linux), le piratage était le principal bénéficiaire.

##### Modchips {.tabs.active}

Comme pour toute autre console de sa génération (et celles d'avant) utilisant des systèmes basés sur des disques, ce n'était qu'une question de temps avant que des entreprises tierces ne fassent de la rétro-ingénierie sur le sous-système DVD. Le but était de trouver une faille exploitable qui pourrait forcer le pilote à naviguer dans le système de fichiers sans avoir besoin d'un fichier map hors de portée.

Cela s'est finalement matérialisé sous la forme de **modchips**, qui ont également levé les contrôles de protection contre la copie et les restrictions de verrouillage régional.

##### Codes de triche {.tab}

En plus des modchips, qui nécessitaient des compétences en soudure pour être installés, des disques non autorisés mais 'authentiques' sont apparus sur le marché. Ces disques ont patché le Kernel pour supprimer la protection régionale et utiliser des cheats en jeu.

Le plus important, c'est que les disques 'cheat' avaient l'avantage de ne nécessiter aucune modification sur la console. Je suppose que le meilleur exemple à mentionner est le *CodeBreaker*.

#### Échange de disques {.tab}

Au beau milieu des dernières avancées, d'autres astuces apparurent. Cette fois, l'astuce exploitait la gestion des secteurs défectueux par le lecteur. La **Swap Magic** ressemblait à un autre disque 'authentique', mais son 'jeu' disait au lecteur DVD de lire un exécutable inexistant trouvé sur un secteur défectueux délibéré, arrêtant ainsi complètement le pilote [@anti_piracy-hacking]. Cette fenêtre d'opportunité permettait aux utilisateurs de changer ce disque pour un disque non-authentique. Puis, Swap Magic, toujours chargé en mémoire, démarrait l'éxecutable princiapl du disque, chargant un vrai jeu à la fin. Tout ceci se passait pendant que le pilote de périphérique pensait qu'un dique authentique était inséré.

Cela ne demandait pas de modifier la console. Cependant, en fonction du modèle, le boitier extérieur de la PS2 devait être trafiqué pour bloquer les capteurs d'éjection du lecteur de disque. Dans certains modèles, placer des morceaux de coton à certains endroits faisait partie de la méthode.

#### Départ des modchips {.tabs-close}

Avec le temps, plus de recherches ont été rassemblées et partagées sur cette console. Par conséquent, de nouvelles découvertes plus sophistiquées ont conduit à une nouvelle vague de développement qui ne dépendait plus du matériel externe, au moins principalement. De plus, le piratage n'était plus le principal objectif. Au lieu de cela, la capacité de faire fonctionner des programmes tiers sans l'approbation de Sony (appelée **Homebrew**) a rapidement dominé les objectifs.

#### Independence overflow {.tabs.active}

La PS2 mémorise sur la carte mémoire un fichier de base de données nommé `TITLE.DB`, qui contient des informations pour optimiser l'émulation des jeux PS1 [@anti_piracy-grand]. Quand un jeu PS1 est inséré, l'OS charge le fichier de base de données et le copie en mémooire à une adresse prédéterminée (*attaque numéro 1*). L'analyseur d'informations est implémenté en utilisant **`strncpy()`**, une fonction en C qui copie des chaînes (chaînes de caractères) d'un endroit à un autre.

Pour les gens familiers avec le C, vous voyez probablement où je veux en venir. Le truc, c'est que `strncpy()` ne connait pas la longueur d'une chaine de caractère, donc à moins qu'elle soit terminée (en plaçant `\0` à la fin de la chaine), la copie continue 'indéfiniment' (avec des résultats inprévisibles). Heureusement, cette fonction contient un paramètre optionnel qui spécifie le nombre maximal d'octets à copier, protégeant la copie d'un dépassement de tampon. Aussi incroyable que cela puisse paraitre, **Sony n'a pas utilisé ce paramètre**, alors que chaque entrée de la base de donnée a une taille fixe de 256 octets (*attaque numéro 2*).

Après une inspection plus poussée dans la RAM, `TITLE.DB` se trouve copiée **à côté d'un registre sauvegardé**, `$ra`, qui indique l'adresse à laquelle revenir après la fin de l'exécution de la fonction en cours (*attaque numéro 3*), conduisant à **The independence exploit** [@anti_piracy-independence] : Créez un `TITLE.DB` avec une grande chaîne, intégrez un exécutable et concevez cette chaîne de manière à ce que `$ra` soit réécrite pour pointer vers l'exécutable. Si vous parvenez à télécharger ce fichier sur votre Memory Card (grâce à une autre faille de sécurité ou un adaptateur USB pour PC), vous obtenez un simple **Homebrew launcher**.

La découverte a été publiée en 2003. Par conséquent, avec la révision slim, Sony a expédié une nouvelle révision de la ROM BIOS qui a corrigé cette faille. Curieusement, ce n'était pas la dernière [gaffe](wii#the-fall-of-encryption) à exposer du code mal branlé.

#### L'exploit de la signature {.tab}

En novembre 2007, un groupe de hackers a commencé à vendre **Memor32** [@anti_piracy-fmcb], juste une autre Memory Card tierce typique, sauf que pour une raison quelconque, elle contenait un **FPGA** et un **port USB**. Il a fallu attendre qu'un firmware appelé **Memento** apparaisse sur les forums internet pour que la vraie nature du Memor32 devienne claire : exécuter des exécutables non autorisés depuis la Memory Card, tout comme l'exploit Independence.

L'implémentation de Memento s'appuyait sur une faille dans la façon dont la signature du lecteur DVD est vérifiée. Il a été découvert que, bien que les binaires doivent être signés en utilisant les clés de Sony, **l'intégrité du binaire n'est pas vérifiée**. Ainsi, n'importe qui pouvait remplacer le code exécutable par autre chose (qui s'adapte toujours dans le même espace) et le système d'exploitation l'exécuterait joyeusement. Le firmware Memento utilisait cet exploit pour déguiser sa charge utile au sein du lecteur DVD et intègre quelques utilitaires qui permettent aux utilisateurs de charger des jeux (soit à partir du lecteur de disque, soit à partir du disque dur).

Cependant, la popularité du Memor32 et du Memento fut rapidement éclipsée une fois qu'une alternative gratuite (au départ open-source) est arrivée : **FreeMCBoot**.

#### La solution universelle {.tab}

Une fois que Memento a été rétro-ingénieré, une alternative qui ne nécessitait pas le Memor32 est apparue sur Internet. **FreeMCBoot** a exploité la même vulnérabilité sauf qu'il pouvait être **installé sur n'importe quelle carte mémoire MagicGate**. Le seul inconvénient est qu'un autre exploit (par exemple l'échange de disques) était encore nécessaire pour démarrer l'installateur.

Assez curieusement, l'interface utilisateur de FreeMCBoot emprunte des éléments de `OSDSYS`, fournissant ainsi un menu familier pour lancer d'autres Homebrews. Il modifie également le noyau pour ajouter des API afin d'accéder aux dispositifs de stockage de masse dans les ports USB 1.1, dont de nombreuses applications homebrew dépendaient pour localiser des fichiers supplémentaires.

De plus, l'installateur offre deux options : installer uniquement les fichiers nécessaires pour la console actuelle, ou installer une configuration globale pour toutes les variantes de la PS2. Fait curieux, cette dernière option a été difficile à réaliser [@anti_piracy-fmcb]. Initialement, l'installateur modifiait la table de partition de la carte mémoire pour éviter de manquer d'espace, ce qui n'était pas particulièrement sûr.

Comme par chance, en 2011, le [système de sécurité](playstation-3#os-security-hierarchy) de la [PlayStation 3](playstation-3) venait d'être [compromis](playstation-3#tab-17-4-the-fall-of-encryption), révélant de nombreux secrets cachés à l'intérieur. Parmi eux, une collection de clés MagicGate utilisées globalement pour la [compatibilité descendante PS2](playstation-3#backwards-compatibility). À partir de ce moment, il n'était plus nécessaire de recourir à l'astuce de la signature binaire limitée du DVD pour fabriquer des exécutables PS2. Et donc, depuis la version `1.8b`, FreeMCBoot a maintenu sa position comme la méthode la plus sûre et la plus populaire pour exécuter tout type de Homebrew sur une PlayStation 2.

### Développements ultérieurs {.tabs-close}

Une fois qu'il est devenu plus facile d'exécuter du Homebrew, les progrès ont continué sous la forme de polissage des exploits précédents et du développement d'applications Homebrew. Certains de ces derniers ont finalement facilité certaines fonctionnalités liées au piratage, mais ils ont également étendu les capacités limitées du système d'exploitation (c'est-à-dire en fournissant des correctifs sur les jeux), je suppose que cela dépendait finalement des intentions de ses utilisateurs.

Pour nommer quelques exemples notables de développements liés aux homebrews :

- **FreeHDBoot** : Une variante de FreeMCBoot qui s'installe plutôt sur le disque dur, d'où le système tentera également de démarrer.
- **ps2sdk** par le groupe ps2dev [@anti_piracy-ps2sdk] : Le SDK non officiel par défaut pour écrire du Homebrew PS2 sans risque d'atteinte aux droits d'auteur. Le projet remonte à 2000, lorsqu'il se trouvait uniquement sous la forme de bibliothèques éparses pour accéder à des composants particuliers [@anti_piracy-ps2sdk_history] et au fil du temps a été unifié en un seul package pour le bénéfice des développeurs de Homebrew.
- **LaunchELF** par plusieurs auteurs [@anti_piracy-launchelf] : Un gestionnaire de fichiers avec des utilitaires supplémentaires. Il a ensuite été poursuivi sous le nom de 'uLaunchELF' et, enfin, 'wLaunchELF'.
- **Open USB Loader** (OPL) par plusieurs auteurs [@anti_piracy-opl] : Permet de démarrer des images de disques à partir de plusieurs sources (HDD, USB, i.Link et même SMB et NMB sur Ethernet) avec des capacités de correction de bogues.
- **ESR** par ffgriever [@anti_piracy-esr] : Initialement appelée 'Vast CDVDV', il s'agit d'une application qui trompe tout le système (y compris le lecteur DVD) pour exécuter une copie gravée d'un jeu sans modification matérielle. Le contenu original du jeu doit être modifié pour se déguiser en film DVD (afin que le lecteur DVD ne le rejette pas), tandis que l'ESR prend le contrôle de l'Emotion Engine et de l'IOP pour rediriger l'exécution vers le jeu réel.

Les années suivantes verront également de nouveaux exploits découverts :

- **Fortuna** par krat0s [@anti_piracy-fortuna] : Un lanceur Homebrew alternatif publié en 2019 qui repose sur un exploit complètement différent. Cette fois, c'est un débordement de tampon dans l'analyseur d'icônes du gestionnaire de sauvegardes (partie de `OSDSYS`) [@anti_piracy-opentuna]. Son principal avantage est la compatibilité avec les modèles PS2 récents qui ont supprimé la prise en charge des mises à jour externes `OSDSYS` (donc incompatibles avec FreeMCBoot).
- **OpenTuna** par alexparrado [@anti_piracy-opentuna] : Une alternative open-source à Fortuna issue de la rétro-ingénierie de ce dernier.
- **FreeDVDBoot** par cturt [@anti_piracy-freedvdboot] : Une nouvelle vulnérabilité publiée en 2020. Il consiste en un nouveau débordement de tampon dans le lecteur DVD qui peut être exploité pour exécuter un code arbitraire. Les utilisateurs n'ont qu'à graver un DVD avec FreeDVDBoot pour exécuter n'importe quel binaire qu'ils souhaitent, y compris l'installateur de FreeMCBoot ou même ESR (Sony a eu beaucoup de chance que cela n'ait pas été découvert de nombreuses années plus tôt ! [comme d'autres ne l'ont pas été](dreamcast#defeating-it)).

## C'est tout pour le moment

Félicitations et merci d'avoir atteint la fin de l'article ! Pour être honnête, il y avait tellement à dire que je me demande si les lecteurs finirons par être lassés de ce qui a trait à la Playstation après avoir fini cet article.

Sinon, pour rester sérieux, j'espère que vous avez découvert de nouvelles choses après avoir lu cet article et si vous avez le moindre commentaire, n'hésitez pas à [me contacter](code>r ref(about_url, root = TRUE)</code).

À la prochaine !  
Rodrigo
