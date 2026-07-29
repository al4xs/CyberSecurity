
---
# Funções em Shell Script (Bash)

## O que é?

Uma função é um bloco de comandos que recebe um nome e pode ser executado sempre que esse nome for chamado.

As funções ajudam a:

- Evitar repetição de código.
- Organizar scripts grandes.
- Separar responsabilidades.
- Facilitar manutenção e testes.

---

## Sintaxe

Existem duas formas comuns de declarar uma função no Bash.

### Forma recomendada

```bash
nome_da_funcao() {
    comandos
}
```

### Forma com `function`

```bash
function nome_da_funcao {
    comandos
}
```

A primeira forma costuma ser preferida por ser mais simples e compatível.

---

# Exemplo básico

```bash
#!/bin/bash

mostrar_mensagem() {
    echo "Olá! Essa mensagem veio de uma função."
}

mostrar_mensagem
```

## Saída

```text
Olá! Essa mensagem veio de uma função.
```

## Observação

Criar a função não executa seus comandos.

Ela só é executada quando é chamada:

```bash
mostrar_mensagem
```

---

# Função com parâmetros

As funções recebem parâmetros da mesma forma que um script.

Dentro da função:

| Variável | Descrição |
|---|---|
| `$1` | Primeiro parâmetro |
| `$2` | Segundo parâmetro |
| `$3` | Terceiro parâmetro |
| `$#` | Quantidade de parâmetros |
| `$@` | Todos os parâmetros |

## Exemplo

```bash
#!/bin/bash

saudar() {
    echo "Olá, $1!"
}

saudar "Allan"
saudar "Maria"
```

## Saída

```text
Olá, Allan!
Olá, Maria!
```

---

# Função com vários parâmetros

```bash
#!/bin/bash

mostrar_usuario() {
    echo "Nome: $1"
    echo "Idade: $2"
}

mostrar_usuario "Allan" 22
```

## Saída

```text
Nome: Allan
Idade: 22
```

---

# Validando parâmetros

```bash
#!/bin/bash

somar() {
    if (( $# != 2 )); then
        echo "Uso: somar <numero1> <numero2>"
        return 1
    fi

    echo $(( $1 + $2 ))
}

somar 10 20
```

## Saída

```text
30
```

Dentro de expressões aritméticas, também é possível escrever:

```bash
echo $(( 1 + 2 ))
```

Mas nesse caso os números seriam literais. Para os parâmetros da função, usamos `$1` e `$2`:

```bash
echo $(( $1 + $2 ))
```

Também funciona sem o `$` dentro de `(( ))` e `$(( ))`:

```bash
echo $(( 1 + 2 ))
```

Para evitar confusão, com parâmetros posicionais é comum manter:

```bash
echo $(( $1 + $2 ))
```

---

# Variáveis locais

Por padrão, uma variável criada dentro de uma função pode continuar existindo fora dela.

Para limitar a variável à função, utilize `local`.

## Exemplo

```bash
#!/bin/bash

criar_nome() {
    local nome="Allan"

    echo "Dentro da função: $nome"
}

criar_nome

echo "Fora da função: $nome"
```

## Saída

```text
Dentro da função: Allan
Fora da função:
```

## Boa prática

Sempre que uma variável for utilizada somente dentro de uma função, prefira:

```bash
local variavel="valor"
```

---

# Retorno de uma função

No Bash, `return` retorna um **código de saída**, não um texto ou número comum.

## Códigos de saída

| Código | Significado |
|---|---|
| `0` | Sucesso |
| `1` a `255` | Erro ou condição específica |

## Exemplo

```bash
#!/bin/bash

arquivo_existe() {
    if [[ -f $1 ]]; then
        return 0
    fi

    return 1
}

if arquivo_existe "arquivo.txt"; then
    echo "O arquivo existe."
else
    echo "O arquivo não existe."
fi
```

---

# Capturando o código de retorno

O código de retorno da última função ou comando fica armazenado em `$?`.

```bash
#!/bin/bash

verificar_numero() {
    if (( $1 > 10 )); then
        return 0
    fi

    return 1
}

verificar_numero 20

echo "Código de retorno: $?"
```

## Saída

```text
Código de retorno: 0
```

---

# Retornando um valor com `echo`

Quando você precisa obter um texto ou resultado calculado, pode usar `echo` e substituição de comando.

```bash
#!/bin/bash

somar() {
    local resultado=$(( $1 + $2 ))

    echo "$resultado"
}

valor=$(somar 10 20)

echo "Resultado: $valor"
```

## Saída

```text
Resultado: 30
```

## Diferença importante

```bash
return 0
```

Retorna um código de status.

```bash
echo "resultado"
```

Produz uma saída que pode ser capturada.

---

# Função utilizando argumentos do script

Os parâmetros da função são separados dos parâmetros do script.

```bash
#!/bin/bash

mostrar_parametro() {
    echo "Parâmetro recebido pela função: $1"
}

echo "Parâmetro recebido pelo script: $1"

mostrar_parametro "Linux"
```

## Execução

```bash
./script.sh Bash
```

## Saída

```text
Parâmetro recebido pelo script: Bash
Parâmetro recebido pela função: Linux
```

---

# Função com loop

```bash
#!/bin/bash

mostrar_argumentos() {
    for argumento in "$@"; do
        echo "Argumento: $argumento"
    done
}

mostrar_argumentos "Linux" "Bash" "Pentest"
```

## Saída

```text
Argumento: Linux
Argumento: Bash
Argumento: Pentest
```

---

# Função com `case`

```bash
#!/bin/bash

executar_acao() {
    case $1 in
        iniciar)
            echo "Iniciando serviço..."
            ;;
        parar)
            echo "Parando serviço..."
            ;;
        reiniciar)
            echo "Reiniciando serviço..."
            ;;
        *)
            echo "Ação inválida."
            return 1
            ;;
    esac
}

executar_acao "$1"
```

## Execução

```bash
./script.sh iniciar
```

## Saída

```text
Iniciando serviço...
```

---

# Ordem de declaração

Uma função deve ser declarada antes de ser chamada.

## Incorreto

```bash
mostrar_mensagem

mostrar_mensagem() {
    echo "Olá"
}
```

## Correto

```bash
mostrar_mensagem() {
    echo "Olá"
}

mostrar_mensagem
```

---

# Exemplo prático completo

```bash
#!/bin/bash

mostrar_uso() {
    echo "Uso: $0 <nome> <idade>"
}

validar_parametros() {
    if (( $# != 2 )); then
        echo "Erro: informe nome e idade."
        return 1
    fi
}

mostrar_dados() {
    local nome=$1
    local idade=$2

    echo "Nome: $nome"
    echo "Idade: $idade"

    if (( idade >= 18 )); then
        echo "Maior de idade."
    else
        echo "Menor de idade."
    fi
}

main() {
    if ! validar_parametros "$@"; then
        mostrar_uso
        return 1
    fi

    mostrar_dados "$1" "$2"
}

main "$@"
```

## Execução

```bash
./script.sh Allan 22
```

## Saída

```text
Nome: Allan
Idade: 22
Maior de idade.
```

---

# Função `main`

O Bash não exige uma função chamada `main`.

Mesmo assim, ela pode ser utilizada para organizar o fluxo principal do script.

```bash
main() {
    echo "Programa iniciado."
}

main
```

Em scripts maiores, é comum declarar as funções primeiro e chamar `main "$@"` no final.

---

# `return` versus `exit`

| Comando | Comportamento |
|---|---|
| `return` | Encerra a função |
| `exit` | Encerra o script inteiro |

## Exemplo com `return`

```bash
minha_funcao() {
    echo "Antes"
    return 1
    echo "Depois"
}

minha_funcao

echo "O script continua."
```

## Saída

```text
Antes
O script continua.
```

## Exemplo com `exit`

```bash
minha_funcao() {
    echo "Antes"
    exit 1
}

minha_funcao

echo "Essa linha não será executada."
```

---

# Quando utilizar?

Utilize funções quando:

- Um trecho de código aparece várias vezes.
- O script possui várias responsabilidades.
- Deseja separar validação, processamento e saída.
- Deseja deixar o código mais legível.
- Deseja reaproveitar comandos em diferentes partes do script.

---

# Boas práticas

- Utilize nomes descritivos.
- Prefira nomes com letras minúsculas e `_`.
- Declare as funções antes de chamá-las.
- Utilize `local` para variáveis internas.
- Utilize `return` para códigos de sucesso ou erro.
- Utilize `echo` ou `printf` para produzir valores.
- Coloque aspas em parâmetros: `"$1"` e `"$@"`.
- Evite funções muito grandes.

## Exemplo de nome ruim

```bash
f() {
    ...
}
```

## Exemplo de nome melhor

```bash
validar_endereco_ip() {
    ...
}
```

---

# Resumo

## Declaração

```bash
nome_da_funcao() {
    comandos
}
```

## Chamada

```bash
nome_da_funcao
```

## Parâmetros

```bash
nome_da_funcao "valor1" "valor2"
```

Dentro da função:

```bash
$1
$2
$#
"$@"
```

## Variável local

```bash
local nome="Allan"
```

## Código de retorno

```bash
return 0
```

## Capturar saída

```bash
resultado=$(nome_da_funcao)
```