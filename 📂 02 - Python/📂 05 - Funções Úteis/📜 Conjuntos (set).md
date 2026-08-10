# 📜 Conjuntos (set)

Um `set` é uma estrutura de dados do Python utilizada para armazenar uma coleção de **valores únicos**.

Isso significa que um conjunto não mantém elementos duplicados.

Exemplo:

```python
numeros = {
    1,
    2,
    3,
    4
}
```

---

# Características principais

Um `set`:

```text
É mutável

Não mantém elementos duplicados

Não possui acesso por índice

Pode adicionar e remover elementos

É muito útil para verificar se um valor existe

Permite operações matemáticas de conjuntos
```

---

# Criando um set

Podemos criar utilizando:

```python
{}
```

Exemplo:

```python
portas = {
    22,
    80,
    443
}
```

Verificando o tipo:

```python
print(
    type(portas)
)
```

Resultado:

```text
<class 'set'>
```

---

# Cuidado com {}

Existe uma pegadinha importante.

Isso:

```python
dados = {}
```

NÃO cria um `set`.

Cria um:

```text
dict
```

Podemos verificar:

```python
print(
    type(dados)
)
```

Resultado:

```text
<class 'dict'>
```

---

# Criando um set vazio

Para criar um conjunto vazio utilizamos:

```python
set()
```

Exemplo:

```python
dados = set()
```

Agora:

```python
print(
    type(dados)
)
```

Resultado:

```text
<class 'set'>
```

Portanto:

```text
{}

↓

dict vazio
```

Enquanto:

```text
set()

↓

set vazio
```

---

# Função set()

Sintaxe:

```python
set(iteravel)
```

O parâmetro é opcional.

Podemos utilizar:

```python
set()
```

ou:

```python
set(iteravel)
```

---

# O que set() recebe?

Quando fornecido, `set()` recebe um objeto iterável.

Por exemplo:

```text
list

tuple

str

range

dict

generator
```

---

# Convertendo uma lista para set

```python
numeros = [
    1,
    2,
    3,
    4
]

resultado = set(numeros)

print(resultado)
```

Teremos um conjunto contendo:

```text
1
2
3
4
```

---

# Valores duplicados

Essa é uma das características mais importantes de `set`.

Exemplo:

```python
numeros = [
    1,
    1,
    2,
    2,
    2,
    3,
    4,
    4
]

resultado = set(numeros)
```

O conjunto conterá apenas:

```text
1
2
3
4
```

Os valores repetidos são eliminados.

---

# Removendo duplicados de uma lista

Uma técnica comum é:

```python
numeros = [
    22,
    80,
    80,
    443,
    443,
    8080
]

numeros = list(
    set(numeros)
)
```

Agora temos uma lista contendo apenas valores únicos.

---

# Atenção à ordem

Não utilize:

```python
list(
    set(lista)
)
```

quando for necessário preservar a ordem original dos elementos.

`set` não deve ser utilizado como uma estrutura indexada ou como forma de garantir a ordem original de uma sequência.

Se a ordem for importante, existem outras técnicas.

---

# Convertendo uma tupla

```python
dados = (
    22,
    80,
    443,
    443
)

resultado = set(dados)
```

Os valores duplicados são eliminados.

---

# Convertendo uma string

Uma string é iterável.

Portanto:

```python
letras = set(
    "banana"
)

print(letras)
```

O Python percorre:

```text
b
a
n
a
n
a
```

Como `set` não mantém duplicados, o conjunto terá apenas:

```text
b
a
n
```

A ordem exibida não deve ser considerada fixa.

---

# set não possui índice

Em uma lista podemos fazer:

```python
numeros = [
    10,
    20,
    30
]

print(
    numeros[0]
)
```

Resultado:

```text
10
```

Em um `set`:

```python
numeros = {
    10,
    20,
    30
}

print(
    numeros[0]
)
```

❌ Não funciona.

O Python gera:

```text
TypeError
```

Porque conjuntos não oferecem acesso aos elementos por posição.

---

# Percorrendo um set

Apesar de não possuir índices, podemos iterar sobre ele.

```python
portas = {
    22,
    80,
    443
}

for porta in portas:

    print(porta)
```

---

# Verificando se um valor existe

Uma das utilizações mais importantes de `set` é o operador:

```python
in
```

Exemplo:

```python
portas = {
    22,
    80,
    443
}

if 443 in portas:

    print(
        "Porta encontrada"
    )
```

---

# Verificando se não existe

Podemos utilizar:

```python
not in
```

Exemplo:

```python
if 8080 not in portas:

    print(
        "8080 não está no conjunto"
    )
```

---

# Por que set é bom para isso?

Conjuntos são projetados para realizar testes de pertencimento de maneira eficiente.

Quando temos uma coleção utilizada principalmente para perguntas como:

```text
Esse valor existe?

Esse IP já foi processado?

Essa porta já apareceu?

Esse usuário já foi encontrado?
```

um `set` pode ser uma excelente escolha.

---

# add()

Utilizamos:

```python
.add()
```

para adicionar **um elemento** ao conjunto.

Sintaxe:

```python
set.add(elemento)
```

Recebe:

```text
1 elemento
```

---

# Exemplo

```python
portas = {
    22,
    80
}

portas.add(443)
```

Agora `443` faz parte do conjunto.

---

# Adicionando valor repetido

```python
portas.add(80)
```

Não teremos dois valores `80`.

O conjunto continua contendo apenas uma ocorrência.

---

# add() x append()

Listas utilizam:

```python
lista.append(valor)
```

Sets utilizam:

```python
conjunto.add(valor)
```

Portanto:

```text
list

↓

append()
```

```text
set

↓

add()
```

---

# update()

`update()` permite adicionar vários elementos.

Sintaxe:

```python
set.update(*iteraveis)
```

Pode receber um ou mais iteráveis.

Exemplo:

```python
portas = {
    22,
    80
}

portas.update(
    [443, 8080]
)
```

Agora o conjunto também contém:

```text
443
8080
```

---

# Diferença entre add() e update()

`add()` adiciona um elemento:

```python
portas.add(443)
```

`update()` percorre um ou mais iteráveis e adiciona seus elementos:

```python
portas.update(
    [443, 8080, 8443]
)
```

Regra mental:

```text
add()

↓

Um elemento
```

```text
update()

↓

Vários elementos vindos de iteráveis
```

---

# Cuidado com strings em update()

```python
dados = set()

dados.update(
    "SSH"
)
```

Como uma string é iterável, serão adicionados seus caracteres:

```text
S
S
H
```

Mas como não existem duplicados, o conjunto terá apenas:

```text
S
H
```

Se quiser adicionar `"SSH"` como um único elemento:

```python
dados.add(
    "SSH"
)
```

---

# remove()

Utilizado para remover um elemento.

Sintaxe:

```python
set.remove(elemento)
```

Exemplo:

```python
portas = {
    22,
    80,
    443
}

portas.remove(80)
```

O valor `80` é removido.

---

# E se o elemento não existir?

```python
portas.remove(8080)
```

Se `8080` não existir, o Python gera:

```text
KeyError
```

---

# discard()

Também remove um elemento.

Sintaxe:

```python
set.discard(elemento)
```

Exemplo:

```python
portas.discard(80)
```

A diferença aparece quando o elemento não existe.

```python
portas.discard(8080)
```

Não gera erro.

---

# remove() x discard()

```text
remove()

↓

Remove o elemento

↓

Se não existir:

KeyError
```

Enquanto:

```text
discard()

↓

Remove o elemento

↓

Se não existir:

Não acontece nada
```

---

# pop()

Também existe:

```python
.pop()
```

Sintaxe:

```python
set.pop()
```

Não recebe parâmetros.

Ele remove e retorna **um elemento arbitrário** do conjunto.

Exemplo:

```python
portas = {
    22,
    80,
    443
}

removido = portas.pop()

print(removido)
```

---

# Cuidado com pop()

Em listas:

```python
lista.pop()
```

normalmente remove o último elemento.

E podemos informar um índice:

```python
lista.pop(0)
```

Em `set` isso é diferente.

```python
conjunto.pop()
```

não recebe índice.

Como `set` não trabalha com posições, você não deve depender de qual elemento será removido.

---

# clear()

Remove todos os elementos.

Sintaxe:

```python
set.clear()
```

Não recebe parâmetros.

Exemplo:

```python
portas = {
    22,
    80,
    443
}

portas.clear()
```

Resultado:

```python
set()
```

---

# copy()

Cria uma cópia superficial do conjunto.

Sintaxe:

```python
set.copy()
```

Exemplo:

```python
portas = {
    22,
    80,
    443
}

copia = portas.copy()
```

Agora:

```python
copia.add(8080)
```

não adiciona `8080` ao conjunto original.

---

# Operações de conjuntos

Uma das partes mais poderosas de `set` são as operações matemáticas entre conjuntos.

Imagine:

```python
scan1 = {
    22,
    80,
    443
}

scan2 = {
    80,
    443,
    8080
}
```

---

# União

União significa:

```text
Todos os elementos presentes
nos dois conjuntos
```

Podemos utilizar:

```python
scan1.union(scan2)
```

Ou:

```python
scan1 | scan2
```

Resultado:

```python
{
    22,
    80,
    443,
    8080
}
```

Os valores duplicados aparecem apenas uma vez.

---

# intersection()

Interseção significa:

```text
Elementos que existem nos DOIS conjuntos
```

Podemos utilizar:

```python
scan1.intersection(scan2)
```

Ou:

```python
scan1 & scan2
```

Resultado:

```python
{
    80,
    443
}
```

Visualmente:

```text
scan1

22
80  ← existe nos dois
443 ← existe nos dois


scan2

80
443
8080
```

---

# difference()

Diferença significa:

```text
O que existe no primeiro

MAS NÃO existe no segundo
```

Exemplo:

```python
scan1.difference(scan2)
```

Ou:

```python
scan1 - scan2
```

Resultado:

```python
{
    22
}
```

---

# A ordem importa na diferença

Isso:

```python
scan1 - scan2
```

não é igual a:

```python
scan2 - scan1
```

No segundo caso:

```python
scan2 - scan1
```

Resultado:

```python
{
    8080
}
```

---

# symmetric_difference()

Diferença simétrica retorna os elementos que aparecem em um conjunto ou no outro, mas não nos dois.

Podemos utilizar:

```python
scan1.symmetric_difference(scan2)
```

Ou:

```python
scan1 ^ scan2
```

Resultado:

```python
{
    22,
    8080
}
```

Os valores:

```text
80
443
```

não aparecem porque existem nos dois conjuntos.

---

# Resumo dos operadores

```text
A | B

↓

União
```

```text
A & B

↓

Interseção
```

```text
A - B

↓

Diferença
```

```text
A ^ B

↓

Diferença simétrica
```

---

# issubset()

Verifica se todos os elementos de um conjunto estão presentes em outro.

Exemplo:

```python
portas_web = {
    80,
    443
}

portas = {
    22,
    80,
    443,
    8080
}
```

Podemos fazer:

```python
portas_web.issubset(portas)
```

Resultado:

```python
True
```

Porque:

```text
80
443
```

existem em `portas`.

---

# issuperset()

É a ideia contrária.

```python
portas.issuperset(
    portas_web
)
```

Resultado:

```python
True
```

Significa:

```text
portas contém todos os elementos
de portas_web?
```

---

# isdisjoint()

Verifica se dois conjuntos não possuem nenhum elemento em comum.

Exemplo:

```python
a = {
    22,
    80
}

b = {
    443,
    8080
}

print(
    a.isdisjoint(b)
)
```

Resultado:

```python
True
```

Porque não existe nenhum elemento compartilhado.

---

# Elementos precisam ser hashable

Os elementos armazenados diretamente dentro de um `set` precisam ser `hashable`.

Tipos comuns que funcionam:

```python
dados = {
    10,
    3.14,
    "Python",
    True,
    None,
    (1, 2)
}
```

---

# Lista dentro de set

Isso não funciona:

```python
dados = {
    [1, 2, 3]
}
```

Porque `list` é mutável e não é hashable.

O Python gera:

```text
TypeError: unhashable type: 'list'
```

---

# Dicionário dentro de set

Também não podemos fazer:

```python
dados = {
    {
        "porta": 22
    }
}
```

Um `dict` também não é hashable.

---

# Tupla dentro de set

Uma tupla pode funcionar:

```python
servicos = {
    ("SSH", 22),
    ("HTTP", 80),
    ("HTTPS", 443)
}
```

Isso permite representar valores compostos.

Porém, os elementos internos da tupla também precisam permitir que a própria tupla seja hashable.

---

# Set Comprehension

Assim como existe:

```text
List Comprehension
```

também existe:

```text
Set Comprehension
```

Sintaxe:

```python
{
    expressao
    for elemento in iteravel
}
```

---

# Exemplo

```python
numeros = [
    1,
    2,
    3,
    4,
    5
]

dobros = {
    numero * 2
    for numero in numeros
}
```

O conjunto conterá:

```text
2
4
6
8
10
```

---

# Set Comprehension removendo duplicados naturalmente

```python
numeros = [
    1,
    1,
    2,
    2,
    3,
    3
]

quadrados = {
    numero ** 2
    for numero in numeros
}
```

O conjunto terá:

```text
1
4
9
```

Mesmo que a expressão produza resultados repetidos, o `set` mantém apenas valores únicos.

---

# Set Comprehension com condição

Também podemos utilizar:

```python
{
    expressao
    for elemento in iteravel
    if condicao
}
```

Exemplo:

```python
portas = [
    21,
    22,
    22,
    80,
    443,
    8080
]

portas_altas = {
    porta
    for porta in portas
    if porta > 1024
}
```

Resultado:

```python
{
    8080
}
```

---

# Exemplo em Cyber Security

Imagine que uma ferramenta coletou:

```python
hosts = [
    "10.10.10.1",
    "10.10.10.2",
    "10.10.10.1",
    "10.10.10.3",
    "10.10.10.2"
]
```

Se queremos trabalhar apenas com hosts únicos:

```python
hosts_unicos = set(hosts)
```

Agora cada host aparece apenas uma vez.

Isso pode ser útil em ferramentas de enumeração, parsing e processamento de resultados.

---

# Evitando processar o mesmo item novamente

Outro uso muito comum:

```python
processados = set()
```

Durante o processamento:

```python
host = "10.10.10.10"

if host not in processados:

    print(
        f"Processando {host}"
    )

    processados.add(host)
```

Na próxima vez:

```python
if host not in processados:
```

será falso.

Esse padrão é muito útil para evitar processamento duplicado.

---

# Comparando resultados

Imagine dois resultados:

```python
resultado_antigo = {
    22,
    80,
    443
}

resultado_novo = {
    22,
    80,
    443,
    8080
}
```

Podemos descobrir o que apareceu de novo:

```python
novas = (
    resultado_novo
    -
    resultado_antigo
)
```

Resultado:

```python
{
    8080
}
```

---

# Quando utilizar set?

Uma boa regra mental:

```text
Preciso armazenar valores únicos?

↓

set
```

```text
Preciso verificar constantemente
se algo já existe?

↓

set
```

```text
Preciso comparar grupos de valores?

↓

set
```

```text
Preciso de união, interseção
ou diferença?

↓

set
```

---

# Quando NÃO utilizar set?

Se você precisa acessar:

```python
dados[0]

dados[1]

dados[2]
```

provavelmente `set` não é a estrutura adequada.

---

Se precisa preservar uma sequência específica e trabalhar por posição:

```text
list

ou

tuple
```

costumam fazer mais sentido.

---

# list x tuple x dict x set

```text
LIST

↓

[1, 2, 3]

Mutável

Indexável

Aceita duplicados
```

---

```text
TUPLE

↓

(1, 2, 3)

Imutável

Indexável

Aceita duplicados
```

---

```text
DICT

↓

{
    "porta": 22
}

Chave → valor

Mutável
```

---

```text
SET

↓

{22, 80, 443}

Mutável

Valores únicos

Sem acesso por índice
```

---

# Métodos principais

```python
conjunto.add(valor)
```

Adiciona um elemento.

---

```python
conjunto.update(iteravel)
```

Adiciona vários elementos.

---

```python
conjunto.remove(valor)
```

Remove um elemento.

Gera erro se não existir.

---

```python
conjunto.discard(valor)
```

Remove um elemento.

Não gera erro se não existir.

---

```python
conjunto.pop()
```

Remove e retorna um elemento arbitrário.

---

```python
conjunto.clear()
```

Remove todos os elementos.

---

```python
conjunto.copy()
```

Cria uma cópia superficial.

---

```python
conjunto.union(outro)
```

União.

---

```python
conjunto.intersection(outro)
```

Interseção.

---

```python
conjunto.difference(outro)
```

Diferença.

---

```python
conjunto.symmetric_difference(outro)
```

Diferença simétrica.

---

```python
conjunto.issubset(outro)
```

Verifica se é subconjunto.

---

```python
conjunto.issuperset(outro)
```

Verifica se é superconjunto.

---

```python
conjunto.isdisjoint(outro)
```

Verifica se não possuem elementos em comum.

---

# Formas mais utilizadas

Criar:

```python
portas = {
    22,
    80,
    443
}
```

Criar vazio:

```python
portas = set()
```

Eliminar duplicados:

```python
unicos = set(dados)
```

Adicionar:

```python
portas.add(8080)
```

Verificar existência:

```python
if 443 in portas:
    ...
```

Percorrer:

```python
for porta in portas:
    ...
```

Interseção:

```python
a & b
```

União:

```python
a | b
```

Diferença:

```python
a - b
```

Set Comprehension:

```python
resultado = {
    valor
    for valor in dados
    if condicao
}
```

---

# Regra mental final

```text
Preciso de uma coleção dinâmica
e indexada?

↓

list
```

```text
Preciso de uma estrutura fixa
e indexada?

↓

tuple
```

```text
Preciso associar uma chave
a um valor?

↓

dict
```

```text
Preciso de valores únicos,
testes de pertencimento ou
operações entre conjuntos?

↓

set
```

