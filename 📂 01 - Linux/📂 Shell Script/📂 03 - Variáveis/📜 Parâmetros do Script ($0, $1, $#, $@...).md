
---
# Parâmetros em Shell Script

## O que são?

Parâmetros permitem enviar informações para um script no momento da execução.

São utilizados para tornar um script reutilizável, sem a necessidade de alterar o código.

## Sintaxe

```bash

./script.sh parametro1 parametro2 parametro3

```

---

# Parâmetros Posicionais

| Parâmetro | Descrição |

|-----------|-----------|

| `$0` | Nome do script |

| `$1` | Primeiro parâmetro |

| `$2` | Segundo parâmetro |

| `$3` | Terceiro parâmetro |

| `$4...` | Demais parâmetros |

## Exemplo

### Script

```bash

#!/bin/bash

echo "Nome do script: $0"

echo "Primeiro parâmetro: $1"

echo "Segundo parâmetro: $2"

```

### Execução

```bash

./script.sh Allan 22

```

### Saída

```text

Nome do script: ./script.sh

Primeiro parâmetro: Allan

Segundo parâmetro: 22

```

---

# Quantidade de parâmetros

O parâmetro `$#` retorna a quantidade de argumentos recebidos.

## Exemplo

```bash

#!/bin/bash

echo "Quantidade de parâmetros: $#"

```

### Execução

```bash

./script.sh a b c d

```

### Saída

```text

Quantidade de parâmetros: 4

```

---

# Todos os parâmetros

## `$@`

Representa todos os parâmetros individualmente.

```bash

#!/bin/bash

echo "$@"

```

### Execução

```bash

./script.sh Allan 22 Linux

```

### Saída

```text

Allan 22 Linux

```

---

## `$*`

Também representa todos os parâmetros.

Na maioria dos casos, prefira utilizar `$@`.

---

# Último código de saída

O parâmetro `$?` contém o código de retorno do último comando executado.

```bash

ls

echo $?

```

Se o comando foi executado com sucesso:

```text

0

```

---

# PID do script

O parâmetro `$$` retorna o PID (Process ID) do script em execução.

```bash

echo $$

```

---

# Exemplo prático

```bash

#!/bin/bash

if [[ $# -lt 2 ]]; then

echo "Uso: ./script.sh <nome> <idade>"

exit 1

fi

echo "Nome: $1"

echo "Idade: $2"

```

### Execução

```bash

./script.sh Allan 22

```

### Saída

```text

Nome: Allan

Idade: 22

```

---

# Resumo

| Variável | Descrição |

|-----------|-----------|

| `$0` | Nome do script |

| `$1` | Primeiro parâmetro |

| `$2` | Segundo parâmetro |

| `$3` | Terceiro parâmetro |

| `$#` | Quantidade de parâmetros |

| `$@` | Todos os parâmetros |

| `$*` | Todos os parâmetros |

| `$$` | PID do script |

| `$?` | Código de saída do último comando |

---

# Observação

É uma boa prática validar a quantidade de parâmetros utilizando `$#` antes de utilizar `$1`, `$2`, etc.