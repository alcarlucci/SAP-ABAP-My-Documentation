# SAP ABAP - *Source Code* (guia rápido)

Guia rápido em trechos de código na linguagem ABAP.

## Variáveis e Constantes

**Variáveis - Declaração:**

```abap
DATA: lv_char TYPE c.
DATA: lv_string TYPE string.
DATA: lv_int TYPE i.
DATA: lv_decimal TYPE p DECIMALS 2.
DATA: lv_data TYPE dats.
DATA: lv_hora TYP uzeit.
```

**Variáveis (inline) - declaração já atribuindo valor:**

```abap
DATA(lv_char) = 'M'.
DATA(lv_string) = 'Hello World !!!'.
DATA(lv_int) = 2.
DATA(lv_decimal) = '2.50'.
DATA(lv_data) = sy-datum.
DATA(lv_hora) = sy-uzeit.
```

**Variáveis `sy` (sistema):**

```abap
WRITE: / 'Data atual do sistema:', sy-datum.
WRITE: / 'Hora atual do sistema:', sy-uzeit.
WRITE: / 'Dia da Semana (numero):', sy-fdayw.
WRITE: / 'Usuário Logado:', sy-uname.
WRITE: / 'ID do sistema:', sy-sysid.
WRITE: / 'Nome do programa em execução:', sy-cprog.
WRITE: / 'Título do programa em execução:', sy-title.
WRITE: / 'Sistema de Banco de Dados em uso:', sy-dbsys.
WRITE: / 'Nome do Servidor da aplicação:', sy-host.
```

**Constantes:**

```abap
CONSTANTS: valor_pi TYPE p DECIMALS 2 VALUE '3.1415',
           type_error TYPE c VALUE 'E'.
```

## Condicionais

**IF e ELSE:**

```abap
DATA(opcao) = 1.

IF opcao = 1.
  WRITE: / 'Opção é 1'.
ELSE.
  WRITE: / 'Opção não e 1'.
ENDIF.

IF opcao >= 1 AND opcao <= 3.
  WRITE: / 'Dentro das opções'.
ENDIF.
```

**CASE:**

```abap
DATA(opcao) = 1.

CASE opcao.
  WHEN 1.
    WRITE: / 'Opção é 1'.
  WHEN 2.
    WRITE: / 'Opção é 2'.
  WHEN 3.
    WRITE: / 'Opção é 3'.
  WHEN OTHERS.
    WRITE: / 'Fora das opções'.
ENDCASE.
```

## Estruturas de Repetição

**LOOP:**

```abap
TYPES: tt_inteiro TYPE TABLE OF i WITH DEFAULT KEY.
DATA(lt_inteiros) = VALUE tt_inteiro( ( 1 ) ( 2 ) ( 3 ) ).

LOOP AT lt_inteiros INTO DATA(ls_inteiro).
  WRITE: / 'Inteiro: ', ls_inteiro.
ENDLOOP.
```

**DO:**

```abap
DATA: n TYPE i VALUE '1'.

DO 10 TIMES.
  WRITE: / '- ', n.
  n = n + 1.
ENDDO.
```

## Estruturas e Tabelas (internas)

**Estrutura:**

```abap
TYPES: BEGIN OF ty_cliente,
         nome      TYPE string,
         data_nasc TYPE dats,
         email     TYPE string,
       END OF ty_cliente.

DATA: ls_cliente TYPE ty_cliente.

ls_cliente-nome = 'Andre Carlucci'.
ls_cliente-data_nasc = '19761129'.
ls_cliente-email = 'meuemail@gamil.com'.
```

**Tabela (para o tipo da estrutura):**

```abap
DATA: lt_clientes TYPE TABLE OF ty_cliente.
```

**Tabelas - comandos básicos:**

```abap
" incluir dados na tabela clientes a partir da variável (estrutura)
APPEND ls_cliente TO lt_clientes.

" incluir diretamente dados na tabela clientes
APPEND VALUE ty_cliente(
  nome = 'Andre Carlucci'
  data_nasc = '19761129'
  email = 'meuemail@gamil.com'
) TO lt_clientes.

" Acesso Direto pelo índice (Table Expression): coluna "Nome" da linha "1"
WRITE: / lt_clientes[ 1 ]-nome.

" Ler uma linha de uma Tabela (pelo INDEX ou KEY)
READ TABLE lt_clientes INTO DATA(rd_linha) INDEX 1.
READ TABLE lt_clientes INTO ls_cliente WITH KEY nome = 'Andre'.

" Mover valores de uma estrutura para outra
MOVE-CORRESPONDING ls_cliente TO ls_cliente2.

" Limpar valores das variáveis
CLEAR lt_cliente2.
```

**Declarar Tabela atribuindo valores (*inline*):**

```abap
" Declaração do Table Type
TYPES tt_clientes TYPE TABLE OF ty_cliente WITH EMPTY KEY.

" Declaração inline da Tabela Clientes já atribuindo valores
DATA(lt_clientes) = VALUE tt_clientes(
  ( nome = 'Andre' data_nasc = '19761129' email = 'andre@gmail.com' )
  ( nome = 'Lucas' data_nasc = '20121227' email = 'lucas@gmail.com' )
  ( nome = 'Carla' data_nasc = '19760123' email = 'carla@gmail.com' )
).
```

**Percorrer Tabela (`LOOP`) e exibir na tela (`WRITE`):**

```abap
WRITE: / 'Tabela de Clientes:'.
LOOP AT lt_clientes INTO DATA(ls_cliente).
  WRITE: / ls_cliente-nome, ' - ', ls_cliente-data_nasc, ' - ', ls_cliente-email.
ENDLOOP.
```

**CORRESPONDING (mover os campos coincidentes) e COLLECT (agrupar):**

```abap
LOOP AT lt_voos INTO DATA(ls_voo).
    DATA(ls_alv) = CORRESPONDING ty_alv( ls_voo ).
    COLLECT ls_alv INTO lt_alv.
ENDLOOP.
```

## Tabelas Transparente (Banco de Dados)

**Declaração para tabela tranparente:**

```abap
DATA: ls_status TYPE zstatus_t01, " estrutura para uma linha da Tabela Status
      lt_status TYPE TABLE OF zstatus_t01. " estrutura para TABELA Status
```

**Comando SELECT (Ler Dados):**

```abap
" Seleciona dados da Tabela no BD (usando * - todas colunas)
select *
  from zstatus_t01
  INTO TABLE lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - precisa estar na ordem certa das colunas na tabela)
select status descricao
  from zstatus_t01
  INTO TABLE lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - qq ordem usando CORRESPONDING FIELDS)
select descricao status
  from zstatus_t01
  INTO CORRESPONDING FIELDS OF TABLE lt_status.
```

**Comando MODIFY (Inserir ou Atualizar):**

```abap
ls_status-status = 'A'.
ls_status-descricao = 'Teste A'.

MODIFY zstatus_t01 FROM ls_status.
```

**Comando DELETE (Apagar):**

```abap
"exclui os registro(s) dentro da condição WHERE
delete from zstatus_t01
where status = 'A'.

" ATENÇÃO: exclui TODOS os registros da tabela (sem WHERE)"
delete from zstatus_t01.
```

## Classes e OOP no ABAP

**Declaração - Classe Local:**

```abap
" Definition: "assinatura" da classe
CLASS lcl_minha_classe DEFINITION.

  PUBLIC SECTION.

    " Declaração de atributos e métodos
    METHODS:
      metodo_um,
      metodo_dois.

ENDCLASS.

" Implementation: lógica em ABAP dos métodos declarados na definição.
CLASS lcl_minha_classe IMPLEMENTATION.

  METHOD metodo_um.
    " Lógica do método
  ENDMETHOD.

  METHOD metodo_dois.
    " Lógica do método
  ENDMETHOD.

ENDCLASS.
```

**Declaração de uma variável (objeto) referenciando a Classe:**

```abap
DATA(lo_minha_classe) = NEW lcl_minha_classe( ).
```

## Comando MESSAGE e MESSAGE CLASSES

```abap
" Comando MESSAGE para disparar as notificações
MESSAGE 'Olá Dev: sucesso!' TYPE 'S'. " Success: mensagem de sucesso
MESSAGE 'Olá Dev: aviso!' TYPE 'W'. " Warning: aviso sobre algo e continua
MESSAGE 'Olá Dev: erro!!' TYPE 'E'. " Error: alerta e Interrompe o processamento
MESSAGE 'Olá Dev: informação!' TYPE 'I'. " Information: exibe um pop-up informativo

" Message Classes (Classes de Mensagem): criada na SE91
" Chama mensagens (000; 001; 002 ...) da classe ZMEUSYS_MSG
MESSAGE s000(zmeusys_msg). " Success
MESSAGE e001(zmeusys_msg). " Error
MESSAGE i002(zmeusys_msg). " Information

" Passagem de Parâmetros na Message Classe - placeholder "&"
" Mensagem 004 definida na zmeusys_msg (SE91:) "Usuário &1 criou a Ordem &2."
MESSAGE i004(zmeusys_msg) WITH sy-uname '1234'.
" Resultado: "Usuário USER-NAME criou a ordem 1234."
```

## Telas de Seleção (Selection Screens)

**Componentes básicos de Seleção:**

```abap
TABLES: sflight. " tabela referenciada no filtro SELECT-OPTIONS

" Parameters (Parâmetros Únicos) - TYPE p/ elemento de dados
PARAMETERS: p_carrid TYPE s_carr_id. " parâmetro não obrigatório
PARAMETERS: p_carrid TYPE s_carr_id OBLIGATORY. " parâmetro obrigatório

" Select-Options (Intervalos/ranges e Múltiplos Valores) - FOR p/ tabela-campo
SELECT-OPTIONS: s_connid FOR sflight-connid,
                s_fldate FOR sflight-fldate.

```

**Checkbox (true/false) - retorna `X` ou `vazio`:**

```abap
PARAMETERS: cx_bloq AS CHECKBOX.
```

**Radio Buttons (uma opção em um grupo):**

```abap
PARAMETERS: rb_cred RADIOBUTTON GROUP rbg1,
            rb_debt RADIOBUTTON GROUP rbg1.
```

**Listbox (menu suspenso/*dropdown*):**

```abap
" declaração da Listbox
PARAMETERS: lb_tipo AS LISTBOX VISIBLE LENGTH 30 MODIF ID tip.

" preenchimento dos valores da Listbox (evento AT SELECTION-SCREEN OUTPUT)
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

**Blocks (organiza a tela em seções com molduras):**

```abap
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
  " Declaração dos parâmetros aqui
SELECTION-SCREEN END OF BLOCK b1.

" text-001 (título do Bloco): Menu Goto > Text Elements > Text Symbols
```

**Quebra de Linha (pula *n* linhas):**

```abap
SELECTION-SCREEN SKIP 1. " pula 1 linha
SELECTION-SCREEN SKIP 2. " pula 2 linhas ...
```

**Botões - Pushbutton (Tela); Function Key (Toolbar):**

```abap
TABLES: sscrfields. "tabela para botões Function Key (toolbar)

" Buttons (Screen) - botões na tela
SELECTION-SCREEN PUSHBUTTON /1(20) p_but1 USER-COMMAND but1.

" Buttons (Toolbar) - botões na barra de ferramentas
SELECTION-SCREEN FUNCTION KEY 1.
SELECTION-SCREEN FUNCTION KEY 2.

" defini o rótulo dos botões
INITIALIZATION.
  p_but1 = '@01@ Carrega Aqui'.
  sscrfields-functxt_01 = '@01@ Carrega Aqui'.
  sscrfields-functxt_02 = '@02@ Descarta Aqui'.

" evento para capturar o clique nos botões
AT SELECTION-SCREEN.
  IF sscrfields-ucomm = 'BUT1'.
    MESSAGE 'Clicou no botão da Screen' TYPE 'I'.
  ELSEIF sscrfields-ucomm = 'FC01'.
    MESSAGE 'Clicou no botão FC01 da Toolbar' TYPE 'I'.
  ELSEIF sscrfields-ucomm = 'FC02'.
    MESSAGE 'Clicou no botão FC02 da Toolbar' TYPE 'I'.
  ENDIF.
```

## ALVs

## Outros Códigos
