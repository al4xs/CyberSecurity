
---
## `[ ]`

Os colchetes simples (`[ ]`) são o comando `test`.

É a forma tradicional e compatível com praticamente todos os shells POSIX.

### Exemplo

```bash
nome="Allan"

if [ "$nome" = "Allan" ]; then
    echo "Olá!"
fi
```

## `[[ ]]`

Os colchetes duplos (`[[ ]]`) são uma construção interna (builtin) do Bash e do Zsh.

Eles oferecem mais recursos, melhor legibilidade e evitam vários problemas comuns encontrados com `[ ]`.

### Exemplo

```bash
nome="Allan"

if [[ $nome == "Allan" ]]; then
    echo "Olá!"
fi
```

---

# Principais diferenças

## 1. Não precisa escapar variáveis vazias

Com `[ ]`:

```bash
if [ "$nome" = "Allan" ]; then
```

Sempre coloque aspas.

Sem aspas:

```bash
if [ $nome = "Allan" ]; then
```

Se `nome` estiver vazio, ocorrerá erro.

---

Com `[[ ]]`:

```bash
if [[ $nome == "Allan" ]]; then
```

Funciona normalmente, mesmo que a variável esteja vazia.

---

## 2. Operadores lógicos

Com `[ ]`:

```bash
if [ "$idade" -ge 18 ] && [ "$idade" -lt 60 ]; then
```

---

Com `[[ ]]`:

```bash
if [[ $idade -ge 18 && $idade -lt 60 ]]; then
```

Mais limpo.

---

## 3. Expressões regulares

Apenas `[[ ]]` suporta regex.

```bash
if [[ $email =~ @gmail\.com$ ]]; then
    echo "Gmail"
fi
```

---

## 4. Wildcards

```bash
arquivo="foto.png"

if [[ $arquivo == *.png ]]; then
    echo "Imagem"
fi
```

Com `[ ]`, isso não funciona da mesma forma.

---

# Qual usar?

## `[ ]`

- Compatibilidade com POSIX.
- Scripts que precisam rodar em qualquer shell.

---

## `[[ ]]`

- Scripts em Bash.
- Mais seguro.
- Mais moderno.
- Suporta regex.
- Suporta operadores lógicos diretamente.

---

# Minha recomendação

Se o script começar com:

```bash
#!/bin/bash
```

Prefira utilizar:

```bash
[[ ]]
```

Na maioria dos casos, ele é mais seguro e legível.

Use `[ ]` apenas quando precisar de compatibilidade com outros shells POSIX.