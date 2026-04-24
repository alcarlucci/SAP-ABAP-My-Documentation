# SAP ABAP - Debug

Conhecendo o Debug de programas na linguagem ABAP.

## Conceito de Debug

O **Debug** (ou Depuração) é o recurso que permite analisar o código em tempo real, verificando o que está sendo executado e quais valores estão sendo processados em um momento específico.

## *Breakpoints* (Pontos de Parada)

Para iniciar uma sessão de debug, utilizamos os **Breakpoints** (pontos de parada) clicando na margem esquerda do editor de código, uma "bolinha vermelha" aparece, indicando que a execução do programa parará naquela Linha. Os *Breakpoints* devem ser inseridos em Linhas de comando executáveis. Após definir o *breakpoint*, basta executar o programa (F8).

Tipos de *breakpoints*:

- **Session Breakpoint (interno):** debugar processos na sessão atual do usuário logado;
- **External Breakpoint (externo):** debugar requisições que vêm de fora do SAP - apps Fiori, APIs ou serviços web;
  - possui validade temporária (2 horas);
  - desenvolvedores ABAP que trabalham com integrações.

### Configurar Debug para outros Usuários

Para *External Breakpoint* pode ocorrer a necessidade de especificar qual usuário será interceptado pelo debug. Para alterar o usuário de debug:

1. Menu Utilitários (*Utilities*) > Configurações (*Settings*);
2. Aba Editor ABAP > Debugging;
3. Campo *Debug External Requests* > User: inserir ID do usuário que realizará a chamada externa (ex: usuário de integração).

## A Interface e Ferramentas do Debugger

- **Elementos do cabeçalho:**
  - Informações do Servidor;
  - Program/Include/Line: programa, include e linha atual (onde a execução parou);
  - Program Event: qual bloco de evento o código está (ex.START-OF-SELECTION);
  - SY-SUBRC: retorno da última instrução executada (0 - com sucesso; <>0 - erro ou exceção);
  - SY-TABIX: índice da linha atual em interações `LOOP`;
- **Tipos de visualizações (Desktops)** - opções de *layouts* para visualizar código/variáveis de formas distintas:
  - Desktop 1;
  - Desktop 2;
  - Desktop 3;
  - Standard (padrão);
- **ABAP and Screen Stack (Pilha ABAP - *Call Stack*):** permite visualizar a hierarquia de execução do programa, ou seja, o caminho percorrido pelo programa até o ponto atual (Tela > Evento > Método), podendo navegar entre os níveis para inspecionar variáveis de escopos anteriores (clicando nos níveis anteriores da pilha de execução);
- **Aba Variables 1 e Variables 2:** monitorar o conteúdo dos dados:
  - **Variáveis Simples:** basta dar um duplo clique no nome da variável no código ou digitá-la na lista para ver seu valor atual e tipo;
  - **Estruturas e Tabelas:** Ícones específicos indicam se uma variável é uma estrutura ou uma tabela interna. Ao clicar, você visualiza as Linhas e colunas detalhadas;
  - **Objetos:** é possível visualizar as instâncias de classes e seus atributos;
  - **Ferramentas de Limpeza:** ícones para limpar uma variável selecionada ou remover todas da lista de monitoramento;
  - **Saves Variables for Start Variants:** salva a lista atual de variáveis, para continuar monitorando mesmo ao reiniciar o debug. Dessa forma, as variáveis já estarão carregadas na tela;
  - **Manipulação de Variáveis:** é possível alterar o valor de uma variável em tempo de execução (*runtime*) - clicando no íncone lápis ao lado de uma variável na lista, permitindo simular cenários para fins de testes;
- **Aba Locals (Variáveis Locais):** exibe automaticamente todas as variáveis que pertencem ao escopo local atual - bloco de código onde o cursor está parado (só existem e são visíveis durante o fluxo de execução da rotina onde foram declaradas);
- **Aba Globals (Variáveis Globais):** exibe automaticamente todas as variáveis de escopo global, que podem ser acessadas e visualizadas em qualquer nível do programa;
- **Funcionalidade *Go to Statement*:** permite mover o cursor de execução para uma linha anterior ou posterior - `Shift + F12` ou bt dir. > *Goto Statement*;
- **Watchpoint:** permite monitorar uma variável e interromper a execução do programa apenas quando uma condição específica for atendida - a execução para quando o valor da variável muda para o critério definido. Configuração:
  - Debug do programa > botão Watchpoint;
  - Variable: nome da variável que será monitorada;
  - Free Condition Entry: defina o critério de parada (valor da variável);
- **Breakpoint Condition:** configurar um *breakpoint* para parar apenas se uma condição for verdadeira (bt dir. > *Create Breakpoint Condition*);
- **Watchpoint x Breakpoint Condition:** o *Watchpoint* monitora a mudança de valor de uma variável em qualquer lugar do programa; já o *Breakpoint Condition* interrompe a execução numa linha específica quando a condição lógica for verdadeira;
- **Menu Breakpoint > Breakpoint at:** criar breakpoint baseados em comandos, forms, mensagens e outras estruturas.

## Comandos de Navegação

- **F5 (*Step Into* / Passo a Passo):** executa a Linha atual. Se a linha for uma chamada (perform, método, função), o debug entra nessa rotina;
- **F6 (*Step Over* / Executar):** executa a Linha atual por completo. Se for uma chamada de rotina, executa toda a rotina sem entrar nela e para na Linha seguinte;
- **F7 (*Step Out* / Retornar):** executa o restante da rotina atual (form, método ou função) e retorna para o ponto de onde ela foi chamada;
- **F8 (*Continue* / Continuar):** segue com a execução do programa até o próximo *breakpoint* ou até o programa finalizar.
