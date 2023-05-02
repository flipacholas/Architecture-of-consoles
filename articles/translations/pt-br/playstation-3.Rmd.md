---
date: 2021-10-20
long_title: Arquitetura do PlayStation 3
short_title: Arquitetura do PlayStation 3
generation: 7
subtitle: Um supercomputador de outro planeta
long_name: PlayStation 3
name: PS3
release_date: 2006-11-11
aliases:
  - /writings/consoles/ps3-private
top_tabs:
  Motherboard:
    caption: "Exibindo a revisão COK-001 (a primeira), retirada do meu modelo CECHA12.<br>Os conectores para a unidade Blu-ray PATA, placa-filha Wifi/BT, painel frontal e leitor de MultiCard, e os 128 MB de memória Flash NAND, estão localizados na parte de trás"
    bib_source: copetti
  Models:
    - 
      active: true
      caption: "O PlayStation 3 original ou \"PS3\". <br>Lançado em 11/11/2006 no Japão, 17/11/2006 na América e 23/03/2007 na Europa"
      title: Original
      file: original
    - 
      caption: "O PS3 série 2000/3000 (também conhecido como  \"<em>Slim</em>\").<br>Lançado em 01/09/2009 na Europa e América; e 03/09/2009 no Japão"
      title: <em>Slim</em>
      file: slim
    - 
      caption: "O PS3 série 4000 (também conhecido como  \"Super <em>Slim</em>\").<br>Lançado em 09/2012 internacionalmente"
      title: Super <em>Slim</em>
      file: super-slim
---

## Uma breve introdução

Em 2006, a Sony revelou o tão esperado console de videogame de "próxima geração", uma máquina brilhante (embora pesada) cuja a arquitetura base do hardware continua os ensinamentos do [Emotion Engine](playstation-2), ou seja, foco no processamento de vetores para obter poder, mesmo com o custo da complexidade. Enquanto isso, o seu novo "super processador", o Cell Broadband Engine, é concebido durante uma crise de inovação e terá que acompanhar as tendências dos serviços multimídias à medida que eles evoluem.

Este artigo examina em profundidade o projeto de parceria da Sony, IBM, Toshiba e Nvidia, junto a sua execução e o efeito na indústria.

### Sobre o tamanho do artigo

Receio que este artigo não seja o típico "intervalo de almoço" que eu geralmente escrevo para os outros consoles desta [série](consoles). Se você estiver interessado em todas as áreas do PlayStation 3, prepare-se para uma jornada completa! Dito isso, esta redação abrange cerca de 6 anos de pesquisa e desenvolvimento realizados por incontáveis engenheiros, então não espero que você digira tudo de uma vez. Por favor, leve o seu tempo (e faça pausas, se necessário) e se, no final ainda quiser mais, consulte a seção de "Fontes"!

```{r results="asis"}
supporting_imagery()
```

## CPU

Bem-vindo à parte mais conhecida e inovadora deste console.

### Introdução

A CPU do PS3 é extremamente complexa, mas também é um trabalho de engenharia muito fascinante que cruza necessidades complexas e soluções incomuns, proeminentes em uma era de mudanças e experimentações. Então, antes de mergulharmos nos detalhes da CPU do PS3, escrevi os próximos parágrafos para trazer um contexto histórico. Dessa forma, conseguiremos decompor o chip de cima a baixo de uma maneira que não só permitirá que você entenda como este chip funciona, mas também compreenda a lógica por trás das principais decisões de projeto.

#### O estado do progresso {.tabs.active}

![A CPU do PS1 (1994). Desenvolvido pela LSI e a Sony, usando a tecnologia MIPS.](cpu/ps1.png) {.tab-float}

![O Emotion Engine do PS2 (2001). Desenvolvido pela Toshiba, novamente com tecnologia MIPS.](cpu/ee.png) {.tab-float}

Quase dez anos após a introdução do [PlayStation original com processador MIPS](playstation), no início dos anos 2000, as coisas não pareciam boas para a CGI/MIPS. A Nintendo tinha recentemente abandonado os processadores MIPS em favor de um [núcleo PowerPC de baixo custo](gamecube#cpu) com a IBM como novo fornecedor, enquanto a Microsoft, a novata deste mercado, [escolheu a Intel](xbox#cpu) e seu império x86.

A Sony tinha um histórico de pegar projetos existentes de baixo custo (núcleos MIPS baratos) e moldá-los para alcançar um desempenho 3D aceitável com custo reduzido, um processo que envolveu outras empresas, como a LSI (para a [CPU do PS1](playstation#cpu)) e a Toshiba (para o [Emotion Engine](playstation-2#cpu) do PS2). Esse método continuou até 2004 com o lançamento do [PlayStation Portable](playstation-portable). Então, qual seria a nova combinação de MIPS que eles iriam construir para o PlayStation 3?

Bem, acontece que o desenvolvimento do PlayStation 3 precede o do PlayStation Portable [@cpu-koranne, p. 5]. Em 2000, meses após o lançamento do PS2, a Sony formou uma aliança com a IBM e a Toshiba chamada "STI" com o único objetivo de entregar o próximo chip que poderia alimentar a próxima geração de supercomputadores [@cpu-engine]. Se isso já não parecesse extravagante o suficiente, o próximo chip também seria usado no sucessor do PS2. No final, em 2004, a IBM apresentou o **Cell Broadband Engine** (também conhecido como "Cell BE" ou simplesmente "Cell") [@cpu-nbc_cell].

#### Novas filosofias de projeto {.tab}

![O chip Cell Broadband Engine. Infelizmente muito brilhante para minha câmera.](cell.jpg) {.tab-float}

Para entender a proposta _radical_, devemos primeiro considerar dos problemas que afetavam aquela época (final dos anos 90 e início dos anos 2000).

A cada ano, os consumidores pediam mais velocidade. Isso sempre foi assim. No entanto, a última abordagem utilizada para resolver isso (construir um [*pipeline* no caminho de dados](xbox#tab-1-4-cisc-or-risc) e aumentar a frequência do *clock*) estava falhando em escalar. O NetBurst da Intel não pôde evoluir mais e seu prometido sucessor não existia. Da mesma forma, o PowerPC 970/G5 da IBM não pôde cumprir sua promessa de "3 GHz" nem de sua CPU ser de baixo consumo de energia (daí a Apple não conseguir lançar nenhum laptop com sua CPU da última geração) [@cpu-no5g]. Resumindo, parecia que os engenheiros tinham uma nova [crise de escalabilidade](playstation#tab-1-1-a-bit-of-history) em mãos.

Então, o foco se voltou para a **computação distribuída** [@cpu-mit]. Em outras palavras, por que se preocupar em aumentar o desempenho de uma única máquina quando você poderia ter várias máquinas menores distribuindo sua carga de trabalho? Por outro lado, essa abordagem não era nova, considerando que todos os consoles analisados neste site contêm mais de um processador. No entanto, foi o desenvolvimento de um "único processador com múltiplos núcleos" que abriu novas oportunidades para o projeto de CPU (que não estavam necessariamente limitadas ao mercado de consoles).

Consequentemente, o Cell fez parte dessa nova onda de pesquisa e desenvolvimento. Essa nova CPU combinou um projeto multi-núcleo com um foco especial no processamento de vetores. Lembre que o processamento de vetores é ótimo para simulações (física, iluminação, etc.) e foi anteriormente materializado na forma do [*Geometry Transformation Engine*](playstation#tab-2-2-geometry-transformation-engine) e do [*Vector Units*](playstation-2#co-cpus), em seguida você verá por que o projeto do Cell foi um grande avanço em relação aos dois anteriores.

#### A nova era dos multinúcleos {.tab}

![Exemplo de projeto heterogêneo.<br> Este está sendo a arquitetura predominante de muitos consoles poderosos até os dias atuais.](cpu/paradigm/heterogeneous.png) {.tab-float}

![Exemplo de projeto homogêneo.<br>Cada núcleo pode realizar as mesmas tarefas como antes, mas não necessariamente está restrito a essa tarefa.](cpu/paradigm/homogeneous.png) {.tab-float}

Se você parar para pensar, tanto o processador do PS1 quanto o Emotion Engine já eram processadores multinúcleos. Então, por que houve tanta empolgação em relação ao Cell? Bem, os dois processadores anteriores eram compostos por um núcleo de propósito geral e vários núcleos de aplicação específica (ou seja, processador de áudio, descompressão de imagem, etc.), misturando diferentes arquiteturas, em que o núcleo de propósito geral é responsável por comandar os outros.

Esse tipo de projeto de CPU é chamado de **computação heterogênea** e era a escolha padrão para construir máquinas especializadas para um conjunto específico de aplicações (jogos, neste caso). Sua contraparte, a **computação homogênea**, é mais predominante no mercado de PC, onde as CPUs são exigidas a realizar uma ampla gama de tarefas (e todas elas têm a mesma prioridade). Portanto, o último tipo pode ter vários núcleos, mas do mesmo tipo.

Voltando ao assunto, o **Cell combina ambos os modelos**: há dois tipos de núcleos nesta CPU, **um "líder" de propósito geral e oito "assistentes" vetoriais**. Esses núcleos vetoriais podem desempenhar vários papéis e, ao fazê-lo, eles cumprem tarefas anteriores que foram originalmente resolvidas por projetos heterogêneos, mas como os vetores do Cell não estão limitados a um único tipo de tarefa, seus núcleos fornecem a flexibilidade dos computadores homogêneos. Resumindo, esse projeto não era perfeito e apresentava algumas limitações, mas ao longo deste texto, você verá os diferentes problemas que o Cell tentou resolver e como ele conseguiu fazê-lo.

### Um olhar sobre o Cell {.tabs-close}

Após ter explicado toda essa história e teoria, acredito que estamos prontos para apresentar o protagonista desta seção. Este é o Cell:

![O Cell Broadband Engine (variante PS3).<br> Projetado pela IBM para supercomputação e simulação científica. O "SPE" riscado significa que está desabilitado (inutilizado). O outro "SPE" à esquerda é reservado para o sistema operacional.](cpu/cell.png)

... e ao final desta seção, você saberá o que cada componente faz.

#### Estrutura

O Cell funciona a uma _impressionante_ velocidade de **3,2 GHz** e é composto por uma infinidade de componentes. Portanto, para fins de análise, essa CPU pode ser dividida em três áreas principais [@cpu-architecture]:

- O **líder**: esta é a parte do Cell que controla os outros do circuitos. Aqui encontramos um componente chamado de **Power Processing Element** (PPE).
- Os **assistentes**: esses são tão cruciais quanto o PPE, mas suas capacidades se limitam a um papel de assistente/acelerador. Esse grupo é composto por oito **Synergistic Processing Elements** (SPEs).
- As **interfaces**: à medida que a necessidade de largura de banda aumenta exponencialmente, novas interfaces são implementadas para transferir os dados sem produzir gargalos. No grupo de interfaces, encontramos um punhado de protocolos: o **Element Interconnect Bus** (EIB), o **Broadband Engine Interface Unit** (BEI), o **Memory Interface Controller** (MIC) e o **Flex I/O buses**.

Essas informações serão revisitadas ao longo deste texto com mais detalhes, para que você não precise memorizar esses nomes. O objetivo principal desta seção é permitir que o leitor tenha uma imagem mental da natureza do Cell e se familiarize com todos os componentes que discutiremos no devido tempo.

#### Como este estudo está organizado

Considerando a estrutura anterior, precisei organizar esta parte do texto para que você não fique sobrecarregado com muitas informações. Assim, começamos a analisar o Cell pelo estudo de cada componente nesta ordem:

1. O barramento que conecta todos os componentes, o **Element Interconnect Bus (EIB)**.
2. O **PowerPC Processing Element (PPE)** e seu elemento principal, a **PowerPC Processing Unit (PPU)**.
3. Qual é a **memória de propósito geral** disponibilizada no console.
4. Os **Synergistic Processing Elements (SPE)** e seu elemento principal, a **Synergistic Processing Unit (SPU)**.
5. O **modelo de programação** desenvolvido para programar o Cell de maneira eficiente.

Dito isso, vamos começar a análise real.

### Dentro do Cell: o coração

Desde o seu anúncio, o Cell tem sido referido como uma ***Network-on-Chip*** (NoC) [@cpu-koranne, p. 62] em vez da definição tradicional do *System-on-Chip* (SoC), isso se deve ao barramento de dados incomum do Cell, o **Element Interconnect Bus** (EIB). [Já vimos](playstation-2#a-recognisable-memory-choice) até agora o quão exigentes os componentes da CPU podem ser, além de quão [suscetível](nintendo-64#tab-3-1-pipeline-stalls) um sistema é a gargalos. Bem, para enfrentar isso pela décima primeira vez, a IBM criou um novo projeto... e o documentou usando termos análogos à _condução de veículos_.

![Diagrama simplificado do Element Interconnect Bus (EIB).<br> Cada seta entre as "<em>Ramps</em>" (nós) representa dois barramentos unidirecionais, assim, cada nó está conectado ao próximo usando quatro canais.](cpu/eib.png)

O EIB é composto por doze nós chamados de **Ramps**, cada um conectando um componente do Cell. Os Ramps são interconectados usando quatro barramentos, dois deles seguem no **sentido horário** e os outros dois no **sentido anti-horário**. Cada barramento (ou **canal**) possui uma largura de banda de 128 bits. Em vez de usar topologias de barramento único (como o Emotion Engine e seu antecessor fazia), os Ramps são interconectados seguindo a topologia em anel (***token ring***), onde os pacotes de dados devem passar por todos os vizinhos até chegar ao destino (não há caminho direto). Considerando que o EIB fornece quatro canais, existem quatro rotas possíveis (**anéis**).

Agora, você pode se perguntar, qual o motivo do uso da topologia em anel se os dados podem acabar viajando por caminhos mais longos (em comparação com um barramento único direto)? Bem, um barramento único é altamente suscetível a [congestionamento](playstation-2#a-recognisable-memory-choice). Portanto, os engenheiros do EIB decidiram por essa topologia para lidar com grandes quantidades de tráfego concorrente (continue lendo se quiser saber como a topologia em anel ajudou).

Os dados são transferidos na forma de **pacotes de 128 bits** [@cpu-cell_arch]. Cada anel pode transportar até **três transferências simultâneas**, desde que os pacotes não se sobreponham. O EIB opera com o uso de **créditos de comando**, ou seja, sempre que um componente precisa iniciar uma transferência, ele envia uma solicitação ao **Data Arbiter** (árbitro de dados, em tradução livre) do EIB, que gerencia o tráfego nos anéis. No momento em que a solicitação é aprovada, os pacotes entram no anel e recebem um "*token*", que o *Data Arbiter* usa como metadados para supervisionar a transferência. Além disso, alguns componentes têm prioridade preferencial sobre outros, como o componente **Memory Interface Controller** (MIC), onde a RAM principal está localizada. Finalmente, o *Data Arbiter* nunca colocará pacotes em anéis cujo caminho é maior que a metade do anel.

Cada Ramp desempenha um papel na transferência, eles leem o endereço de destino do pacote para saber se devem enviar dados para seu respectivo componente ou encaminhá-lo para o próximo Ramp. Durante cada ciclo de *clock*, os Ramps podem receber e enviar pacotes de 128 bits (16 bytes) simultaneamente. Portanto, considerando que existem quatro canais e o **EIB opera a 1,6 GHz** (metade da velocidade do Cell), a taxa máxima teórica de transferência é de `16 bytes x 2 transferências/clock x 4 anéis x 1,6 GHz = 204,8 GB/s`, esse valor é, claro, muito otimista e há vários fatores externos (por exemplo, caminho de origem/destino, estado do barramento, etc.) que condicionam o desempenho. De qualquer maneira, muitos trabalhos de pesquisa feitos pela IBM e outros autores compilaram velocidades mais realistas usando experimentos práticos [@cpu-hpc].

Agora que você viu como cada componente do Cell está interconectado, é hora de conferir o primeiro componente deste chip...

### Dentro do Cell: o líder

Aqui vamos dar uma olhada na "parte principal" do Cell. Essa é a parte do silício encarregada de comandar o resto. O nome do componente é **PowerPC Processing Element** (PPE) e você pode pensar nele como o MIPS R5900 do Emotion Engine.

#### Composição do PPE

Lembra como eu dividi o Cell em diferentes áreas? Bem, o mesmo pode ser feito com o PPE. A IBM usa o termo "elemento" para descrever a máquina independente [@cpu-arevalo, p. 8], mas uma vez dentro dele, usa o termo "unidade" para separar a parte do circuito principal das interfaces que se comunicam com o resto do Cell.

![Diagrama simplificado do PowerPC Processing Element (PPE).](cpu/ppe.png)

Dito isso, o elemento PowerPC Processor é _surpreendentemente_ composto por duas partes:

- **PowerPC Processing Unit** (PPU): essa é a parte lógica do PPE (o "núcleo"). Não esqueça que este não é o [PPU](nes#graphics) da Nintendo! (mesmo que compartilhem as mesmas letras do alfabeto latino... na mesma ordem...).
- **PowerPC Processor Storage Subsystem** (PPSS): a grande interface que conecta a PPU ao mundo externo. Além disso, ela fornece incríveis **512 KB de cache L2**.

Como você pode ver, o projeto do PPE (e do restante do Cell) é bastante modular, o que segue os ensinamentos do projeto RISC. Logo você verá que a modularidade é aplicada até mesmo dentro da PPU.

#### A PowerPC Processing Unit

Vamos dar uma olhada no interior da PPU agora. Para recapitular, mergulhamos no Cell, depois no PPE e, finalmente, na PPU. Vamos analisar a PPU como qualquer outro núcleo de CPU.

##### Uma arquitetura familiar {.tabs.active}

Para começar, a PPU não é construída do zero, mas **reutiliza a tecnologia PowerPC existente**. No entanto, ao contrário de [iterações anteriores](gamecube#cpu) em que a IBM pegou um processador existente e o atualizou pela metade para atender novos requisitos, o PPE não sucede nenhum projeto anterior de CPU. Em vez disso, a IBM construiu uma nova CPU que segue a versão 2.02 da especificação PowerPC (que acontece de ser a última especificação PowerPC antes de ser renomeada para "Power ISA"). Resumindo, você não encontrará o mesmo projeto da PPU em nenhum chip existente daquela época, no entanto, ele é programado usando o mesmo código de máquina que em outros chips PowerPC.

Dito isto, por que a IBM escolheu a tecnologia PowerPC para desenvolver um chip de alto desempenho? Simples, o PowerPC é uma plataforma madura que desfrutou de cerca de 10 anos de testes e revisões por parte dos usuários do Macintosh, atende a todas as necessidades da Sony e, se surgir a necessidade, pode ser adaptado a diferentes ambientes. Por último, mas não menos importante, o uso de uma arquitetura conhecida é uma boa notícia para compiladores e conjuntos de códigos existentes, o que, para um novo console, é uma grande vantagem inicial.

Vale ressaltar que a IBM foi uma das autoras dos primeiros chips PowerPC, juntamente com a Motorola e a Apple (lembre da aliança [AIM](gamecube#cpu)). Seja como for, no início dos anos 2000, os chamados membros da aliança já estavam trabalhando separadamente, onde a Motorola/Freescale desenvolveu uma série diferente de PowerPC da IBM.

##### Recursos distintos {.tab}

A PPU compartilha parte da história com o PowerPC 970 (chamado de _G5_ pela Apple), ambos são descendentes do POWER4, um antecessor do PowerPC usado principalmente em estações de trabalho e supercomputadores. Isso fica mais evidente quando eu lhe mostrar as unidades de execução modularizadas. Esta foi uma mudança radical comparado com a CPU da [linha 750](gamecube#cpu) do GameCube, que teve uma considerável contribuição da Motorola, mas foi ligeiramente modificada pela IBM.

Voltando ao assunto, a PPU é um **processador completo de 64 bits**. Isso significa:

- O tamanho de uma palavra é de **64 bits**.
- Existem **registradores de propósito geral de 64 bits** (para ser exato, 32).
- O barramento de dados tem _pelo menos_ 64 bits de largura de banda: nas próximas seções do artigo, você verá que é **muito mais amplo** do que isso, mas por enquanto, tenha em mente que transferir palavras de 64 bits **não penaliza o desempenho**.
- O barramento de endereço tem 64 bits de largura: na teoria, a CPU pode acessar até **16 exabytes de memória**. Agora, na prática, isso é muito caro se a máquina não abrigar toda essa memória. Então, as CPUs modernas delegam o endereçamento à unidade de gerência de memória (MMU, do inglês * Memory Management Unit*) para fornecer mais usos ao barramento de endereço.

Finalmente, a PPU implementa a **ISA PowerPC versão 2.02**, incluindo instruções opcionais para raíz quadrada de ponto flutuante [@cpu-prog_tut, p. 23]. Também foi estendido com um grupo de instruções SIMD chamadas de **Vector/SIMD Multimedia Extension** (VMX). Por outro lado, alguns elementos estão faltando na especificação original, como o modo *little-endian* (na verdade, o Cell opera apenas em *big-endian*) além de algumas poucas instruções.

#### Os blocos de construção da PPU {.tabs-close}

Ao aplicarmos uma visão "microscópica" à PPU, podemos observar que esta unidade é composta por diferentes blocos ou sub-unidades que realizam operações independentes (carregar valores da memória, realizar operações aritméticas, etc). As capacidades da PPU são definidas pelo que cada bloco pode fazer e como:

##### Instruções {.tabs.active}

![Diagrama simplificado da Instruction Unit (IU).](cpu/ppu/iu.jpg) {.tab-float}

O primeiro bloco é chamado de **Instruction unit** (IU) e, como o nome sugere, ele retira as instruções da cache L2 e sinaliza outras unidades para executar a operação solicitada. Assim como seus [contemporâneos i686](xbox#cpu), parte do conjunto de instruções é interpretada com o uso de [**microcódigo**](nintendo-64#tab-1-1-reality-signal-processor) (a IU incorpora uma pequena ROM para esse fim). Por fim, a IU também possui **32 KB de cache L1 para instruções**.

A emissão de instruções é realizada com um **pipeline de 12 estágios**, embora, na prática o número total de estágios varie muito dependendo do tipo de instrução. Por exemplo, o bloco de **previsão de desvio** (*branch prediction*) pode ignorar boa parte do *pipeline*. Se combinarmos a IU com as unidades vizinhas, o número final de estágios geralmente fica **próximo de 24** (sim, é um número grande, mas lembre-se que o Cell funciona a 3,2 GHz).

Agora para as partes interessantes, a IU é de **dupla emissão**: em alguns casos, a IU emitirá até duas instruções ao mesmo tempo, aumentando assim consideravelmente a taxa de transferência. Na prática, no entanto, existem muitas condições para que isso funcione, portanto, os programadores/compiladores são responsáveis por otimizar suas rotinas para que sua sequência de instruções possa aproveitar essa funcionalidade. A propósito, a emissão dupla (*dual-issuing*) também foi implementada por outras CPUs, e o termo varia entre os fornecedores, então aqui usei a definição da IBM.

Além disso, para completar, a IU também é ***multi-threaded***, onde a unidade pode executar duas sequências diferentes de instruções (chamadas de "*threads*") simultaneamente. Nos bastidores, a IU simplesmente alterna entre os dois *threads* a cada ciclo, dando a aparência de *multi-threading*. Essa técnica é historicamente conhecida como ***simultaneous multi-threading*** (SMT) ou __hyper-threading_*, como a Intel posteriormente chamou. No entanto, o *multi-threading* da IBM mitiga efeitos indesejados como [bloqueios de *pipeline*](nintendo-64#tab-3-1-pipeline-stalls), uma vez que a CPU não ficará mais bloqueada se uma instrução travar o fluxo. Para realizar o *multi-threading*, os engenheiros da IBM duplicaram os recursos internos da IU, que incluem registradores de propósito geral (anteriormente eu disse que existem 32 registradores disponíveis, isso é por *thread*. De fato, existem 64 no total!), no entanto, recursos que não pertencem à especificação do PowerPC (como *cache* L1 e L2; e as interfaces) ainda são compartilhados. Assim, o último grupo é de thread único.

Resumindo, combinando o *threading* duplo com a emissão dupla, a PPU pode executar **até quatro instruções por ciclo**. Mesmo que este seja um "cenário ideal", ele ainda oferece oportunidades de otimização que os usuários eventualmente perceberão na taxa de quadros do jogo!

##### Gerência de memória {.tab}

![Diagrama simplificado da Load-Store Unit (LSU) e sua vizinhança.](cpu/ppu/lsu.png) {.tab-float}

Os blocos seguintes concedem à PPU a capacidade de executar instruções de carga-armazenamento e de realizar gerenciamento de memória.

Para começar, a **Load and Store Unit** (LSU) executa instruções de "carga" e "armazenamento" com suporte de **32 KB de cache de dados L1**. Como consequência, essa unidade tem acesso direto à memória e aos registradores.

Além disso, a LSU é acoplada à **Memory Management Unit** (MMU — Unidade de Gerência de Memória, em tradução livre), o que é uma ocorrência comum no hardware atual. Resumindo, a MMU lida com o endereçamento de memória usando um [mapa de endereços virtuais](nintendo-64#memory-management) combinados com [proteção de memória](playstation-portable#focused-memory-management). Para melhorar este último aspecto, esta MMU em particular apresenta uma **unidade de segmentação**, que agrupa endereços de memória usando faixas de endereço chamadas "segmentos". Agora, para evitar degradação de desempenho no processo, um *Translation Lookaside Buffer* (TLB) (caches de endereços traduzidos) e um *Segment Lookaside Buffer* (SLB) (caches de segmentos) estão incluídos.

##### Aritmética {.tab}

![Diagrama simplificado das unidades que realizam as operações aritméticas.](cpu/ppu/vsu_fxu.png) {.tab-float}

Restam apenas duas unidades do PPU para explicar, aquelas que calculam a matemática que qualquer jogo precisa.

A primeira é uma **Fixed-Point Integer Unit** (FXU — Unidade de Ponto Fixo, em tradução livre) tradicional. Ela realiza aritmética de inteiros, como divisão, multiplicação, rotação de bits (similar ao deslocamento de bits, mas os bits descartados são retornados para a outra extremidade) e contagem de zeros à esquerda (útil para normalizar coordenadas de vértices, por exemplo). Seu *pipeline* possui 11 estágios.

Se você olhar o diagrama, verá que a FXU, LSU e MMU são agrupados em uma única unidade chamada de **Execution Unit** (XU), isso porque eles compartilham o mesmo arquivo de registro.

A segunda unidade é muito mais interessante, a **Vector/Scalar Unit** (VSU) realiza operações com números de ponto flutuante e vetores. Ela é composta por uma **FPU de 64 bits** (segue o padrão IEEE 754) e uma **Vector/SIMD Multimedia Extension Unit** (VXU), que executa um conjunto de instruções SIMD chamado _VMX_. Esses vetores são codificados com 128 bits de comprimento e feitos de dois ou três valores de 8/16/32 bits. Você pode ter ouvido falar dessa extensão antes, já que "VMX" é o nome da IBM para "AltiVec" da Motorola ou "Velocity Engine" da Apple (_viva as marcas registradas_). Por outro lado, as capacidades competitivas SIMD do Cell são encontradas em outro processador, então não relaxe ainda!

#### Concluindo o PPE {.tabs-close}

Você acabou de ver como o PPE funciona e do que ele é feito, mas o que isso significa para um desenvolvedor?

Afinal, o PowerPC Processing Element é apenas um processador de propósito geral, mas aqui está a questão: **ele não é destinado a trabalhar sozinho**. Lembre-se do amplo barramento principal (o EIB)? A IBM projetou o PPE para que os engenheiros pudessem combiná-lo com outros processadores para acelerar aplicativos específicos (ou seja,  HPC, gráficos 3D, segurança, simulações científicas, rede, processamento de vídeo, etc.) e, como este texto é sobre o PlayStation 3, o restante do Cell é tratado tendo em mente gráficos de computador e física, então este texto reflete esse propósito.

### Fora do Cell: a memória principal

Vamos sair um pouco do Cell, pois não importa quão bom seja o PPE se não tivermos um espaço de trabalho adequado (memória) para colocá-lo em funcionamento.

Por isso, a Sony equipou a placa-mãe com **256 MB de XDR DRAM**... Mas, novamente, _o que isso significa_? Para responder a essa pergunta, precisamos dar uma olhada em como funcionam os blocos de memória e como eles se conectam ao Cell.

![O Cell próximo a quatro chips de 64 MB XDR DRAM.](cell_mem.jpg) {.open-float}

Primeiramente, o tipo de memória utilizada é chamado de **Extreme Data Rate** (XDR). Você pode reconhecer a XDR DRAM como sucessora da _problemática_ **RDRAM** encontrada no [Nintendo 64](nintendo-64) e no [PlayStation 2](playstation-2#a-recognisable-memory-choice). Mas não tire conclusões precipitadas ainda!

A Rambus, como qualquer outra empresa, melhora suas invenções. Sua terceira revisão (XDR) opera em taxa óctupla (*octa-rate* — quatro vezes a taxa de sua adversária, a DDR DRAM) [@cpu-xdr]. A latência não é mais um problema, se olharmos para a ficha técnica de seus fabricantes, a latência do XDR é relatada entre 28 ns e 36 ns [@cpu-k4y5016, p. 2], quase [10 vezes mais rápido](nintendo-64#memory-design) do que os chips RDRAM de primeira geração.

`r close_float_group(with_markdown = TRUE)`

A primeira revisão da placa-mãe do PlayStation 3 contém quatro chips de 64 MB, operandos em pares. O XDR é conectado ao Cell usando dois barramentos de 32 bits, um para cada par. Então, sempre que a PPU escreve uma palavra (dados de 64 bits), ela é dividida entre dois chips XDR. Estes últimos são sincronizados em 400 MHz [@cpu-ram].

![Diagrama da arquitetura de memória do Cell.](cpu/memory.jpg)

O Cell se conecta aos chips XDR usando o **Memory Interface Controller** (MIC), outro componente dentro do Cell (como o PPE). Além disso, o MIC armazena em *buffer* as transferências de memória para melhorar a largura de banda, mas possui uma grande limitação: o alinhamento de bytes. Em essência, o tamanho mínimo de dados do MIC para transferência é de **128 bytes**, o que funciona bem para leituras ou gravações sequenciais. Mas se os dados forem menores que 128 B ou se for exigido a alternância entre gravação e leitura, as penalidades de desempenho aparecem.

Dito isso, o MIC é um gargalo ou um acelerador? Bem, você tem que colocar isso em perspectiva, a otimização de largura de banda é crítica em sistemas que exigem muitos dados. No passado, vimos soluções como [*write-gather pipe*](gamecube#ibms-enhancements) ou o [*write back buffer*](playstation-2#preventing-past-mishaps), então o MIC é simplesmente uma nova proposta para um problema recorrente. Vale ressaltar que a Sony afirma que a taxa de transferência é de 25,6 GB/s, no entanto, existem muitos fatores que, na prática, condicionam a taxa final (você viu o quão complicado é mover dados de um lugar para outro dentro do Cell).

Isso é tudo para a RAM principal, mas há mais memória em outros lugares: o disco rígido. O PS3 também permite que os jogos usem **2 GB** do seu disco rígido interno como uma área de trabalho (similar ao que o [Xbox original oferecia](xbox#memory-layout)) [@cpu-files].

### Dentro do Cell: os assistentes

Já [vimos antes](playstation-2#the-leader) como a Sony sempre fornece um processador de propósito geral (o PPE, neste caso), acompanhado de aceleradores para alcançar um desempenho de jogo aceitável (os VPUs e IPU no caso do PS2; e o GTE e MDEC com o PS1). Esta é uma prática comum no hardware de consoles de videogame, já que os processadores de propósito geral podem realizar uma ampla gama de tarefas, mas não são especializados em nada. Consoles de jogos exigem apenas um subconjunto de habilidades (física, gráficos e áudio, por exemplo) então os coprocessadores são usados para fornecer a capacidade de processamento necessária para essas tarefas.

> [O PPE] é uma versão simplificada para reduzir o consumo de energia. Então, ele não tem a potência que você vê, por exemplo, em um Pentium 4(...) Se você pegar o código que roda hoje no seu processador Intel ou AMD, qualquer que seja sua potência, e recompilá-lo para o Cell, ele vai rodar — talvez você tenha que mudar uma ou duas bibliotecas, mas vai rodar, sem problemas. Mas ele será cerca de 50% a 60% mais lento e então as pessoas dirão "Oh meu Deus! Esse processador Cell é terrível!" **Mas isso ocorre porque você está usando apenas uma componente** [@cpu-mit].
> 
> -- <cite>Dr. Michael Perrone, gerente do IBM TJ Watson Research Center's Cell Solutions Department</cite>

Os aceleradores incluídos dentro do Cell do PS3 são os **Synergistic Processor Element** (SPE). O Cell possui **oito deles**, embora um seja desativado durante a inicialização do console. Isso ocorre porque a fabricação do chip requer uma quantidade excepcional de precisão (o Cell inicialmente usou o processo de fabricação de 90nm) e a máquina não é perfeita. Então, em vez de descartar os circuitos que apresentam defeito (<10%), o Cell inclui um SPE sobressalente. Assim, se um deles apresentar defeito, o chip inteiro não é descartado. Agora, esse SPE sobressalente sempre estará desativado, independentemente de estar funcionando corretamente ou não (a Sony não pode ter dois PS3s diferentes no mercado).

#### Composição do SPE

Seguindo em frente, o Synergistic Processor Element (SPE) é um pequeno computador independente dentro do Cell e comandado pelo PPE. Lembra do que eu expliquei antes sobre a adoção de elementos de computação homogênea? Bem, esses coprocessadores são um tanto quanto de propósito geral e não restritos a uma única aplicação, então eles serão capazes de auxiliar em uma ampla gama de tarefas, isto é, desde que os desenvolvedores possam programá-los adequadamente.

![Diagrama simplificado do Synergistic Processor Element (SPE), existem oito dentro do Cell (um fica desativado).](cpu/spe.png)

Assim como fizemos com o PPE, vamos dar uma olhada no SPE. É um tópico mais curto, então se no final você quiser aprender mais sobre os SPEs, confira a seção "Fontes" no final do texto. Dito isso, vamos começar...

O SPE é um processador que segue uma estrutura semelhante à do PPE, sendo composto por duas partes:

##### O Memory Flow Controller {.tabs.active}

O **Memory Flow Controller** (MFC) é um bloco que interconecta o núcleo com o restante do Cell, sendo equivalente ao PowerPC Processor Storage Subsystem (PPSS) no PPE. O trabalho principal do MFC é mover dados entre a memória local da SPU e a memória principal do Cell e manter o SPU em sincronia com seus vizinhos.

Para realizar suas funções, o MFC incorpora um **controlador de DMA** para lidar com a comunicação entre o barramento EIB e a memória local do SPU. Além disso, o MFC abriga outro componente chamado **Synergistic Bus Interface** (SBI) que fica entre o barramento EIB e o controlador DMA. É um circuito muito complexo para resumir, mas basicamente interpreta comandos e dados recebidos de fora e envia sinais para as unidades internas do SPE. Como porta de entrada para o Cell, o SBI opera em dois modos: mestre de barramento (onde o SPE é adaptado para solicitar dados de fora) ou escravo de barramento (onde o SPE é configurado para receber ordem de fora).

Como curiosidade, considerando o limite dos pacotes EIB (comprimento de até 128 bits), o bloco de DMA do MFC só pode mover até 16 KB de dados por ciclo, caso contrário, o EIB gera uma exceção de "erro de barramento" durante a execução [@cpu-hpc, p. 4].

##### A Synergistic Processor Unit {.tab}

A **Synergistic Processor Unit** (SPU) é a parte do SPE onde o processador principal reside, equivalente ao "PPU" quando falamos do PPE.

Ao contrário da PPU, a SPU é isolada do restante do Cell. Portanto, não há memória compartilhada entre a PPU ou outras SPUs. Em vez disso, a SPU contém memória local usada como espaço de trabalho. No entanto, o conteúdo da memória local pode ser movido de um lado para outro usando o MFC.

Em termos de funcionalidade, a SPU é muito mais limitada do que a PPU. Por exemplo, a SPU não inclui funções de gerenciamento de memória (tradução de endereços e proteção de memória) ou mesmo funções de última geração (ou seja, previsão dinâmica de desvio). No entanto, ele funciona excepcionalmente bem no processamento vetorial.

Para programar essa unidade, os desenvolvedores usam a PPU para invocar rotinas fornecidas pelo Sistema Operacional do PlayStation 3, que fazem o *upload* do executável especificamente escrito para a SPU escolhida e a sinaliza para iniciar a execução. Depois, a PPU mantém uma referência à *thread* da SPU para fins de sincronização [@cpu-spuprog].

#### Arquitetura da SPU {.tabs-close}

Como qualquer CPU, a Synergistic Processor Unit (SPU) é programada usando uma arquitetura de conjunto de instruções (ISA, do inglês *Instruction Set Architecture*). Tanto a SPU quanto a PPU seguem a metodologia RISC, no entanto, ao contrário da PPU (que implementa uma ISA PowerPC), a ISA da SPU é proprietária e principalmente composta por um conjunto de instruções do tipo SIMD. Como resultado, a SPU apresenta **128 registradores de propósito geral de 128 bits** que abrigam vetores feitos de valores de ponto fixo ou de ponto flutuante de 32/16 bits. Por outro lado, para preservar a memória, as instruções da SPU são muito menores, com apenas **32 bits de comprimento**. A primeira parte contém o código de operação e o restante pode fazer referência a até três operandos a serem calculados em paralelo.

Isso é muito semelhante antiga [Vector Floating Point Unit](playstation-2#co-cpus) apresentada no PS2, entretanto muita coisa mudou desde então. Por exemplo, a SPU não exige que os desenvolvedores aprendam uma nova linguagem de montagem proprietária - a IBM e a Sony forneceram kits de desenvolvimento para programar as SPUs usando C++, C ou *assembly*.

Em termos de projeto, este processador não executa todas as instruções usando a mesma unidade. A execução é dividida em dois blocos ou "*pipelines* de execução", uma é chamada de ***pipeline* ímpar** e a outra é chamada de ***pipeline* par**. Esses dois *pipelines* executam tipos diferentes de instruções, permitindo que a SPU emita **duas instruções por ciclo** sempre que possível. Por outro lado, a SPU nunca emitirá duas instruções simultâneas que dependam uma da outra, mitigando assim os [riscos de dados](playstation#delay-galore) (*data hazards*) que possam surgir.

Vamos dar uma olhada nos dois *pipelines* [@cpu-hyesoon]:

##### *Pipeline* ímpar {.tabs.active}

![Diagrama simplificado do <em>pipeline</em> ímpar.](cpu/spu/odd.png) {.tab-float}

O *pipeline* ímpar executa a maioria das instruções, exceto as aritméticas.

Em primeiro lugar, você encontrará a **SPU load/store unit** (SLS), que faz três coisas essenciais:

- Contém **256 KB de memória local** para armazenar instruções e dados. O tipo de memória utilizada é de porta única (considerando que esta é uma área crítica, é um pouco decepcionante que eles não tenham usado chips de porta dupla...). Além disso, o barramento de endereço tem 32 bits de comprimento.
- Executa instruções de carga e armazenamento.
- Encaminha instruções para outro bloco para emissão.

Observe que **apenas 256 KB estão disponíveis para armazenar o programa**. Considerando que os programas SPU podem ser compilados usando C/C++, não é fácil prever o tamanho que o programa terá. Por esse motivo, é recomendado que os programas assumam que há apenas metade da memória disponível (128 KB) [@cpu-hpc, p. 4], o que deixa espaço suficiente para que o código compilado ocupe o espaço que precisar, embora isso venha com o custo de armazenamento e eficiência.

Finalmente, também há uma **SPU Channel and DMA Transport Unit** (SSC), que o Memory Flow Controller usa para preencher e/ou buscar memória local, e uma unidade de **ponto fixo** _insignificante_ que apenas faz embaralhamento e rotação de vetores.

##### *Pipeline* par {.tab}

![Diagrama simplificado do <em>pipeline</em> par.](cpu/spu/even.png) {.tab-float}

O * pipeline* par é notável por suas capacidades aritméticas.

Aqui encontramos uma _verdadeira_ **Fixed-point Unit** (FXU) que realiza aritmética básica, operações lógicas (AND, OR, etc.) e deslocamento de palavras.

Por último, mas não menos importante, temos uma **Floating-point Unit** (FPU) que realiza operações de precisão simples (`float` de 32 bits), dupla precisão (`double` de 64 bits) e inteiro (`int` de 32 bits). Segue o padrão IEEE com algumas divergências (os pontos flutuantes se comportam de maneira semelhante ao [PS2](playstation-2#the-leader)).

### Dentro do Cell: estilos de programação {.tabs-close}

Como chegamos no final do Cell, você pode se perguntar como os desenvolvedores devem programar esse _monstro_? Bem, de forma semelhante aos [modelos de programação anteriores](playstation-2#you-define-the-workflow) criados para o Emotion Engine, a IBM propôs as seguintes metodologias [@cpu-prog_tut, p. 13]:

#### Abordagens centradas no PPE

![Representação do padrão de vários estágios, em que o PPE atribui uma tarefa passada por cada SPE e, eventualmente, retornada com os dados processados.](cpu/programming/multistage.png){.tabs-nested .active title="Multiestágio"}

![Representação do padrão paralelo, em que o PPE atribui uma sub-tarefa para cada SPE e, por sua vez, cada SPE retorna os dados processados, que o PPE mescla.](cpu/programming/parallel.png){.tab-nested title="Paralelo"}

![Representação do padrão de serviços, em que o PPE aloca uma tarefa diferente para cada SPE, e cada um trabalha individualmente para cumprí-la.](cpu/programming/services.png){.tabs-nested-last title="Serviços"}

As abordagens centradas no PPE são um conjunto de padrões de programação que colocam as principais responsabilidades no PPE e deixam o SPE para a descarga de trabalho. Existem três possíveis padrões:

- **Modelo de *pipeline* multiestágios**: o PPE é encarregado de enviar trabalho para um único SPE, que por sua vez realiza os cálculos necessários e passa os resultados para o próximo SPE. Isso continua até que o último SPE na cadeia envie os dados processados de volta ao PPE.
  - Por razões óbvias, a IBM não sugere este projeto para tarefas principais, pois requer uma quantidade considerável de largura de banda e tende a ser difícil de manter.
- **Modelo de estágios paralelos**: o PPE divide sua tarefa principal em subtarefas independentes e envia cada uma para um SPE diferente. Cada SPE retorna os dados processados ao PPE depois de concluídos, e o PPE os combina para produzir o resultado final.
- **Modelo de serviços**: cada SPE recebe a atribuíção de uma tarefa única (por exemplo,  decodificação MPEG, *streaming* de áudio, projeção de perspectiva, iluminação de vértice, etc.) e o PPE é responsável por enviar dados brutos para o SPE designado. Enquanto espera, o PPE realiza outras funções.
  - Embora isso implique que cada SPE terá um único trabalho, sua designação de trabalho não é destinada a durar para sempre. O PPE deve realocar diferentes trabalhos dinamicamente à medida que as necessidades do programa mudam.

#### Abordagens centradas no SPE

![Representação do padrão centrado em SPE, em que cada SPE é responsável por sua funcionalidade e interage apenas com o PPE para obter um recurso.](cpu/programming/spe_centric.png)

Em vez de usar os SPEs para servir o PPE, é o contrário. Usando a unidade interna de DMA, os SPEs buscam e executam tarefas armazenadas na memória principal, enquanto o PPE é limitado ao gerenciamento de recursos.

Este modelo é muito mais radical do que o restante, no sentido de que os padrões anteriores estão mais próximos do paradigma "processador geral de propósito com coprocessadores". Assim, as bases de código que implementam algoritmos centrados no SPE podem ser mais difíceis de portar para outras plataformas.

### Conclusão

Como você pode imaginar, embora o projeto multi-núcleos do Cell acelere técnicas emergentes, como [geração procedural](playstation-2#infinite-worlds), nenhum desses projetos é particularmente simples de implementar, especialmente considerando que os estúdios de jogos preferem bases de código que possam ser compartilhadas em diferentes plataformas.

Para dar um exemplo, os desenvolvedores do Unreal Engine 3 (Epic Games) demonstraram as limitações dos SPUs ao tentar implementar seu sistema de detecção de colisão [@cpu-unreal]. Seu projeto depende do *Binary Space Partitioning* (BSP — partição binária do espaço, em tradução livre), um algoritmo fortemente dependente de comparações (*branching*). Como as SPUs não fornecem previsão dinâmica de desvios (*dynamic branch prediction*) como a PPU, sua implementação decepcionou os usuários do PlayStation 3 quando comparada lado a lado com outras plataformas (ou seja, Xbox 360 ou PCs [i386](xbox#tab-1-3-the-microarchitecture), ambos que fornecem técnicas avançadas de previsão em todos os seus núcleos). Assim, a Epic Games teve que recorrer a mais otimizações compatíveis apenas com o Cell.

Suponho que seja apenas uma questão de tempo, paciência e muito aprendizado para que os engenheiros de software aproveitem todo o potencial do Cell. No entanto, a história demonstrou [que isso não é viável para todos os estúdios](sega-saturn#as-a-challenging-3d-console), o que me faz pensar se esse é o motivo pelo qual o hardware do console atual (a partir de 2021) se tornou tão homogêneo.

## Gráficos

Se você pensou que o Cell, com todas as suas peculiaridades, poderia cuidar de todas as tarefas do console, então deixe-me dizer algo engraçado: **a Sony instalou um chip separado para gráficos 3D**.

::: {.subfigures .tabs-nested}

![Uncharted 3: Drake's Deception (2011).](games/uncharted3.jpg){.active title="Uncharted"}

![The Elder Scrolls V: Skyrim (2011).](games/skyrim.jpg){title="Skyrim"}

![Killzone 3 (2011).](games/killzone.jpg){title="Killzone"}

![One Piece: Pirate Warriors (2012).](games/onepiece.jpg){title="One Piece"}

Exemplos de jogos de PS3. Todos são renderizados na resolução máxima (1280x720 píxeis).

:::

Parece que mesmo com um chip de supercomputador, a Sony ainda teve que buscar uma GPU para finalizar o PlayStation 3. Isso faz você se perguntar se a IBM/Sony/Tpshiba encontrou algum obstáculo ao tentar escalar ainda mais o Cell, então a Sony não teve outra opção senão buscar ajuda de uma empresa de processadores gráficos. No entanto, isso é pura especulação e não tenho certeza se, algum dia, saberei a resposta.

O que eu sei é que o PS3 contém um chip GPU fabricado pela **Nvidia**, destinado a aliviar parte do *pipeline* gráfico. O chip é chamado de **Reality Synthesizer** ou "RSX" e roda a **500 MHz** [@graphics-rsx_dev]. Sua frequência de *clock* parece preocupante quando comparada com a do Cell (3,2 GHz), mas veja que a GPU é melhor equipada para realizar quantidades grandes de operações em paralelo. Portanto, é uma questão de encontrar um equilíbrio entre o Cell e o RSX quando se trata de construir o *pipeline* gráfico (embora eu deva confessar que isso parece mais simples no papel do que na prática).

Agora, farei a análise do RSX e suas capacidades gráficas no mesmo nível que fiz com o Cell.

### Visão geral

Já tinha se passado cinco anos desde que a Nvidia lançou a [linha GeForce3/NV30](xbox#graphics) em 2001, e na época o mercado de processadores gráficos era disputado por empresas fortes como a 3dfx, a S3 e a ArtX/ATI. No entanto, nos anos seguintes, o número dessas empresas reduziu lentamente até o ponto que, em 2006, apenas a ATI e a Nvidia permaneciam como os principais fornecedores de placas de vídeo no mercado de PCs.

![O chip RSX próximo ao Cell.](rsx.jpg) {.open-float}

O RSX herda a tecnologia existente da Nvidia, é referido como sendo baseado no modelo 7800 GTX vendido para PCs, que implementa a arquitetura **GeForce7** (ou NV47) [@graphics-nvarch], também chamada de "Curie".

Na minha [análise anterior do Xbox](xbox#graphics), eu falei sobre a GeForce3 e seus estreantes *shaders* de píxel. Então, o que mudou? Houve altos e baixos, mas principalmente mudanças incrementais, nada muito revolucionário em comparação com os *shaders* de píxel da GeForce3.

`r close_float_group(with_markdown = TRUE)`

Por outro lado, enquanto o 7800 GTX depende do protocolo PCI Express para se comunicar com a CPU, o RSX foi remodelado para trabalhar com um protocolo proprietário chamado **Flex I/O** [@graphics-gschwind], uma interface diferente dentro do Cell projetada para se conectar a chips vizinhos. O Flex I/O opera em dois modos:

- O modo **BIC** para conectar outros processadores Cell (a ser usado em ambientes com múltiplos processadores).
- O modo **IOIF** mais lento para conectar até dois periféricos, um "rápido" e um "lento".

Infelizmente, o RSX não é o Cell, então ele passa pelo protocolo IOIF, usando o *slot* mais rápido.

Para fins de comparação, o IOIF funciona como um barramento paralelo de 32 bits com uma largura de banda teórica de até 20 GB/s, enquanto o PCI-Express usado no 7800 GTX (x16 1.0) é um barramento serial de 16 bits com uma largura de banda teórica de até 4 GB/s.

### Organizando o conteúdo

O RSX tem **256 MB de GDDR3 SDRAM dedicados** à sua disposição. Surpreendentemente, é o [mesmo tipo de memória](wii#what-about-memory) encontrada no Wii. O barramento de memória funciona a **650 MHz** com uma largura de banda teórica de até **20,8 GB/s**.

![Exemplo de como os dados são organizados na memória disponível. Observe como o RSX pode acessar seu conteúdo dos diferentes chips de memória.](gpu/content.png)

Dentro desses 256 MB, o Cell pode colocar tudo o que o RSX precisa para renderizar um quadro. Isso inclui os dados de vértices, os *shaders*, as texturas e os comandos. Graças ao barramento Flex I/O do Cell, o RSX também pode utilizar os mencionados 256 MB de memória XDR (RAM principal da CPU) como espaço de trabalho, embora isso acarrete algumas penalidades de desempenho. Isso é útil se o quadro renderizado for processado posteriormente por um SPU, por exemplo.

Como pode ser visto, embora o console não tenha implementado uma arquitetura UMA (do inglês, *Unified Memory Architecture*), **ainda é possível distribuir dados gráficos por diferentes chips de memória** se os programadores decidirem fazê-lo. Menciono isso porque gostaria que muitos "analistas técnicos" lessem mais sobre esses recursos antes de gritar declarações super resumidas como "O PS3 era limitado porque não tinha arquitetura UMA". Isso pode ser verdade em certos casos, mas, a menos que mencionem esses recursos, essa afirmação genérica é, na minha opinião, **enganosa**.

Por fim, o RSX suporta muitas formas de otimização de dados para economizar largura de banda, exemplos incluem compressão de cores 4:1, compressão de profundidade e modo "*tiled*" (explicarei mais sobre isso mais tarde).

### Construindo o quadro (*frame*)

Vamos dar uma olhada em como o RSX processa e renderiza cenas em 3D.

![Visão geral do <em>pipeline</em> do RSX.](gpu/pipeline.png)

Seu modelo de *pipeline* é muito semelhante ao da [GeForce3](xbox#architecture-and-design), mas foi turbinado com cinco anos de progresso tecnológico. Então, sugiro verificar este artigo [@graphics-marcelina] antes, já que este texto foca nas novas funcionalidades. Também recomendo ler sobre a [GPU do PlayStation Portable](playstation-portable#functionality), pois muitos dos novos desenvolvimentos e das novas necessidades se sobrepõem com a desse chip. Dito isso, vamos ver o que temos aqui...

#### Comandos {.tabs.active}

![Diagrama do estágio de comando.](gpu/commands.png) {.tab-float}

Assim como acontece com qualquer outra GPU, deve haver um bloco de circuito encarregado de receber as ordens de fora. Com o RSX, isso é tratado por dois blocos: **Host** e **Graphics Front End**.

O Host é responsável por ler os comandos da memória (seja local ou principal) e traduzi-los em sinais internos que outros componentes do RSX entendam. Isso é feito com o uso de quatro sub-blocos:

- O **Pusher**: busca comandos gráficos da memória e [interpreta](playstation-portable#tab-2-1-commands) as instruções de desvio (*branch*). Ele também contém 1 KB de [*buffer* de pré-busca](game-boy-advance#memory-locations). Os comandos processados são enviados para uma *cache* FIFO.
- **Cache FIFO**: armazena até 512 comandos decodificados pelo Pusher obedecendo à ordem de chegada na *cache* para fornecer acesso rápido.
- **Puller**: coleta comandos da *cache* FIFO sempre que o RSX está pronto para renderizar e os envia para a próxima unidade.
- **Graphics FIFO**: armazena até oito comandos que serão lidos pelo Graphics Front End.

O Graphics Front End lê do Graphics FIFO e sinaliza para as unidades necessárias dentro do RSX para computar as operações. Caso você lembre, isso é equivalente ao "pfifo" na GeForce3.

Como pode ser visto, comandos e dados passam por muitos *buffers* e *caches* antes de chegar ao destino final. Isso é intencional, pois evita que o *pipeline* fique parado devido as diferentes unidades e barramentos operando em velocidades diferentes. Assim, a memória *cache* tira aproveito da largura de banda rápida sempre que possível.

#### *Shader* de vértice {.tab}

![Diagrama do processo do estágio de vértice, observe que os Vertex Processing Engine (VPE) são ignorados se os vértices não precisam de mais serem processados pelo vertex shader.](gpu/vertex.png) {.tab-float}

A próxima unidade é o bloco de **Geometry Processing**, uma evolução do "Bloco de Vértices" da GeForce3 que realiza a transformações de vértices. Permanece programável com o uso de **shaders de vértices**, que atualmente é um recurso amplamente utilizado na indústria de gráficos. Além disso, o limite de instruções foi aumentado para um mínimo de 512 instruções (originalmente, 136 era o limite!).

O bloco que executa *shaders* é chamado de **Vertex Processing Engine** (VPE) e pode processar **um vértice por pulso de *clock***. Como se não bastasse, há **oito VPEs trabalhando em paralelo**. Desde a série GeForce6, a Nvidia alinhou sua interface de programação de *shader* a um modelo chamado "*Vertex Shader Model 3*" ou "vs_3_0_", um padrão desenvolvido pela Microsoft para uso com suas bibliotecas DirectX 9.0c [@graphics-vs3]. Os VPEs também são compatíveis com o modelo não proprietário OpenGL 2.1 [@graphics-kilgard] e com a variante da Nvidia (Cg) [@graphics-vp40].

Comparado com a GeForce3, temos novas instruções disponíveis para desvios e chamadas de sub-rotina. Além disso, o VPE contém quatro amostradores de textura (*texture samplers*) que buscam as cores das texturas durante este estágio, caso os programadores queiram usar esta unidade para realizar algumas operações nelas.

O bloco de Geometry Processing funciona da seguinte maneira:

1. O **Index Vertex Processor** (IDX) busca e armazena na *cache* de dados de vértices e texturas da VRAM. Em seguida, envia os dados para o VAB.
2. O **Vertex Attribute Buffer** (VAB) busca os dados na *cache* IDX e os redireciona para cada VPE.
3. Cada VPE processa os dados com base no *shader* carregado. Ele computa **uma instrução de *shader* por pulso de clock**.
4. O resultado de cada VPE é enviado para o **Post Transform Cache**, que armazena em *cache* os resultados para pular cálculos idênticos sobre o mesmo vértice. Isso se aplica se [índices de vértices](gamecube#tab-1-2-geometry) forem usados em vez de dados de vértices.
5. O resultado final é armazenado na **Viewport Cull Unit** (VPC), que aplica corte para descartar vértices encontrados fora da janela de visualização; e na **Attribute RAM** (ATR), que armazena em *cache* os atributos dos vértices (textura, cor, neblina, etc.) a serem lidos nas próximas etapas.

#### Rasterização {.tab}

![Diagrama simplificado do estágio de rasterização. O RSX incorpora unidades diferentes para calcular os valores usados para interpolação de píxeis e cores.](gpu/rasterizer.png) {.tab-float}

Seguindo, é hora de converter ([rasterizar](playstation#tab-4-3-rasterization)) os vértices em píxeis. O rasterizador do RSX é bastante rápido, ele pode rasterizar até 8x8 píxeis (64) por ciclo e funciona com um *buffer* de imagem até **4096x4096 píxeis** (embora os desenvolvedores possam precisar de menos que isso).

O rasterizador aceita pontos, linhas (incluindo tipos fechados e em tiras), triângulos (incluindo tiras e leques), quadriláteros e polígonos regulares. Naturalmente, como nesta geração de consoles, o rasterizador trabalha com **coordenadas de subpixel**, onde os pontos de amostragem são meias-coordenadas (`0,5`) de píxeis. Isso permite que a unidade aplique métodos de anti-serrilhamento, como o **multiamostragem** (*multisampling*), posteriormente. A multiamostragem consiste em rasterizar a mesma geometria várias vezes, mas deslocando alguns subpíxeis a cada lote (o RSX suporta quatro modos de deslocamento) e, em seguida, calcular uma média. Isso resulta em uma imagem suavizada.

Além disso, essa unidade também realiza **ocultação de profundidade** (*z-culling*) usando a RAM dedicada dentro do RSX (com capacidade de cerca de três milhões de píxeis). Isso economiza o processamento de píxeis e estênceis que já foram renderizados e permite realizar **[testes de profundidade](nintendo-64#modern-visible-surface-determination) (*z-test*) antecipados** na geometria que está chegando.

Uma unidade separada é usada para rasterizar objetos 2D (*sprites*), embora esta seja isolada do *pipeline* 3D. Consequentemente, o RSX trabalha em dois modos, 2D e 3D, mas alternar entre eles, intermitentemente, é caro em termos de desempenho.

#### *Shader* de píxel {.tab}

![Diagrama do estágio de Píxel/Fragmento.](gpu/pixel.png) {.tab-float}

A próxima unidade é o bloco **Fragment Shader & Texture**, que é uma unidade programável (por meio do uso de "programas de fragmentos" ou "*shader*") que aplica mapeamento de texturas e outros efeitos.

Sendo o sucessor avançado das [unidades de textura](xbox#tab-2-3-pixel) da GeForce3, o novo bloco contém **seis unidades de fragmento** (também chamadas de "*pipes*"), cada uma processando 2x2 *texels* (chamados de "*quads*"). Para organizar várias unidades trabalhando simultaneamente, outro sub-bloco chamado **Shader Quad Distributor** (SQD) é colocado para distribuir *quads* para cada unidade de fragmento. Depois, cada unidade de fragmento carrega o programa de fragmentos.

Para realizar operações, cada *pipe* contém uma _enorme_ quantidade de **1536 registradores de 128 bits**. Além disso, cada *pipe* pode processar vários *quads* em paralelo (***multithreading***), embora o número de *quads* processados em paralelo dependa do número de registradores alocados para o programa de fragmento (`número` `de threads = 1536 ÷ número de registradores reservados pelo shader`). Em termos globais, até 460 *quads* podem ser processados em paralelo. Além disso, até três *pipes* de fragmentos podem processar duas instruções em simultâneo (**emissão dupla**, como a PPU) contanto que as instruções não dependam uma da outra.

As unidades de fragmento fornecem instruções semelhantes em termos aritmeticos à unidade de vértice, com a adição de instruções relacionadas a textura, com vários tipos de busca de textura (uma vez que as texturas podem ser codificadas usando muitas estruturas e, em seguida, comprimidas) e descompactação. Assim como o bloco de vértice, o *shader* de fragmento segue o modelo *shader* de píxel 3.0 do DirectX [@graphics-vs3], o perfil NV_fragment_program2 do OpenGL [@graphics-khronos] e o perfil "fp40" do Cg [@graphics-fp40]. Tudo isso para facilitar a programação e evitar aprender APIs de baixo nível desde o início.

Por fim, como as unidades estarão constantemente buscando fragmentos de textura da RAM de vídeo ou da RAM principal, este bloco inclui três *caches* de textura: **4KB de *cache* L1** para cada *pipe*, **48 KB de *cache* L2** para buscas na RAM de vídeo; e **96 KB de *cache* L2 para a RAM principal**. Observe que a *cache* da RAM principal é significativamente maior, isso foi uma decisão consciente para compensar a maior latência.

#### Operações de píxeis {.tab}

![Estágio de pós-processamento.](gpu/post.png) {.tab-float}

Antes de escrever os resultados no *buffer* de imagem (armazenado na VRAM ou na RAM principal), um bloco final chamado **Raster Operation Block** (ROP) realiza testes finais nos píxeis resultantes.

Existem dois conjuntos de ROPs compostos por quatro blocos cada (oito no total). Cada grupo realiza teste de profundidade (*z-testing*), mistura alfa e a gravação final na memória. No total, este circuito pode processar até 16 valores de profundidade (*z-values*) e 8 cores de píxel por pulso de *clock*. Curiosamente, a variante para PC, Nvidia 7800 GTX, apresenta 16 ROPs em vez de 8, talvez esse corte tenha sido feito para priorizar a largura de banda de memória consumida pelas SPUs?

Para economizar ainda mais largura de banda, os ROPs também fornecem compressão de cor e compressão Z (*z-compression*). Além disso, há um **Tiling mode** disponível para otimizar o acesso à memória pelo codificador de vídeo. No *Tiling mode*, o *buffer* de imagem é armazenado em blocos contínuos de 128 B ordenados da mesma forma que seriam transmitidos/escaneados. Assim, a GPU é poupada de fazer trocas de páginas (usadas para endereçamento de memória) enquanto transmite o *buffer* de imagem para exibição, melhorando consequentemente a largura de banda. Esses '_tiles_' são armazenados em locais marcados na memória exclusivos para esse tipo de endereçamento.

### Uma saída de vídeo unificada {.tabs-close}

Foram-se os dias dos conectores de vídeo exclusivos de consoles e dezenas de sinais analógicos espremidos juntos em um único conector para acomodar todas as regiões do planeta. O PlayStation 3 finalmente incorporou um sinal de vídeo unificado que logo foi adotado em todo o mundo: a **High Definition Media Interface** (HDMI), usada para transferir áudio e vídeo simultaneamente.

![Traseira do PS3, há uma saída HDMI do lado esquerdo e, no outro extremo, a antiga saída de vídeo analógico Multi A/V.](cecha/back.jpg) {.open-float}

O conector HDMI é composto por 19 pinos [@graphics-hdmi_wiki], todos em um único soquete. Ele transfere um sinal digital, o que significa que a imagem e o áudio são transmitidos usando valores discretos de zero e um (e não uma série de valores contínuos encontrados em sinais analógicos). Consequentemente, ele não sofre com interferência ou degradação de imagem que equipamentos anteriores sofriam, como artefatos na tela produzidos por cabos SCART baratos.

Até hoje, o protocolo HDMI é continuamente revisado [@graphics-hdmi_spec], com novas versões da especificação oferecendo mais recursos (por exemplo, resolução de imagem maior, taxa de atualização, espaço de cores alternativos, etc.) enquanto mantém o mesmo meio físico para compatibilidade com versões anteriores.

`r close_float_group(with_markdown = TRUE)`

Durante o ciclo de vida do PS3, a Sony adicionou certas funcionalidades das novas revisões do HDMI no console por meio de atualizações de software [@graphics-hdmi_dev]. O último protocolo compatível com o PS3 é a versão 1.4, trazendo suporte para "televisores 3D", embora outras capacidades, como resoluções de vídeo mais altas, permaneçam limitadas a **1920x1080** píxeis (e mesmo assim, a maioria dos jogos renderizava seu *buffer* de imagem em 1280x720 píxeis).

### Visão/projeção 3D "real"

Mas o que é esse "televisor 3D" que eu mencionei antes? Bem, acontece que a vida útil do console coincidiu com uma febre passageira por TVs 3D (as chamadas _3DTV_) @graphics-3dtv]. Para suportá-las, a Sony atualizou seu SDK para ajudar na renderização de quadros estereoscópios na RSX e implementou a "especificação 3D" em seu codificador HDMI. O que acontecia nos bastidores é que o codificador transmitia dois quadros por vez, e a televisão os alternava de maneiras semelhantes ao que os óculos 3D do [Master System](master-system#tab-4-3-3d-glasses) faziam 30 anos antes.

## Áudio

Receio que você não encontrará muitas informações nesta seção, principalmente porque desde o [PlayStation Portable](playstation-portable#audio), o áudio mudou _silenciosamente_ para o lado do software. Em outras palavras, não existem mais chips de áudio dedicados.

Observe que, enquanto a necessidade de gráficos melhores tende a crescer exponencialmente (os consumidores querem mais cenários, detalhes e cores melhores), não há a mesma demanda por som. Presumo que isso ocorre porque as capacidades técnicas já alcançaram nosso limite cognitivo (taxa de amostragem de 44,1 kHz e resolução de 16 bits). A única coisa que ainda resta é implementar mais canais e efeitos, mas para isso não é necessário tanto poder de processamento a ponto de exigir a instalação de chips dedicados, pelo menos para equipamentos de uso doméstico.

![Resumo do <em>pipeline</em> de áudio.](audio.png)

Então, no fim das contas, o áudio agora é completamente implementado com software e processado pelas SPUs (quero dizer pelas Synergistic Processor Units, não pela [Sound Processing Unit](playstation-2#audio)! É um pouco irônico que ambos compartilhem a mesma sigla...). Continuando, a Sony fornece diversas bibliotecas em seu SDK que instruem as SPUs a realizar sequenciamento, mixagem e transmissão de áudio. E se isso não for suficiente, muitos efeitos também podem ser aplicados.

Dito isso, onde o sinal de áudio é enviado para transmissão? Para o **RSX**. Esse chip também contém as portas usadas para transmitir sinais de áudio brutos para a TV. Antes de enviar, o sinal é codificado em diferentes formatos, dependendo da saída selecionada (analógica, HDMI ou S/PDIF, este último também é chamado de "áudio digital").

## Entrada/Saída e a retrocompartibilidade.

Todas as operações de entrada e saída são delegadas a outro chip robusto chamado **Southbridge** [@io-southbridge]. Isso é muito semelhante à arquitetura [adotada](xbox#motherboard-architecture) pelo Xbox original lá atrás. Parece que a diferença das arquiteturas dos consoles está se tornando menor, ou talvez essa abordagem tenha se mostrado muito confiável e seja agnóstica em relação à arquitetura, deixarei você decidir.

![O grande chip Southbridge supervisionando os pequenos chips e interfaces de E/S.](southbridge.png){.tabs-nested .active title="Original"}

![A mesma imagem com as partes importantes rotuladas.](southbridge_marked.png){.tabs-nested-last title="Ilustrado"}

Assim como no [IOP](playstation-2#io) do PS2, o Southbridge é completamente proprietário, embora feita desta vez pela Toshiba (eles a chamaram de "*Super Companion Chip*" (chip super companheiro, em tradução livre) [@io-supercompanion]). Portanto, embora ainda seja um pedaço de silício obscuro, ele faz um trabalho superior em consolidar muitas interfaces e protocolos, tanto externos (ou seja,  USB, Ethernet, etc.) quanto internos (ou seja,  SATA). Para referência, no passado, a baixa velocidade do *clock* do IOP acabava [causando gargalos](playstation-2#available-interfaces) nas interfaces rápidas como ATA e Ethernet, reduzindo muito sua largura de banda total.

Além disso, o Southbridge implementa algoritmos de criptografia para proteger a comunicação entre protocolos de maneira transparente, como os dados do disco rígido.

![Diagrama das conexões do Southbridge.](southbridge_diagram.png)

Em geral, o Southbridge incorpora uma quantidade enorme de interfaces, isso se deve ao fato de que o console foi projetado durante a tendência das "centrais multimídia". Não era suficiente para os consoles de videogame apenas reproduzirem jogos, eles também precisavam se tornar reprodutores de DVD e Blu-ray, decodificadores (parcialmente), visualizadores de fotos (importando as fotos da câmera usando o leitor de cartões múltiplos) e possivelmente realizar mais outras funções a medida que as necessidades evoluiam (graças ao seu sistema oeracional atualizável).

### Interfaces externas

![Como muitos gabinetes de PC da época (incluindo o meu), um leitor de multi-cartões era necessário. Abaixo, você tem quatro portas USB 2.0.<br>Isso era bastante "premium" para um console que custava £425! (£628 em 2021).](cecha/front_open.jpg){.open-float .tabs-nested .tab-float .active title="Aberto"}

![O mesmo console com a tampa fechada.<br>Para reduzir ainda mais os custos, modelos posteriores selaram a baía e removeram o leito de cartões e duas portas USB.](cecha/front_closed.jpg){.tabs-nested-last title="Fechado"}

No caso das portas acessíveis pelo usuário, o Southbridge está conectada a:

- Um *hub* **USB 2.0**: fornece **quatro portas frontais USB-A**. Estas portas podem ser usadas para acessórios ou para conectar/carregar os controles.
- Uma interface **Serial ATA** (SATA): conecta a unidade de Blu-ray e um disco rígido de 2,5 polegadas.
  - Até 2008, leitores de Blu-ray se conectavam com ATA paralelo (PATA) [@io-bd_rev], então um chip intermediário foi instalado para fazer a conversão SATA → PATA.
- Um **Controlador Ethernet 1000/100/10 (Gigabit)**: na forma de um conector RJ45 na parte de trás, mas também se ramifica para uma placa-filha sem fio, fornecendo conexões **Wi-Fi 802.11b/g** e **Bluetooth 2.0**.
- Um **leitor de cartões múltiplos**: fornece *slots* para cartões do tipo [Memory Stick](playstation-portable#tab-5-2-memory-stick-duo), SD, MultiMediaCard (MMC), Microdrive e Compact Flash.

`r close_float_group(with_markdown = TRUE)`

#### Equipamentos com "menos fios"

Graças à ampla adoção da tecnologia Blutetooth, os controles com fio se tornaram coisa do passado. A nova versão do controle [Dualshock 2](playstation-2#interactive-accessories) do PS2 foi chamada de **Sixaxis** (seis eixos, em tradução livre) e, embora não seja a [mudança radical](wii#next-gen-controllers) que outra fabricante decidiu seguir, apresenta um giroscópio para novos tipos de interação. Entretanto, isso ocorreu removendo o feedback tátil (_vibração_). Um ano depois, a Sony surpreendeu os jogadores com o **Dualshock 3**, que restaurou o motor tátil.

Uma outra novidade, agora é possível ligar o console pelo controle sem fio.

### Interfaces internas

Com relação aos componentes internos, a *SouthBridge* se conecta a:

- **Starship 2**: um adaptador para **duas memória Flash NAND de 128 MB**. Nos bastidores, o Starship faz a ponte entre o barramento local da Southbridge e o protocolo padronizado "Common Flash Interface Protocol" (amplamente adotado para a interface de memória Flash) [@io-starship]. O PS3 armazena o sistema operacional nesses chips, entre outras coisas.
- O **chipset do PlayStation 2**: no canto da placa-mãe, há um chip chamativo que abriga nada menos que o [Emotion Engine](playstation-2#cpu) e o [Graphics Synthesizer](playstation-2#graphics). A combinação EE+GS se conecta a **32 MB de RDRAM** e uma **ponte de E/S** (nomeada "ponte PS2"), que juntas formam cerca de 90% do PlayStation 2 original.
  - O chip EE+GS envia o sinal de vídeo diretamente para o RSX.
  - Esses chips não são acessíveis pelos desenvolvedores, eles são usados apenas para a retrocompatibilidade!

#### Retrocompatibilidade

Tendo mencionando os chips do PS2, suponho que seja hora de falar sobre a retrocompatibilidade do PlayStation 3 de uma vez por toda.

Primeiramente, deixe-me explicar como a retrocompatibilidade geralmente funciona: os consoles podem reproduzir os jogos de seus predecessores com a ajuda do **software** (instruindo o hardware existente a se comportar como o console antigo) e do **hardware** (onde o hardware existente fornece retrocompatibilidade total ou parcial; e/ou a empresa adicionou chips extras para recriar o sistema antigo dentro da nova placa-mãe). Com a quantidade de poder de processamento que o PS3 apresenta, seria de se esperar que a Sony colocasse um emulador de PS2 rodando dentro do Cell e acelerado pelo RSX. Bem, por alguma razão, isso não aconteceu e, em vez disso, a Sony encaixou o conjunto de chips do PS2 em um canto da placa-mãe.

![O grande chip EE+GS, dois módulos de 16 MB de [RDRAM](playstation-2#a-recognisable-memory-choice) e a "ponte PS2".](eegs.jpg){.tabs-nested .active title="Original"}

![A mesma imagem com as partes importantes rotuladas.](eegs_marked.jpg){.tabs-nested-last title="Ilustrado"}

Por outro lado, os chips ausentes que não eram críticos (IOP, SPU, etc.), foram replicados via software e rodavam no Cell. No caso dos salvamentos de jogos, inicialmente, os usuários precisavam adquirir um adaptador para o *memory card*, mas, após uma nova atualização de software, os *memory cards* começaram a ser emulados como imagens de disco armazenadas no disco rígido, enquanto o Magic Gate (o sistema de criptografia) era manipulado perfeitamente por uma SPU.

Como o Cell e o RSX ficam ainda "ativos" durante a reprodução de um jogo de PS2, o sistema oferece dois métodos de redimensionamento para aumentar a resolução da tela durante o jogo. Os métodos são "vizinho mais próximo" ou "suavizado" (antisserrilhamento).

![A interface de usuário do PS3 mostrando a entrada do jogo após inserir um disco de PS2.<br>(Nâo se preocupe com os outros ícones por enquanto, pois alguns nem são oficiais).](xmb/ps2.jpg)

Em resumo, graças a essa configuração, o PS3 roda jogos do PS2 com um nível de compatibilidade impressionante. Além de tudo, você pode aproveitar os novos recursos que vêm com o novo console (controle sem fio, interface HDMI e cartões de memória virtual).

Como se não bastasse, os jogos de PS1 também podem ser executados, desta vez sem precisar incorporar o antigo SoC ou GPU (ele depende de software de emulação pura).

#### O estranho fim da retrocompatibilidade via hardware

Ao longo do ciclo de vida do PS3, a Sony lentamente removeu os chips exclusivos do PS2 da placa-mãe do PS3, chegando ao ponto em que a retrocompatibilidade fosse apenas feita através software de emulação (com maiores limitações, como apenas rodar jogos de PS2 adquiridos em sua loja online). Como a Sony nunca substituiu o chipset do PS2 (como havia feito anteriormente com o hardware do PS1 dentro do PS2), faz você se perguntar sobre o racional técnico e executivo por trás disso. Bem, como um estudo de caso, aqui está minha opinião rápida sobre os motivos para isso:

- **Momento**: é provável que a Sony tenha planejado que os proprietários de PS2 comprassem seu novo produto como substituto do atual, já que isso é mais acessível para os consumidores (eles podem vender seu antigo sistema). No entanto, por algum motivo, a Sony não havia preparado um software de emulação antes do dia do lançamento, então inicialmente recorreu à adição de chips extras. Depois, à medida que o software de emulação progredia satisfatoriamente, eles gradualmente removeram esses chips em revisões posteriores da placa-mãe.
  - Para complementar, o desenvolvedor "M4j0r" comentou: "Um ponto interessante pode ser que a Sony desenvolveu as duas revisões de emulação via hardware simultaneamente (EE/GS e GS somente), acredito que alguns jogos rodam melhor dependendo de qual você usa" [@operating_system-psxplace].
- **Custos**: o preço inicial da primeira revisão do console (_CECHA_, apenas no Japão e EUA) em 2006, que era compatível com o PS2, foi fixado em US$ 599,99 ou ¥60.000,00 sem impostos (£425,00 em 2020 com correção da inflação) [@io-launch_fun]. O modelo seguinte (_CECHC_, lançado em 2007 internacionalmente) removeu o Emotion Engine e a RDRAM (transferindo essas tarefas para o software de emulação) e foi lançado no Reino Unido com um preço de etiqueta de £425,00 (£603,00 em 2020). Mais tarde, no mesmo ano, a Sony lançou um novo modelo (_CECHG_) sem nenhum chip do PS2 por £126 a menos [@io-price_cut]. Tudo isso prova que a retrocompatibilidade é, no final, uma funcionalidade cara.
- **Hardware ocioso e desperdício de energia**: embora o Cell e o RSX ainda executem algumas tarefas para recriar o ambiente original, estas são mínimas em comparação com o seu potencial total. Combinado com o fato que os modelos CECHA têm um consumo de energia acumulado de 399 Watts [@io-psu], faz você se perguntar se este projeto vale o consumo de energia, muito menos eficiente (para comparação, a nova fonte de alimentação do CECHG consome 285 Watts).
  - Eu entendo que existem outros fatores envolvidos na redução do consumo de energia, como as novas revisões do Cell e do RSX. Contudo, ainda acredito que o chipset do PS2 desempenhou um papel importante.
- **Inflexibilidade**: o chip EE+GS não é reprogramável, o que significa que o resultado final sempre será o mesmo, independentemente de haver falhas ou possíveis melhorias. Compare isso com as melhorias gráficas do emulador PCSX2 [@io-widescreen_hack] e suas capacidades de modificação [@io-persona_mods], isso nos mostra que há espaço para melhorias que são possíveis e apreciadas.

Pessoalmente, acredito que a emulação puramente via software seja a opção mais viável a longo prazo devido à sua escalabilidade, personalização e independência de hardware proprietário. Mas é claro que isso exige mais esforço para ser implementado com precisão, como demonstra o contínuo desenvolvimento do PCSX2 por uma comunidade de voluntários (observe que o mencionado emulador só roda em PCs x86).

#### Compatibilidade lateral

Ainda não terminamos de falar sobre compatibilidade! Você pode ficar surpreso que a Sony permitiu que os usuários jogassem um subconjunto de jogos do [PlayStation Portable](playstation-portable#games). Embora a emulação tenha sido realizada completamente via software, assim como a compatibilidade com o PS2 em modelos posteriores.

Como não há nenhum leitor de [disco UMD](playstation-portable#tab-5-1-umd-discs) no PS3, os usuários devem acessar um catálogo de jogos da loja online da Sony para baixar e instalar qualquer jogo do PSP.

## Sistema Operacional

Agora que os consoles domésticos se tornaram poderosas centrais multimídias, foi necessário ter um sistema operacional mais _complicado_ para fornecer aos usuários mais serviços e jogos com uma camada maior de abstração. Tudo isso, mantendo a segurança e o desempenho adequado.

Consequentemente, termos como [shell](playstation-2#operating-system) ou [BIOS](playstation#operating-system) não são mais usados para descrever esta área, não porque não existem mais, mas devido descreverem apenas uma pequena fração do novo sistema. O termo genérico agora é "sistema operacional", que compreende muitas áreas (carregador de inicialização, kernel, interface do usuário) analisadas separadamente. Como sempre, recomendo verificar o [sistema operacional do PSP](playstation-portable#operating-system) primeiro, já que seu projeto modular é um ingrediente recorrente no PS3.

### Níveis de privilégio do Cell

Antes de entrarmos em detalhes, preciso mencionar os diferentes modos de operação no Cell. Eu originalmente planejava descrever isso na seção "CPU", mas como aquela seção ficou incrivelmente densa, vou apresentá-lo aqui onde você verá seu uso prático imediatamente. Além disso, seus modos também afetam o projeto de qualquer sistema operacional que execute dentro do Cell — não apenas o que a Sony desenvolveu para o console.

Dito isso, para proteger contra o acesso não autorizado aos dados e/ou recursos sensíveis, o Cell implementa um conjunto de **níveis de privilégio** herdado da especificação PowerPC. Em outras palavras, o Cell executa os programas em dois modos:

- **Modo privilegiado**: o Cell concede acesso a todos os cantos do seu hardware (registradores, endereços de memória, instruções, etc.) [@cpu-cell_arch-book, p. 183]. Por razões de segurança, este modo só deve ser usado pelo núcleo do sistema operacional (ou seja, o Kernel).
  - Além disso, o Cell foi projetado para **executar vários sistemas operacionais simultaneamente** e para alcançar isso no nível de hardware, o "modo privilegiado" pode ser dividido em dois subníveis: **Privilegiado 1** e **Privilegiado 2**. O "Privilegiado 2" é destinado a ser usado por um Kernel, enquanto o "Privilegiado 1" é usado por um **Hypervisor**, este último arbitra recursos entre diferentes Kernels que executam simultaneamente.
  - A funcionalidade "hypervisor" também se tornou uma área de pesquisa na sede da IBM \[@operating_system-watson_hypervisor\] \[@operating_system-shype\].
- **Modo usuário**: como o nome indica, o Cell concede apenas um conjunto limitado de recursos [@cpu-cell_arch-book, p. 32] e é direcionado para aplicativos tradicionais que rodam em cima do sistema operacional. Se por algum motivo, um programa solicitar acesso a um local protegido, então a execução é direcionada ao Kernel ou Hypervisor para decidir se o acesso deve ser concedido ou não.

Além disso, os SPEs contêm um *modus operandi* chamado **modo isolado** e ele protege o processo a execução dentro da SPU, de modo que nenhuma unidade externa (o PPE ou outros SPEs) possa acessá-la até que a SPU termine. O modo isolado pode ser ativado após o envio de um programa para qualquer SPE e garante que o processador não seja alterado enquanto um código sensível (ou seja, uma rotina de criptografia) está executado.

O sistema operacional da Sony, que descreverei nos parágrafos a seguir, usa todos os modos descritos para lidar com sua segurança.

### Visão geral

Como eu disse antes, o sistema operacional é bastante complexo. Portanto, para podermos seguir esta seção sem muita dificuldade, podemos dividir o tipo de arquivos que encontraremos no sistema operacional do console em camadas diferentes:

- **Loaders** (carregadores): para resumir, os programas/binários nesse console são sistematicamente criptografados. Assim, os "Loaders" são programas que executam programas "reais". Em outras palavras, os Loaders pegam os binários, descriptografam, verificam sua autenticidade e, finalmente, enviam para o respectivo processador (o PPE ou SPE) para execução. Se isso não parece complicado, os Loaders são ligados em cadeia para proteger ainda mais o software. Finalmente, os Loaders são encontrados em muitas mídias.
  - Alguns Loaders são atualizados pela Sony (por meio de atualização de software), enquanto outros não podem ser alterados. Isso é independente de serem instalados em armazenamento regravável, já que alguns Loaders são criptografados usando chaves específicas do console, então não podem ser alterados depois que o console sai de fábrica (ao menos pelos meios tradicionais).
- **System files** (arquivos de sistema): esses compreendem de binários de baixo nível (executados através de Loaders), metadados para organizar o hardware, utilitários e outros recursos (ou seja, fontes, imagens). Assim como os Loaders, há arquivos de sistema específicos do console que não podem ser substituídos ou gerados automaticamente.
  - Alguns binários pegaram emprestado o código dos projetos FreeBSD e NetBSD [@operating_system-opensource].
- **User Content** (conteúdo de usuário): esses incluem arquivos de configuração (por exemplo, configurações da internet), dados usados por jogos (por exemplo, arquivos de instalação e salvamento de jogos) e dados gerados automaticamente pelo console (por exemplo, informações de disco rígido).
  - Ao contrário das outras camadas, a destruição desses dados não leva a resultados catastróficos.

### Hierarquia de segurança do sistema operacional

Em termos gerais, o sistema operacional do PS3 é projetado com a mesma abordagem modular do PSP. Relembrando do artigo anterior, o sistema operacional é composto de vários **módulos**. Esses podem servir ao usuário (como um jogo ou aplicativo) ou residir na memória indefinidamente para servir outros módulos (na forma de chamadas de sistema e/ou drivers). Alguns módulos têm mais acesso privilegiado do que outros (módulo do Kernel versus módulo do usuário).

![Diagrama mostrando como os componentes do sistema operacional do PlayStation se encaixam nos níveis de privilégio do Cell.<br>Referência ao "OtherOS" são explicadas com mais detalhes nas próximas seções.](oslevels.png)

O sistema operacional, durante seu ciclo de vida, chamará muitos módulos com diferentes privilégios. A Sony construiu seu sistema operacional para que os módulos rodem nos três níveis de privilégio do Cell:

- **Nível 1**: aqui é onde reside um **Hypervisor** programado pela Sony. Também referido como `lv1`, este programa é a porta para cada bit do console e vinculado as exceções acionadas pela MMU. Dito isso, o hypervisor só aceita solicitações de programas autorizados pela Sony (residentes no próximo nível de privilégio). Enquanto o Hypervisor reside na memória, ele também fornece chamadas de sistema de baixo nível e suporte ao sistema de arquivos FAT16.
- **Nível 2**: naturalmente reservado para o **Kernel**, um programa privilegiado também chamado `lv2` ou "Supervisor". O Kernel abstrai o hypervisor para que os programas do nível 3 não tenham interação direta com ele. O Kernel fornece funções de *multithreading* tanto para PPU quanto para SPU. Por fim, o Kernel inicializa os módulos do espaço do usuário.
- **Nível 3**: o restante dos programas (chamados espaço do usuário), incluindo **jogos** e a **interface visual**, são executados neste nível. Estes _programas_ estão sob o controle do Kernel para se comunicar com o hardware do console e não podem criar processos/programas novos unilateralmente.

### Mídia de armazenamento

Com tudo isso dito, onde todos esses dados são armazenados? Do ponto de vista do usuário comum, existem apenas dois meios visíveis: os discos Blu-ray para jogos e um disco rígido para *saves*. Bem, existem mais alguns, então vamos dar uma olhada em cada um deles agora!

#### BootROM do Cell {.tabs.active}

Acontece que dentro do Cell há uma pequena ROM escondida em algum lugar que os fabricantes podem usar para armazenar um *boot-loader* (carregador de inicialização) "protegido". A IBM fornece esse espaço para poupar as empresas (não apenas a Sony) de terem que implementar manualmente [seus próprios métodos de ofuscação de código](xbox#system-protection) para proteger seu código de inicialização, já que os componentes disponíveis comercialmente nem sempre estão preparados para suas necessidades específicas.

Como essa memória já está fisicamente protegida com ofuscação de código, ela não precisa ser criptografada. Desta forma, ela é ideal para um *boot-loader* de primeiro estágio (que não pode ser criptografado) e o PlayStation 3 armazena seu estágio inicial de inicialização nela.

#### Memória Flash NAND/NOR {.tab}

Lembre-se daquelas **256 MB de memória flash NAND** que mencionei brevemente antes? Bem, é aqui que fica a maior parte do sistema operacional. Isto é, era até que a Sony lançou o modelo _CHECHH_ no final de 2007, substituindo as NANDs de 256MB por uma **NOR de apenas 16 MB**. Como consequência, alguns arquivos tiveram que ser movidos para outro lugar. Para fins de simplicidade, vamos primeiro ver o que esses chips armazenam [@operating_system-flash]:

- **Carregadores específicos do console**: especificamente, dois carregadores chamados `bootldr` e `metldr`. Esses arquivos são criptografados com uma chave gravada durante a fabricação, portanto, não podem ser substituídos!
  - Seja como for, existem funções ocultas no hipervisor da Sony que permitem atualizá-los, embora, por alguma razão, nunca tenham sido usadas [@operating_system-psxplace].
- **CoreOS**: a primeira metade do sistema operacional. Consiste principalmente em mais carregadores que continuarão o processo de inicialização e, eventualmente, a inicialização a segunda metade (**GameOS**). O CoreOS também fornece o **menu de recuperação**, um *shell* alternativo que contém os utilitários de manutenção que os usuários podem usar para (tentar) reparar seu console.
- **IDs únicos**: semelhantes ao [IDStorage](playstation-portable#tab-3-2-kernelipl) do PSP, eles são usados pelo console para controlar o hardware protegido como a unidade de Blu-ray; ou pela Sony para autenticar o console em seus servidores online (ou seja, a chave IDPS).
- **Recursos de segurança**: alguns programas dependem deles para executar operações de segurança. Por exemplo, filmes em Blu-ray com DRM verificam um bloco chamado *Virtual Table Rights Management* (VTRM — tabela virtual de gestão de direitos, em tradução livre). A Sony também armazena **ferramentas de revogação e registros** para fazer uma lista negra de certificados de segurança que foram comprometidos no passado.

Devido ao seu tamanho maior, os modelos com memória Flash NAND também armazenam a parte restante do sistema operacional (chamada de **GameOS** ou `devflash`). Isso inclui:

- A **Interface Visual** (VSH, de *Visual Shell*): uma continuação da [interface característica do PSP](playstation-portable#visual-shell), também é fornecida com toneladas de módulos (*plugins*) e recursos.
- **Emuladores**: programas mencionados anteriormente que permitem que o PS3 execute jogos de PS1, PS2 ou PSP. O emulador específico do PS2 carregado depende da revisão do console (se ele tem o hardware completo do PS2, ou o hardware parcial do PS2, ou se não tem nenhum hardware do PS2).
- **Bibliotecas de tempo de execução**: programas desenvolvidos com o SDK da Sony são vinculados dinamicamente a um conjunto de bibliotecas armazenadas aqui.
- **Reprodutor de Blu-ray**: programas que lidam com a interação com a unidade de Blu-ray e a decodificação de filmes.
- **Recursos do sistema**: como fontes e certificados nos quais os binários dependem para funcionar.

Como se não bastasse, os consoles com NAND também armazenam outros dados, como o **xRegistry** (coleção de configurações de rede, contas da PlayStation Network e uma lista de dispositivos Blutooth emparelhados), mas registros de revogação e um Carregador para o **OtherOS** (uma peça realmente interessante que discutiremos mais nos próximos parágrafos).

#### Disco rígido {.tab}

O estreante disco de 2,5 polegadas, com capacidade de armazenamento que variou de 20 GB a 500GB (conforme mais revisões foram lançadas), fornecia armazenamento persistente de dados para:

- **Conteúdo do usuário**: incluindo *saves* de jogos, troféus (mais informações na seção "Jogos") e outros dados relacionados ao usuário.
- **Recursos de jogo**: jogos podem copiar arquivos do disco Blu-ray para o disco rígido para melhorar os tempos de carregamento. Eles são tratados como "dados do jogo" pelo sistema operacional.
- **Cache**: uma partição separada de 2 GB está disponível para jogos para armazenamento temporário (caso a RAM principal não seja suficiente).

No entanto, os sistemas NOR também armazenam o GameOS no HDD. Como consequência, sempre que o usuário troca o disco rígido, o console solicita um arquivo de atualização para reinstalar o GameOS no disco. Seja como for, nenhum dos sistemas NOR ou NAND será inicializado sem o disco rígido.

Certos dados do usuário podem ser salvos em um pendrive USB e depois transferidos para outro console, se necessário, embora esse processo reformate o console mais novo antes de copiar os dados antigos.

#### Memória eMMC {.tab}

Em 2012, a Sony lançou uma revisão redesenhada do console chamada "SuperSlim" (codinome CECH-4xxx). Estes estavam disponíveis em três versões: uma com um disco rígido de 250 GB, outra com um disco rígido de 500 GB ou uma terceira opção com apenas uma **memória flash eMMC interna de 12 GB**. As duas primeiras opções seguem o projeto de sistema de arquivos implementado em modelos com memória NOR, enquanto a terceira armazena tudo na memória eMMC (incluindo dados do usuário) e segue o projeto com memória NAND para armazenar os arquivos do sistema.

Contudo, há uma pegadinha no modelo com memória eMMC. Em vez do chip de memória NOR, a Sony encaixou um chip Panasonic "MN66840", que, segundo o PS3 Dev Wiki [@operating_system-MN66840], parece redirecionar o barramento da memória NOR para a memória eMMC. Presume-se que isso seja apenas um truque para economizar custos, já que recicla a mesma Southbridge das outras versões.

Curiosamente, se o usuário decidir colocar um disco rígido no modelo com memória eMMC, o console move todos os dados do usuário da memória eMMC para o novo disco rígido. Como consequência, o usuário pode usar totalmente o disco rígido, embora o espaço vazio deixado na memória eMMC fique sem uso.

### Processo de inicialização {.tabs-close}

Certo, utilizando todo o conhecimento anterior, você vai aprender como o sistema inicia — e antecipo: é bastante complicado. A motivo é simples: a Sony não quer que você mexa no hardware ou software deles, então eles construíram várias camadas de ofuscação e criptografia para impedir que você invada e carregue seu próprio código (e esperançosamente _desista e continue comprando jogos/filmes/o que for_), porém, como a história nos conta, o oposto aconteceu.

Nas próximas linhas, detalho o que este console faz assim que você pressiona o botão de ligar. Note que este processo só mudou drasticamente uma vez (depois que hackers o quebraram). Então, para fins de simplicidade, começamos com o processo de inicialização "original" (implementado antes da versão de sistema `3.60`) \[@operating_system-levin\] \[@operating_system-boot\] [@operating_system-the_exploit]:

1. Um chip separado na placa-mãe (chamado de **Syscon**) é ligado e executa instruções de sua ROM interna. Em seguida, envia um "Anel de Configuração" para o Cell via SPI (uma conexão serial), isso inicializa o Cell e desativa o oitavo SPU. Por fim, ele liga a linha de energia e dá vida ao Cell.
2. O vetor de reinicialização da PPU do Cell aponta para a sua ROM oculta, que armazena as rotinas para localizar e descriptografar o `bootldr` na memória Flash. A parte descriptografada é então carregada pela primeira SPU em modo de isolamento.
3. Após carregar o `bootldr`, a SPU, já isolada, inicia parte do hardware (a memória XDR e as interfaces de E/S), descriptografa um binário chamado `lv0` e instrui a PPU a executá-lo.
4. A PPU, agora executando `lv0`, descriptografa o `metldr` (um carregador específico do console) e o envia para a terceira SPU, novamente em modo de isolamento.
5. A SPU2, agora executando o `metldr`, executa mais cinco carregadores sequencialmente:
    1. O `lv1ldr` descriptografa e carrega o `lv1`, que contém o **Hypervisor** que assume o primeiro nível de privilégio. Além disso, `lv1` configura o disco rígido, a unidade de Blu-ray e o RSX.
    2. O `lv2ldr` descriptografa e carrega o `lv2`, que contém o Kernel e é executado em cima do hypervisor. Ele também finaliza a inicialização do RSX, a emulação de PS2, o controlador Bluetooth, o controlador USB e o leitor Multi-card.
    3. O `appldr`descriptografa e carrega o `vsh` ( o **Visual Shell**) e outras dependências. Depois, o `vsh` permite que o usuário carregue um jogo.
    4. O `isoldr` descriptografa e carrega módulos que serão executados na terceira SPU em modo de isolamento. Esses módulos são críticos para a segurança e realizam muitas funções criptográficas ao longo do ciclo de vida do console. Consequentemente, a **terceira SPU é reservado para funções de segurança e os jogos não podem usá-lo** (deixando apenas seis SPEs disponíveis para os jogos).

A PPU, após carregar o `vsh`, concede o controle ao usuário através de uma interface gráfica de usuário, que se manifesta com um _som de orquestra icônico_ seguido do menu _XMB_.

#### Processo de inicialização revisado

Em março de 2011, um hacker conhecido pelo nome "GeoHot" quebrou a segurança do `metldr`, comprometendo assim a autenticidade dos carregadores subsequentes. Portanto, a Sony revidou emitindo atualizações de segurança em seu hardware e software. Essas correções são discutidas em maiores detalhes mais a frente na seção "Antipirataria e Homebrew".

### Visual Shell

Você está ficando cansado de toda essa teoria? Deixe-me mudar para algo que todos possam realmente ver: A **Visual Shell**.

A **XrossMediaBar** (XMB), uma nova interface de usuário que ganhou reconhecimento internacional [dois anos antes](playstation-portable#visual-shell), foi ligeiramente adaptada para que possa ser interagida do sofá (chamada de "*10-foot user interface*" - "interface de usuário de 10 pés, ou 3 metros", em tradução livre) e expandida para aproveitar a resolução "full HD" (1920x1080 píxeis).

![XMB no PSP (2004).<br>Renderizado em 480×272 píxeis.](images/consoles/psp/xmb/main.jpg) {.toleft}

![XMB no PS3 (2006).<br>Renderizado em 1920x1080 píxeis.](xmb/main.jpg) {.toright}

Embora os usuários do PSP encontrem muitas semelhanças, a Sony adicionou um novo conjunto de aplicativos que usam o potencial do Cell, do RSX e da unidade de Blu-ray. Muitos deles relacionados a multimídia (como o reprodutor de vídeo e a apresentação de slides de imagens), televisão (como aplicativos de TV sob demanda, como o iPlayer da BBC), perfil social (avatares online) e compras online (PlayStation Now e PlayStation Store, só para citar alguns).

Além disso, como este é um console doméstico que pode ser compartilhado por várias pessoas, a XMB suporta **múltiplos usuários**, onde cada um pode usar uma conta diferente da PlayStation Network e armazenar seus dados de usuários separados (jogo comprados e *saves*).

![Assim como no PSP, destacar um jogo pode estilizar o plano de fundo para chamar sua atenção!.](xmb/game.jpg){.tabs-nested .active title="Jogo"}

![O XMB fornece uma imensa quantidade de configurações, especialmente úteis quando você precisa configurar sua novíssima TV 1080p com áudio <em>surround</em> 3.1.](xmb/settings.jpg){.tab-nested title="Configurações"}

![Várias opções multimídia.](xmb/multimedia.jpg){.tab-nested title="Multimídia"}

![O XMB pode instalar jogos, atualizações e expansões (DLCs) usando um instalador de pacotes nativo.](xmb/installpkg.jpg){.tabs-nested-last title="Instalação"}

Por fim, a inclusão de um disco rígido é um alívio para os jogadores veteranos (do PSP) que, no passado, eram obrigados a comprar armazenamento proprietário caro ([Memory Stick Pro Duo](playstation-portable#tab-5-2-memory-stick-duo)) sempre que ficavam sem espaço.

#### Empreste-me o seu PS3

De maneira impressionante, nem todos os aplicativos incluídos no console tinham objetivos _egoístas_. Com o advento da computação distribuída e as capacidades do Cell para projetos de ciências de dados, a Universidade de Stanford se uniu à Sony para permitir que os proprietários do PlayStation 3 contribuíssem para pesquisas médicas. O resultado disso foi a **Folding&#64;home**.

A Folding&#64;home consistia em um aplicativo instalado em todos os PlayStation 3 que, uma vez aberto pelo usuário, se conectava a um servidor central e executava simulações de proteínas. Além disso, o aplicativo também podia ser executado em segundo plano durante os horários de menor uso.

![O Folding&#64;Home exibe o trabalho realizado desde que o usuário iniciou o aplicativo [@operating_system-folding_app].](xmb/folding.jpg)

Ao longo de sua vida útil, o poder de computação combinado de 15 milhões de usuários do PS3 em todo o mundo ajudou o Folding&#64;home em suas pesquisas para a cura de doença de Alzheimer [@operating_system-protein_retirement]. No final, a Folding&#64;home e a Sony aposentaram o aplicativo em 2012 e o projeto continua em outras plataformas.

Pessoalmente, gosto de ler sobre projetos que fazem contribuições globais usando as capacidades da computação distribuída, ao invés dos artigos sensacionalistas intermináveis sobre mineração de criptomoedas. Creio que não podemos esquecer que, com cada nova tecnologia poderosa, sempre teremos aplicativos altruístas desenvolvidos para ela.

### Uma proposta de múltiplos sistemas operacionais

Quando a IBM definiu o Cell no nível de software, eles mencionaram que o Cell seria capaz de executar vários sistemas operacionais (SO) ao mesmo tempo, devido aos vários núcleos de execução do Cell [@cpu-mit]. Assim, a Sony levou essa ideia adiante e adicionou uma opção no XMB para instalar um **sistema operacional secundário** [@operating_system-open_platform]. Essa funcionalidade foi chamada de **OtherOS** e, em resumo, fornece um gerenciador de partição (o XMB apenas guia o usuário para redimensionar a partição do GameOS e alocar novo espaço para o segundo SO) e um botão para iniciar a partir do segundo SO (graças aos arquivos de inicialização do OtherOS já configurados na memória Flash). Então, o usuário só precisa preencher a nova partição com um SO. Consequentemente, várias distribuições Linux (por exemplo,  Ubuntu e Fedora) adicionaram o PS3 como outro possível alvo para instalação. Você pode considerar isso como um sucessor espiritual do Linux para [PS2](playstation-2#an-unusual-kind-of-game).

![Red Ribbon GNU/Linux é uma distribuição exclusiva para o PS3/Cell e compilada para arquitetura PPC64 [@operating_system-ribbon].](xmb/red_ribbon.png) {.open-float}

Graças ao OtherOS, usuários experientes tiveram a oportunidade de desenvolver aplicativos homebrew que rodavam no Cell sem restrições de licença, o que foi particularmente interessante para fins de pesquisa/científicos \[@games-cluster\] \[@operating_system-barcelona\], uma vez que este console tinha um preço mais acessível do que um mainframe. Para fins de multimídia, a unidade de Blu-ray e o leitor Multi-card também eram acessíveis a partir do OtherOS.

Por outro lado, embora os privilégios do OtherOS possam superar os do GameOS (no nível de Kernel), **eles não ultrapassam o hypervisor**, que ainda reside na memória. Portanto, qualquer acesso de hardware do OtherOS ainda depende da vontade do hypervisor da Sony, e acontece que este último **bloqueia o acesso aos *buffers* de comando do RSX** (impedindo o uso das unidades de sombreamento, entre outros componentes usados para acelerar as operações gráficas). Consequentemente, as distribuições Linux recorrem à renderização de software (todos os gráficos são desenhados pelo Cell) e, em seguida, o *buffer* de imagem é transmitido para o RSX para exibição. Embora seja decepcionante que o OtherOS não possa aproveitar todas as capacidades do console, isso provavelmente foi feito para reduzir as superfícies de ataque. Ironicamente, o uso do Cell pelo OtherOS é semelhante ao que a IBM/Toshiba/Sony podem ter originalmente previsto para o PS3!

`r close_float_group(with_markdown = TRUE)`

Compartilhando o mesmo destino do aplicativo Folding&#64;home, o OtherOS foi eventualmente removido em uma atualização subsequente, mas devido a causas diferentes (principalmente relacionadas à segurança). Pouco depois, o OtherOS foi restaurado de forma _não oficial_ graças a *exploits* de software e de engenharia reversa. No momento, o OtherOS está disponível se o usuário instalar um _firmware personalizado_. Eu explico isso mais detalhadamente na seção "Antipirataria e homebrew".

No momento em que este texto foi escrito, o desenvolvedor René Rebe está implementando *drivers* xf86 próprios que aproveitam a aceleração fornecida pelo RSX e seus 256 MB de memória [@operating_system-rsxx11]. Seu trabalho é combinado com outros desenvolvimentos que removeram as restrições impostas pelo hipervisor (inicialmente graças à descoberta de *exploits* de software e posteriormente com o uso de um _firmware personalizado_, este último é explicado mais detalhadamente na seção "Antipirataria e homebrew") O Sr. Rebe publica seu progresso em seu canal no YouTube e conta com doações voluntárias para continuar seu trabalho [@operating_system-rsxfund].

### Atualização

Para a parte final desta longa seção, falaremos sobre as capacidades de atualização do GameOS.

Em resumo, assim como o [PSP](playstation-portable#updatability), a Sony distribuiu arquivos `PS3UPDAT.PUP` que empacotam todos os novos binários do sistema operacional. Devido ao sistema de segurança do console, apenas os arquivos que não estão protegidos com chaves exclusivas do console e que são armazenados em memória regraváveis (Flash, disco rígido, eMMC) são atualizáveis, o restante deve permanecer como está.

Os arquivos PUP foram distribuídos através do site oficial da Sony, do assistente de atualização da XMB ou encontrados nos conteúdos de um disco de jogo (todos os jogos incorporam um arquivo PUP, refletindo a versão SDK que ele foi desenvolvido). Como os modelos com memória Flash NAND contêm apenas 256 MB de espaço e armazenam todo o sistema operacional, a Sony nunca lançou arquivos de atualização com tamanhos maiores que 256 MB.

## Jogos

Esta seção abrange os tópicos relacionados ao desenvolvimento de jogos, distribuição e serviços.

### Ecossistema de desenvolvimento

Como o console mescla tecnologias de várias empresas, incluindo produtos já comercializados em outros mercados (ou seja, a linha de GPU GeForce7 da Nvidia para PCs), os desenvolvedores foram inundados com muitas ferramentas diferentes para desenvolver o seu software. Note que isso não implica que o desenvolvimento fosse fácil, mas é algo a ser apreciado em comparação com os [dias que se usava apenas linguagem de montagem (*assembly*)](master-system#games).

Para programar o Cell, a IBM e a Sony distribuíram conjuntos de desenvolvimentos separados, sendo que os da IBM tinham como alvo ambientes não restritivos como o Linux (e o OtherOS), enquanto as ferramentas da Sony visavam explicitamente o GameOS do PS3 como o único ambiente de execução.

Disto isto, a IBM distribuiu gratuitamente o pacote **IBM Cell SDK** [@games-cell_programming]. Ele incluiu a **cadeia de ferramentas GCC** modificada para gerar binários para a PPU e SPU, permitindo o desenvolvimento em C, C++, Fortran e *assembly*. Ele também era multiplataforma, o que permitia compilar códigos de outros equipamentos (como do PC x86). O SDK também incluiu bibliotecas de baixo nível para facilitar operações matemáticas SIMD e gerenciamento da SPU-PPU. Por fim, incluiu um *fork* do IDE **Eclipse**.

Para facilitar a complexidade do desenvolvimento do Cell, a IBM também desenvolveu outro compilador, que não durou muito, chamado **XLCL** que compilava códigos em **OpenCL** (uma variante do C/C++ para computação paralela) para a PPU e SPU. No entanto, isso foi apenas distribuído através do canal Alphawork da IBM, o que significa que permaneceu experimental.

E a Sony? Bem, similar ao seu [PSP SDK](playstation-portable#development-ecosystem), ela distribuiu kits de desenvolvimento de hardware (muitas versões com diferentes tamanhos e melhorias) e um pacote de software composto de compiladores, bibliotecas e depuradores que usavam o Visual Studio 2008 (e posteriormente o 2010) como IDE [@games-napier]. Como ela apenas dava suporte ao PS3, o seu SDK incluía as mesmas cadeias de ferramentas GCC, mas complementadas com toneladas de bibliotecas para ajudar em tarefas gráficas, áudio e E/S. No caso dos gráficos/RSX, a Sony forneceu o **GCM** para construir comandos brutos e o **psGL**, construído sobre o GCM, para fornecer uma API OpenGL ES. Para escrever *shaders*, a Nvidia forneceu o **Cg**, um compilador de *shaders* que analisa uma linguagem semelhante ao HLSL (a linguagem de *shader* definida pela Microsoft).

#### Desenvolvimento livre de licenças

Com o advento do Homebrew nativo (executando no GameOS, não no OtherOS), novos SDKs de código aberto foram criados para contornar a dependência das bibliotecas protegidas por direitos autorais da Sony e, desta forma, prevenindo de litígios de direitos autorais. Um exemplo é o **PSL1GHT**, um SDK usado em conjunto com **ps3toolchain** [@games-ps3toolchain], para fornecer uma suíte completa de desenvolvimento pronta para o desenvolvimento legal de Homebrew (embora isso exija um console modificado/hackeado com verificações de assinatura desabilitadas).

Em 2018, eu criei minha própria suíte baseada no ps3toolchain, mas distribuída na forma de um contêiner Docker [@games-docker], para que os desenvolvedores não precisassem compilar o ps3toolchain e, em vez disso, pudesse baixar minha configuração pré-compilada (economizando muitas horas de tempo de compilação). O contêiner também incluía muitas ferramentas como o *shader* Cg da Nvidia para mitigar problemas de dependência que encontrei enquanto experimentava projetos baseados no PSL1GHT. No final, foi um experimento divertido que me ajudou a aprender mais sobre o ambiente de desenvolvimento.

#### Desenvolvimento terceirizado

Vale destacar que durante esse período, um modelo de negócio peculiar voltado para empresas se popularizou: as **Game Engines**. Em vez de gastar tempo e dinheiro desenvolvendo um jogo do zero, por que não comprar a base de código de outras empresas e construir o jogo em cima disso? Foi isso que empresas de jogos como a Epic Games imaginaram [@games-unreal]. Além de vender títulos populares como o _Unreal Tournament 3_, o estúdio licenciou uma versão simplificada (sem os recursos de seus jogos) para outros desenvolvedores. Isso foi empacotado e chamado de **"Unreal Engine 3"**. Em resumo, os *game engines* cuidam de todas as áreas fundamentais (física, iluminação, etc.), para que os desenvolvedores só precisem adicionar seu conteúdo personalizado (scripts, texturas, modelos, sons, etc.).

O licenciamento de *game engine* não é um modelo de negócio novo, mas devido ao ambiente desafiador do PS3, eles se tornaram outra opção atraente para o desenvolvimento.

### Mídia de armazenamento

Agora que terminamos de falar sobre o desenvolvimento de jogos, é hora de falar sobre a distribuição. Aqui, descrevo os mecanismos oficiais de distribuição disponíveis para jogos de PS3.

#### Discos Blu-ray {.tabs.active}

![Exemplo de jogo de varejo.](games/retail.jpg) {.tab-float}

Nova geração = nova mídia. Conforme as vantagens do DVD começaram a diminuir e suas limitações, expressas pela indústria de jogos (limite de espaço) e pela indústria de filmes (formato 480i) [@games-dvd], se tornaram evidentes, era apenas uma questão de tempo até que a Sony apresentasse [outro padrão](playstation-portable#tab-5-1-umd-discs) para substituir seus novos aparelhos. Para seu novo console, o **disco Blu-ray** foi escolhido.

O Blu-ray, como o próprio nome indica, é um novo formato de disco óptico que proporciona maior densidade de armazenamento graças ao uso de diodos de luz azul [@games-brit], ao invés dos diodos de luz vermelha usados em DVDs. Como a luz azul tem um comprimento de onda menor do que a luz vermelha, mais informações ([pits e lands](playstation#anti-piracy--region-lock)) podem ser comprimidas no mesmo espaço [@games-blue_laser]. Como resultado, os discos Blu-ray oferecem uma capacidade surpreendentemente grande (entre 25 GB a 50 GB!) usando o mesmo disco de plástico com as mesmas dimensões do CD/DVD.

O formato de dados do Blu-ray atende a muitas necessidades de diferentes indústrias: filmes de alta definição, gerenciamento de direitos digitais (DRM), bloqueio regional, um novo sistema de arquivos e até mesmo um ambiente de tempo de execução para programas Java [@games-bdj]. No caso da indústria de videogames, jogos de varejos para o PlayStation 3 foram distribuídos em discos Blu-ray de **25 GB ou 50 GB** com **proteção contra cópia**. Estes são lidos por uma unidade de velocidade 2x que alcança até 8,58 MB/s [@io-bd_rev], embora o laser do PS3 também possa ler DVDs (a velocidade 8x) e CDs (a velocidade de 24x) para reproduzir jogos e filmes antigos.

Enquanto os títulos de lançamento são executados a partir do disco, jogos posteriores copiavam parte de seu conteúdo para o disco rígido para aumentar a velocidade de leitura. No entanto, o disco do jogo é sempre necessário para iniciar o jogo.

#### Loja online {.tab}

![O aplicativo PS Store na XMB.](xmb/store.jpg){.tabs-nested .active .tab-float title="Aplicativo"}

![O catálogo de jogos mostrado ao abrir a loja.](store/main.jpg){.tab-nested title="Tela inicial"}

![A interface de pesquisa (que contorna o teclado nativo do XMB, por sinal).](store/search.jpg){.tab-nested title="Pesquisa"}

![Exemplo de entrada de jogo (acompanhado de sugestões que só funcionam em outros consoles...).](store/game.jpg){.tabs-nested-last title="Jogo"}

Na mesma época do lançamento do console, a Sony lançou seu canal de distribuição proprietário chamado **PlayStation Store**, permitindo que os estúdios de jogos vendessem seus jogos digitalmente e que os usuários comprassem novos conteúdos sem sair do sofá. Esses jogos não requerem nenhum meio físico para funcionar (além do espaço no disco rígido), mas a propriedade do produto digital está vinculada a uma conta online, algo que se tornou uma preocupação para os usuários em março de 2021, quando a Sony anunciou (e depois adiou) o fechamento desta loja.

Dentro de sua loja digital, a Sony também aproveitou a oportunidade para vender versões digitalizadas de jogos do PS1, PS2 e PSP chamados de **PlayStation Classics**. Eles também são baixados e instalados da mesma maneira, mas usam emuladores incorporados para funcionar. Na verdade, os clássicos do PS2 invocam o mesmo software de emulação não acelerada, independentemente se os modelos do PS3 contém ou não o chipset do PS2 [@io-ps2emu]! Creio que este foi o capítulo final para a emulação baseada em hardware no PS3.

Nos bastidores, a PS Store não passa de um site acessível somente através do aplicativo PS Store no XMB. Ao longo de sua vida útil, sua interface do usuário foi reformulada algumas vezes para refletir o que suponho ser _uma demanda global por interfaces de usuário mais sofisticadas_.

### Serviços de rede {.tabs-close}

Além da loja online, muitas outras soluções online foram adicionadas à plataforma, incluindo a estreante **PlayStation Network**, um serviço online gratuito competindo diretamente com o [Xbox Live](xbox#network-service), o serviço online pago da Microsoft.

A PlaySation Network permitiu que os usuários criassem uma conta pessoal e atribuíssem um avatar, e então usassem essa nova identidade digital para jogos multijogadores, mensagens e outras interações sociais. Os usuários também podem ganhar **troféus** se completarem um determinado evento em um jogo, e esses então aparecem no seu perfil online (como uma espécie de medalha de honra) para intimidar rivais e ganhar o respeito de amigos, acredito eu.

(ref:trophiestitle) Troféus

![Os jogos oferecem um catálogo de objetivos para desafiar seus usuários. A intenção é proporcionar aos jogadores uma sensação de orgulho e conquista.](xmb/trophies.jpg){.tabs-nested .active title="Troféus"}

![Lista de amigos. (Os nomes estão censurados por razões óbvias).](xmb/friends.jpg){.tab-nested title="Amigos"}

![Depois de jogar online por um tempo, pessoas aleatórias começaram a me enviar mensagens.](xmb/mail.jpg){.tabs-nested-last title="Mensagem"}

Por último, mas não menos importante, assim como tendo um sistema operacional atualizável, os jogos também são atualizados. Portanto, ao iniciar um jogo, o XMB pode lhe sugerir baixar atualizações do jogo (na forma de "pacotes") que corrigem erros e/ou adicionam novos conteúdos. As atualizações são instaladas no disco rígido e funcionam de forma como um _sistema de arquivos em camadas_.

## Antipirataria e Homebrew

Tudo o que você acabou de ler precisa ser protegido de alguma forma contra o acesso "não autorizado". A seguir, você terá uma visão geral de como a Sony lidou com isso.

### Resumo da base de segurança

Várias partes do console já fornecem recursos de segurança que não exigem nenhuma implementação manual no software:

- O **SysCon**, o chip proprietário obscuro (brevemente mencionado no processo de inicialização), controla as linhas de alimentação do Cell, do RSX e do Southbridge. Sua memória EEPROM contém registros que são lidos pelos módulos do sistema operacional para determinar quais funções estão habilitadas e quais não estão [@anti_piracy-qaflag].
  - Embora eu use a palavra "obscuro", o SysCon é apenas um microcontrolador, seja um [ARM7TDMI](game-boy-advance#cpu)-S comercial (isso mesmo, o PS3 compartilha parte de seu DNA com o [Game Boy Advance](game-boy-advance) e até mesmo com as últimas revisões do PS2), aprimorado com suporte ao [MagicGate](playstation-2#interactive-accessories), ou uma variante personalizada NEC 78KOR [@anti_piracy-syscon]. O firmware interno do SysCon é o mais intrigante.
  - O SysCon e o Cell se comunicam entre si usando uma interface serial (SPI) que se conecta ao componente **TEST** do Cell [@anti_piracy-cell_test]. O TEST fornece muitas funções de depuração no Cell, embora o SysCon só se conecte à porta de "lógica pervasiva", permitindo que o SysCon gerencie áreas como energia ou térmica [@anti_piracy-pervasive].
- O Cell abriga uma memória **ROM oculta** que armazena rotinas de inicialização não criptografadas sem se preocupar com bisbilhoteiros.
- Os **modos de privilégio** do Cell e o **modo isolado** do SPE impedem que os programas acessem recursos não autorizados.
- O Southbridge criptografa perfeitamente o conteúdo do disco rígido usando AES.
- O subsistema Blu-ray é outra fortaleza protegida, e seu conteúdo de disco é criptografado usando uma chave encontrada na área de "marcas de ROM" do disco (inacessível por leitores convencionais) [@anti_piracy-bdauth].

Além disso, a Sony implementou as seguintes proteções no software:

- Uma complexa **cadeia de confiança** começa com o boot ROM não criptografado do Cell e termina com uma interface gráfica de usuário (XMB) que carrega apenas binários criptografados (pela Sony) sob um Kernel e Hypervisor.
  - A cadeia de confiança implementa múltiplos **algoritmos de criptografia**, incluindo algoritmos assimétricos como RSA e ECDSA e sistemas simétricos como AES, combinados com HMAC e SHA-1 (para confirmar a integridade dos dados).
- Algumas chaves de criptografia são **produzidas durante a fabricação** do console, o que significa que, se o hackers encontrarem e vazarem essas chaves, elas não funcionarão em outros consoles. No entanto, isso tem um custo para a Sony, pois ela não pode corrigir o software criptografado com essas chaves depois que o console sai da fábrica.
  - Essas chaves especiais são usadas para o `bootldr` e o `metldr` (os estágios iniciais de inicialização).
- Os jogos devem chamar o Kernel para acessar o hardware, que por sua vez solicita o acesso ao **Hypervisor**. Essa "cebola de abstração" impede que as [vulnerabilidades do jogo](playstation-portable#tab-8-1-early-blunders) aumentem os privilégios, _em teoria_.

### Derrota

Sabendo o quanto esse console é poderoso, você esperaria que os hackers se contentassem apenas com os recursos limitados do OtherOS? Creio que nem a Sony esperava, a empresa tentou proteger fortemente algumas áreas, enquanto deixou outras meio desprotegida, como os hackers demonstrariam mais tarde.

Vamos dar uma olhada em como algumas de suas fortalezas foram quebradas por hackers independentes de vários lugares do mundo. Tenha em mente que a comunidade de hacking do PS3 era muito ativa, com muitas ferramentas e documentação produzida a cada ano. Então, vou me concentrar em alguns marcos que abrem caminho para um influxo de conteúdo e desenvolvimento caseiro. Você pode encontrar mais informações em PS3History [@anti_piracy-ps3history].

#### Contornando o Hypervisor {.tabs.active}

Em 2010, após três anos de relativa tranquilidade na cena *hacking*, a comunidade teve uma reviravolta para melhor. George Hotz, um hacker conhecido por desbloquear o primeiro modelo de iPhone (conhecido como "2G") para que pudesse funcionar com qualquer operadora (originalmente apenas na Cingular/AT&T), conseguiu ler e escrever áreas protegidas da memória sem ser interrompido pelo Hypervisor. Ele publicou seu *exploit* juntamente com um breve resumo em seu blog [@anti_piracy-lv1xploit].

O *exploit* requer duas coisas: uma instalação do Linux rodando sob OtherOS (para execução de código arbitrário, porém limitado); e um *glitcher* externo conectado ao barramento XDR (interfaceando a memória RAM principal). Para resumir, o Hypervisor usa uma tabela *hash*, armazenada na memória RAM principal, para catalogar os endereços de memória com seus níveis de privilégio. Assim, evitando que os programas de usuário acessem os espaços de memória protegidos. O ataque funciona quebrando a integridade dessa tabela para poder escrever sobre ela e, em seguida, usar esse privilégio para modificar as entradas para conceder ao programa atual acesso a todos os cantos da memória.

Em resumo, Hotz descobriu que, sob o Linux/OtherOS, programas podem solicitar ao Hypervisor vários blocos de memória apontando para o mesmo endereço físico, mas se o programa desalocá-los enquanto houver interferência externa no barramento XDR (devido a um *glitcher* enviando pulsos elétricos), o processo de desalocação termina pela metade [@anti_piracy-lv1xploit_summary]. Como consequência, a tabela *hash* do Hypervisor (residente na memória RAM) ainda contém uma entrada dos endereços alocados, mas ao mesmo tempo, ele pensa que esse espaço foi liberado. O *exploit* de Hotz prossegue solicitando mais blocos para que o Hypervisor estenda sua tabela com mais entradas, e o processo continua até que uma entrada da tabela *hash* se sobreponha à localização de memória do bloco que deveria desalocar. Como a tabela _hash_ manteve a entrada antiga concedendo ao usuário acesso a esse endereço, o Hypervisor acaba dando ao usuário acesso para modificar uma entrada da tabela _hash_! Assim, o *exploit* altera a entrada para estender o acesso a todo o espaço de memória.

Embora esse *exploit* tenha exigido um ambiente de execução do Linux em OtherOS, ele foi um grande passo em direção a projetos de engenharia reversa e de pesquisa, já que os hackers agora podiam investigar áreas críticas do sistema que eram originalmente inacessíveis. Vale mencionar que, durante o mesmo período, a Sony lançou a atualização de software `3.21` removendo o OtherOS. Poderia-se pensar que isso desencorajaria os hackers de continuar seu trabalho, mas na verdade isso apenas os motivou a acelerá-lo ainda mais.

#### PS Jailbreak {.tab}

Mais tarde em 2010, um grupo identificado como "PS Jailbreak" anunciou (e posteriormente lançou) uma solução única para executar *Homebrew* diretamente do *shell* nativo do console (XMB, sob o GameOS) sem a necessidade de manipular o hardware do console. Tudo isso para o desespero da Sony, que em logo seguiu o caminho legal para bloquear as vendas desse produto.

O "PS Jailbreak" consistia em um dispositivo USB que era conectado à porta USB frontal antes de ligar o console. O usuário teria que pressionar o botão *power* e logo depois o botão *eject*. Se a instruções fossem executadas com sucesso, os usuários veriam a interface normal do XMB, mas com a adição de uma opção **"*Install* PKG"** e alguns aplicativos *Homebrew* feitos para copiar jogos Blu-ray para o disco rígido e, em seguida, carregá-los.

Por baixo dos panos, esse dispositivo realiza uma enorme quantidade de trabalho, que pode ser dividido em dois grupos [@anti_piracy-psjail]:

1. O ***exploit* USB**: uma vez que o console é ligado, o dispositivo engana o sistema fazendo-o pensar que está conectado a um hub USB de seis portas e, em seguida, realiza uma sequência complexa de comandos USB até chegar a uma **sobrecarga de *heap*** que escalar para o acesso ao Kernel do PS3 (nível 2), em seguida, procede à execução de um *payload*.
2. O ***Payload***: este é outro pacote complexo que altera o *shell* original para habilitar funções ocultas disponíveis apenas em unidades de depuração (ou seja, a entrada "*install* PKG"), desativar a verificação de assinatura (para carregar qualquer módulo/pacote arbitrário); e redirecionar os comandos do Blu-ray para o disco rígido (para carregar jogos do disco rígido). O fato deste programa poder alterar tanto do nível do Kernel nos faz perguntar para que serve o Hypervisor?
    - Para complementar, mais tarde fui informado pelo M4j0r: "Curiosamente, não explora nem mesmo o código da Sony; esta parte do lv2 foi escrita pela Logitech e os desenvolvedores desse *exploit* podem ter tido acesso ao código-fonte (devido ao *hack* de 2008)." [@operating_system-psxplace].

Posteriormente, este produto sofreu engenharia reversa por outras comunidades e, rapidamente, apareceram clones de código aberto (ou seja, PS Groove), no qual muitas restrições foram removidas (por exemplo, os usuários agora podiam desconectar o dispositivo depois que o *exploit* terminava). Algumas variantes foram até implantadas em uma calculadora da Texas Instruments [@anti_piracy-tijb]. De qualquer forma, a Sony agiu rapidamente com a atualização de software `3.42` para remover esta mina de ouro [@anti_piracy-ps3history], embora a porta para *Homebrew* já estivesse aberta.

#### Menções honrosas {.tab}

Antes de finalmente falar sobre o grande vencedor da cena *homebrew* do PS3, deixe-me descrever-lhe alguns métodos que foram desenvolvidos na mesma época:

- O **USB Jig**: outro pendrive, dessa vez programado para enganar o console para entrar no **Modo de Serviço de Fábrica**, que é destinado apenas para manutenção do console por pessoal autorizado. O programa embutido no Jig replica o que a Sony fornece para seus engenheiros. A principal vantagem do modo de serviço é permitir um [*downgrade*](playstation-portable#tab-8-2-downgrading) do console para uma versão compatível com o PS Jailbreak. O *payload* também estava disponível na forma de um aplicativo *Homebrew* para o PSP [@anti_piracy-pspjig]. A Sony respondeu corrigindo o modo de serviço para tornar mais difícil restaurá-lo ao modo "normal" ou alterar o firmware dele, desencorajando os usuários a recorrerem ao modo de serviço.
- O **Emulador de Disco Óptico** (ODE, de *Optical Disc Emulator*): uma série de produtos de hardware que diferentes empresas (Cobra, E3, etc.) lançaram. Em vez de mexer no firmware do console, eles mexeram na interface SATA/PATA do Blu-ray. Os ODEs são placas que ficam entre a placa-mãe e o drive de Blu-ray, agindo como intermediários que enganam o console fazendo-o pensar que contém um jogo em disco válido, mas está, na verdade, carregando uma imagem de disco de um dispositivo USB externo. Entretanto, esses dispositivos eram bastante caros quando foram lançados. A história *hacking* do PS3 inclui longos períodos de "inviolabilidade" em que não havia nenhum *exploit* de software disponível para novos consoles, então os ODEs foram capazes de preencher essa lacuna.
- O **Downgrader**: à medida que a Sony continuava a mitigar as vulnerabilidades com mais atualizações de software, os usuários não tinham outra opção senão fazer *downgrade* para um firmware explorável. Assim, empresas como a E3 lançaram equipamentos especializados que podiam sobrescrever o sistema do console "à moda antiga". Ou seja, diretamente programando os chips NAND ou NOR. Por razões óbvias, esse método exigia mais habilidade e paciência em comparação com os métodos baseados em dispositivos USB.
- **Vazamentos isolados**: este é para fins de pesquisa, ao contrário de uma "funcionalidade" que o usuário verá (mas ainda é imperativo para futuros desenvolvimentos). De qualquer maneira, os dados de revogação (usados para listar certificados comprometidos) são analisados pelo `lv2ldr`, até aí tudo bem? Bem, foi descoberto que esse processo continha muitas vulnerabilidades. Em primeiro lugar e por alguma razão inexplicável, **os dados de revogação podem ser escritos no espaço do usuário**. Em segundo lugar, **o analisador não realiza verificação de limites** nos dados coletados ([_lá vamos nós novamente_](playstation-2#tab-8-4-ps1-overflow)). Portanto, os hackers conseguiram criar dados personalizados de revogação que poderiam produzir um estouro de *buffer* e, em última instância, permitir que eles executassem **código arbitrário no modo isolado da SPU**. Isso permitiu que eles acessassem dados confidenciais (ou seja, chaves) que presumivelmente estavam protegidos do restante do sistema [@operating_system-psxplace].

#### A quebra da criptografia {.tab}

Assim como na saga do PSP, os *exploits* iniciais exigiam muito esforço e poderiam ser facilmente corrigidos pela Sony, resultando em um desvantajoso jogo de gato e rato. No entanto, como aconteceu com o PSP, era apenas uma questão de tempo antes que uma descoberta quebrasse a segurança fundamental deste sistema: sua cadeia de confiança.

Em 2011, George Hotz (junto com a equipe failOverflow) publicou outro avanço: **as chaves privadas de criptografias que a Sony usa para assinar binários executados por `metldr`**. Os binários carregados durante essa fase de inicialização são assinados com uma chave ECDSA. Sendo um sistema de criptografia assimétrica, isso significa que qualquer pessoa que possua a chave privada (Sony, e agora _outros_) pode criptografar e assinar binários, consequentemente, eles parecerão "autênticos" aos olhos do `metldr`. Como o `metldr` é o terceiro estágio de inicialização antes de carregar o `lv1` (o Hypervisor), isso significa que os hackers poderão personalizar ou desenvolver seu próprio Hypervisor, Kernel e qualquer coisa abaixo dele. Além disso, todos os PlayStation 3 no mercado também pensarão que os binários personalizados são autênticos. No geral, um *exploit* do [tipo Pandora](playstation-portable#tab-8-3-pandora) completamente feito por software.

A descoberta dessa chave, que deveria ser computacionalmente inviável, é possível graças ao que é considerado um "erro" na implementação da Sony do algoritmo ECDSA. Para resumir, a fórmula matemática usada para o ECDSA usa um valor aleatório que a Sony nunca mudou em todos os arquivos de atualização que distribuiu [@anti_piracy-ecsda], tornando aquele número uma constante, e deixando mais fácil descobrir as outras variáveis. Foi o que acabou acontecendo.

Os efeitos desta descoberta estão descritos nos próximos parágrafos.

### A era dos Custom Firmware (CFW) {.tabs-close}

A quebra de segurança do metldr significou que agora todos podiam criar sistemas "oficiais" para o PS3, o que resultou no surgimento de diversas versões do GameOS que diferentes comunidades produziram com diversas personalizações. Esses sistemas eram modificações dos arquivos oficiais do firmware da Sony (que a Sony distribuía como atualizações) e reempacotados usando as suas chaves vazadas. Assim, eles podiam ser instalados em qualquer PS3. O resultado foi chamado de **Custom Firmware** (CFW) e tornou o método definitivo para hackear o console, até que a Sony respondeu com medidas mais rigorosas.

![Minha instalação do CFW com "menu VSH" aberto. Essa versão (chamada "Rebug") também me permitiu transformar meu console em uma estação de depuração (observe o endereço IP no canto inferior direito, você precisa digitá-lo no depurador para se conectar a um processo em execução) e mexer em meus próprios homebrews.](cfw/vshmenu.jpg)

Enquanto isso, muitos CFW apareceram na rede com muitos nomes (por exemplo, "Rebug", "Ferrox", etc.) e eles continham personalizações como [@anti_piracy-patches]:

- Desabilitar as verificações de assinatura em qualquer módulo instalado ou a ser instalado.
- Permitir a leitura e gravação (o clássico _peek e poke_) em qualquer endereço de memória, seja usando o Hypervisor (nível 1) ou o Kernel (nível 2).
- Ativar as funções de depuração ocultas para instalar módulos empacotados como arquivos "pkg". Estes arquivos não precisavam ser assinados com as chaves da Sony para funcionar dentro de um ambiente CFW.
- Permitir montar imagens de disco como um disco Blu-ray virtual.
- Restaurar o OtherOS e até mesmo aprimorá-lo removendo as restrições impostas pelo Hypervisor. O resultado foi chamado de OtherOS++.
- Escrever sobre o banco de dados do EEPROM do Syscon para permitir a instalação de qualquer versão de sistema escolhida. Isso também é conhecido como _QA Toggling_.
- Alterar o estilo do XMB (por exemplo, removendo o aviso de epilepsia, permitindo tirar capturas de tela em jogo, etc.).

Há também o meu favorito: trazer as funções de depuração de um kit de teste, permitindo que qualquer console de varejo se torne uma estação de depuração. Isso era feito instalando uma CFW com capacidades de depuração ou uma CFW que pudesse converter o console de varejo (chamdo de "CEX") em um modelo de depuração (chamado de "DEX") alterando dados específicos do console na memória Flash.

#### A resposta rígida da Sony

<!-- TODO: metldr.2 is not mentioned. -->

Semelhante aos eventos que ocorreram após a criação de [CFWs](playstation-portable#tab-8-4-cfw-and-beyond) para o PSP, a Sony retaliou com duas atualizações de segurança:

Do **lado do software**, a Sony lançou duas atualizações de sistema que aprimoraram o sistema de segurança:

- Com o sistema `3.56`, os binários eram assinados com novas chaves de criptografia resistentes à descoberta anterior do ECSDA [@anti_piracy-keys], portanto, os criadores de CFW não podiam personalizar os novos binários (já que não possuíam as chaves privadas para recriptografá-los). Além disso, uma nova revisão do aplicativo "atualizador de sistema" (*system updater*) também foi lançada, que impôs novos certificados nos arquivos de atualização do sistema (`PS3UPDAT.PUP`). Isto significava que mesmo que os hackers consiguissem empacotar uma nova CFW, apenas os consoles com a versão do sistema `3.55` ou anterior poderiam instalá-lo [@anti_piracy-spkg].
- Posteriormente, a atualização de sistema `3.60` reformulou o processo de inicialização, anulou o `metldr` e promoveu o `lv0` para assumir o *bootstrap* dos carregadores (`lv1ldr`, `lv2ldr`, `appldr` e `isoldr`). Isso significava que os hackers não podiam modificar os novos arquivos do sistema sem primeiro quebrar o `lv0` (ou seja, encontrando sua chave privada).
  - Isso acabou acontecendo no final de 2012, quando uma equipe chamada "Os três mosqueteiros" publicou as chaves do `lv0` [@anti_piracy-lv0leak], o que abriu caminho para novas CFW feitas a partir de versões do sistema mais recentes do que `3.55`. No entanto, devido às mudanças mencionadas no atualizador, apenas usuários com a versão do sistema `3.55` ou anterior (incluindo qualquer CFW com verificações de assinatura desativadas) poderiam instalá-lo.

Do **lado do hardware**, não apenas os modelos posteriores do PS3 (CECH-25xxx, CECH-3xxx e CECH-4xxx) vieram pré-instalados com uma versão de sistema mais recente que a versão `3.55`, mas também continham uma versão diferente do `bootldr`/`lv0ldr` (chamdo `lv0ldr.1`) que não apenas descriptografa e carrega o `lv0`, mas também buscava um novo arquivo de sistema chamado `lv0.2`. Este último contém metadados sobre `lv0` [@anti_piracy-lv0ldr] para garantir que o `lv0` não tenha sido adulterado. O `lv0.2` é assinado com uma nova chave criptográfica (também é imune à descoberta ECDSA anterior), impedindo assim que hackers pudessem assumir o controle da cadeia de inicialização.

Até hoje, **esses modelos não conseguem executar um CFW**, por isso foram apelidados de _invioláveis_. No entanto, eles podem executar um "Firmware Híbrido" (HFW) e falaremos mais sobre isso posteriormente.

Com o tempo, o número de consoles compatíveis com o CFW foi diminuindo, tornando os PS3s que não foram atualizados além de 3.55 uma espécie de _relíquia_. Enquanto isso, houve um aumento na demanda por alternativas, como *downgraders* (para reverter para a versão do sistema `3.55` em modelos antigos) e ODEs (para jogar jogos pirateados em modelos novos).

#### O renascimento do Homebrew

Após um longo período de espera para os usuários que perderam a oportunidade de instalar um CFW, no final de 2017, uma equipe de hackers lançou o **PS3Xploit**, uma coleção de *exploit* e utilitários [@anti_piracy-ps3xploit] que trouxeram de volta a capacidade de instalar o CFW em modelos antigos sem precisar de um *downgrader* caro (e habilidade para operá-lo).

O *payload* principal do PS3Xploit replica o trabalho de um *downgrader* de hardware (patcheando os arquivos do CoreOS) inteiramente por software, e funciona da seguinte maneira:

1. O ponto de partida é o navegador da internet do XMB, construído em cima do WebKit. O PS3Xploit usa JavaScript para conseguir **executar um código arbitrário no espaço do usuário do sistema** (e fora do ambiente JavaScript). Para iniciar a execução, os usuários só precisam abrir o navegador web nativo do XMB, inserir um URL apontando para o host do PS3Xploit e deixá-lo fazer seu trabalho.
2. Acontece que o Kernel fornece chamadas de sistema que podem ser usadas para **sobrescrever os arquivos do sistema na memória Flash**. Além disso, o Visual Shell (XMB) e seus *plugins* armazenam rotinas na memória que usam essas chamadas.
3. O PS3Xploit não pode acionar essas chamadas do sistema diretamente devido à proteção do Hypervisor, que impede que o *exploit* carregue um novo código no espaço do usuário. No entanto, ele pode encontrar uma maneira de sobrescrever a memória Flash "pegando emprestado" as rotinas do Visual Shell.
4. Consequentemente, o PS3Xploit procede com a modificação da pilha de execução do Webkit para redirecionar a execução para as rotinas do Visual Shell. Esse tipo de técnica (corromper a pilha para desviar a execução para outro código residente na memória) é chamado de **Programação Orientada a Retorno** (ROP, do inglês *Return Oriented Programming*) e é muito popular na área de segurança da informação. Uma maneira de mitigar isso é implementando a **randomização da organização do espaço de endereço** (ASLR, do inglês *Address space layout randomisation*), o que torna difícil adivinhar a localização das rotinas (chamadas de _gadgets_), mas, como você pode imaginar, o Hypervisor da Sony não tem ASLR.
5. Finalmente, essas chamadas de sistema são acionadas com os parâmetros do PS3Xploit e, assim, substituem os arquivos do CoreOS (a primeira parte do sistema operacional, armazenada na memória Flash) por arquivos patcheados [@anti_piracy-flash_writer].
6. Agora, o console consegue instalar atualizações de software não oficiais, uma oportunidade que permite o usuário _explorar_ a instalação de um firmware personalizado. No entanto, ainda não é possível fazer *downgrade* da versão do sistema, porém dado que um CFW atualizado é instalado, o usuário pode instalar mais utilitários para realizar o *downgrade* da versão do sistema e instalar um CFW mais bem equipado, se desejar.

Como você pode ver, esse _presente dos céus_ trouxe os firmwares personalizados de volta à atenção e tornou os *downgraders* de hardware e os ODEs obsoletos. Por outro lado, para as unidades que não podiam instalar um CFW de qualquer maneira (os _não hackeáveis_), a equipe posteriormente ofereceu o **PS3Hen**, um pacote de *exploits* diferente que se concentrou em habilitar um subconjunto de funções do CFW (incluíndo a capacidade de executar homebrew). Ele se instala como uma entrada no XMB e o usuário deve executá-lo toda vez que ligar o console para reativar a execução de aplicativos homebew.

#### A resposta parcial da Sony

Por sorte, a Sony tomou apenas medidas pequenas para bloquear o PS3Xploit (talvez porque esse acontecimento tenha ocorrido anos após o lançamento do sucessor do PS3, o PlayStation 4). Eles lançaram algumas atualizações de sistema que não corrigiram essa cadeia de *exploits*, mas removeram a rotina usada no Webkit para inicializar a cadeia. Em resposta, os hackers publicaram atualizações de software ligeiramente modificadas que restauraram tal entrada (e de alguma forma, eles não precisavam ser re-assinadas) [@anti_piracy-hfw]. Essas atualizações personalizadas foram chamadas de **Hybrid firmware** (HFW) e, no momento em que este texto foi escrito, são a opção de fato usada para habilitar homebrew em sistemas não-hackeáveis.

E aqui termina a saga anti-pirataria/homebrew. Em minha humilde opinião, não acredito que a Sony esteja interessada em colocar mais esforço no console. Portanto, não esperaria mais jogos de gato e rato neste campo.

## Isso é tudo pessoal

![Dois PS3, um jogando o jogo que-não-deve-ser-nomeado.<br>Além do jogo apresentar um <em>gameplay</em> com bugs e uma história incomum, eu gosto bastante do gênero.](myps3s.jpg)

Chegamos ao fim!

Para ser justo, eu originalmente planejei isso como um projeto de dois meses, mas acabou se tornando um projeto de verão completo (e você viu o porquê). De qualquer forma, espero que isso tenha lhe ajudado a expandir seu conhecimento sobre o sistema e permitido-lhe entender as razões por trás do progresso tecnológico durante essa época. Dessa forma, você pode pensar além do que é constantemente reciclado pelas _massas_.

Se você estiver se perguntando, para este texto, eu usei três modelos de PS3:

- Um modelo CECH-3001 _não-hackeável_ do tempo da minha adolescência (por algum motivo, a caixa diz ser um modelo CECH-25XX!). Foi recentemente tirado do sótão para experimentar o PS3Hen.
- Um modelo CECH-2100 que comprei depois que o PS3Xploit saiu. Com ele, finalmente consegui instalar homebrews.
- Um modelo CECHA (lançado apenas no Japão) que adquiri em agosto de 2021 para reunir material para este artigo (principalmente fotografia e informações sobre compatibilidade com o PS2). Foi bastante caro, mas felizmente as doações dos apoiadores me ajudaram a compensar os custos.

Embora eu tenha repetido várias vezes como o Cell era uma tecnologia revolucionária, você pode ter notado que eu não mencionei o quão pouco confiáveis os modelos iniciais acabaram sendo. Foi a primeira vez que ouvi falar sobre um console falhar simplesmente jogando um jogo por um tempo. De fato, essas coisas consumiam eletricidade como dinossauros e aqueciam como um forno (um forno de _plástico_). Felizmente, eu tinha um modelo slim (deveria ter sido chamado de "o que funcionava")... Que era uma época apressada, né?

De qualquer forma, em relação ao que está na minha agenda, tirarei um tempo para mim antes de começar o próximo artigo, para poder trabalhar em outras áreas e melhorar o site, além de resolver algumas coisas pessoais.

Até a próxima!  
Rodrigo
