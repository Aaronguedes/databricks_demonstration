# projeto_vertigo

Esse projeto consiste na ingestão e análise de uma base de dados de um ecommerce. Foram adotados os seguintes passos para essa solução.

# 1) Análise do Data Source:

No primeiro momento, fiz uma análise da base de dados do ecommerce, e com isso confeccionei um dicionário de dados para ajudar no entendimento do BD.

# Dicionário de Dados

**Tabela: products**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| product_code | inteiro | Chave primária. Código ou identificador de cada produto. PK |
| product_name | texto | Nome do produto. |
| product_line | texto | A linha de produtos à qual o produto pertence. FK da product_lines |
| product_scale | texto | Escala do produto (por exemplo, 1:18, 1:24). |
| product_vendor | texto | Fornecedor ou fornecedora do produto. |
| product_description | texto | Descrição do produto. |
| quantity_in_stock | inteiro | Quantidade do produto disponível em estoque. |
| buy_price | numérico | O preço de compra ou custo do produto. |
| msrp | numérico | Preço de varejo sugerido pelo fabricante do produto. |



**Tabela: customers**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| customer_number | inteiro | Identificador único para cada cliente. PK |
| customer_name | texto | Nome do cliente ou empresa. |
| contact_last_name | texto | Sobrenome do contato principal. |
| contact_first_name | texto | Nome do contato principal. |
| phone | texto | Número de telefone para contato com o cliente. |
| address_line1 | texto | Primeira linha do endereço do cliente. |
| address_line2 | texto | Segunda linha do endereço do cliente (opcional). |
| city | texto | Cidade onde o cliente está localizado. |
| state | texto | Estado onde o cliente está localizado. |
| postal_code | texto | Código postal do endereço do cliente. |
| country | texto | País onde o cliente está localizado. |
| sales_rep_employee_number | inteiro | Chave estrangeira referenciando o número do funcionário representante de vendas responsável por este cliente. FK employee |
| credit_limit | numérico | O limite de crédito para o cliente. |



**Tabela: employees**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| employee_number | inteiro |  Identificador único para cada funcionário. PK |
| last_name | texto | Sobrenome do funcionário. |
| first_name | texto | Nome do funcionário. |
| extension | texto | Ramal telefônico do funcionário. |
| email | texto | Endereço de e-mail do funcionário. |
| office_code | texto | Chave estrangeira referenciando o código do escritório onde o funcionário trabalha. |
| reports_to | inteiro | Número do funcionário supervisor ou gerente a quem este funcionário se reporta. |
| job_title | texto | Cargo ou função do funcionário. |



**Tabela: offices**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| office_code | texto | Identificador único para cada escritório. PK |
| city | texto | Cidade onde o escritório está localizado. |
| phone | texto | Número de telefone de contato do escritório. |
| address_line1 | texto | Primeira linha do endereço do escritório. |
| address_line2 | texto | Segunda linha do endereço do escritório (opcional). |
| state | texto | Estado ou província onde o escritório está localizado. |
| country | texto | País onde o escritório está localizado. |
| postal_code | texto | Código postal do endereço do escritório. |
| territory | texto | Território ou região associada ao escritório. |



**Tabela: orderdetails**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| order_number | inteiro | Liga a order à descrição. FK da orders. |
| product_code | texto | Código ou identificador do produto neste detalhe do pedido. |
| quantity_ordered | inteiro | A quantidade do produto encomendada neste detalhe. |
| price_each | numérico | O preço por unidade do produto neste detalhe. |
| order_line_number | inteiro | - |



**Tabela: orders**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| order_number | inteiro | Identificador único para cada pedido. PK |
| order_date | data | Data e hora em que o pedido foi criado. |
| required_date | data | Data e hora em que o pedido deve ser enviado. |
| shipped_date | data | Data e hora em que o pedido foi enviado. |
| status | texto | Status do pedido (por exemplo, processando, enviado, entregue). |
| comments | texto | Comentários ou notas adicionais sobre o pedido. |
| customer_number | inteiro | Chave estrangeira referenciando o número do cliente que fez este pedido. FK da customers |



**Tabela: payments**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| customer_number | inteiro | Chave estrangeira referenciando o número do cliente para o qual o pagamento é feito. FK da customers. |
| check_number | texto | Número do cheque ou identificador do pagamento. |
| payment_date | date | Data em que o pagamento foi efetuado. |
| amount | numérico | Valor do pagamento. |



**Tabela: product_lines**

| Nome da Coluna | Tipo de Dados | Descrição |
| --- | --- | --- |
| product_line | texto | Identificador único para cada linha de produtos. PK |
| text_description | texto | Descrição textual da linha de produtos. |
| html_description | texto | Descrição da linha de produtos formatada em HTML. |
| image | texto | Caminho ou URL para a imagem representativa da linha de produtos. |
|  |  |  |

Juntamente com o dicionário confeccionei a ERD para uma visão mais holística do data source.

# E**RD**

![Untitled](ERD%20-%20Ecommerce%20Website%20Database.png)

Também disponível na versão dbml \
[ERD DBML](erd_commerce.dbml)

# 2) Arquitetura:

Após isso, confeccionei o desenho da arquitetura para melhor entendimento do projeto.

![Untitled](Arquitetura%20Solução%20-%20Ecommerce%20Analysis%20-%20%20Databricks.png)

Trabalhei com 2 camadas de refinamento, a raw que traz os dados brutos semelhante aos da origem, e a camada de análise que responde os questionamentos feitos na proposta. A ideia inicial era trabalhar com a *Medallion Architecture* e as camadas *bronze, prata e ouro*, no entanto, não havia nenhuma especificação de negócio na proposta que realmente necessitasse disso, contudo, observo que poderia ter sido feita uma camada prata validando alguns dados da raw (bronze), e a camada gold poderia contemplar uma modelagem dimensional (star schema) com a tabela orders como o fato a ser analisado ( talvez até trazendo mesclando dados de outras tabelas como orderdetails nela) , acredito que isso esponderia perfeitamente as questões de negócio formuladas no enunciado do exercício.

Nessa versão do projeto, não mudei a modelagem inicial.

# 3) Implementação:

-Criação das DB Delta no Databricks

[https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305185/4732751666501953/latest.html](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305185/4732751666501953/latest.html)

-Ingestão dos dados do BD para as Delta Tables criadas

[https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305192/4732751666501953/latest.html](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305192/4732751666501953/latest.html)

-Análise e resposta das questões elaboradas, também salvas nas Delta Tables correspondentes.

[https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305213/4732751666501953/latest.html](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6117752450471575/1616688727305213/4732751666501953/latest.html)
