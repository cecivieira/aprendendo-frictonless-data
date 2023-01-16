# Exemplos básicos

## Abrir arquivo
```python script
with open('data/countries.csv') as paises:
    print(paises.read())
```

## Imprimir na tela o arquivo
```python script
from pprint import pprint
from frictionless import describe

resource = describe('data/countries.csv')
pprint(resource)
```

## Insere novos metadados no schema e corrige tipo de dado
<!-- ```python script
from frictionless import Detector, describe

detectar = Detector(field_missing_values=["", "n/a"]) # informa quais os valores que o sistema deve interpretar como valor faltante
resource = describe('data/countries.csv', detector=detectar) #aplica substituição ao arquivo countries.csv
resource.schema.get_field("neighbor_id").type="integer" #modifica tipo de dado da coluna neighbor_id para inteiro
resource.schema.foreign_keys.append(
    {"fields": ["neighbor_id"], "reference": {"resource": "", "fields": ["id"]}} 
) #cria vínculo entre colunas neighbor_id e id. Informando que neighbor_id é chave estrangeira a qual sua referência é a coluna id localizada no mesmo arquivo
resource.to_yaml("data/countries.resource.yaml")
``` -->

```python script
with open('data/countries.resource.yaml') as paises_yaml:
    print(paises_yaml.read())
```

# Extrair dados usando os metadados corrigidos
```python script
from pprint import pprint
from frictionless import extract

pasta = 'data'
rows = extract('../data/countries.resource.yaml') #esse arquivo precisa estar na pasta raiz para que o método rode
pprint(rows)
```

# Validar dados
```python script
from pprint import pprint
from frictionless import validate

report = validate('countries.resource.yaml')
pprint(report.flatten(["rowPosition", "fieldPosition", "code"]))
```

# Transformar dados
```python script
from frictionless import Resource, describe, transform, steps

def clean(resource):
    current = resource.to_copy()

    # Data
    def data():
        with current:
            for row in current.row_stream:
                if row["name"] == "France":
                    row["population"] = 67
                if row["name"] == "Germany":
                    row["neighbor_id"] = 2
                if row["name"]:
                    yield row

    # Meta
    resource.schema = Resource("countries.resource.yaml").schema
    resource.data = data

source = describe("data/countries.csv")
target = transform(
    source,
    steps=[
        clean,
        steps.table_write(path="data/countries.csv"),
    ],
)

# Exibir o arquivo transformado
with open('data/countries.csv') as file:
    print(file.read())
```

## Listar arquivos e metadados pertencentes ao conjunto de dados que acabamos o pré-processamento

Esse trecho de código não está com o comportamento esperado. É necessário ler a documentação da biblioteca OS, identificar a função a qual aceite como argumento o caminho da pasta data e aplique na pasta o filtro desejado (nesse caso o filtro é "retornar todos os arquivos começados com countries.").

```python script
import os

pasta2 = 'data/'
files = [f for f in os.listdir(pasta2) if os.path.isfile(f'{pasta2}{f}') and f.startswith('countries.')]

print(files)

```


