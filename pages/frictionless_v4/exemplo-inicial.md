# Exemplo inicial

## Abrir o arquivo
```python script
with open('data/invalid.csv') as arquivo:
    print(arquivo.read())
```

## Descrever o arquivo
```python task id=data-describe
from frictionless import describe

resource = describe('data/invalid.csv')
print(resource)
```

```bash script
livemark run data-describe
```

## Extract
```python task id=data-extract
from frictionless import extract
from pprint import pprint

rows = extract('data/invalid.csv')
pprint(rows)
```

```bash script
livemark run data-extract
```

## Validate
```python task id=data-validate
from frictionless import validate
from pprint import pprint

report = validate('data/invalid.csv')
flatten_2 = report.flatten(["rowPosition", "fieldPosition", "code"])
pprint(flatten_2)
```

```bash script
livemark run data-validate
```