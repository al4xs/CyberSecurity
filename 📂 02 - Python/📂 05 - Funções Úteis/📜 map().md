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
---

# Utilizando lambda com map()

O uso mais comum do `map()` é junto com `lambda`.

Isso permite transformar elementos sem criar uma função separada.

---

# Exemplo

```python
numeros = [

    1,

    2,

    3,

    4

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
6,
8
]
```

---

# Como funciona?

O Python faz algo semelhante.

```python
resultado = []

for numero in numeros:

    resultado.append(

        numero * 2

    )
```

O resultado será exatamente o mesmo.

---

# Trabalhando com Strings

```python
nomes = [

    "ana",

    "carlos",

    "pedro"

]

resultado = map(

    lambda nome:

        nome.capitalize(),

    nomes

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

# Trabalhando com dicionários

Imagine.

```python
usuarios = [

    {

        "nome":"Ana",

        "idade":20

    },

    {

        "nome":"Carlos",

        "idade":30

    }

]
```

Obtendo apenas os nomes.

```python
resultado = map(

    lambda usuario:

        usuario["nome"],

    usuarios

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'Ana',

'Carlos'
]
```

---

# Trabalhando com objetos

```python
class Usuario:

    def __init__(self, nome):

        self.nome = nome


usuarios = [

    Usuario("Ana"),

    Usuario("Carlos")

]

resultado = map(

    lambda usuario:

        usuario.nome,

    usuarios

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'Ana',

'Carlos'
]
```

---

# Trabalhando com dois iteráveis

O `map()` pode percorrer mais de um iterável ao mesmo tempo.

Sintaxe.

```python
map(

    funcao,

    iteravel1,

    iteravel2
)
```

---

# Exemplo

```python
lista1 = [

    1,

    2,

    3

]

lista2 = [

    10,

    20,

    30

]

resultado = map(

    lambda x, y:

        x + y,

    lista1,

    lista2

)

print(

    list(resultado)

)
```

Resultado.

```python
[
11,
22,
33
]
```

---

# Como funciona?

Visualmente.

```text
1 + 10

↓

11

----------------

2 + 20

↓

22

----------------

3 + 30

↓

33
```

---

# Se os iteráveis tiverem tamanhos diferentes

Assim como o `zip()`.

O `map()` para quando o menor iterável termina.

Exemplo.

```python
lista1 = [

    1,

    2,

    3

]

lista2 = [

    10,

    20

]

resultado = map(

    lambda x, y:

        x + y,

    lista1,

    lista2

)

print(

    list(resultado)

)
```

Resultado.

```python
[
11,
22
]
```

O número `3` será ignorado.

---

# map() + list()

É a forma mais utilizada.

```python
resultado = list(

    map(

        int,

        numeros

    )

)
```

Porque transforma imediatamente o objeto `map` em uma lista.

---

# map() + tuple()

Também é possível.

```python
resultado = tuple(

    map(

        int,

        numeros

    )

)
```

Resultado.

```python
(
10,
20,
30
)
```

---

# map() + set()

```python
resultado = set(

    map(

        str.upper,

        nomes

    )

)
```

---

# map() + zip()

Podemos combinar.

```python
nomes = [

    "Ana",

    "Carlos"

]

idades = [

    20,

    30

]

resultado = map(

    lambda dado:

        f"{dado[0]} ({dado[1]})",

    zip(

        nomes,

        idades

    )

)

print(

    list(resultado)

)
```

Resultado.

```python
[
'Ana (20)',

'Carlos (30)'
]
```

---

# Exemplo em automação

Lendo um arquivo.

```python
linhas = [

    " admin ",

    " root ",

    " guest "

]

usuarios = list(

    map(

        str.strip,

        linhas

    )

)

print(usuarios)
```

Resultado.

```python
[
'admin',

'root',

'guest'
]
```

Muito utilizado para limpar dados.

---

# Exemplo em Pentest

Imagine uma lista de IPs.

```python
ips = [

    "10.10.10.5",

    "10.10.10.20"

]
```

Criando URLs.

```python
urls = list(

    map(

        lambda ip:

            f"http://{ip}",

        ips

    )

)

print(urls)
```

Resultado.

```python
[
'http://10.10.10.5',

'http://10.10.10.20'
]
```

---

# Outro exemplo

Criando comandos.

```python
hosts = [

    "10.10.10.5",

    "10.10.10.20"

]

comandos = list(

    map(

        lambda host:

            f"ping -c 1 {host}",

        hosts

    )

)

print(comandos)
```

Resultado.

```python
[
'ping -c 1 10.10.10.5',

'ping -c 1 10.10.10.20'
]
```

---

# Forma mais utilizada

As formas mais encontradas em projetos são.

Converter tipos.

```python
map(

    int,

    numeros

)
```

---

Maiúsculas.

```python
map(

    str.upper,

    nomes

)
```

---

Remover espaços.

```python
map(

    str.strip,

    linhas

)
```

---

Lambda.

```python
map(

    lambda numero:

        numero * 2,

    numeros

)
```

---

# Boas práticas

✅ Utilize funções prontas sempre que existirem.

✅ Utilize `lambda` apenas para transformações simples.

✅ Utilize mais de um iterável quando houver relação entre eles.

✅ Converta para `list()` apenas se precisar armazenar todos os resultados.

---

# Comparação com for

O `map()` e o `for` conseguem resolver praticamente os mesmos problemas.

A diferença está na forma de escrever.

---

## Utilizando for

```python
nomes = [

    "ana",

    "carlos",

    "pedro"

]

resultado = []

for nome in nomes:

    resultado.append(

        nome.upper()

    )

print(resultado)
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

## Utilizando map()

```python
nomes = [

    "ana",

    "carlos",

    "pedro"

]

resultado = list(

    map(

        str.upper,

        nomes

    )

)

print(resultado)
```

Resultado.

```python
[
'ANA',

'CARLOS',

'PEDRO'
]
```

Os dois produzem exatamente o mesmo resultado.

---

# map() x List Comprehension

Essa é uma comparação muito comum.

---

## map()

```python
resultado = list(

    map(

        lambda numero:

            numero * 2,

        numeros

    )

)
```

---

## List Comprehension

```python
resultado = [

    numero * 2

    for numero in numeros

]
```

Mesmo resultado.

---

# Qual utilizar?

Depende.

## Prefira map()

Quando já existir uma função pronta.

Exemplo.

```python
map(

    int,

    numeros

)
```

---

```python
map(

    str.upper,

    nomes

)
```

---

```python
map(

    str.strip,

    linhas

)
```

Esses casos ficam muito limpos.

---

## Prefira List Comprehension

Quando utilizar.

```python
lambda
```

Exemplo.

```python
list(

    map(

        lambda numero:

            numero * 2,

        numeros

    )

)
```

Pode ser escrito de forma mais simples.

```python
[

    numero * 2

    for numero in numeros

]
```

Na comunidade Python, essa segunda forma costuma ser preferida por ser mais legível.

---

# Performance

O `map()` não cria uma lista imediatamente.

Ele retorna um objeto.

```python
map object
```

Isso economiza memória.

Somente quando fazemos.

```python
list(

    map(...)

)
```

é que todos os elementos são carregados.

---

# Como funciona internamente?

Imagine.

```python
map(

    str.upper,

    nomes

)
```

O Python faz algo semelhante.

```python
for nome in nomes:

    yield nome.upper()
```

Por isso ele retorna um objeto iterável.

Esse comportamento também torna o `map()` eficiente para trabalhar com grandes volumes de dados.

---

# Erros comuns

## Erro 1

Esquecer de converter para lista.

```python
resultado = map(

    int,

    numeros

)

print(resultado)
```

Resultado.

```text
<map object at 0x...>
```

Correto.

```python
print(

    list(resultado)

)
```

---

## Erro 2

Utilizar lambda sem necessidade.

Evite.

```python
map(

    lambda numero:

        int(numero),

    numeros

)
```

Prefira.

```python
map(

    int,

    numeros

)
```

Fica menor e mais legível.

---

## Erro 3

Utilizar map() apenas porque ele existe.

Às vezes.

```python
[
    numero * 2

    for numero in numeros

]
```

fica muito mais claro do que.

```python
list(

    map(

        lambda numero:

            numero * 2,

        numeros

    )

)
```

---

## Erro 4

Esperar que map() filtre elementos.

Errado.

```python
map(...)
```

serve para.

```text
Transformar
```

Quem filtra é.

```python
filter(...)
```

---

# Quando NÃO utilizar

Evite utilizar quando.

- A transformação é muito complexa.
- Existem vários `if`.
- O código ficou difícil de entender.

Nesses casos.

Um `for` tradicional costuma ser melhor.

---

# Como aparece em projetos Open Source

É muito comum encontrar.

Conversão de tipos.

```python
map(

    int,

    portas

)
```

---

Removendo espaços.

```python
map(

    str.strip,

    linhas

)
```

---

Convertendo para maiúsculas.

```python
map(

    str.upper,

    nomes

)
```

---

Criando objetos.

```python
map(

    Path,

    arquivos

)
```

Esses padrões aparecem frequentemente em projetos como.

- Requests
- Django
- Flask
- Scrapy
- Scapy
- pwntools
- Impacket

---

# Exemplos em Cyber Security

Convertendo portas.

```python
portas = [

    "22",

    "80",

    "443"

]

portas = list(

    map(

        int,

        portas

    )

)
```

Resultado.

```python
[
22,

80,

443
]
```

---

Removendo espaços de um wordlist.

```python
with open("usuarios.txt") as arquivo:

    usuarios = list(

        map(

            str.strip,

            arquivo

        )

    )
```

Muito utilizado em scripts de brute force.

---

Criando URLs.

```python
ips = [

    "10.10.10.5",

    "10.10.10.20"

]

urls = list(

    map(

        lambda ip:

            f"http://{ip}",

        ips

    )

)
```

---

# Curiosidades

- `map()` é uma função nativa do Python.
- Funciona com qualquer iterável.
- Pode receber vários iteráveis.
- Retorna um objeto `map`.
- É avaliado sob demanda (lazy evaluation).
- Pode ser combinado com `zip()`, `filter()` e `enumerate()`.

---

# Resumo

| Recurso | map() |
|----------|:-----:|
| Função nativa | ✅ |
| Transforma elementos | ✅ |
| Filtra elementos | ❌ |
| Retorna map object | ✅ |
| Funciona com iteráveis | ✅ |
| Aceita lambda | ✅ |
| Aceita funções prontas | ✅ |
| Aceita vários iteráveis | ✅ |

---

# Formas mais utilizadas

Converter para inteiro.

```python
map(

    int,

    numeros

)
```

---

Remover espaços.

```python
map(

    str.strip,

    linhas

)
```

---

Maiúsculas.

```python
map(

    str.upper,

    nomes

)
```

---

Transformação personalizada.

```python
map(

    lambda numero:

        numero * 2,

    numeros

)
```

---

# Boas práticas

✅ Utilize funções prontas (`int`, `str`, `float`, `str.upper`, `str.strip`) sempre que possível.

✅ Prefira List Comprehension quando a transformação for simples e envolver `lambda`.

✅ Utilize `map()` quando a intenção do código for claramente "aplicar esta função em todos os elementos".

✅ Converta para `list()` apenas quando precisar armazenar todos os resultados.

✅ Lembre-se: `map()` transforma dados. Se o objetivo é selecionar apenas alguns elementos, utilize `filter()`.