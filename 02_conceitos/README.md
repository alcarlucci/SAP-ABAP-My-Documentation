# SAP ABAP - Conceitos

Conceitos do Ambiente SAP

## O Ciclo de Desenvolvimento

Para compreender a necessidade de uma Request, é essencial entender o fluxo de trabalho de uma implementação:
· Entendimento do Problema: O Consultor Funcional alinha com o cliente a necessidade de negócio.
· Planejamento e Especificação: Criação da Especificação Funcional (regras de negócio) e da Especificação Técnica (detalhes de implementação para o ABAP).
· Implementação (DEV): O desenvolvimento ocorre no ambiente de Desenvolvimento.
· Testes Unitários: O desenvolvedor realiza testes isolados e correções de erros.
· Transporte e Testes Integrados (QAS): A solução é movida para o ambiente de Qualidade para validação com dados mais próximos da realidade.
· Produção (PRD): Após a aprovação, a solução é transportada para o ambiente Produtivo, onde o cliente opera suas atividades reais.

## Landscape de Ambientes SAP

A infraestrutura SAP é geralmente dividida em três ambientes principais (podendo variar conforme a empresa):
· DEV (Desenvolvimento): Onde as soluções são criadas. Possui poucos dados e é focado na codificação e configuração.
· QAS (Qualidade/Teste): Ambiente de validação. Geralmente é uma cópia do ambiente produtivo, contendo dados históricos e volumetria para testes integrados seguros.
· PRD (Produção): Ambiente real onde as transações de negócio ocorrem (vendas, emissão de notas, etc.). É o ambiente crítico que não deve sofrer alterações diretas sem testes prévios.

## O que é uma Request (Ordem de Transporte)?

A Request funciona como um "container" ou pacote que armazena objetos (programas, tabelas, configurações) para serem transportados de um ambiente para outro (ex: de DEV para QAS).

Conceitos Importantes:
· Objetos Transportáveis (Z): Desenvolvimentos customizados que devem seguir o fluxo de transporte entre ambientes.
· Objetos Locais (Pacote $TMP): Objetos criados apenas para testes locais no servidor atual. Eles não pedem Request e não são transportados. O instrutor
menciona a convenção de nomenclatura "Y" para estes objetos em alguns clientes.

### Estrutura: Request vs. Task

A hierarquia de transporte é composta por:
· Request (Ordem): O container principal que agrupa as alterações de um projeto ou demanda.
· Task (Tarefa): Subdivisões dentro da Request. Cada desenvolvedor trabalhando na mesma Request terá sua própria Task.

Regra de Liberação: Para liberar (transportar) uma Request, todas as Tasks contidas nela devem ser liberadas primeiro. A Task pertence a um usuário específico,
enquanto a Request agrupa o todo.

### Tipos de Requests

Existem diferentes categorias de ordens de transporte, sendo as principais:
· Workbench Request: Utilizada para objetos de repositório e desenvolvimento técnico (Tabelas, Programas ABAP, Funções, Telas, Perfis de Acesso PFCG).
Geralmente independe de mandante (client).
· Customizing Request: Utilizada para configurações funcionais e parametrizações de negócio (Transação SPRO, tabelas de customizing). Estas alterações são
específicas de mandante.
· Transport of Copies (Transporte de Cópias): Uma técnica utilizada para clonar uma Request e transportá-la para o ambiente de teste (QAS) sem fechar/liberar a
Request original. Isso permite testar versões parciais sem finalizar o desenvolvimento no ambiente de origem.

### Prática: Transação SE09 / SE10

A gestão das Requests é feita através das transações SE09 ou SE10 (Transport Organizer). Nesta ferramenta é possível:
· Visualizar Requests (Modificáveis e Liberadas).
. Criar novas Requests e Tasks.
· Alterar o titular (dono) de uma Request.
· Adicionar objetos manualmente.
· Liberar (Release): O ícone do "caminhão" é utilizado para liberar primeiro a Task e, em seguida, a Request, iniciando o processo de transporte para o próximo
ambiente.

## Pacotes (Packages) no SAP

Os pacotes funcionam como contêineres lógicos para agrupar objetos de desenvolvimento (como programas, tabelas e classes) que pertencem ao mesmo módulo ou projeto. A principal função do pacote é definir se e como esses objetos serão transportados entre os ambientes (Desenvolvimento -> Qualidade -> Produção).
. Objetos Locais ($TMP): Não possuem pacote de transporte definido e ficam restritos ao ambiente de desenvolvimento.
. Objetos Transportáveis: Devem ser atribuídos a um pacote (iniciado com Z ou Y para desenvolvimentos customizados) e a uma Request (Ordem de Transporte).
Analogia Didática: Se a Request é o caminhão que transporta as mudanças, o Pacote é a estrada ou o conteiner que agrupa o que sera levado.

### Transação SE80 (Object Navigator)
A transação SE80 é o ambiente central de desenvolvimento. Nela, é possível visualizar, criar e gerenciar pacotes e seus objetos hierarquicamente.

### Estrutura de Pacotes (Boas Práticas)
Foi apresentado um exemplo real de arquitetura de pacotes baseado em um "Gerenciador de Projetos":
· Pacote Principal (Main): O pacote raiz que agrupa toda a solução (Ex: ZDLPM - Z + Descomplicando Linguagens + Product Manager).
Subpacotes: Divisões por módulos ou funcionalidades (EX: _ TICKET , _TIME , _SQUAD )
Cross Application (CA): Um subpacote dedicado a objetos reutilizáveis por todos os outros módulos (Ex: mensagens de sistema, tratamento de arquivos).

## Tipos Primitivos

Os tipos primitivos são a base para o armazenamento de dados. Eles definem se um valor será um texto, um número inteiro, uma data, etc. Independentemente da complexidade da estrutura (como tabelas ou classes), no nível mais baixo, o dado sempre será armazenado como um tipo primitivo.

Os principais tipos primitivos no SAP ABAP mencionados são:
· I (Integer): Números inteiros.
· F (Float): Ponto flutuante (números com casas decimais).
. C (Char): Caractere ou String de tamanho fixo.
· N (Numeric Text): Texto numérico (apenas dígitos, mas tratado como texto).
· STRING: Cadeia de caracteres de tamanho dinâmico.
· XSTRING: Sequência de bytes hexadecimal dinâmica.
· D (Date): Data (formato AAAAMMDD).
· T (Time): Hora (formato HHMMSS).

## O que é um Domínio?

O Domínio é um objeto do Dicionário de Dados ABAP (transação SE11) que define as características técnicas de um campo. Ele atua como uma camada acima do tipo primitivo, adicionando regras e propriedades específicas.

Características definidas pelo Domínio:
· Tipo de dado (ex: CHAR, DEC, INT).
· Tamanho do campo (Length).
· Casas decimais (para campos numéricos).
· Rotinas de conversão (ex: converter zeros à esquerda)
· Intervalo de Valores (Value Range): Restrições de valores aceitos.
· Maiúsculas/Minúsculas (Case Sensitivity).

Regra Importante: Um domínio não pode ser utilizado diretamente na declaração de variáveis em um programa. Ele serve como base técnica para a criação de um Elemento de Dados (Data Element), que por sua vez será utilizado nas tabelas e programas.
