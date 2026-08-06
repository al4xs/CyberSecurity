`zip()` é uma função nativa do Python utilizada para agrupar elementos de dois ou mais objetos iteráveis.

Ela percorre todos os iteráveis ao mesmo tempo, retornando pares (ou grupos) de elementos que ocupam a mesma posição.

É muito utilizada quando precisamos trabalhar com listas relacionadas entre si.

---

# O que significa "zip"?

O nome vem do inglês.

Imagine um zíper.

```text
Lista A

A
B
C

↓

Lista B

1
2
3

↓

zip()

↓

(A,1)

(B,2)

(C,3)
```

Ele "fecha o zíper", unindo elementos da mesma posição.

---

# Problema

Imagine.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

idades = [

    20,

    30,

    25

]
```

Queremos imprimir.

```text
Ana -> 20

Carlos -> 30

Pedro -> 25
```

Sem `zip()` normalmente faríamos.

```python
for i in range(len(nomes)):

    print(

        nomes[i],

        idades[i]

    )
```

Funciona.

Mas existe uma forma muito mais elegante.

---

# Solução

Utilizando.

```python
zip()
```

---

# Sintaxe

```python
zip(*iterables, strict=False)
```

---

# Como ler essa sintaxe?

```text
zip

↓

Função nativa

--------------------------

*iterables

↓

Um ou mais objetos iteráveis

--------------------------

strict

↓

Verifica se todos possuem o mesmo tamanho
```

---

# Parâmetros

## *iterables

### O que é?

Representa os objetos que serão percorridos simultaneamente.

---

### É obrigatório?

✅ Sim.

É necessário informar pelo menos um iterável.

---

### O que recebe?

Pode receber.

- list
- tuple
- set
- str
- dict
- range
- generator
- qualquer objeto iterável

---

### Posso passar apenas uma lista?

Sim.

```python
zip(lista)
```

Resultado.

Cada elemento será colocado dentro de uma tupla.

---

### Posso passar várias?

Sim.

```python
zip(

    lista1,

    lista2

)
```

Também.

```python
zip(

    lista1,

    lista2,

    lista3

)
```

Não existe limite prático pequeno.

---

# strict

## O que é?

Controla se todos os iteráveis devem possuir exatamente o mesmo tamanho.

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
False
```

Ou seja.

```python
zip(

    lista1,

    lista2

)
```

é igual a.

```python
zip(

    lista1,

    lista2,

    strict=False

)
```

---

## Valores aceitos

```python
True
```

ou.

```python
False
```

---

### Quando utilizar?

Quando você deseja garantir que todas as listas possuam o mesmo número de elementos.

Veremos exemplos completos na Parte 2.

---

# Valor de retorno

Assim como `enumerate()`.

`zip()` não retorna uma lista.

Ele retorna.

```python
zip object
```

Exemplo.

```python
nomes = [

    "Ana",

    "Carlos"

]

idades = [

    20,

    30

]

resultado = zip(

    nomes,

    idades

)

print(resultado)
```

Saída.

```text
<zip object at 0x...>
```

---

# Transformando em lista

Podemos visualizar.

```python
nomes = [

    "Ana",

    "Carlos"

]

idades = [

    20,

    30

]

print(

    list(

        zip(

            nomes,

            idades

        )

    )

)
```

Resultado.

```python
[
    ('Ana',20),

    ('Carlos',30)
]
```

Observe.

Cada elemento virou uma tupla.

---

# Como funciona internamente?

Imagine.

```python
zip(

    nomes,

    idades

)
```

O Python cria.

```text
("Ana",20)

↓

("Carlos",30)
```

Depois.

O `for` desempacota automaticamente.

```python
for nome, idade in zip(

    nomes,

    idades

):

    print(nome, idade)
```

Visualmente.

```text
("Ana",20)

↓

nome = Ana

idade = 20

----------------------

("Carlos",30)

↓

nome = Carlos

idade = 30
```

---

# Exemplo básico

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

idades = [

    20,

    30,

    25

]

for nome, idade in zip(

    nomes,

    idades

):

    print(

        nome,

        idade

    )
```

Resultado.

```text
Ana 20

Carlos 30

Pedro 25
```

---

# Utilizando apenas uma variável

Também é possível.

```python
for item in zip(

    nomes,

    idades

):

    print(item)
```

Resultado.

```python
('Ana',20)

('Carlos',30)

('Pedro',25)
```

Cada elemento é uma tupla.

---

# O que é desempacotamento?

Quando fazemos.

```python
for nome, idade in zip(...):
```

O Python realiza automaticamente.

```python
tupla = (

    "Ana",

    20

)

nome = tupla[0]

idade = tupla[1]
```

Isso recebe o nome de.

```text
Desempacotamento
```

É um recurso muito utilizado em Python.

---

# Resumo da Parte 1

| Parâmetro | Obrigatório | Valor padrão |
|-----------|:-----------:|:------------:|
| *iterables | ✅ | — |
| strict | ❌ | False |

---

| Retorno |
|---------|
| zip object |

