---
short_title: Arquitetura do PlayStation 2
long_title: Arquitetura do PlayStation 2
long_name: PlayStation 2
name: PS2
subtitle: Ofuscando o resto
date: 2020-04-08
release_date: 2000-03-04
javascript:
  - 'threejs'
generation: 6
top_tabs:
  Model:
    file: international
    caption: "O PlayStation 2 original.<br>Lançado em 04/03/2000 no Japão, 26/10/2000 nos EUA e em 24/11/2000 na Europa"
  Motherboard:
    caption: "Mostrando a revisão 'GH-001' do modelo SCPH-10000 somente lançado no Japão.<br>Graças às doações recebidas, consegui comprar este modelo e tirar uma boa foto que me permite identificar a maior parte dos chips.<br>Eu presumo que o chip no canto inferior direito é a BIOS ROM de 4 MB"
    bib_source: copetti
  Diagram:
    caption: "O design original (implementado na revisão 'SCPH-10000').<br>Cada barramento de dados está rotulado com sua largura e velocidade.<br>Essa arquitetura passou por várias revisões, mais detalhes abaixo"
#Historical
aliases:
  - /projects/consoles/playstation-2/
---

## Uma breve introdução

O PlayStation 2 não foi um dos consoles mais potentes de sua geração, porém conseguiu alcançar um nível de popularidade impensável para outras empresas.

Esta máquina não é nem perto de ser simples como o [primeiro PlayStation](playstation) foi, mas veremos o motivo dela não ter sofrido o mesmo destino de [consoles complexos passados](sega-saturn).

```{r results="asis"}
supporting_imagery()
```

## CPU

No coração deste console encontramos um potente conjunto encapsulado chamado **Emotion Engine** ou 'EE' desenvolvido pela Sony e rodando a **~294,91 MHz** [@cpu-rockin]. Este chipset contém múltiplos componentes, um deles sendo a CPU principal. O restante dos componentes está à disposição da CPU para acelerar algumas tarefas.

### O líder

O núcleo principal é uma CPU compatível com **MIPS R5900** com vários aperfeiçoamentos. Este é o primeiro chip que começa a executar instruções quando o console é ligado. O processador fornece os seguintes recursos:

- **MIPS III ISA**: um conjunto de instruções RISC 64 bits. *Calma, sou eu ou isso é o mesmo conjunto de instruções encontrado em um [console da concorrência](nintendo-64#cpu)?* Não exatamente, a Sony aprimorou o conjunto de instruções adicionando algumas instruções do **MIPS IV** (prefetch e move condicional) junto com sua própria extensão SIMD chamada **instruções multimídia**.
- **32 registradores extras de 128 bits**: outra melhoria. Eles são melhor gerenciados utilizando instruções multimídia e são muito úteis para o processamento de vetores.
  - Estes registradores são acessados através de um barramento de 128 bits, enquanto o resto da CPU usa um barramento interno de 64 bits.
- **Superescalar de duas vias**: até duas instruções podem ser executadas em paralelo.
- **Cache L1 com 24 KB**: dividido em 16 KB para instruções e 8 KB para dados.
  - Ela também implementa uma **função de prefetch** para guardar instruções e dados antes que eles sejam requisitados. Isso é feito incluindo circuitos adicionais que conseguem identificar quais lugares da memória são mais frequentemente requisitados.
- **16 KB de Scratchpad RAM**: também conhecido como 'Fast RAM'.
- **Unidade de gerenciamento de memória**: faz a interface entre o acesso à memória e o resto do sistema.

O núcleo é complementado com uma **unidade de ponto flutuante dedicada** (identificada como 'COP1') que acelera operações com números de ponto flutuante de 32 bits (também conhecidos como `floats` em C). É um bloco peculiar visto que ele não obedece ao padrão IEEE 754, mais evidente com sua falta de `infinity` (computado como `0` no lugar) [@cpu-krysto].

### Uma escolha de memória reconhecida

Próximo ao Emotion Engine estão dois blocos de 16 MB de RAM, dando um total de **32 MB** de memória principal. O tipo de memória usado é **RDRAM** ([*déjà vu!*](nintendo-64#memory-design)) que é acessado por um barramento de 16 bits.

![Design da memória do Emotion Engine. Você consegue adivinhar onde o congestionamento vai surgir.](MemoryArch.png)

À primeira vista, isso pode parecer um pouco decepcionante, considerando que o barramento interno do Emotion Engine tem uma largura de até 128 bits. Porém, os chips de RAM foram estrategicamente posicionados seguindo a **arquitetura dual-channel**, que consiste em conectar ambos os chips usando dois barramentos independentes de 16 bits (um barramento por chip) para melhorar a vazão de dados. A configuração resultante fornece uma vazão teórica de 3,2 GB/sec, portanto pode ter certeza que latência de memória não é um problema neste console!

Em um canto do Emotion Engine há um poderoso **Controlador DMA** ou 'DMAC' que transfere dados entre a memória principal e o Scratchpad (memória de rascunho); ou entre a memória principal e qualquer componente dentro do EE.

Transferências de dados são feitas em lotes de 128 bits, mas aqui fica a parte interessante: a cada oito lotes, o barramento principal é temporariamente desbloqueado. Isso deixa uma pequena janela de tempo para realizar outras transferências DMA em paralelo (até dez) ou deixar a CPU usar o barramento principal. Esse *modus operandi* é chamado **slice mode** e é um dos vários modos disponíveis nesta unidade DMA. Tenha em mente que enquanto o slice mode reduz paradas no barramento principal, ele o faz ao custo de desacelerar a transferência geral do DMA.

### Prevenindo contratempos passados

Quer queiramos ou não, com a quantidade de tráfego ocorrendo dentro do Emotion Engine, esse design vai eventualmente sofrer as consequências da **arquitetura de memória unificada** ou 'UMA' (Unified Memory Architecture). Isto é... múltiplos componentes independentes tentando acessar a memória principal ao mesmo tempo, causando congestionamento. Bem, para corrigir esses problemas, a Sony aliviou o uso constante de memória com os seguintes métodos:

- Envolvendo seus processadores com **bastante cache**. Assim, apenas pedindo acesso à memória principal se for absolutamente necessário.
    - 99% das menções de cache/scratchpad neste artigo serão por esta razão.
- Adicionando um **Write Back Buffer** de 128 bits: muito similar ao [Write Gather Pipe](gamecube#ibms-enhancements), mas ao invés de esperar até que fique 25% cheio, ele vai antes verificar o estado do barramento (i.e. congestionado ou livre).

Isso soa muito conveniente em aplicações que podem se beneficiar da cache, mas e em certas tarefas, como a manipulação de Display Lists, que não devem usar a cache de forma alguma? Por sorte, a CPU fornece um modo de acesso alternativo chamado **UnCached**, que usa **apenas** o Write Back Buffer. Portanto, ciclos não serão desperdiçados corrigindo a cache (resultados de *cache misses*).

Além disso, o modo **UnCached accelerated** também está disponível. Este adiciona um buffer para acelerar a leitura de endereços contínuos na memória.

### Outras partes interessantes

Dentro do mesmo encapsulamento do Emotion Engine, há ainda um outro processador chamado **Unidade de Processamento de Imagem** ou 'IPU' (Image Processing Unit), desta vez feito para **descompressão de imagens**. Como sucessor do [MDEC](playstation#tab-2-3-motion-decoder), a IPU pode ser útil quando um jogo precisa decodificar um filme MPEG2 sem travar a CPU principal.

De forma resumida, o jogo envia fluxos de imagens comprimidas para a IPU (com sorte utilizando DMA) os quais são então decodificados em um formato que a GPU pode exibir. O sistema operacional do PS2 também depende da IPU para fornecer reprodução de DVDs.

Finalmente, a IPU também opera em **texturas de alta resolução** comprimidas, o que economiza uso de CPU e reduz grandes transferências.

## Co-CPUs

Já faz dois anos desde que os rivais apresentaram suas [mais recentes ofertas ao mercado](dreamcast). Se você leu o artigo anterior e acabou de começar a ler este aqui, eu presumo que você *ainda* está esperando 'a coisa' que faz o PS2 ser tão poderoso quanto parecia na época. Agora, deixe-me introduzir um conjunto de componentes *muito* importantes que a Sony colocou no Emotion Engine, as **Unidades de Processamento Vetorial** ou 'VPU' (Vector Processing Units).

### Arquitetura

Uma Unidade de Processamento Vetorial é um pequeno processador independente projetado para operar vetores, e em particular, vetores com quatro `floats`. Esses processadores são tão rápidos que eles gastam apenas **um ciclo por operação**, o que pode ser extremamente conveniente para o processamento de geometria. Apesar disso, eles apresentam um comportamento não padronizado similar à FPU da CPU.

VPUs são feitos dos seguintes componentes:

- Algumas **Memórias da Unidade Vetorial** ou 'VU Mem' (Vector Unit Memory): São usadas como espaço de trabalho para as unidades vetoriais. Elas guardam valores que precisam ser operados e/ou os resultados de operações anteriores.
- Uma **Unidade Vetorial (Vector Unit)**: O núcleo do processador. Contém um pouco de memória (chamada **Micromemória**) para guardar um programa (chamado **Microprograma**) que instrui a unidade sobre como operar os dados encontrados na 'VU Mem'.
  - Ela implementa um **conjunto de instruções 64 bits** e a unidade de execução é **dividida em duas subunidades paralelas**. A primeira multiplica ou adiciona floats, enquanto a outra divide floats ou opera inteiros. Isso permite operar tanto floats quanto inteiros **simultaneamente**.
- Uma **Interface Vetorial (Vector Interface)**: automaticamente descomprime dados vetoriais vindos da memória principal em um formato que a Unidade Vetorial consiga entender. Essa unidade também consegue transferir microprogramas para a micromemória.

### Funcionalidade

Para começar a funcionar, a Unidade Vetorial precisa de um 'pontapé inicial'. Para isso, a CPU principal fica encarregada de fornecer o microcódigo.

Há **duas VPUs** dentro do Emotion Engine, mas elas são organizadas de forma diferente, dando lugar a diferentes usos e otimizações.

#### Unidade de Processamento Vetorial 0 {.tabs.active}

![Arquitetura da VPU0.](VU0.png) {.tab-float}

A primeira VPU, a **VPU0**, está posicionada entre a CPU e a outra unidade vetorial (VPU1). Ela possui um papel 'auxiliar' para a CPU principal.

A VPU0 possui dois modos de operação:

- **Micromode**: Esse é o 'modo tradicional'. A VPU vai executar de forma independente 'microinstruções' de um microprograma guardado na micromemória.
- **Macromode**. A VPU0 se torna o 'COP2' da CPU principal e executa 'macroinstruções', recebidas da CPU principal por um barramento dedicado de 128 bits.
  - Macroinstruções têm a mesma funcionalidade das microinstruções, porém utilizam opcodes diferentes. No entanto, a unidade de execução da VPU não fica mais dividida (o que significa que ela só pode executar uma instrução por vez).
  - Apesar desse modo não utilizar completamente todos os componentes da VPU0, ele ainda acelera as operações vetoriais da CPU. Além disso, em termos de simplicidade, um coprocessador é mais fácil de programar que uma unidade independente (algo que os programadores de PC vão achar útil).

O mapa de memória da VPU0 também tem acesso a alguns registradores e flags da outra VPU, presumivelmente para verificar seu estado ou ler rapidamente os resultados de algumas operações feitas pela outra VPU.

#### Unidade de Processamento Vetorial 1 {.tab}

![Arquitetura da VPU1.](VUP1.png) {.tab-float}

A segunda VPU, a **VPU1**, é uma versão melhorada da VPU0 com o quádruplo de micromemória e memória VU. Além disso, essa unidade inclui um componente adicional chamado **Unidade de Função Elementar** ou 'EFU' (Elementary Function Unit) que acelera a execução de funções exponenciais e trigonométricas.

A VPU1 está localizada entre a VPU0 e a Interface Gráfica (o 'portão' para a GPU), então ela possui barramentos adicionais que fornecem geometria para a GPU o mais rápido possível e sem utilizar o barramento principal.

No entanto, devido à sua localização, a VPU1 **apenas opera em micromode**.

É óbvio que essa VPU foi projetada para operações trigonométricas, e ela pode ser usada como um pré-processador para a GPU. Logo, ela é geralmente responsável pelo fornecimento dos famosos Display Lists.

### Mundos infinitos {.tabs-close}

Uma abordagem útil que pode ser explorada com essas unidades é a **geração procedural**. Em outras palavras, ao invés de construir uma cena utilizando geometria definida por código, deixe que as VPUs a gerem utilizando algoritmos. Neste caso, a VPU calcula **funções matemáticas para produzir a geometria** que é então interpretada pela GPU (i.e. triângulos, linhas, quadriláteros, etc.) e, finalmente, usada para desenhar a cena.

Comparado com o uso de dados explícitos, o conteúdo procedural é ideal para tarefas paralelizadas, libera largura de banda, e é dinâmico (programadores podem definir parâmetros para obter resultados diferentes) [@cpu-green]. Muitas áreas podem se beneficiar fortemente desta técnica:

- **Superfícies complexas** (ex: esferas e rodas).
- **Renderização de mundos** (ex: terrenos, partículas, árvores).
- **Curvas de Bézier** (uma equação muito popular na computação gráfica utilizada para desenhar curvas), elas são convertidas em **caminhos de Bézier** (geometria explícita) e suportam diferentes graus de precisão baseado no nível de detalhe necessário.

Por outro lado, o conteúdo procedural pode ter problemas com animações e, se o algoritmo é muito complexo, a VPU pode não gerar a geometria no tempo exigido.

Em resumo, geração procedural não é uma técnica nova, mas graças às VPUs, essa técnica abre portas para maiores otimizações e gráficos mais sofisticados. Contudo, não é uma técnica simples de implementar e a equipe de P&D da Sony publicou vários artigos descrevendo diferentes abordagens para utilizá-la em seu console.

### Você define o fluxo de trabalho

Com essas novas adições, programadores agora possuem muita flexibilidade para desenvolver seus motores gráficos. Para ajudar nesta tarefa, a Sony gastou recursos adicionais para elaborar e documentar modelos eficientes de pipeline. Os exemplos a seguir são pipelines gráficas otimizadas para diferentes tipos de cargas de trabalho [@cpu-stokes]:

![Design do pipeline paralelo.](Parallel.png){.tabs-nested .active title="Paralelo"}

![Design do pipeline serial.](Serial.png){.tabs-nested-last title="Serial"}

No primeiro exemplo, o design **Paralelo**, a CPU é combinada com a VPU0 em macromode para produzir geometria em paralelo com a VPU1. O conjunto CPU/VPU0 faz uso total do scratchpad e da cache para evitar usar o barramento principal, que a VPU1 depende para buscar dados da memória principal. No final, ambos os grupos de renderização simultaneamente enviam seus respectivos Display Lists à GPU.

O segundo exemplo, o design **Serial**, propõe uma abordagem diferente onde o grupo CPU/VPU0 trabalha como um preprocessador para a VPU1. A primeira etapa irá buscar e processar toda a geometria que a VPU1 vai posteriormente transformar em uma Display List.

Esses foram, até agora, exemplos do ponto de vista teórico, porém para explicar uma implementação mais 'prática', eu vou referenciar um vídeo publicado por Jon Burton que fala sobre o desenvolvimento de um de seus jogos de PS2 [@cpu-burton].

![Crash Bandicoot: The Wrath of Cortex (2001). Partículas formam o fogo da vela e a luz que vem das janelas de vidro.](Crash.jpg) {.open-float}

O antigo diretor da Travellers Tales explicou como seu time obteve um sistema de partículas totalmente encapsulado dentro da VPU1. Em poucas palavras, a VPU1 focou em ler uma base de dados previamente preenchida em sua memória VU, e a base de dados foi usada para calcular as coordenadas de partículas a todo instante sem depender de nenhum outro componente. O resultado da operação poderia ser transformado em Display Lists e enviado imediatamente.

`r close_float_group(with_markdown = TRUE)`

Com essa abordagem, a CPU ficou significativamente descarregada, permitindo-lhe realizar outras tarefas como IA e física.

Há vários outros exemplos por aí, mas resumindo as coisas: cabe agora ao programador encontrar a configuração mais otimizada, e isso é um ponto positivo.

## Gráficos

Considerando todo o trabalho feito pelo Emotion Engine, falta alguma coisa? Na verdade, o último passo: a visualização dos gráficos!

![Final Fantasy X (2001).](ffx.jpg) {.open-float}

Existe um simples porém rápido chip especializado nisso: O **Sintetizador Gráfico** ou 'GS' (Graphics Synthesizer) rodando a **~147,46 MHz**. Ele contém **4 MB de DDRAM** embutido para realizar todo o processamento internamente. Assim, eliminando a necessidade de acesso à memória principal. A RAM embutida é conectada utilizando diferentes barramentos dependendo do tipo de dado exigido.

O GS possui menos recursos que outros sistemas gráficos [analisados anteriormente](gamecube#graphics) neste site. No entanto, é muito rápido no que se propõe a fazer.

`r close_float_group(with_markdown = TRUE)`

### Arquitetura e design

Essa GPU só faz **rasterização** e isso inclui... Gerar pixels, mapear texturas, aplicar iluminação e alguns outros efeitos. Isso significa que não há transformações de vértices (estas são feitas pelas VPUs). Além disso, trata-se de um pipeline de função fixa, então não há [modificações sofisticadas](gamecube#creativity) nem [shaders](xbox#graphics), estamos restritos a um modelo de sombreamento fixo (ex: Gouraud).

![Design do pipeline do Graphics Synthesizer](GS_Pipeline.png)

Parece bem simples né? Bem, vamos nos aprofundar mais e ver o que acontece em cada estágio.

#### Pré-processamento {.tabs.active}

![Estágio pré-processamento.](gs_pipeline/Preprocessing.png) {.tab-float}

O Emotion Engine inicia o Sintetizador Gráfico preenchendo sua DRAM com os materiais exigidos (**Bitmaps de texturas** e **Lookup tables de cores**, sendo o último também chamado 'CLUT'), atribuindo valores nos registradores do 'GS' para configurá-lo e, finalmente, emitindo os comandos de desenho (Display Lists) que vão instruir o GS a desenhar primitivas (pontos, linhas, triângulos, sprites, etc.) em lugares específicos da tela.

Adicionalmente, o GS irá pré-processar alguns valores que serão necessários para cálculos futuros. Um desses números em especial é o valor do **Algoritmo Diferencial Digital**, que será usado para interpolações durante o processo de desenho.

#### Rasterização {.tab}

![Estágio de rasterização.](gs_pipeline/Rasterizing.png) {.tab-float}

Usando os valores calculados anteriormente, o renderizador gera pixels a partir das primitivas. Essa unidade pode gerar 8 pixels (com texturas) ou 16 pixels (sem texturas) simultaneamente, e para cada pixel as seguintes propriedades são calculadas:

- **RGBA**: correspondente ao gradiente de vermelho (Red), verde (Green), azul (Blue) e Alfa (transparência).
- **Z-value**: usado para testes de profundidade em estágios seguintes.
- **Fog**: opcional, usado para simular névoa no ambiente.
- **Propriedade de texturas**: contém o endereço de DRAM da textura dentre outras propriedades (coordenadas, nível de detalhe, filtro, etc.) que serão usadas no estágio seguinte.

Também são feitos **testes de corte (Scissoring Tests)** que descartam polígonos fora do quadro (baseado nos seus valores X/Y), e algumas propriedades de pixel são encaminhadas a um estágio de 'teste de pixel' para verificações adicionais. A **Iluminação** é também fornecida ao selecionar uma das duas opções disponíveis, **Gauraud** e **Flat**.

O pacote é então entregue ao motor de 'mapeamento de texturas', mas cada propriedade é operada por um 'submotor' especializado, o que permite o processamento de diferentes propriedades em paralelo.

#### Texturização {.tab}

![Estágio de mapeamento de texturas](gs_pipeline/Textures.png) {.tab-float}

Esse estágio é movido por uma grande Unidade de Pixel que pode computar até 16 pixels de uma vez, e é aqui que as texturas serão mapeadas nos polígonos (que agora são pixels). Ademais, os efeitos de névoa e anti-aliasing são aplicados aqui.

Mapeamentos de textura são obtidos da DRAM em uma área definida como **buffer de textura**, embora isso seja interligada com uma área separada chamada **buffer de paginação de texturas**, que parece servir como um mecanismo de cache de texturas. CLUTs também são mapeados utilizando este sistema de paginação. Ambos os elementos são retirados com o uso de um **barramento de 512 bits**.

A unidade de pixel executa uma **correção de perspectiva** para mapear texturas nas primitivas (uma grande melhoria considerando a abordagem anterior de [mapeamento afim](playstation#tab-4-5-textures)). Adicionalmente, ela também fornece **filtragem bilinear e trilinear**, o último sendo usado junto com texturas MIP mapeadas.

#### Testes {.tab}

![Estágio de teste de pixels](gs_pipeline/Tests.png) {.tab-float}

Aqui certos pixels serão descartados se eles não atenderem a vários requisitos. Dito isso, os seguintes testes são realizados:

- **Teste de Alfa**: compara o valor de alfa (transparência) de um pixel com o valor 'padrão'. O motivo disso é que em alguns casos o valor de alfa precisa estar dentro de um determinado intervalo ou maior/menor que um valor arbitrário.
- **Teste de Alfa do destino**: verifica o valor de alfa do pixel novamente antes de desenhá-lo no frame buffer.
- **Teste de Profundidade**: compara o valor de Z do pixel com o valor de Z correspondente ao [Z-buffer](nintendo-64#modern-visible-surface-determination). Isso evita o processamento de pixels que ficariam ocultados atrás de outros pixels.

#### Pós-Processamento {.tab}

![Estágio pós-processamento](gs_pipeline/Postprocessing.png) {.tab-float}

O último estágio pode aplicar alguns efeitos sobre nossos novos pixels usando o frame buffer anterior localizado na DRAM local:

- **Alpha Blending**: Mescla cores do buffer atual com o anterior na memória.
- **Dithering**: Altos valores RGBA precisam ser cortados, então o dithering pode ser aplicado para suavizar a perda de precisão.
- **Colour Clamping**: Após aplicar operações como o Alpha Blending, o novo valor RGB pode exceder o intervalo válido (0-255), então o clamping determina um valor dentro do intervalo.
- **Formatação**: Converte o frame buffer final gerado no pipeline em um formato que pode ser guardado em memória.

Finalmente, o novo frame buffer, junto com o Z-buffer atualizado, são escritos na memória usando um **barramento de 1024 bits**.

### Ainda mais pós-processamento {.tabs-close}

Existe um componente dedicado dentro do GS chamado **Controlador CRT Programável** ou 'PCRTC' (Programmable CRT Controller) que envia o frame buffer na memória para a saída de vídeo, para você poder ver o frame na TV. Mas isso não é tudo: ele também contém um bloco especial chamado **Merge Circuit** que permite o Alpha Blend de dois frame buffers separados (útil se os jogos querem reusar o frame anterior para formar o novo). O frame resultante pode ser enviado pelo sinal de vídeo e/ou escrito de volta à memória.

### Modelos melhores

Com tudo o que foi dito, isso certamente trouxe designs melhores para atualizar personagens já famosos. Dê uma olhada neste 'Antes & Depois':

![Crash Bandicoot (1996) para o PS1.<br>732 triângulos.](crash_ps1){.toleft model3d="true"}

![Crash Bandicoot: The Wrath of Cortex (2001).<br>2226 triângulos.](crash_wrath_ps2){.toright model3d="true"}

Aqui estão personagens de novas séries de jogos. Estes foram modelados com altos níveis de detalhe desde o início:

![Kingdom Hearts (2002).<br>2744 triângulos.](sora_kh1_ps2){.toleft model3d="true"}

![Dragon Quest VIII (2004).<br>2700 triângulos.](hero_ps2){.toright model3d="true"}

Vale a pena mencionar que jogos como *Dragon Quest* implementaram um modelo de iluminação personalizado chamado **Cel Shading** (um termo que eu já mencionei [antes](gamecube#creativity)), porém, nos meus artigos anteriores eu expliquei que a GPU era a principal responsável por isso. No caso do PS2, os cálculos de cores exigidos são presumivelmente feitos pelo Emotion Engine, dado que o GS não é tão flexível quanto outras GPUs.

### Saída de Vídeo

Como dito anteriormente, o PCRTC envia o frame buffer pelo sinal de vídeo. A interface pode transmitir vídeo utilizando uma ampla gama de formatos (para funcionar com TVs de qualquer região geográfica) [@graphics-opl]:

- **PAL**: envia até 640x512 pixels a 50 Hz, tanto em modo progressivo (576p) quanto em entrelaçado (576i).
  - Nenhum jogo encontrado no mercado usa 576p. Embora alguns suportam modo progressivo, eles o fazem em modo 480p.
- **NTSC**: até 640x448 pixels a 60 Hz, tanto em modo progressivo (480p) quanto em entrelaçado (480i).
- **VESA**: Até 1280x1024 pixels.
- **DTV**: até a *enorme quantidade* de 720x480 pixels em modo progressivo ou 1920x1080 em modo entrelaçado.
  - Ajustando as configurações do PCRTC, um jogo também pode forçar a saída de vídeo em 1080p. Porém, este modo não está documentado e, portanto, está sujeito a comportamentos imprevisíveis.
  - Isso significa que o PS2 consegue fornecer uma 'imagem HD'? Tecnicamente... sim, mas não acredito que a maior parte dos estúdios de jogos arriscaram a perda de desempenho por um formato que ainda não era popular.

![Lado direito do console [@photography-amos], visto de trás. Exibindo a porta A/C, porta de áudio digital e saída Multi AV.](photos/ps2_back.png) {.open-float}

Há muitos modos para escolher, porém, tudo se resume ao formato adotado durante o início dos anos 2000, o que diminui as opções para PAL e NTSC. Além disso, embora o padrão PAL tenha fornecido uma resolução maior que o NTSC, algumas versões europeias de jogos NTSC recorreram ao letterboxing para mascarar as linhas horizontais não utilizadas e diminuíram a taxa de atualização da imagem do jogo para encaixar no limite de 50 Hz (Eu os chamo de 'más adaptações'!).

`r close_float_group(with_markdown = TRUE)`

A porta da saída de vídeo (**Multi A/V**) é muito conveniente. Ela carrega os sinais RGB, Componente, S-Video e Composto. Logo, todos os sinais importantes estão disponíveis sem a necessidade de adaptadores proprietários ou modificações internas.

## Áudio

O novo chip de áudio é uma atualização incremental da antiga [SPU](playstation#audio) chamada... **SPU2**! Melhorias incluem a adição de **2 MB de memória interna** e **48 canais disponíveis** (duas vezes mais que a quantidade original).

A SPU2 é feita de dois processadores de som em seu interior, referidos como **CORE0** e **CORE1**, e roda a ~36,86 MHz. Cada um processa **24 canais**.

Curiosamente, esses ainda são dois processadores independentes e para os configurar, você precisa alterar seus registradores. No entanto, a Sony avisou os desenvolvedores que os dois conjuntos de registradores devem ser configurados com 1/48000 segundo de intervalo. Se você se apressar demais, o comportamento da SPU2 pode se tornar imprevisível!

A SPU2 herda os mesmos efeitos disponíveis na SPU original. A memória fornecida é usada como uma 'área de trabalho': você pode guardar dados brutos de formas de ondas e alocar espaço extra para processá-los e aplicar efeitos sobre eles.

Finalmente, o chip pode mixar todos os canais para fornecer saída **estéreo**. Agora, aqui fica a parte interessante: a SPU2 pode se alimentar da amostra estéreo mixada como uma nova entrada, e isso permite que o EE a acesse (para mixá-la com ainda mais áudio, por exemplo), ou permite continuar com a adição de mais efeitos (como reverberação, eco e atraso).

![Kingdom Hearts II (2005). Sem reverberação.](goomy_noreverb){.toleft video="true"}

![Kingdom Hearts II (2005). Com reverberação.](goomy){.toright video="true"}

### Saída de áudio

O sinal de áudio é transmitido através de dois meios:

- **Áudio Digital**: Denominada como a Interface Digital Sony/Philips ou 'S/PDIF' (Sony/Philips Digital Interface).
- **Áudio Analógico**: Atravessa o conversor digital-analógico e termina na porta Multi A/V.

## E/S (Entrada e Saída)

A E/S do PS2 não é complicada, mas várias revisões deste console mudaram completamente várias interfaces internas e externas.

Para começar, existe um processador dedicado que arbitra a comunicação entre os diferentes componentes, e essa CPU não é nenhuma outra além do **núcleo original baseado no MIPS R3000** encontrado no [PlayStation 1](playstation#cpu). Desta vez, ela é chamada **IOP** e roda a 37,5 MHz usando um barramento de 32 bits [@io-buses].

A IOP comunica com o Emotion Engine usando uma interface de E/S especializada chamada **System Interface** ou 'SIF', e ambos usam suas respectivas unidades de DMA para transferir dados entre si. A IOP também contém sua própria memória utilizada como um buffer.

A IOP dá acesso às portas frontais, ao controlador de DVD, à SPU2, à BIOS ROM e ao slot de placas de PC.

### Compatibilidade herdada

Ao incluir a CPU original, podemos suspeitar que a compatibilidade de PS1 eventualmente aconteceria de alguma forma. Convenientemente, ocorre que a IOP inclui o resto dos componentes que formam o subsistema da CPU do PS1. No mais, este núcleo pode diminuir sua frequência para rodar na velocidade do PS1. Infelizmente, a SPU2 mudou demais para o PS1 e, devido a isso, o Emotion Engine é 'reprogramado' para emular a antiga SPU.

Em revisões posteriores deste console, a IOP foi substituída por um **PowerPC 401 'Deckard'** e **4 MB de SDRAM** (2 MB a mais que antes), mantendo a retrocompatibilidade, porém através de software.

### Interfaces disponíveis

Este console manteve as portas frontais anteriores incluídas no PlayStation original, e também apresentou algumas interfaces 'experimentais' que pareciam muito promissoras no início.

![Frente do PS2 mostrando portas comuns incluindo controles e Memory Cards, mais os novos USBs e o i.Link [@photography-amos].](photos/ps2_front.png) {.open-float}

A adição mais popular: **duas portas USB 1.1**. Amplamente adotada por acessórios de outras fabricantes, essas portas foram mantidas em todas as revisões futuras do console.

E quanto às 'experimentais'? Para começar, havia inicialmente uma **porta i.Link** frontal (também conhecida como IEEE 1394, ou 'FireWire' no mundo da Apple). Essa porta foi utilizada para conectar dois PS2s para possibilitar partidas multijogador local, porém foi removida após a terceira revisão (presumivelmente foi trocada pela 'placa de rede Network Adaptor', mais detalhes abaixo).

`r close_float_group(with_markdown = TRUE)`

Na parte de trás do console também havia um slot para **placas de PC**, e você podia comprar uma 'placa Network Adaptor' da Sony que fornecia duas portas extras. Uma para conectar um cabo ethernet, e outra para conectar uma 'Unidade de Disco Rígido' (Hard Disk Drive Unit) externa e proprietária, também vendida pela Sony. Ter um disco rígido permitiu que jogos guardassem dados temporários (ou instalá-los permanentemente) para obter tempos de carregamento mais rápidos. No entanto, apenas alguns poucos jogos usaram esse recurso.

![Parte traseira do PS2 mostrando o compartimento do disco rígido (tampa removida) [@photography-amos].](photos/back_bay.png){.tabs-nested .open-float .tab-float .active title="Compartimento"}

![Network adaptor (adaptador de rede) visto de frente [@photography-amos]. Este modelo em particular fornece portas modem e ethernet.](photos/harddrive_adaptor_front.png){.tab-nested title="Parte da Frente"}

![Network adaptor visto de trás [@photography-amos], com um disco rígido inserido.](photos/harddrive_adaptor_back.png){.tab-nested title="Parte de trás"}

![Parte traseira do modelo slim com a porta ethernet fixa visível.](photos/ps2_slim_back.jpg){.tabs-nested-last title="Slim"}

Em revisões posteriores, a porta PCMCIA foi substituída por um **Compartimento de Expansão (Expansion Bay)** onde um disco rígido de 3,5 polegadas poderia ser instalado dentro do console. Você precisava comprar antes um **Network adaptor** que não apenas fornecia as portas de modem e/ou ethernet (dependendo do modelo), mas também incluía as conexões necessárias para um disco rígido ATA-66. Revisões 'Slim' removeram completamente essa funcionalidade, mas deixaram uma porta ethernet permanentemente instalada na parte de trás do console. Além disso, a nova revisão adicionou uma nova 'porta' frontal, o **sensor infravermelho**.

`r close_float_group(with_markdown = TRUE)`

O transceptor ethernet fornecido suporta taxas de transferência de até 100 Mbps (12,5 MB/s). Porém, a **taxa observada é notoriamente menor** (diminui para até 2 MB/s em alguns casos). A explicação para isso é relativamente simples: para conseguir uma comunicação de rede utilizável, é necessário implementar todas as camadas do padrão 'Modelo OSI', e o transceptor é apenas uma peça do quebra-cabeça. O resto é geralmente delegado à IOP (logo, feito em software), mas o desempenho limitado da IOP [@io-bottleneck] resulta em gargalos.

### Acessórios interativos

A nova versão do controle deles, o **DualShock 2**, é uma versão ligeiramente melhorada do DualShock.

![Controle DualShock 2 [@photography-amos].](photos/dualshock2.png){.tabs-nested .open-float .tab-float .active title="DualShock 2"}

![Memory Card oficial (modelo de 8 MB) [@photography-amos].](photos/memorycard.png){.tabs-nested-last title="Memory Card"}

Durante os dias do PlayStation original, múltiplas revisões do controle original foram lançadas com diferentes funcionalidades (o que também trouxe fragmentação ao mercado). Agora, em benefício aos desenvolvedores, havia um único controle que unificava todas as propriedades anteriores.

Comparado com o DualShock, a nova versão possui uma pequena mudança de design, incluía duas alavancas analógicas e dois motores de vibração para uma entrada e feedback com maior riqueza, respectivamente.

`r close_float_group(with_markdown = TRUE)`

Próximo ao slot de controle está o **slot de Memory Card** que é compatível com os cartões do PS1 e do PS2. Os novos cartões de memória incorporaram circuitos extras para fins de segurança chamados **MagicGate**, que permite que os jogos bloqueiem transferências de dados entre diferentes Memory Cards.

## Sistema Operacional (SO)

Há um chip **ROM de 4 MB** instalado na placa-mãe que armazena uma grande quantidade de código usado para carregar um menu de shell que os usuários podem interagir, mas ele também fornece chamadas de sistema para simplificar o acesso de E/S [@cpu-rockin], nos quais os jogos dependem.

![Animação de splash após ligar o console.](bios/animated.jpg){.tabs-nested .active title="Animação de boot (inicialização)"}

![Logo do PS2 visível após um jogo válido de PS2 ser inserido.](bios/game_splash.jpg){.tabs-nested-last title="Disco válido"}

Durante a inicialização, a CPU irá executar instruções na ROM que irão:

1. Inicializar o hardware.
2. Carregar um **Kernel** na RAM, que lidará com chamadas de sistema e também fornecer suporte a multi-threading (cooperativo e baseado em prioridades).
3. Iniciar o processador IOP e enviar-lhe **módulos**; estes permitirão que a IOP lide com o hardware deste console. No final, a IOP será colocada em um estado de 'aguardando comandos'.
    - O uso de módulos permite que a Sony lance novas revisões de hardware do PS2 sem mudar a IOP, reduzindo os custos de produção.
3. Carregar a ''OSDSYS', o programa que mostra a animação de splash e o menu do shell.

### Shell interativo

A funcionalidade do shell do PS2 está praticamente em pé de igualdade com os outros consoles da 6ª geração.

![Menu inicial. Aparece quando não há disco inserido.](bios/menu.jpg){.tabs-nested .active title="Menu"}

![Navegador do Memory Card.](bios/mem_list.jpg){.tab-nested title="Navegador"}

![Navegador de jogos salvos. Ele aparece após selecionar um Memory Card.](bios/save_list.jpg){.tab-nested title="Jogos salvos"}

![Editor de jogos salvos, exibido após selecionar um jogo salvo.](bios/save_editor.jpg){.tab-nested title="Editor"}

![Configurações do Sistema.](bios/options.jpg){.tabs-nested-last title="Opções"}

O shell apresenta algumas seções práticas que permitem a realização de operações do dia-a-dia, como a manipulação de jogos salvos no Memory Card. Ele também fornece algumas opções excepcionais, como alterar o modo de vídeo atual.

## Jogos

É sem precedentes o nível de popularidade que este sistema alcançou durante a primeira década dos anos 2000, e no final do seu tempo de vida (2013, depois de 13 anos!) o catálogo de jogos continha 1850 títulos [@games-antista].

![Mr Moskeeto (2001). Toda vez que alguém discute sobre a abundância de jogos de PS2, eu me lembro desse jogo.](mr_moskeeto.jpg) {.open-float}

O que aconteceu aqui é realmente impressionante. O PS2 não possui uma arquitetura 'amigável para programadores' (da perspectiva de um programador de PC), porém com essa quantidade de jogos desenvolvidos, eu também me pergunto se havia mais fatores envolvidos (como 'facilidades de licenciamento', baixos custos de distribuição, formato compacto, etc.).

`r close_float_group(with_markdown = TRUE)`

### Ecossistema de desenvolvimento

A Sony forneceu o hardware e o software para ajudar no desenvolvimento de jogos.

No lado do software, havia o **PlayStation 2 SDK** que incluía [@games-sdkkit]:

- O toolchain do Emotion Engine: Um conjunto de compiladores, assemblers, linkers, e debuggers **C** e **C++** usados para controlar cada elemento do EE. A CPU principal foi programada principalmente usando C/C++, porém, componentes críticos de desempenho como as unidades vetoriais foram programados em assembly (microcode/macrocode).
  - O pacote também incluiu um 'simulador Emotion Engine' que poderia realizar testes aproximados do código sem enviá-lo ao hardware real, embora o simulador não fosse tão preciso quanto o chip físico do EE.
  - Todas essas ferramentas funcionaram no Linux, Solaris e Windows. Esta última variante rodava dentro do ambiente Cygnus.
- Bibliotecas de baixo nível: Eram a interface de várias funções do sistema (através de chamadas à BIOS).
- Ferramentas de 'análise' para elaborar um perfil do desempenho de utilização.
- Ferramentas adicionais para conectar com o hardware de desenvolvimento oficial.

No lado do hardware, a Sony forneceu hardware dedicado aos estúdios para executar e realizar debug de jogos internamente. Os kits de desenvolvimento (devkits) iniciais eram um conjunto de placas expostas empilhadas que replicavam o hardware ainda não lançado do PS2. Os kits posteriores (chamados **Development Tool**), possuíam uma aparência mais apresentável, E/S aprimorada e combinavam um workstation (rodando RedHat 5.2) com o hardware do PS2 para compilar e instalar o jogo dentro do mesmo kit [@games-devkit].

A combinação do Devkit, do SDK oficial e do Codewarrior (um IDE famoso) era uma das configurações mais populares.

### Mídia

O leitor de discos consegue ler tanto DVDs e CDs, então os jogos poderiam ser distribuídos usando um dos dois formatos, mas por razões óbvias você encontrará a maioria dos títulos no formato DVD.

![Kingdom Hearts II (2005). Embalagem de jogo e disco comuns no varejo.](kh2_box.jpeg) {.open-float}

DVDs podem guardar até **4,7 GB** de dados no caso do DVD-5 (o 'subformato' mais comum) ou até 8,5 GB no caso do DVD-9 (versão dual-layer, menos comum) [@games-dvd]. Na verdade, há um terceiro formato, o DVD-10, que é de dupla face, mas nenhum jogo o utilizou.

Devido ao tipo de mídia usada, não apenas jogos poderiam ser rodados, mas filmes também. Agora, um decodificador é necessário para que o formato de um filme DVD possa ser lido, e para isso, o PS2 inicialmente incluiu as partes necessárias para tal instaladas no Memory Card (este é, afinal, um meio de armazenamento como qualquer outro), porém modelos posteriores do console vieram com o software de DVD pré-instalado na ROM da BIOS.

`r close_float_group(with_markdown = TRUE)`

Em termos de velocidade, CD-ROMs são lidos em velocidade 24x (logo a 3,6 MB/s) e DVD-ROMs são lidos em velocidade 4x (5,28 MB/s) [@games-transfer].

### Serviços de rede

Como você pôde observar, os recursos de rede desses consoles não foram padronizados até revisões posteriores, que chegaram quatro anos após o lançamento da primeira versão. Da mesma forma, os estúdios de jogos estavam encarregados de prover a infraestrutura necessária se eles decidirem fornecer serviços online (como partidas multijogador). Anos mais tarde, a Sony implantou o **Sistema de Autentificação de Rede Dinâmica** ou 'DNAS' (Dynamic Network Authentication System), e isso não era um servidor online, mas um sistema de autentificação para impedir que jogos pirateados conectassem online.

### Um tipo de jogo incomum

Além de todos esses jogos com seus *gráficos sofisticados*, a Sony lançou uma distribuição Linux baseada no 'Kondara' (que por sua vez é baseada no Red Hat 6) disponível em dois DVDs (o primeiro disco chamado 'Runtime Environment' e o segundo chamado 'Software Packages') junto com um adaptador VGA, teclado e mouse USB, e alguns manuais de desenvolvedor. Esse pacote era conhecido como **Kit Linux** e você podia executar o SO iniciando o primeiro DVD e então prosseguir da mesma forma que em qualquer ambiente Linux *old school*. Você obviamente precisava de um disco rígido instalado no console para instalar a distribuição Linux. Uma vez instalada, o primeiro DVD sempre era necessário para inicializar o SO.

O Kit Linux incluía compiladores tendo como alvo o EE (gcc 2.95.2 com glibc 2.2.2) e assemblers para as unidades vetoriais, junto com um sistema gráfico de janelas (XFree86 3.3.6) 'acelerado' no Sintetizador Gráfico [@games-linux]. De modo geral, isso parece um ambiente interessante. De fato, um dos artigos de pesquisa que eu li para escrever este artigo foi feito com essa configuração.

## Anti-Pirataria e Homebrew

Há bastante coisa para falar aqui, então vamos começar com o leitor de DVD.

### Proteção contra cópia

Essa seção foi particularmente preocupante para os estúdios de jogos, visto que este console usava um formato de disco acessível para guardar os jogos e havia um grande risco de pirataria.

![Essa tela de erro pode aparecer se o leitor está com defeito... ou se uma cópia pirata foi inserida.](bios/rsod.jpg) {.open-float}

Quando o SO carrega um jogo, ele o faz enviando comandos específicos para o leitor de DVD. Os comandos especificamente usados para ler o conteúdo de um jogo comportavam-se de forma muito diferente dos comandos padrões de DVD (i.e. para ler um filme em DVD). Acontece que os jogos autorizados contêm um 'arquivo de mapeamento' fora de alcance na seção interna do disco que indexa o sistema de arquivos por nome, posição e tamanho. Quando o DVD é solicitado a ler um disco de jogo, ele sempre vai navegar pelo disco usando um arquivo de mapeamento, o que significa que uma cópia pirata de um jogo, que não poderia incluir o arquivo de mapeamento, será impossível de ser lida. Isso foi complementado por um sistema de bloqueio de região que impedia que jogos importados funcionassem em um console de região diferente.

`r close_float_group(with_markdown = TRUE)`

### Vulnerabilidades descobertas

Tendo explorado as partes mais críticas deste console, vamos dar uma olhada em vários métodos descobertos que poderiam contornar os mecanismos de proteção.

#### Modchips {.tabs.active}

Como qualquer outro console de sua geração (e da geração passada) que usava um sistema baseado em disco, era uma questão de tempo até que outras empresas realizassem engenharia reversa do subsistema de DVD. O objetivo aqui era encontrar uma vulnerabilidade (exploit) útil que poderia forçar o leitor a navegar pelos arquivos do sistema sem a necessidade de um arquivo de mapeamento fora de alcance.

Isso eventualmente aconteceu na forma de **modchips**, o que também contornou as restrições de bloqueio de região.

#### Trapaças {.tab}

Junto com os modchips, que exigiam habilidades de solda para instalar, discos não autorizados porém 'legítimos' surgiram no mercado. Estes permitiam contornar a proteção de região e o uso de trapaças no jogo por meio de pequenas alterações no SO. Além disso, 'discos de trapaça' tinham a vantagem de não precisar modificar o console. Suponho que o melhor exemplo a ser mencionado é o *CodeBreaker*.

#### Disc swapping (troca de discos) {.tab}

No meio dos últimos avanços, surgiu mais um truque. Desta vez, explorando a forma como o leitor lida com setores defeituosos. **Swap Magic** parece como um outro disco 'legítimo', mas o 'jogo' presente nele diz ao DVD para ler um executável inexistente localizado deliberadamente em um setor defeituoso, o que interrompe o leitor por completo [@anti_piracy-hacking]. Essa janela de oportunidade permitiu que usuários trocassem o disco por um não-legítimo. Então o Swap Magic, ainda carregado em memória, inicializa o executável principal do novo disco, carregando um jogo de verdade no final do processo. Tudo isso é feito enquanto o leitor de CD/DVD ainda acredita que um disco legítimo foi inserido.

Isso não requer necessariamente uma alteração no console. Porém, dependendo do modelo, a carcaça externa do PS2 terá que ser manipulada para bloquear os sensores de remoção de mídia do leitor. Em alguns casos, colocar algodão em alguns lugares será o suficiente.

#### Overflow do PS1 {.tab}

O PS2 guarda um arquivo de base de dados chamado `TITLE.DB` no Memory Card que contém informações usadas para otimizar a emulação de jogos de PS1 [@anti_piracy-grand]. Quando um jogos de PS1 é inserido, o SO busca o arquivo de base de dados e carrega o arquivo inteiro em memória em um endereço fixo (*strike um*). O analisador de informações é implementado utilizando o **`strncpy()`**, uma função em C que copia strings (cadeia de caracteres) de um lugar para outro.

Para pessoas familiarizadas com C, vocês provavelmente já adivinharam onde eu vou chegar com isso. O fato é que `strncpy()` não sabe o quão longo é uma string, então a menos que ela seja finalizada (colocando `\0` no final da cadeia) o processo de cópia vai continuar 'para sempre' (com resultados imprevisíveis!). Felizmente, essa função contém um parâmetro opcional que especifica o número máximo de bytes a serem copiados, protegendo a cópia de buffer overflows. Por mais ridículo que possa parecer, **a Sony não usou esse parâmetro**, embora cada entrada da base de dados tenha um tamanho fixo de 256 bytes (*strike dois*).

Após uma inspeção mais detalhada da RAM, ocorre que TITLE.DB é copiado **próximo de um registrador salvo**, o `$ra`, que indica o endereço de retorno após a função atual em execução finalizar (*strike três*), o que levou à vulnerabilidade conhecida como **The independence exploit**: faça um TITLE.DB com uma string comprida, inclua um executável dentro dele e faça a string de forma que o `$ra` seja sobrescrito para apontar para o executável. Se você conseguir enviar este arquivo para seu Memory Card (através de outro exploit ou um adaptador USB no PC) você conseguiu um Homebrew launcher simplificado.

Após o lançamento das revisões slim, a vulnerabilidade foi corrigida (*me pergunto como*). Curiosamente, este caso não foi o último [descuido grave](wii#the-fall-of-encryption) que expôs código desleixado.

### Um desbloqueio de software semi-permanente {.tabs-close}

Há algum tempo, foi descoberto que a BIOS deste console pode ser atualizado usando o Memory Card, e que essa função nunca foi usada na prática, mas também não foi removida (pelo menos durante boa parte do período de vida do console). Com isso, hackers perceberam que se eles encontrassem um jeito de instalar softwares específicos no Memory Card, então a BIOS sempre iria carregá-los durante a inicialização. Essa descoberta levou ao **Free MCBoot**, um programa apresentado ao console como 'dados de atualização' que substitui o shell original com um que pode executar **Homebrew**.

Tenha em mente que essas alterações não são permanentes, um Memory Card com o 'Free MCBoot' instalado deve ser inserido antes da inicialização do console. Além disso, este software precisa ser instalado de alguma forma, então um outro exploit (ex: disc swapping) é necessário para inicializar o instalador.

### Mais truques de disco

No mesmo ano após o lançamento do Free MCBoot, um outro truque foi descoberto: Disfarçar jogos como se fossem filmes em DVD, permitindo efetivamente que cópias não autorizadas de jogos possam ser lidos sem a necessidade de um modchip.

Isso apenas exigia a alteração da imagem do jogo com a adição de metadados e partições somente usadas por filmes em DVD. Então, quando a cópia gravada é inserida no console, o leitor não irá rejeitá-lo, mas o jogo também não será executado. Porém, com a ajuda de um programa Homebrew chamado **ESR**, o jogo consegue ser inicializado.

## Isso é tudo pessoal

Parabéns e obrigado por chegar ao fim do artigo! Para ser honesto, havia tanta coisa para falar que eu me perguntei se os leitores iriam eventualmente se cansar de conteúdos relacionados ao PlayStation depois de terminar isso.

De qualquer forma, e falando sério, eu espero que você tenha descoberto novas coisas após ler este artigo e se você tem qualquer comentário, não hesite em [falar comigo](code>r ref(about_url, root = TRUE)</code).

Até a próxima!  
Rodrigo
