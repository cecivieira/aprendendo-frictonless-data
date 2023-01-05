# My Project

It will be great here

```html markup
<i class="fas fa-heart"></i>
```

Python task

```python task id=data-e
print('Data Extract')
```
```python task id=data-t
print('Data Transform')
```
```python task id=data-l
print('Data Load')
```

As tasks servem para determinar funcionalidades. Para rodar uma task é necessário inserir o seguinte trecho de código:

```
'''bash script
$ livemark run NOME-TASK
'''
```

Para listar todas as funcionalidades existentes, execute:

```
'''bash script
$ livemark run
'''
```


Python script

```python script
print("Hello World")
```

-------------------

```yaml chart
data:
  url: data/cars.csv
mark: circle
selection:
  brush:
    type: interval
encoding:
  x:
    type: quantitative
    field: kmpl
    scale:
     domain: [12,25]
  y:
    type: quantitative
    field: price
    scale:
     domain: [100,900]
  color:
    condition:
      selection: brush
      field: type
      type: nominal
    value: grey
  size:
    type: quantitative
    field: bhp
width: 500
height: 300
```

## Tipos possíveis de remark

```markdown remark type=info
Remark tipo info
```

```markdown remark type=danger
Remark tipo danger
```

```markdown remark type=success
Remark tipo success
```

```markdown remark type=warning
Remark tipo warning
```

## Scroll infinito

```html markup
<div class="livemark-infinity">
  {% for number in range(1, 10) %}
  <div>{{ number }}</div>
  {% endfor %}
</div>
```

## Paginação

```html markup
<div class="livemark-pagination">
  {% for number in range(1, 1001) %}
  <div>{{ number }}</div>
  {% endfor %}
</div>
```