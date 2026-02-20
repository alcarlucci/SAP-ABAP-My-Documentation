# SAP ABAP - Fundamentos

Fundamentos da linguagem ABAP

## SAP GUI

- transação *se38*: criar/acessar programas em ABAP
- prefixos em nomes de programas
  - z: programas que serão utilizados pelo cliente final
  - y: programas que não serão utilizados pelo cliente final (testes etc)
- Novo programa (create)
  - Title: título do programa
  - Type: tipo; ex.: **Executable Program**
  - Save > Local Object

### Dicas de Produtividade (Atalhos)

- **CTRL + D:** Duplica a linha atual no editor de código.
- **CTRL + Clique:** Seleciona a palavra inteira onde o cursor está posicionado.
- **Comentários:** Segundo as boas práticas (*Clean Code*), prefira utilizar aspas duplas (`"`) para comentários, pois permitem comentar na mesma linha do código ou em linhas indentadas, diferentemente do asterisco (`*`) que deve estar na primeira coluna.

### Transações principais

- **SE38** (*ABAP Editor*): criar/acessar programas em ABAP;
- **SE09** ou **SE10** (*Transport Organizer*): realizar a gestão das Requests;
- **SE11** (*ABAP Dictionary*): Dicionário de Dados ABAP;
- **SE80** (*Object Navigator*): ambiente central de desenvolvimento - criar/gerenciar pacotes e seus objetos hierarquicamente;
- **SE16** ou **SE16N** (*Data Brower*): navegador do banco de dados - visualização/edição dos dados das tabelas do BD.

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

Variáveis do Servidor.

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

* incluir dados na tabela clientes a partir da variável
APPEND cliente TO clientes.

* incluir diretamente dados na tabela clientes
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

### CLEAR - limpar valores das variáveis
CLEAR cliente2.
WRITE: / 'CLEAR cliente2: ', cliente2-nome.
```
