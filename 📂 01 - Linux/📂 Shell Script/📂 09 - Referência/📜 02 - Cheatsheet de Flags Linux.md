
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

