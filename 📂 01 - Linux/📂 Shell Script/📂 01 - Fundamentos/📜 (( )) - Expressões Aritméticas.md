# `(( ))` - Expressões Aritméticas

## O que é?

Os parênteses duplos (`(( ))`) são utilizados para realizar operações aritméticas no Bash.

Eles tornam operações matemáticas mais simples e legíveis, sem a necessidade de utilizar comandos externos como `expr`.

## Sintaxe

```bash
(( expressão ))
```

---

# Exemplos

## Soma

```bash
idade=20

(( idade = idade + 1 ))

echo "$idade"
```

### Saída

```text
21
```

---

## Incremento

```bash
contador=0

(( contador++ ))

echo "$contador"
```

### Saída

```text
1
```

---

## Decremento

```bash
contador=10

(( contador-- ))

echo "$contador"
```

### Saída

```text
9
```

---

## Multiplicação

```bash
a=5
b=10

(( resultado = a * b ))

echo "$resultado"
```

### Saída

```text
50
```

---

## Divisão

```bash
(( resultado = 20 / 5 ))

echo "$resultado"
```

### Saída

```text
4
```

---

# Utilizando em condições

```bash
idade=18

if (( idade >= 18 )); then
    echo "Maior de idade"
fi
```

---

# Operadores

| Operador | Descrição |
|----------|-----------|
| `+` | Soma |
| `-` | Subtração |
| `*` | Multiplicação |
| `/` | Divisão |
| `%` | Resto da divisão |
| `++` | Incremento |
| `--` | Decremento |
| `+=` | Soma e atribui |
| `-=` | Subtrai e atribui |

---

# Diferença entre `[ ]`, `[[ ]]` e `(( ))`

| Sintaxe | Utilização |
|----------|------------|
| `[ ]` | Testes POSIX |
| `[[ ]]` | Testes avançados (strings, regex, operadores lógicos) |
| `(( ))` | Operações matemáticas |

## Exemplo

```bash
nome="Allan"
idade=20

if [[ $nome == "Allan" ]]; then
    echo "Nome válido"
fi

if (( idade >= 18 )); then
    echo "Maior de idade"
fi
```

## Observação

Sempre que o objetivo for realizar cálculos ou comparações numéricas em scripts Bash, prefira utilizar `(( ))`, pois a sintaxe é mais simples e legível.