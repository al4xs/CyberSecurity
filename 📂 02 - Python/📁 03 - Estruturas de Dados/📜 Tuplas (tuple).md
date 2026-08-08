Uma tupla é uma estrutura de dados utilizada para armazenar vários valores.

---

Exemplo.

```python
cores = (

    "vermelho",

    "verde",

    "azul"

)
```

Uma das principais características das tuplas é que elas são.

```text
Imutáveis
```

Isso significa que, depois que uma tupla é criada, sua estrutura não pode ser modificada.

---

# Lista x Tupla

Uma lista é criada normalmente utilizando.

```python
[]
```

Exemplo.

```python
cores = [

    "vermelho",

    "verde",

    "azul"

]
```

Uma tupla normalmente utiliza.

```python
()
```

Exemplo.

```python
cores = (

    "vermelho",

    "verde",

    "azul"

)
```

---

# O que significa imutável?

Significa que não podemos alterar diretamente os elementos armazenados pela tupla.

Exemplo.

```python
cores = (

    "vermelho",

    "verde",

    "azul"

)

cores[0] = "preto"
```

Isso gera.

```text
TypeError
```

Porque estamos tentando substituir.

```text
vermelho

↓

preto
```

dentro da própria tupla.

---

# Também não podemos adicionar elementos

Em listas podemos utilizar.

```python
lista.append("novo")
```

Tuplas não possuem.

```python
append()
```

---

# Também não podemos remover elementos

Listas possuem métodos como.

```python
remove()

pop()
```

Tuplas não possuem esses métodos.

---

# Mas podemos acessar os valores

Imutável não significa que os dados ficam inacessíveis.

Podemos normalmente fazer.

```python
cores = (

    "vermelho",

    "verde",

    "azul"

)

print(cores[0])
```

Resultado.

```text
vermelho
```

---

Também podemos utilizar índices negativos.

```python
print(cores[-1])
```

Resultado.

```text
azul
```

---

E também podemos utilizar slicing.

```python
print(

    cores[0:2]

)
```

Resultado.

```python
(
    'vermelho',
    'verde'
)
```

---

# Uma tupla pode armazenar qualquer tipo?

Sim.

Por exemplo.

```python
dados = (

    "Ramon",

    18,

    1.75,

    True,

    None

)
```

Ela também pode armazenar outras estruturas.

```python
dados = (

    [1, 2, 3],

    {"nome": "Ramon"},

    ("Python", "Linux")

)
```

Aqui temos dentro da tupla.

```text
list

dict

tuple
```

---

# Tupla contendo objetos mutáveis

Essa é uma parte muito importante.

Uma tupla é imutável.

Porém, ela pode armazenar objetos que são mutáveis.

Exemplo.

```python
cores = (

    ["rosa", "amarelo", "preto"],

    ["ciano", "roxo", "vermelho"]

)
```

Temos.

```text
tuple

├── list
│   ├── rosa
│   ├── amarelo
│   └── preto
│
└── list
    ├── ciano
    ├── roxo
    └── vermelho
```

---

Podemos fazer.

```python
cores[0][0] = "neutro"
```

Depois.

```python
print(cores)
```

Resultado.

```python
(
    ['neutro', 'amarelo', 'preto'],

    ['ciano', 'roxo', 'vermelho']
)
```

---

# Mas por que isso funciona?

Porque não alteramos a estrutura da tupla.

A tupla continua contendo as mesmas duas listas.

Antes.

```text
tuple

↓

lista A

lista B
```

Depois.

```text
tuple

↓

lista A

lista B
```

O que mudou foi o conteúdo da.

```text
lista A
```

---

# O que NÃO podemos fazer?

Isso.

```python
cores[0] = [

    "branco",

    "preto"

]
```

❌ Não funciona.

Porque agora estamos tentando substituir um elemento da própria tupla.

---

# Diferença importante

Isso funciona.

```python
cores[0][0] = "neutro"
```

Porque estamos modificando.

```text
lista
```

---

Isso não funciona.

```python
cores[0] = ["neutro"]
```

Porque estamos modificando.

```text
tupla
```

---

# Outra demonstração

```python
dados = (

    [1, 2, 3],

    "Python"

)
```

Podemos fazer.

```python
dados[0].append(4)
```

Resultado.

```python
(
    [1, 2, 3, 4],

    "Python"
)
```

A lista é mutável.

---

Também podemos fazer.

```python
dados[0].remove(2)
```

Resultado.

```python
(
    [1, 3, 4],

    "Python"
)
```

Novamente.

A tupla continua contendo a mesma lista.

Quem mudou foi a lista.

---

# Uma forma melhor de entender

Pense na tupla como posições que não podem ser substituídas.

```text
tuple

posição 0 ──────► lista

posição 1 ──────► string
```

Não podemos fazer.

```text
posição 0 ──────► outro objeto
```

Mas se o objeto naquela posição for mutável, podemos modificar o próprio objeto.

---

# Quando utilizar uma tupla?

Uma boa regra mental é.

```text
Os valores representam uma estrutura que não deveria mudar?

↓

tuple
```

Caso a coleção precise crescer, diminuir ou ter elementos substituídos frequentemente.

```text
list
```

---

# Exemplo

Coordenadas.

```python
coordenada = (

    1920,

    1080

)
```

Faz sentido representar isso como uma tupla.

Temos uma estrutura fixa.

```text
x

y
```

---

Outro exemplo.

Informações retornadas por uma função.

```python
def obter_host():

    return "192.168.1.10", 80
```

Podemos receber.

```python
host, porta = obter_host()
```

Os valores retornados estão agrupados em uma tupla.

---

# Quando utilizar lista?

Quando queremos modificar a coleção.

```python
hosts = [

    "192.168.1.10",

    "192.168.1.20"

]
```

Podemos descobrir outro host.

```python
hosts.append(

    "192.168.1.30"

)
```

Nesse caso uma lista faz muito mais sentido.

---

# Regra mental

```text
LISTA

↓

Coleção dinâmica

↓

Pretendo adicionar, remover ou substituir elementos
```

```text
TUPLA

↓

Estrutura fixa

↓

Não pretendo adicionar, remover ou substituir elementos
```

---

# Importante

Isso não significa que uma tupla deve ser utilizada simplesmente porque você "não pretende alterar os dados".

Tuplas também são muito utilizadas quando os valores juntos representam uma única estrutura.

Exemplo.

```python
("192.168.1.10", 443)
```

Pode representar.

```text
(host, porta)
```

Enquanto.

```python
[
    "192.168.1.10",
    "192.168.1.20",
    "192.168.1.30"
]
```

representa uma coleção de hosts.

Essa diferença de significado é muito importante em código Python.

---

# Resumo

```text
list

↓

Mutável

↓

Pode adicionar

Pode remover

Pode substituir
```

```text
tuple

↓

Imutável

↓

Não pode adicionar

Não pode remover

Não pode substituir
```

Porém.

```text
tuple
   ↓
   list
   ↓
mutável
```

Se uma tupla armazenar um objeto mutável, o conteúdo desse objeto ainda pode ser modificado.