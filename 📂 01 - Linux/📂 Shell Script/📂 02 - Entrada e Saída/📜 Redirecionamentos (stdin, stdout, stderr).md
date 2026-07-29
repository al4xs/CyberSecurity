
---
# Redirecionamentos (stdin, stdout, stderr)

## O que são?

Todo programa executado no Linux possui três fluxos de comunicação padrão.

Esses fluxos são chamados de:

- Entrada padrão (stdin)
- Saída padrão (stdout)
- Saída de erro (stderr)

Esses fluxos podem ser redirecionados para arquivos, outros comandos ou descartados.

---

# Fluxos padrão

| Nome | Número | Descrição |
|------|--------|-----------|
| stdin | 0 | Entrada de dados |
| stdout | 1 | Saída normal |
| stderr | 2 | Saída de erro |

---

# stdin (0)

É a entrada padrão do programa.

Normalmente vem do teclado.

Exemplo:

```bash
read nome
```

O comando `read` recebe os dados digitados pelo usuário através do **stdin**.

---

# stdout (1)

É a saída normal do programa.

Por padrão, aparece na tela.

Exemplo

```bash
echo "Olá"
```

Saída

```text
Olá
```

Na verdade:

```text
echo
   ↓
stdout
   ↓
Terminal
```

---

# stderr (2)

É a saída destinada às mensagens de erro.

Exemplo

```bash
ls arquivo_inexistente
```

Saída

```text
ls: cannot access 'arquivo_inexistente'
```

Essa mensagem não é enviada para o stdout.

Ela utiliza o **stderr**.

---

# Redirecionando stdout

## Operador `>`

Redireciona a saída normal para um arquivo.

```bash
echo "Olá" > arquivo.txt
```

Conteúdo de `arquivo.txt`

```text
Olá
```

---

# Acrescentando conteúdo

## Operador `>>`

Acrescenta ao final do arquivo.

```bash
echo "Nova linha" >> arquivo.txt
```

---

# Redirecionando stderr

## Operador `2>`

Redireciona apenas mensagens de erro.

```bash
ls arquivo_inexistente 2> erro.txt
```

A mensagem de erro será salva em:

```text
erro.txt
```

---

# Redirecionando stdout e stderr separadamente

```bash
comando > saida.txt 2> erros.txt
```

Resultado

```text
stdout → saida.txt

stderr → erros.txt
```

---

# /dev/null

O arquivo especial:

```text
/dev/null
```

funciona como um "buraco negro".

Tudo enviado para ele é descartado.

Exemplo

```bash
echo "teste" > /dev/null
```

Nada será exibido.

---

# Ocultando a saída

```bash
ping google.com > /dev/null
```

O comando será executado normalmente.

A saída será descartada.

---

# 2>&1

Essa é uma das sintaxes mais utilizadas em Shell Script.

```bash
2>&1
```

Significa:

> Redirecione o **stderr (2)** para o mesmo destino do **stdout (1)**.

---

# Exemplo

```bash
ping google.com > /dev/null 2>&1
```

A execução ocorre nesta ordem.

Primeiro:

```bash
> /dev/null
```

Resultado

```text
stdout → /dev/null
```

Depois:

```bash
2>&1
```

Resultado

```text
stderr → stdout
```

Como o stdout já está apontando para:

```text
/ dev/null
```

O stderr também será descartado.

Fluxo final

```text
stdout → /dev/null

stderr → /dev/null
```

Nenhuma mensagem será exibida.

---

# Fluxo visual

```text
            comando
               │
      ┌────────┴────────┐
      │                 │
   stdout           stderr
      │                 │
      ▼                 │
 /dev/null              │
                        │
                        ▼
                   stdout
                        │
                        ▼
                   /dev/null
```

---

# Exemplo prático

```bash
ip="8.8.8.8"

if ping -c 1 -W 2 "$ip" > /dev/null 2>&1
then
    echo "Host online."
else
    echo "Host offline."
fi
```

O `ping` continua sendo executado normalmente.

A diferença é que toda a saída foi descartada.

O `if` utiliza apenas o código de retorno do comando.

---

# Resumo

| Operador | Função |
|----------|--------|
| `>` | Redireciona stdout |
| `>>` | Acrescenta stdout |
| `2>` | Redireciona stderr |
| `2>&1` | Envia stderr para stdout |
| `/dev/null` | Descarta qualquer saída |

---

# Exemplo mais comum

```bash
comando > /dev/null 2>&1
```

Significa:

- Execute o comando.
- Oculte a saída normal.
- Oculte as mensagens de erro.

O comando continua sendo executado normalmente.