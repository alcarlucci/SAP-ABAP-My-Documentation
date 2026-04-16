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

**CHECK:**

```abap
" Dentro de Loops
LOOP AT lt_dados INTO ls_linha.
  CHECK ls_linha-valor > 10. " Só processa se o valor for maior que 10
    
  " O código abaixo só é executado se o CHECK passar (for verdadeiro)
  WRITE: ls_linha-id.
ENDLOOP.
```

```abap
" Fora de Loops
METHOD get_produtos.

  CHECK lt_pedido_item[] IS NOT INITIAL. " Só processa se tiverem registros na tabela lt_pedido_item

  " O código restante do método só é executado se o CHECK passar (for verdadeiro)
  SELECT *
    FROM zfajr_pcx_t2
    INTO TABLE @lt_produtos
    FOR ALL ENTRIES IN @lt_pedido_item
    WHERE id = @lt_pedido_item-produto.

ENDMETHOD.
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
      lt_status TYPE TABLE OF zstatus_t01. " tabela interna para TABELA Status
```

**Comando SELECT (Ler Dados):**

```abap
" Seleciona dados da Tabela no BD (usando * - todas colunas)
select *
  from zstatus_t01
  INTO TABLE @lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - precisa estar na ordem certa das colunas na tabela)
select status, descricao
  from zstatus_t01
  INTO TABLE @lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - qq ordem usando CORRESPONDING FIELDS)
select descricao, status
  from zstatus_t01
  INTO CORRESPONDING FIELDS OF TABLE @lt_status.
```

**Cláusula WHERE e FOR ALL ENTRIES IN (filtrar Dados):**

```abap
" Filtros - Tela de Seleção
TABLES: zpedido_t1.
SELECT-OPTIONS: so_ped FOR zpedido_t1-id.

" Declaração das tabelas internas
DATA: lt_pedido TYPE TABLE OF zpedido_t1,
      lt_pedido_item TYPE TABLE OF zpedido_item_t2,

" Seleciona os Pedidos para o filtro em so_ped (SELECT-OPTIONS)
SELECT *
  FROM zpedido_t1
  INTO TABLE @lt_pedido
  WHERE id IN @so_ped.

" Verifica se achou Vendas
IF lt_pedido[] IS NOT INITIAL.

  " Seleciona os Itens do Pedido fazendo JOIN com Pedido (já selecionado)
  SELECT *
    FROM zpedido_item_t2
    INTO TABLE @lt_pedido_item
    FOR ALL ENTRIES IN @lt_pedido
    WHERE id = @lt_pedido-id.

ENDIF.
```

**Comando MODIFY (Inserir ou Atualizar):**

```abap
" insere/atualiza uma única linha na tabela (a partir de uma estrutura)

DATA: ls_status TYPE zstatus_t01.

ls_status-status = 'A'.
ls_status-descricao = 'Teste A'.

MODIFY zstatus_t01 FROM ls_status.

" (OU)
" OPÇÃO MODERNA: utilizando o modo 'inline'

DATA(ls_status) = VALUE zstatus_t01(
  status = 'A'
  descricao = 'Teste A'
).

MODIFY zstatus_t01 FROM ls_status.
```

```abap
" insere/atualiza várias linha na tabela (a partir de uma tabela interna)

DATA: ls_status TYPE zstatus_t01.
      lt_status TYPE TABLE OF zstatus_t01.

ls_status-status = 'A'.
ls_status-descricao = 'Teste A'.
APPEND ls_status TO lt_status.

ls_status-status = 'B'.
ls_status-descricao = 'Teste B'.
APPEND ls_status TO lt_status.

MODIFY zstatus_t01 FROM TABLE lt_status.

" (OU)
" OPÇÃO MODERNA: utilizando o modo 'inline'

" Declaração do Table Type
TYPES tt_status TYPE TABLE OF zstatus_t01 WITH DEFAULT KEY.

DATA(lt_status) = VALUE tt_status(
  ( status = 'A' descricao = 'Teste A' )
  ( status = 'B' descricao = 'Teste B' )
).

MODIFY zstatus_t01 FROM TABLE lt_status.
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

" Parameters (Parâmetros Únicos) - TYPE p/ tabela-campo ou elemento de dados
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

## ALVs (*Application List Viewer*)

**ALV - ex.1: visão única (analítico) - usando OOP:**

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
             display_message IMPORTING VALUE(iv_msg) TYPE string,
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

    IF lt_voos[] IS INITIAL.
      display_message( 'Não foram encontrados Dados!' ).
    ENDIF.

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

  METHOD display_message.
    MESSAGE iv_msg TYPE 'S'.
  ENDMETHOD.

  METHOD run.
    get_voos( ).
    display_alv( ).
  ENDMETHOD.

ENDCLASS.

" EVENTOS DE EXECUÇÃO
INITIALIZATION.
  DATA(lo_voos) = NEW lcl_voos( ). " instancia a classe Local

START-OF-SELECTION.
  lo_voos->run( ). " chama o método run( ) da instância criada
```

**ALV - ex.1: visão única (analítico) - usando *Forms*:**

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
" (utiliza os eventos START-OF-SELECTION e END-OF-SELECTION para orquestrar as chamadas)

START-OF-SELECTION.
  PERFORM get_voos.

END-OF-SELECTION.
  PERFORM display_alv.
```

**ALV - ex.2: multi visões (analítico/sintético) - usando OOP:**

**1. Classe para visão analítica (criar include  `zalvrp_002_analitico`):**

```abap
" Classe local para Visão Analítica
CLASS lcl_voos_analitica DEFINITION.

  PUBLIC SECTION.

    " Definição dos tipos Range para parâmetros SELECT-OPTIONS
    TYPES: tr_connid TYPE RANGE OF sflight-connid,
           tr_fldate TYPE RANGE OF sflight-fldate.

    DATA: lt_voos TYPE TABLE OF sflight.

    METHODS: get_voos IMPORTING VALUE(it_carrid) TYPE s_carr_id
                                VALUE(it_connid) TYPE tr_connid
                                VALUE(it_fldate) TYPE tr_fldate,
             display_alv.

ENDCLASS.

CLASS lcl_voos_analitica IMPLEMENTATION.

  METHOD get_voos.

    SELECT *
      FROM sflight
      INTO TABLE lt_voos
      WHERE carrid = it_carrid
        AND connid IN it_connid
        AND fldate IN it_fldate.

  ENDMETHOD.

  METHOD display_alv.
    " usando Classe da SAP para ALV (OOP)
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

ENDCLASS.
```

**2. Classe para visão sintética (criar include  `zalvrp_002_sintetico`):**

```abap
" Classe local para Visão Sintética
CLASS lcl_voos_sintetica DEFINITION.

  PUBLIC SECTION.

    " Definição dos tipos Range para parâmetros SELECT-OPTIONS
    TYPES: tr_connid TYPE RANGE OF sflight-connid,
           tr_fldate TYPE RANGE OF sflight-fldate.

    " Tipo de Exibição do ALV sintetico (estrutura)
    TYPES: BEGIN OF ty_alv,
            carrid TYPE s_carr_id,
            connid TYPE s_conn_id,
            paymentsum TYPE s_sum,
           END OF ty_alv.

    DATA: lt_voos TYPE TABLE OF sflight,
          lt_alv  TYPE TABLE OF ty_alv.

    METHODS: get_voos IMPORTING VALUE(it_carrid) TYPE s_carr_id
                                VALUE(it_connid) TYPE tr_connid
                                VALUE(it_fldate) TYPE tr_fldate,
             display_alv.

ENDCLASS.

CLASS lcl_voos_sintetica IMPLEMENTATION.

  METHOD get_voos.

    " Resolver somatorio via LOOP e COLLECT
*    SELECT carrid, connid, paymentsum
*      FROM sflight
*      INTO CORRESPONDING FIELDS OF TABLE @lt_voos
*      WHERE carrid = @it_carrid
*        AND connid IN @it_connid
*        AND fldate IN @it_fldate.
*
*    DATA: soma_total TYPE p DECIMALS 2.
*    soma_total = 0.
*
*    LOOP AT lt_voos INTO DATA(ls_voo).
*      DATA(ls_alv) = CORRESPONDING ty_alv( ls_voo ).
*      soma_total = soma_total + ls_alv-paymentsum.
*      COLLECT ls_alv INTO lt_alv.
*    ENDLOOP.
*
*    APPEND VALUE ty_alv(
*      paymentsum = soma_total
*    ) TO lt_alv.

    " Resolver somatorio via SELECT GROUP BY (mais indicado nesse caso)
    SELECT carrid, connid, SUM( paymentsum ) as paymentsum
      FROM sflight
      INTO TABLE @lt_alv
      WHERE carrid = @it_carrid
        AND connid IN @it_connid
        AND fldate IN @it_fldate
      GROUP BY carrid, connid.

  ENDMETHOD.

  METHOD display_alv.
    " usando Classe da SAP para ALV (OOP)
    cl_salv_table=>factory(
      IMPORTING
        r_salv_table = DATA(lo_alv)
      CHANGING
        t_table = lt_alv
    ).

    " Ativar a Toolbar do ALV
    lo_alv->get_functions( )->set_all( abap_true ).

    " Apresenta o ALV na tela
    lo_alv->display( ).
  ENDMETHOD.

ENDCLASS.
```

**3. Programa ALV multi visões (`zalvrp_002`):**

```abap
" Duas Visões:
" - Analítica: Dados brutos globais
" - Sintética: Resumos / padrões / agrupamentos

TABLES: sflight.

INCLUDE: zalvrp_002_analitico,
         zalvrp_002_sintetico.

" TELA DE SELEÇÃO
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
PARAMETERS: p_carrid TYPE s_carr_id OBLIGATORY.
SELECT-OPTIONS: s_connid FOR sflight-connid, "s_conn_id
                s_fldate FOR sflight-fldate. "s_date
SELECTION-SCREEN SKIP 1.
PARAMETERS: rb_anli RADIOBUTTON GROUP rbg1,
            rb_sint RADIOBUTTON GROUP rbg1.
SELECTION-SCREEN END OF BLOCK b1.

" Eventos de Execução

INITIALIZATION.
  DATA(lo_voos_analitica) = NEW lcl_voos_analitica( ).
  DATA(lo_voos_sintetica) = NEW lcl_voos_sintetica( ).

START-OF-SELECTION.
  IF rb_anli = 'X'.
    lo_voos_analitica->get_voos( it_carrid = p_carrid
                                 it_connid = s_connid[]
                                 it_fldate = s_fldate[] ).
  ELSE.
    lo_voos_sintetica->get_voos( it_carrid = p_carrid
                                 it_connid = s_connid[]
                                 it_fldate = s_fldate[] ).
  ENDIF.

END-OF-SELECTION.
  IF rb_anli = 'X'.
    lo_voos_analitica->display_alv( ).
  ELSE.
    lo_voos_sintetica->display_alv( ).
  ENDIF.
```

## Outros Códigos
