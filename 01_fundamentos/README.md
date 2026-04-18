# SAP ABAP - Fundamentos

Fundamentos da linguagem ABAP

## SAP GUI

- transação *SE38*: criar/acessar programas em ABAP
- prefixos em nomes de programas
  - z: programas que serão utilizados pelo cliente final
  - y: programas que não serão utilizados pelo cliente final (testes etc)
- Novo programa (create)
  - Title: título do programa
  - Type: tipo; ex.: **Executable Program**
  - Save > Local Object

### Dicas de Produtividade (atalhos) no editor ABAP (*SE38*)

- **CTRL + D:** Duplica a linha atual no editor de código;
- **CTRL + Clique:** Seleciona a palavra inteira onde o cursor está posicionado;
- **Comentários:** Segundo as boas práticas (*Clean Code*), prefira utilizar aspas duplas (`"`) para comentários, pois permitem comentar na mesma linha do código ou em linhas indentadas, diferentemente do asterisco (`*`) que deve estar na primeira coluna;
- **Documentação (F1):** utilize a tecla `F1` sobre um comando para ver a documentação oficial da SAP e exemplos de sintaxe;
- **Code Templates:** autopreenchimento de modelos de código;
  - no editor ABAP, digite a palavra-chave do template (ex: `if`, `case`, `loop`);
  - a funcionalidade de *Code Completion* mostrará sugestões com o símbolo `§`;
  - para inserir o *template* pressione `Ctrl + Enter`.

### Limpeza de Cache do HANA/SAP

Alterações na SE11 podem não refletir imediatamente na SE16 devido ao buffer. Para contornar isso, utiliza-se um programa para Limpar o buffer **ALV/Dicionário**. O nome técnico padrão do programa para Limpeza de buffer de ALV/Field Catalog geralmente é `BALVBUFDEL` ou comandos de sistema equivalentes.

## Transações

As Transações funcionam como atalhos de navegação. Cada transação possui um **Código de Transação** (*T-Code*) único que, ao ser digitado na caixa de comando, direciona o usuário para uma tela específica, seja ela um relatório, uma configuração ou um ambiente de desenvolvimento.

### Principais Transações

- **SE38** (*ABAP Editor*): criar/acessar programas em ABAP;
- **SE09** ou **SE10** (*Transport Organizer*): realizar a gestão das Requests;
- **SE11** (*ABAP Dictionary*): Dicionário de Dados ABAP;
- **SE80** (*Object Navigator*): ambiente central de desenvolvimento - criar/gerenciar pacotes e seus objetos hierarquicamente;
- **SE16**; **SE16N**; **SE16H** (*Data Brower*): navegador do banco de dados - visualização/edição dos dados das tabelas do BD;
- **SM30** (*Edit Table Views*): criação de interfaces para visualização e manipulação de dados de tabelas e views (visões);
- **SE24** (*Class Builder*): criação e manutenção de Classes (OOP);
- **SE37** (*Function Builder*): criação e testes de Módulos de Função; configuração de *Remote Call Functions* (**RFC**) para criação de APIs;
- **SM36** (*Define Job*): criação de Jobs (processos executados em segundo plano/*background* - *server side*);
- **SM37** (*Simple Job Selection*): monitorar a execução e visualizar os logs (*spools*) dos Jobs;
- **SM66** (*Work Processes*): exibe os processos em execução no servidor SAP identificando os usuários;
- **SM12** (*Select Lock Entries*): monitora bloqueios de tabelas/registros no Banco de Dados;
- **DBACOCKPIT** (*DBA Cockpit - System Configuration Maintenance*): painel de administração do Banco de Dados;
- **SAT** (*Runtime Analysis*): análise de performance na execução de um programa;
- **SE61** (*Edit Documents*): criar e editar arquivos de documentações para serem linkados na documentação dos objetos no SAP;
- **SE91** (Message Maintenance): criação e manutenção de Classes de Mensagens;
- **SE93** (*Maintain Transaction*): criação/exibição de Transações.

## Variáveis

### Inteiro

```abap
DATA: num1 TYPE i,
      num2 TYPE i.

num1 = 10.
num2 = 20.
```

### Char - texto de 1 posição

```abap
DATA: sexo TYPE c.
sexo = 'M'.
```

### String - texto 256 caracteres

```abap
DATA: mensagem TYPE string.
mensagem = 'Olá Mundo!'.
```

### Date - data

```abap
DATA: aniversario TYPE dats,
      data_atual  TYPE dats.

* formato aaaammdd
aniversario = '19761129'.
data_atual = sy-datum.

WRITE: / 'Data aniversário: ', aniversario.
WRITE: / 'Data atual: ', data_atual.
```

### Time - hora

```abap
DATA: hora_aula  TYPE uzeit,
      hora_atual TYPE uzeit.

* formato hhmmss
hora_aula = '195500'.
hora_atual = sy-uzeit.

WRITE: / 'Hora da aula: ', hora_aula.
WRITE: / 'Hora atual: ', hora_atual.
```

### Decimal - números com casas decimais

```abap
DATA: valor TYPE p DECIMALS 2.
valor = '110.50'.
```

### Variáveis SY

O SAP possui uma estrutura global chamada `sy` que contém informações do sistema em tempo de execução:

- `sy-datum`: Data atual do sistema;
- `sy-uzeit`: Hora atual do sistema;
- `sy-uname`: Usuário Logado;
- `sy-sysid`: ID do sistema;
- `sy-cprog`: Nome do programa em execução;
- `sy-title`: Título do programa.

## Constantes

Um valor que não pode ser modificado

```abap
CONSTANTS: valor_pi TYPE p DECIMALS 2 VALUE '3.1415',
           type_error TYPE c VALUE 'E'.
```

## Estruturas

É um tipo de dado complexo que agrupa campos relacionados (componentes) sob um único nome, como um registro.

```abap
TYPES: BEGIN OF ty_cliente,
         nome      TYPE string,
         data_nasc TYPE dats,
         email     TYPE string,
       END OF ty_cliente.

DATA: cliente TYPE ty_cliente.

cliente-nome = 'Andre Carlucci'.
cliente-data_nasc = '19761129'.
cliente-email = 'meuemail@gamil.com'.

WRITE: / 'Cliente: ', cliente-nome,
       / 'Data Nasc.: ', cliente-data_nasc,
       / 'E-mail: ', cliente-email.
```

## Tables

Tabelas de linhas para uma estrutura definida.

No exemplo abaixo usando a estrutura definida no tópico anterior `ty_cliente` e variável `cliente` dessa estrutura.

```abap
DATA: clientes TYPE TABLE OF ty_cliente.

" incluir dados na tabela clientes a partir da variável
APPEND cliente TO clientes.

" incluir diretamente dados na tabela clientes
APPEND VALUE ty_cliente(
  nome = 'Andre Carlucci'
  data_nasc = '19761129'
  email = 'meuemail@gamil.com'
) TO clientes.
```

## Condicionais

### IF e ELSE

```abap
DATA: opcao TYPE i.
opcao = 1.

IF opcao = 1.
  WRITE: / 'Opção é 1'.
ELSE.
  WRITE: / 'Opção não e 1'.
ENDIF.

IF opcao >= 1 AND opcao <= 3.
  WRITE: / 'Dentro das opções'.
ENDIF.
```

### CASE

```abap
DATA opcao TYPE i.
opcao = 1.

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

### CHECK

O comando `CHECK` no ABAP funciona como um condicional.

- **condição verdadeira:** o programa continua normalmente;
- **condição falsa:** o comando interrompe a execução do bloco atual (como um loop ou sub-rotina).

Baseado no contexto - varia dependendo de onde ele é utilizado no código:

- **Dentro de Loops** (`LOOP`; `DO`):
  - age como um `CONTINUE` condicional;
  - se for falsa, o `CHECK` pula o restante do código dentro do loop para aquela iteração e inicia a próxima iteração.
  
  ```abap
  LOOP AT lt_dados INTO ls_linha.
    CHECK ls_linha-valor > 10. " Só processa se o valor for maior que 10
    
    " O código abaixo só é executado se o CHECK passar (for verdadeiro)
    WRITE: ls_linha-id.
  ENDLOOP.
  ```

- **Fora de Loops** (Métodos; FMs; Sub-rotinas):
  - age como um `RETURN` condicional;
  - se for falsa, o `CHECK` sai do bloco de processamento atual, ignorando o código subsequente dentro daquele método ou formulário.
  
  ```abap
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

## Estruturas de repetição (loop)

### LOOP

Percorrer linhas de uma tabela.

```abap
TYPES: ty_inteiro TYPE TABLE OF i WITH DEFAULT KEY.
DATA(tab_inteiros) = VALUE ty_inteiro( ( 1 ) ( 2 ) ( 3 ) ).

LOOP AT tab_inteiros INTO DATA(linha).
  WRITE: / 'Linha: ', linha.
ENDLOOP.
```

### DO

Percorrer *n* vezes.

```abap
DATA: n TYPE i VALUE '1'.

DO 10 TIMES.
  WRITE: / 'DO - ', n.
  n = n + 1.
ENDDO.
```

## Tabelas - comandas básicos

### READ TABLE

Ler uma linha de uma Tabela.

```abap
READ TABLE tab_inteiros INTO DATA(rd_linha) INDEX 1.
WRITE: / 'Read Table - index 1: ', rd_linha.

READ TABLE tab_inteiros INTO rd_linha INDEX 3.
WRITE: / 'Read Table - index 3: ', rd_linha.

READ TABLE clientes INTO cliente WITH KEY nome = 'Lucas'.
WRITE: / 'Read Table - cliente Lucas: ',
       / cliente-nome,
       / cliente-data_nasc,
       / cliente-email.
```

### MOVE-CORRESPONDING

Mover valores de uma estrutura para outra.

```abap
DATA: cliente2 TYPE ty_cliente.

MOVE-CORRESPONDING cliente TO cliente2.
WRITE: / 'cliente -> cliente 2: ', cliente2-nome, cliente2-data_nasc, cliente2-email.
```

### CORRESPONDING e COLLECT

- `CORRESPONDING`: mover os campos coincidentes para um estrutura de trabalho;
- `COLLECT` (agrupar): verifica na tabela se já existe uma linha com a mesma chave - se existir: soma os campos numéricos; se não: insere uma nova linha

```abap
TYPES: BEGIN OF ty_alv,
         carrid TYPE s_carr_id,
         connid TYPE s_conn_id,
         paymentsum TYPE s_sum,
       END OF ty_alv.

DATA: lt_voos TYPE TABLE OF sflight,
      lt_alv  TYPE TABLE OF ty_alv.

SELECT carrid, connid, paymentsum
  FROM sflight
  INTO CORRESPONDING FIELDS OF TABLE @lt_voos
  WHERE carrid = @it_carrid
    AND connid IN @it_connid
    AND fldate IN @it_fldate.

LOOP AT lt_voos INTO DATA(ls_voo).
    DATA(ls_alv) = CORRESPONDING ty_alv( ls_voo ).
    COLLECT ls_alv INTO lt_alv.
ENDLOOP.
```

### CLEAR - limpar valores das variáveis

```abap
CLEAR cliente2.
WRITE: / 'CLEAR cliente2: ', cliente2-nome.
```
