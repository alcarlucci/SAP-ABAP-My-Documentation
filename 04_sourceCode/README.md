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

## ALVs

**Telas de Seleção:**
