
---
# Comando `break`

## O que é?

O comando `break` interrompe imediatamente a execução de um loop.

Após o `break`, o Bash continua a execução no primeiro comando depois do loop.

Pode ser utilizado em:

- `for`
- `while`
- `until`
- `select`

## Sintaxe

```bash
break
```

## Exemplo com `for`

```bash
#!/bin/bash

for numero in {1..10}; do
    if (( numero == 5 )); then
        break
    fi

    echo "$numero"
done

echo "Loop encerrado."
```

## Saída

```text
1
2
3
4
Loop encerrado.
```

## Exemplo com `while`

```bash
#!/bin/bash

while true; do
    read -p "Digite 'sair' para encerrar: " resposta

    if [[ $resposta == "sair" ]]; then
        break
    fi

    echo "Você digitou: $resposta"
done

echo "Programa encerrado."
```

## `break` em loops aninhados

Por padrão, o `break` encerra apenas o loop atual.

```bash
for i in {1..3}; do
    for j in {1..3}; do
        if (( j == 2 )); then
            break
        fi

        echo "i=$i j=$j"
    done
done
```

## Saída

```text
i=1 j=1
i=2 j=1
i=3 j=1
```

## Interrompendo mais de um loop

É possível informar quantos níveis devem ser encerrados:

```bash
break 2
```

## Exemplo

```bash
for i in {1..3}; do
    for j in {1..3}; do
        if (( i == 2 && j == 2 )); then
            break 2
        fi

        echo "i=$i j=$j"
    done
done

echo "Todos os loops foram encerrados."
```

## Quando utilizar?

Utilize o `break` quando:

- Uma condição de saída for encontrada.
- O usuário solicitar o encerramento.
- Um resultado desejado for localizado.
- Ocorrer uma situação que torne desnecessário continuar o loop.

## Observações

- `break` não encerra o script inteiro.
- Ele encerra somente o loop.
- Para encerrar o script, utilize `exit`.
- `break 2` encerra dois níveis de loops aninhados.

## Diferença entre `break` e `exit`

| Comando | Comportamento    |
| ------- | ---------------- |
| `break` | Encerra o loop   |
| `exit`  | Encerra o script |