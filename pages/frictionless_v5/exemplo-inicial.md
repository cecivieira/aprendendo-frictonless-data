# Introdução

```bash script
frictionless validate data/invalid.csv
```

# Atualização de versão

Para atualizar a versão do frictionless, rode o seguinte comando:

'''
pip install --upgrade frictionless
'''

# Uso

Extração de dados - exemplo inicial
```python script
from frictionless import extract
from pprint import pprint

rows = extract('data/invalid.csv')
pprint(rows)
```
Impressão do arquivo na tela

```python script
with open('data/invalid.csv') as file:
    print(file.read())
```

Describe - exemplo inicial

```python script
from frictionless import describe
from pprint import pprint

resource = describe('data/invalid.csv')
pprint(resource)
```

Extract - exemplo inicial
```python script
from frictionless import extract
from pprint import pprint

resource = extract('data/invalid.csv')
pprint(resource)
```

Validate - exemplo inicial
```python script
from frictionless import validate
from pprint import pprint

resource = validate('data/invalid.csv')
pprint(resource.flatten(['rowNumber', 'fieldNumber', 'type']))
```