
---
# Comando `continue`

## O que é?

O comando `continue` interrompe apenas a iteração atual do loop.

Depois disso, o Bash avança para a próxima repetição.

Pode ser utilizado em:

- `for`
- `while`
- `until`
- `select`

## Sintaxe

```bash
continue
```

## Exemplo com `for`

```bash
#!/bin/bash

for numero in {1..5}; do
    if (( numero == 3 )); then
        continue
    fi

    echo "$numero"
done
```

## Saída

```text
1
2
4
5
```

O número `3` não foi exibido porque aquela iteração foi ignorada.

## Exemplo com números pares

```bash
#!/bin/bash

for numero in {1..10}; do
    if (( numero % 2 != 0 )); then
        continue
    fi

    echo "$numero"
done
```

## Saída

```text
2
4
6
8
10
```

## Exemplo com arquivos

```bash
#!/bin/bash

for arquivo in *; do
    if [[ ! -f $arquivo ]]; then
        continue
    fi

    echo "Arquivo: $arquivo"
done
```

Nesse exemplo, diretórios e outros tipos de entrada são ignorados.

## Exemplo com `while`

```bash
#!/bin/bash

contador=0

while (( contador < 5 )); do
    ((contador++))

    if (( contador == 3 )); then
        continue
    fi

    echo "$contador"
done
```

## Saída

```text
1
2
4
5
```

## Cuidado com loops infinitos

A variável deve ser atualizada antes do `continue`.

### Incorreto

```bash
contador=0

while (( contador < 5 )); do
    if (( contador == 3 )); then
        continue
    fi

    ((contador++))
done
```

Quando `contador` chegar a `3`, ele nunca será incrementado novamente.

### Correto

```bash
contador=0

while (( contador < 5 )); do
    ((contador++))

    if (( contador == 3 )); then
        continue
    fi

    echo "$contador"
done
```

## Quando utilizar?

Utilize o `continue` quando:

- Desejar ignorar determinados valores.
- Desejar pular arquivos inválidos.
- Desejar ignorar entradas vazias.
- Desejar continuar o processamento sem encerrar o loop.

## Observações

- `continue` não encerra o loop.
- Ele ignora somente a iteração atual.
- Em loops `while`, tenha cuidado com a atualização do contador.
- Também é possível utilizar `continue 2` em loops aninhados.

## Diferença entre `break` e `continue`

| Comando | Comportamento |
|---|---|
| `break` | Encerra completamente o loop |
| `continue` | Pula apenas a iteração atual |