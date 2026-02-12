# SAP ABAP - Conceitos

Conceitos do Ambiente SAP.

## O Ciclo de Desenvolvimento

Para compreender a necessidade de uma ***Request***, é essencial entender o fluxo de trabalho de uma implementação:

- **Entendimento do Problema:** O Consultor Funcional alinha com o cliente a necessidade de negócio;
- **Planejamento e Especificação:** Criação da *Especificação Funcional* (regras de negócio) e da *Especificação Técnica* (detalhes de implementação para o ABAP);
- **Implementação (DEV):** O desenvolvimento ocorre no ambiente de Desenvolvimento;
- **Testes Unitários:** O desenvolvedor realiza testes isolados e correções de erros;
- **Transporte e Testes Integrados (QAS):** A solução é movida para o ambiente de Qualidade para validação com dados mais próximos da realidade;
- **Produção (PRD):** Após a aprovação, a solução é transportada para o ambiente Produtivo, onde o cliente opera suas atividades reais.

## Landscape de Ambientes SAP

A infraestrutura SAP é geralmente dividida em três ambientes principais (podendo variar conforme a empresa):

- **DEV (Desenvolvimento):** Onde as soluções são criadas. Possui poucos dados e é focado na codificação e configuração;
- **QAS (Qualidade/Teste):** Ambiente de validação. Geralmente é uma cópia do ambiente produtivo, contendo dados históricos e volumetria para testes integrados seguros;
- **PRD (Produção):** Ambiente real onde as transações de negócio ocorrem (vendas, emissão de notas, etc.). É o ambiente crítico que não deve sofrer alterações diretas sem testes prévios.

## O que é uma Request (Ordem de Transporte)?

A ***Request*** funciona como um "container" ou pacote que armazena objetos (programas, tabelas, configurações) para serem transportados de um ambiente para outro (ex: de DEV para QAS).

**Conceitos Importantes:**

- **Objetos Transportáveis (Z):** Desenvolvimentos customizados que devem seguir o fluxo de transporte entre ambientes;
- **Objetos Locais (Pacote $TMP):** Objetos criados apenas para testes locais no servidor atual. Eles não pedem Request e não são transportados. O instrutor menciona a convenção de nomenclatura "**Y**" para estes objetos em alguns clientes.

### Estrutura: Request vs. Task

A **hierarquia de transporte** é composta por:

- **Request (Ordem):** O container principal que agrupa as alterações de um projeto ou demanda;
- **Task (Tarefa):** Subdivisões dentro da Request. Cada desenvolvedor trabalhando na mesma Request terá sua própria Task.

**Regra de Liberação:** para liberar (transportar) uma *Request*, todas as *Tasks* contidas nela devem ser liberadas primeiro. A Task pertence a um usuário específico, enquanto a Request agrupa o todo.

### Tipos de Requests

Existem diferentes categorias de ordens de transporte, sendo as principais:

- **Workbench Request:** Utilizada para objetos de repositório e desenvolvimento técnico (Tabelas, Programas ABAP, Funções, Telas, Perfis de Acesso PFCG). Geralmente independe de mandante (client);
- **Customizing Request:** Utilizada para configurações funcionais e parametrizações de negócio (Transação SPRO, tabelas de customizing). Estas alterações são específicas de mandante;
- **Transport of Copies (Transporte de Cópias):** Uma técnica utilizada para clonar uma Request e transportá-la para o ambiente de teste (QAS) sem fechar/liberar a Request original. Isso permite testar versões parciais sem finalizar o desenvolvimento no ambiente de origem.

### Prática: Transação SE09 / SE10

A gestão das Requests é feita através das transações **SE09** ou **SE10** (**Transport Organizer**). Nesta ferramenta é possível:

- **Visualizar** Requests (Modificáveis e Liberadas);
- **Criar** novas Requests e Tasks;
- **Alterar** o titular (dono) de uma Request;
- **Adicionar objetos** manualmente;
- **Liberar (Release):** o ícone do "*caminhão*" é utilizado para liberar primeiro a Task e, em seguida, a Request, iniciando o processo de transporte para o próximo
ambiente.

## Pacotes (Packages) no SAP

Os pacotes funcionam como contêineres lógicos para agrupar objetos de desenvolvimento (como programas, tabelas e classes) que pertencem ao mesmo módulo ou projeto. A principal função do pacote é definir se e como esses objetos serão transportados entre os ambientes (Desenvolvimento -> Qualidade -> Produção).

- **Objetos Locais ($TMP):** Não possuem pacote de transporte definido e ficam restritos ao ambiente de desenvolvimento;
- **Objetos Transportáveis:** Devem ser atribuídos a um pacote (iniciado com **Z** ou **Y** para desenvolvimentos customizados) e a uma Request (Ordem de Transporte).
Analogia Didática: Se a Request é o caminhão que transporta as mudanças, o Pacote é a estrada ou o conteiner que agrupa o que sera levado.

### Transação SE80 (Object Navigator)

A transação **SE80** (**Object Navigator**) é o ambiente central de desenvolvimento. Nela, é possível visualizar, criar e gerenciar pacotes e seus objetos hierarquicamente.

### Estrutura de Pacotes (Boas Práticas)

- **Pacote Principal (Main):** o pacote raiz que agrupa toda a solução;
- **Subpacotes:** divisões por módulos ou funcionalidades;
- **Cross Application (CA):** um subpacote dedicado a objetos reutilizáveis por todos os outros módulos (Ex: mensagens de sistema, tratamento de arquivos).

## Tipos Primitivos

Os tipos primitivos são a base para o armazenamento de dados. Eles definem se um valor será um texto, um número inteiro, uma data, etc. Independentemente da complexidade da estrutura (como tabelas ou classes), no nível mais baixo, o dado sempre será armazenado como um tipo primitivo.

Os principais tipos primitivos no SAP ABAP mencionados são:

- **i (Integer):** Números inteiros (4 bytes);
- **f (Float):** Ponto flutuante (números com casas decimais) (8 bytes);
- **p (packed number):** número flutuante preciso (16 bytes);
- **c (Char):** Caractere ou String de tamanho fixo;
- **n (Numeric Text):** Texto numérico (apenas dígitos, mas tratado como texto);
- **string:** Cadeia de caracteres de tamanho dinâmico;
- **xstring:** Sequência de bytes hexadecimal dinâmica;
- **d (Date):** Data (formato *AAAAMMDD*);
- **t (Time):** Hora (formato *HHMMSS*).

## O que é um Domínio?

O Domínio é um objeto do **Dicionário de Dados ABAP** (transação **SE11**) que define as características técnicas de um campo. Ele atua como uma camada acima do tipo primitivo, adicionando regras e propriedades específicas.

Características definidas pelo Domínio:

- Tipo de dado (ex: CHAR, DEC, INT).
- Tamanho do campo (Length).
- Casas decimais (para campos numéricos).
- Rotinas de conversão (ex: converter zeros à esquerda)
- Intervalo de Valores (Value Range): Restrições de valores aceitos.
- Maiúsculas/Minúsculas (Case Sensitivity).

**Regra Importante:** Um domínio não pode ser utilizado diretamente na declaração de variáveis em um programa. Ele serve como base técnica para a criação de um Elemento de Dados (*Data Element*), que por sua vez será utilizado nas tabelas e programas.

## Elemento de Dados (Data Elements)

O Elemento de Dados (*Data Elements*) descreve o papel desempenhado por um campo em um contexto técnico.

Enquanto o Domínio define as características técnicas (tipo de dado, tamanho, casas decimais); o Elemento de Dados define o significado daquele campo para o negócio e para o usuario final. Ele atua como uma camada intermediária que pode:

- Utilizar um Dominio preexistente;
- Utilizar um Tipo Primitivo (*Built-in type*) diretamente, sem a necessidade de um domínio;

### Principais Benefícios e Funcionalidades

A utilização de Elementos de Dados traz diversas vantagens para o desenvolvimento e manutenção de software:

- **Reutilização:** Você cria o elemento uma vez e o utiliza em múltiplas tabelas, estruturas e programas;
- **Manutenção Centralizada:** Se for necessário alterar o tipo de dado ou o tamanho de um campo (por ex. de 1 para 2 caracteres), basta ajustar o Domínio ou o Elemento de Dados, e a alteração sera refletida automaticamente em todos os objetos que o utilizam;
- **Textos de Exibição (*Field Labels*):** Define os rótulos que aparecem nas telas (cabeçalhos de colunas em ALVs, descrições em telas de seleção). Isso evita a necessidade de tratar nomes de colunas manualmente via *Field Catalog*;
- **Ajuda de Pesquisa (*Search Help/Match Code*):** Permite associar uma ajuda de pesquisa (F4) para sugerir valores válidos ao usuário;
- **Documentação (F1):** Permite vincular uma documentação tecnica ou funcional que sera exibida quando o usuario pressionar F1 no campo.

### Criando um Elemento de Dados na Prática (Transação SE11)

Para criar um Elemento de Dados, utilizamos a transação **SE11** (Dicionário de Dados ABAP) ou a **SE80**.

1. Acesse a transação SE11;
2. Selecione a opção Tipo de dados, insira o nome (iniciando com Z ou Y, ex: ZDLFAJR_EL001) e clique em Criar;
3. Escolha a opção Elemento de dados;
4. Na aba Tipo dados, você deve definir a característica técnica:
    - Domínio: insira o nome do domínio criado anteriormente. O sistema herdara automaticamente o tipo e o tamanho do domínio selecionado;
    - Tipo incorporado: caso não use domínio, defina diretamente o tipo primitivo (ex: CHAR, INT, DATS);
5. Na aba Denominação de campo, preencha os textos (Breve, Médio, Longo e Cabeçalho). Estes são os textos que aparecerão para o usuário nas telas do SAP;
6. Salve em um *Pacote* e uma *Request*, e ative o objeto (Ctrl + F3).

### Utilização em Programas ABAP (Transação SE38)

Diferente do Domínio, o Elemento de Dados pode ser referenciado diretamente na declaração de variáveis e parâmetros em um programa.

#### Exemplo de Declaração

```abap
* Declaração de variável local
DATA: gv_status TYPE zdlfajrel_001.

* Declaração de parâmetro de tela (Input do usuário)
PARAMETERS: p_status TYPE zdlfajrel_001.
```

#### Comportamento Automático (Match Code)

Se o Elemento de Dados estiver vinculado a um Domínio que possui Valores Fixos (*Value Range*), o SAP gera automaticamente um *Match Code* (F4) no campo de entrada (*PARAMETERS*). Isso exibe para o usuario apenas as opções válidas cadastradas no
domínio, garantindo a integridade dos dados sem necessidade de codificação adicional.

#### Textos de Seleção

Para que o nome do campo na tela de seleção não apareça como o nome técnico da variável (ex: P_STATUS ), ir para:

- Elementos de texto > Textos de seleção;
- Ao marcar a opção de "Dicionario", o programa assume automaticamente o texto
definido no Elemento de Dados.

## Estruturas (Structure)

Uma estrutura é um tipo de dado complexo que permite agrupar diversos campos (colunas) sob um único nome, de forma didática, pode ser comparada a uma única linha de uma tabela de banco de dados, composta por vários Elementso de Dados.

As estruturas servem para definir um conjunto de colunas (variáveis) agrupadas. Enquanto um Elemento de Dados tipifica um único campo (como o CPF ou o Nome), a Estrutura agrupa esses campos para representar um objeto mais complexo (como os dados completos de um Cliente: Nome, CPF, Endereço, Status).

### Criando Estruturas no Dicionário de Dados (SE11)

A criação de estruturas globais e feita através da transação **SE11** (ou via navegação na **SE80**). Estas estruturas ficam armazenadas no Dicionario de Dados e podem ser reutilizadas por qualquer programa no sistema SAP.

Passo a passo da criação:

- Acesse a transação **SE11**, selecione a opção Tipo de dados, insira o nome (ex: `ZDLFAJR_ES_001`) e clique em Criar. Selecione a opção Estrutura;
- **Aba Componentes:** aqui você define as colunas:
  - Componente: Nome do campo (ex: `STATUS`);
  - Tipo de componente: O Elemento de Dados criado anteriormente (ex: `ZDLFAJR_EL_001`) ou um tipo standard (ex: `CHAR40`);
- **Campos de Moeda e Quantidade:** Se a estrutura contiver campos de valor monetario (CURR) ou quantidade (QUAN), é obrigatório preencher a aba Campos moeda/quant, indicando a tabela e o campo de referência para a unidade (ex: BRL, USD) ou unidade de medida (ex: KG, UN);
- **Categoria de Ampliação:** Antes de ativar, vá em Suplementos > Categoria de ampliação. Para fins didáticos iniciais, pode-se definir como "Não ampliável".

### Utilização de Estruturas no Código (SE38)

Existem duas formas principais de declarar e utilizar estruturas em seus programas ABAP (Reports):

1. **Estrutura Global (Dicionário de Dados):** utiliza a estrutura criada na SE11. É a forma recomendada quando o objeto de dados precisa ser padronizado e reutilizado em múltiplos programas. **Vantagem:** Se você adicionar um novo campo na estrutura pela SE11 (ex: `DESCRIPTION`), todos os programas que utilizam essa estrutura reconhecerão o novo campo automaticamente após a ativação.
2. **Estrutura Local (Declarada no Programa):** a estrutura é definida exclusivamente dentro do código do programa utilizando o comando `TYPES` . Ela só existe dentro daquele report específico. **Desvantagem:** se você precisar usar essa mesma estrutura em outro programa, terá que copiar e colar o código. Se houver uma alteração, terá que ajustar manualmente em todos os programas.

Exemplos:

```abap
" Usando ESTRUTURAS criadas Dicionário de Dados (reutilizavel em outros programas)
DATA: ls_status TYPE ZDLFAJRES_001.

ls_status-status = 'A'.
ls_status-description = 'Em aberto'.
WRITE: / 'Estrutura do Dicionário de dados: ', ls_status-status, ls_status-description.

" Usando ESTRUTURAS declaradas localmente no código (utilizado apenas nesse programa)
TYPES: BEGIN OF ty_status,
        status TYPE zdlfajrel_001,
        description TYPE c LENGTH 40,
       END OF ty_status.
DATA: status TYPE tY_status.

status-status = 'V'.
status-description = 'Validado'.
WRITE: / 'Estrutura local no código: ', status-status, status-description.
```

## Tipo Tabela (Table Type)

Diferente de uma estrutura, que armazena apenas uma única linha de dados (um registro), o **Tipo Tabela** (*Table Type*) permite armazenar um conjunto de linhas, ou seja, múltiplos registros baseados em uma estrutura pre-definida.

### Conceito e Criação no Dicionário de Dados

O Tipo Tabela serve para definir uma coleção de dados. Para criá-lo, ele necessita de uma "Categoria de Linha" (Line Type), que geralmente é uma estrutura.

- **Estrutura:** Modelo para uma única linha (ex: colunas Status e Descrição).
- **Tipo Tabela:** Modelo para armazenar várias dessas linhas.

#### Passo a passo na SE80/SE11

1. Criar um novo objeto do tipo *Table Type*;
2. Atribuir a "Categoria de Linha" (*Line Type*) a uma estrutura previamente criada;
3. Salvar em um pacote (para ser transportável) e ativar.

### Utilização no Código ABAP (Sintaxe Tradicional)

Ao declarar variáveis no programa, utilizamos prefixos para facilitar a identificação:

- `ls _` : *Local Structure* (Estrutura Local).
- `lt _` : *Local Table* (Tabela Local).

Para popular uma tabela interna no modo clássico, preenchemos a estrutura e utilizamos o comando `APPEND` para inserir a linha na tabela.

```abap
DATA: ls_status TYPE ZDLFAJRES_001, " Usando ESTRUTURA criada no Dicionário de Dados
      lt_status TYPE ZDLFAJRTT_001. " Usando TABELA criada no Dicionário de Dados

ls_status-status = 'A'.
ls_status-description = 'Em aberto'.
WRITE: / 'Conteúdo na Estrutura: ', ls_status-status, ' - ', ls_status-description.
APPEND ls_status TO lt_status. " adiciona a linha na tabela
```

### Acessando Dados da Tabela

Existem formas diferentes de ler os dados armazenados:

- Acesso Direto (Table Expression): acessa uma linha específica pelo índice: `WRITE: lt_status[ 1 ]-description.` (Imprime a coluna *description* da primeira linha);
- Loop: percorre todas as linhas da tabela jogando o conteúdo de cada linha da tabela para a estrutura a cada iteração:

```abap
LOOP AT lt_status INTO ls_status.
  WRITE: / ls_status-status, ' - ', ls_status-description.
ENDLOOP.
```

### Declaração Inline e Operador VALUE (sintaxe moderna)

Nas versões mais recentes do ABAP, é possível declarar e popular a tabela simultaneamente, reduzindo a quantidade de código. Utilizamos o operador `VALUE` e a declaração `DATA`.

```abap
" Outra maneira de inserir linhas na tabela diretamente na declaração dela (declara a variavel já atribuindo valores)
DATA(lt_status_inline) = VALUE ZDLFAJRTT_001(
  ( status = 'A' description = 'Em Aberto' )
  ( status = 'V' description = 'Validado' )
  ( status = 'S' description = 'Em Separação Estoque' )
  ( status = 'R' description = 'Em Rota' )
  ( status = 'F' description = 'Finalizado' )
).
```

Também é possível utilizar declarações inline para variáveis simples, onde o sistema infere o tipo do dado automaticamente:

- `DATA(lv_texto) = 'Olá Mundo".` (String/Char)
- `DATA(lv_numero) = 1.` (Inteiro)
- `DATA(lv_data) = sy-datum.` (Data atual do sistema)
