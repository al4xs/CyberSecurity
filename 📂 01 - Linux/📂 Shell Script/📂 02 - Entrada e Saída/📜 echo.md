
---

# Echo

> O comando `echo` exibe textos, variáveis e caracteres especiais na saída padrão (`stdout`).

---

# Sintaxe

```bash
echo [OPÇÕES] [TEXTO]
```

---

# `-n`

## O que é?

O parâmetro `-n` impede que o `echo` adicione uma quebra de linha ao final da saída.

## Exemplo

```bash
echo -n "Olá"
echo " Mundo"
```

### Saída

```text
Olá Mundo
```

---

# `-e`

## O que é?

O parâmetro `-e` interpreta sequências de escape precedidas por `\`.

## Exemplo

```bash
echo -e "Olá\nMundo"
```

### Saída

```text
Olá
Mundo
```

---

# `-E`

## O que é?

O parâmetro `-E` desativa a interpretação de sequências de escape.

> Na maioria das distribuições Linux, este já é o comportamento padrão do `echo`.

## Exemplo

```bash
echo -E "Olá\nMundo"
```

### Saída

```text
Olá\nMundo
```

---

# Sequências de escape

| Sequência | Descrição |
|-----------|-----------|
| `\n` | Nova linha |
| `\t` | Tabulação horizontal |
| `\\` | Barra invertida (`\`) |
| `\"` | Aspas duplas |
| `\'` | Aspas simples |
| `\r` | Retorno de carro |
| `\b` | Backspace |
| `\a` | Alerta sonoro (beep) |

---

# Exemplos

## Exibir texto

```bash
echo "Olá Mundo"
```

### Saída

```text
Olá Mundo
```

---

## Exibir variável

```bash
nome="Allan"

echo "$nome"
```

### Saída

```text
Allan
```

---

## Exibir várias linhas

```bash
echo -e "Linux\nBash\nShell Script"
```

### Saída

```text
Linux
Bash
Shell Script
```

---

## Criar espaçamento

```bash
echo -e "Nome:\tAllan"
```

### Saída

```text
Nome:   Allan
```

---

## Escrever em arquivo

```bash
echo "Backup iniciado" > log.txt
```

### Conteúdo do arquivo

```text
Backup iniciado
```

---

## Acrescentar ao arquivo

```bash
echo "Backup finalizado" >> log.txt
```

### Conteúdo do arquivo

```text
Backup iniciado
Backup finalizado
```

---

# Observações

- `echo` envia sua saída para a **saída padrão (stdout)**.
- O comportamento pode variar entre implementações (`bash`, `dash`, `zsh`, `BusyBox`).
- Para formatação mais precisa e portável, prefira o comando `printf`.

---

# `echo` vs `printf`

| Recurso | `echo` | `printf` |
|---------|:------:|:--------:|
| Fácil de usar | ✅ | ⚠️ |
| Formatação avançada | ❌ | ✅ |
| Portabilidade | ⚠️ | ✅ |
| Controle da saída | ❌ | ✅ |
| Suporte a especificadores (`%s`, `%d`, `%f`) | ❌ | ✅ |
