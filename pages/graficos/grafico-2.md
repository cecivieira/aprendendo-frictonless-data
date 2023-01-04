# Gráfico modificado

```yaml chart
data:
  url: ../../data/cars.csv
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
    value: red
  size:
    type: quantitative
    field: bhp
width: 500
height: 300
```

## Capítulo secundário 1

jahajhjha

## Capítulo secundário 2

gagagaga

### Capítulo terciário 1
hfytydyf

### Capítulo terciário 2
hfytydyf