# Exemplos básicos 
([link para capítulo do Manual](https://framework.frictionlessdata.io/docs/basic-examples.html))

- Abrir arquivo
```python script
with open('data/countries.csv') as arquivo:
    print(arquivo.read())
```
- Descrever o arquivo (descrição automática)
```python script
from pprint import pprint
from frictionless import describe

recurso = describe('data/countries.csv')
pprint(recurso)

```

- Atualizar metadados do arquivo
```python script
from frictionless import Detector, describe

deteccao_valor_ausentes = Detector(field_missing_values = ['', 'n/a'])
recurso = describe('data/countries.csv', detector=deteccao_valor_ausentes)
recurso.schema.get_field('neighbor_id').type = 'integer'
recurso.schema.foreign_keys.append(
    {"fields": ["neighbor_id"], "reference": {"resource": "", "fields": ["id"]}}
)
recurso.path = 'countries.csv' # corrige path do arquivo csv. Originalmente é gerado um path do caminho relativo à pasta raiz. A alteração é necessária porque quando o arquivo de metadados e dados estão na mesma pasta, o caminho deveria ser relativo à pasta atual.
recurso.to_yaml('data/countries_v5.resource.yaml')
```

```python script
with open('data/countries_v5.resource.yaml') as arquivo:
    print(arquivo.read())
```

- Extrair dados
```python script
from pprint import pprint
from frictionless import extract

tabela = extract('data/countries_v5.resource.yaml')
pprint(tabela)
```

- Validar dados
```python script
from pprint import pprint
from frictionless import validate

relatorio_validacao = validate('data/countries_v5.resource.yaml')
pprint(relatorio_validacao.flatten(['rowNumber', 'fieldName', 'type']))
```
1. Para imprimir o relatório completo de validação, use apenas `pprint(relatorio_validacao`.
1. A numeração de cada linha considera a contagem a partir da chave 'headerRows' da descrição (describe). Nesse caso, o 'headerRows' é 2, então a primeira linha de dados é 3.

- Transformar dados
```python script
from pprint import pprint
from frictionless import Resource, Pipeline, describe, transform, steps

pipeline = Pipeline(steps=[
    steps.cell_replace(field_name='neighbor_id', pattern='22', replace='2'),
    steps.cell_replace(field_name='population', pattern='n/a', replace='67'),
    steps.row_filter(formula='population'),
    steps.field_update(name='neighbor_id', descriptor={"type": "integer"}),
    steps.table_normalize(),
    steps.table_write(path="data/countries-cleaned.csv"),
])

resource = Resource('data/countries.csv')
target = resource.transform(pipeline)
pprint(target.read_rows())
```