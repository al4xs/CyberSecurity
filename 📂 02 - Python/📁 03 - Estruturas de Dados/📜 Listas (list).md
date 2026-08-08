# 📜 Listas (list)

Uma lista (`list`) é uma estrutura de dados do Python utilizada para armazenar vários valores dentro de uma única variável.

Exemplo.

```python
usuarios = [
    "admin",
    "root",
    "guest"
]
```

Uma das principais características das listas é que elas são:

```text
Mutáveis
```

Isso significa que podemos modificar a lista depois que ela foi criada.

Podemos:

- Adicionar elementos.
- Remover elementos.
- Substituir elementos.
- Reordenar elementos.
- Modificar objetos mutáveis armazenados dentro dela.

---

# Criando uma lista

A forma mais comum é utilizar:

```python
[]
```

Exemplo.

```python
nomes = [
    "Ana",
    "Carlos",
    "Pedro"
]
```

---

# Lista vazia

Podemos criar uma lista sem nenhum elemento.

```python
usuarios = []
```

Depois podemos adicionar elementos.

```python
usuarios.append("admin")
```

Resultado.

```python
["admin"]
```

---

# Utilizando list()

Também existe a função:

```python
list()
```

Sintaxe:

```python
list(iteravel)
```

---

## O parâmetro é obrigatório?

❌ Não.

Podemos fazer:

```python
lista = list()
```

Resultado:

```python
[]
```

---

## O que list() recebe?

Quando informado, recebe um objeto iterável.

Por exemplo:

- String
- Tupla
- Set
- Range
- Dicionário
- Generator
- Outros objetos iteráveis

---

# Convertendo uma string

```python
letras = list("Python")

print(letras)
```

Resultado:

```python
[
    "P",
    "y",
    "t",
    "h",
    "o",
    "n"
]
```

A string foi percorrida caractere por caractere.

---

# Convertendo uma tupla

```python
dados = (
    "admin",
    "root",
    "guest"
)

usuarios = list(dados)

print(usuarios)
```

Resultado:

```python
[
    "admin",
    "root",
    "guest"
]
```

---

# Convertendo range()

```python
numeros = list(
    range(5)
)

print(numeros)
```

Resultado:

```python
[
    0,
    1,
    2,
    3,
    4
]
```

---

# Uma lista pode armazenar vários tipos?

Sim.

```python
dados = [
    "Python",
    10,
    3.14,
    True,
    None
]
```

Temos:

```text
str

int

float

bool

None
```

dentro da mesma lista.

---

# Uma lista também pode armazenar outras estruturas

```python
dados = [
    [1, 2, 3],

    ("SSH", 22),

    {
        "host": "192.168.1.10"
    }
]
```

Temos:

```text
list

↓

list
tuple
dict
```

---

# Listas aninhadas

Uma lista pode conter outras listas.

Exemplo:

```python
cores = [
    ["rosa", "amarelo", "preto"],
    ["ciano", "roxo", "vermelho"]
]
```

Visualmente:

```text
cores
│
├── índice 0
│   │
│   ├── rosa
│   ├── amarelo
│   └── preto
│
└── índice 1
    │
    ├── ciano
    ├── roxo
    └── vermelho
```

---

# Acessando elementos

Cada elemento possui um índice.

```python
usuarios = [
    "admin",
    "root",
    "guest"
]
```

Visualmente:

```text
 admin    root    guest

   0        1        2
```

Podemos acessar:

```python
print(
    usuarios[0]
)
```

Resultado:

```text
admin
```

---

# Índices negativos

Também podemos acessar do final para o início.

```text
 admin    root    guest

   0        1        2

  -3       -2       -1
```

Último elemento:

```python
usuarios[-1]
```

Resultado:

```text
guest
```

---

Penúltimo:

```python
usuarios[-2]
```

Resultado:

```text
root
```

---

# Acessando listas aninhadas

Temos:

```python
cores = [
    ["rosa", "amarelo", "preto"],
    ["ciano", "roxo", "vermelho"]
]
```

Fazendo:

```python
cores[0]
```

recebemos:

```python
["rosa", "amarelo", "preto"]
```

Agora:

```python
cores[0][0]
```

Resultado:

```text
rosa
```

---

# Como ler?

```python
cores[0][0]
```

Primeiro:

```text
cores[0]

↓

Primeira lista
```

Depois:

```text
[0]

↓

Primeiro elemento daquela lista
```

Portanto:

```text
cores[0][0]

↓

rosa
```

---

# Listas são mutáveis

Essa é uma diferença extremamente importante entre `list` e `tuple`.

Podemos fazer:

```python
usuarios = [
    "admin",
    "root",
    "guest"
]

usuarios[0] = "administrator"
```

Resultado:

```python
[
    "administrator",
    "root",
    "guest"
]
```

A lista foi modificada.

---

# Modificando uma lista aninhada

```python
cores = [
    ["rosa", "amarelo", "preto"],
    ["ciano", "roxo", "vermelho"]
]

cores[0][0] = "neutro"
```

Resultado:

```python
[
    ["neutro", "amarelo", "preto"],
    ["ciano", "roxo", "vermelho"]
]
```

---

# Adicionando elementos

Um dos métodos mais utilizados é:

```python
.append()
```

Sintaxe:

```python
lista.append(objeto)
```

O `append()` recebe **um objeto** e adiciona esse objeto ao final da lista.

---

# Exemplo

```python
usuarios = [
    "admin",
    "root"
]

usuarios.append("guest")
```

Resultado:

```python
[
    "admin",
    "root",
    "guest"
]
```

---

# append() pode receber qualquer objeto?

Sim.

Podemos adicionar:

```python
lista.append(10)

lista.append("Python")

lista.append(True)

lista.append(None)
```

Também podemos adicionar outra lista:

```python
lista.append(
    [1, 2, 3]
)
```

Importante:

A lista inteira será adicionada como **um único elemento**.

---

# Exemplo

```python
numeros = [
    1,
    2
]

numeros.append(
    [3, 4]
)

print(numeros)
```

Resultado:

```python
[
    1,
    2,
    [3, 4]
]
```

Observe que não virou:

```python
[1, 2, 3, 4]
```

Para esse comportamento existe outro método:

```python
.extend()
```

que veremos posteriormente.

---

# Removendo pelo valor

Podemos utilizar:

```python
.remove()
```

Sintaxe:

```python
lista.remove(valor)
```

Ele procura o valor e remove sua **primeira ocorrência**.

---

# Exemplo

```python
usuarios = [
    "admin",
    "root",
    "guest"
]

usuarios.remove("root")

print(usuarios)
```

Resultado:

```python
[
    "admin",
    "guest"
]
```

---

# Se houver valores repetidos

```python
numeros = [
    10,
    20,
    10,
    30
]

numeros.remove(10)
```

Resultado:

```python
[
    20,
    10,
    30
]
```

Somente o primeiro `10` foi removido.

---

# E se o valor não existir?

```python
usuarios.remove("teste")
```

O Python gera:

```text
ValueError
```

---

# Removendo pela posição

Utilizamos:

```python
.pop()
```

Sintaxe:

```python
lista.pop(indice)
```

O índice é opcional.

---

# Sem informar índice

```python
usuarios = [
    "admin",
    "root",
    "guest"
]

usuarios.pop()
```

Por padrão, remove o último elemento.

Resultado da lista:

```python
[
    "admin",
    "root"
]
```

---

# Informando índice

```python
usuarios.pop(0)
```

Remove o elemento do índice:

```text
0
```

---

# pop() também retorna o elemento

Essa parte é importante.

```python
usuarios = [
    "admin",
    "root",
    "guest"
]

removido = usuarios.pop()

print(removido)
```

Resultado:

```text
guest
```

Portanto:

```python
pop()
```

faz duas coisas:

```text
Remove o elemento

+

Retorna o elemento removido
```

---

# remove() x pop()

```text
remove()

↓

Remove pelo VALOR
```

Exemplo:

```python
usuarios.remove("admin")
```

---

```text
pop()

↓

Remove pelo ÍNDICE
```

Exemplo:

```python
usuarios.pop(0)
```

---

# Quando utilizar uma lista?

Utilize uma lista quando estiver trabalhando com uma coleção que pode mudar.

Por exemplo:

```python
hosts = []
```

Durante uma enumeração podemos encontrar hosts.

```python
hosts.append(
    "192.168.1.10"
)

hosts.append(
    "192.168.1.20"
)
```

Resultado:

```python
[
    "192.168.1.10",
    "192.168.1.20"
]
```

Depois podemos encontrar outro:

```python
hosts.append(
    "192.168.1.30"
)
```

A coleção é dinâmica.

Nesse cenário, `list` faz sentido.

---

# Exemplo em automação

Imagine resultados obtidos por um scanner em um laboratório autorizado.

```python
portas_abertas = []

portas_abertas.append(22)

portas_abertas.append(80)

portas_abertas.append(443)
```

Resultado:

```python
[
    22,
    80,
    443
]
```

Podemos percorrer:

```python
for porta in portas_abertas:

    print(
        f"Porta encontrada: {porta}"
    )
```

Saída:

```text
Porta encontrada: 22
Porta encontrada: 80
Porta encontrada: 443
```

---

# Exemplo com serviços

```python
servicos = [
    ["SSH", 22],
    ["HTTP", 80],
    ["HTTPS", 443]
]
```

Podemos acessar:

```python
print(
    servicos[0][0]
)
```

Resultado:

```text
SSH
```

E:

```python
print(
    servicos[0][1]
)
```

Resultado:

```text
22
```

---

# Lista x Tupla

Uma regra mental inicial muito boa é:

```text
LISTA

↓

Coleção mutável

↓

Os elementos podem mudar
```

Enquanto:

```text
TUPLA

↓

Estrutura imutável

↓

Os elementos da tupla não podem ser
adicionados, removidos ou substituídos
```

Exemplo de lista:

```python
hosts = [
    "192.168.1.10",
    "192.168.1.20"
]
```

Novos hosts podem aparecer.

---

Exemplo de tupla:

```python
servico = (
    "SSH",
    22
)
```

Temos uma estrutura fixa:

```text
(nome, porta)
```

---

# Resumo inicial

```text
list

↓

[]
```

É:

```text
Ordenada

Mutável

Indexável

Aceita valores repetidos

Aceita diferentes tipos

Pode armazenar outras estruturas
```

Principais operações vistas até agora:

```python
lista[indice]

lista[indice] = valor

lista.append(valor)

lista.remove(valor)

lista.pop()
```