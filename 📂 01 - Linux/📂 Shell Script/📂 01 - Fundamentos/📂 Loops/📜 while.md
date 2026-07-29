
---
# Loop `while`

## O que é?

O loop `while` executa um bloco de comandos enquanto uma condição for verdadeira.

É útil quando não sabemos exatamente quantas vezes o loop será executado.

## Sintaxe

```bash
while condição; do
    comandos
done
```

## Exemplo

```bash
#!/bin/bash

contador=1

while (( contador <= 5 )); do
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

## Utilizando `[[ ]]`

```bash
#!/bin/bash

resposta=""

while [[ $resposta != "sair" ]]; do
    read -p "Digite algo ou 'sair': " resposta
    echo "Você digitou: $resposta"
done
```

## Loop infinito

```bash
while true; do
    echo "Executando..."
done
```

Esse loop continuará executando até ser interrompido.

No terminal, pode ser interrompido com:

```text
Ctrl + C
```

## Loop infinito com `break`

```bash
#!/bin/bash

while true; do
    read -p "Digite 'sair' para encerrar: " resposta

    if [[ $resposta == "sair" ]]; then
        break
    fi

    echo "Continuando..."
done
```

## Lendo um arquivo linha por linha

```bash
while read -r linha; do
    echo "$linha"
done < arquivo.txt
```

### Forma recomendada

```bash
while IFS= read -r linha; do
    echo "$linha"
done < arquivo.txt
```

O `IFS=` evita a remoção de espaços no início e no final da linha.

O parâmetro `-r` impede que a barra invertida seja interpretada como caractere especial.

## Quando utilizar?

Utilize o `while` quando:

- A quantidade de repetições não é conhecida.
- O loop depende de uma condição.
- É necessário ler um arquivo linha por linha.
- É necessário esperar uma entrada do usuário.
- É necessário criar um menu interativo.

## Observações

- A condição é verificada antes de cada repetição.
- Se a condição começar falsa, o bloco não será executado.
- É necessário alterar a variável da condição para evitar loops infinitos.

## Dicas

Em comparações numéricas, prefira:

```bash
while (( contador <= 10 )); do
```

Em comparações de strings, prefira:

```bash
while [[ $resposta != "sair" ]]; do
```