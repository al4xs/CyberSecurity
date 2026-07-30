
---

# Cheatsheet de Flags Linux

> Consulta rápida das flags mais comuns utilizadas em comandos Linux e Unix.

---

# `-a`

| Item | Informação |
|------|------------|
| **Significado** | **All** (Todos) |
| **Uso comum** | Mostrar todos os arquivos, incluindo os ocultos |
| **Comandos comuns** | `ls`, `cp`, `grep` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -a
```

**Saída**

```text
.
..
.bashrc
.profile
Documentos
Downloads
```

---

# `-A`

| Item | Informação |
|------|------------|
| **Significado** | **Almost All** (Quase todos) |
| **Uso comum** | Mostrar arquivos ocultos, exceto `.` e `..` |
| **Comandos comuns** | `ls` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
ls -A
```

**Saída**

```text
.bashrc
.profile
Documentos
Downloads
```

---

# `-l`

| Item | Informação |
|------|------------|
| **Significado** | **Long** (Formato longo) |
| **Uso comum** | Exibir informações detalhadas sobre arquivos |
| **Comandos comuns** | `ls` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -l
```

**Saída**

```text
drwxr-xr-x 2 user user 4096 Jul 20 10:30 Documentos
-rw-r--r-- 1 user user  842 Jul 20 09:10 notas.txt
```

---

# `-h`

| Item | Informação |
|------|------------|
| **Significado** | **Human Readable** (Legível para humanos) |
| **Uso comum** | Exibir tamanhos em KB, MB, GB etc. |
| **Comandos comuns** | `ls`, `du`, `df` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
ls -lh
```

**Saída**

```text
-rw-r--r-- 1 user user 1.5K notas.txt
-rw-r--r-- 1 user user 2.3M backup.tar
```

---

# Convenções Mais Comuns

| Letra | Inglês | Português |
|-------|---------|------------|
| `a` | All | Todos |
| `c` | Create / Count | Criar / Contar |
| `d` | Directory | Diretório |
| `e` | Expression | Expressão |
| `f` | Force | Forçar |
| `h` | Help / Human Readable | Ajuda / Legível para humanos |
| `i` | Interactive | Interativo |
| `l` | Long | Formato longo |
| `n` | Number | Número |
| `o` | Output | Saída |
| `p` | Preserve / Parents | Preservar / Pais |
| `q` | Quiet | Silencioso |
| `r` | Recursive | Recursivo |
| `s` | Silent | Silencioso |
| `t` | Target / Time | Destino / Tempo |
| `u` | Update | Atualizar |
| `v` | Verbose | Modo detalhado |
| `w` | Word | Palavra |
| `x` | Execute | Executar |

---

# `-r`

| Item | Informação |
|------|------------|
| **Significado** | **Recursive / Reverse** (Recursivo / Inverter) |
| **Uso comum** | Processar diretórios recursivamente ou inverter a ordenação, dependendo do comando |
| **Comandos comuns** | `cp`, `rm`, `grep`, `sort` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
cp -r projetos/ backup/
```

**Saída**

```text
Diretório "projetos" copiado recursivamente para "backup/".
```

---

# `-R`

| Item | Informação |
|------|------------|
| **Significado** | **Recursive** (Recursivo) |
| **Uso comum** | Executar operações recursivamente em diretórios |
| **Comandos comuns** | `ls`, `chmod`, `chown` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
chmod -R 755 site/
```

**Saída**

```text
Permissões alteradas para todos os arquivos e diretórios de "site/".
```

---

# `-i`

| Item | Informação |
|------|------------|
| **Significado** | **Interactive** (Interativo) |
| **Uso comum** | Solicitar confirmação antes de executar ações potencialmente destrutivas |
| **Comandos comuns** | `rm`, `cp`, `mv` |
| **Importância** | ⭐⭐⭐⭐⭐ |

### Exemplo

```bash
rm -i arquivo.txt
```

**Saída**

```text
rm: remover arquivo regular 'arquivo.txt'?
```

---

# `-I`

| Item | Informação |
|------|------------|
| **Significado** | **Ignore / Interactive** (Ignorar / Interativo especial) |
| **Uso comum** | Ignorar arquivos ou solicitar uma única confirmação em operações grandes, dependendo do comando |
| **Comandos comuns** | `grep`, `rm` |
| **Importância** | ⭐⭐⭐☆☆ |

### Exemplo

```bash
grep -I "senha" backup.bin
```

**Saída**

```text
Arquivos binários são ignorados durante a busca.
```

---

# `-n`

| Item | Informação |
|------|------------|
| **Significado** | **Number / Numeric** (Número / Numérico) |
| **Uso comum** | Exibir numeração de linhas ou utilizar valores numéricos |
| **Comandos comuns** | `cat`, `grep`, `head`, `tail` |
| **Importância** | ⭐⭐⭐⭐☆ |

### Exemplo

```bash
grep -n "erro" sistema.log
```

**Saída**

```text
15: Erro ao iniciar o serviço.
42: Erro de autenticação.
```

---

# Combinações Mais Utilizadas

| Comando | Expansão |
|----------|----------|
| `cp -r` | `-r` Recursive (Recursivo) |
| `cp -ri` | `-r` Recursive (Recursivo) + `-i` Interactive (Interativo) |
| `rm -ri` | `-r` Recursive (Recursivo) + `-i` Interactive (Interativo) |
| `chmod -R` | `-R` Recursive (Recursivo) |
| `grep -rn` | `-r` Recursive (Recursivo) + `-n` Number (Número da linha) |
| `grep -rIn` | `-r` Recursive (Recursivo) + `-I` Ignore Binary Files (Ignorar arquivos binários) + `-n` Number (Número da linha) |

