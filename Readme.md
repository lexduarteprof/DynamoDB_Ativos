- Criar da Tabela AtivoFinanceiro, que irá armazenar
informações sobre os diversos ativos que irão compor
as carteiras de investimento.

```text
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
--table-name Music \
--item file://src/LTN_01012025.json \
