---
short_title: Arquitetura do PlayStation
long_title: Arquitetura do PlayStation
long_name: PlayStation
name: PS1
subtitle: Um recém-chegado promissor
date: 2019-08-08
release_date: 1994-12-03
generation: 5
javascript:
  - 'threejs'
top_tabs:
  Models:
    - 
      title: "Original"
      caption: "O PlayStation original.<br>Lançado em 03/12/1994 no Japão, 09/09/1995 na América do Norte, 29/09/1995 na Europa."
      file: original
      active: true
    - 
      title: "<em>Slim</em>"
      caption: "O PS One (revisão <em>slim</em>).<br>Lançado em 07/07/2000 no Japão, 19/09/2000 na América do Norte, 29/09/2000 na Europa. <br>Isso é o que se obtém após colocar tantos CI personalizados em um único componente."
      file: slim
  Motherboard:
    caption: "Exibindo o modelo \"SCPH-1000\".<br>Os chips restantes são mostrados na parte traseira.<br>Modelos posteriores incluíram SG-RAM em vez de VRAM e removeram a maioria das entradas/saídas externas de vídeo."
  Diagram:
    caption: "A Unidade de Interface de Barramento também está conectada a portas especiais da GPU e da SPU."
#Historical
aliases:
  - /projects/consoles/playstation/
---

## Uma breve introdução

A Sony sabia que o hardware 3D poderia se tornar muito complicado para ser desenvolvido. Assim, seu console estreante manteve seu projeto *simples* e *prático*... Embora isso tivesse um custo!

```{r results="asis"}
supporting_imagery()
```

## CPU

Esta seção analisa o **Sony CXD8530BQ**, um dos dois grandes chips que o console possui. É o que hoje chamamos de "*System-on-Chip*" (SoC — Sistema-no-Chip, em tradução livre).

### As origens

O processador principal é um daqueles "x projetado por y, com base em z e adquirido de forma secundária por w", o que é um pouco denso para resumir em algumas frases. Então, por que não começamos com um pouco de contexto histórico?

#### Um pouco de história {.tabs.active}

Os primeiros anos da década de 90 foram marcados por uma mudança no destino de muitas CPUs populares. As CPUs de 8 bits, como a [Z80](master-system#cpu) e a [6502](nes#cpu), que antes eram líderes de mercado, já não estavam mais em destaque. O famoso [68000](mega-drive-genesis#cpu) da Motorola, juntamente com outros [projetos de 16 bits](super-nintendo#cpu) que fizeram sucesso no final dos anos 80, tornaram-se candidatos à substituição. Mesmo nessa época, no mundo dos PCs, Tanenbaum, em seu debate celebrado com Torvalds, deu apenas *mais cinco anos* de vida para a arquitetura x86 da Intel no mercado doméstico.

À primeira vista, pode parecer que o desenvolvimento tecnológico atingiu um limite naquele momento. Mas, na realidade, havia uma nova onda de CPUs pouco conhecidas que faziam seu caminho para dispositivos populares. Muitos desses projetos tiveram origem na academia, e existiam para provar determinados conjuntos de ideias de concepção. Exemplos de CPUs da próxima geração incluídos neste ponto:

- **MIPS**: adotado pela Silicon Graphics Incorporated (destinado a estações de trabalho gráficas).
- **PowerPC**: adotado pela Apple (destinado ao mercado de computadores pessoais).
- **SPARC**: criado pela Sun Microsystems (destinado para servidores e estações de trabalho empresariais).
- **ARM**: criado pela Acorn (destinado ao mercado consumidor e estações de trabalho).
- ... e muitos outros chips "microcontroladores" ainda não concluídos ou adotados por qualquer indústria. Como o **SH da Hitachi** ou o **V810 da NEC**, que para surpresa deles, foram posteriormente escolhidos para o [Sega Saturn](sega-saturn) e o [Nintendo Virtual Boy](virtual-boy).

Todos esses processadores tinham algo em comum: seguiram a disciplina do **Reduced Instruction Set Computer** (RISC), que mudou radicalmente a maneira como estes chips precisavam ser projetados e programados. Uma regra das CPUs RISC ditava que uma única instrução não pode misturar operações de memória com operações de registrador, permitindo que projetistas de hardware simplifiquem o circuito que executa instruções... e, em seguida, aprimorem-no com técnicas de paralelismo.

#### A MIPS e a Sony {.tab}

No final dos anos 80, os processadores MIPS se tornaram um tópico de conversa após serem adotados (e posteriormente adquiridos) pela Silicon Graphics Incorporated (SGI) para equipar seus equipamentos. A SGI foi uma inovação influente no mercado de gráficos de computador, especialmente com o desenvolvimento de [*pipelines* de vértices acelerados por hardware](nintendo-64#graphics), cuja tarefa era originalmente realizada por software (dentro da CPU).

Durante o desenvolvimento do PlayStation, a MIPS estava oferecendo os processadores da **série R3000A**. Tratava-se de máquinas de 32 bits e era parte de seu catálogo de baixo custo. Consequentemente, embora o R3000A não fizesse parte da linha principal (ao contrário do R4000, que [outros](nintendo-64#cpu) escolheriam mais tarde), era um investimento atraente em termos de custo.

A Sony projetou seus chips de áudio e de gráficos internamente, mas ainda precisava de um chip principal que pudesse conduzir os dois. A CPU escolhida tinha que ser poderosa o suficiente para mostrar as *impressionantes* capacidades dos chips da Sony, mas também acessível para manter o console a um preço competitivo.

#### A LSI e a encomenda {.tab}

Ao mesmo tempo, a **LSI Logic** (uma fabricante de semicondutores) estava fornecendo um programa "construa sua própria" CPU para empresas. Este serviço era chamado de **CoreWare** e permitia que os clientes construíssem seus próprios pacotes de CPU escolhendo uma série de blocos de circuito [@cpu-lsi]. Parte da biblioteca da CoreWare incluía o bloco "CW33300", um núcleo de CPU derivado do LSI LR33300, um chip de CPU genérico que a LSI também comercializava.

Agora, onde estou querendo chegar com isso? Ocorre que o LR33300 e o CW33300 da LSI são uma **versão compatível ao nível binário da família MIPS R3000A**. Suas arquiteturas diferem ligeiramente em algumas áreas, mas a interface de programação (ISA MIPS) permanece a mesma.

No final, a Sony contratou a LSI para construir seu pacote de CPU. Eles escolheram o CW33300, alteraram alguns bits e combinaram com outros blocos para formar o chip encontrado na placa-mãe do PlayStation.

### O produto oferecido {.tabs-close}

O núcleo da CPU resultante opera a **33,87 MHz** e possui:

- A ISA **MIPS**: a primeira versão do conjunto de instruções MIPS. Entre outras coisas, as palavras têm comprimento de 32 bits e o conjunto de instruções inclui instruções de multiplicação e divisão.
- **32 registradores de propósito geral** e **2 registradores de multiplicação/divisão**: eles também têm 32 bits. Um dos registradores de propósito geral é sempre zero (`R0`), o que é comum em processadores RISC.
- **Barramento de dados de 32 bits**: no PS1, o barramento de dados se bifurca em dois barramentos.
  - **Barramento principal** (32 bits) → conecta o MDEC e a GPU.
  - **Barramento secundário** (16/8 bits) → conecta o restante dos chips de E/S. Esse barramento é conectado pela **Unidade de Interface do Barramento** (*Bus Interface Unit*), que também permite o acesso às portas especiais da GPU e da SPU.
- **Barramento de endereço de 32 bits**: até 4GB de memória física (ou seja, RAM, E/S mapeada em memória, etc.) podem ser acessados.
- ***Pipeline* de 5 estágios**: até cinco instruções podem ser executadas simultaneamente (uma explicação detalhada pode ser encontrada em um [artigo anterior](sega-saturn#cpu)).
- **4 KB de cache de instruções**: ele também pode ser "isolado", permitindo que o programa manipule a cache de instruções diretamente.
- Surpreendentemente, **não há cache de dados**. Os **1 KB de memória** normalmente usados para o cache de dados são mapeados para um endereço fixo [@cpu-mame_cpu]. Essa área também é chamada de ***Scratchpad*** (SRAM rápida).

Para fazer algo significativo, a Sony forneceu **2 MB de RAM** para uso geral. Curiosamente, eles instalaram chips ***Extended Data Out*** (EDO) na placa-mãe. Esses chips são ligeiramente mais eficientes do que uma DRAM típica, obtendo menor latência.

### Assumindo o controle da CPU

Em algum momento, qualquer subsistema (gráfico, áudio ou CD) exigirá grandes quantidades de dados em alta velocidade. No entanto, a CPU nem sempre conseguirá acompanhar a demanda.

Por esse motivo, o controlador de CD-ROM, MDEC, GPU, SPU e a porta paralela têm acesso a um **controlador DMA** exclusivo sempre que precisam. O DMA assume o controle do barramento principal e realiza uma transferência de dados. A taxa resultante é muito mais rápida do que se dependesse da CPU, embora ela ainda seja necessária para configurar uma transferência do DMA.

Além disso, tenha em mente que quando o DMA é ativado, a CPU não pode mais acessar o barramento principal. Isso significa que a CPU ficará ociosa a menos que tenha algo na *Scratchpad* para mantê-la ocupada!

### Complementando o núcleo

Assim como outros processadores baseados no MIPS R3000, o CW33300 suporta configurações com até quatro coprocessadores, mas a Sony personalizou-o com três:

#### Coprocessador de Controle de Sistema {.tabs.active}

Identificado como "CP0", o **coprocessador de controle de sistema** é um bloco comum encontrando em CPUs MIPS. Em sistemas baseados no R3000, como este, o CP0 controla como a cache é implementada. Assim, permitindo o acesso direto à cache de dados (na forma de "*Scratchpad*") e à cache de instruções (usando o "isolamento de cache"). O coprocessador de controle também é responsável por lidar com as interrupções, exceções e pontos de parada, sendo este último útil durante a depuração.

> Espera aí, os coprocessadores não deveriam apenas _expandir_ as funções da CPU? Por que o CP0 está fortemente acoplado à CPU?

De fato, os núcleos R3000 dependem do coprocessador de controle de sistema para conseguir usar muitos componentes, mas se isso deve ser "legal" ou não, depende da interpretação da palavra "coprocessador". Segundo a MIPS, um coprocessador não é estritamente uma parte opcional da CPU, ele também pode comandar o ambiente da CPU (cache, interrupções, etc.). Portanto, um coprocessador pode ser uma parte integrante do sistema. Isso é apenas algo a se ter em mente ao falar sobre sistemas relacionados ao MIPS.

Posteriormente, os [sistemas baseados no R4000](nintendo-64) incorporaram uma unidade de gerência de memória (MMU, do inglês *Memory Management Unit*) e um *Translation Lookaside Buffer* (TLB) neste bloco, aumentando assim suas capacidades e assumindo [novos papéis](nintendo-64#memory-management).

#### *Geometry Transformation Engine* {.tab}

A "CP2" ou ***Geometry Transformation Engine*** (GTE) é um processador matemático especial que acelera cálculos de vetores e matrizes.

Embora opere apenas com valores do tipo ponto fixo, ele ainda fornece operações úteis para gráficos 3D, como:

- Multiplicação e adição de matriz ou vetor; e quadrado de vetor.
- Transformação de perspectiva (usada para projeções 3D).
- Produto externo de dois ou três vetores (este último é usado para recorte).
- Muitas funções de interpolação que usam diferentes parâmetros.
- *Depth Cueing*(efeito profundidade, em tradução livre) e valor de cor de uma fonte de luz (usado para operações de iluminação e cor).
- Média de profundidade Z (suspeito que isso seja para a "tabela de ordenação", mas detalhes na seção "gráficos").

Você não precisa se lembrar de todas essas informações para seguir o restante do artigo! Apenas tenha em mente que o GTE cuidará das primeiras etapas do *pipeline* de gráficos, como projeção 3D, iluminação e recorte. Isso ajudará a gerar os dados necessários que serão enviados à GPU para renderização.

#### *Motion Decoder* {.tab}

O ***Motion Decoder*** (decodificador de movimento, em tradução livre), também é chamado "MDEC" ou "*Macroblock Decoder*", é outro processador que fica ao lado da CPU. Desta vez, ele descomprime "*Macroblocks*" em um formato que a GPU consegue entender. Um *Macroblock* é uma estrutura de dados que contém uma imagem com codificação semelhante à do JPEG.

O MDEC descomprime por vez *bitmaps* feitos de 8x8 píxeis com 24 bpp. O guia de programação de Walker afirma que o MDEC pode processar 9.000 *macroblocks* por segundo. Isso permitiria transmitir um ***Full-Motion Video*** (FMV — vídeo de movimento completo, em tradução livre) de 320x240 píxeis a 30 quadros por segundo.

O DMA é usado para enviar dados comprimidos através do CD-ROM → RAM → MDEC. O mesmo caminho é percorrido na direção oposta, embora, neste caso, o destino seja a VRAM.

Embora este componente esteja dentro do SoC e compartilhe o mesmo barramento de dados, ele não é um coprocessador MIPS, então a CPU/DMA o acessa através do mapa de memória (em vez de interceptar instruções).

Para obter mais informações sobre a unidade MDEC, sugiro verificar os textos de Sabin [@cpu-sabin] e de Czekański's [@cpu-jakub_mdec].

### Unidades ausentes? {.tabs-close}

Até agora, temos uma "CP0" e uma "CP2", mas **onde está a "CP1"?** Bem, essa está reservada para uma **Unidade de Ponto Flutuante** (FPU) e, infelizmente, a Sony não forneceu uma. Isso não significa que a CPU não possa realizar aritmética com números decimais, apenas não será rápida o suficiente (usando FPU emulada por software) ou muito precisa (usando aritmética de ponto fixo).

A lógica do jogo (envolvendo física, detecção de colisão, etc.) ainda pode funcionar com aritmética de ponto fixo. A codificação de ponto fixo armazena números decimais com um número imutável de casas decimais. Isso implica uma perda de precisão após certas operações, mas lembre-se, isso é um console de videogame, não um simulador de voo profissional. Portanto, o trade-off entre precisão e desemprenho é relativamente viável.

Aliás, às vezes confundo os tipos de números "ponto fixo", "ponto flutuante", "decimal" e "inteiro" (espero que não mais!). Se você se sente da mesma forma, recomendo dar uma olhada no resumo rápido de Gabriel Ivancescu [@cpu-gabriel] para atualizar rapidamente esses conceitos.

### Uma infinidade de atrasos

Como já vimos anteriormente, o processador CW33300 é um processador com *pipeline*, o que significa que ele coloca várias instruções em fila e as executa em paralelo em diferentes estágios. Isso melhora enormemente a taxa de processamento de instruções, mas se não for controlado adequadamente, pode levar a **riscos de *pipeline*** (*pipeline hazards*), resultando em erros computacionais.

A arquitetura MIPS I é suscetível a **riscos de controle e de dados** [@cpu-chen], o que significa que as instruções podem ser executadas quando não deveriam; e que as instruções podem operar com dados desatualizados antes de serem atualizados.

![Instruções de "Spyro The Dragon" visualizada no depurador do emulador NO$PSX. Observe como as instruções LW (<em>load word from memory</em>), JAL (<em>jump and link</em>) e BAL (<em>branch on not equal</em>) são seguidas por um <em>slot</em> de atraso para evitar os riscos de <em>pipeline</em>. As instruções marcadas em vermelho são de preenchimento sem utilidade. As instruções marcadas em azul executam operações úteis.](delay_slots.jpg) {.open-float}

Consequentemente, as CPUs MIPS I apresentam o seguinte comportamento:

- **Qualquer instrução que segue um código de operação do tipo "*branch*" ou "*jump*" é executada incondicionalmente**: assim, os desenvolvedores têm que preencher manualmente o *pipeline* com instruções modestas (como `calcular 0 mais 0`) após o *branch* ou *jump* para mitigar o risco de controle. Essas instruções de preenchimento são chamados de **branch delay slots**.
  - As CPUs modernas converteram esse fenômeno em vantagem: [predição de *branch*](gamecube#cpu). Adicionando circuitos extras para detectar o risco de controle, a CPU descarta os novos cálculos se a condição de *branch/jump* não for atendida. Mas se for, a CPU economiza algum tempo.
- **As instruções de carga não interrompem o *pipeline* até que os dados recuperados estejam disponíveis**: o segundo estágio do *pipeline* (chamado de "RD" ou "*Read and Decode*" - Leia e Decodifique, em tradução livre) coleta os operadores, que serão usados para realizar o cálculo no terceiro estágio (ALU). O quarto estágio ("MEM", de "acesso à MEMória") procura por dados na memória (ou seja, na RAM principal, no leitor de CD, etc.). Agora, aqui está o problema: quando uma instrução de carga recuperou os dados de fora, a instrução seguinte já havia buscado os operadores. Isso significa que uma instrução dependente dos valores da instrução de `carga` anterior requer um preenchedor entre elas, para que os operadores corretos possam ser buscados a tempo.

`r close_float_group(with_markdown = TRUE)`

Como podemos ver a partir do exemplo, alguns *slots* de atraso são preenchidos com instruções úteis, que realizam cálculos que não são afetados pelo risco de *pipeline*. Assim, os *slots* de atraso nem sempre implicam em desperdício de ciclos.

Geralmente, o compilador ou montador automaticamente reorganizará as instruções para preencher os *slots* com instruções úteis, ou, em último caso, adicionará preenchimentos inúteis. Em resumo, esse fenômeno é uma mistura de coisas boas e ruins.

## Gráficos

Recapitulando, uma grande parte do *pipeline* gráfico é realizado pelo GTE. Isso inclui a transformação de perspectiva (que projeta o espaço 3D em um plano 2D usando a perspectiva da câmera) e a iluminação. Os dados processados são então enviados para a **GPU proprietária da Sony** para renderização.

### Organizando o conteúdo

O sistema possui **1 MB de VRAM** que é usada para armazenar o *buffer* de imagem (do inglês, *frame buffer*), texturas e outros recursos que a GPU precisa para renderizar uma cena. A CPU pode preencher essa área usando o DMA.

O tipo de chip utilizado (**VRAM**) é de duas portas, como no [Virtual Boy](virtual-boy#organising-the-content). A VRAM utiliza dois barramentos de 16 bits, o que possibilita o acesso simultâneo entre a CPU/DMA/GPU e o codificador de vídeo.

![Layout de memória usando VRAM.](vram/vram.png){.tabs-nested .active title="VRAM"}

![Layout de memória usando SGRAM.](vram/sgram.png){.tabs-nested-last title="SGRAM"}

Embora em revisões posteriores do console, a Sony tenha mudado para chips **SGRAM** (a opção de porta única usando um barramento de dados de 32 bits individuais). *Que pena!*... Bem, para ser justo, cada um tem suas vantagens e desvantagens. Uma coisa é certa, devido às diferenças de temporização, jogos posteriores (como Jet Moto 3) exibirão gráficos com falhas quando executados nos sistemas baseados em VRAM. Se você quiser saber os detalhes, o documento "*Nocash PSX Specifications*" de Martin Korth descreve as diferentes temporizações e outras especificações [@cpu-korth].

### Desenhando a cena

Se você leu o artigo sobre o [Sega Saturn](sega-saturn), deixe-me dizer que o projeto desta GPU é *muito mais* simples!

Agora, para mostrar como uma cena é desenhada, vou usar principalmente como exemplo o jogo *Spyro: Year of the Dragon* da Isomniac. Por favor, tenha em mente que a resolução interna deste jogo é muito baixa (292x217 píxeis), o que impede uma análise clara de cada estágio, então eu aumentei um pouco a escala para fins de demonstração. `r img_link(label="Aqui é uma amostra", src="spyro/result_real.jpg")` na escala original se você estiver curioso.

#### Comandos {.tabs.active}

![<em>Pipeline</em> básico de comandos da GPU.](commands.png) {.tab-float}

Para começar, a CPU envia dados de geometria (vértices) para a GPU preenchendo seu *buffer* FIFO interno de 64 bytes com **comandos** (até três). Basicamente, um comando declara como e onde desenhar uma primitiva.

Uma vez que a geometria é recebida, é aplicado o **recorte** para evitar operações nos polígonos não vistos (localizados fora do campo de visão da câmera).

A localização da primitiva é declarada com um sistema de coordenada X, Y apontando para o *buffer* de imagem. A GPU do PS1 emprega um **modelo de coordenadas inteiras**, onde cada coordenada corresponde ao ponto central de um píxel (chamado de **ponto de amostragem**). Em outras palavras, não há coordenadas fracionárias.

#### Abordagem de visibilidade {.tab}

![Crash Bandicoot (1996). Este jogo usava geometria pré-ordenada para ganhar desempenho [@graphics-gavin]. Portanto, sua câmera só se move para trás ou para frente.](crash.jpg) {.tab-float}

Assim como a [concorrência](sega-saturn), o PS1 não inclui nenhuma funcionalidade de hardware que resolva o [problema de visibilidade](sega-saturn#an-introduction-to-the-visibility-problem). No entanto, a GPU lida com polígonos ordenados fornecendo uma **tabela de ordenação**: uma tabela dedicada onde cada entrada é indexada usando um valor de profundidade (também chamado de "valor Z") e contém o endereço onde fica o comando da GPU [@graphics-table].

A CPU precisa primeiro ordenar manualmente os polígonos e, em seguida, referenciá-los na entrada correta da tabela. Finalmente, a CPU ordena que o DMA envie a tabela para a GPU. Esse processo permite que a GPU renderize a geometria na ordem correta.

O DMA também oferece diversas funções para auxiliar tanto a CPU quanto a GPU na criação e transferência dessa tabela.

#### Rasterização {.tab}

![Visualização do <em>wireframe</em> da cena.](spyro/wireframes.png) {.tab-float}

Uma vez que os comandos são decodificados pela GPU, é hora de converter a geometria recebida (vértices) em píxeis. Isso permite que o sistema aplique o mapeamento de textura, efeitos e, por fim, exiba-os em um painel de duas dimensões (sua TV ou seu monitor). Para fazer isso, a GPU aloca uma matriz de píxeis que é usada como uma área de trabalho, chamada de ***buffer* de imagem**. Comparado com o complexo [Sega Saturn](sega-saturn), a GPU só precisa de um único *buffer* de imagem.

A GPU usa **triângulos como primitivas** para formar os modelos 3D. Ao ser a única primitiva disponível, as diferenças entre plano de fundo e elementos em primeiro plano não fazem diferença em termos de composição (ambos são constituídos por triângulos). Jogos em 2D herdam a mesma natureza: eles são apenas dois triângulos unidos para formar um quadrilátero (embora a GPU forneça rotinas para construir *sprites* automaticamente).

O rasterizador é a unidade responsável por converter vetores em triângulos e, em seguida, em píxeis. Isso é feito por meio de:

1. Capturando cada vértice de três pontos e calculando as arestas. Isso forma um triângulo.
2. Analisando a área do triângulo para identificar quais píxeis do *buffer* de imagem eles ocupam. Qualquer seção do triângulo que cobre o **ponto de amostragem** é convertida em um píxel.

Os píxeis gerados não são escritos imediatamente no *buffer* de imagem. Em vez disso, eles são enviados para os próximos estágios do *pipeline* para processamento adicional, que veremos nos parágrafos seguintes.

#### *Shaders* {.tab}

![Gouraud <em>shading</em> em ação.](spyro/shaders.png) {.tab-float}

Para aplicar efeitos de iluminação sobre esses polígonos, a GPU fornece dois algoritmos:

- ***Flat shading***: cada primitiva tem um nível de iluminação constante.
- **Gouraud *shading***: cada vértice das primitivas possui seu próprio nível de iluminação. Então, o brilho entre eles é interpolado automaticamente.
  - Como você pode imaginar, o resultado é mais realista. Por outro lado, este algoritmo não está disponível para *sprites*.

A razão para ter essa escolha se resume ao fato de que o *flat shading* preenche cerca de 2,5 vezes mais polígonos por segundo do que o Gouraud, portanto, é importante otimizar quais polígonos precisam de um sombreamento mais realista do que outros.

#### Texturas {.tab}

![Texturas aplicadas (_Tada!_).](spyro/result.png) {.tab-float}

Finalmente, superfícies sombreadas são mescladas com as texturas (*bitmaps* 2D) para produzir o resultado final.

A GPU realiza o **mapeamento de textura inversa**, onde a GPU percorre cada píxel rasterizado e procura o píxel correspondente no mapa de textura (chamado de **textel**). Os texels são calculados interpolando linearmente o mapa de textura (encontrado na VRAM) para ajustar ao formato do polígono. A rotina usada para a interpolação é chamada de **mapeamento de textura afim**, essa técnica opera apenas usando coordenadas 2D (valores de X e Y) enquanto descarta a terceira coordenada (Z ou profundidade) usada para perspectiva.

Devido os mapas de textura raramente terem a dimensão exata do polígono rasterizado, os **serrilhados** (resultados incorretos) podem aparecer. Isso se manifesta com distorções indesejadas, como texels faltantes ou ampliados. Para remediar isso, GPUs sofisticadas empregam **filtragem de textura** para suavizar (interpolando) mudanças repentinas de cor. Agora, a GPU do PS1 não implementa nenhum filtro, então ele recorre a um algoritmo chamado **vizinho mais próximo** para corrigir escalas sem suavizar os resultados. Isso é muito rápido (e barato), mas também explica porque os modelos texturizados podem parecer "quadriculados".

A unidade gráfica também inclui os seguintes efeitos disponíveis para uso:

- ***Semi-Transparency***: simula a luz passando por múltiplas texturas.
- ***Dithering***: suaviza mudanças abruptas de cor enquanto mantém a paleta de cores.

Vale ressaltar que o PS1 se destacou na realização desses efeitos!

`r close_tabs()`

Uma vez finalizado, a GPU escreve os píxeis no *buffer* de imagem na VRAM, que, por sua vez, são capturados pelo codificador de vídeo e transmitidos para a tela.

### Designs

Vamos dar uma pausa agora em toda essa teoria. Aqui estão alguns exemplos de personagens de jogos projetados do zero para a era 3D, eles são interativos, então eu encorajo você a conferi-los!

![Spyro the Dragon (1998).<br>413 trângulos.](spyro_ps1){.toleft model3d="true"}

![Crash Bandicoot (1996).<br>732 triângulos.](crash_ps1){.toright model3d="true"}

### Brincando com a VRAM

Com a quantidade disponível de VRAM (1 *megabyte inteiro*), pode-se alocar um *enorme* *buffer* de imagem de 1024x512 píxeis com cores de 16 bits ou um *realista* de 960x512 píxeis com cores de 24 bits — permitindo desenhar os melhores quadros que qualquer jogo já mostrou... Isso parece bastante impressionante, certo? Bem, isso levanta algumas questões, por exemplo:

- Quadros com essas dimensões precisam ser redimensionadas para seguir uma resolução padronizada (ou seja, 480 para NTSC, 576 para PAL) e então o codificador de vídeo pode transmiti-los para TVs comuns.
- Como a GPU vai conseguir desenhar algo decente se não houver espaço sobrando para o resto dos recursos visuais (por exemplo, texturas, tabelas de cores, etc.)?
- A GPU do PS1 só pode desenhar *buffers* de imagem com até 640x480 píxeis e cores de 16 bpp.

Tudo bem, então vamos ter um *buffer* de 640x480 com 16 bpp, o que deixa 424 KB de VRAM para recursos visuais. Até agora tudo bem? Novamente, tal resolução pode ser boa em monitores CRT, mas não particularmente notável naquelas TVs dos anos 90 que todo mundo tinha em casa. Então, há alguma maneira de otimizar o *buffer* de imagem? Apresentando o ***buffer* de imagem ajustável**.

![Visualização da VRAM no depurador NO$PSX. Você pode ver o <em>buffer</em> de dois quadros de imagem, juntamente com as texturas (que são traduzidas usando uma tabela de cores, também armazenadas ali).](vram.jpg) {.open-float}

Em essência, em vez de desperdiçar a valiosa VRAM usando resoluções "não apreciadas", a GPU permite diminuir as dimensões do *buffer* de imagem para aumentar efetivamente o espaço disponível para outros recursos. Em "Gears Episode 2" [@graphics-halkun], Halkun mostra uma configuração que divide o *buffer* de imagem de 640x480 em dois de 320x480 e, em seguida, confia em uma técnica chamada de **page-flipping** (alternância de página, em tradução livre) para renderizar várias cenas simultaneamente.

O *page-flipping* consiste em alternar a localização do quadro para exibição entre os dois disponíveis sempre que o jogo quiser, permitindo que o jogo renderize uma cena enquanto exibe outra. Assim, escondendo qualquer efeito de cintilação e melhorando os tempos de carregamento (algo que certamente o jogador apreciará!).

`r close_float_group(with_markdown = TRUE)`

No geral, o layout de Halkun consome apenas 600 KB de VRAM. O resto (424 KB) pode ser usado para armazenar tabelas de consulta de cores e texturas que, combinadas com **2 KB de cache de textura disponível**, resultam em uma configuração muito conveniente e eficiente.

Finalmente, vale mencionar que a VRAM pode ser mapeada usando **várias profundidades de cor simultaneamente**, o que significa que os programadores podem alocar um *buffer* de imagem de 16 bpp ao lado de *bitmaps* de 24 bpp (usados, por exemplo, em quadros de FMV). Essa é outra característica que facilita ainda mais a otimização do espaço.

### Segredos e limitações

Embora o PS1 tivesse uma arquitetura muito simples e adequada, surgiram problemas de qualquer maneira. Surpreendentemente, certos probelmas foram resolvidos com soluções inteligentes e engenhosas!

#### Modelos/texturas distorcidas {.tabs.active}

![Final Fantasy VIII da Square Soft (1999). As texturas tremem um pouco enquanto se movem.](ffviii){.tab-float video="true"}

As rotinas usadas para manipulação de geometria e aplicação de textura são conhecidas por apresentar algumas imprecisões.

Em primeiro lugar, **o rasterizador só manipula unidades de píxeis**: enquanto as coordenadas dos vértices são inteiras, as bordas do triângulo calculadas podem ocupar apenas uma fração do píxel. No entanto, o rasterizador só desenhará o píxel se a área do triângulo cobrir o ponto de amostragem do píxel e **não acompanhará a fração ocupada** [@graphics-raster]. Isso traz alguns problemas:

- As bordas externas dos modelos saltam abruptamente quando movidas ligeiramente.
- Os triângulos dentro de uma malha (compartilhando os mesmos vértices e arestas) estarão "lutando" para desenhar sobre os mesmos píxeis. Com a tabela de ordenação, a GPU desenhará seguindo uma base de "o último a chegar é o primeiro a ser servido", o que pode levar a interseções de triângulos piscando ou sobrepondo-se quando movidos ligeiramente.

Isso é comumente resolvido implementando a **resolução subpíxel**, onde o rasterizador acompanha as frações de píxeis ocupados por cada área do triângulo [@graphics-tomas]. Consequentemente, métodos de antisserrilhamento podem ser adicionados para suavizar bordas irregulares ou mudanças abruptas na cor.

Além disso, a tabela de ordenação coloca a **responsabilidade no desenvolvedor/programador para mostrar a geometria na ordem correta**. Em alguns casos, os cálculos implementados dependem de muitas aproximações para ganhar desempenho. Isso pode resultar em superfícies piscando ou **ocultas** que deveriam ter sido exibidas.

Além disso, definir um *buffer* de imagem de baixa resolução pode amplificar todos esses problemas de serrilhamento.

Finalmente, como você sabe, as transformações afins não têm **noção de profundidade**, o que pode confundir a percepção do usuário quando a câmera está próxima do modelo e posicionada perpendicularmente ao espectador [@graphics-retrocomp]. O efeito é referido como **distorção de textura**. Por isso, alguns jogos recorreram à **tesselação** (dividindo um polígono grande em polígonos menores) para reduzir a distorção, enquanto outros simplesmente trocaram as texturas por **cores sólidas**. Em geral, uma GPU resolve esse problema implementando a **correção de perspectiva**, que interpola as texturas usando o valor de profundidade.

#### Alegações contraditórias {.tab}

Se você verificar outros canais técnicos ou fóruns, encontrará explicações alternativas sobre os efeitos de oscilação e distorção do PS1. Embora alguns deles coincidam com o que expliquei anteriormente, outros argumentarão de forma diferente. Portanto, gostaria de dar minha opinião sobre o por que as seguintes declarações **não** são precisas:

> Modelos e texturas oscilam devido à falta de FPU

Um FPU não dita se um computador pode operar ou não com números fracionários. O PS1, como qualquer outro computador sem uma FPU, ainda pode realizar aritmética de ponto fixo. Além disso, usando emulação de software, números de ponto flutuante também podem ser calculados (apenas mais lentamente). Em resumo, as **FPUs são apenas aceleradores**, não os confunda com uma ALU (a parte crítica de uma CPU que realiza aritmética) ou números decimais.

> Modelos e texturas tremem devido ao sistema de coordenadas inteiras da GPU

Usar coordenadas inteiras é uma abordagem comum para tornar os cálculos menos dispendiosos na GPU. É o fato de não usar a resolução de subpíxeis que torna isso em um problema visível. Resumindo, o **antisserrilhamento poderia ser aplicado para suavizar mudanças abruptas de cores** se o rasterizador contasse a fração do píxel ocupada pelo triângulo.

> Texturas sofrem deformação devido à falta de *mipmapping* (mapa de níveis de detalhe, em tradução livre)

As GPUs que implementam o mapeamento de textura inverso, como esta, estão sujeitas a um erro de medição chamado de "subamostragem" (um píxel é mapeado para vários texels). Isso cria serrilhamentos (resultados incorretos). Esse comportamento é percebido ao renderizar geometrias distantes da câmera. Para remediar isso, os mapeadores modernos de textura aplicam um "filtro trilinear", que suaviza os texels usando a mesma textura armazenada em diferentes escalas (*mipmaps*) e faz alguma interpolação no meio. Em resumo, o ***mipmapping* é uma abordagem para resolver os serrilhamentos**. É o **mapeamento de textura afim** (com sua falta de correção de perspectiva) que interpola texturas em superfícies de três pontos sem considerar a profundidade. Daí o efeito de "deformação".

> Modelos ou texturas tremulam devido à falta de *buffer* de profundidade

Uma GPU com [*buffer* de profundidade](nintendo-64#modern-visible-surface-determination) resolve o [problema da determinação de superfícies visíveis](sega-saturn#an-introduction-to-the-visibility-problem) ao nível de hardware. O PS1 depende de uma tabela de ordenação, o que significa que os desenvolvedores são responsáveis por determinar qual geometria está à frente das outras. Em resumo, **qualquer modelo exibindo triângulos tremulantes é causado pelas rotinas de ordenação (software)**, portanto, é remediado no mesmo software.

#### Gráficos pré-renderizados {.tab}

![Final Fantasy VIII da Square Soft (1999). Uma cena com plano de fundo pré-renderizado. A maneira como os modelos interagem e se movem é essencial para _enganar_ a percepção do usuário.](prerendered.png) {.tab-float}

Vamos mencionar uma funcionalidade "positiva" para variar...

Se um jogo procurava uma paisagem mais realista do que o que a GPU podia oferecer, uma opção disponível era empilhar dois triângulos e usar o *Motion Decoder* para reproduzir **animação pré-renderizada** neles. Vídeos FMV podiam ter um tamanho muito grande, e felizmente o CD-ROM estava preparado para isso.

Alguns jogos contavam especificamente com essa técnica para compor fundos e, honestamente, era bastante convincente vê-los em uma TV CRT, obviamente os emuladores modernos com capacidade de aumento de resolução revelam isso muito facilmente.

### Saída de vídeo {.tabs-close}

A primeira revisão do console apresenta uma quantidade surpreendente de sinais de vídeo com as seguintes portas:

- **RFU DC**: esta porta foi logo removida, pois era destinava a ser conectada a um modulador RF.
- **RCA**: fornece vídeo composto.
- **S-Video**: fornece sinais de luminância + sincronização (combinado) e de cores.
- **AV Multi Out**: fornece todos os sinais anteriores, exceto RFU, além de sinal RGB e uma linha de +5 volts.

Revisões posteriores do console removeram essas portas e, no final, apenas a porta "AV Multi Out" permaneceu.

## Áudio

A **unidade de processamento de som** (SPU, do inglês *Sound Processing Unit*) cuida disso. Este chip suporta a enorme quantidade de **24 canais** de **amostras de 16 bits ADPCM** (uma versão mais eficiente da, já conhecida, amostragem PCM) com uma taxa de amostragem de **44,1 kHz** (qualidade de áudio de CD).

Este chip também oferece as seguintes capacidades:

- **Modulação de tom**: como o nome sugere, os jogos podem alterar automaticamente o tom de suas amostras em vez de precisar de armazenar amostras extras. Isso é útil para sequenciamento de música.
- **Modulação de frequência**: as vozes podem ser atribuídas para alterar a frequência de outras. Comparável ao [sintetizador FM](mega-drive-genesis#audio).
- **Envelope ADSR**: é um conjunto de propriedades disponíveis para modulação de amplitude.
- ***Looping***: instrui o sistema a reproduzir uma parte do áudio repetidamente.
- **Reverberação digital**: simula a amostra sendo reproduzida em uma atmosfera específica para a imersão do jogador.

**512 KB de DRAM** (chamada de "RAM de som") são fornecidos como *buffer* de áudio. Esta memória é acessível a partir da CPU (somente através do DMA) e do controlador de CD. Embora os jogos tenham apenas 508 KB disponíveis para armazenar amostras, o restante é reservado pela SPU para processar música de CD de áudio. Essa quantidade é reduzida ainda mais se a reverberação estiver ativada.

O controlador de CD também pode enviar amostras diretamente para o *mixer* de áudio sem passar pelo *buffer* de áudio ou exigir intervenção da CPU. As amostras também podem ser comprimidas usando a codificação "XA", que a SPU consegue decodificar em tempo real.

### A era do *streaming*

[Assim como no Saturn](sega-saturn#the-opportunity), os jogos não dependem mais do sequenciamento de música ou de formas de onda pré-definidas, e graças à quantidade de espaço disponível na mídia CD-ROM, os desenvolvedores podem armazenar amostras totalmente produzidas e simplesmente transmiti-las para o chip de áudio.

## E/S

Existem duas portas de E/S (**Serial** e **Paralela**) disponíveis para expensão. Contudo, elas foram removidas em revisões posteriores do console devido à falta de adoção e ao fato de que elas poderiam ser potencialmente usadas para quebrar o sistema de proteção contra pirataria.

### Subsistema de CD

O bloco que controla a unidade de CD é uma área interessante, você pode imaginá-lo como um computador separado vivendo dentro do PlayStation.

![Layout do subsistema de CD.](cd.png) {.open-float}

Este subsistema é composto de:

- Um **DSP**: controla o motor e o laser, e processa o sinal RF que vem do laser.
- Uma **Sub-CPU**: um pacote de CPU composto de um microcontrolador **Motorola 68HC05**, **512 B de RAM** e **16 KB de ROM** [@io-cdrom]. Em resumo, a Sub-CPU executa um programa local armazenado na ROM e controla o DSP. O programa da Sub-CPU implementa medidas de proteção contra pirataria e elas são aplicadas independentemente da CPU principal "gostar ou não".
- Um **controlador de CD**: este é o intermediário entre o subsistema de CD e o resto do console, recebendo comandos da CPU principal (organizados em fila) e disparando interrupções após certos eventos. Como controlador, o chip conversa com a Sub-CPU e recebe, do DSP, os dados do CD. Além disso, o controlador contém uma unidade DMA e está conectada ao SPU, o que permite que ele transmita áudio diretamente.
- **32 KB de SRAM** conectados ao controlador:: isso é presumivelmente usado como um *buffer* para dados lidos do disco.

`r close_float_group(with_markdown = TRUE)`

O subsistema se assemelha um pouco a um leitor de CD típico que todos tinham em casa, exceto pelos ajustes que a Sony implementou no programa da Sub-CPU para realizar verificações contra pirataria.

### Portas frontais

O controle e os *slots* do *memory card* são eletricamente idênticos [@io-manual], portanto, o endereço de cada um deles é codificado diretamente no hardware. Além disso, a Sony alterou a forma física das portas para evitar acidentes.

A comunicação com esses dispositivos é feita usando uma interface serial. Os comandos enviados pelo console serão entregues a um dos dois *slots* (ou "mem. card 0" e "controle 0", ou "mem. card 1" e "controle 1"). Em seguida, ambos os acessórios responderão com seus identificadores exclusivos, permitindo que o console se concentre em um tipo específico de dispositivo (*memory card* ou controle) daquele momento em diante.

## Sistema Operacional

O sistema inclui uma **ROM de 512 KB** que armazena uma "BIOS". Este programa realiza muitos serviços, incluindo cuidar do processo de inicialização, exibir uma interface de usuário (*shell*) e, finalmente, expor uma coleção de rotinas de E/S [@operating_system-bios].

### BIOS/Kernel

A BIOS é uma dependência crítica para os jogos, pois esse programa os inicia a partir da unidade de CD. Além disso, a BIOS funciona como um "intermediário" para interagir com o hardware do console. A metodologia anterior é semelhante ao que a IBM implementou com sua BIOS do IBM PC, que incentivou os desenvolvedores a usar uma tabela padrão de interrupção (contendo rotinas de E/S) em vez de [portas de E/S](master-system#accessing-the-rest-of-the-components) dependentes da plataforma.

Dito isso, a BIOS do PS1 disponibiliza rotinas como:

- Comandos para a unidade de CD-ROM.
- Operações de sistema de arquivos (do CD-ROM e do *memory card*).
- Multitarefa.
- Funções padrão da linguagem C (manipulação de *strings*, operações de memória, etc.).

Como o acesso à ROM da BIOS é muito lento (está conectado a um barramento de dados de 8 bits), as APIs são empacotadas na forma de um **Kernel** e copiadas para a RAM principal durante a inicialização. Assim, 64 KB da RAM principal são reservados para esse Kernel. Aliás, o Kernel também é referido como **PlayStation OS**.

### Processo de inicialização

O vetor de inicialização da CPU está localizado no endereço `0xBFC00000`, o qual aponta para a ROM da BIOS.

![Famosa cena de abertura após ligar o console.](bios/splash.png){.tabs-nested .active title="Animação"}

![Logo do PlayStation mostrado após um jogo original ser inserido.](bios/valid.png){.tab-nested title="Logotipo"}

![A <em>shell</em> é exibida se não houver nenhum disco inserido.](bios/shell.png){.tabs-nested-last title="*shell*"}

Da mesma forma que o processo de inicialização do [Saturn](sega-saturn#games), depois de receber energia, o PS1 irá:

1. Buscar a ROM da BIOS e executar rotinas para inicializar o hardware.
2. Carregar o PlayStation OS.
2. Exibir a tela de carregamento.
3. Se houver um CD inserido, o controlador de CD-ROM verificará se é original:
    - **É original** → o controlador permitirá a leitura de seu conteúdo.
        1. A CPU procurará pelo arquivo "SYSTEM.CNF" e continuará a execução a partir daí.
    - **Não é original** → A CPU exibirá uma mensagem de erro.
4. Sem CD inserido, a CPU exibirá uma ***shell***. O usuário agora tem o controle.

A *shell* é uma interface gráfica simples que permite ao usuário copiar ou excluir salvamentos do *memory card*; ou reproduzir um CD de áudio.

## Jogos

Assim como o [Sega Saturn](sega-saturn) e qualquer outro console que fez a transição para a mídia de CD, os jogos agora têm um novo conjunto de recursos: grande armazenamento (640 MB), qualidade de áudio boa e uma velocidade de leitura "não tão lenta" graças as unidades de velocidade dupla.

Se você quiser saber como o Disco Compacto (CD) funciona, eu escrevi uma explicação rápida no artigo do [Sega Saturn](sega-saturn#the-compact-disc-cd).

### Ecossistema de desenvolvimento

O SDK oficial forneceu bibliotecas em C que estão vinculadas às rotinas da BIOS para acessar o hardware. Caso esteja se perguntando, esse é o principal fator que ajudou a emular o PS1 em uma ampla gama de plataformas.

Junto com o SDK, a Sony também distribuiu hardware especializado, como o **DTL-H2000**: um cartão ISA de dois *slots* contendo as partes internas e de E/S do PS1 [@games-shadow], além de circuitos extras para fins de depuração. A placa tem acesso ao disco rígido do computador hospedeiro e pode executar software do PS1 sem restrições. O respectivo software exigia um PC com Windows 3.1 ou 95 instalado.

## Anti-pirataria / Trava de região

Como você deve saber, para recuperar dados de um CD, um feixe de laser é usado para detectar as **cavidades** e as **áreas refletoras** da trilha do disco.

Agora, discos convencionais não são 100% planos e frequentemente têm pequenas flutuações em suas trilhas. Esses *defeitos* são completamente imperceptíveis durante a leitura dos dados, uma vez que os lasers podem se calibrar automaticamente conforme eles leem.

Foi com base nisso que a Sony desenvolveu sua proteção contra cópias: a Sub-CPU permite a leitura de discos cujas tabelas de conteúdo (TOC, do inglês *Table of Contents*) foram gravadas usando uma frequência definida informalmente conhecida como ***Wobble Groove*** (ranhura de oscilação, em tradução livre), que é aplicada apenas durante a fabricação e não pode ser replicada por gravadores convencionais. A TOC é encontrada na seção interna do CD (chamada de "*Lead-In*" — trilha de início, em tradução livre) e instrui o laser sobre como navegar pelo disco [@copy_protection-kalymos]. Além disso, ela é repetida muitas vezes como um mecanismo de tolerância a falhas.

Dentro da TOC dos jogos do PS1, é incorporada uma das seguintes sequências de caracteres:

- **SCEA** → Sony Computer Entertainment of America.
- **SCEE** → Sony Computer Entertainment of Europe.
- **SCEI** → Sony Computer Entertainment of Japan.

Como você pode imaginar, o leitor aplica a **trava de região** usando esta técnica também.

### Derrota

Por outro lado, essa verificação é executada apenas uma vez no início, então trocar manualmente o disco logo após passar pela verificação pode derrotar essa proteção... com o risco de danificar a unidade de CD. Mais tarde, alguns jogos tomaram medidas próprias e frequentemente reiniciaram a unidade de CD durante o jogo para que a verificação fosse executada novamente, isso foi feito para impedir que os usuários realizassem o "truque da troca".

Alternativamente, pequenas placas programadas para imitar o sinal de *wobble* podiam ser soldadas no console. Essas placas são conhecidas como **Modchips** e, embora questionáveis legalmente, eram incrivelmente populares.

### Retaliação

O uso de emuladores também foi visto como uma ameaça pelas publicadoras de jogos. Como resultado, alguns jogos incluíram seus próprios controles (principalmente *checksums* — verificação de integridade, em tradução livre) para combater qualquer tipo de uso ou modificação não autorizada.

Um dos controles que me foi informado consistia em reinicializar deliberadamente a unidade de CD e, em seguida, fazer com que ele lesse setores específicos que não passariam na verificação do *wobble groove*. Se conseguissem desbloquear a unidade de CD de alguma forma, o jogo (ainda residente na RAM) revelaria *alegremente* seu material anti-pirataria. Note que essa abordagem também pode afetar consoles modificados que usam jogos originais.

Mais tarde, a Sony forneceu uma biblioteca chamada **Lybcrypt** que fortaleceu a proteção contra pirataria com o uso de duas abordagens [@copy_protection-libcrypt]:

- Do lado do hardware, os *checksums* de setores são armazenados em subcanais do disco.
  - Os subcanais do CD-ROM tradicionalmente armazenam metadados, principalmente para guiar a unidade de CD. Estes não são acessíveis pelo usuário e os leitores convencionais raramente permitem escrever manualmente sobre eles.
- Do lado do software, um conjunto de rotinas, que obtém os valores de *checksum*, mistura esse valores obtidos e os incorporados em diferentes pontos do jogo. Essa tentativa tentou reduzir tanto a ação de emuladores quanto de *modchips*.

## Isso é tudo pessoal
