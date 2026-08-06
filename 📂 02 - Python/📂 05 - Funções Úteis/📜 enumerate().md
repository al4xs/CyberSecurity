`enumerate()` é uma função nativa do Python utilizada para percorrer objetos iteráveis adicionando automaticamente um índice para cada elemento.

Ela elimina a necessidade de controlar manualmente um contador.

É uma das funções mais utilizadas em código Python moderno.

---

# O que é um índice?

O índice representa a posição de um elemento dentro de uma sequência.

Exemplo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]
```

Visualmente.

```text
Índice

0 → Ana

1 → Carlos

2 → Pedro
```

Normalmente acessamos um elemento assim.

```python
nomes[0]
```

Resultado.

```python
Ana
```

---

# Problema

Imagine.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for nome in nomes:

    print(nome)
```

Resultado.

```text
Ana

Carlos

Pedro
```

Conseguimos acessar o elemento.

Mas não sabemos sua posição.

---

# Solução

Utilizar.

```python
enumerate()
```

---

# Sintaxe

```python
enumerate(iterable, start=0)
```

---

# Como ler essa sintaxe?

```text
enumerate

↓

Função nativa

----------------------

iterable

↓

Objeto que será percorrido

----------------------

start

↓

Valor inicial do contador
```

---

# Parâmetros

## iterable

### O que é?

Objeto que será percorrido.

---

### É obrigatório?

✅ Sim.

---

### O que recebe?

Qualquer objeto iterável.

Exemplos.

Lista.

```python
enumerate(lista)
```

---

Tupla.

```python
enumerate(tupla)
```

---

String.

```python
enumerate("python")
```

---

Set.

```python
enumerate({1,2,3})
```

---

Dicionário.

```python
enumerate(dicionario)
```

---

Range.

```python
enumerate(range(10))
```

---

### O que NÃO recebe?

Objetos que não podem ser percorridos.

Exemplo.

```python
enumerate(100)
```

Resultado.

```text
TypeError

'int' object is not iterable
```

---

# start

## O que é?

Define o valor inicial do contador.

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
0
```

Ou seja.

```python
enumerate(lista)
```

é exatamente igual a.

```python
enumerate(

    lista,

    start=0

)
```

---

## O que recebe?

Recebe um número inteiro.

Exemplos.

```python
start=0
```

---

```python
start=1
```

---

```python
start=100
```

---

Também aceita valores negativos.

```python
start=-5
```

---

# Valor de retorno

Uma dúvida muito comum.

`enumerate()` NÃO retorna uma lista.

Ele retorna um objeto do tipo.

```python
enumerate
```

Exemplo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

resultado = enumerate(nomes)

print(resultado)
```

Saída.

```text
<enumerate object at 0x...>
```

---

# Transformando em lista

Podemos visualizar seu conteúdo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

print(

    list(

        enumerate(nomes)

    )

)
```

Resultado.

```python
[
    (0, 'Ana'),

    (1, 'Carlos'),

    (2, 'Pedro')
]
```

Observe.

Cada elemento virou uma tupla.

```text
(índice, valor)
```

---

# Como funciona internamente?

Imagine.

```python
enumerate(nomes)
```

O Python cria algo semelhante.

```text
(0, Ana)

↓

(1, Carlos)

↓

(2, Pedro)
```

Depois o `for` desempacota essas tuplas.

```python
for indice, nome in enumerate(nomes):

    print(indice, nome)
```

Visualmente.

```text
(0, Ana)

↓

indice = 0

nome = Ana

------------------------

(1, Carlos)

↓

indice = 1

nome = Carlos

------------------------

(2, Pedro)

↓

indice = 2

nome = Pedro
```

É por isso que conseguimos utilizar duas variáveis.

```python
indice

nome
```

---

# Exemplo básico

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for indice, nome in enumerate(nomes):

    print(indice, nome)
```

Saída.

```text
0 Ana

1 Carlos

2 Pedro
```

---

# Utilizando apenas uma variável

Também é possível.

```python
for item in enumerate(nomes):

    print(item)
```

Resultado.

```python
(0, 'Ana')

(1, 'Carlos')

(2, 'Pedro')
```

Observe.

Agora cada elemento é uma tupla.

---

# Resumo da Parte 1

| Parâmetro | Obrigatório | Valor padrão |
|-----------|:-----------:|:------------:|
| iterable | ✅ | — |
| start | ❌ | 0 |

---

| Retorno |
|---------|
| enumerate object |

