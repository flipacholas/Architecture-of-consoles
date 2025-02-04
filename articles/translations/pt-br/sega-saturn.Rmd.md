---
short_title: Arquitetura do Sega Saturn
long_title: Arquitetura do Sega Saturn
name: Sega Saturn
subtitle: O que é possível fazer com 8 processadores?
date: 2019-08-03
release_date: 1994-11-22
generation: 5
cover: saturn
javascript:
  - 'threejs'
published: true
seo_image_pos: "Top"
top_tabs:
  Models:
    - 
      title: "Japonês"
      caption: "O Sega Saturn, lançado em 22/11/1994 no Japão"
      file: japanese
    - 
      title: "Internacional"
      caption: "O Sega Saturn, lançado em 11/05/1995 nos EUA e em 08/07/1995 na Europa"
      active: true
      file: international
  Motherboard:
    caption: "Exibição da revisão \"VA8\", a qual inclui todos os componentes numa única placa. <br>Os chips de RAM restantes ficam encaixados na parte traseira"
#Historical
aliases:
  - /projects/consoles/sega-saturn/
---

## Uma breve introdução

Bem vindo à era 3D! Bom... *mais ou menos*. A Sega teve um sucesso considerável com o Mega Drive, então não havia razão para forçar os desenvolvedores a criar jogos em 3D *ainda*.

No caso de algum desenvolvedor querer usar a dimensão adicional, a Sega adaptou certas partes do hardware para permitir também o desenho de polígonos. Claro que isso não iria sair de controle, não é?

## {.supporting-imagery}

## CPU

Assim como seus competidores mais próximos, que estiveram [cheios de opções](playstation#tab-1-1-a-bit-of-history) ao longo da febre do RISC, a Sega precisou passar por todos os dilemas de escolher um novo fornecedor que pudesse erguer a próxima geração de jogos (inclusive aqueles com capacidades "3D"). No final, a empresa optou por uma CPU recente, cujo criador estava desesperado atrás de algum adotante: a **Hitachi SuperH**, ou "SH".

Ainda que, de início, a nova criação da Hitachi tenha focado em aplicativos embarcados, ela estreou artifícios modernos, como [@cpu-prog_manual]:

- Uma [arquitetura de load/store](xbox#tab-1-4-cisc-or-risc), o que significa que as instruções não misturam memória com operações do registro, o que resulta num design de CPU mais limpo e escalável. Este é um dos pilares das CPUs RISC.
- **Barramento de dados 32-bit e ULA**, que permite à CPU mover e operar uma quantidade maior de dados (valores de 32 bits) de uma só vez, sem que isso consuma ciclos extras.
- **16 registradores de 32 bits para uso geral**, o dobro do que havia em CPUs anteriores como a [Motorola 68000](mega-drive-genesis#cpu). Essa foi mais uma decisão de design derivada das diretrizes RISC [@cpu-patterson].
- **Barramento de endereços de 32 bits**, permitindo o endereçamento de até 4 GB de memória (_adeus [mappers](nes#going-beyond-existing-capabilities)_).
- Um **caminho de dados segmentado** com **cinco estágios**: A execução das instruções é dividida em cinco passos ou *estágios*. A CPU põe em fila até cinco instruções, e cada uma é alocada em um estágio. Isso permite que todos os recursos da CPU sejam aproveitados sem necessidade de tempo ocioso, enquanto também aumenta o número de instruções executadas por unidade de tempo.
- Uma **unidade de multiplicação de 16 bits**: Faz multiplicações com inteiros de 16 bits.

Além disso, o SuperH apresenta um novo conjunto de instruções, chamado **SuperH ISA**, o qual, mesmo com a adoção do design RISC, **tem todas as suas instruções no tamanho de 16 bits**. Isso é uma surpresa, pois essa CPU opera com palavras de 32 bits, portanto seria esperado que as instruções tivessem o mesmo comprimento. Ainda assim, a Hitachi conseguiu encaixar seu conjunto de instruções usando metade do tamanho. Esse formato não apenas reduz o tamanho dos programas: como a CPU busca por instruções em lotes de 32 bits, **ela pode captar duas instruções num único ciclo**. Num geral, essa técnica de comprimir o conjunto de instruções ajudou a lidar com uma preocupação comum das arquiteturas baseadas em RISC, chamada "densidade de código". Essas arquiteturas acabavam requerindo mais instruções (e, portanto, mais memória) para executar as mesmas tarefas do que sistemas não RISC.

### As ramificações

Por outro lado, as desvantagens dos designs RISC ainda estão presentes no SuperH, como os [hazards de controle](playstation#delay-galore). Por consequência, os programas precisam incluir **espaços para atraso de desvio condicional**, para evitar erros de cálculo. Para remediar a situação, o SuperH possui **instruções de atraso de desvio condicional**, que são instruções de desvio pré-equipadas com um espaço de atraso [@cpu-prog_manual].

**Hazards de dados** também estão presentes, mas não são resolvidos pelo programador ou pelo compilador: a CPU automaticamente irá reter a pipeline quando for necessário.

### A Sega não está satisfeita

No entanto, nada disso impediu a Sega de manifestar sua insatisfação com o produto final. Isso ocorreu principalmente por causa do pequeno multiplicador de 16 bits, percebido como um funil para o processamento de grandes quantidades de dados (uma nova necessidade dos jogos 3D). Por isso, a Hitachi sintetizou uma segunda revisão, com uma unidade multiplicadora estendida e outros requisitos da lista da Sega [@cpu-history], o que levou a uma nova CPU chamada **SH-2**.

![Os dois chips SH-2 encontrados no Sega Saturn](sh2s.jpg)

Ainda assim, a Sega não pôde ficar parada após ouvir a escolha de CPU que [seus](playstation#cpu) [competidores](nintendo-64#cpu) haviam feito. Ela pediu à Hitachi que aumentasse a taxa de clock do SH-2 — uma tarefa impossível uma vez que o chip estivesse pronto para fabricação. Por sorte, a Hitachi tinha outro truque na manga: **multiprocessamento**. Durante a fase de pesquisa do SH, o time havia adicionado circuitos mínimos capazes de permitir que o SH funcionasse com outros SHs dentro de um mesmo sistema ao mesmo tempo. Ao ouvir isso, a Sega decidiu que o Sega Saturn teria uma configuração de dois chips. E o resto é história.

### O produto final

Com as origens explicadas, vamos dar uma olhada no produto entregue.

O console tem não uma, mas **duas CPUs Hitachi SH-2** rodando a **~28.63 MHz cada** [@cpu-overview]. Ainda que sejam idênticas fisicamente, elas funcionam num estado **master-slave**, onde a primeira deve enviar comandos para a segunda. É possível que atinjam certo grau de paralelismo, ainda que ambas dividam o mesmo barramento externo \[@cpu-dualcpu\] (o que pode levar a congestionamento).

A Hitachi preparou diferentes variantes do SH-2 e as vendeu como parte de uma série chamada "SH7600". Todas elas possuem [@cpu-brief]:

- O já mencionado **caminho de dados segmentado em cinco estágios** e a **SuperH ISA**. Este último foi estendido com seis instruções adicionais para desvio de dados especializado e aritmética.
- Uma **unidade de multiplicação de 32 bits** melhorada, a qual agora faz multiplicações com integrais de 32 bits.
- Um **barramento externo de dados de 32 bits**, o qual é dividido entre as duas CPUs.

O chip específico selecionado para este console, o "SH7604", contém as seguintes adições [@cpu-prog_manual]:

- **4 KB de cache**: Guarda um pequeno número de instruções e dados previamente buscados da memória, para acelerar futuras leituras.
- Uma **unidade de divisão de 32 bits**: Executa divisões com integrais de 32 bits.
- **Controlador interno de DMA**: Transfere dados da memória sem intervenção da CPU.
- Suporte a **little endian**, permitindo que a CPU entenda valores codificados na ordem oposta. Isso é útil quando a memória externa é dividida com outros processadores.

É importante ressaltar que **ter duas CPUs não quer dizer que os jogos irão rodar duas vezes mais rápido!** Na prática, porém, isso requer uma programação muito complexa, que administre com eficiência CPUs que dividam o mesmo barramento. Portanto, o uso organizado do cache tem um papel crucial nesse console.

### Uma escolha dividida de memória

O Sega Saturn contém um total de **2 MB de RAM** para uso geral, chamada **Work RAM** (WRAM). Esses dois mega são divididos entre dois blocos bem diferentes:

- O primeiro bloco providencia **1 MB de SDRAM**, e devido à sua rápida taxa de acesso, esse bloco também é chamado de "WRAM-H".
- O outro bloco contém o outro megabyte, mas se chama "WRAM-L", pois ele usa **DRAM**, o que resulta em taxas menores.

### O terceiro (sim, mais um) processador

Parece que, surpreendentemente, duas CPUs SH-2 ainda não eram o bastante para a Sega. Então, para acelerar o processamento de vetores (ao custo de maior complexidade), o console abriga um coprocessador adicional, o **Saturn Control Unit**, ou "SCU".

É um chip composto de dois módulos [@cpu-scu]:

- **Um controlador DMA**: Arbitra o acesso à WRAM pelos três barramentos principais sem a intervenção das CPUs.
- **Um DSP**: Usado como uma "unidade de geometria" de ponto fixo. Comparado ao SH-2, faz cálculos de matriz/vetores tais como transformações 3D e iluminação mais rapidamente. Entretanto, ele roda em meia velocidade, e seu conjunto de instruções é mais complexo. Além disso, ele precisa da WRAM do SH-2 para buscar e guardar dados (usando DMA).

Pelo lado positivo, a SCU vem com **32 KB de SRAM** para uso local. Do lado negativo, a SCU não é capaz de acessar a WRAM-L.

## Gráficos

Como o Saturn é o primeiro "console 3D" analisado para [esta série](consoles), vamos primeiro ver as mudanças fundamentais de design que abriram caminho rumo à nova geração de gráficos 3D:

- A GPU agora conta com um **frame buffer**: Os gráficos não precisam mais ser renderizados na hora. Em vez disso, a GPU reserva uma porção da VRAM para desenhar um bitmap com toda a geometria computada que a GPU solicita, e então um codificador de vídeo capta essa região e a reproduz através do sinal de vídeo.
  - Consequentemente, essa "área de trabalho" reservada permite à GPU continuar manipulando o bitmap mesmo após concluir a renderização da cena, permitindo à GPU delegar certas tarefas exaustivas como iluminação e suavização de bordas para a GPU. É aqui que o termo **pipeline gráfico** começa a ganhar impulso.
- **Maior demanda de VRAM**: O uso de um frame buffer implica um aumento de demanda por memória (o que já não é mais um grande problema); a quantidade de RAM requerida para um frame buffer é proporcional à dimensão da tela e ao número de cores utilizadas. Como exemplo, com 600 KB de VRAM, podemos armazenar um frame buffer de 640x480 pixels com 32 mil cores por pixel (16 bpp).
  - E os programadores ainda ficam livres para organizar o uso da VRAM: Nem todos os bits precisam ser alocados para o frame buffer, então por que também não usá-los para o cache de texturas, para renderizar outros frame buffers ao mesmo tempo, e também adicionar umas tabelas de busca de cores para acelerar as coisas?
- A CPU incorpora **operações com vetores**: Uma GPU com capacidades 3D estaria incompleta sem uma CPU capaz de alimentá-la com a geometria necessária. Por esse motivo, CPUs da próxima geração incluem um tipo de instrução especializada que acelera o cálculo de vetores. São conhecidas como extensões **Single instruction, multiple data**, ou "SIMD".
  - No caso do Saturn, a operação de vetores é acelerada pela Saturn Control Unit (e não pelas CPUs SH-2).

### A Oferta da Sega

O console inclui **duas GPUs proprietárias**, cada uma servindo a diferentes propósitos ao trabalhar simultaneamente. Alguém pode argumentar que as novas GPUs são uma evolução do [clássico VDP](mega-drive-genesis#graphics), enquanto outros podem dizer que é uma total reformulação... Eu acho que é um pouco de cada.

Com isso dito, vamos dar uma olhada nos dois chips.

#### VDP1 {.tabs.active}

![Arquitetura do VDP1.](vdp/VDP1.png) {.tab-float}

O **Video Display Processor 1** (VDP1) é um chip que desenha sprites com transformações geométricas [@graphics-vdp1]. Os resultados são gravados num frame buffer, o qual em resposta é transmitido para o VDP2 para exibição.

O chip é programado através do envio de "comandos de desenho". Assim sendo, os programadores têm **512 KB de RAM dedicada** para guardar esses comandos de desenho e os materiais requeridos (texturas/blocos, tabelas de cores, etc).

Por consequência, o VDP1 foi construído para usar **quadriláteros como primitivas**, o que significa que ele pode apenas compor modelos usando polígonos (sprites) de 4 vértices. O chip aplica **Forward Texture Mapping** parta conectar pontos da textura no quadrilátero. Ele não vem com nenhuma técnica de filtro/interpolação, então os cálculos ficam sujeitos a **aliasing**.

O VDP1 também proporciona esta seleção de efeitos:

- Dois **algoritmos de sombreamento** (Plano e Gouraud) para iluminação.
- **Anti-aliasing**: Nesse caso, ele duplica pixels para cobrir espaços vazios durante o mapeamento.
- **Clipping** para descartar polígonos fora do campo de visão da câmera.
- **Transparência** para mesclar dois bitmaps não opacos.

**Dois chips de frame buffer de 256 KB** estão disponíveis para desenhar, ao mesmo tempo, novas cenas do jogo, sem quebrar a que estiver sendo exibida. Quando o buffer secundário termina de ser desenhado, o VDP1 começa a transmitir o segundo no lugar (**page-flipping**), e o ciclo continua.

#### VDP2 {.tab}

![Arquitetura do VDP2.](vdp/VDP2.png) {.tab-float}

O **Video Display Processor 2** (VDP2) se especializa em renderizar planos grandes (4096×4096 pixels) com transformações (rotação, escala e translação) aplicadas aos mesmos [@graphics-vdp2].

Mais importante, o VDP2 renderiza **na hora** (sem frame buffer) tal como as [engines baseadas em tiles](mega-drive-genesis#constructing-the-frame) anteriores. Ele pode exibir até **16.7 milhões de cores** (24 bits). Este chip é também responsável pela exibição do buffer do VDP1, que também pode ser transformado e/ou misturado com as camadas do VDP2. O "frame" do VDP é composto de até quatro planos 2D e um plano 3D; ou dois planos 3D.

Esse chip utiliza [tile-maps](mega-drive-genesis#constructing-the-frame) para compor planos, e faz a **correção de perspectiva** para o mapeamento de texturas 3D. Esse é um método mais sofisticado, que leva em conta o valor de profundidade para computar rotações.

Os efeitos disponíveis incluem **multi-texturação** (mapeamento de mais de uma textura por polígono) e **sombreamento**. Com este último, após receber sprites gerados pelo VDP1, o VDP2 consegue reduzir o brilho deles e mesclá-los com meia-transparência. Porém o VDP2 recebe apenas um fluxo de sprites do VDP1 (no mesmo passo do raio da CRT), então essa função tende a ser complicada de codificar e operar.

Esse chip também abriga **4 KB de RAM de cores (CRAM)**, que é usada para traduzir os valores customizados de cor do VDP1 (cores de índice) para cores RGB de 24 bits.

Por fim, ainda que o VDP2 seja limitado a dois planos 3D, nada impede a CPU de usar a VRAM dele como uma área de frame buffer para desenhar gráficos 2D ou 3D adicionais em software.

Eu recomendo dar uma olhada nas fontes (ao fim do artigo) caso essa seção tenha chamado sua atenção, já que os VDPs têm muitas outras peculiaridades que estão além do escopo desse artigo.

### Definindo o problema {.tabs-close}

Como você pode ver, a arquitetura do subsistema gráfico é bem complexa, e portanto é interpretada de forma diferente a depender da necessidade:

### Como um *poderoso* console 2D

As capacidades do Saturn em desenhar cenas em 2D eram imensas comparadas com as do [Mega Drive](mega-drive-genesis) e do [SNES](super-nintendo), ainda que não fossem o principal atrativo comercial do console.

#### *Sprites* {.tabs.active}

![Mega Man X4 (1997).<br>Plano de sprites do VDP1.](2d/sprites.png) {.tab-float}

Nesse caso, o VDP1 tem a tarefa de traçar sprites tradicionais sem aplicar qualquer distorção 3D.

A CPU configura o VDP1 gravando por cima de seus registradores e preenchendo sua VRAM com comandos e tiles. O processo também pode ser acelerado graças ao controlador DMA.

#### *Backgrounds* (Planos de fundo) {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Plano 2D 1.](2d/bg1.png){.active title="Plano 2D 1"}

![Plano 2D 2.](2d/bg2.png){title="Plano 2D 2"}

![Plano 2D 3.](2d/bg3.png){title="Plano 2D 3"}

Mega Man X4 (1997). Planos de fundo do VDP2.

:::

O VDP2 é então instruído a desenhar os planos de fundo. Esses, junto com a camada de sprites, são automaticamente mesclados para formar uma cena totalmente colorida.

A parte de comando é fundamentalmente similar à do VDP1: Os programadores têm os registradores e a VRAM para configurar como precisarem.

Algumas funções do VDP2 podem ser exploradas para a criação de cenários mais realísticos, tal como o redimensionamento usado para simular uma onda de calor (ver "plano 2D 2").

#### Resultado {.tab}

![Mega Man X4 (1997). Os planos mesclados (_Tadaaa!_).](2d/result.jpg) {.tab-float}

Não tem muito mistério aqui: o VDP2 é o responsável pelo último passo, que é enviar o sinal processado para o codificador de vídeo.

O VDP2 opera em sincronia com o raio do CRT, o que quer dizer que suas computações correspondem aos pixels que serão exibidos na próxima scanline.

### Como um console 3D *desafiador* {.tabs-close}

Aqui é onde o Saturn *brilhou e sofreu* ao mesmo tempo. Ainda que esse console tivesse oito processadores para se explorar, no fim tudo era uma questão de:

- Se os programadores seriam capazes de dominar a maior parte dos recursos do console num curto espaço de tempo (lembrando que a vida comercial do console chegaria ao fim assim que seu sucessor fosse lançado, ou sequer anunciado).
- Se o tempo disponível até o lançamento do jogo seria razoável.

Por esse motivo, a maior parte dos jogos acabava variando drasticamente de qualidade, já que cada estúdio trazia sua solução individual.

#### Modelagem 3D {.tabs.active}

![Virtua Fighter Remix (1995).<br>Modelos 3D dos personagens, sem texturas nem fundo. Repare nas primitivas usadas na construção dos modelos.](3d/models.png) {.tab-float}

Até agora haviam sido usados quadriláteros individuais regulares para formar sprites e/ou planos de fundo. Mas e se agruparmos múltiplas primitivas irregulares e as ordenarmos para formar uma figura mais complexa? É assim que surgem os modelos 3D.

Simplificando, consoles 2D clássicos como o [Super Nintendo](super-nintendo) organizam seus gráficos (fundos e sprites) em áreas mais ou menos retangulares. Em alguns casos, tais como o do [Mode 7](super-nintendo#that-feature), os programadores podem providenciar uma matriz de rotação para aplicar transformações sobre algumas dessas áreas. O Saturn, pelo contrário, permite a definição de quadriláteros de 4 pontos com ângulos arbitrários entre suas bordas (a Sega chama isso de "sprites distorcidos"). Então, as capacidades de mapeamento de texturas do VDP pinta a área do quadrilátero com uma textura, esta sendo escalada de acordo com a forma do polígono.

Quanto às operações de que um jogo 3D necessita, as CPUs e a SCU recebem a função de formular um mundo 3D e projetá-lo num espaço 2D. Daí, ambas as VDPs recebem o comando para renderizá-lo, aplicar efeitos e finalmente transmiti-lo para a TV.

#### Processamento de pixels {.tab}

![Virtua Fighter Remix (1995).<br>Cena renderizada com modelos 3D e planos de fundo.](3d/complete.png) {.tab-float}

Qualquer um dos VDPs pode desenhar esse novo espaço (projetado em) 3D e estampá-lo com texturas e efeitos. Agora, qual chip fica "encarregado" disso varia de jogo para jogo.

Certos jogos usavam o VDP1 prioritariamente para desenhar os polígonos mais próximos, deixando o VDP2 para o processamento de cenários mais distantes. Outros encontravam curiosas soluções para encarregar o VDP2 de desenhar polígonos mais próximos (portanto aliviando a carga de geometria do VDP1). O desafio está em projetar um mecanismo eficiente que possa exibir gráficos *impressionantes* enquanto mantém uma framerate aceitável.

### Os novos designs {.tabs-close}

Estes são alguns exemplos de personagens que foram redesenhados para o console. Os modelos são interativos, tente mexer com eles!

![Sonic em Sonic R (1997).<br>185 quadriláteros.](sonic_r_saturn){.toleft model3d="true"}

![Tails em Sonic R (1997).<br>254 quadriláteros.](tails_r_saturn){.toright model3d="true"}

Ainda que o Saturn só seja capaz de desenhar quadrângulos, você logo percebe que esses modelos exibem dois triângulos ao invés de um único quadrângulo no modo "wireframe". Isso porque o formato usado para codificar esse modelo (gITF, um padrão aberto de modelagem 3D moderna), de forma que seu dispositivo moderno possa renderizá-lo, não tem suporte a quadrângulos até o momento em que esse texto foi escrito. Recomendo mudar para o modo "surface" para observar os quadrângulos.

De certa forma, isso demonstra o quão difícil é para a tecnologia gráfica atual reproduzir seus predecessores de 30 anos atrás!

### Uma introdução ao problema da visibilidade

Quando polígonos 3D são projetados num espaço 2D, é crucial determinar **quais polígonos são visíveis da posição da câmera e quais ficam ocultos no plano de trás** [@graphics-vsd]. Senão os modelos não são desenhados corretamente, efeitos como transparência parecerão "quebrados" e, principalmente, recursos de hardware são desperdiçados. Esse processo é mais conhecido como **Visible surface determination (determinação de superfície visível)** ou "VSD", e é um problema fundamental no mundo dos gráficos computadorizados. Há múltiplos documentos publicados que descrevem algoritmos de abordagem a esse problema em diferentes estágios do pipeline gráfico. Alguns dão resultados bastante precisos, e outros trocam essa precisão por uma melhor performance.

Diferente de equipamento acadêmico/profissional, o hardware do consumidor é incrivelmente limitado, portanto a escolha do algoritmo fica reduzida a apenas alguns... Ou até mesmo nenhum.

![Project Z-Treme (2019, Homebrew) [@graphics-ztreme].<br>Essa engine abandonou o Z-sort em troca de particionamento binário de espaço (BSP), corrigindo os glitches.](projectz.jpg) {.open-float}

O método do Sega Saturn é o que considero um caso "meio resolvido". O VDP1 não implementa nenhuma função VSD: Ou você entrega a geometria a ele na ordem correta, ou recebe dele uma bagunça. Entretanto, a Sega tinha uma biblioteca gráfica chamada "SGL", que implementava uma solução chamada **Z-sort** ou **Algoritmo do Pintor** [@graphics-sgl] o qual faz a **ordenação dos polígonos via software**.

Essencialmente, o SGL aloca um buffer para ordenar os polígonos baseando-se na distância deles da câmera (do mais distante ao mais próximo), então envia os comandos de exibição para o VDP1 nessa ordem.

{.close-float}

Um dos problemas do Z-sort com espaços 3D é que o seu valor de distância (Z-order) é **aproximado**, então podem haver glitches visuais. Por causa disso, os desenvolvedores podiam deixar o SGL pra lá e implementar seus próprios algoritmos.

Em artigos mais para a frente, você verá abordagens alternativas. Algumas ainda dependem de software, enquanto outras são aceleradas via hardware.

### A questão da transparência

O Sega Saturn é capaz de desenhar **gráficos semitransparentes**, isto é, de misturar camadas sobrepostas de cores (blending) para criar a ilusão de que podemos ver através delas. Infelizmente, os VDPs não são tão coordenados como se esperaria, então esse efeito não funciona direito quando as camadas estão em VDPs diferentes.

Como alternativa, os jogos podem ativar a propriedade "mesh" numa textura. Com texturas em mesh, o VDP1 define as coordenadas X/Y da textura como "transparentes" (vazias). Isso torna possível misturar outras camadas usando os pixels transparentes. Curiosamente, o mesh aparecia borrado se o console estivesse conectado à TV usando o sinal de vídeo composto (o que era o padrão de antigamente, além do RF), o que resultava numa maneira acidental, porém efetiva de atingir a semitransparência [@graphics-geer].

Como você deve suspeitar, isso não era viável em alguns jogos. No fim das contas, esses não tinham opção além de deixar para lá a semitransparência. Alguns estúdios acharam métodos engenhosos, agora dê uma olhada nesses dois casos:

::: {.subfigures .side-by-side}

![Daytona, da Sega (1993).](daytona){.toleft video="true"}

![Sonic R, da Traveller's Tales (1997).](sonicr){.toright video="true"}

Ambos os jogos mandam o VDP1 desenhar os objetos de primeiro plano e o cenário de fundo. Já o VDP2 renderiza a paisagem distante e as estatísticas de jogo à frente dos modelos 3D. Por consequência, os modelos do VDP1 que tiverem semitransparência não irão refratar a paisagem do VDP2, já que o VDP1 não está ciente dos frame buffers do VDP2.

:::

Além do meu terrível gameplay, você irá reparar que o plano de fundo do primeiro jogo brota do nada (sem semitransparência), enquanto o segundo jogo não apenas conseguiu a semitransparência como também um **efeito de fading**: A Traveller's Tales encontrou uma solução ao mudar os registros da "taxa de mistura" do VDP2 (usados para definir o canal alpha da textura), combinando isso com uma mudança nos níveis de iluminação à medida que o personagem se aproxima [@graphics-burton].

## Áudio

O subsistema de som consiste em várias partes [@audio-scsp]:

- **Motorola 68EC000**: Controla os outros componentes e interfaces junto às CPUs principais. Ele executa um [driver de som](mega-drive-genesis#the-conductor) para operar os componentes vizinhos.
- **Saturn Custom Sound Processor** (SCSP): Também chamado de Yamaha YMF292, é composto de dois módulos:
  - Um **gerador de som multifunção**: Processa até **32 canais** com **samples PCM** (de até 16 bits com 44.1 kHz, a chamada "qualidade de CD") ou [**canais FM**](mega-drive-genesis#audio). No segundo caso, certo número de canais é reservado para operadores.
  - Um **DSP**: Aplica efeitos como eco, reverb e chorus. A documentação menciona também uns "filtros", mas não sei se isso se refere a filtros de envelope ou de frequência (por exemplo, low pass, etc).
- **512 KB de RAM**: Guarda o driver, os dados de áudio (como samples PCM) e também é uma área de trabalho para o DSP.

### A oportunidade

As novas capacidades de áudio significavam que os estúdios finalmente podiam gravar/produzir trilhas sonoras internamente, e colocá-las no jogo sem ter de criar novos arranjos (como acontecia com os [sequenciadores](super-nintendo#audio) limitados, ou com chips com [métodos de sintetização](mega-drive-genesis#audio) restritos).

Isto foi possível graças a uma combinação de vários fatores:

- O novo método de armazenamento de mídia para jogos (CD-ROM) permitia aos desenvolvedores salvar trilhas sonoras grandes.
- O ponto de áudio recebia e misturava os dados PCM com uma qualidade aceitável.
- O subsistema de áudio providenciava energia e largura de banda suficientes para o streaming de dados PCM que estivessem com alguma forma de compressão, e para decodificá-los na hora.

## Sistema Operacional

Assim que o usuário liga o console, o primeiro componente que inicia é o **System Management & Peripheral Control** (SMPC), um microcontrolador de 4 bits que cuida da inicialização dos chips ao redor (coisas como ligar os dois SH-2 e colocá-los em configuração de "master-slave") [@games-smpc].

::: {.subfigures .side-by-side}

![Versão japonesa.](ipl/logo_jap.jpg) {.toleft}

![Versões europeia e americana.](ipl/logo_eu.jpg) {.toright}

O logo exibido após a animação de início.

:::

Depois, o vetor de reconfiguração do SH-2 master é colocado em `0x00000000` [@games-sh2], o que aponta para uma ROM interna que contém o **Initial Program Loader** (IPL). O programa executa as seguintes funções [@operating_system-bootrom]:

1. Finaliza a inicialização do hardware.
2. Se houver um cartucho inserido e ele incluir um programa, ele segue a inicialização a partir dele.
4. Se um cartão de "Video CD" estiver inserido, o inicializa.
3. Se houver um disco inserido, verifica se ele é genuíno.
    - E enquanto isso, ele exibe a animação de início.
4. Se o disco for genuíno, inicie o jogo.
4. Se o disco não for genuíno ou se não houver disco inserido, execute o shell.

### Shell interativo

Além de rodar jogos, o Saturn incluía um player de música chamado "Multiplayer", do qual é possível abrir um gerenciador de saves.

![Shell interativa chamada "Multiplayer" ou "Audio CD Control Panel".](ipl/multiplayer.jpg) {.toleft}

![Gerenciador de memória. Move saves entre o cartucho e a memória interna.](ipl/mem_manager.jpg) {.toright}

Se um Video CD estiver inserido, o player pode reproduzir vídeo MPEG decodificado do próprio cartão.

### Sem BIOS?

Ao contrário do [PlayStation](playstation), cuja ROM continha uma [BIOS](playstation#operating-system), que acabava expondo APIs para uso dos desenvolvedores. A ROM do Saturn costuma ser chamada de "IPL", possivelmente porque sua função principal é inicializar o jogo e executar a shell. Entretanto, esta ainda guardava algumas rotinas (chamadas **serviços**) para manipular o hardware (tal como gerenciar os dados salvos e controle de energia). Ele tem até um "semáforo"! (usado para sincronizar operações que envolvem múltiplos processadores ao mesmo tempo). Por isso, essa parte da ROM é chamada de **System program**.

## Jogos

Jogos oficiais de Sega Saturn são carregados do **leitor 2x CD-ROM**. Sua mídia, o compact disc (CD), tem capacidade de **680 MB**, e os jogos de Sega Saturn seguem o padrão **ISO 9660** de armazenamento de dados [@games-format]. Além disso, muitos jogos armazenam as faixas de áudio próximo às faixas de dados, para streaming de áudio descomprimido enquanto o jogo roda.

### O Compact Disc (CD)

O CD é uma mídia ótica onde a informação é armazenada pela gravação de **pits (sulcos)** e **lands (saliências)** em sua superfície de policarbonato [@cpu-optical]. Uma luz infravermelha é irradiada do leitor e seu reflexo na superfície do CD é usada para ler a informação gravada.

O processo de converter informação digital (zeros e uns) em pits e lands e vice-versa não é simples de forma alguma, especialmente dado que os CDs precisam ser robustos o bastante para suportar os danos do dia a dia e o uso intenso, assim como confiáveis para se guardar qualquer tipo de dado sem medo de perda. Por isso, como parte de sua especificação, os dados são gravados usando-se o modelo **Non-Return-to-Zero inverted** (NRZi), em que o fluxo de bits será totalmente de zeros até que uma mudança de land para pit ou de pit para land seja detectada, à qual o número `1` será atribuído.

O design funciona bem até o leitor encontrar uma sequência de 1 (mudanças contínuas entre pit e land) ou longas sequências de 0 (pits ou lands constantes), durante as quais o sensor terá, respectivamente, dificuldades com a detecção ou com a sincronia. Assim, um modelo adicional é aplicado, chamado modulação **Eight-to-Fourteen** (ETF). Através dele, um punhado de zeros é colocado para preencher os intervalos entre as codificações, ajudando o sensor na leitura.

Ainda outros mecanismos de detecção de erros podem ser adicionados, mas estes estão além do escopo deste artigo. Se estiver interessado em saber mais, você pode checar uma apresentação de slides da RWTH Aachen University [@cpu-optical].

### Desenvolvimento

No começo, a Sega não forneceu bibliotecas de software completas nem ferramentas de desenvolvimento (na verdade, a documentação inicial continha erros), então o único jeito de obter boa performance era por meio de uma *grossa* programação de base.

Posteriormente, a Sega lançou SDKs completos, kits de hardware e algumas bibliotecas para facilitar operações I/O e de gráficos. No geral, os jogos são escritos numa mistura de **C** e várias linguagens de montagem direcionadas a componentes individuais.

### E/S

O gerenciamento de periféricos e do relógio de tempo real são também fornecidas pelo **System Management & Peripheral Control** (SMPC). O SMPC é controlado por comandos enviados pelos SH-2.

### Métodos de expansão

Esse console agrupa um número considerável de conectores externos e interfaces que receberam não mais que uma meia dúzia de usos, quando muito.

- Atrás do drive há um **slot de cartucho** usado oficialmente para **armazenamento adicional** (dados de saves) ou **RAM extra**. No Japão e nos EUA, foi também oferecido um modem para fornecer [funcionalidades online](mega-drive-genesis#early-network-attempts).
- Atrás do console, há um slot para um **Video CD Card** que faz descompactação de MPEG para programas/jogos que tenham suporte.
- Finalmente, há um misterioso soquete na parte traseira do console chamado **Communication Connector**. A Sega não publicou documentação nenhuma para os desenvolvedores, mas após esforços de engenharia reversa, as pessoas descobriram que ele está conectado aos pinos MIDI do SCSP e à interface serial (SCI) de ambos os SH-2 [@games-development]. De todo modo, a Sega lançou um drive de disquete que dependia dessa interface.

## Anti-pirataria & Homebrew

Em resposta à facilidade de se clonar um CD, o Saturn adicionou um sistema de proteção contra cópias (junto a uma trava de região) para controlar a distribuição de jogos.

A proteção contra cópia nos CDs é aplicada gravando-se dados especiais (chamados de "área do sistema") fora do alcance de gravadores convencionais. O Saturn se recusa a inicializar o disco como um "disco de jogo" se os dados da área fora de alcance não forem encontrados ou forem inválidos. O leitor de disco também contém um processador **SH-1** customizado que se comunica com o resto do console usando protocolos ocultos.

Vale mencionar que, já que os CDs do Saturn seguem a norma ISO 9660 (um padrão de sistema de arquivos para dados em CD), os PCs podem ler os discos de jogos sem problemas (mas, claro, não podem executar o jogo a não ser que usem um emulador).

### Derrota

O método clássico usado para desligar a proteção contra cópias consistia em instalar um **mod-chip** capaz de enganar o leitor de CD quando um disco gravado era inserido. Havia também o truque de troca, em que se fazia **hot-swapping** com um disco genuíno, trocando-o por um disco gravado logo após o fim da verificação de proteção... Com o risco de se estragar o drive!

Com a virada do século, foram descobertos métodos alternativos e mais sofisticados de rodar código não autorizado. Por exemplo:

- Uma **brecha no mecanismo de proteção contra cópias** foi descoberta, e ela permitia iniciar qualquer disco de jogo sem que ele precisasse passar pelas verificações contra cópias. Esse método chegou posteriormente à forma de um cartucho, chamado **pseudosaturn** [@anti_piracy-pseudosaturn]. Devido ao uso de cartuchos, os cartuchos de Action Replay costumam ser regravados com o pseudosaturn (entretanto, o gravador do cartucho também precisa ser inicializado de alguma forma, normalmente usando-se o truque de troca).
  - Esse método ainda é usado em 2022, mas um novo fork do pseudosaturn, chamado "Pseudo Saturn Kai" é que é instalado.
- Outro método foi reportado em 2016 (quase 20 anos mais tarde), o qual se aproveita do fato de que o **periférico de Video CD pode injetar código não criptografado** através do subsistema de CD (ignorando o leitor de CD completamente). Isso enfim permitiu que os usuários rodassem homebrew independentemente do desgaste temporal do drive. O truque do Video CD é distribuído comercialmente, como um produto chamado "Satiator" (e aliás, não estou sendo patrocinado).
- E há mais uma alternativa comercial, que substitui o leitor de CD com um adaptador SD ou SATA. O Saturn ainda acredita estar lendo de um CD, mas o "CD" está sendo emulado pelo adaptador, o qual está lendo de uma imagem de disco [@anti_piracy-ode].  Esses produtos são chamados **Optical Drive Emulators (emuladores de drives óticos)** (ODE).

## Isso é tudo pessoal

![Um Saturn japonês que adquiri para obter mais material para esse artigo. Ainda que os jogos em si sejam bons, foi graças à imensa biblioteca de homebrew do Saturn que eu fui capaz de entender as verdadeiras capacidades desse console.](mine.jpg)
