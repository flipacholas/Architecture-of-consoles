---
long_title: "Arquitetura do Nintendo Entertainment System (NES)"
short_title: "Arquitetura do NES"
name: NES
long_name: Nintendo Entertainment System (NES)
subtitle: Mais do que uma máquina 6502
date: 2019-01-25
release_date: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - 
      title: "Internacional"
      file: international
      caption: "O NES.<br>Lançado em 18/10/1985 na América e 01/09/1986 na Europa."
      active: true
    - 
      title: "Japonês"
      file: japanese
      caption: "O Famicom.<br>Lançado em 15/07/1983 no Japão."
  Motherboard:
    caption: "Mostrando o modelo 'NES'."
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nes
---

## Uma breve introdução

À primeira vista, o NES parece ser só mais um computador 6502, com uma carcaça sofisticada e um controle.

E apesar disso ser *tecnicamente* verdade, deixe-me mostrar o porquê da CPU não ser a *parte principal* desse sistema.

```{r results="asis"}
supporting_imagery()
```

## Modelos e variantes

A Nintendo distribuiu vários modelos diferentes do mesmo console através do globo [@general-variants], e mesmo que todos tenham a mesma arquitetura, muitos têm visuais extremamente diferentes e alguns podem incluir acessórios embutidos. Então, para manter as coisas simples neste artigo, vou me concentrar nas duas revisões mais populares:

- O **Family Computer** (conhecido como _Famicom_) foi a primeira encarnação, lançado somente no Japão. Esse design parecido com um brinquedo possui dois controles não removídeis (dos quais o segundo controle conta com um microfone integrado), uma porta para a pistola (*light gun*, chamada de _Zapper_), saída de vídeo RF (utilizando sinal NTSC-J) e pinos extras na entrada para cartuchos, a fim de expandir as capacidades de aúdio. Esse modelo foi a base para clones populares no Brasil, como o *Dynavision*, *Phantom System* e *PolyStation*.
- O **Nintendo Entertainment System** (conhecido como _NES_ ou informalmente *Nintendinho*) foi uma versão repaginada para o público da América do Norte, Europa e Oceania; com uma aparência bastante similar à de um aparelho VHS. Do ponto de vista técnico, os controles agora são removíveis (sem microfone) e o vídeo foi aprimorado com mais opções de saída (componente RCA), embora a expansão de áudio tenha sido substituída por um subsistema antipirataria. Por fim, a parte inferior do aparelho carrega uma "porta de expansão" que nunca foi utilizada, junto com pinos de cartucho extras que se comunicam com aquela porta [@general-cartridge].

Como o autor cresceu com o nome "NES", esse termo será usado para se referir ao console em geral, mas trocarei para o nome "Famicom" quando me referir a capacidades únicas só encontradas na variante japonesa e seus clones.

## CPU

A CPU do NES é um **Ricoh 2A03** [@cpu-cpu], que por sua vez é baseado na popular CPU 8 bits **MOS Technology 6502** e roda com um clock de **1.79 MHz** ( ou 1.66 MHz em sistemas PAL).

### Um pouco de contexto

O mercado de CPUs no final dos anos 70 e início dos anos 80 era bastante diversificado. Se uma empresa quisesse construir um microcomputador com preços acessíveis, as seguintes opções estavam disponíveis:

- O **Intel 8080**: uma CPU popular em destaque no *Altair*, o primeiro computador "pessoal" (PC). Tem um barramento de dados de 8 bits e um barramento de endereçamento de 16 bits.
- O **Zilog Z80**: uma atualização "não oficial" do 8080, aprimorada com mais instruções, registradores e componentes internos. Foi vendido por um preço menor e ainda podia rodar programas escritos para o 8080 [@cpu-re_1977, p. 86]. A Amstrad e a Sinclair (entre outros) escolheram esta CPU.
- O **Motorola 6800**: outra CPU de 8 bits projetada pela Motorola, contém um conjunto de instruções completamente diferente. Muitos kits DIY de computador, sintetizadores e computadores tudo-em-um incluíam o 6800.

Como se essas opções não fossem o suficiente, outra empresa chamada **MOS** apareceu no mercado e ofereceu uma versão reformulada do 6800: o **6502**. Apesar de ser incompatível com código escrito para os outros, o novo chip era muito, mas *muito* mais barato de produzir \[@cpu-summary_costs\] \[@cpu-re_1981, p. 76\], e era só questão de tempo até as fabricantes de computadores mais famosas (Commodore, Apple, Atari, Acorn, entre outras) escolherem o 6502 para comandar suas máquinas.

De volta ao Japão, a Nintendo precisava de algo barato, porém familiar para desenvolverem, então eles escolheram o 6502. A **Ricoh**, sua fornecedora de CPUs, produziu com sucesso uma CPU compatível com o 6502.

#### O enigma de licenciamento da Ricoh

*Como* a Ricoh conseguiu clonar o 6502 não é claro até hoje. Se esperaria que a MOS tivesse licenciado o projeto do chip para Ricoh, mas há muitas contradições nisso:

- Ambas as versões da Ricoh e da MOS apresentam o mesmo layout, mas a versão da Ricoh contém barramentos cortados (desabilitando certas funções) [@cpu-differences]. Vou entrar em mais detalhes depois.
- Nenhum documento que afirme explicitamente que a MOS licenciou o 6502 para a Ricoh foi encontrado até agora.
- Um artigo publicado em 2008 no site Nikkei Trendy afirma que a Ricoh conseguiu a licença com a Rockwell, uma fabricante de chips autorizada [@cpu-trendi]. Embora seja discutível se uma propriedade intelectual teria sido repassada de segunda mão a terceiros com aprovação da MOS.
- Essa não seria a primeira vez que a Nintendo conseguiu contornar direitos de propriedade intelectual, já que o caso *Ikegami Tsushinki v. Nintendo* no Japão determinou que a Nintendo não possuia direitos autorais sob o código da versão original de Donkey Kong [@cpu-dk].

#### Funções removidas

O Ricoh 2A03 omite o modo **BCD** (*Binary-Coded Decimal*), originalmente incluso no 6502 [@cpu-visualbcd]. O modo codifica cada dígito decimal de um número como um binário de 4 bits separado. O 6502 usa "palavras" de 8 bits, o que significa que cada palavra armazena dois dígitos decimais.

Como um exemplo para os curiosos, o número decimal `42` é representado como:

- `0010 1010` em binário.
- `0100 0010` em BCD.

Poderíamos continuar falando sobre o assunto, mas para resumir: BCD é útil para aplicativos que exigem tratamento de cada casa decimal separadamente (por exemplo, um relógio digital). No entanto, isso exige mais armazenamento, pois cada palavra de 8 bits só consegue representar até o número decimal `99`, enquanto o binário tradicional consegue até o `255`.

De qualquer forma, a Ricoh deliberadamente omitiu o modo BCD de seu chip cortando as linhas de controle que o ativam. Presumivelmente, isso foi feito para evitar o pagamento de royalties para a MOS, já que o BCD foi patenteado por eles (e a legislação que permite direitos autorais sobre os layouts de circuitos integrados nos Estados Unidos só seria promulgada em 1984 [@cpu-protection_act]).

### Memória

Ambos os Ricoh 2A03 e MOS 6502 possuem um **barramento de dados de 8 bits** e um ** barramento de endereçamento de 16 bits**, que lhes permite acessar até **64 KB de memória**. Então, como a Nintendo preencheu esse espaço de memória?

Por um lado, a placa-mãe contém um chip fornecendo **2 KB de RAM Estática** (SRAM) [@cpu-sample_ram]. A Nintendo chama esta área de "*Work Ram*" (WRAM) e ela pode ser usada para armazenar:

- Variáveis para controlar o estado do jogo e/ou para buscar informações.
- A "pilha" (*stack*), que salva temporariamente os valores de registradores enquanto a CPU está executando sub-rotinas.
- Uma "área de *buffer*" para que a CPU possa copiar grandes quantidades de dados entre dois locais.

Por outro lado, os componentes do sistema são **mapeados na memória** [@cpu-cpu_map], o que significa que eles são acessados usando endereços de memória, assim, eles ocupam parte do espaço de endereçamento da CPU. O espaço de memória da CPU é preenchido com endereços apontando para o cartucho do jogo, WRAM, a PPU, a APU e dois controles (não se preocupe com cada componente, eles são explicados ao longo desse artigo).

#### Cartucho/dados do jogo

Caso não saiba, jogos do NES são distribuídos na forma de cartuchos e os barramentos do cartucho se conectam diretamente à CPU.

A Nintendo conectou as linhas do cartucho de uma forma que apenas 49120 Bytes (~ 49,97 KB) dos dados do cartucho podem ser acessados [@cpu-cpu_map]. Agora, o que quero dizer com "dados do cartucho"? Bem, qualquer chip conectado a esses barramentos, por exemplo:

- Uma **ROM de Programa** onde o programa do jogo é armazenado. Isso não inclui os dados de gráficos, como você verá mais tarde na seção "Gráficos". Naturalmente, e ao contrário dos outros chips, este é obrigatório.
- **Chips de RAM** para estender a WRAM.
- Um **Chip de RAM com uma bateria de lítio** para armazenar o progresso do jogo.

O fato de existirem diferentes combinações se deve ao fato que a CPU não se importa com o tipo de componente que está sendo lido, ela só vê os endereços de memória. Então, cabe ao desenvolvedor de jogos escolher (ou criar) um *layout* viável para adequar seu jogo.

![PCB do Super Mario Bros [@photography-nrom].](nrom.png){.tabs-nested .active title="Original"}

![O mesmo PCB com as partes importantes rotuladas. O significado do chip de "Bloqueio" é explicado na seção "Anti-pirataria".](nrom_marked.png){.tabs-nested-last title="Ilustrado"}

Por exemplo, no "Super Mario Bros." da Nintendo foi usado um layout chamado _NES-NROM-256_ que consistia em 32 KB de ROM de programa e 8 KB de *"Character ROM"* para gráficos (veremos mais sobre isso na seção "Gráficos") [@cpu-nrom]. O *layout* _NES-NROM-256_ também estava preparado para suportar até 3 KB de WRAM extra, embora o jogo não faça uso disso.

#### Indo além das capacidades existentes

Uma das grandes limitações dos barramentos de endereço de 16 bits (que afetam os consoles de terceira e quarta geração) é o seu espaço de endereço compacto. Hoje em dia, computadores de 32 bits podem endereçar até 4 GB de memória (e máquinas de 64 bits desfrutam generosamente de até 16 exabytes), portanto isso já não é mais motivo de preocupação. Porém, naquele tempo o NES só tinha um espaço de endereçamento de 64 KB, além de que boa parte dele era consumida por hardware mapeado em memória (algo que os [competidores](master-system#accessing-the-rest-of-the-components) evitaram).

Então, quer dizer que os desenvolvedores de jogos só podiam desenvolver jogos de até 49,97 KB? Não mesmo! Se a história nos ensinou alguma coisa, é que há sempre uma solução inteligente para um problema desafiador; e este problema foi resolvido com um ***Mapper*** (Mapeador).

![Representação simplificada de como um <em>mapper</em> estende as capacidades de endereçamento da CPU. Com a inclusão de um <em>mapper</em>, a CPU pode acessar bancos extras (grupos de endereços) de uma ROM de Programa maior. Embora o jogo/programa tenha a nova tarefa de alternar manualmente entre bancos sempre que necessário.](mapper/mapper.png){.tabs-nested .active title="Com *mapper*"}

![A mesma configuração, mas sem um <em>mapper</em> instalado. Embora mais simples e barata, a CPU só pode acessar um número finito de bancos.](mapper/no_mapper.png){.tabs-nested-last title="Sem *mapper*"}

Um *mapper* é um chip extra incluído no cartucho que fica entre os chips de memória e as linhas de endereçamento do console. Seu principal trabalho é aumentar o espaço de endereçamento para que os desenvolvedores possam usufruir de chips com maior capacidade de armazenamento. Isso é feito pela **troca de banco**: Endereços de memória são agrupados em bancos, e o *mapper* fornece interruptores (controlados através de endereços de memória) para alternar entre os bancos. Apesar disso, a CPU ainda vê a mesma quantidade de memória, então é o jogo que foi programado com a presença de um *mapper* que fica responsável pela troca de banco. Devido ao seu custo-benefício, os *mappers* foram amplamente utilizados nas tecnologias dos anos 80 até o início dos anos 90.

![PCB de Super Mario Bros 3 [@photography-tsrom]](tsrom.png){.tabs-nested .active title="Original"}

![A mesma imagem com partes importantes rotuladas. A princípio, pensei que a WRAM extra fosse usada para armazenar <em>saves</em>, mas depois percebi que não há <em>saves</em> neste jogo (e também não há bateria). Na realidade, esse chip de RAM é utilizado para armazenar um nível descompactado.](tsrom_marked.png){.tabs-nested-last title="Ilustrado"}

Voltando ao NES, um exemplo famoso é o "Super Mario Bros. 3" que foi distribuído com o *mapper* "MMC3" (feito pela Nintendo) em seu cartucho. Para fins de comparação, o chip MMC3 fornecia até 512 KB de espaço para a ROM de programa, até 256 KB para a *Character Memory* e até 8 KB de WRAM adicionais [@cpu-mmc3]. Agora fica claro por que "Super Mario Bros. 3" difere significativamente de qualidade em comparação com o primeiro lançamento da série.

Em resumo, embora este console possa parecer limitado ao examinar suas características internas, a Nintendo se certificou de que ele pudesse se adaptar à medida que a tecnologia evoluía. Por outro lado, ao mesmo tempo que esta técnica ajudou a manter os custos do console baixos, ela também transferiu parte do custo para o cartucho do jogo. Portanto, a qualidade do jogo e os custos com cartuchos eram duas preocupações para os desenvolvedores de jogos ponderarem.

## Gráficos

Os gráficos são gerados por um chip proprietário chamado ***Picture Processing Unit*** (PPU). Esse é um dos chips que dão ao NES uma *identidade*. Em outras palavras, qualquer um pode pegar uma CPU 6502 na loja de hardware, então por que o NES é diferente de, digamos, de um Apple 2 ou de um Commodore 64? Bem, o que distingue o NES de outras máquinas são os chips em volta da CPU: A PPU e a APU. Esses dois constituem, respectivamente, as capacidades gráficas e de áudio exclusivas do NES.

Dito isso, a PPU renderiza gráficos 2D chamados de ***sprites*** e ***backgrounds*** (planos de fundo), enviando o resultado para o sinal de vídeo.

### Organizando o conteúdo

![Arquitetura de memória da PPU](ppu_content.png)

Para renderizar algo na tela, a PPU precisa saber *quais* gráficos deve desenhar, *onde* na tela colocá-los; e *como* desenhá-los (ex: qual paleta usar).

Para responder a estas perguntas, a PPU foi pré-programada com um mapa de memória diferente que procura os seguintes tipos de dados:

- Dados gráficos são lidos do cartucho do jogo, que contém um chip dedicado chamado ***Character Memory*** armazenando os desenhos em 2D (chamados ***tiles***, ou ladrilhos), organizados em uma estrutura de dados chamada ***Pattern Table*** (Tabela de Padrões). A *Character Memory* pode vir na forma de "*Read Only Memory*" (ROM) ou "*Random Access Memory*" (RAM), dependendo se o jogo é composto por um conjunto imutável de gráficos ou se a CPU deve intervir, respectivamente.
  - A PPU endereça **até 8 KB** de *Character Memory*, organizada em dois grupos de 4 KB cada.
- Metadados dizendo à PPU "onde" e "como" desenhar gráficos são encontrados em outras áreas:
  - Uma SRAM separada de **2 KB** fica localizada na placa-mãe, dedicada aos dados relacionados aos gráficos. A Nintendo chama este espaço **Video RAM** (VRAM) e armazena duas estruturas de dados chamadas ***Nametables*** (Tabela de Nomes).
  - A PPU incorpora **256 B** de DRAM para armazenar a ***Object Attribute Memory*** (Memória de Atributo de Objeto, ou OAM).
  - Por último, a PPU também possui **4 B** de memória para definir as paletas de cores.

Não se preocupe com a nova terminologia, o significado dessas estruturas de dados é discutido passo a passo nos parágrafos a seguir.

### Construindo o quadro

Tal como com seus contemporâneos, este chip é projetado para o comportamento de uma tela CRT, popularmente conhecida como "TV de tubo". Não existe um *frame buffer*: a PPU irá renderizar ao mesmo tempo que o feixe de elétrons da CRT varre a tela, construindo a imagem em tempo real.

A PPU desenha quadros com uma dimensão fixa de **256x240 píxeis** [@graphics-overscan]. Infelizmente, devido às discrepâncias entre os padrões de vídeo analógico ao redor do mundo, a imagem terá uma aparência diferente dependendo da região do aparelho (NTSC ou PAL) pelo qual é exibido. Resumindo, as televisões NTSC vão cortar as bordas superiores e inferiores para acomodar a faixa de *overscan* (apenas ~224 linhas de varredura são visíveis), essas bordas são consideradas "zonas de perigo" pelos desenvolvedores na hora de decidir onde colocar elementos no jogo. Por outro lado, as telas PAL não cortarão as bordas, mas mostrarão barras pretas extras para preencher o sinal mais alto (PAL usa 288 linhas de varredura).

Por trás dos panos, o quadro que a saí da PPU é composto por duas camadas diferentes. Para fins de demonstração, vamos usar *Super Mario Bros.* para mostrar como isso funciona:

#### Tiles {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined}

![Duas tabelas de padrões com múltiplos tiles juntos.](ppu_mario/chr_map.png){.active title="Tudo"}

![Um único <em>tile</em>.](ppu_mario/single.png){title="Único"}

*Tiles* encontrado na *Character ROM* (para fins de demonstração, está sendo usada uma paleta padrão).

:::

Para começar, a PPU usa ***tiles*** como um ingrediente básico para produzir *sprites* e *backgrounds*.

O NES define *tiles* como **mapas básicos de 8x8 píxeis**, estes são armazenados na **Character memory** (localizada no cartucho do jogo) e organizados em uma grande estrutura de dados chamada **Tabela de Padrões** [@graphics-rust]. Cada *tile* ocupa 16 B e a Tabela de Padrões contém 256 *tiles* [@graphics-pattern]. Como a PPU acessa até 8 KB de *Character memory*, ela pode acessar até duas Tabelas de Padrões.

Dentro de um *tile*, cada um dos seus pixeis é codificado usando um valor de 2 bits, que faz referência a uma das quatro cores de uma paleta. Os programadores podem definir até oito paletas (quatro para o *background* e as outras quatro para *sprites*). As cores referenciadas em cada paleta apontam para uma "paleta mestra" composta de 64 cores [@graphics-palettes], representando todas as cores que o console consegue produzir. As paletas são feitas de quatro cores, embora uma seja reservada para `transparência`.

Para começar a desenhar algo na tela, os jogos preenchem um conjunto de tabelas com referências a *tiles* na *Character memory*. Cada tabela é responsável por uma camada (*sprite* ou *background*) do quadro. Então, a PPU lê destas tabelas e constrói as linhas de varredura que serão projetadas pelo canhão CRT.

Agora vou explicar como cada camada/tabela funciona e como elas diferem em termos de funcionalidade.

#### Camada *Background* {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![Mapa de plano de fundo alocado.](ppu_mario/nametable.png){.active title="Geral"}

![Mapa de <em>background</em> alocado com a área selecionada marcada.](ppu_mario/nametable_marked.png){title="Selecionado"}

O mapa *background* configurado com espelhamento vertical, que permite rolagem horizontal suave. Contudo, apenas uma metade pode ser usada.

:::

A camada de *background* é um mapa de 512x480 píxeis contendo *tiles* estáticos [@graphics-nametables]. Você pode se lembrar que o quadro visível é muito menor, então o jogo decide qual parte da camada é selecionada para exibição. Jogos também podem mover a área visível durante o jogo; é assim que se chega ao **efeito de rolagem**.

Para economizar memória, grupos de quatro *tiles* são combinados em mapas de 16x16 pixeis chamados de **blocos**, nos quais todos os *tiles* compartilham uma paleta de cores.

As **tabelas de nomes** (armazenadas na VRAM) especificam quais *tiles* exibir na camada de *background*. A PPU procura por quatro tabelas de nomes de tamanho 1024 bytes, onde cada uma correspondendo a um quadrante da camada. No entanto, só 2 KB de VRAM está disponível! Como consequência, apenas duas tabelas de nomes podem ser armazenadas sem o uso de hardware adicional no cartucho. Apesar de ainda ser necessário endereçar as duas tabelas de nomes restantes em algum lugar, a maioria dos jogos apenas as aponta para onde as primeiras duas estão localizadas (o que é conhecido como **espelhamento**).

Embora essa arquitetura possa parecer falha à primeira vista, ela foi projetada para manter os custos baixos enquanto oferecer uma **expansibilidade** simples: se os jogos precisassem de um *background* mais amplo, mais VRAM poderia ser incluída no cartucho.

Os últimos bytes de cada tabela de nomes armazenam uma **tabela de atributos** de 64 bytes que especifica qual paleta de cores é atribuída a cada bloco [@graphics-attribute_table].

#### Camada de *sprites* {.tab}

![Camada de <em>sprite</em> renderizada.](ppu_mario/sprite_layer.png) {.tab-float.pixel}

*Sprites* são *tiles* que podem se mover pela tela. Eles também podem se sobrepor uns aos outros ou aparecer atrás do *background*. A imagem visível será decidida com base em seu valor de prioridade (é o mesmo conceito de "camadas" utilizados em software tradicionais de design gráfico).

A tabela de **Object Attribute Memory** (OAM) especifica quais *tiles* serão usados como *sprites* [@graphics-oam]. Além do índice do *tile*, cada entrada contém uma posição (x,y) e vários atributos (paleta de cores, prioridade e *flags* de espelhamento). Esta tabela é armazenada em uma DRAM de 256 bytes encontrada no chip PPU.

A tabela OAM pode ser preenchida pela CPU. Contudo, isso pode ser bastante lento na prática (e corre o risco de corromper o quadro se não for feito no momento certo). Como consequência, a PPU contém um pequeno componente chamado **Direct Memory Access** ou DMA, que pode ser programado (alterando os registrados da PPU) para buscar a tabela na WRAM. Com o DMA, é garantido que a tabela será carregada a tempo para o próximo quadro ser desenhado, mas é importante lembrar que a CPU será interrompida durante a transferência!

A PPU é limitada a oito *sprites* por linha de varredura e até 64 por quadro. O limite de linha de varredura pode ser excedido graças às habilidades de multiplexação da PPU. Em outras palavras, a PPU irá alternar automaticamente os *sprites* entre as varreduras; contudo, eles aparecerão piscando na tela.

#### Divisão de *background* {.tab}

![Camada de <em>background</em> renderizada, destacando as duas partes com valores de rolagem distintos definidos. Apenas a segunda parte rola conforme o Mario se move.](ppu_mario/split.png) {.tab-float.pixel}

Antes de prosseguirmos, há algo que eu ainda não disse. Se você jogar Super Mario Bros, irá perceber que quando o Mario se move, a cena rola sem problemas. No entanto, você também observará que a área superior (onde fica as estatísticas) permanece estática, **mesmo que ambas as partes façam parte da mesma camada de *background*!** Então, o que acontece aqui? Bem, o jogo está alternando os valores de rolagem no meio do quadro para mostrar o mundo e as estatísticas (que residem numa parte fixa do *background*) simultaneamente. O NES não oferece esse recurso nativamente, mas o jogo deduz os tempos observando o estado da PPU (manifestado através de seu registro de status [@graphics-ppustatus]).

Para conseguir isso, os jogos utilizam uma técnica chamada **Sprite 0 Hit**. Em Super Mario Bros, a PPU é instruída a renderizar um *sprite* fictício atrás da moeda, que é o primeiro *sprite* desenhado no quadro. Após a PPU desenhar este *sprite*, ela atualiza seu registro de status com uma *flag* que denota que o primeiro *sprite* (conhecido como *sprite 0*) foi desenhado. Enquanto isso, o jogo está constantemente verificando no meio do quadro se o status do *sprite 0* foi marcado (ou seja, *hit*), se isso acontecer, o jogo passa a atualizar a propriedade de rolagem da tabela de *background* para movê-la para onde o Mario está.

Em geral, o *Sprite 0 Hit* é um procedimento muito delicado, pois é fácil errar nos tempos (a *flag sprite 0* não é apagada depois de verificada, o que leva a positivos "duplicados" [@graphics-chibiakumas]). Além disso, como essa rotina se repete indefinidamente, pode ser bastante dispendiosa (em ciclos da CPU) para executar. Por outro lado, posteriormente, os *mappers* assumiram essa função com o uso de interrupções automáticas que são acionadas sempre que uma linha de varredura arbitrária é atingida \[@graphics-nesdoug\] (uma técnica muito mais eficiente), o que melhorou significativamente as capacidades visuais de Super Mario Bros 3, por exemplo.

#### Resultado {.tab}

![Tada!](ppu_mario/result.png) {.tab-float.pixel}

Depois que o quadro é finalizado, é hora de passar para o próximo!

No entanto, a CPU não pode modificar nenhuma tabela que esteja sendo usada atualmente pela PPU, caso contrário, podem aparecer artefatos na tela. Então, quando todas as linhas de varredura são concluídas, a PPU aciona a interrupção **Vertical Blank** (V-Blank) na CPU. Isso notifica o jogo de que ele pode começar a atualizar as tabelas sem rasgar a imagem exibida atualmente. Nesse momento, o feixe do CRT está apontando abaixo da área visível da tela, na área de borda inferior (*overscan*).

Apenas alguns registros da PPU podem ser atualizados foram da janela V-Blank [@graphics-outside_vblank], o que explica a capacidade de rolar a camada de *background* no meio do quadro.

### Segredos e limitações {.tabs-close}

Se você está pensando que o sistema de *buffer* de quadros com memória alocada para armazenar o quadro completo teria sido preferível: os cursos de RAM eram muito altos e o objetivo do console era ser acessível. Deixe-me agora mostrar por que esse projeto se mostrou muito eficiente e flexível.

#### Multi-rolagem {.tabs.active}

![Super Mario Bros. 2. Configuração da tabela de nome para rolagem vertical (espelhamento horizontal).](secrets/multiscrolling_mirror.png) {.tab-float.pixel}

![Super Mario Bros. 3. Mario pode correr e voar, então a PPU precisa rolar diagonalmente. Observe a borda direita mostrando a paleta de cores errada! A borda esquerda tem uma máscara aplicada.](secrets/multiscrolling.png) {.tab-float.pixel}

Alguns jogos exigem que o personagem principal se mova verticalmente - assim, a tabela de nomes será configurada com **espelhamento horizontal**. Outros jogos precisam que o personagem se mova para a esquerda e para a direita, então eles usam o **espelhamento vertical**.

Qualquer um dos modos de espelhamentos permite que a PPU atualize os *tiles* de *background* sem que o usuário perceba: há bastante espaço para rolar enquanto novos *tiles* estão sendo renderizados a uma certa distância.

E se o personagem quiser se mover diagonalmente? A PPU pode rolar em qualquer direção, mas sem VRAM extra, as bordas são forçadas a compartilhar a mesma paleta de cores (lembre-se de que os *tiles* são agrupados em blocos).

É por isso que alguns jogos, como *Super Mario Bros. 3*, mostram gráficos estranhos na borda direita da tela enquanto Mario se move (o jogo é configurado para rolagem vertical) [@graphics-seam]. É possível que os desenvolvedores precisassem minimizar o custo de hardware por cartucho (já que este jogo já tem um poderoso *mapper* instalado).

Como uma *correção* interessante, a PPU permite que os desenvolvedores apliquem uma máscara vertical sobre os *tiles*, ocultando efetivamente parte da área com defeito.

#### Troca de *Tile* {.tab}

![Hipoteticamente, se fosse renderizado usando os <em>tiles</em> disponíveis durante as primeiras linhas de varredura.](secrets/multiplexing_1.png){.tabs-nested .tab-float .pixel .active title="Varredura 1"}

![Hipoteticamente, se fosse renderizado usando os <em>tiles</em> disponíveis durante as últimas linhas de varredura.](secrets/multiplexing_2.png){.tab-nested .pixel title="Varredura 2"}

![O quadro real exibido ao usuário.](secrets/multiplexing_complete.png){.tabs-nested-last .pixel title="Exibido"}

Outra especialidade de Super Mario Bros. 3 é a quantidade de gráficos que ele pode exibir.

Este jogo exibe mais *tiles* de *background* do que é estritamente permitido. Então, como ele faz isso? Se tirarmos duas capturas de tela em momentos diferentes enquanto a exibição é gerada, é possível observar que o quadro final é realmente composto de *dois* quadros diferentes.

Esta é outra magia do *mapper* MMC3. Ele não só foi usado para acessar espaço extra na ROM de Programa, também foi usado para estende o espaço do *Character ROM* conectando dois chips de *Character ROM* diferentes. Ao verificar qual parte da tela a PPU está solicitando, o *mapper* redireciona para um chip ou outro — permitindo assim mais *tiles* únicos na tela do que era originalmente suportado [@graphics-n3s].

#### Comportamento curioso {.tab}

Ao longo da minha pesquisa, encontrei muitos artigos interessantes que explicam comportamentos incomuns da PPU, então pensei em mencionar alguns aqui:

- Ao contrário do [VDP do Master System](master-system#graphics), que gera cores RGB que são posteriormente codificadas em sinais NTSC/PAL para transmissão, a **PPU do NES faz tudo de uma vez** [@graphics-palettes]. Portanto, não há uma conexão um-a-um entre as cores da paleta mestra da PPU e o espaço de cores RGB padrão (amplamente adotado pela tecnologia atual). Isso deixa algum espaço para interpretação e, como consequência, vários emuladores exibirão uma paleta diferente.
  - As discrepâncias entre paletas RGB são mais evidentes com o kit DIY de Tim Worthington, que adiciona saída de sinal RGB ao NES, pois ele também implementa uma interruptor que escolhe entre três paletas pré-definidas [@graphics-nesrgb].
- A paleta mestra contém uma cor **"amaldiçoada"** (`$0D`) que pode bagunçar o sinal de TV NTSC [@graphics-cursed_colour]. Bem, o que acontece é que a TV confunde o sinal ao exibir essa cor com o sinal de supressão, então pode ocorrer cintilação.
- A PPU depende da DRAM para armazenar sua *Object Attribute Memory* (OAM). A DRAM exige atualizações constantes para evitar a perda de dados (ao contrário do SRAM), entretanto a PPU não atualiza o DRAM quando não está renderizando o quadro [@graphics-oam]. Isso se manifesta durante a sincronização vertical. Por esse motivo, é recomendado não atualizar a OAM fora da sincronização vertical, pois o período de não-atualização que ocorre durante o *V-blank* pode corromper parte da tabela.
  - A variante da PPU para sistemas PAL não são afetadas por isso, pois atualiza durante a *V-Blank* (que dura mais nos sistemas PAL).

## Áudio {.tabs-close}

Um componente dedicado chamado **Audio Processing Unit** (Unidade de Processamento de Áudio, ou APU) fornece esse serviço [@audio-apu]. A Ricoh a embutiu dentro do chip da CPU, presumivelmente para evitar a clonagem não autorizada tanto da CPU quanto da APU.

### Funcionalidade

Este circuito de áudio é comumente referido como um ***Programmable Sound Generator*** (Gerador de Som Programável, ou PSG), o que implica vagamente que ele pode produzir apenas um conjunto predefinido de formas de onda, e nesse caso é _em grande parte_ verdade. A APU sequencia dados de áudio em **cinco canais** de áudio — cada um reservado para uma forma de onda ou sinal específico. Cada canal contém diferentes propriedades que alteram o tom da forma de onda, o som, o volume e/ou duração. Eles são continuamente mixados e enviados através do sinal de saída de áudio.

A funcionalidade da APU é exposta através dos endereços de memória, a CPU lê os dados relacionados à música encontrados na ROM de programa e programa a APU de acordo.

Indo além, o modelo Famicom em particular fornece pinos de cartucho que enviam o sinal de áudio mixado para o cartucho, assim o cartucho pode mixar o som com **canais extras** (que exigem chips adicionais) [@general-cartridge].

Vamos agora analisar os cinco canais fornecidos pela APU [@audio-review]:

#### Pulso {.tabs.active}

::: {.subfigures .tabs-nested .tab-float}

![Visão do osciloscópio do canal de pulso 1.](pulse_single_1){video="true" title="Pulso 1"}

![Visão do osciloscópio do canal de pulso 2.](pulse_single_2){.active video="true" title="Pulso 2"}

![Visão do osciloscópio de todos os canais de áudio.](pulse_full){video="true" title="Completo"}

Mother (1989).

:::

Os primeiros **dois canais** produzem **ondas de pulso** [@audio-apupulse]. Quando ouvidas, apresentam um som de *beep* muito distinto que é principalmente usado para **melodias ou efeitos sonoros**. O sequenciador respectivo pode gerar três tipos de ondas de pulso, feitas variando a duração do pulso (também conhecido como ***duty cycle***, ou razão cíclica). Os circuitos também estão conectados a uma **unidade de *sweep*** (permitindo modular o tom) e a um **gerador de envelope** para alterar o volume ao longo do tempo (também conhecido como **decaimento**).

A maioria dos jogos usa um canal de pulso para a melodia e outro para o acompanhamento. Você pode notar que frequentemente quando um jogo precisa reproduzir um efeito sonoro, o canal de acompanhamento é alterado para reproduzir o efeito e depois retorna ao acompanhamento. Isso evita interromper a melodia durante o jogo.

Acredito ser justo dizer que as ondas de pulso são um dos emblemas desta geração de consoles. Presumo que sua adoção foi feita puramente por motivos de custo-benefício: uma CPU (limitada) pode processar apenas uma certa quantidade de dados por vez, e as ondas de pulso são ideais no sentido de que não exigem muitos parâmetros para tocar melodias simples (o que, por sua vez, libera ciclos da CPU para outras operações).

#### Triângulo {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Visão do osciloscópio do canal de triângulo.](triangle_single){.active video="true" title="Triângulo"}

![Visão do osciloscópio de todos os canais de áudio.](triangle_full){video="true" title="Completo"}

Mother (1989).

:::

Uma das especialidades da APU, quando comparada à concorrência, é a capacidade de produzir **ondas triangulares**. Estas são frequentemente usadas como uma **linha de baixo** para a melodia. Além disso, modificando drasticamente o seu tom, é possível utilizá-las também para **percussão**.

A APU tem um canal reservado para este tipo de onda. Nos bastidores, um sequenciador dedicado leva 32 ciclos para gerar um sinal triangular [@audio-aputriangle], esta limitação faz com que a forma de onda resultante tenha a forma de uma escada.

Por outro lado, o respectivo circuito não fornece controle de volume. De qualquer forma, alguns jogos encontraram outras formas, mexendo com o controle de volume do mixer.

#### Ruído {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Visão do osciloscópio do canal de ruído.](noise_single){.active video="true" title="Ruído"}

![Visão do osciloscópio de todos os canais de áudio.](noise_full){video="true" title="Completo"}

Mother (1989).

:::

O conceito de "ruído" é atribuído a uma série de formas de onda que não seguem um padrão ou ordem. Por sua vez, nossos ouvidos o interpretam como estática branca. Dito isto, a APU aloca um canal que pode reproduzir diferentes tipos de ruído.

Nos bastidores, o gerador de ruído se baseia no gerador de envelope (similar ao canal de pulso), sendo aleatoriamente silenciado por uma porta OR [@audio-apunoise]. A condição para o silenciamento depende do valor de um registrador de deslocamento de 15 bits conectado ao circuito de retroalimentação. Isso faz com que a saída do circuito gere um sinal com padrões _pseudo-imprevisíveis_, e portanto ruído.

Em termos de controle, 4 bits alteram o período do gerador de envelope e um bit altera o "modo" do registrador de deslocamento. Isso deixa disponíveis 32 predefinições de ruídos. Metade (16) dessas predefinições produzem **estática limpa** e a outra metade, **estática robótica**.

Em geral, os jogos usam o canal de ruído para percussão ou efeitos ambientais.

#### Amostra {.tab}

::: {.subfigures .tabs-nested .tab-float}

![Visão do osciloscópio do canal de amostra.](sample_single){.active video="true" title="Amostra"}

![Visão do osciloscópio de todos os canais de áudio.](sample_full){video="true" title="Completo"}

Mother (1989).

:::

Amostras são pedaços gravados de música que podem ser reproduzidos. Como você pode ver, as amostras não se limitam a uma única forma de onda, mas consomem muito mais espaço.

A APU possui um canal dedicado a amostras. Nesse canal, as amostras são limitadas a uma **resolução de 7 bits** (codificadas com valores de `0` a `127`) e uma taxa de amostragem de **~15,74 kHz** [@audio-2a03ref]. Para programar esse canal, os jogos podem transmitir valores de 7 bits (o que consome muitos ciclos e espaço de armazenamento) ou usar **modulação delta** para codificar apenas a variação entre a próxima amostra e a anterior.

A implementação da modulação delta na APU só recebe valores de 1 bit, o que significa que os jogos só podem informar se a amostra aumenta ou diminui `1` a cada vez que o contador é acionado. Assim, em detrimento da fidelidade, a modulação delta pode poupar os jogos de ter que transmitir valores contínuos para a APU.

Como a programação desse canal requer mais espaço e ciclos de CPU, os jogos normalmente armazenam pequenas amostras (como sons de bateria) que podem ser reproduzidas repetidamente. De qualquer forma, ao longo da vida útil do NES, inúmeros desenvolvedores criaram usos inteligentes para esse canal.

### Segredos e limitações {.tabs-close}

Enquanto a APU não era comparável à qualidade do vinil, cassete ou CD, os programadores encontraram maneiras de expandir sua capacidade, principalmente graças à arquitetura modular do NES.

#### Canais extras {.tabs.active}

![Visão do osciloscópio de Castlevania III (EUA/Europa, 1989).](castlevania_usa){.tabs-nested .tab-float .active video="true" title="Americano"}

![Visão de osciloscópio de Akumajō Densetsu (versão japonesa de Castlevania III, 1989).](castlevania_jap){.tabs-nested-last video="true" title="Japonês"}

Lembra que o Famicom fornecia pinos de cartucho exclusivos disponíveis para expansão de som? Bem, jogos como *Castlevania 3* aproveitaram isso e incluíram um chip extra chamado **Konami VRC6**, que adicionou **duas ondas de pulso extras e uma onda de dente de serra** à mistura.

Dê uma olhada nos dois exemplos que mostram a diferença entre a versão japonesa e a americana do jogo (esta última roda na variante do NES, que não forneceu recursos de expansão de som).

#### Tremolo {.tab}

![Visão do osciloscópio de Final Fantasy III (1990).](tremolo_full){.tab-float video="true"}

Em vez de aumentar o custo dos cartuchos, alguns jogos priorizaram a criatividade em vez da tecnologia para adicionar mais canais.

Neste exemplo, Final Fantasy III teve a ideia de usar efeitos de tremolo para dar a sensação de canais extras.

### Uma observação mais refinada {.tabs-close}

Agora que você teve uma visão do que a APU é capaz, deixe-me mostrar um método alternativo para observar como o seu som se comporta. Isso não apenas complementará o que já foi falado sobre a APU, como também fornecerá uma análise mais objetiva, especialmente porque não depende mais dos seus ouvidos.

Antes de tudo, vamos começar com uma breve introdução à teoria do som.

Graças aos princípios da **Análise de Fourier**, podemos decompor todo o som que ouvimos em uma **soma de ondas senoidais** de diferentes frequências e amplitudes [@audio-complexwaveforms]. A onda senoidal mais grave (na frequência mais baixa) é chamada de **fundamental** e as demais são chamadas de **sobretons**. Se você adicionar a onda fundamental e seus sobretons, você recupera o som original. Dito isso, com os sons que têm um tom reconhecível, você notará que a maioria (se não todos) os sobretons têm frequências que são múltiplos da frequência fundamental. Assim, esses sobretons são chamados de **harmônicos** [@audio-harmonics].

Harmônicos se tornarão um tópico recorrente nesta seção, pois formas de onda como pulsos, triângulos e dentes de serra seguem uma fórmula que dita os harmônicos que elas contêm. Caso contrário, essas formas de onda podem se desviar de sua forma "perfeita".

#### Introdução aos espectrogramas

Como as ondas senoidais são agora o ingrediente-chave que pode compor qualquer som, podemos analisar os sons que ouvimos pelas suas ondas senoidais. Agora, para qualquer tipo de análise de dados, nada é mais conveniente do que plotar um gráfico para organizar grandes quantidades de informação. Bem, em caso de análise sonora, temos os **Espectogramas**. Eles codificam todas as informações de uma amostra de áudio em um único gráfico. O eixo X denota o tempo (em segundos), o eixo Y denota as frequências (em Hz) das ondas senoidais produzidas durante esse tempo e o eixo Z (brilho da cor de cada ponto) denota a potência/sonoridade (em decibéis) de cada frequência.

![Exemplo de espectrograma visualizando seis segundos de um canal de pulso.](spectrogram_example){video="true"}

Como você pode observar neste exemplo, cada linha horizontal (também conhecida como sequência de pontos) corresponde a uma onda senoidal (a mais baixa é a fundamental, enquanto as demais são os harmônicos) e o brilho indica a amplitude. Com isso em mente, podemos extrair as seguintes informações:

- Com o tempo, as linhas horizontais tendem a se deslocar drasticamente. Isso é a mudança de tom do canal de pulso.
- O início de cada nota é alto (os pontos são brilhantes) seguido de um rápido declínio. Isso é o controle de envelope da APU em ação.
  - Além disso, no final do período de decaimento, aparece uma linha vertical brilhante. É bastante breve, então não é facilmente audível, mas, em todo caso, é ruído (um som curto de estalo) e eu suspeito que seja uma deficiência da APU.
- Segurar uma nota por mais de 0,25 segundos faz surgir **vibrato** (flutuações contínuas no eixo Y). Não tenho certeza se isso é intencional (usando a função de varredura) ou um efeito adverso da APU.

Observe como a maioria dessas observações não são facilmente derivadas apenas ouvindo um simples áudio, por esta razão, esta seção foi escrita.

#### Plotando a APU

Para estudar a APU do NES, compilei cinco espectogramas, cada um correspondente a um canal de APU utilizando os exemplos anteriores. Ao lado deles, você verá minha tentativa de desvendar o que os dados estão exibindo.

Antes de começar, devo admitir que para obter dados sem imprecisões (como ruídos extras), tive que fazer algumas concessões. As gravações foram obtidas usando um programa do Windows chamado "towave", que usa **síntese de banda limitada** para resolver um problema fundamental com a emulação de chips de áudio baseados em PSG. Basicamente, pulsos, triângulos e dentes de serra são feitos de **harmônicos infinitos**. No entanto, isso não se mistura bem com placas de som modernas que são limitadas as amostras de 44,1 kHz. Portanto, uma técnica chamada "síntese de banda limitada" é usada para escolher os harmônicos corretos na limitação da placa de som. No geral, essa técnica fornece um equilíbrio viável entre desempenho, precisão e prevenção de *aliasing* (frequências falsas). Contudo, os dados podem não ser 100% idênticos ao seu equivalente analógico (que também introduziria outros problemas, como ruído do equipamento de gravação), mas acredito que estejam em um grau aceitável e, o mais importante, cumpre sua tarefa para esta seção do artigo.

Dito isso, vamos para a análise.

##### Pulso {.tabs.active}

![Espectrograma do canal de pulso 1.](spectrograms/eb0_pulse_nes.png) {.tab-float}

A teoria diz que um tom de pulso contém apenas harmônicos ímpares. Em outras palavras, a fundamental é combinada com seu terceiro harmônico, quinto e assim por diante. Além disso, cada harmônico diminui sua amplitude quanto mais afastado estiver da fundamental. A fórmula da amplitude é `amplitude = 1 ÷ número harmônico` [@audio-pulse].

Portanto, observe como o brilho de cada harmônico no espectrograma diminui à medida que ele sobre no eixo Y. Contudo, as ondas de pulso da APU também parecem exibir o efeito de *vibrato* mencionado anteriormente, que aumenta a cada número harmônico. Além disso, áreas do espectrograma que deveriam estar vazias de qualquer som contêm sobretons abafados (possivelmente resultado de ruído e outras imperfeições).

##### Triângulo {.tab}

![Espectrograma do canal de triângulo.](spectrograms/eb0_triangle_nes.png) {.tab-float}

Uma onda triangular é também composta por harmônicos ímpares, mas com uma diminuição mais rápida de sua amplitude (onde a `amplitude = 1 ÷ harmonic number²` [@audio-triangle]).

No entanto, isso não é o que aparece aqui, a forma de escada da onda triangular, que a APU produz, leva a harmônicos extras e aumento das amplitudes.

##### Ruído {.tab}

![Espectrograma do canal de ruído.](spectrograms/eb0_noise_nes.png) {.tab-float}

Naturalmente, o ruído não segue as regras dos harmônicos e pode preencher aleatoriamente todo o espaço de frequência, portanto, a falta de um tom facilmente reconhecível.

No entanto, seguindo a linha do tempo, é possível diferenciar os diferentes pré-configurações de ruído que a APU fornece, cada um exibindo um conjunto distinto de sobretons.

##### Amostra {.tab}

![Espectrograma do canal de amostra.](spectrograms/eb0_dcm_nes.png) {.tab-float}

Diferente dos canais anteriores, o canal de amostra reproduz apenas a gravação de baixa resolução que o desenvolvedor alimenta na APU. Considerando que o exemplo tocava uma bateria, não consigo identificar nenhuma característica identificável no espectrograma (além das semelhanças com o ruído branco).

##### Dente de serra {.tab}

![Espectrograma do canal de dente de serra VRC6.](spectrograms/castlevania_saw_nes.png) {.tab-float}

Como bônus, vamos dar uma olhada no canal de dente de serra (*sawtooth*) da expansão VRC6. Para começar, uma onda de dente de serra perfeita é composta por todos os harmônicos, cada um exibindo amplitudes decrescentes (onde a `amplitude = 1 ÷ harmonic number` [@audio-sawtooth]).

Isso é bastante exigente para equipamentos digitais e naturalmente inacessível para um cartucho de jogo (talvez nem precise de tanta perfeição!). Similar as ondas de triângulo da APU, o VRC6 sequencia as ondas de dente de serra em 7 ciclos (e assim produz efeitos de escada semelhantes).

Consequentemente, o respectivo espectrograma é bastante confuso, as técnicas de aproximação do VRC6 preenchem a onda com harmônicos extras em diversos lugares.

#### Conclusão {.tabs-close}

Bem, parece que as formas de onda sintéticas do NES não são moldadas como a teoria dita. Isso significa que a APU tem falhas? Não! A maneira que a APU foi projetada acabou concedendo ao console sons únicos e identificáveis — e essas propriedades, intencionais ou não, fizeram com que os espectrogramas exibissem resultados incomuns.

Como observação adicional, a geometria perfeita pode ser agradável aos nossos olhos, mas curiosamente, nossos ouvidos não são particularmente adeptos de formas de onda com bordas perfeitas! (você começa a ouvir estalos).

Olhando adiante, a análise de som usando espectrogramas será útil em outros artigos, seja para uma análise simples ou para fazer comparações com outros sistemas. Observe que esses gráficos não são a _ferramenta definitiva_, especialmente com amostras de som que foram misturadas com muitos canais/instrumentos (dificultando muito sua decomposição). Mas acredito que eles fornecem um bom ponto de partida para qualquer tido de estudo objetivo.

## Jogos

Os jogos do NES foram escritos principalmente em linguagem de montagem 6502 e residem na **ROM de Programa**, enquanto os gráficos do jogo (*tiles*) são armazenados na **Character memory**.

Além disso, os jogos eram vendidos (ou alugados) em lojas de varejo sob a aprovação da Nintendo.

### A mídia alternativa

Mesmo que tenha sido lançado apenas no Japão, eu pensei que essa seria uma boa oportunidade para apresentar um complemento curto, mas peculiar, que, assim como os *mappers*, trouxe mais capacidades para este console. Este periférico era chamado de **Famicom Disk System** (FDS) e foi lançado em 1986 (~3 anos após o Famicom). Ele tinha a forma de um leitor de disquetes externo e vinha com um cartucho de formato estranho chamado "adaptador de RAM".

::: {.subfigures .side-by-side}

![A unidade, onde os disquetes são inseridos (a foto mostra um disquete de papelão inserido para proteção). Ele funciona com seis pilhas C (1,5 V cada) ou um adaptador AC de 3,6 W.](fds/drive.png) {.toleft.no-borders}

![O adaptador de RAM, encaixado na entrada de cartucho do Famicom e conectado à unidade por meio de um cabo.](fds/ram.png) {.toright.no-borders}

Os dois componentes que compõem o *Famicom Disk System* (FDS).

:::

O *Famicom Disk System* adicionou os seguintes serviços ao Famicom:

- Uma nova mídia de distribuição para jogos chamada **Famicom Disk** [@games-fds]. Baseado no "Quick Disk" da Mitsumi, ele fornece **~64 KB de dados** por lado e é regravável.
- Um **canal de áudio extra** que usa [síntese de tabela de onda](game-boy#tab-3-2-wave) [@games-fds_audio].

![O equipamento FDS acoplado ao Famicom.](fds/mounted.png) {.open-float.no-borders}

Como o disquete é uma mídia única (diferente dos cartuchos que podem ter vários chips), todos os dados do jogo precisam ser comprimidos dentro dele, embora seja mantido organizado com o uso de um sistema de arquivos proprietário.

No entanto, o Famicom/NES exige rigorosamente que a memória de Programa e de Personagens seja segregada para funcionar, então esse é o trabalho que "adaptador de RAM" tem que resolver. Este componente abriga **32 KB de RAM de Programa** e **8 KB de RAM de Personagem** para armazenar em *buffer* os dados do jogo lidos do disquete, e ao fazê-lo, permite que o console leia dele como se fosse um jogo em cartucho.

{.close-float}

Para operar a unidade, o adaptador de RAM incorpora uma ROM adicional de 8 KB para armazenar um **BIOS** [@games-fds_bios]. Este programa realiza as seguintes tarefas:

- Carrega uma animação de abertura.
- Inicializa o jogo a partir do disquete. Nos bastidores, o BIOS contém rotinas para copiar o conteúdo do disquete para o respectivo chip de memória, possibilitando que o console possa lê-lo.
- Fornece bibliotecas de entrada e saídas para os jogos, permitindo, por exemplo, percorrer o sistema de arquivo do disco.

![Exemplo de dois jogos comerciais para o FDS. A cor azul do disco também era à prova de poeira.](fds/floppies.jpg) {.toleft}

![A animação de abertura do FDS, esperando o usuário... hum... inserir um jogo.](fds_bios){.toright video="true"}

Durante aquela época, a Nintendo instalou alguns "quiosques" em lojas de varejo para que os usuários pudessem levar seus disquetes e regravá-los com um novo jogo a um preço reduzido.

Infelizmente, após alguns anos de vida útil, o Famicom Disk System foi descontinuado e os jogos subsequentes voltaram ao formato de cartucho. Por outro lado, surgiram novos *mappers* disponíveis com capacidades semelhantes (ou melhores) em comparação às funções do FDS.

## Anti-pirataria e Trava de região

A Nintendo foi capaz de bloquear a publicação não autorizada graças à inclusão de um chip de **bloqueio** proprietário chamado **Checking Integrated Circuit** (CIC) [@anti_piracy-cic]. Ele está localizado no console e está conectado ao sinal de reinicialização (portanto, não pode ser facilmente removido).

Este chip executa o **10NES**, um programa interno que verifica a existência de outro chip de bloqueio no cartucho do jogo. Se essa verificação falhar, o console é enviado para um ciclo de reinicialização infinito.

Ambos os chips de bloqueio estão em constante comunicação durante o tempo que o console está ligado. Este sistema pode ser vencido cortando um dos pinos do bloqueio do console, o que deixa o chip em estado ocioso. Alternativamente, enviando um sinal de -5V pode congelá-lo.

O CIC existe como resultado do medo causado pelo colapso dos videogames em 1983. O então presidente da Nintendo, Hiroshi Yamauchi, decidiu que, para impor jogos de boa qualidade, eles seriam responsáveis por aprovar cada um deles [@anti_piracy-vindicator].

Perceba que o modelo japonês do console, o Famicom, foi lançado antes do colapso de 1983. É por isso que nem os cartuchos, nem o console incluem circuitos CIC [@anti_piracy-nocic], em vez disso, os pinos são usados para expansão de som opcional.

## Isso é tudo pessoal
