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

Sintaxe para declarar uma variável (objeto) referenciando uma clasee:

```abap
DATA(lo_objeto) = NEW lcl_nome_da_classe( ).
```

### Organização com INCLUDES

À medida que o código da classe Local cresce, o programa principal pode ficar extenso e difícil de Ler. Para resolver isso, utilizamos
`Includes`. Um *Include* é um objeto que permite modularizar o código. Na prática, movemos a definição e a implementação da classe para um arquivo separado (ex: `Z_PROGRAMA_INC`) e o referenciamos no programa principal. Isso mantém o código organizado, embora a classe continue sendo Local ao programa que chama o *include*.

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
MESSAGE e000 (ZMEUSYS).
```

### Passagem de Parâmetros na Mensagem

Para preencher os *placeholders* (`&`) definidos na transação SE91, utilizamos a cláusula `WITH`. Isso permite injetar variáveis do sistema ou dados do programa na mensagem.

Exemplo prático:

```abap
" Mensagem 004 definida na SE91: "Usuário &1 criou a Ordem &2."
MESSAGE i004(ZMEUSY) WITH sy-uname '1234' .
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

  - `text-001` (título do Bloco), definir em: Meun Goto > Text Elements > Text Symbols

- **Quebra de Linha:** pula '*n*' Linhas
  - Sintaxe: `SELECTION-SCREEN SKIP 1.`

### Botões e Eventos

Podemos adicionar botões tanto no corpo da tela quanto na *toolbar* (barra de ferramentas) superior.

- **Pushbutton** (Botão na Tela)
  - Sintaxe: `SELECTION-SCREEN PUSHBUTTON /pos(len) nome_botao USER-COMMAND ucom.`
  - Texto do Botão: definido no evento `INITIALIZATION`.

- **Function Key** (Botão na Toolbar)
  - Sintaxe: `SELECTION-SCREEN FUNCTION KEY 1.`
  - Configuração: requer a declaração `TABLES: sscrfields.`; o texto e atribuído ao campo `sscrfields-functxt_01`;
  - Permite até 5 botões: `KEY 1` até `KEY 5`

- **Tratamento de Eventos:** evento `AT SELECTION-SCREEN` para capturar o clique nos botões; o campo `sscrfields-ucom` conterá o código do comando acionado (ex: `FC01` para toolbar `KEY 1` ou o nome definido no *Pushbutton*).

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
