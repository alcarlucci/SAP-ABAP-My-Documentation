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

## Tabela Transparente (*Database Tables*)

Diferente das **tabelas internas** (*Internal Tables* - memória temporária) vistas anteriormente, que perdem seus dados assim que o programa é encerrado, as **Tabelas Transparentes** (Banco de Dados) servem para armazenar dados reais e físicos no banco de dados do sistema. Assim, iremos entender como criar estruturas que armazenam dados de forma persistente no SAP e como manipulá-los por meio da linguagem **ABAP**.

### Criação da Tabela no Dicionário de Dados (SE11)

A criação é feita através do Dicionário de Dados ABAP. O processo envolve a definição do nome da tabela, seus campos e configurações técnicas.

#### Estrutura e Campos

- **Nome da Tabela:** segue o padrão do cliente (iniciando com `Z` ou `Y` );
- **Mandante (MANDT):** é o primeiro campo padrão na maioria das tabelas SAP;
  - Função: o SAP opera com o conceito de "Mandantes" (Clients). Isso permite que varias empresas ou filiais compartilhem o mesmo servidor físico e o mesmo código, mas mantenham seus dados isolados.
  - O campo `MANDT` garante que os dados gravados no mandante 100 não sejam visíveis no mandante 200, por exemplo.
- **Chave Primaria (Primary Key):** conjunto de campos que identifica unicamente um registro. Não é permitido salvar dois registros com a mesma chave;
  - A *flag* **Valores Iniciais** deve ser marcada para impedir chaves nulas.
- **Elementos de Dados:** define o tipo do dado (Char, Numc, etc.);

#### Configurações Técnicas

Ao ativar a tabela, é necessário definir como o banco de dados gerenciará o armazenamento:

- **Categoria de Dados (Data Class):** define a área física do banco (*Tablespace*);
  - `APPL0` : **Dados Mestres** (dados que mudam pouco, como configurações).
  - `APPL1` : **Dados de Movimento** (dados transacionais, como pedidos de venda).
- **Categoria de Tamanho (Size Category):** Estimativa da quantidade de registros (ex: `0` para até alguns milhares de registros);
- **Categoria de Ampliação:** define se a tabela pode ser estendida futuramente. Para evitar avisos (*warnings*), pode-se marcar como "Não classificável" ou "Não ampliável" em ambientes de estudo.

### Visualização de Dados (SE16N)

A transação **SE16N** é utilizada para visualizar, inserir e editar registros diretamente nas tabelas do banco de dados (quando permitido). É uma ferramenta essencial para validar se os dados foram gravados corretamente.

### Manipulação de Dados via ABAP (Open SQL)

Para interagir com a tabela transparente dentro de um programa (*Report*), utilizamos comandos **Open SQL**.

#### Declaração

Para trabalhar com os dados no programa, criamos uma tabela interna que espelha a estrutura da tabela do banco, ex.:

```abap
DATA: ls_status TYPE ZDLFAJRT_001, " estrutura para uma linha da Tabela
      lt_status TYPE TABLE OF ZDLFAJRT_001. " estrutura para TABELA Status
```

#### Comando SELECT (Ler Dados)

Busca os dados do banco e os traz para a memória do programa, ex.:

```abap
" Seleciona dados da Tabela no BD (usando * - todas colunas)
select *
  from ZDLFAJRT_001
  INTO TABLE lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - precisa estar na ordem certa das colunas na tabela)
select status descricao
  from ZDLFAJRT_001
  INTO TABLE lt_status.

" Seleciona dados da Tabela no BD (usando nome das colunas - qq ordem usando CORRESPONDING FIELDS)
select descricao status
  from ZDLFAJRT_001
  INTO CORRESPONDING FIELDS OF TABLE lt_status.
```

#### Comando MODIFY (Inserir ou Atualizar)

O comando MODIFY é versátil: se o registro (chave) já existir, ele atualiza; se não existir, ele insere um novo, ex.:

```abap
" INSERIR dados na tabela no BD
ls_status-status = 'A'.
ls_status-descricao = 'Teste A'.
MODIFY ZDLFAJRT_001 FROM ls_status.
```

#### Comando DELETE (Apagar)

Remove registros do banco de dados. Boas práticas: sempre utilize a cláusula `WHERE`, ex.:

```abap
"exclui os registro(s) dentro da condição WHERE
delete from zdlfajrt_001
where status = 'A'.

" PERIGO: exclui TODOS os registros da tabela (sem WHERE)"
delete from zdlfajrt_001.
```

### Conclusão (Database Tables)

As **Tabelas Transparentes** são a base da persistência de dados no SAP. O uso correto das chaves primárias garante a integridade dos dados, e os comandos SQL (`SELECT`, `MODIFY`, `DELETE`) permitem que seus programas interajam dinamicamente com esses dados.

## Visões (Views) no SAP

Uma **Visão** (**View**) é uma estrutura lógica que permite visualizar dados de uma ou mais tabelas de banco de dados, sem duplicar
fisicamente esses dados, definidas no Dicionário de Dados do SAP (transação SE11/SE80).

As visões no SAP possuem, basicamente, duas funcionalidades principais:

- **Projeção (Projection):** permite criar uma visualização de uma única tabela, selecionando apenas colunas específicas e ocultando outras (como dados sensíveis ou técnicos);
- **Junção (Join):** permite combinar dados de duas ou mais tabelas relacionadas.

Nota: a Visão de Banco de Dados (**Database View**) é somente Leitura, embora existam outros tipos de visões como **Visões de Atualização**.

### Criação de Views

A criação é feita através do Dicionário de Dados ABAP (transação **SE11** ou **SE80**). O processo envolve as configurações:

- **Table/Join Conditions:** seleção das tabelas e definição das relações (join conditions);
- **View Fields:** seleção das colunas (campos/fields) desejadas;
- **Selection Conditions:** recurso de filtrar dados nativamente, definindo regras fixas (condições) para os dados exibidos.

### Conclusão (Views)

As Visões de Banco de Dados são ferramentas essenciais para simplificar consultas, garantir segurança através da projeção de colunas e otimizar o desenvolvimento ao realizar junções complexas diretamente no Dicionário de Dados, sem a necessidade de codificação excessiva em ABAP.

## SM30 - Edit Table Views

A **SM30** é uma transação standard da SAP utilizada para manipular dados de tabelas ou visões. Ela gera uma interface gráfica amigável que permite ao usuário inserir, modificar e deletar registros sem a necessidade de criar um programa do zero.

- Serve para manutenção de dados de parametrização e cadastro;
- Permite a manipulação de **Eventos** (pontos no tempo). Estes eventos são usados para automatizar lógicas, como gravar data de criação ou usuário que modificou o registro.

### Criando um Diálogo de Atualização

Embora a SM30 seja a transação de execução, a criação do diálogo (tela) ocorre através do Dicionário de Dados (SE11). Para que uma tabela possa ter uma SM30, ela deve possuir uma chave primária definida (além do `MANDT`).

Passo a passo para criação:

1. Acesse a transação **SE11** e informe a tabela desejada;
2. Utilitários > Gerador de Atualização de Tabelas:
    - **Grupo de Autorização:** define quem pode acessar; use `&NC&` (sem grupo) para testes ou se não houver restrição de acesso específica;
    - **Grupo de Funções:** nome do grupo onde os módulos de função da tela serão gerados (geralmente o mesmo nome da tabela);
    - **Tipo de Atualização:**
      - *Nível Único:* listagem e edição na mesma tela.
      - *Dois Níveis:* uma tela de Listagem e outra tela individual para Detalhes/Edição;
3. Clique em "**Procurar nº tela**" para o SAP sugerir a numeração (geralmente tela 1);
4. Clique no ícone de **Criar** (*papel branco)* e atribua ao pacote e request adequados.

### Customização da Tela

Após gerar o diálogo (tela), é possível personalizar a aparência da tela gerada. Isso pode ser feito via **Layout Gráfico** (*Screen Painter*) ou pela **Lista de Elementos**
(caso o editor gráfico não esteja disponível no ambiente).

Alguns ajustes possíveis:

- Textos de Cabeçalho: Alterar nomes técnicos (ex: CHAR45 ) para descrições amigáveis;
- Dropdowns (Caixa de Listagem): se o Domínio do campo possuir valores fixos, a SM30 cria automaticamente um combo box. É possível configurar para exibir apenas o texto ou a chave + texto;
- Tamanho dos Campos: ajuste da largura visual (*Vis. Length*) para melhorar a estética da tabela;
- Atributos: configurações de somente leitura, obrigatoriedade, entre outros.

### Conclusão SM30

A SM30 cria uma interface (tela) para o usuário que é um reflexo direto do banco de dados. Qualquer alteração feita nela (inserção, alteração ou exclusão) e efetivada na tabela transparente (*Database Table*).

## Ajuda de Pesquisa (*Search Helps*)

A **Ajuda de Pesquisa**, tecnicamente chamadas de ***Search Helps*** e popularmente conhecidas no ecossistema SAP como ***Matchcodes*** (acionados pela tecla F4), é um objeto do Dicionário de Dados (SE11) que tem como objetivo auxiliar o usuário no preenchimento de campos de entrada. Ela fornece :

- **Sugestões de preenchimento:** lista de valores possíveis;
- **Validação:** Garante que o dado inserido existe na base de dados ou lista permitida.

### Formas de Implementação

Existem diferentes formar de gerar essa ajuda ao usuário:

- **Via Domínio (Valores Fixos):** útil para Listas estáticas e pequenas (ex: Status "Aberto", "Fechado"). O sistema gera o *matchcode* automaticamente com base nos "Valores Individuais" configurados no domínio;
- **Via Objeto Search Help:** permite buscas dinâmicas em tabelas do banco de dados, oferecendo maior flexibilidade e opções de filtragem.

### Contextos de Atribuição

É preciso definir onde vincular a Ajuda de Pesquisa. Existem três contextos principais:

1. **No Domínio:** restrito a valores fixos;
2. **Na Tabela (Coluna):** a ajuda de pesquisa funciona apenas para aquela tabela específica. Indicado quando a regra de busca muda dependendo da tabela;
3. **No Elemento de Dados:** a ajuda de pesquisa é herdada automaticamente em qualquer Lugar (tabelas, estruturas, programas) onde este elemento de dados for utilizado.

**Recomendação:** sempre que a característica do dado for constante (sempre buscará na mesma tabela mestre), vincule a *Search Help* ao **Elemento de Dados** para garantir reutilização e padronização.

### Passo a Passo Prático

Criação de uma *Elementary Search Help*:

- **Criação:** transação SE11 > Search Help;
- **Método de Seleção:** definição da tabela de onde os dados virão;
- **Parâmetros:**
  - **IMP (Import):** parâmetros que recebem valores para filtrar a busca;
  - **EXP (Export):** o valor que será retornado para o campo na tela (ex: o código da atividade);
  - **LPos/SPos:** posições de exibição na Lista de resultados e na Janela de seleção;
- **Vinculação:** acessar o Elemento de Dados > aba "Caract. Adicionais" (*Further Characteristics*) > atribuição da *Search Help* criada, mapeando o parâmetro de exportação.

## Criando uma Transação (SE93)

Para disponibilizar um programa desenvolvido (como um relatório) para o usuário final criamos uma transação personalizada.

### Passo a passo na transação SE93

1. Acesse a transação **SE93**;
2. Insira o código da nova transação (geralmente iniciando com `Z` ou `Y`);
3. Clique em "Criar" e insira uma descrição breve;
4. **Tipo de Objeto de início:** para relatórios comuns utilize **Program and selection screen** (Programa com tela de seleção);
5. **Programa:** vincule o nome do programa ABAP que você criou;
6. **Opções de Compatibilidade de GUI:** marque as opções SAPGUI for Windows; Java; HTML;
7. Salve a transação, atribuindo-a ao seu Pacote e Request de transporte.

Após esse processo, o programa pode ser executado diretamente digitando o novo código criado na caixa de comando do SAP.
