# SAP ABAP - Programação ABAP

Conceitos para criação de programas na linguagem ABAP.

## Introdução à Criação de Programas ABAP (Reports)

O objetivo principal de um programa no SAP é solucionar um problema de negócio ou atender a uma necessidade específica, como a geração de relatórios de vendas por exemplo.

### Estrutura de um programa Procedural

O SAP suporta e incentiva a Orientação a Objetos, no entanto, para o entendimento lógico em programação, vamos ver a estrutura fundamental de um programa procedural. Um programa **ABAP clássico** é estruturado em três pilares principais:

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
