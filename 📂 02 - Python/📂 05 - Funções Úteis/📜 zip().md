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

---

# Trabalhando com várias listas

O `zip()` não está limitado a apenas duas listas.

Ele pode agrupar três, quatro ou quantos iteráveis forem necessários.

---

## Exemplo

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

cidades = [

    "São Paulo",

    "Rio de Janeiro",

    "Curitiba"

]

for nome, idade, cidade in zip(

    nomes,

    idades,

    cidades

):

    print(

        nome,

        idade,

        cidade

    )
```

Saída.

```text
Ana 20 São Paulo

Carlos 30 Rio de Janeiro

Pedro 25 Curitiba
```

---

# Quantas listas posso utilizar?

Na prática.

Quantas forem necessárias.

```python
zip(

    lista1,

    lista2,

    lista3,

    lista4,

    lista5
)
```

Não existe um limite pequeno imposto pela função.

---

# O que acontece quando as listas possuem tamanhos diferentes?

Essa é uma das dúvidas mais comuns.

Imagine.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

idades = [

    20,

    30

]
```

Observe.

```text
nomes

↓

3 elementos

------------------

idades

↓

2 elementos
```

---

Executando.

```python
for nome, idade in zip(

    nomes,

    idades

):

    print(nome, idade)
```

Resultado.

```text
Ana 20

Carlos 30
```

Observe.

```text
Pedro
```

foi ignorado.

---

# Por que isso acontece?

O comportamento padrão do `zip()` é parar quando o menor iterável termina.

Visualmente.

```text
nomes

Ana

Carlos

Pedro

-------------------

idades

20

30

Fim

↓

zip()

↓

Parou
```

O Python não gera erro.

Ele simplesmente encerra a iteração.

---

# strict=False

Este é o comportamento padrão.

```python
zip(

    nomes,

    idades,

    strict=False

)
```

Ou simplesmente.

```python
zip(

    nomes,

    idades

)
```

Os dois possuem exatamente o mesmo comportamento.

---

# strict=True

A partir do Python 3.10 foi adicionado.

```python
strict=True
```

Agora.

Se os iteráveis possuírem tamanhos diferentes.

O Python gera erro.

---

## Exemplo

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

idades = [

    20,

    30

]

for nome, idade in zip(

    nomes,

    idades,

    strict=True

):

    print(nome, idade)
```

Resultado.

```text
ValueError:

zip() argument 2 is shorter than argument 1
```

---

# Quando utilizar strict=True?

Quando todos os iteráveis DEVEM possuir exatamente o mesmo tamanho.

Exemplos.

- Usuário e senha.
- Nome e CPF.
- Host e Porta.
- Nome e Idade.

Caso contrário.

Provavelmente existe um erro nos dados.

---

# Quando utilizar strict=False?

Quando não existe problema em ignorar elementos extras.

Exemplo.

Ler duas listas onde apenas os elementos em comum interessam.

---

# Comparando

## strict=False

```python
zip(

    lista1,

    lista2
)
```

Resultado.

```text
Ignora os elementos restantes.
```

---

## strict=True

```python
zip(

    lista1,

    lista2,

    strict=True
)
```

Resultado.

```text
ValueError
```

Caso possuam tamanhos diferentes.

---

# Exemplo em automação

Imagine.

```python
hosts = [

    "10.10.10.10",

    "10.10.10.20",

    "10.10.10.30"

]

portas = [

    22,

    80,

    443

]
```

Criando comandos.

```python
for host, porta in zip(

    hosts,

    portas

):

    print(

        f"nmap -p {porta} {host}"

    )
```

Resultado.

```text
nmap -p 22 10.10.10.10

nmap -p 80 10.10.10.20

nmap -p 443 10.10.10.30
```

Muito utilizado para gerar comandos automaticamente.

---

# Exemplo em Pentest

Imagine.

```python
usuarios = [

    "Administrator",

    "Guest",

    "Backup"

]

senhas = [

    "admin123",

    "guest",

    "backup2024"

]
```

Criando tentativas.

```python
for usuario, senha in zip(

    usuarios,

    senhas

):

    print(

        f"{usuario}:{senha}"

    )
```

Saída.

```text
Administrator:admin123

Guest:guest

Backup:backup2024
```

Esse padrão aparece bastante em scripts que processam arquivos de credenciais.

---

# Exemplo em Scanner

```python
hosts = [

    "10.10.10.10",

    "10.10.10.20"

]

status = [

    "UP",

    "DOWN"

]

for host, situacao in zip(

    hosts,

    status

):

    print(

        f"{host} -> {situacao}"

    )
```

Saída.

```text
10.10.10.10 -> UP

10.10.10.20 -> DOWN
```

---

# Exemplo em geração de relatórios

```python
servicos = [

    "SSH",

    "HTTP",

    "SMB"

]

portas = [

    22,

    80,

    445

]

for servico, porta in zip(

    servicos,

    portas

):

    print(

        f"{servico}: {porta}"

    )
```

Resultado.

```text
SSH: 22

HTTP: 80

SMB: 445
```

---

# Forma mais utilizada

Na maioria dos projetos.

Você verá.

```python
for a, b in zip(

    lista1,

    lista2

):
```

Ou.

```python
for host, porta in zip(

    hosts,

    portas

):
```

O uso de.

```python
strict=True
```

ainda é pouco comum.

Mas é excelente quando queremos validar dados.

---

# Boas práticas

✅ Utilize `zip()` quando precisar percorrer duas ou mais coleções relacionadas.

✅ Utilize `strict=True` quando os iteráveis obrigatoriamente devem possuir o mesmo tamanho.

✅ Escolha nomes descritivos.

Evite.

```python
for x, y in zip(...):
```

Prefira.

```python
for usuario, senha in zip(...):
```

ou.

```python
for host, porta in zip(...):
```

O código fica muito mais legível.

---

# Desfazendo um zip() (Unpacking)

Até agora aprendemos a unir várias listas.

Mas também podemos fazer o processo inverso.

Ou seja.

Separar novamente os dados.

Para isso utilizamos.

```python
*
```

Este recurso recebe o nome de.

```text
Unpacking
```

ou.

```text
Desempacotamento
```

---

# Exemplo

Temos.

```python
dados = [

    ("Ana",20),

    ("Carlos",30),

    ("Pedro",25)

]
```

Queremos.

```python
nomes

↓

["Ana","Carlos","Pedro"]

---------------------

idades

↓

[20,30,25]
```

Podemos fazer.

```python
nomes, idades = zip(*dados)

print(nomes)

print(idades)
```

Resultado.

```python
('Ana', 'Carlos', 'Pedro')

(20, 30, 25)
```

Observe.

O `zip()` retorna tuplas.

Caso queira listas.

```python
nomes = list(nomes)

idades = list(idades)
```

Resultado.

```python
['Ana', 'Carlos', 'Pedro']

[20,30,25]
```

---

# Como funciona?

Visualmente.

Antes.

```text
("Ana",20)

("Carlos",30)

("Pedro",25)
```

Depois.

```text
zip(*dados)

↓

Ana
Carlos
Pedro

----------------

20
30
25
```

---

# O que significa o *?

Esse símbolo.

```python
*
```

Desempacota um iterável.

Exemplo.

Lista.

```python
lista = [1,2,3]
```

Sem.

```python
print(lista)
```

Resultado.

```python
[1,2,3]
```

Com.

```python
print(*lista)
```

Resultado.

```text
1 2 3
```

O Python "abre" a lista.

No `zip()` acontece a mesma ideia.

---

# Casos avançados

## Criando um dicionário

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

Podemos fazer.

```python
usuarios = dict(

    zip(

        nomes,

        idades

    )

)

print(usuarios)
```

Resultado.

```python
{

    'Ana':20,

    'Carlos':30,

    'Pedro':25

}
```

Muito utilizado.

---

# Criando uma lista de tuplas

```python
nomes = [

    "Ana",

    "Carlos"

]

idades = [

    20,

    30

]

resultado = list(

    zip(

        nomes,

        idades

    )

)

print(resultado)
```

Resultado.

```python
[
    ('Ana',20),

    ('Carlos',30)
]
```

---

# Utilizando com enumerate()

As funções podem ser combinadas.

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

for indice, (nome, idade) in enumerate(

    zip(

        nomes,

        idades

    ),

    start=1

):

    print(

        indice,

        nome,

        idade

    )
```

Resultado.

```text
1 Ana 20

2 Carlos 30

3 Pedro 25
```

Observe.

O Python realizou dois desempacotamentos.

Primeiro.

```text
(Ana,20)
```

Depois.

```text
indice

↓

1
```

---

# Comparação com enumerate()

`enumerate()`

Serve para.

```text
Índice

+

Elemento
```

---

`zip()`

Serve para.

```text
Elemento

+

Outro elemento
```

---

Resumo.

```text
enumerate()

↓

Posição

+

Valor

------------------------

zip()

↓

Valor

+

Outro valor
```

---

# Comparação com map()

`zip()`

Une informações.

```text
Nome

↓

Idade
```

---

`map()`

Transforma informações.

```text
"22"

↓

22
```

São funções completamente diferentes.

---

# Performance

Assim como.

```python
enumerate()
```

`zip()` não cria uma lista automaticamente.

Ele retorna um.

```python
zip object
```

Isso economiza memória.

Somente quando fazemos.

```python
list(zip(...))
```

é que todos os elementos são armazenados em memória.

Por isso.

```python
for item in zip(...):
```

é mais eficiente do que.

```python
lista = list(

    zip(...)

)
```

quando apenas queremos percorrer os dados.

---

# Erros comuns

## Erro 1

Esperar uma lista.

```python
resultado = zip(lista1, lista2)

print(resultado)
```

Resultado.

```text
<zip object at ...>
```

Utilize.

```python
list(resultado)
```

caso queira visualizar.

---

## Erro 2

Esquecer que zip() para no menor iterável.

```python
nomes

↓

3 elementos

----------------

idades

↓

2 elementos
```

Resultado.

O último nome será ignorado.

---

## Erro 3

Não utilizar strict=True quando necessário.

Imagine.

Lista de usuários.

↓

100 elementos.

Lista de senhas.

↓

99 elementos.

O último usuário ficará sem senha.

Se isso for um erro para o seu programa.

Utilize.

```python
strict=True
```

---

## Erro 4

Utilizar nomes genéricos.

Evite.

```python
for x, y in zip(...):
```

Prefira.

```python
for host, porta in zip(...):
```

ou.

```python
for usuario, senha in zip(...):
```

---

# Como aparece em ferramentas reais

É extremamente comum encontrar.

```python
zip(...)
```

Em projetos como.

- Impacket
- pwntools
- Scapy
- Scrapy
- Requests
- Netmiko
- Nornir
- Ansible

Principalmente para.

- associar hosts e portas;
- relacionar usuários e senhas;
- montar tabelas;
- criar dicionários;
- gerar relatórios.

---

# Exemplo em Pentest

Imagine.

```python
hosts = [

    "10.10.10.5",

    "10.10.10.20"

]

servicos = [

    "SSH",

    "HTTP"

]

portas = [

    22,

    80

]
```

Criando um relatório.

```python
for host, servico, porta in zip(

    hosts,

    servicos,

    portas

):

    print(

        f"{host} -> {servico} ({porta})"

    )
```

Saída.

```text
10.10.10.5 -> SSH (22)

10.10.10.20 -> HTTP (80)
```

---

# Curiosidades

- `zip()` é uma função nativa do Python.
- Funciona com qualquer objeto iterável.
- Retorna um objeto `zip`.
- Não cria listas automaticamente.
- Pode trabalhar com dezenas de iteráveis ao mesmo tempo.
- Pode ser combinado com praticamente qualquer estrutura de repetição.

---

# Resumo

| Recurso | zip() |
|----------|:-----:|
| Função nativa | ✅ |
| Funciona com iteráveis | ✅ |
| Une vários iteráveis | ✅ |
| Retorna objeto zip | ✅ |
| Aceita `strict` | ✅ |
| Permite desempacotamento | ✅ |

---

# Formas mais utilizadas

Percorrer duas listas.

```python
for nome, idade in zip(

    nomes,

    idades

):

    ...
```

---

Criar dicionário.

```python
usuarios = dict(

    zip(

        nomes,

        idades

    )

)
```

---

Separar novamente os dados.

```python
nomes, idades = zip(*dados)
```

---

Validar tamanhos.

```python
zip(

    lista1,

    lista2,

    strict=True

)
```

---

# Boas práticas

✅ Utilize `zip()` quando houver relação entre duas ou mais coleções.

✅ Utilize `strict=True` quando os iteráveis obrigatoriamente precisarem ter o mesmo tamanho.

✅ Evite transformar em lista (`list(zip(...))`) se você apenas for percorrer os dados.

✅ Utilize nomes descritivos para as variáveis.

✅ Aproveite o desempacotamento (`zip(*dados)`) para reorganizar estruturas de dados de forma simples e legível.