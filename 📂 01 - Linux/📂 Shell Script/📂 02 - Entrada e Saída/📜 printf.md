
---

# Printf

> O comando `printf` exibe textos formatados na saída padrão (`stdout`), oferecendo maior controle sobre a formatação do que o comando `echo`.

---

# Sintaxe

```bash
printf "FORMATO" [ARGUMENTOS]
```

---

# `%s` - String

## O que é?

Exibe uma sequência de caracteres (string).

## Exemplo

```bash
printf "%s\n" "Olá Mundo"
```

### Saída

```text
Olá Mundo
```

---

# `%d` - Inteiro

## O que é?

Exibe um número inteiro em formato decimal.

## Exemplo

```bash
printf "%d\n" 100
```

### Saída

```text
100
```

---

# `%f` - Ponto flutuante

## O que é?

Exibe números com casas decimais.

## Exemplo

```bash
printf "%f\n" 3.14159
```

### Saída

```text
3.141590
```

---

# `%.2f` - Precisão decimal

## O que é?

Define quantas casas decimais serão exibidas.

## Exemplo

```bash
printf "%.2f\n" 3.14159
```

### Saída

```text
3.14
```

---

# `%x` - Hexadecimal

## O que é?

Exibe um número em hexadecimal.

## Exemplo

```bash
printf "%x\n" 255
```

### Saída

```text
ff
```

---

# `%o` - Octal

## O que é?

Exibe um número em octal.

## Exemplo

```bash
printf "%o\n" 64
```

### Saída

```text
100
```

---

# `%c` - Caractere

## O que é?

Exibe um único caractere.

## Exemplo

```bash
printf "%c\n" 65
```

### Saída

```text
A
```

---

# Largura de campo

## O que é?

Permite alinhar a saída utilizando uma largura fixa.

## Exemplo

```bash
printf "|%10s|\n" "Linux"
```

### Saída

```text
|     Linux|
```

---

## Alinhamento à esquerda

```bash
printf "|%-10s|\n" "Linux"
```

### Saída

```text
|Linux     |
```

---

# Preenchimento com zeros

```bash
printf "%05d\n" 42
```

### Saída

```text
00042
```

---

# Múltiplos valores

```bash
printf "%s tem %d anos.\n" "Allan" 25
```

### Saída

```text
Allan tem 25 anos.
```

---

# Variáveis

```bash
nome="Allan"
idade=25

printf "Nome: %s\nIdade: %d\n" "$nome" "$idade"
```

### Saída

```text
Nome: Allan
Idade: 25
```

---

# Sequências de escape

O `printf` interpreta sequências de escape sem precisar da opção `-e`.

| Sequência | Descrição |
|-----------|-----------|
| `\n` | Nova linha |
| `\t` | Tabulação |
| `\\` | Barra invertida (`\`) |
| `\"` | Aspas duplas |
| `\r` | Retorno de carro |
| `\b` | Backspace |

## Exemplo

```bash
printf "Linha 1\nLinha 2\n"
```

### Saída

```text
Linha 1
Linha 2
```

---

# Escrever em arquivo

```bash
printf "Backup iniciado\n" > log.txt
```

### Conteúdo do arquivo

```text
Backup iniciado
```

---

# Acrescentar ao arquivo

```bash
printf "Backup finalizado\n" >> log.txt
```

### Conteúdo do arquivo

```text
Backup iniciado
Backup finalizado
```

---

# Tabela de especificadores

| Especificador | Descrição |
|---------------|-----------|
| `%s` | String |
| `%d` | Inteiro decimal |
| `%i` | Inteiro |
| `%f` | Ponto flutuante |
| `%e` | Notação científica |
| `%g` | Formato compacto |
| `%c` | Caractere |
| `%o` | Octal |
| `%x` | Hexadecimal (minúsculo) |
| `%X` | Hexadecimal (maiúsculo) |
| `%%` | Exibir o caractere `%` |

---

# `printf` vs `echo`

| Recurso | `printf` | `echo` |
|---------|:--------:|:------:|
| Controle da formatação | ✅ | ❌ |
| Especificadores (`%d`, `%s`, `%f`) | ✅ | ❌ |
| Largura de campo | ✅ | ❌ |
| Precisão decimal | ✅ | ❌ |
| Portabilidade | ✅ | ⚠️ |
| Simplicidade | ⚠️ | ✅ |

---

# Observações

- `printf` é mais previsível e portável do que `echo`.
- Não adiciona uma quebra de linha automaticamente; utilize `\n` quando necessário.
- É a opção recomendada para scripts que exigem formatação consistente.