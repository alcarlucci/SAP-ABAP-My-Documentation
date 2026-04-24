# SAP ABAP - Programação ABAP

Conceitos para criação de programas na linguagem ABAP.

## Introdução à Criação de Programas ABAP (Reports)

O objetivo principal de um programa no SAP é solucionar um problema de negócio ou atender a uma necessidade específica, como a geração de relatórios de vendas por exemplo.

### Estrutura de um Programa Procedural

O SAP suporta e incentiva a Programação Orientação a Objetos (**OOP**), no entanto, para o entendimento lógico em programação, vamos ver a estrutura fundamental de um programa usando **Programação Procedural**. Um programa **ABAP clássico** é estruturado em três pilares principais:

1. **Inputs (Entradas de Dados):** os Inputs constituem a Tela de Seleção (*Selection Screen*). É a interface onde o usuário insere filtros antes da execução do programa. Existem dois comandos principais para criar esses campos (filtros):
    - **PARAMETERS:** utilizado para criar campos de entrada de valor único (ex.: filtrar por um ID específico). Pode-se usar a adição `OBLIGATORY` para tornar o preenchimento obrigatório;
    - **SELECT-OPTIONS:** utilizado para criar campos que aceitam múltiplos valores, intervalos (*ranges*) e exclusões. Para utilizar este comando referenciando um campo de tabela, é necessário declarar a tabela anteriormente com o comando `TABLES`;
2. **Processamento (Lógica e Regras de Negócio):** esta etapa ocorre após o usuário preencher os filtros e executar o programa. É onde manipulamos os dados. O evento principal utilizado é o `START-OF-SELECTION`.
    - **Seleção de Dados:** utilizamos o comando `SELECT` para buscar dados das tabelas do banco de dados e armazená-los em tabelas internas (variáveis Locais);
    - **Filtros:** conectamos os *Inputs* à seleção de dados utilizando uma cláusula `WHERE` na seleção;
      - Para `PARAMETERS` utilizamos a igualdade `=` ou operador `EQ`;
      - Para `SELECT-OPTIONS` utilizamos o operador `IN`, pois ele verifica se o valor está dentro do *range* ou lista de valores informados;
3. **Outputs (Saídas de Dados):** resultado visual apresentado ao usuário. O evento associado ao final do processamento é o `END-OF-SELECTION`.
    - **Exibição Básica (`WRITE`):** para fins didáticos, usamos o comando `WRITE` dentro de um Laço de repetição (`LOOP`) para exibir os dados da tabela interna na
tela;
    - **ALV (*ABAP List Viewer*):** em cenários reais, utiliza-se o **ALV** para gerar relatórios em formato de **Grid**, com recursos avançados de ordenação e exportação.

**Resumo / Exemplo de Código:**

```abap
" Declaração de Tabelas
TABLES: zdlfajrt_002. " utiliza a tabela de Atividades

" Inputs (Entrada de Dados)

" Definição da tela de Seleção
PARAMETERS:     p_id   TYPE zdlfajrel_002. " Filtrar uma opção
SELECT-OPTIONS: s_stat FOR  zdlfajrt_002-status. " Filtrar várias opções

" Processamentos (Lógica e Regras de Negócio)

START-OF-SELECTION. " Evento principal

" Seleção de Dados
SELECT *
  FROM zdlfajrt_002 INTO TABLE @DATA(lt_atividades)
  WHERE atividade EQ @p_id AND
        status    IN @s_stat.

END-OF-SELECTION. " envento de finalização do processamento

" Outputs (Saída de Dados)

" Loop na Tabela e Exibição dos Dados
LOOP AT lt_atividades INTO DATA(ls_atividade).
  WRITE: / 'ID Atividade: ', ls_atividade-atividade, ' - Descrição: ', ls_atividade-descricao.
ENDLOOP.
```

## Funções (Function Modules)

As funções são blocos de código reutilizáveis projetados para executar tarefas específicas, como cálculos,
formatação de dados ou interações com o banco de dados. Uma função agrupa um algoritmo recebendo parâmetros de entrada e retornando resultados.

### Conceitos Fundamentais

- **Transação SE37 (*Function Builder*):** é o ambiente principal para criar, exibir e testar módulos de função;
- **Grupo de Funções (*Function Group*):** toda função deve pertencer a um grupo de funções. O grupo atua como um conteiner ou pacote que organiza funções relacionadas (SE80 > Function Groups > Create);
- **RFC (*Remote Function Call*):** uma função pode ser configurada como "acesso remoto", permitindo que sistemas externos a consumam como uma API (via protocolo SOAP, por exemplo).

### Estrutura de uma Função

Criação de uma função: **SE37** > **Function Module** (*nome da função*) > **Create**. Abas importantes após a criação:

- **Import:** parâmetros de entrada (dados que a função recebe);
- **Export:** parâmetros de saída (dados que a função retorna);
- **Changing:** parâmetros que entram, são processados e retornam modificados;
- **Tables:** utilizado para passar ou retornar Listas de dados (tabelas internas);
- **Exceptions:** tratamento de erros previstos na Lógica;
- **Source Code:** código fonte da função, onde a lógica ABAP é escrita.

### Funções prontas do SAP

O SAP possui milhares de funções padrão prontas para uso. Antes de criar uma nova função é recomendável pesquisar na SE37. Por ex.: a função padrão `DATE_COMPUTE_DAY` realiza o cálculo numérico do dia da semana.

## Classes e OOP no ABAP

A Orientação a Objetos é um paradigma de programação adotado pelo SAP para modernizar o desenvolvimento, antes apenas focado no modelo estruturado (*Forms/Reports*).

Uma **Classe** em Programação Orientada a Objetos (**OOP**) é um modelo que agrupa funcionalidades relacionadas a um contexto especifico. Ela encapsula **atributos** e **métodos** que os objetos criados a partir dela possuirão. É portanto composta por:

- **Atributos:** são as características (variáveis/dados);
- **Métodos:** são as ações/comportamentos (semelhantes a funções). Cada método deve resolver um problema específico (ex: inserir, deletar, Ler);

### Tipos de Classes no SAP

- **Global (SE24):** criada via *Class Builder*. Pode ser reutilizada em qualquer lugar do sistema SAP;
- **Local (SE38):** criada dentro de um programa ou *include*. Seu escopo é limitado ao programa onde foi definida.

### Estrutura de uma Classe Local

Para declarar uma classe local, é necessário definir duas partes fundamentais: a Definição e a Implementação.

- **Definition (Definição):** é onde declaramos a "assinatura" da classe: seus atributos, métodos e a visibilidade (ex: `PUBLIC SECTION`). É o contrato do que a classe possui.

```abap
CLASS Lcl_nome_da_classe DEFINITION.

  PUBLIC SECTION.

    " Declaração de atributos e métodos
    METHODS:
      metodo_um,
      metodo_dois.

ENDCLASS.
```

- **Implementation (Implementação):** é onde escrevemos a lógica real do código ABAP para cada método declarado na definição.

```abap
CLASS Lcl_nome_da_classe IMPLEMENTATION.

  METHOD metodo_um.
    " Lógica do método
  ENDMETHOD.

  METHOD metodo_dois.
    " Lógica do método
  ENDMETHOD.

ENDCLASS.
```

Sintaxe para declarar uma variável (objeto) referenciando uma classe:

```abap
DATA(lo_objeto) = NEW lcl_nome_da_classe( ).
```

### Organização com INCLUDES

Um *Include* é um objeto que permite modularizar o código, utilizados para armazenar trechos de código que compõem um programa maior. Na prática, no do código de uma classe Local, movemos a definição e a implementação da classe para um arquivo separado (ex: `Z_PROGRAMA_INC`) e o referenciamos no programa principal. Isso mantém o código organizado, embora a classe continue sendo Local ao programa que chama o *include*.

Portanto, o uso de *includes* tem por objetivos a **organização** (modularizar o código em blocos), **legibilidade** (manter o programa principal mais legível) e **reutilização** (agrupar trechos similares de código).

Alguns casos de uso, com suas respectivas nomenclaturas, comumente vistos em programas ABAP, seriam *includes* para modularizar:

- **TOP:** Declarações Globais (variáveis, tabelas internas);
- **SCR (screen):** construção da Tela de Seleção;
- **PBO (*process before output*):** Eventos antes de exibir a Tela de Seleção;
- **PAI (*process after input*):** Eventos depois de exibir a Tela de Seleção;
- **F01, F02 ... (*Forms*):** *forms* (sub-rotinas) contendo a lógica de negócio.

## Classes de Mensagem (*Message Classes*)

Classes de Mensagem (***Message Classes***) são objetos do repositório SAP utilizados para armazenar textos de mensagens. Ao invés de escrever o texto da mensagem diretamente no código (*hardcode*), cria-se uma classe contendo IDs numéricos para cada mensagem. Isso permite que o mesmo texto seja reutilizado em diversos programas (funções, classes, relatórios) e, caso seja necessário alterar o texto, a mudança é feita em um único Lugar. Essa centralização, padronização e reutilização de mensagens (*pop-ups*, retornos de função, avisos de erro) dentro do sistema SAP, acaba facilitando a manutenção do código.

### Criação e Manutenção de Classes de Mensagens

Para criar e gerenciar as classes de mensagem utilizamos a transação **SE91** (*Message Maintenance*). Elementos principais:

- **Nomeação:** deve seguir o padrão do cliente (iniciando com `Z` ou `Y`);
- **Mensagens:** dentro da classe, as mensagens são numeradas (000, 001, 002 etc.);
- ***Placeholders* (Parâmetros):** é possível criar mensagens dinâmicas utilizando o caractere `&`; o sistema substituirá esses símbolos pelos valores passados via código.

### Comando MESSAGE e Tipos de Mensagem

No código ABAP, utilizamos o comando `MESSAGE` para disparar as notificações. Existem diferentes tipos de comportamento para as mensagens:

- S (Success) : Mensagem de sucesso (geralmente verde na barra de status).
- W (Warning): Aviso (amarelo). Permite que o usuário continue após confirmar, mas alerta sobre algo.
- E (Error): Erro (vermelho). Interrompe o processamento até que o problema seja corrigido.
- I (Information): Informação. Exibe um pop-up informativo para o usuário.

Exemplo de Sintaxe:

```abap
" Chama a mensagem 000 da classe ZMEUSYS como um erro
MESSAGE e000(ZMEUSYS).
```

### Passagem de Parâmetros na Mensagem

Para preencher os *placeholders* (`&`) definidos na transação SE91, utilizamos a cláusula `WITH`. Isso permite injetar variáveis do sistema ou dados do programa na mensagem.

Exemplo prático:

```abap
" Mensagem 004 definida na SE91: "Usuário &1 criou a Ordem &2."
MESSAGE i004(ZMEUSY) WITH sy-uname '1234'.
" Resultado: "Usuário USER-NAME criou a ordem 1234."
```

### Documentação de Mensagens (*Long Text*)

Criação de **Texto Descritivo** (*Long Text*) para mensagens que não são autoexplicativas. Na transação SE91, ao desmarcar a opção *Self-Explanatory* (Autoexplicativo) e clicar em *Long Text* (Texto descritivo), é possível redigir uma documentação detalhada.

Isso é útil para explicar motivos de erros complexos (ex: Motivo A, B ou C) e instruir o usuário ou consultor funcional sobre como proceder. Essa documentação é exibida quando o usuário clica na ajuda (`F1`) da mensagem.

## Documentação de Objetos no SAP

Inserir documentação diretamente nos objetos criados, como Tabelas Transparentes, Elementos de Dados e Programas. Ao visualizar um objeto (na transação SE11 ou SE80), utiliza-se o menu superior: Go to > Documentation > Change.

### Documentação Avançada com a Transação SE61

Esta ferramenta permite criar documentos mais robustos e vinculá-los aos objetos ABAP (SAPscript: Documentação).

Passo a passo na SE61:

1. Acesse a transação **SE61**;
2. Selecione a classe do documento (classe Word ou General Text para integração com editor de texto);
3. Crie o documento com o mesmo nome do objeto ou um nome identificável;
4. Utilize o editor (que pode simular o MS Word) para criar cabeçalhos, descrições detalhadas e instruções de uso.

### Vinculação de Documentos (Hiperlinks)

Para vincular o documento criado na SE61 dentro da documentação padrão do objeto, utiliza-se uma sintaxe específica de Link do SAPscript. Sintaxe de Vinculação:

```text
<DS: CLASSE : NOME DO DOCUMENTO>Texto do Link
```

- **DS:** indica que é um Link de documentação;
- **CLASSE:** a classe do documento criado na SE61 (ex: WORD);
- **NOME_DO_DOCUMENTO:** o nome técnico dado ao arquivo na SE61.

Ao salvar e ativar, o texto se torna um hiperlink clicável que abre a documentação detalhada.

## Telas de Seleção (Selection Screens) no ABAP

Uma tela de seleção é a interface primária de um relatório ou programa executável, onde o usuário insere os filtros necessários
(parâmetros) para processar os dados.

### Componentes Básicos de Entrada

- **Parameters** (Parâmetros Únicos): o comando `PARAMETERS` cria um campo de entrada para um valor único. É utilizado quando o filtro exige um dado específico, sem intervalos.
  - Sintaxe: `PARAMETERS: p_belnr TYPE bkpf-belnr.`
  - Exemplo prático: filtrar um número de documento contábil específico;
  - `TYPE`: você pode referenciar diretamente um Campo de Tabela ou um Elemento de Dados (ex: BELNR_D ).

- **Select-Options** (Intervalos e Múltiplos Valores): o comando `SELECT-OPTIONS` permite criar filtros complexos, aceitando intervalos (De/Até) e múltiplos valores. Ele gera automaticamente o botão de *Matchcode* (ajuda de pesquisa) e opções de seleção (maior que, menor que,
excluir etc. )
  - Pré-requisito: È necessário declarar a tabela utilizada com o comando `TABLES`;
  - Exemplo prático: filtrar diferentes tipos de documentos (ex: KR, DR, SA);
  - Sintaxe:

  ```abap
  TABLES: bkpf.
  SELECT-OPTIONS: s_blart FOR bkpf-blart.
  ```

  **Obs.:** *Select Options (Ranges)* são tratados como tabelas internas contendo quatro colunas padrão:
  - **SIGN:** Indica inclusão (I) ou exclusão (E);
  - **OPTION:** Operador Lógico (EQ para igual, BT para Between/Entre, GT para maior que, etc.);
  - **LOW:** Valor inicial ou único;
  - **HIGH:** Valor final (usado em intervalos).

### Componentes de Controle e Opções

- **Checkbox:** utilizado para opções booleanas (*true/false*). Retorna `X` se marcado e `vazio` se desmarcado.
  - Sintaxe: `PARAMETERS: p_block AS CHECKBOX.`
  - Uso comum: Ativar Logs detalhados, executar em modo de teste ou filtrar itens bloqueados.

- **Radio Buttons:** permite a seleção de uma única opção dentro de um grupo de alternativas mutuamente exclusivas.
  - Sintaxe:

    ```abap
    PARAMETERS: r_cred RADIOBUTTON GROUP rbg1,
                r_deb RADIOBUTTON GROUP rbg1.
    ```

  - Regra: Todos os botões do mesmo grupo (ex: `rbg1`) funcionam juntos; ao selecionar um, os outros são desmarcados.

- **Listbox** (Caixa de Listagem): cria um menu suspenso (*dropdown*) com opções pré-definidas.
  - Sintaxe: `PARAMETERS: lb_tipo AS LISTBOX VISIBLE LENGTH 30 MODIF ID tip.`
  - Preenchimento de Valores: o Listbox precisa ser populado via código, utilizando a função `VRM_SET_VALUES` (do grupo de funções VRM). O preenchimento deve ocorrer no evento `AT SELECTION-SCREEN OUTPUT` OU `INITIALIZATION`. Veja sintaxe abaixo:

    ```abap
    " Acrescenta os valores para ListBox
    AT SELECTION-SCREEN OUTPUT.
      " define a lista utilizando a estrutura 'vrm_values'
      DATA(lt_values) = VALUE vrm_values(
        ( key = '1' text = 'Arquivo PDF' )
        ( key = '2' text = 'Arquivo Texto' )
        ( key = '3' text = 'Power Point' )
        ( key = '4' text = 'Doc. Word' )
      ).
      " popula a lista utilizando a função `VRM_SET_VALUES`
      call function 'VRM_SET_VALUES'
        exporting
          id              = 'LB_TIPO'
          values          = lt_values
      EXCEPTIONS
        ID_ILLEGAL_NAME  = 1
        OTHERS           = 2.
    ```

### Layout e Estética da Tela

- **Selection Texts** (Textos de Seleção): para exibir nomes amigávies no lugar dos nomes das variáveis (ex: "Documento Contábil" ao invés de P_BELNR)
  - Menu Goto > Text Elements > Selection Texts

- **Ícones**: é possível inserir ícones junto aos textos de seleção utilizando a sintaxe `@xx@`, onde XX é o código do ícone.
  - Dica: Utilize o programa (SE38) `SHOWICON` para visualizar a Lista de ícones disponíveis e seus códigos.

- **Blocks** (Blocos): organizam a tela em seções visuais com molduras, melhorando a usabilidade.
  - Sintaxe:

    ```abap
    SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
      " Declaração dos parâmetros aqui
    SELECTION-SCREEN END OF BLOCK b1.
    ```

  - `text-001` (título do Bloco), definir em: Menu Goto > Text Elements > Text Symbols

- **Quebra de Linha:** pula '*n*' Linhas
  - Sintaxe: `SELECTION-SCREEN SKIP 1.`

### Botões e Eventos

Podemos adicionar botões tanto no corpo da tela quanto na *toolbar* (barra de ferramentas) superior.

- **Pushbutton** (Botão na Tela)
  - Sintaxe: `SELECTION-SCREEN PUSHBUTTON /pos(len) nome_botao USER-COMMAND ucom.`
  - Texto do Botão: definido no evento `INITIALIZATION`.

- **Function Key** (Botão na Toolbar)
  - Sintaxe: `SELECTION-SCREEN FUNCTION KEY 1.`
  - Configuração: requer a declaração `TABLES: sscrfields.`; o texto é atribuído ao campo `sscrfields-functxt_01`;
  - Permite até 5 botões: `KEY 1` até `KEY 5`

- **Tratamento de Eventos:** evento `AT SELECTION-SCREEN` para capturar o clique nos botões; o campo `sscrfields-ucom` conterá o código do comando acionado (ex: `FC01` para toolbar `KEY 1`; ou o nome definido no *Pushbutton*).

Sintaxe de exemplo (1 botão na tela + 2 botões na toolbar):

```abap
TABLES: sscrfields.

" Buttons (Screen) - botões na tela
SELECTION-SCREEN PUSHBUTTON /1(20) p_but1 USER-COMMAND but1.
" Buttons (Toolbar) - botões na barra de ferramentas
SELECTION-SCREEN FUNCTION KEY 1.
SELECTION-SCREEN FUNCTION KEY 2.

INITIALIZATION.
  p_but1 = '@01@ Carrega Aqui'.
  sscrfields-functxt_01 = '@01@ Carrega Aqui'.
  sscrfields-functxt_02 = '@02@ Descarta Aqui'.

AT SELECTION-SCREEN.
  IF sscrfields-ucomm = 'BUT1'.
    MESSAGE 'Clicou no botão da Screen' TYPE 'I'.
  ELSEIF sscrfields-ucomm = 'FC01'.
    MESSAGE 'Clicou no botão FC01 da Toolbar' TYPE 'I'.
  ELSEIF sscrfields-ucomm = 'FC02'.
    MESSAGE 'Clicou no botão FC02 da Toolbar' TYPE 'I'.
  ENDIF.
```

## *Reports* ALV

O **ALV** (*Application List Viewer*) é uma ferramenta padrão do SAP utilizada para a exibição de Listas e Relatórios de forma estruturada. Oferece funcionalidades nativas como filtros, ordenação, somatórios e exportação para Excel.

### Evolução dos Relatórios no SAP

- **Comando WRITE:** método antigo de escrita em tela. Pouco utilizado para relatórios corporativos;
- **ALV** (funções e OOP): o padrão atual para ambientes SAP *ECC*. Implementado via módulos de função ou, preferencialmente, via OOP;
- **SAP Fiori e CDS Views:** no ambiente SAP *S/4HANA*, o conceito de ALV é substituído por aplicativos *Fiori* e *CDS Views*. Embora os ALVs clássicos continuam funcionando no HANA.

### Estrutura de um Report ALV

Um relatório ALV deve seguir quatro elementos fundamentais:

1. Definição dos Filtros (Tela de Seleção) .
2. Seleção de Dados do Banco.
3. Montagem/Tratamento dos Dados.
4. Exibição dos Dados.

#### Passo 1: Tela de Seleção

Tela de seleção utilizando blocos e ícones para melhorar a experiência do usuário.

- **PARAMETERS**;
- **SELECT-OPTIONS**;
- **Ícones:** utilizando códigos de ícones (`@XX@`) nos textos de seleção.

#### Passo 2: Implementação Orientada a Objetos

Ao invés de deixar o código solto (procedural), criar uma Classe Local para encapsular a Lógica, respeitando princípios de responsabilidade única.

- Classe Local `lcl_minha_classe`;
- Método `get_dados`: responsável por realizar o `SELECT` nas tabelas com base nos filtros da tela de seleção e armazenar em uma tabela interna global da classe;
- Método `display_alv`: responsável por configurar e exibir o relatório em tela;
- Método `run` : método orquestrador que chama o `get_dados` e o `display_alv`.

#### Passo 3: Criação do ALV com CL_SALV_TABLE

Uso da classe moderna da SAP para geração de ALVs, a `CL_SALV_TABLE`. O processo de criação envolve:

1. **Factory:** chamada do método estático `cl_salv_table=>factory`, passando a tabela interna com os dados;
2. **Funções (Toolbar):** ativação da barra de ferramentas padrão (botões de filtro, soma etc. ) através do método `get_functions( )->set_all( abap_true )`;
3. **Display:** Chamada do método `display( )` para renderizar o relatório na tela.

### Eventos de Execução

Organizamos a chamada da classe nos eventos corretos do Report:

- **INITIALIZATION:** instanciamos a classe Local;
- **START-OF-SELECTION:** chamamos o método run( ) da instância criada, que dispara a seleção e a exibição.

### ALV - Código de exemplo

```abap
TABLES: sflight.

" Tabelas Utilizadas no exemplo
" SPFLI:   Voos (rotas)
" SFLIGHT: Horarios dos Voos (datas e ocupações específicas)
" SBOOK:   Reservas de Voo (passageiros)
" OBS.: Programa Gerador de dados nas tabelas:
" - Executar via SE38 para popular as tabelas: SAPBC_DATA_GENERATOR

" TELA DE SELEÇÃO
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
PARAMETERS: p_carrid TYPE s_carr_id OBLIGATORY.
SELECT-OPTIONS: s_connid FOR sflight-connid,
                s_fldate FOR sflight-fldate.
SELECTION-SCREEN END OF BLOCK b1.

" Definição da Classe Local
CLASS lcl_voos DEFINITION.
  public section.
    DATA: lt_voos TYPE TABLE OF sflight.

    METHODS: get_voos,
             display_alv,
             run.
ENDCLASS.

" Implementação da Classe Local
CLASS lcl_voos IMPLEMENTATION.

  " SELECIONAR OS DADOS
  METHOD get_voos.
    SELECT *
      FROM sflight
      INTO TABLE lt_voos
      WHERE carrid = p_carrid
        AND connid IN s_connid
        AND fldate IN s_fldate.
  ENDMETHOD.

  " EXIBIÇÃO DOS DADOS
  METHOD display_alv.
    " ex. usando WRITE
*    LOOP AT gt_voos INTO DATA(ls_voo).
*      WRITE: / ls_voo-carrid, ls_voo-connid, ls_voo-fldate.
*    ENDLOOP.

    " ex. usando Classe da SAP para ALV (OOP)
    cl_salv_table=>factory(
      IMPORTING
        r_salv_table = DATA(lo_alv)
      CHANGING
        t_table = lt_voos
    ).

    " Ativar a Toolbar do ALV
    lo_alv->get_functions( )->set_all( abap_true ).

    " Apresenta o ALV na tela
    lo_alv->display( ).
  ENDMETHOD.

  METHOD run.
    get_voos( ).
    display_alv( ).
  ENDMETHOD.

ENDCLASS.

" EVENTOS DE EXECUÇÃO
INITIALIZATION.
  DATA(lo_voos) = NEW lcl_voos( ).

START-OF-SELECTION.
  lo_voos->run( ).
```

### Tópico Avançado: ALV IDA

Conceito de **ALV IDA** (*Integrated Data Access*): trata-se de uma tecnologia otimizada para o **SAP HANA** (classe `CL_SALV_GUI_TABLE_IDA`), que permite exibir grandes volumes de dados diretamente de tabelas ou *CDS Views* sem a necessidade de carregar tudo para a memória interna do programa (tabelas internas), delegando o processamento pesado para o banco de dados.

## Modularização com Forms e Performs

Embora a Programação Orientada a Objetos (OOP) seja o padrão moderno e recomendado para novos desenvolvimentos, o conhecimento sobre a programação estruturada (*procedural*), com o uso de *Forms*, é essencial para a manutenção de programas Legados que ainda operam em muitos ambientes SAP, inclusive após migrações para o banco de dados HANA.

- **Definição:** o `FORM` é um bloco de código modularizado dentro de um programa. O comando `PERFORM` é utilizado para chamar a execução desse bloco;
- **Comparação com Métodos:** assim como os métodos em uma Classe, os *Forms* servem para organizar e reutilizar código. No entanto, eles não possuem o encapsulamento e a estrutura hierárquica da Orientação a Objetos;
- **Escopo e Variáveis:** diferente dos métodos que compartilham atributos da classe, os *Forms* isolados não se comunicam automaticamente. Para compartilhar dados entre eles (como tabelas internas), frequentemente utilizam-se variaveis globais declaradas no topo do programa para serem visiveis por todos os Forms;
Implementação Prática
- **Eventos:** utilizamos os eventos `START-OF-SELECTION` e `END-OF-SELECTION` para orquestrar as chamadas;
- **Passagem de Parametros:** para passar dados para dentro de um *Form* utilizamos a palavra-chave `USING` (equivalente ao parâmetro de importação).

### Código de exemplo: ALV usando *Forms*

Refatoração do programa de ALV acima para utilizar a estrutura de Forms ao invés de Classes:

```abap
TABLES: sflight.

" TELA DE SELEÇÃO
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
PARAMETERS: p_carrid TYPE s_carr_id OBLIGATORY.
SELECT-OPTIONS: s_connid FOR sflight-connid,
                s_fldate FOR sflight-fldate.
SELECTION-SCREEN END OF BLOCK b1.

" Variável Global
DATA: gt_voos TYPE TABLE OF sflight.

" Declaração dos Forms (seria o equivalente aos métodos declarados dentro de uma Classe)

FORM get_voos.

  SELECT *
    FROM sflight
    INTO TABLE gt_voos
    WHERE carrid = p_carrid
      AND connid IN s_connid
      AND fldate IN s_fldate.

  IF gt_voos[] IS INITIAL.
    PERFORM display_message USING 'Não foram encontrados Dados!'.
  ENDIF.

ENDFORM.

FORM display_alv.

  " classe do SAP para ALV
  cl_salv_table=>factory(
    IMPORTING
      r_salv_table = DATA(lo_alv)
    CHANGING
      t_table = gt_voos
  ).

  " Ativar a Toolbar do ALV
  lo_alv->get_functions( )->set_all( abap_true ).

  " Apresenta o ALV na tela
  lo_alv->display( ).

ENDFORM.

FORM display_message USING iv_msg TYPE string. " Form com passagem de parâmetro
  MESSAGE iv_msg TYPE 'S'.
ENDFORM.

" Eventos de Execução

START-OF-SELECTION.
  PERFORM get_voos.

END-OF-SELECTION.
  PERFORM display_alv.
```

## JOBs

Os Jobs são ferramentas para a automação e performance no SAP, permitindo que processos pesados sejam executados em segundo plano (*background*) de forma eficiente sem impactar a produtividade do usuário final.

Distanção entre dois modos de execução:

- **Foreground (Primeiro Plano):** ocorre quando o usuário executa uma transação ou relatório e aguarda o processamento na tela. Há interação direta e o terminal fica ocupado até o fim da execução;
- **Background (Segundo Plano):** a execução ocorre no servidor, sem intervenção ou bloqueio da tela do usuário. É ideal para processos demorados, rotinas automáticas ou processamento de grandes volumes de dados (ex: geração de milhares de pedidos ou relatórios complexos).

Duas transações são essenciais para o gerenciamento de Jobs:

- **SM36:** utilizada para criar e definir Jobs;
- **SM37:** utilizada para monitorar e gerenciar os Jobs existentes.

### Criando um Job (SM36)

A criação de um Job envolve a definição de passos (*steps*) e condições de início:

1. **Definição do Job:** atribuição de um nome para a tarefa;
2. **Steps (Passos):** determinam o que o Job irá executar:
    - um comando externo;
    - um programa externo;
    - um programa ABAP (mais comum);
    - é possível encadear múltiplos passos em um único Job.
3. **Variantes:** ao agendar um programa ABAP que possui tela de seleção, utilizamos *Variants*. Uma variante é um conjunto de parâmetros de seleção pre-salvos, garantindo que o Job execute com os filtros corretos sem intervenção manual.
4. **Condição de Execução (Start Condition):** define quando o Job será iniciado. As opções incluem:
    - **Imediato:** inicia assim que salvo;
    - **Data/Hora:** agendamento para um momento específico;
    - **Periódico:** execução recorrente (ex: a cada hora, diariamente, semanalmente).

### Monitoramento e Resultados (SM37)

Após o agendamento, utilizamos a transação **SM37** para acompanhar o status e os resultados:

- **Status do Job:** indica a situação atual (ex: Liberado, Pronto, Ativo, Concluído ou Cancelado);
- **Spool:** é o local onde o SAP armazena a saída de impressão do Job. Se o programa gera um relatório ALV ou comandos `WRITE`, o resultado ficará acessível através do icone de Spool na SM37;
- **Log do Job:** registra o histórico de execução, incluindo mensagens de sucesso/avisos/erros (pelo comando `MESSAGE`) e informações sobre os passos processados.
