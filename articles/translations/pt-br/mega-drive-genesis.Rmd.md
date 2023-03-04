---
short_title: Arquitetura do Mega Drive/Genesis
title: Arquitetura do Mega Drive/Genesis
name: Mega Drive/Genesis
subtitle: Novas técnicas de composição
date: 2019-05-18
release_date: 1988-10-29
generation: 4
cover: megadrive
top_tabs:
  Models:
    - 
      title: "Japonês"
      caption: "O Mega Drive.<br>Lançado em 29/10/1988 no Japão."
      file: japonês
    - 
      title: "Americano"
      caption: "O Genesis.<br>Lançado em 14/08/1989 nos EUA."
      file: american
    - 
      title: "Europeu"
      caption: "O Mega Drive.<br>Lançado em 09/1990 na Europa."
      file: european
      active: true
  Motherboard:
    caption: "Mostrando a revisão 'VA0' japonesa.<br> Perceba a placa-filha incomum sobre o VDP usada para corrigir problemas pós-fabricação (corrigidos em revisões posteriores)."
    extension: "jpg"
    bib_source: photography-okqubit
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/mega-drive-genesis/
---

## Uma breve introdução

A Sega (e seus anúncios de TV) quer que você saiba: os desenvolvedores não conseguem criar jogos decentes a menos que o console forneça gráficos mais rápidos e sons mais ricos.

Seu novo sistema inclui muitos componentes *já conhecidos* e prontos para serem programados. Isso significa que, teoricamente, os desenvolvedores só precisariam aprender sobre a nova GPU da Sega... certo?

```{r results="asis"}
supporting_imagery()
```

## CPU

Este console possui dois processadores de propósito geral.

Primeiramente, temos um **Motorola 68000** rodando a **~7.6MHz**, um processador popular já presente em muitos computadores da época, como o Amiga, o Macintosh (original), o Atari ST... Curiosamente, cada um desses computadores sucedeu um "antecessor 6502", e apesar do [Master System](master-system) (antecessor do Mega Drive) não ter usado uma CPU 6502, o [NES](nes) usou (e de certa forma, o objetivo da Sega era conquistar os consumidores da Nintendo). De modo geral, é possível ver uma correlação entre a evolução dos computadores e a tecnologia de consoles de videogame.

Voltando ao assunto, o processador 68k tem o papel de CPU "principal" e vai ser usado para a lógica do jogo, gerenciação da E/S e cálculo dos gráficos. Ele tem as seguintes capacidades [@cpu-user]:

- **68000 ISA**: Um novo conjunto de instruções com muitos recursos, incluindo um conjunto de códigos de operação de multiplicação e divisão. Algumas instruções possuem 8 bits de comprimento (byte), outras possuem 16 bits (word) e o restante possui 32 bits (long-word).
- **Registradores de 32 bits**: este é um avanço significativo, considerando que tanto o processador 6502 quanto o Z80 possuem apenas registradores de 8 bits.
- **Unidade de lógica e aritmética (ALU) de 16 bits**: Significa que é necessário ciclos extras de CPU para calcular operações aritméticas em números de 32 bits, mas funciona bem em números de 16 bits e de 8 bits.
- **Barramento de dados externo de 16 bits**: Observe que, embora este processador tenha alguma capacidade de 32 bits, ele não foi projetado para ser uma máquina de 32 bits completa. A largura desse barramento sugere um melhor desempenho ao mover dados de 16 bits.
  - É curioso notar que a Motorola lançou uma CPU de 32 bits completa, o **68020**, quatro anos antes do lançamento deste console. Imagino que os custos teriam disparado se a Sega tivesse escolhido chip mais novo.
- **Barramento de endereços de 24 bits**: Significa que **até 16 MB de memória podem ser acessados**. Os endereços de memória ainda são codificados com valores de 32 bits internamente na CPU (o byte mais significativo é simplesmente descartado). Dito isso, o barramento está fisicamente conectado a [@cpu-memorymap]:
  - 64 KB de RAM de propósito geral.
  - A ROM do cartucho (até 4 MB).
  - Dois controles.
  - Os registradores, as portas e o DMA do *Video Display Processor* (VDP).
  - Os registradores da placa-mãe (identificam o console).
  - As portas de expansão (usadas para acessórios "futuros").
  - A memória RAM da segunda CPU, intermediada pelo *árbitro de barramento*.

(Se você estiver se perguntando o motivo de utilizar endereços de 24 bits com uma CPU capaz de lidar com palavras de 32 bits, eu duvido que nos anos 80 muitos desenvolvedores estivessem pedindo para gerenciar 4 GB de RAM, além do fato que adicionar linhas não utilizadas é caro em termos de desempenho e de dinheiro).

Em segundo lugar, há outro processador instalado neste console, um **Zilog Z80** com velocidade de **~3,5 MHz**. Este é o mesmo processador encontrado no [Master System](master-system#cpu) e é principalmente utilizado para **controle de som**. Ele possui [@cpu-z80manual]:

- **Z80 ISA**: uma extensão da ISA Intel 8080, que manipula palavras de **8 bits**.
- **Registradores de 8 bits** e **barramento de dados de 8 bits**: _nenhuma surpresa aqui_.
- **ALU de 4 bits**: isso pode ser um pouco surpreendente, mas ele consegue lidar com operações de 8 bits sem problemas, mas leva o dobro de ciclos para serem processadas.
  - Note como o 6502 roda a ~2 MHz em [alguns sistemas](nes#cpu), enquanto este quase chega a 4 MHz: A velocidade do clock por si só não torna o Z80 mais rápido, mas ajuda a equilibrar a falta de transistores em algumas áreas do chip.
- **Barramento de endereços de 16 bits** com o seguinte mapa de endereços [@cpu-z80map]:
  - 8 KB de RAM.
  - Dois chips de som.
  - A memória RAM do 68000 (novamente, manipulada pelo árbitro do barramento).

Por fim, **ambas as CPUs rodam em paralelo**.

### Memória disponível

A CPU principal possui **64 KB** de memória RAM dedicada para armazenamento de dados de propósito geral e o processador Z80 possui **8 KB** de RAM para operações relacionadas ao som.

#### Intercomunicação

A Sega escolheu dois processadores independentes que **não têm conhecimento um do outro**, então como os jogos podem gerenciar ambos ao mesmo tempo? Bem, o programa principal é executado no 68000 e esse processador pode escrever na RAM do Z80. Então, o 68000 pode enviar um programa para a RAM do Z80 e instruí-lo a carregar esse programa (enviando um sinal de reinicialização para o Z80) [@cpu-using_z80]. Uma vez que o Z80 está sob controle, ele pode ser usado para gerenciar o sub-sistema de som e manipular a memória usando o método descrito anteriormente, tudo isso enquanto o 68000 está cuidando de outras operações.

![Arquitetura de memória do Mega Drive/Genesis.](memory.png) {.no-borders}

Devido uma CPU ter que entrar no barramento da outra e ambas não poderem usá-lo simultaneamente, há um componente extra chamado **árbitro de barramento** que deve ser ativado para parar cada um dos processadores, então a memória pode ser escrita sem problemas.

É importante destacar que esse design pode afetar negativamente o desempenho se não for gerenciado adequadamente, portanto, os jogos precisam ter um cuidado especial com o árbitro de barramento e cuidar para não parar uma CPU por mais tempo do que o necessário.

## Gráficos

A resposta é _Blast Processing!_ O que mais você precisa saber?

Ok, se você quer saber a _verdadeira_ resposta: os gráficos são processados pelo 68000 e renderizados em um chip proprietário chamado ***Video Display Processor** *(VDP), o qual envia o quadro resultante (na forma de linhas de varredura) para exibição.

O VDP opera a** ~13 MHz** e suporta múltiplos modos de resolução dependendo da região: até **320x224 pixels** em NTSC e até **320x240 pixels** em PAL.

### Por trás das múltiplas resoluções de exibição

Tecnicamente falando, o VDP pode acomodar tanto 40 ou 32 colunas de *[tiles](master-system#tiles)* (ladrilhos) por linha de varredura, e o número de linhas de *tiles* depende da região (28 no NTSC ou 30 no PAL) [@graphics-keil]. No entanto, a maioria dos jogos PAL não se preocupa com os *tiles* extras permitidos em sistemas PAL (provavelmente porque precisam manter a consistência entre as duas regiões, e o NTSC é o denominador comum), então eles instruem o VDP a renderizar apenas 28 linhas (como fariam em sistemas NTSC). Assim, o VDP não tem opção se não preencher a área não utilizada com uma cor de fundo (também usada durante o *overscan*).

É possível ver quais jogos em PAL renderizam no modo NTSC verificando o `Mode Set Register #2` em um emulador com recursos de depuração (por exemplo, o Exodus). Se o quarto bit da direita for `0`, então o VDP está sendo executado no modo NTSC [@graphics-resolution].

![Para fornecer um modo dois jogadores de forma rápida no Sonic 2 (1992), o jogo ativa o 'modo entrelaçado' para renderizar um nível do modo um jogador usando blocos de 8x16 píxeis no lugar (juntamente com outras mudanças).](twopsonic/sonic2.png) {.side-by-side.toleft.pixel}

![Em contrapartida, o modo dois jogadores mais sofisticado de Sonic 3 (1994) usa de <em>tiles</em> de 8x8 píxeis que são diferentes dos encontrados nos níveis do modo de um jogador.](twopsonic/sonic3.png) {.toright.pixel}

Além disso, há um parâmetro adicional que pode ser definido no VDP para empilhar dois *tiles* e formar **mapas de 8x16**, tratando-os como um único *tile*. Assim, dobrando a resolução vertical. No entanto, isso acaba reduzindo pela metade a taxa de atualização, pois os quadros agora são renderizados com entrelaçamento (um quadro renderiza as linhas de varredura pares, o próximo, as linhas ímpares, e assim por diante), sendo mais limitado em termo de funcionalidade. O modo dois jogadores do Sonic 2 é uma boa representação deste modo [@graphics-sonicmultip].

Por fim, vale ressaltar que o VDP cuida automaticamente da adição de preenchimento para a área de *overscan*, para que os jogos não precisem se preocupar com quais áreas são seguras para desenhar gráficos (como acontecia com as ["zonas perigosas" do NES](nes#constructing-the-frame))

### Organizando o conteúdo

Em termos de processamento gráfico, este chip tem dois modos de operação:

- **Mode IV**: modo de legado que se comporta como seu [antecessor](master-system#graphics).
  - Isso não significa que o console rodará jogos do Master System imediatamente, pois é necessário um acessório adicional (o *Power Base Converter*) para encaixar os cartuchos do console anterior. O conversor também instruirá o chip de E/S a colocar o Z80 no controle.
- **Mode V**: modo nativo de operação, iremos focar neste.

E quanto aos Modos do 0 ao III? Bem, esses pertencem ao ainda mais antigo *SG-1000* e o Mega Drive não tem suporte para eles.

Uma nota interessante: fui informado posteriormente por um ex-desenvolvedor deste sistema que a estrutura de comando do Modo V (usado para controlar o VDP no jogo) herdou o projeto do TMS9918 (o famoso chip de vídeo usado no SG-1000) [@graphics-clarification]. Isso facilitou para desenvolvedores *third-party* usarem o Modo V sem depender da documentação oficial (e dos subsequentes custos de licenciamento).

#### Memória disponível

![Arquitetura de memória do VDP.](VDP_architecture.png) {.no-borders}

O conteúdo gráfico é distribuído em três regiões de memória [@graphics-keil]:

- 64 KB de **VRAM** (Vídeo RAM): contém a maioria dos dados gráficos.
- 80 B de **VSRAM** (*Vertical Scroll* RAM): o VDP suporta rolagem vertical e horizontal. Os valores de *V-scroll* são armazenados neste espaço separado, por alguma razão.
- 128 B de **CRAM** (*Colour* RAM): armazena quatro entradas de paleta com 16 cores cada (incluindo `transparent`), o sistema oferece 512 cores para escolher. Além disso, efeitos de *luz* e *sombra* podem ser aplicados a cada paleta para alcançar uma ampla gama de cores por paleta.

### Construindo o frame

A próxima seção explica como o VDP desenha cada quadro, para fins de demonstração, o jogo *Sonic The Hedgehog* é usado como exemplo. Antes de começar, eu recomendo verificar o _modus operandi_ de seu [antecessor](master-system#graphics), já que muitos conceitos serão revisados aqui.

#### *Tiles* {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Múltiplos <em>tiles</em> comprimidos juntos. Para fins de demonstração, uma paleta padronizada é usada.](vdp_sonic/tiles.png){.active title="Tudo"}

![Um único <em>tile</em> de 8x8 píxeis.](vdp_sonic/tiles_single.png){title="Único"}

Alguns *tiles* encontrados na VRAM.

:::

Assim como a PPU da Nintendo, o VDP é um mecanismo baseado em ***tiles***, e como tal, usa *tiles* (*bitmaps* básicos de 8x8) para compor planos gráficos. No caso do VDP, cada *tile* é codificado com um vetor de 4 bytes de comprimento, onde cada entrada de 4 bits corresponde a um píxel e seu valor corresponde a uma entrada de cor (apontando para uma paleta de cores).

Os cartuchos de jogo armazenam os *tiles* em sua **ROM** (encontrada no próprio cartucho), porém eles precisam ser copiados para a memória **VRAM** para que o VDP possa lê-los [@graphics-ports]. Tradicionalmente, isso só era possível durante intervalos de tempo específicos e era manuseado pela CPU, mas felizmente, o console possuí circuitos especiais para transferir essa tarefa para o VDP (entraremos em detalhes mais adiante).

*Tiles* são usados para construir um total de **quatro planos** que, uma vez mesclados, formam o quadro visto na tela. Além disso, para os *tiles* dos planos que se sobrepõem, o VDP decidirá qual tile será visível com base no tipo de plano e no **valor de prioridade** do *tile*.

#### Plano de fundo {.tab}

![Mapa de plano de fundo alocado.](vdp_sonic/layer2.png){.tabs-nested .active .tab-float .pixel title="Completo"}

![Mapa de plano de fundo alocado com área selecionada marcada.](vdp_sonic/layer2_selected.png){.tabs-nested-last .pixel title="Selecionado"}

O plano de fundo, também conhecido como **Plano B**, é um *tilemap* (conjunto de *tiles*) rolável contendo ***tiles* estáticos** [@graphics-macdonald].

Esse plano pode ter seis dimensões diferentes: 256x256, 256x512, 256x1024, 512x256, 512x512, 1024x256. Os programadores podem selecionar a dimensão que melhor se adapta ao tipo de rolagem necessária.

Cada *tile* pode ser **virado horizontal e/ou verticalmente** e ter uma **prioridade** definida.

No exemplo mostrado, você nota que a área selecionada para exibição não é um quadrado... *Não precisa ser!*. O VDP permite configurar valores de rolagem horizontal para todo o quadro, cada linha de varredura individual ou a cada oito píxeis. Isso significa que os desenvolvedores podem moldar a área selecionada como um losango e alterar seus ângulos à medida que o jogador se move para simular **efeitos de perspectiva**. Truques como este não corrompem o plano, o VDP busca cada linha horizontal (selecionada) e constrói um quadro regular a partir dela.

#### Primeiro plano {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Primeiro plano alocado.](vdp_sonic/layer1.png){.active title="Completo"}

![Primeiro plano alocado com área selecionada marcada.](vdp_sonic/layer1_selected.png){title="Selecionado"}

Exemplo de primeiro plano, o plano janela não é usado.

:::

O primeiro plano, também conhecido como **Plano A** [@graphics-macdonald], possui as mesmas propriedades do plano de fundo, exceto que ele tem uma **prioridade mais alta**, consequentemente, os *tiles* renderizados aqui estarão inerentemente em cima do plano de fundo.

Além disso, este plano permite se dividir para formar um novo *sub-plano*: o **plano janela**. A única diferença é que este plano não se move durante a rolagem.

Em resumo, é possível ver que os novos valores de prioridade e planos separados permitem aos game designers produzirem novos tipos de cenários. Além disso, usando diferentes velocidades de rolagem em cada plano, um **efeito de paralaxe** pode ser realizado.

#### *Sprites* {.tab}

![Camada de <em>sprite</em> alocada.](vdp_sonic/sprite.png){.tabs-nested .active .tab-float .pixel title="Completo"}

![Camada de <em>sprite</em> alocada com área selecionada marcada.](vdp_sonic/sprite_selected.png){.tabs-nested-last .pixel title="Selecionado"}

Neste plano, tiles são tratados como ***sprites***. Eles são posicionados em um mapa de **512x512 píxeis** e apenas uma parte dele (a resolução de saída do VDP) é selecionada para exibição. Isto é conveniente para ocultar *sprites* indesejados ou preparar outros que serão mostrados no futuro. O VDP também fornece uma antiga função de [detecção de colisão](master-system#tab-4-1-collision-detection).

Os *sprites* são formados pela combinação de até 4x4 *tiles* (mapa de 32x32 píxeis) e selecionando até 16 cores (incluindo *transparência*). Se for necessário um *sprite* maior, vários *sprites* podem ser combinados em um.

Pode haver no máximo 20 *sprites* por linha de varredura e 80 por tela (mais do que isso corromperá toda a camada).

A região na memória VRAM onde os *sprites* são definidos é chamada de ***Sprite Attribute Table*** (Tabela de Atributos de *Sprites*) [@graphics-macdonald] e cada entrada contém o índice do *tile*, as coordenadas da camada (x e y), o valor `link` (gerencia quais *sprites* são desenhados primeiro), a prioridade (o *sprite* com a maior prioridade é o que será exibido em cima dos demais caso haja sobreposição), o índice da paleta de cores e o *flip* vertical e horizontal.

#### Resultado {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Quadro resultante.](vdp_sonic/result.png){.active title="Quadro"}

![Quadro transmitido para TV (formato NTSC), o VDP cobre automaticamente o quadro com a área de <em>overscan</em> que a maioria das TV CRT escondem.](vdp_sonic/overscan.png){title="Com *overscan*"}

Tan-ran!

:::

Enquanto o quadro está sendo desenhado, o sistema chamará sequencialmente diferentes rotinas de interrupção, dependendo de onde o feixe do CRT está apontando. Como você provavelmente já viu em consoles anteriores, isso permite que a CPU trabalhe no próximo quadro (ou altere o quadro atual).

Convencionalmente, existem dois tipos de interrupções, chamadas: **H-Blank** (a cada linha horizontal) e **V-Blank** (a cada quadro).

O H-Blank é chamado várias vezes, mas é limitado a executar rotinas curtas. Além disso, somente memórias CRAM e VSRAM são acessíveis, então os jogos só podem atualizar suas paletas de cores ou rolar verticalmente suas camadas.

O V-Blank permite rotinas mais longas com a desvantagem de ser chamado apenas 50 ou 60 vezes por segundo (dependendo da região do console), mas consegue acessar toda a memória.

Observe que a área de *overscan* no exemplo exibe alguns pontos coloridos aleatórios no canto inferior direito. Isso é popularmente conhecido como ***CRAM dots*** e acontece porque a CPU está atualizando as paletas na memória CRAM ao mesmo tempo que o VDP está enviando as linhas restantes do feixe (no exemplo, isso acontece durante o *overscan*). Este conflito faz com que o VDP use qualquer valor que a CPU esteja escrevendo naquele momento (ao contrário do local necessário no CRAM), então a imagem acaba corrompida. No entanto, como neste caso o jogo só atualiza a memória CRAM durante o *overscan*, essa anomalia passa despercebida nos televisores CRTs tradicionais. Outros jogos tentam atualizar as paletas no meio do quadro para obter mais cores, com o custo de ter que equilibrar a aparição de *CRAM dots*.

### Uma unidade de transferência dedicada {.tabs-close}

Até agora, discutimos o que a CPU pode fazer para atualizar quadros, mas e quanto ao VDP? Ele fornece algo mais especializado? Bem, sim, este chip apresenta um **Direct Memory Access** (Acesso direto à memoria, ou DMA) que permite mover dados entre locais de memória a uma **taxa mais rápida** e **sem a intervenção da CPU**.

O DMA pode ser ativado durante o H-Blank, o V-Blank ou o estado ativo (fora de qualquer interrupção), e pode ser usado para escrever nas memórias VRAM, CRAM e/ou VSRAM [@graphics-macdonald]. Além disso, durante as transferências da memória RAM da CPU usando o DMA, o barramento da CPU é bloqueado, então é essencial um bom planejamento para alcançar um desempenho adequado.

O uso excepcional desses recursos pode fornecer gráficos de alta resolução, rolagem de paralaxe fluida e altas taxas de quadros. Ademais, seu jogo também pode ser apresentado em comerciais de TV com muitos *slogans* de *Blast Processing!* por toda parte.

### Saída de vídeo

A primeira revisão deste console (comumente referida como *Model 1*) possui a mesma porta de saída de vídeo do [Master System](master-system#video-output). Os subsequentes Modelo 2 e Modelo 3 mudaram para uma porta mini-DIN. É importante lembrar que isso se refere às revisões Norte-Americanas, já que os modelos da TecToy usam denominações diferentes.

## Áudio

As capacidades de áudio deste console são um pouco incomuns, para dizer o mínimo. Por um lado, o Mega Drive oferece tecnologia de áudio existente da geração anterior, por outro lado, adiciona uma nova (porém complicada) técnica de síntese por cima da existente. Assim, de certa forma, você obtém os recursos de ambas as gerações.

Dito isto, o Mega Drive possui dois chips de som: um **Yamaha YM2612** e um **Texas Instruments SN76489**.

### Funcionalidade

Agora, veremos o que cada chip oferece, já que um é *muito* diferente do outro.

#### Yamaha YM2612 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Canais FM.](fm_single){.active video="true" title="FM"}

![Canal PCM.](pcm_single){video="true" title="Amostra PCM"}

Sonic The Hedgehog (1991).

:::

O **Yamaha YM2612** é um **sintetizador FM** [@audio-ymwiki] que opera na velocidade do 68000 e fornece **seis canais FM**, onde um deles pode reproduzir **amostras PCM** (com resolução de 8 bits e taxa de amostragem de 32 kHz).

A síntese de modulação de frequência, ou FM, é uma das muitas técnicas profissionais usadas para sintetizar som, que aumentou significativamente em popularidade durante os anos 80 e deu origem a sons completamente novos (muitos dos quais você pode encontrar ouvindo os hits pop daquela era).

De maneira *muito simplificada*, o algoritmo de FM pega uma única onda (**portadora**) e altera sua frequência usando outra onda (**moduladora**). O resultado é uma nova onda com um som diferente. A combinação portador-modulador é chamada **operador** e vários operadores podem ser encadeados para formar a onda final. Diferentes combinações geram diferentes resultados. Este chip permite 4 operadores por canal.

Comparado aos sintetizadores PSG tradicionais, isso representou uma melhoria drástica: você não estava mais preso a formas de onda *pré-definidas*.

#### Texas Instruments SN76489 {.tab}

![Canais PSG.<br>Sonic The Hedgehog (1991).](psg_single){.tab-float video="true"}

O **Texas Instruments SN76489** é um **chip PSG** que pode produzir três ondas de pulso e uma de ruído.

Este é na verdade o [chip de som](master-system#audio) original do Master System, e está embutido no VDP. Ele opera na mesma velocidade do Z80.

Observe que o canal *Pulse 3* permanece inutilizado. Isso ocorre porque o jogo usa um modo para o canal de ruído que reserva o terceiro canal de pulso para modulação [@audio-sonic], uma função presente também no Master System.

#### Mixado {.tab}

![Todos os canais de áudio.<br>Sonic The Hedgehog (1991).](complete){.tab-float video="true"}

Ambos os chips podem produzir som simultaneamente, e então um componente extra chamado '*audio mixer*' é responsável por receber ambos os sinais e mixá-los.

Por fim, o sinal analógico resultante é enviado pela saída de áudio.

### O condutor {.tabs-close}

Em teoria, o mapa de memória do Z80 sugere que o Z80 é o **único processador** capaz de comandar esses dois chips (o que pode ser um alívio para o 68000, já que este último já está sobrecarregado com outras tarefas). Na prática, no entanto, o Z80 pode ser desativado para que o 68000 tenha acesso ao YM2612 (mas não ao SN76489) [@audio-68kym]. Portanto, para fins de simplicidade, neste artigo vamos supor que as tarefas de áudio são designadas ao Z80.

Prosseguindo, o Z80 é um processador independente por si só, então ele precisa de seu próprio programa (armazenado nos 8 KB de memória RAM disponível) para poder interpretar os dados de música recebidos do 68000 e manipular efetivamente os dois chips de som de acordo. Este programa é chamado de **sequenciador** ou **driver**.

### Desvendando o *sampling*

Alguns compositores musicais podem decidir focar no canal PCM para reproduzir sons mais fiéis, e para isso, os jogos precisam sequenciar e transmitir continuamente sua música usando o restante da memória RAM disponível. A principal restrição é que para preencher essa memória, o barramento principal deve ser **bloqueado** primeiro (então nenhum comando ou amostra pode ser enviado para os chips de áudio durante esse período). Caso contrário, anomalias sonoras podem aparecer (como som mudo, notas congeladas, taxas de amostragem baixas, etc).

Por essa razão, decidi dedicar esta seção a alguns exemplos de jogos que conseguiram superar com sucesso a restrição mencionada anteriormente. Em vez de apenas usar kits de bateria comuns, alguns jogos encontraram maneiras incríveis de transmitir amostras mais ricas para esse único canal PCM. Confira estes exemplos:

::: {.subfigures .tabs-nested .toleft}

![Canal PCM.](good_sampling/sonic_pcm){.active video="true" title="Amostra PCM"}

![Todos os canais de áudio.](good_sampling/sonic_complete){video="true" title="Completo"}

Sonic The Hedgehog 3 (1994).<br>Esta é uma das faixas que dizem ser de coautoria de Michael Jackson. De qualquer forma, a trilha sonora inteira em um geral tem uma batida distinta quando comparada com as anteriores.

:::

![Canal PCM (O único canal utilizado).<br>Toy Story (1995).<br> É sequenciado em tempo real com ajuda do 68000 [@audio-gamehut]. Uma tarefa muito intensa, o que significa que só pode ser tocado em pontos muito particulares do jogo (isto é, no menu principal).](good_sampling/randy_pcm){.tabs-nested .tabs-nested-last .active .toright video="true" title="Amostra PCM/Completo"}

Eu sei que eles estão muito aquém da qualidade de CD (16 bits a 44,1 kHz), mas tenha em mente que esses sons já foram considerados impossíveis de reproduzir neste console, e nem estou enfatizando o quanto isso representa de progresso em comparação com a geração anterior, então eles certamente merecem pelo menos algum mérito!

### Composição FM assistida

Se programar um sintetizador FM já era considerado complicado usando um painel do Yamaha DX7, imagine as dores de cabeça de compor música usando apenas a linguagem de assembly do 68k...

Felizmente, mais tarde a Sega distribuiu um software para MS-DOS chamado **GEMS**, que facilitava a composição (e depuração) da música do Mega Drive [@audio-gst]. Era uma ferramenta muito completa, que incluía muitos *patches* (operadores pré-configurados para escolher), o que também explicaria o motivo de alguns jogos terem sons *muito* semelhantes.

Além do mais, o subsistema de áudio permitia que os jogos designassem mais canais do que o permitido e atribuíssem **um valor de prioridade** para cada um deles. Então, quando o console tocava a música, ele distribuía dinamicamente as faixas contendo cada instrumento para os canais disponíveis com base nas prioridades. Além disso, canais com alta prioridade mas sem música poderiam ser automaticamente pulados.

Os canais também poderiam reagir a **lógica** ao implementar condicionais em seus dados, permitindo que a música "evoluísse" dependendo de como o jogador progredia no jogo.

### (Bônus) Som do Mega CD

Aqui está um fato interessante: o add-on Mega CD (Sega CD no Brasil) fornecia 2 canais extras para áudio de CD (entre outras coisas). Um de seus jogos mais famosos, o Sonic CD, tinha uma qualidade de música muito impressionante, mas como todos os jogos, precisava repetir sua música sem interrupção. O problema era que lacunas perceptíveis surgiam ao repetir a faixa de música em um leitor de CD de velocidade 1x, então o jogo incluiu faixas adicionais para preencher esses vazios, mas que eram executados a partir de outro chip (este PCM), enquanto o cabeçalho do CD voltava ao início da faixa.

Esses trechos adicionais só são encontrados nas primeiras versões beta do jogo, e não foram incluídos no lançamento original. O remake de 2011 finalmente os incluiu. Este é um dos níveis do jogo:

![Versão do Mega CD (1993).](sonic_cd_original){.toleft audio="true"}

![Versão remasterizada (2011).](sonic_cd_remaster){.toright audio="true"}

Percebeu a lacuna da versão do Mega CD?

## Jogos

Os jogos são codificados principalmente em **linguagem assembly do 68000** enquanto o driver de som é implementado com a **linguagem assembly do Z80**. Ambos os códigos residem na ROM do cartucho e podem ter até 4 MB sem a necessidade de um [mapeador](nes#going-beyond-existing-capabilities).

### Funções extras

Em termos de expansibilidade, esse projeto não foi tão modular quanto o [NES](nes) ou o [SNES](super-nintendo). Portanto, as expansões posteriores como o 32x (que incluía um novo chipset que assumia o controle do 68k) tiveram que buscar alternativas para se comunicar com o VDP (daí a necessidade do "Cabo Conector").

Apenas um chip personalizado foi produzido para cartuchos, o **Sega Virtua Processor** \[@games-virtua\] (uma reformulação do Samsung SSP1601, um DSP (processador de sinal digital) de 16 bits), que produzia polígonos subsequentemente codificados na forma de *tiles* (para que o VDP possa lê-los). De qualquer forma, apenas um jogo foi distribuído com ele, já que o SVP acabou sendo muito caro para produzir.

### Tentativas primitivas de rede

Antes que os serviços online se tornassem amplamente adotados (e padronizados), a Sega tentou a sorte com o **Sega Meganet**, um serviço de acesso discado para jogos. O Meganet exigia que os usuários comprassem um acessório separado chamado _Sega Mega Modem_, o conectassem na parte de trás do console (onde o conector DE-9 era encontrado) e, finalmente, o conectassem à linha telefônica. Os jogos tratariam a unidade de modem como outro controle, com a adição da comunicação com ele ser feita em serial \[@games-ioports\] (em vez da codificação paralela usada para os controles).

Seja como for, essa característica durou apenas alguns anos antes que a Sega removesse o conector DE-9 em revisões futuras e descontinuasse o serviço completamente.

## Anti-pirataria / Trava de região

Para bloquear jogos importados, a Sega mudou ligeiramente a forma da entrada do cartucho entre as regiões, embora mantivesse as pinagens. Os jogos também poderiam executar um 'bloqueio de região' verificando o valor do registrador `Version` (que produz o valor da região).

Havia duas maneiras fáceis de contornar isso. A primeira era comprando um desses conversores de cartucho de terceiros. A segunda era desmontar o console e fazer uma ponte nos pinos da placa-mãe que alteravam o valor do registrador `Version`.

No que se refere às medidas antipirataria via software, a verificação mais fácil era no tamanho da memória SRAM: os cartuchos pirateados tinham mais espaço do que o necessário para caber qualquer jogo, então os jogos verificavam o tamanho esperado na inicialização. Os programadores também podiam implementar verificações de *checksum* adicionais em pontos aleatórios do jogo no caso de hackers removerem as verificações iniciais da memória SRAM. A única maneira de derrotar isso era encontrar tediosamente todas as verificações e removê-las uma por uma...

## Isso é tudo pessoal
