
---
# Arrays em Bash

## O que é?

Um **array** (vetor) é uma variável capaz de armazenar **vários valores**.

Ao invés de criar várias variáveis:

```bash
nome1="Ana"
nome2="Carlos"
nome3="Maria"
```

Podemos criar apenas uma:

```bash
nomes=("Ana" "Carlos" "Maria")
```

Cada valor recebe automaticamente um índice.

| Índice | Valor |
|---------|--------|
| 0 | Ana |
| 1 | Carlos |
| 2 | Maria |

> **No Bash, os índices começam em 0.**

---

# Criando um array

## Array de strings

```bash
nomes=("Ana" "Carlos" "Maria")
```

---

## Array de números

```bash
numeros=(10 20 30 40)
```

---

## Array vazio

```bash
nomes=()
```

---

# Acessando elementos

Para acessar um elemento utilizamos seu índice.

```bash
echo "${nomes[0]}"
```

Saída

```text
Ana
```

Outro exemplo

```bash
echo "${nomes[2]}"
```

Saída

```text
Maria
```

---

# Exibindo todos os elementos

```bash
echo "${nomes[@]}"
```

Saída

```text
Ana Carlos Maria
```

Também existe:

```bash
echo "${nomes[*]}"
```

Na maioria dos casos ambos produzem resultados semelhantes.

---

# Quantidade de elementos

```bash
echo "${#nomes[@]}"
```

Saída

```text
3
```

O símbolo `#` representa a quantidade de elementos do array.

---

# Tamanho de um elemento

```bash
echo "${#nomes[0]}"
```

Saída

```text
3
```

Porque `"Ana"` possui três caracteres.

---

# Alterando um elemento

```bash
nomes[1]="Pedro"
```

Agora:

```text
Ana
Pedro
Maria
```

---

# Adicionando elementos

```bash
nomes+=("João")
```

Resultado

```text
Ana
Carlos
Maria
João
```

Também é possível adicionar em uma posição específica.

```bash
nomes[4]="Lucas"
```

---

# Removendo elementos

```bash
unset nomes[1]
```

O índice 1 será removido.

---

# Percorrendo um array

## Percorrendo os valores

```bash
nomes=("Ana" "Carlos" "Maria")

for nome in "${nomes[@]}"
do
    echo "$nome"
done
```

Saída

```text
Ana
Carlos
Maria
```

---

## Percorrendo pelos índices

```bash
for indice in "${!nomes[@]}"
do
    echo "$indice -> ${nomes[$indice]}"
done
```

Saída

```text
0 -> Ana
1 -> Carlos
2 -> Maria
```

O operador:

```bash
${!nomes[@]}
```

retorna todos os índices existentes.

---

# Arrays e RANDOM

Uma utilização bastante comum é escolher um elemento aleatoriamente.

```bash
frutas=("Maçã" "Banana" "Laranja" "Uva")

indice=$(( RANDOM % ${#frutas[@]} ))

echo "${frutas[$indice]}"
```

### Como funciona?

Primeiro:

```bash
${#frutas[@]}
```

Retorna a quantidade de elementos.

```text
4
```

Depois:

```bash
RANDOM % 4
```

Gera um número entre:

```text
0 e 3
```

Esse número é utilizado como índice.

Exemplo

```text
Índice: 2

↓

Laranja
```

---

# Arrays e Funções

Arrays podem ser passados para funções.

```bash
mostrar() {
    for nome in "$@"
    do
        echo "$nome"
    done
}

nomes=("Ana" "Carlos" "Maria")

mostrar "${nomes[@]}"
```

---

# Boas práticas

Utilize sempre aspas quando acessar arrays.

### Correto

```bash
"${nomes[@]}"
```

```bash
"${nomes[0]}"
```

Evita problemas caso algum elemento contenha espaços.

---

# Erros comuns

## Esquecer as chaves

### Incorreto

```bash
echo "$nomes[0]"
```

### Correto

```bash
echo "${nomes[0]}"
```

---

## Esquecer as aspas

### Incorreto

```bash
for nome in ${nomes[@]}
```

### Correto

```bash
for nome in "${nomes[@]}"
```

---

# Resumo

## Criar um array

```bash
nomes=("Ana" "Carlos" "Maria")
```

---

## Acessar um elemento

```bash
echo "${nomes[0]}"
```

---

## Mostrar todos

```bash
echo "${nomes[@]}"
```

---

## Quantidade de elementos

```bash
echo "${#nomes[@]}"
```

---

## Índices do array

```bash
echo "${!nomes[@]}"
```

---

## Adicionar elemento

```bash
nomes+=("João")
```

---

## Alterar elemento

```bash
nomes[1]="Pedro"
```

---

## Remover elemento

```bash
unset nomes[1]
```

---

## Percorrer um array

```bash
for nome in "${nomes[@]}"
do
    echo "$nome"
done
```

---

## Escolher elemento aleatório

```bash
indice=$(( RANDOM % ${#nomes[@]} ))

echo "${nomes[$indice]}"
```

---

# Tabela de referência

| Sintaxe | Função |
|---------|--------|
| `array=("A" "B")` | Criar um array |
| `${array[0]}` | Primeiro elemento |
| `${array[@]}` | Todos os elementos |
| `${array[*]}` | Todos os elementos |
| `${#array[@]}` | Quantidade de elementos |
| `${#array[0]}` | Tamanho de um elemento |
| `${!array[@]}` | Índices do array |
| `array+=("C")` | Adicionar elemento |
| `array[1]="Novo"` | Alterar elemento |
| `unset array[1]` | Remover elemento |