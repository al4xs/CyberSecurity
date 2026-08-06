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

---

# Utilizando if na List Comprehension

Até agora aprendemos.

```python
resultado = [

    numero

    for numero in range(10)

]
```

Mas também podemos filtrar elementos.

Para isso utilizamos.

```python
if
```

---

# Sintaxe

```python
[

expressao

for variavel in iteravel

if condicao

]
```

Observe.

O `if` fica no final da expressão.

---

# Como ler essa sintaxe?

```text
Percorra todos os elementos

↓

Verifique a condição

↓

Se for verdadeira

↓

Adicione o elemento na nova lista
```

---

# Primeiro exemplo

Queremos apenas números pares.

```python
pares = [

    numero

    for numero in range(10)

    if numero % 2 == 0

]

print(pares)
```

Resultado.

```python
[
0,
2,
4,
6,
8
]
```

---

# Como funciona internamente?

A List Comprehension acima equivale a.

```python
pares = []

for numero in range(10):

    if numero % 2 == 0:

        pares.append(numero)
```

O resultado será exatamente o mesmo.

---

# Outro exemplo

Filtrando nomes.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro",

    "Alberto"

]

resultado = [

    nome

    for nome in nomes

    if len(nome) > 5

]

print(resultado)
```

Resultado.

```python
[
'Carlos',

'Alberto'
]
```

---

# A expressão continua funcionando

A expressão fica antes do `for`.

Podemos fazer.

```python
resultado = [

    nome.upper()

    for nome in nomes

    if len(nome) > 5

]
```

Resultado.

```python
[
'CARLOS',

'ALBERTO'
]
```

Observe.

O Python faz.

```text
Percorre

↓

Filtra

↓

Transforma

↓

Adiciona na nova lista
```

---

# Utilizando funções

Também funciona.

```python
palavras = [

    "python",

    "c",

    "assembly",

    "bash"

]

resultado = [

    palavra.upper()

    for palavra in palavras

    if len(palavra) >= 5

]
```

Resultado.

```python
[
'PYTHON',

'ASSEMBLY'
]
```

---

# Múltiplas condições

Podemos utilizar.

```python
and
```

Exemplo.

```python
resultado = [

    numero

    for numero in range(30)

    if numero > 10 and numero < 20

]
```

Resultado.

```python
[
11,
12,
13,
14,
15,
16,
17,
18,
19
]
```

---

Também podemos utilizar.

```python
or
```

```python
resultado = [

    numero

    for numero in range(10)

    if numero == 2 or numero == 8

]
```

Resultado.

```python
[
2,
8
]
```

---

# Utilizando not

Também é permitido.

```python
nomes = [

    "",

    "Carlos",

    "",

    "Pedro"

]

resultado = [

    nome

    for nome in nomes

    if nome

]

print(resultado)
```

Resultado.

```python
[
'Carlos',

'Pedro'
]
```

Observe.

Strings vazias são consideradas falsas.

---

# if / else

Também podemos utilizar.

Mas a sintaxe muda.

---

# Sintaxe

```python
[

valor_se_verdadeiro

if condicao

else

valor_se_falso

for variavel in iteravel

]
```

Observe.

Agora.

O `if` fica antes do `for`.

Essa é uma das maiores dúvidas dos iniciantes.

---

# Exemplo

```python
resultado = [

    "PAR"

    if numero % 2 == 0

    else

    "ÍMPAR"

    for numero in range(6)

]

print(resultado)
```

Resultado.

```python
[
'PAR',

'ÍMPAR',

'PAR',

'ÍMPAR',

'PAR',

'ÍMPAR'
]
```

---

# Como funciona internamente?

Equivale a.

```python
resultado = []

for numero in range(6):

    if numero % 2 == 0:

        resultado.append("PAR")

    else:

        resultado.append("ÍMPAR")
```

---

# Diferença importante

## Apenas filtro

```python
[

numero

for numero in lista

if numero > 10

]
```

O `if` fica depois do `for`.

Porque estamos filtrando.

---

## if/else

```python
[

"A"

if condicao

else

"B"

for numero in lista

]
```

O `if` fica antes.

Porque estamos escolhendo qual valor colocar na nova lista.

Essa diferença costuma confundir bastante quem está aprendendo.

---

# Exemplo em automação

Imagine.

```python
hosts = [

    "10.10.10.5",

    "",

    "10.10.10.20",

    ""

]
```

Removendo entradas vazias.

```python
hosts_validos = [

    host

    for host in hosts

    if host

]
```

Resultado.

```python
[
'10.10.10.5',

'10.10.10.20'
]
```

Muito utilizado ao ler arquivos.

---

# Exemplo em Pentest

Lista de portas.

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

Selecionando apenas portas maiores que 1024.

```python
resultado = [

    porta

    for porta in portas

    if porta > 1024

]
```

Resultado.

```python
[
8080
]
```

---

# Boas práticas

✅ Utilize o `if` para filtrar listas.

✅ Utilize `if/else` apenas quando realmente precisar substituir valores.

✅ Evite colocar muitas condições na mesma List Comprehension.

Se começar a ficar difícil de ler, utilize um `for` tradicional.

---

# Curiosidades

- O `if` de filtro sempre fica depois do `for`.
- O `if/else` de transformação sempre fica antes do `for`.
- É possível utilizar operadores como `and`, `or` e `not`.
- List Comprehension pode combinar filtro e transformação na mesma expressão.

---

# Loops aninhados

Assim como um `for` tradicional, uma List Comprehension pode possuir mais de um laço de repetição.

Isso é chamado de.

```text
Loop aninhado
```

ou.

```text
Nested Loop
```

---

# Sintaxe

```python
[
    expressao

    for variavel1 in iteravel1

    for variavel2 in iteravel2
]
```

Observe.

Agora temos dois `for`.

O Python executa o primeiro.

Depois executa o segundo para cada elemento do primeiro.

---

# Como funciona?

Imagine.

```python
letras = [

    "A",

    "B"

]

numeros = [

    1,

    2,

    3

]
```

List Comprehension.

```python
resultado = [

    f"{letra}{numero}"

    for letra in letras

    for numero in numeros

]

print(resultado)
```

Resultado.

```python
[
'A1',

'A2',

'A3',

'B1',

'B2',

'B3'
]
```

---

# Como o Python executa?

É equivalente a.

```python
resultado = []

for letra in letras:

    for numero in numeros:

        resultado.append(

            f"{letra}{numero}"

        )
```

Visualmente.

```text
A

↓

1

↓

A1

----------------

A

↓

2

↓

A2

----------------

A

↓

3

↓

A3

----------------

B

↓

1

↓

B1

...
```

---

# Trabalhando com dicionários

List Comprehension também funciona muito bem com dicionários.

Exemplo.

```python
usuarios = [

    {

        "nome": "Ana",

        "idade": 20

    },

    {

        "nome": "Carlos",

        "idade": 30

    },

    {

        "nome": "Pedro",

        "idade": 25

    }

]
```

Criando uma lista apenas com os nomes.

```python
nomes = [

    usuario["nome"]

    for usuario in usuarios

]

print(nomes)
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

Obtendo apenas as idades.

```python
idades = [

    usuario["idade"]

    for usuario in usuarios

]
```

Resultado.

```python
[
20,

30,

25
]
```

---

# Trabalhando com objetos

Também funciona com classes.

```python
class Usuario:

    def __init__(self, nome, idade):

        self.nome = nome

        self.idade = idade


usuarios = [

    Usuario("Ana",20),

    Usuario("Carlos",30),

    Usuario("Pedro",25)

]
```

Criando uma lista.

```python
idades = [

    usuario.idade

    for usuario in usuarios

]
```

Resultado.

```python
[
20,

30,

25
]
```

---

# Chamando funções

A expressão pode conter qualquer função.

Exemplo.

```python
nomes = [

    "ana",

    "carlos",

    "pedro"

]

resultado = [

    nome.capitalize()

    for nome in nomes

]

print(resultado)
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

Também podemos utilizar.

```python
resultado = [

    len(nome)

    for nome in nomes

]
```

Resultado.

```python
[
3,

6,

5
]
```

---

# Utilizando enumerate()

Podemos combinar.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

resultado = [

    f"{indice} - {nome}"

    for indice, nome in enumerate(

        nomes,

        start=1

    )

]

print(resultado)
```

Resultado.

```python
[
'1 - Ana',

'2 - Carlos',

'3 - Pedro'
]
```

---

# Utilizando zip()

Também funciona.

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

resultado = [

    f"{nome} ({idade})"

    for nome, idade in zip(

        nomes,

        idades

    )

]

print(resultado)
```

Resultado.

```python
[
'Ana (20)',

'Carlos (30)',

'Pedro (25)'
]
```

---

# Exemplo em automação

Imagine que um script encontrou vários arquivos.

```python
arquivos = [

    "passwd",

    "shadow",

    "config.php",

    "backup.zip"

]
```

Queremos obter apenas os arquivos `.php`.

```python
php = [

    arquivo

    for arquivo in arquivos

    if arquivo.endswith(".php")

]

print(php)
```

Resultado.

```python
[
'config.php'
]
```

---

# Outro exemplo

Removendo espaços.

```python
usuarios = [

    " admin ",

    " guest ",

    " root "

]

resultado = [

    usuario.strip()

    for usuario in usuarios

]

print(resultado)
```

Resultado.

```python
[
'admin',

'guest',

'root'
]
```

Muito utilizado ao ler arquivos de texto.

---

# Exemplo em Pentest

Imagine.

```python
hosts = [

    "10.10.10.5",

    "10.10.10.8",

    "10.10.10.20"

]
```

Criando automaticamente URLs.

```python
urls = [

    f"http://{host}"

    for host in hosts

]

print(urls)
```

Resultado.

```python
[
'http://10.10.10.5',

'http://10.10.10.8',

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

comandos = [

    f"ping -c 1 {host}"

    for host in hosts

]

print(comandos)
```

Resultado.

```python
[
'ping -c 1 10.10.10.5',

'ping -c 1 10.10.10.20'
]
```

Muito comum em automações.

---

# Exemplo em Scanner

Imagine uma lista de portas.

```python
portas = [

    22,

    80,

    443

]
```

Criando argumentos.

```python
argumentos = [

    f"-p {porta}"

    for porta in portas

]
```

Resultado.

```python
[
'-p 22',

'-p 80',

'-p 443'
]
```

---

# Boas práticas

✅ Utilize List Comprehension para criar novas listas.

✅ Utilize funções pequenas na expressão.

✅ Combine com `zip()` e `enumerate()` quando necessário.

✅ Evite muitos `for` aninhados.

Se a compreensão começar a ficar difícil de entender, utilize um `for` tradicional.

O objetivo da List Comprehension é deixar o código mais legível, não mais complicado.

---

# Performance

Uma das dúvidas mais comuns é.

```text
List Comprehension é mais rápida que um for?
```

Na maioria dos casos.

✅ Sim.

---

# Por quê?

A List Comprehension é otimizada pelo próprio Python.

Enquanto.

```python
resultado = []

for numero in range(1000):

    resultado.append(numero)
```

executa diversas operações.

A List Comprehension.

```python
resultado = [

    numero

    for numero in range(1000)

]
```

é implementada de forma mais eficiente.

---

# Comparação

## for

```python
resultado = []

for numero in numeros:

    resultado.append(

        numero * 2

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

Menos código.

Normalmente mais rápida.

---

# Sempre devo utilizar?

❌ Não.

Essa é uma das maiores armadilhas.

O objetivo da List Comprehension não é deixar o código menor.

O objetivo é deixar o código mais legível.

---

# Quando NÃO utilizar

Evite quando.

- Existem vários `if`.
- Existem vários `for`.
- Existem muitos cálculos.
- A expressão ficou difícil de entender.

Exemplo ruim.

```python
resultado = [

    numero ** 2

    if numero % 2 == 0

    else

    numero ** 3

    for numero in numeros

    if numero > 10

]
```

Funciona.

Mas demora para entender.

Nesse caso.

Um `for` tradicional costuma ser melhor.

---

# Quando utilizar um for

Sempre que existir muita lógica.

Exemplo.

```python
resultado = []

for usuario in usuarios:

    if usuario["ativo"]:

        idade = usuario["idade"]

        if idade >= 18:

            resultado.append(

                usuario["nome"]

            )
```

Muito mais fácil de ler.

---

# Quando utilizar List Comprehension

Quando a transformação é simples.

Exemplo.

```python
resultado = [

    usuario["nome"]

    for usuario in usuarios

]
```

Ou.

```python
resultado = [

    numero * 2

    for numero in numeros

]
```

---

# Erros comuns

## Erro 1

Esquecer os colchetes.

Errado.

```python
numero

for numero in lista
```

Correto.

```python
[

    numero

    for numero in lista

]
```

---

## Erro 2

Confundir a posição do if.

Filtro.

```python
[

    numero

    for numero in lista

    if numero > 10

]
```

---

Transformação.

```python
[

    "PAR"

    if numero % 2 == 0

    else

    "ÍMPAR"

    for numero in lista

]
```

Essa diferença é muito importante.

---

## Erro 3

Criar comprehensions gigantes.

Evite.

```python
[
...
...
...
...
...
]
```

Se você precisar parar para descobrir como ela funciona.

Ela provavelmente está grande demais.

---

## Erro 4

Utilizar apenas porque "fica bonito".

Nem sempre.

Às vezes.

```python
for
```

é muito melhor.

Python valoriza.

```text
Legibilidade.
```

---

# Como aparece em projetos Open Source

Você encontrará List Comprehension praticamente em qualquer projeto Python.

Exemplos.

```python
arquivos = [

    arquivo

    for arquivo in arquivos

    if arquivo.endswith(".txt")

]
```

---

```python
hosts = [

    host["ip"]

    for host in resultado
]
```

---

```python
portas = [

    porta

    for porta in scanner

]
```

---

É comum em projetos como.

- pwntools
- Impacket
- Scapy
- Scrapy
- Django
- Flask
- FastAPI
- Requests
- Nornir
- Netmiko

---

# Como aparece em ferramentas de Pentest

Selecionando hosts.

```python
hosts = [

    host

    for host in resultado

    if host["ativo"]

]
```

---

Obtendo IPs.

```python
ips = [

    host["ip"]

    for host in resultado

]
```

---

Obtendo portas abertas.

```python
portas = [

    porta["numero"]

    for porta in resultado
]
```

---

Criando URLs.

```python
urls = [

    f"http://{ip}"

    for ip in ips

]
```

---

Criando comandos.

```python
comandos = [

    f"ping -c 1 {ip}"

    for ip in ips

]
```

---

Filtrando arquivos.

```python
php = [

    arquivo

    for arquivo in arquivos

    if arquivo.endswith(".php")

]
```

Esses padrões aparecem constantemente em scripts de automação.

---

# Comparação

## for

Ideal quando.

- Existem muitas condições.
- Existem vários passos.
- Existe muita lógica.
- O código ficou grande.

---

## List Comprehension

Ideal quando.

- Criar listas.
- Filtrar listas.
- Transformar elementos.
- Código pequeno.
- Código simples.

---

# Curiosidades

- Foi adicionada ao Python na versão 2.0.
- É considerada uma das características mais marcantes da linguagem.
- É muito utilizada por desenvolvedores Python experientes.
- Pode ser combinada com praticamente qualquer iterável.
- Pode utilizar funções, métodos, operadores e chamadas de função.

---

# Resumo

| Recurso | List Comprehension |
|----------|:------------------:|
| Cria listas | ✅ |
| Pode filtrar elementos | ✅ |
| Pode transformar elementos | ✅ |
| Pode utilizar `if` | ✅ |
| Pode utilizar `if/else` | ✅ |
| Pode utilizar vários `for` | ✅ |
| Pode utilizar funções | ✅ |
| Pode utilizar `zip()` | ✅ |
| Pode utilizar `enumerate()` | ✅ |
| Geralmente mais rápida que `for` | ✅ |

---

# Formas mais utilizadas

Criar lista.

```python
[

    numero

    for numero in numeros

]
```

---

Transformar valores.

```python
[

    numero * 2

    for numero in numeros

]
```

---

Filtrar.

```python
[

    numero

    for numero in numeros

    if numero % 2 == 0

]
```

---

Transformar com condição.

```python
[

    "PAR"

    if numero % 2 == 0

    else

    "ÍMPAR"

    for numero in numeros

]
```

---

Utilizando `zip()`.

```python
[

    f"{nome} ({idade})"

    for nome, idade in zip(

        nomes,

        idades

    )

]
```

---

Utilizando `enumerate()`.

```python
[

    f"{indice} - {nome}"

    for indice, nome in enumerate(

        nomes,

        start=1

    )

]
```

---

# Boas práticas

✅ Utilize List Comprehension para criar novas listas de forma simples.

✅ Prefira um `for` tradicional quando houver muita lógica.

✅ Utilize nomes descritivos para as variáveis.

✅ Evite expressões muito longas.

✅ Combine com `zip()` e `enumerate()` quando fizer sentido.

✅ Escreva código pensando em quem vai lê-lo depois (inclusive você mesmo).

---

# Conclusão

List Comprehension é uma das ferramentas mais importantes do Python moderno.

Ela permite escrever código mais limpo, expressivo e geralmente mais eficiente.

Entretanto, deve ser utilizada com equilíbrio. Quando a expressão ficar difícil de entender, um `for` tradicional é a melhor escolha.

Lembre-se: em Python, **legibilidade sempre vem antes de escrever menos linhas de código**.