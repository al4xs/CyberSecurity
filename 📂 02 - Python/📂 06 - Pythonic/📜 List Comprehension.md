List Comprehension é uma forma mais curta, elegante e "Pythonica" de criar listas.

Ela permite criar uma nova lista utilizando uma única expressão.

É um dos recursos mais utilizados em projetos Python modernos.

---

# O que é?

Normalmente criamos listas utilizando um laço de repetição.

Exemplo.

```python
numeros = []

for numero in range(5):

    numeros.append(numero)

print(numeros)
```

Saída.

```python
[0, 1, 2, 3, 4]
```

List Comprehension faz exatamente a mesma coisa.

```python
numeros = [

    numero

    for numero in range(5)

]

print(numeros)
```

Resultado.

```python
[0, 1, 2, 3, 4]
```

Observe.

O resultado é exatamente igual.

A diferença está apenas na forma de escrever.

---

# Por que ela existe?

Antes da List Comprehension era muito comum escrever.

```python
resultado = []

for item in lista:

    resultado.append(item)
```

Esse padrão aparecia milhares de vezes.

A linguagem passou a oferecer uma forma mais simples.

```python
resultado = [

    item

    for item in lista

]
```

O código ficou.

- menor
- mais limpo
- mais fácil de ler

---

# Sintaxe

```python
[expressao for variavel in iteravel]
```

---

# Como ler essa sintaxe?

Vamos separar.

```python
[

expressao

for

variavel

in

iteravel

]
```

Cada parte possui uma função.

---

# expressao

## O que é?

É o valor que será colocado dentro da nova lista.

Pode ser.

Um número.

```python
numero
```

Uma operação.

```python
numero * 2
```

Uma função.

```python
len(nome)
```

Um objeto.

```python
usuario.nome
```

Qualquer expressão válida do Python.

---

# for

É o laço de repetição.

Funciona exatamente igual.

```python
for numero in lista:
```

---

# variavel

Representa cada elemento percorrido.

Exemplo.

```python
for nome in nomes
```

A variável é.

```python
nome
```

---

# in

Indica de onde os dados serão obtidos.

Exemplo.

```python
for nome in nomes
```

O Python percorre.

```python
nomes
```

---

# iteravel

Representa o objeto percorrido.

Pode ser.

- list
- tuple
- set
- dict
- str
- range()
- enumerate()
- zip()
- generator
- qualquer objeto iterável

---

# Como funciona internamente?

Imagine.

```python
quadrados = [

    numero ** 2

    for numero in range(5)

]
```

O Python faz algo parecido com.

```python
quadrados = []

for numero in range(5):

    quadrados.append(

        numero ** 2

    )
```

Observe.

A List Comprehension é apenas uma forma reduzida de escrever um `for`.

---

# Primeiro exemplo

Criando uma lista.

```python
numeros = [

    numero

    for numero in range(5)

]

print(numeros)
```

Resultado.

```python
[0,1,2,3,4]
```

---

# Outro exemplo

Dobrando valores.

```python
dobro = [

    numero * 2

    for numero in range(5)

]

print(dobro)
```

Resultado.

```python
[0,2,4,6,8]
```

Observe.

A expressão.

```python
numero * 2
```

É executada para cada elemento.

---

# Utilizando Strings

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

maiusculas = [

    nome.upper()

    for nome in nomes

]

print(maiusculas)
```

Resultado.

```python
[
'ANA',

'CARLOS',

'PEDRO'
]
```

---

# Utilizando funções

Também podemos chamar funções.

```python
palavras = [

    "python",

    "bash",

    "assembly"

]

tamanhos = [

    len(palavra)

    for palavra in palavras

]

print(tamanhos)
```

Resultado.

```python
[
6,

4,

8
]
```

---

# Comparação com o for

Forma tradicional.

```python
resultado = []

for numero in range(10):

    resultado.append(

        numero * 2

    )
```

---

List Comprehension.

```python
resultado = [

    numero * 2

    for numero in range(10)

]
```

Resultado.

O mesmo.

---

# Quando utilizar?

Sempre que o objetivo for.

- Criar uma nova lista.
- Transformar dados.
- Deixar o código mais simples.
- Evitar um `for` muito pequeno.

---

# Quando NÃO utilizar?

Se a lógica começar a ficar complicada.

Exemplo.

```python
resultado = []

for usuario in usuarios:

    if ...

        ...

    else:

        ...

    ...
```

Nesses casos.

O `for` tradicional costuma ser mais legível.

Veremos isso nas próximas partes.

---

# Resumo da Parte 1

Uma List Comprehension possui quatro partes principais.

```python
[

expressao

for

variavel

in

iteravel

]
```

Visualmente.

```text
Expressão

↓

Valor que será armazenado

----------------------

for

↓

Percorre os elementos

----------------------

Variável

↓

Elemento atual

----------------------

Iterável

↓

Objeto percorrido
```

# 📜 List Comprehension

List Comprehension é uma forma mais curta, elegante e "Pythonica" de criar listas.

Ela permite criar uma nova lista utilizando uma única expressão.

É um dos recursos mais utilizados em projetos Python modernos.

---

# O que é?

Normalmente criamos listas utilizando um laço de repetição.

Exemplo.

```python
numeros = []

for numero in range(5):

    numeros.append(numero)

print(numeros)
```

Saída.

```python
[0, 1, 2, 3, 4]
```

List Comprehension faz exatamente a mesma coisa.

```python
numeros = [

    numero

    for numero in range(5)

]

print(numeros)
```

Resultado.

```python
[0, 1, 2, 3, 4]
```

Observe.

O resultado é exatamente igual.

A diferença está apenas na forma de escrever.

---

# Por que ela existe?

Antes da List Comprehension era muito comum escrever.

```python
resultado = []

for item in lista:

    resultado.append(item)
```

Esse padrão aparecia milhares de vezes.

A linguagem passou a oferecer uma forma mais simples.

```python
resultado = [

    item

    for item in lista

]
```

O código ficou.

- menor
- mais limpo
- mais fácil de ler

---

# Sintaxe

```python
[expressao for variavel in iteravel]
```

---

# Como ler essa sintaxe?

Vamos separar.

```python
[

expressao

for

variavel

in

iteravel

]
```

Cada parte possui uma função.

---

# expressao

## O que é?

É o valor que será colocado dentro da nova lista.

Pode ser.

Um número.

```python
numero
```

Uma operação.

```python
numero * 2
```

Uma função.

```python
len(nome)
```

Um objeto.

```python
usuario.nome
```

Qualquer expressão válida do Python.

---

# for

É o laço de repetição.

Funciona exatamente igual.

```python
for numero in lista:
```

---

# variavel

Representa cada elemento percorrido.

Exemplo.

```python
for nome in nomes
```

A variável é.

```python
nome
```

---

# in

Indica de onde os dados serão obtidos.

Exemplo.

```python
for nome in nomes
```

O Python percorre.

```python
nomes
```

---

# iteravel

Representa o objeto percorrido.

Pode ser.

- list
- tuple
- set
- dict
- str
- range()
- enumerate()
- zip()
- generator
- qualquer objeto iterável

---

# Como funciona internamente?

Imagine.

```python
quadrados = [

    numero ** 2

    for numero in range(5)

]
```

O Python faz algo parecido com.

```python
quadrados = []

for numero in range(5):

    quadrados.append(

        numero ** 2

    )
```

Observe.

A List Comprehension é apenas uma forma reduzida de escrever um `for`.

---

# Primeiro exemplo

Criando uma lista.

```python
numeros = [

    numero

    for numero in range(5)

]

print(numeros)
```

Resultado.

```python
[0,1,2,3,4]
```

---

# Outro exemplo

Dobrando valores.

```python
dobro = [

    numero * 2

    for numero in range(5)

]

print(dobro)
```

Resultado.

```python
[0,2,4,6,8]
```

Observe.

A expressão.

```python
numero * 2
```

É executada para cada elemento.

---

# Utilizando Strings

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

maiusculas = [

    nome.upper()

    for nome in nomes

]

print(maiusculas)
```

Resultado.

```python
[
'ANA',

'CARLOS',

'PEDRO'
]
```

---

# Utilizando funções

Também podemos chamar funções.

```python
palavras = [

    "python",

    "bash",

    "assembly"

]

tamanhos = [

    len(palavra)

    for palavra in palavras

]

print(tamanhos)
```

Resultado.

```python
[
6,

4,

8
]
```

---

# Comparação com o for

Forma tradicional.

```python
resultado = []

for numero in range(10):

    resultado.append(

        numero * 2

    )
```

---

List Comprehension.

```python
resultado = [

    numero * 2

    for numero in range(10)

]
```

Resultado.

O mesmo.

---

# Quando utilizar?

Sempre que o objetivo for.

- Criar uma nova lista.
- Transformar dados.
- Deixar o código mais simples.
- Evitar um `for` muito pequeno.

---

# Quando NÃO utilizar?

Se a lógica começar a ficar complicada.

Exemplo.

```python
resultado = []

for usuario in usuarios:

    if ...

        ...

    else:

        ...

    ...
```

Nesses casos.

O `for` tradicional costuma ser mais legível.

Veremos isso nas próximas partes.

---

# Resumo da Parte 1

Uma List Comprehension possui quatro partes principais.

```python
[

expressao

for

variavel

in

iteravel

]
```

Visualmente.

```text
Expressão

↓

Valor que será armazenado

----------------------

for

↓

Percorre os elementos

----------------------

Variável

↓

Elemento atual

----------------------

Iterável

↓

Objeto percorrido
```

