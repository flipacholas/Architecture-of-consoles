---
short_title: Arquitetura do Super Nintendo
long_title: Arquitetura do Super Nintendo
name: Super Nintendo
subtitle: Hardware antigo com recursos incríveis
date: 2019-06-28
release_date: 1990-11-21
generation: 4
cover: snes
published: true
top_tabs:
  Models:
    - 
      title: "Internacional"
      file: internacional
      caption: "O Super Nintendo (na Europa) ou Super Famicom (no Japão).<br>Lançado em 21/11/1990 no Japão e 11/04/1992 na Europa."
      active: true
    - 
      title: "Americano"
      file: americano
      caption: "O Super Nintendo.<br>Lançado em 13/08/1991 na América."
  Motherboard:
    caption: "Revisão 'SNS-RGB-CPU-01'.<br>Revisões anteriores tinham o Subsistema de Som conectado como uma placa-filha, as mais recentes unificaram ambos as PPUs."
    bib_source: photography-yaca
    extension: "jpg"
  Diagram:
    caption: "Os barramentos \"A\" e \"B\" são barramentos de endereço, o barramento de dados segue as trilhas do barramento \"B\" e possui 8 bits de largura."
#Historical
aliases:
  - /projects/consoles/super-nintendo/
  - /writings/consoles/snes/
---

## Uma breve introdução

Parece que a Nintendo conseguiu trazer a próxima geração de gráficos e sons sem usar componentes caros disponíveis no mercado. Entretanto, há um porém: o novo console também foi **projetado com a expansibilidade em mente**. Num mundo onde as CPUs estão evoluindo mais rápido que a velocidade da luz, a Nintendo acabou dependendo dos cartuchos de jogos para fazer seu console brilhar.

```{r results="asis"}
supporting_imagery()
```

## CPU

A escolha do processador do Super Nintendo é peculiar. Ao contrário de [seu concorrente](mega-drive-genesis#cpu) que incluía um 68000 completo, o chip do SNES não é uma mudança radical de seu antecessor. Recapitulando, o NES usava uma CPU [6502 modificada](nes#cpu), um ingrediente admirado dos computadores do final dos anos 70 e início dos anos 80. Agora, para pavimentar o caminho para a nova década (os anos 90), a Nintendo optou por uma solução mais conservadora (e mais barata): o **65C816**, uma extensão de 16 bits do 6502.

### Modernizando o 6502

A CPU 65C816 tem sua origem da Western Design Center, especialmente de Bill Mensch, um ex-membro da equipe 6502 da MOS e, antes disso, parte da equipe 6800 da Motorola. Em 1978, um ano após deixar a MOS, Mensch fundou a Western Design Center (WDC), uma empresa de semicondutores que envia clones do MOS 6502 com melhorias atraentes (ou seja, projeto CMOS, instruções extras, correções de circuito, novos modos de endereçamento, etc.).

Um dia, a Apple procurou a WDC para projetar uma variante retrocompatível do 6502 que pudesse processar quantidades maiores de dados. Esse projeto tornou-se o 65C816 que, após alguns contratempos durante a fase de desenvolvimento, a Apple finalmente o usou em uma nova máquina chamada Apple IIGS.

Enquanto isso, a Nintendo estava desfrutando de um bom relacionamento com a Ricoh e seu conjunto de chips sob-medida para o NES. Não encontrei o documento exato que conecte a Ricoh com a WDC, mas o que posso confirmar é que, em algum momento, a WDC concordou em licenciar seus projetos do 65C816 com a Ricoh [@cpu-interview]. Consequentemente, a Ricoh adaptou o projeto aos novos requisitos do Super Nintendo e o nomeou de **Ricoh 5A22**, fornecido exclusivamente à Nintendo.

### A nova CPU

Como já apresentado, o processador principal deste console é o **Ricoh 5A22**, uma CPU de 16 bits.

Ao contrário do Apple IIGS, que desfrutava de retrocompatibilidade com o software Apple II, o Super Nintendo **não é compatível com os jogos do NES** (embora, ao olhar para a escolha do processador, haja uma leve possibilidade de que o SNES tenha sido originalmente planejado para ser compatível com jogos do NES, quem sabe).

Continuando, a CPU emprega uma **velocidade de *clock* variável** que chega até **3,58 MHz** durante as operações de registro e até **1,79 MHz** ao acessar os barramentos mais lentos (ou seja, a porta serial/controles).

Em resumo, o 5A22 possui as seguintes características:

- A **ISA 65816**: as instruções estreantes de 16 bits do 65C816. Elas são baeadas na ISA 6502, mas não implementa instruções não documentadas que alguns jogos do NES usaram [@cpu-unoffopcodes].
  - O tamanho das instruções pode variar entre 1 byte (8 bits) e 4 bytes (32 bits) dependendo de como os endereços de memória são referenciados (conhecidos como o "modo de endereçamento" utilizado) [@cpu-isaref].
  - O [modo BCD](nes#scrapped-functions) está **funcionando** novamente (suponho que seja consequência de uma licença _apropriada_).
- **10 diferentes modos de operação**: devido à combinação de um modo de retrocompatibilidade, o retorno do modo BCD (ausente no NES) e a capacidade de alternar entre grupos de registrados de 8 e de 16 bits [@cpu-opcodes], os desenvolvedores podem utilizar essa CPU com diferentes combinações de modos.
  - Ao contrário das [CPUs MIPS](nintendo-64#cpu) lançadas depois, não há um conjunto de instruções misto com instruções dedicadas para palavras de 8 bits e de 16 bits. Em vez disso, o mesmo conjunto de instruções será interpretado de forma diferente com base no modo ativado.
  - Por razões de compatibilidade, a CPU sempre começa no modo "emulação" (6502 puro) e cabe ao programador alternar para um modo "nativo" particular para habilitar a funcionalidade de 16 bits. Observação: é curioso como a Intel ainda aplica o [mesmo *modus operandi*](xbox#boot-process) em suas CPUs modernas do tipo x86.
- **Registradores de 16 bits**. Além disso, devido aos diferentes modos de operação, o acumulador (onde as operações aritméticas são realizadas) e o registrador de índice (usado para calcular endereços de memória) podem alternar entre modos de 8 e de 16 bits.
- **Barramento interno de dados de 16 bits** e **barramento externo de dados de 8 bits**: significa que há penalidade de desempenho ao mover valores maiores de 8 bits pela memória (a CPU gasta ciclos adicionais), especialmente considerando que a maioria das instruções tem 16 bits de tamanho. No entanto, o custo geral é relativo, já que a variante da Ricoh inclui unidades de DMA (explicado mais adiante) e o *clock* da CPU varia dependendo da operação.
- **Espaço de endereçamento de 24 bits**: permite que a CPU acesse **até 16 MB de memória**. Assim como o [Motorola 68000](mega-drive-genesis#cpu), com exceção que o 65C816 obtém seus endereços de 24 bits combinando registradores extras de 8 bits (`DBR` e `PBR`) com as linhas de endereçamento originais de 16 bits do 6502 [@cpu-chibi]. Em geral, essa metodologia é semelhante ao uso de [*mapper* interno](pc-engine#memory-access).

### Adições da Ricoh

O 65C816 foi uma CPU aceitável de propósitos gerais para sua década (os anos 80). No entanto, a Nintendo planejou seu console para durar durante toda a década de 90. Assim, a Ricoh teve que elevar o nível de seu jogo, se me permite o trocadilho.

Como consequência, a primeira atualização aparente foi a adição de **unidades de multiplicação e divisão de 16 bits**, que fornece à CPU a capacidade de realizar essas operações por hardware (o 65C816 não inclui nenhuma instrução dedicada para este tipo de operações aritméticas).

#### Acesso rápido à memória

A segunda adição foi **duas DMA exclusivas** (*Direct Memory Access*) que habilita a transferência de dados sem a intervenção da CPU (resultando velocidades maiores). Para que esse projeto funcione, as regiões de memória são referenciadas usando dois barramentos de endereços diferentes [@cpu-manual]:

- **Barramento A** de 24 bits de largura controlado pela CPU: conecta o cartucho, a CPU e a WRAM.
- **Barramento B** de 8 bits de largura controlado pela S-PPU: conecta o cartucho, a CPU, a WRAM, a S-PPU e a CPU de áudio.

Quando uma DMA é configurada, a *origem* deve vir de um barramento diferente do *destino*.

Além disso, as duas DMA não são idênticas e fornecem funcionalidades muito distintas [@graphics-piepgrass]:

- O **DMA de propósito geral** realiza transferências a qualquer momento. A CPU é interrompida até que a transferência seja concluída.
- O **DMA Horizontal** (HDMA) realiza uma pequena transferência após cada varredura horizontal (enquanto o feixe do CRT se prepara para desenhar a próxima linha). Isso evita interromper a CPU por longos intervalos, mas as transferências são limitadas a 4 bytes por linha de varredura.

Por fim, o sistema fornece **oito canais** para configurar as transferências do DMA, permitindo enviar até oito transferências independentes de uma só vez.

#### Falha de segmentação

Este console também apresenta uma "anomalia" chamada **Barramento Aberto**: se houver uma instrução tentando ler de um endereço não mapeado/inválido, o último valor lido é fornecido em seu lugar (a CPU armazena esse valor em um registrador chamado **Registro de Dados da Memória** ou `MDR`, do inglês *Memory Data Register*) e a execução continua em um estado imprevisível.

Para comparação, o 68000 usa uma tabela de vetores para lidar com exceções, então a execução será redirecionada sempre que uma falha for detectada.

## Gráficos

Depois de tudo o que foi dito até agora, deixe-me dizer que o subsistema gráfico deste console é um verdadeiro trabalho de engenharia. Com uma CPU tão limitada, alguém poderia imaginar que o SNES nunca poderia competir com seu concorrente, o Motorola 68000 com seus "32 bits". No entanto, os engenheiros da Nintendo e da Ricoh conseguiram encontrar truques inteligentes que tiram proveito da forma como os monitores CRT se comportam, expandindo assim as capacidades do console sem exigir componentes caros de última geração.

De qualquer forma, antes de aprofundarmos, recomendo fortemente que você leia primeiro [artigo sobre o NES](nes#graphics), pois ele apresenta conceitos úteis que serão revisitados aqui.

### Projeto

Como qualquer outro console de sua geração, o Super Nintendo desenha gráficos usando *tiles* 2D (8 x 8 píxeis). O NES originalmente realizava isso agrupando a "Unidade de Processamento de Imagem" (PPU, do inglês * Picture Processing Unit*) que projetava a imagem em sincronia com uma tela CRT. O Super Nintendo segue o mesmo caminho, mas agora implementa técnicas mais sofisticadas para obter resultados mais ricos.

#### O *chipset*

O Super Nintendo possui dois chips PPU diferentes que constituem o subsistema gráfico, os quais combinados são conhecidos como Super PPU ou "S-PPU". Ambos os pacotes PPU são projetados para servir diferentes funcionalidades [@cpu-manual]:

- **PPU 1**: renderiza gráficos (*tiles*) e aplica transformações neles (rotação e dimensionamento).
- **PPU 2**: fornece efeitos como *janela*, *mosaico* e *desvanecimentos* sobre os gráficos renderizados.

Essa separação, do ponto de vista da programação, é redundante, dado que ambos os chips são virtualmente tratados como um só.

#### Modalidades de exibição

O sistema produz uma resolução padrão de **256 x 224 píxeis** a **~60 Hz** (NTSC) [@graphics-guide]. A versão europeia PAL produz uma resolução de **256 x 240 píxeis** a **~50 Hz** em vez disso (para atender à especificação PAL). Seja como for, a maioria dos jogos não utiliza os píxeis extras e exibe faixas pretas em cima e embaixo da tela.

Aqui está a parte complicada: as TVs tradicionais têm uma proporção de aspecto de 4:3. No entanto, se fizermos as contas, a resolução de saída do Super Nintendo tem a **proporção de aspecto de 8:7**. Consequentemente, após projetarr a imagem na TV, ela parecerá **esticada horizontalmente**, como se fosse um quadro de **292 x 224 píxeis** (no caso da versão NTSC) [@graphics-aspect]. Em outras palavras, poderíamos dizer que os píxeis no Super Nintendo têm uma proporção de aspecto de 8:7 em vez de serem "perfeitamente quadrados".

::: {.subfigures .side-by-side .pixel}

![Quadro renderizado com a resolução de 256 x 224 píxeis. É assim o quadro que o console envia para a TV.](stretch/internal.png) {.toleft.snesratiosmaller}

![Quadro esticado como visto na TV (com uma resolução aparente de 292 x 224 píxeis).](stretch/external.png) {.toright.snesratiobigger}

Kirby's Dream Land 3 (1997).

:::

Alguns jogos, como "*The Legend of Zelda: A Link to the Past*", considera esse fator, de modo que as formas são explicitamente projetadas achatadas, o que parece correto após serem esticadas pela TV. Isso, no entanto, é um caso excepcional, já que a maioria dos jogos não toma medidas extras para lidar com esse fator.

### Organizando o conteúdo

![Arquitetura de memória da S-PPU.](SPPU_architecture.png) {.no-borders}

Por motivos de custos e desempenho, os dados gráficos são distribuídos em três regiões de memória:

- 64 KB de **VRAM** (*Video RAM*): armazena *tiles* e mapas (tabelas) usados para construir camadas de plano de fundo (*background*).
- 512 B de **CGRAM** (* Colour Graphics RAM*): contém 512 entradas de paleta de cores, cada entrada tem o tamanho de uma *palavra* (16 bits).
- 544 B de **OAM** (*Object Attribute Memory*): contém tabelas com referência de 128 *tiles* que serão usados como *sprites*, juntamente com seus atributos.

### Construindo o quadro (*frame*)

Vamos agora ver como um quadro é renderizado no console e posteriormente exibido na TV. Para fins de demonstração, *Super Mario World* é usado como exemplo.

#### *Tiles* {.tabs.active}

![Alguns <em>Tiles</em> de 16x16 encontrado na VRAM.](sppu_mario/tiles.png) {.tab-float.pixel}

Assim como seu antecessor, o S-PPU usa *tiles* para construir gráficos sofisticados. Embora haja melhorias significativas em comparação ao PPU original:

- Os **cartuchos de jogos não estão mais conectados a PPU**, portanto, os *tiles* terão que ser copiados para a VRAM primeiro (assim como no Mega Drive da Sega). É aí que a unidade DMA se torna muito útil.
- Os *Tiles* não estão mais restritos a sua dimensão (8x8 píxeis), a partir de agora eles também podem ter tamanho de **16x16 píxeis**.
- Quando os *tiles* são armazenados na memória, eles são comprimidos dependendo de quantas cores por píxel precisam ser usadas. A unidade de tamanho é **bpp** (bits por píxel). O valor mínimo é de **2 bpp** (onde cada píxel ocupa apenas dois bits na memória e tem apenas 4 cores disponíveis) enquanto o máximo é **8 bpp**, que codifica até 256 cores (com o custo de consumir um byte inteiro).

#### Plano de fundo {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Camada de plano de fundo 1 (BG1).](sppu_mario/background1_map.png){.active title="Camada 1"}

![Camada de plano de fundo 2 (BG2).](sppu_mario/background2_map.png){title="Camada 2"}

![Camada de plano de fundo 3 (BG3).](sppu_mario/background3_map.png){title="Camada 3"}

Mapas de plano de fundo na VRAM.

:::

::: {.subfigures .tabs-nested .tab-float .pixel}

![Camada de plano de fundo renderizada (BG1).](sppu_mario/background1.png){.active title="Camada 1"}

![Camada de plano de fundo renderizada (BG2).](sppu_mario/background2.png){title="Camada 2"}

![Camada de plano de fundo renderizada (BG3).](sppu_mario/background3.png){title="Camada 3"}

![Camadas de plano de fundo renderizadas combinadas.](sppu_mario/background_complete.png){title="Combinado"}

Camadas de plano de fundo renderizadas após aplicação de seleção e transparência.

:::

O Super Nintendo pode gerar até quatro planos de fundo diferentes. Usando *tiles* de 8x8 ou 16x16, os blocos são feitos de 32x32 píxeis (*tiles* de 2x2). Dito isso, o tamanho de cada camada de fundo pode ser de até 1024x1024 píxeis de tamanho (32x32 *tiles*). A região na VRAM onde essas camadas são configuradas é chamada de **Tilemap** e é estruturada como uma tabela (valores contínuos na memória).

Cada entrada no *Tilemap* contém os seguintes atributos:

- Valores de *flip* vertical e horizontal.
- Prioridade (`0` ou `1`).
- Referência da paleta na CRAM.
- Referência de *tile*.

Como sempre, esses planos são roláveis, embora o número de recursos disponíveis (cor, número de camadas, região de rolagem independente e tamanho de seleção) dependerá do **modo de plano de fundo** ativado no S-PPU, o que nos leva à próxima seção...

#### Modos {.tab}

A S-PPU fornece muitas operações para planos de fundo, mas elas não podem ser escolhidas arbitrariamente. Em vez disso, existem oito modos de plano de fundo para escolher, cada um fornecendo um conjunto diferente de recursos [@cpu-rgme]:

- **Modo 0**: 4 camadas com 4 cores cada.
  - A paleta de cores é particularmente sem graça, já que este modo é o único que permite o maior número de camadas.
- **Modo 1**: 2 camadas com 16 cores cada + 1 camada com 4 cores.
  - Uma camada pode ser dividida em primeiro plano e plano de fundo.
  - Este é o modo mais comum usado.
- **Modo 2**: 2 camadas com 16 cores cada.
  - Este modo tem um efeito extra: as camadas podem ter cada uma de suas colunas roladas independentemente (semelhante aos efeitos do [GameBoy](game-boy#graphics), mas rolados verticalmente).
- **Modo 3**: 1 camada de plano de fundo com 128 cores + 1 plano de fundo com 16 cores.
  - As cores podem ser definidas como valores RGB em vez de usar as referências da CGRAM.
- **Modo 4**: Modo 2 e 3 combinados (rolagem de coluna + mapeamento de cores RGB).
  - A primeira camada possui 128 cores e a segunda possui apenas 4.
- **Modo 5**: 1 camada com 16 cores + 1 camada com 4 cores.
  - A área selecionada terá uma resolução de **512 x 224 píxeis**, é espremido horizontalmente para caber na tela (o quadro de saída ainda é de 256 x 224 píxel!). Isso vem com o custo de renderizar *tiles* de 16x8 píxeis como um de 8x8, e <tiles> de 16x16 píxeis como um de 8x16.
  - Além disso, a resolução vertical também pode ser expandida ativando **entrelaçamento** (atingindo **512 x 448 píxeis** que é agora proporcional à dimensão de saída). Em troca, o mesmo efeito de espremer afetará também os *tiles* verticalmente. Isso é útil para os casos em que a tela precisa exibir quantidades extras de informações (ou seja, durante o modo multijogador/tela dividida).
- **Modo 6**: combinação do Modo 2 e 5 (alta resolução + rolagem de colunas), mas apenas uma camada com 32 cores é permitida.

Como você pode ver, os programadores agora têm a opção de priorizar o número de cores, camadas, efeitos ou resolução da área selecionada.

#### *Sprites* {.tab}

![Camada de <em>sprite</em> renderizada.](sppu_mario/sprites.png) {.tab-float.pixel}

Uma área da memória chamada **Object Attribute Memory** (OAM) armazena uma tabela com referências de até 128 *sprites* com as seguintes propriedades [@graphics-guidelines]:

- **Tamanho**: a PPU pode combinar até 16 pequenos *tiles* na forma de um quadrante de 4x4 *tiles* para construir um *sprite*.
- **Referências do *tile***: o valor aponta para os *tiles* usados para desenhar o *sprite*.
- **Posição na tela**. Somente os *sprites* posicionados na área visível serão renderizados.
- **Prioridade**: como várias camadas se sobrepõem uma nas outras, o gráfico com a maior prioridade será exibido, isso também é determinado pelo modo de plano de fundo em uso.
- ***Slot* da paleta de cores**, permitindo escolher 9 *slots* da CGRAM.
- ***Flip* X/Y**.

A S-PPU pode desenhar até 32 *sprites* por linha de varredura, ultrapassar isso fará com que o S-PPU descarte os *sprites* com menor prioridade.

#### Resultado {.tab}

![Tada!](sppu_mario/complete.png) {.tab-float.pixel}

A S-PPU desenha cada varredura na tela dinamicamente, processando primeiro a parte respectiva de cada camada e depois as mesclando.

Uma das principais restrições dos jogos do NES era o fato de só poderem atualizar seus gráficos durante o **V-Blank**. O momento em que o feixe do CRT estava retornando ao ponto de partida fornecia um intervalo de tempo razoável para reorganizar alguns *tiles* sem quebrar a imagem.

Bem, agora, graças às novas capacidades do SNES, essa limitação ganhou um significado diferente.

Veja bem, como as novas unidades de DMA/HDMA agora permitem que os programadores realizem transferências de memória sem esperar pelo V-Scan [@cpu-dma], os jogos agora podem atualizar *tiles*, cores e registradores sem esperar que todo o quadro seja desenhado. Na verdade, podemos ir, além disso: como agora é possível **mudar as configurações da S-PPU durante o meio do quadro**, isso significa que é possível **ativar diferentes modos de plano de fundo em diferentes estágios do mesmo quadro**, abrindo a porta para novos projetos originais de jogos!

### _Aquela_ funcionalidade {.tabs-close}

Para ser sincero, eu ainda não mencionei a funcionalidade mais importante deste console...

::: {.subfigures .tabs-nested .tab-float .open-float .pixel}

![Camada de plano de fundo renderizada.](mode7/layer.png){.active title="Plano de fundo"}

![Mapa de plano de fundo alocado.](mode7/map.png){title="Mapa"}

![Quadro renderizado na tela.<br>O primeiro quarto das linhas de varredura usa outro modo para simular a distância, o Modo 7 começa no segundo quarto (isso é possível graças ao HDMA).](mode7/displayed.png){title="Exibido"}

F-Zero (1990).

:::

Apresentando o **Modo 7**, *mais um* modo de plano de fundo, mas desta vez, com uma forma completamente diferente de trabalhar. Embora possa renderizar apenas uma única camada de fundo de 8 bpp, ele fornece a capacidade exclusiva de aplicar as seguintes **transformações afins** nesse plano [@cpu-rgme]:

- Translação.
- Escala.
- Rotação.
- Reflexão.
- Cisalhamento.

A S-PPU é controlada com uma **matriz de rotação** para alterar os parâmetros desse modo. Não entrarei em álgebra linear aqui, mas dependendo do efeito desejado, a CPU terá que executar algumas funções trigonométricas (seno e cosseno) para preencher as entradas dessas tabelas adequadamente. Isso é realmente caro para a 65C816, mesmo com o uso de precisão de ponto fixo. Por esse motivo, com a 5A22, a Ricoh adicionou registradores de multiplicação e divisão para aliviar alguns ciclos.

`r close_float_group(with_markdown = TRUE)`

A propósito, observe que a lista de transformações não menciona a **perspectiva**, que é o que você vê no jogo de exemplo (F-Zero). Isso é alcançado alterando a matriz de rotação em cada chamada do HDMA, criando um efeito pseudo-3D no processo. Isso deve dar uma ideia de quão prático é a S-PPU!

Finalmente, devido ao alto número de cálculos necessários, o mapa de memória é alterado para otimizar o *pipeline* das duas PPUs, o primeiro processa o **Tilemap** (onde os *tiles* são referenciados) enquanto o segundo busca o **Tileset** (onde os *tiles* são armazenados).

### Causas das quedas de quadros

Um outro tópico, você já se perguntou o que causa a lentidão dos jogos? Quando a interrupção V-Blank é chamada para permitir a atualização gráfica, às vezes o jogo ainda está executando um código pesado e pula a janela V-Blank, os gráficos não podem ser atualizados até a próxima chamada V-Blank e, como o quadro não foi atualizado, isso se manifesta como uma queda na taxa de quadros.

Isso também pode acontecer de forma oposta, um processamento extensivo durante um V-Blank não permitirá que o PPU produza o sinal de vídeo (pois o barramento está bloqueado). Assim, linhas pretas durante uma varredura serão exibidas, embora isso seja pouco perceptível, já que os quadros são atualizados 50 ou 60 vezes por segundo.

### Uma saída de vídeo conveniente

Todos os avanços mencionados serão inúteis, a menos que o console envie a imagem para a TV através de um meio que ambos possam entender. Com o Super Nintendo, a empresa estreou algum tipo de conexões *universais, mas proprietária*, chamada de ***Multi Out***, que pode transportar vários tipos de sinais simultaneamente, incluindo **vídeo composto**, **S-Video** e **RGB** [@graphics-pinouts].

Junto ao console, a Nintendo incluiu um cabo "*Multi Out* para video composto", já que essa era praticamente a forma comum de entrada de sinal nas TVs naquela época. Na Europa, no entanto, a porta **SCART** também era muito popular, já que muitos decodificadores e videocassetes dependiam dela. Uma grande coisa sobre o SCART é que ele também pode transportar muitos tipos de sinais, isso permite que o equipamento AV use o tipo de sinal mais adequado sem encontrar problemas de compatibilidade. Infelizmente, a Nintendo nunca lançou um cabo SCART oficial que aproveitasse os pinos RGB expostos no Super Nintendo.

No entanto, a Nintendo alterou o esquema de pinos de seus consoles PAL para cumprir o protocolo SCART e, ao fazer isso, substituiu o pino "sincronização de video composto" por um de 12 volts (que informa à TV para definir a proporção de 4:3). Então, mesmo que o *Multi Out* seja "universal", os cabos RGB resultantes, se houver, são específicos por região.

Penso que os benefícios reais do *Multi Out* começaram a se tornar evidentes durante os tempos atuais, pois permitiu que os usuários aproveitassem o sinal RGB com suas modernas televisões sem alterar os componentes internos do console. Embora, ao contrário do vídeo composto e do S-Video, o RGB exige um sinal extra de "sincronização". Para isso, o cabo pode ser conectado para capturar o sinal de sincronização do vídeo composto ou do S-Video; ou para melhores resultados, use uma linha de sincronização dedicada chamada "sincronização de vídeo composto". Mas, como mencionado no parágrafo anterior, apenas os consoles NTSC tinham essa última opção.

## Áudio

Assim como as habilidades gráficas, o departamento de áudio deste console passou por uma grande reformulação. Eu diria que ele até mesmo oferece as técnicas de síntese mais avançadas de sua geração.

### Arquitetura

Algumas empresas [se associaram à Yamaha](mega-drive-genesis#audio), enquanto outras desenvolveram uma [solução interna](pc-engine#audio). Bem, a Nintendo se associou à **Sony** (o conglomerado de eletrônicos e criadores do _Walkman_) para fornecer a eles um sintetizador sofisticado. Assim, a Sony lhes deu dois processadores: um DSP que pode sequenciar e mixar amostras; e uma CPU que comanda esse DSP.

Portanto, o subsistema de áudio resultante do console é composto por:

- **O DSP**: reproduz amostras ADPCM em oito canais diferentes, que são mesclados e enviados através da saída de áudio. O DSP consegue manipular amostras com resolução de 16 bits a uma taxa de amostragem de 32 kHz, e também fornece:
  - **Panorâma estéreo**: distribui nossos canais para fornecer som estéreo.
  - **Controle de envelope ADSR**: define como o volume muda em diferentes momentos.
  - **Atraso**: simula efeito de eco. Inclui também um filtro de frequência para cortar algumas frequências durante o retorno. Não confuda isso com *Reverb*!
  - **Gerador de ruídos**: gera formas de onda pseudoaleatórias que soam como estática branca.
  - **Modulação de tom**: permite que alguns canais distorçam outros. Similar à síntese FM (usada por [seus concorrentes](mega-drive-genesis#audio)).
- **A CPU SPC700**: conhecido também como "S-SMP", é uma CPU independente de 8 bits que se comunica com o DSP e recebe comandos da CPU principal [@audio-smp].
- **64 KB de PSRAM**: armazena dados de áudio e programas. A CPU principal é responsável por preenchê-la.
  - Se o "Atraso" for ativado, algum espaço será alocado para os dados de retorno (isso é na verdade muito perigoso, já que, se não usado com cuidado, pode substituir dados existentes!).

Este bloco funciona em paralelo com a CPU principal. Quando o console é ligado, o SPC700 inicializa uma ROM interna de 64 bytes que o prepara para receber comandos da CPU principal [@audio-spc]. Depois disso, ele fica inativo.

::: {.subfigures .tabs-nested .tab-float .open-float}

![Canais usados para melodia.](melody){.active video="true" title="Melodia"}

![Os tambores foram escolhidos para fins de demonstração.](drums){video="true" title="Tambores"}

![Todos os canais de áudio.](complete){video="true" title="Completo"}

StarFox (1993).

:::

Para o S-SMP começar a realizar um trabalho útil, ele precisa carregar um programa conhecido como ***driver* de som**. Este último instrui o chip como manipular os dados de áudio brutos que a CPU principal envia para a PSRAM e também como comandar o S-DSP.

Como você pode ver, o subsistema de som foi um grande avanço em comparação com a geração anterior, mas também foi desafiador programá-lo. A documentação que a Nintendo forneceu era notável por incluir explicações incompreensíveis e ignorar recursos importantes, então coube aos programadores conduzir suas próprias pesquisas.

Como consequência, foram encontrados muitos *drivers* de som diferentes no mercado [@audio-drivers], e alguns deles acabaram apresentando recursos surpreendentes.

`r close_float_group(with_markdown = TRUE)`

### Controle de tom

O dobramento de tom (*Pitch bending*) permite produzir notas diferentes utilizando a mesma amostra. O S-SMP incluía um recurso de dobrador de tom útil para alterar o tom de foma suave. Dê uma olhada neste canal extraído de Mother 2/Earthbound, ambos os exemplos vêm da trilha sonora original, no entanto, o primeiro tem o controle de tom desativado.

![Sem modulação de tom.](pitch/no_pitch){.toleft video="true"}

![Com modulação de tom habilitada.](pitch/pitch){.toright video="true"}

### Evolução do NES

Para demonstrar a evolução dos sons do NES para o Super NES, aqui estão duas partituras musicais, uma de um jogo do NES e outra de sua sequência no Super NES. Ambas usaram a mesma composição:

![Mother (1989).](snowman_nes){.toleft video="true"}

![Mother 2/Earthbound (1994).](snowman_snes){.toright video="true"}

### Uso avançado

::: {.subfigures .tabs-nested .tab-float .open-float}

![Canais usados para melodia.](kirby/trebble){.active video="true" title="Melodia"}

![Os tambores foram escolhidos para fins de demonstração.](kirby/drums){video="true" title="Tambores"}

![Todos os canais de áudio.](kirby/complete){video="true" title="Completo"}

Kirby's Dream Land 3 (1997).

:::

Aqui está uma composição mais rica em instrumentos que tira grande proveito do dobramento de tom, eco e envelope.

Essa combinação de técnicas permitiu que a música utilizasse no total apenas cinco canais de som, deixando os outros três canais para efeitos sonoros.

`r close_float_group(with_markdown = TRUE)`

### Confusão estéreo

Os controles de volume do DSP são organizados em blocos com valores de 8 bits com sinal [@audio-gst], o que significa que o volume pode ser configurado com **valores negativos**. *Mas espere*, se "0" significa mudo, o que um número como "-1" faz? Bem, ele **inverterá o sinal**.

Isso é particularmente usado para criar um **efeito de ambiente** (*surrounding*) diferenciado, que é realizado configurando os canais estéreos para saírem **fora de fase** (um canal envia o sinal normal e o outro envia o mesmo sinal, mas invertido).

Infelizmente, o abuso dessa funcionalidade resulta em resultados muito desagradáveis (por exemplo, a sensação de que a música está vindo de dentro da sua cabeça), então você notará que a maioria dos emuladores de SNES simplesmente ignora essa configuração.

Além disso, o estéreo fora de fase é cancelado em dispositivos mono, então esses jogos tiveram que incluir um seletor estéreo/mono para evitar silenciar sua própria trilha sonora.

## Jogos

Para ser franco, o programa principal é escrito em **linguagem de montagem (*assembly*) do 65816** e o *driver* de música é escrito em **linguagem de montagem do SPC700**. Receio que você não encontrará nenhuma das comodidades disponíveis em equipamentos do século XXI.

No entanto, foram distribuídas algumas ferramentas pela Nintendo, pela Intelligent Systems e pela Ricoh para facilitar a vida dos programadores, incluindo [@games-sdk]:

- Diferentes tipos de **unidades de desenvolvimento** com a capacidade de serem controladas por um **depurador** (*debugger*) de uma máquina hospedeira (geralmente rodando MS-DOS).
- **Cartuchos graváveis** (também conhecidos como _Flashcarts_). Não para _pirataria_, mas para permitir que desenvolvedores testem seu código em unidades comerciais.
- **Montadores** (*assemblers*) do 65816 e do SPC700.
- **Manuais de desenvolvimento** que explicam, de uma perspectiva de baixo nível, como este console funciona. Eles também podem incluir diretrizes e normas que os desenvolvedores devem seguir para obter a aprovação de seus jogos pela Nintendo (obrigatória para distribuição).

Curiosamente, vários estúdios de jogos como Argonaut Software, Accolade, SN Systems e outros também desenvolveram seus próprios equipamentos internos que forneceram mais capacidades do que as ofertadas oficialmente (ou seja, editor de memória, leitor de disco flexível, kit de desenvolvimento como uma placa-filha ISA, etc.) [@games-devkit].

### Configuração do Cartucho

Quando se trata de acessar o cartucho, as coisas ficam muito mais confusas em comparação com o modelo de mapeamento relativamente mais simples do NES. Ainda assim, acho interessante, especialmente para entender como ele poderia ser expandido.

O 65C816 usa um barramento de endereço de 24 bits, o que permite acessar até 16 MB de dados. No entanto, devido à forma como o console é projetado, parte do espaço de endereçamento é consumido por [componentes mapeados em memória](nes#memory). Além disso, o 65C816 possui apenas 16 linhas de endereço que são combinadas com um registrador interno para formar um endereço de 24 bits. Isso é análogo a abrigar um [mapeador interno](pc-engine#memory-access) e exigir [comutação de bancos](nes#cartridgegame-data) para acessar dados extras além dos limites do barramento de endereço. Se você ler outros artigos da mesma geração, encontrará essa metodologia um tanto familiar.

![Exemplo de placas de cartuchos projetadas com diferentes configurações [@photography-amos].<br>De cima pra baixo: LoROM (ROM dupla com SRAM alimentada por bateria), LoROM (ROM simples com SRAM alimentada por bateria) e LoROM (ROM simples)](cartridges.png) {.no-borders}

Dito isso, quando se trata de projetar cartuchos, há diversas maneiras de conectar eletricamente os pinos de endereço entre a ROM e a CPU. Cada uma se aproveita da comutação de bancos de uma maneira diferente. Existem dois modelos fundamentais que permitem acessar até **4 MB de ROM** e **64 KB de SRAM** [@games-memory], e eles funcionam assim:

- Com o **modelo LoROM**, os dados da ROM estão disponíveis em blocos de 32 KB com 128 bancos para escolher [@games-bazzinotti]. A SRAM, por outro lado, cabe em dois bancos, mas pode ser acessada através de 15 bancos onde os dados da ROM também podem ser encontrados.
  - Isso significa que o jogo/programa pode precisar realizar uma comutação significativa de bancos durante a execução. Por outro lado, metade dos bancos também são mapeados para parte da WRAM (o que significa que tanto a ROM, SRAM e WRAM podem ser acessadas sem comutação de bancos).
- Com o **modelo HiROM**, o espaço da ROM ocupa bancos inteiros, o que significa que os dados estão disponíveis em blocos de 64 KB com 64 bancos para escolher. Isso requer menos comutação de bancos, mas não permite ler ROM, WRAM e SRAM dentro do mesmo banco.

Em ambos os casos, grande parte do espaço da ROM é espelhada em toda a área restante do espaço de endereçamento da CPU, mas aqui está a parte interessante: uma metade do espaço pode ser lida a ~2,68 MHz enquanto a outra pode atingir **3,58 MHz.**. Isso só ocorrerá se o chip de ROM suportar a velocidade mais alta e o sinal `FastROM` da CPU estiver habilitado [@games-registers].

Algo que ainda não mencionei é que os cartuchos que contém SRAM também precisam ter um chip **MAD-1** (ou similar) para realizar a decodificação de endereço. Isso é especialmente necessário para selecionar corretamente os pinos que escolhem entre chips de ROM e de SRAM [@games-pcb].

#### Além da convenção

Agora, se os programadores precisarem de mais espaço, é aí que entra em jogo os modelos derivados da LoROM e HiROM. Por exemplo, duas variações comumente referidas como **ExHiROM** e **ExLoROM** expandem o espaço de endereçamento da ROM, reduzindo a área espelhada. Ambas podem acessar cerca de **7,9 MB de ROM**.

![Star Fox (1993) usa o chip Super FX GSU para renderizar superfícies 3D (a S-PPU apenas enxerga a camada de plano de fundo feita de <em>tiles<em> _erráticas_).](fox.png) {.open-float.pixel}

Alternativamente, e o mais importante, a LoROM e a HiROM também podem ser adaptadas para abrigar **chips de aprimoramento** no cartucho. Esses são processadores adicionais que expandem as capacidades do console. Para citar alguns exemplos de novas configurações:

- O modelo **MMC** permite a troca de bancos pelo chip local de aprimoramento. Isso foi usado por componentes como **S-DD1** ou o **SPC7110**, ambos descompressores de hardware de *tiles</me>.</li>
- O modelo ***Super Accelerator System*** foi projetado para o muito aclamado **SA-1**, uma **CPU secundária baseada no 65C816** que opera a **10,74 MHz** e possui funcionalidades extras. Ele se baseia no modelo MMC com circuitos para componentes mapeados em memória.
- Duas configurações extras, uma baseada em LoROM e outra em HiROM, abrem caminho para a série de chips **DSP**. Estes são os mesmos derivados da CPU NEC µPD77C25, mas executando um programa diferente. Eles fornecem cálculos de vetores e matrizes [@games-dsp] e diferentes jogos os usam para calcular transformações afins, descomprimir gráficos e calcular o caminho mais curtos.
- O modelo **SFX** tem como alvo o popular chip **Super FX GSU**. Esta configuração mapeia até 8 MB de ROM, onde 2 MB são compartilhados entre a CPU principal e o Super FX. O restante do espaço de endereço também inclui RAM de backup e SRAM. O Super FX é um processador proprietário feito pela Argonaut que se especializa em **renderização de superfícies 3D** e **transformações afins 2D**, os resultados são então transmitidos como *tiles* para que a S-PPU possa exibi-los. Um punhado de jogos notáveis o usou para desenhar modelos 3D e/ou estender o Modo 7 para *sprites* (já que o Modo 7 só pode transformar fundos).</ul>

`r close_float_group()`

É difícil ignorar o impacto que essa engenharia teve nos jogos durante os anos 90, muitos dos quais conseguiram superar as expectativas destes consoles sem exigir módulos de expansão e afins. Pode-se dizer que isso dificultou os planos da Nintendo de lançar um sucessor do SNES (o que pode explicar por que jogos em estágio de desenvolvimento avançado como Starfox 2 foram cancelados).

## Anti-pirataria / Trava de região

Como o Super Nintendo, a Nintendo mais uma vez se tornou a única autoridade na distribuição dos jogos. Assim, em uma tentativa de fazer cumprir as regras da empresa, os engenheiros desenvolveram três camadas diferentes para proteger os cartuchos contra distribuição por terceiros (e, consequentemente, evitar a violação dos direitos autorais).

Em primeiro lugar, o formato dos cartuchos é **diferente entre as regiões**, para que eles não encaixem com consoles de regiões diferentes. De qualquer forma, qualquer pessoa poderia facilmente contornar isso usando um adaptador de terceiros.

Em segundo lugar, o console, assim como o NES, ainda incorpora o [sistema de proteção **10NES**](nes#anti-piracy-and-region-lock) para impedir a distribuição não autorizada. De qualquer forma, o chip CIC acabou sendo clonado.

Como camada final (feita especialmente para proteger contra pirataria), os jogos também incluíam uma cadeia de verificações de pirataria, como:

1. Comparar o tamanho do SRAM (as cópias piratas normalmente incluem grandes blocos de SRAM para funcionar com qualquer tipo de jogo).
2. Uma série de *checksums* no código para verificar se a comparação anterior foi removida. Essas verificações seriam dispersas em diferentes partes do jogo, tornando-as difíceis de encontrar.

Isso poderia ser anulado removendo manualmente essas rotinas, mas, naturalmente, levaria um tempo significativo para encontrar todas. Afinal, elas seriam espalhadas pelo jogo apenas para frustrar o jogador (e eventualmente fazê-los comprar uma cópia legítima). Verdade seja dita, você notará que a maioria das ROMs encontradas na internet teve todas as suas verificações de pirataria removidas.

## Isso é tudo pessoal

![Meu SNES modificado com um cartucho americano.<br>Este jogo foi lançlado apenas nos EUA. Por sorte, um rapaz estava vendendo isso em Glasgow!](mysnes.png)
