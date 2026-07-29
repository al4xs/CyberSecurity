
---

# Loop `for`

## O que é?

O loop `for` é uma estrutura de repetição utilizada para percorrer uma lista de valores ou executar um bloco de código um número determinado de vezes.

É o loop mais utilizado em Shell Script.

---

# Sintaxe

```bash
for variavel in lista; do
    comandos
done
```

---

# Percorrendo uma lista

```bash
for nome in Allan João Maria; do
    echo "$nome"
done
```

### Saída

```text
Allan
João
Maria
```

---

# Intervalo Numérico

```bash
for i in {1..5}; do
    echo "$i"
done
```

### Saída

```text
1
2
3
4
5
```

---

# Incremento personalizado

```bash
for i in {0..10..2}; do
    echo "$i"
done
```

### Saída

```text
0
2
4
6
8
10
```

---

# Estilo C

O Bash também suporta a sintaxe semelhante à linguagem C.

```bash
for (( i=0; i<5; i++ )); do
    echo "$i"
done
```

### Saída

```text
0
1
2
3
4
```

---

# Percorrendo arquivos

```bash
for arquivo in *.txt; do
    echo "$arquivo"
done
```

---

# Percorrendo parâmetros do script

```bash
for parametro in "$@"; do
    echo "$parametro"
done
```

### Execução

```bash
./script.sh Allan Linux Bash
```

### Saída

```text
Allan
Linux
Bash
```

---

# Utilizando `break`

Interrompe imediatamente o loop.

```bash
for i in {1..10}; do

    if (( i == 5 )); then
        break
    fi

    echo "$i"

done
```

### Saída

```text
1
2
3
4
```

---

# Utilizando `continue`

Ignora apenas a iteração atual.

```bash
for i in {1..5}; do

    if (( i == 3 )); then
        continue
    fi

    echo "$i"

done
```

### Saída

```text
1
2
4
5
```

---

# Quando utilizar?

Utilize o `for` quando:

- Percorrer listas.
- Percorrer arquivos.
- Percorrer diretórios.
- Percorrer parâmetros (`$@`).
- Executar uma quantidade conhecida de repetições.

---

# Observações

- O `for` é o loop mais utilizado em Shell Script.
- Pode utilizar intervalos (`{1..10}`).
- Pode utilizar a sintaxe da linguagem C (`for (( ))`).
- Pode percorrer listas e arquivos facilmente.

---

# Resumo

## Lista

```bash
for item in lista; do
done
```

## Intervalo

```bash
for i in {1..10}; do
done
```

## Estilo C

```bash
for (( i=0; i<10; i++ )); do
done
```

## Parâmetros

```bash
for parametro in "$@"; do
done
```