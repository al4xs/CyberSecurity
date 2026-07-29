
---
# Sintaxe do Bash

## O que é?

O Bash utiliza alguns símbolos especiais para executar ações específicas.

No começo é comum confundir esses símbolos, principalmente o `$`, pois ele possui vários significados dependendo do contexto.

Esta nota serve como um guia rápido para consulta.

---

# Resumo

| Sintaxe | Função | Exemplo |
|----------|--------|----------|
| `$variavel` | Obtém o valor de uma variável | `echo "$HOME"` |
| `${variavel}` | Expansão de variável | `${nome}.txt` |
| `$(comando)` | Executa um comando e retorna sua saída | `data=$(date)` |
| `(( ))` | Executa uma operação aritmética | `((contador++))` |
| `$(( ))` | Calcula uma expressão e retorna o resultado | `echo $((10+5))` |

---

# `$variavel`

Obtém o valor de uma variável.

```bash
nome="Allan"

echo "$nome"
```

Saída

```text
Allan
```

Outro exemplo

```bash
echo "$HOME"
```

---

# `${variavel}`

Também obtém o valor da variável, mas permite combiná-la com outros textos sem ambiguidades.

Exemplo

```bash
arquivo="backup"

echo "${arquivo}.zip"
```

Saída

```text
backup.zip
```

Sem as chaves, o Bash poderia interpretar o nome da variável de forma incorreta em algumas situações.

---

# `$(comando)`

Executa um comando e utiliza sua saída.

```bash
data=$(date)

echo "$data"
```

O Bash faz:

```text
date

↓

Tue Jul 29 ...

↓

data="Tue Jul 29 ..."
```

Outro exemplo

```bash
usuario=$(whoami)
```

---

# `(( ))`

Executa uma operação aritmética.

Normalmente é usado quando queremos alterar o valor de uma variável.

```bash
contador=0

((contador++))
```

Agora:

```text
contador = 1
```

Outro exemplo

```bash
((idade += 5))
```

---

# `$(( ))`

Calcula uma expressão matemática e devolve o resultado.

```bash
echo $((10 + 5))
```

Saída

```text
15
```

Outro exemplo

```bash
numero=$(( RANDOM % 10 + 1 ))
```

O Bash faz:

1. Obtém o valor do `RANDOM`.
2. Calcula `RANDOM % 10 + 1`.
3. Armazena o resultado em `numero`.

---

# Quando usar cada um?

## Apenas ler uma variável

```bash
echo "$HOME"
```

Use:

```bash
$variavel
```

---

## Executar um comando

```bash
usuario=$(whoami)
```

Use:

```bash
$(comando)
```

---

## Fazer uma conta

```bash
echo $((5 + 3))
```

Use:

```bash
$(( ))
```

---

## Alterar uma variável

```bash
((contador++))
```

Use:

```bash
(( ))
```

---

# Como lembrar

Imagine:

```text
$variavel

↓

Quero o valor da variável.
```

```text
$( )

↓

Execute um comando.
```

```text
(( ))

↓

Calculadora do Bash.
```

```text
$(( ))

↓

Faça a conta e me devolva o resultado.
```

---

# Exemplos

```bash
nome="Allan"

echo "$nome"
```

↓

```text
Allan
```

---

```bash
hoje=$(date)
```

↓

Executa o comando `date`.

---

```bash
echo $((10 + 5))
```

↓

```text
15
```

---

```bash
((contador++))
```

↓

Incrementa a variável.

---

```bash
numero=$(( RANDOM % 6 + 1 ))
```

↓

Gera um número entre **1 e 6**.