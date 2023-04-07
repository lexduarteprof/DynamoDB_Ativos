- Usando a AWS para gerenciar um banco de dados 
DynamoDB, para registro de ativos financeiros.


- Configurar a AWS CLI

```
aws configure

```


- Criar da Tabela AtivoFinanceiro, que irá armazenar
informações sobre os diversos ativos que irão compor
as carteiras de investimento.

```
aws dynamodb create-table \
    --table-name AtivoFinanceiro \
    --attribute-definitions \
        AttributeName=codigoISIN,AttributeType=S \
        AttributeName=nome,AttributeType=S \
    --key-schema \
        AttributeName=codigoISIN,KeyType=HASH \
        AttributeName=nome,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserir um item

aws dynamodb put-item \
--table-name AtivoFinanceiro \
--item file://LTN_01012025.json \

- Inserir múltiplos itens

```
aws dynamodb batch-write-item ^
    --request-items file://VariosAtivos.json
```

- Criar um index global secundário baseado no nome do Ativo

```
aws dynamodb update-table ^
    --table-name AtivoFinanceiro ^
    --attribute-definitions AttributeName=nome,AttributeType=S ^
    --global-secondary-index-updates "[{\"Create\":{\"IndexName\":\"nomeAtivo-index\",\"KeySchema\":[{\"AttributeName\":\"nome\",\"KeyType\":\"HASH\"}],\"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
- Criar um index global secundário baseado no código ISIN e no nome do título

```
aws dynamodb update-table ^
    --table-name AtivoFinanceiro ^
    --attribute-definitions ^
        AttributeName=codigoISIN,AttributeType=S ^
        AttributeName=nome,AttributeType=S ^
    --global-secondary-index-updates ^
        "[{\"Create\":{\"IndexName\": \"ISINnome-index\",\"KeySchema\":[{\"AttributeName\":\"codigoISIN\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"nome\",\"KeyType\":\"RANGE\"}],\"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisar item pelo código ISIN (usando CMD Windows)
```
aws dynamodb query ^
    --table-name AtivoFinanceiro ^
    --key-condition-expression "codigoISIN = :ISIN" ^
    --expression-attribute-values "{\":ISIN\": {\"S\":\"BRSTNCLTN7N2\"}}"
```

- Pesquisar itens pelo nome (usando CMD Windows)
```
aws dynamodb query ^
    --table-name AtivoFinanceiro ^
    --index-name nomeAtivo-index ^
    --key-condition-expression "nome = :nome" ^
    --expression-attribute-values "{\":nome\":{\"S\":\"LTN\"}}"
```

- Pesquisar itens pelo codigoISIN e nome (usando CMD Windows)
```
aws dynamodb query ^
    --table-name AtivoFinanceiro ^
    --index-name ISINnome-index ^
    --key-condition-expression "codigoISIN = :codigoISIN and nome = :nome" ^
    --expression-attribute-values "{\":codigoISIN\":{\"S\":\"BRSTNCLTN7N2\"}, \":nome\":{\"S\":\"LTN\"}}"
```
- Alguns prints da AWS:

![DynamoDB1.jpg](prints%2FDynamoDB1.jpg)

![DynamoDB2.jpg](prints%2FDynamoDB2.jpg)