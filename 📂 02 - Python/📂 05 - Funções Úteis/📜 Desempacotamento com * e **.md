O operador `*` pode ser utilizado no Python para **desempacotar elementos de um iterável**.

Isso significa pegar os elementos que estão dentro de uma estrutura e passá-los separadamente.

---

# Exemplo básico

```python
numeros = [
    10,
    20,
    30
]

print(numeros)
```

Saída:

```text
[10, 20, 30]
```

Agora utilizando:

```python
print(*numeros)
```

Saída:

```text
10 20 30
```

---

# O que aconteceu?

Sem:

```python
print(numeros)
```

O `print()` recebeu:

```text
1 argumento

↓

a lista inteira
```

Com:

```python
print(*numeros)
```

O `*` desempacotou a lista.

É como se tivéssemos escrito:

```python
print(
    10,
    20,
    30
)
```

Visualmente:

```text
[10, 20, 30]

       ↓

       *

       ↓

10    20    30
```

---

# Seu exemplo

```python
host = {
    "ip": [
        "1.1.1.1",
        "2.2.2.2",
        "192.158.168"
    ],

    "portas": [
        22,
        80,
        443
    ]
}

for key, value in host.items():

    print(
        key,
        *value
    )
```

Saída:

```text
ip 1.1.1.1 2.2.2.2 192.158.168
portas 22 80 443
```

---

# Como esse código funciona?

Primeiro:

```python
host.items()
```

gera pares semelhantes a:

```python
(
    "ip",
    [
        "1.1.1.1",
        "2.2.2.2",
        "192.158.168"
    ]
)
```

e:

```python
(
    "portas",
    [
        22,
        80,
        443
    ]
)
```

---

O `for` desempacota cada par:

```python
for key, value in host.items():
```

Primeira execução:

```text
key

↓

"ip"
```

```text
value

↓

[
    "1.1.1.1",
    "2.2.2.2",
    "192.158.168"
]
```

---

Depois temos:

```python
print(
    key,
    *value
)
```

O `*value` transforma:

```python
[
    "1.1.1.1",
    "2.2.2.2",
    "192.158.168"
]
```

em argumentos separados:

```python
print(
    "ip",
    "1.1.1.1",
    "2.2.2.2",
    "192.158.168"
)
```

Por isso a saída fica:

```text
ip 1.1.1.1 2.2.2.2 192.158.168
```

---

# `*` funciona apenas com listas?

Não.

Ele funciona com vários iteráveis.

---

## Tupla

```python
dados = (
    22,
    80,
    443
)

print(*dados)
```

Resultado:

```text
22 80 443
```

---

## String

```python
texto = "SSH"

print(*texto)
```

Resultado:

```text
S S H
```

Isso acontece porque uma string é iterável caractere por caractere.

---

## Set

```python
numeros = {
    10,
    20,
    30
}

print(*numeros)
```

Funciona.

Porém, lembre-se de que conjuntos não devem ser usados quando você depende de uma ordem específica.

---

## Range

```python
print(
    *range(5)
)
```

Resultado:

```text
0 1 2 3 4
```

---

# Desempacotamento em variáveis

O `*` também pode capturar vários elementos.

Exemplo:

```python
numeros = [
    10,
    20,
    30,
    40
]

primeiro, *restante = numeros
```

Agora:

```python
print(primeiro)
```

Resultado:

```text
10
```

E:

```python
print(restante)
```

Resultado:

```python
[
    20,
    30,
    40
]
```

---

# Outro exemplo

```python
primeiro, *meio, ultimo = [
    10,
    20,
    30,
    40,
    50
]
```

Resultado:

```python
print(primeiro)
```

```text
10
```

```python
print(meio)
```

```python
[
    20,
    30,
    40
]
```

```python
print(ultimo)
```

```text
50
```

---

# Juntando listas com `*`

Também podemos desempacotar listas dentro de outra lista.

```python
lista1 = [
    1,
    2,
    3
]

lista2 = [
    4,
    5,
    6
]
```

Podemos fazer:

```python
resultado = [
    *lista1,
    *lista2
]
```

Resultado:

```python
[
    1,
    2,
    3,
    4,
    5,
    6
]
```

---

# Exemplo em Cyber Security

Imagine duas coleções de portas:

```python
portas_comuns = [
    22,
    80,
    443
]

portas_extras = [
    8080,
    8443
]
```

Podemos montar:

```python
portas = [
    *portas_comuns,
    *portas_extras
]
```

Resultado:

```python
[
    22,
    80,
    443,
    8080,
    8443
]
```

---

# `*` em chamadas de função

Imagine:

```python
def conectar(host, porta):

    print(
        host,
        porta
    )
```

Temos:

```python
destino = (
    "10.10.10.10",
    22
)
```

Sem desempacotamento, isto não funciona como desejado:

```python
conectar(destino)
```

Porque a função espera:

```text
2 argumentos
```

Mas recebeu:

```text
1 tupla
```

---

Podemos fazer:

```python
conectar(*destino)
```

Isso equivale a:

```python
conectar(
    "10.10.10.10",
    22
)
```

Resultado:

```text
10.10.10.10 22
```

---

# Forma mental

Quando `*` aparece na chamada:

```python
funcao(*dados)
```

Pense:

```text
Abra esse iterável

↓

Passe seus elementos separadamente
```

---

# O `**`

Existe também:

```python
**
```

Ele é utilizado principalmente para desempacotar dicionários.

Exemplo:

```python
dados = {
    "host": "10.10.10.10",
    "porta": 22
}
```

Função:

```python
def conectar(host, porta):

    print(
        host,
        porta
    )
```

Podemos fazer:

```python
conectar(**dados)
```

Isso equivale a:

```python
conectar(
    host="10.10.10.10",
    porta=22
)
```

---

# Diferença entre `*` e `**`

```text
*

↓

Desempacota valores posicionais
```

Exemplo:

```python
funcao(*lista)
```

---

```text
**

↓

Desempacota pares chave=valor
```

Exemplo:

```python
funcao(**dicionario)
```

---

# Exemplo

```python
def servico(host, porta, protocolo):

    print(
        host,
        porta,
        protocolo
    )
```

Dicionário:

```python
dados = {
    "host": "10.10.10.10",
    "porta": 443,
    "protocolo": "HTTPS"
}
```

Chamando:

```python
servico(**dados)
```

Resultado:

```text
10.10.10.10 443 HTTPS
```

---

# Importante sobre `**`

Os nomes das chaves precisam combinar com os nomes dos parâmetros da função.

Temos:

```python
def conectar(host, porta):
```

Então isto funciona:

```python
{
    "host": "...",
    "porta": 22
}
```

Mas isto:

```python
{
    "ip": "...",
    "port": 22
}
```

não corresponde aos parâmetros:

```text
host

porta
```

e pode gerar `TypeError`.

---

# Forma mais usada no dia a dia

## Exibir uma lista sem os colchetes

```python
print(*lista)
```

---

## Passar uma lista ou tupla como argumentos

```python
funcao(*dados)
```

---

## Capturar o restante dos valores

```python
primeiro, *resto = lista
```

---

## Juntar iteráveis

```python
resultado = [
    *lista1,
    *lista2
]
```

---

## Passar um dicionário como argumentos nomeados

```python
funcao(**dados)
```

---

# Resumo

| Sintaxe             | Função                             |
| ------------------- | ---------------------------------- |
| `print(*lista)`     | Passa os elementos separadamente   |
| `funcao(*dados)`    | Desempacota argumentos posicionais |
| `a, *resto = lista` | Captura vários elementos           |
| `[*a, *b]`          | Une iteráveis em uma nova lista    |
| `funcao(**dict)`    | Desempacota argumentos nomeados    |