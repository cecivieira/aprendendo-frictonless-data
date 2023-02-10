# Descrever arquivo
[link para o capítulo](https://framework.frictionlessdata.io/docs/guides/describing-data.html)

Exemplo genérico
```python script
from frictionless import describe

package = describe("data/table.csv", type="package")
print(package.to_yaml())
```

The frictionless framework provides 4 different describe functions in Python:

- describe: detects the source type and returns Data Resource or Data Package metadata
- Schema.describe: always returns Table Schema metadata
- Resource.describe: always returns Data Resource metadata
- Package.describe: always returns Data Package metadata


## Describe Schema

```python script
with open('data/country-1.csv') as arquivo:
    print(arquivo.read())
```

Criando a descrição do tipo "Schema.describe", que descreve a estrutura da tabela, ou seja, se houverem várias tabelas com a mesa estrutura o mesmo describe schema deve representá-las.

```python script
from frictionless import Schema

schema = Schema.describe('data/country-1.csv')
schema.to_yaml('data/country-1.schema.yaml')
```

```python script
with open('data/country-1.schema.yaml') as arquivo:
    print(arquivo.read())
```

Editar metadados do schema da tabela:

```python script
from frictionless import Schema

schema = Schema.describe("data/country-1.csv")
schema.get_field("id").title = "Identifier"
schema.get_field("neighbor_id").title = "Identifier of the neighbor"
schema.get_field("name").title = "Name of the country"
schema.get_field("population").title = "Population"
schema.get_field("population").description = "According to the year 2020's data"
schema.get_field("population").constraints["minimum"] = 0 #estabelece valor mínimo para a coluna "population"
schema.foreign_keys.append(
    {"fields": ["neighbor_id"], "reference": {"resource": "", "fields": ["id"]}}
) #insere relação entre colunas (chave estrangeira do id)
schema.to_yaml("data/country-1.schema-full.yaml")
```
```python script
with open('data/country-1.schema-full.yaml') as arquivo:
    print(arquivo.read())
```

## Describing resource

Descreve um arquivo, e apenas 1, arquivo de dados. Retorna informações como encoding, extensão do arquivo e referencia o schema da tabela.

```python script
with open('data/country-2.csv') as file:
    print(file.read())
```
Descrevendo o arquivo original. Metadados não refinados
```python script
from frictionless import describe

resource = describe('data/country-2.csv')
print(resource.to_yaml())
```

Ajustando os metadados
```python script
# from frictionless import Schema, describe

# resource = describe("data/country-2.csv")
# resource.dialect.header_rows = [2] #estabelece a linha da tabela que possui o header, ou seja, ignora comentários que possam existir em linhas anteriores ao início da tabela
# resource.dialect.get_control('csv').delimiter = ";" #explicita delimitador
# resource.schema = "data/country-1.schema-full.yaml" #ATENÇÃO: essa referência é um ponto crítico, pois o caminho relativo correto seria "country-1.schema-full.yaml", no entanto no ato da atribuição não é possível fazê-lo porque dá erro quando não é passado o caminho relativo à pasta raiz. No entanto, no ato do uso do metadado para transformação, dá erro porque o caminho relativo está em relação à raiz e não ao arquivo que está chamando o metadado
# resource.path = "country-2.csv"
# resource.to_yaml("data/country-2.resource-cleaned.yaml")
```

Metadados refinados
```python script
with open('data/country-2.resource-cleaned.yaml') as file:
    print(file.read())
```

## Describing Package

Essa descrição engloba todo o conjunto de dados, que pode ter mais de um arquivo. No exemplo abaixo, usarei 2 arquivos: países e capitais.

```python script
with open('data/country-3.csv') as file:
    print(file.read())

```

```python script
with open('data/capital-3.csv') as file:
    print(file.read())

```

Descrevendo conjunto de dados com vários arquivos
```python script
from frictionless import describe

package = describe("data/*-3.csv")
print(package.to_yaml())
```

Refinando os metadados do conjunto de dados
```python script
from frictionless import describe

package = describe("data/*-3.csv")
package.title = "Countries and their capitals"
package.description = "The data was collected as a research project"
package.get_resource("country-3").name = "country" # o módulo get_resource recebe como argumento a propriedade "name" do describe do recurso
package.get_resource("capital-3").name = "capital" # o módulo get_resource recebe como argumento a propriedade "name" do describe do recurso

# Insere metadados do schema do recurso "country"

package.get_resource("country").schema.foreign_keys.append(
    {"fields": ["capital_id"], "reference": {"resource": "capital", "fields": ["id"]}} 
) #observar que nessa relação, a chave "resource" não está vazia, isso porque está criando relação com coluna de outra tabela
package.get_resource("country").schema.get_field("id").title = "Identifier"
package.get_resource("country").schema.get_field("name").title = "Name of the country"
package.get_resource("country").schema.get_field("population").title = "Population"
package.get_resource("country").schema.get_field("population").description = "According to the year 2020's data"
package.get_resource("country").schema.get_field("population").constraints["minimum"] = 0 #estabelece valor mínimo para a coluna "population"

package.to_yaml("data/country-3.package.yaml")
```

Metadados já refinados
```python script
with open('data/country-3.package.yaml') as file:
    print(file.read())
```

```markdown remark type=warning
A partir daqui as explicações são apenas um aprofundamento do que já foi estudado até aqui. Explica como funciona o método "describe" no baixo nível.
```

## Metadata Importance

Without metadata many software will not be able to even read this data file. Furthermore, without metadata people cannot understand the purpose of this data.
We can export and share this data without any worries.


## Inferring Metadata

Many Frictionless functions infer metadata under the hood such as describe, extract, etc. To see this, let's create a Resource.


```python script
from frictionless import Resource

resource = Resource("data/country-1.csv")
print(resource)

```

Infer metadata
```python script
from frictionless import Resource

resource = Resource("data/country-1.csv")
resource.infer(stats=True)
print(resource)
```

Package infer
```python script
from frictionless import describe

resource = describe("data/*-3.csv")
resource.infer()
print(resource)
```

Resource infer
```python script
from pprint import pprint
from frictionless import describe

resource = describe("data/country-1.csv")
resource.infer()
pprint(resource)
```

## Validating Metadata
```python script
import yaml
from frictionless import Resource

descriptor = {}
descriptor['path'] = 'data/country-1.csv'
descriptor['title'] = 1
try:
    Resource(descriptor)
except Exception as exception:
    print(exception.error)
    print(exception.reasons)

```

## Transforming Metadata
```python script
from frictionless import Resource

resource = Resource("data/country-2.resource-cleaned.yaml")
resource.title = "Countries"
resource.description = "It's a research project"
resource.dialect.header_rows = [2]
resource.dialect.get_control('csv').delimiter = ";"

# Adiciona propriedades customizadas na descrição do recurso de dados
resource.custom["customKey1"] = "Value1"
resource.custom["customKey2"] = "Value2"

resource.to_yaml("data/country-2.resource-updated.yaml")
```

```python script
with open('data/country-2.resource-updated.yaml') as file:
    print(file.read())

```