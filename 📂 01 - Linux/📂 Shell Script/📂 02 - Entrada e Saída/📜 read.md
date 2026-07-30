
---
# Read

> O comando `read` lê dados da entrada padrão (`stdin`) e armazena o valor informado em uma ou mais variáveis.

---

# Sintaxe

```bash
read [OPÇÕES] [VARIÁVEIS]
```

---

# `-p`

## O que é?

Exibe uma mensagem (prompt) antes de aguardar a entrada do usuário.

## Exemplo

```bash
read -p "Digite seu nome: " nome
```

### Saída

```text
Digite seu nome: Allan
```

A variável `nome` armazenará:

```text
Allan
```

---

# `-s`

## O que é?

Oculta os caracteres digitados pelo usuário.

Muito utilizado para leitura de senhas.

## Exemplo

```bash
read -s -p "Senha: " senha
```

### Saída

```text
Senha:
```

> Os caracteres digitados não aparecem na tela.

---

# `-r`

## O que é?

Impede que a barra invertida (`\`) seja interpretada como caractere de escape.

## Exemplo

```bash
read -r caminho
```

Entrada

```text
C:\Users\Allan
```

Valor armazenado

```text
C:\Users\Allan
```

Sem `-r`, algumas barras invertidas podem ser interpretadas como escape.

---

# `-a`

## O que é?

Armazena os valores digitados em um array.

## Exemplo

```bash
read -a nomes
```

Entrada

```text
João Maria Pedro
```

Acesso

```bash
echo "${nomes[0]}"
echo "${nomes[1]}"
echo "${nomes[2]}"
```

Saída

```text
João
Maria
Pedro
```

---

# `-t`

## O que é?

Define um tempo limite para o usuário informar uma entrada.

## Exemplo

```bash
read -t 5 -p "Digite algo: " texto
```

Se nada for digitado em 5 segundos, o comando termina automaticamente.

---

# `-n`

## O que é?

Lê apenas uma quantidade específica de caracteres.

## Exemplo

```bash
read -n 1 -p "Continuar? (s/n): " opcao
```

### Saída

```text
Continuar? (s/n): s
```

O usuário precisa digitar apenas um caractere.

---

# `-d`

## O que é?

Define um delimitador para encerrar a leitura.

O padrão é a tecla **Enter** (`\n`).

## Exemplo

```bash
read -d ":" texto
```

Entrada

```text
admin:1234
```

Valor armazenado

```text
admin
```

---

# Ler múltiplas variáveis

```bash
read nome idade cidade
```

Entrada

```text
Allan 25 Curitiba
```

Resultado

```bash
echo "$nome"
echo "$idade"
echo "$cidade"
```

Saída

```text
Allan
25
Curitiba
```

---

# Ler para a variável padrão

Se nenhuma variável for informada, o Bash utiliza a variável especial `REPLY`.

## Exemplo

```bash
read
```

Entrada

```text
Linux
```

Resultado

```bash
echo "$REPLY"
```

Saída

```text
Linux
```

---

# Mini Cheatsheet

| Opção | Função |
|--------|--------|
| `-p` | Exibir mensagem |
| `-s` | Ocultar entrada |
| `-r` | Não interpretar `\` |
| `-a` | Ler para um array |
| `-t` | Definir tempo limite |
| `-n` | Limitar quantidade de caracteres |
| `-d` | Alterar delimitador |

---

# Exemplos comuns

## Nome

```bash
read -p "Nome: " nome
```

---

## Senha

```bash
read -s -p "Senha: " senha
echo
```

---

## Confirmar ação

```bash
read -n 1 -p "Deseja continuar? (s/n): " resp
echo
```

---

## Array

```bash
read -a usuarios
```

---

## Timeout

```bash
read -t 10 resposta
```

---

# Observações

- `read` lê dados da **entrada padrão (`stdin`)**.
- A entrada normalmente é finalizada ao pressionar **Enter**.
- É um **builtin do Bash**, ou seja, não é um programa externo.
- Algumas opções podem variar entre diferentes shells, como `zsh`, `dash` e `BusyBox`.