# Validar dados

```python script
from pprint import pprint
from frictionless import validate

report = validate('data/capital-invalid.csv')
print(report)

```
The high-level interface for validating data provided by Frictionless is a set of validate functions:

- validate: detects the source type and validates data accordingly
- Schema.validate_descriptor: validates a schema's metadata
- resource.validate: validates a resource's data and metadata
- package.validate: validates a package's data and metadata
- inquiry.validate: validates a special Inquiry object which represents a validation task instruction


## Validating a Schema

```python script
import yaml
from frictionless import Schema

descriptor = {}
descriptor['fields'] = 'bad' # must be a list
with open('bad.schema.yaml', 'w') as file:
    yaml.dump(descriptor, file)
```

```python script
from pprint import pprint
from frictionless import validate

report = validate('bad.schema.yaml')
pprint(report)
```

## Validating a Resource
```python script
from frictionless import describe

resource = describe('data/capital-invalid.csv')
resource.path = 'capital-invalid.csv'
resource.to_yaml('data/capital.resource.yaml')
```

```python script
from frictionless import validate

report = validate('data/capital.resource.yaml')
print(report)
```
A validação passada está toda coreta pois valida o próprio arquivo que gerou os metadados, então vamos modificar um pouco os metadados e fazer a validação do arquivo novamente.

```python script
from frictionless import describe

resource = describe('data/capital-invalid.csv')
resource.path = 'capital-invalid.csv'
resource.add_defined('stats')  # TODO: fix and remove this line
resource.stats.md5 = 'ae23c74693ca2d3f0e38b9ba3570775b' # this is a made up incorrect
resource.stats.bytes = 100 # this is wrong
resource.to_yaml('data/capital.resource-bad.yaml')
```

```python script
from frictionless import validate

report = validate('data/capital.resource-bad.yaml')
print(report)
```

## Validating a Package

```python script
with open('data/capital-valid.csv') as file:
    print(file.read())
```

```python script
from frictionless import describe, validate

# create package descriptor
package = describe("data/capital-*id.csv")
package.to_yaml("data/capital.package.yaml")
# validate
report = validate("data/capital.package.yaml")
print(report)
```

## Validating an Inquiry

The Inquiry is an advanced concept mostly used by software integrators. For example, under the hood, Frictionless Framework uses inquiries to implement client-server validation within the built-in API. Please skip this section if this information feels unnecessary for you.

Skipped.

## Validation Report

Analisando erro de colunas duplicadas.

```python script
from frictionless import validate

report = validate('data/capital-invalid.csv', pick_errors=['duplicate-label'])
print(report)
```

Simplificando o relatório de validação, ou seja, selecionando apenas algumas propriedades do json.
```python script
from frictionless import validate

report = validate('data/capital-invalid.csv', pick_errors=['duplicate-label'])
print(report.flatten(["rowNumber", "fieldNumber", "code", "message"]))
```

In some situations, an error can't be associated with a task; then it goes to the top-level report.errors property:
```python script
# from frictionless import validate

# report = validate("bad.json", type='schema')
# print(report)
```

## Validation Errors

The Error object is at the heart of the validation process. The Report has report.errors and report.tasks[].errors, properties that can contain the Error object. Let's explore it by taking a deeper look at the duplicate-label error:
```python script
from frictionless import validate

report = validate("data/capital-invalid.csv", pick_errors=["duplicate-label"])
error = report.error  # this is only available for one table / one error sitution
print(error)
```
Também é possível retornar apenas algumas propriedades do erro
```python script
from frictionless import validate

report = validate("data/capital-invalid.csv", pick_errors=["duplicate-label"])
error = report.error  # this is only available for one table / one error sitution
print(f'Type: "{error.type}"')
print(f'Title: "{error.title}"')
print(f'Tags: "{error.tags}"')
print(f'Note: "{error.note}"')
print(f'Message: "{error.message}"')
print(f'Description: "{error.description}"')

```

## Available Checks

Também é possível customizar os relatórios.
```python script
from pprint import pprint
from frictionless import validate, checks

checks = [checks.sequential_value(field_name='id')]
report = validate('data/capital-invalid.csv', checks=checks)
pprint(report.flatten(["rowNumber", "fieldNumber", "type", "note"]))
```

```python script
```

```python script
```

```python script
```

```python script
```