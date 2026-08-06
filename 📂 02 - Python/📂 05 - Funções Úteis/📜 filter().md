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

---

# Utilizando lambda com filter()

A forma mais comum de utilizar o `filter()` é junto com `lambda`.

Exemplo.

```python
numeros = [

    1,

    2,

    3,

    4,

    5,

    6

]

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

# Como funciona?

O Python faz algo semelhante.

```python
resultado = []

for numero in numeros:

    if numero % 2 == 0:

        resultado.append(numero)
```

O resultado será exatamente o mesmo.

---

# Utilizando funções próprias

Também podemos utilizar funções.

```python
def par(numero):

    return numero % 2 == 0

resultado = filter(

    par,

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

Essa abordagem costuma ser melhor quando a lógica é maior.

---

# filter(None, iteravel)

Existe um comportamento especial.

Quando utilizamos.

```python
None
```

o próprio Python remove todos os valores considerados falsos.

Sintaxe.

```python
filter(

    None,

    iteravel

)
```

---

# Quais valores são considerados falsos?

```python
False

None

0

0.0

''

[]

{}

()

set()
```

Todos serão removidos.

---

# Exemplo

```python
dados = [

    "",

    "Ana",

    None,

    "Carlos",

    0,

    "Pedro"

]

resultado = filter(

    None,

    dados

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'Ana',

'Carlos',

'Pedro'
]
```

---

# Outro exemplo

```python
usuarios = [

    "admin",

    "",

    "guest",

    "",

    "root"

]

usuarios = list(

    filter(

        None,

        usuarios

    )

)

print(usuarios)
```

Resultado.

```python
[
'admin',

'guest',

'root'
]
```

Muito utilizado ao ler arquivos.

---

# Trabalhando com Strings

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro",

    "Jo"

]

resultado = filter(

    lambda nome:

        len(nome) > 3,

    nomes

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'Carlos',

'Pedro'
]
```

---

# Trabalhando com dicionários

Imagine.

```python
usuarios = [

    {

        "nome":"Ana",

        "ativo":True

    },

    {

        "nome":"Carlos",

        "ativo":False

    },

    {

        "nome":"Pedro",

        "ativo":True

    }

]
```

Filtrando apenas usuários ativos.

```python
resultado = filter(

    lambda usuario:

        usuario["ativo"],

    usuarios

)

print(

    list(resultado)

)
```

Resultado.

```python
[
{'nome':'Ana','ativo':True},

{'nome':'Pedro','ativo':True}
]
```

---

# Trabalhando com objetos

```python
class Usuario:

    def __init__(self, nome, ativo):

        self.nome = nome

        self.ativo = ativo


usuarios = [

    Usuario("Ana", True),

    Usuario("Carlos", False),

    Usuario("Pedro", True)

]

resultado = filter(

    lambda usuario:

        usuario.ativo,

    usuarios

)

for usuario in resultado:

    print(usuario.nome)
```

Resultado.

```text
Ana

Pedro
```

---

# Exemplo em automação

Imagine um arquivo.

```text
admin

guest


root


backup
```

Após ler.

```python
linhas = [

    "admin",

    "",

    "guest",

    "",

    "root"

]

usuarios = list(

    filter(

        None,

        linhas

    )

)
```

Resultado.

```python
[
'admin',

'guest',

'root'
]
```

Muito utilizado para limpar listas.

---

# Exemplo em Pentest

Imagine.

```python
portas = [

    22,

    80,

    135,

    139,

    445,

    8080
]
```

Selecionando apenas portas acima de 1024.

```python
resultado = list(

    filter(

        lambda porta:

            porta > 1024,

        portas

    )

)

print(resultado)
```

Resultado.

```python
[
8080
]
```

---

# Outro exemplo

Hosts ativos.

```python
hosts = [

    {

        "ip":"10.10.10.5",

        "ativo":True

    },

    {

        "ip":"10.10.10.20",

        "ativo":False

    },

    {

        "ip":"10.10.10.30",

        "ativo":True

    }

]

ativos = list(

    filter(

        lambda host:

            host["ativo"],

        hosts

    )

)

print(ativos)
```

Resultado.

```python
[
{'ip':'10.10.10.5','ativo':True},

{'ip':'10.10.10.30','ativo':True}
]
```

---

# Forma mais utilizada

Remover elementos vazios.

```python
filter(

    None,

    lista

)
```

---

Filtrar números.

```python
filter(

    lambda numero:

        numero > 10,

    numeros

)
```

---

Filtrar objetos.

```python
filter(

    lambda usuario:

        usuario.ativo,

    usuarios

)
```

---

Filtrar dicionários.

```python
filter(

    lambda host:

        host["ativo"],

    hosts

)
```

---

# Boas práticas

✅ Utilize `filter()` apenas quando o objetivo for remover elementos.

✅ Utilize `None` quando quiser eliminar valores considerados falsos.

✅ Prefira funções próprias quando a condição for complexa.

✅ Utilize `lambda` apenas para filtros simples.

✅ Converta para `list()` somente quando precisar armazenar todos os resultados.

---

# Comparação com for

O `filter()` e o `for` resolvem o mesmo problema.

A diferença está na forma de escrever.

---

## Utilizando for

```python
numeros = [

    1,

    2,

    3,

    4,

    5,

    6

]

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

---

## Utilizando filter()

```python
resultado = list(

    filter(

        lambda numero:

            numero % 2 == 0,

        numeros

    )

)

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

O resultado é exatamente o mesmo.

---

# filter() x List Comprehension

Essa é a comparação mais comum.

---

## filter()

```python
resultado = list(

    filter(

        lambda numero:

            numero % 2 == 0,

        numeros

    )

)
```

---

## List Comprehension

```python
resultado = [

    numero

    for numero in numeros

    if numero % 2 == 0

]
```

Mesmo resultado.

---

# Qual utilizar?

Na comunidade Python.

A List Comprehension costuma ser preferida quando o filtro é simples.

Ela normalmente é considerada mais legível.

---

## Exemplo

```python
usuarios = [

    "Ana",

    "",

    "Carlos"

]
```

Utilizando.

```python
filter(

    None,

    usuarios

)
```

Funciona.

Mas muitos preferem.

```python
[

    usuario

    for usuario in usuarios

    if usuario

]
```

As duas formas são válidas.

---

# filter() x map()

As funções possuem objetivos diferentes.

---

## map()

Transforma os elementos.

```text
10

↓

20
```

---

## filter()

Decide se o elemento permanece.

```text
10

↓

Mantém

----------------

5

↓

Remove
```

---

Resumo.

```text
map()

↓

Transformar

--------------------

filter()

↓

Filtrar
```

---

# Performance

Assim como.

```python
map()
```

e.

```python
zip()
```

O `filter()` retorna um objeto.

```python
filter object
```

Isso significa que os elementos são produzidos conforme necessário.

Somente quando fazemos.

```python
list(

    filter(...)

)
```

todos os resultados são carregados na memória.

---

# Como funciona internamente?

Imagine.

```python
filter(

    lambda numero:

        numero % 2 == 0,

    numeros

)
```

O Python faz algo semelhante.

```python
for numero in numeros:

    if numero % 2 == 0:

        yield numero
```

Ou seja.

Os elementos são produzidos sob demanda.

---

# Erros comuns

## Erro 1

Esperar uma lista.

```python
resultado = filter(

    lambda numero:

        numero > 2,

    numeros

)

print(resultado)
```

Resultado.

```text
<filter object at ...>
```

Correto.

```python
print(

    list(resultado)

)
```

---

## Erro 2

Esquecer que a função deve retornar um valor verdadeiro ou falso.

Errado.

```python
filter(

    lambda numero:

        numero * 2,

    numeros

)
```

Embora funcione por causa do conceito de "truthy" e "falsy", ela não deixa claro que a intenção é filtrar.

Prefira.

```python
filter(

    lambda numero:

        numero % 2 == 0,

    numeros

)
```

---

## Erro 3

Utilizar `filter()` para transformar dados.

Errado.

```python
filter(

    lambda numero:

        numero * 2,

    numeros

)
```

Quem transforma é.

```python
map()
```

ou.

```python
List Comprehension
```

---

## Erro 4

Utilizar `filter()` quando uma List Comprehension fica mais clara.

Exemplo.

```python
[

    numero

    for numero in numeros

    if numero > 10

]
```

Na maioria dos projetos modernos essa forma é mais comum.

---

# Quando NÃO utilizar

Evite utilizar quando.

- Precisar modificar os elementos.
- Precisar executar várias operações.
- O filtro ficou complexo.

Nesses casos.

Prefira.

```python
for
```

ou.

```python
List Comprehension
```

---

# Como aparece em projetos Open Source

Você encontrará muito.

Removendo elementos vazios.

```python
filter(

    None,

    linhas

)
```

---

Selecionando arquivos.

```python
filter(

    lambda arquivo:

        arquivo.endswith(".php"),

    arquivos

)
```

---

Selecionando objetos.

```python
filter(

    lambda usuario:

        usuario.ativo,

    usuarios

)
```

---

Selecionando hosts.

```python
filter(

    lambda host:

        host["ativo"],

    hosts

)
```

---

Muito comum em projetos como.

- Django
- Flask
- Scrapy
- Scapy
- pwntools
- Impacket
- Requests

---

# Exemplos em Cyber Security

Filtrando portas abertas.

```python
portas = [

    22,

    23,

    80,

    443,

    8080

]

resultado = list(

    filter(

        lambda porta:

            porta != 23,

        portas

    )

)
```

Resultado.

```python
[
22,

80,

443,

8080
]
```

---

Filtrando IPs válidos.

```python
ips = [

    "10.10.10.5",

    "",

    "10.10.10.20"

]

ips = list(

    filter(

        None,

        ips

    )

)
```

Resultado.

```python
[
'10.10.10.5',

'10.10.10.20'
]
```

---

Selecionando vulnerabilidades críticas.

```python
vulnerabilidades = [

    {

        "cvss":9.8

    },

    {

        "cvss":4.2

    },

    {

        "cvss":8.5

    }

]

criticas = list(

    filter(

        lambda vulnerabilidade:

            vulnerabilidade["cvss"] >= 7,

        vulnerabilidades

    )

)
```

---

# Curiosidades

- `filter()` é uma função nativa do Python.
- Funciona com qualquer objeto iterável.
- Retorna um objeto `filter`.
- Trabalha com avaliação preguiçosa (*lazy evaluation*).
- Pode ser combinado com `map()`, `zip()` e `enumerate()`.

---

# Resumo

| Recurso | filter() |
|----------|:--------:|
| Função nativa | ✅ |
| Filtra elementos | ✅ |
| Transforma elementos | ❌ |
| Retorna filter object | ✅ |
| Funciona com iteráveis | ✅ |
| Aceita lambda | ✅ |
| Aceita funções próprias | ✅ |
| Aceita `None` | ✅ |

---

# Formas mais utilizadas

Filtrar números.

```python
filter(

    lambda numero:

        numero > 10,

    numeros

)
```

---

Remover valores vazios.

```python
filter(

    None,

    lista

)
```

---

Filtrar objetos.

```python
filter(

    lambda usuario:

        usuario.ativo,

    usuarios

)
```

---

Filtrar dicionários.

```python
filter(

    lambda host:

        host["ativo"],

    hosts

)
```

---

# Boas práticas

✅ Utilize `filter()` apenas quando o objetivo for selecionar elementos.

✅ Utilize `None` para remover valores considerados falsos.

✅ Prefira funções próprias quando a condição de filtro for grande ou reutilizável.

✅ Para filtros simples, a List Comprehension costuma ser mais legível.

✅ Lembre-se da diferença:

- `map()` → transforma.
- `filter()` → filtra.
- List Comprehension → pode transformar e filtrar ao mesmo tempo.

---

# Conclusão

`filter()` é uma ferramenta simples e eficiente para selecionar elementos de um iterável.

Apesar de ainda ser muito utilizada, em muitos projetos modernos você verá filtros escritos com **List Comprehension**, por serem considerados mais claros e idiomáticos em Python.

Conhecer ambas as abordagens permite entender e manter praticamente qualquer código Python encontrado em projetos reais.