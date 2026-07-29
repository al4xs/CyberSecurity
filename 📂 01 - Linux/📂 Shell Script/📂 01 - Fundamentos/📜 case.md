
---
# Estrutura `case` no Bash

## O que é?

A estrutura `case` permite comparar um valor com diferentes padrões.

Ela funciona de forma parecida com o `switch` de linguagens como C, Java e JavaScript.

É bastante utilizada para:

- Criar menus.
- Interpretar parâmetros.
- Tratar opções do usuário.
- Executar comandos diferentes conforme um valor.

---

## Sintaxe

```bash
case valor in
    padrão1)
        comandos
        ;;
    padrão2)
        comandos
        ;;
    *)
        comandos
        ;;
esac
```

## Estrutura

| Elemento | Função |
|---|---|
| `case` | Inicia a estrutura |
| `valor` | Valor que será analisado |
| `padrão)` | Opção que será comparada |
| `;;` | Finaliza aquela opção |
| `*)` | Opção padrão |
| `esac` | Encerra o `case` |

> `esac` é `case` escrito ao contrário.

---

# Exemplo básico

```bash
#!/bin/bash

read -p "Digite uma opção: " opcao

case $opcao in
    1)
        echo "Você escolheu a opção 1"
        ;;
    2)
        echo "Você escolheu a opção 2"
        ;;
    3)
        echo "Você escolheu a opção 3"
        ;;
    *)
        echo "Opção inválida"
        ;;
esac
```

## Saída possível

```text
Digite uma opção: 2
Você escolheu a opção 2
```

---

# Comparação com `if`

## Utilizando `if`

```bash
if [[ $opcao == "1" ]]; then
    echo "Opção 1"
elif [[ $opcao == "2" ]]; then
    echo "Opção 2"
elif [[ $opcao == "3" ]]; then
    echo "Opção 3"
else
    echo "Opção inválida"
fi
```

## Utilizando `case`

```bash
case $opcao in
    1)
        echo "Opção 1"
        ;;
    2)
        echo "Opção 2"
        ;;
    3)
        echo "Opção 3"
        ;;
    *)
        echo "Opção inválida"
        ;;
esac
```

O `case` costuma ficar mais organizado quando existem muitas opções fixas.

---

# Múltiplos valores

É possível aceitar mais de um valor usando `|`.

```bash
#!/bin/bash

read -p "Deseja continuar? " resposta

case $resposta in
    s|S|sim|SIM)
        echo "Continuando..."
        ;;
    n|N|nao|não|NAO|NÃO)
        echo "Encerrando..."
        ;;
    *)
        echo "Resposta inválida"
        ;;
esac
```

Nesse exemplo, todas estas respostas são aceitas:

```text
s
S
sim
SIM
```

---

# Utilizando padrões

O `case` trabalha com padrões, não apenas valores exatos.

## Coringa `*`

O `*` representa qualquer quantidade de caracteres.

```bash
read -p "Digite um arquivo: " arquivo

case $arquivo in
    *.txt)
        echo "Arquivo de texto"
        ;;
    *.sh)
        echo "Shell Script"
        ;;
    *.jpg|*.png)
        echo "Arquivo de imagem"
        ;;
    *)
        echo "Tipo desconhecido"
        ;;
esac
```

---

## Coringa `?`

O `?` representa exatamente um caractere.

```bash
case $valor in
    a?)
        echo "Começa com 'a' e possui dois caracteres"
        ;;
esac
```

Valores que correspondem:

```text
ab
a1
ax
```

Valores que não correspondem:

```text
a
abc
```

---

## Intervalos

Também é possível utilizar intervalos entre colchetes.

```bash
read -p "Digite um caractere: " caractere

case $caractere in
    [0-9])
        echo "É um número"
        ;;
    [a-z])
        echo "É uma letra minúscula"
        ;;
    [A-Z])
        echo "É uma letra maiúscula"
        ;;
    *)
        echo "Outro caractere"
        ;;
esac
```

---

# Utilizando parâmetros do script

O `case` é muito usado para interpretar parâmetros.

```bash
#!/bin/bash

case $1 in
    iniciar)
        echo "Iniciando o serviço..."
        ;;
    parar)
        echo "Parando o serviço..."
        ;;
    reiniciar)
        echo "Reiniciando o serviço..."
        ;;
    *)
        echo "Uso: $0 {iniciar|parar|reiniciar}"
        exit 1
        ;;
esac
```

## Execução

```bash
./servico.sh iniciar
```

## Saída

```text
Iniciando o serviço...
```

---

# Exemplo de menu

```bash
#!/bin/bash

echo "1 - Ver data"
echo "2 - Ver usuário"
echo "3 - Ver diretório atual"
echo "4 - Sair"

read -p "Escolha uma opção: " opcao

case $opcao in
    1)
        date
        ;;
    2)
        whoami
        ;;
    3)
        pwd
        ;;
    4)
        echo "Saindo..."
        exit 0
        ;;
    *)
        echo "Opção inválida"
        ;;
esac
```

---

# Menu com loop

```bash
#!/bin/bash

while true; do
    echo
    echo "1 - Mostrar data"
    echo "2 - Mostrar usuário"
    echo "3 - Mostrar diretório"
    echo "4 - Sair"

    read -p "Escolha uma opção: " opcao

    case $opcao in
        1)
            date
            ;;
        2)
            whoami
            ;;
        3)
            pwd
            ;;
        4)
            echo "Programa encerrado."
            break
            ;;
        *)
            echo "Opção inválida."
            ;;
    esac
done
```

Nesse exemplo:

- O `while true` mantém o menu em execução.
- O `case` trata cada opção.
- O `break` encerra o loop.

---

# Finalizadores do `case`

## `;;`

Finaliza a opção atual.

```bash
1)
    echo "Opção 1"
    ;;
```

É o finalizador mais comum.

---

## `;&`

Executa também os comandos da próxima opção, sem testar o próximo padrão.

```bash
case $valor in
    1)
        echo "Primeiro bloco"
        ;&
    2)
        echo "Segundo bloco"
        ;;
esac
```

Se o valor for `1`, a saída será:

```text
Primeiro bloco
Segundo bloco
```

---

## `;;&`

Continua testando os próximos padrões.

```bash
case $valor in
    a*)
        echo "Começa com a"
        ;;&
    *z)
        echo "Termina com z"
        ;;
esac
```

Se o valor for:

```text
arroz
```

A saída será:

```text
Começa com a
Termina com z
```

> `;&` e `;;&` são recursos do Bash. Para scripts simples, normalmente usamos apenas `;;`.

---

# Quando utilizar?

Utilize `case` quando:

- Existem várias opções fixas.
- Está criando um menu.
- Está tratando parâmetros do script.
- Precisa comparar extensões de arquivos.
- Precisa trabalhar com padrões.
- Uma sequência de `if` e `elif` ficou muito grande.

---

# Quando utilizar `if`?

Prefira `if` quando precisar testar:

- Comparações numéricas complexas.
- Várias condições com `&&` e `||`.
- Existência e permissões de arquivos.
- Expressões aritméticas.
- Condições que não são apenas opções fixas.

## Exemplo

```bash
if (( idade >= 18 && idade <= 60 )); then
    echo "Faixa válida"
fi
```

---

# Erros comuns

## Esquecer o `;;`

### Incorreto

```bash
case $opcao in
    1)
        echo "Opção 1"
    2)
        echo "Opção 2"
        ;;
esac
```

### Correto

```bash
case $opcao in
    1)
        echo "Opção 1"
        ;;
    2)
        echo "Opção 2"
        ;;
esac
```

---

## Esquecer o `esac`

Todo `case` deve terminar com:

```bash
esac
```

---

## Utilizar `switch`

No Bash não existe a palavra-chave:

```bash
switch
```

O correto é:

```bash
case
```

---

# Resumo

```bash
case $valor in
    opção1)
        comandos
        ;;
    opção2|alternativa)
        comandos
        ;;
    padrão*)
        comandos
        ;;
    *)
        comandos padrão
        ;;
esac
```

## Principais símbolos

| Símbolo | Significado |
|---|---|
| `|` | Alternativa entre padrões |
| `*` | Zero ou mais caracteres |
| `?` | Exatamente um caractere |
| `[0-9]` | Um número entre 0 e 9 |
| `;;` | Finaliza a opção |
| `*)` | Caso padrão |
