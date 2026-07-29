
---
# Operadores de Comparação - Bash

## O que são?

Os operadores de comparação são utilizados para comparar valores em condições (`if`, `while`, `until`, etc.).

No Bash, existem operadores diferentes para:

- Comparações numéricas.

- Comparações de strings.

- Comparações de arquivos.

---

# Operadores Numéricos

| Operador | Significado |

|----------|-------------|

| `-eq` | Igual (`==`) |

| `-ne` | Diferente (`!=`) |

| `-gt` | Maior que (`>`) |

| `-ge` | Maior ou igual (`>=`) |

| `-lt` | Menor que (`<`) |

| `-le` | Menor ou igual (`<=`) |

## Exemplo

```bash

idade=20

if [[ $idade -ge 18 ]]; then

echo "Maior de idade"

fi

```

---

# Operadores de Strings

| Operador | Significado |

|----------|-------------|

| `==` | Igual |

| `!=` | Diferente |

| `<` | Menor (ordem alfabética) |

| `>` | Maior (ordem alfabética) |

| `=~` | Expressão regular (Regex) |

## Exemplo

```bash

nome="Allan"

if [[ $nome == "Allan" ]]; then

echo "Nome correto"

fi

```

---

## Regex

```bash

email="teste@gmail.com"

if [[ $email =~ @gmail\.com$ ]]; then

echo "Gmail"

fi

```

---

# Operadores de Arquivos

| Operador | Descrição |

|----------|-----------|

| `-e` | Arquivo existe |

| `-f` | Arquivo comum |

| `-d` | Diretório |

| `-r` | Permissão de leitura |

| `-w` | Permissão de escrita |

| `-x` | Permissão de execução |

| `-s` | Arquivo não está vazio |

| `-L` | Link simbólico |

## Exemplo

```bash

if [[ -f arquivo.txt ]]; then

echo "Arquivo encontrado"

fi

```

---

# Operadores para Strings Vazias

| Operador | Descrição |

|----------|-----------|

| `-z` | String vazia |

| `-n` | String não vazia |

## Exemplo

```bash

if [[ -z $nome ]]; then

echo "Nome vazio"

fi

```

---

# Operadores Lógicos

| Operador | Significado |

|----------|-------------|

| `&&` | AND (E) |

| `||` | OR (OU) |

| `!` | NOT (NÃO) |

## Exemplo

```bash

idade=20

if [[ $idade -ge 18 && $idade -lt 60 ]]; then

echo "Adulto"

fi

```

---

# Comparação entre Bash e outras linguagens

| Bash | C / Python / Java |

|------|--------------------|

| `-eq` | `==` |

| `-ne` | `!=` |

| `-gt` | `>` |

| `-ge` | `>=` |

| `-lt` | `<` |

| `-le` | `<=` |

---

# Resumo

## Números

```bash

-eq

-ne

-gt

-ge

-lt

-le

```

## Strings

```bash

==

!=

<

>

=~

```

## Arquivos

```bash

-f

-d

-e

-r

-w

-x

```

## Lógicos

```bash

&&

||

!

```