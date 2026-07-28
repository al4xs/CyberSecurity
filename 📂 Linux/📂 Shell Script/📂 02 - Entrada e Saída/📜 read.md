
---
# Echo - Parâmetro `-e`

## O que é?

O parâmetro `-e` do comando `echo` permite interpretar caracteres especiais precedidos por uma barra invertida (`\`).

## Exemplo

```bash

echo -e "Olá\nMundo"

```

### Saída

```text

Olá

Mundo

```

## Caracteres especiais mais utilizados

| Caractere | Descrição |

| ---------- | --------- |

| `\n` | Quebra de linha |

| `\t` | Tabulação |

| `\\` | Barra invertida (`\`) |

| `\"` | Aspas duplas |

| `\r` | Retorno de carro |

## Observação

Sem o parâmetro `-e`, o `echo` imprime os caracteres literalmente.

### Exemplo

```bash

echo "Olá\nMundo"

```

### Saída

```text

Olá\nMundo

```

---

# Read - Parâmetro `-p`

## O que é?

O parâmetro `-p` do comando `read` permite exibir uma mensagem (prompt) antes de o usuário digitar uma entrada.

A mensagem é exibida na **mesma linha** em que o usuário irá inserir o valor.

## Sintaxe

```bash

read -p "Mensagem: " variavel

```

## Exemplo

```bash

read -p "Digite seu nome: " nome

```

### Saída

```text

Digite seu nome: Allan

```

A variável `nome` armazenará o valor digitado pelo usuário.

## Equivalente sem `-p`

Sem o parâmetro `-p`, é necessário usar o comando `echo` para exibir a mensagem.

```bash

echo "Digite seu nome:"

read nome

```

### Saída

```text

Digite seu nome:

Allan

```

## Observação

O parâmetro `-p` é uma opção do **Bash**.

No **Zsh**, a sintaxe é diferente:

```zsh

read "nome?Digite seu nome: "

```

Caso utilize `read -p` no Zsh, será exibido o erro:

```text

read: -p: no coprocess

```
