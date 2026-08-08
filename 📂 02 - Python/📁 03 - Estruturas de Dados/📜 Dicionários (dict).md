Um dicionário (`dict`) é uma estrutura de dados do Python utilizada para armazenar informações no formato:

---

```text
chave → valor
```

Exemplo:

```python
usuario = {
    "nome": "Ramon",
    "idade": 18,
    "ativo": True
}
```

Nesse dicionário temos:

```text
chave       valor

nome   →    Ramon
idade  →    18
ativo  →    True
```

Diferentemente de uma lista, normalmente não acessamos os dados pela posição.

Utilizamos a **chave**.

---

# Lista x Dicionário

Em uma lista:

```python
usuario = [
    "Ramon",
    18,
    True
]
```

Para obter o nome:

```python
usuario[0]
```

Precisamos saber que:

```text
índice 0 = nome
índice 1 = idade
índice 2 = ativo
```

Em um dicionário:

```python
usuario = {
    "nome": "Ramon",
    "idade": 18,
    "ativo": True
}
```

Podemos fazer:

```python
usuario["nome"]
```

Resultado:

```text
Ramon
```

Isso deixa os dados muito mais descritivos.

---

# Criando um dicionário

A forma mais comum utiliza:

```python
{}
```

Exemplo:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 22,
    "servico": "SSH"
}
```

---

# Dicionário vazio

```python
dados = {}
```

Também podemos utilizar:

```python
dados = dict()
```

Os dois criam:

```python
{}
```

---

# Utilizando dict()

`dict()` é a classe utilizada para criar dicionários.

Uma forma possível é:

```python
usuario = dict(
    nome="Ramon",
    idade=18,
    ativo=True
)
```

Resultado:

```python
{
    "nome": "Ramon",
    "idade": 18,
    "ativo": True
}
```

---

# Chave e valor

Cada entrada possui duas partes:

```python
"nome": "Ramon"
```

Temos:

```text
"nome"

↓

chave
```

e:

```text
"Ramon"

↓

valor
```

A separação é feita utilizando:

```python
:
```

E as entradas são separadas por:

```python
,
```

Exemplo:

```python
dados = {
    "nome": "Ramon",
    "idade": 18,
    "cidade": "São Paulo"
}
```

---

# O que um valor pode receber?

O valor pode armazenar praticamente qualquer objeto Python.

Exemplo:

```python
dados = {
    "nome": "Ramon",
    "idade": 18,
    "altura": 1.75,
    "ativo": True,
    "observacao": None,
    "portas": [22, 80, 443],
    "coordenada": (10, 20),
    "config": {
        "debug": False
    }
}
```

Temos valores do tipo:

```text
str
int
float
bool
None
list
tuple
dict
```

---

# E as chaves?

Aqui existe uma regra importante.

As chaves precisam ser objetos **hashable**.

Na prática, para começar, pense que tipos imutáveis normalmente podem ser utilizados como chave.

Muito comum:

```python
str
```

Exemplo:

```python
{
    "nome": "Ramon"
}
```

Também podemos utilizar números:

```python
{
    22: "SSH",
    80: "HTTP",
    443: "HTTPS"
}
```

E tuplas adequadas também podem ser chaves:

```python
servicos = {
    ("192.168.1.10", 22): "SSH"
}
```

---

# Lista pode ser chave?

❌ Não.

Isso gera erro:

```python
dados = {
    [1, 2]: "teste"
}
```

Porque uma `list` é mutável e não é hashable.

O Python gera:

```text
TypeError: unhashable type: 'list'
```

---

# Dicionários são mutáveis

Assim como listas, dicionários podem ser modificados depois de criados.

Exemplo:

```python
usuario = {
    "nome": "Ramon",
    "idade": 18
}
```

Podemos alterar:

```python
usuario["idade"] = 19
```

Resultado:

```python
{
    "nome": "Ramon",
    "idade": 19
}
```

---

# Adicionando uma nova chave

A mesma sintaxe pode criar uma nova entrada.

```python
usuario["sistema"] = "Linux"
```

Resultado:

```python
{
    "nome": "Ramon",
    "idade": 19,
    "sistema": "Linux"
}
```

Observe a diferença.

Se a chave já existe:

```python
usuario["idade"] = 20
```

ela é alterada.

Se não existe:

```python
usuario["cidade"] = "Recife"
```

ela é criada.

---

# Acessando valores

Forma básica:

```python
usuario["nome"]
```

Exemplo:

```python
usuario = {
    "nome": "Ramon",
    "idade": 18
}

print(
    usuario["nome"]
)
```

Resultado:

```text
Ramon
```

---

# E se a chave não existir?

Imagine:

```python
usuario = {
    "nome": "Ramon"
}
```

Fazendo:

```python
usuario["senha"]
```

O Python gera:

```text
KeyError
```

Porque:

```text
senha
```

não existe no dicionário.

---

# get()

Existe outra maneira muito importante de buscar valores:

```python
.get()
```

Sintaxe:

```python
dicionario.get(chave, padrao)
```

---

# Parâmetro chave

É a chave que queremos procurar.

É obrigatório.

Exemplo:

```python
usuario.get("nome")
```

Resultado:

```text
Ramon
```

---

# Parâmetro padrao

É opcional.

Define o valor retornado caso a chave não exista.

Exemplo:

```python
usuario.get(
    "senha",
    "Não encontrada"
)
```

Resultado:

```text
Não encontrada
```

---

# E se não informar o valor padrão?

```python
usuario.get("senha")
```

Resultado:

```python
None
```

Portanto:

```python
usuario["senha"]
```

pode gerar:

```text
KeyError
```

Enquanto:

```python
usuario.get("senha")
```

retorna:

```python
None
```

se a chave não existir.

---

# [] x get()

Utilizando:

```python
dados["chave"]
```

É útil quando a chave **deve existir**.

Se não existir:

```text
KeyError
```

---

Utilizando:

```python
dados.get("chave")
```

É útil quando a chave pode não existir.

Se não existir:

```text
None
```

ou o valor padrão informado.

---

# Dicionários aninhados

Um dicionário pode armazenar outro dicionário.

Exemplo:

```python
host = {
    "ip": "192.168.1.10",

    "servico": {
        "nome": "SSH",
        "porta": 22
    }
}
```

Visualmente:

```text
host
│
├── ip
│   └── 192.168.1.10
│
└── servico
    │
    ├── nome
    │   └── SSH
    │
    └── porta
        └── 22
```

---

# Acessando dados aninhados

Primeiro:

```python
host["servico"]
```

Resultado:

```python
{
    "nome": "SSH",
    "porta": 22
}
```

Agora:

```python
host["servico"]["porta"]
```

Resultado:

```text
22
```

Como ler:

```text
host

↓

pegue "servico"

↓

dentro de "servico"

↓

pegue "porta"
```

---

# Dicionário contendo listas

Muito comum:

```python
host = {
    "ip": "192.168.1.10",

    "portas": [
        22,
        80,
        443
    ]
}
```

Para acessar a lista:

```python
host["portas"]
```

Resultado:

```python
[22, 80, 443]
```

---

Para acessar a primeira porta:

```python
host["portas"][0]
```

Resultado:

```text
22
```

Como ler:

```text
host["portas"]

↓

[22, 80, 443]

↓

[0]

↓

22
```

---

# Lista contendo dicionários

Essa estrutura aparece MUITO em Python.

```python
hosts = [
    {
        "ip": "192.168.1.10",
        "porta": 22
    },

    {
        "ip": "192.168.1.20",
        "porta": 80
    }
]
```

Temos:

```text
list
│
├── dict
│
└── dict
```

---

# Acessando

```python
hosts[0]
```

Resultado:

```python
{
    "ip": "192.168.1.10",
    "porta": 22
}
```

Agora:

```python
hosts[0]["ip"]
```

Resultado:

```text
192.168.1.10
```

---

# Percorrendo um dicionário

Imagine:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 22,
    "servico": "SSH"
}
```

Podemos utilizar:

```python
for chave in host:

    print(chave)
```

Resultado:

```text
ip
porta
servico
```

Por padrão, iterar diretamente sobre um dicionário percorre suas **chaves**.

---

# Obtendo o valor durante o for

```python
for chave in host:

    print(
        chave,
        host[chave]
    )
```

Resultado:

```text
ip 192.168.1.10
porta 22
servico SSH
```

---

# Exemplo em Cyber Security

Dicionários são excelentes para representar resultados estruturados.

Exemplo de um resultado fictício de scanner em laboratório:

```python
host = {
    "ip": "192.168.1.10",

    "sistema": "Linux",

    "portas": [
        22,
        80,
        443
    ],

    "servicos": {
        22: "SSH",
        80: "HTTP",
        443: "HTTPS"
    }
}
```

Agora podemos consultar:

```python
host["ip"]
```

Resultado:

```text
192.168.1.10
```

---

```python
host["portas"]
```

Resultado:

```python
[22, 80, 443]
```

---

```python
host["servicos"][22]
```

Resultado:

```text
SSH
```

---

# Representando uma vulnerabilidade

Outro exemplo:

```python
vulnerabilidade = {
    "nome": "SQL Injection",
    "severidade": "Alta",
    "endpoint": "/login",
    "confirmada": True
}
```

Podemos verificar:

```python
if vulnerabilidade["confirmada"]:

    print(
        vulnerabilidade["nome"]
    )
```

Resultado:

```text
SQL Injection
```

Esse tipo de estrutura é muito útil para organizar resultados de ferramentas.

---

# Dicionários e JSON

Você verá estruturas muito parecidas com dicionários quando trabalhar com JSON.

Exemplo Python:

```python
usuario = {
    "username": "admin",
    "ativo": True
}
```

É importante não confundir:

```text
dict

↓

Objeto Python
```

com:

```text
JSON

↓

Formato textual de troca de dados
```

Eles possuem aparência parecida, mas não são exatamente a mesma coisa.

---

# Quando utilizar um dicionário?

Quando os dados possuem significado associado a nomes.

Por exemplo:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 443,
    "servico": "HTTPS"
}
```

É mais descritivo do que:

```python
host = [
    "192.168.1.10",
    443,
    "HTTPS"
]
```

Na lista precisamos lembrar:

```text
0 = IP
1 = porta
2 = serviço
```

No dicionário:

```python
host["ip"]

host["porta"]

host["servico"]
```

O próprio código explica o significado dos dados.

---

# Regra mental

Pense assim:

```text
LIST

↓

Coleção de elementos

↓

["SSH", "HTTP", "HTTPS"]
```

---

```text
TUPLE

↓

Estrutura fixa

↓

("SSH", 22)
```

---

```text
DICT

↓

Dados associados por chave e valor

↓

{
    "servico": "SSH",
    "porta": 22
}
```

---

# Resumo inicial

Um `dict`:

```text
É mutável

Armazena chave → valor

Pode ser alterado

Pode receber novas entradas

Pode conter listas

Pode conter tuplas

Pode conter outros dicionários

Pode armazenar praticamente qualquer objeto como valor
```

Principais operações vistas:

```python
dados["chave"]

dados["chave"] = valor

dados.get("chave")

dados.get("chave", padrao)

for chave in dados:
    ...
```

---

# keys()

O método:

```python
.keys()
```

retorna uma visão com todas as chaves do dicionário.

Exemplo:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 22,
    "servico": "SSH"
}

print(
    host.keys()
)
```

Resultado:

```python
dict_keys([
    'ip',
    'porta',
    'servico'
])
```

---

# O que keys() recebe?

Nada.

Sintaxe:

```python
dicionario.keys()
```

Não recebe parâmetros.

---

# O que retorna?

Retorna um objeto do tipo:

```python
dict_keys
```

Não é uma lista.

Mas pode ser percorrido normalmente.

```python
for chave in host.keys():

    print(chave)
```

Resultado:

```text
ip
porta
servico
```

---

# Preciso usar keys() no for?

Não.

Isso:

```python
for chave in host:

    print(chave)
```

é equivalente a:

```python
for chave in host.keys():

    print(chave)
```

Na prática, a primeira forma costuma ser mais usada.

---

# Convertendo para lista

Se realmente precisar de uma lista:

```python
chaves = list(
    host.keys()
)
```

Resultado:

```python
[
    "ip",
    "porta",
    "servico"
]
```

---

# Quando utilizar keys()?

Quando você quer trabalhar explicitamente com as chaves.

Exemplo:

```python
if "porta" in host.keys():

    print("A chave existe")
```

Porém, normalmente é ainda melhor escrever:

```python
if "porta" in host:

    print("A chave existe")
```

---

# values()

O método:

```python
.values()
```

retorna todos os valores do dicionário.

Exemplo:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 22,
    "servico": "SSH"
}

print(
    host.values()
)
```

Resultado:

```python
dict_values([
    '192.168.1.10',
    22,
    'SSH'
])
```

---

# O que values() recebe?

Nada.

Sintaxe:

```python
dicionario.values()
```

Não recebe parâmetros.

---

# O que retorna?

Retorna:

```python
dict_values
```

---

# Percorrendo os valores

```python
for valor in host.values():

    print(valor)
```

Resultado:

```text
192.168.1.10
22
SSH
```

---

# Convertendo para lista

```python
valores = list(
    host.values()
)
```

Resultado:

```python
[
    "192.168.1.10",
    22,
    "SSH"
]
```

---

# Quando utilizar values()?

Quando as chaves não importam e você quer apenas os valores.

Exemplo:

```python
servicos = {
    22: "SSH",
    80: "HTTP",
    443: "HTTPS"
}

for servico in servicos.values():

    print(servico)
```

Resultado:

```text
SSH
HTTP
HTTPS
```

---

# items()

Esse é provavelmente o método mais importante para percorrer dicionários.

O método:

```python
.items()
```

retorna pares no formato:

```text
(chave, valor)
```

Exemplo:

```python
host = {
    "ip": "192.168.1.10",
    "porta": 22,
    "servico": "SSH"
}

print(
    host.items()
)
```

Resultado:

```python
dict_items([
    ('ip', '192.168.1.10'),
    ('porta', 22),
    ('servico', 'SSH')
])
```

---

# O que items() recebe?

Nada.

Sintaxe:

```python
dicionario.items()
```

Não recebe parâmetros.

---

# O que retorna?

Retorna:

```python
dict_items
```

Cada elemento representa:

```python
(chave, valor)
```

---

# Forma mais utilizada

```python
for chave, valor in host.items():

    print(
        chave,
        valor
    )
```

Resultado:

```text
ip 192.168.1.10
porta 22
servico SSH
```

---

# Como funciona?

O `items()` produz algo parecido com:

```python
(
    "ip",
    "192.168.1.10"
)
```

Depois o `for` desempacota:

```text
"ip"

↓

chave


"192.168.1.10"

↓

valor
```

Por isso conseguimos fazer:

```python
for chave, valor in host.items():
```

---

# Sem items()

Também poderíamos escrever:

```python
for chave in host:

    print(
        chave,
        host[chave]
    )
```

Funciona.

Mas:

```python
for chave, valor in host.items():

    print(chave, valor)
```

costuma ficar mais limpo.

---

# keys() x values() x items()

| Método | Retorna |
|---|---|
| `keys()` | Chaves |
| `values()` | Valores |
| `items()` | Chave + valor |

---

# Forma mental

```text
.keys()

↓

"ip"
"porta"
"servico"
```

---

```text
.values()

↓

"192.168.1.10"
22
"SSH"
```

---

```text
.items()

↓

("ip", "192.168.1.10")

("porta", 22)

("servico", "SSH")
```

---

# Exemplo em Cyber Security

Imagine resultados de enumeração:

```python
servicos = {
    22: "SSH",
    80: "HTTP",
    443: "HTTPS"
}
```

Percorrendo:

```python
for porta, servico in servicos.items():

    print(
        f"{porta}/tcp -> {servico}"
    )
```

Resultado:

```text
22/tcp -> SSH
80/tcp -> HTTP
443/tcp -> HTTPS
```

Esse formato é extremamente útil em scanners, parsers e geração de relatórios.

---

# Outro exemplo

```python
host = {
    "ip": "10.10.10.10",
    "os": "Linux",
    "hostname": "nexus"
}
```

```python
for campo, valor in host.items():

    print(
        f"{campo}: {valor}"
    )
```

Resultado:

```text
ip: 10.10.10.10
os: Linux
hostname: nexus
```

---

# Qual é mais usado?

Para percorrer apenas chaves:

```python
for chave in dicionario:
```

é a forma mais comum.

Para percorrer apenas valores:

```python
for valor in dicionario.values():
```

Para trabalhar com chave e valor juntos:

```python
for chave, valor in dicionario.items():
```

Essa última aparece o tempo todo em código Python real.