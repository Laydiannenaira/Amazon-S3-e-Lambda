# Amazon-S3-e-Lambda


---

### 1. LocalStack (Simulação Local da AWS)

*   **Definição e Natureza:** É uma ferramenta de **código aberto (open source)**, criada em 2016 e tornada pública em 2017.
*   **Propósito Principal:** Ajuda a **simular localmente o ambiente AWS**.
*   **Objetivo:** Fornecer uma alternativa local para desenvolvimento, teste e integração de serviços em nuvem, eliminando a necessidade de acessar a AWS real. Isso permite que os desenvolvedores **economizem tempo e custo**, especialmente em testes automatizados e ambientes de integração contínua.
*   **Versões:** Oferece uma **versão gratuita**, que é suficiente para muitos casos, mas também possui versões Pro ou Enterprise com recursos profissionais adicionais.
*   **Serviços Suportados (Exemplos):** Permite a simulação de diversos serviços AWS, como **Lambda**, **API Gateway**, **S3**, **DynamoDB** (DamdB), **SNS**, **SQS**, **CloudFormation**, SQS, SC2, Cloud Watch, entre outros.
*   **Configuração e Instalação:**
    *   Requer a instalação do **AWS CLI** (Command Line Interface).
    *   Recomenda-se a instalação do **Python**.
    *   Pode ser executado via CLI (usando `local stack - version`) ou via **Docker**.
    *   É necessário configurar o AWS Config, embora as credenciais não precisem ser válidas em um ambiente local, elas precisam ser definidas.
    *   A instância do LocalStack deve estar no status **"running"** (em execução) para que o trabalho comece.
    *   Para acesso ao Dashboard, a ferramenta abre no browser, mostrando a instância e os serviços.

### 2. AWS Lambda (Computação Serverless)

*   **Definição:** É um serviço de computação **serverless** (sem servidor) que permite a **execução de código em resposta a eventos**.
*   **Modelo de Eventos:** O código é executado somente quando necessário, disparado por ações específicas (eventos), como a chegada de um arquivo no S3, uma chamada, leitura de logs, ou interação com tabelas.
*   **Tecnologias:** Pode executar código em várias linguagens, como **Python**, Java, Node.js e C#.
*   **Práticas de Execução:** O ideal é que as execuções sejam curtas, com um **tempo máximo de processamento de 15 minutos** (desde a entrada da informação até a saída).
*   **Vantagens Principais:**
    *   **Execução sob Demanda:** O código é executado apenas quando solicitado.
    *   **Escalabilidade Automática:** A capacidade se ajusta automaticamente com base no volume de eventos.
    *   **Custo Eficiente:** A cobrança é feita apenas pelo tempo de execução e quantidade de solicitações, sendo muito barato (milhões de execuções por centavos de dólar).
    *   **Integração:** Funciona como um **"conector"** poderoso entre diversos serviços AWS (S3, DynamoDB, API Gateway).
*   **Arquitetura:** A boa prática é usar múltiplas Lambda Functions para tarefas pequenas (ex: uma para consulta, outra para inserção, outra para validação).

### 3. Amazon S3 (Simple Storage Service)

*   **Definição:** É um serviço de **armazenamento em nuvem** da AWS, funcionando como um **repositório**.
*   **Características:** É altamente **escalável**, **durável**, **seguro** e está sempre disponível.
*   **Armazenamento:** Suporta o armazenamento de **qualquer tipo de arquivo** (vídeos, áudios, imagens, documentos, arquivos trocados por instituições financeiras).
*   **Casos de Uso:** É ideal para **backup**, armazenamento de dados e histórico (ex: exames médicos, arquivos de contabilidade).
*   **Gerenciamento de Custos:** Possui diferentes classes de armazenamento (Hot/Standar e Cold), onde arquivos menos acessados podem ser movidos para *code* (armazenamento frio) para evitar gastos desnecessários. A recuperação de arquivos *code* pode levar alguns dias.
*   **Segurança e Acesso:** É fundamental aplicar a segurança e o controle de acesso corretos. Se o recurso for público (como um website estático), deve-se ter cautela.
*   **URLs Assináveis:** É possível gerar **URLs assináveis (*signed URLs*)** via AWS CLI, que permitem o acesso a um arquivo por um período limitado (ex: 1 hora), sendo ideais para controle de acesso temporário.

### 4. Estudo de Caso (Integração S3, Lambda, DynamoDB e API Gateway)

*   **Arquitetura do Fluxo:** O projeto simula o processamento automatizado de arquivos de notas fiscais/dados de clientes.
*   **Processo de Upload e Disparo:**
    1.  O usuário/sistema envia um arquivo (ex: JSON) para um *bucket* no **S3**.
    2.  O evento de upload **dispara uma *trigger*** configurada no S3.
    3.  A *trigger* **invoca uma Lambda Function**.
    4.  A Lambda Function **processa o conteúdo** do arquivo, lendo registro por registro.
    5.  A Lambda **grava os dados** em uma tabela do **DynamoDB** (banco NoSQL).
*   **Controle de Sucesso/Erro:** O código Lambda (grava DB) move o arquivo original para uma pasta **"sucesso"** ou **"erro"** dentro do *bucket* S3, o que garante histórico, rastreabilidade e permite a geração de alertas em caso de falha.
    *   A regra de processamento pode ser configurada para **ler somente arquivos com um sufixo específico** (ex: `.json`).
*   **Consulta via API Gateway:**
    1.  O **API Gateway** é configurado para ser o ponto de entrada da consulta de dados.
    2.  O API Gateway invoca **outra Lambda Function** (ou *handler* específico dentro da mesma função, dependendo do método POST/GET).
    3.  Essa Lambda Function consulta a tabela do DynamoDB e retorna a informação para o cliente/usuário.
    *   A chamada da Lambda pode diferenciar ações (ex: método **POST** chama `inserir registro`, método **GET** chama `consultar registro`).

### 5. Ferramentas e Recursos de Apoio

*   **AWS CLI:** Utilizada para executar comandos de criação, listagem e configuração de recursos (como S3 e DynamoDB) no ambiente local (usando `aws local`).
*   **Draw.io (ou Draw):** Ferramenta recomendada para **desenhar a arquitetura** do projeto (diagramas) de forma clara e profissional, usando os ícones da AWS.
*   **Postman:** Uma **IDE** essencial para a área de integração de sistemas, usada para **testar e validar chamadas de API** (GET/POST) para o API Gateway.
*   **NoSQL Workbench:** Uma IDE utilizada para se conectar e interagir com bancos de dados DynamoDB, sendo útil para visualizar, escanear e validar os itens inseridos na tabela local do LocalStack.
*   **Token de Acesso:** O LocalStack pode requerer um token para acessar alguns recursos ou versões Pro, que pode ser encontrado e gerenciado na área de *tokens*.

---

A arquitetura demonstrada (S3 -> Trigger -> Lambda -> DynamoDB, com consulta via API Gateway) é um **caso real** e muito comum em empresas que trabalham com cloud, sendo considerado o "feijão com arroz" do processamento de dados. Trabalhar localmente com o LocalStack permite **prototipar e estressar** essa arquitetura antes de migrar para a AWS real.
