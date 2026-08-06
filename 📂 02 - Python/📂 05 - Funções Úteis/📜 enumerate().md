`enumerate()` é uma função nativa do Python utilizada para percorrer objetos iteráveis adicionando automaticamente um índice para cada elemento.

Ela elimina a necessidade de controlar manualmente um contador.

É uma das funções mais utilizadas em código Python moderno.

---

# O que é um índice?

O índice representa a posição de um elemento dentro de uma sequência.

Exemplo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]
```

Visualmente.

```text
Índice

0 → Ana

1 → Carlos

2 → Pedro
```

Normalmente acessamos um elemento assim.

```python
nomes[0]
```

Resultado.

```python
Ana
```

---

# Problema

Imagine.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for nome in nomes:

    print(nome)
```

Resultado.

```text
Ana

Carlos

Pedro
```

Conseguimos acessar o elemento.

Mas não sabemos sua posição.

---

# Solução

Utilizar.

```python
enumerate()
```

---

# Sintaxe

```python
enumerate(iterable, start=0)
```

---

# Como ler essa sintaxe?

```text
enumerate

↓

Função nativa

----------------------

iterable

↓

Objeto que será percorrido

----------------------

start

↓

Valor inicial do contador
```

---

# Parâmetros

## iterable

### O que é?

Objeto que será percorrido.

---

### É obrigatório?

✅ Sim.

---

### O que recebe?

Qualquer objeto iterável.

Exemplos.

Lista.

```python
enumerate(lista)
```

---

Tupla.

```python
enumerate(tupla)
```

---

String.

```python
enumerate("python")
```

---

Set.

```python
enumerate({1,2,3})
```

---

Dicionário.

```python
enumerate(dicionario)
```

---

Range.

```python
enumerate(range(10))
```

---

### O que NÃO recebe?

Objetos que não podem ser percorridos.

Exemplo.

```python
enumerate(100)
```

Resultado.

```text
TypeError

'int' object is not iterable
```

---

# start

## O que é?

Define o valor inicial do contador.

---

## É obrigatório?

❌ Não.

---

## Valor padrão

```python
0
```

Ou seja.

```python
enumerate(lista)
```

é exatamente igual a.

```python
enumerate(

    lista,

    start=0

)
```

---

## O que recebe?

Recebe um número inteiro.

Exemplos.

```python
start=0
```

---

```python
start=1
```

---

```python
start=100
```

---

Também aceita valores negativos.

```python
start=-5
```

---

# Valor de retorno

Uma dúvida muito comum.

`enumerate()` NÃO retorna uma lista.

Ele retorna um objeto do tipo.

```python
enumerate
```

Exemplo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

resultado = enumerate(nomes)

print(resultado)
```

Saída.

```text
<enumerate object at 0x...>
```

---

# Transformando em lista

Podemos visualizar seu conteúdo.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

print(

    list(

        enumerate(nomes)

    )

)
```

Resultado.

```python
[
    (0, 'Ana'),

    (1, 'Carlos'),

    (2, 'Pedro')
]
```

Observe.

Cada elemento virou uma tupla.

```text
(índice, valor)
```

---

# Como funciona internamente?

Imagine.

```python
enumerate(nomes)
```

O Python cria algo semelhante.

```text
(0, Ana)

↓

(1, Carlos)

↓

(2, Pedro)
```

Depois o `for` desempacota essas tuplas.

```python
for indice, nome in enumerate(nomes):

    print(indice, nome)
```

Visualmente.

```text
(0, Ana)

↓

indice = 0

nome = Ana

------------------------

(1, Carlos)

↓

indice = 1

nome = Carlos

------------------------

(2, Pedro)

↓

indice = 2

nome = Pedro
```

É por isso que conseguimos utilizar duas variáveis.

```python
indice

nome
```

---

# Exemplo básico

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for indice, nome in enumerate(nomes):

    print(indice, nome)
```

Saída.

```text
0 Ana

1 Carlos

2 Pedro
```

---

# Utilizando apenas uma variável

Também é possível.

```python
for item in enumerate(nomes):

    print(item)
```

Resultado.

```python
(0, 'Ana')

(1, 'Carlos')

(2, 'Pedro')
```

Observe.

Agora cada elemento é uma tupla.

---

# Resumo da Parte 1

| Parâmetro | Obrigatório | Valor padrão |
|-----------|:-----------:|:------------:|
| iterable | ✅ | — |
| start | ❌ | 0 |

---

| Retorno |
|---------|
| enumerate object |

---

# Utilizando o parâmetro start

Até agora utilizamos.

```python
enumerate(lista)
```

O contador iniciou em.

```text
0
```

Porque este é o valor padrão.

---

Podemos alterar esse comportamento.

Utilizando.

```python
start=
```

---

# Exemplo

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for indice, nome in enumerate(

    nomes,

    start=1

):

    print(indice, nome)
```

Saída.

```text
1 Ana

2 Carlos

3 Pedro
```

Observe.

O primeiro índice agora é.

```text
1
```

---

# Outro exemplo

```python
for indice, nome in enumerate(

    nomes,

    start=100

):

    print(indice, nome)
```

Resultado.

```text
100 Ana

101 Carlos

102 Pedro
```

---

Também funciona com números negativos.

```python
for indice, nome in enumerate(

    nomes,

    start=-3

):

    print(indice, nome)
```

Resultado.

```text
-3 Ana

-2 Carlos

-1 Pedro
```

---

# Quando utilizar start=1?

É extremamente comum.

Imagine um menu.

```python
opcoes = [

    "Cadastrar",

    "Editar",

    "Excluir",

    "Sair"

]
```

Ao usuário faz mais sentido.

```text
1 - Cadastrar

2 - Editar

3 - Excluir

4 - Sair
```

Do que.

```text
0 - Cadastrar

1 - Editar

2 - Excluir

3 - Sair
```

Por isso normalmente utilizamos.

```python
start=1
```

---

# enumerate() x range(len())

Antigamente era muito comum fazer.

```python
nomes = [

    "Ana",

    "Carlos",

    "Pedro"

]

for i in range(len(nomes)):

    print(i, nomes[i])
```

Resultado.

```text
0 Ana

1 Carlos

2 Pedro
```

Funciona.

Mas existe uma forma melhor.

---

Utilizando.

```python
for indice, nome in enumerate(nomes):

    print(indice, nome)
```

Resultado.

```text
0 Ana

1 Carlos

2 Pedro
```

---

# Qual é melhor?

Na maioria dos casos.

```python
enumerate()
```

Motivos.

- Código mais limpo.
- Mais legível.
- Não precisa acessar a lista pelo índice.
- Evita erros.

---

# Quando utilizar range(len())?

Ainda existe um caso.

Quando realmente precisamos trabalhar com índices.

Exemplo.

Trocar elementos de posição.

```python
for i in range(len(lista)):

    lista[i] *= 2
```

Nesse caso.

`range(len())` continua sendo uma boa opção.

---

# Quando utilizar enumerate()?

Quando você precisa.

- Índice.
- Valor.

Ao mesmo tempo.

---

# Exemplos reais

Lista de arquivos.

```python
arquivos = [

    "passwd",

    "shadow",

    "config.php"

]

for indice, arquivo in enumerate(

    arquivos,

    start=1

):

    print(

        f"[{indice}] {arquivo}"

    )
```

Saída.

```text
[1] passwd

[2] shadow

[3] config.php
```

---

# Exemplo em automação

Imagine um script que encontra hosts ativos.

```python
hosts = [

    "10.10.10.5",

    "10.10.10.8",

    "10.10.10.20"

]
```

Mostrando ao usuário.

```python
for indice, host in enumerate(

    hosts,

    start=1

):

    print(

        f"{indice}. {host}"

    )
```

Resultado.

```text
1. 10.10.10.5

2. 10.10.10.8

3. 10.10.10.20
```

Muito utilizado em scanners.

---

# Exemplo em Pentest

Imagine um enumerador.

```python
usuarios = [

    "Administrator",

    "Guest",

    "Backup"

]

for indice, usuario in enumerate(

    usuarios,

    start=1

):

    print(

        f"[{indice}] {usuario}"

    )
```

Saída.

```text
[1] Administrator

[2] Guest

[3] Backup
```

Esse padrão aparece bastante em ferramentas de enumeração.

---

# Como aparece em projetos Open Source

Você verá muito.

```python
for indice, item in enumerate(lista):
```

ou.

```python
for indice, linha in enumerate(arquivo):
```

Muito comum em projetos como.

- pwntools
- Impacket
- Scapy
- Scrapy
- Django
- Flask
- Requests
- Netmiko

Sempre que o programa precisa exibir ou processar elementos junto com sua posição.

---

# Erros comuns

## Erro 1

Esquecer que enumerate() retorna um objeto.

```python
resultado = enumerate(lista)

print(resultado)
```

Saída.

```text
<enumerate object at ...>
```

Para visualizar os valores.

```python
print(

    list(

        enumerate(lista)

    )

)
```

---

## Erro 2

Utilizar apenas uma variável.

```python
for item in enumerate(lista):

    print(item)
```

Resultado.

```python
(0, 'Ana')

(1, 'Carlos')
```

Lembre-se.

Cada elemento é uma tupla.

```text
(índice, valor)
```

---

## Erro 3

Utilizar range(len()) sem necessidade.

Errado.

```python
for i in range(len(lista)):

    print(lista[i])
```

Melhor.

```python
for indice, item in enumerate(lista):

    print(indice, item)
```

---

# Boas práticas

✅ Utilize `enumerate()` quando precisar do índice e do valor.

✅ Utilize `start=1` para menus exibidos ao usuário.

✅ Utilize `range(len())` apenas quando realmente precisar manipular posições da lista.

✅ Utilize nomes descritivos.

Prefira.

```python
for indice, usuario in enumerate(usuarios):
```

Ao invés de.

```python
for i, x in enumerate(lista):
```

---

# Curiosidades

- `enumerate()` existe desde o Python 2.3.
- É considerado o padrão moderno para obter índice e valor ao mesmo tempo.
- Funciona com qualquer objeto iterável.
- Não cria uma lista automaticamente, retornando um objeto `enumerate`, o que economiza memória.

---

# Resumo

| Recurso | enumerate() |
|----------|:-----------:|
| Função nativa | ✅ |
| Funciona com iteráveis | ✅ |
| Retorna objeto enumerate | ✅ |
| Permite alterar o índice inicial | ✅ |
| Substitui `range(len())` na maioria dos casos | ✅ |

---

# Formas mais utilizadas

Percorrer lista.

```python
for indice, item in enumerate(lista):

    ...
```

---

Menu.

```python
for indice, opcao in enumerate(

    opcoes,

    start=1

):

    ...
```

---

Percorrer arquivo.

```python
for numero_linha, linha in enumerate(

    arquivo,

    start=1

):

    ...
```

---

# Quando usar?

Use `enumerate()` sempre que precisar trabalhar com o elemento e sua posição ao mesmo tempo.

Ele torna o código mais limpo, mais legível e evita o uso desnecessário de `range(len())`.