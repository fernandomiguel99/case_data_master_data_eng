# case_data_master_data_eng
Projeto de Data Master da trilha de Engenheiro de Dados

GitHub: https://github.com/fernandomiguel99/case_data_master_data_eng

#1 Objetivo do Case:

- Implementação de um Data Warehouse;
- Utilizando estrutura de Camada "Medallion";
- Consumo e verificação dos dados gerados.

#2 Arquitetura:

Utilização do ambiente Databricks SQL Warehouse:
- Estrutura Lakehouse;
- Processamento Serverless;
- Volumes, schemas e catalogs;
- Alertas via e-mail de erros em processos;
- Integração com dashboard PowerBi;

GitHub:

- Versionamento de código;
- Colaboração;
- Segurança.

PowerBi

- Visualização dos dados gerados na camada Gold;
- Customização de gráficos e relatórios;
- Integração com websites.

#3 Explicação sobre o Case desenvolvido:

Base de dados pública do BNDES de operações de financiamento não automáticas: https://dadosabertos.bndes.gov.br/dataset/operacoes-financiamento

Base disponibilizada mensalmente através de um arquivo .csv.

Através do arquivo 'environment_setup', as estruturas Catalog, Schema, Volume e um Job Pipeline usados neste projeto serão criados automaticamente.

Esse projeto pode ser executado de duas formas diferentes: processo único ou em 4 etapas.

##3.1 Processo único:
Podemos executar diretamente o arquivo '99.process_full' ou criar uma job com esse arquivo.
Desse modo, só é necessário que após a criação do ambiente, o arquivo CSV baixado no site do BNDES seja salvo no 'file_ingest' e em seguida é possível executar o código.

##3.2 Processo em 4 etapas:
Desse modo, é necessário criar uma job com as respectivas tasks: "00_FILE_INGEST", "01_BRONZE_TABLE", "02_SILVER_TABLE", "03_GOLD_TABLE".
Os arquivos estão prontos para receber os nomes acima como nomes das tasks por compartilharem variáveis entre elas.

Por fim, a job pode ser configurada para rodar automaticamente aguardando a atualização de um arquivo no volume 'file_ingest' ou pode ser executada de forma manual.

- > A etapa '00.file-ingest' procura dentro do volume 'file_ingest' pelo arquivo mais recente e verifica se o nome do arquivo contém "operacoes-financiamento-operacoes-nao-auto_".
- > Caso o passo anterior seja verdadeiro, a data de referência é extraída do nome do arquivo e o arquivo de carga('operacoes-financiamento-operacoes-nao-auto_carga') é sobreescrito.
- > Na etapa '01.bronze_table', o Schema da tabela bronze é imposto para garantir a qualidade das informações, os campos de CNPJ do cliente e da instituição financeira são anonimizados conforme exigido pela LGPD.
- > Na etapa '02.silver_table', é adicionado um número sequencial ao contrato, a fim de separar os subcontratos, alguns campos que não serão utilizados são removidos e algumas nomenclaturas de campos ajustadas.
- > Na etapa final '03.gold_table', é gerado um modelo analítico, sem dados pessoais identificáveis, apenas com características dos contratos, para gerar os dados necessários para alimentar o dashboard em PowerBI.
- > Por fim a base 'data_warehouse.bndes.GOLD_LAYER_bndes_oper_finan_n_auto' é conectada à ferramenta PowerBi, para validar e utilizar os dados gerados nos passos anteriores.

## Segurança e continuidde

Para garantir a segurança e a continuidade do processo, durante a etapa 00_FILE_INGEST, caso o arquivo mais recente não seja o esperado, as outras etapas de tratamento das bases não são executadas. 
Nas etapas seguintes, no momento de carregamento das tabelas, é realizada uma comparação entre as referências para evitar a duplicação de informações já existentes.

#4 Melhorias e considerações finais:

- Substituição da ingestão via arquivos .CSV por uma comunicação direta via API, removendo o risco operacional da carga manual.


- Implementação de criptografia baseada em técnicas de HASH,a fim de reforçar a segurança e garantir a conformidade com a LGPD.


- Criação de um script completo de configuração do ambiente de Data Warehouse, incluindo definição de grupos, regras de acesso e políticas, permitindo fácil acesso a protipação desse projeto.