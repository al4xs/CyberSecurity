
O Python oferece duas formas principais de ordenar dados:

- `list.sort()`
- `sorted()`

Apesar de ambas ordenarem elementos, elas possuem diferenças importantes.

Saber quando utilizar cada uma delas é uma habilidade importante, pois aparecem constantemente em projetos Python, automações, scripts de Pentest e ferramentas Open Source.

---

# O que é uma ordenação?

Ordenar significa reorganizar uma sequência de elementos seguindo algum critério.

Normalmente:

- Ordem crescente
- Ordem decrescente
- Ordem alfabética
- Tamanho da palavra
- Data
- Idade
- Prioridade
- Qualquer critério criado pelo programador

Exemplo.

Antes

```python
[9, 5, 1, 8, 2]
```

Depois

```python
[1, 2, 5, 8, 9]
```

---

# Formas de ordenar no Python

Existem duas maneiras.

## 1. sort()

Método da lista.

Modifica a lista original.

```python
lista.sort()
```

---

## 2. sorted()

Função nativa do Python.

Retorna uma nova lista ordenada.

```python
nova_lista = sorted(lista)
```

A lista original permanece inalterada.

---

# Diferença principal

## sort()

```text
Lista original
        │
        ▼
sort()
        │
        ▼
A própria lista é modificada
```

---

## sorted()

```text
Lista original
        │
        ▼
sorted()
        │
        ▼
Nova lista ordenada
```

---

# Quando utilizar cada um?

## Utilize sort()

Quando você deseja modificar a lista atual.

Exemplo.

```python
usuarios.sort()
```

Após isso, a lista estará definitivamente ordenada.

---

## Utilize sorted()

Quando deseja preservar os dados originais.

Exemplo.

```python
usuarios_ordenados = sorted(usuarios)
```

Agora existem duas listas.

A original.

E outra ordenada.

---

# Sintaxe do sort()

```python
lista.sort(*, key=None, reverse=False)
```

---

# Como ler essa sintaxe?

Muitas pessoas olham essa assinatura e não entendem nada.

Vamos separar.

```python
lista.sort(
    *,
    key=None,
    reverse=False
)
```

Existem apenas dois parâmetros que podem ser utilizados.

- key
- reverse

O símbolo `*` possui um significado especial.

Ele indica que todos os parâmetros após ele devem ser informados pelo nome.

Por isso fazemos:

```python
lista.sort(reverse=True)
```

e não

```python
lista.sort(True)
```

Isso deixa o código muito mais legível.

---

# Sintaxe do sorted()

```python
sorted(iterable, *, key=None, reverse=False)
```

Perceba que agora apareceu um parâmetro novo.

```python
iterable
```

Porque `sorted()` não pertence à lista.

Ela pode ordenar vários tipos de objetos.

---

# Parâmetros

## iterable

### O que é?

É o objeto que será ordenado.

---

### É obrigatório?

✅ Sim.

Sem ele o Python não sabe o que ordenar.

---

### O que recebe?

Qualquer objeto iterável.

Por exemplo.

- list
- tuple
- set
- dict
- str
- range
- generator
- qualquer objeto iterável criado por você

---

### Exemplos

Lista

```python
sorted([3,1,2])
```

---

Tupla

```python
sorted((3,1,2))
```

---

String

```python
sorted("python")
```

---

Set

```python
sorted({4,1,8,2})
```

---

Range

```python
sorted(range(10))
```

---

### O que NÃO recebe?

Não recebe objetos que não podem ser percorridos.

Exemplo.

```python
sorted(100)
```

Erro.

```text
TypeError

'int' object is not iterable
```

---

# key

## O que é?

Define qual será o critério utilizado para ordenar.

Por padrão, o Python utiliza sua ordenação natural.

Mas às vezes queremos ordenar por:

- tamanho da palavra
- idade
- nome
- data
- prioridade
- IP
- severidade
- qualquer outro critério

Para isso existe o parâmetro `key`.

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
None
```

Quando `None`, o Python utiliza sua ordenação padrão.

Ou seja.

```python
sorted(lista)
```

é exatamente igual a

```python
sorted(lista, key=None)
```

---

## O que recebe?

Recebe uma função.

Pode ser.

Função nativa.

```python
len
```

---

Função criada por você.

```python
def idade(pessoa):

    return pessoa["idade"]
```

---

Função anônima.

```python
lambda pessoa: pessoa["idade"]
```

---

Métodos.

```python
str.lower
```

---

## Funciona sozinho?

✅ Sim.

```python
sorted(lista, key=len)
```

---

## Funciona junto com reverse?

✅ Sim.

```python
sorted(
    lista,
    key=len,
    reverse=True
)
```

Veremos isso em detalhes na próxima parte.

---

# reverse

## O que é?

Controla a direção da ordenação.

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
False
```

Portanto.

```python
sorted(lista)
```

é igual a

```python
sorted(lista, reverse=False)
```

---

## Valores aceitos

```python
True
```

Ordenação decrescente.

---

```python
False
```

Ordenação crescente.

---

## Funciona sozinho?

✅ Sim.

```python
sorted(lista, reverse=True)
```

---

## Funciona junto com key?

✅ Sim.

```python
sorted(
    lista,
    key=len,
    reverse=True
)
```

---

# Ordem de execução

Quando usamos.

```python
sorted(
    lista,
    key=len,
    reverse=True
)
```

O Python faz.

```text
Lista
      │
      ▼
Aplica key
      │
      ▼
Ordena utilizando esse critério
      │
      ▼
Aplica reverse
      │
      ▼
Retorna a lista
```

Ou seja.

Primeiro ele decide COMO comparar.

Depois ordena.

E somente no final inverte a ordem.

---

# Valor de retorno

## sort()

Retorna.

```python
None
```

Muita gente comete este erro.

```python
nova = lista.sort()
```

Resultado.

```python
None
```

Porque `sort()` modifica a própria lista.

---

## sorted()

Retorna.

```python
list
```

Sempre retorna uma nova lista.

Mesmo quando recebe uma tupla.

```python
sorted((5,1,2))
```

Retorno.

```python
[1,2,5]
```

Observe.

A entrada era uma tupla.

Mas o retorno continua sendo uma lista.

---

# Resumo da Parte 1

| Característica | sort() | sorted() |
|----------------|:------:|:--------:|
| Modifica a lista original | ✅ | ❌ |
| Retorna nova lista | ❌ | ✅ |
| Retorna None | ✅ | ❌ |
| Funciona apenas com listas | ✅ | ❌ |
| Funciona com qualquer iterável | ❌ | ✅ |

---

Na próxima parte veremos:

- Ordenando números.
- Ordenando strings.
- Ordenação crescente.
- Ordenação decrescente.
- Como utilizar `key`.
- Como utilizar `reverse`.
- Todas as combinações possíveis.
- Exemplos reais utilizados em projetos.

---

# Exemplos básicos

## Ordenando números

O exemplo mais comum.

```python
numeros = [8, 3, 5, 1, 9]

resultado = sorted(numeros)

print(resultado)
```

Saída.

```python
[1, 3, 5, 8, 9]
```

Observe.

A lista original permanece igual.

```python
print(numeros)
```

Saída.

```python
[8, 3, 5, 1, 9]
```

Porque `sorted()` cria uma nova lista.

---

Agora utilizando `sort()`.

```python
numeros = [8, 3, 5, 1, 9]

numeros.sort()

print(numeros)
```

Saída.

```python
[1, 3, 5, 8, 9]
```

Neste caso a lista original foi modificada.

---

# Comparando os dois

## sorted()

```python
lista = [3, 1, 2]

nova = sorted(lista)

print(lista)

print(nova)
```

Saída.

```python
[3, 1, 2]

[1, 2, 3]
```

---

## sort()

```python
lista = [3, 1, 2]

lista.sort()

print(lista)
```

Saída.

```python
[1, 2, 3]
```

---

# Erro muito comum

Muitos iniciantes fazem isto.

```python
lista = [5,2,1]

nova = lista.sort()

print(nova)
```

Resultado.

```python
None
```

Por quê?

Porque.

```python
sort()
```

não retorna a lista.

Ele modifica a lista existente.

O correto.

```python
lista.sort()

print(lista)
```

Ou.

```python
nova = sorted(lista)
```

---

# Ordenando Strings

O Python também consegue ordenar texto.

```python
nomes = [

    "Carlos",

    "Ana",

    "Bruno"

]

print(sorted(nomes))
```

Saída.

```python
[
    'Ana',
    'Bruno',
    'Carlos'
]
```

---

# Como ocorre a ordenação?

O Python utiliza a tabela Unicode.

Por isso.

```python
print(sorted(["a","b","c"]))
```

Resultado.

```python
[
'a',
'b',
'c'
]
```

Mas.

```python
print(sorted(["Z","a","B"]))
```

Resultado.

```python
[
'B',
'Z',
'a'
]
```

Porque letras maiúsculas possuem códigos menores que letras minúsculas.

Isso costuma surpreender quem está começando.

Na Parte 4 veremos como resolver isso utilizando:

```python
key=str.lower
```

---

# Ordenação Crescente

É o comportamento padrão.

```python
sorted(lista)
```

é igual a

```python
sorted(
    lista,
    reverse=False
)
```

Exemplo.

```python
numeros = [8,5,1,9]

print(sorted(numeros))
```

Saída.

```python
[1,5,8,9]
```

---

# Ordenação Decrescente

Para inverter.

Utilizamos.

```python
reverse=True
```

Exemplo.

```python
numeros = [8,5,1,9]

print(

    sorted(

        numeros,

        reverse=True

    )

)
```

Saída.

```python
[9,8,5,1]
```

---

# O parâmetro reverse

Sintaxe.

```python
sorted(
    iterable,
    reverse=True
)
```

---

## O que recebe?

Recebe apenas valores booleanos.

```python
True
```

ou

```python
False
```

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
False
```

---

## Posso utilizar sozinho?

✅ Sim.

```python
sorted(

    numeros,

    reverse=True

)
```

---

## Posso utilizar sem key?

✅ Sim.

É a forma mais utilizada.

```python
sorted(

    numeros,

    reverse=True

)
```

---

## Posso utilizar junto com key?

✅ Sim.

```python
sorted(

    nomes,

    key=len,

    reverse=True

)
```

Primeiro o Python utiliza o critério informado em `key`.

Depois inverte o resultado.

---

# O parâmetro key

Sintaxe.

```python
sorted(

    iterable,

    key=...
)
```

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
None
```

---

## Posso utilizar sozinho?

✅ Sim.

```python
sorted(

    lista,

    key=len

)
```

---

## Posso utilizar junto com reverse?

✅ Sim.

```python
sorted(

    lista,

    key=len,

    reverse=True

)
```

Essa combinação aparece bastante em projetos.

---

# Combinações possíveis

## Apenas iterable

```python
sorted(lista)
```

Forma mais utilizada.

---

## iterable + reverse

```python
sorted(

    lista,

    reverse=True

)
```

Muito utilizada.

---

## iterable + key

```python
sorted(

    lista,

    key=len

)
```

Também muito utilizada.

---

## iterable + key + reverse

```python
sorted(

    lista,

    key=len,

    reverse=True

)
```

Muito comum quando trabalhamos com objetos.

---

# Forma mais utilizada

Na maioria dos projetos Python você encontrará apenas estas duas.

```python
sorted(lista)
```

ou.

```python
sorted(

    lista,

    reverse=True

)
```

O uso de `key` aparece quando precisamos ordenar por um critério específico, como tamanho de uma string, idade de uma pessoa ou prioridade de um item.

---

# Em projetos de Cyber Security

Também é comum ordenar listas antes de exibir os resultados.

Exemplo.

```python
portas = [

    8080,

    22,

    443,

    80

]

portas.sort()

print(portas)
```

Saída.

```python
[
22,
80,
443,
8080
]
```

Isso facilita muito a leitura da saída de scanners, enumeradores e ferramentas de automação.

Na próxima parte veremos como utilizar `key` para criar critérios personalizados de ordenação.

---

# Utilizando o parâmetro key

Até agora utilizamos apenas a ordenação padrão do Python.

Porém, nem sempre queremos ordenar um objeto pelo seu valor.

Às vezes queremos ordenar por:

- Tamanho de uma palavra.
- Idade de uma pessoa.
- Severidade de uma vulnerabilidade.
- Quantidade de portas abertas.
- Data.
- Nome.
- Prioridade.

Para isso utilizamos o parâmetro:

```python
key=
```

---

# Como o key funciona?

Imagine esta lista.

```python
nomes = [

    "Carlos",

    "Ana",

    "Fernanda",

    "João"

]
```

Se utilizarmos.

```python
sorted(nomes)
```

Resultado.

```python
[
'Ana',
'Carlos',
'Fernanda',
'João'
]
```

O Python ordenou alfabeticamente.

Agora imagine que queremos ordenar pelo tamanho do nome.

Para isso.

```python
sorted(

    nomes,

    key=len

)
```

Resultado.

```python
[
'Ana',
'João',
'Carlos',
'Fernanda'
]
```

Observe.

O Python não comparou mais as palavras.

Ele comparou.

```text
Ana

↓

3

------------------

João

↓

4

------------------

Carlos

↓

6

------------------

Fernanda

↓

9
```

---

# Como o Python faz isso?

Fluxo.

```text
Lista

↓

key()

↓

Novo valor

↓

Ordenação

↓

Resultado
```

Ou seja.

O Python chama a função informada em `key` para cada elemento.

Depois utiliza o retorno dessa função para ordenar.

---

# key=len

É provavelmente o uso mais comum.

```python
nomes = [

    "Carlos",

    "Ana",

    "Fernanda",

    "João"

]

resultado = sorted(

    nomes,

    key=len

)

print(resultado)
```

Saída.

```python
[
'Ana',
'João',
'Carlos',
'Fernanda'
]
```

---

# O que o len recebe?

```python
len(objeto)
```

Recebe qualquer objeto que possua tamanho.

Pode receber.

- list
- tuple
- dict
- set
- str

Não recebe.

```python
len(100)
```

Erro.

```text
TypeError
```

---

# Outro exemplo

```python
palavras = [

    "python",

    "c",

    "assembly",

    "bash"

]

print(

    sorted(

        palavras,

        key=len

    )

)
```

Resultado.

```python
[
'c',
'bash',
'python',
'assembly'
]
```

---

# key=str.lower

Outro uso extremamente comum.

Problema.

```python
nomes = [

    "Carlos",

    "ana",

    "Bruno",

    "joao"

]

print(sorted(nomes))
```

Resultado.

```python
[
'Bruno',
'Carlos',
'ana',
'joao'
]
```

Isso acontece porque.

```text
A

↓

Unicode menor

↓

vem primeiro

------------------

a

↓

Unicode maior

↓

vem depois
```

---

Podemos resolver.

```python
sorted(

    nomes,

    key=str.lower

)
```

Resultado.

```python
[
'ana',
'Bruno',
'Carlos',
'joao'
]
```

Agora todas as comparações são feitas utilizando letras minúsculas.

---

# key com função própria

Também podemos criar nossa própria função.

```python
def tamanho(texto):

    return len(texto)
```

Depois.

```python
sorted(

    nomes,

    key=tamanho

)
```

Resultado.

O mesmo obtido com.

```python
key=len
```

---

# Posso passar qualquer função?

Não.

A função deve receber exatamente um elemento da lista.

Exemplo.

Lista.

```python
nomes = [

    "Ana",

    "Carlos",

    "João"
]
```

Python faz.

```text
tamanho("Ana")

↓

3

-------------------

tamanho("Carlos")

↓

6

-------------------

tamanho("João")

↓

4
```

Se sua função não aceitar apenas um parâmetro.

Ocorrerá erro.

---

# Forma mais utilizada

Na prática.

Estas são as mais comuns.

```python
key=len
```

---

```python
key=str.lower
```

---

```python
key=lambda ...
```

A última veremos na próxima parte.

Ela aparece em praticamente todos os projetos Python.

---

# Exemplo em automação

Imagine uma lista de arquivos encontrados.

```python
arquivos = [

    "config.php",

    "passwd",

    "backup.zip",

    "id_rsa"

]
```

Queremos visualizar primeiro os menores nomes.

```python
print(

    sorted(

        arquivos,

        key=len

    )

)
```

Resultado.

```python
[
'id_rsa',
'passwd',
'config.php',
'backup.zip'
]
```

---

# Exemplo em Pentest

Imagine que um scanner encontrou estas portas.

```python
portas = [

    "8080",

    "22",

    "443",

    "80"

]
```

Ordenando normalmente.

```python
sorted(portas)
```

Resultado.

```python
[
'22',
'443',
'80',
'8080'
]
```

Observe.

Está ordenando como texto.

Na próxima parte veremos como resolver isso utilizando `lambda`.

---

# Boas práticas

✅ Utilize.

```python
key=len
```

Quando quiser ordenar pelo tamanho.

---

✅ Utilize.

```python
key=str.lower
```

Quando trabalhar com nomes.

---

✅ Evite criar funções enormes para utilizar em `key`.

O ideal é que retornem apenas o valor utilizado na comparação.

