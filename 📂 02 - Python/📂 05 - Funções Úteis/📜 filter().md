`filter()` é uma função nativa do Python utilizada para filtrar elementos de um objeto iterável.

Ela percorre todos os elementos e mantém apenas aqueles cuja condição seja verdadeira.

É muito utilizada para remover dados indesejados de listas.

---

# O que é?

Imagine uma lista.

```python
numeros = [

    1,

    2,

    3,

    4,

    5,

    6

]
```

Queremos manter apenas os números pares.

Sem `filter()`.

```python
resultado = []

for numero in numeros:

    if numero % 2 == 0:

        resultado.append(numero)

print(resultado)
```

Resultado.

```python
[
2,
4,
6
]
```

Com `filter()`.

```python
resultado = filter(

    lambda numero:

        numero % 2 == 0,

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

# Sintaxe

```python
filter(funcao, iteravel)
```

---

# Como ler a sintaxe?

```text
filter

↓

Função nativa

----------------------

funcao

↓

Decide quais elementos permanecem

----------------------

iteravel

↓

Objeto percorrido
```

---

# Parâmetros

## funcao

### O que é?

Função responsável por decidir se um elemento permanece ou será removido.

---

### É obrigatório?

✅ Sim.

---

### O que recebe?

Pode receber.

Funções próprias.

```python
def par(numero):

    return numero % 2 == 0
```

---

Funções anônimas.

```python
lambda numero:

    numero % 2 == 0
```

---

Também aceita.

```python
None
```

Veremos esse caso na Parte 2.

---

### O que deve retornar?

Sempre um valor verdadeiro ou falso.

Exemplos.

```python
True
```

Mantém o elemento.

---

```python
False
```

Remove o elemento.

---

## iteravel

### O que é?

Objeto percorrido pelo `filter()`.

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

Assim como.

```python
map()
```

e.

```python
zip()
```

O `filter()` não retorna uma lista.

Ele retorna.

```python
filter object
```

Exemplo.

```python
resultado = filter(

    lambda numero:

        numero > 2,

    [1,2,3]

)

print(resultado)
```

Resultado.

```text
<filter object at 0x...>
```

---

Para visualizar.

```python
print(

    list(resultado)

)
```

---

# Como funciona?

Visualmente.

```text
1

↓

False

↓

Remove

----------------

2

↓

True

↓

Mantém

----------------

3

↓

False

↓

Remove

----------------

4

↓

True

↓

Mantém
```

---

# Exemplo básico

```python
numeros = [

    1,

    2,

    3,

    4,

    5

]

resultado = filter(

    lambda numero:

        numero > 3,

    numeros

)

print(

    list(resultado)

)
```

Resultado.

```python
[
4,
5
]
```

---

# Utilizando função própria

```python
def maior_que_tres(numero):

    return numero > 3

resultado = filter(

    maior_que_tres,

    numeros

)

print(

    list(resultado)

)
```

Resultado.

```python
[
4,
5
]
```

---

# O que acontece quando a função retorna True?

O elemento permanece.

```text
Elemento

↓

Função

↓

True

↓

Mantém
```

---

# O que acontece quando retorna False?

O elemento é descartado.

```text
Elemento

↓

Função

↓

False

↓

Remove
```

---

# Comparação

## map()

Transforma.

```text
10

↓

20
```

---

## filter()

Filtra.

```text
10

↓

Permanece

---------------

5

↓

Remove
```

---

# Quando utilizar?

Quando precisar.

- Remover elementos.
- Filtrar dados.
- Selecionar apenas alguns valores.
- Limpar listas.

---

# Quando NÃO utilizar?

Quando precisar modificar os elementos.

Nesse caso.

Utilize.

```python
map()
```

ou.

```python
List Comprehension
```

---

# Boas práticas

✅ Utilize quando o objetivo for apenas filtrar.

✅ A função deve retornar sempre um valor booleano (`True` ou `False`) para deixar o código claro.

✅ Prefira nomes descritivos nas funções de filtro.

```python
def usuario_ativo(usuario):

    return usuario["ativo"]
```

É mais claro do que.

```python
def teste(usuario):

    ...
```