O fatiamento (*Slicing*) é um recurso do Python utilizado para obter apenas uma parte de um objeto iterável.

Ele funciona com.

- String
- Lista
- Tupla
- Bytes
- Range

É muito utilizado para extrair informações, remover partes de textos e manipular dados.

---

# Sintaxe

```python
objeto[inicio:fim:passo]
```

---

# Como ler a sintaxe?

```text
objeto

↓

Objeto que será fatiado

----------------------

inicio

↓

Onde começa

----------------------

fim

↓

Onde termina

----------------------

passo

↓

Quantidade de posições puladas
```

---

# Parâmetros

## inicio

Define a posição inicial.

É opcional.

Valor padrão.

```python
0
```

Exemplo.

```python
texto = "Python"

print(texto[2:])
```

Resultado.

```text
thon
```

---

## fim

Define onde termina.

É opcional.

Importante.

O índice informado NÃO é incluído.

Exemplo.

```python
texto = "Python"

print(texto[:3])
```

Resultado.

```text
Pyt
```

Observe.

```text
3

↓

Não entra.
```

---

## passo

Define quantas posições serão puladas.

É opcional.

Valor padrão.

```python
1
```

Exemplo.

```python
texto = "Python"

print(texto[::2])
```

Resultado.

```text
Pto
```

---

Também pode ser negativo.

```python
texto[::-1]
```

---

# Valores padrão

```python
texto[:]
```

É igual.

```python
texto[0:len(texto):1]
```

---

# Índices negativos

Também podemos contar de trás para frente.

```text
Python

 P  y  t  h  o  n

 0  1  2  3  4  5

-6 -5 -4 -3 -2 -1
```

---

Último caractere.

```python
texto[-1]
```

Resultado.

```text
n
```

---

Penúltimo.

```python
texto[-2]
```

Resultado.

```text
o
```

---

# Como funciona?

Imagine.

```python
texto = "Python"
```

```python
texto[1:4]
```

O Python faz.

```text
1

↓

y

2

↓

t

3

↓

h

4

↓

Para
```

Resultado.

```text
yth
```

---

# Exemplos básicos

Primeiros caracteres.

```python
texto = "Python"

print(texto[:2])
```

Resultado.

```text
Py
```

---

Últimos caracteres.

```python
print(texto[-2:])
```

Resultado.

```text
on
```

---

Removendo o primeiro.

```python
print(texto[1:])
```

Resultado.

```text
ython
```

---

Removendo o último.

```python
print(texto[:-1])
```

Resultado.

```text
Pytho
```

---

Copiando.

```python
novo = texto[:]
```

---

Invertendo.

```python
texto[::-1]
```

Resultado.

```text
nohtyP
```

---

Pulando caracteres.

```python
texto[::2]
```

Resultado.

```text
Pto
```

---

Pulando de trás para frente.

```python
texto[::-2]
```

Resultado.

```text
nhy
```

---

# Funciona com listas

```python
numeros = [

    10,

    20,

    30,

    40,

    50

]

print(numeros[1:4])
```

Resultado.

```python
[
20,
30,
40
]
```

---

# Funciona com tuplas

```python
dados = (

    1,

    2,

    3,

    4

)

print(dados[:2])
```

Resultado.

```python
(
1,
2
)
```

---

# Funciona com bytes

```python
dados = b"\x01\x02\x03\x04"

print(dados[:2])
```

Resultado.

```python
b'\x01\x02'
```

---

# Funciona com range

```python
print(

    list(

        range(10)[2:7]

    )

)
```

Resultado.

```python
[
2,
3,
4,
5,
6
]
```

---

# Exemplos em Automação

Remover quebra de linha.

```python
linha = "admin\n"

print(linha[:-1])
```

Embora hoje seja mais recomendado.

```python
linha.strip()
```

---

Criar cópia de lista.

```python
nova_lista = lista[:]
```

---

Obter extensão.

```python
arquivo = "shell.php"

print(arquivo[-3:])
```

Resultado.

```text
php
```

---

Remover extensão.

```python
arquivo = "shell.php"

print(arquivo[:-4])
```

Resultado.

```text
shell
```

---

# Exemplos em Pentest

Criando URL.

```python
url = "http://site.com"

print(url[7:])
```

Resultado.

```text
site.com
```

---

Lendo protocolo.

```python
pacote = b"\x16\x03\x01\x00\x2e"

print(pacote[:2])
```

Resultado.

```python
b'\x16\x03'
```

Muito comum em análise de protocolos.

---

Hash.

```python
hash_md5 = "5f4dcc3b5aa765d61d8327deb882cf99"

print(hash_md5[:8])
```

Resultado.

```text
5f4dcc3b
```

---

JWT.

```python
token = "eyJhbGciOiJIUzI1NiIs..."

print(token[-10:])
```

---

Banner Grabbing.

```python
banner[:30]
```

---

Payload.

```python
payload[16:]
```

---

Magic Bytes.

```python
arquivo[:2]
```

Verificando.

```text
MZ
```

ou.

```text
PK
```

Muito utilizado em Engenharia Reversa e Malware.

---

# Performance

O slicing cria um novo objeto.

Exemplo.

```python
novo = texto[:]
```

A string original permanece intacta.

---

# Erros comuns

## Esquecer que o fim não é incluído

```python
texto[:3]
```

Resultado.

```text
Pyt
```

O índice `3` não faz parte do resultado.

---

## Utilizar índice inexistente

```python
texto[100:]
```

Não gera erro.

Resultado.

```text
''
```

---

## Esperar modificar a string

```python
texto = "Python"

texto[:2]
```

A variável continua igual.

É necessário armazenar o resultado.

```python
novo = texto[:2]
```

---

# Formas mais utilizadas

Primeiros caracteres.

```python
texto[:5]
```

---

Últimos caracteres.

```python
texto[-5:]
```

---

Remover primeiro.

```python
texto[1:]
```

---

Remover último.

```python
texto[:-1]
```

---

Copiar.

```python
texto[:]
```

---

Inverter.

```python
texto[::-1]
```

---

Pular caracteres.

```python
texto[::2]
```

---

# Boas práticas

✅ Utilize índices negativos quando precisar acessar o final.

✅ Utilize `[::-1]` para inverter sequências.

✅ Utilize `[:]` para copiar listas.

✅ Utilize `strip()` para remover espaços e quebras de linha, em vez de depender de slicing.

✅ Lembre-se de que o índice final nunca é incluído.

---

# Resumo

| Sintaxe | Resultado |
|----------|:---------:|
| `[: ]` | Copia todo o objeto |
| `[2:]` | Do índice 2 até o final |
| `[:5]` | Do início até o índice 4 |
| `[2:5]` | Índices 2, 3 e 4 |
| `[::2]` | Pula de 2 em 2 |
| `[::-1]` | Inverte a sequência |
| `[-1]` | Último elemento |
| `[:-1]` | Remove o último elemento |
| `[-3:]` | Últimos 3 elementos |