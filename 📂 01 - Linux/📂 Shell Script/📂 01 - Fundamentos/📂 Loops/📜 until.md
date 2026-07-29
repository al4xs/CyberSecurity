
---
# Loop `until`

## O que é?

O loop `until` executa um bloco de comandos enquanto uma condição for falsa.

Quando a condição se torna verdadeira, o loop é encerrado.

Ele funciona de maneira inversa ao `while`.

## Sintaxe

```bash
until condição; do
    comandos
done
```

## Exemplo

```bash
#!/bin/bash

contador=1

until (( contador > 5 )); do
    echo "$contador"
    ((contador++))
done
```

## Saída

```text
1
2
3
4
5
```

## Comparação com `while`

Este código com `while`:

```bash
while (( contador <= 5 )); do
    echo "$contador"
    ((contador++))
done
```

É equivalente a este código com `until`:

```bash
until (( contador > 5 )); do
    echo "$contador"
    ((contador++))
done
```

## Exemplo com arquivo

```bash
#!/bin/bash

until [[ -f relatorio.txt ]]; do
    echo "Aguardando o arquivo..."
    sleep 2
done

echo "Arquivo encontrado."
```

O script verifica a cada dois segundos se o arquivo existe.

## Exemplo com conexão

```bash
until ping -c 1 8.8.8.8 > /dev/null 2>&1; do
    echo "Sem conexão..."
    sleep 3
done

echo "Conexão disponível."
```

## Quando utilizar?

Utilize o `until` quando:

- Desejar repetir até uma condição se tornar verdadeira.
- Estiver aguardando um arquivo ser criado.
- Estiver aguardando um serviço ficar disponível.
- Estiver aguardando uma conexão.
- A lógica ficar mais fácil de compreender na forma negativa.

## Observações

- A condição é verificada antes de cada repetição.
- Se a condição começar verdadeira, o bloco não será executado.
- O `until` é menos utilizado que o `while`.
- Muitas situações podem ser escritas usando qualquer um dos dois.

## Dicas

Use a estrutura que deixar a condição mais legível.

```bash
until [[ -f arquivo.txt ]]; do
```

Pode ser mais fácil de entender do que:

```bash
while [[ ! -f arquivo.txt ]]; do
```