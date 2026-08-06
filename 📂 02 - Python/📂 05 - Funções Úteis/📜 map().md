`map()` é uma função nativa do Python utilizada para aplicar uma função a todos os elementos de um objeto iterável.

Ela percorre o iterável, aplica a função informada e retorna os novos valores.

É muito utilizada para transformar dados.

---

# O que é?

Imagine uma lista.

```python
numeros = [1, 2, 3, 4, 5]
```

Queremos multiplicar todos por 2.

Sem `map()`.

```python
resultado = []

for numero in numeros:

    resultado.append(numero * 2)

print(resultado)
```

Resultado.

```python
[2, 4, 6, 8, 10]
```

Com `map()`.

```python
resultado = map(

    lambda numero: numero * 2,

    numeros

)

print(list(resultado))
```

Resultado.

```python
[2, 4, 6, 8, 10]
```

---

# Sintaxe

```python
map(funcao, iteravel)
```

---

# Como ler a sintaxe?

```text
map

↓

Função nativa

---------------------

funcao

↓

Função aplicada em cada elemento

---------------------

iteravel

↓

Objeto percorrido
```

---

# Parâmetros

## funcao

### O que é?

Função que será executada para cada elemento.

---

### É obrigatório?

✅ Sim.

---

### O que recebe?

Pode receber.

Funções nativas.

```python
int

str

float

len

abs
```

---

Funções próprias.

```python
def dobro(numero):

    return numero * 2
```

---

Funções anônimas.

```python
lambda numero:

    numero * 2
```

---

Métodos.

```python
str.upper

str.lower

str.strip
```

---

### Pode receber mais de um parâmetro?

Sim.

Desde que existam vários iteráveis.

Exemplo.

```python
map(

    lambda x, y: x + y,

    lista1,

    lista2

)
```

---

## iteravel

### O que é?

Objeto percorrido.

---

### É obrigatório?

✅ Sim.

---

### O que recebe?

Qualquer objeto iterável.

- list
- tuple
- set
- dict
- str
- range
- enumerate
- zip
- generators

---

# Valor de retorno

O `map()` NÃO retorna uma lista.

Ele retorna.

```python
map object
```

Exemplo.

```python
resultado = map(

    str,

    [1,2,3]

)

print(resultado)
```

Saída.

```text
<map object at 0x...>
```

---

Para visualizar.

```python
print(

    list(resultado)

)
```

Resultado.

```python
['1','2','3']
```

---

# Como funciona?

Visualmente.

```text
1

↓

função

↓

2

----------------

2

↓

função

↓

4

----------------

3

↓

função

↓

6
```

---

# Exemplo básico

```python
numeros = [

    1,

    2,

    3

]

resultado = map(

    lambda numero:

        numero * 2,

    numeros

)

print(

    list(resultado)

)
```

Resultado.

```python
[
2,
4,
6
]
```

---

# Utilizando funções prontas

Convertendo para inteiro.

```python
numeros = [

    "10",

    "20",

    "30"

]

resultado = map(

    int,

    numeros

)

print(

    list(resultado)

)
```

Resultado.

```python
[
10,
20,
30
]
```

---

Convertendo para string.

```python
map(

    str,

    numeros
)
```

---

Convertendo para float.

```python
map(

    float,

    numeros
)
```

---

Maiúsculas.

```python
nomes = [

    "ana",

    "carlos"

]

resultado = map(

    str.upper,

    nomes

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'ANA',

'CARLOS'
]
```

---

# Comparação

## map()

Transforma elementos.

```text
10

↓

"10"
```

---

## filter()

Filtra elementos.

```text
10

↓

Permanece

----------------

5

↓

Remove
```

---

# Quando utilizar?

Quando todos os elementos precisam sofrer a mesma transformação.

Exemplos.

- Converter string para inteiro.
- Converter para float.
- Colocar tudo em maiúsculo.
- Remover espaços.
- Aplicar uma função em todos os elementos.

---

# Quando NÃO utilizar?

Quando precisar filtrar elementos.

Nesse caso.

Utilize.

```python
filter()
```

---

# Boas práticas

✅ Utilize funções prontas sempre que possível.

Prefira.

```python
map(

    int,

    numeros

)
```

Ao invés de.

```python
map(

    lambda numero:

        int(numero),

    numeros

)
```

---

✅ Utilize `list()` apenas quando realmente precisar armazenar todos os resultados.

Caso contrário.

Percorra diretamente.

```python
for item in map(

    str.upper,

    nomes

):

    print(item)
```

---

# Resumo

| Recurso | map() |
|----------|:-----:|
| Função nativa | ✅ |
| Transforma elementos | ✅ |
| Retorna map object | ✅ |
| Funciona com iteráveis | ✅ |
| Aceita lambda | ✅ |
| Aceita funções | ✅ |
| Aceita métodos | ✅ |
